---
description: sys.dm_os_volume_stats (Transact-sql)
title: sys.dm_os_volume_stats (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 09/03/2020
ms.prod: sql
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- dm_os_volume_stats_TSQL
- dm_os_volume_stats
- sys.dm_os_volume_stats
- sys.dm_os_volume_stats_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_os_volume_stats dynamic management function
ms.assetid: fa1c58ad-8487-42ad-956c-983f2229025f
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: feec80838dbb911b4cc551ba2c260e9d8eea9fd3
ms.sourcegitcommit: 295b9dfc758471ef7d238a2b0f92f93e34acbb1b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106054605"
---
# <a name="sysdm_os_volume_stats-transact-sql"></a>sys.dm_os_volume_stats (Transact-sql)
[!INCLUDE[tsql-appliesto-2008R2SP1-xxxx-xxxx-xxx-md](../../includes/tsql-appliesto-2008R2sp1-xxxx-xxxx-xxx-md.md)]

  指定されたデータベースおよびファイルが格納されているオペレーティングシステムボリューム (ディレクトリ) に関する情報を返し [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。 この動的管理関数は、物理ディスク ドライブの属性を確認する場合や、ディレクトリの使用可能な空き容量に関する情報を取得する場合に使用します。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```  
sys.dm_os_volume_stats (database_id, file_id)  
```  
  
##  <a name="arguments"></a><a name="Arguments"></a> 引数  
 *database_id*  
 データベースの ID です。 *database_id* は **int**, 、既定値はありません。 Nll は指定できません。  
  
 *file_id*  
 ファイルの ID。 *file_id* は **int**,、既定値はありません。 Nll は指定できません。  
  
## <a name="table-returned"></a>返されるテーブル  
  
|**列**|**データの種類**|**説明**|  
|-|-|-|  
|**database_id**|**int**|データベースの ID です。 null にすることはできません。|  
|**file_id**|**int**|ファイルの ID。 null にすることはできません。|  
|**volume_mount_point**|**nvarchar(512)**|ボリュームがルートとするマウント ポイント。 は空の文字列を返すことができます。 Linux オペレーティングシステムでは null を返します。|  
|**volume_id**|**nvarchar(512)**|オペレーティングシステムのボリューム ID。 は空の文字列を返すことができます。 Linux オペレーティングシステムでは null を返します。|  
|**logical_volume_name**|**nvarchar(512)**|論理ボリューム名。 は空の文字列を返すことができます。 Linux オペレーティングシステムでは null を返します。|  
|**file_system_type**|**nvarchar(512)**|ファイルシステムボリュームの種類 (たとえば、NTFS、FAT、RAW)。 は空の文字列を返すことができます。 Linux オペレーティングシステムでは null を返します。|  
|**total_bytes**|**bigint**|ボリュームの合計サイズ (バイト単位)。 null にすることはできません。|  
|**available_bytes**|**bigint**|ボリューム上の使用可能な空き領域。 null にすることはできません。|  
|**supports_compression**|**tinyint**|ボリュームがオペレーティング システムによる圧縮をサポートするかどうかを示します。 Windows では null にすることはできず、Linux オペレーティングシステムでは null を返します。|  
|**supports_alternate_streams**|**tinyint**|ボリュームが代替ストリームをサポートするかどうかを示します。 Windows では null にすることはできず、Linux オペレーティングシステムでは null を返します。|  
|**supports_sparse_files**|**tinyint**|ボリュームがスパースファイルをサポートするかどうかを示します。  Windows では null にすることはできず、Linux オペレーティングシステムでは null を返します。|  
|**is_read_only**|**tinyint**|ボリュームが現在読み取り専用としてマークされているかどうかを示します。 null にすることはできません。|  
|**is_compressed**|**tinyint**|このボリュームが現在圧縮されているかどうかを示します。 Windows では null にすることはできず、Linux オペレーティングシステムでは null を返します。|  
|**incurs_seek_penalty**|**tinyint**|このボリュームをサポートしているストレージの種類を示します。 次のいずれかの値になります。<br /><br />0: 通常、記憶装置が PMM または SSD の場合、このボリュームに対するシークペナルティはありません。<br /><br />1: 通常、記憶装置が HDD の場合、このボリュームのシークペナルティ<br /><br />2: ボリュームが UNC パスまたはマウントされた共有にある場合、記憶域の種類を特定できません<br /><br />NULL: ストレージの種類を Linux オペレーティングシステムで特定できません<br /><br />**適用対象:** [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (以降 [!INCLUDE[sql-server-2019](../../includes/sssql19-md.md)] )|  
  
## <a name="security"></a>Security  
  
### <a name="permissions"></a>アクセス許可  
 `VIEW SERVER STATE` 権限が必要です。  
  
## <a name="examples"></a>例  
  
### <a name="a-return-total-space-and-available-space-for-all-database-files"></a>A. すべてのデータベースファイルの合計領域と使用可能な領域を返す  
 次の例では、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスにあるすべてのデータベース ファイルの合計領域と使用可能な領域 (バイト単位) を返します。  
  
```sql  
SELECT f.database_id, f.file_id, volume_mount_point, total_bytes, available_bytes  
FROM sys.master_files AS f  
CROSS APPLY sys.dm_os_volume_stats(f.database_id, f.file_id);  
```  
  
### <a name="b-return-total-space-and-available-space-for-the-current-database"></a>B. 現在のデータベースの合計領域と使用可能な領域を返す  
 次の例では、現在のデータベースのデータベースファイルの合計領域と使用可能な領域 (バイト単位) を返します。  
  
```sql  
SELECT database_id, f.file_id, volume_mount_point, total_bytes, available_bytes  
FROM sys.database_files AS f  
CROSS APPLY sys.dm_os_volume_stats(DB_ID(f.name), f.file_id);  
```  
  
## <a name="see-also"></a>参照  
 [sys.master_files &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-master-files-transact-sql.md)   
 [sys.database_files &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-database-files-transact-sql.md)  
  
  
