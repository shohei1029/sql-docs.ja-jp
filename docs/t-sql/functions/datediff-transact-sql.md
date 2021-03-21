---
title: DATEDIFF (Transact-SQL) | Microsoft Docs
description: DATEDIFF 関数の Transact-SQL リファレンス。 datepart に基づき、開始と終了日付の間の数値的な差が返されます。
ms.date: 07/18/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- DATEDIFF_TSQL
- DATEDIFF
dev_langs:
- TSQL
helpviewer_keywords:
- dates [SQL Server], functions
- DATEDIFF function [SQL Server]
- time [SQL Server], crossed boundaries
- differences in date and time [SQL Server]
- counting crossed date time boundaries [SQL Server]
- date and time [SQL Server], DATEDIFF
- dates [SQL Server], crossed boundaries
- boundary differences date and time [SQL Server]
- functions [SQL Server], time
- functions [SQL Server], date and time
- interval dates [SQL Server]
- time [SQL Server], functions
- crossing date time boundaries [SQL Server]
- calculating dates times [SQL Server]
ms.assetid: eba979f2-1a8d-4cce-9d75-b74f9b519b37
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 9a8ad83b30d62b2b5c6b7348eed99e013259081f
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104754602"
---
# <a name="datediff-transact-sql"></a>DATEDIFF (Transact-SQL)

[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

この関数は、*startdate* と *enddate* で指定された 2 つの日付間の差を、指定された datepart 境界の数で (符号付き整数値として) で返します。
  
*startdate* 値と *enddate* 値の間のより大きな差を処理する関数については、「[DATEDIFF_BIG &#40;Transact-SQL&#41;](../../t-sql/functions/datediff-big-transact-sql.md)」を参照してください。 [!INCLUDE[tsql](../../includes/tsql-md.md)] の日付と時刻のあらゆるデータ型と関数に関する概要については、「[日付と時刻のデータ型および関数 &#40;Transact-SQL&#41;](../../t-sql/functions/date-and-time-data-types-and-functions-transact-sql.md)」を参照してください。
  
![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)
  
## <a name="syntax"></a>構文  
  
```syntaxsql
DATEDIFF ( datepart , startdate , enddate )  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数

*datepart*  
**DATEDIFF** で _startdate_ と _enddate_ の違いを報告する場合の単位。 一般的に使用される _datepart_ の単位には、`month` または `second` が含まれます。

_datepart_ 値を変数に指定することはできません。また、`'month'` のように引用符で囲まれた文字列として指定することもできません。

次の表に、すべての有効な _datepart_ 値の一覧を示します。 **DATEDIFF** は、_datepart_ の完全名、または一覧にある完全名の省略形のいずれかを受け取ります。

|*datepart* 名|*datepart* 省略形|  
|-----------|------------|
|**year**|**yy、yyyy**|  
|**quarter**|**qq, q**|  
|**month**|**mm, m**|  
|**dayofyear**|**dy、y**|  
|**day**|**dd, d**|  
|**week**|**wk、ww**|  
|**hour**|**hh**|  
|**minute**|**mi、n**|  
|**second**|**ss, s**|  
|**millisecond**|**ms**|  
|**microsecond**|**mcs**|  
|**nanosecond**|**ns**|  
| &nbsp; | &nbsp; |

> [!NOTE]
> 特定の各 *datepart* 名と、その *datepart* 名の省略形では、同じ値が返されます。

*startdate*  
次のいずれかの値に解決できる式。

+ **date**
+ **datetime**
+ **datetimeoffset**
+ **datetime2** 
+ **smalldatetime**
+ **time**
  
4 桁の西暦を使用して、あいまいさを排除します。 2 桁の西暦値については、「[two digit year cutoff サーバー構成オプションの構成](../../database-engine/configure-windows/configure-the-two-digit-year-cutoff-server-configuration-option.md)」を参照してください。
  
*enddate*  
「*startdate*」をご覧ください。
  
## <a name="return-type"></a>戻り値の型  
 **int**  
  
## <a name="return-value"></a>戻り値  

*datepart* により設定された境界に表示された、*startdate* と *enddate* の間の **int** 差。
  
たとえば、`SELECT DATEDIFF(day, '2036-03-01', '2036-02-28');` からは -2 が返されます。2036 はうるう年である必要があることを示します。 この場合、_startdate_ '2036-03-01' から開始し、-2 日をカウントすると、'2036-02-28' の _enddate_ に達することを意味します。
  
**int** の範囲 (-2,147,483,648 から +2,147,483,647) を超える戻り値の場合、`DATEDIFF` はエラーを返します。  **millisecond** の場合、*startdate* と *enddate* の差の最大値は 24 日 20 時間 31 分 23.647 秒です。 **second** の場合は、差の最大値は 68 年 19 日 3 時間 14 分 7 秒です。
  
*startdate* と *enddate* の両方に時刻値のみが割り当てられており、*datepart* が時刻の *datepart* でない場合、`DATEDIFF` は 0 を返します。
  
`DATEDIFF` では、戻り値を計算するために、*startdate* または *enddate* のタイム ゾーン オフセット要素が使用されます。
  
[smalldatetime](../../t-sql/data-types/smalldatetime-transact-sql.md) の精度は分単位までなので、*startdate* または **enddate** に *smalldatetime* 値を使用した場合、戻り値では秒とミリ秒が常に 0 に設定されます。
  
日付データ型の変数に時刻値のみが割り当てられている場合、`DATEDIFF` では、欠落している日付要素の値が既定値である `1900-01-01` に設定されます。 時刻データ型または日付データ型の変数に日付値のみが割り当てられている場合、`DATEDIFF` では、欠落している時刻要素の値が既定値である `00:00:00` に設定されます。 *startdate* または *enddate* のいずれか一方が時刻要素のみで、もう一方が日付要素のみであった場合、`DATEDIFF` では、欠落している時刻要素と日付要素がそれぞれの既定値に設定されます。
  
*startdate* と *enddate* で異なる日付データ型が使用されており、一方の時刻要素の数または秒の小数部の有効桁数が、もう一方のデータ型を超えている場合、`DATEDIFF` では、欠落している要素が 0 に設定されます。
  
## <a name="_datepart_-boundaries"></a>_datepart_ の境界

次の各ステートメントには、すべて同じ *startdate* と *enddate* の値が指定されています。 これらの日付は隣接しており、時間的な差は 100 ナノ秒 (0.0000001 秒) です。 各ステートメントにおける *startdate* と *enddate* の差は、どの要素をとっても、*datepart* の 1 単位分となるように配慮されています。 いずれのステートメントも 1 を返します。
  
```sql
SELECT DATEDIFF(year,        '2005-12-31 23:59:59.9999999', '2006-01-01 00:00:00.0000000');
SELECT DATEDIFF(quarter,     '2005-12-31 23:59:59.9999999', '2006-01-01 00:00:00.0000000');
SELECT DATEDIFF(month,       '2005-12-31 23:59:59.9999999', '2006-01-01 00:00:00.0000000');
SELECT DATEDIFF(dayofyear,   '2005-12-31 23:59:59.9999999', '2006-01-01 00:00:00.0000000');
SELECT DATEDIFF(day,         '2005-12-31 23:59:59.9999999', '2006-01-01 00:00:00.0000000');
SELECT DATEDIFF(week,        '2005-12-31 23:59:59.9999999', '2006-01-01 00:00:00.0000000');
SELECT DATEDIFF(hour,        '2005-12-31 23:59:59.9999999', '2006-01-01 00:00:00.0000000');
SELECT DATEDIFF(minute,      '2005-12-31 23:59:59.9999999', '2006-01-01 00:00:00.0000000');
SELECT DATEDIFF(second,      '2005-12-31 23:59:59.9999999', '2006-01-01 00:00:00.0000000');
SELECT DATEDIFF(millisecond, '2005-12-31 23:59:59.9999999', '2006-01-01 00:00:00.0000000');
SELECT DATEDIFF(microsecond, '2005-12-31 23:59:59.9999999', '2006-01-01 00:00:00.0000000');
```

*startdate* と *enddate* の年の値は異なるが、カレンダー週の値が同じである場合、`DATEDIFF` では、*datepart* **week** に対して 0 を返します。

## <a name="remarks"></a>解説  
`DATEDIFF` は、`SELECT <list>`、`WHERE`、`HAVING`、`GROUP BY`、`ORDER BY` 句で使用します。
  
`DATEDIFF` は、文字列リテラルを **datetime2** 型として暗黙的にキャストします。 つまり、`DATEDIFF` では、日付が文字列として渡される場合、YDM 形式がサポートされません。 文字列を明示的にキャストする必要があります、 **datetime** または **smalldatetime** YDM 形式を使用する型。
  
`SET DATEFIRST` を指定しても、`DATEDIFF` には何の影響もありません。 `DATEDIFF` では、週の最初の曜日として常に日曜日を使用し、関数が決定的な方法で動作するようにします。

*enddate* と *startdate* の差として **int** の範囲を超える値が返された場合、`DATEDIFF` は **minute** 以上の精度でオーバーフローする可能性があります。
  
## <a name="examples"></a>例  
次の例では、*startdate* パラメーターと *enddate* パラメーターの引数として、各種の式を使用しています。
  
### <a name="a-specifying-columns-for-startdate-and-enddate"></a>A. startdate と enddate に列を指定する  
この例では、テーブルの 2 つの列に日付を格納し、両者の差を日単位で計算しています。
  
```sql
CREATE TABLE dbo.Duration  
    (startDate datetime2, endDate datetime2);  
    
INSERT INTO dbo.Duration(startDate, endDate)  
    VALUES ('2007-05-06 12:10:09', '2007-05-07 12:10:09');  
    
SELECT DATEDIFF(day, startDate, endDate) AS 'Duration'  
    FROM dbo.Duration;  
-- Returns: 1  
```  
  
### <a name="b-specifying-user-defined-variables-for-startdate-and-enddate"></a>B. startdate と enddate にユーザー定義変数を指定する  
この例では、ユーザー定義変数が *startdate* と *enddate* の引数として機能します。
  
```sql
DECLARE @startdate DATETIME2 = '2007-05-05 12:10:09.3312722';  
DECLARE @enddate   DATETIME2 = '2007-05-04 12:10:09.3312722';   
SELECT DATEDIFF(day, @startdate, @enddate);  
```  
  
### <a name="c-specifying-scalar-system-functions-for-startdate-and-enddate"></a>C. startdate と enddate にスカラー システム関数を指定する  
この例では、*startdate* と *enddate* の引数としてスカラー システム関数を使用しています。
  
```sql
SELECT DATEDIFF(millisecond, GETDATE(), SYSDATETIME());  
```  
  
### <a name="d-specifying-scalar-subqueries-and-scalar-functions-for-startdate-and-enddate"></a>D. startdate と enddate にスカラー サブクエリおよびスカラー関数を指定する  
この例では、*startdate* と *enddate* の引数として、サブクエリとスカラー関数を使用しています。
  
```sql
USE AdventureWorks2012;  
GO  
SELECT DATEDIFF(day,
    (SELECT MIN(OrderDate) FROM Sales.SalesOrderHeader),  
    (SELECT MAX(OrderDate) FROM Sales.SalesOrderHeader));  
```  
  
### <a name="e-specifying-constants-for-startdate-and-enddate"></a>E. startdate と enddate に定数を指定する  
この例では、*startdate* と *enddate* の引数として文字定数を使用しています。
  
```sql
SELECT DATEDIFF(day,
   '2007-05-07 09:53:01.0376635',
   '2007-05-08 09:53:01.0376635');  
```  
  
### <a name="f-specifying-numeric-expressions-and-scalar-system-functions-for-enddate"></a>F. enddate に数値式およびスカラー システム関数を指定する  
この例では、*enddate* の引数として、数値式 `(GETDATE() + 1)` のほか、スカラー システム関数 `GETDATE` および `SYSDATETIME` を使用しています。
  
```sql
USE AdventureWorks2012;  
GO  
SELECT DATEDIFF(day, '2007-05-07 09:53:01.0376635', GETDATE() + 1)
    AS NumberOfDays  
    FROM Sales.SalesOrderHeader;  
GO  
USE AdventureWorks2012;  
GO  
SELECT
    DATEDIFF(
            day,
            '2007-05-07 09:53:01.0376635',
            DATEADD(day, 1, SYSDATETIME())
        ) AS NumberOfDays  
    FROM Sales.SalesOrderHeader;  
GO  
```  
  
### <a name="g-specifying-ranking-functions-for-startdate"></a>G. startdate に順位付け関数を指定する  
この例では、*startdate* の引数として順位付け関数を使用しています。
  
```sql
USE AdventureWorks2012;  
GO  
SELECT p.FirstName, p.LastName  
    ,DATEDIFF(day, ROW_NUMBER() OVER (ORDER BY   
        a.PostalCode), SYSDATETIME()) AS 'Row Number'  
FROM Sales.SalesPerson s   
    INNER JOIN Person.Person p   
        ON s.BusinessEntityID = p.BusinessEntityID  
    INNER JOIN Person.Address a   
        ON a.AddressID = p.BusinessEntityID  
WHERE TerritoryID IS NOT NULL   
    AND SalesYTD <> 0;  
```  
  
### <a name="h-specifying-an-aggregate-window-function-for-startdate"></a>H. startdate に集計関数を指定する  
この例では、*startdate* の引数として集計関数を使用しています。
  
```sql
USE AdventureWorks2012;  
GO  
SELECT soh.SalesOrderID, sod.ProductID, sod.OrderQty, soh.OrderDate,
    DATEDIFF(day, MIN(soh.OrderDate)   
        OVER(PARTITION BY soh.SalesOrderID), SYSDATETIME()) AS 'Total'  
FROM Sales.SalesOrderDetail sod  
    INNER JOIN Sales.SalesOrderHeader soh  
        ON sod.SalesOrderID = soh.SalesOrderID  
WHERE soh.SalesOrderID IN(43659, 58918);  
GO  
```  

### <a name="i-finding-difference-between-startdate-and-enddate-as-date-parts-strings"></a>I. startdate と enddate の差を日付部分の文字列として検索する

```sql
-- DOES NOT ACCOUNT FOR LEAP YEARS
DECLARE @date1 DATETIME, @date2 DATETIME, @result VARCHAR(100);
DECLARE @years INT, @months INT, @days INT,
    @hours INT, @minutes INT, @seconds INT, @milliseconds INT;

SET @date1 = '1900-01-01 00:00:00.000'
SET @date2 = '2018-12-12 07:08:01.123'

SELECT @years = DATEDIFF(yy, @date1, @date2)
IF DATEADD(yy, -@years, @date2) < @date1 
SELECT @years = @years-1
SET @date2 = DATEADD(yy, -@years, @date2)

SELECT @months = DATEDIFF(mm, @date1, @date2)
IF DATEADD(mm, -@months, @date2) < @date1 
SELECT @months=@months-1
SET @date2= DATEADD(mm, -@months, @date2)

SELECT @days=DATEDIFF(dd, @date1, @date2)
IF DATEADD(dd, -@days, @date2) < @date1 
SELECT @days=@days-1
SET @date2= DATEADD(dd, -@days, @date2)

SELECT @hours=DATEDIFF(hh, @date1, @date2)
IF DATEADD(hh, -@hours, @date2) < @date1 
SELECT @hours=@hours-1
SET @date2= DATEADD(hh, -@hours, @date2)

SELECT @minutes=DATEDIFF(mi, @date1, @date2)
IF DATEADD(mi, -@minutes, @date2) < @date1 
SELECT @minutes=@minutes-1
SET @date2= DATEADD(mi, -@minutes, @date2)

SELECT @seconds=DATEDIFF(s, @date1, @date2)
IF DATEADD(s, -@seconds, @date2) < @date1 
SELECT @seconds=@seconds-1
SET @date2= DATEADD(s, -@seconds, @date2)

SELECT @milliseconds=DATEDIFF(ms, @date1, @date2)

SELECT @result= ISNULL(CAST(NULLIF(@years,0) AS VARCHAR(10)) + ' years,','')
     + ISNULL(' ' + CAST(NULLIF(@months,0) AS VARCHAR(10)) + ' months,','')    
     + ISNULL(' ' + CAST(NULLIF(@days,0) AS VARCHAR(10)) + ' days,','')
     + ISNULL(' ' + CAST(NULLIF(@hours,0) AS VARCHAR(10)) + ' hours,','')
     + ISNULL(' ' + CAST(@minutes AS VARCHAR(10)) + ' minutes and','')
     + ISNULL(' ' + CAST(@seconds AS VARCHAR(10)) 
     + CASE
            WHEN @milliseconds > 0
                THEN '.' + CAST(@milliseconds AS VARCHAR(10)) 
            ELSE ''
       END 
     + ' seconds','')

SELECT @result
```

[!INCLUDE[ssResult](../../includes/ssresult-md.md)] 

```
118 years, 11 months, 11 days, 7 hours, 8 minutes and 1.123 seconds
```
  
## <a name="examples-sssdwfull-and-sspdw"></a>例: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)]、[!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
次の例では、*startdate* パラメーターと *enddate* パラメーターの引数として、各種の式を使用しています。
  
### <a name="j-specifying-columns-for-startdate-and-enddate"></a>J. startdate と enddate に列を指定する  
この例では、テーブルの 2 つの列に日付を格納し、両者の差を日単位で計算しています。
  
```sql
CREATE TABLE dbo.Duration 
    (startDate datetime2, endDate datetime2);
    
INSERT INTO dbo.Duration (startDate, endDate)  
    VALUES ('2007-05-06 12:10:09', '2007-05-07 12:10:09');  
    
SELECT TOP(1) DATEDIFF(day, startDate, endDate) AS Duration  
    FROM dbo.Duration;  
-- Returns: 1  
```  
  
### <a name="k-specifying-scalar-subqueries-and-scalar-functions-for-startdate-and-enddate"></a>K. startdate と enddate にスカラー サブクエリおよびスカラー関数を指定する  
この例では、*startdate* と *enddate* の引数として、サブクエリとスカラー関数を使用しています。
  
```sql
-- Uses AdventureWorks  
  
SELECT TOP(1) DATEDIFF(day, (SELECT MIN(HireDate) FROM dbo.DimEmployee),  
    (SELECT MAX(HireDate) FROM dbo.DimEmployee))   
FROM dbo.DimEmployee;  
  
```  
  
### <a name="l-specifying-constants-for-startdate-and-enddate"></a>L. startdate と enddate に定数を指定する  
この例では、*startdate* と *enddate* の引数として文字定数を使用しています。
  
```sql
-- Uses AdventureWorks  
  
SELECT TOP(1) DATEDIFF(day,
    '2007-05-07 09:53:01.0376635',
    '2007-05-08 09:53:01.0376635') FROM DimCustomer;  
```  
  
### <a name="m-specifying-ranking-functions-for-startdate"></a>M. startdate に順位付け関数を指定する  
この例では、*startdate* の引数として順位付け関数を使用しています。
  
```sql
-- Uses AdventureWorks  
  
SELECT FirstName, LastName,
    DATEDIFF(day, ROW_NUMBER() OVER (ORDER BY   
        DepartmentName), SYSDATETIME()) AS RowNumber  
FROM dbo.DimEmployee;  
```  
  
### <a name="n-specifying-an-aggregate-window-function-for-startdate"></a>北 startdate に集計関数を指定する  
この例では、*startdate* の引数として集計関数を使用しています。
  
```sql
-- Uses AdventureWorks  
  
SELECT FirstName, LastName, DepartmentName,
    DATEDIFF(year, MAX(HireDate)  
        OVER (PARTITION BY DepartmentName), SYSDATETIME()) AS SomeValue  
FROM dbo.DimEmployee  
```  
  
## <a name="see-also"></a>関連項目
[DATEDIFF_BIG &#40;Transact-SQL&#41;](../../t-sql/functions/datediff-big-transact-sql.md)  
[CAST および CONVERT &#40;Transact-SQL&#41;](../../t-sql/functions/cast-and-convert-transact-sql.md)
  
