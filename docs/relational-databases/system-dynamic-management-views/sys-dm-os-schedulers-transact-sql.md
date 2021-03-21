---
description: sys.dm_os_schedulers (Transact-sql)
title: sys.dm_os_schedulers (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/13/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- dm_os_schedulers
- sys.dm_os_schedulers_TSQL
- sys.dm_os_schedulers
- dm_os_schedulers_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_os_schedulers dynamic management view
ms.assetid: 3a09d81b-55d5-416f-9cda-1a3a5492abe0
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: ab809b35b282d0a5c5ed0c0f2095ee6593b512bc
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104754572"
---
# <a name="sysdm_os_schedulers-transact-sql"></a>sys.dm_os_schedulers (Transact-sql)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のスケジューラごとに 1 行のデータを返します。各スケジューラは個別のプロセッサにマップされています。 このビューは、スケジューラの状況の監視やランナウェイ タスクの特定に使用できます。 スケジューラの詳細については、「 [スレッドおよびタスクアーキテクチャガイド](../../relational-databases/thread-and-task-architecture-guide.md)」を参照してください。  
  
> [!NOTE]  
>  またはからこれを呼び出すに [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] は [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] 、 **sys.dm_pdw_nodes_os_schedulers** という名前を使用します。  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|scheduler_address|**varbinary (8)**|スケジューラのメモリアドレス。 NULL 値は許可されません。|  
|parent_node_id|**int**|スケジューラが属しているノードの ID。親ノードとも呼ばれます。 これは非均質メモリ アクセス (NUMA) ノードを表します。 NULL 値は許可されません。|  
|scheduler_id|**int**|スケジューラの ID。 通常のクエリを実行するために使用されるすべてのスケジューラには、1048576未満の ID 番号が付けられています。 Id が1048576以上のスケジューラは [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 、専用管理者接続スケジューラなど、によって内部的に使用されます。 NULL 値は許可されません。|  
|cpu_id|**smallint**|スケジューラに割り当てられた CPU ID。<br /><br /> NULL 値は許可されません。<br /><br /> **注:** 255 は、の場合とは関係がないことを示していません [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] 。 追加のアフィニティ情報については、「 [sys.dm_os_threads &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-os-threads-transact-sql.md) 」を参照してください。|  
|status|**nvarchar(60)**|スケジューラの状態。 値は、次のいずれかです。<br /><br /> -非表示 (オンライン)<br />-非表示 (オフライン)<br />-オンラインで表示<br />-オフラインで表示<br />-オンラインで表示 (DAC)<br />-HOT_ADDED<br /><br /> NULL 値は許可されません。<br /><br /> 非表示のスケジューラは、の内部の要求を処理するために使用され [!INCLUDE[ssDE](../../includes/ssde-md.md)] ます。 表示されるスケジューラは、ユーザー要求を処理するために使用されます。<br /><br /> オフラインスケジューラは、関係マスクでオフラインになっているプロセッサにマップされるため、要求の処理には使用されません。 ONLINE スケジューラは、関係マスクでオンラインになっているプロセッサにマップされ、スレッドの処理に使用されます。<br /><br /> DAC は、スケジューラが専用管理者接続で実行されていることを示します。<br /><br /> HOT ADDED は、スケジューラがホット アド CPU イベントに応答して追加されたことを示します。|  
|is_online|**bit**|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]サーバーで使用可能なプロセッサの一部だけを使用するようにが構成されている場合、この構成では、一部のスケジューラが関係マスクに含まれていないプロセッサにマップされていることを意味します。 この場合、この列は0を返します。 この値は、スケジューラがクエリまたはバッチの処理に使用されていないことを意味します。<br /><br /> NULL 値は許可されません。|  
|is_idle|**bit**|1 = スケジューラはアイドル状態です。 現在実行中のワーカーはありません。 NULL 値は許可されません。|  
|preemptive_switches_count|**int**|このスケジューラのワーカーがプリエンプティブモードに切り替えた回数。<br /><br /> [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 外部のコード (拡張ストアド プロシージャや分散クエリなど) を実行するには、スレッドを非プリエンプティブ スケジューラの制御外で実行する必要があります。 このとき、ワーカーはプリエンプティブ モードに切り替えられます。|  
|context_switches_count|**int**|このスケジューラで発生したコンテキストスイッチの数。 NULL 値は許可されません。<br /><br /> 他のワーカーの実行を許可するには、現在実行しているワーカーでスケジューラの制御を解放するか、コンテキストを切り替える必要があります。<br /><br /> **注:** ワーカーがスケジューラを生成し、それ自体を実行可能キューに配置した後、他のワーカーを検索しない場合、ワーカーは自身を選択します。 この場合、context_switches_count は更新されませんが、yield_count は更新されます。|  
|idle_switches_count|**int**|スケジューラがアイドル中にイベントを待機していた回数。 この列は context_switches_count と類似しています。 NULL 値は許可されません。|  
|current_tasks_count|**int**|このスケジューラに関連付けられている現在のタスクの数。 この数には、次のものが含まれます。<br /><br /> -ワーカーが実行するのを待機しているタスク。<br />-現在待機中または実行中のタスク (中断状態または実行可能状態)。<br /><br /> タスクが完了すると、このカウントは 1 減ります。 NULL 値は許可されません。|  
|runnable_tasks_count|**int**|実行可能キューでスケジュールされるのを待機している、タスクが割り当てられているワーカーの数。 NULL 値は許可されません。|  
|current_workers_count|**int**|このスケジューラに関連付けられているワーカーの数。 この数には、タスクが割り当てられていないワーカーが含まれます。 NULL 値は許可されません。|  
|active_workers_count|**int**|アクティブなワーカーの数。 アクティブなワーカーはプリエンプティブではなく、タスクが関連付けられており、実行中、実行可能、または中断されている必要があります。 NULL 値は許可されません。|  
|work_queue_count|**bigint**|保留中のキュー内のタスクの数。 保留キュー内のタスクは、ワーカーによる取得を待機しています。 NULL 値は許可されません。|  
|pending_disk_io_count|**int**|完了を待機している保留中の i/o の数。 各スケジューラには、コンテキスト切り替えが行われるたびに完了したかどうかを確認するためにチェックされる保留中の i/o の一覧があります。 要求が挿入されると、カウントがインクリメントされます。 要求が完了すると、カウントは 1 減ります。 この数は、I/O の状態を示すわけではありません。 NULL 値は許可されません。|  
|load_factor|**int**|このスケジューラで認識されている負荷を示す内部値。 この値は、新しいタスクをこのスケジューラまたは別のスケジューラに配置する必要があるかどうかを判断するために使用されます。 この値は、スケジューラが均等に読み込まれていないと思われる場合にデバッグのために役立ちます。 ルーティングの決定は、スケジューラの負荷に基づいて行われます。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、ノードとスケジューラの占有率も使用して、リソースを取得するための最適な場所を決定します。 タスクがエンキューされると、占有率が増加します。 タスクが完了すると、占有率は減少します。 占有率を利用すると、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] OS で負荷を効率よく分散できます。 NULL 値は許可されません。|  
|yield_count|**int**|このスケジューラの進行状況を示すために使用される内部値。 この値は、スケジューラモニターによって使用され、スケジューラのワーカーが時間内に他のワーカーに応答していないかどうかを判断します。 この値は、ワーカーまたはタスクが新しいワーカーに移行されたことを示すものではありません。 NULL 値は許可されません。|  
|last_timer_activity|**bigint**|前回、スケジューラのタイマー キューがスケジューラにより確認された時間 (CPU ティック単位)。 NULL 値は許可されません。|  
|failed_to_create_worker|**bit**|このスケジューラで新しいワーカーを作成できなかった場合は、1に設定します。 これは通常、メモリ制約が原因で発生します。 NULL 値が許可されます。|  
|active_worker_address|**varbinary (8)**|現在アクティブなワーカーのメモリアドレス。 NULL 値が許可されます。 詳細については、「 [sys.dm_os_workers &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-os-workers-transact-sql.md)」を参照してください。|  
|memory_object_address|**varbinary (8)**|スケジューラメモリオブジェクトのメモリアドレス。 NULL 値は許容されません。|  
|task_memory_object_address|**varbinary (8)**|タスクメモリオブジェクトのメモリアドレス。 NULL 値は許可されません。 詳細については、「 [sys.dm_os_memory_objects &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-os-memory-objects-transact-sql.md)」を参照してください。|  
|quantum_length_us|**bigint**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]SQLOS によって使用されるスケジューラ クォンタムを公開します。|  
| total_cpu_usage_ms |**bigint**|**適用対象**: [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 以降 <br><br> 非プリエンプティブワーカーによって報告された、このスケジューラによって消費された合計 CPU。 NULL 値は許可されません。|
|total_cpu_idle_capped_ms|**bigint**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)][サービスレベル目標](/azure/sql-data-warehouse/what-is-a-data-warehouse-unit-dwu-cdwu#service-level-objective)に基づく調整を示します。は、Azure 以外のバージョンのでは常に0になり [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。 NULL 値が許可されます。|
|total_scheduler_delay_ms|**bigint**|**適用対象**: [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 以降 <br><br> 1つのワーカーが切り替えを行ってから、もう1つの切り替えが切り替わるまでの時間。 プリエンプティブなワーカーが次の非プリエンプティブワーカーのスケジュール設定を遅らせた場合、または他のプロセスからの OS スケジューリングスレッドによって発生する場合があります。 NULL 値は許可されません。|
|ideal_workers_limit|**int**|**適用対象**: [!INCLUDE[sql-server-2019](../../includes/sssql19-md.md)] 以降 <br><br> スケジューラに最適なワーカーの数。 現在のワーカーが負荷分散されたタスクの負荷によって制限を超過した場合、アイドル状態になると、そのワーカーは切り捨てられます。 NULL 値は許可されません。|
|pdw_node_id|**int**|**適用対象**: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 、 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]<br /><br /> このディストリビューションが配置されているノードの識別子。|  
  
## <a name="permissions"></a>権限
で [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] は、 `VIEW SERVER STATE` 権限が必要です。   
SQL Database Basic、S0、S1 のサービス目標、およびエラスティックプール内のデータベースについては、 [サーバー管理者](/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database) アカウントまたは [Azure Active Directory 管理者](/azure/azure-sql/database/authentication-aad-overview#administrator-structure) アカウントが必要です。 その他のすべての SQL Database サービスの目的で `VIEW DATABASE STATE` は、データベースで権限が必要になります。   

## <a name="examples"></a>例  
  
### <a name="a-monitoring-hidden-and-nonhidden-schedulers"></a>A. 非表示スケジューラと表示スケジューラスケジューラの監視  
 次のクエリは、すべてのスケジューラにおけるのワーカーとタスクの状態を出力し [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。 このクエリは、次のものを持つコンピューターシステムで実行されました。  
  
-   2つのプロセッサ (Cpu)  
  
-   2つの (NUMA) ノード  
  
-   NUMA ノードごとに1つの CPU  
  
-   関係マスクが `0x03` に設定されている  
  
```sql  
SELECT  
    scheduler_id,  
    cpu_id,  
    parent_node_id,  
    current_tasks_count,  
    runnable_tasks_count,  
    current_workers_count,  
    active_workers_count,  
    work_queue_count  
  FROM sys.dm_os_schedulers;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
scheduler_id cpu_id parent_node_id current_tasks_count  
------------ ------ -------------- -------------------  
0            1      0              9                    
257          255    0              1                    
1            0      1              10                   
258          255    1              1                    
255          255    32             2                    
  
runnable_tasks_count current_workers_count  
-------------------- ---------------------  
0                    11                     
0                    1                      
0                    18                     
0                    1                      
0                    3                      
  
active_workers_count work_queue_count  
-------------------- --------------------  
6                    0  
1                    0  
8                    0  
1                    0  
1                    0  
```  
  
 出力には、次の情報が表示されます。  
  
-   スケジューラは5つあります。 2つのスケジューラの ID 値 < 1048576。 ID >= 1048576 のスケジューラは非表示スケジューラと呼ばれます。 Scheduler `255` は、専用管理者接続 (DAC) を表します。 インスタンスごとに 1 つの DAC スケジューラが存在します。 メモリ負荷を調整するリソースモニター `257` `258` は、NUMA ノードごとに1つずつ、スケジューラとスケジューラを使用します。  
  
-   出力には、23個のアクティブなタスクがあります。 これらのタスクには、によって開始されたリソース管理タスクに加えて、ユーザー要求が含まれ [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。 タスクの例とし [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ては、リソースモニター (numa ノードごとに1つ)、LAZY WRITER (numa ノードごとに1つ)、ロックモニター、チェックポイント、ログライターなどがあります。  
  
-   NUMA ノード `0` は CPU `1` にマップされており、NUMA ノード `1` は CPU `0` にマップされています。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] は通常、ノード0以外の NUMA ノードで開始されます。  
  
-   ここでは `runnable_tasks_count` に `0` が返されており、これはアクティブに実行中のタスクが存在しないことを意味します。 ただし、アクティブなセッションが存在する場合もあります。  
  
-   `255`DAC を表す Scheduler には `3` worker が関連付けられています。 これらのワーカーは [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の起動時に割り当てられ、変更されることはありません。 これらのワーカーは、DAC クエリのみを処理するために使用されます。 このスケジューラの2つのタスクは、接続マネージャーとアイドル状態のワーカーを表します。  
  
-   `active_workers_count` タスクが関連付けられ、非プリエンプティブモードで実行されているすべてのワーカーを表します。 ネットワークリスナーなどの一部のタスクは、プリエンプティブスケジューリングで実行されます。  
  
-   非表示のスケジューラは、一般的なユーザー要求を処理しません。 ただし、DAC スケジューラは例外です。 この DAC スケジューラには、要求を処理するためのスレッドが 1 つあります。  
  
### <a name="b-monitoring-nonhidden-schedulers-in-a-busy-system"></a>B. ビジー状態のシステムでの表示スケジューラスケジューラの監視  
 次のクエリでは、負荷が高い表示スケジューラの状態を示します。このスケジューラでは、利用可能なワーカーの処理数を上回る要求が存在します。 この例では、256のワーカーにタスクが割り当てられています。 一部のタスクはワーカーへの割り当てを待機中です。 実行可能数を低くすると、複数のタスクがリソースを待機していることを意味します。  
  
> [!NOTE]  
>  sys.dm_os_workers でクエリを実行すると、ワーカーの状態を確認できます。 詳細については、「 [sys.dm_os_workers &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-os-workers-transact-sql.md)」を参照してください。  
  
 クエリは次のようになります。  
  
```sql  
SELECT  
    scheduler_id,  
    cpu_id,  
    current_tasks_count,  
    runnable_tasks_count,  
    current_workers_count,  
    active_workers_count,  
    work_queue_count  
  FROM sys.dm_os_schedulers  
  WHERE scheduler_id < 255;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
scheduler_id current_tasks_count runnable_tasks_count  
------------ ------------------- --------------------  
0            144                 0                     
1            147                 1                     
  
current_workers_count active_workers_count work_queue_count  
--------------------- -------------------- --------------------  
128                   125                  16  
128                   126                  19  
```  
  
 これに対して、次の結果は、タスクがワーカーの取得を待機していない複数の実行可能なタスクを示しています。 `work_queue_count` は両方のスケジューラで `0` になっています。  
  
```  
scheduler_id current_tasks_count runnable_tasks_count  
------------ ------------------- --------------------  
0            107                 98                    
1            110                 100                   
  
current_workers_count active_workers_count work_queue_count  
--------------------- -------------------- --------------------  
128                   104                  0  
128                   108                  0  
```  
  
## <a name="see-also"></a>参照  
 [SQL Server オペレーティングシステム関連の動的管理ビュー &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sql-server-operating-system-related-dynamic-management-views-transact-sql.md)