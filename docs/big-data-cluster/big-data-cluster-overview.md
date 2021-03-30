---
title: ビッグ データ クラスターとは
titleSuffix: SQL Server Big Data Clusters
description: Kubernetes 上で動作し、リレーショナル データと HDFS データの両方にスケールアウト オプションを提供する SQL Server ビッグ データ クラスターについて説明します。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: mihaelab
ms.date: 01/07/2020
ms.topic: overview
ms.prod: sql
ms.technology: big-data-cluster
ms.openlocfilehash: 5e3b94698712f8359c51f925b70e148917610360
ms.sourcegitcommit: c242f423cc3b776c20268483cfab0f4be54460d4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/25/2021
ms.locfileid: "105551273"
---
# <a name="what-are-big-data-clusters-2019"></a>[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)]とは

[!INCLUDE[SQL Server 2019](../includes/applies-to-version/sqlserver2019.md)]

[!INCLUDE[SQL Server 2019](../includes/sssql19-md.md)] 以降、[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)]を使用すると、Kubernetes 上で動作する SQL Server、Spark、および HDFS コンテナーのスケーラブルなクラスターを展開できます。 これらのコンポーネントを並行して実行し、Transact-SQL または Spark からのビッグ データの読み取り、書き込み、処理を行うことができるので、高価値のリレーショナル データと大量のビッグ データを簡単に組み合わせて分析できます。

[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)] を使用して以下の操作を実行できます。

- Kubernetes で実行している SQL Server、Spark、HDFS コンテナーの[スケーラブルなクラスターを配置します](./deploy-get-started.md)。 
- Transact-SQL または Spark からビッグ データの読み取り、書き込み、処理を行います。
- 大量のビッグ データを使用して、価値の高いリレーショナル データを簡単に組み合わせて分析します。
- 外部データ ソースを照会します。
- SQL Server によって管理される HDFS にビッグ データを格納します。
- クラスターを介して複数の外部データ ソースからデータを照会します。
- AI、機械学習、その他の分析タスクにデータを使用します。
- [!INCLUDE[big-data-clusters](../includes/ssbigdataclusters-nover.md)] に[アプリケーションを展開して実行](./concept-application-deployment.md)します。
- [PolyBase](../relational-databases/polybase/polybase-guide.md) を使用してデータを仮想化します。 外部テーブルを使用して、外部の SQL Server、Oracle、Teradata、MongoDB、ODBC データ ソースからデータを照会します。
- Always On 可用性グループ テクノロジを使用して、SQL Server マスター インスタンスとすべてのデータベースの高可用性を実現します。

最新リリースの新機能と既知の問題の詳細については、[リリース ノート](release-notes-big-data-cluster.md)を参照してください。   

ビッグ データ クラスターの詳細については、[ビッグ データ クラスターに関する FAQ](big-data-cluster-faq.yml) に関するページを参照してください。

## <a name="scenarios"></a>シナリオ

[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)]を使用すると、ビッグ データの操作が柔軟になります。 外部データ ソースに対してクエリを実行する、SQL Server が管理する HDFS にビッグ データを格納する、またはクラスターを介して複数の外部データ ソースのデータに対してクエリを実行することができます。 このデータは、AI、機械学習、その他の分析タスクに使用できます。 以下に、これらのシナリオについて詳しく説明します。

### <a name="data-virtualization"></a>データの仮想化

[PolyBase](../relational-databases/polybase/polybase-guide.md) を利用することで、[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)]では、データを移動またはコピーすることなく、外部データ ソースに対してクエリを実行できます。 [!INCLUDE[SQL Server 2019](../includes/sssql19-md.md)] では、データ ソースに新しいコネクタが導入されています。

![データの仮想化](media/big-data-cluster-overview/data-virtualization.png)

### <a name="data-lake"></a>データ レイク

SQL Server ビッグ データ クラスターには、スケーラブルな HDFS *記憶域プール* が含まれています。 これは、複数の外部ソースから取り込まれた可能性があるビッグ データを格納するために使用できます。 ビッグ データ クラスターの HDFS にビッグ データが格納されたら、そのデータを分析してクエリを実行し、リレーショナル データと組み合わせることができます。

![データ レイク](media/big-data-cluster-overview/data-lake.png)

### <a name="scale-out-data-mart"></a>スケールアウト データ マート

[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)]には、任意のデータの分析パフォーマンスを向上させるスケールアウト コンピューティングとストレージが用意されています。 さまざまなソースのデータを取り込み、さらに分析するためにキャッシュとして *データ プール* ノード全体に分散することができます。

![データ マート](media/big-data-cluster-overview/data-mart.png)

### <a name="integrated-ai-and-machine-learning"></a>AI と機械学習の統合

[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)]を使用すると、HDFS 記憶域プールとデータ プールに格納されているデータに対して AI と機械学習タスクを実行できます。 R、Python、Scala、または Java を使用し、Spark だけでなく SQL Server の組み込みの AI ツールを使用できます。

![AI と ML](media/big-data-cluster-overview/ai-ml-spark.png)

### <a name="management-and-monitoring"></a>管理と監視

管理と監視は、コマンド ライン ツール、API、ポータル、および動的管理ビューを組み合わせて実行できます。

[Azure Data Studio](../azure-data-studio/what-is-azure-data-studio.md) を使用すると、ビッグ データ クラスターに対してさまざまなタスクを実行できます。
- 一般的な管理タスク用の組み込みスニペット。
- HDFS の参照、ファイルのアップロード、ファイルのプレビュー、およびディレクトリの作成を行う機能。
- Jupyter 互換ノートブックを作成、開く、および実行する機能。
- 外部データ ソースの作成を簡易化するデータ仮想化ウィザード (**データ仮想化の拡張機能** によって有効化されます)。

## <a name="architecture"></a><a id="architecture"></a> アーキテクチャ

SQL Server ビッグ データ クラスターは、[Kubernetes](https://kubernetes.io/docs/concepts/) によって調整された Linux コンテナーのクラスターです。

### <a name="kubernetes-concepts"></a>Kubernetes の概念

Kubernetes はオープン ソースのコンテナー オーケストレーターであり、必要に応じてコンテナーの展開を拡張できます。 次の表では、重要な Kubernetes 用語をいくつか定義します。

|期間|説明|
|:--|:--|
| **クラスター** | Kubernetes クラスターは、ノードと呼ばれる一連のマシンです。 1 つのノードがクラスターを制御し、マスター ノードに指定されます。残りのノードはワーカー ノードです。 Kubernetes マスターは、ワーカー間で作業を分散し、クラスターの正常性を監視する役割を担います。 |
| **Node** | ノードによって、コンテナー化されたアプリケーションが実行されます。 物理マシンまたは仮想マシンのいずれかです。 Kubernetes クラスターには、物理マシンと仮想マシンの両方のノードを含めることができます。 |
| **ポッド** | ポッドは、Kubernetes のアトミック展開単位です。 ポッドは、アプリケーションの実行に必要な 1 つ以上のコンテナーと関連するリソースの論理グループです。 各ポッドはノード上で実行されます。ノードでは、1 つ以上のポッドを実行できます。 Kubernetes マスターによって、クラスター内のノードにポッドが自動的に割り当てられます。 |
| &nbsp; ||

[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)]では、Kubernetes は [!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)]の状態の責任を負います。Kubernetes では、クラスター ノードの構築と構成が行われ、ポッドがノードに割り当てられ、クラスターの正常性が監視されます。

### <a name="big-data-clusters-architecture"></a>ビッグ データ クラスターのアーキテクチャ

次の図は、SQL Server ビッグ データ クラスターのコンポーネントを示しています。

![アーキテクチャの概要](media/big-data-cluster-overview/architecture-diagram-overview.png)

### <a name="controller"></a><a id="controlplane"></a> コントローラー

コントローラーには、クラスターの管理とセキュリティ機能があります。 これには、制御サービス、構成ストア、および Kibana、Grafana、Elasticsearch などのその他のクラスターレベルサービスが含まれています。

### <a name="compute-pool"></a><a id="computeplane"></a> コンピューティング プール

コンピューティング プールは、クラスターにコンピューティング リソースを提供します。 これには SQL Server on Linux ポッドを実行するノードが含まれます。 コンピューティング プール内のポッドは、特定の処理タスクのために *SQL コンピューティング インスタンス* に分割されます。 

### <a name="data-pool"></a><a id="dataplane"></a> データ プール

データ プールは、データの永続化とキャッシュに使用されます。 データ プールは、SQL Server on Linux を実行している 1 つ以上のポッドで構成されます。 これは、SQL クエリまたは Spark ジョブからデータを取り込むために使用されます。 SQL Server ビッグ データ クラスターのデータ マートは、データ プールに保持されます。 

### <a name="storage-pool"></a>記憶域プール

記憶域プールは、SQL Server on Linux、Spark、および HDFS で構成される記憶域プール ポッドで構成されます。 SQL Server ビッグ データ クラスター内のすべての記憶域ノードは、HDFS クラスターのメンバーです。

> [!TIP]
> ビッグ データ クラスターのアーキテクチャとインストールの詳細については、「[ワークショップ:Microsoft [!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)] アーキテクチャ](https://github.com/microsoft/sqlworkshops-bdc)」を参照してください。

## <a name="next-steps"></a>次のステップ

* [!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)] の展開の詳細については、「[[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)]の概要](deploy-get-started.md)」を参照してください。    
* [ビッグ データ クラスターに関する FAQ](big-data-cluster-faq.yml) に関するページを確認します。  