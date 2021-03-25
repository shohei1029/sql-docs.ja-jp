---
title: カスタム キーストア プロバイダー
description: ODBC Driver for SQL Server および Always Encrypted 機能と共に使用するためのカスタム キー ストア プロバイダーを実装する方法について説明します。
ms.custom: ''
ms.date: 07/12/2017
ms.prod: sql
ms.prod_service: connectivity
ms.reviewer: v-daenge
ms.technology: connectivity
ms.topic: conceptual
ms.assetid: a6166d7d-ef34-4f87-bd1b-838d3ca59ae7
ms.author: v-daenge
author: David-Engel
ms.openlocfilehash: 90eadc72e631ee59b0773dc47fe1199668484b65
ms.sourcegitcommit: 00af0b6448ba58e3685530f40bc622453d3545ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104673136"
---
# <a name="custom-keystore-providers"></a>カスタム キーストア プロバイダー

[!INCLUDE[Driver_ODBC_Download](../../includes/driver_odbc_download.md)]

## <a name="overview"></a>概要

SQL Server 2016 の列暗号化機能を使用するには、サーバーに格納されている暗号化された列暗号化キー (ECEK) をクライアントで取得してから、暗号化された列に格納されているデータにアクセスするために列暗号化キー (CEK) に暗号化解除する必要があります。 ECEK は列マスター キー (CMK) によって暗号化され、CMK のセキュリティは列暗号化のセキュリティにとって重要です。 そのため、CMK は安全な場所に格納する必要があります。列暗号化キーストア プロバイダーの目的は、安全に格納されている CMK に ODBC ドライバーからアクセスできるようにするためのインターフェイスを提供することです。 独自のセキュリティで保護されたストレージを持つユーザーの場合、カスタム キーストア プロバイダー インターフェイスでは、ODBC ドライバー用の CMK のセキュリティで保護されたストレージへのアクセスを実装するためのフレームワークが提供されます。これは、CEK の暗号化とその解除を行うために使用できます。

各キーストア プロバイダーでは、1 つまたは複数の CMK を格納し、管理します。これらは、キー パス (プロバイダーによって定義された形式の文字列) で識別されます。 この CMK は、(プロバイダーによって定義された文字列でもある) 暗号化アルゴリズムと共に、CEK の暗号化と ECEK の暗号化解除を行うために使用できます。 このアルゴリズムは、ECEK およびプロバイダーの名前と共に、データベースの暗号化メタデータに格納されます。 詳細については、[CREATE COLUMN MASTER KEY](../../t-sql/statements/create-column-master-key-transact-sql.md) と [CREATE COLUMN ENCRYPTION KEY](../../t-sql/statements/create-column-encryption-key-transact-sql.md) に関する記事をご覧ください。 このため、キー管理の 2 つの基本操作は次のようになります。

```cpp
CEK = DecryptViaCEKeystoreProvider(CEKeystoreProvider_name, Key_path, Key_algorithm, ECEK)

-and-

ECEK = EncryptViaCEKeystoreProvider(CEKeyStoreProvider_name, Key_path, Key_algorithm, CEK)
```

ここで、`CEKeystoreProvider_name` は特定の列暗号化キーストア プロバイダー (CEKeystoreProvider) を識別するために使用され、他の引数は CEKeystoreProvider によって、(E)CEK の暗号化とその解除を行うために使用されます。 名前とキー パスは CMK メタデータで指定されますが、アルゴリズムと ECEK 値は CEK メタデータで指定されます。 既定の組み込みプロバイダーと共に、複数のキーストア プロバイダーが存在する場合があります。 CEK を必要とする操作を実行するときに、ドライバーでは CMK メタデータを使用して、適切なキーストア プロバイダーを名前で検索し、その暗号化解除の操作を実行します。これは次のように表すことができます。

```cpp
CEK = CEKeyStoreProvider_specific_decrypt(Key_path, Key_algorithm, ECEK)
```

ドライバーで CEK を暗号化する必要はありませんが、キー管理ツールでは、CMK の作成や交換などの操作を実装するために必要になる場合があります。 これらのアクションでは、逆の操作を実行する必要があります。

```cpp
ECEK = CEKeyStoreProvider_specific_encrypt(Key_path, Key_algorithm, CEK)
```

### <a name="cekeystoreprovider-interface"></a>CEKeyStoreProvider インターフェイス

このドキュメントでは、CEKeyStoreProvider インターフェイスについて詳しく説明します。 このインターフェイスを実装するキーストア プロバイダーは、Microsoft ODBC Driver for SQL Server で使用できます。 CEKeyStoreProvider の実装者は、このガイドを使用して、ドライバーで使用できるカスタム キーストア プロバイダーを開発できます。

キーストア プロバイダー ライブラリ ("プロバイダー ライブラリ") はダイナミックリンク ライブラリであり、ODBC ドライバーで読み込むことができ、1 つまたは複数のキーストア プロバイダーが含まれています。 シンボルの `CEKeystoreProvider` は、プロバイダー ライブラリによってエクスポートされる必要があります。また、ライブラリ内のキーストア プロバイダーごとに 1 つある、`CEKeystoreProvider` 構造体へのポインターの null 値で終わる配列のアドレスである必要があります。

`CEKeystoreProvider` 構造体では、1 つのキーストア プロバイダーのエントリ ポイントを定義します。

```cpp
typedef struct CEKeystoreProvider {
    wchar_t *Name;
    int (*Init)(CEKEYSTORECONTEXT *ctx, errFunc *onError);
    int (*Read)(CEKEYSTORECONTEXT *ctx, errFunc *onError, void *data, unsigned int *len);
    int (*Write)(CEKEYSTORECONTEXT *ctx, errFunc *onError, void *data, unsigned int len);
    int (*DecryptCEK)(  CEKEYSTORECONTEXT *ctx,
                        errFunc *onError,
                        const wchar_t *keyPath,
                        const wchar_t *alg,
                        unsigned char *ecek,
                        unsigned short ecekLen,
                        unsigned char **cekOut,
                        unsigned short *cekLen);
    int (*EncryptCEK)(  CEKEYSTORECONTEXT *ctx,
                        errFunc *onError,
                        const wchar_t *keyPath,
                        const wchar_t *alg,
                        unsigned char *cek,
                        unsigned short cekLen,
                        unsigned char **ecekOut,
                        unsigned short *ecekLen);
    void (*Free)();
} CEKEYSTOREPROVIDER;
```

|フィールド名|説明|
|:--|:--|
|`Name`|キーストア プロバイダーの名前。 これは、ドライバーによって以前に読み込まれたか、このライブラリにある他のキーストア プロバイダーと同じにすることはできません。 null 値で終わる、ワイド文字列 (*) です。|
|`Init`|初期化関数。 初期化関数が必要でない場合は、このフィールドを null にすることができます。|
|`Read`|プロバイダーの読み取り関数。 必要でない場合は、null にすることができます。|
|`Write`|プロバイダーの書き込み関数。 Read が null でない場合は必須です。 必要でない場合は、null にすることができます。|
|`DecryptCEK`|ECEK の暗号化解除関数。 キーストア プロバイダーが存在するのは、この関数のためです。null にすることはできません。|
|`EncryptCEK`|CEK の暗号化関数。 ドライバーではこの関数を呼び出しませんが、これは、キー管理ツールによる ECEK の作成に対するプログラムでのアクセスを許可するために提供されます。 必要でない場合は、null にすることができます。|
|`Free`|終了関数。 必要でない場合は、null にすることができます。|

Free を除き、このインターフェイス内の関数にはすべて、パラメーター (**ctx** と **onError**) のペアがあります。 前者では関数が呼び出されるコンテキストを識別しますが、後者はエラーの報告に使用されます。 詳細については、以下の[コンテキスト](#context-association)に関する記述と、「[エラー処理](#error-handling)」を参照してください。

```cpp
int Init(CEKEYSTORECONTEXT *ctx, errFunc onError);
```

プロバイダー定義の初期化関数のプレースホルダー名。 ドライバーでは、プロバイダーが読み込まれた後 (ただし、最初に ECEK の暗号化解除または Read() と Write() 要求を実行するために必要になる前) に、この関数を 1 回呼び出します。 この関数は、必要な初期化を実行するために使用します。

|引数|説明|
|:--|:--|
|`ctx`|[入力] 操作コンテキスト。|
|`onError`|[入力] エラー レポート関数。|
|`Return Value`|成功を示す場合は 0 以外、失敗を示す場合は 0 を返します。|

```cpp
int Read(CEKEYSTORECONTEXT *ctx, errFunc onError, void *data, unsigned int *len);
```

プロバイダー定義の通信関数のプレースホルダー名。 ドライバーでは、アプリケーションによって SQL_COPT_SS_CEKEYSTOREDATA 接続属性を使用して (以前に書き込まれた) プロバイダーからデータを読み取るように要求されたときに、この関数を呼び出します。これにより、アプリケーションではプロバイダーから任意のデータを読み取ることができます。 詳細については、「[キーストア プロバイダーとの通信](using-always-encrypted-with-the-odbc-driver.md#communicating-with-keystore-providers)」をご覧ください。

|引数|説明|
|:--|:--|
|`ctx`|[入力] 操作コンテキスト。|
|`onError`|[入力] エラー レポート関数。|
|`data`|[出力] アプリケーションによって読み取られるデータが、プロバイダーによって書き込まれるバッファーへのポインター。 このバッファーは、CEKEYSTOREDATA 構造体のデータ フィールドに対応します。|
|`len`|[入出力] 長さの値へのポインター。入力時では、この値はデータ バッファーの最大長であり、プロバイダーでは `*len` を超えるバイトは書き込まれません。 戻り時に、プロバイダーでは、書き込まれたバイト数で `*len` を更新する必要があります。|
|`Return Value`|成功を示す場合は 0 以外、失敗を示す場合は 0 を返します。|

```cpp
int Write(CEKEYSTORECONTEXT *ctx, errFunc onError, void *data, unsigned int len);
```

プロバイダー定義の通信関数のプレースホルダー名。 ドライバーでは、アプリケーションによって、SQL_COPT_SS_CEKEYSTOREDATA 接続属性を使用してプロバイダーにデータを書き込むように要求されたときに、この関数を呼び出します。これにより、アプリケーションではプロバイダーに任意のデータを書き込むことができます。 詳細については、「[キーストア プロバイダーとの通信](using-always-encrypted-with-the-odbc-driver.md#communicating-with-keystore-providers)」をご覧ください。

|引数|説明|
|:--|:--|
|`ctx`|[入力] 操作コンテキスト。|
|`onError`|[入力] エラー レポート関数。|
|`data`|[入力] プロバイダーで読み取るデータが含まれているバッファーへのポインター。 このバッファーは、CEKEYSTOREDATA 構造体のデータ フィールドに対応します。 プロバイダーでは、このバッファーから len を超えるバイトを読み取ることはできません。|
|`len`|[入力] データで使用可能なバイト数。 この値は、CEKEYSTOREDATA 構造体の dataSize フィールドに対応します。|
|`Return Value`|成功を示す場合は 0 以外、失敗を示す場合は 0 を返します。|

```cpp
int (*DecryptCEK)( CEKEYSTORECONTEXT *ctx, errFunc *onError, const wchar_t *keyPath, const wchar_t *alg, unsigned char *ecek, unsigned short ecekLen, unsigned char **cekOut, unsigned short *cekLen);
```

プロバイダー定義の ECEK 暗号化解除関数のプレースホルダー名。 ドライバーでは、このプロバイダーに関連付けられている CMK によって暗号化された ECEK を CEK に暗号化解除するために、この関数を呼び出します。

|引数|説明|
|:--|:--|
|`ctx`|[入力] 操作コンテキスト。|
|`onError`|[入力] エラー レポート関数。|
|`keyPath`|[入力] 特定の ECEK によって参照される、CMK の[KEY_PATH](../../t-sql/statements/create-column-master-key-transact-sql.md) メタデータ属性の値。 null 値で終わるワイド文字列 (*) です。 この値は、このプロバイダーによって処理される CMK を識別するためのものです。|
|`alg`|[入力] 特定の ECEK の [ALGORITHM](../../t-sql/statements/create-column-encryption-key-transact-sql.md) メタデータ属性の値。 null 値で終わるワイド文字列 (*) です。 この値は、指定された ECEK の暗号化に使用される暗号化アルゴリズムを識別するためのものです。|
|`ecek`|[入力] 暗号化解除される ECEK へのポインター。|
|`ecekLen`|[入力] ECEK の長さ。|
|`cekOut`|[出力] プロバイダーでは、暗号化解除された ECEK にメモリを割り当て、cekOut が指すポインターにそのアドレスを書き込みます。 [LocalFree](/windows/desktop/api/winbase/nf-winbase-localfree) (Windows) または free (Linux/macOS) 関数を使用して、このメモリ ブロックを解放できる必要があります。 エラーまたはその他の原因でメモリが割り当てられなかった場合、プロバイダーでは *cekOut を null ポインターに設定します。|
|`cekLen`|[出力] プロバイダーでは、cekLen が指すアドレスに、**cekOut に書き込まれて暗号化解除された ECEK の長さを書き込みます。|
|`Return Value`|成功を示す場合は 0 以外、失敗を示す場合は 0 を返します。|

```cpp
int (*EncryptCEK)( CEKEYSTORECONTEXT *ctx, errFunc *onError, const wchar_t *keyPath, const wchar_t *alg, unsigned char *cek,unsigned short cekLen, unsigned char **ecekOut, unsigned short *ecekLen);
```

プロバイダー定義の CEK 暗号化関数のプレースホルダー名。 ドライバーでは、この関数を呼び出したり、ODBC インターフェイスを介してその機能を公開したりすることはありませんが、これは、キー管理ツールによる ECEK の作成に対するプログラムでのアクセスを許可するために提供されます。

|引数|説明|
|:--|:--|
|`ctx`|[入力] 操作コンテキスト。|
|`onError`|[入力] エラー レポート関数。|
|`keyPath`|[入力] 特定の ECEK によって参照される、CMK の[KEY_PATH](../../t-sql/statements/create-column-master-key-transact-sql.md) メタデータ属性の値。 null 値で終わるワイド文字列 (*) です。 この値は、このプロバイダーによって処理される CMK を識別するためのものです。|
|`alg`|[入力] 特定の ECEK の [ALGORITHM](../../t-sql/statements/create-column-encryption-key-transact-sql.md) メタデータ属性の値。 null 値で終わるワイド文字列 (*) です。 この値は、指定された ECEK の暗号化に使用される暗号化アルゴリズムを識別するためのものです。|
|`cek`|[入力] 暗号化される CEK へのポインター。|
|`cekLen`|[入力] CEK の長さ。|
|`ecekOut`|[出力] プロバイダーで、暗号化された CEK にメモリを割り当て、ecekOut が指すポインターにそのアドレスを書き込みます。 [LocalFree](/windows/desktop/api/winbase/nf-winbase-localfree) (Windows) または free (Linux/macOS) 関数を使用して、このメモリ ブロックを解放できる必要があります。 エラーまたはその他の原因でメモリが割り当てられなかった場合、プロバイダーでは *ecekOut を null ポインターに設定します。|
|`ecekLen`|[出力] プロバイダーでは、ecekLen が指すアドレスに、**ecekOut に書き込まれて暗号化された CEK の長さを書き込みます。|
|`Return Value`|成功を示す場合は 0 以外、失敗を示す場合は 0 を返します。|

```cpp
void (*Free)();
```

プロバイダー定義の終了関数のプレースホルダー名。 ドライバーでは、プロセスの通常の終了時にこの関数を呼び出すことができます。

> [!NOTE]
> *ワイド文字列は、SQL Server での格納方法により、2 バイト文字 (UTF-16) になります。*

### <a name="error-handling"></a>エラー処理

プロバイダーの処理中にエラーが発生する可能性があるため、ブール値の成功や失敗よりも具体的に詳しくドライバーにエラーを報告できるようにするためのメカニズムが提供されます。 関数の多くには、パラメーター (**ctx** と **onError**) のペアがあります。これらは、成功や失敗の戻り値に加え、この目的のために一緒に使用されます。

**ctx** パラメーターでは、プロバイダー操作が発生するコンテキストを識別します。

**onError** パラメーターでは、次のプロトタイプを使用して、エラーレポート関数を指します。

`typedef void errFunc(CEKEYSTORECONTEXT *ctx, const wchar_t *msg, ...);`

|引数|説明|
|:--|:--|
|`ctx`|[入力] エラーを報告するコンテキスト。|
|`msg`|[入力] 報告するエラー メッセージ。 null 値で終わるワイド文字列です。 情報をパラメーター化するために、この文字列には、[FormatMessage](/windows/desktop/api/winbase/nf-winbase-formatmessage) 関数によって受け入れられるフォームの挿入書式設定シーケンスを含めることができます。 拡張機能は、以下に示すように、このパラメーターで指定できます。|
|...|[入力] 必要に応じて、msg の書式指定子に合わせるための追加の可変個引数パラメーター。|

エラーが発生したときに報告するために、プロバイダーでは onError を呼び出します。これにより、ドライバーによってプロバイダー関数に渡されたコンテキスト パラメーターと、書式設定する省略可能な追加パラメーターを含むエラー メッセージが提供されます。 プロバイダーでは、この関数を複数回呼び出して、1 つのプロバイダー関数呼び出し内で複数のエラー メッセージを連続してポストする場合があります。 次に例を示します。

```cpp
    if (!doSomething(...))
    {
        onError(ctx, L"An error occurred in doSomething.");
        onError(ctx, L"Additional error message with more details.");
        return 0;
    }
```

`msg` パラメーターは通常、ワイド文字列ですが、追加の拡張機能を使用できます。

IDS_MSG マクロで特別な定義済みの値の 1 つを使用することによって、既に存在しており、ドライバーのローカライズされたフォームの一般的なエラー メッセージを利用できます。 たとえば、プロバイダーでメモリの割り当てに失敗した場合は、`IDS_S1_001` "メモリの割り当てに失敗しました" メッセージを使用できます。

`onError(ctx, IDS_MSG(IDS_S1_001));`

ドライバーによってエラーが認識されるようにするには、プロバイダー関数で失敗を返す必要があります。 ODBC 操作のコンテキストでエラーが発生すると、標準的な ODBC 診断メカニズム (`SQLError`、`SQLGetDiagRec`、および `SQLGetDiagField`) を使用して、接続またはステートメント ハンドルでポストされたエラーにアクセスできるようになります。

### <a name="context-association"></a>コンテキストの関連付け

`CEKEYSTORECONTEXT` 構造体は、エラー コールバックのコンテキストを提供するだけでなく、プロバイダー操作が実行される ODBC コンテキストを特定するためにも使用できます。 このコンテキストにより、プロバイダーでは、これらの各コンテキストにデータを関連付け、接続ごとの構成の実装などを行うことができます。 このため、構造体には、環境、接続、およびステートメントのコンテキストに対応する、3 つの非透過ポインターが含まれています。

```cpp
typedef struct CEKeystoreContext
{
void *envCtx;
void *dbcCtx;
void *stmtCtx;
} CEKEYSTORECONTEXT;
```

|フィールド|説明|
|:--|:--|
|`envCtx`|環境コンテキスト。|
|`dbcCtx`|接続コンテキスト。|
|`stmtCtx`|ステートメント コンテキスト。|

これらの各コンテキストは非透過値ですが、対応する ODBC ハンドルと同じではなく、ハンドルの一意識別子として使用できます。ハンドル *X* がコンテキスト値 *Y* に関連付けられている場合、*X* と同時に存在する他の環境、接続、ステートメント ハンドルには、*Y* のコンテキスト値がなく、他のコンテキスト値はハンドル *X* に関連付けられません。実行されているプロバイダーの操作に特定のハンドル コンテキストがない (たとえば、SQLSetConnectAttr を呼び出してプロバイダーを読み込んで構成する場合に、ステートメント ハンドルがない) 場合は、構造体の対応するコンテキスト値が null になります。

## <a name="example"></a>例

### <a name="keystore-provider"></a>キーストア プロバイダー

次のコードは、最小のキーストア プロバイダー実装の例です。

```cpp
/* Custom Keystore Provider Example

Windows:   compile with cl MyKSP.c /LD /MD /link /out:MyKSP.dll
Linux/macOS: compile with gcc -fshort-wchar -fPIC -o MyKSP.so -shared MyKSP.c

 */

#ifdef _WIN32
#include <windows.h>
#else
#define __stdcall
#endif

#include <stdlib.h>
#include <sqltypes.h>
#include "msodbcsql.h"
#include <sql.h>
#include <sqlext.h>

int __stdcall KeystoreInit(CEKEYSTORECONTEXT *ctx, errFunc *onError) {
    printf("KSP Init() function called\n");
    return 1;
}

static unsigned char *g_encryptKey;
static unsigned int g_encryptKeyLen;

int __stdcall KeystoreWrite(CEKEYSTORECONTEXT *ctx, errFunc *onError, void *data, unsigned int len) {
    printf("KSP Write() function called (%d bytes)\n", len);
    if (len) {
        if (g_encryptKey)
            free(g_encryptKey);
        g_encryptKey = malloc(len);
        if (!g_encryptKey) {
            onError(ctx, L"Memory Allocation Error");
            return 0;
        }
        memcpy(g_encryptKey, data, len);
        g_encryptKeyLen = len;
    }
    return 1;
}

// Very simple "encryption" scheme - rotating XOR with the key
int __stdcall KeystoreDecrypt(CEKEYSTORECONTEXT *ctx, errFunc *onError, const wchar_t *keyPath, const wchar_t *alg,
    unsigned char *ecek, unsigned short ecekLen, unsigned char **cekOut, unsigned short *cekLen) {
    unsigned int i;
    printf("KSP Decrypt() function called (keypath=%S alg=%S ecekLen=%u)\n", keyPath, alg, ecekLen);
    if (wcscmp(keyPath, L"TheOneAndOnlyKey")) {
        onError(ctx, L"Invalid key path");
        return 0;
    }
    if (wcscmp(alg, L"none")) {
        onError(ctx, L"Invalid algorithm");
        return 0;
    }
    if (!g_encryptKey) {
        onError(ctx, L"Keystore provider not initialized with key");
        return 0;
    }
#ifndef _WIN32
    *cekOut = malloc(ecekLen);
#else
    *cekOut = LocalAlloc(LMEM_FIXED, ecekLen);
#endif
    if (!*cekOut) {
        onError(ctx, L"Memory Allocation Error");
        return 0;
    }
    *cekLen = ecekLen;
    for (i = 0; i < ecekLen; i++)
        (*cekOut)[i] = ecek[i] ^ g_encryptKey[i % g_encryptKeyLen];
    return 1;
}

// Note that in the provider interface, this function would be referenced via the CEKEYSTOREPROVIDER
// structure. However, that does not preclude keystore providers from exporting their own functions,
// as illustrated by this example where the encryption is performed via a separate function (with a
// different prototype than the one in the KSP interface.)
#ifdef _WIN32
__declspec(dllexport)
#endif
int KeystoreEncrypt(CEKEYSTORECONTEXT *ctx, errFunc *onError,
    unsigned char *cek, unsigned short cekLen,
    unsigned char **ecekOut, unsigned short *ecekLen) {
    unsigned int i;
    printf("KSP Encrypt() function called (cekLen=%u)\n", cekLen);
    if (!g_encryptKey) {
        onError(ctx, L"Keystore provider not initialized with key");
        return 0;
    }
    *ecekOut = malloc(cekLen);
    if (!*ecekOut) {
        onError(ctx, L"Memory Allocation Error");
        return 0;
    }
    *ecekLen = cekLen;
    for (i = 0; i < cekLen; i++)
        (*ecekOut)[i] = cek[i] ^ g_encryptKey[i % g_encryptKeyLen];
    return 1;
}

CEKEYSTOREPROVIDER MyCustomKSPName_desc = {
    L"MyCustomKSPName",
    KeystoreInit,
    0,
    KeystoreWrite,
    KeystoreDecrypt,
    0
};

#ifdef _WIN32
__declspec(dllexport)
#endif
CEKEYSTOREPROVIDER *CEKeystoreProvider[] = {
    &MyCustomKSPName_desc,
    0
};
```

### <a name="odbc-application"></a>ODBC アプリケーション

次のコードは、上記のキーストア プロバイダーを使用する、デモ アプリケーションです。 これを実行するときは、プロバイダー ライブラリがアプリケーション バイナリと同じディレクトリにあり、接続文字列によって `ColumnEncryption=Enabled` 設定が指定されている (または、それを含む DSN が指定されている) ことを確認します。

```cpp
/*
 Example application for demonstration of custom keystore provider usage

Windows:   compile with cl /MD kspapp.c /link odbc32.lib
Linux/macOS: compile with gcc -o kspapp -fshort-wchar kspapp.c -lodbc -ldl
 
 usage: kspapp connstr

 */

#define KSPNAME L"MyCustomKSPName"
#define PROV_ENCRYPT_KEY "JHKCWYT06N3RG98J0MBLG4E3"

#include <stdio.h>
#include <stdlib.h>
#ifdef _WIN32
#include <windows.h>
#else
#define __stdcall
#include <dlfcn.h>
#endif
#include <sql.h>
#include <sqlext.h>
#include "msodbcsql.h"

/* Convenience functions */

int checkRC(SQLRETURN rc, char *msg, int ret, SQLHANDLE h, SQLSMALLINT ht) {
    if (rc == SQL_ERROR) {
        fprintf(stderr, "Error occurred upon %s\n", msg);
        if (h) {
            SQLSMALLINT i = 0;
            SQLSMALLINT outlen = 0;
            char errmsg[1024];
            while ((rc = SQLGetDiagField(
                ht, h, ++i, SQL_DIAG_MESSAGE_TEXT, errmsg, sizeof(errmsg), &outlen)) == SQL_SUCCESS
                || rc == SQL_SUCCESS_WITH_INFO) {
                fprintf(stderr, "Err#%d: %s\n", i, errmsg);
            }
        }
        if (ret)
            exit(ret);
        return 0;
    }
    else if (rc == SQL_SUCCESS_WITH_INFO && h) {
        SQLSMALLINT i = 0;
        SQLSMALLINT outlen = 0;
        char errmsg[1024];
        printf("Success with info for %s:\n", msg);
        while ((rc = SQLGetDiagField(
            ht, h, ++i, SQL_DIAG_MESSAGE_TEXT, errmsg, sizeof(errmsg), &outlen)) == SQL_SUCCESS
            || rc == SQL_SUCCESS_WITH_INFO) {
            fprintf(stderr, "Msg#%d: %s\n", i, errmsg);
        }
    }
    return 1;
}

void postKspError(CEKEYSTORECONTEXT *ctx, const wchar_t *msg, ...) {
    if (msg > (wchar_t*)65535)
        wprintf(L"Provider emitted message: %s\n", msg);
    else
        wprintf(L"Provider emitted message ID %d\n", msg);
}

int main(int argc, char **argv) {
    char sqlbuf[1024];
    SQLHENV env;
    SQLHDBC dbc;
    SQLHSTMT stmt;
    SQLRETURN rc;
    unsigned char CEK[32];
    unsigned char *ECEK;
    unsigned short ECEKlen;
#ifdef _WIN32
    HMODULE hProvLib;
#else
    void *hProvLib;
#endif
    CEKEYSTORECONTEXT ctx = {0};
    CEKEYSTOREPROVIDER **ppKsp, *pKsp;
    int(__stdcall *pEncryptCEK)(CEKEYSTORECONTEXT *, errFunc *, unsigned char *, unsigned short, unsigned char **, unsigned short *);
    int i;
    if (argc < 2) {
        fprintf(stderr, "usage: kspapp connstr\n");
        return 1;
    }

    /* Load the provider library */
#ifdef _WIN32
    if (!(hProvLib = LoadLibrary("MyKSP.dll"))) {
#else
    if (!(hProvLib = dlopen("./MyKSP.so", RTLD_NOW))) {
#endif
        fprintf(stderr, "Error loading KSP library\n");
        return 2;
    }
#ifdef _WIN32
    if (!(ppKsp = (CEKEYSTOREPROVIDER**)GetProcAddress(hProvLib, "CEKeystoreProvider"))) {
#else
    if (!(ppKsp = (CEKEYSTOREPROVIDER**)dlsym(hProvLib, "CEKeystoreProvider"))) {
#endif
        fprintf(stderr, "The export CEKeystoreProvider was not found in the KSP library\n");
        return 3;
    }
    while (pKsp = *ppKsp++) {
        if (!memcmp(KSPNAME, pKsp->Name, sizeof(KSPNAME)))
            goto FoundProv;
    }
    fprintf(stderr, "Could not find provider in the library\n");
    return 4;
FoundProv:
    if (pKsp->Init && !pKsp->Init(&ctx, postKspError)) {
        fprintf(stderr, "Could not initialize provider\n");
        return 5;
    }
#ifdef _WIN32
    if (!(pEncryptCEK = (LPVOID)GetProcAddress(hProvLib, "KeystoreEncrypt"))) {
#else
    if (!(pEncryptCEK = dlsym(hProvLib, "KeystoreEncrypt"))) {
#endif
        fprintf(stderr, "The export KeystoreEncrypt was not found in the KSP library\n");
        return 6;
    }
    if (!pKsp->Write) {
        fprintf(stderr, "Provider does not support configuration\n");
        return 7;
    }

    /* Configure the provider with the key */
    if (!pKsp->Write(&ctx, postKspError, PROV_ENCRYPT_KEY, strlen(PROV_ENCRYPT_KEY))) {
        fprintf(stderr, "Error writing to KSP\n");
        return 8;
    }

    /* Generate a CEK and encrypt it with the provider */
    srand(time(0) ^ getpid());
    for (i = 0; i < sizeof(CEK); i++)
        CEK[i] = rand();

    if (!pEncryptCEK(&ctx, postKspError, CEK, sizeof(CEK), &ECEK, &ECEKlen)) {
        fprintf(stderr, "Error encrypting CEK\n");
        return 9;
    }

    /* Connect to Server */
    rc = SQLAllocHandle(SQL_HANDLE_ENV, NULL, &env);
    checkRC(rc, "allocating environment handle", 2, 0, 0);
    rc = SQLSetEnvAttr(env, SQL_ATTR_ODBC_VERSION, (SQLPOINTER)SQL_OV_ODBC3, 0);
    checkRC(rc, "setting ODBC version to 3.0", 3, env, SQL_HANDLE_ENV);
    rc = SQLAllocHandle(SQL_HANDLE_DBC, env, &dbc);
    checkRC(rc, "allocating connection handle", 4, env, SQL_HANDLE_ENV);
    rc = SQLDriverConnect(dbc, 0, argv[1], strlen(argv[1]), NULL, 0, NULL, SQL_DRIVER_NOPROMPT);
    checkRC(rc, "connecting to data source", 5, dbc, SQL_HANDLE_DBC);
    rc = SQLAllocHandle(SQL_HANDLE_STMT, dbc, &stmt);
    checkRC(rc, "allocating statement handle", 6, dbc, SQL_HANDLE_DBC);

    /* Create a CMK definition on the server */
    {
        static char cmkSql[] = "CREATE COLUMN MASTER KEY CustomCMK WITH ("
            "KEY_STORE_PROVIDER_NAME = 'MyCustomKSPName',"
            "KEY_PATH = 'TheOneAndOnlyKey')";
        printf("Create CMK: %s\n", cmkSql);
        SQLExecDirect(stmt, cmkSql, SQL_NTS);
    }

    /* Create a CEK definition on the server */
    {
        const char cekSqlBefore[] = "CREATE COLUMN ENCRYPTION KEY CustomCEK WITH VALUES ("
            "COLUMN_MASTER_KEY = CustomCMK,"
            "ALGORITHM = 'none',"
            "ENCRYPTED_VALUE = 0x";
        char *cekSql = malloc(sizeof(cekSqlBefore) + 2 * ECEKlen + 2); /* 1 for ')', 1 for null terminator */
        strcpy(cekSql, cekSqlBefore);
        for (i = 0; i < ECEKlen; i++)
            sprintf(cekSql + sizeof(cekSqlBefore) - 1 + 2 * i, "%02x", ECEK[i]);
        strcat(cekSql, ")");
        printf("Create CEK: %s\n", cekSql);
        SQLExecDirect(stmt, cekSql, SQL_NTS);
        free(cekSql);
#ifdef _WIN32
        LocalFree(ECEK);
#else
        free(ECEK);
#endif
    }

#ifdef _WIN32
    FreeLibrary(hProvLib);
#else
    dlclose(hProvLib);
#endif

    /* Create a table with encrypted columns */
    {
        static char *tableSql = "CREATE TABLE CustomKSPTestTable ("
            "c1 int,"
            "c2 varchar(255) COLLATE Latin1_General_BIN2 ENCRYPTED WITH (COLUMN_ENCRYPTION_KEY = CustomCEK, ENCRYPTION_TYPE = DETERMINISTIC, ALGORITHM = 'AEAD_AES_256_CBC_HMAC_SHA_256'))";
        printf("Create table: %s\n", tableSql);
        SQLExecDirect(stmt, tableSql, SQL_NTS);
    }

    /* Load provider into the ODBC Driver and configure it */
    {
        unsigned char ksd[sizeof(CEKEYSTOREDATA) + sizeof(PROV_ENCRYPT_KEY) - 1];
        CEKEYSTOREDATA *pKsd = (CEKEYSTOREDATA*)ksd;
        pKsd->name = L"MyCustomKSPName";
        pKsd->dataSize = sizeof(PROV_ENCRYPT_KEY) - 1;
        memcpy(pKsd->data, PROV_ENCRYPT_KEY, sizeof(PROV_ENCRYPT_KEY) - 1);
#ifdef _WIN32
        rc = SQLSetConnectAttr(dbc, SQL_COPT_SS_CEKEYSTOREPROVIDER, "MyKSP.dll", SQL_NTS);
#else
        rc = SQLSetConnectAttr(dbc, SQL_COPT_SS_CEKEYSTOREPROVIDER, "./MyKSP.so", SQL_NTS);
#endif
        checkRC(rc, "Loading KSP into ODBC Driver", 7, dbc, SQL_HANDLE_DBC);
        rc = SQLSetConnectAttr(dbc, SQL_COPT_SS_CEKEYSTOREDATA, (SQLPOINTER)pKsd, SQL_IS_POINTER);
        checkRC(rc, "Configuring the KSP", 7, dbc, SQL_HANDLE_DBC);
    }

    /* Insert some data */
    {
        int c1;
        char c2[256];
        rc = SQLBindParameter(stmt, 1, SQL_PARAM_INPUT, SQL_C_LONG, SQL_INTEGER, 0, 0, &c1, 0, 0);
        checkRC(rc, "Binding parameters for insert", 9, stmt, SQL_HANDLE_STMT);
        rc = SQLBindParameter(stmt, 2, SQL_PARAM_INPUT, SQL_C_CHAR, SQL_VARCHAR, 255, 0, c2, 255, 0);
        checkRC(rc, "Binding parameters for insert", 9, stmt, SQL_HANDLE_STMT);
        for (i = 0; i < 10; i++) {
            c1 = i * 10 + i + 1;
            sprintf(c2, "Sample data %d for column 2", i);
            rc = SQLExecDirect(stmt, "INSERT INTO CustomKSPTestTable (c1, c2) values (?, ?)", SQL_NTS);
            checkRC(rc, "Inserting rows query", 10, stmt, SQL_HANDLE_STMT);
        }
        printf("(Encrypted) data has been inserted into the [CustomKSPTestTable]. You may inspect the data now.\n"
            "Press Enter to continue...\n");
        getchar();
    }

    /* Retrieve the data */
    {
        int c1;
        char c2[256];
        rc = SQLBindCol(stmt, 1, SQL_C_LONG, &c1, 0, 0);
        checkRC(rc, "Binding columns for select", 11, stmt, SQL_HANDLE_STMT);
        rc = SQLBindCol(stmt, 2, SQL_C_CHAR, c2, sizeof(c2), 0);
        checkRC(rc, "Binding columns for select", 11, stmt, SQL_HANDLE_STMT);
        rc = SQLExecDirect(stmt, "SELECT c1, c2 FROM CustomKSPTestTable", SQL_NTS);
        checkRC(rc, "Retrieving rows query", 12, stmt, SQL_HANDLE_STMT);
        while (SQL_SUCCESS == (rc = SQLFetch(stmt)))
            printf("Retrieved data: c1=%d c2=%s\n", c1, c2);
        SQLFreeStmt(stmt, SQL_CLOSE);
        printf("Press Enter to clean up and exit...\n");
        getchar();
    }

    /* Clean up */
    {
        SQLExecDirect(stmt, "DROP TABLE CustomKSPTestTable", SQL_NTS);
        SQLExecDirect(stmt, "DROP COLUMN ENCRYPTION KEY CustomCEK", SQL_NTS);
        SQLExecDirect(stmt, "DROP COLUMN MASTER KEY CustomCMK", SQL_NTS);
        printf("Removed table, CEK, and CMK\n");
    }
    SQLDisconnect(dbc);
    SQLFreeHandle(SQL_HANDLE_DBC, dbc);
    SQLFreeHandle(SQL_HANDLE_ENV, env);
    return 0;
}

```

## <a name="see-also"></a>参照

[ODBC ドライバーで Always Encrypted を使用する](using-always-encrypted-with-the-odbc-driver.md)
