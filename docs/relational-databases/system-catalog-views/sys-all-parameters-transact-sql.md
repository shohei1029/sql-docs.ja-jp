---
description: sys.all_parameters (Transact-sql)
title: sys.all_parameters (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- all_parameters_TSQL
- sys.all_parameters
- all_parameters
- sys.all_parameters_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.all_parameters catalog view
ms.assetid: eecbb68e-9b4c-4243-94e2-8096a9cc7892
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: ec312e3913bdbe321c182f8a56d0ff529a5ac57d
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104748316"
---
# <a name="sysall_parameters-transact-sql"></a>sys.all_parameters (Transact-sql)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  ユーザー定義オブジェクトまたはシステムオブジェクトに属するすべてのパラメーターの和集合を示します。  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|**object_id**|**int**|このパラメーターが属するオブジェクトの ID。|  
|**name**|**sysname**|パラメーターの名前。 は、オブジェクト内で一意です。 オブジェクトがスカラー関数の場合、パラメーター名は、戻り値を表す行の空の文字列になります。|  
|**parameter_id**|**int**|パラメーターの ID。 は、オブジェクト内で一意です。 オブジェクトがスカラー関数の場合、 **parameter_id** = 0 は戻り値を表します。|  
|**system_type_id**|**tinyint**|パラメーターのシステム型の ID。|  
|**user_type_id**|**int**|ユーザーによって定義されたパラメーターの型の ID。<br /><br /> 型の名前を返すには、この列の [型](../../relational-databases/system-catalog-views/sys-types-transact-sql.md) のカタログビューに結合します。|  
|**max_length**|**smallint**|パラメーターの最大長 (バイト単位)。<br /><br /> -1 = 列のデータ型は **varchar (max)**,、 **nvarchar (max)**,、 **varbinary (max)**,、または **xml** です。|  
|**有効桁数 (precision)**|**tinyint**|パラメーターが数値ベースの場合の有効桁数。数値ではない場合は 0 です。|  
|**scale**|**tinyint**|数値ベースの場合は、パラメーターの小数点以下桁数です。それ以外の場合は0です。|  
|**is_output**|**bit**|1 = パラメーターは output (または return) です。それ以外の場合は0です。|  
|**is_cursor_ref**|**bit**|1 = パラメーターはカーソル参照パラメーターです。|  
|**has_default_value**|**bit**|1 = パラメーターには既定値があります。<br /><br /> [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] は、このカタログビュー内の CLR オブジェクトの既定値のみを保持します。したがって、オブジェクトの場合、この列の値は常に0になり [!INCLUDE[tsql](../../includes/tsql-md.md)] ます。 オブジェクトのパラメーターの既定値を表示するに [!INCLUDE[tsql](../../includes/tsql-md.md)] は、 [sys.sql_modules](../../relational-databases/system-catalog-views/sys-sql-modules-transact-sql.md)カタログビューの **定義** 列に対してクエリを実行するか、 [OBJECT_DEFINITION](../../t-sql/functions/object-definition-transact-sql.md)システム関数を使用します。|  
|**is_xml_document**|**bit**|1 = コンテンツは完全な XML ドキュメントです。<br /><br /> 0 = コンテンツがドキュメントフラグメントであるか、列のデータ型が **xml** ではありません。|  
|**default_value**|**sql_variant**|**Has_default_value** が1の場合、この列の値はパラメーターの既定値になります。それ以外の場合は NULL。|  
|**xml_collection_id**|**int**|パラメーターを評価するために使用される XML スキーマ コレクションの ID。<br /><br /> パラメーターのデータ型が **xml** で xml が型指定されている場合は0以外の。<br /><br /> 0 = XML スキーマ コレクションが存在しないか、パラメーターが XML ではありません。|  
  
## <a name="permissions"></a>権限  
 [!INCLUDE[ssCatViewPerm](../../includes/sscatviewperm-md.md)] 詳細については、「 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)」を参照してください。  
  
## <a name="see-also"></a>参照  
 [オブジェクト カタログ ビュー &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/object-catalog-views-transact-sql.md)   
 [カタログ ビュー &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/catalog-views-transact-sql.md)   
 [SQL Server システムカタログに対するクエリについてよく寄せられる質問](../../relational-databases/system-catalog-views/querying-the-sql-server-system-catalog-faq.yml)   
 [Transact-sql&#41;&#40;のパラメーター ](../../relational-databases/system-catalog-views/sys-parameters-transact-sql.md)   
 [sys.system_parameters &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-system-parameters-transact-sql.md)  
  
  
