---
description: sys.dm_db_column_store_row_group_physical_stats (Transact-sql)
title: sys.dm_db_column_store_row_group_physical_stats (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 05/05/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- sys.dm_db_column_store_row_group_physical_stats_TSQL
- sys.dm_db_column_store_row_group_physical_stats
- dm_db_column_store_row_group_physical_stats
- dm_db_column_store_row_group_physical_stats_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- dm_db_column_store_row_group_physical_stats
- sys.dm_db_column_store_row_group_physical_stats dynamic management view
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 6345892c246c0ffc693a28e67833068c65243672
ms.sourcegitcommit: 295b9dfc758471ef7d238a2b0f92f93e34acbb1b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106054714"
---
# <a name="sysdm_db_column_store_row_group_physical_stats-transact-sql"></a>sys.dm_db_column_store_row_group_physical_stats (Transact-sql)


[!INCLUDE [sqlserver2016-asdb-asdbmi](../../includes/applies-to-version/sqlserver2016-asdb-asdbmi.md)]

現在のデータベース内のすべての列ストアインデックスについて、現在の行グループレベルの情報を提供します。  

これに [より、transact-sql&#41;&#40;sys.column_store_row_groups ](../../relational-databases/system-catalog-views/sys-column-store-row-groups-transact-sql.md)カタログビューが拡張されます。  

|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|**object_id**|**int**|基になるテーブルの ID。|  
|**index_id**|**int**|*Object_id* テーブルのこの列ストアインデックスの ID。|  
|**partition_number**|**int**|*Row_group_id* を保持するテーブルパーティションの ID。 Partition_number を使用して、この DMV を sys パーティションに参加させることができます。|  
|**row_group_id**|**int**|この行グループの ID。 パーティションテーブルの場合、値はパーティション内で一意です。<br /><br /> インメモリテールの場合は-1。|  
|**delta_store_hobt_id**|**bigint**|デルタストアの行グループの hobt_id。<br /><br /> 行グループがデルタストアに存在しない場合は NULL になります。<br /><br /> メモリ内のテーブルの末尾の場合は NULL です。|  
|**状態**|**tinyint**|*State_description* に関連付けられた ID 番号。<br /><br /> 0 = 非表示<br /><br /> 1 = OPEN <br /><br /> 2 = CLOSED <br /><br /> 3 = 圧縮<br /><br /> 4 = 廃棄標識<br /><br /> 圧縮は、インメモリテーブルに適用される唯一の状態です。|  
|**state_desc**|**nvarchar(60)**|行グループの状態の説明:<br /><br /> 0-非表示-ビルドされている行グループ。 次に例を示します。 <br />列ストアの行グループは、データの圧縮中は非表示になります。 圧縮が完了すると、メタデータスイッチは列ストアの行グループの状態を "非表示" から "圧縮済み" に変更し、デルタストアの行グループの状態を "終了" から "廃棄済み" に変更します。<br /><br /> 1-OPEN-新しい行を受け入れる、デルタストアの行グループ。 開いている行グループは、行ストア形式のままであり、列ストア形式に圧縮されていません。<br /><br /> 2-CLOSED-最大行数を含むデルタストア内の行グループ。これは、組ムーバープロセスによって列ストアに圧縮されるのを待機しています。<br /><br /> 3-圧縮-列ストア圧縮で圧縮され、列ストアに格納される行グループ。<br /><br /> 4-TOMBSTONE-以前はデルタストア内にあった行グループは使用されなくなりました。|  
|**total_rows**|**bigint**|行グループに物理的に格納されている行の数。 圧縮された行グループの場合。 削除済みとしてマークされている行が含まれます。|  
|**deleted_rows**|**bigint**|削除対象としてマークされている圧縮行グループに物理的に格納されている行の数。<br /><br /> デルタ ストア内の行グループの場合は 0 です。|  
|**size_in_bytes**|**bigint**|この行グループ内のすべてのページの合計サイズ (バイト単位)。 このサイズには、メタデータまたは共有辞書を格納するために必要なサイズは含まれていません。|  
|**trim_reason**|**tinyint**|圧縮された行グループをトリガーした理由は、行の最大数よりも少なくなっています。<br /><br /> 0-UNKNOWN_UPGRADED_FROM_PREVIOUS_VERSION<br /><br /> 1-NO_TRIM<br /><br /> 2-BULKLOAD<br /><br /> 3-再組織<br /><br /> 4-DICTIONARY_SIZE<br /><br /> 5-MEMORY_LIMITATION<br /><br /> 6-RESIDUAL_ROW_GROUP<br /><br /> 7-STATS_MISMATCH<br /><br /> 8-スピルオーバー<br /><br /> 9-AUTO_MERGE|  
|**trim_reason_desc**|**nvarchar(60)**|*Trim_reason* の説明。<br /><br /> 0-UNKNOWN_UPGRADED_FROM_PREVIOUS_VERSION: 以前のバージョンのからアップグレードするときに発生しました [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。<br /><br /> 1-NO_TRIM: 行グループは切り捨てられませんでした。 行グループが最大1048576行で圧縮されました。  行のサブセットが閉じられた後に行のサブセットが削除された場合、行の数が少なくなる可能性があります。<br /><br /> 2-BULKLOAD: 一括読み込みバッチのサイズは、行の数に制限されています。<br /><br /> 3-REORG: REORG コマンドの一部として強制圧縮。<br /><br /> 4-DICTIONARY_SIZE: ディクショナリのサイズが大きすぎて、すべての行をまとめて圧縮できませんでした。<br /><br /> 5-MEMORY_LIMITATION: すべての行をまとめて圧縮するのに十分なメモリがありません。<br /><br /> 6-RESIDUAL_ROW_GROUP: インデックスの構築操作中に行 < 100万を含む最後の行グループの一部として閉じられました<br /><br /> 7-STATS_MISMATCH: インメモリテーブルの列ストアのみです。 Stats が不適切に示されている場合 >は、末尾に100万の修飾された行を指定しますが、これは少なくとも、圧縮された行グループは100万行を < します。<br /><br /> 8-スピルオーバー: インメモリテーブルの列ストアのみです。 末尾 > に100万の行が含まれている場合、その数が 10 ~ 100万の場合、残りの行の最後のバッチは圧縮されます。<br /><br /> 9-AUTO_MERGE: バックグラウンドで実行されている組ムーバーのマージ操作で、この行グループに1つ以上の行グループが統合されています。|  
|**transition_to_compressed_state**|tinyint|この行グループがデルタストアから列ストアの圧縮状態に移動した方法を示します。<br /><br /> 1-NOT_APPLICABLE<br /><br /> 2-INDEX_BUILD<br /><br /> 3-TUPLE_MOVER<br /><br /> 4-REORG_NORMAL<br /><br /> 5-REORG_FORCED<br /><br /> 6-BULKLOAD<br /><br /> 7-マージ|  
|**transition_to_compressed_state_desc**|nvarchar(60)| 1-NOT_APPLICABLE-操作はデルタストアには適用されません。 または、にアップグレードする前に、行グループが圧縮されて [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] います。この場合、履歴は保持されません。<br /><br /> 2-INDEX_BUILD-インデックスの作成またはインデックスの再構築によって、行グループが圧縮されます。<br /><br /> 3-TUPLE_MOVER-バックグラウンドで実行されている組ムーバーが、行グループを圧縮します。 組ムーバーは、行グループが状態をオープンから終了に変更した後に発生します。<br /><br /> 4-REORG_NORMAL-再編成操作、ALTER INDEX...REORG は、終了した行グループをデルタストアから列ストアに移動しました。 これは、組ムーバーが行グループを移動するのに時間があった前に発生しました。<br /><br /> 5-REORG_FORCED-この行グループはデルタストアで開かれており、行をすべて保持する前に列ストアに強制されていました。<br /><br /> 6-BULKLOAD-一括読み込み操作では、デルタストアを使用せずに行グループを直接圧縮しました。<br /><br /> 7-MERGE-マージ操作では、1つ以上の行グループをこの行グループに統合し、列ストア圧縮を実行しました。|  
|**has_vertipaq_optimization**|bit|VertiPaq の最適化では、より高い圧縮を実現するために、行グループ内の行の順序を再調整することによって列ストア圧縮が向上します。 ほとんどの場合、この最適化は自動的に行われます。 VertiPaq の最適化が使用されない場合は、次の2つのケースがあります。<br/>  a. デルタ行グループが列ストアに移動し、列ストアインデックスに1つ以上の非クラスター化インデックスがある場合、この例では、マッピングインデックスへの変更を最小限に抑えるために、VertiPaq の最適化がスキップされます。<br/> b. メモリ最適化テーブルの列ストアインデックスの場合。 <br /><br /> 0 = いいえ<br /><br /> 1 = はい|  
|**generation**|bigint|この行グループに関連付けられている行グループの生成。|  
|**created_time**|datetime2|この行グループが作成された時刻。<br /><br /> NULL-インメモリテーブルの列ストアインデックスの場合。| 
|**closed_time**|datetime2|この行グループが終了した時刻。<br /><br /> NULL-インメモリテーブルの列ストアインデックスの場合。| 
| &nbsp; | &nbsp; | &nbsp; |

## <a name="results"></a>結果  
 現在のデータベース内の各行グループに対して1行のデータを返します。  
  
## <a name="permissions"></a>アクセス許可  
`CONTROL`テーブルに対する権限と `VIEW DATABASE STATE` 、データベースに対する権限が必要です。  
  
## <a name="examples"></a>例  
  
### <a name="a-calculate-fragmentation-to-decide-when-to-reorganize-or-rebuild-a-columnstore-index"></a>A. 断片化を計算して、列ストアインデックスを再編成または再構築するタイミングを決定します。  
 列ストアインデックスの場合、行グループの断片化を解消するには、削除された行の割合を使用することをお勧めします。 断片化が20% 以上の場合は、削除された行を削除します。 他の例については、「 [インデックスの再編成と再構築](~/relational-databases/indexes/reorganize-and-rebuild-indexes.md)」を参照してください。  
  
 この例では、 **sys.dm_db_column_store_row_group_physical_stats** を他のシステムテーブルと結合し、 `Fragmentation` 現在のデータベース内の各行グループの効率の推定値として列を計算します。 1つのテーブルに関する情報を検索するには、 **WHERE** 句の前にあるコメントハイフンを削除し、テーブル名を指定します。  
  
```sql  
SELECT i.object_id,   
    object_name(i.object_id) AS TableName,   
    i.name AS IndexName,   
    i.index_id,   
    i.type_desc,   
    CSRowGroups.*,  
    100*(ISNULL(deleted_rows,0))/NULLIF(total_rows,0) AS 'Fragmentation'
FROM sys.indexes AS i  
JOIN sys.dm_db_column_store_row_group_physical_stats AS CSRowGroups  
    ON i.object_id = CSRowGroups.object_id AND i.index_id = CSRowGroups.index_id   
-- WHERE object_name(i.object_id) = 'table_name'   
ORDER BY object_name(i.object_id), i.name, row_group_id;  
```  
  
## <a name="see-also"></a>参照  
 [オブジェクト カタログ ビュー &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/object-catalog-views-transact-sql.md)   
 [カタログ ビュー &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/catalog-views-transact-sql.md)      
 [列ストア インデックスのアーキテクチャ](../../relational-databases/sql-server-index-design-guide.md#columnstore_index)         
 [SQL Server システムカタログに対するクエリについてよく寄せられる質問](../../relational-databases/system-catalog-views/querying-the-sql-server-system-catalog-faq.yml)   
 [sys.columns (Transact-SQL)](../../relational-databases/system-catalog-views/sys-columns-transact-sql.md)   
 [sys.all_columns &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-all-columns-transact-sql.md)   
 [sys.computed_columns &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-computed-columns-transact-sql.md)  
 [sys.column_store_dictionaries &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-column-store-dictionaries-transact-sql.md)   
 [sys.column_store_segments &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-column-store-segments-transact-sql.md)  
  
