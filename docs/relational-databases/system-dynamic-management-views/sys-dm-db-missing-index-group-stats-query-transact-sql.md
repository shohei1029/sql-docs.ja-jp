---
description: sys.dm_db_missing_index_group_stats_query (Transact-sql)
title: sys.dm_db_missing_index_group_stats_query (Transact-sql)
ms.custom: ''
ms.date: 03/12/2021
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- sys.dm_db_missing_index_group_stats_query_TSQL
- sys.dm_db_missing_index_group_stats_query
- dm_db_missing_index_group_stats_query_TSQL
- dm_db_missing_index_group_stats_query
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_db_missing_index_group_stats_query dynamic management view
- missing indexes feature [SQL Server], sys.dm_db_missing_index_group_stats_query dynamic management view
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azuresqldb-current||>=sql-server-ver15||>=sql-server-linux-ver15||=azuresqldb-mi-current
ms.openlocfilehash: 01554b6abf8728ff317638f3e5e304b3823890ac
ms.sourcegitcommit: c242f423cc3b776c20268483cfab0f4be54460d4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/25/2021
ms.locfileid: "105551546"
---
# <a name="sysdm_db_missing_index_group_stats_query-transact-sql"></a>sys.dm_db_missing_index_group_stats_query (Transact-sql)
[!INCLUDE [SQL Server 2019 SQL Database](../../includes/applies-to-version/sqlserver2019-asdb-asdbmi.md)]

  空間インデックスを除く、欠落しているインデックスのグループから欠落インデックスが必要なクエリに関する情報を返します。 欠落したインデックスグループごとに複数のクエリが返される可能性があります。 欠落インデックスグループの1つに、同じインデックスを必要とする複数のクエリが存在する場合があります。
  
 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] では、動的管理ビューは、データベースの包含に影響する情報を公開することも、ユーザーがアクセスできる他のデータベースに関する情報を公開することもできません。 この情報を公開しないように、接続されたテナントに属していないデータを含むすべての行がフィルターで除外されます。  
    
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|**group_handle**|**int**|欠落インデックス グループの識別子。 この識別子はサーバー内で一意です。<br /><br /> 他の列では、グループ内のインデックスが欠落していると考えられる、すべてのクエリに関する情報が提供されます。<br /><br /> インデックス グループには、インデックスが 1 つだけ含まれます。<BR><BR>Sys.dm_db_missing_index_groups のに参加させることができ `index_group_handle` ます。 [](../../relational-databases/system-dynamic-management-views/sys-dm-db-missing-index-groups-transact-sql.md)|  
|**query_hash**|**binary (8)**|クエリで計算され、同様のロジックを持つクエリを識別するために使用される、バイナリのハッシュ値です。 クエリ ハッシュを使用して、リテラル値だけが異なるクエリの全体的なリソース使用率を決定できます。|  
|**query_plan_hash**|**binary (8)**|クエリ実行プランで計算され、同様のクエリ実行プランを識別するために使用される、バイナリのハッシュ値です。 クエリ プラン ハッシュを使用して、同様の実行プランを持つクエリの累積コストを確認できます。<br /><br /> ネイティブ コンパイル ストアド プロシージャがメモリ最適化テーブルに対してクエリを実行するときは、常に 0x000 になります。|  
|**last_sql_handle**|**varbinary(64)**|このインデックスが必要な最後にコンパイルされたステートメントのバッチまたはストアドプロシージャを一意に識別するトークンです。<BR><BR>`last_sql_handle` を使用すると、 [sys.dm_exec_sql_text](../../relational-databases/system-dynamic-management-views/sys-dm-exec-sql-text-transact-sql.md) 動的管理関数を呼び出すことによって、クエリの SQL テキストを取得できます。|
|**last_statement_start_offset**|**int**|SQL バッチ内でこのインデックスが必要な最後にコンパイルされたステートメントのバッチまたは保存されたオブジェクトのテキスト内で、行が示すクエリの開始位置 (バイト単位) を0から開始します。|
|**last_statement_end_offset**|**int**|SQL バッチ内でこのインデックスが必要になった最後にコンパイルされたステートメントのバッチまたは保存されたオブジェクトのテキスト内で、行が示すクエリの終了位置 (バイト単位) を0から開始します。|
|**last_statement_sql_handle**|**varbinary(64)**|このインデックスが必要な最後にコンパイルされたステートメントのバッチまたはストアドプロシージャを一意に識別するトークンです。<BR><BR>`last_statement_sql_handle`は、およびと共に `last_statement_start_offset` `last_statement_end_offset` 使用して、 [sys.dm_exec_sql_text](../../relational-databases/system-dynamic-management-views/sys-dm-exec-sql-text-transact-sql.md) 動的管理関数を呼び出すことによって、クエリの SQL テキストを取得することができます。<BR><BR>クエリのコンパイル時にクエリストアが有効になっていなかった場合、は0を返します。|
|**user_seeks**|**bigint**|グループ内の推奨インデックスを使用できたユーザー クエリによって発生したシーク数。|  
|**user_scans**|**bigint**|グループ内の推奨インデックスを使用できたユーザー クエリによって発生したスキャン数。|  
|**last_user_seek**|**datetime**|グループ内の推奨インデックスを使用できたユーザー クエリによって発生した前回のシークの日時。|  
|**last_user_scan**|**datetime**|グループ内の推奨インデックスを使用できたユーザー クエリによって発生した前回のスキャンの日時。|  
|**avg_total_user_cost**|**float**|グループ内のインデックスによって削減できたユーザー クエリの平均コスト。|  
|**avg_user_impact**|**float**|この欠落インデックス グループが実装されていた場合のユーザー クエリへの効果の平均パーセンテージ (%)。 この値は、この欠落インデックス グループが実装されていた場合に減少したクエリ コストの平均パーセンテージを示します。|  
|**system_seeks**|**bigint**|グループ内の推奨インデックスを使用できたシステム クエリ (Auto Stats クエリなど) によって発生したシーク数。 詳細については、「 [Auto Stats イベントクラス](../../relational-databases/event-classes/auto-stats-event-class.md)」を参照してください。|  
|**system_scans**|**bigint**|グループ内の推奨インデックスを使用できたシステム クエリによって発生したスキャン数。|  
|**last_system_seek**|**datetime**|グループ内の推奨インデックスを使用できたシステム クエリによって発生した前回のシステム シークの日時。|  
|**last_system_scan**|**datetime**|グループ内の推奨インデックスを使用できたシステム クエリによって発生した前回のシステム スキャンの日時。|  
|**avg_total_system_cost**|**float**|グループ内のインデックスによって削減できたシステム クエリの平均コスト。|  
|**avg_system_impact**|**float**|この欠落インデックス グループが実装されていた場合のシステム クエリへの効果の平均パーセンテージ (%)。 この値は、この欠落インデックス グループが実装されていた場合に減少したクエリ コストの平均パーセンテージを示します。|  
  
## <a name="remarks"></a>解説  
 によって返される情報 `sys.dm_db_missing_index_group_stats_query` は、すべてのクエリのコンパイルまたは再コンパイルではなく、すべてのクエリの実行によって更新されます。 使用状況の統計は保存されず、データベースエンジンが再起動されるまで保持されます。 使用状況の統計をサーバーの再利用後も保持する場合は、データベース管理者が欠落インデックスの情報のバックアップ コピーを定期的に作成する必要があります。 Sys.dm_os_sys_info の列を使用して、 `sqlserver_start_time` データベースエンジンの最後の起動時刻を検索します。 [](sys-dm-os-sys-info-transact-sql.md)   
 
  >[!NOTE]
  >この DMV の結果セットは、600行に制限されています。 各行には、欠落しているインデックスが1つ含まれています。 検出されたインデックスの数が600を超えている場合は、既存の不足しているインデックスに対処して、新しいインデックスを表示できるようにする必要があります。

## <a name="permissions"></a>アクセス許可  
 この動的管理ビューをクエリするには、VIEW SERVER STATE 権限、または VIEW SERVER STATE が暗黙的に与えられる権限が許可されている必要があります。  
  
## <a name="examples"></a>例  
 次の例は、動的管理ビューの使用方法を示して `sys.dm_db_missing_index_group_stats_query` います。  
  
  
### <a name="a-find-the-latest-query-text-for-the-top-10-highest-anticipated-improvements-for-user-queries"></a>A. ユーザークエリに関して予想される上位10件の改善のための最新のクエリテキストを検索する 
 次のクエリは、予想される累積向上率が最も高いものを降順で生成する10個の欠落インデックスについて、最後に記録されたクエリテキストを返します。  

```sql
SELECT TOP 10 
    SUBSTRING
    (
            sql_text.text,
            misq.last_statement_start_offset / 2 + 1,
            (
            CASE misq.last_statement_Start_offset
                WHEN -1 THEN datalength(sql_text.text)
                ELSE misq.last_statement_end_offset
            END - misq.last_statement_start_offset
            ) / 2 + 1
    ),
    misq.*
FROM sys.dm_db_missing_index_group_stats_query AS misq
CROSS APPLY sys.dm_exec_sql_text(last_sql_handle) AS sql_text
ORDER BY avg_total_user_cost * avg_user_impact * (user_seeks + user_scans) DESC; 
```
  
## <a name="see-also"></a>参照  
 [sys.dm_db_missing_index_columns &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-missing-index-columns-transact-sql.md)   
 [sys.dm_db_missing_index_details &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-missing-index-details-transact-sql.md)   
 [sys.dm_db_missing_index_groups &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-missing-index-groups-transact-sql.md)   
 [sys.dm_db_missing_index_group_stats &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-missing-index-group-stats-transact-sql.md)   
 [sys.dm_exec_sql_text &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-sql-text-transact-sql.md)   
 [CREATE INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/create-index-transact-sql.md)  
 [sys.dm_os_sys_info &#40;Transact-sql&#41;](sys-dm-os-sys-info-transact-sql.md)