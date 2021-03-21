---
description: '- (負号) (Transact-SQL)'
title: '- (負号) (Transact-SQL) | Microsoft Docs'
ms.custom: ''
ms.date: 03/13/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- negative
dev_langs:
- TSQL
helpviewer_keywords:
- '- (negative)'
- negative operator (-)
- negative values
ms.assetid: d6c14d14-d379-403b-82db-c197ad58c896
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 0dcd07b4ae448c57eb3a5cace38110095771efbc
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104750482"
---
# <a name="unary-operators---negative"></a>単項演算子 - 負号
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  数値式について負の値を返します (単項演算子)。 単項演算子は、数値型に分類されるデータ型の 1 つの式に対してだけ操作を実行します。   
  
|演算子|説明|  
|--------------|-------------|  
|[+ (正号)](../../t-sql/language-elements/unary-operators-positive.md)|数値は正の値です。|  
|[- (負号)](../../t-sql/language-elements/unary-operators-negative.md)|数値は負の値です。|  
|[~ (ビット演算子 NOT)](../../t-sql/language-elements/bitwise-not-transact-sql.md)|値の 1 の補数を返します。|  
  
 + (正) 演算子と - (負) 演算子は、数値型カテゴリのいずれかのデータ型の任意の式で使用します。 ~ (ビットごとの NOT) 演算子を使用できるのは、整数型に分類されるデータ型の式だけです。 
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql
- numeric_expression  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
 *numeric_expression*  
 有効な[式](../../t-sql/language-elements/expressions-transact-sql.md)を指定します。データ型は、日付と時刻以外の数値データ型であることが必要です。  
  
## <a name="result-types"></a>戻り値の型  
 *numeric_expression* のデータ型を返します。ただし符号なし **tinyint** 型の式は例外で、この場合の結果は符号ありの **smallint** 型になります。  
  
## <a name="examples"></a>例  
  
### <a name="a-setting-a-variable-to-a-negative-value"></a>A. 変数に負の値を設定する  
 次の例では、変数に負の値を設定します。  
  
```sql 
USE tempdb;  
GO  
DECLARE @MyNumber DECIMAL(10,2);  
SET @MyNumber = -123.45;  
SELECT @MyNumber AS NegativeValue;  
GO  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
NegativeValue  
---------------------------------------  
-123.45  
  
(1 row(s) affected)  
  
```  
  
### <a name="b-changing-a-variable-to-a-negative-value"></a>B. 変数を負の値に変更する  
 次の例では、変数を負の値に変更します。  
  
```sql  
USE tempdb;  
GO  
DECLARE @Num1 INT;  
SET @Num1 = 5;  
SELECT @Num1 AS VariableValue, -@Num1 AS NegativeValue;  
GO  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
VariableValue NegativeValue  
------------- -------------  
5             -5  
  
(1 row(s) affected)  
  
```  
  
## <a name="examples-sssdwfull-and-sspdw"></a>例: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)]、[!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="c-returning-the-negative-of-a-positive-constant"></a>C. 正の定数の負の値を返す  
 次の例では、正の定数の負の値を返します。  
  
```sql  
USE ssawPDW;  
  
SELECT TOP (1) - 17 FROM DimEmployee;  
```  
  
 戻り値  
  
```  
-17  
```  
  
### <a name="d-returning-the-positive-of-a-negative-constant"></a>D. 負の定数の正の値を返す  
 次の例では、負の定数の正の値を返します。  
  
```sql  
USE ssawPDW;  
  
SELECT TOP (1) - ( - 17) FROM DimEmployee;  
```  
  
 戻り値  
  
```  
17  
```  
  
### <a name="e-returning-the-negative-of-a-column"></a>E. 列の負の値を返す  
 次の例では、`dimEmployee` テーブルの各従業員に対し、`BaseRate` 値の負の値を返します。  
  
```sql  
USE ssawPDW;  
  
SELECT - BaseRate FROM DimEmployee;  
```  
  
## <a name="see-also"></a>参照  
 [データ型 &#40;Transact-SQL&#41;](../../t-sql/data-types/data-types-transact-sql.md)   
 [式 &#40;Transact-SQL&#41;](../../t-sql/language-elements/expressions-transact-sql.md)   
 [演算子 &#40;Transact-SQL&#41;](../../t-sql/language-elements/operators-transact-sql.md)  
  
  

