---
description: OPTION 句 (Transact-SQL)
title: OPTION 句 (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/16/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- OPTION clause
- OPTION_TSQL
- OPTION
- OPTION_clause_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- clauses [SQL Server], OPTION
- OPTION clause
ms.assetid: f47e2f3f-9302-4711-9d66-16b1a2a7ffe3
author: VanMSFT
ms.author: vanto
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 29f3bd1f215e4a915a9f7a51bc51d78e91d9f293
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104750502"
---
# <a name="option-clause-transact-sql"></a>OPTION 句 (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  クエリ全体で、指定のクエリ ヒントを使用する必要があることを指定します。 複数のクエリ ヒントを使用できますが、各クエリ ヒントを指定できるのは 1 回だけです。 OPTION 句はステートメントで 1 回だけ指定できます。  
  
 この句は SELECT、DELETE、UPDATE、MERGE ステートメントで指定できます。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
### <a name="syntax-for-ssnoversion-mdmd-and-ssazure_mdmd"></a>[!INCLUDE[ssnoversion-md.md](../../includes/ssnoversion-md.md)] および [!INCLUDE[ssazure_md.md](../../includes/ssazure_md.md)] の構文

```syntaxsql

[ OPTION ( <query_hint> [ ,...n ] ) ]   
```  
  
### <a name="syntax-for-sssdw-mdmd-and-sspdw-mdmd"></a>[!INCLUDE[sssdw-md.md](../../includes/sssdw-md.md)] および [!INCLUDE[sspdw-md.md](../../includes/sspdw-md.md)] の構文

```syntaxsql
  
OPTION ( <query_option> [ ,...n ] )  
  
<query_option> ::=  
    LABEL = label_name |  
    <query_hint>  
  
<query_hint> ::=  
    HASH JOIN   
    | LOOP JOIN   
    | MERGE JOIN  
    | FORCE ORDER  
    | { FORCE | DISABLE } EXTERNALPUSHDOWN  
```  
  
### <a name="syntax-for-sssodfull-mdmd"></a>[!INCLUDE[sssodfull-md.md](../../includes/sssodfull-md.md)] の構文

```syntaxsql

OPTION ( <query_option> [ ,...n ] )

<query_option> ::=
    LABEL = label_name
```  

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
 *query_hint*  
 データベース エンジンのステートメント処理をカスタマイズするためのオプティマイザー ヒントを示すキーワードです。 詳細については、「[クエリ ヒント &#40;Transact-SQL&#41;](../../t-sql/queries/hints-transact-sql-query.md)」を参照してください。  
  
## <a name="examples"></a>例  
  
### <a name="a-using-an-option-clause-with-a-group-by-clause"></a>A. OPTION 句を GROUP BY 句と共に使用する  
 次の例では、`OPTION` 句と共に `GROUP BY` 句を使用する方法を示します。  
  
```sql
USE AdventureWorks2012;  
GO  
SELECT ProductID, OrderQty, SUM(LineTotal) AS Total  
FROM Sales.SalesOrderDetail  
WHERE UnitPrice < $5.00  
GROUP BY ProductID, OrderQty  
ORDER BY ProductID, OrderQty  
OPTION (HASH GROUP, FAST 10);  
GO  
```  
  
## <a name="examples-sssdwfull-and-sspdw"></a>例: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)]、[!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="b-select-statement-with-a-label-in-the-option-clause"></a>B. SELECT ステートメントと OPTION 句のラベル  
 次は、単純な [!INCLUDE[ssDW](../../includes/ssdw-md.md)] SELECT ステートメントと OPTION 句のラベルの例です。  
  
```sql
-- Uses AdventureWorks  
  
SELECT * FROM FactResellerSales  
  OPTION ( LABEL = 'q17' );  
```  
  
### <a name="c-select-statement-with-a-query-hint-in-the-option-clause"></a>C. SELECT ステートメントと OPTION 句のクエリ ヒント  
 次は、OPTION 句で HASH JOIN クエリ ヒントを使用する SELECT ステートメントの例です。  
  
```sql
-- Uses AdventureWorks  
  
SELECT COUNT (*) FROM dbo.DimCustomer a  
INNER JOIN dbo.FactInternetSales b   
ON (a.CustomerKey = b.CustomerKey)  
OPTION (HASH JOIN);  
```  
  
### <a name="d-select-statement-with-a-label-and-multiple-query-hints-in-the-option-clause"></a>D. SELECT ステートメントと OPTION 句のラベルと複数のクエリ ヒント  
 次は、ラベルと複数のクエリ ヒントを含む [!INCLUDE[ssDW](../../includes/ssdw-md.md)] SELECT ステートメントの例です。 クエリが計算ノードで実行されるとき、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] は、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] で最適であると判断された方針に基づいて、ハッシュ結合またはマージ結合を適用します。  
  
```sql
-- Uses AdventureWorks  
  
SELECT COUNT (*) FROM dbo.DimCustomer a  
INNER JOIN dbo.FactInternetSales b   
ON (a.CustomerKey = b.CustomerKey)  
OPTION ( Label = 'CustJoin', HASH JOIN, MERGE JOIN);  
```  
  
### <a name="e-using-a-query-hint-when-querying-a-view"></a>E. ビューにクエリを実行するとき、クエリ ヒントを使用する  
 次の例では、CustomerView という名前のビューを作成し、ビューとテーブルを参照するクエリで HASH JOIN クエリ ヒントを使用します。  
  
```sql
-- Uses AdventureWorks  
  
CREATE VIEW CustomerView  
AS  
SELECT CustomerKey, FirstName, LastName FROM ssawPDW..DimCustomer;  
  
SELECT COUNT (*) FROM dbo.CustomerView a  
INNER JOIN dbo.FactInternetSales b  
ON (a.CustomerKey = b.CustomerKey)  
OPTION (HASH JOIN);  
  
DROP VIEW CustomerView;
```  
  
### <a name="f-query-with-a-subselect-and-a-query-hint"></a>F. サブセレクトとクエリ ヒントを含むクエリ  
 次は、サブセレクトとクエリ ヒントの両方が含まれるクエリの例です。 クエリ ヒントはグローバルに適用されます。 クエリ ヒントはサブセレクト ステートメントに追加できません。  
  
```sql
-- Uses AdventureWorks  
  
CREATE VIEW CustomerView AS  
SELECT CustomerKey, FirstName, LastName FROM ssawPDW..DimCustomer;  
  
SELECT * FROM (  
SELECT COUNT (*) AS a FROM dbo.CustomerView a  
INNER JOIN dbo.FactInternetSales b  
ON ( a.CustomerKey = b.CustomerKey )) AS t  
OPTION (HASH JOIN);  
```  
  
### <a name="g-force-the-join-order-to-match-the-order-in-the-query"></a>G. クエリの順序どおりの結合順序を強制する  
 次の例では、FORCE ORDER ヒントを使用し、クエリで指定されている結合順序を使用するようにクエリ プランに強制します。 これですべてではありませんが、一部のクエリでパフォーマンスが改善されます。  
  
```sql
-- Uses AdventureWorks  
  
-- Obtain partition numbers, boundary values, boundary value types, and rows per boundary  
-- for the partitions in the ProspectiveBuyer table of the ssawPDW database.  
SELECT sp.partition_number, prv.value AS boundary_value, lower(sty.name) AS boundary_value_type, sp.rows   
FROM sys.tables st JOIN sys.indexes si ON st.object_id = si.object_id AND si.index_id <2  
JOIN sys.partitions sp ON sp.object_id = st.object_id AND sp.index_id = si.index_id  
JOIN sys.partition_schemes ps ON ps.data_space_id = si.data_space_id   
JOIN sys.partition_range_values prv ON prv.function_id = ps.function_id   
JOIN sys.partition_parameters pp ON pp.function_id = ps.function_id   
JOIN sys.types sty ON sty.user_type_id = pp.user_type_id AND prv.boundary_id = sp.partition_number   
WHERE st.object_id = (SELECT object_id FROM sys.objects WHERE name = 'FactResellerSales')   
ORDER BY sp.partition_number  
OPTION ( FORCE ORDER )  
;  
```  
  
### <a name="h-using-externalpushdown"></a>H. EXTERNALPUSHDOWN の使用  
 次の例では、外部 Hadoop テーブルで MapReduce ジョブに WHERE 句を強制的にプッシュダウンします。  
  
```sql
SELECT ID FROM External_Table_AS A   
WHERE ID < 1000000  
OPTION (FORCE EXTERNALPUSHDOWN);  
```  
  
 次の例では、外部 Hadoop テーブルで MapReduce ジョブに WHERE 句を強制的にプッシュダウンする行為を防止します。 すべての行は、WHERE 句が適用される PDW に戻ります。  
  
```sql
SELECT ID FROM External_Table_AS A   
WHERE ID < 10  
OPTION (DISABLE EXTERNALPUSHDOWN);  
```  
  
## <a name="see-also"></a>参照  
 [Hints &#40;Transact-SQL&#41;](../../t-sql/queries/hints-transact-sql.md)   
 [SELECT &#40;Transact-SQL&#41;](../../t-sql/queries/select-transact-sql.md)   
 [UPDATE &#40;Transact-SQL&#41;](../../t-sql/queries/update-transact-sql.md)   
 [MERGE &#40;Transact-SQL&#41;](../../t-sql/statements/merge-transact-sql.md)   
 [DELETE &#40;Transact-SQL&#41;](../../t-sql/statements/delete-transact-sql.md)  
  
  

