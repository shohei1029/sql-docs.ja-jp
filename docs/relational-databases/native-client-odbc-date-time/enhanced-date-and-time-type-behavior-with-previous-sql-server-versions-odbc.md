---
description: 以前のバージョンの SQL Server における、強化された日付型と時刻型の動作 (ODBC)
title: SQL バージョンの日付時刻 (ODBC)
ms.custom: ''
ms.date: 12/18/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- date/time [ODBC], enhanced behavior with earlier SQL Server versions
ms.assetid: cd4e137f-dc5e-4df7-bc95-51fe18c587e0
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: c7312c0cbee0edd2790713755334b3c6b227dcc9
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104756092"
---
# <a name="enhanced-date-and-time-type-behavior-with-previous-sql-server-versions-odbc"></a>以前のバージョンの SQL Server における、強化された日付型と時刻型の動作 (ODBC)
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  このトピックでは、強化された日付や時刻の機能を使用するクライアント アプリケーションが [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] より前のバージョンの [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] と通信する場合、および Microsoft Data Access Components、Windows Data Access Components、または [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] より前のバージョンの [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] Native Client を使用しているクライアント アプリケーションから、機能強化された日付や時刻をサポートするサーバーへコマンドを送信する場合に想定される動作について説明します。  
  
## <a name="down-level-client-behavior"></a>下位クライアントの動作  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] より前のバージョンの [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] Native Client を使用してコンパイルされたクライアント アプリケーションでは、新しい日付型と時刻型が nvarchar 列と見なされます。 列の内容は、「データ形式: 文字列とリテラル」で説明されているリテラル表現です。 [ODBC の日付と時刻の機能強化に関するデータ型のサポートに関する](../../relational-databases/native-client-odbc-date-time/data-type-support-for-odbc-date-and-time-improvements.md)セクションで説明しています。 列のサイズは、列に指定された秒の小数部の有効桁数に対するリテラルの最大長です。  
  
 カタログ API によって、クライアントに返される下位データ型のコード (nvarchar など) および関連する下位の表現 (適切なリテラル形式など) と一貫性のあるメタデータが返されます。 ただし、返されるデータ型名は、実際の [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] の型名です。  
  
 SQLDescribeCol、SQLDescribeParam、SQGetDescField、SQLColAttribute によって返されるステートメントメタデータは、型名を含め、すべての点でダウンレベルの型と一貫性のあるメタデータを返します。 このようなダウンレベルの型の例として、 **nvarchar** が挙げられます。  
  
 [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)]日付型または時刻型へのスキーマ変更が行われた (以降の) サーバーに対して下位クライアントアプリケーションを実行する場合、想定される動作は次のようになります。  
  
|SQL Server 2005 の型|[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降の型|ODBC クライアントの型|結果の変換 (SQL から C へ)|パラメーターの変換 (C から SQL へ)|  
|--------------------------|----------------------------------------------|----------------------|------------------------------------|---------------------------------------|  
|Datetime|Date|SQL_C_TYPE_DATE|OK|OK (1)|  
|||SQL_C_TYPE_TIMESTAMP|時刻フィールドは 0 に設定されます。|OK (2)<br /><br /> 時刻フィールドが 0 以外の場合は失敗します。 [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] で機能します。|  
||Time(0)|SQL_C_TYPE_TIME|OK|OK (1)|  
|||SQL_C_TYPE_TIMESTAMP|日付フィールドは現在の日付に設定されます。|OK (2)<br /><br /> 日付は無視されます。 秒の小数部が0以外の場合に失敗します。 [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] で機能します。|  
||Time(7)|SQL_C_TIME|失敗しました-時刻リテラルが無効です。|OK (1)|  
|||SQL_C_TYPE_TIMESTAMP|失敗しました-時刻リテラルが無効です。|OK (1)|  
||Datetime2 (3)|SQL_C_TYPE_TIMESTAMP|OK|OK (1)|  
||Datetime2 (7)|SQL_C_TYPE_TIMESTAMP|OK|クライアントでの変換により、値は 1/300 秒単位に丸められます。|  
|Smalldatetime|Date|SQL_C_TYPE_DATE|OK|OK|  
|||SQL_C_TYPE_TIMESTAMP|時刻フィールドは 0 に設定されます。|OK (2)<br /><br /> 時刻フィールドが 0 以外の場合は失敗します。 [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] で機能します。|  
||Time(0)|SQL_C_TYPE_TIME|OK|OK|  
|||SQL_C_TYPE_TIMESTAMP|日付フィールドは現在の日付に設定されます。|OK (2)<br /><br /> 日付は無視されます。 秒の小数部が 0 以外の場合は失敗します。<br /><br /> [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] で機能します。|  
||Datetime2(0)|SQL_C_TYPE_TIMESTAMP|OK|OK|  
|||||

## <a name="key-to-symbols"></a>記号の説明  
  
|Symbol|意味|  
|------------|-------------|  
|1|[!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] で機能した場合には、新しいバージョンの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] でも引き続き機能します。|  
|2|[!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] で機能したアプリケーションが、新しいバージョンの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では機能しない可能性があります。|  
|||

 一般的なスキーマ変更のみが考慮されていることに注意してください。 一般的な変更は次のとおりです。  
  
-   論理的にアプリケーションで日付値または時刻値のみが必要な場合に、新しい型を使用します。 ただし、個別の日付型および時刻型がないため、アプリケーションでは datetime または smalldatetime の使用が強制されました。  
  
-   追加の秒の小数部の有効桁数または精度を取得するために、新しい型を使用します。  
  
-   日付と時刻に推奨されるデータ型なので datetime2 に切り替えます。  
  
### <a name="column-metadata-returned-by-sqlcolumns-sqlprocedurecolumns-and-sqlspecialcolumns"></a>SQLColumns、SQLProcedureColumns、および SQLSpecialColumns から返される列のメタデータ  
 日付型または時刻型に対して返される列の値を次に示します。  
  
|列の型|date|time|smalldatetime|DATETIME|datetime2|datetimeoffset|  
|-----------------|----------|----------|-------------------|--------------|---------------|--------------------|  
|DATA_TYPE|SQL_WVARCHAR|SQL_WVARCHAR|SQL_TYPE_TIMESTAMP|SQL_TYPE_TIMESTAMP|SQL_WVARCHAR|SQL_WVARCHAR|  
|TYPE_NAME|date|time|smalldatetime|DATETIME|datetime2|datetimeoffset|  
|COLUMN_SIZE|10|8、10.. 16|16|23|19、21..27|26、28..34|  
|BUFFER_LENGTH|20|16、20.. 32|16|16|38、42.. 54|52、56.. 68|  
|DECIMAL_DIGITS|NULL|NULL|0|3|NULL|NULL|  
|SQL_DATA_TYPE|SQL_WVARCHAR|SQL_WVARCHAR|SQL_DATETIME|SQL_DATETIME|SQL_WVARCHAR|SQL_WVARCHAR|  
|SQL_DATETIME_SUB|NULL|NULL|SQL_CODE_TIMESTAMP|SQL_CODE_TIMESTAMP|NULL|NULL|  
|CHAR_OCTET_LENGTH|NULL|NULL|NULL|NULL|NULL|NULL|  
|SS_DATA_TYPE|0|0|111|111|0|0|  
||||||||

 SQLSpecialColumns は、SQL_DATA_TYPE、SQL_DATETIME_SUB、CHAR_OCTET_LENGTH、または SS_DATA_TYPE を返しません。  
  
### <a name="data-type-metadata-returned-by-sqlgettypeinfo"></a>SQLGetTypeInfo から返されるデータ型のメタデータ  
 日付型または時刻型に対して返される列の値を次に示します。  
  
|列の型|date|time|smalldatetime|DATETIME|datetime2|datetimeoffset|  
|-----------------|----------|----------|-------------------|--------------|---------------|--------------------|  
|TYPE_NAME|date|time|smalldatetime|DATETIME|datetime2|datetimeoffset|  
|DATA_TYPE|SQL_WVARCHAR|SQL_WVARCHAR|SQL_TYPE_TIMESTAMP|SQL_TYPE_TIMESTAMP|SQL_WVARCHAR|SQL_WVARCHAR|  
|COLUMN_SIZE|10|16|16|23|27|34|  
|LITERAL_PREFIX|'|'|'|'|'|'|  
|LITERAL_SUFFIX|'|'|'|'|'|'|  
|CREATE_PARAMS|NULL|NULL|NULL|NULL|NULL|NULL|  
|NULLABLE|SQL_NULLABLE|SQL_NULLABLE|SQL_NULLABLE|SQL_NULLABLE|SQL_NULLABLE|SQL_NULLABLE|  
|CASE_SENSITIVE|SQL_FALSE|SQL_FALSE|SQL_FALSE|SQL_FALSE|SQL_FALSE|SQL_FALSE|  
|検索可能|SQL_PRED_SEARCHABLE|SQL_PRED_SEARCHABLE|SQL_PRED_SEARCHABLE|SQL_PRED_SEARCHABLE|SQL_PRED_SEARCHABLE|SQL_PRED_SEARCHABLE|  
|UNSIGNED_ATTRIBUTE|NULL|NULL|NULL|NULL|NULL|NULL|  
|FXED_PREC_SCALE|SQL_FALSE|SQL_FALSE|SQL_FALSE|SQL_FALSE|SQL_FALSE|SQL_FALSE|  
|AUTO_UNIQUE_VALUE|NULL|NULL|NULL|NULL|NULL|NULL|  
|LOCAL_TYPE_NAME|date|time|smalldatetime|DATETIME|datetime2|datetimeoffset|  
|MINIMUM_SCALE|NULL|NULL|0|3|NULL|NULL|  
|MAXIMUM_SCALE|NULL|NULL|0|3|NULL|NULL|  
|SQL_DATA_TYPE|SQL_WVARCHAR|SQL_WVARCHAR|SQL_DATETIME|SQL_DATETIME|SQL_WVARCHAR|SQL_WVARCHAR|  
|SQL_DATETIME_SUB|NULL|NULL|SQL_CODE_TIMESTAMP|SQL_CODE_TIMESTAMP|NULL|NULL|  
|NUM_PREC_RADIX|NULL|NULL|NULL|NULL|NULL|NULL|  
|INTERVAL_PRECISION|NULL|NULL|NULL|NULL|NULL|NULL|  
|USERTYPE|0|0|12|22|0|0|  
||||||||

## <a name="down-level-server-behavior"></a>下位サーバーの動作  
 [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] より前のバージョンのサーバー インスタンスに接続している場合、新しいサーバーの型または関連するメタデータ コードおよび記述子フィールドを使用しようとすると、SQL_ERROR が返されます。 "接続しているサーバーのバージョンに対して、SQL データ型が無効です" というメッセージで SQLSTATE HY004 の診断レコード、または、"データ型の属性に関する制限に違反しました" というメッセージで SQLSTATE 07006 の診断レコードが生成されます。  
  
## <a name="see-also"></a>参照  
 [ODBC&#41;&#40;の日付と時刻の改善 ](../../relational-databases/native-client-odbc-date-time/date-and-time-improvements-odbc.md)  
