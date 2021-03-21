---
description: sys.dm_resource_governor_resource_pools (Transact-sql)
title: sys.dm_resource_governor_resource_pools (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 04/24/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- sys.dm_resource_governor_resource_pools_TSQL
- dm_resource_governor_resource_pools_TSQL
- sys.dm_resource_governor_resource_pools
- dm_resource_governor_resource_pools
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_resource_governor_resource_pools dynamic management view
ms.assetid: 9bfc926e-d8bc-40f8-9229-ab1f8a1e69c5
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 1088356349cd94e96a5952667b4e9436a4fa4ddf
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104750652"
---
# <a name="sysdm_resource_governor_resource_pools-transact-sql"></a>sys.dm_resource_governor_resource_pools (Transact-sql)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  リソース プールの現在の状態、現在の構成、および統計に関する情報を返します。  
  
> [!NOTE]  
>  またはからこれを呼び出すに [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] は [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] 、 **sys.dm_pdw_nodes_resource_governor_resource_pools** という名前を使用します。  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|pool_id|**int**|リソースプールの ID。 NULL 値は許可されません。|  
|name|**sysname**|共有リソースの名前。 NULL 値は許可されません。|  
|statistics_start_time|**datetime**|このプールの統計がリセットされた時刻。 NULL 値は許可されません。|  
|total_cpu_usage_ms|**bigint**|リソースガバナー統計がリセットされた後の累積 CPU 使用率 (ミリ秒単位)。 NULL 値は許可されません。|  
|cache_memory_kb|**bigint**|現在の合計キャッシュ メモリ使用量 (KB 単位)。 NULL 値は許可されません。|  
|compile_memory_kb|**bigint**|現在の合計メモリ流用量 (KB 単位)。 この量の大半はコンパイルと最適化に使用されますが、他のメモリ ユーザーに使用されている可能性もあります。 NULL 値は許可されません。|  
|used_memgrant_kb|**bigint**|メモリ許可からの現在の (流用された) メモリの合計。 NULL 値は許可されません。|  
|total_memgrant_count|**bigint**|このリソースプール内のメモリ許可の累積数。 NULL 値は許可されません。|  
|total_memgrant_timeout_count|**bigint**|このリソースプールにおけるメモリ許可のタイムアウトの累積数。 NULL 値は許可されません。|  
|active_memgrant_count|**int**|メモリ許可の現在の数。 NULL 値は許可されません。|  
|active_memgrant_kb|**bigint**|現在のメモリ許可の合計 (kb 単位)。 NULL 値は許可されません。|  
|memgrant_waiter_count|**int**|メモリ許可で現在保留中のクエリの数。 NULL 値は許可されません。|  
|max_memory_kb|**bigint**|リソースプールが持つことができるメモリの最大量 (kb 単位)。 これは、現在の設定とサーバーの状態に基づいています。 NULL 値は許可されません。|  
|used_memory_kb|**bigint**|リソースプールに使用されるメモリの量 (kb 単位)。 NULL 値は許可されません。|  
|target_memory_kb|**bigint**|リソースプールが達成しようとしている目標メモリ量 (kb 単位)。 これは、現在の設定とサーバーの状態に基づいています。 NULL 値は許可されません。|  
|out_of_memory_count|**bigint**|リソースガバナーまたは統計がリセットされた後にプールで失敗したメモリ割り当ての数。 NULL 値は許可されません。|  
|min_cpu_percent|**int**|CPU の競合がある場合に、リソースプール内のすべての要求に対して保証される平均 CPU 帯域幅の現在の構成。 NULL 値は許可されません。|  
|max_cpu_percent|**int**|CPU の競合がある場合に、リソースプールのすべての要求で許容される最大平均 CPU 帯域幅の現在の構成。 NULL 値は許可されません。|  
|min_memory_percent|**int**|メモリの競合がある場合に、リソースプール内のすべての要求に対して保証されるメモリの量の現在の構成。 これは、他のリソース プールとは共有されません。 NULL 値は許可されません。|  
|max_memory_percent|**int**|このリソースプール内の要求で使用できる合計サーバーメモリの割合の現在の構成。 NULL 値は許可されません。|  
|cap_cpu_percent|**int**|**適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降。<br /><br /> リソースプール内のすべての要求が受信する CPU 帯域幅のハードキャップ。 CPU 帯域幅の最大レベルを指定されたレベルに制限します。 value の許容範囲は 1 ～ 100 です。 NULL 値は許可されません。|  
|min_iops_per_volume|**int**|**適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。<br /><br /> このプールのディスクボリューム設定ごとの1秒あたりの最小 IO (IOPS)。 NULL 値が許可されます。 リソースプールに IO が適用されていない場合は Null です。 つまり、リソースプールの MIN_IOPS_PER_VOLUME と MAX_IOPS_PER_VOLUME の設定は0です。|  
|max_iops_per_volume|**int**|**適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。<br /><br /> このプールのディスクボリューム設定ごとの1秒あたりの最大 IO 数 (IOPS)。 NULL 値が許可されます。 リソースプールに IO が適用されていない場合は Null です。 つまり、リソースプールの MIN_IOPS_PER_VOLUME と MAX_IOPS_PER_VOLUME の設定は0です。|  
|read_io_queued_total|**int**|**適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。<br /><br /> リソース ガバナーがリセットされた後にエンキューされた読み取り IO の合計。 NULL 値が許可されます。 リソースプールに IO が適用されていない場合は Null です。 つまり、リソースプールの MIN_IOPS_PER_VOLUME と MAX_IOPS_PER_VOLUME の設定は0です。|  
|read_io_issued_total|**int**|**適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。<br /><br /> リソースガバナー統計がリセットされた後に発行された読み取り Io の合計。 NULL 値が許可されます。 リソースプールに IO が適用されていない場合は Null です。 つまり、リソースプールの MIN_IOPS_PER_VOLUME と MAX_IOPS_PER_VOLUME の設定は0です。|  
|read_io_completed_total|**int**|**適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。<br /><br /> リソース ガバナー統計がリセットされた後に完了した読み取り IO の合計。 NULL 値は許可されません。|  
|read_io_throttled_total|**int**|リソースガバナー統計がリセットされた後に調整された読み取り Io の合計。 NULL 値が許可されます。 リソースプールに IO が適用されていない場合は Null です。 つまり、リソースプールの MIN_IOPS_PER_VOLUME と MAX_IOPS_PER_VOLUME の設定は0です。|  
|read_bytes_total|**bigint**|**適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。<br /><br /> リソース ガバナー統計がリセットされた後に読み取られたバイト数の合計。 NULL 値は許可されません。|  
|read_io_stall_total_ms|**bigint**|**適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。<br /><br /> 読み取り IO の到着から完了までの合計時間 (ミリ秒単位)。 NULL 値は許可されません。 |  
|read_io_stall_queued_ms|**bigint**|**適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。<br /><br /> 読み取り IO の到着から問題までの合計時間 (ミリ秒)。 NULL 値が許可されます。 リソースプールに IO が適用されていない場合は Null です。 つまり、リソースプールの MIN_IOPS_PER_VOLUME と MAX_IOPS_PER_VOLUME の設定は0です。<br /><br /> プールの IO 設定が待機時間の原因であるかどうかを判断するには、 **read_io_stall_total_ms** から **read_io_stall_queued_ms** を減算します。|  
|write_io_queued_total|**int**|**適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。<br /><br /> リソースガバナー統計がリセットされた後にエンキューされた書き込み Io の合計。 NULL 値が許可されます。 リソースプールに IO が適用されていない場合は Null です。 つまり、リソースプールの MIN_IOPS_PER_VOLUME と MAX_IOPS_PER_VOLUME の設定は0です。|  
|write_io_issued_total|**int**|**適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。<br /><br /> リソースガバナー統計がリセットされた後に発行された書き込み Io の合計。 NULL 値が許可されます。 リソースプールに IO が適用されていない場合は Null です。 つまり、リソースプールの MIN_IOPS_PER_VOLUME と MAX_IOPS_PER_VOLUME の設定は0です。|  
|write_io_completed_total|**int**|**適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。<br /><br /> リソース ガバナー統計がリセットされた後に完了した書き込み IO の合計。 Null 値はありません|  
|write_io_throttled_total|**int**|**適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。<br /><br /> リソースガバナー統計がリセットされた後に調整された書き込み Io の合計。 Null 値はありません|  
|write_bytes_total|**bigint**|**適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。<br /><br /> リソース ガバナー統計がリセットされた後に書き込まれたバイト数の合計。 NULL 値は許可されません。|  
|write_io_stall_total_ms|**bigint**|**適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。<br /><br /> 書き込み IO の到着から完了までの合計時間 (ミリ秒単位)。 NULL 値は許可されません。 |  
|write_io_stall_queued_ms|**bigint**|**適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。<br /><br /> 書き込み IO の到着から発行までの合計時間 (ミリ秒単位)。 NULL 値が許可されます。 リソースプールに IO が適用されていない場合は Null です。 つまり、リソースプールの MIN_IOPS_PER_VOLUME と MAX_IOPS_PER_VOLUME の設定は0です。<br /><br /> これは、IO リソースガバナンスで導入された遅延です。|  
|io_issue_violations_total|**int**|**適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。<br /><br /> IO 発行違反の合計。 つまり、IO の問題の発生率が予約されたレートを下回った回数です。 NULL 値が許可されます。 リソースプールに IO が適用されていない場合は Null です。 つまり、リソースプールの MIN_IOPS_PER_VOLUME と MAX_IOPS_PER_VOLUME の設定は0です。|  
|io_issue_delay_total_ms|**bigint**|**適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。<br /><br /> スケジュールされた問題と IO の実際の問題との間の合計時間 (ミリ秒単位)。 NULL 値が許可されます。 リソースプールに IO が適用されていない場合は Null です。 つまり、リソースプールの MIN_IOPS_PER_VOLUME と MAX_IOPS_PER_VOLUME の設定は0です。|  
|pdw_node_id|**int**|**適用対象**: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 、 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]<br /><br /> このディストリビューションが配置されているノードの識別子。|  
  
## <a name="remarks"></a>解説  
 Resource Governor ワークロードグループと Resource Governor リソースプールには、多対一のマッピングがあります。 このため、多くのリソース プール統計が、ワークロード グループ統計から派生しています。  
  
 この動的管理ビューには、メモリ内の構成が表示されます。 保存されている構成メタデータを表示するには、sys.resource_governor_resource_pools カタログビューを使用します。  
  
## <a name="permissions"></a>アクセス許可  
 VIEW SERVER STATE 権限が必要です。  
  
## <a name="see-also"></a>参照  
 [動的管理ビューと動的管理関数 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)   
 [sys.dm_resource_governor_workload_groups &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-transact-sql.md)   
 [sys.resource_governor_resource_pools &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-resource-governor-resource-pools-transact-sql.md)   
 [ALTER RESOURCE GOVERNOR &#40;Transact-SQL&#41;](../../t-sql/statements/alter-resource-governor-transact-sql.md)  
  
  



