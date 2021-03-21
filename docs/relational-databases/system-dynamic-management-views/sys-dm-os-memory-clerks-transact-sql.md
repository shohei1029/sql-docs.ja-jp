---
description: sys.dm_os_memory_clerks (Transact-SQL)
title: sys.dm_os_memory_clerks (Transact-SQL)
ms.custom: ''
ms.date: 02/18/2021
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- dm_os_memory_clerks
- sys.dm_os_memory_clerks
- dm_os_memory_clerks_TSQL
- sys.dm_os_memory_clerks_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_os_memory_clerks dynamic management view
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 9d02513db2bedda6e0ce6291bb0e0c3c308f6540
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104750982"
---
# <a name="sysdm_os_memory_clerks-transact-sql"></a>sys.dm_os_memory_clerks (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  のインスタンスで現在アクティブなすべてのメモリクラークのセットを返し [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。  
  
> [!NOTE]  
>  またはからこれを呼び出すに [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] は [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] 、 **sys.dm_pdw_nodes_os_memory_clerks** という名前を使用します。 
 
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|**memory_clerk_address**|**varbinary (8)**|メモリ クラークの一意のメモリ アドレスを指定します。 これは主キー列です。 NULL 値は許可されません。|  
|**type**|**nvarchar(60)**|メモリクラークの種類を指定します。 各クラークには、CLR Clerks MEMORYCLERK_SQLCLR などの特定の種類があります。 NULL 値は許可されません。|  
|**name**|**nvarchar (256)**|このメモリ クラークに内部的に割り当てられた名前を指定します。 コンポーネントは、特定の種類の複数のメモリクラークを持つことができます。 同じ種類のメモリ クラークを識別するために、コンポーネントで特定の名前を選択して使用することもできます。 NULL 値は許可されません。|  
|**memory_node_id**|**smallint**|メモリノードの ID を指定します。 NULL 値は許可されません。|  
|**single_pages_kb**|**bigint**|**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] から [!INCLUDE[ssKilimanjaro](../../includes/sskilimanjaro-md.md)] 詳細については、「 [SQL Server 2012 (2.x) 以降のメモリ管理の変更](../memory-management-architecture-guide.md#changes-to-memory-management-starting-with-)」を参照してください。|  
|**pages_kb**|**bigint**|**適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降。<br /><br /> このメモリクラークに割り当てられたページメモリの量をキロバイト (KB) 単位で指定します。 NULL 値は許可されません。|  
|**multi_pages_kb**|**bigint**|**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] から [!INCLUDE[ssKilimanjaro](../../includes/sskilimanjaro-md.md)] 詳細については、「 [SQL Server 2012 (2.x) 以降のメモリ管理の変更](../memory-management-architecture-guide.md#changes-to-memory-management-starting-with-)」を参照してください。<br /><br /> 割り当てられた複数ページメモリの量 (KB 単位)。 これは、メモリノードの複数ページアロケーターを使用して割り当てられたメモリの量です。 このメモリは、バッファー プール外に割り当てられ、メモリ ノードの仮想アロケーターを利用します。 NULL 値は許可されません。|  
|**virtual_memory_reserved_kb**|**bigint**|メモリクラークによって予約されている仮想メモリの量を指定します。 NULL 値は許可されません。|  
|**virtual_memory_committed_kb**|**bigint**|メモリクラークによってコミットされる仮想メモリの量を指定します。 コミット済みのメモリ量は、予約済みのメモリ量よりも常に少ない状態である必要があります。 NULL 値は許可されません。|  
|**awe_allocated_kb**|**bigint**|オペレーティングシステムによってページングされていない物理メモリ内のメモリの量をキロバイト (KB) 単位で指定します。 NULL 値は許可されません。|  
|**shared_memory_reserved_kb**|**bigint**|メモリクラークによって予約されている共有メモリの量を指定します。 共有メモリおよびファイル マッピングで使用するために予約されるメモリの量です。 NULL 値は許可されません。|  
|**shared_memory_committed_kb**|**bigint**|メモリ クラークによってコミット済みの共有メモリの量を指定します。 NULL 値は許可されません。|  
|**page_size_in_bytes**|**bigint**|このメモリ クラークのページ割り当ての粒度を指定します。 NULL 値は許可されません。|  
|**page_allocator_address**|**varbinary (8)**|ページアロケーターのアドレスを指定します。 このアドレスは、メモリクラークに対して一意であり、 **sys.dm_os_memory_objects** で使用して、この clerk にバインドされているメモリオブジェクトを見つけることができます。 NULL 値は許可されません。|  
|**host_address**|**varbinary (8)**|このメモリ クラークのホストのメモリ アドレスを指定します。 詳細については、「 [sys.dm_os_hosts &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-os-hosts-transact-sql.md)」を参照してください。 Native Client などのコンポーネントは、 [!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ホストインターフェイスを介してメモリリソースにアクセスします。<br /><br /> 0x00000000 = メモリ クラークは、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] に属します。<br /><br /> NULL 値は許可されません。|  
|**pdw_node_id**|**int**|**適用対象**: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 、 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]<br /><br /> このディストリビューションが配置されているノードの識別子。|  

## <a name="permissions"></a>権限

で [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] は、 `VIEW SERVER STATE` 権限が必要です。   
[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]Basic、S0、S1 のサービス目標、およびエラスティックプール内のデータベースについては、[サーバー管理者](/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database)アカウントまたは[Azure Active Directory 管理者](/azure/azure-sql/database/authentication-aad-overview#administrator-structure)アカウントが必要です。 他のすべてのサービスの目的では、 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] `VIEW DATABASE STATE` データベースで権限が必要です。   
  
## <a name="remarks"></a>解説

 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]メモリマネージャーは、3層階層で構成されます。 階層の最下部には、メモリノードがあります。 中間レベルは、メモリクラーク、メモリキャッシュ、およびメモリプールで構成されます。 最上位の階層はメモリ オブジェクトから成ります。 これらのオブジェクトは、のインスタンスにメモリを割り当てるために使用され [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。  
  
 メモリノードは、低レベルのアロケーターのインターフェイスと実装を提供します。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 内部では、メモリ クラークのみがメモリ ノードにアクセスできます。 メモリクラークはメモリノードインターフェイスにアクセスしてメモリを割り当てます。 また、メモリノードは、診断の clerk を使用して割り当てられたメモリを追跡します。 大量のメモリを割り当てるすべてのコンポーネントは、独自のメモリクラークを作成し、clerk インターフェイスを使用してすべてのメモリを割り当てる必要があります。 通常、コンポーネントは、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の起動時に、それぞれに対応したクラークを作成します。  

### <a name="cachestore-and-userstore"></a>CACHESTORE と USERSTORE

CACHESTORE と USERSTORE はメモリクラークですが、実際のキャッシュとして機能します。 通常、キャッシュ削除ポリシーによって割り当てが解放されるまで、キャッシュは割り当てを保持します。 キャッシュされた割り当てを再作成しないようにするために、キャッシュされた割り当ては可能な限りキャッシュに保持され、通常はキャッシュから削除されます。または、新しい情報のためにメモリ領域が必要な場合は、キャッシュから削除されます (詳細については、「 [クロックハンドスイープ](sys-dm-os-memory-cache-clock-hands-transact-sql.md#remarks)」を参照)。 これは、キャッシュの有効期間制御と可視性制御の2つの主要なコントロールの1つです。

キャッシュストアとユーザーストアは、割り当ての有効期間を制御する方法が異なります。 キャッシュストアの場合、エントリの有効期間は SQLOS のキャッシュフレームワークによって完全に制御されます。 ユーザーストアでは、エントリの有効期間はストアによってのみ部分的に制御されます。 各ユーザーストアの実装はメモリ割り当ての性質に固有であるため、ユーザーストアはそのエントリの有効期間の制御に参加します。 

可視性コントロールは、エントリの表示を管理します。 キャッシュ内のエントリは存在しても、表示されない場合があります。 たとえば、キャッシュエントリが1回だけ使用するようにマークされている場合、エントリは使用後に表示されません。 また、キャッシュエントリがダーティとマークされることもあります。キャッシュには引き続き存在しますが、参照することはできません。 どちらのストアでも、エントリの可視性はキャッシュフレームワークによって制御されます。

詳細については、「 [Sqlos キャッシュ](https://docs.microsoft.com/archive/blogs/slavao/sqlos-caching)」を参照してください。

### <a name="objectstore"></a>OBJECTSTORE

オブジェクトストアは、単純なプールです。 これは、同種のデータをキャッシュするために使用されます。 プール内のすべてのエントリが等しいと見なされます。  オブジェクトストアは、他のキャッシュとの相対的なサイズを制御する最大キャップを実装します。

詳細については、「 [Sqlos キャッシュ](https://docs.microsoft.com/archive/blogs/slavao/sqlos-caching)」を参照してください。

## <a name="types"></a>種類

次の表は、メモリクラークの種類を示しています。

|Type  |説明  |
|---------|---------|
|CACHESTORE_BROKERDSH     |     このキャッシュストアは [Service Broker](../../database-engine/configure-windows/sql-server-service-broker.md) ダイアログセキュリティヘッダーキャッシュによって割り当てを格納するために使用されます    |
|CACHESTORE_BROKERKEK     |   このキャッシュストアは [Service Broker](../../database-engine/configure-windows/sql-server-service-broker.md)  キー交換キーキャッシュによって割り当てを格納するために使用されます    |
|CACHESTORE_BROKERREADONLY     |     このキャッシュストアは [Service Broker](../../database-engine/configure-windows/sql-server-service-broker.md) 読み取り専用キャッシュによって割り当てを格納するために使用されます    |
|CACHESTORE_BROKERRSB     |  このキャッシュストアは、[リモートサービスバインド](../../t-sql/statements/create-remote-service-binding-transact-sql.md)キャッシュ[Service Broker](../../database-engine/configure-windows/sql-server-service-broker.md)によって割り当てを格納するために使用されます。    |
|CACHESTORE_BROKERTBLACS     |     このキャッシュストアは、セキュリティアクセス構造のために [Service Broker](../../database-engine/configure-windows/sql-server-service-broker.md) によって割り当てを格納するために使用されます。    |
|CACHESTORE_BROKERTO     |  このキャッシュストアは [Service Broker](../../database-engine/configure-windows/sql-server-service-broker.md) [転送オブジェクト](../performance-monitor/sql-server-broker-to-statistics-object.md) キャッシュによって割り当てを格納するために使用されます   |
|CACHESTORE_BROKERUSERCERTLOOKUP     |    このキャッシュストアは [Service Broker](../../database-engine/configure-windows/sql-server-service-broker.md)  ユーザー証明書の参照キャッシュによって割り当てを格納するために使用されます  |
|CACHESTORE_COLUMNSTOREOBJECTPOOL     |     このキャッシュストアは、[セグメント](../system-catalog-views/sys-column-store-segments-transact-sql.md)と[ディクショナリ](../system-catalog-views/sys-column-store-dictionaries-transact-sql.md)の[列ストアインデックス](../indexes/columnstore-indexes-overview.md)による割り当てに使用されます   |
|CACHESTORE_CONVPRI     |  このキャッシュストアは、メッセージ[交換の優先度](../system-catalog-views/sys-conversation-priorities-transact-sql.md)を追跡するために[Service Broker](../../database-engine/configure-windows/sql-server-service-broker.md)によって割り当てを格納するために使用されます。     |
|CACHESTORE_EVENTS     |     このキャッシュストアは[Service Broker](../../database-engine/configure-windows/sql-server-service-broker.md) [イベント通知](../service-broker/event-notifications.md)によって割り当てを格納するために使用されます |
|CACHESTORE_FULLTEXTSTOPLIST     |    このメモリクラークは、 [ストップリスト](../search/configure-and-manage-stopwords-and-stoplists-for-full-text-search.md) 機能の Full-Text エンジンによる割り当てに使用されます。       |
|CACHESTORE_NOTIF     |    このキャッシュストアは、 [クエリ通知](../../connect/ado-net/sql/query-notifications-sql-server.md) 機能による割り当てに使用されます    |
|CACHESTORE_OBJCP     |    このキャッシュストアは、コンパイル済みプラン (CP) でオブジェクトをキャッシュするために使用されます。ストアドプロシージャ、関数、トリガーです。 説明すると、ストアドプロシージャのクエリプランが作成されると、そのプランがこのキャッシュに格納されます。  |
|CACHESTORE_PHDR     |    このキャッシュストアは、クエリのコンパイル時に、ビュー、制約、および既定の algebrizer はツリーの解析中に、一時的なメモリキャッシュに使用されます。 クエリが解析されたら、メモリを解放する必要があります。 例としては、1つのバッチ内に多数のステートメントがあります。1つのバッチに何千もの挿入や更新が含まれている場合、動的に生成される大規模なクエリを含む T-sql バッチ、IN 句に多数の値が含まれます。      |
|CACHESTORE_QDSRUNTIMESTATS     |   このキャッシュストアは [クエリストア](../performance/monitoring-performance-by-using-the-query-store.md)  ランタイムの統計をキャッシュするために使用されます |
|CACHESTORE_SEARCHPROPERTYLIST     |     このキャッシュストアは、 [プロパティリスト](../search/search-document-properties-with-search-property-lists.md) キャッシュの Full-Text エンジンによる割り当てに使用されます  |
|CACHESTORE_SEHOBTCOLUMNATTRIBUTE     |  このキャッシュストアは、キャッシュヒープまたは B-tree (HoBT) 列のメタデータ構造のためにストレージエンジンによって使用されます。      |
|CACHESTORE_SQLCP     |    このキャッシュストアは、プランキャッシュ内のアドホッククエリ、準備されたステートメント、およびサーバー側カーソルをキャッシュするために使用されます。 アドホッククエリは、一般的には、明示的なパラメーター化を行わずにサーバーに送信される言語イベントの T-sql ステートメントです。 準備されたステートメントは、このキャッシュストアも使用します。このキャッシュストアは、 [SQLPrepare ()](../../odbc/reference/syntax/sqlprepare-function.md) /  [sqlexecute](../../odbc/reference/syntax/sqlexecute-function.md) (ODBC) または[SqlCommand](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlcommand.prepare)SqlCommand.ExecuteNonQuery (ADO.NET) のような API 呼び出しを使用してアプリケーションによって送信され、 / [ ](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlcommand.executenonquery) [sp_prepare](../system-stored-procedures/sp-prepare-transact-sql.md) / [sp_execute](../system-stored-procedures/sp-execute-transact-sql.md)または[sp_prepexec](../system-stored-procedures/sp-prepexec-transact-sql.md)システムプロシージャの実行としてサーバーに表示されます。 また、サーバー側カーソルはこのキャッシュストア ([sp_cursoropen](../system-stored-procedures/sp-cursoropen-transact-sql.md)、 [sp_cursorfetch](../system-stored-procedures/sp-cursorfetch-transact-sql.md)、 [sp_cursorclose](../system-stored-procedures/sp-cursorclose-transact-sql.md)) から使用します。    |
|CACHESTORE_STACKFRAMES     |    このキャッシュストアは、スタックフレームに関連する内部 SQL OS 構造の割り当てに使用されます。     |
|CACHESTORE_SYSTEMROWSET     |   このキャッシュストアは、トランザクションのログと復旧に関連する内部構造の割り当てに使用されます。      |
|CACHESTORE_TEMPTABLES     |     このキャッシュストアは [、一時テーブルとテーブル変数のキャッシュ](../databases/tempdb-database.md#performance-improvements-in-tempdb-for-sql-server) に関連する割り当て (プランキャッシュの一部) に使用されます。    |
|CACHESTORE_VIEWDEFINITIONS     |     このキャッシュストアは、クエリ最適化の一部としてビュー定義をキャッシュするために使用されます。    |
|CACHESTORE_XML_SELECTIVE_DG     |     このキャッシュストアは、XML 処理のための XML 構造をキャッシュするために使用されます。    |
|CACHESTORE_XMLDBATTRIBUTE     |     このキャッシュストアは、 [XQuery](../../xquery/xquery-language-reference-sql-server.md)などの xml アクティビティの xml 属性構造をキャッシュするために使用されます。    |
|CACHESTORE_XMLDBELEMENT     |   このキャッシュストアは、 [XQuery](../../xquery/xquery-language-reference-sql-server.md)などの xml アクティビティの xml 要素構造をキャッシュするために使用されます。      |
|CACHESTORE_XMLDBTYPE     |    このキャッシュストアは、XQuery などの XML アクティビティの XML 構造をキャッシュするために使用されます。     |
|CACHESTORE_XPROC     |   このキャッシュストアは、プランキャッシュ内の [拡張ストアドプロシージャ (Xprocs)](../extended-stored-procedures-programming/database-engine-extended-stored-procedures-programming.md) の構造をキャッシュするために使用されます。     |
|MEMORYCLERK_BACKUP     |    このメモリクラークは、 [バックアップ](../../t-sql/statements/backup-transact-sql.md) 機能によるさまざまな割り当てに使用されます。    |
|MEMORYCLERK_BHF     |      このメモリクラークは、クエリ実行中の BLOB (バイナリラージオブジェクト) 管理の割り当てに使用されます (Blob ハンドルのサポート)。  |
|MEMORYCLERK_BITMAP     |     このメモリクラークは、ビットマップフィルター処理のために SQL OS 機能による割り当てに使用されます。    |
|MEMORYCLERK_CSILOBCOMPRESSION     |  このメモリクラークは、[列ストアインデックスのバイナリラージオブジェクト (BLOB) 圧縮](../data-compression/data-compression.md#columnstore-and-columnstore-archive-compression)による割り当てに使用されます    |
|MEMORYCLERK_DRTLHEAP     |    このメモリクラークは、SQL OS 機能による割り当てに使用されます   <br /><br />**適用対象**: [!INCLUDE[sql-server-2019](../../includes/sssql19-md.md)] 以降   |
|MEMORYCLERK_EXPOOL     |    このメモリクラークは、SQL OS 機能による割り当てに使用されます   <br /><br />**適用対象**: [!INCLUDE[sql-server-2019](../../includes/sssql19-md.md)] 以降   |
|MEMORYCLERK_EXTERNAL_EXTRACTORS     |   このメモリクラークは、 [バッチモード](../query-processing-architecture-guide.md#batch-mode-execution) 操作のクエリ実行エンジンによる割り当てに使用されます。   <br /><br />**適用対象**: [!INCLUDE[sql-server-2019](../../includes/sssql19-md.md)] 以降   |
|MEMORYCLERK_FILETABLE     |      このメモリクラークは、 [filetable](../blob/filetables-sql-server.md) 機能によるさまざまな割り当てに使用されます。   |
|MEMORYCLERK_FSAGENT     |      このメモリクラークは、 [FILESTREAM](../blob/filestream-sql-server.md) 機能によるさまざまな割り当てに使用されます。    |
|MEMORYCLERK_FSCHUNKER     |      このメモリクラークは、filestream のチャンクを作成するための [filestream](../blob/filestream-sql-server.md) 機能によるさまざまな割り当てに使用されます。   |
|MEMORYCLERK_FULLTEXT     |     このメモリクラークは Full-Text エンジン構造による割り当てに使用されます。   |
|MEMORYCLERK_FULLTEXT_SHMEM     |   このメモリクラークは、フルテキストデーモンプロセスとの共有メモリ接続に関連する Full-Text エンジン構造によって割り当てに使用されます。      |
|MEMORYCLERK_HADR     |     このメモリクラークは、AlwaysOn 機能によるメモリ割り当てに使用されます     |
|MEMORYCLERK_HOST     |    このメモリクラークは、SQL OS 機能による割り当てに使用されます。     |
|MEMORYCLERK_LANGSVC     |     このメモリクラークは、SQL T-sql ステートメントとコマンドによる割り当て (パーサー、algebrizer はなど) に使用されます。    |
|MEMORYCLERK_LWC     |   このメモリクラークは Full-Text [セマンティック検索](../search/semantic-search-sql-server.md) エンジンによる割り当てに使用されます       |
|MEMORYCLERK_POLYBASE     |    このメモリクラークは、SQL Server 内の [Polybase](../polybase/polybase-guide.md) 機能のメモリ割り当てを追跡します。     |
|MEMORYCLERK_QSRANGEPREFETCH     |  このメモリクラークは、クエリスキャンの範囲プリフェッチのクエリ実行中に割り当てに使用されます。      |
|MEMORYCLERK_QUERYDISKSTORE     |     このメモリクラークは、SQL Server 内のメモリ割り当てを [クエリストア](../performance/monitoring-performance-by-using-the-query-store.md) によって使用されます。    |
|MEMORYCLERK_QUERYDISKSTORE_HASHMAP     |   このメモリクラークは、SQL Server 内のメモリ割り当てを [クエリストア](../performance/monitoring-performance-by-using-the-query-store.md) によって使用されます。      |
|MEMORYCLERK_QUERYDISKSTORE_STATS     |  このメモリクラークは、SQL Server 内のメモリ割り当てを [クエリストア](../performance/monitoring-performance-by-using-the-query-store.md) によって使用されます。      |
|MEMORYCLERK_QUERYPROFILE     |  このメモリクラークは、サーバーの起動時にクエリプロファイルを有効にするために使用されます。    <br /><br />**適用対象**: [!INCLUDE[sql-server-2019](../../includes/sssql19-md.md)] 以降   |
|MEMORYCLERK_RTLHEAP     |    このメモリクラークは、SQL OS 機能による割り当てに使用されます。  <br /><br />**適用対象**: [!INCLUDE[sql-server-2019](../../includes/sssql19-md.md)] 以降   |
|MEMORYCLERK_SECURITYAPI     |    このメモリクラークは、SQL OS 機能による割り当てに使用されます。  <br /><br />**適用対象**: [!INCLUDE[sql-server-2019](../../includes/sssql19-md.md)] 以降   |
|MEMORYCLERK_SERIALIZATION     |     内部でのみ使用されます    |
|MEMORYCLERK_SLOG     |     このメモリクラークは、[高速データベース回復](../accelerated-database-recovery-concepts.md)で sLog (セカンダリインメモリログストリーム) による割り当てに使用されます <br /><br />**適用対象**: [!INCLUDE[sql-server-2019](../../includes/sssql19-md.md)] 以降   |
|MEMORYCLERK_SNI     |     このメモリクラークは、サーバーネットワークインターフェイス (SNI) コンポーネントにメモリを割り当てます。 SNI は、の接続と [TDS](https://docs.microsoft.com/openspecs/windows_protocols/ms-tds/893fcc7e-8a39-4b3c-815a-773b7b982c50) パケットを管理します。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]  |
|MEMORYCLERK_SOSMEMMANAGER     |   このメモリクラークは、SQLOS (SOS) スレッドスケジューリングとメモリおよび i/o 管理の構造体を割り当てます。     |
|MEMORYCLERK_SOSNODE     |     このメモリクラークは、SQLOS (SOS) スレッドスケジューリングとメモリおよび i/o 管理の構造体を割り当てます。    |
|MEMORYCLERK_SOSOS     |     このメモリクラークは、SQLOS (SOS) スレッドスケジューリングとメモリおよび i/o 管理の構造体を割り当てます。    |
|MEMORYCLERK_SPATIAL     |    このメモリクラークは、メモリ割り当てのために [空間データ](../spatial/spatial-data-sql-server.md) コンポーネントによって使用されます。     |
|MEMORYCLERK_SQLBUFFERPOOL     |    このメモリクラークは、一般的に最も大きなメモリコンシューマーの内部 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] データとインデックスページを追跡します。 バッファープールまたはデータキャッシュは、データおよびインデックスページをメモリに読み込んで、データに高速にアクセスできるようにします。 詳細については、「 [バッファー管理](../memory-management-architecture-guide.md#buffer-management)」を参照してください。     |
|MEMORYCLERK_SQLCLR     |     このメモリクラークは [SQLCLR ](../clr-integration/clr-integration-overview.md)による割り当てに使用されます。     |
|MEMORYCLERK_SQLCLRASSEMBLY     |     このメモリクラークは [SQLCLR ](../clr-integration/clr-integration-overview.md) アセンブリの割り当てに使用されます。     |
|MEMORYCLERK_SQLCONNECTIONPOOL     |     このメモリクラークは、サーバー上の情報をキャッシュします。この情報は、クライアントアプリケーションが追跡を維持するためにサーバーに必要な場合があります。 1つの例として、  [sp_prepexecrpc](../system-stored-procedures/sp-prepexecrpc-transact-sql.md)を介して準備ハンドルを作成するアプリケーションがあります。 アプリケーションは、実行後にこれらのハンドルを適切に unprepare (閉じる) 必要があります。  |
|MEMORYCLERK_SQLEXTENSIBILITY     |    このメモリクラークは、で外部の Python または R スクリプトを実行するための [機能拡張フレームワーク](../../machine-learning/concepts/extensibility-framework.md) による割り当てに使用され [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。  <br /><br />**適用対象**: [!INCLUDE[sql-server-2019](../../includes/sssql19-md.md)] 以降   |
|MEMORYCLERK_SQLGENERAL     |   このメモリクラークは、SQL エンジン内の複数のコンシューマーによって使用される可能性があります。 例としては、レプリケーションメモリ、内部デバッグ/診断、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] スタートアップ機能、SQL パーサーの機能、システムインデックスの作成、グローバルメモリオブジェクトの初期化、サーバーとリンクサーバーのクエリ内での OLEDB 接続の作成、サーバーとリンクサーバーのクエリ、サーバー側のプロファイラーのトレース、計算列のコンパイル、並列化構造のメモリ、一部の XML 機能のメモリ     |
|MEMORYCLERK_SQLHTTP     |    非推奨     |
|MEMORYCLERK_SQLLOGPOOL     |   このメモリクラークは、ログプールによって使用され [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。  ログプールは、トランザクションログの読み取り時のパフォーマンスを向上させるために使用されるキャッシュです。 具体的には、複数のログ読み取り中にログキャッシュの使用率を向上させ、ディスク i/o ログの読み取りを減らし、ログスキャンを共有できるようにします。 ログプールの主要なコンシューマーは、AlwaysOn (変更のキャプチャと送信)、再実行マネージャー、データベースの復旧-分析/やり直し/取り消し、トランザクションランタイムロールバック、レプリケーション/CDC、バックアップ/復元です。    |
|MEMORYCLERK_SQLOPTIMIZER     |     このメモリクラークは、クエリのコンパイルのさまざまなフェーズでメモリ割り当てに使用されます。 使用例として、クエリの最適化、インデックス統計マネージャー、ビュー定義のコンパイル、ヒストグラムの生成などがあります。   |
|MEMORYCLERK_SQLQERESERVATIONS     |     このメモリクラークは、クエリの実行中に並べ替え操作とハッシュ操作を実行するためにクエリに割り当てられたメモリであるメモリ許可割り当てに使用されます。 クエリ実行の予約 (メモリ許可) の詳細については、こちらの[ブログ](https://techcommunity.microsoft.com/t5/sql-server-support/memory-grants-the-mysterious-sql-server-memory-consumer-with/ba-p/333994)を参照してください。     |
|MEMORYCLERK_SQLQUERYCOMPILE     |    このメモリクラークは、クエリのコンパイル中にメモリを割り当てるためにクエリオプティマイザーによって使用されます。   |
|MEMORYCLERK_SQLQUERYEXEC     |    このメモリクラークは、次の領域の割り当てに使用されます。 [バッチモード処理](../query-processing-architecture-guide.md#batch-mode-execution)、 [並列クエリ](../query-processing-architecture-guide.md#parallel-query-processing) の実行、クエリの実行コンテキスト、 [空間インデックステセレーション](../spatial/spatial-indexes-overview.md#tessellation)、並べ替えとハッシュの操作 (テーブルの並べ替え、ハッシュテーブル)、一部の dvm 処理、 [更新統計](../statistics/update-statistics.md) の実行    |
|MEMORYCLERK_SQLQUERYPLAN     |     このメモリクラークは、 [ヒープ](../indexes/heaps-tables-without-clustered-indexes.md) ページ管理、 [DBCC CHECKTABLE](../../t-sql/database-console-commands/dbcc-checktable-transact-sql.md) 割り当て、および [sp_cursor * ストアドプロシージャ](../system-stored-procedures/cursor-stored-procedures-transact-sql.md) の割り当てによる割り当てに使用されます。   |
|MEMORYCLERK_SQLSERVICEBROKER     |   このメモリクラークは [SQL Server Service Broker](../../database-engine/configure-windows/sql-server-service-broker.md) メモリ割り当てによって使用されます。       |
|MEMORYCLERK_SQLSERVICEBROKERTRANSPORT     |     このメモリクラークは、 [SQL Server Service Broker](../../database-engine/configure-windows/sql-server-service-broker.md) トランスポートメモリの割り当てによって使用されます。    |
|MEMORYCLERK_SQLSLO_OPERATIONS     |      このメモリクラークは、パフォーマンスの統計情報を収集するために使用されます。 <br /><br />**適用対象**:  [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]   |
|MEMORYCLERK_SQLSOAP     |     非推奨    |
|MEMORYCLERK_SQLSOAPSESSIONSTORE     |    非推奨     |
|MEMORYCLERK_SQLSTORENG     |   このメモリクラークは、複数のストレージエンジンコンポーネントによる割り当てに使用されます。 コンポーネントの例としては、データベースファイルの構造、データベーススナップショットレプリカファイルマネージャー、デッドロックモニター、DBTABLE 構造体、ログマネージャーの構造、いくつかの tempdb のバージョン管理の構造、サーバー起動時の機能、並列クエリの子スレッドの実行コンテキストなどがあります。      |
|MEMORYCLERK_SQLTRACE     |     このメモリクラークは、サーバー側の [SQL トレース](../sql-trace/sql-trace.md) のメモリ割り当てに使用されます。     |
|MEMORYCLERK_SQLUTILITIES     |    このメモリクラークは SQL Server 内の複数のアロケーターで使用できます。 例として、バックアップと復元、ログ配布、データベースミラーリング、DBCC コマンド、サーバー側の BCP コード、一部のクエリの並列処理、ログスキャンバッファーなどがあります。    |
|MEMORYCLERK_SQLXML     |     このメモリクラークは、XML 操作の実行時にメモリ割り当てに使用されます。    |
|MEMORYCLERK_SQLXP     |     このメモリクラークは、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] [拡張ストアドプロシージャ](../extended-stored-procedures-reference/database-engine-extended-stored-procedures-reference.md)を呼び出すときにメモリ割り当てに使用されます。    |
|MEMORYCLERK_SVL     |    このメモリクラークは、内部 SQL OS 構造の割り当てに使用されます。 |
|MEMORYCLERK_TEST     |    内部でのみ使用されます   |
|MEMORYCLERK_UNITTEST     |      内部でのみ使用されます  |
|MEMORYCLERK_WRITEPAGERECORDER     |    このメモリクラークは、書き込みページレコーダーによる割り当てに使用されます。   |
|MEMORYCLERK_XE     |    このメモリクラークは、 [拡張イベント](../extended-events/extended-events.md) のメモリ割り当てに使用されます      |
|MEMORYCLERK_XE_BUFFER     |      このメモリクラークは、 [拡張イベント](../extended-events/extended-events.md) のメモリ割り当てに使用されます   |
|MEMORYCLERK_XLOG_SERVER     |   このメモリクラークは、SQL Azure データベースのログファイル管理に使用される Xlog による割り当てに使用されます。   <br /><br />**適用対象**:  [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] |
|MEMORYCLERK_XTP     |    このメモリクラークは、 [インメモリ OLTP](../in-memory-oltp/in-memory-oltp-in-memory-optimization.md) のメモリ割り当てに使用されます。     |
|OBJECTSTORE_LBSS     |    このオブジェクトストアは、式の一時的な Lob 変数、パラメーター、および中間結果を割り当てるために使用されます。 このストアを使用する例としては、 [テーブル値パラメーター](../../connect/ado-net/sql/table-valued-parameters.md) (tvp) が挙げられます。 この領域の修正の詳細については、 [サポート技術情報の記事 4468102](https://support.microsoft.com/topic/kb4468102-fix-excessive-memory-usage-when-you-trace-rpc-events-that-involve-table-valued-parameters-in-sql-server-2016-and-2017-c68aa214-26f1-98de-6b4d-c7dcad82dbd4) およびサポート技術情報の  [記事 4051359](https://support.microsoft.com/topic/kb4051359-fix-sql-server-runs-out-of-memory-when-table-valued-parameters-are-captured-in-extended-events-sessions-in-sql-server-2016-even-if-collecting-statement-or-data-stream-isn-t-enabled-a3639efa-0618-82a8-f6b1-8cdcba29ce6d) を参照してください。     |
|OBJECTSTORE_LOCK_MANAGER     |      このメモリクラークは、の [ロックマネージャー](../sql-server-transaction-locking-and-row-versioning-guide.md#Lock_Engine) によって行われた割り当てを追跡 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] します。   |
|OBJECTSTORE_SECAUDIT_EVENT_BUFFER     |   このオブジェクトストアは [SQL Server 監査](../security/auditing/sql-server-audit-database-engine.md) メモリ割り当てに使用されます。        |
|OBJECTSTORE_SERVICE_BROKER     |     このオブジェクトストアは、によって使用され [Service Broker](../../database-engine/configure-windows/sql-server-service-broker.md)    |
|OBJECTSTORE_SNI_PACKET     |     このオブジェクトストアは、接続を管理するサーバーネットワークインターフェイス (SNI) コンポーネントによって使用されます。    |
|OBJECTSTORE_XACT_CACHE     |    このオブジェクトストアは、トランザクション情報をキャッシュするために使用されます。     |
|USERSTORE_DBMETADATA     |      このオブジェクトストアはメタデータ構造に使用されます     |
|USERSTORE_OBJPERM     |     このストアは、オブジェクトのセキュリティ/アクセス許可を追跡する構造体に使用されます     |
|USERSTORE_QDSSTMT     |  このキャッシュストアは、 [クエリストア](../performance/monitoring-performance-by-using-the-query-store.md)  ステートメントをキャッシュするために使用されます       |
|USERSTORE_SCHEMAMGR     |    スキーママネージャーのキャッシュでは、データベースオブジェクトに関するさまざまな種類のメタデータ情報がメモリに格納されます (例: テーブル)。 このストアの一般的なユーザーには、テーブル、一時プロシージャ、テーブル変数、テーブル値パラメーター、作業テーブル、作業ファイル、バージョンストアなどのオブジェクトを含む tempdb データベースを使用できます。  |
|USERSTORE_SXC     |    このユーザーストアは、すべての [RPC](https://docs.microsoft.com/openspecs/windows_protocols/ms-tds/619c43b6-9495-4a58-9e49-a4950db245b3) パラメーターを格納する割り当てに使用されます。     |
|USERSTORE_TOKENPERM     |    TokenAndPermUserStore は、セキュリティコンテキスト、ログイン、ユーザー、アクセス許可、および監査のセキュリティエントリを追跡する単一の SOS ユーザーストアです。 これらのオブジェクトを格納するために、複数のハッシュテーブルが割り当てられます。    |

## <a name="see-also"></a>参照  

 [SQL Server オペレーティングシステム関連の動的管理ビュー &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sql-server-operating-system-related-dynamic-management-views-transact-sql.md)   
 [sys.dm_os_sys_info &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-os-sys-info-transact-sql.md)   
 [sys.dm_exec_query_memory_grants &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-query-memory-grants-transact-sql.md)   
 [sys.dm_exec_requests &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql.md)   
 [sys.dm_exec_query_plan &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-query-plan-transact-sql.md)   
 [sys.dm_exec_sql_text &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-sql-text-transact-sql.md)  
  
