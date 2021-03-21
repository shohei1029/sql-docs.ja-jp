---
description: LAG (Transact-SQL)
title: LAG (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 11/09/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- LAG_TSQL
- LAG
dev_langs:
- TSQL
helpviewer_keywords:
- LAG function
- analytic functions, LAG
ms.assetid: a9a90bdb-3f80-4c97-baca-b7407bcdc7f0
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 6718d71ae8fd2df6791b85d921e72611813677eb
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104756422"
---
# <a name="lag-transact-sql"></a>LAG (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] で開始する自己結合を使用せずに同じ結果セットの前の行からデータにアクセスします。 LAG によって、現在の行の前にある指定された物理的なオフセットの行にアクセスできます。 SELECT ステートメントでこの分析関数を使用して、現在の行の値と前の行の値を比較します。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則 &#40;Transact-SQL&#41;](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql  
LAG (scalar_expression [,offset] [,default])  
    OVER ( [ partition_by_clause ] order_by_clause )  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
 *scalar_expression*  
 指定されたオフセットに基づいて返される値。 単一の (スカラー) 値を返す任意の型の式です。 *scalar_expression* は分析関数にはなりません。  
  
 *offset*  
 値を取得する現在の行から戻る行の数。 指定しない場合は、1 が既定値です。 *offset* は、列、サブクエリ、または正の整数と評価されたり、暗黙的に **bigint** に変換される可能性があるその他の式です。 *offset* は、負の値または分析関数にはなりません。  
  
 *default*  
 *offset* がパーティションの範囲外である場合に返される値。 既定値を指定しない場合、NULL が返されます。 *default* には、列、サブクエリ、または式を指定できますが、分析関数は指定できません。 *default* には、*scalar_expression* の型との互換性が必要です。  
  
 OVER **(** [ _partition\_by\_clause_ ] _order\_by\_clause_ **)**  
 *partition_by_clause* は、FROM 句で生成された結果セットをパーティションに分割します。このパーティションに関数が適用されます。 指定しない場合、関数ではクエリ結果セットのすべての行を 1 つのグループとして扱います。 *order_by_clause* は、関数を適用する前にデータの順序を決定します。 *partition_by_clause* を指定した場合、この引数によってパーティション内のデータの順序が決定されます。 *order_by_clause* が必要です。 詳細については、を参照してください。 [OVER 句 &#40;Transact-SQL&#41;](../../t-sql/queries/select-over-clause-transact-sql.md).  
  
## <a name="return-types"></a>戻り値の型  
 指定した *scalar_expression* のデータ型。 *scalar_expression* が NULL 値を許容するか *default* が NULL に設定されている場合は、NULL が返されます。  
  
## <a name="general-remarks"></a>全般的な解説  
 LAG は非決定的です。 詳細については、「 [決定的関数と非決定的関数](../../relational-databases/user-defined-functions/deterministic-and-nondeterministic-functions.md)」を参照してください。  
  
## <a name="examples"></a>例  
  
### <a name="a-compare-values-between-years"></a>A. 年間の値を比較する  
 次の例では、LAG 関数を使用して、過去数年間にわたる特定の従業員の販売ノルマの差を返します。 最初の行に使用できるラグ値がないため、既定のゼロ (0) が返されることに注意してください。  
  
```sql   
USE AdventureWorks2012;  
GO  
SELECT BusinessEntityID, YEAR(QuotaDate) AS SalesYear, SalesQuota AS CurrentQuota,   
       LAG(SalesQuota, 1,0) OVER (ORDER BY YEAR(QuotaDate)) AS PreviousQuota  
FROM Sales.SalesPersonQuotaHistory  
WHERE BusinessEntityID = 275 AND YEAR(QuotaDate) IN ('2005','2006');  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```    
BusinessEntityID SalesYear   CurrentQuota          PreviousQuota  
---------------- ----------- --------------------- ---------------------  
275              2005        367000.00             0.00  
275              2005        556000.00             367000.00  
275              2006        502000.00             556000.00  
275              2006        550000.00             502000.00  
275              2006        1429000.00            550000.00  
275              2006        1324000.00            1429000.00  
  
```  
  
### <a name="b-compare-values-within-partitions"></a>B. パーティション内の値を比較する  
 次の例では、LAG 関数を使用して、従業員間の今年に入ってからの売上高を比較します。 PARTITION BY 句を指定して、販売区域ごとに結果セットの行を分割します。 LAG 関数は各パーティションに対して個別に適用され、各パーティションで計算が再開されます。 OVER 句の ORDER BY 句によって、各パーティション内の行の順序が決められます。 SELECT ステートメントの ORDER BY 句によって、結果セット全体で行の並べ替えが行われます。 各パーティションの最初の行に使用できるラグ値がないため、既定のゼロ (0) が返されることに注意してください。  
  
```sql   
USE AdventureWorks2012;  
GO  
SELECT TerritoryName, BusinessEntityID, SalesYTD,   
       LAG (SalesYTD, 1, 0) OVER (PARTITION BY TerritoryName ORDER BY SalesYTD DESC) AS PrevRepSales  
FROM Sales.vSalesPerson  
WHERE TerritoryName IN (N'Northwest', N'Canada')   
ORDER BY TerritoryName;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```    
TerritoryName            BusinessEntityID SalesYTD              PrevRepSales  
-----------------------  ---------------- --------------------- ---------------------  
Canada                   282              2604540.7172          0.00  
Canada                   278              1453719.4653          2604540.7172  
Northwest                284              1576562.1966          0.00  
Northwest                283              1573012.9383          1576562.1966  
Northwest                280              1352577.1325          1573012.9383  
  
```  
  
### <a name="c-specifying-arbitrary-expressions"></a>C. 任意の式を指定する  
 次の例では、LAG 関数の構文にさまざまな任意の式を指定する方法を示します。  
  
```sql   
CREATE TABLE T (a INT, b INT, c INT);   
GO  
INSERT INTO T VALUES (1, 1, -3), (2, 2, 4), (3, 1, NULL), (4, 3, 1), (5, 2, NULL), (6, 1, 5);   
  
SELECT b, c,   
    LAG(2*c, b*(SELECT MIN(b) FROM T), -c/2.0) OVER (ORDER BY a) AS i  
FROM T;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
b           c           i  
----------- ----------- -----------  
1           -3          1  
2           4           -2  
1           NULL        8  
3           1           -6  
2           NULL        NULL  
1           5           NULL  
```  
  
## <a name="examples-sssdwfull-and-sspdw"></a>例: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)]、[!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="d-compare-values-between-quarters"></a>D. 四半期の値を比較する  
 次の例では、LAG 関数を示します。 クエリでは、LAG 関数を使用してカレンダー年の過去数四半期にわたる特定の従業員の販売ノルマの差を返します。 最初の行に使用できるラグ値がないため、既定のゼロ (0) が返されることに注意してください。  
  
```sql   
-- Uses AdventureWorks  
  
SELECT CalendarYear, CalendarQuarter, SalesAmountQuota AS SalesQuota,  
       LAG(SalesAmountQuota,1,0) OVER (ORDER BY CalendarYear, CalendarQuarter) AS PrevQuota,  
       SalesAmountQuota - LAG(SalesAmountQuota,1,0) OVER (ORDER BY CalendarYear, CalendarQuarter) AS Diff  
FROM dbo.FactSalesQuota  
WHERE EmployeeKey = 272 AND CalendarYear IN (2001, 2002)  
ORDER BY CalendarYear, CalendarQuarter;   
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 ```
Year Quarter  SalesQuota  PrevQuota  Diff  
---- -------  ----------  ---------  -------------  
2001 3        28000.0000      0.0000   28000.0000  
2001 4         7000.0000  28000.0000  -21000.0000  
2001 1        91000.0000   7000.0000   84000.0000  
2002 2       140000.0000  91000.0000   49000.0000  
2002 3         7000.0000 140000.0000  -70000.0000  
2002 4       154000.0000   7000.0000   84000.0000
```  
  
## <a name="see-also"></a>参照  
 [LEAD &#40;Transact-SQL&#41;](../../t-sql/functions/lead-transact-sql.md)  
  
  


