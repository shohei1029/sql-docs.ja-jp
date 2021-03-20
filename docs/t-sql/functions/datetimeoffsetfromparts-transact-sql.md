---
description: DATETIMEOFFSETFROMPARTS (Transact-SQL)
title: DATETIMEOFFSETFROMPARTS (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 07/29/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- DATETIMEOFFSETFROMPARTS_TSQL
- DATETIMEOFFSETFROMPARTS
dev_langs:
- TSQL
helpviewer_keywords:
- DATETIMEOFFSETFROMPARTS function
ms.assetid: 463da1f4-b4b6-45a3-9a95-ea1f99575542
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 60cf63db7bf946c3154fcbd926fdfa7eca8cafb2
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104752112"
---
# <a name="datetimeoffsetfromparts-transact-sql"></a>DATETIMEOFFSETFROMPARTS (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

指定された日付引数と時刻引数に対する **datetimeoffset** 値を返します。 返される値は、精度引数によって指定された有効桁数と、オフセット引数によって指定されたオフセットを持ちます。  
  
![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)
  
## <a name="syntax"></a>構文  
  
```syntaxsql
DATETIMEOFFSETFROMPARTS ( year, month, day, hour, minute, seconds, fractions, hour_offset, minute_offset, precision )  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数

*year*  
年を指定する整数式。  
  
*month*  
月を指定する整数式。  
  
*day*  
日を指定する整数式。  
  
*hour*  
時間を指定する整数式。  
  
*minute*  
分を指定する整数式。  
  
*seconds*  
秒を指定する整数式。  
  
*fractions*  
秒の小数部を指定する整数式。  
  
*hour_offset*  
タイム ゾーン オフセットの時間部分を指定する整数式。  
  
*minute_offset*  
タイム ゾーン オフセットの分部分を指定する整数式。  
  
*有効桁数 (precision)*  
`DATETIMEOFFSETFROMPARTS` が返す **datetimeoffset** 値の有効桁数を指定する整数リテラル値です。  
  
## <a name="return-types"></a>戻り値の型
**datetimeoffset(** *precision* **)**  
  
## <a name="remarks"></a>注釈  

`DATETIMEOFFSETFROMPARTS` は、完全に初期化された **datetimeoffset** データ型を返します。 オフセット引数は、タイム ゾーンのオフセットを表します。 オフセット引数を省略すると、`DATETIMEOFFSETFROMPARTS` はタイム ゾーン オフセットが `00:00` (つまり、タイム ゾーン オフセットなし) であるものと想定します。 オフセット引数が指定される場合、`DATETIMEOFFSETFROMPARTS` は両方の引数の値が指定され、両方とも正の値または両方とも負の値であるものと想定します。 *minute_offset* の値が設定されていて、*hour_offset* の値が設定されていない場合、`DATETIMEOFFSETFROMPARTS` はエラーになります。 他の引数が無効な値の場合、`DATETIMEOFFSETFROMPARTS` はエラーになります。 必須引数の少なくとも 1 つが `NULL` 値である場合、`DATETIMEOFFSETFROMPARTS` は `NULL` を返します。 ただし、*precision* 引数が `NULL` 値の場合は、`DATETIMEOFFSETFROMPARTS` はエラーを生成します。  
  
*分数* 引数によって異なります、 有効桁数 引数。 たとえば、precision の値が 7 の場合、小数部分はそれぞれ 100 ナノ秒を表します。precision の値が 3 の場合、小数部分はそれぞれ 1 ミリ秒を表します。 precision 値がゼロの場合、fractions の値もゼロでなければなりません。それ以外の場合、`DATETIMEOFFSETFROMPARTS` はエラーを生成します。  
  
この関数は、リモート処理は実行することのできる [!INCLUDE[sssql11-md](../../includes/sssql11-md.md)] サーバー上とします。 [!INCLUDE[sssql11-md](../../includes/sssql11-md.md)] 下のバージョンのサーバーには、リモート処理されません。  
  
## <a name="examples"></a>例  
  
### <a name="a-an-example-without-fractions-of-a-second"></a>A. 秒の小数部を使用しない場合の例  
  
```sql
SELECT DATETIMEOFFSETFROMPARTS ( 2010, 12, 31, 14, 23, 23, 0, 12, 0, 7 ) AS Result;  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
Result  
----------------------------------
2010-12-31 14:23:23.0000000 +12:00  
  
(1 row(s) affected)  
```  
  
### <a name="b-example-with-fractions-of-a-second"></a>B. 秒の小数部を使用する場合の例  

この例は、*fractions* パラメーターと *precision* パラメーターの使用方法を示しています。  

1. *fractions* の値が 5 のとき、*precision* の値が 1 であれば、*fractions* の値は 1 秒の 5/10 になります。  

2. *fractions* の値が 50 のとき、*precision* の値が 2 であれば、*fractions* の値は 1 秒の 50/100 になります。  

3. *fractions* の値が 500 で、*precision* の値が 3 の場合、*fractions* の値は 1 秒の 500/1000 を表します。  
  
```sql
SELECT DATETIMEOFFSETFROMPARTS ( 2011, 8, 15, 14, 30, 00, 5, 12, 30, 1 );  
SELECT DATETIMEOFFSETFROMPARTS ( 2011, 8, 15, 14, 30, 00, 50, 12, 30, 2 );  
SELECT DATETIMEOFFSETFROMPARTS ( 2011, 8, 15, 14, 30, 00, 500, 12, 30, 3 );  
GO  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
----------------------------------  
2011-08-15 14:30:00.5 +12:30  
  
(1 row(s) affected)  
  
----------------------------------  
2011-08-15 14:30:00.50 +12:30  
  
(1 row(s) affected)  
  
----------------------------------  
2011-08-15 14:30:00.500 +12:30  
  
(1 row(s) affected)  
```  
  
## <a name="see-also"></a>関連項目
[datetimeoffset &#40;Transact-SQL&#41;](../../t-sql/data-types/datetimeoffset-transact-sql.md)  
[AT TIME ZONE &#40;Transact-SQL&#41;](../../t-sql/queries/at-time-zone-transact-sql.md)
  
  


