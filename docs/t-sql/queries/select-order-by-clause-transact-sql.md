---
description: SELECT - ORDER BY 句 (Transact-SQL)
title: ORDER BY 句 (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 12/24/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- ORDER_TSQL
- BY
- ORDER_BY_TSQL
- BY_TSQL
- ORDER
- ORDER BY
dev_langs:
- TSQL
helpviewer_keywords:
- ad-hoc query paging
- OFFSET clause
- SELECT statement [SQL Server], FETCH clause
- clauses [SQL Server], ORDER BY
- SELECT statement [SQL Server], limiting the rows returned
- data [SQL Server], limiting the rows returned
- data [SQL Server], ad-hoc query paging
- sort orders [SQL Server]
- SELECT statement [SQL Server], OFFSET clause
- ORDER BY clause [Transact-SQL]
- LIMIT
- limiting result sets
- SELECT statement [SQL Server], ORDER BY clause
- query paging
- data [SQL Server], sorting
- limiting data returned in a query
- sort orders [SQL Server], ORDER BY clause
- FETCH clause
ms.assetid: bb394abe-cae6-4905-b5c6-8daaded77742
author: VanMSFT
ms.author: vanto
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: e7deb9fdda73dfc85e92341ee77781b6d5491fba
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104753912"
---
# <a name="select---order-by-clause-transact-sql"></a>SELECT - ORDER BY 句 (Transact-SQL)

[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] でクエリによって返されるデータを並べ替えます。 この句は次の場合に使用します。  
  
-   指定した列リストでクエリの結果セットを並べ替え、必要に応じて、返される行を指定の範囲に制限する。 ORDER BY 句が指定されていない限り、結果セットとして返される行の順序は保証されません。  
  
-   [順位付け関数](../../t-sql/functions/ranking-functions-transact-sql.md)の値が結果セットに適用される順序を決定する。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
> [!NOTE]  
>  [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] または [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] では、ORDER BY は SELECT/INTO または CREATE TABLE AS SELECT (CTAS) ステートメントでサポートされません。

## <a name="syntax"></a>構文  
  
```syntaxsql
-- Syntax for SQL Server and Azure SQL Database  
  
ORDER BY order_by_expression  
    [ COLLATE collation_name ]   
    [ ASC | DESC ]   
    [ ,...n ]   
[ <offset_fetch> ]  
  
<offset_fetch> ::=  
{   
    OFFSET { integer_constant | offset_row_count_expression } { ROW | ROWS }  
    [  
      FETCH { FIRST | NEXT } {integer_constant | fetch_row_count_expression } { ROW | ROWS } ONLY  
    ]  
}  
```  
  
```syntaxsql
-- Syntax for Azure Synapse Analytics and Parallel Data Warehouse  
  
[ ORDER BY   
    {  
    order_by_expression   
    [ ASC | DESC ]   
    } [ ,...n ]   
]   
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
 *order_by_expression*  
 クエリの結果セットの並べ替えに使用する列または式を指定します。 並べ替え列は、列の名前、列の別名、または選択リスト内の列の位置を示す負以外の整数で指定できます。  
  
 並べ替え列は複数指定できます。 列名は一意である必要があります。 ORDER BY 句内に記述する並べ替え列の並び順によって、並べ替えられた結果セットの構成が決まります。 つまり、結果セットが最初の列を基準に並べ替えられた後、その並べ替えられたリストが 2 つ目の列を基準に並べ替えられます。それ以降も同様の並べ替えが行われます。  
  
 ORDER BY 句で参照されている列名は、選択リスト内の列または列エイリアスか、FROM 句で指定したテーブルで定義されている列と正確に対応している必要があります。 ORDER BY 句が選択リストの列エイリアスを参照している場合、列エイリアスは、ORDER BY 句内の式の一部としてではなく、スタンドアロンで使用する必要があります。次に例を示します。
 
```sql
SELECT SCHEMA_NAME(schema_id) AS SchemaName FROM sys.objects 
ORDER BY SchemaName; -- correct 
SELECT SCHEMA_NAME(schema_id) AS SchemaName FROM sys.objects 
ORDER BY SchemaName + ''; -- wrong
```
  
 COLLATE *collation_name*  
 テーブルまたはビューで定義した列の照合順序ではなく、*collation_name* で指定した照合順序に従って ORDER BY 操作を実行します。 *collation_name* には、Windows 照合順序名または SQL 照合順序名を指定できます。 詳細については、「 [Collation and Unicode Support](../../relational-databases/collations/collation-and-unicode-support.md)」を参照してください。 COLLATE は、**char**、**varchar**、**nchar**、および **nvarchar** 型の列にのみ適用できます。  
  
 **ASC** | DESC  
 指定した列の値を昇順と降順のどちらで並べ替えるかを指定します。 ASC を指定した場合、最小値から最大値の順序で並べ替えられます。 DESC を指定した場合、最大値から最小値の順序で並べ替えられます。 ASC が既定の並べ替え順序です。 NULL 値は最小値として扱われます。  
  
 OFFSET { *integer_constant* | *offset_row_count_expression* } { ROW | ROWS }  
 クエリ式から行を取得する前にスキップする行の数を指定します。 0 以上の整数の定数か式を指定できます。  
  
**適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降と [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。  
  
 *offset_row_count_expression* には、変数、パラメーター、または定数スカラー サブクエリを指定できます。 サブクエリを使用する場合は、クエリ スコープの外部で定義された列は参照できません。 つまり、外部のクエリと関連付けることはできません。  
  
 ROW と ROWS はシノニムで、ANSI 互換性を確保するために提供されています。  
  
 クエリ実行プランでは、オフセット行数値は TOP クエリ演算子の **Offset** 属性に示されます。  
  
 FETCH { FIRST \| NEXT } { *integer_constant* \| *fetch_row_count_expression* } { ROW \| ROWS } ONLY  
 OFFSET 句が処理された後に取得する行の数を指定します。 1 以上の整数の定数か式を指定できます。  
  
**適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降と [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。  
  
 *fetch_row_count_expression* には、変数、パラメーター、または定数スカラー サブクエリを指定できます。 サブクエリを使用する場合は、クエリ スコープの外部で定義された列は参照できません。 つまり、外部のクエリと関連付けることはできません。  
  
 FIRST と NEXT はシノニムで、ANSI 互換性を確保するために提供されています。  
  
 ROW と ROWS はシノニムで、ANSI 互換性を確保するために提供されています。  
  
 クエリ実行プランでは、オフセット行数値は TOP クエリ演算子の **Rows** または **Top** 属性に示されます。  
  
## <a name="best-practices"></a>ベスト プラクティス  
 選択リスト内の列の位置を表すために、ORDER BY 句で整数を指定しないでください。 たとえば、`SELECT ProductID, Name FROM Production.Production ORDER BY 2` などのステートメントは有効ですが、実際の列名を指定した場合と比べて理解が難しくなります。 さらに、列の順序を変更したり、新しい列を追加するなどして、選択リストに変更を加えた場合は、予期しない結果が生じないように ORDER BY 句を変更する必要が生じます。  
  
 SELECT TOP (*N*) ステートメントでは必ず ORDER BY 句を使用してください。 これは、TOP の処理対象の行を指定するための唯一の方法です。 詳細については、「[TOP &#40;Transact-SQL&#41;](../../t-sql/queries/top-transact-sql.md)」を参照してください。  
  
## <a name="interoperability"></a>相互運用性  
 別のソースから行を挿入するときに、ORDER BY 句を SELECT...INTO または INSERT...SELECT ステートメントと共に使用する場合、行が指定した順序で挿入されるかどうかは保証されません。  
  
 ビューで OFFSET および FETCH を使用しても、ビューの updateability プロパティは変更されません。  
  
## <a name="limitations-and-restrictions"></a>制限事項と制約事項  
 ORDER BY 句で指定できる列の数に制限はありません、ただし、ORDER BY 句で指定した列の合計サイズが 8,060 バイトを超えることはできません。  
  
 **ntext**、**text**、**image**、**geography**、**geometry**、および **xml** 型の列は、ORDER BY 句では使用できません。  
  
 順位付け関数に *order_by_expression* が使用されている場合、整数または定数は指定できません。 詳細については、を参照してください。 [OVER 句 &#40;Transact-SQL&#41;](../../t-sql/queries/select-over-clause-transact-sql.md).  
  
 FROM 句でテーブルの別名を指定している場合、ORDER BY 句でその列を修飾する際に使用できるのは別名だけです。  
  
 SELECT ステートメントで次のいずれかの句または演算子を使用する場合は、ORDER BY 句で指定する列の名前と別名が選択リストで定義されている必要があります。  
  
-   UNION 演算子  
  
-   EXCEPT 演算子  
  
-   INTERSECT 演算子  
  
-   SELECT DISTINCT  
  
 これに加えて、ステートメントで UNION、EXCEPT、または INTERSECT 演算子を使用する場合は、列の名前または別名が、最初の (左側の) クエリの選択リストに指定されている必要があります。  
  
 UNION、EXCEPT、または INTERSECT 演算子を使用するクエリでは、ステートメントの末尾でのみ ORDER BY を使用できます。 この制限が適用されるのは、UNION、EXCEPT、および INTERSECT をサブクエリではなく、最上位レベルのクエリで指定する場合のみです。 例については、後の「例」のセクションを参照してください。  
  
 TOP 句か、OFFSET 句と FETCH 句が指定されていない場合、ビュー、インライン関数、派生テーブル、およびサブクエリでは ORDER BY 句は無効です。 これらのオブジェクトで ORDER BY を使用する場合、この句は TOP 句か、OFFSET 句と FETCH 句で返される行の特定にのみ使用されます。 クエリ自体にも ORDER BY を指定しない限り、これらの構造をクエリしたときに、ORDER BY 句で順序どおりの結果が得られるかどうかは保証されません。  
  
 インデックス付きビューや、CHECK OPTION 句を使用して定義されたビューでは、OFFSET と FETCH はサポートされません。  
  
 TOP と ORDER BY を使用できるクエリでは、OFFSET と FETCH を使用できます。ただし次の制限があります。  
  
-   OVER 句は OFFSET と FETCH をサポートしていません。  
  
-   OFFSET と FETCH は、INSERT、UPDATE、MERGE、および DELETE ステートメントでは直接指定できませんが、これらのステートメントで定義されたサブクエリで指定できます。 たとえば、INSERT INTO SELECT ステートメントの場合、SELECT ステートメントで OFFSET と FETCH を指定できます。  
  
-   UNION、EXCEPT、または INTERSECT 演算子を使用するクエリでは、クエリ結果の順序を指定する最後のクエリでのみ OFFSET と FETCH を指定できます。  
  
-   (同じクエリ スコープ内の) 同じクエリ式で TOP を OFFSET および FETCH と組み合わせて使用することはできません。  
  
## <a name="using-offset-and-fetch-to-limit-the-rows-returned"></a>OFFSET と FETCH を使用した返される行の制限  
 クエリ ページング ソリューションを実装し、クライアント アプリケーションに送信される行の数を制限するには、TOP 句ではなく OFFSET 句と FETCH 句を使用することをお勧めします。  
  
 ページング ソリューションとして OFFSET と FETCH を使用する場合は、クライアント アプリケーションに返されるデータの "ページ" ごとにクエリを 1 回実行する必要があります。 たとえば、10 行単位でクエリの結果を取得するには、クエリを 1 回実行して行 1 から行 10 を取得した後、クエリをもう一度実行して行 11 から行 20 を取得する必要があります。これ以降も、同様の処理が必要です。 各クエリは独立しており、相互に関連付けられることはありません。 つまり、クエリを 1 回実行してサーバーで状態を維持するカーソルを使用する場合とは異なり、状態の追跡はクライアント アプリケーションで行われます。 OFFSET と FETCH を使用してクエリ要求の結果に一貫性を持たせるためには、次の条件を満たす必要があります。  
  
1.  クエリで使用される基になるデータが変更されていない。 つまり、クエリで処理される行が更新されていないか、クエリからのすべてのページ要求が、スナップショットまたはシリアル化可能なトランザクション分離を使用して単一のトランザクションで実行される必要があります。 これらのトランザクション分離レベルの詳細については、「[SET TRANSACTION ISOLATION LEVEL &#40;Transact-SQL&#41;](../../t-sql/statements/set-transaction-isolation-level-transact-sql.md)」を参照してください。  
  
2.  ORDER BY 句に、一意であることが保証されている列または列の組み合わせが含まれている。  
  
 例については、このトピックの「例」セクションの「単一のトランザクションで複数のクエリを実行する」を参照してください。  
  
 ページング ソリューションにおいて、一貫性のある実行プランが重要な場合は、OFFSET および FETCH のパラメーターに OPTIMIZE FOR クエリ ヒントを使用することを検討してください。 例については、このトピックの「例」セクションの「OFFSET と FETCH の値として式を指定する」を参照してください。 OPTIMIZE FOR の詳細については、「[クエリ ヒント &#40;Transact-SQL&#41;](../../t-sql/queries/hints-transact-sql-query.md)」を参照してください。  
  
## <a name="examples"></a>例  
  
|カテゴリ|主な構文要素|  
|--------------|------------------------------|  
|[基本構文](#BasicSyntax)|ORDER BY|  
|[昇順と降順の指定](#SortOrder)|DESC および ASC|  
|[照合順序の指定](#Collation)|COLLATE|  
|[条件に基づく順序の指定](#Case)|CASE 式|  
|[順位付け関数での ORDER BY の使用](#Rank)|順位付け関数|  
|[返される行の数の制限](#Offset)|OFFSET および FETCH|  
|[UNION、EXCEPT、および INTERSECT と ORDER BY の併用](#Union)|UNION|  
  
###  <a name="basic-syntax"></a><a name="BasicSyntax"></a> 基本構文  
 このセクションの例では、最低限必要な構文を使用して ORDER BY 句の基本機能を示します。  
  
#### <a name="a-specifying-a-single-column-defined-in-the-select-list"></a>A. 選択リストで定義されている単一の列を指定する  
 次の例では、数値の `ProductID` 列を基準に結果セットを並べ替えます。 特定の並べ替え順序が指定されていないため、既定の順序 (昇順) が使用されます。  
  
```sql
USE AdventureWorks2012;  
GO  
SELECT ProductID, Name FROM Production.Product  
WHERE Name LIKE 'Lock Washer%'  
ORDER BY ProductID;  
```  
  
#### <a name="b-specifying-a-column-that-is-not-defined-in-the-select-list"></a>B. 選択リストで定義されていない列を指定する  
 次の例では、選択リストではなく、FROM 句で指定したテーブルで定義されている列を基準に結果セットを並べ替えます。  
  
```sql
USE AdventureWorks2012;  
GO  
SELECT ProductID, Name, Color  
FROM Production.Product  
ORDER BY ListPrice;  
```  
  
#### <a name="c-specifying-an-alias-as-the-sort-column"></a>C. 別名を並べ替え列として指定する  
 次の例では、列の別名 `SchemaName` を並べ替え列として指定します。  
  
```sql
USE AdventureWorks2012;  
GO  
SELECT name, SCHEMA_NAME(schema_id) AS SchemaName  
FROM sys.objects  
WHERE type = 'U'  
ORDER BY SchemaName;  
```  
  
#### <a name="d-specifying-an-expression-as-the-sort-column"></a>D. 式を並べ替え列として指定する  
 次の例では、式を並べ替え列として使用します。 DATEPART 関数を使用して式を定義し、従業員の採用年で結果セットを並べ替えます。  
  
```sql
USE AdventureWorks2012;  
GO  
SELECT BusinessEntityID, JobTitle, HireDate  
FROM HumanResources.Employee  
ORDER BY DATEPART(year, HireDate);  
```  
  
###  <a name="specifying-ascending-and-descending-sort-order"></a><a name="SortOrder"></a> 昇順と降順の並べ替え順序を指定する  
  
#### <a name="a-specifying-a-descending-order"></a>A. 降順を指定する  
 次の例では、数値列 `ProductID` を基準に、結果セットを降順で並べ替えます。  
  
```sql
USE AdventureWorks2012;  
GO  
SELECT ProductID, Name FROM Production.Product  
WHERE Name LIKE 'Lock Washer%'  
ORDER BY ProductID DESC;  
```  
  
#### <a name="b-specifying-an-ascending-order"></a>B. 昇順を指定する  
 次の例では、`Name` 列を基準に、結果セットを昇順で並べ替えます。 文字は数字ではなくアルファベット順に並べ替えられます。 つまり、10 の位置は、2 の前になります。  
  
```sql
USE AdventureWorks2012;  
GO  
SELECT ProductID, Name FROM Production.Product  
WHERE Name LIKE 'Lock Washer%'  
ORDER BY Name ASC ;  
```  
  
#### <a name="c-specifying-both-ascending-and-descending-order"></a>C. 昇順と降順の両方を指定する  
 次の例では、2 つの列を基準に結果セットを並べ替えます。 クエリの結果セットは、まず `FirstName` 列を基準に昇順で並べ替えられた後、`LastName` 列を基準に降順で並べ替えられます。  
  
```sql
USE AdventureWorks2012;  
GO  
SELECT LastName, FirstName FROM Person.Person  
WHERE LastName LIKE 'R%'  
ORDER BY FirstName ASC, LastName DESC ;  
```  
  
###  <a name="specifying-a-collation"></a><a name="Collation"></a> 照合順序の指定  
 次の例では、ORDER BY 句での照合順序の指定により、クエリ結果が返される順序がどう変化するかを示します。 作成されるテーブルには、大文字と小文字、およびアクセントを区別しない照合順序で定義された列が含まれます。 大文字と小文字、およびアクセントが混じった値が挿入されます。 ORDER BY 句で照合順序が指定されていないため、最初のクエリでは、値を並べ替えるときに列の照合順序が使用されます。 2 番目のクエリでは、大文字と小文字、およびアクセントを区別する照合順序が ORDER BY 句で指定されているため、行が返される順序が変わります。  
  
```sql
USE tempdb;  
GO  
CREATE TABLE #t1 (name NVARCHAR(15) COLLATE Latin1_General_CI_AI)  
GO  
INSERT INTO #t1 VALUES(N'Sánchez'),(N'Sanchez'),(N'sánchez'),(N'sanchez');  
  
-- This query uses the collation specified for the column 'name' for sorting.  
SELECT name  
FROM #t1  
ORDER BY name;  
-- This query uses the collation specified in the ORDER BY clause for sorting.  
SELECT name  
FROM #t1  
ORDER BY name COLLATE Latin1_General_CS_AS;  
```  
  
###  <a name="specifying-a-conditional-order"></a><a name="Case"></a> 条件に基づく順序の指定  
 次の例では、ORDER BY 句で CASE 式を使用して、指定された列の値に基づいて、条件に応じて行の並べ替え順序を決定しています。 最初の例では、`SalariedFlag` テーブルの `HumanResources.Employee` 列の値を評価します。 `SalariedFlag` が 1 に設定されている従業員は `BusinessEntityID` の降順で、 `SalariedFlag` が 0 に設定されている従業員は `BusinessEntityID` の昇順で返されます。 2 番目の例では、`TerritoryName` 列が 'United States' と等しい場合は結果セットが `CountryRegionName` 列の順序に従って並べ替えられ、他のすべての列は `CountryRegionName` の順序に従って並べ替えられます。  
  
```sql
SELECT BusinessEntityID, SalariedFlag  
FROM HumanResources.Employee  
ORDER BY CASE SalariedFlag WHEN 1 THEN BusinessEntityID END DESC  
        ,CASE WHEN SalariedFlag = 0 THEN BusinessEntityID END;  
GO  
```  
  
```sql
SELECT BusinessEntityID, LastName, TerritoryName, CountryRegionName  
FROM Sales.vSalesPerson  
WHERE TerritoryName IS NOT NULL  
ORDER BY CASE CountryRegionName WHEN 'United States' THEN TerritoryName  
         ELSE CountryRegionName END;  
```  
  
###  <a name="using-order-by-in-a-ranking-function"></a><a name="Rank"></a> 順位付け関数での ORDER BY の使用  
 次の例では、順序付け関数 ROW_NUMBER、RANK、DENSE_RANK、および NTILE で ORDER BY 句を使用しています。  
  
```sql
USE AdventureWorks2012;  
GO  
SELECT p.FirstName, p.LastName  
    ,ROW_NUMBER() OVER (ORDER BY a.PostalCode) AS "Row Number"  
    ,RANK() OVER (ORDER BY a.PostalCode) AS "Rank"  
    ,DENSE_RANK() OVER (ORDER BY a.PostalCode) AS "Dense Rank"  
    ,NTILE(4) OVER (ORDER BY a.PostalCode) AS "Quartile"  
    ,s.SalesYTD, a.PostalCode  
FROM Sales.SalesPerson AS s   
    INNER JOIN Person.Person AS p   
        ON s.BusinessEntityID = p.BusinessEntityID  
    INNER JOIN Person.Address AS a   
        ON a.AddressID = p.BusinessEntityID  
WHERE TerritoryID IS NOT NULL AND SalesYTD <> 0;  
```  
  
###  <a name="limiting-the-number-of-rows-returned"></a><a name="Offset"></a> 返される行の数の制限  
 次の例では、OFFSET と FETCH を使用して、クエリによって返される行の数を制限します。  
  
**適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降と [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。  
  
#### <a name="a-specifying-integer-constants-for-offset-and-fetch-values"></a>A. OFFSET と FETCH の値として整数の定数を指定する  
 次の例では、OFFSET および FETCH 句の値として整数の定数を指定します。 最初のクエリにより、`DepartmentID` 列で並べ替えられたすべての行が返されます。 このクエリによって返される結果と、後の 2 つのクエリの結果を比べてみてください。 次のクエリは、`OFFSET 5 ROWS` 句を使用して最初の 5 行をスキップし、残りのすべての行を返します。 最後のクエリでは、`OFFSET 0 ROWS` 句を使用して最初の行から処理を開始した後、`FETCH NEXT 10 ROWS ONLY` を使用して、返される行を、並べ替えられた結果セットからの 10 行に制限します。  
  
```sql
USE AdventureWorks2012;  
GO  
-- Return all rows sorted by the column DepartmentID.  
SELECT DepartmentID, Name, GroupName  
FROM HumanResources.Department  
ORDER BY DepartmentID;  
  
-- Skip the first 5 rows from the sorted result set and return all remaining rows.  
SELECT DepartmentID, Name, GroupName  
FROM HumanResources.Department  
ORDER BY DepartmentID OFFSET 5 ROWS;  
  
-- Skip 0 rows and return only the first 10 rows from the sorted result set.  
SELECT DepartmentID, Name, GroupName  
FROM HumanResources.Department  
ORDER BY DepartmentID   
    OFFSET 0 ROWS  
    FETCH NEXT 10 ROWS ONLY;  
```  
  
#### <a name="b-specifying-variables-for-offset-and-fetch-values"></a>B. OFFSET と FETCH の値として変数を指定する  
 次の例では、変数 `@RowsToSkip` および `@FetchRows` を宣言し、これらの変数を OFFSET および FETCH 句で指定しています。  
  
```sql
USE AdventureWorks2012;  
GO  
-- Specifying variables for OFFSET and FETCH values    
DECLARE @RowsToSkip TINYINT = 2
      , @FetchRows TINYINT = 8;  
SELECT DepartmentID, Name, GroupName  
FROM HumanResources.Department  
ORDER BY DepartmentID ASC   
    OFFSET @RowsToSkip ROWS   
    FETCH NEXT @FetchRows ROWS ONLY;  
```  
  
#### <a name="c-specifying-expressions-for-offset-and-fetch-values"></a>C. OFFSET と FETCH の値として式を指定する  
 次の例では、`@StartingRowNumber - 1` という式を使用して OFFSET 値を指定し、`@EndingRowNumber - @StartingRowNumber + 1` という式を使用して FETCH 値を指定しています。 さらに、クエリ ヒントの OPTIMIZE FOR も指定しています。 このヒントにより、クエリをコンパイルおよび最適化するときにローカル変数に特定の値を指定できます。 この値はクエリを最適化する過程でのみ使用され、クエリの実行時には使用されません。 詳細については、「[クエリ ヒント &#40;Transact-SQL&#41;](../../t-sql/queries/hints-transact-sql-query.md)」を参照してください。  
  
```sql
USE AdventureWorks2012;  
GO  
  
-- Specifying expressions for OFFSET and FETCH values      
DECLARE @StartingRowNumber TINYINT = 1  
      , @EndingRowNumber TINYINT = 8;  
SELECT DepartmentID, Name, GroupName  
FROM HumanResources.Department  
ORDER BY DepartmentID ASC   
    OFFSET @StartingRowNumber - 1 ROWS   
    FETCH NEXT @EndingRowNumber - @StartingRowNumber + 1 ROWS ONLY  
OPTION ( OPTIMIZE FOR (@StartingRowNumber = 1, @EndingRowNumber = 20) );  
```  
  
#### <a name="d-specifying-a-constant-scalar-subquery-for-offset-and-fetch-values"></a>D. OFFSET と FETCH の値として定数スカラー サブクエリを指定する  
 次の例では、定数スカラー サブクエリを使用して FETCH 句に値を定義します。 サブクエリにより、`PageSize` テーブルの `dbo.AppSettings` 列から 1 つの値が返されます。  
  
```sql
-- Specifying a constant scalar subquery  
USE AdventureWorks2012;  
GO  
CREATE TABLE dbo.AppSettings (AppSettingID INT NOT NULL, PageSize INT NOT NULL);  
GO  
INSERT INTO dbo.AppSettings VALUES(1, 10);  
GO  
DECLARE @StartingRowNumber TINYINT = 1;  
SELECT DepartmentID, Name, GroupName  
FROM HumanResources.Department  
ORDER BY DepartmentID ASC   
    OFFSET @StartingRowNumber ROWS   
    FETCH NEXT (SELECT PageSize FROM dbo.AppSettings WHERE AppSettingID = 1) ROWS ONLY;  
```  
  
#### <a name="e-running-multiple-queries-in-a-single-transaction"></a>E. 単一のトランザクションで複数のクエリを実行する  
 次の例は、クエリのすべての要求で一貫性のある結果が返されるようにするページング ソリューションを実装する方法の 1 つを示しています。 このクエリは、スナップショット分離レベルを使用して単一のトランザクションで実行されます。また、ORDER BY 句で指定された列により、列の一意性が確保されます。  
  
```sql
USE AdventureWorks2012;  
GO  
  
-- Ensure the database can support the snapshot isolation level set for the query.  
IF (SELECT snapshot_isolation_state FROM sys.databases WHERE name = N'AdventureWorks2012') = 0  
    ALTER DATABASE AdventureWorks2012 SET ALLOW_SNAPSHOT_ISOLATION ON;  
GO  
  
-- Set the transaction isolation level  to SNAPSHOT for this query.  
SET TRANSACTION ISOLATION LEVEL SNAPSHOT;  
GO  
  
-- Beginning the transaction.
BEGIN TRANSACTION;  
GO  
-- Declare and set the variables for the OFFSET and FETCH values.  
DECLARE @StartingRowNumber INT = 1  
      , @RowCountPerPage INT = 3;  
  
-- Create the condition to stop the transaction after all rows have been returned.  
WHILE (SELECT COUNT(*) FROM HumanResources.Department) >= @StartingRowNumber  
BEGIN  
  
-- Run the query until the stop condition is met.  
SELECT DepartmentID, Name, GroupName  
FROM HumanResources.Department  
ORDER BY DepartmentID ASC   
    OFFSET @StartingRowNumber - 1 ROWS   
    FETCH NEXT @RowCountPerPage ROWS ONLY;  
  
-- Increment @StartingRowNumber value.  
SET @StartingRowNumber = @StartingRowNumber + @RowCountPerPage;  
CONTINUE  
END;  
GO  
COMMIT TRANSACTION;  
GO  
```  
  
###  <a name="using-order-by-with-union-except-and-intersect"></a><a name="Union"></a> UNION、EXCEPT、および INTERSECT と ORDER BY の併用  
 クエリで UNION、EXCEPT、または INTERSECT 演算子を使用する場合は、ORDER BY 句をステートメントの末尾に指定する必要があります。この場合、結合されたクエリの結果が並べ替えられます。 次の例では、赤色または黄色のすべての製品が返され、この結合リストが `ListPrice` 列を基準に並べ替えられます。  
  
```sql
USE AdventureWorks2012;  
GO  
SELECT Name, Color, ListPrice  
FROM Production.Product  
WHERE Color = 'Red'  
-- ORDER BY cannot be specified here.  
UNION ALL  
SELECT Name, Color, ListPrice  
FROM Production.Product  
WHERE Color = 'Yellow'  
ORDER BY ListPrice ASC;  
```  
  
## <a name="examples-sssdwfull-and-sspdw"></a>例: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)]、[!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
 次の例は、`EmployeeKey` の数値列を昇順で並べ替えた結果セットの順序付けを示しています。  
  
```sql
-- Uses AdventureWorks  
  
SELECT EmployeeKey, FirstName, LastName FROM DimEmployee  
WHERE LastName LIKE 'A%'  
ORDER BY EmployeeKey;  
```  
  
 次の例では、`EmployeeKey` の数値列を基準に降順で結果セットを並べ替えます。  
  
```sql
-- Uses AdventureWorks  
  
SELECT EmployeeKey, FirstName, LastName FROM DimEmployee  
WHERE LastName LIKE 'A%'  
ORDER BY EmployeeKey DESC;  
```  
  
 次の例では、`LastName` 列を基準に結果セットを並べ替えます。  
  
```sql
-- Uses AdventureWorks  
  
SELECT EmployeeKey, FirstName, LastName FROM DimEmployee  
WHERE LastName LIKE 'A%'  
ORDER BY LastName;  
```  
  
 次の例では、2 つの列を基準に並べ替えます。 このクエリでは、`FirstName` 列で昇順に並べ替えを行ってから、`FirstName` 列の共通の `LastName` 値を降順に並べ替えます。  
  
```sql
-- Uses AdventureWorks  
  
SELECT EmployeeKey, FirstName, LastName FROM DimEmployee  
WHERE LastName LIKE 'A%'  
ORDER BY LastName, FirstName;  
```  
  
## <a name="see-also"></a>参照  
 [式 &#40;Transact-SQL&#41;](../../t-sql/language-elements/expressions-transact-sql.md)   
 [SELECT &#40;Transact-SQL&#41;](../../t-sql/queries/select-transact-sql.md)   
 [FROM &#40;Transact-SQL&#41;](../../t-sql/queries/from-transact-sql.md)   
 [順位付け関数 &#40;Transact-SQL&#41;](../../t-sql/functions/ranking-functions-transact-sql.md)   
 [TOP &#40;Transact-SQL&#41;](../../t-sql/queries/top-transact-sql.md)   
 [クエリ ヒント &#40;Transact-SQL&#41;](../../t-sql/queries/hints-transact-sql-query.md)   
 [EXCEPT および INTERSECT &#40;Transact-SQL&#41;](../../t-sql/language-elements/set-operators-except-and-intersect-transact-sql.md)   
 [UNION &#40;Transact-SQL&#41;](../../t-sql/language-elements/set-operators-union-transact-sql.md)   
 [CASE &#40;Transact-SQL&#41;](../../t-sql/language-elements/case-transact-sql.md)  
  
  

