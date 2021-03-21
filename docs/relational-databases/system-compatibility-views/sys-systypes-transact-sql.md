---
description: sys.sysの型 (Transact-sql)
title: sys.sysの型 (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- sys.systypes_TSQL
- systypes_TSQL
- sys.systypes
- systypes
dev_langs:
- TSQL
helpviewer_keywords:
- sys.systypes compatibility view
- systypes system table
ms.assetid: 1b0b1d0c-5f7b-470b-bd52-8bfa922d7889
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: abdd2bcbb66f388d2695a8836ca3f9a56257a9ce
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104748662"
---
# <a name="syssystypes-transact-sql"></a>sys.sysの型 (Transact-sql)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  システムによって提供される、およびデータベースで定義されているユーザー定義データ型ごとに1行のデータを返します。  
  
> [!IMPORTANT]  
>  [!INCLUDE[ssnoteCompView](../../includes/ssnotecompview-md.md)]  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|**name**|**sysname**|データ型の名前。|  
|**xtype**|**tinyint**|物理記憶型です。|  
|**status**|**tinyint**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|**xusertype**|**smallint**|拡張ユーザーの種類。 データ型の数が32767を超えた場合、オーバーフローまたは NULL を返します。|  
|**length**|**smallint**|データ型の物理的な長さ。|  
|**xprec**|**tinyint**|サーバーで使用される内部精度。 クエリでは使用されません。|  
|**xscale**|**tinyint**|内部スケール。サーバーで使用されます。 クエリでは使用されません。|  
|**tdefault**|**int**|このデータ型の整合性チェックが含まれているストアドプロシージャの ID。|  
|**領域**|**int**|このデータ型の整合性チェックが含まれているストアドプロシージャの ID。|  
|**uid**|**smallint**|型の所有者のスキーマ ID です。<br /><br /> [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の前のバージョンからアップグレードしたデータベースの場合、スキーマ ID は所有者のユーザー ID と同じです。<br /><br /> 重要次のいずれかの DDL ステートメントを使用する場合は、sys.sys **\* 型ではなく、sys カタログビューを使用する必要があります。 \* \* \*** [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] [](../../relational-databases/system-catalog-views/sys-types-transact-sql.md) <br /><br /> 型に対する ALTER AUTHORIZATION<br /><br /> CREATE TYPE<br /><br /> ユーザーおよびロールの数が32767を超えた場合、オーバーフローまたは NULL を返します。|  
|**確保**|**smallint**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|**collationid**|**int**|文字ベースの場合、 **collationid** は現在のデータベースの照合順序の id です。それ以外の場合は NULL になります。|  
|**usertype**|**smallint**|ユーザー型の ID です。 データ型の数が32767を超えた場合、オーバーフローまたは NULL を返します。|  
|**variable**|**bit**|可変長データ型です。<br /><br /> 1 = True<br /><br /> 0 = False|  
|**allownulls**|**bit**|このデータ型の既定の null 値の許容属性を示します。 [CREATE TABLE](../../t-sql/statements/create-table-transact-sql.md)または[ALTER TABLE](../../t-sql/statements/alter-table-transact-sql.md)を使用して null 値を許容する場合、この既定値はによってオーバーライドされます。|  
|**type**|**tinyint**|物理記憶データ型です。|  
|**printfmt**|**varchar(255)**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|**prec**|**smallint**|このデータ型の有効桁数。<br /><br /> -1 = **xml** または大きな値の型。|  
|**scale**|**tinyint**|このデータ型の有効桁数に基づく小数点以下桁数です。<br /><br /> NULL = データ型は数値型ではありません。|  
|**規則**|**sysname**|文字ベースの場合、 **collation** は現在のデータベースの照合順序です。それ以外の場合は NULL になります。|  
  
## <a name="see-also"></a>参照  
 [互換性ビュー &#40;Transact-sql&#41;](~/relational-databases/system-compatibility-views/system-compatibility-views-transact-sql.md)   
 [システムビューへのシステムテーブルのマッピング &#40;Transact-sql&#41;](../../relational-databases/system-tables/mapping-system-tables-to-system-views-transact-sql.md)  
  
  
