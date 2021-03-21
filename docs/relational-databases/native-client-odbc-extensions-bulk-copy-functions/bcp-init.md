---
description: bcp_init
title: bcp_init |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
apiname:
- bcp_init
- bcp_initW
apilocation:
- sqlncli11.dll
apitype: DLLExport
helpviewer_keywords:
- bcp_init function
ms.assetid: 6a25862c-7f31-4873-ab65-30f3abde89d2
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 3ce9cdbff8e722e732ae8aaf9ced180d1c45eb12
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104749542"
---
# <a name="bcp_init"></a>bcp_init
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

一括コピー操作を初期化します。  

## <a name="syntax"></a>構文  
  
```  
RETCODE bcp_init (  
        HDBC hdbc,  
        LPCTSTR szTable,  
        LPCTSTR szDataFile,  
        LPCTSTR szErrorFile,  
        INT eDirection);  
```  

Unicode および ANSI 名:
- bcp_initA (ANSI)
- bcp_initW (Unicode)

## <a name="arguments"></a>引数  
 *hdbc*  
 一括コピーが有効な ODBC 接続ハンドルです。  
  
 *szTable*  
 コピー操作の対象になるデータベース テーブルの名前です。 この名前には、データベース名または所有者名を含めることもできます。 たとえば、 **gracie** のようになります。 **タイトル**、 **gracie**、 **タイトル** は、すべて有効なテーブル名です。  
  
 *Edirection* が DB_OUT の場合、 *sztable* をデータベースビューの名前にすることもできます。  
  
 *Edirection* が DB_OUT で、 [bcp_exec](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-exec.md)を呼び出す前に [BCP_CONTROL](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-control.md)を使用して select ステートメントを指定した場合は、 **bcp_init** *sztable* を NULL に設定する必要があります。  
  
 *szDataFile*  
 コピー操作の対象になるユーザー ファイルの名前です。 [Bcp_sendrow](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-sendrow.md)を使用して変数からデータを直接コピーする場合は、 *SZDATAFILE ファイル* を NULL に設定します。  
  
 *szErrorFile*  
 進行状況メッセージ、エラー メッセージ、および何かの理由でユーザー ファイルからテーブルにコピーできなかった任意の行のコピーを書き込むエラー ファイルの名前です。 NULL が *Szerrorfile* として渡された場合、エラーファイルは使用されません。  
  
 *eDirection*  
 コピーの方向です。この値は DB_IN または DB_OUT になります。 DB_IN は、プログラム変数またはユーザー ファイルからテーブルへのコピーを示します。 DB_OUT は、データベース テーブルからユーザー ファイルへのコピーを示します。 DB_OUT を指定する場合は、ユーザー ファイル名を指定する必要があります。  
  
## <a name="returns"></a>戻り値  
 SUCCEED または FAIL。  
  
## <a name="remarks"></a>解説  
 他の一括コピー関数を呼び出す前に **bcp_init** を呼び出します。 **bcp_init** は、ワークステーションとの間のデータの一括コピーに必要な初期化を実行し [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。  
  
 **Bcp_init** 関数は、一括コピー関数で使用できるように ODBC 接続ハンドルが有効になっている必要があります。 ハンドルを有効にするには、SQL_COPT_SS_BCP が割り当てられていて、接続されていない接続ハンドルで SQL_BCP_ON に設定されている [SQLSetConnectAttr](../../relational-databases/native-client-odbc-api/sqlsetconnectattr.md) を使用します。 接続済みのハンドルの属性を割り当てようとすると、エラーが発生します。  
  
 データファイルを指定すると、 **bcp_init** によって、データファイルではなく、データベースのソースまたは対象のテーブルの構造が調べられます。 **bcp_init** は、データベーステーブル、ビュー、または SELECT 結果セットの各列に基づいて、データファイルのデータ形式値を指定します。 このデータ形式値には、各列のデータ型、長さや NULL のインジケーターとターミネータのバイト文字列がデータ内に存在するかどうか、および固定長データ型の幅の指定などが含まれます。 **bcp_init** は、次のように値を設定します。  
  
-   指定するデータ型は、データベース テーブル、ビュー、または SELECT 結果セット内の列のデータ型です。 データ型は、sqlncli.h に指定されている [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ネイティブ データ型によって列挙されます。 データ自体はそのコンピューターの形式で表されます。 つまり、 **整数** データ型の列のデータは、データファイルを作成したコンピューターに基づいて、ビッグエンディアンまたはリトルエンディアンの4バイトのシーケンスによって表されます。  
  
-   データベースのデータ型が固定長の場合は、データ ファイルのデータも固定長になります。 データを処理する一括コピー関数 ( [bcp_exec](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-exec.md)など) では、データ行の解析で、データファイル内のデータの長さが、データベーステーブル、ビュー、または選択列リストで指定されたデータの長さと同一である必要があります。 たとえば、 **char (13)** として定義されているデータベース列のデータは、ファイル内のデータ行ごとに13文字で表す必要があります。 データベース列で NULL 値を許容する場合は、固定長データにプレフィックスとして NULL インジケーターを付けることができます。  
  
-   ターミネータ バイト シーケンスを定義すると、ターミネータ バイト シーケンスの長さが 0 に設定されます。  
  
-   データを [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] にコピーするときは、データ ファイルにデータベース テーブル内の各列に格納するデータが含まれている必要があります。 データを [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] からコピーするときは、データベース テーブル、ビュー、または SELECT 結果セット内のすべての列のデータがデータ ファイルにコピーされます。  
  
-   データを [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] にコピーするときは、データ ファイル内の列の序数位置がデータベース テーブル内の列の序数位置と同じであることが必要です。 からコピーする場合 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 、 **bcp_exec** は、データベーステーブル内の列の序数位置に基づいてデータを格納します。  
  
-   データベースのデータ型が可変長 (たとえば、 **varbinary (22)**) の場合、またはデータベース列に null 値を含めることができる場合は、データファイル内のデータの先頭に長さ/null インジケーターが付きます。 インジケーターの幅は、データ型と一括コピーのバージョンによって異なります。  
  
 データファイルに対して指定されたデータ形式の値を変更するには、 [bcp_columns](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-columns.md) を呼び出し、 [bcp_colfmt](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-colfmt.md)します。  
  
 インデックスを含まないテーブルの場合は、データベース復旧モデルを SIMPLE または BULK_LOGGED に設定することで、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] への一括コピーを最適化できます。 詳細については、「 [一括インポートで最小ログ記録を行うための前提条件](../../relational-databases/import-export/prerequisites-for-minimal-logging-in-bulk-import.md) 」および「 [ALTER database](../../t-sql/statements/alter-database-transact-sql.md)」を参照してください。  
  
 データファイルが使用されていない場合は、 [bcp_bind](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-bind.md) を呼び出して各列のデータの形式と場所を指定し、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] [bcp_sendrow](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-sendrow.md)を使用してデータ行をにコピーする必要があります。  
  
## <a name="example"></a>例  
 このサンプルでは、ODBC bcp_init 関数をフォーマット ファイルと共に使用する方法を示します。  
  
 この C++ コードをコンパイルして実行する前に、次の手順を実行する必要があります。  
  
-   Test という ODBC データ ソースを作成し、 任意のデータベースに関連付けます。  
  
-   そのデータベースに対して次の [!INCLUDE[tsql](../../includes/tsql-md.md)] を実行します。  
  
    ```  
    CREATE TABLE BCPDate (cola int, colb datetime);  
    ```  
  
-   アプリケーションを実行するディレクトリに Bcpfmt.fmt というファイルを追加して、そのファイルに次の内容を追加します。  
  
    ```  
    8.0  
    2  
    1SQLCHAR04"\t"1colaSQL_Latin1_General_Cp437_Bin  
    2SQLCHAR08"\r\n"2colbSQL_Latin1_General_Cp437_Bin  
    ```  
  
-   アプリケーションを実行するディレクトリに Bcpodbc.bcp というファイルを追加して、そのファイルに次の内容を追加します。  
  
    ```  
    1  
    2  
    ```  
  
 以上で、この C++ コードをコンパイルして実行できるようになりました。  
  
```  
// compile with: odbc32.lib sqlncli11.lib  
#include <stdio.h>  
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
  
   // Allocate ODBC connection handle, set BCP mode, and connect.  
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
  
   // Sample uses Integrated Security. Create SQL Server DSN using Windows NT authentication.  
   retcode = SQLConnect(hdbc1, (UCHAR*)"Test", SQL_NTS, (UCHAR*)"", SQL_NTS, (UCHAR*)"", SQL_NTS);  
   if ( (retcode != SQL_SUCCESS) && (retcode != SQL_SUCCESS_WITH_INFO) ) {  
      printf("SQLConnect() Failed\n\n");  
      Cleanup();  
      return(9);  
   }  
  
   // Initialize the bulk copy.  
   retcode = bcp_init(hdbc1, "BCPDate", "BCPODBC.bcp", NULL, DB_IN);  
   if ( (retcode != SUCCEED) ) {  
      printf("bcp_init(hdbc1) Failed\n\n");  
      Cleanup();  
      return(9);  
   }  
  
   // Read the format file.  
   retcode = bcp_readfmt(hdbc1, "BCPFMT.fmt");  
   if ( (retcode != SUCCEED) ) {  
      printf("bcp_readfmt(hdbc1) Failed\n\n");  
      Cleanup();  
      return(9);  
   }  
  
   // Execute the bulk copy.  
   retcode = bcp_exec(hdbc1, &cRows);  
   if ( (retcode != SUCCEED) ) {  
      printf("bcp_exec(hdbc1) Failed\n\n");  
      Cleanup();  
      return(9);  
   }  
  
   printf("Number of rows bulk copied in = %d.\n", cRows);  
  
   // Cleanup  
   SQLDisconnect(hdbc1);  
   SQLFreeHandle(SQL_HANDLE_DBC, hdbc1);  
   SQLFreeHandle(SQL_HANDLE_ENV, henv);  
}  
  
```  

## <a name="see-also"></a>参照  
 [一括コピー関数](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/sql-server-driver-extensions-bulk-copy-functions.md)  
  
  
