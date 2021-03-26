---
description: sys.dm_db_missing_index_group_stats (Transact-SQL)
title: sys.dm_db_missing_index_group_stats (Transact-SQL)
ms.custom: ''
ms.date: 03/12/2021
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- sys.dm_db_missing_index_group_stats_TSQL
- sys.dm_db_missing_index_group_stats
- dm_db_missing_index_group_stats_TSQL
- dm_db_missing_index_group_stats
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_db_missing_index_group_stats dynamic management view
- missing indexes feature [SQL Server], sys.dm_db_missing_index_group_stats dynamic management view
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: d2271f7b1b90f34d25370ed156348892b0a4ddb5
ms.sourcegitcommit: c242f423cc3b776c20268483cfab0f4be54460d4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/25/2021
ms.locfileid: "105551497"
---
# <a name="sysdm_db_missing_index_group_stats-transact-sql"></a>sys.dm_db_missing_index_group_stats (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  空間インデックスを除く、欠落インデックス グループに関する概要を返します。  
  
 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] では、動的管理ビューは、データベースの包含に影響する情報を公開することも、ユーザーがアクセスできる他のデータベースに関する情報を公開することもできません。 この情報を公開しないように、接続されたテナントに属していないデータを含むすべての行がフィルターで除外されます。  
    
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|**group_handle**|**int**|欠落インデックス グループの識別子。 この識別子はサーバー内で一意です。<br /><br /> 他の列では、グループ内のインデックスが欠落していると考えられる、すべてのクエリに関する情報が提供されます。<br /><br /> インデックス グループには、インデックスが 1 つだけ含まれます。<BR><BR>Sys.dm_db_missing_index_groups のに参加させることができ `index_group_handle` ます。 [](../../relational-databases/system-dynamic-management-views/sys-dm-db-missing-index-groups-transact-sql.md)|  
|**unique_compiles**|**bigint**|この欠落インデックス グループによって影響を受けるコンパイルおよび再コンパイルの数。 多くの異なるクエリでコンパイルおよび再コンパイルが行われるほど、この列の値は大きくなります。|  
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
 によって返される情報 `sys.dm_db_missing_index_group_stats` は、すべてのクエリのコンパイルまたは再コンパイルではなく、すべてのクエリの実行によって更新されます。 使用状況の統計は保存されず、データベースエンジンが再起動されるまで保持されます。 使用状況の統計をサーバーの再利用後も保持する場合は、データベース管理者が欠落インデックスの情報のバックアップ コピーを定期的に作成する必要があります。 Sys.dm_os_sys_info の列を使用して、 `sqlserver_start_time` データベースエンジンの最後の起動時刻を検索します。 [](sys-dm-os-sys-info-transact-sql.md)   

  >[!NOTE]
  >この DMV の結果セットは、600行に制限されています。 各行には、欠落しているインデックスが1つ含まれています。 検出されたインデックスの数が600を超えている場合は、既存の不足しているインデックスに対処して、新しいインデックスを表示できるようにする必要があります。

 欠落インデックスグループの1つに、同じインデックスを必要とする複数のクエリが存在する場合があります。 この DMV で特定のインデックスを必要とする個々のクエリの詳細については、「 [sys.dm_db_missing_index_group_stats_query](../../relational-databases/system-dynamic-management-views/sys-dm-db-missing-index-group-stats-query-transact-sql.md)」を参照してください。
  
## <a name="permissions"></a>アクセス許可  
 この動的管理ビューをクエリするには、VIEW SERVER STATE 権限、または VIEW SERVER STATE が暗黙的に与えられる権限が許可されている必要があります。  
  
## <a name="examples"></a>例  
 次の例は、動的管理ビューの使用方法を示して `sys.dm_db_missing_index_group_stats` います。  
  
### <a name="a-find-the-10-missing-indexes-with-the-highest-anticipated-improvement-for-user-queries"></a>A. ユーザークエリの予測向上率が高い10個の欠落インデックスを検索する  
 次のクエリでは、ユーザー クエリで最も高い累積のパフォーマンス向上を見込むことができる、上位 10 個の不足しているインデックスを降順で特定します。  
  
```sql
SELECT TOP 10 *  
FROM sys.dm_db_missing_index_group_stats  
ORDER BY avg_total_user_cost * avg_user_impact * (user_seeks + user_scans)DESC;  
```  
  
### <a name="b-find-the-individual-missing-indexes-and-their-column-details-for-a-particular-missing-index-group"></a>B. 特定の欠落インデックス グループについて、個別の欠落インデックスとその列の詳細を検索する  
 次のクエリでは、特定の欠落インデックス グループを構成しているインデックスを特定し、その列の詳細を表示します。 この例では、不足しているインデックス `group_handle` は24です。  
  
```sql
SELECT migs.group_handle, mid.*  
FROM sys.dm_db_missing_index_group_stats AS migs  
INNER JOIN sys.dm_db_missing_index_groups AS mig  
    ON (migs.group_handle = mig.index_group_handle)  
INNER JOIN sys.dm_db_missing_index_details AS mid  
    ON (mig.index_handle = mid.index_handle)  
WHERE migs.group_handle = 24;  
```  
  
 このクエリを実行すると、インデックスが欠落しているデータベース、スキーマ、テーブルの名前が返されます。 また、インデックス キーに使用される列の名前も返されます。 CREATE INDEX DDL ステートメントを記述して欠落インデックスを実装する場合は、CREATE INDEX ステートメントの ON 句で最初に等値列を指定し、次に非等値列を指定し \<*table_name*> ます。 付加列は、CREATE INDEX ステートメントの INCLUDE 句で指定します。 等値の列の有効な順序を決定するには、選択度の最も高い列を左の先頭に指定し、選択度が高い順に並べます。  
  
## <a name="see-also"></a>参照  
 [sys.dm_db_missing_index_columns &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-missing-index-columns-transact-sql.md)   
 [sys.dm_db_missing_index_details &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-missing-index-details-transact-sql.md)   
 [sys.dm_db_missing_index_groups &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-missing-index-groups-transact-sql.md)   
 [sys.dm_db_missing_index_group_stats_query &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-missing-index-group-stats-query-transact-sql.md)   
 [CREATE INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/create-index-transact-sql.md)  
 [sys.dm_os_sys_info &#40;Transact-sql&#41;](sys-dm-os-sys-info-transact-sql.md)  
  
