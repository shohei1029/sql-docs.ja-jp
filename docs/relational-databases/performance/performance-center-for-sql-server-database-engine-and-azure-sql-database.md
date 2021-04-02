---
title: パフォーマンス センター
description: SQL Server データベース エンジンと Azure SQL Database のパフォーマンスについて必要とする情報を見つけます。
ms.custom: seo-dt-2019
ms.date: 12/11/2018
ms.prod: sql
ms.reviewer: ''
ms.technology: performance
ms.topic: conceptual
f1_keywords:
- Performance (SQL Server)
- Performance (SQL Database)
helpviewer_keywords:
- SQL Server, performance
- performance (SQL Server)
- database performance (SQL Server)
- SQL Database (Performance)
- performance (SQL Database)
- database performance (SQL Database)
ms.assetid: 301204b2-140d-4495-98ed-021a9b5025f5
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 6dca7abfb1cccbea021a2ee7650b78079ca3f777
ms.sourcegitcommit: 295b9dfc758471ef7d238a2b0f92f93e34acbb1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106054885"
---
# <a name="performance-center-for-sql-server-database-engine-and-azure-sql-database"></a>SQL Server データベース エンジンと Azure SQL Database のパフォーマンス センター
[!INCLUDE [SQL Server Azure SQL Database](../../includes/applies-to-version/sql-asdb.md)]
  このページでは、 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] および [!INCLUDE[ssSDSFull](../../includes/sssdsfull-md.md)]でのパフォーマンスに関して必要になる情報の検索に役立つリンクを示します。  
  
 **凡例**  
  
 ![使用可能な機能アイコンについて説明している凡例のスクリーンショット。](../../relational-databases/performance/media/security-center-legend.PNG "security-center-legend")  
  
## <a name="configuration-options-for-performance"></a>パフォーマンスの構成オプション  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] レベルのさまざまな構成オプションを使用してデータベース エンジンのパフォーマンスを制御できます。 [!INCLUDE[ssSDSFull](../../includes/sssdsfull-md.md)]、これらの最適化のすべてではありませんがほとんどが自動的に行われます。  
  
|Options|説明|  
|-|-|  
|**ディスク構成オプション**|:::image type="icon" source="../../relational-databases/performance/media/security-center-sqlserver.png"::: [ディスクのストライピングと RAID](https://technet.microsoft.com/library/ms190764\(v=sql.105\).aspx)|  
|**データとログ ファイルの構成オプション**|:::image type="icon" source="../../relational-databases/performance/media/security-center-sqlserver.png"::: [別々のドライブへのデータ ファイルとログ ファイルの配置](../../relational-databases/policy-based-management/place-data-and-log-files-on-separate-drives.md)<br />:::image type="icon" source="../../relational-databases/performance/media/security-center-sqlserver.png"::: [データ ファイルとログ ファイルの既定の場所の表示または変更 &#40;SQL Server Management Studio&#41;](../../database-engine/configure-windows/view-or-change-the-default-locations-for-data-and-log-files.md)|  
|**TempDB の構成オプション**|:::image type="icon" source="../../relational-databases/performance/media/security-center-sqlserver.png"::: [TempDB でのパフォーマンスの強化](../databases/tempdb-database.md)<br />:::image type="icon" source="../../relational-databases/performance/media/security-center-sqlserver.png"::: [データベース エンジンの構成 - TempDB](../../database-engine/install-windows/install-sql-server.md)<br />:::image type="icon" source="../../relational-databases/performance/media/security-center-sqlserver.png"::: [Azure VM で SSD を使用した SQL Server TempDB とバッファー プール拡張機能の保存](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/)<br />:::image type="icon" source="../../relational-databases/performance/media/security-center-sqlserver.png"::: [Azure Virtual Machines での SQL Server 用一時ディスクに関するディスクとパフォーマンスのベスト プラクティス](/aspnet/core/performance/performance-best-practices)|  
|**サーバー構成オプション**|**プロセッサ構成オプション**<br /><br />:::image type="icon" source="../../relational-databases/performance/media/security-center-sqlserver.png"::: [affinity mask サーバー構成オプション](../../database-engine/configure-windows/affinity-mask-server-configuration-option.md)<br />:::image type="icon" source="../../relational-databases/performance/media/security-center-sqlserver.png"::: [affinity Input-Output mask サーバー構成オプション](../../database-engine/configure-windows/affinity-input-output-mask-server-configuration-option.md)<br />:::image type="icon" source="../../relational-databases/performance/media/security-center-sqlserver.png"::: [affinity64 mask サーバー構成オプション](../../database-engine/configure-windows/affinity64-mask-server-configuration-option.md)<br />:::image type="icon" source="../../relational-databases/performance/media/security-center-sqlserver.png"::: [affinity64 Input-Output mask サーバー構成オプション](../../database-engine/configure-windows/affinity64-input-output-mask-server-configuration-option.md)<br />:::image type="icon" source="../../relational-databases/performance/media/security-center-sqlserver.png"::: [max worker threads サーバー構成オプションの構成](../../database-engine/configure-windows/configure-the-max-worker-threads-server-configuration-option.md)<br /><br />**メモリ構成オプション**<br /><br />:::image type="icon" source="../../relational-databases/performance/media/security-center-sqlserver.png"::: [サーバー メモリのサーバー構成オプション](../../database-engine/configure-windows/server-memory-server-configuration-options.md)<br /><br />**インデックス構成オプション**<br /><br />:::image type="icon" source="../../relational-databases/performance/media/security-center-sqlserver.png"::: [fill factor サーバー構成オプションの構成](../../database-engine/configure-windows/configure-the-fill-factor-server-configuration-option.md)<br /><br />**クエリ構成オプション**<br /><br />:::image type="icon" source="../../relational-databases/performance/media/security-center-sqlserver.png"::: [min memory per query サーバー構成オプションの構成](../../database-engine/configure-windows/configure-the-min-memory-per-query-server-configuration-option.md)<br />:::image type="icon" source="../../relational-databases/performance/media/security-center-sqlserver.png"::: [query governor cost limit サーバー構成オプションの構成](../../database-engine/configure-windows/configure-the-query-governor-cost-limit-server-configuration-option.md)<br />:::image type="icon" source="../../relational-databases/performance/media/security-center-sqlserver.png"::: [max degree of parallelism サーバー構成オプションの構成](../../database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option.md)<br />:::image type="icon" source="../../relational-databases/performance/media/security-center-sqlserver.png"::: [cost threshold for parallelism サーバー構成オプションの構成](../../database-engine/configure-windows/configure-the-cost-threshold-for-parallelism-server-configuration-option.md)<br />:::image type="icon" source="../../relational-databases/performance/media/security-center-sqlserver.png"::: [optimize for ad hoc workloads サーバー構成オプション](../../database-engine/configure-windows/optimize-for-ad-hoc-workloads-server-configuration-option.md)<br /><br />**バックアップ構成オプション**<br /><br />:::image type="icon" source="../../relational-databases/performance/media/security-center-sqlserver.png"::: [backup compression default サーバー構成オプションの表示または構成](../../database-engine/configure-windows/view-or-configure-the-backup-compression-default-server-configuration-option.md)|  
|**データベース構成最適化オプション**|:::image type="icon" source="../../relational-databases/performance/media/security-center-sqlserver.png"::: [データ圧縮](../../relational-databases/data-compression/data-compression.md)<br />:::image type="icon" source="../../relational-databases/performance/media/security-center-both.png"::: [データベースの互換性レベルの表示または変更](../../relational-databases/databases/view-or-change-the-compatibility-level-of-a-database.md)<br />:::image type="icon" source="../../relational-databases/performance/media/security-center-both.png"::: [ALTER DATABASE SCOPED CONFIGURATION (Transact-SQL)](../../t-sql/statements/alter-database-scoped-configuration-transact-sql.md)|  
|**テーブル構成最適化**|:::image type="icon" source="../../relational-databases/performance/media/security-center-sqlserver.png"::: [パーティション テーブルとパーティション インデックス](../../relational-databases/partitions/partitioned-tables-and-indexes.md)|  
|**Azure Virtual Machine でのデータベース エンジンのパフォーマンス**|:::image type="icon" source="../../relational-databases/performance/media/security-center-sqlserver.png"::: [クイック チェック リスト](/aspnet/core/performance/performance-best-practices)<br />:::image type="icon" source="../../relational-databases/performance/media/security-center-sqlserver.png"::: [仮想マシンのサイズとストレージ アカウントに関する考慮事項](/aspnet/core/performance/performance-best-practices)<br />:::image type="icon" source="../../relational-databases/performance/media/security-center-sqlserver.png"::: [ディスクとパフォーマンスに関する考慮事項](/aspnet/core/performance/performance-best-practices)<br />:::image type="icon" source="../../relational-databases/performance/media/security-center-sqlserver.png"::: [I/O パフォーマンスに関する考慮事項](/aspnet/core/performance/performance-best-practices)<br />:::image type="icon" source="../../relational-databases/performance/media/security-center-sqlserver.png"::: [機能固有のパフォーマンスに関する考慮事項](/aspnet/core/performance/performance-best-practices)| 
|**パフォーマンスのベスト プラクティスと SQL Server on Linux の構成ガイドライン**|:::image type="icon" source="../../relational-databases/performance/media/security-center-sqlserver.png"::: [SQL Server の構成](../../linux/sql-server-linux-performance-best-practices.md#sql-server-configuration)<br />:::image type="icon" source="../../relational-databases/performance/media/security-center-sqlserver.png"::: [Linux OS の構成](../../linux/sql-server-linux-performance-best-practices.md#linux-os-configuration)|

> [!IMPORTANT]
> 追加の考慮事項は次の場所にあります。    
> -  [SQL Server 2012 と SQL Server 2014 でパフォーマンス ワークロードの高いときに推奨される更新プログラムと構成オプション](https://support.microsoft.com/help/2964518)
> -  [SQL Server 2017 と 2016 でパフォーマンス ワークロードの高いときに推奨される更新プログラムと構成オプション](https://support.microsoft.com/help/4465518)

## <a name="query-performance-options"></a>クエリ パフォーマンス オプション  
  
|オプション|説明|  
|-|-|  
|:::image type="icon" source="../../relational-databases/performance/media/security-center-both.png"::: **[インデックス](../../relational-databases/indexes/indexes.md)**|[インデックスの再編成と再構築](../../relational-databases/indexes/reorganize-and-rebuild-indexes.md)<br />[インデックスの FILL FACTOR の指定](../../relational-databases/indexes/specify-fill-factor-for-an-index.md)<br />[並列インデックス操作の構成](../../relational-databases/indexes/configure-parallel-index-operations.md)<br />[インデックスの SORT_IN_TEMPDB オプション](../../relational-databases/indexes/sort-in-tempdb-option-for-indexes.md)<br />[フルテキスト インデックスのパフォーマンスの向上](../../relational-databases/search/improve-the-performance-of-full-text-indexes.md)<br />[min memory per query サーバー構成オプションの構成](../../database-engine/configure-windows/configure-the-min-memory-per-query-server-configuration-option.md)<br />[index create memory サーバー構成オプションの構成](../../database-engine/configure-windows/configure-the-index-create-memory-server-configuration-option.md)|  
|:::image type="icon" source="../../relational-databases/performance/media/security-center-both.png"::: **[パーティション テーブルとパーティション インデックス](../../relational-databases/partitions/partitioned-tables-and-indexes.md)**|[パーティション分割の利点](../partitions/partitioned-tables-and-indexes.md)|  
|:::image type="icon" source="../../relational-databases/performance/media/security-center-both.png"::: **[結合](../../relational-databases/performance/joins.md)**|[結合の基礎](../../relational-databases/performance/joins.md#fundamentals)<br />[ネステッド ループ結合](../../relational-databases/performance/joins.md#nested_loops)<br />[マージ結合](../../relational-databases/performance/joins.md#merge)<br />[ハッシュ結合](../../relational-databases/performance/joins.md#hash)|  
|:::image type="icon" source="../../relational-databases/performance/media/security-center-both.png"::: **[サブクエリ](../../relational-databases/performance/subqueries.md)**|[サブクエリの基礎](../../relational-databases/performance/subqueries.md#fundamentals)<br />[相関サブクエリ](../../relational-databases/performance/subqueries.md#correlated)<br />[サブクエリの種類](../../relational-databases/performance/subqueries.md#types)|  
|:::image type="icon" source="../../relational-databases/performance/media/security-center-both.png"::: **[ストアド プロシージャ](../stored-procedures/stored-procedures-database-engine.md)**|[CREATE PROCEDURE &#40;Transact-SQL&#41;](../../t-sql/statements/create-procedure-transact-sql.md#best-practices)|  
|:::image type="icon" source="../../relational-databases/performance/media/security-center-both.png"::: **[ユーザー定義関数](../user-defined-functions/user-defined-functions.md)**|[CREATE FUNCTION &#40;Transact-SQL&#41;](../../t-sql/statements/create-function-transact-sql.md#best-practices)<br />[ユーザー定義関数の作成 &#40;データベース エンジン&#41;](../user-defined-functions/create-user-defined-functions-database-engine.md)|  
|:::image type="icon" source="../../relational-databases/performance/media/security-center-both.png"::: **並列処理の最適化**|[max worker threads サーバー構成オプションの構成](../../database-engine/configure-windows/configure-the-max-worker-threads-server-configuration-option.md)<br />[ALTER DATABASE SCOPED CONFIGURATION (Transact-SQL)](../../t-sql/statements/alter-database-scoped-configuration-transact-sql.md)|  
|:::image type="icon" source="../../relational-databases/performance/media/security-center-both.png"::: **クエリ オプティマイザーの最適化**|[ALTER DATABASE SCOPED CONFIGURATION (Transact-SQL)](../../t-sql/statements/alter-database-scoped-configuration-transact-sql.md)<br />[USE HINT クエリ ヒント](../../t-sql/queries/hints-transact-sql-query.md#use_hint)|  
|:::image type="icon" source="../../relational-databases/performance/media/security-center-both.png"::: **[統計](../../relational-databases/statistics/statistics.md)**|[統計を更新する場合](../statistics/statistics.md)<br />[統計の更新](../../relational-databases/statistics/update-statistics.md)|  
|:::image type="icon" source="../../relational-databases/performance/media/security-center-both.png":::  **[インメモリ OLTP &#40;インメモリ最適化&#41;](../../relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization.md)**|[メモリ最適化テーブル](../in-memory-oltp/sample-database-for-in-memory-oltp.md)<br />[ネイティブ コンパイル ストアド プロシージャ](../in-memory-oltp/a-guide-to-query-processing-for-memory-optimized-tables.md)<br />[ネイティブ コンパイル ストアド プロシージャからの TempDB 内のテーブルの作成およびアクセス](../../relational-databases/in-memory-oltp/create-and-access-tables-in-tempdb-from-stored-procedures.md)<br />[メモリ最適化されたハッシュ インデックスのパフォーマンスに関する一般的な問題のトラブルシューティング](/previous-versions/sql/sql-server-2016/dn589805(v=sql.130))<br />「[実証: インメモリ OLTP によるパフォーマンスの向上](../../relational-databases/in-memory-oltp/demonstration-performance-improvement-of-in-memory-oltp.md)|
|:::image type="icon" source="../../relational-databases/performance/media/security-center-both.png":::  **[インテリジェントなクエリ処理](../../relational-databases/performance/intelligent-query-processing.md)**|[インテリジェントなクエリ処理](../../relational-databases/performance/intelligent-query-processing.md)|
  
## <a name="see-also"></a>参照  
 [パフォーマンスの監視とチューニング](../../relational-databases/performance/monitor-and-tune-for-performance.md)   
 [クエリのストアを使用した、パフォーマンスの監視](../../relational-databases/performance/monitoring-performance-by-using-the-query-store.md)   
 [単一データベースの Azure SQL Database パフォーマンス ガイダンス](/azure/azure-sql/database/performance-guidance)   
 [エラスティック プールを使用した Azure SQL Database のパフォーマンスの最適化](/azure/azure-sql/database/elastic-pool-overview)   
 [Azure SQL Database の Query Performance Insight](/azure/azure-sql/database/query-performance-insight-use)  
 [インデックスのデザイン ガイド](../../relational-databases/sql-server-index-design-guide.md)  
 [メモリ管理アーキテクチャ ガイド](../../relational-databases/memory-management-architecture-guide.md)  
 [ページとエクステントのアーキテクチャ ガイド](../../relational-databases/pages-and-extents-architecture-guide.md)  
 [移行後の検証および最適化ガイド](../../relational-databases/post-migration-validation-and-optimization-guide.md)  
 [クエリ処理アーキテクチャ ガイド](../../relational-databases/query-processing-architecture-guide.md)  
 [SQL Server トランザクションのロックおよび行のバージョン管理ガイド](../sql-server-transaction-locking-and-row-versioning-guide.md)  
 [SQL Server トランザクション ログのアーキテクチャと管理ガイド](../../relational-databases/sql-server-transaction-log-architecture-and-management-guide.md)  
 [スレッドおよびタスクのアーキテクチャ ガイド](../../relational-databases/thread-and-task-architecture-guide.md) 
