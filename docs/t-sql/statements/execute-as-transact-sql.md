---
description: EXECUTE AS (Transact-SQL)
title: EXECUTE AS (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 08/27/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- EXECUTE AS
- EXECUTE_AS_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- REVERT statement
- WITH NO REVERT clause
- sessions [SQL Server], execution context
- EXECUTE AS
- execution context [SQL Server]
- switching execution context
ms.assetid: 613b8271-7f7d-4378-b7a2-5a7698551dbd
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: = azuresqldb-current || >= sql-server-2016 || >= sql-server-linux-2017||=azure-sqldw-latest
ms.openlocfilehash: 7d100893b14a135e0e08a1bfa6c40996a7a256e5
ms.sourcegitcommit: bacd45c349d1b33abef66db47e5aa809218af4ea
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104793073"
---
# <a name="execute-as-transact-sql"></a>EXECUTE AS (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa.md)]

  セッションの実行コンテキストを設定します。  
  
 既定では、セッションはユーザーがログインしたときに開始し、ユーザーがログオフしたときに終了します。 セッション中のすべての操作で、ユーザーに対する権限の確認が行われます。 **EXECUTE AS** ステートメントを実行するとき、セッションの実行コンテキストは指定したログインまたはユーザー名に切り替えられます。 コンテキスト切り替えの後は、**EXECUTE AS** ステートメントを呼び出したログインまたはユーザーではなく、コンテキスト スイッチのアカウントに関連するログインとユーザーのセキュリティ トークンに対して権限の確認が行われます。 実質的に、ユーザーまたはログイン アカウントは、セッションまたはモジュールの実行中、あるいはコンテキスト スイッチが明示的に戻されるまで続けて借用されます。  
  

  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql
{ EXEC | EXECUTE } AS <context_specification>  
[;]  
  
<context_specification>::=  
{ LOGIN | USER } = 'name'  
    [ WITH { NO REVERT | COOKIE INTO @varbinary_variable } ]   
| CALLER  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
 Login  
 **適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。  
  
 権限を借用する実行コンテキストがログインであることを指定します。 権限借用のスコープはサーバー レベルです。  
  
> [!NOTE]  
>  このオプションは、包含データベースまたは [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] または [!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] では使用できません。  
  
 User  
 権限を借用するコンテキストが、現在のデータベース内のユーザーであることを指定します。 権限借用のスコープは、現在のデータベースに限定されます。 コンテキスト スイッチの対象がデータベース ユーザーであっても、そのユーザーのサーバー レベルの権限は継承されません。  
  
> [!IMPORTANT]  
>  データベース ユーザーに対するコンテキスト スイッチがアクティブであるときに、データベース外部のリソースへアクセスを試みると、ステートメントが失敗する原因となります。 たとえば、USE *database* ステートメントや分散クエリ、3 部または 4 部構成の識別子を使用する別のデータベースを参照するクエリなどは実行しないでください。  
  
 '*name*' は、有効なユーザー名またはログイン名です。 *name* は、**sysadmin** 固定サーバー ロールのメンバーであるか、[sys.database_principals](../../relational-databases/system-catalog-views/sys-database-principals-transact-sql.md) または [sys.server_principals](../../relational-databases/system-catalog-views/sys-server-principals-transact-sql.md) のプリンシパルとして存在する必要があります。  
  
 *name* はローカル変数として指定できます。  
  
 *name* には単一アカウントを指定する必要があり、グループ、ロール、証明書、キー、NT AUTHORITY\LocalService、NT AUTHORITY\NetworkService、NT AUTHORITY\LocalSystem などのビルトイン アカウントにすることはできません。  
  
 詳しくは、後の「[ユーザーまたはログイン名の指定](#_user)」をご覧ください。  
  
 NO REVERT  
 コンテキスト スイッチを以前のコンテキストに戻せないことを示します。 **NO REVERT** オプションを使用できるのは、アドホック レベルでのみです。  
  
 以前のコンテキストに戻す方法について詳しくは、「[REVERT &#40;Transact-SQL&#41;](../../t-sql/statements/revert-transact-sql.md)」をご覧ください。  
  
 COOKIE INTO @*varbinary_variable*  
 REVERT WITH COOKIE ステートメントの呼び出し時に適切な @*varbinary_variable* 値が含まれている場合にのみ、実行コンテキストを以前のコンテキストに戻すことができます。 [!INCLUDE[ssDE](../../includes/ssde-md.md)]では、Cookie が @*varbinary_variable* に渡されます。 **COOKIE INTO** オプションを使用できるのは、アドホック レベルでのみです。  
  
 @*varbinary_variable* は **varbinary(8000)** です。  
  
> [!NOTE]  
>  Cookie の **OUTPUT** パラメーターは現在、適切な最大長である **varbinary(8000)** としてドキュメントに記載されています。 ただし、現在の実装では **varbinary(100)** を返します。 将来のリリースでクッキーの戻り値のサイズが増えた場合にアプリケーションが引き続き正常に動作するように、アプリケーションでは **varbinary(8000)** を予約しておく必要があります。  
  
 CALLER  
 モジュール内で使用した場合、モジュールの呼び出し元のコンテキストで、モジュール内のステートメントが実行されます。
モジュール外で使用した場合、このステートメントでは何も処理されません。
 > [!NOTE]  
>  [!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)]では、このオプションは使用できません。  
  
## <a name="remarks"></a>解説  
 実行コンテキストでの変更は、次のいずれかが行われるまで有効です。  
  
-   別の EXECUTE AS ステートメントの実行  
  
-   REVERT ステートメントの実行。  
  
-   セッションの停止  
  
-   コマンドが実行されたストアド プロシージャまたはトリガーの終了  
  
複数のプリンシパルで複数回、EXECUTE AS ステートメントを呼び出すと、実行コンテキストのスタックを作成できます。 これを実行すると、REVERT ステートメントにより、コンテキスト スタックの次の上位レベルのログインまたはユーザーにコンテキストが切り替えられます。 この動作のデモについては、「[例 A](#_exampleA)」をご覧ください。  
  
##  <a name="specifying-a-user-or-login-name"></a><a name="_user"></a> ユーザーまたはログイン名の指定  
 EXECUTE AS \<context_specification> で指定するユーザーまたはログイン名は、それぞれ **sys.database_principals** または **sys.server_principals** のプリンシパルとして存在する必要があります。存在しない場合、EXECUTE AS ステートメントは失敗します。 さらに、プリンシパルで IMPERSONATE 権限が許可されている必要があります。 呼び出し元がデータベース所有者または固定サーバー ロール **sysadmin** のメンバーでない場合は、ユーザーが Windows グループ メンバーシップによって [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のデータベースやインスタンスにアクセスしているときでも、プリンシパルは存在する必要があります。 たとえば、次のような条件を想定します。 
  
-   **CompanyDomain\SQLUsers** グループに **Sales** データベースへのアクセス権がある。  
  
-   **CompanyDomain\SqlUser1** は **SQLUsers** のメンバーであり、したがって **Sales** データベースへのアクセスが暗黙的に許可されている。  
  
 この場合、**CompanyDomain\SqlUser1** は **SQLUsers** グループのメンバーシップを介してデータベースにアクセスできますが、`CompanyDomain\SqlUser1` がプリンシパルとしてデータベースに存在しないので、ステートメント `EXECUTE AS USER = 'CompanyDomain\SqlUser1'` は失敗します。  
  
ユーザーが孤立した (関連付けられたログインが存在しない) 状態になり、かつユーザーが **WITHOUT LOGIN** で作成されなかった場合、そのユーザーに対する **EXECUTE AS** は失敗します。  
  
## <a name="best-practice"></a>推奨事項  
 セッションで操作を実行する場合に必要となる最低限の権限を持つログインまたはユーザーを指定します。 たとえば、データベース レベルの権限だけが必要な場合、サーバー レベルの権限が与えられているログイン名は指定しません。また、データベース所有者アカウントは、データベース レベルの権限が必要とされない場合は指定しません。  
  
> [!CAUTION]  
>  EXECUTE AS ステートメントは、[!INCLUDE[ssDE](../../includes/ssde-md.md)]が名前を解決できる限り、正常に実行できます。 ドメイン ユーザーが存在する場合、Windows ユーザーに [!INCLUDE[ssDE](../../includes/ssde-md.md)] へのアクセス権がなくても、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]のユーザーを解決できることがあります。 これにより、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] へのアクセス権のないログインがログインしているように見える場合もありますが、権限借用したログインの権限が public または guest に付与されているだけの場合があります。  
  
## <a name="using-with-no-revert"></a>WITH NO REVERT の使用  
 EXECUTE AS ステートメントにオプションの WITH NO REVERT 句が含まれている場合、REVERT を使用、または別の EXECUTE AS ステートメントを実行して、セッションの実行コンテキストを元に戻すことはできません。 ステートメントで設定されたコンテキストはセッションが削除されるまで有効です。  
  
 WITH NO REVERT COOKIE = @*varbinary_variable* 句を指定した場合、[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]はクッキー値を @*varbinary_variable* に渡します。 そのステートメントで設定された実行コンテキストを以前のコンテキストに戻すことができるのは、REVERT WITH COOKIE = @*varbinary_variable* ステートメントの呼び出し時に、同じ *\@varbinary_variable* 値が含まれている場合だけです。  
  
 このオプションは、接続プールが使用されている環境で役立ちます。 接続プールには、アプリケーション サーバーのアプリケーションで再利用できるよう、データベース接続のグループが保持されています。 *\@varbinary_variable* に渡される値を認識できるのは EXECUTE AS ステートメントの呼び出し元のみであるため、呼び出し元が確立した実行コンテキストは、他のアカウントによって変更されることはありません。  
  
## <a name="determining-the-original-login"></a>元のログインの特定  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスに接続したログインの名前に戻すには、[ORIGINAL_LOGIN](../../t-sql/functions/original-login-transact-sql.md) 関数を使用します。 この関数を使用すると、明示的または暗黙的にコンテキストが何度も切り替えられるセッションにおける、元のログインの ID を取得できます。  
  
## <a name="permissions"></a>アクセス許可  
 ログインで **EXECUTE AS** を指定するには、呼び出し元に、指定のログイン名に対する **IMPERSONATE** 権限が与えられている必要があり、また **IMPERSONATE ANY LOGIN** 権限が拒否されていないことも条件となります。 データベース ユーザーに **EXECUTE AS** を指定するには、呼び出し元に、指定のユーザー名に対する **IMPERSONATE** 権限が与えられている必要があります。 **EXECUTE AS CALLER** を指定した場合、**IMPERSONATE** 権限は必要ありません。  
  
## <a name="examples"></a>例  
  
###  <a name="a-using-execute-as-and-revert-to-switch-context"></a><a name="_exampleA"></a> A. EXECUTE AS と REVERT を使用してコンテキストを切り替える  
 次の例では、複数のプリンシパルを使用してコンテキスト実行スタックを作成した後、 `REVERT` ステートメントを使用して実行コンテキストを以前のコンテキストに戻します。 `REVERT` ステートメントは、実行コンテキストが最初の呼び出し元に設定されるまで、スタックの上層に向かって複数回実行されます。  
  

```sql
USE AdventureWorks2012;  
GO  
--Create two temporary principals  
CREATE LOGIN login1 WITH PASSWORD = 'J345#$)thb';  
CREATE LOGIN login2 WITH PASSWORD = 'Uor80$23b';  
GO  
CREATE USER user1 FOR LOGIN login1;  
CREATE USER user2 FOR LOGIN login2;  
GO  
--Give IMPERSONATE permissions on user2 to user1  
--so that user1 can successfully set the execution context to user2.  
GRANT IMPERSONATE ON USER:: user2 TO user1;  
GO  
--Display current execution context.  
SELECT SUSER_NAME(), USER_NAME();  
-- Set the execution context to login1.   
EXECUTE AS LOGIN = 'login1';  
--Verify the execution context is now login1.  
SELECT SUSER_NAME(), USER_NAME();  
--Login1 sets the execution context to login2.  
EXECUTE AS USER = 'user2';  
--Display current execution context.  
SELECT SUSER_NAME(), USER_NAME();  
-- The execution context stack now has three principals: the originating caller, login1 and login2.  
--The following REVERT statements will reset the execution context to the previous context.  
REVERT;  
--Display current execution context.  
SELECT SUSER_NAME(), USER_NAME();  
REVERT;  
--Display current execution context.  
SELECT SUSER_NAME(), USER_NAME();  
  
--Remove temporary principals.  
DROP LOGIN login1;  
DROP LOGIN login2;  
DROP USER user1;  
DROP USER user2;  
GO  
```  
  
### <a name="b-using-the-with-cookie-clause"></a>B. WITH COOKIE 句を使用する  
 次の例では、セッションの実行コンテキストを、指定したユーザーに設定し、WITH COOKIE INTO @*varbinary_variable* 句を指定します。 コンテキストを正常に呼び出し元に戻すには、`REVERT` ステートメントで、`EXECUTE AS` ステートメントの `@cookie` 変数に渡される値を指定する必要があります。 この例を実行するには、例 A で作成したログイン `login1` とユーザー `user1` が存在している必要があります。  
  

```sql 
DECLARE @cookie VARBINARY(8000);  
EXECUTE AS USER = 'user1' WITH COOKIE INTO @cookie;  
-- Store the cookie in a safe location in your application.  
-- Verify the context switch.  
SELECT SUSER_NAME(), USER_NAME();  
--Display the cookie value.  
SELECT @cookie;  
GO  
-- Use the cookie in the REVERT statement.  
DECLARE @cookie VARBINARY(8000);  
-- Set the cookie value to the one from the SELECT @cookie statement.  
SET @cookie = <value from the SELECT @cookie statement>;  
REVERT WITH COOKIE = @cookie;  
-- Verify the context switch reverted.  
SELECT SUSER_NAME(), USER_NAME();  
GO  
```  
  
## <a name="see-also"></a>参照  
 [REVERT &#40;Transact-SQL&#41;](../../t-sql/statements/revert-transact-sql.md)   
 [EXECUTE AS 句 &#40;Transact-SQL&#41;](../../t-sql/statements/execute-as-clause-transact-sql.md)  
  
  

