---
description: sys.dm_io_virtual_file_stats (Transact-sql)
title: sys.dm_io_virtual_file_stats (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 05/11/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- dm_io_virtual_file_stats
- sys.dm_io_virtual_file_stats_TSQL
- sys.dm_io_virtual_file_stats
- dm_io_virtual_file_stats_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_io_virtual_file_stats dynamic management function
ms.assetid: fa3e321f-6fe5-45ff-b397-02a0dd3d6b7d
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 11acce4555f26a6b2455d12a074881fa20f99a50
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104750262"
---
# <a name="sysdm_io_virtual_file_stats-transact-sql"></a>sys.dm_io_virtual_file_stats (Transact-sql)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa.md)]

  データファイルとログファイルの i/o 統計を返します。 この動的管理関数は、 [fn_virtualfilestats](../../relational-databases/system-functions/sys-fn-virtualfilestats-transact-sql.md) 関数を置き換えます。  
  
> [!NOTE]  
>  これをから呼び出すには [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 、 **sys.dm_pdw_nodes_io_virtual_file_stats** という名前を使用します。 

## <a name="syntax"></a>構文  
  
```  
-- Syntax for SQL Server and Azure SQL Database

sys.dm_io_virtual_file_stats (   
    { database_id | NULL },  
    { file_id | NULL }  
)  
```  

```  
-- Syntax for Azure Synapse Analytics

sys.dm_pdw_nodes_io_virtual_file_stats
```
  
## <a name="arguments"></a>引数  


 *database_id* | NULL

 **適用対象:** SQL Server (2008 以降)、Azure SQL Database

 データベースの ID です。 *database_id* は int,、既定値はありません。 有効な入力値は、データベースの ID 番号または NULL です。 NULL を指定した場合、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンス内のすべてのデータベースが返されます。  
  
 組み込み関数 [DB_ID](../../t-sql/functions/db-id-transact-sql.md) を指定できます。  
  
*file_id* | NULL

**適用対象:** SQL Server (2008 以降)、Azure SQL Database
 
ファイルの ID。 *file_id* は int,、既定値はありません。 有効な入力値は、ファイルの ID 番号または NULL です。 NULL を指定すると、データベース上のすべてのファイルが返されます。  
  
 組み込み関数 [FILE_IDEX](../../t-sql/functions/file-idex-transact-sql.md) を指定でき、現在のデータベース内のファイルを参照します。  
  
## <a name="table-returned"></a>返されるテーブル  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|**database_name**|**sysname**|**次のものには適用されません:**: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。<br /><br /> データベース名。</br></br>Azure Synapse Analytics の場合、これは pdw_node_id によって識別されるノードに格納されているデータベースの名前です。 各ノードには、13個のファイルを持つ tempdb データベースが1つあります。 各ノードには、ディストリビューションごとに1つのデータベースがあり、各ディストリビューションデータベースには5つのファイルがあります。 たとえば、各ノードに4つのディストリビューションが含まれている場合、結果には pdw_node_id あたり20個のディストリビューションデータベースファイルが表示されます。 
|**database_id**|**smallint**|データベースの ID。|  
|**file_id**|**smallint**|ファイルの ID。|  
|**sample_ms**|**bigint**|コンピューターの起動後に経過した時間 (ミリ秒単位)。 この列を使用して、この関数とは異なる出力を比較できます。</br></br>のデータ型は **int** です [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 。 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)]|  
|**num_of_reads**|**bigint**|ファイルに対して発行された読み取りの数。|  
|**num_of_bytes_read**|**bigint**|ファイルで読み込まれた総バイト数。|  
|**io_stall_read_ms**|**bigint**|ファイルの読み取りをユーザーが待機した合計時間 (ミリ秒単位)。|  
|**num_of_writes**|**bigint**|このファイルに対して行われた書き込みの数。|  
|**num_of_bytes_written**|**bigint**|ファイルに書き込まれた総バイト数。|  
|**io_stall_write_ms**|**bigint**|ファイルでの書き込み完了をユーザーが待機した総時間 (ミリ秒単位)。|  
|**io_stall**|**bigint**|ファイルでの i/o の完了をユーザーが待機した合計時間 (ミリ秒単位)。|  
|**size_on_disk_bytes**|**bigint**|このファイルのディスクで使用されているバイト数。 スパースファイルの場合、この数は、データベーススナップショットに使用されるディスク上の実際のバイト数です。|  
|**file_handle**|**varbinary**|このファイルの Windows ファイルハンドル。|  
|**io_stall_queued_read_ms**|**bigint**|**は、:: からには適用されません** [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 。<br /><br /> 読み取りの IO リソースガバナンスによって導入された IO 待機時間の合計。 NULL 値は許可されません。 詳細については、「 [sys.dm_resource_governor_resource_pools &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-transact-sql.md)」を参照してください。|  
|**io_stall_queued_write_ms**|**bigint**|**は、:: からには適用されません** [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 。<br /><br />  書き込みの IO リソースガバナンスによって導入された IO 待機時間の合計。 NULL 値は許可されません。|
|**pdw_node_id**|**int**|**適用対象:** [!INCLUDE[ssSDW](../../includes/sssdw-md.md)]</br></br>分布のノードの識別子。
 
## <a name="remarks"></a>解説
カウンタは、SQL Server (MSSQLSERVER) サービスが開始されるたびに空に初期化されます。
  
## <a name="permissions"></a>アクセス許可  
 VIEW SERVER STATE 権限が必要です。 詳細については、「 [transact-sql&#41;&#40;動的管理ビューと関数 ](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)」を参照してください。  
  
## <a name="examples"></a>例  

### <a name="a-return-statistics-for-a-log-file"></a>A. ログファイルの統計を返す

**適用対象:** SQL Server (2008 以降)、Azure SQL Database

 次の例では、[!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] データベースのログ ファイルに関するすべての統計を返します。  
  
```sql  
SELECT * FROM sys.dm_io_virtual_file_stats(DB_ID(N'AdventureWorks2012'), 2);  
GO  
```  
  
### <a name="b-return-statistics-for-file-in-tempdb"></a>B. Tempdb 内のファイルの統計を返す

**適用対象:** Azure Synapse Analytics

```sql
SELECT * FROM sys.dm_pdw_nodes_io_virtual_file_stats 
WHERE database_name = 'tempdb' AND file_id = 2;

```

## <a name="see-also"></a>参照  
 [動的管理ビューと動的管理関数 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)   
 [I O 関連の動的管理ビューおよび関数 &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/i-o-related-dynamic-management-views-and-functions-transact-sql.md)   
 [sys.database_files &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-database-files-transact-sql.md)   
 [sys.master_files &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-master-files-transact-sql.md)  
  
  

