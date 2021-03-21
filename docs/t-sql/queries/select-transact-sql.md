---
title: SELECT (Transact-SQL)
description: SELECT (Transact-SQL)
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- SELECT_TSQL
- SELECT
dev_langs:
- TSQL
helpviewer_keywords:
- retrieving rows
- SELECT statement [SQL Server]
- SELECT statement [SQL Server], about SELECT statement
- row retrieval [SQL Server], SELECT statement
- DML [SQL Server], SELECT statement
- data manipulation language [SQL Server], SELECT statement
- row retrieval [SQL Server]
- queries [SQL Server], results
author: VanMSFT
ms.author: vanto
ms.custom: ''
ms.date: 10/24/2017
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 67c4317c4cdd6a04359f844d91a229053c5a2f55
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104743062"
---
# <a name="select-transact-sql"></a>SELECT (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]で、データベースから行を取得し、1 つ以上のテーブルから 1 つ以上の行または列を選択できるようにします。 SELECT ステートメントの完全な構文は複雑ですが、主な句は次のとおりです。  
  
[ WITH { [[ XMLNAMESPACES ,]](../../t-sql/xml/with-xmlnamespaces.md) [[ \<common_table_expression> ]](../../t-sql/queries/with-common-table-expression-transact-sql.md) } ]
  
 [SELECT *select_list*](../../t-sql/queries/select-clause-transact-sql.md) [ [INTO *new_table*](../../t-sql/queries/select-into-clause-transact-sql.md) ]  
  
 [ [FROM *table_source*](../../t-sql/queries/from-transact-sql.md) ] [ [WHERE *search_condition*](../../t-sql/queries/where-transact-sql.md) ]  
  
 [ [GROUP BY *group_by_expression*](../../t-sql/queries/select-group-by-transact-sql.md) ]  
  
 [ [HAVING *search_condition*](../../t-sql/queries/select-having-transact-sql.md) ]  
  
 [ [ORDER BY *order_expression* [ ASC | DESC ]](../../t-sql/queries/select-order-by-clause-transact-sql.md)]  
  
 [UNION](../../t-sql/language-elements/set-operators-union-transact-sql.md)、[EXCEPT、INTERSECT](../../t-sql/language-elements/set-operators-except-and-intersect-transact-sql.md) 演算子をクエリ間で使用すると、クエリの結果を結合または比較して単一の結果セットにできます。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql
-- Syntax for SQL Server and Azure SQL Database  
  
<SELECT statement> ::=    
    [ WITH { [ XMLNAMESPACES ,] [ <common_table_expression> [,...n] ] } ]  
    <query_expression>   
    [ ORDER BY <order_by_expression> ] 
    [ <FOR Clause>]   
    [ OPTION ( <query_hint> [ ,...n ] ) ]   
<query_expression> ::=   
    { <query_specification> | ( <query_expression> ) }   
    [  { UNION [ ALL ] | EXCEPT | INTERSECT }  
        <query_specification> | ( <query_expression> ) [...n ] ]   
<query_specification> ::=   
SELECT [ ALL | DISTINCT ]   
    [TOP ( expression ) [PERCENT] [ WITH TIES ] ]   
    < select_list >   
    [ INTO new_table ]   
    [ FROM { <table_source> } [ ,...n ] ]   
    [ WHERE <search_condition> ]   
    [ <GROUP BY> ]   
    [ HAVING < search_condition > ]   
```  
  
```syntaxsql
-- Syntax for Azure Synapse Analytics and Parallel Data Warehouse  
  
[ WITH <common_table_expression> [ ,...n ] ]  
SELECT <select_criteria>  
[;]  
  
<select_criteria> ::=  
    [ TOP ( top_expression ) ]   
    [ ALL | DISTINCT ]   
    { * | column_name | expression } [ ,...n ]   
    [ FROM { table_source } [ ,...n ] ]  
    [ WHERE <search_condition> ]   
    [ GROUP BY <group_by_clause> ]   
    [ HAVING <search_condition> ]   
    [ ORDER BY <order_by_expression> ]  
    [ OPTION ( <query_option> [ ,...n ] ) ]  
  
```  

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="remarks"></a>解説  
 SELECT ステートメントは非常に複雑であるため、構文の構成要素と引数の詳細を句ごとに説明します。  

:::row:::
    :::column:::
        [WITH XMLNAMESPACES](../../t-sql/xml/with-xmlnamespaces.md)
    :::column-end:::
    :::column:::
        [HAVING](../../t-sql/queries/select-having-transact-sql.md)
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [WITH common_table_expression](../../t-sql/queries/with-common-table-expression-transact-sql.md)
    :::column-end:::
    :::column:::
        [UNION](../../t-sql/language-elements/set-operators-union-transact-sql.md)
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [SELECT 句](../../t-sql/queries/select-clause-transact-sql.md)
    :::column-end:::
    :::column:::
        [EXCEPT および INTERSECT](../../t-sql/language-elements/set-operators-except-and-intersect-transact-sql.md)
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [INTO 句](../../t-sql/queries/select-into-clause-transact-sql.md)
    :::column-end:::
    :::column:::
        [ORDER BY](../../t-sql/queries/select-order-by-clause-transact-sql.md)
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [FROM](../../t-sql/queries/from-transact-sql.md)
    :::column-end:::
    :::column:::
        [FOR 句](../../t-sql/queries/select-for-clause-transact-sql.md)
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [WHERE](../../t-sql/queries/where-transact-sql.md)
    :::column-end:::
    :::column:::
        [OPTION 句](../../t-sql/queries/option-clause-transact-sql.md)
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [GROUP BY](../../t-sql/queries/select-group-by-transact-sql.md)
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

 SELECT ステートメント内の句の順序は重要です。 オプションの句は省略できます。ただし、オプションの句を使用する場合は、適切な順序で指定する必要があります。  
  
 ユーザー定義関数に SELECT ステートメントを指定できるのは、関数にとってローカルな変数に値を代入する式がステートメントの選択リストに含まれる場合だけです。  
  
 サーバー名要素として OPENDATASOURCE 関数を使用する 4 部構成の名前は、SELECT ステートメント内にテーブル名を指定できる任意の場所で、テーブル ソースとして使用できます。 4 部構成の名前を [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] に対して指定することはできません。  
  
 リモート テーブルに関係する SELECT ステートメントには、構文の制約がいくつか適用されます。  
  
## <a name="logical-processing-order-of-the-select-statement"></a>SELECT ステートメントの論理的な処理順序  
 以下のステップは、SELECT ステートメントの論理的な処理順序 (バインド順序) を示しています。 この順序によって、1 つのステップで定義されたオブジェクトが、以降のステップにおける句でいつ利用可能になるかが決まります。 たとえば、クエリ プロセッサが FROM 句で定義されているテーブルまたはビューにバインド (アクセス) できる場合、これらのオブジェクトとその列は、以降のすべてのステップで利用できます。 逆に、SELECT 句はステップ 8 にあるので、この句で定義されているすべての列の別名と派生列は、それより前の句では参照できません。 ただし、ORDER BY 句などの後続の句で参照されていることができます。 ステートメントの実際の物理的な実行は、クエリ プロセッサによって決定され、このリストでの順序とは異なる場合があります。  
  
1.  FROM  
2.  ON  
3.  JOIN  
4.  WHERE  
5.  GROUP BY  
6.  WITH CUBE または WITH ROLLUP  
7.  HAVING  
8.  SELECT  
9. DISTINCT  
10. ORDER BY  
11. TOP  

> [!WARNING]
> 上記の順序は、通常は true です。 ただし、一般的ではないシーケンスが異なる場合があります。
>
> たとえば、ビューにクラスター化インデックスがいくつかあり、ビューがいくつかのテーブル行を除外しており、ビューの SELECT 列リストがデータ型を *varchar* から *integer* に変更するために CONVERT を使用しているとします。 このような状況では、WHERE 句が実行される前に CONVERT が実行される場合があります。 実際に珍しいです。 これが問題となる場合、しばしばビューを変更して、異なるシーケンスを回避する方法があります。 

## <a name="permissions"></a>アクセス許可  
 データの選択にはテーブルまたはビューに対する **SELECT** 権限が必要です。この権限は、スキーマに対する **SELECT** 権限やテーブルに対する **CONTROL** 権限など上位スコープから継承されます。 または、**db_datareader** または **db_owner** 固定データベース ロールまたは **sysadmin** 固定サーバー ロールのメンバーシップが必要です。 **SELECT INTO** を使用した新しいテーブルの作成には、**CREATE TABLE** 権限、および新しいテーブルを所有するスキーマに対する **ALTER SCHEMA** 権限も必要です。  
  
## <a name="examples"></a>例 :   
次の例では、[!INCLUDE[ssawPDW](../../includes/ssawpdw-md.md)] データベースを使用します。
  
### <a name="a-using-select-to-retrieve-rows-and-columns"></a>A. SELECT を使用して行および列を取得する  
 このセクションでは、次の 3 つのコード例を示します。 最初のコード例では、`DimEmployee` テーブルから、WHERE 句を指定せずにすべての行を返し、また `*` を使用してすべての列を返しています。  
  
```sql  
SELECT *  
FROM DimEmployee  
ORDER BY LastName;  
```  
  
 この次の例では、テーブルのエイリアシングを使用して同じ結果を得ようとしています。  
  
```sql  
SELECT e.*  
FROM DimEmployee AS e  
ORDER BY LastName;  
```  
  
 この例では、`AdventureWorksPDW2012` データベース内の `DimEmployee` テーブルから、WHERE 句を指定せずにすべての行と、一部の列 (`FirstName`、`LastName`、`StartDate`) を返しています。 3 番目の列見出しは `FirstDay` に名前変更されています。  
  
```sql  
SELECT FirstName, LastName, StartDate AS FirstDay  
FROM DimEmployee   
ORDER BY LastName;  
```  
  
 この例では、`EndDate` が NULL でない、`MaritalStatus` が 'M' (結婚している) の `DimEmployee` の行のみが返されます。  
  
```sql  
SELECT FirstName, LastName, StartDate AS FirstDay  
FROM DimEmployee   
WHERE EndDate IS NOT NULL   
AND MaritalStatus = 'M'  
ORDER BY LastName;  
```  
  
### <a name="b-using-select-with-column-headings-and-calculations"></a>B. 列ヘッダーおよび計算処理と共に SELECT を使用する  
 次の例では、`DimEmployee` テーブルのすべての行が返され、各従業員の `BaseRate` と週 40 時間勤務に基いた、給与総額が計算されます。  
  
```sql  
SELECT FirstName, LastName, BaseRate, BaseRate * 40 AS GrossPay  
FROM DimEmployee  
ORDER BY LastName;  
```  
  
### <a name="c-using-distinct-with-select"></a>C. DISTINCT を SELECT と共に使用する  
 次の例では、`DISTINCT` を使用し、`DimEmployee` テーブル内の一意のすべてのタイトルの一覧が生成されます。  
  
```sql  
SELECT DISTINCT Title  
FROM DimEmployee  
ORDER BY Title;  
```  
  
### <a name="d-using-group-by"></a>D. GROUP BY を使用する  
 次の例では、各日のすべての売上の合計金額を検索します。  
  
```sql  
SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales  
FROM FactInternetSales  
GROUP BY OrderDateKey  
ORDER BY OrderDateKey;  
```  
  
 `GROUP BY` 句があるため、すべての売上の合計を含む 1 つの行のみが日ごとに返されます。  
  
### <a name="e-using-group-by-with-multiple-groups"></a>E. GROUP BY を複数のグループと共に使用する  
 次の例では、各日の平均価格とインターネット販売の合計を、発注日および昇格キーでグループ化して検索します。  
  
```sql  

SELECT OrderDateKey, PromotionKey, AVG(SalesAmount) AS AvgSales, SUM(SalesAmount) AS TotalSales  
FROM FactInternetSales  
GROUP BY OrderDateKey, PromotionKey  
ORDER BY OrderDateKey;   
```  
  
### <a name="f-using-group-by-and-where"></a>F. GROUP BY と WHERE を使用する  
 次の例では、注文日が 2002 年 8 月 1 日より遅い行のみを取得した後、結果をグループ化します。  
  
```sql  
SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales  
FROM FactInternetSales  
WHERE OrderDateKey > '20020801'  
GROUP BY OrderDateKey  
ORDER BY OrderDateKey;  
```  
  
### <a name="g-using-group-by-with-an-expression"></a>G. 1 つの式と共に GROUP BY を使用する  
 次の例では、式によってグループ化します。 式に集計関数が含まれない限り、式によってグループ化することができます。  
  
```sql  
SELECT SUM(SalesAmount) AS TotalSales  
FROM FactInternetSales  
GROUP BY (OrderDateKey * 10);  
```  
  
### <a name="h-using-group-by-with-order-by"></a>H. ORDER BY と共に GROUP BY を使用する  
 次の例では、1 日の売り上げ高と注文額の合計を検索します。  
  
```sql  
SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales  
FROM FactInternetSales  
GROUP BY OrderDateKey  
ORDER BY OrderDateKey;  
```  
  
### <a name="i-using-the-having-clause"></a>I. HAVING 句を使用する  
 このクエリは、`HAVING` 句を使用して結果を制限しています。  
  
```sql  
SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales  
FROM FactInternetSales  
GROUP BY OrderDateKey  
HAVING OrderDateKey > 20010000  
ORDER BY OrderDateKey;  
```  
  
## <a name="see-also"></a>参照  
 [SELECT 例 &#40;Transact-SQL&#41;](../../t-sql/queries/select-examples-transact-sql.md)  
 [Hints &#40;Transact-SQL&#41;](../../t-sql/queries/hints-transact-sql.md)
  

