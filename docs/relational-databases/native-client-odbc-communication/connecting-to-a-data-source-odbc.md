---
description: データ ソースへの接続 (ODBC)
title: データソースへの接続 (ODBC) |Microsoft Docs
ms.custom: ''
ms.date: 03/16/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- checking connection states
- ODBC data sources, connections
- data sources [SQL Server Native Client]
- SQLBrowseConnect function
- ODBC applications, connections
- ODBC applications, data sources
- connections [SQL Server Native Client]
- SQLConnect function
- SQLDriveConnect function
- verifying connection states
- SQL Server Native Client ODBC driver, data sources
- SQL Server Native Client ODBC driver, connections
ms.assetid: ae30dd1d-06ae-452b-9618-8fd8cd7ba074
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 97bcd6e94d77f6c4fe76426ac25d6d21c60282b3
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104756362"
---
# <a name="connecting-to-a-data-source-odbc"></a>データ ソースへの接続 (ODBC)
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  アプリケーションは、環境ハンドルと接続ハンドルを割り当て、任意の接続属性を設定してから、データ ソースまたはドライバーに接続します。 接続には、次の 3 つの関数を使用できます。  
  
-   **SQLConnect**  
  
-   **SQLDriverConnect**  
  
-   **SQLBrowseConnect**  
  
 使用できるさまざまな接続文字列オプションなど、データソースへの接続の作成の詳細については、「 [SQL Server Native Client での接続文字列キーワードの使用](../../relational-databases/native-client/applications/using-connection-string-keywords-with-sql-server-native-client.md)」を参照してください。  
  
## <a name="sqlconnect"></a>SQLConnect  
 **SQLConnect** は最も単純な接続関数です。 この関数は、データ ソース名、ユーザー ID、パスワードの 3 つのパラメーターを受け取ります。 この3つのパラメーターに、データベースへの接続に必要なすべての情報が含まれている場合は、 **SQLConnect** を使用します。 これを行うには、 **sqldatasources**; を使用してデータソースの一覧を作成します。データソース、ユーザー ID、およびパスワードの入力をユーザーに求めます。次に、 **SQLConnect** を呼び出します。  
  
 **SQLConnect** では、データソース名、ユーザー ID、およびパスワードを使用してデータソースに接続することができ、odbc データソースには odbc ドライバーが接続を確立するために必要な他のすべての情報が含まれていることを前提としています。 [SQLDriverConnect](../../relational-databases/native-client-odbc-api/sqldriverconnect.md)や [SQLBrowseConnect](../../relational-databases/native-client-odbc-api/sqlbrowseconnect.md)とは異なり、 **SQLConnect** は接続文字列を使用しません。  
  
## <a name="sqldriverconnect"></a>SQLDriverConnect  
 **SQLDriverConnect** は、データソース名、ユーザー ID、およびパスワードよりも多くの情報が必要な場合に使用します。 **SQLDriverConnect** のパラメーターの1つは、ドライバー固有の情報を含む接続文字列です。 次の理由により、 **SQLConnect** の代わりに **SQLDriverConnect** を使用できます。  
  
-   接続時にドライバー固有の情報を指定する場合  
  
-   ドライバーがユーザーに対して接続情報を要求する場合  
  
-   ODBC データ ソースを使用せずに接続する場合  
  
 **SQLDriverConnect** 接続文字列には、ODBC ドライバーによってサポートされるすべての接続情報を指定する一連のキーワードと値のペアが含まれています。 各ドライバーでは、ドライバーでサポートされるすべての接続情報を表すドライバー固有のキーワード以外に、標準の ODBC キーワード (DSN、FILEDSN, DRIVER、UID、PWD、および SAVEFILE) をサポートします。 **SQLDriverConnect** は、データソースを使用せずに接続するために使用できます。 たとえば、のインスタンスに対して "DSN レス" 接続を作成するように設計されたアプリケーションは、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログイン ID、パスワード、ネットワークライブラリ、接続先のサーバー名、および使用する既定のデータベースを定義する接続文字列を使用して **SQLDriverConnect** を呼び出すことができます。  
  
 **SQLDriverConnect** を使用する場合、必要な接続情報をユーザーに確認するには、次の2つのオプションがあります。  
  
-   アプリケーション ダイアログ ボックス  
  
     接続情報の入力を求めるアプリケーションダイアログボックスを作成し、NULL ウィンドウハンドルと *Drivercompletion* を SQL_DRIVER_NOPROMPT に設定して **SQLDriverConnect** を呼び出すことができます。 このようにパラメーターを設定すると、ODBC ドライバー独自のダイアログ ボックスが開かれなくなります。 この方法は、アプリケーションでユーザー インターフェイスを制御することが重要な場合に使用します。  
  
-   ドライバー ダイアログ ボックス  
  
     アプリケーションをコーディングして、有効なウィンドウハンドルを **SQLDriverConnect** に渡し、 *drivercompletion* パラメーターを SQL_DRIVER_COMPLETE、SQL_DRIVER_PROMPT、または SQL_DRIVER_COMPLETE_REQUIRED に設定できます。 この場合、ドライバーがダイアログ ボックスを生成して、ユーザーに接続情報を要求します。 この方法を使用すると、アプリケーション コードが簡素化されます。  
  
## <a name="sqlbrowseconnect"></a>SQLBrowseConnect  
 **SQLBrowseConnect** は、 **SQLDriverConnect** のように、接続文字列を使用します。 ただし、 **SQLBrowseConnect** を使用すると、アプリケーションは実行時にデータソースと繰り返して完全な接続文字列を作成できます。 この方法を使用すると、アプリケーションで次の 2 つのことを行えます。  
  
-   アプリケーション独自のダイアログ ボックスを作成して目的の情報を要求できるので、アプリケーションのユーザー インターフェイスで制御できます。  
  
-   特定のドライバーが使用できるデータ ソースをシステムから参照できます。これは複数の手順になる場合があります。  
  
     たとえば、ユーザーは最初にネットワークを介してサーバーを参照して、サーバーを選択します。次に、そのサーバーを介して、ドライバーがアクセスできるデータベースを参照するといった手順です。  
  
 **SQLBrowseConnect** が成功した接続を完了すると、 **SQLDriverConnect** への後続の呼び出しで使用できる接続文字列が返されます。  
  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]Native CLIENT ODBC ドライバーは、 **SQLConnect**、 **SQLDriverConnect**、または **SQLBrowseConnect** が正常に実行された場合、常に SQL_SUCCESS_WITH_INFO を返します。 SQL_SUCCESS_WITH_INFO 取得した後に ODBC アプリケーションが **SQLGetDiagRec** を呼び出すと、次のメッセージが表示されます。  
  
 5701  
 このメッセージは、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] が、ユーザーのコンテキストをデータ ソースで定義されている既定のデータベースに登録したことを示します。または、データ ソースに既定のデータベースが定義されていない場合は、接続で使用したログイン ID に対して定義されている既定のデータベースに登録したことを示します。  
  
 5703  
 このメッセージは、その言語がサーバーで使用されていることを示します。  
  
 次の例では、システム管理者によって接続が正常に確立されたときに返されるメッセージを示します。  
  
```  
szSqlState = "01000", *pfNativeError = 5701,  
szErrorMsg="[Microsoft][SQL Server Native Client][SQL Server]  
       Changed database context to 'pubs'."  
szSqlState = "01000", *pfNativeError = 5703,  
szErrorMsg="[Microsoft][SQL Server Native Client][SQL Server]  
       Changed language setting to 'us_english'."  
```  
  
 5701 と 5703 のメッセージは、情報提供だけを目的としているので無視できます。 ただし、SQL_SUCCESS_WITH_INFO リターン コードでは 5701 や 5703 以外のメッセージも返されることがあるので、そのようなリターン コードは無視しないでください。 たとえば、ドライバーがのインスタンスを実行しているサーバーに、古いカタログストアドプロシージャを使用して接続している場合、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] SQL_SUCCESS_WITH_INFO 後に **SQLGetDiagRec** によって返されたエラーの1つは次のようになります。  
  
```  
SqlState:   01000  
pfNative:   0  
szErrorMsg: "[Microsoft][SQL Server Native Client]The ODBC  
            catalog stored procedures installed on server  
            my65server are version 06.50.0193; version 07.00.0205  
            or later is required to ensure proper operation.  
            Please contact your system administrator."  
```  
  
 接続用のアプリケーションのエラー処理関数は、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] SQL_NO_DATA を返すまで **SQLGetDiagRec** を呼び出す必要があります。 次に、 *pfNative* コードが5701または5703のメッセージ以外のすべてのメッセージに対して動作します。  
  
## <a name="see-also"></a>参照  
 [ODBC&#41;&#40;SQL Server との通信 ](../../relational-databases/native-client-odbc-communication/communicating-with-sql-server-odbc.md)  
  
  
