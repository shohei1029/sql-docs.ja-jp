---
description: GRANT (データベースの権限の許可) (Transact-SQL)
title: GRANT (データベースの権限の許可) (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 05/22/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
dev_langs:
- TSQL
helpviewer_keywords:
- granting permissions [SQL Server]
- database permissions [SQL Server]
- granting permissions [SQL Server], databases
- permissions [SQL Server], databases
- database permissions [SQL Server], granting
- GRANT statement, databases
ms.assetid: 499e5ed6-945c-4791-ab45-68dec0b9c289
author: VanMSFT
ms.author: vanto
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: a5873dd851bbb0b56dd1cab27794ea7052f45f44
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104750662"
---
# <a name="grant-database-permissions-transact-sql"></a>GRANT (データベースの権限の許可) (Transact-SQL)

[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のデータベースに対する権限を許可します。

![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)

## <a name="syntax"></a>構文

```syntaxsql

GRANT <permission> [ ,...n ]
    TO <database_principal> [ ,...n ] [ WITH GRANT OPTION ]
    [ AS <database_principal> ]

<permission>::=
permission | ALL [ PRIVILEGES ]

<database_principal> ::=
    Database_user
  | Database_role
  | Application_role
  | Database_user_mapped_to_Windows_User
  | Database_user_mapped_to_Windows_Group
  | Database_user_mapped_to_certificate
  | Database_user_mapped_to_asymmetric_key
  | Database_user_with_no_login
```

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数

*permission*: データベースで許可できる権限を指定します。 権限の一覧については、後の「解説」を参照してください。

ALL: このオプションに設定しても、可能な権限がすべて許可されるわけではありません。 ALL を指定すると、次のアクセス許可が許可されます。BACKUP DATABASE、BACKUP LOG、CREATE DEFAULT、CREATE FUNCTION、CREATE PROCEDURE、CREATE RULE、CREATE TABLE、CREATE VIEW。

PRIVILEGES: ANSI-92 準拠のために用意されています。 ALL の動作は変更されません。

WITH GRANT OPTION: 権限が許可されたプリンシパルが、この権限を他のプリンシパルにも許可できることを示します。

AS \<database_principal> このクエリを実行するプリンシパルが権限を許可する権利を取得した、元のプリンシパルを指定します。

*Database_user*: データベース ユーザーを指定します。

*Database_role*: データベース ロールを指定します。

*Application_role*
**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降、[!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)]

アプリケーション ロールを指定します。

*Database_user_mapped_to_Windows_User*
**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降

Windows ユーザーにマップされているデータベース ユーザーを指定します。

*Database_user_mapped_to_Windows_Group*
**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降

Windows グループにマップされているデータベース ユーザーを指定します。

*Database_user_mapped_to_certificate*
**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降

証明書にマップされているデータベース ユーザーを指定します。

*Database_user_mapped_to_asymmetric_key*
**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降

非対称キーにマップされているデータベース ユーザーを指定します。

*Database_user_with_no_login*: 対応するサーバー レベルのプリンシパルがないデータベース ユーザーを指定します。

## <a name="remarks"></a>解説

> [!IMPORTANT]
> 権限許可対象ユーザーは、ALTER 権限と REFERENCE 権限を組み合わせて使用することで、データを表示したり、許可されていない関数を実行できる場合があります。 次に例を示します。テーブルの ALTER 権限と関数の REFERENCE 権限を持つユーザーは、関数を介した計算列を作成して実行できます。 この場合、ユーザーには計算列の SELECT 権限も必要です。

データベースは、セキュリティ保護可能なリソースで、権限の階層で親となっているサーバーに含まれています。 次の表に、データベースで許可できる権限のうち最も限定的なものを、それらを暗黙的に含む一般的な権限と共に示します。

|データベース権限|権限が含まれるデータベース権限|権限が含まれるサーバー権限|
|-------------------------|------------------------------------|----------------------------------|
|ADMINISTER DATABASE BULK OPERATIONS<br/>**適用対象**: [!INCLUDE[ssSDS_md](../../includes/sssds-md.md)]。|CONTROL|CONTROL SERVER|
|ALTER|CONTROL|ALTER ANY DATABASE|
|ALTER ANY APPLICATION ROLE|ALTER|CONTROL SERVER|
|ALTER ANY ASSEMBLY|ALTER|CONTROL SERVER|
|ALTER ANY ASYMMETRIC KEY|ALTER|CONTROL SERVER|
|ALTER ANY CERTIFICATE|ALTER|CONTROL SERVER|
|任意の列の暗号化キーを変更します。|ALTER|CONTROL SERVER|
|ALTER ANY COLUMN MASTER KEY DEFINITION|ALTER|CONTROL SERVER|
|ALTER ANY CONTRACT|ALTER|CONTROL SERVER|
|ALTER ANY DATABASE AUDIT|ALTER|ALTER ANY SERVER AUDIT|
|ALTER ANY DATABASE DDL TRIGGER|ALTER|CONTROL SERVER|
|ALTER ANY DATABASE EVENT NOTIFICATION|ALTER|ALTER ANY EVENT NOTIFICATION|
|ALTER ANY DATABASE EVENT SESSION<br />**適用対象**: [!INCLUDE[ssSDS_md](../../includes/sssds-md.md)]|ALTER|ALTER ANY EVENT SESSION|
|ALTER ANY DATABASE SCOPED CONFIGURATION<br /> **適用対象**: [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 以降、[!INCLUDE[ssSDS](../../includes/sssds-md.md)]。|CONTROL|CONTROL SERVER|
|ALTER ANY DATASPACE|ALTER|CONTROL SERVER|
|すべての外部データ ソースを変更します。|ALTER|CONTROL SERVER|
|任意の外部のファイル形式を変更します。|ALTER|CONTROL SERVER|
|ALTER ANY EXTERNAL LIBRARY <br /> **適用対象**: [!INCLUDE [sssql17-md](../../includes/sssql17-md.md)]|CONTROL|CONTROL SERVER |
|ALTER ANY FULLTEXT CATALOG|ALTER|CONTROL SERVER|
|任意のマスクを変更します。|CONTROL|CONTROL SERVER|
|ALTER ANY MESSAGE TYPE|ALTER|CONTROL SERVER|
|ALTER ANY REMOTE SERVICE BINDING|ALTER|CONTROL SERVER|
|ALTER ANY ROLE|ALTER|CONTROL SERVER|
|ALTER ANY ROUTE|ALTER|CONTROL SERVER|
|ALTER ANY SCHEMA|ALTER|CONTROL SERVER|
|すべてのセキュリティ ポリシーを変更します。<br /> **適用対象**: [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]|CONTROL|CONTROL SERVER|
|ALTER ANY SERVICE|ALTER|CONTROL SERVER|
|ALTER ANY SYMMETRIC KEY|ALTER|CONTROL SERVER|
|ALTER ANY USER|ALTER|CONTROL SERVER|
|AUTHENTICATE|CONTROL|AUTHENTICATE SERVER|
|BACKUP DATABASE|CONTROL|CONTROL SERVER|
|BACKUP LOG|CONTROL|CONTROL SERVER|
|CHECKPOINT|CONTROL|CONTROL SERVER|
|CONNECT|CONNECT REPLICATION|CONTROL SERVER|
|CONNECT REPLICATION|CONTROL|CONTROL SERVER|
|CONTROL|CONTROL|CONTROL SERVER|
|CREATE AGGREGATE|ALTER|CONTROL SERVER|
|CREATE ANY EXTERNAL LIBRARY <br /> **適用対象**: [!INCLUDE [sssql17-md](../../includes/sssql17-md.md)]|CONTROL|CONTROL SERVER |
|CREATE ASSEMBLY|ALTER ANY ASSEMBLY|CONTROL SERVER|
|CREATE ASYMMETRIC KEY|ALTER ANY ASYMMETRIC KEY|CONTROL SERVER|
|CREATE CERTIFICATE|ALTER ANY CERTIFICATE|CONTROL SERVER|
|CREATE CONTRACT|ALTER ANY CONTRACT|CONTROL SERVER|
|CREATE DATABASE|CONTROL|CREATE ANY DATABASE|
|CREATE DATABASE DDL EVENT NOTIFICATION|ALTER ANY DATABASE EVENT NOTIFICATION|CREATE DDL EVENT NOTIFICATION|
|CREATE DEFAULT|ALTER|CONTROL SERVER|
|CREATE FULLTEXT CATALOG|ALTER ANY FULLTEXT CATALOG|CONTROL SERVER|
|CREATE FUNCTION|ALTER|CONTROL SERVER|
|CREATE MESSAGE TYPE|ALTER ANY MESSAGE TYPE|CONTROL SERVER|
|CREATE PROCEDURE|ALTER|CONTROL SERVER|
|CREATE QUEUE|ALTER|CONTROL SERVER|
|CREATE REMOTE SERVICE BINDING|ALTER ANY REMOTE SERVICE BINDING|CONTROL SERVER|
|CREATE ROLE|ALTER ANY ROLE|CONTROL SERVER|
|CREATE ROUTE|ALTER ANY ROUTE|CONTROL SERVER|
|CREATE RULE|ALTER|CONTROL SERVER|
|CREATE SCHEMA|ALTER ANY SCHEMA|CONTROL SERVER|
|CREATE SERVICE|ALTER ANY SERVICE|CONTROL SERVER|
|CREATE SYMMETRIC KEY|ALTER ANY SYMMETRIC KEY|CONTROL SERVER|
|CREATE SYNONYM|ALTER|CONTROL SERVER|
|CREATE TABLE|ALTER|CONTROL SERVER|
|CREATE TYPE|ALTER|CONTROL SERVER|
|CREATE VIEW|ALTER|CONTROL SERVER|
|CREATE XML SCHEMA COLLECTION|ALTER|CONTROL SERVER|
|DELETE|CONTROL|CONTROL SERVER|
|EXECUTE|CONTROL|CONTROL SERVER|
|EXECUTE ANY EXTERNAL SCRIPT <br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)]|CONTROL|CONTROL SERVER|
|EXECUTE EXTERNAL SCRIPT <br /> **適用対象**: [!INCLUDE[sssql19-md](../../includes/sssql19-md.md)]|EXECUTE ANY EXTERNAL SCRIPT|CONTROL SERVER|
|INSERT|CONTROL|CONTROL SERVER|
|KILL DATABASE CONNECTION<br />**適用対象**: [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]|CONTROL|ALTER ANY CONNECTION|
|REFERENCES|CONTROL|CONTROL SERVER|
|SELECT|CONTROL|CONTROL SERVER|
|SHOWPLAN|CONTROL|ALTER TRACE|
|SUBSCRIBE QUERY NOTIFICATIONS|CONTROL|CONTROL SERVER|
|TAKE OWNERSHIP|CONTROL|CONTROL SERVER|
|マスク解除します。|CONTROL|CONTROL SERVER|
|UPDATE|CONTROL|CONTROL SERVER|
|列の暗号化キーの定義を表示します。|CONTROL|VIEW ANY DEFINITION|
|任意の列のマスター_キーの定義の表示|CONTROL|VIEW ANY DEFINITION|
|VIEW DATABASE STATE|CONTROL|VIEW SERVER STATE|
|VIEW DEFINITION|CONTROL|VIEW ANY DEFINITION|

## <a name="permissions"></a>アクセス許可

権限の許可者 (または AS オプションで指定されたプリンシパル) は、GRANT OPTION によって与えられた権限を保持しているか、権限が暗黙的に与えられる上位の権限を保持している必要があります。

AS オプションを使用している場合は、次の追加要件があります。

|AS *granting_principal*|必要な追加権限|
|------------------------------|------------------------------------|
|データベース ユーザー|ユーザーに対する IMPERSONATE 権限、db_securityadmin 固定データベース ロールのメンバーシップ、db_owner 固定データベース ロールのメンバーシップ、または sysadmin 固定サーバー ロールのメンバーシップ。|
|Windows ログインにマップされているデータベース ユーザー|ユーザーに対する IMPERSONATE 権限、db_securityadmin 固定データベース ロールのメンバーシップ、db_owner 固定データベース ロールのメンバーシップ、または sysadmin 固定サーバー ロールのメンバーシップ。|
|Windows グループにマップされているデータベース ユーザー|Windows グループのメンバーシップ、db_securityadmin 固定データベース ロールのメンバーシップ、db_owner 固定データベース ロールのメンバーシップ、または sysadmin 固定サーバー ロールのメンバーシップ。|
|証明書にマップされているデータベース ユーザー|db_securityadmin 固定データベース ロールのメンバーシップ、db_owner 固定データベース ロールのメンバーシップ、または sysadmin 固定サーバー ロールのメンバーシップ。|
|非対称キーにマップされているデータベース ユーザー|db_securityadmin 固定データベース ロールのメンバーシップ、db_owner 固定データベース ロールのメンバーシップ、または sysadmin 固定サーバー ロールのメンバーシップ。|
|サーバー プリンシパルにマップされていないデータベース ユーザー|ユーザーに対する IMPERSONATE 権限、db_securityadmin 固定データベース ロールのメンバーシップ、db_owner 固定データベース ロールのメンバーシップ、または sysadmin 固定サーバー ロールのメンバーシップ。|
|データベース ロール|ロールに対する ALTER 権限、db_securityadmin 固定データベース ロールのメンバーシップ、db_owner 固定データベース ロールのメンバーシップ、または sysadmin 固定サーバー ロールのメンバーシップ。|
|アプリケーション ロール|ロールに対する ALTER 権限、db_securityadmin 固定データベース ロールのメンバーシップ、db_owner 固定データベース ロールのメンバーシップ、または sysadmin 固定サーバー ロールのメンバーシップ。|

オブジェクトの所有者は、所有するオブジェクトの権限を許可できます。 セキュリティ保護可能なリソースに対して CONTROL 権限があるプリンシパルは、そのリソースの権限を許可できます。

sysadmin 固定サーバー ロールのメンバーなど、CONTROL SERVER 権限が許可されているユーザーは、サーバー内のセキュリティ保護可能なリソースに対する権限を許可できます。

## <a name="examples"></a>例

### <a name="a-granting-permission-to-create-tables"></a>A. テーブルを作成する権限を許可する

次の例では、`AdventureWorks` データベースでの `CREATE TABLE` 権限を、ユーザー `MelanieK` に対して許可します。

```sql
USE AdventureWorks;
GRANT CREATE TABLE TO MelanieK;
GO
```

### <a name="b-granting-showplan-permission-to-an-application-role"></a>B. SHOWPLAN 権限をアプリケーション ロールに許可する

 次の例では、`AdventureWorks2012` データベースでの `SHOWPLAN` 権限を、アプリケーション ロール `AuditMonitor` に対して許可します。

**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降、[!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)]

```sql
USE AdventureWorks2012;
GRANT SHOWPLAN TO AuditMonitor;
GO
```

### <a name="c-granting-create-view-with-grant-option"></a>C. GRANT OPTION を指定して CREATE VIEW 権限を許可する

 次の例では、ユーザー `CarmineEs` に対して、`AdventureWorks2012` データベースでの `CREATE VIEW` 権限を許可すると共に、他のプリンシパルに `CREATE VIEW` を許可する権利を与えます。

```sql
USE AdventureWorks2012;
GRANT CREATE VIEW TO CarmineEs WITH GRANT OPTION;
GO
```

### <a name="d-granting-control-permission-to-a-database-user"></a>D. データベース ユーザーに CONTROL 権限を許可する

 次の例では、`AdventureWorks2012` データベースでの `CONTROL` 権限を、データベース ユーザー `Sarah` に対して許可します。 ユーザーはデータベース内に存在する必要があり、コンテキストはデータベースに設定されている必要があります。

```sql
USE AdventureWorks2012;
GRANT CONTROL ON DATABASE::AdventureWorks2012 TO Sarah;
GO
```

## <a name="see-also"></a>参照

- [sys.database_permissions](../../relational-databases/system-catalog-views/sys-database-permissions-transact-sql.md)
- [sys.database_principals](../../relational-databases/system-catalog-views/sys-database-principals-transact-sql.md)
- [CREATE DATABASE](../../t-sql/statements/create-database-transact-sql.md)
- [GRANT](../../t-sql/statements/grant-transact-sql.md)
- [アクセス許可](../../relational-databases/security/permissions-database-engine.md)
- [プリンシパル](../../relational-databases/security/authentication-access/principals-database-engine.md)
