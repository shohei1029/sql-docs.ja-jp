---
title: DATEPART (Transact-SQL) | Microsoft Docs
description: DATEPART 関数の Transact-SQL リファレンス。 この関数では、指定日付の datepart に対応する整数が返されます。
ms.date: 07/29/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- DATEPART_TSQL
- DATEPART
dev_langs:
- TSQL
helpviewer_keywords:
- dates [SQL Server], functions
- date and time [SQL Server], DATEPART
- dateparts [SQL Server]
- functions [SQL Server], time
- functions [SQL Server], date and time
- time [SQL Server], functions
- dateparts [SQL Server], datepart
- comparing dates times [SQL Server]
- DATEPART function [SQL Server]
- dates [SQL Server], dateparts
ms.assetid: 15f1a5bc-4c0c-4c48-848d-8ec03473e6c1
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: d36754648df2a70ff858a514087811d98b8e5a77
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104752162"
---
# <a name="datepart-transact-sql"></a>DATEPART (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]


この関数は、指定された *date* の指定された *datepart* を表す整数を返します。
  
[!INCLUDE[tsql](../../includes/tsql-md.md)] の日付と時刻のあらゆるデータ型と関数に関する概要については、「[日付と時刻のデータ型および関数 &#40;Transact-SQL&#41;](../../t-sql/functions/date-and-time-data-types-and-functions-transact-sql.md)」を参照してください。
  
![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)
  
## <a name="syntax"></a>構文  
  
```syntaxsql
DATEPART ( datepart , date )  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
*datepart*  
`DATEPART` によって **整数** が返される *date* 引数の特定の部分。 この表には、有効な *datepart* 引数をすべて一覧表示しています。

> [!NOTE]
> `DATEPART` は、*datepart* 引数に関して、ユーザー定義変数に相当するものは受け入れられません。
  
|*datepart*|省略形|  
|---|---|
|**year**|**yy**、**yyyy**|  
|**quarter**|**qq**、**q**|  
|**month**|**mm**、**m**|  
|**dayofyear**|**dy**、**y**|  
|**day**|**dd**、**d**|  
|**week**|**wk**、**ww**|  
|**weekday**|**dw**|  
|**hour**|**hh**|  
|**minute**|**mi、n**|  
|**second**|**ss**、**s**|  
|**millisecond**|**ms**|  
|**microsecond**|**mcs**|  
|**nanosecond**|**ns**|  
|**tzoffset**|**tz**|  
|**iso_week**|**isowk**、**isoww**|  
  
*date*  
次のいずれかのデータ型に解決される式。 

+ **date**
+ **datetime**
+ **datetimeoffset**
+ **datetime2** 
+ **smalldatetime**
+ **time**

*date* の場合、`DATEPART` では、列式、式、文字列リテラル、ユーザー定義の変数が受け入れられます。 あいまいさの問題を排除するために、4 桁の西暦を使用してください。 2 桁の西暦については、「[two digit year cutoff サーバー構成オプションの構成](../../database-engine/configure-windows/configure-the-two-digit-year-cutoff-server-configuration-option.md)」を参照してください。
  
## <a name="return-type"></a>戻り値の型  
 **int**  
  
## <a name="return-value"></a>戻り値  
*-各日付構成要素とその省略形は、同じ値を返します。*
  
戻り値は、[SET LANGUAGE](../../t-sql/statements/set-language-transact-sql.md) と、ログインの [default language サーバー構成オプションの構成](../../database-engine/configure-windows/configure-the-default-language-server-configuration-option.md)で設定した言語環境に依存します。 *date* がなんらかの形式の文字列リテラルである場合、戻り値は [SET DATEFORMAT](../../t-sql/statements/set-dateformat-transact-sql.md) に依存します。 date が日付データ型や時刻データ型の列式である場合、SET DATEFORMAT によって戻り値が変わることはありません。
  
この表は、`SELECT DATEPART(datepart,'2007-10-30 12:15:32.1234567 +05:10')` ステートメントについて、すべての *datepart* 引数と、対応する戻り値を一覧にしたものです。 *date* 引数のデータ型は **datetimeoffset(7)** です。 **nanosecond** *datepart* の戻り値の末尾 2 桁は常に `00` であり、この値の小数点以下桁数は 9 桁です。

**.123456700**
  
|*datepart*|戻り値|  
|---|---|
|**year、yyyy、yy**|2007|  
|**quarter、qq、q**|4|  
|**month、mm、m**|10|  
|**dayofyear、dy、y**|303|  
|**day、dd、d**|30|  
|**week、wk、ww**|44|  
|**weekday、dw**|3|  
|**hour、hh**|12|  
|**minute、n**|15|  
|**second、ss、s**|32|  
|**millisecond、ms**|123|  
|**microsecond、mcs**|123456|  
|**nanosecond、ns**|123456700|  
|**tzoffset, tz**|310|  
|**iso_week, isowk, isoww**|44|  
  
## <a name="week-and-weekday-datepart-arguments"></a>week および weekday (datepart 引数)
**week** (**wk**、**ww**) または **weekday** (**dw**) *datepart* の場合、`DATEPART` の戻り値は、[SET DATEFIRST](../../t-sql/statements/set-datefirst-transact-sql.md) で設定された値によって変わります。
  
任意の年の 1 月 1 日が、**week** _datepart_ の開始番号と定義されます。 次に例を示します。

DATEPART (**wk**, 'Jan 1, *xxx* x') = 1

この *xxxx* は任意の年です。
  
この表は、**week** と **weekday** の *datepart* の戻り値を示しています。SET DATEFIRST 引数には、それぞれ '2007-04-21' が指定されています。 2007 年 1 月 1 日は月曜日です。 2007 年 4 月 21 日は土曜日です。 米国英語の場合、

`SET DATEFIRST 7 -- ( Sunday )`

が既定として使用されます。 DATEFIRST を設定した後、datepart テーブル値に次の推奨される SQL ステートメントを使用します。

`SELECT DATEPART(week, '2007-04-21 '), DATEPART(weekday, '2007-04-21 ')`
  
|SET DATEFIRST<br /><br /> 引数|week<br /><br /> 返される値|weekday<br /><br /> 返される値|  
|---|---|---|
|1|16|6|  
|2|17|5|  
|3|17|4|  
|4|17|3|  
|5|17|2|  
|6|17|1|  
|7|16|7|  
  
## <a name="year-month-and-day-datepart-arguments"></a>year、month、day (datepart 引数)  
DATEPART (**year**, *date*)、DATEPART (**month**, *date*)、DATEPART (**day**, *date*) で返される値は、それぞれ [YEAR](../../t-sql/functions/year-transact-sql.md)、[MONTH](../../t-sql/functions/month-transact-sql.md)、[DAY](../../t-sql/functions/day-transact-sql.md) の各関数で返される値と同じです。
  
## <a name="iso_week-datepart"></a>iso_week datepart  
ISO 8601 には、ISO 週日付方式 (週番号方式) が規定されています。 それぞれの週は、木曜日が出現する年と関連付けられます。 たとえば、2004 年の第 1 週 (2004W01) は、2003 年 12 月 29 日月曜日から 2004 年 1 月 4 日 日曜日です。 ヨーロッパの国/地域では、通常、このスタイルの付番方式が使用されます。 通常、ヨーロッパ以外の国/地域はこの方式を使用しません。

注: 1 年の最大週番号は 52 または 53 のいずれかになります。
  
異なる国や地域では、付番方式が ISO 標準に準拠していない可能性があります。 この表は 6 つの可能性を示しています。
  
|週の最初の曜日|年の最初の週の構成|2 回割り当てられる週の有無|利用されている地域|  
|---|---|---|---|
|土曜日|1 月 1 日<br /><br /> 最初の土曜日<br /><br /> 年の 1 から 7 日間|はい|United States|  
|月曜日|1 月 1 日<br /><br /> 最初の日曜日<br /><br /> 年の 1 から 7 日間|はい|欧州およびイギリス|  
|月曜日|1 月 4 日<br /><br /> 最初の木曜日<br /><br /> 年の 4 から 7 日間|いいえ|ISO 8601、ノルウェー、およびスウェーデン|  
|月曜日|1 月 7 日<br /><br /> 最初の月曜日<br /><br /> 年の 7 日間|いいえ||  
|水曜日|1 月 1 日<br /><br /> 最初の火曜日<br /><br /> 年の 1 から 7 日間|はい||  
|土曜日|1 月 1 日<br /><br /> 最初の金曜日<br /><br /> 年の 1 から 7 日間|はい||  
  
## <a name="tzoffset"></a>tzoffset  
`DATEPART` は、符号付きの分数として **tzoffset** (**tz**) 値を返します。 次のステートメントは、310 分のタイム ゾーン オフセットを返します。
  
```sql
SELECT DATEPART (tzoffset, '2007-05-10  00:00:01.1234567 +05:10');  
```  
`DATEPART` では、tzoffset 値が次のようにレンダリングされます。
- datetimeoffset と datetime2 の場合は、tzoffset は分単位で時刻オフセットを返します。datetime2 のオフセットは常に 0 分です。
- 暗黙的に **datetimeoffset** または **datetime2** に変換できるデータ型の場合、`DATEPART` は時刻オフセットを分単位で返します。 例外: 他の日付/時刻データ型。
- 他のすべての型のパラメーターは、エラーが発生します。
  
  
## <a name="smalldatetime-date-argument"></a>smalldatetime (date 引数)  
[smalldatetime](../../t-sql/data-types/smalldatetime-transact-sql.md) *date* 値の場合、`DATEPART` は秒を 00 として返します。
  
## <a name="default-returned-for-a-datepart-that-is-not-in-a-date-argument"></a>date 引数に存在しない datepart を指定した場合に返される既定値  
*date* 引数のデータ型に指定した *datepart* がない場合、リテラルが *date* に指定されている場合にのみ、`DATEPART` はその *datepart* の既定値を返します。
  
など、既定の年-月-日の任意の **日付** データ型は 1900年-01-01 です。 このステートメントでは、*datepart* 引数と *date* 引数にそれぞれ日付部分と時刻を表す値が指定されています。このステートメントは `1900, 1, 1, 1, 2` を返します。
  
```sql
SELECT DATEPART(year, '12:10:30.123')  
    ,DATEPART(month, '12:10:30.123')  
    ,DATEPART(day, '12:10:30.123')  
    ,DATEPART(dayofyear, '12:10:30.123')  
    ,DATEPART(weekday, '12:10:30.123');  
```  
  
*date* が変数またはテーブル列として指定され、その変数または列のデータ型に *datepart* が指定されていない場合、`DATEPART` はエラー 9810 を返します。 この例では、変数 *\@t* は **time** データ型です。 **time** データ型の日付部分の年度が無効なため、この例は失敗します。
  
```sql
DECLARE @t time = '12:10:30.123';   
SELECT DATEPART(year, @t);  
```  
  
## <a name="fractional-seconds"></a>秒の小数部
これらのステートメントは、`DATEPART` が秒の小数部を返すことを示しています。
  
```sql
SELECT DATEPART(millisecond, '00:00:01.1234567'); -- Returns 123  
SELECT DATEPART(microsecond, '00:00:01.1234567'); -- Returns 123456  
SELECT DATEPART(nanosecond,  '00:00:01.1234567'); -- Returns 123456700  
```  
  
## <a name="remarks"></a>解説  
`DATEPART` は、選択リスト、WHERE、HAVING、GROUP BY、および ORDER BY 句で使用できます。
  
[!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] では、DATEPART は文字列リテラルを **datetime2** 型として暗黙的にキャストします。 つまり、DATENAME では、日付が文字列として渡される場合、YDM 形式がサポートされません。 文字列を明示的にキャストする必要があります、 **datetime** または **smalldatetime** YDM 形式を使用する型。
  
## <a name="examples"></a>例  
この例では、基準年を返します。 この基準年は、日付の計算に役立ちます。 この例では、数値で日付を指定します。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] は、0 を 1900 年 1 月 1 日と解釈することに注意してください。
  
```sql
SELECT DATEPART(year, 0), DATEPART(month, 0), DATEPART(day, 0);  

-- Returns: 1900    1    1 
```  
  
この例は、日付 `12/20/1974` の日の部分を返します。
  
```sql
-- Uses AdventureWorks  
  
SELECT TOP(1) DATEPART (day,'12/20/1974') FROM dbo.DimCustomer;  

-- Returns: 20
```  
  
この例は、日付 `12/20/1974` の年の部分を返します。
  
```sql
-- Uses AdventureWorks  
  
SELECT TOP(1) DATEPART (year,'12/20/1974') FROM dbo.DimCustomer;  

-- Returns: 1974
```  
  
## <a name="see-also"></a>関連項目
[CAST および CONVERT &#40;Transact-SQL&#41;](../../t-sql/functions/cast-and-convert-transact-sql.md)
  
  
