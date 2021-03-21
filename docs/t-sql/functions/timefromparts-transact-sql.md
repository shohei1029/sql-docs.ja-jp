---
description: TIMEFROMPARTS (Transact-SQL)
title: TIMEFROMPARTS (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/04/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- TIMEFROMPARTS_TSQL
- TIMEFROMPARTS
dev_langs:
- TSQL
helpviewer_keywords:
- TIMEFROMPARTS function
ms.assetid: 786c65a1-2b3f-4e4b-82b6-4940d62f3801
author: julieMSFT
ms.author: jrasnick
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 889cdc554a27da1ba5db64fa3ee67bc1b51f2dad
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104748226"
---
# <a name="timefromparts-transact-sql"></a>TIMEFROMPARTS (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  指定された有効桁数を使用して、指定された時刻を表す **time** 値を返します。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql
TIMEFROMPARTS ( hour, minute, seconds, fractions, precision )  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
 *hour*  
 時間を指定する整数式。  
  
 *minute*  
 分を指定する整数式。  
  
 *seconds*  
 秒を指定する整数式。  
  
 *fractions*  
 小数部分を指定する整数式。  
  
 *有効桁数 (precision)*  
 返される **time** 値の有効桁数を指定する整数リテラル。  
  
## <a name="return-types"></a>戻り値の型  
 **time(** *precision* **)**  
  
## <a name="remarks"></a>解説  
 TIMEROMPARTS では、完全に初期化された time 値が返されます。 引数が無効な場合は、エラーが発生します。 パラメーターのいずれかが NULL の場合、NULL が返されます。 ただし場合、 *有効桁数* 引数が null の場合、エラーが発生します。  
  
 *分数* 引数によって異なります、 *有効桁数* 引数。 たとえば、*precision* が 7 の場合、小数部分はそれぞれ 100 ナノ秒を表します。*precision* が 3 の場合、小数部分はそれぞれ 1 ミリ秒を表します。 場合の値 *有効桁数* が 0 の場合、値の *分数* もする必要があります。 0 にするそれ以外の場合、エラーが発生します。  
  
 この関数は、[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以上のサーバーに対してリモート処理が可能です。 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] より前のバージョンのサーバーには、リモート処理は実行できません。  
  
## <a name="examples"></a>例  
  
### <a name="a-simple-example-without-fractions-of-a-second"></a>A. 秒の小数部を使用しない場合の簡単な例  
  
```sql
SELECT TIMEFROMPARTS ( 23, 59, 59, 0, 0 ) AS Result;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
Result  
--------------------  
23:59:59.0000000  
  
(1 row(s) affected)  
```  
  
### <a name="b-example-with-fractions-of-a-second"></a>B. 秒の小数部を使用する場合の例  
 以下の例は、*fractions* パラメーターと *precision* パラメーターの使用方法を示しています。  
  
1.  ときに *分数* 5 の値を持つと *有効桁数* しの値の 1 の値を持つ *分数* 5/10 秒を表します。  
  
2.  ときに *分数* 5 の値を持つと *有効桁数* しの値の 1 の値を持つ *分数* 5/10 秒を表します。  
  
3.  ときに *分数* 5 の値を持つと *有効桁数* しの値の 1 の値を持つ *分数* 5/10 秒を表します。  
  
```sql  
SELECT TIMEFROMPARTS ( 14, 23, 44, 5, 1 );  
SELECT TIMEFROMPARTS ( 14, 23, 44, 50, 2 );  
SELECT TIMEFROMPARTS ( 14, 23, 44, 500, 3 );  
GO  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
----------------  
14:23:44.5  
  
(1 row(s) affected)  
  
----------------  
14:23:44.50  
  
(1 row(s) affected)  
  
----------------  
14:23:44.500  
  
(1 row(s) affected)  
```  
  

