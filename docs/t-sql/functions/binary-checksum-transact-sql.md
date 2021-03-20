---
description: BINARY_CHECKSUM (Transact-SQL)
title: BINARY_CHECKSUM (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 07/24/2017
ms.prod: sql
ms.prod_service: synapse-analytics, database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- BINARY_CHECKSUM
- BINARY_CHECKSUM_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- BINARY_CHECKSUM function
- binary [SQL Server], checksum values
ms.assetid: 07fece4d-58e3-446e-a3b5-92fe24d2d1fb
author: cawrites
ms.author: chadam
monikerRange: =azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: c0edde3340e70cc41c09071e57d4639075dd892e
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104740292"
---
# <a name="binary_checksum--transact-sql"></a>BINARY_CHECKSUM (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa.md)]

テーブルの 1 つの行、または一連の式に対して計算された、バイナリのチェックサム値を返します。
  
![記事リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "記事リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)
  
## <a name="syntax"></a>構文  
  
```syntaxsql
BINARY_CHECKSUM ( * | expression [ ,...n ] )   
```  
  
[!INCLUDE[synapse-analytics-od-unsupported-syntax](../../includes/synapse-analytics-od-unsupported-syntax.md)]

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
*\**  
計算がすべてのテーブルの列に対して行われることを指定します。 BINARY_CHECKSUM の計算では、比較できないデータ型の列は無視されます。 比較できないデータ型は次のとおりです。  
* **cursor**  
* **image**  
* **ntext**  
* **text**  
* **xml**  

および比較できない共通言語ランタイム (CLR) ユーザー定義型です。
  
*式 (expression)*  
任意のデータ型の[式](../../t-sql/language-elements/expressions-transact-sql.md)。 BINARY_CHECKSUM の計算では、比較できないデータ型の式は無視されます。

## <a name="return-types"></a>戻り値の型  
 **int**
  
## <a name="remarks"></a>解説  
テーブルの任意の行で計算される `BINARY_CHECKSUM(*)` では、行が後で変更されていない限り、同じ値が返されます。 `BINARY_CHECKSUM` はハッシュ関数のプロパティに対応します。2 つの式のリストに適用した場合、2 つのリストの対応する要素のデータ型が同じであり、等号 (=) 演算子による比較で等価であれば、同じ値が返されます。 この定義では、指定した型が NULL 値であるとすると、等しい値として比較されます。 式リストのいずれかの値を変更した場合は、その式のチェックサムも変わります。 ただし、この変更は保証されていないため、値が変更されたかどうかを検出するには、アプリケーションが変更を検出できないことを許容できる場合のみ、`BINARY_CHECKSUM` の使用をお勧めします。 それ以外の場合は、代わりに `HASHBYTES` の使用を検討してください。 MD5 ハッシュ アルゴリズムを指定した場合は、`HASHBYTES` から 2 つの異なる入力に対して同じ結果が返される可能性が `BINARY_CHECKSUM` よりもはるかに低くなります。
  
`BINARY_CHECKSUM` は式のリスト全体を処理でき、指定されたリストに対して同じ値を返します。 `BINARY_CHECKSUM` を 2 つの式のリストに適用した場合、2 つのリストの対応する要素が同じ型と同じバイト表現であれば、同じ値が返されます。 この定義では、指定した型の値が NULL であった場合、これらの値は同じバイト表現として扱われます。
  
`BINARY_CHECKSUM` と `CHECKSUM` は類似する関数です。 式のリストにあるチェックサム値を計算するために使用でき、式の順序は結果となる値に影響します。 `BINARY_CHECKSUM(*)` で使用される列の順序は、テーブルまたはビュー定義に指定された列の順序です。 この順序には、計算列が含まれます。
  
ロケールによっては、異なる表現の文字列が等しいとして比較される場合があります。このような場合、`BINARY_CHECKSUM` と `CHECKSUM` では文字列データ型に対して異なる値が返されます。 文字列データ型は次のとおりです。  

* **char**  
* **nchar**  
* **nvarchar**  
* **varchar**  

or  

* **sql_variant** (**sql_variant** の基本データ型が文字列データ型の場合)。  
  
たとえば、文字列 "McCavity" と "Mccavity" の `BINARY_CHECKSUM` 値は異なります。 これに対し、大文字小文字が区別されないサーバーの場合、`CHECKSUM` ではこれらの文字列に同じチェックサム値が返されます。 `CHECKSUM` 値と `BINARY_CHECKSUM` 値の比較は避ける必要があります。
 
`BINARY_CHECKSUM` では、任意の長さの **varbinary(max)** 型の文字と、最大 255 文字の **nvarchar(max)** 型がサポートされます。
  
## <a name="examples"></a>例  
この例では、`BINARY_CHECKSUM` を使用して、テーブル行の変更を検出します。
  
```sql
USE AdventureWorks2012;  
GO  
CREATE TABLE myTable (column1 INT, column2 VARCHAR(256));  
GO  
INSERT INTO myTable VALUES (1, 'test');  
GO  
SELECT BINARY_CHECKSUM(*) from myTable;  
GO  
UPDATE myTable set column2 = 'TEST';  
GO  
SELECT BINARY_CHECKSUM(*) from myTable;  
GO  
```  
  
## <a name="see-also"></a>関連項目
[集計関数 &#40;Transact-SQL&#41;](../../t-sql/functions/aggregate-functions-transact-sql.md)  
[CHECKSUM_AGG &#40;Transact-SQL&#41;](../../t-sql/functions/checksum-agg-transact-sql.md)  
[CHECKSUM &#40;Transact-SQL&#41;](../../t-sql/functions/checksum-transact-sql.md)  
[HASHBYTES &#40;Transact-SQL&#41;](../../t-sql/functions/hashbytes-transact-sql.md)  
  
  
