---
description: sp_addrolemember (Transact-SQL)
title: sp_addrolemember (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 01/30/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- sp_addrolemember_TSQL
- sp_addrolemember
dev_langs:
- TSQL
helpviewer_keywords:
- sp_addrolemember
ms.assetid: a583c087-bdb3-46d2-b9e5-3921b3e6d10b
author: VanMSFT
ms.author: vanto
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 0174aef135fcf579b1363efb414ee1dcbee4ae26
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104754062"
---
# <a name="sp_addrolemember-transact-sql"></a>sp_addrolemember (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  データベース ユーザー、データベース ロール、Windows ログイン、または Windows グループを、現在のデータベースのデータベース ロールに追加します。  
  
> [!IMPORTANT]  
>  [!INCLUDE[ssNoteDepFutureAvoid](../../includes/ssnotedepfutureavoid-md.md)] 代わりに [ALTER ROLE](../../t-sql/statements/alter-role-transact-sql.md) を使用してください。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql
sp_addrolemember [ @rolename = ] 'role', [ @membername = ] 'security_account'  
```    

[!INCLUDE[synapse-analytics-od-unsupported-syntax](../../includes/synapse-analytics-od-unsupported-syntax.md)]
  
## <a name="arguments"></a>引数  
 [ @rolename =] '*role*'  
 現在のデータベースのデータベースロールの名前を指定します。 *role* は **sysname** で、既定値はありません。  
  
 [ @membername =] '*security_account*'  
 ロールに追加するセキュリティアカウントを示します。 *security_account* は **sysname** であり、既定値はありません。 *security_account* には、データベースユーザー、データベースロール、windows ログイン、または windows グループを指定できます。  
  
## <a name="return-code-values"></a>リターン コードの値  
 0 (成功) または 1 (失敗)  
  
## <a name="remarks"></a>解説  
 sp_addrolemember を使用してロールに追加されたメンバーは、ロールの権限を継承します。 新しいメンバーが、対応するデータベースユーザーのない Windows レベルのプリンシパルである場合は、データベースユーザーが作成されますが、そのログインに完全にマップされていない可能性があります。 ログインが存在し、そのログインがデータベースへのアクセス権を持っていることを必ず確認してください。  
  
 ロールには、ロール自体をメンバーとして含めることはできません。 このような "循環" 定義は、1 つ以上の中間メンバーシップを介して間接的にメンバーシップが与えられる場合でも無効です。  
  
 固定データベースロール、固定サーバーロール、または dbo をロールに追加 sp_addrolemember ことはできません。
  
 sp_addrolemember は、メンバーをデータベース ロールに追加する場合にのみ使用します。 サーバーロールにメンバーを追加するには、 [sp_addsrvrolemember &#40;transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-addsrvrolemember-transact-sql.md)を使用します。  
  
## <a name="permissions"></a>権限  
 柔軟なデータベースロールにメンバーを追加するには、次のいずれかが必要です。  
  
-   Db_securityadmin または db_owner 固定データベースロールのメンバーシップ。  
  
-   ロールを所有するロールのメンバーシップ  
  
-   ロールに対する **ALTER ANY role** 権限または **alter** 権限。  
  
 固定データベースロールにメンバーを追加するには、db_owner 固定データベースロールのメンバーシップが必要です。  
  
## <a name="examples"></a>例  
  
### <a name="a-adding-a-windows-login"></a>A. Windows ログインの追加  
 次の例では、Windows ログイン `Contoso\Mary5` を `AdventureWorks2012` ユーザーとしてデータベースに追加し `Mary5` ます。 その後、ユーザー `Mary5` がロールに追加され `Production` ます。  
  
> [!NOTE]  
>  `Contoso\Mary5` はデータベース [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] のデータベース ユーザー `Mary5` として設定されているので、ユーザー名 `Mary5` を指定する必要があります。 `Contoso\Mary5` のログインが存在しない場合、このステートメントは失敗します。 ドメインからのログインを使用してテストします。  
  
```sql  
USE AdventureWorks2012;  
GO  
CREATE USER Mary5 FOR LOGIN [Contoso\Mary5] ;  
GO  
```  
  
### <a name="b-adding-a-database-user"></a>B. データベースユーザーの追加  
 次の例では、データベースユーザーを `Mary5` 現在のデータベースのデータベースロールに追加し `Production` ます。  
  
```sql  
EXEC sp_addrolemember 'Production', 'Mary5';  
```  
  
## <a name="examples-sspdw"></a>例: [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="c-adding-a-windows-login"></a>C. Windows ログインの追加  
 次の例では、ログインを `LoginMary` `AdventureWorks2008R2` ユーザーとしてデータベースに追加し `UserMary` ます。 その後、ユーザー `UserMary` がロールに追加され `Production` ます。  
  
> [!NOTE]  
>  ログイン `LoginMary` はデータベースのデータベースユーザーとして知られているので、 `UserMary` [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] ユーザー名を `UserMary` 指定する必要があります。 `Mary5` のログインが存在しない場合、このステートメントは失敗します。 通常、ログインとユーザーの名前は同じです。 この例では、ログインとユーザーに影響するアクションを区別するために、異なる名前を使用しています。  
  
```sql  
-- Uses AdventureWorks  
  
CREATE USER UserMary FOR LOGIN LoginMary ;  
GO  
EXEC sp_addrolemember 'Production', 'UserMary'  
```  
  
### <a name="d-adding-a-database-user"></a>D. データベースユーザーの追加  
 次の例では、データベースユーザーを `UserMary` 現在のデータベースのデータベースロールに追加し `Production` ます。  
  
```sql  
EXEC sp_addrolemember 'Production', 'UserMary'  
```  
  
## <a name="see-also"></a>関連項目  
 [セキュリティ ストアド プロシージャ &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/security-stored-procedures-transact-sql.md)   
 [sp_addsrvrolemember &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-addsrvrolemember-transact-sql.md)   
 [sp_droprolemember &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-droprolemember-transact-sql.md)   
 [sp_grantdbaccess &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-grantdbaccess-transact-sql.md)   
 [システム ストアド プロシージャ &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/system-stored-procedures-transact-sql.md)   
 [データベース レベルのロール](../../relational-databases/security/authentication-access/database-level-roles.md)  
  
  
