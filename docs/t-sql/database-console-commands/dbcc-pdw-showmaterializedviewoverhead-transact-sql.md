---
description: DBCC PDW_SHOWMATERIALIZEDVIEWOVERHEAD (Transact-SQL)
title: DBCC PDW_SHOWMATERIALIZEDVIEWOVERHEAD (Transact-SQL)
ms.custom: seo-dt-2019
ms.date: 07/03/2019
ms.prod: sql
ms.technology: data-warehouse
ms.prod_service: synapse-analytics, pdw
ms.reviewer: ''
ms.topic: language-reference
dev_langs:
- TSQL
author: XiaoyuMSFT
ms.author: xiaoyul
monikerRange: = azure-sqldw-latest
ms.openlocfilehash: 3c99b71fe0ba8a1a0bbfe1bcd929d4d6db093bdb
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104744442"
---
# <a name="dbcc-pdw_showmaterializedviewoverhead-transact-sql"></a>DBCC PDW_SHOWMATERIALIZEDVIEWOVERHEAD (Transact-SQL)  

[!INCLUDE [asa](../../includes/applies-to-version/asa.md)]

[!INCLUDE[ssSDW](../../includes/sssdw-md.md)] 内の具体化されたビュー用に維持される、ベース テーブル内の増分変更の数が表示されます。 オーバーヘッド比率は、TOTAL_ROWS / MAX (1, BASE_VIEW_ROWS) として計算されます。

![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則 (Transact-SQL)](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)
  
## <a name="syntax"></a>構文

```syntaxsql
DBCC PDW_SHOWMATERIALIZEDVIEWOVERHEAD ( " [ schema_name .] materialized_view_name  " )
[;]
```

[!INCLUDE[synapse-analytics-od-unsupported-syntax](../../includes/synapse-analytics-od-unsupported-syntax.md)]

## <a name="arguments"></a>引数

 *schema_name*     
 ビューが所属するスキーマの名前を指定します。

*materialized_view_name*   
具体化されたビューの名前です。

## <a name="remarks"></a>解説

ベース テーブルでのデータ変更によって具体化されたビューを更新し続けるため、データ ウェアハウス エンジンにより影響を受ける各ビューに追跡行が追加され、変更が反映されます。 具体化されたビューからの選択には、クラスター化列ストア インデックスのスキャンと増分変更の適用が含まれます。追跡行 (TOTAL_ROWS - BASE_VIEW_ROWS) は、ユーザーが具体化されたビューを再構築するまで削除されません。  

overhead_ratio は、TOTAL_ROWS/MAX(1, BASE_VIEW_ROWS) として計算されます。  高い場合は、SELECT のパフォーマンスが低下します。ユーザーは、具体化されたビューを再構築して、オーバーヘッドの比率を下げることができます。

## <a name="permissions"></a>アクセス許可  
  
VIEW DATABASE STATE 権限が必要です。  

## <a name="examples"></a>例  

### <a name="a-this-example-returns-the-overhead-ratio-of-a-materialized-view"></a>A. この例では、具体化されたビューのオーバーヘッド比率が返されます。

```sql
DBCC PDW_SHOWMATERIALIZEDVIEWOVERHEAD ( "dbo.MyIndexedView" )
```

出力:

|OBJECT_ID|BASE_VIEW_ROWS|TOTAL_ROWS|OVERHEAD_RATIO|
|--------|--------|--------|--------|  
|1234|1|3 |3.0 |

</br>

### <a name="b-this-example-shows-how-the-materialized-view-overhead-increases-as-data-changes-in-base-tables"></a>B. この例では、ベース テーブルのデータ変更に応じて、具体化されたビューのオーバーヘッドがどのように増加するかを示します

テーブルを作成する
```sql
CREATE TABLE t1 (c1 INT NOT NULL, c2 INT NOT NULL, c3 INT NOT NULL)
```
t1 に 5 行を挿入
```sql
INSERT INTO t1 VALUES (1, 1, 1)
INSERT INTO t1 VALUES (2, 2, 2) 
INSERT INTO t1 VALUES (3, 3, 3) 
INSERT INTO t1 VALUES (4, 4, 4) 
INSERT INTO t1 VALUES (5, 5, 5) 
```
具体化されたビュー MV1 の作成
```sql
CREATE MATERIALIZED VIEW MV1 
WITH (DISTRIBUTION = HASH(c1))  
AS
SELECT c1, COUNT(*) total_number 
FROM dbo.t1 WHERE c1 < 3
GROUP BY c1  
```
具体化されたビューから選択すると、2 つの行が返されます。

|c1|total_number|
|--------|--------| 
|1|1| 
|2|1|

ベース テーブルのデータが変更される前に、具体化されたビューのオーバーヘッドを確認します。
```sql
DBCC PDW_SHOWMATERIALIZEDVIEWOVERHEAD ("dbo.mv1")
```
出力:

|OBJECT_ID|BASE_VIEW_ROWS|TOTAL_ROWS|OVERHEAD_RATIO|
|--------|--------|--------|--------|  
|587149137|2|2 |1.00000000000000000 |

ベース テーブルを更新します。  このクエリにより、同じ行の同じ列が 100 回同じ値に更新されます。  具体化されたビューの内容は変更されません。
```sql
DECLARE @p INT
SELECT @p = 1
WHILE (@p < 101)
BEGIN
UPDATE t1 SET c1 = 1 WHERE c1 = 1
SELECT @p = @p+1
END  
```

具体化されたビューから選択すると、以前と同じ結果が返されます。  

|c1|total_number|
|--------|--------| 
|1|1| 
|2|1|

DBCC PDW_SHOWMATERIALIZEDVIEWOVERHEAD ("dbo.mv1") からの出力を次に示します。  具体化されたビュー (total_row - base_view_rows) に 100 行が追加され、その overhead_ratio が増加します。 

|OBJECT_ID|BASE_VIEW_ROWS|TOTAL_ROWS|OVERHEAD_RATIO|
|--------|--------|--------|--------|  
|587149137|2|102 |51.00000000000000000 |

具体化されたビューを再構築すると、データの増分変更の追跡行がすべて削除され、ビューのオーバーヘッド比率が減少します。  

```sql
ALTER MATERIALIZED VIEW dbo.MV1 REBUILD
GO
DBCC PDW_SHOWMATERIALIZEDVIEWOVERHEAD ("dbo.mv1")
```
出力

|OBJECT_ID|BASE_VIEW_ROWS|TOTAL_ROWS|OVERHEAD_RATIO|
|--------|--------|--------|--------|  
|587149137|2|2 |1.00000000000000000 |

## <a name="see-also"></a>関連項目

[具体化されたビューを使用したパフォーマンス チューニング](/azure/sql-data-warehouse/performance-tuning-materialized-views)   
[CREATE MATERIALIZED VIEW AS SELECT &#40;Transact-SQL&#41;](../statements/create-materialized-view-as-select-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)   
[ALTER MATERIALIZED VIEW &#40;Transact-SQL&#41;](../statements/alter-materialized-view-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)   
[EXPLAIN &#40;Transact-SQL&#41;](../queries/explain-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)   
[sys.pdw_materialized_view_column_distribution_properties &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-pdw-materialized-view-column-distribution-properties-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)   
[sys.pdw_materialized_view_distribution_properties &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-pdw-materialized-view-distribution-properties-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)   
[sys.pdw_materialized_view_mappings &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-pdw-materialized-view-mappings-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)   
[Azure Synapse Analytics と Parallel Data Warehouse のカタログ ビュー](../../relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views.md)   
[Azure Synapse Analytics でサポートされているシステム ビュー](/azure/sql-data-warehouse/sql-data-warehouse-reference-tsql-system-views)   
[Azure Synapse Analytics でサポートされている T-SQL ステートメント](/azure/sql-data-warehouse/sql-data-warehouse-reference-tsql-statements)