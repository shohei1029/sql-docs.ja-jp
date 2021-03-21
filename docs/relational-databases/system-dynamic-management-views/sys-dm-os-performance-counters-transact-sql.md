---
description: sys.dm_os_performance_counters (Transact-SQL)
title: sys.dm_os_performance_counters (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/13/2017
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
ms.assetid: a1c3e892-cd48-40d4-b6be-2a9246e8fbff
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: a49588f109f6964893904ec7d14293aacd71370e
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104750932"
---
# <a name="sysdm_os_performance_counters-transact-sql"></a>sys.dm_os_performance_counters (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  サーバーによって管理されているパフォーマンス カウンターごとに 1 つの行を返します。 各パフォーマンスカウンターの詳細については、「 [SQL Server オブジェクトの使用](../../relational-databases/performance-monitor/use-sql-server-objects.md)」を参照してください。  
  
> [!NOTE]  
>  またはからこれを呼び出すに [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] は [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] 、 **sys.dm_pdw_nodes_os_performance_counters** という名前を使用します。  
  
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

パフォーマンスカウンターは、 `cntr_type` 列の値が65792、272696320、および537003264で、インスタントスナップショットカウンターの値が表示されます。

パフォーマンスカウンターは、 `cntr_type` 列の値が272696576、1073874176、および1073939712で、インスタントスナップショットではなく、累積カウンター値が表示されます。 そのため、スナップショットのような読み取りを行うには、2つのコレクションポイント間の差分を比較する必要があります。

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
