---
description: sys.database_files (Transact-SQL)
title: sys.database_files (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 09/19/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- sys.database_files
- sys.database_files_TSQL
- database_files
- database_files_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.database_files catalog view
ms.assetid: 0f5b0aac-c17d-4e99-b8f7-d04efc9edf44
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 357727a7f59a8dbce15e7268e00a2278f17f0565
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104753222"
---
# <a name="sysdatabase_files-transact-sql"></a>sys.database_files (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  データベース自体に保存されているデータベースのファイルごとに 1 行のデータを格納します。 これはデータベース単位のビューです。  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|**file_id**|**int**|データベース内のファイルの ID。|  
|**file_guid**|**uniqueidentifier**|ファイルの GUID。<br /><br /> NULL = データベースは、以前のバージョンのからアップグレードされました [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (SQL Server 2005 以前では有効)。|  
|**type**|**tinyint**|ファイルの種類です。<br/><br /> 0 = 行<br /><br/> 1 = ログ<br/><br /> 2 = FILESTREAM<br /><br /> 3 = [!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]<br /><br /> 4 = フルテキスト|  
|**type_desc**|**nvarchar(60)**|ファイルの種類の説明。<br /><br /> ROWS <br /><br /> LOG<br /><br /> FILESTREAM<br /><br /> FULLTEXT|  
|**data_space_id**|**int**|値には0または0より大きい値を指定できます。 値0はデータベースログファイルを表し、0より大きい値はこのデータファイルが格納されているファイルグループの ID を表します。|  
|**name**|**sysname**|データベース内のファイルの論理名。|  
|**physical_name**|**nvarchar(260)**|オペレーティングシステムのファイル名。 データベースが AlwaysOn の [読み取り可能なセカンダリレプリカ](../../database-engine/availability-groups/windows/active-secondaries-readable-secondary-replicas-always-on-availability-groups.md)によってホストされている場合、 **physical_name** は、プライマリレプリカデータベースのファイルの場所を示します。 読み取り可能なセカンダリデータベースの正しいファイルの場所に対して、クエリ [sys.sysaltfiles](../../relational-databases/system-compatibility-views/sys-sysaltfiles-transact-sql.md)を使用します。|  
|**状態**|**tinyint**|ファイルの状態です。<br /><br /> 0 = ONLINE<br /><br /> 1 = 復元中<br /><br /> 2 = 回復中<br /><br /> 3 = RECOVERY_PENDING<br /><br /> 4 = 問題あり<br /><br /> 5 = [!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]<br /><br /> 6 = OFFLINE <br /><br /> 7 = DEFUNCT|  
|**state_desc**|**nvarchar(60)**|ファイルの状態の説明です。<br /><br /> ONLINE<br /><br /> RESTORING<br /><br /> RECOVERING<br /><br /> RECOVERY_PENDING<br /><br /> SUSPECT<br /><br /> OFFLINE<br /><br /> DEFUNCT<br /><br /> 詳しくは、「[ファイルの状態](../../relational-databases/databases/file-states.md)」をご覧ください。|  
|**size**|**int**|ファイルの現在のサイズ (8 KB ページ単位)。<br /><br /> 0 = 適用なし<br /><br /> データベース スナップショットの場合、size は、スナップショットがファイルに対して使用する中で最大の領域を表します。<br /><br /> FILESTREAM ファイルグループコンテナーの場合、サイズはコンテナーの現在使用されているサイズを反映します。|  
|**max_size**|**int**|最大ファイルサイズ (8 KB ページ単位):<br /><br /> 0 = 拡張は許可されません。<br /><br /> -1 = ディスクがいっぱいになるまでファイル サイズが拡張します。<br /><br /> 268435456 = ログファイルは、最大サイズの 2 TB まで拡張されます。<br /><br /> FILESTREAM ファイルグループコンテナーの場合、max_size にはコンテナーの最大サイズが反映されます。<br /><br /> ログファイルのサイズを無制限にしてアップグレードされたデータベースでは、ログファイルの最大サイズに対して-1 が報告されることに注意してください。|  
|**成長**|**int**|0 = ファイルは固定サイズであり、拡張されません。<br /><br /> >0 = ファイルは自動的に拡張されます。<br /><br /> is_percent_growth = 0 の場合、ファイルは 8 KB ページ単位で拡張され、最も近い 64 KB 単位の値に丸められます。<br /><br /> is_percent_growth が 1 の場合、拡張増分は、整数のパーセンテージで表されます。|  
|**is_media_read_only**|**bit**|1 = ファイルは読み取り専用メディア上にあります。<br /><br /> 0 = ファイルは読み取り/書き込みメディア上にあります。|  
|**is_read_only**|**bit**|1 = ファイルは読み取り専用に設定されています。<br /><br /> 0 = ファイルは読み取り/書き込み用としてマークされています。|  
|**is_sparse**|**bit**|1 = ファイルはスパース ファイルです。<br /><br /> 0 = ファイルはスパース ファイルではありません。<br /><br /> 詳細については、「[データベース スナップショットのスパース ファイルのサイズを表示する方法 &#40;Transact-SQL&#41;](../../relational-databases/databases/view-the-size-of-the-sparse-file-of-a-database-snapshot-transact-sql.md)」を参照してください。|  
|**is_percent_growth**|**bit**|1 = ファイルの拡張はパーセンテージで表されます。<br /><br /> 0 = ページ単位の絶対拡張サイズ。|  
|**is_name_reserved**|**bit**|1 = 削除されたファイル名 (名前または physical_name) は、次のログバックアップの後でのみ再利用できます。 ファイルがデータベースから削除されると、ファイルの論理名は次回のログ バックアップまで予約された状態になります。 この列は完全復旧モデルと一括ログ復旧モデルにのみ関係します。|  
|**create_lsn**|**numeric(25,0)**|ファイルが作成されたログ シーケンス番号 (LSN) です。|  
|**drop_lsn**|**numeric(25,0)**|ファイルが削除された LSN。<br /><br /> 0 = ファイル名は再利用できません。|  
|**read_only_lsn**|**numeric(25,0)**|ファイルを含むファイルグループが読み取り/書き込みから読み取り専用 (最新の変更) に変更された LSN。|  
|**read_write_lsn**|**numeric(25,0)**|ファイルを含むファイルグループが読み取り専用から読み取り/書き込み (最新の変更) に変更された LSN。|  
|**differential_base_lsn**|**numeric(25,0)**|差分バックアップのベースです。 この LSN の後に変更されたデータエクステントは、差分バックアップに含まれます。|  
|**differential_base_guid**|**uniqueidentifier**|差分バックアップの基になるベースバックアップの一意識別子。|  
|**differential_base_time**|**datetime**|differential_base_lsn に対応する時間です。|  
|**redo_start_lsn**|**numeric(25,0)**|次のロールフォワードを開始する必要がある LSN です。<br /><br /> state = RESTORING または state = RECOVERY_PENDING でない場合は NULL です。|  
|**redo_start_fork_guid**|**uniqueidentifier**|復旧分岐の一意識別子です。 復元される次のログ バックアップの first_fork_guid は、この値と一致する必要があります。 これは、ファイルの現在の状態を表します。|  
|**redo_target_lsn**|**numeric(25,0)**|このファイルのオンラインロールフォワードが停止できる LSN です。<br /><br /> state = RESTORING または state = RECOVERY_PENDING でない場合は NULL です。|  
|**redo_target_fork_guid**|**uniqueidentifier**|ファイルを回復できる復旧分岐。 redo_target_lsn と組み合わせて使用します。|  
|**backup_lsn**|**numeric(25,0)**|ファイルの最新のデータまたは差分バックアップの LSN。|  
  
> [!NOTE]  
>  大きなインデックスを削除または再構築したり、大きなテーブルを削除したり切り捨てたりすると、では、 [!INCLUDE[ssDE](../../includes/ssde-md.md)] トランザクションがコミットされるまで、実際のページの割り当て解除とそれに関連付けられているロックが延期されます。 遅延削除操作では、割り当てられた領域はすぐに解放されません。 このため、大きなオブジェクトを削除または切り捨てた直後に sys.database_files によって返された値は、実際に使用可能なディスク領域を反映していない可能性があります。  
  
## <a name="permissions"></a>アクセス許可  
 ロール **public** のメンバーシップが必要です。 詳細については、「 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)」を参照してください。  

## <a name="examples"></a>例  
次のステートメントは、各データベースファイルの名前、ファイルサイズ、および空き領域の大きさを返します。

```
SELECT name, size/128.0 FileSizeInMB,
size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0 
   AS EmptySpaceInMB
FROM sys.database_files;
```
を使用する場合の詳細につい [!INCLUDE[ssSDS_md](../../includes/sssds-md.md)] ては、SQL Customer アドバイザリチームブログの「 [Azure SQL Database V12 でのデータベースサイズの決定](/archive/blogs/sqlcat/determining-database-size-in-azure-sql-database-v12) 」を参照してください。
  
## <a name="see-also"></a>参照  
 [データベースとファイルのカタログ ビュー &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/databases-and-files-catalog-views-transact-sql.md)   
 [ファイルの状態](../../relational-databases/databases/file-states.md)   
 [sys.databases &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md)   
 [sys.master_files &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-master-files-transact-sql.md)   
 [Database Files and Filegroups](../../relational-databases/databases/database-files-and-filegroups.md)   
 [sys.data_spaces &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-data-spaces-transact-sql.md)  
  
