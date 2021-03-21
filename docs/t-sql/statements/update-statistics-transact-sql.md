---
description: UPDATE STATISTICS (Transact-SQL)
title: UPDATE STATISTICS (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 01/04/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- UPDATE STATISTICS
- UPDATE_STATISTICS_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- updating statistics
- query optimization statistics [SQL Server], updating
- UPDATE STATISTICS statement
- statistical information [SQL Server], updating
ms.assetid: 919158f2-38d0-4f68-82ab-e1633bd0d308
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: c0064fbe4666bb79fa2786166e5f314f67130ee5
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104748542"
---
# <a name="update-statistics-transact-sql"></a>UPDATE STATISTICS (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

テーブルまたはインデックス付きビューで、クエリ最適化に関する統計を更新します。 統計は既定で、クエリ プランを改善するためにクエリ オプティマイザーによって必要に応じて更新されますが、`UPDATE STATISTICS` またはストアド プロシージャ [sp_updatestats](../../relational-databases/system-stored-procedures/sp-updatestats-transact-sql.md) を使用して既定の更新より頻繁に統計を更新することでクエリのパフォーマンスを向上できる場合もあります。  
  
統計を更新すると、クエリが最新の統計を使用してコンパイルされるようになります。 ただし、統計の更新によりクエリが再コンパイルされます。 パフォーマンスの向上を目的とする場合、クエリ プランの改善とクエリの再コンパイルに要する時間の間にはトレードオフの関係があるため、あまり頻繁に統計を更新しないようにすることをお勧めします。 実際のトレードオフはアプリケーションによって異なります。 `UPDATE STATISTICS` では、tempdb を使用して、統計を作成するための行のサンプルを並べ替えます。  
  
![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql
-- Syntax for SQL Server and Azure SQL Database  
  
UPDATE STATISTICS table_or_indexed_view_name   
    [   
        {   
            { index_or_statistics__name }  
          | ( { index_or_statistics_name } [ ,...n ] )   
                }  
    ]   
    [    WITH   
        [  
            FULLSCAN   
              [ [ , ] PERSIST_SAMPLE_PERCENT = { ON | OFF } ]    
            | SAMPLE number { PERCENT | ROWS }   
              [ [ , ] PERSIST_SAMPLE_PERCENT = { ON | OFF } ]    
            | RESAMPLE   
              [ ON PARTITIONS ( { <partition_number> | <range> } [, ...n] ) ]  
            | <update_stats_stream_option> [ ,...n ]  
        ]   
        [ [ , ] [ ALL | COLUMNS | INDEX ]   
        [ [ , ] NORECOMPUTE ]   
        [ [ , ] INCREMENTAL = { ON | OFF } ] 
        [ [ , ] MAXDOP = max_degree_of_parallelism ] 
    ] ;  
  
<update_stats_stream_option> ::=  
    [ STATS_STREAM = stats_stream ]  
    [ ROWCOUNT = numeric_constant ]  
    [ PAGECOUNT = numeric_contant ]  
```  
  
```syntaxsql
-- Syntax for Azure Synapse Analytics and Parallel Data Warehouse  
  
UPDATE STATISTICS [ schema_name . ] table_name   
    [ ( { statistics_name | index_name } ) ]  
    [ WITH   
       {  
              FULLSCAN   
            | SAMPLE number PERCENT   
            | RESAMPLE   
        }  
    ]  
[;]  
```  

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

[!INCLUDE[synapse-analytics-od-unsupported-syntax](../../includes/synapse-analytics-od-unsupported-syntax.md)]

## <a name="arguments"></a>引数
 *table_or_indexed_view_name*  
 統計オブジェクトを含んでいるテーブルまたはインデックス付きビューの名前です。  
  
 *index_or_statistics_name*  
 統計の更新対象のインデックスの名前、または更新する統計の名前を指定します。 *index_or_statistics_name* を指定しない場合は、クエリ オプティマイザーによってテーブルまたはインデックス付きビューのすべての統計が更新されます。 これには、CREATE STATISTICS ステートメントを使用して作成した統計、AUTO_CREATE_STATISTICS がオンの場合に作成される 1 列ずつの統計、およびインデックスに対して作成された統計が含まれます。  
  
 AUTO_CREATE_STATISTICS について詳しくは、「[ALTER DATABASE の SET オプション &#40;Transact-SQL&#41;](../../t-sql/statements/alter-database-transact-sql-set-options.md)」をご覧ください。 テーブルまたはビューのすべてのインデックスを表示するには、[sp_helpindex](../../relational-databases/system-stored-procedures/sp-helpindex-transact-sql.md) を使用します。  
  
 FULLSCAN  
 テーブルまたはインデックス付きビュー内のすべての行をスキャンして統計を計算します。 FULLSCAN と SAMPLE 100 PERCENT は同じ結果になります。 SAMPLE オプションには FULLSCAN を使用できません。  
  
 SAMPLE *number* { PERCENT | ROWS }  
 クエリ オプティマイザーが統計を更新するときに使用する、テーブルやインデックス付きビューに含まれている行のおおよその割合または数を指定します。 PERCENT の場合、*number* には 0 ～ 100 を指定します。ROWS の場合、*number* には 0 ～合計行数を指定します。 クエリ オプティマイザーによってサンプリングされる行の実際の割合や行数が、指定した割合や行数と一致しない場合もあります。 たとえば、データ ページではすべての行がスキャンされます。  
  
 SAMPLE は、既定のサンプリングに基づくクエリ プランが最適ではない特殊な場合に使用できます。 クエリ オプティマイザーは、既定でサンプリングを使用して統計的に有意なサンプル サイズを決定するため、SAMPLE を指定する必要はほとんどありませんが、高品質のクエリ プランを作成する場合は、SAMPLE が必要です。 
 
[!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 以降では、データのサンプリングによる統計の作成が並列で実行できるようになりました (互換性レベル 130 を使用している場合)。これにより、統計情報収集のパフォーマンスが上がります。 テーブル サイズが一定のしきい値を超えると、クエリ オプティマイザーは並列サンプリングによる統計を使用します。 
   
 FULLSCAN オプションには SAMPLE を使用できません。 SAMPLE も FULLSCAN も指定しない場合、既定でサンプリングしたデータが使用され、サンプル サイズが計算されます。  
  
 0 PERCENT や 0 ROWS を指定することはお勧めしません。 0 PERCENT または 0 ROWS を指定した場合、統計オブジェクトは更新されますが、統計データは含まれません。  
  
 ほとんどのワークロードでは、フル スキャンは必要なく、既定のサンプリングで十分です。  
ただし、変化するデータ分布の影響を受ける特定のワークロードではサンプル サイズの増加が必要な場合があり、フル スキャンが必要な場合もあります。  
詳しくは、[CSS SQL Escalation Services に関するブログ](/archive/blogs/psssql/sampling-can-produce-less-accurate-statistics-if-the-data-is-not-evenly-distributed)をご覧ください。  
  
 RESAMPLE  
 最新のサンプル レートを使用して各統計を更新します。  
  
 RESAMPLE を使用すると、フル テーブル スキャンが実行される場合があります。 たとえば、インデックスの統計では、サンプル レートを取得するためにフル テーブル スキャンが使用されます。 サンプル オプション (SAMPLE、FULLSCAN、RESAMPLE) がいずれも指定されていなければ、既定ではクエリ オプティマイザーはデータをサンプリングしてサンプル サイズを計算します。  

PERSIST_SAMPLE_PERCENT = { ON | OFF }  
**ON** の場合、統計では、サンプリング率が明示的に指定されていない後続の更新の設定サンプリング率が保持されます。 **OFF** の場合、統計サンプリング率は、サンプリング率が明示的に指定されていない後続の更新で既定のサンプリングにリセットされます。 既定値は **OFF** です。 
 
 > [!NOTE]
 > AUTO_UPDATE_STATISTICS が実行された場合、保存されたサンプリング率が使用可能な場合はそのサンプリング率が使用され、そうでない場合は既定のサンプリング率が使用されます。
 > RESAMPLE の動作は、このオプションの影響を受けません。
 
 > [!NOTE]
 > テーブルが切り捨てられた場合、切り捨てられた HoBT に基づいて作成されたすべての統計は、既定のサンプリング率を使用するように戻されます。
 
 > [!TIP] 
 > [DBCC SHOW_STATISTICS](../../t-sql/database-console-commands/dbcc-show-statistics-transact-sql.md) と [sys.dm_db_stats_properties](../../relational-databases/system-dynamic-management-views/sys-dm-db-stats-properties-transact-sql.md) は、選択した統計について保存されたサンプル率値を公開します。
 
 **適用対象**: [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] ([!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] SP1 CU4 以降) 以降 ([!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] CU1 以降)。  
 
 ON PARTITIONS ( { \<partition_number> | \<range> } [, ...n] ) ] ON PARTITIONS 句で指定したパーティションを対象としたリーフ レベルの統計を強制的に再計算してから、それらをマージして全体統計を構築します。 異なるサンプル レートで構築されたパーティションの統計はマージできないため、WITH RESAMPLE が必要になります。  
  
**適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降
  
 ALL | COLUMNS | INDEX  
 すべての既存の統計、1 つ以上の列で作成された統計、またはインデックスに対して作成された統計を更新します。 何も指定しない場合は、UPDATE STATISTICS ステートメントによりテーブルまたはインデックス付きビューのすべての統計が更新されます。  
  
 NORECOMPUTE  
 指定した統計の自動統計更新オプション (AUTO_UPDATE_STATISTICS) を無効にします。 このオプションを指定すると、クエリ オプティマイザーはこの統計の更新を実行し、その後の更新を無効にします。  
  
 AUTO_UPDATE_STATISTICS オプションの動作を再有効化するには、NORECOMPUTE オプションを指定せずに UPDATE STATISTICS を再実行するか、または **sp_autostats** を実行します。  
  
> [!WARNING]  
> このオプションを使用すると、最適ではないクエリ プランが作成されることがあります。 このオプションは慎重に使用してください。特に、資格のあるシステム管理者だけが使用することをお勧めします。  
  
 AUTO_STATISTICS_UPDATE オプションについて詳しくは、「[ALTER DATABASE の SET オプション &#40;Transact-SQL&#41;](../../t-sql/statements/alter-database-transact-sql-set-options.md)」をご覧ください。  
  
 INCREMENTAL = { ON | OFF }  
 **ON** の場合、パーティションごとの統計のように統計が再作成されます。 **OFF** の場合、統計ツリーが削除され、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] によって統計が再計算されます。 既定値は **OFF** です。  
  
 パーティションごとの統計がサポートされていない場合は、エラーが生成されます。 次の種類の統計では、増分統計がサポートされていません。  
  
-   ベース テーブルにパーティションで固定されていないインデックスを使用して作成された統計。  
-   Always On の読み取り可能なセカンダリ データベースに対して作成された統計。  
-   読み取り専用のデータベースに対して作成された統計。  
-   フィルター選択されたインデックスに対して作成された統計。  
-   ビューに対して作成された統計。  
-   内部テーブルに対して作成された統計。  
-   空間インデックスまたは XML インデックスを使用して作成された統計。  
  
**適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降

MAXDOP = *max_degree_of_parallelism*  
**適用対象**:[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] SP2 および [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] CU3 以降)。  
  
 統計操作の間、**max degree of parallelism** 構成オプションをオーバーライドします。 詳細については、「 [max degree of parallelism サーバー構成オプションの構成](../../database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option.md)」を参照してください。 並列プランの実行で使用されるプロセッサ数を制限するには、MAXDOP を使用します。 最大数は 64 プロセッサです。  
  
 *max_degree_of_parallelism* は次のように指定できます。  
  
 1  
 並列プラン生成を抑制します。  
  
 \>1  
 現在のシステム ワークロードに基づいて、並列統計操作で使用される最大プロセッサ数を指定の数以下に制限します。  
  
 0 (既定値)  
 現在のシステム ワークロードに基づいて、実際の数以下のプロセッサを使用します。  
  
 \<update_stats_stream_option> 
 
 [!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]  

## <a name="remarks"></a>解説  
  
### <a name="when-to-use-update-statistics"></a>いつ UPDATE STATISTICS を使用するか  
 `UPDATE STATISTICS` を使用する場合の詳細については、「[統計](../../relational-databases/statistics/statistics.md)」を参照してください。  

### <a name="limitations-and-restrictions"></a>制限事項と制約事項  
* テーブルの外部では、統計を更新することはできません。 外部テーブルの統計を更新するには、統計を削除して再作成します。  
* `MAXDOP` オプションは、`STATS_STREAM`、`ROWCOUNT`、および `PAGECOUNT` の各オプションと互換性がありません。
* `MAXDOP` オプションは、Resource Governor ワークロード グループの `MAX_DOP` の設定によって制限されます (使用されている場合)。

### <a name="updating-all-statistics-with-sp_updatestats"></a>sp_updatestats によるすべての統計の更新  
データベース内のすべてのユーザー定義および内部テーブルの統計を更新する方法については、ストアド プロシージャ [sp_updatestats &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-updatestats-transact-sql.md) の説明を参照してください。 たとえば、次のコマンドは、sp_updatestats を呼び出してデータベースのすべての統計を更新します。  
  
```sql  
EXEC sp_updatestats;  
```  

### <a name="automatic-index-and-statistics-management"></a>インデックスと統計の自動管理
[Adaptive Index Defrag](https://github.com/Microsoft/tigertoolbox/tree/master/AdaptiveIndexDefrag) のようなソリューションを活用し、1 つまたは複数のデータベースに対するインデックスの最適化と統計更新を自動管理します。 このプロシージャでは、断片化レベルやその他のパラメーターに基づいてインデックスを再構築または再構成するか、線形しきい値で統計を更新するかが自動的に選択されます。
  
### <a name="determining-the-last-statistics-update"></a>統計の最終更新日の特定  
 統計の最終更新日を調べるには、 [STATS_DATE](../../t-sql/functions/stats-date-transact-sql.md) 関数を使用します。  
  
### <a name="pdw--azure-synapse-analytics"></a>PDW / Azure Synapse Analytics  
 以下の構文は [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] / [!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] でサポートされていません  
  
```sql
UPDATE STATISTICS t1 (a,b);   
```  
  
```sql  
UPDATE STATISTICS t1 (a) WITH SAMPLE 10 ROWS;  
```  
  
```sql  
UPDATE STATISTICS t1 (a) WITH NORECOMPUTE;  
```  
  
```sql  
UPDATE STATISTICS t1 (a) WITH INCREMENTAL = ON;  
```  
  
```sql  
UPDATE STATISTICS t1 (a) WITH stats_stream = 0x01;  
```  
  
## <a name="permissions"></a>アクセス許可  
 テーブルまたはビューに対する `ALTER` 権限が必要です。  
  
## <a name="examples"></a>例  
  
### <a name="a-update-all-statistics-on-a-table"></a>A. テーブルのすべての統計を更新する  
 次の例では、`SalesOrderDetail` テーブルのすべてのインデックスの統計を更新します。  
  
```sql  
USE AdventureWorks2012;  
GO  
UPDATE STATISTICS Sales.SalesOrderDetail;  
GO  
```  
  
### <a name="b-update-the-statistics-for-an-index"></a>B. 1 つのインデックスの統計を更新する  
 次の例では、`SalesOrderDetail` テーブルの `AK_SalesOrderDetail_rowguid` インデックスの統計を更新します。  
  
```sql  
USE AdventureWorks2012;  
GO  
UPDATE STATISTICS Sales.SalesOrderDetail AK_SalesOrderDetail_rowguid;  
GO  
```  
  
### <a name="c-update-statistics-by-using-50-percent-sampling"></a>C. 50% サンプリングで統計を更新する  
 次の例では、`Product` テーブルの `Name` および `ProductNumber` 列に統計を作成し、更新します。  
  
```sql  
USE AdventureWorks2012;  
GO  
CREATE STATISTICS Products  
    ON Production.Product ([Name], ProductNumber)  
    WITH SAMPLE 50 PERCENT  
-- Time passes. The UPDATE STATISTICS statement is then executed.  
UPDATE STATISTICS Production.Product(Products)   
    WITH SAMPLE 50 PERCENT;  
```  
  
### <a name="d-update-statistics-by-using-fullscan-and-norecompute"></a>D. FULLSCAN および NORECOMPUTE を使用して統計を更新する  
 次の例では、`Product` テーブル内の `Products` 統計を更新し、`Product` テーブル内のすべての行でフル スキャンを強制的に実行し、`Products` 統計の自動統計更新を無効にします。  
  
```sql  
USE AdventureWorks2012;  
GO  
UPDATE STATISTICS Production.Product(Products)  
    WITH FULLSCAN, NORECOMPUTE;  
GO  
```  
  
## <a name="examples-sssdwfull-and-sspdw"></a>例: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)]、[!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="e-update-statistics-on-a-table"></a>E. テーブルの統計を更新する  
 次の例では、`Customer` テーブルの `CustomerStats1` 統計を更新します。  
  
```sql  
UPDATE STATISTICS Customer ( CustomerStats1 );  
```  
  
### <a name="f-update-statistics-by-using-a-full-scan"></a>F. フル スキャンを使用して統計を更新する  
 次の例では、`Customer` テーブルのすべての行のスキャンに基づいて `CustomerStats1` 統計を更新します。  
  
```sql  
UPDATE STATISTICS Customer (CustomerStats1) WITH FULLSCAN;  
```  
  
### <a name="g-update-all-statistics-on-a-table"></a>G. テーブルのすべての統計を更新する  
 次の例では、`Customer` テーブルのすべての統計を更新します。  
  
```sql  
UPDATE STATISTICS Customer;  
```  
  
## <a name="see-also"></a>参照  
 [統計](../../relational-databases/statistics/statistics.md)   
 [ALTER DATABASE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-database-transact-sql.md)   
 [CREATE STATISTICS &#40;Transact-SQL&#41;](../../t-sql/statements/create-statistics-transact-sql.md)   
 [DBCC SHOW_STATISTICS &#40;Transact-SQL&#41;](../../t-sql/database-console-commands/dbcc-show-statistics-transact-sql.md)   
 [DROP STATISTICS &#40;Transact-SQL&#41;](../../t-sql/statements/drop-statistics-transact-sql.md)   
 [sp_autostats &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-autostats-transact-sql.md)   
 [sp_updatestats &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-updatestats-transact-sql.md)   
 [STATS_DATE &#40;Transact-SQL&#41;](../../t-sql/functions/stats-date-transact-sql.md)  
 [sys.dm_db_stats_properties &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-stats-properties-transact-sql.md)    
 [sys.dm_db_stats_histogram &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-stats-histogram-transact-sql.md)