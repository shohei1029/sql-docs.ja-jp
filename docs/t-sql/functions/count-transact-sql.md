---
description: COUNT (Transact-SQL)
title: COUNT (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 07/24/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- COUNT_TSQL
- COUNT
dev_langs:
- TSQL
helpviewer_keywords:
- totals [SQL Server], COUNT function
- totals [SQL Server]
- counting items in group
- groups [SQL Server], number of items in
- number of group items
- COUNT function [Transact-SQL]
ms.assetid: 28d39da6-bc2e-46c7-858c-b1721c938830
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: d061173f0898345c9ebdef3b9208be3dddfccc53
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104748032"
---
# <a name="count-transact-sql"></a>COUNT (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

この関数は、グループ内で見つかった項目数を返します。 `COUNT` は [COUNT_BIG](../../t-sql/functions/count-big-transact-sql.md) 関数と同じように動作します。 これらの関数の違いは、戻り値のデータ型のみです。 `COUNT` は常に **int** データ型の値を返します。 `COUNT_BIG` は常に **bigint** データ型の値を返します。
  
![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)
  
## <a name="syntax"></a>構文  
  
```syntaxsql

-- Aggregation Function Syntax  
COUNT ( { [ [ ALL | DISTINCT ] expression ] | * } )  

-- Analytic Function Syntax  
COUNT ( [ ALL ]  { expression | * } ) OVER ( [ <partition_by_clause> ] )  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
**ALL**  
すべての値にこの集計関数を適用します。 既定として ALL が使用されます。
  
DISTINCT  
`COUNT` で一意の NULL ではない値の数を返すことを指定します。
  
*式 (expression)*  
**image**、**ntext**、**text** を除く、任意の型の [式](../../t-sql/language-elements/expressions-transact-sql.md)です。 `COUNT` は、式の集計関数またはサブクエリをサポートしていません。
  
\*  
`COUNT` ですべての行をカウントし、返すテーブルの合計行数を決定することを指定します。 `COUNT(*)` はパラメーターを受け取らず、DISTINCT の使用をサポートしていません。 `COUNT(*)` では、この関数の定義上、特定の列についての情報は使用されないため、*expression* パラメーターは必要ありません。 `COUNT(*)` は、指定されたテーブル内の行数を返し、重複する行を保持します。 各行は 1 行としてカウントされ、 これには NULL 値を保持している行も含まれます。
  
OVER **(** [ *partition_by_clause* ] [ *order_by_clause* ] [ *ROW_or_RANGE_clause* ] **)**  
*partition_by_clause* は、`FROM` 句で生成された結果セットをパーティションに分割します。このパーティションに `COUNT` 関数が適用されます。 指定しない場合、関数ではクエリ結果セットのすべての行を 1 つのグループとして扱います。 *order_by_clause* は、操作の論理的順序を決定します。 詳細については、[OVER 句 &#40;Transact-SQL&#41;](../../t-sql/queries/select-over-clause-transact-sql.md) に関するページを参照してください。 

## <a name="return-types"></a>戻り値の型
 **int**  
  
## <a name="remarks"></a>解説  
COUNT(\*) はグループ内のアイテム数を返します。 これには NULL 値と重複値が含まれます。
  
COUNT(ALL *expression*) はグループ内の各行に対して *expression* を評価し、非 NULL 値の数を返します。
  
COUNT(DISTINCT *expression*) はグループ内の各行に対して *expression* を評価し、一意の非 NULL 値の数を返します。
  
戻り値が 2^31-1 を超える場合、`COUNT` はエラーを返します。 このような場合は代わりに `COUNT_BIG` を使用してください。
  
`COUNT` は、OVER および ORDER BY 句 "***なし** _" で使用される場合は決定的関数です。 OVER および ORDER BY 句と "*_共に_*" 使用される場合は、非決定論的関数です。 詳細については、「[決定的関数と非決定的関数](../../relational-databases/user-defined-functions/deterministic-and-nondeterministic-functions.md)」を参照してください。
  
## <a name="examples"></a>例  
  
### <a name="a-using-count-and-distinct"></a>A. COUNT と DISTINCT を使用する  
この例では、[!INCLUDE[ssSampleDBCoFull](../../includes/sssampledbcofull-md.md)] の従業員が保持できるさまざまな役職の数を返します。
  
```sql
SELECT COUNT(DISTINCT Title)  
FROM HumanResources.Employee;  
GO  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
-----------
67  
  
(1 row(s) affected)
```
  
### <a name="b-using-count"></a>B. COUNT(\*) を使用する  
この例は、[!INCLUDE[ssSampleDBCoFull](../../includes/sssampledbcofull-md.md)] の従業員の合計数を返します。
  
```sql
SELECT COUNT(*)  
FROM HumanResources.Employee;  
GO  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
-----------
290  
  
(1 row(s) affected)
```
  
### <a name="c-using-count-with-other-aggregates"></a>C. COUNT(\*) を他の集計関数と共に使用する  
この例は、`COUNT(*)` が `SELECT` リスト内の他の集計関数と連携していることを示しています。 この例では、[!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] データベースを使用します。
  
```sql
SELECT COUNT(*), AVG(Bonus)  
FROM Sales.SalesPerson  
WHERE SalesQuota > 25000;  
GO  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
----------- ---------------------
14            3472.1428
  
(1 row(s) affected)
```
  
### <a name="d-using-the-over-clause"></a>D. OVER 句を使用する  
この例では、`MIN`、`MAX`、`AVG`、および `COUNT` 関数と `OVER` 句を使用して、[!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] データベースの `HumanResources.Department` テーブルの各部門について集計値を返します。
  
```sql
SELECT DISTINCT Name  
       , MIN(Rate) OVER (PARTITION BY edh.DepartmentID) AS MinSalary  
       , MAX(Rate) OVER (PARTITION BY edh.DepartmentID) AS MaxSalary  
       , AVG(Rate) OVER (PARTITION BY edh.DepartmentID) AS AvgSalary  
       ,COUNT(edh.BusinessEntityID) OVER (PARTITION BY edh.DepartmentID) AS EmployeesPerDept  
FROM HumanResources.EmployeePayHistory AS eph  
JOIN HumanResources.EmployeeDepartmentHistory AS edh  
     ON eph.BusinessEntityID = edh.BusinessEntityID  
JOIN HumanResources.Department AS d  
ON d.DepartmentID = edh.DepartmentID
WHERE edh.EndDate IS NULL  
ORDER BY Name;  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
Name                          MinSalary             MaxSalary             AvgSalary             EmployeesPerDept  
----------------------------- --------------------- --------------------- --------------------- ----------------  
Document Control              10.25                 17.7885               14.3884               5  
Engineering                   32.6923               63.4615               40.1442               6  
Executive                     39.06                 125.50                68.3034               4  
Facilities and Maintenance    9.25                  24.0385               13.0316               7  
Finance                       13.4615               43.2692               23.935                10  
Human Resources               13.9423               27.1394               18.0248               6  
Information Services          27.4038               50.4808               34.1586               10  
Marketing                     13.4615               37.50                 18.4318               11  
Production                    6.50                  84.1346               13.5537               195  
Production Control            8.62                  24.5192               16.7746               8  
Purchasing                    9.86                  30.00                 18.0202               14  
Quality Assurance             10.5769               28.8462               15.4647               6  
Research and Development      40.8654               50.4808               43.6731               4  
Sales                         23.0769               72.1154               29.9719               18  
Shipping and Receiving        9.00                  19.2308               10.8718               6  
Tool Design                   8.62                  29.8462               23.5054               6  
  
(16 row(s) affected)
```  
  
## <a name="examples-sssdwfull-and-sspdw"></a>例: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)]、[!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="e-using-count-and-distinct"></a>E. COUNT と DISTINCT を使用する  
この例では、特定の会社の従業員が保持できるさまざまな役職の数を返します。
  
```sql
USE ssawPDW;  
  
SELECT COUNT(DISTINCT Title)  
FROM dbo.DimEmployee;  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
-----------
67
```  
  
### <a name="f-using-count"></a>F. COUNT(\*) を使用する  
この例は、`dbo.DimEmployee` テーブルの合計行数を返します。
  
```sql
USE ssawPDW;  
  
SELECT COUNT(*)  
FROM dbo.DimEmployee;  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
-------------
296
```  
  
### <a name="g-using-count-with-other-aggregates"></a>G. COUNT(\*) を他の集計関数と共に使用する  
この例では、`COUNT(*)` と、`SELECT` リスト内の他の集計関数を組み合わせています。 年間販売ノルマが $500,000 より多い販売担当者の数と、販売担当者の平均販売ノルマを返します。
  
```sql
USE ssawPDW;  
  
SELECT COUNT(EmployeeKey) AS TotalCount, AVG(SalesAmountQuota) AS [Average Sales Quota]  
FROM dbo.FactSalesQuota  
WHERE SalesAmountQuota > 500000 AND CalendarYear = 2001;  
  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
TotalCount  Average Sales Quota
----------  -------------------
10          683800.0000
```
  
### <a name="h-using-count-with-having"></a>H. HAVING で COUNT を使用する  
この例では、`COUNT` と `HAVING` 句を使用して、従業員数が 15 人を超える会社の部門を返します。
  
```sql
USE ssawPDW;  
  
SELECT DepartmentName,   
       COUNT(EmployeeKey)AS EmployeesInDept  
FROM dbo.DimEmployee  
GROUP BY DepartmentName  
HAVING COUNT(EmployeeKey) > 15;  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
DepartmentName  EmployeesInDept
--------------  ---------------
Sales           18
Production      179
```
  
### <a name="i-using-count-with-over"></a>I. OVER で COUNT を使用する  
この例では、`COUNT` と `OVER` 句を使用して、指定した各受注に含まれる製品数を返します。
  
```sql
USE ssawPDW;  
  
SELECT DISTINCT COUNT(ProductKey) OVER(PARTITION BY SalesOrderNumber) AS ProductCount  
    ,SalesOrderNumber  
FROM dbo.FactInternetSales  
WHERE SalesOrderNumber IN (N'SO53115',N'SO55981');  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
ProductCount   SalesOrderID`
------------   -----------------
3              SO53115
1              SO55981
```
  
## <a name="see-also"></a>関連項目
[集計関数 &#40;Transact-SQL&#41;](../../t-sql/functions/aggregate-functions-transact-sql.md)  
[COUNT_BIG &#40;Transact-SQL&#41;](../../t-sql/functions/count-big-transact-sql.md)  
[OVER 句 &#40;Transact-SQL&#41;](../../t-sql/queries/select-over-clause-transact-sql.md)
  
  


