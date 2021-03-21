---
description: datetime データ型の SQL から C への変換
title: SQL から C への変換 |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- conversions [ODBC], SQL to C
ms.assetid: 059431e2-a65c-4587-ba4a-9929a1611e96
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 543e32480f1fcdfcca09390f26c733b838f3ba75
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104756102"
---
# <a name="datetime-data-type-conversions-from-sql-to-c"></a>datetime データ型の SQL から C への変換
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  次の表に、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の日付型または時刻型から C の型に変換する際に考慮する問題を示します。  
  
## <a name="conversions"></a>コンバージョン  
  
|   | SQL_C_DATE | SQL_C_TIME | SQL_C_TIMESTAMP | SQL_C_SS_TIME2 | SQL_C_SS_TIMESTAMPOFFSET | SQL_C_BINARY | SQL_C_CHAR | SQL_C_WCHAR |
| - | ---------- | ---------- | --------------- | -------------- | ------------------------ | ------------ | ---------- | ----------- |
| **SQL_CHAR** |2、3、4、5|2、3、6、7、8|2、3、9、10、11|2、3、6、7|2、3、9、10、11|1|1|1|  
| **SQL_WCHAR** |2、3、4、5|2、3、6、7、8|2、3、9、10、11|2、3、6、7|2、3、9、10、11|1|1|1|  
| **SQL_TYPE_DATE** |OK|12|13|12|13、23|14|16|16|  
| **SQL_SS_TIME2** |12|8|15|OK|10、23|17|16|16|  
| **SQL_TYPE_TIMESTAMP** |18|7、8|OK|7|23|19|16|16|  
| **SQL_SS_TIMESTAMPOFFSET** |18、22|7、8、20|20|7、20|OK|21|16|16|  
  
## <a name="key-to-symbols"></a>記号の説明  
  
|Symbol|意味|  
|------------|-------------|  
|[OK]|変換の問題は発生しません。|  
|1|[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] より前の規則が適用されます。|  
|2|先頭および末尾にあるスペースは無視されます。|  
|3|文字列が日付、時刻、タイム ゾーン、またはタイム ゾーン オフセットに解析され、秒の小数部は 9 桁まで許容されます。 タイム ゾーン オフセットが解析されると、時刻はクライアントのタイム ゾーンに変換されます。 この変換中にエラーが発生した場合、"Datetime field overflow" というメッセージで SQLSTATE 22018 の診断レコードが生成されます。|  
|4|値が日付、タイムスタンプ、またはタイムスタンプ オフセットの有効な値ではない場合、"キャストした文字コードが正しくありません" というメッセージで SQLSTATE 22018 の診断レコードが生成されます。|  
|5|時刻が 0 以外の場合、"分数が切り捨てられました" というメッセージで SQLSTATE 01S07 の診断レコードが生成されます。|  
|6|値が時刻、タイムスタンプ、またはタイムスタンプ オフセットの有効な値ではない場合、"キャストした文字コードが正しくありません" というメッセージで SQLSTATE 22018 の診断レコードが生成されます。|  
|7|日付部分は無視されます。|  
|8|秒の小数部が 0 以外の場合、"分数が切り捨てられました" というメッセージで SQLSTATE 01S07 の診断レコードが生成されます。|  
|9|値が日付、時刻、タイムスタンプ、またはタイムスタンプ オフセットの有効な値ではない場合、"キャストした文字コードが正しくありません" というメッセージで SQLSTATE 22018 の診断レコードが生成されます。|  
|10|値が有効な時刻の場合、日付部分は現在のクライアントの日付に設定されます。|  
|11|値が有効な日付の場合、時刻は 0 に設定されます。|  
|12|"データ型の属性に関する制限に違反しました" というメッセージで SQLSTATE 07006 の診断レコードが生成されます。|  
|13|時刻は 0 に設定されます。|  
|14|SQL_DATE_STRUCT を格納するにはバッファーの大きさが十分ではない場合、"数値が範囲を超えています" というメッセージで SQLSTATE 22003 の診断レコードが生成されます。|  
|15|日付は現在のクライアントの日付に設定されます。|  
|16|変換された文字列値の秒の小数部以外を格納するにはバッファーの大きさが十分でない場合、"文字列データの右側が切り捨てられました" というメッセージで SQLSTATE 01004 の診断レコードが生成されます。 日付、時刻、またはオフセット部分を切り捨てずに文字列値を格納するにはバッファーの大きさが十分でない場合、"数値が範囲を超えています" というメッセージで SQLSTATE 22003 の診断レコードが生成されます。 日付とタイムスタンプ オフセットでは、変換された文字列の右端に秒の小数部が含まれないため、SQLSTATE 01004 は生成されないことに注意してください。 したがって、切り捨てにより、データの損失が発生します。|  
|17|SQL_SS_TIME2_STRUCT を格納するにはバッファーの大きさが十分でない場合、"数値が範囲を超えています" というメッセージで SQLSTATE 22003 の診断レコードが生成されます。|  
|18|時刻が 0 以外の場合、"分数が切り捨てられました" というメッセージで SQLSTATE 01S07 の診断レコードが生成されます。|  
|19|サーバー側の型が datetime または smalldatetime の場合、値は TDS ワイヤ形式に対応し、smalldatetime の場合は 4 バイトの値、datetime の場合は 8 バイトの値になります。<br /><br /> サーバー側の型が datetime2 の場合、値は SQL_TIMESTAMP_STRUCT として返されます。 返された値を格納するにはバッファーの大きさが十分でない場合、"数値が範囲を超えています" というメッセージで SQLSTATE 22003 の診断レコードが生成されます。|  
|20|時刻はクライアントのタイム ゾーンに変換されます。 この変換中にエラーが発生すると、"Datetime フィールド オーバーフロー" というメッセージで SQLSTATE 22008 の診断レコードが生成されます。|  
|21|SQL_SS_TIMESTAMPOFFSET_STRUCT を格納するのにバッファーの大きさが十分である場合、値は SQL_SS_TIMESTAMPOFFSET_STRUCT として返されます。 それ以外の場合は、"数値が範囲を超えています" というメッセージで SQLSTATE 22003 の診断レコードが生成されます。|  
|22|値がクライアントのタイム ゾーンに変換されてから、日付が抽出されます。 これにより、タイムスタンプ オフセットの種類によるその他の変換との一貫性が提供されます。 この変換中にエラーが発生すると、"Datetime フィールド オーバーフロー" というメッセージで SQLSTATE 22008 の診断レコードが生成されます。 これにより、単純な切り捨てによって取得された値とは異なる日付になることもあります。|  
  
 このトピックの表では、クライアントに返される型とバインドの型との間の変換について説明しています。 出力パラメーターの場合、SQLBindParameter で指定されたサーバーの型がサーバー上の実際の型と一致しないと、サーバーによって暗黙的な変換が実行され、クライアントに返される型は SQLBindParameter によって指定された型と一致します。 これにより、サーバーの変換規則が前の表に記載されているものと異なる場合に、予期しない変換結果が発生する可能性があります。 たとえば、既定の日付を指定する必要がある場合、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では現在の日付ではなく 1900-1-1 が使用されます。  
  
## <a name="see-also"></a>参照  
 [ODBC&#41;&#40;の日付と時刻の改善 ](../../relational-databases/native-client-odbc-date-time/date-and-time-improvements-odbc.md)  
  
  
