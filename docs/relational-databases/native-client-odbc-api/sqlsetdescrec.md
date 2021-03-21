---
description: SQLSetDescRec
title: SQLSetDescRec |Microsoft Docs
ms.custom: ''
ms.date: 03/17/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- SQLSetDescRec function
ms.assetid: 203d02a2-aa09-462b-a489-a2cdd6f6023b
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: f4745fedbcba60d449d22c84e0e2a07b6728eeb2
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104754882"
---
# <a name="sqlsetdescrec"></a>SQLSetDescRec
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  このトピックでは、Native Client に固有の SQLSetDescRec 機能について説明 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] します。  
  
## <a name="sqlsetdescrec-and-table-valued-parameters"></a>SQLSetDescRec とテーブル値パラメーター  
 SQLSetDescRec を使用すると、テーブル値パラメーターおよびテーブル値パラメーター列の記述子フィールドを設定できます。 テーブル値パラメーター列は、記述子のヘッダー フィールド SQL_SOPT_SS_PARAM_FOCUS に、SQL_DESC_TYPE が SQL_SS_TABLE に設定されているレコードの序数が設定される場合のみ使用できます。 SQL_SOPT_SS_PARAM_FOCUS の詳細については、「 [SQLSetStmtAttr](../../relational-databases/native-client-odbc-api/sqlsetstmtattr.md)」を参照してください。  
  
 次の表では、パラメーターと記述子フィールド間のマッピングについて説明します。  
  
|パラメーター|テーブル値パラメーター以外のパラメーター型の関連属性 (テーブル値パラメーター列を含む)|テーブル値パラメーターに関連する属性|  
|---------------|--------------------------------------------------------------------------------------------------------|----------------------------------------------------|  
|*Type*|SQL_DESC_TYPE|SQL_SS_TABLE|  
|*サブタイプ*|無視|SQL_DATETIME 型または SQL_INTERVAL 型のレコードの場合は、これに SQL_DESC_DATETIME_INTERVAL_CODE を設定します。|  
|*Length*|SQL_DESC_OCTET_LENGTH|テーブル値パラメーターの型名の長さ。 型名が null で終了した場合は SQL_NTS、テーブル値パラメーターの型名が不要な場合は0になります。|  
|*[精度]*|SQL_DESC_PRECISION|SQL_DESC_ARRAY_SIZE|  
|*スケール*|SQL_DESC_SCALE|未使用。 このパラメーターは 0 にする必要があります。|  
|*DataPtr*|APD の SQL_DESC_DATA_PTR|SQL_CA_SS_TYPE_NAME<br /><br /> このパラメーターは、ストアド プロシージャの呼び出しでは省略可能で、不要の場合は NULL を指定できます。 このパラメーターは、プロシージャの呼び出し以外の SQL ステートメント用に指定する必要があります。<br /><br /> *DataPtr* は、変数の行バインドを使用するときに、アプリケーションがこのテーブル値パラメーターを識別するために使用できる一意の値としても機能します。|  
|*Stringlength Ptr*|SQL_DESC_OCTET_LENGTH_PTR|SQL_DESC_OCTET_LENGTH_PTR<br /><br /> テーブル値パラメーターの場合、これは転送する行数または SQL_DATA_AT_EXEC です。 これは、SQLExecDirect で転送する行の数を保持する値へのポインターです。|  
|*IndicatorPtr*|SQL_DESC_INDICATOR_PTR|SQL_DESC_INDICATOR_PTR|  
  
 テーブル値パラメーターの詳細については、「 [テーブル値パラメーター &#40;ODBC&#41;](../../relational-databases/native-client-odbc-table-valued-parameters/table-valued-parameters-odbc.md)」を参照してください。  
  
## <a name="sqlsetdescrec-support-for-enhanced-date-and-time-features"></a>SQLSetDescRec による機能強化された日付と時刻のサポート  
 日付型または時刻型に対して許可される値を次に示します。  
  
| 属性 | *Type* | *サブタイプ* | *Length* | *[精度]* | *スケール* |
| --------- | ------ | --------- | -------- | ----------- | ------- |
|DATETIME|SQL_DATETIME|SQL_CODE_TIMESTAMP|4|3|3|  
|smalldatetime|SQL_SQL_DATETIME|SQL_CODE_TIMESTAMP|8|0|0|  
|date|SQL_DATETIME|SQL_CODE_DATE|6|0|0|  
|time|SQL_SS_TIME2|0|10|0..7|0..7|  
|datetime2|SQL_DATETIME|SQL_CODE_TIMESTAMP|16|0..7|0..7|  
|datetimeoffset|SQL_SS_TIMESTAMPOFFSET|0|20|0..7|0..7|  
  
 詳細については、「 [日付と時刻の機能強化 &#40;ODBC&#41;](../../relational-databases/native-client-odbc-date-time/date-and-time-improvements-odbc.md)」を参照してください。  
  
## <a name="sqlsetdescrec-support-for-large-clr-udts"></a>SQLSetDescRec による大きな CLR UDT のサポート  
 **SQLSetDescRec** は、大きな CLR ユーザー定義型 (udt) をサポートしています。 詳細については、「 [LARGE CLR User-Defined Types &#40;ODBC&#41;](../../relational-databases/native-client/odbc/large-clr-user-defined-types-odbc.md)」を参照してください。  
  
## <a name="see-also"></a>参照  
 [SQLSetDescRec](../../odbc/reference/syntax/sqlsetdescrec-function.md)   
 [ODBC API 実装の詳細](../../relational-databases/native-client-odbc-api/odbc-api-implementation-details.md)  
  
