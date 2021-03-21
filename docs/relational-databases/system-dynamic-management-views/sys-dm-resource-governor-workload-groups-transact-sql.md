---
description: sys.dm_resource_governor_workload_groups (Transact-SQL)
title: sys.dm_resource_governor_workload_groups (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/15/2020
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- sys.dm_resource_governor_workload_groups
- sys.dm_resource_governor_workload_groups_TSQL
- dm_resource_governor_workload_groups
- dm_resource_governor_workload_groups_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_resource_governor_workload_groups dynamic management view
ms.assetid: f63c4914-1272-43ef-b135-fe1aabd953e0
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: f9e8989568fdb55bb4db326799f4b824a4df7cb7
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104743302"
---
# <a name="sysdm_resource_governor_workload_groups-transact-sql"></a>sys.dm_resource_governor_workload_groups (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  ワークロードグループの統計と、ワークロードグループの現在のメモリ内構成を返します。 このビューを sys.dm_resource_governor_resource_pools と結合すると、リソース プール名を取得できます。  
  
> [!NOTE]  
>  またはからこれを呼び出すに [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] は [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] 、 **sys.dm_pdw_nodes_resource_governor_workload_groups** という名前を使用します。  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|group_id|**int**|ワークロードグループの ID。 NULL 値は許可されません。|  
|name|**sysname**|ワークロードグループの名前。 NULL 値は許可されません。|  
|pool_id|**int**|リソースプールの ID。 NULL 値は許可されません。|  
|external_pool_id|**int**|**適用対象**: 以降 [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 。<br /><br /> 外部リソースプールの ID。 NULL 値は許可されません。|  
|statistics_start_time|**datetime**|ワークロードグループの統計コレクションがリセットされた時刻。 NULL 値は許可されません。|  
|total_request_count|**bigint**|ワークロードグループ内の完了した要求の累積数。 NULL 値は許可されません。|  
|total_queued_request_count|**bigint**|GROUP_MAX_REQUESTS の制限に達した後にキューに登録された要求の累積数。 NULL 値は許可されません。|  
|active_request_count|**int**|現在の要求数。 NULL 値は許可されません。|  
|queued_request_count|**int**|現在キューに置かれている要求の数。 NULL 値は許可されません。|  
|total_cpu_limit_violation_count|**bigint**|CPU の制限を超えている要求の累積数。 NULL 値は許可されません。|  
|total_cpu_usage_ms|**bigint**|このワークロードグループによる累積 CPU 使用率 (ミリ秒単位)。 NULL 値は許可されません。|  
|max_request_cpu_time_ms|**bigint**|1つの要求に対する最大 CPU 使用率 (ミリ秒単位)。 NULL 値は許可されません。<br /><br /> **注:** これは、構成可能な設定である request_max_cpu_time_sec とは異なり、測定値です。 詳細については、「[CPU Threshold Exceeded イベント クラス](../../relational-databases/event-classes/cpu-threshold-exceeded-event-class.md)」を参照してください。|  
|blocked_task_count|**int**|現在ブロックされているタスクの数。 NULL 値は許可されません。|  
|total_lock_wait_count|**bigint**|発生したロック待機の累積数。 NULL 値は許可されません。|  
|total_lock_wait_time_ms|**bigint**|ロックが保持されるミリ秒単位の累積合計。 NULL 値は許可されません。|  
|total_query_optimization_count|**bigint**|このワークロードグループ内のクエリ最適化の累積数。 NULL 値は許可されません。|  
|total_suboptimal_plan_generation_count|**bigint**|メモリ不足のため、このワークロードグループで発生した、最適化されていないプランの世代の累積数。 NULL 値は許可されません。|  
|total_reduced_memgrant_count|**bigint**|クエリの最大サイズ制限に達したメモリ許可の累積数。 NULL 値は許可されません。|  
|max_request_grant_memory_kb|**bigint**|統計のリセット以降、1 つの要求に対する最大メモリ許可サイズ (KB 単位)。 NULL 値は許可されません。|  
|active_parallel_thread_count|**bigint**|並列スレッド使用の現在の数。 NULL 値は許可されません。|  
|importance|**sysname**|このワークロードグループ内の要求の相対的な重要度の現在の構成値。 重要度は次のいずれかです。 Medium が既定値 (低、中、高) です。<br /><br /> NULL 値は許可されません。|  
|request_max_memory_grant_percent|**int**|1つの要求に対する最大メモリ許可の現在の設定 (パーセンテージ)。 NULL 値は許可されません。|  
|request_max_cpu_time_sec|**int**|1 つの要求に対する最大 CPU 使用制限の現在の設定 (秒単位)。 NULL 値は許可されません。|  
|request_memory_grant_timeout_sec|**int**|1つの要求に対するメモリ許可のタイムアウト (秒単位) の現在の設定。 NULL 値は許可されません。|  
|group_max_requests|**int**|同時要求の最大数の現在の設定です。 NULL 値は許可されません。|  
|max_dop|**int**|ワークロードグループの並列処理の最大限度を構成しました。 既定値は0で、グローバル設定が使用されます。 NULL 値は許可されません。| 
|effective_max_dop|**int**|**適用対象**: 以降 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 。<br /><br />ワークロードグループの並列処理の有効な最大限度。 NULL 値は許可されません。| 
|total_cpu_usage_preemptive_ms|**bigint**|**適用対象**: 以降 [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 。<br /><br />ワークロードグループのプリエンプティブモードのスケジュール設定で使用された合計 CPU 時間 (ミリ秒単位)。 NULL 値は許可されません。<br /><br />[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 外部のコード (拡張ストアド プロシージャや分散クエリなど) を実行するには、スレッドを非プリエンプティブ スケジューラの制御外で実行する必要があります。 このとき、ワーカーはプリエンプティブ モードに切り替えられます。| 
|request_max_memory_grant_percent_numeric|**float**|**適用対象**: 以降 [!INCLUDE[sql-server-2019](../../includes/sssql19-md.md)] 。<br /><br />1つの要求に対する最大メモリ許可の現在の設定 (パーセンテージ)。 NULL 値は許可されません。| 
|pdw_node_id|**int**|**適用対象**: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 、 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]<br /><br /> このディストリビューションが配置されているノードの識別子。|  
  
## <a name="remarks"></a>解説  
 この動的管理ビューには、メモリ内の構成が表示されます。 格納されている構成メタデータを表示するには、 [sys.resource_governor_workload_groups &#40;transact-sql&#41;](../../relational-databases/system-catalog-views/sys-resource-governor-workload-groups-transact-sql.md) カタログビューを使用します。  
  
 が正常に実行されると、、、、、、、、、、、、 `ALTER RESOURCE GOVERNOR RESET STATISTICS` `statistics_start_time` `total_request_count` `total_queued_request_count` `total_cpu_limit_violation_count` `total_cpu_usage_ms` `max_request_cpu_time_ms` `total_lock_wait_count` `total_lock_wait_time_ms` `total_query_optimization_count` `total_suboptimal_plan_generation_count` `total_reduced_memgrant_count` および `max_request_grant_memory_kb` の各カウンターがリセットされます。 カウンター `statistics_start_time` は、現在のシステムの日付と時刻に設定され、その他のカウンターはゼロ (0) に設定されます。  
  
## <a name="permissions"></a>アクセス許可  
 `VIEW SERVER STATE` 権限が必要です。  
  
## <a name="see-also"></a>参照  
 [動的管理ビューと動的管理関数 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)   
 [sys.dm_resource_governor_resource_pools &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-transact-sql.md)   
 [sys.resource_governor_workload_groups &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-resource-governor-workload-groups-transact-sql.md)   
 [ALTER RESOURCE GOVERNOR &#40;Transact-SQL&#41;](../../t-sql/statements/alter-resource-governor-transact-sql.md)  
  
