---
description: ODBC API 実装の詳細
title: ODBC API の実装の詳細 |Microsoft Docs
ms.custom: ''
ms.date: 03/16/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- ODBC, functions
- SQL Server Native Client ODBC driver, SQL Server-specific behaviors
- ODBC, SQL Server-specific behaviors
- functions [ODBC]
ms.assetid: dca92489-f179-4b1f-997c-adcc46aa17a3
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 0f8406775ab4420843a68521fed29a1833be850b
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104751422"
---
# <a name="odbc-api-implementation-details"></a>ODBC API 実装の詳細
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  ODBC (Open Database Connectivity) は、ODBC データ ソース内のデータにアクセスするアプリケーションで使用される、Microsoft Win32 アプリケーション プログラミング インターフェイスです。  
  
 この [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client ODBC ドライバー リファレンスは、すべての ODBC 関数呼び出しを記載したものではありません。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client ODBC ドライバーと共に使用される、ドライバー固有のパラメーターや動作を備えた ODBC 関数だけについて説明しています。  
  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client ODBC ドライバーは ODBC 3.51 仕様に準拠しています。 ODBC 3.51 の総合的なリファレンスについては、 [データアクセスおよびストレージデベロッパーセンター](https://go.microsoft.com/fwlink?linkid=4173)から Microsoft Data ACCESS Components SDK をダウンロードするか、 [odbc プログラマーズリファレンス](../../odbc/reference/odbc-programmer-s-reference.md) をオンラインで参照してください。  
  
## <a name="in-this-section"></a>このセクションの内容  
  
-   [SQLBindCol](../../relational-databases/native-client-odbc-api/sqlbindcol.md)  
  
-   [SQLBindParameter](../../relational-databases/native-client-odbc-api/sqlbindparameter.md)  
  
-   [SQLBrowseConnect](../../relational-databases/native-client-odbc-api/sqlbrowseconnect.md)  
  
-   [SQLCancel](../../relational-databases/native-client-odbc-api/sqlcancel.md)  
  
-   [SQLCloseCursor](../../relational-databases/native-client-odbc-api/sqlclosecursor.md)  
  
-   [SQLColAttribute](../../relational-databases/native-client-odbc-api/sqlcolattribute.md)  
  
-   [SQLColumnPrivileges](../../relational-databases/native-client-odbc-api/sqlcolumnprivileges.md)  
  
-   [SQLColumns](../../relational-databases/native-client-odbc-api/sqlcolumns.md)  
  
-   [SQLConfigDataSource](../../relational-databases/native-client-odbc-api/sqlconfigdatasource.md)  
  
-   [SQLConnect](../../relational-databases/native-client-odbc-api/sqlconnect.md)  
  
-   [SQLDescribeCol](../../relational-databases/native-client-odbc-api/sqldescribecol.md)  
  
-   [SQLDescribeParam](../../relational-databases/native-client-odbc-api/sqldescribeparam.md)  
  
-   [SQLDriverConnect](../../relational-databases/native-client-odbc-api/sqldriverconnect.md)  
  
-   [SQLDrivers](../../relational-databases/native-client-odbc-api/sqldrivers.md)  
  
-   [SQLEndTran](../../relational-databases/native-client-odbc-api/sqlendtran.md)  
  
-   [SQLExecDirect](../../relational-databases/native-client-odbc-api/sqlexecdirect.md)  
  
-   [SQLExecute](../../relational-databases/native-client-odbc-api/sqlexecute.md)  
  
-   [SQLFetch](../../relational-databases/native-client-odbc-api/sqlfetch.md)  
  
-   [SQLFetchScroll](../../relational-databases/native-client-odbc-api/sqlfetchscroll.md)  
  
-   [SQLForeignKeys](../../relational-databases/native-client-odbc-api/sqlforeignkeys.md)  
  
-   [SQLFreeHandle](../../relational-databases/native-client-odbc-api/sqlfreehandle.md)  
  
-   [SQLFreeStmt](../../relational-databases/native-client-odbc-api/sqlfreestmt.md)  
  
-   [SQLGetConnectAttr](../../relational-databases/native-client-odbc-api/sqlgetconnectattr.md)  
  
-   [SQLGetCursorName](../../relational-databases/native-client-odbc-api/sqlgetcursorname.md)  
  
-   [SQLGetData](../../relational-databases/native-client-odbc-api/sqlgetdata.md)  
  
-   [SQLGetDescField](../../relational-databases/native-client-odbc-api/sqlgetdescfield.md)  
  
-   [SQLGetDiagField](../../relational-databases/native-client-odbc-api/sqlgetdiagfield.md)  
  
-   [SQLGetFunctions](../../relational-databases/native-client-odbc-api/sqlgetfunctions.md)  
  
-   [SQLGetInfo](../../relational-databases/native-client-odbc-api/sqlgetinfo.md)  
  
-   [SQLGetStmtAttr](../../relational-databases/native-client-odbc-api/sqlgetstmtattr.md)  
  
-   [SQLGetTypeInfo](../../relational-databases/native-client-odbc-api/sqlgettypeinfo.md)  
  
-   [SQLMoreResults](../../relational-databases/native-client-odbc-api/sqlmoreresults.md)  
  
-   [SQLNativeSql](../../relational-databases/native-client-odbc-api/sqlnativesql.md)  
  
-   [SQLNumParams](../../relational-databases/native-client-odbc-api/sqlnumparams.md)  
  
-   [SQLNumResultCols](../../relational-databases/native-client-odbc-api/sqlnumresultcols.md)  
  
-   [SQLParamData](../../relational-databases/native-client-odbc-api/sqlparamdata.md)  
  
-   [SQLPrimaryKeys](../../relational-databases/native-client-odbc-api/sqlprimarykeys.md)  
  
-   [SQLProcedureColumns](../../relational-databases/native-client-odbc-api/sqlprocedurecolumns.md)  
  
-   [SQLProcedures](../../relational-databases/native-client-odbc-api/sqlprocedures.md)  
  
-   [SQLPutData](../../relational-databases/native-client-odbc-api/sqlputdata.md)  
  
-   [SQLRowCount](../../relational-databases/native-client-odbc-api/sqlrowcount.md)  
  
-   [SQLSetConnectAttr](../../relational-databases/native-client-odbc-api/sqlsetconnectattr.md)  
  
-   [SQLSetDescField](../../relational-databases/native-client-odbc-api/sqlsetdescfield.md)  
  
-   [SQLSetDescRec](../../relational-databases/native-client-odbc-api/sqlsetdescrec.md)  
  
-   [SQLSetEnvAttr](../../relational-databases/native-client-odbc-api/sqlsetenvattr.md)  
  
-   [SQLSetStmtAttr](../../relational-databases/native-client-odbc-api/sqlsetstmtattr.md)  
  
-   [SQLSpecialColumns](../../relational-databases/native-client-odbc-api/sqlspecialcolumns.md)  
  
-   [SQLStatistics](../../relational-databases/native-client-odbc-api/sqlstatistics.md)  
  
-   [SQLTablePrivileges](../../relational-databases/native-client-odbc-api/sqltableprivileges.md)  
  
-   [SQLTables](../../relational-databases/native-client-odbc-api/sqltables.md)  
  
## <a name="see-also"></a>参照  
 [SQL Server Native Client &#40;ODBC&#41; リファレンス](../native-client/odbc/sql-server-native-client-odbc.md)   
 [SQL Server Native Client を使用したアプリケーションのビルド](../../relational-databases/native-client/applications/building-applications-with-sql-server-native-client.md)  
  
