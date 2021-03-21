---
description: SQLGetDescRec
title: SQLGetDescRec |Microsoft Docs
ms.custom: ''
ms.date: 03/17/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- SQLGetDescRec function
ms.assetid: f3389ff2-f3be-4035-9fb5-c9ebc2f15025
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: af0c3140515ded17b1b7af6775c476a5a77246ce
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104749092"
---
# <a name="sqlgetdescrec"></a>SQLGetDescRec
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  このトピックでは、Native Client に固有の SQLGetDescRec 機能について説明 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] します。  
  
## <a name="sqlgetdescrec-and-table-valued-parameters"></a>SQLGetDescRec とテーブル値パラメーター  
 SQLGetDescRec を使用すると、テーブル値パラメーターおよびテーブル値パラメーター列の属性の値を取得できます。 SQLGetDescRec の *Recnumber* パラメーターは、SQLBindParameter の *parameternumber* パラメーターに対応します。  
  
 テーブル値パラメーター列は、記述子のヘッダー フィールド SQL_SOPT_SS_PARAM_FOCUS に、SQL_DESC_TYPE が SQL_SS_TABLE に設定されているレコードの序数が設定される場合のみ使用できます。 の SQL_SOPT_SS_PARAM_FOCUS の詳細については、「 [SQLSetStmtAttr](../../relational-databases/native-client-odbc-api/sqlsetstmtattr.md)」を参照してください。  
  
 SQLGetDescRec は、次のデータを返します。  
  
|パラメーター|テーブル値パラメーター|テーブル値パラメーター列とその他のパラメーター|  
|---------------|-----------------------------|----------------------------------------------------------|  
|*名前*|ストアド プロシージャ呼び出しの場合は仮パラメーター名。それ以外の場合は長さが 0 の文字列。|テーブル値パラメーター列の名前。|  
|*TypePtr*|SQL_DESC_TYPE。 テーブル値パラメーターの場合、これは SQL_SS_TABLE です。|SQL_DESC_TYPE|  
|*SubTypePtr*|未定義。|SQL_DESC_DATETIME_INTERVAL_CODE (SQL_DATETIME 型または SQL_INTERVAL 型のレコードの場合)。|  
|*Length Ptr*|0|SQL_DESC_OCTET_LENGTH|  
|*PrecisionPtr*|0|SQL_DESC_PRECISION|  
|*ScalePtr*|0|SQL_DESC_SCALE|  
|*NullablePtr*|1|SQL_DESC_NULLABLE|  
  
 テーブル値パラメーターの詳細については、「 [テーブル値パラメーター &#40;ODBC&#41;](../../relational-databases/native-client-odbc-table-valued-parameters/table-valued-parameters-odbc.md)」を参照してください。  
  
## <a name="sqlgetdescrec-support-for-enhanced-date-and-time-features"></a>SQLGetDescRec による機能強化された日付と時刻のサポート  
 日付型または時刻型に対して返される値を次に示します。  
  
| 属性 | *TypePtr* | *SubTypePtr* | *Length Ptr* | *PrecisionPtr* | *ScalePtr* |  
| --------- | --------- | ------------ | ----------- | -------------- | ---------- |  
|DATETIME|SQL_DATETIME|SQL_CODE_TIMESTAMP|4|3|3|  
|smalldatetime|SQL_DATETIME|SQL_CODE_TIMESTAMP|8|0|0|  
|date|SQL_DATETIME|SQL_CODE_DATE|6|0|0|  
|time|SQL_SS_TIME2|0|10|0..7|0..7|  
|datetime2|SQL_DATETIME|SQL_CODE_TIMESTAMP|16|0..7|0..7|  
|datetimeoffset|SQL_SS_TIMESTAMPOFFSET|0|20|0..7|0..7|  
  
 詳細については、「 [日付と時刻の機能強化 &#40;ODBC&#41;](../../relational-databases/native-client-odbc-date-time/date-and-time-improvements-odbc.md)」を参照してください。  
  
## <a name="sqlgetdescrec-support-for-large-clr-udts"></a>SQLGetDescRec による大きな CLR UDT のサポート  
 **Sqlgetdescrec** は、大きな CLR ユーザー定義型 (udt) をサポートしています。 詳細については、「 [LARGE CLR User-Defined Types &#40;ODBC&#41;](../../relational-databases/native-client/odbc/large-clr-user-defined-types-odbc.md)」を参照してください。  
  
## <a name="see-also"></a>参照  
 [SQLGetDescRec](../../odbc/reference/syntax/sqlgetdescrec-function.md)   
 [ODBC API 実装の詳細](../../relational-databases/native-client-odbc-api/odbc-api-implementation-details.md)  
  
