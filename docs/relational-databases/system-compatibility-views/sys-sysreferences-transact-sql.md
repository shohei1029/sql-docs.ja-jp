---
description: sys.sysreferences (Transact-SQL)
title: sys.sys参照 (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- sys.sysreferences
- sys.sysreferences_TSQL
- sysreferences
- sysreferences_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.sysreferences compatibility view
- sysreferences system table
ms.assetid: 81276f13-202e-4e74-962d-46eb98c98d2e
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: d28d9df5ed31ca6d2c03d4ab1d641d8436789c1e
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104751772"
---
# <a name="syssysreferences-transact-sql"></a>sys.sysreferences (Transact-SQL)
[!INCLUDE [sql-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdbmi-asa-pdw.md)]

  参照される列への FOREIGN KEY 制約定義のマッピングをデータベース内に保持します。  
  
> [!IMPORTANT]  
>  [!INCLUDE[ssnoteCompView](../../includes/ssnotecompview-md.md)]  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|**constid**|**int**|外部キー制約の ID。|  
|**fkeyid**|**int**|参照しているテーブルの ID。|  
|**rkeyid**|**int**|参照されるテーブルの ID。|  
|**rkeyindid**|**smallint**|参照されるキー列を含む参照されるテーブルにおける、一意なインデックスのインデックス ID です。|  
|**keycnt**|**smallint**|キー内の列の数。|  
|**forkeys**|**varbinary(32)**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|**refkeys**|**varbinary(32)**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|**fkeydbid**|**smallint**|予約済み。|  
|**rkeydbid**|**smallint**|予約済み。|  
|**fkey1**|**smallint**|参照している列の列 ID。|  
|**fkey2**|**smallint**|参照している列の列 ID。|  
|**fkey3**|**smallint**|参照している列の列 ID。|  
|**fkey4**|**smallint**|参照している列の列 ID。|  
|**fkey5**|**smallint**|参照している列の列 ID。|  
|**fkey6**|**smallint**|参照している列の列 ID。|  
|**fkey7**|**smallint**|参照している列の列 ID。|  
|**fkey8**|**smallint**|参照している列の列 ID。|  
|**fkey9**|**smallint**|参照している列の列 ID。|  
|**fkey10**|**smallint**|参照している列の列 ID。|  
|**fkey11**|**smallint**|参照している列の列 ID。|  
|**fkey12**|**smallint**|参照している列の列 ID。|  
|**fkey13**|**smallint**|参照している列の列 ID。|  
|**fkey14**|**smallint**|参照している列の列 ID。|  
|**fkey15**|**smallint**|参照している列の列 ID。|  
|**fkey16**|**smallint**|参照している列の列 ID。|  
|**rkey1**|**smallint**|参照されている列の列 ID。|  
|**rkey2**|**smallint**|参照されている列の列 ID。|  
|**rkey3**|**smallint**|参照されている列の列 ID。|  
|**rkey4**|**smallint**|参照されている列の列 ID。|  
|**rkey5**|**smallint**|参照されている列の列 ID。|  
|**rkey6**|**smallint**|参照されている列の列 ID。|  
|**rkey7**|**smallint**|参照されている列の列 ID。|  
|**rkey8**|**smallint**|参照されている列の列 ID。|  
|**rkey9**|**smallint**|参照されている列の列 ID。|  
|**rkey10**|**smallint**|参照されている列の列 ID。|  
|**rkey11**|**smallint**|参照されている列の列 ID。|  
|**rkey12**|**smallint**|参照されている列の列 ID。|  
|**rkey13**|**smallint**|参照されている列の列 ID。|  
|**rkey14**|**smallint**|参照されている列の列 ID。|  
|**rkey15**|**smallint**|参照されている列の列 ID。|  
|**rkey16**|**smallint**|参照されている列の列 ID。|  
  
## <a name="see-also"></a>参照  
 [システムビューへのシステムテーブルのマッピング &#40;Transact-sql&#41;](../../relational-databases/system-tables/mapping-system-tables-to-system-views-transact-sql.md)   
 [互換性ビュー &#40;Transact-SQL&#41;](~/relational-databases/system-compatibility-views/system-compatibility-views-transact-sql.md)  
  
  
