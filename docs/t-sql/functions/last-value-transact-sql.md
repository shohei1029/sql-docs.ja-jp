---
description: LAST_VALUE (Transact-SQL)
title: LAST_VALUE (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 09/22/2020
ms.prod: sql
ms.prod_service: synapse-analytics, database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- LAST_VALUE
- LAST_VALUE_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- analytic functions, LAST_VALUE
- LAST_VALUE function
ms.assetid: fd833e34-8092-42b7-80fc-95ca6b0eab6b
author: cawrites
ms.author: chadam
monikerRange: =azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 9b958231903343c281172b522aa24c242241a2c1
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104754512"
---
# <a name="last_value-transact-sql"></a>LAST_VALUE (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa](../../includes/applies-to-version/sql-asdb-asdbmi-asa.md)]

  順序付けられた値のセットで最後の値を返します。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  

```syntaxsql 
LAST_VALUE ( [ scalar_expression ] )  [ IGNORE NULLS | RESPECT NULLS ]
    OVER ( [ partition_by_clause ] order_by_clause rows_range_clause )   
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
 *scalar_expression*  
 返される値。 *scalar_expression* 列、サブクエリ、または 1 つの値となるその他の式を指定できます。 他の分析関数は指定できません。  
  
 [ IGNORE NULLS | RESPECT NULLS ]     
 **適用対象**:Azure SQL Edge

 IGNORE NULLS: パーティションの最後の値の計算時に、データセット内の null 値を無視します。     
 RESPECT NULLS: パーティションの最後の値の計算時に、データセット内の null 値を使用します。     
 
  詳細については、[欠損値の補完](/azure/azure-sql-edge/imputing-missing-values/)に関する記事を参照してください。
  
 OVER **(** [ *partition_by_clause* ] *order_by_clause* [ *rows_range_clause* ] **)**  
 *partition_by_clause* は、FROM 句で生成された結果セットをパーティションに分割します。このパーティションに関数が適用されます。 指定しない場合、関数ではクエリ結果セットのすべての行を 1 つのグループとして扱います。  
  
 *order_by_clause* は、関数を適用する前にデータの順序を決定します。 *order_by_clause* が必要です。 *rows_range_clause* は始点と終点を指定することによって、パーティション内の行をさらに制限します。 詳細については、を参照してください。 [OVER 句 &#40;Transact-SQL&#41;](../../t-sql/queries/select-over-clause-transact-sql.md).  
  
## <a name="return-types"></a>戻り値の型  
 同じ型には *scalar_expression* です。  
  
## <a name="general-remarks"></a>全般的な解説  
 LAST_VALUE は非決定的です。 詳細については、「 [決定的関数と非決定的関数](../../relational-databases/user-defined-functions/deterministic-and-nondeterministic-functions.md)」を参照してください。  
  
## <a name="examples"></a>例  
  
### <a name="a-using-last_value-over-partitions"></a>A. パーティションで LAST_VALUE を使用する  
 次の例では、特定の給与 (等級) について各部門の最後の従業員の採用日を返します。  PARTITION BY 句によって部門ごとに従業員がパーティションに分割され、各パーティションに個別に LAST_VALUE 関数が適用されます。 OVER 句に指定された ORDER BY 句は、各パーティション内の行に LAST_VALUE 関数が適用される論理的な順序を決定します。  
  
```sql  
USE AdventureWorks2012;  
GO  
SELECT Department, LastName, Rate, HireDate,   
    LAST_VALUE(HireDate) OVER (PARTITION BY Department ORDER BY Rate) AS LastValue  
FROM HumanResources.vEmployeeDepartmentHistory AS edh  
INNER JOIN HumanResources.EmployeePayHistory AS eph    
    ON eph.BusinessEntityID = edh.BusinessEntityID  
INNER JOIN HumanResources.Employee AS e  
    ON e.BusinessEntityID = edh.BusinessEntityID  
WHERE Department IN (N'Information Services',N'Document Control');   
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
  
Department                  LastName                Rate         HireDate     LastValue  
--------------------------- ----------------------- ------------ ----------   ----------  
Document Control            Chai                    10.25        2003-02-23   2003-03-13  
Document Control            Berge                   10.25        2003-03-13   2003-03-13  
Document Control            Norred                  16.8269      2003-04-07   2003-01-17  
Document Control            Kharatishvili           16.8269      2003-01-17   2003-01-17  
Document Control            Arifin                  17.7885      2003-02-05   2003-02-05  
Information Services        Berg                    27.4038      2003-03-20   2003-01-24  
Information Services        Meyyappan               27.4038      2003-03-07   2003-01-24  
Information Services        Bacon                   27.4038      2003-02-12   2003-01-24  
Information Services        Bueno                   27.4038      2003-01-24   2003-01-24  
Information Services        Sharma                  32.4519      2003-01-05   2003-03-27  
Information Services        Connelly                32.4519      2003-03-27   2003-03-27  
Information Services        Ajenstat                38.4615      2003-02-18   2003-02-23  
Information Services        Wilson                  38.4615      2003-02-23   2003-02-23  
Information Services        Conroy                  39.6635      2003-03-08   2003-03-08  
Information Services        Trenary                 50.4808      2003-01-12   2003-01-12  
  
```  
  
### <a name="b-using-first_value-and-last_value-in-a-computed-expression"></a>B. 計算された式で FIRST_VALUE と LAST_VALUE を使用する  
 次の例では、計算された式で FIRST_VALUE 関数と LAST_VALUE 関数を使用して、指定された人数の従業員について、その年の現在の四半期の販売ノルマの値と第 1 四半期および第 4 四半期の販売ノルマの値の差をそれぞれ示します。 FIRST_VALUE 関数は、その年の第 1 四半期の販売ノルマの値を返し、その値を現在の四半期の販売ノルマの値から引きます。 そして、その結果を DifferenceFromFirstQuarter という派生列に返します。 その年の第 1 四半期については、DifferenceFromFirstQuarter 列の値は 0 です。 LAST_VALUE 関数は、その年の最終四半期の販売ノルマの値を返し、その値を現在の四半期の販売ノルマの値から引きます。 そして、その結果を DifferenceFromLastQuarter という派生列に返します。  その年の最終四半期については、DifferenceFromLastQuarter 列の値は 0 です。  
  
 以下に示すように、この例の場合、DifferenceFromLastQuarter 列でゼロ以外の値を返すには、"RANGE BETWEEN CURRENT ROW AND UNBOUNDED FOLLOWING" 句が必要です。 既定の範囲は "RANGE BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW" です。 この例では、この既定の範囲を使用した場合 (または、範囲を含めない結果、既定値が使用される場合)、DifferenceFromLastQuarter 列にゼロが返されます。 詳細については、を参照してください。 [OVER 句 &#40;Transact-SQL&#41;](../../t-sql/queries/select-over-clause-transact-sql.md).  
  
```sql  
USE AdventureWorks2012;  
SELECT BusinessEntityID, DATEPART(QUARTER,QuotaDate)AS Quarter, YEAR(QuotaDate) AS SalesYear,   
    SalesQuota AS QuotaThisQuarter,   
    SalesQuota - FIRST_VALUE(SalesQuota)   
        OVER (PARTITION BY BusinessEntityID, YEAR(QuotaDate)   
              ORDER BY DATEPART(QUARTER,QuotaDate) ) AS DifferenceFromFirstQuarter,   
    SalesQuota - LAST_VALUE(SalesQuota)   
        OVER (PARTITION BY BusinessEntityID, YEAR(QuotaDate)   
              ORDER BY DATEPART(QUARTER,QuotaDate)   
              RANGE BETWEEN CURRENT ROW AND UNBOUNDED FOLLOWING ) AS DifferenceFromLastQuarter   
FROM Sales.SalesPersonQuotaHistory   
WHERE YEAR(QuotaDate) > 2005   
AND BusinessEntityID BETWEEN 274 AND 275   
ORDER BY BusinessEntityID, SalesYear, Quarter;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
BusinessEntityID Quarter     SalesYear   QuotaThisQuarter      DifferenceFromFirstQuarter DifferenceFromLastQuarter  
---------------- ----------- ----------- --------------------- --------------------------- -----------------------  
274              1           2006        91000.00              0.00                        -63000.00  
274              2           2006        140000.00             49000.00                    -14000.00  
274              3           2006        70000.00              -21000.00                   -84000.00  
274              4           2006        154000.00             63000.00                    0.00  
274              1           2007        107000.00             0.00                        -9000.00  
274              2           2007        58000.00              -49000.00                   -58000.00  
274              3           2007        263000.00             156000.00                   147000.00  
274              4           2007        116000.00             9000.00                     0.00  
274              1           2008        84000.00              0.00                        -103000.00  
274              2           2008        187000.00             103000.00                   0.00  
275              1           2006        502000.00             0.00                        -822000.00  
275              2           2006        550000.00             48000.00                    -774000.00  
275              3           2006        1429000.00            927000.00                   105000.00  
275              4           2006        1324000.00            822000.00                   0.00  
275              1           2007        729000.00             0.00                        -489000.00  
275              2           2007        1194000.00            465000.00                   -24000.00  
275              3           2007        1575000.00            846000.00                   357000.00  
275              4           2007        1218000.00            489000.00                   0.00  
275              1           2008        849000.00             0.00                        -20000.00  
275              2           2008        869000.00             20000.00                    0.00  
  
(20 row(s) affected)  
  
```  
  
## <a name="see-also"></a>参照  

 [First_Value &#40;Transact-SQL&#41;](first-value-transact-sql.md)  
