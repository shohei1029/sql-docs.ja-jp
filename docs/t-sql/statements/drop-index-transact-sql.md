---
description: DROP INDEX (Transact-SQL)
title: DROP INDEX (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 05/11/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- DROP_INDEX_TSQL
- DROP INDEX
dev_langs:
- TSQL
helpviewer_keywords:
- nonclustered indexes [SQL Server], removing
- MAXDOP index option, DROP INDEX statement
- index removal [SQL Server]
- spatial indexes [SQL Server], dropping
- removing indexes
- deleting indexes
- dropping indexes
- MOVE TO clause
- clustered indexes, removing
- indexes [SQL Server], dropping
- filtered indexes [SQL Server], dropping
- ONLINE option
- indexes [SQL Server], moving
- XML indexes [SQL Server], dropping
- DROP INDEX statement
ms.assetid: 2b1464c8-934c-405f-8ef7-2949346b5372
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: d6cf7d1c69378fde0eeecdc0ff8500e4650a2f3e
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104750462"
---
# <a name="drop-index-transact-sql"></a>DROP INDEX (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  1 つ以上のリレーショナル インデックス、空間インデックス、フィルター選択されたインデックス、または XML インデックスを現在のデータベースから削除します。 MOVE TO オプションを指定すると、1 つのトランザクションで、クラスター化インデックスを削除し、その結果生成されたテーブルを別のファイル グループまたはパーティション構成に移動できます。  
  
 DROP INDEX ステートメントは、PRIMARY KEY 制約または UNIQUE 制約を定義することで作成されたインデックスには適用されません。 制約および対応するインデックスを削除するには、[ALTER TABLE](../../t-sql/statements/alter-table-transact-sql.md) を DROP CONSTRAINT 句と共に使用します。  
  
> [!IMPORTANT]
>  `<drop_backward_compatible_index>` で定義される構文は、[!INCLUDE[msCoName](../../includes/msconame-md.md)][!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の今後のバージョンでは削除される予定です。 新規の開発作業ではこの構文を使用しないようにし、現在この機能を使用しているアプリケーションは修正することを検討してください。 代わりに、`<drop_relational_or_xml_index>` で指定されている構文を使用してください。 XML インデックスは、旧バージョンとの互換性のための構文を使用して削除することはできません。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql
-- Syntax for SQL Server (All options except filegroup and filestream apply to Azure SQL Database.)  
  
DROP INDEX [ IF EXISTS ]   
{ <drop_relational_or_xml_or_spatial_index> [ ,...n ]   
| <drop_backward_compatible_index> [ ,...n ]  
}  
  
<drop_relational_or_xml_or_spatial_index> ::=  
    index_name ON <object>   
    [ WITH ( <drop_clustered_index_option> [ ,...n ] ) ]  
  
<drop_backward_compatible_index> ::=  
    [ owner_name. ] table_or_view_name.index_name  
  
<object> ::=  
{ database_name.schema_name.table_or_view_name | schema_name.table_or_view_name | table_or_view_name }  
  
<drop_clustered_index_option> ::=  
{  
    MAXDOP = max_degree_of_parallelism  
  | ONLINE = { ON | OFF }  
  | MOVE TO { partition_scheme_name ( column_name )   
            | filegroup_name  
            | "default"   
            }  
  [ FILESTREAM_ON { partition_scheme_name   
            | filestream_filegroup_name   
            | "default" } ]  
}  
```  
  
```syntaxsql
-- Syntax for Azure SQL Database  
  
DROP INDEX  
{ <drop_relational_or_xml_or_spatial_index> [ ,...n ]   
}  
  
<drop_relational_or_xml_or_spatial_index> ::=   
    index_name ON <object>  
  
<object> ::=   
{ database_name.schema_name.table_or_view_name | schema_name.table_or_view_name | table_or_view_name }  
```  
  
```syntaxsql
-- Syntax for Azure Synapse Analytics and Parallel Data Warehouse  
  
DROP INDEX index_name ON { database_name.schema_name.table_name | schema_name.table_name | table_name }  
[;]  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
 *IF EXISTS*  
 **適用対象**: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] から [現在のバージョン](/troubleshoot/sql/general/determine-version-edition-update-level)まで)。  
  
 条件付きでは既に存在する場合にのみ、インデックスを削除します。  
  
 *index_name*  
 削除するインデックスの名前です。  
  
 *database_name*  
 データベースの名前です。  
  
 *schema_name*  
 テーブルまたはビューが属するスキーマの名前を指定します。  
  
 *table_or_view_name*  
 インデックスに関連付けられているテーブルまたはビューの名前です。 空間インデックスはテーブルでのみサポートされます。  
  
 オブジェクトに対するインデックスのレポートを表示するには、[sys.indexes](../../relational-databases/system-catalog-views/sys-indexes-transact-sql.md) カタログ ビューを使用します。  
  
 Azure SQL Database では、database_name が現在のデータベースの場合、または database_name が tempdb で、object_name が # で始まる場合に、3 つの要素で構成された名前形式 database_name.[schema_name].object_name がサポートされます。  
  
 \<drop_clustered_index_option>  
 **適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降、[!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)]。  
  
 クラスター化インデックス オプションを制御します。 これらのオプションは、他のインデックス型では使用できません。  
  
 MAXDOP = *max_degree_of_parallelism*  
 **適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降、[!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)] (パフォーマンス レベル P2 と P3 のみ)。  
  
 インデックス操作の間、**max degree of parallelism** 構成オプションをオーバーライドします。 詳細については、「 [max degree of parallelism サーバー構成オプションの構成](../../database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option.md)」を参照してください。 並列プランの実行で使用されるプロセッサ数を制限するには、MAXDOP を使用します。 最大数は 64 プロセッサです。  
  
> [!IMPORTANT]  
>  MAXDOP は、空間インデックスまたは XML インデックスには使用できません。  
  
 *max_degree_of_parallelism* は次のように指定できます。  
  
 1  
 並列プラン生成を抑制します。  
  
 \>1  
 並列インデックス操作で使用される最大プロセッサ数を、指定した数に制限します。  
  
 0 (既定値)  
 現在のシステム ワークロードに基づいて、実際の数以下のプロセッサを使用します。  
  
 詳細については、「 [並列インデックス操作の構成](../../relational-databases/indexes/configure-parallel-index-operations.md)」を参照してください。  
  
> [!NOTE]  
>  並列インデックス操作は、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の一部のエディションで使用できません。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の各エディションでサポートされる機能の一覧については、「[Editions and Supported Features for SQL Server 2016](../../sql-server/editions-and-components-of-sql-server-2016.md)」 (SQL Server 2016 のエディションとサポートされる機能) を参照してください。  
  
 ONLINE = ON | **OFF**  
 **適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降、[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。  
  
 インデックス操作時に、基になるテーブルや関連するインデックスをクエリやデータ変更で使用できるかどうかを指定します。 既定値は OFF です。  
  
 ON  
 長期間のテーブル ロックは持続されません。 これにより、基となるテーブルに対してクエリや更新を続けることができます。  
  
 OFF  
 テーブル ロックが適用され、インデックス操作中はテーブルが利用できません。  
  
 クラスター化インデックスを削除するときには、ONLINE オプションだけを指定できます。 詳細については、「解説」を参照してください。  
  
> [!NOTE]  
>  オンラインでのインデックス操作は、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]のすべてのエディションで使用できるわけではありません。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の各エディションでサポートされる機能の一覧については、「[Editions and Supported Features for SQL Server 2016](../../sql-server/editions-and-components-of-sql-server-2016.md)」 (SQL Server 2016 のエディションとサポートされる機能) を参照してください。  
  
 MOVE TO { _partition\_scheme\_name_ **(** _column\_name_ **)**  | _filegroup\_name_ |  **"** default **"**  
 **適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。 [!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)] はファイル グループの名前として "default" をサポートしています。  
  
 現在クラスター化インデックスのリーフ レベルにあるデータ行を移動する場所を指定します。 データは、ヒープの形式で新しい場所に移動されます。 新しい場所としてパーティション構成またはファイル グループを指定できますが、このパーティション構成やファイル グループはあらかじめ存在している必要があります。 MOVE TO は、インデックス付きビューまたは非クラスター化インデックスに対しては有効ではありません。 パーティション構成やファイル グループを指定しないと、結果のテーブルは、クラスター化インデックスに対して定義されているのと同じパーティション構成またはファイル グループに配置されます。  
  
 MOVE TO を使用してクラスター化インデックスを削除すると、ベース テーブル上の非クラスター化インデックスが再構築されますが、元のファイル グループまたはパーティション構成からは移動されません。 ベース テーブルを別のファイル グループやパーティション構成に移動しても、非クラスター化インデックスは、ベース テーブルの新しい場所 (ヒープ) に同時に移動されません。 したがって、以前に非クラスター化インデックスがクラスター化インデックスに対応した位置にあっても、ヒープとは対応しなくなる可能性があります。 パーティション インデックスの位置合わせの詳細については、「[パーティション テーブルとパーティション インデックス](../../relational-databases/partitions/partitioned-tables-and-indexes.md)」を参照してください。  
  
 _partition_scheme_name_ **(** _column_name_ **)**  
 **適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降、[!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)]。  
  
 結果のテーブルのための場所として、パーティション構成を指定します。 パーティション構成は、[CREATE PARTITION SCHEME](../../t-sql/statements/create-partition-scheme-transact-sql.md) または [ALTER PARTITION SCHEME](../../t-sql/statements/alter-partition-scheme-transact-sql.md) のどちらかを実行して、あらかじめ作成しておく必要があります。 場所を指定しないでテーブルをパーティション分割すると、テーブルは既存のクラスター化インデックスと同じパーティション構成に格納されます。  
  
 構成内の列名は、インデックス定義内の列に制限されません。 ベース テーブルの任意の列を指定できます。  
  
 *filegroup_name*  
 **適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。  
  
 結果のテーブルのための場所として、ファイル グループを指定します。 場所を指定しないでテーブルをパーティション分割すると、結果のテーブルはクラスター化インデックスと同じファイル グループに格納されます。 ファイル グループは既に存在している必要があります。  
  
 **"** default **"**  
 結果のテーブルの既定の場所を指定します。  
  
> [!NOTE]
>  このコンテキストでは、default はキーワードではありません。 これは、既定のファイル グループの識別子で、MOVE TO **"** default **"** または MOVE TO **[** default **]** のように区切り記号で区切る必要があります。 **"** default **"** を指定する場合は、現在のセッションに対して QUOTED_IDENTIFIER オプションが ON に設定されている必要があります。 これが既定の設定です。 詳細については、「[SET QUOTED_IDENTIFIER &#40;Transact-SQL&#41;](../../t-sql/statements/set-quoted-identifier-transact-sql.md)」をご覧ください。  
  
 FILESTREAM_ON { *partition_scheme_name* | *filestream_filegroup_name* |  **"** default **"** }  
 **適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。  
  
 現在クラスター化インデックスのリーフ レベルに格納されている FILESTREAM テーブルを移動する場所を指定します。 データは、ヒープの形式で新しい場所に移動されます。 新しい場所としてパーティション構成またはファイル グループを指定できますが、このパーティション構成やファイル グループはあらかじめ存在している必要があります。 FILESTREAM ON は、インデックス付きビューまたは非クラスター化インデックスに対しては無効です。 パーティション構成が指定されていない場合、データは、クラスター化インデックスに定義されていたものと同じパーティション構成に格納されます。  
  
 *partition_scheme_name*  
 FILESTREAM データのパーティション構成を指定します。 パーティション構成は、[CREATE PARTITION SCHEME](../../t-sql/statements/create-partition-scheme-transact-sql.md) または [ALTER PARTITION SCHEME](../../t-sql/statements/alter-partition-scheme-transact-sql.md) のどちらかを実行して、あらかじめ作成しておく必要があります。 場所を指定しないでテーブルをパーティション分割すると、テーブルは既存のクラスター化インデックスと同じパーティション構成に格納されます。  
  
 MOVE TO にパーティション構成を指定する場合は、FILESTREAM ON にも同じパーティション構成を使用する必要があります。  
  
 *filestream_filegroup_name*  
 FILESTREAM データの FILESTREAM ファイル グループを指定します。 位置を指定せず、テーブルがパーティション分割されていない場合、データは既定の FILESTREAM ファイル グループに含められます。  
  
 **"** default **"**  
 FILESTREAM データの既定の位置を指定します。  
  
> [!NOTE]
>  このコンテキストでは、default はキーワードではありません。 これは、既定のファイル グループの識別子で、MOVE TO **"** default **"** または MOVE TO **[** default **]** のように区切り記号で区切る必要があります。 "default" を指定する場合は、現在のセッションに対して QUOTED_IDENTIFIER オプションが ON である必要があります。 これが既定の設定です。 詳細については、「[SET QUOTED_IDENTIFIER &#40;Transact-SQL&#41;](../../t-sql/statements/set-quoted-identifier-transact-sql.md)」をご覧ください。  
  
## <a name="remarks"></a>解説  
 非クラスター化インデックスを削除すると、インデックス定義がメタデータから削除され、インデックス データ ページ (B ツリー) がデータベース ファイルから削除されます。 クラスター化インデックスを削除すると、インデックス定義がメタデータから削除され、クラスター化インデックスのリーフ レベルに格納されたデータ行は、結果の順序付けられていないテーブル (ヒープ) に格納されます。 それまでインデックスが使用していたすべての領域は解放されます。 この領域は、任意のデータベース オブジェクトに使用できます。  
  
 インデックスが格納されているファイル グループがオフラインであるか読み取り専用に設定されている場合には、インデックスを削除することはできません。  
  
 インデックス付きビューのクラスター化インデックスを削除すると、同じビューのすべての非クラスター化インデックスと自動作成された統計情報が自動的に削除されます。 手動で作成した統計情報は削除されません。  
  
 構文 _table\_or\_view\_name_ **.** _index\_name_ は旧バージョンとの互換性のために維持されます。 XML インデックスまたは空間インデックスは、旧バージョンとの互換性のための構文を使用して削除することはできません。  
  
 128 以上のエクステントを持つインデックスを削除すると、[!INCLUDE[ssDE](../../includes/ssde-md.md)]は、トランザクションがコミットされるまで実際のページの割り当て解除と関連するロックを遅らせます。  
  
 新しい FILL FACTOR 値を適用したり、一括読み取りの後でデータを再構成するためなどに、インデックスを削除し、作り直して、再構成または再構築することがあります。 これを行うには、特にクラスター化インデックスに対しては、[ALTER INDEX](../../t-sql/statements/alter-index-transact-sql.md) を使用するのがより効率的です。 ALTER INDEX REBUILD は、非クラスター化インデックスを再構築するオーバーヘッドをなくすために最適化されています。  
  
## <a name="using-options-with-drop-index"></a>DROP INDEX でのオプションの使用  
 クラスター化インデックスを削除する際に、MAXDOP、ONLINE、MOVE TO インデックス オプションを設定できます。  
  
 MOVE TO は、単一のトランザクションでクラスター化インデックスを削除し、結果のテーブルを別のファイル グループまたはパーティション構成に移動するために使用します。  
  
 ONLINE = ON を指定すると、基となるデータや関連する非クラスター化インデックスに対するクエリと変更は、DROP INDEX トランザクションによってブロックされません。 オンラインでは、一度に 1 つのクラスター化インデックスしか削除できません。 ONLINE オプションの詳細な説明については、「[CREATE INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/create-index-transact-sql.md)」を参照してください。  
  
 インデックスがビュー上で無効になっているか、リーフ レベルのデータ行に **text**、**ntext**、**image**、**varchar(max)** 、**nvarchar(max)** 、**varbinary(max)** 、または **xml** 列を含む場合には、クラスター化インデックスをオンラインで削除することはできません。  
  
 ONLINE = ON オプションおよび MOVE TO オプションを使用するには、追加の一時ディスク領域が必要です。  
  
 インデックスを削除すると、結果のヒープは、**sys.indexes** カタログ ビューで **name** 列が NULL として表示されます。 テーブル名を表示するには、**sys.indexes** と **sys.tables** を **object_id** で結合します。 クエリの例については、例 D を参照してください。  
  
 [!INCLUDE[ssEnterpriseEd2005](../../includes/ssenterpriseed2005-md.md)] 以降を実行するマルチプロセッサ コンピューターでは、他のクエリと同様に、クラスター化インデックスの削除に関連するスキャン操作や並べ替え操作の実行に、DROP INDEX がより多くのプロセッサを使用する場合があります。 MAXDOP インデックス オプションを指定すると、DROP INDEX ステートメントの実行に使用されるプロセッサ数を手動で構成できます。 詳細については、「 [並列インデックス操作の構成](../../relational-databases/indexes/configure-parallel-index-operations.md)」を参照してください。  
  
 クラスター化インデックスを削除する場合、パーティション構成を変更しない限り、対応するヒープ パーティションでデータ圧縮設定が維持されます。 パーティション構成を変更すると、すべてのパーティションが圧縮されていない状態に再構築されます (DATA_COMPRESSION = NONE)。 クラスター化インデックスを削除し、パーティション構成を変更するには、次の 2 つの手順が必要です。  
  
1.  クラスター化インデックスを削除します。  
  
2.  圧縮オプションを指定する ALTER TABLE ...REBUILD ... オプションを使用して、テーブルを変更します。  
  
OFFLINE でクラスター化インデックスを削除すると、クラスター化インデックスの上位レベルだけが削除されます。そのため、操作はとても高速です。 ONLINE でクラスター化インデックスを削除すると、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] によって、ヒープが手順 1. で 1 回、手順 2. で 1 回の計 2 回再構築されます。 データ圧縮の詳細については、「[データの圧縮](../../relational-databases/data-compression/data-compression.md)」を参照してください。  
  
## <a name="xml-indexes"></a>XML インデックス  
 AnXML インデックスを削除するときに、オプションを指定することはできません。 また、_table\_or\_view\_name_ **.** _index\_name_ 構文を使用することはできません。 プライマリ XML インデックスを削除すると、関連するすべてのセカンダリ XML インデックスが自動的に削除されます。 詳細については、「[XML インデックス &#40;SQL Server&#41;](../../relational-databases/xml/xml-indexes-sql-server.md)」をご覧ください。  
  
## <a name="spatial-indexes"></a>空間インデックス  
 空間インデックスはテーブルでのみサポートされます。 空間インデックスを削除する場合、オプションを指定することも、 **.** _index\_name_ を使用することもできません。 正しい構文は次のとおりです。  
  
 DROP INDEX *spatial_index_name* ON *spatial_table_name*;  
  
 空間インデックスについて詳しくは、「[空間インデックスの概要](../../relational-databases/spatial/spatial-indexes-overview.md)」を参照してください。  
  
## <a name="permissions"></a>アクセス許可  
 DROP INDEX を実行するには、少なくとも、テーブルまたはビューの ALTER 権限が必要です。 この権限は、固定サーバー ロール **sysadmin** と、固定データベース ロール **db_ddladmin** および **db_owner** に既定で許可されています。  
  
## <a name="examples"></a>例  
  
### <a name="a-dropping-an-index"></a>A. インデックスを削除する  
 次の例では、[!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] データベースにある `ProductVendor` テーブルの `IX_ProductVendor_VendorID` インデックスを削除します。  
  
```sql  
DROP INDEX IX_ProductVendor_BusinessEntityID   
    ON Purchasing.ProductVendor;  
GO  
```  
  
### <a name="b-dropping-multiple-indexes"></a>B. 複数のインデックスを削除する  
 次の例では、[!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] データベースから、単一のトランザクションで 2 つのインデックスを削除します。  
  
```sql  
DROP INDEX  
    IX_PurchaseOrderHeader_EmployeeID ON Purchasing.PurchaseOrderHeader,  
    IX_Address_StateProvinceID ON Person.Address;  
GO  
```  
  
### <a name="c-dropping-a-clustered-index-online-and-setting-the-maxdop-option"></a>C. クラスター化インデックスをオンラインで削除し、MAXDOP オプションを設定する  
 次の例では、`ONLINE` オプションに `ON` を設定し、`MAXDOP` オプションに `8` を設定してクラスター化インデックスを削除します。 MOVE TO オプションは指定していないため、結果のテーブルは、インデックスと同じファイル グループに格納されます。 この例では [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] データベースを使用します  
  
**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降、[!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)]。  
  
```sql  
DROP INDEX AK_BillOfMaterials_ProductAssemblyID_ComponentID_StartDate   
    ON Production.BillOfMaterials WITH (ONLINE = ON, MAXDOP = 2);  
GO  
```  
  
### <a name="d-dropping-a-clustered-index-online-and-moving-the-table-to-a-new-filegroup"></a>D. クラスター化インデックスをオンラインで削除し、テーブルを新しいファイル グループに移動する  
 次の例では、クラスター化インデックスをオンラインで削除し、 `NewGroup` 句を使用することで、結果のテーブル (ヒープ) をファイル グループ `MOVE TO` に移動します。 移動の前後で `sys.indexes`、 `sys.tables`、および `sys.filegroups` カタログ ビューを参照し、ファイル グループ内のインデックスとテーブルの配置を確認します。 (以降で [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] インデックス IF EXISTS の DROP 構文を使用することができます)。  
  
**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。  
  
```sql  
--Create a clustered index on the PRIMARY filegroup if the index does not exist.  
CREATE UNIQUE CLUSTERED INDEX  
    AK_BillOfMaterials_ProductAssemblyID_ComponentID_StartDate   
        ON Production.BillOfMaterials (ProductAssemblyID, ComponentID,   
        StartDate)  
    ON 'PRIMARY';  
GO  
-- Verify filegroup location of the clustered index.  
SELECT t.name AS [Table Name], i.name AS [Index Name], i.type_desc,  
    i.data_space_id, f.name AS [Filegroup Name]  
FROM sys.indexes AS i  
    JOIN sys.filegroups AS f ON i.data_space_id = f.data_space_id  
    JOIN sys.tables as t ON i.object_id = t.object_id  
        AND i.object_id = OBJECT_ID(N'Production.BillOfMaterials','U')  
GO  
--Create filegroup NewGroup if it does not exist.  
IF NOT EXISTS (SELECT name FROM sys.filegroups  
                WHERE name = N'NewGroup')  
    BEGIN  
    ALTER DATABASE AdventureWorks2012  
        ADD FILEGROUP NewGroup;  
    ALTER DATABASE AdventureWorks2012  
        ADD FILE (NAME = File1,  
            FILENAME = 'C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\MSSQL\DATA\File1.ndf')  
        TO FILEGROUP NewGroup;  
    END  
GO  
--Verify new filegroup  
SELECT * from sys.filegroups;  
GO  
-- Drop the clustered index and move the BillOfMaterials table to  
-- the Newgroup filegroup.  
-- Set ONLINE = OFF to execute this example on editions other than Enterprise Edition.  
DROP INDEX AK_BillOfMaterials_ProductAssemblyID_ComponentID_StartDate   
    ON Production.BillOfMaterials   
    WITH (ONLINE = ON, MOVE TO NewGroup);  
GO  
-- Verify filegroup location of the moved table.  
SELECT t.name AS [Table Name], i.name AS [Index Name], i.type_desc,  
    i.data_space_id, f.name AS [Filegroup Name]  
FROM sys.indexes AS i  
    JOIN sys.filegroups AS f ON i.data_space_id = f.data_space_id  
    JOIN sys.tables as t ON i.object_id = t.object_id  
        AND i.object_id = OBJECT_ID(N'Production.BillOfMaterials','U');  
GO  
```  
  
### <a name="e-dropping-a-primary-key-constraint-online"></a>E. PRIMARY KEY 制約をオンラインで削除する  
 PRIMARY KEY 制約や UNIQUE 制約の結果作成されたインデックスは、DROP INDEX を使用して削除することができません。 そのようなインデックスは、ALTER TABLE DROP CONSTRAINT ステートメントを使用して削除します。 詳細については、「[ALTER TABLE](../../t-sql/statements/alter-table-transact-sql.md)」を参照してください。  
  
 次の例では、制約を削除することで、PRIMARY KEY 制約によるクラスター化インデックスを削除します。 `ProductCostHistory` テーブルには FOREIGN KEY 制約はありません。 それがある場合には、まずそれらの制約を削除する必要があります。  
  
```sql  
-- Set ONLINE = OFF to execute this example on editions other than Enterprise Edition.  
ALTER TABLE Production.TransactionHistoryArchive  
DROP CONSTRAINT PK_TransactionHistoryArchive_TransactionID  
WITH (ONLINE = ON);  
```  
  
### <a name="f-dropping-an-xml-index"></a>F. XML インデックスを削除する  
 次の例では、[!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] データベースにある `ProductModel` テーブルの XML インデックスを削除します。  
  
```sql  
DROP INDEX PXML_ProductModel_CatalogDescription   
    ON Production.ProductModel;  
```  
  
### <a name="g-dropping-a-clustered-index-on-a-filestream-table"></a>G. FILESTREAM テーブルのクラスター化インデックスを削除する  
 次の例では、クラスター化インデックスをオンラインで削除し、`MyPartitionScheme` 句と `MOVE TO` 句の両方を使用して、結果のテーブル (ヒープ) と FILESTREAM データを `FILESTREAM ON` パーティション構成に移動します。  
  
**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。  
  
```sql  
DROP INDEX PK_MyClusteredIndex   
    ON dbo.MyTable   
    WITH (MOVE TO MyPartitionScheme,  
          FILESTREAM_ON MyPartitionScheme);  
GO  
```  

  
## <a name="see-also"></a>参照  
 [ALTER INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/alter-index-transact-sql.md)   
 [ALTER PARTITION SCHEME &#40;Transact-SQL&#41;](../../t-sql/statements/alter-partition-scheme-transact-sql.md)   
 [ALTER TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-table-transact-sql.md)   
 [CREATE INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/create-index-transact-sql.md)   
 [CREATE PARTITION SCHEME &#40;Transact-SQL&#41;](../../t-sql/statements/create-partition-scheme-transact-sql.md)   
 [CREATE SPATIAL INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/create-spatial-index-transact-sql.md)   
 [CREATE XML INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/create-xml-index-transact-sql.md)   
 [EVENTDATA &#40;Transact-SQL&#41;](../../t-sql/functions/eventdata-transact-sql.md)   
 [sys.indexes &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-indexes-transact-sql.md)   
 [sys.tables &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-tables-transact-sql.md)   
 [sys.filegroups &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-filegroups-transact-sql.md)   
 [sp_spaceused &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-spaceused-transact-sql.md)  
