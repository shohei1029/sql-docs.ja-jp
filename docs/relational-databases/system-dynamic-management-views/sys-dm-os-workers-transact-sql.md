---
description: sys.dm_os_workers (Transact-sql)
title: sys.dm_os_workers (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/13/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- dm_os_workers_TSQL
- sys.dm_os_workers_TSQL
- dm_os_workers
- sys.dm_os_workers
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_os_workers dynamic management view
ms.assetid: 4d5d1e52-a574-4bdd-87ae-b932527235e8
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 54b518d95329be0fef64b212fb95692c199a2ad0
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104749752"
---
# <a name="sysdm_os_workers-transact-sql"></a>sys.dm_os_workers (Transact-sql)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  システム内のすべてのワーカーに対して1行の値を返します。 ワーカーの詳細については、「 [スレッドおよびタスクアーキテクチャガイド](../../relational-databases/thread-and-task-architecture-guide.md)」を参照してください。 
  
> [!NOTE]  
>  またはからこれを呼び出すに [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] は [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] 、 **sys.dm_pdw_nodes_os_workers** という名前を使用します。  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|worker_address|**varbinary (8)**|ワーカーのメモリアドレス。|  
|status|**int**|内部使用のみです。|  
|is_preemptive|**bit**|1 = ワーカーは、プリエンプティブなスケジュール設定で実行中です。 外部コードを実行しているワーカーは、プリエンプティブスケジューリングの下で実行されます。|  
|is_fiber|**bit**|1 = ワーカーは、簡易プーリングを使用して実行されています。 詳細については、「 [sp_configure &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-configure-transact-sql.md)」を参照してください。|  
|is_sick|**bit**|1 = ワーカーは、スピン ロックを取得しようとして停止しています。 このビットが設定されている場合は、頻繁にアクセスされるオブジェクトの競合に問題がある可能性があります。|  
|is_in_cc_exception|**bit**|1 = ワーカーは、現在、非例外を処理してい [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。|  
|is_fatal_exception|**bit**|このワーカーが致命的な例外を受信したかどうかを指定します。|  
|is_inside_catch|**bit**|1 = ワーカーは現在例外を処理しています。|  
|is_in_polling_io_completion_routine|**bit**|1 = ワーカーは、保留中の i/o に対して i/o 完了ルーチンを現在実行しています。 詳細については、「 [sys.dm_io_pending_io_requests &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-io-pending-io-requests-transact-sql.md)」を参照してください。|  
|context_switch_count|**int**|このワーカーによって実行されるスケジューラコンテキストスイッチの数。|  
|pending_io_count|**int**|ワーカーによって実行された物理 I/O の数。|  
|pending_io_byte_count|**bigint**|このワーカーのすべての保留中の物理 i/o の合計バイト数。|  
|pending_io_byte_average|**int**|ワーカーの物理 I/O の平均バイト数。|  
|wait_started_ms_ticks|**bigint**|このワーカーが中断状態になった時点 ( [ms_ticks](../../relational-databases/system-dynamic-management-views/sys-dm-os-sys-info-transact-sql.md))。 [Sys.dm_os_sys_info](../../relational-databases/system-dynamic-management-views/sys-dm-os-sys-info-transact-sql.md)の ms_ticks からこの値を減算すると、ワーカーが待機していたミリ秒数が返されます。|  
|wait_resumed_ms_ticks|**bigint**|このワーカーが実行可能状態に入った時点 ( [ms_ticks](../../relational-databases/system-dynamic-management-views/sys-dm-os-sys-info-transact-sql.md))。 [Sys.dm_os_sys_info](../../relational-databases/system-dynamic-management-views/sys-dm-os-sys-info-transact-sql.md)の ms_ticks からこの値を差し引くと、ワーカーが実行可能キューにあるミリ秒数が返されます。|  
|task_bound_ms_ticks|**bigint**|タスクがこのワーカーにバインドされているときの、 [ms_ticks](../../relational-databases/system-dynamic-management-views/sys-dm-os-sys-info-transact-sql.md)内の特定の時点。|  
|worker_created_ms_ticks|**bigint**|ワーカーが作成されたときの、 [ms_ticks](../../relational-databases/system-dynamic-management-views/sys-dm-os-sys-info-transact-sql.md)内の特定の時点。|  
|exception_num|**int**|ワーカーで前回発生した例外のエラー番号。|  
|exception_severity|**int**|ワーカーで前回発生した例外の重大度。|  
|exception_address|**varbinary (8)**|例外をスローしたコード アドレス。|  
|affinity|**bigint**|ワーカーのスレッド関係。 [Sys.dm_os_threads &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-os-threads-transact-sql.md)のスレッドの関係と一致します。|  
|state|**nvarchar(60)**|ワーカーの状態。 値は、次のいずれかです。<br /><br /> INIT = Worker は現在初期化中です。<br /><br /> RUNNING = ワーカーは、現在非プリエンプティブまたはプリエンプティブのいずれかで実行中です。<br /><br /> RUNNABLE = ワーカーは、スケジューラ上で実行できる状態です。<br /><br /> SUSPENDED = ワーカーは現在中断されています。イベントによるシグナル送信を待機中です。|  
|start_quantum|**bigint**|ワーカーの現在の実行が開始された時間 (ミリ秒単位)。|  
|end_quantum|**bigint**|このワーカーの現在の実行が終了するまでの時間 (ミリ秒単位)。|  
|last_wait_type|**nvarchar(60)**|最後の待機の種類。 待機の種類の一覧については、「 [sys.dm_os_wait_stats &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql.md)」を参照してください。|  
|return_code|**int**|前回の待機からの戻り値。 値は、次のいずれかです。<br /><br /> 0 =SUCCESS<br /><br /> 3 = デッドロック<br /><br /> 4 = PREMATURE_WAKEUP<br /><br /> 258 = TIMEOUT|  
|quantum_used|**bigint**|内部使用のみです。|  
|max_quantum|**bigint**|内部使用のみです。|  
|boost_count|**int**|内部使用のみです。|  
|tasks_processed_count|**int**|このワーカーが処理したタスクの数。|  
|fiber_address|**varbinary (8)**|ワーカーが関連付けられているファイバーのメモリ アドレス。<br /><br /> NULL = [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] は、簡易プーリング用に構成されていません。|  
|task_address|**varbinary (8)**|現在のタスクのメモリアドレス。 詳細については、「 [sys.dm_os_tasks &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-os-tasks-transact-sql.md)」を参照してください。|  
|memory_object_address|**varbinary (8)**|ワーカーのメモリ オブジェクトのメモリ アドレス。 詳細については、「 [sys.dm_os_memory_objects &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-os-memory-objects-transact-sql.md)」を参照してください。|  
|thread_address|**varbinary (8)**|このワーカーに関連付けられているスレッドのメモリアドレス。 詳細については、「 [sys.dm_os_threads &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-os-threads-transact-sql.md)」を参照してください。|  
|signal_worker_address|**varbinary (8)**|このオブジェクトを最後に通知したワーカーのメモリアドレス。 詳細については、「 [sys.dm_os_workers](../../relational-databases/system-dynamic-management-views/sys-dm-os-workers-transact-sql.md)」を参照してください。|  
|scheduler_address|**varbinary (8)**|スケジューラのメモリアドレス。 詳細については、「 [sys.dm_os_schedulers &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-os-schedulers-transact-sql.md)」を参照してください。|  
|processor_group|**smallint**|このスレッドに割り当てられているプロセッサ グループ ID が格納されます。|  
|pdw_node_id|**int**|**適用対象**: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 、 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]<br /><br /> このディストリビューションが配置されているノードの識別子。|  
  
## <a name="remarks"></a>解説  
 ワーカーの状態が RUNNING で、非プリエンプティブに実行されている場合、そのワーカーのアドレスは、sys.dm_os_schedulers 内の active_worker_address と一致します。  
  
 イベントで待機中のワーカーがシグナルを受け取ると、そのワーカーは実行可能キューの先頭に置かれます。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] でこれが 1,000 回続けて発生すると、ワーカーはキューの末尾に置かれます。 ワーカーがキューの末尾に移動すると、パフォーマンスに影響が生じる場合があります。  
  
## <a name="permissions"></a>権限
で [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] は、 `VIEW SERVER STATE` 権限が必要です。   
[!INCLUDE[ssSDS_md](../../includes/sssds-md.md)]Premium レベルでは、データベースの権限が必要です `VIEW DATABASE STATE` 。 [!INCLUDE[ssSDS_md](../../includes/sssds-md.md)]Standard レベルと Basic レベルでは、 `Server Admin` ロールのメンバーシップ、またはアカウントが必要です `Azure Active Directory admin` 。   

## <a name="examples"></a>例  
 次のクエリを使用すると、SUSPENDED または RUNNABLE 状態でのワーカーの実行時間を調べることができます。  
  
```sql
SELECT   
    t1.session_id,  
    CONVERT(varchar(10), t1.status) AS status,  
    CONVERT(varchar(15), t1.command) AS command,  
    CONVERT(varchar(10), t2.state) AS worker_state,  
    w_suspended =   
      CASE t2.wait_started_ms_ticks  
        WHEN 0 THEN 0  
        ELSE   
          t3.ms_ticks - t2.wait_started_ms_ticks  
      END,  
    w_runnable =   
      CASE t2.wait_resumed_ms_ticks  
        WHEN 0 THEN 0  
        ELSE   
          t3.ms_ticks - t2.wait_resumed_ms_ticks  
      END  
  FROM sys.dm_exec_requests AS t1  
  INNER JOIN sys.dm_os_workers AS t2  
    ON t2.task_address = t1.task_address  
  CROSS JOIN sys.dm_os_sys_info AS t3  
  WHERE t1.scheduler_id IS NOT NULL;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  

```
 session_id status     command         worker_state w_suspended w_runnable  
 ---------- ---------- --------------- ------------ ----------- --------------------  
 4          background LAZY WRITER     SUSPENDED    688         688  
 6          background LOCK MONITOR    SUSPENDED    4657        4657
 19         background BRKR TASK       SUSPENDED    603820344   603820344  
 14         background BRKR EVENT HNDL SUSPENDED    63583641    63583641  
 51         running    SELECT          RUNNING      0           0  
 2          background RESOURCE MONITO RUNNING      0           603825954  
 3          background LAZY WRITER     SUSPENDED    422         422  
 7          background SIGNAL HANDLER  SUSPENDED    603820485   603820485  
 13         background TASK MANAGER    SUSPENDED    603824704   603824704  
 18         background BRKR TASK       SUSPENDED    603820407   603820407  
 9          background TRACE QUEUE TAS SUSPENDED    454         454  
 52         suspended  SELECT          SUSPENDED    35094       35094  
 1          background RESOURCE MONITO RUNNING      0           603825954  
```

 出力で、 `w_runnable` と `w_suspended` が等しい場合、これはワーカーが中断状態にある時間を表します。 等しくない場合、`w_runnable` は、RUNNABLE 状態でワーカーが費やした時間を示します。 出力では、セッション `52` は `SUSPENDED` ミリ秒を対象としてい `35,094` ます。  
  
## <a name="see-also"></a>参照  
[SQL Server オペレーティングシステム関連の動的管理ビュー &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sql-server-operating-system-related-dynamic-management-views-transact-sql.md)       
[クエリ処理アーキテクチャ ガイド](../../relational-databases/query-processing-architecture-guide.md#DOP)       
[スレッドおよびタスクのアーキテクチャ ガイド](../../relational-databases/thread-and-task-architecture-guide.md)    
