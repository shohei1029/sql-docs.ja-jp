---
description: システムカタログビュー (Transact-sql)
title: カタログビュー (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 05/02/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- sql13.SysViewExpandPortal.f1
dev_langs:
- TSQL
helpviewer_keywords:
- catalog metadata [SQL Server]
- system views [SQL Server], catalog
- metadata [SQL Server], views
- catalogs [SQL Server], metadata
- catalog views [SQL Server]
- Database Engine [SQL Server], metadata
- catalog views [SQL Server], about catalog views
ms.assetid: 13bccc2f-ed3c-4b58-abd0-ca8bf34a66b8
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 11a9a831d5bb4671b4fc7dcd1449b2209567b558
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104755662"
---
# <a name="system-catalog-views-transact-sql"></a>システムカタログビュー (Transact-sql)

[!INCLUDE[appliesto-ss-asdb-asdw-pdw-md](../../includes/appliesto-ss-asdb-asdw-pdw-md.md)]

カタログ ビューは、[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]によって使用される情報を返します。 カタログビューはカタログメタデータに最も一般的なインターフェイスであり、この情報を取得、変換、およびカスタマイズされた形式で表示するための最も効率的な方法を提供するため、カタログビューを使用することをお勧めします。 すべてのユーザーが利用できるカタログメタデータは、カタログビューによって公開されます。

> [!NOTE]
> カタログ ビューには、レプリケーション、バックアップ、データベース メンテナンス プラン、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エージェントなどのカタログ データに関する情報は含まれていません。

 カタログ ビューの中には、他のカタログ ビューの行を継承するものもあります。 たとえば、、 [テーブル](../../relational-databases/system-catalog-views/sys-tables-transact-sql.md) カタログビューは [、の各カタログビュー](../../relational-databases/system-catalog-views/sys-objects-transact-sql.md) を継承します。 sys.objects カタログ ビューはベース ビューと呼ばれ、sys.tables ビューは派生ビューと呼ばれます。 sys.tables カタログ ビューではテーブルに固有の列のほか、sys.objects カタログ ビューで返されるすべての列が返されます。 sys.objects カタログ ビューでは、テーブル以外の、ストアド プロシージャやビューなどのオブジェクトの行が返されます。 テーブルが作成されると、両方のビューでテーブルのメタデータが返されます。 これら 2 つのカタログ ビューではテーブルに関する異なるレベルの情報が返されますが、このテーブルのメタデータ内のエントリは 1 つだけで、名前と object_id が、それぞれ 1 つだけ含まれています。 まとめると次のようになります。

- ベース ビューには列のサブセットと行のスーパーセットが含まれます。
- 派生ビューには列のスーパーセットと行のサブセットが含まれます。

> [!IMPORTANT]
> 今後の [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)][!INCLUDE[msCoName](../../includes/msconame-md.md)] のリリースでは、列のリストの末尾に列を追加することにより、システム カタログ ビューの定義が拡張される可能性があります。 \*返される列の数が変更され、アプリケーションが中断される可能性があるため、実稼働コードで SELECT FROM *sys.catalog_view_name* を使用することをお勧めします。

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のカタログ ビューは、次のカテゴリに分類されます。

:::row:::
    :::column:::
        [AlwaysOn 可用性グループのカタログ ビュー &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/always-on-availability-groups-catalog-views-transact-sql.md)
        
        [カタログビューの Azure SQL Database](../../relational-databases/system-catalog-views/azure-sql-database-catalog-views.md)
        
        [Change Tracking カタログビュー &#40;Transact-sql&#41;](../system-catalog-views/change-tracking-catalog-views-sys-change-tracking-databases.md)
        
        [CLR アセンブリカタログビュー &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/clr-assembly-catalog-views-transact-sql.md)
        
        [データ コレクターのビュー &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/data-collector-views-transact-sql.md)
        
        [データ領域 &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/data-spaces-transact-sql.md)
        
        [データベースメールビュー &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/database-mail-views-transact-sql.md)
        
        [データベースミラーリング監視サーバーのカタログビュー &#40;Transact-sql&#41;](../system-catalog-views/database-mirroring-witness-catalog-views-sys-database-mirroring-witnesses.md)
        
        [データベースとファイルのカタログ ビュー &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/databases-and-files-catalog-views-transact-sql.md)
        
        [エンドポイントのカタログ ビュー &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/endpoints-catalog-views-transact-sql.md)
        
        [拡張イベント カタログ ビュー &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/extended-events-catalog-views-transact-sql.md)
        
        [拡張プロパティ カタログ ビュー &#40;Transact-SQL&#41;](../system-catalog-views/extended-properties-catalog-views-sys-extended-properties.md)
        
        [外部の操作カタログビュー &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/external-operations-catalog-views-transact-sql.md)
        
        [Filestream および FileTable のカタログビュー &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/filestream-and-filetable-catalog-views-transact-sql.md)
        
        [Transact-sql&#41;&#40;のフルテキスト検索およびセマンティック検索カタログビュー ](../../relational-databases/system-catalog-views/full-text-search-and-semantic-search-catalog-views-transact-sql.md)
        
        [リンクサーバーのカタログビュー &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/linked-servers-catalog-views-transact-sql.md)
    :::column-end:::
    :::column:::
        [エラーのメッセージ &#40;&#41; カタログビュー &#40;Transact-sql&#41;](../system-catalog-views/messages-for-errors-catalog-views-sys-messages.md)
        
        [オブジェクト カタログ ビュー &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/object-catalog-views-transact-sql.md)
        
        [パーティション関数のカタログビュー &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/partition-function-catalog-views-transact-sql.md)
        
        [ポリシーベースの管理ビュー &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/policy-based-management-views-transact-sql.md)
        
        [Resource Governor カタログビュー &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/resource-governor-catalog-views-transact-sql.md)
        
        [クエリ ストアのカタログ ビュー &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/query-store-catalog-views-transact-sql.md)
        
        [Transact-sql&#41;&#40;スカラー型のカタログビュー ](../../relational-databases/system-catalog-views/scalar-types-catalog-views-transact-sql.md)
        
        [スキーマカタログビュー &#40;Transact-sql&#41;](../system-catalog-views/schemas-catalog-views-sys-schemas.md)
        
        [セキュリティ カタログ ビュー &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/security-catalog-views-transact-sql.md)
        
        [Service Broker カタログ ビュー &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/service-broker-catalog-views-transact-sql.md)
        
        [サーバー全体の構成のカタログビュー &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/server-wide-configuration-catalog-views-transact-sql.md)
        
        [空間データのカタログ ビュー](../../relational-databases/system-catalog-views/spatial-data-catalog-views.md)
        
        [Azure Synapse Analytics と Parallel Data Warehouse のカタログ ビュー](../../relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views.md)
        
        [Stretch Database カタログビュー &#40;Transact-sql&#41;](../system-catalog-views/stretch-database-catalog-views-sys-remote-data-archive-databases.md)
        
        [Xml スキーマ &#40;XML 型システム&#41; カタログビュー &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/xml-schemas-xml-type-system-catalog-views-transact-sql.md)
    :::column-end:::
:::row-end:::

## <a name="see-also"></a>参照

- [情報スキーマビュー &#40;Transact-sql&#41;](../../relational-databases/system-information-schema-views/system-information-schema-views-transact-sql.md)
- [システム テーブル &#40;Transact-SQL&#41;](../../relational-databases/system-tables/system-tables-transact-sql.md)
- [SQL Server システム カタログに対するクエリに関してよく寄せられる質問](../../relational-databases/system-catalog-views/querying-the-sql-server-system-catalog-faq.yml)
