---
description: SQLBrowseConnect
title: SQLBrowseConnect |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
apitype: DLLExport
helpviewer_keywords:
- SQLBrowseConnect function
ms.assetid: 57faf388-c7ca-4696-9845-34e0a10cc5f7
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: ef7782ed2c0fb5f60d0ca51ff814f109116820d8
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104751372"
---
# <a name="sqlbrowseconnect"></a>SQLBrowseConnect
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  **SQLBrowseConnect** は、3つのレベルの接続情報に分類できるキーワードを使用します。 次の表では、キーワードごとに、有効な値の一覧が返されるかどうか、およびそのキーワードが省略可能であるかどうかを示します。  
  
## <a name="level-1"></a>[レベル 1]  
  
|キーワード|一覧が返されるかどうか|省略できるかどうか|説明|  
|-------------|--------------------|---------------|-----------------|  
|DSN (DSN)|該当なし|いいえ|**Sqldatasources** ソースによって返されるデータソースの名前。 DSN キーワードは、DRIVER キーワードと同時に使用できません。|  
|DRIVER|該当なし|いいえ|Microsoft® [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native CLIENT ODBC ドライバー名は { [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] native client 11} です。 DRIVER キーワードは、DSN キーワードと同時に使用できません。|  
  
## <a name="level-2"></a>[レベル 2]  
  
|キーワード|一覧が返されるかどうか|省略できるかどうか|説明|  
|-------------|--------------------|---------------|-----------------|  
|SERVER|はい|いいえ|データ ソースがあるネットワーク上のサーバー名。 サーバー名には「(local)」と入力することもできます。これは、ネットワークに接続されていない [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のローカル コピーも使用できることを意味します。|  
|UID|いいえ|はい|ユーザー ログイン ID。|  
|PWD|いいえ|はい (ユーザーによって異なります)|ユーザーが指定したパスワード。|  
|APP|いいえ|はい|**SQLBrowseConnect** を呼び出すアプリケーションの名前。|  
|WSID|いいえ|はい|ワークステーション ID。 通常は、アプリケーションが実行されているコンピューターのネットワーク名です。|  
  
## <a name="level-3"></a>レベル 3  
  
|キーワード|一覧が返されるかどうか|省略できるかどうか|説明|  
|-------------|--------------------|---------------|-----------------|  
|DATABASE|はい|はい|データベースの名前 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。|  
|LANGUAGE|はい|はい|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] で使用される言語。|  
  
 **SQLBrowseConnect** は、ODBC データソースの定義に格納されているデータベースと言語キーワードの値を無視します。 **SQLBrowseConnect** に渡された接続文字列で指定されたデータベースまたは言語が無効である場合、 **SQLBrowseConnect** は SQL_NEED_DATA とレベル3の接続属性を返します。  
  
 [SQLSetConnectAttr](../../relational-databases/native-client-odbc-api/sqlsetconnectattr.md)を呼び出すことによって設定される次の属性は、 **SQLBrowseConnect** によって返される結果セットを決定します。  
  
|属性|説明|  
|---------------|-----------------|  
|SQL_COPT_SS_BROWSE_CONNECT|SQL_MORE_INFO_YES に設定されている場合、 **SQLBrowseConnect** はサーバープロパティの拡張文字列を返します。<br /><br /> **SQLBrowseConnect** によって返される拡張文字列の例を次に示します。<br /><br /> <br /><br /> `ServerName\InstanceName;Clustered:No;Version:8.00.131`<br /><br /> <br /><br /> この文字列では、サーバーに関するさまざまな情報がセミコロンで区切られます。 コンマは、異なるサーバー インスタンスを区切るために使用します。|  
|SQL_COPT_SS_BROWSE_SERVER|サーバー名が指定されている場合、 **SQLBrowseConnect** は指定されたサーバーの情報を返します。 SQL_COPT_SS_BROWSE_SERVER が NULL に設定されている場合、 **SQLBrowseConnect** はドメイン内のすべてのサーバーに関する情報を返します。<br /><br /> <br /><br /> ネットワークの問題のため、 **SQLBrowseConnect** は、すべてのサーバーからタイムリーな応答を受信できない可能性があります。 したがって、要求ごとに返されるサーバーの一覧が異なる可能性があります。|  
|SQL_COPT_SS_BROWSE_CACHE_DATA|SQL_COPT_SS_BROWSE_CACHE_DATA 属性が SQL_CACHE_DATA_YES に設定されている場合は、バッファー長の不足が原因で結果を保持できないときにデータをチャンクでフェッチできます。 この長さは、SQLBrowseConnect の BufferLength 引数で指定されます。<br /><br /> バッファー長を超えるデータがあるときは SQL_NEED_DATA が返されます。 取得対象のデータがそれ以上ないときは SQL_SUCCESS が返されます。<br /><br /> 既定値は SQL_CACHE_DATA_NO です。|  
  
## <a name="sqlbrowseconnect-support-for-high-availability-disaster-recovery"></a>SQLBrowseConnect の HADR サポート  
 **SQLBrowseConnect** を使用してクラスターに接続する方法の詳細については、 [!INCLUDE[ssHADR](../../includes/sshadr-md.md)] 「[高可用性、ディザスターリカバリーのサポートの SQL Server Native Client](../../relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery.md)」を参照してください。  
  
## <a name="sqlbrowseconnect-support-for-service-principal-names-spns"></a>SQLBrowseConnect によるサービス プリンシパル名 (SPN) のサポート  
 接続が開いている場合、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client では、SQL_COPT_SS_MUTUALLY_AUTHENTICATED および SQL_COPT_SS_INTEGRATED_AUTHENTICATION_METHOD が、接続を開くときに使用された認証方式に設定されます。  
  
 Spn の詳細については、「 [クライアント接続 &#40;ODBC&#41;のサービスプリンシパル名 &#40;spn&#41; ](../../relational-databases/native-client/odbc/service-principal-names-spns-in-client-connections-odbc.md)」を参照してください。  
  
## <a name="change-history"></a>変更履歴  
  
|変更内容|  
|---------------------|  
|SQL_COPT_SS_BROWSE_CACHE_DATA に関する記述を追加しました。|  
  
## <a name="see-also"></a>参照  
 [SQLBrowseConnect 関数](../../odbc/reference/syntax/sqlbrowseconnect-function.md)   
 [ODBC API 実装の詳細](../../relational-databases/native-client-odbc-api/odbc-api-implementation-details.md)  
  
