---
description: CUME_DIST (Transact-SQL)
title: CUME_DIST (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 07/24/2017
ms.prod: sql
ms.prod_service: synapse-analytics, database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- CUME_DIST
- CUME_DIST_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- CUME_DIST function
- analytic functions, CUME_DIST
ms.assetid: 491b07f3-9ffd-4cdd-93e5-5abb636fc5ef
author: cawrites
ms.author: chadam
monikerRange: =azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 82a9847d9903cb6f0f4c7faab22ce27247c09bd6
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104751942"
---
# <a name="cume_dist-transact-sql"></a>CUME_DIST (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa](../../includes/applies-to-version/sql-asdb-asdbmi-asa.md)]

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] について、値のグループ内の値の累積分布を計算します。 つまり、`CUME_DIST` は、値のグループにおける指定された値の相対位置を計算します。 行 _r_ の値の `CUME_DIST` は行 _r_ の値以下の値を持つ行数として定義されます。これは、パーティションまたはクエリ結果セットで評価された行数で割った値です。 `CUME_DIST` は `PERCENT_RANK` 関数に似ています。
  
![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)
  
## <a name="syntax"></a>構文  
  
```syntaxsql
CUME_DIST( )  
    OVER ( [ partition_by_clause ] order_by_clause )  
```

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
OVER **(** [ _partition\_by\_clause_ ] _order\_by\_clause_)  

_partition\_by\_clause_ は、FROM 句の結果セットをパーティションに分割します。このパーティションに関数が適用されます。 _partition\_by\_clause_ 引数を指定しない場合、`CUME_DIST` ではクエリ結果セットのすべての行を 1 つのグループとして扱います。 _order\_by\_clause_ は、操作が実行される論理的順序を決定します。 `CUME_DIST` には _order\_by\_clause_ が必要です。 `CUME_DIST` では、OVER 構文の \<rows or range clause> は受け入れられません。 詳細については、を参照してください。 [OVER 句 &#40;Transact-SQL&#41;](../../t-sql/queries/select-over-clause-transact-sql.md).
  
## <a name="return-types"></a>戻り値の型
**float(53)**
  
## <a name="remarks"></a>解説  
`CUME_DIST` は、0 より大きく 1 以下の値の範囲を返します。 同順位の値は常に、同じ累積分布の値に評価されます。 `CUME_DIST` は既定で NULL 値を含み、これらの値を最小限の値として扱います。
  
`CUME_DIST` は非決定的です。 詳細については、「 [決定的関数と非決定的関数](../../relational-databases/user-defined-functions/deterministic-and-nondeterministic-functions.md)」を参照してください。
  
## <a name="examples"></a>例  
この例では、`CUME_DIST` 関数を使用して、特定の部門の各従業員の給与を百分位数で計算します。 `CUME_DIST` は、同じ部門内で、現在の従業員の給与以下の従業員の割合を表す値を返します。 `PERCENT_RANK` 関数は、部門における従業員の給与の割合の順位を計算します。 結果セット行を部門別に分割するために、この例では _partition\_by\_clause_ 値を指定しています。 OVER 句の ORDER BY 句によって、各パーティション内の行が論理的に順序付けられます。 SELECT ステートメントの ORDER BY 句によって、結果セットの表示順序が決定されます。
  
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
  
## <a name="see-also"></a>関連項目
[PERCENT_RANK &#40;Transact-SQL&#41;](../../t-sql/functions/percent-rank-transact-sql.md)
  
  
