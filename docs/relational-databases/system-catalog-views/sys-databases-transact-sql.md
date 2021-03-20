---
description: sys.databases (Transact-SQL)
title: sys. databases (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/08/2020
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- databases
- databases_TSQL
- sys.databases
- sys.databases_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.databases catalog view
ms.assetid: 46c288c1-3410-4d68-a027-3bbf33239289
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: e3b9701423440c97c1728223dd6d4df68b164459
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104750052"
---
# <a name="sysdatabases-transact-sql"></a>sys.databases (Transact-SQL)

[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]のインスタンスに、データベースごとに 1 行のデータを保持します。  
  
データベースがでない場合 `ONLINE` 、また `AUTO_CLOSE` はに設定され `ON` ていて、データベースが閉じている場合は、一部の列の値がになることがあり `NULL` ます。 データベースがの場合 `OFFLINE` 、対応する行は低い特権のユーザーには表示されません。 データベースがである場合に対応する行を表示するには、少なくとも `OFFLINE` `ALTER ANY DATABASE` サーバーレベルの権限、または `CREATE DATABASE` データベース内の権限を持っている必要があり `master` ます。  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|**name**|**sysname**|のインスタンス内 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] またはサーバー内で一意のデータベースの名前 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 。|  
|**database_id**|**int**|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンス内、または [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] サーバー内で一意な、データベースの識別子。|  
|**source_database_id**|**int**|NULL 以外 = このデータベース スナップショットのソース データベースの ID です。<br /> NULL = データベース スナップショットではありません。|  
|**owner_sid**|**varbinary(85)**|サーバーに登録したデータベースの外部所有者の SID (セキュリティ識別子) です。 データベースを所有できるユーザーの詳細については、「alter [authorization](../../t-sql/statements/alter-authorization-transact-sql.md)」の「 **alter authorization for databases** 」セクションを参照してください。|  
|**create_date**|**datetime**|データベースの作成または名前の変更を行った日付です。 **Tempdb** の場合は、サーバーが再起動されるたびにこの値が変更されます。|  
|**compatibility_level**|**tinyint**|動作に互換性のある [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のバージョンに対応する整数です。<br /><br /><table border="0"><tr><td>**Value**</td><td>**適用対象**</td></tr><tr><td>70</td><td>[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 7.0 ~ [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)]</td></tr><tr><td>80</td><td>[!INCLUDE[ssVersion2000](../../includes/ssversion2000-md.md)] 行い [!INCLUDE[ssKilimanjaro](../../includes/sskilimanjaro-md.md)]</td></tr><tr><td>90</td><td>[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 行い [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)]</td></tr><tr><td>100</td><td>[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (開始値 [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)]) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]</td></tr><tr><td>110</td><td>[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (開始値 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)]) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]</td></tr><tr><td>120</td><td>[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (開始値 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)]) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]</td></tr><tr><td>130</td><td>[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (開始値 [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)]) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]</td></tr><tr><td>140</td><td>[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (開始値 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)]) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]</td></tr><tr><td>150</td><td>[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (開始値 [!INCLUDE[sql-server-2019](../../includes/sssql19-md.md)]) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]</td></tr></table>|  
|**collation_name**|**sysname**|データベースの照合順序です。 データベースの既定の照合順序として機能します。<br /> NULL = データベースがオンラインでないか、AUTO_CLOSE が ON に設定されていて、データベースが閉じています。|  
|**user_access**|**tinyint**|ユーザー アクセス設定です。<br /> 0 = MULTI_USER が指定されています。<br /> 1 = SINGLE_USER が指定されています。<br /> 2 = RESTRICTED_USER が指定されています。|  
|**user_access_desc**|**nvarchar(60)**|ユーザー アクセス設定の説明です。|  
|**is_read_only**|**bit**|1 = データベースは READ_ONLY です。<br /> 0 = データベースは READ_WRITE です。|  
|**is_auto_close_on**|**bit**|1 = AUTO_CLOSE は ON です。<br /> 0 = AUTO_CLOSE は OFF です。|  
|**is_auto_shrink_on**|**bit**|1 = AUTO_SHRINK は ON です。<br /> 0 = AUTO_SHRINK は OFF です。|  
|**状態**|**tinyint**|**Value**<br /> 0 = ONLINE <br /> 1 = 復元中 <br /> 2 = 回復 <sup>1</sup><br /> 3 = RECOVERY_PENDING <sup>1</sup><br /> 4 = 問題あり <br /> 5 = 緊急 <sup>1</sup><br /> 6 = オフライン <sup>1</sup><br /> 7 = コピー <sup>2</sup> <br /> 10 = OFFLINE_SECONDARY <sup>2</sup> <br /><br /> **注:** Always On データベースの場合は、 `database_state` sys.dm_hadr_database_replica_states の列または列に対してクエリを実行 `database_state_desc` します。 [](../../relational-databases/system-dynamic-management-views/sys-dm-hadr-database-replica-states-transact-sql.md)<br /><br /><sup>1</sup> **に適用さ** れます: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (以降 [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] ) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]<br /><sup>2</sup> **に適用さ** れます。 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)][!INCLUDE[ssGeoDR](../../includes/ssgeodr-md.md)]|  
|**state_desc**|**nvarchar(60)**|データベースの状態の説明。 「状態」を参照してください。|  
|**is_in_standby**|**bit**|データベースは、復元ログに対し、読み取り専用です。|  
|**is_cleanly_shutdown**|**bit**|1 = データベースはクリーンにシャットダウンされ、起動時に復旧処理は必要ありません。<br /> 0 = データベースはクリーンにシャットダウンされなかったため、起動時に復旧処理が必要です。|  
|**is_supplemental_logging_enabled**|**bit**|1 = SUPPLEMENTAL_LOGGING は ON です。<br /> 0 = SUPPLEMENTAL_LOGGING は OFF です。|  
|**snapshot_isolation_state**|**tinyint**|ALLOW_SNAPSHOT_ISOLATION オプションによって設定された、許可されているスナップショット分離トランザクションの状態。<br /> 0 = スナップショット分離の状態は OFF です (既定)。 スナップショット分離は許可されていません。<br /> 1 = スナップショット分離の状態は ON です。 スナップショット分離は許可されています。<br /> 2 = スナップショット分離状態はオフ状態に遷移中です。 すべてのトランザクションで、その変更がバージョン管理されます。 スナップショット分離を使用して新しいトランザクションを開始することはできません。 ALTER DATABASE が実行されたときにアクティブだったすべてのトランザクションが完了するまで、データベースは OFF に移行中の状態となります。<br /> 3 = スナップショット分離の状態はオン状態に遷移中です。 新しいトランザクションでは、変更がバージョン管理されます。 スナップショット分離の状態が 1 (ON) になるまで、トランザクションでスナップショット分離を使用することはできません。 ALTER DATABASE が実行されたときにアクティブだったすべての更新トランザクションが完了するまで、データベースは状態に遷移したままになります。|  
|**snapshot_isolation_state_desc**|**nvarchar(60)**|ALLOW_SNAPSHOT_ISOLATION オプションによって設定された、許可されているスナップショット分離トランザクションの状態の説明。|  
|**is_read_committed_snapshot_on**|**bit**|1 = READ_COMMITTED_SNAPSHOT オプションは ON です。 READ COMMITTED 分離レベルでの読み取り操作は、スナップショット スキャンに基づいており、ロックを取得しません。<br /> 0 = READ_COMMITTED_SNAPSHOT オプションは OFF です (既定)。 Read committed 分離レベルでの読み取り操作では、共有ロックが使用されます。|  
|**recovery_model**|**tinyint**|選択される復旧モデルです。<br /> 1 = FULL<br /> 2 = BULK_LOGGED<br /> 3 = SIMPLE|  
|**recovery_model_desc**|**nvarchar(60)**|選択された復旧モデルの説明です。|  
|**page_verify_option**|**tinyint**|PAGE_VERIFY オプションの設定です。<br /> 0 = NONE<br /> 1 = TORN_PAGE_DETECTION<br /> 2 = CHECKSUM|  
|**page_verify_option_desc**|**nvarchar(60)**|PAGE_VERIFY オプション設定の説明です。|  
|**is_auto_create_stats_on**|**bit**|1 = AUTO_CREATE_STATISTICS は ON です。<br /> 0 = AUTO_CREATE_STATISTICS は OFF です。|  
|**is_auto_create_stats_incremental_on**|**bit**|自動統計の増分オプションの既定の設定を示します。<br /> 0 = 自動作成の統計は非増分です。<br /> 1 = 可能な場合は、自動作成の統計情報は増分されます。<br /> **適用対象**: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降)。|  
|**is_auto_update_stats_on**|**bit**|1 = AUTO_UPDATE_STATISTICS は ON です。<br /> 0 = AUTO_UPDATE_STATISTICS は OFF です。|  
|**is_auto_update_stats_async_on**|**bit**|1 = AUTO_UPDATE_STATISTICS_ASYNC は ON です。<br /> 0 = AUTO_UPDATE_STATISTICS_ASYNC は OFF です。|  
|**is_ansi_null_default_on**|**bit**|1 = ANSI_NULL_DEFAULT は ON です。<br /> 0 = ANSI_NULL_DEFAULT は OFF です。|  
|**is_ansi_nulls_on**|**bit**|1 = ANSI_NULLS は ON です。<br /> 0 = ANSI_NULLS は OFF です。|  
|**is_ansi_padding_on**|**bit**|1 = ANSI_PADDING は ON です。<br /> 0 = ANSI_PADDING は OFF です。|  
|**is_ansi_warnings_on**|**bit**|1 = ANSI_WARNINGS は ON です。<br /> 0 = ANSI_WARNINGS は OFF です。|  
|**is_arithabort_on**|**bit**|1 = ARITHABORT は ON です。<br /> 0 = ARITHABORT は OFF です。|  
|**is_concat_null_yields_null_on**|**bit**|1 = CONCAT_NULL_YIELDS_NULL は ON です。<br /> 0 = CONCAT_NULL_YIELDS_NULL は OFF です。|  
|**is_numeric_roundabort_on**|**bit**|1 = NUMERIC_ROUNDABORT は ON です。<br /> 0 = NUMERIC_ROUNDABORT は OFF です。|  
|**is_quoted_identifier_on**|**bit**|1 = QUOTED_IDENTIFIER は ON です。<br /> 0 = QUOTED_IDENTIFIER は OFF です。|  
|**is_recursive_triggers_on**|**bit**|1 = RECURSIVE_TRIGGERS は ON です。<br /> 0 = RECURSIVE_TRIGGERS は OFF です。|  
|**is_cursor_close_on_commit_on**|**bit**|1 = CURSOR_CLOSE_ON_COMMIT は ON です。<br /> 0 = CURSOR_CLOSE_ON_COMMIT は OFF です。|  
|**is_local_cursor_default**|**bit**|1 = CURSOR_DEFAULT はローカルです。<br /> 0 = CURSOR_DEFAULT はグローバルです。|  
|**is_fulltext_enabled**|**bit**|1 = データベースに対してフルテキストが有効です。<br /> 0 = データベースに対してフルテキストが無効です。|  
|**is_trustworthy_on**|**bit**|1 = データベースは信頼できるものとしてマークされています。<br /> 0 = データベースは信頼できるものとしてマークされていません。<br /> 既定では、復元またはアタッチされたデータベースの信頼が有効になっていません。|  
|**is_db_chaining_on**|**bit**|1 = 複数データベースの組み合わせ所有権は ON です。<br /> 0 = 複数データベースの組み合わせ所有権は OFF です。|  
|**is_parameterization_forced**|**bit**|1 = パラメーター化は FORCED です。<br /> 0 = パラメーター化は SIMPLE です。|  
|**is_master_key_encrypted_by_server**|**bit**|1 = データベースは暗号化されたマスター キーを保有しています。<br /> 0 = データベースは暗号化されたマスター キーを保有していません。|  
|**is_query_store_on**|**bit**|1 = このデータベースに対してクエリストアが有効になっています。 [Sys.database_query_store_options](../../relational-databases/system-catalog-views/sys-database-query-store-options-transact-sql.md)をオンにして、クエリのストアの状態を表示します。<br /> 0 = クエリストアが有効になっていません<br /> **適用対象**: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 以降)。|  
|**is_published**|**bit**|1 = データベースは、トランザクション レプリケーション トポロジまたはスナップショット レプリケーション トポロジにおけるパブリケーション データベースです。<br /> 0 = パブリケーション データベースではありません。|  
|**is_subscribed**|**bit**|この列は使用されません。 データベースのサブスクライバーの状態に関係なく、常に 0 を返します。|  
|**is_merge_published**|**bit**|1 = データベースは、マージ レプリケーション トポロジにおけるパブリケーション データベースです。<br /> 0 = マージ レプリケーション トポロジにおけるパブリケーション データベースではありません。|  
|**is_distributor**|**bit**|1 = データベースは、レプリケーション トポロジにおけるディストリビューション データベースです。<br /> 0 = レプリケーション トポロジにおけるディストリビューション データベースではありません。|  
|**is_sync_with_backup**|**bit**|1 = データベースはバックアップとのレプリケーション同期用に設定されています。<br /> 0 = バックアップとのレプリケーション同期用に設定されていません。|  
|**service_broker_guid**|**uniqueidentifier**|このデータベースの Service Broker の識別子です。 ルーティング テーブルでターゲットの **broker_instance** として使用されます。|  
|**is_broker_enabled**|**bit**|1 = このデータベースのブローカーは現在メッセージを送受信中です。<br /> 0 = このデータベースでは、すべての送信メッセージは転送キューにとどまり、受信メッセージはキューに配置されません。<br /> 既定では、復元されたデータベースまたはアタッチされたデータベースでは、ブローカーは無効になります。 ただし、フェールオーバー後にブローカーが有効になるデータベース ミラーリングは例外です。|  
|**log_reuse_wait**|**tinyint**|トランザクションログ領域の再利用は、現在、最後のチェックポイントの時点で、次のいずれかを待機しています。 これらの値の詳細については、 [トランザクションログ](../../relational-databases/logs/the-transaction-log-sql-server.md)を参照してください。<br /> **Value**<br /> 0 = なし<br /> 1 = チェックポイント (データベースが復旧モデルを使用していて、メモリ最適化データファイルグループがある場合、列がまたはであることを確認する必要があります `log_reuse_wait` `checkpoint` `xtp_checkpoint` ) <sup>1</sup><br /> 2 = ログバックアップ <sup>1</sup><br /> 3 = アクティブなバックアップまたは復元 <sup>1</sup><br /> 4 = アクティブなトランザクション <sup>1</sup><br /> 5 = データベースミラーリング <sup>1</sup><br /> 6 = レプリケーション <sup>1</sup><br /> 7 = データベーススナップショットの作成 <sup>1</sup><br /> 8 = ログスキャン <br /> 9 = Always On 可用性グループセカンダリレプリカは、このデータベースのトランザクションログレコードを対応するセカンダリデータベースに適用します。 <sup>2</sup><br /> 9 = その他 (一時的) <sup>3</sup><br /> 10 = 内部使用のみ <sup>2</sup><br /> 11 = 内部使用のみ <sup>2</sup><br /> 12 = 内部使用のみ <sup>2</sup><br /> 13 = 最も古いページ <sup>2</sup><br /> 14 = その他 <sup>2</sup><br />  16 = XTP_CHECKPOINT (データベースが復旧モデルを使用していて、メモリ最適化データファイルグループがある場合、列がまたはであることを確認する必要があります `log_reuse_wait` `checkpoint` `xtp_checkpoint` ) <sup>4</sup><br /><br /><sup>1</sup> **に適用さ** れます: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (以降 [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] )<br /><sup>2</sup> **に適用さ** れます: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (以降 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] )<br /><sup>3</sup> **に適用さ** れます: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (およびを含む [!INCLUDE[ssKilimanjaro](../../includes/ssKilimanjaro-md.md)] )<br /><sup>4</sup> **に適用さ** れます: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (以降 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] )|  
|**log_reuse_wait_desc**|**nvarchar(60)**|前回のチェックポイントの時点で現在待機中の、トランザクション ログ領域の再利用の理由の説明です。|  
|**is_date_correlation_on**|**bit**|1 = DATE_CORRELATION_OPTIMIZATION は ON です。<br /> 0 = DATE_CORRELATION_OPTIMIZATION は OFF です。|  
|**is_cdc_enabled**|**bit**|1 = データベースで変更データ キャプチャが有効になっています。 詳細については、「 [sys.sp_cdc_enable_db &#40;transact-sql&#41;](../../relational-databases/system-stored-procedures/sys-sp-cdc-enable-db-transact-sql.md)」を参照してください。|  
|**is_encrypted**|**bit**|データベースが暗号化されているかどうかを示します (は、句を使用して最後に設定された状態を反映し `ALTER DATABASE SET ENCRYPTION` ます)。 値は、次のいずれかです。<br /> 1 = 暗号化<br /> 0 = 暗号化されていない<br /> データベース暗号化の詳細については、「[Transparent Data Encryption &#40;TDE&#41;](../../relational-databases/security/encryption/transparent-data-encryption.md)」を参照してください。<br /> データベースの暗号化が解除されている場合、には `is_encrypted` 値0が表示されます。 [動的管理ビューの [sys.dm_database_encryption_keys](../../relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql.md) ] を使用すると、暗号化プロセスの状態を確認できます。|  
|**is_honor_broker_priority_on**|**bit**|データベースがメッセージ交換の優先度を優先するかどうかを示します (句を使用して最後に設定された状態を反映し `ALTER DATABASE SET HONOR_BROKER_PRIORITY` ます)。 値は、次のいずれかです。<br /> 1 = HONOR_BROKER_PRIORITY は ON です。<br /> 0 = HONOR_BROKER_PRIORITY は OFF です。<br /> 既定では、復元またはアタッチされたデータベースの broker の優先度はオフになっています。|  
|**replica_id**|**uniqueidentifier**|データベースが参加している可用性グループ (存在する場合) のローカル [!INCLUDE[ssHADR](../../includes/sshadr-md.md)] 可用性レプリカの一意の識別子です。<br /> NULL = データベースは可用性グループの可用性レプリカの一部ではありません。<br /> **適用対象**: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降) と [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]|  
|**group_database_id**|**uniqueidentifier**|データベースが参加している Always On 可用性グループ (存在する場合) 内のデータベースの一意識別子。 **group_database_id** は、プライマリレプリカのこのデータベースと、データベースが可用性グループに参加しているすべてのセカンダリレプリカで同じです。<br /> NULL = データベースは、どの可用性グループの可用性レプリカの一部でもありません。<br /> **適用対象**: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]|  
|**resource_pool_id**|**int**|このデータベースにマップされているリソースプールの id。 このリソースプールは、このデータベース内のメモリ最適化テーブルで使用できるメモリの合計を制御します。<br /> **適用対象**: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (以降 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] )|  
|**default_language_lcid**|**smallint**|包含データベースの既定の言語のローカル ID (LCID) を示します。<br /> **注:** の [default Language サーバー構成オプションの構成](../../database-engine/configure-windows/configure-the-default-language-server-configuration-option.md) として機能し `sp_configure` ます。 非包含データベースの場合、この値は **null** です。<br /> **適用対象**: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]|  
|**default_language_name**|**nvarchar(128)**|包含データベースの既定の言語を示します。<br /> 非包含データベースの場合、この値は **null** です。<br /> **適用対象**: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降) と [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]|  
|**default_fulltext_language_lcid**|**int**|包含データベースの既定のフルテキスト言語のロケール id (lcid) を示します。<br /> **注:** 既定の [フルテキスト言語サーバー構成オプション](../../database-engine/configure-windows/configure-the-default-full-text-language-server-configuration-option.md) の既定の構成として機能 `sp_configure` します。 非包含データベースの場合、この値は **null** です。<br /> **適用対象**: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]|  
|**default_fulltext_language_name**|**nvarchar(128)**|包含データベースの既定のフルテキスト言語を示します。<br /> 非包含データベースの場合、この値は **null** です。<br /> **適用対象**: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]|  
|**is_nested_triggers_on**|**bit**|包含データベースで入れ子になったトリガーが許可されるかどうかを示します。<br /> 0 = 入れ子になったトリガーは許可されません。<br /> 1 = 入れ子になったトリガーは許可されます。<br /> **注:** の関数は、の [nested Triggers サーバー構成オプションを構成](../../database-engine/configure-windows/configure-the-nested-triggers-server-configuration-option.md) し `sp_configure` ます。 非包含データベースの場合、この値は **null** です。 詳細については、「 [ transact-sql&#41;&#40;sys.configurations ](../../relational-databases/system-catalog-views/sys-configurations-transact-sql.md) を参照してください。<br /> **適用対象**: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]|  
|**is_transform_noise_words_on**|**bit**|包含データベースでノイズ ワードを変換する必要があるかどうかを示します。<br /> 0 = ノイズ ワードは変換する必要がありません。<br /> 1 = ノイズ ワードは変換する必要があります。<br /> **注:** の [変換ノイズワードのサーバー構成オプション](../../database-engine/configure-windows/transform-noise-words-server-configuration-option.md) として機能し `sp_configure` ます。 非包含データベースの場合、この値は **null** です。 詳細については、「 [ transact-sql&#41;&#40;sys.configurations ](../../relational-databases/system-catalog-views/sys-configurations-transact-sql.md) を参照してください。<br /> **適用対象**: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (以降 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] )|  
|**two_digit_year_cutoff**|**smallint**|2 桁の数字を 4 桁の西暦として解釈する場合の区切りの年を表す 1753 ～ 9999 の範囲の数値を示します。<br /> **注:** の [2 桁表記の年の切り捨てサーバー構成オプションの構成](../../database-engine/configure-windows/configure-the-two-digit-year-cutoff-server-configuration-option.md) として機能し `sp_configure` ます。 非包含データベースの場合、この値は **null** です。 詳細については、「 [ transact-sql&#41;&#40;sys.configurations ](../../relational-databases/system-catalog-views/sys-configurations-transact-sql.md) を参照してください。<br /> **適用対象**: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]|  
|**containment**|**tinyint not null**|データベースの包含状態を示します。<br />  0 = データベースの包含がオフです。 **適用対象**: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]<br /> 1 = データベースは部分的な含有に **適用さ** れます: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (以降 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] )|  
|**containment_desc**|**nvarchar (60) not null**|データベースの包含状態を示します。<br /> NONE = 従来のデータベース (包含なし)<br /> PARTIAL = 部分的包含データベース<br /> **適用対象**: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降) と [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]|  
|**target_recovery_time_in_seconds**|**int**|データベースの推定復旧時間 (秒) です。 NULL 値は許可されます。<br /> **適用対象**: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]|  
|**delayed_durability**|**int**|遅延持続性の設定:<br /> 0 = 無効<br /> 1 = 許可<br /> 2 = 強制<br /> 詳しくは、「[トランザクションの持続性の制御](../../relational-databases/logs/control-transaction-durability.md)」をご覧ください。<br /> **適用対象**: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。|  
|**delayed_durability_desc**|**nvarchar(60)**|遅延持続性の設定:<br /> DISABLED<br /> ALLOWED<br /> FORCED<br /> **適用対象**: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。|  
|**is_memory_optimized_elevate_to_snapshot_on**|**bit**|セッション設定 TRANSACTION ISOLATION LEVEL が低い分離レベル (READ COMMITTED または READ UNCOMMITTED) に設定されている場合は、SNAPSHOT 分離を使用してメモリ最適化テーブルにアクセスします。<br /> 1 = 最小分離レベルは SNAPSHOT です。<br /> 0 = 分離レベルは昇格されません。|  
|**is_federation_member**|**bit**|データベースがフェデレーションのメンバーであるかどうかを示します。<br /> **適用対象**: [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]|  
|**is_remote_data_archive_enabled**|**bit**|データベースが拡張されているかどうかを示します。<br /> 0 = データベースは Stretch 対応ではありません。<br /> 1 = データベースは Stretch に対応しています。<br /> **適用対象**: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (以降 [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] )<br /> 詳細については、「[Stretch Database](../../sql-server/stretch-database/stretch-database.md)」を参照してください。|  
|**is_mixed_page_allocation_on**|**bit**|データベース内のテーブルとインデックスが混合エクステントから初期ページを割り当てることができるかどうかを示します。<br /> 0 = データベース内のテーブルとインデックスは、常に最初のページを一様なエクステントから割り当てます。<br /> 1 = データベース内のテーブルとインデックスは、混合エクステントから初期ページを割り当てることができます。<br /> 詳細については、「 `SET MIXED_PAGE_ALLOCATION` [transact-sql&#41;&#40;の ALTER Database SET オプション ](../../t-sql/statements/alter-database-transact-sql-set-options.md)のオプション」を参照してください。<br /> **適用対象**: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (以降 [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] )|  
|**is_temporal_history_retention_enabled**|**bit**|テンポラル保持ポリシーのクリーンアップタスクが有効かどうかを示します。<br /><br />1 = テンポラルリテンション期間が有効<br />0 = 一時的な保持は無効<br />**適用対象**: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 以降) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]|
|**catalog_collation_type**|**int**|カタログの照合順序の設定:<br />0 = DATABASE_DEFAULT<br />2 = SQL_Latin_1_General_CP1_CI_AS<br /> **適用対象**: [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]|
|**catalog_collation_type_desc**|**nvarchar(60)**|カタログの照合順序の設定:<br />COLLATE<br />SQL_Latin_1_General_CP1_CI_AS<br /> **適用対象**: [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]|
|**physical_database_name**|**nvarchar(128)**|の場合、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] データベースの物理名。 の場合 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 、サーバー上のデータベースの一般的な id です。 <br />**適用対象**: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[sql-server-2019](../../includes/sssql19-md.md)] 以降) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]|
|**is_result_set_caching_on**|**bit**|結果セットのキャッシュが有効かどうかを示します。<br />1 = 結果セットのキャッシュが有効<br />0 = 結果セットのキャッシュが無効<br />**適用対象**: [!INCLUDE[ssSDW](../../includes/sssdw-md.md)] Gen2。 この機能はすべてのリージョンにロールアウトされていますが、ご使用のインスタンスにデプロイされているバージョンと、利用可能な機能については、最新の [Azure Synapse リリースノート](/azure/synapse-analytics/sql-data-warehouse/release-notes-10-0-10106-0) と [Gen2 アップグレードスケジュール](/azure/synapse-analytics/sql-data-warehouse/gen2-migration-schedule) をご確認ください。|
|**is_accelerated_database_recovery_on**|**bit**|高速データベース回復 (ADR) が有効かどうかを示します。<br />1 = ADR が有効<br />0 = ADR は無効です。<br />**適用対象**: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[sql-server-2019](../../includes/sssql19-md.md)] 以降) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]|
|**is_tempdb_spill_to_remote_store**|**bit**|リモートストアへの tempdb の書き込みが有効になっているかどうかを示します。<br />1 = 有効<br />0 = 無効<br />**適用対象**: [!INCLUDE[ssSDW](../../includes/sssdw-md.md)] Gen2。 この機能はすべてのリージョンにロールアウトされていますが、ご使用のインスタンスにデプロイされているバージョンと、利用可能な機能については、最新の [Azure Synapse リリースノート](/azure/synapse-analytics/sql-data-warehouse/release-notes-10-0-10106-0) と [Gen2 アップグレードスケジュール](/azure/synapse-analytics/sql-data-warehouse/gen2-migration-schedule) をご確認ください。|
|**is_stale_page_detection_on**|**bit**|古いページ検出が有効になっているかどうかを示します。<br />1 = 古いページ検出が有効になっている<br />0 = 古いページ検出は無効になっています<br />**適用対象**: [!INCLUDE[ssSDW](../../includes/sssdw-md.md)] Gen2。 この機能はすべてのリージョンにロールアウトされていますが、ご使用のインスタンスにデプロイされているバージョンと、利用可能な機能については、最新の [Azure Synapse リリースノート](/azure/synapse-analytics/sql-data-warehouse/release-notes-10-0-10106-0) と [Gen2 アップグレードスケジュール](/azure/synapse-analytics/sql-data-warehouse/gen2-migration-schedule) をご確認ください。|
|**is_memory_optimized_enabled**|**bit**|[ハイブリッドバッファープール](../../database-engine/configure-windows/hybrid-buffer-pool.md)などの特定の In-Memory 機能がデータベースに対して有効かどうかを示します。 [インメモリ OLTP](../../relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization.md)の可用性または構成の状態は反映されません。 <br />1 = メモリ最適化機能が有効になっている<br />0 = メモリ最適化機能は無効です。<br />**適用対象**: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[sql-server-2019](../../includes/sssql19-md.md)] 以降) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]|
  
## <a name="permissions"></a>権限

 の呼び出し元 `sys.databases` がデータベースの所有者ではなく、データベースがまたはではない場合、 `master` 対応する行を `tempdb` 表示するために必要な最低限の権限は、 `ALTER ANY DATABASE` または `VIEW ANY DATABASE` `CREATE DATABASE` データベース内のサーバーレベルの権限、または権限です `master` 。 呼び出し元が接続されているデータベースは、常にで表示でき `sys.databases` ます。  
  
> [!IMPORTANT]  
> 既定では、public ロールには権限が付与されているの `VIEW ANY DATABASE` で、すべてのログインでデータベース情報を参照できます。 データベースを検出する権限、 `REVOKE` `VIEW ANY DATABASE` からの権限、 `public` または `DENY` `VIEW ANY DATABASE` 個々のログインに対する権限をブロックする権限。  
  
## <a name="azure-sql-database-remarks"></a>Azure SQL Database 解説

[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]このビューは、 `master` データベースとユーザーデータベースで使用できます。 データベースでは、このビューには、 `master` `master` サーバー上のデータベースとすべてのユーザーデータベースに関する情報が返されます。 ユーザー データベースでは、このビューには、現在のデータベースと master データベースのみの情報が返されます。  
  
 新しいデータベースが作成される [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] サーバーの `master` データベースの `sys.databases` ビューを使用します。 データベースのコピーが開始されたら、コピー `sys.databases` 先サーバーのデータベースからおよびビューに対してクエリを実行し、 `sys.dm_database_copies` `master` コピーの進行状況に関する詳細情報を取得できます。  
  
## <a name="examples"></a>例  
  
### <a name="a-query-the-sysdatabases-view"></a>A. sys.databases ビューに対するクエリ

次の例では、ビューで使用できる列のいくつかを返し `sys.databases` ます。  
  
```sql  
SELECT name, user_access_desc, is_read_only, state_desc, recovery_model_desc  
FROM sys.databases;  
```  
  
### <a name="b-check-the-copying-status-in-sssds"></a>B. [!INCLUDE[ssSDS](../../includes/sssds-md.md)] でのコピーの進行状況を確認します。

次の例では、ビューとビューに対してクエリを実行し、 `sys.databases` `sys.dm_database_copies` データベースのコピー操作に関する情報を返します。  
  
**適用対象**: [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]  
  
```sql
-- Execute from the master database.  
SELECT a.name, a.state_desc, b.start_date, b.modify_date, b.percent_complete  
FROM sys.databases AS a  
INNER JOIN sys.dm_database_copies AS b ON a.database_id = b.database_id  
WHERE a.state = 7;  
```

### <a name="c-check-the-temporal-retention-policy-status-in-sssds"></a>C. の一時リテンション期間ポリシーの状態を確認します。 [!INCLUDE[ssSDS](../../includes/sssds-md.md)]

次の例では、をクエリして、 `sys.databases` テンポラル保持クリーンアップタスクが有効になっているかどうかの情報を返します。 復元操作の後、テンポラルリテンション期間は既定で無効になっていることに注意してください。 を使用し `ALTER DATABASE` て、明示的に有効にします。
  
**適用対象**: [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]  
  
```sql  
-- Execute from the master database.  
SELECT a.name, a.is_temporal_history_retention_enabled 
FROM sys.databases AS a;
```  
  
## <a name="next-steps"></a>次のステップ

- [ALTER DATABASE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-database-transact-sql.md)
- [sys.database_mirroring_witnesses &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/database-mirroring-witness-catalog-views-sys-database-mirroring-witnesses.md)
- [sys.database_recovery_status &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-database-recovery-status-transact-sql.md)
- [データベースとファイルのカタログ ビュー &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/databases-and-files-catalog-views-transact-sql.md)
- [sys.dm_database_copies &#40;Azure SQL Database&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-database-copies-azure-sql-database.md)  
