---
description: sys.dm_fts_semantic_similarity_population (Transact-sql)
title: sys.dm_fts_semantic_similarity_population (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- dm_fts_semantic_similarity_population_TSQL
- sys.dm_fts_semantic_similarity_population
- dm_fts_semantic_similarity_population
- sys.dm_fts_semantic_similarity_population_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_fts_semantic_similarity_population dynamic management view
ms.assetid: 33666f28-c370-47e2-a932-190316ed5f69
author: pmasl
ms.author: pelopes
ms.reviewer: mikeray
ms.openlocfilehash: d44731be44d6e3356b74ac2f856dbb8c38e2970c
ms.sourcegitcommit: 295b9dfc758471ef7d238a2b0f92f93e34acbb1b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106054726"
---
# <a name="sysdm_fts_semantic_similarity_population-transact-sql"></a>sys.dm_fts_semantic_similarity_population (Transact-sql)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  関連付けられたセマンティックインデックスを持つ各テーブルの各類似性インデックスについて、ドキュメントの類似性のインデックスの作成に関するステータス情報の1行を返します。  
  
 作成手順は抽出手順に従います。 類似性の抽出手順の詳細については、「 [sys.dm_fts_index_population &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-fts-index-population-transact-sql.md)」を参照してください。  
    
|**列名**|**Type**|**説明**|  
|-|-|-|  
|**database_id**|**int**|設定されているフルテキストインデックスを含むデータベースの ID。|  
|**catalog_id**|**int**|フルテキスト インデックスを含む、フルテキスト カタログの ID。|  
|**table_id**|**int**|フルテキストインデックスが設定されるテーブルの ID。|  
|**document_count**|**int**|作成時の総ドキュメント数。|  
|**document_processed_count**|**int**|この作成サイクルの開始以降に処理されたドキュメントの数。|  
|**completion_type**|**int**|この作成が完了した方法の状態。|  
|**completion_type_description**|**nvarchar(120)**|入力候補の種類の説明。|  
|**worker_count**|**int**|類似性抽出に関連付けられているワーカースレッドの数|  
|**status**|**int**|設定の状態。 注: 状態には一時的なものもあります。 次のいずれか:<br /><br /> 3 = 開始<br /><br /> 5 = 正常に処理中<br /><br /> 7 = 処理を停止しました<br /><br /> 11 = 作成が中止されました|  
|**status_description**|**nvarchar(120)**|作成の状態の説明。|  
|**start_time**|**datetime**|作成が開始された時刻。|  
|**incremental_timestamp**|**timestamp**|完全作成の開始タイムスタンプを表します。 その他のすべての母集団の種類では、この値は作成の進行状況を表す最後にコミットされたチェックポイントです。|  
  
## <a name="general-remarks"></a>全般的な解説  
 詳細については、「 [セマンティック検索の管理と監視](../../relational-databases/search/manage-and-monitor-semantic-search.md)」を参照してください。  
  
## <a name="metadata"></a>Metadata  
 セマンティックインデックス作成の状態の詳細については、クエリ [sys.dm_fts_index_population &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-fts-index-population-transact-sql.md)を参照してください。  
  
## <a name="security"></a>Security  
  
### <a name="permissions"></a>アクセス許可  
 サーバーに対する VIEW SERVER STATE 権限が必要です。  
  
## <a name="examples"></a>例  
 次の例では、セマンティックインデックスが関連付けられているすべてのテーブルについて、ドキュメントの類似性のインデックス作成の状態を照会する方法を示します。  
  
```  
SELECT * FROM sys.dm_fts_semantic_similarity_population;  
GO  
```  
  
## <a name="see-also"></a>関連項目  
 [セマンティック検索の管理および監視](../../relational-databases/search/manage-and-monitor-semantic-search.md)  
  
  
