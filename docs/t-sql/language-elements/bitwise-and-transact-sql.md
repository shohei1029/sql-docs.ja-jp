---
description: '&amp; (ビットごとの AND) (Transact-SQL)'
title: '&amp; (ビットごとの AND) (Transact-SQL) | Microsoft Docs'
ms.custom: ''
ms.date: 01/10/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- bitwise
- '&'
- '&_TSQL'
dev_langs:
- TSQL
helpviewer_keywords:
- AND, bitwise AND
- '& (bitwise AND)'
- bitwise AND (&)
ms.assetid: 20275755-4fa7-47b1-a9be-ac85606d63b0
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 9fced4f3698e07950c20ef1fa16d0e70e4040348
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104733702"
---
# <a name="amp-bitwise-and-transact-sql"></a>&amp; (ビットごとの AND) (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  2 つの整数値の間でビットごとの論理積演算を実行します。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql
expression & expression  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
 *式 (expression)*  
 整数データ型に分類されるデータ型のいずれか、または **bit**、または **binary** または **varbinary** データ型の有効な [式](../../t-sql/language-elements/expressions-transact-sql.md)を指定します。 *式* は、ビットごとの演算に対して 2 進数として扱われます。  
  
> [!NOTE]  
>  ビットごとの演算では、1 つの *式* のみが **binary** または **varbinary** データ型のいずれかになります。  
  
## <a name="result-types"></a>戻り値の型  
 入力値が **int** の場合は **int** です。  
  
 入力値が **smallint** の場合は **smallint** です。  
  
 入力値が **tinyint** または **bit** の場合は **tinyint** です。  
  
## <a name="remarks"></a>解説  
 ビットごとの **&** 演算子は、2 つの式の対応するビットを対象にビットごとの論理積演算を実行します。 入力式の中で現在処理の対象にあるビットについて、両方のビットが 1 という値を持つ場合だけ、結果セットのビットは 1 に設定されます。それ以外の場合、結果は 0 に設定されます。  
  
 左側の式と右側の式が異なる整数型の場合 (たとえば、左側の *式* が **smallint** 型で、右側の *式* が **int** 型の場合)、小さいデータ型の引数が大きいデータ型の引数に変換されます。 この場合、**smallint** "_式_" は **int** に変換されます。  
  
## <a name="examples"></a>例  
 次の例では、**int** 型で値を格納するテーブルを作成し、1 行に 2 つの値を挿入します。  
  
```sql
CREATE TABLE bitwise (   
  a_int_value INT NOT NULL,  
  b_int_value INT NOT NULL);  
GO  
INSERT bitwise VALUES (170, 75);  
GO  
```  
  
 このクエリは、`a_int_value` 列と `b_int_value` 列の間でビットごとの AND を実行します。  
  
```sql  
SELECT a_int_value & b_int_value  
FROM bitwise;  
GO  
```  
  
 結果セットは次のようになります。  
  
```  
-----------   
10            
  
(1 row(s) affected)  
```  
  
 170 (`a_int_value` または `A`) をバイナリで表すと、`0000 0000 1010 1010` になります。 75 (`b_int_value` または `B`) をバイナリで表すと、`0000 0000 0100 1011` になります。 この 2 つの値に対してビットごとの論理積演算を実行すると、結果はバイナリで `0000 0000 0000 1010`、10 進数では 10 になります。  
  
```  
(A & B)  
0000 0000 1010 1010  
0000 0000 0100 1011  
-------------------  
0000 0000 0000 1010  
```  
  
  
## <a name="see-also"></a>参照  
 [式 &#40;Transact-SQL&#41;](../../t-sql/language-elements/expressions-transact-sql.md)   
 [演算子 &#40;Transact-SQL&#41;](../../t-sql/language-elements/operators-transact-sql.md)   
 [ビットごとの演算子 &#40;Transact-SQL&#41;](../../t-sql/language-elements/bitwise-operators-transact-sql.md)   
 [&= &#40;ビットごとの AND 代入&#41; &#40;Transact-SQL&#41;](../../t-sql/language-elements/bitwise-and-equals-transact-sql.md)   
 [複合演算子 &#40;Transact-SQL&#41;](../../t-sql/language-elements/compound-operators-transact-sql.md)  
  
  


