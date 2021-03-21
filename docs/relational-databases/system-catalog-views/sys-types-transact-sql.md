---
description: sys.types (Transact-SQL)
title: sys. types (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- types
- types_TSQL
- sys.types_TSQL
- sys.types
dev_langs:
- TSQL
helpviewer_keywords:
- sys.types catalog view
- table-valued parameters,sys.types
ms.assetid: a5dbc842-71a0-4f62-b5e0-f560a99b7f8c
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 4b2382aee7cc2b540a18dbcff5cb2ca15b7ba07c
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104747852"
---
# <a name="systypes-transact-sql"></a>sys.types (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  システム型とユーザー定義の型ごとに 1 行のデータを格納します。  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|**name**|**sysname**|型の名前。 スキーマ内で一意です。|  
|**system_type_id**|**tinyint**|型の内部システム型の ID。|  
|**user_type_id**|**int**|型の ID です。 データベース内で一意です。 システムデータ型の場合は、 **user_type_id**  =  **system_type_id** ます。|  
|**schema_id**|**int**|型が属するスキーマの ID。|  
|**principal_id**|**int**|スキーマの所有者と異なる場合は、個々の所有者の ID。 既定では、スキーマに含まれるオブジェクトはスキーマの所有者によって所有されます。 ただし、ALTER AUTHORIZATION ステートメントを使用して所有権を変更することによって、代替所有者を指定できます。<br /><br /> 別の所有者がいない場合は NULL になります。|  
|**max_length**|**smallint**|型の最大長 (バイト単位)。<br /><br /> -1 = 列のデータ型は **varchar (max)**,、 **nvarchar (max)**,、 **varbinary (max)**,、または **xml** です。<br /><br /> **テキスト** 列の場合、 **max_length** 値は16になります。|  
|**有効桁数 (precision)**|**tinyint**|数値ベースの場合の型の最大有効桁数。それ以外の場合は0です。|  
|**scale**|**tinyint**|数値ベースの場合の型の最大小数点以下桁数それ以外の場合は0です。|  
|**collation_name**|**sysname**|文字ベースの場合は、型の照合順序の名前。それ以外の場合は NULL です。|  
|**is_nullable**|**bit**|型は nullable です。|  
|**is_user_defined**|**bit**|1 = ユーザー定義型。<br /><br /> 0 = [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] システム データ型。|  
|**is_assembly_type**|**bit**|1 = 型の実装は CLR アセンブリで定義されています。<br /><br /> 0 = 型はシステムデータ型に基づいてい [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。|  
|**default_object_id**|**int**|[Sp_bindefault](../../relational-databases/system-stored-procedures/sp-bindefault-transact-sql.md)を使用して型にバインドされているスタンドアロンの既定の ID。<br /><br /> 0 = 既定値は存在しません。|  
|**rule_object_id**|**int**|[Sp_bindrule](../../relational-databases/system-stored-procedures/sp-bindrule-transact-sql.md)を使用して型にバインドされているスタンドアロンルールの ID。<br /><br /> 0 = ルールは存在しません。|  
|**is_table_type**|**bit**|型がテーブルであることを示します。|  
  
## <a name="permissions"></a>権限  
 [!INCLUDE[ssCatViewPerm](../../includes/sscatviewperm-md.md)] 詳細については、「 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)」を参照してください。  
  
## <a name="see-also"></a>参照  
 [カタログ ビュー &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/catalog-views-transact-sql.md)   
 [Transact-sql&#41;&#40;スカラー型のカタログビュー ](../../relational-databases/system-catalog-views/scalar-types-catalog-views-transact-sql.md)   
 [ALTER AUTHORIZATION &#40;Transact-SQL&#41;](../../t-sql/statements/alter-authorization-transact-sql.md)   
 [OBJECTPROPERTY &#40;Transact-SQL&#41;](../../t-sql/functions/objectproperty-transact-sql.md)   
 [SQL Server システム カタログに対するクエリに関してよく寄せられる質問](../../relational-databases/system-catalog-views/querying-the-sql-server-system-catalog-faq.yml)  
  
  
