---
title: 型のサポート、ODBC の日付と時刻
description: 文字列、リテラル、データ構造のデータ型のマッピングと形式など、SQL Server の日付と時刻のデータ型をサポートする ODBC 型について説明します。
ms.custom: ''
ms.date: 12/18/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- date/time [ODBC], data type support
- ODBC, date/time improvements
ms.assetid: 8e0d9ba2-3ec1-4680-86e3-b2590ba8e2e9
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 8acb8060cb543d264f7237bb720d54ce5b9daedd
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104756122"
---
# <a name="data-type-support-for-odbc-date-and-time-improvements"></a>ODBC の日付/時刻の強化に対するデータ型のサポート
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  このトピックでは、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の日付と時刻のデータ型をサポートする ODBC 型について説明します。  
  
## <a name="data-type-mapping-in-parameters-and-resultsets"></a>パラメーターと結果セットでのデータ型マッピング  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client ODBC では、サーバーの新しい型を公開するために、ODBC データ型 (SQL_TYPE_TIMESTAMP と SQL_TIMESTAMP) に加え、次の新しい 2 つのデータ型が追加されました。  
  
-   SQL_SS_TIME2  
  
-   SQL_SS_TIMESTAMPOFFSET  
  
 次の表は、完全なサーバー型のマッピングを示しています。 表の一部のセルには、2 つの項目が記載されています。このような場合、1 つ目は ODBC 3.0 の値、2 つ目は ODBC 2.0 の値です。  
  
|SQL Server のデータ型|SQL データ型|値|  
|--------------------------|-------------------|-----------|  
|Datetime|SQL_TYPE_TIMESTAMP<br /><br /> SQL_TIMESTAMP|93 (sql.h)<br /><br /> 11 (sqlext.h)|  
|Smalldatetime|SQL_TYPE_TIMESTAMP<br /><br /> SQL_TIMESTAMP|93 (sql.h)<br /><br /> 11 (sqlext.h)|  
|Date|SQL_TYPE_DATE<br /><br /> SQL_DATE|91 (sql .h)<br /><br /> 9 (sqlext. h)|  
|Time|SQL_SS_TIME2|-154 (SQLNCLI)|  
|DatetimeOFFSET|SQL_SS_TIMESTAMPOFFSET|-155 (SQLNCLI.h)|  
|Datetime2|SQL_TYPE_TIMESTAMP<br /><br /> SQL_TIMESTAMP|93 (sql.h)<br /><br /> 11 (sqlext.h)|  
||||

 次の表に、対応する構造体と ODBC C 型を示します。 ODBC ではドライバーで定義された C 型が許可されないため、time と datetimeoffset では、SQL_C_BINARY がバイナリ構造体として使用されます。  
  
|SQL データ型|メモリ レイアウト|既定の C データ型|値 (sqlext.h)|  
|-------------------|-------------------|-------------------------|------------------------|  
|SQL_TYPE_TIMESTAMP<br /><br /> SQL_TIMESTAMP|SQL_TIMESTAMP_STRUCT<br /><br /> TIMESTAMP_STRUCT|SQL_C_TYPE_TIMESTAMP<br /><br /> SQL_C_TIMESTAMP|SQL_TYPE_TIMESTAMP<br /><br /> SQL_TIMESTAMP|  
|SQL_TYPE_DATE<br /><br /> SQL_DATE|SQL_DATE_STRUCT<br /><br /> DATE_STRUCT|SQL_C_TYPE_DATE<br /><br /> SQL_C_DATE|SQL_TYPE_DATE<br /><br /> SQL_DATE|  
|SQL_SS_TIME2|SQL_SS_TIME2_STRUCT|SQL_C_SS_TIME2<br /><br /> SQL_C_BINARY (ODBC 3.5 以前)|0x4000 (sqlncli.h)<br /><br /> SQL_BINARY (-2)|  
|SQL_SS_TIMESTAMPOFFSET|SQL_SS_TIMESTAMPOFFSET_STRUCT|SQL_C_SS_TIMESTAMPOFFSET<br /><br /> SQL_C_BINARY (ODBC 3.5 以前)|0x4001 (sqlncli.h)<br /><br /> SQL_BINARY (-2)|  
|||||

 SQL_C_BINARY バインドを指定すると、配置チェックが実行され、配置が正しくない場合はエラーが報告されます。 このエラーの SQLSTATE は IM016 で、メッセージは "構造体の配置が正しくありません" です。  
  
## <a name="data-formats-strings-and-literals"></a>データ形式 : 文字列とリテラル  
 次の表に、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] データ型、ODBC データ型、ODBC 文字列リテラルの間のマッピングを示します。  
  
|SQL Server のデータ型|ODBC データ型|クライアントで変換した場合の文字列の形式|  
|--------------------------|--------------------|------------------------------------------|  
|Datetime|SQL_TYPE_TIMESTAMP<br /><br /> SQL_TIMESTAMP|'yyyy-mm-dd hh:mm:ss[.999]'<br /><br /> [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、datetime における秒の小数部の桁数を 3 桁までサポートします。|  
|Smalldatetime|SQL_TYPE_TIMESTAMP<br /><br /> SQL_TIMESTAMP|'yyyy-mm-dd hh:hh:ss'<br /><br /> このデータ型の精度は 1 分です。 秒の部分は、出力時には 0 になり、入力時にはサーバーによって丸められます。|  
|Date|SQL_TYPE_DATE<br /><br /> SQL_DATE|'yyyy-mm-dd'|  
|Time|SQL_SS_TIME2|'hh:mm:ss[.9999999]'<br /><br /> 秒の小数部には、必要に応じて最大 7 桁まで指定できます。|  
|Datetime2|SQL_TYPE_TIMESTAMP<br /><br /> SQL_TIMESTAMP|' yyyy-mm-dd hh: mm: ss [. 9999999] '<br /><br /> 秒の小数部には、必要に応じて最大 7 桁まで指定できます。|  
|DatetimeOFFSET|SQL_SS_TIMESTAMPOFFSET|'yyyy-mm-dd hh:mm:ss[.9999999] +/- hh:mm'<br /><br /> 秒の小数部には、必要に応じて最大 7 桁まで指定できます。|  
||||

 日付リテラルまたは時刻リテラルの ODBC エスケープ シーケンスに変更はありません。  
  
 結果に含まれる秒の小数部には、常に、コロン (:) ではなくドット (.) を使用します。  
  
 アプリケーションに返される文字列値は、常に特定の列に対して同じ長さになります。 年、月、日、時、分、秒の各部分は、最大幅に合わせて先頭にゼロが埋め込まれます。また、datetime 値の日付と時刻の間には空白が 1 つ入ります。 datetimeoffset 値では、時間とタイム ゾーン オフセットの間にも空白が 1 つ入ります。 タイム ゾーン オフセットの前には常に符号を指定します。オフセットが 0 の場合は、正符号 (+) を指定します。 秒の小数部では、必要に応じて、列に定義されている有効桁数になるまで後ろにゼロが埋め込まれます。 datetime 列の場合、秒の小数部は 3 桁になります。 smalldatetime 列の場合、秒の小数部はなく、秒は常にゼロになります。  
  
 空の文字列は、有効な日付リテラルまたは時間リテラルではありません。また、NULL 値を表すものでもありません。 空の文字列を日付または時刻の値に変換しようとすると、SQLState 22018 のエラーが発生し、"キャストした文字コードが正しくありません" というメッセージが表示されます。  
  
 文字列パラメーターからの変換では、文字列が同じ形式である必要があります。ただし、0 時 0 分のタイム ゾーンの符号が正符号と負符号のどちらも指定できる点と、秒の小数部では最大 9 桁になるように末尾にゼロを含めることができる点は同じである必要はありません。 時刻部分は、小数点で終了して、秒の小数部を省略することができます。  
  
 現在は、句読点の前後に空白を追加することができ、時間とタイム ゾーン オフセットの間の空白は省略可能です。 ただし、これは将来のリリースで変更される可能性があるので、アプリケーションは現在の動作に依存しないようにしてください。  
  
## <a name="data-formats-data-structures"></a>データ形式 : データ構造体  
 後述の構造体では、ODBC によって次の制約が定められています。これらはグレゴリオ暦を取り入れたものです。  
  
-   月の範囲は 1 ～ 12 です。  
  
-   日フィールドの範囲は 1 からその月の日数までです。この範囲は、うるう年を考慮して、年フィールドおよび月フィールドと一貫性を保つ必要があります。  
  
-   時刻の範囲は 0 ～ 23 です。  
  
-   分の範囲は 0 ～ 59 です。  
  
-   秒の範囲は 0 ～ 61.9(n) です。 この範囲では、恒星時との同期を維持するために最大 2 秒のうるう秒が許可されています。  
  
     [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] はうるう秒を許可しないので、秒の値が 59 を超えるとサーバー エラーが発生することに注意してください。  
  
 次の既存の ODBC 構造体の実装は、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の新しい日付と時刻のデータ型をサポートするように変更されました。 ただし、定義は変更されていません。  
  
-   DATE_STRUCT  
  
-   TIME_STRUCT  
  
-   TIMESTAMP_STRUCT  
  
 また、次の新しい 2 つの構造体が導入されました。  
  
-   SQL_SS_TIME2_STRUCT  
  
-   SQL_SS_TIMESTAMPOFFSET_STRUCT  
  
### <a name="sql_ss_time2_struct"></a>SQL_SS_TIME2_STRUCT  
 この構造体では、32 ビットと 64 ビットの両方のオペレーティング システムで、12 バイトまで埋め込みが行われます。  
  
```cpp
typedef struct tagSS_TIME2_STRUCT {  
   SQLUSMALLINT hour;  
   SQLUSMALLINT minute;  
   SQLUSMALLINT second;  
   SQLUINTEGER fraction;  
} SQL_SS_TIME2_STRUCT;  
```  
  
### <a name="sql_ss_timestampoffset_struct"></a>SQL_SS_TIMESTAMPOFFSET_STRUCT  
  
```cpp
typedef struct tagSS_TIMESTAMPOFFSET_STRUCT {  
   SQLSMALLINT year;  
   SQLUSMALLINT month;  
   SQLUSMALLINT day;  
   SQLUSMALLINT hour;  
   SQLUSMALLINT minute;  
   SQLUSMALLINT second;  
   SQLUINTEGER fraction;  
   SQLSMALLINT timezone_hour;  
   SQLSMALLINT timezone_minute;  
} SQL_SS_TIMESTAMPOFFSET_STRUCT;  
```  
  
 **Timezone_hour** が負の場合、 **timezone_minute** は負の値または0である必要があります。 **Timezone_hour** が正の値の場合、 **timezone_minute** は正の値または0である必要があります。 **Timezone_hour** が0の場合、 **timezone_minute** は-59 ~ + 59 の範囲の任意の値を持つことができます。  
  
## <a name="see-also"></a>参照  
 [ODBC&#41;&#40;の日付と時刻の改善 ](../../relational-databases/native-client-odbc-date-time/date-and-time-improvements-odbc.md)  
