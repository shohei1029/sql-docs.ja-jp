---
description: sys.dm_db_missing_index_details (Transact-sql)
title: sys.dm_db_missing_index_details (Transact-sql)
ms.custom: ''
ms.date: 03/12/2021
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- sys.dm_db_missing_index_details
- dm_db_missing_index_details
- sys.dm_db_missing_index_details_TSQL
- dm_db_missing_index_details_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- missing indexes feature [SQL Server], sys.dm_db_missing_index_details dynamic management view
- sys.dm_db_missing_index_details dynamic management view
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 994fdc45a49486f1f57214be7646aa89ffb7d7f9
ms.sourcegitcommit: c242f423cc3b776c20268483cfab0f4be54460d4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/25/2021
ms.locfileid: "105551624"
---
# <a name="sysdm_db_missing_index_details-transact-sql"></a>sys.dm_db_missing_index_details (Transact-sql)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  空間インデックスを除く、欠落インデックスに関する詳細情報を返します。  
  
 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] では、動的管理ビューは、データベースの包含に影響する情報を公開することも、ユーザーがアクセスできる他のデータベースに関する情報を公開することもできません。 この情報を公開しないように、接続されたテナントに属していないデータを含むすべての行がフィルターで除外されます。  

  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|**index_handle**|**int**|特定の欠落インデックスの識別子。 識別子はサーバー全体で一意です。 `index_handle` は、このテーブルのキーです。|  
|**database_id**|**smallint**|欠落インデックスを含むテーブルがあるデータベースの識別子。|  
|**object_id**|**int**|インデックスが欠落しているテーブルの識別子。|  
|**equality_columns**|**nvarchar (4000)**|次の形式の等値述語に使用できる列のコンマ区切り一覧。<br /><br /> *表. 列*  =*constant_value*|  
|**inequality_columns**|**nvarchar (4000)**|次の形式のような不等値述語に使用できる列のコンマ区切り一覧。<br /><br /> *表. 列*  > *constant_value*<br /><br /> "=" 以外の比較演算子はすべて、不等値を表します。|  
|**included_columns**|**nvarchar (4000)**|クエリの包括列として必要な列のコンマ区切り一覧。 カバリング列または付加列の詳細については、「 [付加列を使用したインデックスの作成](../../relational-databases/indexes/create-indexes-with-included-columns.md)」を参照してください。<br /><br /> メモリ最適化インデックス (ハッシュとメモリ最適化された非クラスター化) の場合は、無視 `included_columns` します。 すべてのメモリ最適化インデックスには、テーブルのすべての列が含まれています。|  
|**statement**|**nvarchar (4000)**|インデックスが欠落しているテーブルの名前。|  
  
## <a name="remarks"></a>解説  
 によって返される情報は、クエリ `sys.dm_db_missing_index_details` がクエリオプティマイザーによって最適化され、永続化されない場合に更新されます。 欠落したインデックス情報は、データベースエンジンが再起動されるまで保持されます。 欠落インデックスの情報を、サーバーの再利用後も保持する場合は、データベース管理者が情報のバックアップ コピーを定期的に作成する必要があります。 Sys.dm_os_sys_info の列を使用して、 `sqlserver_start_time` データベースエンジンの最後の起動時刻を検索します。 [](sys-dm-os-sys-info-transact-sql.md)   

  
 特定の欠落インデックスが含まれている欠落インデックスグループを特定するには、 `sys.dm_db_missing_index_groups` その列に基づいて、動的管理ビューに対してクエリを実行し `sys.dm_db_missing_index_details` `index_handle` ます。  

  >[!NOTE]
  >この DMV の結果セットは、600行に制限されています。 各行には、欠落しているインデックスが1つ含まれています。 検出されたインデックスの数が600を超えている場合は、既存の不足しているインデックスに対処して、新しいインデックスを表示できるようにする必要があります。 
  
## <a name="using-missing-index-information-in-create-index-statements"></a>CREATE INDEX ステートメントでの欠落インデックス情報の使用  
 によって返された情報を `sys.dm_db_missing_index_details` メモリ最適化インデックスとディスクベースインデックスの両方の CREATE INDEX ステートメントに変換するには、等値列を非等値列の前に配置し、それらを組み合わせてインデックスのキーを作成する必要があります。 付加列は、INCLUDE 句を使用して CREATE INDEX ステートメントに追加します。 等値の列の有効な順序を決定するには、選択度の最も高い列を左の先頭に指定し、選択度が高い順に並べます。  
  
 メモリ最適化インデックスの詳細については、「 [Memory-Optimized テーブルのインデックス](../../relational-databases/in-memory-oltp/indexes-for-memory-optimized-tables.md)」を参照してください。  
  
## <a name="transaction-consistency"></a>トランザクションの一貫性  
 トランザクションでテーブルを作成または削除する場合、削除されたオブジェクトに関する欠落インデックス情報を含む行は、トランザクションの一貫性を保持するためこの動的管理オブジェクトから削除されます。  
  
## <a name="permissions"></a>アクセス許可

で [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] は、 `VIEW SERVER STATE` 権限が必要です。   
SQL Database Basic、S0、S1 のサービス目標、およびエラスティックプール内のデータベースについては、 [サーバー管理者](/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database) アカウントまたは [Azure Active Directory 管理者](/azure/azure-sql/database/authentication-aad-overview#administrator-structure) アカウントが必要です。 その他のすべての SQL Database サービスの目的で `VIEW DATABASE STATE` は、データベースで権限が必要になります。   

## <a name="see-also"></a>参照  
 [sys.dm_db_missing_index_columns &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-missing-index-columns-transact-sql.md)   
 [sys.dm_db_missing_index_groups &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-missing-index-groups-transact-sql.md)   
 [sys.dm_db_missing_index_group_stats &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-missing-index-group-stats-transact-sql.md)  
 [sys.dm_db_missing_index_group_stats_query &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-missing-index-group-stats-query-transact-sql.md)     
 [sys.dm_os_sys_info &#40;Transact-sql&#41;](sys-dm-os-sys-info-transact-sql.md)
