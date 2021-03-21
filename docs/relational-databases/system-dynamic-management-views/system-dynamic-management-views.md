---
description: 動的管理ビュー (Transact-sql)
title: 動的管理ビュー (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/29/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
dev_langs:
- TSQL
helpviewer_keywords:
- database scoped dynamic management objects [SQL Server]
- dynamic management objects [SQL Server], about dynamic management objects
- server state information [SQL Server]
- dynamic management functions [SQL Server]
- metadata [SQL Server], dynamic management objects
- dynamic management views [SQL Server]
- DMVs [SQL Server]
- functions [SQL Server], dynamic management
- server scoped dynamic management objects [SQL Server]
- dynamic management objects [SQL Server]
ms.assetid: cf893ecb-0bf6-4cbf-ac00-8a1099e405b1
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: f1a80d9dd8d791fbcb2f955b0da0a9a371bf9055
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104755852"
---
# <a name="system-dynamic-management-views"></a>システム動的管理ビュー
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  動的管理ビューと動的管理関数では、サーバーの状態情報が返されます。返された情報は、サーバー インスタンスのヘルス状態の監視、問題の診断、パフォーマンスのチューニングに使用できます。  
  
> [!IMPORTANT]  
>  動的管理ビューおよび関数は、実装固有の内部状態データを返します。 これらのスキーマおよび返されるデータは、の将来のリリースで変更される可能性があり [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。 そのため、今後のリリースの動的管理ビューおよび関数は、このリリースの動的管理ビューおよび関数と互換性がない可能性があります。 たとえば、の将来のリリースでは、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 列リストの末尾に列を追加することにより、動的管理ビューの定義が拡張される可能性があります。 `SELECT * FROM dynamic_management_view_name`返される列の数が変更され、アプリケーションが中断される可能性があるため、実稼働コードでは構文を使用しないことをお勧めします。  
  
 動的管理ビューと関数には、次の2種類があります。  
  
-   サーバー スコープの動的管理ビューと動的管理関数。 サーバーに対する VIEW SERVER STATE 権限が必要です。  
  
-   データベース スコープの動的管理ビューと動的管理関数。 そのためには、データベースに対する VIEW DATABASE STATE 権限が必要です。  
  
## <a name="querying-dynamic-management-views"></a>動的管理ビューのクエリ  
 動的管理ビューは、ステートメントで2部構成、 [!INCLUDE[tsql](../../includes/tsql-md.md)] 3 部構成、または4部構成の名前を使用して参照できます。 一方、動的管理関数は、 [!INCLUDE[tsql](../../includes/tsql-md.md)] ステートメントで2部構成または3部構成の名前を使用して参照できます。 動的管理ビューおよび関数は、ステートメント内で [!INCLUDE[tsql](../../includes/tsql-md.md)] 1 つの部分で構成される名前を使用して参照することはできません。  
  
 すべての動的管理ビューと動的管理関数は sys スキーマに含まれ、dm_* という名前付け規則に従います。 動的管理ビューまたは動的管理関数を使用するときには、sys スキーマを使用して、ビューまたは関数の名前にプレフィックスを付ける必要があります。 たとえば、動的管理ビュー dm_os_wait_stats をクエリするには、次のクエリを実行します。  
  
 ```sql
SELECT wait_type, wait_time_ms  
FROM sys.dm_os_wait_stats;  
```  
  
### <a name="required-permissions"></a>必要なアクセス許可  
 動的管理ビューまたは動的管理関数をクエリするには、オブジェクトに対する SELECT 権限と、VIEW SERVER STATE 権限または VIEW DATABASE STATE 権限が必要です。 これらの権限を使用することにより、動的管理ビューと動的管理関数に対するユーザーまたはログインのアクセスを選択的に制限できます。 これを行うには、まず master にユーザーを作成し、次にアクセスを禁止する動的管理ビューまたは動的管理関数に対するユーザーの SELECT 権限を拒否します。 その後、ユーザーのデータベースコンテキストに関係なく、これらの動的管理ビューまたは関数から選択することはできません。  
  
> [!NOTE]  
>  DENY が優先されるため、ユーザーが VIEW SERVER STATE 権限を許可されていても VIEW DATABASE STATE 権限が拒否された場合、ユーザーはサーバーレベルの情報を表示できますが、データベースレベルの情報は参照できません。  
  
## <a name="in-this-section"></a>このセクションの内容  
 動的管理ビューおよび動的管理関数は、次のカテゴリに分類されています。  

:::row:::
    :::column:::
        [Always On 可用性グループの動的管理ビューおよび関数 (Transact-sql)](../../relational-databases/system-dynamic-management-views/always-on-availability-groups-dynamic-management-views-functions.md)

        [変更データ キャプチャに関連した動的管理ビュー &#40;Transact-SQL&#41;](change-data-capture-sys-dm-cdc-errors.md)

        [関連する動的管理ビューの Change Tracking](change-tracking-sys-dm-tran-commit-table.md)

        [Transact-sql&#41;&#40;共通言語ランタイム関連の動的管理ビュー ](../../relational-databases/system-dynamic-management-views/common-language-runtime-related-dynamic-management-views-transact-sql.md)

        [データベースミラーリング関連の動的管理ビュー &#40;Transact-sql&#41;](database-mirroring-sys-dm-db-mirroring-auto-page-repair.md)

        [Transact-sql&#41;&#40;データベース関連の動的管理ビュー ](../../relational-databases/system-dynamic-management-views/database-related-dynamic-management-views-transact-sql.md)

        [実行関連の動的管理ビューおよび関数 &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/execution-related-dynamic-management-views-and-functions-transact-sql.md)

        [拡張イベントの動的管理ビュー](../../relational-databases/system-dynamic-management-views/extended-events-dynamic-management-views.md)

        [Transact-sql&#41;&#40;の Filestream および FileTable の動的管理ビュー ](../../relational-databases/system-dynamic-management-views/filestream-and-filetable-dynamic-management-views-transact-sql.md)

        [Transact-sql&#41;&#40;のフルテキスト検索とセマンティック検索の動的管理ビューおよび関数 ](../../relational-databases/system-dynamic-management-views/full-text-and-semantic-search-dynamic-management-views-functions.md)

        [Geo レプリケーションの動的管理ビューおよび関数 &#40;Azure SQL Database&#41;](../../relational-databases/system-dynamic-management-views/geo-replication-dynamic-management-views-and-functions-azure-sql-database.md)

        [インデックス関連の動的管理ビューおよび関数 &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/index-related-dynamic-management-views-and-functions-transact-sql.md)

        [I O 関連の動的管理ビューおよび関数 &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/i-o-related-dynamic-management-views-and-functions-transact-sql.md)
    :::column-end:::
    :::column:::
        [メモリ最適化テーブルの動的管理ビュー &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/memory-optimized-table-dynamic-management-views-transact-sql.md)

        [オブジェクト関連の動的管理ビューおよび関数 &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/object-related-dynamic-management-views-and-functions-transact-sql.md)

        [クエリ通知関連の動的管理ビュー &#40;Transact-sql&#41;](query-notifications-sys-dm-qn-subscriptions.md)

        [レプリケーション関連の動的管理ビュー &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/replication-related-dynamic-management-views-transact-sql.md)

        [リソース ガバナー関連の動的管理ビュー &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/resource-governor-related-dynamic-management-views-transact-sql.md)

        [セキュリティ関連の動的管理ビューおよび関数 &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/security-related-dynamic-management-views-and-functions-transact-sql.md)

        [サーバー関連の動的管理ビューおよび関数 &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/server-related-dynamic-management-views-and-functions-transact-sql.md)

        [Service Broker 関連の動的管理ビュー &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/service-broker-related-dynamic-management-views-transact-sql.md)

        [空間データ関連の動的管理ビューおよび関数 &#40;Transact-sql&#41;](./spatial-data-sys-dm-db-objects-disabled-on-compatibility-level-change.md)

        [Azure Synapse Analytics と並列データウェアハウスの動的管理ビュー &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sql-and-parallel-data-warehouse-dynamic-management-views.md)

        [SQL Server オペレーティングシステム関連の動的管理ビュー &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sql-server-operating-system-related-dynamic-management-views-transact-sql.md)

        [Transact-sql&#41;&#40;の動的管理ビューの Stretch Database ]()

        [トランザクション関連の動的管理ビューおよび関数 &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/transaction-related-dynamic-management-views-and-functions-transact-sql.md)
    :::column-end:::
:::row-end:::

## <a name="see-also"></a>参照  
 [Transact-sql&#41;&#40;サーバー権限を付与する ](../../t-sql/statements/grant-server-permissions-transact-sql.md)   
 [GRANT (データベースの権限の許可) &#40;Transact-SQL&#41;](../../t-sql/statements/grant-database-permissions-transact-sql.md)   
 [システムビュー &#40;Transact-sql&#41;](../../t-sql/language-reference.md)  
  
