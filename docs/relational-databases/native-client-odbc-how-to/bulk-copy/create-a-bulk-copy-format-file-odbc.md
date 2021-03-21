---
description: 一括コピー フォーマット ファイル (ODBC) の作成
title: 一括コピーフォーマットファイルを作成する (ODBC) |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- bulk copy [ODBC], file formats
- bulk copy [ODBC], data files
ms.assetid: 0572fef3-daf5-409e-b557-c2a632f9a06d
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: d75bd4b00d56bb233b129968551415804ac74b63
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104754122"
---
# <a name="create-a-bulk-copy-format-file-odbc"></a>一括コピー フォーマット ファイル (ODBC) の作成
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  このサンプルでは、一括コピー関数を使用してデータ ファイルとフォーマット ファイルの両方を作成する方法を示します。 このサンプルは、ODBC 3.0 以降のバージョン用に開発されました。  
  
> [!IMPORTANT]  
>  可能な場合は、Windows 認証を使用します。 Windows 認証が使用できない場合は、実行時に資格情報を入力するようユーザーに求めます。 資格情報をファイルに保存するのは避けてください。 資格情報を保持する必要がある場合は、[Win32 Crypto API](/windows/win32/seccrypto/cryptography-reference) を使用して暗号化してください。  
  
### <a name="to-create-a-bulk-copy-format-file"></a>一括コピー フォーマット ファイルを作成するには  
  
1.  環境ハンドルおよび接続ハンドルを割り当てます。  
  
2.  一括コピー操作が有効になるように SQL_COPT_SS_BCP および SQL_BCP_ON を設定します。  
  
3.  SQL Server に接続します。  
  
4.  [Bcp_init](../../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-init.md)を呼び出して、次の情報を設定します。  
  
    -   一括コピー操作の対象になるテーブルまたはビューの名前  
  
    -   データベースにコピーするデータが含まれたデータ ファイルの名前、またはデータベースからのコピー時にデータを受け取るデータ ファイルの名前  
  
    -   一括コピー エラー メッセージを受信するデータ ファイルの名前 (メッセージ ファイルが不要な場合は NULL を指定)。  
  
    -   コピーの方向 (テーブルまたはビューからファイルへのコピーの場合は DB_OUT)  
  
5.  [Bcp_columns](../../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-columns.md)を呼び出して、列の数を設定します。  
  
6.  各列に対して [bcp_colfmt](../../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-colfmt.md) を呼び出して、データファイル内の特性を定義します。  
  
7.  [Bcp_writefmt](../../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-writefmt.md)を呼び出して、一括コピー操作によって作成されるデータファイルを記述するフォーマットファイルを作成します。  
  
8.  [Bcp_exec](../../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-exec.md)を呼び出して、一括コピー操作を実行します。  
  
 このように一括コピー操作を実行すると、一括コピーされたデータが含まれたデータ ファイルとデータ ファイルのレイアウトが記述されたフォーマット ファイルが作成されます。  
  
## <a name="example"></a>例  
 AdventureWorks と呼ばれる ODBC データ ソース (既定のデータベースは AdventureWorks サンプル データベース) が必要です  (AdventureWorks サンプルデータベースは、 [Microsoft SQL Server のサンプルとコミュニティのプロジェクト](https://go.microsoft.com/fwlink/?LinkID=85384) のホームページからダウンロードできます)。このデータソースは、オペレーティングシステムによって提供される ODBC ドライバーに基づいている必要があります (ドライバー名は "SQL Server")。 このサンプルを 64 ビット オペレーティング システムで 32 ビット アプリケーションとしてビルドし、実行する場合、%windir%\SysWOW64\odbcad32.exe の ODBC アドミニストレーターを使用して ODBC データ ソースを作成する必要があります。  
  
 このサンプルでは、コンピューターの既定の [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] インスタンスに接続します。 名前付きインスタンスに接続するには、ODBC データ ソースの定義を変更し、server\namedinstance 形式でそのインスタンスを指定します。 [!INCLUDE[ssExpress](../../../includes/ssexpress-md.md)] は、既定で名前付きインスタンスとしてインストールされます。  
  
 最初の ( [!INCLUDE[tsql](../../../includes/tsql-md.md)] ) コードリストを実行して、サンプルで使用するテーブルを作成します。  
  
 odbc32.lib と odbcbcp.lib を使用して 2 つ目の (C++) コード リストをコンパイルします。  
  
 3番目の ( [!INCLUDE[tsql](../../../includes/tsql-md.md)] ) コードリストを実行して、サンプルで使用したテーブルを削除します。  
  
```  
use AdventureWorks  
  
IF EXISTS (SELECT name FROM sysobjects WHERE name = 'BCPDate')  
     DROP TABLE BCPDate  
GO  
  
CREATE TABLE BCPDate (cola int, colb datetime)  
insert BCPDate(cola) values(1)   
insert BCPDate(cola) values(2)   
insert BCPDate(cola) values(3)   
insert BCPDate(cola) values(4)  
```  
  
```  
// compile with: odbc32.lib odbcbcp.lib  
#include <stdio.h>  
#include <string.h>  
#include <windows.h>  
#include <sql.h>  
#include <sqlext.h>  
#include <odbcss.h>  
  
SQLHENV henv = SQL_NULL_HENV;  
HDBC hdbc1 = SQL_NULL_HDBC;    
  
void Cleanup() {  
   if (hdbc1 != SQL_NULL_HDBC) {  
      SQLDisconnect(hdbc1);  
      SQLFreeHandle(SQL_HANDLE_DBC, hdbc1);  
   }  
  
   if (henv != SQL_NULL_HENV)  
      SQLFreeHandle(SQL_HANDLE_ENV, henv);  
}  
  
int main() {  
   RETCODE retcode;  
  
   // BCP variables.  
   SDWORD cRows;  
  
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
      printf("SQLSetEnvAttr(ODBC version) Failed\n\n");  
      Cleanup();  
      return(9);  
   }  
  
   // Sample uses Integrated Security, create SQL Server DSN using Windows NT authentication.   
   retcode = SQLConnect(hdbc1, (UCHAR*)"AdventureWorks", SQL_NTS, (UCHAR*)"",SQL_NTS, (UCHAR*)"", SQL_NTS);  
   if ( (retcode != SQL_SUCCESS) && (retcode != SQL_SUCCESS_WITH_INFO) ) {  
      printf("SQLConnect() Failed\n\n");  
      Cleanup();  
      return(9);  
   }  
  
   // Initialize the bulk copy.  
   retcode = bcp_init(hdbc1, "BCPDate", "BCPODBC.bcp", NULL, DB_OUT);  
   if (retcode != SUCCEED) {  
      printf("bcp_init() Failed\n\n");  
      Cleanup();  
      return(9);  
   }  
  
   // Set the number of output columns.  
   retcode = bcp_columns(hdbc1, 2);  
   if (retcode != SUCCEED) {  
      printf("bcp_init() Failed\n\n");  
      Cleanup();  
      return(9);  
   }  
  
   // Describe the format of column 1 in the data file.  
   retcode = bcp_colfmt(hdbc1, 1, SQLCHARACTER, -1, 5, NULL, 0, 1);  
   if (retcode != SUCCEED) {  
      printf("bcp_init() Failed\n\n");  
      Cleanup();  
      return(9);  
   }  
  
   // Describe the format of column 2 in the data file.  
   retcode = bcp_colfmt(hdbc1, 2, SQLCHARACTER, -1, 20, NULL, 0, 2);  
   if (retcode != SUCCEED) {  
      printf("bcp_init() Failed\n\n");  
      Cleanup();  
      return(9);  
   }  
  
   // Create the format file.  
   retcode = bcp_writefmt(hdbc1, "c:\\BCPFMT.fmt");  
   if (retcode != SUCCEED) {  
      printf("bcp_init() Failed\n\n");  
      Cleanup();  
      return(9);  
   }  
  
   // Execute the bulk copy.  
   retcode = bcp_exec(hdbc1, &cRows);  
   if (retcode != SUCCEED) {  
      printf("bcp_init() Failed\n\n");  
      Cleanup();  
      return(9);  
   }  
  
   printf("Number of rows bulk copied out = %d.\n", cRows);  
  
   // Cleanup  
   SQLDisconnect(hdbc1);  
   SQLFreeHandle(SQL_HANDLE_DBC, hdbc1);  
   SQLFreeHandle(SQL_HANDLE_ENV, henv);  
   return(0);  
}  
```  
  
```  
use AdventureWorks  
IF EXISTS (SELECT name FROM sysobjects WHERE name = 'BCPDate')  
     DROP TABLE BCPDate  
GO  
```  
  
## <a name="see-also"></a>参照  
 [SQL Server ODBC ドライバーを使用した一括コピーの操作方法に関するトピック &#40;ODBC&#41;](../../../relational-databases/native-client-odbc-how-to/bulk-copy/bulk-copying-with-the-sql-server-odbc-driver-how-to-topics-odbc.md)   
 [データ ファイルとフォーマット ファイルの使用](../../../relational-databases/native-client-odbc-bulk-copy-operations/using-data-files-and-format-files.md)  
  
