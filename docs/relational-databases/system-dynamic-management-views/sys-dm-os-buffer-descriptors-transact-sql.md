---
description: sys.dm_os_buffer_descriptors (Transact-SQL)
title: sys.dm_os_buffer_descriptors (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 08/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- sys.dm_os_buffer_descriptors_TSQL
- dm_os_buffer_descriptors_TSQL
- sys.dm_os_buffer_descriptors
- dm_os_buffer_descriptors
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_os_buffer_descriptors dynamic management view
ms.assetid: 012aab95-8888-4f35-9ea3-b5dff6e3f60f
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 449c4d2ca5a101d17c84b414ffc119dbdb0fa476
ms.sourcegitcommit: 295b9dfc758471ef7d238a2b0f92f93e34acbb1b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106054735"
---
# <a name="sysdm_os_buffer_descriptors-transact-sql"></a>sys.dm_os_buffer_descriptors (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  現在バッファープールにあるすべてのデータページに関する情報を返し [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。 このビューの出力を使用すると、データベース、オブジェクト、または型に応じて、バッファープール内のデータベースページの分布を確認できます。 [!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] では、この動的管理ビューは、バッファー プール拡張ファイルのデータ ページに関する情報も返します。 詳細については、「 [バッファープール拡張](../../database-engine/configure-windows/buffer-pool-extension.md)」を参照してください。  
  
 ディスクから読み込まれたデータ ページは、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のバッファー プールにコピーされ、再使用に備えてキャッシュされます。 キャッシュされた各データページには、1つのバッファー記述子があります。 このバッファー記述子により、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンスに現在キャッシュされている各データ ページが一意に識別されます。 sys.dm_os_buffer_descriptors は、すべてのユーザーデータベースおよびシステムデータベースのキャッシュされたページを返します。 これには、リソースデータベースに関連付けられているページも含まれます。  
  
> **注:** またはからこれを呼び出すに [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] は [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] 、 **sys.dm_pdw_nodes_os_buffer_descriptors** という名前を使用します。  

|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|database_id|**int**|バッファープール内のページに関連付けられているデータベースの ID。 NULL 値が許可されます。|  
|file_id|**int**|ページの永続化されたイメージを格納するファイルの ID。 NULL 値が許可されます。|  
|page_id|**int**|ファイル内のページの ID。 NULL 値が許可されます。|  
|page_level|**int**|ページのインデックス レベル。 NULL 値が許可されます。|  
|allocation_unit_id|**bigint**|ページのアロケーションユニットの ID。 この値は sys.allocation_units の結合に使用できます。 NULL 値が許可されます。|  
|page_type|**nvarchar(60)**|ページの種類。たとえば、データページやインデックスページなどです。 NULL 値が許可されます。|  
|row_count|**int**|ページ上の行の数。 NULL 値が許可されます。|  
|free_space_in_bytes|**int**|ページの使用可能な空き領域のサイズ (バイト単位)。 NULL 値が許可されます。|  
|is_modified|**bit**|1 = ディスクからの読み取り後にページが変更されました。 NULL 値が許可されます。|  
|numa_node|**int**|バッファーの Nonuniform Memory Access ノード。 NULL 値が許可されます。|  
|read_microsec|**bigint**|バッファーにページを読み込むために必要な実際の時間 (マイクロ秒単位)。 この数値は、バッファーが再利用されるとリセットされます。 NULL 値が許可されます。| 
|is_in_bpool_extension|**bit**|1 = ページはバッファープール拡張機能に含まれています。 NULL 値が許可されます。| 
|pdw_node_id|**int**|**適用対象**: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 、 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]<br /><br /> このディストリビューションが配置されているノードの識別子。| 
  
## <a name="permissions"></a>アクセス許可  

で [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] は、 `VIEW SERVER STATE` 権限が必要です。   
SQL Database Basic、S0、S1 のサービス目標、およびエラスティックプール内のデータベースについては、 [サーバー管理者](/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database) アカウントまたは [Azure Active Directory 管理者](/azure/azure-sql/database/authentication-aad-overview#administrator-structure) アカウントが必要です。 その他のすべての SQL Database サービスの目的で `VIEW DATABASE STATE` は、データベースで権限が必要になります。   
   
## <a name="remarks"></a>解説  
 sys.dm_os_buffer_descriptors によって、リソースデータベースによって使用されているページが返されます。 sys.dm_os_buffer_descriptors では、無料または盗難にあったページ、または読み取り時にエラーが発生したページに関する情報は返されません。  
  
|ソース|終了|On|リレーションシップ|  
|----------|--------|--------|------------------|  
|sys.dm_os_buffer_descriptors|sys.databases|database_id|多対一|  
|sys.dm_os_buffer_descriptors|\<userdb>.sys.allocation_units|allocation_unit_id|多対一|  
|sys.dm_os_buffer_descriptors|\<userdb>.sys.database_files|file_id|多対一|  
|sys.dm_os_buffer_descriptors|sys.dm_os_buffer_pool_extension_configuration|file_id|多対一|  
  
## <a name="examples"></a>例  
  
### <a name="a-returning-cached-page-count-for-each-database"></a>A. 各データベースのキャッシュされたページ数を返す  
 次の例では、データベースごとに読み込まれたページ数を返します。  
  
```  
SELECT COUNT(*)AS cached_pages_count  
    ,CASE database_id   
        WHEN 32767 THEN 'ResourceDb'   
        ELSE db_name(database_id)   
        END AS database_name  
FROM sys.dm_os_buffer_descriptors  
GROUP BY DB_NAME(database_id) ,database_id  
ORDER BY cached_pages_count DESC;  
```  
  
### <a name="b-returning-cached-page-count-for-each-object-in-the-current-database"></a>B. 現在のデータベース内の各オブジェクトのキャッシュ ページ数を返す  
 次の例では、現在のデータベース内の各オブジェクトに対して読み込まれたページ数を返します。  
  
```  
SELECT COUNT(*)AS cached_pages_count   
    ,name ,index_id   
FROM sys.dm_os_buffer_descriptors AS bd   
    INNER JOIN   
    (  
        SELECT object_name(object_id) AS name   
            ,index_id ,allocation_unit_id  
        FROM sys.allocation_units AS au  
            INNER JOIN sys.partitions AS p   
                ON au.container_id = p.hobt_id   
                    AND (au.type = 1 OR au.type = 3)  
        UNION ALL  
        SELECT object_name(object_id) AS name     
            ,index_id, allocation_unit_id  
        FROM sys.allocation_units AS au  
            INNER JOIN sys.partitions AS p   
                ON au.container_id = p.partition_id   
                    AND au.type = 2  
    ) AS obj   
        ON bd.allocation_unit_id = obj.allocation_unit_id  
WHERE database_id = DB_ID()  
GROUP BY name, index_id   
ORDER BY cached_pages_count DESC;  
```  
  
## <a name="see-also"></a>参照  
 [sys.allocation_units &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-allocation-units-transact-sql.md)   
 
 [SQL Server オペレーティングシステム関連の動的管理ビュー &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sql-server-operating-system-related-dynamic-management-views-transact-sql.md)   
 [Resource データベース](../../relational-databases/databases/resource-database.md)   
 [sys.dm_os_buffer_pool_extension_configuration &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-os-buffer-pool-extension-configuration-transact-sql.md)  
  
