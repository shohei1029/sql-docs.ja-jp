---
description: sys.dm_tran_locks (Transact-sql)
title: sys.dm_tran_locks (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/30/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- dm_tran_locks
- sys.dm_tran_locks
- sys.dm_tran_locks_TSQL
- dm_tran_locks_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_tran_locks dynamic management view
ms.assetid: f0d3b95a-8a00-471b-9da4-14cb8f5b045f
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 92852653bb3be257d66a202c671a9d65e4ae3efd
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104753792"
---
# <a name="sysdm_tran_locks-transact-sql"></a>sys.dm_tran_locks (Transact-sql)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  [!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] で現在アクティブなロック マネージャーのリソースに関する情報を返します。 各行は、ロック マネージャーに対して現在アクティブになっている要求を示しています。この要求は、許可されたロックまたは許可を待機しているロックに対するものです。  
  
 結果セットの列は、リソースと要求の 2 つの主要グループに分けられます。 リソース グループは、ロック要求が出されているリソースを示し、要求グループはロック要求を示します。  
  
> [!NOTE]  
> またはからこれを呼び出すに [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] は [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] 、 **sys.dm_pdw_nodes_tran_locks** という名前を使用します。  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|**resource_type**|**nvarchar(60)**|リソースの種類。 値は DATABASE、FILE、OBJECT、PAGE、KEY、EXTENT、RID、APPLICATION、METADATA、HOBT、ALLOCATION_UNIT のいずれかです。|  
|**resource_subtype**|**nvarchar(60)**|**resource_type** のサブタイプ。 親タイプのサブタイプ化されていないロックを保持せずに、サブタイプのロックを取得することができます。 サブタイプが異なる場合でも、サブタイプどうしや、サブタイプ化されていない親タイプとの競合は発生しません。 また、すべての種類のリソースにサブタイプが含まれるわけではありません。|  
|**resource_database_id**|**int**|リソースのスコープとなっているデータベースの ID。 ロック マネージャーによって処理されるすべてのリソースのスコープは、このデータベース ID に基づいて決定されます。|  
|**resource_description**|**nvarchar (256)**|別のリソース列からは使用できない情報のみを含むリソースの説明。|  
|**resource_associated_entity_id**|**bigint**|リソースが関連付けられているデータベース内のエンティティの ID。 この ID はリソースの種類に応じて、オブジェクト ID、Hobt ID、またはアロケーション ユニット ID になります。|  
|**resource_lock_partition**|**Int**|ロック リソースがパーティション分割されている場合の、ロック パーティションの ID。 パーティション分割されていないロック リソースの値は 0 です。|  
|**request_mode**|**nvarchar(60)**|要求のモード。 許可された要求については許可モード、待機中の要求については要求中モードになります。 <br /><br /> NULL = リソースへのアクセスは許可されていません。 プレースホルダーとして機能します。<br /><br /> Sch-m (スキーマの安定性) = スキーマ要素に対するスキーマの安定性ロックを保持しているセッションがある間、テーブルやインデックスなどのスキーマ要素が削除されないようにします。<br /><br /> Sch-m-M (スキーマ変更) = 指定されたリソースのスキーマを変更する必要があるすべてのセッションによって保持される必要があります。 指定されたオブジェクトを参照している他のセッションがないことを確認します。<br /><br /> S (共有) = 保持するセッションには、リソースへの共有アクセスが許可されます。<br /><br /> U (更新) = 最終的に更新される可能性のあるリソースに対して取得された更新ロックを示します。 これは、後で更新される可能性があるリソースが複数のセッションによってロックされるとき、一般的な形式のデッドロックが発生するのを防止するために使用します。<br /><br /> X (排他) = 保持するセッションには、リソースへの排他アクセスが許可されます。<br /><br /> IS (インテント共有) = ロック階層内の下位のリソースに S ロックを配置することを示します。<br /><br /> IU (インテント更新) = ロック階層内の下位のリソースに U ロックを設定することを示します。<br /><br /> IX (インテント排他) = ロック階層内の下位のリソースに X ロックを配置することを示します。<br /><br /> SIU (共有インテント更新) = ロック階層内の下位のリソースに更新ロックを取得する目的で、リソースへの共有アクセスを示します。<br /><br /> 6 (共有インテント排他) = ロック階層内の下位のリソースに排他ロックを取得する目的で、リソースへの共有アクセスを示します。<br /><br /> [UIX (更新インテント排他)] = ロック階層内の下位のリソースに排他ロックを取得することを目的として、リソースに対する更新ロックが保持されていることを示します。<br /><br /> BU = 一括操作で使用されます。<br /><br /> RangeS_S (共有 Key-Range と共有リソースロック) = シリアル化可能な範囲スキャンを示します。<br /><br /> RangeS_U (共有 Key-Range と更新リソースロック) = シリアル化可能な更新プログラムのスキャンを示します。<br /><br /> RangeI_N (Insert Key-Range と Null リソースロック) = 新しいキーをインデックスに挿入する前に範囲をテストするために使用します。<br /><br /> RangeI_S = RangeI_N と S ロックの重複によって作成された変換ロック Key-Range。<br /><br /> RangeI_U = RangeI_N と U ロックの重複によって作成された変換ロック Key-Range。<br /><br /> RangeI_X = RangeI_N と X ロックの重なりによって作成された変換ロック Key-Range。<br /><br /> RangeX_S = RangeI_N と RangeS_S の重なりによって作成された変換ロック Key-Range。 固定.<br /><br /> RangeX_U = RangeI_N および RangeS_U ロックの重なりによって作成された変換ロック Key-Range。<br /><br /> RangeX_X (排他 Key-Range と排他リソースロック) = これは、範囲内のキーを更新するときに使用される変換ロックです。|  
|**request_type**|**nvarchar(60)**|要求の種類。 値は LOCK です。|  
|**request_status**|**nvarchar(60)**|この要求の現在の状態。 指定できる値は、許可、変換、待機、LOW_PRIORITY_CONVERT、LOW_PRIORITY_WAIT、または ABORT_BLOCKERS です。 優先度の低い待機と中止ブロッカーの詳細については、「 [ALTER INDEX &#40;transact-sql&#41;](../../t-sql/statements/alter-index-transact-sql.md)」の「 *low_priority_lock_wait* 」を参照してください。|  
|**request_reference_count**|**smallint**|同じ要求元がこのリソースを要求した回数の概数。|  
|**request_lifetime**|**int**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|**request_session_id**|**int**|要求を現在所有するセッション ID。 所有セッション ID は、分散トランザクションとバインドされたトランザクションでは異なります。 値が -2 の場合、その要求が孤立した分散トランザクションに属することを示します。 値が -3 の場合、その要求が遅延復旧トランザクションに属することを示します。遅延復旧トランザクションとは、たとえば、ロールバックが正常に完了しなかったためにロールバックの復旧を遅延したトランザクションのことです。|  
|**request_exec_context_id**|**int**|要求を現在所有するプロセスの、実行コンテキスト ID。|  
|**request_request_id**|**int**|要求を現在所有するプロセスの要求 ID (バッチ ID)。 この値は、トランザクションに対する複数のアクティブな結果セット (MARS) 接続が変わるたびに変化します。|  
|**request_owner_type**|**nvarchar(60)**|要求を所有するエンティティの種類。 ロック マネージャーの要求は、さまざまな種類のエンティティで所有されます。 次のいずれかの値になります。<br /><br /> TRANSACTION = 要求はトランザクションが所有しています。<br /><br /> CURSOR = 要求はカーソルが所有しています。<br /><br /> SESSION = 要求はユーザー セッションが所有しています。<br /><br /> SHARED_TRANSACTION_WORKSPACE = 要求は、トランザクション ワークスペースの共有部分が所有しています。<br /><br /> EXCLUSIVE_TRANSACTION_WORKSPACE = 要求は、トランザクション ワークスペースの排他部分が所有しています。<br /><br /> NOTIFICATION_OBJECT = 要求は、内部 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] コンポーネントが所有しています。 このコンポーネントは、別のコンポーネントがロックの取得を待機しているときに、そのことを通知するようにロック マネージャーに要求しました。 FileTable 機能は、この値を使用するコンポーネントです。<br /><br /> **注:** ワークスペースは、参加しているセッションのロックを保持するために内部的に使用されます。|  
|**request_owner_id**|**bigint**|この要求の特定の所有者の ID。<br /><br /> トランザクションが要求の所有者である場合、この値にはトランザクション ID が含まれます。<br /><br /> FileTable が要求の所有者である場合、**request_owner_id** には次のいずれかの値が含まれます。<br /> <ul><li>**-4** : FileTable はデータベースロックを取得しました。<li> **-3** : FileTable はテーブルロックを取得しました。<li> **その他の値** : 値はファイルハンドルを表します。 この値は、動的管理ビュー [sys.dm_filestream_non_transacted_handles &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-filestream-non-transacted-handles-transact-sql.md)に **fcb_id** としても表示されます。</li></ul>|  
|**request_owner_guid**|**uniqueidentifier**|この要求の特定の所有者の GUID。 この値は、分散トランザクションの MS DTC GUID に対応する場合に、そのトランザクションによってのみ使用されます。|  
|**request_owner_lockspace_id**|**nvarchar(32)**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]この値は、要求元のロック領域 ID を示します。 ロック領域 ID によって、2 つの要求元の間に互換性があり、互いに競合しないモードでロックを許可できるかどうかを判断できます。|  
|**lock_owner_address**|**varbinary (8)**|要求を追跡するときに使用される内部データ構造のメモリ アドレス。 この列は **sys.dm_os_waiting_tasks** の **resource_address** 列と結合できます。|  
|**pdw_node_id**|**int**|**適用対象**: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 、 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]<br /><br /> このディストリビューションが配置されているノードの識別子。|  
  
## <a name="permissions"></a>権限
で [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] は、 `VIEW SERVER STATE` 権限が必要です。   
SQL Database Basic、S0、S1 のサービス目標、およびエラスティックプール内のデータベースについては、 [サーバー管理者](/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database) アカウントまたは [Azure Active Directory 管理者](/azure/azure-sql/database/authentication-aad-overview#administrator-structure) アカウントが必要です。 その他のすべての SQL Database サービスの目的で `VIEW DATABASE STATE` は、データベースで権限が必要になります。   
 
## <a name="remarks"></a>解説  
 要求が許可された状態とは、要求元に対してリソースのロックが許可されたことを示します。 要求を待機している状態とは、その要求がまだ許可されていないことを示します。 次に示す要求待機の種類は、**request_status** 列で返されます。  
  
-   要求変換の状態とは、要求元がリソース要求を既に許可されており、最初の要求からさらに上の段階の許可を現在待機中であることを示します。  
  
-   要求待機の状態とは、要求元が現在、リソースに対して許可された要求を保持していないことを示します。  
  
 **sys.dm_tran_locks** には内部のロック マネージャーのデータ構造を基にデータが追加されるため、この情報を保持していても、通常の処理に余分なオーバーヘッドはかかりません。 ビューを具現化するには、ロック マネージャーの内部データ構造にアクセスする必要があります。 これによって、サーバーでの通常の処理にわずかな影響が生じることがありますが、 このような影響は重要ではなく、頻繁に使用されるリソースのみに影響します。 このビューのデータはアクティブなロック マネージャーの状態に対応しているので、データはいつでも変更される可能性があります。ロックが取得または解放されるときに、行が追加または削除されます。 このビューには、履歴情報はありません。  
  
 2 つの要求は、すべてのリソース グループの行が等しい場合のみ、同じリソースに実行されます。  
  
 次のツールを使用すると、読み取り操作のロックを制御できます。  
  
-   SET TRANSACTION ISOLATION LEVEL を使用すると、セッションに対するロックのレベルを指定できます。 詳細については、「[SET TRANSACTION ISOLATION LEVEL &#40;Transact-SQL&#41;](../../t-sql/statements/set-transaction-isolation-level-transact-sql.md)」を参照してください。  
  
-   テーブル ヒントをロックすると、FROM 句内にあるテーブルの個別の参照に対してロックのレベルを指定できます。 構文と制限については、「 [transact-sql&#41;&#40;テーブルヒント ](../../t-sql/queries/hints-transact-sql-table.md)」を参照してください。  
  
 1 つのセッション ID で実行されているリソースには、複数のロックを許可できます。 つまり、1 つのセッションで実行中の複数のエンティティが、同じリソースに対して別々のロックを保持できます。この情報は、**sys.dm_tran_locks** で返される **request_owner_type** 列と **request_owner_id** 列に表示されます。 **request_owner_type** 列の値が同じになっているインスタンスが複数存在する場合、各インスタンスを区別するには **request_owner_id** 列を使用します。 分散トランザクションでは、**request_owner_type** 列と **request_owner_guid** 列に基づいて、異なるエンティティ情報が表示されます。  
  
 たとえば、セッション S1 が **テーブル 1** に共有ロックを保持しており、セッション S1 で実行中のトランザクション T1 も **テーブル 1** に共有ロックを保持しているとします。 この場合、**sys.dm_tran_locks** で返される **resource_description** 列には、同じリソースの 2 つのインスタンスが表示されます。 **request_owner_type** 列には、1 つのインスタンスがセッションとして表示され、もう 1 つのインスタンスがトランザクションとして表示されます。 また、**resource_owner_id** 列には異なる値が設定されます。  
  
 1 つのセッションで実行する複数のカーソルは区別できないため、1 つのエンティティとして扱われます。  
  
 セッション ID 値に関連付けられていない分散トランザクションは孤立したトランザクションで、セッション ID 値 -2 が割り当てられます。 詳しくは、「[KILL &#40;Transact-SQL&#41;](../../t-sql/language-elements/kill-transact-sql.md)」をご覧ください。  

## <a name="locks"></a><a name="locks"></a> 固定
ロックは、複数のトランザクションで同じ [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] リソースが同時に使用されるのを防ぐために、トランザクション中に読み取られたり変更されたりする行などにかけられます。 たとえば、あるトランザクションによってテーブルの行に排他 (X) ロックがかけられると、他のトランザクションはロックが解除されるまでその行を変更できません。 ロックを最小限にとどめるとコンカレンシーが向上し、パフォーマンスが向上します。 

## <a name="resource-details"></a>リソースの詳細  
 次の表は、**resource_associated_entity_id** 列に表示されるリソースの一覧です。  
  
|リソースの種類|リソースの説明|Resource_associated_entity_id|  
|-------------------|--------------------------|--------------------------------------|  
|DATABASE|データベースを示します。|適用なし|  
|ファイル|データベース ファイルを示します。 このファイルは、データまたはログ ファイルのいずれかになります。|適用なし|  
|OBJECT|データベース オブジェクトを示します。 このオブジェクトは、データ テーブル、ビュー、ストアド プロシージャ、拡張ストアド プロシージャ、またはオブジェクト ID 付きのオブジェクトのいずれかになります。|オブジェクト ID|  
|PAGE|データ ファイル内の 1 ページを示します。|HoBt ID。 この値は **sys.partitions.hobt_id** に対応します。 PAGE リソースに対し常に HoBt ID が使用できるとは限りません。HoBt ID は呼び出し元から提供される追加情報であり、呼び出し元によってはこの情報を提供できないことがあります。|  
|KEY|インデックス内の行を示します。|HoBt ID。 この値は **sys.partitions.hobt_id** に対応します。|  
|EXTENT|データ ファイルのエクステントを示します。 エクステントは連続する 8 ページのグループです。|適用なし|  
|RID|ヒープ内の物理的な行を示します。|HoBt ID。 この値は **sys.partitions.hobt_id** に対応します。 RID リソースに対し常に HoBt ID が使用できるとは限りません。HoBt ID は呼び出し元から提供される追加情報であり、呼び出し元によってはこの情報を提供できないことがあります。|  
|APPLICATION|アプリケーション固有のリソースを示します。|適用なし|  
|METADATA|メタデータの情報を示します。|適用なし|  
|HOBT|ヒープまたは B-Tree を示します。 これは、基本的なアクセス パス構造です。|HoBt ID。 この値は **sys.partitions.hobt_id** に対応します。|  
|ALLOCATION_UNIT|インデックス パーティションなどの関連ページのセットを示します。 各アロケーション ユニットは、1 つの IAM (Index Allocation Map) チェーンを対象としています。|アロケーション ユニット ID。 この値は **sys.allocation_units.allocation_unit_id** に対応します。|  
  
 次の表は、各リソースの種類に関連付けられるサブタイプの一覧です。  
  
|リソースのサブタイプ|同期|  
|---------------------|------------------|  
|ALLOCATION_UNIT.BULK_OPERATION_PAGE|一括操作で使用されるあらかじめ割り当てられたページ。|  
|ALLOCATION_UNIT.PAGE_COUNT|遅延削除操作での、アロケーション ユニットのページ数の統計。|  
|DATABASE.BULKOP_BACKUP_DB|データベースのバックアップと一括操作。|  
|DATABASE.BULKOP_BACKUP_LOG|データベース ログのバックアップと一括操作。|  
|DATABASE.CHANGE_TRACKING_CLEANUP|変更の追跡のクリーンアップ タスク。|  
|DATABASE.CT_DDL|データベースおよびテーブルレベルの変更の追跡の DDL 操作。|  
|DATABASE.CONVERSATION_PRIORITY|CREATE BROKER PRIORITY などの Service Broker のメッセージ交換の優先度操作。|  
|DATABASE.DDL|データ定義言語 (DDL) 操作と、削除などのファイル グループ操作。|  
|DATABASE.ENCRYPTION_SCAN|TDE 暗号化の同期。|  
|DATABASE.PLANGUIDE|プラン ガイドの同期。|  
|DATABASE.RESOURCE_GOVERNOR_DDL|ALTER RESOURCE POOL などのリソース ガバナー操作の DDL 操作。|  
|DATABASE.SHRINK|データベースの圧縮操作。|  
|DATABASE.STARTUP|データベースの起動を同期するときに使用します。|  
|FILE.SHRINK|ファイルの圧縮操作。|  
|HOBT.BULK_OPERATION|SNAPSHOT、READ COMMITTED、および行のバージョン管理を使用する READ COMMITTED の分離レベルでの、ヒープが最適化された一括読み込み操作と同時実行スキャン。|  
|HOBT.INDEX_REORGANIZE|ヒープまたはインデックスの再構成操作。|  
|OBJECT.COMPILE|ストアド プロシージャのコンパイル。|  
|OBJECT.INDEX_OPERATION|インデックス操作。|  
|OBJECT.UPDSTATS|テーブル上の統計の更新。|  
|METADATA.ASSEMBLY|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.ASSEMBLY_CLR_NAME|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.ASSEMBLY_TOKEN|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.ASYMMETRIC_KEY|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.AUDIT|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.AUDIT_ACTIONS|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.AUDIT_SPECIFICATION|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.AVAILABILITY_GROUP|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.CERTIFICATE|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.CHILD_INSTANCE|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.COMPRESSED_FRAGMENT|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.COMPRESSED_ROWSET|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.CONVERSTATION_ENDPOINT_RECV|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.CONVERSTATION_ENDPOINT_SEND|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.CONVERSATION_GROUP|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.CONVERSATION_PRIORITY|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.CREDENTIAL|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.CRYPTOGRAPHIC_PROVIDER|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.DATA_SPACE|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.DATABASE|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.DATABASE_PRINCIPAL|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.DB_MIRRORING_SESSION|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.DB_MIRRORING_WITNESS|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.DB_PRINCIPAL_SID|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.ENDPOINT|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.ENDPOINT_WEBMETHOD|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.EXPR_COLUMN|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.EXPR_HASH|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.FULLTEXT_CATALOG|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.FULLTEXT_INDEX|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.FULLTEXT_STOPLIST|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.INDEX_EXTENSION_SCHEME|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.INDEXSTATS|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.INSTANTIATED_TYPE_HASH|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.MESSAGE|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.METADATA_CACHE|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.PARTITION_FUNCTION|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.PASSWORD_POLICY|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.PERMISSIONS|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.PLAN_GUIDE|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.PLAN_GUIDE_HASH|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.PLAN_GUIDE_SCOPE|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.QNAME|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.QNAME_HASH|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.REMOTE_SERVICE_BINDING|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.ROUTE|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.SCHEMA|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.SECURITY_CACHE|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.SECURITY_DESCRIPTOR|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.SEQUENCE|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.SERVER_EVENT_SESSIONS|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.SERVER_PRINCIPAL|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.SERVICE|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.SERVICE_BROKER_GUID|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.SERVICE_CONTRACT|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.SERVICE_MESSAGE_TYPE|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.STATS|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.SYMMETRIC_KEY|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.USER_TYPE|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.XML_COLLECTION|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.XML_COMPONENT|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.XML_INDEX_QNAME|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
  
 次の表は、各リソースの種類に対する **resource_description** 列の形式です。  
  
|リソース|Format|説明|  
|--------------|------------|-----------------|  
|DATABASE|適用なし|データベース ID は **resource_database_id** 列で既に使用可能になっています。|  
|ファイル|<file_id>|このリソースが示すファイルの ID。|  
|OBJECT|<object_id>|このリソースが示すオブジェクトの ID。 テーブルだけでなく、**sys.objects** に一覧表示されるあらゆるオブジェクトが対象になります。|  
|PAGE|<file_id>:<page_in_file>|このリソースが示すページの、ファイルおよびページ ID。|  
|KEY|<hash_value>|このリソースが示す行のキー列のハッシュ。|  
|EXTENT|<file_id>:<page_in_files>|このリソースが示すエクステントの、ファイルおよびページ ID。 エクステント ID は、そのエクステント内にある最初のページのページ ID と同じになります。|  
|RID|<file_id>:<page_in_file>:<row_on_page>|このリソースが示すページ ID と、行の行 ID。 関連するオブジェクト ID が 99 の場合、このリソースは、IAM チェーンの最初の IAM ページにある、8 つの混合ページ スロットのいずれかを表すことに注意してください。|  
|APPLICATION|\<DbPrincipalId>: \<upto 32 characters> :(<hash_value>)|アプリケーションのロック リソースのスコープに使用する、データベース プリンシパルの ID。 アプリケーションのロック リソースに対応する、最大 32 文字のリソース文字列も含まれます。 場合によっては、完全な文字列が使用できず、2 文字のみが表示されることがあります。 この動作は、データベース復旧時、復旧処理の一部として再取得されるアプリケーション ロックに関してのみ発生します。 ハッシュ値は、このアプリケーション ロック リソースに対応する、完全リソース文字列のハッシュを示します。|  
|HOBT|適用なし|**resource_associated_entity_id** として含まれる HoBt ID。|  
|ALLOCATION_UNIT|適用なし|**resource_associated_entity_id** として含まれるアロケーション ユニット ID。|  
|METADATA.ASSEMBLY|assembly_id = A|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.ASSEMBLY_CLR_NAME|$qname_id = Q|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.ASSEMBLY_TOKEN|assembly_id = A, $token_id|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.ASSYMMETRIC_KEY|asymmetric_key_id = A|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.AUDIT|audit_id = A|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.AUDIT_ACTIONS|device_id = D, major_id = M|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.AUDIT_SPECIFICATION|audit_specification_id = A|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.AVAILABILITY_GROUP|availability_group_id = A|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.CERTIFICATE|certificate_id = C|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.CHILD_INSTANCE|$hash = H1:H2:H3|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.COMPRESSED_FRAGMENT|object_id = O , compressed_fragment_id = C|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.COMPRESSED_ROW|object_id = O|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.CONVERSTATION_ENDPOINT_RECV|$hash = H1:H2:H3|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.CONVERSTATION_ENDPOINT_SEND|$hash = H1:H2:H3|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.CONVERSATION_GROUP|$hash = H1:H2:H3|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.CONVERSATION_PRIORITY|conversation_priority_id = C|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.CREDENTIAL|credential_id = C|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.CRYPTOGRAPHIC_PROVIDER|provider_id = P|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.DATA_SPACE|data_space_id = D|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.DATABASE|database_id = D|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.DATABASE_PRINCIPAL|principal_id = P|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.DB_MIRRORING_SESSION|database_id = D|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.DB_MIRRORING_WITNESS|$hash = H1:H2:H3|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.DB_PRINCIPAL_SID|$hash = H1:H2:H3|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.ENDPOINT|endpoint_id = E|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.ENDPOINT_WEBMETHOD|$hash = H1:H2:H3|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.FULLTEXT_CATALOG|fulltext_catalog_id = F|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.FULLTEXT_INDEX|object_id = O|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.EXPR_COLUMN|object_id = O, column_id = C|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.EXPR_HASH|object_id = O, $hash = H|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.FULLTEXT_CATALOG|fulltext_catalog_id = F|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.FULLTEXT_INDEX|object_id = O|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.FULLTEXT_STOPLIST|fulltext_stoplist_id = F|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.INDEX_EXTENSION_SCHEME|index_extension_id = I|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.INDEXSTATS|object_id = O, index_id or stats_id = I|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.INSTANTIATED_TYPE_HASH|user_type_id = U, hash = H|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.MESSAGE|message_id = M|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.METADATA_CACHE|$hash = H1:H2:H3|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.PARTITION_FUNCTION|function_id = F|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.PASSWORD_POLICY|principal_id = P|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.PERMISSIONS|class = C|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.PLAN_GUIDE|plan_guide_id = P|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA. PLAN_GUIDE_HASH|$hash = H1:H2:H3|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA. PLAN_GUIDE_SCOPE|scope_id = S|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.QNAME|$qname_id = Q|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.QNAME_HASH|$qname_scope_id = Q, $qname_hash = H|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.REMOTE_SERVICE_BINDING|remote_service_binding_id = R|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.ROUTE|route_id = R|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.SCHEMA|schema_id = S|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.SECURITY_CACHE|$hash = H1:H2:H3|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.SECURITY_DESCRIPTOR|sd_id = S|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.SEQUENCE|$seq_type = S, object_id = O|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.SERVER|server_id = S|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.SERVER_EVENT_SESSIONS|event_session_id = E|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.SERVER_PRINCIPAL|principal_id = P|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.SERVICE|service_id = S|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.SERVICE_BROKER_GUID|$hash = H1:H2:H3|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.SERVICE_CONTRACT|service_contract_id = S|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.SERVICE_MESSAGE_TYPE|message_type_id = M|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.STATS|object_id = O, stats_id = S|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.SYMMETRIC_KEY|symmetric_key_id = S|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.USER_TYPE|user_type_id = U|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.XML_COLLECTION|xml_collection_id = X|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.XML_COMPONENT|xml_component_id = X|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.XML_INDEX_QNAME|object_id = O, $qname_id = Q|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
  
 次の Xevent は、パーティション **切り替え** とオンラインインデックス再構築に関連しています。 構文の詳細については、「 [ALTER TABLE &#40;transact-sql&#41;](../../t-sql/statements/alter-table-transact-sql.md) 」および「 [Alter INDEX &#40;transact-sql&#41;](../../t-sql/statements/alter-index-transact-sql.md)」を参照してください。  
  
-   lock_request_priority_state  
  
-   process_killed_by_abort_blockers  
  
-   ddl_with_wait_at_low_priority  
  
 オンラインインデックス操作用の既存の XEvent **progress_report_online_index_operation** は、 **partition_number** と **partition_id** を追加することによって拡張されました。  
  
## <a name="examples"></a>例  
  
### <a name="a-using-sysdm_tran_locks-with-other-tools"></a>A. 別のツールで sys.dm_tran_locks を使用する  
 次の例では、更新操作が別のトランザクションによってブロックされたシナリオを処理します。 **sys.dm_tran_locks** と追加ツールを使用すると、リソースのロックに関する情報を取得できます。  
  
```sql  
USE tempdb;  
GO  
  
-- Create test table and index.  
CREATE TABLE t_lock  
    (  
    c1 int, c2 int  
    );  
GO  
  
CREATE INDEX t_lock_ci on t_lock(c1);  
GO  
  
-- Insert values into test table  
INSERT INTO t_lock VALUES (1, 1);  
INSERT INTO t_lock VALUES (2,2);  
INSERT INTO t_lock VALUES (3,3);  
INSERT INTO t_lock VALUES (4,4);  
INSERT INTO t_lock VALUES (5,5);  
INSERT INTO t_lock VALUES (6,6);  
GO  
  
-- Session 1  
SET TRANSACTION ISOLATION LEVEL READ COMMITTED;  
  
BEGIN TRAN  
    SELECT c1  
        FROM t_lock  
        WITH(holdlock, rowlock);  
  
-- Session 2  
BEGIN TRAN  
    UPDATE t_lock SET c1 = 10  
```  
  
 次のクエリでは、ロックの情報を表示します。 `<dbid>` の値は、**sys.databases** からの **database_id** に置き換える必要があります。  
  
```sql  
SELECT resource_type, resource_associated_entity_id,  
    request_status, request_mode,request_session_id,  
    resource_description   
    FROM sys.dm_tran_locks  
    WHERE resource_database_id = <dbid>  
```  
  
 次のクエリでは、前のクエリからの `resource_associated_entity_id` を使用してオブジェクトの情報を返します。 このクエリは、オブジェクトを含むデータベースに接続して実行する必要があります。  
  
```  
SELECT object_name(object_id), *  
    FROM sys.partitions  
    WHERE hobt_id=<resource_associated_entity_id>  
```  
  
 次のクエリでは、ブロッキング情報を表示します。  
  
```sql  
SELECT   
        t1.resource_type,  
        t1.resource_database_id,  
        t1.resource_associated_entity_id,  
        t1.request_mode,  
        t1.request_session_id,  
        t2.blocking_session_id  
    FROM sys.dm_tran_locks as t1  
    INNER JOIN sys.dm_os_waiting_tasks as t2  
        ON t1.lock_owner_address = t2.resource_address;  
```  
  
 リソースを解放するには、トランザクションをロールバックします。  
  
```sql  
-- Session 1  
ROLLBACK;  
GO  
  
-- Session 2  
ROLLBACK;  
GO  
```  
  
### <a name="b-linking-session-information-to-operating-system-threads"></a>B. セッション情報のオペレーティングシステムスレッドへのリンク  
 次の例では、セッション ID と Windows のスレッド ID を関連付ける情報を返します。 スレッドのパフォーマンスは、Windows パフォーマンス モニターで監視できます。 このクエリでは、現在休止しているセッション ID は返されません。  
  
```sql  
SELECT STasks.session_id, SThreads.os_thread_id  
    FROM sys.dm_os_tasks AS STasks  
    INNER JOIN sys.dm_os_threads AS SThreads  
        ON STasks.worker_address = SThreads.worker_address  
    WHERE STasks.session_id IS NOT NULL  
    ORDER BY STasks.session_id;  
GO  
```  
  
## <a name="see-also"></a>参照  
[sys.dm_tran_database_transactions &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-tran-database-transactions-transact-sql.md)      
[動的管理ビューと動的管理関数 &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)     
[トランザクション関連の動的管理ビューおよび関数 &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/transaction-related-dynamic-management-views-and-functions-transact-sql.md)      
[SQL Server の Locks オブジェクト](../../relational-databases/performance-monitor/sql-server-locks-object.md)