---
description: IS_ROLEMEMBER (Transact-SQL)
title: IS_ROLEMEMBER (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- IS_ROLEMEMBER
- IS_ROLEMEMBER_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- roles [SQL Server], members
- IS_ROLEMEMBER function
- members [SQL Server], verifying
ms.assetid: 73efa688-ae91-4014-98bc-1cabe47321f7
author: VanMSFT
ms.author: vanto
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 6a71bdc75ed965e377ec0fd8e5e6a8ddfd60b477
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104746792"
---
# <a name="is_rolemember-transact-sql"></a>IS_ROLEMEMBER (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  データベース プリンシパルが指定されたデータベース ロールのメンバーであるかどうかを示します。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql
IS_ROLEMEMBER ( 'role' [ , 'database_principal' ] )  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
 **'** *role* **'**  
 確認するデータベース ロールの名前を指定します。 *role* は **sysname** です。  
  
 **'** *database_principal* **'**  
 確認するデータベース ユーザー、データベース ロール、またはアプリケーション ロールの名前です。 *database_principal* は **sysname**, 、既定値は NULL です。 値を指定しない場合、結果は現在の実行コンテキストに基づきます。 パラメーターに "NULL" という語が含まれていると、NULL が返されます。  
  
## <a name="return-types"></a>戻り値の型  
 **int**  
  
|戻り値|説明|  
|------------------|-----------------|  
|0|*database_principal* のメンバーではない *ロール* です。|  
|1|*database_principal* のメンバーである *ロール* です。|  
|NULL|*database_principal* または *ロール* が有効でないか、ロールのメンバーシップを表示する権限がありません。|  
  
## <a name="remarks"></a>解説  
 IS_ROLEMEMBER は、現在のユーザーがデータベース ロールの権限を必要とするアクションを実行できるかどうかを判断するために使用します。  
  
  unless the *database_principal* が許可または拒否された [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] への直接アクセスである場合を除き、*database_principal* が Contoso\Mary5 などの Windows ログインに基づいている場合、IS_ROLEMEMBER は NULL を返します。  
  
 場合、省略可能な *database_principal* パラメーターを指定しない場合、 *database_principal* ベースは、Windows ドメイン ログインに Windows グループのメンバーシップを通じて、データベース ロールのメンバーである可能性があります。 そのような間接的なメンバーシップを解決するために、IS_ROLEMEMBER は、Windows グループのメンバーシップ情報をドメイン コントローラーに要求します。 ドメイン コントローラーにアクセスできないか、またはドメイン コントローラーが応答しない場合、IS_ROLEMEMBER はユーザーとそのローカル グループのみを考慮したロール メンバーシップ情報を返します。 指定されたユーザーが現在のユーザーでない場合、IS_ROLEMEMBER が返す値は、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] に対する認証システム (Active Directory など) の最後のデータ更新と異なることがあります。  
  
 省略可能な *database_principal* パラメーターが指定された場合、照会されるデータベース プリンシパルが sys.database_principals に存在している必要があります。そうでない場合、IS_ROLEMEMBER は NULL を返します。 これが示す、 *database_principal* はこのデータベースでは無効です。  
  
 ときに、 *database_principal* パラメーターがに基づいてドメイン ログインか、Windows グループに基づく、ドメイン コント ローラーにアクセスできなくなって、IS_ROLEMEMBER の呼び出しは失敗し、不正確または不完全なデータを返す場合があります。  
  
 ドメイン コントローラーを利用できなくても、Windows プリンシパルをローカルで認証できる場合 (ローカル Windows アカウントや [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログインの場合など) は、IS_ROLEMEMBER の呼び出しで正確な情報が返されます。  
  
 **IS_ROLEMEMBER** は、Windows グループをデータベース プリンシパル引数として使用され、この Windows グループは、さらに、指定されたデータベース ロールのメンバー、別の Windows グループのメンバーである場合は、常に 0 を返します。  
  
 [!INCLUDE[wiprlhext](../../includes/wiprlhext-md.md)] および Windows Server 2008 にあるユーザー アカウント制御 (UAC) も異なる結果を返す場合があります。 これは、ユーザーがサーバーに Windows グループのメンバーとしてアクセスしたか、特定の [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ユーザーとしてアクセスしたかによります。  
  
 この関数で評価されるのはロールのメンバーシップであって、基になる権限ではありません。 たとえば、 **db_owner** 固定データベース ロールには、 **CONTROL DATABASE** 権限です。 ユーザーがいる場合、 **CONTROL DATABASE** 権限はない、ロールのメンバーと、この関数は、ユーザーがのメンバーではないことを報告して正しく、 **db_owner** ロールでは、ユーザーは、同じアクセス許可を持っている場合でもです。  
  
## <a name="related-functions"></a>関連する関数  
 現在のユーザーが指定された Windows グループまたは [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] データベース ロールのメンバーであるかどうかを判断するには、[IS_MEMBER &#40;Transact-SQL&#41;](../../t-sql/functions/is-member-transact-sql.md) を使用します。 確認するかどうか、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] を使用してログインがサーバー ロールのメンバーを [IS_SRVROLEMEMBER &#40;Transact-SQL&#41;](../../t-sql/functions/is-srvrolemember-transact-sql.md).  
  
## <a name="permissions"></a>アクセス許可  
 データベース ロールに対する VIEW DEFINITION 権限が必要です。  
  
## <a name="examples"></a>例  
 次の例では、現在のユーザーが `db_datareader` 固定データベース ロールのメンバーであるかどうかを示しています。  
  
```sql  
IF IS_ROLEMEMBER ('db_datareader') = 1  
   print 'Current user is a member of the db_datareader role'  
ELSE IF IS_ROLEMEMBER ('db_datareader') = 0  
   print 'Current user is NOT a member of the db_datareader role'  
ELSE IF IS_ROLEMEMBER ('db_datareader') IS NULL  
   print 'ERROR: The database role specified is not valid.';  
```  
  
## <a name="see-also"></a>参照  
 [CREATE ROLE &#40;Transact-SQL&#41;](../../t-sql/statements/create-role-transact-sql.md)   
 [ALTER ROLE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-role-transact-sql.md)   
 [DROP ROLE &#40;Transact-SQL&#41;](../../t-sql/statements/drop-role-transact-sql.md)   
 [CREATE SERVER ROLE &#40;Transact-SQL&#41;](../../t-sql/statements/create-server-role-transact-sql.md)   
 [ALTER SERVER ROLE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-server-role-transact-sql.md)   
 [DROP SERVER ROLE &#40;Transact-SQL&#41;](../../t-sql/statements/drop-server-role-transact-sql.md)   
 [IS_MEMBER &#40;Transact-SQL&#41;](../../t-sql/functions/is-member-transact-sql.md)   
 [IS_SRVROLEMEMBER &#40;Transact-SQL&#41;](../../t-sql/functions/is-srvrolemember-transact-sql.md)   
 [セキュリティ関数 &#40;Transact-SQL&#41;](../../t-sql/functions/security-functions-transact-sql.md)  
  
  
