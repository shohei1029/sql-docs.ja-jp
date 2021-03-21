---
title: ODBC クライアントのサービスプリンシパル名 (Spn)
description: クライアントアプリケーションでサービスプリンシパル名 (Spn) をサポートする ODBC 属性と関数について説明します。
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
ms.assetid: 1d60cb30-4c46-49b2-89ab-701e77a330a2
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 0e7309f75d9e1a92adf831b0405d8e93f3eb73f1
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104754092"
---
# <a name="service-principal-names-spns-in-client-connections-odbc"></a>クライアント接続におけるサービス プリンシパル名 (SPN) (ODBC)
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  ここでは、クライアント アプリケーションのサービス プリンシパル名 (SPN) をサポートする ODBC 属性および関数について説明します。 クライアントアプリケーションでの Spn の詳細については、「 [サービスプリンシパル名 &#40;spn&#41; クライアント接続でのサポート](../../../relational-databases/native-client/features/service-principal-name-spn-support-in-client-connections.md) 」および「 [相互 Kerberos 認証の取得](../../../relational-databases/native-client-odbc-how-to/get-mutual-kerberos-authentication.md)」を参照してください。  
  
## <a name="connection-string-keywords"></a>接続文字列キーワード  
 次の接続文字列キーワードを使用すると、クライアント アプリケーションは SPN を指定できます。  
  
|キーワード|値|  
|-------------|-----------|  
|**ServerSPN**|サーバーの SPN。 既定値は空文字です。この場合、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Native Client は、ドライバーが生成した SPN を既定値として使用します。|  
|**FailoverPartnerSPN**|フェールオーバー パートナーの SPN。 既定値は空文字です。この場合、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Native Client は、ドライバーが生成した SPN を既定値として使用します。|  
  
## <a name="connection-attributes"></a>接続属性  
 次の接続属性を使用すると、クライアント アプリケーションは、SPN を指定して認証方法を照会できます。  
  
|名前|種類|使用法|  
|----------|----------|-----------|  
|SQL_COPT_SS_SERVER_SPN<br /><br /> SQL_COPT_SS_FAILOVER_PARTNER_SPN|SQLTCHAR、読み取り/書き込み|サーバーの SPN を指定します。 既定値は空文字です。この場合、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Native Client は、ドライバーが生成した SPN を既定値として使用します。<br /><br /> この属性を照会できるのは、属性がプログラムによって設定された後、または接続が開かれた後だけです。 接続が開いていない場合にこの属性を照会し、属性がプログラムによって設定されていない場合、SQL_ERROR が返され、"接続は開いていません" というメッセージで SQLState 08003 の診断レコードが記録されます。<br /><br /> 接続が開いている場合にこの属性を設定すると、SQL_ERROR が返され、"この操作は、ここでは実行できません" というメッセージで SQLState HY011 の診断レコードが記録されます。|  
|SQL_COPT_SS_INTEGRATED_AUTHENTICATION_METHOD|SQLTCHAR、読み取り専用|接続に使用された認証方法を返します。 アプリケーションに返される値は、Windows が [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Native Client に返す値です。 次のいずれかの値になります。<br /><br /> "NTLM"。これは NTLM 認証を使用して接続を開いたときに返されます。<br /><br /> "Kerberos"。これは Kerberos 認証を使用して接続を開いたときに返されます。<br /><br /> <br /><br /> この属性は、Windows 認証を使用する、開いている接続でのみ読み取りが可能です。 接続が開かれる前にこの属性を読み取ると、SQL_ERROR が返され、"接続は開いていません" というメッセージで SQLState 08003 がエラーとして記録されます。<br /><br /> この属性が Windows 認証を使用していない接続で照会されると、SQL_ERROR が返され、"属性またはオプション識別子が無効です (SQL_COPT_SS_INTEGRATED_AUTHENTICATION_METHOD は信頼関係接続でのみ使用できます)" というメッセージで SQLState HY092 がエラーとして記録されます。<br /><br /> 認証方法を特定できない場合は、SQL_ERROR が返され、"一般的なエラー" というメッセージで SQLState HY000 がエラーとして記録されます。|  
|SQL_COPT_SS_MUTUALLY_AUTHENTICATED|SQLSMALLINT、読み取り専用|接続されているサーバーが相互に認証された場合は SQL_TRUE を返します。それ以外の場合は SQL_FALSE を返します。<br /><br /> この属性は、開いている接続のみで読み取ることができます。 接続が開かれる前にこの属性を読み取ると、SQL_ERROR が返され、"接続は開いていません" というメッセージで SQLState 08003 がエラーとして記録されます。<br /><br /> Windows 認証を使用していない接続に対してこの属性が照会されると、SQL_FALSE が返されます。|  
  
## <a name="odbc-function-support-for-specifying-spns"></a>ODBC 関数による SPN 指定のサポート  
 次の ODBC 関数では、クライアント アプリケーションと SPN をサポートしています。  
  
-   [SQLBrowseConnect](../../../relational-databases/native-client-odbc-api/sqlbrowseconnect.md)  
  
-   [SQLConnect](../../../relational-databases/native-client-odbc-api/sqlconnect.md)  
  
-   [SQLDriverConnect](../../../relational-databases/native-client-odbc-api/sqldriverconnect.md)  
  
-   [SQLGetConnectAttr](../../../relational-databases/native-client-odbc-api/sqlgetconnectattr.md)  
  
-   [SQLSetConnectAttr](../../../relational-databases/native-client-odbc-api/sqlsetconnectattr.md)  
  
## <a name="see-also"></a>参照  
 [SQL Server Native Client &#40;ODBC&#41;](../../../relational-databases/native-client/odbc/sql-server-native-client-odbc.md)  
  
  
