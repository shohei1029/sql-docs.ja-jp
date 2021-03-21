---
description: DATENAME (Transact-SQL)
title: DATENAME (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 07/29/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- DATENAME_TSQL
- DATENAME
dev_langs:
- TSQL
helpviewer_keywords:
- dates [SQL Server], functions
- DATENAME function [SQL Server
- functions [SQL Server], time
- functions [SQL Server], date and time
- dateparts [SQL Server], datename
- date and time [SQL Server], DATENAME
- comparing dates times [SQL Server]
- dates [SQL Server], dateparts
ms.assetid: 11855b56-c554-495d-aad4-ba446990153b
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: b7b246557e68cedc9995aca8fb7a5b943b4195ce
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104752122"
---
# <a name="datename-transact-sql"></a>DATENAME (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

この関数は、指定された *date* の指定された *datepart* を表す文字列を返します。

[!INCLUDE[tsql](../../includes/tsql-md.md)] の日付と時刻のあらゆるデータ型と関数に関する概要については、「[日付と時刻のデータ型および関数 &#40;Transact-SQL&#41;](../../t-sql/functions/date-and-time-data-types-and-functions-transact-sql.md)」を参照してください。
  
![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)
  
## <a name="syntax"></a>構文  
  
```syntaxsql
DATENAME ( datepart , date )  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
*datepart*  
`DATENAME` によって返される *date* 引数の特定の部分。 この表には、有効な *datepart* 引数をすべて一覧表示しています。

> [!NOTE]
> `DATENAME` は、*datepart* 引数に関して、ユーザー定義変数に相当するものは受け入れられません。
  
|*datepart*|省略形|  
|---|---|
|**year**|**yy、yyyy**|  
|**quarter**|**qq, q**|  
|**month**|**mm, m**|  
|**dayofyear**|**dy、y**|  
|**day**|**dd, d**|  
|**week**|**wk、ww**|  
|**weekday**|**dw、w**|  
|**hour**|**hh**|  
|**minute**|**mi、n**|  
|**second**|**ss, s**|  
|**millisecond**|**ms**|  
|**microsecond**|**mcs**|  
|**nanosecond**|**ns**|  
|**TZoffset**|**tz**|  
|**ISO_WEEK**|**ISOWK、ISOWW**|  
  
*date*  

次のいずれかのデータ型に解決できる式。 

+ **date**
+ **datetime**
+ **datetimeoffset**
+ **datetime2** 
+ **smalldatetime**
+ **time**

*date* の場合、`DATENAME` では、列式、式、文字列リテラル、ユーザー定義の変数が受け入れられます。 あいまいさの問題を排除するために、4 桁の西暦を使用してください。 2 桁の西暦については、「[two digit year cutoff サーバー構成オプションの構成](../../database-engine/configure-windows/configure-the-two-digit-year-cutoff-server-configuration-option.md)」を参照してください。
  
## <a name="return-type"></a>戻り値の型  
**nvarchar**
  
## <a name="return-value"></a>戻り値  
  
-   *-各日付構成要素とその省略形は、同じ値を返します。*  
  
戻り値は、[SET LANGUAGE](../../t-sql/statements/set-language-transact-sql.md) と、ログインの [default language サーバー構成オプションの構成](../../database-engine/configure-windows/configure-the-default-language-server-configuration-option.md)で設定した言語環境に依存します。 *date* がなんらかの形式の文字列リテラルである場合、戻り値は [SET DATEFORMAT](../../t-sql/statements/set-dateformat-transact-sql.md) に依存します。 date が日付データ型や時刻データ型の列式である場合、SET DATEFORMAT によって戻り値が変わることはありません。
  
*date* パラメーターに **date** データ型引数がある場合、戻り値は [SET DATEFIRST](../../t-sql/statements/set-datefirst-transact-sql.md) によって指定された設定に依存します。
  
## <a name="tzoffset-datepart-argument"></a>TZoffset (datepart 引数)  
*datepart* 引数が **TZoffset** (**tz**) で、*date* 引数にタイム ゾーン オフセットがない場合、`DATEADD` は 0 を返します。
  
## <a name="smalldatetime-date-argument"></a>smalldatetime (date 引数)  
*date* が [smalldatetime](../../t-sql/data-types/smalldatetime-transact-sql.md) のとき、`DATENAME` は秒として 00 を返します。
  
## <a name="default-returned-for-a-datepart-that-is-not-in-the-date-argument"></a>date 引数に存在しない datepart を指定した場合に返される既定値  
*date* 引数のデータ型に *datepart* が指定されていない場合、`DATENAME` は、*date* 引数にリテラルが含まれる場合にのみ、その *datepart* の既定値を返します。
  
など、既定の年-月-日の任意の **日付** データ型は 1900年-01-01 です。 このステートメントでは、*datepart* 引数と *date* 引数にそれぞれ日付部分と時刻を表す値が指定されています。`DATENAME` は `1900, January, 1, 1, Monday` を返します。
  
```sql
SELECT DATENAME(year, '12:10:30.123')  
    ,DATENAME(month, '12:10:30.123')  
    ,DATENAME(day, '12:10:30.123')  
    ,DATENAME(dayofyear, '12:10:30.123')  
    ,DATENAME(weekday, '12:10:30.123');  
```  
  
*date* が変数またはテーブル列として指定され、その変数または列のデータ型に *datepart* が指定されていない場合、`DATENAME` はエラー 9810 を返します。 この例では、変数 *\@t* は **time** データ型です。 **time** データ型の日付部分の年度が無効なため、この例は失敗します。
  
```sql
DECLARE @t time = '12:10:30.123';   
SELECT DATENAME(year, @t);  
```  
  
## <a name="remarks"></a>解説  

次の句で `DATENAME` を使用します。

+ GROUP BY
+ HAVING
+ ORDER BY
+ SELECT \<list>
+ WHERE
  
[!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] , 、DATENAME は、文字列リテラルを暗黙的にキャスト、 **datetime2** 型です。 つまり、`DATENAME` では、日付が文字列として渡される場合、YDM 形式がサポートされません。 文字列を明示的にキャストする必要があります、 **datetime** または **smalldatetime** YDM 形式を使用する型。
  
## <a name="examples"></a>例  
この例は、指定された日付の日付部分を返します。 SELECT ステートメントの `datepart` 引数の代わりにテーブルの *datepart* 値を使用します。
  
`SELECT DATENAME(datepart,'2007-10-30 12:15:32.1234567 +05:10');`
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
|*datepart*|戻り値|  
|---|---|
|**year、yyyy、yy**|2007|  
|**quarter、qq、q**|4|  
|**month、mm、m**|10 月|  
|**dayofyear、dy、y**|303|  
|**day、dd、d**|30|  
|**week、wk、ww**|44|  
|**weekday、dw**|Tuesday|  
|**hour、hh**|12|  
|**minute、n**|15|  
|**second、ss、s**|32|  
|**millisecond、ms**|123|  
|**microsecond、mcs**|123456|  
|**nanosecond、ns**|123456700|  
|**TZoffset、tz**|+05:10|  
|**ISO_WEEK、ISOWK、ISOWW**|44|  
  
[!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] および [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]

この例は、指定された日付の日付部分を返します。 SELECT ステートメントの `datepart` 引数の代わりにテーブルの *datepart* 値を使用します。
  
```sql
SELECT DATENAME(datepart,'2007-10-30 12:15:32.1234567 +05:10');  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
|*datepart*|戻り値|  
|---|---|
|**year、yyyy、yy**|2007|  
|**quarter、qq、q**|4|  
|**month、mm、m**|10 月|  
|**dayofyear、dy、y**|303|  
|**day、dd、d**|30|  
|**week、wk、ww**|44|  
|**weekday、dw**|Tuesday|  
|**hour、hh**|12|  
|**minute、n**|15|  
|**second、ss、s**|32|  
|**millisecond、ms**|123|  
|**microsecond、mcs**|123456|  
|**nanosecond、ns**|123456700|  
|**TZoffset、tz**|+05:10|  
|**ISO_WEEK、ISOWK、ISOWW**|44|  
  
## <a name="see-also"></a>関連項目
[CAST および CONVERT &#40;Transact-SQL&#41;](../../t-sql/functions/cast-and-convert-transact-sql.md)
  
  

