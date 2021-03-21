---
description: sys.external_data_sources (Transact-SQL)
title: sys.external_data_sources (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
dev_langs:
- TSQL
ms.assetid: 1016db6e-9950-4ae2-a004-bd4171e27359
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: d5cd1f39c1d9970ac7bcb0d33987b374643c8574
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104754942"
---
# <a name="sysexternal_data_sources-transact-sql"></a>sys.external_data_sources (Transact-SQL)

[!INCLUDE [sqlserver2016-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sqlserver2016-asdb-asdbmi-asa-pdw.md)]

  、、およびの現在のデータベース内の外部データソースごとに1行のデータを格納し [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] [!INCLUDE[ssSDS](../../includes/sssds-md.md)] [!INCLUDE[ssSDW](../../includes/sssdw-md.md)] ます。  
  
 のサーバー上の外部データソースごとに1行のデータを格納 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] します。  
  
|列名|データ型|説明|Range|  
|-----------------|---------------|-----------------|-----------|  
|data_source_id|**int**|外部データソースのオブジェクト ID。||  
|name|**sysname**|外部データソースの名前。||  
|location|**nvarchar (4000)**|外部データソースのプロトコル、IP アドレス、およびポートを含む接続文字列。||  
|type_desc|**nvarchar (255)**|データソースの種類が文字列として表示されます。|HADOOP、RDBMS、SHARD_MAP_MANAGER、Remotedataアーカイブ Typeextdatasource|  
|type|**tinyint**|数値として表示されるデータソースの種類。|0-HADOOP<br /><br /> 1-RDBMS<br /><br /> 2-SHARD_MAP_MANAGER<br /><br /> 3-Remotedataアーカイブ Typeextdatasource|  
|resource_manager_location|**nvarchar (4000)**|HADOOP の場合、Hadoop リソースマネージャーの IP とポートの場所を指定します。 これは、Hadoop データソースでジョブを送信するために使用されます。<br /><br /> 他の種類の外部データソースの場合は NULL です。||  
|credential_id|**int**|外部データソースへの接続に使用されるデータベーススコープの資格情報のオブジェクト ID。||  
|database_name|**sysname**|RDBMS 型の場合は、リモートデータベースの名前。 [種類] には、シャードマップマネージャーデータベースの名前 SHARD_MAP_MANAGER ます。 他の種類の外部データソースの場合は NULL です。||  
|shard_map_name|**sysname**|SHARD_MAP_MANAGER 型の場合は、シャードマップの名前を指定します。 他の種類の外部データソースの場合は NULL です。||  
  
## <a name="permissions"></a>アクセス許可  
 カタログ ビューでのメタデータの表示が、ユーザーが所有しているかそのユーザーが権限を許可されている、セキュリティ保護可能なメタデータに制限されます。 詳細については、「 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)」を参照してください。  
  
## <a name="see-also"></a>参照  
 [sys.external_file_formats &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-external-file-formats-transact-sql.md)   
 [sys.external_tables &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-external-tables-transact-sql.md)   
 [CREATE EXTERNAL DATA SOURCE &#40;Transact-SQL&#41;](../../t-sql/statements/create-external-data-source-transact-sql.md)  
  
  
