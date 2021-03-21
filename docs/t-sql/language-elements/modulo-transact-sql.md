---
description: '% (剰余) (Transact-SQL)'
title: '% (剰余) (Transact-SQL) | Microsoft Docs'
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- modulo
- modulus
- '% (Modulo)'
- '% (Modulus)'
- MOD_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- '% (modulo operator)'
- '% (modulus operator)'
- remainder of division operation
- modulo operator (%)
- modulus operator (%)
ms.assetid: f93c662e-f405-486e-bf23-a2d03907b5bd
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 38a4cc536d506e9f70a68198a7a7ea5550c84eca
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104748862"
---
# <a name="-modulus-transact-sql"></a>% (剰余) (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  ある値を別の値で除算した結果の余りを返します。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql  
dividend % divisor  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
 *dividend*  
 除算される数値式です。 *dividend* には、整数および通貨型に分類されるデータ型、または **numeric** データ型の有効な [式](../../t-sql/language-elements/expressions-transact-sql.md)を指定する必要があります。  
  
 *divisor*  
 被除数を除算する数値式です。 *divisor* には、整数および通貨型に分類されるデータ型、または **numeric** データ型の任意の有効な式を指定する必要があります。  
  
## <a name="result-types"></a>戻り値の型  
 2 つの引数のデータ型によって決まります。  
  
## <a name="remarks"></a>解説  
 剰余算術演算子は、SELECT ステートメントの選択リストの中で、列名、数値定数、整数や通貨型に分類されるデータ型、または **numeric** データ型の有効な式と組み合わせて使用できます。  
  
## <a name="examples"></a>例  
  
### <a name="a-simple-example"></a>A. 簡単な例  
 次の例では、38 を 5 で割ります。 この結果は、答えの整数部分である 7 となります。また、剰余演算子を使用して余り 3 を返す方法も示しています。  
  
```sql  
SELECT 38 / 5 AS Integer, 38 % 5 AS Remainder;
```  
  
### <a name="b-example-using-columns-in-a-table"></a>B. テーブルの列を使用した例  
 次の例では、製品 ID 番号、製品の単価、および注文された製品数を各製品の価格で割った余り (剰余) を整数値に変換した値を返します。  
  
```sql  
-- Uses AdventureWorks  
  
SELECT TOP(100)ProductID, UnitPrice, OrderQty,  
   CAST((UnitPrice) AS INT) % OrderQty AS Modulo  
FROM Sales.SalesOrderDetail;  
GO  
```  
  
## <a name="examples-sssdwfull-and-sspdw"></a>例: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)]、[!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="c-simple-example"></a>C. 簡単な例  
 次の例は、3 を 2 で割ったときの `%` 演算子の結果を示しています。  
  
```sql  
-- Uses AdventureWorks  
  
SELECT TOP(1) 3%2 FROM dimEmployee;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
---------   
1         
```  
  
## <a name="see-also"></a>参照  
 [組み込み関数 &#40;Transact-SQL&#41;](~/t-sql/functions/functions.md)   
 [LIKE &#40;Transact-SQL&#41;](../../t-sql/language-elements/like-transact-sql.md)   
 [演算子 &#40;Transact-SQL&#41;](../../t-sql/language-elements/operators-transact-sql.md)   
 [SELECT &#40;Transact-SQL&#41;](../../t-sql/queries/select-transact-sql.md)   
 [%= &#40;剰余代入&#41; &#40;Transact-SQL&#41;](../../t-sql/language-elements/modulo-equals-transact-sql.md)   
 [複合演算子 &#40;Transact-SQL&#41;](../../t-sql/language-elements/compound-operators-transact-sql.md)  
  
  


