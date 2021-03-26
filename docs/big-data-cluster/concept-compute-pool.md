---
title: Spark コンピューティング プールとは
titleSuffix: SQL Server big data clusters
description: この記事では、SQL Server 2019 ビッグ データ クラスターのコンピューティング プールについて説明します。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: mihaelab
ms.date: 10/15/2020
ms.topic: conceptual
ms.prod: sql
ms.technology: big-data-cluster
ms.openlocfilehash: 26efddc55b0e43f1290be1cde2b593bed4f10640
ms.sourcegitcommit: c242f423cc3b776c20268483cfab0f4be54460d4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/25/2021
ms.locfileid: "105551589"
---
# <a name="what-are-compute-pools-in-a-sql-server-big-data-cluster"></a>SQL Server ビッグ データ クラスターのコンピューティング プールとは

[!INCLUDE[SQL Server 2019](../includes/applies-to-version/sqlserver2019.md)]

この記事では、SQL Server ビッグ データ クラスターでの "*SQL Server コンピューティング プール*" の役割について説明します。 コンピューティング プールにより、SQL Server ビッグ データ クラスター用のスケールアウト コンピューティング リソースが提供されます。 これらは、SQL Server マスター インスタンスから計算作業 (中間結果セット) をオフロードするために使用されます。 以降のセクションでは、コンピューティング プールのアーキテクチャ、機能、使用シナリオについて説明します。

この 5 分間のビデオでは、コンピューティング プールの概要についてもご覧いただけます。

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Overview-Big-Data-Cluster-Compute-Pool/player?WT.mc_id=dataexposed-c9-niner]

## <a name="compute-pool-architecture"></a>コンピューティング プールのアーキテクチャ

コンピューティング プールは、Kubernetes で実行されている 1 つまたは複数のコンピューティング ポッドで構成されます。 これらのポッドの自動化された作成と管理は、[SQL Server マスター インスタンス](concept-master-instance.md)によって調整されます。 各ポッドには、一連の基本サービスと、SQL Server データベース エンジンのインスタンスが含まれています。

![コンピューティング プールのアーキテクチャ](media/concept-compute-pool/compute-pool-architecture.png)

## <a name="scale-out-groups"></a>スケール アウト グループ

コンピューティング プールは、SQL Server、Oracle、MongoDB、Teradata、HDFS などのさまざまな外部データ ソースに対する分散クエリの PolyBase スケールアウト グループとして機能することができます。 Kubernetes でコンピューティング ポッドを使用することにより、SQL Server ビッグ データ クラスターで PolyBase スケールアウト グループ用のコンピューティング ポッドの作成と構成を自動化できます。

## <a name="compute-pool-scenarios"></a>コンピューティング プールのシナリオ

コンピューティング プールが使用されるシナリオには、次のものがあります。

- マスター インスタンスに送信されたクエリによって、[記憶域プール](concept-storage-pool.md)内にある 1 つ以上のテーブルが使用される場合。

- マスター インスタンスに送信されたクエリによって、[データ プール](concept-data-pool.md)内にあるラウンド ロビン分散の 1 つ以上のテーブルが使用される場合。

- マスター インスタンスに送信されたクエリによって、SQL Server、Oracle、MongoDB、Teradata の外部データ ソースを含む **パーティション分割された** テーブルが使用される場合。 このシナリオの場合、クエリ ヒント OPTION (FORCE SCALEOUTEXECUTION) を有効にする必要があります。

- マスター インスタンスに送信されたクエリによって、[HDFS の階層](hdfs-tiering.md)内にある 1 つ以上のテーブルが使用される場合。

コンピューティング プールが使用 **されない** シナリオには、次のものがあります。

- マスター インスタンスに送信されたクエリによって、外部の Hadoop HDFS クラスター内にある 1 つ以上のテーブルが使用される場合。

- マスター インスタンスに送信されたクエリによって、Azure Blob Storage 内の 1 つ以上のテーブルが使用される場合。

- マスター インスタンスに送信されたクエリによって、SQL Server、Oracle、MongoDB、Teradata の外部データ ソースを含む **パーティション分割されていない** テーブルが使用される場合。

- クエリ ヒント OPTION (DISABLE SCALEOUTEXECUTION) が有効になっている場合。

- マスター インスタンスに送信されたクエリが、マスター インスタンスに配置されているデータベースに適用される場合。

## <a name="next-steps"></a>次のステップ

[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)] の詳細については、次のリソースを参照してください。

- [[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ver15.md)]とは](big-data-cluster-overview.md)
- [ワークショップ: Microsoft [!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)] アーキテクチャ](https://github.com/microsoft/sqlworkshops-bdc)
