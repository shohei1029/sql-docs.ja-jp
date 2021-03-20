---
description: DROP TABLE (Transact-SQL)
title: DROP TABLE (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 05/12/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- DROP_TABLE_TSQL
- DROP TABLE
dev_langs:
- TSQL
helpviewer_keywords:
- removing indexes
- table removal [SQL Server]
- deleting indexes
- dropping data
- deleting tables
- dropping indexes
- removing constraints
- removing permissions
- DROP TABLE statement
- removing tables
- deleting triggers
- removing data
- dropping tables
- deleting permissions
- dropping triggers
- deleting constraints
- removing triggers
- deleting data
- dropping constraints
- dropping permissions
ms.assetid: 0b6f2b6f-3aa3-4767-943f-43df3c3c5cfd
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 743ea71b7ca136c4fe410646c52df84a9799ba62
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104752702"
---
# <a name="drop-table-transact-sql"></a>DROP TABLE (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  1 つ以上のテーブルの定義、およびそれらのテーブルのすべてのデータ、インデックス、トリガー、制約、権限の仕様を削除します。 削除されたテーブルを参照しているすべてのビューとストアド プロシージャは、[DROP VIEW](../../t-sql/statements/drop-view-transact-sql.md) および [DROP PROCEDURE](../../t-sql/statements/drop-procedure-transact-sql.md) を使用して明示的に削除する必要があります。 テーブルへの依存関係を報告するには [sys.dm_sql_referencing_entities](../../relational-databases/system-dynamic-management-views/sys-dm-sql-referencing-entities-transact-sql.md) を使用します。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql
-- Syntax for SQL Server and Azure SQL Database  
  
DROP TABLE [ IF EXISTS ] { database_name.schema_name.table_name | schema_name.table_name | table_name } [ ,...n ]  
[ ; ]  
```  
  
```syntaxsql
-- Syntax for Azure Synapse Analytics and Parallel Data Warehouse  
  
DROP TABLE { database_name.schema_name.table_name | schema_name.table_name | table_name }
[;]  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
 *database_name*  
 テーブルが作成されたデータベースの名前を指定します。  
  
 Azure SQL Database では、database_name が現在のデータベースの場合、または database_name が tempdb で、object_name が # で始まる場合に、3 つの要素で構成された名前形式 database_name.[schema_name].object_name がサポートされます。 Azure SQL Database では、4 つの要素で構成された名前はサポートされません。  
  
 *IF EXISTS*  
 **適用対象**: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] から [現在のバージョン](/troubleshoot/sql/general/determine-version-edition-update-level)まで)。  
  
 条件付きでは既に存在する場合にのみ、テーブルを削除します。  
  
 *schema_name*  
 テーブルが所属するスキーマの名前を指定します。  
  
 *table_name*  
 削除するテーブル名を指定します。  
  
## <a name="remarks"></a>注釈  
 DROP TABLE を使用して、FOREIGN KEY 制約によって参照されているテーブルを削除することはできません。 まず、参照している FOREIGN KEY 制約または参照テーブルを削除する必要があります。 参照しているテーブルと、主キーを格納しているテーブルの両方を同じ DROP TABLE ステートメントで削除する場合には、参照しているテーブルを先に指定する必要があります。  
  
 任意のデータベースから、複数のテーブルを削除することができます。 削除するテーブルが、同時に削除される別のテーブルの主キーを参照している場合には、外部キーを持つ参照元のテーブルを、参照されている主キーを持つテーブルよりも前に指定する必要があります。  
  
 テーブルを削除すると、そのテーブルのルールや既定値はバインドを失い、そのテーブルに関係付けられている制約やトリガーも自動的に削除されます。 テーブルを再作成する場合は、適切なルールや既定値を再バインドし、トリガーを再作成し、必要なすべての制約を追加する必要があります。  
  
 DELETE *tablename* や、TRUNCATE TABLE ステートメントを使用して、テーブルのすべての行を削除しても、そのテーブルは、テーブル自体を削除しない限り存在します。  
  
 128 個を超えるエクステントを使用する大きなテーブルやインデックスは、論理フェーズと物理フェーズの 2 段階で削除します。 論理フェーズでは、そのテーブルが使用している既存のアロケーション ユニットに割り当て解除のマークが付き、トランザクションがコミットするまでロックされます。 物理フェーズでは、割り当て解除のマークが付いた IAM ページが、バッチで物理的に削除されます。  
  
 FILESTREAM 属性が指定されている VARBINARY(MAX) 列を含むテーブルを削除しても、ファイル システムに保存されているデータは削除されません。  
  
> [!IMPORTANT]  
>  DROP TABLE と CREATE TABLE を同じバッチ内の同じテーブルに対して実行しないでください。 実行した場合、予期しないエラーが発生する可能性があります。  
  
## <a name="permissions"></a>アクセス許可  
 テーブルが属するスキーマに対する ALTER 権限、テーブルに対する CONTROL 権限、または **db_ddladmin** 固定データベース ロールのメンバーシップが必要です。  
  
## <a name="examples"></a>例  
  
### <a name="a-dropping-a-table-in-the-current-database"></a>A. 現在のデータベース内のテーブルを削除する  
 次の例では、現在のデータベースから、`ProductVendor1` テーブルとそのデータおよびインデックスを削除します。  
  
```sql  
DROP TABLE ProductVendor1 ;  
```  
  
### <a name="b-dropping-a-table-in-another-database"></a>B. 他のデータベースのテーブルを削除する  
 次の例では、[!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)] データベースにある `SalesPerson2` テーブルを削除します。 この例は、サーバー インスタンス上にあるどのデータベースからでも実行できます。  
  
```sql  
DROP TABLE AdventureWorks2012.dbo.SalesPerson2 ;  
```  
  
### <a name="c-dropping-a-temporary-table"></a>C. 一時テーブルを削除する  
 次の例では、一時テーブルを作成して、その存在テストを行います。さらに、このテーブルを削除して、再度存在テストを行います。 この例では、[!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 以降で使用できる **IF EXISTS** 構文は使用しません。  
  
```sql  
CREATE TABLE #temptable (col1 INT);  
GO  
INSERT INTO #temptable  
VALUES (10);  
GO  
SELECT * FROM #temptable;  
GO  
IF OBJECT_ID(N'tempdb..#temptable', N'U') IS NOT NULL   
DROP TABLE #temptable;  
GO  
--Test the drop.  
SELECT * FROM #temptable;  
```  
  
### <a name="d-dropping-a-table-using-if-exists"></a>D. IF EXISTS を使用してテーブルを削除する  
  
**適用対象**: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] から [現在のバージョン](/troubleshoot/sql/general/determine-version-edition-update-level)まで)。  
  
 次の例では、T1 のという名前のテーブルを作成します。 2 番目のステートメントで、テーブルを削除します。 3 番目のステートメントでは、テーブルがすでに削除されているため、何も操作が実行されませんが、エラーは発生しません。  
  
```sql  
CREATE TABLE T1 (Col1 INT);  
GO  
DROP TABLE IF EXISTS T1;  
GO  
DROP TABLE IF EXISTS T1;  
```  
  
  
## <a name="see-also"></a>参照  
 [ALTER TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-table-transact-sql.md)   
 [CREATE TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/create-table-transact-sql.md)   
 [DELETE &#40;Transact-SQL&#41;](../../t-sql/statements/delete-transact-sql.md)   
 [sp_help &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-help-transact-sql.md)   
 [sp_spaceused &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-spaceused-transact-sql.md)   
 [TRUNCATE TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/truncate-table-transact-sql.md)   
 [DROP VIEW &#40;Transact-SQL&#41;](../../t-sql/statements/drop-view-transact-sql.md)   
 [DROP PROCEDURE &#40;Transact-SQL&#41;](../../t-sql/statements/drop-procedure-transact-sql.md)   
 [EVENTDATA &#40;Transact-SQL&#41;](../../t-sql/functions/eventdata-transact-sql.md)   
 [sys.sql_expression_dependencies &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-sql-expression-dependencies-transact-sql.md)