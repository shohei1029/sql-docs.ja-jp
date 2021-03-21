---
description: '列ストア インデックス: 概要'
title: '列ストア インデックス: 概要 | Microsoft Docs'
ms.custom: ''
ms.date: 05/08/2020
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: table-view-index
ms.topic: conceptual
helpviewer_keywords:
- indexes creation, columnstore
- indexes [SQL Server], columnstore
- columnstore index
- batch mode execution
- columnstore index, described
- xVelocity, columnstore indexes
ms.assetid: f98af4a5-4523-43b1-be8d-1b03c3217839
author: MikeRayMSFT
ms.author: mikeray
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: bbc4cb7f94fdbd2788005f1e277e70b7b0c4247d
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104744272"
---
# <a name="columnstore-indexes-overview"></a>列ストア インデックス: 概要
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

列ストア インデックスは、大規模なデータ ウェアハウス ファクト テーブルを格納し、そのテーブルにクエリを実行する際の標準となります。 このインデックスは列ベースのデータ ストレージとクエリ処理を使用して、従来の行指向ストレージと比較して最大 10 倍のクエリ パフォーマンスをデータ ウェアハウスで実現します。 また、非圧縮データのサイズと比較して最大で 10 倍のデータ圧縮を実現できます。 [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] SP1 以降、列ストア インデックスでは運用分析が可能になりました。トランザクション ワークロードでパフォーマンスの高いリアルタイム分析を実行することができます。  
  
関連するシナリオについての詳細は次のとおりです。  
  
-   [データ ウェアハウスの列ストア インデックス](../../relational-databases/indexes/columnstore-indexes-data-warehouse.md)  
-   [列ストアを使用したリアルタイム運用分析の概要](../../relational-databases/indexes/get-started-with-columnstore-for-real-time-operational-analytics.md)  
  
## <a name="what-is-a-columnstore-index"></a>列ストア インデックスとは  
列ストア インデックスは、列指向データ形式を使用してデータを格納、取得、および管理するためのテクノロジです ("*列ストア*" と呼ばれます)。  
  
### <a name="key-terms-and-concepts"></a>主な用語と概念  
ここでは、列ストア インデックスに関連する主な用語と概念について説明します。  
  
#### <a name="columnstore"></a>列ストア
列ストアは、行と列を含むテーブルとして論理的に編成され、列方向のデータ形式で物理的に格納されているデータです。  
  
#### <a name="rowstore"></a>行ストア
行ストアは、行と列を含むテーブルとして論理的に編成され、行方向のデータ形式で物理的に格納されているデータです。 この形式は、リレーショナル テーブル データを格納する従来の方法です。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、行ストアは、基になるデータ ストレージ形式が、ヒープ、クラスター化インデックス、またはメモリ最適化テーブルであるテーブルを示します。  
  
> [!NOTE]  
> 列ストア インデックスの説明では、データ ストレージの形式を強調するために行ストアと列ストア という用語が使用されます。  
  
#### <a name="rowgroup"></a>行グループ
行グループは、同時に列ストア形式に圧縮される行のグループです。 通常、1 つの行グループには、行グループあたりの最大行数である 1,048,576 行が含まれます。  
  
高パフォーマンスと高い圧縮率を実現するために、列ストア インデックスは、テーブルを行グループにスライスし、各行グループを列方向に圧縮します。 行グループ内の行数は、高い圧縮率が実現される程度に多く、インメモリ操作の利点を得られる程度に少なくなければなりません。    

すべてのデータが削除された行グループは、COMPRESSED 状態から TOMBSTONE 状態に移行し、後で組ムーバーというバックグラウンド プロセスによって削除されます。 行グループの状態の詳細については、「[sys.dm_db_column_store_row_group_physical_stats (Transact-SQL)](../../relational-databases/system-dynamic-management-views/sys-dm-db-column-store-row-group-physical-stats-transact-sql.md)」を参照してください。

> [!TIP]
> 小さな行グループが多すぎると、列ストア インデックスの品質が低下します。 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] までは、削除された行を削除して圧縮された行グループを結合する方法を決定する内部しきい値ポリシーに従って、小さな COMPRESSED 行グループをマージするには、再編成操作が必要です。    
> [!INCLUDE[sql-server-2019](../../includes/sssql19-md.md)] 以降では、多数の行が削除された COMPRESSED 行グループをマージするには、バックグラウンド マージ タスクも利用できます。     
> 小さい行グループをマージすると、インデックスの品質が改善されます。 

> [!NOTE]
> [!INCLUDE[sql-server-2019](../../includes/sssql19-md.md)] 以降、組ムーバーは、内部しきい値によってしばらくの間存在していると判断された小さな OPEN デルタ行グループを自動的に圧縮するか、多数の行が削除されている COMPRESSED 行グループをマージするバックグラウンド マージ タスクによってサポートされています。 これにより、時間の経過とともに、列ストア インデックスの品質が向上します。         

#### <a name="column-segment"></a>列セグメント
列セグメントは、行グループ内のデータ列です。  
  
-   それぞれの行グループには、テーブルの 1 つの列につき 1 つの列セグメントが含まれます。  
-   それぞれの列セグメントは一緒に圧縮され、物理メディアに格納されます。  
  
![列セグメント](../../relational-databases/indexes/media/sql-server-pdw-columnstore-columnsegment.gif "列セグメント")  
  
#### <a name="clustered-columnstore-index"></a>クラスター化列ストア インデックス
クラスター化列ストア インデックスは、テーブル全体に対する物理ストレージです。    
  
![クラスター化列ストア インデックス](../../relational-databases/indexes/media/sql-server-pdw-columnstore-physicalstorage.gif "クラスター化列ストア インデックス")  
  
列セグメントの断片化を低減し、パフォーマンスを高めるために、列ストア インデックスでは、一部のデータを、クラスター化インデックス ("*デルタストア*" と呼ばれます) と削除された行の ID の btree リストに格納することがあります。 デルタストア操作は内部で処理されます。 列ストア インデックスは、正しいクエリ結果を返すために、列ストアとデルタストアの両方からのクエリ結果を結合します。  
  
#### <a name="delta-rowgroup"></a>デルタ行グループ
デルタ行グループは、列ストア インデックスでのみ使用されるクラスター化 B ツリー インデックスです。 これは、行数がしきい値 (1,048,576 行) に達して列ストアに移動できるまで行を格納することで、列ストアの圧縮とパフォーマンスを高めます。  

デルタ行グループが行数の上限に達すると、OPEN 状態から CLOSED 状態に移行します。 組ムーバーというバックグラウンド プロセスによって、閉じられた行グループがチェックされます。 プロセスによって閉じられた行グループが検出されると、デルタ行グループは圧縮され、COMPRESSED 行グループとして列ストアに格納されます。 

デルタ行グループが圧縮されると、既存のデルタ行グループは TOMBSTONE 状態に移行し、参照がない場合は組ムーバーによって後で削除されます。 

行グループの状態の詳細については、「[sys.dm_db_column_store_row_group_physical_stats (Transact-SQL)](../../relational-databases/system-dynamic-management-views/sys-dm-db-column-store-row-group-physical-stats-transact-sql.md)」を参照してください。 

> [!NOTE]
> [!INCLUDE[sql-server-2019](../../includes/sssql19-md.md)] 以降、組ムーバーは、内部しきい値によってしばらくの間存在していると判断された小さな OPEN デルタ行グループを自動的に圧縮するか、多数の行が削除されている COMPRESSED 行グループをマージするバックグラウンド マージ タスクによってサポートされています。 これにより、時間の経過とともに、列ストア インデックスの品質が向上します。         
  
#### <a name="deltastore"></a>デルタストア
列ストア インデックスは、複数のデルタ行グループを持つことができます。 すべてのデルタ行グループを総称して、デルタストアと呼びます。   

大規模な一括読み込みでは、行のほとんどがデルタストアを通らずに列ストアに直接移動します。 一括読み込みの最後に位置する行の数は、行グループの最小サイズである 102,400 行を満たすには足りないことがあります。 その結果、それらの行は列ストアではなくデルタストアに移動します。 102,400 行未満の小規模な一括読み込みでは、すべての行がデルタストアに直接移動します。  
  
#### <a name="nonclustered-columnstore-index"></a>非クラスター化列ストア インデックス
非クラスター化列ストア インデックスとクラスター化列ストアインデックスは同じように機能します。 異なるのは、非クラスター化列ストア インデックスが行ストア テーブルに作成されたセカンダリ インデックスであるのに対し、クラスター化インデックスはテーブル全体のプライマリ ストレージである点です。  
  
非クラスター化インデックスには、基になるテーブルの行と列の一部または全体のコピーが含まれています。 インデックスはテーブルの 1 つ以上の列として定義され、行のフィルター処理条件をオプションで設定できます。  
  
非クラスター化列ストア インデックスによりリアルタイム運用分析が可能になります。ここで、OLTP ワークロードは基になるクラスター化インデックスを使用します。一方、列ストア インデックスでは同時に分析が実行されます。 詳細については、「[列ストアを使用したリアルタイム運用分析の概要](../../relational-databases/indexes/get-started-with-columnstore-for-real-time-operational-analytics.md)」をご覧ください。  
  
#### <a name="batch-mode-execution"></a>バッチ モード実行
バッチ モード実行は、複数の行をまとめて処理するためのクエリ処理方法です。 バッチ モード実行は、列ストア ストレージ形式と緊密に統合され、このストレージ形式に合わせて最適化されています。 バッチ モード実行は、"*ベクター ベースの*" 実行、または "*ベクター化された*" 実行と呼ばれることもあります。 列ストア インデックスのクエリではバッチ モード実行が使用され、これによりクエリ パフォーマンスが、通常、2 から 4 倍向上します。 詳細については、「[クエリ処理アーキテクチャ ガイド](../query-processing-architecture-guide.md#execution-modes)」をご覧ください。 
  
##  <a name="why-should-i-use-a-columnstore-index"></a><a name="benefits"></a> 列ストア インデックスを使用する理由  
列ストア インデックスにより、非常に高いレベルでデータ圧縮が実現し (通常 10 倍)、データ ウェアハウスのストレージ コストが大幅に削減されます。 分析においても、列ストア インデックスは btree インデックスと比べて桁違いに優れたパフォーマンスを発揮します。 列ストア インデックスは、データ ウェアハウスと分析のワークロードに対して推奨されるデータ ストレージ形式です。 [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)]以降、運用ワークロードにおけるリアルタイム分析で列ストア インデックスを使用できます。  
  
列ストア インデックスが高速に動作する理由:  
  
-   列には同じドメインの値 (一般的に似たような値) が格納されます。これにより圧縮率が上がります。 システム内における I/O のボトルネックが最小化または除外され、メモリ使用量が大幅に減少します。  
  
-   高い圧縮比率により、メモリ使用量が削減され、クエリのパフォーマンスが向上します。 さらに、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] がより多くのクエリやデータ操作をインメモリで実行できるため、クエリのパフォーマンスを向上させることが可能です。  
  
-   バッチ実行では、複数の行をまとめて処理することで、クエリのパフォーマンスを高めます (通常 2 から 4 倍)。  
  
-   クエリはテーブルから少数の列のみを選択することが多く、物理メディアからの合計 I/O を低減します。  
  
## <a name="when-should-i-use-a-columnstore-index"></a>列ストア インデックスを使用するタイミング  
推奨されるユース ケース  
  
-   クラスター化列ストア インデックスを使用して、データ ウェアハウス ワークロード用にファクト テーブルと大きなディメンション テーブルを格納します。 この方法により、クエリのパフォーマンスとデータ圧縮が最大で 10 倍向上します。 詳しくは、「[データ ウェアハウスの列ストア インデックス](~/relational-databases/indexes/columnstore-indexes-data-warehouse.md)」をご覧ください。  
  
-   非クラスター化列ストア インデックスを使用して、OLTP ワークロードでリアルタイム分析を実行します。 詳細については、「[列ストアを使用したリアルタイム運用分析の概要](../../relational-databases/indexes/get-started-with-columnstore-for-real-time-operational-analytics.md)」をご覧ください。  
  
### <a name="how-do-i-choose-between-a-rowstore-index-and-a-columnstore-index"></a>行ストア インデックスと列ストア インデックスはどのように選択すればよいですか。  
行ストア インデックスは、データをシークして特定の値を検索するクエリや、狭い範囲の値でのクエリを実行する場合に、最適なパフォーマンスを発揮します。 トランザクション ワークロードでは、テーブル スキャンではなく主にテーブル シークを必要とする傾向があるため、行ストア インデックスを使用してください。  
  
列ストア インデックスは、特に大規模なテーブルで、大量のデータをスキャンする分析クエリを実行するときにパフォーマンスが高くなります。 この列ストア インデックスは、特にファクト テーブルのデータ ウェアハウスと分析のワークロードで使用します。ファクト テーブルでは、テーブル シークではなく完全なテーブル スキャンが必要になることが多いためです。  
  
### <a name="can-i-combine-rowstore-and-columnstore-on-the-same-table"></a>行ストアと列ストアを同じテーブルで結合できますか。  
はい。 [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 以降、更新可能な非クラスター化列ストア インデックスを、行ストア テーブルに作成できます。 列ストア インデックスには選択された列のコピーが格納されるため、このデータ用に追加の容量が必要になります。ただし、選択されたデータは平均で 10 倍に圧縮されます。 列ストア インデックスの分析と行ストア インデックスのトランザクションを同時に実行できます。 行ストア テーブルでデータが変更されると列ストアが更新されます。このため、両方のインデックスが同じデータに対して作業を行うことになります。  
  
[!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 以降、列ストア インデックス上に 1 つまたは複数の非クラスター化行ストア インデックスを持たせて、基になる列ストア上で効率的にテーブルを検索できるようになりました。 他のオプションも使用できます。 たとえば、行ストア テーブルで UNIQUE 制約を使用することで、主キー制約を適用できます。 一意でない値は行ストア テーブルに挿入できないため、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] で列ストアにその値を挿入することはできません。  
  
## <a name="metadata"></a>Metadata  
列ストア インデックス内のすべての列は、付加列としてメタデータに格納されます。 列ストア インデックスはキー列を持ちません。  

:::row:::
    :::column:::
        [sys.indexes &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-indexes-transact-sql.md)
    :::column-end:::
    :::column:::
        [sys.index_columns &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-index-columns-transact-sql.md)
    :::column-end:::
:::row-end:::  
:::row:::
    :::column:::
        [sys.partitions &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-partitions-transact-sql.md)
    :::column-end:::
    :::column:::
        [sys.internal_partitions &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-internal-partitions-transact-sql.md)
    :::column-end:::
:::row-end:::  
:::row:::
    :::column:::
        [sys.column_store_segments &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-column-store-segments-transact-sql.md)
    :::column-end:::
    :::column:::
        [sys.column_store_dictionaries &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-column-store-dictionaries-transact-sql.md)
    :::column-end:::
:::row-end:::  
:::row:::
    :::column:::
        [sys.column_store_row_groups &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-column-store-row-groups-transact-sql.md)
    :::column-end:::
    :::column:::
        [sys.dm_db_column_store_row_group_operational_stats &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-column-store-row-group-operational-stats-transact-sql.md)
    :::column-end:::
:::row-end:::  
:::row:::
    :::column:::
        [sys.dm_db_column_store_row_group_physical_stats &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-column-store-row-group-physical-stats-transact-sql.md)
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
        [sys.dm_db_index_operational_stats &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-index-operational-stats-transact-sql.md)
    :::column-end:::
:::row-end:::  
:::row:::
    :::column:::
        [sys.dm_db_index_physical_stats &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-index-physical-stats-transact-sql.md)
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::
 

## <a name="related-tasks"></a>関連タスク  
クラスター化列ストア インデックスとしてリレーショナル テーブルを指定していない限り、そのリレーショナル テーブルでは、行ストアが、基になるデータ形式として使用されます。 `WITH CLUSTERED COLUMNSTORE INDEX` オプションを指定しない場合、`CREATE TABLE` によって行ストア テーブルが作成されます。  
  
`CREATE TABLE` ステートメントでテーブルを作成する際に、`WITH CLUSTERED COLUMNSTORE INDEX` オプションを指定することでそのテーブルを列ストアとして作成できます。 既に、行ストア テーブルがある場合、その行ストアは、`CREATE COLUMNSTORE INDEX` ステートメントを使用して列ストアに変換できます。  
  
|タスク|参照トピック|Notes|  
|----------|----------------------|-----------|  
|テーブルを列ストアとして作成する。|[CREATE TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/create-table-transact-sql.md)|[!INCLUDE[sssql16-md](../../includes/sssql16-md.md)]以降、テーブルをクラスター化列ストア インデックスとして作成できます。 最初に行ストア テーブルを作成し、次に列ストアに変換する必要はありません。|  
|列ストア インデックスを持つメモリ テーブルを作成します。|[CREATE TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/create-table-transact-sql.md)|[!INCLUDE[sssql16-md](../../includes/sssql16-md.md)]以降、列ストア インデックスを持つ、メモリ最適化テーブルを作成できます。 列ストア インデックスは、テーブルの作成後に `ALTER TABLE ADD INDEX` 構文を使用して追加することもできます。|  
|行ストア テーブルを列ストアに変換する。|[CREATE COLUMNSTORE INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/create-columnstore-index-transact-sql.md)|既存のヒープまたはバイナリ ツリーを列ストアに変換します。 この変換を実行するときの既存のインデックスとインデックス名の処理方法を例示します。|  
|列ストア テーブルを行ストアに変換する。|[CREATE CLUSTERED INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/create-columnstore-index-transact-sql.md#d-convert-a-columnstore-table-to-a-rowstore-table-with-a-clustered-index)、[列ストア テーブルを行ストア ヒープに戻す](../../t-sql/statements/create-columnstore-index-transact-sql.md#e-convert-a-columnstore-table-back-to-a-rowstore-heap) |この変換は通常は必要ありませんが、状況によっては必要になる場合があります。 列ストアをヒープまたはクラスター化インデックスに変換する方法を例示します。|  
|行ストア テーブルで列ストア インデックスを作成する。|[CREATE COLUMNSTORE INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/create-columnstore-index-transact-sql.md)|行ストア テーブルでは列ストア インデックスを 1 つ使用できます。 [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)]以降、列ストア インデックスにフィルター条件を指定できるようになりました。 基本構文を例示します。|  
|運用分析のパフォーマンスの高いインデックスを作成する。|[列ストアを使用したリアルタイム運用分析の概要](../../relational-databases/indexes/get-started-with-columnstore-for-real-time-operational-analytics.md)|補完的な列ストア インデックスと btree インデックスを作成する方法について説明します。OLTP クエリでは btree インデックスが使用され、分析クエリでは列ストア インデックスが使用されます。|  
|データ ウェアハウス用のパフォーマンスの高い列ストア インデックスを作成する。|[データ ウェアハウスの列ストア インデックス](~/relational-databases/indexes/columnstore-indexes-data-warehouse.md)|列ストア テーブルで btree インデックスを使用して、パフォーマンスの高いデータ ウェアハウス クエリを作成する方法について説明します。|  
|btree インデックスを使用して列ストア インデックスに主キー制約を適用する|[データ ウェアハウスの列ストア インデックス](~/relational-databases/indexes/columnstore-indexes-data-warehouse.md)|btree インデックスと列ストア インデックスを組み合わせて、列ストア インデックスに主キー制約を適用する方法を示します。|  
|列ストア インデックスを削除する。|[DROP INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/drop-index-transact-sql.md)|列ストア インデックスを削除するには、btree インデックスが使用する標準の `DROP INDEX` 構文を使用します。 クラスター化列ストア インデックスを削除すると、列ストア テーブルがヒープに変換されます。|  
|列ストア インデックスから行を削除する。|[DELETE &#40;Transact-SQL&#41;](../../t-sql/statements/delete-transact-sql.md)|[DELETE &#40;Transact-SQL&#41;](../../t-sql/statements/delete-transact-sql.md) を使用して行を削除します。<br /><br /> **列ストアの行**: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] は行を論理的に削除されたとしてマークしますが、インデックスが再構築されるまで行の物理ストレージを再確保することはありません。<br /><br /> **デルタストアの行**: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] は論理的および物理的に行を削除します。|  
|列ストア インデックスの行を更新する。|[UPDATE &#40;Transact-SQL&#41;](../../t-sql/queries/update-transact-sql.md)|[UPDATE &#40;Transact-SQL&#41;](../../t-sql/queries/update-transact-sql.md) を使用して行を更新します。<br /><br /> **列ストアの行**: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] は行を論理的に削除されたとしてマークし、更新された行をデルタストアに挿入します。<br /><br /> **デルタストアの行**: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] は、デルタストアの行を更新します。|  
|データを列ストア インデックスに読み込む。|[列ストア インデックス データの読み込み](~/relational-databases/indexes/columnstore-indexes-data-loading-guidance.md)||  
|デルタストアのすべての行を強制的に列ストアに移動します。|[ALTER INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/alter-index-transact-sql.md) ... `REBUILD`<br /><br /> [インデックスの再編成と再構築](../../relational-databases/indexes/reorganize-and-rebuild-indexes.md)|`ALTER INDEX` に `REBUILD` オプションを指定すると、すべての行が列ストアに強制的に移動されます。|  
|列ストア インデックスを最適化する。|[ALTER INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/alter-index-transact-sql.md)|`ALTER INDEX ... REORGANIZE` は、列ストア インデックスをオンラインで最適化します。|  
|テーブルと列ストア インデックスをマージする。|[MERGE &#40;Transact-SQL&#41;](../../t-sql/statements/merge-transact-sql.md)||  
  
## <a name="see-also"></a>関連項目  
 [列ストア インデックス データの読み込み](~/relational-databases/indexes/columnstore-indexes-data-loading-guidance.md)   
 [列ストア インデックスのバージョン管理機能の概要](~/relational-databases/indexes/columnstore-indexes-what-s-new.md)   
 [列ストア インデックスのクエリ パフォーマンス](~/relational-databases/indexes/columnstore-indexes-query-performance.md)   
 [列ストアを使用したリアルタイム運用分析の概要](../../relational-databases/indexes/get-started-with-columnstore-for-real-time-operational-analytics.md)   
 [データ ウェアハウスの列ストア インデックス](~/relational-databases/indexes/columnstore-indexes-data-warehouse.md)   
 [列ストア インデックスの最適化](~/relational-databases/indexes/columnstore-indexes-defragmentation.md)   
 [SQL Server インデックス デザイン ガイド](../../relational-databases/sql-server-index-design-guide.md)   
 [列ストア インデックスのアーキテクチャ](../../relational-databases/sql-server-index-design-guide.md#columnstore_index)   
  
  
