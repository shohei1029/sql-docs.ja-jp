---
description: CREATE MATERIALIZED VIEW AS SELECT (Transact-SQL)
title: CREATE MATERIALIZED VIEW AS SELECT (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/04/2020
ms.prod: sql
ms.prod_service: synapse-analytics
ms.reviewer: jrasnick
ms.technology: data-warehouse
ms.topic: reference
f1_keywords:
- CREATE VIEW
- VIEW_TSQL
- VIEW
- CREATE_VIEW_TSQL
- SCHEMABINDING_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- table creation [SQL Server], CREATE VIEW
- views [SQL Server], creating
- CREATE VIEW statement
- updatable partitioned views
- tables [SQL Server], virtual
- updatable views
- modifying partitioned views
- virtual tables [SQL Server]
- number of columns per view
- partitioned views [SQL Server], creating
- WITH ENCRYPTION clause
- WITH CHECK OPTION clause
- partitioned views [SQL Server], modifying
- partitioned views [SQL Server], replication
- distributed partitioned views [SQL Server]
- views [SQL Server], indexed views
- maximum number of columns per view
ms.assetid: aecc2f73-2ab5-4db9-b1e6-2f9e3c601fb9
author: XiaoyuMSFT
ms.author: xiaoyul
monikerRange: =azure-sqldw-latest
ms.openlocfilehash: a484f4d57281ad869df4f86a4d6cebfb7602c8e8
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104734172"
---
# <a name="create-materialized-view-as-select-transact-sql"></a>CREATE MATERIALIZED VIEW AS SELECT (Transact-SQL)  

[!INCLUDE [Azure Synapse Analytics](../../includes/applies-to-version/asa.md)]

この記事では、ソリューション開発用の [!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] の CREATE MATERIALIZED VIEW AS SELECT T-SQL ステートメントについて説明します。 この記事には、コード例も記載されています。

具体化されたビューでは、ビュー定義クエリから返されるデータを保持し、基になるテーブルのデータが変更されると自動的に更新されます。   これによって、複雑なクエリ (一般に結合と集計を含むクエリ) のパフォーマンスが向上すると共に、メンテナンス操作が簡単になります。   実行プランの自動一致機能により、置換するビューをオプティマイザーで検討する際、具体化されたビューをクエリで参照する必要はありません。  この機能により、データ エンジニアは、クエリを変更せずにクエリの応答時間を短縮するメカニズムとして、具体化されたビューを実装できます。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql
CREATE MATERIALIZED VIEW [ schema_name. ] materialized_view_name
    WITH (  
      <distribution_option>
    )
    AS <select_statement>
[;]

<distribution_option> ::=
    {  
        DISTRIBUTION = HASH ( distribution_column_name )  
      | DISTRIBUTION = ROUND_ROBIN  
    }

<select_statement> ::=
    SELECT select_criteria

```

[!INCLUDE[synapse-analytics-od-unsupported-syntax](../../includes/synapse-analytics-od-unsupported-syntax.md)]
  
## <a name="arguments"></a>引数

*schema_name*     
 ビューが所属するスキーマの名前を指定します。  
  
*materialized_view_name*   
ビューの名前です。 ビュー名は、識別子のルールに従っている必要があります。 ビューの所有者名の指定は省略可能です。  

*distribution option*     
サポートされる分布は、HASH および ROUND_ROBIN のみです。

*select_statement*   
具体化されたビューの定義の SELECT リストでは、以下の 2 つの条件の 1 つ以上が満たされている必要があります。
- SELECT リストに集計関数が含まれています。
- 具体化されたビューの定義で GROUP BY が使用されており、GROUP BY 内のすべての列が SELECT リストに含まれています。  GROUP BY 句では、最大 32 列使用できます。

具体化されたビューの定義の SELECT リストには、集計関数が必要です。  サポートされる集計には、MAX、MIN、AVG、COUNT、COUNT_BIG、SUM、VAR、STDEV が含まれます。

具体化されたビューの定義の SELECT リストで MIN/MAX 集計が使用される場合、以下の要件が適用されます。
 
- FOR_APPEND が必要です。  次に例を示します。
  ```sql 
  CREATE MATERIALIZED VIEW mv_test2  
  WITH (distribution = hash(i_category_id), FOR_APPEND)  
  AS
  SELECT MAX(i.i_rec_start_date) as max_i_rec_start_date, MIN(i.i_rec_end_date) as min_i_rec_end_date, i.i_item_sk, i.i_item_id, i.i_category_id
  FROM syntheticworkload.item i  
  GROUP BY i.i_item_sk, i.i_item_id, i.i_category_id
  ```

- 参照されるベース テーブルで UPDATE または DELETE が実行されると、具体化されたビューが無効になります。  この制限は、INSERT には適用されません。  具体化されたビューを再度有効にするには、REBUILD を指定して ALTER MATERIALIZED VIEW を実行します。
  
## <a name="remarks"></a>注釈

Azure データ ウェアハウスの具体化されたビューは、SQL Server のインデックス付きビューに似ています。具体化されたビューで集計関数がサポートされる点を除き、インデックス付きビューとほぼ同じ制限が共有されています (詳細については、「[Create Indexed Views (インデックス付きビューを作成する)](../../relational-databases/views/create-indexed-views.md)」 を参照してください)。   

>[!Note]
>CREATE MATERIALIZED VIEW で COUNT、DISTINCT、COUNT (DISTINCT expression)、COUNT_BIG (DISTINCT expression) はサポートされていませんが、これらの関数を指定した SELECT クエリを使用すると、具体化されたビューの利点が得られ、パフォーマンスをより高速化することができます。これは、Synapse SQL オプティマイザーを使用すると、既存の具体化されたビューと一致するようにユーザー クエリの集計を自動的に再作成できるためです。  詳細については、この記事の例のセクションを確認してください。 

CREATE MATERIALIZED VIEW AS SELECT で APPROX_COUNT_DISTINCT はサポートされません。

具体化されたビューでは、CLUSTERED COLUMNSTORE INDEX のみがサポートされます。 

具体化されたビューでは、他のビューを参照できません。  

DDM 列が具体化されたビューの一部でない場合でも、動的データ マスク (DDM) を使用するテーブルに具体化されたビューを作成することはできません。  テーブル列が、アクティブな具体化されたビューの一部であるか、無効な具体化されたビューの一部である場合、DDM をこの列に追加することはできません。  

行レベルのセキュリティが有効になっているテーブルには具体化されたビューを作成できません。

具体化されたビューは、パーティション テーブル上で作成できます。  パーティションの SPLIT/MERGE は、具体化されたビューのベース テーブルでサポートされていますが、パーティションの SWITCH はサポートされていません。  
 
具体化されたビューで参照されるテーブル上では、ALTER TABLE SWITCH はサポートされません。 ALTER TABLE SWITCH を使用する前に、具体化されたビューを無効にするか、ドロップします。 以下のシナリオでは、具体化されたビューを作成する際、このビューに新しい列を追加する必要があります。

|シナリオ|具体化されたビューに追加する新しい列|コメント|  
|-----------------|---------------|-----------------|
|具体化されたビューの定義の SELECT リストに COUNT_BIG() が欠落しています| COUNT_BIG (*) |具体化されたビューを作成する際に自動的に追加されます。  ユーザーによる操作は不要です。|
|具体化されたビューの定義の SELECT リストでユーザーが SUM(a) を指定しています。また、"a" は null 許容式です |COUNT_BIG (a) |ユーザーは、具体化されたビューの定義で式 "a" を手動で追加する必要があります。|
|具体化されたビューの定義の SELECT リストでユーザーが AVG(a) を指定しています。ここで "a" は式です。|SUM(a), COUNT_BIG(a)|具体化されたビューを作成する際に自動的に追加されます。  ユーザーによる操作は不要です。|
|具体化されたビューの定義の SELECT リストでユーザーが STDEV(a) を指定しています。ここで "a" は式です。|SUM(a), COUNT_BIG(a), SUM(square(a))|具体化されたビューを作成する際に自動的に追加されます。  ユーザーによる操作は不要です。 |
| | | |

具体化されたビューは、一度作成されると、SQL Server Management Studio 内の [!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] インスタンスのビュー フォルダーの下に表示されます。

ユーザーは [SP_SPACEUSED](../../relational-databases/system-stored-procedures/sp-spaceused-transact-sql.md?view=azure-sqldw-latest&preserve-view=true) および [DBCC PDW_SHOWSPACEUSED](../database-console-commands/dbcc-pdw-showspaceused-transact-sql.md?view=azure-sqldw-latest&preserve-view=true) を実行して、具体化されたビューによって使用されている領域を確認できます。  

具体化されたビューは、DROP VIEW でドロップできます。  ALTER MATERIALIZED VIEW を使用して、具体化されたビューを無効にしたり、リビルドしたりできます。   

具体化されたビューは、自動クエリ最適化メカニズムです。  ユーザーは、具体化されたビューに直接クエリを実行する必要はありません。  ユーザー クエリが送信されると、エンジンがクエリ オブジェクトに対するユーザーのアクセス許可を確認し、ユーザーがクエリでテーブルまたは通常のビューにアクセスできない場合は、クエリが実行することなく失敗します。  ユーザーのアクセス許可が確認された場合、オプティマイザーが、対応する具体化されたビューを自動的に使用してクエリを実行し、パフォーマンスを向上させることができます。  ユーザーは、クエリの実行がベース テーブルと具体化されたビューのどちらのクエリによって行われるかに関係なく、同じデータを返します。  

SQL Server Management Studio 内の説明プランとグラフィカルな推定実行プランを見ると、クエリ実行の際、具体化されたビューがクエリ オプティマイザーで考慮されるかどうかがわかります。 SQL Server Management Studio 内のグラフィカルな推定実行プランを見ると、クエリ実行の際、具体化されたビューがクエリ オプティマイザーで考慮されるかどうかがわかります。

新しい具体化されたビューから SQL ステートメントでメリットが得られるかどうかを確認するには、`WITH_RECOMMENDATIONS` を指定して `EXPLAIN` コマンドを実行します。  詳細については、「[EXPLAIN (Transact-SQL)](../queries/explain-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)」を参照してください。

## <a name="ownership"></a>所有権
- ベース テーブルの所有者と、作成する具体化されたビューが同じでない場合は具体化されたビューを作成できません。
- 具体化されたビューとそのベース テーブルは、異なるスキーマに配置できます。 具体化されたビューを作成すると、ビューのスキーマ所有者が自動的に具体化されたビューの所有者になり、このビューの所有権を変更することはできません。     

## <a name="permissions"></a>アクセス許可
具体化されたビューを作成するには、オブジェクトの所有権の要件を満たすことに加えて、次の権限が必要です。 
1) データベースの CREATE VIEW 権限
2) 具体化されたビューのベース テーブルに対する SELECT 権限
3) ベース テーブルを含むスキーマに対する REFERENCES 権限
4) 具体化されたビューを含むスキーマに対する ALTER 権限 


## <a name="example"></a>例
A. この例には、Synapse SQL オプティマイザーで具体化されたビューを自動的に使用してクエリを実行し、パフォーマンスを向上させる方法が示されています。これは、クエリで、COUNT(DISTINCT expression) などの、CREATE MATERIALIZED VIEW でサポートされていない関数を使用する場合でも同様です。 これまで完了するのに数秒かかっていたクエリが、ユーザー クエリに変更を加えることなく 1 秒未満で終了するようになりました。   

``` sql 

-- Create a table with ~536 million rows
create table t(a int not null, b int not null, c int not null) with (distribution=hash(a), clustered columnstore index);

insert into t values(1,1,1);

declare @p int =1;
while (@P < 30)
    begin
    insert into t select a+1,b+2,c+3 from t;  
    select @p +=1;
end

-- A SELECT query with COUNT_BIG (DISTINCT expression) took multiple seconds to complete and it reads data directly from the base table a. 
select a, count_big(distinct b) from t group by a;

-- Create two materialized views, not using COUNT_BIG(DISTINCT expression).
create materialized view V1 with(distribution=hash(a)) as select a, b from dbo.t group by a, b;

-- Clear all cache.

DBCC DROPCLEANBUFFERS;
DBCC freeproccache;

-- Check the estimated execution plan in SQL Server Management Studio.  It shows the SELECT query is first step (GET operator) is to read data from the materialized view V1, not from base table a.
select a, count_big(distinct b) from t group by a;

-- Now execute this SELECT query.  This time it took sub-second to complete because Synapse SQL engine automatically matches the query with materialized view V1 and uses it for faster query execution.  There was no change in the user query.

DECLARE @timerstart datetime2, @timerend datetime2;
SET @timerstart = sysdatetime();

select a, count_big(distinct b) from t group by a;

SET @timerend = sysdatetime()
select DATEDIFF(ms,@timerstart,@timerend);

```

B. この例では、User1 が所有するテーブルに User2 が具体化されたビューを作成します。  具体化されたビューは User1 によって所有されています。
```sql
/****************************************************************
Setup:
SchemaX owner = DBO
SchemaX.T1 owner = User1
SchemaX.T2 owner = User1
SchemaY owner = User1
*****************************************************************/
CREATE USER User1 WITHOUT LOGIN ;
CREATE USER User2 WITHOUT LOGIN ;
CREATE SCHEMA SchemaX;  
CREATE SCHEMA SchemaY AUTHORIZATION User1;
GO
CREATE TABLE [SchemaX].[T1] (   [vendorID] [varchar](255) Not NULL, [totalAmount] [float] Not NULL, [puYear] [int] NULL );
CREATE TABLE [SchemaX].[T2] (   [vendorID] [varchar](255) Not NULL, [totalAmount] [float] Not NULL, [puYear] [int] NULL);
GO
ALTER AUTHORIZATION ON OBJECT::SchemaX.[T1] TO User1;
ALTER AUTHORIZATION ON OBJECT::SchemaX.[T2] TO User1;

/*****************************************************************************
For user2 to create a MV in SchemaY on SchemaX.T1 and SchemaX.T2, user2 needs:
1. CREATE VIEW permission in the database
2. REFERENCES permission on the schema1
3. SELECT permission on base table T1, T2  
4. ALTER permission on SchemaY
******************************************************************************/
GRANT CREATE VIEW to User2;
GRANT REFERENCES ON SCHEMA::SchemaX to User2;  
GRANT SELECT ON OBJECT::SchemaX.T1 to User2; 
GRANT SELECT ON OBJECT::SchemaX.T2 to User2;
GRANT ALTER ON SCHEMA::SchemaY to User2; 
GO
EXECUTE AS USER = 'User2';  
GO
CREATE materialized VIEW [SchemaY].MV_by_User2 with(distribution=round_robin) 
as 
        select A.vendorID, sum(A.totalamount) as S, Count_Big(*) as T 
        from [SchemaX].[T1] A
        inner join [SchemaX].[T2] B on A.vendorID = B.vendorID group by A.vendorID ;
GO
revert;
GO
```

## <a name="see-also"></a>関連項目

[具体化されたビューを使用したパフォーマンス チューニング](/azure/sql-data-warehouse/performance-tuning-materialized-views)   
[ALTER MATERIALIZED VIEW &#40;Transact-SQL&#41;](./alter-materialized-view-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)      
[DROP VIEW](./drop-view-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)  
[EXPLAIN &#40;Transact-SQL&#41;](../queries/explain-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)   
[sys.pdw_materialized_view_column_distribution_properties &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-pdw-materialized-view-column-distribution-properties-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)   
[sys.pdw_materialized_view_distribution_properties &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-pdw-materialized-view-distribution-properties-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)   
[sys.pdw_materialized_view_mappings &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-pdw-materialized-view-mappings-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)   
[DBCC PDW_SHOWMATERIALIZEDVIEWOVERHEAD &#40;Transact-SQL&#41;](../database-console-commands/dbcc-pdw-showmaterializedviewoverhead-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)   
[[!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] と [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] カタログ ビュー](../../relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views.md)   
[Azure [!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] でサポートされているシステム ビュー](/azure/sql-data-warehouse/sql-data-warehouse-reference-tsql-system-views)   
[Azure [!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] でサポートされている T-SQL ステートメント](/azure/sql-data-warehouse/sql-data-warehouse-reference-tsql-statements)
