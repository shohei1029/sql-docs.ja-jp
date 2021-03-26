---
description: sys.dm_db_wait_stats (Azure SQL データベース)
title: sys.dm_db_wait_stats (Azure SQL データベース)
ms.custom: ''
ms.date: 03/12/2021
ms.service: sql-database
ms.reviewer: ''
ms.topic: reference
f1_keywords:
- dm_db_wait_stats_TSQL
- dm_db_wait_stats
- sys.dm_db_wait_stats
- sys.dm_db_wait_stats_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_db_wait_stats dynamic management view
- dm_db_wait_stats
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: = azuresqldb-current
ms.openlocfilehash: 6819bba097963249e68d61941e4dc4e704a2b7a7
ms.sourcegitcommit: c242f423cc3b776c20268483cfab0f4be54460d4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/25/2021
ms.locfileid: "105551618"
---
# <a name="sysdm_db_wait_stats-azure-sql-database"></a>sys.dm_db_wait_stats (Azure SQL データベース)
[!INCLUDE[Azure SQL Database Azure SQL Managed Instance](../../includes/applies-to-version/asdb-asdbmi.md)]

  操作中に実行されたスレッドにより検出されたすべての待機に関する情報を返します。 この集計ビューを使って、[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] および特定のクエリとバッチに関するパフォーマンスの問題を診断できます。  
  
 クエリ実行中に発生している待機時間の種類によって、クエリにボトルネックや機能停止ポイントがあるかどうかを判断できます。 また、サーバー全体の待機時間や待機カウントが高い値を示している場合は、サーバー インスタンス内の対話型クエリの対話にボトルネックまたはホット スポットが存在していることを表しています。 たとえば、ロック待機の場合はクエリによるデータ競合、ページ I/O ラッチ待機の場合は遅い I/O 反応時間、ページ ラッチ更新待機の場合は不正なファイル レイアウトが存在していると判断できます。  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|wait_type|**nvarchar(60)**|待機の種類の名前。 詳細については、このトピックの「[待機の種類](#WaitTypes)」を参照してください。|  
|waiting_tasks_count|**bigint**|この待機の種類における待機の数。 このカウンターは、待機が開始するたび増加します。|  
|wait_time_ms|**bigint**|この待機の種類における総待機時間 (ミリ秒単位)。 この時間には signal_wait_time_ms が含まれます。|  
|max_wait_time_ms|**bigint**|この待機の種類における最大待機時間。|  
|signal_wait_time_ms|**bigint**|待機スレッドがシグナルを受け取ってから実行を開始するまでの時間。|  
  
## <a name="remarks"></a>解説  
  
-   この動的管理ビューには、現在のデータベースのデータのみが表示されます。  
  
-   この動的管理ビューでは、既に終わった待機時間が示されます。 現在の待機時間は示されません。  
  
-   カウンターは、データベースが移動されるたび、またはオフラインになるたびに 0 にリセットされます。  
  
-   次のいずれかに該当する場合、SQL Server ワーカー スレッドは待機中とは見なされません。  
  
    -   リソースが使用可能になった。  
  
    -   キューが空でない。  
  
    -   外部プロセスが終了した。  
  

> [!NOTE]
> これらの統計は SQL Database フェールオーバーイベントの後は保持されず、統計が最後にリセットされたときまたはデータベースエンジンが起動してからのすべてのデータが累積されます。 Sys.dm_os_sys_info の列を使用して、 `sqlserver_start_time` データベースエンジンの最後の起動時刻を検索します。 [](sys-dm-os-sys-info-transact-sql.md)   
  
## <a name="permissions"></a>アクセス許可  
 サーバーに対する VIEW DATABASE STATE 権限が必要です。  
  
##  <a name="types-of-waits"></a><a name="WaitTypes"></a> 待機の種類  
 リソース待機  
 リソース待機は、ワーカーがリソースへのアクセスを要求したときに、そのリソースが他のワーカーによって使用されているか、まだ準備ができておらず、使用できない場合に発生します。 リソース待機の例としては、ロック、ラッチ、ネットワークおよびディクス I/O 待機があります。 ロック待機およびラッチ待機は、同期オブジェクトで発生する待機です。  
  
 キュー待機  
 キュー待機は、ワーカーが作業割り当ての待機でアイドル状態となっている場合に発生します。 キュー待機は、デッドロック監視のようなシステム バックグラウンド タスクや、削除されたレコードのクリーンアップ タスクで最も多く発生します。 これらのタスクは、作業要求が作業キューに配置されるまで待機します。 キュー待機は、新しいパケットがキューに配置されていない場合でも、定期的にアクティブになることがあります。  
  
 外部待機  
 外部待機は、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ワーカーが、拡張ストアド プロシージャの呼び出しやリンク サーバー クエリなどの外部イベントの終了を待機しているときに発生します。 ブロッキングの問題を診断するときには、外部待機が発生していてもワーカーがアイドル状態になっているとは限らないことに注意してください。ワーカーはアクティブ状態で外部コードを実行している可能性があるためです。  
  
 スレッドは、待機中でなくなってもすぐに実行を開始する必要はありません。 このようなスレッドは、最初に実行可能なワーカーのキューに配置された後、スケジューラに従って実行するために、クォンタムの間待機する必要があります。  
  
 では [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 、待機時間カウンターは **bigint** 値であるため、以前のバージョンのの同等のカウンターとしてカウンターロールオーバーが発生する可能性は低く [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] なります。  
  
 次の表は、タスクで発生する待機の種類の一覧です。  
  
|待機の種類|説明|  
|---------------|-----------------|  
|ABR|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|ASSEMBLY_LOAD|アセンブリ読み込みへの排他アクセス時に発生します。|  
|ASYNC_DISKPOOL_LOCK|ファイルの作成や初期化などのタスクを実行している並列スレッドを同期しようとすると発生します。|  
|ASYNC_IO_COMPLETION|タスクが I/O の終了を待機しているときに発生します。|  
|ASYNC_NETWORK_IO|ネットワークの書き込みでタスクがブロックされているときに発生します。 クライアントがサーバーからのデータを処理しているかどうかを確認してください。|  
|AUDIT_GROUPCACHE_LOCK|特殊なキャッシュへのアクセスを制御するロックに待機があるときに発生します。 このキャッシュには、各監査アクション グループの監査に使用されている監査報告書についての情報が格納されます。|  
|AUDIT_LOGINCACHE_LOCK|特殊なキャッシュへのアクセスを制御するロックに待機があるときに発生します。 このキャッシュには、ログイン監査アクション グループの監査に使用されている監査報告書についての情報が格納されます。|  
|AUDIT_ON_DEMAND_TARGET_LOCK|ロックに待機があるときに発生します。このロックは、監査に関係する拡張イベントのターゲットを確実に単独で初期化できるようにするために使用されるものです。|  
|AUDIT_XE_SESSION_MGR|ロックに待機があるときに発生します。このロックは、監査に関係する拡張イベントのセッションの開始と終了を同期するために使用されるものです。|  
|BACKUP|バックアップ処理の一部としてタスクがブロックされているときに発生します。|  
|BACKUP_OPERATOR|タスクがテープのマウントを待機しているときに発生します。|  
|BACKUPBUFFER|バックアップ タスクが、データまたはデータを格納するバッファーを待機しているときに発生します。 この待機は、タスクがテープのマウントを待機しているとき以外はほとんど発生しません。|  
|BACKUPIO|バックアップ タスクが、データまたはデータを格納するバッファーを待機しているときに発生します。 この待機は、タスクがテープのマウントを待機しているとき以外はほとんど発生しません。|  
|BACKUPTHREAD|タスクがバックアップ タスクの終了を待機しているときに発生します。 待機時間の長さは、数分から数時間に及ぶ場合があります。 待機中のタスクが I/O 処理中である場合は、この待機が発生しても問題はありません。|  
|BAD_PAGE_PROCESS|問題があると考えられるバックグラウンドのページ ロガーが、5 秒間隔より頻繁な実行を回避しようとする場合に発生します。 問題があると考えられるページが多くある場合、ロガーは頻繁に実行されます。|  
|BROKER_CONNECTION_RECEIVE_TASK|接続エンドポイントでメッセージを受信するためアクセスを待機しているときに発生します。 エンドポイントへの受信アクセスはシリアル化されます。|  
|BROKER_ENDPOINT_STATE_MUTEX|[!INCLUDE[ssSB](../../includes/sssb-md.md)] 接続エンドポイントの状態へのアクセスが競合しているときに発生します。 変更の状態へのアクセスはシリアル化されます。|  
|BROKER_EVENTHANDLER|タスクが [!INCLUDE[ssSB](../../includes/sssb-md.md)] のプライマリ イベント ハンドラーを待機しているときに発生します。 この待機は、非常に短い時間の待機です。|  
|BROKER_INIT|各アクティブ データベースで [!INCLUDE[ssSB](../../includes/sssb-md.md)] を初期化するときに発生します。 この待機は、発生頻度の低い待機です。|  
|BROKER_MASTERSTART|タスクが [!INCLUDE[ssSB](../../includes/sssb-md.md)] のプライマリ イベント ハンドラーの起動を待機しているときに発生します。 この待機は、非常に短い時間の待機です。|  
|BROKER_RECEIVE_WAITFOR|RECEIVE WAITFOR が待機しているときに発生します。 これは、メッセージの受信準備ができていない場合によく起こります。|  
|BROKER_REGISTERALLENDPOINTS|[!INCLUDE[ssSB](../../includes/sssb-md.md)] の接続エンドポイントの初期化中に発生します。 この待機は、非常に短い時間の待機です。|  
|BROKER_SERVICE|発信先サービスに関連付けられている [!INCLUDE[ssSB](../../includes/sssb-md.md)] 宛先一覧が更新されるか優先順位が設定し直されると発生します。|  
|BROKER_SHUTDOWN|[!INCLUDE[ssSB](../../includes/sssb-md.md)] のシャットダウンが予定されている場合に発生します。 この待機は、非常に短い時間の待機です。|  
|BROKER_TASK_STOP|[!INCLUDE[ssSB](../../includes/sssb-md.md)] キューのタスク ハンドラーがタスクをシャットダウンしようとした場合に発生します。 ステート チェックがシリアル化されます。ステート チェックは事前に実行状態になっている必要があります。|  
|BROKER_TO_FLUSH|[!INCLUDE[ssSB](../../includes/sssb-md.md)] レイジー フラッシャが、メモリ内転送オブジェクトを作業テーブルにフラッシュした場合に発生します。|  
|BROKER_TRANSMITTER|[!INCLUDE[ssSB](../../includes/sssb-md.md)] 送信機能が作業を待機しているときに発生します。|  
|BUILTIN_HASHKEY_MUTEX|この待機はインスタンスの起動後、内部データ構造の初期化中に発生することがあります。 データ構造がいったん初期化されると、それ以降に再発生することはありません。|  
|CHECK_PRINT_RECORD|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|CHECKPOINT_QUEUE|チェックポイント タスクが、次のチェックポイント要求を待機しているときに発生します。|  
|CHKPT |サーバー起動時に発生し、チェックポイント スレッドに開始できることを伝えます。|  
|CLEAR_DB|データベースの開閉など、データベースの状態を変更する操作時に発生します。|  
|CLR_AUTO_EVENT|タスクが共通言語ランタイム (CLR) を実行中で、特定の自動イベントが開始されるのを待機しているときに発生します。 待機は長時間になることが一般的で、問題はありません。|  
|CLR_CRST|タスクが CLR を実行中で、重大なセクションが別のタスクによって現在使用されている場合、そのセクションを待機しているときに発生します。|  
|CLR_JOIN|タスクが CLR を実行中で、別のタスクの終了を待機しているときに発生します。 また、この待機状態は、タスク間の結合があるときに発生します。|  
|CLR_MANUAL_EVENT |タスクが CLR を実行中で、特定の手動イベントが開始されるのを待機しているときに発生します。|  
|CLR_MEMORY_SPY|データ構造のロック取得での待機中に発生します。このデータ構造は、CLR に由来するすべての仮想メモリ割り当てを記録するために使用されます。 データ構造はロックされますが、それは並行アクセスがある場合でもデータの整合性を保持するためです。|  
|CLR_MONITOR|タスクが CLR を実行中で、モニターのロックの取得を待機しているときに発生します。|  
|CLR_RWLOCK_READER|タスクが CLR を実行中で、リーダー ロックを待機しているときに発生します。|  
|CLR_RWLOCK_WRITER|タスクが CLR を実行中で、ライター ロックを待機しているときに発生します。|  
|CLR_SEMAPHORE|タスクが CLR を実行中で、セマフォを待機しているときに発生します。|  
|CLR_TASK_START|CLR タスクが完全に開始されるのを待機しているときに発生します。|  
|CLRHOST_STATE_ACCESS|CLR ホストのデータ構造への排他アクセスを取得するのを待機する場合に発生します。 この待機の種類が発生するのは、CLR ランタイムの設定時か設定解除時です。|  
|CMEMTHREAD|タスクがスレッドセーフ メモリ オブジェクトで待機しているときに発生します。 複数のタスクが同じメモリ オブジェクトからメモリを割り当てようとして競合が発生している場合、待機時間は長くなる可能性があります。|  
|CXPACKET|クエリ プロセッサ交換反復子を同期しようとするときに発生します。 この待機の種類で競合の発生が問題になる場合は、並列処理の次数を下げることを検討してください。|  
|CXROWSET_SYNC|範囲の並列スキャン中に発生します。|  
|DAC_INIT|専用管理者接続の初期化中に発生します。|  
|DBMIRROR_DBM_EVENT|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|DBMIRROR_DBM_MUTEX|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|DBMIRROR_EVENTS_QUEUE|データベース ミラーリングがイベントの処理を待機しているときに発生します。|  
|DBMIRROR_SEND |タスクが、ネットワーク層の通信バックログが消去されメッセージ送信できるようになるのを待機しているときに発生します。 通信層が過負荷になり、データベース ミラーリング データのスループットに影響が生じ始めていることを表します。|  
|DBMIRROR_WORKER_QUEUE|データベース ミラーリング ワーカー タスクが、次の作業の実行を待機していることを表します。|  
|DBMIRRORING_CMD |タスクが、ログ レコードのディスクへのフラッシュを待機しているときに発生します。 この待機状態は、長時間続くことが予想されます。|  
|DEADLOCK_ENUM_MUTEX|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] で同時に複数のデッドロック検索が実行されていないかどうかを、デッドロック モニターと `sys.dm_os_waiting_tasks` が確認しようとするときに発生します。|  
|DEADLOCK_TASK_SEARCH |このリソースでの待機時間が長い場合は、サーバーが `sys.dm_os_waiting_tasks` 上で複数のクエリを実行したことにより、デッドロック モニターでデッドロック検索を実行できなくなっていることを表します。 この待機の種類は、デッドロック モニターにのみ使用されます。 `sys.dm_os_waiting_tasks` の上部のクエリは、DEADLOCK_ENUM_MUTEX を使用します。|  
|DEBUG|[!INCLUDE[tsql](../../includes/tsql-md.md)] と CLR が内部同期のためデバッグしているときに発生します。|  
|DISABLE_VERSIONING|一番最初のアクティブなトランザクションのタイムスタンプが、状態が変化し始めたときのタイムスタンプより後かどうかを確認するために、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] がバージョン トランザクション マネージャーをポーリングしたときに発生します。 最初のトランザクションのタイムスタンプが状態変化のタイムスタンプより後の場合、ALTER DATABASE ステートメントの実行前に開始されたスナップショット トランザクションはすべて終了しています。 この待機状態は、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] が ALTER DATABASE ステートメントを使用してバージョン管理を無効にするときに使用されます。|  
|DISKIO_SUSPEND|外部バックアップがアクティブで、タスクがファイルへのアクセスを待機しているときに発生します。 これは、ユーザー プロセスの待機が発生するたびに報告されます。 1 つのユーザー プロセスの待機が 5 回を超えた場合は、外部バックアップの完了に時間がかかりすぎている可能性があります。|  
|DISPATCHER_QUEUE_SEMAPHORE|ディスパッチャー プールのスレッドが、まだ多くの作業の処理を待機しているときに発生します。 ディスパッチャーがアイドル状態の場合、この待機の種類の待機時間は長くなると予想されます。|  
|DLL_LOADING_MUTEX|XML パーサー DLL が読み込まれるのを待機しているときに 1 回発生します。|  
|DROPTEMP|一時オブジェクトの削除を試行して失敗した場合に、次の削除を試行するまでの間に発生します。 この待機時間は、削除の試行が失敗するたびに指数関数的に増えます。|  
|DTC|タスクが、状態遷移の管理に使用されるイベントで待機しているときに発生します。 この待機状態によって、[!INCLUDE[msCoName](../../includes/msconame-md.md)] 分散トランザクション コーディネーター (MS DTC) サービスが使用できなくなったという通知を [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] で受信した後で、MS DTC トランザクションの復旧が発生するタイミングが制御されます。<br /><br /> また、この待機状態によって、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] が MS DTC トランザクションのコミットを開始するときに待機中になっているタスクを把握することができます。さらに、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] が MS DTC のコミットの終了を待機しているときに待機中になっているタスクも把握することができます。|  
|DTC_ABORT_REQUEST |MS DTC ワーカー セッションが、MS DTC トランザクションの所有権取得を待機しているときに発生します。 MS DTC がトランザクションの所有権を取得した後、セッションはそのトランザクションをロールバックできます。 一般に、セッションが待機するのは、そのトランザクションが別のセッションで使用されている場合です。|  
|DTC_RESOLVE|複数のデータベースにまたがるトランザクションで、復旧タスクが、トランザクションの結果をクエリするため master データベースを待機しているときに発生します。|  
|DTC_STATE |内部の MS DTC グローバル状態オブジェクトに対する変更を保護するイベントで、タスクが待機しているときに発生します。 この待機状態は非常に短い時間保持されます。|  
|DTC_TMDOWN_REQUEST|MS DTC ワーカー セッションで、MS DTC サービスが使用できないという通知を [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] が受信したときに発生します。 ワーカーはまず MS DTC 復旧プロセスの開始を待機し、 次に、ワーカーが操作している分散トランザクションの結果取得を待機します。 この待機は、MS DTC サービスとの接続が再度確立されるまで継続される場合があります。|  
|DTC_WAITFOR_OUTCOME |MS DTC がアクティブになり、準備されたトランザクションの解決が可能になるのを、復旧タスクが待機しているときに発生します。|  
|DUMP_LOG_COORDINATOR |メイン タスクが、サブタスクによるデータ生成を待機しているときに発生します。 通常、この待機状態は発生しません。 待機が長時間になる場合は、予期しないブロックが発生している可能性があり、 サブタスクを調査する必要があります。|  
|DUMPTRIGGER|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|EC|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|EE_PMOLOCK|ステートメントの実行時、特定の種類のメモリ割り当てを同期するときに発生します。|  
|EE_SPECPROC_MAP_INIT|内部プロシージャ ハッシュ テーブルの作成における同期中に発生します。 この待機は、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンスの開始後、ハッシュ テーブルに最初にアクセスするときにのみ発生します。|  
|ENABLE_VERSIONING|データベースがスナップショット分離が許可されている状態に移行できることを宣言するために、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] が、このデータベースにおけるすべての更新トランザクションの終了を待機しているときに発生します。 この待機状態は、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] が ALTER DATABASE ステートメントを使用してスナップショット分離を有効にするときに使用されます。|  
|ERROR_REPORTING_MANAGER|複数のエラー ログの初期化における同期中に発生します。|  
|EXCHANGE|並列クエリの実行時、クエリ プロセッサ交換反復子での同期中に発生します。|  
|EXECSYNC |並列クエリの実行時、交換反復子に関係のない領域での、クエリ プロセッサによる同期中に発生します。 このような領域の例としては、ビットマップ、ラージ バイナリ オブジェクト (LOB)、スプール反復子などがあります。 LOB では、この待機状態が頻繁に使用されることがあります。|  
|EXECUTION_PIPE_EVENT_INTERNAL|バッチ実行のプロデューサー部とコンシューマー部との間で同期しているときに発生します。これらの部は接続コンテキストを通じて送信されます。|  
|FAILPOINT|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|FCB_REPLICA_READ |スナップショット (または DBCC によって作成された一時スナップショット) のスパース ファイルの読み取りを同期するときに発生します。|  
|FCB_REPLICA_WRITE |スナップショット (または DBCC によって作成された一時スナップショット) のスパース ファイルに対するページのプッシュまたはプルを同期するときに発生します。|  
|FS_FC_RWLOCK|FILESTREAM ガベージ コレクターが次のいずれかを実行するために待機しているときに発生します。<br /><br /> ガベージ コレクションを無効にします (バックアップと復元で使用)。<br /><br /> FILESTREAM ガベージ コレクターを 1 サイクル実行します。|  
|FS_GARBAGE_COLLECTOR_SHUTDOWN|FILESTREAM ガベージ コレクターがクリーンアップ タスクの完了を待機しているときに発生します。|  
|FS_HEADER_RWLOCK|FILESTREAM データ コンテナーの FILESTREAM ヘッダーへのアクセスの取得を待機しているときに発生します。待機するのは、FILESTREAM ヘッダー ファイル (Filestream.hdr) の内容を読み取るかまたは更新するためです。|  
|FS_LOGTRUNC_RWLOCK|次のいずれかを実行するために、FILESTREAM ログの切り捨てへのアクセスの取得を待機しているときに発生します。<br /><br /> FILESTREAM ログ (FSLOG) の切り捨てを一時的に無効にします (バックアップと復元で使用)。<br /><br /> FSLOG の切り捨てを 1 サイクル実行します。|  
|FSA_FORCE_OWN_XACT|FILESTREAM ファイル I/O 操作が、関連付けられているトランザクションにバインドされる必要があるのに、別のセッションがそのトランザクションを現在所有している場合に発生します。|  
|FSAGENT|FILESTREAM ファイル I/O 操作が、FILESTREAM エージェント リソースを待機しているときに発生します。このリソースは別のファイル I/O 操作が使用中です。|  
|FSTR_CONFIG_MUTEX|別の FILESTREAM 機能の再構成が完了するのを待機しているときに発生します。|  
|FSTR_CONFIG_RWLOCK|FILESTREAM 構成パラメーターへのアクセスのシリアル化を待機しているときに発生します。|  
|FT_METADATA_MUTEX|単に情報を示すためだけに記述されます。 サポートされていません。 将来の互換性は保証されません。|  
|FT_RESTART_CRAWL|一時的なエラーから復旧するために、正常と認識されている最後の時点からフルテキスト クロールを再開する必要があるときに発生します。 この待機が発生した場合、その設定を現在処理中のワーカー タスクは、現在のステップを完了するか終了することを許可されます。|  
|FULLTEXT GATHERER|フルテキスト操作の同期中に発生します。|  
|GUARDIAN|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|HTTP_ENUMERATION|起動時に発生し、HTTP を開始するため HTTP エンドポイントを列挙します。|  
|HTTP_START|接続が HTTP の初期化完了を待機しているときに発生します。|  
|IMPPROV_IOWAIT|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] が、一括読み込み I/O の終了を待機しているときに発生します。|  
|INTERNAL_TESTING|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|IO_AUDIT_MUTEX|トレース イベント バッファーの同期中に発生します。|  
|IO_COMPLETION|I/O 操作の完了を待機しているときに発生します。 この待機の種類は通常、データ ページ以外の I/O を表します。 データ ページ I/O の完了の待機は、PAGEIOLATCH_* の待機として表示されます。|  
|IO_QUEUE_LIMIT|[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] の非同期 IO のキューに多数の保留中の IO がある場合に発生します。 別の IO を発行しようとしているタスクは、この待機の種類でブロックされます。これにより、保留中の IOs がしきい値を下回っています。 しきい値は、データベースに割り当てられている DTU に比例します。|  
|IO_RETRY|I/O 操作 (ディスクの読み取り/書き込みなど) に失敗すると発生します。原因はリソース不足による再試行です。|  
|IOAFF_RANGE_QUEUE|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|KSOURCE_WAKEUP|サービス コントロール タスクによって、サービス コントロール マネージャーからの要求を待機しているときに使用されます。 待機は長時間になることが予想されますが、問題はありません。|  
|KTM_ENLISTMENT|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|KTM_RECOVERY_MANAGER|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|KTM_RECOVERY_RESOLUTION|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|LATCH_DT |DT (破棄) ラッチを待機しているときに発生します。 これには、バッファー ラッチまたはトランザクション マーク ラッチは含まれません。 LATCH_* 待機の一覧は、`sys.dm_os_latch_stats` で確認できます。 `sys.dm_os_latch_stats` では、LATCH_NL、LATCH_SH、LATCH_UP、LATCH_EX、および LATCH_DT の待機はグループ化されます。|  
|LATCH_EX |EX (排他) ラッチを待機しているときに発生します。 これには、バッファー ラッチまたはトランザクション マーク ラッチは含まれません。 LATCH_* 待機の一覧は、`sys.dm_os_latch_stats` で確認できます。 `sys.dm_os_latch_stats` では、LATCH_NL、LATCH_SH、LATCH_UP、LATCH_EX、および LATCH_DT の待機はグループ化されます。|  
|LATCH_KP |KP (保持) ラッチを待機しているときに発生します。 これには、バッファー ラッチまたはトランザクション マーク ラッチは含まれません。 LATCH_* 待機の一覧は、`sys.dm_os_latch_stats` で確認できます。 `sys.dm_os_latch_stats` では、LATCH_NL、LATCH_SH、LATCH_UP、LATCH_EX、および LATCH_DT の待機はグループ化されます。|  
|LATCH_NL |[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|LATCH_SH |SH (共有) ラッチを待機しているときに発生します。 これには、バッファー ラッチまたはトランザクション マーク ラッチは含まれません。 LATCH_* 待機の一覧は、`sys.dm_os_latch_stats` で確認できます。 `sys.dm_os_latch_stats` では、LATCH_NL、LATCH_SH、LATCH_UP、LATCH_EX、および LATCH_DT の待機はグループ化されます。|  
|LATCH_UP |UP (更新) ラッチを待機しているときに発生します。 これには、バッファー ラッチまたはトランザクション マーク ラッチは含まれません。 LATCH_* 待機の一覧は、`sys.dm_os_latch_stats` で確認できます。 `sys.dm_os_latch_stats` では、LATCH_NL、LATCH_SH、LATCH_UP、LATCH_EX、および LATCH_DT の待機はグループ化されます。|  
|LAZYWRITER_SLEEP |レイジー ライター タスクが一時中断されるときに発生します。 待機中のバックグラウンド タスクで費やされた時間を測定することができます。 ユーザーの機能停止を検索しているときには、この待機状態は考慮しないでください。|  
|LCK_M_BU |タスクが一括更新 (BU) ロックの取得を待機しているときに発生します。 ロックの互換性マトリックスについては、「 [sys.dm_tran_locks &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql.md)」を参照してください。|  
|LCK_M_IS|タスクがインテント共有 (IS) ロックの取得を待機しているときに発生します。 ロックの互換性マトリックスについては、「 [sys.dm_tran_locks &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql.md)」を参照してください。|  
|LCK_M_IU|タスクがインテント更新 (IU) ロックの取得を待機しているときに発生します。 ロックの互換性マトリックスについては、「 [sys.dm_tran_locks &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql.md)」を参照してください。|  
|LCK_M_IX |タスクがインテント排他 (IX) ロックの取得を待機しているときに発生します。 ロックの互換性マトリックスについては、「 [sys.dm_tran_locks &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql.md)」を参照してください。|  
|LCK_M_RIn_NL |タスクが、現在のキー値に対する NULL ロックの取得、および現在のキーから以前のキーまでを対象とした挿入範囲ロックの取得を待機しているときに発生します。 キーの NULL ロックは、すぐに解放されるロックです。 ロックの互換性マトリックスについては、「 [sys.dm_tran_locks &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql.md)」を参照してください。|  
|LCK_M_RIn_S |タスクが、現在のキー値に対する共有ロックの取得、および現在のキーから以前のキーまでを対象とした挿入範囲ロックの取得を待機しているときに発生します。 ロックの互換性マトリックスについては、「 [sys.dm_tran_locks &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql.md)」を参照してください。|  
|LCK_M_RIn_U |タスクが、現在のキー値に対する更新ロックの取得、および現在のキーから以前のキーまでを対象とした挿入範囲ロックの取得を待機しているときに発生します。 ロックの互換性マトリックスについては、「 [sys.dm_tran_locks &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql.md)」を参照してください。|  
|LCK_M_RIn_X |タスクが、現在のキー値に対する排他ロックの取得、および現在のキーから以前のキーまでを対象とした挿入範囲ロックの取得を待機しているときに発生します。 ロックの互換性マトリックスについては、「 [sys.dm_tran_locks &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql.md)」を参照してください。|  
|LCK_M_RS_S |タスクが、現在のキー値に対する共有ロックの取得、および現在のキーから以前のキーまでを対象とした共有範囲ロックの取得を待機しているときに発生します。 ロックの互換性マトリックスについては、「 [sys.dm_tran_locks &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql.md)」を参照してください。|  
|LCK_M_RS_U |タスクが、現在のキー値に対する更新ロックの取得、および現在のキーから以前のキーまでを対象とした更新範囲ロックの取得を待機しているときに発生します。 ロックの互換性マトリックスについては、「 [sys.dm_tran_locks &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql.md)」を参照してください。|  
|LCK_M_RX_S |タスクが、現在のキー値に対する共有ロックの取得、および現在のキーから以前のキーまでを対象とした排他範囲ロックの取得を待機しているときに発生します。 ロックの互換性マトリックスについては、「 [sys.dm_tran_locks &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql.md)」を参照してください。|  
|LCK_M_RX_U |タスクが、現在のキー値に対する更新ロックの取得、および現在のキーから以前のキーまでを対象とした排他範囲ロックの取得を待機しているときに発生します。 ロックの互換性マトリックスについては、「 [sys.dm_tran_locks &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql.md)」を参照してください。|  
|LCK_M_RX_X |タスクが、現在のキー値に対する排他ロックの取得、および現在のキーから以前のキーまでを対象とした排他範囲ロックの取得を待機しているときに発生します。 ロックの互換性マトリックスについては、「 [sys.dm_tran_locks &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql.md)」を参照してください。|  
|LCK_M_S |タスクが共有ロックの取得を待機しているときに発生します。 ロックの互換性マトリックスについては、「 [sys.dm_tran_locks &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql.md)」を参照してください。|  
|LCK_M_SCH_M |タスクがスキーマ変更ロックの取得を待機しているときに発生します。 ロックの互換性マトリックスについては、「 [sys.dm_tran_locks &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql.md)」を参照してください。|  
|LCK_M_SCH_S |タスクがスキーマ共有ロックの取得を待機しているときに発生します。 ロックの互換性マトリックスについては、「 [sys.dm_tran_locks &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql.md)」を参照してください。|  
|LCK_M_SIU |タスクがインテント更新付き共有ロックの取得を待機しているときに発生します。 ロックの互換性マトリックスについては、「 [sys.dm_tran_locks &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql.md)」を参照してください。|  
|LCK_M_SIX |タスクがインテント排他付き共有ロックの取得を待機しているときに発生します。 ロックの互換性マトリックスについては、「 [sys.dm_tran_locks &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql.md)」を参照してください。|  
|LCK_M_U |タスクが更新ロックの取得を待機しているときに発生します。 ロックの互換性マトリックスについては、「 [sys.dm_tran_locks &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql.md)」を参照してください。|  
|LCK_M_UIX |タスクがインテント排他付き更新ロックの取得を待機しているときに発生します。 ロックの互換性マトリックスについては、「 [sys.dm_tran_locks &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql.md)」を参照してください。|  
|LCK_M_X |タスクが排他ロックの取得を待機しているときに発生します。 ロックの互換性マトリックスについては、「 [sys.dm_tran_locks &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql.md)」を参照してください。|  
|LOG_RATE_GOVERNOR|DB がクォータのログへの書き込みを待機しているときに発生します。|  
|LOGBUFFER |タスクが、ログ レコードを格納するログ バッファーの領域を待機しているときに発生します。 常に高い値が示される場合は、ログ デバイスで解放される領域よりも、サーバーにより生成されるログ サイズが大きいことを表しています。|  
|LOGGENERATION|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|LOGMGR|データベースを閉じる間、ログのシャットダウン前に未処理のログ I/O が終了するのをタスクが待機しているときに発生します。|  
|LOGMGR_FLUSH |[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|LOGMGR_QUEUE|ログ ライター タスクが作業要求を待機しているときに発生します。|  
|LOGMGR_RESERVE_APPEND |新しいログ レコードを書き込むために、タスクが、ログの切り捨てによるログ領域の解放の確認を待機しているときに発生します。 この待機を短縮すると、影響を受けるデータベースのログ ファイル サイズが増えることに注意してください。|  
|LOWFAIL_MEMMGR_QUEUE|メモリが使用可能になるのを待機しているときに発生します。|  
|MSQL_DQ|タスクが分散クエリ操作の終了を待機しているときに発生します。 これは、複数のアクティブな結果セット (MARS) アプリケーションにデッドロックの可能性があるかどうかを検出するために使用されます。 分散クエリ呼び出しが終了すると、待機は終了します。|  
|MSQL_XACT_MGR_MUTEX|タスクが、セッション レベル トランザクション操作を実行するために、セッション トランザクション マネージャーの所有権の取得を待機しているときに発生します。|  
|MSQL_XACT_MUTEX|トランザクション使用の同期中に発生します。 要求でトランザクションを使用するには、まずミューテックスを取得する必要があります。|  
|MSQL_XP |タスクが、拡張ストアド プロシージャの終了を待機しているときに発生します。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、この待機状態を使用して、MARS アプリケーションにデッドロックの可能性があるかどうかを検出します。 拡張ストアド プロシージャの呼び出しが終了すると、待機は終了します。|  
|MSSEARCH|フルテキスト検索の呼び出し中に発生します。 フルテキスト操作が完了すると、待機は終了します。 この待機はフルテキスト操作の競合ではなく、操作時間を表します。|  
|NET_WAITFOR_PACKET|ネットワークの読み取り中に、接続がネットワーク パケットを待機しているときに発生します。|  
|OLEDB|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] が [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client OLE DB プロバイダーを呼び出すときに発生します。 この種類の待機は、同期では使用されません。 代わりに、OLE DB プロバイダー呼び出しの持続時間を示します。|  
|ONDEMAND_TASK_QUEUE|バックグラウンド タスクが、優先度の高いシステム タスクの要求を待機しているときに発生します。 待機時間が長い場合は処理優先度が高い要求がないことを示します。問題があるわけではありません。|  
|PAGEIOLATCH_DT |タスクが、I/O 要求内のバッファー ラッチで待機しているときに発生します。 ラッチ要求は破棄モードです。 待機時間が長い場合、ディスク サブシステムに問題がある可能性があります。|  
|PAGEIOLATCH_EX |タスクが、I/O 要求内のバッファー ラッチで待機しているときに発生します。 ラッチ要求は排他モードです。 待機時間が長い場合、ディスク サブシステムに問題がある可能性があります。|  
|PAGEIOLATCH_KP |タスクが、I/O 要求内のバッファー ラッチで待機しているときに発生します。 ラッチ要求は保持モードです。 待機時間が長い場合、ディスク サブシステムに問題がある可能性があります。|  
|PAGEIOLATCH_NL |[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|PAGEIOLATCH_SH |タスクが、I/O 要求内のバッファー ラッチで待機しているときに発生します。 ラッチ要求は共有モードです。 待機時間が長い場合、ディスク サブシステムに問題がある可能性があります。|  
|PAGEIOLATCH_UP |タスクが、I/O 要求内のバッファー ラッチで待機しているときに発生します。 ラッチ要求は更新モードです。 待機時間が長い場合、ディスク サブシステムに問題がある可能性があります。|  
|PAGELATCH_DT |タスクが、I/O 要求内にないバッファー ラッチで待機しているときに発生します。 ラッチ要求は破棄モードです。|  
|PAGELATCH_EX |タスクが、I/O 要求内にないバッファー ラッチで待機しているときに発生します。 ラッチ要求は排他モードです。|  
|PAGELATCH_KP |タスクが、I/O 要求内にないバッファー ラッチで待機しているときに発生します。 ラッチ要求は保持モードです。|  
|PAGELATCH_NL |[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|PAGELATCH_SH |タスクが、I/O 要求内にないバッファー ラッチで待機しているときに発生します。 ラッチ要求は共有モードです。|  
|PAGELATCH_UP |タスクが、I/O 要求内にないバッファー ラッチで待機しているときに発生します。 ラッチ要求は更新モードです。|  
|PARALLEL_BACKUP_QUEUE|RESTORE HEADERONLY、RESTORE FILELISTONLY、または RESTORE LABELONLY によって生成された出力をシリアル化しているときに発生します。|  
|PREEMPTIVE_ABR|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|PREEMPTIVE_AUDIT_ACCESS_EVENTLOG|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] オペレーティング システム (SQLOS) スケジューラが、監査イベントを Windows イベント ログに書き込むためにプリエンプティブ モードに切り替えたときに発生します。|  
|PREEMPTIVE_AUDIT_ACCESS_SECLOG|SQLOS スケジューラが、監査イベントを Windows セキュリティ ログに書き込むためにプリエンプティブ モードに切り替えたときに発生します。|  
|PREEMPTIVE_CLOSEBACKUPMEDIA|SQLOS スケジューラが、バックアップ メディアを閉じるためにプリエンプティブ モードに切り替えたときに発生します。|  
|PREEMPTIVE_CLOSEBACKUPTAPE|SQLOS スケジューラが、テープ バックアップ デバイスを閉じるためにプリエンプティブ モードに切り替えたときに発生します。|  
|PREEMPTIVE_CLOSEBACKUPVDIDEVICE|SQLOS スケジューラが、仮想バックアップ デバイスを閉じるためにプリエンプティブ モードに切り替えたときに発生します。|  
|PREEMPTIVE_CLUSAPI_CLUSTERRESOURCECONTROL|SQLOS スケジューラが、Windows フェールオーバー クラスター操作を実行するためにプリエンプティブ モードに切り替えたときに発生します。|  
|PREEMPTIVE_COM_COCREATEINSTANCE|SQLOS スケジューラが、COM オブジェクトを作成するためにプリエンプティブ モードに切り替えたときに発生します。|  
|PREEMPTIVE_HADR_LEASE_MECHANISM|Always On 可用性グループは、CSS 診断のためにリースマネージャーをスケジュールします。|  
|PREEMPTIVE_SOSTESTING|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|PREEMPTIVE_STRESSDRIVER|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|PREEMPTIVE_TESTING|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|PREEMPTIVE_XETESTING|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|PRINT_ROLLBACK_PROGRESS |ALTER DATABASE 終了句を使って遷移されたデータベースで、ユーザー プロセスが終了するのを待機する場合に使用されます。 詳細については、「[ALTER DATABASE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-database-transact-sql.md)」を参照してください。|  
|PWAIT_HADR_CHANGE_NOTIFIER_TERMINATION_SYNC|バックグラウンド タスクが、Windows Server フェールオーバー クラスタリングの通知を (ポーリング経由で) 受信するバックグラウンド タスクの終了を待機しているときに発生します。  内部使用のみです。|  
|PWAIT_HADR_CLUSTER_INTEGRATION|追加、置換、削除のいずれかの操作が、Always On 内部リスト (ネットワーク、ネットワークアドレス、または可用性グループリスナーの一覧など) での書き込みロックの取得を待機しています。  内部使用のみです。|  
|PWAIT_HADR_OFFLINE_COMPLETED|Always On drop availability group 操作は、Windows Server フェールオーバークラスタリングオブジェクトを破棄する前に、対象の可用性グループがオフラインになるのを待機しています。|  
|PWAIT_HADR_ONLINE_COMPLETED|可用性グループの作成またはフェールオーバー操作の Always On が、ターゲットの可用性グループがオンラインになるのを待機しています。|  
|PWAIT_HADR_POST_ONLINE_COMPLETED|Always On drop availability group 操作は、前のコマンドの一部としてスケジュールされたバックグラウンドタスクの終了を待機しています。 たとえば、可用性データベースをプライマリ ロールに遷移させるバックグラウンド タスクが存在する場合があります。 DROP AVAILABILITY GROUP DDL は、競合状態を回避するために、このバックグラウンド タスクの終了を待機する必要があります。|  
|PWAIT_HADR_WORKITEM_COMPLETED|非同期作業タスクの完了を待機するスレッドによる、内部的な待機です。 これは想定される待機であり、CSS で使用されます。|  
|PWAIT_MD_LOGIN_STATS|ログイン統計に関するメタデータの内部同期中に発生します。|  
|PWAIT_MD_RELATION_CACHE|テーブルまたはインデックスに関するメタデータの内部同期中に発生します。|  
|PWAIT_MD_SERVER_CACHE|リンク サーバーに関するメタデータの内部同期中に発生します。|  
|PWAIT_MD_UPGRADE_CONFIG|サーバー全体の構成に関するアップグレードの内部同期中に発生します。|  
|PWAIT_METADATA_LAZYCACHE_RWLOCk|テーブルのインデックスまたは統計の反復と共にメタデータ キャッシュの内部同期中に発生します。|  
|QPJOB_KILL|非同期自動統計更新が実行を開始したときに、強制終了の呼び出しにより操作が取り消されたことを示します。 終了スレッドは一時中断され、強制終了コマンドの受信開始を待機します。 1 秒未満であれば問題はありません。|  
|QPJOB_WAITFOR_ABORT|非同期自動統計更新の実行中に、強制終了の呼び出しにより操作が取り消されたことを示します。 更新は現在完了していますが、終了スレッド メッセージ調整が完了するまでは一時中断されます。 これは通常の状態ですが、発生することはほとんどありません。発生しても非常に短い時間です。 1 秒未満であれば問題はありません。|  
|QRY_MEM_GRANT_INFO_MUTEX |クエリ実行メモリ管理が、静的な許可情報リストへのアクセスを制御しようとするときに発生します。 この待機状態では、現在許可されており待機中のメモリ要求に関する情報が一覧表示されます。 またこの状態は、単純なアクセス制御状態です。 この状態では、長時間に及ぶ待機は避けてください。 このミューテックスが解放されない場合、新しいメモリを使用するすべてのクエリは応答を停止します。|  
|QUERY_ERRHDL_SERVICE_DONE|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|QUERY_EXECUTION_INDEX_SORT_EVENT_OPEN|オフラインでのインデックス作成が並列実行される場合、並べ替えを行っている複数のワーカー スレッドが並べ替えファイルへのアクセスを同期するときに発生する場合があります。|  
|QUERY_NOTIFICATION_MGR_MUTEX |クエリ通知マネージャー内のガベージ コレクション キューの同期中に発生します。|  
|QUERY_NOTIFICATION_SUBSCRIPTION_MUTEX |クエリ通知内のトランザクションの状態の同期中に発生します。|  
|QUERY_NOTIFICATION_TABLE_MGR_MUTEX |クエリ通知マネージャー内での内部同期中に発生します。|  
|QUERY_NOTIFICATION_UNITTEST_MUTEX |[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|QUERY_OPTIMIZER_PRINT_MUTEX|クエリ オプティマイザー診断の出力作成の同期中に発生します。 この待機は、[!INCLUDE[msCoName](../../includes/msconame-md.md)] 製品サポートの指示により、診断設定を有効にしている場合にのみ発生します。|  
|QUERY_TRACEOUT|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|QUERY_WAIT_ERRHDL_SERVICE|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|RECOVER_CHANGEDB|ウォーム スタンバイ データベース内で、データベースの状態の同期中に発生します。|  
|REPL_CACHE_ACCESS|レプリケーション アーティクル キャッシュでの同期中に発生します。 この待機中、レプリケーション ログ リーダーは停止し、パブリッシュされたテーブルに対するデータ定義言語 (DDL) ステートメントはブロックされます。|  
|REPL_SCHEMA_ACCESS|レプリケーション スキーマのバージョン情報の同期中に発生します。 この状態は、レプリケートされたオブジェクトで DDL ステートメントが実行されるとき、および、ログ リーダーが DDL の発生に基づいてバージョン管理されたスキーマを作成または使用するときに発生します。|  
|REPLICA_WRITES|タスクが、データベース スナップショットまたは DBCC レプリカへのページ書き込みの完了を待機しているときに発生します。|  
|REQUEST_DISPENSER_PAUSE|タスクが、未処理の I/O がすべて完了しスナップショット バックアップ用にファイルの I/O が固定されるのを待機しているときに発生します。|  
|REQUEST_FOR_DEADLOCK_SEARCH|デッドロック モニターが、次のデッドロック検索の開始を待機しているときに発生します。 この待機は、デッドロックが検出されてから次に検出されるまでの間に発生することが予想されます。このリソースにおける合計待機時間が長くても問題はありません。|  
|RESMGR_THROTTLED|新しい要求が着信し、GROUP_MAX_REQUESTS 設定に基づいて絞り込まれたときに発生します。|  
|RESOURCE_QUEUE|さまざまな内部リソース キューの同期中に発生します。|  
|RESOURCE_SEMAPHORE|他の同時実行クエリがあるため、クエリ メモリの要求がすぐに許可されない場合に発生します。 待機および待機時間が高い値を示している場合は、同時実行クエリの数が多すぎるか、またはメモリ要求の数が多すぎる可能性があります。|  
|RESOURCE_SEMAPHORE_MUTEX|クエリが、スレッドを予約するための要求を待機しているときに発生します。 この待機は、クエリのコンパイルとメモリの要求許可を同期しているときにも発生します。|  
|RESOURCE_SEMAPHORE_QUERY_COMPILE|コンパイルされる同時実行クエリの数が、スロットルの制限値に達したときに発生します。 待機および待機時間が高い値を示している場合は、コンパイル、再コンパイル、またはキャッシュできないプランの数が多すぎる可能性があります。|  
|RESOURCE_SEMAPHORE_SMALL_QUERY|他の同時実行クエリがあるため、サイズの小さいクエリからのメモリ要求がすぐに許可されない場合に発生します。 待機時間は数秒以内である必要があります。要求したメモリが数秒以内に許可されないと、サーバーによって要求がメイン クエリのメモリ プールに転送されます。 待機が高い値を示している場合は、待機クエリによって主要なメモリ プールがブロックされているときに、サイズの小さい同時実行クエリの数が多すぎる可能性があります。|  
|SE_REPL_CATCHUP_THROTTLE|セカンダリ データベースの 1 つについて処理が進行するまでトランザクションが待機しているときに発生します。|  
|SE_REPL_COMMIT_ACK|トランザクションがセカンダリレプリカからのクォーラムコミット確認を待機しているときに発生します。|  
|SE_REPL_COMMIT_TURN|トランザクションが、クォーラムコミットの確認を受け取った後にコミットを待機しているときに発生します。|  
|SE_REPL_ROLLBACK_ACK|トランザクションがセカンダリレプリカからのクォーラムロールバック確認を待機しているときに発生します。|  
|SE_REPL_SLOW_SECONDARY_THROTTLE|スレッドがデータベースのセカンダリ レプリカの 1 つを待機しているときに発生します。|  
|SEC_DROP_TEMP_KEY|一時セキュリティ キーを削除しようとして失敗した後、再試行するまでの間に発生します。|  
|SECURITY_MUTEX|ミューテックスを待機しているときに発生します。このミューテックスは、拡張キー管理 (EKM) 暗号化サービス プロバイダーのグローバル リストへのアクセス、および EKM セッションのセッション スコープ リストへのアクセスを制御します。|  
|SEQUENTIAL_GUID|新しいシーケンシャル GUID を取得中に発生します。|  
|SERVER_IDLE_CHECK|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンスのアイドル状態を同期している間に、リソース モニターが [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンスをアイドルとして宣言しようとするとき、または起動しようとするときに発生します。|  
|SHUTDOWN|シャットダウン ステートメントが、アクティブな接続の終了を待機しているときに発生します。|  
|SLEEP_BPOOL_FLUSH|ディスク サブシステムが飽和状態にならないよう、チェックポイントで新しい I/O の実行をスロットル中に発生します。|  
|SLEEP_DBSTARTUP|すべてのデータベースが復旧するのを待機している間、データベースの起動中に発生します。|  
|SLEEP_DCOMSTARTUP|DCOM の初期化が完了するのを待機している間、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンスの起動中に、多くても 1 回だけ発生します。|  
|SLEEP_MSDBSTARTUP|SQL トレースが msdb データベースの起動完了を待機しているときに発生します。|  
|SLEEP_SYSTEMTASK|バックグラウンドタスクの開始中に発生し、tempdb が起動を完了するのを待機しています。|  
|SLEEP_TASK|ジェネリック イベントの発生を待機している間、タスクがスリープ状態のときに発生します。|  
|SLEEP_TEMPDBSTARTUP|タスクが tempdb の起動完了を待機しているときに発生します。|  
|SNI_CRITICAL_SECTION|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ネットワーク コンポーネント内での内部同期中に発生します。|  
|SNI_HTTP_WAITFOR_0_DISCON|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のシャットダウン中、未完了の HTTP 接続が終了するのを待機している間に発生します。|  
|SNI_LISTENER_ACCESS|NUMA (non-uniform memory access) ノードが状態の変化を更新するのを待機している間に発生します。 状態の変化へのアクセスはシリアル化されます。|  
|SNI_TASK_COMPLETION|NUMA ノード状態の変化中にすべてのタスクが終了するのを待機しているときに発生します。|  
|SOAP_READ|HTTP ネットワークの読み取り完了を待機しているときに発生します。|  
|SOAP_WRITE|HTTP ネットワークの書き込み完了を待機しているときに発生します。|  
|SOS_CALLBACK_REMOVAL|コールバックを削除するために、コールバックの一覧で同期を実行しているときに発生します。 サーバーの初期化が完了した後、通常この待機カウンターが変更されることはありません。|  
|SOS_DISPATCHER_MUTEX|ディスパッチャー プールの内部初期化中に発生します。 これには、プールの調整中も含まれます。|  
|SOS_LOCALALLOCATORLIST|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] メモリ マネージャー内での内部同期中に発生します。|  
|SOS_MEMORY_USAGE_ADJUSTMENT|プール間でメモリ使用量が調整されている場合に発生します。|  
|SOS_OBJECT_STORE_DESTROY_MUTEX|メモリ プールからオブジェクトを破棄するときに、メモリ プール内での内部同期中に発生します。|  
|SOS_PROCESS_AFFINITY_MUTEX|関係設定を処理するためのアクセスの同期中に発生します。|  
|SOS_RESERVEDMEMBLOCKLIST|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] メモリ マネージャー内での内部同期中に発生します。|  
|SOS_SCHEDULER_YIELD|タスクが、他のタスクの実行にスケジューラを自主的に解放したときに発生します。 この待機中、タスクはクォンタムの更新を待機しています。|  
|SOS_SMALL_PAGE_ALLOC|メモリの割り当てと開放中に発生します。このメモリはいくつかのメモリ オブジェクトによって管理されます。|  
|SOS_STACKSTORE_INIT_MUTEX|内部ストアの初期化の同期中に発生します。|  
|SOS_SYNC_TASK_ENQUEUE_EVENT|タスクが同期して開始したときに発生します。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のほとんどのタスクは非同期で開始し、タスクの要求が作業キューに挿入されるとすぐ制御が最初に戻ります。|  
|SOS_VIRTUALMEMORY_LOW|メモリ割り当てが、リソース マネージャーによる仮想メモリの解放を待機しているときに発生します。|  
|SOSHOST_EVENT|CLR などのホストされるコンポーネントが、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] イベント同期オブジェクト上で待機しているときに発生します。|  
|SOSHOST_INTERNAL|CLR などのホストされるコンポーネントで使用される、メモリ マネージャーのコールバックの同期中に発生します。|  
|SOSHOST_MUTEX|CLR などのホストされるコンポーネントが、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ミューテックス同期オブジェクト上で待機しているときに発生します。|  
|SOSHOST_RWLOCK|CLR などのホストされるコンポーネントが、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] リーダー/ライター同期オブジェクト上で待機しているときに発生します。|  
|SOSHOST_SEMAPHORE|CLR などのホストされるコンポーネントが、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セマフォ同期オブジェクト上で待機しているときに発生します。|  
|SOSHOST_SLEEP|ジェネリック イベントの発生を待機している間、ホストされるタスクがスリープ状態のときに発生します。 ホストされるタスクは、CLR などのホストされるコンポーネントで使用されます。|  
|SOSHOST_TRACELOCK|ストリームをトレースするためのアクセスの同期中に発生します。|  
|SOSHOST_WAITFORDONE|CLR などのホストされるコンポーネントが、タスクの完了を待機しているときに発生します。|  
|SQLCLR_APPDOMAIN|CLR が、アプリケーション ドメインの起動完了を待機しているときに発生します。|  
|SQLCLR_ASSEMBLY|アプリケーション ドメインに読み込まれたアセンブリ一覧へのアクセスを待機しているときに発生します。|  
|SQLCLR_DEADLOCK_DETECTION|CLR がデッドロック検出の完了を待機しているときに発生します。|  
|SQLCLR_QUANTUM_PUNISHMENT|クォンタムの実行時間を超えたことが原因で、CLR タスクがスロットルされたときに発生します。 このスロットルは、こうしたリソース消費の多いタスクによる他のタスクへの影響を軽減するために行われます。|  
|SQLSORT_NORMMUTEX|内部同期中、内部の並べ替え構造が初期化される間に発生します。|  
|SQLSORT_SORTMUTEX|内部同期中、内部の並べ替え構造が初期化される間に発生します。|  
|SQLTRACE_BUFFER_FLUSH|タスクが、バックグラウンド タスクによってトレース バッファーが 4 秒ごとにディスクにフラッシュされるのを待機しているときに発生します。|  
|SQLTRACE_LOCK|ファイルのトレース中、トレース バッファーの同期中に発生します。|  
|SQLTRACE_SHUTDOWN|トレースのシャットダウンが、未処理のトレース イベントが完了するのを待機しているときに発生します。|  
|SQLTRACE_WAIT_ENTRIES|SQL トレース イベント キューが、パケットの到着を待機しているときに発生します。|  
|SRVPROC_SHUTDOWN|シャットダウン プロセスが、正常にシャットダウンするために内部リソースの解放を待機しているときに発生します。|  
|TEMPOBJ|一時オブジェクトの削除が同期されるときに発生します。 この待機が発生するのはまれで、タスクが temp テーブルに対して削除操作を行うための排他アクセスを要求した場合にのみ発生します。|  
|THREADPOOL|タスクがワーカーの実行を待機しているときに発生します。 ワーカー数の最大設定値が低すぎるか、バッチ実行時間が長すぎるため、他のバッチ用にワーカー数が削減されている可能性があります。|  
|TIMEPRIV_TIMEPERIOD|拡張イベント タイマーの内部初期化中に発生します。|  
|TRACEWRITE|SQL トレースの行セット トレース プロバイダーが、空きバッファーまたは処理するイベントを含むバッファーのいずれかを待機しているときに発生します。|  
|TRAN_MARKLATCH_DT|トランザクション マーク ラッチで破棄モードのラッチを待機しているときに発生します。 トランザクション マーク ラッチは、マークされたトランザクションでのコミットの同期に使用されます。|  
|TRAN_MARKLATCH_EX|マークされたトランザクションで排他モードのラッチを待機しているときに発生します。 トランザクション マーク ラッチは、マークされたトランザクションでのコミットの同期に使用されます。|  
|TRAN_MARKLATCH_KP|マークされたトランザクションで保持モードのラッチを待機しているときに発生します。 トランザクション マーク ラッチは、マークされたトランザクションでのコミットの同期に使用されます。|  
|TRAN_MARKLATCH_NL|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|TRAN_MARKLATCH_SH|マークされたトランザクションで共有モードのラッチを待機しているときに発生します。 トランザクション マーク ラッチは、マークされたトランザクションでのコミットの同期に使用されます。|  
|TRAN_MARKLATCH_UP|マークされたトランザクションで更新モードのラッチを待機しているときに発生します。 トランザクション マーク ラッチは、マークされたトランザクションでのコミットの同期に使用されます。|  
|TRANSACTION_MUTEX|複数のバッチによるトランザクションへのアクセスの同期中に発生します。|  
|UTIL_PAGE_ALLOC|トランザクション ログのスキャンが、メモリに負荷がかかっている間に、使用できるメモリを待機しているときに発生します。|  
|VIA_ACCEPT|起動中に仮想インターフェイス アダプター (VIA) プロバイダー接続が完了すると発生します。|  
|VIEW_DEFINITION_MUTEX|キャッシュされたビュー定義へのアクセスの同期中に発生します。|  
|WAIT_FOR_RESULTS|クエリ通知が行われるのを待機しているときに発生します。|  
|WAITFOR|WAITFOR [!INCLUDE[tsql](../../includes/tsql-md.md)] ステートメントの結果として発生します。 この待機時間は、ステートメントに渡すパラメーターによって決まります。 この待機はユーザーによって開始されるものです。|  
|WAITFOR_TASKSHUTDOWN|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|WAITSTAT_MUTEX|`sys.dm_os_wait_stats` の設定に使用する統計コレクションへのアクセスの同期中に発生します。|  
|WCC|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|WORKTBL_DROP|作業テーブルの削除が失敗してから、再試行されるまで一時停止しているときに発生します。|  
|WRITE_COMPLETION|書き込み操作が進行中のときに発生します。|  
|WRITELOG|ログ フラッシュの完了を待機しているときに発生します。 ログ フラッシュの原因となる主な操作としては、チェックポイントとトランザクションのコミットがあります。|  
|XACT_OWN_TRANSACTION|トランザクションの所有権取得を待機しているときに発生します。|  
|XACT_RECLAIM_SESSION|セッションの現在の所有者がその所有権を解放するのを待機しているときに発生します。|  
|XACTLOCKINFO|トランザクションのロック一覧へのアクセスの同期中に発生します。 このロック一覧には、トランザクション自体だけでなく、ページ分割時にデッドロック検出やロック移行などの操作からもアクセスが行われます。|  
|XACTWORKSPACE_MUTEX|トランザクションからの参加解除や、トランザクションの参加メンバー間におけるデータベース ロック数の同期中に発生します。|  
|XE_BUFFERMGR_ALLPROCESSED_EVENT|拡張イベント セッション バッファーがターゲットにフラッシュされたときに発生します。 この待機はバックグラウンド スレッドで発生します。|  
|XE_BUFFERMGR_FREEBUF_EVENT|次のいずれかの条件が該当した場合に発生します。<br /><br /> 拡張イベント セッションが、イベントの削除を許可しないように構成されており、なおかつ、現在セッション内のすべてのバッファーがいっぱいになっている。 この場合、拡張イベント セッションのバッファーを大きくするか、パーティション分割する必要があります。<br /><br /> 監査で遅延が生じている。 監査の書き込み先ドライブでディスクのボトルネックが生じている可能性があります。|  
|XE_DISPATCHER_CONFIG_SESSION_LIST|非同期ターゲットを使用している拡張イベント セッションが開始されたか、停止された場合に発生します。 このタイプの待機は、次のいずれかを示します。<br /><br /> 拡張イベント セッションがバックグラウンドのスレッド プールへの登録を行っている。<br /><br /> バックグラウンド スレッド プールが、現在の負荷に応じて必要なスレッド数を計算している。|  
|XE_DISPATCHER_JOIN|拡張イベント セッションに使用されているバックグラウンド スレッドの終了時に発生します。|  
|XE_DISPATCHER_WAIT|拡張イベント セッションに使用されているバックグラウンド スレッドが、イベント バッファーの処理を待っているときに発生します。|  
|XE_MODULEMGR_SYNC|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|XE_OLS_LOCK|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|XE_PACKAGE_LOCK_BACKOFF|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|FT_COMPROWSET_RWLOCK|フル テキストがフラグメントのメタデータの操作を待機しています。 単に情報を示すためだけに記述されます。 サポートされていません。 将来の互換性は保証されません。|  
|FT_IFTS_RWLOCK|フルテキストが内部同期を待機しています。 単に情報を示すためだけに記述されます。 サポートされていません。 将来の互換性は保証されません。|  
|FT_IFTS_SCHEDULER_IDLE_WAIT|フルテキストのスケジューラが待機の種類をスリープしています。 スケジューラはアイドル状態です。|  
|FT_IFTSHC_MUTEX|フルテキストが fdhost 制御操作を待機しています。 単に情報を示すためだけに記述されます。 サポートされていません。 将来の互換性は保証されません。|  
|FT_IFTSISM_MUTEX|フル テキストが通信操作を待機しています。 単に情報を示すためだけに記述されます。 サポートされていません。 将来の互換性は保証されません。|  
|FT_MASTER_MERGE|フル テキストがマスター マージ操作を待機しています。 単に情報を示すためだけに記述されます。 サポートされていません。 将来の互換性は保証されません。|  
  
  
## <a name="see-also"></a>関連項目

 [sys.dm_os_sys_info &#40;Transact-sql&#41;](sys-dm-os-sys-info-transact-sql.md)    
 [sys.dm_tran_locks &#40;Transact-SQL&#41;](sys-dm-tran-locks-transact-sql.md)    
 [sys.dm_os_waiting_tasks &#40;Transact-sql&#41;](sys-dm-os-waiting-tasks-transact-sql.md)    
