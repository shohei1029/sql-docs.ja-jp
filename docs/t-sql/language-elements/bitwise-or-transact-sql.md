---
description: '| (ビット演算子 OR) (Transact-SQL)'
title: '| (ビット演算子 OR) (Transact-SQL) | Microsoft Docs'
ms.custom: ''
ms.date: 01/10/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- '|'
- '|_TSQL'
- Bitwise OR
- bitwise
- OR
dev_langs:
- TSQL
helpviewer_keywords:
- OR operator
- bitwise OR (|)
- '| (bitwise OR operator)'
ms.assetid: 86a3b87f-9688-4eaf-a552-29f1b01d880a
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: c94e77d43aed650bd67389c1bab173167a02862f
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104749902"
---
# <a name="-bitwise-or-transact-sql"></a>| (ビット演算子 OR) (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  [!INCLUDE[tsql](../../includes/tsql-md.md)] ステートメントの中で、バイナリ式に変換された 2 つの指定される整数値に対して、ビットごとの論理和演算を実行します。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql   
expression | expression  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
 *式 (expression)*  
 整数データ型に分類されるデータ型、または **bit**、または **binary** または **varbinary** データ型の有効な [式](../../t-sql/language-elements/expressions-transact-sql.md)を指定します。 *式* は、ビットごとの演算に対して 2 進数として扱われます。  
  
> [!NOTE]  
>  ビットごとの演算では、1 つの *式* のみが **binary** または **varbinary** データ型のいずれかになります。  
  
## <a name="result-types"></a>戻り値の型  
 入力値が **int** の場合は **int**、入力値が **smallint** の場合は **smallint**、入力値が **tinyint** の場合は **tinyint** を返します。  
  
## <a name="remarks"></a>解説  
 ビットごとの | 演算子は、2 つの式の対応するビットを対象にビットごとの論理和演算を実行します。 入力式の中で現在処理の対象にあるビットについて、いずれかのビットまたは両方のビットが 1 の値を持つ場合、結果セットのビットは 1 に設定されます。入力式のビットが両方とも 1 の値を持たない場合、結果セットのビットは 0 に設定されます。  
  
 左側の式と右側の式が異なる整数型の場合 (たとえば、左側の *式* が **smallint** 型で、右側の *式* が **int** 型の場合)、小さいデータ型の引数が大きいデータ型の引数に変換されます。 この例では、**smallint**_expression_ は **int** に変換されます。  
  
## <a name="examples"></a>例  
 この例では、元の値を示すために **int** データ型を使用するテーブルを作成し、このテーブルに 1 行挿入します。  
  
```sql  
CREATE TABLE bitwise (  
  a_int_value INT NOT NULL,  
  b_int_value INT NOT NULL);  
GO  
INSERT bitwise VALUES (170, 75);  
GO  
```  
  
 このクエリは、**a_int_value** 列と **b_int_value** 列との間でビットごとの論理和演算を実行します。  
  
```sql  
SELECT a_int_value | b_int_value  
FROM bitwise;  
GO  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
-----------   
235           
  
(1 row(s) affected)  
```  
  
 170 (**a_int_value** または下記の `A`) をバイナリで表すと `0000 0000 1010 1010` です。 75 (**b_int_value** または下記の `B`) をバイナリで表すと `0000 0000 0100 1011` です。 この 2 つの値に対してビットごとの論理和演算を実行すると、結果はバイナリで `0000 0000 1110 1011`、10 進数では 235 になります。  
  
```  
(A | B)  
0000 0000 1010 1010  
0000 0000 0100 1011  
-------------------  
0000 0000 1110 1011  
```  
  
## <a name="see-also"></a>参照  
 [演算子 &#40;Transact-SQL&#41;](../../t-sql/language-elements/operators-transact-sql.md)   
 [ビットごとの演算子 &#40;Transact-SQL&#41;](../../t-sql/language-elements/bitwise-operators-transact-sql.md)   
 [&#124;= &#40;ビットごとの OR 代入&#41; &#40;Transact-SQL&#41;](../../t-sql/language-elements/bitwise-or-equals-transact-sql.md)   
 [複合演算子 &#40;Transact-SQL&#41;](../../t-sql/language-elements/compound-operators-transact-sql.md)  
  
  


