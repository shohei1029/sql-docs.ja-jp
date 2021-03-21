---
description: CREATE CERTIFICATE (Transact-SQL)
title: CREATE CERTIFICATE (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 02/06/2020
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- CERTIFICATE
- CREATE_CERTIFICATE_TSQL
- sql13.swb.createcertificate.f1
- CERTIFICATE_TSQL
- CREATE CERTIFICATE
- sql13.swb.certificateproperties.f1
dev_langs:
- TSQL
helpviewer_keywords:
- encryption [SQL Server], certificates
- certificates [SQL Server], adding
- certificates [SQL Server]
- adding certificates
- cryptography [SQL Server], certificates
- CREATE CERTIFICATE statement
ms.assetid: a4274b2b-4cb0-446a-a956-1c8e6587515d
author: VanMSFT
ms.author: vanto
monikerRange: = azuresqldb-current || = azuresqldb-mi-current || >= sql-server-2016 || >= sql-server-linux-2017||= azure-sqldw-latest
ms.openlocfilehash: 02ec401b8167dbf6dcc145ab08a9604496b6f1a9
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104753372"
---
# <a name="create-certificate-transact-sql"></a>CREATE CERTIFICATE (Transact-SQL)
[!INCLUDE [sql-asdb-asa](../../includes/applies-to-version/sql-asdb-asa.md)]

  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のデータベースに証明書を追加します。  

 この機能は、データ層アプリケーション フレームワーク (DACFx) を使用してデータベースのエクスポートとの互換性はありません。 エクスポートする前に、すべての証明書を削除する必要があります。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)

[!INCLUDE[synapse-analytics-od-unsupported-syntax](../../includes/\synapse-analytics-od-unsupported-syntax.md)]  
  
## <a name="syntax"></a>構文  
  
```syntaxsql
-- Syntax for SQL Server and Azure SQL Database  
  
CREATE CERTIFICATE certificate_name [ AUTHORIZATION user_name ]   
    { FROM <existing_keys> | <generate_new_keys> }  
    [ ACTIVE FOR BEGIN_DIALOG = { ON | OFF } ]  
  
<existing_keys> ::=   
    ASSEMBLY assembly_name  
    | {   
        [ EXECUTABLE ] FILE = 'path_to_file'  
        [ WITH PRIVATE KEY ( <private_key_options> ) ]   
      }  
    | {   
        BINARY = asn_encoded_certificate  
        [ WITH PRIVATE KEY ( <private_key_options> ) ]  
      }  
<generate_new_keys> ::=   
    [ ENCRYPTION BY PASSWORD = 'password' ]   
    WITH SUBJECT = 'certificate_subject_name'   
    [ , <date_options> [ ,...n ] ]   
  
<private_key_options> ::=  
      {   
        FILE = 'path_to_private_key'  
         [ , DECRYPTION BY PASSWORD = 'password' ]  
         [ , ENCRYPTION BY PASSWORD = 'password' ]    
      }  
    |  
      {   
        BINARY = private_key_bits  
         [ , DECRYPTION BY PASSWORD = 'password' ]  
         [ , ENCRYPTION BY PASSWORD = 'password' ]    
      }  
  
<date_options> ::=  
    START_DATE = 'datetime' | EXPIRY_DATE = 'datetime'  
```  
  
   
```syntaxsql
-- Syntax for Parallel Data Warehouse  
  
CREATE CERTIFICATE certificate_name   
    { <generate_new_keys> | FROM <existing_keys> }  
    [ ; ]  
  
<generate_new_keys> ::=   
    WITH SUBJECT = 'certificate_subject_name'   
    [ , <date_options> [ ,...n ] ]   
  
<existing_keys> ::=   
    {   
      FILE ='path_to_file'  
      WITH PRIVATE KEY   
         (   
           FILE = 'path_to_private_key'  
           , DECRYPTION BY PASSWORD ='password'   
         )  
    }  
  
<date_options> ::=  
    START_DATE ='datetime' | EXPIRY_DATE ='datetime'  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
 *certificate_name*  
 データベースの証明書の名前を指定します。  
  
 AUTHORIZATION *user_name*  
 証明書の所有者となるユーザーの名前を指定します。  
  
 ASSEMBLY *assembly_name*  
 データベース内に既に読み込まれている署名付きアセンブリを指定します。  
  
 [ EXECUTABLE ] FILE = '*path_to_file*'  
 証明書が含まれる DER エンコード ファイルへの完全なパスを、ファイル名を含めて指定します。 EXECUTABLE オプションを使用する場合、ファイルはこの証明書によって署名された DLL になります。 *path_to_file* には、ローカル パスまたはネットワーク上の場所を示す UNC パスを指定できます。 ファイルには、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] サービス アカウントのセキュリティ コンテキストでアクセスします。 このアカウントは、ファイル システムで必要となる権限を保持している必要があります。  

> [!IMPORTANT]
> [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] では、ファイルからの証明書の作成や秘密キー ファイルの使用はサポートされません。
  
 BINARY = *asn_encoded_certificate*  
 バイナリ定数として指定された、ASN でエンコードされた証明書バイト。  
 **適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降。  
  
 WITH PRIVATE KEY  
 証明書の秘密キーを [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] に読み込むように指定します。 この句は、アセンブリから証明書を作成する場合には無効です。 アセンブリから作成された証明書の秘密キーを読み込むには、[ALTER CERTIFICATE](../../t-sql/statements/alter-certificate-transact-sql.md) を使用します。  
  
 FILE ='*path_to_private_key*'  
 秘密キーへの完全なパスを、ファイル名を含めて指定します。 *path_to_private_key* には、ローカル パスまたはネットワーク上の場所を示す UNC パスを指定できます。 ファイルには、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] サービス アカウントのセキュリティ コンテキストでアクセスします。 このアカウントは、ファイル システムで必要となる権限を保持している必要があります。  
  
> [!IMPORTANT]  
> このオプションは、包含データベースまたは [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] では使用できません。  
  
 BINARY = *private_key_bits*  
 **適用対象**:[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (開始値 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)]) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。  
  
 バイナリ定数として指定された秘密キーのビット。 これらのビットは暗号化された形式でもかまいません。 暗号化されている場合は、ユーザーは暗号化解除パスワードを指定する必要があります。 パスワード ポリシーのチェックは、このパスワードに対しては実行されません。 秘密キーのビットは PVK ファイル形式にする必要があります。  
  
 DECRYPTION BY PASSWORD = '*key_password*'  
 ファイルから取得する秘密キーの暗号化解除に必要なパスワードを指定します。 秘密キーが NULL パスワードで保護されている場合、この句は省略可能です。 パスワード保護なしで秘密キーをファイルに保存することは推奨されません。 パスワードが必要な場合にパスワードを指定しない場合、ステートメントは失敗します。  
  
 ENCRYPTION BY PASSWORD = '*password*'  
 秘密キーの暗号化に使用するパスワードを指定します。 このオプションは、証明書をパスワードで暗号化する場合にのみ使用します。 この句を省略した場合、秘密キーはデータベースのマスター キーで暗号化されます。 *password* は、Windows のパスワード ポリシーが [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスを実行するコンピューターに要求する条件を満足する必要があります。 詳細については、「 [Password Policy](../../relational-databases/security/password-policy.md)」をご参照ください。  
  
 SUBJECT = '*certificate_subject_name*'  
 "*サブジェクト*" という用語は、X.509 標準で定義されている、証明書のメタデータ内にあるフィールドを指します。 Linux 上の [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の場合、サブジェクトは半角 64 文字以下の長さでなければなりません。 Windows 上の [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の場合は、サブジェクトは半角 128 文字まで設定できます。 128 文字を超えた場合、サブジェクトはカタログに格納されるときには切り捨てられますが、証明書を含むバイナリ ラージ オブジェクト (BLOB) では、完全なサブジェクト名が保持されます。  
  
 START_DATE = '*datetime*'  
 証明書が有効となる日付を指定します。 指定しない場合、START_DATE は現在の日付に設定されます。 START_DATE は UTC 時刻で、日時に変換可能な任意の形式で指定できます。  
  
 EXPIRY_DATE = '*datetime*'  
 証明書が期限切れとなる日付を指定します。 指定しない場合、EXPIRY_DATE は START_DATE の 1 年後の日付に設定されます。 EXPIRY_DATE は UTC 時刻で、日時に変換可能な任意の形式で指定できます。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Service Broker は、有効期限の日付を確認します。 証明書を使用する暗号化されたバックアップでは、有効期限も確認され、期限切れの証明書を使用する新しいバックアップの作成は許可されません。ただし、期限切れの証明書を使用する復元は許可されます。 ただし、証明書がデータベースの暗号化または Always Encrypted のために使用される場合、有効期限は強制されません。  
  
 ACTIVE FOR BEGIN_DIALOG = { **ON** | OFF }  
 [!INCLUDE[ssSB](../../includes/sssb-md.md)] メッセージ交換の発信側で証明書を使用できるようにします。 既定値は ON です。  
  
## <a name="remarks"></a>注釈  
 証明書は、X.509 標準に準拠したデータベース レベルのセキュリティ保護可能なリソースであり、X.509 V1 フィールドをサポートします。 `CREATE CERTIFICATE` では、ファイル、バイナリ定数、またはアセンブリから証明書を読み込むことができます。 このステートメントでは、キー ペアを生成して自己署名証明書を作成することもできます。  
  
 秘密キーは、暗号化された形式で 2500 バイト以下でなければなりません。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] によって生成される秘密キーの長さは、[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] までは 1024 ビット、[!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 以降は 2048 ビットです。 外部ソースからインポートされる秘密キーの最小の長さは 384 ビットで、最大の長さは 4,096 ビットです。 インポートされる秘密キーの長さは、64 ビットの整数倍であることが必要です。 TDE に使用される証明書では、秘密キーのサイズが 3456 ビットに制限されています。  
  
 証明書の全体のシリアル番号が格納されているが、sys.certificates カタログ ビューで最初の 16 バイトのみが表示されます。  
  
 証明書の発行者フィールド全体が格納されているが、カタログ ビューの sys.certificates で最初の 884 バイトのみです。  
  
 秘密キーは、*certificate_name* で指定する公開キーに対応している必要があります。  
  
 証明書をコンテナーから作成する場合、秘密キーの読み込みは省略可能です。 しかし、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] で自己署名証明書が生成されるときには、常に秘密キーが作成されます。 既定では、秘密キーはデータベースのマスター キーを使用して暗号化されます。 データベースのマスター キーが存在せず、パスワードを指定しない場合、ステートメントは失敗します。  
  
 秘密キーがデータベースのマスター キーで暗号化されている場合、`ENCRYPTION BY PASSWORD` オプションは必要ありません。 このオプションは、秘密キーをパスワードで暗号化するときにのみ使用します。 パスワードを指定しない場合、証明書の秘密キーは、データベースのマスター キーを使用して暗号化されます。 データベースのマスター キーを開くことができない場合、この句を省略するとエラーが発生します。  
  
 秘密キーがデータベース マスター キーを使って暗号化されている場合は、暗号化解除のパスワードを指定する必要はありません。  
  
> [!NOTE]  
> 暗号化や署名用の組み込み関数では、証明書の有効期限はチェックされません。 これらの関数を使用する場合、ユーザーは、証明書の有効期限をいつチェックするかを自分で決定する必要があります。  
  
 証明書のバイナリ記述は、[CERTENCODED &#40;Transact-SQL&#41;](../../t-sql/functions/certencoded-transact-sql.md) および [CERTPRIVATEKEY &#40;Transact-SQL&#41;](../../t-sql/functions/certprivatekey-transact-sql.md) 関数を使って作成できます。 **CERTPRIVATEKEY** と **CERTENCODED** を使用して証明書を別のデータベースにコピーする例については、「[CERTENCODED &#40;Transact-SQL&#41;](../../t-sql/functions/certencoded-transact-sql.md)」の例 B を参照してください。  

[!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] では、MD2、MD4、MD5、SHA、SHA1 のアルゴリズムは非推奨です。 [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] までは、SHA1 を使用して自己署名証明書が作成されます。 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 以降では、SHA2_256 を使用して自己署名証明書が作成されます。

## <a name="permissions"></a>アクセス許可  
 データベースに対する `CREATE CERTIFICATE` 権限が必要です。 証明書を所有できるのは、Windows ログイン、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログイン、およびアプリケーション ロールだけです。 グループとロールは証明書を所有できません。  
  
## <a name="examples"></a>例  
  
### <a name="a-creating-a-self-signed-certificate"></a>A. 自己署名証明書を作成する  
 次の例では、`Shipping04` という証明書を作成します。 この証明書の秘密キーは、パスワードを使用して保護されます。  
  
```sql  
CREATE CERTIFICATE Shipping04   
   ENCRYPTION BY PASSWORD = 'pGFD4bb925DGvbd2439587y'  
   WITH SUBJECT = 'Sammamish Shipping Records',   
   EXPIRY_DATE = '20201031';  
GO  
```  
  
### <a name="b-creating-a-certificate-from-a-file"></a>B. ファイルから証明書を作成する  
 次の例では、データベースに証明書を作成し、ファイルからキー ペアを読み込みます。  
  
```sql  
CREATE CERTIFICATE Shipping11   
    FROM FILE = 'c:\Shipping\Certs\Shipping11.cer'   
    WITH PRIVATE KEY (FILE = 'c:\Shipping\Certs\Shipping11.pvk',   
    DECRYPTION BY PASSWORD = 'sldkflk34et6gs%53#v00');  
GO   
```  

> [!IMPORTANT]
> [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] では、ファイルからの証明書の作成はサポートされません。
   
### <a name="c-creating-a-certificate-from-a-signed-executable-file"></a>C. 署名付き実行可能ファイルから証明書を作成する  
  
```sql  
CREATE CERTIFICATE Shipping19   
    FROM EXECUTABLE FILE = 'c:\Shipping\Certs\Shipping19.dll';  
GO  
```  
  
 `dll` ファイルからアセンブリを作成し、次にそのアセンブリから証明書を作成することもできます。  
  
```sql  
CREATE ASSEMBLY Shipping19   
    FROM ' c:\Shipping\Certs\Shipping19.dll'   
    WITH PERMISSION_SET = SAFE;  
GO  
CREATE CERTIFICATE Shipping19 FROM ASSEMBLY Shipping19;  
GO  
```  

> [!IMPORTANT]
> [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] では、ファイルからの証明書の作成はサポートされません。

> [!IMPORTANT]
> [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 以降では、最初にセキュリティを設定しなくても、['CLR strict security'](../../database-engine/configure-windows/clr-strict-security.md) サーバー構成オプションによりアセンブリの読み込みが防止されます。 証明書を読み込み、そこからログインを作成し、`UNSAFE ASSEMBLY` にそのログインを付与してから、アセンブリを読み込みます。

### <a name="d-creating-a-self-signed-certificate"></a>D. 自己署名証明書を作成する  
 次の例では、暗号化パスワードを指定しないで、`Shipping04` という証明書を作成します。 この例は、[!INCLUDE[ssPDW](../../includes/sspdw-md.md)] で使用できます。
  
```sql  
CREATE CERTIFICATE Shipping04   
   WITH SUBJECT = 'Sammamish Shipping Records';  
GO  
```  
  
## <a name="see-also"></a>関連項目  
 [ALTER CERTIFICATE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-certificate-transact-sql.md)   
 [DROP CERTIFICATE &#40;Transact-SQL&#41;](../../t-sql/statements/drop-certificate-transact-sql.md)   
 [BACKUP CERTIFICATE &#40;Transact-SQL&#41;](../../t-sql/statements/backup-certificate-transact-sql.md)   
 [暗号化階層](../../relational-databases/security/encryption/encryption-hierarchy.md)   
 [EVENTDATA &#40;Transact-SQL&#41;](../../t-sql/functions/eventdata-transact-sql.md)   
 [CERTENCODED &#40;Transact-SQL&#41;](../../t-sql/functions/certencoded-transact-sql.md)   
 [CERTPRIVATEKEY &#40;Transact-SQL&#41;](../../t-sql/functions/certprivatekey-transact-sql.md)  
 [CERT_ID &#40;Transact-SQL&#41;](../../t-sql/functions/cert-id-transact-sql.md)  
 [CERTPROPERTY &#40;Transact-SQL&#41;](../../t-sql/functions/certproperty-transact-sql.md)  
  
  


