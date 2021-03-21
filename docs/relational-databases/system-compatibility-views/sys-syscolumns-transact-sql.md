---
description: sys.syscolumns (Transact-SQL)
title: sys.sysの列 (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- sys.syscolumns
- sys.syscolumns_TSQL
- syscolumns_TSQL
- syscolumns
dev_langs:
- TSQL
helpviewer_keywords:
- syscolumns system table
- sys.syscolumns compatibility view
ms.assetid: 863fd87b-ff33-4ac5-9aa9-df21140681da
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: b37f7981477541ee02b044ef86a8654ace544811
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104755502"
---
# <a name="syssyscolumns-transact-sql"></a>sys.syscolumns (Transact-SQL)
[!INCLUDE [sql-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdbmi-asa-pdw.md)]

  すべてのテーブルおよびビューのすべての列に対して1行、およびデータベース内のストアドプロシージャ内の各パラメーターの行を返します。  
  
> [!IMPORTANT]  
>  [!INCLUDE[ssnoteCompView](../../includes/ssnotecompview-md.md)]  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|**name**|**sysname**|列またはプロシージャパラメーターの名前。|  
|**ID**|**int**|この列が所属するテーブルのオブジェクト ID またはこのパラメーターが使用されているストアド プロシージャの ID です。|  
|**xtype**|**tinyint**|**Sys. 型** からの物理ストレージ型。|  
|**typestat**|**tinyint**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|**xusertype**|**smallint**|拡張ユーザー定義データ型の ID。 データ型の数が32767を超えた場合、オーバーフローまたは NULL を返します。|  
|**length**|**smallint**|**Sys** からの最大物理ストレージ長。**型**。|  
|**xprec**|**tinyint**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|**xscale**|**tinyint**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|**colid**|**smallint**|列またはパラメーター ID。|  
|**xoffset**|**smallint**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|**bitpos**|**tinyint**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|**確保**|**tinyint**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|**colstat**|**smallint**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|**cdefault**|**int**|この列の既定値の ID。|  
|**領域**|**int**|この列のルールまたは CHECK 制約の ID。|  
|**number**|**smallint**|グループ化しているプロシージャの場合は、サブプロシージャ番号です。<br /><br /> 0 = プロシージャ以外のエントリ|  
|**colorder**|**smallint**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|**autoval**|**varbinary(8000)**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|**offset**|**smallint**|この列が表示される行にオフセットします。|  
|**collationid**|**int**|列の照合順序の ID。 非文字ベースの列の場合は NULL です。|  
|**status**|**tinyint**|列またはパラメーターのプロパティを説明するビットマップです。<br /><br /> 0x08 = NULL 入力を許可する列です。<br /><br /> 0x10 = **varchar** または **varbinary** 列が追加されたときに、ANSI padding が有効になりました。 **Varchar** の後続の空白は保持され、 **varbinary** 列の後続のゼロは保持されます。<br /><br /> 0x40 = OUTPUT パラメーターです。<br /><br /> 0x80 = 列は ID 列です。|  
|**type**|**tinyint**|**Sys** からの物理ストレージの種類。**型**。|  
|**usertype**|**smallint**|**Sys. 型** からのユーザー定義データ型の ID。 データ型の数が32767を超えた場合、オーバーフローまたは NULL を返します。|  
|**printfmt**|**varchar(255)**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|**prec**|**smallint**|この列の有効桁数。<br /><br /> -1 = **xml** または大きな値の型。|  
|**scale**|**int**|この列の小数点以下桁数。<br /><br /> NULL = データ型は数値型ではありません。|  
|**iscomputed**|**int**|列が計算されるかどうかを示すフラグです。<br /><br /> 0 = 非計算列<br /><br /> 1 = 計算列|  
|**isoutparam**|**int**|プロシージャパラメーターが出力パラメーターかどうかを示します。<br /><br /> 1 = True<br /><br /> 0 = False|  
|**isnullable**|**int**|列が NULL 値を許容するかどうかを示します。<br /><br /> 1 = True<br /><br /> 0 = False|  
|**規則**|**sysname**|列の照合順序の名前です。 文字ベースの列ではない場合は NULL です。|  
  
## <a name="see-also"></a>参照  
 [システムビューへのシステムテーブルのマッピング &#40;Transact-sql&#41;](../../relational-databases/system-tables/mapping-system-tables-to-system-views-transact-sql.md)   
 [互換性ビュー &#40;Transact-SQL&#41;](~/relational-databases/system-compatibility-views/system-compatibility-views-transact-sql.md)  
  
  
