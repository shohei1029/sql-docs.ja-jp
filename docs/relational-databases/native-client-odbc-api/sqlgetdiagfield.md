---
description: SQLGetDiagField
title: SQLGetDiagField |Microsoft Docs
ms.custom: ''
ms.date: 03/16/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
apitype: DLLExport
helpviewer_keywords:
- SQLGetDiagField function
ms.assetid: 395245ba-0372-43ec-b9a4-a29410d85a6d
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 2aa29dda546e709b06b7a87300ffe66e5505ffd5
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104754152"
---
# <a name="sqlgetdiagfield"></a>SQLGetDiagField
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]Native CLIENT ODBC ドライバーでは、 **SQLGetDiagField** 用に次の追加の診断フィールドが指定されています。 これらのフィールドでは、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] アプリケーションに関する各種エラー報告がサポートされます。また、これらのフィールドは、接続されている ODBC 接続ハンドルや ODBC ステートメント ハンドルで生成されるすべての診断レコードで使用できます。 これらのフィールドは、sqlncli.h で定義されています。  
  
|診断レコードのフィールド|説明|  
|------------------------------|-----------------|  
|SQL_DIAG_SS_LINE|ストアド プロシージャのエラーが発生した行番号を報告します。 SQL_DIAG_SS_LINE の値は、SQL_DIAG_SS_PROCNAME に値が返される場合にのみ意味があります。 この値は、16 ビットの符号なし整数で返されます。|  
|SQL_DIAG_SS_MSGSTATE|エラー メッセージの状態。 エラーメッセージの状態の詳細については、「 [RAISERROR](../../t-sql/language-elements/raiserror-transact-sql.md)」を参照してください。 この値は、32 ビットの符号付き整数で返されます。|  
|SQL_DIAG_SS_PROCNAME|エラーが発生したストアド プロシージャの名前 (該当する場合)。 この値は文字列で返されます。 この文字列の長さ (文字数) は、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のバージョンによって異なります。 SQL_MAX_PROCEDURE_NAME_LEN の値を要求する [SQLGetInfo](../../relational-databases/native-client-odbc-api/sqlgetinfo.md) を呼び出すことによって決定できます。|  
|SQL_DIAG_SS_SEVERITY|関連付けられたエラー メッセージの重大度レベル。 この値は、32 ビットの符号付き整数で返されます。|  
|SQL_DIAG_SS_SRVNAME|エラーが発生したサーバーの名前。 この値は文字列で返されます。 この文字列の長さ (文字列) は、sqlncli.h の SQL_MAX_SQLSERVERNAME マクロで定義されます。|  
  
 文字データを含む [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 固有の診断フィールドの SQL_DIAG_SS_PROCNAME と SQL_DIAG_SS_SRVNAME では、NULL で終わる ANSI 文字列または Unicode 文字列としてデータをクライアントに返します。 必要に応じて、文字数を文字幅で調整する必要があります。 また、TCHAR や SQLTCHAR などの移植可能な C データ型を使用して、プログラム変数の適切な長さを保証できます。  
  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client ODBC ドライバーでは、次の補足の動的機能コードが報告されます。この動的機能コードでは、最後に試行された [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ステートメントが特定されます。 動的機能コードは、診断レコード セットのヘッダー (レコード 0) に返されるので、各実行が成功しても失敗しても参照できます。  
  
|動的機能コード|source|  
|---------------------------|------------|  
|SQL_DIAG_DFC_SS_ALTER_DATABASE|ALTER DATABASE ステートメント|  
|SQL_DIAG_DFC_SS_CHECKPOINT|CHECKPOINT ステートメント|  
|SQL_DIAG_DFC_SS_CONDITION|ステートメントの WHERE 句または HAVING 句でエラーが発生|  
|SQL_DIAG_DFC_SS_CREATE_DATABASE|CREATE DATABASE ステートメント|  
|SQL_DIAG_DFC_SS_CREATE_DEFAULT|CREATE DEFAULT ステートメント|  
|SQL_DIAG_DFC_SS_CREATE_PROCEDURE|CREATE PROCEDURE ステートメント|  
|SQL_DIAG_DFC_SS_CREATE_RULE|CREATE RULE ステートメント|  
|SQL_DIAG_DFC_SS_CREATE_TRIGGER|CREATE TRIGGER ステートメント|  
|SQL_DIAG_DFC_SS_CURSOR_DECLARE|DECLARE CURSOR ステートメント|  
|SQL_DIAG_DFC_SS_CURSOR_OPEN|OPEN ステートメント|  
|SQL_DIAG_DFC_SS_CURSOR_FETCH|FETCH ステートメント|  
|SQL_DIAG_DFC_SS_CURSOR_CLOSE|CLOSE ステートメント|  
|SQL_DIAG_DFC_SS_DEALLOCATE_CURSOR|割り当て解除ステートメント|  
|SQL_DIAG_DFC_SS_DBCC|DBCC ステートメント|  
|SQL_DIAG_DFC_SS_DENY|DENY ステートメント|  
|SQL_DIAG_DFC_SS_DROP_DATABASE|DROP DATABASE ステートメント|  
|SQL_DIAG_DFC_SS_DROP_DEFAULT|DROP DEFAULT ステートメント|  
|SQL_DIAG_DFC_SS_DROP_PROCEDURE|DROP PROCEDURE ステートメント|  
|SQL_DIAG_DFC_SS_DROP_RULE|DROP RULE ステートメント|  
|SQL_DIAG_DFC_SS_DROP_TRIGGER|DROP TRIGGER ステートメント|  
|SQL_DIAG_DFC_SS_DUMP_DATABASE|BACKUP DATABASE ステートメントまたは DUMP DATABASE ステートメント|  
|SQL_DIAG_DFC_SS_DUMP_TABLE|DUMP TABLE ステートメント|  
|SQL_DIAG_DFC_SS_DUMP_TRANSACTION|BACKUP TRANSACTION ステートメントまたは DUMP TRANSACTION ステートメント。 **Chkpt. 上の trunc** の場合、CHECKPOINT ステートメントにも返されます。 データベースオプションは on です。|  
|SQL_DIAG_DFC_SS_GOTO|GOTO 流れ制御ステートメント|  
|SQL_DIAG_DFC_SS_INSERT_BULK|INSERT BULK ステートメント|  
|SQL_DIAG_DFC_SS_KILL|KILL ステートメント|  
|SQL_DIAG_DFC_SS_LOAD_DATABASE|LOAD DATABASE ステートメントまたは RESTORE DATABASE ステートメント|  
|SQL_DIAG_DFC_SS_LOAD_HEADERONLY|LOAD HEADERONLY ステートメントまたは RESTORE HEADERONLY ステートメント|  
|SQL_DIAG_DFC_SS_LOAD_TABLE|LOAD TABLE ステートメント|  
|SQL_DIAG_DFC_SS_LOAD_TRANSACTION|LOAD TRANSACTION ステートメントまたは RESTORE TRANSACTION ステートメント|  
|SQL_DIAG_DFC_SS_PRINT|PRINT ステートメント|  
|SQL_DIAG_DFC_SS_RAISERROR|RAISERROR ステートメント|  
|SQL_DIAG_DFC_SS_READTEXT|READTEXT ステートメント|  
|SQL_DIAG_DFC_SS_RECONFIGURE|RECONFIGURE ステートメント|  
|SQL_DIAG_DFC_SS_RETURN|RETURN 流れ制御ステートメント|  
|SQL_DIAG_DFC_SS_SELECT_INTO|SELECT INTO ステートメント|  
|SQL_DIAG_DFC_SS_SET|SET ステートメント (すべての一般的なオプション)|  
|SQL_DIAG_DFC_SS_SET_IDENTITY_INSERT|SET IDENTITY_INSERT ステートメント|  
|SQL_DIAG_DFC_SS_SET_ROW_COUNT|SET ROWCOUNT ステートメント|  
|SQL_DIAG_DFC_SS_SET_STATISTICS|SET STATISTICS IO ステートメントまたは SET STATISTICS TIME ステートメント|  
|SQL_DIAG_DFC_SS_SET_TEXTSIZE|SET TEXTSIZE ステートメント|  
|SQL_DIAG_DFC_SS_SETUSER|SETUSER ステートメント|  
|SQL_DIAG_DFC_SS_SET_XCTLVL|SET TRANSACTION 分離レベルステートメント|  
|SQL_DIAG_DFC_SS_SHUTDOWN|SHUTDOWN ステートメント|  
|SQL_DIAG_DFC_SS_TRANS_BEGIN|BEGIN TRAN ステートメント|  
|SQL_DIAG_DFC_SS_TRANS_COMMIT|COMMIT TRAN ステートメント|  
|SQL_DIAG_DFC_SS_TRANS_PREPARE|分散トランザクションをコミットするための準備ステートメント|  
|SQL_DIAG_DFC_SS_TRANS_ROLLBACK|ROLLBACK TRAN ステートメント|  
|SQL_DIAG_DFC_SS_TRANS_SAVE|SAVE TRAN ステートメント|  
|SQL_DIAG_DFC_SS_TRUNCATE_TABLE|TRUNCATE TABLE ステートメント|  
|SQL_DIAG_DFC_SS_UPDATE_STATISTICS|UPDATE STATISTICS ステートメント|  
|SQL_DIAG_DFC_SS_UPDATETEXT|UPDATETEXT ステートメント|  
|SQL_DIAG_DFC_SS_USE|USE ステートメント|  
|SQL_DIAG_DFC_SS_WAITFOR|WAITFOR 流れ制御ステートメント|  
|SQL_DIAG_DFC_SS_WRITETEXT|WRITETEXT ステートメント|  
  
## <a name="sqlgetdiagfield-and-table-valued-parameters"></a>SQLGetDiagField とテーブル値パラメーター  
 SQLGetDiagField は、SQL_DIAG_SS_TABLE_COLUMN_NUMBER と SQL_DIAG_SS_TABLE_ROW_NUMBER の2つの診断フィールドを取得するために使用できます。 これらのフィールドは、診断レコードに関連するエラーまたは警告の原因となった値を特定するのに役立ちます。  
  
 テーブル値パラメーターの詳細については、「 [テーブル値パラメーター &#40;ODBC&#41;](../../relational-databases/native-client-odbc-table-valued-parameters/table-valued-parameters-odbc.md)」を参照してください。  
  
## <a name="see-also"></a>参照  
 [SQLGetDiagField 関数](../../odbc/reference/syntax/sqlgetdiagfield-function.md)   
 [ODBC API 実装の詳細](../../relational-databases/native-client-odbc-api/odbc-api-implementation-details.md)  
  
