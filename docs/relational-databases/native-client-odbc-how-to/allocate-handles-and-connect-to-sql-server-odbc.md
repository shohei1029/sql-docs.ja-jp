---
description: ハンドルの割り当てと SQL Server への接続 (ODBC)
title: ハンドルを割り当てて SQL Server に接続する (ODBC) |Microsoft Docs
ms.custom: ''
ms.date: 03/16/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- handles [ODBC]
- handles [ODBC], connection
- handles [ODBC], about handles
ms.assetid: 6172cd52-9c9a-467d-992f-def07f3f3bb1
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 0c981ed247a40c42d8ef8737d1ae891296194dad
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104753282"
---
# <a name="allocate-handles-and-connect-to-sql-server-odbc"></a>ハンドルの割り当てと SQL Server への接続 (ODBC)
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

    
### <a name="to-allocate-handles-and-connect-to-sql-server"></a>ハンドルを割り当てて SQL Server に接続するには  
  
1.  ODBC ヘッダー ファイル Sql.h、Sqlext.h、Sqltypes.h を含めます。  
  
2.  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ドライバー固有のヘッダー ファイル Odbcss.h を含めます。  
  
3.  SQL_HANDLE_ENV の **Handletype** を使用して [SQLAllocHandle](../../odbc/reference/syntax/sqlallochandle-function.md)を呼び出し、ODBC を初期化して環境ハンドルを割り当てます。  
  
4.  **属性** を SQL_ATTR_ODBC_VERSION に設定し、 **valueptr** を SQL_OV_ODBC3 に設定して [SQLSetEnvAttr](../../relational-databases/native-client-odbc-api/sqlsetenvattr.md)を呼び出し、アプリケーションで ODBC 3. x 形式の関数呼び出しを使用することを示します。  
  
5.  必要に応じて、 [SQLSetEnvAttr](../../relational-databases/native-client-odbc-api/sqlsetenvattr.md) を呼び出して他の環境オプションを設定するか、 [SQLGetEnvAttr](../../odbc/reference/syntax/sqlgetenvattr-function.md) を呼び出して環境オプションを取得します。  
  
6.  SQL_HANDLE_DBC の **Handletype** を使用して [SQLAllocHandle](../../odbc/reference/syntax/sqlallochandle-function.md)を呼び出し、接続ハンドルを割り当てます。  
  
7.  必要に応じて、 [SQLSetConnectAttr](../../relational-databases/native-client-odbc-api/sqlsetconnectattr.md) を呼び出して接続オプションを設定するか、 [Sqlgetconnectattr](../../relational-databases/native-client-odbc-api/sqlgetconnectattr.md) を呼び出して接続オプションを取得します。  
  
8.  SQLConnect を呼び出して、既存のデータソースを使用してに接続 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] します。  
  
     または  
  
     接続文字列を使用してに接続するには、 [SQLDriverConnect](../../relational-databases/native-client-odbc-api/sqldriverconnect.md) を呼び出し [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。  
  
     最小の完全な [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 接続文字列は、次の 2 つの形式のいずれかになります。  
  
    ```  
    DSN=dsn_name;Trusted_connection=yes;  
    DRIVER={SQL Server Native Client 10.0};SERVER=server;Trusted_connection=yes;  
    ```  
  
     接続文字列が完全でない場合、 **SQLDriverConnect** は必要な情報の入力を求めることができます。 これは、 *Drivercompletion* パラメーターに指定された値によって制御されます。  
  
     \- または  
  
     反復的な方法で [SQLBrowseConnect](../../relational-databases/native-client-odbc-api/sqlbrowseconnect.md) を複数回呼び出して、接続文字列を作成し、に接続し [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。  
  
9. 必要に応じて、 [SQLGetInfo](../../relational-databases/native-client-odbc-api/sqlgetinfo.md) を呼び出して、データソースのドライバー属性と動作を取得し [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。  
  
10. ステートメントを割り当てて使用します。  
  
11. 切断するには SQLDisconnect を呼び出し、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 接続ハンドルを新しい接続に使用できるようにします。  
  
12. SQL_HANDLE_DBC の **Handletype** を使用して [sqlfreehandle](../../relational-databases/native-client-odbc-api/sqlfreehandle.md)を呼び出し、接続ハンドルを解放します。  
  
13. SQL_HANDLE_ENV の **Handletype** を使用して **sqlfreehandle** を呼び出し、環境ハンドルを解放します。  
  
> [!IMPORTANT]  
>  可能な場合は、Windows 認証を使用します。 Windows 認証が使用できない場合は、実行時に資格情報を入力するようユーザーに求めます。 資格情報をファイルに保存するのは避けてください。 資格情報を保持する必要がある場合は、[Win32 Crypto API](/windows/win32/seccrypto/cryptography-reference) を使用して暗号化してください。  
  
## <a name="example"></a>例  
 この例では、既存の ODBC データソースを必要とせずにのインスタンスに接続するための **SQLDriverConnect** の呼び出しを示し [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。 不完全な接続文字列を **SQLDriverConnect** に渡すと、ODBC ドライバーによって、不足している情報を入力するように求めるメッセージが表示されます。  
  
```  
#define MAXBUFLEN   255  
  
SQLHENV      henv = SQL_NULL_HENV;  
SQLHDBC      hdbc1 = SQL_NULL_HDBC;  
SQLHSTMT      hstmt1 = SQL_NULL_HSTMT;  
  
SQLCHAR      ConnStrIn[MAXBUFLEN] =  
         "DRIVER={SQL Server Native Client 10.0};SERVER=MyServer";  
  
SQLCHAR      ConnStrOut[MAXBUFLEN];  
SQLSMALLINT   cbConnStrOut = 0;  
  
// Make connection without data source. Ask that driver   
// prompt if insufficient information. Driver returns  
// SQL_ERROR and application prompts user  
// for missing information. Window handle not needed for  
// SQL_DRIVER_NOPROMPT.  
retcode = SQLDriverConnect(hdbc1,      // Connection handle  
                  NULL,         // Window handle  
                  ConnStrIn,      // Input connect string  
                  SQL_NTS,         // Null-terminated string  
                  ConnStrOut,      // Address of output buffer  
                  MAXBUFLEN,      // Size of output buffer  
                  &cbConnStrOut,   // Address of output length  
                  SQL_DRIVER_PROMPT);  
```  
  
