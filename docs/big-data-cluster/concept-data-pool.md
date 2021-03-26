---
title: データ プールとは
titleSuffix: SQL Server big data clusters
description: SQL Server ビッグ データ クラスター内の SQL Server データ プールの役割、および SQL データ プールのアーキテクチャと機能について説明します。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: mihaelab
ms.date: 08/21/2019
ms.topic: conceptual
ms.prod: sql
ms.technology: big-data-cluster
ms.openlocfilehash: bd42bed16500e58f6fb1873f28ccd5abce5d30ab
ms.sourcegitcommit: c242f423cc3b776c20268483cfab0f4be54460d4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/25/2021
ms.locfileid: "105551573"
---
# <a name="what-are-data-pools-in-a-sql-server-big-data-cluster"></a>SQL Server ビッグ データ クラスターのデータ プールとは

[!INCLUDE[SQL Server 2019](../includes/applies-to-version/sqlserver2019.md)]

この記事では、SQL Server ビッグ データ クラスターでの "*SQL Server データ プール*" の役割について説明します。 以下のセクションでは、データ プールのアーキテクチャ、機能、使用シナリオについて説明します。

この 5 分間のビデオでは、データ プールについて説明し、データ プールからデータのクエリを実行する方法について説明します。

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Querying-Data-from-Big-Data-Cluster-Data-Pool/player?WT.mc_id=dataexposed-c9-niner]

## <a name="data-pool-architecture"></a>データ プールのアーキテクチャ

データ プールは、クラスターに永続的な SQL Server ストレージを提供する 1 つ以上の SQL Server データ プール インスタンスで構成されます。 これにより、外部データ ソースおよび作業のオフロードに対してキャッシュ データをクエリするパフォーマンスを向上させることができます。 データは、T-SQL クエリまたは Spark ジョブのいずれかを使用してデータ プールに取り込まれます。 大きなデータセット全体のパフォーマンスを向上させるために、取り込まれたデータはシャードに分散され、プール内のすべての SQL Server インスタンスに格納されます。 サポートされているディストリビューション方法はラウンド ロビン方式であり、レプリケートされます。 読み取りアクセスの最適化では、各データ プール インスタンスの各テーブルにクラスター化列ストア インデックスが作成されます。 データ プールは、[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)] のスケールアウト データ マートとして機能します。

![スケールアウト データ マート](media/concept-data-pool/data-virtualization-improvements.png)

データ プール内の SQL Server インスタンスへのアクセスは、SQL Server マスター インスタンスから管理されます。 データ プールに対する外部データ ソースが、データ キャッシュを格納する PolyBase 外部テーブルと共に作成されます。 バックグラウンドでは、コントローラーによって、外部テーブルに一致するテーブルを含むデータベースがデータ プールに作成されます。 SQL Server マスター インスタンスからは、ワークフローは透過的になります。コントローラーにより、特定の外部テーブル要求が、コンピューティング プールを介してデータ プール内の SQL Server インスタンスにリダイレクトされ、クエリを実行して結果セットが返されます。 データ プール内のデータは、取り込みまたはクエリのみが可能であり、変更することはできません。 そのため、データを更新するには、テーブルを削除してから、テーブルを再作成し、その後データを再設定する必要があります。

## <a name="data-pool-scenarios"></a>データ プールのシナリオ

 レポートを作成する目的は、データ プールの一般的なシナリオです。 たとえば、複数の PolyBase データ ソースを結合する複雑なクエリは、週次レポートで使用され、データ プールにオフロードできます。 キャッシュ データにより、ローカルの高速コンピューティングが提供され、元のデータセットに戻る必要がなくなります。 同様に、定期的に更新する必要があるダッシュボード データも、最適化されたレポート作成のためにデータ プールにキャッシュすることができます。 また、Machine Learning の繰り返し探索も、データ プール内のデータセットをキャッシュすることからメリットが得られます。

## <a name="next-steps"></a>次のステップ

[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)] の詳細については、次のリソースを参照してください。

- [[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ver15.md)]とは](big-data-cluster-overview.md)
- [ワークショップ: Microsoft [!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)] アーキテクチャ](https://github.com/microsoft/sqlworkshops-bdc)
