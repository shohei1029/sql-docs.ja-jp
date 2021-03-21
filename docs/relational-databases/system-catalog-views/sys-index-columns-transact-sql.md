---
description: sys.index_columns (Transact-SQL)
title: sys.index_columns (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 07/03/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- sys.index_columns
- sys.index_columns_TSQL
- index_columns
- index_columns_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.index_columns catalog view
ms.assetid: 211471aa-558a-475c-9b94-5913c143ed12
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 514291047ecb0bda70d215b32b3e68f9b1839d0b
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104745492"
---
# <a name="sysindex_columns-transact-sql"></a>sys.index_columns (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  **Sys** インデックスまたは順序なしテーブル (ヒープ) の一部である列ごとに1行の値を格納します。  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|**object_id**|**int**|インデックスが定義されているオブジェクトの ID。|  
|**index_id**|**int**|列が定義されているインデックスの ID です。|  
|**index_column_id**|**int**|インデックス列の ID。 **index_column_id** は **index_id** 内でのみ一意です。|  
|**column_id**|**int**|**Object_id** 内の列の ID。<br /><br /> 0 = 非クラスター化インデックス内の行識別子 (RID) です。<br /><br /> **column_id** は **object_id** 内でのみ一意です。|  
|**key_ordinal**|**tinyint**|一連のキー列内での 1 から始まる序数です。<br /><br /> 0 = キー列ではないか、XML インデックス、列ストア インデックス、または空間インデックスです。<br /><br /> 注: 基になる列が比較できないため、XML インデックスまたは空間インデックスをキーにすることはできません。つまり、値を並べ替えることはできません。|  
|**partition_ordinal**|**tinyint**|パーティション分割列のセット内での序数 (1 から始まる)。 クラスター化列ストア インデックスには、最大で 1 個のパーティション分割列を含めることができます。<br /><br /> 0 = パーティション分割列ではありません。|  
|**is_descending_key**|**bit**|1 = インデックスキー列には、降順の並べ替え方向があります。<br /><br /> 0 = インデックスキー列に昇順の並べ替え方向があるか、列が列ストアまたはハッシュインデックスの一部です。|  
|**is_included_column**|**bit**|1 = 列は、CREATE INDEX INCLUDE 句を使用してインデックスに追加された非キー列です。または、列が列ストアインデックスの一部です。<br /><br /> 0 = 列は付加列ではありません。<br /><br /> クラスター化キーの一部であるために暗黙的に追加された列は **sys.index_columns** には記載されていません。<br /><br /> パーティション分割列であるために暗黙的に追加された列は、0として返されます。| 
|**column_store_order_ordinal**</br> 適用対象: Azure Synapse Analytics (プレビュー)|**tinyint**|順序付けられたクラスター化列ストアインデックスの順序列のセット内での序数 (1 から始まる)。|
  
## <a name="permissions"></a>権限

 [!INCLUDE[ssCatViewPerm](../../includes/sscatviewperm-md.md)] 詳細については、「 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)」を参照してください。  
  
## <a name="examples"></a>例

 次の例では、`Production.BillOfMaterials` テーブルのすべてのインデックスおよびインデックス列を返します。  
  
```sql
USE AdventureWorks2012;  
GO  
SELECT i.name AS index_name  
    ,COL_NAME(ic.object_id,ic.column_id) AS column_name  
    ,ic.index_column_id  
    ,ic.key_ordinal  
,ic.is_included_column  
FROM sys.indexes AS i  
INNER JOIN sys.index_columns AS ic
    ON i.object_id = ic.object_id AND i.index_id = ic.index_id  
WHERE i.object_id = OBJECT_ID('Production.BillOfMaterials');  
  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```
  
index_name                                                 column_name        index_column_id key_ordinal is_included_column  
---------------------------------------------------------- -----------------  --------------- ----------- -------------  
AK_BillOfMaterials_ProductAssemblyID_ComponentID_StartDate ProductAssemblyID  1               1           0  
AK_BillOfMaterials_ProductAssemblyID_ComponentID_StartDate ComponentID        2               2           0  
AK_BillOfMaterials_ProductAssemblyID_ComponentID_StartDate StartDate          3               3           0  
PK_BillOfMaterials_BillOfMaterialsID                       BillOfMaterialsID  1               1           0  
IX_BillOfMaterials_UnitMeasureCode                         UnitMeasureCode    1               1           0  
  
(5 row(s) affected)  
  
```  
  
## <a name="see-also"></a>参照  
 [オブジェクト カタログ ビュー &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/object-catalog-views-transact-sql.md)   
 [カタログ ビュー &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/catalog-views-transact-sql.md)   
 [sys.indexes &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-indexes-transact-sql.md)   
 [sys.objects &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-objects-transact-sql.md)   
 [CREATE INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/create-index-transact-sql.md)   
 [sys.columns (Transact-SQL)](../../relational-databases/system-catalog-views/sys-columns-transact-sql.md)   
 [SQL Server システム カタログに対するクエリに関してよく寄せられる質問](../../relational-databases/system-catalog-views/querying-the-sql-server-system-catalog-faq.yml)  
  
  
