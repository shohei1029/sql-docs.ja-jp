---
title: DATEADD (Transact-SQL) | Microsoft Docs
description: DATEADD 関数の Transact-SQL リファレンス。 この関数では、指定の datepart によって変更されている日付が返されます。
ms.date: 07/29/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- DATEADD
- DATEADD_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- dates [SQL Server], functions
- add interval to date or time [SQL Server]
- subtract interval from date or time [SQL Server]
- functions [SQL Server], time
- functions [SQL Server], date and time
- time [SQL Server], functions
- dates [SQL Server], intervals
- date and time [SQL Server], DATEADD
- DATEADD function [SQL Server]
ms.assetid: 89c5ae32-89c6-47e1-979e-15d97908b9f1
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: cdcddd4eee63afc89446aa8390cef8b57bb2ef60
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104754612"
---
# <a name="dateadd-transact-sql"></a>DATEADD (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

この関数は、入力 *date* 値の指定 *datepart* に指定 *number* 値 (符号付き整数として) を追加し、その変更後の値を返します。
  
[!INCLUDE[tsql](../../includes/tsql-md.md)] の日付と時刻のあらゆるデータ型と関数に関する概要については、「[日付と時刻のデータ型および関数 &#40;Transact-SQL&#41;](../../t-sql/functions/date-and-time-data-types-and-functions-transact-sql.md)」を参照してください。
  
![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)
  
## <a name="syntax"></a>構文  
  
```sql
DATEADD (datepart , number , date )  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
*datepart*  
`DATEADD` によって **integer** *number* が追加される *date* のパーツ。 この表には、有効な *datepart* 引数をすべて一覧表示しています。 

> [!NOTE]
> `DATEADD` は、*datepart* 引数に関して、ユーザー定義変数に相当するものは受け入れられません。 
  
|*datepart*|省略形|  
|---|---|
|**year**|**yy**、**yyyy**|  
|**quarter**|**qq**、**q**|  
|**month**|**mm**、**m**|  
|**dayofyear**|**dy**、**y**|  
|**day**|**dd**、**d**|  
|**week**|**wk**、**ww**|  
|**weekday**|**dw**、**w**|  
|**hour**|**hh**|  
|**minute**|**mi**、**n**|  
|**second**|**ss**、**s**|  
|**millisecond**|**ms**|  
|**microsecond**|**mcs**|  
|**nanosecond**|**ns**|  
  
*number*  
`DATEADD` によって追加される [int](../../t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql.md) を *date* の *datepart* に解決できる式。 `DATEADD` は、*number* に関してユーザー定義の変数値を受け取ります。 `DATEADD` は、小数を持つ指定 *number* 値に切り捨てを行います。 この状況で *number* 値が丸められることはありません。
  
*date*  
次のいずれかの値に解決できる式。 

+ **date**
+ **datetime**
+ **datetimeoffset**
+ **datetime2** 
+ **smalldatetime**
+ **time**

*date* の場合、`DATEADD` では、列式、式、文字列リテラル、ユーザー定義の変数が受け入れられます。 文字列リテラル値は **datetime** に解決する必要があります。 あいまいさの問題を排除するために、4 桁の西暦を使用してください。 2 桁の西暦については、「[two digit year cutoff サーバー構成オプションの構成](../../database-engine/configure-windows/configure-the-two-digit-year-cutoff-server-configuration-option.md)」を参照してください。
  
## <a name="return-types"></a>戻り値の型

このメソッドの戻り値のデータ型は動的です。 戻り値の型は、`date` に与えられた引数によって異なります。 `date` の値が文字列リテラル日付であれば、`DATEADD` からは **datetime** 値が返されます。 別の有効な入力データ型が `date` に指定された場合、`DATEADD` からは同じデータ型が返されます。 `DATEADD` は、文字列リテラルの秒の小数点以下桁数 (.nnn) が 3 を超えるか、文字列リテラルにタイム ゾーン オフセット部分が含まれる場合、エラーを出します。
  
## <a name="return-value"></a>戻り値  
  
## <a name="datepart-argument"></a>datepart 引数  
**dayofyear**、**day**、および **weekday** は同じ値を返します。
  
*-各日付構成要素とその省略形は、同じ値を返します。*
  
次に当てはまる場合:

+ *datepart* が **month** である
+ *date* 月の日数が戻り値の月の日数より多い
+ *date* 日が戻り値の月に存在しない

`DATEADD` は、戻り値の月の最後の日を返します。 たとえば、9 月の日数が 30 日である場合、次のステートメントは、2006-09-30 00:00:00.000 を返します。
  
```sql
SELECT DATEADD(month, 1, '20060830');
SELECT DATEADD(month, 1, '20060831');
```
  
## <a name="number-argument"></a>number 引数  
*number* 引数は、**int** の範囲を超えることはできません。次のステートメントでは、*number* の引数が **int** の範囲を 1 超えています。 これらのステートメントはいずれも次のエラー メッセージを返します。 "`Msg 8115, Level 16, State 2, Line 1. Arithmetic overflow error converting expression to data type int."`
  
```sql
SELECT DATEADD(year,2147483648, '20060731');  
SELECT DATEADD(year,-2147483649, '20060731');  
```  
  
## <a name="date-argument"></a>date 引数  
`DATEADD` は、そのデータ型の範囲を超える値まで増える *date* 引数を受け取りません。 次のステートメントでは、*date* 値に加算された *number* 値は *date* データ型の範囲を超えています。 `DATEADD` は次のエラー メッセージを返します。 "`Msg 517, Level 16, State 1, Line 1 Adding a value to a 'datetime' column caused overflow`."
  
```sql
SELECT DATEADD(year,2147483647, '20060731');  
SELECT DATEADD(year,-2147483647, '20060731');  
```  
  
## <a name="return-values-for-a-smalldatetime-date-and-a-second-or-fractional-seconds-datepart"></a>smalldatetime に対する戻り値 (日付および秒または 1 秒未満の秒)  
[smalldatetime](../../t-sql/data-types/smalldatetime-transact-sql.md) 値の秒の部分は常に 00 です。 **smalldatetime** *date* 値の場合、次が適用されます。 

-   **second** の *datepart* と -30 から +29 の *number* 値の場合、`DATEADD` によって変更が行われることはありません。  
-   **second** の *datepart* と、-30 未満、+29 超の *number* 値の場合、`DATEADD` は 1 分から始まる加算を実行します。  
-   **millisecond** の *datepart* と -30001 から +29998 の *number* 値の場合、`DATEADD` によって変更が行われることはありません。  
-   **second** の *datepart* と、-30001 未満、+29998 超の *number* 値の場合、`DATEADD` は 1 分から始まる加算を実行します。  
  
## <a name="remarks"></a>解説  
次の句で `DATEADD` を使用します。

+ GROUP BY
+ HAVING
+ ORDER BY
+ SELECT \<list>
+ WHERE
  
## <a name="fractional-seconds-precision"></a>秒の小数部の有効桁数
`DATEADD` は、*date* データ型の **smalldatetime**、**date**、**datetime** について、**microsecond** または **nanosecond** の *datepart* に加算を許可しません。
  
ミリ秒の小数点以下桁数は 3 (.123) です。マイクロ秒の小数点以下桁数は 6 (.123456) です。ナノ秒の小数点以下桁数は 9 (.123456789) です。 **time**、**datetime2**、および **datetimeoffset** データ型の小数点以下桁数は最大 7 (.1234567) です。 **nanosecond** の *datepart* については、*date* の 1 秒未満の秒を増やす前に、*number* を 100 にする必要があります。 1 から 49 の *number* は 0 に切り捨てられ、50 から 99 は 100 に切り上げられます。
  
次のステートメントは、**millisecond**、**microsecond**、または **nanosecond** の *datepart* を加算します。
  
```sql
DECLARE @datetime2 datetime2 = '2007-01-01 13:10:10.1111111';  
SELECT '1 millisecond', DATEADD(millisecond,1,@datetime2)  
UNION ALL  
SELECT '2 milliseconds', DATEADD(millisecond,2,@datetime2)  
UNION ALL  
SELECT '1 microsecond', DATEADD(microsecond,1,@datetime2)  
UNION ALL  
SELECT '2 microseconds', DATEADD(microsecond,2,@datetime2)  
UNION ALL  
SELECT '49 nanoseconds', DATEADD(nanosecond,49,@datetime2)  
UNION ALL  
SELECT '50 nanoseconds', DATEADD(nanosecond,50,@datetime2)  
UNION ALL  
SELECT '150 nanoseconds', DATEADD(nanosecond,150,@datetime2);  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
1 millisecond     2007-01-01 13:10:10.1121111  
2 milliseconds    2007-01-01 13:10:10.1131111  
1 microsecond     2007-01-01 13:10:10.1111121  
2 microseconds    2007-01-01 13:10:10.1111131  
49 nanoseconds    2007-01-01 13:10:10.1111111  
50 nanoseconds    2007-01-01 13:10:10.1111112  
150 nanoseconds   2007-01-01 13:10:10.1111113  
```  
  
## <a name="time-zone-offset"></a>タイム ゾーン オフセット
`DATEADD` は、タイム ゾーン オフセットの加算を許可しません。
  
## <a name="examples"></a>例  

### <a name="a-incrementing-datepart-by-an-interval-of-1"></a>A. datepart を 1 単位増やす  
次の各ステートメントは、*datepart* を 1 単位増やします。
  
```sql
DECLARE @datetime2 datetime2 = '2007-01-01 13:10:10.1111111';  
SELECT 'year', DATEADD(year,1,@datetime2)  
UNION ALL  
SELECT 'quarter',DATEADD(quarter,1,@datetime2)  
UNION ALL  
SELECT 'month',DATEADD(month,1,@datetime2)  
UNION ALL  
SELECT 'dayofyear',DATEADD(dayofyear,1,@datetime2)  
UNION ALL  
SELECT 'day',DATEADD(day,1,@datetime2)  
UNION ALL  
SELECT 'week',DATEADD(week,1,@datetime2)  
UNION ALL  
SELECT 'weekday',DATEADD(weekday,1,@datetime2)  
UNION ALL  
SELECT 'hour',DATEADD(hour,1,@datetime2)  
UNION ALL  
SELECT 'minute',DATEADD(minute,1,@datetime2)  
UNION ALL  
SELECT 'second',DATEADD(second,1,@datetime2)  
UNION ALL  
SELECT 'millisecond',DATEADD(millisecond,1,@datetime2)  
UNION ALL  
SELECT 'microsecond',DATEADD(microsecond,1,@datetime2)  
UNION ALL  
SELECT 'nanosecond',DATEADD(nanosecond,1,@datetime2);  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
Year         2008-01-01 13:10:10.1111111  
quarter      2007-04-01 13:10:10.1111111  
month        2007-02-01 13:10:10.1111111  
dayofyear    2007-01-02 13:10:10.1111111  
day          2007-01-02 13:10:10.1111111  
week         2007-01-08 13:10:10.1111111  
weekday      2007-01-02 13:10:10.1111111  
hour         2007-01-01 14:10:10.1111111  
minute       2007-01-01 13:11:10.1111111  
second       2007-01-01 13:10:11.1111111  
millisecond  2007-01-01 13:10:10.1121111  
microsecond  2007-01-01 13:10:10.1111121  
nanosecond   2007-01-01 13:10:10.1111111  
```  
  
### <a name="b-incrementing-more-than-one-level-of-datepart-in-one-statement"></a>B. 単一のステートメントで datepart を複数レベル増やす  
次の各ステートメントは、*date* の *datepart* を付加的に繰り上げる *number* だけ *datepart* を増やします。
  
```sql
DECLARE @datetime2 datetime2;  
SET @datetime2 = '2007-01-01 01:01:01.1111111';  
--Statement                                 Result     
-------------------------------------------------------------------   
SELECT DATEADD(quarter,4,@datetime2);     --2008-01-01 01:01:01.1111111  
SELECT DATEADD(month,13,@datetime2);      --2008-02-01 01:01:01.1111111  
SELECT DATEADD(dayofyear,365,@datetime2); --2008-01-01 01:01:01.1111111  
SELECT DATEADD(day,365,@datetime2);       --2008-01-01 01:01:01.1111111  
SELECT DATEADD(week,5,@datetime2);        --2007-02-05 01:01:01.1111111  
SELECT DATEADD(weekday,31,@datetime2);    --2007-02-01 01:01:01.1111111  
SELECT DATEADD(hour,23,@datetime2);       --2007-01-02 00:01:01.1111111  
SELECT DATEADD(minute,59,@datetime2);     --2007-01-01 02:00:01.1111111  
SELECT DATEADD(second,59,@datetime2);     --2007-01-01 01:02:00.1111111  
SELECT DATEADD(millisecond,1,@datetime2); --2007-01-01 01:01:01.1121111  
```  
  
### <a name="c-using-expressions-as-arguments-for-the-number-and-date-parameters"></a>C. number パラメーターと date パラメーターの引数として式を使用する  
次の例では、*number* パラメーターと *date* パラメーターの引数として、さまざまな種類の式を使用しています。 使用例では、AdventureWorks データベースを使用します。
  
#### <a name="specifying-a-column-as-date"></a>列を date として指定する  
次の例では、`OrderDate` 列の各値に `2` 日を加算し、`PromisedShipDate` という名前の新しい列を作成します。
  
```sql
SELECT SalesOrderID  
    ,OrderDate   
    ,DATEADD(day,2,OrderDate) AS PromisedShipDate  
FROM Sales.SalesOrderHeader;  
```  
  
次に結果セットの一部を示します。
  
```
SalesOrderID OrderDate               PromisedShipDate  
------------ ----------------------- -----------------------  
43659        2005-07-01 00:00:00.000 2005-07-03 00:00:00.000  
43660        2005-07-01 00:00:00.000 2005-07-03 00:00:00.000  
43661        2005-07-01 00:00:00.000 2005-07-03 00:00:00.000  
...  
43702        2005-07-02 00:00:00.000 2005-07-04 00:00:00.000  
43703        2005-07-02 00:00:00.000 2005-07-04 00:00:00.000  
43704        2005-07-02 00:00:00.000 2005-07-04 00:00:00.000  
43705        2005-07-02 00:00:00.000 2005-07-04 00:00:00.000  
43706        2005-07-03 00:00:00.000 2005-07-05 00:00:00.000  
...  
43711        2005-07-04 00:00:00.000 2005-07-06 00:00:00.000  
43712        2005-07-04 00:00:00.000 2005-07-06 00:00:00.000  
...  
43740        2005-07-11 00:00:00.000 2005-07-13 00:00:00.000  
43741        2005-07-12 00:00:00.000 2005-07-14 00:00:00.000  
  
```  
  
#### <a name="specifying-user-defined-variables-as-number-and-date"></a>number および date にユーザー定義変数を指定する  
次の例では、*number* と *date* の引数としてユーザー定義変数を指定しています。
  
```sql
DECLARE @days INT = 365,   
        @datetime DATETIME = '2000-01-01 01:01:01.111'; /* 2000 was a leap year */;  
SELECT DATEADD(day, @days, @datetime);  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
-----------------------  
2000-12-31 01:01:01.110  
  
(1 row(s) affected)  
```  
  
#### <a name="specifying-scalar-system-function-as-date"></a>スカラー システム関数を date として指定する  
この例では、*date* の `SYSDATETIME` を指定しています。 返される厳密な値は、ステートメント実行の日時によって変わります。
  
```sql
SELECT DATEADD(month, 1, SYSDATETIME());  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
---------------------------  
2013-02-06 14:29:59.6727944  
  
(1 row(s) affected)  
```  
  
#### <a name="specifying-scalar-subqueries-and-scalar-functions-as-number-and-date"></a>number および date にスカラー サブクエリやスカラー関数を指定する  
次の例では、*number* と *date* の引数として、スカラー サブクエリ (`MAX(ModifiedDate)`) を使用しています。 `(SELECT TOP 1 BusinessEntityID FROM Person.Person)` は、値リストから *number* 引数を選択する方法を紹介するために用意した、数値パラメーターの架空の引数です。
  
```sql
SELECT DATEADD(month,(SELECT TOP 1 BusinessEntityID FROM Person.Person),  
    (SELECT MAX(ModifiedDate) FROM Person.Person));  
```  
  
#### <a name="specifying-numeric-expressions-and-scalar-system-functions-as-number-and-date"></a>number および date として数値式やスカラー システム関数を指定する  
次の例では、*number* と *date* の引数として、数値式 (-`(10/2))`、[単項演算子](../../mdx/unary-operators.md) (`-`)、[算術演算子](../../mdx/arithmetic-operators.md) (`/`)、スカラー システム関数 (`SYSDATETIME`) を使用しています。
  
```sql
SELECT DATEADD(month,-(10/2), SYSDATETIME());  
```  
  
#### <a name="specifying-ranking-functions-as-number"></a>number として順位付け関数を指定する  
次の例では、*number* の引数として順位付け関数を使用しています。
  
```sql
SELECT p.FirstName, p.LastName  
    ,DATEADD(day,ROW_NUMBER() OVER (ORDER BY  
        a.PostalCode),SYSDATETIME()) AS 'Row Number'  
FROM Sales.SalesPerson AS s   
    INNER JOIN Person.Person AS p   
        ON s.BusinessEntityID = p.BusinessEntityID  
    INNER JOIN Person.Address AS a   
        ON a.AddressID = p.BusinessEntityID  
WHERE TerritoryID IS NOT NULL   
    AND SalesYTD <> 0;  
```  
  
#### <a name="specifying-an-aggregate-window-function-as-number"></a>number として集計関数を指定する  
次の例では、*number* の引数として集計関数を使用しています。
  
```sql
SELECT SalesOrderID, ProductID, OrderQty  
    ,DATEADD(day,SUM(OrderQty)   
        OVER(PARTITION BY SalesOrderID),SYSDATETIME()) AS 'Total'  
FROM Sales.SalesOrderDetail   
WHERE SalesOrderID IN(43659,43664);  
GO  
```  
  
  
## <a name="see-also"></a>関連項目
[CAST および CONVERT &#40;Transact-SQL&#41;](../../t-sql/functions/cast-and-convert-transact-sql.md)
  
  

