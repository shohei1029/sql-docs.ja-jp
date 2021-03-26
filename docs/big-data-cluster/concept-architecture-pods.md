---
title: デプロイされるリソース
titleSuffix: SQL Server Big Data Clusters
description: SQL Server ビッグ データ クラスターに一般的に展開されるポッドの説明。
author: mihaelablendea
ms.author: mihaelab
ms.reviewer: mikeray
ms.date: 02/11/2021
ms.topic: conceptual
ms.prod: sql
ms.technology: big-data-cluster
ms.openlocfilehash: a3428763808e4bf2928c66ff42911b44cc74361d
ms.sourcegitcommit: c242f423cc3b776c20268483cfab0f4be54460d4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/25/2021
ms.locfileid: "105551593"
---
# <a name="resources-deployed-with-big-data-cluster"></a>ビッグ データ クラスターに展開されるリソース

[!INCLUDE[SQL Server 2019](../includes/applies-to-version/sqlserver2019.md)]

この記事では、SQL Server ビッグ データ クラスターで展開されるリソースについて説明します。

ビッグ データ クラスターであるクラスターでは、展開プロファイルに基づいてポッドが展開されます。 詳細については、「[既定の構成](deployment-guidance.md#configfile)」を参照してください。 

この記事では、`aks-dev-test-ha` プロファイルで展開されるポッドについて説明します。Spark プールが含まれています。 クラスターに展開されているポッドを確認するには、Kubernetes にクエリを実行します。 次の例では、特定の名前空間にあるポッドの一覧が返されます。

```bash
kubectl get pods -n <namespace>
```

`<namespace>` をビッグ データ クラスターの名前に置き換えます。 

詳細については、「[Kubernetes に [!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)]を展開する方法](deployment-guidance.md#configfile)」を参照してください。

次の図は、ビッグ データ クラスターに展開されたコンポーネントを示しています。

:::image type="content" source="media/big-data-cluster-overview/architecture-diagram-overview.png" alt-text="big-data-cluster-diagram":::

アーキテクチャの詳細については、「[[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)]とは](big-data-cluster-overview.md)」を参照してください。

## <a name="deployed-pods"></a>展開されたポッド

次の表は、ビッグ データ クラスターに展開されたポッドを示しています。

|名前  |領域|
|---------|---------|
|`control-<nnnn>`        |[コントロール](#control)|
|`controldb-<#>`         |[コントロール](#control)|
|`controlwd-<nnnn>`      |[コントロール](#control)|
|`logsdb-<#>`            |[コントロール](#control)|
|`logsui-<nnnn>`         |[コントロール](#control)|
|`metricsdb-<#>`         |[コントロール](#control)|
|`metricsdc-<nnnn>`      |[コントロール](#control)|
|`metricsui-<nnnn>`      |[コントロール](#control)|
|`mgmtproxy-<nnnn>`      |[コントロール](#control)|
|`zookeeper-<#>`         |[コントロール](#control)|
|`dns-<nnnn>`            |[コントロール](#control)|
|`master-<#n>`           |[マスター インスタンス](#master-instance)|
|`operator-<nnnn>`       |[マスター インスタンス](#master-instance)
|`compute-<#n>-<#m>`     |[コンピューティング プール](#compute-pool)|
|`data-<#>-<#>`          |[データ プール](#data-pool) |
|`storage-<#>-<#>`       |[記憶域プール](#storage-pool)|
|`nmnode-<#>-<#>`        |[記憶域プール](#storage-pool)|
|`sparkhead-<#>`         |[記憶域プール](#storage-pool)|
|`appproxy-<#m>`         |[アプリケーション プール](#application-pool)|
|`gateway-<#>`           |[ゲートウェイ サービス](#gateway-service)|

すべての BDC クラスターにすべてのポッドが含まれているわけではありません。 高可用性または Active Directory 統合を使用する展開には、特定のポッドが含まれます。 

### <a name="high-availability-specific-pods"></a>高可用性に固有のポッド:

- `operator-<nnnn>`
- `zookeeper-<#>`

### <a name="active-directory-specific-pods"></a>Active Directory に固有のポッド:

- `dns-<nnnn>`

次のセクションでは、ポッドについて説明し、各ポッドのコンテナーの一覧を示します。

## <a name="control"></a>コントロール

コントロール ポッドでは制御サービスが提供されます。

|ポッド名 |Count| Kubernetes コントローラーの種類 | Containers |
|--------|----|------|--------|-------|
|`control-#`|1| ReplicaSet |- `controller`<br><br>- `security-support`<br><br>- `fluentbit`
|`controldb`|1| StatefulSet |- `mssql-server`<br><br>- `fluentbit`
|`controlwd`|1|  ReplicaSet |- `controlwatchdog`
|`logsdb-#` |1| StatefulSet |- `elasticsearch`
|`logsui`   |1| ReplicaSet |- `kibana`
|`metricsdb-#`|1| StatefulSet |- `influxdb`
|`metricsdc`|Kubernetes ノードごとに 1 つ。 | DaemonSet |- `telegraf` |
|`metricsui-nnnn`|1| ReplicaSet |- `grafana` |
|`mgmtproxy-nnnn`|1| ReplicaSet |- `service-proxy`<br><br>- `fluentbit`|
|`dns-nnnn`|Active Directory 統合の場合は 0 または 1| ReplicaSet |- `dns`<br><br>- `fluentbit`|

## <a name="master-instance"></a>マスター インスタンス

`master-<#n>` は SQL Server マスター インスタンスです。

- DDL を使用してデータ プールを管理します
- DML を使用してデータ プール内のデータを操作します
- データ プールに対する分析クエリ実行をオフロードします

|ポッド名 |Count| Kubernetes コントローラーの種類 | Containers |
|--------|----|------|--------|
|`master-<#n>`|高可用性の場合は 1 つ以上。| StatefulSet|- `mssql-server`<br><br>- `fluentbit`<br><br>- `collectd`<br><br>- `mssql-ha-supervisor` <sup>*</sup>|
|`operator`<sup>*</sup>| 高可用性の場合は 0 または 1 | ReplicaSet |- `mssql-ha-operator`

<sup>*</sup> 高可用性の展開のみ。 オペレーターによって、SQL Server および可用性グループのリソースのカスタム リソース定義が実装され、登録されます。 オペレーターが展開されると、オペレーターによって、Kubernetes クラスターに展開された SQL Server リソースに関する通知のリスナーとしてオペレーター自身が登録されます。 `mssql-ha-supervisor` によって可用性グループがサポートされます。

各 `master` ポッドには、SQL Server のインスタンスが 1 つ含まれています。 高可用性の展開には 3 つのポッドが含まれます。 各ポッドには、SQL Server インスタンスと、SQL Server Always On 可用性グループ内のデータベースが含まれています。

展開時に、ワークロードに応じて追加のポッドを含めます。 

## <a name="compute-pool"></a>計算プール

コンピューティング プールによって、コンピューティング用の SQL Server インスタンスが提供されます。

|ポッド名 |Count| Kubernetes コントローラーの種類 | Containers |
|--------|----|------|--------|
|`compute-<#n>-<#m>`|1 つ以上。| StatefulSet |- `mssql-server`<br><br>- `fluentbit`<br><br>- `collectd`

- `#n` でコンピューティング プールを識別します。
- `#m` でプール内のインスタンス ID を識別します。

コンピューティング プール SQL Server インスタンスはステートレスです。 それらに必要なのは `tempdb` 用のストレージのみです。

展開時に、ワークロードに応じて追加のポッドを含めます。 

## <a name="data-pool"></a>データ プール

データ プールによって、ストレージとコンピューティングのための SQL Server インスタンスが提供されます。

|ポッド名 |Count| Kubernetes コントローラーの種類 | Containers |
|--------|----|------|--------|-------|
|`data-<#n>-<#m>` | 0 以上 | StatefulSet | -` mssql-server` <br><br>- `fluentbit`<br><br>- `collectd`|

- `#n` でデータベース ロックを識別します。
- `#m` でプール内のインスタンス ID を識別します。

ワークロードに応じて、展開時に追加のポッドを含めます。

## <a name="storage-pool"></a>記憶域プール

記憶域プールによって、Spark を介したデータ インジェスト、HDFS 内のストレージ、HDFS と SQL Server エンドポイントを介したデータ アクセスが提供されます。

|ポッド名 |Count| Kubernetes コントローラーの種類 | Containers |
|--------|----|------|--------|
|`storage-0-#`|1 つ以上。 ワークロードに応じて、展開時に追加のポッドを含めます。 | StatefulSet |- `hadoop`<br><br>- `mssql-server`<br><br>- `fluentbit`<br><br>
|`nmnode-0-#`|高可用性の場合は 1 つ以上| StatefulSet |- `hadoop`<br><br>- `fluentbit`
|`sparkehead-#`|高可用性の場合は 1 つ以上| StatefulSet |- `hadoop-yarn-jobhistory`<br><br>- `hadoop-livy-sparkhistory`<br><br>- `hadoop-hivemetastore`<br><br>-- `fluentbit`
|`zookeeper`|高可用性の場合は 0 または 3。 | StatefulSet |- `zookeeper`<br><br>- `fluentbit`

## <a name="application-pool"></a>アプリケーション プール

アプリケーション プールは、一部のテスト構成プロファイルに含まれています。 アプリケーション プールによって、ビッグ データ クラスター用のアプリケーションを展開するときに定義したアプリケーション サービス プロキシがホストされます。 

`appproxy` は、アプリケーション プールのアプリケーションの前に置かれている Web API です。 それによってユーザーが認証された後、要求がアプリケーションにルーティングされます。

|ポッド名 | Kubernetes コントローラーの種類 | Containers  |
|--------|----|------|
|`appproxy`| ReplicaSet |- `app-service-proxy`<br><br>- `fluentbit`

詳細については、「[ビッグ データ クラスターへのアプリケーション展開とは](concept-application-deployment.md)」を参照してください。

ワークロードに応じて、展開時に追加のポッドを含めます。 

## <a name="gateway-service"></a>ゲートウェイ サービス

ゲートウェイ サービスによって、Spark、HDFS、[Yarn](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html)、Yarn UI、Spark UI への Knox ゲートウェイが提供されます。

|ポッド名 | Kubernetes コントローラーの種類 | Containers |
|--------|-----|-----|
|`gateway-<#>`| StatefulSet |- `knox`<br><br>- `fluentbit`

1 つのゲートウェイだけがサポートされます。

## <a name="open-source-container-references"></a>オープンソースのコンテナー参照

特定のオープンソース プロジェクトとバージョンについては、「[オープンソース ソフトウェア リファレンス](reference-open-source-software.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)] の詳細については、次のリソースを参照してください。

- [[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ver15.md)]とは](big-data-cluster-overview.md)
- [ワークショップ: Microsoft [!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)] アーキテクチャ](https://github.com/microsoft/sqlworkshops-bdc)
- [Kubernetes に [!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)] を展開する方法](deployment-guidance.md#configfile)
