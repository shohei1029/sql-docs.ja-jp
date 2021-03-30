---
title: 拡張イベント ログの診断情報へのアクセス
description: Microsoft JDBC Driver for SQL Server からイベントに関連するサーバーの拡張イベントにアクセスする方法について学習します。
ms.custom: ''
ms.date: 05/06/2020
ms.prod: sql
ms.prod_service: connectivity
ms.reviewer: ''
ms.technology: connectivity
ms.topic: conceptual
ms.assetid: a79e9468-2257-4536-91f1-73b008c376c3
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: b42d175fcc9a3cf10563b647756e8619d45b5dd3
ms.sourcegitcommit: 524a0f0cc9533188f4b14d2e78ba1cfe816b3b9a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2021
ms.locfileid: "105633257"
---
# <a name="accessing-diagnostic-information-in-the-extended-events-log"></a>拡張イベント ログの診断情報へのアクセス

[!INCLUDE[Driver_JDBC_Download](../../includes/driver_jdbc_download.md)]

[!INCLUDE[jdbc_40](../../includes/jdbc_40_md.md)] では、トレース ([ドライバー操作のトレース](tracing-driver-operation.md)) を行うことで、クライアント イベントと診断情報を簡単に関連付けることができます。 サーバーの接続リング バッファーや、拡張イベント ログ内のアプリケーション パフォーマンス情報から接続エラーなどの問題点をトレースできます。 拡張イベント ログの読み取りの詳細については、[拡張イベント](../../relational-databases/extended-events/extended-events.md)に関するページを参照してください。

## <a name="details"></a>詳細

接続操作では、[!INCLUDE[jdbcNoVersion](../../includes/jdbcnoversion_md.md)] によってクライアント接続 ID が送信されます。 接続が失敗した場合は、接続リング バッファーにアクセスし、**ClientConnectionID**  フィールドを見つけて、その失敗に関する診断情報を取得できます。 リング バッファーの詳細については、「[接続リング バッファーを使用した SQL Server 2008 での接続のトラブルシューティング](/archive/blogs/sql_protocols/connectivity-troubleshooting-in-sql-server-2008-with-the-connectivity-ring-buffer)」を参照してください。 クライアント接続 ID は、エラーが発生した場合にのみリング バッファーに記録されます。 ログイン前のパケットを送信する前に接続に失敗した場合、クライアント接続 ID は生成されません。

クライアント接続 ID は 16 バイトの GUID です。 **client_connection_id** アクションを拡張イベント セッションのイベントに追加すると、クライアント接続 ID が拡張イベントのターゲット出力に含められます。 クライアント ドライバーのより詳細な診断が必要な場合は、トレースを有効にして、接続コマンドを再実行し、トレース内の **ClientConnectionID** フィールドを確認してください。

[ISQLServerConnection インターフェイス](reference/isqlserverconnection-interface.md)を使用すると、クライアント接続 ID をプログラムで取得できます。 接続 ID は接続関連の例外にも含まれます。

接続エラーが発生する場合、サーバーの Built In Diagnostics (BID) トレース情報および接続リング バッファーに含まれるクライアント接続 ID を使用すると、クライアント接続をサーバー上の接続に関連付けることができます。 サーバー上の BID トレースの詳細については、「[SQL Server 2008 でのデータ アクセスのトレース](/previous-versions/sql/sql-server-2008/cc765421(v=sql.100))」を参照してください。 データ アクセスのトレースに関する記事には、データ アクセスのトレースに関する情報も含まれていますが、これは [!INCLUDE[jdbcNoVersion](../../includes/jdbcnoversion_md.md)] には当てはまりません。[!INCLUDE[jdbcNoVersion](../../includes/jdbcnoversion_md.md)] を使用してデータ アクセスのトレースを実行する方法については、「[ドライバー操作のトレース](tracing-driver-operation.md)」を参照してください。

JDBC Driver からは、スレッド固有のアクティビティ ID も送信されます。 TRACK_CAUSAILITY オプションを有効にしてセッションを開始した場合、アクティビティ ID は拡張イベント セッションでキャプチャされます。 アクティブな接続に関するパフォーマンスの問題については、クライアントのトレース (ActivityID フィールド) からアクティビティ ID を取得し、このアクティビティ ID を拡張イベント出力で探すことができます。

拡張イベント内のアクティビティ ID とは、16 バイトの GUID (クライアント接続 ID の GUID とは異なる) の後に 4 バイトのシーケンス番号が付いたものです。 このシーケンス番号は、スレッド内の要求順序を表しています。 ActivityId は、SQL バッチ ステートメントおよび RPC 要求用に送信されます。 ActivityId をサーバーに送信できるようにするには、Logging.Properties ファイル内で次のキーと値のペアを指定してください。

```java
com.microsoft.sqlserver.jdbc.traceactivity = on
```

`on` (大文字小文字を区別) 以外の値の場合には、ActivityId の送信は無効になります。

詳細については、「[ドライバー操作のトレース](tracing-driver-operation.md)」を参照してください。 このトレース フラグは、JDBC Driver で ActivityId をトレースおよび送信するかどうかを決定するために、対応する JDBC オブジェクト ロガーと共に使用されます。 Logging.Properties ファイルの更新に加えて、ロガー com.microsoft.sqlserver.jdbc を FINER 以上で有効にしてください。 特定のクラスによる要求に対してサーバーに ActivityId を送信する場合は、対応するクラス ロガーを FINER または FINEST で有効にします。 たとえば、クラスが SQLServerStatement であれば、ロガー com.microsoft.sqlserver.jdbc.SQLServerStatement を有効にします。

次のサンプルでは、[!INCLUDE[tsql](../../includes/tsql-md.md)] を使用して、拡張イベント セッションを開始しています。これはリング バッファーに格納されていて、RPC およびバッチ操作でクライアントから送信されたアクティビティ ID を記録します。

```sql
create event session MySession on server
add event connectivity_ring_buffer_recorded,
add event sql_statement_starting (action (client_connection_id)),
add event sql_statement_completed (action (client_connection_id)),
add event rpc_starting (action (client_connection_id)),
add event rpc_completed (action (client_connection_id))
add target ring_buffer with (track_causality=on)
```

## <a name="see-also"></a>関連項目

[JDBC ドライバーに関する問題の診断](diagnosing-problems-with-the-jdbc-driver.md)
