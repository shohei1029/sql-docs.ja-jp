---
description: ALTER AUTHORIZATION (Transact-SQL)
title: ALTER AUTHORIZATION (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 02/01/2021
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- ALTER_AUTHORIZATION_TSQL
- ALTER AUTHORIZATION
dev_langs:
- TSQL
helpviewer_keywords:
- owners [SQL Server], transferring
- modifying entity ownership
- full-text search [SQL Server], permissions
- owners [SQL Server], entities
- ALTER AUTHORIZATION statement
- entity ownership [SQL Server]
- transferring ownership
- search property lists [SQL Server], permissions
- TAKE OWNERSHIP
ms.assetid: 8c805ae2-91ed-4133-96f6-9835c908f373
author: VanMSFT
ms.author: vanto
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 66574b67a8a98c010cab144d3affceb2d822ee6c
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104734377"
---
# <a name="alter-authorization-transact-sql"></a>ALTER AUTHORIZATION (Transact-SQL)

[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  セキュリティ保護可能なエンティティの所有権を変更します。

 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)

## <a name="syntax"></a>構文

```syntaxsql
-- Syntax for SQL Server
ALTER AUTHORIZATION
    ON [ <class_type>:: ] entity_name
    TO { principal_name | SCHEMA OWNER }
    [;]

<class_type> ::=
     {
      OBJECT | ASSEMBLY | ASYMMETRIC KEY | AVAILABILITY GROUP | CERTIFICATE
    | CONTRACT | TYPE | DATABASE | ENDPOINT | FULLTEXT CATALOG
    | FULLTEXT STOPLIST | MESSAGE TYPE | REMOTE SERVICE BINDING
    | ROLE | ROUTE | SCHEMA | SEARCH PROPERTY LIST | SERVER ROLE
    | SERVICE | SYMMETRIC KEY | XML SCHEMA COLLECTION
     }
```

```syntaxsql
-- Syntax for SQL Database

ALTER AUTHORIZATION
    ON [ <class_type>:: ] entity_name
    TO { principal_name | SCHEMA OWNER }
    [;]

<class_type> ::=
     {
    OBJECT | ASSEMBLY | ASYMMETRIC KEY | CERTIFICATE
     | TYPE | DATABASE | FULLTEXT CATALOG
     | FULLTEXT STOPLIST
     | ROLE | SCHEMA | SEARCH PROPERTY LIST
     | SYMMETRIC KEY | XML SCHEMA COLLECTION
     }
```

```syntaxsql
-- Syntax for Azure Synapse Analytics

ALTER AUTHORIZATION ON
     [ <class_type> :: ] <entity_name>
     TO { principal_name | SCHEMA OWNER }
    [;]

    <class_type> ::= {
    SCHEMA
     | OBJECT
    }

    <entity_name> ::=
    {
    schema_name
     | [ schema_name. ] object_name
    }
```

```syntaxsql
-- Syntax for Parallel Data Warehouse

ALTER AUTHORIZATION ON
     [ <class_type> :: ] <entity_name>
     TO { principal_name | SCHEMA OWNER }
    [;]

<class_type> ::= {
    DATABASE
     | SCHEMA
     | OBJECT
    }

<entity_name> ::=
    {
    database_name
     | schema_name
     | [ schema_name. ] object_name
    }
```

[!INCLUDE[synapse-analytics-od-unsupported-syntax](../../includes/synapse-analytics-od-unsupported-syntax.md)]

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数

\<class_type> 所有者を変更するエンティティのセキュリティ保護可能なクラスを指定します。 既定値は OBJECT です。

|クラス|Product|
|-|-|
|OBJECT|**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降、[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]、[!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)]、[!INCLUDE[ssPDW](../../includes/sspdw-md.md)]。|
|ASSEMBLY|**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降、[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。|
|ASYMMETRIC KEY|**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降、[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。|
|AVAILABILITY GROUP |**適用対象**:SQL Server 2012 以降。|
|CERTIFICATE|**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降、[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。|
|CONTRACT|**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。|
|DATABASE|**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降、[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。 詳細については、「[データベースに対する ALTER AUTHORIZATION](#alter-authorization-for-databases)」を参照してください。|
|ENDPOINT|**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。|
|FULLTEXT CATALOG|**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降、[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。|
|FULLTEXT STOPLIST|**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降、[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。|
|MESSAGE TYPE|**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。|
|REMOTE SERVICE BINDING|**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。|
|ROLE|**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降、[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。|
|ROUTE|**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。|
|SCHEMA|**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降、[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]、[!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)]、[!INCLUDE[ssPDW](../../includes/sspdw-md.md)]。|
|SEARCH PROPERTY LIST|**適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降、[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。|
|SERVER ROLE|**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。|
|SERVICE|**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。|
|SYMMETRIC KEY|**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降、[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。|
|TYPE|**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降、[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。|
|XML SCHEMA COLLECTION|**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降、[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。|

*entity_name* はエンティティの名前です。

*principal_name* | SCHEMA OWNER エンティティの所有者となるセキュリティ プリンシパルの名前。 データベース オブジェクトはデータベース プリンシパル (データベース ユーザーまたはデータベース ロール) が所有する必要があります。 サーバー オブジェクト (データベースなど) はサーバー プリンシパル (ログイン) が所有する必要があります。 **SCHEMA OWNER** を *principal_name- として指定して、オブジェクトのスキーマを所有するプリンシパルがオブジェクトを所有する必要があることを示します。

## <a name="remarks"></a>解説

ALTER AUTHORIZATION は、所有者が存在するエンティティの所有権を変更するときに使用できます。 データベースに含まれるエンティティの所有権は、任意のデータベース レベルのプリンシパルに譲渡できます。 サーバー レベルのエンティティの所有権は、サーバー レベルのプリンシパルのみに譲渡できます。

> [!IMPORTANT]
> [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] 以降では、ユーザーは他のデータベース ユーザーが所有するスキーマに含まれる OBJECT または TYPE を所有できます。 これは、以前のバージョンの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]の動作から変更されています。 詳しくは、「[OBJECTPROPERTY &#40;Transact-SQL&#41;](../../t-sql/functions/objectproperty-transact-sql.md)」および「[TYPEPROPERTY &#40;Transact-SQL&#41;](../../t-sql/functions/typeproperty-transact-sql.md)」をご覧ください。

スキーマに含まれるエンティティのうち、種類が "オブジェクト" のエンティティ (テーブル、ビュー、関数、プロシージャ、キュー、およびシノニム) の所有権は譲渡できます。

リンク サーバー、統計、制約、ルール、デフォルト、トリガー、[!INCLUDE[ssSB](../../includes/sssb-md.md)] キュー、資格情報、パーティション関数、パーティション構成、データベース マスター キー、サービス マスター キー、およびイベント通知の各エンティティの所有権は譲渡できません。

セキュリティ保護可能なリソース クラス (サーバー、ログイン、ユーザー、アプリケーション ロール、および列) のメンバーの所有権は譲渡できません。

SCHEMA OWNER オプションは、スキーマに含まれるエンティティの所有権を譲渡する場合にのみ有効です。 SCHEMA OWNER を使用すると、エンティティの所有権は、所属するスキーマの所有者に譲渡されます。 スキーマに含まれるのは、OBJECT、TYPE、または XML SCHEMA COLLECTION クラスのエンティティのみです。

対象のエンティティがデータベース以外であり、エンティティを新しい所有者に譲渡する場合は、すべての権限が削除されます。

> [!CAUTION]
> [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] でのスキーマの動作は、以前のバージョンの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] から変更されました。 コードで、スキーマがデータベース ユーザーと同じであることが前提となっている場合、正しい結果が返されない場合があります。 以下の DDL ステートメントが使用されているデータベースでは、sysobjects などの古いカタログ ビューを使用してはいけません:CREATE SCHEMA、ALTER SCHEMA、DROP SCHEMA、CREATE USER、ALTER USER、DROP USER、CREATE ROLE、ALTER ROLE、DROP ROLE、CREATE APPROLE、ALTER APPROLE、DROP APPROLE、ALTER AUTHORIZATION。 これらのいずれかのステートメントが使用されたデータベースでは、新しいカタログ ビューを使用する必要があります。 新しいカタログ ビューでは、[!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] で導入されたプリンシパルとスキーマの分離が考慮されます。 カタログ ビューの詳細については、「[カタログ ビュー &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/catalog-views-transact-sql.md)」を参照してください。

 また、次の点も注意してください。

> [!IMPORTANT]
> オブジェクトの所有者を調べる唯一の信頼性のある方法は、**sys.objects** カタログ ビューに対するクエリを実行する方法です。 型の所有者を調べる唯一の信頼性のある方法は、TYPEPROPERTY 関数を使用する方法です。

## <a name="special-cases-and-conditions"></a>特殊ケースと条件

次の表は、権限を変更する場合の特殊ケースと例外、および条件の一覧です。

|クラス|条件|
|-----------|---------------|
|OBJECT|トリガー、制約、ルール、デフォルト、統計、システム オブジェクト、キュー、インデックス付きビュー、またはインデックス付きビューのあるテーブルの所有権は変更できません。|
|SCHEMA|所有権を譲渡すると、スキーマに含まれるオブジェクトの所有者が明示的に指定されていない場合は、そのオブジェクトに対する権限が削除されます。 sys、dbo、または information_schema の所有者は変更できません。|
|TYPE|sys または information_schema に属する TYPE の所有権は変更できません。|
|CONTRACT、MESSAGE TYPE、SERVICE|システム エンティティの所有権は変更できません。|
|SYMMETRIC KEY|グローバル一時キーの所有権は変更できません。|
|CERTIFICATE または ASYMMETRIC KEY|これらのエンティティの所有権をロールまたはグループに譲渡することはできません。|
|ENDPOINT|プリンシパルは、ログインであることが必要です。|

## <a name="alter-authorization-for-databases"></a> データベースに対する ALTER AUTHORIZATION

### <a name="for-sql-server"></a>SQL Server の場合

**新しい所有者の要件:** 新しい所有者プリンシパルは、次のいずれかである必要があります。

- SQL サーバー認証ログイン。
- Windows ユーザー (グループではなく) を表す Windows 認証ログイン。
- Windows グループを表す Windows 認証ログインを使って認証を行う Windows ユーザー。

**ALTER AUTHORIZATION ステートメントを実行するユーザーの要件:** **sysadmin** 固定サーバー ロールのメンバーではないユーザーの場合は、データベースに対する TAKE OWNERSHIP アクセス許可以上と、新しい所有者ログインに対する IMPERSONATE アクセス許可が必要です。

### <a name="for-azure-sql-database"></a>Azure SQL Database の場合

**新しい所有者の要件:** 新しい所有者プリンシパルは、次のいずれかである必要があります。

- SQL サーバー認証ログイン。
- Azure AD 内に存在するフェデレーション ユーザー (グループではなく)。
- Azure AD 内に存在するマネージド ユーザー (グループではなく) またはアプリケーション。

新しい所有者が Azure Active Directory ユーザーの場合は、新しい所有者が新しい DBO になるデータベース内にユーザーとして存在することはできません。 このような Azure AD ユーザーは、ALTER AUTHORIZATION ステートメントを実行して新しいユーザーにデータベース所有権を変更する前にまず、データベースから削除する必要があります。 SQL Database での Azure Active Directory ユーザーの構成について詳しくは、[Azure Active Directory 認証を使用して SQL Database または [!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] に接続する](https://azure.microsoft.com/documentation/articles/sql-database-aad-authentication/)方法に関するページをご覧ください。

**ALTER AUTHORIZATION ステートメントを実行するユーザーの要件:** データベースの所有者を変更するには、そのデータベースに接続する必要があります。

次の種類のアカウントは、データベースの所有者を変更できます。

- サーバー レベルのプリンシパル ログイン (SQL Database サーバー作成時にプロビジョニングされた SQL Azure 管理者)。
- Azure SQL Server の Azure Active Directory 管理者。
- データベースの現在の所有者。

次の表は要件をまとめたものです。

実行者  |移行先  |結果
---------|---------|---------
SQL サーバー認証ログイン  |SQL サーバー認証ログイン|Success
SQL サーバー認証ログイン  |Azure AD ユーザー|失敗
Azure AD ユーザー  |SQL サーバー認証ログイン|Success
Azure AD ユーザー  |Azure AD ユーザー|Success

データベースの Azure AD 所有者を確認するには、ユーザー データベース (この例では `testdb`) で次の Transact-SQL コマンドを実行します。

```sql
SELECT CAST(owner_sid as uniqueidentifier) AS Owner_SID
FROM sys.databases
WHERE name = 'testdb';
```

出力は識別子 (6D8B81F6-7C79-444C-8858-4AF896C03C67 など) になります。これは、`richel@cqclinic.onmicrosoft.com` に割り当てられた Azure AD ObjectID に対応します。SQL Server 認証ログイン ユーザーがデータベース所有者である場合、データベースの所有者を確認するには、master データベースで次のステートメントを実行します。

```sql
SELECT d.name, d.owner_sid, sl.name
FROM sys.databases AS d
JOIN sys.sql_logins AS sl
ON d.owner_sid = sl.sid;

```

### <a name="best-practice"></a>ベスト プラクティス

Azure AD ユーザーをデータベースの個人所有者として使う代わりに、Azure AD グループを **db_owner** 固定データベース ロールのメンバーとして使います。 次の手順では、データベース所有者として無効なログインを構成し、Azure Active Directory グループ (`mydbogroup`) を **db_owner** ロールのメンバーにする方法を示します。

1. Azure AD 管理者として SQL Server にログインし、データベースの所有者を無効化された SQL Server 認証ログインに変更ます。 たとえば、ユーザー データベースから次のコマンドを実行します。

   ```sql
   ALTER AUTHORIZATION ON database::testdb TO DisabledLogin;
   ```

1. データベースを所有する Azure AD グループを作成し、そのグループをユーザー データベースにユーザーとして追加します。 次に例を示します。

   ```sql
   CREATE USER [mydbogroup] FROM EXTERNAL PROVIDER;
   ```

1. ユーザー データベースで、Azure AD グループを表すユーザーを、**db_owner** 固定データベース ロールに追加します。 次に例を示します。

   ```sql
   ALTER ROLE db_owner ADD MEMBER mydbogroup;
   ```

これで、`mydbogroup` のメンバーは、**db_owner** ロールのメンバーとしてデータベースを集中管理できます。

- このグループのメンバーが Azure AD グループから削除されると、このデータベースの dbo アクセス許可を自動的に失います。
- 同様に、新しいメンバーが `mydbogroup` Azure AD グループに追加された場合ば、このデータベースの dbo アクセス許可を自動的に取得します。

特定のユーザーが有効な dbo アクセス許可を持つかどうかを確認するには、ユーザーに次のステートメントを実行させます。

```sql
SELECT IS_MEMBER ('db_owner');
```

戻り値 1 は、ユーザーがロールのメンバーであることを示します。

## <a name="permissions"></a>アクセス許可

エンティティに対する TAKE OWNERSHIP 権限が必要です。 新しい所有者がステートメントを実行するユーザーではない場合は、次の条件に応じた権限が必要になります。1) 新しい所有者がユーザーまたはログインの場合は、新しい所有者に対する IMPERSONATE 権限。2) 新しい所有者がロールまたはロールのメンバーシップの場合は、ロールに対する ALTER 権限。3) 新しい所有者がアプリケーション ロールの場合は、アプリケーション ロールに対する ALTER 権限。

## <a name="examples"></a>例

### <a name="a-transfer-ownership-of-a-table"></a>A. テーブルの所有権を譲渡する

次の例では、テーブル `Sprockets` の所有権をユーザー `MichikoOsada` に譲渡します。 このテーブルは、スキーマ `Parts` 内にあります。

```sql
ALTER AUTHORIZATION ON OBJECT::Parts.Sprockets TO MichikoOsada;
GO
```

クエリは次のようにもできます。

```sql
ALTER AUTHORIZATION ON Parts.Sprockets TO MichikoOsada;
GO
```

オブジェクトのスキーマがステートメントの一部として含まれない場合、[!INCLUDE[ssDE](../../includes/ssde-md.md)] はユーザーの既定のスキーマでオブジェクトを検索します。 次に例を示します。

```sql
ALTER AUTHORIZATION ON Sprockets TO MichikoOsada;
ALTER AUTHORIZATION ON OBJECT::Sprockets TO MichikoOsada;
```

### <a name="b-transfer-ownership-of-a-view-to-the-schema-owner"></a>B. ビューの所有権をスキーマの所有者に譲渡する

次の例では、ビュー `ProductionView06` の所有権を、所属するスキーマの所有者に譲渡します。 このビューは、スキーマ `Production` 内にあります。

```sql
ALTER AUTHORIZATION ON OBJECT::Production.ProductionView06 TO SCHEMA OWNER;
GO
```

### <a name="c-transfer-ownership-of-a-schema-to-a-user"></a>C. スキーマの所有権をユーザーに譲渡する

次の例では、スキーマ `SeattleProduction11` の所有権をユーザー `SandraAlayo` に譲渡します。

```sql
ALTER AUTHORIZATION ON SCHEMA::SeattleProduction11 TO SandraAlayo;
GO
```

### <a name="d-transfer-ownership-of-an-endpoint-to-a-sql-server-login"></a>D. エンドポイントの所有権を SQL Server ログインに譲渡する

次の例では、エンドポイント `CantabSalesServer1` の所有権を `JaePak` に譲渡します。 エンドポイントはサーバー レベルでセキュリティ保護可能なリソースであるため、エンドポイントを譲渡できるのはサーバー レベルのプリンシパルのみです。

**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。

```sql
ALTER AUTHORIZATION ON ENDPOINT::CantabSalesServer1 TO JaePak;
GO
```

### <a name="e-changing-the-owner-of-a-table"></a>E. テーブルの所有者を変更する

以下の各例では、`Parts` データベースの `Sprockets` テーブルの所有者を、データベース ユーザー `MichikoOsada` に変更します。

```sql
ALTER AUTHORIZATION ON Sprockets TO MichikoOsada;
ALTER AUTHORIZATION ON dbo.Sprockets TO MichikoOsada;
ALTER AUTHORIZATION ON OBJECT::Sprockets TO MichikoOsada;
ALTER AUTHORIZATION ON OBJECT::dbo.Sprockets TO MichikoOsada;
```

### <a name="f-changing-the-owner-of-a-database"></a>F. データベースの所有者を変更する

 **適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降、[!INCLUDE[ssPDW](../../includes/sspdw-md.md)]、[!INCLUDE[ssSDS_md](../../includes/sssds-md.md)]。

 次の例では、`Parts` データベースの所有者をログイン `MichikoOsada` に変更します。

```sql
ALTER AUTHORIZATION ON DATABASE::Parts TO MichikoOsada;
```

### <a name="g-changing-the-owner-of-a-sql-database-to-an-azure-ad-user"></a>G. SQL Database の所有者を Azure AD ユーザーに変更する

次の例では、`cqclinic.onmicrosoft.com` という名前の Active Directory を使用する組織内の SQL Server の Azure Active Directory 管理者は、次のコマンドを使用して、データベース `targetDB` の現在の所有権を変更し、AAD ユーザー `richel@cqclinic.onmicorsoft.com` を新しいデータベース所有者にすることができます。

```sql
ALTER AUTHORIZATION ON database::targetDB TO [rachel@cqclinic.onmicrosoft.com];
```

Azure AD では、ユーザー名を囲む角かっこ `[]` 必要です。

## <a name="see-also"></a>参照
 「[OBJECTPROPERTY &#40;Transact-SQL&#41;](../../t-sql/functions/objectproperty-transact-sql.md)」「[TYPEPROPERTY &#40;Transact-SQL&#41;](../../t-sql/functions/typeproperty-transact-sql.md)」「[EVENTDATA &#40;Transact-SQL&#41;](../../t-sql/functions/eventdata-transact-sql.md)」

