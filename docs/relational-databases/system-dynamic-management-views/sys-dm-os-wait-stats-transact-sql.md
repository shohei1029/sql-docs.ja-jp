---
description: sys.dm_os_wait_stats (Transact-SQL)
title: sys.dm_os_wait_stats (Transact-SQL)
ms.custom: contperf-fy21q3
ms.date: 03/15/2021
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- dm_os_wait_stats_TSQL
- dm_os_wait_stats
- sys.dm_os_wait_stats
- sys.dm_os_wait_stats_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_os_wait_stats dynamic management view
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 4ea6295e78f7d0e7df4fe2d0e4266e565c1278c6
ms.sourcegitcommit: c242f423cc3b776c20268483cfab0f4be54460d4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/25/2021
ms.locfileid: "105551488"
---
# <a name="sysdm_os_wait_stats-transact-sql"></a>sys.dm_os_wait_stats (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

実行されたスレッドによって検出されたすべての待機に関する情報を返します。 この集計ビューを使って、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] および特定のクエリとバッチに関するパフォーマンスの問題を診断できます。 [sys.dm_exec_session_wait_stats &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-session-wait-stats-transact-sql.md) は、セッションによって同様の情報を提供します。  
  
> [!NOTE] 
> これを **[!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] または [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]** から呼び出すには、という名前を使用し `sys.dm_pdw_nodes_os_wait_stats` ます。  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|wait_type|**nvarchar(60)**|待機の種類の名前。 詳細については、このトピックの「[待機の種類](#WaitTypes)」を参照してください。|  
|waiting_tasks_count|**bigint**|この待機の種類における待機の数。 このカウンターは、待機が開始するたび増加します。|  
|wait_time_ms|**bigint**|この待機の種類における総待機時間 (ミリ秒単位)。 この時間には signal_wait_time_ms が含まれます。|  
|max_wait_time_ms|**bigint**|この待機の種類における最大待機時間。|  
|signal_wait_time_ms|**bigint**|待機スレッドがシグナルを受け取ってから実行を開始するまでの時間。|  
|pdw_node_id|**int**|このディストリビューションが配置されているノードの識別子。 <br/> **適用対象**: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 、 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] |  
  
## <a name="permissions"></a>アクセス許可

で [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] は、 `VIEW SERVER STATE` 権限が必要です。   
SQL Database Basic、S0、S1 のサービス目標、およびエラスティックプール内のデータベースについては、 [サーバー管理者](/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database) アカウントまたは [Azure Active Directory 管理者](/azure/azure-sql/database/authentication-aad-overview#administrator-structure) アカウントが必要です。 その他のすべての SQL Database サービスの目的で `VIEW DATABASE STATE` は、データベースで権限が必要になります。   

##  <a name="types-of-waits"></a><a name="WaitTypes"></a> 待機の種類  
 リソース **待機** は、ワーカーが、リソースが他のワーカーによって使用されているか、まだ使用できないために利用できないリソースへのアクセスを要求したときに発生します。 リソース待機の例としては、ロック、ラッチ、ネットワーク、ディスク i/o 待機などがあります。 ロックおよびラッチ待機は同期オブジェクトで待機します  
  
**キュー待機** は、ワーカーがアイドル状態で、作業が割り当てられるのを待機しているときに発生します。 キュー待機は、デッドロック監視のようなシステム バックグラウンド タスクや、削除されたレコードのクリーンアップ タスクで最も多く発生します。 これらのタスクは、作業要求が作業キューに配置されるまで待機します。 キュー待機は、新しいパケットがキューに配置されていない場合でも、定期的にアクティブになることがあります。  
  
 **外部待機** [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] は、ワーカーが拡張ストアドプロシージャ呼び出しやリンクサーバークエリなどの外部イベントを完了するのを待機しているときに発生します。 ブロッキングの問題を診断するときには、外部待機が発生していてもワーカーがアイドル状態になっているとは限らないことに注意してください。ワーカーはアクティブ状態で外部コードを実行している可能性があるためです。  
  
 この動的管理ビューでは、既に終わった待機時間が示されます。 この動的管理ビューには、現在の待機が表示されません。  
  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]次のいずれかに該当する場合、ワーカースレッドは待機しているとは見なされません。  
  
-   リソースが使用可能になった。  
  
-   キューが空でない。  
  
-   外部プロセスが終了した。  
  
 スレッドは、待機中でなくなってもすぐに実行を開始する必要はありません。 このようなスレッドは、最初に実行可能なワーカーのキューに配置された後、スケジューラに従って実行するために、クォンタムの間待機する必要があります。  
  
 では [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 、待機時間カウンターは **bigint** 値であるため、以前のバージョンのの同等のカウンターとしてカウンターロールオーバーが発生する可能性は低く [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] なります。  
  
 クエリ実行中に発生している待機時間の種類によって、クエリにボトルネックや機能停止ポイントがあるかどうかを判断できます。 また、サーバー全体の待機時間や待機カウントが高い値を示している場合は、サーバー インスタンス内の対話型クエリの対話にボトルネックまたはホット スポットが存在していることを表しています。 たとえば、ロック待機の場合はクエリによるデータ競合、ページ I/O ラッチ待機の場合は遅い I/O 反応時間、ページ ラッチ更新待機の場合は不正なファイル レイアウトが存在していると判断できます。  
  
 この動的管理ビューの内容をリセットできます。 この T-sql コマンドは、すべてのカウンターを0にリセットします。  
```sql  
DBCC SQLPERF ('sys.dm_os_wait_stats', CLEAR);  
GO  
```  
  

  
> [!NOTE]
> これらの統計は、データベースエンジンの再起動後には保持されません。また、統計が最後にリセットされたときまたはデータベースエンジンが起動してからのすべてのデータが累積されます。 Sys.dm_os_sys_info の列を使用して、 `sqlserver_start_time` データベースエンジンの最後の起動時刻を検索します。 [](sys-dm-os-sys-info-transact-sql.md)   
  
 次の表は、タスクで発生する待機の種類の一覧です。  

|待機の種類 |説明| 
|-------------------------- |--------------------------| 
|ABR |単に情報を示すためだけに特定されます。 サポートされていません。 将来の互換性は保証されません。| 
|AM_INDBUILD_ALLOCATION |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。|
|AM_SCHEMAMGR_UNSHARED_CACHE |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|ASSEMBLY_FILTER_HASHTABLE |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|ASSEMBLY_LOAD |アセンブリ読み込みへの排他アクセス時に発生します。| 
|ASYNC_DISKPOOL_LOCK |ファイルの作成や初期化などのタスクを実行している並列スレッドを同期しようとすると発生します。| 
|ASYNC_IO_COMPLETION |タスクが非同期の非データ i/o の完了を待機しているときに発生します。 たとえば、ウォームスタンバイログ配布に関係する i/o、データベースミラーリング、一括インポート関連の操作などがあります。| 
|ASYNC_NETWORK_IO |タスクがブロックされたときに、クライアントアプリケーションが送信したすべてのデータを処理していることを待機しているときに、ネットワーク書き込み時に発生します。 クライアントがサーバーからのデータを処理していること、またはネットワークが予期したとおりに実行されていることを確認します。 クライアントがデータを十分に利用できない理由: 結果の到着中にファイルに結果を書き込む、ユーザー入力を待機する、人為的な待機が発生するなど、アプリケーションの設計上の問題。 また、クライアントシステムでは、仮想/物理メモリが不足している、100% の CPU 使用率などの問題が原因で応答が遅くなっている可能性があります。ネットワークの遅延も、この待機につながる可能性があります。|
|ASYNC_OP_COMPLETION |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|ASYNC_OP_CONTEXT_READ |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|ASYNC_OP_CONTEXT_WRITE |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|ASYNC_SOCKETDUP_IO |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|AUDIT_GROUPCACHE_LOCK |特殊なキャッシュへのアクセスを制御するロックに待機があるときに発生します。 このキャッシュには、各監査アクション グループの監査に使用されている監査報告書についての情報が格納されます。| 
|AUDIT_LOGINCACHE_LOCK |特殊なキャッシュへのアクセスを制御するロックに待機があるときに発生します。 このキャッシュには、ログイン監査アクション グループの監査に使用されている監査報告書についての情報が格納されます。| 
|AUDIT_ON_DEMAND_TARGET_LOCK |ロックに待機があるときに発生します。このロックは、監査に関係する拡張イベントのターゲットを確実に単独で初期化できるようにするために使用されるものです。| 
|AUDIT_XE_SESSION_MGR |ロックに待機があるときに発生します。このロックは、監査に関係する拡張イベントのセッションの開始と終了を同期するために使用されるものです。| 
|BACKUP |バックアップ処理の一部としてタスクがブロックされているときに発生します。| 
|BACKUP_OPERATOR |タスクがテープのマウントを待機しているときに発生します。 テープの状態を表示するには、クエリを実行 `sys.dm_io_backup_tapes` します。 マウント操作が保留中以外のときに、この待機が発生した場合は、テープ ドライブにハードウェア上の問題があると考えられます。| 
|BACKUPBUFFER |バックアップ タスクが、データまたはデータを格納するバッファーを待機しているときに発生します。 この待機は、タスクがテープのマウントを待機しているとき以外はほとんど発生しません。| 
|BACKUPIO |バックアップ タスクが、データまたはデータを格納するバッファーを待機しているときに発生します。 この待機は、タスクがテープのマウントを待機しているとき以外はほとんど発生しません。| 
|BACKUPTHREAD |タスクがバックアップ タスクの終了を待機しているときに発生します。 待機時間の長さは、数分から数時間に及ぶ場合があります。 待機中のタスクが I/O 処理中である場合は、この待機が発生しても問題はありません。| 
|BAD_PAGE_PROCESS |問題があると考えられるバックグラウンドのページ ロガーが、5 秒間隔より頻繁な実行を回避しようとする場合に発生します。 問題があると考えられるページが多くある場合、ロガーは頻繁に実行されます。| 
|BLOB_METADATA |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|BMPALLOCATION |大きなビットマップフィルターの割り当てを同期するときに、並列バッチモードプランで発生します。 待機時間が長く、クエリのチューニング (インデックスの追加など) によって削減できない場合は、並列処理のコストしきい値を調整するか、並列処理の次数を下げることを検討してください。<br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|BMPBUILD |大きなビットマップフィルターの作成を同期するときに、並列バッチモードプランで発生します。 待機時間が長く、クエリのチューニング (インデックスの追加など) によって削減できない場合は、並列処理のコストしきい値を調整するか、並列処理の次数を下げることを検討してください。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|BMPREPARTITION |大きなビットマップフィルターの再分割を同期するときに、並列バッチモードプランで発生します。 待機時間が長く、クエリのチューニング (インデックスの追加など) によって削減できない場合は、並列処理のコストしきい値を調整するか、並列処理の次数を下げることを検討してください。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|BMPREPLICATION |ワーカースレッド間で大きなビットマップフィルターのレプリケーションを同期するときに、並列バッチモードプランで実行されます。 待機時間が長く、クエリのチューニング (インデックスの追加など) によって削減できない場合は、並列処理のコストしきい値を調整するか、並列処理の次数を下げることを検討してください。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|BPSORT |複数のスレッド間でデータセットの並べ替えを同期するときに、並列バッチモードプランで実行されます。 待機時間が長く、クエリのチューニング (インデックスの追加など) によって削減できない場合は、並列処理のコストしきい値を調整するか、並列処理の次数を下げることを検討してください。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|BROKER_CONNECTION_RECEIVE_TASK |接続エンドポイントでメッセージを受信するためアクセスを待機しているときに発生します。 エンドポイントへの受信アクセスはシリアル化されます。| 
|BROKER_DISPATCHER |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|BROKER_ENDPOINT_STATE_MUTEX |Service Broker 接続エンドポイントの状態にアクセスする競合がある場合に発生します。 変更の状態へのアクセスはシリアル化されます。| 
|BROKER_EVENTHANDLER |タスクが Service Broker のプライマリイベントハンドラーを待機しているときに発生します。 この待機は、非常に短い時間の待機です。| 
|BROKER_FORWARDER |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|BROKER_INIT |各アクティブデータベースで Service Broker を初期化するときに発生します。 この待機は、発生頻度の低い待機です。| 
|BROKER_MASTERSTART |タスクが Service Broker のプライマリイベントハンドラーの開始を待機しているときに発生します。 この待機は、非常に短い時間の待機です。| 
|BROKER_RECEIVE_WAITFOR |RECEIVE WAITFOR が待機しているときに発生します。 これは、キューにメッセージを受信する準備ができていないか、ロックの競合によってキューからメッセージを受信できないことを意味する可能性があります。| 
|BROKER_REGISTERALLENDPOINTS |Service Broker 接続エンドポイントの初期化中に発生します。 この待機は、非常に短い時間の待機です。| 
|BROKER_SERVICE |ターゲットサービスに関連付けられている Service Broker 宛先リストが更新されたとき、または優先順位が変更されたときに発生します。| 
|BROKER_SHUTDOWN |Service Broker の計画されたシャットダウンがあるときに発生します。 この待機は、非常に短い時間の待機です。| 
|BROKER_START |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|BROKER_TASK_SHUTDOWN |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|BROKER_TASK_STOP |Service Broker queue タスクハンドラーがタスクをシャットダウンしようとしたときに発生します。 ステート チェックがシリアル化されます。ステート チェックは事前に実行状態になっている必要があります。| 
|BROKER_TASK_SUBMIT |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|BROKER_TO_FLUSH |Service Broker レイジーフラッシャーがメモリ内転送オブジェクトを作業テーブルにフラッシュするときに発生します。| 
|BROKER_TRANSMISSION_OBJECT |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|BROKER_TRANSMISSION_TABLE |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|BROKER_TRANSMISSION_WORK |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|BROKER_TRANSMITTER |Service Broker 送信機能が作業を待機しているときに発生します。 Service Broker には、複数のダイアログからのメッセージを1つ以上の接続エンドポイント経由でネットワーク経由で送信されるようにスケジュールする、トランスミッタと呼ばれるコンポーネントがあります。 送信機には、この目的のための2つの専用スレッドがあります。 この待機の種類は、これらの送信スレッドがトランスポート接続を使用してダイアログメッセージを送信するのを待機している場合に課金されます。 この待機の種類の値の上限 `waiting_tasks_count` は、これらの送信スレッドで断続的に機能し、パフォーマンスの問題を示すものではありません。 Service broker がまったく使用されていない場合は、2 `waiting_tasks_count` (2 トランスミッタのスレッドの場合) である必要があります。また、wait_time_ms インスタンスが起動してからの期間の2倍にする必要があります。 「 [Service broker の待機統計](/archive/blogs/sql_service_broker/service-broker-wait-types)」を参照してください。|
|BUILTIN_HASHKEY_MUTEX |この待機はインスタンスの起動後、内部データ構造の初期化中に発生することがあります。 データ構造がいったん初期化されると、それ以降に再発生することはありません。| 
|CHANGE_TRACKING_WAITFORCHANGES |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|CHECK_PRINT_RECORD |単に情報を示すためだけに特定されます。 サポートされていません。 将来の互換性は保証されません。| 
|CHECK_SCANNER_MUTEX |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|CHECK_TABLES_INITIALIZATION |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|CHECK_TABLES_SINGLE_SCAN |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|CHECK_TABLES_THREAD_BARRIER |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|CHECKPOINT_QUEUE |チェックポイント タスクが、次のチェックポイント要求を待機しているときに発生します。| 
|CHKPT  |サーバー起動時に発生し、チェックポイント スレッドに開始できることを伝えます。| 
|CLEAR_DB |データベースの開閉など、データベースの状態を変更する操作時に発生します。| 
|CLR_AUTO_EVENT |タスクが共通言語ランタイム (CLR) を実行中で、特定の自動イベントが開始されるのを待機しているときに発生します。 待機は長時間になることが一般的で、問題はありません。| 
|CLR_CRST |タスクが CLR を実行中で、重大なセクションが別のタスクによって現在使用されている場合、そのセクションを待機しているときに発生します。| 
|CLR_JOIN |タスクが CLR を実行中で、別のタスクの終了を待機しているときに発生します。 また、この待機状態は、タスク間の結合があるときに発生します。| 
|CLR_MANUAL_EVENT  |タスクが CLR を実行中で、特定の手動イベントが開始されるのを待機しているときに発生します。| 
|CLR_MEMORY_SPY |データ構造のロック取得での待機中に発生します。このデータ構造は、CLR に由来するすべての仮想メモリ割り当てを記録するために使用されます。 データ構造はロックされますが、それは並行アクセスがある場合でもデータの整合性を保持するためです。| 
|CLR_MONITOR |タスクが CLR を実行中で、モニターのロックの取得を待機しているときに発生します。| 
|CLR_RWLOCK_READER |タスクが CLR を実行中で、リーダー ロックを待機しているときに発生します。| 
|CLR_RWLOCK_WRITER |タスクが CLR を実行中で、ライター ロックを待機しているときに発生します。| 
|CLR_SEMAPHORE |タスクが CLR を実行中で、セマフォを待機しているときに発生します。| 
|CLR_TASK_START |CLR タスクが完全に開始されるのを待機しているときに発生します。| 
|CLRHOST_STATE_ACCESS |CLR ホストのデータ構造への排他アクセスを取得するのを待機する場合に発生します。 この待機の種類が発生するのは、CLR ランタイムの設定時か設定解除時です。| 
|CMEMPARTITIONED |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|CMEMTHREAD |タスクがスレッドセーフ メモリ オブジェクトで待機しているときに発生します。 複数のタスクが同じメモリ オブジェクトからメモリを割り当てようとして競合が発生している場合、待機時間は長くなる可能性があります。| 
|COLUMNSTORE_BUILD_THROTTLE |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|COLUMNSTORE_COLUMNDATASET_SESSION_LIST |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|COMMIT_TABLE |内部使用のみです。| 
|CONNECTION_ENDPOINT_LOCK |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|COUNT/YMGR |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|CREATE_DATINISERVICE |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|CXCONSUMER <a name="cxconsumer"></a>|コンシューマースレッド (親) がプロデューサースレッドによる行の送信を待機しているときに、並列クエリプランで発生します。 CXCONSUMER の待機は、そのプロデューサースレッドから行を使い果たした Exchange 反復子によって発生します。 これは、並列クエリの実行の通常の部分です。 <br /><br /> **適用対象**: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ( [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] SP2、CU3 以降 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] )、 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 、 [!INCLUDE[ssSDSMIfull](../../includes/sssdsmifull-md.md)]|
|CXPACKET <a name="cxpacket"></a>|クエリプロセッサ [交換反復子](../../relational-databases/showplan-logical-and-physical-operators-reference.md)の同期を待機しているとき、および行を生成および使用しているときに、並列クエリプランで発生します。 待機時間が長く、クエリのチューニング (インデックスの追加など) によって減らすことができない場合は、並列処理のコストしきい値を調整するか、並列処理の最大限度 (MaxDOP) を下げることを検討してください。 <br /><br /> **注:**[!INCLUDE[sssql16-md](../../includes/sssql16-md.md)]SP2 と CU3 以降では [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 、CXPACKET は、Exchange 反復子の同期と行の生成を待機していることのみを指します。 行を消費するスレッドは、CXCONSUMER の待機の種類で個別に追跡されます。 コンシューマースレッドが遅すぎると、交換反復子バッファーがいっぱいになり、CXPACKET 待機が発生する可能性があります。 <br /><br /> **注:** とでは [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] [!INCLUDE[ssSDSMIfull](../../includes/sssdsmifull-md.md)] 、CXPACKET は、行を生成するスレッドを待機することのみを参照します。 交換反復子の同期は、CXSYNC_PORT と CXSYNC_CONSUMER の待機の種類で個別に追跡されます。 行を消費するスレッドは、CXCONSUMER の待機の種類で個別に追跡されます。<br /> | 
|CXSYNC_PORT|プロデューサーとコンシューマーのスレッド間で [交換反復子](../../relational-databases/showplan-logical-and-physical-operators-reference.md) ポートのオープン、終了、同期を待機しているときに、並列クエリプランで発生します。 たとえば、クエリプランに長い並べ替え操作が含まれている場合、CXSYNC_PORT の待機が長くなる可能性があります。これは、Exchange の反復子ポートを同期する前に並べ替えを完了する必要があるためです。 <br /><br /> **適用対象**: [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 、 [!INCLUDE[ssSDSMIfull](../../includes/sssdsmifull-md.md)]| 
|CXSYNC_CONSUMER|すべてのコンシューマースレッド間で [交換反復子](../../relational-databases/showplan-logical-and-physical-operators-reference.md) 同期ポイントに到着するのを待機しているときに、並列クエリプランで発生します。 <br /><br /> **適用対象**: [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 、 [!INCLUDE[ssSDSMIfull](../../includes/sssdsmifull-md.md)]| 
|CXROWSET_SYNC |範囲の並列スキャン中に発生します。| 
|DAC_INIT |専用管理者接続の初期化中に発生します。| 
|DBCC_SCALE_OUT_EXPR_CACHE |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|DBMIRROR_DBM_EVENT |単に情報を示すためだけに特定されます。 サポートされていません。 将来の互換性は保証されません。| 
|DBMIRROR_DBM_MUTEX |単に情報を示すためだけに特定されます。 サポートされていません。 将来の互換性は保証されません。| 
|DBMIRROR_EVENTS_QUEUE |データベース ミラーリングがイベントの処理を待機しているときに発生します。| 
|DBMIRROR_SEND  |タスクが、ネットワーク層の通信バックログが消去されメッセージ送信できるようになるのを待機しているときに発生します。 通信層が過負荷になり、データベース ミラーリング データのスループットに影響が生じ始めていることを表します。| 
|DBMIRROR_WORKER_QUEUE |データベース ミラーリング ワーカー タスクが、次の作業の実行を待機していることを表します。| 
|DBMIRRORING_CMD  |タスクが、ログ レコードのディスクへのフラッシュを待機しているときに発生します。 この待機状態は、長時間続くことが予想されます。| 
|DBSEEDING_FLOWCONTROL |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|DBSEEDING_OPERATION |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|DEADLOCK_ENUM_MUTEX |[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] で同時に複数のデッドロック検索が実行されていないかどうかを、デッドロック モニターと `sys.dm_os_waiting_tasks` が確認しようとするときに発生します。| 
|DEADLOCK_TASK_SEARCH  |このリソースでの待機時間が長い場合は、サーバーが `sys.dm_os_waiting_tasks` 上で複数のクエリを実行したことにより、デッドロック モニターでデッドロック検索を実行できなくなっていることを表します。 この待機の種類は、デッドロック モニターにのみ使用されます。 `sys.dm_os_waiting_tasks` の上部のクエリは、DEADLOCK_ENUM_MUTEX を使用します。| 
|DEBUG |Transact-sql と CLR による内部同期のデバッグ中に発生します。| 
|DIRECTLOGCONSUMER_LIST |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|DIRTY_PAGE_POLL |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|DIRTY_PAGE_SYNC |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|DIRTY_PAGE_TABLE_LOCK |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|DISABLE_VERSIONING |一番最初のアクティブなトランザクションのタイムスタンプが、状態が変化し始めたときのタイムスタンプより後かどうかを確認するために、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] がバージョン トランザクション マネージャーをポーリングしたときに発生します。 最初のトランザクションのタイムスタンプが状態変化のタイムスタンプより後の場合、ALTER DATABASE ステートメントの実行前に開始されたスナップショット トランザクションはすべて終了しています。 この待機状態は、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] が ALTER DATABASE ステートメントを使用してバージョン管理を無効にするときに使用されます。| 
|DISKIO_SUSPEND |外部バックアップがアクティブで、タスクがファイルへのアクセスを待機しているときに発生します。 これは、ユーザー プロセスの待機が発生するたびに報告されます。 1 つのユーザー プロセスの待機が 5 回を超えた場合は、外部バックアップの完了に時間がかかりすぎている可能性があります。| 
|DISPATCHER_PRIORITY_QUEUE_SEMAPHORE |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|DISPATCHER_QUEUE_SEMAPHORE |ディスパッチャー プールのスレッドが、まだ多くの作業の処理を待機しているときに発生します。 ディスパッチャーがアイドル状態の場合、この待機の種類の待機時間は長くなると予想されます。| 
|DLL_LOADING_MUTEX |XML パーサー DLL が読み込まれるのを待機しているときに 1 回発生します。| 
|DPT_ENTRY_LOCK |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|DROP_DATABASE_TIMER_TASK |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|DROPTEMP |一時オブジェクトの削除を試行して失敗した場合に、次の削除を試行するまでの間に発生します。 この待機時間は、削除の試行が失敗するたびに指数関数的に増えます。| 
|DTC |タスクが、状態遷移の管理に使用されるイベントで待機しているときに発生します。 この状態 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] は、MS dtc サービスが使用できなくなったという通知を受信した後に、Microsoft 分散トランザクションコーディネーター (MS dtc) トランザクションの復旧が行われるタイミングを制御します。| 
|DTC_ABORT_REQUEST  |MS DTC ワーカー セッションが、MS DTC トランザクションの所有権取得を待機しているときに発生します。 MS DTC がトランザクションの所有権を取得した後、セッションはそのトランザクションをロールバックできます。 一般に、セッションが待機するのは、そのトランザクションが別のセッションで使用されている場合です。| 
|DTC_RESOLVE |複数のデータベースにまたがるトランザクションで、復旧タスクが、トランザクションの結果をクエリするため master データベースを待機しているときに発生します。| 
|DTC_STATE  |内部の MS DTC グローバル状態オブジェクトに対する変更を保護するイベントで、タスクが待機しているときに発生します。 この待機状態は非常に短い時間保持されます。| 
|DTC_TMDOWN_REQUEST |MS DTC ワーカー セッションで、MS DTC サービスが使用できないという通知を [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] が受信したときに発生します。 ワーカーはまず MS DTC 復旧プロセスの開始を待機し、 次に、ワーカーが操作している分散トランザクションの結果取得を待機します。 この待機は、MS DTC サービスとの接続が再度確立されるまで継続される場合があります。| 
|DTC_WAITFOR_OUTCOME  |MS DTC がアクティブになり、準備されたトランザクションの解決が可能になるのを、復旧タスクが待機しているときに発生します。| 
|DTCNEW_ENLIST |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|DTCNEW_PREPARE |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|DTCNEW_RECOVERY |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|DTCNEW_TM |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|DTCNEW_TRANSACTION_ENLISTMENT |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|DTCPNTSYNC |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|DUMP_LOG_COORDINATOR  |メイン タスクが、サブタスクによるデータ生成を待機しているときに発生します。 通常、この待機状態は発生しません。 待機が長時間になる場合は、予期しないブロックが発生している可能性があり、 サブタスクを調査する必要があります。| 
|DUMP_LOG_COORDINATOR_QUEUE |内部使用のみです。| 
|DUMPTRIGGER |単に情報を示すためだけに特定されます。 サポートされていません。 将来の互換性は保証されません。| 
|EC |単に情報を示すためだけに特定されます。 サポートされていません。 将来の互換性は保証されません。| 
|EE_PMOLOCK |ステートメントの実行時、特定の種類のメモリ割り当てを同期するときに発生します。| 
|EE_SPECPROC_MAP_INIT |内部プロシージャ ハッシュ テーブルの作成における同期中に発生します。 この待機は、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンスの開始後、ハッシュ テーブルに最初にアクセスするときにのみ発生します。| 
|ENABLE_EMPTY_VERSIONING |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|ENABLE_VERSIONING |データベースがスナップショット分離が許可されている状態に移行できることを宣言するために、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] が、このデータベースにおけるすべての更新トランザクションの終了を待機しているときに発生します。 この待機状態は、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] が ALTER DATABASE ステートメントを使用してスナップショット分離を有効にするときに使用されます。| 
|ERROR_REPORTING_MANAGER |複数のエラー ログの初期化における同期中に発生します。| 
|EXCHANGE |並列クエリの実行時、クエリ プロセッサ交換反復子での同期中に発生します。| 
|EXECSYNC  |並列クエリの実行時、交換反復子に関係のない領域での、クエリ プロセッサによる同期中に発生します。 このような領域の例としては、ビットマップ、ラージ バイナリ オブジェクト (LOB)、スプール反復子などがあります。 LOB では、この待機状態が頻繁に使用されることがあります。| 
|EXECUTION_PIPE_EVENT_INTERNAL |バッチ実行のプロデューサー部とコンシューマー部との間で同期しているときに発生します。これらの部は接続コンテキストを通じて送信されます。| 
|EXTERNAL_RG_UPDATE |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|EXTERNAL_SCRIPT_NETWORK_IO |内部使用のみです。 <br /><br /> **に適用さ** れます: を [!INCLUDE [sssql17-md](../../includes/sssql17-md.md)] 通じて現在のです。| 
|EXTERNAL_SCRIPT_PREPARE_SERVICE |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|EXTERNAL_SCRIPT_SHUTDOWN |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|EXTERNAL_WAIT_ON_LAUNCHER、 |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|FABRIC_HADR_TRANSPORT_CONNECTION |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|FABRIC_REPLICA_CONTROLLER_LIST |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|FABRIC_REPLICA_CONTROLLER_STATE_AND_CONFIG |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|FABRIC_REPLICA_PUBLISHER_EVENT_PUBLISH |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|FABRIC_REPLICA_PUBLISHER_SUBSCRIBER_LIST |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|FABRIC_WAIT_FOR_BUILD_REPLICA_EVENT_PROCESSING |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|FAILPOINT |単に情報を示すためだけに特定されます。 サポートされていません。 将来の互換性は保証されません。| 
|FCB_REPLICA_READ  |スナップショット (または DBCC によって作成された一時スナップショット) のスパース ファイルの読み取りを同期するときに発生します。| 
|FCB_REPLICA_WRITE  |スナップショット (または DBCC によって作成された一時スナップショット) のスパース ファイルに対するページのプッシュまたはプルを同期するときに発生します。| 
|FEATURE_SWITCHES_UPDATE |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|FFT_NSO_DB_KILL_FLAG |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|FFT_NSO_DB_LIST |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|FFT_NSO_FCB |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|FFT_NSO_FCB_FIND |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|FFT_NSO_FCB_PARENT |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|FFT_NSO_FCB_RELEASE_CACHED_ENTRIES |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|FFT_NSO_FCB_STATE |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|FFT_NSO_FILEOBJECT |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|FFT_NSO_TABLE_LIST |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|FFT_NTFS_STORE |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|FFT_RECOVERY |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|FFT_RSFX_COMM |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|FFT_RSFX_WAIT_FOR_MEMORY |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|FFT_STARTUP_SHUTDOWN |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|FFT_STORE_DB |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|FFT_STORE_ROWSET_LIST |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|FFT_STORE_TABLE |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|FILE_VALIDATION_THREADS |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|FILESTREAM_CACHE |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|FILESTREAM_CHUNKER |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|FILESTREAM_CHUNKER_INIT |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|FILESTREAM_FCB |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|FILESTREAM_FILE_OBJECT |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|FILESTREAM_WORKITEM_QUEUE |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|FILETABLE_SHUTDOWN |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|FOREIGN_REDO |内部使用のみです。 <br /><br /> **に適用さ** れます: を [!INCLUDE [sssql17-md](../../includes/sssql17-md.md)] 通じて現在のです。| 
|FORWARDER_TRANSITION |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|FS_FC_RWLOCK |FILESTREAM ガベージ コレクターが次のいずれかを実行するために待機しているときに発生します。| 
|FS_GARBAGE_COLLECTOR_SHUTDOWN |FILESTREAM ガベージ コレクターがクリーンアップ タスクの完了を待機しているときに発生します。| 
|FS_HEADER_RWLOCK |FILESTREAM データ コンテナーの FILESTREAM ヘッダーへのアクセスの取得を待機しているときに発生します。待機するのは、FILESTREAM ヘッダー ファイル (Filestream.hdr) の内容を読み取るかまたは更新するためです。| 
|FS_LOGTRUNC_RWLOCK |次のいずれかを実行するために、FILESTREAM ログの切り捨てへのアクセスの取得を待機しているときに発生します。| 
|FSA_FORCE_OWN_XACT |FILESTREAM ファイル I/O 操作が、関連付けられているトランザクションにバインドされる必要があるのに、別のセッションがそのトランザクションを現在所有している場合に発生します。| 
|FSAGENT |FILESTREAM ファイル I/O 操作が、FILESTREAM エージェント リソースを待機しているときに発生します。このリソースは別のファイル I/O 操作が使用中です。| 
|FSTR_CONFIG_MUTEX |別の FILESTREAM 機能の再構成が完了するのを待機しているときに発生します。| 
|FSTR_CONFIG_RWLOCK |FILESTREAM 構成パラメーターへのアクセスのシリアル化を待機しているときに発生します。| 
|FT_COMPROWSET_RWLOCK |フル テキストがフラグメントのメタデータの操作を待機しています。 単に情報を示すためだけに記述されます。 サポートされていません。 将来の互換性は保証されません。| 
|FT_IFTS_RWLOCK |フルテキストが内部同期を待機しています。 単に情報を示すためだけに記述されます。 サポートされていません。 将来の互換性は保証されません。| 
|FT_IFTS_SCHEDULER_IDLE_WAIT |フルテキストのスケジューラが待機の種類をスリープしています。 スケジューラはアイドル状態です。| 
|FT_IFTSHC_MUTEX |フルテキストが fdhost 制御操作を待機しています。 単に情報を示すためだけに記述されます。 サポートされていません。 将来の互換性は保証されません。| 
|FT_IFTSISM_MUTEX |フル テキストが通信操作を待機しています。 単に情報を示すためだけに記述されます。 サポートされていません。 将来の互換性は保証されません。| 
|FT_MASTER_MERGE |フル テキストがマスター マージ操作を待機しています。 単に情報を示すためだけに記述されます。 サポートされていません。 将来の互換性は保証されません。| 
|FT_MASTER_MERGE_COORDINATOR |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|FT_METADATA_MUTEX |単に情報を示すためだけに記述されます。 サポートされていません。 将来の互換性は保証されません。| 
|FT_PROPERTYLIST_CACHE |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|FT_RESTART_CRAWL |一時的なエラーから復旧するために、正常と認識されている最後の時点からフルテキスト クロールを再開する必要があるときに発生します。 この待機が発生した場合、その設定を現在処理中のワーカー タスクは、現在のステップを完了するか終了することを許可されます。| 
|FULLTEXT GATHERER |フルテキスト操作の同期中に発生します。| 
|GDMA_GET_RESOURCE_OWNER |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|GHOSTCLEANUP_UPDATE_STATS |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE [sssql17-md](../../includes/sssql17-md.md)] 以降。| 
|GHOSTCLEANUPSYNCMGR |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|GLOBAL_QUERY_CANCEL |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE [sssql17-md](../../includes/sssql17-md.md)] 以降。| 
|GLOBAL_QUERY_CLOSE |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|GLOBAL_QUERY_CONSUMER |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE [sssql17-md](../../includes/sssql17-md.md)] 以降。| 
|GLOBAL_QUERY_PRODUCER |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE [sssql17-md](../../includes/sssql17-md.md)] 以降。| 
|GLOBAL_TRAN_CREATE |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|GLOBAL_TRAN_UCS_SESSION |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|GUARDIAN |単に情報を示すためだけに特定されます。 サポートされていません。 将来の互換性は保証されません。| 
|HADR_AG_MUTEX |Always On DDL ステートメントまたは Windows Server フェールオーバークラスタリングコマンドが、可用性グループの構成に対する排他的読み取り/書き込みアクセスを待機しているときに発生します。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|HADR_AR_CRITICAL_SECTION_ENTRY |Always On DDL ステートメントまたは Windows Server フェールオーバークラスタリングコマンドが、関連付けられている可用性グループのローカルレプリカのランタイム状態に対する排他的読み取り/書き込みアクセスを待機しているときに発生します。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|HADR_AR_MANAGER_MUTEX |可用性レプリカのシャットダウンが起動完了を待機しているとき、または可用性レプリカの起動がシャットダウン完了を待機しているときに発生します。 内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|HADR_AR_UNLOAD_COMPLETED |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|HADR_ARCONTROLLER_NOTIFICATIONS_SUBSCRIBER_LIST |可用性レプリカイベントのパブリッシャー (状態の変更や構成の変更など) が、イベントサブスクライバーの一覧に対する排他的な読み取り/書き込みアクセスを待機しています。 内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|HADR_BACKUP_BULK_LOCK |Always On プライマリデータベースは、セカンダリデータベースからバックアップ要求を受け取り、BulkOp ロックの取得または解放時にバックグラウンドスレッドが要求の処理を完了するのを待機しています。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|HADR_BACKUP_QUEUE |Always On プライマリデータベースのバックアップバックグラウンドスレッドが、セカンダリデータベースからの新しい作業要求を待機しています。 (通常、これは、プライマリデータベースが BulkOp ログを保持していて、セカンダリデータベースがプライマリデータベースがロックを解放できることを示すために待機している場合に発生します)。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|HADR_CLUSAPI_CALL |[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]スレッドが、Windows Server フェールオーバークラスタリング api を起動するために、非プリエンプティブモード (によってスケジュールされ [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] た) からプリエンプティブモード (オペレーティングシステムによってスケジュール) への切り替えを待機しています。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|HADR_COMPRESSED_CACHE_SYNC |複数のセカンダリデータベースに送信されるログブロックの冗長圧縮を回避するために使用される、圧縮されたログブロックのキャッシュへのアクセスを待機しています。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|HADR_CONNECTIVITY_INFO |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|HADR_DATABASE_FLOW_CONTROL |メッセージがキュー登録の最大数に達している場合に、パートナーにメッセージが送信されるのを待機しています。 ログ スキャンがネットワーク送信よりも高速に実行されていることを示します。 これは、ネットワーク送信が予想よりも遅い場合にのみ問題になります。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|HADR_DATABASE_VERSIONING_STATE |Always On セカンダリデータベースのバージョン管理状態の変更に対して発生します。 この待機は内部データ構造に対して行われ、通常、データアクセスに直接影響することはありません。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|HADR_DATABASE_WAIT_FOR_RECOVERY |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|HADR_DATABASE_WAIT_FOR_RESTART |Always On 可用性グループの制御下で、データベースが再起動するのを待機しています。 この待機はここで想定されるため、通常の状況下ではこれは顧客の問題ではありません。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|HADR_DATABASE_WAIT_FOR_TRANSITION_TO_VERSIONING |Always On 可用性グループの読み取り可能なセカンダリデータベース内のオブジェクトに対するクエリは、行のバージョン管理でブロックされ、セカンダリレプリカが読み取りワークロードに対して有効になっているときに実行されていたすべてのトランザクションのコミットまたはロールバックを待機しています。 この待機の種類では、行バージョンが使用できるようになってから、スナップショット分離でのクエリが実行されることが保証されます。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|HADR_DB_COMMAND |会話メッセージへの応答を待機しています (相手側からの明示的な応答が必要です)。 Always On の会話メッセージインフラストラクチャを使用してください。 この待機の種類は、さまざまなメッセージの種類で使用します。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|HADR_DB_OP_COMPLETION_SYNC |会話メッセージへの応答を待機しています (相手側からの明示的な応答が必要です)。 Always On の会話メッセージインフラストラクチャを使用してください。 この待機の種類は、さまざまなメッセージの種類で使用します。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|HADR_DB_OP_START_SYNC |Always On DDL ステートメントまたは Windows Server フェールオーバークラスタリングコマンドが、可用性データベースとその実行時状態へのシリアル化アクセスを待機しています。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|HADR_DBR_SUBSCRIBER |可用性レプリカイベント (状態の変更や構成の変更など) のパブリッシャーが、可用性データベースに対応するイベントサブスクライバーの実行時状態に対する排他的読み取り/書き込みアクセスを待機しています。 内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|HADR_DBR_SUBSCRIBER_FILTER_LIST |可用性レプリカイベント (状態の変更や構成の変更など) のパブリッシャーが、可用性データベースに対応するイベントサブスクライバーの一覧に対する排他的読み取り/書き込みアクセスを待機しています。 内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|HADR_DBSEEDING |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|HADR_DBSEEDING_LIST |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|HADR_DBSTATECHANGE_SYNC |データベース レプリカの内部状態の更新に対するコンカレンシー制御の待機です。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|HADR_FABRIC_CALLBACK |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|HADR_FILESTREAM_BLOCK_FLUSH |FILESTREAM Always On トランスポートマネージャーは、ログブロックの処理が完了するまで待機しています。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|HADR_FILESTREAM_FILE_CLOSE |FILESTREAM Always On トランスポートマネージャーは、次の FILESTREAM ファイルが処理され、ハンドルが閉じられるまで待機しています。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|HADR_FILESTREAM_FILE_REQUEST |Always On セカンダリレプリカは、元に戻すときに、プライマリレプリカが要求されたすべての FILESTREAM ファイルを送信するのを待機しています。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|HADR_FILESTREAM_IOMGR |FILESTREAM Always On トランスポートマネージャーが、起動中またはシャットダウン中に FILESTREAM Always On i/o マネージャーを保護する R/W ロックを待機しています。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|HADR_FILESTREAM_IOMGR_IOCOMPLETION |FILESTREAM Always On i/o マネージャーが i/o 完了を待機しています。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|HADR_FILESTREAM_MANAGER |FILESTREAM Always On トランスポートマネージャーが、起動中またはシャットダウン中に FILESTREAM Always On transport manager を保護する R/W ロックを待機しています。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|HADR_FILESTREAM_PREPROC |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|HADR_GROUP_COMMIT |トランザクションのコミット処理では、複数のコミットログレコードを1つのログブロックに入れることができるように、グループのコミットが許可されるのを待機しています。 この待機は、I/O のログ、キャプチャ、および送信の各操作を最適化する、想定される状況です。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|HADR_LOGCAPTURE_SYNC |スキャンを作成または破棄する場合の、キャプチャのログまたはオブジェクトの適用に関連するコンカレンシー制御です。 これは、パートナーが状態または接続状態を変更する場合に想定される待機です。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|HADR_LOGCAPTURE_WAIT |ログ レコードが使用可能になるのを待機しています。 接続によって新しいログ レコードが生成されるのを待機している場合、またはキャッシュにないログを読み取る際の I/O 完了を待機している場合に発生します。 ログスキャンがログの最後まで検出された場合、またはディスクから読み取られた場合、これは予想される待機です。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|HADR_LOGPROGRESS_SYNC |データベースレプリカのログの進行状況を更新するときに、同時実行制御を待機しています。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|HADR_NOTIFICATION_DEQUEUE |Windows Server フェールオーバークラスタリングの通知を処理するバックグラウンドタスクが、次の通知を待機しています。 内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|HADR_NOTIFICATION_WORKER_EXCLUSIVE_ACCESS |Always On 可用性レプリカマネージャーは、Windows Server フェールオーバークラスタリングの通知を処理するバックグラウンドタスクのランタイム状態へのシリアル化アクセスを待機しています。 内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|HADR_NOTIFICATION_WORKER_STARTUP_SYNC |バックグラウンド タスクが、Windows Server フェールオーバー クラスタリングの通知を処理するバックグラウンド タスクの起動完了を待機しています。 内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|HADR_NOTIFICATION_WORKER_TERMINATION_SYNC |バックグラウンド タスクが、Windows Server フェールオーバー クラスタリングの通知を処理するバックグラウンド タスクの終了を待機しています。 内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|HADR_PARTNER_SYNC |パートナーの一覧でのコンカレンシー制御の待機です。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|HADR_READ_ALL_NETWORKS |WSFC ネットワークの一覧に対する読み取りまたは書き込みアクセスの取得を待機しています。 内部使用のみです。 注: エンジンは、動的管理ビュー (など) で使用される WSFC ネットワークの一覧を保持 `sys.dm_hadr_cluster_networks` するか、wsfc ネットワーク情報を参照する transact-sql ステートメント Always On を検証します。 この一覧は、エンジンの起動時、WSFC 関連の通知、および内部 Always On の再起動時に更新されます (たとえば、WSFC クォーラムの損失や取り戻しなど)。 通常、この一覧の更新中はタスクがブロックされます。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|HADR_RECOVERY_WAIT_FOR_CONNECTION |復旧を実行する前に、セカンダリ データベースがプライマリ データベースに接続するのを待機しています。 これは想定される待機であり、プライマリへの接続確立に時間がかかる場合は長くなる可能性があります。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|HADR_RECOVERY_WAIT_FOR_UNDO |データベース復旧が、セカンダリ データベースが復帰および初期化フェーズを完了し、プライマリ データベースと共通のログ ポイントに戻るのを待機しています。 これは、フェールオーバー後に予想される待機です。 元に戻す処理は、Windows システムモニター (perfmon.exe) および動的管理ビューを使用して追跡できます。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|HADR_REPLICAINFO_SYNC |コンカレンシー制御が現在のレプリカの状態を更新するのを待機しています。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|HADR_SEEDING_CANCELLATION |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|HADR_SEEDING_FILE_LIST |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|HADR_SEEDING_LIMIT_BACKUPS |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|HADR_SEEDING_SYNC_COMPLETION |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|HADR_SEEDING_TIMEOUT_TASK |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|HADR_SEEDING_WAIT_FOR_COMPLETION |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|HADR_SYNC_COMMIT |同期されているセカンダリ データベースのトランザクション コミット処理が、ログを書き込むのを待機しています。 この待機は、Transaction Delay パフォーマンス カウンターの影響も受けます。 この待機の種類は、同期された可用性グループに必要であり、セカンダリデータベースへのログの送信、書き込み、および確認の時間を示します。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|HADR_SYNCHRONIZING_THROTTLE |トランザクション コミット処理が、同期中のセカンダリ データベースに対して、プライマリのログの末尾からの遅れを取り戻して同期済み状態に遷移することを許可するのを待機しています。 これは、セカンダリデータベースが遅れているときに想定される待機です。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|HADR_TDS_LISTENER_SYNC |内部 Always On システムまたは WSFC クラスターは、リスナーを開始または停止するように要求します。 この要求の処理は常に非同期であり、冗長な要求を削除するためのメカニズムがあります。 構成の変更によって、このプロセスが中断されることもあります。 このリスナー同期機構に関連するすべての待機で、この待機の種類が使用されます。 内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|HADR_TDS_LISTENER_SYNC_PROCESSING |可用性グループリスナーの開始または停止を必要とする Always On Transact-sql ステートメントの最後に使用されます。 開始/停止操作は非同期的に実行されるため、リスナーの状況が判明するまで、ユーザースレッドはこの待機の種類を使用してブロックします。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|HADR_THROTTLE_LOG_RATE_GOVERNOR |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|HADR_THROTTLE_LOG_RATE_MISMATCHED_SLO | Geo レプリケーションのセカンダリが、プライマリよりも低いコンピューティングサイズ (SLO) で構成されている場合に発生します。 プライマリデータベースは、セカンダリによるログの遅延消費によって制限されます。 これは、セカンダリデータベースのコンピューティング能力が、プライマリデータベースの変化率に対応しきれないことが原因で発生します。 <br /><br /> **適用対象**: Azure SQL Database| 
|HADR_THROTTLE_LOG_RATE_LOG_SIZE |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE [sssql17-md](../../includes/sssql17-md.md)] 以降。| 
|HADR_THROTTLE_LOG_RATE_SEEDING |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE [sssql17-md](../../includes/sssql17-md.md)] 以降。| 
|HADR_THROTTLE_LOG_RATE_SEND_RECV_QUEUE_SIZE |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE [sssql17-md](../../includes/sssql17-md.md)] 以降。| 
|HADR_TIMER_TASK |タイマー タスク オブジェクトのロックを待機しています。またこれは、実行される作業の間の実際の待機に使用されます。 たとえば、10秒ごとに実行されるタスクの場合、1回の実行後に Always On 可用性グループはタスクの再スケジュールを10秒間待機し、待機はここに含めます。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|HADR_TRANSPORT_DBRLIST |トランスポート層のデータベース レプリカの一覧に対するアクセスを待機しています。 一覧に対するアクセスを許可するスピンロックで使用されます。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|HADR_TRANSPORT_FLOW_CONTROL |未応答の Always On メッセージの数が、フロー制御のしきい値を超えたときに待機しています。 これは可用性レプリカ間の処理です (データベース間ではありません)。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|HADR_TRANSPORT_SESSION |Always On 可用性グループは、基になるトランスポートの状態を変更またはアクセス中に待機しています。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|HADR_WORK_POOL |Always On 可用性グループのバックグラウンド作業タスクオブジェクトでの同時実行制御の待機。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|HADR_WORK_QUEUE |新しい作業が割り当てられるのを待機している可用性グループのバックグラウンドワーカースレッド Always On します。 これは、標準状態の新しい作業を待機している準備完了ワーカーがある場合に想定される待機です。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|HADR_XRF_STACK_ACCESS |Always On 可用性データベースの拡張復旧分岐スタックにアクセス (検索、追加、および削除) する。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|HCCO_CACHE |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|HK_RESTORE_FILEMAP |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|HKCS_PARALLEL_MIGRATION |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|HKCS_PARALLEL_RECOVERY |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|HTBUILD |ハッシュ結合/集計の入力側でハッシュテーブルの作成を同期するときに、並列バッチモードプランで発生します。 待機時間が長く、クエリのチューニング (インデックスの追加など) によって削減できない場合は、並列処理のコストしきい値を調整するか、並列処理の次数を下げることを検討してください。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|HTDELETE |ハッシュ結合または集計の終了時に同期するときに、並列バッチモードプランで実行されます。 待機時間が長く、クエリのチューニング (インデックスの追加など) によって削減できない場合は、並列処理のコストしきい値を調整するか、並列処理の次数を下げることを検討してください。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|HTMEMO |ハッシュテーブルをスキャンしてハッシュ結合/集計で一致/一致しない出力を行う前に同期するときに、並列バッチモードプランで実行されます。 待機時間が長く、クエリのチューニング (インデックスの追加など) によって削減できない場合は、並列処理のコストしきい値を調整するか、並列処理の次数を下げることを検討してください。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|HTREINIT |次の部分結合でハッシュ結合または集計をリセットする前に同期するときに、並列バッチモードプランで実行されます。 待機時間が長く、クエリのチューニング (インデックスの追加など) によって削減できない場合は、並列処理のコストしきい値を調整するか、並列処理の次数を下げることを検討してください。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|HTREPARTITION パーティション |ハッシュ結合/集計の入力側でハッシュテーブルの再分割を同期するときに、並列バッチモードプランで発生します。 待機時間が長く、クエリのチューニング (インデックスの追加など) によって削減できない場合は、並列処理のコストしきい値を調整するか、並列処理の次数を下げることを検討してください。<br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|HTTP_ENUMERATION |起動時に発生し、HTTP を開始するため HTTP エンドポイントを列挙します。| 
|HTTP_START |接続が HTTP の初期化完了を待機しているときに発生します。| 
|HTTP_STORAGE_CONNECTION |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|IMPPROV_IOWAIT |[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] が、一括読み込み I/O の終了を待機しているときに発生します。| 
|INSTANCE_LOG_RATE_GOVERNOR |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|INTERNAL_TESTING |単に情報を示すためだけに特定されます。 サポートされていません。 将来の互換性は保証されません。| 
|IO_AUDIT_MUTEX |トレース イベント バッファーの同期中に発生します。| 
|IO_COMPLETION |I/O 操作の完了を待機しているときに発生します。 この待機の種類は通常、データ ページ以外の I/O を表します。 データページの i/o 完了の待機は、PAGEIOLATCH 待機として表示され \_ \* ます。| 
|IO_QUEUE_LIMIT |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|IO_RETRY |I/O 操作 (ディスクの読み取り/書き込みなど) に失敗すると発生します。原因はリソース不足による再試行です。| 
|IOAFF_RANGE_QUEUE |単に情報を示すためだけに特定されます。 サポートされていません。 将来の互換性は保証されません。| 
|KSOURCE_WAKEUP |サービス コントロール タスクによって、サービス コントロール マネージャーからの要求を待機しているときに使用されます。 待機は長時間になることが予想されますが、問題はありません。| 
|KTM_ENLISTMENT |単に情報を示すためだけに特定されます。 サポートされていません。 将来の互換性は保証されません。| 
|KTM_RECOVERY_MANAGER |単に情報を示すためだけに特定されます。 サポートされていません。 将来の互換性は保証されません。| 
|KTM_RECOVERY_RESOLUTION |単に情報を示すためだけに特定されます。 サポートされていません。 将来の互換性は保証されません。| 
|LATCH_DT  |DT (破棄) ラッチを待機しているときに発生します。 これには、バッファー ラッチまたはトランザクション マーク ラッチは含まれません。 ラッチ待機の一覧については、「 \_ \* 」を参照 `sys.dm_os_latch_stats` してください。 `sys.dm_os_latch_stats` では、LATCH_NL、LATCH_SH、LATCH_UP、LATCH_EX、および LATCH_DT の待機はグループ化されます。| 
|LATCH_EX  |EX (排他) ラッチを待機しているときに発生します。 これには、バッファー ラッチまたはトランザクション マーク ラッチは含まれません。 ラッチ待機の一覧については、「 \_ \* 」を参照 `sys.dm_os_latch_stats` してください。 `sys.dm_os_latch_stats` では、LATCH_NL、LATCH_SH、LATCH_UP、LATCH_EX、および LATCH_DT の待機はグループ化されます。| 
|LATCH_KP  |KP (保持) ラッチを待機しているときに発生します。 これには、バッファー ラッチまたはトランザクション マーク ラッチは含まれません。 ラッチ待機の一覧については、「 \_ \* 」を参照 `sys.dm_os_latch_stats` してください。 `sys.dm_os_latch_stats` では、LATCH_NL、LATCH_SH、LATCH_UP、LATCH_EX、および LATCH_DT の待機はグループ化されます。| 
|LATCH_NL  |単に情報を示すためだけに特定されます。 サポートされていません。 将来の互換性は保証されません。| 
|LATCH_SH  |SH (共有) ラッチを待機しているときに発生します。 これには、バッファー ラッチまたはトランザクション マーク ラッチは含まれません。 ラッチ待機の一覧については、「 \_ \* 」を参照 `sys.dm_os_latch_stats` してください。 `sys.dm_os_latch_stats` では、LATCH_NL、LATCH_SH、LATCH_UP、LATCH_EX、および LATCH_DT の待機はグループ化されます。| 
|LATCH_UP  |UP (更新) ラッチを待機しているときに発生します。 これには、バッファー ラッチまたはトランザクション マーク ラッチは含まれません。 ラッチ待機の一覧については、「 \_ \* 」を参照 `sys.dm_os_latch_stats` してください。 `sys.dm_os_latch_stats` では、LATCH_NL、LATCH_SH、LATCH_UP、LATCH_EX、および LATCH_DT の待機はグループ化されます。| 
|LAZYWRITER_SLEEP  |レイジーライタータスクが中断されたときに発生します。 待機中のバックグラウンド タスクで費やされた時間を測定することができます。 ユーザーの機能停止を検索しているときには、この待機状態は考慮しないでください。| 
|LCK_M_BU  |タスクが一括更新 (BU) ロックの取得を待機しているときに発生します。 詳細については、「 [一括更新ロック](../sql-server-transaction-locking-and-row-versioning-guide.md#bulk_update) 」を参照してください。| 
|LCK_M_BU_ABORT_BLOCKERS |タスクがアボート ブロッカーによる一括更新 (BU) ロックの取得を待機しているときに発生します  (ALTER TABLE および ALTER INDEX の優先度の低い待機オプションに関連します)。 詳細については、「 [一括更新ロック](../sql-server-transaction-locking-and-row-versioning-guide.md#bulk_update) 」を参照してください。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|LCK_M_BU_LOW_PRIORITY |タスクが優先度の低い一括更新 (BU) ロックの取得を待機しているときに発生します  (ALTER TABLE および ALTER INDEX の優先度の低い待機オプションに関連します)。 詳細については、「 [一括更新ロック](../sql-server-transaction-locking-and-row-versioning-guide.md#bulk_update) 」を参照してください。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|LCK_M_IS |タスクがインテント共有 (IS) ロックの取得を待機しているときに発生します。 詳細については [インテントロック](../sql-server-transaction-locking-and-row-versioning-guide.md#intent) を参照してください| 
|LCK_M_IS_ABORT_BLOCKERS |タスクがアボート ブロッカーによるインテント共有 (IS) ロックの取得を待機しているときに発生します  (ALTER TABLE および ALTER INDEX の優先度の低い待機オプションに関連します)。詳細については、 [インテントロック](../sql-server-transaction-locking-and-row-versioning-guide.md#intent) に関する説明を参照してください。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|LCK_M_IS_LOW_PRIORITY |タスクが優先度の低いインテント共有 (IS) ロックの取得を待機しているときに発生します。 (ALTER TABLE および ALTER INDEX の優先度の低い待機オプションに関連します)。 詳細については、 [インテントロック](../sql-server-transaction-locking-and-row-versioning-guide.md#intent) に関する説明を参照してください。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|LCK_M_IU |タスクがインテント更新 (IU) ロックの取得を待機しているときに発生します。 詳細については [インテントロック](../sql-server-transaction-locking-and-row-versioning-guide.md#intent) を参照してください| 
|LCK_M_IU_ABORT_BLOCKERS |タスクがアボート ブロッカーによるインテント更新 (IU) ロックの取得を待機しているときに発生します  (ALTER TABLE および ALTER INDEX の優先度の低い待機オプションに関連します)。詳細については、 [インテントロック](../sql-server-transaction-locking-and-row-versioning-guide.md#intent) に関する説明を参照してください。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|LCK_M_IU_LOW_PRIORITY |タスクが優先度の低いインテント更新 (IU) ロックの取得を待機しているときに発生します  (ALTER TABLE および ALTER INDEX の優先度の低い待機オプションに関連します)。 詳細については、 [インテントロック](../sql-server-transaction-locking-and-row-versioning-guide.md#intent) に関する説明を参照してください。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|LCK_M_IX  |タスクがインテント排他 (IX) ロックの取得を待機しているときに発生します。 詳細については [インテントロック](../sql-server-transaction-locking-and-row-versioning-guide.md#intent) を参照してください| 
|LCK_M_IX_ABORT_BLOCKERS |タスクがアボート ブロッカーによるインテント排他 (IX) ロックの取得を待機しているときに発生します  (ALTER TABLE および ALTER INDEX の優先度の低い待機オプションに関連します)。 詳細については、 [インテントロック](../sql-server-transaction-locking-and-row-versioning-guide.md#intent) に関する説明を参照してください。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|LCK_M_IX_LOW_PRIORITY |タスクが優先度の低いインテント排他 (IX) ロックの取得を待機しているときに発生します。 (ALTER TABLE および ALTER INDEX の優先度の低い待機オプションに関連します)。 詳細については、 [インテントロック](../sql-server-transaction-locking-and-row-versioning-guide.md#intent) に関する説明を参照してください。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|LCK_M_RIn_NL  |タスクが、現在のキー値に対する NULL ロックの取得、および現在のキーから以前のキーまでを対象とした挿入範囲ロックの取得を待機しているときに発生します。 キーの NULL ロックは、すぐに解放されるロックです。| 
|LCK_M_RIn_NL_ABORT_BLOCKERS |タスクが、現在のキー値に対するアボート ブロッカーによる NULL ロックの取得、および現在のキーから以前のキーまでを対象としたアボート ブロッカーによる挿入範囲ロックの取得を待機しているときに発生します。 キーの NULL ロックは、すぐに解放されるロックです。 (ALTER TABLE および ALTER INDEX の優先度の低い待機オプションに関連します)。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|LCK_M_RIn_NL_LOW_PRIORITY |タスクが、現在のキー値に対する優先度の低い NULL ロックの取得、および現在のキーから以前のキーまでを対象とした優先度の低い挿入範囲ロックの取得を待機しているときに発生します。 キーの NULL ロックは、すぐに解放されるロックです。 (ALTER TABLE および ALTER INDEX の優先度の低い待機オプションに関連します)。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|LCK_M_RIn_S  |タスクが、現在のキー値に対する共有ロックの取得、および現在のキーから以前のキーまでを対象とした挿入範囲ロックの取得を待機しているときに発生します。| 
|LCK_M_RIn_S_ABORT_BLOCKERS |タスクが、現在のキー値に対するアボート ブロッカーによる共有ロックの取得、および現在のキーから以前のキーまでを対象としたアボート ブロッカーによる挿入範囲ロックの取得を待機しているときに発生します  (ALTER TABLE および ALTER INDEX の優先度の低い待機オプションに関連します)。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|LCK_M_RIn_S_LOW_PRIORITY |タスクが、現在のキー値に対する優先度の低い共有ロックの取得、および現在のキーから以前のキーまでを対象とした優先度の低い挿入範囲ロックの取得を待機しているときに発生します  (ALTER TABLE および ALTER INDEX の優先度の低い待機オプションに関連します)。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|LCK_M_RIn_U  |タスクが、現在のキー値に対する更新ロックの取得、および現在のキーから以前のキーまでを対象とした挿入範囲ロックの取得を待機しているときに発生します。| 
|LCK_M_RIn_U_ABORT_BLOCKERS |タスクが、現在のキー値に対するアボート ブロッカーによる更新ロックの取得、および現在のキーから以前のキーまでを対象としたアボート ブロッカーによる挿入範囲ロックの取得を待機しているときに発生します  (ALTER TABLE および ALTER INDEX の優先度の低い待機オプションに関連します)。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|LCK_M_RIn_U_LOW_PRIORITY |タスクが、現在のキー値に対する優先度の低い更新ロックの取得、および現在のキーから以前のキーまでを対象とした優先度の低い挿入範囲ロックの取得を待機しています。 (ALTER TABLE および ALTER INDEX の優先度の低い待機オプションに関連します)。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|LCK_M_RIn_X  |タスクが、現在のキー値に対する排他ロックの取得、および現在のキーから以前のキーまでを対象とした挿入範囲ロックの取得を待機しているときに発生します。| 
|LCK_M_RIn_X_ABORT_BLOCKERS |タスクが、現在のキー値に対するアボート ブロッカーによる排他ロックの取得、および現在のキーから以前のキーまでを対象としたアボート ブロッカーによる挿入範囲ロックの取得を待機しているときに発生します  (ALTER TABLE および ALTER INDEX の優先度の低い待機オプションに関連します)。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|LCK_M_RIn_X_LOW_PRIORITY |タスクが、現在のキー値に対する優先度の低い排他ロックの取得、および現在のキーから以前のキーまでを対象とした優先度の低い挿入範囲ロックの取得を待機しているときに発生します。 (ALTER TABLE および ALTER INDEX の優先度の低い待機オプションに関連します)。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|LCK_M_RS_S  |タスクが、現在のキー値に対する共有ロックの取得、および現在のキーから以前のキーまでを対象とした共有範囲ロックの取得を待機しているときに発生します。| 
|LCK_M_RS_S_ABORT_BLOCKERS |タスクが、現在のキー値に対するアボートブロッカーによる共有ロックの取得、および現在のキーから以前のキーまでを対象としたアボートブロッカーによる共有範囲ロックの取得を待機しているときに発生します。 (ALTER TABLE および ALTER INDEX の優先度の低い待機オプションに関連します)。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|LCK_M_RS_S_LOW_PRIORITY |タスクが、現在のキー値に対する優先度の低い共有ロックの取得、および現在のキーから以前のキーまでを対象とした優先度の低い共有範囲ロックの取得を待機しているときに発生します  (ALTER TABLE および ALTER INDEX の優先度の低い待機オプションに関連します)。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|LCK_M_RS_U  |タスクが、現在のキー値に対する更新ロックの取得、および現在のキーから以前のキーまでを対象とした更新範囲ロックの取得を待機しているときに発生します。| 
|LCK_M_RS_U_ABORT_BLOCKERS |タスクが、現在のキー値に対するアボート ブロッカーによる更新ロックの取得、および現在のキーから以前のキーまでを対象としたアボート ブロッカーによる更新範囲ロックの取得を待機しているときに発生します  (ALTER TABLE および ALTER INDEX の優先度の低い待機オプションに関連します)。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|LCK_M_RS_U_LOW_PRIORITY |タスクが、現在のキー値に対する優先度の低い更新ロックの取得、および現在のキーから以前のキーまでを対象とした優先度の低い更新範囲ロックの取得を待機しているときに発生します  (ALTER TABLE および ALTER INDEX の優先度の低い待機オプションに関連します)。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|LCK_M_RX_S  |タスクが、現在のキー値に対する共有ロックの取得、および現在のキーから以前のキーまでを対象とした排他範囲ロックの取得を待機しているときに発生します。| 
|LCK_M_RX_S_ABORT_BLOCKERS |タスクが、現在のキー値に対するアボートブロッカーによる共有ロックの取得、および現在のキーから以前のキーまでを対象としたアボートブロッカーによる排他的範囲ロックの取得を待機しているときに発生します。 (ALTER TABLE および ALTER INDEX の優先度の低い待機オプションに関連します)。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|LCK_M_RX_S_LOW_PRIORITY |タスクが、現在のキー値に対する優先度の低い共有ロックの取得、および現在のキーから以前のキーまでを対象とした優先順位の低い排他範囲の取得を待機しているときに発生します。 (ALTER TABLE および ALTER INDEX の優先度の低い待機オプションに関連します)。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|LCK_M_RX_U  |タスクが、現在のキー値に対する更新ロックの取得、および現在のキーから以前のキーまでを対象とした排他範囲ロックの取得を待機しているときに発生します。| 
|LCK_M_RX_U_ABORT_BLOCKERS |タスクが、現在のキー値に対するアボート ブロッカーによる更新ロックの取得、および現在のキーから以前のキーまでを対象としたアボート ブロッカーによる排他範囲ロックの取得を待機しているときに発生します  (ALTER TABLE および ALTER INDEX の優先度の低い待機オプションに関連します)。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|LCK_M_RX_U_LOW_PRIORITY |タスクが、現在のキー値に対する優先度の低い更新ロックの取得、および現在のキーから以前のキーまでを対象とした優先度の低い排他範囲ロックの取得を待機しているときに発生します。 (ALTER TABLE および ALTER INDEX の優先度の低い待機オプションに関連します)。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|LCK_M_RX_X  |タスクが、現在のキー値に対する排他ロックの取得、および現在のキーから以前のキーまでを対象とした排他範囲ロックの取得を待機しているときに発生します。| 
|LCK_M_RX_X_ABORT_BLOCKERS |タスクが、現在のキー値に対するアボートブロッカーによる排他ロックの取得、および現在のキーから以前のキーまでを対象としたアボートブロッカーによる排他範囲ロックの取得を待機しているときに発生します。 (ALTER TABLE および ALTER INDEX の優先度の低い待機オプションに関連します)。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|LCK_M_RX_X_LOW_PRIORITY |タスクが、現在のキー値に対する優先度の低い排他ロックの取得、および現在のキーから以前のキーまでを対象とした優先度の低い排他範囲ロックの取得を待機しているときに発生します  (ALTER TABLE および ALTER INDEX の優先度の低い待機オプションに関連します)。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|LCK_M_S  |タスクが共有ロックの取得を待機しているときに発生します。 詳細については、「 [共有ロック](../sql-server-transaction-locking-and-row-versioning-guide.md#shared) 」を参照してください。| 
|LCK_M_S_ABORT_BLOCKERS |タスクがアボートブロッカーによる共有ロックの取得を待機しているときに発生します。 (ALTER TABLE および ALTER INDEX の優先度の低い待機オプションに関連します)。 詳細については、「 [共有ロック](../sql-server-transaction-locking-and-row-versioning-guide.md#shared) 」を参照してください。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|LCK_M_S_LOW_PRIORITY |タスクが優先度の低い共有ロックの取得を待機しているときに発生します。 (ALTER TABLE および ALTER INDEX の優先度の低い待機オプションに関連します)。 詳細については、「 [共有ロック](../sql-server-transaction-locking-and-row-versioning-guide.md#shared) 」を参照してください。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|LCK_M_SCH_M  |タスクがスキーマ変更ロックの取得を待機しているときに発生します。 詳細については、「 [スキーマロック](../sql-server-transaction-locking-and-row-versioning-guide.md#schema) 」を参照してください。| 
|LCK_M_SCH_M_ABORT_BLOCKERS |タスクがアボート ブロッカーによるスキーマ変更ロックの取得を待機しているときに発生します  (ALTER TABLE および ALTER INDEX の優先度の低い待機オプションに関連します)。 詳細については、「 [スキーマロック](../sql-server-transaction-locking-and-row-versioning-guide.md#schema) 」を参照してください。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|LCK_M_SCH_M_LOW_PRIORITY |タスクが優先度の低いスキーマ変更ロックの取得を待機しているときに発生します  (ALTER TABLE および ALTER INDEX の優先度の低い待機オプションに関連します)。 詳細については、「 [スキーマロック](../sql-server-transaction-locking-and-row-versioning-guide.md#schema) 」を参照してください。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|LCK_M_SCH_S  |タスクがスキーマ共有ロックの取得を待機しているときに発生します。 詳細については、「 [スキーマロック](../sql-server-transaction-locking-and-row-versioning-guide.md#schema) 」を参照してください。| 
|LCK_M_SCH_S_ABORT_BLOCKERS |タスクがアボート ブロッカーによるスキーマ共有ロックの取得を待機しているときに発生します  (ALTER TABLE および ALTER INDEX の優先度の低い待機オプションに関連します)。 詳細については、「 [スキーマロック](../sql-server-transaction-locking-and-row-versioning-guide.md#schema) 」を参照してください。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|LCK_M_SCH_S_LOW_PRIORITY |タスクが優先度の低いスキーマ共有ロックの取得を待機しているときに発生します。 (ALTER TABLE および ALTER INDEX の優先度の低い待機オプションに関連します)詳細については、「 [スキーマロック](../sql-server-transaction-locking-and-row-versioning-guide.md#schema) 」を参照してください。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|LCK_M_SIU  |タスクがインテント更新付き共有ロックの取得を待機しているときに発生します。 詳細については、 [インテントロック](../sql-server-transaction-locking-and-row-versioning-guide.md#intent) に関する説明を参照してください。| 
|LCK_M_SIU_ABORT_BLOCKERS |タスクが、中止ブロッカーによるインテント更新ロック付き共有の取得を待機しているときに発生します。 (ALTER TABLE および ALTER INDEX の優先度の低い待機オプションに関連します)。 詳細については、 [インテントロック](../sql-server-transaction-locking-and-row-versioning-guide.md#intent) に関する説明を参照してください。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|LCK_M_SIU_LOW_PRIORITY |タスクが、優先度の低いインテント更新ロックで共有を取得するのを待機しているときに発生します。 (ALTER TABLE および ALTER INDEX の優先度の低い待機オプションに関連します)。 詳細については、 [インテントロック](../sql-server-transaction-locking-and-row-versioning-guide.md#intent) に関する説明を参照してください。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|LCK_M_SIX  |タスクがインテント排他付き共有ロックの取得を待機しているときに発生します。 詳細については、 [インテントロック](../sql-server-transaction-locking-and-row-versioning-guide.md#intent) に関する説明を参照してください。| 
|LCK_M_SIX_ABORT_BLOCKERS |タスクがアボートブロッカーによるインテント排他付き共有ロックの取得を待機しているときに発生します。 (ALTER TABLE および ALTER INDEX の優先度の低い待機オプションに関連します)。 詳細については、 [インテントロック](../sql-server-transaction-locking-and-row-versioning-guide.md#intent) に関する説明を参照してください。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|LCK_M_SIX_LOW_PRIORITY |タスクが、優先順位の低いインテント排他付き共有ロックの取得を待機しているときに発生します。 (ALTER TABLE および ALTER INDEX の優先度の低い待機オプションに関連します)。 詳細については、 [インテントロック](../sql-server-transaction-locking-and-row-versioning-guide.md#intent) に関する説明を参照してください。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|LCK_M_U  |タスクが更新ロックの取得を待機しているときに発生します。 詳細については、「 [更新ロック](../sql-server-transaction-locking-and-row-versioning-guide.md#update) 」を参照してください。| 
|LCK_M_U_ABORT_BLOCKERS |タスクが中止ブロッカーによる更新ロックの取得を待機しているときに発生します。 (ALTER TABLE および ALTER INDEX の優先度の低い待機オプションに関連します)。 詳細については、「 [更新ロック](../sql-server-transaction-locking-and-row-versioning-guide.md#update) 」を参照してください。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|LCK_M_U_LOW_PRIORITY |タスクが優先度の低い更新ロックの取得を待機しているときに発生します  (ALTER TABLE および ALTER INDEX の優先度の低い待機オプションに関連します)。 詳細については、「 [更新ロック](../sql-server-transaction-locking-and-row-versioning-guide.md#update) 」を参照してください。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|LCK_M_UIX  |タスクがインテント排他付き更新ロックの取得を待機しているときに発生します。 詳細については、 [インテントロック](../sql-server-transaction-locking-and-row-versioning-guide.md#intent) に関する説明を参照してください。| 
|LCK_M_UIX_ABORT_BLOCKERS |タスクがアボート ブロッカーによるインテント排他付き更新ロックの取得を待機しているときに発生します  (ALTER TABLE および ALTER INDEX の優先度の低い待機オプションに関連します)。 詳細については、 [インテントロック](../sql-server-transaction-locking-and-row-versioning-guide.md#intent) に関する説明を参照してください。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|LCK_M_UIX_LOW_PRIORITY |タスクが、優先度の低いインテント排他ロックで更新の取得を待機しているときに発生します。 (ALTER TABLE および ALTER INDEX の優先度の低い待機オプションに関連します)。 詳細については、 [インテントロック](../sql-server-transaction-locking-and-row-versioning-guide.md#intent) に関する説明を参照してください。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|LCK_M_X  |タスクが排他ロックの取得を待機しているときに発生します。 詳細については、「 [排他ロック](../sql-server-transaction-locking-and-row-versioning-guide.md#exclusive) 」を参照してください。| 
|LCK_M_X_ABORT_BLOCKERS |タスクがアボートブロッカーによる排他ロックの取得を待機しているときに発生します。 (ALTER TABLE および ALTER INDEX の優先度の低い待機オプションに関連します)。 詳細については、「 [排他ロック](../sql-server-transaction-locking-and-row-versioning-guide.md#exclusive) 」を参照してください。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|LCK_M_X_LOW_PRIORITY |タスクが、優先度の低い排他ロックの取得を待機しているときに発生します。 (ALTER TABLE および ALTER INDEX の優先度の低い待機オプションに関連します)。 詳細については、「 [排他ロック](../sql-server-transaction-locking-and-row-versioning-guide.md#exclusive) 」を参照してください。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|LOG_POOL_SCAN |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|LOG_RATE_GOVERNOR |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|LOGBUFFER  |タスクが、ログ レコードを格納するログ バッファーの領域を待機しているときに発生します。 常に高い値が示される場合は、ログ デバイスで解放される領域よりも、サーバーにより生成されるログ サイズが大きいことを表しています。| 
|LOGCAPTURE_LOGPOOLTRUNCPOINT |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|LOGGENERATION |単に情報を示すためだけに特定されます。 サポートされていません。 将来の互換性は保証されません。| 
|LOGMGR |データベースを閉じる間、ログのシャットダウン前に未処理のログ I/O が終了するのをタスクが待機しているときに発生します。| 
|LOGMGR_FLUSH  |単に情報を示すためだけに特定されます。 サポートされていません。 将来の互換性は保証されません。| 
|LOGMGR_PMM_LOG |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|LOGMGR_QUEUE |ログ ライター タスクが作業要求を待機しているときに発生します。| 
|LOGMGR_RESERVE_APPEND  |新しいログ レコードを書き込むために、タスクが、ログの切り捨てによるログ領域の解放の確認を待機しているときに発生します。 この待機を短縮すると、影響を受けるデータベースのログ ファイル サイズが増えることに注意してください。| 
|LOGPOOL_CACHESIZE |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|LOGPOOL_CONSUMER |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|LOGPOOL_CONSUMERSET |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|LOGPOOL_FREEPOOLS |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|LOGPOOL_MGRSET |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|LOGPOOL_REPLACEMENTSET |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|LOGPOOLREFCOUNTEDOBJECT_REFDONE |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|LOWFAIL_MEMMGR_QUEUE |メモリが使用可能になるのを待機しているときに発生します。| 
|MD_AGENT_YIELD |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|MD_LAZYCACHE_RWLOCK |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|MEMORY_ALLOCATION_EXT |内部 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] メモリプールまたはオペレーティングシステムのいずれかからメモリを割り当てているときに発生します。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|MEMORY_GRANT_UPDATE |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE [sssql17-md](../../includes/sssql17-md.md)] 以降。| 
|METADATA_LAZYCACHE_RWLOCK |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssKilimanjaro_md](../../includes/sskilimanjaro-md.md)] のみ。 |  
|MIGRATIONBUFFER |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE [sssql17-md](../../includes/sssql17-md.md)] 以降。| 
|その他 |単に情報を示すためだけに特定されます。 サポートされていません。 将来の互換性は保証されません。| 
|その他 |単に情報を示すためだけに特定されます。 サポートされていません。 将来の互換性は保証されません。| 
|MSQL_DQ |タスクが分散クエリ操作の終了を待機しているときに発生します。 これは、複数のアクティブな結果セット (MARS) アプリケーションにデッドロックの可能性があるかどうかを検出するために使用されます。 分散クエリ呼び出しが終了すると、待機は終了します。| 
|MSQL_XACT_MGR_MUTEX |タスクが、セッション レベル トランザクション操作を実行するために、セッション トランザクション マネージャーの所有権の取得を待機しているときに発生します。| 
|MSQL_XACT_MUTEX |トランザクション使用の同期中に発生します。 要求でトランザクションを使用するには、まずミューテックスを取得する必要があります。| 
|MSQL_XP  |タスクが、拡張ストアド プロシージャの終了を待機しているときに発生します。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、この待機状態を使用して、MARS アプリケーションにデッドロックの可能性があるかどうかを検出します。 拡張ストアド プロシージャの呼び出しが終了すると、待機は終了します。| 
|MSSEARCH |フルテキスト検索の呼び出し中に発生します。 フルテキスト操作が完了すると、待機は終了します。 この待機はフルテキスト操作の競合ではなく、操作時間を表します。| 
|NET_WAITFOR_PACKET |ネットワークの読み取り中に、接続がネットワーク パケットを待機しているときに発生します。| 
|NETWORKSXMLMGRLOAD |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|NODE_CACHE_MUTEX |内部使用のみです。| 
|OLEDB |が、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] SNAC OLE DB Provider (SQLNCLI) または Microsoft OLE DB Driver for SQL Server (MSOLEDBSQL) を呼び出したときに発生します。 この種類の待機は、同期では使用されません。 代わりに、OLE DB プロバイダー呼び出しの持続時間を示します。| 
|ONDEMAND_TASK_QUEUE |バックグラウンド タスクが、優先度の高いシステム タスクの要求を待機しているときに発生します。 待機時間が長い場合は処理優先度が高い要求がないことを示します。問題があるわけではありません。| 
|PAGEIOLATCH_DT  |タスクが、I/O 要求内のバッファー ラッチで待機しているときに発生します。 ラッチ要求は破棄モードです。 待機時間が長い場合、ディスク サブシステムに問題がある可能性があります。| 
|PAGEIOLATCH_EX  |タスクが、I/O 要求内のバッファー ラッチで待機しているときに発生します。 ラッチ要求は排他モードであり、バッファーがディスクに書き込まれるときに使用されるモードです。 待機時間が長い場合、ディスク サブシステムに問題がある可能性があります。 <br /><br /> 詳細については、この [SQL Server 低速の i/o トラブルシューティング](https://techcommunity.microsoft.com/t5/sql-server-support/slow-i-o-sql-server-and-disk-i-o-performance/ba-p/333983) に関するブログを参照してください。| 
|PAGEIOLATCH_KP  |タスクが、I/O 要求内のバッファー ラッチで待機しているときに発生します。 ラッチ要求は保持モードです。 待機時間が長い場合、ディスク サブシステムに問題がある可能性があります。| 
|PAGEIOLATCH_NL  |単に情報を示すためだけに特定されます。 サポートされていません。 将来の互換性は保証されません。| 
|PAGEIOLATCH_SH  |タスクが、I/O 要求内のバッファー ラッチで待機しているときに発生します。 ラッチ要求は共有モードで、バッファーがディスクから読み取られるときに使用されるモードです。 待機時間が長い場合、ディスク サブシステムに問題がある可能性があります。<br /><br /> 詳細については、この [SQL Server 低速の i/o トラブルシューティング](https://techcommunity.microsoft.com/t5/sql-server-support/slow-i-o-sql-server-and-disk-i-o-performance/ba-p/333983) に関するブログを参照してください。| 
|PAGEIOLATCH_UP  |タスクが、I/O 要求内のバッファー ラッチで待機しているときに発生します。 ラッチ要求は更新モードです。 待機時間が長い場合、ディスク サブシステムに問題がある可能性があります。<br /><br /> 詳細については、この [SQL Server 低速の i/o トラブルシューティング](https://techcommunity.microsoft.com/t5/sql-server-support/slow-i-o-sql-server-and-disk-i-o-performance/ba-p/333983) に関するブログを参照してください。| 
|PAGELATCH_DT  |タスクが、I/O 要求内にないバッファー ラッチで待機しているときに発生します。 ラッチ要求は破棄モードです。 ページの内容を削除する前に、破棄モードを取得する必要があります。 詳細については、「 [ラッチモード](../diagnose-resolve-latch-contention.md#sql-server-latch-modes-and-compatibility) 」を参照してください。| 
|PAGELATCH_EX  |タスクが、I/O 要求内にないバッファー ラッチで待機しているときに発生します。 ラッチ要求は排他モードであるため、他のスレッドがページ (buffer) への書き込みまたは読み取りを行うことをブロックします。 </br></br> このラッチにつながる一般的なシナリオは、"最後のページ挿入" バッファーラッチの競合です。 これを理解して解決するには、[ [最終ページ挿入 PAGELATCH_EX の競合の解決](/troubleshoot/sql/performance/resolve-pagelatch-ex-contention) ] を使用し、 [SQL Server で最後のページ挿入ラッチの競合を診断して解決し](../diagnose-resolve-latch-contention.md#last-pagetrailing-page-insert-contention)ます。 もう1つのシナリオは [、非クラスター化インデックスとランダム挿入 (キューテーブル) を持つ小さなテーブルでのラッチ競合](../diagnose-resolve-latch-contention.md#latch-contention-on-small-tables-with-a-non-clustered-index-and-random-inserts-queue-table)です。| 
|PAGELATCH_KP  |タスクが、I/O 要求内にないバッファー ラッチで待機しているときに発生します。 ラッチ要求は保持モードのため、ページが別のスレッドによって破棄されるのを防ぎます。 詳細については、「 [ラッチモード](../diagnose-resolve-latch-contention.md#sql-server-latch-modes-and-compatibility) 」を参照してください。| 
|PAGELATCH_NL  |単に情報を示すためだけに特定されます。 サポートされていません。 将来の互換性は保証されません。| 
|PAGELATCH_SH  |タスクが、I/O 要求内にないバッファー ラッチで待機しているときに発生します。 ラッチ要求は共有モードであり、複数のスレッドがバッファー (ページ) を読み取りますが、変更することはできません。 詳細については、「 [ラッチモード](../diagnose-resolve-latch-contention.md#sql-server-latch-modes-and-compatibility) 」を参照してください。| 
|PAGELATCH_UP  |タスクが、I/O 要求内にないバッファー ラッチで待機しているときに発生します。 ラッチ要求は更新モードです。 通常、この待機の種類は、PFS、GAM、SGAM などのシステムページ (バッファー) がラッチされたときに発生する可能性があります。 詳細については、「 [ラッチモード](../diagnose-resolve-latch-contention.md#sql-server-latch-modes-and-compatibility) 」を参照してください。 </br></br> このラッチを使用した一般的なシナリオのトラブルシューティングについては、「 [SQL Server tempdb データベースでの割り当ての競合を減らす](/troubleshoot/sql/performance/recommendations-reduce-allocation-contention)」を参照してください。| 
|PARALLEL_BACKUP_QUEUE |RESTORE HEADERONLY、RESTORE FILELISTONLY、または RESTORE LABELONLY によって生成された出力をシリアル化しているときに発生します。| 
|PARALLEL_REDO_DRAIN_WORKER |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|PARALLEL_REDO_FLOW_CONTROL |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|PARALLEL_REDO_LOG_CACHE |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|PARALLEL_REDO_TRAN_LIST |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|PARALLEL_REDO_TRAN_TURN |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|PARALLEL_REDO_WORKER_SYNC |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|PARALLEL_REDO_WORKER_WAIT_WORK |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|PERFORMANCE_COUNTERS_RWLOCK |内部使用のみです。| 
|PHYSICAL_SEEDING_DMV |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|POOL_LOG_RATE_GOVERNOR |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|PREEMPTIVE_ABR |単に情報を示すためだけに特定されます。 サポートされていません。 将来の互換性は保証されません。| 
|PREEMPTIVE_AUDIT_ACCESS_EVENTLOG |[!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] オペレーティング システム (SQLOS) スケジューラが、監査イベントを Windows イベント ログに書き込むためにプリエンプティブ モードに切り替えたときに発生します。 <br /><br /> **適用対象**: [!INCLUDE[ssKilimanjaro_md](../../includes/sskilimanjaro-md.md)] のみ。 |  
|PREEMPTIVE_AUDIT_ACCESS_SECLOG |SQLOS スケジューラが、監査イベントを Windows セキュリティ ログに書き込むためにプリエンプティブ モードに切り替えたときに発生します。 <br /><br /> **適用対象**: [!INCLUDE[ssKilimanjaro_md](../../includes/sskilimanjaro-md.md)] のみ。 |  
|PREEMPTIVE_CLOSEBACKUPMEDIA |SQLOS スケジューラが、バックアップ メディアを閉じるためにプリエンプティブ モードに切り替えたときに発生します。| 
|PREEMPTIVE_CLOSEBACKUPTAPE |SQLOS スケジューラが、テープ バックアップ デバイスを閉じるためにプリエンプティブ モードに切り替えたときに発生します。| 
|PREEMPTIVE_CLOSEBACKUPVDIDEVICE |SQLOS スケジューラが、仮想バックアップ デバイスを閉じるためにプリエンプティブ モードに切り替えたときに発生します。| 
|PREEMPTIVE_CLUSAPI_CLUSTERRESOURCECONTROL |SQLOS スケジューラが、Windows フェールオーバー クラスター操作を実行するためにプリエンプティブ モードに切り替えたときに発生します。| 
|PREEMPTIVE_COM_COCREATEINSTANCE |SQLOS スケジューラが、COM オブジェクトを作成するためにプリエンプティブ モードに切り替えたときに発生します。| 
|PREEMPTIVE_COM_COGETCLASSOBJECT |内部使用のみです。| 
|PREEMPTIVE_COM_CREATEACCESSOR |内部使用のみです。| 
|PREEMPTIVE_COM_DELETEROWS |内部使用のみです。| 
|PREEMPTIVE_COM_GETCOMMANDTEXT |内部使用のみです。| 
|PREEMPTIVE_COM_GETDATA |内部使用のみです。| 
|PREEMPTIVE_COM_GETNEXTROWS |内部使用のみです。| 
|PREEMPTIVE_COM_GETRESULT |内部使用のみです。| 
|PREEMPTIVE_COM_GETROWSBYBOOKMARK |内部使用のみです。| 
|PREEMPTIVE_COM_LBFLUSH |内部使用のみです。| 
|PREEMPTIVE_COM_LBLOCKREGION |内部使用のみです。| 
|PREEMPTIVE_COM_LBREADAT |内部使用のみです。| 
|PREEMPTIVE_COM_LBSETSIZE |内部使用のみです。| 
|PREEMPTIVE_COM_LBSTAT |内部使用のみです。| 
|PREEMPTIVE_COM_LBUNLOCKREGION |内部使用のみです。| 
|PREEMPTIVE_COM_LBWRITEAT |内部使用のみです。| 
|PREEMPTIVE_COM_QUERYINTERFACE |内部使用のみです。| 
|PREEMPTIVE_COM_RELEASE |内部使用のみです。| 
|PREEMPTIVE_COM_RELEASEACCESSOR |内部使用のみです。| 
|PREEMPTIVE_COM_RELEASEROWS |内部使用のみです。| 
|PREEMPTIVE_COM_RELEASESESSION |内部使用のみです。| 
|PREEMPTIVE_COM_RESTARTPOSITION |内部使用のみです。| 
|PREEMPTIVE_COM_SEQSTRMREAD |内部使用のみです。| 
|PREEMPTIVE_COM_SEQSTRMREADANDWRITE |内部使用のみです。| 
|PREEMPTIVE_COM_SETDATAFAILURE |内部使用のみです。| 
|PREEMPTIVE_COM_SETPARAMETERINFO |内部使用のみです。| 
|PREEMPTIVE_COM_SETPARAMETERPROPERTIES |内部使用のみです。| 
|PREEMPTIVE_COM_STRMLOCKREGION |内部使用のみです。| 
|PREEMPTIVE_COM_STRMSEEKANDREAD |内部使用のみです。| 
|PREEMPTIVE_COM_STRMSEEKANDWRITE |内部使用のみです。| 
|PREEMPTIVE_COM_STRMSETSIZE |内部使用のみです。| 
|PREEMPTIVE_COM_STRMSTAT |内部使用のみです。| 
|PREEMPTIVE_COM_STRMUNLOCKREGION |内部使用のみです。| 
|PREEMPTIVE_CONSOLEWRITE |内部使用のみです。| 
|PREEMPTIVE_CREATEPARAM |内部使用のみです。| 
|PREEMPTIVE_DEBUG |内部使用のみです。| 
|PREEMPTIVE_DFSADDLINK |内部使用のみです。| 
|PREEMPTIVE_DFSLINKEXISTCHECK |内部使用のみです。| 
|PREEMPTIVE_DFSLINKHEALTHCHECK |内部使用のみです。| 
|PREEMPTIVE_DFSREMOVELINK |内部使用のみです。| 
|PREEMPTIVE_DFSREMOVEROOT |内部使用のみです。| 
|PREEMPTIVE_DFSROOTFOLDERCHECK |内部使用のみです。| 
|PREEMPTIVE_DFSROOTINIT |内部使用のみです。| 
|PREEMPTIVE_DFSROOTSHARECHECK |内部使用のみです。| 
|PREEMPTIVE_DTC_ABORT |内部使用のみです。| 
|PREEMPTIVE_DTC_ABORTREQUESTDONE |内部使用のみです。| 
|PREEMPTIVE_DTC_BEGINTRANSACTION |内部使用のみです。| 
|PREEMPTIVE_DTC_COMMITREQUESTDONE |内部使用のみです。| 
|PREEMPTIVE_DTC_ENLIST |内部使用のみです。| 
|PREEMPTIVE_DTC_PREPAREREQUESTDONE |内部使用のみです。| 
|PREEMPTIVE_FILESIZEGET |内部使用のみです。| 
|PREEMPTIVE_FSAOLEDB_ABORTTRANSACTION |内部使用のみです。| 
|PREEMPTIVE_FSAOLEDB_COMMITTRANSACTION |内部使用のみです。| 
|PREEMPTIVE_FSAOLEDB_STARTTRANSACTION |内部使用のみです。| 
|PREEMPTIVE_FSRECOVER_UNCONDITIONALUNDO |内部使用のみです。| 
|PREEMPTIVE_GETRMINFO |内部使用のみです。| 
|PREEMPTIVE_HADR_LEASE_MECHANISM |Always On 可用性グループは、Microsoft サポート診断のためにリースマネージャーをスケジュールします。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|PREEMPTIVE_HTTP_EVENT_WAIT |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|PREEMPTIVE_HTTP_REQUEST |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|PREEMPTIVE_LOCKMONITOR |内部使用のみです。| 
|PREEMPTIVE_MSS_RELEASE |内部使用のみです。| 
|PREEMPTIVE_ODBCOPS |内部使用のみです。| 
|PREEMPTIVE_OLE_UNINIT |内部使用のみです。| 
|PREEMPTIVE_OLEDB_ABORTORCOMMITTRAN |内部使用のみです。| 
|PREEMPTIVE_OLEDB_ABORTTRAN |内部使用のみです。| 
|PREEMPTIVE_OLEDB_GETDATASOURCE |内部使用のみです。| 
|PREEMPTIVE_OLEDB_GETLITERALINFO |内部使用のみです。| 
|PREEMPTIVE_OLEDB_GETPROPERTIES |内部使用のみです。| 
|PREEMPTIVE_OLEDB_GETPROPERTYINFO |内部使用のみです。| 
|PREEMPTIVE_OLEDB_GETSCHEMALOCK |内部使用のみです。| 
|PREEMPTIVE_OLEDB_JOINTRANSACTION |内部使用のみです。| 
|PREEMPTIVE_OLEDB_RELEASE |内部使用のみです。| 
|PREEMPTIVE_OLEDB_SETPROPERTIES |内部使用のみです。| 
|PREEMPTIVE_OLEDBOPS |内部使用のみです。| 
|PREEMPTIVE_OS_ACCEPTSECURITYCONTEXT |内部使用のみです。| 
|PREEMPTIVE_OS_ACQUIRECREDENTIALSHANDLE |内部使用のみです。| 
|PREEMPTIVE_OS_AUTHENTICATIONOPS |内部使用のみです。| 
|PREEMPTIVE_OS_AUTHORIZATIONOPS |内部使用のみです。| 
|PREEMPTIVE_OS_AUTHZGETINFORMATIONFROMCONTEXT |内部使用のみです。| 
|PREEMPTIVE_OS_AUTHZINITIALIZECONTEXTFROMSID |内部使用のみです。| 
|PREEMPTIVE_OS_AUTHZINITIALIZERESOURCEMANAGER |内部使用のみです。| 
|PREEMPTIVE_OS_BACKUPREAD |内部使用のみです。| 
|PREEMPTIVE_OS_CLOSEHANDLE |内部使用のみです。| 
|PREEMPTIVE_OS_CLUSTEROPS |内部使用のみです。| 
|PREEMPTIVE_OS_COMOPS |内部使用のみです。| 
|PREEMPTIVE_OS_COMPLETEAUTHTOKEN |内部使用のみです。| 
|PREEMPTIVE_OS_COPYFILE |内部使用のみです。| 
|PREEMPTIVE_OS_CREATEDIRECTORY |内部使用のみです。| 
|PREEMPTIVE_OS_CREATEFILE |内部使用のみです。| 
|PREEMPTIVE_OS_CRYPTACQUIRECONTEXT |内部使用のみです。| 
|PREEMPTIVE_OS_CRYPTIMPORTKEY |内部使用のみです。| 
|PREEMPTIVE_OS_CRYPTOPS |内部使用のみです。| 
|PREEMPTIVE_OS_DECRYPTMESSAGE |内部使用のみです。| 
|PREEMPTIVE_OS_DELETEFILE |内部使用のみです。| 
|PREEMPTIVE_OS_DELETESECURITYCONTEXT |内部使用のみです。| 
|PREEMPTIVE_OS_DEVICEIOCONTROL |内部使用のみです。| 
|PREEMPTIVE_OS_DEVICEOPS |内部使用のみです。| 
|PREEMPTIVE_OS_DIRSVC_NETWORKOPS |内部使用のみです。| 
|PREEMPTIVE_OS_DISCONNECTNAMEDPIPE |内部使用のみです。| 
|PREEMPTIVE_OS_DOMAINSERVICESOPS |内部使用のみです。| 
|PREEMPTIVE_OS_DSGETDCNAME |内部使用のみです。| 
|PREEMPTIVE_OS_DTCOPS |内部使用のみです。| 
|PREEMPTIVE_OS_ENCRYPTMESSAGE |内部使用のみです。| 
|PREEMPTIVE_OS_FILEOPS |内部使用のみです。| 
|PREEMPTIVE_OS_FINDFILE |内部使用のみです。| 
|PREEMPTIVE_OS_FLUSHFILEBUFFERS |内部使用のみです。| 
|PREEMPTIVE_OS_FORMATMESSAGE |内部使用のみです。| 
|PREEMPTIVE_OS_FREECREDENTIALSHANDLE |内部使用のみです。| 
|PREEMPTIVE_OS_FREELIBRARY |内部使用のみです。| 
|PREEMPTIVE_OS_GENERICOPS |内部使用のみです。| 
|PREEMPTIVE_OS_GETADDRINFO |内部使用のみです。| 
|PREEMPTIVE_OS_GETCOMPRESSEDFILESIZE |内部使用のみです。| 
|PREEMPTIVE_OS_GETDISKFREESPACE |内部使用のみです。| 
|PREEMPTIVE_OS_GETFILEATTRIBUTES |内部使用のみです。| 
|PREEMPTIVE_OS_GETFILESIZE |内部使用のみです。| 
|PREEMPTIVE_OS_GETFINALFILEPATHBYHANDLE |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|PREEMPTIVE_OS_GETLONGPATHNAME |内部使用のみです。| 
|PREEMPTIVE_OS_GETPROCADDRESS |内部使用のみです。| 
|PREEMPTIVE_OS_GETVOLUMENAMEFORVOLUMEMOUNTPOINT |内部使用のみです。| 
|PREEMPTIVE_OS_GETVOLUMEPATHNAME |内部使用のみです。| 
|PREEMPTIVE_OS_INITIALIZESECURITYCONTEXT |内部使用のみです。| 
|PREEMPTIVE_OS_LIBRARYOPS |内部使用のみです。| 
|PREEMPTIVE_OS_LOADLIBRARY |内部使用のみです。| 
|PREEMPTIVE_OS_LOGONUSER |内部使用のみです。| 
|PREEMPTIVE_OS_LOOKUPACCOUNTSID |内部使用のみです。| 
|PREEMPTIVE_OS_MESSAGEQUEUEOPS |内部使用のみです。| 
|PREEMPTIVE_OS_MOVEFILE |内部使用のみです。| 
|PREEMPTIVE_OS_NETGROUPGETUSERS |内部使用のみです。| 
|PREEMPTIVE_OS_NETLOCALGROUPGETMEMBERS |内部使用のみです。| 
|PREEMPTIVE_OS_NETUSERGETGROUPS |内部使用のみです。| 
|PREEMPTIVE_OS_NETUSERGETLOCALGROUPS |内部使用のみです。| 
|PREEMPTIVE_OS_NETUSERMODALSGET |内部使用のみです。| 
|PREEMPTIVE_OS_NETVALIDATEPASSWORDPOLICY |内部使用のみです。| 
|PREEMPTIVE_OS_NETVALIDATEPASSWORDPOLICYFREE |内部使用のみです。| 
|PREEMPTIVE_OS_OPENDIRECTORY |内部使用のみです。| 
|PREEMPTIVE_OS_PDH_WMI_INIT |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|PREEMPTIVE_OS_PIPEOPS |内部使用のみです。| 
|PREEMPTIVE_OS_PROCESSOPS |内部使用のみです。| 
|PREEMPTIVE_OS_QUERYCONTEXTATTRIBUTES |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|PREEMPTIVE_OS_QUERYREGISTRY |内部使用のみです。| 
|PREEMPTIVE_OS_QUERYSECURITYCONTEXTTOKEN |内部使用のみです。| 
|PREEMPTIVE_OS_REMOVEDIRECTORY |内部使用のみです。| 
|PREEMPTIVE_OS_REPORTEVENT |内部使用のみです。| 
|PREEMPTIVE_OS_REVERTTOSELF |内部使用のみです。| 
|PREEMPTIVE_OS_RSFXDEVICEOPS |内部使用のみです。| 
|PREEMPTIVE_OS_SECURITYOPS |内部使用のみです。| 
|PREEMPTIVE_OS_SERVICEOPS |内部使用のみです。| 
|PREEMPTIVE_OS_SETENDOFFILE |内部使用のみです。| 
|PREEMPTIVE_OS_SETFILEPOINTER |内部使用のみです。| 
|PREEMPTIVE_OS_SETFILEVALIDDATA |内部使用のみです。| 
|PREEMPTIVE_OS_SETNAMEDSECURITYINFO |内部使用のみです。| 
|PREEMPTIVE_OS_SQLCLROPS |内部使用のみです。| 
|PREEMPTIVE_OS_SQMLAUNCH |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssKilimanjaro_md](../../includes/sskilimanjaro-md.md)] から [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] |  
|PREEMPTIVE_OS_VERIFYSIGNATURE |内部使用のみです。| 
|PREEMPTIVE_OS_VERIFYTRUST |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|PREEMPTIVE_OS_VSSOPS |内部使用のみです。| 
|PREEMPTIVE_OS_WAITFORSINGLEOBJECT |内部使用のみです。| 
|PREEMPTIVE_OS_WINSOCKOPS |内部使用のみです。| 
|PREEMPTIVE_OS_WRITEFILE |内部使用のみです。| 
|PREEMPTIVE_OS_WRITEFILEGATHER |内部使用のみです。| 
|PREEMPTIVE_OS_WSASETLASTERROR |内部使用のみです。| 
|PREEMPTIVE_REENLIST |内部使用のみです。| 
|PREEMPTIVE_RESIZELOG |内部使用のみです。| 
|PREEMPTIVE_ROLLFORWARDREDO |内部使用のみです。| 
|PREEMPTIVE_ROLLFORWARDUNDO |内部使用のみです。| 
|PREEMPTIVE_SB_STOPENDPOINT |内部使用のみです。| 
|PREEMPTIVE_SERVER_STARTUP |内部使用のみです。| 
|PREEMPTIVE_SETRMINFO |内部使用のみです。| 
|PREEMPTIVE_SHAREDMEM_GETDATA |内部使用のみです。| 
|PREEMPTIVE_SNIOPEN |内部使用のみです。| 
|PREEMPTIVE_SOSHOST |内部使用のみです。| 
|PREEMPTIVE_SOSTESTING |単に情報を示すためだけに特定されます。 サポートされていません。 将来の互換性は保証されません。| 
|PREEMPTIVE_SP_SERVER_DIAGNOSTICS |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|PREEMPTIVE_STARTRM |内部使用のみです。| 
|PREEMPTIVE_STREAMFCB_CHECKPOINT |内部使用のみです。| 
|PREEMPTIVE_STREAMFCB_RECOVER |内部使用のみです。| 
|PREEMPTIVE_STRESSDRIVER |単に情報を示すためだけに特定されます。 サポートされていません。 将来の互換性は保証されません。| 
|PREEMPTIVE_TESTING |単に情報を示すためだけに特定されます。 サポートされていません。 将来の互換性は保証されません。| 
|PREEMPTIVE_TRANSIMPORT |内部使用のみです。| 
|PREEMPTIVE_UNMARSHALPROPAGATIONTOKEN |内部使用のみです。| 
|PREEMPTIVE_VSS_CREATESNAPSHOT |内部使用のみです。| 
|PREEMPTIVE_VSS_CREATEVOLUMESNAPSHOT |内部使用のみです。| 
|PREEMPTIVE_XE_CALLBACKEXECUTE |内部使用のみです。| 
|PREEMPTIVE_XE_CX_FILE_OPEN |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|PREEMPTIVE_XE_CX_HTTP_CALL |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|PREEMPTIVE_XE_DISPATCHER |内部使用のみです。| 
|PREEMPTIVE_XE_ENGINEINIT |内部使用のみです。| 
|PREEMPTIVE_XE_GETTARGETSTATE |内部使用のみです。| 
|PREEMPTIVE_XE_SESSIONCOMMIT |内部使用のみです。| 
|PREEMPTIVE_XE_TARGETFINALIZE |内部使用のみです。| 
|PREEMPTIVE_XE_TARGETINIT |内部使用のみです。| 
|PREEMPTIVE_XE_TIMERRUN |内部使用のみです。| 
|PREEMPTIVE_XETESTING |単に情報を示すためだけに特定されます。 サポートされていません。 将来の互換性は保証されません。| 
|PRINT_ROLLBACK_PROGRESS  |ALTER DATABASE 終了句を使って遷移されたデータベースで、ユーザー プロセスが終了するのを待機する場合に使用されます。 詳しくは、「 ALTER DATABASE (Transact-SQL)」をご覧ください。| 
|PRU_ROLLBACK_DEFERRED |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|PWAIT_ALL_COMPONENTS_INITIALIZED |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|PWAIT_COOP_SCAN |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|PWAIT_DIRECTLOGCONSUMER_GETNEXT |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|PWAIT_EVENT_SESSION_INIT_MUTEX |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|PWAIT_FABRIC_REPLICA_CONTROLLER_DATA_LOSS |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|PWAIT_HADR_ACTION_COMPLETED |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|PWAIT_HADR_CHANGE_NOTIFIER_TERMINATION_SYNC |バックグラウンド タスクが、Windows Server フェールオーバー クラスタリングの通知を (ポーリング経由で) 受信するバックグラウンド タスクの終了を待機しているときに発生します。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|PWAIT_HADR_CLUSTER_INTEGRATION |追加、置換、削除のいずれかの操作が、Always On 内部リスト (ネットワーク、ネットワークアドレス、または可用性グループリスナーの一覧など) での書き込みロックの取得を待機しています。 内部使用のみ、 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|PWAIT_HADR_FAILOVER_COMPLETED |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|PWAIT_HADR_JOIN |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|PWAIT_HADR_OFFLINE_COMPLETED |Always On drop availability group 操作は、Windows Server フェールオーバークラスタリングオブジェクトを破棄する前に、対象の可用性グループがオフラインになるのを待機しています。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|PWAIT_HADR_ONLINE_COMPLETED |可用性グループの作成またはフェールオーバー操作の Always On が、ターゲットの可用性グループがオンラインになるのを待機しています。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|PWAIT_HADR_POST_ONLINE_COMPLETED |Always On drop availability group 操作は、前のコマンドの一部としてスケジュールされたバックグラウンドタスクの終了を待機しています。 たとえば、可用性データベースをプライマリ ロールに遷移させるバックグラウンド タスクが存在する場合があります。 DROP AVAILABILITY GROUP DDL は、競合状態を回避するために、このバックグラウンド タスクの終了を待機する必要があります。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|PWAIT_HADR_SERVER_READY_CONNECTIONS |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|PWAIT_HADR_WORKITEM_COMPLETED |非同期作業タスクの完了を待機するスレッドによる、内部的な待機です。 これは想定される待機であり、CSS で使用されます。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|PWAIT_HADRSIM |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|PWAIT_LOG_CONSOLIDATION_IO |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|PWAIT_LOG_CONSOLIDATION_POLL |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|PWAIT_MD_LOGIN_STATS |ログイン統計に関するメタデータの内部同期中に発生します。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|PWAIT_MD_RELATION_CACHE |テーブルまたはインデックスに関するメタデータの内部同期中に発生します。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|PWAIT_MD_SERVER_CACHE |リンク サーバーに関するメタデータの内部同期中に発生します。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|PWAIT_MD_UPGRADE_CONFIG |サーバー全体の構成に関するアップグレードの内部同期中に発生します。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|PWAIT_PREEMPTIVE_APP_USAGE_TIMER |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE [sssql17-md](../../includes/sssql17-md.md)] 以降。| 
|PWAIT_PREEMPTIVE_AUDIT_ACCESS_WINDOWSLOG |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|PWAIT_QRY_BPMEMORY |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|PWAIT_REPLICA_ONLINE_INIT_MUTEX |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|PWAIT_RESOURCE_SEMAPHORE_FT_PARALLEL_QUERY_SYNC |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|PWAIT_SBS_FILE_OPERATION |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE [sssql17-md](../../includes/sssql17-md.md)] 以降。| 
|PWAIT_XTP_FSSTORAGE_MAINTENANCE |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|PWAIT_XTP_HOST_STORAGE_WAIT |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|QDS_ASYNC_CHECK_CONSISTENCY_TASK |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|QDS_ASYNC_PERSIST_TASK |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|QDS_ASYNC_PERSIST_TASK_START |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|QDS_ASYNC_QUEUE |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|QDS_BCKG_TASK |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|QDS_BLOOM_FILTER |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|QDS_CLEANUP_STALE_QUERIES_TASK_MAIN_LOOP_SLEEP |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|QDS_CTXS |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|QDS_DB_DISK |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|QDS_DYN_VECTOR |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|QDS_EXCLUSIVE_ACCESS |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|QDS_HOST_INIT |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE [sssql17-md](../../includes/sssql17-md.md)] 以降。| 
|QDS_LOADDB |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|QDS_PERSIST_TASK_MAIN_LOOP_SLEEP |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|QDS_QDS_CAPTURE_INIT |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|QDS_SHUTDOWN_QUEUE |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|QDS_STMT |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|QDS_STMT_DISK |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|QDS_TASK_SHUTDOWN |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|QDS_TASK_START |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|QE_WARN_LIST_SYNC |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|QPJOB_KILL |非同期自動統計更新が実行を開始したときに、強制終了の呼び出しにより操作が取り消されたことを示します。 終了スレッドは一時中断され、強制終了コマンドの受信開始を待機します。 1 秒未満であれば問題はありません。| 
|QPJOB_WAITFOR_ABORT |非同期自動統計更新の実行中に、強制終了の呼び出しにより操作が取り消されたことを示します。 更新は現在完了していますが、終了スレッド メッセージ調整が完了するまでは一時中断されます。 これは通常の状態ですが、発生することはほとんどありません。発生しても非常に短い時間です。 1 秒未満であれば問題はありません。| 
|QRY_MEM_GRANT_INFO_MUTEX  |クエリ実行メモリ管理が、静的な許可情報リストへのアクセスを制御しようとするときに発生します。 この待機状態では、現在許可されており待機中のメモリ要求に関する情報が一覧表示されます。 またこの状態は、単純なアクセス制御状態です。 この状態では、長時間に及ぶ待機は避けてください。 このミューテックスが解放されない場合、新しいメモリを使用するすべてのクエリは応答を停止します。| 
|QRY_PARALLEL_THREAD_MUTEX |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|QRY_PROFILE_LIST_MUTEX |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|QUERY_ERRHDL_SERVICE_DONE |単に情報を示すためだけに特定されます。 サポートされていません。 <br /><br /> **適用対象**: [!INCLUDE[ssKilimanjaro_md](../../includes/sskilimanjaro-md.md)] のみ。 |  
|QUERY_WAIT_ERRHDL_SERVICE |単に情報を示すためだけに特定されます。  サポートされていません。 <br /><br /> **適用対象**: [!INCLUDE[ssKilimanjaro_md](../../includes/sskilimanjaro-md.md)] のみ。  |  
|QUERY_EXECUTION_INDEX_SORT_EVENT_OPEN |オフラインでのインデックス作成が並列実行される場合、並べ替えを行っている複数のワーカー スレッドが並べ替えファイルへのアクセスを同期するときに発生する場合があります。| 
|QUERY_NOTIFICATION_MGR_MUTEX  |クエリ通知マネージャー内のガベージ コレクション キューの同期中に発生します。| 
|QUERY_NOTIFICATION_SUBSCRIPTION_MUTEX  |クエリ通知内のトランザクションの状態の同期中に発生します。| 
|QUERY_NOTIFICATION_TABLE_MGR_MUTEX  |クエリ通知マネージャー内での内部同期中に発生します。| 
|QUERY_NOTIFICATION_UNITTEST_MUTEX  |単に情報を示すためだけに特定されます。 サポートされていません。 将来の互換性は保証されません。| 
|QUERY_OPTIMIZER_PRINT_MUTEX |クエリ オプティマイザー診断の出力作成の同期中に発生します。 この待機の種類は、Microsoft 製品サポートの指示に基づいて診断設定が有効になっている場合にのみ発生します。| 
|QUERY_TASK_ENQUEUE_MUTEX |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|QUERY_TRACEOUT |単に情報を示すためだけに特定されます。 サポートされていません。 将来の互換性は保証されません。| 
|RBIO_WAIT_VLF |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE [sssql17-md](../../includes/sssql17-md.md)] 以降。| 
|RBIO_RG_STORAGE |ページサーバーでの遅延ログの使用により、ハイパースケールデータベース計算ノードが制限されている場合に発生します。 <br /><br /> **適用対象**: Azure SQL Database ハイパースケール。|
|RBIO_RG_DESTAGE |長期的なログストレージによる遅延ログの使用により、ハイパースケールデータベース計算ノードが制限されている場合に発生します。 <br /><br /> **適用対象**: Azure SQL Database ハイパースケール。|
|RBIO_RG_REPLICA |読み取り可能なセカンダリレプリカノードによる遅延ログの使用により、ハイパースケールデータベース計算ノードが制限されている場合に発生します。 <br /><br /> **適用対象**: Azure SQL Database ハイパースケール。|
|RBIO_RG_LOCALDESTAGE |ログサービスによる遅延ログの使用により、ハイパースケールデータベース計算ノードが制限されている場合に発生します。 <br /><br /> **適用対象**: Azure SQL Database ハイパースケール。|
|RECOVER_CHANGEDB |ウォーム スタンバイ データベース内で、データベースの状態の同期中に発生します。| 
|RECOVERY_MGR_LOCK |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|REDO_THREAD_PENDING_WORK |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|REDO_THREAD_SYNC |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|REMOTE_BLOCK_IO |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE [sssql17-md](../../includes/sssql17-md.md)] 以降。| 
|REMOTE_DATA_ARCHIVE_MIGRATION_DMV |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|REMOTE_DATA_ARCHIVE_SCHEMA_DMV |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|REMOTE_DATA_ARCHIVE_SCHEMA_TASK_QUEUE |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|REPL_CACHE_ACCESS |レプリケーション アーティクル キャッシュでの同期中に発生します。 この待機中、レプリケーション ログ リーダーは停止し、パブリッシュされたテーブルに対するデータ定義言語 (DDL) ステートメントはブロックされます。| 
|REPL_HISTORYCACHE_ACCESS |内部使用のみです。| 
|REPL_SCHEMA_ACCESS |レプリケーション スキーマのバージョン情報の同期中に発生します。 この状態は、レプリケートされたオブジェクトで DDL ステートメントが実行されるとき、および、ログ リーダーが DDL の発生に基づいてバージョン管理されたスキーマを作成または使用するときに発生します。 トランザクションレプリケーションを使用する1つのパブリッシャーにパブリッシュされたデータベースが多数存在し、パブリッシュされたデータベースが非常にアクティブである場合、この待機の種類で競合が発生する可能性があります。| 
|REPL_TRANFSINFO_ACCESS |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|REPL_TRANHASHTABLE_ACCESS |内部使用のみです。| 
|REPL_TRANTEXTINFO_ACCESS |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|REPLICA_WRITES |タスクが、データベース スナップショットまたは DBCC レプリカへのページ書き込みの完了を待機しているときに発生します。| 
|REQUEST_DISPENSER_PAUSE |タスクが、未処理の I/O がすべて完了しスナップショット バックアップ用にファイルの I/O が固定されるのを待機しているときに発生します。| 
|REQUEST_FOR_DEADLOCK_SEARCH |デッドロック モニターが、次のデッドロック検索の開始を待機しているときに発生します。 この待機は、デッドロックが検出されてから次に検出されるまでの間に発生することが予想されます。このリソースにおける合計待機時間が長くても問題はありません。| 
|RESERVED_MEMORY_ALLOCATION_EXT |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|RESMGR_THROTTLED |新しい要求が着信し、GROUP_MAX_REQUESTS 設定に基づいて絞り込まれたときに発生します。| 
|RESOURCE_GOVERNOR_IDLE |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|RESOURCE_QUEUE |さまざまな内部リソース キューの同期中に発生します。| 
|RESOURCE_SEMAPHORE |他の同時実行クエリが原因で、クエリの実行中にクエリメモリ要求がすぐに許可されない場合に発生します。 待機および待機時間が高い値を示している場合は、同時実行クエリの数が多すぎるか、またはメモリ要求の数が多すぎる可能性があります。 この型を過度に待機すると、"クエリ実行のためのメモリリソースを待機中にタイムアウトが発生しました。" という SQL [エラー 8645](../errors-events/mssqlserver-8645-database-engine-error.md)が発生する可能性があります。 クエリを再実行してください。 "<br /><br /> メモリ許可待機の詳細とトラブルシューティングのアイデアについては、こちらの[ブログ投稿](https://techcommunity.microsoft.com/t5/sql-server-support/memory-grants-the-mysterious-sql-server-memory-consumer-with/ba-p/333994)を参照してください。| 
|RESOURCE_SEMAPHORE_MUTEX |クエリが、スレッドを予約するための要求を待機しているときに発生します。 この待機は、クエリのコンパイルとメモリの要求許可を同期しているときにも発生します。| 
|RESOURCE_SEMAPHORE_QUERY_COMPILE |コンパイルされる同時実行クエリの数が、スロットルの制限値に達したときに発生します。 待機時間と待機時間が長い場合は、過剰なコンパイル、再コンパイル、または、可能なプランがないことを示している可能性があります。| 
|RESOURCE_SEMAPHORE_SMALL_QUERY |他の同時実行クエリがあるため、サイズの小さいクエリからのメモリ要求がすぐに許可されない場合に発生します。 待機時間は数秒以内である必要があります。要求したメモリが数秒以内に許可されないと、サーバーによって要求がメイン クエリのメモリ プールに転送されます。 待機が高い値を示している場合は、待機クエリによって主要なメモリ プールがブロックされているときに、サイズの小さい同時実行クエリの数が多すぎる可能性があります。 <br /><br /> **適用対象**: [!INCLUDE[ssKilimanjaro_md](../../includes/sskilimanjaro-md.md)] のみ。 |  
|RESTORE_FILEHANDLECACHE_ENTRYLOCK |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|RESTORE_FILEHANDLECACHE_LOCK |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|RG_RECONFIG |内部使用のみです。| 
|ROWGROUP_OP_STATS |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|ROWGROUP_VERSION |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|RTDATA_LIST |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|SATELLITE_CARGO |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|SATELLITE_SERVICE_SETUP |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|SATELLITE_TASK |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|SBS_DISPATCH |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE [sssql17-md](../../includes/sssql17-md.md)] 以降。| 
|SBS_RECEIVE_TRANSPORT |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE [sssql17-md](../../includes/sssql17-md.md)] 以降。| 
|SBS_TRANSPORT |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE [sssql17-md](../../includes/sssql17-md.md)] 以降。| 
|SCAN_CHAR_HASH_ARRAY_INITIALIZATION |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|SEC_DROP_TEMP_KEY |一時セキュリティ キーを削除しようとして失敗した後、再試行するまでの間に発生します。| 
|SECURITY_CNG_PROVIDER_MUTEX |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE [sssql17-md](../../includes/sssql17-md.md)] 以降。| 
|SECURITY_CRYPTO_CONTEXT_MUTEX |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|SECURITY_DBE_STATE_MUTEX |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|SECURITY_KEYRING_RWLOCK |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|SECURITY_MUTEX |ミューテックスを待機しているときに発生します。このミューテックスは、拡張キー管理 (EKM) 暗号化サービス プロバイダーのグローバル リストへのアクセス、および EKM セッションのセッション スコープ リストへのアクセスを制御します。| 
|SECURITY_RULETABLE_MUTEX |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|SEMPLAT_DSI_BUILD |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|SEQUENCE_GENERATION |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|SEQUENTIAL_GUID |新しいシーケンシャル GUID を取得中に発生します。| 
|SERVER_IDLE_CHECK |[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンスのアイドル状態を同期している間に、リソース モニターが [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンスをアイドルとして宣言しようとするとき、または起動しようとするときに発生します。| 
|SERVER_RECONFIGURE |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|SESSION_WAIT_STATS_CHILDREN |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|SHARED_DELTASTORE_CREATION |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|SHUTDOWN |シャットダウン ステートメントが、アクティブな接続の終了を待機しているときに発生します。| 
|SLEEP_BPOOL_FLUSH |ディスク サブシステムが飽和状態にならないよう、チェックポイントで新しい I/O の実行をスロットル中に発生します。| 
|SLEEP_BUFFERPOOL_HELPLW |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|SLEEP_DBSTARTUP |すべてのデータベースが復旧するのを待機している間、データベースの起動中に発生します。| 
|SLEEP_DCOMSTARTUP |DCOM の初期化が完了するのを待機している間、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンスの起動中に、多くても 1 回だけ発生します。| 
|SLEEP_MASTERDBREADY |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|SLEEP_MASTERMDREADY |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|SLEEP_MASTERUPGRADED |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|SLEEP_MEMORYPOOL_ALLOCATEPAGES |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|SLEEP_MSDBSTARTUP |SQL トレースが msdb データベースの起動完了を待機しているときに発生します。| 
|SLEEP_RETRY_VIRTUALALLOC |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|SLEEP_SYSTEMTASK |バックグラウンドタスクの開始中に発生し、tempdb が起動を完了するのを待機しています。| 
|SLEEP_TASK |ジェネリック イベントの発生を待機している間、タスクがスリープ状態のときに発生します。| 
|SLEEP_TEMPDBSTARTUP |タスクが tempdb の起動完了を待機しているときに発生します。| 
|SLEEP_WORKSPACE_ALLOCATEPAGE |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|SLO_UPDATE |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|SMSYNC |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|SNI_CONN_DUP |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|SNI_CRITICAL_SECTION |[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ネットワーク コンポーネント内での内部同期中に発生します。| 
|SNI_HTTP_WAITFOR_0_DISCON |[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のシャットダウン中、未完了の HTTP 接続が終了するのを待機している間に発生します。| 
|SNI_LISTENER_ACCESS |NUMA (non-uniform memory access) ノードが状態の変化を更新するのを待機している間に発生します。 状態の変化へのアクセスはシリアル化されます。| 
|SNI_TASK_COMPLETION |NUMA ノード状態の変化中にすべてのタスクが終了するのを待機しているときに発生します。| 
|SNI_WRITE_ASYNC |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE [sssql17-md](../../includes/sssql17-md.md)] 以降。| 
|SOAP_READ |HTTP ネットワークの読み取り完了を待機しているときに発生します。| 
|SOAP_WRITE |HTTP ネットワークの書き込み完了を待機しているときに発生します。| 
|SOCKETDUPLICATEQUEUE_CLEANUP |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|SOS_CALLBACK_REMOVAL |コールバックを削除するために、コールバックの一覧で同期を実行しているときに発生します。 サーバーの初期化が完了した後、通常この待機カウンターが変更されることはありません。| 
|SOS_DISPATCHER_MUTEX |ディスパッチャー プールの内部初期化中に発生します。 これには、プールの調整中も含まれます。| 
|SOS_LOCALALLOCATORLIST |[!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] メモリ マネージャー内での内部同期中に発生します。 <br /><br /> **適用対象**: [!INCLUDE[ssKilimanjaro_md](../../includes/sskilimanjaro-md.md)] のみ。 |  
|SOS_MEMORY_TOPLEVELBLOCKALLOCATOR |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|SOS_MEMORY_USAGE_ADJUSTMENT |プール間でメモリ使用量が調整されている場合に発生します。| 
|SOS_OBJECT_STORE_DESTROY_MUTEX |メモリ プールからオブジェクトを破棄するときに、メモリ プール内での内部同期中に発生します。| 
|SOS_PHYS_PAGE_CACHE |スレッドは、物理ページを割り当てる前に、またはこれらのページをオペレーティングシステムに返す前に取得する必要があるミューテックスの取得を待機する時間のアカウントです。 この型の待機は、のインスタンスが AWE メモリを使用している場合にのみ表示され [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|SOS_PROCESS_AFFINITY_MUTEX |関係設定を処理するためのアクセスの同期中に発生します。| 
|SOS_RESERVEDMEMBLOCKLIST |メモリマネージャーでの内部同期中に発生し [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。 <br /><br /> **適用対象**: [!INCLUDE[ssKilimanjaro_md](../../includes/sskilimanjaro-md.md)] のみ。 |  
|SOS_SCHEDULER_YIELD |タスクが、他のタスクの実行にスケジューラを自主的に解放したときに発生します。 この待機中、タスクは実行可能キューでクォンタムが更新されるのを待機しています。つまり、CPU での実行が再度スケジュールされるのを待機しています。 この待機の種類で長時間待機すると、インデックスまたはテーブルスキャンを実行するクエリを最適化する機会が最も頻繁に示されます。 プランの回帰、不足しているインデックス、統計の更新、クエリの再書き込みに注目します。 ランタイムを最適化すると、タスクが複数回発生する必要性が減ります。 このような CPU を消費するタスクのクエリ時間が許容される場合は、この待機の種類が想定されているので、無視してかまいません。 | 
|SOS_SMALL_PAGE_ALLOC |メモリの割り当てと開放中に発生します。このメモリはいくつかのメモリ オブジェクトによって管理されます。| 
|SOS_STACKSTORE_INIT_MUTEX |内部ストアの初期化の同期中に発生します。| 
|SOS_SYNC_TASK_ENQUEUE_EVENT |タスクが同期して開始したときに発生します。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のほとんどのタスクは非同期で開始し、タスクの要求が作業キューに挿入されるとすぐ制御が最初に戻ります。| 
|SOS_VIRTUALMEMORY_LOW |メモリ割り当てが、リソースマネージャーによる仮想メモリの解放を待機しているときに発生します。| 
|SOSHOST_EVENT |CLR などのホストされるコンポーネントが、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] イベント同期オブジェクト上で待機しているときに発生します。| 
|SOSHOST_INTERNAL |CLR などのホストされるコンポーネントで使用される、メモリ マネージャーのコールバックの同期中に発生します。| 
|SOSHOST_MUTEX |CLR などのホストされるコンポーネントが、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ミューテックス同期オブジェクト上で待機しているときに発生します。| 
|SOSHOST_RWLOCK |CLR などのホストされるコンポーネントが、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] リーダー/ライター同期オブジェクト上で待機しているときに発生します。| 
|SOSHOST_SEMAPHORE |CLR などのホストされるコンポーネントが、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セマフォ同期オブジェクト上で待機しているときに発生します。| 
|SOSHOST_SLEEP |ジェネリック イベントの発生を待機している間、ホストされるタスクがスリープ状態のときに発生します。 ホストされるタスクは、CLR などのホストされるコンポーネントで使用されます。| 
|SOSHOST_TRACELOCK |ストリームをトレースするためのアクセスの同期中に発生します。| 
|SOSHOST_WAITFORDONE |CLR などのホストされるコンポーネントが、タスクの完了を待機しているときに発生します。| 
|SP_PREEMPTIVE_SERVER_DIAGNOSTICS_SLEEP |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|SP_SERVER_DIAGNOSTICS_BUFFER_ACCESS |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|SP_SERVER_DIAGNOSTICS_INIT_MUTEX |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|SP_SERVER_DIAGNOSTICS_SLEEP |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|SQLCLR_APPDOMAIN |CLR が、アプリケーション ドメインの起動完了を待機しているときに発生します。| 
|SQLCLR_ASSEMBLY |アプリケーション ドメインに読み込まれたアセンブリ一覧へのアクセスを待機しているときに発生します。| 
|SQLCLR_DEADLOCK_DETECTION |CLR がデッドロック検出の完了を待機しているときに発生します。| 
|SQLCLR_QUANTUM_PUNISHMENT |クォンタムの実行時間を超えたことが原因で、CLR タスクがスロットルされたときに発生します。 このスロットルは、こうしたリソース消費の多いタスクによる他のタスクへの影響を軽減するために行われます。| 
|SQLSORT_NORMMUTEX |内部同期中、内部の並べ替え構造が初期化される間に発生します。| 
|SQLSORT_SORTMUTEX |内部同期中、内部の並べ替え構造が初期化される間に発生します。| 
|SQLTRACE_BUFFER_FLUSH |タスクが、バックグラウンド タスクによってトレース バッファーが 4 秒ごとにディスクにフラッシュされるのを待機しているときに発生します。 <br /><br /> **適用対象**: [!INCLUDE[ssKilimanjaro_md](../../includes/sskilimanjaro-md.md)] のみ。 |  
|SQLTRACE_FILE_BUFFER |ファイルのトレース中、トレース バッファーの同期中に発生します。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|SQLTRACE_FILE_READ_IO_COMPLETION |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|SQLTRACE_FILE_WRITE_IO_COMPLETION |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|SQLTRACE_INCREMENTAL_FLUSH_SLEEP |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|SQLTRACE_LOCK |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssKilimanjaro_md](../../includes/sskilimanjaro-md.md)] のみ。 |  
|SQLTRACE_PENDING_BUFFER_WRITERS |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|SQLTRACE_SHUTDOWN |トレースのシャットダウンが、未処理のトレース イベントが完了するのを待機しているときに発生します。| 
|SQLTRACE_WAIT_ENTRIES |SQL トレース イベント キューが、パケットの到着を待機しているときに発生します。| 
|SRVPROC_SHUTDOWN |シャットダウン プロセスが、正常にシャットダウンするために内部リソースの解放を待機しているときに発生します。| 
|STARTUP_DEPENDENCY_MANAGER |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|TDS_BANDWIDTH_STATE |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|TDS_INIT |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|TDS_PROXY_CONTAINER |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|TEMPOBJ |一時オブジェクトの削除が同期されるときに発生します。 この待機が発生するのはまれで、タスクが temp テーブルに対して削除操作を行うための排他アクセスを要求した場合にのみ発生します。| 
|TEMPORAL_BACKGROUND_PROCEED_CLEANUP |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE [sssql17-md](../../includes/sssql17-md.md)] 以降。| 
|TERMINATE_LISTENER |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|THREADPOOL |タスクがワーカーの実行を待機しているときに発生します。 ワーカー数の最大設定値が低すぎるか、バッチ実行時間が長すぎるため、他のバッチ用にワーカー数が削減されている可能性があります。| 
|TIMEPRIV_TIMEPERIOD |拡張イベント タイマーの内部初期化中に発生します。| 
|TRACE_EVTNOTIF |内部使用のみです。| 
|TRACEWRITE |SQL トレースの行セット トレース プロバイダーが、空きバッファーまたは処理するイベントを含むバッファーのいずれかを待機しているときに発生します。| 
|TRAN_MARKLATCH_DT |トランザクション マーク ラッチで破棄モードのラッチを待機しているときに発生します。 トランザクション マーク ラッチは、マークされたトランザクションでのコミットの同期に使用されます。| 
|TRAN_MARKLATCH_EX |マークされたトランザクションで排他モードのラッチを待機しているときに発生します。 トランザクション マーク ラッチは、マークされたトランザクションでのコミットの同期に使用されます。| 
|TRAN_MARKLATCH_KP |マークされたトランザクションで保持モードのラッチを待機しているときに発生します。 トランザクション マーク ラッチは、マークされたトランザクションでのコミットの同期に使用されます。| 
|TRAN_MARKLATCH_NL |単に情報を示すためだけに特定されます。 サポートされていません。 将来の互換性は保証されません。| 
|TRAN_MARKLATCH_SH |マークされたトランザクションで共有モードのラッチを待機しているときに発生します。 トランザクション マーク ラッチは、マークされたトランザクションでのコミットの同期に使用されます。| 
|TRAN_MARKLATCH_UP |マークされたトランザクションで更新モードのラッチを待機しているときに発生します。 トランザクション マーク ラッチは、マークされたトランザクションでのコミットの同期に使用されます。| 
|TRANSACTION_MUTEX |複数のバッチによるトランザクションへのアクセスの同期中に発生します。| 
|UCS_ENDPOINT_CHANGE |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|UCS_MANAGER |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|UCS_MEMORY_NOTIFICATION |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|UCS_SESSION_REGISTRATION |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|UCS_TRANSPORT |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|UCS_TRANSPORT_STREAM_CHANGE |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|UTIL_PAGE_ALLOC |トランザクション ログのスキャンが、メモリに負荷がかかっている間に、使用できるメモリを待機しているときに発生します。| 
|VDI_CLIENT_COMPLETECOMMAND |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|VDI_CLIENT_GETCOMMAND |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|VDI_CLIENT_OPERATION |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|VDI_CLIENT_OTHER |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|VERSIONING_COMMITTING |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|VIA_ACCEPT |起動中に仮想インターフェイス アダプター (VIA) プロバイダー接続が完了すると発生します。| 
|VIEW_DEFINITION_MUTEX |キャッシュされたビュー定義へのアクセスの同期中に発生します。| 
|WAIT_FOR_RESULTS |クエリ通知が行われるのを待機しているときに発生します。| 
|WAIT_ON_SYNC_STATISTICS_REFRESH |クエリのコンパイルと実行を再開する前に、同期統計の更新が完了するのを待機しているときに発生します。<br /><br /> **適用対象**:[!INCLUDE[sql-server-2019](../../includes/sssql19-md.md)] 以降|
|WAIT_SCRIPTDEPLOYMENT_REQUEST |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|WAIT_SCRIPTDEPLOYMENT_WORKER |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|WAIT_XLOGREAD_SIGNAL |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE [sssql17-md](../../includes/sssql17-md.md)] 以降。| 
|WAIT_XTP_ASYNC_TX_COMPLETION |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|WAIT_XTP_CKPT_AGENT_WAKEUP |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|WAIT_XTP_CKPT_CLOSE |チェックポイントの完了を待機しているときに発生します。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|WAIT_XTP_CKPT_ENABLED |チェックポイント処理が無効になっており、チェックポイント処理が有効になるのを待機しているときに発生します。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|WAIT_XTP_CKPT_STATE_LOCK |チェックポイントの状態のチェックを同期するときに発生します。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|WAIT_XTP_COMPILE_WAIT |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE [sssql17-md](../../includes/sssql17-md.md)] 以降。| 
|WAIT_XTP_GUEST |データベースメモリアロケーターがメモリ不足通知の受信を停止する必要がある場合に発生します。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|WAIT_XTP_HOST_WAIT |待機がデータベースエンジンによってトリガーされ、ホストによって実装されると発生します。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|WAIT_XTP_OFFLINE_CKPT_BEFORE_REDO |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|WAIT_XTP_OFFLINE_CKPT_LOG_IO |オフラインチェックポイントがログ読み取り IO の完了を待機しているときに発生します。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|WAIT_XTP_OFFLINE_CKPT_NEW_LOG |オフラインチェックポイントが、新しいログレコードのスキャンを待機しているときに発生します。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|WAIT_XTP_PROCEDURE_ENTRY |DROP PROCEDURE がその現在のすべての実行が完了するのを待機しているときに発生します。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|WAIT_XTP_RECOVERY |データベース復旧が、メモリ最適化オブジェクトの復旧が完了するのを待機しているときに発生します。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|WAIT_XTP_SERIAL_RECOVERY |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE [sssql17-md](../../includes/sssql17-md.md)] 以降。| 
|WAIT_XTP_SWITCH_TO_INACTIVE |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|WAIT_XTP_TASK_SHUTDOWN |In-Memory OLTP スレッドの完了を待機しているときに発生します。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|WAIT_XTP_TRAN_DEPENDENCY |トランザクションの依存関係を待機しているときに発生します。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|WAITFOR |WAITFOR Transact-sql ステートメントの結果として発生します。 この待機時間は、ステートメントに渡すパラメーターによって決まります。 この待機はユーザーによって開始されるものです。| 
|WAITFOR_PER_QUEUE |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|WAITFOR_TASKSHUTDOWN |単に情報を示すためだけに特定されます。 サポートされていません。 将来の互換性は保証されません。| 
|WAITSTAT_MUTEX |`sys.dm_os_wait_stats` の設定に使用する統計コレクションへのアクセスの同期中に発生します。| 
|WCC |単に情報を示すためだけに特定されます。 サポートされていません。 将来の互換性は保証されません。| 
|WINDOW_AGGREGATES_MULTIPASS |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|WINFAB_API_CALL |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|WINFAB_REPLICA_BUILD_OPERATION |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|WINFAB_REPORT_FAULT |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|WORKTBL_DROP |作業テーブルの削除が失敗してから、再試行されるまで一時停止しているときに発生します。| 
|WRITE_COMPLETION |書き込み操作が進行中のときに発生します。| 
|WRITELOG |ログ フラッシュの完了を待機しているときに発生します。 ログ フラッシュの原因となる主な操作としては、チェックポイントとトランザクションのコミットがあります。| 
|XACT_OWN_TRANSACTION |トランザクションの所有権取得を待機しているときに発生します。| 
|XACT_RECLAIM_SESSION |セッションの現在の所有者がその所有権を解放するのを待機しているときに発生します。| 
|XACTLOCKINFO |トランザクションのロック一覧へのアクセスの同期中に発生します。 このロック一覧には、トランザクション自体だけでなく、ページ分割時にデッドロック検出やロック移行などの操作からもアクセスが行われます。| 
|XACTWORKSPACE_MUTEX |トランザクションからの参加解除や、トランザクションの参加メンバー間におけるデータベース ロック数の同期中に発生します。| 
|XDB_CONN_DUP_HASH |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|XDES_HISTORY |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|XDES_OUT_OF_ORDER_LIST |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|XDES_SNAPSHOT |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|XDESTSVERMGR |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|XE_BUFFERMGR_ALLPROCESSED_EVENT |拡張イベント セッション バッファーがターゲットにフラッシュされたときに発生します。 この待機はバックグラウンド スレッドで発生します。| 
|XE_BUFFERMGR_FREEBUF_EVENT |次のいずれかの条件が該当した場合に発生します。| 
|XE_CALLBACK_LIST |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|XE_CX_FILE_READ |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|XE_DISPATCHER_CONFIG_SESSION_LIST |非同期ターゲットを使用している拡張イベント セッションが開始されたか、停止された場合に発生します。 このタイプの待機は、次のいずれかを示します。| 
|XE_DISPATCHER_JOIN |拡張イベント セッションに使用されているバックグラウンド スレッドの終了時に発生します。| 
|XE_DISPATCHER_WAIT |拡張イベント セッションに使用されているバックグラウンド スレッドが、イベント バッファーの処理を待っているときに発生します。| 
|XE_FILE_TARGET_TVF |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|XE_LIVE_TARGET_TVF |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。| 
|XE_MODULEMGR_SYNC |単に情報を示すためだけに特定されます。 サポートされていません。 将来の互換性は保証されません。| 
|XE_OLS_LOCK |単に情報を示すためだけに特定されます。 サポートされていません。 将来の互換性は保証されません。| 
|XE_PACKAGE_LOCK_BACKOFF |単に情報を示すためだけに特定されます。 サポートされていません。 <br /><br /> **適用対象**: [!INCLUDE[ssKilimanjaro_md](../../includes/sskilimanjaro-md.md)] のみ。 |  
|XE_SERVICES_EVENTMANUAL |内部使用のみです。| 
|XE_SERVICES_MUTEX |内部使用のみです。| 
|XE_SERVICES_RWLOCK |内部使用のみです。| 
|XE_SESSION_CREATE_SYNC |内部使用のみです。| 
|XE_SESSION_FLUSH |内部使用のみです。| 
|XE_SESSION_SYNC |内部使用のみです。| 
|XE_STM_CREATE |内部使用のみです。| 
|XE_TIMER_EVENT |内部使用のみです。| 
|XE_TIMER_MUTEX |内部使用のみです。| 
|XE_TIMER_TASK_DONE |内部使用のみです。| 
|XIO_CREDENTIAL_MGR_RWLOCK |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|XIO_CREDENTIAL_RWLOCK |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|XIO_EDS_MGR_RWLOCK |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE [sssql17-md](../../includes/sssql17-md.md)] 以降。| 
|XIO_EDS_RWLOCK |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE [sssql17-md](../../includes/sssql17-md.md)] 以降。| 
|XIO_IOSTATS_BLOBLIST_RWLOCK |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE [sssql17-md](../../includes/sssql17-md.md)] 以降。| 
|XIO_IOSTATS_FCBLIST_RWLOCK |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE [sssql17-md](../../includes/sssql17-md.md)] 以降。| 
|XIO_LEASE_RENEW_MGR_RWLOCK |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|XTP_HOST_DB_COLLECTION |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|XTP_HOST_LOG_ACTIVITY |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|XTP_HOST_PARALLEL_RECOVERY |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|XTP_PREEMPTIVE_TASK |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|XTP_TRUNCATION_LSN |内部使用のみです。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降。| 
|XTPPROC_CACHE_ACCESS |すべてのネイティブコンパイルストアドプロシージャキャッシュオブジェクトにアクセスするときに発生します。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。| 
|XTPPROC_PARTITIONED_STACK_CREATE |特定のプロシージャのネイティブ コンパイル ストアド プロシージャのキャッシュ構造を NUMA ノードごとに割り当てる (単一スレッドで行われる必要がある) ときに発生します。 <br /><br /> **適用対象**: [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 以降。|

  
 次の Xevent は、パーティション **切り替え** とオンラインインデックス再構築に関連しています。 構文の詳細については、「 [ALTER TABLE &#40;transact-sql&#41;](../../t-sql/statements/alter-table-transact-sql.md) 」および「 [Alter INDEX &#40;transact-sql&#41;](../../t-sql/statements/alter-index-transact-sql.md)」を参照してください。  
  
-   lock_request_priority_state  
  
-   process_killed_by_abort_blockers  
  
-   ddl_with_wait_at_low_priority  
  
 ロックの互換性マトリックスについては、「 [sys.dm_tran_locks &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql.md)」を参照してください。  
  
## <a name="see-also"></a>関連項目  
    
 [SQL Server オペレーティングシステム関連の動的管理ビュー &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sql-server-operating-system-related-dynamic-management-views-transact-sql.md)   
 [sys.dm_exec_session_wait_stats &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-session-wait-stats-transact-sql.md)   
 [sys.dm_db_wait_stats &#40;Azure SQL Database&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database.md)    
 [sys.dm_os_sys_info &#40;Transact-sql&#41;](sys-dm-os-sys-info-transact-sql.md)    