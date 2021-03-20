---
description: sys.dm_os_waiting_tasks (Transact-SQL)
title: sys.dm_os_waiting_tasks (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/13/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- dm_os_waiting_tasks
- sys.dm_os_waiting_tasks_TSQL
- dm_os_waiting_tasks_TSQL
- sys.dm_os_waiting_tasks
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_os_waiting_tasks dynamic management view
ms.assetid: ca5e6844-368c-42e2-b187-6e5f5afc8df3
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: b322638c5d6b1b6a1efb453e7227136691434c86
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104755872"
---
# <a name="sysdm_os_waiting_tasks-transact-sql"></a>sys.dm_os_waiting_tasks (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  あるリソースで待機しているタスクの待機キューに関する情報を返します。 タスクの詳細については、「 [スレッドおよびタスクアーキテクチャガイド](../../relational-databases/thread-and-task-architecture-guide.md)」を参照してください。
   
> [!NOTE]  
> またはからこれを呼び出すに [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] は [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] 、 **sys.dm_pdw_nodes_os_waiting_tasks** という名前を使用します。  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|**waiting_task_address**|**varbinary (8)**|待機中のタスクのアドレス。|  
|**session_id**|**smallint**|タスクに関連付けられているセッションの ID。|  
|**exec_context_id**|**int**|タスクに関連付けられている実行コンテキストの ID。|  
|**wait_duration_ms**|**bigint**|この待機の種類の合計待機時間 (ミリ秒単位)。 この時間には **signal_wait_time** が含まれます。|  
|**wait_type**|**nvarchar(60)**|待機の種類の名前。|  
|**resource_address**|**varbinary (8)**|タスクが待機しているリソースのアドレス。|  
|**blocking_task_address**|**varbinary (8)**|このリソースを現在保持しているタスク|  
|**blocking_session_id**|**smallint**|要求をブロックしているセッションの ID。 この列が NULL の場合は、要求がブロックされていないか、ブロックしているセッションのセッション情報が使用または識別できません。<br /><br /> -2 = ブロックしているリソースは、孤立した分散トランザクションが所有しています。<br /><br /> -3 = ブロックしているリソースは、遅延復旧トランザクションが所有しています。<br /><br /> -4 = 内部ラッチの状態遷移のため、ブロックしているラッチの所有者のセッション ID を特定できませんでした。|  
|**blocking_exec_context_id**|**int**|ブロックしているタスクの実行コンテキストの ID。|  
|**resource_description**|**nvarchar (3072)**|消費されているリソースの説明。 詳細については、以下の説明を参照してください。|  
|**pdw_node_id**|**int**|**適用対象**: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 、 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]<br /><br /> このディストリビューションが配置されているノードの識別子。|  
  
## <a name="resource_description-column"></a>resource_description 列  
 resource_description 列に返される値は次のとおりです。  
  
 **スレッドプールリソース所有者:**  
  
-   threadpool id = scheduler\<hex-address>  
  
 **並列クエリリソース所有者:**  
  
-   exchangeEvent id = {Port |パイプ} \<hex-address> WaitType = \<exchange-wait-type> nodeId =\<exchange-node-id>  
  
 **Exchange-待機の種類:**  
  
-   e_waitNone  
  
-   e_waitPipeNewRow  
  
-   e_waitPipeGetRow  
  
-   e_waitSynchronizeConsumerOpen  
  
-   e_waitPortOpen  
  
-   e_waitPortClose  
  
-   e_waitRange  
  
 **ロック リソース所有者:**  
  
-   \<type-specific-description> id = ロック \<lock-hex-address> モード = \<mode> associatedObjectId =\<associated-obj-id>  
  
     **\<type-specific-description> 次のように指定できます。**  
  
    -   データベースの場合: databaselock subresource = \<databaselock-subresource> dbid =\<db-id>  
  
    -   ファイルの場合: filelock fileid = \<file-id> subresource = \<filelock-subresource> dbid =\<db-id>  
  
    -   オブジェクトの場合: objectlock lockPartition = \<lock-partition-id> objid = \<obj-id> subresource = \<objectlock-subresource> dbid =\<db-id>  
  
    -   PAGE の場合: pagelock fileid = \<file-id> ページ id = \<page-id> dbid = \<db-id> subresource =\<pagelock-subresource>  
  
    -   キーの場合: キーロック hobtid = \<hobt-id> dbid =\<db-id>  
  
    -   エクステントの場合: extentlock fileid = \<file-id> ページ id = \<page-id> dbid =\<db-id>  
  
    -   RID の場合: ridlock fileid = \<file-id> ページ id = \<page-id> dbid =\<db-id>  
  
    -   アプリケーションの場合: applicationlock hash = \<hash> databasePrincipalId = \<role-id> dbid =\<db-id>  
  
    -   メタデータの場合: metadatalock subresource = \<metadata-subresource> classid = \<metadatalock-description> dbid =\<db-id>  
  
    -   HOBT の場合: hobtlock hobtid = \<hobt-id> subresource = \<hobt-subresource> dbid =\<db-id>  
  
    -   ALLOCATION_UNIT の場合: allocunitlock hobtid = \<hobt-id> subresource = \<alloc-unit-subresource> dbid =\<db-id>  
  
     **\<mode> 次のように指定できます。**  
  
     Sch-m-S、Sch-m、S、U、X、IS、IU、IX、SIU、6、UIX、BU、範囲-S、範囲-U、RangeI-N、RangeI-S、RangeI-U、RangeI-X、RangeX-、RangeX-U、RangeX-X  
  
 **外部リソース所有者:**  
  
-   外部 ExternalResource =\<wait-type>  
  
 **汎用リソース所有者:**  
  
-   TransactionMutex Transactionmutex ワークスペース =\<workspace-id>  
  
-   Mutex  
  
-   CLRTaskJoin  
  
-   CLRMonitorEvent  
  
-   CLRRWLockEvent  
  
-   resourceWait  
  
 **ラッチリソース所有者:**  
  
-   \<db-id>:\<file-id>:\<page-in-file>  
  
-   \<GUID>  
  
-   \<latch-class> (\<latch-address>)  
  
## <a name="permissions"></a>権限

で [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] は、 `VIEW SERVER STATE` 権限が必要です。   
SQL Database Basic、S0、S1 のサービス目標、およびエラスティックプール内のデータベースについては、 [サーバー管理者](/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database) アカウントまたは [Azure Active Directory 管理者](/azure/azure-sql/database/authentication-aad-overview#administrator-structure) アカウントが必要です。 その他のすべての SQL Database サービスの目的で `VIEW DATABASE STATE` は、データベースで権限が必要になります。   
 
## <a name="example"></a>例
### <a name="a-identify-tasks-from-blocked-sessions"></a>A. ブロックされたセッションからタスクを識別します。 

```sql
SELECT * FROM sys.dm_os_waiting_tasks 
WHERE blocking_session_id IS NOT NULL; 
```   

### <a name="b-view-waiting-tasks-per-connection"></a>B. 接続ごとの待機中のタスクの表示

```sql
SELECT st.text AS [SQL Text], c.connection_id, w.session_id, 
  w.wait_duration_ms, w.wait_type, w.resource_address, 
  w.blocking_session_id, w.resource_description, c.client_net_address, c.connect_time
FROM sys.dm_os_waiting_tasks AS w
INNER JOIN sys.dm_exec_connections AS c ON w.session_id = c.session_id 
CROSS APPLY (SELECT * FROM sys.dm_exec_sql_text(c.most_recent_sql_handle)) AS st 
              WHERE w.session_id > 50 AND w.wait_duration_ms > 0
ORDER BY c.connection_id, w.session_id
GO
```

### <a name="c-view-waiting-tasks-for-all-user-processes-with-additional-information"></a>C. すべてのユーザープロセスの待機中のタスクを追加情報で表示する

```sql
SELECT 'Waiting_tasks' AS [Information], owt.session_id,
    owt.wait_duration_ms, owt.wait_type, owt.blocking_session_id,
    owt.resource_description, es.program_name, est.text,
    est.dbid, eqp.query_plan, er.database_id, es.cpu_time,
    es.memory_usage*8 AS memory_usage_KB
FROM sys.dm_os_waiting_tasks owt
INNER JOIN sys.dm_exec_sessions es ON owt.session_id = es.session_id
INNER JOIN sys.dm_exec_requests er ON es.session_id = er.session_id
OUTER APPLY sys.dm_exec_sql_text (er.sql_handle) est
OUTER APPLY sys.dm_exec_query_plan (er.plan_handle) eqp
WHERE es.is_user_process = 1
ORDER BY owt.session_id;
GO
```
  
## <a name="see-also"></a>参照  
[SQL Server オペレーティングシステム関連の動的管理ビュー &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sql-server-operating-system-related-dynamic-management-views-transact-sql.md)      
[スレッドおよびタスクのアーキテクチャ ガイド](../../relational-databases/thread-and-task-architecture-guide.md)     
   
