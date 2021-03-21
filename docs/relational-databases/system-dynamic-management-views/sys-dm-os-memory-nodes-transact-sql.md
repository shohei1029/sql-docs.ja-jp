---
description: sys.dm_os_memory_nodes (Transact-SQL)
title: sys.dm_os_memory_nodes (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/13/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- dm_os_memory_nodes_TSQL
- sys.dm_os_memory_nodes
- sys.dm_os_memory_nodes_TSQL
- dm_os_memory_nodes
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_os_memory_nodes dynamic management view
ms.assetid: bf4032fe-7db1-40e9-a62e-d69cebff4b44
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 43e8fc06fdb7fb0c7fb8fd1de75fd58ecb4741ca
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104750972"
---
# <a name="sysdm_os_memory_nodes-transact-sql"></a>sys.dm_os_memory_nodes (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  メモリマネージャーを使用する内部の割り当て [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。 **Sys.dm_os_process_memory** と内部カウンターのプロセスメモリカウンターの差を追跡すると、メモリ空間内の外部コンポーネントからのメモリ使用を示すことができ [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。  
  
 ノードは、物理 NUMA メモリ ノードごとに作成されます。 これらは、 **sys.dm_os_nodes** の CPU ノードとは異なる場合があります。  
  
 Windows メモリ割り当てルーチンを介して直接実行された割り当ては追跡されません。 次の表は、memory manager インターフェイスを使用した場合にのみ実行されるメモリ割り当てに関する情報を示して [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] います。  
  
> [!NOTE]  
>  またはからこれを呼び出すに [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] は [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] 、 **sys.dm_pdw_nodes_os_memory_nodes** という名前を使用します。  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|**memory_node_id**|**smallint**|メモリノードの ID を指定します。 **Sys.dm_os_memory_clerks** の **memory_node_id** に関連します。 NULL 値は許可されません。|  
|**virtual_address_space_reserved_kb**|**bigint**|仮想アドレス予約の数 (kb 単位) を示します。これは、コミットされていないか、物理ページにマップされていません。 NULL 値は許可されません。|  
|**virtual_address_space_committed_kb**|**bigint**|コミットまたは物理ページにマップされた仮想アドレスの量を KB 単位で指定します。 NULL 値は許可されません。|  
|**locked_page_allocations_kb**|**bigint**|によってロックされている物理メモリの量を KB 単位で指定し [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。 NULL 値は許可されません。|  
|**single_pages_kb**|**bigint**|**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] から [!INCLUDE[ssKilimanjaro](../../includes/sskilimanjaro-md.md)]<br /><br /> このノードで実行されているスレッドによって1ページアロケーターを使用して割り当てられたコミット済みメモリの量 (KB 単位)。 このメモリは、バッファープールから割り当てられます。 この値は、割り当て要求が実行された場所を示します。割り当て要求が満たされた物理的な場所ではありません。|  
|**pages_kb**|**bigint**|**適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降。<br /><br /> メモリ マネージャー ページ アロケーターによってこの NUMA ノードから割り当てられる、コミット済みのメモリ量 (KB 単位) を指定します。 NULL 値は許可されません。|  
|**multi_pages_kb**|**bigint**|**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] から [!INCLUDE[ssKilimanjaro](../../includes/sskilimanjaro-md.md)]<br /><br /> このノード上で実行されているスレッドが複数ページ アロケーターを使って割り当てたコミット済みのメモリ量 (KB 単位)。 このメモリは、バッファー プール外から割り当てられます。 この値は、割り当て要求が実行された場所を示します。割り当て要求が満たされた物理的な場所ではありません。|  
|**shared_memory_reserved_kb**|**bigint**|このノードから予約されている共有メモリの量を KB 単位で指定します。 NULL 値は許可されません。|  
|**shared_memory_committed_kb**|**bigint**|このノードでコミットされた共有メモリの量 (KB 単位) を指定します。 NULL 値は許可されません。|  
|**cpu_affinity_mask**|**bigint**|**適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降。<br /><br /> 内部使用のみです。 NULL 値は許可されません。|  
|**online_scheduler_mask**|**bigint**|**適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降。<br /><br /> 内部使用のみです。 NULL 値は許可されません。|  
|**processor_group**|**smallint**|**適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降。<br /><br /> 内部使用のみです。 NULL 値は許可されません。|  
|**foreign_committed_kb**|**bigint**|**適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降。<br /><br /> 他のメモリノードからコミットされたメモリの量を KB 単位で指定します。 NULL 値は許可されません。|  
|**target_kb** |**bigint** |**適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降、[!INCLUDE[ssSDS_md](../../includes/sssds-md.md)]。<br /><br /> メモリノードのメモリ目標を KB 単位で指定します。 |   
|**pdw_node_id**|**int**|**適用対象**: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 、 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]<br /><br /> このディストリビューションが配置されているノードの識別子。|  
  
## <a name="permissions"></a>権限

で [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] は、 `VIEW SERVER STATE` 権限が必要です。   
SQL Database Basic、S0、S1 のサービス目標、およびエラスティックプール内のデータベースについては、 [サーバー管理者](/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database) アカウントまたは [Azure Active Directory 管理者](/azure/azure-sql/database/authentication-aad-overview#administrator-structure) アカウントが必要です。 その他のすべての SQL Database サービスの目的で `VIEW DATABASE STATE` は、データベースで権限が必要になります。   

## <a name="see-also"></a>参照  
  [SQL Server オペレーティングシステム関連の動的管理ビュー &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sql-server-operating-system-related-dynamic-management-views-transact-sql.md)  
  
