---
description: ドライバーのパフォーマンス データのプロファイル (ODBC)
title: プロファイルドライバーのパフォーマンスデータ (ODBC) |Microsoft Docs
ms.custom: ''
ms.date: 03/17/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- driver performance data [ODBC]
ms.assetid: b997790a-8cc6-4800-8867-74c1bef07be3
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: f833b5fcf0d1a1d592537bc246d86b6cfa4650ff
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104751872"
---
# <a name="profiling-odbc-driver-performance-data"></a>ODBC ドライバーのパフォーマンスのプロファイル
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  このサンプルでは、パフォーマンス統計を記録するための [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ODBC ドライバー固有のオプションを示します。 このサンプルでは、1 つのファイル (odbcperf.log) を作成します。このサンプルを通じて、パフォーマンス データのログ ファイルの作成と、SQLPERF データ構造体からのパフォーマンス データの直接表示の例を確認できます (SQLPERF 構造体は Odbcss.h で定義されます)。 このサンプルは、ODBC 3.0 以降のバージョン用に開発されました。  
  
> [!IMPORTANT]  
>  可能な場合は、Windows 認証を使用します。 Windows 認証が使用できない場合は、実行時に資格情報を入力するようユーザーに求めます。 資格情報をファイルに保存するのは避けてください。 資格情報を保持する必要がある場合は、[Win32 Crypto API](/windows/win32/seccrypto/cryptography-reference) を使用して暗号化してください。  
  
### <a name="to-log-driver-performance-data-using-odbc-administrator"></a>ODBC アドミニストレーターを使用してドライバーのパフォーマンス データをログに記録するには  
  
1.  **コントロールパネル** で、[**管理ツール**] をダブルクリックし、[**データソース (ODBC)**] をダブルクリックします。 または、odbcad32.exe を呼び出すことができます。  
  
2.  [ **ユーザー dsn**]、[ **システム dsn**]、または [ **ファイル dsn** ] タブをクリックします。  
  
3.  パフォーマンスのログを記録するデータ ソースをクリックします。  
  
4.  **[構成]** をクリックします。  
  
5.  Microsoft SQL Server DSN の構成ウィザードで、ログ **ファイルに ODBC ドライバーの統計情報が記録** されたページに移動します。  
  
6.  [ **ODBC ドライバーの統計情報をログファイルに記録する**] を選択します。 ボックスに、統計をログに記録するファイルの名前を入力します。 必要に応じて、[ **参照** ] をクリックして、ファイルシステムで統計ログを参照します。  
  
### <a name="to-log-driver-performance-data-programmatically"></a>ドライバーのパフォーマンス データをプログラムを使用してログに記録するには  
  
1.  SQL_COPT_SS_PERF_DATA_LOG と、パフォーマンスデータログファイルの完全なパスとファイル名を使用して [SQLSetConnectAttr](../../relational-databases/native-client-odbc-api/sqlsetconnectattr.md) を呼び出します。 次に例を示します。  
  
    ```  
    "C:\\Odbcperf.log"  
    ```  
  
2.  SQL_COPT_SS_PERF_DATA と SQL_PERF_START を使用して [SQLSetConnectAttr](../../relational-databases/native-client-odbc-api/sqlsetconnectattr.md) を呼び出し、パフォーマンスデータのログ記録を開始します。  
  
3.  必要に応じて、SQL_COPT_SS_LOG_NOW および NULL を使用して [SQLSetConnectAttr](../../relational-databases/native-client-odbc-api/sqlsetconnectattr.md) を呼び出し、パフォーマンスデータのタブ区切りのレコードをパフォーマンスデータログファイルに書き込みます。 これは、アプリケーションの実行時に複数回実行できます。  
  
4.  SQL_COPT_SS_PERF_DATA と SQL_PERF_STOP を使用して [SQLSetConnectAttr](../../relational-databases/native-client-odbc-api/sqlsetconnectattr.md) を呼び出し、パフォーマンスデータのログ記録を停止します。  
  
### <a name="to-pull-driver-performance-data-into-an-application"></a>ドライバーのパフォーマンス データをアプリケーションにプルするには  
  
1.  SQL_COPT_SS_PERF_DATA と SQL_PERF_START を使用して [SQLSetConnectAttr](../../relational-databases/native-client-odbc-api/sqlsetconnectattr.md) を呼び出し、パフォーマンスデータのプロファイルを開始します。  
  
2.  SQL_COPT_SS_PERF_DATA と SQLPERF 構造体へのポインターのアドレスを使用して、 [Sqlgetconnectattr](../../relational-databases/native-client-odbc-api/sqlgetconnectattr.md) を呼び出します。 最初の呼び出しでは、現在のパフォーマンス データを含む有効な SQLPERF 構造体のアドレスを指すポインターが設定されます。 ドライバーは、パフォーマンス構造体内のデータを継続的に更新しません。 アプリケーションでは、現在のパフォーマンスデータを使用して構造を更新する必要があるときはいつでも、 [Sqlgetconnectattr](../../relational-databases/native-client-odbc-api/sqlgetconnectattr.md) への呼び出しを繰り返す必要があります。  
  
3.  SQL_COPT_SS_PERF_DATA と SQL_PERF_STOP を使用して [SQLSetConnectAttr](../../relational-databases/native-client-odbc-api/sqlsetconnectattr.md) を呼び出し、パフォーマンスデータのログ記録を停止します。  
  
## <a name="example"></a>例  
 AdventureWorks と呼ばれる ODBC データ ソース (既定のデータベースは AdventureWorks サンプル データベース) が必要です  (AdventureWorks サンプルデータベースは、 [Microsoft SQL Server のサンプルとコミュニティのプロジェクト](https://go.microsoft.com/fwlink/?LinkID=85384) のホームページからダウンロードできます)。このデータソースは、オペレーティングシステムによって提供される ODBC ドライバーに基づいている必要があります (ドライバー名は "SQL Server")。 このサンプルを 64 ビット オペレーティング システムで 32 ビット アプリケーションとしてビルドし、実行する場合、%windir%\SysWOW64\odbcad32.exe の ODBC アドミニストレーターを使用して ODBC データ ソースを作成する必要があります。  
  
 このサンプルでは、コンピューターの既定の [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンスに接続します。 名前付きインスタンスに接続するには、ODBC データ ソースの定義を変更し、server\namedinstance 形式でそのインスタンスを指定します。 [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] は、既定で名前付きインスタンスとしてインストールされます。  
  
 odbc32.lib を使用してコンパイルします。  
  
```  
// compile with: odbc32.lib  
#include <stdio.h>  
#include <string.h>  
#include <windows.h>  
#include <sql.h>  
#include <sqlext.h>  
#include <odbcss.h>  
  
SQLHENV henv = SQL_NULL_HENV;  
SQLHDBC hdbc1 = SQL_NULL_HDBC;       
SQLHSTMT hstmt1 = SQL_NULL_HSTMT;  
  
void Cleanup() {  
   if (hstmt1 != SQL_NULL_HSTMT)  
      SQLFreeHandle(SQL_HANDLE_STMT, hstmt1);  
  
   if (hdbc1 != SQL_NULL_HDBC) {  
      SQLDisconnect(hdbc1);  
      SQLFreeHandle(SQL_HANDLE_DBC, hdbc1);  
   }  
  
   if (henv != SQL_NULL_HENV)  
      SQLFreeHandle(SQL_HANDLE_ENV, henv);  
}  
  
int main() {  
   RETCODE retcode;  
  
   // Pointer to the ODBC driver performance structure.  
   SQLPERF *PerfPtr;  
   SQLINTEGER cbPerfPtr;  
  
   // Allocate the ODBC environment and save handle.  
   retcode = SQLAllocHandle (SQL_HANDLE_ENV, NULL, &henv);  
   if( (retcode != SQL_SUCCESS_WITH_INFO) && (retcode != SQL_SUCCESS)) {  
      printf("SQLAllocHandle(Env) Failed\n\n");  
      Cleanup();  
      return(9);  
   }  
  
   // Notify ODBC that this is an ODBC 3.0 app.  
   retcode = SQLSetEnvAttr(henv, SQL_ATTR_ODBC_VERSION, (SQLPOINTER) SQL_OV_ODBC3, SQL_IS_INTEGER);  
   if( (retcode != SQL_SUCCESS_WITH_INFO) && (retcode != SQL_SUCCESS)) {  
      printf("SQLSetEnvAttr(ODBC version) Failed\n\n");  
      Cleanup();  
      return(9);      
   }  
  
   // Allocate ODBC connection handle and connect.  
   retcode = SQLAllocHandle(SQL_HANDLE_DBC, henv, &hdbc1);  
   if( (retcode != SQL_SUCCESS_WITH_INFO) && (retcode != SQL_SUCCESS)) {  
      printf("SQLAllocHandle(hdbc1) Failed\n\n");  
      Cleanup();  
      return(9);  
   }  
  
   // This sample use Integrated Security. Please create the SQL Server   
   // DSN by using the Windows NT authentication.   
   retcode = SQLConnect(hdbc1, (UCHAR*)"AdventureWorks", SQL_NTS, (UCHAR*)"", SQL_NTS, (UCHAR*)"", SQL_NTS);  
   if( (retcode != SQL_SUCCESS) && (retcode != SQL_SUCCESS_WITH_INFO) ) {  
      printf("SQLConnect() Failed\n\n");  
      Cleanup();  
      return(9);  
   }  
  
   // Set options to log performance statistics.  Specify file to use for the log.  
   retcode = SQLSetConnectAttr( hdbc1, SQL_COPT_SS_PERF_DATA_LOG, &"odbcperf.log", SQL_NTS);  
   if( (retcode != SQL_SUCCESS) && (retcode != SQL_SUCCESS_WITH_INFO) ) {  
      printf("SQLSetConnectAttr() Failed\n\n");  
      Cleanup();  
      return(9);  
   }  
  
   // Start the performance statistics log.  
   retcode =   
      SQLSetConnectAttr( hdbc1, SQL_COPT_SS_PERF_DATA, (SQLPOINTER)SQL_PERF_START, SQL_IS_UINTEGER);  
   if( (retcode != SQL_SUCCESS) && (retcode != SQL_SUCCESS_WITH_INFO) ) {  
      printf("SQLSetConnectAttr() Failed\n\n");  
      Cleanup();  
      return(9);  
   }  
  
   // Allocate statement handle, then execute command.  
   retcode = SQLAllocHandle(SQL_HANDLE_STMT, hdbc1, &hstmt1);  
   if( (retcode != SQL_SUCCESS) && (retcode != SQL_SUCCESS_WITH_INFO) ) {  
      printf("SQLAllocHandle() Failed\n\n");  
      Cleanup();  
      return(9);  
   }  
  
   retcode = SQLExecDirect(hstmt1, (UCHAR*)"SELECT * FROM Purchasing.Vendor", SQL_NTS);  
   if( (retcode != SQL_SUCCESS) && (retcode != SQL_SUCCESS_WITH_INFO) ) {  
      printf("SQLExecDirect() Failed\n\n");  
      Cleanup();  
      return(9);  
   }  
  
   // Clear any result sets generated.  
   while ( ( retcode = SQLMoreResults(hstmt1) ) != SQL_NO_DATA ) {  
      if( (retcode != SQL_SUCCESS) && (retcode != SQL_SUCCESS_WITH_INFO) ) {  
         printf("SQLMoreResults() Failed\n\n");  
         Cleanup();  
         return(9);  
      }  
   }  
  
   retcode = SQLExecDirect(hstmt1, (UCHAR*)"SELECT * FROM Sales.Store", SQL_NTS);  
   if( (retcode != SQL_SUCCESS) && (retcode != SQL_SUCCESS_WITH_INFO) ) {  
      printf("SQLExecDirect() Failed\n\n");  
      Cleanup();  
      return(9);  
   }  
  
   // Clear any result sets generated.  
   while ( ( retcode = SQLMoreResults(hstmt1) ) != SQL_NO_DATA ) {  
      if( (retcode != SQL_SUCCESS) && (retcode != SQL_SUCCESS_WITH_INFO) ) {  
         printf("SQLMoreResults() Failed\n\n");  
         Cleanup();  
         return(9);  
      }  
   }  
  
   // Generate a long-running query.  
   retcode = SQLExecDirect(hstmt1, (UCHAR*)"waitfor delay '00:00:04' ", SQL_NTS);  
   if( (retcode != SQL_SUCCESS) && (retcode != SQL_SUCCESS_WITH_INFO) ) {  
      printf("SQLExecDirect() Failed\n\n");  
      Cleanup();  
      return(9);  
   }  
  
   // Clear any result sets generated.  
   while ( ( retcode = SQLMoreResults(hstmt1) ) != SQL_NO_DATA ) {  
      if( (retcode != SQL_SUCCESS) && (retcode != SQL_SUCCESS_WITH_INFO) ) {  
         printf("SQLMoreResults() Failed\n\n");  
         Cleanup();  
         return(9);  
      }  
   }  
  
   // Write current statistics to the performance log.  
   retcode =   
      SQLSetConnectAttr( hdbc1, SQL_COPT_SS_PERF_DATA_LOG_NOW, (SQLPOINTER)NULL, SQL_IS_UINTEGER);  
   if( (retcode != SQL_SUCCESS) && (retcode != SQL_SUCCESS_WITH_INFO) ) {  
      printf("SQLSetConnectAttr() Failed\n\n");  
      Cleanup();  
      return(9);  
   }  
  
   // Get pointer to current SQLPerf structure.  
   // Print a couple of statistics.  
   retcode =   
      SQLGetConnectAttr( hdbc1, SQL_COPT_SS_PERF_DATA, (SQLPOINTER)&PerfPtr, SQL_IS_POINTER, &cbPerfPtr);  
   if( (retcode != SQL_SUCCESS) && (retcode != SQL_SUCCESS_WITH_INFO) ) {  
      printf("SQLGetConnectAttr() Failed\n\n");  
      Cleanup();  
      return(9);  
   }  
  
   printf("SQLSelects = %d, SQLSelectRows = %d\n", PerfPtr->SQLSelects, PerfPtr->SQLSelectRows);  
  
   // Cleanup  
   SQLFreeHandle(SQL_HANDLE_STMT, hstmt1);  
   SQLDisconnect(hdbc1);  
   SQLFreeHandle(SQL_HANDLE_DBC, hdbc1);  
   SQLFreeHandle(SQL_HANDLE_ENV, henv);  
}  
```  
  
## <a name="see-also"></a>参照  
 [Odbc&#41;&#40;ODBC ドライバーのパフォーマンスのプロファイル方法に関するトピック ](../../relational-databases/native-client-odbc-how-to/profiling-odbc-driver-performance-odbc.md)   
 [ODBC ドライバーのパフォーマンスのプロファイル](../../relational-databases/native-client/odbc/profiling-odbc-driver-performance.md)  
  
