---
description: sys.partition_functions (Transact-sql)
title: sys.partition_functions (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- sys.partition_functions_TSQL
- partition_functions
- sys.partition_functions
- partition_functions_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.partition_functions catalog view
ms.assetid: 96515727-728b-4bea-804a-36ce915b8b75
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 5757db5ee8f22b5a55368afa927be486e8d468fd
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104754922"
---
# <a name="syspartition_functions-transact-sql"></a>sys.partition_functions (Transact-sql)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のパーティション関数ごとに 1 行のデータを保持します。  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|**name**|**sysname**|パーティション関数の名前。 データベース内で一意です。|  
|**function_id**|**int**|パーティション関数 ID です。 データベース内で一意です。|  
|**type**|**char(2)**|関数の種類。<br /><br /> R = 範囲|  
|**type_desc**|**nvarchar(60)**|関数の種類。<br /><br /> RANGE|  
|**ファンアウト**|**int**|関数によって作成されたパーティションの数。|  
|**boundary_value_on_right**|**bit**|範囲パーティション分割用です。<br /><br /> 1 = 境界値は、境界の右側の範囲に含まれています。<br /><br /> 0 = 左。|  
|**is_system**||**適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降。<br /><br /> 1 = オブジェクトは、フルテキスト インデックス フラグメントに使用されます。<br /><br /> 0 = オブジェクトは、フルテキストインデックスフラグメントには使用されません。|  
|**create_date**|**datetime**|関数が作成された日付。|  
|**modify_date**|**datetime**|関数が ALTER ステートメントを使用して最後に変更された日付。|  
  
## <a name="permissions"></a>アクセス許可  
 ロール **public** のメンバーシップが必要です。 詳細については、「 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)」を参照してください。  
  
## <a name="see-also"></a>参照  
 [パーティション関数のカタログビュー &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/partition-function-catalog-views-transact-sql.md)   
 [カタログ ビュー &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/catalog-views-transact-sql.md)   
 [sys.partition_range_values &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-partition-range-values-transact-sql.md)   
 [sys.partition_parameters &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-partition-parameters-transact-sql.md)  
  
  
