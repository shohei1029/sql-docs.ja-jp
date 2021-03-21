---
description: sys.dm_os_sys_memory (Transact-SQL)
title: sys.dm_os_sys_memory (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- dm_os_sys_memory
- sys.dm_os_sys_memory_TSQL
- sys.dm_os_sys_memory
- dm_os_sys_memory_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_os_sys_memory dynamic management view
ms.assetid: 1ca58814-1caa-44c1-b307-ff0bdcbbef62
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 285f80b8c2b0be14c02ade02c8cf9e34bb9b5c69
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104750702"
---
# <a name="sysdm_os_sys_memory-transact-sql"></a>sys.dm_os_sys_memory (Transact-SQL)
[!INCLUDE [sql-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdbmi-asa-pdw.md)]

  オペレーティングシステムからメモリ情報を返します。  
  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] は、オペレーティングシステムレベルの外部メモリ条件と、基になるハードウェアの物理的な制限によって制限され、応答します。 システム全体の状態を調査することは、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のメモリ使用量を評価するうえで重要な要素です。  
  
> [!NOTE]  
>  またはからこれを呼び出すに [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] は [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] 、 **sys.dm_pdw_nodes_os_sys_memory** という名前を使用します。  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|**total_physical_memory_kb**|**bigint**|オペレーティングシステムで使用できる物理メモリの合計サイズ (KB 単位)。|  
|**available_physical_memory_kb**|**bigint**|使用可能な物理メモリのサイズ (KB 単位)。|  
|**total_page_file_kb**|**bigint**|オペレーティング システムによって報告されたコミット制限のサイズ (KB 単位)。|  
|**available_page_file_kb**|**bigint**|使用されていないページファイルの合計サイズ (KB 単位)。|  
|**system_cache_kb**|**bigint**|システム キャッシュ メモリの合計サイズ (KB 単位)。|  
|**kernel_paged_pool_kb**|**bigint**|ページ カーネル プールの合計サイズ (KB 単位)。|  
|**kernel_nonpaged_pool_kb**|**bigint**|非ページカーネルプールの合計サイズ (KB 単位)。|  
|**system_high_memory_signal_state**|**bit**|システムの高メモリ リソース通知の状態。 この値が 1 の場合、Windows によって高メモリ シグナルが設定されていることを意味します。 詳細については、MSDN ライブラリの「 [CreateMemoryResourceNotification](/windows/win32/api/memoryapi/nf-memoryapi-creatememoryresourcenotification) 」を参照してください。|  
|**system_low_memory_signal_state**|**bit**|システムのメモリ不足のリソース通知の状態。 値が1の場合は、メモリ不足のシグナルが Windows によって設定されていることを示します。 詳細については、MSDN ライブラリの「 [CreateMemoryResourceNotification](/windows/win32/api/memoryapi/nf-memoryapi-creatememoryresourcenotification) 」を参照してください。|  
|**system_memory_state_desc**|**nvarchar (256)**|メモリ状態の説明。 次の表を参照してください。|  
|**pdw_node_id**|**int**|**適用対象**: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 、 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]<br /><br /> このディストリビューションが配置されているノードの識別子。|  
  
|条件|値|  
|---------------|-----------|  
|system_high_memory_signal_state = 1<br /><br /> および<br /><br /> system_low_memory_signal_state = 0|使用可能な物理メモリが十分に存在します。|  
|system_high_memory_signal_state = 0<br /><br /> および<br /><br /> system_low_memory_signal_state = 1|使用可能な物理メモリが不足しています。|  
|system_high_memory_signal_state = 0<br /><br /> および<br /><br /> system_low_memory_signal_state = 0|物理メモリの使用量が安定しています。|  
|system_high_memory_signal_state = 1<br /><br /> および<br /><br /> system_low_memory_signal_state = 1|物理メモリの状態が遷移中です<br /><br /> 高シグナルと低シグナルが同時にオンになることはありません。 ただし、オペレーティングシステムレベルでの迅速な変更により、両方の値がユーザーモードアプリケーションに表示される可能性があります。 両方のシグナルがオンのように見えるとき、その状態は遷移中の状態と解釈されます。|  
  
## <a name="permissions"></a>権限  
 サーバーに対する VIEW SERVER STATE 権限が必要です。  
  
## <a name="see-also"></a>参照  
 [動的管理ビューと動的管理関数 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)   
 [SQL Server オペレーティングシステム関連の動的管理ビュー &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sql-server-operating-system-related-dynamic-management-views-transact-sql.md)  
  
