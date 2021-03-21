---
description: sys.workload_management_workload_groups (Transact-sql)
title: sys.workload_management_workload_groups (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 11/05/2019
ms.prod: sql
ms.technology: system-objects
ms.prod_service: synapse-analytics
ms.reviewer: jrasnick
ms.topic: reference
dev_langs:
- TSQL
author: ronortloff
ms.author: rortloff
monikerRange: =azure-sqldw-latest
ms.openlocfilehash: 0a8f145be38259f5f3a600cb7a270ccf193b4672
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104747752"
---
# <a name="sysworkload_management_workload_groups-transact-sql"></a>sys.workload_management_workload_groups (Transact-sql)

[!INCLUDE [asa](../../includes/applies-to-version/asa.md)]

 ワークロードグループの詳細を返します。  
  
|列名|データ型|説明|Range|  
|-----------------|---------------|-----------------|-----------|
|group_id|**int**|ワークロード グループの一意の ID。 NULL 値は許可されません。||
|name|**sysname**|ワークロードグループの名前。 インスタンスに対して一意である必要があります。  NULL 値は許可されません。||
|importance|**nvarchar(128)**|は、このワークロードグループ内の要求の相対的な重要度であり、共有リソースの複数のワークロードグループにまたがっています。 NULL 値は許可されません。|low、below_normal、normal (既定)、above_normal、high||
|min_percentage_resource|**tinyint**|ワークロードグループ内の要求に対して確保されるリソースの量。 リソースは、他のワークロードグループと共有されません。 NULL 値は許可されません。||
|cap_percentage_resource|**tinyint**|ワークロードグループ内の要求に対するリソース割り当ての割合 (%)。 指定されたレベルに割り当てられるリソースの最大数を制限します。 value の許容範囲は 1 ～ 100 です。||
|request_min_resource_grant_percent|**decimal (5, 2)**|要求に割り当てられるリソースの最小量を指定します。 値の許容範囲は 0.75 ~ 100 です。||
|request_max_resource_grant_percent |**decimal (5, 2)**|要求に割り当てられるリソースの最大量を指定します。||
|query_execution_timeout_sec|**int**|クエリが取り消されるまでの実行時間の長さ (秒単位)。  実行の戻りフェーズに達した後は、クエリを取り消すことはできません。  query_execution_timeout_sec には、キューに置かれた時間は含まれません。|
|query_wait_timeout_sec|**int**|INTERNAL||
|create_time|**datetime**|ワークロードグループが作成された時刻。 NULL 値は許可されません。||
modify_time|**datetime**|ワークロードグループが最後に変更された時刻。 NULL 値は許可されません。||
|&nbsp;||||
  
## <a name="permissions"></a>アクセス許可

VIEW SERVER STATE 権限が必要です。

## <a name="next-steps"></a>次のステップ

 Azure Synapse Analytics と Parallel Data Warehouse のすべてのカタログビューの一覧については、「 [Azure Synapse analytics と並列データウェアハウスのカタログビュー](../../relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views.md)」を参照してください。 ワークロードグループを作成するには、「 [ワークロードグループの作成](../../t-sql/statements/create-workload-group-transact-sql.md)」を参照してください。 ワークロードの分類の詳細については、「[ワークロードの分離](/azure/sql-data-warehouse/sql-data-warehouse-workload-isolation)」を参照してください。
