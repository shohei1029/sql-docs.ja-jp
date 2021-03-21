---
description: sys.foreign_keys (Transact-sql)
title: sys.foreign_keys (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- foreign_keys
- sys.foreign_keys
- sys.foreign_keys_TSQL
- foreign_keys_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.foreign_keys catalog view
ms.assetid: e960df1a-13fc-43ee-ba91-34c1b719ac2c
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: cbdacfd88d1e807461bd168a4df41ddbed4f49b0
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104745242"
---
# <a name="sysforeign_keys-transact-sql"></a>sys.foreign_keys (Transact-sql)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  FOREIGN KEY 制約であるオブジェクトごとに1行の値を格納します。 **type** = F.  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|**\<Columns inherited from sys.objects>**||このビューが継承する列の一覧については、「 [sys. objects &#40;transact-sql&#41;](../../relational-databases/system-catalog-views/sys-objects-transact-sql.md)」を参照してください。|  
|**referenced_object_id**|**int**|参照されるオブジェクトの ID。|  
|**key_index_id**|**int**|参照されたオブジェクト内のキーインデックスの ID。|  
|**is_disabled**|**bit**|FOREIGN KEY 制約が無効になっています。|  
|**is_not_for_replication**|**bit**|FOREIGN KEY 制約は、NOT FOR REPLICATION オプションを使用して作成されました。|  
|**is_not_trusted**|**bit**|外部キー制約がシステムによって検証されていません。|  
|**delete_referential_action**|**tinyint**|削除が発生したときに、この外部キーに対して宣言された参照アクション。<br /><br /> 0 = 操作なし<br /><br /> 1 = Cascade<br /><br /> 2 = null に設定<br /><br /> 3 = 既定値に設定|  
|**delete_referential_action_desc**|**nvarchar(60)**|削除が発生したときに FOREIGN KEY に対して宣言された参照操作の説明。<br /><br /> NO_ACTION<br /><br /> CASCADE<br /><br /> SET_NULL<br /><br /> SET_DEFAULT|  
|**update_referential_action**|**tinyint**|更新が発生したときに FOREIGN KEY に対して宣言された参照操作。<br /><br /> 0 = 操作なし<br /><br /> 1 = Cascade<br /><br /> 2 = null に設定<br /><br /> 3 = 既定値に設定|  
|**update_referential_action_desc**|**nvarchar(60)**|更新が発生したときに、この外部キーに対して宣言された参照操作の説明。<br /><br /> NO_ACTION<br /><br /> CASCADE<br /><br /> SET_NULL<br /><br /> SET_DEFAULT|  
|**is_system_named**|**bit**|1 = システムによって名前が生成されました。<br /><br /> 0 = ユーザーによって指定された名前。|  
  
## <a name="permissions"></a>権限  
 [!INCLUDE[ssCatViewPerm](../../includes/sscatviewperm-md.md)] 詳細については、「 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)」を参照してください。  
  
## <a name="see-also"></a>参照  
 [カタログ ビュー &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/catalog-views-transact-sql.md)   
 [オブジェクト カタログ ビュー &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/object-catalog-views-transact-sql.md)   
 [SQL Server システム カタログに対するクエリに関してよく寄せられる質問](../../relational-databases/system-catalog-views/querying-the-sql-server-system-catalog-faq.yml)  
  
  
