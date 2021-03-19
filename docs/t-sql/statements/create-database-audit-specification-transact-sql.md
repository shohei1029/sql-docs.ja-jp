---
title: CREATE DATABASE AUDIT SPECIFICATION
description: SQL Server Audit 機能を使用して、データベース監査仕様オブジェクトを作成します。
titleSuffix: SQL Server (Transact-SQL)
ms.custom: seo-lt-2019
ms.date: 01/03/2020
ms.prod: sql
ms.prod_service: sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- CREATE DATABASE AUDIT
- DATABASE_AUDIT_SPECIFICATION_TSQL
- DATABASE AUDIT SPECIFICATION
- CREATE_DATABASE_AUDIT_SPECIFICATION_TSQL
- CREATE_DATABASE_AUDIT_TSQL
- CREATE DATABASE AUDIT SPECIFICATION
dev_langs:
- TSQL
helpviewer_keywords:
- database audit specification
- CREATE DATABASE AUDIT SPECIFICATION statement
ms.assetid: 0544da48-0ca3-4a01-ba4c-940e23dc315b
author: VanMSFT
ms.author: vanto
ms.openlocfilehash: dfa2c988670df2c2b7b9176cbde4057c4b125c75
ms.sourcegitcommit: 2cc2e4e17ce88ef47cda32a60a02d929e617738e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/15/2021
ms.locfileid: "103473206"
---
# <a name="create-database-audit-specification-transact-sql"></a>CREATE DATABASE AUDIT SPECIFICATION (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 監査機能を使用して、データベース監査仕様オブジェクトを作成します。 詳しくは、「[SQL Server Audit &#40;データベース エンジン&#41;](../../relational-databases/security/auditing/sql-server-audit-database-engine.md)」を参照してください。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql
CREATE DATABASE AUDIT SPECIFICATION audit_specification_name  
{  
    FOR SERVER AUDIT audit_name   
        [ { ADD ( { <audit_action_specification> | audit_action_group_name } )   
      } [, ...n] ]  
    [ WITH ( STATE = { ON | OFF } ) ]  
}  
[ ; ]  
<audit_action_specification>::=  
{  
      action [ ,...n ]ON [ class :: ] securable BY principal [ ,...n ]  
}  
```  

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
 *audit_specification_name*  
 監査仕様の名前を指定します。  
  
 *audit_name*  
 この仕様が適用される監査の名前を指定します。  
  
 *audit_action_specification*  
 監査で記録する、セキュリティ保護可能なリソースに対するプリンシパルによるアクションの仕様を指定します。  
  
 *action*  
 データベース レベルの 1 つまたは複数の監査可能なアクションの名前を指定します。 監査アクションの一覧については、「[SQL Server 監査のアクション グループとアクション](../../relational-databases/security/auditing/sql-server-audit-action-groups-and-actions.md)」をご覧ください。  
  
 *audit_action_group_name*  
 データベース レベルの 1 つ以上の監査可能なアクション グループの名前を指定します。 監査アクション グループの一覧については、「[SQL Server 監査のアクション グループとアクション](../../relational-databases/security/auditing/sql-server-audit-action-groups-and-actions.md)」をご覧ください。  
  
 *class*  
 セキュリティ保護可能なリソースのクラス名 (該当する場合) です。  
  
 *securable*  
 監査アクションまたは監査アクション グループを適用するデータベース内のテーブル、ビュー、またはその他のセキュリティ保護可能なオブジェクトを指定します。 詳細については、「 [セキュリティ保護可能](../../relational-databases/security/securables.md)」を参照してください。  
  
 *principal*  
 監査アクションまたは監査アクション グループを適用するデータベース プリンシパルの名前を指定します。 データベース プリンシパルをすべて監査するには、データベース プリンシパル `public` を使用します。 詳しくは、「[プリンシパル &#40;データベース エンジン&#41;](../../relational-databases/security/authentication-access/principals-database-engine.md)」をご覧ください。  
  
 WITH ( STATE = { ON | OFF } )  
 監査による、この監査仕様についてのレコードの収集を有効または無効にします。  
  
## <a name="remarks"></a>解説  
 データベース監査仕様は、セキュリティ保護できないオブジェクトであり、特定のデータベースに保存されます。 作成されたデータベース監査仕様は無効な状態です。  
  
## <a name="permissions"></a>アクセス許可  
 `ALTER ANY DATABASE AUDIT` 権限を持つユーザーは、データベース監査の仕様を作成し、任意の監査にバインドできます。  
  
 データベース監査仕様の作成後は、`CONTROL SERVER` 権限を持つユーザー、または `sysadmin` アカウントがその仕様を表示できます。  
  
## <a name="examples"></a>例

### <a name="a-audit-select-and-insert-on-a-table-for-any-database-principal"></a>A. 任意のデータベース プリンシパルについてテーブルで SELECT と INSERT を監査する 
 次の例では、`Payrole_Security_Audit` というサーバー監査を作成した後、`AdventureWorks2012` データベースの `HumanResources.EmployeePayHistory` テーブルで、`public` データベース ロールのすべてのメンバーによる `SELECT` ステートメントと `INSERT` ステートメントを監査する、`Payrole_Security_Audit` というデータベース監査仕様を作成します。 これにより、すべてのユーザーが監査されるようになります。すべてのユーザーは常に `public` ロールのメンバーであるからです。
  
```sql  
USE master ;  
GO  
-- Create the server audit.  
CREATE SERVER AUDIT Payrole_Security_Audit  
    TO FILE ( FILEPATH =   
'D:\SQLAudit\' ) ;  -- make sure this path exists
GO  
-- Enable the server audit.  
ALTER SERVER AUDIT Payrole_Security_Audit   
WITH (STATE = ON) ;  
GO  
-- Move to the target database.  
USE AdventureWorks2012 ;  
GO  
-- Create the database audit specification.  
CREATE DATABASE AUDIT SPECIFICATION Audit_Pay_Tables  
FOR SERVER AUDIT Payrole_Security_Audit  
ADD (SELECT , INSERT  
     ON HumanResources.EmployeePayHistory BY public )  
WITH (STATE = ON) ;  
GO  
``` 

### <a name="b-audit-any-dml-insert-update-or-delete-on-_all_-objects-in-the-_sales_-schema-for-a-specific-database-role"></a>B. 特定のデータベース ロールについて _sales_ スキーマの _すべて_ のオブジェクトで任意の DML (INSERT、UPDATE、または DELETE) を監査する  
 次の例では、`DataModification_Security_Audit` というサーバー監査を作成した後、`Audit_Data_Modification_On_All_Sales_Tables` データベースでの `INSERT` スキーマのすべてのオブジェクトについて、新しいデータベース ロール `UPDATE` のユーザーによる `DELETE`、`SalesUK` および `Sales` ステートメントを監査する、`AdventureWorks2012` というデータベース監査仕様を作成します。  
  
```sql  
USE master ;  
GO  
-- Create the server audit.
-- Change the path to a path that the SQLServer Service has access to. 
CREATE SERVER AUDIT DataModification_Security_Audit  
    TO FILE ( FILEPATH = 
'D:\SQLAudit\' ) ;  -- make sure this path exists
GO  
-- Enable the server audit.  
ALTER SERVER AUDIT DataModification_Security_Audit   
WITH (STATE = ON) ;  
GO  
-- Move to the target database.  
USE AdventureWorks2012 ;  
GO  
CREATE ROLE SalesUK
GO
-- Create the database audit specification.  
CREATE DATABASE AUDIT SPECIFICATION Audit_Data_Modification_On_All_Sales_Tables  
FOR SERVER AUDIT DataModification_Security_Audit  
ADD ( INSERT, UPDATE, DELETE  
     ON Schema::Sales BY SalesUK )  
WITH (STATE = ON) ;    
GO  
```  


## <a name="see-also"></a>参照  
 [CREATE SERVER AUDIT &#40;Transact-SQL&#41;](../../t-sql/statements/create-server-audit-transact-sql.md)   
 [ALTER SERVER AUDIT &#40;Transact-SQL&#41;](../../t-sql/statements/alter-server-audit-transact-sql.md)   
 [DROP SERVER AUDIT &#40;Transact-SQL&#41;](../../t-sql/statements/drop-server-audit-transact-sql.md)   
 [CREATE SERVER AUDIT SPECIFICATION &#40;Transact-SQL&#41;](../../t-sql/statements/create-server-audit-specification-transact-sql.md)   
 [ALTER SERVER AUDIT SPECIFICATION &#40;Transact-SQL&#41;](../../t-sql/statements/alter-server-audit-specification-transact-sql.md)   
 [DROP SERVER AUDIT SPECIFICATION &#40;Transact-SQL&#41;](../../t-sql/statements/drop-server-audit-specification-transact-sql.md)   
 [CREATE DATABASE AUDIT SPECIFICATION (Transact-SQL)](../../t-sql/statements/create-database-audit-specification-transact-sql.md)   
 [ALTER DATABASE AUDIT SPECIFICATION &#40;Transact-SQL&#41;](../../t-sql/statements/alter-database-audit-specification-transact-sql.md)   
 [DROP DATABASE AUDIT SPECIFICATION &#40;Transact-SQL&#41;](../../t-sql/statements/drop-database-audit-specification-transact-sql.md)   
 [ALTER AUTHORIZATION &#40;Transact-SQL&#41;](../../t-sql/statements/alter-authorization-transact-sql.md)   
 [sys.fn_get_audit_file &#40;Transact-SQL&#41;](../../relational-databases/system-functions/sys-fn-get-audit-file-transact-sql.md)   
 [sys.server_audits &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-server-audits-transact-sql.md)   
 [sys.server_file_audits &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-server-file-audits-transact-sql.md)   
 [sys.server_audit_specifications &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-server-audit-specifications-transact-sql.md)   
 [sys.server_audit_specification_details &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-server-audit-specification-details-transact-sql.md)   
 [sys.database_audit_specifications &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-database-audit-specifications-transact-sql.md)   
 [sys.database_audit_specification_details &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-database-audit-specification-details-transact-sql.md)   
 [sys.dm_server_audit_status &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-server-audit-status-transact-sql.md)   
 [sys.dm_audit_actions &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-audit-actions-transact-sql.md)   
 [サーバー監査およびサーバー監査の仕様を作成する](../../relational-databases/security/auditing/create-a-server-audit-and-server-audit-specification.md)  
  
  
