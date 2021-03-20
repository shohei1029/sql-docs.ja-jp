---
description: sys.dm_db_index_operational_stats (Transact-sql)
title: sys.dm_db_index_operational_stats (Transact-sql)
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.prod_service: synapse-analytics, database-engine, pdw, sql-database
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- dm_db_index_operational_stats
- sys.dm_db_index_operational_stats_TSQL
- sys.dm_db_index_operational_stats
- dm_db_index_operational_stats_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_db_index_operational_stats dynamic management function
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: a4fd888929933fbd9951fedda9553ce320a31827
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104739532"
---
# <a name="sysdm_db_index_operational_stats-transact-sql"></a>sys.dm_db_index_operational_stats (Transact-sql)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  データベース内のテーブルまたはインデックスの各パーティションについて、現在の下位レベルの i/o、ロック、ラッチ、およびアクセスメソッドのアクティビティを返します。    
    
 メモリ最適化インデックスは、この DMV には表示されません。    
    
> [!NOTE]    
>  **sys.dm_db_index_operational_stats** は、メモリ最適化インデックスに関する情報を返しません。 メモリ最適化インデックスの使用の詳細については、「 [sys.dm_db_xtp_index_stats &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-xtp-index-stats-transact-sql.md)」を参照してください。    
        
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)    
    
## <a name="syntax"></a>構文    
    
```    
sys.dm_db_index_operational_stats (    
    { database_id | NULL | 0 | DEFAULT }    
  , { object_id | NULL | 0 | DEFAULT }    
  , { index_id | 0 | NULL | -1 | DEFAULT }    
  , { partition_number | NULL | 0 | DEFAULT }    
)    
```    
    
## <a name="arguments"></a>引数    

*database_id* |NULL |0 |標準

  データベースの ID です。 *database_id* は **smallint** です。 有効な入力値は、データベースの ID 番号、NULL、0、または DEFAULT です。 既定値は 0 です。 このコンテキストでは、NULL、0、および DEFAULT は同じ値になります。    
    
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスのすべてのデータベースに関する情報を返すには NULL を指定します。 *Database_id* に null を指定した場合は、 *object_id*、 *index_id*、および *partition_number* にも null を指定する必要があります。    
    
 組み込み関数 [DB_ID](../../t-sql/functions/db-id-transact-sql.md) を指定できます。    

*object_id* |NULL |0 |標準

 インデックスがあるテーブルまたはビューのオブジェクト ID を指定します。 *object_id* は **int** です。    
    
 有効な入力値は、テーブルおよびビューの ID 番号、NULL、0、または DEFAULT です。 既定値は 0 です。 このコンテキストでは、NULL、0、および DEFAULT は同じ値になります。    
    
 NULL を指定すると、指定されたデータベース内にあるすべてのテーブルとビューに関するキャッシュされた情報が返されます。 *Object_id* に null を指定する場合は、 *index_id* と *partition_number* にも null を指定する必要があります。    

*index_id* |0 |NULL |-1 |標準

 インデックスの ID。 *index_id* は **int** です。有効な入力値は、インデックスの ID 番号です。 *object_id* がヒープ、NULL、-1、または DEFAULT である場合は0になります。 既定値は -1 です。このコンテキストでは、NULL、-1、および DEFAULT は同じ値になります。    
    
 NULL を指定すると、ベース テーブルまたはビューのすべてのインデックスに関するキャッシュされた情報が返されます。 *Index_id* に null を指定する場合は、 *partition_number* に null を指定する必要もあります。    

*partition_number* |NULL |0 |標準

 オブジェクト内のパーティション番号を指定します。 *partition_number* は **int** です。有効な入力値は、インデックスまたはヒープの *partion_number* 、NULL、0、または DEFAULT です。 既定値は 0 です。 このコンテキストでは、NULL、0、および DEFAULT は同じ値になります。    
    
 NULL を指定すると、インデックスまたはヒープのすべてのパーティションについてキャッシュされた情報が返されます。    
    
 *partition_number* は1から始まるです。 非パーティションインデックスまたはヒープの *partition_number* が1に設定されています。    
    
## <a name="table-returned"></a>返されるテーブル    
    
|列名|データ型|説明|    
|-----------------|---------------|-----------------|    
|**database_id**|**smallint**|データベース ID。|    
|**object_id**|**int**|テーブルまたはビューの ID。|    
|**index_id**|**int**|インデックスまたはヒープの ID。<br /><br /> 0 = ヒープ| 
|**partition_number**|**int**|インデックスまたはヒープ内の、1 から始まるパーティション番号。| 
|**hobt_id**|**bigint**|[!INCLUDE [SQL Server 2016 and later](../../includes/applies-to-version/sqlserver2016.md)], [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)].<br /><br /> 列ストアインデックスの内部データを追跡するデータヒープまたは B ツリー行セットの ID。<br /><br /> NULL-これは内部列ストア行セットではありません。<br /><br /> 詳細については、「 [sys.internal_partitions &#40;transact-sql&#41;](../../relational-databases/system-catalog-views/sys-internal-partitions-transact-sql.md) 」を参照してください。|       
|**leaf_insert_count**|**bigint**|リーフレベルの挿入の累積数。|    
|**leaf_delete_count**|**bigint**|リーフレベルの削除の累積数。 leaf_delete_count は、まずゴーストとしてマークされていない削除済みレコードに対してのみインクリメントされます。 削除されたレコードが最初にゴーストである場合は、代わりに **leaf_ghost_count** がインクリメントされます。|    
|**leaf_update_count**|**bigint**|リーフレベルの更新の累積数。|    
|**leaf_ghost_count**|**bigint**|削除とマークされており、まだ削除されていないリーフレベルの行の累積数。 この数には、ゴーストとしてマークされずにすぐに削除されるレコードは含まれません。 このような行は、設定された間隔でクリーンアップ スレッドにより削除されます。 この値には、未処理のスナップショット分離トランザクションが原因で保持されている行の数は含まれません。|    
|**nonleaf_insert_count**|**bigint**|リーフ レベルより上の挿入の累積数。<br /><br /> 0 = ヒープまたは列ストア|    
|**nonleaf_delete_count**|**bigint**|リーフ レベルより上の削除の累積数。<br /><br /> 0 = ヒープまたは列ストア|    
|**nonleaf_update_count**|**bigint**|リーフ レベルより上の更新の累積数。<br /><br /> 0 = ヒープまたは列ストア|    
|**leaf_allocation_count**|**bigint**|インデックスまたはヒープにおけるリーフ レベルのページ割り当ての累積数。<br /><br /> インデックスの場合、ページ割り当てとページ分割は対応しています。|    
|**nonleaf_allocation_count**|**bigint**|リーフ レベルより上のページ分割によって発生したページ割り当ての累積数。<br /><br /> 0 = ヒープまたは列ストア|    
|**leaf_page_merge_count**|**bigint**|リーフ レベルでページをマージした累積数。 列ストア インデックスでは、常に 0 です。|    
|**nonleaf_page_merge_count**|**bigint**|リーフ レベルより上でページをマージした累積数。<br /><br /> 0 = ヒープまたは列ストア|    
|**range_scan_count**|**bigint**|インデックスまたはヒープで開始された範囲スキャンとテーブル スキャンの累積数。|    
|**singleton_lookup_count**|**bigint**|インデックスまたはヒープから取得した単一行の累積数。|    
|**forwarded_fetch_count**|**bigint**|前方向レコードを介してフェッチされた行の数。<br /><br /> 0 = インデックス|    
|**lob_fetch_in_pages**|**bigint**|LOB_DATA アロケーション ユニットから取得したラージ オブジェクト (LOB) ページの累積数。 これらのページには、 **text**、 **ntext**、 **image**、 **varchar (max)**、 **nvarchar (max)**、 **varbinary (max)**、および **xml** 型の列に格納されているデータが含まれています。 詳細については、「[データ型 &#40;Transact-SQL&#41;](../../t-sql/data-types/data-types-transact-sql.md)」を参照してください。|    
|**lob_fetch_in_bytes**|**bigint**|取得した LOB データの累積バイト数。|    
|**lob_orphan_create_count**|**bigint**|一括操作用に作成された、孤立した LOB 値の累積数。<br /><br /> 0 = 非クラスター化インデックス|    
|**lob_orphan_insert_count**|**bigint**|一括操作中に挿入された、孤立した LOB 値の累積数。<br /><br /> 0 = 非クラスター化インデックス|    
|**row_overflow_fetch_in_pages**|**bigint**|ROW_OVERFLOW_DATA アロケーション ユニットから取得した行オーバーフロー データ ページの累積数。<br /><br /> これらのページには、行外にプッシュされた **varchar (n)**、 **nvarchar (n)**、 **varbinary (n)**、および **sql_variant** 型の列に格納されているデータが含まれています。|    
|**row_overflow_fetch_in_bytes**|**bigint**|取得した行オーバーフロー データの累積バイト数。|    
|**column_value_push_off_row_count**|**bigint**|挿入または更新された行をページ内に収めるため、行外に出された LOB データおよび行オーバーフロー データに対する列値の累積数。|    
|**column_value_pull_in_row_count**|**bigint**|行内に取り込まれた LOB データおよび行オーバーフロー データに対する列値の累積数。 これは、更新操作によりレコード内の領域が解放され、LOB_DATA または ROW_OVERFLOW_DATA アロケーション ユニットから IN_ROW_DATA アロケーション ユニットに 1 つ以上の行外値を取り込めるようになったときに発生します。|    
|**row_lock_count**|**bigint**|要求された行ロックの累積数。|    
|**row_lock_wait_count**|**bigint**|[!INCLUDE[ssDE](../../includes/ssde-md.md)]が行ロックで待機した累積回数。|    
|**row_lock_wait_in_ms**|**bigint**|[!INCLUDE[ssDE](../../includes/ssde-md.md)]が行ロックで待機した合計ミリ秒数。|    
|**page_lock_count**|**bigint**|要求されたページ ロックの累積数。|    
|**page_lock_wait_count**|**bigint**|[!INCLUDE[ssDE](../../includes/ssde-md.md)]がページ ロックで待機した累積回数。|    
|**page_lock_wait_in_ms**|**bigint**|[!INCLUDE[ssDE](../../includes/ssde-md.md)]がページ ロックで待機した合計ミリ秒数。|    
|**index_lock_promotion_attempt_count**|**bigint**|[!INCLUDE[ssDE](../../includes/ssde-md.md)]がロックのエスカレートを試行した累積回数。|    
|**index_lock_promotion_count**|**bigint**|[!INCLUDE[ssDE](../../includes/ssde-md.md)]がロックをエスカレートした累積回数。|    
|**page_latch_wait_count**|**bigint**|ラッチ競合のために、[!INCLUDE[ssDE](../../includes/ssde-md.md)]が待機した累積回数。|    
|**page_latch_wait_in_ms**|**bigint**|ラッチ競合のために、[!INCLUDE[ssDE](../../includes/ssde-md.md)]が待機した累積ミリ秒数。|    
|**page_io_latch_wait_count**|**bigint**|[!INCLUDE[ssDE](../../includes/ssde-md.md)]がページ I/O ラッチで待機した累積回数。|    
|**page_io_latch_wait_in_ms**|**bigint**|[!INCLUDE[ssDE](../../includes/ssde-md.md)]がページ I/O ラッチで待機した累積ミリ秒数。|    
|**tree_page_latch_wait_count**|**bigint**|上位レベルの B ツリーのページのみを含んだ **page_latch_wait_count** のサブセット。 ヒープまたは列ストア インデックスでは、常に 0 です。|    
|**tree_page_latch_wait_in_ms**|**bigint**|上位レベルの B ツリーのページのみを含んだ **page_latch_wait_in_ms** のサブセット。 ヒープまたは列ストア インデックスでは、常に 0 です。|    
|**tree_page_io_latch_wait_count**|**bigint**|上位レベルの B ツリーのページのみを含んだ **page_io_latch_wait_count** のサブセット。 ヒープまたは列ストア インデックスでは、常に 0 です。|    
|**tree_page_io_latch_wait_in_ms**|**bigint**|上位レベルの B ツリーのページのみを含んだ **page_io_latch_wait_in_ms** のサブセット。 ヒープまたは列ストア インデックスでは、常に 0 です。|    
|**page_compression_attempt_count**|**bigint**|テーブル、インデックス、またはインデックス付きビューの特定のパーティションで、ページ レベルの圧縮が評価されたページの数。 大幅な節減を実現できないため圧縮されなかったページも含まれます。 列ストア インデックスでは、常に 0 です。|    
|**page_compression_success_count**|**bigint**|テーブル、インデックス、またはインデックス付きビューの特定のパーティションで、ページの圧縮を使用して圧縮されたデータ ページの数。 列ストア インデックスでは、常に 0 です。|    
    
## <a name="remarks"></a>解説    
 この動的管理オブジェクトは、およびからの相関パラメーターを受け入れません `CROSS APPLY` `OUTER APPLY` 。    
    
 **sys.dm_db_index_operational_stats** を使用すると、テーブル、インデックス、またはパーティションに対する読み書きを行うために、ユーザーが待機する必要がある時間の長さを追跡でき、重大な I/O 動作やホット スポットが発生したテーブルまたはインデックスを特定できます。    
    
 競合について確認するには、次の列を使用します。    
    
 **テーブルまたはインデックス パーティションへの一般的なアクセス パターンを分析する場合**、次の列を使用します。    
    
-   **leaf_insert_count**    
    
-   **leaf_delete_count**    
    
-   **leaf_update_count**    
    
-   **leaf_ghost_count**    
    
-   **range_scan_count**    
    
-   **singleton_lookup_count**    
    
 ラッチおよびロックの競合を特定する場合、次の列を使用します。    
    
-   **page_latch_wait_count** および **page_latch_wait_in_ms**    
    
     これらの列では、インデックスまたはヒープにラッチ競合があるかどうかが示されます。また競合の重大度も示されます。    
    
-   **row_lock_count** および **page_lock_count**    
    
     これらの列では、[!INCLUDE[ssDE](../../includes/ssde-md.md)]で行とページ ロックの取得が何回試行されたかが示されます。    
    
-   **row_lock_wait_in_ms** および **page_lock_wait_in_ms**    
    
     これらの列では、インデックスまたはヒープにロック競合があるかどうかが示されます。また競合の重大度も示されます。    
    
 **インデックスまたはヒープ パーティションの物理 I/O の統計を分析する場合**    
    
-   **page_io_latch_wait_count** および **page_io_latch_wait_in_ms**    
    
     これらの列では、インデックスまたはヒープ ページをメモリ内に移動するために、物理 I/O が発行されたかどうかが示されます。また I/O の発行回数も示されます。    
    
## <a name="column-remarks"></a>列の解説    
 **lob_orphan_create_count** および **lob_orphan_insert_count** 内の値は、常に同じになります。    
    
 付加列として 1 つ以上の LOB 列を含む非クラスター化インデックスの場合、**lob_fetch_in_pages** および **lob_fetch_in_bytes** 列内の値は 0 より大きくなります。 詳細については、「 [付加列インデックスの作成](../../relational-databases/indexes/create-indexes-with-included-columns.md)」を参照してください。 同様に、行外に出される可能性がある列を含む非クラスター化インデックスの場合は、**row_overflow_fetch_in_pages** および **row_overflow_fetch_in_bytes** 列内の値は 0 より大きくなります。    
    
## <a name="how-the-counters-in-the-metadata-cache-are-reset"></a>メタデータキャッシュ内のカウンターのリセット方法    
 **sys.dm_db_index_operational_stats** により返されるデータが存在するのは、ヒープまたはインデックスを表すメタデータ キャッシュ オブジェクトが使用できる間だけです。 このデータは持続性はなく、トランザクション上の一貫性もありません。 つまり、これらのカウンターを使って、インデックスが使用されているかどうかや、インデックスが最後に使用されたのはいつであるかを判断することはできません。 詳細については、「 [sys.dm_db_index_usage_stats &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-index-usage-stats-transact-sql.md)」を参照してください。    
    
 ヒープまたはインデックスに対するメタデータがメタデータ キャッシュに組み込まれるたび、各列の値はゼロに設定されます。統計値は、キャッシュ オブジェクトがメタデータ キャッシュから削除されるまで累積されます。 したがって、キャッシュにはアクティブなヒープまたはインデックスのメタデータが常に存在し、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンスが最後に開始してからの動作が累積数として反映されます。 アクティブになる頻度が低いヒープやインデックスのメタデータは、使用状況に応じてキャッシュ内外に移動されます。 その結果、使用できる値が存在する場合と、存在しない場合が発生します。 インデックスを削除すると、対応する統計はメモリから削除され、この関数ではレポートされなくなります。 インデックスに対するその他の DDL 操作によって、統計の値がゼロにリセットされる場合もあります。    
    
## <a name="using-system-functions-to-specify-parameter-values"></a>システム関数を使用したパラメーター値の指定    
 [!INCLUDE[tsql](../../includes/tsql-md.md)]関数 [DB_ID](../../t-sql/functions/db-id-transact-sql.md)と [OBJECT_ID](../../t-sql/functions/object-id-transact-sql.md)を使用して、 *database_id* および *object_id* パラメーターの値を指定できます。 ただし、これらの関数に無効な値を渡すと、意図しない結果が生じる可能性があります。 DB_ID または OBJECT_ID を使用する場合は、必ず有効な ID が返されるようにしてください。 詳細については、「 [sys.dm_db_index_physical_stats &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-index-physical-stats-transact-sql.md)」の「解説」を参照してください。    
    
## <a name="permissions"></a>権限    
 次の権限が必要です。    
    
-   `CONTROL` データベース内の指定されたオブジェクトに対する権限    
    
-   `VIEW DATABASE STATE` オブジェクトのワイルドカード @*object_id* = NULL を使用して、指定したデータベース内のすべてのオブジェクトに関する情報を返す権限    
    
-   `VIEW SERVER STATE` データベースのワイルドカードを使用してすべてのデータベースに関する情報を返す権限 @*database_id* = NULL    
    
 許可 `VIEW DATABASE STATE` すると、特定のオブジェクトに対して拒否された制御権限に関係なく、データベース内のすべてのオブジェクトを返すことができます。    
    
 拒否 `VIEW DATABASE STATE` すると、特定のオブジェクトに対して許可された制御権限に関係なく、データベース内のすべてのオブジェクトが返されます。 また、データベースのワイルドカードを指定すると、 `@database_id=NULL` データベースは省略されます。    
    
 詳細については、「 [transact-sql&#41;&#40;動的管理ビューと関数 ](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)」を参照してください。    
    
## <a name="examples"></a>例    
    
### <a name="a-returning-information-for-a-specified-table"></a>A. 指定されたテーブルの情報を返す    
 次の例では、[!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] データベース内にある `Person.Address` テーブルのすべてのインデックスとパーティションについて、情報を返します。 このクエリを実行するには、少なくとも `Person.Address` テーブルに対する CONTROL 権限が必要です。    
    
> [!IMPORTANT]    
> [!INCLUDE[tsql](../../includes/tsql-md.md)] 関数 DB_ID および OBJECT_ID を使ってパラメーター値を返すときには、有効な ID が返されることを常に確認してください。 存在しない場合やスペルが正しくない場合など、データベースまたはオブジェクト名が見つからない場合、両方の関数が NULL を返します。 **sys.dm_db_index_operational_stats** 関数では、NULL 値はすべてのデータベースまたはすべてのオブジェクトを指定するワイルドカード値として解釈されます。 これは、意図しない操作である可能性があるため、このセクションの例では、データベースおよびオブジェクト ID を確認する安全な方法を示します。    
    
```sql    
DECLARE @db_id int;    
DECLARE @object_id int;    
SET @db_id = DB_ID(N'AdventureWorks2012');    
SET @object_id = OBJECT_ID(N'AdventureWorks2012.Person.Address');    
IF @db_id IS NULL     
  BEGIN;    
    PRINT N'Invalid database';    
  END;    
ELSE IF @object_id IS NULL    
  BEGIN;    
    PRINT N'Invalid object';    
  END;    
ELSE    
  BEGIN;    
    SELECT * FROM sys.dm_db_index_operational_stats(@db_id, @object_id, NULL, NULL);    
  END;    
GO    
```    
    
### <a name="b-returning-information-for-all-tables-and-indexes"></a>B. すべてのテーブルとインデックスの情報を返す    
 次の例では、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンス内のすべてのテーブルとインデックスについて情報を返します。 このクエリを実行するには、VIEW SERVER STATE 権限が必要です。    
    
```sql    
SELECT * FROM sys.dm_db_index_operational_stats( NULL, NULL, NULL, NULL);    
GO        
```    
    
## <a name="see-also"></a>参照    
 [動的管理ビューと動的管理関数 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)     
 [インデックス関連の動的管理ビューおよび関数 &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/index-related-dynamic-management-views-and-functions-transact-sql.md)     
 [パフォーマンスの監視とチューニング](../../relational-databases/performance/monitor-and-tune-for-performance.md)     
 [sys.dm_db_index_physical_stats &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-index-physical-stats-transact-sql.md)     
 [sys.dm_db_index_usage_stats &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-index-usage-stats-transact-sql.md)     
 [sys.dm_os_latch_stats &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-os-latch-stats-transact-sql.md)     
 [sys.dm_db_partition_stats &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-partition-stats-transact-sql.md)     
 [sys.allocation_units &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-allocation-units-transact-sql.md)     
 [sys.indexes &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-indexes-transact-sql.md)    
    
