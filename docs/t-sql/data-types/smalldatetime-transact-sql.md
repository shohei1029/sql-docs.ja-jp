---
description: smalldatetime (Transact-SQL)
title: smalldatetime (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 07/22/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- smalldatetime_TSQL
- smalldatetime
dev_langs:
- TSQL
helpviewer_keywords:
- time [SQL Server], data types
- smalldatetime data type [SQL Server]
- dates [SQL Server], data types
- date and time [SQL Server], smalldatetime
- data types [SQL Server], date and time
ms.assetid: 68b74610-d54c-4c8e-b4b2-7e3747546ee0
author: MikeRayMSFT
ms.author: mikeray
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 24de52b89c597167c8ba6b9f7448701b637bba87
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104736852"
---
# <a name="smalldatetime-transact-sql"></a>smalldatetime (Transact-SQL)

[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

日付を時刻と組み合わせて定義します。 時間は 24 時制とし、秒は常にゼロ (: 00) にし、秒の小数部は使用しません。
  
> [!NOTE]  
>  使用して、 **time**, 、**date**, 、**datetime2** と **datetimeoffset** 新しい作業のデータ型。 これらの型は、SQL 標準に準拠しています。 これらの型は、移植性がより高いです。 **time**、**datetime2**、**datetimeoffset** は秒の有効桁数が増えています。 **datetimeoffset** グローバルに配置されるアプリケーション向けにタイム ゾーンのサポートを提供します。  
  
## <a name="smalldatetime-description"></a>smalldatetime の説明
  
|プロパティ|値|
|--------|-----|
|構文|**smalldatetime**|  
|使用法|DECLARE \@MySmalldatetime **smalldatetime**<br /><br /> CREATE TABLE Table1 ( Column1 **smalldatetime** )|  
|既定の文字列リテラル形式<br /><br /> (下位クライアントに使用)|適用なし|  
|期間|1900-01-01 から 2079-06-06<br /><br /> 1900 年 1 月 1 日から 2079 年 6 月 6 日|  
|時間の範囲|00:00:00 から 23:59:59<br /><br /> 2007-05-09 23:59:59 は次のように丸められます。<br /><br /> 2007-05-10 00:00:00|  
|要素範囲|YYYY は、1900 から 2079 の年を表す 4 桁の数字です。<br /><br /> MM は、指定された年の 01 ～ 12 の月を表す 2 桁の数字です。<br /><br /> DD は、指定された月の (月に応じて) 01 ～ 31 の日を表す 2 桁の数字です。<br /><br /> hh は、00 ～ 23 の時を表す 2 桁の数字です。<br /><br /> mm は、分を表す 00 から 59 の 2 桁の数字です。<br /><br /> ss は、秒を表す 00 から 59 の 2 桁の数字です。 29.998 秒以下の値は、最も近い分に切り捨てられます。 29.999 秒以上の値は、最も近い分に切り上げられます。|  
|文字長|最大 19 文字|  
|ストレージ サイズ|4 バイト、固定。|  
|精度|1 分|  
|既定値|1900-01-01 00:00:00|  
|Calendar|グレゴリオ暦<br /><br /> (完全な年の範囲は含まれません。)|  
|ユーザー定義の 1 秒未満の秒の有効桁数|いいえ|  
|タイム ゾーン オフセットへの対応と保持|いいえ|  
|夏時間への対応|いいえ|  
  
## <a name="ansi-and-iso-8601-compliance"></a>ANSI および ISO 8601 への準拠  
**smalldatetime** は ANSI または ISO 8601 に準拠していません。
  
## <a name="converting-date-and-time-data"></a>日付と時刻のデータ型の変換
日付と時刻のデータ型に変換する場合、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] で日付または時刻と認識できない値はすべて拒否されます。 CAST 関数および CONVERT 関数で日付と時刻のデータを使用する方法については、「[CAST および CONVERT &#40;Transact-SQL&#41;](../../t-sql/functions/cast-and-convert-transact-sql.md)」をご覧ください。
  
### <a name="converting-smalldatetime-to-other-date-and-time-types"></a>smalldatetime から他の日付/時刻データ型への変換
ここでは、**smalldatetime** データ型が他の日付/時刻データ型に変換される場合の処理について説明します。
  
**date** への変換の場合は、年、月、および日がコピーされます。 次のコードは、`smalldatetime` 値を `date` 値に変換した結果を示しています。
  
```sql
DECLARE @smalldatetime smalldatetime = '1955-12-13 12:43:10';  
DECLARE @date date = @smalldatetime  
  
SELECT @smalldatetime AS '@smalldatetime', @date AS 'date';  
  
--Result  
--@smalldatetime          date  
------------------------- ----------  
--1955-12-13 12:43:00     1955-12-13  
--  
--(1 row(s) affected)  
```  
  
**time(n)** への変換では、時、分、および秒がコピーされます。 秒未満の時間は 0 に設定されます。 次のコードは、`smalldatetime` 値を `time(4)` 値に変換した結果を示しています。
  
```sql
DECLARE @smalldatetime smalldatetime = '1955-12-13 12:43:10';  
DECLARE @time time(4) = @smalldatetime;  
  
SELECT @smalldatetime AS '@smalldatetime', @time AS 'time';  
  
--Result  
--@smalldatetime          time  
------------------------- -------------  
--1955-12-13 12:43:00     12:43:00.0000  
--  
--(1 row(s) affected)  
```  
  
**datetime** に変換する場合は、**smalldatetime** 値が **datetime** 値にコピーされます。 秒未満の時間は 0 に設定されます。 次のコードは、`smalldatetime` 値を `datetime` 値に変換した結果を示しています。
  
```sql
DECLARE @smalldatetime smalldatetime = '1955-12-13 12:43:10';  
DECLARE @datetime datetime = @smalldatetime;  
  
SELECT @smalldatetime AS '@smalldatetime', @datetime AS 'datetime';  
  
--Result  
--@smalldatetime          datetime  
------------------------- -----------------------  
--1955-12-13 12:43:00     1955-12-13 12:43:00.000  
--  
--(1 row(s) affected)  
```  
  
**datetimeoffset(n)** に変換する場合は、**smalldatetime** 値が **datetimeoffset(n)** 値にコピーされます。 秒の小数部は 0 に設定され、タイム ゾーン オフセットは + 00:0 に設定されます。 次のコードは、`smalldatetime` 値を `datetimeoffset(4)` 値に変換した結果を示しています。
  
```sql
DECLARE @smalldatetime smalldatetime = '1955-12-13 12:43:10';  
DECLARE @datetimeoffset datetimeoffset(4) = @smalldatetime;  
  
SELECT @smalldatetime AS '@smalldatetime', @datetimeoffset AS 'datetimeoffset(4)';  
  
--Result  
--@smalldatetime          datetimeoffset(4)  
------------------------- ------------------------------  
--1955-12-13 12:43:00     1955-12-13 12:43:00.0000 +00:0  
--  
--(1 row(s) affected)  
```  
  
**datetime2(n)** に変換する場合は、**smalldatetime** 値が **datetime2(n)** 値にコピーされます。 秒未満の時間は 0 に設定されます。 次のコードは、`smalldatetime` 値を `datetime2(4)` 値に変換した結果を示しています。
  
```sql
DECLARE @smalldatetime smalldatetime = '1955-12-13 12:43:10';  
DECLARE @datetime2 datetime2(4) = @smalldatetime;  
  
SELECT @smalldatetime AS '@smalldatetime', @datetime2 AS ' datetime2(4)';  
  
--Result  
--@smalldatetime           datetime2(4)  
------------------------- ------------------------  
--1955-12-13 12:43:00     1955-12-13 12:43:00.0000  
--  
--(1 row(s) affected)  
```  
  
## <a name="examples"></a>例  
  
### <a name="a-casting-string-literals-with-seconds-to-smalldatetime"></a>A. 秒を含む文字列リテラルを smalldatetime にキャストする  
次の例では、文字列リテラル内の秒の `smalldatetime` への変換を比較します。
  
```sql
SELECT   
     CAST('2007-05-08 12:35:29'     AS smalldatetime)  
    ,CAST('2007-05-08 12:35:30'     AS smalldatetime)  
    ,CAST('2007-05-08 12:59:59.998' AS smalldatetime);  
```  
  
|入力|Output|  
|---|---|
|2007-05-08 12:35:29|2007-05-08 12:35:00|  
|2007-05-08 12:35:30|2007-05-08 12:36:00|  
|2007-05-08 12:59:59.998|2007-05-08 13:00:00|  
  
### <a name="b-comparing-date-and-time-data-types"></a>B. date と time のデータ型を比較する  
次の例では、文字列をそれぞれの date および time データ型にキャストした結果を比較します。
  
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
|---|---|
|**time**|12:35:29. 1234567|  
|**date**|2007-05-08|  
|**smalldatetime**|2007-05-08 12:35:00|  
|**datetime**|2007-05-08 12:35:29.123|  
|**datetime2**|2007-05-08 12:35:29. 1234567|  
|**datetimeoffset**|2007-05-08 12:35:29.1234567 +12:15|  
  
## <a name="see-also"></a>関連項目
[CAST および CONVERT &#40;Transact-SQL&#41;](../../t-sql/functions/cast-and-convert-transact-sql.md)
  
