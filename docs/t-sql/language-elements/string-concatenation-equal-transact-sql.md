---
title: += 文字列連結
description: 2 つの文字列を連結し、その結果の文字列を演算の結果に設定します。
titleSuffix: SQL Server (Transact-SQL)
ms.custom: seo-lt-2019
ms.date: 12/07/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
dev_langs:
- TSQL
helpviewer_keywords:
- concatenate strings
- string concatenation
- += (concatenate operator)
ms.assetid: 4aaeaab7-9b2b-48e0-8487-04ed672ebcb1
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: e2b0bf5c2ae5c1514a54207c0ece773b3e6d4ed2
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104752952"
---
# <a name="-string-concatenation-assignment-transact-sql"></a>+= (文字列連結代入) (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  2 つの文字列を連結し、その結果の文字列を演算の結果に設定します。 たとえば、変数 @x が 'Adventure' である場合、@x += 'Works' は @x の元の値を取得し、その文字列に 'Works' を追加して、@x に 'AdventureWorks' という新しい値を設定します。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql
expression += expression  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
 *式 (expression)*  
 任意の文字データ型の任意の有効な[式](../../t-sql/language-elements/expressions-transact-sql.md)です。  
  
## <a name="result-types"></a>戻り値の型  
 変数に対して定義されているデータ型を返します。  
  
## <a name="remarks"></a>解説  
 SET @v1 += 'expression' は SET @v1 = @v1 + ('expression') と同じです。 また、SET @v1 = @v2 + @v3 + @v4 は SET @v1 = (@v2 + @v3) + @v4 と同じです。  
  
 += 演算子を変数なしで使用することはできません。 たとえば、次のコードはエラーになります。  
  
```sql  
SELECT 'Adventure' += 'Works'  
```  
  
## <a name="examples"></a>例  
### <a name="a-concatenation-using--operator"></a>A. += 演算子を使用した連結
 次の例では、`+=` 演算子を使用して文字列を連結しています。  
  
```sql  
DECLARE @v1 VARCHAR(40);  
SET @v1 = 'This is the original.';  
SET @v1 += ' More text.';  
PRINT @v1;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 `This is the original. More text.`  
  
### <a name="b-order-of-evaluation-while-concatenating-using--operator"></a>B. += 演算子を使用して連結するときの評価の順序
次の例では、複数の文字列を連結して 1 つの長い文字列を形成し、最終的な文字列の計算を試行します。 この例では、連結演算子を使用するときの評価順序と切り捨てルールを示します。 

```sql
DECLARE @x VARCHAR(4000) = REPLICATE('x', 4000)
DECLARE @z VARCHAR(8000) = REPLICATE('z',8000)
DECLARE @y VARCHAR(max);
 
SET @y = '';
SET @y += @x + @z;
SELECT LEN(@y) AS Y; -- 8000
 
SET @y = '';
SET @y = @y + @x + @z;
SELECT LEN(@y) AS Y; -- 12000
 
SET @y = '';
SET @y = @y +(@x + @z);
SELECT LEN(@y) AS Y; -- 8000
-- or
SET @y = '';
SET @y = @x + @z + @y;
SELECT LEN(@y) AS Y; -- 8000
GO
```
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 ```
 Y       
 ------- 
 8000 
  
 (1 row(s) affected) 
  
    
 Y       
 ------- 
 12000 
  
 (1 row(s) affected) 

 Y       
 ------- 
 8000 
  
 (1 row(s) affected) 
  
 Y       
 ------- 
 8000 
  
 (1 row(s) affected)
  ```   
   
## <a name="see-also"></a>参照  
 [演算子 &#40;Transact-SQL&#41;](../../t-sql/language-elements/operators-transact-sql.md)   
 [+= &#40;加算代入&#41; &#40;Transact-SQL&#41;](../../t-sql/language-elements/add-equals-transact-sql.md)   
 [+ &#40;文字列連結&#41; &#40;Transact-SQL&#41;](../../t-sql/language-elements/string-concatenation-transact-sql.md)  
  
  
