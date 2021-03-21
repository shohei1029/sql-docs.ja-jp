---
title: メモリ管理アーキテクチャ ガイド | Microsoft Docs
description: 以前のバージョンでのメモリ管理の変更など、SQL Server のメモリ管理アーキテクチャについて説明します。
ms.custom: ''
ms.date: 01/09/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: supportability
ms.topic: conceptual
helpviewer_keywords:
- guide, memory management architecture
- memory management architecture guide
- PMO
- Partitioned Memory Objects
- cmemthread
- AWE
- SPA, Single Page Allocator
- MPA, Multi Page Allocator
- memory allocation, SQL Server
- memory pressure, SQL Server
- stack size, SQL Server
- buffer manager, SQL Server
- buffer pool, SQL Server
- resource monitor, SQL Server
ms.assetid: 7b0d0988-a3d8-4c25-a276-c1bdba80d6d5
author: rothja
ms.author: jroth
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 36beaf966ba070f7055c1619ae650a16bb13563b
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104751452"
---
# <a name="memory-management-architecture-guide"></a>メモリ管理アーキテクチャ ガイド

[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

## <a name="windows-virtual-memory-manager"></a>Windows 仮想メモリ マネージャー  
Windows 仮想メモリ マネージャー (VMM) は、使用可能な物理メモリにコミット済みのアドレス空間をマップします。  
  
さまざまなオペレーティング システムでサポートされている物理メモリ量の詳細については、Windows のマニュアルの「[Windows のリリース別のメモリ制限](/windows/desktop/Memory/memory-limits-for-windows-releases)」を参照してください。  
  
仮想メモリ システムでは、仮想メモリと物理メモリの比率が 1:1 を超えるような物理メモリの設定を許可しています。 その結果、さまざまな物理メモリ構成のコンピューターで大規模なプログラムを実行できます。 しかし、すべてのプロセスの平均ワーキング セットを合わせた容量よりもはるかに大きな仮想メモリを使用すると、パフォーマンスが低下する可能性があります。 

## <a name="sql-server-memory-architecture"></a>SQL Server のメモリ アーキテクチャ

[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] では、メモリの確保と解放が必要に応じて動的に行われます。 通常、管理者が [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]に割り当てるメモリ量を指定する必要はありませんが、このオプションは一部の環境で必要になるのでまだ存在しています。

ディスクの読み書きはコンピューター操作の中でも特にリソースを消費するので、どのようなデータベース ソフトウェアでも、ディスク I/O を最小限に抑えることを主な設計目標としています。 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] では、データベースから読み取ったページを保持するバッファー プールがメモリ内に構築されます。 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] のコードの大部分は、ディスクとバッファー プールの間の物理的な読み書きの回数が最も少なくなるように記述されています。 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] では、次の 2 つの目標のバランスを取ることを目指しています。

* システム全体のメモリ不足を防ぐため、バッファー プールが大きくなりすぎないようにする。
* バッファー プールのサイズを最大にして、データベース ファイルの物理 I/O を最小限に抑える。

> [!NOTE]
> 負荷の高いシステムでは、実行に大量のメモリを必要とする大きなクエリが必要最低限のメモリ量を確保できず、メモリ リソースの待機中にタイムアウト エラーが発生することがあります。 これを解決するには、 [query wait オプション](../database-engine/configure-windows/configure-the-query-wait-server-configuration-option.md)の値を増やします。 並列クエリの場合は、 [max degree of parallelism オプション](../database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option.md)の値を減らすことを検討してください。
 
> [!NOTE]
> メモリ不足で負荷の高いシステムでは、クエリ プランにマージ結合、並べ替え、およびビットマップを使用したクエリが含まれていると、クエリがビットマップに必要な最低限のメモリ量を確保できなかった場合に、ビットマップが削除されることがあります。 この動作がクエリのパフォーマンスに影響を与える場合があります。そのために並べ替え処理がメモリに収まらなくなったときに、tempdb データベース内の作業テーブルの使用率が増加し、tempdb データベースのサイズが大きくなります。 この問題を解決するには、物理メモリを追加するか、より実行速度の速い別のクエリ プランを使用するようにクエリをチューニングします。
 
### <a name="providing-the-maximum-amount-of-memory-to-ssnoversion"></a>[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] に対する最大メモリ容量の指定

AWE および Locked Pages in Memory 特権を使用して、 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] データベース エンジンに次の容量のメモリを指定できます。 

> [!NOTE]
> 次の表には、現在利用できない 32 ビット バージョンの列が含まれています。

|メモリ ポリシー|32 ビット <sup>1</sup> |64 ビット|
|-------|-------|-------| 
|コンベンショナル メモリ |すべての [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] エディション。 プロセス仮想アドレス空間制限まで: <br>- 2 GB<br>- 3 GB (/3 gb のブート パラメーターを使用する場合) <sup>2</sup> <br>- 4 GB (WOW64 の場合) <sup>3</sup> |すべての [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] エディション。 プロセス仮想アドレス空間制限まで: <br>- 7 TB (IA64 アーキテクチャを使用する場合) (IA64 は [!INCLUDE[ssSQL11](../includes/sssql11-md.md)] 以降ではサポートされません)<br>- オペレーティング システムの最大容量 (x64 アーキテクチャを使用する場合) <sup>4</sup>
|AWE メカニズム ( [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] で 32 ビット プラットフォームのプロセス仮想アドレス空間制限を超えることを許可する) |[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] Standard、Enterprise、および Developer エディション:バッファー プールは、最大 64 GB のメモリにアクセスできます。|該当なし <sup>5</sup> |
|Lock Pages in Memory オペレーティング システム (OS) 特権 (物理メモリのロックを許可し、ロックされたメモリの OS ページングを回避する)<sup>6</sup> |[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] Standard、Enterprise、および Developer エディション:AWE メカニズムを使用する [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] プロセスで必要です。 AWE メカニズムによって割り当てられたメモリは、ページ アウトできません。 <br> AWE を有効にせずにこの特権を許可しても、サーバーに対する影響はありません。 | 必要なときにのみ、具体的には、sqlservr プロセスがページ アウトされているという兆候があるときにのみ使用します。その場合、次のようなエラー 17890 がエラー ログに報告されます。`A significant part of sql server process memory has been paged out. This may result in a performance degradation. Duration: #### seconds. Working set (KB): ####, committed (KB): ####, memory utilization: ##%.`|

<sup>1</sup>[!INCLUDE[ssSQL14](../includes/sssql14-md.md)]から続けて 32 ビット バージョンを利用することはできません。  
<sup>2</sup> /3gb は、オペレーティング システムのブート パラメーターです。  
<sup>3</sup> WOW64 (Windows on Windows 64) は、32 ビットの [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] が 64 ビットのオペレーティング システムで実行される場合のモードです。  
<sup>4</sup> [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] Standard Edition は、最大 128 GB をサポートします。 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] Enterprise Edition は、オペレーティング システムの最大容量をサポートします。  
<sup>5</sup> sp_configure awe enabled オプションは、64 ビットの [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]に存在しますが、無視されます。    
<sup>6</sup> Lock Pages in Memory (LPIM) 特権が許可されている (AWE サポートの場合は 32 ビット、AWE そのものでは 64 ビットで) 場合は、サーバーの最大メモリも設定することをお勧めします。 LPIM の詳細については、「[サーバー メモリに関するサーバー構成オプション](../database-engine/configure-windows/server-memory-server-configuration-options.md#lock-pages-in-memory-lpim)」を参照してください。

> [!NOTE]
> 32 ビット オペレーティング システム上では、古いバージョンの [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] を実行できます。 32 ビットのオペレーティング システム上で 4 ギガバイト (GB) を超えるメモリにアクセスするには、Address Windowing Extensions (AWE) がメモリを管理する必要がありました。 これは、64 ビット オペレーティング システムで [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] を実行するときには必要ありません。 AWE の詳細については、[!INCLUDE[ssKatmai](../includes/ssKatmai-md.md)] ドキュメントの「[プロセス アドレス空間](/previous-versions/sql/sql-server-2008-r2/ms189334(v=sql.105))」および「[大規模データベースのメモリ管理](/previous-versions/sql/sql-server-2008-r2/ms191481(v=sql.105))」をご覧ください。   

<a name="changes-to-memory-management-starting-2012-11x-gm"></a>

## <a name="changes-to-memory-management-starting-with-sssql11"></a>[!INCLUDE[ssSQL11](../includes/sssql11-md.md)] 以降のメモリ管理の変更点

以前のバージョンの [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] ([!INCLUDE[ssVersion2005](../includes/ssversion2005-md.md)]、[!INCLUDE[ssKatmai](../includes/ssKatmai-md.md)]、[!INCLUDE[ssKilimanjaro](../includes/ssKilimanjaro-md.md)]) では、次の 5 つの異なるメカニズムを利用してメモリが割り当てられていました。
-  **SPA (Single-Page Allocator/単一ページ アロケータ)** 。[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] プロセスで 8KB 以下のメモリ割り当てのみ含む。 構成オプションの *max server memory (MB)* と *min server memory (MB)* によって、SPA が利用する物理メモリの上限が決められていました。 同時にバッファー プールが SPA のメカニズムであり、これが単一ページ割り当てを最も多く利用していました。
-  **MPA (Multi-Page Allocator/複数ページ アロケータ)** 。8KB より多くを要求するメモリ割り当て用。
-  **CLR アロケータ**。CLR 初期化中に作成される SQL CLR ヒープとそのグローバル割り当てを含む。
-  [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] プロセスの **[スレッド スタック](../relational-databases/memory-management-architecture-guide.md#stacksizes)** のメモリ割り当て。
-  **DWA (Direct Windows allocations/直接 Windows 割り当て)** 。Windows に直接行われるメモリ割り当て要求。 モジュールによって行われ、[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] プロセスに読み込まれる、Windows のヒープ使用量と直接仮想割り当てが含まれます。 このようなメモリ割り当ての例としては、たとえば、拡張ストアド プロシージャ DLL からの割り当て、オートメーション プロシージャ (sp_OA 呼び出し) で作成されたオブジェクト、リンク サーバー プロバイダーからの割り当てがあります。

[!INCLUDE[ssSQL11](../includes/sssql11-md.md)] 以降、SPA、MPA、CLR 割り当てがすべて統合され、 **"あらゆるサイズの" ページ アロケータ** になります。これは、構成オプションの *max server memory (MB)* と *min server memory (MB)* によって制御されるメモリ上限に含まれます。 この変更によって、[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] メモリ マネージャーを通過するすべてのメモリ要件において、より正確にサイズを調整できるようになりました。 

> [!IMPORTANT]
> [!INCLUDE[ssSQL11](../includes/sssql11-md.md)] ～ [!INCLUDE[ssCurrent](../includes/sscurrent-md.md)] にアップグレードしたら、現在の *max server memory (MB)* 構成と *min server memory (MB)* 構成を注意深く見直してください。 これは、[!INCLUDE[ssSQL11](../includes/sssql11-md.md)] 以降は、以前のバージョンに比べ、このような構成に含まれるメモリ割り当てが多くなっているためです。 この変更は [!INCLUDE[ssSQL11](../includes/sssql11-md.md)] と [!INCLUDE[ssSQL14](../includes/sssql14-md.md)] の 32 ビット バージョンと 64 ビット バージョン、[!INCLUDE[sssql15-md](../includes/sssql16-md.md)] ～ [!INCLUDE[ssCurrent](../includes/sscurrent-md.md)] の 64 ビット バージョンに適用されます。

次の表は、メモリ割り当ての種類とそれを制御する構成オプションである *max server memory (MB)* と *min server memory (MB)* についてまとめたものです。

|メモリ割り当ての種類| [!INCLUDE[ssVersion2005](../includes/ssversion2005-md.md)]、[!INCLUDE[ssKatmai](../includes/ssKatmai-md.md)]、[!INCLUDE[ssKilimanjaro](../includes/ssKilimanjaro-md.md)]| [!INCLUDE[ssSQL11](../includes/sssql11-md.md)] 以降|
|-------|-------|-------|
|単一ページ割り当て|はい|はい。"あらゆるサイズの" ページ割り当てに統合。|
|複数ページ割り当て|いいえ|はい。"あらゆるサイズの" ページ割り当てに統合。|
|CLR 割り当て|いいえ|はい|
|スレッド スタック メモリ|いいえ|いいえ|
|Windows からの直接割り当て|いいえ|いいえ|

[!INCLUDE[ssSQL11](../includes/sssql11-md.md)] 以降、[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] は、max server memory 設定に指定されている値より多いメモリを割り当てる場合があります。 そのような動作は、**_Total Server Memory (KB)_** の値が (max server memory によって指定される) **_Target Server Memory (KB)_** の設定に既に到達しているときに発生することがあります。 メモリの断片化によって、複数ページ メモリ要求 (8 KB 超) を満たすだけの連続した空き容量がない場合、[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] はメモリ要求を拒否せず、オーバーコミットを実行できます。 

この割り当ての実行直後、バックグラウンド タスクの *リソース モニター* がすべてのメモリ コンシューマーに信号を送り、割り当てられているメモリの解放を求め、*Total Server Memory (KB)* が *Target Server Memory (KB)* 仕様を下回るようにします。 そのため、[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] メモリ使用量が短い時間だけ max server memory 設定を超えることがあります。 このような状況では、*Total Server Memory (KB)* パフォーマンス カウンター読み取り値が max server memory 設定と *Target Server Memory (KB)* 設定を超えます。

この動作は通常、次の操作中に観察されます。 
-  大規模な列ストア インデックス クエリ。
-  列ストア インデックスの (再) ビルド。大量のメモリを使用し、ハッシュ操作とソート操作を実行します。
-  大量のメモリ バッファーを必要とするバックアップ操作。
-  大量の入力パラメーターを格納する必要があるトレース操作。

<a name="#changes-to-memory-management-starting-with-includesssql11includessssql11-mdmd"></a>
## <a name="changes-to-memory_to_reserve-starting-with-sssql11"></a>[!INCLUDE[ssSQL11](../includes/sssql11-md.md)] 以降の "memory_to_reserve" の変更点
以前のバージョンの SQL Server ([!INCLUDE[ssVersion2005](../includes/ssversion2005-md.md)]、[!INCLUDE[ssKatmai](../includes/ssKatmai-md.md)]、[!INCLUDE[ssKilimanjaro](../includes/ssKilimanjaro-md.md)]) では、[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] メモリ マネージャーは、**複数ページ アロケータ (MPA)** 、**CLR アロケータ**、SQL Server プロセスの **スレッド スタック** のメモリ割り当て、**直接 Windows 割り当て (DWA)** で使用するために、プロセス VAS (Virtual Address Space/仮想アドレス空間) の一部を予約しました。 仮想アドレス空間のこの部分は、"Mem-To-Leave" または "non-Buffer Pool" 領域とも呼ばれています。

このような割り当てのために予約される仮想アドレス空間は、構成オプション _**memory\_to\_reserve**_ によって決定されます。 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] で使用される初期値は 256 MB です。 既定値をオーバーライドするには、スタートアップ パラメーターの [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] *-g* を使用します。 スタートアップ パラメーター *-g* の詳細については、ドキュメント ページの「[データベース エンジン サービスのスタートアップ オプション](../database-engine/configure-windows/database-engine-service-startup-options.md)」を参照してください。

[!INCLUDE[ssSQL11](../includes/sssql11-md.md)] 以降、新しい "あらゆるサイズの" ページ アロケータは 8 KB を超える割り当ても処理するため、*memory_to_reserve* 値には複数ページ割り当てが含まれません。 この変更を除き、他のすべてはこの構成オプションと引き続き同じになります。

次の表は、メモリ割り当ての種類とそれが適用される、[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] プロセスの仮想アドレス空間の *memory_to_reserve* 領域についてまとめたものです。

|メモリ割り当ての種類| [!INCLUDE[ssVersion2005](../includes/ssversion2005-md.md)]、[!INCLUDE[ssKatmai](../includes/ssKatmai-md.md)]、[!INCLUDE[ssKilimanjaro](../includes/ssKilimanjaro-md.md)]| [!INCLUDE[ssSQL11](../includes/sssql11-md.md)] 以降|
|-------|-------|-------|
|単一ページ割り当て|いいえ|いいえ。"あらゆるサイズの" ページ割り当てに統合。|
|複数ページ割り当て|はい|いいえ。"あらゆるサイズの" ページ割り当てに統合。|
|CLR 割り当て|はい|はい|
|スレッド スタック メモリ|はい|はい|
|Windows からの直接割り当て|はい|はい|

## <a name="dynamic-memory-management"></a><a name="dynamic-memory-management"></a> 動的メモリ管理
[!INCLUDE[ssDEnoversion](../includes/ssdenoversion-md.md)] の既定のメモリ管理動作では、システムでメモリ不足を発生させることなく、必要な量のメモリを獲得します。 [!INCLUDE[ssDEnoversion](../includes/ssdenoversion-md.md)]では、Microsoft Windows の Memory Notification API を使用してこれを実現しています。

メモリを動的に使用する場合、 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] はシステムに定期的にクエリして、メモリの空き容量を確認します。 このようにメモリの空き容量を維持することによって、オペレーティング システム (OS) のページングが防止されます。 空きメモリが少ない場合、 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] は OS に対してメモリを解放します。 空きメモリが多い場合、 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] はより多くのメモリを割り当てることができます。 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] によってメモリが追加されるのは、ワークロードが高いためにメモリを増やす必要がある場合だけです。アクティブでないサーバーの仮想アドレス空間のサイズは増えません。  
  
**[max server memory](../database-engine/configure-windows/server-memory-server-configuration-options.md)** は、[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] のメモリ割り当て、コンパイル メモリ、すべてのキャッシュ (バッファー プールを含む)、[クエリ実行メモリ許可](#effects-of-min-memory-per-query)、[ロック マネージャー メモリ](#memory-used-by-sql-server-objects-specifications)、CLR <sup>1</sup> メモリ (基本的に、 **[sys.dm_os_memory_clerks](../relational-databases/system-dynamic-management-views/sys-dm-os-memory-clerks-transact-sql.md)** で見つかったメモリ クラーク) を制御します。 

<sup>1</sup>[!INCLUDE[ssSQL11](../includes/sssql11-md.md)] 以降、CLR メモリは max_server_memory 割り当ての下で管理されます。

次のクエリでは、現在割り当てられているメモリに関する情報を返します。  
  
```sql  
SELECT 
  physical_memory_in_use_kb/1024 AS sql_physical_memory_in_use_MB, 
    large_page_allocations_kb/1024 AS sql_large_page_allocations_MB, 
    locked_page_allocations_kb/1024 AS sql_locked_page_allocations_MB,
    virtual_address_space_reserved_kb/1024 AS sql_VAS_reserved_MB, 
    virtual_address_space_committed_kb/1024 AS sql_VAS_committed_MB, 
    virtual_address_space_available_kb/1024 AS sql_VAS_available_MB,
    page_fault_count AS sql_page_fault_count,
    memory_utilization_percentage AS sql_memory_utilization_percentage, 
    process_physical_memory_low AS sql_process_physical_memory_low, 
    process_virtual_memory_low AS sql_process_virtual_memory_low
FROM sys.dm_os_process_memory;  
```  
 
<a name="stacksizes"></a> スレッド スタック <sup>1</sup>のメモリ、CLR <sup>2</sup>、拡張プロシージャ .dll ファイル、分散クエリで参照される OLE DB プロバイダー、[!INCLUDE[tsql](../includes/tsql-md.md)] ステートメントで参照されるオートメーション オブジェクト、非 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] DLL で割り当てられるメモリは max server memory で **制御されません**。

<sup>1</sup> 現在のホストで関連付けられている所与の CPU 数に対して計算される既定のワーカー スレッドについては、ドキュメント ページの「[max worker threads サーバー構成オプションの構成](../database-engine/configure-windows/configure-the-max-worker-threads-server-configuration-option.md)」を参照してください。 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] スタックのサイズは次のようになります。

|SQL Server アーキテクチャ|OS アーキテクチャ|スタック サイズ|  
|--------------------|----------------------|----------------------|
|x86 (32 ビット)|x86 (32 ビット)|512 KB|
|x86 (32 ビット)|x64 (64 ビット)|768 KB| 
|x64 (64 ビット)|x64 (64 ビット)|2048 KB|
|IA64 (Itanium)|IA64 (Itanium)|4096 KB|

<sup>2</sup>[!INCLUDE[ssSQL11](../includes/sssql11-md.md)] 以降、CLR メモリは max_server_memory 割り当ての下で管理されます。

[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] では、メモリ通知 API **QueryMemoryResourceNotification** を使用して、いつ [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] Memory Manager がメモリの割り当てまたは解放を行うことができるかを判断します。  

[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] を起動すると、システムの物理メモリの量、サーバー スレッドの数、さまざまな起動パラメーターなど、数多くのパラメーターに基づいてバッファー プール用の仮想アドレス空間のサイズが計算されます。 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] では、計算された量のプロセス仮想アドレス空間をバッファー プール用に予約しますが、現在の負荷に必要な量だけ物理メモリを獲得 (コミット) します。

インスタンスでは、ワークロードのサポートに必要なメモリを獲得し続けます。 多くのユーザーが接続してクエリを実行すると、 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] では要求に応じて追加の物理メモリを獲得します。 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] インスタンスでは、max server memory の割当量に達するか、または OS によって余分な空きメモリがなくなったことが示されるまで、物理メモリを獲得し続けます。獲得したメモリの量が min server memory 設定よりも多く、OS によって空きメモリの不足が示されると、メモリが解放されます。 

[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]のインスタンスが動作しているコンピューター上で他のアプリケーションを起動すると、メモリを消費し、物理メモリの空き領域が [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] の目標よりも少なくなります。 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] のインスタンスでは、メモリの消費を調整します。 他のアプリケーションが停止され、使用可能なメモリが増えると、 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] のインスタンスはメモリ割り当てのサイズを大きくします。 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] は、数 MB のメモリの解放および獲得を毎秒行うことができるため、メモリ割り当ての変更に迅速に対応できます。

## <a name="effects-of-min-and-max-server-memory"></a>最小および最大サーバー メモリの効果
構成オプションの *min server memory* と *max server memory* によって、[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] データベース エンジンのバッファー プールとその他のキャッシュで使用されるメモリ量の上限と下限が決められます。 バッファー プールは、min server memory に指定されたメモリ容量をすぐには獲得しません。 バッファー プールは、初期化に必要なメモリのみで起動します。 [!INCLUDE[ssDEnoversion](../includes/ssdenoversion-md.md)]のワークロードが増えるにしたがって、そのワークロードに対応するために必要なメモリを獲得し続けます。 バッファー プールは、min server memory で指定しされたメモリ容量に達するまでは獲得したメモリを解放しません。 メモリ容量が min server memory に達すると、バッファー プールは標準アルゴリズムを使用して、必要に応じてメモリ容量を獲得または解放します。 唯一の違いは、バッファー プールはそのメモリ割り当てを min server memory の値より少ないメモリ容量にはせず、max server memory の値より多いメモリ容量は獲得しないということです。

> [!NOTE]
> プロセスとしての[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] は、max server memory オプションで指定された容量を超えるメモリを獲得します。 内部コンポーネントも外部コンポーネントも、バッファー プール外にメモリ容量を割り当てることができます。この場合は、メモリ容量が余分に消費されますが、通常、[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] によって消費されるメモリ容量の最大部分は、バッファー プールに割り当てられたメモリ容量が占めます。

[!INCLUDE[ssDEnoversion](../includes/ssdenoversion-md.md)]が獲得するメモリ容量は、そのインスタンスのワークロードに完全に依存します。 あまり多くの要求を処理しない [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] のインスタンスでは、まったく min server memory に達しないこともあります。

min server memory と max server memory の両方に同じ値を指定した場合、[!INCLUDE[ssDEnoversion](../includes/ssdenoversion-md.md)] に割り当てられたメモリがその値に達すると、[!INCLUDE[ssDEnoversion](../includes/ssdenoversion-md.md)] はバッファー プールのためにメモリを動的に解放/獲得することを停止します。

他のアプリケーションが頻繁に停止または起動されるコンピューター上で [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] のインスタンスが動作している場合、 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] のインスタンスによるメモリの割り当てと解放により、他のアプリケーションの起動時間が遅くなることがあります。 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] が、1 つのコンピューター上で動作している複数のサーバー アプリケーションのうちの 1 つであるときも、 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]に割り当てるメモリ量をシステム管理者が制御しなければならない場合があります。 このような場合には、min server memory オプションと max server memory オプションを使用して、 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] が使用するメモリ量を制御できます。 **min server memory** と **max server memory** は MB 単位で指定されます。 詳細については、 [サーバー メモリ構成オプション](../database-engine/configure-windows/server-memory-server-configuration-options.md)の設定を参照してください。

## <a name="memory-used-by-sql-server-objects-specifications"></a>SQL Server オブジェクトの仕様で使用されるメモリ
次の一覧で、 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]の各オブジェクトによって使用されるおおよそのメモリ量について説明します。 表示されている金額は概算であり、環境とオブジェクトの作成方法によって異なります。

* ロック (ロック マネージャーにより保守管理):64 バイト + (32 バイト * 所有者数)   
* ユーザー接続:約 (3 \* network_packet_size + 94 kb)    

**ネットワーク パケット サイズ** は、アプリケーションと [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] データベース エンジン間の通信に使用される表形式のデータ スキーム (TDS) パケットのサイズです。 既定のパケット サイズは 4 KB であり、network packet size 構成オプションによって制御されます。

複数のアクティブな結果セット (MARS) が有効になっている場合、ユーザー接続で使用されるメモリは約 (3 + 3 \*num_logical_connections)\* network_packet_size + 94 KB です。

## <a name="effects-of-min-memory-per-query"></a>min memory per query の効果
*min memory per query* 構成オプションでは、クエリの実行用に割り当てる最小メモリ容量 (KB 単位) を確定します。 これは、最小メモリ許可とも呼ばれます。 すべてのクエリは、実行を開始するためには、要求された最小メモリをセキュリティで保護できるようになるまで、または query wait サーバー構成オプションで指定された値を超えるまで、待機する必要があります。 このシナリオで累積される待機の種類は、RESOURCE_SEMAPHORE です。

> [!IMPORTANT]
> 稼働率が非常に高いシステムでは特に、min memory per query サーバー構成オプションの値を高く設定しすぎないでください。そうしないと、次の状況を招く可能性があります。         
> - メモリ リソースの競合が増加します。         
> - 実行時に必要なメモリがこの構成より少ない場合でも、すべての単一クエリのメモリ量を増やすことで、コンカレンシーが低下します。    
>    
> この構成の使い方の推奨事項については、「[min memory per query サーバー構成オプションの構成](../database-engine/configure-windows/configure-the-min-memory-per-query-server-configuration-option.md#Recommendations)」を参照してください。

### <a name="memory-grant-considerations"></a><a name="memory-grant-considerations"></a>メモリ許可に関する考慮事項
**行モード実行** の場合は、いかなる状況でも初期のメモリ許可を超過することはありません。 **ハッシュ** 操作または **並べ替え** 操作を実行するために、初期のメモリ許可より多くのメモリを必要とする場合、ディスクへの書き込みが行われます。 ハッシュ操作では TempDB 内の作業ファイルによって書き込みがサポートされます。一方、並べ替え操作では[作業テーブル](../relational-databases/query-processing-architecture-guide.md#worktables)によって書き込みがサポートされます。   

並べ替え操作中に発生する書き込みは、[並べ替えの警告](../relational-databases/event-classes/sort-warnings-event-class.md)と呼ばれています。 並べ替えの警告は、並べ替え操作がメモリに収まらないことを示します。 インデックスの作成に関連する並べ替え操作は対象になりません。`SELECT` ステートメントで使用される `ORDER BY` 句などのクエリ内の並べ替え操作のみが対象になります。

ハッシュ操作中に発生する書き込みは、[ハッシュの警告](../relational-databases/event-classes/hash-warning-event-class.md)と呼ばれています。 これらは、ハッシュ演算中にハッシュの再帰またはハッシュの中断 (ハッシュの保留) が生じたときに発生します。
-  使用できるメモリ内にビルド入力が収まらないときに、ハッシュの再帰が発生します。その結果、入力が複数のパーティションに分割され、個別に処理されます。 複数のパーティションに分割されても使用できるメモリ内に収まらない場合は、さらにサブパーティションに分割され、個別に処理されます。 この分割プロセスは、使用できるメモリ内に各パーティションが収まるようになるまで、または最大再帰レベルに到達するまで続きます。
-  ハッシュ演算が最大再帰レベルに到達するとハッシュの保留が発生し、パーティション分割された残りのデータを処理するための代替プランに移行されます。 これらのイベントが原因となって、サーバー内のパフォーマンスが低下する可能性があります。

**バッチ モード実行** の場合、初期のメモリ許可は既定では特定の内部しきい値まで動的に増加することができます。 この動的なメモリ許可メカニズムは、バッチ モードで実行されている **ハッシュ** または **並べ替え** 操作のメモリ常駐実行を可能にするように設計されています。 これらの操作がまだメモリ内に収まらない場合は、ディスクへの書き込みが行われます。

実行モードの詳細については、「[クエリ処理アーキテクチャ ガイド](../relational-databases/query-processing-architecture-guide.md#execution-modes)」を参照してください。

## <a name="buffer-management"></a>バッファー管理
[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] データベースの主な目的はデータの格納と取得であるため、データベース エンジンの主要な特性は頻繁なディスク I/O ということになります。 ディスク I/O 操作は多くのリソースを消費するうえ、完了するのに比較的長い時間がかかるので、 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] では I/O の効率を上げることに重点を置いています。 バッファー管理は、この効率向上を実現するための重要なコンポーネントです。 バッファー管理コンポーネントは 2 つのメカニズムから構成されています。1 つはデータベース ページに対するアクセスと更新を行う **バッファー マネージャー** で、もう 1 つはデータベース ファイルの I/O を削減する **バッファー キャッシュ** (**バッファー プール**) です。 

### <a name="how-buffer-management-works"></a>バッファー管理のしくみ
バッファーはメモリ内の 8 KB のページで、データ ページやインデックス ページと同じサイズです。 したがって、バッファー キャッシュは 8 KB 単位のページに分割されます。 バッファー マネージャーは、データベース ディスク ファイルのデータ ページやインデックス ページをバッファー キャッシュに読み取って、変更されたページをディスクに書き戻すための機能を管理しています。 バッファー マネージャーが別のデータを読み取るためのバッファー領域を必要とするまで、そのページはバッファー キャッシュ内に残ります。 データに変更が加えられた場合だけ、そのデータがディスクに書き戻されます。 バッファー キャッシュ内のデータは、ディスクに書き戻す前に何度でも変更できます。 詳細については、「 [ページの読み取り](../relational-databases/reading-pages.md) 」および「 [ページの書き込み](../relational-databases/writing-pages.md)」をご覧ください。

[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] を起動すると、システムの物理メモリの量、構成されるサーバー スレッドの最大数、さまざまな起動パラメーターなど、数多くのパラメーターに基づいてバッファー キャッシュ用の仮想アドレス空間のサイズが計算されます。 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] では、この計算された量のプロセス仮想アドレス空間 (メモリ ターゲット) をバッファー キャッシュ用に予約しますが、現在の負荷に必要な量だけ物理メモリを獲得 (コミット) します。 **sys.dm_os_sys_info** カタログ ビューの **bpool_commit_target** 列と [bpool_committed](../relational-databases/system-dynamic-management-views/sys-dm-os-sys-info-transact-sql.md) 列に対してクエリを実行すると、メモリ ターゲットとして予約されているページ数と、バッファー キャッシュ内で現在コミットされているページ数をそれぞれ返すことができます。

[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] の起動からバッファー キャッシュがメモリ ターゲットを取得するまでの間隔を、割り当て増加といいます。 この間、読み取り要求によって、必要に応じてバッファーが使用されます。 たとえば、1 ページ 8 KB の読み取り要求では、1 つのバッファー ページが使用されます。 つまり、割り当て増加は、クライアント要求の数や種類によって異なります。 1 ページずつの読み取り要求を 8 ページ分の要求にまとめて変換することで (1 つのエクステントとします)、割り当て増加を高速化しています。 これにより、特に多くのメモリが搭載されたコンピューターでは、割り当て増加が非常に高速に完了します。 ページとエクステントの詳細については、「[ページとエクステントのアーキテクチャ ガイド](../relational-databases/pages-and-extents-architecture-guide.md#pages-and-extents)」を参照してください。

バッファー マネージャーはほとんどのメモリを [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] プロセスで使用するので、メモリ マネージャーと連携して他のコンポーネントでバッファーを使用できるようにします。 バッファー マネージャーは主に次のコンポーネントと対話します。

* リソース マネージャー。全体的なメモリ使用量を制御します。32 ビット プラットフォームではアドレス空間の使用量を制御します。  
* データベース マネージャーおよび [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] オペレーティング システム (SQLOS)。低レベルのファイル I/O 操作を行います。  
* ログ マネージャー。先行書き込みログ記録を行います。  

### <a name="supported-features"></a>サポートされている機能
バッファー マネージャーは、次の機能をサポートしています。

* **NUMA (non-uniform memory access)** に対応しています。 バッファー キャッシュ ページはハードウェア NUMA ノード間に分散されます。そのため、スレッドは外部メモリからではなく、ローカルの NUMA ノードに割り当てられているバッファー ページにアクセスすることができます。 
* **ホット アド メモリ** をサポートしています。そのため、ユーザーはサーバーを再起動することなく物理メモリを追加できます。 
* 64 ビット プラットフォームの **大きなページ** をサポートしています。 ページのサイズは、Windows のバージョンに固有です。

  > [!NOTE]
  > [!INCLUDE[ssSQL11](../includes/sssql11-md.md)] より前のバージョンの場合、[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] で大きなページを有効にするには、[トレース フラグ 834](../t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql.md) が必要です。  

* バッファー マネージャーは、動的管理ビューによって公開される追加の診断情報を提供します。 これらのビューを使用して、 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]に固有のさまざまなオペレーティング システム リソースを監視できます。 たとえば、[sys.dm_os_buffer_descriptors](../relational-databases/system-dynamic-management-views/sys-dm-os-buffer-descriptors-transact-sql.md) ビューを使用すると、バッファー キャッシュ内のページを監視できます。   

### <a name="disk-io"></a>ディスク I/O
バッファー マネージャーはデータベースの読み取りと書き込みだけを行います。 他のファイル操作やデータベース操作 (開く、閉じる、拡張、圧縮など) は、データベース マネージャー コンポーネントおよびファイル マネージャー コンポーネントによって実行されます。 

バッファー マネージャーによるディスク I/O 操作には、次の特性があります。

* すべての I/O は非同期で実行されます。つまり、呼び出し側スレッドでの処理中でも、I/O 操作はバックグラウンドで進行します。
* すべての I/O は affinity I/O mask オプションが使用中でなければ、呼び出し側スレッドで発行されます。 affinity I/O mask オプションでは、 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] のディスク I/O が、指定した CPU のサブセットに関連付けられます。 ハイエンドな [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] オンライン トランザクション処理 (OLTP) 環境では、この拡張機能により、I/O を発行する [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] スレッドのパフォーマンスを向上できます。
* 複数ページの I/O は、スキャッター/ギャザー I/O を使用して実行されます。スキャッター/ギャザー I/O を使用すると、連続しないメモリ領域との間でデータを転送できます。 つまり、 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] は、複数の物理 I/O 要求を回避しながら、バッファー キャッシュをすばやく使用またはフラッシュできます。 

#### <a name="long-io-requests"></a>実行時間の長い I/O 要求  
バッファー マネージャーは未処理状態が 15 秒以上続いた I/O 要求を報告します。 これはシステム管理者が [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] の問題か I/O サブシステムの問題かを区別するのに役立ちます。 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] のエラー ログには、次のようなエラー メッセージ 833 が報告および記録されます。

`SQL Server has encountered ## occurrence(s) of I/O requests taking longer than 15 seconds to complete on file [##] in database [##] (#). The OS file handle is 0x00000. The offset of the latest long I/O is: 0x00000.` 

実行時間の長い I/O は読み取りまたは書き込みのどちらかの処理ですが、どちらの処理なのかはメッセージに示されません。 実行時間の長い I/O のメッセージは、警告であってエラーではありません。 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] の問題は示しませんが、基礎になっている I/O システムの問題を示します。 これらのメッセージが報告されることにより、システム管理者は、 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] の応答時間が遅い原因を追求したり、 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]の制御の範囲外にある問題を見分けたりするのに役立てることができます。 このように、メッセージに対するアクションは不要ですが、システム管理者は I/O 要求が長時間かかっている理由や、かかっている時間が正当であるかどうかを調べる必要があります。

#### <a name="causes-of-long-io-requests"></a>実行時間の長い I/O 要求の原因  
実行時間の長い I/O のメッセージは、I/O が永続的にブロックされていて決して完了しないこと (ロスト I/O ともいいます) を示す場合があります。また、I/O が単純にまだ完了していないことを示す場合があります。 この場合、どちらのシナリオなのかをメッセージから区別することはできません。ただ、ロスト I/O の結果、ラッチ タイムアウトが生じることがよくあります。

多くの場合、実行時間の長い I/O は、 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] のワークロードによってディスク サブシステムに過度の負荷がかかっていることを示します。 ディスク サブシステムが不十分だと、次の現象が発生することがあります。

* 負荷の高い [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] ワークロード中に、実行時間の長い I/O のメッセージがエラー ログに複数記録される。
* パフォーマンス カウンターに、長時間ディスクが遅延している、長時間ディスク キューに登録されている、ディスクのアイドル時間がないといった情報が表示される。  

実行時間の長い I/O は、I/O パス内のコンポーネント (ドライバー、コントローラー、ファームウェアなど) が原因になっている場合もあります。ディスク ヘッドの現在位置の近くにある新しい I/O 要求の処理を優先して、古い I/O 要求の処理を絶えず延期するためです。 読み取り/書き込みヘッドの現在位置の最も近くにある要求を優先して要求を処理する一般的な技法を、"エレベーター シーク" といいます。 これは Windows システム モニター (PERFMON.EXE) ツールと連携させるのが困難である場合があります。大半の I/O は速やかに処理されるためです。 実行時間の長い I/O 要求は大容量のシーケンシャル I/O を実行するワークロードによって増大することがあります。たとえば、バックアップおよび復元、テーブル スキャン、並べ替え、インデックスの作成、一括読み込み、ファイルの占有領域の解放処理などがあります。

実行時間の長い I/O のうち、以前の状態には関係ないと考えられる孤立した I/O は、ハードウェアやドライバーの問題が原因になっている場合があります。 システム イベント ログには、問題の診断に役立つ関連イベントが含まれていることがあります。

### <a name="memory-pressure-detection"></a>メモリ不足の検出
メモリ不足は、メモリの不足が原因で発生する状態であり、次の結果を招く可能性があります。
- 余分な I/O の発生 (レイジー ライターの非常にアクティブなバック グラウンド スレッドなど)
- 再コンパイルの比率が高くなる
- クエリの実行時間が長くなる (メモリ許可待機が存在する場合)
- 余分な CPU サイクルが発生する

この状況は、外部的な原因または内部的な原因によって引き起こされる可能性があります。 外部的な原因には次のようなものがあります。
- 使用可能な物理メモリ (RAM) が不足しています。 これにより、システムは現在実行中のプロセスのワーキング セットをトリミングします。結果として、全体的な速度が低下する可能性があります。 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] はバッファー プールのコミット ターゲットを削減し、内部キャッシュのトリミングを頻繁に開始する可能性があります。 
- 使用できる全体的なシステム メモリ (システムのページ ファイルを含む) が不足しています。 これにより、システムはメモリの割り当てを失敗する場合があります。現在割り当てられているメモリをページ アウトできないためです。
内部的な原因には次のようなものがあります。
- [!INCLUDE[ssDEnoversion](../includes/ssdenoversion-md.md)] がメモリ使用量の下限を設定する場合に外部メモリ不足に対応します。
- *max server memory* 構成の値を手動で縮小することにより、メモリ設定の値が引き下げられました。 
- 内部コンポーネントによるいくつかのキャッシュ間のメモリ配分に変更が生じました。

[!INCLUDE[ssDEnoversion](../includes/ssdenoversion-md.md)] では、動的メモリ管理の一環として、メモリ不足の検出および処理のために専用のフレームワークが実装されます。 このフレームワークには、**リソース モニター** と呼ばれるバックグラウンド タスクが含まれています。 リソース モニター タスクでは、外部および内部のメモリ インジケーターの状態が監視されます。 これらのインジケーターのいずれかの状態が変化すると、対応する通知が計算され、その通知がブロードキャストされます。 これらの通知は各エンジン コンポーネントからの内部メッセージであり、リング バッファーに格納されます。 

次の 2 つのリング バッファーに、動的メモリ管理に関連する情報が保持されます。 
- メモリ不足が通知されているかどうかなど、リソース モニターのアクティビティを追跡するリソース モニター リング バッファー。 このリング バッファーの状態情報は、*RESOURCE_MEMPHYSICAL_HIGH*、*RESOURCE_MEMPHYSICAL_LOW*、*RESOURCE_MEMPHYSICAL_STEADY*、または *RESOURCE_MEMVIRTUAL_LOW* の現在の状態に依存します。
- 各 Resource Governor リソース プールのメモリ通知のレコードが含まれるメモリ ブローカー リング バッファー。 内部メモリ不足が検出されると、メモリの割り当てを行うコンポーネントに対して、メモリ不足を示す通知がオンになり、キャッシュ間でメモリのバランスをとるためのアクションがトリガーされます。 

メモリ ブローカーは、コンポーネントごとにメモリの需要と消費量を監視し、収集した情報に基づいて、これらのコンポーネントの各々に対してメモリの最適な値を算出します。 Resource Governor リソース プールごとにブローカー セットがあります。 この情報は、使用量を必要に応じて拡大または縮小する各コンポーネントにブロードキャストされます。
メモリ ブローカーの詳細については、[sys.dm_os_memory_brokers](../relational-databases/system-dynamic-management-views/sys-dm-os-memory-brokers-transact-sql.md) に関するページを参照してください。 

### <a name="error-detection"></a>エラー検出  
データベース ページで 2 つのオプションのメカニズム (破損ページ保護とチェックサム保護) を使用して、ページがディスクに書き込まれてから再び読み取られるまでの間、ページの整合性を保証できます。 これらのメカニズムによって、データ ストレージだけでなく、ハードウェア コンポーネント (コントローラー、ドライバー、ケーブルなど)、およびオペレーティング システムに至るまで、個々の正確性を検証するための独立した手段が可能になります。 この保護はディスクに書き込む直前にページに追加され、ディスクから読み取られた後で検証されます。

[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] は、チェックサム、破損ページ、またはその他の I/O エラーで読み取りに失敗した場合、その読み取りを 4 回再試行します。 いずれかの再試行で読み取りに成功した場合には、エラー ログにメッセージが書き込まれ、その読み取りを起動したコマンドは続行されます。 再試行が失敗した場合には、そのコマンドはエラー メッセージ 824 で失敗します。 

使用されている種類のページ保護は、ページが含まれているデータベースの属性です。 チェックサム保護は [!INCLUDE[ssVersion2005](../includes/ssversion2005-md.md)] 以降で作成されたデータベースの既定の保護です。 ページ保護のメカニズムはデータベースの作成時に指定するもので、ALTER DATABASE SET を使用して変更できます。 ページ保護の現在の設定を確認するには、[sys.databases](../relational-databases/system-catalog-views/sys-databases-transact-sql.md) カタログ ビューの *page_verify_option* 列、または [DATABASEPROPERTYEX](../t-sql/functions/databasepropertyex-transact-sql.md) 関数の *IsTornPageDetectionEnabled* プロパティを照会します。 

> [!NOTE]
> ページ保護の設定が変更されたとき、新しい設定がデータベース全体にすぐに反映されるわけではありません。 個々のページの出力時に、現在のデータベースの保護レベルがそのページに適用されます。 つまり、データベースはそれぞれ保護の種類が異なるページで構成されている場合があります。 

#### <a name="torn-page-protection"></a>破損ページ保護  
破損ページ保護は、 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 2000 で導入されたもので、主に電源障害によるページ破損を検出する方法です。 たとえば、予期しない電源障害でページの一部だけがディスクに書き込まれた状態になったとします。 破損ページ保護が使用されているとき、ディスクへのページ書き込み時に、8 KB のデータベース ページ内の 512 バイトのセクターごとに、特定の 2 ビット署名パターンがデータベース ページ ヘッダーに格納されます。 そのページがディスクから読み取られるときに、ページ ヘッダーに保存されている各セクターの破損ビットと、実際のページ セクター情報とが比較されます。 書き込みが行われるたびに署名パターンとしてバイナリの 01 と 10 が交互に設定されるので、セクターの一部だけがディスクに書き込まれたときを常に判別することが可能です。つまり、後でページが読み取られたときにビットの正しくない状態の場合、ページが不適切に書き込まれたので、破損ページが検出されます。 破損ページ検出で使用されるリソースは最小限です。ただし、ディスクのハードウェア障害が原因で発生したすべてのエラーを検出できるわけではありません。 破損ページ検出の詳細については、「[ALTER DATABASE の SET オプション &#40;Transact-SQL&#41;](../t-sql/statements/alter-database-transact-sql-set-options.md#page_verify)」を参照してください。

#### <a name="checksum-protection"></a>チェックサム保護  
チェックサム保護は、[!INCLUDE[ssVersion2005](../includes/ssversion2005-md.md)] から導入されたもので、今まで以上に強力なデータ整合性チェックを提供します。 チェックサムは各ページに書き込まれるデータから算出され、ページ ヘッダーに書き込まれます。 ページにチェックサムが書き込まれている場合は、そのページをディスクから読み取るたびにデータのチェックサムが再計算されます。そして、新しく計算されたチェックサムと、現在書き込まれているチェックサムが異なる場合は、エラー 824 が生成されます。 チェックサム保護はページの各バイトの影響を受けるので、破損ページ保護よりも多くのエラーをキャッチできますが、使用されるリソースがやや多くなります。 チェックサムが有効になっている場合、電源障害、欠陥のあるハードウェアやソフトウェアが原因で発生するエラーは、バッファー マネージャーがディスクからページを読み取るたびに検出できます。 チェックサム設定の詳細については、「[ALTER DATABASE の SET オプション &#40;Transact-SQL&#41;](../t-sql/statements/alter-database-transact-sql-set-options.md#page_verify)」を参照してください。

> [!IMPORTANT]
> ユーザー データベースまたはシステム データベースを [!INCLUDE[ssVersion2005](../includes/ssversion2005-md.md)] 以降のバージョンにアップグレードしても、[PAGE_VERIFY](../t-sql/statements/alter-database-transact-sql-set-options.md#page_verify) 値 (NONE または TORN_PAGE_DETECTION) が保持されます。 CHECKSUM の使用を強くお勧めします。
> TORN_PAGE_DETECTION は、使用するリソースが比較的少なくて済みますが、CHECKSUM による保護の最小限のサブセットしか利用できません。

## <a name="understanding-non-uniform-memory-access"></a>Non-Uniform Memory Access について
[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] は Non-Uniform Memory Access (NUMA) に対応しているので、特殊な構成を行わなくても NUMA ハードウェアで適切に実行されます。 プロセッサのクロック速度や数が増加するにつれて処理能力が向上しますが、その一方で、向上した能力の活用に必要となるメモリの待機時間を減らすことが困難になります。 ハードウェア ベンダーはメモリの待機時間をなくすために、大容量の L3 キャッシュを搭載していますが、この解決策にも限界があります。 この問題に対する、拡張性に優れた解決方法が NUMA アーキテクチャです。 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] は、アプリケーションを変更しなくても NUMA ベースのコンピューターを活用できるように設計されています。 詳細については、「[SQL Server を構成する方法](../database-engine/configure-windows/soft-numa-sql-server.md)」をご覧ください。

## <a name="dynamic-partition-of-memory-objects"></a>メモリ オブジェクトの動的パーティション
[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] のメモリ オブジェクトと呼ばれるヒープ アロケーターを使用すると、[!INCLUDE[ssde_md](../includes/ssde_md.md)] で、ヒープからメモリを割り当てることができます。 これらは [sys.dm_os_memory_objects](../relational-databases/system-dynamic-management-views/sys-dm-os-memory-objects-transact-sql.md) DMV を使用して追跡できます。 CMemThread とは、複数のスレッドから同時にメモリを割り当てることを可能にするスレッドセーフなメモリ オブジェクトの種類です。 追跡を正確に行うために、CMemThread オブジェクトは、同期コンストラクト (ミューテックス) に依存し、一度に 1 つのスレッドのみで重要な情報が確実に更新されるようになっています。 

> [!NOTE]
> CMemThread オブジェクトの種類は、[!INCLUDE[ssde_md](../includes/ssde_md.md)] コード ベース全体でさまざまな割り当てに使用され、ノードごとまたは CPU ごとにグローバルに分割することができます。   

ただし、ミューテックスを使用すると、多数のスレッドが同じメモリ オブジェクトから同時性の高い方法で割り当てられる場合に競合が発生する可能性があります。 したがって、[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] にはパーティション分割されたメモリ オブジェクト (PMO) という概念があり、各パーティションはそれぞれ 1 つの CMemThread オブジェクトによって表されます。 メモリ オブジェクトのパーティション分割は静的に定義され、作成後に変更することはできません。 メモリ割り当てパターンは、ハードウェアやメモリの使用状況などの側面に大きく左右されるので、完全なパーティション分割パターンを事前に取得することは不可能です。 ほとんどの場合は、1 つのパーティションを使用するだけで十分ですが、シナリオによっては、高度にパーティション分割されたメモリ オブジェクトのみが回避できる競合が発生する可能性があります。 各メモリ オブジェクトをパーティション分割することは望ましくありません。パーティションが増えると他の非効率性が生じ、メモリの断片化が増す可能性があるからです。

> [!NOTE]
> [!INCLUDE[sssql15-md](../includes/sssql16-md.md)] より前は、トレース フラグ 8048 を使用して、ノードベースの PMO を強制的に CPU ベースの PMO にすることができました。 [!INCLUDE[ssSQL14](../includes/sssql14-md.md)] SP2 および [!INCLUDE[sssql15-md](../includes/sssql16-md.md)] 以降は、この動作は動的であり、エンジンによって制御されます。

[!INCLUDE[ssSQL14](../includes/sssql14-md.md)] SP2 および [!INCLUDE[sssql15-md](../includes/sssql16-md.md)] 以降は、[!INCLUDE[ssde_md](../includes/ssde_md.md)] が特定の CMemThread オブジェクトでの競合を動的に検出し、そのオブジェクトをノードごと、または CPU ごとに基づく実装に昇格させることができます。  昇格すると、[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] プロセスが再開されるまで、PMO は昇格されたままになります。 CMemThread の競合は、[sys.dm_os_wait_stats](../relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql.md) DMV 内に CMEMTHREAD の長い待機時間が存在すること、および [sys.dm_os_memory_objects](../relational-databases/system-dynamic-management-views/sys-dm-os-memory-objects-transact-sql.md) DMV 列 *contention_factor*、*partition_type*、*exclusive_allocations_count*、*waiting_tasks_count* を観察することで検出できます。

## <a name="see-also"></a>参照
[サーバー メモリに関するサーバー構成オプション](../database-engine/configure-windows/server-memory-server-configuration-options.md)   
[ページの読み取り](../relational-databases/reading-pages.md)   
[ページの書き込み](../relational-databases/writing-pages.md)   
[方法: ソフト NUMA を使用するように SQL Server を構成する](../database-engine/configure-windows/soft-numa-sql-server.md)   
[メモリ最適化テーブルを使用するための要件](../relational-databases/in-memory-oltp/requirements-for-using-memory-optimized-tables.md)   
[メモリ最適化テーブルを利用してメモリ不足の問題を解決する](../relational-databases/in-memory-oltp/resolve-out-of-memory-issues.md)