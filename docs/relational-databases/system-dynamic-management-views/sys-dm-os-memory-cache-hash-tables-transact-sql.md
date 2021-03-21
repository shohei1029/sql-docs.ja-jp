---
description: sys.dm_os_memory_cache_hash_tables (Transact-SQL)
title: sys.dm_os_memory_cache_hash_tables (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/13/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- sys.dm_os_memory_cache_hash_tables_TSQL
- sys.dm_os_memory_cache_hash_tables
- dm_os_memory_cache_hash_tables
- dm_os_memory_cache_hash_tables_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_os_memory_cache_hash_tables dynamic management view
ms.assetid: 68b94f35-8f80-4d2b-bcde-7a21934219af
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: db1c09094232aa7eaf9df20582c63952e44e0ad2
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104735972"
---
# <a name="sysdm_os_memory_cache_hash_tables-transact-sql"></a>sys.dm_os_memory_cache_hash_tables (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  のインスタンス内のアクティブなキャッシュごとに1行の値を返し [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。  
  
> [!NOTE]  
>  またはからこれを呼び出すに [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] は [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] 、 **sys.dm_pdw_nodes_os_memory_cache_hash_tables** という名前を使用します。  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|**cache_address**|**varbinary (8)**|キャッシュエントリのアドレス (主キー)。 NULL 値は許可されません。|  
|**name**|**nvarchar (256)**|キャッシュの名前。 NULL 値は許可されません。|  
|**type**|**nvarchar(60)**|キャッシュの種類。 NULL 値は許可されません。|  
|**table_level**|**int**|ハッシュ テーブル番号。 特定のキャッシュには、異なるハッシュ関数に対応する複数のハッシュテーブルが含まれている場合があります。 NULL 値は許可されません。|  
|**buckets_count**|**int**|ハッシュテーブル内のバケットの数。 NULL 値は許可されません。|  
|**buckets_in_use_count**|**int**|現在使用されているバケット数。 NULL 値は許可されません。|  
|**buckets_min_length**|**int**|バケットの最小キャッシュ エントリ数。 NULL 値は許可されません。|  
|**buckets_max_length**|**int**|バケット内のキャッシュエントリの最大数。 NULL 値は許可されません。|  
|**buckets_avg_length**|**int**|各バケットの平均キャッシュ エントリ数。 NULL 値は許可されません。|  
|**buckets_max_length_ever**|**int**|サーバーの起動後に、このハッシュテーブルのハッシュバケットにキャッシュされたエントリの最大数。 NULL 値は許可されません。|  
|**hits_count**|**bigint**|キャッシュヒット数。 NULL 値は許可されません。|  
|**misses_count**|**bigint**|キャッシュミスの数。 NULL 値は許可されません。|  
|**buckets_avg_scan_hit_length**|**int**|検索したアイテムが見つかるまでにバケットで検証したエントリの平均数。 NULL 値は許可されません。|  
|**buckets_avg_scan_miss_length**|**int**|検索が正常に終了するまでのバケット内の検査されたエントリの平均数。 NULL 値は許可されません。|  
|**pdw_node_id**|**int**|このディストリビューションが配置されているノードの識別子。<br /><br /> **適用対象**: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 、 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]|  
  
## <a name="permissions"></a>権限 

で [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] は、 `VIEW SERVER STATE` 権限が必要です。   
SQL Database Basic、S0、S1 のサービス目標、およびエラスティックプール内のデータベースについては、 [サーバー管理者](/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database) アカウントまたは [Azure Active Directory 管理者](/azure/azure-sql/database/authentication-aad-overview#administrator-structure) アカウントが必要です。 その他のすべての SQL Database サービスの目的で `VIEW DATABASE STATE` は、データベースで権限が必要になります。   

## <a name="see-also"></a>参照  
 
  [SQL Server オペレーティングシステム関連の動的管理ビュー &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sql-server-operating-system-related-dynamic-management-views-transact-sql.md)  
  
