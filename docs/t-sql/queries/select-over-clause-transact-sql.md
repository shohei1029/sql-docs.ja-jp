---
title: OVER 句 (Transact-SQL) | Microsoft Docs
description: OVER 句の Transact-SQL リファレンス。クエリ結果セット内でユーザー指定の行セットが定義されます。
ms.date: 08/11/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- OVER_TSQL
- OVER
dev_langs:
- TSQL
helpviewer_keywords:
- order of rowsets [SQL Server]
- rowsets [SQL Server], windowing
- window function
- partitions [SQL Server], rowsets
- clauses [SQL Server], OVER
- rowsets [SQL Server], partitioning
- rowsets [SQL Server], ordering
- OVER clause
ms.assetid: ddcef3a6-0341-43e0-ae73-630484b7b398
author: VanMSFT
ms.author: vanto
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 7fe2cf996f17a93b3081326a015095f482107850
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104753902"
---
# <a name="select---over-clause-transact-sql"></a>SELECT - OVER 句 (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  関連するウィンドウ関数が適用される前に、行セットのパーティション処理と並べ替えを決定します。 つまり、OVER 句はクエリ結果セット内のウィンドウまたはユーザー指定の行セットを定義します。 その後、ウィンドウ関数はウィンドウ内の各行の値を計算します。 関数で OVER 句を使用すると、移動平均、累積集計、集計途中経過、グループ結果ごとの上位 N などの集計値を計算できます。  
  
-   [順位付け関数](../../t-sql/functions/ranking-functions-transact-sql.md)  
  
-   [集計関数](../../t-sql/functions/aggregate-functions-transact-sql.md)  
  
-   [分析関数](../../t-sql/functions/analytic-functions-transact-sql.md)  
  
-   [NEXT VALUE FOR 関数](../../t-sql/functions/next-value-for-transact-sql.md)  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql
-- Syntax for SQL Server, Azure SQL Database, and Azure Synapse Analytics  
  
OVER (   
       [ <PARTITION BY clause> ]  
       [ <ORDER BY clause> ]   
       [ <ROW or RANGE clause> ]  
      )  
  
<PARTITION BY clause> ::=  
PARTITION BY value_expression , ... [ n ]  
  
<ORDER BY clause> ::=  
ORDER BY order_by_expression  
    [ COLLATE collation_name ]   
    [ ASC | DESC ]   
    [ ,...n ]  
  
<ROW or RANGE clause> ::=  
{ ROWS | RANGE } <window frame extent>  
  
<window frame extent> ::=   
{   <window frame preceding>  
  | <window frame between>  
}  
<window frame between> ::=   
  BETWEEN <window frame bound> AND <window frame bound>  
  
<window frame bound> ::=   
{   <window frame preceding>  
  | <window frame following>  
}  
  
<window frame preceding> ::=   
{  
    UNBOUNDED PRECEDING  
  | <unsigned_value_specification> PRECEDING  
  | CURRENT ROW  
}  
  
<window frame following> ::=   
{  
    UNBOUNDED FOLLOWING  
  | <unsigned_value_specification> FOLLOWING  
  | CURRENT ROW  
}  
  
<unsigned value specification> ::=   
{  <unsigned integer literal> }  
  
```  
  
```syntaxsql
-- Syntax for Parallel Data Warehouse  
  
OVER ( [ PARTITION BY value_expression ] [ order_by_clause ] )  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数

ウィンドウ関数の `OVER` 句には、次の関数が含まれる場合があります。
- [PARTITION BY](#partition-by)。クエリ結果セットをパーティションに分割します。
- [ORDER BY](#order-by)。結果セットの各パーティション内の行の論理的な順序を定義します。 
- [ROWS/RANGE](#rows-or-range)。パーティション内の開始点と終了点を指定することで、パーティション内の行をさらに制限します。 これには `ORDER BY` 引数が必要です。また、`ORDER BY` 引数が指定されている場合、既定値はパーティションの先頭から現在の要素までです。

引数を指定しない場合、そのウィンドウ関数は結果セット全体に適用されます。
```sql
select 
      object_id
    , [min] = min(object_id) over()
    , [max] = max(object_id) over()
from sys.objects
```
 
|object_id | 分 | max |
|---|---|---|
| 3 | 3 | 2139154666 |
| 5 | 3 | 2139154666 |
| ... | ... | ... |
| 2123154609 |  3 | 2139154666 |
| 2139154666 |  3 | 2139154666 |

### <a name="partition-by"></a>PARTITION BY  
 クエリ結果セットをパーティションに分割します。 ウィンドウ関数は各パーティションに対して個別に適用され、各パーティションで計算が再開されます。  

```syntaxsql
PARTITION BY *value_expression* 
```
 
 PARTITION BY を指定しない場合、関数ではクエリ結果セットのすべての行を 1 つのパーティションとして扱います。
`ORDER BY` 句を指定しない場合、関数はそのパーティション内のすべての行に適用されます。
  
#### <a name="partition-by-value_expression"></a>PARTITION BY *value_expression*  
 行セットをパーティションに分割するときに使用する列を指定します。 *value_expression* で参照できるのは FROM 句で取得した列だけです。 *value_expression* は、選択リストの式または別名を参照できません。 *value_expression* には、列式、スカラー サブクエリ、スカラー関数、またはユーザー定義変数を指定できます。 
 
 ```sql
 select 
      object_id, type
    , [min] = min(object_id) over(partition by type)
    , [max] = max(object_id) over(partition by type)
from sys.objects
```

|object_id | type | 分 | max |
|---|---|---|---|
| 68195293  | PK    | 68195293  | 711673583 |
| 631673298 | PK    | 68195293  | 711673583 |
| 711673583 | PK    | 68195293  | 711673583 |
| ... | ... | ... |
| 3 | S | 3 | 98 |
| 5 | S |   3   | 98 |
| ... | ... | ... |
| 98    | S |   3   | 98 |
| ... | ... | ... |
  
### <a name="order-by"></a>ORDER BY  

```syntaxsql
ORDER BY *order_by_expression* [COLLATE *collation_name*] [ASC|DESC]  
```

 結果セットの各パーティション内の行の論理的な順序を定義します。 つまり、ウィンドウ関数の計算が実行される論理的な順序を指定します。 
 - これが指定されていない場合、既定の順序は `ASC` であり、ウィンドウ関数ではパーティション内のすべての行を使用します。
 - これが指定されており、かつ ROWS/RANGE が指定されていない場合、省略可能な ROWS/RANGE の指定を受け入れることができる関数 (`min` や `max`など) によって、既定の `RANGE UNBOUNDED PRECEDING AND CURRENT ROW` がウィンドウ フレームの既定値として使用されます。 
 
```sql
select 
      object_id, type
    , [min] = min(object_id) over(partition by type order by object_id)
    , [max] = max(object_id) over(partition by type order by object_id)
from sys.objects
```

|object_id | type | 分 | max |
|---|---|---|---|
| 68195293  | PK    | 68195293  | 68195293 |
| 631673298 | PK    | 68195293  | 631673298 |
| 711673583 | PK    | 68195293  | 711673583 |
| ... | ... | ... |
| 3 | S | 3 | 3 |
| 5 | S |   3 | 5 |
| 6 | S |   3 | 6 |
| ... | ... | ... |
| 97    | S |   3 | 97 |
| 98    | S |   3 | 98 |
| ... | ... | ... |


 *order_by_expression*  
 並べ替えに使用する列または式を指定します。 *order_by_expression* で参照できるのは FROM 句で取得した列だけです。 整数を指定して列の名前または別名を表すことはできません。  
  
 COLLATE *collation_name*  
 *collation_name* で指定した照合順序に従って ORDER BY 操作を実行することを指定します。 *collation_name* には、Windows 照合順序名または SQL 照合順序名を指定できます。 詳細については、「 [Collation and Unicode Support](../../relational-databases/collations/collation-and-unicode-support.md)」を参照してください。 COLLATE は、**char**、**varchar**、**nchar**、**nvarchar** 型の列にのみ適用できます。  
  
 **ASC** | DESC  
 指定した列の値を昇順と降順のどちらで並べ替えるかを指定します。 ASC が既定の並べ替え順序です。 NULL 値は最小値として扱われます。  
  
### <a name="rows-or-range"></a>ROWS または RANGE  
**適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降。 
  
 パーティション内の開始点と終了点を指定することで、パーティション内の行をさらに制限します。 これは、論理アソシエーションまたは物理アソシエーションによって現在の行を基準に行の範囲を指定することで行います。 物理アソシエーションは ROWS 句を使用することで実現されます。  
  
 ROWS 句では、現在行の前または後にある固定数の行を指定することにより、パーティション内の行が限定されます。 または、RANGE 句は、現在行の値を基準とする値の範囲を指定することにより、パーティション内の行を論理的に限定します。 前後の行は、ORDER BY 句での順序に基づいて定義されます。 ウィンドウ フレーム "RANGE ...CURRENT ROW ..." には、ORDER BY 式に現在行と同じ値を持つすべての行が含まれます。 たとえば、ROWS BETWEEN 2 PRECEDING AND CURRENT ROW は、関数の操作対象である行のウィンドウが、現在行の 2 行前の行から現在行までの 3 行 (現在行を含みます) であることを意味します。  
 
```sql
select
      object_id
    , [preceding]   = count(*) over(order by object_id ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW )
    , [central] = count(*) over(order by object_id ROWS BETWEEN 2 PRECEDING AND 2 FOLLOWING )
    , [following]   = count(*) over(order by object_id ROWS BETWEEN CURRENT ROW AND UNBOUNDED FOLLOWING)
from sys.objects
order by object_id asc
```

|object_id | preceding | central | following |
|---|---|---|---|
| 3 | 1 | 3 | 156 |
| 5 | 2 | 4 | 155 |
| 6 | 3 | 5 | 154 |
| 7 | 4 | 5 | 153 |
| 8 | 5 | 5 | 152 |
| ...   | ...   | ...   | ... |
| 2112726579    | 153   | 5 | 4 |
| 2119678599    | 154   | 5 | 3 |
| 2123154609    | 155   | 4 | 2 |
| 2139154666    | 156   | 3 | 1 | 
  
> [!NOTE]  
>  ROWS または RANGE を使用する場合は、ORDER BY 句を指定する必要があります。 ORDER BY に複数の順序式が含まれる場合、ROW FOR RANGE では、現在行を決定するときに ORDER BY リスト内のすべての列が考慮されます。  
  
#### <a name="unbounded-preceding"></a>UNBOUNDED PRECEDING  
**適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降。  
  
 ウィンドウがパーティションの最初の行から開始することを指定します。 UNBOUNDED PRECEDING はウィンドウの開始位置としてのみ指定できます。  
  
 \<unsigned value specification> PRECEDING  
 \<unsigned value specification> は、現在行より前にある行または値の数を示します。 RANGE に対してはこの指定を使用できません。  
  
#### <a name="current-row"></a>CURRENT ROW  
**適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降。 
  
 ウィンドウが現在の行 (ROWS の場合) または現在の値 (RANGE の場合) で開始または終了することを指定します。 CURRENT ROW は開始位置としても終了位置としても指定できます。  
  
#### <a name="between-and"></a>BETWEEN AND  
**適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降。 
  
```syntaxsql
BETWEEN <window frame bound > AND <window frame bound >  
```
 ROWS または RANGE で使用し、ウィンドウの下限 (開始) と上限 (終了) の境界位置を指定します。 前の \<window frame bound> は境界の開始位置を定義し、後の \<window frame bound> は境界の終了位置を定義します。 上限を下限より小さくすることはできません。  
  
#### <a name="unbounded-following"></a>UNBOUNDED FOLLOWING  
**適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降。 
  
 ウィンドウがパーティションの最後の行で終了することを指定します。 UNBOUNDED FOLLOWING はウィンドウの終了位置としてのみ指定できます。 たとえば、RANGE BETWEEN CURRENT ROW AND UNBOUNDED FOLLOWING では、現在行から開始してパーティションの最後の行で終了するウィンドウが定義されます。  
  
 \<unsigned value specification> FOLLOWING  
 \<unsigned value specification> は、現在行より後にある行または値の数を示します。 \<unsigned value specification> FOLLOWING をウィンドウの開始位置として指定するときは、終了位置を \<unsigned value specification> FOLLOWING にする必要があります。 たとえば、ROWS BETWEEN 2 FOLLOWING AND 10 FOLLOWING では、現在行より後の 2 行目から開始して現在行より後の 10 行目で終了するウィンドウが定義されます。 RANGE に対してはこの指定を使用できません。  
  
 unsigned integer literal  
**適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降。  
  
 現在の行または値より前または後にある行または値の数を指定する正の整数リテラル (0 を含む) です。 この指定は ROWS に対してのみ有効です。  
  
## <a name="general-remarks"></a>全般的な解説  
 1 つの FROM 句を使用した 1 つのクエリで、複数のウィンドウ関数を使用できます。 各関数の OVER 句は、パーティション分割や並べ替えが異なっていてもかまいません。  
  
 PARTITION BY を指定しない場合、関数ではクエリ結果セットのすべての行が 1 つのグループとして扱われます。 
 
### <a name="important"></a>重要:

ROWS/RANGE を指定し、\<window frame preceding> を \<window frame extent> に対して使用すると (短い構文)、ウィンドウ フレーム境界の開始位置に対してはこの指定が使用され、境界の終了位置に対しては CURRENT ROW が使用されます。 たとえば、"ROWS 5 PRECEDING" は "ROWS BETWEEN 5 PRECEDING AND CURRENT ROW" と同じです。  
  
> [!NOTE]
> ORDER BY を指定しないと、パーティション全体がウィンドウ フレームに対して使用されます。 これは、ORDER BY 句を必要としない関数にのみ適用されます。 ROWS/RANGE を指定しないで ORDER BY を指定すると、RANGE UNBOUNDED PRECEDING AND CURRENT ROW がウィンドウ フレームに対する既定値として使用されます。 これは、オプションの ROWS/RANGE 指定を受け入れることができる関数に対してのみ適用されます。 たとえば、順位付け関数は ROWS/RANGE を受け入れないため、ORDER BY があり、ROWS/RANGE がなくても、このウィンドウ フレームは適用されません。  
    
## <a name="limitations-and-restrictions"></a>制限事項と制約事項  
 OVER 句を CHECKSUM 集計関数と共に使用することはできません。  
  
 RANGE を \<unsigned value specification> PRECEDING または \<unsigned value specification> FOLLOWING と共に使用することはできません。  
  
 OVER 句と共に使用される順位付け関数、集計関数、または分析関数によっては、\<ORDER BY clause> と \<ROWS and RANGE clause> のどちらか一方または両方がサポートされない場合があります。  
  
## <a name="examples"></a>例  
  
### <a name="a-using-the-over-clause-with-the-row_number-function"></a>A. OVER 句を ROW_NUMBER 関数と共に使用する  
 次の例では、OVER 句と ROW_NUMBER 関数を使用して、パーティション内の各行の行番号を表示する方法を示します。 OVER 句に指定した ORDER BY 句によって、列 `SalesYTD` を基準に各パーティション内の行の順序付けが行われます。 SELECT ステートメントの ORDER BY 句によって、クエリ結果セット全体が返される順序が決まります。  
  
```sql  
USE AdventureWorks2012;  
GO  
SELECT ROW_NUMBER() OVER(PARTITION BY PostalCode ORDER BY SalesYTD DESC) AS "Row Number",   
    p.LastName, s.SalesYTD, a.PostalCode  
FROM Sales.SalesPerson AS s   
    INNER JOIN Person.Person AS p   
        ON s.BusinessEntityID = p.BusinessEntityID  
    INNER JOIN Person.Address AS a   
        ON a.AddressID = p.BusinessEntityID  
WHERE TerritoryID IS NOT NULL   
    AND SalesYTD <> 0  
ORDER BY PostalCode;  
GO  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 ```
 Row Number      LastName                SalesYTD              PostalCode 
 --------------- ----------------------- --------------------- ---------- 
 1               Mitchell                4251368.5497          98027 
 2               Blythe                  3763178.1787          98027 
 3               Carson                  3189418.3662          98027 
 4               Reiter                  2315185.611           98027 
 5               Vargas                  1453719.4653          98027  
 6               Ansman-Wolfe            1352577.1325          98027  
 1               Pak                     4116871.2277          98055  
 2               Varkey Chudukatil       3121616.3202          98055  
 3               Saraiva                 2604540.7172          98055  
 4               Ito                     2458535.6169          98055  
 5               Valdez                  1827066.7118          98055  
 6               Mensa-Annan             1576562.1966          98055  
 7               Campbell                1573012.9383          98055  
 8               Tsoflias                1421810.9242          98055
 ```  
  
### <a name="b-using-the-over-clause-with-aggregate-functions"></a>B. OVER 句を集計関数と共に使用する  
 次の例では、クエリによって返されるすべての行に対して、`OVER` 句と集計関数を使用しています。 この例では、`OVER` 句を使用した方が、サブクエリを使用して集計値を取得するより効率的です。  
  
```sql  
USE AdventureWorks2012;  
GO  
SELECT SalesOrderID, ProductID, OrderQty  
    ,SUM(OrderQty) OVER(PARTITION BY SalesOrderID) AS Total  
    ,AVG(OrderQty) OVER(PARTITION BY SalesOrderID) AS "Avg"  
    ,COUNT(OrderQty) OVER(PARTITION BY SalesOrderID) AS "Count"  
    ,MIN(OrderQty) OVER(PARTITION BY SalesOrderID) AS "Min"  
    ,MAX(OrderQty) OVER(PARTITION BY SalesOrderID) AS "Max"  
FROM Sales.SalesOrderDetail   
WHERE SalesOrderID IN(43659,43664);  
GO  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
SalesOrderID ProductID   OrderQty Total       Avg         Count       Min    Max  
------------ ----------- -------- ----------- ----------- ----------- ------ ------  
43659        776         1        26          2           12          1      6  
43659        777         3        26          2           12          1      6  
43659        778         1        26          2           12          1      6  
43659        771         1        26          2           12          1      6  
43659        772         1        26          2           12          1      6  
43659        773         2        26          2           12          1      6  
43659        774         1        26          2           12          1      6  
43659        714         3        26          2           12          1      6  
43659        716         1        26          2           12          1      6  
43659        709         6        26          2           12          1      6  
43659        712         2        26          2           12          1      6  
43659        711         4        26          2           12          1      6  
43664        772         1        14          1           8           1      4  
43664        775         4        14          1           8           1      4  
43664        714         1        14          1           8           1      4  
43664        716         1        14          1           8           1      4  
43664        777         2        14          1           8           1      4  
43664        771         3        14          1           8           1      4  
43664        773         1        14          1           8           1      4  
43664        778         1        14          1           8           1      4  
```  
  
 次の例では、`OVER` 句を集計関数と共に計算値の中で使用します。  
  
```sql  
USE AdventureWorks2012;  
GO  
SELECT SalesOrderID, ProductID, OrderQty  
    ,SUM(OrderQty) OVER(PARTITION BY SalesOrderID) AS Total  
    ,CAST(1. * OrderQty / SUM(OrderQty) OVER(PARTITION BY SalesOrderID)   
        *100 AS DECIMAL(5,2))AS "Percent by ProductID"  
FROM Sales.SalesOrderDetail   
WHERE SalesOrderID IN(43659,43664);  
GO  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)] 集計は `SalesOrderID` ごとに計算され、`Percent by ProductID` は各 `SalesOrderID` の各行に対して計算されることに注意してください。  
  
```  
SalesOrderID ProductID   OrderQty Total       Percent by ProductID  
------------ ----------- -------- ----------- ---------------------------------------  
43659        776         1        26          3.85  
43659        777         3        26          11.54  
43659        778         1        26          3.85  
43659        771         1        26          3.85  
43659        772         1        26          3.85  
43659        773         2        26          7.69  
43659        774         1        26          3.85  
43659        714         3        26          11.54  
43659        716         1        26          3.85  
43659        709         6        26          23.08  
43659        712         2        26          7.69  
43659        711         4        26          15.38  
43664        772         1        14          7.14  
43664        775         4        14          28.57  
43664        714         1        14          7.14  
43664        716         1        14          7.14  
43664        777         2        14          14.29  
43664        771         3        14          21.4  
43664        773         1        14          7.14  
43664        778         1        14          7.14  
  
 (20 row(s) affected)  
```  
  
### <a name="c-producing-a-moving-average-and-cumulative-total"></a>C. 移動平均および累積合計を生成する  
 次の例では、OVER 句を指定した AVG および SUM 関数を使用して、`Sales.SalesPerson` テーブルに各区域の年間売り上げの移動平均と累積合計を入力します。 データは `TerritoryID` によってパーティションに分割され、`SalesYTD` によって論理的に順序付けされます。 つまり、AVG 関数は年を基にして区域ごとに計算されます。 `TerritoryID` 1 の 2005 年については、その年の 2 人の営業担当者を表す 2 行があります。 これら 2 行の平均売上が計算された後、2006 年の売上を表す 3 番目の行が計算に組み込まれます。  
  
```sql  
USE AdventureWorks2012;  
GO  
SELECT BusinessEntityID, TerritoryID   
   ,DATEPART(yy,ModifiedDate) AS SalesYear  
   ,CONVERT(VARCHAR(20),SalesYTD,1) AS  SalesYTD  
   ,CONVERT(VARCHAR(20),AVG(SalesYTD) OVER (PARTITION BY TerritoryID   
                                            ORDER BY DATEPART(yy,ModifiedDate)   
                                           ),1) AS MovingAvg  
   ,CONVERT(VARCHAR(20),SUM(SalesYTD) OVER (PARTITION BY TerritoryID   
                                            ORDER BY DATEPART(yy,ModifiedDate)   
                                            ),1) AS CumulativeTotal  
FROM Sales.SalesPerson  
WHERE TerritoryID IS NULL OR TerritoryID < 5  
ORDER BY TerritoryID,SalesYear;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
BusinessEntityID TerritoryID SalesYear   SalesYTD             MovingAvg            CumulativeTotal  
---------------- ----------- ----------- -------------------- -------------------- --------------------  
274              NULL        2005        559,697.56           559,697.56           559,697.56  
287              NULL        2006        519,905.93           539,801.75           1,079,603.50  
285              NULL        2007        172,524.45           417,375.98           1,252,127.95  
283              1           2005        1,573,012.94         1,462,795.04         2,925,590.07  
280              1           2005        1,352,577.13         1,462,795.04         2,925,590.07  
284              1           2006        1,576,562.20         1,500,717.42         4,502,152.27  
275              2           2005        3,763,178.18         3,763,178.18         3,763,178.18  
277              3           2005        3,189,418.37         3,189,418.37         3,189,418.37  
276              4           2005        4,251,368.55         3,354,952.08         6,709,904.17  
281              4           2005        2,458,535.62         3,354,952.08         6,709,904.17  
  
(10 row(s) affected)  
  
```  
  
 この例では、OVER 句に PARTITION BY が含まれません。 これは、関数がクエリによって返されるすべての行に適用されることを意味します。 OVER 句で指定されている ORDER BY 句によって、AVG 関数が適用される論理的な順序が決まります。 このクエリは、WHERE 句で指定されているすべての販売区域について、年ごとの売上の移動平均を返します。 SELECT ステートメントで指定されている ORDER BY 句により、クエリの行が表示される順序が決まります。  
  
```sql  
SELECT BusinessEntityID, TerritoryID   
   ,DATEPART(yy,ModifiedDate) AS SalesYear  
   ,CONVERT(VARCHAR(20),SalesYTD,1) AS SalesYTD  
   ,CONVERT(VARCHAR(20),AVG(SalesYTD) OVER (ORDER BY DATEPART(yy,ModifiedDate)   
                                            ),1) AS MovingAvg  
   ,CONVERT(VARCHAR(20),SUM(SalesYTD) OVER (ORDER BY DATEPART(yy,ModifiedDate)   
                                            ),1) AS CumulativeTotal  
FROM Sales.SalesPerson  
WHERE TerritoryID IS NULL OR TerritoryID < 5  
ORDER BY SalesYear;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
BusinessEntityID TerritoryID SalesYear   SalesYTD             MovingAvg            CumulativeTotal  
---------------- ----------- ----------- -------------------- -------------------- --------------------  
274              NULL        2005        559,697.56           2,449,684.05         17,147,788.35  
275              2           2005        3,763,178.18         2,449,684.05         17,147,788.35  
276              4           2005        4,251,368.55         2,449,684.05         17,147,788.35  
277              3           2005        3,189,418.37         2,449,684.05         17,147,788.35  
280              1           2005        1,352,577.13         2,449,684.05         17,147,788.35  
281              4           2005        2,458,535.62         2,449,684.05         17,147,788.35  
283              1           2005        1,573,012.94         2,449,684.05         17,147,788.35  
284              1           2006        1,576,562.20         2,138,250.72         19,244,256.47  
287              NULL        2006        519,905.93           2,138,250.72         19,244,256.47  
285              NULL        2007        172,524.45           1,941,678.09         19,416,780.93  
(10 row(s) affected)  
```  
  
### <a name="d-specifying-the-rows-clause"></a>D. ROWS 句を指定する  
  
**適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降。  
  
 次の例では、ROWS 句を使用して、行の計算対象のウィンドウを、現在行からその後の *N* 行まで (この例では 1 行) と定義しています。  
  
```sql  
SELECT BusinessEntityID, TerritoryID   
    ,CONVERT(VARCHAR(20),SalesYTD,1) AS SalesYTD  
    ,DATEPART(yy,ModifiedDate) AS SalesYear  
    ,CONVERT(VARCHAR(20),SUM(SalesYTD) OVER (PARTITION BY TerritoryID   
                                             ORDER BY DATEPART(yy,ModifiedDate)   
                                             ROWS BETWEEN CURRENT ROW AND 1 FOLLOWING ),1) AS CumulativeTotal  
FROM Sales.SalesPerson  
WHERE TerritoryID IS NULL OR TerritoryID < 5;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
BusinessEntityID TerritoryID SalesYTD             SalesYear   CumulativeTotal  
---------------- ----------- -------------------- ----------- --------------------  
274              NULL        559,697.56           2005        1,079,603.50  
287              NULL        519,905.93           2006        692,430.38  
285              NULL        172,524.45           2007        172,524.45  
283              1           1,573,012.94         2005        2,925,590.07  
280              1           1,352,577.13         2005        2,929,139.33  
284              1           1,576,562.20         2006        1,576,562.20  
275              2           3,763,178.18         2005        3,763,178.18  
277              3           3,189,418.37         2005        3,189,418.37  
276              4           4,251,368.55         2005        6,709,904.17  
281              4           2,458,535.62         2005        2,458,535.62  
```  
  
 次の例では、ROWS 句と共に UNBOUNDED PRECEDING を指定しています。 その結果、ウィンドウはパーティションの最初の行から開始します。  
  
```sql  
SELECT BusinessEntityID, TerritoryID   
    ,CONVERT(VARCHAR(20),SalesYTD,1) AS SalesYTD  
    ,DATEPART(yy,ModifiedDate) AS SalesYear  
    ,CONVERT(VARCHAR(20),SUM(SalesYTD) OVER (PARTITION BY TerritoryID   
                                             ORDER BY DATEPART(yy,ModifiedDate)   
                                             ROWS UNBOUNDED PRECEDING),1) AS CumulativeTotal  
FROM Sales.SalesPerson  
WHERE TerritoryID IS NULL OR TerritoryID < 5;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
BusinessEntityID TerritoryID SalesYTD             SalesYear   CumulativeTotal  
---------------- ----------- -------------------- ----------- --------------------  
274              NULL        559,697.56           2005        559,697.56  
287              NULL        519,905.93           2006        1,079,603.50  
285              NULL        172,524.45           2007        1,252,127.95  
283              1           1,573,012.94         2005        1,573,012.94  
280              1           1,352,577.13         2005        2,925,590.07  
284              1           1,576,562.20         2006        4,502,152.27  
275              2           3,763,178.18         2005        3,763,178.18  
277              3           3,189,418.37         2005        3,189,418.37  
276              4           4,251,368.55         2005        4,251,368.55  
281              4           2,458,535.62         2005        6,709,904.17  
  
```  
  
## <a name="examples-sspdw"></a>例: [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="e-using-the-over-clause-with-the-row_number-function"></a>E. OVER 句を ROW_NUMBER 関数と共に使用する  
 次の例は、割り当てられている販売ノルマに基づいて営業担当者の ROW_NUMBER を返します。  
  
```sql  
-- Uses AdventureWorks  
  
SELECT ROW_NUMBER() OVER(ORDER BY SUM(SalesAmountQuota) DESC) AS RowNumber,  
    FirstName, LastName,   
CONVERT(VARCHAR(13), SUM(SalesAmountQuota),1) AS SalesQuota   
FROM dbo.DimEmployee AS e  
INNER JOIN dbo.FactSalesQuota AS sq  
    ON e.EmployeeKey = sq.EmployeeKey  
WHERE e.SalesPersonFlag = 1  
GROUP BY LastName, FirstName;  
```  
  
 次に結果セットの一部を示します。  
  
 ```
 RowNumber  FirstName  LastName            SalesQuota  
 ---------  ---------  ------------------  -------------  
 1          Jillian    Carson              12,198,000.00  
 2          Linda      Mitchell            11,786,000.00  
 3          Michael    Blythe              11,162,000.00  
 4          Jae        Pak                 10,514,000.00  
 ```
 
### <a name="f-using-the-over-clause-with-aggregate-functions"></a>F. OVER 句を集計関数と共に使用する  
 次の例では、OVER 句を集計関数と共に使用します。 この例では、OVER 句を使用した方が、サブクエリを使用するより効率的です。  
  
```sql  
-- Uses AdventureWorks  
  
SELECT SalesOrderNumber AS OrderNumber, ProductKey,   
       OrderQuantity AS Qty,   
       SUM(OrderQuantity) OVER(PARTITION BY SalesOrderNumber) AS Total,  
       AVG(OrderQuantity) OVER(PARTITION BY SalesOrderNumber) AS Avg,  
       COUNT(OrderQuantity) OVER(PARTITION BY SalesOrderNumber) AS Count,  
       MIN(OrderQuantity) OVER(PARTITION BY SalesOrderNumber) AS Min,  
       MAX(OrderQuantity) OVER(PARTITION BY SalesOrderNumber) AS Max  
FROM dbo.FactResellerSales   
WHERE SalesOrderNumber IN(N'SO43659',N'SO43664') AND  
      ProductKey LIKE '2%'  
ORDER BY SalesOrderNumber,ProductKey;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 ```
 OrderNumber  Product  Qty  Total  Avg  Count  Min  Max  
 -----------  -------  ---  -----  ---  -----  ---  ---  
 SO43659      218      6    16     3    5      1    6  
 SO43659      220      4    16     3    5      1    6  
 SO43659      223      2    16     3    5      1    6  
 SO43659      229      3    16     3    5      1    6  
 SO43659      235      1    16     3    5      1    6  
 SO43664      229      1     2     1    2      1    1  
 SO43664      235      1     2     1    2      1    1  
 ```
 
 次の例では、OVER 句を集計関数と共に計算値の中で使用します。 集計は `SalesOrderNumber` ごとに計算され、受注合計の割合は各 `SalesOrderNumber` の各行に対して計算されることに注意してください。  
  
```sql  
-- Uses AdventureWorks  
  
SELECT SalesOrderNumber AS OrderNumber, ProductKey AS Product,   
       OrderQuantity AS Qty,   
       SUM(OrderQuantity) OVER(PARTITION BY SalesOrderNumber) AS Total,  
       CAST(1. * OrderQuantity / SUM(OrderQuantity)   
        OVER(PARTITION BY SalesOrderNumber)   
            *100 AS DECIMAL(5,2)) AS PctByProduct  
FROM dbo.FactResellerSales   
WHERE SalesOrderNumber IN(N'SO43659',N'SO43664') AND  
      ProductKey LIKE '2%'  
ORDER BY SalesOrderNumber,ProductKey;  
```  
  
 この結果セットの最初の開始は次のようになります。  
  
 ```
 OrderNumber  Product  Qty  Total  PctByProduct  
 -----------  -------  ---  -----  ------------  
 SO43659      218      6    16     37.50  
 SO43659      220      4    16     25.00  
 SO43659      223      2    16     12.50  
 SO43659      229      2    16     18.75  
 ```
 
## <a name="see-also"></a>参照  
 [集計関数 &#40;Transact-SQL&#41;](../../t-sql/functions/aggregate-functions-transact-sql.md)   
 [分析関数 &#40;Transact-SQL&#41;](../../t-sql/functions/analytic-functions-transact-sql.md)   
 [Itzik Ben-Gan によるsqlmag.com上のウィンドウ関数と OVERに関する優れたブログ投稿](https://www.itprotoday.com/sql-server/how-use-microsoft-sql-server-2012s-window-functions-part-1)  
  
  
