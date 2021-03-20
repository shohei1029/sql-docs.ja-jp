---
description: sys. プロシージャ (Transact-sql)
title: sys. プロシージャ (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- procedures
- sys.procedures_TSQL
- sys.procedures
- procedures_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.procedures catalog view
ms.assetid: d17af274-b2dd-464e-9523-ee1f43e1455b
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 99ac54da3d38fe3b8f93aa70bd97b9fd67256f9f
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104753012"
---
# <a name="sysprocedures-transact-sql"></a>sys. プロシージャ (Transact-sql)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  特定の種類のプロシージャであるオブジェクトごとに1行の値を格納します。 **type** = P、X、RF、および PC。  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|**\<Columns inherited from sys.objects>**||このビューが継承する列の一覧については、「 [sys. objects &#40;transact-sql&#41;](../../relational-databases/system-catalog-views/sys-objects-transact-sql.md) 」を参照してください。|  
|**is_auto_executed**|**bit**|1 = プロシージャは、サーバーの起動時に自動的に実行されます。それ以外の場合は0です。 master データベースのプロシージャに対してのみ設定できます。|  
|**is_execution_replicated**|**bit**|プロシージャの実行をレプリケートします。|  
|**is_repl_serializable_only**|**bit**|プロシージャ実行のレプリケーションは、トランザクションをシリアル化できる場合にのみ実行されます。|  
|**skips_repl_constraints**|**bit**|実行時に、NOT FOR REPLICATION とマークされた制約がスキップされます。|  
  
## <a name="permissions"></a>権限  
 [!INCLUDE[ssCatViewPerm](../../includes/sscatviewperm-md.md)] 詳細については、「 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)」を参照してください。  
  
## <a name="see-also"></a>参照  
 [オブジェクト カタログ ビュー &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/object-catalog-views-transact-sql.md)   
 [カタログ ビュー &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/catalog-views-transact-sql.md)  
  
  
