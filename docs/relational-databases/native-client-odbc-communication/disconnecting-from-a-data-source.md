---
description: データ ソースからの切断
title: データソースからの切断 |Microsoft Docs
ms.custom: ''
ms.date: 03/03/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- ODBC data sources, connections
- data sources [SQL Server Native Client]
- disconnecting [ODBC]
- ODBC applications, disconnecting
- SQLDisconnect function
- ODBC applications, data sources
- connections [SQL Server Native Client]
- SQLFreeHandle function
- ODBC data sources, disconnecting
- SQL Server Native Client ODBC driver, data sources
- ODBC functions
- SQL Server Native Client ODBC driver, connections
ms.assetid: 65b0267d-b2ab-4a59-83f2-436d90cfbf79
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 4864b004cf9bd885697aa2a36aac025ae0bf12a1
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104756342"
---
# <a name="disconnecting-from-a-data-source"></a>データ ソースからの切断
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  アプリケーションでデータソースの使用が完了すると、 **Sqldisconnect** が呼び出されます。 **Sqldisconnect** は、接続に割り当てられているすべてのステートメントを解放し、データソースからドライバーを切断します。 切断後、アプリケーションは [Sqlfreehandle](../../relational-databases/native-client-odbc-api/sqlfreehandle.md) を呼び出して接続ハンドルを解放できます。 終了する前に、アプリケーションは **Sqlfreehandle** を呼び出して環境ハンドルを解放します。  
  
 切断後は、割り当てられていた接続ハンドルを再利用して、別のデータ ソースに接続したり、同じデータ ソースに再接続したりできます。 切断してから後で再接続するか、接続した状態を維持するかを決める際、アプリケーションの作成者は各操作の相対コストを考慮する必要があります。 接続メディアによっては、データ ソースに接続し、接続した状態を維持するのに比較的コストがかかる場合があります。 両者を比較検討する場合は、アプリケーションで同じデータ ソースに追加操作が行われる可能性やタイミングについて想定することも必要です。 また、アプリケーションで複数の接続が必要になる場合もあります。  
  
## <a name="see-also"></a>参照  
 [ODBC&#41;&#40;SQL Server との通信 ](../../relational-databases/native-client-odbc-communication/communicating-with-sql-server-odbc.md)  
  
  
