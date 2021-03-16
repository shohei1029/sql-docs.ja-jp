---
description: ALTER EXTERNAL LANGUAGE (Transact-SQL) - SQL Server
title: ALTER EXTERNAL LANGUAGE (Transact-SQL) - SQL Server | Microsoft Docs
ms.custom: ''
ms.date: 03/15/2021
ms.prod: sql
ms.technology: language-extensions
ms.topic: language-reference
author: nelgson
ms.author: negust
ms.reviewer: dphansen
manager: cgronlun
monikerRange: '>=sql-server-ver15||>=sql-server-linux-ver15'
ms.openlocfilehash: a86741824e47c51c6a24c39e6dfc5437d12bca45
ms.sourcegitcommit: e2d25f265556af92afcc0acde662929e654bf841
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103489535"
---
# <a name="alter-external-language-transact-sql"></a>ALTER EXTERNAL LANGUAGE (Transact-SQL)
[!INCLUDE [SQL Server 2019 and later](../../includes/applies-to-version/sqlserver2019.md)]

データベース内の既存の外部言語拡張機能の内容を変更します。

## <a name="syntax"></a>構文

```syntaxsql
ALTER EXTERNAL LANGUAGE language_name  
[ AUTHORIZATION owner_name ]
{
    SET <file_spec>
    | ADD <file_spec>
    | REMOVE PLATFORM <platform> 
}
[ ; ]  

<file_spec> ::=  
{
    ( CONTENT = {<external_lang_specifier> | <content_bits>,
    FILE_NAME = <external_lang_file_name>
    [, PLATFORM = <platform> ]
    [, PARAMETERS = <external_lang_parameters> ]
    [, ENVIRONMENT_VARIABLES = <external_lang_env_variables> ] )
}

<external_lang_specifier> :: =  
{
    '[file_path\]os_file_name'  
}

<content_bits> :: =  
{
    varbinary_literal
   | varbinary_expression
}

<external_lang_file_name> :: =  
'extension_file_name'

<platform> :: =
{
   WINDOWS
  | LINUX
}

< external_lang_parameters > :: =  
'extension_specific_parameters'
```

### <a name="arguments"></a>引数

**language_name**

言語は、データベース スコープのオブジェクトです。 言語名は、データベース内で一意であることが必要です。

**owner_name**

外部言語を所有しているユーザーまたはロールの名前を指定します。 このオプションを指定しない場合は、所有権は現在のユーザーに与えられます。 アクセス許可によっては、特定の言語を使用するスクリプトを実行するために、他のユーザーに明示的なアクセス許可を付与することが必要な場合があります。

**file_spec**

言語拡張機能の内容を指定します。プラットフォームごとに特定の言語に対しては 1 つの filespec だけが許可されます。 

**external_lang_specifier**

拡張機能のコードが含まれる .zip または tar.gz ファイルの完全なファイル パスです。 この内容では、.zip ファイル (Windows の場合) または tar.gz (Linux の場合) へのパスを指定できます。

**content_bits**

アセンブリと同様に、言語の内容を 16 進数のリテラルとして指定します。
このオプションは、言語を作成するか、既存の言語を変更する必要があり (そして、それを行うために必要なアクセス許可を持っていて)、しかしサーバー上のファイル システムが制限されていて、サーバーがアクセスできる場所にライブラリ ファイルをコピーできない場合に、役に立ちます。

**external_lang_file_name**

拡張機能の .dll または .so ファイルの名前です。 これは、<external_lang_specifier> の .zip または tar.gz に複数の .dll ファイルまたは .so ファイルが含まれる場合に、正しいファイルを示すために必要です。

**external_lang_parameters**

外部言語ランタイムにパラメーターのセットを提供できます。 パラメーターの値は、外部プロセスが開始した後で、外部のランタイムに提供されます。 ただし、外部プロセスの開始前でも、言語拡張機能は環境変数にアクセスできます。

**external_lang_env_variables**

これにより、外部プロセスの開始前に、外部言語ランタイムに環境変数のセットを提供できます。 環境変数の例は、たとえば、ランタイム自体のホーム ディレクトリです。 次に例を示します。JRE_HOME。

**platform**

このパラメーターは、ハイブリッド OS のシナリオに必要です。 ハイブリッド アーキテクチャでは、プラットフォームごとに 1 回、言語を登録する必要があります。 プラットフォームと言語の名前は、外部言語ごとの一意のキーになります。 プラットフォームを指定しないと、現在の OS が想定されます。

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="remarks"></a>解説

現在、**PARAMETERS** と **ENVIRONMENT_VARIABLES** はサポートされていません。

## <a name="permissions"></a>アクセス許可

`ALTER ANY EXTERNAL LANGUAGE` アクセス許可が必要です。 既定では、**db_owner** ロールのメンバーである **dbo** を持つすべてのユーザーに、外部言語を変更するためのアクセス許可があります。 他のすべてのユーザーについては、[GRANT](./grant-database-permissions-transact-sql.md) ステートメントを使用し、特権として ALTER ANY EXTERNAL LANGUAGE を指定して、アクセス許可を明示的に付与する必要があります。

## <a name="examples"></a>例

### <a name="alter-an-external-language-in-a-database"></a>データベース内の外部言語を変更する  

次の例では、Windows 上の SQL Server のデータベースに、Java という名前の外部言語を追加します。

```sql
ALTER EXTERNAL LANGUAGE Java 
SET (CONTENT = N'<path-to-zip>', FILE_NAME = 'javaextension.dll');
GO
```

## <a name="see-also"></a>関連項目

[CREATE EXTERNAL LANGUAGE (Transact-SQL)](create-external-language-transact-sql.md)  
[DROP EXTERNAL LANGUAGE (Transact-SQL)](drop-external-language-transact-sql.md)  
[sys.external_languages](../../relational-databases/system-catalog-views/sys-external-languages-transact-sql.md)  
[sys.external_language_files](../../relational-databases/system-catalog-views/sys-external-language-files-transact-sql.md)
