---
description: ALTER INDEX (Transact-SQL)
title: ALTER INDEX (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 08/21/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- ALTER INDEX
- ALTER_INDEX_TSQL
dev_langs:
- t-sql
helpviewer_keywords:
- indexes [SQL Server], reorganizing
- ALTER INDEX statement
- indexes [SQL Server], disabling
- online index operations
- index reorganization [SQL Server]
- ALLOW_ROW_LOCKS option
- ALL keyword
- reorganizing indexes
- constraints [SQL Server], indexes
- row locks [SQL Server]
- index rebuilding [SQL Server]
- rebuilding indexes
- locking [SQL Server], indexes
- partitioned indexes [SQL Server], rebuilding
- defragmenting indexes
- disabling indexes
- XML indexes [SQL Server], modifying
- index modifications [SQL Server]
- indexes [SQL Server], modifying
- index options [SQL Server]
- modifying indexes
- index disabling [SQL Server]
- MAXDOP index option, ALTER INDEX statement
- spatial indexes [SQL Server], modifying
- indexes [SQL Server], options
- ALLOW_PAGE_LOCKS option
- page locks [SQL Server]
- index rebuild [SQL Server]
- index reorganize [SQL Server]
ms.assetid: b796c829-ef3a-405c-a784-48286d4fb2b9
author: pmasl
ms.author: pelopes
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 4c23245c88b8c68e075b498b00ab113567f26fc8
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104743102"
---
# <a name="alter-index-transact-sql"></a>ALTER INDEX (Transact-SQL)

[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  インデックスの無効化、再構築、再構成によって、またはインデックスに関するオプションの設定によって、既存のテーブルやビュー インデックス (行ストア、列ストア、または XML) を変更します。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql
-- Syntax for SQL Server and Azure SQL Database
  
ALTER INDEX { index_name | ALL } ON <object>  
{  
      REBUILD {  
            [ PARTITION = ALL ] [ WITH ( <rebuild_index_option> [ ,...n ] ) ]   
          | [ PARTITION = partition_number [ WITH ( <single_partition_rebuild_index_option> ) [ ,...n ] ]  
      }  
    | DISABLE  
    | REORGANIZE  [ PARTITION = partition_number ] [ WITH ( <reorganize_option>  ) ]  
    | SET ( <set_index_option> [ ,...n ] )   
    | RESUME [WITH (<resumable_index_options>,[...n])]
    | PAUSE
    | ABORT
}  
[ ; ]  
  
<object> ::=   
{  
    { database_name.schema_name.table_or_view_name | schema_name.table_or_view_name | table_or_view_name }  
}  
  
<rebuild_index_option > ::=  
{  
      PAD_INDEX = { ON | OFF }  
    | FILLFACTOR = fillfactor   
    | SORT_IN_TEMPDB = { ON | OFF }  
    | IGNORE_DUP_KEY = { ON | OFF }  
    | STATISTICS_NORECOMPUTE = { ON | OFF }  
    | STATISTICS_INCREMENTAL = { ON | OFF }  
    | ONLINE = {   
          ON [ ( <low_priority_lock_wait> ) ]   
        | OFF } 
    | RESUMABLE = { ON | OFF } 
    | MAX_DURATION = <time> [MINUTES}     
    | ALLOW_ROW_LOCKS = { ON | OFF }  
    | ALLOW_PAGE_LOCKS = { ON | OFF }  
    | MAXDOP = max_degree_of_parallelism  
    | DATA_COMPRESSION = { NONE | ROW | PAGE | COLUMNSTORE | COLUMNSTORE_ARCHIVE }   
        [ ON PARTITIONS ( {<partition_number> [ TO <partition_number>] } [ , ...n ] ) ]  
}  
  
<single_partition_rebuild_index_option> ::=  
{  
      SORT_IN_TEMPDB = { ON | OFF }  
    | MAXDOP = max_degree_of_parallelism  
    | RESUMABLE = { ON | OFF } 
    | MAX_DURATION = <time> [MINUTES}     
    | DATA_COMPRESSION = { NONE | ROW | PAGE | COLUMNSTORE | COLUMNSTORE_ARCHIVE } }  
    | ONLINE = { ON [ ( <low_priority_lock_wait> ) ] | OFF }  
}  
  
<reorganize_option>::=  
{  
       LOB_COMPACTION = { ON | OFF }  
    |  COMPRESS_ALL_ROW_GROUPS =  { ON | OFF}  
}  
  
<set_index_option>::=  
{  
      ALLOW_ROW_LOCKS = { ON | OFF }  
    | ALLOW_PAGE_LOCKS = { ON | OFF }  
    | OPTIMIZE_FOR_SEQUENTIAL_KEY = { ON | OFF }
    | IGNORE_DUP_KEY = { ON | OFF }  
    | STATISTICS_NORECOMPUTE = { ON | OFF }  
    | COMPRESSION_DELAY= { 0 | delay [Minutes] }  
}  

<resumable_index_option> ::=
 { 
    MAXDOP = max_degree_of_parallelism
    | MAX_DURATION =<time> [MINUTES]
    | <low_priority_lock_wait>  
 }
 
<low_priority_lock_wait>::=  
{  
    WAIT_AT_LOW_PRIORITY ( MAX_DURATION = <time> [ MINUTES ] ,   
                          ABORT_AFTER_WAIT = { NONE | SELF | BLOCKERS } )  
}  

```  
  
```syntaxsql
-- Syntax for Azure Synapse Analytics and Parallel Data Warehouse 
  
ALTER INDEX { index_name | ALL }  
    ON   [ schema_name. ] table_name  
{  
      REBUILD {  
            [ PARTITION = ALL [ WITH ( <rebuild_index_option> ) ] ] 
          | [ PARTITION = partition_number [ WITH ( <single_partition_rebuild_index_option> )] ] 
      }  
    | DISABLE  
    | REORGANIZE [ PARTITION = partition_number ]  
}  
[;]  

<rebuild_index_option > ::=   
{  
    DATA_COMPRESSION = { COLUMNSTORE | COLUMNSTORE_ARCHIVE }
        [ ON PARTITIONS ( {<partition_number> [ TO <partition_number>] } [ , ...n ] ) ]   
}

<single_partition_rebuild_index_option > ::=   
{  
    DATA_COMPRESSION = { COLUMNSTORE | COLUMNSTORE_ARCHIVE }  
}  
  
```

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数

 *index_name*  
 インデックスの名前です。 インデックス名は、テーブルまたはビュー内では一意である必要がありますが、データベース内で一意である必要はありません。 インデックス名は、[識別子](../../relational-databases/databases/database-identifiers.md)の規則に従っている必要があります。  
  
 ALL  
 インデックスの種類に関係なく、テーブルまたはビューに関連付けられているすべてのインデックスを指定します。 1 つ以上のインデックスがオフラインまたは読み取り専用のファイル グループにあるか、指定した操作が 1 つ以上のインデックスの種類に許可されていない場合、ALL を指定するとステートメントは失敗します。 次の表は、インデックス操作と、許可されないインデックスの種類の一覧です。  
  
|キーワード ALL を使用する操作|テーブル内に存在すると操作が失敗するインデックスの種類|  
|----------------------------------------|----------------------------------------|  
|REBUILD WITH ONLINE = ON|XML インデックス<br /><br /> 空間インデックス<br /><br /> 列ストア インデックス:**適用対象:** [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (開始値 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)]) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]|  
|REBUILD PARTITION = *partition_number*|非パーティション インデックス、XML インデックス、空間インデックス、または無効化されたインデックス|  
|REORGANIZE|ALLOW_PAGE_LOCKS が OFF に設定されたインデックス|  
|REORGANIZE PARTITION = *partition_number*|非パーティション インデックス、XML インデックス、空間インデックス、または無効化されたインデックス|  
|IGNORE_DUP_KEY = ON|XML インデックス<br /><br /> 空間インデックス<br /><br /> 列ストア インデックス:**適用対象:** [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (開始値 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)]) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]|  
|ONLINE = ON|XML インデックス<br /><br /> 空間インデックス<br /><br /> 列ストア インデックス:**適用対象:** [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (開始値 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)]) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]|
|RESUMABLE = ON| **All** キーワードでサポートされていない再開可能なインデックス。 <br /><br /> **適用対象**:[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (開始値 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)]) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] |   
  
> [!WARNING]
> オンラインで実行できるインデックス操作の詳細については、「[オンライン インデックス操作のガイドライン](../../relational-databases/indexes/guidelines-for-online-index-operations.md)」を参照してください。

 ALL を PARTITION = *partition_number* と共に指定する場合、すべてのインデックスを固定する必要があります。 つまり、すべてのインデックスは、等価パーティション関数に基づいてパーティション分割されます。 ALL を PARTITION と共に使用すると、同じ *partition_number* を持つすべてのインデックス パーティションが再構築または再構成されることになります。 パーティション インデックスの詳細については、「 [Partitioned Tables and Indexes](../../relational-databases/partitions/partitioned-tables-and-indexes.md)」を参照してください。  
  
 *database_name*  
 データベースの名前です。  
  
 *schema_name*  
 テーブルまたはビューが属するスキーマの名前を指定します。  
  
 *table_or_view_name*  
 インデックスに関連付けられているテーブルまたはビューの名前です。 オブジェクトに対するインデックスのレポートを表示するには、[sys.indexes](../../relational-databases/system-catalog-views/sys-indexes-transact-sql.md) カタログ ビューを使用します。  
  
 [!INCLUDE[ssSDS](../../includes/sssds-md.md)] では、database_name が現在のデータベースの場合、または database_name が tempdb であり、table_or_view_name が # で始まる場合に、3 つの要素で構成された名前形式 database_name.[schema_name].table_or_view_name をサポートします。  
  
 REBUILD [ WITH **(** \<rebuild_index_option> [ **,** ... *n*] **)** ]  
  
**適用対象**:[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (開始値 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)]) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 

同じ列、インデックスの種類、一意性属性、および並べ替え順に従って、インデックスを再構築します。 この句には [DBCC DBREINDEX](../../t-sql/database-console-commands/dbcc-dbreindex-transact-sql.md) と同じ機能があります。 REBUILD では、無効化されたインデックスが有効になります。 クラスター化インデックスを再構築しても、キーワード ALL を指定しない限り、関連付けられている非クラスター化インデックスは再構築されません。 インデックス オプションを指定しない場合は、[sys.indexes](../../relational-databases/system-catalog-views/sys-indexes-transact-sql.md) に格納されている既存のインデックス オプション値が適用されます。 値が **sys.indexes** に格納されていないインデックス オプションについては、オプションの引数に定義されている既定値が適用されます。  
  
 ALL を指定した場合で、基になるテーブルがヒープの場合、テーブルは再構築操作の影響を受けません。 テーブルに関連付けられている非クラスター化インデックスは再構築されます。  
  
 データベース復旧モデルが一括ログ復旧モデルまたは単純復旧モデルのいずれかに設定されている場合、再構築操作のログへの記録は最小限にできます。  
  
> [!NOTE]
> プライマリ XML インデックスを再構築するとき、基になるユーザー テーブルはインデックス操作の間使用できなくなります。  
 
列ストア インデックスの場合、再構築操作。  
  
-  並べ替え順序を使用しません。  
-  再構築が行われている間、テーブルまたはパーティションを排他的にロックします。  データは、NOLOCK、RCSI、または SI を使用する場合でも、"オフライン" であり、再構築中に使用できません。  
-  すべてのデータを列ストアに再圧縮します。 再構築が行われている間、列ストア インデックスのコピーが 2 つ存在します。 再構築が完了したら、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] により、元の列ストア インデックスが削除されます。  

詳細については、「 [インデックスの再編成と再構築](../../relational-databases/indexes/reorganize-and-rebuild-indexes.md)」を参照してください。 
  
PARTITION  

**適用対象**:[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (開始値 [!INCLUDE[ssKatmai](../../includes/ssKatmai-md.md)]) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]  
  
 インデックスの 1 つのパーティションのみを再構築または再構成します。 PARTITION は、*index_name* がパーティション インデックス以外の場合は指定できません。  
  
 PARTITION = ALL により、すべてのパーティションが再構築されます。  
  
> [!WARNING]
> 固定されていないインデックスをパーティションが 1, 000 個以上あるテーブルに作成または再構築することは可能ですが、サポートされていません。 このような操作を行うと、操作中にパフォーマンスが低下したりメモリが過度に消費される可能性があります。 パーティションの数が 1,000 個を超えた場合は、固定されたインデックスのみを使用することをお勧めします。  
  
 *partition_number*  
   
**適用対象**:[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (開始値 [!INCLUDE[ssKatmai](../../includes/ssKatmai-md.md)]) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]
  
 再構築または再構成するパーティション インデックスのパーティション番号を指定します。 *partition_number* は変数を参照できる定数式です。 これにはユーザー定義型の変数または関数、およびユーザー定義関数が含まれますが、[!INCLUDE[tsql](../../includes/tsql-md.md)] ステートメントを参照することはできません。 *partition_number* は必須であり、指定しないとステートメントは失敗します。  
  
 WITH **(** \<single_partition_rebuild_index_option> **)**  
   
**適用対象**:[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (開始値 [!INCLUDE[ssKatmai](../../includes/ssKatmai-md.md)]) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]  
  
 `SORT_IN_TEMPDB`、`MAXDOP`、および `DATA_COMPRESSION` は、単一のパーティション (PARTITION = *partition_number*) をリビルドするときに指定できるオプションです。 XML インデックスは、単一のパーティションの再構築操作では指定できません。  
  
 DISABLE  
 インデックスを無効とマークし、[!INCLUDE[ssDE](../../includes/ssde-md.md)]で使用されないようにします。 どのインデックスも無効にできます。 無効になったインデックスのインデックス定義は、基になるインデックス データがなくてもシステム カタログに残ります。 クラスター化インデックスを無効にすると、基になるテーブル データをユーザーのアクセスができなくなります。 インデックスを有効にするには、ALTER INDEX REBUILD または CREATE INDEX WITH DROP_EXISTING を使用します。 詳細については、「[インデックスと制約の無効化](../../relational-databases/indexes/disable-indexes-and-constraints.md)」および「[インデックスと制約の有効化](../../relational-databases/indexes/enable-indexes-and-constraints.md)」を参照してください。  
  
 REORGANIZE (**行ストア** インデックスの再構成)  
 行ストア インデックスの場合、REORGANIZE ではインデックス リーフ レベルを再構成するように指定します。 REORGANIZE 操作は次のとおりです。  
  
-   常にオンラインで実行されます。 つまり、ALTER INDEX REORGANIZE トランザクション中は、長期にわたって他をブロックするテーブル ロックは保持されず、基になるテーブルへのクエリまたは更新を続行できます。
-   無効なインデックスに対しては指定できません。
-   ALLOW_PAGE_LOCKS が OFF に設定されている場合は指定できません。
-   トランザクション内で実行されている場合、トランザクションがロールバックされても、REORGANIZE はロールバックされません。

> [!NOTE]
> ALTER INDEX REORGANIZE で既定の暗黙のトランザクション モードの代わりに、明示的なトランザクションが使用されている場合 (たとえば、BEGIN TRAN ...COMMIT/ROLLBACK 内の ALTER INDEX)、REORGANIZE のロック動作がより制限の厳しいものになり、ブロッキングが発生する可能性があります。 暗黙のトランザクションの詳細については、「[SET IMPLICIT_TRANSACTIONS &#40;Transact-SQL&#41;](../../t-sql/statements/set-implicit-transactions-transact-sql.md)」を参照してください。

詳細については、「 [インデックスの再編成と再構築](../../relational-databases/indexes/reorganize-and-rebuild-indexes.md)」を参照してください。 

REORGANIZE WITH **(** LOB_COMPACTION = { **ON** | OFF } **)**  
 行ストア インデックスに適用されます。  
  
LOB_COMPACTION = ON  
  
-   これらのラージ オブジェクト (LOB) データ型のデータを含むすべてのページを圧縮するように指定します。LOB データ型には image、text、ntext、varchar (max)、nvarchar (max)、varbinary (max)、xml があります。 このデータを圧縮すると、ディスク上のデータ サイズを縮小できます。  
-   クラスター化インデックスの場合、テーブルに含まれているすべての LOB 列が圧縮されます。  
-   非クラスター化インデックスの場合、そのインデックス内で非キー列 (付加列) となっているすべての LOB 列が圧縮されます。  
-   REORGANIZE ALL はすべてのインデックスに対して LOB_COMPACTION を実行します。 インデックスごとに、クラスター化インデックス内のすべての LOB 列、基になるテーブル、または非クラスター化インデックス内の付加列が圧縮されます。  
  
LOB_COMPACTION = OFF  
  
-   ラージ オブジェクト データを含むページは圧縮されません。  
-   OFF の指定は、ヒープには影響しません。  
  
 REORGANIZE (**列ストア** インデックスの再構成)  
 列ストア インデックスごとに、REORGANIZE は各 CLOSED デルタ行グループを圧縮し、圧縮された行グループとして列ストアに移動します。 REORGANIZE 操作は常にオンラインで実行されます。 つまり、ALTER INDEX REORGANIZE トランザクション中は、長期にわたって他をブロックするテーブル ロックは保持されず、基になるテーブルへのクエリまたは更新を続行できます。 詳細については、「 [インデックスの再編成と再構築](../../relational-databases/indexes/reorganize-and-rebuild-indexes.md)」を参照してください。 
  
-   CLOSED デルタ行グループを圧縮された行グループに移動する場合、REORGANIZE は必須ではありません。 CLOSED デルタ行グループを圧縮するために、組ムーバー (TM) バックグラウンド プロセスが定期的に起動します。 組ムーバーに遅延がある場合は、REORGANIZE の使用をお勧めします。 REORGANIZE では、行グループをより積極的に圧縮できます。  
-   すべての OPEN 行グループと CLOSED 行グループを圧縮するには、このセクションの `REORGANIZE WITH (COMPRESS_ALL_ROW_GROUPS)` オプションを参照してください。  
  
[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 以降) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] における列ストア インデックスの場合、REORGANIZE では、次の追加のデフラグ最適化がオンラインで実行されます。  
  
-   行の 10% 以上が論理的に削除されたとき、行グループから行を物理的に削除します。 削除されたバイトは、物理メディア上で解放されます。 たとえば、100 万行から成る圧縮された行グループで 10 万行が論理的に削除された場合、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] は、削除された行を物理的に削除し、90 万行を含む行グループを圧縮し直します。 削除された行を解放することで、記憶域が節約されます。  
  
-   1 つまたは複数の圧縮された行グループを結合して、行グループあたりの行数を最大で 1,048,576 行まで増やすことができます。 たとえば、102,400 行のバッチを 5 つ一括でインポートした場合は、5 つの圧縮された行グループが得られます。 REORGANIZE を実行すると、これらの行グループは圧縮された 1 つの行グループにマージされ、そのサイズは 512,000 行となります。 この処理は、ディクショナリ サイズまたはメモリに関する制限が存在していないことを前提としています。  
  
-   行グループの 10% 以上の行が論理的に削除された場合、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ではこのような行グループを 1 つまたは複数の行グループと結合することが試みられます。 たとえば、行グループ 1 は 500,000 行の圧縮された行グループであり、行グループ 21 は最大 1,048, 576 行の圧縮された行グループであるとします。 行グループ 21 は行の 60% が削除され、残りが 409,830 行になっています。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、優先的にこの 2 つの行グループが結合されて、909,830 行の新しい行グループが圧縮されます。  
  
REORGANIZE WITH ( COMPRESS_ALL_ROW_GROUPS = { ON | **OFF** } )  
 列ストア インデックスに適用されます。 

 **適用対象:** [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (開始値 [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)]) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]

COMPRESS_ALL_ROW_GROUPS では、OPEN デルタ行グループまたは CLOSED デルタ行グループを強制的に列ストアに移動することができます。 このオプションを使用すると、デルタ行グループを空にするために列ストア インデックスを再構築する必要がありません。  このオプションを他の削除およびマージ最適化機能と組み合わせて使用すれば、ほとんどの状況でインデックスを再構築する必要はなくなります。    

-   ON を指定すると、サイズおよび状態 (CLOSED または OPEN) に関係なく、すべての行グループが強制的に列ストアに移動されます。  
-   OFF を指定すると、すべての CLOSED 行グループが強制的に列ストアに移動されます。  

詳細については、「 [インデックスの再編成と再構築](../../relational-databases/indexes/reorganize-and-rebuild-indexes.md)」を参照してください。 

SET **(** \<set_index option> [ **,** ... *n*] **)**  
 インデックスを再構築または再構成しないでインデックス オプションを指定します。 無効化されたインデックスには、SET は指定できません。  
  
PAD_INDEX = { ON | OFF }  
   
**適用対象**:[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (開始値 [!INCLUDE[ssKatmai](../../includes/ssKatmai-md.md)]) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]  

 インデックスの埋め込みを指定します。 既定値は OFF です。  
  
 ON  
 FILLFACTOR で指定される空き領域のパーセンテージが、インデックスの中間レベルのページに適用されます。 PAD_INDEX を ON に設定するときに FILLFACTOR を指定しなければ、[sys.indexes](../../relational-databases/system-catalog-views/sys-indexes-transact-sql.md) に格納されている FILL FACTOR 値が使用されます。  
  
 OFF または *fillfactor* の指定なし  
 中間レベルのページは、ほぼ全容量が使用されます。 この場合、中間ページのキーのセットに基づき、インデックスに割り当てることのできる 1 行以上の最大サイズが収まる分の領域は残されます。  
  
 詳細については、「[CREATE INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/create-index-transact-sql.md)」を参照してください。  
  
FILLFACTOR = *fillfactor*  
 
 **適用対象**:[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (開始値 [!INCLUDE[ssKatmai](../../includes/ssKatmai-md.md)]) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]
  
 インデックスの作成時または変更時に、[!INCLUDE[ssDE](../../includes/ssde-md.md)] が各インデックス ページのリーフ レベルをどの程度まで埋めるかを、パーセント値で指定します。 *fillfactor* 値には、1 ～ 100 の整数値を指定してください。 既定値は 0 です。 FILL FACTOR 値 0 と 100 の機能は、まったく同じです。  
  
 明示的な FILLFACTOR 設定値は、インデックスの初回作成時または再構築時のみ適用されます。 [!INCLUDE[ssDE](../../includes/ssde-md.md)] では、ページ内で指定されたパーセント分の空き領域は動的に保持されません。 詳細については、「[CREATE INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/create-index-transact-sql.md)」を参照してください。  
  
 FILL FACTOR 設定を表示するには、**sys.indexes** を使用します。  
  
> [!IMPORTANT]
> [!INCLUDE[ssDE](../../includes/ssde-md.md)]ではクラスター化インデックスの作成時にデータが再分配されるため、FILLFACTOR 値を使用してクラスター化インデックスを作成または変更すると、データ用のストレージ領域のサイズに影響が生じます。  
  
 SORT_IN_TEMPDB = { ON | **OFF** }  
 
**適用対象**:[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (開始値 [!INCLUDE[ssKatmai](../../includes/ssKatmai-md.md)]) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]  
  
 **tempdb** に並べ替え結果を格納するかどうかを指定します。 Azure SQL Database Hyperscale を除き、既定値は OFF です。 Hyperscale のインデックス作成操作についてはすべて、再開可能なインデックス リビルドが使用されていない限り、SORT_IN_TEMPDB は常に ON になります。  
  
 ON  
 インデックス構築に使用される中間の並べ替え結果が **tempdb** に格納されます。 **tempdb** がユーザー データベースとは異なるディクス セット上にある場合は、インデックスの作成に必要な時間が短縮されることがあります。 インデックスの構築中に使用されるディスク領域のサイズは増加します。  
  
 OFF  
 中間の並べ替え結果はインデックスと同じデータベースに格納されます。  
  
 並べ替え操作が必要ない場合、または並べ替えをメモリ内で実行できる場合、SORT_IN_TEMPDB オプションは無視されます。  
  
 詳細については、「[インデックスの SORT_IN_TEMPDB オプション](../../relational-databases/indexes/sort-in-tempdb-option-for-indexes.md)」を参照してください。  
  
 IGNORE_DUP_KEY **=** { ON | OFF }  
 挿入操作で、一意のインデックスに重複するキー値を挿入しようとした場合のエラー応答を指定します。 IGNORE_DUP_KEY オプションは、インデックスが作成または再構築された後の挿入操作のみに適用されます。 既定値は OFF です。  
  
 ON  
 重複したキー値が一意のインデックスに挿入されると、警告メッセージが表示されます。 一意性制約に違反する行のみが失敗します。  
  
 OFF  
 重複したキー値が一意のインデックスに挿入されると、エラー メッセージが表示されます。 INSERT 操作全体がロールバックされます。  
  
 ビューに作成されたインデックス、一意でないインデックス、XML インデックス、空間インデックス、およびフィルター処理されたインデックスについては、IGNORE_DUP_KEY を ON に設定することはできません。  
  
 IGNORE_DUP_KEY を表示するには、[sys.indexes](../../relational-databases/system-catalog-views/sys-indexes-transact-sql.md) を使用します。  
  
 旧バージョンと互換性のある構文では、WITH IGNORE_DUP_KEY は WITH IGNORE_DUP_KEY = ON と同じです。  
  
 STATISTICS_NORECOMPUTE **=** { ON | OFF }  
 分布統計を再計算するかどうかを指定します。 既定値は OFF です。  
  
 ON  
 古い統計情報は、自動的には再計算されません。  
  
 OFF  
 自動統計更新が有効です。  
  
 自動統計更新を復元するには、STATISTICS_NORECOMPUTE を OFF に設定するか、NORECOMPUTE 句を指定せずに UPDATE STATISTICS を実行します。  
  
> [!IMPORTANT]
> 分布統計の自動再計算を無効にすると、クエリ オプティマイザーで、テーブルに関連するクエリの最適実行プランが選択されなくなる場合があります。  
  
STATISTICS_INCREMENTAL = { ON | **OFF** }  

**適用対象**:[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (開始値 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)]) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]

**ON** の場合、作成される統計はパーティションごとの統計です。 **OFF** の場合、統計ツリーが削除され、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] によって統計が再計算されます。 既定値は **OFF** です。  
  
 パーティションごとの統計がサポートされていない場合、このオプションは無視され、警告が生成されます。 次の種類の統計では、増分統計がサポートされていません。  
  
-   ベース テーブルにパーティションで固定されていないインデックスを使用して作成された統計  
-   Always On の読み取り可能なセカンダリ データベースに対して作成された統計  
-   読み取り専用のデータベースに対して作成された統計  
-   フィルター選択されたインデックスに対して作成された統計  
-   ビューに対して作成された統計  
-   内部テーブルに対して作成された統計  
-   空間インデックスまたは XML インデックスを使用して作成された統計  
  
 ONLINE **=** { ON | **OFF** } \<as applies to rebuild_index_option>  
 インデックス操作時に、基になるテーブルや関連するインデックスをクエリやデータ変更で使用できるかどうかを指定します。 既定値は OFF です。  
  
 XML インデックスまたは空間インデックスの場合、`ONLINE = OFF` だけがサポートされます。ONLINE を ON に設定すると、エラーが発生します。  
  
> [!IMPORTANT]
> オンラインでのインデックス操作は、[!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のすべてのエディションで使用できるわけではありません。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の各エディションでサポートされる機能の一覧については、「[[!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] の各エディションとサポートされている機能](../../sql-server/editions-and-supported-features-for-sql-server-2016.md)」および「[SQL Server 2017 の各エディションとサポートされている機能](../../sql-server/editions-and-components-of-sql-server-2017.md)」を参照してください。  
  
 ON  
 長期のテーブル ロックは、インデックス操作の間は保持されません。 インデックス操作の主なフェーズの間は、基になるテーブル上に、インテント共有 (IS) ロックのみが保持されます。 これによって、基になるテーブルおよびインデックスに対してクエリや更新を続けることができます。 操作の開始時、非常に短い時間、ソース オブジェクトでは共有 (S) ロックが保持されます。 操作の終了時、非クラクタ化インデックスが作成される場合は、短い時間、ソース オブジェクト上で S ロックが保持されます。また、クラスター化インデックスがオンラインで作成または削除されるか、クラスター化または非クラスター化インデックスが再構築される場合は、SCH-M (スキーマ修正) ロックが取得されます。 インデックスがローカルの一時テーブルに作成される場合、ONLINE は ON にできません。  
  
 OFF  
 テーブル ロックは、インデックス操作の間適用されます。 オフラインのインデックス操作で、クラスター化インデックス、空間インデックス、XML インデックスの作成、再構築、削除を行う場合や、非クラスター化インデックスの再構築、削除を行う場合は、テーブルで Sch-M (スキーマ修正) ロックが取得されます。 このため、操作中は、すべてのユーザーは基になるテーブルにアクセスできません。 非クラスター化インデックスを作成するオフライン インデックス操作では、テーブルの共有 (S) ロックが取得されます。 この場合は、基になるテーブルに対して更新は許可されませんが、SELECT ステートメントなどの読み取り操作は許可されます。  
  
詳しくは、「 [Perform Index Operations Online](../../relational-databases/indexes/perform-index-operations-online.md)」をご覧ください。  
  
インデックスは、グローバル一時テーブル上のインデックスを含めてオンラインでリビルドできます。ただし次の場合は例外です。
- XML インデックス
- ローカル一時テーブルのインデックス
- ビューの最初の一意クラスター化インデックス
- 列ストア インデックス
- 基になるテーブルに LOB データ型 (**image**、**ntext**、**text**) および空間データ型が含まれる場合のクラスター化インデックス。
- **varchar(max)** 列と **varbinary(max)** 列は、インデックスの一部にすることはできません。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] では、テーブルに **varchar(max)** 列または **varbinary(max)** 列が含まれている場合、他の列を含むクラスター化インデックスは、**ONLINE** オプションを使用して作成または再作成できます。 ベース テーブルに **varchar(max)** 列または **varbinary(max)** 列が含まれている場合、[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] では **ONLINE** オプションが許可されません

詳細については、「[オンライン インデックス操作の動作原理](../../relational-databases/indexes/how-online-index-operations-work.md)」を参照してください。

RESUMABLE **=** { ON | **OFF**}

**適用対象**:[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (開始値 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)]) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]   

 オンラインでのインデックス操作が再開可能かどうかを指定します。

 ON の場合、インデックス操作は再開可能です。

 OFF の場合、インデックス操作は再開可能ではありません。

MAX_DURATION **=** *time* **[MINUTES]** は **RESUMABLE = ON** (**ONLINE = ON** が必須) と共に使用。

**適用対象**:[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (開始値 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)]) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 

再開可能なオンラインでのインデックス操作が、一時停止までに実行される時間 (分単位で指定する整数値) を示します。 

> [!IMPORTANT]
> オンラインで実行できるインデックス操作の詳細については、「[オンライン インデックス操作のガイドライン](../../relational-databases/indexes/guidelines-for-online-index-operations.md)」を参照してください。

> [!NOTE] 
> 再開可能なオンライン インデックスのリビルドは、列ストア インデックスではサポートされていません。

ALLOW_ROW_LOCKS **=** { **ON** | OFF }  

**適用対象**:[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (開始値 [!INCLUDE[ssKatmai](../../includes/ssKatmai-md.md)]) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]  
  
 行ロックを許可するかどうかを指定します。 既定値は ON です。  
  
 ON  
 インデックスにアクセスするとき、行ロックが許可されます。 いつ行ロックを使用するかは、[!INCLUDE[ssDE](../../includes/ssde-md.md)]によって決定されます。  
  
 OFF  
 行ロックは使用されません。  
  
ALLOW_PAGE_LOCKS **=** { **ON** | OFF }  
  
**適用対象**:[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (開始値 [!INCLUDE[ssKatmai](../../includes/ssKatmai-md.md)]) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]
  
 ページ ロックを許可するかどうかを指定します。 既定値は ON です。  
  
 ON  
 インデックスにアクセスするとき、ページ ロックが許可されます。 いつページ ロックを使用するかは、[!INCLUDE[ssDE](../../includes/ssde-md.md)] によって決定されます。  
  
 OFF  
 ページ ロックは使用されません。  
  
> [!NOTE]
> ALLOW_PAGE_LOCKS を OFF に設定した場合、インデックスを再構成することはできません。  

 OPTIMIZE_FOR_SEQUENTIAL_KEY = { ON | **OFF** }

**適用対象**:[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (開始値 [!INCLUDE[sql-server-2019](../../includes/sssql19-md.md)]) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]

最終ページ挿入競合に対して最適化するかどうかを指定します。 既定値は OFF です。 詳細については、CREATE INDEX のページの「[シーケンシャル キー](./create-index-transact-sql.md#sequential-keys)」セクションを参照してください。

 MAXDOP **=** max_degree_of_parallelism  
 
**適用対象**:[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (開始値 [!INCLUDE[ssKatmai](../../includes/ssKatmai-md.md)]) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]  
  
 インデックス操作の間、**max degree of parallelism** 構成オプションをオーバーライドします。 詳細については、「 [max degree of parallelism サーバー構成オプションの構成](../../database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option.md)」を参照してください。 並列プランの実行で使用されるプロセッサ数を制限するには、MAXDOP を使用します。 最大数は 64 プロセッサです。  
  
> [!IMPORTANT]
>  MAXDOP オプションはすべての XML インデックスで構文的にサポートされていますが、空間インデックスまたはプライマリ XML インデックスの場合、現在の ALTER INDEX では単一のプロセッサのみが使用されます。  
  
 *max_degree_of_parallelism* は次のように指定できます。  
  
 1  
 並列プラン生成を抑制します。  
  
 \>1  
 並列インデックス操作で使用される最大プロセッサ数を、指定した数に制限します。  
  
 0 (既定値)  
 現在のシステム ワークロードに基づいて、実際の数以下のプロセッサを使用します。  
  
 詳細については、「 [並列インデックス操作の構成](../../relational-databases/indexes/configure-parallel-index-operations.md)」を参照してください。  
  
> [!NOTE]
> 並列インデックス操作は、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の一部のエディションで使用できません。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の各エディションでサポートされる機能の一覧については、「[Editions and Supported Features for SQL Server 2016](../../sql-server/editions-and-components-of-sql-server-2016.md)」 (SQL Server 2016 のエディションとサポートされる機能) を参照してください。  
  
COMPRESSION_DELAY **=** { **0** |*期間 [分]* }  

**適用対象:** [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 以降)  
  
 ディスク ベースのテーブルの場合は、CLOSED 状態のデルタ行グループがそのデルタ行グループに留まる必要がある最低限の分数が遅延によって指定され、その時間が経過すると、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] は行グループを、圧縮された行グループに圧縮できるようになります。 ディスク ベース テーブルでは個々の行において挿入時間と更新時間が追跡されないため、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] は CLOSED 状態のデルタ行グループに遅延を適用します。  
  
 既定値は、0 分です。  
  
 COMPRESSION_DELAY を使用する場合の推奨事項については、「[列ストアを使用したリアルタイム運用分析の概要](../../relational-databases/indexes/get-started-with-columnstore-for-real-time-operational-analytics.md)」をご覧ください。  
  
 DATA_COMPRESSION  
 指定したインデックス、パーティション番号、またはパーティション範囲に、データ圧縮オプションを指定します。 次のようなオプションがあります。  
  
 NONE  
 インデックスまたは指定したパーティションが圧縮されません。 これは、列ストア インデックスには適用されません。  
  
 ROW  
 行の圧縮を使用して、インデックスまたは指定したパーティションが圧縮されます。 これは、列ストア インデックスには適用されません。  
  
 PAGE  
 ページの圧縮を使用して、インデックスまたは指定したパーティションが圧縮されます。 これは、列ストア インデックスには適用されません。  
  
 COLUMNSTORE  
   
**適用対象**:[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (開始値 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)]) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]
  
 非クラスター化列ストアとクラスター化列ストア インデックスの両方を含む列ストア インデックスにのみ適用されます。 COLUMNSTORE は、COLUMNSTORE_ARCHIVE オプションで圧縮されたインデックスまたは指定のパーティションを解凍するように指定します。 復元されるデータは、すべての列ストア インデックスに使用された列ストア圧縮を使用して引き続き圧縮されます。  
  
 COLUMNSTORE_ARCHIVE  
  
**適用対象**:[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (開始値 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)]) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]
  
 非クラスター化列ストアとクラスター化列ストア インデックスの両方を含む列ストア インデックスにのみ適用されます。 COLUMNSTORE_ARCHIVE は、指定したパーティションをより小さなサイズにさらに圧縮します。 これは、アーカイブ用や、ストレージのサイズを減らす必要があり、かつ保存と取得に時間をかける余裕があるその他の状況で使用できます。  
  
 圧縮の詳細については、「[データ圧縮](../../relational-databases/data-compression/data-compression.md)」を参照してください。  
  
 ON PARTITIONS **(** { \<partition_number_expression> | \<range> } [ **,** ...n] **)**  
    
**適用対象**:[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (開始値 [!INCLUDE[ssKatmai](../../includes/ssKatmai-md.md)]) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 
  
 DATA_COMPRESSION 設定を適用するパーティションを指定します。 インデックスがパーティション分割されていない場合に ON PARTITIONS 引数を使用すると、エラーが発生します。 ON PARTITIONS 句を指定しないと、パーティション インデックスのすべてのパーティションに対して DATA_COMPRESSION オプションが適用されます。  
  
 \<partition_number_expression> は以下の方法で指定できます。  
  
-   パーティション番号を指定します (例: `ON PARTITIONS (2)`)。  
-   コンマで区切った複数の個別のパーティションのパーティション番号を指定します (例: `ON PARTITIONS (1, 5)`)。  
-   次のように範囲と個別のパーティションの両方を指定します: `ON PARTITIONS (2, 4, 6 TO 8)`。  
  
 \<range> はパーティション番号として、TO で区切って指定できます。たとえば、`ON PARTITIONS (6 TO 8)` のようになります。  
  
 さまざまなパーティションにさまざまな種類のデータ圧縮を設定するには、DATA_COMPRESSION オプションを複数回指定します。例:  
  
```sql  
REBUILD WITH   
(  
DATA_COMPRESSION = NONE ON PARTITIONS (1),   
DATA_COMPRESSION = ROW ON PARTITIONS (2, 4, 6 TO 8),   
DATA_COMPRESSION = PAGE ON PARTITIONS (3, 5)  
);  
```  
  
 ONLINE **=** { ON  | **OFF** } \<as applies to single_partition_rebuild_index_option>  
 インデックスまたは基になるテーブルのインデックス パーティションをオンラインまたはオフラインで再構築できるかどうかを指定します。 **REBUILD** がオンライン (**ON**) で行われる場合、このテーブルのデータは、インデックス操作中にクエリやデータ変更で使用できます。  既定値は **OFF** です。  
  
 ON  
 長期のテーブル ロックは、インデックス操作の間は保持されません。 インデックス操作の主要フェーズの期間、ソース テーブルではインテント共有 (IS) ロックのみが保持されます。 インデックスの再構築を開始するときにテーブルに対する S ロックが必要であり、オンライン インデックス再構築を終了するときにテーブルに対する Sch-M ロックが必要です。 どちらのロックも短いメタデータ ロックですが、特に Sch-M ロックは、すべてのブロックしているトランザクションの完了を待機する必要があります。 待機中、Sch-M ロックは、同じテーブルにアクセスするためにこのロックの後に待機している他のすべてのトランザクションをブロックします。  
  
> [!NOTE]
>  オンライン インデックス再構築では、このセクションの後の方で説明されている *low_priority_lock_wait* オプションを設定できます。  
  
 OFF  
 テーブル ロックは、インデックス操作の間適用されます。 このため、操作中は、すべてのユーザーは基になるテーブルにアクセスできません。  
  
 WAIT_AT_LOW_PRIORITY は **ONLINE = ON** の場合にのみ使用。  
 
**適用対象**:[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (開始値 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)]) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]
  
 オンライン インデックス再構築では、このテーブルに対する操作がブロックされるまで待機する必要があります。 **WAIT_AT_LOW_PRIORITY** は、オンライン インデックス再構築操作が優先度の低いロックを待機して、オンライン インデックス構築操作が待機している間、他の操作を続行可能にすることを示します。 **WAIT AT LOW PRIORITY** オプションを省略すると、WAIT_AT_LOW_PRIORITY (MAX_DURATION = 0 minutes, ABORT_AFTER_WAIT = NONE) と同等になります。 詳細については、「[WAIT_AT_LOW_PRIORITY](alter-index-transact-sql.md)」を参照してください。 
  
 MAX_DURATION = *time* **[MINUTES]**  
  
**適用対象**:[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (開始値 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)]) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]
  
 オンライン インデックス再構築のロックが、DDL コマンドの実行時に低い優先度で待機する時間 (分単位で指定した整数値) です。 操作が **MAX_DURATION** の期間ブロックされると、**ABORT_AFTER_WAIT** アクションのいずれかが実行されます。 **MAX_DURATION** の期間は常に分単位なので、**MINUTES** という単語は省略できます。  
 
 ABORT_AFTER_WAIT = [**NONE** | **SELF** | **BLOCKERS** } ]  
   
**適用対象**:[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (開始値 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)]) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]
  
 NONE  
 通常の (標準) 優先度のロックを待機し続けます。  
  
 SELF  
 いずれのアクションも行わずに、現在実行中のオンライン インデックス再構築の DDL 操作を終了します。  
  
 BLOCKERS  
 オンライン インデックス再構築の DDL 操作をブロックしているすべてのユーザー トランザクションを強制終了して、操作を続行できるようにします。 **BLOCKERS** オプションを使用するには、ログインに **ALTER ANY CONNECTION** 権限が必要です。  
 
 RESUME 
 
**適用対象**:[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (開始値 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)]) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]  

手動で一時停止、またはエラーのために一時停止されたインデックス操作を再開します。

MAX_DURATION は **RESUMABLE=ON** と共に使用
 
**適用対象**:[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (開始値 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)]) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]

再開可能なオンラインでのインデックス操作が、再開後に実行される時間 (分単位で指定する整数値) を示します。 その時間が経過すると、再開可能な操作はまだ実行中であっても一時停止されます。

WAIT_AT_LOW_PRIORITY は **RESUMABLE=ON** および **ONLINE = ON** と共に使用。  
  
**適用対象**:[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (開始値 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)]) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 
  
 一時停止の後でオンラインでのインデックス再構築を再開するには、このテーブルに対する操作がブロックされるまで待機する必要があります。 **WAIT_AT_LOW_PRIORITY** は、オンライン インデックス再構築操作が優先度の低いロックを待機して、オンライン インデックス構築操作が待機している間、他の操作を続行可能にすることを示します。 **WAIT AT LOW PRIORITY** オプションを省略すると、WAIT_AT_LOW_PRIORITY (MAX_DURATION = 0 minutes, ABORT_AFTER_WAIT = NONE) と同等になります。 詳細については、「[WAIT_AT_LOW_PRIORITY](alter-index-transact-sql.md)」を参照してください。 

PAUSE
 
**適用対象**:[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (開始値 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)]) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 
  
再開可能なオンラインでのインデックス再構築操作を一時停止します。

ABORT

**適用対象**:[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (開始値 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)]) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]   

再開可能として宣言されている実行中または一時停止中のインデックス操作を中止します。 再開可能なインデックス再構築操作を終了するには、**ABORT** コマンドを明示的に実行する必要があります。 障害が発生しても再開可能なインデックス操作を一時停止しても、操作の実行は終了しません。操作は無期限の一時停止状態のままとなります。
  
## <a name="remarks"></a>解説  
インデックスをパーティションに再分割するか別のファイル グループに移動する場合、ALTER INDEX は使用できません。 このステートメントは、列の追加や削除、または列の順序変更など、インデックス定義の変更には使用できません。 これらの操作を実行するには、CREATE INDEX を DROP_EXISTING 句と共に使用します。  
  
オプションを明示的に指定しない場合は、現在の設定が適用されます。 たとえば、REBUILD 句で FILLFACTOR 設定を指定しなかった場合、再構築処理では、システム カタログに格納されている FILL FACTOR 値が使用されます。 現在のインデックス オプション設定を表示するには、[sys.indexes](../../relational-databases/system-catalog-views/sys-indexes-transact-sql.md) を使用します。  
  
`ONLINE`、`MAXDOP`、`SORT_IN_TEMPDB` の値はシステム カタログに保存されません。 インデックス ステートメントでオプション値を指定しない限り、各オプションの既定値が使用されます。
  
マルチプロセッサ コンピューター上では、`ALTER INDEX REBUILD` は他のクエリと同様、自動的に使用プロセッサの数を増やしてインデックスの変更に関連するスキャンや並べ替え操作を実行します。 `ALTER INDEX REORGANIZE` を実行すると、LOB_COMPACTION の有無に関係なく、**並列処理の最大限度** の値は単一スレッドの操作になります。 詳細については、「 [並列インデックス操作の構成](../../relational-databases/indexes/configure-parallel-index-operations.md)」を参照してください。  
  
> [!IMPORTANT]
> インデックスのあるファイル グループがオフラインであるか、または読み取り専用に設定されている場合、インデックスを再構成または再構築することはできません。 キーワード ALL を指定した場合で、1 つ以上のインデックスがオフラインまたは読み取り専用のファイル グループにある場合、ステートメントは失敗します。  
  
## <a name="rebuilding-indexes"></a><a name="rebuilding-indexes"></a> インデックスの再構築  
インデックスの再構築では、インデックスを削除し再作成します。 この操作では、断片化をなくし、指定されているか既に存在する FILL FACTOR 設定に基づいてページを圧縮することによりディスク領域を取り戻した後、連続するページにインデックス行を再び並べ替えます。 ALL を指定した場合、テーブル上のすべてのインデックスが、1 回のトランザクションで削除され再構築されます。 外部キー制約は、前もって削除しておく必要はありません。 128 以上のエクステントがあるインデックスを再構築すると、[!INCLUDE[ssDE](../../includes/ssde-md.md)]では、トランザクションがコミットされるまで実際のページの割り当て解除とそれに関連するロックが延期されます。  
 
詳細については、「 [インデックスの再編成と再構築](../../relational-databases/indexes/reorganize-and-rebuild-indexes.md)」を参照してください。 
  
## <a name="reorganizing-indexes"></a><a name="reorganizing-indexes"></a> インデックスの再構成
インデックスの再構成では、最小のシステム リソースが使用されます。 この操作では、リーフ レベル ページをリーフ ノードの論理順序 (左から右) に合わせて物理的に並べ替えることにより、テーブルやビュー上にあるクラスター化および非クラスター化インデックスのリーフ レベルをデフラグします。 再構成でも、インデックス ページは圧縮されます。 圧縮は既存の FILL FACTOR 値に基づいて行われます。 
  
`ALL` を指定した場合、テーブル上のクラスター化と非クラスター化の両方のリレーショナル インデックスと XML インデックスが再構成されます。 ALL を指定した場合は、いくつかの制限が適用されます。本記事の「引数」セクションの ALL の定義を参照してください。  
  
詳細については、「 [インデックスの再編成と再構築](../../relational-databases/indexes/reorganize-and-rebuild-indexes.md)」を参照してください。  

> [!IMPORTANT]
> 順序付けされたクラスター化列ストア インデックスを使用しての [!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] テーブルでは、`ALTER INDEX REORGANIZE` がデータを再度並べ替えません。 データを再度並べ替えるには `ALTER INDEX REBUILD` を使用します。
  
## <a name="disabling-indexes"></a><a name="disabling-indexes"></a> インデックスの無効化  
インデックスを無効化すると、ユーザーはインデックスにアクセスできなくなり、クラスター化インデックスの場合は基になるテーブル データにもアクセスできなくなります。 インデックス定義はシステム カタログに残ります。 ビュー上で非クラスター化インデックスまたはクラスター化インデックスを物理的に無効にすると、インデックス データが削除されます。 クラスター化インデックスを無効にすると、データにアクセスできなくなりますが、データはインデックスが削除または再構築されるまで B ツリーに残ります。このデータは管理されません。 有効化または無効化されたインデックスの状態を表示するには、**sys.indexes** カタログ ビューの **is_disabled** 列にクエリを実行します。  
  
テーブルがトランザクション レプリケーション パブリケーション内にある場合、主キー列に関連付けられているインデックスを無効にすることはできません。 これらのインデックスはレプリケーションで必要です。 インデックスを無効にするには、まずパブリケーションからテーブルを削除する必要があります。 詳細については、「[Publish Data and Database Objects](../../relational-databases/replication/publish/publish-data-and-database-objects.md)」(データとデータベース オブジェクトのパブリッシュ) をご覧ください。  
  
インデックスを有効にするには、`ALTER INDEX REBUILD` ステートメントまたは `CREATE INDEX WITH DROP_EXISTING` ステートメントを使用します。 ONLINE オプションが ON に設定されていると、無効化されたクラスター化インデックスを再構築できません。 詳細については、「 [インデックスと制約の無効化](../../relational-databases/indexes/disable-indexes-and-constraints.md)」を参照してください。  
  
## <a name="setting-options"></a>オプションの設定  
指定のインデックスに対して、そのインデックスを再構築したり、再編成したりすることなく、オプション `ALLOW_ROW_LOCKS`、`ALLOW_PAGE_LOCKS`、`OPTIMIZE_FOR_SEQUENTIAL_KEY`、`IGNORE_DUP_KEY`、`STATISTICS_NORECOMPUTE` を設定できます。 変更された値はすぐにインデックスに適用されます。 これらの設定を表示するには、**sys.indexes** を使用します。 詳細については、「 [インデックス オプションの設定](../../relational-databases/indexes/set-index-options.md)」を参照してください。  
  
### <a name="row-and-page-locks-options"></a>行およびページ ロック オプション  
`ALLOW_ROW_LOCKS = ON` と `ALLOW_PAGE_LOCK = ON` の場合、インデックスにアクセスするとき、行レベル、ページ レベル、テーブル レベルのロックが許可されます。 [!INCLUDE[ssDE](../../includes/ssde-md.md)]は適切なロックを選択し、行ロックまたはページ ロックをテーブル ロックにエスカレートすることができます。  
  
`ALLOW_ROW_LOCKS = OFF` と `ALLOW_PAGE_LOCK = OFF` の場合、インデックスにアクセスするとき、テーブル レベルのロックのみが許可されます。  
  
行またはページ ロック オプションが設定されている場合に ALL を指定すると、この設定はすべてのインデックスに適用されます。 基になるテーブルがヒープの場合、この設定は次のように適用されます。  
  
|オプション|詳細|
|------|-------|
|ALLOW_ROW_LOCKS = ON または OFF|ヒープおよび関連する非クラスター化インデックスに適用。|  
|ALLOW_PAGE_LOCKS = ON|ヒープおよび関連する非クラスター化インデックスに適用。|  
|ALLOW_PAGE_LOCKS = OFF|非クラスター化インデックスに完全に適用。 この場合、非クラスター化インデックスではすべてのページ ロックが許可されません。 ヒープでは、ページに対して共有 (S)、更新 (U) および排他 (X) ロックだけが許可されなくなります。 [!INCLUDE[ssDE](../../includes/ssde-md.md)]では内部目的用にインテント ページ ロック (IS、IU、または IX) を引き続き取得できます。|  
  
## <a name="online-index-operations"></a><a name="online-index-operations"></a>オンラインのインデックス操作  
インデックスを再構築する場合で ONLINE オプションが ON に設定されている場合、クエリおよびデータ変更で、基になるオブジェクト、テーブルおよび関連インデックスを使用できます。 1 つのパーティションに存在するインデックスの一部をオンラインで再構築することもできます。 排他テーブル ロックは、変更処理中の非常に短い時間だけ保持されます。  
  
インデックスの再構成は、常にオンラインで実行されます。 この処理ではロックが長期間保持されないので、実行中のクエリや更新はブロックされません。  
  
同じテーブルまたはテーブル パーティションでのオンライン インデックス操作は、次の場合のみ同時に実行できます。  
  
-   複数の非クラスター化インデックスを作成する。  
-   同じテーブルで異なるインデックスを再構成する。  
-   同じテーブルで重複しないインデックスを再構築する間、別のインデックスを再構成する。  
  
その他すべてのオンライン インデックス操作は、同時に実行しようとしても失敗します。 たとえば、同じテーブル上で同時に複数のインデックスを再構築したり、同じテーブルで既存のインデックスを再構築する間に新しいインデックスを作成することはできません。  

### <a name="resumable-index-operations"></a><a name="resumable-indexes"></a>再開可能なインデックス操作

**適用対象**:[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (開始値 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)]) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 

オンライン インデックス再構築は、RESUMABLE = ON オプションを使用し、再開可能として指定できます。 
-  RESUMABLE オプションは特定のインデックス用のメタデータ内に保持されるのではなく、現在の DDL ステートメントの実行中にのみ適用されます。 したがって、再開機能を有効にするには、RESUMABLE = ON 句を明示的に指定する必要があります。
-  MAX_DURATION オプションは、RESUMABLE = ON オプションまたは **low_priority_lock_wait** 引数オプションに対してサポートされています。 
   -  MAX_DURATION for RESUMABLE オプションでは、再構築するインデックスの時間間隔を指定します。 この時間が経過すると、インデックスの再構築が一時停止するか、またはその実行が完了します。 ユーザーは、一時停止されたインデックスの再構築を再開可能にするタイミングを決定します。 MAX_DURATION の **時間** (分単位) は、0 分より長く、かつ 1 週間 (7 \* 24 \* 60 = 10080 分) 以下とする必要があります。 インデックス操作の一時停止時間を長くすると、特定のテーブルでの DML パフォーマンスおよびデータベースのディスク容量に影響を及ぼす可能性があります。元々存在するインデックスも新たに作成されたインデックスもディスク容量を必要とし、DML 操作中に更新される必要があるからです。 MAX_DURATION オプションを省略した場合、インデックス操作は、それが完了するかまたは障害が発生するまで続行されます。 
   -  \<low_priority_lock_wait> 引数オプションを使用すると、SCH-M ロックでブロックされたときにインデックス操作を続行する方法を決定することができます。
 
-  元の ALTER INDEX REBUILD ステートメントを同じパラメーターで再実行すると、一時停止中のインデックス再構築操作が再開されます。 ALTER INDEX RESUME ステートメントを実行して、一時停止中のインデックス再構築操作を再開することもできます。
-  SORT_IN_TEMPDB=ON オプションは、再開可能なインデックスに対してサポートされていません。 
-  RESUMABLE=ON を指定した DDL コマンドを明示的なトランザクション内で実行することはできません (begin tran ... commit ... ブロックの一部にすることはできません)。
-  一時停止されているインデックス操作のみが再開可能です。
-  一時停止されているインデックス操作を再開するときには、MAXDOP 値を新しい値に変更することができます。  一時停止されたインデックス操作を再開するときに MAXDOP を指定しない場合は、前回の MAXDOP 値が使用されます。 インデックス再構築操作に対して MAXDOP オプションをまったく指定していない場合は、既定値が使用されます。
- インデックス操作を直ちに一時停止するには、進行中のコマンドを停止するか (CTRL + C キー)、または ALTER INDEX PAUSE コマンドもしくは KILL *session_id* コマンドを実行します。 コマンドが一時停止されたら、RESUME オプションを使用して再開することができます。
-  ABORT コマンドは、元のインデックス再構築をホストしたセッションを強制終了し、インデックス操作を中止します。  
-  再開可能なインデックス再構築には追加のリソースは必要ありません。ただし、次の場合は例外です。
   -    インデックスが一時停止されている期間など、構築されているインデックスを保持するために追加の領域が必要である。
   -    いかなる DDL 変更も阻止する DDL 状態
-  ゴースト クリーンアップはインデックスの一時停止フェーズ中に実行されますが、インデックスの実行中には一時停止されます。   
再開可能なインデックス再構築操作に対して次の機能は無効になります。
   -    RESUMABLE=ON では、無効になっているインデックスの再構築はサポートされていません。
   -    ALTER INDEX REBUILD ALL コマンド
   -    インデックス再構築を使用する ALTER TABLE  
   -    "RESUMABLE = ON" を指定した DDL コマンドを明示的なトランザクション内で実行することはできません (begin tran ... commit ... ブロックの一部にすることはできません)
   -    キー列として計算列または TIMESTAMP 列を含むインデックスを再構築します。
-   ベース テーブルに 1 つまたは複数の LOB 列が含まれている場合、再開可能なクラスター化インデックスの再構築では、この操作の開始時に Sch-M ロックが必要です。 

> [!NOTE]
> DDL コマンドは、完了するか、一時停止するか、または失敗するまで実行されます。 コマンドが一時停止した場合は、操作が一時停止され、インデックスの作成が完了しなかったことを示すエラーが発行されます。 現在のインデックスの状態の詳細については、[sys.index_resumable_operations](../../relational-databases/system-catalog-views/sys-index-resumable-operations.md) を参照してください。 前と同様に、障害が発生した場合はエラーも発行されます。 

 詳しくは、「 [Perform Index Operations Online](../../relational-databases/indexes/perform-index-operations-online.md)」をご覧ください。  
  
 ### <a name="wait_at_low_priority-with-online-index-operations"></a>オンライン インデックス操作での WAIT_AT_LOW_PRIORITY  
  
 オンライン インデックス再構築の DDL ステートメントを実行するには、特定のテーブルで実行されているすべてのアクティブなブロック トランザクションが完了する必要があります。 オンライン インデックス再構築を実行すると、このテーブルに対する実行の開始が準備できているすべての新しいトランザクションがブロックされます。 オンライン インデックス再構築のロックの期間は非常に短いですが、特定のテーブルに対して開かれているすべてのトランザクションの完了を待機し、新しいトランザクションの開始をブロックすることで、スループットに大きな影響を与える場合があり、ワークロードの速度低下またはタイムアウトを引き起こしたり、基になるテーブルへのアクセスが大幅に制限されたりします。 **WAIT_AT_LOW_PRIORITY** オプションは、オンライン インデックス再構築に必要な S ロックおよび Sch-M ロックを DBA が管理できるようにし、3 つのオプションのいずれかを選択できるようにします。 3 つのいずれのケースでも、待機時間 ((MAX_DURATION = n [minutes])) 中にブロックするアクティビティがない場合は、オンライン インデックス再構築が待機なしで直ちに実行され、DDL ステートメントが完了します。  
  
## <a name="spatial-index-restrictions"></a>空間インデックスに関する制限  
 空間インデックスを再構築するとき、基になるユーザー テーブルはインデックス操作の間使用できなくなります。これは、空間インデックスがスキーマ ロックを保持するためです。  
  
 ユーザー テーブルの PRIMARY KEY 制約は、そのテーブルの列に空間インデックスが定義されているときは変更できません。 PRIMARY KEY 制約を変更する場合は、初めにテーブルのすべての空間インデックスを削除してください。 PRIMARY KEY 制約を変更した後、各空間インデックスを再作成できます。  
  
 単一のパーティションの再構築操作では、空間インデックスを指定できません。 ただし、パーティションの完全な再構築では、空間インデックスを指定できます。  
  
 BOUNDING_BOX や GRID など、空間インデックス固有のオプションを変更するには、DROP_EXISTING = ON を指定する CREATE SPATIAL INDEX ステートメントを使用するか、空間インデックスを削除して新しく作成します。 例については、「 [CREATE SPATIAL INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/create-spatial-index-transact-sql.md)」の「解説」をご覧ください。  
  
## <a name="data-compression"></a>データ圧縮  
 データ圧縮の詳細については、「 [データの圧縮](../../relational-databases/data-compression/data-compression.md)」を参照してください。  
  
 圧縮状態の変更 (PAGE および ROW) による、テーブル、インデックス、またはパーティションへの影響を評価するには、[sp_estimate_data_compression_savings](../../relational-databases/system-stored-procedures/sp-estimate-data-compression-savings-transact-sql.md) ストアド プロシージャを使用します。  
  
パーティション インデックスには次の制限が適用されます。  
  
-   ALTER INDEX ALL ... を使用しても、固定されていないインデックスがテーブルにあると、そのパーティションの圧縮の設定を変更できません。  
-   ALTER INDEX \<index> ...REBUILD PARTITION ... 構文は、そのインデックスの指定のパーティションを再構築します。  
-   ALTER INDEX \<index> ...REBUILD WITH ... 構文は、そのインデックスのすべてのパーティションを再構築します。  
  
## <a name="statistics"></a>統計  
 テーブルで **ALTER INDEX ALL ...** を実行すると、インデックスに関連付けられた統計のみが更新されます。 (インデックスではなく) テーブルで作成されている自動または手動の統計は更新されません。  
  
## <a name="permissions"></a>アクセス許可  
 ALTER INDEX を実行するには、少なくとも、テーブルまたはビューの ALTER 権限が必要です。  
  
## <a name="version-notes"></a>バージョンに関するメモ  
  
-  [!INCLUDE[ssSDS](../../includes/sssds-md.md)] で、filegroup オプションおよび filestream オプションは使用されません。  
-  [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] より前のバージョンで列ストア インデックスを使用することはできません。 
-  再開可能なインデックス操作は、[!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 以降のバージョンで使用できます   
  
## <a name="basic-syntax-example"></a>基本構文例:   
  
```sql 
ALTER INDEX index1 ON table1 REBUILD;  
  
ALTER INDEX ALL ON table1 REBUILD;  
  
ALTER INDEX ALL ON dbo.table1 REBUILD;  
```

## <a name="examples-columnstore-indexes"></a>例 :列ストア インデックス  
 これらの例は、列ストア インデックスに適用されます。  
  
### <a name="a-reorganize-demo"></a>A. REORGANIZE のデモ  
 この例では、ALTER INDEX REORGANIZE コマンドの動作方法を示します。  複数の行グループを含むテーブルを作成し、REORGANIZE で行グループを結合する方法を示します。  
  
```sql  
-- Create a database   
CREATE DATABASE [ columnstore ];  
GO  
  
-- Create a rowstore staging table  
CREATE TABLE [ staging ] (  
     AccountKey              int NOT NULL,  
     AccountDescription      nvarchar (50),  
     AccountType             nvarchar(50),  
     AccountCodeAlternateKey     int  
     )  
  
-- Insert 10 million rows into the staging table.   
DECLARE @loop int  
DECLARE @AccountDescription varchar(50)  
DECLARE @AccountKey int  
DECLARE @AccountType varchar(50)  
DECLARE @AccountCode int  
  
SELECT @loop = 0  
BEGIN TRAN  
    WHILE (@loop < 300000)   
      BEGIN  
        SELECT @AccountKey = CAST (RAND()*10000000 as int);  
        SELECT @AccountDescription = 'accountdesc ' + CONVERT(varchar(20), @AccountKey);  
        SELECT @AccountType = 'AccountType ' + CONVERT(varchar(20), @AccountKey);  
        SELECT @AccountCode =  CAST (RAND()*10000000 as int);  
  
        INSERT INTO  staging VALUES (@AccountKey, @AccountDescription, @AccountType, @AccountCode);  
  
        SELECT @loop = @loop + 1;  
    END  
COMMIT  
  
-- Create a table for the clustered columnstore index  
  
CREATE TABLE cci_target (  
     AccountKey              int NOT NULL,  
     AccountDescription      nvarchar (50),  
     AccountType             nvarchar(50),  
     AccountCodeAlternateKey int  
     )  
  
-- Convert the table to a clustered columnstore index named inxcci_cci_target;  
CREATE CLUSTERED COLUMNSTORE INDEX idxcci_cci_target ON cci_target;  
```  
  
 TABLOCK オプションを使用して、行を並列に挿入します。 [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 以降のバージョンでは、TABLOCK を使用すると、INSERT INTO 操作を並列に実行できます。  
  
```sql  
INSERT INTO cci_target WITH (TABLOCK) 
SELECT TOP 300000 * FROM staging;  
```  
  
 OPEN デルタ行グループを確認するには、次のコマンドを実行します。 行グループの数は、並列処理の次数によって決まります。  
  
```sql  
SELECT *   
FROM sys.dm_db_column_store_row_group_physical_stats   
WHERE object_id  = object_id('cci_target');  
```  
  
 CLOSED 行グループと OPEN 行グループをすべて列ストアに強制的に移動するには、次のコマンドを実行します。  
  
```sql  
ALTER INDEX idxcci_cci_target ON cci_target REORGANIZE WITH (COMPRESS_ALL_ROW_GROUPS = ON);  
```  
  
 次のコマンドを再度実行すると、小さな行グループが 1 つの圧縮された行グループにマージされるのを確認できます。  
  
```sql  
ALTER INDEX idxcci_cci_target ON cci_target REORGANIZE WITH (COMPRESS_ALL_ROW_GROUPS = ON);  
```  
  
### <a name="b-compress-closed-delta-rowgroups-into-the-columnstore"></a>B. CLOSED デルタ行グループを列ストアに圧縮する  
 この例では、REORGANIZE オプションを使用して、各 CLOSED デルタ行グループを圧縮し、圧縮された行グループとして列ストアに移動します。   これは必須ではありませんが、組ムーバーによる CLOSED 行グループの圧縮の速度が十分でない場合に使用すると便利です。  
  
```sql  
-- Uses AdventureWorksDW  
-- REORGANIZE all partitions  
ALTER INDEX cci_FactInternetSales2 ON FactInternetSales2 REORGANIZE;  
  
-- REORGANIZE a specific partition  
ALTER INDEX cci_FactInternetSales2 ON FactInternetSales2 REORGANIZE PARTITION = 0;  
```  
  
### <a name="c-compress-all-open-and-closed-delta-rowgroups-into-the-columnstore"></a>C. すべての OPEN デルタ行グループおよび CLOSED デルタ行グループを列ストアに圧縮する  
 **適用対象:** [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (開始値 [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)]) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 
  
 コマンド REORGANIZE WITH ( COMPRESS_ALL_ROW_GROUPS = ON ) では、OPEN および CLOSED のデルタ行グループをそれぞれ圧縮し、圧縮された行グループとして列ストアに移動します。 これにより、デルタストアは空になり、すべての行が強制的に列ストアに圧縮されます。 これは多くの挿入操作を実行した後で特に便利です。挿入操作を多く実行すると行が 1 つまたは複数のデルタ行グループに格納されるからです。  
  
 REORGANIZE では、行グループを結合して、行グループに最大で \<= 1,024,576 個の行を含めることができます。 このため、OPEN および CLOSED の行グループをすべて圧縮する場合は、行が少ししか含まれていない多くの行グループをそれぞれ個別に圧縮する必要がなくなります。 行グループにできるだけ多くの行を詰め込むことで、圧縮サイズを縮小し、クエリのパフォーマンスを向上させることができます。  
  
```sql  
-- Uses AdventureWorksDW2016  
-- Move all OPEN and CLOSED delta rowgroups into the columnstore.  
ALTER INDEX cci_FactInternetSales2 ON FactInternetSales2 REORGANIZE WITH (COMPRESS_ALL_ROW_GROUPS = ON);  
  
-- For a specific partition, move all OPEN AND CLOSED delta rowgroups into the columnstore  
ALTER INDEX cci_FactInternetSales2 ON FactInternetSales2 REORGANIZE PARTITION = 0 WITH (COMPRESS_ALL_ROW_GROUPS = ON);  
```  
  
### <a name="d-defragment-a-columnstore-index-online"></a>D. オンラインで列ストア インデックスを最適化する  
 適用対象: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] および [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)]。  
  
 [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 以降のバージョンでは、REORGANIZE によってより多くのデルタ行グループが列ストアに圧縮されます。 また、オンラインでの最適化も行われます。 まず、行グループ内にある行の 10% 以上が削除されると、削除された行を物理的に削除することで、列ストアのサイズを縮小します。  次に、複数の行グループを結合して、より大きな行グループを形成します。行グループあたり最大で 1,024,576 行を含めることができます。  変更された行グループはすべて再度圧縮されます。  
  
> [!NOTE]
> [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 以降のバージョンでは、ほとんどの状況で列ストア インデックスを再構築する必要はなくなりました。REORGANIZE によって行が物理的に削除され、行グループがマージされるからです。 COMPRESS_ALL_ROW_GROUPS オプションを指定すると、OPEN または CLOSED の行グループがすべて列ストアに強制的に圧縮されます。以前、この処理は再構築によってのみ可能でした。 REORGANIZE はオンラインで動作し、バックグラウンドで実行されます。このため、操作が発生してもクエリを続行することができます。  
  
```sql  
-- Uses AdventureWorks  
-- Defragment by physically removing rows that have been logically deleted from the table, and merging rowgroups.  
ALTER INDEX cci_FactInternetSales2 ON FactInternetSales2 REORGANIZE;  
```  
  
### <a name="e-rebuild-a-clustered-columnstore-index-offline"></a>E. クラスター化列ストア インデックスをオフラインで再構築する  
適用対象:[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (開始値 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)])   
  
> [!TIP]
> [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 以降、および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] では、ALTER INDEX REBUILD ではなく ALTER INDEX REORGANIZE を使用することをお勧めします。  
  
> [!NOTE]
> [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] および [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] において、REORGANIZE は CLOSED 行グループを列ストアに圧縮するためにのみ使用されます。 最適化操作を実行し、すべてのデルタ行グループを列ストアに強制的に移動する唯一の方法は、インデックスを再構築することです。  
  
 この例では、クラスター化列ストア インデックスを再構築し、すべてのデルタ行グループを列ストアに強制的に移動する方法を示します。 この最初の手順では、クラスター化列ストア インデックスを含む FactInternetSales2 テーブルを準備し、最初の 4 つの列にデータを挿入します。  
  
```sql  
-- Uses AdventureWorksDW  
  
CREATE TABLE dbo.FactInternetSales2 (  
    ProductKey [int] NOT NULL,   
    OrderDateKey [int] NOT NULL,   
    DueDateKey [int] NOT NULL,   
    ShipDateKey [int] NOT NULL);  
  
CREATE CLUSTERED COLUMNSTORE INDEX cci_FactInternetSales2  
ON dbo.FactInternetSales2;  
  
INSERT INTO dbo.FactInternetSales2  
SELECT ProductKey, OrderDateKey, DueDateKey, ShipDateKey  
FROM dbo.FactInternetSales;  
  
SELECT * FROM sys.column_store_row_groups;  
```  
  
 その結果は、OPEN 行グループが 1 つあることを示しています。これは、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]が、行グループを閉じてデータを列ストアに移動する前に、より多くの行が追加されるのを待機することを意味します。 次のステートメントではクラスター化列ストア インデックスを再構築します。これにより、すべての行が列ストアに強制的に移動されます。  
  
```sql  
ALTER INDEX cci_FactInternetSales2 ON FactInternetSales2 REBUILD;  
SELECT * FROM sys.column_store_row_groups;  
```  
  
 SELECT ステートメントの結果は行グループが圧縮されていることを示しています。つまり、行グループの列セグメントは圧縮され、列ストアに格納されます。  
  
### <a name="f-rebuild-a-partition-of-a-clustered-columnstore-index-offline"></a>F. クラスター化列ストア インデックスのパーティションをオフラインで再構築する  
 **適用対象**:[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降)  
 
 大きなクラスター化列ストア インデックスのパーティションを再構築するには、ALTER INDEX REBUILD とパーティション オプションを一緒に使用します。 この例では、パーティション 12 を再構築します。 [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 以降では、REBUILD を REORGANIZE に置き換えることをお勧めします。  
  
```sql  
ALTER INDEX cci_fact3   
ON fact3  
REBUILD PARTITION = 12;  
```  
  
### <a name="g-change-a-clustered-columstore-index-to-use-archival-compression"></a>G. アーカイブ用圧縮を使用するようにクラスター化列ストア インデックスを変更する  
 適用対象外: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)]  
  
 COLUMNSTORE_ARCHIVE データ圧縮オプションを使用することにより、クラスター化列ストア インデックスのサイズをさらに縮小することができます。 これは、以前のデータをより安価なストレージに保持したい場合に実用的な方法です。 この方法は、通常の COLUMNSTORE 圧縮の場合と比べて圧縮解除に時間がかかるため、それほど頻繁にアクセスしないデータに対してのみ使用することをお勧めします。  
  
 次の例では、保存用圧縮を使用するクラスター化列ストア インデックスを再構築し、次に保管用圧縮を削除する方法を示します。 最終結果では、列ストア圧縮のみを使用します。  
  
```sql  
--Prepare the example by creating a table with a clustered columnstore index.  
CREATE TABLE SimpleTable (  
    ProductKey [int] NOT NULL,   
    OrderDateKey [int] NOT NULL,   
    DueDateKey [int] NOT NULL,   
    ShipDateKey [int] NOT NULL  
);  
  
CREATE CLUSTERED INDEX cci_SimpleTable ON SimpleTable (ProductKey);  
  
CREATE CLUSTERED COLUMNSTORE INDEX cci_SimpleTable  
ON SimpleTable  
WITH (DROP_EXISTING = ON);  
  
--Compress the table further by using archival compression.  
ALTER INDEX cci_SimpleTable ON SimpleTable  
REBUILD  
WITH (DATA_COMPRESSION = COLUMNSTORE_ARCHIVE);  
  
--Remove the archive compression and only use columnstore compression.  
ALTER INDEX cci_SimpleTable ON SimpleTable  
REBUILD  
WITH (DATA_COMPRESSION = COLUMNSTORE);  
GO  
```  
  
## <a name="examples-rowstore-indexes"></a>例 :行ストア インデックス  
  
### <a name="a-rebuilding-an-index"></a>A. インデックスを再構築する  
 次の例では、[!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] データベースにある `Employee` テーブルで単一のインデックスを再構築します。  
  
```sql  
ALTER INDEX PK_Employee_EmployeeID ON HumanResources.Employee REBUILD;  
```  
  
### <a name="b-rebuilding-all-indexes-on-a-table-and-specifying-options"></a>B. テーブルですべてのインデックスを再構築し、オプションを指定する  
 次の例では、キーワード ALL を指定します。 これにより、[!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] データベースの Production.Product テーブルに関連付けられたすべてのインデックスが再構築されます。 3 つのオプションが指定されます。  
  
**適用対象**:[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (開始値 [!INCLUDE[ssKatmai](../../includes/ssKatmai-md.md)]) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]  
  
```sql  
ALTER INDEX ALL ON Production.Product  
REBUILD WITH (FILLFACTOR = 80, SORT_IN_TEMPDB = ON, STATISTICS_NORECOMPUTE = ON);  
```  
  
次の例は、優先度の低いロック オプションを含めて ONLINE オプションを追加し、行の圧縮オプションを追加します。  
  
**適用対象**:[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (開始値 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)]) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]  
  
```sql  
ALTER INDEX ALL ON Production.Product  
REBUILD WITH   
(  
    FILLFACTOR = 80,   
    SORT_IN_TEMPDB = ON,  
    STATISTICS_NORECOMPUTE = ON,  
    ONLINE = ON ( WAIT_AT_LOW_PRIORITY ( MAX_DURATION = 4 MINUTES, ABORT_AFTER_WAIT = BLOCKERS ) ),   
    DATA_COMPRESSION = ROW  
);  
```  
  
### <a name="c-reorganizing-an-index-with-lob-compaction"></a>C. LOB 圧縮を行いインデックスを再構成する  
 次の例では、[!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] データベースの単一のクラスター化インデックスを再構成します。 インデックスではリーフ レベルに LOB データ型が含まれるので、このステートメントではラージ オブジェクト データを含むすべてのページが圧縮されます。 既定値が ON であるため、WITH (LOB_COMPACTION = ON) オプションの指定は必須ではありません。  
  
```sql  
ALTER INDEX PK_ProductPhoto_ProductPhotoID ON Production.ProductPhoto REORGANIZE WITH (LOB_COMPACTION = ON);  
```  
  
### <a name="d-setting-options-on-an-index"></a>D. インデックスにオプションを設定する  
 次の例では、[!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] データベースのインデックス `AK_SalesOrderHeader_SalesOrderNumber` にいくつかのオプションを設定します。  
  
**適用対象**:[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (開始値 [!INCLUDE[ssKatmai](../../includes/ssKatmai-md.md)]) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]  
  
```sql  
ALTER INDEX AK_SalesOrderHeader_SalesOrderNumber ON  
    Sales.SalesOrderHeader  
SET (  
    STATISTICS_NORECOMPUTE = ON,  
    IGNORE_DUP_KEY = ON,  
    ALLOW_PAGE_LOCKS = ON  
    ) ;  
GO
```  
  
### <a name="e-disabling-an-index"></a>E. インデックスを無効にする  
 次の例では、[!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] データベースにある `Employee` テーブルで非クラスター化インデックスを無効にします。  
  
```sql  
ALTER INDEX IX_Employee_ManagerID ON HumanResources.Employee DISABLE;
```  
  
### <a name="f-disabling-constraints"></a>F. 制約を無効化する  
 次の例では、[!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] データベースの PRIMARY KEY インデックスを無効にすることにより PRIMARY KEY 制約を無効にします。 基になるテーブルに対する FOREIGN KEY 制約は自動的に無効になり、警告メッセージが表示されます。  
  
```sql  
ALTER INDEX PK_Department_DepartmentID ON HumanResources.Department DISABLE;  
```  
  
結果セットでは、次の警告メッセージが返されます。  
  
```  
Warning: Foreign key 'FK_EmployeeDepartmentHistory_Department_DepartmentID'  
on table 'EmployeeDepartmentHistory' referencing table 'Department'  
was disabled as a result of disabling the index 'PK_Department_DepartmentID'.
```  
  
### <a name="g-enabling-constraints"></a>G. 制約を有効にする  
 次の例では、例 F で無効にした PRIMARY KEY および FOREIGN KEY 制約を有効にします。  
  
PRIMARY KEY 制約は、PRIMARY KEY インデックスを再構築することにより有効にできます。  
  
```sql  
ALTER INDEX PK_Department_DepartmentID ON HumanResources.Department REBUILD;  
```  
  
次に FOREIGN KEY 制約を有効にします。  
  
```sql  
ALTER TABLE HumanResources.EmployeeDepartmentHistory  
CHECK CONSTRAINT FK_EmployeeDepartmentHistory_Department_DepartmentID;  
GO  
```  
  
### <a name="h-rebuilding-a-partitioned-index"></a>H. パーティション インデックスを再構築する  
 次の例では、[!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] データベースのパーティション インデックス `5` のパーティション番号 `IX_TransactionHistory_TransactionDate` の単一パーティションを再構築します。 パーティション 5 はオンラインで再構築され、優先度の低いロックの 10 分間の待機時間が、インデックスの再構築操作によって取得された各ロックに個別に適用されます。 この期間中に、インデックスの再構築を完了するためのロックを取得できない場合は、再構築操作のステートメントが中止されます。  
  
**適用対象**:[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (開始値 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)]) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]  
  
```sql  
-- Verify the partitioned indexes.  
SELECT *  
FROM sys.dm_db_index_physical_stats (DB_ID(),OBJECT_ID(N'Production.TransactionHistory'), NULL , NULL, NULL);  
GO  
--Rebuild only partition 5.  
ALTER INDEX IX_TransactionHistory_TransactionDate  
ON Production.TransactionHistory  
REBUILD Partition = 5   
   WITH (ONLINE = ON (WAIT_AT_LOW_PRIORITY (MAX_DURATION = 10 minutes, ABORT_AFTER_WAIT = SELF)));  
GO  
```  
  
### <a name="i-changing-the-compression-setting-of-an-index"></a>I. インデックスの圧縮設定を変更する  
 次の例では、非パーティション行ストア テーブルのインデックスを再構築します。  
  
```sql
ALTER INDEX IX_INDEX1   
ON T1  
REBUILD   
WITH (DATA_COMPRESSION = PAGE);  
GO  
```  
  
その他のデータ圧縮の例については、「[データ圧縮](../../relational-databases/data-compression/data-compression.md)」を参照してください。  
 
### <a name="j-online-resumable-index-rebuild"></a>J. オンラインでの再開可能なインデックス再構築

**適用対象**:[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (開始値 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)]) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]   

 次の例では、オンラインでの再開可能なインデックス再構築を使用する方法を示します。 

1. MAXDOP=1 を使用して再開可能な操作としてオンラインでのインデックス再構築を実行します。

   ```sql
   ALTER INDEX test_idx on test_table REBUILD WITH (ONLINE=ON, MAXDOP=1, RESUMABLE=ON) ;
   ```

2. インデックス操作が一時停止された後に同じコマンドを再度実行し (上記参照)、インデックス再構築操作を自動的に再開します。

3. MAX_DURATION を 240 分に設定して、オンラインでのインデックス再構築を再開可能な操作として実行します。

   ```sql
   ALTER INDEX test_idx on test_table REBUILD WITH (ONLINE=ON, RESUMABLE=ON, MAX_DURATION=240) ; 
   ```
4. オンラインでの再開可能なインデックス再構築の実行を一時停止します。

   ```sql
   ALTER INDEX test_idx on test_table PAUSE ;
   ```   
5. 再開可能な操作として実行したインデックス再構築について、MAXDOP の新しい値を 4 に指定して、オンラインでのインデックス再構築を再開します。

   ```sql
   ALTER INDEX test_idx on test_table RESUME WITH (MAXDOP=4) ;
   ```
6. 再開可能として実行されたオンラインでのインデックス再構築について、オンラインでのインデックス再構築操作を再開します。 MAXDOP を 2 に設定し、再開可能として実行されるインデックスの実行時間を 240 分に設定します。ロックでインデックスがブロックされる場合は、10 分間待機し、その後すべてのブロックを強制終了します。 

   ```sql
      ALTER INDEX test_idx on test_table  
         RESUME WITH (MAXDOP=2, MAX_DURATION= 240 MINUTES, 
         WAIT_AT_LOW_PRIORITY (MAX_DURATION=10, ABORT_AFTER_WAIT=BLOCKERS)) ;
   ```      
7. 実行中または一時停止中である再開可能なインデックス再構築操作を中止します。

   ```sql
   ALTER INDEX test_idx on test_table ABORT ;
   ``` 
  
## <a name="see-also"></a>参照  
[SQL Server のインデックスのアーキテクチャとデザイン ガイド](../../relational-databases/sql-server-index-design-guide.md)     
[オンラインでのインデックス操作の実行](../../relational-databases/indexes/perform-index-operations-online.md)    
[CREATE INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/create-index-transact-sql.md)     
[CREATE SPATIAL INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/create-spatial-index-transact-sql.md)   
[CREATE XML INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/create-xml-index-transact-sql.md)   
[DROP INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/drop-index-transact-sql.md)   
[インデックスと制約の無効化](../../relational-databases/indexes/disable-indexes-and-constraints.md)   
[XML インデックス &#40;SQL Server&#41;](../../relational-databases/xml/xml-indexes-sql-server.md)   
[インデックスの再構成と再構築](../../relational-databases/indexes/reorganize-and-rebuild-indexes.md)   
[sys.dm_db_index_physical_stats &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-index-physical-stats-transact-sql.md)   
[EVENTDATA &#40;Transact-SQL&#41;](../../t-sql/functions/eventdata-transact-sql.md)    
