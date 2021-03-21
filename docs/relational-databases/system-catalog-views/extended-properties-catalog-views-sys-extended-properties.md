---
description: 拡張プロパティのカタログビュー-sys.extended_properties
title: sys.extended_properties (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- sys.extended_properties
- sys.extended_properties_TSQL
- extended_properties
- extended_properties_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.extended_properties catalog view
ms.assetid: 439b7299-dce3-4d26-b1c7-61be5e0df82a
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: a431e14f32e84ac2e511d0b16c8efaf13698019f
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104755002"
---
# <a name="extended-properties-catalog-views---sysextended_properties"></a>拡張プロパティのカタログビュー-sys.extended_properties
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  現在のデータベース内の拡張プロパティごとに 1 行のデータを返します。  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|class|**tinyint**|プロパティが存在する項目のクラスを識別します。 以下のいずれかを指定できます。<br /><br /> 0 = データベース<br /><br /> 1 = オブジェクトまたは列<br /><br /> 2 = パラメーター<br /><br /> 3 = スキーマ<br /><br /> 4 = データベース プリンシパル<br /><br /> 5 = アセンブリ<br /><br /> 6 = 型<br /><br /> 7 = インデックス<br /><br /> 8 = ユーザー定義テーブル型の列<br /><br /> 10 = XML スキーマ コレクション<br /><br /> 15 = メッセージの種類<br /><br /> 16 = サービスコントラクト<br /><br /> 17 = サービス<br /><br /> 18 = リモート サービス バインド<br /><br /> 19 = ルート<br /><br /> 20 = データ領域 (ファイル グループまたはパーティションのスキーマ)<br /><br /> 21 = パーティション関数<br /><br /> 22 = データベース ファイル<br /><br /> 27 = プランガイド|  
|class_desc|**nvarchar(60)**|拡張プロパティが存在するクラスの説明です。 以下のいずれかを指定できます。<br /><br /> DATABASE<br /><br /> OBJECT_OR_COLUMN<br /><br /> PARAMETER<br /><br /> SCHEMA<br /><br /> DATABASE_PRINCIPAL<br /><br /> ASSEMBLY<br /><br /> TYPE<br /><br /> INDEX<br /><br /> XML_SCHEMA_COLLECTION<br /><br /> MESSAGE_TYPE<br /><br /> SERVICE_CONTRACT<br /><br /> SERVICE<br /><br /> REMOTE_SERVICE_BINDING<br /><br /> ROUTE<br /><br /> DATASPACE<br /><br /> PARTITION_FUNCTION<br /><br /> DATABASE_FILE<br /><br /> PLAN_GUIDE|  
|major_id|**int**|拡張プロパティが属するアイテムの ID です。アイテムのクラスに従って解釈されます。 ほとんどの項目では、これはクラスが表すものに適用される ID です。 非標準の主要な Id の解釈は次のとおりです。<br /><br /> class が 0 の場合、major_id は常に 0 になります。<br /><br /> class が 1、2、または 7 の場合、major_id は object_id になります。|  
|minor_id|**int**|拡張プロパティが属するアイテムのセカンダリ ID です。アイテムのクラスに従って解釈されます。 ほとんどの項目では、これは0です。それ以外の場合、ID は次のようになります。<br /><br /> class が 1 の場合、minor_id は、列であれば column_id に、オブジェクトであれば 0 になります。<br /><br /> class が 2 の場合、minor_id は parameter_id になります。<br /><br /> class が 7 の場合、minor_id は index_id になります。|  
|name|**sysname**|class、major_id、および minor_id で一意となるプロパティ名です。|  
|値|**sql_variant**|拡張プロパティの値です。|  
  
## <a name="permissions"></a>権限  
 [!INCLUDE[ssCatViewPerm](../../includes/sscatviewperm-md.md)] 詳細については、「 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)」を参照してください。  
  
## <a name="see-also"></a>参照  
 [カタログ ビュー &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/catalog-views-transact-sql.md)   
 [拡張プロパティのカタログビュー &#40;Transact-sql&#41;](./catalog-views-transact-sql.md)   
 [sys.fn_listextendedproperty &#40;Transact-sql&#41;](../../relational-databases/system-functions/sys-fn-listextendedproperty-transact-sql.md)   
 [sp_addextendedproperty &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql.md)   
 [sp_dropextendedproperty &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-dropextendedproperty-transact-sql.md)   
 [sp_updateextendedproperty &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-updateextendedproperty-transact-sql.md)  
  
