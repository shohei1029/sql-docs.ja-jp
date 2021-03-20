---
description: ページとエクステントのアーキテクチャ ガイド
title: ページとエクステントのアーキテクチャ ガイド | Microsoft Docs
ms.custom: ''
ms.date: 03/12/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: ''
ms.topic: conceptual
helpviewer_keywords:
- page and extent architecture guide
- guide, page and extent architecture
ms.assetid: 83a4aa90-1c10-4de6-956b-7c3cd464c2d2
author: rothja
ms.author: jroth
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: e0f95e2e07036ed9e7a560bb7aef22c9fb910a3f
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104750102"
---
# <a name="pages-and-extents-architecture-guide"></a>ページとエクステントのアーキテクチャ ガイド
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

ページは、[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] のデータ ストレージの基本単位です。 エクステントは物理的に連続する 8 ページをまとめたものです。 エクステントを使用すると、ページを効率的に管理できます。 このガイドでは、すべてのバージョンの SQL Server でページとエクステントの管理に使用されるデータ構造について説明します。 ページとエクステントのアーキテクチャを理解することは、効率的に実行されるデータベースを設計、開発するうえで重要です。

## <a name="pages-and-extents"></a>ページとエクステント

[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] のデータ ストレージの基本単位はページです。 データベースのデータ ファイル (.mdf または .ndf) に割り当てられたディスク領域は、0 ～ n の連番が付けられたページに論理的に分割されます。 ディスク I/O 操作は、このページ レベルで実行されます。 つまり、SQL Server では、データ ページ全体に対して読み取りや書き込みが行われます。

エクステントは、物理的に連続する 8 ページをまとめたもので、ページを効率的に管理するために使用されます。 すべてのページは、エクステントで構成されます。

### <a name="pages"></a>ページ

1 冊の本を考えてみてください。その中のすべての内容は複数のページに書き込まれています。 本と同様に、SQL Server では、すべてのデータ行が複数のページに書き込まれています。 本では、すべてのページのサイズは物理的に同じです。 同様に SQL Server では、すべてのデータ ページのサイズが同じ 8 KB になります。 本では、ほとんどのページにデータが含まれています。本の内容と、目次やインデックスなど、内容に関するメタデータが含まれているページがあります。 これも、SQL Server で違いがありません。ほとんどのページには、ユーザーによって保存された実際のデータ行が含まれています。これらは、データ ページとテキスト/イメージ ページ (特殊なケースの場合) と呼ばれます。 インデックス ページには、データの場所に関するインデックス参照が含まれています。最後に、データの編成 (PFS、GAM、SGAM、IAM、DCM、BCM ページ) に関するさまざまなメタデータを格納するシステム ページがあります。 ページの種類とその説明については、以下に示す表を参照してください。

前述したように、[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] では、ページのサイズは 8 KB です。 したがって、[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] データベースには 1 MB あたり 128 ページあります。 各ページの先頭には 96 バイトのヘッダーがあり、ここには各ページに関するシステム情報が格納されています。 この情報には、ページ番号、ページの種類、ページ上の空き容量、そのページを所有しているオブジェクトのアロケーション ユニット ID が含まれます。

次の表に、[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] データベースのデータ ファイルで使用されるページの種類を示します。

|ページの種類 | 内容 |
|-------|-------|
|Data |text、ntext、image、nvarchar(max)、varchar(max)、varbinary(max)、xml データを除くすべてのデータが含まれるデータ行 (text in row が ON に設定されている場合)。 |
|インデックス |インデックスのエントリ。 |
|テキスト/イメージ |ラージ オブジェクト データ型: text、ntext、image、nvarchar(max)、varchar(max)、varbinary(max)、xml データ <br> 可変長列 (データ行のサイズが 8 KB を超える場合): varchar、nvarchar、varbinary、sql_variant |
|グローバル アロケーション マップ、セカンダリ グローバル アロケーション マップ |エクステントが割り当てられているかどうかについての情報。 |
|ページ空き容量 (PFS) |ページ割り当てとページ上で使用可能な空き容量に関する情報。 |
|Index Allocation Map (Index Allocation Map) |テーブルまたはインデックスによって使用されるエクステントに関するアロケーション ユニットごとの情報。 |
|一括変更マップ |最後の BACKUP LOG ステートメント以降の一括操作で変更されたエクステントに関するアロケーション ユニットごとの情報。 |
|差分変更マップ |最後の BACKUP DATABASE ステートメント以降に変更されたエクステントに関するアロケーション ユニットごとの情報。 |

> [!NOTE]
> ログ ファイルにはページではなく、一連のログ レコードが含まれます。

データ行はヘッダーの直後から始まり、ページ上に連続的に配置されます。 ページの末尾から行オフセット テーブルが始まります。各行オフセット テーブルにはページ上の 1 行につき 1 つのエントリが格納されます。 各々の行オフセットエントリには、その行の最初のバイトがページの先頭からどれだけ離れているかが記録されます。 そのため、行オフセット テーブルの役割は、ページ上の行が一瞬で特定されるように SQL Server を支援することです。 行オフセット テーブル内のエントリは、ページ上の行と逆の順序になっています。

![page_architecture](../relational-databases/media/page-architecture.gif)

#### <a name="large-row-support"></a>大きな行のサポート  

行は複数のページにまたがることができません。ただし、行の一部をその行のページから移動することで、事実上大きな行を実現できます。 ページの 1 行に格納できるデータおよびオーバーヘッドの量は、最大 8,060 バイト (8 KB) です。 ただし、これには Text/Image の種類のページに格納されているデータは含まれません。 

varchar 型、nvarchar 型、varbinary 型、または sql_variant 型の列を含むテーブルでは、この制限は緩和されます。 テーブル内のすべての固定長列と可変長列の行サイズの合計が、8,060 バイトの制限を超過した場合、[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] は、サイズの最も大きなものから順に動的に 1 つ以上の可変長列を ROW_OVERFLOW_DATA アロケーション ユニットのページに移動します。 

挿入操作または更新操作により行の合計サイズが 8,060 バイトの制限を超えると、必ずこの処理が実行されます。 列が ROW_OVERFLOW_DATA アロケーション ユニットのページに移動された場合、IN_ROW_DATA アロケーション ユニットに元のページの 24 バイトのポインターが保持されます。 その後の操作により、行サイズが削減されると、[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] は動的に列を元のデータ ページに戻します。 

##### <a name="row-overflow-considerations"></a>行のオーバーフローに関する注意点 

前述のように、ある行を複数のページに配置することはできません。可変長データ型フィールドのサイズ合計が 8060 バイトの上限を超える場合、オーバーフローが発生する可能性があります。 説明すると、たとえば、あるテーブルを varchar(7000) と varchar(2000) という 2 つの列で作成するとします。 いずれの列もそれ自体では 8060 バイトを超えませんが、組み合わせると、各列の幅全体が満たされる場合、この上限を超えます。 SQL Server では、可変長列 varchar(7000) が ROW_OVERFLOW_DATA アロケーション ユニットのページに動的に移動されることがあります。 varchar 型、nvarchar 型、varbinary 型、sql_variant 型、または CLR ユーザー定義型の列の組み合わせが 1 行あたり 8,060 バイトを超える場合は、次のことを考慮してください。
-  更新操作に基づいてレコードが大きくなると、大きなレコードが別のページに動的に移動されます。 レコードが短くなる更新操作が発生すると、レコードが IN_ROW_DATA アロケーション ユニット内の元のページに移動することがあります。 行オーバーフロー データを含む大きなレコードで、クエリを実行したり並べ替えや結合などの他の選択操作を実行すると、処理に時間がかかります。これは、これらのレコードが非同期にではなく同期的に処理されるためです。   
   したがって、複数の varchar 型、nvarchar 型、varbinary 型、sql_variant 型、または CLR ユーザー定義型の列を含むテーブルをデザインするときは、オーバーフローする可能性が高い行の割合と、このオーバーフロー データへのクエリが実行される頻度を考慮します。 行オーバーフロー データの多くの行にクエリが頻繁に実行される可能性が高い場合は、いくつかの列を別のテーブルに移動して、テーブルのサイズを正規化することを検討します。 これにより、非同期結合操作でクエリを行えるようになります。 
-  varchar 型、nvarchar 型、varbinary 型、sql_variant 型、および CLR ユーザー定義型の個々の列の長さは、8,000 バイトの制限の範囲内に収まる必要があります。 8,060 バイトというテーブル行の制限を超えることができるのは、これらの列を組み合わせた長さだけです。
-  char 型や nchar 型のデータなど、他のデータ型の列の合計は、8,060 バイトの行の制限の範囲内に収まる必要があります。 ラージ オブジェクト データにも、8,060 バイトの行の制限は適用されません。 
-  クラスター化インデックスのインデックス キーには、ROW_OVERFLOW_DATA アロケーション ユニットに既存のデータを持つ varchar 列を含めることはできません。 クラスター化インデックスが varchar 列に作成され、既存のデータが IN_ROW_DATA アロケーション ユニットにある場合に、データを行外に押し出すような挿入処理や更新処理をその列に対して行うと失敗します。 アロケーション ユニットの詳細については、「テーブルとインデックスの編成」を参照してください。
-  行オーバーフロー データを含む列を、非クラスター化インデックスのキー列または非キー列として含めることができます。
-  スパース列を使用するテーブルのレコード サイズの制限は 8,018 バイトです。 変換したデータに既存のレコード データを加えると 8,018 バイトを超える場合は、[MSSQLSERVER ERROR 576](../relational-databases/errors-events/database-engine-events-and-errors.md) が返されます。 列がスパース型と非スパース型の間で変換される場合は、データベース エンジンによって現在のレコード データのコピーが保持されます。 このため、レコードのために必要なストレージは一時的に 2 倍になります。
-  行オーバーフロー データを含んでいる可能性のあるテーブルまたはインデックスに関する情報を取得するには、[sys.dm_db_index_physical_stats](../relational-databases/system-dynamic-management-views/sys-dm-db-index-physical-stats-transact-sql.md) 動的管理関数を使用します。

### <a name="extents"></a>Extents 

エクステントは、領域を管理する際の基本単位です。 1 つのエクステントは物理的に連続した 8 ページ、つまり 64 KB です。 したがって、SQL Server データベースには 1 MB あたり 16 のエクステントがあります。

[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] には、2 種類のエクステントがあります。 

* **単一** エクステントは、単一のオブジェクトに所有され、所有しているオブジェクトだけがエクステント内の 8 ページすべてを使用できます。
* **混合** エクステントは最大 8 つのオブジェクトによって共有されます。 エクステント内の各 8 ページを、それぞれ異なるオブジェクトが所有できます。

![単一エクステントと混合エクステント](../relational-databases/media/extents.gif)

[!INCLUDE[ssSQL14](../includes/sssql14-md.md)] までは、[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] では、データ量が少ないテーブルにエクステント全体が割り当てられることはありません。 新規テーブルや新規インデックスには、通常、混合エクステントからページが割り当てられます。 そのテーブルやインデックスが 8 ページまで拡張された時点で、その後の割り当てには単一エクステントが使用されるように切り替えられます。 インデックスに 8 ページ分を生成できるだけの行がある既存のテーブルのインデックスを作成すると、インデックスへのすべての割り当ては単一エクステントになります。 

[!INCLUDE[sssql15-md](../includes/sssql16-md.md)] 以降では、割り当てが [IAM チェーン](#IAM)の最初の 8 ページに属している場合を除き、ユーザー データベースと tempdb のほとんどの割り当てで、単一エクステントが既定値として使用されます。 master、msdb、model の各データベースの割り当てでは、以前の動作がそのまま保持されます。 

> [!NOTE]
> [!INCLUDE[ssSQL14](../includes/sssql14-md.md)] までは、トレース フラグ 1118 を使用して、常に単一エクステントを使用するように既定の割り当てを変更できます。 このトレースフラグの詳細については、「[DBCC TRACEON - トレース フラグ](../t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql.md)」を参照してください。   
>   
> [!INCLUDE[sssql15-md](../includes/sssql16-md.md)] 以降では、TF 1118 によって提供される機能が tempdb およびすべてのデータベースに対して自動的に有効になります。 ユーザー データベースの場合、この動作は `ALTER DATABASE` の `SET MIXED_PAGE_ALLOCATION` オプションによって制御され、既定値は OFF に設定され、トレース フラグ 1118 には効果がありません。 詳細については、「[ALTER DATABASE SET オプション (Transact-SQL)](../t-sql/statements/alter-database-transact-sql-set-options.md)」を参照してください。

[!INCLUDE[ssSQL11](../includes/sssql11-md.md)] 以降では、`sys.dm_db_database_page_allocations` システム関数を使用して、データベース、テーブル、インデックス、パーティションに対するページ割り当て情報を取得できます。

> [!IMPORTANT]
> `sys.dm_db_database_page_allocations` システム関数はドキュメントには記載されておらず、変更される可能性があります。 互換性は保証されません。 

[!INCLUDE[sql-server-2019](../includes/sssql19-md.md)] 以降では、[sys.dm_db_page_info](../relational-databases/system-dynamic-management-views/sys-dm-db-page-info-transact-sql.md) システム関数を使用して、データベースのページに関する情報を取得できます。 その関数では、object_id、index_id、partition_id など、ヘッダー情報が含まれる 1 行がページから返されます。 この関数を使用すると、ほとんどの場合に `DBCC PAGE` を使用する必要がなくなります。

## <a name="managing-extent-allocations-and-free-space"></a>エクステント割り当てと空き領域の管理 

エクステントの割り当てを管理したり、空き領域を追跡したりする [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] のデータ構造は、比較的単純です。 これには、次のような利点があります。 

* 空き領域情報は高密度で格納されるので、比較的少数のページに格納できます。   
  このため、割り当て情報の取得に必要なディスク読み取り量が減り、処理が高速化します。 また、アロケーション ページがメモリ内にとどまる機会が増え、改めて読み込む必要も少なくなります。 

* 割り当て情報の大部分は相互に連結されていません。 このため、割り当て情報のメンテナンスが容易になっています。    
  個々のページの割り当ておよび割り当て解除は迅速に行うことができます。 これにより、ページ割り当てや割り当て解除を必要とする同時実行中のタスク間の競合が減少します。 

### <a name="managing-extent-allocations"></a>エクステント割り当ての管理

[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] は、エクステントの割り当てを記録するために次の 2 種類のアロケーション マップを使用します。 

- **GAM (Global Allocation Map)**    
  GAM ページには、どのエクステントが既に割り当てられているかが記録されます。 1 つの GAM で 64,000 のエクステント、つまり約 4 ギガバイト (GB) のデータが対象となります。 GAM では対象とする範囲内のエクステント 1 つにつき 1 ビットが使用されます。 このビットが 1 の場合、そのエクステントは空いており、0 の場合は割り当て済みです。 

- **SGAM (Shared Global Allocation Map)**    
  SGAM ページには、混合エクステントとして使用中であり、1 ページ以上が未使用であるエクステントが記録されます。 1 つの SGAM で 64,000 のエクステント、つまり約 4 GB のデータが対象となります。 SGAM では対象とする範囲内のエクステント 1 つにつき 1 ビットが使用されます。 このビットが 1 の場合、そのエクステントは混合エクステントとして使用されており、空きページが含まれています。 ビットが 0 の場合、混合エクステントとして使用されていないエクステントか、すべてのページが使用されている混合エクステントです。 

各エクステントには、現在の使用状況に基づいて GAM および SGAM 内に次のビット パターンが設定されます。 

|エクステントの現在の使用状況 | GAM のビット設定 | SGAM のビット設定 |
|---------|----------|------| 
|空きがあり、未使用 |1 |0 |
|単一エクステントであるか、空きページのない混合エクステント |0 |0 |
|空きページがある混合エクステント |0 |1 |
 
このようなビット パターンの設定により、エクステントの管理アルゴリズムが簡素化されます。 
-   [!INCLUDE[ssDEnoversion](../includes/ssdenoversion-md.md)]が単一エクステントを割り当てる際には、GAM の中から 1 のビットを検索し、そのビットを 0 に設定します。 
-   空きページがある混合エクステントを見つける際には、[!INCLUDE[ssDEnoversion](../includes/ssdenoversion-md.md)] は SGAM の中から 1 のビットを検索します。 
-   [!INCLUDE[ssDEnoversion](../includes/ssdenoversion-md.md)]が混合エクステントを割り当てる際には、GAM の中から 1 のビットを検索してそのビットを 0 に設定し、SGAM 中の対応するビットを 1 に設定します。 
-   [!INCLUDE[ssDEnoversion](../includes/ssdenoversion-md.md)] では、エクステントの割り当てを解除するために、GAM のビットが 1 に、SGAM のビットが 0 に設定されます。 [!INCLUDE[ssDEnoversion](../includes/ssdenoversion-md.md)] によりデータベース内のデータが均等に分散されるので、実際には、この記事の説明よりもさらに高度なアルゴリズムが [!INCLUDE[ssDEnoversion](../includes/ssdenoversion-md.md)] の内部で使用されます。 この高度なアルゴリズムも、エクステント割り当て情報のチェーンを管理する必要がないので簡素化されています。

### <a name="tracking-free-space"></a>空き領域の追跡

**PFS (Page Free Space)** ページには各ページの割り当て状態、個々のページが割り当て済みかどうか、および各ページの空き領域の量が記録されます。 PFS では 1 ページにつき 1 バイトを使用してページが割り当て済みかどうかを示し、割り当て済みの場合はそのページの使用率を 0%、1 ～ 50%、51 ～ 80%、81 ～ 95%、96 ～ 100% の 5 段階で示します。

エクステントがオブジェクトに割り当てられた後は、エクステント内のどのページが割り当て済みでどのページが空いているかを[!INCLUDE[ssDEnoversion](../includes/ssdenoversion-md.md)]が PFS ページに記録します。 この情報は、[!INCLUDE[ssDEnoversion](../includes/ssdenoversion-md.md)]で新しいページを割り当てる必要が生じたときに使用されます。 ページ内の空き領域の量は、ヒープおよび Text/Image ページに関してのみ管理されます。 [!INCLUDE[ssDEnoversion](../includes/ssdenoversion-md.md)]が新しく挿入された行を保持するための空き領域があるページを検索する際に、この情報が使用されます。 新しい行を挿入する場所がインデックス キーの値で設定されるので、インデックスは PFS の追跡を必要としません。

新しい PFS、GAM、または SGAM ページが、それによって追跡記録される範囲が増えるたびに、データ ファイルに追加されます。 つまり、最初の PFS ページから 8,088 ページ後に新しい PFS ページがあり、以降、8,088 ページの間隔で PFS ページが追加されます。 説明すると、ページ ID 1 が PFS ページとなり、ページ ID 8088 が PFS ページとなり、ページ ID 16176 が PFS ページとなります (以下、同様に続きます)。 最初の GAM ページの 64,000 エクステント後に新しい GAM ページがあり、このページがそれに続く 64,000 エクステントを追跡記録します。その後、64,000 エクステントの間隔で続きます。 同様に、最初の SGAM ページの 64,000 エクステント後に新しい SGAM ページがあり、その後、64,000 エクステントの間隔で SGAM ページが追加されます。 下図に、[!INCLUDE[ssDEnoversion](../includes/ssdenoversion-md.md)]がエクステントの割り当てと管理に使用する一連のページを示します。

![manage_extents](../relational-databases/media/manage-extents.gif)

## <a name="managing-space-used-by-objects"></a><a name="IAM"></a> オブジェクトによって使用されている領域の管理 

**IAM (Index Allocation Map)** 1 ページには、アロケーション ユニットが使用する 4 GB 分のデータベース ファイルのエクステントがマップされます。 アロケーション ユニットは次の 3 種類のいずれかです。

- IN_ROW_DATA   
    ヒープまたはインデックスのパーティションを保存します。

- LOB_DATA   
   ラージ オブジェクト (LOB) データ型 (XML、VARBINARY(max)、VARCHAR(max) など) が保持されます。

- ROW_OVERFLOW_DATA   
   VARCHAR、NVARCHAR、VARBINARY、または SQL_VARIANT のいずれかの列に格納される、行サイズが 8,060 バイトの制限を超える可変長のデータが保存されます。 

ヒープまたはインデックスの各パーティションには、IN_ROW_DATA アロケーション ユニットが必ず含まれています。 ヒープまたはインデックスのスキーマによっては、LOB_DATA アロケーション ユニットまたは ROW_OVERFLOW_DATA アロケーション ユニットが含まれる場合もあります。

IAM 1 ページで、GAM ページまたは SGAM ページと同じ 4 GB 分のファイルを管理できます。 複数のファイルのエクステントまたは 1 ファイル内でも 4 GB の単位の複数にまたがるエクステントがアロケーション ユニットに含まれている場合、1 つの IAM チェーンに複数の IAM ページがリンクされます。 したがって、各アロケーション ユニットには、含まれているエクステントが所属するファイル 1 つについて、IAM が少なくとも 1 ページ存在します。 アロケーション ユニットに割り当てられているファイルのエクステントの範囲が 1 ページの IAM に記録できる範囲を超えている場合、1 つのファイルに複数の IAM ページが存在する場合もあります。 

![iam_pages](../relational-databases/media/iam-pages.gif)

IAM ページはアロケーション ユニットごとに必要に応じて割り当てられ、そのファイル内での配置はランダムです。 `sys.system_internals_allocation_units` システム ビューでは、アロケーション ユニットの最初の IAM ページがポイントされています。 アロケーション ユニットのすべての IAM ページは、1 つの IAM チェーンでリンクされています。

> [!IMPORTANT]
> `sys.system_internals_allocation_units` システム ビューは内部専用であり、変更されることがあります。 互換性は保証されません。 このビューは、 [!INCLUDE[ssSDSfull](../includes/sssdsfull-md.md)]では使用できません。 

![iam_chain](../relational-databases/media/iam-chain.gif)
 
アロケーション ユニットのチェーンにリンクされている IAM ページIAM ページには、その IAM ページによってマップされるエクステントの範囲の開始エクステントを示すヘッダーがあります。 また、1 ビットが 1 つのエクステントを表す大きな 1 つのビットマップがあります。 マップ内の最初のビットは、その範囲で最初のエクステントを表し、2 番目のビットは第 2 エクステントを表し、それ以降のビットも同様の順でエクステントを表します。 ビットが 0 の場合、そのビットが表すエクステントは、IAM を所有するアロケーション ユニットに割り当てられていません。 ビットが 1 の場合、そのビットが表すエクステントは、IAM ページを所有するアロケーション ユニットに割り当てられています。

新しい行を挿入する必要があるのに現在のページに使用できる領域がない場合、[!INCLUDE[ssDEnoversion](../includes/ssdenoversion-md.md)]では IAM ページおよび PFS ページで割り当てのためのページを検索するか、ヒープあるいは Text 型または Image 型のページについては行を格納するのに必要な空き領域があるページを検索します。 まず、[!INCLUDE[ssDEnoversion](../includes/ssdenoversion-md.md)]は IAM ページを使用してそのアロケーション ユニットに割り当てられているエクステントを検索します。 それぞれのエクステントについて、[!INCLUDE[ssDEnoversion](../includes/ssdenoversion-md.md)]は使用可能なページがあるかどうかを PFS ページの中で検索します。 IAM ページおよび PFS ページは 1 ページで多数のデータ ページを管理できるので、データベース内の IAM ページおよび PFS ページの数はわずかです。 このため、IAM ページおよび PFS ページは一般的に [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] バッファー プールのメモリに入っており、高速に検索できます。 インデックスの場合、新しい行の挿入ポイントがインデックス キーによって設定されるが、新しいページが必要なときは、前述のプロセスが発生します。

[!INCLUDE[ssDEnoversion](../includes/ssdenoversion-md.md)]によりアロケーション ユニットに新しいエクステントが割り当てられるのは、挿入する行を格納するのに必要な空き領域のあるページが既存のエクステントの中ですぐに見つからない場合のみです。 

<a name="ProportionalFill"></a>[!INCLUDE[ssDEnoversion](../includes/ssdenoversion-md.md)]は **比例配分割り当てアルゴリズム** を使用して、ファイル グループ内の利用可能なエクステントからエクステントを割り当てます。 同じファイル グループに 2 つのファイルがあり、一方のファイルにもう一方の 2 倍の空き領域がある場合は、空きが少ないファイルから 1 ページ割り当てられるごとに、空きが多いファイルからは 2 ページが割り当てられます。 したがって、ファイル グループ内のすべてのファイルは、使用済み領域のパーセンテージがほとんど同じになります。 

## <a name="tracking-modified-extents"></a>変更されたエクステントの追跡 

[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] では、一括コピー操作によって変更されたか、または最後の完全バックアップによって変更されたエクステントを追跡するために、2 つの内部データ構造を使用します。 これらのデータ構造により、差分バックアップの処理速度が大幅に向上します。 また、データベースで一括ログ復旧モデルを使用している場合は、一括ログ コピー操作の速度も向上します。 GAM (グローバル アロケーション マップ) ページや SGAM (共有グローバル アロケーション マップ) ページと同様に、これらの構造はビットマップで、各ビットが 1 つのエクステントを表します。 

- **DCM (差分変更マップ)**    
   最後の `BACKUP DATABASE` ステートメント以降に変更されたエクステントが追跡されます。 エクステントのビットが 1 の場合、そのエクステントは最後の `BACKUP DATABASE` ステートメント以降に変更されています。 このビットが 0 の場合、エクステントは変更されていません。 差分バックアップでは、DCM ページを読み取るだけで、変更されているエクステントを判断します。 これにより、差分バックアップでスキャンしなければならないページの数が大幅に削減されます。 差分バックアップを実行するときの時間の長さは、最後の BACKUP DATABASE ステートメント以降に変更されたエクステントの数に比例し、データベースの全体的なサイズには比例しません。 

- **BCM (一括変更マップ)**    
   最後の `BACKUP LOG` ステートメント以降に、一括ログ記録操作によって変更されたエクステントが追跡されます。 エクステントのビットが 1 の場合、そのエクステントは最後の `BACKUP LOG` ステートメント以降に一括ログ記録操作によって変更されています。 このビットが 0 の場合、一括ログ記録操作によってエクステントは変更されていません。 BCM ページはすべてのデータベースにありますが、データベースで一括ログ復旧モデルを使用している場合にのみ使用します。 この復旧モデルでは、`BACKUP LOG` を実行するときにバックアップ プロセスで BCM をスキャンし、変更されたエクステントを探します。 その後、これらのエクステントをログ バックアップに含めます。 これにより、データベースがデータベース バックアップと、トランザクション ログ バックアップのシーケンスから復元される場合に、一括ログ記録操作を復旧できます。 一括ログ記録操作はログに記録されないので、BCM ページは単純復旧モデルを使用しているデータベースには関係しません。 完全復旧モデルでは一括ログ記録操作が完全にログに記録された操作として扱われるので、BCM ページは完全復旧モデルを使用するデータベースにも関係しません。 

DCM ページと BCM ページ間の間隔は、GAM ページと SGAM ページの間隔と同じで 64,000 エクステントです。 DCM ページと BCM ページは、物理ファイル内の GAM ページと SGAM ページの後に配置されます。

![special_page_order](../relational-databases/media/special-page-order.gif)

## <a name="see-also"></a>参照
[sys.allocation_units &#40;Transact-SQL&#41;](../relational-databases/system-catalog-views/sys-allocation-units-transact-sql.md)     
[ヒープ &#40;クラスター化インデックスなしのテーブル&#41;](../relational-databases/indexes/heaps-tables-without-clustered-indexes.md#heap-structures)       
[sys.dm_db_page_info](../relational-databases/system-dynamic-management-views/sys-dm-db-page-info-transact-sql.md)     
[ページの読み取り](../relational-databases/reading-pages.md)   
[ページの書き込み](../relational-databases/writing-pages.md)   
