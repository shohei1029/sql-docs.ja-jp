---
description: sp_rename (Transact-sql)
title: sp_rename (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 10/14/2020
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- sp_rename_TSQL
- sp_rename
dev_langs:
- TSQL
helpviewer_keywords:
- renaming indexes
- renaming columns
- sp_rename
- renaming tables
ms.assetid: bc3548f0-143f-404e-a2e9-0a15960fc8ed
author: markingmyname
ms.author: maghan
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current||=azure-sqldw-latest
ms.openlocfilehash: 2e24d74b8c2d2d3e661ab5169ae800e99af42f68
ms.sourcegitcommit: bf7577b3448b7cb0e336808f1112c44fa18c6f33
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104610843"
---
# <a name="sp_rename-transact-sql"></a>sp_rename (Transact-sql)
[!INCLUDE [sql-asdb-asa](../../includes/applies-to-version/sql-asdb-asa.md)]

  現在のデータベース内のユーザーが作成したオブジェクトの名前を変更します。 このオブジェクトには、テーブル、インデックス、列、別名データ型、または [!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[dnprdnshort](../../includes/dnprdnshort-md.md)] 共通言語ランタイム (CLR) ユーザー定義型を使用できます。  
  
> [!NOTE]
> で [!INCLUDE[ssazuresynapse](../../includes/ssazuresynapse_md.md)] は、sp_rename は **プレビュー** 段階であり、 **dbo** スキーマ内のユーザーオブジェクトの列の名前を変更する場合にのみ使用できます。

> [!CAUTION]  
>  オブジェクト名の一部または全部を変更すると、スクリプトおよびストアド プロシージャが壊れる可能性があります。 ストアド プロシージャ、トリガー、ユーザー定義関数、またはビューの名前を変更する場合は、このステートメントを使用しないことをお勧めします。代わりに、オブジェクトを削除して新しい名前で再作成してください。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```sql  
-- Transact-SQL Syntax for sp_rename in SQL Server and Azure SQL Database
sp_rename [ @objname = ] 'object_name' , [ @newname = ] 'new_name'   
    [ , [ @objtype = ] 'object_type' ]   
```  

```sql  
-- Transact-SQL Syntax for sp_rename (preview) in Azure Synapse Analytics
sp_rename [ @objname = ] 'object_name' , [ @newname = ] 'new_name'   
    , [ @objtype = ] 'COLUMN'   
``` 
  
## <a name="arguments"></a>引数  
 [ @objname =] '*object_name*'  
 ユーザーオブジェクトまたはデータ型の現在の修飾名または修飾され名を指定します。 名前を変更するオブジェクトがテーブル内の列である場合は、 *object_name* の形式である必要があります *。 column また* は schema. *テーブル* です。 名前を変更するオブジェクトがインデックスの場合、 *object_name* *テーブル* の形式である必要があります。インデックスまたは *スキーマ。* 名前を変更するオブジェクトが制約の場合、 *object_name* は *schema. 制約* の形式である必要があります。  
  
 引用符は、修飾オブジェクトを指定する場合のみ必要です。 データベース名を含む完全修飾名を指定する場合は、データベース名を現在のデータベースの名前にする必要があります。 *object_name* は **nvarchar (776)**,、既定値はありません。  
  
 [ @newname =] '*new_name*'  
 指定したオブジェクトの新しい名前を指定します。 *new_name* は、1部構成の名前である必要があり、識別子の規則に従っている必要があります。 *newname* は **sysname**,、既定値はありません。  
  
> [!NOTE]  
>  トリガー名の先頭に # または ## は使用できません。  
  
 [ @objtype =] '*object_type*'  
 名前を変更するオブジェクトの種類を指定します。 *object_type* は **varchar (13)**,、既定値は NULL の場合、これらの値のいずれかを指定できます。  
  
|値|説明|  
|-----------|-----------------|  
|COLUMN|名前を変更する列。|  
|DATABASE|ユーザー定義データベース。 このオブジェクトの種類は、データベースの名前を変更するときに必要です。|  
|INDEX|ユーザー定義インデックス。 統計を使用してインデックスの名前を変更すると、統計の名前も自動的に変更されます。|  
|OBJECT|[Sys. オブジェクト](../../relational-databases/system-catalog-views/sys-objects-transact-sql.md)で追跡される型の項目。 たとえば、OBJECT を使用して、制約 (CHECK、FOREIGN KEY、PRIMARY/UNIQUE KEY)、ユーザー テーブル、ルールなどのオブジェクトの名前を変更できます。|  
|STATISTICS|**適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降と [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。<br /><br /> ユーザーによって明示的に作成された、またはインデックスを使用して暗黙的に作成された統計。 インデックスの統計の名前を変更すると、インデックスの名前も自動的に変更されます。|  
|USERDATATYPE|[CREATE TYPE](../../t-sql/statements/create-type-transact-sql.md)または[sp_addtype](../../relational-databases/system-stored-procedures/sp-addtype-transact-sql.md)を実行して追加された[CLR ユーザー定義型](../../relational-databases/clr-integration-database-objects-user-defined-types/clr-user-defined-types.md)。|  
  
[ @objtype =] '*COLUMN*' **が適用さ** れます: Azure Synapse Analytics  
の sp_rename (プレビュー) では [!INCLUDE[ssazuresynapse](../../includes/ssazuresynapse_md.md)] 、 *列* は、名前を変更するオブジェクトの種類が列であることを指定する必須パラメーターです。 これは **varchar (13)** であり、既定値はありません。常に sp_rename (プレビュー) ステートメントに含める必要があります。 列が非ディストリビューション列である場合にのみ、列の名前を変更できます。

## <a name="return-code-values"></a>リターン コードの値  
 0 (成功) または0以外の数値 (失敗)  
  
## <a name="remarks"></a>解説  
**適用対象** SQL Server (サポートされているすべてのバージョン) と Azure SQL Database  
 sp_rename では、PRIMARY KEY (主キー) または UNIQUE (一意) 制約の名前を変更した場合、関連するインデックスの名前も自動的に変更されます。 名前を変更したインデックスが PRIMARY KEY 制約に関連付けられている場合、PRIMARY KEY 制約も sp_rename によって自動的に名前が変更されます。  

**適用対象** SQL Server (サポートされているすべてのバージョン) と Azure SQL Database  
 sp_rename は、プライマリおよびセカンダリ XML インデックスの名前を変更する場合に使用できます。  
  
**適用対象** SQL Server (サポートされているすべてのバージョン) と Azure SQL Database  
 ストアドプロシージャ、関数、ビュー、またはトリガーの名前を変更しても、対応するオブジェクトの名前は、 [sys.sql_modules](../../relational-databases/system-catalog-views/sys-sql-modules-transact-sql.md) カタログビューの定義列、または [OBJECT_DEFINITION](../../t-sql/functions/object-definition-transact-sql.md) の組み込み関数を使用して取得されます。 したがって、これらのオブジェクトの種類の名前を変更する場合は、sp_rename を使用しないことをお勧めします。 代わりに、オブジェクトを削除して新しい名前で再作成してください。  

**適用対象** SQL Server (サポートされているすべてのバージョン)、Azure SQL Database、Azure Synapse Analytics  
 テーブルや列などのオブジェクトの名前を変更しても、そのオブジェクトへの参照の名前は自動的に変更されません。 名前が変更されたオブジェクトを参照するオブジェクトは、手動で変更する必要があります。 たとえば、テーブルの列の名前を変更するとき、その列がトリガーで参照されている場合は、新しい列名が反映されるようにトリガーに変更を加える必要があります。 オブジェクトの名前を変更する前には、 [sys.sql_expression_dependencies](../../relational-databases/system-catalog-views/sys-sql-expression-dependencies-transact-sql.md) を使ってオブジェクトの従属関係を一覧表示できます。  

**適用対象** SQL Server (サポートされているすべてのバージョン)、Azure SQL Database、Azure Synapse Analytics  
 現在のデータベースでのみ、オブジェクトまたはデータ型の名前を変更できます。 ほとんどのシステムデータ型とシステムオブジェクトの名前は変更できません。  
  
## <a name="permissions"></a>アクセス許可  
 オブジェクト、列、およびインデックスの名前を変更するには、オブジェクトに対する ALTER 権限が必要です。 ユーザー定義型の名前を変更するには、その型に対する CONTROL 権限が必要です。 データベースの名前を変更するには、sysadmin 固定サーバー ロールまたは dbcreator 固定サーバー ロールのメンバーシップが必要です。  
  
## <a name="examples"></a>例  
  
### <a name="a-renaming-a-table"></a>A. テーブル名の変更  
 次の例では、`Sales` スキーマの `SalesTerritory` テーブルの名前を `SalesTerr` に変更します。  
  
```sql  
USE AdventureWorks2012;  
GO  
EXEC sp_rename 'Sales.SalesTerritory', 'SalesTerr';  
GO  
```  
  
### <a name="b-renaming-a-column"></a>B. 列の名前を変更する  
 次の例では、テーブルの列の名前 `TerritoryID` `SalesTerritory` をに変更 `TerrID` します。  
  
```sql  
USE AdventureWorks2012;  
GO  
EXEC sp_rename 'Sales.SalesTerritory.TerritoryID', 'TerrID', 'COLUMN';  
GO  
```  
  
### <a name="c-renaming-an-index"></a>C. インデックス名を変更する  
 次の例では、インデックスの名前 `IX_ProductVendor_VendorID` をに変更 `IX_VendorID` します。  
  
```sql  
USE AdventureWorks2012;  
GO  
EXEC sp_rename N'Purchasing.ProductVendor.IX_ProductVendor_VendorID', N'IX_VendorID', N'INDEX';  
GO  
```  
  
### <a name="d-renaming-an-alias-data-type"></a>D. 別名データ型の名前を変更する  
 次の例では、 `Phone` 別名データ型の名前をに変更し `Telephone` ます。  
  
```sql  
USE AdventureWorks2012;  
GO  
EXEC sp_rename N'Phone', N'Telephone', N'USERDATATYPE';  
GO  
```  
  
### <a name="e-renaming-constraints"></a>E. 制約の名前変更  
 次の例では、PRIMARY KEY 制約、CHECK 制約、および FOREIGN KEY 制約の名前を変更します。 制約の名前を変更する際は、制約が属するスキーマを指定する必要があります。  
  
```sql  
USE AdventureWorks2012;   
GO  
-- Return the current Primary Key, Foreign Key and Check constraints for the Employee table.  
SELECT name, SCHEMA_NAME(schema_id) AS schema_name, type_desc  
FROM sys.objects  
WHERE parent_object_id = (OBJECT_ID('HumanResources.Employee'))   
AND type IN ('C','F', 'PK');   
GO  
  
-- Rename the primary key constraint.  
sp_rename 'HumanResources.PK_Employee_BusinessEntityID', 'PK_EmployeeID';  
GO  
  
-- Rename a check constraint.  
sp_rename 'HumanResources.CK_Employee_BirthDate', 'CK_BirthDate';  
GO  
  
-- Rename a foreign key constraint.  
sp_rename 'HumanResources.FK_Employee_Person_BusinessEntityID', 'FK_EmployeeID';  
  
-- Return the current Primary Key, Foreign Key and Check constraints for the Employee table.  
SELECT name, SCHEMA_NAME(schema_id) AS schema_name, type_desc  
FROM sys.objects  
WHERE parent_object_id = (OBJECT_ID('HumanResources.Employee'))   
AND type IN ('C','F', 'PK');  
  
```  
  
```  
  
name                                  schema_name        type_desc  
------------------------------------- ------------------ ----------------------  
FK_Employee_Person_BusinessEntityID   HumanResources     FOREIGN_KEY_CONSTRAINT  
PK_Employee_BusinessEntityID          HumanResources     PRIMARY_KEY_CONSTRAINT  
CK_Employee_BirthDate                 HumanResources     CHECK_CONSTRAINT  
CK_Employee_MaritalStatus             HumanResources     CHECK_CONSTRAINT  
CK_Employee_HireDate                  HumanResources     CHECK_CONSTRAINT  
CK_Employee_Gender                    HumanResources     CHECK_CONSTRAINT  
CK_Employee_VacationHours             HumanResources     CHECK_CONSTRAINT  
CK_Employee_SickLeaveHours            HumanResources     CHECK_CONSTRAINT  
  
(7 row(s) affected)  
  
name                                  schema_name        type_desc  
------------------------------------- ------------------ ----------------------  
FK_Employee_ID                        HumanResources     FOREIGN_KEY_CONSTRAINT  
PK_Employee_ID                        HumanResources     PRIMARY_KEY_CONSTRAINT  
CK_BirthDate                          HumanResources     CHECK_CONSTRAINT  
CK_Employee_MaritalStatus             HumanResources     CHECK_CONSTRAINT  
CK_Employee_HireDate                  HumanResources     CHECK_CONSTRAINT  
CK_Employee_Gender                    HumanResources     CHECK_CONSTRAINT  
CK_Employee_VacationHours             HumanResources     CHECK_CONSTRAINT  
CK_Employee_SickLeaveHours            HumanResources     CHECK_CONSTRAINT  
  
(7 row(s) affected)  
  
```  
  
### <a name="f-renaming-statistics"></a>F. 統計の名前を変更する  
 次の例では、contactMail1 という名前の統計オブジェクトを作成し、sp_rename を使用して、その統計の名前を NewContact に変更します。 統計の名前を変更する場合は、schema.table.statistics_name の形式でオブジェクトを指定する必要があります。  
  
```sql  
CREATE STATISTICS ContactMail1  
    ON Person.Person (BusinessEntityID, EmailPromotion)  
    WITH SAMPLE 5 PERCENT;  
  
sp_rename 'Person.Person.ContactMail1', 'NewContact','Statistics';  
  
```  

## <a name="examples-sssdwfull"></a>例: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)]
### <a name="g-renaming-a-column"></a>G. 列の名前を変更する  
 次の例では、テーブルの列の名前 `c1` `table1` をに変更 `col1` します。 

> [!NOTE]
> この [!INCLUDE[ssazuresynapse](../../includes/ssazuresynapse_md.md)] 機能はまだプレビュー段階であり、現在は **dbo** スキーマ内のオブジェクトに対してのみ使用できます。 
  
```sql  
CREATE TABLE table1 (c1 INT, c2 INT);
EXEC sp_rename 'table1.c1', 'col1', 'COLUMN';
GO  
```  
  
## <a name="see-also"></a>参照  
 [sys.sql_expression_dependencies &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-sql-expression-dependencies-transact-sql.md)   
 [sys.sql_modules &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-sql-modules-transact-sql.md)   
 [システム ストアド プロシージャ &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/system-stored-procedures-transact-sql.md)   
 [Transact-sql&#41;&#40;のストアドプロシージャのデータベースエンジン ](../../relational-databases/system-stored-procedures/database-engine-stored-procedures-transact-sql.md)  
  
  
