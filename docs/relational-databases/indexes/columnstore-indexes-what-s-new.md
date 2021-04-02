---
description: 列ストア インデックス - 新機能
title: 列ストア インデックス - 新機能 | Microsoft Docs
ms.custom: ''
ms.date: 05/11/2020
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: table-view-index
ms.topic: conceptual
author: MikeRayMSFT
ms.author: mikeray
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: d79a2b7617d195e8140945c23c5bdb7284cb0077
ms.sourcegitcommit: 295b9dfc758471ef7d238a2b0f92f93e34acbb1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106054681"
---
# <a name="columnstore-indexes---what39s-new"></a>列ストア インデックス - 新機能
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の各バージョンで利用可能な列ストア機能の概要と、[!INCLUDE[ssSDS](../../includes/sssds-md.md)]、[!INCLUDE[ssSDW](../../includes/sssdw-md.md)]、および [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] の最新リリース。  

 > [!NOTE]
 > [!INCLUDE[ssSDS](../../includes/sssds-md.md)] では、列ストア インデックスが [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] Premium レベル、Standard レベル (S3 以上)、すべての vCore レベルで使用できます。 [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] SP1 以降では、列ストア インデックスがすべてのエディションで使用できます。 [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] (SP1 より前) 以前のバージョンでは、列ストア インデックスが Enterprise Edition でのみ使用できます。
 
## <a name="feature-summary-for-product-releases"></a>製品リリースの機能の概要  
 列ストア インデックスの主な機能と、これらの機能を利用できる製品をまとめた表を次に示します。  

|列ストア インデックスの機能|[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)]|[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)]|[!INCLUDE[sssql16-md](../../includes/sssql16-md.md)]|[!INCLUDE[ssSQL17](../../includes/sssql17-md.md)]|[!INCLUDE[sql-server-2019](../../includes/sssql19-md.md)]|[!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)]|[!INCLUDE[ssSDW](../../includes/sssdw-md.md)]|  
|-------------------------------|---------------------------|---------------------------|---------------------------|---------------------------|--------------------------------------------|-------------------------|---|  
|マルチ スレッド クエリのバッチ モード実行|はい|はい|はい|はい|はい|はい|はい| 
|シングル スレッド クエリのバッチ モード実行|||はい|はい|はい|はい|はい|  
|アーカイブ圧縮オプション||はい|はい|はい|はい|はい|はい|  
|スナップショット分離および Read Committed スナップショット分離|||はい|はい|はい|はい|はい| 
|テーブルの作成時に、列ストア インデックスを指定する|||はい|はい|はい|はい|はい|  
|AlwaysOn は列ストア インデックスをサポートする|はい|はい|はい|はい|はい|はい|はい| 
|AlwaysOn の読み取り可能なセカンダリは非クラスター化列ストア インデックスをサポートする|はい|はい|はい|はい|はい|はい|はい|  
|Always On の読み取り可能なセカンダリは、更新可能な列ストア インデックスをサポートする|||はい||はい|||  
|ヒープまたは B ツリーの読み取り専用の非クラスター化列ストア インデックス|はい|はい|はい <sup>1</sup>|はい <sup>1</sup>|はい <sup>1</sup>|はい <sup>1</sup>|はい <sup>1</sup>|  
|ヒープまたは B ツリーの更新可能な非クラスター化列ストア インデックス|||はい|はい|はい|はい|はい|  
|ヒープまたは B ツリーで許容される追加の B ツリー インデックスには非クラスター化列ストア インデックスがある|はい|はい|はい|はい|はい|はい|はい|  
|更新可能なクラスター化列ストア インデックス||はい|はい|はい||はい|はい|  
|クラスター化列ストア インデックスの B ツリー インデックス|||はい|はい||はい|はい|  
|メモリ最適化テーブルの列ストア インデックス|||はい|はい||はい|はい|  
|非クラスター化列ストア インデックスの定義では、フィルター適用条件の使用をサポートする|||はい|はい|はい|はい|はい|  
|`CREATE TABLE` および `ALTER TABLE` での列ストア インデックスの圧縮遅延オプション|||はい|はい|はい|はい|はい|
|列ストア インデックスは保存されない計算列を使用できる||||はい|はい|||   
|組ムーバーのバックグラウンド マージ サポート|||||はい|はい|はい|
  
 <sup>1</sup> 読み取り専用の非クラスター化列ストア インデックスを作成するには、読み取り専用ファイル グループにインデックスを格納します。  
 
> [!NOTE]
> [バッチ モード](../../relational-databases/query-processing-architecture-guide.md#batch-mode-execution)操作の並列処理の度合い (DOP) は、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Standard Edition では 2 DOP、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Web Edition および Express Edition では 1 DOP に制限されます。 これは、ディスク ベース テーブルとメモリ最適化テーブルで作成された列ストア インデックスに当てはまります。

## [!INCLUDE[sql-server-2019](../../includes/sssql19-md.md)] 
 [!INCLUDE[sql-server-2019](../../includes/sssql19-md.md)] での新機能は次のとおりです。

### <a name="functional"></a>機能
- [!INCLUDE[sql-server-2019](../../includes/sssql19-md.md)] 以降、組ムーバーは、内部しきい値によってしばらくの間存在していると判断された小さな OPEN デルタ行グループを自動的に圧縮するか、多数の行が削除されている COMPRESSED 行グループをマージするバックグラウンド マージ タスクによってサポートされています。 行グループを部分的に削除されたデータとマージするには、以前はインデックスの再編成操作が必要でした。 これにより、時間の経過とともに、列ストア インデックスの品質が向上します。 

## [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 
 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] での新機能は次のとおりです。

### <a name="functional"></a>機能
- [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] は、クラスター化列ストア インデックス内の保存されない計算列をサポートします。 保存される計算列は、クラスター化列ストア インデックス内ではサポートされません。 計算列が含まれる列ストア インデックスに非クラスター化インデックスを作成することはできません。 

## [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)]  
 [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] には、パフォーマンスと列ストア インデックスの柔軟性を向上させるために重要な機能強化が追加されます。 これらの機能強化により、データ ウェアハウスのシナリオが強化され、リアルタイムの運用分析が可能になります。  
  
### <a name="functional"></a>機能  
  
-   行ストア テーブルで、更新可能な非クラスター化列ストア インデックスを 1 つ使用できます。 以前、非クラスター化列ストア インデックスは、読み取り専用でした。  
  
-   非クラスター化列ストア インデックスの定義で、フィルター適用条件の使用をサポートします。 OLTP テーブルに列ストア インデックスを追加することによるパフォーマンスへの影響を最小限に抑えるには、フィルター条件を使って、用して、運用ワークロードのコールド データのみに、非クラスター化列ストア インデックスを作成します。 
  
-   インメモリ テーブルでは、列ストア インデックスを 1 つ使用できます。 これは、テーブルの作成時に作成することも、後で [ALTER TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-table-transact-sql.md) を使用して追加することもできます。 以前は、列ストア インデックスを保持できたのはディスク ベースのテーブルのみでした。  
  
-   クラスター化列ストア インデックスでは、1 つ以上の非クラスター化行ストア インデックスを使用できます。 以前、列ストア インデックスでは、非クラスター化インデックスはサポートされていませんでした。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、DML 操作の非クラスター化インデックスが自動的に維持されます。  
  
-   B ツリー インデックスを使用して主キーと外部キーをサポートし、これらの制約をクラスター化列ストア インデックスに適用します。  
  
-   列ストア インデックスには、リアルタイム運用分析へのトランザクション ワークロードの影響を最小限に抑える圧縮遅延オプションが用意されています。  このオプションでは、頻繁に変更される行が安定するように配慮してから、それらの行を列ストアに圧縮します。 詳しくは、「[CREATE COLUMNSTORE INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/create-columnstore-index-transact-sql.md)」および「[列ストアを使用したリアルタイム運用分析の概要](../../relational-databases/indexes/get-started-with-columnstore-for-real-time-operational-analytics.md)」をご覧ください。  
  
### <a name="performance-for-database-compatibility-level-120-or-130"></a>データベースの互換性レベルが 120 または 130 の場合のパフォーマンス  
  
-   列ストア インデックスでは、Read Committed スナップショット分離レベル (RCSI) とスナップショット分離 (SI) をサポートします。 これにより、ロックなしのトランザクション一貫性分析クエリが有効になります。  
  
-   列ストアでは、削除された行を取り除くことでインデックス最適化をサポートしており、明示的にインデックスを再構築する必要はありません。 `ALTER INDEX ... REORGANIZE` ステートメントは、オンライン操作として、内部的に定義されたポリシーに基づいて、削除された行を列ストアから削除します。  
  
-   列ストア インデックスには、Always On の読み取り可能なセカンダリ レプリカでアクセスできます。 Always On セカンダリ レプリカに分析クエリをオフロードすることで、運用分析のパフォーマンスを向上させることができます。  
  
-   集計プッシュダウンでは、データ型で使われているバイト数が 8 バイト以下で、かつ文字列でない場合は、テーブル スキャン中に集計関数 `MIN`、`MAX`、`SUM`、`COUNT`、`AVG` が計算されます。 クラスター化列ストア インデックスおよび非クラスター化列ストア インデックスの両方で、`GROUP BY` 句を使用するかどうかに関係なく、集計プッシュダウンがサポートされています。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、この拡張機能は Enterprise edition 用に予約されています。
  
-   文字列述語のプッシュダウンは、VARCHAR/CHAR 型または NVARCHAR/NCHAR 型の文字列を比較するクエリを高速化します。 これは、一般的な比較演算子に適用され、ビットマップ フィルターを使用する演算子 (`LIKE` など) が含まれます。 サポートされるすべての照合順序で機能します。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、この拡張機能は Enterprise edition 用に予約されています。 

-   ベクターベースのハードウェア機能を活用したバッチ モード操作の機能強化。 [!INCLUDE[ssde_md](../../includes/ssde_md.md)] によって、AVX 2 (Advanced Vector Extensions) と SSE 4 (Streaming SIMD Extensions 4) のハードウェア拡張機能の CPU サポート レベルが検出されます。サポートされている場合、これらが使用されます。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、この拡張機能は Enterprise edition 用に予約されています。
  
### <a name="performance-for-database-compatibility-level-130"></a>データベースの互換性レベルが 130 の場合のパフォーマンス  
  
-   次のいずれかの操作を使用して、クエリの新しいバッチ モード実行をサポートします。  
    -   `SORT`  
    -   複数の異なる関数では集計します。 例: `COUNT/COUNT`、`AVG/SUM`、`CHECKSUM_AGG`、`STDEV/STDEVP`  
    -   ウィンドウ集計関数: `COUNT`、`COUNT_BIG`、`SUM`、`AVG`、`MIN`、`MAX`、`CLR`  
    -   ユーザー定義のウィンドウ集計関数: `CHECKSUM_AGG`、`STDEV`、`STDEVP`、`VAR`、`VARP`、`GROUPING`  
    -   ウィンドウ集計分析関数: `LAG`、`LEAD`、`FIRST_VALUE`、`LAST_VALUE`、`PERCENTILE_CONT`、`PERCENTILE_DISC`、`CUME_DIST`、`PERCENT_RANK`  

-   `MAXDOP 1` または直列クエリ プランで実行されるシングル スレッド クエリは、バッチ モードで実行されます。 以前は、マルチ スレッド クエリのみがバッチ モードで実行されていました。  

-   メモリ最適化テーブル クエリでは、行ストア インデックス内または列ストア インデックス内のデータにアクセスする際に、並列プランを SQL 相互運用モードで使用できます。
  
### <a name="supportability"></a>サポート  
次に、列ストア用の新しいシステム ビューを示します。  
  
:::row:::
    :::column:::
        [sys.column_store_row_groups &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-column-store-row-groups-transact-sql.md)
    :::column-end:::
    :::column:::
        [sys.dm_column_store_object_pool &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-column-store-object-pool-transact-sql.md)
    :::column-end:::
:::row-end:::  
:::row:::
    :::column:::
        [sys.dm_db_column_store_row_group_operational_stats &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-column-store-row-group-operational-stats-transact-sql.md)
    :::column-end:::
    :::column:::
        [sys.dm_db_column_store_row_group_physical_stats &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-column-store-row-group-physical-stats-transact-sql.md)
    :::column-end:::
:::row-end:::  
:::row:::
    :::column:::
        [sys.dm_db_index_operational_stats &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-index-operational-stats-transact-sql.md)
    :::column-end:::
    :::column:::
        [sys.dm_db_index_physical_stats &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-index-physical-stats-transact-sql.md)
    :::column-end:::
:::row-end:::  
:::row:::
    :::column:::
        [sys.internal_partitions &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-internal-partitions-transact-sql.md)
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::
  
これらのインメモリ OLTP ベースの DMV には、列ストアに対する更新が含まれます。  

:::row:::
    :::column:::
        [sys.dm_db_xtp_hash_index_stats &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-xtp-hash-index-stats-transact-sql.md)
    :::column-end:::
    :::column:::
        [sys.dm_db_xtp_index_stats &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-xtp-index-stats-transact-sql.md)
    :::column-end:::
:::row-end:::  
:::row:::
    :::column:::
        [sys.dm_db_xtp_memory_consumers &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-xtp-memory-consumers-transact-sql.md)
    :::column-end:::
    :::column:::
        [sys.dm_db_xtp_nonclustered_index_stats &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-xtp-nonclustered-index-stats-transact-sql.md)
    :::column-end:::
:::row-end:::  
:::row:::
    :::column:::
        [sys.dm_db_xtp_object_stats &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-xtp-object-stats-transact-sql.md)
    :::column-end:::
    :::column:::
        [sys.dm_db_xtp_table_memory_stats &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-xtp-table-memory-stats-transact-sql.md)
    :::column-end:::
:::row-end:::

### <a name="limitations"></a>制限事項    
-   インメモリ テーブルの場合、列ストア インデックスにはすべての列が含まれている必要があります。列ストア インデックスにフィルター適用条件を含めることはできません。  
-   インメモリ テーブルの場合、列ストア インデックスに対するクエリは相互運用モードでのみ実行され、インメモリ ネイティブ モードでは実行されません。 並列実行がサポートされています。  
  
## [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)]  
 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] では、プライマリ ストレージ形式として、クラスター化列ストア インデックスが導入されました。 これにより、通常の読み込みに加えて、更新、削除、および挿入の操作が可能になりました。  
  
-   テーブルでは、プライマリ テーブル ストレージとしてクラスター化列ストア インデックスを使用できます。 テーブルで他のインデックスは使用できません。ただし、クラスター化列ストア インデックスは更新できるため、通常の読み込みを実行し、個々の行に変更を加えることができます。  
-   非クラスター化列ストア インデックスについては、バッチ モードで実行可能になった演算子が追加されたことを除けば、[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] の場合と同じ機能を引き続き備えています。 更新は相変わらずサポートされていません。ただし、再構築およびパーティションの切り替えによる更新は除きます。 非クラスター化列ストア インデックスは、ディスクベースのテーブルでのみサポートされており、インメモリ テーブルではサポートされていません。  
-   クラスター化および非クラスター化列ストア インデックスには、データをさらに圧縮するアーカイブ圧縮オプションがあります。 アーカイブ オプションは、メモリ内でもディスク上でもデータ サイズを縮小するのに便利ですが、クエリのパフォーマンスを低下させます。 アクセス頻度の低いデータに対して適切に機能します。  
-   クラスター化列ストア インデックスと非クラスター化列ストア インデックスには類似点が多数あります。両者は、同じ列ストレージ形式、同じクエリ処理エンジン、および同じ動的管理ビュー セットを使用します。 違いは、一方がプライマリ インデックス型で他方がセカンダリ インデックス型であることです。非クラスター化列ストア インデックスは読み取り専用です。  
-   Scan、Filter、Project、Join、Group By、Union All の各演算子は、マルチ スレッド クエリではバッチ モードで実行されます。  
  
## [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)]  
 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] では、行ストア テーブルに対するもう 1 つのインデックス型としての非クラスター化列ストア インデックスと、列ストア データに対するクエリのバッチ処理が導入されました。  
  
-   行ストア テーブルでは、非クラスター化列ストア インデックスを 1 つ使用することができます。  
-   列ストア インデックスは読み取り専用です。 列ストア インデックスの作成後は、`INSERT`、`DELETE`、および `UPDATE` の操作で、テーブルを更新することはできません。これらの操作を実行するには、インデックスを削除し、テーブルを更新し、列ストア インデックスを再構築する必要があります。 パーティション切り替えを使用することで、テーブルに追加データを読み込むことができます。 パーティション切り替えの利点は、列ストア インデックスを削除して再構築しなくても、データを読み込むことができることです。  
-   列ストア インデックスでは、データのコピーを格納するため、常に余分な (通常は、行ストアより 10% 多い) ストレージを確保しておく必要があります。  
-   バッチ処理は、クエリのパフォーマンスを 2 倍以上向上させますが、並列クエリの実行でしか利用できません。  
  
## <a name="see-also"></a>参照  
 [列ストア インデックスの設計ガイダンス](../../relational-databases/indexes/columnstore-indexes-design-guidance.md)   
 [列ストア インデックスのデータ読み込みガイダンス](../../relational-databases/indexes/columnstore-indexes-data-loading-guidance.md)   
 [列ストア インデックスのクエリ パフォーマンス](../../relational-databases/indexes/columnstore-indexes-query-performance.md)   
 [列ストアを使用したリアルタイム運用分析の概要](../../relational-databases/indexes/get-started-with-columnstore-for-real-time-operational-analytics.md)   
 [データ ウェアハウスの列ストア インデックス](../../relational-databases/indexes/columnstore-indexes-data-warehouse.md)   
 [インデックスの再編成と再構築](../../relational-databases/indexes/reorganize-and-rebuild-indexes.md)
  
