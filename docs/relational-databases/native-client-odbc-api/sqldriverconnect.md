---
title: SQLDriverConnect |Microsoft Docs
description: SQL Server Native Client ODBC ドライバーでの高可用性/ディザスターリカバリーと Spn の SQLDriverConnect 接続属性とサポートについて説明します。
ms.custom: ''
ms.date: 03/16/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
apitype: DLLExport
helpviewer_keywords:
- SQLDriverConnect function
ms.assetid: a1e38e2c-3a97-42d1-9c45-a0ca3282ffd1
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 4ca61ae1bdb79c4b6af27a803d4c5451cfd2c40e
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104751362"
---
# <a name="sqldriverconnect"></a>SQLDriverConnect
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client ODBC ドライバーでは、接続文字列のキーワードを置き換えたり、拡張したりするための接続属性を定義します。 接続文字列のいくつかのキーワードには、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client ODBC ドライバーが指定する既定値があります。  
  
 Native Client ODBC ドライバーで使用できるキーワードの一覧については [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 、「 [SQL Server Native Client での接続文字列キーワードの使用](../../relational-databases/native-client/applications/using-connection-string-keywords-with-sql-server-native-client.md)」を参照してください。  
  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]接続属性とドライバーの既定の動作の詳細については、「 [SQLSetConnectAttr](../../relational-databases/native-client-odbc-api/sqlsetconnectattr.md)」を参照してください。  
  
 Native Client で有効な接続文字列キーワードの詳細については [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 、「 [SQL Server Native Client での接続文字列キーワードの使用](../../relational-databases/native-client/applications/using-connection-string-keywords-with-sql-server-native-client.md)」を参照してください。  
  
 **SQLDriverConnect**_drivercompletion_ パラメーター値が SQL_DRIVER_PROMPT、SQL_DRIVER_COMPLETE、または SQL_DRIVER_COMPLETE_REQUIRED の場合、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client ODBC ドライバーは、表示されるダイアログボックスからキーワード値を取得します。 接続文字列でキーワード値が渡され、ユーザーがダイアログ ボックスでキーワードの値を変更しなかった場合、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client ODBC ドライバーは接続文字列の値を使用します。 接続文字列で値が設定されていない場合、ユーザーがダイアログ ボックスで割り当てを行わないと、ドライバーは既定値を使用します。  
  
 *Drivercompletion* の値でドライバーの接続ダイアログボックスを表示する必要がある (または必要になる可能性がある) 場合は、 **SQLDriverConnect** に有効な *WindowHandle* を指定する必要があります。 無効なハンドルを指定すると、SQL_ERROR が返されます。  
  
 DRIVER キーワードまたは DSN キーワードを指定します。 ODBC は、これらの 2 つのキーワードが両方指定されている場合、左側に指定されているキーワードを使用し、他方を無視するように指示します。 DRIVER が指定されている場合、またはが2の左端で、 **SQLDriverConnect**_drivercompletion_ パラメーター値が SQL_DRIVER_NOPROMPT の場合、SERVER キーワードと適切な値が必要です。  
  
 SQL_DRIVER_NOPROMPT が指定されているときは、ユーザー認証に関するキーワードに値が指定されている必要があります。 ドライバーは、文字列 "Trusted_Connection=yes" または UID キーワードと PWD キーワードの両方が指定されていることを確認します。  
  
 *Drivercompletion* パラメーター値が SQL_DRIVER_NOPROMPT または SQL_DRIVER_COMPLETE_REQUIRED で、言語またはデータベースが接続文字列から取得され、その言語またはデータベースが無効である場合、 **SQLDriverConnect** は SQL_ERROR を返します。  
  
 *Drivercompletion* パラメーター値が SQL_DRIVER_NOPROMPT または SQL_DRIVER_COMPLETE_REQUIRED であり、言語またはデータベースが ODBC データソースの定義から取得されていて、または無効な場合、 **SQLDriverConnect** は指定されたユーザー ID の既定の言語またはデータベースを使用して、SQL_SUCCESS_WITH_INFO を返します。  
  
 *Drivercompletion* パラメーター値が SQL_DRIVER_COMPLETE または SQL_DRIVER_PROMPT で、言語またはデータベースが無効である場合、 **SQLDriverConnect** はダイアログボックスを再指定します。  
  
## <a name="sqldriverconnect-support-for-high-availability-disaster-recovery"></a>SQLDriverConnect の HADR サポート  
 **SQLDriverConnect** を使用してクラスターに接続する方法の詳細については、 [!INCLUDE[ssHADR](../../includes/sshadr-md.md)] 「[高可用性、ディザスターリカバリーのサポートの SQL Server Native Client](../../relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery.md)」を参照してください。  
  
## <a name="sqldriverconnect-support-for-service-principal-names-spns"></a>SQLDriverConnect によるサービス プリンシパル名 (SPN) のサポート  
 SQLDDriverConnect では、プロンプトが有効になっているときに ODBC ログインダイアログボックスが使用されます。 これにより、プリンシパル サーバーとそのフェールオーバー パートナーの両方に対して SPN を入力できます。  
  
 SQLDriverConnect は新しい接続文字列キーワード **Serverspn** と **failoverpartnerspn** を受け入れ、新しい接続属性 SQL_COPT_SS_SERVER_SPN と SQL_COPT_SS_FAILOVER_PARTNER_SPN を認識します。  
  
 接続属性の値が複数指定されている場合は、DSN の値や接続文字列の値より、プログラムによって設定された値が優先されます。 DSN の値は接続文字列の値より優先されます。  
  
 接続が開いている場合、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client では、SQL_COPT_SS_MUTUALLY_AUTHENTICATED および SQL_COPT_SS_INTEGRATED_AUTHENTICATION_METHOD が、接続を開くときに使用された認証方式に設定されます。  
  
 Spn の詳細については、「 [クライアント接続 &#40;ODBC&#41;のサービスプリンシパル名 &#40;spn&#41; ](../../relational-databases/native-client/odbc/service-principal-names-spns-in-client-connections-odbc.md)」を参照してください。  
  
## <a name="examples"></a>例  
 次の呼び出しは、 **SQLDriverConnect** に必要なデータ量の最小値を示しています。  
  
```  
SQLDriverConnect(hdbc, hwnd,  
    (SQLTCHAR*) TEXT("DRIVER={SQL Server Native Client 10};"), SQL_NTS, szOutConn,  
    MAX_CONN_OUT, &cbOutConn, SQL_DRIVER_COMPLETE);  
```  
  
 次の接続文字列は、 *Drivercompletion* パラメーター値が SQL_DRIVER_NOPROMPT 場合に最低限必要なデータを示しています。  
  
```  
"DSN=Human Resources;Trusted_Connection=yes"  
  
"FILEDSN=HR_FDSN;Trusted_Connection=yes"  
  
"DRIVER={SQL Server Native Client 10};SERVER=(local);Trusted_Connection=yes"  
```  
  
## <a name="see-also"></a>参照  
 [SQLDriverConnect 関数](../../odbc/reference/syntax/sqldriverconnect-function.md)   
 [ODBC API の実装の詳細](../../relational-databases/native-client-odbc-api/odbc-api-implementation-details.md)   
 [SET ANSI_NULLS &#40;Transact-SQL&#41;](../../t-sql/statements/set-ansi-nulls-transact-sql.md)   
 [SET ANSI_PADDING &#40;Transact-SQL&#41;](../../t-sql/statements/set-ansi-padding-transact-sql.md)   
 [SET ANSI_WARNINGS &#40;Transact-SQL&#41;](../../t-sql/statements/set-ansi-warnings-transact-sql.md)  
  
