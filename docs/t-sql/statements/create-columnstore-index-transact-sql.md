---
description: CREATE COLUMNSTORE INDEX (Transact-SQL)
title: CREATE COLUMNSTORE INDEX (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 09/25/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- CREATE_COLUMNSTORE_INDEX_TSQL
- COLUMNSTORE INDEX
- COLUMNSTORE_INDEX_TSQL
- CREATE CLUSTERED COLUMNSTORE INDEX
- COLUMNSTORE_TSQL
- CREATE NONCLUSTERED COLUMNSTORE INDEX
- CREATE_NONCLUSTERED_COLUMNSTORE_INDEX_TSQL
- CREATE COLUMNSTORE INDEX
- CREATE_CLUSTERED_COLUMNSTORE_INDEX_TSQL
- COLUMNSTORE
dev_langs:
- TSQL
helpviewer_keywords:
- index creation [SQL Server], columnstore indexes
- columnstore index, creating
- CREATE COLUMNSTORE INDEX statement
- CREATE INDEX statement
ms.assetid: 7e1793b3-5383-4e3d-8cef-027c0c8cb5b1
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: f1cd141fff964f13f4bbbae7bcd71bc1ee31694f
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104753392"
---
# <a name="create-columnstore-index-transact-sql"></a>CREATE COLUMNSTORE INDEX (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

行ストア テーブルをクラスター化された列ストア インデックスに変換するか、クラスター化されていない列ストア インデックスを作成します。 OLTP ワークロードに対して効率的に運用分析をリアルタイムで実行するには、またはデータ ウェアハウスに対するワークロードのデータ圧縮とクエリのパフォーマンスを向上させるには、列ストア インデックスを使用します。  
  
> [!NOTE]
> [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 以降、テーブルをクラスター化列ストア インデックスとして作成できます。   最初に行ストア テーブルを作成し、それをクラスター化列ストア インデックスに変換する作業は不要になりました。  

> [!TIP]
> インデックスの設計のガイドラインについては、「[SQL Server インデックス デザイン ガイド](../../relational-databases/sql-server-index-design-guide.md)」をご覧ください。

次の例に進みます。  
-   [行ストア テーブルを列ストアに変換する例](../../t-sql/statements/create-columnstore-index-transact-sql.md#convert)  
-   [非クラスター化列ストア インデックスの例](../../t-sql/statements/create-columnstore-index-transact-sql.md#nonclustered)  
  
シナリオに移動:  
-   [リアルタイム運用分析の列ストア インデックス](../../relational-databases/indexes/get-started-with-columnstore-for-real-time-operational-analytics.md)  
-   [データ ウェアハウスの列ストア インデックス](../../relational-databases/indexes/columnstore-indexes-data-warehouse.md)  
  
詳細情報:  
-   [列ストア インデックス ガイド](../../relational-databases/indexes/columnstore-indexes-overview.md)  
-   [列ストア インデックスの機能の概要](../../relational-databases/indexes/columnstore-indexes-what-s-new.md)  
  
![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql
-- Syntax for SQL Server and Azure SQL Database  
  
-- Create a clustered columnstore index on disk-based table.  
CREATE CLUSTERED COLUMNSTORE INDEX index_name  
    ON { database_name.schema_name.table_name | schema_name.table_name | table_name }  
    [ WITH ( < with_option> [ ,...n ] ) ]  
    [ ON <on_option> ] 
[ ; ]  
  
--Create a nonclustered columnstore index on a disk-based table.  
CREATE [NONCLUSTERED]  COLUMNSTORE INDEX index_name   
    ON { database_name.schema_name.table_name | schema_name.table_name | table_name }
        ( column  [ ,...n ] )  
    [ WHERE <filter_expression> [ AND <filter_expression> ] ]
    [ WITH ( < with_option> [ ,...n ] ) ]  
    [ ON <on_option> ]
[ ; ]  
  
<with_option> ::=  
      DROP_EXISTING = { ON | OFF } -- default is OFF  
    | MAXDOP = max_degree_of_parallelism 
    | ONLINE = { ON | OFF } 
    | COMPRESSION_DELAY  = { 0 | delay [ Minutes ] }  
    | DATA_COMPRESSION = { COLUMNSTORE | COLUMNSTORE_ARCHIVE }  
      [ ON PARTITIONS ( { partition_number_expression | range } [ ,...n ] ) ]  
  
<on_option>::=  
      partition_scheme_name ( column_name )
    | filegroup_name
    | "default"
  
<filter_expression> ::=  
      column_name IN ( constant [ ,...n ]  
    | column_name { IS | IS NOT | = | <> | != | > | >= | !> | < | <= | !< } constant  
  
```  
  
```syntaxsql
-- Syntax for Azure Synapse Analytics and Parallel Data Warehouse  
  
CREATE CLUSTERED COLUMNSTORE INDEX index_name
    ON { database_name.schema_name.table_name | schema_name.table_name | table_name } 
    [ORDER (column [,...n] ) ]  
    [ WITH ( DROP_EXISTING = { ON | OFF } ) ] --default is OFF  
[;]  

```
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数

一部のオプションは、すべてのデータベース エンジンのバージョンで使用できません。 次の表では、CLUSTERED COLUMNSTORE インデックスおよび NONCLUSTERED COLUMNSTORE インデックスに導入されているオプションのバージョンを示します。

|オプション| CLUSTERED | NONCLUSTERED |
|---|---|---|
| COMPRESSION_DELAY | [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] | [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] |
| DATA_COMPRESSION | [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] | [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] | 
| ONLINE | [!INCLUDE[ssSQLv15_md](../../includes/sssql19-md.md)] | [!INCLUDE [sssql17-md](../../includes/sssql17-md.md)] |
| WHERE 句 | 該当なし | [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] |

すべてのオプションは、Azure SQL Database で使用できます。

### <a name="create-clustered-columnstore-index"></a>CREATE CLUSTERED COLUMNSTORE INDEX

すべてのデータが列ごとに圧縮されて格納されるクラスター化列ストア インデックスを作成します。 インデックスにはテーブル内の列がすべて含まれ、テーブル全体が格納されます。 既存のテーブルがヒープまたはクラスター化インデックスである場合、そのテーブルはクラスター化列ストア インデックスに変換されます。 テーブルが既にクラスター化列ストア インデックスとして格納されている場合、既存のインデックスは削除され、再構築されます。  
  
*index_name*  
新しいインデックスの名前を指定します。  
  
テーブルに既にクラスター化列ストア インデックスがある場合、既存のインデックスとして、同じ名前を指定するか、DROP EXISTING オプションを使用して新しい名前を指定します。  
  
ON [*database_name*. [*schema_name* ] . | *schema_name* . ] *table_name*

クラスター化列ストア インデックスとして格納するテーブルの 1 部、2 部、または 3 部構成の名前を指定します。 テーブルがヒープかクラスター化インデックスの場合、テーブルは行ストアから列ストアに変換されます。 テーブルが既に列ストアである場合、このステートメントでクラスター化列ストア インデックスが再構築されます。 順序付けされたクラスター化列ストア インデックスに変換するには、、既存のインデックスがクラスター化列ストア インデックスである必要があります。
  
#### <a name="with-options"></a>WITH オプション

##### <a name="drop_existing--off--on"></a>DROP_EXISTING = [OFF] | ON

   `DROP_EXISTING = ON` の場合、既存のインデックスを削除し、新しい列ストア インデックスを作成します。  
```sql
CREATE CLUSTERED COLUMNSTORE INDEX cci ON Sales.OrderLines
       WITH (DROP_EXISTING = ON);
```
   既定の DROP_EXISTING = OFF では、既存の名前と同じインデックス名が期待されます。 指定したインデックス名が既に存在するは、エラーが発生します。  
  
##### <a name="maxdop--max_degree_of_parallelism"></a>MAXDOP = *max_degree_of_parallelism*  
   インデックス操作の間、既存のサーバーの最大並列度構成をオーバーライドします。 並列プランの実行で使用されるプロセッサ数を制限するには、MAXDOP を使用します。 最大数は 64 プロセッサです。  
  
   *max_degree_of_parallelism* 値に指定できる値:  
   - 1 - 並列プラン生成を抑制します。  
   - \>1 - 現在のシステム ワークロードに基づいて、並列インデックス操作で使用される最大プロセッサ数を指定の数以下に制限します。 たとえば、MAXDOP が 4 の場合、使用されるプロセッサの数は 4 以下になります。  
   - 0 (既定) - 現在のシステム ワークロードに基づいて、実際の数以下のプロセッサを使用します。  
  
```sql
CREATE CLUSTERED COLUMNSTORE INDEX cci ON Sales.OrderLines
       WITH (MAXDOP = 2);
```

   詳細については、「[max degree of parallelism サーバー構成オプションの構成](../../database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option.md) 」と「 [並列インデックス操作の構成](../../relational-databases/indexes/configure-parallel-index-operations.md)」を参照してください。  
 
###### <a name="compression_delay--0--delay--minutes-"></a>COMPRESSION_DELAY = **0** | *delay* [ Minutes ]  
   ディスク ベースのテーブルの場合は、CLOSED 状態のデルタ行グループがそのデルタ行グループに留まる必要がある最低限の分数が *遅延* によって指定され、その時間が経過すると、SQL Server は行グループを、圧縮された行グループに圧縮できるようになります。 ディスク ベース テーブルでは個々の行において挿入時間および更新時間が追跡されないため、SQL Server は CLOSED 状態のデルタ行グループに遅延を適用します。  
   既定値は、0 分です。  
   
```sql
CREATE CLUSTERED COLUMNSTORE INDEX cci ON Sales.OrderLines
       WITH ( COMPRESSION_DELAY = 10 Minutes );
```

   COMPRESSION_DELAY を使用する場合の推奨事項については、「[列ストアを使用したリアルタイム運用分析の概要](../../relational-databases/indexes/get-started-with-columnstore-for-real-time-operational-analytics.md)」をご覧ください。  
  
##### <a name="data_compression--columnstore--columnstore_archive"></a>DATA_COMPRESSION = COLUMNSTORE | COLUMNSTORE_ARCHIVE  
   指定したテーブル、パーティション番号、またはパーティション範囲に、データ圧縮オプションを指定します。 次のようなオプションがあります。   
- `COLUMNSTORE` は既定で、最もパフォーマンスの高い列ストア圧縮を使用して圧縮します。 これは、一般的な選択です。  
- `COLUMNSTORE_ARCHIVE` では、さらにテーブルまたはパーティションを小さいサイズに圧縮します。 このオプションは、保存に必要なストレージ サイズが少なく、保存と取得の速度を落とすことが可能な状況などで使用できます。  
  
```sql
CREATE CLUSTERED COLUMNSTORE INDEX cci ON Sales.OrderLines
       WITH ( DATA_COMPRESSION = COLUMNSTORE_ARCHIVE );
```
   圧縮の詳細については、「[データ圧縮](../../relational-databases/data-compression/data-compression.md)」を参照してください。  

###### <a name="online--on--off"></a>ONLINE = [ON | OFF]
- `ON` の場合、インデックスの新しいコピーが構築されている間、列ストア インデックスはオンラインのままで、利用可能です。
- `OFF` の場合、新しいコピーが構築されている間、インデックスは使用できません。

```sql
CREATE CLUSTERED COLUMNSTORE INDEX cci ON Sales.OrderLines
       WITH ( ONLINE = ON );
```

#### <a name="on-options"></a>ON オプション 
   ON オプションを使用すると、パーティション構成、特定のファイル グループ、既定のファイル グループなど、データ ストレージのオプションを指定できます。 ON オプションを指定しない場合、インデックスでは、既存のテーブルの設定パーティションまたはファイル グループ設定が使用されます。  
  
   *partition_scheme_name* **(** _column_name_ **)**  
   テーブルのパーティション構成を指定します。 このパーティション構成は既にデータベースに存在している必要があります。 パーティション構成を作成するには、「[CREATE PARTITION SCHEME](../../t-sql/statements/create-partition-scheme-transact-sql.md)」をご覧ください。  
 
   *column_name* には、パーティション インデックスがパーティション分割される対象の列を指定します。 この列は、*partition_scheme_name* で使用されているパーティション関数の引数のデータ型、長さ、有効桁数に一致する必要があります。  

   *filegroup_name*  
   クラスター化列ストア インデックスを格納するファイル グループを指定します。 位置の指定がなく、テーブルがパーティション分割されていない場合は、基になるテーブルまたはビューと同じファイル グループが使用されます。 ファイル グループは既に存在している必要があります。  

   **"** default **"**  
   既定のファイル グループにインデックスを作成するには、"default" または [ default ] を使用します。  
  
   "default" を指定する場合は、現在のセッションに対して QUOTED_IDENTIFIER オプションが ON である必要があります。 QUOTED_IDENTIFIER は既定で ON です。 詳細については、「[SET QUOTED_IDENTIFIER &#40;Transact-SQL&#41;](../../t-sql/statements/set-quoted-identifier-transact-sql.md)」をご覧ください。  
  
### <a name="create-nonclustered-columnstore-index"></a>CREATE [NONCLUSTERED] COLUMNSTORE INDEX  
行ストア テーブルに、ヒープまたはクラスター化インデックスとして格納されるメモリ内の非クラスター化列ストア インデックスを作成します。 このインデックスにはフィルター条件を持たせることができ、基になるテーブルの列のすべてを含める必要はありません。 列ストア インデックスでは、データのコピーの保存に十分な領域が必要です。 更新可能であり、基になるテーブルが変更されると更新されます。 クラスター化インデックス上の非クラスター化列ストア インデックスでは、リアルタイム分析が可能です。  
  
*index_name*  
   インデックスの名前を指定します。 *index_name* はテーブル内で一意にする必要がありますが、データベース内で一意である必要はありません。 インデックス名は、[識別子](../../relational-databases/databases/database-identifiers.md)の規則に従っている必要があります。  
  
 **(** _column_  [ **,** ...*n* ] **)**  
    格納する列を指定します。 非クラスター化列ストア インデックスの列の上限は、1024 です。  
   各列のデータ型は、列ストア インデックスでサポートされているものである必要があります。 サポートされるデータ型の一覧については、「[制限事項と制約事項](../../t-sql/statements/create-columnstore-index-transact-sql.md#LimitRest)」を参照してください。  

ON [*database_name*. [*schema_name* ] . | *schema_name* . ] *table_name*  
   インデックスが含まれているテーブルの 1 部、2 部、または 3 部構成の名前を指定します。  

#### <a name="with-options"></a>WITH オプション
##### <a name="drop_existing--off--on"></a>DROP_EXISTING = [OFF] | ON  
   DROP_EXISTING = ON 既存のインデックスは削除され、再構築されます。 指定するインデックス名は、現在存在するインデックスと同じにする必要がありますが、インデックス定義は変更できます。 たとえば、別の列またはインデックス オプションを指定できます。
  
   DROP_EXISTING = OFF 指定するインデックス名が既に存在する場合、エラーが表示されます。 DROP_EXISTING を使用してインデックスの種類を変更することはできません。 旧バージョンと互換性のある構文では、WITH DROP_EXISTING は WITH DROP_EXISTING = ON と同じです。  

###### <a name="maxdop--max_degree_of_parallelism"></a>MAXDOP = *max_degree_of_parallelism*  
   インデックス操作の間、[max degree of parallelism サーバー構成オプション](../../database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option.md)更新オプションをオーバーライドします。 並列プランの実行で使用されるプロセッサ数を制限するには、MAXDOP を使用します。 最大数は 64 プロセッサです。  
  
   *max_degree_of_parallelism* 値に指定できる値:  
   - 1 - 並列プラン生成を抑制します。  
   - \>1 - 現在のシステム ワークロードに基づいて、並列インデックス操作で使用される最大プロセッサ数を指定の数以下に制限します。 たとえば、MAXDOP が 4 の場合、使用されるプロセッサの数は 4 以下になります。  
   - 0 (既定) - 現在のシステム ワークロードに基づいて、実際の数以下のプロセッサを使用します。  
  
   詳細については、「 [並列インデックス操作の構成](../../relational-databases/indexes/configure-parallel-index-operations.md)」を参照してください。  
  
> [!NOTE]
>  並列インデックス操作は、[!INCLUDE[msC](../../includes/msconame-md.md)][!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のすべてのエディションで使用できるわけではありません。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の各エディションでサポートされる機能の一覧については、「[Editions and Supported Features for SQL Server 2016](../../sql-server/editions-and-components-of-sql-server-2016.md)」 (SQL Server 2016 のエディションとサポートされる機能) を参照してください。  
  
###### <a name="online--on--off"></a>ONLINE = [ON | OFF]   
- `ON` の場合、インデックスの新しいコピーが構築されている間、列ストア インデックスはオンラインのままで、利用可能です。
- `OFF` の場合、新しいコピーが構築されている間、インデックスは使用できません。 非クラスター化インデックスでは、ベース テーブルは利用できます。新しいインデックスが完成するまで、非クラスター化インデックスのみクエリの応答に利用されません。 

```sql
CREATE COLUMNSTORE INDEX ncci ON Sales.OrderLines (StockItemID, Quantity, UnitPrice, TaxRate) WITH ( ONLINE = ON );
```

##### <a name="compression_delay--0--delayminutes"></a>COMPRESSION_DELAY = **0** | \<delay>[Minutes]  
   行がデルタ行グループに残る期間の下限を指定します。この下限までは、圧縮された行グループに移行できます。 たとえば、行が 120 分間変更されない場合、単票格納形式に圧縮するという顧客がいるかもしれません。 ディスクベース テーブルの列ストア インデックスの場合、行が挿入または更新された時刻は追跡しません。代わりに、行のプロキシとして、デルタ行グループの終了時刻を利用します。 既定の継続時間は 0 分です。 100 万行がデルタ行グループに累積されると、1 行が単票格納に移行されます。その行に終了の印が付きます。  
  
###### <a name="data_compression"></a>DATA_COMPRESSION  
   指定したテーブル、パーティション番号、またはパーティション範囲に、データ圧縮オプションを指定します。 非クラスター化列ストアとクラスター化列ストア インデックスの両方を含む列ストア インデックスにのみ適用されます。 次のようなオプションがあります。
   
- 既定の `COLUMNSTORE` では、最もパフォーマンスの高い列ストア圧縮を使用して圧縮します。 これは、一般的な選択です。  
- `COLUMNSTORE_ARCHIVE` COLUMNSTORE_ARCHIVE は、テーブルまたはパーティション サイズをより小さなサイズに圧縮します。 これは、アーカイブ用や、ストレージのサイズを減らす必要があり、かつ保存と取得に時間をかける余裕があるその他の状況で使用できます。  
  
 圧縮の詳細については、「[データ圧縮](../../relational-databases/data-compression/data-compression.md)」を参照してください。  
  
##### <a name="where-filter_expression--and-filter_expression-"></a>WHERE \<filter_expression> [ AND \<filter_expression> ]
  
   フィルター述語が呼び出されると、インデックスに含める行を指定します。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] は、フィルター選択されたインデックスのデータ行で、フィルター選択された統計情報を作成します。  
  
   フィルター述語では、単純な比較ロジックが使用されます。 比較演算子では、NULL リテラルを使用する比較を実行できません。 代わりに、IS NULL 演算子と IS NOT NULL 演算子を使用します。  
  
   次に、`Production.BillOfMaterials` テーブルのフィルター述語の例をいくつか示します。  
   `WHERE StartDate > '20000101' AND EndDate <= '20000630'`    
   `WHERE ComponentID IN (533, 324, 753)`  
   `WHERE StartDate IN ('20000404', '20000905') AND EndDate IS NOT NULL`  
   
   フィルター選択されたインデックスについては、「[フィルター選択されたインデックスの作成](../../relational-databases/indexes/create-filtered-indexes.md)」を参照してください。  
  
#### <a name="on-options"></a>ON オプション  
   これらのオプションによって、インデックスが作成されるファイル グループが指定されます。  
  
*partition_scheme_name* **(** _column_name_ **)**  
   ファイル グループを定義するパーティション構成を指定します。このファイル グループは、パーティション インデックスのパーティションのマップ先となります。 [CREATE PARTITION SCHEME](../../t-sql/statements/create-partition-scheme-transact-sql.md) を実行し、パーティション構成がデータベース内に存在するようにする必要があります。 
   *column_name* には、パーティション インデックスがパーティション分割される対象の列を指定します。 この列は、*partition_scheme_name* で使用されているパーティション関数の引数のデータ型、長さ、有効桁数に一致する必要があります。 *column_name* は、インデックス定義で指定されている列に限定されません。 列ストア インデックスをパーティション分割するとき、[!INCLUDE[ssDE](../../includes/ssde-md.md)]では、まだ指定されていない場合、パーティション分割列がインデックスの列として追加されます。  
   *partition_scheme_name* または *filegroup* が指定されないまま、テーブルがパーティション分割されると、インデックスは基になるテーブルと同じパーティション分割列を使用して、同じパーティション構造に配置されます。  
   パーティション テーブルの列ストア インデックスは、パーティション固定にする必要があります。  
   インデックスのパーティション分割の詳細については、「[パーティション テーブルとパーティション インデックス](../../relational-databases/partitions/partitioned-tables-and-indexes.md)」を参照してください。  

*filegroup_name*  
   インデックスを作成するファイル グループ名を指定します。 *filegroup_name* の指定がなく、テーブルがパーティション分割されていない場合は、基になるテーブルと同じファイル グループが使用されます。 ファイル グループは既に存在している必要があります。  
 
**"** default **"**  
既定のファイル グループに、指定したインデックスを作成します。  
  
このコンテキストでの default という用語はキーワードではありません。 default は、既定ファイル グループの識別子なので、ON **"** default **"** または ON **[** default **]** のように区切る必要があります。 "default" を指定する場合は、現在のセッションに対して QUOTED_IDENTIFIER オプションが ON である必要があります。 これが既定の設定です。 詳細については、「[SET QUOTED_IDENTIFIER &#40;Transact-SQL&#41;](../../t-sql/statements/set-quoted-identifier-transact-sql.md)」をご覧ください。  
  
##  <a name="permissions"></a><a name="Permissions"></a> Permissions  
 テーブルに対する ALTER 権限が必要です。  
  
##  <a name="general-remarks"></a><a name="GenRemarks"></a> 全般的な解説  
列ストア インデックスは一時テーブルに作成できます。 テーブルが削除されるかセッションが終了すると、インデックスも削除されます。  

順序付けされたクラスター化列ストア インデックスは、文字列型の列を除く [!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] でサポートされている任意のデータ型の列に作成できます。  
 
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
  
     *ANSI_WARNINGS を ON に設定すると、データベース互換性レベルが 90 以上に設定されている場合、暗黙的に ARITHABORT が ON に設定されます。 データベース互換性レベルが 80 以下に設定されている場合は、ARITHABORT オプションを明示的に ON に設定する必要があります。  
  
 SET オプションが正しくないと、次の状態が発生する場合があります。  
  
-   フィルター選択されたインデックスが作成されません。  
  
-   [!INCLUDE[ssDE](../../includes/ssde-md.md)] によりエラーが生成され、インデックスのデータを変更していた INSERT ステートメント、UPDATE ステートメント、DELETE ステートメント、または MERGE ステートメントがロールバックされます。  
  
-   Transact-SQL ステートメントの実行プランで、クエリ オプティマイザーがインデックスを無視します。  
  
 フィルター選択されたインデックスについて詳しくは、「[フィルター選択されたインデックスの作成](../../relational-databases/indexes/create-filtered-indexes.md)」をご覧ください。 
  
##  <a name="limitations-and-restrictions"></a><a name="LimitRest"></a> 制限事項と制約事項  

**列ストア インデックスの各列は、次の一般的なビジネス データ型のいずれかである必要があります。** 
-   datetimeoffset [ ( *n* ) ]  
-   datetime2 [ ( *n* ) ]  
-   DATETIME  
-   smalldatetime  
-   date  
-   time [ ( *n* ) ]  
-   float [ ( *n* ) ]  
-   real [ ( *n* ) ]  
-   decimal [ ( *precision* [ *, scale* ] **)** ]
-   numeric [ ( *precision* [ *, scale* ] **)** ]    
-   money  
-   smallmoney  
-   bigint  
-   INT  
-   smallint  
-   tinyint  
-   bit  
-   nvarchar [ ( *n* ) ] 
-   nvarchar(max)  (クラスタ化列ストア インデックスのみの [!INCLUDE [sssql17-md](../../includes/sssql17-md.md)] および Premium 層、Standard 層 (S3 以上)、すべての VCore サービス層に適用されます)   
-   nchar [ ( *n* ) ]  
-   varchar [ ( *n* ) ]  
-   varchar(max)  (クラスタ化列ストア インデックスのみの [!INCLUDE [sssql17-md](../../includes/sssql17-md.md)] および Premium 層、Standard 層 (S3 以上)、すべての VCore サービス層に適用されます)
-   char [ ( *n* ) ]  
-   varbinary [ ( *n* ) ] 
-   varbinary (max)  (クラスタ化列ストア インデックスのみの [!INCLUDE [sssql17-md](../../includes/sssql17-md.md)] および Premium 層、Standard 層 (S3 以上)、すべての VCore サービス層の Azure SQL Database に適用されます)
-   binary [ ( *n* ) ]  
-   uniqueidentifier ([!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降に適用)
  
基になるテーブルに列ストア インデックスでサポートされていないデータ型の列がある場合は、非クラスター化列ストア インデックスからは、その列を省く必要があります。  
  
**以下のいずれかのデータ型を使用する列は、列ストア インデックスに含めることができません。**
-   ntext、text、image  
-   nvarchar(max)、varchar(max)、varbinary(max) ([!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 以前のバージョンに適用、非クラスター化列ストア インデックス) 
-   rowversion (timestamp)  
-   sql_variant  
-   CLR 型 (hierarchyid 型および空間型)  
-   xml  
-   uniqueidentifier ([!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] に適用)  

**非クラスター化列ストア インデックス**
-   1,024 より多い列を持つことはできません。
-   制約ベースのインデックスとして作成することはできません。 列ストア インデックスを持つテーブルには、一意の制約、主キー制約、外部キー制約を含めることができます。 制約は常に行ストア インデックスで適用されます。 列ストア (クラスター化または非クラスター化) インデックスで制約を適用することはできません。
-   スパース列を含めることはできません。  
-   **ALTER INDEX** ステートメントを使用して変更することはできません。 非クラスター化インデックスを変更するには、代わりに列ストア インデックスを削除してから再作成する必要があります。 **ALTER INDEX** を使用し、列ストア インデックスを無効にし、再構築できます。  
-   **INCLUDE** キーワードを使用して作成することはできません。  
-   インデックスを並べ替えるための **ASC** または **DESC** キーワードを含めることはできません。 列ストア インデックスは、圧縮アルゴリズムに従って順序付けされます。 並べ替えを行うと、パフォーマンス上の利点の多くが無効になります。  
-   非クラスター化列ストア インデックスに型が nvarchar(max)、varchar(max)、varbinary(max) のラージ オブジェクト (LOB) 列を含めることはできません。 Premium 層、Standard 層 (S3 以上)、およびすべての VCore サービス層で構成されている [!INCLUDE [sssql17-md](../../includes/sssql17-md.md)] バージョンと Azure SQL Database 以降で、クラスター化列ストア インデックスのみ LOB 型をサポートしています。 以前のバージョンでは、クラスター化列ストア インデックスと非クラスター化列ストア インデックスで LOB 型をサポートしていません。


> [!NOTE]  
> [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 以降では、インデックス付きビューに対して非クラスター化列ストア インデックスを作成できます。  


 **列ストア インデックスと同時に使用できない機能:**  
-   計算列。 SQL Server 2017 以降、クラスター化列ストア インデックスに、保存されない計算列を含めることができます。 ただし、SQL Server 2017 では、クラスター化列ストア インデックスに、保存される計算列を含めることができません。計算列で非クラスター化インデックスを作成することはできません。 
-   ページと行の圧縮、**vardecimal** ストレージ形式 (列ストア インデックスは既に別の形式で圧縮されているため)。  
-   レプリケーション  
-   Filestream

クラスター化列ストア インデックスを使用しているテーブルでは、カーソルやトリガーは使用できません。 この制限は、非クラスター化列ストア インデックスには適用されません。非クラスター化列ストア インデックスを使用しているテーブルでは、カーソルとトリガーを使用できます。

**[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] に固有の制限事項**  
これらの制限は [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] にのみ適用されます。 このリリースでは、更新可能なクラスター化列ストア インデックスを導入しました。 非クラスター化列ストア インデックスは引き続き読み取り専用でした。  

-   変更の追跡。 列ストア インデックスで変更履歴を使用することはできません。  
-   変更データ キャプチャ。 クラスター化列ストア インデックスを持つテーブルでは、変更データ キャプチャを有効にできません。 SQL Server 2016 以降では、非クラスター化列ストア インデックスを持つテーブルで有効にすることができます。  
-   読み取り可能セカンダリ。 AlwaysOn 可用性グループの読み取り可能セカンダリからクラスター化列ストア インデックス (CCI) にアクセスすることはできません。  読み取り可能セカンダリから非クラスター化列ストア インデックス (NCCI) にアクセスできます。  
-   複数のアクティブな結果セット (MARS)。 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] では、列ストア インデックスを含むテーブルに読み取り専用で接続するために、MARS が使用されます。 ただし、[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] では、列ストア インデックスを含むテーブルで DML (データ操作言語) を同時操作する場合、MARS を利用できません。 この場合、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] は接続を強制終了し、トランザクションを中止します。  
-  ビューまたはインデックス付きビューに対して非クラスター化列ストア インデックスを作成することはできません。
  
 列ストア インデックスのパフォーマンス上の利点と制限の詳細については、「[列ストア インデックス - 概要](../../relational-databases/indexes/columnstore-indexes-overview.md)」をご覧ください。
  
##  <a name="metadata"></a><a name="Metadata"></a> メタデータ  
 列ストア インデックス内のすべての列は、付加列としてメタデータに格納されます。 列ストア インデックスにキー列はありません。 列ストア インデックスに関する情報は、次のシステム ビューによって提供されます。  
  
-   [sys.indexes &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-indexes-transact-sql.md)  
-   [sys.index_columns &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-index-columns-transact-sql.md)  
-   [sys.partitions &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-partitions-transact-sql.md)  
-   [sys.column_store_segments &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-column-store-segments-transact-sql.md)  
-   [sys.column_store_dictionaries &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-column-store-dictionaries-transact-sql.md)  
-   [sys.column_store_row_groups &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-column-store-row-groups-transact-sql.md)  

##  <a name="examples-for-converting-a-rowstore-table-to-columnstore"></a><a name="convert"></a> 行ストア テーブルを列ストアに変換する例  
  
### <a name="a-convert-a-heap-to-a-clustered-columnstore-index"></a>A. ヒープをクラスター化列ストア インデックスに変換する  
 この例では、テーブルをヒープとして作成してから、cci_Simple という名前のクラスター化 columnstore インデックスに変換します。 こうすることで、テーブル全体のストレージが行ストアから列ストアに変更されます。  
  
```sql  
CREATE TABLE SimpleTable(  
    ProductKey [INT] NOT NULL,   
    OrderDateKey [INT] NOT NULL,   
    DueDateKey [INT] NOT NULL,   
    ShipDateKey [INT] NOT NULL);  
GO  
CREATE CLUSTERED COLUMNSTORE INDEX cci_Simple ON SimpleTable;  
GO  
```  
  
### <a name="b-convert-a-clustered-index-to-a-clustered-columnstore-index-with-the-same-name"></a>B. クラスター化インデックスを同じ名前のクラスター化列ストア インデックスに変換する。  
 この例では、クラスター化インデックスを持つテーブルを作成し、クラスター化インデックスをクラスター化列ストア インデックスに変換する構文を示します。 こうすることで、テーブル全体のストレージが行ストアから列ストアに変更されます。  
  
```sql  
CREATE TABLE SimpleTable (  
    ProductKey [INT] NOT NULL,   
    OrderDateKey [INT] NOT NULL,   
    DueDateKey [INT] NOT NULL,   
    ShipDateKey [INT] NOT NULL);  
GO  
CREATE CLUSTERED INDEX cl_simple ON SimpleTable (ProductKey);  
GO  
CREATE CLUSTERED COLUMNSTORE INDEX cl_simple ON SimpleTable  
WITH (DROP_EXISTING = ON);  
GO  
```  
  
### <a name="c-handle-nonclustered-indexes-when-converting-a-rowstore-table-to-a-columnstore-index"></a>C. 行ストア テーブルを列ストア インデックスに変換するときに、非クラスター化インデックスを処理する。  
 この例では、行ストア テーブルを列ストア インデックスに変換するときに、非クラスター化インデックスを処理する方法を示します。 実際には、以降では、[!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 特別な操作は不要です。[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] は新しいクラスター化列ストア インデックスで非クラスター化インデックスを自動的に定義し、再構築します。  
  
 非クラスター化インデックスを削除する場合は、列ストア インデックスを作成する前に、DROP INDEX ステートメントを使用します。 DROP EXISTING オプションは、変換されるクラスター化インデックスのみを削除します。 非クラスター化インデックスは削除されません。  
  
 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] と [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)], 、列ストア インデックスに非クラスター化インデックスを作成できませんでした。 この例は、どのようにを列ストア インデックスを作成する前に、非クラスター化インデックスを削除する必要する以前のリリースで表示されます。  
  
```sql  
--Create the table for use with this example.  
CREATE TABLE SimpleTable (  
    ProductKey [INT] NOT NULL,   
    OrderDateKey [INT] NOT NULL,   
    DueDateKey [INT] NOT NULL,   
    ShipDateKey [INT] NOT NULL);  
GO  
  
--Create two nonclustered indexes for use with this example  
CREATE INDEX nc1_simple ON SimpleTable (OrderDateKey);  
CREATE INDEX nc2_simple ON SimpleTable (DueDateKey);   
GO  
  
--SQL Server 2012 and SQL Server 2014: you need to drop the nonclustered indexes  
--in order to create the columnstore index.   
  
DROP INDEX SimpleTable.nc1_simple;  
DROP INDEX SimpleTable.nc2_simple;  
  
--Convert the rowstore table to a columnstore index.  
CREATE CLUSTERED COLUMNSTORE INDEX cci_simple ON SimpleTable;   
GO  
```  
  
### <a name="d-convert-a-large-fact-table-from-rowstore-to-columnstore"></a>D. 大きいファクト テーブルを行ストアから列ストアに変換する  
 この例では、大きいファクト テーブルを行ストア テーブルから列ストア テーブルに変換する方法を説明します。  
  
 行ストア テーブルを列ストア テーブルに変換するには、次の手順に従います。  
  
1.  まず、この例で使用する小さいテーブルを作成します。  
  
    ```sql  
    --Create a rowstore table with a clustered index and a nonclustered index.  
    CREATE TABLE MyFactTable (  
        ProductKey [INT] NOT NULL,  
        OrderDateKey [INT] NOT NULL,  
         DueDateKey [INT] NOT NULL,  
         ShipDateKey [INT] NOT NULL )  
    )  
    WITH (  
        CLUSTERED INDEX ( ProductKey )  
    );  
  
    --Add a nonclustered index.  
    CREATE INDEX my_index ON MyFactTable ( ProductKey, OrderDateKey );  
    ```  
  
2.  行ストア テーブルからすべての非クラスター化インデックスを削除します。  
  
    ```sql  
    --Drop all nonclustered indexes  
    DROP INDEX my_index ON MyFactTable;  
    ```  
  
3.  クラスター化インデックスを削除します。  
  
    -   これは、クラスター化列ストア インデックスに変換されるときにインデックスに新しい名前を指定する場合にのみ行ってください。 クラスター化インデックスを削除しない場合は、新しいクラスター化列ストア インデックスに同じ名前が付けられます。  
  
        > [!NOTE]  
        > インデックスの名前に自分の名前を使用すると記憶しやすくなります。 すべての行ストア クラスター化インデックスは、既定の名前である "ClusteredIndex_\<GUID>" を使用します。  
  
    ```sql  
    --Process for dropping a clustered index.  
    --First, look up the name of the clustered rowstore index.  
    --Clustered rowstore indexes always use the DEFAULT name 'ClusteredIndex_<GUID>'.  
    SELECT i.name   
    FROM sys.indexes i   
    JOIN sys.tables t  
    ON ( i.type_desc = 'CLUSTERED' ) WHERE t.name = 'MyFactTable';  
  
    --Drop the clustered rowstore index.  
    DROP INDEX ClusteredIndex_d473567f7ea04d7aafcac5364c241e09 ON MyDimTable;  
    ```  
  
4.  行ストア テーブルを、クラスター化列ストア インデックスを持つ列ストア テーブルに変換します。  
  
    ```sql  
    --Option 1: Convert to columnstore and name the new clustered columnstore index MyCCI.  
    CREATE CLUSTERED COLUMNSTORE INDEX MyCCI ON MyFactTable;  
  
    --Option 2: Convert to columnstore and use the rowstore clustered   
    --index name for the columnstore clustered index name.  
    --First, look up the name of the clustered rowstore index.  
    SELECT i.name   
    FROM sys.indexes i  
    JOIN sys.tables t   
    ON ( i.type_desc = 'CLUSTERED' )  
    WHERE t.name = 'MyFactTable';  
  
    --Second, create the clustered columnstore index and   
    --Replace ClusteredIndex_d473567f7ea04d7aafcac5364c241e09  
    --with the name of your clustered index.  
    CREATE CLUSTERED COLUMNSTORE INDEX   
    ClusteredIndex_d473567f7ea04d7aafcac5364c241e09  
     ON MyFactTable  
    WITH DROP_EXISTING = ON;  
    ```  
  
### <a name="e-convert-a-columnstore-table-to-a-rowstore-table-with-a-clustered-index"></a>E. 列ストア テーブルを、クラスター化インデックスを持つ行ストア テーブルに変換する  
 列ストア テーブルをクラスター化インデックスを持つ行ストア テーブルに変換するには、CREATE INDEX ステートメントと DROP_EXISTING オプションを使用します。  
  
```sql  
CREATE CLUSTERED INDEX ci_MyTable   
ON MyFactTable  
WITH ( DROP EXISTING = ON );  
```  
  
### <a name="f-convert-a-columnstore-table-to-a-rowstore-heap"></a>F. 列ストア テーブルを行ストア ヒープに変換する  
 列ストア テーブルを行ストア ヒープに変換するには、クラスター化列ストア インデックスを削除します。  
  
```sql  
DROP INDEX MyCCI   
ON MyFactTable;  
```  
  

### <a name="g-defragment-by-rebuilding-the-entire-clustered-columnstore-index"></a>G. クラスター化列ストア インデックス全体を再構築して最適化する  
   適用対象: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)]  
  
 クラスター化列ストア インデックス全体を再構築するには、2 つの方法があります。 CREATE CLUSTERED COLUMNSTORE INDEX か [ALTER INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/alter-index-transact-sql.md) と REBUILD オプションを使用できます。 いずれの方法でも、同じ結果が得られます。  
  
> [!NOTE]  
> [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 以降では、この例で説明した方法を使って再構築する代わりに、`ALTER INDEX...REORGANIZE` を使用します。  
  
```sql  
--Determine the Clustered Columnstore Index name of MyDimTable.  
SELECT i.object_id, i.name, t.object_id, t.name   
FROM sys.indexes i   
JOIN sys.tables t  
ON (i.type_desc = 'CLUSTERED COLUMNSTORE')  
WHERE t.name = 'RowstoreDimTable';  
  
--Rebuild the entire index by using CREATE CLUSTERED INDEX.  
CREATE CLUSTERED COLUMNSTORE INDEX my_CCI   
ON MyFactTable  
WITH ( DROP_EXISTING = ON );  
  
--Rebuild the entire index by using ALTER INDEX and the REBUILD option.  
ALTER INDEX my_CCI  
ON MyFactTable  
REBUILD PARTITION = ALL  
WITH ( DROP_EXISTING = ON );  
```  
  
##  <a name="examples-for-nonclustered-columnstore-indexes"></a><a name="nonclustered"></a> 非クラスター化列ストア インデックスの例  
  
### <a name="a-create-a-columnstore-index-as-a-secondary-index-on-a-rowstore-table"></a>A. 行ストア テーブルのセカンダリ インデックスとして列ストア インデックスを作成する  
 この例では、行ストア テーブルに非クラスター化列ストア インデックスを作成します。 このような状況では、1 つのみ列ストア インデックスを作成できます。 列ストア インデックスには、行ストア テーブルのデータのコピーが含まれているために、追加のストレージが必要です。 次の例では、単純なテーブルとクラスター化インデックスを作成します。その後、非クラスター化列ストア インデックスを作成する構文を示します。  
  
```sql  
CREATE TABLE SimpleTable  
(ProductKey [INT] NOT NULL,   
OrderDateKey [INT] NOT NULL,   
DueDateKey [INT] NOT NULL,   
ShipDateKey [INT] NOT NULL);  
GO  
CREATE CLUSTERED INDEX cl_simple ON SimpleTable (ProductKey);  
GO  
CREATE NONCLUSTERED COLUMNSTORE INDEX csindx_simple  
ON SimpleTable  
(OrderDateKey, DueDateKey, ShipDateKey);  
GO  
```  
  
### <a name="b-create-a-simple-nonclustered-columnstore-index-using-all-options"></a>B. すべてのオプションを使用して、単純な非クラスター化列ストア インデックスを作成する  
 次の例は、すべてのオプションを使用して非クラスター化列ストア インデックスを作成する構文を示します。  
  
```sql  
CREATE NONCLUSTERED COLUMNSTORE INDEX csindx_simple  
ON SimpleTable  
(OrderDateKey, DueDateKey, ShipDateKey)  
WITH (DROP_EXISTING =  ON,   
    MAXDOP = 2)  
ON "default"  
GO  
```  
  
 パーティション テーブルを使用した、より複雑な例については、「[列ストア インデックス - 概要](../../relational-databases/indexes/columnstore-indexes-overview.md)」をご覧ください。  
  
### <a name="c-create-a-nonclustered-columnstore-index-with-a-filtered-predicate"></a>C. フィルター選択された述語で、非クラスター化列ストア インデックスを作成する  
 次の例では、[!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] データベースの Production.BillOfMaterials テーブルにフィルター選択された非クラスター化列ストア インデックスを作成します。 フィルター述語では、フィルター選択されたインデックスに非キー列を含めることができます。 この例の述語では、EndDate が NULL 以外の行だけを選択します。  
  
```sql  
IF EXISTS (SELECT name FROM sys.indexes  
    WHERE name = N'FIBillOfMaterialsWithEndDate'   
    AND object_id = OBJECT_ID(N'Production.BillOfMaterials'))  
DROP INDEX FIBillOfMaterialsWithEndDate  
    ON Production.BillOfMaterials;  
GO  
CREATE NONCLUSTERED COLUMNSTORE INDEX "FIBillOfMaterialsWithEndDate"  
    ON Production.BillOfMaterials (ComponentID, StartDate)  
    WHERE EndDate IS NOT NULL;  
```  
  
###  <a name="d-change-the-data-in-a-nonclustered-columnstore-index"></a><a name="ncDML"></a> D. 非クラスター化列ストア インデックス内のデータを変更する  
   適用対象: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] から [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)]。
  
 テーブルに非クラスター化列ストア インデックスを作成すると、そのテーブル内のデータは変更できなくなります。 INSERT、UPDATE、DELETE、または MERGE を使用するクエリは失敗し、エラー メッセージが返されます。 そのテーブル内のデータを追加または変更するには、次のいずれかの操作を行います。  
  
-   列ストア インデックスを無効にするか削除します。 その後、テーブル内のデータを更新できます。 列ストア インデックスを無効にした場合、データの更新の終了時に列ストア インデックスを再構築できます。 たとえば、次のように入力します。  
  
    ```sql  
    ALTER INDEX mycolumnstoreindex ON mytable DISABLE;  
    -- update mytable --  
    ALTER INDEX mycolumnstoreindex on mytable REBUILD  
    ```  
  
-   列ストア インデックスのないステージング テーブルにデータを読み込みます。 そのステージング テーブルに列ストア インデックスを構築します。 そのステージング テーブルをメイン テーブルの空のパーティションに切り替えます。  
  
-   列ストア インデックスを持つテーブルから空のステージング テーブルにパーティションを切り替えます。 ステージング テーブルに列ストア インデックスがある場合は、列ストア インデックスを無効にします。 更新を実行します。 列ストア インデックスを構築 (または再構築) します。 ステージング テーブルを切り替えて、メイン テーブルの (空になった) パーティションに戻します。  
  
## <a name="examples-sssdwfull-and-sspdw"></a>例: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)]、[!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="a-change-a-clustered-index-to-a-clustered-columnstore-index"></a>A. クラスター化インデックスをクラスター化列ストア インデックスに変換する  
 DROP_EXISTING = ON で CREATE CLUSTERED COLUMNSTORE INDEX ステートメントを使用すると、次のことができます。  
  
-   クラスター化インデックスをクラスター化列ストア インデックスに変換します。  
  
-   クラスター化列ストア インデックスを再構築します。  
  
 この例では、クラスター化インデックスを含む列ストア テーブルとして xDimProduct テーブルを作成し、CREATE CLUSTERED COLUMNSTORE INDEX を使用して行ストア テーブルから列ストア テーブルにテーブルを変更します。  
  
```sql  
-- Uses AdventureWorks  
  
IF EXISTS (SELECT name FROM sys.tables  
    WHERE name = N'xDimProduct'  
    AND object_id = OBJECT_ID (N'xDimProduct'))  
DROP TABLE xDimProduct;  
  
--Create a distributed table with a clustered index.  
CREATE TABLE xDimProduct (ProductKey, ProductAlternateKey, ProductSubcategoryKey)  
WITH ( DISTRIBUTION = HASH(ProductKey),  
    CLUSTERED INDEX (ProductKey) )  
AS SELECT ProductKey, ProductAlternateKey, ProductSubcategoryKey FROM DimProduct;  
  
--Change the existing clustered index   
--to a clustered columnstore index with the same name.  
--Look up the name of the index before running this statement.  
CREATE CLUSTERED COLUMNSTORE INDEX <index_name>   
ON xdimProduct   
WITH ( DROP_EXISTING = ON );  
```  
  
### <a name="b-rebuild-a-clustered-columnstore-index"></a>B. クラスター化列ストア インデックスを再構築する  
 この例は先の例を元に作られています。CREATE CLUSTERED COLUMNSTORE INDEX を使用し、cci_xDimProduct という名前の既存のクラスター化列ストア インデックスを再構築します。  
  
```sql  
--Rebuild the existing clustered columnstore index.  
CREATE CLUSTERED COLUMNSTORE INDEX cci_xDimProduct   
ON xdimProduct   
WITH ( DROP_EXISTING = ON );  
```  
  
### <a name="c-change-the-name-of-a-clustered-columnstore-index"></a>C. クラスター化列ストア インデックスの名前を変更する  
 クラスター化列ストア インデックスの名前を変更するには、既存のクラスター化列ストア インデックスを削除し、新しい名前でインデックスを再作成します。  
  
 この操作は小規模のテーブルかからのテーブルでのみ行うことをお勧めします。 大規模なクラスター化列ストア インデックスを削除し、別の名前で再構築すると長い時間がかかります。  
  
 この例では、先の例の cci_xDimProduct という名前のクラスター化列ストア インデックスを使用します。cci_xDimProduct を削除し、mycci_xDimProduct という名前で再作成します。  
  
```sql  
--For illustration purposes, drop the clustered columnstore index.   
--The table continues to be distributed, but changes to a heap.  
DROP INDEX cci_xdimProduct ON xDimProduct;  
  
--Create a clustered index with a new name, mycci_xDimProduct.  
CREATE CLUSTERED COLUMNSTORE INDEX mycci_xDimProduct  
ON xdimProduct  
WITH ( DROP_EXISTING = OFF );  
```  
  
### <a name="d-convert-a-columnstore-table-to-a-rowstore-table-with-a-clustered-index"></a>D. 列ストア テーブルを、クラスター化インデックスを持つ行ストア テーブルに変換する  
 クラスター化列ストア インデックスを削除し、クラスター化インデックスを作成するという状況もあります。 その場合、行ストア形式でテーブルを保存します。 この例では、クラスター化インデックスが含まれる行ストア テーブルに列ストア テーブルを変換します。同じ名前が使用されます。 データは何も失われません。 すべてのデータが行ストア テーブルに入り、一覧の列はクラスター化インデックスのキー列になります。  
  
```sql  
--Drop the clustered columnstore index and create a clustered rowstore index.   
--All of the columns are stored in the rowstore clustered index.   
--The columns listed are the included columns in the index.  
CREATE CLUSTERED INDEX cci_xDimProduct    
ON xdimProduct (ProductKey, ProductAlternateKey, ProductSubcategoryKey, WeightUnitMeasureCode)  
WITH ( DROP_EXISTING = ON);  
```  
  
### <a name="e-convert-a-columnstore-table-back-to-a-rowstore-heap"></a>E. 列ストア テーブルを行ストア ヒープに戻す  
 [DROP INDEX (SQL Server PDW)](drop-index-transact-sql.md) を使用し、クラスター化列ストア インデックスを削除し、テーブルを行ストア ヒープに変換します。 この例では、cci_xDimProduct テーブルを行ストア ヒープに変換します。 テーブルは引き続き配布されますが、ヒープとして保存されます。  
  
```sql  
--Drop the clustered columnstore index. The table continues to be distributed, but changes to a heap.  
DROP INDEX cci_xdimProduct ON xdimProduct;  
```  

### <a name="f-create-an-ordered-clustered-columnstore-index-on-a-table-with-no-index"></a>F. インデックスのないテーブル上で順序付けされたクラスター化列ストア インデックスを作成する

```sql
CREATE CLUSTERED COLUMNSTORE INDEX cci ON Sales.OrderLines
ORDER ( SHIPDATE );
```

### <a name="g-convert-a-clustered-columnstore-index-to-an-ordered-clustered-columnstore-index"></a>G. クラスター化列ストア インデックスを順序付けされたクラスター化列ストア インデックスに変換する

```sql  
CREATE CLUSTERED COLUMNSTORE INDEX cci ON Sales.OrderLines
ORDER ( SHIPDATE );
WITH (DROP_EXISTING = ON)
```

### <a name="h-add-a-column-to-the-ordering-of-an-ordered-clustered-columnstore-index"></a>H. 順序付けされたクラスター化列ストア インデックスの順序に列を追加する

```sql
-- The original ordered clustered columnstore index was ordered on SHIPDATE column only.  Add PRODUCTKEY column to the ordering.
CREATE CLUSTERED COLUMNSTORE INDEX cci ON Sales.OrderLines
ORDER ( SHIPDATE, PRODUCTKEY );
WITH (DROP_EXISTING = ON)
```
### <a name="i-change-the-ordinal-of-ordered-columns"></a>I. 順序付けされた列の序数を変更する  
```sql
-- The original ordered clustered columnstore index was ordered on SHIPDATE, PRODUCTKEY.  Change the ordering to PRODUCTKEY, SHIPDATE.  
CREATE CLUSTERED COLUMNSTORE INDEX cci ON Sales.OrderLines
ORDER ( PRODUCTKEY,SHIPDATE );
WITH (DROP_EXISTING = ON)
```