---
description: sys.syscharsets (Transact-SQL)
title: sys.sys文字セット (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- sys.syscharsets
- syscharsets
- sys.syscharsets_TSQL
- syscharsets_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- syscharsets system table
- sys.syscharsets compatibility view
ms.assetid: f16d987c-bd19-4668-9ef7-785b8fb9ff5b
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 7ed22e5d8862e01335f2771ae5161251852dddc6
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104755192"
---
# <a name="syssyscharsets-transact-sql"></a>sys.syscharsets (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  で使用するために定義された文字セットと並べ替え順序ごとに1行の値を格納し [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] ます。 並べ替え順序の1つは、 **sysconfigures** で既定の並べ替え順序としてマークされています。 実際はこの並べ替え順だけが使用されます。  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|**type**|**smallint**|この行で表されるエンティティの種類。<br /><br /> 1001 = 文字セット。<br /><br /> 2001 = 並べ替え順|  
|**ID**|**tinyint**|文字セットまたは並べ替え順を表す一意な ID。 注並べ替え順序と文字セットで同じ ID 番号を共有することはできません。 1 ～ 240 の ID は、[!INCLUDE[ssDE](../../includes/ssde-md.md)]が使用するために予約されています。|  
|**csid**|**tinyint**|文字セットを表す行の場合、このフィールドは使用されません。 並べ替え順を表す行の場合は、その並べ替え順が適用される文字セットの ID になります。 このテーブルには、この ID を持つ文字セットの行が存在すると見なされます。|  
|**status**|**smallint**|内部システム状態の情報ビット。|  
|**name**|**sysname**|文字セットまたは並べ替え順序の一意の名前。 このフィールドには、A ~ Z または a ~ z の文字、数字 0-9、およびアンダースコア (_) のみを含める必要があります。また、先頭にはアルファベットを使用する必要があります。|  
|**description**|**nvarchar (255)**|文字セットまたは並べ替え順序の機能について説明します (省略可能)。|  
|**binarydefinition**|**varbinary (6000)**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|**カスタム**|**image**|文字セットまたは並べ替え順序の内部定義。 このフィールド内のデータの構造は、型によって異なります。|  
  
## <a name="see-also"></a>参照  
 [システムビューへのシステムテーブルのマッピング &#40;Transact-sql&#41;](../../relational-databases/system-tables/mapping-system-tables-to-system-views-transact-sql.md)   
 [互換性ビュー &#40;Transact-SQL&#41;](~/relational-databases/system-compatibility-views/system-compatibility-views-transact-sql.md)  
  
  
