---
description: sys.dm_hadr_physical_seeding_stats (Transact-sql)
title: sys.dm_hadr_physical_seeding_stats (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/17/2021
ms.prod: sql
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- dm_hadr_physical_seeding_stats_TSQL
- sys.dm_hadr_physical_seeding_stats
- sys.dm_hadr_physical_seeding_stats_TSQL
- sys.dm_hadr_physical_seeding_stats
dev_langs:
- TSQL
helpviewer_keywords:
- Availability Groups [SQL Server], monitoring
- physical seeding
- sys.dm_hadr_physical_seeding_stats dynamic management view
author: cawrites
ms.author: chadam
ms.openlocfilehash: 89736fd8a97e9532f7729003e4750ac07e703735
ms.sourcegitcommit: 14f2051d329b69a7b5ff7bce1d136cf7f25bb219
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/02/2021
ms.locfileid: "106232266"
---
# <a name="sysdm_hadr_physical_seeding_stats-transact-sql"></a>sys.dm_hadr_physical_seeding_stats (Transact-sql)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

プライマリレプリカで sys.dm_hadr_physical_seeding_stats DMV を照会して、現在実行されている各シード処理の物理統計を確認します。

次の表では、さまざまな列の意味を定義します。  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|**local_physical_seeding_id**|**uniqueidentifier**|ローカルレプリカでのこのシード処理操作の一意識別子。|  
|**remote_physical_seeding_id**|**uniqueidentifier**|リモートレプリカでのこのシード処理操作の一意識別子。|  
|**local_database_id**|**int**|ローカルレプリカのデータベース ID。|  
|**local_database_name**|**nvarchar**|ローカルレプリカ上のデータベース名。 |
|**remote_machine_name**|**nvarchar**|リモートレプリカコンピューター名。|  
|**role_desc**|**nvarchar**|シード処理のロールの説明です。 (使用可能な値: Source、Destination、フォワーダー、ForwarderDestination)|
|**internal_state_desc**|**nvarchar**|ローカルレプリカの状態の説明。|
|**transfer_rate_bytes_per_second**|**bigint**|現在のシード処理転送速度 (バイト/秒)。|
|**transfered_size_bytes**|**bigint**|既に転送された合計バイト数。|
|**database_size_bytes**|**bigint**|シードされるデータベースの合計サイズ (バイト単位)。|
|**start_time_utc**|**datetime**|シード処理の開始時刻 (UTC)。|
|**end_time_utc**|**datetime**|シード処理の終了時刻 (UTC)。|
|**estimate_time_complete_utc**|**datetime**|インプロセスシード処理の完了時間の推定 (UTC)。|
|**total_disk_io_wait_time_ms**|**bigint**|発生したディスク IO 待機時間の合計 (ミリ秒単位)。|
|**total_network_wait_time_ms**|**bigint**|検出されたネットワーク IO 待機時間の合計 (ミリ秒単位)。|
|**failure_code**|**int**|シード処理操作の失敗コード。|
|**failure_message**|**nvarchar**|エラーコードに対応するメッセージ。|
|**failure_time_utc**|**datetime**|エラーの時刻 (UTC)。|
|**is_compression_enabled**|**bit**|シード処理操作に対して圧縮を有効にするかどうかを示します。|
  
## <a name="security"></a>セキュリティ  
  
### <a name="permissions"></a>アクセス許可  
 サーバーに対する VIEW SERVER STATE 権限が必要です。  
  
## <a name="see-also"></a>参照  
 [ページの自動修復 &#40;可用性グループ:データベース ミラーリング&#41;](../../sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring.md)   
 [suspect_pages &#40;Transact-sql&#41;](../../relational-databases/system-tables/suspect-pages-transact-sql.md)   
 [suspect_pages テーブルの管理 &#40;SQL Server&#41;](../../relational-databases/backup-restore/manage-the-suspect-pages-table-sql-server.md)  
  