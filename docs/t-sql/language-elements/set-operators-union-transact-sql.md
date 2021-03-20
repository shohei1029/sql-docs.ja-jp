---
description: セット演算子 - UNION (Transact-SQL)
title: UNION (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 08/07/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- UNION
- UNION_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- UNION queries
- combining query results
- UNION operator [SQL Server]
ms.assetid: 607c296f-8a6a-49bc-975a-b8d0c0914df7
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 2d723460752835242646a926b2b0ee2cba9cb28b
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104733666"
---
# <a name="set-operators---union-transact-sql"></a>セット演算子 - UNION (Transact-SQL)

[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

2 つのクエリの結果を、単一の結果セットに連結します。 結果セットに重複する行が含まれるかどうかを制御します。

- **UNION ALL** - 重複が含まれます。
- **UNION** - 重複が除外されます。

**UNION** 操作は **[JOIN](../queries/from-transact-sql.md)** とは異なります。

- **UNION** では、2 つのクエリからの結果セットが連結されます。 ただし、**UNION** では、2 つのテーブルから収集された列から個々の行が作成されるわけではありません。
- **JOIN** では、2 つのテーブルの列を比較して、2 つのテーブルの列で構成される結果行を作成します。
  
**UNION** を使用して 2 つのクエリの結果セットを結合するための基本的な規則を以下に示します。  
  
-   列の数と順番は、すべてのクエリで同じであること  
  
-   データ型に互換性があること  
  
![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql
{ <query_specification> | ( <query_expression> ) }   
{ UNION [ ALL ]   
  { <query_specification> | ( <query_expression> ) } 
  [ ...n ] }
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
\<query_specification> | ( \<query_expression> ) 別のクエリ仕様またはクエリ式からのデータと結合するデータを返すクエリ仕様またはクエリ式。 UNION 操作の一部である列の定義は同じである必要はありませんが、暗黙的な変換により一致させる必要があります。 データ型が異なるとき、最終的なデータ型は[データ型の優先順位](../../t-sql/data-types/data-type-precedence-transact-sql.md)ルールに基づいて決定されます。 型は同じだが、有効桁数、小数点以下桁数、または長さが異なる場合、結果は式の結合と同じルールに基づいて決定されます。 詳しくは、「[有効桁数、小数点以下桁数、および長さ (Transact-SQL)](../../t-sql/data-types/precision-scale-and-length-transact-sql.md)」をご覧ください。  
  
**xml** データ型の列は同じである必要があります。 すべての列が XML スキーマに型指定されているか、型指定されていない必要があります。 型指定されている場合は、同じ XML スキーマ コレクションに従って型指定されている必要があります。  
  
UNION  
複数の結果セットを結合し、1 つの結果セットとして返すことを指定します。  
  
ALL  
重複も含めて、すべての行が結果セットに組み込まれます。 指定しない場合、重複する行は削除されます。  
  
## <a name="examples"></a>例  
  
### <a name="a-using-a-simple-union"></a>A. 単純な UNION を使用する  
次の例では、結果セットに `ProductModelID` テーブルと `Name` テーブルの `ProductModel` 列と `Gloves` 列の内容が含まれています。  
 
```sql
-- Uses AdventureWorks  
  
IF OBJECT_ID ('dbo.Gloves', 'U') IS NOT NULL  
DROP TABLE dbo.Gloves;  
GO  
-- Create Gloves table.  
SELECT ProductModelID, Name  
INTO dbo.Gloves  
FROM Production.ProductModel  
WHERE ProductModelID IN (3, 4);  
GO  
  
-- Here is the simple union.  
-- Uses AdventureWorks  
  
SELECT ProductModelID, Name  
FROM Production.ProductModel  
WHERE ProductModelID NOT IN (3, 4)  
UNION  
SELECT ProductModelID, Name  
FROM dbo.Gloves  
ORDER BY Name;  
GO  
```  
  
### <a name="b-using-select-into-with-union"></a>B. UNION と共に SELECT INTO を使用する  
次の例では、2 番目の `SELECT` ステートメントの `INTO` 句で、`ProductModel` テーブルと `Gloves` テーブルから選択された列の UNION の最終的な結果セットを `ProductResults` という名前のテーブルに格納することを指定します。 `Gloves` テーブルは、最初の `SELECT` ステートメントで作成されます。  
  
```sql
-- Uses AdventureWorks  
  
IF OBJECT_ID ('dbo.ProductResults', 'U') IS NOT NULL  
DROP TABLE dbo.ProductResults;  
GO  
IF OBJECT_ID ('dbo.Gloves', 'U') IS NOT NULL  
DROP TABLE dbo.Gloves;  
GO  
-- Create Gloves table.  
SELECT ProductModelID, Name  
INTO dbo.Gloves  
FROM Production.ProductModel  
WHERE ProductModelID IN (3, 4);  
GO  
  
-- Uses AdventureWorks  
  
SELECT ProductModelID, Name  
INTO dbo.ProductResults  
FROM Production.ProductModel  
WHERE ProductModelID NOT IN (3, 4)  
UNION  
SELECT ProductModelID, Name  
FROM dbo.Gloves;  
GO  
  
SELECT ProductModelID, Name   
FROM dbo.ProductResults;  
```  
  
### <a name="c-using-union-of-two-select-statements-with-order-by"></a>C. ORDER BY 句を指定した 2 つの SELECT ステートメントで UNION 句を使用する  
UNION 句で使用するある種のパラメーターの順序には重要な意味があります。 次の例では、出力時に列名を変更する 2 つの `SELECT` ステートメントでの `UNION` の誤った使用法と正しい使用法を示しています。  
  
```sql
-- Uses AdventureWorks  
  
IF OBJECT_ID ('dbo.Gloves', 'U') IS NOT NULL  
DROP TABLE dbo.Gloves;  
GO  
-- Create Gloves table.  
SELECT ProductModelID, Name  
INTO dbo.Gloves  
FROM Production.ProductModel  
WHERE ProductModelID IN (3, 4);  
GO  
  
/* INCORRECT */  
-- Uses AdventureWorks  
  
SELECT ProductModelID, Name  
FROM Production.ProductModel  
WHERE ProductModelID NOT IN (3, 4)  
ORDER BY Name  
UNION  
SELECT ProductModelID, Name  
FROM dbo.Gloves;  
GO  
  
/* CORRECT */  
-- Uses AdventureWorks  
  
SELECT ProductModelID, Name  
FROM Production.ProductModel  
WHERE ProductModelID NOT IN (3, 4)  
UNION  
SELECT ProductModelID, Name  
FROM dbo.Gloves  
ORDER BY Name;  
GO  
```  
  
### <a name="d-using-union-of-three-select-statements-to-show-the-effects-of-all-and-parentheses"></a>D. 3 つの SELECT ステートメントで UNION を使用して、ALL とかっこの効果を示す  
次の例では、`UNION` を使用して 3 つのテーブルのクエリ結果を結合します。これらのテーブルはすべて同じ 5 行のデータで構成されます。 最初の例では、`UNION ALL` を使用して、重複するレコードも含めて 15 行すべてを返します。 2 番目の例では、`ALL` を指定せずに `UNION` を使用して、3 つの `SELECT` ステートメントの結果を結合したものから重複する行を削除し、5 行を返します。  
  
3 番目の例では、最初の `UNION` と共に `ALL` を使用し、`ALL` を使用していない 2 番目の `UNION` をかっこで囲んでいます。 2 番目の `UNION` はかっこで囲まれているので、最初に処理されます。また、`ALL` オプションが使用されず、重複が削除されるため、5 行が返されます。 これらの 5 行は、`UNION ALL` キーワードを使用して最初の `SELECT` の結果と結合されます。 この例では、5 行で構成される 2 つのセットの重複は削除されません。 最終的な結果は 10 行になります。  
  
```sql
-- Uses AdventureWorks  
  
IF OBJECT_ID ('dbo.EmployeeOne', 'U') IS NOT NULL  
DROP TABLE dbo.EmployeeOne;  
GO  
IF OBJECT_ID ('dbo.EmployeeTwo', 'U') IS NOT NULL  
DROP TABLE dbo.EmployeeTwo;  
GO  
IF OBJECT_ID ('dbo.EmployeeThree', 'U') IS NOT NULL  
DROP TABLE dbo.EmployeeThree;  
GO  
  
SELECT pp.LastName, pp.FirstName, e.JobTitle   
INTO dbo.EmployeeOne  
FROM Person.Person AS pp JOIN HumanResources.Employee AS e  
ON e.BusinessEntityID = pp.BusinessEntityID  
WHERE LastName = 'Johnson';  
GO  
SELECT pp.LastName, pp.FirstName, e.JobTitle   
INTO dbo.EmployeeTwo  
FROM Person.Person AS pp JOIN HumanResources.Employee AS e  
ON e.BusinessEntityID = pp.BusinessEntityID  
WHERE LastName = 'Johnson';  
GO  
SELECT pp.LastName, pp.FirstName, e.JobTitle   
INTO dbo.EmployeeThree  
FROM Person.Person AS pp JOIN HumanResources.Employee AS e  
ON e.BusinessEntityID = pp.BusinessEntityID  
WHERE LastName = 'Johnson';  
GO  
-- Union ALL  
SELECT LastName, FirstName, JobTitle  
FROM dbo.EmployeeOne  
UNION ALL  
SELECT LastName, FirstName ,JobTitle  
FROM dbo.EmployeeTwo  
UNION ALL  
SELECT LastName, FirstName,JobTitle   
FROM dbo.EmployeeThree;  
GO  
  
SELECT LastName, FirstName,JobTitle  
FROM dbo.EmployeeOne  
UNION   
SELECT LastName, FirstName, JobTitle   
FROM dbo.EmployeeTwo  
UNION   
SELECT LastName, FirstName, JobTitle   
FROM dbo.EmployeeThree;  
GO  
  
SELECT LastName, FirstName,JobTitle   
FROM dbo.EmployeeOne  
UNION ALL  
(  
SELECT LastName, FirstName, JobTitle   
FROM dbo.EmployeeTwo  
UNION  
SELECT LastName, FirstName, JobTitle   
FROM dbo.EmployeeThree  
);  
GO  
  
```  
  
## <a name="examples-sssdwfull-and-sspdw"></a>例: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)]、[!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="e-using-a-simple-union"></a>E. 単純な UNION を使用する  
次の例では、結果セットに `FactInternetSales` テーブルと `DimCustomer` テーブルの `CustomerKey` 列の内容が含まれています。 ALL キーワードが使用されていないため、重複が結果から除外されます。  
  
```sql
-- Uses AdventureWorks  
  
SELECT CustomerKey   
FROM FactInternetSales    
UNION   
SELECT CustomerKey   
FROM DimCustomer   
ORDER BY CustomerKey;  
```  
  
### <a name="f-using-union-of-two-select-statements-with-order-by"></a>F. ORDER BY 句を指定した 2 つの SELECT ステートメントで UNION 句を使用する  
 UNION ステートメント内の SELECT ステートメントに ORDER BY 句が含まれるとき、その句はすべての SELECT ステートメントの後に置いてください。 次の例では、列を ORDER BY で並べ替える 2 つの `UNION` ステートメントでの `SELECT` の誤った使用法と正しい使用法を示しています。  
  
```sql
-- Uses AdventureWorks  
  
-- INCORRECT  
SELECT CustomerKey   
FROM FactInternetSales    
ORDER BY CustomerKey  
UNION   
SELECT CustomerKey   
FROM DimCustomer  
ORDER BY CustomerKey;  
  
-- CORRECT   
USE AdventureWorksPDW2012;  
  
SELECT CustomerKey   
FROM FactInternetSales    
UNION   
SELECT CustomerKey   
FROM DimCustomer   
ORDER BY CustomerKey;  
```  
  
### <a name="g-using-union-of-two-select-statements-with-where-and-order-by"></a>G. WHERE 句と ORDER BY 句を指定した 2 つの SELECT ステートメントで UNION 句を使用する  
次の例では、WHERE と ORDER BY を必要とする 2 つの `UNION` ステートメントでの `SELECT` の誤った使用法と正しい使用法を示しています。  
  
```sql
-- Uses AdventureWorks  
  
-- INCORRECT   
SELECT CustomerKey   
FROM FactInternetSales   
WHERE CustomerKey >= 11000  
ORDER BY CustomerKey   
UNION   
SELECT CustomerKey   
FROM DimCustomer   
ORDER BY CustomerKey;  
  
-- CORRECT  
USE AdventureWorksPDW2012;  
  
SELECT CustomerKey   
FROM FactInternetSales   
WHERE CustomerKey >= 11000  
UNION   
SELECT CustomerKey   
FROM DimCustomer   
ORDER BY CustomerKey;  
```  
  
### <a name="h-using-union-of-three-select-statements-to-show-effects-of-all-and-parentheses"></a>H. 3 つの SELECT ステートメントで UNION を使用して、ALL とかっこの効果を示す  
次の例では、`UNION` 利用時の ALL とかっこの効果を示す目的で、`UNION` を使用して **同じテーブル** の結果を結合しています。  
  
最初の例では `UNION ALL` を使用し、重複レコードを表示し、ソース テーブルの各行を 3 回返しています。 2 番目の例では、`ALL` を指定せずに `UNION` を使用して、3 つの `SELECT` ステートメントの結果を結合したものから重複する行を削除し、ソース テーブルから重複しない行のみ返します。  
  
3 番目の例では、最初の `UNION` と共に `ALL` を使用し、`ALL` を使用していない 2 番目の `UNION` をかっこで囲んでいます。 2 番目の `UNION` はかっこで囲まれているために最初に処理されます。 `ALL` オプションが使用されず、重複は削除されるため、重複なしの行のみがテーブルから返されます。 これらの行は、`SELECT` キーワードを使用して最初の `UNION ALL` の結果と結合されます。 この例では、2 つのセットの重複は削除されません。  
  
```sql
-- Uses AdventureWorks  
  
SELECT CustomerKey, FirstName, LastName  
FROM DimCustomer  
UNION ALL   
SELECT CustomerKey, FirstName, LastName  
FROM DimCustomer  
UNION ALL   
SELECT CustomerKey, FirstName, LastName  
FROM DimCustomer;  
  
SELECT CustomerKey, FirstName, LastName  
FROM DimCustomer  
UNION   
SELECT CustomerKey, FirstName, LastName  
FROM DimCustomer  
UNION   
SELECT CustomerKey, FirstName, LastName  
FROM DimCustomer;  
  
SELECT CustomerKey, FirstName, LastName  
FROM DimCustomer  
UNION ALL  
(  
SELECT CustomerKey, FirstName, LastName  
FROM DimCustomer  
UNION   
SELECT CustomerKey, FirstName, LastName  
FROM DimCustomer  
);  
```  
  
## <a name="see-also"></a>参照  
[SELECT (Transact-SQL)](../../t-sql/queries/select-transact-sql.md)   
[SELECT の例 (Transact-SQL)](../../t-sql/queries/select-examples-transact-sql.md)  
