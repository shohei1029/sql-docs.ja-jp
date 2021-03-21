---
description: sys.dm_exec_procedure_stats (Transact-sql)
title: sys.dm_exec_procedure_stats (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/03/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- sys.dm_exec_procedure_stats_TSQL
- dm_exec_procedure_stats_TSQL
- dm_exec_procedure_stats
- sys.dm_exec_procedure_stats
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_exec_procedure_stats dynamic management view
ms.assetid: ab8ddde8-1cea-4b41-a7e4-697e6ddd785a
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 0556a20a490aad3663144f8a34bab5ee54b65501
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104751802"
---
# <a name="sysdm_exec_procedure_stats-transact-sql"></a>sys.dm_exec_procedure_stats (Transact-sql)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  キャッシュされたストアド プロシージャの集計パフォーマンス統計を返します。 ビューは、キャッシュされたストアド プロシージャのプランごとに 1 行を返します。その行の有効期間はストアド プロシージャがキャッシュに残っている間になります。 つまり、ストアド プロシージャがキャッシュから削除されると、対応する行もこのビューから削除されます。 その時点で、**sys.dm_exec_query_stats** と同様にパフォーマンス統計 SQL トレース イベントが発生します。  
  
 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] では、動的管理ビューは、データベースの包含に影響する情報を公開することも、ユーザーがアクセスできる他のデータベースに関する情報を公開することもできません。 この情報を公開しないように、接続されたテナントに属していないデータを含むすべての行がフィルターで除外されます。  
  
> [!NOTE]
> データには完了したクエリだけが反映され、まだ処理中ではないため、 **sys.dm_exec_procedure_stats**  の結果は実行ごとに異なる場合があります。
> またはからこれを呼び出すに [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] は [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] 、 **sys.dm_pdw_nodes_exec_procedure_stats** という名前を使用します。 

  
|列名|データ型|説明|  
|-----------------|---------------|-----------------| 
|**database_id**|**int**|ストアド プロシージャが存在するデータベースの ID。|  
|**object_id**|**int**|ストアド プロシージャのオブジェクト ID 番号。|  
|**type**|**char(2)**|次のいずれかのオブジェクトの種類。<br /><br /> P = SQL ストアド プロシージャ<br /><br /> PC = アセンブリ (CLR) ストアド プロシージャ<br /><br /> X = 拡張ストアド プロシージャ|  
|**type_desc**|**nvarchar(60)**|オブジェクトの種類の説明。<br /><br /> SQL_STORED_PROCEDURE<br /><br /> CLR_STORED_PROCEDURE<br /><br /> EXTENDED_STORED_PROCEDURE|  
|**sql_handle**|**varbinary(64)**|これを使用すると、このストアドプロシージャ内から実行された **sys.dm_exec_query_stats** 内のクエリと相関させることができます。|  
|**plan_handle**|**varbinary(64)**|インメモリ プランの識別子。 この識別子は一時的なもので、プランがキャッシュに残っている間だけ一定の値になります。 この値は、 **sys.dm_exec_cached_plans** 動的管理ビューで使用できます。<br /><br /> ネイティブ コンパイル ストアド プロシージャがメモリ最適化テーブルに対してクエリを実行するときは、常に 0x000 になります。|  
|**cached_time**|**datetime**|ストアド プロシージャがキャッシュに追加された時刻。|  
|**last_execution_time**|**datetime**|前回ストアド プロシージャが実行された時刻。|  
|**execution_count**|**bigint**|ストアドプロシージャが最後にコンパイルされてから実行された回数。|  
|**total_worker_time**|**bigint**|コンパイル後にこのストアドプロシージャの実行で消費された CPU 時間の合計 (マイクロ秒単位)。<br /><br /> ネイティブ コンパイル ストアド プロシージャに関して、多くの実行が 1 ミリ秒未満である場合は、 **total_worker_time** は精度が高くない可能性があります。|  
|**last_worker_time**|**bigint**|ストアド プロシージャを前回実行したときに使用された CPU 時間 (マイクロ秒単位)。 <sup>1</sup>|  
|**min_worker_time**|**bigint**|このストアドプロシージャが1回の実行で使用した最小 CPU 時間 (マイクロ秒単位)。 <sup>1</sup>|  
|**max_worker_time**|**bigint**|このストアドプロシージャが1回の実行で使用した最大 CPU 時間 (マイクロ秒単位)。 <sup>1</sup>|  
|**total_physical_reads**|**bigint**|コンパイル後にこのストアドプロシージャの実行で行われた物理読み取りの合計数。<br /><br /> は常にメモリ最適化テーブルを照会する0になります。|  
|**last_physical_reads**|**bigint**|ストアドプロシージャが最後に実行されたときに実行された物理読み取りの数。<br /><br /> は常にメモリ最適化テーブルを照会する0になります。|  
|**min_physical_reads**|**bigint**|このストアドプロシージャの1回の実行で行われた物理読み取りの最小数。<br /><br /> は常にメモリ最適化テーブルを照会する0になります。|  
|**max_physical_reads**|**bigint**|このストアドプロシージャの1回の実行で行われた物理読み取りの最大数。<br /><br /> は常にメモリ最適化テーブルを照会する0になります。|  
|**total_logical_writes**|**bigint**|コンパイル後にこのストアドプロシージャの実行によって実行された論理書き込みの合計数。<br /><br /> は常にメモリ最適化テーブルを照会する0になります。|  
|**last_logical_writes**|**bigint**|プランが最後に実行されたときにダーティしたバッファープールページの数。 ページが既にダーティの場合 (変更された場合)、書き込みはカウントされません。<br /><br /> は常にメモリ最適化テーブルを照会する0になります。|  
|**min_logical_writes**|**bigint**|このストアドプロシージャの1回の実行で行われた論理書き込みの最小数。<br /><br /> は常にメモリ最適化テーブルを照会する0になります。|  
|**max_logical_writes**|**bigint**|このストアドプロシージャの1回の実行で行われた論理書き込みの最大数。<br /><br /> は常にメモリ最適化テーブルを照会する0になります。|  
|**total_logical_reads**|**bigint**|コンパイル後にこのストアドプロシージャの実行によって実行された論理読み取りの合計数。<br /><br /> は常にメモリ最適化テーブルを照会する0になります。|  
|**last_logical_reads**|**bigint**|ストアドプロシージャが最後に実行されたときに実行された論理読み取りの数。<br /><br /> は常にメモリ最適化テーブルを照会する0になります。|  
|**min_logical_reads**|**bigint**|このストアドプロシージャの1回の実行で行われた論理読み取りの最小数。<br /><br /> は常にメモリ最適化テーブルを照会する0になります。|  
|**max_logical_reads**|**bigint**|このストアドプロシージャの1回の実行で行われた論理読み取りの最大数。<br /><br /> は常にメモリ最適化テーブルを照会する0になります。|  
|**total_elapsed_time**|**bigint**|このストアドプロシージャの実行完了までの経過時間の合計 (マイクロ秒単位)。|  
|**last_elapsed_time**|**bigint**|このストアドプロシージャの前回の実行完了までの経過時間 (マイクロ秒単位)。|  
|**min_elapsed_time**|**bigint**|このストアドプロシージャの実行完了までの最小経過時間 (マイクロ秒単位)。|  
|**max_elapsed_time**|**bigint**|このストアドプロシージャの実行完了までの最大経過時間 (マイクロ秒単位)。|  
|**total_spills**|**bigint**|コンパイル後にこのストアドプロシージャの実行によって書き込まれたページの合計数。<br /><br /> **適用対象**: CU3 以降 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)]|  
|**last_spills**|**bigint**|ストアドプロシージャが最後に実行されたときに書き込まれたページの数。<br /><br /> **適用対象**: CU3 以降 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)]|  
|**min_spills**|**bigint**|このストアドプロシージャが1回の実行中に書き込まれたページの最小数。<br /><br /> **適用対象**: CU3 以降 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)]|  
|**max_spills**|**bigint**|このストアドプロシージャが1回の実行中に書き込まれたページの最大数。<br /><br /> **適用対象**: CU3 以降 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)]|  
|**pdw_node_id**|**int**|このディストリビューションが配置されているノードの識別子。<br /><br />**適用対象**: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 、 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]|  
|**total_page_server_reads**|**bigint**|コンパイル後にこのストアドプロシージャの実行によって実行されたページサーバー読み取りの合計数。<br /><br /> **適用対象**: Azure SQL Database ハイパースケール|  
|**last_page_server_reads**|**bigint**|最後にストアドプロシージャを実行したときに実行されたページサーバーの読み取り回数。<br /><br /> **適用対象**: Azure SQL Database ハイパースケール|  
|**min_page_server_reads**|**bigint**|このストアドプロシージャの1回の実行で行われた、ページサーバーの読み取りの最小数。<br /><br /> **適用対象**: Azure SQL Database ハイパースケール|  
|**max_page_server_reads**|**bigint**|このストアドプロシージャの1回の実行で行われた、ページサーバーの読み取りの最大数。<br /><br /> **適用対象**: Azure SQL Database ハイパースケール|  
  
 <sup>1</sup> ネイティブコンパイルストアドプロシージャの統計コレクションが有効になっている場合、ワーカー時間はミリ秒単位で収集されます。 クエリが 1 ミリ秒未満で実行された場合は、値は 0 になります。  
  
## <a name="permissions"></a>権限  

で [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] は、 `VIEW SERVER STATE` 権限が必要です。   
SQL Database Basic、S0、S1 のサービス目標、およびエラスティックプール内のデータベースについては、 [サーバー管理者](/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database) アカウントまたは [Azure Active Directory 管理者](/azure/azure-sql/database/authentication-aad-overview#administrator-structure) アカウントが必要です。 その他のすべての SQL Database サービスの目的で `VIEW DATABASE STATE` は、データベースで権限が必要になります。   
   
## <a name="remarks"></a>解説  
 ビュー内の統計は、ストアド プロシージャの実行が完了したときに更新されます。  
  
## <a name="examples"></a>例  
 次の例では、平均経過時間で識別される上位 10 個のストアド プロシージャに関する情報を返します。  
  
```sql  
SELECT TOP 10 d.object_id, d.database_id, OBJECT_NAME(object_id, database_id) 'proc name',   
    d.cached_time, d.last_execution_time, d.total_elapsed_time,  
    d.total_elapsed_time/d.execution_count AS [avg_elapsed_time],  
    d.last_elapsed_time, d.execution_count  
FROM sys.dm_exec_procedure_stats AS d  
ORDER BY [total_worker_time] DESC;  
```  
  
## <a name="see-also"></a>参照  
[実行関連の動的管理ビューおよび関数 &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/execution-related-dynamic-management-views-and-functions-transact-sql.md)   
[sys.dm_exec_sql_text &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-sql-text-transact-sql.md)   
[sys.dm_exec_query_plan &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-query-plan-transact-sql.md)    
[sys.dm_exec_query_stats &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql.md)    
[sys.dm_exec_trigger_stats &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-trigger-stats-transact-sql.md)    
[sys.dm_exec_cached_plans &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-cached-plans-transact-sql.md)    
  
