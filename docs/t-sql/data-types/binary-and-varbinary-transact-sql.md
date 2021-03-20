---
description: binary と varbinary (Transact-SQL)
title: binary と varbinary (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 08/16/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- binary_TSQL
- varbinary_TSQL
- binary
- varbinary
dev_langs:
- TSQL
helpviewer_keywords:
- varbinary data type
- binary [SQL Server], about binary data type
ms.assetid: bcce65f9-10db-4b3e-bfaf-dfc06c6f820f
author: MikeRayMSFT
ms.author: mikeray
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 49c20ae59ec25668725b3fa763eb4f81304d0962
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104737112"
---
# <a name="binary-and-varbinary-transact-sql"></a>binary と varbinary (Transact-SQL)

[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

固定長または可変長のバイナリ データ型です。
  
## <a name="arguments"></a>引数

**binary** [ ( _n_ ) ]。長さ _n_ バイトの固定長のbinary データです。_n_ は 1 ～ 8,000 の値になります。 ストレージのサイズは _n_ バイトです。
  
**varbinary** [ ( _n_ | **max**) ] 可変長のbinary データ。 _n_ には 1 ～ 8,000 の値を指定できます。 **max** 記憶域の最大サイズが 2 であることを示します。 ^ 31-1 バイトです。 格納サイズは、入力したデータの実際の長さ + 2 バイトとなります。 入力するデータの長さは 0 バイトでもかまいません。 **varbinary** の ANSI SQL シノニム **binary** 可変です。
  
## <a name="remarks"></a>解説  
データ定義または変数宣言ステートメントで _n_ を指定しないと、既定の長さは 1 になります。 CAST 関数で _n_ を指定しないと、既定の長さは 30 になります。

| データ型 | 次の場合に使用 |
| --- | --- |
| **[バイナリ]** | 列データ エントリのサイズが一定である。|
| **varbinary** | 列データ エントリのサイズが大幅に変化する。|
| **varbinary(max)** | 列データ エントリのサイズが 8,000 バイトを超える。|


## <a name="converting-binary-and-varbinary-data"></a>binary 型データと varbinary 型データの変換
データが文字列データ型からデータ長の異なる **binary** データ型または **varbinary** データ型に変換される場合、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] によりデータの右側の桁が埋め込まれるか、切り捨てられます。 文字列データ型は次のとおりです。

* **char** 
* **varchar**
* **nchar**
* **nvarchar**
* **[バイナリ]**
* **varbinary**
* **text**
* **ntext**
* **image**

他のデータ型が変換される場合 **binary** または **varbinary**, 、データが埋め込まれるか、左側に切り捨てられます。 桁の埋め込みには 16 進数の 0 が使用されます。
  
データの **binary** データ型と **varbinary** データ型への変換は、データ間を移動するもっとも簡単な方法が **binary** データである場合に便利です。 ある時点で、値の型をサイズが十分に大きなバイナリ値に変換し、その後、元に戻すことがあります。 両方の変換が同じバージョンの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] で行われる場合、この変換では常に結果的に同じ値が生成されます。 値の 2 進表現は、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のバージョン間で異なる場合があります。
  
**int**、**smallint**、**tinyint** を **binary** または **varbinary** に変換できます。 **binary** 型の値を再度 integer 型の値に戻した場合、切り捨てが行われていると、この値は元の integer の値とは同じになりません。 たとえば、次の SELECT ステートメントは整数値 `123456` をバイナリ値 `0x0001e240` として格納することを示しています。
  
```sql
SELECT CAST( 123456 AS BINARY(4) );  
```  
  
ただし、次の `SELECT` ステートメントは、変換先の **binary** 型が小さすぎて値全体を保持できない場合は、左側の桁が暗黙的に切り捨てられ、同じ数値が `0xe240` として格納されることを示します。
  
```sql
SELECT CAST( 123456 AS BINARY(2) );  
```  
  
次のバッチは、暗黙的な切り捨てが算術演算に影響を与えることがあり、その場合でもエラーが生成されないことを示します。
  
```sql
DECLARE @BinaryVariable2 BINARY(2);  
  
SET @BinaryVariable2 = 123456;  
SET @BinaryVariable2 = @BinaryVariable2 + 1;  
  
SELECT CAST( @BinaryVariable2 AS INT);  
GO  
```  
  
最終的な値は `57921` ではなく `123457` になります。
  
> [!NOTE]  
>  すべてのデータ間の変換の入力と **binary** データ型は、バージョンの間で同じであるとは限りません [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]です。  
  
## <a name="see-also"></a>関連項目
[CAST および CONVERT &#40;Transact-SQL&#41;](../../t-sql/functions/cast-and-convert-transact-sql.md)  
[データ型の変換&#40;Transact-SQL&#41;](../../t-sql/data-types/data-type-conversion-database-engine.md)  
[データ型 &#40;Transact-SQL&#41;](../../t-sql/data-types/data-types-transact-sql.md)
  
  
