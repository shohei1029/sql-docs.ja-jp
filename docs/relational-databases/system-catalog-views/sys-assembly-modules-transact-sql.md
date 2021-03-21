---
description: sys.assembly_modules (Transact-sql)
title: sys.assembly_modules (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- sys.assembly_modules
- sys.assembly_modules_TSQL
- assembly_modules
- assembly_modules_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.assembly_modules catalog view
ms.assetid: 5f9e644e-8065-49a2-b53d-db7df98f70d8
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: ef736275896ceaa282ff695eb6f1a76c83ac5268
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104754972"
---
# <a name="sysassembly_modules-transact-sql"></a>sys.assembly_modules (Transact-sql)
[!INCLUDE [sql-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdbmi-asa-pdw.md)]

  共通言語ランタイム (CLR) アセンブリによって定義されている関数、プロシージャ、またはトリガーごとに1行の値を返します。 このカタログビューは、CLR ストアドプロシージャ、CLR トリガー、または CLR 関数を基になる実装にマップします。 種類が TA、AF、PC、FS、および FT のオブジェクトには、関連するアセンブリ モジュールがあります。 オブジェクトとアセンブリの間の関連を見つけるには、このカタログ ビューを他のカタログ ビューに結合します。 たとえば、CLR ストアドプロシージャを作成すると、そのストアドプロシージャは、sys. **オブジェクト** 内の1行で表されます。また、sys. procedure 内の 1 **行 (sys** . **objects** から継承) と **sys.assembly_modules** の1行で表されます。 ストアドプロシージャ自体は、 **sys. オブジェクト** と **sys プロシージャ** のメタデータによって表されます。 プロシージャの基になる CLR 実装への参照は **sys.assembly_modules** にあります。  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|**object_id**|**int**|SQL オブジェクトのオブジェクト ID 番号。 データベース内で一意です。|  
|**assembly_id**|**int**|このモジュールが作成されたアセンブリの ID。|  
|**assembly_class**|**sysname**|このモジュールを定義するアセンブリ内のクラスの名前。|  
|**assembly_method**|**sysname**|このモジュールを定義する **assembly_class** 内のメソッドの名前。<br /><br /> 集計関数 (AF) の場合は NULL です。|  
|**null_on_null_input**|**bit**|モジュールは、任意の NULL 入力に対して NULL 出力を生成するように宣言されています。|  
|**execute_as_principal_id**|**int**|CLR 関数、ストアドプロシージャ、またはトリガーの EXECUTE AS 句で指定されているように、コンテキストの実行が発生するデータベースプリンシパルの ID。<br /><br /> NULL = EXECUTE AS CALLER。 これは既定値です。<br /><br /> 指定されたデータベースプリンシパルの ID = EXECUTE AS SELF、EXECUTE AS *user_name*、または execute as *login_name* です。<br /><br /> -2 = EXECUTE AS OWNER。|  
  
## <a name="permissions"></a>権限  
 [!INCLUDE[ssCatViewPerm](../../includes/sscatviewperm-md.md)] 詳細については、「 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)」を参照してください。  
  
## <a name="see-also"></a>参照  
 [オブジェクト カタログ ビュー &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/object-catalog-views-transact-sql.md)   
 [カタログ ビュー &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/catalog-views-transact-sql.md)  
  
  
