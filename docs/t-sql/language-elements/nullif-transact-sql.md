---
description: NULLIF (Transact-SQL)
title: NULLIF (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 09/08/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- NULLIF
- NULLIF_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- null values [SQL Server], equivalent expressions
- expressions [SQL Server], null values
- NULLIF function
- equivalent expressions [SQL Server]
ms.assetid: 44c7b67e-74c7-4bb9-93a4-7a3016bd2feb
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: a54ea5ad6e7c66bc983e9d8101b7af235cb090cd
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104752352"
---
# <a name="nullif-transact-sql"></a>NULLIF (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  指定された 2 つの式が等しい場合に NULL 値を返します。 たとえば、`SELECT NULLIF(4,4) AS Same, NULLIF(5,7) AS Different;` の最初の列 (4 と 4) は、2 つの入力値が同じなので NULL を返します。 2 つ目の列は、2 つの入力値が異なるため、最初の値 (5) を返します。 
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql  
NULLIF ( expression , expression )  
```  
  
## <a name="arguments"></a>引数  
 *式 (expression)*  
 任意の有効なスカラー[式](../../t-sql/language-elements/expressions-transact-sql.md)です。  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="return-types"></a>戻り値の型
 最初の *式* と同じ型を返します。  
  
 NULLIF は、2 つの式が等しくない場合、最初の *式* を返します。 式が等しい場合、NULLIF は最初の *式* のデータ型の NULL 値を返します。  
  
## <a name="remarks"></a>注釈  
 NULLIF は、2 つの式を比較し、その 2 つが等価な場合に NULL を返す検索 CASE 式と同じです。  
  
 NULLIF 関数の中では、RAND() など時間に依存する関数は使用しないことをお勧めします。 関数が 2 回呼び出されて評価され、それぞれ異なる結果が返されることがあります。  
  
## <a name="examples"></a>例  
  
### <a name="a-returning-budget-amounts-that-have-not-changed"></a>A. 変更のない予算額を返す  
 次の例では、部門 (`dept`)、今年度予算 (`current_year`)、および昨年度予算 (`previous_year`) で構成される `budgets` テーブルを作成します。 今年度予算が昨年度予算と変わらない部門については `NULL` を使用し、今年度予算がまだ決定していない場合は `0` を使用します。 昨年度予算の値を使用する場合も含めて (`previous_year` が `current_year` の場合は `NULL` の値を使用)、今年度予算を受け取った部門についてだけその平均値を求めるには、`NULLIF` 関数と `COALESCE` 関数を組み合わせて使用します。  
  
```sql  
CREATE TABLE dbo.budgets  
(  
   dept            TINYINT   IDENTITY,  
   current_year    DECIMAL   NULL,  
   previous_year   DECIMAL   NULL  
);  
INSERT budgets VALUES(100000, 150000);  
INSERT budgets VALUES(NULL, 300000);  
INSERT budgets VALUES(0, 100000);  
INSERT budgets VALUES(NULL, 150000);  
INSERT budgets VALUES(300000, 250000);  
GO    
SET NOCOUNT OFF;  
SELECT AVG(NULLIF(COALESCE(current_year,  
   previous_year), 0.00)) AS [Average Budget]  
FROM budgets;  
GO  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 ```
 Average Budget  
 --------------  
 212500.000000  
 (1 row(s) affected)
 ```  
  
### <a name="b-comparing-nullif-and-case"></a>B. NULLIF と CASE を比較する  
 `NULLIF` と `CASE` の類似性を示すため、次のクエリでは、`MakeFlag` 列と `FinishedGoodsFlag` 列の値が同じかどうかを評価します。 最初のクエリでは `NULLIF` を使用します。 2 番目のクエリでは `CASE` 式を使用します。  
  
```sql  
USE AdventureWorks2012;  
GO  
SELECT ProductID, MakeFlag, FinishedGoodsFlag,   
   NULLIF(MakeFlag,FinishedGoodsFlag) AS [Null if Equal]  
FROM Production.Product  
WHERE ProductID < 10;  
GO  
  
SELECT ProductID, MakeFlag, FinishedGoodsFlag, [Null if Equal] =  
   CASE  
       WHEN MakeFlag = FinishedGoodsFlag THEN NULL  
       ELSE MakeFlag  
   END  
FROM Production.Product  
WHERE ProductID < 10;  
GO  
```  

### <a name="c-returning-budget-amounts-that-contain-no-data"></a>C. データを含まない予算額を返す  
 次の例では、`budgets` テーブルを作成し、データを読み込み、`current_year` と `previous_year` のいずれにもデータが含まれない場合は `NULLIF` を使用して null を返します。  
  
```sql  
CREATE TABLE budgets (  
   dept           TINYINT,  
   current_year   DECIMAL(10,2),  
   previous_year  DECIMAL(10,2)  
);  
  
INSERT INTO budgets VALUES(1, 100000, 150000);  
INSERT INTO budgets VALUES(2, NULL, 300000);  
INSERT INTO budgets VALUES(3, 0, 100000);  
INSERT INTO budgets VALUES(4, NULL, 150000);  
INSERT INTO budgets VALUES(5, 300000, 300000);  
  
SELECT dept, NULLIF(current_year,  
   previous_year) AS LastBudget  
FROM budgets;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 ```
 dept   LastBudget  
 ----   -----------  
 1      100000.00  
 2      null 
 3      0.00  
 4      null  
 5      null
 ```  
  
## <a name="see-also"></a>参照  
 [CASE &#40;Transact-SQL&#41;](../../t-sql/language-elements/case-transact-sql.md)   
 [decimal 型と numeric 型 &#40;Transact-SQL&#41;](../../t-sql/data-types/decimal-and-numeric-transact-sql.md)   
 [システム関数 &#40;Transact-SQL&#41;](../../relational-databases/system-functions/system-functions-category-transact-sql.md)  
  
  

