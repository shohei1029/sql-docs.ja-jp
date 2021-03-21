---
description: sys.dm_os_nodes (Transact-SQL)
title: sys.dm_os_nodes (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 02/13/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- sys.dm_os_nodes
- dm_os_nodes_TSQL
- dm_os_nodes
- sys.dm_os_nodes_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_os_nodes dynamic management view
ms.assetid: c768b67c-82a4-47f5-850b-0ea282358d50
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 139a6cb96a21828130079b184c0dbf7bfb018c88
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104750942"
---
# <a name="sysdm_os_nodes-transact-sql"></a>sys.dm_os_nodes (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

SQLOS という内部コンポーネントは、ハードウェア プロセッサの局所性を疑似的に表現したノード構造を作成します。 これらの構造体は [、ソフト NUMA](../../database-engine/configure-windows/soft-numa-sql-server.md) を使用してカスタムノードレイアウトを作成することによって変更できます。  

> [!NOTE]
> 以降、では [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 、 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 特定のハードウェア構成でソフト NUMA が自動的に使用されます。 詳細については、「 [自動ソフト NUMA](../../database-engine/configure-windows/soft-numa-sql-server.md#automatic-soft-numa)」を参照してください。
  
次の表は、これらのノードに関する情報を示しています。  
  
> [!NOTE]
> またはからこの DMV を呼び出すに [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] は [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] 、 **sys.dm_pdw_nodes_os_nodes** という名前を使用します。  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|node_id|**smallint**|ノードの ID。|  
|node_state_desc|**nvarchar (256)**|ノードの状態の説明。 相互排他的な値から先に表示され、続けて、組み合わせ可能な値が表示されます。 次に例を示します。<br /> Online、Thread Resources Low、Lazy Preemptive<br /><br />相互に排他的な4つの node_state_desc 値があります。 これらの説明については、以下に説明します。<br /><ul><li>オンライン: ノードはオンラインです<li>OFFLINE: ノードがオフラインです<li>IDLE: ノードには保留中の作業要求がなく、アイドル状態になりました。<li>IDLE_READY: ノードには保留中の作業要求がなく、アイドル状態に入る準備ができています。</li></ul><br />Node_state_desc 値には3つの組み合わせがあります。以下にその説明を示します。<br /><ul><li>DAC: このノードは [専用管理接続](../../database-engine/configure-windows/diagnostic-connection-for-database-administrators.md)用に予約されています。<li>THREAD_RESOURCES_LOW: メモリ不足の状態により、このノードに新しいスレッドを作成することはできません。<li>ホット追加: ホットアド CPU イベントへの応答としてノードが追加されたことを示します。</li></ul>|  
|memory_object_address|**varbinary (8)**|このノードに関連付けられているメモリ オブジェクトのアドレス。 [Sys.dm_os_memory_objects](../../relational-databases/system-dynamic-management-views/sys-dm-os-memory-objects-transact-sql.md).memory_object_address の一対一の関係。|  
|memory_clerk_address|**varbinary (8)**|このノードに関連付けられているメモリクラークのアドレス。 [Sys.dm_os_memory_clerks](../../relational-databases/system-dynamic-management-views/sys-dm-os-memory-clerks-transact-sql.md).memory_clerk_address の一対一の関係。|  
|io_completion_worker_address|**varbinary (8)**|このノードの IO 完了に割り当てられているワーカーのアドレス。 [Sys.dm_os_workers](../../relational-databases/system-dynamic-management-views/sys-dm-os-workers-transact-sql.md).worker_address の一対一の関係。|  
|memory_node_id|**smallint**|このノードが属しているメモリノードの ID。 [Sys.dm_os_memory_nodes](../../relational-databases/system-dynamic-management-views/sys-dm-os-memory-nodes-transact-sql.md).memory_node_id との多対一の関係。|  
|cpu_affinity_mask|**bigint**|このノードが関連付けられている Cpu を識別するビットマップ。|  
|online_scheduler_count|**smallint**|このノードによって管理されているオンラインスケジューラの数。|  
|idle_scheduler_count|**smallint**|アクティブなワーカーの存在しないオンライン スケジューラの数。|  
|active_worker_count|**int**|このノードによって管理されているすべてのスケジューラでアクティブなワーカーの数。|  
|avg_load_balance|**int**|このノード上のスケジューラあたりの平均タスク数。|  
|timer_task_affinity_mask|**bigint**|タイマー タスクの割り当てが可能なスケジューラを識別するビットマップ。|  
|permanent_task_affinity_mask|**bigint**|永続的なタスクの割り当てが可能なスケジューラを識別するビットマップ。|  
|resource_monitor_state|**bit**|各ノードには1つのリソースモニターが割り当てられています。 リソースモニターは、実行中またはアイドル状態になっている可能性があります。 値1は実行を示し、値0はアイドル状態を示します。|  
|online_scheduler_mask|**bigint**|このノードのプロセス関係マスクを識別します。|  
|processor_group|**smallint**|このノードのプロセッサ グループを識別します。|  
|cpu_count |**int** |このノードで使用可能な Cpu の数。 |
|pdw_node_id|**int**|このディストリビューションが配置されているノードの識別子。<br /><br /> **適用対象**: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 、 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]|  
  
## <a name="permissions"></a>権限

で [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] は、 `VIEW SERVER STATE` 権限が必要です。   
SQL Database Basic、S0、S1 のサービス目標、およびエラスティックプール内のデータベースについては、 [サーバー管理者](/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database) アカウントまたは [Azure Active Directory 管理者](/azure/azure-sql/database/authentication-aad-overview#administrator-structure) アカウントが必要です。 その他のすべての SQL Database サービスの目的で `VIEW DATABASE STATE` は、データベースで権限が必要になります。   

## <a name="see-also"></a>参照    
 [SQL Server オペレーティングシステム関連の動的管理ビュー &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sql-server-operating-system-related-dynamic-management-views-transact-sql.md)   
 [ソフト NUMA &#40;SQL Server&#41;](../../database-engine/configure-windows/soft-numa-sql-server.md)  
