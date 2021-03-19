---
description: sys.dm_hadr_availability_group_states (Transact-SQL)
title: sys.dm_hadr_availability_group_states (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- sys.dm_hadr_availability_group_states
- sys.dm_hadr_availability_group_states_TSQL
- dm_hadr_availability_group_states_TSQL
- dm_hadr_availability_group_states
dev_langs:
- TSQL
helpviewer_keywords:
- Availability Groups [SQL Server], monitoring
- sys.dm_hadr_availability_group_states dynamic management view
ms.assetid: d18019dd-f8dc-4492-b035-b1a639369b65
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: b8973b883ebdfe205ff0e9017e746c8ac65ebf58
ms.sourcegitcommit: bf7577b3448b7cb0e336808f1112c44fa18c6f33
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104610907"
---
# <a name="sysdm_hadr_availability_group_states-transact-sql"></a>sys.dm_hadr_availability_group_states (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  のローカルインスタンス上の可用性レプリカを所有する Always On 可用性グループごとに1行の値を返し [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。 各行には、特定の可用性グループの正常性を定義する状態が表示されます。  
  
> [!NOTE]  
>  の完全な一覧を取得するには、 [sys.availability_groups](../../relational-databases/system-catalog-views/sys-availability-groups-transact-sql.md) カタログビューに対してクエリを実行します。  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|**group_id**|**uniqueidentifier**|可用性グループの一意識別子。|  
|**primary_replica**|**varchar(128)**|現在のプライマリ レプリカをホストしているサーバー インスタンスの名前。<br /><br /> NULL = プライマリレプリカではなく、WSFC フェールオーバークラスターと通信できません。|  
|**primary_recovery_health**|**tinyint**|プライマリ レプリカの復旧の正常性状態を示します。次のいずれかになります。<br /><br /> 0 = 実行中<br /><br /> 1 = オンライン<br /><br /> NULL<br /><br /> セカンダリレプリカでは、 **primary_recovery_health** 列が NULL になります。|  
|**primary_recovery_health_desc**|**nvarchar(60)**|**Primary_replica_health** の説明。次のいずれかになります。<br /><br /> ONLINE_IN_PROGRESS<br /><br /> ONLINE<br /><br /> NULL|  
|**secondary_recovery_health**|**tinyint**|セカンダリレプリカレプリカの回復の正常性を示します。次のいずれかになります。<br /><br /> 0 = 実行中<br /><br /> 1 = オンライン<br /><br /> NULL<br /><br /> プライマリレプリカで、 **secondary_recovery_health** 列が NULL になっています。|  
|**secondary_recovery_health_desc**|**nvarchar(60)**|**Secondary_recovery_health** の説明。次のいずれかになります。<br /><br /> ONLINE_IN_PROGRESS<br /><br /> ONLINE<br /><br /> NULL|  
|**synchronization_health**|**tinyint**|可用性グループ内のすべての可用性レプリカの **synchronization_health** のロールアップを反映します。 使用可能な値とその説明を次に示します。<br /><br /> 0: 異常です。 正常な **synchronization_health** (2 = 正常) の可用性レプリカはありません。<br /><br /> 1: 部分的に正常です。 一部の可用性レプリカの同期状態は正常です。<br /><br /> 2: 正常。 すべての可用性レプリカの同期状態は正常です。<br /><br /> レプリカの同期の正常性については、 [sys.dm_hadr_availability_replica_states &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-hadr-availability-replica-states-transact-sql.md)の **synchronization_health** 列を参照してください。|  
|**synchronization_health_desc**|**nvarchar(60)**|**Synchronization_health** の説明。次のいずれかになります。<br /><br /> NOT_HEALTHY<br /><br /> PARTIALLY_HEALTHY<br /><br /> 戻ら|  
  
## <a name="security"></a>セキュリティ  
  
### <a name="permissions"></a>アクセス許可  
 サーバーに対する VIEW SERVER STATE 権限が必要です。  
  
## <a name="see-also"></a>参照  
 [Transact-sql&#41;&#40;可用性グループの監視 ](../../database-engine/availability-groups/windows/monitor-availability-groups-transact-sql.md)   
 [AlwaysOn 可用性グループ &#40;SQL Server&#41;](../../database-engine/availability-groups/windows/always-on-availability-groups-sql-server.md)   
 [AlwaysOn 可用性グループの動的管理ビューおよび関数 &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/always-on-availability-groups-dynamic-management-views-functions.md)  
  
  
