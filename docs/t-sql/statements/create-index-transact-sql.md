---
description: CREATE INDEX (Transact-SQL)
title: CREATE INDEX (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/17/2020
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- CREATE INDEX
- INDEX
- INDEX_TSQL
- CREATE_INDEX_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- CREATE XML INDEX statement
- PRIMARY XML INDEX statement
- indexes [SQL Server], creating
- online index operations
- index keys [SQL Server]
- PROPERTY INDEX statement
- computed columns, index creation
- clustered indexes, creating
- indexed views [SQL Server], index creation
- partitioned indexes [SQL Server], creating
- VALUE INDEX statement
- index creation [SQL Server], CREATE INDEX statement
- DROP_EXISTING clause
- row locks [SQL Server]
- composite indexes
- MAXDOP index option, CREATE INDEX statement
- filtered indexes [SQL Server], creating
- PATH INDEX statement
- locking [SQL Server], indexes
- unique indexes, creating
- primary indexes [SQL Server]
- CREATE PRIMARY XML INDEX statement
- SET statement, index creation
- index options [SQL Server]
- included columns
- ONLINE option
- nonclustered indexes [SQL Server], creating
- CREATE INDEX statement
- IGNORE_DUP_KEY option
- deterministic functions
- keys [SQL Server], index
- SECONDARY XML INDEX statement
- page locks [SQL Server]
- secondary indexes [SQL Server]
- XML indexes [SQL Server], creating
ms.assetid: d2297805-412b-47b5-aeeb-53388349a5b9
author: pmasl
ms.author: pelopes
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: c66d38bafd8d011e8630e865359fcc781dab01c6
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104734410"
---
# <a name="create-index-transact-sql"></a>CREATE INDEX (Transact-SQL)

[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

テーブルまたはビューにリレーショナル インデックスを作成します。 クラスター化または非クラスター化 B ツリー インデックスであるため、行ストア インデックスとも呼ばれます。 行ストア インデックスは、テーブルにデータが設定される前に作成することができます。 クエリが特定の列から選択するか、特定の順序での値の並べ替えを要求する場合は特に、クエリのパフォーマンスを向上させるために行ストア インデックスを使用します。

> [!NOTE]
> [!INCLUDE[ssSDW](../../includes/sssdw-md.md)] および [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] は、現在一意制約をサポートしていません。 一意制約を参照している例は、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] と [!INCLUDE[ssSDS](../../includes/sssds-md.md)] にのみ適用されます。
> [!TIP]
> インデックスの設計のガイドラインについては、「[SQL Server インデックス デザイン ガイド](../../relational-databases/sql-server-index-design-guide.md)」をご覧ください。

 **簡単な例:**

```sql
-- Create a nonclustered index on a table or view
CREATE INDEX i1 ON t1 (col1);

-- Create a clustered index on a table and use a 3-part name for the table
CREATE CLUSTERED INDEX i1 ON d1.s1.t1 (col1);

-- Syntax for SQL Server and Azure SQL Database
-- Create a nonclustered index with a unique constraint
-- on 3 columns and specify the sort order for each column
CREATE UNIQUE INDEX i1 ON t1 (col1 DESC, col2 ASC, col3 DESC);
```

**重要なシナリオ:**

[!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] および [!INCLUDE[ssSDS](../../includes/sssds-md.md)] 以降では、列ストア インデックスに非クラスター化インデックスを使用して、データ ウェアハウスのクエリのパフォーマンスを向上させます。 詳しくは、「[列ストア インデックス - データ ウェアハウス](../../relational-databases/indexes/columnstore-indexes-data-warehouse.md)」をご覧ください。

その他の種類のインデックスについては、以下を参照してください。

- [CREATE XML INDEX](../../t-sql/statements/create-xml-index-transact-sql.md)
- [CREATE SPATIAL INDEX](../../t-sql/statements/create-spatial-index-transact-sql.md)
- [CREATE COLUMNSTORE INDEX](../../t-sql/statements/create-columnstore-index-transact-sql.md)

![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)

## <a name="syntax"></a>構文

### <a name="syntax-for-sql-server-and-azure-sql-database"></a>SQL Server と Azure SQL Database の構文

```syntaxsql
CREATE [ UNIQUE ] [ CLUSTERED | NONCLUSTERED ] INDEX index_name
    ON <object> ( column [ ASC | DESC ] [ ,...n ] )
    [ INCLUDE ( column_name [ ,...n ] ) ]
    [ WHERE <filter_predicate> ]
    [ WITH ( <relational_index_option> [ ,...n ] ) ]
    [ ON { partition_scheme_name ( column_name )
         | filegroup_name
         | default
         }
    ]
    [ FILESTREAM_ON { filestream_filegroup_name | partition_scheme_name | "NULL" } ]
  
[ ; ]
  
<object> ::=
{ database_name.schema_name.table_or_view_name | schema_name.table_or_view_name | table_or_view_name }

<relational_index_option> ::=
{
    PAD_INDEX = { ON | OFF }
  | FILLFACTOR = fillfactor
  | SORT_IN_TEMPDB = { ON | OFF }
  | IGNORE_DUP_KEY = { ON | OFF }
  | STATISTICS_NORECOMPUTE = { ON | OFF }
  | STATISTICS_INCREMENTAL = { ON | OFF }
  | DROP_EXISTING = { ON | OFF }
  | ONLINE = { ON | OFF }
  | RESUMABLE = { ON | OFF }
  | MAX_DURATION = <time> [MINUTES]
  | ALLOW_ROW_LOCKS = { ON | OFF }
  | ALLOW_PAGE_LOCKS = { ON | OFF }
  | OPTIMIZE_FOR_SEQUENTIAL_KEY = { ON | OFF }
  | MAXDOP = max_degree_of_parallelism
  | DATA_COMPRESSION = { NONE | ROW | PAGE }
     [ ON PARTITIONS ( { <partition_number_expression> | <range> }
     [ , ...n ] ) ]
}

<filter_predicate> ::=
    <conjunct> [ AND <conjunct> ]

<conjunct> ::=
    <disjunct> | <comparison>

<disjunct> ::=
        column_name IN (constant ,...n)

<comparison> ::=
        column_name <comparison_op> constant

<comparison_op> ::=
    { IS | IS NOT | = | <> | != | > | >= | !> | < | <= | !< }

<range> ::=
<partition_number_expression> TO <partition_number_expression>
```

### <a name="backward-compatible-relational-index"></a>下位互換性のあるリレーショナル インデックス

> [!IMPORTANT]
> 下位互換性のあるリレーショナル インデックスの構文構造は、今後のバージョンの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では削除される予定です。
> 新しい開発作業ではこの構文構造の使用を避け、現在この構造を使用しているアプリケーションの変更を検討してください。
> 代わりに <relational_index_option> に指定されている構文構造を使用します。

```syntaxsql
CREATE [ UNIQUE ] [ CLUSTERED | NONCLUSTERED ] INDEX index_name
    ON <object> ( column_name [ ASC | DESC ] [ ,...n ] )
    [ WITH <backward_compatible_index_option> [ ,...n ] ]
    [ ON { filegroup_name | "default" } ]

<object> ::=
{
    [ database_name. [ owner_name ] . | owner_name. ]
    table_or_view_name
}

<backward_compatible_index_option> ::=
{
    PAD_INDEX
  | FILLFACTOR = fillfactor
  | SORT_IN_TEMPDB
  | IGNORE_DUP_KEY
  | STATISTICS_NORECOMPUTE
  | DROP_EXISTING
}
```

### <a name="syntax-for-azure-synapse-analytics-and-parallel-data-warehouse"></a>Azure Synapse Analytics と Parallel Data Warehouse の構文

```syntaxsql

CREATE CLUSTERED COLUMNSTORE INDEX index_name
    ON [ database_name . [ schema ] . | schema . ] table_name
    [ORDER (column[,...n])]
    [WITH ( DROP_EXISTING = { ON | OFF } )]
[;]


CREATE [ CLUSTERED | NONCLUSTERED ] INDEX index_name
    ON [ database_name . [ schema ] . | schema . ] table_name
        ( { column [ ASC | DESC ] } [ ,...n ] )
    WITH ( DROP_EXISTING = { ON | OFF } )
[;]


```

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数

UNIQUE      
テーブルまたはビューに一意のインデックスを作成します。 一意のインデックスとは、どの 2 つの行にも同じインデックス キー値が設定されていないインデックスです。 ビューのクラスター化インデックスは一意である必要があります。

[!INCLUDE[ssDE](../../includes/ssde-md.md)]では、IGNORE_DUP_KEY が ON に設定されているかどうかに関係なく、重複する値が既に含まれている列に対して一意のインデックスを作成できません。 作成しようとすると、[!INCLUDE[ssDE](../../includes/ssde-md.md)]ではエラー メッセージが表示されます。 1 行または複数行に対して一意のインデックスを作成するには、先に重複する値を削除しておく必要があります。 一意のインデックスに使用する列は NOT NULL に設定してください。一意のインデックスを作成するとき、複数の NULL 値は重複した値と見なされます。

CLUSTERED      
キー値の論理的順序がテーブル内にある対応する行の物理的な順序を決めるインデックスを作成します。 クラスター化インデックスの最下位レベル (リーフ レベル) には、テーブルの実際のデータ行が含まれます。 1 つのテーブルまたはビューに、同時に複数のクラスター化インデックスを定義することはできません。

一意のクラスター化インデックスが定義されているビューは、インデックス付きビューと呼ばれます。 ビューに一意のクラスター化インデックスを作成すると、ビューを物理的に具体化することになります。 ビューにその他のインデックスを定義するには、まずそのビューに一意のクラスター化インデックスを作成する必要があります。 詳細については、「 [インデックス付きビューの作成](../../relational-databases/views/create-indexed-views.md)」を参照してください。

非クラスター化インデックスを作成する前に、クラスター化インデックスを作成します。 これは、クラスター化インデックスを作成すると、テーブルの既存の非クラスター化インデックスが再構築されるためです。

`CLUSTERED` を指定しない場合、非クラスター化インデックスが作成されます。

> [!NOTE]
> クラスター化インデックスおよびデータ ページのリーフ レベルは、定義では同一であるため、クラスター化インデックスを作成して、ON を使用して *partition_scheme_name* または ON *filegroup_name* 句に効率的に移動、テーブル、テーブルが作成されたファイル グループから新しいパーティション構成またはファイル グループにします。 特定のファイル グループ上にテーブルまたはインデックスを作成する前に、使用可能なファイル グループとインデックス用の十分な空領域を確認しておいてください。

場合によっては、クラスター化インデックスを作成すると、以前に無効化されたインデックスが有効になることがあります。 詳細については、次を参照してください。 [を有効にするインデックスと制約](../../relational-databases/indexes/enable-indexes-and-constraints.md) と [を無効にするインデックスと制約](../../relational-databases/indexes/disable-indexes-and-constraints.md)です。

NONCLUSTERED      
テーブルの論理順序を示すインデックスを作成します。 非クラスター化インデックスの場合、データ行の物理的な順序は、そのインデックスが作成された順序とは関係ありません。

インデックスの作成方法に関係なく、PRIMARY KEY および UNIQUE 制約で暗黙的に作成する場合も、CREATE INDEX で明示的に作成する場合も、各テーブルには 999 個までの非クラスター化インデックスを作成できます。

インデックス付きビューの場合は、既に一意のクラスター化インデックスが作成されているビューにのみ、非クラスター化インデックスを作成できます。

特に指定しない場合、既定のインデックスの種類は NONCLUSTERED です。

*index_name*      
 インデックスの名前です。 インデックス名は、テーブルまたはビュー内では一意である必要がありますが、データベース内で一意である必要はありません。 インデックス名は、[識別子](../../relational-databases/databases/database-identifiers.md)の規則に従っている必要があります。

*column*      
 インデックスの基準となる 1 列または複数列を指定します。 指定した列を組み合わせた値で複合インデックスを作成するには、2 つ以上の列名を指定します。 複合インデックスに含まれる列は、*table_or_view_name* の後のかっこ内に、並べ替えの優先順序に従って指定します。

1 つの複合インデックス キーには、最大 32 の列を結合できます。 複合インデックス キーに含まれる列はすべて、同じテーブルまたはビュー内に存在する必要があります。 複合インデックスの値の最大許容サイズは、クラスター化インデックスの場合は、900 バイトまたは非クラスター化インデックスの 1,700 です。 [!INCLUDE[ssSDS](../../includes/sssds-md.md)] および [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 以前のバージョンの場合、制限は列数が 16、サイズが 900 バイトになります。

ラージ オブジェクト (LOB) データ型の列 **ntext**、**text**、**varchar(max)** 、**nvarchar(max)** 、**varbinary(max)** 、**xml**、または **image** 、インデックスのキー列として指定することはできません。 また、ビュー定義に含めることはできません **ntext**、**text**、または **image** 列では、CREATE INDEX ステートメントで参照されていない場合でも。

バイナリ順序がサポートされる CLR ユーザー定義型列に対してインデックスを作成できます。 ユーザー定義型列からメソッドを呼び出すように定義されている計算列にも、そのメソッドが決定的とマークされていて、データ アクセス操作が実行されない限り、インデックスを作成できます。 CLR ユーザー定義型列でのインデックス作成の詳細については、「[CLR ユーザー定義型の使用](../../relational-databases/clr-integration-database-objects-user-defined-types/clr-user-defined-types.md)」を参照してください。

[ **ASC** | DESC ]      
特定のインデックス列に対して、昇順または降順の並べ替えの方向を指定します。 既定値は ASC です。

INCLUDE **(** _column_ [ **,** ... *n* ] **)**       
非クラスター化インデックスのリーフ レベルに、非キー列を追加します。 非クラスター化インデックスは、一意であっても一意でなくてもかまいません。

列名は INCLUDE リスト内で繰り返すことはできず、キー列と非キー列両方で同時に使用することはできません。 テーブルにクラスター化インデックスが定義されている場合、非クラスター化インデックスには常にクラスター化インデックスの列が含まれます。 詳細については、「 [付加列インデックスの作成](../../relational-databases/indexes/create-indexes-with-included-columns.md)」を参照してください。

**text**、 **ntext**、および **image** を除く、すべてのデータ型を使用できます。 インデックスを作成またはオフラインで再構築する必要があります (ONLINE = OFF) かどうかには、指定された非キー列のいずれかは **varchar (max)** , 、**nvarchar (max)** , 、または **varbinary (max)** データ型。

決定的な計算列、および正確または不正確な計算列を、付加列にできます。 派生した計算列 **image**、**ntext**、**text**、**varchar(max)** 、**nvarchar(max)** 、**varbinary(max)** 、および **xml** 計算列のデータ型が付加列として使用できる限り、非キー列でのデータ型を含めることができます。 詳細については、「 [計算列のインデックス](../../relational-databases/indexes/indexes-on-computed-columns.md)」を参照してください。

XML インデックスの作成の詳細については、「[CREATE XML INDEX](../../t-sql/statements/create-xml-index-transact-sql.md)」を参照してください。

WHERE \<filter_predicate>      
含める行を指定して、フィルター選択されたインデックスを作成します。 フィルター選択されたインデックスは、テーブル上の非クラスター化インデックスである必要があります。 フィルター選択されたインデックスのデータ行のフィルター選択された統計情報を作成します。

フィルター述語には単純な比較ロジックを使用するので、計算列、UDT 列、空間データ型列、または hierarchyID データ型列を参照することはできません。 比較演算子では、NULL リテラルを使用する比較を実行できません。 代わりに、IS NULL 演算子と IS NOT NULL 演算子を使用します。

次に、`Production.BillOfMaterials` テーブルのフィルター述語の例をいくつか示します。

```sql
WHERE StartDate > '20000101' AND EndDate <= '20000630'

WHERE ComponentID IN (533, 324, 753)

WHERE StartDate IN ('20000404', '20000905') AND EndDate IS NOT NULL
```

フィルター選択されたインデックスは、XML インデックスおよびフルテキスト インデックスには適用されません。 UNIQUE インデックスの場合、一意のインデックス値を持つ必要があるのは選択した行のみです。 フィルター選択されたインデックスでは IGNORE_DUP_KEY オプションを使用できません。

ON *partition_scheme_name* **( _column_name_ )**      

**適用対象**: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降) と [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]

ファイル グループが定義されているパーティション構成を指定します。このファイル グループは、パーティション インデックスのパーティションのマップ先となります。 [CREATE PARTITION SCHEME](../../t-sql/statements/create-partition-scheme-transact-sql.md) または [ALTER PARTITION SCHEME](../../t-sql/statements/alter-partition-scheme-transact-sql.md) を実行して、パーティション構成がデータベース内に存在するようにする必要があります。 *column_name* には、パーティション インデックスがパーティション分割される対象の列を指定します。 この列は、*partition_scheme_name* で使用されているパーティション関数の引数のデータ型、長さ、有効桁数に一致する必要があります。 *column_name* は、インデックス定義で指定されている列に限定されません。 UNIQUE インデックスをパーティション分割する場合、*column_name* は一意のキーとして使用されている列から選択する必要がありますが、それ以外の場合はベース テーブルの任意の列を指定できます。 この制限により、[!INCLUDE[ssDE](../../includes/ssde-md.md)]では、単一のパーティション内だけでキー値の一意性を確認できます。

> [!NOTE]
> 一意でないクラスター化インデックスをパーティション分割するとき、[!INCLUDE[ssDE](../../includes/ssde-md.md)]では既定により、まだ指定されていない場合、パーティション分割列がクラスター化インデックス キーのリストに追加されます。 一意でない非クラスター化インデックスをパーティション分割するとき、[!INCLUDE[ssDE](../../includes/ssde-md.md)]では、まだ指定されていない場合、パーティション分割列がインデックスの非キー列 (付加列) として追加されます。

_partition_scheme_name_ または _filegroup_ が指定されないまま、テーブルがパーティション分割されると、インデックスは基になるテーブルと同じパーティション分割列を使用して、同じパーティション構造に配置されます。

> [!NOTE]
> XML インデックスにはパーティション構成を指定できません。 ベース テーブルがパーティション分割される場合、XML インデックスではテーブルと同じパーティション構造が使用されます。

パーティション分割の詳細については、「[パーティション テーブルとパーティション インデックス](../../relational-databases/partitions/partitioned-tables-and-indexes.md)」を参照してください。

ON _filegroup_name_      

**適用対象**: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降)

指定したファイル グループに、指定したインデックスを作成します。 位置の指定がなく、テーブルまたはビューがパーティション分割されていない場合、インデックスには、基になるテーブルまたはビューと同じファイル グループが使用されます。 ファイル グループは既に存在している必要があります。

ON **"** default **"**      

**適用対象**: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降) と [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]

テーブルまたはビューと同じファイルグループまたはパーティション スキームに対して、指定されたインデックスを作成します。

このコンテキストでの default という用語はキーワードではありません。 default は、既定ファイル グループの識別子なので、ON **"** default **"** または ON **[** default **]** のように区切る必要があります。 "default" を指定する場合は、現在のセッションに対して QUOTED_IDENTIFIER オプションが ON である必要があります。 これが既定の設定です。 詳しくは、「[SET QUOTED_IDENTIFIER](../../t-sql/statements/set-quoted-identifier-transact-sql.md)」をご覧ください。

> [!NOTE]
> "default" は、CREATE INDEX のコンテキストでは、データベースの既定のファイル グループを示していません。 これは、"default" でデータベースの既定のファイルグループに対してテーブルを検索する CREATE TABLE とは異なります。

[ FILESTREAM_ON { *filestream_filegroup_name* | *partition_scheme_name* | "NULL" } ]      

**適用対象**: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降)

クラスター化インデックスの作成時に、テーブルの FILESTREAM データの配置を指定します。 FILESTREAM_ON 句を使用すると、異なる FILESTREAM ファイル グループやパーティション構成に FILESTREAM データを移動できます。

_filestream_filegroup_name_ FILESTREAM ファイル グループの名前を指定します。 ファイル グループには、[CREATE DATABASE](../../t-sql/statements/create-database-transact-sql.md) ステートメントまたは [ALTER DATABASE](../../t-sql/statements/alter-database-transact-sql.md) ステートメントを使用してファイルが 1 つ定義されている必要があります。それ以外の場合は、エラーが発生します。

テーブルがパーティション分割されている場合、FILESTREAM_ON 句を使用して、テーブルのパーティション構成と同じパーティション関数とパーティション列を使用するように、FILESTREAM ファイル グループのパーティション構成を指定する必要があります。 それ以外の場合は、エラーが発生します。

テーブルがパーティション分割されていない場合、FILESTREAM 列をパーティション分割することはできません。 テーブルの FILESTREAM データは、FILESTREAM_ON 句で指定した単一のファイル グループに格納する必要があります。

クラスター化インデックスの作成で、テーブルに FILESTREAM 列が含まれていないときは、CREATE INDEX ステートメントに `FILESTREAM_ON NULL` を指定できます。

詳細については、「[FILESTREAM &#40;SQL Server&#41;](../../relational-databases/blob/filestream-sql-server.md)」をご覧ください。

**\<object>::=**      

インデックスを作成するオブジェクトを、完全修飾または完全修飾ではない形式で指定します。

_database_name_      
データベースの名前です。

*schema_name*      
テーブルまたはビューが属するスキーマの名前を指定します。

*table_or_view_name*      
インデックスを作成するテーブルまたはビューの名前を指定します。

ビューにインデックスを作成するには、SCHEMABINDING を指定してそのビューを定義する必要があります。 ビューに非クラスター化インデックスを作成する前に、そのビューに一意のクラスター化インデックスを作成する必要があります。 インデックス付きビューの詳細については、「解説」を参照してください。

[!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 以降では、オブジェクトをクラスター化列ストア インデックスに格納されたテーブルに指定できます。

[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] では、*database_name* が現在のデータベースの場合、または _database_name_ が `tempdb` で、_object_name_ が # で始まる場合に、3 つの要素で構成された名前形式 _database_name_.[_schema_name_]._object_name_ をサポートします。

**\<relational_index_option\>::=**       
インデックスを作成するときに使用するオプションを指定します。

PAD_INDEX = { ON | **OFF** }      

**適用対象**: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降) と [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]

インデックスの埋め込みを指定します。 既定値は OFF です。

ON      
*fillfactor* で指定される空き領域のパーセンテージが、インデックスの中間レベルのページに適用されます。

OFF または _fillfactor_ の指定なし      
中間レベルのページはほぼ全容量が使用されます。ただし、中間ページにあるキーのセットを考慮して、インデックスに割り当てることのできる、少なくとも 1 行の最大サイズが収まる分の領域は残されます。

PAD_INDEX では FILLFACTOR で指定されるパーセンテージが使用されるので、PAD_INDEX オプションは、FILLFACTOR が指定されている場合にのみ有効です。 FILLFACTOR で指定されるパーセンテージで 1 行分のデータを格納できない場合、[!INCLUDE[ssDE](../../includes/ssde-md.md)] では内部的に、最小サイズを格納できるパーセンテージにオーバーライドします。 中間インデックス ページの行数は、_fillfactor_ の値がどれだけ小さくなっても 2 未満にはなりません。

旧バージョンと互換性のある構文では、WITH PAD_INDEX は WITH PAD_INDEX = ON と同じです。

FILLFACTOR **=** _fillfactor_      

**適用対象**: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降) と [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]

インデックスの作成時または再構築時に、[!INCLUDE[ssDE](../../includes/ssde-md.md)] が各インデックス ページのリーフ レベルをどの程度まで埋めるかを、パーセント値で指定します。 *fillfactor* 値には、1 ～ 100 の整数値を指定してください。 *fillfactor* が 100 の場合、[!INCLUDE[ssDE](../../includes/ssde-md.md)] では全容量を使用するリーフ ページでインデックスが作成されます。

FILLFACTOR 設定は、インデックスが作成または再構築されるときのみ適用されます。 [!INCLUDE[ssDE](../../includes/ssde-md.md)] では、ページ内で指定されたパーセント分の空き領域は動的に保持されません。 FILL FACTOR 設定を表示するには、[sys.indexes](../../relational-databases/system-catalog-views/sys-indexes-transact-sql.md) カタログ ビューを使用します。

> [!IMPORTANT]
> [!INCLUDE[ssDE](../../includes/ssde-md.md)]では、クラスター化インデックスの作成時にデータが再分配されるため、100 未満の FILLFACTOR 値を使ってクラスター化インデックスを作成すると、データ用のストレージ領域のサイズに影響が生じます。

詳細については、「 [インデックスの FILL FACTOR の指定](../../relational-databases/indexes/specify-fill-factor-for-an-index.md)」を参照してください。

SORT_IN_TEMPDB = { ON | **OFF** }      

**適用対象**: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降) と [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]

**tempdb** に一時的な並べ替え結果を格納するかどうかを指定します。 Azure SQL Database Hyperscale を除き、既定は OFF です。Hyperscale のインデックス作成操作についてはすべて、再開可能なインデックス リビルドが使用されていない限り、SORT_IN_TEMPDB は常に ON になります。

ON      
インデックス構築に使用される中間の並べ替え結果が **tempdb** に格納されます。 **tempdb** がユーザー データベースとは異なるディスク セットにある場合は、インデックスの作成に要する時間が削減されます。 インデックスの構築中に使用されるディスク領域のサイズは増加します。

OFF      
中間の並べ替え結果はインデックスと同じデータベースに格納されます。

インデックスを作成するためにユーザー データベース内に必要となる領域の他に、**tempdb** には、並べ替えの中間結果を格納するためにほぼ同じ大きさの追加領域が必要になります。 詳細については、「[インデックスの SORT_IN_TEMPDB オプション](../../relational-databases/indexes/sort-in-tempdb-option-for-indexes.md)」を参照してください。

旧バージョンと互換性のある構文では、WITH SORT_IN_TEMPDB は WITH SORT_IN_TEMPDB = ON と同じです。

IGNORE_DUP_KEY = { ON | **OFF** }      
挿入操作で、一意のインデックスに重複するキー値を挿入しようとした場合のエラー応答を指定します。 IGNORE_DUP_KEY オプションは、インデックスが作成または再構築された後の挿入操作のみに適用されます。 [CREATE INDEX](../../t-sql/statements/create-index-transact-sql.md)、[ALTER INDEX](../../t-sql/statements/alter-index-transact-sql.md)、または [UPDATE](../../t-sql/queries/update-transact-sql.md) を実行した場合、このオプションは無効です。 既定値は OFF です。

ON      
重複したキー値が一意のインデックスに挿入されると、警告メッセージが表示されます。 一意性制約に違反する行のみが失敗します。

OFF      
重複したキー値が一意のインデックスに挿入されると、エラー メッセージが表示されます。 INSERT 操作全体がロールバックされます。

ビューに作成されたインデックス、一意でないインデックス、XML インデックス、空間インデックス、およびフィルター選択されたインデックスの IGNORE_DUP_KEY を ON に設定することはできません。

IGNORE_DUP_KEY を表示するには、[sys.indexes](../../relational-databases/system-catalog-views/sys-indexes-transact-sql.md) を使用します。

旧バージョンと互換性のある構文では、WITH IGNORE_DUP_KEY は WITH IGNORE_DUP_KEY = ON と同じです。

STATISTICS_NORECOMPUTE = { ON | **OFF**}      
分布統計を再計算するかどうかを指定します。 既定値は OFF です。

ON      
古い統計情報は、自動的には再計算されません。

OFF      
自動統計更新が有効です。

自動統計更新を復元するには、STATISTICS_NORECOMPUTE を OFF に設定するか、NORECOMPUTE 句を指定せずに UPDATE STATISTICS を実行します。

> [!IMPORTANT]
> 分布統計の自動再計算を無効にすると、クエリ オプティマイザーで、テーブルが関与するクエリの最適実行プランが選択されなくなる場合があります。

旧バージョンと互換性のある構文では、WITH STATISTICS_NORECOMPUTE は WITH STATISTICS_NORECOMPUTE = ON と同じです。

STATISTICS_INCREMENTAL = { ON | **OFF** }     

**適用対象**:[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (開始値 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)]) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]

**ON** の場合、作成される統計はパーティションごとの統計です。 **OFF** の場合、統計ツリーが削除され、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] によって統計が再計算されます。 既定値は **OFF** です。

パーティションごとの統計がサポートされていない場合、このオプションは無視され、警告が生成されます。 次の種類の統計では、増分統計がサポートされていません。

- ベース テーブルにパーティションで固定されていないインデックスを使用して作成された統計。
- Always On の読み取り可能なセカンダリ データベースに対して作成された統計。
- 読み取り専用のデータベースに対して作成された統計。
- フィルター選択されたインデックスに対して作成された統計。
- ビューに対して作成された統計。
- 内部テーブルに対して作成された統計。
- 空間インデックスまたは XML インデックスを使用して作成された統計。

DROP_EXISTING = { ON | **OFF** }      
列の仕様が変更された既存のクラスター化または非クラスター化インデックスを削除して再構築し、インデックスを同じ名前のままにするオプションです。 既定値は OFF です。

ON      
既存のインデックスを削除して再構築することを指定します。これはパラメーター *index_name* と同じ名前である必要があります。

OFF      
既存のインデックスを削除および再構築しないことを指定します。 指定するインデックス名が既に存在する場合、SQL Server はエラーを表示します。

DROP_EXISTING では、以下を変更することができます。

- 非クラスター化行ストア インデックスからクラスター化行ストア インデックスへの変更。

DROP_EXISTING では、以下を変更できません。

- クラスター化行ストア インデックスから非クラスター化行ストア インデックスへの変更。
- クラスター化列ストア インデックスから任意の種類の行ストア インデックスへの変更。

旧バージョンと互換性のある構文では、WITH DROP_EXISTING は WITH DROP_EXISTING = ON と同じです。

ONLINE = { ON | **OFF** }      
インデックス操作時に、基になるテーブルや関連するインデックスをクエリやデータ変更で使用できるかどうかを指定します。 既定値は OFF です。

> [!IMPORTANT]
> オンラインでのインデックス操作は、[!INCLUDE[msCoName](../../includes/msconame-md.md)][!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のすべてのエディションで使用できるわけではありません。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の各エディションでサポートされる機能の一覧については、「[Editions and Supported Features for SQL Server 2016](../../sql-server/editions-and-components-of-sql-server-2016.md)」 (SQL Server 2016 のエディションとサポートされる機能) を参照してください。

ON      
長期のテーブル ロックは、インデックス操作の間は保持されません。 インデックス操作の主なフェーズの間は、基になるテーブル上に、インテント共有 (IS) ロックのみが保持されます。 これにより、基になるテーブルやインデックスに対するクエリや更新を続行できます。 操作の開始時、非常に短い時間ですが、ソース オブジェクトの共有 (S) ロックが保持されます。 操作の終了時、短い時間ですが、非クラスタ化インデックスが作成される場合は、ソース オブジェクト上で共有 (S) ロックの取得が行われます。また、クラスター化インデックスがオンラインで作成または削除され、クラスター化または非クラスター化インデックスが再構築される場合は、SCH-M (スキーマ修正) ロックが取得されます。 インデックスがローカルの一時テーブルに作成される場合、ONLINE は ON にできません。

OFF      
テーブル ロックは、インデックス操作の間適用されます。 クラスター化インデックスを作成、再構築、または削除するオフライン インデックス操作や、非クラスター化インデックスを再構築または削除するオフライン インデックス操作では、テーブル上のスキーマ修正 (Sch-M) ロックが取得されます。 このため、操作中は、すべてのユーザーは基になるテーブルにアクセスできません。 非クラスター化インデックスを作成するオフライン インデックス操作では、テーブルの共有 (S) ロックが取得されます。 この場合は、基になるテーブルに対して更新は許可されませんが、SELECT ステートメントなどの読み取り操作は許可されます。

詳しくは、「 [Perform Index Operations Online](../../relational-databases/indexes/perform-index-operations-online.md)」をご覧ください。  

インデックスは、グローバル一時テーブル上のインデックスを含めてオンラインで作成できます。ただし次の場合は例外です。

- XML インデックス
- ローカル一時テーブルのインデックス
- ビューの最初の一意クラスター化インデックス
- 無効なクラスター化インデックス
- 列ストア インデックス
- 基になるテーブルに LOB データ型 (**image**、**ntext**、**text**) および空間データ型が含まれる場合のクラスター化インデックス。
- **varchar(max)** 列と **varbinary(max)** 列は、インデックスの一部にすることはできません。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] では、テーブルに **varchar(max)** 列または **varbinary(max)** 列が含まれている場合、他の列を含むクラスター化インデックスは、**ONLINE** オプションを使用して作成または再作成できます。 ベース テーブルに **varchar(max)** 列または **varbinary(max)** 列が含まれている場合、[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] では **ONLINE** オプションが許可されません

詳細については、「[オンライン インデックス操作の動作原理](../../relational-databases/indexes/how-online-index-operations-work.md)」を参照してください。

RESUMABLE **=** { ON | **OFF**}      

**適用対象**:[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (開始値 [!INCLUDE[sql-server-2019](../../includes/sssql19-md.md)]) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]

 オンラインでのインデックス操作が再開可能かどうかを指定します。

 ON      
インデックス操作は再開可能です。

 OFF      
インデックス操作は再開可能ではありません。

MAX_DURATION **=** *time* **[MINUTES]** は **RESUMABLE = ON** (**ONLINE = ON** が必須) と共に使用   

**適用対象**:[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (開始値 [!INCLUDE[sql-server-2019](../../includes/sssql19-md.md)]) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]

再開可能なオンラインでのインデックス操作が、一時停止までに実行される時間 (分単位で指定する整数値) を示します。

> [!IMPORTANT]
> オンラインで実行できるインデックス操作の詳細については、「[オンライン インデックス操作のガイドライン](../../relational-databases/indexes/guidelines-for-online-index-operations.md)」を参照してください。

> [!NOTE] 
> 再開可能なオンライン インデックスのリビルドは、列ストア インデックスではサポートされていません。

ALLOW_ROW_LOCKS = { **ON** | OFF }      
**適用対象**: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降) と [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]

行ロックを許可するかどうかを指定します。 既定値は ON です。

ON      
インデックスにアクセスするとき、行ロックが許可されます。 いつ行ロックを使用するかは、[!INCLUDE[ssDE](../../includes/ssde-md.md)]によって決定されます。

OFF      
行ロックは使用されません。

ALLOW_PAGE_LOCKS = { **ON** | OFF }      
**適用対象**: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降) と [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]

ページ ロックを許可するかどうかを指定します。 既定値は ON です。

ON      
ページにアクセスするとき、行ロックが許可されます。 いつページ ロックを使用するかは、[!INCLUDE[ssDE](../../includes/ssde-md.md)] によって決定されます。

OFF      
ページ ロックは使用されません。

OPTIMIZE_FOR_SEQUENTIAL_KEY = { ON | **OFF** }      
**適用対象**:[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (開始値 [!INCLUDE[sql-server-2019](../../includes/sssql19-md.md)]) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]

最終ページ挿入競合に対して最適化するかどうかを指定します。 既定値は OFF です。 詳細については、「[シーケンシャル キー](#sequential-keys)」セクションを参照してください。

MAXDOP = *max_degree_of_parallelism*      
**適用対象**: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降) と [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]

インデックス操作の間、**max degree of parallelism** 構成オプションをオーバーライドします。 詳細については、「 [max degree of parallelism サーバー構成オプションの構成](../../database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option.md)」を参照してください。 並列プランの実行で使用されるプロセッサ数を制限するには、MAXDOP を使用します。 最大数は 64 プロセッサです。

*max_degree_of_parallelism* は次のように指定できます。

1      
並列プラン生成を抑制します。

\>1      
現在のシステム ワークロードに基づいて、並列インデックス操作で使用される最大プロセッサ数を指定の数以下に制限します。

0 (既定値)      
現在のシステム ワークロードに基づいて、実際の数以下のプロセッサを使用します。

 詳細については、「 [並列インデックス操作の構成](../../relational-databases/indexes/configure-parallel-index-operations.md)」を参照してください。

> [!NOTE]
> 並列インデックス操作は、[!INCLUDE[msCoName](../../includes/msconame-md.md)][!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のすべてのエディションで使用できるわけではありません。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の各エディションでサポートされる機能の一覧については、「[SQL Server 2016 の各エディションとサポートされている機能](../../sql-server/editions-and-components-of-sql-server-2016.md)」および「[SQL Server 2017 の各エディションとサポートされている機能](../../sql-server/editions-and-components-of-sql-server-2017.md)」をご覧ください。

DATA_COMPRESSION      
指定したインデックス、パーティション番号、またはパーティション範囲に、データ圧縮オプションを指定します。 次のようなオプションがあります。

NONE      
インデックスまたは指定したパーティションが圧縮されません。

ROW      
行の圧縮を使用して、インデックスまたは指定したパーティションが圧縮されます。

PAGE      
ページの圧縮を使用して、インデックスまたは指定したパーティションが圧縮されます。

圧縮の詳細については、「[データ圧縮](../../relational-databases/data-compression/data-compression.md)」を参照してください。

ON PARTITIONS **(** { \<partition_number_expression> | \<range> } [ **,** ..._n_ ] **)**       
**適用対象**: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降) と [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]

DATA_COMPRESSION 設定を適用するパーティションを指定します。 インデックスがパーティション分割されていない場合に ON PARTITIONS 引数を使用すると、エラーが発生します。 ON PARTITIONS 句を指定しないと、パーティション インデックスのすべてのパーティションに対して DATA_COMPRESSION オプションが適用されます。

\<partition_number_expression> は以下の方法で指定できます。

- パーティション番号を指定します。たとえば次のとおりです。ON PARTITIONS (2)。
- コンマで区切った複数の個別のパーティションのパーティション番号を指定します。たとえば次のとおりです。ON PARTITIONS (1, 5)。
- 範囲と個別のパーティションの両方を指定します。たとえば次のとおりです。ON PARTITIONS (2, 4, 6 TO 8)。

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

## <a name="remarks"></a>解説
CREATE INDEX ステートメントは、他のクエリと同じように最適化されます。 クエリ プロセッサでは I/O 操作を減らすため、テーブル スキャンの代わりに別のインデックスがスキャンされる場合があります。 状況によっては、並べ替え操作が行われない場合もあります。 マルチプロセッサ コンピューターの場合、CREATE INDEX では他のクエリと同様に、インデックス作成に関連するスキャンおよび並べ替え操作を実行するために、より多くのプロセッサを使用することができます。 詳細については、「 [並列インデックス操作の構成](../../relational-databases/indexes/configure-parallel-index-operations.md)」を参照してください。

データベース復旧モデルが一括ログ復旧モデルまたは単純復旧モデルのいずれかに設定されている場合、インデックス作成操作のログへの記録を最小限にできます。

一時テーブルにインデックスを作成することもできます。 テーブルが削除されるか、セッションが終了すると、インデックスは削除されます。

主キーが作成されたときに、テーブル変数にクラスター化インデックスを構築できます。 クエリが完了するかセッションが終了すると、インデックスが削除されます。

インデックスでは拡張プロパティがサポートされます。

## <a name="clustered-indexes"></a>クラスター化インデックス
テーブル (ヒープ) にクラスター化インデックスを作成したり、既存のクラスター化インデックスを削除して再作成する場合は、データの並べ替えや、基のテーブルまたは既存のクラスター化インデックス データの一時的コピーを実行するために、データベース内で追加の作業領域が使用可能になっている必要があります。 クラスター化インデックスの詳細については、「[クラスター化インデックスの作成](../../relational-databases/indexes/create-clustered-indexes.md)」と「[SQL Server のインデックスのアーキテクチャとデザイン ガイド](../../relational-databases/sql-server-index-design-guide.md)」を参照してください。

## <a name="nonclustered-indexes"></a>非クラスター化インデックス
[!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 以降および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] では、クラスター化列ストア インデックスとして格納されたテーブルに非クラスター化インデックスを作成できます。 最初に保存されているテーブルに非クラスター化インデックスを作成するかどうかは後で、テーブルをクラスター化列ストア インデックスに変換する場合は、ヒープまたはクラスター化インデックスに適用する場合は、インデックスが永続化されます。 クラスター化列ストア インデックスを再構築するときに、非クラスター化インデックスを削除する必要がではありません。

制限事項と制約事項:

- クラスター化列ストア インデックスとして格納されたテーブルに非クラスター化インデックスを作成する場合、`FILESTREAM_ON` オプションが無効です。

## <a name="unique-indexes"></a>一意なインデックス
一意のインデックスが存在する場合、[!INCLUDE[ssDE](../../includes/ssde-md.md)]は、挿入操作によってデータが追加されるたびに、重複した値がないかをチェックします。 重複キー値を生成する挿入操作はロールバックされ、[!INCLUDE[ssDE](../../includes/ssde-md.md)]はエラー メッセージを表示します。 挿入操作で多くの行が変更された場合でも、重複が 1 つでもあれば、ロールバックが行われます。 IGNORE_DUP_KEY 句が ON に設定されている一意のインデックスにデータを入力しようとすると一意のインデックスに違反する行だけが失敗します。

## <a name="partitioned-indexes"></a>パーティション インデックス
パーティション インデックスは、パーティション分割されたテーブルと同様の方法で作成および維持されますが、通常のインデックスのように、個別のデータベース オブジェクトとして扱われます。 パーティション分割されていないテーブルにパーティション インデックスを作成したり、パーティション分割されているテーブルに非パーティション インデックスを作成することもできます。

パーティション分割されているテーブルにインデックスを作成し、インデックスを配置するファイル グループを指定しない場合、インデックスは基になるテーブルと同じ方法でパーティション分割されます。 これは、既定では、インデックスは基になるテーブルと同じファイル グループに配置され、パーティション分割されたテーブルの場合、同じパーティション分割列を使用する同じパーティション構成に配置されるためです。 インデックスがテーブルと同じパーティション構成とパーティション分割列を使用する場合、インデックスはテーブルに *固定* されます。

> [!WARNING]
> 固定されていないインデックスをパーティションが 1, 000 個以上あるテーブルに作成または再構築することは可能ですが、サポートされていません。 このような操作を行うと、操作中にパフォーマンスが低下したりメモリが過度に消費される可能性があります。 パーティションの数が 1,000 個を超えた場合は、固定されたインデックスのみを使用することをお勧めします。

一意でないクラスター化インデックスをパーティション分割するとき、[!INCLUDE[ssDE](../../includes/ssde-md.md)] は既定では、まだ指定されていないパーティション分割列をクラスター化インデックス キーのリストに追加します。

インデックス付きビューは、テーブルのインデックスと同じ方法でパーティション分割されたテーブルに作成できます。 パーティション インデックスの詳細については、「[パーティション テーブルとパーティション インデックス](../../relational-databases/partitions/partitioned-tables-and-indexes.md)」と「[SQL Server のインデックスのアーキテクチャとデザイン ガイド](../../relational-databases/sql-server-index-design-guide.md)」を参照してください。

[!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] では、パーティション インデックスが作成または再構築された場合、テーブル内のすべての行をスキャンして統計を作成することはできません。 代わりに、クエリ オプティマイザーが既定のサンプリング アルゴリズムを使用して統計を生成します。 テーブル内のすべての行をスキャンしてパーティション インデックスの統計を作成するには、`FULLSCAN` 句で `CREATE STATISTICS` または `UPDATE STATISTICS` を使用します。

## <a name="filtered-indexes"></a>フィルター選択されたインデックス
フィルター選択されたインデックスは、最適化された非クラスター化インデックスであり、テーブルから選択する行の少ないクエリに適しています。 フィルター選択されたインデックスは、フィルター述語を使用してテーブル内の一部のデータにインデックスを作成します。 フィルター選択されたインデックスを適切に設計すると、クエリのパフォーマンスを向上させ、ストレージ コストとメンテナンス コストを削減することができます。

### <a name="required-set-options-for-filtered-indexes"></a>フィルター選択されたインデックスに必要な SET オプション

次の条件のいずれかに該当する場合、"必要な値" 列の SET オプションが必要となります。

- フィルター選択されたインデックスを作成するとき。
- INSERT、UPDATE、DELETE、MERGE のいずれかの操作で、フィルター選択されたインデックスのデータを変更するとき。
- クエリ オプティマイザーで、クエリ プランの生成にフィルター選択されたインデックスが使用されるとき。

    |SET オプション|必須値|既定のサーバー値|Default<br /><br /> OLE DB および ODBC 値|Default<br /><br /> DB-Library 値|
    |-----------------|--------------------|--------------------------|---------------------------------------|-----------------------------------|
    |ANSI_NULLS|ON|ON|ON|OFF|
    |ANSI_PADDING|ON|ON|ON|OFF|
    |ANSI_WARNINGS*|ON|ON|ON|OFF|
    |ARITHABORT|ON|ON|OFF|OFF|
    |CONCAT_NULL_YIELDS_NULL|ON|ON|ON|OFF|
    |NUMERIC_ROUNDABORT|OFF|OFF|OFF|OFF|
    |QUOTED_IDENTIFIER|ON|ON|ON|OFF|
  
     * ANSI_WARNINGS を ON に設定すると、データベース互換性レベルが 90 以上に設定されている場合、暗黙的に ARITHABORT が ON に設定されます。 データベース互換性レベルが 80 以下に設定されている場合は、ARITHABORT オプションを明示的に ON に設定する必要があります。

SET オプションが正しくないと、次の状態が発生する場合があります。

- フィルター選択されたインデックスが作成されません。
- [!INCLUDE[ssDE](../../includes/ssde-md.md)] によりエラーが生成され、インデックスのデータを変更していた INSERT ステートメント、UPDATE ステートメント、DELETE ステートメント、または MERGE ステートメントがロールバックされます。
- Transact-SQL ステートメントの実行プランで、クエリ オプティマイザーがインデックスを無視します。

 フィルター選択されたインデックスの詳細については、「[フィルター選択されたインデックスの作成](../../relational-databases/indexes/create-filtered-indexes.md)」と「[SQL Server のインデックスのアーキテクチャとデザイン ガイド](../../relational-databases/sql-server-index-design-guide.md)」を参照してください。

## <a name="spatial-indexes"></a>空間インデックス
空間インデックスについては、「[CREATE SPATIAL INDEX](../../t-sql/statements/create-spatial-index-transact-sql.md)」および「[空間インデックスの概要](../../relational-databases/spatial/spatial-indexes-overview.md)」をご覧ください。

## <a name="xml-indexes"></a>XML インデックス
XML インデックスについては、「[CREATE XML INDEX](../../t-sql/statements/create-xml-index-transact-sql.md)」および「[XML インデックス &#40;SQL Server&#41;](../../relational-databases/xml/xml-indexes-sql-server.md)」をご覧ください。

## <a name="index-key-size"></a>インデックス キーのサイズ
インデックス キーの最大サイズは 900 バイトをクラスター化インデックスと非クラスター化インデックスの 1,700 バイトです。 ([!INCLUDE[ssSDS](../../includes/sssds-md.md)] および [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 以前では、制限は常に 900 バイトでした)。インデックス **varchar** 既存のデータ列には、インデックスの作成時の制限を超えない場合をバイトの制限を超える列を作成することができます。 ただし、後続の挿入や更新操作を、制限を超える合計サイズとなる列には失敗します。 クラスター化インデックスのインデックス キーには、ROW_OVERFLOW_DATA アロケーション ユニットに既存のデータを持つ **varchar** 列を含めることはできません。 クラスター化インデックスが **varchar** 列に作成され、既存のデータが IN_ROW_DATA アロケーション ユニットにある場合に、データを行外に押し出すような挿入処理や更新処理をその列に対して行うと失敗します。

非クラスター化インデックスのリーフ レベルに非キー列を含めることができます。 インデックス キー サイズを計算するとき、[!INCLUDE[ssDE](../../includes/ssde-md.md)]ではこれらの列は考慮されません。 詳細については、「[付加列インデックスの作成](../../relational-databases/indexes/create-indexes-with-included-columns.md)」と「[SQL Server のインデックスのアーキテクチャとデザイン ガイド](../../relational-databases/sql-server-index-design-guide.md)」を参照してください。

> [!NOTE]
> テーブルがパーティション分割されるとき、パーティション分割キー列が一意でないクラスター化インデックスにまだ存在していない場合は、[!INCLUDE[ssDE](../../includes/ssde-md.md)]によってインデックスに追加されます。 インデックス付きの列の合計サイズ (付加列は含みません) と追加されるパーティション分割列のサイズの合計は、一意でないクラスター化インデックスでは 1800 バイトを超えることはできません。

## <a name="computed-columns"></a>計算列
インデックスを計算列に作成できます。 また、計算列にプロパティ PERSISTED を設定することができます。 その場合、 [!INCLUDE[ssDE](../../includes/ssde-md.md)] によってテーブルに計算値が格納され、計算列が依存している他の列が更新されるとその計算値も更新されます。 [!INCLUDE[ssDE](../../includes/ssde-md.md)] は、列にインデックスを作成するとき、およびインデックスがクエリで参照されるときに、これらの保存値を使用します。

計算列のインデックスを作成するには、計算列が決定的で正確である必要があります。 ただし、PERSISTED プロパティを使用した場合、インデックス作成が可能となる計算列の種類は、次のようになります。

- [!INCLUDE[tsql](../../includes/tsql-md.md)]、CLR 関数、およびユーザーによって決定的とマークされた CLR ユーザー定義型メソッドに基づく計算列。
- [!INCLUDE[ssDE](../../includes/ssde-md.md)]の定義によると決定的であるが、正確でない式に基づく計算列。

保存される計算列に対しては、前の「[フィルター選択されたインデックスに必要な SET オプション](#required-set-options-for-filtered-indexes)」で示すように、次の SET オプションを設定する必要があります。

インデックス作成の条件をすべて満たしている限り、UNIQUE または PRIMARY KEY 制約があっても計算列を含めることができます。 この計算列は、決定的かつ正確であるか、決定的かつ持続可能である必要があります。 決定性の詳細については、「[決定的関数と非決定的関数](../../relational-databases/user-defined-functions/deterministic-and-nondeterministic-functions.md)」を参照してください。

派生した計算列 **image**、**ntext**、**text**、**varchar(max)** 、**nvarchar(max)** 、**varbinary(max)** 、および **xml** データ型は、インデックスを設定または含まれる非キー列としては、計算列のデータ型をインデックス キー列または非キー列として使用できる限りです。 たとえば、**xml** 計算列にはプライマリ XML インデックスを作成できません。 インデックス サイズが 900 バイトを超える場合、警告メッセージが表示されます。

計算列にインデックスを作成すると、以前は機能していた挿入または更新の操作が失敗することがあります。 このような失敗は、計算列の結果が算術エラーになる場合に発生する可能性があります。 たとえば、次のテーブルでは、計算列 `c` は計算エラーになりますが、INSERT ステートメントは正常に実行されます。

```sql
CREATE TABLE t1 (a INT, b INT, c AS a/b);
INSERT INTO t1 VALUES (1, 0);
```

これに対し、テーブルの作成後に計算列 `c` にインデックスを作成すると、同じ `INSERT` ステートメントは失敗します。

```sql
CREATE TABLE t1 (a INT, b INT, c AS a/b);
CREATE UNIQUE CLUSTERED INDEX Idx1 ON t1(c);
INSERT INTO t1 VALUES (1, 0);
```

詳細については、「 [計算列のインデックス](../../relational-databases/indexes/indexes-on-computed-columns.md)」を参照してください。

## <a name="included-columns-in-indexes"></a>インデックスの付加列
付加列と呼ばれる非キー列は、非クラスター化インデックスのリーフ レベルに追加でき、クエリに対応することによりクエリ パフォーマンスを向上できます。 この場合、クエリで参照されるすべての列は、キー列または非キー列としてインデックスに含まれます。 これにより、クエリ オプティマイザーではテーブルまたはクラスター化インデックス データにアクセスすることなく、インデックス スキャンによって必要な情報をすべて特定できます。 詳細については、「[付加列インデックスの作成](../../relational-databases/indexes/create-indexes-with-included-columns.md)」と「[SQL Server のインデックスのアーキテクチャとデザイン ガイド](../../relational-databases/sql-server-index-design-guide.md)」を参照してください。

## <a name="specifying-index-options"></a>インデックス オプションの指定
[!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] では新しいインデックス オプションが導入され、オプションの指定方法も変更になりました。 旧バージョンと互換性のある構文では、WITH *option_name* は WITH **(** \<option_name> **= ON )** と同じです。 インデックス オプションを設定する場合は、次の規則が適用されます。

- 新しいインデックス オプションは、WITH (**_option\_name_ = ON | OFF**) を使用してのみ指定できる。
- 同じステートメントで、旧バージョンとの互換性がある構文と新しい構文の両方を使ってオプションを指定することはできない。 たとえば、WITH (**DROP_EXISTING, ONLINE = ON**) を指定すると、ステートメントは失敗します。
- XML インデックスを作成するとき、オプションは WITH (**_option_name_= ON | OFF**) を使用して指定する必要がある。

## <a name="drop_existing-clause"></a>DROP_EXISTING 句
DROP_EXISTING 句を使用して、インデックスの再構築、列の追加または削除、オプションの変更、列の並べ替え順の変更、パーティション構成またはファイル グループの変更を行うことができます。

インデックスに PRIMARY KEY または UNIQUE 制約が設定されていて、インデックス定義が変更されることがない場合は、既存の制約を保持したままインデックスが削除され再作成されます。 ただし、インデックス定義が変更されると、ステートメントは失敗します。 PRIMARY KEY または UNIQUE 制約の定義を変更するには、制約を削除し、新しい定義で制約を追加します。

DROP_EXISTING を使用すると、非クラスター化インデックスが定義されているテーブル上で、同じまたは異なるキー セットのクラスター化インデックスを再作成するときのパフォーマンスを向上できます。 DROP_EXISTING では、古いクラスター化インデックスに DROP INDEX ステートメントを実行した後、新しいクラスター化インデックスに CREATE INDEX ステートメントを実行するという操作を一度に実行できます。 非クラスター化インデックスは一度だけ再構築され、その後はインデックス定義が変更された場合のみ再構築されます。 インデックス定義に元のインデックスと同じインデックス名、キーおよびパーティション列、一意性属性、および並べ替え順がある場合、DROP_EXISTING 句で非クラスター化インデックスを再構築できません。

非クラスター化インデックスが再構築されるかどうかに関係なく、非クラスター化インデックスは元のファイル グループまたはパーティション構成に常に属したままになり、元のパーティション関数を使用します。 クラスター化インデックスが他のファイル グループまたはパーティション構成に再構築される場合、非クラスター化インデックスはクラスター化インデックスの新しい位置に移動されません。 したがって、以前に非クラスター化インデックスがクラスター化インデックスに対応した位置にあっても、再構築後は別の位置になる可能性があります。 パーティション インデックスの位置合わせの詳細については、「[パーティション テーブルとパーティション インデックス](../../relational-databases/partitions/partitioned-tables-and-indexes.md)」を参照してください。

インデックス ステートメントで非クラスター化インデックスが指定され、かつ ONLINE オプションが OFF に設定されている場合を除き、同じインデックス キー列が同じ順序 (昇順または降順も同じ) で使用される場合、DROP_EXISTING 句では再度データの並べ替えは行われません。 クラスター化インデックスが無効になっている場合、CREATE INDEX WITH DROP_EXISTING 操作は ONLINE が OFF に設定された状態で実行する必要があります。 非クラスター化インデックスが無効で、無効なクラスター化インデックスと関連がない場合、CREATE INDEX WITH DROP_EXISTING 操作は、ONLINE が OFF または ON に設定された状態で実行できます。

> [!NOTE]
> 128 以上のエクステントがあるインデックスを削除または再構築するとき、[!INCLUDE[ssDE](../../includes/ssde-md.md)]は、トランザクションがコミットされるまで実際のページの割り当て解除とそれに関連するロックを延期します。

## <a name="online-option"></a>ONLINE オプション
インデックス操作をオンラインで実行する場合は、次のガイドラインが適用されます。

- オンライン インデックス操作の実行中、基になるテーブルは変更、切り捨て、削除できない。
- インデックス操作中は、追加の一時ディスク領域が必要。
- オンライン操作は、パーティション インデックスや、保存される計算列を含むインデックス、または付加列で実行できる。

詳しくは、「 [Perform Index Operations Online](../../relational-databases/indexes/perform-index-operations-online.md)」をご覧ください。

### <a name="resumable-index-operations"></a><a name="resumable-indexes"></a>再開可能なインデックス操作
**適用対象**:[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (開始値 [!INCLUDE[sql-server-2019](../../includes/sssql19-md.md)]) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]

次のガイドラインは再開可能なインデックス操作に適用されます。

- オンラインでのインデックス作成は、`RESUMABLE = ON` オプションを使って再開可能として指定できます。
- RESUMABLE オプションは特定のインデックス用のメタデータ内に保持されるのではなく、現在の DDL ステートメントの実行中にのみ適用されます。 したがって、再開機能を有効にするには、`RESUMABLE = ON` 句を明示的に指定する必要があります。
- MAX_DURATION オプションは、`RESUMABLE = ON` オプションに対してのみサポートされます。
- MAX_DURATION for RESUMABLE オプションでは、構築するインデックスの時間間隔を指定します。 この時間が経過すると、インデックスの構築が一時停止するか、またはその実行が完了します。 ユーザーは、一時停止されたインデックスの構築を再開可能にするタイミングを決定します。 MAX_DURATION の **時間** (分単位) は、0 分より長く、かつ 1 週間 (7 \* 24 \* 60 = 10080 分) 以下とする必要があります。 インデックス操作の一時停止時間を長くすると、特定のテーブルでの DML パフォーマンスおよびデータベースのディスク容量に影響を及ぼす可能性があります。元々存在するインデックスも新たに作成されたインデックスもディスク容量を必要とし、DML 操作中に更新される必要があるからです。 MAX_DURATION オプションを省略した場合、インデックス操作は、それが完了するかまたは障害が発生するまで続行されます。
- インデックス操作を直ちに一時停止するには、進行中のコマンドを停止するか (Ctrl + C キー)、[ALTER INDEX](alter-index-transact-sql.md) PAUSE コマンドを実行するか、または `KILL <session_id>` コマンドを実行します。 コマンドが一時停止されたら、[ALTER INDEX](alter-index-transact-sql.md) コマンドを使って再開することができます。
- 再開可能なインデックスに対する元の CREATE INDEX ステートメントを再実行すると、一時停止されていたインデックス作成操作が自動的に再開されます。
- `SORT_IN_TEMPDB = ON` オプションは、再開可能なインデックスに対してはサポートされていません。
- `RESUMABLE = ON` を指定した DDL コマンドを、明示的なトランザクション内で実行することはできません (BEGIN TRAN ...COMMIT ブロックの一部にすることはできません)。
- インデックスの作成/再構築を再開/中止するには、[ALTER INDEX](alter-index-transact-sql.md) T-SQL 構文を使用します

> [!NOTE]
> DDL コマンドは、完了するか、一時停止するか、または失敗するまで実行されます。 コマンドが一時停止した場合は、操作が一時停止され、インデックスの作成が完了しなかったことを示すエラーが発行されます。 現在のインデックスの状態の詳細については、[sys.index_resumable_operations](../../relational-databases/system-catalog-views/sys-index-resumable-operations.md) を参照してください。 前と同様に、障害が発生した場合はエラーも発行されます。

インデックス作成が再開可能な操作として実行されることを示し、現在の実行状態を確認する方法については、「[sys.index_resumable_operations](../../relational-databases/system-catalog-views/sys-index-resumable-operations.md)」をご覧ください。

#### <a name="resources"></a>リソース
再開可能なオンライン インデックス作成操作には、次のリソースが必要です。

- インデックスが一時停止されている期間など、構築されているインデックスを保持するために追加の領域が必要である。
- 並べ替えフェーズ中の追加ログ スループット。 再開可能なインデックスの全体的なログ領域使用量は、通常のオンライン インデックス作成と比較して少なく、この操作中にログを切り捨てることができます。
- いかなる DDL 変更も阻止する DDL 状態
- 一時停止と操作実行の両方の操作期間中、ゴースト クリーンアップはビルド内のインデックスでブロックされます。

#### <a name="current-functional-limitations"></a>現在の機能上の制限
再開可能なインデックス作成操作に対して次の機能は無効になります。

- 再開可能なオンライン インデックス作成操作が一時停止された後、MAXDOP の初期値を変更することはできません。
- 次のものを含むインデックスの作成

  - キー列としての計算列または TIMESTAMP 列
  - 再開可能なインデックス作成に含まれる列としての LOB 列
  - フィルター選択されたインデックス

## <a name="row-and-page-locks-options"></a>行およびページ ロック オプション
`ALLOW_ROW_LOCKS = ON` と `ALLOW_PAGE_LOCK = ON` の場合、インデックスにアクセスするとき、行レベル、ページ レベル、テーブル レベルのロックが許可されます。 [!INCLUDE[ssDE](../../includes/ssde-md.md)]は適切なロックを選択し、行ロックまたはページ ロックをテーブル ロックにエスカレートすることができます。

`ALLOW_ROW_LOCKS = OFF` と `ALLOW_PAGE_LOCK = OFF` の場合、インデックスにアクセスするとき、テーブル レベルのロックのみが許可されます。

## <a name="sequential-keys"></a>シーケンシャル キー
**適用対象**:[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (開始値 [!INCLUDE[sql-server-2019](../../includes/sssql19-md.md)]) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 

最終ページ挿入競合は、多数の同時実行スレッドがシーケンシャル キーを使用するインデックスに行を挿入しようと発生する、一般的なパフォーマンスの問題です。 先頭のキー列に、ID 列や現在の日付/時刻が既定値である日付など、常に増加 (または減少) する値が含まれている場合、インデックスはシーケンシャルと見なされます。 挿入されるキーはシーケンシャルであるため、すべての新しい行がインデックス構造の最後、つまり同じページに挿入されます。 これにより、メモリ内のページで競合が発生し、対象のページに対する PAGELATCH_EX で待機している複数のスレッドとして観察できます。

OPTIMIZE_FOR_SEQUENTIAL_KEY インデックス オプションをオンにすると、インデックスへの高コンカレンシーの挿入のスループット向上に役立つ、データベース エンジン内での最適化が有効になります。 それは、シーケンシャル キーを使用していて最終ページ挿入競合が発生しやすいインデックスを対象とするものですが、B ツリー インデックス構造の他の領域でホット スポットが発生するインデックスでも役に立つ場合があります。

## <a name="viewing-index-information"></a>インデックス情報の表示
インデックスに関する情報を返すには、カタログ ビュー、システム関数、およびシステム ストアド プロシージャを使用できます。

## <a name="data-compression"></a>データ圧縮
データの圧縮については、「[Data Compression](../../relational-databases/data-compression/data-compression.md)」に記載されています。 特に次の点に注意してください。

- 圧縮を使用すると、ページに格納できる行数が増えますが、最大行サイズは変更されません。
- インデックスの非リーフ ページでは、ページの圧縮は行われませんが、行の圧縮は可能です。
- 非クラスター化インデックスにはそれぞれ個別の圧縮設定があり、基になるテーブルの圧縮設定は継承されません。
- ヒープにクラスター化インデックスを作成する場合、圧縮状態を特に指定しない限り、ヒープの圧縮状態がクラスター化インデックスに継承されます。

パーティション インデックスには次の制限が適用されます。

- 固定されていないインデックスがテーブルにある場合、そのパーティションの圧縮設定を変更できません。
- ALTER INDEX \<index> ...REBUILD PARTITION ... 構文は、そのインデックスの指定のパーティションを再構築します。
- ALTER INDEX \<index> ...REBUILD WITH ... 構文は、そのインデックスのすべてのパーティションを再構築します。

圧縮状態の変更による、テーブル、インデックス、またはパーティションへの影響を評価するには、 [sp_estimate_data_compression_savings](../../relational-databases/system-stored-procedures/sp-estimate-data-compression-savings-transact-sql.md) ストアド プロシージャを使用します。

## <a name="permissions"></a>アクセス許可
テーブルまたはビューの `ALTER` 権限、もしくは `db_ddladmin` 固定データベース ロールのメンバーシップが必要です。

## <a name="limitations-and-restrictions"></a>制限事項と制約事項
[!INCLUDE[ssSDW](../../includes/sssdw-md.md)] および [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] では、以下を作成することはできません。

- 列ストア インデックスが既に存在する場合に、データ ウェアハウス テーブル上のクラスター化または非クラスター化行ストア インデックス。 この動作は、行ストアと列ストアの両方のインデックスが同じテーブル上に共存できる SMP [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] とは異なります。
- インデックスをビューに作成することはできません。

## <a name="metadata"></a>Metadata
既存のインデックスに関する情報を表示するには、[sys.indexes](../../relational-databases/system-catalog-views/sys-indexes-transact-sql.md) カタログ ビューに対してクエリを実行します。

## <a name="version-notes"></a>バージョンに関するメモ

[!INCLUDE[ssSDS](../../includes/sssds-md.md)] では、filegroup オプションおよび filestream オプションはサポートされません。

## <a name="examples-all-versions-uses-the-adventureworks-database"></a>例 :すべてのバージョン。 AdventureWorks データベースを使用します。

### <a name="a-create-a-simple-nonclustered-rowstore-index"></a>A. 単純な非クラスター化行ストア インデックスを作成する
次の例では、`Purchasing.ProductVendor` テーブルの `VendorID` 列に非クラスター化インデックスを作成します。

```sql
CREATE INDEX IX_VendorID ON ProductVendor (VendorID);
CREATE INDEX IX_VendorID ON dbo.ProductVendor (VendorID DESC, Name ASC, Address DESC);
CREATE INDEX IX_VendorID ON Purchasing..ProductVendor (VendorID);
```

### <a name="b-create-a-simple-nonclustered-rowstore-composite-index"></a>B. 単純な非クラスター化行ストア複合インデックスを作成する
次の例では、`Sales.SalesPerson` テーブルの `SalesQuota` 列および `SalesYTD` 列に非クラスター化複合インデックスを作成します。

```sql
CREATE NONCLUSTERED INDEX IX_SalesPerson_SalesQuota_SalesYTD ON Sales.SalesPerson (SalesQuota, SalesYTD);
```

### <a name="c-create-an-index-on-a-table-in-another-database"></a>C. 他のデータベースのテーブルにインデックスを作成する
次の例では、`Purchasing` データベースにある `ProductVendor` テーブルの `VendorID` 列にクラスター化インデックスを作成します。

```sql
CREATE CLUSTERED INDEX IX_ProductVendor_VendorID ON Purchasing..ProductVendor (VendorID);
```

### <a name="d-add-a-column-to-an-index"></a>D. インデックスに列を追加する
次の例では、dbo.FactFinance テーブルの 2 つの列でインデックス IX_FF を作成します。 次のステートメントでは、1 つ以上の列でインデックスを再構築し、既存の名前を保持します。

```sql
CREATE INDEX IX_FF ON dbo.FactFinance (FinanceKey ASC, DateKey ASC);

-- Rebuild and add the OrganizationKey
CREATE INDEX IX_FF ON dbo.FactFinance (FinanceKey, DateKey, OrganizationKey DESC)
  WITH (DROP_EXISTING = ON);
```

## <a name="examples-sql-server-azure-sql-database"></a>例 :SQL Server、Azure SQL Database

### <a name="e-create-a-unique-nonclustered-index"></a>E. 一意の非クラスター化インデックスを作成する
次の例では、[!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] データベースにある `Name` テーブルの `Production.UnitMeasure` 列に一意の非クラスター化インデックスを作成します。 このインデックスでは、`Name` 列に挿入されるデータが一意である必要があります。

```sql
CREATE UNIQUE INDEX AK_UnitMeasure_Name
  ON Production.UnitMeasure(Name);
```

次のクエリでは、既存の行と同じ値の行を挿入することによって、一意性の制約をテストします。

```sql
-- Verify the existing value.
SELECT Name FROM Production.UnitMeasure WHERE Name = N'Ounces';
GO

INSERT INTO Production.UnitMeasure (UnitMeasureCode, Name, ModifiedDate)
  VALUES ('OC', 'Ounces', GETDATE());
```

結果のエラー メッセージは次のようになります。

```
Server: Msg 2601, Level 14, State 1, Line 1
Cannot insert duplicate key row in object 'UnitMeasure' with unique index 'AK_UnitMeasure_Name'. The statement has been terminated.
```

### <a name="f-use-the-ignore_dup_key-option"></a>F. IGNORE_DUP_KEY オプションを使用する
次の例では、最初に `IGNORE_DUP_KEY` オプションを `ON` に設定し、次にこのオプションを `OFF` に設定して、複数の行を一時テーブルに挿入したときのこのオプションの影響を検証します。 2 番目の複数行の `#Test` ステートメントを実行するときには、`INSERT` テーブルに、重複する値となる 1 行を意図的に挿入します。 テーブル内の行数としては、挿入された行数が返されます。

```sql
CREATE TABLE #Test (C1 NVARCHAR(10), C2 NVARCHAR(50), C3 DATETIME);
GO

CREATE UNIQUE INDEX AK_Index ON #Test (C2)
  WITH (IGNORE_DUP_KEY = ON);
GO

INSERT INTO #Test VALUES (N'OC', N'Ounces', GETDATE());
INSERT INTO #Test SELECT * FROM Production.UnitMeasure;
GO

SELECT COUNT(*) AS [Number of rows] FROM #Test;
GO

DROP TABLE #Test;
GO
```

次は 2 番目の `INSERT` ステートメントの結果です。

```cmd
Server: Msg 3604, Level 16, State 1, Line 5 Duplicate key was ignored.

Number of rows
--------------
38
```

一意性の制約に違反していない `Production.UnitMeasure` テーブルからの行は、正常に挿入されています。 ここでは警告が発行され、重複する行が無視されましたが、トランザクション全体はロールバックされていません。

次に、`IGNORE_DUP_KEY` を `OFF` に設定して同じステートメントを実行します。

```sql
CREATE TABLE #Test (C1 NVARCHAR(10), C2 NVARCHAR(50), C3 DATETIME);
GO

CREATE UNIQUE INDEX AK_Index ON #Test (C2)
  WITH (IGNORE_DUP_KEY = OFF);
GO

INSERT INTO #Test VALUES (N'OC', N'Ounces', GETDATE());
INSERT INTO #Test SELECT * FROM Production.UnitMeasure;
GO

SELECT COUNT(*) AS [Number of rows] FROM #Test;
GO

DROP TABLE #Test;
GO
```

次は 2 番目の `INSERT` ステートメントの結果です。

```
Server: Msg 2601, Level 14, State 1, Line 5
Cannot insert duplicate key row in object '#Test' with unique index
'AK_Index'. The statement has been terminated.

Number of rows
--------------
1
```

ここでは、`Production.UnitMeasure` テーブルで `UNIQUE` インデックス制約に違反した行は 1 行だけでしたが、このテーブルから行は挿入されませんでした。

### <a name="g-using-drop_existing-to-drop-and-re-create-an-index"></a>G. DROP_EXISTING を使ってインデックスを削除し再作成する
次の例では、`ProductID` オプションを使って、[!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] データベースにある`Production.WorkOrder` テーブルの `DROP_EXISTING` 列にある既存のインデックスを削除して再作成します。 ここではオプション `FILLFACTOR` および `PAD_INDEX` も設定されています。

```sql
CREATE NONCLUSTERED INDEX IX_WorkOrder_ProductID
  ON Production.WorkOrder(ProductID)
    WITH (FILLFACTOR = 80,
      PAD_INDEX = ON,
      DROP_EXISTING = ON);
GO
```

### <a name="h-create-an-index-on-a-view"></a>H. ビューにインデックスを作成する
次の例では、ビューとそのビューのインデックスを作成します。 ここでは、インデックス付きビューを使用する 2 つのクエリを実行します。

```sql
-- Set the options to support indexed views
SET NUMERIC_ROUNDABORT OFF;
SET ANSI_PADDING, ANSI_WARNINGS, CONCAT_NULL_YIELDS_NULL, ARITHABORT,
  QUOTED_IDENTIFIER, ANSI_NULLS ON;
GO

-- Create view with schemabinding
IF OBJECT_ID ('Sales.vOrders', 'view') IS NOT NULL
  DROP VIEW Sales.vOrders;
GO

CREATE VIEW Sales.vOrders
  WITH SCHEMABINDING
AS
  SELECT SUM(UnitPrice * OrderQty * (1.00 - UnitPriceDiscount)) AS Revenue,
    OrderDate, ProductID, COUNT_BIG(*) AS COUNT
  FROM Sales.SalesOrderDetail AS od, Sales.SalesOrderHeader AS o
  WHERE od.SalesOrderID = o.SalesOrderID
  GROUP BY OrderDate, ProductID;
GO

-- Create an index on the view
CREATE UNIQUE CLUSTERED INDEX IDX_V1
  ON Sales.vOrders (OrderDate, ProductID);
GO

-- This query can use the indexed view even though the view is
-- not specified in the FROM clause.
SELECT SUM(UnitPrice * OrderQty * (1.00 - UnitPriceDiscount)) AS Rev,
  OrderDate, ProductID
FROM Sales.SalesOrderDetail AS od
  JOIN Sales.SalesOrderHeader AS o ON od.SalesOrderID = o.SalesOrderID
    AND ProductID BETWEEN 700 AND 800
    AND OrderDate >= CONVERT(DATETIME, '05/01/2002', 101)
GROUP BY OrderDate, ProductID
ORDER BY Rev DESC;
GO

-- This query can use the above indexed view
SELECT OrderDate, SUM(UnitPrice * OrderQty * (1.00 - UnitPriceDiscount)) AS Rev
FROM Sales.SalesOrderDetail AS od
  JOIN Sales.SalesOrderHeader AS o ON od.SalesOrderID = o.SalesOrderID
    AND DATEPART(mm, OrderDate) = 3
  AND DATEPART(yy, OrderDate) = 2002
GROUP BY OrderDate
ORDER BY OrderDate ASC;
GO
```

### <a name="i-create-an-index-with-included-non-key-columns"></a>I. 非キー列 (付加列) を使用してインデックスを作成する
次の例では、1 つのキー列 (`PostalCode`) と 4 つの非キー列 (`AddressLine1`、`AddressLine2`、`City`、`StateProvinceID`) を使って非クラクタ化インデックスを作成します。 次に、そのインデックスが対応するクエリを実行します。 クエリ オプティマイザーによって選択されるインデックスを [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] の **[クエリ]** メニューに表示するには、クエリを実行する前に **[実際の実行プランを含める]** を選択します。

```sql
CREATE NONCLUSTERED INDEX IX_Address_PostalCode
  ON Person.Address (PostalCode)
  INCLUDE (AddressLine1, AddressLine2, City, StateProvinceID);
GO

SELECT AddressLine1, AddressLine2, City, StateProvinceID, PostalCode
FROM Person.Address
WHERE PostalCode BETWEEN N'98000' and N'99999';
GO
```

### <a name="j-create-a-partitioned-index"></a>J. パーティション インデックスを作成する
次の例では、[!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] データベースの既存のパーティション構成 `TransactionsPS1` に非クラスター化パーティション インデックスを作成します。 この例では、パーティション インデックスのサンプルがインストールされていることを前提としています。

**適用対象**: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降) と [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]

```sql
CREATE NONCLUSTERED INDEX IX_TransactionHistory_ReferenceOrderID
  ON Production.TransactionHistory (ReferenceOrderID)
  ON TransactionsPS1 (TransactionDate);
GO
```

### <a name="k-creating-a-filtered-index"></a>K. フィルター選択されたインデックスを作成する
次の例では、[!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] データベースの Production.BillOfMaterials テーブルにフィルター選択されたインデックスを作成します。 フィルター述語では、フィルター選択されたインデックスに非キー列を含めることができます。 この例の述語では、EndDate が NULL 以外の行だけを選択します。

```sql
CREATE NONCLUSTERED INDEX "FIBillOfMaterialsWithEndDate"
  ON Production.BillOfMaterials (ComponentID, StartDate)
  WHERE EndDate IS NOT NULL;
```

### <a name="l-create-a-compressed-index"></a>L. 圧縮されたインデックスを作成する
次の例では、行の圧縮を使用して、非パーティション テーブルのインデックスを作成します。

```sql
CREATE NONCLUSTERED INDEX IX_INDEX_1
  ON T1 (C2)
  WITH (DATA_COMPRESSION = ROW);
GO
```

次の例では、インデックスのすべてのパーティションに行の圧縮を使用して、パーティション テーブルのインデックスを作成します。

```sql
CREATE CLUSTERED INDEX IX_PartTab2Col1
  ON PartitionTable1 (Col1)
  WITH (DATA_COMPRESSION = ROW);
GO
```

 次の例では、インデックスのパーティション `1` にページの圧縮を、パーティション `2` から `4` までに行の圧縮を使用して、パーティション テーブルのインデックスを作成します。

```sql
CREATE CLUSTERED INDEX IX_PartTab2Col1
  ON PartitionTable1 (Col1)
  WITH (
    DATA_COMPRESSION = PAGE ON PARTITIONS(1),
    DATA_COMPRESSION = ROW ON PARTITIONS (2 TO 4)
  );
GO
```

### <a name="m-create-resume-pause-and-abort-resumable-index-operations"></a>M. 再開可能なインデックス操作を作成、再開、一時停止、中止する
**適用対象**:[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (開始値 [!INCLUDE[sql-server-2019](../../includes/sssql19-md.md)]) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]

```sql
-- Execute a resumable online index create statement with MAXDOP=1
CREATE INDEX test_idx1 ON test_table (col1) WITH (ONLINE = ON, MAXDOP = 1, RESUMABLE = ON);

-- Executing the same command again (see above) after an index operation was paused, resumes automatically the index create operation.

-- Execute a resumable online index creates operation with MAX_DURATION set to 240 minutes. After the time expires, the resumable index create operation is paused.
CREATE INDEX test_idx2 ON test_table (col2) WITH (ONLINE = ON, RESUMABLE = ON, MAX_DURATION = 240);

-- Pause a running resumable online index creation
ALTER INDEX test_idx1 ON test_table PAUSE;
ALTER INDEX test_idx2 ON test_table PAUSE;

-- Resume a paused online index creation
ALTER INDEX test_idx1 ON test_table RESUME;
ALTER INDEX test_idx2 ON test_table RESUME;

-- Abort resumable index create operation which is running or paused
ALTER INDEX test_idx1 ON test_table ABORT;
ALTER INDEX test_idx2 ON test_table ABORT;
```

## <a name="examples-sssdwfull-and-sspdw"></a>例: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)]、[!INCLUDE[ssPDW](../../includes/sspdw-md.md)]

### <a name="n-basic-syntax"></a>北 基本構文
再開可能なインデックス操作を作成、再開、一時停止、中止する       

**適用対象**:[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (開始値 [!INCLUDE[sql-server-2019](../../includes/sssql19-md.md)]) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]

```sql
-- Execute a resumable online index create statement with MAXDOP=1
CREATE INDEX test_idx ON test_table WITH (ONLINE = ON, MAXDOP = 1, RESUMABLE = ON);

-- Executing the same command again (see above) after an index operation was paused, resumes automatically the index create operation.

-- Execute a resumable online index creates operation with MAX_DURATION set to 240 minutes. After the time expires, the resumable index create operation is paused.
CREATE INDEX test_idx ON test_table WITH (ONLINE = ON, RESUMABLE = ON, MAX_DURATION = 240);

-- Pause a running resumable online index creation
ALTER INDEX test_idx ON test_table PAUSE;

-- Resume a paused online index creation
ALTER INDEX test_idx ON test_table RESUME;

-- Abort resumable index create operation which is running or paused
ALTER INDEX test_idx ON test_table ABORT;
```

### <a name="o-create-a-nonclustered-index-on-a-table-in-the-current-database"></a>O. 現在のデータベース内のテーブルに非クラスター化インデックスを作成する
次の例では、`ProductVendor` テーブルの `VendorID` 列に非クラスター化インデックスを作成します。

```sql
CREATE INDEX IX_ProductVendor_VendorID
  ON ProductVendor (VendorID);
```

### <a name="p-create-a-clustered-index-on-a-table-in-another-database"></a>P. 他のデータベースのテーブルにクラスター化インデックスを作成する
次の例では、`Purchasing` データベースにある `ProductVendor` テーブルの `VendorID` 列に非クラスター化インデックスを作成します。

```sql
CREATE CLUSTERED INDEX IX_ProductVendor_VendorID
  ON Purchasing..ProductVendor (VendorID);
```
### <a name="q-create-an-ordered-clustered-index-on-a-table"></a>Q. 順序を付けてクラスター化したインデックスをテーブルで作成する  
次の例では、順序を付けてクラスター化したインデックスが `MyDB` データベースの `T1` テーブルの `c1` 列と `c2` 列で作成されます。

```sql
CREATE CLUSTERED COLUMNSTORE INDEX MyOrderedCCI ON MyDB.dbo.T1 
ORDER (c1, c2);

```

### <a name="r-convert-a-cci-to-an-ordered-clustered-index-on-a-table"></a>R. 順序を付けてクラスター化したインデックスに CCI をテーブルで変換する  
次の例では、クラスター化された既存の列ストア インデックスが、`MyDB` データベースの `T2` テーブルの `c1` 列と `c2` 列で、順序を付けてクラスター化した、`MyOrderedCCI` という名称の列ストア インデックスに変換されます。

```sql
CREATE CLUSTERED COLUMNSTORE INDEX MyOrderedCCI ON MyDB.dbo.T2
ORDER (c1, c2)
WITH (DROP_EXISTING = ON);

```

## <a name="see-also"></a>参照
[SQL Server のインデックスのアーキテクチャとデザイン ガイド](../../relational-databases/sql-server-index-design-guide.md)     
[オンラインでのインデックス操作の実行](../../relational-databases/indexes/perform-index-operations-online.md)  
[インデックスと ALTER TABLE](../../t-sql/statements/alter-table-transact-sql.md#indexes-and-alter-table)     
[ALTER INDEX](../../t-sql/statements/alter-index-transact-sql.md)     
[CREATE PARTITION FUNCTION](../../t-sql/statements/create-partition-function-transact-sql.md)     
[CREATE PARTITION SCHEME](../../t-sql/statements/create-partition-scheme-transact-sql.md)     
[CREATE SPATIAL INDEX](../../t-sql/statements/create-spatial-index-transact-sql.md)      
[CREATE STATISTICS](../../t-sql/statements/create-statistics-transact-sql.md)     
[CREATE TABLE](../../t-sql/statements/create-table-transact-sql.md)    
[CREATE XML INDEX](../../t-sql/statements/create-xml-index-transact-sql.md)     
[データ型](../../t-sql/data-types/data-types-transact-sql.md)    
[DBCC SHOW_STATISTICS](../../t-sql/database-console-commands/dbcc-show-statistics-transact-sql.md)    
[DROP INDEX](../../t-sql/statements/drop-index-transact-sql.md)    
[XML インデックス &#40;SQL Server&#41;](../../relational-databases/xml/xml-indexes-sql-server.md)     
[sys.indexes](../../relational-databases/system-catalog-views/sys-indexes-transact-sql.md)     
[sys.index_columns](../../relational-databases/system-catalog-views/sys-index-columns-transact-sql.md)    
[sys.xml_indexes](../../relational-databases/system-catalog-views/sys-xml-indexes-transact-sql.md)     
[EVENTDATA](../../t-sql/functions/eventdata-transact-sql.md)
