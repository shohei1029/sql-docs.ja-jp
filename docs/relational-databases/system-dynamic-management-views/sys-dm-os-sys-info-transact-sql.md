---
description: sys.dm_os_sys_info (Transact-SQL)
title: sys.dm_os_sys_info (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 04/24/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- sys.dm_os_sys_info_TSQL
- dm_os_sys_info
- dm_os_sys_info_TSQL
- sys.dm_os_sys_info
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_os_sys_info dynamic management view
- time [SQL Server], instance started
- starting time
ms.assetid: 20f6bc9c-839a-4fa4-b3f3-a6c47d1b69af
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 024eafe8832b520373fb9e25fdfd2eb59e160fce
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104754562"
---
# <a name="sysdm_os_sys_info-transact-sql"></a>sys.dm_os_sys_info (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  コンピューターに関する有用な情報のその他のセット、およびによって使用および使用されるリソースについての情報を返し [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] ます。  
  
> **注:** またはからこれを呼び出すに [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] は [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] 、 **sys.dm_pdw_nodes_os_sys_info** という名前を使用します。  
  
|列名|データ型|説明とバージョン固有の注意事項 |  
|-----------------|---------------|-----------------|  
|**cpu_ticks**|**bigint**|現在の CPU ティック数を指定します。 CPU ティックは、プロセッサの RDTSC カウンターから取得されます。 これは、単調に増加する数値です。 NULL 値は許可されません。|  
|**ms_ticks**|**bigint**|コンピューターが起動してからのミリ秒数を指定します。 NULL 値は許可されません。|  
|**cpu_count**|**int**|システム上の論理 CPU の数を指定します。 NULL 値は許可されません。|  
|**hyperthread_ratio**|**int**|1つの物理プロセッサパッケージによって公開される論理コアまたは物理コアの数の比率を指定します。 NULL 値は許可されません。|  
|**physical_memory_in_bytes**|**bigint**|**適用対象:** [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] から [!INCLUDE[ssKilimanjaro](../../includes/sskilimanjaro-md.md)] 。<br /><br /> コンピューターの物理メモリの合計サイズを指定します。 NULL 値は許可されません。|  
|**physical_memory_kb**|**bigint**|**適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降。<br /><br /> コンピューターの物理メモリの合計サイズを指定します。 NULL 値は許可されません。|  
|**virtual_memory_in_bytes**|**bigint**|**適用対象:** [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] から [!INCLUDE[ssKilimanjaro](../../includes/sskilimanjaro-md.md)] 。<br /><br /> ユーザーモードでプロセスで使用できる仮想メモリの量。 これは、3 GB スイッチを使用して SQL Server が開始されたかどうかを判断するために使用できます。|  
|**virtual_memory_kb**|**bigint**|**適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降。<br /><br /> ユーザーモードでのプロセスで使用可能な仮想アドレス空間の合計サイズを指定します。 NULL 値は許可されません。|  
|**bpool_committed**|**int**|**適用対象:** [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] から [!INCLUDE[ssKilimanjaro](../../includes/sskilimanjaro-md.md)] 。<br /><br /> メモリマネージャーでコミットされたメモリをキロバイト (KB) 単位で表します。 メモリ マネージャー内の予約済みメモリは含まれません。 NULL 値は許可されません。|  
|**committed_kb**|**int**|**適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降。<br /><br /> メモリマネージャーでコミットされたメモリをキロバイト (KB) 単位で表します。 メモリ マネージャー内の予約済みメモリは含まれません。 NULL 値は許可されません。|  
|**bpool_commit_target**|**int**|**適用対象:** [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] から [!INCLUDE[ssKilimanjaro](../../includes/sskilimanjaro-md.md)] 。<br /><br /> SQL Server memory manager で使用できるメモリの量をキロバイト (KB) 単位で表します。|  
|**committed_target_kb**|**int**|**適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降。<br /><br /> SQL Server memory manager で使用できるメモリの量をキロバイト (KB) 単位で表します。 目標値は、次のようなさまざまな入力を使用して計算されます。<br /><br /> -システムの負荷を含むシステムの現在の状態<br /><br /> -現在のプロセスによって要求されたメモリ<br /><br /> -コンピューターにインストールされているメモリの量<br /><br /> -構成パラメーター<br /><br /> **Committed_target_kb** が **committed_kb** より大きい場合、メモリマネージャーは追加のメモリを取得しようとします。 **Committed_target_kb** が **committed_kb** より小さい場合、メモリマネージャーはコミットされたメモリの量を減らします。 **Committed_target_kb** には、常に盗難および予約されたメモリが含まれます。 NULL 値は許可されません。|  
|**bpool_visible**|**int**|**適用対象:** [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] から [!INCLUDE[ssKilimanjaro](../../includes/sskilimanjaro-md.md)] 。<br /><br /> プロセス仮想アドレス空間で直接アクセス可能な、バッファープール内の 8 KB バッファーの数。 アドレスウィンドウ拡張機能 (AWE) を使用していない場合、バッファープールがメモリターゲット (bpool_committed = bpool_commit_target) を取得すると、bpool_visible の値は bpool_committed の値と等しくなります。SQL Server の32ビットバージョンで AWE を使用する場合、bpool_visible は、バッファープールによって割り当てられた物理メモリへのアクセスに使用される AWE マッピングウィンドウのサイズを表します。 このマッピングウィンドウのサイズはプロセスのアドレス空間によって制限されます。したがって、表示される金額は、コミットされた量よりも小さくなります。また、データベースページ以外の目的でメモリを使用する内部コンポーネントによってさらに小さくすることができます。 Bpool_visible の値が小さすぎると、メモリ不足エラーが発生する可能性があります。|  
|**visible_target_kb**|**int**|**適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降。<br /><br /> は **committed_target_kb** と同じです。 NULL 値は許可されません。|  
|**stack_size_in_bytes**|**int**|によって作成された各スレッドの呼び出し履歴のサイズを指定し [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。 NULL 値は許可されません。|  
|**os_quantum**|**bigint**|非プリエンプティブ タスクのクォンタムを表します (ミリ秒単位)。 クォンタム (秒) = **os_quantum** /CPU クロック速度。 NULL 値は許可されません。|  
|**os_error_mode**|**int**|プロセスのエラーモードを指定し [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。 NULL 値は許可されません。|  
|**os_priority_class**|**int**|プロセスの優先度クラスを指定し [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。 NULL 値は許可されます。<br /><br /> 32 = 通常 ([!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] は通常の優先度ベース (= 7) で起動しています)。<br /><br /> 128 = 高 ([!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] は高い優先度ベースで実行しています。  (= 13)。<br /><br /> 詳細については、「 [priority boost サーバー構成オプションの構成](../../database-engine/configure-windows/configure-the-priority-boost-server-configuration-option.md)」を参照してください。|  
|**max_workers_count**|**int**|作成できるワーカーの最大数を表します。 NULL 値は許可されません。|  
|**scheduler_count**|**int**|プロセスで構成されたユーザースケジューラの数を表し [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。 NULL 値は許可されません。|  
|**scheduler_total_count**|**int**|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 内のスケジューラの総数を表します。 NULL 値は許可されません。|  
|**deadlock_monitor_serial_number**|**int**|現在のデッドロック監視シーケンスの ID を指定します。 NULL 値は許可されません。|  
|**sqlserver_start_time_ms_ticks**|**bigint**|最後に開始されたときの **ms_tick** 番号を表し [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。 現在の ms_ticks 列と比較します。 NULL 値は許可されません。|  
|**sqlserver_start_time**|**datetime**|最後に開始されたローカルシステムの日付と時刻を指定し [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。 NULL 値は許可されません。|  
|**affinity_type**|**int**|**適用対象**: [!INCLUDE[ssKilimanjaro](../../includes/sskilimanjaro-md.md)] 以降。<br /><br /> 現在使用中のサーバー CPU プロセス関係の種類を指定します。 NULL 値は許可されません。 詳細については、「 [ALTER SERVER CONFIGURATION &#40;transact-sql&#41;](../../t-sql/statements/alter-server-configuration-transact-sql.md)」を参照してください。<br /><br /> 1 = MANUAL<br /><br /> 2 = 自動|  
|**affinity_type_desc**|**varchar(60)**|**適用対象**: [!INCLUDE[ssKilimanjaro](../../includes/sskilimanjaro-md.md)] 以降。<br /><br /> **Affinity_type** 列について説明します。 NULL 値は許可されません。<br /><br /> MANUAL = 少なくとも 1 台の CPU に関係が設定されています。<br /><br /> AUTO = [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] は、cpu 間でスレッドを自由に移動できます。|  
|**process_kernel_time_ms**|**bigint**|**適用対象**: [!INCLUDE[ssKilimanjaro](../../includes/sskilimanjaro-md.md)] 以降。<br /><br /> すべて [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のスレッドがカーネルモードで費やした合計時間 (ミリ秒単位)。 この値にはサーバー上のすべてのプロセッサの時間が含まれるため、単一のプロセッサ クロックより大きくなる場合があります。 NULL 値は許可されません。|  
|**process_user_time_ms**|**bigint**|**適用対象**: [!INCLUDE[ssKilimanjaro](../../includes/sskilimanjaro-md.md)] 以降。<br /><br /> すべての [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] スレッドがユーザー モードで費やした合計時間 (ミリ秒)。 この値にはサーバー上のすべてのプロセッサの時間が含まれるため、単一のプロセッサ クロックより大きくなる場合があります。 NULL 値は許可されません。|  
|**time_source**|**int**|**適用対象**: [!INCLUDE[ssKilimanjaro](../../includes/sskilimanjaro-md.md)] 以降。<br /><br /> [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]ウォールクロック時間を取得するために使用している API を示します。 NULL 値は許可されません。<br /><br /> 0 = QUERY_PERFORMANCE_COUNTER<br /><br /> 1 = MULTIMEDIA_TIMER|  
|**time_source_desc**|**nvarchar(60)**|**適用対象**: [!INCLUDE[ssKilimanjaro](../../includes/sskilimanjaro-md.md)] 以降。<br /><br /> **Time_source** 列について説明します。 NULL 値は許可されません。<br /><br /> QUERY_PERFORMANCE_COUNTER = [Queryperformancecounter](/windows/win32/api/profileapi/nf-profileapi-queryperformancecounter) API は、ウォールクロック時間を取得します。<br /><br /> MULTIMEDIA_TIMER = ウォールクロック時間を取得する [マルチメディアタイマー](/previous-versions//ms713418(v=vs.85)) API。|  
|**virtual_machine_type**|**int**|**適用対象**: [!INCLUDE[ssKilimanjaro](../../includes/sskilimanjaro-md.md)] 以降。<br /><br /> [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]が仮想化環境で実行されているかどうかを示します。  NULL 値は許可されません。<br /><br /> 0 = NONE<br /><br /> 1 = HYPERVISOR<br /><br /> 2 = OTHER|  
|**virtual_machine_type_desc**|**nvarchar(60)**|**適用対象**: [!INCLUDE[ssKilimanjaro](../../includes/sskilimanjaro-md.md)] 以降。<br /><br /> **Virtual_machine_type** 列について説明します。 NULL 値は許可されません。<br /><br /> NONE = [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] は仮想マシン内で実行されていません。<br /><br /> ハイパーバイザー = [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ハイパーバイザーを実行する os によってホストされる仮想マシン内で実行されている (ハードウェア支援による仮想化を採用したホスト os)。<br /><br /> OTHER = [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] は、Microsoft VIRTUAL PC などのハードウェアアシスタントを使用しない OS によってホストされる仮想マシン内で実行されています。|  
|**softnuma_configuration**|**int**|**適用対象**: [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 以降。<br /><br /> NUMA ノードの構成方法を指定します。 NULL 値は許可されません。<br /><br /> 0 = OFF はハードウェアの既定値を示します。<br /><br /> 1 = 自動ソフト NUMA<br /><br /> 2 = レジストリを使用した手動ソフト NUMA|  
|**softnuma_configuration_desc**|**nvarchar(60)**|**適用対象**: [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 以降。<br /><br /> OFF = ソフト NUMA 機能はオフです。<br /><br /> ON = [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ソフト numa の numa ノードのサイズを自動的に決定します。<br /><br /> MANUAL = 手動で構成されたソフト NUMA|
|**process_physical_affinity**|**nvarchar (3072)** |**適用対象:** 以降 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 。<br /><br />情報はまだ提供されていません。 |
|**sql_memory_model**|**int**|**適用対象:** [!INCLUDE[sssql11](../../includes/sssql11-md.md)] SP4、 [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] SP1 以降。<br /><br />メモリを割り当てるためにによって使用されるメモリモデルを指定し [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。 NULL 値は許可されません。<br /><br />1 = 従来のメモリモデル<br />2 = メモリ内のページをロックする<br /> 3 = メモリ内の大きなページ|
|**sql_memory_model_desc**|**nvarchar(120)**|**適用対象:** [!INCLUDE[sssql11](../../includes/sssql11-md.md)] SP4、 [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] SP1 以降。<br /><br />メモリを割り当てるためにによって使用されるメモリモデルを指定し [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。 NULL 値は許可されません。<br /><br />**従来の**  =  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]は、メモリの割り当てに従来のメモリモデルを使用しています。 これは [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 、起動時にサービスアカウントがメモリのロックページを持っていない場合の既定の sql メモリモデルです。<br />  =  LOCK_PAGES [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]メモリのロックページを使用してメモリを割り当てています。 これは、SQL Server の起動時にサービスアカウントに Lock Pages in Memory 特権がある場合の既定の sql memory manager です。 SQL Server ます。<br />   =  LARGE_PAGES [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]メモリ内の大きなページを使用してメモリを割り当てています。 SQL Server は、サーバーの起動時とトレースフラグ834が有効になっているときに SQL Server サービスアカウントがメモリのロックページを保持しているときに、Large Pages アロケーターを使用して Enterprise edition でのみメモリを割り当てます。|
|**pdw_node_id**|**int**|**適用対象:** [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 、 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]<br /><br /> このディストリビューションが配置されているノードの識別子。|  
|**socket_count** |**int** | **適用対象:** [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] SP2 以降。<br /><br />システムで使用可能なプロセッサソケットの数を指定します。 |  
|**cores_per_socket** |**int** | **適用対象:** [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] SP2 以降。<br /><br />システムで使用可能なソケットあたりのプロセッサ数を指定します。 |  
|**numa_node_count** |**int** | **適用対象:** [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] SP2 以降。<br /><br />システムで使用可能な numa ノードの数を指定します。 この列には、物理 numa ノードとソフト numa ノードが含まれています。 |  
  
## <a name="permissions"></a>権限

で [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] は、 `VIEW SERVER STATE` 権限が必要です。   
SQL Database Basic、S0、S1 のサービス目標、およびエラスティックプール内のデータベースについては、 [サーバー管理者](/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database) アカウントまたは [Azure Active Directory 管理者](/azure/azure-sql/database/authentication-aad-overview#administrator-structure) アカウントが必要です。 その他のすべての SQL Database サービスの目的で `VIEW DATABASE STATE` は、データベースで権限が必要になります。   

## <a name="see-also"></a>参照  
 [動的管理ビューと動的管理関数 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)   
 [SQL Server オペレーティングシステム関連の動的管理ビュー &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sql-server-operating-system-related-dynamic-management-views-transact-sql.md)  
