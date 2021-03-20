---
description: CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)
title: CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 09/25/2019
ms.prod: sql
ms.prod_service: synapse-analytics, database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- DATABASE SCOPED CREDENTIAL
- DATABASE_SCOPED_CREDENTIAL_TSQL
- SCOPED_TSQL
- CREATE_DATABASE_SCOPED_CREDENTIAL
- CREATE_DATABASE_SCOPED_CREDENTIAL_TSQL
- SCOPED_CREDENTIAL_TSQL
- SCOPED_CREDENTIAL
helpviewer_keywords:
- DATABASE SCOPED CREDENTIAL statement
- credentials [SQL Server], DATABASE SCOPED CREDENTIAL statement
ms.assetid: fe830577-11ca-44e5-953b-2d589d54d045
author: VanMSFT
ms.author: vanto
monikerRange: =azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=aps-pdw-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 0f5f3bc676b2ba7c37df9ce463c99a1954a671a0
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104753352"
---
# <a name="create-database-scoped-credential-transact-sql"></a>CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)

[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

データベースの資格情報を作成します。 データベースの資格情報は、サーバー ログインまたはデータベース ユーザーにマップされません。 アクセスを必要とする操作がデータベースで実行されている場合はいつも、データベースでは資格情報を使用して外部の場所へのアクセスが行われます。

![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)

## <a name="syntax"></a>構文

```syntaxsql
CREATE DATABASE SCOPED CREDENTIAL credential_name
WITH IDENTITY = 'identity_name'
    [ , SECRET = 'secret' ]

```

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数

*credential_name* 作成するデータベース スコープの資格情報の名前を指定します。 *credential_name* はシャープ (#) 記号で始めることはできません。 システム資格情報は ## で始まります。

IDENTITY **='** _identity\_name_ **'** サーバーの外部に接続するときに使用するアカウントの名前を指定します。 共有キーを使用して Azure Blob Storage からファイルをインポートするには、ID 名が `SHARED ACCESS SIGNATURE` である必要があります。 データを Azure Synapse Analytics に読み込むには、任意の有効な値を ID に使用できます。 Shared Access Signature の詳細については、「[Shared Access Signatures (SAS) の使用](/azure/storage/storage-dotnet-shared-access-signature-part-1)」をご覧ください。 Kerberos (Windows Active Directory または MIT KDC) の使用時、IDENTITY 引数でドメイン名を使用しないでください。 アカウント名にしておけば問題ありません。

> [!IMPORTANT]
> Kerberos 認証をサポートする PolyBase 外部データ ソースは Hadoop だけです。 他のすべての外部データ ソース (SQL Server、Oracle、Teradata、MongoDB、汎用 ODBC) は、基本認証のみをサポートします。

> [!NOTE]
> Azure Blob Storage 内のコンテナーで匿名アクセスが有効になっている場合は、WITH IDENTITY を使用する必要はありません。 Azure Blob Storage に対するクエリの例については、「[Azure Blob Storage に格納されているファイルからテーブルへのインポート](../functions/openrowset-transact-sql.md#j-importing-into-a-table-from-a-file-stored-on-azure-blob-storage)」を参照してください。

SECRET **='** _secret_ **'** 送信の認証に必要なシークレットを指定します。 `SECRET` は、Azure Blob Storage からファイルをインポートするために必要です。 Azure Blob Storage から Azure Synapse Analytics または Parallel Data Warehouse に読み込むには、シークレットが Azure Storage キーである必要があります。
> [!WARNING]
> SAS キーの値は '?' (疑問符) で始まる可能性があります。 SAS キーを使用する場合は、先頭の '?' を削除する必要があります。 そうしないと、作業がブロックされる可能性があります。

## <a name="remarks"></a>解説

データベース スコープ資格情報は、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 外部のリソースへの接続に必要な認証情報を含むレコードです。 通常、資格情報には Windows ユーザーとパスワードが含まれます。

データベース スコープ資格情報を作成する前に、データベースに資格情報を保護するためのマスター キーが必要です。 詳細については、[CREATE MASTER KEY &#40;Transact-SQL&#41;](../../t-sql/statements/create-master-key-transact-sql.md)を参照してください。

IDENTITY が Windows ユーザーの場合、このシークレットはパスワードにすることができます。 シークレットはサービス マスター キーを使用して暗号化されます。 サービス マスター キーが再生成された場合、シークレットは新しいサービス マスター キーを使用して再度暗号化されます。

データベース スコープの資格情報に関する情報は、[sys.database_scoped_credentials](../../relational-databases/system-catalog-views/sys-database-scoped-credentials-transact-sql.md) カタログ ビューで確認できます。

ここでは、データベース スコープ資格情報のアプリケーションをいくつか示します。

- [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] は、PolyBase によるパブリック以外の Azure Blob Storage または Kerberos でセキュリティ保護された Hadoop クラスターへのアクセスにデータベース スコープ資格情報を使用します。 詳しくは、「[CREATE EXTERNAL DATA SOURCE (Transact-SQL)](../../t-sql/statements/create-external-data-source-transact-sql.md)」をご覧ください。

- [!INCLUDE[ssSDW_md](../../includes/sssdw-md.md)] は、PolyBase によるパブリック以外の Azure Blob Storage へのアクセスにデータベース スコープ資格情報を使用します。 詳しくは、「[CREATE EXTERNAL DATA SOURCE (Transact-SQL)](../../t-sql/statements/create-external-data-source-transact-sql.md)」をご覧ください。

- [!INCLUDE[ssSDS](../../includes/sssds-md.md)] は、グローバル クエリ機能にデータベース スコープ資格情報を使用します。 これは、複数のデータベース シャード間でクエリする機能です。

- [!INCLUDE[ssSDS](../../includes/sssds-md.md)] は、データベース スコープ資格情報を使用して、Azure Blob Storage に拡張イベント ファイルを書き込みます。

- [!INCLUDE[ssSDS](../../includes/sssds-md.md)] は、エラスティック プールにデータベース スコープ資格情報を使用します。 詳しくは、[エラスティック データベースでの急増の緩和](/azure/azure-sql/database/elastic-pool-overview)に関する記事をご覧ください

- [BULK INSERT](../../t-sql/statements/bulk-insert-transact-sql.md) と [OPENROWSET](../../t-sql/functions/openrowset-transact-sql.md) は、データベース スコープ資格情報を使用して Azure Blob Storage からデータにアクセスします。 詳しくは、「[Azure BLOB ストレージのデータに一括アクセスする例](../../relational-databases/import-export/examples-of-bulk-access-to-data-in-azure-blob-storage.md)」をご覧ください。 

## <a name="permissions"></a>アクセス許可

データベースに対する **CONTROL** 権限が必要です。

## <a name="examples"></a>例

### <a name="a-creating-a-database-scoped-credential-for-your-application"></a>A. アプリケーションのデータベース スコープ資格情報の作成

次の例では、`AppCred` というデータベース スコープ資格情報を作成します。 データベース スコープ資格情報には Windows ユーザー `Mary5` とパスワードが含まれます。

```sql
-- Create a db master key if one does not already exist, using your own password.
CREATE MASTER KEY ENCRYPTION BY PASSWORD='<EnterStrongPasswordHere>';

-- Create a database scoped credential.
CREATE DATABASE SCOPED CREDENTIAL AppCred WITH IDENTITY = 'Mary5',
    SECRET = '<EnterStrongPasswordHere>';
```

### <a name="b-creating-a-database-scoped-credential-for-a-shared-access-signature"></a>B. Shared Access Signature のデータベース スコープ資格情報の作成

次の例では、[BULK INSERT](../../t-sql/statements/bulk-insert-transact-sql.md) や [OPENROWSET](../../t-sql/functions/openrowset-transact-sql.md) など、一括操作できる[外部データ ソース](../../t-sql/statements/create-external-data-source-transact-sql.md)の作成に使用できるデータベース スコープ資格情報を作成します。 Shared Access Signatures は、SQL Server、APS、または Azure Synapse Analytics では PolyBase と共に使用できません。

```sql
-- Create a db master key if one does not already exist, using your own password.
CREATE MASTER KEY ENCRYPTION BY PASSWORD='<EnterStrongPasswordHere>';

-- Create a database scoped credential.
CREATE DATABASE SCOPED CREDENTIAL MyCredentials
WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
SECRET = 'QLYMgmSXMklt%2FI1U6DcVrQixnlU5Sgbtk1qDRakUBGs%3D';
```

### <a name="c-creating-a-database-scoped-credential-for-polybase-connectivity-to-azure-data-lake-store"></a>C. Azure Data Lake Store に PolyBase で接続するためのデータベース スコープ資格情報の作成

次の例では、[!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] で PolyBase によって使用できる[外部データ ソース](../../t-sql/statements/create-external-data-source-transact-sql.md)の作成に使用できるデータベース スコープ資格情報を作成します。

Azure Data Lake Store は、Azure Active Directory アプリケーションをサービス間認証に使用します。
データベース スコープ資格情報を作成する前に、[AAD アプリケーションを作成](/azure/data-lake-store/data-lake-store-authenticate-using-active-directory)し、client_id、OAuth_2.0_Token_EndPoint、キーを文書化してください。

```sql
-- Create a db master key if one does not already exist, using your own password.
CREATE MASTER KEY ENCRYPTION BY PASSWORD='<EnterStrongPasswordHere>';

-- Create a database scoped credential.
CREATE DATABASE SCOPED CREDENTIAL ADL_User
WITH
    IDENTITY = '<client_id>@\<OAuth_2.0_Token_EndPoint>',
    SECRET = '<key>'
;
```

## <a name="more-information"></a>詳細情報

- [資格情報 &#40;データベース エンジン&#41;](../../relational-databases/security/authentication-access/credentials-database-engine.md)
- [ALTER DATABASE SCOPED CREDENTIAL &#40;Transact-SQL&#41;](../../t-sql/statements/alter-database-scoped-credential-transact-sql.md)
- [DROP DATABASE SCOPED CREDENTIAL &#40;Transact-SQL&#41;](../../t-sql/statements/drop-database-scoped-credential-transact-sql.md)
- [sys.database_scoped_credentials](../../relational-databases/system-catalog-views/sys-database-scoped-credentials-transact-sql.md)
- [CREATE CREDENTIAL &#40;Transact-SQL&#41;](../../t-sql/statements/create-credential-transact-sql.md)
- [sys.credentials &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-credentials-transact-sql.md)
