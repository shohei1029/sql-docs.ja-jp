---
title: COALESCE (Transact-SQL) | Microsoft Docs
description: COALESCE の Transact-SQL リファレンス。NULL と評価されない最初の式の現在の値を返します。
ms.date: 08/30/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- COALESCE
- COALESCE_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- expressions [SQL Server], nonnull
- COALESCE function
- first nonnull expressions [SQL Server]
- nonnull expressions
ms.assetid: fafc0dba-f8a8-4aad-9b7f-908e34b74d88
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 4df79264b6390539dd267cf15877fa5332f2dd88
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104752942"
---
# <a name="coalesce-transact-sql"></a>COALESCE (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

引数を順番に評価し、`NULL` と評価されない最初の式の現在の値を返します。 たとえば、`SELECT COALESCE(NULL, NULL, 'third_value', 'fourth_value');` では、3 番目の値が null ではない最初の値であるために、3 番目の値が返されます。 
  
![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql
COALESCE ( expression [ ,...n ] )   
```  
  
## <a name="arguments"></a>引数  
_式 (expression)_  
任意のデータ型の[式](../../t-sql/language-elements/expressions-transact-sql.md)を指定します。  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="return-types"></a>戻り値の型
_式_ のデータ型のうち、最も優先順位が高いものを返します。 すべての式で NULL 値が許可されない場合、結果は NULL 値が許可されない型になります。  
  
## <a name="remarks"></a>解説  
すべての引数が `NULL` である場合、`COALESCE` は `NULL` を返します。 NULL 値の少なくとも 1 つは、型指定された `NULL` である必要があります。  
  
## <a name="comparing-coalesce-and-case"></a>COALESCE と CASE の比較  
`COALESCE` 式は `CASE` 式を簡単にした構文です。  つまり、`COALESCE`(_expression1_, _...n_) というコードは、次の `CASE` 式としてクエリ オプティマイザーによって書き換えられます。  
  
```sql  
CASE  
WHEN (expression1 IS NOT NULL) THEN expression1  
WHEN (expression2 IS NOT NULL) THEN expression2  
...  
ELSE expressionN  
END  
```  
  
そのため、入力値 (_expression1_、_expression2_、_expressionN_ など) が複数回評価されます。 サブクエリを含む値式は不明確な式と見なされ、サブクエリは 2 回評価されます。 この結果は、SQL 標準に準拠しています。 どちらの場合も、最初の評価とその後の評価で返される結果が異なります。  
  
たとえば、`COALESCE((subquery), 1)` というコードを実行すると、サブクエリは 2 回評価されます。 その結果、クエリの分離レベルによっては、得られる結果が異なる場合があります。 たとえば、マルチユーザー環境の `READ COMMITTED` 分離レベルでは、このコードによって `NULL` 値が返される場合があります。 安定した結果が返されるようにするには、`SNAPSHOT ISOLATION` 分離レベルを使用するか、`COALESCE` を `ISNULL` 関数に置き換えてください。 または、次の例に示すように、サブクエリをサブセレクトに含めるようにクエリを書き換えることもできます。  
  
```sql  
SELECT CASE WHEN x IS NOT NULL THEN x ELSE 1 END  
FROM  
(  
SELECT (SELECT Nullable FROM Demo WHERE SomeCol = 1) AS x  
) AS T;  
  
```  
  
## <a name="comparing-coalesce-and-isnull"></a>COALESCE と ISNULL の比較  
`ISNULL` 関数と `COALESCE` 式の目的は同じですが、動作は異なる場合があります。  
  
1.  `ISNULL` は関数なので、評価されるのは一度だけです。  `COALESCE` 式の入力値は、前述のとおり、複数回評価できます。  
  
2.  結果式のデータ型の判定が異なります。 `ISNULL` では最初のパラメーターのデータ型が使用されますが、`COALESCE` では、`CASE` 式の規則に従って、優先順位が最も高い値のデータ型が返されます。  
  
3.  結果式の NULL 値の許容は、`ISNULL` と `COALESCE` で異なります。 `ISNULL` の戻り値は、常に NULL 値が許容されないと見なされます (戻り値は NULL 値が許容されない値であることが想定されます)。 これに対し、NULL 以外のパラメーターを使用した `COALESCE` の場合は `NULL` であると見なされます。 そのため、式 `ISNULL(NULL, 1)` と `COALESCE(NULL, 1)` は同じですが、NULL 値を許容するかどうかは異なります。 これらの値により、次の例に示すように、これらの式を計算列で使用する場合、キー制約を作成する場合、またはインデックスを作成できるようにスカラー UDF の戻り値を明確にする場合に違いが生じます。  
  
    ```sql  
    USE tempdb;  
    GO  
    -- This statement fails because the PRIMARY KEY cannot accept NULL values  
    -- and the nullability of the COALESCE expression for col2   
    -- evaluates to NULL.  
    CREATE TABLE #Demo   
    (   
      col1 INTEGER NULL,   
      col2 AS COALESCE(col1, 0) PRIMARY KEY,   
      col3 AS ISNULL(col1, 0)   
    );   
  
    -- This statement succeeds because the nullability of the   
    -- ISNULL function evaluates AS NOT NULL.  
  
    CREATE TABLE #Demo   
    (   
      col1 INTEGER NULL,   
      col2 AS COALESCE(col1, 0),   
      col3 AS ISNULL(col1, 0) PRIMARY KEY   
    );  
    ```  
  
4.  `ISNULL` と `COALESCE` の妥当性検査も異なります。 たとえば、`ISNULL` の `NULL` 値は **int** に変換されますが、`COALESCE` の場合は、データ型を指定する必要があります。  
  
5.  `ISNULL` は、2 つのパラメーターのみを受け取ります。 これに対し `COALESCE` はさまざまな数のパラメーターを受け取ります。  
  
## <a name="examples"></a>例  
  
### <a name="a-running-a-simple-example"></a>A. 簡単な例を実行する  
次の例では、`COALESCE` を使用して、NULL 以外の値を持つ最初の列からデータを選択する方法を示します。 この例では、[!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] データベースを使用します。  
  
```sql  
SELECT Name, Class, Color, ProductNumber,  
COALESCE(Class, Color, ProductNumber) AS FirstNotNull  
FROM Production.Product;  
```  
  
### <a name="b-running-a-complex-example"></a>B. 複雑な例を実行する  
次の例では、`wages` テーブルに、従業員の年俸に関する情報 (時給、給与、歩合) が含まれている 3 つの列を含めています。 ただし、1 人の従業員が受け取る給与の種類は 1 つだけです。 すべての従業員に支払われている給料の総額を算出するには、`COALESCE` を使って `hourly_wage`、`salary`、および `commission` から NULL でない値だけを取り出します。  
  
```sql  
SET NOCOUNT ON;  
GO  
USE tempdb;  
IF OBJECT_ID('dbo.wages') IS NOT NULL  
    DROP TABLE wages;  
GO  
CREATE TABLE dbo.wages  
(  
    emp_id        TINYINT   IDENTITY,  
    hourly_wage   DECIMAL   NULL,  
    salary        DECIMAL   NULL,  
    commission    DECIMAL   NULL,  
    num_sales     TINYINT   NULL  
);  
GO  
INSERT dbo.wages (hourly_wage, salary, commission, num_sales)  
VALUES  
    (10.00, NULL, NULL, NULL),  
    (20.00, NULL, NULL, NULL),  
    (30.00, NULL, NULL, NULL),  
    (40.00, NULL, NULL, NULL),  
    (NULL, 10000.00, NULL, NULL),  
    (NULL, 20000.00, NULL, NULL),  
    (NULL, 30000.00, NULL, NULL),  
    (NULL, 40000.00, NULL, NULL),  
    (NULL, NULL, 15000, 3),  
    (NULL, NULL, 25000, 2),  
    (NULL, NULL, 20000, 6),  
    (NULL, NULL, 14000, 4);  
GO  
SET NOCOUNT OFF;  
GO  
SELECT CAST(COALESCE(hourly_wage * 40 * 52,   
   salary,   
   commission * num_sales) AS money) AS 'Total Salary'   
FROM dbo.wages  
ORDER BY 'Total Salary';  
GO  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```   
Total Salary  
------------  
10000.00  
20000.00  
20800.00  
30000.00  
40000.00  
41600.00  
45000.00  
50000.00  
56000.00  
62400.00  
83200.00  
120000.00  
  
(12 row(s) affected)
```  
  
### <a name="c-simple-example"></a>C: 簡単な例  
次の例では、`COALESCE` が NULL 以外の値を含む最初の列からデータを選択する方法を示します。 `Products` テーブルに、このデータが含まれているとします。  
  
```  
Name         Color      ProductNumber  
------------ ---------- -------------  
Socks, Mens  NULL       PN1278  
Socks, Mens  Blue       PN1965  
NULL         White      PN9876
```  
 
次いで、次の COALESCE クエリを実行します。  
  
```sql  
SELECT Name, Color, ProductNumber, COALESCE(Color, ProductNumber) AS FirstNotNull   
FROM Products ;  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
Name         Color      ProductNumber  FirstNotNull  
------------ ---------- -------------  ------------  
Socks, Mens  NULL       PN1278         PN1278  
Socks, Mens  Blue       PN1965         Blue  
NULL         White      PN9876         White
```  
  
最初の行の `FirstNotNull` 値が `Socks, Mens` でなく `PN1278` であることに着目してください。 この値がこうなるのは、この例で、`Name` 列が `COALESCE` のパラメーターとして指定されていないためです。  
  
### <a name="d-complex-example"></a>D:複雑な例  
次の例では、`COALESCE` を使用して 3 つの列の値を比較し、列で検索された null 以外の値のみを返します。  
  
```sql  
CREATE TABLE dbo.wages  
(  
    emp_id        TINYINT   NULL,  
    hourly_wage   DECIMAL   NULL,  
    salary        DECIMAL   NULL,  
    commission    DECIMAL   NULL,  
    num_sales     TINYINT   NULL  
);  
INSERT INTO dbo.wages (emp_id, hourly_wage, salary, commission, num_sales)  
VALUES (1, 10.00, NULL, NULL, NULL);  
  
INSERT INTO dbo.wages (emp_id, hourly_wage, salary, commission, num_sales)  
VALUES (2, 20.00, NULL, NULL, NULL);  
  
INSERT INTO dbo.wages (emp_id, hourly_wage, salary, commission, num_sales)  
VALUES (3, 30.00, NULL, NULL, NULL);  
  
INSERT INTO dbo.wages (emp_id, hourly_wage, salary, commission, num_sales)  
VALUES (4, 40.00, NULL, NULL, NULL);  
  
INSERT INTO dbo.wages (emp_id, hourly_wage, salary, commission, num_sales)  
VALUES (5, NULL, 10000.00, NULL, NULL);  
  
INSERT INTO dbo.wages (emp_id, hourly_wage, salary, commission, num_sales)  
VALUES (6, NULL, 20000.00, NULL, NULL);  
  
INSERT INTO dbo.wages (emp_id, hourly_wage, salary, commission, num_sales)  
VALUES (7, NULL, 30000.00, NULL, NULL);  
  
INSERT INTO dbo.wages (emp_id, hourly_wage, salary, commission, num_sales)  
VALUES (8, NULL, 40000.00, NULL, NULL);  
  
INSERT INTO dbo.wages (emp_id, hourly_wage, salary, commission, num_sales)  
VALUES (9, NULL, NULL, 15000, 3);  
  
INSERT INTO dbo.wages (emp_id, hourly_wage, salary, commission, num_sales)  
VALUES (10,NULL, NULL, 25000, 2);  
  
INSERT INTO dbo.wages (emp_id, hourly_wage, salary, commission, num_sales)  
VALUES (11, NULL, NULL, 20000, 6);  
  
INSERT INTO dbo.wages (emp_id, hourly_wage, salary, commission, num_sales)  
VALUES (12, NULL, NULL, 14000, 4);  
  
SELECT CAST(COALESCE(hourly_wage * 40 * 52,   
   salary,   
   commission * num_sales) AS DECIMAL(10,2)) AS TotalSalary   
FROM dbo.wages  
ORDER BY TotalSalary;  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```
Total Salary  
------------  
10000.00  
20000.00  
20800.00  
30000.00  
40000.00  
41600.00  
45000.00  
50000.00  
56000.00  
62400.00  
83200.00  
120000.00
```  
  
## <a name="see-also"></a>参照  
[ISNULL &#40;Transact-SQL&#41;](../../t-sql/functions/isnull-transact-sql.md)   
[CASE &#40;Transact-SQL&#41;](../../t-sql/language-elements/case-transact-sql.md)  
  
  
