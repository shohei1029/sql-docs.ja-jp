---
description: sys.system_objects (Transact-SQL)
title: sys.system_objects (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- sys.system_objects
- system_objects
- system_objects_TSQL
- sys.system_objects_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.system_objects catalog view
ms.assetid: 069e9045-97f2-4463-8e8f-c73855f3ea0a
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 18b9f39ab92c433ecafd92cb2fff6a7ec8548d35
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104739672"
---
# <a name="syssystem_objects-transact-sql"></a>sys.system_objects (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  に含まれているすべてのスキーマスコープシステムオブジェクトに対して1行のレコードを格納 [!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] します。 すべてのシステム オブジェクトは、sys または INFORMATION_SCHEMA という名前のスキーマに含まれています。  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|name|**sysname**|オブジェクト名|  
|object_id|**int**|オブジェクト ID 番号。 データベース内で一意です。|  
|principal_id|**int**|スキーマの所有者と異なる場合は、個々の所有者の ID。 既定では、スキーマに含まれるオブジェクトはスキーマの所有者によって所有されます。 ただし、別の所有者を指定するには、ALTER AUTHORIZATION ステートメントを使用して所有権を変更します。<br /><br /> 他の所有者がいない場合は NULL になります。<br /><br /> オブジェクトの型が次のいずれかの場合は NULL になります。<br /><br /> C = CHECK 制約<br /><br /> D = DEFAULT (制約またはスタンドアロン)<br /><br /> F = FOREIGN KEY 制約<br /><br /> PK = PRIMARY KEY 制約<br /><br /> R = Rule (旧形式、スタンドアロン)<br /><br /> TA = アセンブリ (CLR) トリガー<br /><br /> TR = SQL トリガー<br /><br /> UQ = UNIQUE 制約|  
|schema_id|**int**|オブジェクトが含まれているスキーマの ID。<br /><br /> [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] に含まれているすべてのスキーマ スコープ システム オブジェクトに対して、この値は常に (schema_id('sys'), schema_id('INFORMATION_SCHEMA')) の形式で示されます。|  
|parent_object_id|**int**|このオブジェクトが属するオブジェクトの ID です。<br /><br /> 0 = 子オブジェクトではありません。|  
|type|**char(2)**|オブジェクトの種類:<br /><br /> AF = 集計関数 (CLR)<br /><br /> C = CHECK 制約<br /><br /> D = DEFAULT (制約またはスタンドアロン)<br /><br /> F = FOREIGN KEY 制約<br /><br /> FN = SQL スカラー関数<br /><br /> FS = アセンブリ (CLR) スカラー関数<br /><br /> FT = アセンブリ (CLR) テーブル値関数<br /><br /> IF = SQL インライン テーブル値関数<br /><br /> 内部テーブル<br /><br /> P = SQL ストアドプロシージャ<br /><br /> PC = アセンブリ (CLR) ストアドプロシージャ<br /><br /> PG = プランガイド<br /><br /> PK = PRIMARY KEY 制約<br /><br /> R = Rule (旧形式、スタンドアロン)<br /><br /> RF = レプリケーション-フィルター-プロシージャ<br /><br /> S = システム ベース テーブル<br /><br /> SN = シノニム<br /><br /> SQ = サービスキュー<br /><br /> TA = アセンブリ (CLR) DML トリガー<br /><br /> TF = SQL テーブル値関数<br /><br /> TR = SQL DML トリガー<br /><br /> TT = テーブル型<br /><br /> U = テーブル (ユーザー定義)<br /><br /> UQ = UNIQUE 制約<br /><br /> V = ビュー<br /><br /> X = 拡張ストアド プロシージャ|  
|type_desc|**nvarchar(60)**|オブジェクトの種類の説明。 AGGREGATE_FUNCTION<br /><br /> CHECK_CONSTRAINT<br /><br /> DEFAULT_CONSTRAINT<br /><br /> FOREIGN_KEY_CONSTRAINT<br /><br /> SQL_SCALAR_FUNCTION<br /><br /> CLR_SCALAR_FUNCTION<br /><br /> CLR_TABLE_VALUED_FUNCTION<br /><br /> SQL_INLINE_TABLE_VALUED_FUNCTION<br /><br /> INTERNAL_TABLE<br /><br /> SQL_STORED_PROCEDURE<br /><br /> CLR_STORED_PROCEDURE<br /><br /> PLAN_GUIDE<br /><br /> PRIMARY_KEY_CONSTRAINT<br /><br /> RULE<br /><br /> REPLICATION_FILTER_PROCEDURE<br /><br /> SYSTEM_TABLE<br /><br /> SYNONYM<br /><br /> SERVICE_QUEUE<br /><br /> CLR_TRIGGER<br /><br /> SQL_TABLE_VALUED_FUNCTION<br /><br /> SQL_TRIGGER<br /><br /> TABLE_TYPE<br /><br /> USER_TABLE<br /><br /> UNIQUE_CONSTRAINT<br /><br /> VIEW<br /><br /> EXTENDED_STORED_PROCEDURE|  
|create_date|**datetime**|オブジェクトが作成された日付です。|  
|modify_date|**datetime**|オブジェクトが ALTER ステートメントを使用して最後に変更された日付です。 オブジェクトがテーブルまたはビューの場合は、テーブルやビューのクラスター化インデックスが作成または変更されると、modify_date も変更されます。|  
|is_ms_shipped|**bit**|オブジェクトが内部 [!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] コンポーネントによって作成されることを示します。|  
|is_published|**bit**|オブジェクトがパブリッシュされます。|  
|is_schema_published|**bit**|オブジェクトのスキーマのみがパブリッシュされることを示します。|  
  
## <a name="permissions"></a>権限  
 [!INCLUDE[ssCatViewPerm](../../includes/sscatviewperm-md.md)] 詳細については、「 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)」を参照してください。  
  
## <a name="see-also"></a>参照  
 [カタログ ビュー &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/catalog-views-transact-sql.md)   
 [オブジェクト カタログ ビュー &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/object-catalog-views-transact-sql.md)  
  
  
