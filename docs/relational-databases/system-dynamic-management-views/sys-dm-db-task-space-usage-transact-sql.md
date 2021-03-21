---
description: sys.dm_db_task_space_usage (Transact-SQL)
title: sys.dm_db_task_space_usage (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- dm_db_task_space_usage_TSQL
- sys.dm_db_task_space_usage_TSQL
- dm_db_task_space_usage
- sys.dm_db_task_space_usage
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_db_task_space_usage dynamic management view
ms.assetid: fb0c87e5-43b9-466a-a8df-11b3851dc6d0
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: eb2a8b8ac5d92812b580d1c1497d56888e138ec0
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104755512"
---
# <a name="sysdm_db_task_space_usage-transact-sql"></a>sys.dm_db_task_space_usage (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  データベースに対するタスクごとに、ページの割り当てと割り当て解除の処理に関する情報を返します。  
  
> [!NOTE]  
>  このビューは、 [tempdb データベース](../../relational-databases/databases/tempdb-database.md)にのみ適用できます。  
  
> [!NOTE]  
>  またはからこれを呼び出すに [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] は [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] 、 **sys.dm_pdw_nodes_db_task_space_usage** という名前を使用します。  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|**session_id**|**smallint**|セッション ID。|  
|**request_id**|**int**|セッション内の要求 ID。<br /><br /> 要求はバッチとも呼ばれ、1つ以上のクエリを含むことができます。 セッションでは、同時に複数の要求をアクティブにすることができます。 並列実行プランが使用されている場合、要求内の各クエリでは複数のスレッド (タスク) を開始できます。|  
|**exec_context_id**|**int**|タスクの実行コンテキスト ID。 詳細については、「 [sys.dm_os_tasks &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-os-tasks-transact-sql.md)」を参照してください。|  
|**database_id**|**smallint**|データベース ID。|  
|**user_objects_alloc_page_count**|**bigint**|このタスクによってユーザーオブジェクトに対して予約または割り当てられたページ数。|  
|**user_objects_dealloc_page_count**|**bigint**|このタスクによってユーザーオブジェクトの割り当てが解除され、予約されなくなったページの数。|  
|**internal_objects_alloc_page_count**|**bigint**|このタスクによって内部オブジェクトに予約または割り当てられたページの数。|  
|**internal_objects_dealloc_page_count**|**bigint**|タスクで、内部オブジェクトへの割り当てが解除され、予約されなくなったページの数。|  
|**pdw_node_id**|**int**|**適用対象**: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 、 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]<br /><br /> このディストリビューションが配置されているノードの識別子。|  
  
## <a name="permissions"></a>権限

で [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] は、 `VIEW SERVER STATE` 権限が必要です。   
SQL Database Basic、S0、S1 のサービス目標、およびエラスティックプール内のデータベースについては、 [サーバー管理者](/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database) アカウントまたは [Azure Active Directory 管理者](/azure/azure-sql/database/authentication-aad-overview#administrator-structure) アカウントが必要です。 その他のすべての SQL Database サービスの目的で `VIEW DATABASE STATE` は、データベースで権限が必要になります。   

## <a name="remarks"></a>解説  
 このビューによって報告されたページ数には、IAM ページは含まれていません。  
  
 ページ カウンターは要求の開始時にゼロ (0) に初期化されます。 これらの値は、要求が完了したときにセッションレベルで集計されます。 詳しくは、「[sys.dm_db_session_space_usage &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-session-space-usage-transact-sql.md)」をご覧ください。  
  
 作業テーブルキャッシュ、一時テーブルキャッシュ、および遅延削除操作は、指定したタスクで割り当てられたページ数と割り当て解除されたページ数に影響します。  
  
## <a name="user-objects"></a>ユーザーオブジェクト  
 ユーザーオブジェクトページカウンターには、次のオブジェクトが含まれています。  
  
-   ユーザー定義テーブルとインデックス  
  
-   システムテーブルとインデックス  
  
-   グローバル一時テーブルとインデックス  
  
-   ローカル一時テーブルとインデックス  
  
-   テーブル変数  
  
-   テーブル値関数で返されるテーブル  
  
## <a name="internal-objects"></a>内部オブジェクト  
 内部オブジェクトは **tempdb** にのみ存在します。 内部オブジェクトページカウンターには、次のオブジェクトが含まれています。  
  
-   カーソルまたはスプール操作の作業テーブルと、一時的なラージオブジェクト (LOB) ストレージ  
  
-   ハッシュ結合などの操作用の作業ファイル  
  
-   並べ替え実行結果  
  
## <a name="physical-joins"></a>物理結合  
 ![sys.dm_db_session_task_usage の物理結合](../../relational-databases/system-dynamic-management-views/media/join-dm-db-task-space-usage-1.gif "sys.dm_db_session_task_usage の物理結合")  
  
## <a name="relationship-cardinalities"></a>リレーションシップ基数  
  
|ソース|終了|リレーションシップ|  
|----------|--------|------------------|  
|dm_db_task_space_usage.request_id|dm_exec_requests.request_id|一対一|  
|dm_db_task_space_usage dm_db_task_space_usage.session_id|dm_exec_requests.session_id|一対一|  
  
## <a name="see-also"></a>参照  
 [動的管理ビューと動的管理関数 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)   
 [Transact-sql&#41;&#40;データベース関連の動的管理ビュー ](../../relational-databases/system-dynamic-management-views/database-related-dynamic-management-views-transact-sql.md)   
 [sys.dm_exec_sessions &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql.md)   
 [sys.dm_exec_requests &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql.md)   
 [sys.dm_os_tasks &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-os-tasks-transact-sql.md)   
 [sys.dm_db_session_space_usage &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-session-space-usage-transact-sql.md)   
 [sys.dm_db_file_space_usage &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-file-space-usage-transact-sql.md)  
  
