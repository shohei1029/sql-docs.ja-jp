---
title: 一括コピーバッチサイズの管理 |Microsoft Docs
description: 一括コピーのバッチサイズでトランザクションの範囲を定義する方法について説明します。これにより、SQL Server Native Client ODBC でのエラーの動作とロックのオーバーヘッドが発生します。
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- SQL Server Native Client ODBC driver, bulk copy
- ODBC, bulk copy operations
- batches [ODBC]
- bulk copy [ODBC], batch sizes
ms.assetid: 4b24139f-788b-45a6-86dc-ae835435d737
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 41b6d411b0a0b82bb440f7bf1d16fe9e2d153eae
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104754732"
---
# <a name="managing-bulk-copy-batch-sizes"></a>一括コピー バッチ サイズの管理
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  一括コピー操作でバッチを使用する主な目的は、トランザクションのスコープを定義することです。 一括コピー関数では、バッチ サイズが設定されていないと、一括コピー全体を 1 つのトランザクションと見なします。 バッチ サイズが設定されている場合、各バッチはそのバッチの終了時にコミットされるトランザクションで構成されます。  
  
 バッチ サイズを指定せずに一括コピーを実行し、エラーが発生した場合は、一括コピー全体がロールバックされます。 実行時間が長い一括コピーの復旧には時間がかかることがあります。 バッチ サイズを設定すると、各バッチが 1 つのトランザクションと見なされ、各バッチがコミットされます。 エラーが発生した場合は、最後の未解決のバッチだけがロールバックされます。  
  
 バッチ サイズは、ロックのオーバーヘッドにも影響を与えることがあります。 に対して一括コピーを実行する場合は [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 、 [bcp_control](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-control.md) を使用して TABLOCK ヒントを指定し、行ロックではなくテーブルロックを取得できます。 1 つのテーブル ロックを設定すると、一括コピー操作全体のオーバーヘッドを最小限に抑えることができます。 TABLOCK を指定しないと各行がロックされるので、一括コピーの実行中にすべてのロックを保持するオーバーヘッドにより、パフォーマンスが低下することがあります。 トランザクションの長さだけロックが保持されるので、バッチ サイズを指定すると、定期的にコミットが発生して、その時点で保持されているロックが解放されるため、この問題を解決できます。  
  
 大量の行を一括コピーする場合、1 つのバッチを構成する行数がパフォーマンスに大きな影響を与えることがあります。 推奨バッチ サイズは、実行する一括コピーの種類によって異なります。  
  
-   [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] への一括コピーを行う場合は、TABLOCK 一括コピー ヒントを指定し、大きなバッチ サイズを設定します。  
  
-   TABLOCK を指定しない場合は、バッチ サイズを 1,000 行未満に制限します。  
  
 データファイルから一括コピーする場合は、 [bcp_exec](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-exec.md)を呼び出す前に、bcpbatch オプションを指定して **bcp_control** を呼び出すことによってバッチサイズを指定します。 [Bcp_bind](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-bind.md)と [bcp_sendrow](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-sendrow.md)を使用してプログラム変数から一括コピーを行う場合、バッチサイズは [bcp_sendrow](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-sendrow.md) *x* 回を呼び出した後 [bcp_batch](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-batch.md)を呼び出すことによって制御されます。ここで、 *x* はバッチ内の行数です。  
  
 バッチはトランザクションのサイズを指定するだけでなく、ネットワーク経由でサーバーに行を送信するときにも影響を与えます。 一括コピー関数は、通常、ネットワークパケットがいっぱいになるまで **bcp_sendrow** から行をキャッシュしてから、完全なパケットをサーバーに送信します。 ただし、アプリケーションが **bcp_batch** を呼び出すと、そのパケットがいっぱいになっているかどうかに関係なく、現在のパケットがサーバーに送信されます。 バッチ サイズを非常に小さくすると、いっぱいになっていないパケットが大量にサーバーに送信されるので、パフォーマンスが低下することがあります。 たとえば、すべての **bcp_sendrow** の後に **bcp_batch** を呼び出すと、各行が個別のパケットで送信され、行が非常に大きい場合を除き、各パケットの領域が無駄になります。 SQL Server のネットワークパケットの既定のサイズは 4 KB ですが、アプリケーションでは、SQL_ATTR_PACKET_SIZE 属性を指定して [SQLSetConnectAttr](../../relational-databases/native-client-odbc-api/sqlsetconnectattr.md) を呼び出すことによってサイズを変更できます。  
  
 バッチのもう1つの副作用は、 **bcp_batch** で完了するまで、各バッチが未処理の結果セットと見なされることです。 バッチが未処理のときに接続ハンドルで他の操作が試行された場合、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native CLIENT ODBC ドライバーでは、SQLState = "HY000" およびエラーメッセージ文字列の次のエラーが発生します。  
  
```  
"[Microsoft][SQL Server Native Client] Connection is busy with  
results for another hstmt."  
```  
  
## <a name="see-also"></a>参照  
 [ODBC&#41;&#40;の一括コピー操作の実行 ](../../relational-databases/native-client-odbc-bulk-copy-operations/performing-bulk-copy-operations-odbc.md)   
 [データの一括インポートと一括エクスポート &#40;SQL Server&#41;](../../relational-databases/import-export/bulk-import-and-export-of-data-sql-server.md)  
  
  
