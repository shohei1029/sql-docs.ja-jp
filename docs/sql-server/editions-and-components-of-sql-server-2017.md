---
title: エディションとサポートされる機能
titleSuffix: SQL Server 2017
description: この記事では、SQL Server 2017 のさまざまなエディションでサポートされている機能について説明します。これらは、さまざまなパフォーマンス、ランタイム、および価格の要件に対応しています。
ms.custom: seo-lt-2019
ms.date: 12/13/2019
ms.prod: sql
ms.reviewer: ''
ms.technology: release-landing
ms.topic: conceptual
helpviewer_keywords:
- Enterprise Edition [SQL Server]
- Developer Edition [SQL Server]
- 32-bit vs. 64-bit editions [SQL Server]
- default components
- Workgroup Edition [SQL Server]
- Internet servers [SQL Server]
- installing SQL Server, components
- Setup [SQL Server], components
- SQL Server, editions
- SQL Server, components
- client/server applications [SQL Server]
- editions [SQL Server]
- versions [SQL Server]
- Setup [SQL Server], editions
- SQL Server Installation Wizard
- components [SQL Server]
- Standard Edition [SQL Server]
- 64-bit edition [SQL Server]
- IIS [SQL Server]
- installing SQL Server, editions
- editions [SQL Server], about edition options
- Setup [SQL Server]
ms.assetid: ''
author: MikeRayMSFT
ms.author: mikeray
monikerRange: '>= sql-server-2017'
ms.openlocfilehash: 1d0918500cdb7e5bf7a21222ad77299092eb8ac1
ms.sourcegitcommit: 295b9dfc758471ef7d238a2b0f92f93e34acbb1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106054483"
---
# <a name="editions-and-supported-features-of-sql-server-2017"></a>SQL Server 2017 の各エディションとサポートされている機能
[!INCLUDE[SQL Server 2017](../includes/applies-to-version/sqlserver2017.md)]

このトピックでは、SQL Server 2017 のさまざまなエディションでサポートされている機能の詳細を説明します。 

その他のバージョンについては、以下を参照してください。

* [SQL Server 2019](editions-and-components-of-sql-server-version-15.md)。  
* [SQL Server 2016](editions-and-components-of-sql-server-2016.md)。  
  
インストールの前提条件は、アプリケーションのニーズによって異なります。 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] にはさまざまなエディションがあり、組織や個人の独自のパフォーマンス、ランタイム、および価格に関する要件に対応できます。 インストールする [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] コンポーネントは、ユーザーの特定の要件によっても異なります。 この後のセクションでは、 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]の最適なエディションおよびコンポーネントを選択する方法について説明します。  

180 日の試用期間中、SQL Server Evaluation Edition をご利用いただけます。  
  
最新のリリース ノートと新機能については、以下の情報を参照してください。
- [SQL Server 2017 リリース ノート](../sql-server/sql-server-2017-release-notes.md)
- [SQL Server 2017 の新機能](../sql-server/what-s-new-in-sql-server-2017.md)

### <a name="try-sql-server"></a>SQL Server をお試しください    
    
> [![Evaluation Center からダウンロードする](/analysis-services/analysis-services/media/download.png)](https://www.microsoft.com/evalcenter/evaluate-sql-server-2017-ctp/) **[Evaluation Centerから SQL Server 2017 をダウンロードする](https://www.microsoft.com/evalcenter/evaluate-sql-server-2017-ctp/)**    

<!---    
> ![Azure Virtual Machine small](/analysis-services/analysis-services/media/azure-virtual-machine-small.png) **[Spin up a Virtual Machine with SQL Server 2016 already installed](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SQL2016SP1-WS2016?tab=Overview?wt.mc_id=sqL16_vm)**   
--->

## <a name="sql-server-editions"></a>SQL Server のエディション  
 次の表で、 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]のエディションについて説明します。 
  
|[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] エディション|定義|  
|---------------------------------------|----------------|  
|Enterprise|プレミアム製品である [!INCLUDE[ssNoVersion](../includes/ssNoVersion-md.md)] Enterprise Edition は、非常に優れたパフォーマンス、無制限の仮想化<sup>1</sup>、およびエンド ツー エンドのビジネス インテリジェンスを備えた包括的なハイエンド データセンター機能を提供することで、ミッション クリティカルなワークロードに高水準のサービス レベルを実現し、エンド ユーザーがデータの分析情報を入手できるようにしています。|  
|Standard|[!INCLUDE[ssNoVersion](../includes/ssNoVersion-md.md)] Standard Edition は、企業部門や小規模組織がアプリケーションを実行するための基本的なデータ管理/ビジネス インテリジェンス データベースを提供し、内部設置型およびクラウド用の一般的な開発ツールをサポートすることで、最小限の IT リソースでデータベースを効果的に管理することを可能にします。|  
|Web|[!INCLUDE[ssNoVersion](../includes/ssNoVersion-md.md)] Web Edition は、大小さまざまな規模の Web 資産に対応できるスケーラビリティ、経済性、および管理性を備えた、Web ホスティング企業および Web VAP 向けの総保有コストの低いオプションです。|  
|Developer|開発者は、[!INCLUDE[ssNoVersion](../includes/ssNoVersion-md.md)] Developer Edition を使用して、 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]上で動作するあらゆる種類のアプリケーションを開発できます。 このエディションには Enterprise Edition の機能がすべて含まれていますが、実稼動サーバーとして使用するのではなく、開発およびテスト システムとしての利用に対してライセンスが供与されます。 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] Developer は、アプリケーションを作成し、テストするユーザーに適しています。|  
|Express Edition|Express Edition はエントリレベルの無料のデータベースで、学習や、デスクトップおよび小規模サーバー データ ドリブン アプリケーションの構築などに適しています。 このエディションは、独立系ソフトウェア ベンダー、開発者、クライアント アプリケーションを趣味で開発する開発者などに最適です。 さらに高度なデータベース機能が必要な場合には、 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] Express を [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]の他の上位バージョンにシームレスにアップグレードできます。 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] Express LocalDB は、Express の簡易バージョンです。Express のプログラミング機能をすべて備えており、ユーザー モードで実行され、前提条件が少なく構成不要の高速インストールが可能です。|  

<sup>1</sup> 無制限の仮想化は、[ソフトウェア アシュアランス](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default)がある Enterprise Edition のお客様が利用できます。 デプロイは、[ライセンス ガイド](https://download.microsoft.com/download/7/8/C/78CDF005-97C1-4129-926B-CE4A6FE92CF5/SQL_Server_2017_Licensing_guide.pdf)に準拠している必要があります。 詳細については、[価格とライセンスのページ](https://www.microsoft.com/sql-server/sql-server-2017-pricing)を参照してください。

## <a name="using-ssnoversion-with-an-internet-server"></a>[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] とインターネット サーバーの使用  
 インターネット インフォメーション サービス (IIS) を実行するサーバーなどのインターネット サーバーでは、一般に [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] クライアント ツールをインストールします。 クライアント ツールには、アプリケーションが [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]のインスタンスに接続する際に使用するクライアント接続コンポーネントが含まれています。  
  
>[!NOTE]
>IIS を実行しているコンピューターに [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] のインスタンスをインストールすることは可能ですが、通常はサーバー コンピューターが 1 台の小規模 Web サイトの場合に行います。 ほとんどの Web サイトでは、その中層 IIS システムが 1 つまたはクラスター構成の複数サーバーに配置され、データベースは 1 つの独立したサーバーまたは連合構成の複数サーバーに配置されます。  
  
## <a name="using-ssnoversion-with-clientserver-applications"></a>[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] とクライアント/サーバー アプリケーションの使用  
 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] のクライアント コンポーネントだけを、 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]のインスタンスに直接接続するクライアント/サーバー アプリケーションを実行するコンピューターにインストールできます。 クライアント コンポーネントをインストールすることは、データベース サーバー上にある [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] のインスタンスを管理する場合、または [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] アプリケーションを開発しようとしている場合にも適切なオプションです。  
  
 クライアント ツールをインストールするオプションを選択すると、下位互換コンポーネント、 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 、接続コンポーネント、管理ツール、ソフトウェア開発キット、 [!INCLUDE[ssBIDevStudio](../includes/ssbidevstudio-md.md)]オンライン ブック コンポーネントなどの [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 機能がインストールされます。 詳細については、「[SQL Server のインストール](../database-engine/install-windows/install-sql-server.md)」を参照してください。  
  
## <a name="deciding-among-ssnoversion-components"></a>[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] コンポーネントの決定  
 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] のインストールに含めるコンポーネントを選択するには、 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]インストール ウィザードの [機能の選択] ページを使用します。 既定では、ツリーの中に、選択されている機能はありません。  
  
 以下の表の情報を参照し、ニーズに最も合う機能を判断してください。  
  
|サーバー コンポーネント|説明|  
|-----------------------|-----------------|  
|[!INCLUDE[ssDEnoversion](../includes/ssdenoversion-md.md)]|[!INCLUDE[ssDEnoversion](../includes/ssdenoversion-md.md)] には[!INCLUDE[ssDE](../includes/ssde-md.md)]が含まれています。これは、データの格納、処理、およびセキュリティ保護、レプリケーション、フルテキスト検索、リレーショナルおよび XML データを管理するツール、データベース内分析機能の統合、および Hadoop とその他の異種データ ソースにアクセスするための PolyBase 統合、および [!INCLUDE[ssDQSnoversion](../includes/ssdqsnoversion-md.md)] (DQS) サーバー用のコア サービスです。|  
|[!INCLUDE[ssASnoversion](../includes/ssasnoversion-md.md)]|[!INCLUDE[ssASnoversion](../includes/ssasnoversion-md.md)] には、オンライン分析処理 (OLAP) アプリケーションおよびデータ マイニング アプリケーションを作成および管理するためのツールが含まれます。|  
|[!INCLUDE[ssRSnoversion](../includes/ssrsnoversion-md.md)]|[!INCLUDE[ssRSnoversion](../includes/ssrsnoversion-md.md)] には、表形式、マトリックス形式、グラフィカル形式、および自由形式のレポートを作成、管理、配置するためのサーバー コンポーネントとクライアント コンポーネントが含まれます。 [!INCLUDE[ssRSnoversion](../includes/ssrsnoversion-md.md)] は、レポート アプリケーション開発用の拡張可能プラットフォームとしても使用できます。|  
|[!INCLUDE[ssISnoversion](../includes/ssisnoversion-md.md)]|[!INCLUDE[ssISnoversion](../includes/ssisnoversion-md.md)] は、データを移動、コピー、変換するためのグラフィカル ツールおよびプログラミング可能なオブジェクトのセットです。 [!INCLUDE[ssDQSnoversion](../includes/ssdqsnoversion-md.md)] 用の [!INCLUDE[ssISnoversion](../includes/ssisnoversion-md.md)](DQS) コンポーネントも含まれています。|  
|[!INCLUDE[ssMDSshort](../includes/ssmdsshort-md.md)]|[!INCLUDE[ssMDSshort](../includes/ssmdsshort-md.md)] (MDS) は、マスター データ管理のための [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] ソリューションです。 MDS は、あらゆるドメイン (製品、顧客、アカウント) を管理するように構成できます。MDS には、データの管理に使用できる [!INCLUDE[ssMDSXLS](../includes/ssmdsxls-md.md)] のほかに、階層、きめ細かいセキュリティ、トランザクション、データのバージョン管理、およびビジネス ルールが含まれています。|  
|Machine Learning Services (データベース内)|Machine Learning Services (データベース内) は、エンタープライズ データ ソースを使用する分散型のスケーラブルな機械学習ソリューションをサポートします。 SQL Server 2016 では、R 言語がサポートされていました。 SQL Server 2017 では、R と Python がサポートされています。|
|Machine Learning Server (スタンドアロン)|Machine Learning Server (スタンドアロン) では、Linux や Hadoop などの複数のエンタープライズ データ ソースを使用する分散型のスケーラブルな機械学習ソリューションを複数のプラットフォームに配置できます。 SQL Server 2016 では、R 言語がサポートされていました。 SQL Server 2017 では、R と Python がサポートされています。|

  
|管理ツール|説明|  
|----------------------|-----------------|  
|[!INCLUDE[ssManStudioFull](../includes/ssmanstudiofull-md.md)]|[!INCLUDE[ssManStudioFull](../includes/ssmanstudiofull-md.md)] は、 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]のコンポーネントを構成、管理、開発し、それらのコンポーネントへアクセスするための統合環境です。 [!INCLUDE[ssManStudio](../includes/ssmanstudio-md.md)] では、すべてのスキル レベルの開発者と管理者が [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]を使用できます。<br /><br /> ダウンロードしてインストールする <br />                [!INCLUDE[ssManStudio](../includes/ssmanstudio-md.md)] (「  [SQL Server Management Studio (SSMS) のダウンロード](../ssms/download-sql-server-management-studio-ssms.md)|  
|[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 構成マネージャー|[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 構成マネージャーを使用して、 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] サービス、サーバー プロトコル、クライアント プロトコル、およびクライアントの別名に関する基本的な構成管理を行います。|  
|[!INCLUDE[ssSqlProfiler](../includes/sssqlprofiler-md.md)]|[!INCLUDE[ssSqlProfiler](../includes/sssqlprofiler-md.md)] には、 [!INCLUDE[ssDE](../includes/ssde-md.md)] または [!INCLUDE[ssASnoversion](../includes/ssasnoversion-md.md)]のインスタンスを監視するためのグラフィカル ユーザー インターフェイスが備わっています。|  
|[!INCLUDE[ssDE](../includes/ssde-md.md)] チューニング アドバイザー|[!INCLUDE[ssDE](../includes/ssde-md.md)] チューニング アドバイザーは、最適なインデックスのセット、インデックス付きビュー、およびパーティションの作成に役立ちます。|  
|Data Quality クライアント|DQS サーバーに接続し、データ クレンジング操作を実行するための、非常にシンプルで直感的なグラフィカル ユーザー インターフェイスを備えています。 データ クレンジング操作の際に行われるさまざまな処理を一元的に監視することもできます。|  
|[!INCLUDE[ssBIDevStudio](../includes/ssbidevstudio-md.md)]|[!INCLUDE[ssBIDevStudio](../includes/ssbidevstudio-md.md)] は、ビジネス インテリジェンス コンポーネント ( [!INCLUDE[ssASnoversion](../includes/ssasnoversion-md.md)]、 [!INCLUDE[ssRSnoversion](../includes/ssrsnoversion-md.md)]、 [!INCLUDE[ssISnoversion](../includes/ssisnoversion-md.md)]) のソリューションをビルドするための IDE です。<br /><br /> (以前の Business Intelligence Development Studio)。<br /><br /> [!INCLUDE[ssBIDevStudio](../includes/ssbidevstudio-md.md)] には、Visual Studio 内で [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] プラットフォーム (社内および社外の両方) のデータベース設計を行うデータベース開発者用の統合環境である "データベース プロジェクト" も含まれます。 データベース開発者は、強化された Visual Studio のサーバー エクスプローラーを使用して、データベース オブジェクトおよびデータの作成または編集、クエリの実行を簡単に行うことができます。|  
|接続コンポーネント|クライアントとサーバー間の通信用コンポーネント、および DB-Library、ODBC、OLE DB 用のネットワーク ライブラリをインストールします。|  
  
|ドキュメント|説明|  
|-------------------|-----------------|  
|[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] オンライン ブック|[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]の主要マニュアル。| 

**Developer Edition と Evaluation Edition**  
Developer Edition と Evaluation Edition でサポートされている機能については、下の表に記載されている SQL Server Enterprise Edition の機能をご覧ください。

Developer Edition は引き続き [SQL Server Distributed Replay](../tools/distributed-replay/sql-server-distributed-replay.md) のクライアントを 1 つだけサポートします。 
  
##  <a name="scale-limits"></a><a name="Cross-BoxScaleLimits"></a> スケールの制限  
  
|機能|Enterprise|Standard|Web|Express with Advanced Services|Express| 
|-------------|----------------|--------------|---------|------------------------------------|------------------------|
|1 つのインスタンスで使用される最大計算容量 - [!INCLUDE[ssDEnoversion](../includes/ssdenoversion-md.md)]<sup>1</sup>|オペレーティング システムの最大容量|4 ソケットまたは 24 コアのいずれか小さいほうに制限|4 ソケットまたは 16 コアのいずれか小さいほうに制限|1 ソケットまたは 4 コアのいずれか小さいほうに制限|1 ソケットまたは 4 コアのいずれか小さいほうに制限| 
|1 つのインスタンスで使用される最大計算容量 - [!INCLUDE[ssASnoversion](../includes/ssasnoversion-md.md)] または [!INCLUDE[ssRSnoversion](../includes/ssrsnoversion-md.md)]|オペレーティング システムの最大容量|4 ソケットまたは 24 コアのいずれか小さいほうに制限|4 ソケットまたは 16 コアのいずれか小さいほうに制限|1 ソケットまたは 4 コアのいずれか小さいほうに制限|1 ソケットまたは 4 コアのいずれか小さいほうに制限|  
|[!INCLUDE[ssDEnoversion](../includes/ssdenoversion-md.md)] のインスタンスごとのバッファー プールの最大メモリ|オペレーティング システムの最大容量|128 GB|64 GB|1410 MB|1410 MB|
|[!INCLUDE[ssDEnoversion](../includes/ssdenoversion-md.md)] のインスタンスごとの列ストア セグメント キャッシュの最大メモリ|メモリ制限なし| 32 GB| 16 GB| 352 MB| 352 MB|  
|[!INCLUDE[ssDEnoversion](../includes/ssdenoversion-md.md)] のデータベースごとの最大メモリ最適化データ サイズ|メモリ制限なし| 32 GB| 16 GB| 352 MB| 352 MB|  
|利用可能な最大メモリ サイズ ( [!INCLUDE[ssASnoversion](../includes/ssasnoversion-md.md)]のインスタンスごと)|オペレーティング システムの最大容量|テーブル: 16 GB<br /><br /> MOLAP: 64 GB|該当なし|なし|該当なし|  
|利用可能な最大メモリ サイズ ( [!INCLUDE[ssRSnoversion](../includes/ssrsnoversion-md.md)]のインスタンスごと)|オペレーティング システムの最大容量|64 GB|64 GB|4 GB|該当なし|
|リレーショナル データベースの最大サイズ|524 PB|524 PB|524 PB|10 GB|10 GB|  
  
<sup>1</sup> Enterprise Edition with Server + Client Access License (CAL) に基づくライセンス (新しい使用許諾契約では利用できません) は、SQL Server インスタンスあたり最大 20 コアに制限されています。 コアベースのサーバー ライセンス モデルでは、制限はありません。 詳細については、「 [Compute Capacity Limits by Edition of SQL Server](../sql-server/compute-capacity-limits-by-edition-of-sql-server.md)」を参照してください。  
 
##  <a name="rdbms-high-availability"></a><a name="RDBMSHA"></a> RDBMS の高可用性  
  
|機能|Enterprise|Standard|Web|Express with Advanced Services|Express|  
|-------------|----------------|--------------|---------|------------------------------------|------------------------|  
|Server Core サポート <sup>1</sup>|はい|はい|はい|はい|はい|  
|ログ配布|はい|はい|はい|いいえ|いいえ|  
|データベース ミラーリング|はい|はい<br /><br /> FULL SAFETY のみ|ミラーリング監視のみ|ミラーリング監視のみ|ミラーリング監視のみ| 
|バックアップ圧縮|はい|はい|いいえ|いいえ|いいえ| 
|データベース スナップショット|はい|はい|はい|はい|はい|
|Always On フェールオーバー クラスター インスタンス<sup>2</sup>|はい|はい|いいえ|いいえ|いいえ|  
|Always On 可用性グループ<sup>3</sup>|はい|いいえ|いいえ|いいえ|いいえ|
|基本的な可用性グループ <sup>4</sup>|いいえ|はい|いいえ|いいえ|いいえ|
|オンライン ページおよびファイルの復元|はい|いいえ|いいえ|いいえ|いいえ|
|オンライン インデックスの作成と再構築|はい|いいえ|いいえ|いいえ|いいえ|
|再開可能なオンライン インデックス再構築|はい|いいえ|いいえ|いいえ|いいえ|
|オンラインのスキーマ変更|はい|いいえ|いいえ|いいえ|いいえ|
|高速復旧|はい|いいえ|いいえ|いいえ|いいえ|
|ミラー化バックアップ|はい|いいえ|いいえ|いいえ|いいえ|
|ホット アド メモリと CPU|はい|いいえ|いいえ|いいえ|いいえ|
|データベース復旧アドバイザー|はい|はい|はい|はい|はい|
|暗号化されたバックアップ|はい|はい|いいえ|いいえ|いいえ|
|Azure へのハイブリッド バックアップ (URL へのバックアップ)|はい|はい|いいえ|いいえ|いいえ|
|読み取りスケール可用性グループ<sup>3、4</sup>|はい|いいえ|いいえ|いいえ|いいえ|

<sup>1</sup> Server Core への SQL Server のインストールの詳細については、「[Server Core への SQL Server のインストール](../database-engine/install-windows/install-sql-server-on-server-core.md)」を参照してください。 

<sup>2</sup> Enterprise Edition では、ノードの数はオペレーティング システムの最大容量です。 Standard Edition では、2 つのノードがサポートされます。 

<sup>3</sup> Enterprise Edition では、2 個の同期セカンダリ レプリカを含む最大 8 個のセカンダリ レプリカがサポートされます。 

<sup>4</sup> Standard Edition では、基本的な可用性グループがサポートされます。 基本的な可用性グループは、1 つのデータベースで、2 つのレプリカをサポートします。 基本的な可用性グループの詳細については、「[基本的な可用性グループ](../database-engine/availability-groups/windows/basic-availability-groups-always-on-availability-groups.md)」を参照してください。  


##  <a name="rdbms-scalability-and-performance"></a><a name="RDBMSSP"></a> RDBMS のスケーラビリティとパフォーマンス  
  
|機能|Enterprise|Standard|Web|Express with Advanced Services|Express|  
|-------------|----------------|--------------|---------|------------------------------------|------------------------| 
|列ストア<sup>1</sup> <sup>2</sup>|はい|はい|はい|はい|はい|  
|クラスター化列ストア インデックス内のラージ オブジェクト バイナリ|はい|はい|はい|はい|はい|  
|オンライン非クラスター化列ストア インデックスの再構築|はい|いいえ|いいえ|いいえ|いいえ|
|インメモリ OLTP<sup>1</sup>|はい|はい|はい|○<sup>3</sup>|はい|
|Stretch Database|はい|はい|はい|はい|はい|
|恒久的なメイン メモリ|はい|はい|はい|はい|はい|
|複数インスタンスのサポート|50|50|50|50|50|
|テーブルとインデックスのパーティション分割|はい|はい|はい|はい|はい|  
|データ圧縮|はい|はい|はい|はい|はい|
|[リソース ガバナー]|はい|いいえ|いいえ|いいえ|いいえ|  
|パーティション テーブルの並列処理|はい|いいえ|いいえ|いいえ|いいえ|
|複数の Filestream コンテナー|はい|はい|はい|はい|はい|
|NUMA 対応のラージ ページ メモリとバッファー配列の割り当て|はい|いいえ|いいえ|いいえ|いいえ|
|バッファー プール拡張|はい|はい|いいえ|いいえ|いいえ|
|I/O リソース管理|はい|いいえ|いいえ|いいえ|いいえ|  
|先行読み取り|はい|いいえ|いいえ|いいえ|いいえ|
|拡張スキャン|はい|いいえ|いいえ|いいえ|いいえ|
|遅延持続性|はい|はい|はい|はい|はい|
|自動調整|はい|いいえ|いいえ|いいえ|いいえ|
|バッチ モードの適応型結合|はい|いいえ|いいえ|いいえ|いいえ|
|バッチ モード メモリ許可フィードバック|はい|いいえ|いいえ|いいえ|いいえ|
|複数ステートメントのテーブル値関数のインターリーブ実行|はい|はい|はい|はい|はい|
|一括挿入の機能強化|はい|はい|はい|はい|はい|

<sup>1</sup> インメモリ OLTP データ サイズおよび列ストア セグメント キャッシュは、「[スケールの制限](#Cross-BoxScaleLimits)」セクションでエディションごとに指定されているメモリ量に制限されます。 [バッチ モード](../relational-databases/query-processing-architecture-guide.md#batch-mode-execution)操作の並列処理の度合い (DOP) は、[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] Standard Edition では 2 DOP、[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] Web Edition および Express Edition では 1 DOP に制限されます。 これは、ディスク ベース テーブルとメモリ最適化テーブルで作成された列ストア インデックスに当てはまります。

<sup>2</sup> 集計プッシュダウン、文字列述語のプッシュダウン、SIMD の最適化は、[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] Enterprise Edition のスケーラビリティの強化です。 詳細については、「[列ストア インデックス - 新機能](../relational-databases/indexes/columnstore-indexes-what-s-new.md)」を参照してください。

<sup>3</sup> LocalDB のインストール オプションには、この機能は含まれていません。

##  <a name="rdbms-security"></a><a name="RDBMSS"></a> RDBMS のセキュリティ  
  
|機能|Enterprise|Standard|Web|Express|Express with Advanced Services|  
|-------------|----------------|--------------|---------|-------------|------------------------------------| 
|行レベルのセキュリティ|はい|はい|はい|はい|はい|  
|Always Encrypted|はい|はい|はい|はい|はい| 
|動的データ マスク|はい|はい|はい|はい|はい|   
|[Server Audit]|はい|はい|はい|はい|はい| 
|データベース監査|はい|はい|はい|はい|はい| 
|透過的なデータベースの暗号化|はい|いいえ|いいえ|いいえ|いいえ|   
|拡張キー管理|はい|いいえ|いいえ|いいえ|いいえ| 
|ユーザー定義ロール|はい|はい|はい|はい|はい| 
|包含データベース|はい|はい|はい|はい|はい| 
|バックアップの暗号化|はい|はい|いいえ|いいえ|いいえ|  

##  <a name="replication"></a><a name="Replication"></a> レプリケーション  
  
|機能|Enterprise|Standard|Web|Express with Advanced Services|Express|   
|-------------|----------------|--------------|---------|------------------------------------|------------------------| 
|異種サブスクライバー|はい|はい|いいえ|いいえ|いいえ|  
|マージ レプリケーション|はい|はい|はい (サブスクライバーのみ)|はい (サブスクライバーのみ)|はい (サブスクライバーのみ)|   
|Oracle パブリッシュ|はい|いいえ|いいえ|いいえ|いいえ| 
|ピア ツー ピア トランザクション レプリケーション|はい|いいえ|いいえ|いいえ|いいえ|   
|スナップショット レプリケーション|はい|はい|はい (サブスクライバーのみ)|はい (サブスクライバーのみ)|はい (サブスクライバーのみ)|   
|SQL Server の変更の追跡|はい|はい|はい|はい|はい| 
|トランザクション レプリケーション|はい|はい|はい (サブスクライバーのみ)|はい (サブスクライバーのみ)|はい (サブスクライバーのみ)|   
|Azure へのトランザクション レプリケーション|はい|はい|いいえ|いいえ|いいえ|   
|トランザクション レプリケーションの更新可能サブスクリプション|はい|はい|いいえ|いいえ|いいえ|  
  
##  <a name="management-tools"></a><a name="SSMS"></a> 管理ツール  
  
|機能|Enterprise|Standard|Web|Express with Advanced Services|Express| 
|-------------|----------------|--------------|---------|------------------------------------|------------------------|  
|SQL 管理オブジェクト (SMO)|はい|はい|はい|はい|はい|  
|SQL 構成マネージャー|はい|はい|はい|はい|はい|   
|SQL CMD (コマンド プロンプト ツール)|はい|はい|はい|はい|はい|      
|分散再生 - 管理ツール|はい|はい|はい|はい|いいえ|  
|分散再生 - クライアント|はい|はい|はい|いいえ|いいえ|  
|分散再生 - コントローラー|はい (最大 16 クライアント)|はい (1 クライアント)|はい (1 クライアント)|いいえ|いいえ|   
|SQL Profiler|はい|はい|いいえ <sup>1</sup>|いいえ <sup>1</sup>|いいえ <sup>1</sup>|  
|SQL Server エージェント|はい|はい|はい|いいえ|いいえ| 
|Microsoft System Center Operations Manager 管理パック|はい|はい|はい|いいえ|いいえ|  
|データベース チューニング アドバイザー (DTA)|はい|はい <sup>2</sup>|はい <sup>2</sup>|いいえ|いいえ|      
  
 <sup>1</sup> SQL Server Web、SQL Server Express、SQL Server Express with Tools、および SQL Server Express with Advanced Services は、SQL Server Standard および SQL Server Enterprise の各エディションを使用してプロファイルできます。  
  
 <sup>2</sup> チューニングは Standard Edition 機能でのみ有効です。  
  
##  <a name="rdbms-manageability"></a><a name="RDBMSM"></a> RDBMS の管理の容易性  
  
|機能|Enterprise|Standard|Web|Express with Advanced Services|Express|   
|-------------|----------------|--------------|---------|------------------------------------|------------------------|  
|ユーザー インスタンス|いいえ|いいえ|いいえ|はい|はい| 
|LocalDB|いいえ|いいえ|いいえ|はい|いいえ| 
|専用管理者接続|はい|はい|はい|はい (トレース フラグを使用)|はい (トレース フラグを使用)|   
|SysPrep のサポート <sup>1</sup>|はい|はい|はい|はい|はい| 
|PowerShell スクリプティングのサポート<sup>2</sup>|はい|はい|はい|はい|はい| 
|データ層アプリケーション コンポーネントの操作のサポート - 抽出、配置、アップグレード、削除|はい|はい|はい|はい|はい| 
|ポリシー オートメーション (変更時とスケジュールに基づいて確認)|はい|はい|はい|いいえ|いいえ|   
|パフォーマンス データ コレクター|はい|はい|はい|いいえ|いいえ| 
|複数インスタンス管理でマネージド インスタンスとして登録できる|はい|はい|はい|いいえ|いいえ|   
|標準的なパフォーマンス レポート|はい|はい|はい|いいえ|いいえ| 
|プラン ガイドおよびプラン ガイドの固定計画|はい|はい|はい|いいえ|いいえ|   
|NOEXPAND ヒントを使用したインデックス付きビューの直接クエリ|はい|はい|はい|はい|はい| 
|インデックス付きビューの自動メンテナンス|はい|はい|はい|いいえ|いいえ| 
|分散パーティション ビュー|はい|いいえ|いいえ|いいえ|いいえ| 
|並列インデックス操作|はい|いいえ|いいえ|いいえ|いいえ|  
|クエリ オプティマイザーによる自動的なインデックス付きのビュー使用|はい|いいえ|いいえ|いいえ|いいえ| 
|並列整合性チェック|はい|いいえ|いいえ|いいえ|いいえ| 
|SQL Server ユーティリティ コントロール ポイント|はい|いいえ|いいえ|いいえ|いいえ|    
|バッファー プール拡張|はい|はい|いいえ|いいえ|いいえ| 
  
 <sup>1</sup> 詳細については、「 [SysPrep を使用した SQL Server のインストールに関する注意点](../database-engine/install-windows/considerations-for-installing-sql-server-using-sysprep.md)」を参照してください。  
 
 <sup>2</sup> Linux では、Linux 上の SQL Server をターゲットとする Windows コンピューターから PowerShell スクリプトがサポートされます。 
##  <a name="development-tools"></a><a name="DevTools"></a> 開発ツール  
  
|機能|Enterprise|Standard|Web|Express with Advanced Services|Express| 
|-------------|----------------|--------------|---------|------------------------------------|------------------------| 
|Microsoft Visual Studio の統合|はい|はい|はい|はい|はい| 
|Intellisense (Transact-SQL および MDX)|はい|はい|はい|はい|はい| 
|SQL Server Data Tools (SSDT)|はい|はい|はい|はい|いいえ|    
|MDX 編集、デバッグ、およびデザイン ツール|はい|はい|いいえ|いいえ|いいえ|   
  
##  <a name="programmability"></a><a name="Programmability"></a> Programmability  
  
|機能|Enterprise|Standard|Web|Express with Advanced Services|Express 
|-------------|----------------|--------------|---------|------------------------------------|------------------------|  
|基本的な R 統合 <sup>1</sup>|はい|はい|はい|はい|いいえ|   
|高度な R 統合 <sup>2</sup>|はい|いいえ|いいえ|いいえ|いいえ| 
|基本的な Python 統合|はい|はい|はい|はい|いいえ|
|高度な Python 統合|はい|いいえ|いいえ|いいえ|いいえ| 
|Machine Learning Server (スタンドアロン)|はい|いいえ|いいえ|いいえ|いいえ|   
|PolyBase コンピューティング ノード|はい|はい <sup>3</sup>|はい <sup>3</sup>|はい <sup>3</sup>|はい <sup>3</sup> | 
|PolyBase ヘッド ノード|はい|いいえ|いいえ|いいえ|いいえ| 
|JSON|はい|はい|はい|はい|はい|   
|クエリ ストア|はい|はい|はい|はい|はい|   
|テンポラル|はい|はい|はい|はい|はい|   
|共通言語ランタイム (CLR) 統合|はい|はい|はい|はい|はい|   
|ネイティブ XML サポート|はい|はい|はい|はい|はい| 
|XML インデックスの作成|はい|はい|はい|はい|はい| 
|MERGE と UPSERT の機能|はい|はい|はい|はい|はい|   
|FILESTREAM のサポート|はい|はい|はい|はい|はい| 
|FileTable|はい|はい|はい|はい|はい| 
|日付および時刻データ型|はい|はい|はい|はい|はい|  
|国際化サポート|はい|はい|はい|はい|はい| 
|フルテキストおよびセマンティック検索|はい|はい|はい|はい|いいえ| 
|クエリ内の言語指定|はい|はい|はい|はい|いいえ|   
|Service Broker (メッセージング)|はい|はい|不可 (クライアントのみ)|不可 (クライアントのみ)|不可 (クライアントのみ)|   
|Transact-SQL エンドポイント|はい|はい|はい|いいえ|いいえ| 
|グラフ|はい|はい|はい|はい|はい|  


<sup>1</sup> 基本的な統合は 2 コアとメモリ内データ セットに制限されます。 

<sup>2</sup> 高度な統合では、使用可能な全てのコアを、ハードウェア制限の対象となるすべてのサイズのデータ セットの並列処理で使用できます。 

<sup>3</sup> 複数のコンピューティング ノードでのスケール アウトにはヘッド ノードが必要です。


## <a name="integration-services"></a><a name="IS"></a> Integration Services

[!INCLUDE[ssNoVersion_md](../includes/ssnoversion-md.md)] の各エディションがサポートする SQL Server Integration Services (SSIS) の機能については、「[SQL Server の各エディションがサポートする Integration Services の機能](../integration-services/integration-services-features-supported-by-the-editions-of-sql-server.md)」をご覧ください。

##  <a name="master-data-services"></a><a name="MDS"></a> Master Data Services  
 [!INCLUDE[ssNoVersion_md](../includes/ssnoversion-md.md)] の各エディションがサポートする [!INCLUDE[ssMDSshort_md](../includes/ssmdsshort-md.md)] と Data Quality Services の機能については、「[マスター データ サービスと SQL Server のエディションでサポートされるデータ品質サービス機能](../master-data-services/master-data-services-and-data-quality-services-features-support.md)」をご覧ください。 

  
##  <a name="data-warehouse"></a><a name="DW"></a> データ ウェアハウス  
  
|機能|Enterprise|Standard|Web|Express with Advanced Services|Express|   
|-------------|----------------|--------------|---------|------------------------------------|------------------------| 
|データベースを使用しないキューブ作成|はい|はい|いいえ|いいえ|いいえ |   
|自動生成ステージングとデータ ウェアハウス スキーマ|はい|はい|いいえ|いいえ|いいえ| 
|変更データ キャプチャ|はい|はい|いいえ|いいえ|いいえ| 
|スター結合クエリ最適化|はい|いいえ|いいえ|いいえ|いいえ| 
|スケーラブルな読み取り専用の Analysis Services 構成|はい|いいえ|いいえ|いいえ|いいえ| 
|パーティション テーブルとパーティション インデックスに対する並列クエリ処理|はい|いいえ|いいえ|いいえ|いいえ|   
|グローバル バッチ集計|はい|いいえ|いいえ|いいえ|いいえ| 

##  <a name="analysis-services"></a><a name="SSAS"></a> Analysis Services  
  
[!INCLUDE[ssNoVersion_md](../includes/ssnoversion-md.md)] の各エディションによってサポートされる Analysis Services の機能については、「[SQL Server の各エディションがサポートする Analysis Services の機能](/analysis-services/analysis-services-features-supported-by-the-editions-of-sql-server-2016)」をご覧ください。 
  
##  <a name="bi-semantic-model-multi-dimensional"></a><a name="BIMD"></a> BI セマンティック モデル (多次元)  
  
[!INCLUDE[ssNoVersion_md](../includes/ssnoversion-md.md)] の各エディションによってサポートされる Analysis Services の機能については、「[SQL Server の各エディションがサポートする Analysis Services の機能](/analysis-services/analysis-services-features-supported-by-the-editions-of-sql-server-2016)」をご覧ください。
   
##  <a name="bi-semantic-model-tabular"></a><a name="BIT"></a> BI セマンティック モデル (表形式)  
  
[!INCLUDE[ssNoVersion_md](../includes/ssnoversion-md.md)] の各エディションによってサポートされる Analysis Services の機能については、「[SQL Server の各エディションがサポートする Analysis Services の機能](/analysis-services/analysis-services-features-supported-by-the-editions-of-sql-server-2016)」をご覧ください。
  
##  <a name="power-pivot-for-sharepoint"></a><a name="PPSP"></a> Power Pivot for SharePoint  
  
[!INCLUDE[ssNoVersion_md](../includes/ssnoversion-md.md)] の各エディションによってサポートされる Power Pivot for SharePoint の機能については、「[SQL Server の各エディションがサポートする Analysis Services の機能](/analysis-services/analysis-services-features-supported-by-the-editions-of-sql-server-2016)」をご覧ください。
  
##  <a name="data-mining"></a><a name="DM"></a> データ マイニング  
  
[!INCLUDE[ssNoVersion_md](../includes/ssnoversion-md.md)] の各エディションによってサポートされるデータ マイニングの機能については、「[SQL Server の各エディションがサポートする Analysis Services の機能](/analysis-services/analysis-services-features-supported-by-the-editions-of-sql-server-2016)」をご覧ください。
  
##  <a name="reporting-services"></a><a name="SSRS"></a> Reporting Services  
  
[!INCLUDE[ssNoVersion_md](../includes/ssnoversion-md.md)] の各エディションによってサポートされる Reporting Services の機能については、「[SQL Server の各エディションがサポートする Reporting Services の機能](../reporting-services/reporting-services-features-supported-by-the-editions-of-sql-server-2016.md)」をご覧ください。

##  <a name="business-intelligence-clients"></a><a name="BIC"></a> Business Intelligence クライアント  

[!INCLUDE[ssNoVersion_md](../includes/ssnoversion-md.md)] の各エディションによってサポートされる Business Intelligence クライアントの機能については、「[SQL Server の各エディションがサポートする Analysis Services の機能](/analysis-services/analysis-services-features-supported-by-the-editions-of-sql-server-2016)」または「[SQL Server の各エディションがサポートする Reporting Services の機能](../reporting-services/reporting-services-features-supported-by-the-editions-of-sql-server-2016.md)」をご覧ください。
  
##  <a name="spatial-and-location-services"></a><a name="SLS"></a> 空間およびロケーション サービス  
  
|機能名|Enterprise|Standard|Web|Express with Advanced Services|Express|  
|------------------|----------------|--------------|---------|------------------------------------|------------------------|
|空間インデックス|はい|はい|はい|はい|はい|   
|平面データ型と測地データ型|はい|はい|はい|はい|はい| 
|高度な空間的なライブラリ|はい|はい|はい|はい|はい|   
|業界標準の空間データ形式のインポート/エクスポート|はい|はい|はい|はい|はい|   
  
##  <a name="additional-database-services"></a><a name="ADS"></a> その他のデータベース サービス  
  
|機能名|Enterprise|Standard|Web|Express with Advanced Services|Express|   
|------------------|----------------|--------------|---------|------------------------------------|------------------------| 
|[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] Migration Assistant|はい|はい|はい|はい|はい|   
|データベース メール|はい|はい|はい|いいえ|いいえ| 
  
##  <a name="other-components"></a><a name="Other"></a> その他のコンポーネント  
  
|機能名|Enterprise|Standard|Web|Express with Advanced Services|Express|   
|------------------|----------------|--------------|---------|------------------------------------|------------------------|  
|StreamInsight|StreamInsight Premium Edition|StreamInsight Standard Edition|StreamInsight Standard Edition|いいえ|いいえ| 
|StreamInsight HA|StreamInsight Premium Edition|いいえ|いいえ|いいえ|いいえ|   

> [![SSMS をダウンロードする](/analysis-services/analysis-services/media/download.png)](../ssms/download-sql-server-management-studio-ssms.md) **[最新バージョンの SQL Server Management Studio をダウンロードする](../ssms/download-sql-server-management-studio-ssms.md)**     
  
## <a name="next-steps"></a>次のステップ 
 [SQL Server の製品仕様](./index.yml)   
 [SQL Server をインストールする](../database-engine/install-windows/install-sql-server.md)  
 
[!INCLUDE[get-help-options](../includes/paragraph-content/get-help-options.md)]

[!INCLUDE[contribute-to-content](../includes/paragraph-content/contribute-to-content.md)]