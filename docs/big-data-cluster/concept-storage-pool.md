---
title: 記憶域プールとは
titleSuffix: SQL Server big data clusters
description: SQL Server 2019 ビッグ データ クラスターの SQL Server ストレージ プールのロールと、SQL ストレージ プールのアーキテクチャと機能について説明します。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: mihaelab
ms.date: 10/01/2020
ms.topic: conceptual
ms.prod: sql
ms.technology: big-data-cluster
ms.openlocfilehash: 25877bc5f69af865a32389e5c293ace7cab09c10
ms.sourcegitcommit: c242f423cc3b776c20268483cfab0f4be54460d4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/25/2021
ms.locfileid: "105551534"
---
# <a name="what-is-the-storage-pool-in-a-sql-server-big-data-cluster"></a>SQL Server ビッグ データ クラスターにおける記憶域プールとは

[!INCLUDE[SQL Server 2019](../includes/applies-to-version/sqlserver2019.md)]

この記事では、SQL Server ビッグ データ クラスターでの "*SQL Server 記憶域プール*" の役割について説明します。 以下のセクションでは、記憶域プールのアーキテクチャと機能について説明します。

## <a name="storage-pool-architecture"></a>記憶域プールのアーキテクチャ

記憶域プールは、SQL Server ビッグ データ クラスター内のローカル HDFS (Hadoop) クラスターです。 非構造化および半構造化データ用の永続的ストレージを提供します。 記憶域プールには、Parquet や区切りテキストなどのデータ ファイルを格納できます。 記憶域を永続化するために、プール内の各ポッドには永続ボリュームがアタッチされています。 記憶域プール ファイルには、SQL Server を通して [PolyBase](../relational-databases/polybase/polybase-guide.md) 経由でアクセスすることも、Apache Knox ゲートウェイを使用して直接アクセスすることもできます。

クラシック HDFS セットアップは、記憶域が接続されている一連の汎用ハードウェア コンピューターで構成されています。 フォールト トレランスを実現し、並列処理を活用するために、データは複数のノード間にブロック単位で分散されます。 クラスターに含まれるノードの中の 1 つは名前ノードとして機能し、そこにはデータ ノードに配置されているファイルに関するメタデータ情報が格納されます。

![クラシック HDFS セットアップ](media/concept-storage-pool/classic-hdfs-setup.png)

記憶域プールは、HDFS クラスターのメンバーである記憶域ノードで構成されます。 1 つまたは複数の Kubernetes ポッドが実行され、次のコンテナーが各ホストによってホストされます。

- 永続ボリューム (記憶域) にリンクされた Hadoop コンテナー。 この種類のすべてのコンテナーが一緒になって、Hadoop クラスターが形成されます。 Hadoop コンテナー内には、オンデマンドの Apache Spark ワーカー プロセスを作成できる YARN ノード マネージャー プロセスがあります。 この Spark ヘッド ノードによって、Hive メタストア、Spark 履歴、YARN ジョブ履歴の各コンテナーがホストされます。
- OpenRowSet テクノロジを使用して HDFS からデータを読み取る SQL Server インスタンス。
- メトリック データを収集するための `collectd`。
- ログ データを収集するための `fluentbit`。

![記憶域プールのアーキテクチャ](media/concept-storage-pool/scale-big-data-on-demand.png)

## <a name="responsibilities"></a>役割

記憶域ノードの役割は次のとおりです。

- Apache Spark を通したデータ インジェスト。
- HDFS のデータ ストレージ (Parquet および区切りテキスト形式)。 HDFS では、HDFS データが SQL BDC 内のすべての記憶域ノードに分散されるため、データの永続性も提供されます。
- HDFS と SQL Server エンドポイントを使用したデータ アクセス。

## <a name="accessing-data"></a>データへのアクセス

記憶域プール内のデータにアクセスするには、主に次の方法があります。

- Spark ジョブ。
- SQL Server 外部テーブルの利用。これにより、PolyBase コンピューティング ノードと HDFS ノード内で実行される SQL Server インスタンスを使用してデータのクエリを実行することができます。

次のものを使用して HDFS とやりとりすることもできます。

- Azure Data Studio。
- [!INCLUDE [azure-data-cli-azdata](../includes/azure-data-cli-azdata.md)].
- Hadoop コンテナーにコマンドを発行する kubectl。
- HDFS http ゲートウェイ。

## <a name="next-steps"></a>次のステップ

[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)] の詳細については、次のリソースを参照してください。

- [[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ver15.md)]とは](big-data-cluster-overview.md)
- [ワークショップ: Microsoft [!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)] アーキテクチャ](https://github.com/microsoft/sqlworkshops-bdc)
