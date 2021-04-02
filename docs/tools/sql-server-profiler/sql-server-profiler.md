---
title: SQL Server プロファイラー
titleSuffix: SQL Server Profiler
description: SQL Server プロファイラーの機能について説明します。 このツールを使用してトレースを作成し、トレース結果を分析および再生することにより、問題のトラブルシューティングに役立つ情報を得ることができます。
ms.prod: sql
ms.prod_service: sql-tools
ms.technology: profiler
ms.topic: conceptual
author: markingmyname
ms.author: maghan
ms.reviewer: ''
ms.custom: seo-lt-2019
ms.date: 05/01/2020
ms.openlocfilehash: 19d31aff6c79badb1f9728325fad4377dee246be
ms.sourcegitcommit: 295b9dfc758471ef7d238a2b0f92f93e34acbb1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106054589"
---
# <a name="sql-server-profiler"></a>SQL Server プロファイラー

[!INCLUDE[sql-asdbmi](../../includes/applies-to-version/sql-asdbmi.md)]

[!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)] は、トレースを作成および管理し、トレースの結果を分析および再生するためのインターフェイスです。 イベントはトレース ファイルに保存され、後で分析したり、問題を診断する際に具体的な一連のステップを再現するために利用できます。

> [!IMPORTANT]
> SQL トレースと [!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)] は、非推奨です。 Microsoft SQL Server の Trace や Replay オブジェクトを含む *Microsoft.SqlServer.Management.Trace* 名前空間も非推奨とされます。
> [!INCLUDE[ssNoteDepFutureAvoid](../../includes/ssnotedepfutureavoid-md.md)]
> 代わりに拡張イベントを使用します。 [拡張イベント](../../relational-databases/extended-events/extended-events.md)について詳しくは、「[クイック スタート:SQL Server 拡張イベント](../../relational-databases/extended-events/quick-start-extended-events-in-sql-server.md)」および [SSMS XEvent Profiler](../../relational-databases/extended-events/use-the-ssms-xe-profiler.md) に関するページをご覧ください。

> [!NOTE]
> [!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)] for Analysis Services のワークロードがサポートされています。

> [!NOTE]
> SQL Server プロファイラーから Azure SQL Database に接続しようとすると、次のような誤解を招くエラー メッセージが誤ってスローされます。
>
> - SQL Server に対してトレースを実行するには、sysadmin 固定サーバー ロールのメンバーであるか、ALTER TRACE 権限が許可されている必要があります。
>
> このメッセージでは、Azure SQL Database が SQL Server プロファイラーでサポートされていないことを説明すべきでした。

## <a name="where-is-the-profiler"></a>プロファイラーはどこにありますか?

[!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 内から、多数の方法でプロファイラーを起動できます。 [プロファイラーを起動する方法のリストについては、こちらのトピックをご覧ください。](start-sql-server-profiler.md)

## <a name="capture-and-replay-trace-data"></a>トレース データをキャプチャし再生する

以下の表に、 [!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] でトレース データのキャプチャおよび再生を行うために使用が推奨される機能を示します。

|**機能\対象のワークロード**|**リレーショナル エンジン**|**Analysis Services**|  
|-|-|-|
|**トレースのキャプチャ**|[!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] の [拡張イベント](../../relational-databases/extended-events/extended-events.md)のグラフィカル ユーザー インターフェイス|[!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)]|  
|**トレースの再生**|[分散再生](../distributed-replay/sql-server-distributed-replay.md)|[!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)]|

## <a name="use-sql-server-profiler"></a>SQL Server Profiler を使用する

Microsoft [!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)] は、 [!INCLUDE[ssDE](../../includes/ssde-md.md)] または Analysis Services のインスタンスを監視するための SQL トレースのグラフィカル ユーザー インターフェイスです。 各イベントに関するデータをキャプチャし、ファイルやテーブルに保存して、後で分析できます。 たとえば、稼動環境を監視して、どのストアド プロシージャの実行が遅く、パフォーマンスに影響を与えているかを確認できます。 [!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)] は、次のようなアクティビティに使用します。

- 問題の原因を特定するため、問題の発生したクエリを順次実行する。

- 実行速度の遅いクエリを検出し、その原因を診断する。

- 問題の原因となる一連の [!INCLUDE[tsql](../../includes/tsql-md.md)] ステートメントをキャプチャする。 トレース ファイルに保存された内容をテスト サーバー上にレプリケートし、問題の診断に利用できます。

- ワークロードを調整するため、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のパフォーマンスを監視する。 データベース ワークロードに関してデータベースの物理設計を調整する方法については、「 [Database Engine Tuning Advisor](../../relational-databases/performance/database-engine-tuning-advisor.md)」を参照してください。

- 問題を診断するために、さまざまなパフォーマンス カウンターの関連を調べる。

[!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)] では、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンスで実行された操作の監査もサポートしています。 監査では、後でセキュリティ管理者が調査できるように、セキュリティ関連の操作を記録します。

## <a name="sql-server-profiler-concepts"></a>SQL Server Profiler の概念

[!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)]を使用するには、ツールがどのように機能するのかを説明する用語を理解しておく必要があります。

> [!NOTE]
> [!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)] を使用するとき、SQL トレースの知識が実際に役立ちます。 詳細については、「 [SQL Trace](../../relational-databases/sql-trace/sql-trace.md)」を参照してください。

### <a name="event"></a>Event

イベントとは、 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]のインスタンス内で発生するアクションです。 次に例を示します。  
  
- ログインの接続、失敗、および接続解除。    
- [!INCLUDE[tsql](../../includes/tsql-md.md)] `SELECT`、`INSERT`、`UPDATE`、`DELETE` ステートメント。
- リモート プロシージャ コール (RPC) のバッチ ステータス。  
- ストアド プロシージャの開始または終了。  
- ストアド プロシージャ内のステートメントの開始または終了。  
- SQL バッチの開始または終了。  
- [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のエラー ログに書き込まれたエラー。  
- データベース オブジェクトで取得または解放されたロック。  
- 開かれたカーソル。  
- セキュリティ権限の確認。  
  
イベントによって生成されたデータは、すべてトレースに 1 行で表示されます。 この行の各データ列には、イベントの詳しい説明が表示されます。  

### <a name="eventclass"></a>EventClass

イベント クラスは、トレースできるイベントの種類を定義します。 イベント クラスには、イベントによって報告できるすべてのデータが含まれています。 イベント クラスの例を次に示します。

- **SQL:BatchCompleted**
- **Audit Login**
- **Audit Logout**
- **Lock:Acquired**
- **Lock:Released**

### <a name="eventcategory"></a>EventCategory

イベント カテゴリは、 [!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)]内でイベントを分類する方法を定義します。 たとえば、ロック イベント クラスはすべて、 **ロック** イベント カテゴリに分類されます。 ただし、イベント カテゴリは [!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)]でしか存在しません。 この用語に、エンジン イベントを分類する方法は反映されていません。

### <a name="datacolumn"></a>DataColumn

各データ列は、トレースでキャプチャされるイベント クラスの属性です。 収集できるデータの種類はイベント クラスによって異なるため、必ずしもすべてのデータ列がすべてのイベント クラスに使用されるわけではありません。 たとえば、**Lock:Acquired** イベント クラスをキャプチャするトレースでは、ロックされたページ ID またはロックされた行の値が **BinaryData** データ列に格納されますが、**Integer Data** データ列にはまったく値が格納されません。これは、キャプチャ対象のイベント クラスにこのデータ列が適合しないためです。

### <a name="template"></a>Template

テンプレートは、トレースの既定の構成を定義します。 特に、テンプレートには、 [!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)]を使用して監視するイベント クラスを指定します。 たとえば、使用するイベント、データ列、およびフィルターを指定するテンプレートを作成できます。 テンプレートは実行されませんが、.tdf 拡張子を持つファイルとして保存されます。 保存したテンプレートは、そのテンプレートに基づいて作成したトレースの実行時にどのようなトレース データをキャプチャするかを制御します。

### <a name="trace"></a>Trace

トレースでは、選択したイベント、データ列、およびフィルターに基づいたデータをキャプチャします。 たとえば、例外エラーを監視するトレースを作成できます。 これには、 **Exception** イベント クラスを選択し、そのデータ列である **Error**、 **State**、および **Severity** を選択します。 トレース結果で意味のあるデータを示すには、これら 3 列のデータを収集する必要があります。 このようにして構成されたトレースを実行し、サーバーで発生するすべての **Exception** イベントに関するデータを収集することができます。 トレース データは、保存することも、すぐに分析に使用することもできます。 トレースは、後日再生できます。ただし、 **Exception** イベントなど、再生不可能なイベントもあります。 また、トレースをテンプレートとして保存しておき、これに似たトレースを後日作成することもできます。  

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスをトレースするための方法が 2 つ用意されています。つまり、[!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)] を使用してトレースする方法と、システム ストアド プロシージャを使用してトレースする方法です。  

### <a name="filter"></a>Assert

トレースまたはテンプレートを作成する際には、指定したイベントで収集されたデータをフィルターで選択する基準を定義できます。 トレースが大きくなりすぎないようにするためには、イベント データのサブセットだけが収集されるようにフィルターを適用します。 たとえば、トレースでキャプチャする Microsoft Windows のユーザー名を特定のユーザーに限定して出力データを絞り込むことができます。  

フィルターが設定されていない場合は、選択したイベント クラスのすべてのイベントがトレースに出力されます。

## <a name="sql-server-profiler-tasks"></a>SQL Server Profiler のタスク

|タスクの説明|トピック|  
|----------------------|-----------|  
|特定の種類のイベントを監視することを目的として SQL Server に備わっている定義済みのテンプレートと、トレースを再生するために必要な権限を紹介します。|[SQL Server プロファイラーのテンプレートとアクセス許可](../../tools/sql-server-profiler/sql-server-profiler-templates-and-permissions.md)|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Profiler を実行する方法について説明します。|[SQL Server Profiler の実行に必要なアクセス許可](../../tools/sql-server-profiler/permissions-required-to-run-sql-server-profiler.md)|  
|トレースの作成方法について説明します。|[トレースの作成 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/create-a-trace-sql-server-profiler.md)|  
|トレース ファイルに含めるイベントおよびデータ列を指定する方法について説明します。|[トレース ファイルに含めるイベントとデータ列の指定 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/specify-events-and-data-columns-for-a-trace-file-sql-server-profiler.md)|  
|トレース結果をファイルに保存する方法について説明します。|[トレース結果のファイルへの保存 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/save-trace-results-to-a-file-sql-server-profiler.md)|  
|トレース結果をテーブルに保存する方法について説明します。|[トレース結果のテーブルへの保存 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/save-trace-results-to-a-table-sql-server-profiler.md)|  
|トレース内のイベントをフィルター処理する方法について説明します。|[トレース内のイベントへのフィルターの適用 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/filter-events-in-a-trace-sql-server-profiler.md)|  
|フィルター情報を表示する方法について説明します。|[フィルター情報の表示 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/view-filter-information-sql-server-profiler.md)|  
|フィルターを変更する方法について説明します。|[フィルターの変更 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/modify-a-filter-sql-server-profiler.md)|  
|トレース ファイルの最大ファイル サイズを設定する方法について説明します ([!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)])。|[トレース ファイルの最大ファイル サイズの設定 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/set-a-maximum-file-size-for-a-trace-file-sql-server-profiler.md)|  
|トレース テーブルの最大テーブル サイズを設定する方法について説明します。|[トレース テーブルの最大テーブル サイズの設定 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/set-a-maximum-table-size-for-a-trace-table-sql-server-profiler.md)|  
|トレースの開始方法について説明します。|[トレースを開始する](../../tools/sql-server-profiler/start-a-trace.md)|  
|サーバーへの接続後、トレースを自動的に開始する方法について説明します。|[サーバーへの接続後の自動的なトレースの開始 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/start-a-trace-automatically-after-connecting-to-a-server-sql-server-profiler.md)|  
|イベントの開始時刻に基づいてイベントをフィルター選択する方法について説明します。|[イベントの開始時刻に基づいたイベントのフィルター選択 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/filter-events-based-on-the-event-start-time-sql-server-profiler.md)|  
|イベントの終了時刻に基づいてイベントをフィルター選択する方法について説明します。|[イベントの終了時刻に基づいたフィルターでのイベントの選択 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/filter-events-based-on-the-event-end-time-sql-server-profiler.md)|  
|トレース内のサーバー プロセス ID (SPID) をフィルター選択する方法について説明します。|[トレースでのサーバー プロセス ID &#40;SPIDs&#41; のフィルター選択 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/filter-server-process-ids-spids-in-a-trace-sql-server-profiler.md)|  
|トレースを一時停止する方法について説明します。|[トレースの一時停止 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/pause-a-trace-sql-server-profiler.md)|  
|トレースを停止する方法について説明します。|[トレースの停止 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/stop-a-trace-sql-server-profiler.md)|  
|一時停止後または停止後にトレースを実行する方法について説明します。|[一時停止または停止したトレースの再開 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/run-a-trace-after-it-has-been-paused-or-stopped-sql-server-profiler.md)|  
|トレース ウィンドウをクリアする方法について説明します。|[トレース ウィンドウの消去 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/clear-a-trace-window-sql-server-profiler.md)|  
|トレース ウィンドウを閉じる方法について説明します。|[トレース ウィンドウを閉じる &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/close-a-trace-window-sql-server-profiler.md)|  
|トレース定義の既定値を設定する方法について説明します。|[トレース定義の既定値の設定 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/set-trace-definition-defaults-sql-server-profiler.md)|  
|トレース表示の既定値を設定する方法について説明します。|[トレース表示の既定値の設定 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/set-trace-display-defaults-sql-server-profiler.md)|  
|トレース ファイルを開く方法について説明します。|[トレース ファイルを開く &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/open-a-trace-file-sql-server-profiler.md)|  
|トレース テーブルを開く方法について説明します。|[トレース テーブルを開く &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/open-a-trace-table-sql-server-profiler.md)|  
|トレース テーブルを再生する方法について説明します。|[トレース テーブルを再生する &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/replay-a-trace-table-sql-server-profiler.md)|  
|トレース ファイルを再生する方法について説明します。|[トレース ファイルを再生する &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/replay-a-trace-file-sql-server-profiler.md)|  
|一度に単一のイベントを再生する方法について説明します。|[一度に単一のイベントの再生 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/replay-a-single-event-at-a-time-sql-server-profiler.md)|  
|ブレークポイントまで再生する方法について説明します。|[ブレークポイントまでの再生 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/replay-to-a-breakpoint-sql-server-profiler.md)|  
|カーソルまで再生する方法について説明します。|[カーソルまでの再生 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/replay-to-a-cursor-sql-server-profiler.md)|  
|[!INCLUDE[tsql](../../includes/tsql-md.md)] スクリプトを再生する方法について説明します。|[Transact-SQL スクリプトの再生 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/replay-a-transact-sql-script-sql-server-profiler.md)|  
|トレース テンプレートの作成方法について説明します。|[トレース テンプレートの作成 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/create-a-trace-template-sql-server-profiler.md)|  
|トレース テンプレートの変更方法について説明します。|[トレース テンプレートの変更 &#40;SQL Server Profiler&#41;](./modify-trace-templates.md)|  
|グローバル トレース オプションを設定する方法について説明します。|[グローバル トレース オプションの設定 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/set-global-trace-options-sql-server-profiler.md)|  
|トレース中に値列またはデータ列を検索する方法について説明します。|[トレース中の値列またはデータ列の検索 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/find-a-value-or-data-column-while-tracing-sql-server-profiler.md)|  
|実行中のトレースからテンプレートを作成する方法について説明します。|[実行中のトレースからのテンプレートの作成 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/derive-a-template-from-a-running-trace-sql-server-profiler.md)|  
|トレース ファイルまたはトレース テーブルからテンプレートを作成する方法について説明します。|[トレース ファイルまたはトレース テーブルからのテンプレートの作成 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/derive-a-template-from-a-trace-file-or-trace-table-sql-server-profiler.md)|  
|トレースを実行するための [!INCLUDE[tsql](../../includes/tsql-md.md)] スクリプトを作成する方法について説明します。|[トレースを実行するための Transact-SQL スクリプトの作成 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/create-a-transact-sql-script-for-running-a-trace-sql-server-profiler.md)|  
|トレース テンプレートをエクスポートする方法について説明します。|[トレース テンプレートのエクスポート &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/export-a-trace-template-sql-server-profiler.md)|  
|トレース テンプレートをインポートする方法について説明します。|[トレース テンプレートのインポート &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/import-a-trace-template-sql-server-profiler.md)|  
|スクリプトをトレースから抽出する方法について説明します。|[トレースからのスクリプトの抽出 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/extract-a-script-from-a-trace-sql-server-profiler.md)|  
|トレースと Windows パフォーマンス ログ データを相互に関連付ける方法について説明します。|[トレースと Windows パフォーマンス ログ データの関連付け &#40;SQL Server Profiler&#41;](./correlate-a-trace-with-windows-performance-log-data.md)|  
|トレースに表示される列を構成する方法について説明します。|[トレースに表示される列の構成 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/organize-columns-displayed-in-a-trace-sql-server-profiler.md)|  
|[!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)] の開始方法について説明します。|[SQL Server Profiler の起動](../../tools/sql-server-profiler/start-sql-server-profiler.md)|  
|トレースとトレース テンプレートを保存する方法について説明します。|[トレースとトレース テンプレートの保存](../../tools/sql-server-profiler/save-traces-and-trace-templates.md)|  
|トレース テンプレートの変更方法について説明します。|[トレース テンプレートを変更する](../../tools/sql-server-profiler/modify-trace-templates.md)|  
|トレースと Windows パフォーマンス ログ データを相互に関連付ける方法について説明します。|[トレースと Windows パフォーマンス ログ データの関連付け](../../tools/sql-server-profiler/correlate-a-trace-with-windows-performance-log-data.md)|  
|[!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)] を使用したトレースの表示と分析の方法について説明します。|[SQL Server Profiler を使用したトレースの表示と分析](../../tools/sql-server-profiler/view-and-analyze-traces-with-sql-server-profiler.md)|  
|[!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)] を使用してデッドロックを分析する方法について説明します。|[SQL Server Profiler を使用したデッドロックの分析](../../tools/sql-server-profiler/analyze-deadlocks-with-sql-server-profiler.md)|  
|SQL Server Profiler での SHOWPLAN 結果を使用してクエリを分析する方法について説明します。|[SQL Server Profiler での Showplan 結果を使用したクエリの分析](../../tools/sql-server-profiler/analyze-queries-with-showplan-results-in-sql-server-profiler.md)|  
|[!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)] でトレースにフィルターを適用する方法について説明します。|[SQL Server Profiler でのトレースへのフィルターの適用](../../tools/sql-server-profiler/filter-traces-with-sql-server-profiler.md)|  
|[!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)] の再生機能の使用方法について説明します。|[トレースの再生](../../tools/sql-server-profiler/replay-traces.md)|  
|[!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)] の状況依存のヘルプ トピックについて紹介します。|[SQL Server Profiler の F1 ヘルプ](../../tools/sql-server-profiler/sql-server-profiler-f1-help.md)|  
|[!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)] でパフォーマンスと利用状況を監視する際に使用される一連のシステム ストアド プロシージャを紹介します。|[SQL Server Profiler のストアド プロシージャ &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sql-server-profiler-stored-procedures-transact-sql.md)|  

## <a name="see-also"></a>関連項目

- [Locks イベント カテゴリ](../../relational-databases/event-classes/locks-event-category.md)
- [Sessions イベント カテゴリ](../../relational-databases/event-classes/sessions-event-category.md)
- [Stored Procedures イベント カテゴリ](../../relational-databases/event-classes/stored-procedures-event-category.md)
- [TSQL イベント カテゴリ](../../relational-databases/event-classes/tsql-event-category.md)
- [サーバーのパフォーマンスと利用状況の監視](../../relational-databases/performance/server-performance-and-activity-monitoring.md)