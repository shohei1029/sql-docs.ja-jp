---
description: sys.dm_exec_distributed_requests (Transact-sql)
title: sys.dm_exec_distributed_requests (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- DM_EXEC_DISTRIBUTED_REQUESTS
- DM_EXEC_DISTRIBUTED_REQUESTS_TSQL
- SYS.DM_EXEC_DISTRIBUTED_REQUESTS_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- PolyBase,views
- sys.dm_exec_distributed_sql_requests management view
- PolyBase
- dm_exec_distributed_sql_requests management view
ms.assetid: c041d416-d8c6-435e-a563-6a310abd33e3
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 5c9d0961a219664f69705e015554bea42a11afc6
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104735912"
---
# <a name="sysdm_exec_distributed_requests-transact-sql"></a>sys.dm_exec_distributed_requests (Transact-sql)
[!INCLUDE [sqlserver2016-asa-pdw](../../includes/applies-to-version/sqlserver2016-asa-pdw.md)]

  PolyBase クエリで現在または最近アクティブになっているすべての要求に関する情報を保持します。 要求/クエリごとに1行が一覧表示されます。  
  
 ユーザーは、セッションと要求 ID に基づいて、実行するように生成された実際の分散要求を sys.dm_exec_distributed_requests 経由で取得できます。 たとえば、通常の SQL テーブルと外部 SQL テーブルを使用するクエリは、さまざまなコンピューティングノードに対して実行されるさまざまなステートメント/要求に分解されます。 すべてのコンピューティングノードの分散された手順を追跡するには、"グローバル" 実行 ID を導入します。これを使用すると、1つの特定の要求と演算子に関連付けられた計算ノードで、すべての操作を追跡できます。  
  
|列名|データ型|説明|Range|  
|-----------------|---------------|-----------------|-----------|  
|sql_handle|**varbinary(64)**|このビューのキー。 要求に関連付けられている一意の数値 id。|システム内のすべての要求間で一意です。|  
|execution_id|**nvarchar (32**|このクエリが実行されたセッションに関連付けられている一意の数値 id。||  
|status|**nvarchar (32**|要求の現在の状態。|' Pending '、' AquireResources '、' AcquireSystemResources '、' 初期化 '、' Plan '、' 解析中 '、' '、' Running '、' 取り消し '、' Complete '、' Failed '、' 取り消し済み '。|  
|error_id|**nvarchar (36)**|要求に関連付けられているエラーの一意の id (存在する場合)。|エラーが発生しなかった場合は、NULL に設定します。|  
|start_time|**datetime**|要求の実行が開始された時刻。|キューに置かれた要求の場合は0。それ以外の場合は、有効な datetime が現在の時刻以下であることを指定します。|  
|end_time|**datetime**|エンジンが要求のコンパイルを完了した時刻。|キューに置かれた要求またはアクティブな要求の場合は Null です。それ以外の場合は、有効な datetime が現在の時刻以下であることを指定します。|  
|total_elapsed_time|**int**|要求が開始されてから経過した時間 (ミリ秒単位)。|0 ~ start_time と end_time の差。Total_elapsed_time が整数の最大値を超えた場合、total_elapsed_time は引き続き最大値になります。 この条件により、"最大値を超えました。" という警告が生成されます。 ミリ秒単位の最大値は24.8 日に相当します。|  
  
## <a name="see-also"></a>参照  
 [動的管理ビューを使用した PolyBase のトラブルシューティング](/previous-versions/sql/sql-server-2016/mt146389(v=sql.130))   
 [動的管理ビューと動的管理関数 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)   
 [Transact-sql&#41;&#40;データベース関連の動的管理ビュー ](../../relational-databases/system-dynamic-management-views/database-related-dynamic-management-views-transact-sql.md)  
  
