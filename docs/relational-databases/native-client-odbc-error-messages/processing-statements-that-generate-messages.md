---
description: メッセージを生成するステートメントの処理
title: Messages | を生成するステートメントの処理Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- PRINT statement
- messages [ODBC], statements generating messages
- statements [ODBC], message generation
- errors [ODBC], statements generating messages
- SQL Server Native Client ODBC driver, errors
- STATISTICS IO option
- STATISTICS TIME option
- DBCC statements
- SQLExecute function
- RAISERROR statement
- SQLGetDiagRec function
- ODBC error handling, statements generating messages
- SQLExecDirect function
ms.assetid: 672ebdc5-7fa1-4ceb-8d52-fd25ef646654
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: fc3ce91857b1ecb650df733958e4715a03893fd5
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104746132"
---
# <a name="processing-statements-that-generate-messages"></a>メッセージを生成するステートメントの処理
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  [!INCLUDE[tsql](../../includes/tsql-md.md)] SET ステートメントのオプション STATISTICS TIME と STATISTICS IO は、実行時間の長いクエリの診断に利用できる情報を取得する場合に使用します。 以前のバージョンの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、クエリ プランを分析するための SHOWPLAN オプションもサポートされています。 ODBC アプリケーションでは、次のステートメントを実行してこれらのオプションを設定できます。  
  
```  
SQLExecDirect(hstmt, "SET SHOWPLAN ON", SQL_NTS);  
SQLExecDirect(hstmt, "SET STATISTICS TIME ON", SQL_NTS90  
);  
SQLExecDirect(hstmt, "SET STATISTICS IO ON", SQL_NTS);  
```  
  
 SET STATISTICS TIME または SET SHOWPLAN を ON に設定すると、 **Sqlexecute** と **SQLExecDirect** return SQL_SUCCESS_WITH_INFO が返されます。その時点で、アプリケーションは SQL_NO_DATA を返すまで **SQLGETDIAGREC** を呼び出して、SHOWPLAN または STATISTICS TIME の出力を取得できます。 SHOWPLAN データの各行は、次の形式で返されます。  
  
```  
szSqlState="01000", *pfNativeError=6223,  
szErrorMsg="[Microsoft][SQL Server Native Client][SQL Server]   
              Table Scan"  
```  
  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Version 7.0 では、SHOWPLAN オプションが SHOWPLAN_ALL オプションと SHOWPLAN_TEXT オプションに置き換えられました。これらの両方のオプションでは、一連のメッセージではなく、結果セットとして出力が返されます。  
  
 STATISTICS TIME の各行は、次の形式で返されます。  
  
```  
szSqlState="01000", *pfNativeError= 3613,  
szErrorMsg="[Microsoft][SQL Server Native Client][SQL Server]  
   SQL Server Parse and Compile Time: cpu time = 0 ms."  
```  
  
 SET STATISTICS IO の出力は、結果セットの最後に到達するまで使用できません。 統計 IO 出力を取得するために、アプリケーションは、 **Sqlfetch** または [sqlfetchscroll](../../relational-databases/native-client-odbc-api/sqlfetchscroll.md)が SQL_NO_DATA を返す時点で **SQLGetDiagRec** を呼び出します。 STATISTICS IO の出力は、次の形式で返されます。  
  
```  
szSqlState="01000", *pfNativeError= 3615,  
szErrorMsg="[Microsoft][ SQL Server Native Client][SQL Server]  
   Table: testshow  scan count 1,  logical reads: 1,  
   physical reads: 0."  
```  
  
## <a name="using-dbcc-statements"></a>DBCC ステートメントの使用  
 DBCC ステートメントは、結果セットではなく、メッセージとしてデータを返します。 **SQLExecDirect** または **sqlexecute** は SQL_SUCCESS_WITH_INFO を返し、SQL_NO_DATA を返すまで、アプリケーションは **SQLGetDiagRec** を呼び出して出力を取得します。  
  
 たとえば、次のステートメントでは SQL_SUCCESS_WITH_INFO が返されます。  
  
```  
SQLExecDirect(hstmt, "DBCC CHECKTABLE(Authors)", SQL_NTS);  
```  
  
 **SQLGetDiagRec** を呼び出すと、次のように戻ります。  
  
```  
szSqlState = "01000", *pfNativeError = 2536,  
szErrorMsg="[Microsoft][ SQL Server Native Client][SQL Server]  
   Checking authors"  
szSqlState = "01000", *pfNativeError = 2579,  
szErrorMsg="[Microsoft][ SQL Server Native Client][SQL Server]  
   The total number of data pages in this table is 1."  
szSqlState = "01000", *pfNativeError = 7929,  
szErrorMsg="[Microsoft][ SQL Server Native Client][SQL Server]  
   Table has 23 data rows."  
szSqlState = "01000", *pfNativeError = 2528  
szErrorMsg="[Microsoft][ SQL Server Native Client][SQL Server]  
   DBCC execution completed. If DBCC printed error messages,  
   see your System Administrator."  
```  
  
## <a name="using-print-and-raiserror-statements"></a>PRINT ステートメントと RAISERROR ステートメントの使用  
 [!INCLUDE[tsql](../../includes/tsql-md.md)] PRINT ステートメントと RAISERROR ステートメントでは、 **SQLGetDiagRec** を呼び出すことによってもデータが返されます。 PRINT ステートメントを実行すると SQL_SUCCESS_WITH_INFO が返され、後続の **SQLGetDiagRec** の呼び出しでは *SQLState* 01000 が返されます。 重大度が 10 以下の RAISERROR の動作は PRINT と同様です。 重大度が11以上の RAISERROR では、execute は SQL_ERROR を返し、後続の **SQLGetDiagRec** の呼び出しでは *SQLState* 42000 が返されます。 たとえば、次のステートメントでは SQL_SUCCESS_WITH_INFO が返されます。  
  
```  
SQLExecDirect (hstmt, "PRINT  'Some message' ", SQL_NTS);  
```  
  
 **SQLGetDiagRec** を呼び出すと返されます。  
  
```  
szSQLState = "01000", *pfNative Error = 0,  
szErrorMsg= "[Microsoft] [SQL Server Native Client][SQL Server]  
   Some message"  
```  
  
 次のステートメントからは、SQL_SUCCESS_WITH_INFO が返されます。  
  
```  
SQLExecDirect (hstmt, "RAISERROR ('Sample error 1.', 10, -1)",  
   SQL_NTS)  
```  
  
 **SQLGetDiagRec** を呼び出すと返されます。  
  
```  
szSQLState = "01000", *pfNative Error = 50000,  
szErrorMsg= "[Microsoft] [SQL Server Native Client][SQL Server]  
   Sample error 1."  
```  
  
 次のステートメントからは、SQL_ERROR が返されます。  
  
```  
SQLExecDirect (hstmt, "RAISERROR ('Sample error 2.', 11, -1)", SQL_NTS)  
```  
  
 **SQLGetDiagRec** を呼び出すと返されます。  
  
```  
szSQLState = "42000", *pfNative Error = 50000,  
szErrorMsg= "[Microsoft] [SQL Server Native Client][SQL Server]  
   Sample error 2."  
```  
  
 PRINT ステートメントまたは RAISERROR ステートメントからの出力が結果セットに含まれる場合、 **SQLGetDiagRec** の呼び出しのタイミングは重要です。 PRINT または RAISERROR 出力を取得する **SQLGetDiagRec** の呼び出しは、SQL_ERROR または SQL_SUCCESS_WITH_INFO を受け取るステートメントの直後に行う必要があります。 この操作は、上記の例のように、1 つの SQL ステートメントのみを実行するときは容易に実行できます。 このような場合、 **SQLExecDirect** または **sqlexecute** を呼び出すと、SQL_ERROR または SQL_SUCCESS_WITH_INFO が返され、 **SQLGetDiagRec** を呼び出すことができます。 SQL ステートメントのバッチの出力を処理するループをコーディングするとき、または [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ストアド プロシージャを実行するときは、1 つの SQL ステートメントのみを実行するときほど簡単ではありません。  
  
 この場合、バッチまたはストアド プロシージャで実行される SELECT ステートメントごとに、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] から結果セットが返されます。 バッチまたはプロシージャに PRINT ステートメントまたは RAISERROR ステートメントが含まれている場合、これらのステートメントの出力は SELECT ステートメントの結果セットと交互に返されます。 バッチまたはプロシージャの最初のステートメントが PRINT または RAISERROR の場合、 **Sqlexecute** または **SQLExecDirect** は SQL_SUCCESS_WITH_INFO または SQL_ERROR を返し、アプリケーションは SQL_NO_DATA を返して印刷情報または raiserror 情報を取得するまで、 **SQLGetDiagRec** を呼び出す必要があります。  
  
 PRINT ステートメントまたは RAISERROR ステートメントが SQL ステートメント (SELECT ステートメントなど) の後にある場合は、 [Sqlmoreresults](../../relational-databases/native-client-odbc-api/sqlmoreresults.md)がエラーを含む結果セットに位置するときに、print または raiserror の情報が返されます。 **Sqlmoreresults** は、メッセージの重大度に応じて SQL_SUCCESS_WITH_INFO または SQL_ERROR を返します。 メッセージは SQL_NO_DATA が返されるまで、 **SQLGetDiagRec** を呼び出すことによって取得されます。  
  
## <a name="see-also"></a>参照  
 [エラーとメッセージの処理](../../relational-databases/native-client-odbc-error-messages/handling-errors-and-messages.md)  
  
  
