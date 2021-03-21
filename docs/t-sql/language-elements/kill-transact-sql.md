---
description: KILL (Transact-SQL)
title: KILL (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 08/31/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- KILL_TSQL
- KILL
dev_langs:
- TSQL
helpviewer_keywords:
- WITH STATUSONLY option
- terminating distributed transactions
- distributed transactions [SQL Server], terminating
- in-doubt transactions
- IDs [SQL Server], user processes
- ending distributed transactions [SQL Server]
- stopping distributed transactions
- session IDs [SQL Server]
- system process termination [SQL Server]
- stopping processes
- ending processes [SQL Server]
- UOW [SQL Server]
- orphaned transactions [SQL Server]
- unit of work [SQL Server]
- process termination [SQL Server]
- KILL statement
- terminating process
ms.assetid: 071cf260-c794-4b45-adc0-0e64097938c0
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: f49a19d0ffa6db9c7834edc6300b8aad5fecd0ec
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104753972"
---
# <a name="kill-transact-sql"></a>KILL (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

セッション ID または作業単位 (UOW) に基づいてユーザーのプロセスを終了します。 指定したセッション ID または UOW に元に戻す作業が多く含まれている場合、KILL ステートメントは、完了するまで時間がかかる可能性があります。 特に、プロセスに長いトランザクションのロールバックが含まれている場合、このプロセスは完了するまで時間がかかります。  
  
KILL は通常の接続を終了します。これにより、指定されたセッション ID に関連付けられたトランザクションが内部的に停止します。 [!INCLUDE[msCoName](../../includes/msconame-md.md)] 分散トランザクション コーディネーター (MS DTC) が使用されることがあります。 MS DTC が使用されている場合も、このステートメントを使用して、孤立している分散トランザクションと疑いのある分散トランザクションを終了できます。  
  
![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql  
-- Syntax for SQL Server  
  
KILL { session ID [ WITH STATUSONLY ] | UOW [ WITH STATUSONLY | COMMIT | ROLLBACK ] }    

```  
  
```syntaxsql  
-- Syntax for Azure Synapse Analytics and Parallel Data Warehouse  
  
KILL 'session_id'  
[;]   
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数

_session ID_   
終了するプロセスのセッション ID です。 `session_id` は、接続されたときに各ユーザー接続に割り当てられる一意の整数 (**int** 型) です。 セッション ID の値は、接続の間、接続に関連付けられます。 接続が終了すると、この整数値は解放され、新しい接続に再度割り当てることができます。  

次のクエリを使用して、強制終了する `session_id` を識別できます。  

 ```sql  
 SELECT conn.session_id, host_name, program_name,
     nt_domain, login_name, connect_time, last_request_end_time 
FROM sys.dm_exec_sessions AS sess
JOIN sys.dm_exec_connections AS conn
    ON sess.session_id = conn.session_id;

```  


_UOW_   
分散トランザクションの作業単位 ID (UOW) を指定します。 _UOW_ は、`sys.dm_tran_locks` 動的管理ビューの request_owner_guid 列から取得できる GUID です。 _UOW_ は、エラー ログや MS DTC モニターからも取得できます。 分散トランザクションの監視の詳細については、MS DTC のドキュメントを参照してください。  
  
孤立した分散トランザクションを終了するには、KILL \<UOW> を使用します。 これらのトランザクションは、実際のセッション ID には関連付けられていませんが、人為的にセッション ID = '-2' に関連付けられています。 `sys.dm_tran_locks`、` sys.dm_exec_sessions`、`sys.dm_exec_requests` のいずれかの動的管理ビューのセッション ID 列を照会すると、孤立したトランザクションをこのセッション ID で容易に識別できます。  

_WITH STATUSONLY_   
指定した _UOW_ または `session_id` の、前の KILL ステートメントに従って実行されているロールバックの進行状況レポートを生成するために使用されます。 KILL WITH STATUSONLY では、UOW および session ID の終了もロールバックも実行されません。 このコマンドは、ロールバックの現在の進行状況のみを表示します。

_WITH COMMIT_   
コミットを伴う未解決の分散トランザクションを中止するために使用されます。 分散トランザクションにのみ適用されます。このオプションを使用するには、_UOW_ を指定する必要があります。  詳細については、[分散トランザクション](../../database-engine/availability-groups/windows/configure-availability-group-for-distributed-transactions.md#manage-unresolved-transactions)に関するセクションを参照してください。

_WITH ROLLBACK_   
ロールバックを伴う未解決の分散トランザクションを中止するために使用されます。 分散トランザクションにのみ適用されます。このオプションを使用するには、_UOW_ を指定する必要があります。  詳細については、[分散トランザクション](../../database-engine/availability-groups/windows/configure-availability-group-for-distributed-transactions.md#manage-unresolved-transactions)に関するセクションを参照してください。


  
## <a name="remarks"></a>解説  
KILL は、通常は、他の重要なプロセスをロックを使用してブロックしているプロセスを終了するために使用されます。 KILL は、必要なシステム リソースを使用しているクエリを実行しているプロセスを停止するためにも使用できます。 システム プロセスと拡張ストアド プロシージャを実行しているプロセスは終了できません。  
  
重要なプロセスが実行している場合は特に、KILL の使用には注意してください。 ユーザー自身のプロセスは終了できません。 次のプロセスも、終了すべきではありません。  
  
-   AWAITING COMMAND  
-   CHECKPOINT SLEEP  
-   LAZY WRITER  
-   LOCK MONITOR  
-   SIGNAL HANDLER  
  
現在のセッションのセッション ID 値を表示するには、@@SPID を使用します。  
  
使用中のセッション ID 値に関するレポートを取得するには、sys.dm_tran_locks、sys.dm_exec_sessions、および sys.dm_exec_requests の各動的管理ビューで session_id 列のクエリを実行します。 sp_who システム ストアド プロシージャが返す SPID 列を確認することもできます。 特定の SPID のロールバックが進行中である場合、その SPID に関する sp_who 結果セット内の cmd 列には、KILLED/ROLLBACK が示されます。  
  
特定の接続がデータベース リソースをロックして別の接続の進行を妨げている場合、`sys.dm_exec_requests` の `blocking_session_id` 列、または `sp_who` が返す `blk` 列に、ブロックしている接続のセッション ID が示されます。  
  
KILL コマンドは、状態が不明な分散トランザクションの解決にも使用できます。 これらのトランザクションは、データベース サーバーまたは MS DTC コーディネーターを予定外に再起動したために生じた未解決の分散トランザクションです。 未確定トランザクションの詳細については、「[マークされたトランザクションを使用して関連するデータベースを一貫した状態に復元する方法 &#40;完全復旧モデル&#41;](../../relational-databases/backup-restore/use-marked-transactions-to-recover-related-databases-consistently.md)」の「2 フェーズ コミット」を参照してください。  
  
## <a name="using-with-statusonly"></a>WITH STATUSONLY を使用する  
KILL WITH STATUSONLY は、以前の KILL _session ID_|_UOW_ ステートメントに従ってセッション ID または UOW が現在ロールバックされている場合に、レポートを生成します。 進行状況レポートには、完了したロールバックの進行状況 (% 単位) と予想残り時間 (秒単位) が示されます。 レポートは、次の形式で示されます。  
  
`Spid|UOW <xxx>: Transaction rollback in progress. Estimated rollback completion: <yy>% Estimated time left: <zz> seconds`  
  
KILL _session ID_|_UOW_ WITH STATUSONLY ステートメントの実行時にセッション ID または UOW のロールバックが完了している場合、KILL _session ID_|_UOW_ WITH STATUSONLY は次のエラー メッセージを返します。  
  
```
"Msg 6120, Level 16, State 1, Line 1"  
"Status report cannot be obtained. Rollback operation for Process ID <session ID> is not in progress."
```  
このエラーは、ロールバックされているセッション ID または UOW が存在しない場合にも発生します。


WITH STATUSONLY オプションを設定しない状態で同じ KILL  _ID_|_UOW_ ステートメントを繰り返すことによって、同じ状態レポートを取得できます。 ただし、このオプションをこの方法で繰り返すことはお勧めしません。 新しい KILL ステートメントを実行する前に、ロールバックが完了し、セッション ID が新しいタスクに再割り当てされている場合、KILL _session ID_ ステートメントの繰り返しによって、新しいプロセスが停止する可能性があります。 WITH STATUSONLY を指定することで、新しいプロセスが停止しないようにしてください。  
  
## <a name="permissions"></a>アクセス許可  
**[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]:** ALTER ANY CONNECTION 権限が必要です。 ALTER ANY CONNECTION は、固定サーバー ロール sysadmin または processadmin のメンバーシップに含まれています。  
  
**[!INCLUDE[ssSDS](../../includes/sssds-md.md)]:** KILL DATABASE CONNECTION 権限が必要です。 サーバー レベル プリンシパル ログインが、KILL DATABASE CONNECTION です。  
  
## <a name="examples"></a>例  
  
### <a name="a-using-kill-to-stop-a-session"></a>A. KILL を使用してセッションを停止する  
 次の例は、セッション ID `53` を停止する方法を示しています。  
  
```sql  
KILL 53;  
GO  
```  
  
### <a name="b-using-kill-session-id-with-statusonly-to-obtain-a-progress-report"></a>B. KILL セッション ID WITH STATUSONLY を使用して進行状況レポートを取得する  
次の例では、特定のセッション ID に対するロールバック プロセスの状態を取得します。  
  
```sql  
KILL 54;  
KILL 54 WITH STATUSONLY;  
GO  
  
--This is the progress report.  
spid 54: Transaction rollback in progress. Estimated rollback completion: 80% Estimated time left: 10 seconds.  
```  
  
### <a name="c-using-kill-to-stop-an-orphaned-distributed-transaction"></a>C. KILL を使用して、孤立した分散トランザクションを停止する  
次の例は、`D5499C66-E398-45CA-BF7E-DC9C194B48CF` の *UOW* で、孤立した分散トランザクション (セッション ID = -2) を停止する例を示しています。  
  
```sql  
KILL 'D5499C66-E398-45CA-BF7E-DC9C194B48CF';  
```  

  
## <a name="see-also"></a>参照  
[KILL STATS JOB &#40;Transact-SQL&#41;](../../t-sql/language-elements/kill-stats-job-transact-sql.md)   
[KILL QUERY NOTIFICATION SUBSCRIPTION &#40;Transact-SQL&#41;](../../t-sql/language-elements/kill-query-notification-subscription-transact-sql.md)   
[組み込み関数 &#40;Transact-SQL&#41;](~/t-sql/functions/functions.md)   
[SHUTDOWN &#40;Transact-SQL&#41;](../../t-sql/language-elements/shutdown-transact-sql.md)   
[@@SPID &#40;Transact-SQL&#41;](../../t-sql/functions/spid-transact-sql.md)   
[sys.dm_exec_requests &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql.md)   
[sys.dm_exec_sessions &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql.md)   
[sys.dm_tran_locks &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql.md)   
[sp_lock &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-lock-transact-sql.md)   
[sp_who &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-who-transact-sql.md)  
  
  
