---
description: 列ストア インデックス - 設計ガイダンス
title: 列ストア インデックス - 設計ガイダンス | Microsoft Docs
ms.custom: ''
ms.date: 12/01/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: table-view-index
ms.topic: conceptual
ms.assetid: fc3e22c2-3165-4ac9-87e3-bf27219c820f
author: MikeRayMSFT
ms.author: mikeray
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: af996a3d65120fd2306d61b43b32a63486b45756
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104750742"
---
# <a name="columnstore-indexes---design-guidance"></a>列ストア インデックス - 設計ガイダンス
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

列ストア インデックスの設計に関する概要レベルの推奨事項です。 設計に関する少数の適切な意思決定は、列ストア インデックスが提供するように設計されている高いデータ圧縮率とクエリ パフォーマンスの実現に役立ちます。 

## <a name="prerequisites"></a>前提条件

この記事では、列ストアのアーキテクチャと用語に精通していることを想定しています。 詳細については、「[列ストア インデックス - 概要](../../relational-databases/indexes/columnstore-indexes-overview.md)」と「[列ストア インデックスのアーキテクチャ](../../relational-databases/sql-server-index-design-guide.md#columnstore_index)」を参照してください。

### <a name="know-your-data-requirements"></a>データ要件を認識する
列ストア インデックスを設計する前に、データ要件について可能な限り理解してください。 たとえば、次の質問に対する答えを考えてみてください。

- テーブルの大きさはどれくらいか。
- 自分のクエリは主に広範囲の値をスキャンする分析を実行するか。  列ストア インデックスは、特定の値の検索ではなく、広範囲のスキャンに対してうまく機能するように設計されています。
- 自分のワークロードは多数の更新と削除を実行するか。 列ストア インデックスは、データが安定しているときにうまく機能します。 クエリが更新および削除するのは行の 10% 未満である必要があります。
- データ ウェアハウスのファクト テーブルとディメンション テーブルがあるか。
- トランザクション ワークロードの分析を実行する必要があるか。 必要がある場合は、リアルタイム運用分析について列ストアの設計ガイダンスをご覧ください。

列ストア インデックスは必要でない場合があります。 ヒープまたはクラスター化インデックスを持つ行ストア テーブルは、データをシークするクエリ、特定の値の検索、狭い範囲の値でのクエリを実行するときに最適なパフォーマンスを発揮します。 トランザクション ワークロードでは、広範囲のテーブル スキャンではなく主にテーブル シークを必要とする傾向があるため、行ストア インデックスを使用してください。  

## <a name="choose-the-best-columnstore-index-for-your-needs"></a>ニーズに最適な列ストア インデックスを選ぶ

列ストア インデックスは、クラスター化または非クラスター化です。  クラスター化列ストア インデックスでは、1 つ以上の非クラスター化 B ツリー インデックスを使用できます。 列ストア インデックスは、簡単に試すことができます。 テーブルを列ストア インデックスとして作成する場合は、列ストア インデックスを削除して、テーブルを行ストア テーブルに簡単に変換できます。 

オプションと推奨事項の概要を次に示します。 

| 列ストア オプション | 使用する場合に関する推奨事項 | 圧縮 |
| :----------------- | :------------------- | :---------- |
| クラスター化列ストア インデックス | 用途:<br></br>1) スター スキーマまたはスノーフレーク スキーマがある従来のデータ ウェアハウス ワークロード<br></br>2) 大量のデータを挿入し、更新と削除は最小限であるモノのインターネット (IoT)。 | 平均 10 倍 |
| クラスター化列ストア インデックスの非クラスター化 B ツリー インデックス | 以下の操作を実行するために使用します。<br></br>    1.クラスター化列ストア インデックスに主キーと外部キーの制約を適用します。<br></br>    2.特定の値または小さい範囲の値を検索するクエリを高速化します。<br></br>    3.特定の行の更新と削除を高速化します。| 平均 10 倍に加えて、NCI 用の追加ストレージ。|
| ディスクベースのヒープまたは B ツリー インデックスの非クラスター化列ストア インデックス | 用途: <br></br>1) いくつかの分析クエリがある OLTP ワークロード。 分析用に作成された B ツリー インデックスを削除し、1 つの非クラスター化列ストアインデックスで置き換えることができます。<br></br>2) 抽出、変換、および読み込み (ETL) 操作を実行してデータを別のデータ ウェアハウスに移動する多くの従来の OLTP ワークロード。 一部の OLTP テーブルに非クラスター化列ストア インデックスを作成すると、ETL と個別のデータ ウェアハウスを除外できます。 | NCCI は、平均で 10% 以上のストレージを必要とする追加のインデックスです。|
| メモリ内のテーブルの列ストア インデックス | ベース テーブルがメモリ内テーブルであることを除いて、ディスク ベース テーブルの非クラスター化列ストア インデックスと同じ推奨事項。 | 列ストア インデックスは追加のインデックスです。|

## <a name="use-a-clustered-columnstore-index-for-large-data-warehouse-tables"></a>大規模なデータ ウェアハウス テーブルにはクラスター化列ストア インデックスを使用する
クラスター化列ストア インデックスは複数のインデックスであり、主テーブル ストレージです。 大規模なデータ ウェアハウスのファクト テーブルとディメンション テーブルに対する高いデータ圧縮率とクエリ パフォーマンスの大幅な向上を実現します。 クラスター化列ストア インデックスは、トランザクション クエリよりも分析クエリに最適です。分析クエリは、特定の値の検索よりも、広範囲の値に対して操作を実行する傾向があるためです。 

次の場合は、クラスター化列ストア インデックスの使用を検討してください。

- 各パーティションに少なくとも 100 万行がある場合。 列ストア インデックスでは、各パーティション内に行グループがあります。 各パーティション内の行グループを満たすにはテーブルが小さすぎる場合、列ストアの圧縮とクエリ パフォーマンスのメリットは得られません。
- クエリが主に値の範囲に対する分析を実行する場合。 たとえば、列の平均値を検索するには、クエリですべての列値をスキャンする必要があります。 次に、値を加算して集計し、平均値を判断します。
- 挿入のほとんどが大量のデータに対するもので、更新と削除は最小限である場合。 モノのインターネット (IoT) などの多くのワークロードは大量のデータを挿入し、更新と削除は最小限です。 これらのワークロードは、クラスター化列ストア インデックスを使用する場合の圧縮とクエリ パフォーマンスのメリットを利用できます。

次の場合はクラスター化列ストア インデックスを使用しないでください。

* テーブルに、varchar(max)、nvarchar(max)、varbinary(max) データ型が必要な場合。 または、これらの列が含まれないように列ストア インデックスを設計します。
* テーブル データが永続的でない場合。 データをすばやく格納および削除する必要がある場合は、ヒープまたは一時テーブルの使用を検討してください。
* テーブルの行数がパーティションあたり 100 万未満の場合。 
* 更新と削除がテーブルに対する操作の 10% を超える場合。 大量の更新と削除は断片化の原因となります。 すべてのデータを列ストアに強制的に入れて断片化を解消する再編成という操作を実行するまで、断片化は圧縮率とクエリ パフォーマンスに影響します。 詳しくは、[列ストア インデックスでインデックスの断片化を最小限に抑える方法](/archive/blogs/sqlserverstorageengine/columnstore-index-defragmentation-using-reorganize-command)に関する記事をご覧ください。

詳しくは、「[Columnstore indexes - data warehousing](../../relational-databases/indexes/columnstore-indexes-data-warehouse.md)」(列ストア インデックス - データ ウェアハウス) をご覧ください。

## <a name="add-b-tree-nonclustered-indexes-for-efficient-table-seeks"></a>B ツリー 非クラスター化インデックスを追加してテーブルのシークを効率化する

[!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 以降では、クラスター化列ストア インデックスのセカンダリ インデックスとして非クラスター化 B ツリー インデックスを作成できます。 列ストア インデックスが変更されると、非クラスター化 B ツリー インデックスが更新されます。 これは、うまく利用するとメリットのある強力な機能です。 

セカンダリ B ツリー インデックスを使用すると、すべての行をスキャンせずに特定の行を効率的に検索できます。  他のオプションも使用できます。 たとえば、B ツリー インデックスで UNIQUE 制約を使用して、主キーまたは外部キー制約を適用できます。 一意でない値は B ツリー インデックスに挿入できないため、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] でその値を列ストアに挿入することはできません。 

列ストア インデックスの B ツリー インデックスは次の目的で使用することを検討してください。
* 特定の値または小さい範囲の値を検索するクエリを実行する。
* 主キー制約や外部キー制約などの制約を適用する。
* 更新や削除の操作を効率的に実行する。 B ツリー インデックスは、テーブルまたはテーブルのパーティション全体をスキャンせずに、更新や削除のために特定の行をすばやく検索できます。
* B ツリー インデックスの格納に使用可能な追加の記憶域がある。

## <a name="use-a-nonclustered-columnstore-index-for-real-time-analytics"></a>非クラスター化列ストア インデックスを使用したリアルタイム分析

[!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 以降では、行ストア ディスク ベース テーブルまたはメモリ内 OLTP テーブルで非クラスター化列ストア インデックスを使用できます。 これにより、トランザクション テーブルに対して分析をリアルタイムで実行できるようになります。 トランザクションは基になるテーブルで発生しますが、列ストア インデックスに対して分析を実行できます。 1 つのテーブルで両方のインデックスを管理するため、行ストアと列ストア両方のインデックスに対して変更をリアルタイムで使用できます。

列ストア インデックスは行ストア インデックスよりもデータ圧縮率が 10 倍高いため、必要な追加ストレージがわずかで済みます。 たとえば、圧縮された行ストア テーブルが 20 GB を必要とする場合、列ストア インデックスではさらに 2 GB 必要な場合があります。 必要な追加領域は、非クラスター化列ストア インデックス内の列数によっても異なります。 

 次の場合は、非クラスター化列ストア インデックスの使用を検討してください。

* トランザクション行ストア テーブルに対して分析をリアルタイムで実行する場合。 分析用に設計された既存の B ツリー インデックスを非クラスター化列ストア インデックスで置き換えることができます。 
  
*   別のデータ ウェアハウスの必要をなくす場合。 従来、企業では行ストア テーブルでトランザクションを実行し、データを別のデータ ウェアハウスに読み込んで分析を実行しています。 多くのワークロードでは、トランザクション テーブルに非クラスター化列ストア インデックスを作成して、読み込みプロセスと別のデータ ウェアハウスを排除できます。

  [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] には、このシナリオのパフォーマンスを高めるいくつかの方法が用意されています。 OLTP アプリケーションを変更せずに非クラスター化列ストア インデックスを有効にできるため、とても簡単に試すことができます。 

処理リソースを追加するには、読み取り可能なセカンダリに対して分析を実行できます。 読み取り可能なセカンダリを使用すると、トランザクションのワークロードと分析ワークロードの処理が分離されます。 

詳しくは、「[Get started with columnstore indexes for real-time operational analytics](../../relational-databases/indexes/get-started-with-columnstore-for-real-time-operational-analytics.md)」(列ストア インデックスを使用したリアルタイム運用分析の概要) をご覧ください

最適な列ストア インデックスの選択について詳しくは、Sunil Agarwal のブログで「[Which columnstore index is right for my workload?](/archive/blogs/sql_server_team/columnstore-index-which-columnstore-index-is-right-for-my-workload)」(自分のワークロードに適した列ストア インデックス) をご覧ください。

## <a name="use-table-partitions-for-data-management-and-query-performance"></a>テーブルのパーティションを使用してデータ管理とクエリのパフォーマンスを高める
列ストア インデックスでは、データの管理とアーカイブの優れた方法であるパーティション分割がサポートされます。 パーティション分割では、操作を 1 つ以上のパーティションに制限することでクエリのパフォーマンスも向上します。

### <a name="use-partitions-to-make-the-data-easier-to-manage"></a>パーティションを使用してデータを管理しやすくする
大規模なテーブルの場合、データの範囲を管理する唯一の現実的な方法は、パーティションの使用です。 行ストア テーブルのパーティションの利点は、列ストア インデックスにも適用されます。 

たとえば、行ストアと列ストアの両方のテーブルは、次の目的でパーティションを使用します。

- 増分バックアップのサイズを制御する。 パーティションをバックアップしてファイル グループを分離し、読み取り専用としてマークできます。 これにより、今後のバックアップでは読み取り専用ファイル グループがスキップされます。 
- 古いパーティションを低コストのストレージに移動してストレージ コストを節約する。 たとえば、パーティションの切り替えを使用して、パーティションを低コストの記憶域の場所に移動できます。
- パーティションに対する操作を制限して操作を効率的に実行する。 たとえば、インデックスのメンテナンスのために、断片化されたパーティションのみを対象とすることができます。

さらに、列ストア インデックスでは、パーティション分割を次の目的で使用します。

* ストレージ コストをさらに 30% を節約する。 COLUMNSTORE_ARCHIVE 圧縮オプションを使用して古いパーティションを圧縮することができます。 データのクエリ パフォーマンスは低下しますが、パーティションのクエリ頻度が低い場合は許容できます。

### <a name="use-partitions-to-improve-query-performance"></a>パーティションを使用してクエリのパフォーマンスを向上させる

パーティションを使用して、スキャンするクエリを特定のパーティションのみに制限して、スキャンする行数を制限できます。 たとえば、インデックスが年でパーティション分割され、クエリが昨年のデータを分析する場合、スキャンする必要があるのは 1 つのパーティション内のデータだけです。 

### <a name="use-fewer-partitions-for-a-columnstore-index"></a>列ストア インデックスに使用するパーティションを少なくする

データ サイズが十分に大きくない限り、列ストア インデックスは、行ストア インデックスに使用するよりも少ないパーティションで最高のパフォーマンスを発揮します。 パーティションごとに少なくとも 100 万行があるのでない場合、行のほとんどは、列ストア圧縮のパフォーマンス上のメリットがないデルタストアに入ります。 たとえば、10 個のパーティションがあり、各パーティションが 100,000 行を受け取るテーブルに 100万行を読み込んだ場合は、すべての行がデルタ行グループに入ります。 

例:
* 1,000,000 行を 1 つのパーティションまたは非パーティション テーブルに読み込みます。 1,000,000 行を含む 1 つの圧縮行グループを取得します。 これは、高いデータ圧縮と高速なクエリ パフォーマンスに優れています。
* 1,000,000 行を 10 個のパーティションに均等に読み込みます。 各パーティションは、列ストア圧縮の最小しきい値未満である 100,000 行を受け取ります。 その結果、列ストア インデックスには、それぞれ 100,000 行を含む 10 個のデルタ行グループがある可能性があります。 デルタ行グループを列ストアに強制的に移動する方法があります。 ただし、これらが列ストア インデックス内の行のみである場合、圧縮された行グループは、最適な圧縮とクエリ パフォーマンスを発揮するには小さくなりすぎます。

パーティション分割について詳しくは、Sunil Agarwal のブログ記事「[Should I partition my columnstore index?](/archive/blogs/sqlserverstorageengine/columnstore-index-should-i-partition-my-columnstore-index)」(自分の列ストア インデックスは分割する必要があるか) をご覧ください。

## <a name="choose-the-appropriate-data-compression-method"></a>適切なデータの圧縮方法を選ぶ
列ストア インデックスは、データ圧縮に関する 2 つの選択肢 (列ストア圧縮とアーカイブ圧縮) を提供しています。 圧縮オプションは、インデックスの作成時、または後で [ALTER INDEX ...REBUILD](../../t-sql/statements/alter-index-transact-sql.md) を使用して変更するときに選択できます。

### <a name="use-columnstore-compression-for-best-query-performance"></a>列ストア圧縮を使用して最適なクエリ パフォーマンスを実現する
列ストア圧縮は通常、行ストア インデックスよりも 10 倍高い圧縮率を実現します。 これは列ストア インデックスの標準的な圧縮方法で、高速なクエリ パフォーマンスを実現します。 

### <a name="use-archive-compression-for-best-data-compression"></a>アーカイブ圧縮を使用して最適なデータ圧縮を実現する
アーカイブ圧縮は、クエリのパフォーマンスが重要ではないときに、圧縮率を最大化するように設計されています。 列ストア圧縮よりも高いデータ圧縮率を実現しますが、価格は高くなります。 データの圧縮と圧縮解除に時間がかかるため、高速なクエリ パフォーマンスには適していません。 

## <a name="use-optimizations-when-you-convert-a-rowstore-table-to-a-columnstore-index"></a>行ストア テーブルを列ストア インデックスに変換するときに最適化を使用する

データが行ストア テーブルに既に存在する場合は、[CREATE COLUMNSTORE INDEX](../../t-sql/statements/create-columnstore-index-transact-sql.md) を使用して、テーブルをクラスター化列ストア インデックスに変換できます。 次のように、テーブルが変換された後にクエリのパフォーマンスを改善するいくつかの最適化があります。

### <a name="use-maxdop-to-improve-rowgroup-quality"></a>MAXDOP を使用して行グループの品質を向上させる
ヒープまたはクラスター化 B ツリー インデックスを列ストア インデックスに変換するプロセッサの最大数を構成できます。 プロセッサを構成するには、最大並列度オプション (MAXDOP) を使用します。 

大量のデータがある場合、MAXDOP 1 では遅すぎる可能性があります。  MAXDOP を 4 に増やすとうまく動作します。 この結果、最適な行数がない少数の行グループが生成される場合は、[ALTER INDEX REORGANIZE](../../t-sql/statements/alter-index-transact-sql.md) を実行してそれらをバックグラウンドでマージできます。

### <a name="keep-the-sorted-order-of-a-b-tree-index"></a>B ツリー インデックスの並べ替え順序を保持する
B ツリー インデックスは並べ替えられた順序で行を既に格納しているため、行を列ストア インデックスに圧縮するときにその順序を維持すると、クエリのパフォーマンスが向上することがあります。

列ストア インデックスは、データを並べ替えませんが、メタデータを使用して、各行グループ内の各列セグメントの最小値と最大値を追跡します。  値の範囲をスキャンする場合、行グループをスキップするときに簡単に計算できます。 データが並べ替えられていると、より多くの行グループをスキップできます。 

変換中に並べ替え順序を維持するには:
* DROP_EXISTING 句を指定した [CREATE COLUMNSTORE INDEX](../../t-sql/statements/create-columnstore-index-transact-sql.md) を使用します。 これにより、インデックスの名前も保持されます。 既に行ストア インデックスの名前を使用しているスクリプトがある場合、それらを更新する必要はありません。 

    次の例では、`MyFactTable` という名前のテーブル上のクラスター化された行ストア インデックスをクラスター化列ストア インデックスに変換します。 インデックス名 `ClusteredIndex_d473567f7ea04d7aafcac5364c241e09` は同じままになります。

    ```sql
    CREATE CLUSTERED COLUMNSTORE INDEX ClusteredIndex_d473567f7ea04d7aafcac5364c241e09  
    ON MyFactTable  
    WITH (DROP_EXISTING = ON);  
    ```

## <a name="related-tasks"></a>Related Tasks  
以下は、列ストア インデックスを作成して保守するためのタスクです。 
  
|タスク|参照トピック|Notes|  
|----------|----------------------|-----------|  
|テーブルを列ストアとして作成する。|[CREATE TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/create-table-transact-sql.md)|[!INCLUDE[sssql16-md](../../includes/sssql16-md.md)]以降、テーブルをクラスター化列ストア インデックスとして作成できます。 最初に行ストア テーブルを作成して列ストアに変換する必要はありません。|  
|列ストア インデックスを持つメモリ テーブルを作成します。|[CREATE TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/create-table-transact-sql.md)|[!INCLUDE[sssql16-md](../../includes/sssql16-md.md)]以降、列ストア インデックスを持つ、メモリ最適化テーブルを作成できます。 列ストア インデックスは、テーブルの作成後に、ALTER TABLE ADD INDEX 構文を使用して追加することもできます。|  
|行ストア テーブルを列ストアに変換する。|[CREATE COLUMNSTORE INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/create-columnstore-index-transact-sql.md)|既存のヒープまたはバイナリ ツリーを列ストアに変換します。 この変換を実行するときの既存のインデックスとインデックス名の処理方法を例示します。|  
|列ストア テーブルを行ストアに変換する。|[CREATE CLUSTERED INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/create-columnstore-index-transact-sql.md#d-convert-a-columnstore-table-to-a-rowstore-table-with-a-clustered-index)、[列ストア テーブルを行ストア ヒープに戻す](../../t-sql/statements/create-columnstore-index-transact-sql.md#e-convert-a-columnstore-table-back-to-a-rowstore-heap) |この変換は通常は必要ありませんが、状況によっては必要になる場合があります。 列ストアをヒープまたはクラスター化インデックスに変換する方法を例示します。|   
|行ストア テーブルで列ストア インデックスを作成する。|[CREATE COLUMNSTORE INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/create-columnstore-index-transact-sql.md)|行ストア テーブルでは列ストア インデックスを 1 つ使用できます。  [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)]以降、列ストア インデックスにフィルター条件を指定できるようになりました。 基本構文を例示します。|  
|運用分析のパフォーマンスの高いインデックスを作成する。|[列ストアを使用したリアルタイム運用分析の概要](../../relational-databases/indexes/get-started-with-columnstore-for-real-time-operational-analytics.md)|補完的な列ストア インデックスと B ツリー インデックスを作成する方法について説明します。OLTP クエリでは B ツリー インデックスが使用され、分析クエリでは列ストア インデックスが使用されます。|  
|データ ウェアハウス用のパフォーマンスの高い列ストア インデックスを作成する。|[列ストア インデックス - データ ウェアハウス](../../relational-databases/indexes/columnstore-indexes-data-warehouse.md)|列ストア テーブルで B ツリー インデックスを使用して、パフォーマンスの高いデータ ウェアハウス クエリを作成する方法について説明します。|  
|B ツリー インデックスを使用して列ストア インデックスに主キー制約を適用する|[列ストア インデックス - データ ウェアハウス](../../relational-databases/indexes/columnstore-indexes-data-warehouse.md)|B ツリー インデックスと列ストア インデックスを組み合わせて、列ストア インデックスに主キー制約を適用する方法を示します。|  
|列ストア インデックスを削除する|[DROP INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/drop-index-transact-sql.md)|列ストア インデックスは、B ツリー インデックスが使用する標準の DROP INDEX 構文を使って削除します。 クラスター化列ストア インデックスを削除すると、列ストア テーブルがヒープに変換されます。|  
|列ストア インデックスから行を削除する|[DELETE &#40;Transact-SQL&#41;](../../t-sql/statements/delete-transact-sql.md)|[DELETE &#40;Transact-SQL&#41;](../../t-sql/statements/delete-transact-sql.md) を使用して行を削除します。<br /><br /> **列ストア** 行: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] は行を論理的に削除されたとしてマークしますが、インデックスが再構築されるまで行の物理ストレージを再確保することはありません。<br /><br /> **デルタストア** 行: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] は論理的および物理的に行を削除します。|  
|列ストア インデックスの行を更新する|[UPDATE &#40;Transact-SQL&#41;](../../t-sql/queries/update-transact-sql.md)|[UPDATE &#40;Transact-SQL&#41;](../../t-sql/queries/update-transact-sql.md) を使用して行を更新します。<br /><br /> **列ストア** 行:  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] は行を論理的に削除されたとしてマークし、更新された行をデルタストアに挿入します。<br /><br /> **デルタストア** 行: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] は、デルタストアの行を更新します。|  
|デルタストアのすべての行を強制的に列ストアに移動します。|[ALTER INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/alter-index-transact-sql.md) ...REBUILD<br /><br /> [インデックスの再編成と再構築](../../relational-databases/indexes/reorganize-and-rebuild-indexes.md)|ALTER INDEX に REBUILD オプションを指定すると、すべての行が列ストアに強制的に移動されます。|  
|列ストア インデックスを最適化する|[ALTER INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/alter-index-transact-sql.md)|ALTER INDEX ...REORGANIZE は、列ストア インデックスをオンラインで最適化します。|  
|テーブルと列ストア インデックスをマージする。|[MERGE &#40;Transact-SQL&#41;](../../t-sql/statements/merge-transact-sql.md)|

## <a name="next-steps"></a>次のステップ
空の列ストア インデックスを作成するには:

* [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] または [!INCLUDE[ssSDS](../../includes/sssds-md.md)]。「[CREATE TABLE (Transact-SQL)](../../t-sql/statements/create-table-transact-sql.md)」を参照してください。
* [!INCLUDE[ssSDW](../../includes/sssdw-md.md)]。「[CREATE TABLE (Azure Synapse Analytics)](../../t-sql/statements/create-table-as-select-azure-sql-data-warehouse.md)」を参照してください。

既存の行ストア ヒープまたは B ツリー インデックスをクラスター化列ストア インデックスに変換する方法、または非クラスター化列ストア インデックスを作成する方法の詳細については、「[CREATE COLUMNSTORE INDEX (Transact-SQL)](../../t-sql/statements/create-columnstore-index-transact-sql.md)」を参照してください。