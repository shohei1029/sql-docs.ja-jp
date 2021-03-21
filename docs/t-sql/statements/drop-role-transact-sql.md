---
description: DROP ROLE (Transact-SQL)
title: DROP ROLE (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 05/11/2017
ms.prod: sql
ms.prod_service: synapse-analytics, pdw, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- DROP ROLE
- DROP_ROLE_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- deleting roles
- database roles [SQL Server], removing
- removing roles
- DROP ROLE statement
- roles [SQL Server], removing
- dropping roles
ms.assetid: 1f6f13ae-56a2-4ef1-93f5-8e6151b83e1d
author: VanMSFT
ms.author: vanto
monikerRange: '>=aps-pdw-2016||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 2c7cd392d498beb5b170715ff6f16b338fc61ca8
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104750582"
---
# <a name="drop-role-transact-sql"></a>DROP ROLE (Transact-SQL)
[!INCLUDE [sql-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdbmi-asa-pdw.md)]

  データベースからロールを削除します。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql  
-- Syntax for SQL Server  
  
DROP ROLE [ IF EXISTS ] role_name  
```  
  

```syntaxsql  
-- Syntax for Azure Synapse Analytics and Parallel Data Warehouse  

DROP ROLE role_name  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
 *IF EXISTS*  
 **適用対象**: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] から [現在のバージョン](/troubleshoot/sql/general/determine-version-edition-update-level)まで)。  
  
 条件付きでは既に存在する場合にのみ、ロールを削除します。  
  
 *role_name*  
 データベースから削除するロールを指定します。  
  
## <a name="remarks"></a>解説  
 セキュリティ保護可能なリソースを所有するロールは、データベースから削除できません。 セキュリティ保護可能なリソースを所有するデータベース ロールを削除するには、まず、セキュリティ保護可能なリソースの所有権を転送するか、リソースをデータベースから削除する必要があります。 メンバーを含むロールは、データベースから削除できません。 メンバーを含むロールを削除するには、まず、ロールのメンバーを削除する必要があります。  
  
 データベース ロールからメンバーを削除するには、[ALTER ROLE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-role-transact-sql.md) を使います。  
  
 DROP ROLE を使用して、固定データベース ロールを削除することはできません。  
  
 ロールのメンバーシップに関する情報は、sys.database_role_members カタログ ビューで表示できます。  
  
> [!CAUTION]  
>  [!INCLUDE[ssCautionUserSchema](../../includes/sscautionuserschema-md.md)]  
  
 サーバー ロールを削除するには、[DROP SERVER ROLE &#40;Transact-SQL&#41;](../../t-sql/statements/drop-server-role-transact-sql.md) を使用します。  
  
## <a name="permissions"></a>アクセス許可  
 データベースに対する **ALTER ANY ROLE** アクセス許可、ロールに対する **CONTROL** アクセス許可、または **db_securityadmin** のメンバーシップが必要です。  
  
## <a name="examples"></a>例  
 次の例では、データベース ロール `purchasing` を `AdventureWorks2012` データベースから削除します。  
  
```sql  
DROP ROLE purchasing;  
GO  
```  
  
  
## <a name="see-also"></a>参照  
 [CREATE ROLE &#40;Transact-SQL&#41;](../../t-sql/statements/create-role-transact-sql.md)   
 [ALTER ROLE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-role-transact-sql.md)   
 [プリンシパル &#40;データベース エンジン&#41;](../../relational-databases/security/authentication-access/principals-database-engine.md)   
 [EVENTDATA &#40;Transact-SQL&#41;](../../t-sql/functions/eventdata-transact-sql.md)   
 [sp_addrolemember &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-addrolemember-transact-sql.md)   
 [sys.database_role_members &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-database-role-members-transact-sql.md)   
 [sys.database_principals &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-database-principals-transact-sql.md)   
 [セキュリティ関数 &#40;Transact-SQL&#41;](../../t-sql/functions/security-functions-transact-sql.md)  
