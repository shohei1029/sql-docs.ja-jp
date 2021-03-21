---
description: CREATE STATISTICS (Transact-SQL)
title: CREATE STATISTICS (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 01/04/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- STATISTICS
- STATISTICS_TSQL
- CREATE STATISTICS
- CREATE_STATISTICS_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- query optimization statistics [SQL Server], creating
- indexed views [SQL Server], statistics
- FULLSCAN option
- CREATE STATISTICS statement
- filtered statistics [SQL Server]
- creating statistics [SQL Server]
- NORECOMPUTE clause
ms.assetid: b23e2f6b-076c-4e6d-9281-764bdb616ad2
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: f7dbfafd8cdcca4c34f25d979b660cd9cbaa679b
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104748225"
---
# <a name="create-statistics-transact-sql"></a>CREATE STATISTICS (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  テーブル、インデックス付きビュー、または、外部テーブルの 1 つまたは複数の列に関するクエリ最適化の統計を作成します。 ほとんどのクエリでは、高品質のクエリ プランに必要な統計がクエリ オプティマイザーによって既に生成されていますが、クエリのパフォーマンスを向上させるために CREATE STATISTICS で追加の統計を作成したりクエリのデザインを変更したりする必要がある場合もあります。  
  
 詳しくは、「[統計](../../relational-databases/statistics/statistics.md)」をご覧ください。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql
-- Syntax for SQL Server and Azure SQL Database  
  
-- Create statistics on an external table  
CREATE STATISTICS statistics_name   
ON { table_or_indexed_view_name } ( column [ ,...n ] )   
    [ WITH FULLSCAN ] ;  
  
-- Create statistics on a regular table or indexed view  
CREATE STATISTICS statistics_name   
ON { table_or_indexed_view_name } ( column [ ,...n ] )   
    [ WHERE <filter_predicate> ]  
    [ WITH   
        [ [ FULLSCAN   
            [ [ , ] PERSIST_SAMPLE_PERCENT = { ON | OFF } ]    
          | SAMPLE number { PERCENT | ROWS }   
            [ [ , ] PERSIST_SAMPLE_PERCENT = { ON | OFF } ]    
          | <update_stats_stream_option> [ ,...n ]    
        [ [ , ] NORECOMPUTE ]   
        [ [ , ] INCREMENTAL = { ON | OFF } ] 
        [ [ , ] MAXDOP = max_degree_of_parallelism ]
    ] ;  
  
<filter_predicate> ::=   
    <conjunct> [AND <conjunct>]  
  
<conjunct> ::=  
    <disjunct> | <comparison>  
  
<disjunct> ::=  
        column_name IN (constant ,...)  
  
<comparison> ::=  
        column_name <comparison_op> constant  
  
<comparison_op> ::=  
    IS | IS NOT | = | <> | != | > | >= | !> | < | <= | !<  
    
<update_stats_stream_option> ::=  
    [ STATS_STREAM = stats_stream ]  
    [ ROWCOUNT = numeric_constant ]  
    [ PAGECOUNT = numeric_contant ] 
```  
  
```syntaxsql
-- Syntax for Azure Synapse Analytics and Parallel Data Warehouse  
  
CREATE STATISTICS statistics_name   
    ON { database_name.schema_name.table_name | schema_name.table_name | table_name }
    ( column_name  [ ,...n ] )   
    [ WHERE <filter_predicate> ]  
    [ WITH {  
           FULLSCAN   
           | SAMPLE number PERCENT   
      }  
    ]  
[;]  
  
<filter_predicate> ::=   
    <conjunct> [AND <conjunct>]  
  
<conjunct> ::=  
    <disjunct> | <comparison>  
  
<disjunct> ::=  
        column_name IN (constant ,...)  
  
<comparison> ::=  
        column_name <comparison_op> constant  
  
<comparison_op> ::=  
    IS | IS NOT | = | <> | != | > | >= | !> | < | <= | !<  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
 *statistics_name*  
 作成する統計の名前です。  
  
 *table_or_indexed_view_name*  
 統計を作成するテーブル、インデックス付きビュー、または外部テーブルの名前です。 別のデータベースの統計を作成するには、修飾テーブル名を指定します。  
  
 *column [ ,...n]*  
 統計に含める 1 つまたは複数の列。 列は、左から右への優先順位にする必要があります。 ヒストグラムの作成には最初の列のみが使用されます。 すべての列は、密度と呼ばれる列間の相関統計に使用されます。  
  
 インデックス キー列として指定できる任意の列を指定できますが、次の例外があります。  
  
-   **Xml** 列、フルテキスト列、FILESTREAM 列は指定できません。  
  
-   計算列は、データベース設定の ARITHABORT と QUOTED_IDENTIFIER が ON に設定されている場合にのみ指定できます。  
  
-   CLR ユーザー定義型の列は、データ型でバイナリ順がサポートされている場合に指定できます。 ユーザー定義型列のメソッド呼び出しとして定義されている計算列は、メソッドが決定的とマークされている場合に指定できます。  
  
 WHERE \<filter_predicate> 統計オブジェクトを作成するときに含める行のサブセットを選択するための式を指定します。 フィルター述語を使用して作成された統計は、フィルター選択された統計情報と呼ばれます。 フィルター述語には単純な比較ロジックを使用するので、計算列、UDT 列、空間データ型列、または **hierarchyID** データ型列を参照することはできません。 比較演算子では、NULL リテラルを使用する比較を実行できません。 代わりに、IS NULL 演算子と IS NOT NULL 演算子を使用します。  
  
 次に、Production.BillOfMaterials テーブルのフィルター述語の例をいくつか示します。  
  
 * `WHERE StartDate > '20000101' AND EndDate <= '20000630'`  
  
 * `WHERE ComponentID IN (533, 324, 753)`  
  
 * `WHERE StartDate IN ('20000404', '20000905') AND EndDate IS NOT NULL`  
  
 フィルター述語について詳しくは、「[フィルター選択されたインデックスの作成](../../relational-databases/indexes/create-filtered-indexes.md)」をご覧ください。  
  
 FULLSCAN  
 すべての行をスキャンして統計を計算します。 FULLSCAN と SAMPLE 100 PERCENT は同じ結果になります。 SAMPLE オプションには FULLSCAN を使用できません。  
  
 省略すると、SQL Server ではサンプリングを使用して統計が作成され、高品質のクエリ プランを作成するために必要なサンプル サイズが決定されます。  
  
 SAMPLE *number* { PERCENT | ROWS }  
 テーブルやインデックス付きビューに含まれている行について、クエリ オプティマイザーで統計を作成する際に使用するおおよその割合または数を指定します。 PERCENT の場合、*number* には 0 ～ 100 を指定します。ROWS の場合、*number* には 0 ～合計行数を指定します。 クエリ オプティマイザーによってサンプリングされる行の実際の割合や行数が、指定した割合や行数と一致しない場合もあります。 たとえば、データ ページではすべての行がスキャンされます。  
  
 SAMPLE は、既定のサンプリングに基づくクエリ プランが最適ではない特殊な場合に使用できます。 クエリ オプティマイザーによって既にサンプリングが使用され、既定で統計的に有意なサンプル サイズが決定されるため、ほとんどの場合は SAMPLE を指定する必要がありませんが、高品質のクエリ プランを作成する場合は必要です。  
  
 FULLSCAN オプションには SAMPLE を使用できません。 SAMPLE も FULLSCAN も指定しない場合、既定でサンプリングしたデータが使用され、サンプル サイズが計算されます。  
  
 0 PERCENT や 0 ROWS を指定することはお勧めしません。 0 PERCENT または 0 ROWS を指定した場合、統計オブジェクトは作成されますが、統計データは含まれません。  
 
 PERSIST_SAMPLE_PERCENT = { ON | OFF }  
 **ON** の場合、統計では、サンプリング率が明示的に指定されていない後続の更新の作成サンプリング率が保持されます。 **OFF** の場合、統計サンプリング率は、サンプリング率が明示的に指定されていない後続の更新で既定のサンプリングにリセットされます。 既定値は **OFF** です。 
 
 > [!NOTE]
 > テーブルが切り捨てられた場合、切り捨てられた HoBT に基づいて作成されたすべての統計は、既定のサンプリング率を使用するように戻されます。

 **適用対象**: [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] ([!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] SP1 CU4 以降) 以降 ([!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] CU1 以降)。    
  
 STATS_STREAM **=** _stats_stream_  
 [!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]  
  
 NORECOMPUTE  
 *statistics_name* の自動統計更新オプション (AUTO_STATISTICS_UPDATE) を無効にします。 このオプションを指定すると、*statistics_name* の進行中の更新は最後まで実行され、その後の更新が無効になります。  
  
 統計の更新を再有効化するには、[DROP STATISTICS](../../t-sql/statements/drop-statistics-transact-sql.md) で統計を削除してから、NORECOMPUTE オプションを指定せずに CREATE STATISTICS を実行します。  
  
> [!WARNING]  
> このオプションを使用すると、最適ではないクエリ プランが作成されることがあります。 このオプションは慎重に使用してください。特に、資格のあるシステム管理者だけが使用することをお勧めします。  
  
 AUTO_STATISTICS_UPDATE オプションについて詳しくは、「[ALTER DATABASE の SET オプション &#40;Transact-SQL&#41;](../../t-sql/statements/alter-database-transact-sql-set-options.md)」をご覧ください。 統計の更新の無効化および再有効化について詳しくは、「[統計](../../relational-databases/statistics/statistics.md)」をご覧ください。  
  
 INCREMENTAL = { ON | OFF }  
 **ON** の場合、作成される統計はパーティションごとの統計です。 **OFF** の場合、すべてのパーティションの統計が結合されます。 既定値は **OFF** です。  
  
 パーティションごとの統計がサポートされていない場合は、エラーが生成されます。 次の種類の統計では、増分統計がサポートされていません。  
  
-   ベース テーブルにパーティションで固定されていないインデックスを使用して作成された統計。  
-   Always On の読み取り可能なセカンダリ データベースに対して作成された統計。  
-   読み取り専用のデータベースに対して作成された統計。  
-   フィルター選択されたインデックスに対して作成された統計。  
-   ビューに対して作成された統計。  
-   内部テーブルに対して作成された統計。  
-   空間インデックスまたは XML インデックスを使用して作成された統計。  
  
**適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。  
  
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
  
 \<update_stats_stream_option> [!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]  

## <a name="permissions"></a>アクセス許可  
 これらのアクセス許可のいずれかが必要です。  
  
-   ALTER TABLE  
-   ユーザーがテーブルの所有者です  
-   **db_ddladmin** 固定データベース ロールのメンバーシップ  
  
## <a name="general-remarks"></a>全般的な解説  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 統計を構築する前に、サンプリングされた行を並べ替えるには、tempdb を使用できます。  
  
### <a name="statistics-for-external-tables"></a>外部テーブルの統計  
 外部テーブルの統計を作成するときに [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 、一時的なに外部テーブル インポート [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] テーブル、および統計を作成します。 サンプルの統計の場合は、サンプリングされた行のみがインポートされます。 大きな外部テーブルがある場合は、フル スキャン オプションではなく既定のサンプリングを使用する方がはるかに高速です。  
  
### <a name="statistics-with-a-filtered-condition"></a>条件がフィルター選択された統計情報  
 適切に定義されたデータのサブセットから選択するクエリでは、フィルター選択された統計情報を使用するとクエリのパフォーマンスを向上させることができます。 フィルター選択された統計情報では、統計情報に含まれるデータのサブセットを選択するために WHERE 句でフィルター述語を使用します。  
  
### <a name="when-to-use-create-statistics"></a>CREATE STATISTICS を使用する場合  
 CREATE STATISTICS を使用する場合について詳しくは、「[統計](../../relational-databases/statistics/statistics.md)」をご覧ください。  
  
### <a name="referencing-dependencies-for-filtered-statistics"></a>フィルター選択された統計情報の参照による依存関係  
 [sys.sql_expression_dependencies](../../relational-databases/system-catalog-views/sys-sql-expression-dependencies-transact-sql.md) カタログ ビューでは、フィルター選択された統計情報の述語の各列を、参照による依存関係として追跡します。 フィルター選択された統計情報の述語で定義されているテーブル列の定義を削除、名前変更、または変更することはできないので、フィルター選択された統計情報を作成する前に、テーブル列で実行する操作を検討してください。  
  
## <a name="limitations-and-restrictions"></a>制限事項と制約事項  
* テーブルの外部では、統計を更新することはできません。 外部テーブルの統計を更新するには、統計を削除して再作成します。  
* 統計オブジェクトごとに最大 64 列の一覧を取得できます。
* MAXDOP オプションは、STATS_STREAM、ROWCOUNT、PAGECOUNT オプションと互換性がありません。
* MAXDOP オプションは、Resource Governor ワークロード グループの MAX_DOP の設定によって制限されます (使用されている場合)。
* Azure SQL Database では、外部テーブルの CREATE と DROP STATISTICS はサポートされていません。
  
## <a name="examples"></a>例  

### <a name="examples-use-the-adventureworks-database"></a>使用例では、AdventureWorks データベースを使用します。  

### <a name="a-using-create-statistics-with-sample-number-percent"></a>A. CREATE STATISTICS を SAMPLE number PERCENT と共に使用する  
 次の例では、[!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] データベースの `Person` テーブルにある `BusinessEntityID` 列と `EmailPromotion` 列の 5% のランダムなサンプルを使用して、`ContactMail1` 統計情報を作成します。  
  
```sql  
CREATE STATISTICS ContactMail1  
    ON Person.Person (BusinessEntityID, EmailPromotion)  
    WITH SAMPLE 5 PERCENT;  
```  
  
### <a name="b-using-create-statistics-with-fullscan-and-norecompute"></a>B. CREATE STATISTICS を FULLSCAN および NORECOMPUTE と共に使用する  
 次の例では、`NamePurchase` テーブルの `BusinessEntityID` 列と `EmailPromotion` 列のすべての行を対象に、`Person` 統計情報を作成し、統計の自動再計算を無効にします。  
  
```sql  
CREATE STATISTICS NamePurchase  
    ON AdventureWorks2012.Person.Person (BusinessEntityID, EmailPromotion)  
    WITH FULLSCAN, NORECOMPUTE;  
```  
  
### <a name="c-using-create-statistics-to-create-filtered-statistics"></a>C. CREATE STATISTICS を使用してフィルター選択された統計情報を作成する  
 次の例では、フィルター選択された統計情報 `ContactPromotion1` を作成します。 [!INCLUDE[ssDE](../../includes/ssde-md.md)]によってデータの 50% がサンプリングされた後、`EmailPromotion` が 2 に等しい行が選択されます。  
  
```sql  
CREATE STATISTICS ContactPromotion1  
    ON Person.Person (BusinessEntityID, LastName, EmailPromotion)  
WHERE EmailPromotion = 2  
WITH SAMPLE 50 PERCENT;  
GO  
```  
  
### <a name="d-create-statistics-on-an-external-table"></a>D. 外部テーブルの統計を作成する  
 列の一覧を指定する以外に、外部テーブルの統計を作成するときに必要な決定事項は、統計を作成する際に行をサンプリングするか、すべての行をスキャンするかという点のみです。 Azure SQL Database では、外部テーブルの CREATE と DROP STATISTICS はサポートされていません。
  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] フル スキャンのオプション、統計を作成するを一時テーブルに外部テーブルからデータをインポートがかなり長くかかります。 大きなテーブルの場合、通常は既定のサンプリング方法で十分です。  
  
```sql  
--Create statistics on an external table and use default sampling.  
CREATE STATISTICS CustomerStats1 ON DimCustomer (CustomerKey, EmailAddress);  
  
--Create statistics on an external table and scan all the rows  
CREATE STATISTICS CustomerStats1 ON DimCustomer (CustomerKey, EmailAddress) WITH FULLSCAN;  
```  

### <a name="e-using-create-statistics-with-fullscan-and-persist_sample_percent"></a>E. CREATE STATISTICS を FULLSCAN および PERSIST_SAMPLE_PERCENT と共に使用する  
 次の例では、`Person` テーブルの `BusinessEntityID` 列と `EmailPromotion` 列のすべての行について `NamePurchase` 統計を作成し、サンプリング率を明示的に指定しない後続のすべての更新について 100 パーセントのサンプリング率を設定します。  
  
```sql  
CREATE STATISTICS NamePurchase  
    ON AdventureWorks2012.Person.Person (BusinessEntityID, EmailPromotion)  
    WITH FULLSCAN, PERSIST_SAMPLE_PERCENT = ON;  
```  
  
### <a name="examples-using-adventureworksdw-database"></a>AdventureWorksDW データベースの使用例。 
  
### <a name="f-create-statistics-on-two-columns"></a>F. 2 つの列の統計を作成する  
 次の例では、`DimCustomer` テーブルの `CustomerKey` 列と `EmailAddress` 列に基づいて、`CustomerStats1` 統計を作成します。 統計は、`Customer` テーブルの行の統計的に優位なサンプリングに基づいて作成されます。  
  
```sql  
CREATE STATISTICS CustomerStats1 ON DimCustomer (CustomerKey, EmailAddress);  
```  
  
### <a name="g-create-statistics-by-using-a-full-scan"></a>G. フル スキャンを使用して統計を作成する  
 次の例では、`DimCustomer` テーブルのすべての行のスキャンに基づいて `CustomerStatsFullScan` 統計を作成します。  
  
```sql  
CREATE STATISTICS CustomerStatsFullScan 
ON DimCustomer (CustomerKey, EmailAddress) WITH FULLSCAN;  
```  
  
### <a name="h-create-statistics-by-specifying-the-sample-percentage"></a>H. サンプル率を指定して統計を作成する  
 次の例では、`DimCustomer` テーブルの行の 50 パーセントのスキャンに基づいて `CustomerStatsSampleScan` 統計を作成します。  
  
```sql  
CREATE STATISTICS CustomerStatsSampleScan 
ON DimCustomer (CustomerKey, EmailAddress) WITH SAMPLE 50 PERCENT;  
```  
  
## <a name="see-also"></a>参照  
 [統計](../../relational-databases/statistics/statistics.md)   
 [UPDATE STATISTICS &#40;Transact-SQL&#41;](../../t-sql/statements/update-statistics-transact-sql.md)   
 [sp_updatestats &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-updatestats-transact-sql.md)   
 [DBCC SHOW_STATISTICS &#40;Transact-SQL&#41;](../../t-sql/database-console-commands/dbcc-show-statistics-transact-sql.md)   
 [DROP STATISTICS &#40;Transact-SQL&#41;](../../t-sql/statements/drop-statistics-transact-sql.md)   
 [sys.stats &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-stats-transact-sql.md)   
 [sys.stats_columns &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-stats-columns-transact-sql.md)  
  
  

