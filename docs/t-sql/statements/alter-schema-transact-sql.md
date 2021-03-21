---
description: ALTER SCHEMA (Transact-SQL)
title: ALTER SCHEMA (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/09/2020
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- ALTER SCHEMA
- ALTER_SCHEMA_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- objects [SQL Server], transferring
- transferring objects between schemas
- ALTER SCHEMA statement
- schemas [SQL Server], modifying
- modifying schemas
ms.assetid: 0a760138-460e-410a-a3c1-d60af03bf2ed
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 49d0136858544c10c73c06060fca9155b2634d18
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104742992"
---
# <a name="alter-schema-transact-sql"></a>ALTER SCHEMA (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  スキーマ間でセキュリティ保護可能なリソースを移動します。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql
-- Syntax for SQL Server and Azure SQL Database  
  
ALTER SCHEMA schema_name   
   TRANSFER [ <entity_type> :: ] securable_name   
[;]  
  
<entity_type> ::=  
    {  
    Object | Type | XML Schema Collection  
    }  
```  
  
```syntaxsql
-- Syntax for Azure Synapse Analytics and Parallel Data Warehouse  
  
ALTER SCHEMA schema_name   
   TRANSFER [ OBJECT :: ] securable_name   
[;]  
```  
  

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
 *schema_name*  
 セキュリティ保護可能なリソースの移動先である、現在のデータベース内にあるスキーマの名前です。 SYS または INFORMATION_SCHEMA は指定できません。  
  
 \<entity_type>  
 所有者を変更するエンティティのクラスを指定します。 既定値はオブジェクトです。  
  
 *securable_name*  
 移動元のスキーマに含まれているセキュリティ保護可能なリソースの名前を指定します。このリソースが対象のスキーマに移動されます。名前を指定するときは、1 つまたは 2 つの要素で構成される名前を使用します。  
  
## <a name="remarks"></a>解説  
 ユーザーとスキーマは完全に分離されています。  
  
 ALTER SCHEMA は、セキュリティ保護可能なリソースを同じデータベース内のスキーマ間で移動する場合にのみ使用できます。 スキーマ内のセキュリティ保護可能なリソースを変更または削除するには、そのリソースに固有の ALTER または DROP ステートメントを使用します。  
  
 *securable_name* に 1 つの要素から構成される名前を使用した場合、セキュリティ保護可能なリソースを特定するために、現在有効な名前解決規則が使用されます。  
  
 セキュリティ保護可能なリソースに関連付けられているすべての権限は、リソースが新しいスキーマに移動したときに削除されます。 セキュリティ保護可能なリソースの所有者が明示的に設定されている場合、所有者は変更されません。 セキュリティ保護可能なリソースの所有者が SCHEMA OWNER に設定されている場合、所有者は SCHEMA OWNER のままですが、移動後、SCHEMA OWNER は新しいスキーマの所有者に解決されます。 新しい所有者の principal_id は NULL になります。  
  
 ストアド プロシージャ、関数、ビュー、またはトリガーを移動しても、[sys.sql_modules](../../relational-databases/system-catalog-views/sys-sql-modules-transact-sql.md) カタログ ビューの definition 列にある対応するオブジェクト、または [OBJECT_DEFINITION](../../t-sql/functions/object-definition-transact-sql.md) 組み込み関数を使用して取得されたオブジェクトのスキーマ名は変更されません。 したがって、これらのオブジェクトの種類を移動する場合は、ALTER SCHEMA を使用しないことをお勧めします。 代わりに、オブジェクトを削除して新しいスキーマで再作成してください。  
  
 テーブルやシノニムなどのオブジェクトを移動しても、そのオブジェクトに対する参照は自動的には更新されません。 移動したオブジェクトを参照しているオブジェクトに対しては、手動で変更を加える必要があります。 たとえば、テーブルを移動するとき、そのテーブルがトリガーで参照されている場合は、新しいスキーマ名が反映されるようにトリガーに変更を加える必要があります。 オブジェクトを移動する前には、[sys.sql_expression_dependencies](../../relational-databases/system-catalog-views/sys-sql-expression-dependencies-transact-sql.md) を使ってオブジェクトの従属関係を一覧表示できます。  

 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] を使用してテーブルのスキーマを変更するには、オブジェクト エクスプローラーでテーブルを右クリックし、 **[デザイン]** をクリックします。 **F4** キーを押して [プロパティ] ウィンドウを開きます。 **[スキーマ]** ボックスで新しいスキーマを選択します。  
 
 ALTER SCHEMA では、スキーマ レベル ロックが使用されます。
  
> [!CAUTION]  
>  [!INCLUDE[ssCautionUserSchema](../../includes/sscautionuserschema-md.md)]  
  
## <a name="permissions"></a>アクセス許可  
 別のスキーマからセキュリティ保護可能なリソースを移動する場合、現在のユーザーには、(スキーマではなく) セキュリティ保護可能なリソースに対する CONTROL 権限とターゲット スキーマに対する ALTER 権限が必要です。  
  
 セキュリティ保護可能なリソースに EXECUTE AS OWNER の指定があり、所有者が SCHEMA OWNER に設定されている場合は、対象スキーマの所有者に対する IMPERSONATE 権限も必要です。  
  
 移動するセキュリティ保護可能なリソースに関連付けられているすべての権限は、移動時に削除されます。  
  
## <a name="examples"></a>例  
  
### <a name="a-transferring-ownership-of-a-table"></a>A. テーブルの所有権を譲渡する  
 次の例では、テーブル `Address` をスキーマ `Person` からスキーマ "HumanResources" に移動することで、スキーマ `HumanResources` を変更します。  
  
```sql  
USE AdventureWorks2012;  
GO  
ALTER SCHEMA HumanResources TRANSFER Person.Address;  
GO  
```  
  
### <a name="b-transferring-ownership-of-a-type"></a>B. 型の所有権を譲渡する  
 次の例では、`Production` スキーマに型を作成し、その型を `Person` スキーマに譲渡します。  
  
```sql  
USE AdventureWorks2012;  
GO  
  
CREATE TYPE Production.TestType FROM [VARCHAR](10) NOT NULL ;  
GO  
  
-- Check the type owner.  
SELECT sys.types.name, sys.types.schema_id, sys.schemas.name  
    FROM sys.types JOIN sys.schemas   
        ON sys.types.schema_id = sys.schemas.schema_id   
    WHERE sys.types.name = 'TestType' ;  
GO  
  
-- Change the type to the Person schema.  
ALTER SCHEMA Person TRANSFER type::Production.TestType ;  
GO  
  
-- Check the type owner.  
SELECT sys.types.name, sys.types.schema_id, sys.schemas.name  
    FROM sys.types JOIN sys.schemas   
        ON sys.types.schema_id = sys.schemas.schema_id   
    WHERE sys.types.name = 'TestType' ;  
GO  
```  
  
## <a name="examples-sssdwfull-and-sspdw"></a>例: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)]、[!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="c-transferring-ownership-of-a-table"></a>C. テーブルの所有権を譲渡する  
 次の例では、`dbo` スキーマで `Region` テーブルを作成し、`Sales` スキーマを作成し、`Region` テーブルを `dbo` スキーマから `Sales` スキーマに移動します。  
  
```sql  
CREATE TABLE dbo.Region   
    (Region_id INT NOT NULL,  
    Region_Name CHAR(5) NOT NULL)  
WITH (DISTRIBUTION = REPLICATE);  
GO  
  
CREATE SCHEMA Sales;  
GO  
  
ALTER SCHEMA Sales TRANSFER OBJECT::dbo.Region;  
GO  
```  
  
## <a name="see-also"></a>参照  
 [CREATE SCHEMA &#40;Transact-SQL&#41;](../../t-sql/statements/create-schema-transact-sql.md)   
 [DROP SCHEMA &#40;Transact-SQL&#41;](../../t-sql/statements/drop-schema-transact-sql.md)   
 [EVENTDATA &#40;Transact-SQL&#41;](../../t-sql/functions/eventdata-transact-sql.md)  
  
  

