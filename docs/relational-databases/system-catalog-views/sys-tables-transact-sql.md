---
description: sys. tables (Transact-sql)
title: sys. tables (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/22/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- tables_TSQL
- sys.tables_TSQL
- sys.tables
- tables
dev_langs:
- TSQL
helpviewer_keywords:
- sys.tables catalog view
ms.assetid: 8c42eba1-c19f-4045-ac82-b97a5e994090
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: ff1a4934ea85e20e0d8bb6fd82136418d9316df5
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104752712"
---
# <a name="systables-transact-sql"></a>sys. tables (Transact-sql)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  のユーザーテーブルごとに1行の値を返し [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|\<inherited columns>||このビューが継承する列の一覧については、「 [sys. objects &#40;transact-sql&#41;](../../relational-databases/system-catalog-views/sys-objects-transact-sql.md)」を参照してください。|  
|lob_data_space_id|**int**|0 以外の値は、このテーブルのラージ オブジェクト バイナリ (LOB) データを格納するデータ領域 (ファイル グループまたはパーティション構成) の ID です。 LOB データ型の例としては、 **varbinary (max)**、 **varchar (max)**、 **geography**、 **xml** などがあります。<br /><br /> 0 = テーブルは LOB データではありません。|  
|filestream_data_space_id|**int**|FILESTREAM ファイル グループまたは FILESTREAM ファイル グループから成るパーティション構成のデータ領域 ID です。<br /><br /> FILESTREAM ファイルグループの名前を報告するには、クエリを実行し `SELECT FILEGROUP_NAME (filestream_data_space_id) FROM sys.tables` ます。<br /><br /> sys.tables は、filestream_data_space_id = data_space_id で次のビューに結合できます。<br /><br /> -sys. ファイルグループ<br /><br /> -sys.partition_schemes<br /><br /> -sys. インデックス<br /><br /> -sys.allocation_units<br /><br /> -sys.fulltext_catalogs<br /><br /> -sys.data_spaces<br /><br /> -sys.destination_data_spaces<br /><br /> -sys.master_files<br /><br /> -sys.database_files<br /><br /> -backupfilegroup (filegroup_id での結合)|  
|max_column_id_used|**int**|このテーブルで使用される最大列 ID。|  
|lock_on_bulk_load|**bit**|テーブルは一括読み込みでロックされています。 詳細については、「[sp_tableoption &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-tableoption-transact-sql.md)」を参照してください。|  
|uses_ansi_nulls|**bit**|テーブルは、SET ANSI_NULLS データベース オプションが ON の場合に作成されます。|  
|is_replicated|**bit**|1 = テーブルは、スナップショットレプリケーションまたはトランザクションレプリケーションを使用してパブリッシュされます。|  
|has_replication_filter|**bit**|1 = テーブルにはレプリケーションフィルターがあります。|  
|is_merge_published|**bit**|1 = テーブルは、マージ レプリケーションを使用してパブリッシュされます。|  
|is_sync_tran_subscribed|**bit**|1 = テーブルは即時更新サブスクリプションを使用してサブスクライブされます。|  
|has_unchecked_assembly_data|**bit**|1 = テーブルには、最後の ALTER ASSEMBLY 中に定義が変更されたアセンブリに依存する永続化されたデータが含まれています。 次の DBCC CHECKDB または DBCC CHECKTABLE が正常に完了した後、は0にリセットされます。|  
|text_in_row_limit|**int**|text in row で許可される最大バイト数です。<br /><br /> 0 = Text in row オプションは設定されていません。 詳細については、「[sp_tableoption &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-tableoption-transact-sql.md)」を参照してください。|  
|large_value_types_out_of_row|**bit**|1 = 大きな値の型は、行外に格納されます。 詳細については、「[sp_tableoption &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-tableoption-transact-sql.md)」を参照してください。|  
|is_tracked_by_cdc|**bit**|1 = テーブルで変更データ キャプチャが有効になっています。 詳細については、「 [sys.sp_cdc_enable_table &#40;transact-sql&#41;](../../relational-databases/system-stored-procedures/sys-sp-cdc-enable-table-transact-sql.md)」を参照してください。|  
|lock_escalation|**tinyint**|テーブルの LOCK_ESCALATION オプションの値。<br /><br /> 0 = TABLE<br /><br /> 1 = 無効<br /><br /> 2 = 自動|  
|lock_escalation_desc|**nvarchar(60)**|テーブルの lock_escalation オプションについての説明テキストです。 有効値は、TABLE、AUTO、および DISABLE です。|  
|is_filetable|**bit**|**適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降と [!INCLUDE[sssdsfull](../../includes/sssdsfull-md.md)]。<br /><br /> 1 = テーブルは FileTable です。<br /><br /> FileTables について詳しくは、「[FileTables &#40;SQL Server&#41;](../../relational-databases/blob/filetables-sql-server.md)」をご覧ください。|  
|durability|**tinyint**|**適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降と [!INCLUDE[sssdsfull](../../includes/sssdsfull-md.md)]。<br /><br /> 返される値は次のとおりです。<br /><br /> 0 = SCHEMA_AND_DATA<br /><br /> 1 = SCHEMA_ONLY<br /><br /> 既定値は0です。|  
|durability_desc|**nvarchar(60)**|**適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降と [!INCLUDE[sssdsfull](../../includes/sssdsfull-md.md)]。<br /><br /> 使用できる値を次に示します。<br /><br /> SCHEMA_ONLY<br /><br /> SCHEMA_AND_DATA<br /><br /> SCHEMA_AND_DATA の値は、テーブルが持続性のあるインメモリテーブルであることを示します。 メモリ最適化テーブルの既定値は SCHEMA_AND_DATA です。 値 SCHEMA_ONLY は、メモリ最適化オブジェクトでは、データベースを再起動した場合にテーブル データの更新内容が保存されないことを示します。|  
|is_memory_optimized|**bit**|**適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降と [!INCLUDE[sssdsfull](../../includes/sssdsfull-md.md)]。<br /><br /> 使用できる値を次に示します。<br /><br /> 0 = メモリ最適化ではありません。<br /><br /> 1 = メモリ最適化です。<br /><br /> 値 0 が既定の値です。<br /><br /> メモリ最適化テーブルは、メモリ内のユーザーテーブルです。スキーマは、他のユーザーテーブルと同様にディスクに保存されます。 メモリ最適化テーブルには、ネイティブコンパイルストアドプロシージャからアクセスできます。|  
|temporal_type|**tinyint**|**適用対象**: [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 以降と [!INCLUDE[sssdsfull](../../includes/sssdsfull-md.md)]。<br /><br /> テーブルの種類を表す数値。<br /><br /> 0 = NON_TEMPORAL_TABLE<br /><br /> 1 = HISTORY_TABLE<br /><br /> 2 = SYSTEM_VERSIONED_TEMPORAL_TABLE|  
|temporal_type_desc|**nvarchar(60)**|**適用対象**: [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 以降と [!INCLUDE[sssdsfull](../../includes/sssdsfull-md.md)]。<br /><br /> テーブルの種類の説明テキスト。<br /><br /> NON_TEMPORAL_TABLE<br /><br /> HISTORY_TABLE<br /><br /> SYSTEM_VERSIONED_TEMPORAL_TABLE|  
|history_table_id|**int**|**適用対象**: [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 以降と [!INCLUDE[sssdsfull](../../includes/sssdsfull-md.md)]。<br /><br /> (2, 4) で temporal_type ときに、履歴データを保持するテーブルの object_id を返します。それ以外の場合は、NULL を返します。|  
|is_remote_data_archive_enabled|**bit**|**適用対象**: [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 以降および [!INCLUDE[sssdsfull](../../includes/sssdsfull-md.md)]<br /><br /> テーブルで Stretch が有効になっているかどうかを示します。<br /><br /> 0 = テーブルは Stretch が有効になっていません。<br /><br /> 1 = テーブルは Stretch が有効です。<br /><br /> 詳細については、「 [Stretch Database](../../sql-server/stretch-database/stretch-database.md)」を参照してください。|  
|is_external|**bit**|**適用対象**: [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 以降、、 [!INCLUDE[sssdsfull](../../includes/sssdsfull-md.md)] および [!INCLUDE[sssdwfull](../../includes/sssdwfull-md.md)] 。<br /><br /> テーブルが外部テーブルであることを示します。<br /><br /> 0 = テーブルは、外部テーブルではありません。<br /><br /> 1 = テーブルは外部テーブルです。| 
|history_retention_period|**int**|**適用対象**: [!INCLUDE[sssdsfull](../../includes/sssdsfull-md.md)] <br/><br/>History_retention_period_unit で指定された単位で表した、テンポラル履歴の保有期間を表す数値。 |  
|history_retention_period_unit|**int**|**適用対象**: [!INCLUDE[sssdsfull](../../includes/sssdsfull-md.md)] <br/><br/>テンポラル履歴の保有期間の単位の種類を表す数値。 <br /><br />-1: 無制限 <br /><br />3: 日 <br /><br />4: 週 <br /><br />5: 月 <br /><br />6: 年 |  
|history_retention_period_unit_desc|**nvarchar (10)**|**適用対象**: [!INCLUDE[sssdsfull](../../includes/sssdsfull-md.md)] <br/><br/>テンポラル履歴保有期間の単位の種類の説明テキスト。 <br /><br />容量 <br /><br />DAY <br /><br />[WEEK] <br /><br />MONTH <br /><br />YEAR |  
|is_node|**bit**|**適用対象**: [!INCLUDE[sssql17-md.md](../../includes/sssql17-md.md)] および [!INCLUDE[sssdsfull](../../includes/sssdsfull-md.md)]。 <br/><br/>1 = これはグラフノードテーブルです。 <br /><br />0 = これはグラフノードテーブルではありません。 |  
|is_edge|**bit**|**適用対象**: [!INCLUDE[sssql17-md.md](../../includes/sssql17-md.md)] および [!INCLUDE[sssdsfull](../../includes/sssdsfull-md.md)]。 <br/><br/>1 = これはグラフのエッジテーブルです。 <br /><br />0 = これは、グラフのエッジテーブルではありません。 |  

## <a name="permissions"></a>権限  
 [!INCLUDE[ssCatViewPerm](../../includes/sscatviewperm-md.md)] 詳細については、「 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)」を参照してください。  
  
## <a name="examples"></a>例  
 次の例は、主キーを持たないすべてのユーザー テーブルを返します。  
  
```  
SELECT SCHEMA_NAME(schema_id) AS schema_name  
    ,name AS table_name   
FROM sys.tables   
WHERE OBJECTPROPERTY(object_id,'TableHasPrimaryKey') = 0  
ORDER BY schema_name, table_name;  
GO  
  
```  
  
次の例は、関連するテンポラルデータを公開する方法を示しています。  
   
**適用対象**: [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 以降と [!INCLUDE[sssdsfull](../../includes/sssdsfull-md.md)]。
  
```  
SELECT T1.object_id, T1.name as TemporalTableName, SCHEMA_NAME(T1.schema_id) AS TemporalTableSchema,  
T2.name as HistoryTableName, SCHEMA_NAME(T2.schema_id) AS HistoryTableSchema,  
T1.temporal_type_desc  
FROM sys.tables T1  
LEFT JOIN sys.tables T2   
ON T1.history_table_id = T2.object_id  
ORDER BY T1.temporal_type desc  
```  

次の例は、テンポラル履歴のリテンション期間に関する情報を公開する方法を示しています。  

**適用対象**: [!INCLUDE[sssdsfull](../../includes/sssdsfull-md.md)]  
  
```  
SELECT DB.is_temporal_history_retention_enabled, SCHEMA_NAME(T1.schema_id) AS TemporalTableSchema, 
T1.name as TemporalTableName, SCHEMA_NAME(T2.schema_id) AS HistoryTableSchema, T2.name as HistoryTableName,
T1.history_retention_period, T1.history_retention_period_unit_desc
FROM sys.tables T1  
OUTER APPLY (select is_temporal_history_retention_enabled from sys.databases where name = DB_NAME()) DB
LEFT JOIN sys.tables T2   
ON T1.history_table_id = T2.object_id WHERE T1.temporal_type = 2 
```  
  
## <a name="see-also"></a>参照  
 [オブジェクト カタログ ビュー &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/object-catalog-views-transact-sql.md)   
 [カタログ ビュー &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/catalog-views-transact-sql.md)   
 [DBCC CHECKDB &#40;Transact-sql&#41;](../../t-sql/database-console-commands/dbcc-checkdb-transact-sql.md)   
 [DBCC CHECKTABLE &#40;Transact-sql&#41;](../../t-sql/database-console-commands/dbcc-checktable-transact-sql.md)   
 [SQL Server システムカタログに対するクエリについてよく寄せられる質問](../../relational-databases/system-catalog-views/querying-the-sql-server-system-catalog-faq.yml)   
 [インメモリ OLTP &#40;インメモリ最適化&#41;](../../relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization.md)  
  
  
