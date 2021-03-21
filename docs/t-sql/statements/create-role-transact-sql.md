---
description: CREATE ROLE (Transact-SQL)
title: CREATE ROLE (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 04/10/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- CREATE ROLE
- DATABASE ROLE
- ROLE_TSQL
- DATABASE_ROLE_TSQL
- CREATE_ROLE_TSQL
- CREATE DATABASE ROLE
- ROLE
- CREATE_DATABASE_ROLE_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- database roles [SQL Server], creating
- CREATE DATABASE ROLE statement
- roles [SQL Server], creating
- CREATE ROLE statement
ms.assetid: b0cd54ad-e81d-4d71-acec-8a6d7261ca08
author: VanMSFT
ms.author: vanto
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 7741884929c7d682bee55067f9d8e64ade999d42
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104749222"
---
# <a name="create-role-transact-sql"></a>CREATE ROLE (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  現在のデータベースに新しいデータベース ロールを作成します。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql  
CREATE ROLE role_name [ AUTHORIZATION owner_name ]  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
 *role_name*  
 作成するロールの名前です。  
  
 AUTHORIZATION *owner_name*  
 新しいロールを所有するデータベース ユーザーまたはロールを指定します。 ユーザーを指定しない場合、ロールは CREATE ROLE を実行するユーザーが所有します。 ロールの所有者、または所有するロールのメンバーは、ロールのメンバーを追加または削除できます。
  
## <a name="remarks"></a>注釈  
 ロールはデータベース レベルのセキュリティ保護可能なリソースです。 ロールを作成した後は、GRANT、DENY、REVOKE を使って、ロールのデータベース レベルの権限を構成します。 データベース ロールにメンバーを追加するには、[ALTER ROLE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-role-transact-sql.md) を使います。 詳しくは、「[データベース レベルのロール](../../relational-databases/security/authentication-access/database-level-roles.md)」をご覧ください。  
  
 データベース ロールは、sys.database_role_members および sys.database_principals カタログ ビューで確認できます。  
  
 権限システムの設計の詳細については、「 [データベース エンジンの権限の概要](../../relational-databases/security/authentication-access/getting-started-with-database-engine-permissions.md)」を参照してください。  
  
> [!CAUTION]  
>  [!INCLUDE[ssCautionUserSchema](../../includes/sscautionuserschema-md.md)]  
  
## <a name="permissions"></a>アクセス許可  
 データベースの **CREATE ROLE** 権限、または **db_securityadmin** 固定データベース ロールのメンバーシップが必要です。 **AUTHORIZATION** オプションを使用する場合は、次の権限も必要です。  
  
-   ロールの所有権を別のユーザーに割り当てるには、そのユーザーに対する IMPERSONATE 権限が必要です。  
  
-   ロールの所有権を別のロールに割り当てるには、割り当て先のロールのメンバーシップまたはそのロールに対する ALTER 権限が必要です。  
  
-   ロールの所有権をアプリケーション ロールに割り当てるには、アプリケーション ロールに対する ALTER 権限が必要です。  
  
## <a name="examples"></a>例  
以下のすべての例では、AdventureWorks データベースを使います。   

### <a name="a-creating-a-database-role-that-is-owned-by-a-database-user"></a>A. データベース ユーザーが所有するデータベース ロールを作成する  
 次の例では、ユーザー `BenMiller` が所有するデータベース ロール `buyers` を作成します。  
  
```sql  
CREATE ROLE buyers AUTHORIZATION BenMiller;  
GO  
```  
  
### <a name="b-creating-a-database-role-that-is-owned-by-a-fixed-database-role"></a>B. 固定データベース ロールが所有するデータベース ロールを作成する  
 次の例では、固定データベース ロール `db_securityadmin` が所有するデータベース ロール `auditors` を作成します。  
  
```sql  
CREATE ROLE auditors AUTHORIZATION db_securityadmin;  
GO  
```  
  
## <a name="see-also"></a>参照  
 [プリンシパル &#40;データベース エンジン&#41;](../../relational-databases/security/authentication-access/principals-database-engine.md)   
 [ALTER ROLE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-role-transact-sql.md)   
 [DROP ROLE &#40;Transact-SQL&#41;](../../t-sql/statements/drop-role-transact-sql.md)   
 [EVENTDATA &#40;Transact-SQL&#41;](../../t-sql/functions/eventdata-transact-sql.md)   
 [sp_addrolemember &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-addrolemember-transact-sql.md)   
 [sys.database_role_members &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-database-role-members-transact-sql.md)   
 [sys.database_principals &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-database-principals-transact-sql.md)   
 [データベース エンジンの権限の概要](../../relational-databases/security/authentication-access/getting-started-with-database-engine-permissions.md)  
  
  


