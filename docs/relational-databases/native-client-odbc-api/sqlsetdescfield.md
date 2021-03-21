---
description: SQLSetDescField
title: SQLSetDescField |Microsoft Docs
ms.custom: ''
ms.date: 03/17/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- SQLSetDescField function
ms.assetid: de4bed15-15be-4825-994c-1046255e725a
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 3751e811455a48b52692583ac42d6ac80a18247c
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104754852"
---
# <a name="sqlsetdescfield"></a>SQLSetDescField
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  SQLSetDescField を使用すると、テーブル値パラメーターおよびテーブル値パラメーター列の記述子フィールドを設定できます。 使用可能なフィールドの詳細については、「 [Table-Valued パラメーター構成列の](../../relational-databases/native-client-odbc-table-valued-parameters/descriptor-fields-for-table-valued-parameter-constituent-columns.md)[テーブル値パラメーター記述子フィールド](../../relational-databases/native-client-odbc-table-valued-parameters/table-valued-parameter-descriptor-fields.md)と記述子フィールド」を参照してください。  
  
## <a name="remarks"></a>解説  
 テーブル値パラメーター列は、記述子のヘッダー フィールド SQL_SOPT_SS_PARAM_FOCUS に、SQL_DESC_TYPE が SQL_SS_TABLE に設定されているレコードの序数が設定される場合のみ使用できます。 SQL_SOPT_SS_PARAM_FOCUS の詳細については、「 [SQLSetStmtAttr](../../relational-databases/native-client-odbc-api/sqlsetstmtattr.md)」を参照してください。  
  
 SQL_SOPT_SS_PARAM_FOCUS をテーブル値パラメーターではないパラメーターの序数に設定しようとした場合、SQLSetStmtAttr は SQL_ERROR を返し、"属性値が無効です" というメッセージで SQLSTATE = HY024 の診断レコードが作成されます。 SQL_SOPT_SS_PARAM_FOCUS は、SQL_ERROR が返されたときに変更されません。  
  
 SQL_SOPT_SS_PARAM_FOCUS に 0 を設定すると、パラメーターの記述子レコードへのアクセスが復元されます。  
  
 テーブル値パラメーターの詳細については、「 [テーブル値パラメーター &#40;ODBC&#41;](../../relational-databases/native-client-odbc-table-valued-parameters/table-valued-parameters-odbc.md)」を参照してください。  
  
## <a name="sqlsetdescfield-support-for-enhanced-date-and-time-features"></a>SQLSetDescField による機能強化された日付と時刻のサポート  
 ODBC では、日付と時刻の機能が強化されました。 新しい日付型または時刻型に対して提供される記述子フィールドの詳細については、「 [パラメーターと結果のメタデータ](../../relational-databases/native-client-odbc-date-time/metadata-parameter-and-result.md)」を参照してください。  
  
 詳細については、「 [日付と時刻の機能強化 &#40;ODBC&#41;](../../relational-databases/native-client-odbc-date-time/date-and-time-improvements-odbc.md)」を参照してください。  
  
## <a name="sqlsetdescfield-support-for-large-clr-udts"></a>SQLSetDescField による大きな CLR UDT のサポート  
 SQLSetDescField は、大きな CLR ユーザー定義型 (Udt) をサポートしています。 詳細については、「 [LARGE CLR User-Defined Types &#40;ODBC&#41;](../../relational-databases/native-client/odbc/large-clr-user-defined-types-odbc.md)」を参照してください。  
  
## <a name="sqlsetdescfield-support-for-sparse-columns"></a>SQLSetDescField によるスパース列のサポート  
 SQLSetDecField を使用して、アプリケーションパラメーター記述子 (APD) の SQL_SOPT_SS_NAME_SCOPE を SQL_SS_NAME_SCOPE_EXTENDED と SQL_SS_NAME_SCOPE_SPARSE_COLUMN_SET の値に設定できます。  
  
 詳細については、「 [スパース列のサポート &#40;ODBC&#41;](../../relational-databases/native-client/odbc/sparse-columns-support-odbc.md)」を参照してください。  
  
## <a name="see-also"></a>参照  
 [SQLSetDescField](../../odbc/reference/syntax/sqlsetdescfield-function.md)   
 [ODBC API 実装の詳細](../../relational-databases/native-client-odbc-api/odbc-api-implementation-details.md)  
  
