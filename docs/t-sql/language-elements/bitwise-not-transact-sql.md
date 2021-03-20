---
description: ~ (ビット演算子 NOT) (Transact-SQL)
title: ~ (ビット演算子 NOT) (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 01/10/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- ~_TSQL
- bitwise
- NOT
- "~"
- Bitwise NOT
dev_langs:
- TSQL
helpviewer_keywords:
- NOT keyword
- bitwise NOT (~)
- ~ (bitwise NOT)
ms.assetid: 02da8016-f6c0-41ae-8d59-33eaa02bfc95
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 786457031e0c004f089340566a736c7e956c2947
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104734581"
---
# <a name="-bitwise-not-transact-sql"></a>~ (ビット演算子 NOT) (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  整数値に対してビットごとの論理否定演算を実行します。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql  
~ expression  
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
  
 入力値が **tinyint** の場合は **tinyint** です。  
  
 入力値が **bit** の場合は **bit** です。  
  
## <a name="remarks"></a>解説  
 ビットごとの **~** 演算子は、各ビットを対象に *式* に対してビットごとの論理否定演算を順番に実行します。 *式* の値が 0 の場合は、結果セット内のビットが 1 に設定されます。0 以外の場合は、結果セット内のビットがクリアされて値が 0 になります。 つまり、1 は 0 に、0 は 1 に変更されます。  
  
> [!IMPORTANT]  
>  どのような種類のビットごとの演算を実行する場合でも、演算の中で使用される式の記憶域の長さが重要になります。 値を格納するときは、式のデータ型と同じバイト数を使用するようにしてください。 たとえば、10 進数の 5 を **tinyint** 型、**smallint** 型、または **int** 型として格納すると、値はそれぞれ異なるバイト数で格納されます。つまり、**tinyint** の場合は 1 バイト、**smallint** の場合は 2 バイト、**int** の場合は 4 バイトをそれぞれ使用してデータが格納されます。 このため、**int** 型の 10 進数でビットごとの演算を実行すると、バイナリで直接実行する場合や、16 進数に変換して実行する場合とは異なる結果が得られる可能性があります。この傾向は、**~** (ビットごとの NOT) 演算子において特に顕著です。 また、長さが短い方の変数に対してビットごとの NOT 演算が実行される場合があります。 この場合、短い長さが、長いデータ型の変数に変換されると、上位 8 ビットのビットが予想値に設定されない場合があります。 短いデータ型を長いデータ型に変換し、その結果に対して NOT 演算を実行するようにしてください。  
  
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
  
 このクエリでは、`a_int_value` 列と `b_int_value` 列に対して、それぞれビットごとの NOT を実行します。  
  
```sql  
SELECT ~ a_int_value, ~ b_int_value  
FROM bitwise;  
```  
  
 結果セットは次のようになります。  
  
```  
--- ---   
-171  -76   
  
(1 row(s) affected)  
```  
  
 170 (`a_int_value` または `A`) をバイナリで表すと、`0000 0000 1010 1010` になります。 この値に対してビットごとの NOT 演算を実行すると、結果はバイナリで `1111 1111 0101 0101`、10 進数では -171 になります。 75 をバイナリで表すと `0000 0000 0100 1011` になります。 ビットごとの NOT 演算を実行すると、結果は `1111 1111 1011 0100`、10 進数では -76 になります。  
  
```  
 (~A)     
         0000 0000 1010 1010  
         -------------------  
         1111 1111 0101 0101  
(~B)     
         0000 0000 0100 1011  
         -------------------  
         1111 1111 1011 0100  
```  
  
 
## <a name="see-also"></a>参照  
 [式 &#40;Transact-SQL&#41;](../../t-sql/language-elements/expressions-transact-sql.md)   
 [演算子 &#40;Transact-SQL&#41;](../../t-sql/language-elements/operators-transact-sql.md)   
 [ビットごとの演算子 &#40;Transact-SQL&#41;](../../t-sql/language-elements/bitwise-operators-transact-sql.md)  
  
  


