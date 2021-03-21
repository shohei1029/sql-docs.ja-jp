---
description: スキーマ (Transact-sql)
title: スキーマ (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 09/08/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- SCHEMATA_TSQL
- SCHEMATA
dev_langs:
- TSQL
helpviewer_keywords:
- INFORMATION_SCHEMA.SCHEMATA view
- SCHEMATA view
ms.assetid: 69617642-0f54-4b25-b62f-5f39c8909601
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 484cf3165dc602b6e808686fbcc62c5516915422
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104754652"
---
# <a name="schemata-transact-sql"></a>スキーマ (Transact-sql)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  現在のデータベースのスキーマごとに1行のデータを返します。 これらのビューから情報を取得するには、INFORMATION_SCHEMA の完全修飾名を指定し **ます。**_view_name_。 のインスタンス内のすべてのデータベースに関する情報を取得するに [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] は、 [transact-sql&#41;](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) カタログビューを &#40;て、データベースに対してクエリを実行します。  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|**CATALOG_NAME**|**sysname**|現在のデータベースの名前|  
|**SCHEMA_NAME**|**nvarchar (** 128 **)**|スキーマの名前を返します。|  
|**SCHEMA_OWNER**|**nvarchar (** 128 **)**|スキーマの所有者の名前。<br /><br /> **&#42;&#42; の重要な &#42;&#42;** オブジェクトのスキーマを決定するために INFORMATION_SCHEMA ビューを使用しないでください。 INFORMATION_SCHEMA ビューは、オブジェクトのメタデータのサブセットのみを表します。 オブジェクトのスキーマを検索する唯一の信頼性のある方法は、のカタログビューに対してクエリを実行することです。|  
|**DEFAULT_CHARACTER_SET_CATALOG**|**varchar (** 6 **)**|常に NULL が返されます。|  
|**DEFAULT_CHARACTER_SET_SCHEMA**|**varchar (** 3 **)**|常に NULL が返されます。|  
|**DEFAULT_CHARACTER_SET_NAME**|**sysname**|既定の文字セットの名前を返します。|  

**例**  
次の例では、master データベースのスキーマに関する情報を返します。  
```sql  
SELECT * FROM master.INFORMATION_SCHEMA.SCHEMATA;
```  

## <a name="see-also"></a>参照  
 [システムビュー &#40;Transact-sql&#41;](../../t-sql/language-reference.md)   
 [情報スキーマビュー &#40;Transact-sql&#41;](~/relational-databases/system-information-schema-views/system-information-schema-views-transact-sql.md)   
 [sys.databases &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md)   
 [sys.schemas &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/schemas-catalog-views-sys-schemas.md)   
 [sys.sys文字セット &#40;Transact-sql&#41;](../../relational-databases/system-compatibility-views/sys-syscharsets-transact-sql.md)  
  
