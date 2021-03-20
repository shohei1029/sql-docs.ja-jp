---
description: CHECKSUM (Transact-SQL)
title: CHECKSUM (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 07/24/2017
ms.prod: sql
ms.prod_service: synapse-analytics, database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- CHECKSUM_TSQL
- CHECKSUM
dev_langs:
- TSQL
helpviewer_keywords:
- hash indexes
- CHECKSUM function
- checksum values
ms.assetid: e26d3339-845c-49c2-9d89-243376874c13
author: cawrites
ms.author: chadam
monikerRange: =azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: e0fd5ebe213b0aba9a58e41bd5d685f35be596c1
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104749972"
---
# <a name="checksum-transact-sql"></a>CHECKSUM (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa.md)]

`CHECKSUM` 関数は、テーブルの行または式のリストで計算されたチェックサム値を返します。 `CHECKSUM` を使用して、ハッシュ インデックスを作成します。
  
![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)
  
## <a name="syntax"></a>構文  
  
```syntaxsql
CHECKSUM ( * | expression [ ,...n ] )  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]
[!INCLUDE[synapse-analytics-od-unsupported-syntax](../../includes/synapse-analytics-od-unsupported-syntax.md)]

## <a name="arguments"></a>引数
\*  
この引数は、チェックサムの計算がすべてのテーブル列をカバーしていることを指定します。 列のいずれかが比較できないデータ型である場合、`CHECKSUM` ではエラーが返されます。 比較できないデータ型は次のとおりです。

- **cursor**
- **image**
- **ntext**
- **text**
- **XML**

比較できないデータ型には、これらのデータ型のいずれかを基本データ型とする **sql_variant** もあります。
  
*式 (expression)*  
比較できないデータ型を除く、任意のデータ型の[式](../../t-sql/language-elements/expressions-transact-sql.md)を指定します。
  
## <a name="return-types"></a>戻り値の型
 **int**  
  
## <a name="remarks"></a>解説  
`CHECKSUM` では、引数リストに対してハッシュ値 (チェックサム) が計算されます。 このハッシュ値を使用して、ハッシュ インデックスを作成します。 `CHECKSUM` 関数に列の引数があり、計算された `CHECKSUM` 値を基にインデックスを作成する場合、結果はハッシュ インデックスになります。 このハッシュ インデックスは、列で等値検索を行うときに使用できます。
  
`CHECKSUM` はハッシュ関数のプロパティとなります。`CHECKSUM` を任意の 2 つの式のリストに適用した場合、その 2 つのリストに対応する要素のデータ型が同じで、等号 (=) 演算子により比較した場合にそれらの対応する要素が等しければ、同じ値が返されます。 `CHECKSUM` 関数の目的のために、指定した型の NULL 値は等しいものとして比較されるように定義されます。 式リストのいずれかの値を変更した場合は、そのリストのチェックサムも変わります。 ただし、これは保証されません。 そのため、値が変更されたかどうかを検出する際には、アプリケーションが変更を検出できないことを許容できる場合のみ、`CHECKSUM` の使用をお勧めします。 それ以外の場合は、代わりに `HASHBYTES` の使用を検討してください。 MD5 ハッシュ アルゴリズムを指定した場合は、`HASHBYTES` から 2 つの異なる入力に対して同じ結果が返される可能性が `CHECKSUM` よりもはるかに低くなります。
  
式の順序は、計算される `CHECKSUM` 値に影響します。 `CHECKSUM(*)` で使用される列の順序は、テーブルまたはビュー定義に指定された列の順序です。 これには、計算列が含まれます。
  
`CHECKSUM` 値は照合順序によって異なります。 異なる照合順序で保存された同じ値は別の `CHECKSUM` 値を返します。
  
`CHECKSUM ()` では、結果が一意になるとは限りません。

## <a name="examples"></a>例  
これらの例は、`CHECKSUM` を使用してハッシュ インデックスを作成する方法を示しています。
  
ハッシュ インデックスを作成するために、最初の例では、インデックスを作成するテーブルに計算されたチェックサム列を追加します。 次に、チェックサム列にインデックスを構築します。 
  
```sql
-- Create a checksum index.  

SET ARITHABORT ON;  
USE AdventureWorks2012;   
GO  
ALTER TABLE Production.Product  
ADD cs_Pname AS CHECKSUM(Name);  
GO  
CREATE INDEX Pname_index ON Production.Product (cs_Pname);  
GO  
```  
  
この例では、ハッシュ インデックスとしてチェックサム インデックスを使用することを示しています。 これにより、インデックスを作成する列が長い文字列の場合は、インデックス作成速度を向上させるのに役立ちます。 チェックサム インデックスは、等値検索に使用できます。
  
```sql
/*Use the index in a SELECT query. Add a second search   
condition to catch stray cases where checksums match,   
but the values are not the same.*/  

SELECT *   
FROM Production.Product  
WHERE CHECKSUM(N'Bearing Ball') = cs_Pname  
AND Name = N'Bearing Ball';  
GO  
```  
  
計算列にインデックスを作成するとチェックサム列が具体化され、`ProductName` 値を変更すると、それがどのような変更であってもチェックサム列に反映されます。 代わりに、インデックスを作成する列に直接インデックスを作成することができます。 ただし、長いキー値の場合は、通常のインデックスはチェックサム インデックスと同様に機能しない可能性があります。
  
## <a name="see-also"></a>関連項目
[CHECKSUM_AGG &#40;Transact-SQL&#41;](../../t-sql/functions/checksum-agg-transact-sql.md)  
[HASHBYTES &#40;Transact-SQL&#41;](../../t-sql/functions/hashbytes-transact-sql.md)  
[BINARY_CHECKSUM  &#40;Transact-SQL&#41;](../../t-sql/functions/binary-checksum-transact-sql.md)
  
  
