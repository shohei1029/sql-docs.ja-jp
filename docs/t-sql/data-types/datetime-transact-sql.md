---
description: datetime (Transact-SQL)
title: datetime (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 07/23/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- datetime_TSQL
- datetime
dev_langs:
- TSQL
helpviewer_keywords:
- time [SQL Server], data types
- date and time [SQL Server], datetime
- dates [SQL Server], data types
- datetime data type [SQL Server]
- data types [SQL Server], date and time
ms.assetid: 9bd1cc5b-227b-4032-95d6-7581ddcc9924
author: MikeRayMSFT
ms.author: mikeray
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 0b1d5deb8e44107b921b16627728bff0caa5741a
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104735642"
---
# <a name="datetime-transact-sql"></a>datetime (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

24 時間形式の時刻 (1 秒未満の秒を含む) と組み合わせた日付を定義します。
  
> [!NOTE]  
>  使用して、 **time**, 、**date**, 、**datetime2** と **datetimeoffset** 新しい作業のデータ型。 これらの型は、SQL 標準に準拠しています。 これらの型は、移植性がより高いです。 **time**、**datetime2**、**datetimeoffset** は秒の有効桁数が増えています。 **datetimeoffset** グローバルに配置されるアプリケーション向けにタイム ゾーンのサポートを提供します。  
  
## <a name="datetime-description"></a>datetime の説明  
  
|プロパティ|値|  
|---|---|
|構文|**datetime**|  
|使用法|DECLARE \@MyDatetime **datetime**<br /><br /> CREATE TABLE Table1 ( Column1 **datetime** )|  
|既定の文字列リテラル形式<br /><br /> (下位クライアントに使用)|適用なし|  
|期間|1753 年 1 月 1 日～ 9999 年 12 月 31 日|  
|時間の範囲|00:00:00 から 23:59:59.997|  
|タイム ゾーンのオフセット範囲|なし|  
|要素範囲|YYYY は、年を表す 1753 から 9999 の 4 桁の数字です。<br /><br /> MM は、指定された年の 01 ～ 12 の月を表す 2 桁の数字です。<br /><br /> DD は、指定された月の (月に応じて) 01 ～ 31 の日を表す 2 桁の数字です。<br /><br /> hh は、00 ～ 23 の時を表す 2 桁の数字です。<br /><br /> mm は、分を表す 00 から 59 の 2 桁の数字です。<br /><br /> ss は、秒を表す 00 から 59 の 2 桁の数字です。<br /><br /> n* は、秒の有効桁数を表す 3 桁の数字です (0 から 999)。|  
|文字長|19 文字以上、23 文字以下|  
|ストレージ サイズ|8 バイト|  
|精度|値は、.000、.003、または .007 秒単位に丸められます。|  
|既定値|1900-01-01 00:00:00|  
|Calendar|グレゴリオ暦 (完全な年の範囲は含まれません。)|  
|ユーザー定義の 1 秒未満の秒の有効桁数|いいえ|  
|タイム ゾーン オフセットへの対応と保持|いいえ|  
|夏時間への対応|いいえ|  
  
## <a name="supported-string-literal-formats-for-datetime"></a>datetime でサポートされる文字列リテラル形式  
次の表は、**datetime** でサポートされている文字列リテラル形式を一覧にしたものです。 ODBC を除き、**datetime** の文字列リテラルは単一引用符 (') で囲んで指定します (例: 'string_literaL')。 **us_english** 以外の環境では、文字列リテラルを N'string_literaL' 形式で指定する必要があります。
  
|数値|説明|  
|---|---|
|日付形式 :<br /><br /> [0]4/15/[19]96 -- (mdy)<br /><br /> [0]4-15-[19]96 -- (mdy)<br /><br /> [0]4.15.[19]96 -- (mdy)<br /><br /> [0]4/[19]96/15 -- (myd)<br /><br /> 15/[0]4/[19]96 -- (dmy)<br /><br /> 15/[19]96/[0]4 -- (dym)<br /><br /> [19]96/15/[0]4 -- (ydm)<br /><br /> [19]96/[0]4/15 -- (ymd)<br /><br /> 時刻の形式:<br /><br /> 14:30<br /><br /> 14:30[:20:999]<br /><br /> 14:30[:20.9]<br /><br /> 4am<br /><br /> 4 PM|月名を数値で表して日付データを指定できます。 たとえば、5/20/97 は 1997 年 5 月 20 日を表します。 数値データの形式を使用する場合、文字列の年、月、日は、スラッシュ (/)、ハイフン (-)、またはピリオド (.) で区切って指定します。 この文字列は、次の形式に従う必要があります。<br /><br /> *number separator number separator number [time] [time]*<br /><br /> <br /><br /> 言語設定が **us_english** の場合、既定の日付順序は月、日、年 (mdy) です。 日付順序は、[SET DATEFORMAT](../../t-sql/statements/set-dateformat-transact-sql.md) ステートメントを使用して変更できます。<br /><br /> SET DATEFORMAT の設定は、日付の値がどのように解釈されるかを決定します。 順序が設定と一致しない場合、値は日付として解釈されません。 日付が順序どおりでない場合、範囲外または不正な値に誤って解釈される可能性があります。 たとえば、12/10/08 は、DATEFORMAT の設定によっては、6 種類の日付形式のうちのいずれにも解釈できます。 4 桁の年は年と解釈されます。|  
  
|アルファベット|説明|  
|---|---|
|Apr[il] [15][,] 1996<br /><br /> Apr[il] 15[,] [19]96<br /><br /> Apr[il] 1996 [15]<br /><br /> [15] Apr[il][,] 1996<br /><br /> 15 Apr[il][,][19]96<br /><br /> 15 [19]96 apr[il]<br /><br /> [15] 1996 apr[il]<br /><br /> 1996 APR[IL] [15]<br /><br /> 1996 [15] APR[IL]|月の正式名を使って日付データを指定できます。 たとえば、現在の言語で指定されている April やその省略形である Apr を使用できます。コンマは省略でき、大文字と小文字の違いは無視されます。<br /><br /> アルファベット日付形式の使用に関するガイドラインを次に示します。<br /><br /> 1) 日時データを単一引用符 (') で囲みます。 English 以外の言語では、N' を使用します。<br /><br /> 2) 角かっこで囲まれた文字は省略可能です。<br /><br /> 3) 西暦の最後の 2 桁のみを指定した場合、その値が [two digit year cutoff サーバー構成オプションの構成](../../database-engine/configure-windows/configure-the-two-digit-year-cutoff-server-configuration-option.md)構成オプションの上限値の最後の 2 桁より小さければ、指定した西暦は終了年と同じ世紀になります。 値がこの構成オプションより大きいか、同じである場合、指定した西暦は基準になる年の世紀より前の世紀になります。 たとえば、**two digit year cutoff** が 2050 (既定値) である場合、25 は 2025 年と解釈され、50 は 1950 年と解釈されます。 こうしたあいまいさを排除するため、4 桁の西暦を使用してください。<br /><br /> 4) 日を省略したときは、その月の 1 日が指定されます。<br /><br /> <br /><br /> 月をアルファベット形式で指定したときは、SET DATEFORMAT のセッション設定は適用されません。|  
  
|ISO 8601|説明|  
|---|---|
|YYYY-MM-DDThh:mm:ss[.mmm]<br /><br /> YYYYMMDD[ hh:mm:ss[.mmm]]|次に例を示します。<br /><br /> 1) 2004-05-23T14:25:10<br /><br /> 2) 2004-05-23T14:25:10.487<br /><br /> <br /><br /> ISO 8601 形式を使用する場合、各要素はこの形式で指定する必要があります。これには、形式にある **T**、コロン (:)、およびピリオド (.) を含みます。<br /><br /> 秒の構成要素を示す角かっこは省略できます。 時の構成要素は 24 時間形式で指定します。<br /><br /> T は、**datetime** 値の時刻の部分の先頭を示します。<br /><br /> ISO 8601 形式を使用する利点は、これが明確な仕様を持つ国際標準であるという点です。 また、この形式は、SET DATEFORMAT や [SET LANGUAGE](../../t-sql/statements/set-language-transact-sql.md) の設定の影響を受けません。|  
  
|区切りなし|説明|  
|---|---|
|YYYYMMDD hh:mm:ss[.mmm]||  
  
|ODBC|説明|  
|---|---|
|{ ts '1998-05-02 01:23:56.123' }<br /><br /> { d '1990-10-02' }<br /><br /> { t '13:33:41' }|ODBC API では、タイムスタンプ データと呼び出す、ODBC において日付時刻値を表すエスケープ シーケンスを定義します。 この ODBC タイムスタンプ形式は、[!INCLUDE[msCoName](../../includes/msconame-md.md)] OLE DB Provider for [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] でサポートされる OLE DB 言語定義 (DBGUID-SQL) でもサポートされます。 ADO、OLE DB、および ODBC ベースの API を使用しているアプリケーションでは、この ODBC タイムスタンプ形式を使用して日付/時刻を表すことができます。<br /><br /> ODBC タイムスタンプ エスケープ シーケンスは、{ *literal_type* '*constant_value*' }: という形式です。<br /><br /> <br /><br /> - *literal_type* はエスケープ シーケンスの種類を指定します。 タイムスタンプには次の 3 つの *literal_type* 指定子があります。<br />1) d = 日付のみ<br />2) t = 時刻のみ<br />3) ts = タイムスタンプ (時刻と日付)<br /><br /> <br /><br /> - '*constant_value*' はエスケープ シーケンスの値です。 *constant_value* は *literal_type* ごとに次の形式に従う必要があります。<br />d : yyyy-mm-dd<br />t : hh:mm:ss[.fff]<br />ts : yyyy-mm-dd hh:mm:ss[.fff]|  
  
## <a name="rounding-of-datetime-fractional-second-precision"></a>datetime における 1 秒未満の秒の有効桁数の丸め処理  
以下の表に示すように、**datetime** 値は .000、.003、または .007 秒の単位になるように丸められます。
  
|ユーザー指定の値|システム格納値|  
|---|---|
|01/01/98 23:59:59.999|1998-01-02 00:00:00.000|  
|01/01/98 23:59:59.995<br /><br /> 01/01/98 23:59:59.996<br /><br /> 01/01/98 23:59:59.997<br /><br /> 01/01/98 23:59:59.998|1998-01-01 23:59:59.997|  
|01/01/98 23:59:59.992<br /><br /> 01/01/98 23:59:59.993<br /><br /> 01/01/98 23:59:59.994|1998-01-01 23:59:59.993|  
|01/01/98 23:59:59.990<br /><br /> 01/01/98 23:59:59.991|1998-01-01 23:59:59.990|  
  
## <a name="ansi-and-iso-8601-compliance"></a>ANSI および ISO 8601 への準拠  
**datetime** は ANSI または ISO 8601 に準拠していません。
  
##  <a name="converting-date-and-time-data"></a><a name="_datetime"></a> 日付型データと時刻型データの変換  
日付と時刻のデータ型に変換する場合、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] で日付または時刻と認識できない値はすべて拒否されます。 CAST 関数および CONVERT 関数で日付と時刻のデータを使用する方法については、「[CAST および CONVERT &#40;Transact-SQL&#41;](../../t-sql/functions/cast-and-convert-transact-sql.md)」をご覧ください。
  
### <a name="converting-other-date-and-time-types-to-the-datetime-data-type"></a>他の日付/時刻型から datetime データ型からへの変換 
ここでは、他の日付/時刻データ型が **datetime** データ型に変換される場合の処理について説明します。  
  
**date** からの変換では、年、月、日がコピーされます。 時刻部分は 00:00:00.000 に設定されます。 次のコードは、`date` 値を `datetime` 値に変換した結果を示しています。  
  
```sql
DECLARE @date date = '12-21-16';  
DECLARE @datetime datetime = @date;  
  
SELECT @datetime AS '@datetime', @date AS '@date';  
  
--Result  
--@datetime               @date  
------------------------- ----------  
--2016-12-21 00:00:00.000 2016-12-21  
```  
  
**time(n)** からの変換では、時刻部分がコピーされ、日付部分は "1900-01-01" に設定されます。 **time(n)** 値の小数部の有効桁数が 3 桁を超える場合、値はその桁数に合わせて切り捨てられます。 次の例は、`time(4)` 値を `datetime` 値に変換した結果を示しています。  
  
```sql
DECLARE @time time(4) = '12:10:05.1237';  
DECLARE @datetime datetime = @time;  
  
SELECT @datetime AS '@datetime', @time AS '@time';  
  
--Result  
--@datetime               @time  
------------------------- -------------  
--1900-01-01 12:10:05.123 12:10:05.1237  
```  
  
**smalldatetime** からの変換では、時と分がコピーされます。 秒と秒の小数部は 0 に設定されます。 次のコードは、`smalldatetime` 値を `datetime` 値に変換した結果を示しています。  
  
```sql
DECLARE @smalldatetime smalldatetime = '12-01-16 12:32';  
DECLARE @datetime datetime = @smalldatetime;  
  
SELECT @datetime AS '@datetime', @smalldatetime AS '@smalldatetime';  
  
--Result  
--@datetime               @smalldatetime  
------------------------- -----------------------  
--2016-12-01 12:32:00.000 2016-12-01 12:32:00  
```  
  
**datetimeoffset(n)** からの変換では、日付と時刻の部分がコピーされます。 タイム ゾーンは切り捨てられます。 **datetimeoffset(n)** 値の小数部の有効桁数が 3 桁を超える場合、値は切り捨てられます。 次の例は、`datetimeoffset(4)` 値を `datetime` 値に変換した結果を示しています。  
  
```sql
DECLARE @datetimeoffset datetimeoffset(4) = '1968-10-23 12:45:37.1234 +10:0';  
DECLARE @datetime datetime = @datetimeoffset;  
  
SELECT @datetime AS '@datetime', @datetimeoffset AS '@datetimeoffset';  
  
--Result  
--@datetime               @datetimeoffset  
------------------------- ------------------------------  
--1968-10-23 12:45:37.123 1968-10-23 12:45:37.1237 +01:0   
```  
  
**datetime2(n)** からの変換では、日付と時刻がコピーされます。 **datetime2(n)** 値の小数部の有効桁数が 3 桁を超える場合、値は切り捨てられます。 次の例は、`datetime2(4)` 値を `datetime` 値に変換した結果を示しています。  
  
```sql
DECLARE @datetime2 datetime2(4) = '1968-10-23 12:45:37.1237';  
DECLARE @datetime datetime = @datetime2;  
  
SELECT @datetime AS '@datetime', @datetime2 AS '@datetime2';  
  
--Result  
--@datetime               @datetime2  
------------------------- ------------------------  
--1968-10-23 12:45:37.123 1968-10-23 12:45:37.1237  
```  
  
## <a name="examples"></a>例  
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
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
|データ型|出力|  
|---|---|
|**time**|12:35:29. 1234567|  
|**date**|2007-05-08|  
|**smalldatetime**|2007-05-08 12:35:00|  
|**datetime**|2007-05-08 12:35:29.123|  
|**datetime2**|2007-05-08 12:35:29. 1234567|  
|**datetimeoffset**|2007-05-08 12:35:29.1234567 +12:15|  
  
## <a name="see-also"></a>関連項目
[CAST および CONVERT &#40;Transact-SQL&#41;](../../t-sql/functions/cast-and-convert-transact-sql.md)
  
  
