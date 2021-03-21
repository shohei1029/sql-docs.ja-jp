---
title: 断片化されたインデックスの検出と解決 | Microsoft Docs
description: この記事では、インデックスの断片化が発生するしくみ、断片化の量を検出する方法、T-SQL と SQL Server Management Studio を利用してインデックス断片化を解決するためのベスト オプションを判断する方法について説明します。
ms.custom: ''
ms.date: 03/19/2020
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.technology: table-view-index
ms.topic: conceptual
f1_keywords:
- sql13.swb.index.rebuild.f1
- sql13.swb.indexproperties.fragmentation.f1
- sql13.swb.index.reorg.f1
helpviewer_keywords:
- large object defragmenting
- indexes [SQL Server], reorganizing
- index reorganization [SQL Server]
- reorganizing indexes
- defragmenting large object data types
- index fragmentation [SQL Server]
- index rebuilding [SQL Server]
- rebuilding indexes
- indexes [SQL Server], rebuilding
- defragmenting indexes
- nonclustered indexes [SQL Server], defragmenting
- fragmentation [SQL Server]
- index defragmenting [SQL Server]
- LOB data [SQL Server], defragmenting
- clustered indexes, defragmenting
ms.assetid: a28c684a-c4e9-4b24-a7ae-e248808b31e9
author: pmasl
ms.author: mikeray
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: d322c728d9118cca544584e22e0e7f83f2154776
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104755142"
---
# <a name="resolve-index-fragmentation-by-reorganizing-or-rebuilding-indexes"></a>インデックスを再構成または再構築することでインデックス断片化を解決する

[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

この記事では、インデックス断片化が発生するしくみと、クエリのパフォーマンスにそれが与える影響について説明します。 [インデックスに存在する断片化の量](#detecting-the-amount-of-fragmentation)を判断したら、自分で選択したツールで Transact-SQL コマンドを実行するか、SQL Server Management Studio を使用して[インデックスを再構成する](#reorganize-an-index)か、[再構築する](#rebuild-an-index)ことでインデックスをデフラグできます。

## <a name="index-fragmentation-overview"></a>インデックスの断片化の概要

インデックスの断片化とはどういうことで、なぜ注意する必要があるのでしょうか。

- インデックスに、インデックスのキー値に基づくインデックス内での論理的な順序と、インデックス ページの内部での物理的な順序が一致しないページが存在すると、断片化が発生します。
- [!INCLUDE[ssde_md](../../includes/ssde_md.md)] では、基になるデータに対して挿入、更新、または削除の各操作が行われるたびに、インデックスが自動的に変更されます。 たとえば、テーブルに行が追加されると、行ストア インデックス内の既存のページが分割されて、新しいキー値を挿入するための領域が確保される場合があります。 このような変更が長期にわたると、インデックス内の情報がデータベース内に散在 (断片化) することになります。 インデックスに、キー値に基づく論理順序とデータ ファイル内の物理順序が一致しないページが存在すると、断片化が発生します。
- インデックスの断片化が大きくなると、インデックスが指し示しているデータを特定するために必要な I/O が増加するため、クエリのパフォーマンスが低下する可能性があります。 I/O が多くなると、アプリケーションの応答が遅くなります。スキャン操作が関係している場合は特にそうです。

## <a name="detecting-the-amount-of-fragmentation"></a>断片化の量を検出する

使用するインデックス最適化方法を決める最初のステップは、断片化の程度を判断するためにインデックスを分析することです。 行ストア インデックスと列ストア インデックスの断片化を異なる方法で検出します。

> [!NOTE]
> 大量のデータを削除した後は、インデックスまたはヒープの断片化を確認することが特に重要です。 ヒープについては、更新が頻繁に行われる場合、転送レコードの急増を回避するために断片化を確認することが必要になる場合もあります。 ヒープの詳細については、「[ヒープ (クラスター化インデックスなしのテーブル)](../../relational-databases/indexes/heaps-tables-without-clustered-indexes.md#heap-structures)」を参照してください。 

### <a name="detecting-fragmentation-of-rowstore-indexes"></a>行ストア インデックスの断片化を検出する

[sys.dm_db_index_physical_stats](../../relational-databases/system-dynamic-management-views/sys-dm-db-index-physical-stats-transact-sql.md) を使用して、特定のインデックス、テーブルやインデックス付きビュー上のすべてのインデックス、データベース内のすべてのインデックス、またはすべてのデータベース内のすべてのインデックスの断片化を検出できます。 パーティション インデックスの場合は、 **sys.dm_db_index_physical_stats** でもパーティションごとの断片化情報が提供されます。

**sys.dm_db_index_physical_stats** から返される結果セットに含まれる列を次に示します。

|列|説明|
|------------|-----------------|
|**avg_fragmentation_in_percent**|論理的な断片化 (インデックス内で順序が乱れたページ) の割合。|
|**fragment_count**|インデックス内の断片化 (物理的に連続したリーフ ページ) の数。|
|**avg_fragment_size_in_pages**|インデックス内の 1 つの断片化内の平均ページ数。|

断片化の程度がわかったら、次の表を使用して、断片化をなくすための最適な方法を決定します。[INDEX REORGANIZE](#reorganize-an-index) か [INDEX](#rebuild-an-index) です。

|**avg_fragmentation_in_percent** 値|断片化解消ステートメント|
|-----------------------------------------------|--------------------------|
|5 から 30%<sup>1</sup>|ALTER INDEX REORGANIZE|
|> 30% <sup>1</sup>|ALTER INDEX REBUILD WITH (ONLINE = ON) <sup>2</sup>|

<sup>1</sup> これらの値は、`ALTER INDEX REORGANIZE` と `ALTER INDEX REBUILD` を切り替える必要があるタイミングを決定するための大まかなガイドラインを提供します。 ただし、実際の値は状況によって変わります。 それぞれの環境で実際に試して最適なしきい値を特定することが重要です。      

> [!TIP] 
> たとえば、特定のインデックスが主にスキャン操作に使用されている場合、断片化を解消すると、これらの操作のパフォーマンスが向上します。 主にシーク操作に使用されるインデックスについては、パフォーマンスのメリットがそれほど顕著ではない場合があります。    
同様に、ヒープ (クラスター化インデックスのないテーブル) 内の断片化の解消は、非クラスター化インデックスのスキャン操作では特に便利ですが、参照操作にはほとんど影響しません。

<sup>2</sup> インデックスの再構築はオンラインでもオフラインでも実行できます。 インデックスの再構成は、常にオンラインで実行されます。 再構成オプションと同様の可用性を実現するには、インデックスをオンラインで再構築してください。 詳しくは、[インデックス](#rebuild-an-index)に関するページと「[オンラインでのインデックス操作の実行](../../relational-databases/indexes/perform-index-operations-online.md)」をご覧ください。

インデックスの断片化が 5% 未満の場合は、デフラグする必要はありません。これは、インデックスの再構成または再構築で、このような少量の断片化を解消するのに見合わない CPU コストが通常かかるからです。 また、少量の行ストア インデックスを再構築または再構成しても、一般的に実際断片化は解消されません。 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)]までは、[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]では、混合エクステントを使用して領域が割り当てられます。 このため、小さいインデックスのページは、混合エクステントに格納される場合があります。 混合エクステントは最大 8 つのオブジェクトで共有されるため、小さなインデックスを再構成または再構築しても、その断片化は解消されない場合があります。 「[行ストア インデックスの再構築に固有の注意点](#considerations-specific-to-rebuilding-rowstore-indexes)」を参照してください。 エクステントの詳細については、「[ページとエクステントのアーキテクチャ ガイド](../../relational-databases/pages-and-extents-architecture-guide.md#extents)」を参照してください。

### <a name="detecting-fragmentation-of-columnstore-indexes"></a>列ストア インデックスの断片化を検出する

[sys.dm_db_column_store_row_group_physical_stats](../../relational-databases/system-dynamic-management-views/sys-dm-db-column-store-row-group-physical-stats-transact-sql.md) を使用することで、インデックスで削除された行の割合を確認できます。これは、列ストア インデックスの行グループの断片化に対する妥当なメジャーです。 この情報を使用して、特定のインデックス、テーブルのすべてのインデックス、データベース内のすべてのインデックス、またはすべてのデータベース内のすべてのインデックスの断片化を計算します。

**sys.dm_db_column_store_row_group_physical_stats** から返される結果セットに含まれる列を次に示します。

|列|説明|
|------------|-----------------|
|**total_rows**|行グループに物理的に格納されている行の数。 圧縮された行グループの場合は、削除済みとマークされた行が含まれます。|
|**deleted_rows**|削除対象としてマークされている圧縮行グループに物理的に格納されている行の数。 デルタ ストア内の行グループの場合は 0 です。|

次の式を使用して、インデックスの断片化を計算するために返されたこの情報を使用します。

```
100*(ISNULL(deleted_rows,0))/NULLIF(total_rows,0)
```

インデックスの断片化の程度がわかったら、次の表を使用して、断片化をなくすための最適な方法を決定します。[INDEX REORGANIZE](#reorganize-an-index) か [INDEX](#rebuild-an-index) です。

|**計算された断片化のパーセント値**|適用されるバージョン|断片化解消ステートメント|
|-----------------------------------------------|--------------------------|--------------------------|
|> = 20%|[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] および [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)]|ALTER INDEX REBUILD|
|> = 20%|[!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 以降|ALTER INDEX REORGANIZE|

### <a name="to-check-the-fragmentation-of-a-rowstore-index-using-tsql"></a>[!INCLUDE[tsql](../../includes/tsql-md.md)] を利用して行ストア インデックスの断片化を確認するには

次の例では、`AdventureWorks2016` データベースの `HumanResources.Employee` テーブルで、すべてのインデックスの断片化の平均パーセンテージを調べます。

```sql
SELECT a.object_id, object_name(a.object_id) AS TableName,
    a.index_id, name AS IndedxName, avg_fragmentation_in_percent
FROM sys.dm_db_index_physical_stats
    (DB_ID (N'AdventureWorks2016_EXT')
        , OBJECT_ID(N'HumanResources.Employee')
        , NULL
        , NULL
        , NULL) AS a
INNER JOIN sys.indexes AS b
    ON a.object_id = b.object_id
    AND a.index_id = b.index_id;
GO
```

前のステートメントによって、次のような結果セットが返されます。

```
object_id   TableName    index_id    IndexName                                             avg_fragmentation_in_percent
----------- ------------ ----------- ----------------------------------------------------- ------------------------------
1557580587  Employee     1           PK_Employee_BusinessEntityID                          0
1557580587  Employee     2           IX_Employee_OrganizationalNode                        0
1557580587  Employee     3           IX_Employee_OrganizationalLevel_OrganizationalNode    0
1557580587  Employee     5           AK_Employee_LoginID                                   66.6666666666667
1557580587  Employee     6           AK_Employee_NationalIDNumber                          50
1557580587  Employee     7           AK_Employee_rowguid                                   0

(6 row(s) affected)
```

詳細については、[sys.dm_db_index_physical_stats](../../relational-databases/system-dynamic-management-views/sys-dm-db-index-physical-stats-transact-sql.md) に関する記事をご覧ください。

### <a name="to-check-the-fragmentation-of-a-columnstore-index-using-transact-sql"></a>Transact-SQL を使用して列ストア インデックスの断片化を確認するには

次の例では、`AdventureWorksDW2016` データベースの `dbo.FactResellerSalesXL_CCI` テーブルで、すべてのインデックスの断片化の平均パーセンテージを調べます。

```sql
SELECT i.object_id,
    object_name(i.object_id) AS TableName,
    i.index_id,
    i.name AS IndexName,
    100*(ISNULL(SUM(CSRowGroups.deleted_rows),0))/NULLIF(SUM(CSRowGroups.total_rows),0) AS 'Fragmentation'
FROM sys.indexes AS i  
INNER JOIN sys.dm_db_column_store_row_group_physical_stats AS CSRowGroups
    ON i.object_id = CSRowGroups.object_id
    AND i.index_id = CSRowGroups.index_id
WHERE object_name(i.object_id) = 'FactResellerSalesXL_CCI'
GROUP BY i.object_id, i.index_id, i.name
ORDER BY object_name(i.object_id), i.name;
```

前のステートメントによって、次のような結果セットが返されます。

```
object_id   TableName                   index_id    IndexName                       Fragmentation
----------- --------------------------- ----------- ------------------------------- ---------------
114099447   FactResellerSalesXL_CCI     1           IndFactResellerSalesXL_CCI      0

(1 row(s) affected)
```

### <a name="check-index-fragmentation-using-sql-server-management-studio"></a>SQL Server Management Studio を使用してインデックス断片化を確認する

> [!NOTE]
> SQL Server の列ストア インデックスの断片化を計算するために、また、Azure SQL Database 内のインデックスの断片化を計算するために、[!INCLUDE[ssManStudio](../../includes/ssManStudio-md.md)] を使用することはできません。 前の [!INCLUDE[tsql](../../includes/tsql-md.md)] の[例](#to-check-the-fragmentation-of-a-columnstore-index-using-transact-sql)を使用します。

1. オブジェクト エクスプローラーで、インデックスの断片化を確認するテーブルが格納されているデータベースを展開します。
2. **[テーブル]** フォルダーを展開します。
3. インデックスの断片化を確認するテーブルを展開します。
4. **[インデックス]** フォルダーを展開します。
5. 断片化を確認するインデックスを右クリックし、 **[プロパティ]** を選択します。
6. **[ページの選択]** の **[断片化]** を選択します。

**[断片化]** ページでは、次の情報を取得できます。

|値|説明|
|---|---|
|**[ページのゆとり]**|インデックス ページのゆとりの平均をパーセントで示します。 100% は、インデックス ページが完全にいっぱいであることを示します。 50% は、平均して各インデックス ページが半分埋まっていることを示します。|
|**[全体の断片化]**|論理的な断片化の割合です。 この値は、順番に格納されないインデックス内のページ数を示します。|
|**[行の平均サイズ]**|リーフ レベルの行の平均サイズです。|
|**[奥行]**|インデックス内のレベルの数 (リーフ レベルを含む) です。|
|**[転送されたレコード]**|別のデータの場所への転送ポインターを持つ、ヒープ内の転送されたレコード数です (この状態は、更新中に、新しい行を格納できる十分なスペースが元の場所にない場合に発生します)。|
|**[非実体行]**|削除のマークが付けられている行の中で、まだ削除されていない行の数です。 これらの行は、サーバーが使用中でないときにクリーンアップ スレッドによって削除されます。 この値には、スナップショット分離トランザクションが未完了であるために保持される行は含まれません。|
|**[インデックスの種類]**|インデックスの種類です。 指定できる値は、 **[クラスター化]** 、 **[非クラスター化]** 、および **[プライマリ XML]** です。 テーブルは、ヒープ (インデックスなし) としても格納できますが、その場合は、この [インデックスのプロパティ] ページは開きません。|
|**[リーフレベルの行]**|リール レベルの行の数です。|
|**[行の最大サイズ]**|リーフ レベルの最大行サイズです。|
|**[行の最小サイズ]**|リーフ レベルの最小行サイズです。|
|**ページ**|データ ページ数の合計です。|
|**パーティション ID**|インデックスを含む B ツリーのパーティション ID です。|
|**[バージョンの非実体行]**|未処理のスナップショット分離トランザクションが原因で保持されているゴースト レコードの数。|

## <a name="defragmenting-indexes-by-rebuilding-or-reorganizing-the-index"></a>インデックスを再構築または再構成してインデックスをデフラグする

次のいずれかの方法で断片化したインデックスをデフラグします。

- インデックスの再編成
- インデックスの再構築

> [!NOTE]
> パーティション構成に基づいて構築されたパーティション インデックスでは、完全なインデックスまたはインデックスの 1 つのパーティションに対して、次のいずれかの方法を使用できます。

### <a name="reorganize-an-index"></a>インデックスを再構成する

インデックスの再構成では、最小のシステム リソースがオンライン操作で使用されます。 つまり、`ALTER INDEX REORGANIZE` トランザクション中は、長期にわたって他をブロックするテーブル ロックは保持されず、基になるテーブルへのクエリまたは更新を続行できます。

- [行ストア](clustered-and-nonclustered-indexes-described.md)の場合は、[!INCLUDE[ssde_md](../../includes/ssde_md.md)] により、リーフ レベル ページをリーフ ノードの論理順序 (左から右) に合わせて物理的に並べ替えることにより、テーブルやビュー上にあるクラスター化および非クラスター化インデックスのリーフ レベルが最適化されます。 また、再構成を行うと、インデックスの FILL FACTOR の値に基づいて、インデックス ページが圧縮されます。 FILL FACTOR 設定を表示するには、[sys.indexes](../../relational-databases/system-catalog-views/sys-indexes-transact-sql.md) を使用します。 構文の例については、[行ストアの再構成の例](../../t-sql/statements/alter-index-transact-sql.md#examples-rowstore-indexes)に関する記事をご覧ください。

- [列ストア インデックス](columnstore-indexes-overview.md)を使用している場合は、時間が経つと、データの挿入、更新、削除の後で、差分ストアが複数の小さな行グループになることがあります。 列ストア インデックスを再構成すると、すべての行グループが列ストアに強制的に移動された後、行グループが、より行数の多い少数の行グループに結合されます。 再構成操作では、列ストアから削除された行も削除されます。 再構成では、最初にデータを圧縮するために必要な CPU リソースが増加し、システムの全体的なパフォーマンスが遅くなる場合があります。 しかし、データが圧縮されるとすぐ、クエリのパフォーマンスは改善します。 構文の例については、[列ストアの再構成の例](../../t-sql/statements/alter-index-transact-sql.md#examples-columnstore-indexes)に関する記事をご覧ください。

### <a name="rebuild-an-index"></a>インデックスを再構築する

インデックスの再構築では、インデックスを削除し再作成します。 インデックスの種類と [!INCLUDE[ssde_md](../../includes/ssde_md.md)] のバージョンによっては、再構築操作をオンラインまたはオフラインで実行できます。 T-SQL の構文については、[ALTER INDEX REBUILD](../../t-sql/statements/alter-index-transact-sql.md#rebuilding-indexes) に関する記事を参照してください

- [行ストア インデックス](clustered-and-nonclustered-indexes-described.md)の場合、再構築では、断片化が解消され、指定または既存の FILL FACTOR の設定に基づいてページが圧縮されることによりディスク領域が解放された後、インデックス行が連続するページに再び並べ替えられます。 `ALL` を指定した場合、テーブル上のすべてのインデックスが、1 回のトランザクションで削除され再構築されます。 外部キー制約は、前もって削除しておく必要はありません。 128 以上のエクステントがあるインデックスを再構築すると、[!INCLUDE[ssDE](../../includes/ssde-md.md)]では、トランザクションがコミットされるまで実際のページの割り当て解除とそれに関連するロックが延期されます。 構文の例については、[行ストアの再構成の例](../../t-sql/statements/alter-index-transact-sql.md#examples-rowstore-indexes)に関する記事をご覧ください。

- [列ストア インデックス](columnstore-indexes-overview.md)では、再構築によって断片化が解消され、すべての行が列ストアに移動されて、テーブルから論理的に削除された行を物理的に削除することによってディスク領域が解放されます。 
  
  > [!TIP]
  > [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 以降では、`REORGANIZE` によってオンライン操作としてバックグラウンドで基本的な再構築が実行されるため、通常、列ストア インデックスの再構築は必要ありません。 
  
  構文の例については、[列ストアのリビルド](../../t-sql/statements/alter-index-transact-sql.md#examples-columnstore-indexes)に関するページを参照してください。

### <a name="permissions"></a><a name="Permissions"></a> Permissions

テーブルまたはビューに対する `ALTER` 権限が必要です。 ユーザーは次のロールの少なくとも 1 つのメンバーである必要があります。

- **db_ddladmin** データベース ロール <sup>1</sup>
- **db_owner** データベース ロール
- **sysadmin** サーバー ロール

<sup>1</sup>**db_ddladmin** データベース ロールには [最も低い特権レベル](/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models)が設定されています。

### <a name="remove-fragmentation-using-ssmanstudiofull"></a><a name="SSMSProcedureReorg"></a>[!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] を利用して断片化を取り除く

#### <a name="to-reorganize-or-rebuild-an-index"></a>インデックスを再構成または再構築するには

1. オブジェクト エクスプローラーで、インデックスを再構成するテーブルが格納されているデータベースを展開します。
2. **[テーブル]** フォルダーを展開します。
3. インデックスを再構成するテーブルを展開します。
4. **[インデックス]** フォルダーを展開します。
5. 再構成するインデックスを右クリックし、 **[再構成]** を選択します。
6. **[インデックスの再構成]** ダイアログ ボックスで、 **[再構成するインデックス]** グリッドに目的のインデックスが表示されていることを確認し、 **[OK]** をクリックします。
7. **[ラージ オブジェクトの列データを圧縮する]** チェック ボックスをオンにして、ラージ オブジェクト (LOB) データを含むページもすべて圧縮することを指定します。
8. **[OK]** をクリックします。

#### <a name="to-reorganize-all-indexes-in-a-table"></a>テーブルのすべてのインデックスを再構成するには

1. オブジェクト エクスプローラーで、インデックスを再構成するテーブルが格納されているデータベースを展開します。
2. **[テーブル]** フォルダーを展開します。
3. インデックスを再構成するテーブルを展開します。
4. **[インデックス]** フォルダーを右クリックし、 **[すべて再構成]** を選択します。
5. **[インデックスの再構成]** ダイアログ ボックスで、 **[再構成するインデックス]** に目的のインデックスが表示されていることを確認します。 **[再構成するインデックス]** グリッドからインデックスを削除するには、インデックスを選択し、Del キーを押します。
6. **[ラージ オブジェクトの列データを圧縮する]** チェック ボックスをオンにして、ラージ オブジェクト (LOB) データを含むページもすべて圧縮することを指定します。
7. **[OK]** をクリックします。

#### <a name="to-rebuild-an-index"></a>インデックスを再構築するには

1. オブジェクト エクスプローラーで、インデックスを再構成するテーブルが格納されているデータベースを展開します。
2. **[テーブル]** フォルダーを展開します。
3. インデックスを再構成するテーブルを展開します。
4. **[インデックス]** フォルダーを展開します。
5. 再構成するインデックスを右クリックし、 **[再構築]** を選択します。
6. **[インデックスの再構築]** ダイアログ ボックスで、 **[再構築するインデックス]** グリッドに目的のインデックスが表示されていることを確認し、 **[OK]** をクリックします。
7. **[ラージ オブジェクトの列データを圧縮する]** チェック ボックスをオンにして、ラージ オブジェクト (LOB) データを含むページもすべて圧縮することを指定します。
8. **[OK]** をクリックします。

### <a name="remove-fragmentation-using-tsql"></a><a name="TsqlProcedureReorg"></a>[!INCLUDE[tsql](../../includes/tsql-md.md)] を利用して断片化を取り除く

> [!NOTE]
> [!INCLUDE[tsql](../../includes/tsql-md.md)] を使ってインデックスを再構築または再構成する他の例については、[ALTER INDEX の例: 列ストア インデックス](../../t-sql/statements/alter-index-transact-sql.md#examples-columnstore-indexes)に関するページおよび [ALTER INDEX の例: 行ストア インデックス](../../t-sql/statements/alter-index-transact-sql.md#examples-rowstore-indexes)に関するページをご覧ください。

#### <a name="to-reorganize-a-fragmented-index"></a>断片化されたインデックスを再構成するには

次の例では、`AdventureWorks2016` データベースの `HumanResources.Employee` テーブルの `IX_Employee_OrganizationalLevel_OrganizationalNode` インデックスが再構成されます。

```sql
ALTER INDEX IX_Employee_OrganizationalLevel_OrganizationalNode
    ON HumanResources.Employee
    REORGANIZE;
```

次の例では、`AdventureWorksDW2016` データベースの `dbo.FactResellerSalesXL_CCI` テーブルの `IndFactResellerSalesXL_CCI` 列ストア インデックスが再構成されます。

```sql
-- This command will force all CLOSED and OPEN rowgroups into the columnstore.
ALTER INDEX IndFactResellerSalesXL_CCI
    ON FactResellerSalesXL_CCI
    REORGANIZE WITH (COMPRESS_ALL_ROW_GROUPS = ON);
```

#### <a name="to-reorganize-all-indexes-in-a-table"></a>テーブルのすべてのインデックスを再構成するには

次の例では、`AdventureWorks2016` データベースの `HumanResources.Employee` テーブルのすべてのインデックスが再構成されます。

```sql
ALTER INDEX ALL ON HumanResources.Employee
   REORGANIZE;
```

#### <a name="to-rebuild-a-fragmented-index"></a>断片化されたインデックスを再構築するには

次の例では、`AdventureWorks2016` データベースにある `Employee` テーブルで単一のインデックスを再構築します。

[!code-sql[IndexDDL#AlterIndex1](../../relational-databases/indexes/codesnippet/tsql/reorganize-and-rebuild-i_1.sql)]

#### <a name="to-rebuild-all-indexes-in-a-table"></a>テーブルのすべてのインデックスを再構築するには

次の例では、`ALL` キーワードを使って、`AdventureWorks2016` データベース内のテーブルに関連付けられたすべてのインデックスが再構築されます。 3 つのオプションが指定されます。

[!code-sql[IndexDDL#AlterIndex2](../../relational-databases/indexes/codesnippet/tsql/reorganize-and-rebuild-i_2.sql)]

詳細については、「[ALTER INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/alter-index-transact-sql.md)」を参照してください。

#### <a name="automatic-index-and-statistics-management"></a>インデックスと統計の自動管理

[Adaptive Index Defrag](https://github.com/Microsoft/tigertoolbox/tree/master/AdaptiveIndexDefrag) のようなソリューションを活用し、1 つまたは複数のデータベースに対するインデックスの最適化と統計更新を自動管理します。 このプロシージャでは、断片化レベルやその他のパラメーターに基づいてインデックスを再構築または再構成するか、線形しきい値で統計を更新するかが自動的に選択されます。

## <a name="considerations-specific-to-rebuilding-rowstore-indexes"></a>行ストア インデックスの再構築に固有の注意点

非クラスター化インデックス レコードに含まれる物理識別子または論理識別子を変更する必要がある場合に、クラスター化インデックスを再構築すると、クラスター化キーを参照する非クラスター化インデックスが自動的に再構築されます。

以下のシナリオでは、テーブル上のすべての行ストア非クラスター化インデックスを、強制的に自動再構築します。

- テーブルに対するクラスター化インデックスの作成
- テーブルがヒープとして格納される原因となる、クラスター化インデックスの削除
- クラスター化キーの変更による列の挿入または除外

以下のシナリオでは、すべての行ストア非クラスター化インデックスをテーブル上で自動的に再構築する必要はありません。

- 一意のクラスター化インデックスの再構築
- 一意でないクラスター化インデックスの再構築
- クラスター化インデックスへのパーティション構成の適用、クラスター化インデックスの別のファイルグループへの移動など、インデックス スキーマの変更

> [!IMPORTANT]
> インデックスのあるファイル グループがオフラインであるか、または読み取り専用に設定されている場合、インデックスを再構成または再構築することはできません。 キーワード ALL を指定した場合で、1 つ以上のインデックスがオフラインまたは読み取り専用のファイル グループにある場合、ステートメントは失敗します。
>
> インデックスの再構築が行われている間、物理メディアにはインデックスのコピーを 2 つ格納するのに十分な領域が必要です。 再構築が完了すると、[!INCLUDE[ssde_md](../../includes/ssde_md.md)] によって元のインデックスが削除されます。

`ALTER INDEX` ステートメントで `ALL` を指定すると、テーブル上のクラスター化と非クラスター化の両方のリレーショナル インデックスと XML インデックスが再構成されます。

## <a name="considerations-specific-to-rebuilding-a-columnstore-index"></a>列ストア インデックスの再構築に固有の注意点

列ストアインデックスを再構築するときは、[!INCLUDE[ssde_md](../../includes/ssde_md.md)] によって元の列ストア インデックスから、デルタ ストアを含むすべてのデータが読み取られます。 データを新しい行グループに結合し、行グループを列ストアに圧縮します。 [!INCLUDE[ssde_md](../../includes/ssde_md.md)] では、テーブルから論理的に削除された行を物理的に削除することで、列ストアがデフラグされます。 削除されたバイトはディスクで再利用されます。

> [!NOTE]
> [!INCLUDE[ssManStudio](../../includes/ssManStudio-md.md)] を使用して列ストア インデックスを再構成すると、COMPRESSED 行グループが結合されますが、すべての行グループが列ストアに強制的に圧縮されることはありません。 CLOSED の行グループは圧縮されますが、OPEN の行グループは列ストアに圧縮されません。 すべての行グループを強制的に圧縮するには、[以下](#TsqlProcedureReorg)の [!INCLUDE[tsql](../../includes/tsql-md.md)] の例を使用してください。

> [!NOTE]
> [!INCLUDE[sql-server-2019](../../includes/sssql19-md.md)] 以降、組ムーバーは、内部しきい値によってしばらくの間存在していると判断された小さな OPEN デルタ行グループを自動的に圧縮するか、多数の行が削除されている COMPRESSED 行グループをマージするバックグラウンド マージ タスクによってサポートされています。 これにより、時間の経過とともに、列ストア インデックスの品質が向上します。    
> 列ストアの用語と概念の詳細については、「[列ストア インデックス: 概要)](../../relational-databases/indexes/columnstore-indexes-overview.md) の下のステートメントを右クリックします。

### <a name="rebuild-a-partition-instead-of-the-entire-table"></a>テーブル全体ではなくパーティションを再構築する

- インデックスが大きいとテーブル全体の再構築には時間がかかり、再構築中にインデックスの追加コピーを格納するために十分なディスク領域が必要です。 通常は、最近使用されたパーティションを再構築するだけで済みます。
- パーティション テーブルでは、columnstore インデックス全体を再構築する必要はありません。断片化は最近変更されたパーティションのみで起きる可能性が高いためです。 ファクト テーブルや大きなディメンション テーブルは通常、テーブルのチャンク上でバックアップおよび管理を行うためにパーティション分割されます。

### <a name="rebuild-a-partition-after-heavy-dml-operations"></a>負荷の高い DML 操作後にパーティションを再構築する

パーティションを再構築すると、パーティションの断片化が解消され、ディスク ストレージが減少します。 再構築すると、削除のマークが付けられた列ストアからすべての行が削除され、すべての行グループがデルタ ストアから列ストアに移動されます。 デルタ ストアには、100 万行未満の複数の行グループが存在する場合があります。

### <a name="rebuild-a-partition-after-loading-data"></a>データを読み込んだ後に、パーティションを再構築する

データを読み込んだ後でパーティションを再構築すると、すべてのデータが列ストアに格納されます。 同時実行のプロセスでそれぞれ 100,000 未満の行が同じパーティションに同時に読み込まれた場合、パーティションに複数のデルタストアが含まれることがあります。 再構築すると、すべてのデルタ ストア行が列ストアに移動されます。

## <a name="considerations-specific-to-reorganizing-a-columnstore-index"></a>列ストア インデックスの再構成に固有の注意点

列ストア インデックスを再構成すると、[!INCLUDE[ssde_md](../../includes/ssde_md.md)] によって、CLOSED の各デルタ行グループが、圧縮された行グループとして列ストアに圧縮されます。 [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 以降および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] の `REORGANIZE` コマンドでは、次の追加のデフラグ最適化がオンラインで実行されます。

- 行の 10% 以上が論理的に削除されたとき、行グループから行を物理的に削除します。 削除されたバイトは、物理メディア上で解放されます。 たとえば、100 万行から成る圧縮された行グループで 10 万行が論理的に削除された場合、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] は、削除された行を物理的に削除し、90 万行を含む行グループを圧縮し直します。 削除された行を解放することで、記憶域が節約されます。

- 1 つまたは複数の圧縮された行グループを結合して、行グループあたりの行数を最大で 1,048,576 行まで増やすことができます。 たとえば、102,400 行のバッチを 5 つ一括でインポートした場合は、5 つの圧縮された行グループが得られます。 REORGANIZE を実行すると、これらの行グループは圧縮された 1 つの行グループにマージされ、そのサイズは 512,000 行となります。 この処理は、ディクショナリ サイズまたはメモリに関する制限が存在していないことを前提としています。

- 行グループの 10% 以上の行が論理的に削除された場合、[!INCLUDE[ssde_md](../../includes/ssde_md.md)] ではこのような行グループを 1 つまたは複数の行グループと結合することが試みられます。 たとえば、行グループ 1 は 500,000 行の圧縮された行グループであり、行グループ 21 は最大 1,048, 576 行の圧縮された行グループであるとします。 行グループ 21 は行の 60% が削除され、残りが 409,830 行になっています。 [!INCLUDE[ssde_md](../../includes/ssde_md.md)] では、優先的にこの 2 つの行グループが結合されて、909,830 行の新しい行グループが圧縮されます。

データの読み込みが実行された後、デルタ ストアには複数の小さな行グループが含まれることがあります。 `ALTER INDEX REORGANIZE` を使用して、すべての行グループを列ストアに強制的に移動し、これらの行グループを、より行数の多い少数の行グループへと結合することができます。 再構成操作では、列ストアから削除された行も削除されます。

## <a name="limitations-and-restrictions"></a><a name="Restrictions"></a> 制限事項と制約事項

128 エクステントを超える行ストア インデックスは、論理フェーズと物理フェーズの 2 つの独立したフェーズで再構築されます。 論理フェーズでは、インデックスによって使用されている既存のアロケーション ユニットが、割り当て解除に設定されます。その後、データ行がコピーされ、並べ替えられてから、再構築されたインデックスを格納するために作成された新しいアロケーション ユニットに移動されます。 物理フェーズでは、バックグラウンドで行われる短いトランザクションで、以前に割り当て解除に設定されたアロケーション ユニットが物理的に削除され、ロックの必要はあまり多くありません。 エクステントの詳細については、「[ページとエクステントのアーキテクチャ ガイド](../../relational-databases/pages-and-extents-architecture-guide.md)」を参照してください。

この操作では、ファイル グループ内の他のファイルではなく、同じファイル上に一時的な作業ページを割り当てなければならないため、`ALTER INDEX REORGANIZE` ステートメントには、使用可能な領域があるインデックスを含むデータ ファイルが必要です。 そのため、ファイル グループに空き容量があるとしても、エラー 1105 が発生することがあります。`Could not allocate space for object '###' in database '###' because the '###' filegroup is full. Create disk space by deleting unneeded files, dropping objects in the filegroup, adding additional files to the filegroup, or setting autogrowth on for existing files in the filegroup.`

> [!WARNING]
> 固定されていないインデックスをパーティションが 1, 000 個以上あるテーブルに作成または再構築することは可能ですが、サポートされていません。 このような操作を行うと、操作中にパフォーマンスが低下したりメモリが過度に消費される可能性があります。 パーティションの数が 1,000 個を超えた場合は、[固定されたインデックス](../partitions/partitioned-tables-and-indexes.md#aligned-index)のみを使用することをお勧めします。

インデックスのあるファイル グループが **オフライン** であるか、または **読み取り専用** に設定されている場合、インデックスを再構成または再構築することはできません。 キーワード `ALL` を指定した場合で、1 つ以上のインデックスがオフラインまたは読み取り専用のファイル グループにある場合、ステートメントは失敗します。

[統計] :

- インデックスが **作成** または **再構築** されるとき、テーブル内のすべての行がスキャンされて、統計が作成または更新されます。 ただし、[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降では、パーティション インデックスが作成または再構築されたとき、テーブル内のすべての行をスキャンして統計が作成または更新されることはありません。 代わりに、クエリ オプティマイザーによって既定のサンプリング アルゴリズムを使用してこれらの統計が生成されます。 テーブル内のすべての行をスキャンしてパーティション インデックスの統計を作成するには、`FULLSCAN` 句で [CREATE STATISTICS](../../t-sql/statements/create-statistics-transact-sql.md) または [UPDATE STATISTICS](../../t-sql/statements/update-statistics-transact-sql.md) を使用します。

- インデックスが **再構成** されるとき、統計は更新されません。

`ALLOW_PAGE_LOCKS` を OFF に設定した場合、インデックスを再構成することはできません。

[!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] までは、クラスター化列ストア インデックスの再構築はオフライン操作です。 データベース エンジンでは、再構築が行われている間、テーブルまたはパーティションを排他的にロックする必要があります。 `NOLOCK`、READ COMMITTED スナップショット分離 (RCSI)、またはスナップショット分離を使用しているときでも、再構築の間は、データはオフラインになり使用できません。
[!INCLUDE[sql-server-2019](../../includes/sssql19-md.md)] 以降では、`ONLINE = ON` オプションを使用してクラスター化列ストア インデックスを再構築できます。

順序付けされたクラスター化列ストア インデックスを使用する Azure Synapse Analytics (旧称 [!INCLUDE[ssSDW](../../includes/sssdw-md.md)]) テーブルの場合、`ALTER INDEX REBUILD` では TempDB を使用してデータが再度並べ替えられます。 再構築操作中に TempDB を監視します。 TempDB 領域がさらに必要な場合は、データ ウェアハウスをスケールアップします。 インデックスの再構築が完了したら、スケール ダウンで戻します。

順序付けされたクラスター化列ストア インデックスを使用する Azure Synapse Analytics (旧称 [!INCLUDE[ssSDW](../../includes/sssdw-md.md)]) テーブルの場合、`ALTER INDEX REORGANIZE` によってデータが再度並べ替えられることはありません。 データを再度並べ替えるには `ALTER INDEX REBUILD` を使用します。

## <a name="using-index-rebuild-to-recover-from-hardware-failures"></a>INDEX REBUILD を使用してハードウェア障害から復旧する

以前のバージョンの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、行ストアの非クラスター化インデックスを再構築することで、ハードウェア障害により発生した不一致を修正できる場合がありました。
[!INCLUDE[ssKatmai](../../includes/ssKatmai-md.md)] 以降でも、非クラスター化インデックスをオフラインで再構築することで、インデックスとクラスター化インデックス間の不一致を修正できます。 オンラインでインデックスを再構築する場合、既存の非クラスター化インデックスを基に再構築が行われるので、不一致を維持してしまい非クラスター化インデックスの不一致を修復できません。 オフラインでインデックスを再構築すると、強制的にクラスター化インデックス (ヒープ) のスキャンがなされ、不一致が解消されることがあります。 クラスター化インデックスからの再構築を保証するため、非クラスター化インデックスを削除および再作成します。 不一致を解消する場合、以前のバージョンと同様に影響を受けたデータをバックアップから復元することをお勧めします。ただし、非クラスター化インデックスをオフラインで再構築しても、インデックスの不一致を修復できます。 詳細については、「[DBCC CHECKDB (Transact-SQL)](../../t-sql/database-console-commands/dbcc-checkdb-transact-sql.md)」を参照してください。

## <a name="see-also"></a>関連項目

- [SQL Server のインデックスのアーキテクチャとデザイン ガイド](../../relational-databases/sql-server-index-design-guide.md)
- [オンラインでのインデックス操作の実行](../../relational-databases/indexes/perform-index-operations-online.md)
- [ALTER INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/alter-index-transact-sql.md)
- [Adaptive Index Defrag](https://github.com/Microsoft/tigertoolbox/tree/master/AdaptiveIndexDefrag)
- [CREATE STATISTICS &#40;Transact-SQL&#41;](../../t-sql/statements/create-statistics-transact-sql.md)
- [UPDATE STATISTICS &#40;Transact-SQL&#41;](../../t-sql/statements/update-statistics-transact-sql.md)
- [列ストア インデックスのクエリ パフォーマンス](../../relational-databases/indexes/columnstore-indexes-query-performance.md)
- [列ストアを使用したリアルタイム運用分析の概要](../../relational-databases/indexes/get-started-with-columnstore-for-real-time-operational-analytics.md)
- [データ ウェアハウスの列ストア インデックス](../../relational-databases/indexes/columnstore-indexes-data-warehouse.md)
- [Columnstore indexes and the merge policy for rowgroups (列ストア インデックスと、行グループのマージ ポリシー)](/archive/blogs/sqlserverstorageengine/columnstore-index-merge-policy-for-reorganize)