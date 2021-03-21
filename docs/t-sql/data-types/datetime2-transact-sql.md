---
description: datetime2 (Transact-SQL)
title: datetime2 (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 08/21/2020
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- datetime2
- datetime2_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- time [SQL Server], data types
- dates [SQL Server], data types
- date and time [SQL Server], datetime2
- data types [SQL Server], date and time
- datetime2 data type [SQL Server]
ms.assetid: 868017f3-214f-43ef-8536-cc1632a2288f
author: MikeRayMSFT
ms.author: mikeray
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: ad0fb7d21621f83370dfcef29ea57b8b1cf4680b
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104747712"
---
# <a name="datetime2-transact-sql"></a>datetime2 (Transact-SQL)

[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

24 時間形式の時刻と組み合わせた日付を定義します。 **datetime2** は、既存の **datetime** 型を拡張して、日付範囲と既定の有効桁数を増やし、ユーザーが必要に応じて有効桁数を指定できるようにしたものと考えることができます。
  
## <a name="datetime2-description"></a>datetime2 の説明
  
|プロパティ|値|  
|--------------|-----------|  
|構文|**datetime2** [ (*fractional seconds precision*) ]|  
|使用法|DECLARE \@MyDatetime2 **datetime2(7)**<br /><br /> CREATE TABLE Table1 ( Column1 **datetime2(7)** )|  
|既定の文字列リテラル形式<br /><br /> (下位クライアントに使用)|YYYY-MM-DD hh:mm:ss[.fractional seconds]<br /><br /> 詳細については、後述の「下位クライアントの下位互換性」セクションを参照してください。|  
|期間|0001-01-01 ～ 31.12.99<br /><br /> 1 月 1 日 1 CE ～12 月 31 日 9999 CE|  
|時間の範囲|00:00:00 から 23:59:59.9999999|  
|タイム ゾーンのオフセット範囲|なし|  
|要素範囲|YYYY は、年を表す 0001 から 9999 までの 4 桁の数字です。<br /><br /> MM は、指定された年の 01 ～ 12 の月を表す 2 桁の数字です。<br /><br /> DD は、指定された月の (月に応じて) 01 ～ 31 の日を表す 2 桁の数字です。<br /><br /> hh は、時を表す 00 から 23 までの 2 桁の数字です。<br /><br /> mm は、分を表す 00 から 59 までの 2 桁の数字です。<br /><br /> ss は、秒を表す 00 ～ 59 の 2 桁の数字です。<br /><br /> n* は、秒の小数部の有効桁数を表す 0 ～ 7 桁の数字です (0 ～ 9999999)。 Informatica では、秒の小数部が 3 桁より多いときは切り捨てられます。|  
|文字長|19 文字 (YYYY-MM-DD hh:mm:ss ) 以上、27 文字 (YYYY-MM-DD hh:mm:ss.0000000) 以下|  
|有効桁数、小数点以下桁数|0 から 7 桁で、精度は 100ns です。 既定の有効桁数は 7 桁です。|  
|ストレージ サイズ <sup>1</sup>|有効桁数が 3 より小さい場合は 6 バイトです。<br/>有効桁数が 3 または 4 の場合は 7 バイトです。<br/>その他のすべての有効桁数では 8 バイトが必要です。<sup>2</sup>|  
|精度|100 ナノ秒|  
|既定値|1900-01-01 00:00:00|  
|Calendar|グレゴリオ暦|  
|ユーザー定義の 1 秒未満の秒の有効桁数|はい|  
|タイム ゾーン オフセットへの対応と保持|いいえ|  
|夏時間への対応|いいえ|  

<sup>1</sup> 指定された値は、非圧縮行ストア用です。 [データ圧縮](../../relational-databases/data-compression/data-compression.md)または[列ストア](../../relational-databases/indexes/columnstore-indexes-overview.md)を使用すると、有効桁数ごとにストレージ サイズが変更される可能性があります。 また、ディスク上およびメモリ内のストレージ サイズが異なる場合があります。 たとえば、バッチ モードを使用する場合、**datetime2** 値は常にメモリ内の 8 バイトを必要とします。

<sup>2</sup> **datetime2** 値が **varbinary** 値にキャストされると、有効桁数を格納するために追加のバイトが **varbinary** 値に追加されます。

データ型のメタデータについては、「[sys.systypes &#40;Transact-SQL&#41;](../../relational-databases/system-compatibility-views/sys-systypes-transact-sql.md)」または「[TYPEPROPERTY &#40;Transact-SQL&#41;](../../t-sql/functions/typeproperty-transact-sql.md)」をご覧ください。 一部の日付時刻データ型では、有効桁数および小数点以下桁数が可変です。 列の有効桁数と小数点以下桁数を取得す方法については、「[COLUMNPROPERTY &#40;Transact-SQL&#41;](../../t-sql/functions/columnproperty-transact-sql.md)」、[COL_LENGTH &#40;Transact-SQL&#41;](../../t-sql/functions/col-length-transact-sql.md)」、または「[sys.columns &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-columns-transact-sql.md)」をご覧ください。
  
## <a name="supported-string-literal-formats-for-datetime2"></a>datetime2 でサポートされる文字列リテラル形式
次の表は、**datetime2** でサポートされている ISO 8601 および ODBC の文字列リテラル形式を一覧にしたものです。 **datetime2** の日付部分と時刻部分に使用できるアルファベット、数値、区切りなし、時刻の各形式については、「[date &#40;Transact-SQL&#41;](../../t-sql/data-types/date-transact-sql.md)」および「[time &#40;Transact-SQL&#41;](../../t-sql/data-types/time-transact-sql.md)」をご覧ください。
  
|ISO 8601|説明|  
|---|---|
|YYYY-MM-DDThh:mm:ss[.nnnnnnn]<br /><br /> YYYY-MM-DDThh:mm:ss[.nnnnnnn]|この形式は、セッションのロケール設定である SET LANGUAGE および SET DATEFORMAT の影響を受けません。 文字列リテラルには、**T**、コロン (:)、およびピリオド (.) が含まれます (例: '2007-05-02T19:58:47.1234567')。|  
  
|ODBC|説明|  
|---|---|
|{ ts 'yyyy-mm-dd hh:mm:ss[.fractional seconds]' }|ODBC API 固有:<br /><br /> 1 秒未満の秒を表す小数点の右側の桁数は、0 から 7 (100 ナノ秒) の範囲で指定できます。|  
  
## <a name="ansi-and-iso-8601-compliance"></a>ANSI および ISO 8601 への準拠  
**datetime2** の ANSI および ISO 8601 への準拠は、[date](../../t-sql/data-types/date-transact-sql.md) 型および [time](../../t-sql/data-types/time-transact-sql.md) 型と同じです。
  
##  <a name="backward-compatibility-for-down-level-clients"></a>下位クライアントの下位互換性  
一部の下位レベル クライアントは、**time**、**date**、**datetime2**、**datetimeoffset** データ型をサポートしていません。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の上位インスタンスと下位クライアントの間のデータ型マッピングを次の表に示します。
  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のデータ型|下位クライアントに渡される既定の文字列リテラル形式|下位 ODBC|下位 OLEDB|下位 JDBC|下位 SQLCLIENT|  
| --- | --- | --- | --- | --- | --- |
|**time**|hh:mm:ss[.nnnnnnn]|SQL_WVARCHAR または SQL_VARCHAR|DBTYPE_WSTR または DBTYPE_STR|Java.sql.String|String または SqString|  
|**date**|YYYY-MM-DD|SQL_WVARCHAR または SQL_VARCHAR|DBTYPE_WSTR または DBTYPE_STR|Java.sql.String|String または SqString|  
|**datetime2**|YYYY-MM-DD hh:mm:ss[.nnnnnnn]|SQL_WVARCHAR または SQL_VARCHAR|DBTYPE_WSTR または DBTYPE_STR|Java.sql.String|String または SqString|  
|**datetimeoffset**|YYYY-MM-DD hh:mm:ss[.nnnnnnn] [+&#124;-]hh:mm|SQL_WVARCHAR または SQL_VARCHAR|DBTYPE_WSTR または DBTYPE_STR|Java.sql.String|String または SqString|  
  
## <a name="converting-date-and-time-data"></a>日付と時刻のデータ型の変換
日付と時刻のデータ型に変換する場合、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] で日付または時刻と認識できない値はすべて拒否されます。 CAST 関数および CONVERT 関数で日付と時刻のデータを使用する方法については、「[CAST および CONVERT &#40;Transact-SQL&#41;](../../t-sql/functions/cast-and-convert-transact-sql.md)」をご覧ください。
  
### <a name="converting-other-date-and-time-types-to-the-datetime2-data-type"></a>他の日付/時刻型から datetime2 データ型からへの変換
ここでは、他の日付/時刻データ型が **datetime2** データ型に変換される場合の処理について説明します。  
  
**date** からの変換では、年、月、日がコピーされます。  時刻部分は 00:00:00.0000000 に設定されます。  次のコードは、`date` 値を `datetime2` 値に変換した結果を示しています。  
  
```sql
DECLARE @date date = '12-21-16';
DECLARE @datetime2 datetime2 = @date;

SELECT @datetime2 AS '@datetime2', @date AS '@date';
  
--Result  
--@datetime2                  @date
----------------------------- ----------
--2016-12-21 00:00:00.0000000 2016-12-21
```  
  
**time(n)** からの変換では、時刻部分がコピーされ、日付部分は "1900-01-01" に設定されます。 次の例は、`time(7)` 値を `datetime2` 値に変換した結果を示しています。  
  
```sql
DECLARE @time time(7) = '12:10:16.1234567';
DECLARE @datetime2 datetime2 = @time;

SELECT @datetime2 AS '@datetime2', @time AS '@time';
  
--Result  
--@datetime2                  @time
----------------------------- ----------------
--1900-01-01 12:10:16.1234567 12:10:16.1234567
```  
  
**smalldatetime** からの変換では、時と分がコピーされます。 秒と秒の小数部は 0 に設定されます。 次のコードは、`smalldatetime` 値を `datetime2` 値に変換した結果を示しています。  
  
```sql
DECLARE @smalldatetime smalldatetime = '12-01-16 12:32';
DECLARE @datetime2 datetime2 = @smalldatetime;

SELECT @datetime2 AS '@datetime2', @smalldatetime AS '@smalldatetime'; 
  
--Result  
--@datetime2                  @smalldatetime
----------------------------- -----------------------
--2016-12-01 12:32:00.0000000 2016-12-01 12:32:00 
```  
  
**datetimeoffset(n)** からの変換では、日付と時刻の部分がコピーされます。 タイム ゾーンは切り捨てられます。 次の例は、`datetimeoffset(7)` 値を `datetime2` 値に変換した結果を示しています。  
  
```sql
DECLARE @datetimeoffset datetimeoffset(7) = '2016-10-23 12:45:37.1234567 +10:0';
DECLARE @datetime2 datetime2 = @datetimeoffset;

SELECT @datetime2 AS '@datetime2', @datetimeoffset AS '@datetimeoffset'; 
  
--Result  
--@datetime2                  @datetimeoffset
----------------------------- ----------------------------------
--2016-10-23 12:45:37.1234567 2016-10-23 12:45:37.1234567 +10:00
```  

**datetime** からの変換では、日付と時刻がコピーされます。 小数部の有効桁数は 7 桁まで拡張されます。 次の例は、`datetime` 値を `datetime2` 値に変換した結果を示しています。

```sql
DECLARE @datetime datetime = '2016-10-23 12:45:37.333';
DECLARE @datetime2 datetime2 = @datetime;

SELECT @datetime2 AS '@datetime2', @datetime AS '@datetime';
   
--Result  
--@datetime2                  @datetime
------------------------- ---------------------------
--2016-10-23 12:45:37.3333333 2016-10-23 12:45:37.333
```  

> [!NOTE]
> データベース互換性レベル 130 の下で、datetime から datetime2 にデータ型を暗黙的に変換するとき、精度が上がります。上の例に示すように、小数ミリ秒が計算に入り、結果的にさまざまな変換値が生成されます。 datetime データ型と datetime2 データ型の間で混合比較シナリオが存在する場合、datetime2 データ型への明示的型変換を使用します。 詳細については、この [Microsoft サポート技術情報](https://support.microsoft.com/help/4010261)を参照してください。

### <a name="converting-string-literals-to-datetime2"></a>文字列リテラルから datetime2 への変換  
文字列リテラルから日付/時刻データ型への変換は、文字列のすべての部分が有効な形式になっている場合に可能になります。 それ以外の場合は実行時エラーが発生します。 日付/時刻データ型から文字列リテラルへの暗黙的な変換や、スタイルを指定しない明示的な変換では、現在のセッションの既定の形式が使用されます。 次の表では、文字列リテラルを **datetime2** データ型に変換する規則を示します。
  
|入力文字列リテラル|**datetime2(n)**|  
|---|---|
|ODBC DATE|ODBC 文字列リテラルは、**datetime** データ型にマップされます。 ODBC DATETIME リテラルから **datetime2** 型への代入演算を行うと、**datetime** 型とこれらの型との間で、変換規則で定義されている暗黙的な変換が行われます。|  
|ODBC TIME|上記の ODBC 日付の規則を参照。|  
|ODBC DATETIME|上記の ODBC 日付の規則を参照。|  
|DATE のみ|時刻部分は既定値の 00:00:00 に設定される|  
|TIME のみ|DATE 部分は既定で 1900-1-1 に設定されます。|  
|タイム ゾーンのみ|既定値が指定されます。|  
|DATE + TIME|単純変換|  
|DATE + TIMEZONE|許可されていません。|  
|TIME + TIMEZONE|DATE 部分は既定で 1900-1-1 に設定されます。 タイム ゾーンの入力は無視される。|  
|DATE + TIME + TIMEZONE|ローカル DATETIME が使用されます。|  
  
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
|**time**|12:35:29.1234567|  
|**date**|2007-05-08|  
|**smalldatetime**|2007-05-08 12:35:00|  
|**datetime**|2007-05-08 12:35:29.123|  
|**datetime2**|2007-05-08 12:35:29.1234567|  
|**datetimeoffset**|2007-05-08 12:35:29.1234567 +12:15|  
  
## <a name="see-also"></a>関連項目
[CAST および CONVERT &#40;Transact-SQL&#41;](../../t-sql/functions/cast-and-convert-transact-sql.md)
  
  
