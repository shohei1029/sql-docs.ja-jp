---
description: sys.dm_exec_compute_node_errors (Transact-sql)
title: sys.dm_exec_compute_node_errors (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 11/04/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- SYS.DM_EXEC_COMPUTE_NODE_ERRORS_TSQL
- DM_EXEC_COMPUTE_NODE_ERRORS
- DM_EXEC_COMPUTE_NODE_ERRORS_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- PolyBase
- PolyBase, views
- dm_exec_compute_node_errors
- sys.dm_exec_compute_node_errors management view
ms.assetid: 9a03c039-70e4-4974-95d8-d3fa45984ffb
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 20a29237c59a558831a0bea0c22eec467e0f4fae
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104750272"
---
# <a name="sysdm_exec_compute_node_errors-transact-sql"></a>sys.dm_exec_compute_node_errors (Transact-sql)

[!INCLUDE [sqlserver2016-asdbmi-asa-pdw](../../includes/applies-to-version/sqlserver2016-asa-pdw.md)]

  PolyBase 計算ノードで発生したエラーを返します。  
  
|列名|データ型|説明|Range|  
|-----------------|---------------|-----------------|-----------|  
|error_id|`nvarchar(36)`|エラーに関連付けられている一意の数値 id。|システム内のすべてのクエリエラー間で一意|  
|source|`nvarchar(255)`|ソーススレッドまたはプロセスの説明||  
|type|`nvarchar(255)`|エラーの種類。||  
|create_time|`datetime`|エラーが発生した時刻||  
|compute_node_id|`int`|特定の計算ノードの識別子|[Transact-sql&#41;&#40;の sys.dm_exec_compute_nodes](../../relational-databases/system-dynamic-management-views/sys-dm-exec-compute-nodes-transact-sql.md)の compute_node_id を参照してください。|  
|rexecution_id|`nvarchar(36)`|PolyBase クエリの識別子 (存在する場合)。||  
|調べる|`int`|SQL Server セッションの識別子||  
|thread_id|`int`|エラーが発生したスレッドの数値識別子。||  
|details|nvarchar(4000)|エラーの詳細の詳細な説明。||
|compute_pool_id|`int`|プールの一意の識別子。|

  
## <a name="see-also"></a>参照  
 [動的管理ビューを使用した PolyBase のトラブルシューティング](/previous-versions/sql/sql-server-2016/mt146389(v=sql.130))   
 [動的管理ビューと動的管理関数 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)   
 [Transact-sql&#41;&#40;データベース関連の動的管理ビュー ](../../relational-databases/system-dynamic-management-views/database-related-dynamic-management-views-transact-sql.md)  
  
