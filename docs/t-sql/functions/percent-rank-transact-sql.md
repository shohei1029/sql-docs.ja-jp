---
description: PERCENT_RANK (Transact-SQL)
title: PERCENT_RANK (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 10/20/2015
ms.prod: sql
ms.prod_service: synapse-analytics, database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- PERCENT_RANK_TSQL
- PERCENT_RANK
dev_langs:
- TSQL
helpviewer_keywords:
- PERCENT_RANK function
- analytic functions, PERCENT_RANK
ms.assetid: e361c2d4-c01f-4da4-8e89-1ddc724a2629
author: julieMSFT
ms.author: jrasnick
monikerRange: =azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: ab0ebbaea4c00c1cf10fa8d6728ba44b7ea2180f
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104746532"
---
# <a name="percent_rank-transact-sql"></a>PERCENT_RANK (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa](../../includes/applies-to-version/sql-asdb-asdbmi-asa.md)]

  [!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] の行のグループ内の行の相対的な順位を計算します。 PERCENT_RANK を使用して、クエリの結果セットまたはパーティション内の値の相対的な位置を評価します。 PERCENT_RANK は CUME_DIST 関数に似ています。  
  
## <a name="syntax"></a>構文  
  
```syntaxsql  
PERCENT_RANK( )  
    OVER ( [ partition_by_clause ] order_by_clause )  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
 OVER **(** [ _partition\_by\_clause_ ] _order\_by\_clause_ **)**  
 *partition_by_clause* は、FROM 句で生成された結果セットをパーティションに分割します。このパーティションに関数が適用されます。 指定しない場合、関数ではクエリ結果セットのすべての行を 1 つのグループとして扱います。 _order\_by\_clause_ は、操作が実行される論理的順序を決定します。 *order_by_clause* が必要です。 OVER 構文の \<rows or range clause\> は、PERCENT_RANK 関数では指定できません。  詳細については、を参照してください。 [OVER 句 &#40;Transact-SQL&#41;](../../t-sql/queries/select-over-clause-transact-sql.md).  
  
## <a name="return-types"></a>戻り値の型  
 **float(53)**  
  
## <a name="general-remarks"></a>全般的な解説  
 PERCENT_RANK によって返される値の範囲は、0 より大きく、1 以下になります。 セットの最初の行では PERCENT_RANK は 0 です。 既定で NULL 値が含まれ、最小限の値として扱われます。  
  
 PERCENT_RANK は非決定的です。 詳細については、「 [決定的関数と非決定的関数](../../relational-databases/user-defined-functions/deterministic-and-nondeterministic-functions.md)」を参照してください。  
  
## <a name="examples"></a>例  
 次の例では、CUME_DIST 関数を使用して、特定の部門の各従業員の給与を百分位数で計算します。 CUME_DIST 関数によって返される値は、同じ部門の現在の従業員の給与以下の従業員が部門に占める割合を表します。 PERCENT_RANK 関数は、部門内の従業員の給与のランクが部門に占める割合を計算します。 PARTITION BY 句を指定して、部門ごとに結果セットの行をパーティション分割します。 OVER 句の ORDER BY 句によって、各パーティション内の行の順序が決められます。 SELECT ステートメントの ORDER BY 句によって、結果セット全体で行の並べ替えが行われます。  
  
```sql  
USE AdventureWorks2012;  
GO  
SELECT Department, LastName, Rate,   
       CUME_DIST () OVER (PARTITION BY Department ORDER BY Rate) AS CumeDist,   
       PERCENT_RANK() OVER (PARTITION BY Department ORDER BY Rate ) AS PctRank  
FROM HumanResources.vEmployeeDepartmentHistory AS edh  
    INNER JOIN HumanResources.EmployeePayHistory AS e    
    ON e.BusinessEntityID = edh.BusinessEntityID  
WHERE Department IN (N'Information Services',N'Document Control')   
ORDER BY Department, Rate DESC;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
  
Department             LastName               Rate                  CumeDist               PctRank  
---------------------- ---------------------- --------------------- ---------------------- ----------------------  
Document Control       Arifin                 17.7885               1                      1  
Document Control       Norred                 16.8269               0.8                    0.5  
Document Control       Kharatishvili          16.8269               0.8                    0.5  
Document Control       Chai                   10.25                 0.4                    0  
Document Control       Berge                  10.25                 0.4                    0  
Information Services   Trenary                50.4808               1                      1  
Information Services   Conroy                 39.6635               0.9                    0.888888888888889  
Information Services   Ajenstat               38.4615               0.8                    0.666666666666667  
Information Services   Wilson                 38.4615               0.8                    0.666666666666667  
Information Services   Sharma                 32.4519               0.6                    0.444444444444444  
Information Services   Connelly               32.4519               0.6                    0.444444444444444  
Information Services   Berg                   27.4038               0.4                    0  
Information Services   Meyyappan              27.4038               0.4                    0  
Information Services   Bacon                  27.4038               0.4                    0  
Information Services   Bueno                  27.4038               0.4                    0  
(15 row(s) affected)  
```  
  
## <a name="see-also"></a>参照  
 [CUME_DIST &#40;Transact-SQL&#41;](../../t-sql/functions/cume-dist-transact-sql.md)  
  
  
