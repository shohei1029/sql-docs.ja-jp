---
description: POWER (Transact-SQL)
title: POWER (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/13/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- POWER_TSQL
- POWER
dev_langs:
- TSQL
helpviewer_keywords:
- POWER function
ms.assetid: 0fd34494-90b9-4559-8011-a8c1b9f40239
author: julieMSFT
ms.author: jrasnick
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 19dc7c34a3544438e5697c793ec77ebe4724620d
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104747962"
---
# <a name="power-transact-sql"></a>POWER (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  指定されたべき乗の指定された式の値を返します。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql  
POWER ( float_expression , y )  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
 *float_expression*  
 **float** 型、または暗黙的に **float** 型に変換できる [式](../../t-sql/language-elements/expressions-transact-sql.md)を指定します。  
  
 *y*  
 *float_expression* の乗数を指定します。 *y* を除く、真数または概数数値の正確なデータ型に分類される式を指定できます、 **ビット** データ型。  
  
## <a name="return-types"></a>戻り値の型  
 戻り値の型は、*float_expression* の入力型によって異なります。
 
|入力の種類|の戻り値の型 :|  
|----------|-----------|  
|**float**、**real**|**float**|
|**decimal(*p*, *s*)**|**decimal(38, *s*)**|
|**int**、**smallint**、**tinyint**|**int**|
|**bigint**|**bigint**|
|**money**、 **smallmoney**|**money**|
|**bit**、**char**、**nchar**、**varchar**、**nvarchar**|**float**|
 
結果が戻り値の型に一致しない合、算術オーバーフロー エラーが発生します。
  
## <a name="examples"></a>例  
  
### <a name="a-using-power-to-return-the-cube-of-a-number"></a>A. POWER を使用して数値の 3 乗を返す  
 次の例は、数値を 3 乗する方法を示しています。  
  
```sql  
DECLARE @input1 FLOAT;  
DECLARE @input2 FLOAT;  
SET @input1= 2;  
SET @input2 = 2.5;  
SELECT POWER(@input1, 3) AS Result1, POWER(@input2, 3) AS Result2;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
Result1                Result2  
---------------------- ----------------------  
8                      15.625  
  
(1 row(s) affected)  
```  
  
### <a name="b-using-power-to-show-results-of-data-type-conversion"></a>B. POWER を使用してデータ型変換の結果を示す  
 例を次に、どのように *float_expression* 予期しない結果を返すことができるデータ型を保持します。  
  
```sql 
SELECT   
POWER(CAST(2.0 AS FLOAT), -100.0) AS FloatResult,  
POWER(2, -100.0) AS IntegerResult,  
POWER(CAST(2.0 AS INT), -100.0) AS IntegerResult,  
POWER(2.0, -100.0) AS Decimal1Result,  
POWER(2.00, -100.0) AS Decimal2Result,  
POWER(CAST(2.0 AS DECIMAL(5,2)), -100.0) AS Decimal2Result;  
GO  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
FloatResult            IntegerResult IntegerResult Decimal1Result Decimal2Result Decimal2Result  
---------------------- ------------- ------------- -------------- -------------- --------------  
7.88860905221012E-31   0             0             0.0            0.00           0.00  
```  
  
### <a name="c-using-power"></a>C. POWER を使用する  
 次の例では、`POWER` に対する `2` の結果を返します。  
  
```sql  
DECLARE @value INT, @counter INT;  
SET @value = 2;  
SET @counter = 1;  
  
WHILE @counter < 5  
   BEGIN  
      SELECT POWER(@value, @counter)  
      SET NOCOUNT ON  
      SET @counter = @counter + 1  
      SET NOCOUNT OFF  
   END;  
GO  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
-----------   
2             
  
(1 row(s) affected)  
  
-----------   
4             
  
(1 row(s) affected)  
  
-----------   
8             
  
(1 row(s) affected)  
  
-----------   
16            
  
(1 row(s) affected)  
```  
  
## <a name="examples-sssdwfull-and-sspdw"></a>例: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)]、[!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="d-using-power-to-return-the-cube-of-a-number"></a>D: POWER を使用して数値の 3 乗を返す  
 次の例は、`2.0` の 3 乗の `POWER` 結果を示しています。  
  
```sql  
SELECT POWER(2.0, 3);  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 ```
------------ 
8.0
```  
  
## <a name="see-also"></a>参照  
 [decimal 型と numeric 型 &#40;Transact-SQL&#41;](../../t-sql/data-types/decimal-and-numeric-transact-sql.md)   
 [float と real &#40;Transact-SQL&#41;](../../t-sql/data-types/float-and-real-transact-sql.md)   
 [int、bigint、smallint、tinyint &#40;Transact-SQL&#41;](../../t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql.md)   
 [数学関数 &#40;Transact-SQL&#41;](../../t-sql/functions/mathematical-functions-transact-sql.md)   
 [money および smallmoney &#40;Transact-SQL&#41;](../../t-sql/data-types/money-and-smallmoney-transact-sql.md)  
  
  

