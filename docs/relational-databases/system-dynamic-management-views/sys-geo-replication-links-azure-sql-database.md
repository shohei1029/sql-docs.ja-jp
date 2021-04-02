---
description: sys.geo_replication_links (Azure SQL Database)
title: sys.geo_replication_links (Azure SQL Database) |Microsoft Docs
ms.custom: ''
ms.date: 01/28/2019
ms.service: sql-database
ms.reviewer: ''
ms.topic: conceptual
f1_keywords:
- dm_geo_replication_links_TSQL
- dm_geo_replication_links
- sys.dm_geo_replication_links
- sys.dm_geo_replication_links_TSQL
helpviewer_keywords:
- sys.dm_geo_replication_links dynamic management view
- dm_geo_replication_links dynamic management view
ms.assetid: 58911798-1d60-4f28-87ab-2def2bfc3de7
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: = azuresqldb-current
ms.openlocfilehash: af2de4a8fdb5aadc2b11b2057239b27749384e9e
ms.sourcegitcommit: ebe81e2daa544f41c8ababb66a91c218ad0c2a0a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2021
ms.locfileid: "106177103"
---
# <a name="sysgeo_replication_links-azure-sql-database"></a>sys.geo_replication_links (Azure SQL Database)

[!INCLUDE[Azure SQL Database Azure SQL Managed Instance](../../includes/applies-to-version/asdb-asdbmi.md)]

  Geo レプリケーションパートナーシップのプライマリデータベースとセカンダリデータベースの間のレプリケーションリンクごとに1行の値を格納します。 このビューは、論理マスター データベースに存在します。  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|database_id|**int**|データベースビュー内の現在のデータベースの ID。|  
|start_date|**datetimeoffset**|データベースレプリケーションが開始されたときの地域 SQL Database データセンターでの UTC 時刻。|  
|modify_date|**datetimeoffset**|リージョン SQL Database データセンターでの UTC 時刻 (データベースの geo レプリケーションが完了したとき)。 この時点で、新しいデータベースがプライマリデータベースと同期されます。|  
|link_guid|**uniqueidentifier**|Geo レプリケーションリンクの一意の ID。|  
|partner_server|**sysname**|Geo レプリケートされたデータベースを含む SQL Database サーバーの名前。|  
|partner_database|**sysname**|リンクされた SQL Database サーバー上の geo レプリケートされたデータベースの名前。|  
|replication_state|**tinyint**|このデータベースの geo レプリケーションの状態。次のいずれかになります。<br /><br /> 0 = 保留中。 アクティブなセカンダリデータベースの作成がスケジュールされていますが、必要な準備手順がまだ完了していません。<br /><br /> 1 = シード処理。 Geo レプリケーションターゲットはシードされていますが、2つのデータベースがまだ同期されていません。 シード処理が完了するまで、セカンダリデータベースに接続することはできません。 プライマリからセカンダリデータベースを削除すると、シード処理操作が取り消されます。<br /><br /> 2 = キャッチアップ。 セカンダリデータベースはトランザクション一貫性のある状態であり、常にプライマリデータベースと同期されています。|  
|replication_state_desc|**nvarchar (256)**|PENDING<br /><br /> SEEDING<br /><br /> CATCH_UP|  
|role|**tinyint**|Geo レプリケーションロール。次のいずれかになります。<br /><br /> 0 = プライマリ。 Database_id は、geo レプリケーションパートナーシップのプライマリデータベースを参照します。<br /><br /> 1 = セカンダリ。  Database_id は、geo レプリケーションパートナーシップのプライマリデータベースを参照します。|  
|role_desc|**nvarchar (256)**|PRIMARY<br /><br /> SECONDARY|  
|secondary_allow_connections|**tinyint**|セカンダリ型。次のいずれかになります。<br /><br /> 0 = いいえ。 セカンダリデータベースには、フェールオーバーまでアクセスできません。<br /><br /> 1 = ReadOnly。 セカンダリデータベースは、ApplicationIntent = ReadOnly のクライアント接続のみにアクセスできます。<br /><br /> 2 = すべて。 セカンダリデータベースには、任意のクライアント接続からアクセスできます。|  
|secondary_allow_connections _desc|**nvarchar (256)**|いいえ<br /><br /> All<br /><br /> 読み取り専用|  
|percent_copied|**int**|シード処理の進行状況 (パーセント)|

## <a name="permissions"></a>アクセス許可

このビューは、サーバーレベルプリンシパルログインの **master** データベースでのみ使用できます。  
  
## <a name="example"></a>例

Geo レプリケーションリンクを持つすべてのデータベースを表示します。  

```sql
SELECT
     database_id  
   , start_date  
   , partner_server  
   , partner_database  
   , replication_state  
   , role_desc  
   , secondary_allow_connections_desc
FROM sys.geo_replication_links;  
```

## <a name="see-also"></a>参照

 [ALTER DATABASE (Azure SQL Database)](../../t-sql/statements/alter-database-transact-sql.md)   
 [sys.dm_geo_replication_link_status &#40;Azure SQL Database&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database.md)   
 [sys.dm_operation_status &#40;Azure SQL Database&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database.md)
