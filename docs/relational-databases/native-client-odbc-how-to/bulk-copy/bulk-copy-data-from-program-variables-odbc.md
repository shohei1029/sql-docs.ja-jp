---
description: プログラム変数からのデータの一括コピー (ODBC)
title: プログラム変数からの一括データコピー (ODBC) |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- bulk copy [ODBC], program variables
- bulk copy [ODBC]
ms.assetid: 0c3f2d7c-4ff2-4887-adfd-1f488a27c21c
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 24328e6e7f7b91773055659aa94ea7fa7550c4c4
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104744182"
---
# <a name="bulk-copy-data-from-program-variables-odbc"></a>プログラム変数からのデータの一括コピー (ODBC)
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  このサンプルでは、一括コピー関数を使用して、 **bcp_bind** と **bcp_sendrow** を使用してプログラム変数から SQL Server にデータを一括コピーする方法を示します。 (この例では、簡略化のためエラーチェック コードが削除されています)。  
  
 このサンプルは、ODBC 3.0 以降のバージョン用に開発されました。  
  
 **セキュリティ** に関する注意可能であれば、Windows 認証を使用します。 Windows 認証が使用できない場合は、実行時に資格情報を入力するようユーザーに求めます。 資格情報をファイルに保存するのは避けてください。 資格情報を保存する必要がある場合は、[Win32 CryptoAPI](/windows/win32/seccng/cng-portal) を使用して暗号化してください。  
  
### <a name="to-use-bulk-copy-functions-directly-on-program-variables"></a>プログラム変数に対して一括コピー関数を直接使用するには  
  
1.  環境ハンドルおよび接続ハンドルを割り当てます。  
  
2.  一括コピー操作が有効になるように SQL_COPT_SS_BCP および SQL_BCP_ON を設定します。  
  
3.  SQL Server に接続します。  
  
4.  [Bcp_init](../../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-init.md)を呼び出して、次の情報を設定します。  
  
    -   一括コピー操作の対象になるテーブルまたはビューの名前  
  
    -   データ ファイルの名前には NULL を指定します。  
  
    -   一括コピー エラー メッセージを受け取るデータ ファイルの名前 (メッセージ ファイルを使用しない場合は NULL を指定します)。  
  
    -   コピーの方向 (アプリケーションからビューまたはテーブルへのコピーの場合は DB_IN、テーブルまたはビューからアプリケーションへのコピーの場合は DB_OUT)。  
  
5.  一括コピーの各列に対して [bcp_bind](../../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-bind.md) を呼び出して、列をプログラム変数にバインドします。  
  
6.  プログラム変数にデータを入力し、 [bcp_sendrow](../../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-sendrow.md) を呼び出してデータ行を送信します。  
  
7.  複数の行が送信された後、 [bcp_batch](../../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-batch.md) を呼び出して、既に送信されている行をチェックポイントします。 1000行ごとに少なくとも1回は [bcp_batch](../../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-batch.md) を呼び出すことをお勧めします。  
  
8.  すべての行が送信されたら、 [bcp_done](../../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-done.md) を呼び出して操作を完了します。  

 一括コピー操作中に、 [bcp_colptr](../../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-colptr.md) と [bcp_collen](../../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-collen.md)を呼び出すことによって、プログラム変数の場所と長さを変更できます。 [Bcp_control](../../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-control.md)を使用して、さまざまな一括コピーオプションを設定します。 [Bcp_moretext](../../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-moretext.md)を使用して、 **text**、 **ntext**、および **image** 型のデータをセグメント単位でサーバーに送信します。  
  
## <a name="example"></a>例  
 このサンプルは IA64 ではサポートされていません。  
  
 AdventureWorks と呼ばれる ODBC データ ソース (既定のデータベースは AdventureWorks サンプル データベース) が必要です  (AdventureWorks サンプルデータベースは、 [Microsoft SQL Server のサンプルとコミュニティのプロジェクト](https://go.microsoft.com/fwlink/?LinkID=85384) のホームページからダウンロードできます)。このデータソースは、オペレーティングシステムによって提供される ODBC ドライバーに基づいている必要があります (ドライバー名は "SQL Server")。 このサンプルを 64 ビット オペレーティング システムで 32 ビット アプリケーションとしてビルドし、実行する場合、%windir%\SysWOW64\odbcad32.exe の ODBC アドミニストレーターを使用して ODBC データ ソースを作成する必要があります。  
  
 このサンプルでは、コンピューターの既定の [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] インスタンスに接続します。 名前付きインスタンスに接続するには、ODBC データ ソースの定義を変更し、server\namedinstance 形式でそのインスタンスを指定します。 [!INCLUDE[ssExpress](../../../includes/ssexpress-md.md)] は、既定で名前付きインスタンスとしてインストールされます。  
  
 最初の ( [!INCLUDE[tsql](../../../includes/tsql-md.md)] ) コードリストを実行して、サンプルで使用するテーブルを作成します。  
  
 odbc32.lib と odbcbcp.lib を使用して 2 つ目の (C++) コード リストをコンパイルします。 MSBuild.exe でビルドした場合は、まずプロジェクト ディレクトリの Bcpfmt.fmt と Bcpodbc.bcp を .exe があるディレクトリにコピーし、次に .exe を起動します。  
  
 3番目の ( [!INCLUDE[tsql](../../../includes/tsql-md.md)] ) コードリストを実行して、サンプルで使用したテーブルを削除します。  
  
```  
// compile with: odbc32.lib odbcbcp.lib  
use AdventureWorks  
IF EXISTS (SELECT name FROM sysobjects WHERE name = 'BCPSource')  
     DROP TABLE BCPSource  
IF EXISTS (SELECT name FROM sysobjects WHERE name = 'BCPTarget')  
     DROP TABLE BCPTarget  
GO  
  
CREATE TABLE BCPSource (cola int PRIMARY KEY, colb CHAR(10) NULL)  
INSERT INTO BCPSource (cola, colb) VALUES (1, 'aaa')  
INSERT INTO BCPSource (cola, colb) VALUES (2, 'bbb')  
CREATE TABLE BCPTarget (cola int PRIMARY KEY, colb CHAR(10) NULL)  
```  
  
```  
#include <stdio.h>  
#include <string.h>  
#include <windows.h>  
#include <sql.h>  
#include <sqlext.h>  
#include <odbcss.h>  
  
SQLHENV henv = SQL_NULL_HENV;  
HDBC hdbc1 = SQL_NULL_HDBC, hdbc2 = SQL_NULL_HDBC;  
SQLHSTMT hstmt2 = SQL_NULL_HSTMT;  
  
void Cleanup() {  
   if (hstmt2 != SQL_NULL_HSTMT)  
      SQLFreeHandle(SQL_HANDLE_STMT, hstmt2);  
  
   if (hdbc1 != SQL_NULL_HDBC) {  
      SQLDisconnect(hdbc1);  
      SQLFreeHandle(SQL_HANDLE_DBC, hdbc1);  
   }  
  
   if (hdbc2 != SQL_NULL_HDBC) {  
      SQLDisconnect(hdbc2);  
      SQLFreeHandle(SQL_HANDLE_DBC, hdbc2);  
   }  
  
   if (henv != SQL_NULL_HENV)  
      SQLFreeHandle(SQL_HANDLE_ENV, henv);  
}  
  
int main() {  
   RETCODE retcode;  
  
   // BCP variables.  
   char *terminator = "\0";  
  
   // bcp_done takes a different format return code because it returns number of rows bulk copied  
   // after the last bcp_batch call.  
   DBINT cRowsDone = 0;  
  
   // Set up separate return code for bcp_sendrow so it is not using the same retcode as SQLFetch.  
   RETCODE SendRet;  
  
   // Column variables.  cbCola and cbColb must be defined right before Cola and szColb because   
   // they are used as bulk copy indicator variables.  
   struct ColaData {  
      int cbCola;  
      SQLINTEGER Cola;  
   } ColaInst;  
  
   struct ColbData {  
      int cbColb;  
      SQLCHAR szColb[11];  
   } ColbInst;  
  
   // Allocate the ODBC environment and save handle.  
   retcode = SQLAllocHandle (SQL_HANDLE_ENV, NULL, &henv);  
   if ( (retcode != SQL_SUCCESS_WITH_INFO) && (retcode != SQL_SUCCESS)) {  
      printf("SQLAllocHandle(Env) Failed\n\n");  
      Cleanup();  
      return(9);  
   }  
  
   // Notify ODBC that this is an ODBC 3.0 app.  
   retcode = SQLSetEnvAttr(henv, SQL_ATTR_ODBC_VERSION, (SQLPOINTER) SQL_OV_ODBC3, SQL_IS_INTEGER);  
   if ( (retcode != SQL_SUCCESS_WITH_INFO) && (retcode != SQL_SUCCESS)) {  
      printf("SQLSetEnvAttr(ODBC version) Failed\n\n");  
      Cleanup();  
      return(9);      
   }  
  
   // Allocate ODBC connection handle, set bulk copy mode, and connect.  
   retcode = SQLAllocHandle(SQL_HANDLE_DBC, henv, &hdbc1);  
   if ( (retcode != SQL_SUCCESS_WITH_INFO) && (retcode != SQL_SUCCESS)) {  
      printf("SQLAllocHandle(hdbc1) Failed\n\n");  
      Cleanup();  
      return(9);  
   }  
  
   retcode = SQLSetConnectAttr(hdbc1, SQL_COPT_SS_BCP, (void *)SQL_BCP_ON, SQL_IS_INTEGER);  
   if ( (retcode != SQL_SUCCESS_WITH_INFO) && (retcode != SQL_SUCCESS)) {  
      printf("SQLSetConnectAttr(hdbc1) Failed\n\n");  
      Cleanup();  
      return(9);  
   }  
  
   // sample uses Integrated Security, create the SQL Server DSN using Windows NT authentication  
   retcode = SQLConnect(hdbc1, (UCHAR*)"AdventureWorks", SQL_NTS, (UCHAR*)"", SQL_NTS, (UCHAR*)"", SQL_NTS);  
   if ( (retcode != SQL_SUCCESS) && (retcode != SQL_SUCCESS_WITH_INFO) ) {  
      printf("SQLConnect() Failed\n\n");  
      Cleanup();  
      return(9);  
   }  
  
   // Initialize the bulk copy.  
   retcode = bcp_init(hdbc1, "AdventureWorks..BCPTarget", NULL, NULL, DB_IN);  
   if ( (retcode != SUCCEED) ) {  
      printf("bcp_init(hdbc1) Failed\n\n");  
      Cleanup();  
      return(9);  
   }  
  
   // Bind the program variables for the bulk copy.  
   retcode = bcp_bind(hdbc1, (BYTE *)&ColaInst.cbCola, 4, SQL_VARLEN_DATA, NULL, (INT)NULL, SQLINT4, 1);  
   if ( (retcode != SUCCEED) ) {  
      printf("bcp_bind(hdbc1) Failed\n\n");  
      Cleanup();  
      return(9);  
   }  
  
   // Could normally use strlen to calculate the bcp_bind cbTerm parameter, but this terminator   
   // is a null byte (\0), which gives strlen a value of 0. Explicitly give cbTerm a value of 1.  
   retcode = bcp_bind(hdbc1, (BYTE *)&ColbInst.cbColb, 4, 11, (UCHAR*)terminator, 1, SQLCHARACTER, 2);  
   if ( (retcode != SUCCEED) ) {  
      printf("bcp_bind(hdbc1) Failed\n\n");  
      Cleanup();  
      return(9);  
   }  
  
   // Allocate second ODBC connection handle so bulk copy and cursor operations do not conflict.  
   retcode = SQLAllocHandle(SQL_HANDLE_DBC, henv, &hdbc2);  
   if ( (retcode != SQL_SUCCESS_WITH_INFO) && (retcode != SQL_SUCCESS)) {  
      printf("SQLAllocHandle(hdbc2) Failed\n\n");  
      Cleanup();  
      return(9);  
   }  
  
   // Sample uses Integrated Security, create SQL Server DSN using Windows NT authentication.   
   retcode = SQLConnect(hdbc2, (UCHAR*)"AdventureWorks", SQL_NTS, (UCHAR*)"", SQL_NTS, (UCHAR*)"", SQL_NTS);  
   if ( (retcode != SQL_SUCCESS) && (retcode != SQL_SUCCESS_WITH_INFO) ) {  
      printf("SQLConnect() Failed\n\n");  
      Cleanup();  
      return(9);  
   }  
  
   // Allocate ODBC statement handle.  
   retcode = SQLAllocHandle(SQL_HANDLE_STMT, hdbc2, &hstmt2);  
   if ( (retcode != SQL_SUCCESS_WITH_INFO) && (retcode != SQL_SUCCESS)) {  
      printf("SQLAllocHandle(hstmt2) Failed\n\n");  
      Cleanup();  
      return(9);  
   }  
  
SQLLEN lDataLengthA;  
SQLLEN lDataLengthB;  
  
   // Bind the SELECT statement to the same program variables bound to the bulk copy operation.  
   retcode = SQLBindCol(hstmt2, 1, SQL_C_SLONG, &ColaInst.Cola, 0, &lDataLengthA);  
   if ( (retcode != SQL_SUCCESS_WITH_INFO) && (retcode != SQL_SUCCESS)) {  
      printf("SQLBindCol(hstmt2) Failed\n\n");  
      Cleanup();  
      return(9);  
   }  
  
   retcode = SQLBindCol(hstmt2, 2, SQL_C_CHAR, &ColbInst.szColb, 11, &lDataLengthB);  
   if ( (retcode != SQL_SUCCESS_WITH_INFO) && (retcode != SQL_SUCCESS)) {  
      printf("SQLBindCol(hstmt2) Failed\n\n");  
      Cleanup();  
      return(9);  
   }  
  
   // Execute SELECT statement to build a cursor containing data to be bulk copied to new table.  
   retcode = SQLExecDirect(hstmt2, (UCHAR*)"SELECT * FROM BCPSource", SQL_NTS);  
   if ( (retcode != SQL_SUCCESS) && (retcode != SQL_SUCCESS_WITH_INFO) ) {  
      printf("SQLExecDirect Failed\n\n");  
      Cleanup();  
      return(9);  
   }  
   // Go into a loop fetching rows from the cursor until each row is fetched. Because the   
   // bcp_bind calls and SQLBindCol calls each reference the same variables, each fetch fills   
   // the variables used by bcp_sendrow, so all you have to do to send the data to SQL Server is   
   // to call bcp_sendrow.  
   while ( (retcode = SQLFetch(hstmt2) ) != SQL_NO_DATA) {  
      if ( (retcode != SQL_SUCCESS) && (retcode != SQL_SUCCESS_WITH_INFO) ) {  
         printf("SQLFetch Failed\n\n");  
         Cleanup();  
         return(9);  
      }  
  
      ColaInst.cbCola = (int)lDataLengthA;  
      ColbInst.cbColb = (int)lDataLengthB;  
  
     if ( (SendRet = bcp_sendrow(hdbc1) ) != SUCCEED ) {  
         printf("bcp_sendrow(hdbc1) Failed\n\n");  
         Cleanup();  
         return(9);  
      }  
   }  
  
   // Signal the end of the bulk copy operation.  
   cRowsDone = bcp_done(hdbc1);  
   if ( (cRowsDone == -1) ) {  
      printf("bcp_done(hdbc1) Failed\n\n");  
      Cleanup();  
      return(9);  
   }  
  
   printf("Number of rows bulk copied after last bcp_batch call = %d.\n", cRowsDone);  
  
   // Cleanup.  
   SQLFreeHandle(SQL_HANDLE_STMT, hstmt2);  
   SQLDisconnect(hdbc1);  
   SQLFreeHandle(SQL_HANDLE_DBC, hdbc1);  
   SQLDisconnect(hdbc2);  
   SQLFreeHandle(SQL_HANDLE_DBC, hdbc2);  
   SQLFreeHandle(SQL_HANDLE_ENV, henv);  
```  
  
```  
use AdventureWorks  
IF EXISTS (SELECT name FROM sysobjects WHERE name = 'BCPSource')  
     DROP TABLE BCPSource  
IF EXISTS (SELECT name FROM sysobjects WHERE name = 'BCPTarget')  
     DROP TABLE BCPTarget  
GO  
```  
  
## <a name="see-also"></a>参照  
 [SQL Server ODBC ドライバーを使用した一括コピーの操作方法に関するトピック &#40;ODBC&#41;](../../../relational-databases/native-client-odbc-how-to/bulk-copy/bulk-copying-with-the-sql-server-odbc-driver-how-to-topics-odbc.md)   
 [プログラム変数からの一括コピー](../../../relational-databases/native-client-odbc-bulk-copy-operations/bulk-copying-from-program-variables.md)  
  
