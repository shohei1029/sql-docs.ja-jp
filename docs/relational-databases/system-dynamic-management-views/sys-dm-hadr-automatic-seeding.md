---
description: sys.dm_hadr_automatic_seeding (Transact-sql)
title: sys.dm_hadr_automatic_seeding (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/01/2021
ms.prod: sql
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- dm_hadr_automatic_seeding_TSQL
- sys.dm_hadr_automatic_seeding
- sys.dm_hadr_automatic_seeding_TSQL
- dm_hadr_automatic_seeding
dev_langs:
- TSQL
helpviewer_keywords:
- Availability Groups [SQL Server], monitoring
- automatic seeding
- sys.dm_hadr_automatic_seeding dynamic management view
ms.assetid: d7840adf-4a1b-41ac-bc94-102c07ad1c79
author: cawrites
ms.author: chadam
ms.openlocfilehash: b53e5077ee42357afc66426044d1caf8f4469c1c
ms.sourcegitcommit: 14f2051d329b69a7b5ff7bce1d136cf7f25bb219
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/02/2021
ms.locfileid: "106232269"
---
# <a name="sysdm_hadr_automatic_seeding-transact-sql"></a>sys.dm_hadr_automatic_seeding (Transact-sql)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

プライマリレプリカで sys.dm_hadr_automatic_seeding クエリを実行して、自動シード処理プロセスの状態を確認します。 このビューは、シード処理プロセスごとに 1 つの行を返します。  
  
次の表では、さまざまな列の意味を定義します。  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|**start_time**|**datetime**|操作が開始された時刻。|
|**completion_time**|**datetime**|操作が完了した時刻 (実行中の場合は null)。|  
|**ag_id**|**uniqueidentifier**|各可用性グループの一意の ID。|  
|**ag_db_id**|**uniqueidentifier**|使用可能なグループ内の各データベースの一意の ID。|  
|**ag_remote_replica_id**|**uniqueidentifier**|このシード処理操作に含まれる、他のレプリカの一意の ID。|
|**operation_id**|**uniqueidentifier**|このシード処理操作の一意識別子。|  
|**is_source**|**bit**|このレプリカがシード処理操作のソース (プライマリ) かどうかを示す値を取得します。|
|**current_state**|**bit**|操作の現在のシード処理状態を取得します。|
|**performed_seeding**|**bit**|シード処理用のデータベースストリーミングが初期化されます。|
|**failure_state**|**int**|操作が失敗した理由を取得します。|
|**failure_state_desc**|**ncharvar**|操作に失敗した説明を取得します。|
|**error_code**|**int**|シード処理中に検出された SQL エラーコードを取得します。|
|**number_of_attempts**|**int**|このシード処理操作が試行された回数を取得します。|


## <a name="security"></a>セキュリティ  
  
### <a name="permissions"></a>アクセス許可  
 サーバーに対する VIEW SERVER STATE 権限が必要です。  
  
## <a name="see-also"></a>参照  
 [ページの自動修復 &#40;可用性グループ:データベース ミラーリング&#41;](../../sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring.md)   
 [suspect_pages &#40;Transact-sql&#41;](../../relational-databases/system-tables/suspect-pages-transact-sql.md)   
 [suspect_pages テーブルの管理 &#40;SQL Server&#41;](../../relational-databases/backup-restore/manage-the-suspect-pages-table-sql-server.md)  
  
