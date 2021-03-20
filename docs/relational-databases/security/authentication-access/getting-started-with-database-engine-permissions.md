---
title: データベース エンジンの権限の概要 | Microsoft Docs
description: SQL Server でのいくつかの基本的なセキュリティ概念を確認し、データベース エンジンのアクセス許可の一般的な実装について説明します。
ms.custom: ''
ms.date: 01/03/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: security
ms.topic: quickstart
helpviewer_keywords:
- permissions [SQL Server], getting started
ms.assetid: 051af34e-bb5b-403e-bd33-007dc02eef7b
author: VanMSFT
ms.author: vanto
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 51e1253eb44d49285a30e8a0ec77ddf6caae7cba
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104750432"
---
# <a name="getting-started-with-database-engine-permissions"></a>データベース エンジンの権限の概要
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  [!INCLUDE[ssDE](../../../includes/ssde-md.md)] の権限は、サーバー レベルではログインとサーバー ロール、データベース レベルではデータベース ユーザーとデータベース ロールを通じて管理されます。 [!INCLUDE[ssSDS](../../../includes/sssds-md.md)] のモデルでは各データベース内に同じシステムを公開していますが、サーバー レベルの権限を使用できません。 このトピックでは、セキュリティの基本的な概念についていくつか確認し、権限の一般的な実装について説明します。  
  
## <a name="security-principals"></a>セキュリティ プリンシパル  
 セキュリティ プリンシパルとは、 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] を使用する ID の正式名であり、アクションを実行するように権限を割り当てることができます。 基本的にはユーザーまたはユーザーのグループですが、ユーザーとして扱われるエンティティでもかまいません。 セキュリティ プリンシパルは一覧の [!INCLUDE[tsql](../../../includes/tsql-md.md)] または [!INCLUDE[ssManStudioFull](../../../includes/ssmanstudiofull-md.md)]を使用して作成および管理できます。  
  
##### <a name="logins"></a>Login  
 ログインとは、 [!INCLUDE[ssDEnoversion](../../../includes/ssdenoversion-md.md)]にログオンするための個々のユーザー アカウントです。 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] と [!INCLUDE[ssSDS](../../../includes/sssds-md.md)] は Windows 認証に基づくログインと、 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 認証に基づくログインをサポートします。 2 種類のログインの詳細については、「 [Choose an Authentication Mode](../../../relational-databases/security/choose-an-authentication-mode.md)」を参照してください。  
  
##### <a name="fixed-server-roles"></a>固定サーバー ロール  
 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]では、固定サーバー ロールは構成済みの一連ロールで、サーバー レベルの権限の便利なグループを提供します。 ロールには `ALTER SERVER ROLE ... ADD MEMBER` ステートメントを使用してログインを追加できます。 詳細については、「[ALTER SERVER ROLE &#40;Transact-SQL&#41;](../../../t-sql/statements/alter-server-role-transact-sql.md)」を参照してください。 [!INCLUDE[ssSDS](../../../includes/sssds-md.md)] は固定サーバー ロールをサポートしていませんが、マスター データベースにサーバー ロールとして機能する 2 つのロール (`dbmanager` と `loginmanager`) があります。  
  
##### <a name="user-defined-server-roles"></a>ユーザー定義サーバー ロール  
 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]では、独自のサーバー ロールを作成して、サーバー レベルの権限を割り当てることができます。 サーバー ロールには `ALTER SERVER ROLE ... ADD MEMBER` ステートメントを使用してログインを追加できます。 詳細については、「[ALTER SERVER ROLE &#40;Transact-SQL&#41;](../../../t-sql/statements/alter-server-role-transact-sql.md)」を参照してください。 [!INCLUDE[ssSDS](../../../includes/sssds-md.md)] はユーザー定義サーバー ロールをサポートしていません。  
  
##### <a name="database-users"></a>データベース ユーザー  
 データベースにデータベース ユーザーを作成してそのデータベース ユーザーをログインにマッピングすることで、ログインにデータベースへのアクセスが付与されます。 通常、データベース ユーザー名はログイン名と同じですが、同じにする必要はありません。 各データベース ユーザーは、単一のログインにマッピングされます。 ログインはデータベース内の 1 つのユーザーにのみマッピングできますが、異なる複数のデータベースにデータベース ユーザーとしてマッピングできます。  
  
 対応するログインがないデータベース ユーザーも作成できます。 これらは *包含データベース ユーザー* と呼ばれます。 [!INCLUDE[msCoName](../../../includes/msconame-md.md)] では包含データベース ユーザーの使用をお勧めしています。 ログインと同様に、包含データベース ユーザーは Windows 認証または [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 認証のいずれかを使用できます。 詳細については、「 [包含データベース ユーザー - データベースの可搬性を確保する](../../../relational-databases/security/contained-database-users-making-your-database-portable.md)」を参照してください。  
  
 12 種類のユーザーはそれぞれ認証方法がわずかに異なり、それぞれ何を代表するかも異なります。 ユーザーの一覧は「[CREATE USER &#40;Transact-SQL&#41;](../../../t-sql/statements/create-user-transact-sql.md)」で確認してください。  
  
##### <a name="fixed-database-roles"></a>固定データベース ロール  
 固定データベース ロールは構成済みの一連のロールで、データベース レベルの権限の便利なグループを提供します。 固定データベース ロールには、`ALTER ROLE ... ADD MEMBER` ステートメントを使用してデータベース ユーザーとユーザー定義データベース ロールを追加できます。 詳細については、「[ALTER ROLE &#40;Transact-SQL&#41;](../../../t-sql/statements/alter-role-transact-sql.md)」を参照してください。  
  
##### <a name="user-defined-database-roles"></a>ユーザー定義データベース ロール  
 `CREATE ROLE` の権限を持つユーザーは、一般的な権限を持つユーザーのグループを代表する、新しいユーザー定義データベース ロールを作成できます。 通常、権限の管理と監視を簡略化するために、権限はロール全体に対して付与または拒否されます。 データベース ロールには、 `ALTER ROLE ... ADD MEMBER` ステートメントを使用してデータベース ユーザーを追加できます。 詳細については、「[ALTER ROLE &#40;Transact-SQL&#41;](../../../t-sql/statements/alter-role-transact-sql.md)」を参照してください。  
  
##### <a name="other-principals"></a>その他のプリンシパル  
 ここで取り上げていないその他のセキュリティ ポリシーには、アプリケーション ロールのほか、証明書や非対称キーに基づくログインやユーザーなどがあります。  
  
 Windows ユーザー、Windows グループ、ログイン、データベース ユーザー間の関係を示す図については、「 [Create a Database User](../../../relational-databases/security/authentication-access/create-a-database-user.md)」を参照してください。  
  
## <a name="typical-scenario"></a>標準のシナリオ  
 次の例は、権限を構成する一般的な方法および推奨される方法を示します。  
  
#### <a name="in-active-directory-or-azure-active-directory"></a>Active Directory または Azure Active Directory の場合：  
  
1.  各ユーザーに Windows ユーザーを作成します。  
  
2.  作業単位と職務を表す Windows グループを作成します。  
  
3.  Windows ユーザーを Windows グループに追加します。  

#### <a name="if-the-person-connecting-will-be-connecting-to-many-databases"></a>接続するユーザーが多数のデータベースに接続する場合  
  
1.  Windows グループのログインを作成します。 ( [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 認証を使用する場合は、Active Directory の手順をスキップし、 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 認証のログインをここで作成します)。  
  
2.  ユーザー データベースで、Windows グループを表すログインのデータベース ユーザーを作成します。  
  
3.  ユーザー データベースで、それぞれ類似した職務を表すユーザー定義データベース ロールを 1 つ以上作成します。 たとえば、財務アナリストやセールス アナリストなどです。  
  
4.  データベース ユーザーを 1 つ以上のユーザー定義データベース ロールに追加します。  
  
5.  ユーザー定義データベース ロールに権限を付与します。  
  
#### <a name="if-the-person-connecting-will-be-connecting-to-only-one-database"></a>接続するユーザーが 1 つのデータベースにのみ接続する場合  
  
1.  ユーザー データベースで、Windows グループの包含データベース ユーザーを作成します。 ( [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 認証を使用する場合は、Active Directory の手順をスキップし、包含データベース ユーザーの [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 認証をここで作成します)。  
  
1.  ユーザー データベースで、それぞれ類似した職務を表すユーザー定義データベース ロールを 1 つ以上作成します。 たとえば、財務アナリストやセールス アナリストなどです。  
  
1.  データベース ユーザーを 1 つ以上のユーザー定義データベース ロールに追加します。  
  
1.  ユーザー定義データベース ロールに権限を付与します。  
  
 この時点での一般的な結果としては、Windows ユーザーは Windows グループのメンバーです。 Windows グループには、 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] または [!INCLUDE[ssSDS](../../../includes/sssds-md.md)]にログインがあります。 ログインは、ユーザー データベース内のユーザー ID にマップされます。 ユーザーはデータベース ロールのメンバーです。 次に、ロールに権限を追加する必要があります。  
  
## <a name="assigning-permissions"></a>権限の割り当て  
 権限のほとんどのステートメントには型が存在します。  
  
```  
AUTHORIZATION  PERMISSION  ON  SECURABLE::NAME  TO  PRINCIPAL;  
```  
  
-   `AUTHORIZATION` は、 `GRANT`型、 `REVOKE` 型、または `DENY`型のいずれかである必要があります。  
  
-   `PERMISSION` は許可または禁止されるアクションを確立します。 [!INCLUDE[sssql16-md](../../../includes/sssql16-md.md)] では、230 の権限を指定できます。 [!INCLUDE[ssSDS](../../../includes/sssds-md.md)] の権限の数が少なくなっています。 権限は、「[アクセス許可 &#40;データベース エンジン&#41;](../../../relational-databases/security/permissions-database-engine.md)」のトピックと以下のチャートで確認できます。  
  
-   `ON SECURABLE::NAME` は、セキュリティ保護可能な型 (サーバー、サーバー オブジェクト、データベース、データベース オブジェクト) とその名前です。 一部の権限は、不明瞭でありコンテキストで不適切であるため、 `ON SECURABLE::NAME` を必要としません。 たとえば、`CREATE TABLE` の権限は `ON SECURABLE::NAME` 句を必要としません。 (たとえば `GRANT CREATE TABLE TO Mary;` は Mary にテーブルの作成を許可します)。  
  
-   `PRINCIPAL` は権限を受け取るまたは失うセキュリティ プリンシパル (ログイン、ユーザー、ロール) です。 ロールに権限を付与できるタイミングで付与します。  
  
 次の例の GRANT ステートメントでは、 `UPDATE` スキーマに含まれている `Parts` テーブルまたはビュー上の `Production` 権限を `PartsTeam`という名前のロールに付与します。  
  
```  
GRANT UPDATE ON OBJECT::Production.Parts TO PartsTeam;  
```  
  
 権限をセキュリティ プリンシパル (ログイン、ユーザー、ロール) に付与するには、 `GRANT` ステートメント使用します。 権限を明示的に拒否するには、  `DENY` コマンドを使用します。 以前に付与または拒否された権限を削除するには、 `REVOKE` ステートメントを使用します。 権限は累積的であるため、ユーザーはユーザー、ログイン、すべてのグループ メンバーシップに付与された権限をすべて受け取ります。ただし、権限の拒否はすべての付与をオーバーライドします。  
  
> [!TIP]  
>  よくある間違いは、 `GRANT` の代わりに `DENY` を使用して `REVOKE`の削除を試行することです。 これにより、ユーザーが複数のソースから権限を受け取る際に問題が発生することがあります。 次の例は、このプリンシパルを示しています。  
  
 Sales グループは、 `SELECT` ステートメントを通じて OrderStatus テーブル上の `GRANT SELECT ON OBJECT::OrderStatus TO Sales;`権限を受け取ります。 ユーザー Ted は、Sales ロールのメンバーです。 `SELECT` ステートメントを通じて、Ted にも OrderStatus テーブルに対する  `GRANT SELECT ON OBJECT::OrderStatus TO Ted;`権限が自身の名前の下に付与されます。 管理者が Sales ロールに対する `GRANT` を削除するとします。  
  
-   管理者が `REVOKE SELECT ON OBJECT::OrderStatus TO Sales;`を正しく実行すると、Ted には個別の `SELECT` ステートメントを通じて OrderStatus テーブルに対する `GRANT` アクセスが保持されます。  
  
-   管理者が `DENY SELECT ON OBJECT::OrderStatus TO Sales;` を間違った方法で実行すると、Sales に対する `SELECT` によって Ted 個人の `DENY` がオーバーライドされるため、Sales ロールのメンバーである Ted の  `GRANT`権限は拒否されます  
  
> [!NOTE]  
>  権限は [!INCLUDE[ssManStudio](../../../includes/ssmanstudio-md.md)]を使用して構成できます。 オブジェクト エクスプローラーでセキュリティ保護可能なリソースを探し、セキュリティ保護可能なリソース名を右クリックして、 **[プロパティ]** をクリックします。 **[権限]** ページを選択します。 権限ページの使用に関するヘルプについては、「 [Permissions or Securables Page](../../../relational-databases/security/permissions-or-securables-page.md)」を参照してください。  
  
## <a name="permission-hierarchy"></a>権限の階層  
 権限には、親子階層があります。 つまり、データベースに `SELECT` 権限を付与すると、その権限にデータベース内のすべての (子) スキーマの `SELECT` 権限が含まれます。 スキーマに `SELECT` 権限を付与すると、その権限にスキーマ内のすべての (子) テーブルとビューの `SELECT` 権限が含まれます。 権限は推移的です。つまり、 `SELECT` 権限をデータベースに付与すると、その権限にすべての (子) スキーマとすべての (孫) テーブルとビューの `SELECT` 権限が含まれます。  
  
 権限には、包含権限もあります。 オブジェクトに対する `CONTROL` 権限は通常、そのオブジェクトに対するその他すべての権限を提供します。  
  
 同じ権限に親子階層と包含階層の両方が存在することがあるため、権限のシステムは複雑になります。 たとえば、データベース (SalesDB) 内のスキーマ (Customers) にあるテーブル (Region) を見てみましょう。  
  
-   `CONTROL` 権限には、テーブル Region に対するその他のすべての権限 ( `ALTER`、 `SELECT`、 `INSERT`、  `UPDATE`、 `DELETE`、 and some other permissions.  
  
-   `SELECT` には、Region テーブルに対する `SELECT` 権限が含まれます。  
  
 したがって、Region テーブルに対する `SELECT` 権限は、次の 6 つのステートメントを通じて取得できます。  
  
```  
GRANT SELECT ON OBJECT::Region TO Ted;   
  
GRANT CONTROL ON OBJECT::Region TO Ted;   
  
GRANT SELECT ON SCHEMA::Customers TO Ted;   
  
GRANT CONTROL ON SCHEMA::Customers TO Ted;   
  
GRANT SELECT ON DATABASE::SalesDB TO Ted;   
  
GRANT CONTROL ON DATABASE::SalesDB TO Ted;  
```  
  
## <a name="grant-the-least-permission"></a>最小限の権限の付与  
 上に示した最初の権限 (`GRANT SELECT ON OBJECT::Region TO Ted;`) が最も詳細であり、 `SELECT`を付与する最小のステートメントです。 下位のオブジェクトに対する権限はありません。 可能な限り最小の権限を付与することをお勧めしますが、付与システムの簡略化のためには高いレベルで付与するほうが適切です。 このため、Ted がスキーマ全体への権限を必要とする場合、 `SELECT` をテーブルまたはビュー レベルで複数回付与するのではなく、 `SELECT` をスキーマ レベルで 1 回付与します。 データベースの設計は、戦略の成功に大きく関わってきます。 一意の権限を必要とするオブジェクトが単一のスキーマに含まれるようにデータベースを設計する際には、この戦略が最適です。  
  
## <a name="list-of-permissions"></a>権限の一覧  
 [!INCLUDE[sssql16-md](../../../includes/sssql16-md.md)] には 230 の権限があります。 [!INCLUDE[ssSQL14](../../../includes/sssql14-md.md)] には 219 の権限があります。 [!INCLUDE[ssSQL11](../../../includes/sssql11-md.md)] には 214 の権限があります。 [!INCLUDE[ssKilimanjaro](../../../includes/sskilimanjaro-md.md)] には 195 の権限があります。 [!INCLUDE[ssSDS](../../../includes/sssds-md.md)]、 [!INCLUDE[ssDW](../../../includes/ssdw-md.md)]、 [!INCLUDE[ssAPS](../../../includes/ssaps-md.md)] にはそれぞれ、 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]に適用されない権限が含まれているものの、データベース エンジンの一部のみを公開しているため、権限の数が少なくなっています。 
 
 [!INCLUDE[database-engine-permissions](../../../includes/paragraph-content/database-engine-permissions.md)]
 
 [!INCLUDE[ssDE](../../../includes/ssde-md.md)] プリンシパルとサーバーおよびデータベース オブジェクト間の関係を示す図については、「[権限の階層 &#40;データベース エンジン&#41;](../../../relational-databases/security/permissions-hierarchy-database-engine.md)」をご覧ください。  
  
## <a name="permissions-vs-fixed-server-and-fixed-database-roles"></a>権限対固定サーバーと固定データベース ロール  
 固定サーバー ロールおよび固定データベース ロールの権限は似ていますが、詳細な権限がまったく同じということではありません。 たとえば、 `sysadmin` 固定サーバー ロールのメンバーには [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]のインスタンスのすべての権限があり、 `CONTROL SERVER` 権限のログインも同様です。 ただし、`CONTROL SERVER` アクセス許可を付与してもログインは sysadmin 固定サーバー ロールのメンバーにはならず、`sysadmin` 固定サーバー ロールにログインを追加しても、ログインに `CONTROL SERVER` アクセス許可は明示的には付与されません。 ストアド プロシージャは、詳細な権限は確認せずに固定ロールを確認することがあります。 たとえば、データベースをデタッチするには、 `db_owner` 固定データベース ロールのメンバーシップが必要です。 同じ `CONTROL DATABASE` 権限では不十分です。 これらの 2 つのシステムは並行して運用されますが、互いに干渉することはほとんどありません。 マイクロソフトでは、可能な限り固定ロールではなくより新しい詳細な権限システムを使用することをお勧めしています。
  
## <a name="monitoring-permissions"></a>権限の監視  
 次のビューは、セキュリティ情報を返します。  
  
-   サーバー上のログインとユーザー定義サーバー ロールは、 `sys.server_principals` ビューを使用して調べることができます。 このビューは、 [!INCLUDE[ssSDS](../../../includes/sssds-md.md)]では使用できません。  
  
-   データベース上のユーザーとユーザー定義ロールは、 `sys.database_principals` ビューを使用して調べることができます。  
  
-   ログインやユーザー定義固定サーバー ロールに付与された権限は、 `sys.server_permissions` ビューを使用して調べることができます。 このビューは、 [!INCLUDE[ssSDS](../../../includes/sssds-md.md)]では使用できません。  
  
-   ユーザーやユーザー定義固定データベース ロールに付与された権限は、 `sys.database_permissions` ビューを使用して調べることができます。  
  
-   データベース ロールのメンバーシップは、 `sys. sys.database_role_members` ビューを使用して調べることができます。  
  
-   サーバー ロールのメンバーシップは、 `sys.server_role_members` ビューを使用して調べることができます。 このビューは、 [!INCLUDE[ssSDS](../../../includes/sssds-md.md)]では使用できません。  
  
-   追加のセキュリティ関連のビューについては、「 [セキュリティ カタログ ビュー &#40;Transact-SQL&#41;](../../../relational-databases/system-catalog-views/security-catalog-views-transact-sql.md) を使用して作成および管理できます。  
  
### <a name="useful-transact-sql-statements"></a>便利な Transact-SQL ステートメント  
 次のステートメントでは、権限に関する有用な情報を返します。  
  
 データベースで明示的に許可または拒否された権限を返す ([!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] と [!INCLUDE[ssSDS](../../../includes/sssds-md.md)]) には、データベースで次のステートメントを実行します。  
  
```sql  
SELECT   
    perms.state_desc AS State,   
    permission_name AS [Permission],   
    obj.name AS [on Object],   
    dPrinc.name AS [to User Name]  
FROM sys.database_permissions AS perms  
JOIN sys.database_principals AS dPrinc  
    ON perms.grantee_principal_id = dPrinc.principal_id  
JOIN sys.objects AS obj  
    ON perms.major_id = obj.object_id;  
```  
  
 サーバー ロールのメンバーを返す ([!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] のみ) には、次のステートメントを実行します。  
  
```sql  
SELECT sRole.name AS [Server Role Name] , sPrinc.name AS [Members]  
FROM sys.server_role_members AS sRo  
JOIN sys.server_principals AS sPrinc  
    ON sRo.member_principal_id = sPrinc.principal_id  
JOIN sys.server_principals AS sRole  
    ON sRo.role_principal_id = sRole.principal_id;  
```  
  
 
 データベース ロールのメンバーを返す ([!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] と [!INCLUDE[ssSDS](../../../includes/sssds-md.md)]) には、データベースで次のステートメントを実行します。  
  
```sql  
SELECT dRole.name AS [Database Role Name], dPrinc.name AS [Members]  
FROM sys.database_role_members AS dRo  
JOIN sys.database_principals AS dPrinc  
    ON dRo.member_principal_id = dPrinc.principal_id  
JOIN sys.database_principals AS dRole  
    ON dRo.role_principal_id = dRole.principal_id;  
```  
  
## <a name="next-steps"></a>次の手順  
 開始に役立つトピックについては、次を参照してください。  
  
-   [チュートリアル:データベース エンジンの概要](../../../relational-databases/tutorial-getting-started-with-the-database-engine.md) 

-   [データベースの作成 (チュートリアル)](../../../t-sql/lesson-1-creating-database-objects.md)  
  
-   [チュートリアル:SQL Server Management Studio](../../../ssms/quickstarts/ssms-connect-query-sql-server.md)  
  
-   [チュートリアル:Transact-SQL ステートメントの作成](../../../t-sql/tutorial-writing-transact-sql-statements.md)  
  
## <a name="see-also"></a>参照  
 [SQL Server データベース エンジンと Azure SQL Database のセキュリティ センター](../../../relational-databases/security/security-center-for-sql-server-database-engine-and-azure-sql-database.md)   
 [セキュリティ関数 &#40;Transact-SQL&#41;](../../../t-sql/functions/security-functions-transact-sql.md)   
 [セキュリティ関連の動的管理ビューおよび関数 &#40;Transact-SQL&#41;](../../../relational-databases/system-dynamic-management-views/security-related-dynamic-management-views-and-functions-transact-sql.md)   
 [セキュリティ カタログ ビュー &#40;Transact-SQL&#41;](../../../relational-databases/system-catalog-views/security-catalog-views-transact-sql.md)   
 [sys.fn_builtin_permissions &#40;Transact-SQL&#41;](../../../relational-databases/system-functions/sys-fn-builtin-permissions-transact-sql.md)   
 [データベース エンジンの有効なアクセス許可の決定](../../../relational-databases/security/authentication-access/determining-effective-database-engine-permissions.md)
