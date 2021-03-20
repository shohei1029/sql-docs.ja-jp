---
title: データベース エンジンの有効なアクセス許可の決定 | Microsoft Docs
description: SQL Server データベース エンジンのさまざまなオブジェクトに対するアクセス許可を持つユーザーを決定する方法について説明します。これには、現在および以前のアクセス許可システムが含まれます。
ms.custom: ''
ms.date: 01/03/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: security
ms.topic: conceptual
helpviewer_keywords:
- permissions, effective
- effective permissions
ms.assetid: 273ea09d-60ee-47f5-8828-8bdc7a3c3529
author: VanMSFT
ms.author: vanto
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 61378302f1ce2b49487cabb63f9580cd82f51409
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104755672"
---
# <a name="determining-effective-database-engine-permissions"></a>データベース エンジンの有効なアクセス許可の決定
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

SQL Server データベース エンジンでは、さまざまなオブジェクトのアクセス許可が与えられます。この記事では、アクセス許可が与えられているユーザーを判断する方法について説明します。 SQL Server では、データベース エンジンのために 2 つのアクセス許可システムが実装されます。 固定ロールの古いほうのシステムでは、アクセス許可が事前構成されました。 SQL Server 2005 より、より柔軟で正確なシステムが利用できます。 (この記事の情報は 2005 以降の SQL Server に適用されます。 SQL Server のバージョンによっては、利用できない種類のアクセス許可があります。)

> [!IMPORTANT]
>  * 両方のアクセス許可システムの集合体が有効なアクセス許可となります。 
>  * アクセス許可の拒否はアクセス許可の承諾をオーバーライドします。 
>  * あるユーザーが sysadmin という固定のサーバー ロールに属している場合、アクセス許可はそれ以上確認されません。拒否は強制されません。 
>  * 古いシステムと新しいシステムには類似点があります。 たとえば、`sysadmin` 固定サーバー ロールのメンバーシップには、`CONTROL SERVER` と同様のアクセス許可が与えられます。 ただし、システムは同じではありません。 たとえば、あるログインに `CONTROL SERVER` アクセス許可のみが与えられ、ストアド プロシージャが `sysadmin` 固定サーバー ロールのメンバーシップを確認する場合、アクセス許可確認は失敗します。 この逆も当てはまります。 


## <a name="summary"></a>まとめ   
* サーバー レベルのアクセス許可は、固定サーバー ロールかユーザー定義サーバー ロールのメンバーシップから与えられます。 全員が `public` 固定サーバー ロールに属し、そこで割り当てられたアクセス許可を受け取ります。   
* サーバー レベルのアクセス許可は、ログインに与えられるアクセス許可かユーザー定義サーバー ロールから与えられます。   
* データベース レベルのアクセス許可は、固定データベース ロールのメンバーシップか各ロールベースのユーザー定義データベース ロールから与えられます。 全員が `public` 固定データベース ロールに属し、そこで割り当てられたアクセス許可を受け取ります。   
* データベース レベルのアクセス許可は、ユーザーに与えられるアクセス許可か各データベースのユーザー定義データベース ロールから与えられます。   
* アクセス許可は、`guest` ログインまたは `guest` データベース ユーザーが有効になっている場合、それから与えられます。 `guest` のログインとユーザーは既定では無効になっています。   
* Windows ユーザーは、ログインが与えられた Windows グループに所属できます。 Windows ユーザーが Windows トークンを Windows グループのセキュリティ ID と結び付けて提示すると、SQL Server は Windows グループのメンバーシップを認識します。 SQL Server は Windows グループ メンバーシップに関する自動更新を管理することも受信することもしないため、Windows ユーザーのアクセス許可が Windows グループ メンバーシップから与えられることを SQL Server が報告しても、その報告には信頼性がありません。   
* アクセス許可は、あるアプリケーション ロールに切り替え、パスワードを指定する方法で取得できます。   
* アクセス許可は、`EXECUTE AS` 句が含まれるストアド プロシージャを実行する方法で取得できます。   
* アクセス許可は、`IMPERSONATE` アクセス許可が与えられているログインまたはユーザーにより取得できます。   
* ローカル コンピューター管理者グループに属する場合、常に自分の特権を `sysadmin` に昇格できます。 (SQL データベースには適用されません。)  
* `securityadmin` 固定サーバー ロールに属する場合、自分のさまざまな特権を昇格できます。特権を `sysadmin` に昇格できる場合もあります。 (SQL データベースには適用されません。)   
* SQL Server 管理者は、すべてのログインとユーザーに関する情報を表示できます。 特権レベルの低いユーザーは、通常、自分の ID のみに関する情報を表示できます。

## <a name="older-fixed-role-permission-system"></a>古い固定ロール アクセス許可システム

固定サーバー ロールと固定データベース ロールでは、アクセス許可が事前構成されています。このアクセス許可は変更できません。 固定サーバー ロールのメンバーを判断するには、次のクエリを実行します。    
> [!NOTE]
>  サーバー レベルのアクセス許可が利用できない SQL Database または Azure Synapse Analytics には適用しないでください。 `sys.server_principals` の `is_fixed_role` 列が SQL Server 2012 に追加されました。 古いバージョンの SQL Server には必要ありません。  
> ```sql
> SELECT SP1.name AS ServerRoleName, 
>  isnull (SP2.name, 'No members') AS LoginName   
>  FROM sys.server_role_members AS SRM
>  RIGHT OUTER JOIN sys.server_principals AS SP1
>    ON SRM.role_principal_id = SP1.principal_id
>  LEFT OUTER JOIN sys.server_principals AS SP2
>    ON SRM.member_principal_id = SP2.principal_id
>  WHERE SP1.is_fixed_role = 1 -- Remove for SQL Server 2008
>  ORDER BY SP1.name;
> ```
> [!NOTE]
>  * すべてのログインは public ロールに属し、削除できません。 
>  * このクエリはマスター データベースのテーブルを確認しますが、オンプレミス製品のデータベースでは実行できません。 

固定データベース ロールのメンバーを判断するには、各データベースで次のクエリを実行します。
```sql
SELECT DP1.name AS DatabaseRoleName, 
   isnull (DP2.name, 'No members') AS DatabaseUserName 
 FROM sys.database_role_members AS DRM
 RIGHT OUTER JOIN sys.database_principals AS DP1
   ON DRM.role_principal_id = DP1.principal_id
 LEFT OUTER JOIN sys.database_principals AS DP2
   ON DRM.member_principal_id = DP2.principal_id
 WHERE DP1.is_fixed_role = 1
 ORDER BY DP1.name;
```
各ロールに与えられるアクセス許可を理解するには、オンライン ブックの図解のロール説明をご覧ください ([サーバー レベルのロール](../../../relational-databases/security/authentication-access/server-level-roles.md)と[データベース レベルのロール](../../../relational-databases/security/authentication-access/database-level-roles.md))。

## <a name="newer-granular-permission-system"></a>新しく詳細なアクセス許可システム

このシステムは柔軟であり、厳密に設定すると複雑になることがあります。 簡単にするには、ロールを作成し、ロールにアクセス許可を割り当て、ロールにグループを追加します。 データベース開発チームがスキーマで活動を降りし、個々のテーブルやプロシージャではなく、スキーマ全体にロール アクセス許可を与えると、さらに簡単になります。 現実のシナリオは複雑であり、ビジネス ニーズによって予想もしないようなセキュリティ要件が発生する可能性があります。   

[!INCLUDE[database-engine-permissions](../../../includes/paragraph-content/database-engine-permissions.md)]


### <a name="security-classes"></a>セキュリティ クラス

アクセス許可はサーバー レベル、データベース レベル、スキーマ レベル、オブジェクト レベルなどで与えることができます。26 のレベルがあります (クラスと呼ばれています)。 クラスをアルファベット順に並べると、`APPLICATION ROLE`、`ASSEMBLY`、`ASYMMETRIC KEY`、`AVAILABILITY GROUP`、`CERTIFICATE`、`CONTRACT`、`DATABASE`、`DATABASE` `SCOPED CREDENTIAL`、`ENDPOINT`、`FULLTEXT CATALOG`、`FULLTEXT STOPLIST`、`LOGIN`、`MESSAGE TYPE`、`OBJECT`、`REMOTE SERVICE BINDING`、`ROLE`、`ROUTE`、`SCHEMA`、`SEARCH PROPERTY LIST`、`SERVER`、`SERVER ROLE`、`SERVICE`、`SYMMETRIC KEY`、`TYPE`、`USER`、`XML SCHEMA COLLECTION` になります。 (SQL Server の種類によっては、一部のクラスが利用できません。)各クラスの完全情報を表示するには、個別のクエリが必要です。

### <a name="principals"></a>プリンシパル

アクセス許可はプリンシパルに与えられます。 プリンシパルには、サーバー ロール、ログイン、データベース ロール、ユーザーがあります。 ログインは、さまざまな Windows ユーザーを含む Windows グループを表すことができます。 Windows グループは SQL Server によって管理されていないため、SQL Server が Windows グループのメンバーを常に認識することはありません。 Windows ユーザーが SQL Server に接続すると、そのユーザーの Windows グループ メンバーシップ トークンがログイン パケットに含まれます。

Windows ユーザーが Windows グループに基づくログインを使用して接続すると、一部のアクティビティでは、個々の Windows ユーザーを表すログインまたはユーザーを作成することが SQL Server で必要になります。 たとえば、ある Windows グループ (エンジニア) にユーザー (Mary、Todd、Pat) が含まれ、このエンジニア グループにデータベース ユーザー アカウントが与えられています。 Mary にアクセス許可が与えられ、テーブルを作成すると、ユーザー (Mary) がテーブルの所有者として作成されることがあります。 あるいは、エンジニア グループの他のメンバーに与えられているアクセス許可が Todd に与えられない場合、アクセス許可を追跡記録するためにユーザー (Todd) を作成する必要があります。

Windows ユーザーは複数の Windows グループに属することがあります (エンジニア グループとマネージャー グループの両方など)。 エンジニア ログインに与えられた (または拒否された) アクセス許可、マネージャー ログインに与えられた (または拒否された) アクセス許可、個々のユーザーに与えられた (または拒否された) アクセス許可、ユーザーが属するロールに与えられた (または拒否された) アクセス許可をすべて集めた上で評価し、有効なアクセス許可が決定されます。 `HAS_PERMS_BY_NAME` 関数は、ユーザーまたはログインに特定のアクセス許可が与えられているかどうかを判断できます。 ただし、アクセス許可の承諾または拒否のソースを決定するこれといった方法はありません。 試行錯誤を重ねることも含め、アクセス許可の一覧を研究してください。

## <a name="useful-queries"></a>便利なクエリ

### <a name="server-permissions"></a>サーバーのアクセス許可

次のクエリは、サーバー レベルで承諾または拒否されているアクセス許可の一覧を返します。 このクエリはマスター データベースで実行してください。   
> [!NOTE]
>  サーバー レベルのアクセス許可を SQL Database または Azure Synapse Analytics で承諾または拒否することはできません。   
> ```sql
> SELECT pr.type_desc, pr.name, 
>  isnull (pe.state_desc, 'No permission statements') AS state_desc, 
>  isnull (pe.permission_name, 'No permission statements') AS permission_name 
>  FROM sys.server_principals AS pr
>  LEFT OUTER JOIN sys.server_permissions AS pe
>    ON pr.principal_id = pe.grantee_principal_id
>  WHERE is_fixed_role = 0 -- Remove for SQL Server 2008
>  ORDER BY pr.name, type_desc;
> ```

### <a name="database-permissions"></a>データベース権限

次のクエリは、データベース レベルで承諾または拒否されているアクセス許可の一覧を返します。 このクエリは各データベースで実行してください。   
```sql
SELECT pr.type_desc, pr.name, 
 isnull (pe.state_desc, 'No permission statements') AS state_desc, 
 isnull (pe.permission_name, 'No permission statements') AS permission_name 
FROM sys.database_principals AS pr
LEFT OUTER JOIN sys.database_permissions AS pe
    ON pr.principal_id = pe.grantee_principal_id
WHERE pr.is_fixed_role = 0 
ORDER BY pr.name, type_desc;
```

アクセス許可テーブルのアクセス許可の各クラスは、セキュリティ保護可能なリソースのそのクラスに関する関連情報を与える他のシステム ビューと結合できます。 たとえば、次のクエリでは、アクセス許可の影響を受けるデータベース オブジェクトの名前が与えられます。    
```sql
SELECT pr.type_desc, pr.name, pe.state_desc, 
 pe.permission_name, s.name + '.' + oj.name AS Object, major_id
 FROM sys.database_principals AS pr
 JOIN sys.database_permissions AS pe
   ON pr.principal_id = pe.grantee_principal_id
 JOIN sys.objects AS oj
   ON oj.object_id = pe.major_id
 JOIN sys.schemas AS s
   ON oj.schema_id = s.schema_id
 WHERE class_desc = 'OBJECT_OR_COLUMN';
```
`HAS_PERMS_BY_NAME` 関数を使用すると、特定のユーザー (この場合、`TestUser`) にアクセス許可が与えられているかどうかが判断されます。 次に例を示します。   
```sql
EXECUTE AS USER = 'TestUser';
SELECT HAS_PERMS_BY_NAME ('dbo.T1', 'OBJECT', 'SELECT');
REVERT;
```
構文の詳細については、「[HAS_PERMS_BY_NAME](../../../t-sql/functions/has-perms-by-name-transact-sql.md)」を参照してください。

## <a name="see-also"></a>参照:

[データベース エンジンの権限の概要](../../../relational-databases/security/authentication-access/getting-started-with-database-engine-permissions.md)    
[チュートリアル:データベース エンジンの概要](../../../relational-databases/tutorial-getting-started-with-the-database-engine.md) 

