---
description: テーブル値パラメーター用の ODBC SQL 型
title: Table-Valued Parameters | の ODBC SQL 型Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- SQL_SS_TABLE
ms.assetid: 6725bfb9-5f10-4115-be09-fd9c9f5779ea
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 723177b3e61af5b5734f3c93dce7d6de72b8bb12
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104748097"
---
# <a name="odbc-sql-type-for-table-valued-parameters"></a>テーブル値パラメーター用の ODBC SQL 型
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  テーブル値パラメーターは、新しい ODBC SQL 型である SQL_SS_TABLE でサポートされます。  
  
## <a name="remarks"></a>解説  
 SQL_SS_TABLE は、他の ODBC データ型または [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] データ型に変換できません。  
  
 SQL_SS_TABLE が SQLBindParameter の *ValueType* パラメーターで C データ型として使用されている場合、またはアプリケーションパラメーター記述子 (APD) レコードの SQL_DESC_TYPE を SQL_SS_TABLE に設定しようとした場合、SQL_ERROR が返され、"アプリケーションバッファーの種類が無効です" という内容の診断レコードが生成されます。  
  
 IPD レコードで SQL_DESC_TYPE を SQL_SS_TABLE に設定し、対応するアプリケーション パラメーター記述子レコードが SQL_C_DEFAULT ではない場合、SQL_ERROR が返され、"アプリケーションのバッファー型が無効です" というメッセージで SQLSTATE=HY003 の診断レコードが生成されます。 これは、SQLSetDescField、SQLSetDescRec、または SQLBindParameter の *ParameterType* で発生する可能性があります。  
  
 SQLGetData を呼び出すときに *TargetType* パラメーターが SQL_SS_TABLE 場合、SQL_ERROR が返され、"アプリケーションバッファーの種類が無効です" という SQLSTATE = HY003 の診断レコードが生成されます。  
  
 テーブル値パラメーターの列は、SQL_SS_TABLE 型としてバインドできません。 **SQLBindParameter** が SQL_SS_TABLE に設定された *ParameterType* で呼び出された場合は、SQL_ERROR が返され、"無効な SQL データ型" で SQLSTATE = HY004 の診断レコードが生成されます。 これは、SQLSetDescField および SQLSetDescRec でも発生する可能性があります。  
  
 テーブル値パラメーターの列の値には、パラメーターおよび結果列と同じデータ変換オプションが設定されています。  
  
 [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降では、テーブル値パラメーターは入力パラメーターのみに使用できます。 SQLBindParameter または SQLSetDescField を介して SQL_PARAM_INPUT 以外の値に SQL_DESC_PARAMETER_TYPE を設定しようとすると、SQL_ERROR が返され、SQLSTATE = HY105 のステートメントに診断レコードが追加され、"パラメーターの型が無効です" というメッセージが表示されます。  
  
 テーブル値パラメーターでは、行ごとの既定値はサポートされていないので、テーブル値パラメーターの列は *StrLen_or_IndPtr* で SQL_DEFAULT_PARAM を使用できません。 代わりに、アプリケーションで列の属性 SQL_CA_SS_COL_HAS_DEFAULT_VALUE を 1 に設定できます。 つまり、この列にすべての行の既定値が含まれます。 *StrLen_or_IndPtr* が SQL_DEFAULT_PARAM に設定されている場合、sqlexecute または SQLExecDirect は SQL_ERROR を返し、メッセージ "文字列またはバッファーの長さが無効です" というメッセージで SQLSTATE = HY090 のステートメントに診断レコードが追加されます。  
  
## <a name="see-also"></a>参照  
 [テーブル値パラメーター &#40;ODBC&#41;](../../relational-databases/native-client-odbc-table-valued-parameters/table-valued-parameters-odbc.md)  
  
  
