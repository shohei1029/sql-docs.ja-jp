---
description: SQLGetStmtAttr
title: SQLGetStmtAttr |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
apitype: DLLExport
helpviewer_keywords:
- SQLGetStmtAttr function
ms.assetid: e64f4f94-eb73-4477-9745-080b6cbdc751
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 9861fdb16e4f4f0d6b72e5bb2b44796ca4a6161b
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104749072"
---
# <a name="sqlgetstmtattr"></a>SQLGetStmtAttr
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]Native CLIENT ODBC ドライバーは、ドライバー固有のステートメント属性を公開するために、SQLGetStmtAttr を拡張します。  
  
 [SQLSetStmtAttr](../../relational-databases/native-client-odbc-api/sqlsetstmtattr.md) には、読み取りと書き込みの両方のステートメント属性が一覧表示されます。 ここでは、読み取り専用のステートメント属性のみを示します。  
  
## <a name="sql_sopt_ss_current_command"></a>SQL_SOPT_SS_CURRENT_COMMAND  
 コマンド バッチの現在のコマンドを公開します。 バッチ内のコマンドの位置を表す整数を返します。 *Valueptr* 値の型は SQLLEN です。  
  
## <a name="sql_sopt_ss_nocount_status"></a>SQL_SOPT_SS_NOCOUNT_STATUS  
 SQL_SOPT_SS_NOCOUNT_STATUS 属性は、NOCOUNT オプションの現在の設定を示し [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。これは、 [SQLRowCount](../../relational-databases/native-client-odbc-api/sqlrowcount.md) が呼び出されたときに、がステートメントの影響を受ける行の数をレポートするかどうかを制御します。 *Valueptr* 値の型は SQLLEN です。  
  
|値|説明|  
|-----------|-----------------|  
|SQL_NC_OFF|NOCOUNT を OFF にします。 SQLRowCount は、影響を受けた行数を返します。|  
|SQL_NC_ON|NOCOUNT を ON にします。 影響を受ける行の数は SQLRowCount によって返されず、戻り値は0です。|  
  
 SQLRowCount が0を返す場合、アプリケーションは SQL_SOPT_SS_NOCOUNT_STATUS をテストする必要があります。 SQL_NC_ON が返された場合、SQLRowCount の値が0の場合は、によって行数が返されなかったことを示し [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。 SQL_NC_OFF が返される場合、NOCOUNT が無効になっており、SQLRowCount から返される値が 0 であると、ステートメントで処理された行がなかったことを示します。  
  
 SQL_SOPT_SS_NOCOUNT_STATUS が SQL_NC_OFF のときは、アプリケーションでは、SQLRowCount の値を表示しないでください。 大きなバッチやストアド プロシージャには、複数の SET NOCOUNT ステートメントが含まれていることがあるので、SQL_SOPT_SS_NOCOUNT_STATUS が一定であると想定することはできません。 このオプションは、SQLRowCount が0を返すたびにテストする必要があります。  
  
## <a name="sql_sopt_ss_querynotification_msgtext"></a>SQL_SOPT_SS_QUERYNOTIFICATION_MSGTEXT  
 クエリ通知要求のメッセージ テキストを返します。  
  
## <a name="sqlgetstmtattr-and-table-valued-parameters"></a>SQLGetStmtAttr とテーブル値パラメーター  
 SQLGetStmtAttr を呼び出すと、テーブル値パラメーターを操作するときに、アプリケーションパラメーター記述子 (APD) の SQL_SOPT_SS_PARAM_FOCUS の値を取得できます。 SQL_SOPT_SS_PARAM_FOCUS の詳細については、「 [SQLSetStmtAttr](../../relational-databases/native-client-odbc-api/sqlsetstmtattr.md)」を参照してください。  
  
 テーブル値パラメーターの詳細については、「 [テーブル値パラメーター &#40;ODBC&#41;](../../relational-databases/native-client-odbc-table-valued-parameters/table-valued-parameters-odbc.md)」を参照してください。  
  
## <a name="see-also"></a>参照  
 [SQLSetStmtAttr 関数](../../odbc/reference/syntax/sqlsetstmtattr-function.md)   
 [ODBC API 実装の詳細](../../relational-databases/native-client-odbc-api/odbc-api-implementation-details.md)  
  
