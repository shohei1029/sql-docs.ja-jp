---
description: 権限:GRANT、DENY、REVOKE (Azure Synapse Analytics、Parallel Data Warehouse)
title: GRANT-DENY-REVOKE の権限
titleSuffix: Azure Synapse Analytics
ms.custom: seo-lt-2019
ms.date: 08/10/2017
ms.prod: sql
ms.prod_service: synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
dev_langs:
- TSQL
ms.assetid: 5a3b7424-408e-4cb0-8957-667ebf4596fc
author: VanMSFT
ms.author: vanto
monikerRange: '>= aps-pdw-2016 || = azure-sqldw-latest'
ms.openlocfilehash: fef6faff2e9e091836afbbe9e2b65f837851aad8
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104734064"
---
# <a name="permissions-grant-deny-revoke-azure-synapse-analytics-parallel-data-warehouse"></a>権限:GRANT、DENY、REVOKE (Azure Synapse Analytics、Parallel Data Warehouse)
[!INCLUDE[applies-to-version/asa-pdw](../../includes/applies-to-version/asa-pdw.md)]

  セキュリティ保護可能なリソース (データベース、テーブル、ビューなど) からセキュリティ プリンシパル (ログイン、データベース ユーザー、またはデータベース ロール) にアクセス許可 (**UPDATE** など) を付与または拒否するには、[!INCLUDE[ssSDW](../../includes/sssdw-md.md)] または [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]**GRANT** および **DENY** ステートメントを使用します。 アクセス許可を許可または拒否するには、**REVOKE** を使用します。  
  
 サーバー レベルのアクセス許可は、ログインに適用されます。 データベース レベルのアクセス許可は、データベースのユーザー ロールとデータベース ロールに適用されます。  
  
 どのようなアクセス許可が許可および拒否されているかを表示するには、sys.server_permissions および sys.database_permissions ビューのクエリを実行します。 セキュリティ プリンシパルに明示的に許可されていない、または拒否されているアクセス許可は、権限を設定したロールのメンバーシップを持つことにより、継承できます。 固定データベース ロールのアクセス許可は変更できず、sys.server_permissions および sys.database_permissions ビューは表示されません。  
  
-   **GRANT** は、明示的に 1 つまたは複数のアクセス許可を付与します。  
  
-   **DENY** は、1 つまたは複数の権限を持つプリンシパルを明示的に拒否します。  
  
-   **REVOKE** は、既存の **GRANT** または **DENY** アクセス許可を削除します。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則 (Transact-SQL)](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql
-- Azure Synapse Analytics and Parallel Data Warehouse  
GRANT   
    <permission> [ ,...n ]  
    [ ON [ <class_type> :: ] securable ]   
    TO principal [ ,...n ]  
    [ WITH GRANT OPTION ]  
[;]  
  
DENY   
    <permission> [ ,...n ]  
    [ ON [ <class_type> :: ] securable ]   
    TO principal [ ,...n ]  
    [ CASCADE ]  
[;]  
  
REVOKE   
    <permission> [ ,...n ]  
    [ ON [ <class_type> :: ] securable ]   
    [ FROM | TO ] principal [ ,...n ]  
    [ CASCADE ]  
[;]  
  
<permission> ::=  
{ see the tables below }  
  
<class_type> ::=  
{  
      LOGIN  
    | DATABASE  
    | OBJECT  
    | ROLE  
    | SCHEMA  
    | USER  
}  
```  
  
## <a name="arguments"></a>引数  
 \<permission>[ **,** ...*n* ]  
 1 つまたは複数のアクセス許可を付与、拒否、または取り消します。  
  
 ON [ \<class_type> :: ] *securable* **ON** 句は、アクセス許可を許可、拒否、または削除するセキュリティ保護可能なパラメーターを記述します。  
  
 \<class_type> セキュリティ保護可能なクラス型。 これは、**LOGIN**、**DATABASE**、**OBJECT**、**SCHEMA**、**ROLE**、または **USER** です。 **SERVER**_class\_type_ にアクエス許可を付与することもできますが、**SERVER** は、これらのアクセス許可に対して指定されません。 **DATABASE** は、アクセス許可に **DATABASE** という単語が含まれる場合 (たとえば **ALTER ANY DATABASE**) 指定されません。 *class_type* が指定されておらず、アクセス許可のタイプがサーバーまたはデータベースのクラスに制限されていない場合、クラスは **OBJECT** と見なされます。  
  
 *securable*  
 アクセス許可を付与、拒否、または取り消す、ログイン、データベース、テーブル、ビュー、スキーマ、プロシージャ、ロール、またはユーザーの名前。 「[Transact-SQL 構文表記規則 &#40;Transact-SQL&#41;](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)」で説明されている 3 部構成の名前付け規則を使用してオブジェクトの名前を指定することができます。  
  
 TO *principal* [ **,** ...*n* ]  
 アクセス許可を付与、拒否、または取り消す 1 つまたは複数のプリンシパル。 プリンシパルは、ログイン、データベース ユーザー、またはデータベース ロールの名前です。  
  
 FROM *principal* [ **,** ...*n* ]  
 アクセス許可を取り消す 1 つまたは複数のプリンシパル。  プリンシパルは、ログイン、データベース ユーザー、またはデータベース ロールの名前です。 **FROM** は、**REVOKE** ステートメントでのみ使用できます。 **TO** は、**GRANT**、**DENY**、または **REVOKE** で使用できます。  
  
 WITH GRANT OPTION  
 権限が許可された被付与者が、この権限を他のプリンシパルにも許可できることを示します。  
  
 CASCADE  
 指定したプリンシパル、およびこのプリンシパルによって権限が許可されている他のすべてのプリンシパルに対して、権限が拒否または取り消されていることを示します。 このオプションは、**GRANT OPTION** を使用してプリンシパルに権限が与えられている場合に必要となります。  
  
 GRANT OPTION FOR  
 指定した権限を与える許可を取り消すことを示します。 **CASCADE** 引数を使用する場合、この引数は必須です。  
  
> [!IMPORTANT]  
>  権限が **GRANT** オプションなしでプリンシパルに許可されている場合は、その権限自体が取り消されます。  
  
## <a name="permissions"></a>アクセス許可  
 アクセス許可を付与するには、権限の許可者が、**WITH GRANT OPTION** でアクセス許可自体を持っているか、アクセス許可が付与されたことを意味する上位の権限を持っている必要があります。  オブジェクトの所有者は、所有するオブジェクトの権限を許可できます。 セキュリティ保護可能なリソースに対して **CONTROL** 権限があるプリンシパルは、そのリソースの権限を許可できます。  **db_owner** および **db_securityadmin** 固定データベース ロールのメンバーは、データベース内のすべてのアクセス権を付与できます。  
  
## <a name="general-remarks"></a>全般的な解説  
 プリンシパルへのアクセス許可の拒否または取り消しは、承認されて現在実行中の要求には影響しません。 アクセスをすぐに制限するには、アクティブな要求をキャンセルするか、現在のセッションを終了させる必要があります。  
  
> [!NOTE]  
>  ほとんどの固定サーバー ロールは、このリリースでは使用できません。 ユーザー定義データベース ロールを使用してください。 **sysadmin** 固定サーバー ロールにログインを追加することはできません。 **CONTROL SERVER** アクセス許可を付与すると、**sysadmin** 固定サーバー ロールのメンバーシップが概算されます。  
  
 いくつかのステートメントでは、複数のアクセス許可が必要です。 たとえば、テーブルを作成するには、データベースの **CREATE TABLE** アクセス許可が必要であり、テーブルが含まれるテーブルの場合は **ALTER SCHEMA** アクセス許可が必要です。  
  
 PDW はストアドプロシージャを実行して、ユーザーの操作を計算ノードに分散することがあります。 そのため、データベース全体に対する execute 権限を拒否できません (たとえば `DENY EXECUTE ON DATABASE::<name> TO <user>;` は失敗します)。回避策として、ユーザー スキーマまたは特定のオブジェクト (プロシージャ) に対する execute 権限を拒否します。  
  
### <a name="implicit-and-explicit-permissions"></a>暗黙的および明示的なアクセス許可  
 *明示的なアクセス許可* は、**GRANT** または **DENY** ステートメントによってプリンシパルに付与される **GRANT** または **DENY** アクセス許可です。  
  
 *暗黙的なアクセス許可* は、プリンシパル (ログイン、ユーザー、データベース ロール) が別のデータベース ロールから継承する **GRANT** または **DENY** アクセス許可です。  
  
 暗黙的なアクセス許可は、包含または親のアクセス許可からも継承できます。 たとえば、テーブルの **UPDATE** アクセス許可は、テーブルが含まれるスキーマに対する **UPDATE** アクセス許可、またはテーブルの **CONTROL** アクセス許可を持つことによって継承できます。  
  
### <a name="ownership-chaining"></a>所有権の継承  
 複数のデータベース オブジェクトが連続して互いにアクセスしている場合、このシーケンスは *チェーン* と呼ばれます。 このようなチェーンは単独では存在しませんが、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] がチェーン内のリンクを移動する際に、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] によって、構成要素であるオブジェクトに対する権限が、オブジェクトに個別にアクセスした場合とは異なる方法で評価されます。 所有権の継承は、セキュリティの管理に重要な影響を与えます。 所有権の継承の詳細については、次を参照してください「[所有権の継承](https://msdn.microsoft.com/library/ms188676\(v=sql11\).aspx)」および「[チュートリアル:所有権の継承とコンテキストの切り替え](../../relational-databases/tutorial-ownership-chains-and-context-switching.md)」です。  
  
## <a name="permission-list"></a>アクセス許可の一覧  
  
### <a name="server-level-permissions"></a>サーバー レベルのアクセス許可  
 サーバー レベルのアクセス許可をログインに付与、拒否、および取り消すことができます。  
  
 **サーバーに適用されるアクセス許可**  
  
-   CONTROL SERVER  
  
-   ADMINISTER BULK OPERATIONS  
  
-   ALTER ANY CONNECTION  
  
-   ALTER ANY DATABASE  
  
-   CREATE ANY DATABASE  
  
-   すべての外部データ ソースを変更します。  
  
-   任意の外部のファイル形式を変更します。  
  
-   ALTER ANY LOGIN  
  
-   ALTER SERVER STATE  
  
-   CONNECT SQL  
  
-   VIEW ANY DEFINITION  
  
-   VIEW ANY DATABASE  
  
-   VIEW SERVER STATE  
  
 **ログインに適用される権限**  
  
-   CONTROL ON LOGIN  
  
-   ALTER ON LOGIN  
  
-   IMPERSONATE ON LOGIN  
  
-   VIEW DEFINITION  
  
### <a name="database-level-permissions"></a>データベース レベルのアクセス許可  
 データベース レベルのアクセス許可は、データベース ユーザーおよびユーザー定義のデータベース ロールに付与、拒否、および取り消すことができます。  
  
 **データベースのすべてのクラスに適用される権限**  
  
-   CONTROL  
  
-   ALTER  
  
-   VIEW DEFINITION  
  
 **ユーザー以外のすべてのデータベース クラスに適用される権限**  
  
-   TAKE OWNERSHIP  
  
 **データベースのみに適用されるアクセス許可**  
  
-   ALTER ANY DATABASE  
  
-   ALTER ON DATABASE  
  
-   ALTER ANY DATASPACE  
  
-   ALTER ANY ROLE  
  
-   ALTER ANY SCHEMA  
  
-   ALTER ANY USER  
  
-   BACKUP DATABASE  
  
-   CONNECT ON DATABASE  
  
-   CREATE PROCEDURE  
  
-   CREATE ROLE  
  
-   CREATE SCHEMA  
  
-   CREATE TABLE  
  
-   CREATE VIEW  
  
-   SHOWPLAN  
  
 **ユーザーにのみ適用されるアクセス許可**  
  
-   IMPERSONATE  
  
 **データベース、スキーマ、およびオブジェクトに適用される権限**  
  
-   ALTER  
  
-   DELETE  
  
-   EXECUTE  
  
-   INSERT  
  
-   SELECT  
  
-   UPDATE  
  
-   REFERENCES  
  
 各権限の種類の定義については、「[権限 (データベース エンジン)](../../relational-databases/security/permissions-database-engine.md)」を参照してください。  
  
### <a name="chart-of-permissions"></a>アクセス許可のグラフ  
 すべてのアクセス許可は、このポスターでグラフィカルに表されます。 これは、アクセス許可の入れ子になった階層構造を確認する最も簡単な方法です。 たとえば、**ALTER ON LOGIN** 自体でアクセス許可を与えることができますが、ログインがそのログインに対する **CONTROL** アクセス許可を与えられている場合、またはログインが **ALTER ANY LOGIN** アクセス許可を与えられている場合にも含まれます。  
  
 ![APS セキュリティの権限ポスター](../../t-sql/statements/media/aps-security-perms-poster.png "APS セキュリティの権限ポスター")  
  
  
## <a name="default-permissions"></a>既定の権限  
 次に、既定のアクセス許可について説明します。  
  
-   **CREATE LOGIN** ステートメントを使用してログインを作成するときに、新しいログインは **CONNECT SQL** アクセス トークンを付与されます。  
  
-   すべてのログインは、**public** サーバー ロールのメンバーであり、**public** から削除することはできません。  
  
-   **CREATE USER** アクセス許可を使用してデータベースユーザーを作成するときには、データベース ユーザーがデータベース内の **CONNECT** アクセス許可を付与されます。  
  
-   **パブリック** ロールを含むすべてのプリンシパルは、既定では明示的または暗黙的なアクセス許可を持っていません。  
  
-   ログインまたはユーザーがデータベースまたはオブジェクトの所有者になった場合、ログインまたはユーザーには、常に、データベースまたはオブジェクトに対するすべてのアクセス許可が与えられます。 所有者のアクセス許可は、変更することはできず、明示的なアクセス許可として表示されません。 **GRANT**、**DENY**、および **REVOKE** ステートメントは所有者に影響しません。  
  
-   **Sa** ログインは、アプライアンス上のすべてのアクセス許可を持っています。 所有者のアクセス許可と同様に、**sa** アクセス許可を変更することはできず、明示的なアクセス許可として表示されません。 **GRANT**、**DENY**、および **REVOKE** ステートメントは **sa** ログインに影響しません。 **Sa** ログインの名前を変更することはできません。  
  
-   **USE** ステートメントでは、権限は必要ありません。 すべてのプリンシパルが、**USE** ステートメントを任意のデータベース上で実行できます。  
  
##  <a name="examples-sssdw-and-sspdw"></a><a name="Examples"></a> 例: [!INCLUDE[ssSDW](../../includes/sssdw-md.md)] および [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="a-granting-a-server-level-permission-to-a-login"></a>A. ログインに、サーバー レベル権限を許可する  
 次の 2 つのステートメントでは、ログインに、サーバー レベルの権限を付与します。  
  
```sql  
GRANT CONTROL SERVER TO [Ted];  
```  
  
```sql  
GRANT ALTER ANY DATABASE TO Mary;  
```  
  
### <a name="b-granting-a-server-level-permission-to-a-login"></a>B. ログインに、サーバー レベル権限を許可する  
 次の例では、サーバー プリンシパル (別のログイン) に対して、ログインに、サーバー レベルの権限を付与します。  
  
```sql  
GRANT  VIEW DEFINITION ON LOGIN::Ted TO Mary;  
```  
  
### <a name="c-granting-a-database-level-permission-to-a-user"></a>C. ユーザーに、データベース レベル権限を許可する  
 次の例では、データベース プリンシパル (別のユーザー) に対して、ユーザーに、データベース レベルのアクセス許可を付与します。  
  
```sql  
GRANT VIEW DEFINITION ON USER::[Ted] TO Mary;  
```  
  
### <a name="d-granting-denying-and-revoking-a-schema-permission"></a>D. スキーマの権限を許可、拒否、および取り消す  
 次の **GRANT** ステートメントは、dbo スキーマの任意のテーブルまたはビューからデータを選択する権限を Yuen に付与します。  
  
```sql  
GRANT SELECT ON SCHEMA::dbo TO [Yuen];  
```  
  
 次の **DENY** ステートメントでは、Yuen が dbo スキーマのどのテーブルまたはビューからもデータを選択できないようにします。 Yuen がロールのメンバーシップを通してなど、その他の何らかの方法で権限を持っている場合でも、データを読み取ることはできません。  
  
```sql  
DENY SELECT ON SCHEMA::dbo TO [Yuen];  
```  
  
 次の **REVOKE** ステートメントは **DENY** 権利を削除します。 Yuen の明示的なアクセス許可は、ニュートラルになりました。 Yuen は、ロールのメンバーシップなどその他のいくつかの暗黙的な権限を任意のテーブルからデータを選択できる場合があります。  
  
```sql  
REVOKE SELECT ON SCHEMA::dbo TO [Yuen];  
```  
  
### <a name="e-demonstrating-the-optional-object-clause"></a>E. 省略可能な OBJECT:: 句を示す  
 OBJECT は、権限ステートメントの既定のクラスであるために、次の 2 つのステートメントは同じです。 **OBJECT::** 句は省略可能です。  
  
```sql  
GRANT UPDATE ON OBJECT::dbo.StatusTable TO [Ted];  
```  
  
```sql  
GRANT UPDATE ON dbo.StatusTable TO [Ted];  
```  
  
