---
description: sp_spaceused (Transact-SQL)
title: sp_spaceused (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 08/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- sp_spaceused_TSQL
- sp_spaceused
dev_langs:
- TSQL
helpviewer_keywords:
- sp_spaceused
ms.assetid: c6253b48-29f5-4371-bfcd-3ef404060621
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 97a2050e9b4c4486f8dbf6e64fde08fdc0e88678
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104745372"
---
# <a name="sp_spaceused-transact-sql"></a>sp_spaceused (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  現在のデータベースのテーブル、インデックス付きビュー、または [!INCLUDE[ssSB](../../includes/sssb-md.md)] キューで使用されている、行数、ディスクの予約領域、およびディスク使用領域を表示します。また、データベース全体で使用されているディスクの予約領域とディスク使用領域を表示します。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql  
sp_spaceused [[ @objname = ] 'objname' ]   
[, [ @updateusage = ] 'updateusage' ]  
[, [ @mode = ] 'mode' ]  
[, [ @oneresultset = ] oneresultset ]  
[, [ @include_total_xtp_storage = ] include_total_xtp_storage ]
```  
[!INCLUDE[synapse-analytics-od-unsupported-syntax](../../includes/synapse-analytics-od-unsupported-syntax.md)]
  
## <a name="arguments"></a>引数  

[!INCLUDE[sssdw-md](../../includes/sssdw-md.md)]と [!INCLUDE[sspdw-md](../../includes/sspdw-md.md)] では、 `sp_spaceused` `sp_spaceused (@objname= N'Table1');` パラメーターの序数位置に依存するのではなく、名前付きパラメーターを指定する必要があります。 

`[ @objname = ] 'objname'`
   
 領域の使用情報を要求するテーブル、インデックス付きビュー、またはキューの、修飾付きまたは修飾なしの名前を指定します。 引用符は、修飾されたオブジェクト名が指定されている場合にのみ必要です。 完全修飾オブジェクト名 (データベース名を含む) が指定されている場合、データベース名は現在のデータベースの名前である必要があります。  
場合 *objname* が指定されていない、データベース全体の結果が返されます。  
*objname* は **nvarchar (776)**,、既定値は NULL です。  
> [!NOTE]  
> [!INCLUDE[sssdw-md](../../includes/sssdw-md.md)] と [!INCLUDE[sspdw-md](../../includes/sspdw-md.md)] は、データベースオブジェクトとテーブルオブジェクトのみをサポートしています。
  
`[ @updateusage = ] 'updateusage'` 領域の使用状況情報を更新するために DBCC UPDATEUSAGE を実行する必要があることを示します。 場合 *objname* が指定されていない、ステートメントは、データベース全体で実行されます。それ以外の場合、ステートメントは *objname* で実行されます。 値は **true** または **false** です。 *updateusage* は **varchar (5)**,、既定値は **false** です。  
  
`[ @mode = ] 'mode'` 結果のスコープを示します。 拡張されたテーブルまたはデータベースの場合、 *mode* パラメーターを使用すると、オブジェクトのリモート部分を含めたり、除外したりできます。 詳細については、「 [Stretch Database](../../sql-server/stretch-database/stretch-database.md)」を参照してください。  
  
 *Mode* 引数には、次の値を指定できます。  
  
|値|説明|  
|-----------|-----------------|  
|ALL|ローカル部分とリモート部分の両方を含む、オブジェクトまたはデータベースのストレージの統計を返します。|  
|LOCAL_ONLY|オブジェクトまたはデータベースのローカル部分のみのストレージ統計情報を返します。 オブジェクトまたはデータベースで Stretch が有効になっていない場合、は = ALL と同じ統計を返し @mode ます。|  
|REMOTE_ONLY|オブジェクトまたはデータベースのリモート部分のみのストレージ統計情報を返します。 このオプションでは、次のいずれかの条件に該当する場合にエラーが発生します。<br /><br /> テーブルで Stretch が有効になっていません。<br /><br /> テーブルで Stretch が有効になっていますが、データ移行が有効になっていません。 この場合、リモートテーブルにはまだスキーマがありません。<br /><br /> ユーザーはリモートテーブルを手動で削除しました。<br /><br /> リモートデータアーカイブのプロビジョニングは成功の状態を返しましたが、実際には失敗しました。|  
  
 *モード* は **varchar (11)**,、既定値は **N'ALL '** です。  
  
`[ @oneresultset = ] oneresultset` 1つの結果セットを返すかどうかを示します。 *Oneresultset* 引数には、次の値を指定できます。  
  
|値|説明|  
|-----------|-----------------|  
|0|*\@ Objname* が null またはが指定されていない場合、2つの結果セットが返されます。 2つの結果セットが既定の動作です。|  
|1|*\@ Objname* = null またはが指定されていない場合、1つの結果セットが返されます。|  
  
 *oneresultset* は **ビット**,、既定値は **0** です。  

`[ @include_total_xtp_storage] 'include_total_xtp_storage'`
**適用対象:** [!INCLUDE[sssql17-md](../../includes/sssql17-md.md)] 、 [!INCLUDE[sssds-md](../../includes/sssds-md.md)] 。  
  
 @oneresultset= 1 の場合、パラメーターは、 @include_total_xtp_storage 1 つの結果セットに MEMORY_OPTIMIZED_DATA ストレージの列を含めるかどうかを決定します。 既定値は0です。つまり、既定では (パラメーターが省略されている場合)、XTP 列は resultset に含まれません。  

## <a name="return-code-values"></a>リターン コードの値  
 0 (成功) または 1 (失敗)  
  
## <a name="result-sets"></a>結果セット  
 *Objname* を省略し、 *oneresultset* の値が0の場合、現在のデータベースサイズ情報を提供するために、次の結果セットが返されます。  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|**database_name**|**nvarchar(128)**|現在のデータベースの名前。|  
|**database_size**|**varchar (18)**|現在のデータベースのサイズ (MB 単位)。 **database_size** には、データファイルとログファイルの両方が含まれます。|  
|**未割り当て領域**|**varchar (18)**|データベースオブジェクト用に予約されていないデータベース内の領域。|  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|**確保**|**varchar (18)**|データベース内でオブジェクトによって割り当てられた領域の合計。|  
|**data**|**varchar (18)**|データの使用領域の合計。|  
|**index_size**|**varchar (18)**|インデックスによって使用されている領域の合計サイズ。|  
|**未使用**|**varchar (18)**|データベース内のオブジェクト用に予約されている領域の合計サイズ。ただし、まだ使用されていません。|  
  
 *Objname* を省略し、 *oneresultset* の値が1の場合、現在のデータベースサイズ情報を提供するために、次の1つの結果セットが返されます。  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|**database_name**|**nvarchar(128)**|現在のデータベースの名前。|  
|**database_size**|**varchar (18)**|現在のデータベースのサイズ (MB 単位)。 **database_size** には、データファイルとログファイルの両方が含まれます。|  
|**未割り当て領域**|**varchar (18)**|データベースオブジェクト用に予約されていないデータベース内の領域。|  
|**確保**|**varchar (18)**|データベース内でオブジェクトによって割り当てられた領域の合計。|  
|**data**|**varchar (18)**|データの使用領域の合計。|  
|**index_size**|**varchar (18)**|インデックスによって使用されている領域の合計サイズ。|  
|**未使用**|**varchar (18)**|データベース内のオブジェクト用に予約されている領域の合計サイズ。ただし、まだ使用されていません。|  
  
 *Objname* を指定した場合は、指定したオブジェクトに対して次の結果セットが返されます。  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|**name**|**nvarchar(128)**|領域の使用情報を要求したオブジェクトの名前。<br /><br /> オブジェクトのスキーマ名は返されません。 スキーマ名が必要な場合は、 [sys.dm_db_partition_stats](../../relational-databases/system-dynamic-management-views/sys-dm-db-partition-stats-transact-sql.md) または [sys.dm_db_index_physical_stats](../../relational-databases/system-dynamic-management-views/sys-dm-db-index-physical-stats-transact-sql.md) 動的管理ビューを使用して、同等のサイズ情報を取得します。|  
|**rows**|**char (20)**|テーブルに含まれる行数。 指定されたオブジェクトが [!INCLUDE[ssSB](../../includes/sssb-md.md)] キューの場合、この列はキュー内のメッセージの数を示します。|  
|**確保**|**varchar (18)**|*Objname* の予約済み領域の合計サイズ。|  
|**data**|**varchar (18)**|*Objname* のデータによって使用されている領域の合計サイズ。|  
|**index_size**|**varchar (18)**|*Objname* のインデックスによって使用されている領域の合計サイズ。|  
|**未使用**|**varchar (18)**|*Objname* 用に予約されていますが、まだ使用されていない領域の合計。|  
 
これは、パラメーターが指定されていない場合の既定のモードです。 ディスク上のデータベースサイズ情報の詳細については、次の結果セットが返されます。 

|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|**database_name**|**nvarchar(128)**|現在のデータベースの名前。|  
|**database_size**|**varchar (18)**|現在のデータベースのサイズ (MB 単位)。 **database_size** には、データファイルとログファイルの両方が含まれます。 データベースに MEMORY_OPTIMIZED_DATA ファイルグループがある場合は、ファイルグループ内のすべてのチェックポイントファイルのディスク上の合計サイズが含まれます。|  
|**未割り当て領域**|**varchar (18)**|データベースオブジェクト用に予約されていないデータベース内の領域。 データベースに MEMORY_OPTIMIZED_DATA ファイルグループがある場合は、ファイルグループで状態が PRECREATED になっているチェックポイントファイルのディスク上の合計サイズが含まれます。|  

データベース内のテーブルによって使用される領域: (この resultset には、ディスク使用量のテーブルごとのアカウンティングがないため、メモリ最適化テーブルは反映されません) 

|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|**確保**|**varchar (18)**|データベース内でオブジェクトによって割り当てられた領域の合計。|  
|**data**|**varchar (18)**|データの使用領域の合計。|  
|**index_size**|**varchar (18)**|インデックスによって使用されている領域の合計サイズ。|  
|**未使用**|**varchar (18)**|データベース内のオブジェクト用に予約されている領域の合計サイズ。ただし、まだ使用されていません。|

次の結果セットが返されるのは、データベースに少なくとも1つのコンテナーを含む MEMORY_OPTIMIZED_DATA ファイルグループがある **場合のみ** です。 

|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|**xtp_precreated**|**varchar (18)**|状態が事前に作成されたチェックポイントファイルの合計サイズ (KB 単位)。 は、データベース全体の未割り当て領域にカウントされます。 たとえば、60万 KB の事前に作成されたチェックポイントファイルがある場合、この列には ' 60万 KB ' が含まれます。|  
|**xtp_used**|**varchar (18)**|構築中、アクティブ、およびマージターゲットの状態を持つチェックポイントファイルの合計サイズ (KB 単位)。 これは、メモリ最適化テーブルのデータに対してアクティブに使用されるディスク領域です。|  
|**xtp_pending_truncation**|**varchar (18)**|状態 WAITING_FOR_LOG_TRUNCATION のチェックポイントファイルの合計サイズ (KB 単位)。 これは、ログの切り捨てが行われると、クリーンアップを待機しているチェックポイントファイルに使用されるディスク領域です。|

*Objname* を省略した場合、oneresultset の値は1であり、 *include_total_xtp_storage* が1の場合は、現在のデータベースサイズ情報を提供するために、次の1つの結果セットが返されます。 `include_total_xtp_storage`が 0 (既定値) の場合、最後の3つの列は省略されます。 

|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|**database_name**|**nvarchar(128)**|現在のデータベースの名前。|  
|**database_size**|**varchar (18)**|現在のデータベースのサイズ (MB 単位)。 **database_size** には、データファイルとログファイルの両方が含まれます。 データベースに MEMORY_OPTIMIZED_DATA ファイルグループがある場合は、ファイルグループ内のすべてのチェックポイントファイルのディスク上の合計サイズが含まれます。|
|**未割り当て領域**|**varchar (18)**|データベースオブジェクト用に予約されていないデータベース内の領域。 データベースに MEMORY_OPTIMIZED_DATA ファイルグループがある場合は、ファイルグループで状態が PRECREATED になっているチェックポイントファイルのディスク上の合計サイズが含まれます。|  
|**確保**|**varchar (18)**|データベース内でオブジェクトによって割り当てられた領域の合計。|  
|**data**|**varchar (18)**|データの使用領域の合計。|  
|**index_size**|**varchar (18)**|インデックスによって使用されている領域の合計サイズ。|  
|**未使用**|**varchar (18)**|データベース内のオブジェクト用に予約されている領域の合計サイズ。ただし、まだ使用されていません。|
|**xtp_precreated**|**varchar (18)**|状態が事前に作成されたチェックポイントファイルの合計サイズ (KB 単位)。 これは、データベース全体の未割り当て領域にカウントされます。 データベースに少なくとも1つのコンテナーを含む memory_optimized_data ファイルグループがない場合は、NULL を返します。 *この列は、 @include_total_xtp_storage = 1 の場合にのみ含ま* れます。| 
|**xtp_used**|**varchar (18)**|構築中、アクティブ、およびマージターゲットの状態を持つチェックポイントファイルの合計サイズ (KB 単位)。 これは、メモリ最適化テーブルのデータに対してアクティブに使用されるディスク領域です。 データベースに少なくとも1つのコンテナーを含む memory_optimized_data ファイルグループがない場合は、NULL を返します。 *この列は、 @include_total_xtp_storage = 1 の場合にのみ含ま* れます。| 
|**xtp_pending_truncation**|**varchar (18)**|状態 WAITING_FOR_LOG_TRUNCATION のチェックポイントファイルの合計サイズ (KB 単位)。 これは、ログの切り捨てが行われると、クリーンアップを待機しているチェックポイントファイルに使用されるディスク領域です。 データベースに少なくとも1つのコンテナーを含む memory_optimized_data ファイルグループがない場合は、NULL を返します。 この列は、の場合にのみ含まれ `@include_total_xtp_storage=1` ます。|

## <a name="remarks"></a>解説  
 **database_size** は  +  ログファイルのサイズが含まれていますが、**予約済み** で **unallocated_space** データページのみを考慮しているため、通常は予約済みの **未割り当て領域** の合計より大きくなります。 Azure Synapse Analytics では、このステートメントが true でない場合があります。 
  
 XML インデックスとフルテキストインデックスによって使用されるページは、両方の結果セットの **index_size** に含まれています。 *Objname* を指定すると、オブジェクトの XML インデックスとフルテキストインデックスのページも、**予約** された結果と **index_size** の結果の合計にカウントされます。  
  
 空間インデックスを持つデータベースまたはオブジェクトに対して領域の使用量を計算する場合、 **database_size**、 **予約済み**、 **index_size** などの領域サイズの列には、空間インデックスのサイズが含まれます。  
  
 *Updateusage* が指定されている場合、は [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] データベース内のデータページをスキャンし、必要に応じて、各テーブルで使用されるストレージ領域に関するカタログビューを **sys.allocation_units** して修正し **ます。** たとえば、インデックスが削除された後、テーブルの領域情報が最新でない可能性があります。 *updateusage* は、大規模なテーブルまたはデータベースで実行されるまでに時間がかかることがあります。 *Updateusage* を使用するのは、返される値が正しくないと思われる場合と、プロセスがデータベース内の他のユーザーやプロセスに悪影響を与えない場合のみです。 DBCC UPDATEUSAGE は、別に実行することもできます。  
  
> [!NOTE]  
>  大きなインデックスを削除または再構築したり、大きなテーブルを削除したり切り捨てたりすると、では、 [!INCLUDE[ssDE](../../includes/ssde-md.md)] トランザクションがコミットされるまで、実際のページの割り当て解除とそれに関連付けられているロックが延期されます。 遅延削除操作では、割り当てられた領域はすぐに解放されません。 このため、大きなオブジェクトを削除または切り捨てた直後に **sp_spaceused** によって返された値は、実際に使用可能なディスク領域を反映していない可能性があります。  
  
## <a name="permissions"></a>権限  
 **sp_spaceused** の実行権限は、 **public** ロールに与えられています。 **\@updateusage** パラメーターを指定できるのは、**db_owner** 固定データベース ロールのメンバーだけです。  
  
## <a name="examples"></a>例  
  
### <a name="a-displaying-disk-space-information-about-a-table"></a>A. テーブルに関するディスク領域情報の表示  
 次の例では、テーブルとそのインデックスのディスク領域情報を報告し `Vendor` ます。  
  
```sql  
USE AdventureWorks2012;  
GO  
EXEC sp_spaceused N'Purchasing.Vendor';  
GO  
```  
  
### <a name="b-displaying-updated-space-information-about-a-database"></a>B. データベースに関する更新された領域情報の表示  
 次の例では、現在のデータベースで使用されている領域情報を要約し、省略可能なパラメーター `@updateusage` を使用して最新の値を取得します。  
  
```sql  
USE AdventureWorks008R2;  
GO  
EXEC sp_spaceused @updateusage = N'TRUE';  
GO  
```  
  
### <a name="c-displaying-space-usage-information-about-the-remote-table-associated-with-a-stretch-enabled-table"></a>C. Stretch が有効なテーブルに関連付けられているリモートテーブルに関する領域の使用状況に関する情報の表示  
 次の例では、 **\@ モード** 引数を使用してリモートターゲットを指定することにより、Stretch が有効なテーブルに関連付けられているリモートテーブルによって使用される領域の概要を示します。 詳細については、「 [Stretch Database](../../sql-server/stretch-database/stretch-database.md)」を参照してください。  
  
```sql  
USE StretchedAdventureWorks2016  
GO  
EXEC sp_spaceused N'Purchasing.Vendor', @mode = 'REMOTE_ONLY'  
```  
  
### <a name="d-displaying-space-usage-information-for-a-database-in-a-single-result-set"></a>D. 1つの結果セットでのデータベースの領域使用状況に関する情報の表示  
 次の例では、1つの結果セットにおける現在のデータベースの領域の使用状況を要約します。  
  
```sql  
USE AdventureWorks2016  
GO  
EXEC sp_spaceused @oneresultset = 1  
```  

### <a name="e-displaying-space-usage-information-for-a-database-with-at-least-one-memory_optimized-file-group-in-a-single-result-set"></a>E. 1つの結果セットに少なくとも1つの MEMORY_OPTIMIZED ファイルグループを含むデータベースの領域使用状況に関する情報を表示する 
 次の例では、1つの結果セットに少なくとも1つの MEMORY_OPTIMIZED ファイルグループを含む、現在のデータベースの領域の使用状況を要約します。
 
```sql
USE WideWorldImporters
GO
EXEC sp_spaceused @updateusage = 'FALSE', @mode = 'ALL', @oneresultset = '1', @include_total_xtp_storage = '1';
GO
``` 

### <a name="f-displaying-space-usage-information-for-a-memory_optimized-table-object-in-a-database"></a>F. データベース内の MEMORY_OPTIMIZED テーブルオブジェクトの領域使用状況に関する情報を表示します。
 次の例では、現在のデータベースにある MEMORY_OPTIMIZED table オブジェクトの領域の使用状況を、少なくとも1つの MEMORY_OPTIMIZED ファイルグループと共に要約しています。
 
```sql
USE WideWorldImporters
GO
EXEC sp_spaceused
@objname = N'VehicleTemparatures',
@updateusage = 'FALSE',
@mode = 'ALL',
@oneresultset = '0',
@include_total_xtp_storage = '1';
GO
```  

## <a name="see-also"></a>参照  
 [CREATE INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/create-index-transact-sql.md)   
 [CREATE TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/create-table-transact-sql.md)   
 [DBCC UPDATEUSAGE &#40;Transact-sql&#41;](../../t-sql/database-console-commands/dbcc-updateusage-transact-sql.md)   
 [SQL Server Service Broker (SQL Server Service Broker)](../../database-engine/configure-windows/sql-server-service-broker.md)   
 [sys.allocation_units &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-allocation-units-transact-sql.md)   
 [sys.indexes &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-indexes-transact-sql.md)   
 [sys.index_columns &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-index-columns-transact-sql.md)   
 [sys.objects &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-objects-transact-sql.md)   
 [sys.partitions &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-partitions-transact-sql.md)   
 [システム ストアド プロシージャ &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/system-stored-procedures-transact-sql.md)  
  
  
