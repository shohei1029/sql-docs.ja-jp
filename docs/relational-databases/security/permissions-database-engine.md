---
title: 権限 (データベース エンジン) | Microsoft Docs
description: この SQL Server 権限の完全な一覧を参照して、使用するプラットフォームに適用される権限を確認します。
ms.custom: ''
ms.date: 10/30/2020
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: security
ms.topic: conceptual
f1_keywords:
- sql13.swb.databaseuser.permissions.database.f1--May use common.permissions
- sql13.swb.databaseuser.permissions.object.f1--May use common.permissions
helpviewer_keywords:
- REFERENCES permission
- permissions [SQL Server]
- security [SQL Server], permissions
- naming conventions [SQL Server]
ms.assetid: f28e3dea-24e6-4a81-877b-02ec4c7e36b9
author: AndreasWolter
ms.author: anwolter
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: ea48dc9d5b0905ad10d98ccb349054421f296606
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104748112"
---
# <a name="permissions-database-engine"></a>権限 (データベース エンジン)
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のセキュリティ保護可能なリソースにはすべて、プリンシパルに許可できる権限が関連付けられています。 [!INCLUDE[ssDE](../../includes/ssde-md.md)] の権限は、ログインおよびサーバー ロールに割り当てられたサーバー レベル、およびデータベース ユーザーおよびデータベース ロールに割り当てられたデータベース レベルで管理されます。 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] のモデルには、データベース権限用に同じシステムがありますが、サーバー レベルの権限は使用できません。 このトピックでは、権限の一覧を示します。 アクセス許可の一般的な実装については、「 [Getting Started with Database Engine Permissions](../../relational-databases/security/authentication-access/getting-started-with-database-engine-permissions.md)」を参照してください。  
  
[!INCLUDE[ssSQLv15_md](../../includes/sssql19-md.md)] の権限の合計数 は 248 です。 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] は 254 の権限を公開します。 ほとんどの権限はすべてのプラットフォームに適用されますがが、一部は適用されません。 たとえば、サーバー レベルの権限は [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] に対して付与することができず、[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] で意味を成すのは、いくつかの権限のみです。
新しい権限が、新しいリリースで徐々に導入されています。 [!INCLUDE [sssql17-md](../../includes/sssql17-md.md)] は 238 の権限を公開します。 [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] は 230 の権限を公開します。 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] は 219 の権限を公開します。 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] は 214 の権限を公開します。 [!INCLUDE[ssKilimanjaro](../../includes/sskilimanjaro-md.md)] は 195 の権限を公開します。 [sys.fn_builtin_permissions](../../relational-databases/system-functions/sys-fn-builtin-permissions-transact-sql.md) トピックでは、最近のバージョンでどの権限が新しいかが明確に記載されています。

権限を理解したら、 [GRANT](../../t-sql/statements/grant-transact-sql.md)、 [REVOKE](../../t-sql/statements/revoke-transact-sql.md)、および [DENY](../../t-sql/statements/deny-transact-sql.md) ステートメントを使用してサーバー レベルの権限をログインとデータベース レベルの権限ユーザーに付与します。 たとえば次のようになります。   
```sql
GRANT SELECT ON OBJECT::HumanResources.Employee TO Larry;
REVOKE SELECT ON OBJECT::HumanResources.Employee TO Larry;
```   
権限システムの計画のヒントについては、「 [データベース エンジンの権限の概要](../../relational-databases/security/authentication-access/getting-started-with-database-engine-permissions.md)」を参照してください。
  
##  <a name="permissions-naming-conventions"></a><a name="_conventions"></a> 権限の名前付け規則  
 ここでは、権限に名前を付ける際に従う一般的な規則について説明します。  
  
-   CONTROL  
  
     権限を与えられたユーザーに所有権のような権限を与えます。 権限を与えられたユーザーは、事実上、セキュリティ保護可能なリソースに対する定義済みのすべての権限を持っています。 CONTROL を許可されたプリンシパルには、セキュリティ保護可能なリソースに対する権限も許可できます。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セキュリティ モデルは階層構造であるため、特定のスコープの CONTROL には、そのスコープ下のセキュリティ保護可能なすべてのリソースに対する CONTROL が暗黙的に含まれます。 たとえば、データベースに対する CONTROL は、データベースに対するすべての権限、データベース内のすべてのアセンブリに対するすべての権限、データベース内のすべてのスキーマに対するすべての権限、およびデータベース内のすべてのスキーマに含まれているオブジェクトに対するすべての権限を意味します。  
  
-   ALTER  
  
     セキュリティ保護可能な特定のリソースのプロパティを変更できるようにします。ただし、所有権は変更できません。 スコープに対して許可された場合、ALTER では、そのスコープに含まれているセキュリティ保護可能なすべてのリソースの変更、作成、または削除も行えるようになります。 たとえば、スキーマに対する ALTER 権限には、スキーマのオブジェクトを作成、変更、および削除する権限が含まれています。  
  
-   ALTER ANY \<*Server Securable*>。ここで、*Server Securable* には、任意のセキュリティ保護可能なサーバーを指定できます。  
  
     *Server Securable* の個々のインスタンスを作成、変更、削除できるようにします。 たとえば、ALTER ANY LOGIN では、インスタンス内の任意のログインを作成、変更、または削除できます。  
  
-   ALTER ANY \<*Database Securable*>。ここで、*Database Securable* には、データベース レベルの任意のセキュリティ保護可能なリソースを指定できます。  
  
     *Database Securable* の個々のインスタンスを CREATE、ALTER、DROP できるようにします。 たとえば、ALTER ANY SCHEMA では、データベース内の任意のスキーマを作成、変更、または削除できます。  
  
-   TAKE OWNERSHIP  
  
     権限を与えられたユーザーが、許可されたセキュリティ保護可能なリソースの所有権を使用できるようにします。  
  
-   IMPERSONATE \<*Login*>  
  
     権限を与えられたユーザーが、Login の権限を借用できるようにします。  
  
-   IMPERSONATE \<*User*>  
  
     権限を与えられたユーザーが、User の権限を借用できるようにします。  
  
-   CREATE \<*Server Securable*>  
  
     権限を与えられたユーザーが *Server Securable* を作成できるようにします。  
  
-   CREATE \<*Database Securable*>  
  
     権限を与えられたユーザーが *Database Securable* を作成できるようにします。  
  
-   CREATE \<*Schema-contained Securable*>  
  
     スキーマに含まれているセキュリティ保護可能なリソースを作成できるようにします。 ただし、特定のスキーマ内でセキュリティ保護可能なリソースを作成するには、そのスキーマに対する ALTER 権限が必要です。  
  
-   VIEW DEFINITION  
  
     権限を与えられたユーザーがメタデータにアクセスできるようにします。  
  
-   REFERENCES  
  
     テーブルを参照する FOREIGN KEY 制約を作成するには、そのテーブルに対する REFERENCES 権限が必要です。  
  
     オブジェクトを参照する `WITH SCHEMABINDING` 句を含む関数またはビューを作成するには、そのオブジェクトに対する REFERENCES 権限が必要です。  
  
## <a name="chart-of-sql-server-permissions"></a>SQL Server 権限の一覧表  
[!INCLUDE[database-engine-permissions](../../includes/paragraph-content/database-engine-permissions.md)]
  
##  <a name="permissions-applicable-to-specific-securables"></a><a name="_securables"></a> 特定のセキュリティ保護可能なリソースに適用できる権限  
 次の表に、主な権限のクラスおよび各権限を適用できるセキュリティ保護可能なリソースの種類を示します。  
  
|権限|適用対象|  
|----------------|----------------|  
|ALTER|オブジェクトのすべてのクラス (TYPE を除く)。|  
|CONTROL|オブジェクトのすべてのクラス: <br />AGGREGATE、<br />APPLICATION ROLE、<br />ASSEMBLY、<br />ASYMMETRIC KEY、<br />AVAILABILITY GROUP、<br />CERTIFICATE、<br />CONTRACT、<br />CREDENTIALS、DATABASE、<br />DATABASE SCOPED CREDENTIAL、<br /> DEFAULT、<br />ENDPOINT、<br />FULLTEXT CATALOG、<br />FULLTEXT STOPLIST、<br />FUNCTION、<br />LOGIN、<br />MESSAGE TYPE、<br />PROCEDURE、<br />QUEUE、 <br />REMOTE SERVICE BINDING、<br />ROLE、<br />ROUTE、<br />RULE、<br />SCHEMA、<br />SEARCH PROPERTY LIST、<br />SERVER、<br />SERVER ROLE、<br />SERVICE、<br />SYMMETRIC KEY、<br />SYNONYM、<br />TABLE、<br />TYPE、<br /> USER,<br />VIEW、および<br />XML SCHEMA COLLECTION|  
|DELETE|オブジェクトのすべてのクラス (DATABASE SCOPED CONFIGURATION、SERVER、および TYPE を除く)。|  
|EXECUTE|CLR 型、外部スクリプト、プロシージャ ([!INCLUDE[tsql](../../includes/tsql-md.md)] と CLR)、スカラー関数、集計関数 ([!INCLUDE[tsql](../../includes/tsql-md.md)] と CLR)、およびシノニム|  
|IMPERSONATE|ログインとユーザー|  
|INSERT|シノニム、テーブルと列、ビューと列。 データベース、スキーマ、またはオブジェクト レベルで権限を付与できます。|  
|RECEIVE|[!INCLUDE[ssSB](../../includes/sssb-md.md)] キュー|  
|REFERENCES|AGGREGATE、<br />ASSEMBLY、<br />ASYMMETRIC KEY、<br />CERTIFICATE、<br />CONTRACT、<br />DATABASE、<br />DATABASE SCOPED CREDENTIAL、<br />FULLTEXT CATALOG、<br />FULLTEXT STOPLIST、<br />FUNCTION、<br />MESSAGE TYPE、<br />PROCEDURE、<br />QUEUE、 <br />RULE、<br />SCHEMA、<br />SEARCH PROPERTY LIST、<br />SEQUENCE OBJECT、 <br />SYMMETRIC KEY、<br />TABLE、<br />TYPE、<br />VIEW、および<br />XML SCHEMA COLLECTION|  
|SELECT|シノニム、テーブルと列、ビューと列。 データベース、スキーマ、またはオブジェクト レベルで権限を付与できます。|  
|TAKE OWNERSHIP|オブジェクトのすべてのクラス (DATABASE SCOPED CONFIGURATION、LOGIN、SERVER、および USER を除く)。|  
|UPDATE|シノニム、テーブルと列、ビューと列。 データベース、スキーマ、またはオブジェクト レベルで権限を付与できます。|  
|VIEW CHANGE TRACKING|スキーマとテーブル|  
|VIEW DEFINITION|オブジェクトのすべてのクラス (DATABASE SCOPED CONFIGURATION および SERVER を除く)。|  
  
> [!CAUTION]  
>  セットアップ時にシステム オブジェクトに付与された既定のアクセス許可は、発生する可能性のある脅威に対して慎重に評価されているため、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインストールの際、セキュリティ強化の一部として変更する必要はありません。 システム オブジェクトのアクセス許可の変更はどのようなものであっても、機能を制限または中断する可能性があり、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインストールがサポートされていない状態のままになる場合があります。  
  
##  <a name="sql-server-permissions"></a><a name="_permissions"></a> SQL Server 権限  
 次の表に、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のすべての権限の一覧を示します。 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] のアクセス許可は、サポートされている基本のセキュリティ保護可能なリソースにのみ使用できます。 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]ではサーバー レベルのアクセス許可を付与することはできませんが、代わりにデータベースのアクセス許可を付与できる場合があります。  
  
|セキュリティ保護可能な基本リソース|セキュリティ保護可能な基本リソースに対する粒度の細かい権限|権限の種類のコード|基本リソースを含んでいる別のセキュリティ保護可能なリソース|セキュリティ保護可能なコンテナーに対する権限 (基本リソースに対する粒度の細かい権限を暗示)|  
|--------------------|--------------------------------------------|--------------------------|--------------------------------------------|------------------------------------------------------------------------------------------|  
|APPLICATION ROLE|ALTER|AL|DATABASE|ALTER ANY APPLICATION ROLE|  
|APPLICATION ROLE|CONTROL|CL|DATABASE|CONTROL|  
|APPLICATION ROLE|VIEW DEFINITION|VW|DATABASE|VIEW DEFINITION|  
|ASSEMBLY|ALTER|AL|DATABASE|ALTER ANY ASSEMBLY|  
|ASSEMBLY|CONTROL|CL|DATABASE|CONTROL|  
|ASSEMBLY|REFERENCES|RF|DATABASE|REFERENCES|  
|ASSEMBLY|TAKE OWNERSHIP|TO|DATABASE|CONTROL|  
|ASSEMBLY|VIEW DEFINITION|VW|DATABASE|VIEW DEFINITION|  
|ASYMMETRIC KEY|ALTER|AL|DATABASE|ALTER ANY ASYMMETRIC KEY|  
|ASYMMETRIC KEY|CONTROL|CL|DATABASE|CONTROL|  
|ASYMMETRIC KEY|REFERENCES|RF|DATABASE|REFERENCES|  
|ASYMMETRIC KEY|TAKE OWNERSHIP|TO|DATABASE|CONTROL|  
|ASYMMETRIC KEY|VIEW DEFINITION|VW|DATABASE|VIEW DEFINITION|  
|AVAILABILITY GROUP|ALTER|AL|SERVER|ALTER ANY AVAILABILITY GROUP|  
|AVAILABILITY GROUP|CONTROL|CL|SERVER|CONTROL SERVER|  
|AVAILABILITY GROUP|TAKE OWNERSHIP|TO|SERVER|CONTROL SERVER|  
|AVAILABILITY GROUP|VIEW DEFINITION|VW|SERVER|VIEW ANY DEFINITION|  
|CERTIFICATE|ALTER|AL|DATABASE|ALTER ANY CERTIFICATE|  
|CERTIFICATE|CONTROL|CL|DATABASE|CONTROL|  
|CERTIFICATE|REFERENCES|RF|DATABASE|REFERENCES|  
|CERTIFICATE|TAKE OWNERSHIP|TO|DATABASE|CONTROL|  
|CERTIFICATE|VIEW DEFINITION|VW|DATABASE|VIEW DEFINITION|  
|CONTRACT|ALTER|AL|DATABASE|ALTER ANY CONTRACT|  
|CONTRACT|CONTROL|CL|DATABASE|CONTROL|  
|CONTRACT|REFERENCES|RF|DATABASE|REFERENCES|  
|CONTRACT|TAKE OWNERSHIP|TO|DATABASE|CONTROL|  
|CONTRACT|VIEW DEFINITION|VW|DATABASE|VIEW DEFINITION|  
|DATABASE|ADMINISTER DATABASE BULK OPERATIONS|DABO|SERVER|CONTROL SERVER|
|DATABASE|ALTER|AL|SERVER|ALTER ANY DATABASE|  
|DATABASE|ALTER ANY APPLICATION ROLE|ALAR|SERVER|CONTROL SERVER|  
|DATABASE|ALTER ANY ASSEMBLY|ALAS|SERVER|CONTROL SERVER|  
|DATABASE|ALTER ANY ASYMMETRIC KEY|ALAK|SERVER|CONTROL SERVER|  
|DATABASE|ALTER ANY CERTIFICATE|ALCF|SERVER|CONTROL SERVER|  
|DATABASE|任意の列の暗号化キーを変更します。|ALCK<br /><br /> 適用対象 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] から現在のバージョンまで)、 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。|SERVER|CONTROL SERVER|  
|DATABASE|ALTER ANY COLUMN MASTER KEY|ALCM<br /><br /> 適用対象 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] から現在のバージョンまで)、 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。|SERVER|CONTROL SERVER|  
|DATABASE|ALTER ANY CONTRACT|ALSC|SERVER|CONTROL SERVER|  
|DATABASE|ALTER ANY DATABASE AUDIT|ALDA|SERVER|ALTER ANY SERVER AUDIT|  
|DATABASE|ALTER ANY DATABASE DDL TRIGGER|ALTG|SERVER|CONTROL SERVER|  
|DATABASE|ALTER ANY DATABASE EVENT NOTIFICATION|ALED|SERVER|ALTER ANY EVENT NOTIFICATION|  
|DATABASE|ALTER ANY DATABASE EVENT SESSION|AADS<br /><br /> [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]に適用されます。|SERVER|ALTER ANY EVENT SESSION|  
|DATABASE|ALTER ANY DATABASE SCOPED CONFIGURATION|ALDC<br /><br /> 適用対象 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] から現在のバージョンまで)、 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。|SERVER|CONTROL SERVER|  
|DATABASE|ALTER ANY DATASPACE|ALDS|SERVER|CONTROL SERVER|  
|DATABASE|すべての外部データ ソースを変更します。|AEDS|SERVER|CONTROL SERVER|  
|DATABASE|任意の外部のファイル形式を変更します。|AEFF|SERVER|CONTROL SERVER|  
|DATABASE|ALTER ANY FULLTEXT CATALOG|ALFT|SERVER|CONTROL SERVER|  
|DATABASE|任意のマスクを変更します。|AAMK<br /><br /> 適用対象 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] から現在のバージョンまで)、 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。|SERVER|CONTROL SERVER|  
|DATABASE|ALTER ANY MESSAGE TYPE|ALMT|SERVER|CONTROL SERVER|  
|DATABASE|ALTER ANY REMOTE SERVICE BINDING|ALSB|SERVER|CONTROL SERVER|  
|DATABASE|ALTER ANY ROLE|ALRL|SERVER|CONTROL SERVER|  
|DATABASE|ALTER ANY ROUTE|ALRT|SERVER|CONTROL SERVER|  
|DATABASE|ALTER ANY SCHEMA|ALSM|SERVER|CONTROL SERVER|  
|DATABASE|すべてのセキュリティ ポリシーを変更します。|ALSP<br /><br /> 適用対象 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] から現在のバージョンまで)、 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。|SERVER|CONTROL SERVER|  
|DATABASE|ALTER ANY SENSITIVITY CLASSIFICATION|ALSP<br />適用対象 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (SQL Server 2019 (15.x) から現在のバージョンまで)、[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。|DATABASE|CONTROL SERVER|
|DATABASE|ALTER ANY SERVICE|ALSV|SERVER|CONTROL SERVER|  
|DATABASE|ALTER ANY SYMMETRIC KEY|ALSK|SERVER|CONTROL SERVER|  
|DATABASE|ALTER ANY USER|ALUS|SERVER|CONTROL SERVER|  
|DATABASE|AUTHENTICATE|AUTH|SERVER|AUTHENTICATE SERVER|  
|DATABASE|BACKUP DATABASE|BADB|SERVER|CONTROL SERVER|  
|DATABASE|BACKUP LOG|BALO|SERVER|CONTROL SERVER|  
|DATABASE|CHECKPOINT|CP|SERVER|CONTROL SERVER|  
|DATABASE|CONNECT|CO|SERVER|CONTROL SERVER|  
|DATABASE|CONNECT REPLICATION|CORP|SERVER|CONTROL SERVER|  
|DATABASE|CONTROL|CL|SERVER|CONTROL SERVER|  
|DATABASE|CREATE AGGREGATE|CRAG|SERVER|CONTROL SERVER|  
|DATABASE|CREATE ASSEMBLY|CRAS|SERVER|CONTROL SERVER|  
|DATABASE|CREATE ASYMMETRIC KEY|CRAK|SERVER|CONTROL SERVER|  
|DATABASE|CREATE CERTIFICATE|CRCF|SERVER|CONTROL SERVER|  
|DATABASE|CREATE CONTRACT|CRSC|SERVER|CONTROL SERVER|  
|DATABASE|CREATE DATABASE|CRDB|SERVER|CREATE ANY DATABASE|  
|DATABASE|CREATE DATABASE DDL EVENT NOTIFICATION|CRED|SERVER|CREATE DDL EVENT NOTIFICATION|  
|DATABASE|CREATE DEFAULT|CRDF|SERVER|CONTROL SERVER|  
|DATABASE|CREATE FULLTEXT CATALOG|CRFT|SERVER|CONTROL SERVER|  
|DATABASE|CREATE FUNCTION|CRFN|SERVER|CONTROL SERVER|  
|DATABASE|CREATE MESSAGE TYPE|CRMT|SERVER|CONTROL SERVER|  
|DATABASE|CREATE PROCEDURE|CRPR|SERVER|CONTROL SERVER|  
|DATABASE|CREATE QUEUE|CRQU|SERVER|CONTROL SERVER|  
|DATABASE|CREATE REMOTE SERVICE BINDING|CRSB|SERVER|CONTROL SERVER|  
|DATABASE|CREATE ROLE|CRRL|SERVER|CONTROL SERVER|  
|DATABASE|CREATE ROUTE|CRRT|SERVER|CONTROL SERVER|  
|DATABASE|CREATE RULE|CRRU|SERVER|CONTROL SERVER|  
|DATABASE|CREATE SCHEMA|CRSM|SERVER|CONTROL SERVER|  
|DATABASE|CREATE SERVICE|CRSV|SERVER|CONTROL SERVER|  
|DATABASE|CREATE SYMMETRIC KEY|CRSK|SERVER|CONTROL SERVER|  
|DATABASE|CREATE SYNONYM|CRSN|SERVER|CONTROL SERVER|  
|DATABASE|CREATE TABLE|CRTB|SERVER|CONTROL SERVER|  
|DATABASE|CREATE TYPE|CRTY|SERVER|CONTROL SERVER|  
|DATABASE|CREATE VIEW|CRVW|SERVER|CONTROL SERVER|  
|DATABASE|CREATE XML SCHEMA COLLECTION|CRXS|SERVER|CONTROL SERVER|  
|DATABASE|DELETE|DL|SERVER|CONTROL SERVER|  
|DATABASE|EXECUTE|EX|SERVER|CONTROL SERVER|  
|DATABASE|EXECUTE ANY EXTERNAL SCRIPT|EAES<br /><br /> 適用対象 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] から現在のバージョンまで)。|SERVER|CONTROL SERVER|  
|DATABASE|INSERT|IN|SERVER|CONTROL SERVER|  
|DATABASE|KILL DATABASE CONNECTION|KIDC<br /><br /> [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]だけに適用されます。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]で ALTER ANY CONNECTION を使用する。|SERVER|ALTER ANY CONNECTION|  
|DATABASE|REFERENCES|RF|SERVER|CONTROL SERVER|  
|DATABASE|SELECT|SL|SERVER|CONTROL SERVER|  
|DATABASE|SHOWPLAN|SPLN|SERVER|ALTER TRACE|  
|DATABASE|SUBSCRIBE QUERY NOTIFICATIONS|SUQN|SERVER|CONTROL SERVER|  
|DATABASE|TAKE OWNERSHIP|TO|SERVER|CONTROL SERVER|  
|DATABASE|マスク解除します。|UMSK<br /><br /> 適用対象 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] から現在のバージョンまで)、 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。|SERVER|CONTROL SERVER|  
|DATABASE|UPDATE|UP|SERVER|CONTROL SERVER|  
|DATABASE|列の暗号化キーの定義を表示します。|VWCK<br /><br /> 適用対象 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] から現在のバージョンまで)、 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。|SERVER|VIEW SERVER STATE|  
|DATABASE|任意の列のマスター_キーの定義の表示|vWCM<br /><br /> 適用対象 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] から現在のバージョンまで)、 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。|SERVER|VIEW SERVER STATE|  
|DATABASE|VIEW DATABASE STATE|VWDS|SERVER|VIEW SERVER STATE|  
|DATABASE|VIEW DEFINITION|VW|SERVER|VIEW ANY DEFINITION|  
|DATABASE SCOPED CREDENTIAL|ALTER|AL|DATABASE|CONTROL|
|DATABASE SCOPED CREDENTIAL|CONTROL|CL|DATABASE|CONTROL|
|DATABASE SCOPED CREDENTIAL|REFERENCES|RF|DATABASE|REFERENCES|
|DATABASE SCOPED CREDENTIAL|TAKE OWNERSHIP|TO|DATABASE|CONTROL|
|DATABASE SCOPED CREDENTIAL|VIEW DEFINITION|VW|DATABASE|VIEW DEFINITION|
|ENDPOINT|ALTER|AL|SERVER|ALTER ANY ENDPOINT|  
|ENDPOINT|CONNECT|CO|SERVER|CONTROL SERVER|  
|ENDPOINT|CONTROL|CL|SERVER|CONTROL SERVER|  
|ENDPOINT|TAKE OWNERSHIP|TO|SERVER|CONTROL SERVER|  
|ENDPOINT|VIEW DEFINITION|VW|SERVER|VIEW ANY DEFINITION|  
|FULLTEXT CATALOG|ALTER|AL|DATABASE|ALTER ANY FULLTEXT CATALOG|  
|FULLTEXT CATALOG|CONTROL|CL|DATABASE|CONTROL|  
|FULLTEXT CATALOG|REFERENCES|RF|DATABASE|REFERENCES|  
|FULLTEXT CATALOG|TAKE OWNERSHIP|TO|DATABASE|CONTROL|  
|FULLTEXT CATALOG|VIEW DEFINITION|VW|DATABASE|VIEW DEFINITION|  
|FULLTEXT STOPLIST|ALTER|AL|DATABASE|ALTER ANY FULLTEXT CATALOG|  
|FULLTEXT STOPLIST|CONTROL|CL|DATABASE|CONTROL|  
|FULLTEXT STOPLIST|REFERENCES|RF|DATABASE|REFERENCES|  
|FULLTEXT STOPLIST|TAKE OWNERSHIP|TO|DATABASE|CONTROL|  
|FULLTEXT STOPLIST|VIEW DEFINITION|VW|DATABASE|VIEW DEFINITION|  
|Login|ALTER|AL|SERVER|ALTER ANY LOGIN|  
|Login|CONTROL|CL|SERVER|CONTROL SERVER|  
|Login|IMPERSONATE|IM|SERVER|CONTROL SERVER|  
|Login|VIEW DEFINITION|VW|SERVER|VIEW ANY DEFINITION|  
|MESSAGE TYPE|ALTER|AL|DATABASE|ALTER ANY MESSAGE TYPE|  
|MESSAGE TYPE|CONTROL|CL|DATABASE|CONTROL|  
|MESSAGE TYPE|REFERENCES|RF|DATABASE|REFERENCES|  
|MESSAGE TYPE|TAKE OWNERSHIP|TO|DATABASE|CONTROL|  
|MESSAGE TYPE|VIEW DEFINITION|VW|DATABASE|VIEW DEFINITION|  
|OBJECT|ALTER|AL|SCHEMA|ALTER|  
|OBJECT|CONTROL|CL|SCHEMA|CONTROL|  
|OBJECT|DELETE|DL|SCHEMA|DELETE|  
|OBJECT|EXECUTE|EX|SCHEMA|EXECUTE|  
|OBJECT|INSERT|IN|SCHEMA|INSERT|  
|OBJECT|RECEIVE|RC|SCHEMA|CONTROL|  
|OBJECT|REFERENCES|RF|SCHEMA|REFERENCES|  
|OBJECT|SELECT|SL|SCHEMA|SELECT|  
|OBJECT|TAKE OWNERSHIP|TO|SCHEMA|CONTROL|  
|OBJECT|UPDATE|UP|SCHEMA|UPDATE|  
|OBJECT|VIEW CHANGE TRACKING|VWCT|SCHEMA|VIEW CHANGE TRACKING|  
|OBJECT|VIEW DEFINITION|VW|SCHEMA|VIEW DEFINITION|  
|REMOTE SERVICE BINDING|ALTER|AL|DATABASE|ALTER ANY REMOTE SERVICE BINDING|  
|REMOTE SERVICE BINDING|CONTROL|CL|DATABASE|CONTROL|  
|REMOTE SERVICE BINDING|TAKE OWNERSHIP|TO|DATABASE|CONTROL|  
|REMOTE SERVICE BINDING|VIEW DEFINITION|VW|DATABASE|VIEW DEFINITION|  
|ROLE|ALTER|AL|DATABASE|ALTER ANY ROLE|  
|ROLE|CONTROL|CL|DATABASE|CONTROL|  
|ROLE|TAKE OWNERSHIP|TO|DATABASE|CONTROL|  
|ROLE|VIEW DEFINITION|VW|DATABASE|VIEW DEFINITION|  
|ROUTE|ALTER|AL|DATABASE|ALTER ANY ROUTE|  
|ROUTE|CONTROL|CL|DATABASE|CONTROL|  
|ROUTE|TAKE OWNERSHIP|TO|DATABASE|CONTROL|  
|ROUTE|VIEW DEFINITION|VW|DATABASE|VIEW DEFINITION|  
|SEARCH PROPERTY LIST|ALTER|AL|SERVER|ALTER ANY FULLTEXT CATALOG|  
|SEARCH PROPERTY LIST|CONTROL|CL|SERVER|CONTROL|  
|SEARCH PROPERTY LIST|REFERENCES|RF|SERVER|REFERENCES|  
|SEARCH PROPERTY LIST|TAKE OWNERSHIP|TO|SERVER|CONTROL|  
|SEARCH PROPERTY LIST|VIEW DEFINITION|VW|SERVER|VIEW DEFINITION|  
|SCHEMA|ALTER|AL|DATABASE|ALTER ANY SCHEMA|  
|SCHEMA|CONTROL|CL|DATABASE|CONTROL|  
|SCHEMA|CREATE SEQUENCE|CRSO|DATABASE|CONTROL|  
|SCHEMA|DELETE|DL|DATABASE|DELETE|  
|SCHEMA|EXECUTE|EX|DATABASE|EXECUTE|  
|SCHEMA|INSERT|IN|DATABASE|INSERT|  
|SCHEMA|REFERENCES|RF|DATABASE|REFERENCES|  
|SCHEMA|SELECT|SL|DATABASE|SELECT|  
|SCHEMA|TAKE OWNERSHIP|TO|DATABASE|CONTROL|  
|SCHEMA|UPDATE|UP|DATABASE|UPDATE|  
|SCHEMA|VIEW CHANGE TRACKING|VWCT|DATABASE|VIEW CHANGE TRACKING|  
|SCHEMA|VIEW DEFINITION|VW|DATABASE|VIEW DEFINITION|  
|SERVER|ADMINISTER BULK OPERATIONS|ADBO|適用なし|適用なし|  
|SERVER|ALTER ANY AVAILABILITY GROUP|ALAG|適用なし|適用なし|  
|SERVER|ALTER ANY CONNECTION|ALCO|適用なし|適用なし|  
|SERVER|ALTER ANY CREDENTIAL|ALCD|適用なし|適用なし|  
|SERVER|ALTER ANY DATABASE|ALDB|適用なし|適用なし|  
|SERVER|ALTER ANY ENDPOINT|ALHE|適用なし|適用なし|  
|SERVER|ALTER ANY EVENT NOTIFICATION|ALES|適用なし|適用なし|  
|SERVER|ALTER ANY EVENT SESSION|AAES|適用なし|適用なし|  
|SERVER|ALTER ANY LINKED SERVER|ALLS|適用なし|適用なし|  
|SERVER|ALTER ANY LOGIN|ALLG|適用なし|適用なし|  
|SERVER|ALTER ANY SERVER AUDIT|ALAA|適用なし|適用なし|  
|SERVER|ALTER ANY SERVER ROLE|ALSR|適用なし|適用なし|  
|SERVER|ALTER RESOURCES|ALRS|適用なし|適用なし|  
|SERVER|ALTER SERVER STATE|ALSS|適用なし|適用なし|  
|SERVER|ALTER SETTINGS|ALST|適用なし|適用なし|  
|SERVER|ALTER TRACE|ALTR|適用なし|適用なし|  
|SERVER|AUTHENTICATE SERVER|AUTH|適用なし|適用なし|  
|SERVER|CONNECT ANY DATABASE|CADB|適用なし|適用なし|  
|SERVER|CONNECT SQL|COSQ|適用なし|適用なし|  
|SERVER|CONTROL SERVER|CL|適用なし|適用なし|  
|SERVER|CREATE ANY DATABASE|CRDB|適用なし|適用なし|  
|SERVER|CREATE AVAILABILITY GROUP|CRAC|適用なし|適用なし|  
|SERVER|CREATE DDL EVENT NOTIFICATION|CRDE|適用なし|適用なし|  
|SERVER|CREATE ENDPOINT|CRHE|適用なし|適用なし|  
|SERVER|CREATE SERVER ROLE|CRSR|適用なし|適用なし|  
|SERVER|CREATE TRACE EVENT NOTIFICATION|CRTE|適用なし|適用なし|  
|SERVER|EXTERNAL ACCESS ASSEMBLY|XA|適用なし|適用なし|  
|SERVER|IMPERSONATE ANY LOGIN|IAL|適用なし|適用なし|  
|SERVER|SELECT ALL USER SECURABLES|SUS|適用なし|適用なし|  
|SERVER|SHUTDOWN|SHDN|適用なし|適用なし|  
|SERVER|UNSAFE ASSEMBLY|XU|適用なし|適用なし|  
|SERVER|VIEW ANY DATABASE|VWDB|適用なし|適用なし|  
|SERVER|VIEW ANY DEFINITION|VWAD|適用なし|適用なし|  
|SERVER|VIEW SERVER STATE|VWSS|適用なし|適用なし|  
|SERVER ROLE|ALTER|AL|SERVER|ALTER ANY SERVER ROLE|  
|SERVER ROLE|CONTROL|CL|SERVER|CONTROL SERVER|  
|SERVER ROLE|TAKE OWNERSHIP|TO|SERVER|CONTROL SERVER|  
|SERVER ROLE|VIEW DEFINITION|VW|SERVER|VIEW ANY DEFINITION|  
|SERVICE|ALTER|AL|DATABASE|ALTER ANY SERVICE|  
|SERVICE|CONTROL|CL|DATABASE|CONTROL|  
|SERVICE|SEND|SN|DATABASE|CONTROL|  
|SERVICE|TAKE OWNERSHIP|TO|DATABASE|CONTROL|  
|SERVICE|VIEW DEFINITION|VW|DATABASE|VIEW DEFINITION|  
|SYMMETRIC KEY|ALTER|AL|DATABASE|ALTER ANY SYMMETRIC KEY|  
|SYMMETRIC KEY|CONTROL|CL|DATABASE|CONTROL|  
|SYMMETRIC KEY|REFERENCES|RF|DATABASE|REFERENCES|  
|SYMMETRIC KEY|TAKE OWNERSHIP|TO|DATABASE|CONTROL|  
|SYMMETRIC KEY|VIEW DEFINITION|VW|DATABASE|VIEW DEFINITION|  
|TYPE|CONTROL|CL|SCHEMA|CONTROL|  
|TYPE|EXECUTE|EX|SCHEMA|EXECUTE|  
|TYPE|REFERENCES|RF|SCHEMA|REFERENCES|  
|TYPE|TAKE OWNERSHIP|TO|SCHEMA|CONTROL|  
|TYPE|VIEW DEFINITION|VW|SCHEMA|VIEW DEFINITION|  
|User|ALTER|AL|DATABASE|ALTER ANY USER|  
|User|CONTROL|CL|DATABASE|CONTROL|  
|User|IMPERSONATE|IM|DATABASE|CONTROL|  
|User|VIEW DEFINITION|VW|DATABASE|VIEW DEFINITION|  
|XML SCHEMA COLLECTION|ALTER|AL|SCHEMA|ALTER|  
|XML SCHEMA COLLECTION|CONTROL|CL|SCHEMA|CONTROL|  
|XML SCHEMA COLLECTION|EXECUTE|EX|SCHEMA|EXECUTE|  
|XML SCHEMA COLLECTION|REFERENCES|RF|SCHEMA|REFERENCES|  
|XML SCHEMA COLLECTION|TAKE OWNERSHIP|TO|SCHEMA|CONTROL|  
|XML SCHEMA COLLECTION|VIEW DEFINITION|VW|SCHEMA|VIEW DEFINITION|  
  
##  <a name="summary-of-the-permission-check-algorithm"></a><a name="_algorithm"></a> 権限チェック アルゴリズムの概要  
 権限のチェックは複雑な場合があります。 権限チェック アルゴリズムには、グループ メンバーシップの重複、所有権の継承、明示的および暗黙的な権限が含まれます。また、セキュリティ保護可能なエンティティを含むセキュリティ保護可能なクラスに対する権限の影響を受けることもあります。 アルゴリズムの一般的な手順では、関連する権限がすべて収集されます。 ブロックする DENY が見つからない場合、十分なアクセス権を付与する GRANT が検索されます。 アルゴリズムには、不可欠な要素が 3 つあります。 **セキュリティ コンテキスト**、 **権限領域**、および **必要な権限** です。  
  
> [!NOTE]  
>  sa、dbo、エンティティ所有者、information_schema、sys、または自分自身に対する権限を許可、拒否、または取り消すことはできません。  
  
-   **セキュリティ コンテキスト**  
  
     これは、アクセス チェックに対して権限を与えるプリンシパルのグループです。 EXECUTE AS ステートメントを使用してセキュリティ コンテキストが別のログインまたはユーザーに変更されていない限り、現在のログインまたはユーザーに関連した権限です。 セキュリティ コンテキストには次のプリンシパルが含まれます。  
  
    -   ログイン  
  
    -   ユーザー  
  
    -   ロールのメンバーシップ  
  
    -   Windows グループのメンバーシップ  
  
    -   モジュール署名が使用されている場合、ユーザーが現在実行しているモジュールの署名に使用された証明書のログインまたはユーザー アカウント、およびそのプリンシパルに関連付けられたロールのメンバーシップ  
  
-   **権限領域**  
  
     これは、セキュリティ保護可能なエンティティと、それを含むすべてのセキュリティ保護可能なクラスです。 たとえば、あるテーブル (セキュリティ保護可能なエンティティ) が、セキュリティ保護可能なクラスであるスキーマとデータベースに含まれているとします。 この場合のアクセスは、テーブル、スキーマ、データベース、サーバーの各レベルの権限による影響を受けます。 詳細については、「[権限の階層 &#40;データベース エンジン&#41;](../../relational-databases/security/permissions-hierarchy-database-engine.md)」を参照してください。  
  
-   **必要な権限**  
  
     必要とされる権限の種類です。 INSERT、UPDATE、DELETE、SELECT、EXECUTE、ALTER、CONTROL などがあります。  
  
     次の例のように、アクセスに複数の権限が必要な場合もあります。  
  
    -   ストアド プロシージャでは、ストアド プロシージャ自体に対する EXECUTE 権限に加えて、ストアド プロシージャによって参照されている複数のテーブルに対する INSERT 権限が必要な場合があります。  
  
    -   動的管理ビューでは、ビューに対する VIEW SERVER STATE 権限と SELECT 権限の両方が必要な場合があります。  
  
### <a name="general-steps-of-the-algorithm"></a>アルゴリズムの一般的な手順  
 セキュリティ保護可能なリソースに対するアクセスを許可するかどうかを判断するためにアルゴリズムが実際に使用する手順は、関連するプリンシパルとセキュリティ保護可能なリソースによって異なる場合があります。 ただし、アルゴリズムは一般に次の手順を実行します。  
  
1.  ログインが sysadmin 固定サーバー ロールのメンバーであるか、ユーザーが現在のデータベースの dbo ユーザーである場合は、権限チェックを行いません。  
  
2.  所有権の継承が適用され、その継承内でオブジェクトに対するアクセス チェックが以前にセキュリティ チェックに合格している場合は、アクセスを許可します。  
  
3.  呼び出し元に関連付けられたサーバーレベル、データベースレベル、署名付きモジュールの各 ID を集計して、 **セキュリティ コンテキスト** を作成します。  
  
4.  その **セキュリティ コンテキスト** 用に、 **権限領域** に対して許可または拒否された権限をすべて収集します。 権限は、GRANT、GRANT WITH GRANT、または DENY として明示的に指定される場合と、暗黙権限または包含権限の GRANT または DENY である場合があります。 たとえば、スキーマに対する CONTROL 権限を使用した場合、テーブルに対する CONTROL 権限も暗黙的に適用されます。 また、テーブルに対して CONTROL 権限を使用した場合、SELECT 権限も暗黙的に適用されます。 したがって、スキーマに対する CONTROL 権限が許可された場合、テーブルに対する SELECT 権限も許可されます。 テーブルに対する CONTROL 権限が拒否された場合、テーブルに対する SELECT 権限も拒否されます。  
  
    > [!NOTE]  
    >  列レベルの権限の GRANT により、オブジェクト レベルの DENY がオーバーライドされます。  
  
5.  **必要な権限** を識別します。  
  
6.  **権限領域** 内のオブジェクトについて、 **必要な権限** が、 **セキュリティ コンテキスト** の任意の ID に対し直接または暗黙的に拒否されている場合は、権限チェックが不合格となります。  
  
7.  **権限領域** 内のすべてのオブジェクトについて、 **必要な権限** が、 **セキュリティ コンテキスト** のいずれの ID に対しても直接または暗黙的に拒否されておらず、 **必要な権限** に GRANT 権限または GRANT WITH GRANT 権限が含まれている場合は、権限チェックが合格となります。  

## <a name="special-considerations-for-column-level-permissions"></a>列レベルのアクセス許可に関する特別な考慮事項

列レベルの権限は構文 *<table_name>(\<column _name>)* を使用して許可されます。 次に例を示します。
```sql
GRANT SELECT ON OBJECT::Customer(CustomerName) TO UserJoe;
```
テーブルの DENY は、列の GRANT によりオーバーライドされます。 ただし、その後にテーブルの DENY があると、列の GRANT は削除されます。 
  
##  <a name="examples"></a><a name="_examples"></a> 使用例  
 このセクションでは、権限に関する情報を取得する例を示します。  
  
### <a name="a-returning-the-complete-list-of-grantable-permissions"></a>A. 許可できる権限の完全な一覧を返す  
 次のステートメントでは、 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 関数によって、すべての `fn_builtin_permissions` 権限が返されます。 詳細については、「[sys.fn_builtin_permissions &#40;Transact-SQL&#41;](../../relational-databases/system-functions/sys-fn-builtin-permissions-transact-sql.md)」を参照してください。  
  
```sql  
SELECT * FROM fn_builtin_permissions(default);  
GO  
```  
  
### <a name="b-returning-the-permissions-on-a-particular-class-of-objects"></a>B. オブジェクトの特定のクラスに対する権限を返す  
 次の例では、 `fn_builtin_permissions` を使用してセキュリティ保護可能なカテゴリに使用できるすべての権限を表示します。 この例では、アセンブリに対する権限を返します。  
  
```sql  
SELECT * FROM fn_builtin_permissions('assembly');  
GO    
```  
  
### <a name="c-returning-the-permissions-granted-to-the-executing-principal-on-an-object"></a>C. オブジェクトに対する実行中のプリンシパルに許可された権限を返す  
 次の例では、 `fn_my_permissions` を使用して、指定したセキュリティ保護可能なリソースについて、呼び出し元のプリンシパルが保持している有効な権限の一覧を返します。 この例では、`Orders55` という名前のオブジェクトに対する権限を返します。 詳細については、「[sys.fn_my_permissions &#40;Transact-SQL&#41;](../../relational-databases/system-functions/sys-fn-my-permissions-transact-sql.md)」を参照してください。  
  
```sql  
SELECT * FROM fn_my_permissions('Orders55', 'object');  
GO  
```  
  
### <a name="d-returning-the-permissions-applicable-to-a-specified-object"></a>D. 指定したオブジェクトに適用できる権限を返す  
 次の例は、 `Yttrium`と呼ばれるオブジェクトに適用できる権限を返します。 オブジェクト `OBJECT_ID` の ID を取得するために、組み込み関数 `Yttrium`が使用されていることに注意してください。  
  
```sql  
SELECT * FROM sys.database_permissions   
    WHERE major_id = OBJECT_ID('Yttrium');  
GO  
```  
  
## <a name="see-also"></a>参照  
 [権限の階層 &#40;データベース エンジン&#41;](../../relational-databases/security/permissions-hierarchy-database-engine.md)   
 [sys.database_permissions &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-database-permissions-transact-sql.md)  
  
  
