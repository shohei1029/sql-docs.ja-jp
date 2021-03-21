---
description: sys.dm_exec_external_work (Transact-sql)
title: sys.dm_exec_external_work (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/10/2021
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- DM_EXEC_EXTERNAL_WORK
- DM_EXEC_EXTERNAL_WORK_TSQL
- SYS.DM_EXEC_EXTERNAL_WORK_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_exec_external_work management view
- dm_exec_external_work management view
- PolyBase,views
- PolyBase
ms.assetid: 7597d97b-1fde-4135-ac35-4af12968f300
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 6b68c597892bad6965b86f62fda3834bb9c069e1
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104752212"
---
# <a name="sysdm_exec_external_work-transact-sql"></a>sys.dm_exec_external_work (Transact-sql)
[!INCLUDE [sqlserver2016-asa-pdw](../../includes/applies-to-version/sqlserver2016-asa-pdw.md)]

各コンピューティングノード上のワーカーごとのワークロードに関する情報を返します。  
  
`sys.dm_exec_external_work`外部データソース (Hadoop、MongoDB など) と通信するためにスピンアップされた作業を識別するためにクエリを実行します。  
  
|列名|データ型|説明|Range|  
|-----------------|---------------|-----------------|-----------|  
|execution_id|`nvarchar(32)`|関連する PolyBase クエリの一意の識別子。|[Transact-sql&#41;&#40;sys.dm_exec_requests](../../relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql.md)の *request_ID* を参照してください。|  
|step_index|`int`|このワーカーが実行している要求。|[Transact-sql&#41;&#40;sys.dm_exec_requests](../../relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql.md)の *step_index* を参照してください。|  
|dms_step_index|`int`|このワーカーが実行している DMS プランのステップ。|「 [Sys.dm_exec_dms_workers &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-dms-workers-transact-sql.md)」を参照してください。|  
|compute_node_id|`int`|ワーカーが実行されているノード。|「 [Sys.dm_exec_compute_nodes &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-compute-nodes-transact-sql.md)」を参照してください。|  
|type|`nvarchar(60)`|外部作業の種類。|' ファイル分割 ' (Hadoop および Azure storage 用)<br/><br/>' ODBC データの分割 ' (他の外部データソース用) |  
|work_id|`int`|実際の分割の ID。|0以上。|  
|input_name|`nvarchar(4000)`|読み取る入力の名前|Hadoop または Azure storage を使用する場合のファイル名 (パスを含む)。 その他の外部データソースの場合、外部データソースの場所と外部テーブルの場所が連結されます。 `scheme://DataSourceHostname[:port]/[DatabaseName.][SchemaName.]TableName`|  
|read_location|`bigint`|読み取り場所のオフセット。| `0` ファイルのバイト数から1を引いた値までです。<br/><br/>`NULL` 非 Hadoop または非 Azure ストレージの場合。 |  
|read_command|`nvarchar(4000)`|外部データソースに送信されるクエリ。 [!INCLUDE [sssql19-md](../../includes/sssql19-md.md)] で導入されました。|クエリを表すテキスト。 Hadoop と Azure storage はを返し `NULL` ます。|
|bytes_processed|`bigint`|このワーカーによってデータを処理するために割り当てられた合計バイト数。 この値は、必ずしもクエリによって返されるデータの合計を表しているとは限りません |0以上。|  
|length|`bigint`|Split または Hadoop の HDFS ブロックの長さ|ユーザー定義可能。 既定値は64M です。|  
|status|`nvarchar(32)`|ワーカーの状態|保留中、処理中、完了、失敗、中止|  
|start_time|`datetime`|作業の開始||  
|end_time|`datetime`|作業の終了||  
|total_elapsed_time|`int`|合計時間 (ミリ秒)||
|compute_pool_id|`int`|ワーカーが実行されているプールの一意の識別子。 SQL Server ビッグデータクラスターにのみ適用されます。 「 [Sys.dm_exec_compute_pools (transact-sql)](sys-dm-exec-compute-pools.md)」を参照してください。|`0`Windows および Linux では、はを返し [!INCLUDE [ssnoversion-md](../../includes/ssnoversion-md.md)] ます。|

## <a name="see-also"></a>参照  
 [動的管理ビューを使用した PolyBase のトラブルシューティング](/previous-versions/sql/sql-server-2016/mt146389(v=sql.130))   
 [動的管理ビューと動的管理関数 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)   
 [Transact-sql&#41;&#40;データベース関連の動的管理ビュー ](../../relational-databases/system-dynamic-management-views/database-related-dynamic-management-views-transact-sql.md)  
  
