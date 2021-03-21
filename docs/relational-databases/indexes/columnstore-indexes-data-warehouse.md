---
description: 列ストア インデックス - データ ウェアハウス
title: 列ストア インデックス - データ ウェアハウス | Microsoft Docs
ms.custom: ''
ms.date: 12/01/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: table-view-index
ms.topic: conceptual
ms.assetid: 21fd153b-116d-47fc-a926-f1528299a391
author: MikeRayMSFT
ms.author: mikeray
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 05b755ac4e7ee4cfa9b6ff1c8d7224839564c89c
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104754892"
---
# <a name="columnstore-indexes---data-warehouse"></a>列ストア インデックス - データ ウェアハウス
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  列ストア インデックスは、パーティション分割と共に [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] データ ウェアハウスの構築に不可欠な機能です。  
  
## <a name="whats-new"></a>新着情報  
 [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] では、列ストアのパフォーマンスを向上させるために、次の機能が導入されています。  
  
-   AlwaysOn で、読み取り可能なセカンダリ レプリカで列ストア インデックスのクエリをサポートします。  
-   複数のアクティブな結果セット (MARS) で、列ストア インデックスをサポートします。  
-   新しい動的管理ビュー [sys.dm_db_column_store_row_group_physical_stats &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-column-store-row-group-physical-stats-transact-sql.md) は、パフォーマンスのトラブルシューティングに関する情報を行グループ レベルで提供します。  
-   列ストア インデックスでのシングル スレッド クエリは、バッチ モードで実行できます。 以前は、バッチ モードで実行できるのはマルチ スレッド クエリのみでした。  
-   `SORT` 演算子は、バッチ モードで実行されます。  
-   マルチ `DISTINCT` 演算子は、バッチ モードで実行されます。  
-   ウィンドウ集計がデータベース互換性レベル 130 以上のバッチ モードで実行されるようになりました。  
-   集計を効率的に処理するための集計プッシュ ダウン。 これは、すべてのデータベース互換性レベルでサポートされています。  
-   文字列の述語を効率的に処理するための文字列述語プッシュ ダウン。 これは、すべてのデータベース互換性レベルでサポートされています。  
-   データベース互換性レベル 130 以上でのスナップショット分離。  
  
## <a name="improve-performance-by-combining-nonclustered-and-columnstore-indexes"></a>非クラスター化インデックスと列ストア インデックスを組み合わせてパフォーマンスを改善する  
 [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 以降では、クラスター化列ストア インデックスに非クラスター化インデックスを定義できます。   
  
### <a name="example-improve-efficiency-of-table-seeks-with-a-nonclustered-index"></a>例: 非クラスター化インデックスを使用してテーブルの検索効率を改善する  
 データ ウェアハウスでのテーブルの検索効率を改善するために、テーブルの検索でクエリが最高のパフォーマンスを発揮するように設計された非クラスター化インデックスを作成できます。 たとえば、一致する値を見つけるクエリや、値の小さな範囲を返すクエリは、列ストア インデックスではなく B ツリー インデックスに対して実行したほうが高いパフォーマンスを発揮します。 このようなクエリでは、列ストア インデックスを介したフル テーブル スキャンは必要ありません。B ツリー インデックスを介したバイナリ検索を実行すると、よりすばやく正しい結果が返されます。  
  
```sql  
--BASIC EXAMPLE: Create a nonclustered index on a columnstore table.  
  
--Create the table  
CREATE TABLE t_account (  
    AccountKey int NOT NULL,  
    AccountDescription nvarchar (50),  
    AccountType nvarchar(50),  
    UnitSold int  
);  
GO  
  
--Store the table as a columnstore.  
CREATE CLUSTERED COLUMNSTORE INDEX taccount_cci ON t_account;  
GO  
  
--Add a nonclustered index.  
CREATE UNIQUE INDEX taccount_nc1 ON t_account (AccountKey);  
```  
  
### <a name="example-use-a-nonclustered-index-to-enforce-a-primary-key-constraint-on-a-columnstore-table"></a>例: 非クラスター化インデックスを使用して列ストア テーブルに主キー制約を適用する  
 仕様により、クラスター化された主キー制約を列ストア テーブルに適用することはできません。 ただし、列ストア テーブルで非クラスター化インデックスを使用して、主キー制約を適用できるようになりました。 主キーは非 NULL 列での UNIQUE 制約に相当し、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] は UNIQUE 制約を非クラスター化インデックスとして実装します。 これらの事実を組み合わせて、次の例では、非 NULL 列 accountkey に UNIQUE 制約を定義しています。 その結果、非クラスター化インデックスにより、非 NULL 列の UNIQUE 制約として主キー制約が適用されます。  
  
 次に、テーブルはクラスター化列ストア インデックスに変換されます。 変換中は、非クラスター化インデックスが保持されます。 その結果、クラスター化列ストア インデックスに、主キー制約を適用する非クラスター化インデックスが含まれます。 列ストア テーブルでの更新または挿入は非クラスター化インデックスにも影響するため、UNIQUE 制約と非 NULL に違反する操作を行うと、操作全体の失敗につながります。  
  
 その結果、列ストア インデックスに、両方のインデックスに主キー制約を適用する非クラスター化インデックスが含まれます。  
  
```sql
--EXAMPLE: Enforce a primary key constraint on a columnstore table.   
  
--Create a rowstore table with a unique constraint.  
--The unique constraint is implemented as a nonclustered index.  
CREATE TABLE t_account (  
    AccountKey int NOT NULL,  
    AccountDescription nvarchar (50),  
    AccountType nvarchar(50),  
    UnitSold int,  
  
    CONSTRAINT uniq_account UNIQUE (AccountKey)  
);  
  
--Store the table as a columnstore.   
--The unique constraint is preserved as a nonclustered index on the columnstore table.  
CREATE CLUSTERED COLUMNSTORE INDEX t_account_cci ON t_account  
  
--By using the previous two steps, every row in the table meets the UNIQUE constraint  
--on a non-NULL column.  
--This has the same end-result as having a primary key constraint  
--All updates and inserts must meet the unique constraint on the nonclustered index or they will fail.  
  
--If desired, add a foreign key constraint on AccountKey.  
  
ALTER TABLE [dbo].[t_account]  
WITH CHECK ADD FOREIGN KEY([AccountKey]) REFERENCES my_dimension(Accountkey); 
```  
  
### <a name="improve-performance-by-enabling-row-level-and-row-group-level-locking"></a>行レベルおよび行グループ レベルのロックを有効にしてパフォーマンスを改善する  
 列ストア インデックス機能で非クラスター化インデックスを補完するために、[!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] には、選択、更新、および削除の各操作に対してより細分化されたロック機能が用意されています。 クエリの実行では、非クラスター化インデックスに対するインデックス検索に行レベルのロックを使用できます。また、列ストア インデックスに対するテーブル全体のスキャンに行グループ レベルのロックを使用できます。 行レベルのロックと行グループ レベルのロックを適切に使用すると、読み取り/書き込みのコンカレンシー度を高めることができます。  
  
```sql  
--Granular locking example  
--Store table t_account as a columnstore table.  
CREATE CLUSTERED COLUMNSTORE INDEX taccount_cci ON t_account  
GO  
  
--Add a nonclustered index for use with this example  
CREATE UNIQUE INDEX taccount_nc1 ON t_account (AccountKey);  
GO  
  
--Look at locking with access through the nonclustered index  
SET TRANSACTION ISOLATION LEVEL repeatable read;  
GO  
  
BEGIN TRAN  
    -- The query plan chooses a seek operation on the nonclustered index  
    -- and takes the row lock  
    SELECT * FROM t_account WHERE AccountKey = 100;  
END TRAN  
```  
  
### <a name="snapshot-isolation-and-read-committed-snapshot-isolations"></a>スナップショット分離と Read Committed スナップショット分離  
 列ストア インデックスのクエリに対し、トランザクションの一貫性を保証するにはスナップショット分離 (SI) を使用し、ステートメント レベルの一貫性を保証するには Read Committed スナップショット分離 (RCSI) を使用します。 これにより、データ ライターをブロックすることなくクエリを実行できるようになります。 また、ブロック不可の動作は、複雑なトランザクションのデッドロックの可能性を大幅に軽減します。 詳細については、MSDN の「 [SQL Server でのスナップショット分離](https://msdn.microsoft.com/library/tcbchxcb\(v=vs.110\).aspx) 」を参照してください。  
  
## <a name="see-also"></a>参照  
 [列ストア インデックスの設計ガイダンス](../../relational-databases/indexes/columnstore-indexes-design-guidance.md)   
 [列ストア インデックスのデータ読み込みガイダンス](../../relational-databases/indexes/columnstore-indexes-data-loading-guidance.md)   
 [列ストア インデックスのクエリ パフォーマンス](../../relational-databases/indexes/columnstore-indexes-query-performance.md)   
 [列ストアを使用したリアルタイム運用分析の概要](../../relational-databases/indexes/get-started-with-columnstore-for-real-time-operational-analytics.md)   
 [インデックスの再構成と再構築](../../relational-databases/indexes/reorganize-and-rebuild-indexes.md)    
 [列ストア インデックスのアーキテクチャ](../../relational-databases/sql-server-index-design-guide.md#columnstore_index) 
  
