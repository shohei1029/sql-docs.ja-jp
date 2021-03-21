---
description: sys.dm_os_tasks (Transact-sql)
title: sys.dm_os_tasks (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/13/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- sys.dm_os_tasks
- sys.dm_os_tasks_TSQL
- dm_os_tasks_TSQL
- dm_os_tasks
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_os_tasks dynamic management view
ms.assetid: 180a3c41-e71b-4670-819d-85ea7ef98bac
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: e9191cfbb71098198f7f9674c29aaa268294d180
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104750672"
---
# <a name="sysdm_os_tasks-transact-sql"></a>sys.dm_os_tasks (Transact-sql)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  のインスタンスでアクティブになっているタスクごとに1行の値を返し [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。 タスクは、SQL Server での実行の基本単位です。 タスクの例としては、クエリ、ログイン、ログアウト、およびゴーストクリーンアップアクティビティ、チェックポイントアクティビティ、ログライター、並列再実行アクティビティなどのシステムタスクがあります。 タスクの詳細については、「 [スレッドおよびタスクアーキテクチャガイド](../../relational-databases/thread-and-task-architecture-guide.md)」を参照してください。
  
> [!NOTE]  
> またはからこれを呼び出すに [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] は [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] 、 **sys.dm_pdw_nodes_os_tasks** という名前を使用します。  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|**task_address**|**varbinary (8)**|オブジェクトのメモリアドレス。|  
|**task_state**|**nvarchar(60)**|タスクの状態です。 DLL は、次のいずれかの場所に置くことができます。<br /><br /> PENDING: ワーカー スレッドを待機しています。<br /><br /> RUNNABLE: 実行可能ですが、クォンタムの受信を待機しています。<br /><br /> 実行中: 現在スケジューラ上で実行されています。<br /><br /> [中断]: ワーカーがありますが、イベントを待機しています。<br /><br /> 完了: 完了しました。<br /><br /> SPINLOOP: スピンロックで停止しています。|  
|**context_switches_count**|**int**|このタスクが完了したスケジューラコンテキストスイッチの数。|  
|**pending_io_count**|**int**|このタスクによって実行される物理 i/o の数。|  
|**pending_io_byte_count**|**bigint**|このタスクによって実行される i/o の合計バイト数。|  
|**pending_io_byte_average**|**int**|このタスクによって実行される i/o の平均バイト数。|  
|**scheduler_id**|**int**|親スケジューラの ID。 これは、このタスクの scheduler 情報を処理するハンドルです。 詳細については、「 [sys.dm_os_schedulers &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-os-schedulers-transact-sql.md)」を参照してください。|  
|**session_id**|**smallint**|タスクに関連付けられているセッションの ID。|  
|**exec_context_id**|**int**|タスクに関連付けられている実行コンテキスト ID。|  
|**request_id**|**int**|タスクの要求の ID。 詳細については、「 [sys.dm_exec_requests &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql.md)」を参照してください。|  
|**worker_address**|**varbinary (8)**|タスクを実行しているワーカーのメモリアドレス。<br /><br /> NULL = タスクは、ワーカーが実行可能になるのを待機しているか、タスクの実行が完了した直後です。<br /><br /> 詳細については、「 [sys.dm_os_workers &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-os-workers-transact-sql.md)」を参照してください。|  
|**host_address**|**varbinary (8)**|ホストのメモリアドレス。<br /><br /> 0 = ホストはタスクの作成に使用されませんでした。 これは、このタスクの作成に使用されたホストを識別するのに役立ちます。<br /><br /> 詳細については、「 [sys.dm_os_hosts &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-os-hosts-transact-sql.md)」を参照してください。|  
|**parent_task_address**|**varbinary (8)**|オブジェクトの親であるタスクのメモリアドレス。|  
|**pdw_node_id**|**int**|**適用対象**: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 、 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]<br /><br /> このディストリビューションが配置されているノードの識別子。|  
  
## <a name="permissions"></a>権限
で [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] は、 `VIEW SERVER STATE` 権限が必要です。   
SQL Database Basic、S0、S1 のサービス目標、およびエラスティックプール内のデータベースについては、 [サーバー管理者](/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database) アカウントまたは [Azure Active Directory 管理者](/azure/azure-sql/database/authentication-aad-overview#administrator-structure) アカウントが必要です。 その他のすべての SQL Database サービスの目的で `VIEW DATABASE STATE` は、データベースで権限が必要になります。   

## <a name="examples"></a>例  
  
### <a name="a-monitoring-parallel-requests"></a>A. 並列要求の監視  
 並列で実行される要求の場合は、(,) の同じ組み合わせに対して複数の行が表示され \<**session_id**> \<**request_id**> ます。 次のクエリを使用して、すべてのアクティブな要求に対する [並列処理の最大限度を構成するサーバー構成オプション](../../database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option.md) を検索します。  
  
> [!NOTE]  
>  **Request_id** は、セッション内で一意です。  
  
```sql  
SELECT  
    task_address,  
    task_state,  
    context_switches_count,  
    pending_io_count,  
    pending_io_byte_count,  
    pending_io_byte_average,  
    scheduler_id,  
    session_id,  
    exec_context_id,  
    request_id,  
    worker_address,  
    host_address  
  FROM sys.dm_os_tasks  
  ORDER BY session_id, request_id;  
```  
  
### <a name="b-associating-session-ids-with-windows-threads"></a>B. セッション ID を Windows のスレッドに関連付ける  
 次のクエリを使用して、セッション ID の値を Windows スレッド ID に関連付けることができます。 これにより、Windows パフォーマンス モニターでスレッドのパフォーマンスを監視できます。 次のクエリでは、スリープ状態のセッションに関する情報は返されません。  
  
```sql  
SELECT STasks.session_id, SThreads.os_thread_id  
  FROM sys.dm_os_tasks AS STasks  
  INNER JOIN sys.dm_os_threads AS SThreads  
    ON STasks.worker_address = SThreads.worker_address  
  WHERE STasks.session_id IS NOT NULL  
  ORDER BY STasks.session_id;  
GO  
```  

## <a name="see-also"></a>参照  
[SQL Server オペレーティングシステム関連の動的管理ビュー &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sql-server-operating-system-related-dynamic-management-views-transact-sql.md)    
[スレッドおよびタスクのアーキテクチャ ガイド](../../relational-databases/thread-and-task-architecture-guide.md)     
