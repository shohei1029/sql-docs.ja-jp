---
description: sys.dm_db_partition_stats (Transact-SQL)
title: sys.dm_db_partition_stats (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 05/28/2020
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- dm_db_partition_stats
- dm_db_partition_stats_TSQL
- sys.dm_db_partition_stats_TSQL
- sys.dm_db_partition_stats
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_db_partition_stats dynamic management view
ms.assetid: 9db9d184-b3a2-421e-a804-b18ebcb099b7
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 6c8ae01cd676f1c57db7ec2d4a794c87b0cffe58
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104747972"
---
# <a name="sysdm_db_partition_stats-transact-sql"></a>sys.dm_db_partition_stats (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  現在のデータベースのパーティションごとに、ページ数と行数の情報を返します。  
  
> [!NOTE]  
> またはからこれを呼び出すに [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] は [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] 、 **sys.dm_pdw_nodes_db_partition_stats** という名前を使用します。 Sys.dm_pdw_nodes_db_partition_stats の partition_id は、Azure Synapse Analytics の [パーティション] カタログビューの partition_id とは異なります。
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|**partition_id**|**bigint**|パーティションの ID。 データベース内で一意です。 これは、Azure Synapse Analytics を **除く、カタログビュー** の **partition_id** と同じ値です。|  
|**object_id**|**int**|パーティションが属するテーブルまたはインデックス付きビューのオブジェクト ID。|  
|**index_id**|**int**|パーティションが属するヒープまたはインデックスの ID。<br /><br /> 0 = ヒープ<br /><br /> 1 = クラスター化インデックス<br /><br /> > 1 = 非クラスター化インデックス|  
|**partition_number**|**int**|インデックスまたはヒープ内の、1 から始まるパーティション番号。|  
|**in_row_data_page_count**|**bigint**|パーティションで行内データの格納に使用されているページ数。 パーティションがヒープに属している場合、値はヒープのデータ ページ数になります。 パーティションがインデックスに属している場合、値はリーフ レベルのページ数になります。 (B-tree の非リーフページはカウントに含まれません)。どちらの場合も、IAM (Index Allocation Map) ページは含まれません。 xVelocity メモリ最適化列ストア インデックスでは、常に 0 です。|  
|**in_row_used_page_count**|**bigint**|パーティションで行内データの格納と管理に使用されているページの合計数。 この数には、非リーフ B-tree ページ、IAM ページ、および **in_row_data_page_count** 列内にあるすべてのページが含まれます。 列ストア インデックスでは、常に 0 です。|  
|**in_row_reserved_page_count**|**bigint**|パーティションで行内データの格納と管理に予約されているページの合計数。ページが使用されているかどうかは考慮されません。 列ストア インデックスでは、常に 0 です。|  
|**lob_used_page_count**|**bigint**|パーティションで行外の **text**、**ntext**、**image**、**varchar(max)**、**nvarchar(max)**、**varbinary(max)**、**xml** 型列の格納と管理に使用されているページ数。 IAM ページは含まれます。<br /><br /> パーティションで列ストア インデックスの格納と管理に使用されている LOB の合計数。|  
|**lob_reserved_page_count**|**bigint**|パーティションで行外の **text**、**ntext**、**image**、**varchar(max)**、**nvarchar(max)**、**varbinary(max)**、**xml** 型列の格納と管理に予約されているページの合計数。ページが使用されているかどうかは考慮されません。 IAM ページは含まれます。<br /><br /> パーティションで列ストア インデックスの格納と管理のために予約されている LOB の合計数。|  
|**row_overflow_used_page_count**|**bigint**|パーティションで行オーバーフローの **varchar**、**nvarchar**、**varbinary**、**sql_variant** 型列の格納と管理に使用されているページ数。 IAM ページは含まれます。<br /><br /> 列ストア インデックスでは、常に 0 です。|  
|**row_overflow_reserved_page_count**|**bigint**|パーティションで行オーバーフローの **varchar**、**nvarchar**、**varbinary**、**sql_variant** 型列の格納と管理に予約されているページの合計数。ページが使用されているかどうかは考慮されません。 IAM ページは含まれます。<br /><br /> 列ストア インデックスでは、常に 0 です。|  
|**used_page_count**|**bigint**|パーティションで使用されているページの合計数。 **In_row_used_page_count** lob_used_page_count row_overflow_used_page_count として計算さ  +    +  れます。|  
|**reserved_page_count**|**bigint**|パーティションで予約されているページの合計数。 **In_row_reserved_page_count** lob_reserved_page_count row_overflow_reserved_page_count として計算さ  +    +  れます。|  
|**row_count**|**bigint**|パーティション内の行数の概算値です。|  
|**pdw_node_id**|**int**|**適用対象**: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 、 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]<br /><br /> このディストリビューションが配置されているノードの識別子。|  
|**distribution_id**|**int**|**適用対象**: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 、 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]<br /><br /> 分布に関連付けられている一意の数値 id です。|  
  
## <a name="remarks"></a>解説  
 **sys.dm_db_partition_stats** では、データベースにあるすべてのパーティションの行内データ、LOB データ、行オーバーフロー データについて、格納と管理に使用されている領域に関する情報が表示されます。 ここではパーティションごとに 1 行が表示されます。  
  
 出力の基になる数字は、メモリにキャッシュされるか、各種システム テーブルのディスクに格納されます。  
  
 行内データ、LOB データ、行オーバーフロー データは、パーティションを構成する 3 つのアロケーション ユニットです。 [sys.allocation_units](../../relational-databases/system-catalog-views/sys-allocation-units-transact-sql.md) カタログ ビューに対して、データベースの各アロケーション ユニットに関するメタデータを取得するクエリを実行できます。  
  
 パーティション分割されていないヒープまたはインデックスは、1 つのパーティション (パーティション番号 = 1) で構成されています。したがって、このようなヒープまたはインデックスの場合は 1 行だけが返されます。 [sys.partitions](../../relational-databases/system-catalog-views/sys-partitions-transact-sql.md) カタログ ビューに対して、データベースのすべてのテーブルとインデックスの、各パーティションに関するメタデータを取得するクエリを実行できます。  
  
 各テーブルまたはインデックスの合計数は、関連するすべてのパーティションにおける数を加算することで取得されます。  
  
## <a name="permissions"></a>権限  
 `VIEW DATABASE STATE` `VIEW DEFINITION` には、 **sys.dm_db_partition_stats** 動的管理ビューにクエリを実行するための権限とアクセス許可が必要です。 動的管理ビューに対する権限の詳細については、「 [transact-sql&#41;&#40;の動的管理ビューおよび関数 ](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)」を参照してください。  
  
## <a name="examples"></a>例  
  
### <a name="a-returning-all-counts-for-all-partitions-of-all-indexes-and-heaps-in-a-database"></a>A. データベースにあるすべてのインデックスとヒープに関するすべてのパーティションについて、ページ数や行数の情報を返す  
 次の例では、[!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] データベースにあるすべてのインデックスとヒープに関するすべてのパーティションについて、ページ数や行数を表示します。  
  
```sql  
USE AdventureWorks2012;  
GO  
SELECT * FROM sys.dm_db_partition_stats;  
GO  
```  
  
### <a name="b-returning-all-counts-for-all-partitions-of-a-table-and-its-indexes"></a>B. テーブルとそのインデックスのすべてのパーティションについて、すべてのカウントを返す  
 次の例では、`HumanResources.Employee` テーブルとテーブルのインデックスに関するすべてのパーティションについて、ページ数や行数を表示します。  
  
```sql  
USE AdventureWorks2012;  
GO  
SELECT * FROM sys.dm_db_partition_stats   
WHERE object_id = OBJECT_ID('HumanResources.Employee');  
GO  
```  
  
### <a name="c-returning-total-used-pages-and-total-number-of-rows-for-a-heap-or-clustered-index"></a>C. ヒープまたはクラスター化インデックスについて、合計使用ページ数と合計行数を返す  
 次の例では、`HumanResources.Employee` テーブルのヒープまたはクラスター化インデックスについて、合計使用ページ数と合計行数を返します。 `Employee` テーブルは既定ではパーティション分割されていないため、合計値には 1 つのパーティションだけが含まれます。  
  
```sql  
USE AdventureWorks2012;  
GO  
SELECT SUM(used_page_count) AS total_number_of_used_pages,   
    SUM (row_count) AS total_number_of_rows   
FROM sys.dm_db_partition_stats  
WHERE object_id=OBJECT_ID('HumanResources.Employee')    AND (index_id=0 or index_id=1);  
GO  
```  
  
## <a name="see-also"></a>参照  
 [動的管理ビューと動的管理関数 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)   
 [Transact-sql&#41;&#40;データベース関連の動的管理ビュー ](../../relational-databases/system-dynamic-management-views/database-related-dynamic-management-views-transact-sql.md)  
  
  


