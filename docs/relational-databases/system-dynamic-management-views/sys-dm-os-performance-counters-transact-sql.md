---
description: sys.dm_os_performance_counters (Transact-SQL)
title: sys.dm_os_performance_counters (Transact-SQL)
ms.custom: ''
ms.date: 03/22/2021
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- dm_os_performance_counters
- sys.dm_os_performance_counters_TSQL
- dm_os_performance_counters_TSQL
- sys.dm_os_performance_counters
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_os_performance_counters dynamic management view
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 62489b131eea77ed67b1207a7606056cea39066d
ms.sourcegitcommit: c242f423cc3b776c20268483cfab0f4be54460d4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/25/2021
ms.locfileid: "105551635"
---
# <a name="sysdm_os_performance_counters-transact-sql"></a>sys.dm_os_performance_counters (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  サーバーによって管理されているパフォーマンス カウンターごとに 1 つの行を返します。 各パフォーマンスカウンターの詳細については、「 [SQL Server オブジェクトの使用](../../relational-databases/performance-monitor/use-sql-server-objects.md)」を参照してください。  
  
> [!NOTE]  
>  これをまたはから呼び出すに [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] は [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] 、という名前を使用し `sys.dm_pdw_nodes_os_performance_counters` ます。  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|**object_name**|**nchar(128)**|このカウンターが属するカテゴリ。|  
|**counter_name**|**nchar(128)**|カウンターの名前。 カウンターに関する詳細情報を取得するには、[ [オブジェクト SQL Server 使用](../../relational-databases/performance-monitor/use-sql-server-objects.md)するカウンターの一覧から選択するトピックの名前を指定します。 |  
|**instance_name**|**nchar(128)**|カウンターの特定のインスタンスの名前。 多くの場合、データベース名が含まれます。|  
|**cntr_value**|**bigint**|カウンターの現在の値。<br /><br /> **注:** 秒単位のカウンターの場合、この値は累積されます。 レート値は、不連続の時間間隔で値をサンプリングすることによって計算する必要があります。 2つの連続するサンプル値の違いは、使用された時間間隔のレートと同じです。|  
|**cntr_type**|**int**|Windows パフォーマンスアーキテクチャで定義されているカウンターの種類。 パフォーマンスカウンターの種類の詳細については、ドキュメントの「 [WMI パフォーマンスカウンターの種類](/windows/desktop/WmiSdk/wmi-performance-counter-types) 」または Windows Server のドキュメントを参照してください。|  
|**pdw_node_id**|**int**|**適用対象**: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 、 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]<br /><br /> このディストリビューションが配置されているノードの識別子。|  
  
## <a name="remarks"></a>解説  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインストール インスタンスで Windows オペレーティング システムのパフォーマンス カウンターが表示されない場合は、次の [!INCLUDE[tsql](../../includes/tsql-md.md)] クエリを使用して、パフォーマンス カウンターが無効になっていることを確認します。  
  
```sql  
SELECT COUNT(*) FROM sys.dm_os_performance_counters;  
```  
  
戻り値が 0 行の場合、パフォーマンス カウンターが無効であることを意味します。 次に、セットアップログを確認し、エラー3409を検索し `Reinstall sqlctr.ini for this instance, and ensure that the instance login account has correct registry permissions.` ます。これは、パフォーマンスカウンターが有効になっていないことを示します。 3409エラーの直前に発生したエラーは、パフォーマンスカウンターの有効化の失敗の根本原因を示しています。 セットアップログファイルの詳細については、「 [SQL Server セットアップログファイルの表示と読み取り](../../database-engine/install-windows/view-and-read-sql-server-setup-log-files.md)」を参照してください。  

パフォーマンスカウンター列の値が65792の場合は、平均では `cntr_type` なく、最後に計測された値のスナップショットのみが表示されます。 

パフォーマンスカウンター列の `cntr_type` 値が272696320または272696576の場合、サンプリング間隔の1秒間に完了した操作の平均数が表示されます。 このタイプのカウンターは、システム時計のタイマー刻みで時間を計測します。 たとえば、とのカウンターに対してのみ、最後の1秒間のスナップショットと同様の読み取りを取得するには、2つの `Buffer Manager:Lazy writes/sec` `Buffer Manager:Checkpoint pages/sec` コレクションポイント間の差分を比較する必要があります。    

パフォーマンスカウンターで `cntr_type` は、列の値が537003264の場合、そのセットに対するサブセットの比率がパーセンテージとして表示されます。 たとえば、カウンターは `Buffer Manager:Buffer cache hit ratio` キャッシュヒットの合計数とキャッシュ参照の合計数を比較します。 そのため、最後の1秒間だけのスナップショットのような読み取りを行うには、現在の値と2つのコレクションポイント間のベース値 (分母) との差を比較する必要があります。 対応する基本値は、 `Buffer Manager:Buffer cache hit ratio base` `cntr_type` 列の値が1073939712であるパフォーマンスカウンターです。

列の値が1073874176のパフォーマンスカウンターで `cntr_type` は、操作の数に対して処理された項目の比率として、平均して処理される項目の数が表示されます。 たとえば、カウンターは、1秒あたりのロック `Locks:Average Wait Time (ms)` 待機回数を1秒あたりのロック要求数と比較し、待機する各ロック要求の平均待機時間 (ミリ秒単位) を表示します。 そのため、最後の1秒間だけのスナップショットのような読み取りを行うには、現在の値と2つのコレクションポイント間のベース値 (分母) との差を比較する必要があります。 対応する基本値は、 `Locks:Average Wait Time Base` `cntr_type` 列の値が1073939712であるパフォーマンスカウンターです。

DMV のデータ `sys.dm_os_performance_counters` は、データベースエンジンの再起動後に保持されません。 Sys.dm_os_sys_info の列を使用して、 `sqlserver_start_time` データベースエンジンの最後の起動時刻を検索します。 [](sys-dm-os-sys-info-transact-sql.md)   

## <a name="permission"></a>権限

で [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] は、 `VIEW SERVER STATE` 権限が必要です。   
SQL Database Basic、S0、S1 のサービス目標、およびエラスティックプール内のデータベースについては、 [サーバー管理者](/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database) アカウントまたは [Azure Active Directory 管理者](/azure/azure-sql/database/authentication-aad-overview#administrator-structure) アカウントが必要です。 その他のすべての SQL Database サービスの目的で `VIEW DATABASE STATE` は、データベースで権限が必要になります。   
 
## <a name="examples"></a>例  
 次の例では、スナップショットカウンターの値を表示するすべてのパフォーマンスカウンターを返します。  
  
```sql  
SELECT object_name, counter_name, instance_name, cntr_value, cntr_type  
FROM sys.dm_os_performance_counters
WHERE cntr_type = 65792 OR cntr_type = 272696320 OR cntr_type = 537003264;  
```  
  
## <a name="see-also"></a>参照  
  [SQL Server オペレーティングシステム関連の動的管理ビュー &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sql-server-operating-system-related-dynamic-management-views-transact-sql.md)   
 [sys.sysperfinfo &#40;Transact-SQL&#41;](../../relational-databases/system-compatibility-views/sys-sysperfinfo-transact-sql.md)  
 [sys.dm_os_sys_info &#40;Transact-sql&#41;](sys-dm-os-sys-info-transact-sql.md)
