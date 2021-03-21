---
description: sys.dm_exec_distributed_request_steps (Transact-sql)
title: sys.dm_exec_distributed_request_steps (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- SYS.DM_EXEC_DISTRIBUTED_REQUEST_STEPS_TSQL
- DM_EXEC_DISTRIBUTED_REQUEST_STEPS_TSQL
- DM_EXEC_DISTRIBUTED_REQUEST_STEPS
dev_langs:
- TSQL
helpviewer_keywords:
- PolyBase,views
- PolyBase
- dm_exec_distributed_request_steps
- sys.dm_exec_distributed_request_steps management view
ms.assetid: 1954541d-b716-4e03-8fcc-7022f428e01d
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: a9efbac50896afca30b8341b839bcd2ccd8f0c35
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104739112"
---
# <a name="sysdm_exec_distributed_request_steps-transact-sql"></a>sys.dm_exec_distributed_request_steps (Transact-sql)
[!INCLUDE [sqlserver2016-asa-pdw](../../includes/applies-to-version/sqlserver2016-asa-pdw.md)]

  指定された PolyBase 要求またはクエリを構成するすべてのステップに関する情報を保持します。 クエリステップごとに1行が表示されます。  
  
|列名|データ型|説明|Range|  
|-----------------|---------------|-----------------|-----------|  
|execution_id|**int**|execution_id と step_index このビューのキーを構成します。 要求に関連付けられている一意の数値 id。|[Sys.dm_exec_requests &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql.md)の ID を参照してください。|  
|step_index|**int**|要求を構成する一連の手順におけるこのステップの位置。|n 個の手順を含む要求の場合は 0 ~ (n-1)。|  
|operation_type|**nvarchar(128)**|このステップで表される操作の種類。|' MoveOperation '、' OnOperation '、' RandomIDOperation '、' RemoteOperation '、' Returnoperation) '、' ShuffleMoveOperation '、' TempTablePropertiesOperation '、' DropDiagnosticsNotifyOperation '、' HadoopShuffleOperation '、' HadoopBroadCastOperation '、' HadoopRoundRobinOperation '|  
|distribution_type|**nvarchar(32)**|ステップが実行されている場所です。|' AllComputeNodes '、' AllDistributions '、' CompuSubsetNodes '、' Distribution '、' Alldistributions '、' '、' SubsetDistributions '、' 未指定 '。|  
|location_type|**nvarchar(32)**|ステップが実行されている場所です。|' Compute '、' Head '、または ' DMS '。 すべてのデータ移動手順は、"DMS" を示しています。|  
|status|**nvarchar(32)**|この手順の状態|' Pending '、' Running '、' Complete '、' Failed '、' UndoFailed '、' PendingCancel '、' 取り消し済み '、' 元に戻す '、' Aborted '|  
|error_id|**nvarchar (36)**|このステップに関連付けられているエラーの一意の id (存在する場合)|「 [Sys.dm_exec_compute_node_errors &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-compute-node-errors-transact-sql.md)の id」を参照してください。エラーが発生しなかった場合は NULL になります。|  
|start_time|**datetime**|ステップが実行を開始した時刻|小さいまたは現在の時刻に等しいが、このステップが属するクエリの end_compile_time 以上です。|  
|end_time|**datetime**|このステップの実行が完了した時刻。が取り消されたか、失敗しました。|小または現在の時刻に等しいか start_time 以上で、現在実行中またはキューに置かれているステップに対して NULL に設定されます。|  
|total_elapsed_time|**int**|クエリステップが実行されていた合計時間 (ミリ秒)|0 ~ end_time と start_time の差。 キューに登録されたステップの場合は0。|  
|row_count|**bigint**|この要求によって変更または返された行の合計数|データが変更または返されなかったステップの場合は0。それ以外の場合は、影響を受けた行の数。 DMS ステップの場合は-1 に設定します。|  
|コマンドを使用します|nvarchar(4000)|このステップのコマンドの完全なテキストを保持します。|ステップの任意の有効な要求文字列。 4000文字を超える場合は切り捨てられます。|  
  
## <a name="see-also"></a>参照  
 [動的管理ビューを使用した PolyBase のトラブルシューティング](/previous-versions/sql/sql-server-2016/mt146389(v=sql.130))   
 [動的管理ビューと動的管理関数 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)   
 [Transact-sql&#41;&#40;データベース関連の動的管理ビュー ](../../relational-databases/system-dynamic-management-views/database-related-dynamic-management-views-transact-sql.md)  
  
