---
description: sys.dm_os_threads (Transact-sql)
title: sys.dm_os_threads (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/13/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- dm_os_threads_TSQL
- sys.dm_os_threads
- dm_os_threads
- sys.dm_os_threads_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_os_threads dynamic management view
ms.assetid: a5052701-edbf-4209-a7cb-afc9e65c41c1
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 4ec18cabc3db2dfdb3be4ec6b4e6148226dbcd69
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104750682"
---
# <a name="sysdm_os_threads-transact-sql"></a>sys.dm_os_threads (Transact-sql)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] プロセスで実行されている [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] オペレーティング システム スレッドの一覧を返します。  
  
> [!NOTE]  
>  またはからこれを呼び出すに [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] は [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] 、 **sys.dm_pdw_nodes_os_threads** という名前を使用します。  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|thread_address|**varbinary (8)**|スレッドのメモリアドレス (主キー)。|  
|started_by_sqlservr|**bit**|スレッドの開始側を示します。<br /><br /> 1 = [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] によってスレッドが開始されました。<br /><br /> 0 = 別のコンポーネントがスレッドを開始しました。たとえば、内から拡張ストアドプロシージャを起動し [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。|  
|os_thread_id|**int**|オペレーティングシステムによって割り当てられたスレッドの ID。|  
|status|**int**|内部状態フラグ。|  
|instruction_address|**varbinary (8)**|現在実行されている命令のアドレス。|  
|creation_time|**datetime**|このスレッドが作成された時刻。|  
|kernel_time|**bigint**|このスレッドで使用されるカーネル時間の量。|  
|usermode_time|**bigint**|スレッドで使用されたユーザー時間。|  
|stack_base_address|**varbinary (8)**|このスレッドの最大スタックアドレスのメモリアドレス。|  
|stack_end_address|**varbinary (8)**|スレッドにおける最下位のスタック アドレスのメモリ アドレス。|  
|stack_bytes_committed|**int**|スタックでコミットされたバイト数。|  
|stack_bytes_used|**int**|スレッドでアクティブに使用されているバイト数。|  
|affinity|**bigint**|このスレッドが実行されている CPU マスク。 これは、 **ALTER SERVER CONFIGURATION SET PROCESS AFFINITY** ステートメントで構成された値によって異なります。 ソフト アフィニティの場合は、スケジューラと異なることがあります。|  
|優先度|**int**|このスレッドの優先度の値。|  
|Locale|**int**|スレッドのキャッシュされたロケール LCID。|  
|トークン|**varbinary (8)**|スレッドのキャッシュされた偽装トークンハンドル。|  
|is_impersonating|**int**|スレッドで Win32 権限借用が使用されているかどうかを示します。<br /><br /> 1 = スレッドではプロセスの既定値と異なるセキュリティ資格情報が使用されています。 これは、スレッドが、プロセスを作成したエンティティ以外のエンティティを偽装していることを示します。|  
|is_waiting_on_loader_lock|**int**|スレッドでローダー ロックを待機中かどうかを示す、オペレーティング システムの状態。|  
|fiber_data|**varbinary (8)**|スレッドで実行されている現在の Win32 ファイバー。 これは [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 、が簡易プーリング用に構成されている場合にのみ適用されます。|  
|thread_handle|**varbinary (8)**|内部使用のみです。|  
|event_handle|**varbinary (8)**|内部使用のみです。|  
|scheduler_address|**varbinary (8)**|このスレッドに関連付けられているスケジューラのメモリアドレス。 詳細については、「 [sys.dm_os_schedulers &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-os-schedulers-transact-sql.md)」を参照してください。|  
|worker_address|**varbinary (8)**|スレッドにバインドしているワーカーのメモリ アドレス。 詳細については、「 [sys.dm_os_workers &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-os-workers-transact-sql.md)」を参照してください。|  
|fiber_context_address|**varbinary (8)**|内部ファイバー コンテキスト アドレス。 これは [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 、が簡易プーリング用に構成されている場合にのみ適用されます。|  
|self_address|**varbinary (8)**|内部一貫性ポインター。|  
|processor_group|**smallint**|**適用対象**: [!INCLUDE[ssKilimanjaro](../../includes/sskilimanjaro-md.md)] 以降。<br /><br /> プロセッサ グループ ID。|  
|pdw_node_id|**int**|**適用対象**: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 、 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]<br /><br /> このディストリビューションが配置されているノードの識別子。|  
  
## <a name="permissions"></a>権限

で [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] は、 `VIEW SERVER STATE` 権限が必要です。   
SQL Database Basic、S0、S1 のサービス目標、およびエラスティックプール内のデータベースについては、 [サーバー管理者](/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database) アカウントまたは [Azure Active Directory 管理者](/azure/azure-sql/database/authentication-aad-overview#administrator-structure) アカウントが必要です。 その他のすべての SQL Database サービスの目的で `VIEW DATABASE STATE` は、データベースで権限が必要になります。   

## <a name="notes-on-linux-version"></a>Linux のバージョンに関する注意事項

Linux での SQL エンジンの動作によって、この情報の一部が Linux 診断データと一致しません。 たとえば、は、、 `os_thread_id` `ps` `top` procfs (/proc/) などのツールの結果と一致しません `pid` 。  これは、プラットフォームアブストラクションレイヤー (SQLPAL)、SQL Server コンポーネントとオペレーティングシステムの間のレイヤーです。

## <a name="examples"></a>例  
 スタートアップ時に、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] スレッドを開始し、ワーカーをそれらのスレッドに関連付けます。 ただし、拡張ストアドプロシージャなどの外部コンポーネントは、プロセスでスレッドを開始でき [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、これらのスレッドを制御できません。 sys.dm_os_threads は、プロセス内のリソースを消費する悪意のあるスレッドに関する情報を提供でき [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。  
  
 次のクエリは、によって開始されていないスレッドを実行しているワーカーと、実行に使用された時間を検索するために使用され [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。  
  
> [!NOTE]
>  次のクエリでは、簡略化のため `*` ステートメントでアスタリスク (`SELECT`) を使用していますが、 特にカタログ ビュー、動的管理ビュー、およびシステム テーブル値関数では、アスタリスク (*) を使用しないようにしてください。 の今後のアップグレードおよびリリースで [!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] は、列を追加し、これらのビューおよび関数に列の順序を変更することができます。 これらの変更によって、特定の順序と列数を想定しているアプリケーションが壊れる可能性があります。  
  
```  
SELECT *  
  FROM sys.dm_os_threads  
  WHERE started_by_sqlservr = 0;  
```  
  
## <a name="see-also"></a>参照  
  [sys.dm_os_workers &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-os-workers-transact-sql.md)   
 [SQL Server オペレーティングシステム関連の動的管理ビュー &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sql-server-operating-system-related-dynamic-management-views-transact-sql.md)  
  
