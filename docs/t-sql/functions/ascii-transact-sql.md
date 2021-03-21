---
description: ASCII (Transact-SQL)
title: ASCII (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 11/14/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- ASCII_TSQL
- ASCII
dev_langs:
- TSQL
helpviewer_keywords:
- ASCII function
- characters [SQL Server], ASCII
- code [SQL Server], ASCII
- leftmost character of expression
ms.assetid: 45c2044a-0593-4805-8bae-0fad4bde2e6b
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: bc38a1198aae090b4198779b311dd4bd921cd6c1
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104740422"
---
# <a name="ascii-transact-sql"></a>ASCII (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

文字式の左端の文字の ASCII コード値を返します。
  
![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)
  
## <a name="syntax"></a>構文  
  
```syntaxsql
ASCII ( character_expression )  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
*character_expression*  
**char** 型または **varchar** 型の [式](../../t-sql/language-elements/expressions-transact-sql.md)。
  
## <a name="return-types"></a>戻り値の型
 **int**  
  
## <a name="remarks"></a>解説
ASCII は、情報交換用米国標準コード (**A** merican **S** tandard **C** ode for **I** nformation **I** nterchange) の略語です。 最新のコンピューター上で文字エンコード標準として機能します。 ASCII 文字の一覧については、「[ASCII](https://www.wikipedia.org/wiki/ASCII)」の **印刷可能文字** に関するセクションを参照してください。

ASCII は 7 ビット文字セットです。 拡張 ASCII または高 ASCII は、`ASCII` 関数によって処理されない 8 ビット文字セットです。 

## <a name="examples"></a>例 

### <a name="a-this-example-assumes-an-ascii-character-set-and-returns-the-ascii-value-for-6-characters"></a>A. この例では、ASCII 文字セットの使用を前提として、6 つの文字の `ASCII` 値を返します。
  
```sql
SELECT ASCII('A') AS A, ASCII('B') AS B,   
ASCII('a') AS a, ASCII('b') AS b,  
ASCII(1) AS [1], ASCII(2) AS [2];  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
A           B           a           b           1           2  
----------- ----------- ----------- ----------- ----------- -----------  
65          66          97          98          49          50  
```  
  
### <a name="b-this-examples-shows-how-a-7-bit-ascii-value-is-returned-correctly-but-an-8-bit-extended-ascii-value-is-not-handled"></a>B. この例では、7 ビットの ASCII 値が正しく返されますが、8 ビットの拡張 ASCII 値は処理されないことを確認できます。

```sql
SELECT ASCII('P') AS [ASCII], ASCII('æ') AS [Extended_ASCII];
```

[!INCLUDE[ssResult](../../includes/ssresult-md.md)]

```
ASCII       Extended_ASCII
----------- --------------
80          195
```

上の結果が正しい文字コード ポイントにマッピングされているかどうかを確認するには、`CHAR` または `NCHAR` 関数で出力値を使用します。

```sql
SELECT NCHAR(80) AS [CHARACTER], NCHAR(195) AS [CHARACTER];
```

[!INCLUDE[ssResult](../../includes/ssresult-md.md)]

```
CHARACTER CHARACTER
--------- ---------
P         Ã
```

前の結果から、コードポイント 195 の文字が **Ã** であり、**æ** ではないことを確認してください。 これは、`ASCII` 関数で最初の 7 ビット ストリームを読み取れるが、余分なビットは読み取れないためです。 文字 `æ` の正しいコード ポイントは `UNICODE` 関数で見つけることができます。この関数では正しい文字コード ポイントを返すことができます。

```sql
SELECT UNICODE('æ') AS [Extended_ASCII], NCHAR(230) AS [CHARACTER];
```

[!INCLUDE[ssResult](../../includes/ssresult-md.md)]

```
Extended_ASCII CHARACTER
-------------- ---------
230            æ
```

## <a name="see-also"></a>関連項目
 [CHAR &#40;Transact-SQL&#41;](../../t-sql/functions/char-transact-sql.md)  
 [NCHAR &#40;Transact-SQL&#41;](../../t-sql/functions/nchar-transact-sql.md)  
 [UNICODE &#40;Transact-SQL&#41;](../../t-sql/functions/unicode-transact-sql.md)  
 [文字列関数 &#40;Transact-SQL&#41;](../../t-sql/functions/string-functions-transact-sql.md)
  
