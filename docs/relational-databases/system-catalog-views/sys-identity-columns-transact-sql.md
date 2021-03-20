---
description: sys.identity_columns (Transact-sql)
title: sys.identity_columns (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- identity_columns
- sys.identity_columns
- sys.identity_columns_TSQL
- identity_columns_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.identity_columns catalog view
ms.assetid: 97ee01e6-9c9e-4fd9-884b-68b4084669d5
author: VanMSFT
ms.author: vanto
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 626e2c201330fbed06f906b92f169d0ee307f0f4
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104750072"
---
# <a name="sysidentity_columns-transact-sql"></a>sys.identity_columns (Transact-sql)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  Id 列である列ごとに1行の値を格納します。  
  
 **Sys.identity_columns** ビューでは、 **sys** ビューから行を継承します。 **Sys.identity_columns** ビューでは、列、 **seed_value**、 **increment_value**、 **last_value**、および **is_not_for_replication** 列が返さ **れます。** 詳細については、「[カタログ ビュー &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/catalog-views-transact-sql.md)」を参照してください。  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|**\<columns inherited from sys.columns>**||**Sys.identity_columns** ビューでは、すべての列が返されます、**列、ビュー** です。 また、以下に示す追加の列も返されます。 **Sys.identity_columns** ビューが **sys** から継承する列の説明については、「 [sys &#40;transact-sql&#41;](../../relational-databases/system-catalog-views/sys-columns-transact-sql.md)」を参照してください。|  
|**seed_value**|**sql_variant**|この id 列のシード値。 シード値のデータ型は、列自体のデータ型と同じです。|  
|**increment_value**|**sql_variant**|この ID 列に対する増分値です。 シード値のデータ型は、列自体のデータ型と同じです。|  
|**last_value**|**sql_variant**|この id 列に対して生成された最後の値。 シード値のデータ型は、列自体のデータ型と同じです。|  
|**is_not_for_replication**|**bit**|Id 列は、NOT FOR REPLICATION として宣言されています。 **注:** この列は、Azure Synapse Analytics には適用されません。|  
  
> [!NOTE]  
>  複数のテーブルで使用できる自動的に増分する番号、またはテーブルを参照せずにアプリケーションから呼び出すことができる自動的に増分する番号を作成するには、「[シーケンス番号](../../relational-databases/sequence-numbers/sequence-numbers.md)」を参照してください。  
  
## <a name="permissions"></a>権限  
 [!INCLUDE[ssCatViewPerm](../../includes/sscatviewperm-md.md)] 詳細については、「 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)」を参照してください。  
  
## <a name="see-also"></a>参照  
 [オブジェクト カタログ ビュー &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/object-catalog-views-transact-sql.md)   
 [カタログ ビュー &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/catalog-views-transact-sql.md)   
 [SQL Server システム カタログに対するクエリに関してよく寄せられる質問](../../relational-databases/system-catalog-views/querying-the-sql-server-system-catalog-faq.yml)  
  
  
