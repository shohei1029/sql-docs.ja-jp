---
description: sys.stats (Transact-SQL)
title: sys. stats (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 12/18/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- sys.stats
- stats_TSQL
- sys.stats_TSQL
- stats
dev_langs:
- TSQL
helpviewer_keywords:
- sys.stats catalog view
ms.assetid: 42605c80-126f-460a-befb-a0b7482fae6a
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 3274dab3393bebd06d479bc0ad91a307ce2d7685
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104750332"
---
# <a name="sysstats-transact-sql"></a>sys.stats (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  データベース内のテーブル、インデックス、およびインデックス付きビューに対して存在する統計オブジェクトごとに1行のデータを格納 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] します。 すべてのインデックスには、同じ名前と ID (**index_id** stats_id) の対応する統計行があり  =  ますが、すべての統計行に対応するインデックスがあるわけではありません。  
  
 カタログビュー [sys.stats_columns](../../relational-databases/system-catalog-views/sys-stats-columns-transact-sql.md) は、データベース内の各列の統計情報を提供します。 統計の詳細については、「[統計](../../relational-databases/statistics/statistics.md)」を参照してください。  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|**object_id**|**int**|これらの統計が属するオブジェクトの ID。|  
|**name**|**sysname**|統計の名前。 は、オブジェクト内で一意です。|  
|**stats_id**|**int**|統計の ID。 は、オブジェクト内で一意です。<br /><br />統計がインデックスに対応している場合、 *stats_id* の値は、 *index_id* [カタログビューの値](../../relational-databases/system-catalog-views/sys-indexes-transact-sql.md) と同じになります。|  
|**auto_created**|**bit**|統計が [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] によって自動的に作成されたかどうかを示します。<br /><br /> 0 = 統計は [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] によって自動的に作成されませんでした。<br /><br /> 1 = 統計は [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] によって自動的に作成されました。|  
|**user_created**|**bit**|統計がユーザーによって作成されたかどうかを示します。<br /><br /> 0 = 統計はユーザーによって作成されませんでした。<br /><br /> 1 = 統計はユーザーによって作成されました。|  
|**no_recompute**|**bit**|統計が **NORECOMPUTE** オプションを使用して作成されたかどうかを示します。<br /><br /> 0 = 統計は、 **NORECOMPUTE** オプションを使用して作成されませんでした。<br /><br /> 1 = 統計は、 **NORECOMPUTE** オプションを使用して作成されました。|  
|**has_filter**|**bit**|0 = 統計にはフィルターがないため、すべての行で計算されます。<br /><br /> 1 = 統計にフィルターがあり、フィルター定義を満たす行についてのみ計算されます。|  
|**filter_definition**|**nvarchar(max)**|フィルター選択された統計情報に含まれる行のサブセットの式。<br /><br /> NULL = フィルター選択されていない統計。|  
|**is_temporary**|**bit**|**適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降。<br /><br /> 統計が一時的なものかどうかを示します。 一時的な統計 [!INCLUDE[ssHADR](../../includes/sshadr-md.md)] は、読み取り専用アクセスが有効になっているセカンダリデータベースをサポートします。<br /><br /> 0 = 統計は一時的ではありません。<br /><br /> 1 = 統計は一時的です。|  
|**is_incremental**|**bit**|**適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降。<br /><br /> 統計が増分統計として作成されているかどうかを示します。<br /><br /> 0 = 統計は増分統計ではありません。<br /><br /> 1 = 統計は増分です。|  
  
## <a name="permissions"></a>権限  
 [!INCLUDE[ssCatViewPerm](../../includes/sscatviewperm-md.md)] 詳細については、「 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)」を参照してください。  
  
## <a name="examples"></a>例  
 次の例では、`HumanResources.Employee` テーブルのすべての統計情報および統計情報列を返します。  
  
```sql  
USE AdventureWorks2012;  
GO  
SELECT s.name AS statistics_name  
      ,c.name AS column_name  
      ,sc.stats_column_id  
FROM sys.stats AS s  
INNER JOIN sys.stats_columns AS sc   
    ON s.object_id = sc.object_id AND s.stats_id = sc.stats_id  
INNER JOIN sys.columns AS c   
    ON sc.object_id = c.object_id AND c.column_id = sc.column_id  
WHERE s.object_id = OBJECT_ID('HumanResources.Employee');  
```  
  
## <a name="see-also"></a>参照  
 [オブジェクト カタログ ビュー &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/object-catalog-views-transact-sql.md)   
 [カタログ ビュー &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/catalog-views-transact-sql.md)   
 [SQL Server システムカタログに対するクエリについてよく寄せられる質問](../../relational-databases/system-catalog-views/querying-the-sql-server-system-catalog-faq.yml)   
 [統計](../../relational-databases/statistics/statistics.md)    
 [sys.dm_db_stats_properties &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-stats-properties-transact-sql.md)   
 [sys.dm_db_stats_histogram &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-stats-histogram-transact-sql.md)   
 [sys.stats_columns &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-stats-columns-transact-sql.md)
 

 
