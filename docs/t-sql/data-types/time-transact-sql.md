---
description: time (Transact-SQL)
title: time (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 06/07/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- time_TSQL
- time
dev_langs:
- TSQL
helpviewer_keywords:
- time [SQL Server], data types
- time [SQL Server]
- date and time [SQL Server], time
- data types [SQL Server], date and time
- time data type [SQL Server]
ms.assetid: 30a6c681-8190-48e4-94d0-78182290a402
author: MikeRayMSFT
ms.author: mikeray
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: e51ebb8bb2a55551ca7a9be918e3d30f58a263a7
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104744612"
---
# <a name="time-transact-sql"></a>time (Transact-SQL)

[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  1 日の時刻を定義します。 時刻は 24 時間形式で、タイム ゾーンは認識されません。  
  
  > [!NOTE]  
  > Informatica の情報は、Informatica コネクタを使っている PDW ユーザーのために提供されます。 
  
## <a name="time-description"></a>time の説明  
  
|プロパティ|値|  
|--------------|-----------|  
|構文|**time** [ (*fractional second scale*) ]|  
|使用法|DECLARE \@MyTime **time(7)**<br /><br /> CREATE TABLE Table1 ( Column1 **time(7)** )|  
|*fractional seconds scale*|秒の小数点以下の有効桁数を指定します。<br /><br /> 0 ～ 7 の整数を指定できます。 Informatica の場合は、0 ～ 3 の整数を指定できます。<br /><br /> 既定の有効桁数は 7 (100 ナノ秒) です。|  
|既定の文字列リテラル形式<br /><br /> (下位クライアントに使用)|hh:mm:ss[.nnnnnnn] (Informatica の場合は nnn)<br /><br /> 詳細については、「[下位クライアントの下位互換性](#BackwardCompatibilityforDownlevelClients)」セクションを参照してください。|  
|範囲|00:00:00.0000000 ～ 23:59:59.9999999 (Informatica の場合は 00:00:00.000 ～ 23:59:59.999)|  
|要素範囲|hh は、0 ～ 23 の時を表す 2 桁の数字です。<br /><br /> mm は、0 ～ 59 の分を表す 2 桁の数字です。<br /><br /> ss は、0 ～ 59 の秒を表す 2 桁の数字です。<br /><br /> n\* は、秒の有効桁数を表す 0 ～ 7 桁の数字です (0 ～ 9999999)。 Informatica の場合は、n\* は 0 ～ 3 桁の数字です (0 ～ 999)。|  
|文字長|8 文字 (hh:mm:ss) 以上、16 文字 (hh:mm:ss.nnnnnnn) 以下。 Informatica の場合は、最大 12 文字 (hh:mm:ss.nnn) です。|  
|有効桁数、小数点以下桁数<br /><br /> (ユーザーは小数点以下桁数のみ指定)|次の表を参照してください。|  
|ストレージ サイズ|既定では 5 バイト固定 (秒部分の既定の有効桁数は 100ns) です。 Informatica の場合は、既定では 4 バイト固定 (秒部分の既定の有効桁数は 1 ミリ秒) です。|  
|精度|100 ナノ秒 (Informatica では 1 ミリ秒)|  
|既定値|00:00:00<br /><br /> この値は、**date** から **datetime2** または **datetimeoffset** への暗黙的な変換で、付加的な時刻要素として使用されます。|  
|ユーザー定義の 1 秒未満の秒の有効桁数|はい|  
|タイム ゾーン オフセットへの対応と保持|いいえ|  
|夏時間への対応|いいえ|  
  
|指定した小数点以下桁数|結果 (有効桁数、小数点以下桁数)|列長 (バイト)|1 秒未満の<br /><br /> seconds<br /><br /> 精度|  
|---------------------|---------------------------------|-----------------------------|------------------------------------------|  
|**time**|(16,7) (Informatica では (12,3))|5 (Informatica では 4)|7 (Informatica では 3)|  
|**time(0)**|(8,0)|3|0-2|  
|**time(1)**|(10,1)|3|0-2|  
|**time(2)**|(11,2)|3|0-2|  
|**time(3)**|(12,3)|4|3-4|  
|**time(4)**<br /><br /> Informatica ではサポートされていません。|(13,4)|4|3-4|  
|**time(5)**<br /><br /> Informatica ではサポートされていません。|(14,5)|5|5-7|  
|**time(6)**<br /><br /> Informatica ではサポートされていません。|(15,6)|5|5-7|  
|**time(7)**<br /><br /> Informatica ではサポートされていません。|(16,7)|5|5-7|  
  
## <a name="supported-string-literal-formats-for-time"></a>time でサポートされる文字列リテラル形式  
 次の表は、**time** データ型に使用できる有効な文字列リテラル形式を示しています。  
  
|SQL Server|説明|  
|----------------|-----------------|  
|hh:mm[:ss][:fractional seconds][AM][PM]<br /><br /> hh:mm[:ss][.fractional seconds][AM][PM]<br /><br /> hhAM[PM]<br /><br /> hh AM[PM]|AM を指定したかどうかに関係なく、時刻値 0 は午前 0 時を表します。 時刻値が 0 のときに PM を指定することはできません。<br /><br /> AM と PM のどちらも指定していない場合、01 から 11 までの時刻値は午前の時刻を表します。 これらの値は、AM を指定した場合も午前の時刻を表します。 PM を指定すると、午後の時刻を表します。<br /><br /> AM と PM のどちらも指定していない場合、時刻値 12 は正午を表します。 AM を指定すると、午前の時刻を表します。 PM を指定すると、正午を表します。 たとえば、12:01 という値は、12:01 PM では正午を 1 分過ぎた時刻、12:01 AM では午前 0 時を 1 分過ぎた時刻を表します。 12:01 AM と指定することは、00:01 または 00:01 AM と指定することと同じです。<br /><br /> 13 から 23 までの時刻値は、AM または PM を指定していなくても、午後の時刻を表します。 また、PM を指定した場合も午後の時刻を表します。 時刻値が 13 から 23 までの場合、AM を指定することはできません。<br /><br /> 24 という時刻値は無効です。 午前 0 時を表すには、12:00 AM または 00:00 を使います。<br /><br /> ミリ秒の前には、コロン (:) またはピリオド (.) を付けます。 コロンを付けると、その数値は 1000 分の 1 秒単位になります。 ピリオドを付けると、数字が 1 桁なら 10 分の 1 秒単位に、2 桁なら 100 分の 1 秒単位に、3 桁なら 1000 分の 1 秒単位になります。 たとえば、12:30:20:1 は 12 時 30 分を 20 と 1000 分の 1 秒過ぎた時刻を表し、12:30:20.1 は 12 時 30 分を 20 と 10 分の 1 秒過ぎた時刻を表します。|  
  
|ISO 8601|メモ|  
|--------------|-----------|  
|hh:mm:ss<br /><br /> hh:mm[:ss][.fractional seconds]|hh は、タイム ゾーン オフセットの時間数を表す、0 から 23 までの 2 桁の数字です。<br /><br /> mm は、タイム ゾーン オフセットの追加の分数を表す、0 から 59 までの 2 桁の数字です。|  
  
|ODBC|メモ|  
|----------|-----------|  
|{t 'hh:mm:ss[.fractional seconds]'}|ODBC API 固有です。|  
  
## <a name="compliance-with-ansi-and-iso-8601-standards"></a>ANSI および ISO 8601 への準拠  
 ISO 8601 (5.3.2 および 5.3) では、午前 0 時を表す時刻値 24 や、59 を超えるうるう秒の使用が定義されていますが、こうした用法は、下位互換性および既存の日付時刻型との一貫性を保つためサポート対象外となります。  
  
 下位クライアントに使用される既定の文字列リテラル形式は、hh:mm:ss[.nnnnnnn] として定義されている SQL 標準形式に準拠しています。 この形式は、1 秒未満の秒を除き、TIME に対する ISO 8601 の定義と似ています。  
  
##  <a name="backward-compatibility-for-down-level-clients"></a><a name="BackwardCompatibilityforDownlevelClients"></a> 下位クライアントの下位互換性  
 一部の下位レベル クライアントは、**time**、**date**、**datetime2**、**datetimeoffset** データ型をサポートしていません。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の上位インスタンスと下位クライアントの間のデータ型マッピングを次の表に示します。  
  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のデータ型|下位クライアントに渡される既定の文字列リテラル形式|下位 ODBC|下位 OLEDB|下位 JDBC|下位 SQLCLIENT|  
|-----------------------------------------|----------------------------------------------------------------|----------------------|-----------------------|----------------------|---------------------------|  
|**time**|hh:mm:ss[.nnnnnnn]|SQL_WVARCHAR または SQL_VARCHAR|DBTYPE_WSTR または DBTYPE_STR|Java.sql.String|String または SqString|  
|**date**|YYYY-MM-DD|SQL_WVARCHAR または SQL_VARCHAR|DBTYPE_WSTR または DBTYPE_STR|Java.sql.String|String または SqString|  
|**datetime2**|YYYY-MM-DD hh:mm:ss[.nnnnnnn]|SQL_WVARCHAR または SQL_VARCHAR|DBTYPE_WSTR または DBTYPE_STR|Java.sql.String|String または SqString|  
|**datetimeoffset**|YYYY-MM-DD hh:mm:ss[.nnnnnnn] [+&#124;-]hh:mm|SQL_WVARCHAR または SQL_VARCHAR|DBTYPE_WSTR または DBTYPE_STR|Java.sql.String|String または SqString|  
  
## <a name="converting-date-and-time-data"></a>日付と時刻のデータ型の変換  
 日付と時刻のデータ型に変換する場合、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] で日付または時刻と認識できない値はすべて拒否されます。 CAST 関数および CONVERT 関数で日付と時刻のデータを使用する方法については、「[CAST および CONVERT &#40;Transact-SQL&#41;](../../t-sql/functions/cast-and-convert-transact-sql.md)」をご覧ください。  
  
### <a name="converting-timen-data-type-to-other-date-and-time-types"></a>time(n) データ型から他の日付/時刻データ型への変換  
 ここでは、**time** データ型が他の日付/時刻データ型に変換される場合の処理について説明します。  
  
 **time(n)** への変換では、時、分、秒がコピーされます。 変換先の有効桁数が、ソースの有効桁数よりも小さい場合、秒の小数部は変換先の有効桁数に合わせて切り上げられます。 次の例は、`time(4)` 値を `time(3)` 値に変換した結果を示しています。  
  
```sql
DECLARE @timeFrom time(4) = '12:34:54.1237';  
DECLARE @timeTo time(3) = @timeFrom;  
  
SELECT @timeTo AS 'time(3)', @timeFrom AS 'time(4)';  
  
--Results  
--time(3)      time(4)  
-------------- -------------  
--12:34:54.124 12:34:54.1237  
--  
--(1 row(s) affected)  
```  
  
 **date** に変換すると、変換は失敗し、エラー メッセージ 206"オペランド型の不整合: date は time と互換性がありません" が発生します。  
  
 変換先が **datetime** の場合は、時、分、秒の値がコピーされ、日付部分が "1900-01-01" に設定されます。 **time(n)** 値の秒の小数部の有効桁数が 3 桁より大きい場合、**datetime** の結果は切り捨てられます。 次のコードは、`time(4)` 値を `datetime` 値に変換した結果を示しています。  
  
```sql
DECLARE @time time(4) = '12:15:04.1237';  
DECLARE @datetime datetime= @time;  
SELECT @time AS '@time', @datetime AS '@datetime';  
  
--Result  
--@time         @datetime  
--------------- -----------------------  
--12:15:04.1237 1900-01-01 12:15:04.123  
--  
--(1 row(s) affected)  
  
```  
  
 変換先が **smalldatetime** の場合は、日付が "1900-01-01" に設定され、時間と分の値が切り上げられます。 秒と秒の小数部は 0 に設定されます。 次のコードは、`time(4)` 値を `smalldatetime` 値に変換した結果を示しています。  
  
```sql
-- Shows rounding up of the minute value.  
DECLARE @time time(4) = '12:15:59.9999';   
DECLARE @smalldatetime smalldatetime= @time;    
SELECT @time AS '@time', @smalldatetime AS '@smalldatetime';   
  
--Result  
@time            @smalldatetime  
---------------- -----------------------  
12:15:59.9999    1900-01-01 12:16:00--  
--(1 row(s) affected)  
  
-- Shows rounding up of the hour value.  
DECLARE @time time(4) = '12:59:59.9999';   
DECLARE @smalldatetime smalldatetime= @time;    
  
SELECT @time AS '@time', @smalldatetime AS '@smalldatetime';  
@time            @smalldatetime  
---------------- -----------------------  
12:59:59.9999    1900-01-01 13:00:00  
  
(1 row(s) affected)  
  
```  
  
 変換先が **datetimeoffset(n)** の場合は、日付が "1900-01-01" に設定され、時刻がコピーされます。 タイム ゾーン オフセットが +00:00 に設定されます。 **time(n)** の値の秒の小数部の有効桁数が **datetimeoffset(n)** の値の有効桁数を超える場合、値はその桁数に合わせて切り上げられます。 次の例は、`time(4)` 値を `datetimeoffset(3)` 型に変換した結果を示しています。  
  
```sql
DECLARE @time time(4) = '12:15:04.1237';  
DECLARE @datetimeoffset datetimeoffset(3) = @time;  
  
SELECT @time AS '@time', @datetimeoffset AS '@datetimeoffset';  
  
--Result  
--@time         @datetimeoffset  
--------------- ------------------------------  
--12:15:04.1237 1900-01-01 12:15:04.124 +00:00  
--  
--(1 row(s) affected)  
  
```  
  
 変換先が **datetime2(n)** の場合は、日付が "1900-01-01"' に設定され、時刻部分がコピーされ、タイム ゾーン オフセットが 00:00 に設定されます。 **datetime2(n)** 値の秒の小数部の有効桁数が **time(n)** 値を超える場合、値はその桁数に合わせて切り上げられます。  次の例は、`time(4)` 値を `datetime2(2)` 値に変換した結果を示しています。  
  
```sql
DECLARE @time time(4) = '12:15:04.1237';  
DECLARE @datetime2 datetime2(3) = @time;  
  
SELECT @datetime2 AS '@datetime2', @time AS '@time';  
  
--Result  
--@datetime2              @time  
------------------------- -------------  
--1900-01-01 12:15:04.124 12:15:04.1237  
--  
--(1 row(s) affected)  
```  
  
### <a name="converting-string-literals-to-timen"></a>文字列リテラルの time(n) への変換  
 文字列リテラルから日付/時刻データ型への変換は、文字列のすべての部分が有効な形式になっている場合に可能になります。 それ以外の場合は実行時エラーが発生します。 日付/時刻データ型から文字列リテラルへの暗黙的な変換や、スタイルを指定しない明示的な変換では、現在のセッションの既定の形式が使用されます。 次の表では、文字列リテラルを **time** データ型に変換する規則を示します。  
  
|入力文字列リテラル|変換規則|  
|--------------------------|---------------------|  
|ODBC DATE|ODBC 文字列リテラルは、**datetime** データ型にマップされます。 ODBC 日付時刻リテラルから **time** 型への代入演算を行うと、**datetime** 型とこれらの型との間で、変換規則で定義されている暗黙的な変換が行われます。|  
|ODBC TIME|上記の ODBC 日付の規則を参照してください。|  
|ODBC DATETIME|上記の ODBC 日付の規則を参照してください。|  
|DATE のみ|既定値が指定されます。|  
|TIME のみ|単純変換|  
|タイム ゾーンのみ|既定値が指定されます。|  
|DATE + TIME|入力文字列の時刻部分が使用される|  
|DATE + TIMEZONE|許可されていません。|  
|TIME + TIMEZONE|入力文字列の時刻部分が使用される|  
|DATE + TIME + TIMEZONE|ローカル datetime の時刻部分が使用されます。|  
  
## <a name="examples"></a>例  
  
### <a name="a-comparing-date-and-time-data-types"></a>A. date と time のデータ型を比較する  
 次の例では、文字列をそれぞれの **date** および **time** データ型にキャストした結果を比較します。  
  
```sql
SELECT   
     CAST('2007-05-08 12:35:29. 1234567 +12:15' AS time(7)) AS 'time'   
    ,CAST('2007-05-08 12:35:29. 1234567 +12:15' AS date) AS 'date'   
    ,CAST('2007-05-08 12:35:29.123' AS smalldatetime) AS   
        'smalldatetime'   
    ,CAST('2007-05-08 12:35:29.123' AS datetime) AS 'datetime'   
    ,CAST('2007-05-08 12:35:29. 1234567 +12:15' AS datetime2(7)) AS   
        'datetime2'  
    ,CAST('2007-05-08 12:35:29.1234567 +12:15' AS datetimeoffset(7)) AS   
        'datetimeoffset';  
```  
  
|データ型|出力|  
|---------------|------------|  
|**time**|12:35:29. 1234567|  
|**date**|2007-05-08|  
|**smalldatetime**|2007-05-08 12:35:00|  
|**datetime**|2007-05-08 12:35:29.123|  
|**datetime2**|2007-05-08 12:35:29. 1234567|  
|**datetimeoffset**|2007-05-08 12:35:29.1234567 +12:15|  
  
###  <a name="b-inserting-valid-time-string-literals-into-a-time7-column"></a><a name="ExampleB"></a> B. 有効な時刻文字列リテラルを time(7) 列に挿入する  
 次の表では、**time(7)** データ型の列に挿入可能な各種の文字列リテラルと、その列に格納される値を示します。  
  
|文字列リテラル形式の種類|挿入する文字列リテラル|格納される time(7) 値|説明|  
|--------------------------------|-----------------------------|------------------------------------|-----------------|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]|'01:01:01:123AM'|01:01:01.1230000|秒の小数部の有効桁数の前にコロン (:) を付けた場合、小数点以下桁数が 3 桁を超えると、エラーが発生します。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]|'01:01:01.1234567 AM'|01:01:01.1234567|AM または PM を指定した場合、時刻は 24 時間形式で格納され、リテラルの AM または PM は格納されません。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]|'01:01:01.1234567 PM'|13:01:01.1234567|AM または PM を指定した場合、時刻は 24 時間形式で格納され、リテラルの AM または PM は格納されません。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]|'01:01:01.1234567PM'|13:01:01.1234567|AM または PM の前の空白は省略できます。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]|'01AM'|01:00:00.0000000|時だけを指定した場合、他のすべての値は 0 になります。|  
|SQL Server|'01 AM'|01:00:00.0000000|AM または PM の前の空白は省略できます。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]|'01:01:01'|01:01:01.0000000|秒の小数部の有効桁数を指定しなかった場合、データ型で定義されている各桁が 0 になります。|  
|ISO 8601|'01:01:01.1234567'|01:01:01.1234567|ISO 8601 に準拠するには、AM または PM ではなく、24 時間形式を使用します。|  
|ISO 8601|'01:01:01.1234567 +01:01'|01:01:01.1234567|入力ではオプションでタイム ゾーンの差 (TZD) を使えますが、保存はされません。|  
  
### <a name="c-inserting-time-string-literal-into-columns-of-each-date-and-time-date-type"></a>C. date 型と time 型のそれぞれの列に対して時間の文字列リテラルを挿入する  
 次の表の 1 列目は、2 列目に示した date データ型または time データ型のデータベース テーブル列に挿入する時間の文字列リテラルを示しています。 3 列目は、データベース テーブル列に格納される値を示しています。  
  
|挿入する文字列リテラル|列のデータ型|列に格納される値|説明|  
|-----------------------------|----------------------|------------------------------------|-----------------|  
|'12:12:12.1234567'|**time(7)**|12:12:12.1234567|秒の小数部の有効桁数が列に指定された値を超えている場合、エラーなしで文字列が切り詰められます。|  
|'2007-05-07'|**date**|NULL|time 値により、INSERT ステートメントが失敗します。|  
|'12:12:12'|**smalldatetime**|1900-01-01 12:12:00|1 秒未満の秒の有効桁数の値によって、INSERT ステートメントが失敗します。|  
|'12:12:12.123'|**datetime**|1900-01-01 12:12:12.123|秒の有効桁数が 3 桁を超えると、INSERT ステートメントが失敗します。|  
|'12:12:12.1234567'|**datetime2(7)**|1900-01-01 12:12:12.1234567|秒の小数部の有効桁数が列に指定された値を超えている場合、エラーなしで文字列が切り詰められます。|  
|'12:12:12.1234567'|**datetimeoffset(7)**|1900-01-01 12:12:12.1234567 +00:00|秒の小数部の有効桁数が列に指定された値を超えている場合、エラーなしで文字列が切り詰められます。|  
  
## <a name="see-also"></a>参照  
 [CAST および CONVERT &#40;Transact-SQL&#41;](../../t-sql/functions/cast-and-convert-transact-sql.md)  
  
  
