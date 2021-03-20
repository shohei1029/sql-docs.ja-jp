---
title: int、bigint、smallint、および tinyint (Transact-SQL) | Microsoft Docs
description: Int、bigint、smallint、tinyint データ型の Transact-SQL リファレンス。 これらのデータ型は整数データを表わすために使用されます。
ms.date: 09/08/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- bigint_TSQL
- smallint
- bigint
- smallint_TSQL
- tinyint_TSQL
- int_TSQL
- int
- tinyint
dev_langs:
- TSQL
helpviewer_keywords:
- exact numeric data [SQL Server]
- numeric data
- tinyint data type
- int data type
- smallint data type
ms.assetid: 9bda5b0b-2380-4931-a1c8-f362fdefa99b
author: MikeRayMSFT
ms.author: mikeray
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 4ccaf291bbb51fa23f6bd697929e5fe5e373630c
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104737052"
---
# <a name="int-bigint-smallint-and-tinyint-transact-sql"></a>int、bigint、smallint、および tinyint (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

整数データを使用する実数データ型です。 データベースの容量を節約するために、すべての可能な値を確実に含めることができる最小のデータ型を使用します。 たとえば、255 歳以上の人は誰もいきていないので、人の年齢には tinyint で十分でしょう。 しかし、255 年を超える建物はあり得るので、建物の経過年数には tinyint では不十分になります。
  
|データ型|Range|ストレージ|  
|---|---|---|
|**bigint**|-2^63 (-9,223,372,036,854,775,808) ～ 2^63-1 (9,223,372,036,854,775,807)|8 バイト|  
|**int**|-2^31 (-2,147,483,648) ～ 2^31-1 (2,147,483,647)|4 バイト|  
|**smallint**|-2^15 (-32,768) ～ 2^15-1 (32,767)|2 バイト|  
|**tinyint**|0 ～ 255|1 バイト|  
  
## <a name="remarks"></a>解説  
**Int** データ型は、主要な整数データ型が [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]です。 **Bigint** データ型が使用するための整数値でサポートされている範囲を超える可能性があるときに、 **int** データ型。
  
**bigint** 間に位置 **smallmoney** と **int** データ型の優先順位表でします。
  
関数を返します。 **bigint** 、パラメーター式が場合にのみ、 **bigint** データ型。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] その他の整数データ型を自動的に昇格しません (**tinyint**, 、**smallint**, 、および **int**) に **bigint** です。
  
> [!CAUTION]  
>  +、-、\*、/、または % の算術演算子を使用して、**int**、**smallint**、**tinyint**、または **bigint** の定数値の暗黙的または明示的変換を実行して **float**、**real**、**decimal**、または **numeric** データ型にした場合、データ型と式の精度を計算するときに [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] が適用する規則は、クエリが自動パラメーター化されているかどうかに応じて異なります。  
>   
>  したがって、クエリで同じ式を使用しても異なる結果が得られることがあります。 クエリが自動でないと、定数の値が最初に変換 **数値**, の有効桁数が十分な指定のデータ型に変換する前に、定数の値を保持するためにします。 定数の値 1 に変換するなど、 **numeric (1, 0)** , 、定数値 250 に変換し、 **numeric (3, 0)** です。  
>   
>  クエリが自動パラメーター、定数の値が常に変換されます **numeric (10, 0)** から最終的なデータ型に変換します。 / 演算子を使用すると、同様のクエリで結果の型の有効桁数が異なるだけでなく、結果の値も異なる場合があります。 たとえば、`SELECT CAST (1.0 / 7 AS float)` という式が含まれる自動パラメーター化されたクエリの結果値と、自動パラメーター化されない同じクエリの結果値は異なります。これは、自動パラメーター化されたクエリの場合、**numeric (10, 0)** データ型に収まるように結果が切り捨てられるためです。  
  
## <a name="converting-integer-data"></a>整数型データの変換
整数を暗黙的に文字データ型に変換するとき、整数が大きすぎて文字型フィールドに格納できない場合、ASCII 文字コード 42 のアスタリスク (*) が [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] によって入力されます。
  
2,147, 483,647 に変換されます。 よりも大きい整数の定数、 **decimal** データ型ではなく、 **bigint** データ型。 次の例は、しきい値を超過すると、結果のデータ型変更から、 **int** を **decimal** です。
  
```sql
SELECT 2147483647 / 2 AS Result1, 2147483649 / 2 AS Result2 ;  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```sql
Result1      Result2  
1073741823   1073741824.500000  
```  
  
## <a name="examples"></a>例  
次の例を使用してテーブルを作成、 **bigint**, 、**int**, 、**smallint**, 、および **tinyint** データ型。 値は各列に挿入され、SELECT ステートメントで返されます。
  
```sql
CREATE TABLE dbo.MyTable  
(  
  MyBigIntColumn BIGINT  
,MyIntColumn  INT
,MySmallIntColumn SMALLINT
,MyTinyIntColumn TINYINT
);  
  
GO  
  
INSERT INTO dbo.MyTable VALUES (9223372036854775807, 2147483647,32767,255);  
 GO  
SELECT MyBigIntColumn, MyIntColumn, MySmallIntColumn, MyTinyIntColumn  
FROM dbo.MyTable;  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
MyBigIntColumn       MyIntColumn MySmallIntColumn MyTinyIntColumn  
-------------------- ----------- ---------------- ---------------  
9223372036854775807  2147483647  32767            255  
  
(1 row(s) affected)  
```  
  
## <a name="see-also"></a>関連項目
[ALTER TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-table-transact-sql.md)  
[CAST および CONVERT &#40;Transact-SQL&#41;](../../t-sql/functions/cast-and-convert-transact-sql.md)  
[CREATE TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/create-table-transact-sql.md)  
[DECLARE @local_variable &#40;Transact-SQL&#41;](../../t-sql/language-elements/declare-local-variable-transact-sql.md)  
[SET @local_variable &#40;Transact-SQL&#41;](../../t-sql/language-elements/set-local-variable-transact-sql.md)  
[sys.types &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-types-transact-sql.md)
  
  
