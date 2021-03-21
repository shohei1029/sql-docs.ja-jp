---
description: DATETIME2FROMPARTS (Transact-SQL)
title: DATETIME2FROMPARTS (Transact-SQL)
ms.custom: ''
ms.date: 01/29/2021
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- DATETIME2FROMPARTS_TSQL
- DATETIME2FROMPARTS
dev_langs:
- TSQL
helpviewer_keywords:
- DATETIME2FROMPARTS function
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: bd8c87b7a5d4aa309654365337a8e83424579ef0
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104752182"
---
# <a name="datetime2fromparts-transact-sql"></a>DATETIME2FROMPARTS (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

この関数は、指定された日付引数と時刻引数に対して **datetime2** 値を返します。 返された値には、有効桁数引数で有効桁数が指定されます。
  
![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)
  
## <a name="syntax"></a>構文  
  
```syntaxsql
DATETIME2FROMPARTS ( year, month, day, hour, minute, seconds, fractions, precision )  
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
  
*有効桁数 (precision)*  
`DATETIME2FROMPARTS` が返す **datetime2** 値の有効桁数を指定する整数式。
  
## <a name="return-types"></a>戻り値の型
**datetime2(** *precision* **)**
  
## <a name="remarks"></a>注釈  
`DATETIME2FROMPARTS` は、完全に初期化された **datetime2** 値を返します。 `DATETIME2FROMPARTS` は、必須引数に 1 つでも無効な値が含まれている場合、エラーを生成します。 `DATETIME2FROMPARTS` は、必須引数に 1 つでも NULL 値が含まれている場合、NULL を返します。 ただし、*precision* 引数に NULL 値が含まれる場合、`DATETIME2FROMPARTS` はエラーを生成します。

*分数* 引数によって異なります、 *有効桁数* 引数。 たとえば、*precision* の値が 7 の場合、小数部分はそれぞれ 100 ナノ秒を表します。*precision* の値が 3 の場合、小数部分はそれぞれ 1 ミリ秒を表します。 *precision* 値がゼロの場合、*fractions* の値もゼロでなければなりません。それ以外の場合、`DATETIME2FROMPARTS` はエラーを生成します。
  
この関数は、リモート処理は実行することのできる [!INCLUDE[sssql11-md](../../includes/sssql11-md.md)] サーバー上とします。 [!INCLUDE[sssql11-md](../../includes/sssql11-md.md)] 下のバージョンのサーバーには、リモート処理されません。  
  
## <a name="examples"></a>例  
  
### <a name="a-an-example-without-fractions-of-a-second"></a>A. 秒の小数部を使用しない場合の例  
  
```sql
SELECT DATETIME2FROMPARTS ( 2010, 12, 31, 23, 59, 59, 0, 0 ) AS Result;  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
Result  
---------------------------  
2010-12-31 23:59:59.0000000  
  
(1 row(s) affected)  
```  
  
### <a name="b-example-with-fractions-of-a-second"></a>B. 秒の小数部を使用する場合の例  
この例は、*fractions* パラメーターと *precision* パラメーターの使用方法を示しています。
  
1.  *fractions* の値が 5 のとき、*precision* の値が 1 であれば、*fractions* の値は 1 秒の 5/10 になります。  
  
2.  *fractions* の値が 50 のとき、*precision* の値が 2 であれば、*fractions* の値は 1 秒の 50/100 になります。  
  
3.  *fractions* の値が 500 で、*precision* の値が 3 の場合、*fractions* の値は 1 秒の 500/1000 を表します。  
  
```sql
SELECT DATETIME2FROMPARTS ( 2011, 8, 15, 14, 23, 44, 5, 1 );  
SELECT DATETIME2FROMPARTS ( 2011, 8, 15, 14, 23, 44, 50, 2 );  
SELECT DATETIME2FROMPARTS ( 2011, 8, 15, 14, 23, 44, 500, 3 );  
GO  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
----------------------  
2011-08-15 14:23:44.5  
  
(1 row(s) affected)  
  
----------------------  
2011-08-15 14:23:44.50  
  
(1 row(s) affected)  
  
----------------------  
2011-08-15 14:23:44.500  
  
(1 row(s) affected)  
```  
  
## <a name="see-also"></a>関連項目
[datetime2 &#40;Transact-SQL&#41;](../../t-sql/data-types/datetime2-transact-sql.md)
  
  

