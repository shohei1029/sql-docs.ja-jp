---
description: SELECT - GROUP BY- Transact-SQL
title: GROUP BY (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/01/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- GROUP
- CUBE
- ROLLUP
- GROUPING_SETS_TSQL
- GROUP BY
- GROUPING SETS
- GROUP_BY_TSQL
- GROUP_TSQL
- CUBE_TSQL
- ROLLUP_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- GROUP BY clause, about GROUP BY clause
- dividing tables into groups
- GROUPING SETS
- GROUP BY clause
- table groups [SQL Server]
- groups [SQL Server], tables divided into groups
- summary values [SQL Server]
ms.assetid: 40075914-6385-4692-b4a5-62fe44ae6cb6
author: shkale-msft
ms.author: shkale
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: d1081121051d3e033bbeec724fa3f5bb6ab4ba58
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104751552"
---
# <a name="select---group-by--transact-sql"></a>SELECT - GROUP BY- Transact-SQL
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

クエリ結果を行のグループに分割する SELECT ステートメント句。これは通常、グループごとに 1 つまたは複数の集計を実行する目的で使用されます。 SELECT ステートメントでは、グループごとに 1 つの行が返されます。  
  
## <a name="syntax"></a>構文  

 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則 (Transact-SQL)](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)
  
```syntaxsql
-- Syntax for SQL Server and Azure SQL Database   
-- ISO-Compliant Syntax  
  
GROUP BY {
      column-expression  
    | ROLLUP ( <group_by_expression> [ ,...n ] )  
    | CUBE ( <group_by_expression> [ ,...n ] )  
    | GROUPING SETS ( <grouping_set> [ ,...n ]  )  
    | () --calculates the grand total 
} [ ,...n ] 
 
<group_by_expression> ::=  
      column-expression  
    | ( column-expression [ ,...n ] )    
   
<grouping_set> ::=  
      () --calculates the grand total  
    | <grouping_set_item>  
    | ( <grouping_set_item> [ ,...n ] )  
  
<grouping_set_item> ::=  
      <group_by_expression>  
    | ROLLUP ( <group_by_expression> [ ,...n ] )  
    | CUBE ( <group_by_expression> [ ,...n ] )  
  

-- For backward compatibility only.
-- Non-ISO-Compliant Syntax for SQL Server and Azure SQL Database 
  
GROUP BY 
      [ ALL ] column-expression [ ,...n ] 
    | column-expression [ ,...n ] [ WITH { CUBE | ROLLUP } ]   

```  
  
```syntaxsql
-- Syntax for Azure Synapse Analytics 
  
GROUP BY {
      column-name [ WITH (DISTRIBUTED_AGG) ]  
    | column-expression
    | ROLLUP ( <group_by_expression> [ ,...n ] ) 
} [ ,...n ]

```

```syntaxsql
-- Syntax for Parallel Data Warehouse  
  
GROUP BY {
      column-name [ WITH (DISTRIBUTED_AGG) ]  
    | column-expression
} [ ,...n ]

```  
    
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
 
### <a name="column-expression"></a>*column-expression*  
列を指定するか、列で集計計算以外を指定します。 この列は、テーブル、派生テーブル、またはビューに属することができます。 この列は、SELECT ステートメントの FROM 句に表示する必要がありますが、SELECT リストに表示する必要はありません。 

有効な式については、[式](~/t-sql/language-elements/expressions-transact-sql.md)に関するページを参照してください。    

この列は、SELECT ステートメントの FROM 句に表示する必要がありますが、SELECT リストに表示する必要はありません。 ただし、\<select> リスト内にある非集計式のテーブル列やビュー列は、それぞれ GROUP BY リストに含まれている必要があります。  
  
次のようなステートメントは使用できます。  
  
```sql  
SELECT ColumnA, ColumnB FROM T GROUP BY ColumnA, ColumnB;  
SELECT ColumnA + ColumnB FROM T GROUP BY ColumnA, ColumnB;  
SELECT ColumnA + ColumnB FROM T GROUP BY ColumnA + ColumnB;  
SELECT ColumnA + ColumnB + constant FROM T GROUP BY ColumnA, ColumnB;  
```  
  
次のようなステートメントは使用できません。  
  
```sql  
SELECT ColumnA, ColumnB FROM T GROUP BY ColumnA + ColumnB;  
SELECT ColumnA + constant + ColumnB FROM T GROUP BY ColumnA + ColumnB;  
```  

列の式には次を含めることはできません。

- SELECT リストで定義されている列の別名。 FROM 句で定義されている派生テーブルの列の別名を使用できます。
- **text**、**ntext**、または **image** 型の列。 ただし、text、ntext、または image の列は、有効なデータ型の値を返す関数の引数として使用できます。 たとえば、式では、SUBSTRING() および CAST() を使用できます。 これは、HAVING 句内の式にも適用されます。
- xml データ型メソッド。 xml データ型メソッドを使用するユーザー定義関数を含めることができます。 xml データ型メソッドを使用する計算列を含めることができます。 
- サブクエリ。 エラー 144 が返されます。 
- インデックス付きビューの列。 
 
### <a name="group-by-column-expression--n-"></a>GROUP BY *column-expression* [ ,...n ] 

1 つまたは複数の列の式のリスト内の値に従って SELECT ステートメントの結果をグループ化します。 

たとえば、このクエリは、Country (国)、Region (地域)、および Sales (売上) の列を持つ Sales テーブルを作成します。 4 つの行を挿入し、そのうちの 2 行は Country と Region の一致する値を持ちます。  

```sql
CREATE TABLE Sales ( Country VARCHAR(50), Region VARCHAR(50), Sales INT );

INSERT INTO sales VALUES (N'Canada', N'Alberta', 100);
INSERT INTO sales VALUES (N'Canada', N'British Columbia', 200);
INSERT INTO sales VALUES (N'Canada', N'British Columbia', 300);
INSERT INTO sales VALUES (N'United States', N'Montana', 100);
```
Sales テーブルには、次の行が含まれています。

| Country | リージョン | 売上 |
|---------|--------|-------|
| Canada | Alberta | 100 |
| Canada | British Columbia | 200 |
| Canada | British Columbia | 300 |
| United States | モンタナ | 100 |

次のクエリでは、Country と Region をグループ化し、値の組み合わせごとの合計を返します。  
 
```sql 
SELECT Country, Region, SUM(sales) AS TotalSales
FROM Sales
GROUP BY Country, Region;
```
Country と Region には値の 3 つの組み合わせがあるため、クエリ結果には 3 つの行ができます。 Canada と British Columbia の TotalSales は、2 つの行の合計です。 

| Country | リージョン | TotalSales |
|---------|--------|-------|
| Canada | Alberta | 100 |
| Canada | British Columbia | 500 |
| United States | モンタナ | 100 |

### <a name="group-by-rollup"></a>GROUP BY ROLLUP

列の式の組み合わせごとにグループを作成します。 さらに、結果を小計と総計に "ロール アップ" します。 これを行うため、右から左に移動して、列の式の数を減らし、それに対してグループと集計を作成します。 

列の順序は ROLLUP 出力に影響を与えます。結果セット内の行数に影響を与えることもあります。  

たとえば、`GROUP BY ROLLUP (col1, col2, col3, col4)` は次の一覧の列の式の組み合わせごとにグループを作成します。  

- col1, col2, col3, col4 
- col1, col2, col3, NULL
- col1, col2, NULL, NULL
- col1, NULL, NULL, NULL
- NULL, NULL, NULL, NULL --これが総計です

前の例のテーブルを使用して、このコードは、単純な GROUP BY の代わりに GROUP BY ROLLUP 操作を実行します。

```sql
SELECT Country, Region, SUM(Sales) AS TotalSales
FROM Sales
GROUP BY ROLLUP (Country, Region);
```

クエリ結果は、ROLLUP なしの単純な GROUP BY と同じ集計になります。 さらに、Country の値ごとの小計を作成します。 最後に、すべての行の総計を示します。 結果は次のようになります。

| Country | リージョン | TotalSales |
| :------ | :----- | ---------: |
| Canada | Alberta | 100 |
| Canada | British Columbia | 500 |
| Canada | NULL | 600 |
| United States | モンタナ | 100 |
| United States | NULL | 100 |
| NULL | NULL | 700 |

### <a name="group-by-cube--"></a>GROUP BY CUBE ( )  

GROUP BY CUBE では、列の可能なすべての組み合わせのグループを作成します。 GROUP BY CUBE (a, b) の場合、結果は (a, b)、(NULL, b)、(a, NULL)、および (NULL, NULL) の一意の値のグループになります。

このコードは、前の例のテーブルを使用して、Country と Region で GROUP BY CUBE 操作を実行します。 

```sql
SELECT Country, Region, SUM(Sales) AS TotalSales
FROM Sales
GROUP BY CUBE (Country, Region);
```

クエリの結果は、(Country, Region)、(NULL, Region)、(Country, NULL)、および (NULL, NULL) の一意の値のグループになります。 結果は、次のようになります。

| Country | リージョン | TotalSales |
|---------|--------|-------|
| Canada | Alberta | 100 |
| NULL | Alberta | 100 |
| Canada | British Columbia | 500 |
| NULL | British Columbia | 500 |
| United States | モンタナ | 100 |
| NULL | モンタナ | 100 |
| NULL | NULL | 700
| Canada | NULL | 600 |
| United States | NULL | 100 |
   
 ### <a name="group-by-grouping-sets--"></a>GROUP BY GROUPING SETS ( )  
 
GROUPING SETS オプションを使用すると、複数の GROUP BY 句を 1 つの GROUP BY 句に統合できます。 結果は、指定したグループの UNION ALL と同じになります。 

たとえば、`GROUP BY ROLLUP (Country, Region)` と `GROUP BY GROUPING SETS ( ROLLUP (Country, Region) )` は同じ結果を返します。 

GROUPING SETS に 2 つ以上の要素がある場合、結果はそれらの要素の和集合になります。 この例では、Country と Region の ROLLUP と CUBE の結果の和集合を返します。

```sql
SELECT Country, Region, SUM(Sales) AS TotalSales
FROM Sales
GROUP BY GROUPING SETS ( ROLLUP (Country, Region), CUBE (Country, Region) );
```

結果は、この 2 つの GROUP BY ステートメントの和集合を返すクエリと同じです。

```sql
SELECT Country, Region, SUM(Sales) AS TotalSales
FROM Sales
GROUP BY ROLLUP (Country, Region)
UNION ALL
SELECT Country, Region, SUM(Sales) AS TotalSales
FROM Sales
GROUP BY CUBE (Country, Region);
```

SQL では、GROUPING SETS リストに対して生成された重複するグループの統合は行われません。 たとえば、`GROUP BY ( (), CUBE (Country, Region) )` では、両方の要素が総計の行を返し、両方の行が結果に表示されます。 

 ### <a name="group-by-"></a>GROUP BY ()  
総計を生成する空のグループを指定します。 これは、GROUPING SET の要素の 1 つとして役立ちます。 たとえば、このステートメントは、各国の売上合計と、すべての国の総計を提供します。

```sql
SELECT Country, SUM(Sales) AS TotalSales
FROM Sales
GROUP BY GROUPING SETS ( Country, () );
```

### <a name="group-by--all--column-expression--n-"></a>GROUP BY [ ALL ] column-expression [ ,...n ] 

適用対象:SQL Server と Azure SQL Database

注:この構文は、旧バージョンとの互換性を維持するためだけに提供されます。 将来のバージョンでは削除される予定です。 新規の開発作業ではこの構文を使用しないようにし、現在この構文を使用しているアプリケーションは修正することを検討してください。

WHERE 句の検索条件を満たしているかどうかに関係なく、結果にすべてのグループを含めることを指定します。 検索条件を満たしていないグループの集計は NULL になります。 

GROUP BY ALL:
- クエリ内に WHERE 句がある場合、リモート テーブルにアクセスするクエリでは、サポートされません。
- FILESTREAM 属性を持つ列では失敗します。
  
### <a name="with-distributed_agg"></a>WITH (DISTRIBUTED_AGG)
適用対象: [!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] および [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]

DISTRIBUTED_AGG クエリ ヒントは、超並列処理 (MPP) システムで、特定の列に対し、集計を実行する前にテーブルを強制的に再配布します。 DISTRIBUTED_AGG クエリ ヒントを持つことができるのは、GROUP BY 句内で 1 つの列だけです。 クエリが完了したら、再配布されたテーブルは破棄されます。 元のテーブルは変更されません。  

注:DISTRIBUTED_AGG クエリ ヒントは、[!INCLUDE[ssPDW](../../includes/sspdw-md.md)] の旧バージョンとの下位互換性を保つために提供されており、ほとんどのクエリでパフォーマンスが向上することはありません。 既定では、MPP は集計のパフォーマンスを向上させるために必要なデータを既に再分配しています。 
  
## <a name="general-remarks"></a>全般的な解説

### <a name="how-group-by-interacts-with-the-select-statement"></a>GROUP BY が SELECT ステートメントと連携するしくみ
SELECT リスト:
- ベクター集計。 SELECT リストに集計関数が含まれている場合は、GROUP BY によって各グループの集計値が計算されます。 これらはベクター集計値と呼ばれます。 
- Distinct 集計。 AVG (DISTINCT *column_name*)、COUNT (DISTINCT *column_name*)、および UM (DISTINCT *column_name*) は ROLLUP、CUBE、および GROUPING SETS でサポートされています。
  
WHERE 句:
- SQL では、WHERE 句の条件と一致しない行は、グループ化操作の実行前に削除されます。  
  
HAVING 句:
- SQL では、HAVING 句を使用して、結果セット内のグループをフィルターできます。 
  
ORDER BY 句:
- 結果セットを並べ替えるには、ORDER BY 句を使用します。 GROUP BY 句では、結果セットの並べ替えが行われません。 
  
NULL 値:
- グループ列に NULL 値が含まれている場合、すべての NULL 値は等しいものと見なされ、1 つのグループにまとめられます。   
  
## <a name="limitations-and-restrictions"></a>制限事項と制約事項

適用対象:SQL Server (2008 以降) および [!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)]

### <a name="maximum-capacity"></a>最大容量

ROLLUP、CUBE、または GROUPING SETS を使用する GROUP BY 句の場合、式の最大数は 32 です。 グループの最大数は 4096 (2<sup>12</sup>) です。 次の例は、GROUP BY 句に 4096 を超えるグループがあるために失敗します。  
 
-   次の例では、4097 (2<sup>12</sup> + 1) 個のグループ化セットが生成され、失敗します。  
  
    ```sql
    GROUP BY GROUPING SETS( CUBE(a1, ..., a12), b )  
    ```  
  
-   次の例では、4097 (2<sup>12</sup> + 1) 個のグループが生成され、失敗します。 `CUBE ()` と `()` の両方のグループ化セットで総計行が生成され、重複したグループ化セットは削除されません。  
  
    ```sql
    GROUP BY GROUPING SETS( CUBE(a1, ..., a12), ())  
    ```  

-   この例では、下位互換性のある構文を使用します。 8192 (2<sup>13</sup>) 個のグループ化セットが生成され、失敗します。  
  
    ```sql
    GROUP BY CUBE (a1, ..., a13)   
    GROUP BY a1, ..., a13 WITH CUBE   
    ```    
    CUBE または ROLLUP を含まない下位互換性のある GROUP BY 句では、項目ごとのグループの数はクエリにかかわる GROUP BY 列のサイズ、集計列、および集計値によって制限されます。 この数制限は、クエリの中間結果を保持するために中間作業テーブル上に必要な 8,060 バイトを基本にしています。 CUBE または ROLLUP を指定している場合は、最大 12 個のグループ化式を使用できます。

### <a name="support-for-iso-and-ansi-sql-2006-group-by-features"></a>ISO および ANSI SQL-2006 の GROUP BY 機能のサポート

GROUP BY 句では、SQL-2006 標準規格に含まれているすべての GROUP BY 機能をサポートしています。ただし、次のような構文上の例外があります。  
  
-   GROUP BY 句では、明示的な GROUPING SETS リストに含まれていないグループ化セットは使用できません。 たとえば、`GROUP BY Column1, (Column2, ...ColumnN`) は標準規格で使用できますが、Transact-SQL では使用できません。  Transact SQL は、同じ意味の `GROUP BY C1, GROUPING SETS ((Column2, ...ColumnN))` と `GROUP BY Column1, Column2, ... ColumnN` をサポートしています。 これらは、前に示した `GROUP BY` の例と意味的に同じです。 これには、`GROUP BY Column1, (Column2, ...ColumnN`) が意味的に異なる `GROUP BY C1, GROUPING SETS ((Column2, ...ColumnN))` と誤って解釈される可能性を回避する目的があります。  
  
-   グループ化セット内でグループ化セットを使用することはできません。 たとえば、`GROUP BY GROUPING SETS (A1, A2,...An, GROUPING SETS (C1, C2, ...Cn))` は SQL-2006 標準規格で使用できますが、Transact-SQL では使用できません。 Transact-SQL では、最初の GROUP BY の例と意味的に同じで、構文がより明確な `GROUP BY GROUPING SETS( A1, A2,...An, C1, C2, ...Cn )` または `GROUP BY GROUPING SETS( (A1), (A2), ... (An), (C1), (C2), ... (Cn) )` を使用できます。  
  
-   GROUP BY [ALL/DISTINCT] は、列の式を含む単純な GROUP BY 句でのみ使用できます。 GROUPING SETS、ROLLUP、CUBE、WITH CUBE または WITH ROLLUP のコンストラクトでは使用できません。 ALL は既定値であり暗黙的です。 また、下位互換性のある構文でのみ使用できます。
  
### <a name="comparison-of-supported-group-by-features"></a>サポートされる GROUP BY 機能の比較  
 次の表に、SQL のバージョンに基づいてサポートされる GROUP BY 機能と、データベースの互換性レベルを示します。  
  
|特徴量|SQL Server Integration Services|SQL Server 互換性レベル 100 以上|SQL Server 2008 以降で互換性レベル 90|  
|-------------|-------------------------------------|--------------------------------------------------|-----------------------------------------------------------|  
|DISTINCT 集計|WITH CUBE および WITH ROLLUP ではサポートされていません。|WITH CUBE、WITH ROLLUP、GROUPING SETS、CUBE、および ROLLUP でサポートされています。|互換性レベル 100 と同じです。|  
|GROUP BY 句内の、CUBE または ROLLUP の名前を持つユーザー定義関数|GROUP BY 句では、ユーザー定義関数 **dbo.cube(** _arg1_ **,** _...argN_ **)** または **dbo.rollup(** _arg1_ **,** ..._argN_ **)** を使用できます。<br /><br /> 例: `SELECT SUM (x) FROM T  GROUP BY dbo.cube(y);`|GROUP BY 句では、ユーザー定義関数 **dbo.cube (** _arg1_ **,** ...argN **)** または **dbo.rollup(** arg1 **,** _...argN_ **)** は使用できません。<br /><br /> 例: `SELECT SUM (x) FROM T  GROUP BY dbo.cube(y);`<br /><br /> 次のエラー メッセージが返されます。"キーワード 'cube'&#124;'rollup' 付近に不適切な構文があります。"<br /><br /> この問題を回避するには、`dbo.cube` を `[dbo].[cube]` に、または `dbo.rollup` を `[dbo].[rollup]` に置き換えます。<br /><br /> 次の例は使用できます: `SELECT SUM (x) FROM T  GROUP BY [dbo].[cube](y);`|GROUP BY 句では、ユーザー定義関数 **dbo.cube (** _arg1_ **,** _...argN_) または **dbo.rollup(** _arg1_ **,** _...argN_ **)** を使用できます<br /><br /> 例: `SELECT SUM (x) FROM T  GROUP BY dbo.cube(y);`|  
|GROUPING SETS|サポートされていません|サポートされています|サポートされています|  
|CUBE|サポートされていません|サポートされています|サポートされていません|  
|ROLLUP|サポートされていません|サポートされています|サポートされていません|  
|GROUP BY () などの総計|サポートされていません|サポートされています|サポートされています|  
|GROUPING_ID 関数|サポートされていません|サポートされています|サポートされています|  
|GROUPING 関数|サポートされています|サポートされています|サポートされています|  
|WITH CUBE|サポートされています|サポートされています|サポートされています|  
|WITH ROLLUP|サポートされています|サポートされています|サポートされています|  
|WITH CUBE または WITH ROLLUP の重複したグループ化の削除|サポートされています|サポートされています|サポートされています| 
 
  
## <a name="examples"></a>例  
  
### <a name="a-use-a-simple-group-by-clause"></a>A. 単純な GROUP BY 句を使用する  
 次の例では、`SalesOrderID` テーブルから `SalesOrderDetail` ごとの合計を取得します。 この例では、AdventureWorks を使用します。  
  
```sql  
SELECT SalesOrderID, SUM(LineTotal) AS SubTotal  
FROM Sales.SalesOrderDetail AS sod  
GROUP BY SalesOrderID  
ORDER BY SalesOrderID;  
```  
  
### <a name="b-use-a-group-by-clause-with-multiple-tables"></a>B. GROUP BY 句を複数のテーブルで使用する  
 次の例では、`City` テーブルと `Address` テーブルを結合したものから、`EmployeeAddress` ごとに従業員の数を取得します。 この例では、AdventureWorks を使用します。 
  
```sql  
SELECT a.City, COUNT(bea.AddressID) EmployeeCount  
FROM Person.BusinessEntityAddress AS bea   
    INNER JOIN Person.Address AS a  
        ON bea.AddressID = a.AddressID  
GROUP BY a.City  
ORDER BY a.City;  
```  
  
### <a name="c-use-a-group-by-clause-with-an-expression"></a>C. GROUP BY 句を式と共に使用する  
 次の例では、`DATEPART` 関数を使用して各年の売上合計を取得します。 `SELECT` リストと `GROUP BY` 句の両方に同じ式が存在する必要があります。  
  
```sql  
SELECT DATEPART(yyyy,OrderDate) AS N'Year'  
    ,SUM(TotalDue) AS N'Total Order Amount'  
FROM Sales.SalesOrderHeader  
GROUP BY DATEPART(yyyy,OrderDate)  
ORDER BY DATEPART(yyyy,OrderDate);  
```  
  
### <a name="d-use-a-group-by-clause-with-a-having-clause"></a>D. GROUP BY 句を HAVING 句と共に使用する  
 次の例では、`HAVING` 句で生成されたグループのうち結果セットに含めるグループを、`GROUP BY` 句を使用して指定します。  
  
```sql  
SELECT DATEPART(yyyy,OrderDate) AS N'Year'  
    ,SUM(TotalDue) AS N'Total Order Amount'  
FROM Sales.SalesOrderHeader  
GROUP BY DATEPART(yyyy,OrderDate)  
HAVING DATEPART(yyyy,OrderDate) >= N'2003'  
ORDER BY DATEPART(yyyy,OrderDate);  
```  
  
## <a name="examples-azure-synapse-analytics-and-parallel-data-warehouse"></a>例 :Azure Synapse Analytics と Parallel Data Warehouse  
  
### <a name="e-basic-use-of-the-group-by-clause"></a>E. GROUP BY 句の基本的な使用方法  
 次の例では、各日のすべての売上の合計金額を検索します。 各日に、すべての売上の合計を含む 1 つの行が返されます。  
  
```sql  
-- Uses AdventureWorksDW  
  
SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales FROM FactInternetSales  
GROUP BY OrderDateKey ORDER BY OrderDateKey;  
```  
  
### <a name="f-basic-use-of-the-distributed_agg-hint"></a>F. DISTRIBUTED_AGG ヒントの基本的な使用方法  
 この例では、DISTRIBUTED_AGG クエリ ヒントを使用して、集計を実行する前に `CustomerKey` 列でテーブルをシャッフルするようにアプライアンスに強制します。  
  
```sql  
-- Uses AdventureWorksDW  
  
SELECT CustomerKey, SUM(SalesAmount) AS sas  
FROM FactInternetSales  
GROUP BY CustomerKey WITH (DISTRIBUTED_AGG)  
ORDER BY CustomerKey DESC;  
```  
  
### <a name="g-syntax-variations-for-group-by"></a>G. GROUP BY の構文の種類  
 選択リストに集計がない場合、選択リスト内の各列を GROUP BY リストに含める必要があります。 選択リスト内の計算列は、GROUP BY リストに一覧表示することができますが、必須ではありません。 構文が有効な SELECT ステートメントの例を次に示します。  
  
```sql  
-- Uses AdventureWorks  
  
SELECT LastName, FirstName FROM DimCustomer GROUP BY LastName, FirstName;  
SELECT NumberCarsOwned FROM DimCustomer GROUP BY YearlyIncome, NumberCarsOwned;  
SELECT (SalesAmount + TaxAmt + Freight) AS TotalCost FROM FactInternetSales GROUP BY SalesAmount, TaxAmt, Freight;  
SELECT SalesAmount, SalesAmount*1.10 SalesTax FROM FactInternetSales GROUP BY SalesAmount;  
SELECT SalesAmount FROM FactInternetSales GROUP BY SalesAmount, SalesAmount*1.10;  
```  
  
### <a name="h-using-a-group-by-with-multiple-group-by-expressions"></a>H. 複数の GROUP BY 式で GROUP BY を使用する  
 次の例では、複数の `GROUP BY` 条件を使用して結果をグループ化します。 各 `OrderDateKey` グループ内に、`DueDateKey` によって区別できるサブグループがある場合、結果セットに新しいグループが定義されます。  
  
```sql  
-- Uses AdventureWorks  
  
SELECT OrderDateKey, DueDateKey, SUM(SalesAmount) AS TotalSales   
FROM FactInternetSales
GROUP BY OrderDateKey, DueDateKey   
ORDER BY OrderDateKey;  
```  
  
### <a name="i-using-a-group-by-clause-with-a-having-clause"></a>I. GROUP BY 句を HAVING 句と共に使用する  
 次の例では、`HAVING` 句を使用して、結果セットに含める必要がある `GROUP BY` 句で生成されたグループを指定します。 注文日が 2004 年以降のグループのみが結果に含まれます。  
  
```sql  
-- Uses AdventureWorks  
  
SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales   
FROM FactInternetSales  
GROUP BY OrderDateKey   
HAVING OrderDateKey > 20040000   
ORDER BY OrderDateKey;  
```  
  
## <a name="see-also"></a>参照  
 [GROUPING_ID &#40;Transact-SQL&#41;](~/t-sql/functions/grouping-id-transact-sql.md)   
 [GROUPING &#40;Transact-SQL&#41;](~/t-sql/functions/grouping-transact-sql.md)   
 [SELECT &#40;Transact-SQL&#41;](~/t-sql/queries/select-transact-sql.md)   
 [SELECT 句 &#40;Transact-SQL&#41;](~/t-sql/queries/select-clause-transact-sql.md)  
  
  




