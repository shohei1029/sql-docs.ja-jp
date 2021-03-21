---
description: sys.dm_exec_dms_workers (Transact-sql)
title: sys.dm_exec_dms_workers (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 11/04/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- SYS.DM_EXEC_DMS_WORKERS_TSQL
- DM_EXEC_DMS_WORKERS_TSQL
- DM_EXEC_DMS_WORKERS
dev_langs:
- TSQL
helpviewer_keywords:
- PolyBase,views
- PolyBase
- dm_exec_dms_workers management view
- sys.dm_exec_dms_workers management view
ms.assetid: f468da29-78c3-4f10-8a3c-17905bbf46f2
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: cfabe8d8d7b4dff3ca81c33b7bb8e2c3dd4e65e7
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104752172"
---
# <a name="sysdm_exec_dms_workers-transact-sql"></a>sys.dm_exec_dms_workers (Transact-sql)
[!INCLUDE [sqlserver2016-asa-pdw](../../includes/applies-to-version/sqlserver2016-asa-pdw.md)]

  DMS ステップを完了するすべてのワーカーに関する情報を保持します。  
  
 このビューには、最後の1000要求とアクティブな要求のデータが表示されます。アクティブな要求には、常にこのビュー内のデータが含まれます。  
  
|列名|データ型|説明|Range|  
|-----------------|---------------|-----------------|-----------|  
|execution_id|`nvarchar(32)`|この DMS ワーカーが含まれているクエリ。 <br /><br /> execution_id、step_index、および dms_step_index は、このビューのキーを形成します。||  
|step_index|`int`|この DMS ワーカーが含まれているクエリステップ。|[Transact-sql&#41;&#40;sys.dm_exec_distributed_request_steps](../../relational-databases/system-dynamic-management-views/sys-dm-exec-distributed-request-steps-transact-sql.md)のステップインデックスを参照してください。|  
|dms_step_index|`int`|このワーカーが実行されている DMS プランのステップ。|「 [Sys.dm_exec_dms_workers (transact-sql)](../../relational-databases/system-dynamic-management-views/sys-dm-exec-dms-workers-transact-sql.md) 」を参照してください。|  
|compute_node_id|`int`|ワーカーが実行されているノード。|「 [Sys.dm_exec_compute_nodes &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-compute-nodes-transact-sql.md)」を参照してください。|  
|distribution_id|`int`|||  
|type|`nvarchar(32)`|このエントリが表す DMS ワーカースレッドの種類。|' DIRECT_CONVERTER '、' DIRECT_READER '、' FILE_READER '、' HASH_CONVERTER '、' HASH_READER '、' ROUNDROBIN_CONVERTER '、' EXPORT_READER '、' EXTERNAL_READER '、' EXTERNAL_WRITER '、' PARALLEL_COPY_READER '、' REJECT_WRITER '、' WRITER '|  
|status|`nvarchar(32)`|この手順の状態|' Pending '、' Running '、' Complete '、' Failed '、' UndoFailed '、' PendingCancel '、' 取り消し済み '、' 元に戻す '、' Aborted '|  
|bytes_per_sec|`bigint`|||  
|bytes_processed|`bigint`|||  
|rows_processed|`bigint`|||  
|start_time|`datetime`|ステップが実行を開始した時刻|小さいまたは現在の時刻に等しいが、このステップが属するクエリの end_compile_time 以上です。|  
|end_time|`datetime`|このステップの実行が完了した時刻。が取り消されたか、失敗しました。|小または現在の時刻に等しいか start_time 以上で、現在実行中またはキューに置かれているステップに対して NULL に設定されます。|  
|total_elapsed_time|`int`|クエリステップが実行されていた合計時間 (ミリ秒)|0 ~ end_time と start_time の差。 キューに登録されたステップの場合は0。|  
|cpu_time|`bigint`|||  
|query_time|`int`|||  
|buffers_available|`int`|||  
|dms_cpid|`int`|||  
|sql_spid|`int`|||  
|error_id|`nvarchar(36)`|||  
|source_info|`nvarchar(4000)`|||  
|destination_info|`nvarchar(4000)`|||  
|コマンドを使用します|`nvarchar(4000)`|||
|compute_pool_id|`int`|プールの一意の識別子。|

## <a name="see-also"></a>参照  
 [動的管理ビューを使用した PolyBase のトラブルシューティング](/previous-versions/sql/sql-server-2016/mt146389(v=sql.130))   
 [動的管理ビューと動的管理関数 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)   
 [Transact-sql&#41;&#40;データベース関連の動的管理ビュー ](../../relational-databases/system-dynamic-management-views/database-related-dynamic-management-views-transact-sql.md)  
  
