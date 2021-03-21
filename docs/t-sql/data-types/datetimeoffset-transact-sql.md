---
description: datetimeoffset (Transact-SQL)
title: datetimeoffset (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 07/23/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- datetimeoffset_TSQL
- datetimeoffset
dev_langs:
- TSQL
helpviewer_keywords:
- time [SQL Server], data types
- date and time [SQL Server], datetimeoffset
- datetimeoffset data type [SQL Server]
- dates [SQL Server], data types
- data types [SQL Server], date and time
- time zones [SQL Server]
ms.assetid: a0455b71-ca25-476e-a7a8-0770f1860bb7
author: MikeRayMSFT
ms.author: mikeray
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: fc89d27800319ecfb64f82501554a23d692adb75
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104747702"
---
# <a name="datetimeoffset-transact-sql"></a>datetimeoffset (Transact-SQL)

[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

タイム ゾーンを認識する 24 時間形式の時刻と組み合わせた日付を定義します。

## <a name="datetimeoffset-description"></a>datetimeoffset の説明
  
|プロパティ|値|  
|---|---|
|構文|**datetimeoffset** [ (*fractional seconds precision*) ]|  
|使用法|DECLARE \@MyDatetimeoffset **datetimeoffset(7)**<br /><br /> CREATE TABLE Table1 ( Column1 **datetimeoffset(7)** )|  
|既定の文字列リテラル形式 (下位クライアント用)|YYYY-MM-DD hh:mm:ss[.nnnnnnn] [{+&#124;-}hh:mm]<br /><br /> 詳細については、後述の「下位クライアントの下位互換性」セクションを参照してください。|  
|期間|0001-01-01 ～ 31.12.99<br /><br /> 1 月 1 日 1 CE ～12 月 31 日 9999 CE|  
|時間の範囲|00:00:00 から 23:59:59.9999999|  
|タイム ゾーンのオフセット範囲|-14:00 ～ +14:00|  
|要素範囲|YYYY は、0001 ～ 9999 の年を表す 4 桁の数字です。<br /><br /> MM は、指定された年の 01 ～ 12 の月を表す 2 桁の数字です。<br /><br /> DD は、指定された月の (月に応じて) 01 ～ 31 の日を表す 2 桁の数字です。<br /><br /> hh は、00 ～ 23 の時を表す 2 桁の数字です。<br /><br /> mm は、分を表す 00 から 59 の 2 桁の数字です。<br /><br /> ss は、秒を表す 00 から 59 の 2 桁の数字です。<br /><br /> n* は、秒の有効桁数を表す 0 ～ 7 桁の数字です (0 ～ 9999999)。<br /><br /> hh は、-14 から +14 までの 2 桁の数字です。 <br /><br /> mm は、00 ～ 59 の 2 桁の数字です。|  
|文字長|26 文字 (YYYY-MM-DD hh:mm:ss {+&#124;-}hh:mm) 以上、34 文字 (YYYY-MM-DD hh:mm:ss.nnnnnnn {+&#124;-}hh:mm) 以下|  
|有効桁数、小数点以下桁数|次の表を参照してください。|  
|ストレージ サイズ|既定では 10 バイト固定 (秒部分の既定の有効桁数は 100 ns) です。|  
|精度|100 ナノ秒|  
|既定値|1900-01-01 00:00:00 00:00|  
|Calendar|グレゴリオ暦|  
|ユーザー定義の 1 秒未満の秒の有効桁数|はい|  
|タイム ゾーン オフセットへの対応と保持|はい|  
|夏時間への対応|いいえ|  
  
|指定した小数点以下桁数|結果 (有効桁数、小数点以下桁数)|列長 (バイト)|秒の小数部の有効桁数|  
|---|---|---|---|
|**datetimeoffset**|(34,7)|10|7|  
|**datetimeoffset(0)**|(26,0)|8|0-2|  
|**datetimeoffset(1)**|(28,1)|8|0-2|  
|**datetimeoffset(2)**|(29,2)|8|0-2|  
|**datetimeoffset(3)**|(30,3)|9|3-4|  
|**datetimeoffset(4)**|(31,4)|9|3-4|  
|**datetimeoffset(5)**|(32,5)|10|5-7|  
|**datetimeoffset(6)**|(33,6)|10|5-7|  
|**datetimeoffset(7)**|(34,7)|10|5-7|  
  
## <a name="supported-string-literal-formats-for-datetimeoffset"></a>datetimeoffset でサポートされる文字列リテラル形式
次の表は、**datetimeoffset** でサポートされている ISO 8601 文字列リテラル形式を一覧にしたものです。 **datetimeoffset** の日付部分と時刻部分に使用できるアルファベット、数値、区切りなし、時刻の各形式については、「[date &#40;Transact-SQL&#41;](../../t-sql/data-types/date-transact-sql.md)」および「[time &#40;Transact-SQL&#41;](../../t-sql/data-types/time-transact-sql.md)」をご覧ください。
  
|ISO 8601|説明|  
|---|---|
|YYYY-MM-DDThh:mm:ss[.nnnnnnn][{+&#124;-}hh:mm]|この 2 つの形式は、セッションのロケール設定である SET LANGUAGE および SET DATEFORMAT の影響を受けません。 **datetimeoffset** 部分と **datetime** 部分の間にスペースを入れることはできません。|  
|YYYY-MM-DDThh:mm:ss[.nnnnnnn]Z (UTC)|ISO 定義に基づくこの形式は、**datetime** 部分を協定世界時 (UTC) で指定する必要があることを示します。 たとえば、1999-12-12 12:30:30.12345 -07:00 は 1999-12-12 19:30:30.12345Z と表す必要があります。|  
  
## <a name="time-zone-offset"></a>タイム ゾーン オフセット
タイム ゾーン オフセットは、**time** または **datetime** 値の UTC を基準とした相対値を指定します。 タイム ゾーン オフセットは、[+|-] hh:mm として表すことができます。
-   hh は、タイム ゾーン オフセットの時間数を表す 00 から 14 までの 2 桁の数字です。  
-   mm は、タイム ゾーン オフセットの付加的な分数を表す 00 から 59 までの 2 桁の数字です。  
-   タイム ゾーン オフセットでは、\+ (正負号) または - (負符号) を必ず指定します。 ローカル時刻を取得する際、UTC 時刻を基準としてタイム ゾーン オフセットを加算するか、減算するかを示します。 タイム ゾーン オフセットの有効範囲は -14:00 ～ +14:00 までです。  
  
タイム ゾーン オフセットの範囲は、XSD スキーマ定義の W3C XML 標準に準拠しており、SQL 2003 標準の定義 (12:59 ～ +14:00) とは若干異なります。
  
*fractional seconds precision* は、1 秒未満の桁数を指定するオプションのパラメーターです。 この値は、0 から 7 (100 ナノ秒) の整数で指定できます。 *fractional seconds precision* の既定値は 100ns (1 秒未満の桁数は 7 桁) です。
  
データベースに格納されたデータは、サーバーで UTC として処理、比較、並べ替え、およびインデックス化されます。 タイム ゾーン オフセットは、データベースに保持され、必要に応じて取得できます。
  
**datetime** が DST 期間に該当する場合、指定したタイム ゾーン オフセットは、夏時間 (DST) が認識されて調整されるものと見なされます。
  
**datetime** 型では、挿入、更新、算術演算、変換、代入などの操作時に、UTC とローカルの両方の **datetime** 値 (保持されたタイム ゾーン オフセットと変換後のタイム ゾーン オフセット) が検証されます。 保持されたタイム ゾーン オフセットまたは変換後のタイム ゾーン オフセットに対して、無効な UTC またはローカル **datetime** 値が検出された場合は、無効な値であることを示すエラーが生成されます。 たとえば、9999-12-31 10:10:00 は UTC では有効ですが、タイム ゾーン オフセットを +13:50 に設定したローカル時間ではオーバーフローします。
  
ターゲットのタイム ゾーンの対応する **datetimeoffset** 値への date の変換については、「[AT TIME ZONE &#40;Transact-SQL&#41;](../../t-sql/queries/at-time-zone-transact-sql.md)」をご覧ください。
  
## <a name="ansi-and-iso-8601-compliance"></a>ANSI および ISO 8601 への準拠  
[date](../../t-sql/data-types/date-transact-sql.md) および [time](../../t-sql/data-types/time-transact-sql.md) のトピックの ANSI と ISO 8601 への準拠に関するセクションは、**datetimeoffset** にも当てはまります。
  
## <a name="backward-compatibility-for-down-level-clients"></a>下位クライアントの下位互換性
一部の下位レベル クライアントは、**time**、**date**、**datetime2**、**datetimeoffset** データ型をサポートしていません。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の上位インスタンスと下位クライアントの間のデータ型マッピングを次の表に示します。
  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のデータ型|下位クライアントに渡される既定の文字列リテラル形式|下位 ODBC|下位 OLEDB|下位 JDBC|下位 SQLCLIENT|  
|---|---|---|---|---|---|
|**time**|hh:mm:ss[.nnnnnnn]|SQL_WVARCHAR または SQL_VARCHAR|DBTYPE_WSTR または DBTYPE_STR|Java.sql.String|String または SqString|  
|**date**|YYYY-MM-DD|SQL_WVARCHAR または SQL_VARCHAR|DBTYPE_WSTR または DBTYPE_STR|Java.sql.String|String または SqString|  
|**datetime2**|YYYY-MM-DD hh:mm:ss[.nnnnnnn]|SQL_WVARCHAR または SQL_VARCHAR|DBTYPE_WSTR または DBTYPE_STR|Java.sql.String|String または SqString|  
|**datetimeoffset**|YYYY-MM-DD hh:mm:ss[.nnnnnnn] [+&#124;-]hh:mm|SQL_WVARCHAR または SQL_VARCHAR|DBTYPE_WSTR または DBTYPE_STR|Java.sql.String|String または SqString|  
  
## <a name="converting-date-and-time-data"></a>日付と時刻のデータ型の変換
日付と時刻のデータ型に変換する場合、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] で日付または時刻と認識できない値はすべて拒否されます。 CAST 関数および CONVERT 関数で日付と時刻のデータを使用する方法については、「[CAST および CONVERT &#40;Transact-SQL&#41;](../../t-sql/functions/cast-and-convert-transact-sql.md)」をご覧ください。
  
### <a name="converting-datetimeoffset-data-type-to-other-date-and-time-types"></a>datetimeoffset データ型の他の日付/時刻データ型への変換
ここでは、**datetimeoffset** データ型が他の日付/時刻データ型に変換される場合の処理について説明します。
  
**date** への変換では、年、月、日がコピーされます。 次のコードは、`datetimeoffset(4)` 値を `date` 値に変換した結果を示しています。  
  
```sql
DECLARE @datetimeoffset datetimeoffset(4) = '12-10-25 12:32:10 +01:00';  
DECLARE @date date= @datetimeoffset;  
  
SELECT @datetimeoffset AS '@datetimeoffset ', @date AS 'date';  
  
--Result  
--@datetimeoffset                date  
-------------------------------- ----------  
--2025-12-10 12:32:10.0000 +01:0 2025-12-10  
--  
--(1 row(s) affected)  
  
```  
  
**time(n)** への変換の場合は、時、分、秒、および秒の小数部がコピーされます。 タイム ゾーンの値は切り捨てられます。 **datetimeoffset(n)** の値の有効桁数が **time(n)** の値の有効桁数を超える場合、値が切り上げられます。 次のコードは、`datetimeoffset(4)` 値を `time(3)` 値に変換した結果を示しています。
  
```sql
DECLARE @datetimeoffset datetimeoffset(4) = '12-10-25 12:32:10.1237 +01:0';  
DECLARE @time time(3) = @datetimeoffset;  
  
SELECT @datetimeoffset AS '@datetimeoffset ', @time AS 'time';  
  
--Result  
--@datetimeoffset                time  
-------------------------------- ------------  
-- 2025-12-10 12:32:10.1237 +01:00    12:32:10.124  
  
--  
--(1 row(s) affected)  
  
```  
  
**datetime** に変換するときは、日付と時刻の値がコピーされ、タイム ゾーンが切り捨てられます。 **datetimeoffset(n)** 値の小数部の有効桁数が 3 桁を超える場合、値は切り捨てられます。 次のコードは、`datetimeoffset(4)` 値を `datetime` 値に変換した結果を示しています。
  
```sql
DECLARE @datetimeoffset datetimeoffset(4) = '12-10-25 12:32:10.1237 +01:0';  
DECLARE @datetime datetime = @datetimeoffset;  
  
SELECT @datetimeoffset AS '@datetimeoffset ', @datetime AS 'datetime';  
  
--Result  
--@datetimeoffset                datetime  
-------------------------------- -----------------------  
--2025-12-10 12:32:10.1237 +01:0 2025-12-10 12:32:10.123  
--  
--(1 row(s) affected)  
```  
  
**smalldatetime** への変換の場合は、日付と時間がコピーされます。 分は秒の値に応じて切り上げられ、秒は 0 に設定されます。 次のコードは、`datetimeoffset(3)` 値を `smalldatetime` 値に変換した結果を示しています。  
  
```sql
DECLARE @datetimeoffset datetimeoffset(3) = '1912-10-25 12:24:32 +10:0';  
DECLARE @smalldatetime smalldatetime = @datetimeoffset;  
  
SELECT @datetimeoffset AS '@datetimeoffset', @smalldatetime AS '@smalldatetime';  
  
--Result  
--@datetimeoffset                @smalldatetime  
-------------------------------- -----------------------  
--1912-10-25 12:24:32.000 +10:00 1912-10-25 12:25:00  
--  
--(1 row(s) affected)  
```  
  
**datetime2(n)** に変換する場合は、日付と時刻が **datetime2** の値にコピーされて、タイム ゾーンは切り捨てられます。 **datetime2(n)** 値の有効桁数が **datetimeoffset(n)** 値の有効桁数を超える場合、秒の小数部はその桁数に合わせて切り捨てられます。 次のコードは、`datetimeoffset(4)` 値を `datetime2(3)` 値に変換した結果を示しています。
  
```sql
DECLARE @datetimeoffset datetimeoffset(4) = '1912-10-25 12:24:32.1277 +10:0';  
DECLARE @datetime2 datetime2(3)=@datetimeoffset;  
  
SELECT @datetimeoffset AS '@datetimeoffset', @datetime2 AS '@datetime2';  
  
--Result  
@datetimeoffset                    @datetime2  
---------------------------------- ----------------------  
1912-10-25 12:24:32.1277 +10:00    1912-10-25 12:24:32.12  
  
--(1 row(s) affected)  
```  
  
### <a name="converting-string-literals-to-datetimeoffset"></a>文字列リテラルの datetimeoffset への変換
文字列リテラルから日付/時刻データ型への変換は、文字列のすべての部分が有効な形式になっている場合に可能になります。 それ以外の場合は実行時エラーが発生します。 日付/時刻データ型から文字列リテラルへの暗黙的な変換や、スタイルを指定しない明示的な変換では、現在のセッションの既定の形式が使用されます。 次の表では、文字列リテラルを **datetimeoffset** データ型に変換する規則を示します。
  
|入力文字列リテラル|**datetimeoffset(n)**|  
|---|---|
|ODBC DATE|ODBC 文字列リテラルは、**datetime** データ型にマップされます。 ODBC DATETIME リテラルから **datetimeoffset** 型への代入演算を行うと、**datetime** 型とこれらの型との間で、変換規則で定義されている暗黙的な変換が行われます。|  
|ODBC TIME|上記の ODBC 日付の規則を参照。|  
|ODBC DATETIME|上記の ODBC 日付の規則を参照。|  
|DATE のみ|時刻部分は既定値の 00:00:00 に設定される TIMEZONE は既定値の +00:00 に設定される。|  
|TIME のみ|DATE 部分は既定で 1900-1-1 に設定されます。 TIMEZONE は既定値の +00:00 に設定される。|  
|タイム ゾーンのみ|既定値が設定される|  
|DATE + TIME|TIMEZONE は既定値の +00:00 に設定される。|  
|DATE + TIMEZONE|使用できません|  
|TIME + TIMEZONE|DATE 部分は既定で 1900-1-1 に設定されます。|  
|DATE + TIME + TIMEZONE|単純変換|  
  
## <a name="examples"></a>例  
次の例では、文字列をそれぞれの **date** および **time** データ型にキャストした結果を比較します。
  
```sql
SELECT   
     CAST('2007-05-08 12:35:29. 1234567 +12:15' AS time(7)) AS 'time'   
    ,CAST('2007-05-08 12:35:29. 1234567 +12:15' AS date) AS 'date'   
    ,CAST('2007-05-08 12:35:29.123' AS smalldatetime) AS   
        'smalldatetime'   
    ,CAST('2007-05-08 12:35:29.123' AS datetime) AS 'datetime'   
    ,CAST('2007-05-08 12:35:29.1234567+12:15' AS datetime2(7)) AS   
        'datetime2'  
    ,CAST('2007-05-08 12:35:29.1234567 +12:15' AS datetimeoffset(7)) AS   
        'datetimeoffset'  
    ,CAST('2007-05-08 12:35:29.1234567+12:15' AS datetimeoffset(7)) AS  
        'datetimeoffset IS08601';  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
|データ型|出力|  
|---|---|
|**Time**|12:35:29. 1234567|  
|**日付**|2007-05-08|  
|**Smalldatetime**|2007-05-08 12:35:00|  
|**Datetime**|2007-05-08 12:35:29.123|  
|**datetime2**|2007-05-08 12:35:29. 1234567|  
|**Datetimeoffset**|2007-05-08 12:35:29.1234567 +12:15|  
  
## <a name="see-also"></a>関連項目
[CAST および CONVERT &#40;Transact-SQL&#41;](../../t-sql/functions/cast-and-convert-transact-sql.md)  
[AT TIME ZONE &#40;Transact-SQL&#41;](../../t-sql/queries/at-time-zone-transact-sql.md)
  
  
