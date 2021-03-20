---
description: 接続ハンドルの割り当て
title: 接続ハンドルを割り当てる |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- ODBC applications, passwords
- ODBC applications, connections
- handles [SQL Server Native Client]
- expiration [SQL Server Native Client]
- passwords [SQL Server], modifying
- SQL Server Native Client ODBC driver, connection handles
- connection handles [SQL Server Native Client]
- modifying passwords
- SQLAllocHandle function
ms.assetid: 471d8a31-199c-4f92-bb10-004fc7733b35
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 9f77f6d5598eda5c94e46bfcf6efbfa868eaa197
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104746012"
---
# <a name="allocating-a-connection-handle"></a>接続ハンドルの割り当て
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  アプリケーションからデータ ソースまたはドライバーに接続する前に、接続ハンドルを割り当てる必要があります。 これを行うには、 *Handletype* パラメーターを SQL_HANDLE_DBC に設定し、初期化された環境ハンドルを指すように *InputHandle* を指定して、 **SQLAllocHandle** を呼び出します。  
  
 接続の特性は、接続属性の設定により制御されます。 たとえば、トランザクションは接続レベルで行われるので、トランザクション分離レベルは 1 つの接続属性になります。 同様に、ログイン タイムアウト、つまり接続試行がタイムアウトするまで待機する秒数も接続属性です。  
  
 接続属性は [SQLSetConnectAttr](../../relational-databases/native-client-odbc-api/sqlsetconnectattr.md)で設定され、その現在の設定は [Sqlgetconnectattr](../../relational-databases/native-client-odbc-api/sqlgetconnectattr.md)を使用して取得されます。 接続を試行する前に **SQLSetConnectAttr** を呼び出すと、ODBC ドライバーマネージャーによって、接続構造に属性が格納され、接続プロセスの一部としてドライバー内に設定されます。 接続属性の中には、アプリケーションが接続を試行する前に設定しなければならないものも、接続の確立後に設定できるものもあります。 たとえば、SQL_ATTR_ODBC_CURSORS は接続前に設定する必要がありますが、SQL_ATTR_AUTOCOMMIT は接続後に設定できます。  
  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 7.0 以降に対してアプリケーションを実行している場合、表形式のデータ ストリーム (TDS) のネットワーク パケット サイズを再設定すると、パフォーマンスが向上することがあります。 サーバーに設定されている既定のパケット サイズは 4 KB です。 パフォーマンスが最も高くなるのは、通常、パケット サイズが 4 ～ 8 KB のときです。 テストにより、他のパケット サイズの方がパフォーマンスが高くなることがわかった場合、アプリケーションではパケット サイズを再設定できます。 ODBC アプリケーションは、SQL_ATTR_PACKET_SIZE オプションを指定して **SQLSetConnectAttr** を呼び出すことによって接続する前にこれを行うことができます。 パケット サイズを大きくすることでパフォーマンスが向上するアプリケーションもありますが、通常、8 KB を超えるパケット サイズを指定して向上するパフォーマンスはごくわずかです。  
  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]Native CLIENT ODBC ドライバーには、アプリケーションが機能を向上させるために使用できる拡張接続属性が多数用意されています。 これらの属性の一部は、データ ソースで指定可能なオプションと同じものをコントロールし、データ ソースで設定されたオプションをどれでもオーバーライドするために使用されます。 たとえば、アプリケーションで引用符で囲まれた識別子を使用する場合は、ドライバー固有の属性である SQL_COPT_SS_QUOTED_IDENT を SQL_QI_ON に設定すると、データ ソースの設定とは関係なく、識別子を引用符で囲むというオプションが常に有効になります。  
  
## <a name="see-also"></a>参照  
 [ODBC&#41;&#40;SQL Server との通信 ](../../relational-databases/native-client-odbc-communication/communicating-with-sql-server-odbc.md)  
  
  
