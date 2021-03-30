---
title: 展開のガイダンス
titleSuffix: SQL Server Big Data Clusters
description: Kubernetes 上に SQL Server ビッグ データ クラスターを展開する方法について説明します。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: mihaelab
ms.date: 06/22/2020
ms.topic: conceptual
ms.prod: sql
ms.technology: big-data-cluster
ms.openlocfilehash: 9ca8eff773bff7900a1228665d65d911b52d5a8f
ms.sourcegitcommit: c242f423cc3b776c20268483cfab0f4be54460d4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/25/2021
ms.locfileid: "105551484"
---
# <a name="how-to-deploy-big-data-clusters-2019-on-kubernetes"></a>Kubernetes 上に [!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)]を展開する方法

[!INCLUDE[SQL Server 2019](../includes/applies-to-version/sqlserver2019.md)]

SQL Server ビッグ データ クラスターは、Kubernetes クラスター上に Docker コンテナーとして展開されます。 ここでは、セットアップと構成の手順の概要を説明します。

- 単一の VM、VM のクラスター、Azure Kubernetes Service (AKS)、Red Hat OpenShift、または Azure Red Hat OpenShift (ARO) で Kubernetes クラスターを設定します。
- クライアント コンピューター上にクラスター構成ツール [!INCLUDE [azure-data-cli-azdata](../includes/azure-data-cli-azdata.md)] をインストールする。
- Kubernetes クラスターに SQL Server ビッグ データ クラスターを展開する。

## <a name="supported-platforms"></a>サポートされているプラットフォーム

SQL Server ビッグ データ クラスターを展開するために検証されるさまざまな Kubernetes プラットフォームの完全な一覧については、「[サポートされているプラットフォーム](release-notes-big-data-cluster.md#supported-platforms)」を参照してください。

### <a name="sql-server-editions"></a>SQL Server のエディション

|Edition|Notes|
|---------|---------|
|Enterprise<br/>Standard<br/>Developer| ビッグ データ クラスターのエディションは、SQL Server マスター インスタンスのエディションによって決まります。 展開時に、Developer エディションが既定で展開されます。 エディションは、展開後に変更できます。 [SQL Server マスター インスタンスの構成](./configure-sql-server-master-instance.md)に関するページを参照してください。 |

## <a name="kubernetes"></a><a id="prereqs"></a> Kubernetes

### <a name="kubernetes-cluster-setup"></a><a id="kubernetes"></a> Kubernetes クラスターの設定

上記の前提条件を満たす Kubernetes クラスターを既にお持ちの場合は、[展開の手順](#deploy)に直接進むことができます。 このセクションでは、Kubernetes の概念の基本的な理解を前提としています。  Kubernetes の詳細については、[Kubernetes のドキュメント](https://kubernetes.io/docs/home)を参照してください。

次の方法で Kubernetes を展開できます。

| Kubernetes の展開先: | 説明 | Link |
|---|---|---|
| **Azure Kubernetes Services (AKS)** | Azure にあるマネージド Kubernetes コンテナー サービス。 | [手順](deploy-on-aks.md) |
| **単一または複数のマシン (`kubeadm`)** | `kubeadm` を使用して物理または仮想マシン上に展開された Kubernetes クラスター | [手順](deploy-with-kubeadm.md) |
|**Azure Red Hat OpenShift** | Azure で実行されている OpenShift のマネージド オファリング。 | [手順](deploy-openshift.md)|
|**Red Hat OpenShift**|ハイブリッド クラウド、エンタープライズ Kubernetes アプリケーション プラットフォーム。| [手順](deploy-openshift.md)|

> [!TIP]
> また、1 つの手順で AKS とビッグ データ クラスターの展開をスクリプト化することもできます。 詳細については、[Python スクリプト](quickstart-big-data-cluster-deploy.md)または Azure Data Studio の[ノートブック](notebooks-deploy.md)において、この操作を行う方法を確認してください。

### <a name="verify-kubernetes-configuration"></a>Kubernetes の構成を確認する

クラスター構成を表示するには、`kubectl` コマンドを実行します。 Kubectl が正しいクラスター コンテキストを指していることを確認します。

```bash
kubectl config view
```

> [!Important] 
> `kubeadm` を使用してブートストラップしたマルチ ノード Kuberntes クラスターに展開する場合は、ビッグ データ クラスターの展開を開始する前に、展開の対象となっているすべての Kubernetes ノード間でクロックが同期されていることを確認します。 ビッグ データ クラスターには、時間の影響を受け、時計のずれが原因で不正な状態になる可能性があるさまざまなサービス用に、正常性プロパティが組み込まれています。

Kubernetes クラスターを構成したら、新しい SQL Server ビッグ データ クラスターの展開に進むことができます。 以前のリリースからアップグレードする場合は、「[[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)]をアップグレードする方法](deployment-upgrade.md)」を参照してください。

## <a name="ensure-you-have-storage-configured"></a>ストレージを構成していることを確認する

ほとんどのビッグ データの場合、クラスターの展開のために永続ストレージを用意します。 この段階では、BDC を展開する前に、Kubernetes クラスターに永続ストレージを与える方法を計画しておく必要があります。

AKS で展開する場合、ストレージ セットアップは必要ありません。 AKS には、動的にプロビジョニングするためのストレージ クラスが組み込まれています。 ストレージクラス (`default` または `managed-premium`) は、展開構成ファイルでカスタマイズできます。 組み込みプロファイルでは、`default` ストレージ クラスが使用されます。 `kubeadm` を使用して展開した Kubernetes クラスターで展開する場合、望ましいスケールのクラスターのために十分なストレージがあることを確認し、使用できるように構成しておく必要があります。 ストレージの使用方法をカスタマイズする場合、続行前にそれを行ってください。 「[Kubernetes 上の SQL Server ビッグ データ クラスターでのデータ永続化](concept-data-persistence.md)」を参照してください。

## <a name="install-sql-server-2019-big-data-tools"></a>SQL Server 2019 のビッグ データ ツールをインストールする

SQL Server 2019 ビッグ データ クラスターを展開する前に、まず、[ビッグ データ ツールをインストール](deploy-big-data-tools.md)します。

- [!INCLUDE [azure-data-cli-azdata](../includes/azure-data-cli-azdata.md)]
- `kubectl`
- Azure Data Studio
- Azure Data Studio 用の[データ仮想化の拡張機能](../azure-data-studio/extensions/data-virtualization-extension.md)


## <a name="deployment-overview"></a><a id="deploy"></a> 展開の概要

ほとんどのビッグ データ クラスター設定は、JSON 展開構成ファイルに定義されています。 AKS および `kubeadm` によって作成された Kubernetes クラスター用の既定の展開プロファイルを使用しても、セットアップ中に使用する独自の展開構成ファイルをカスタマイズしてもかまいません。 セキュリティ上の理由から、認証設定は環境変数を介して渡されます。

以降のセクションでは、ビッグ データ クラスターの展開を構成する方法と、一般的なカスタマイズの例について詳しく説明します。 また、たとえば VS Code のようなエディターを使用して、カスタムの展開構成ファイルをいつでも編集できます。

## <a name="default-configurations"></a><a id="configfile"></a> 既定の構成

ビッグ データ クラスターの展開オプションは、JSON 構成ファイルに定義されています。 [!INCLUDE [azure-data-cli-azdata](../includes/azure-data-cli-azdata.md)] に用意されている組み込みの展開プロファイルから、クラスター展開のカスタマイズを開始できます。 

> [!NOTE]
> ビッグ データ クラスターの展開に必要なコンテナー イメージは、Microsoft Container Registry (`mcr.microsoft.com`) の `mssql/bdc` リポジトリにホストされます。 既定では、これらの設定は、[!INCLUDE [azure-data-cli-azdata](../includes/azure-data-cli-azdata.md)] に含まれる各展開プロファイルの `control.json` 構成ファイルに既に含まれています。 また、各リリースのコンテナー イメージ タグも、同じ構成ファイルにあらかじめ設定されています。 コンテナー イメージを独自のプライベート コンテナー レジストリにプルするか、コンテナー レジストリ/リポジトリの設定を変更する必要がある場合は、[オフライン インストールに関する記事](deploy-offline.md)に記載されている手順に従ってください

次のコマンドを実行して、使用可能なテンプレートを確認します。

```
azdata bdc config list -o table 
```

SQL Server 2019 CU5 では、次のテンプレートを使用できます。 

| 展開プロファイル | Kubernetes 環境 |
|---|---|
| `aks-dev-test` | Azure Kubernetes Service (AKS) に SQL Server ビッグ データ クラスターを展開します|
| `aks-dev-test-ha` | Azure Kubernetes Service (AKS) に SQL Server ビッグ データ クラスターを展開します。 SQL Server マスターや HDFS の名前ノードなどのミッション クリティカルなサービスは、高可用性を実現するように構成されています。|
| `aro-dev-test`|開発とテストのために、SQL Server ビッグ データ クラスターを Azure Red Hat OpenShift に展開します。 <br/><br/>SQL Server 2019 CU 5 で導入されています。|
| `aro-dev-test-ha`|開発とテストのために、高可用性を備えた SQL Server ビッグ データ クラスターを Red Hat OpenShift クラスターに展開します。 <br/><br/>SQL Server 2019 CU 5 で導入されています。|
| `kubeadm-dev-test` | 1 つまたは複数の物理マシンまたは仮想マシンを使用して、kubeadm によって作成された Kubernetes クラスター上に SQL Server ビッグ データ クラスターを展開します。|
| `kubeadm-prod`| 1 つまたは複数の物理マシンまたは仮想マシンを使用して、kubeadm によって作成された Kubernetes クラスター上に SQL Server ビッグ データ クラスターを展開します。 ビッグ データ クラスター サービスを Active Directory と統合できるようにするには、このテンプレートを使用します。 SQL Server マスター インスタンスや HDFS 名前ノードなどのミッション クリティカルなサービスは、高可用性構成で展開されます。  |
| `openshift-dev-test`|開発とテストのために、SQL Server ビッグ データ クラスターを Red Hat OpenShift クラスターに展開します。 <br/><br/>SQL Server 2019 CU 5 で導入されています。|
| `openshift-prod`|高可用性を備えた SQL Server ビッグ データ クラスターを Red Hat OpenShift クラスターに展開します。 <br/><br/>SQL Server 2019 CU 5 で導入されています。|

`azdata bdc create` を実行することで、ビッグ データ クラスターを展開できます。 これにより、既定の構成の 1 つを選択するよう求めるメッセージが表示され、展開へと進みます。

初めて [!INCLUDE [azure-data-cli-azdata](../includes/azure-data-cli-azdata.md)] を実行するときは、使用許諾契約書 (EULA) に同意するために `--accept-eula=yes` を含める必要があります。

```bash
azdata bdc create --accept-eula=yes
```

このシナリオでは、パスワードなど、既定の構成に含まれない設定をすべて入力するように求められます。 

> [!IMPORTANT]
> ビッグ データ クラスターの既定の名前は `mssql-cluster` です。 `-n` パラメーターを使用して Kubernetes 名前空間を指定する任意の `kubectl` コマンドを実行するために、このことを知っておくことは重要です。

## <a name="custom-configurations"></a><a id="customconfig"></a> カスタムの構成

実行する予定のワークロードに合わせて展開をカスタマイズすることもできます。 展開後にビッグ データ クラスター サービスのスケール (レプリカの数) またはストレージ設定を変更できないため、展開構成を慎重に計画して容量の問題を回避する必要があります。 展開をカスタマイズするには、次の手順に従います。

1. Kubernetes 環境に適合する標準の展開プロファイルの 1 つを使用して開始します。 `azdata bdc config list` コマンドを使用して、それらの一覧を表示できます。

   ```bash
   azdata bdc config list
   ```

1. 展開をカスタマイズするには、`azdata bdc config init` コマンドを使用して、展開プロファイルのコピーを作成します。 たとえば、次のコマンドでは、`aks-dev-test` 展開構成ファイルのコピーが `custom` という名前のターゲット ディレクトリに作成されます。

   ```bash
   azdata bdc config init --source aks-dev-test --target custom
   ```

   >[!TIP]
   >`--target` には、`--source` パラメーターに基づいて、構成ファイル `bdc.json` および `control.json` を含むディレクトリを指定します。

1. 展開構成プロファイルでの設定をカスタマイズするために、VS Code などの JSON ファイルの編集に適したツール上で、展開構成ファイルを編集できます。 スクリプト化されたオートメーションの場合、`azdata bdc config` コマンドを使用してカスタムの展開プロファイルを編集することもできます。 たとえば、次のコマンドでは、カスタムの展開プロファイルを変更して、展開されたクラスターの名前を既定 (`mssql-cluster`) から `test-cluster` に変えています。  

   ```bash
   azdata bdc config replace --config-file custom/bdc.json --json-values "metadata.name=test-cluster"
   ```

   > [!TIP]
   > また、*azdata create bdc* コマンドに *--name* パラメータ―を使用して、展開時にクラスター名を渡すことも可能です。 コマンド内のパラメーターは、構成ファイルでの値よりも優先されます。
   >
   > JSON パスを検索するための便利なツールとして、[JSONPath Online Evaluator](https://jsonpath.com/) があります。
   >
   キーと値のペアを渡すだけでなく、インライン JSON 値を指定したり、JSON パッチ ファイルを渡したりすることもできます。 詳細については、「[ビッグ データ クラスターの展開設定を構成する](deployment-custom-configuration.md)」を参照してください。

1. カスタム構成ファイルを `azdata bdc create` に渡します。 必須の[環境変数](#env)を必ず設定することに注意してください。そうしないと、ターミナルから値の入力を求められます。

   ```bash
   azdata bdc create --config-profile custom --accept-eula yes
   ```

> 展開構成ファイルの構造の詳細については、[展開構成ファイルのリファレンス](reference-deployment-config.md)に関するページを参照してください。 他の構成例については、「[ビッグ データ クラスターの展開設定を構成する](deployment-custom-configuration.md)」を参照してください。

## <a name="environment-variables"></a><a id="env"></a> 環境変数

次の環境変数は、展開構成ファイルに保存されないセキュリティ設定に使用されます。 資格情報以外の Docker 設定は、構成ファイル内で設定できることに注意してください。

| 環境変数 | 要件 |説明 |
|---|---|---|
| `AZDATA_USERNAME` | 必須 |SQL Server ビッグ データ クラスター管理者のユーザー名。 同じ名前の sysadmin ログインが SQL Server マスター インスタンス内に作成されます。 セキュリティのベスト プラクティスとして、`sa` アカウントは無効になっています。 <br/><br/>[!INCLUDE [big-data-cluster-root-user](../includes/big-data-cluster-root-user.md)]|
| `AZDATA_PASSWORD` | 必須 |上記で作成したユーザー アカウントのパスワード。 SQL Server 2019 CU5 より前に展開されたクラスターでは、Knox ゲートウェイと HDFS のセキュリティ保護のために、`root` ユーザーに同じパスワードが使用されます。 |
| `ACCEPT_EULA`| [!INCLUDE [azure-data-cli-azdata](../includes/azure-data-cli-azdata.md)] を初めて使用する場合は必須| "yes" に設定します。 環境変数として設定された場合、SQL Server と [!INCLUDE [azure-data-cli-azdata](../includes/azure-data-cli-azdata.md)] の両方に EULA が適用されます。 環境変数として設定されない場合、[!INCLUDE [azure-data-cli-azdata](../includes/azure-data-cli-azdata.md)] コマンドの初めての使用時に `--accept-eula=yes` を含めることができます。|
| `DOCKER_USERNAME` | 省略可能 | コンテナー イメージがプライベート リポジトリに格納されている場合に、それらにアクセスするためのユーザー名。 ビッグ データ クラスターの展開にプライベート Docker リポジトリを使用する方法の詳細については、[オフライン展開](deploy-offline.md)に関するトピックを参照してください。|
| `DOCKER_PASSWORD` | 省略可能 |上記のプライベート リポジトリにアクセスするためのパスワード。 |

これらの環境変数は、`azdata bdc create` を呼び出す前に設定される必要があります。 設定されていない変数がある場合は、入力を求められます。

次の例は、Linux (bash) と Windows (PowerShell) 用に環境変数を設定する方法を示しています。

```bash
export AZDATA_USERNAME=admin
export AZDATA_PASSWORD=<password>
export ACCEPT_EULA=yes
```

```PowerShell
SET AZDATA_USERNAME=admin
SET AZDATA_PASSWORD=<password>
```

> [!NOTE]
> SQL Server 2019 CU5 より前に展開されたクラスターでは、上記のパスワードを持つ Knox ゲートウェイの `root` ユーザーを使用する必要があります。 `root` は、この基本認証 (ユーザー名とパスワード) でサポートされている唯一のユーザーです。
> [!INCLUDE [big-data-cluster-root-user](../includes/big-data-cluster-root-user.md)]
> 基本認証を使用して SQL Server に接続するには、AZDATA_USERNAME および AZDATA_PASSWORD の[環境変数](#env)と同じ値を使用します。 

環境変数を設定したら、`azdata bdc create` を実行して展開をトリガーする必要があります。 この例では、上記で作成したクラスター構成プロファイルを使用します。

```bash
azdata bdc create --config-profile custom --accept-eula yes
```

次のガイドラインに注意してください。

- 特殊文字が含まれている場合は、必ずパスワードを二重引用符で囲みます。 `AZDATA_PASSWORD` は自由に設定できますが、必ずパスワードを十分に複雑にして、`!`、`&`、`'` 文字は使用しないでください。 二重引用符の区切り記号は bash コマンドでしか機能しないことに注意してください。
- `AZDATA_USERNAME` ログインは、セットアップ時に作成される SQL Server マスター インスタンス上でのシステム管理者です。 SQL Server のコンテナーを作成した後、そのコンテナーで `echo $AZDATA_PASSWORD` を実行すると、指定した環境変数 `AZDATA_PASSWORD` が検索できるようになります。 セキュリティ上の理由から、ベスト プラクティスとしてパスワードを変更してください。

## <a name="unattended-install"></a><a id="unattended"></a> 自動実行インストール

無人展開の場合は、必要なすべての環境変数を設定して、構成ファイルを使用し、`--accept-eula yes` パラメーターを指定して `azdata bdc create` コマンドを呼び出す必要があります。 前のセクションの例では、無人インストールに対応した構文を示しています。

## <a name="monitor-the-deployment"></a><a id="monitor"></a> 展開を監視する

クラスターのブートストラップ中に、クライアントのコマンド ウィンドウに展開の状態が返されます。 展開プロセス中に、コントローラー ポッドを待機している一連のメッセージが表示されます。

```output
Waiting for cluster controller to start.
```

15 から 30 分以内に、コントローラー ポッドが実行中になっていることが通知されるはずです。

```output
Cluster controller endpoint is available at 11.111.111.11:30080.
Cluster control plane is ready.
```

> [!IMPORTANT]
> ビッグ データ クラスターのコンポーネントに対応するコンテナー イメージのダウンロードに必要な時間によって、展開全体に時間がかかる場合があります。 ただし、数時間もかかることはありません。 展開時に問題が発生した場合は、「[[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)]の監視とトラブルシューティング](cluster-troubleshooting-commands.md)」を参照してください。

展開が完了すると、出力に成功したことが通知されます。

```output
Cluster deployed successfully.
```

> [!TIP]
> カスタム構成によって変更されない限り、展開されたビッグ データ クラスターの既定の名前は `mssql-cluster` になります。

## <a name="retrieve-endpoints"></a><a id="endpoints"></a> エンドポイントを取得する

展開スクリプトが正常に完了したら、次の手順を使用して、ビッグ データ クラスターに対応する外部エンドポイントのアドレスを取得できます。

1. 展開後、展開の標準出力から、または次の `kubectl` コマンドの EXTERNAL-IP 出力を確認して、コントローラー エンドポイントの IP アドレスを検索します。

   ```bash
   kubectl get svc controller-svc-external -n <your-big-data-cluster-name>
   ```

   > [!TIP]
   > 展開中に既定の名前を変更しなかった場合、前のコマンドにある `-n mssql-cluster` を使用します。 `mssql-cluster` は、ビッグ データ クラスターの既定の名前です。

1. [azdata login](../azdata/reference/reference-azdata.md) を使用して、ビッグ データ クラスターにログインします。 `--endpoint` パラメーターをコントローラー エンドポイントの外部 IP アドレスに設定します。

   ```bash
   azdata login --endpoint https://<ip-address-of-controller-svc-external>:30080 --username <user-name>
   ```

   展開中にビッグ データ クラスター管理者 (AZDATA_USERNAME と AZDATA_PASSWORD) に対して構成したユーザー名とパスワードを指定します。

   > [!TIP]
   > Kubernetes クラスター管理者であり、クラスター構成ファイル (Kube 構成ファイル) にアクセスできる場合は、対象の Kubernetes クラスターを指すように現在のコンテキストを構成できます。 この場合は、`azdata login -n <namespaceName>` を使用してログインできます。ここで、`namespace` はビッグ データ クラスター名です。 ログイン コマンド内で指定されていない場合は、資格情報の入力を求められます。
   
1. [azdata bdc endpoint list](../azdata/reference/reference-azdata-bdc-endpoint.md) を実行して、各エンドポイントの説明とそれに対応する IP アドレスおよびポート値を示した一覧を取得します。 

   ```bash
   azdata bdc endpoint list -o table
   ```

   次の一覧は、このコマンドからの出力例を示しています。

   ```output
   Description                                             Endpoint                                                   Ip              Name               Port    Protocol
   ------------------------------------------------------  ---------------------------------------------------------  --------------  -----------------  ------  ----------
   Gateway to access HDFS files, Spark                     https://11.111.111.111:30443                               11.111.111.111  gateway            30443   https
   Spark Jobs Management and Monitoring Dashboard          https://11.111.111.111:30443/gateway/default/sparkhistory  11.111.111.111  spark-history      30443   https
   Spark Diagnostics and Monitoring Dashboard              https://11.111.111.111:30443/gateway/default/yarn          11.111.111.111  yarn-ui            30443   https
   Application Proxy                                       https://11.111.111.111:30778                               11.111.111.111  app-proxy          30778   https
   Management Proxy                                        https://11.111.111.111:30777                               11.111.111.111  mgmtproxy          30777   https
   Log Search Dashboard                                    https://11.111.111.111:30777/kibana                        11.111.111.111  logsui             30777   https
   Metrics Dashboard                                       https://11.111.111.111:30777/grafana                       11.111.111.111  metricsui          30777   https
   Cluster Management Service                              https://11.111.111.111:30080                               11.111.111.111  controller         30080   https
   SQL Server Master Instance Front-End                    11.111.111.111,31433                                       11.111.111.111  sql-server-master  31433   tcp
   HDFS File System Proxy                                  https://11.111.111.111:30443/gateway/default/webhdfs/v1    11.111.111.111  webhdfs            30443   https
   Proxy for running Spark statements, jobs, applications  https://11.111.111.111:30443/gateway/default/livy/v1       11.111.111.111  livy               30443   https
   ```

次の `kubectl` コマンドを実行して、クラスターに展開されたすべてのサービス エンドポイントを取得することもできます。

```bash
kubectl get svc -n <your-big-data-cluster-name>
```

## <a name="verify-the-cluster-status"></a><a id="status"></a> クラスターの状態を確認する

展開後に、[azdata bdc status show](../azdata/reference/reference-azdata-bdc-status.md) コマンドを使用して、クラスターの状態を確認できます。

```bash
azdata bdc status show
```

> [!TIP]
> status コマンドを実行するには、最初に、前述のエンドポイントのセクションに示した `azdata login` コマンドを使ってログインする必要があります。

このコマンドからの出力例を、次に示します。

```output
Bdc: ready                                                                                                                                                                                                          Health Status:  healthy
 ===========================================================================================================================================================================================================================================
 Services: ready                                                                                                                                                                                                     Health Status:  healthy
 -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
 Servicename    State    Healthstatus    Details

 sql            ready    healthy         -
 hdfs           ready    healthy         -
 spark          ready    healthy         -
 control        ready    healthy         -
 gateway        ready    healthy         -
 app            ready    healthy         -


 Sql Services: ready                                                                                                                                                                                                 Health Status:  healthy
 -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
 Resourcename    State    Healthstatus    Details

 master          ready    healthy         StatefulSet master is healthy
 compute-0       ready    healthy         StatefulSet compute-0 is healthy
 data-0          ready    healthy         StatefulSet data-0 is healthy
 storage-0       ready    healthy         StatefulSet storage-0 is healthy


 Hdfs Services: ready                                                                                                                                                                                                Health Status:  healthy
 -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
 Resourcename    State    Healthstatus    Details

 nmnode-0        ready    healthy         StatefulSet nmnode-0 is healthy
 storage-0       ready    healthy         StatefulSet storage-0 is healthy
 sparkhead       ready    healthy         StatefulSet sparkhead is healthy


 Spark Services: ready                                                                                                                                                                                               Health Status:  healthy
 -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
 Resourcename    State    Healthstatus    Details

 sparkhead       ready    healthy         StatefulSet sparkhead is healthy
 storage-0       ready    healthy         StatefulSet storage-0 is healthy


 Control Services: ready                                                                                                                                                                                             Health Status:  healthy
 -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
 Resourcename    State    Healthstatus    Details

 controldb       ready    healthy         -
 control         ready    healthy         -
 metricsdc       ready    healthy         DaemonSet metricsdc is healthy
 metricsui       ready    healthy         ReplicaSet metricsui is healthy
 metricsdb       ready    healthy         StatefulSet metricsdb is healthy
 logsui          ready    healthy         ReplicaSet logsui is healthy
 logsdb          ready    healthy         StatefulSet logsdb is healthy
 mgmtproxy       ready    healthy         ReplicaSet mgmtproxy is healthy


 Gateway Services: ready                                                                                                                                                                                             Health Status:  healthy
 -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
 Resourcename    State    Healthstatus    Details

 gateway         ready    healthy         StatefulSet gateway is healthy


 App Services: ready                                                                                                                                                                                                 Health Status:  healthy
 -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
 Resourcename    State    Healthstatus    Details

 appproxy        ready    healthy         ReplicaSet appproxy is healthy
```

次のコマンドを使用してさらに詳しい状態を取得することもできます。

- [azdata bdc control status show](../azdata/reference/reference-azdata-bdc-control-status.md) では、コントロール管理サービスに関連付けられているすべてのコンポーネントの正常性状態が返されます
```
azdata bdc control status show
```
サンプル出力:
```output
Control: ready                                                                                                                                                                                                      Health Status:  healthy
 ===========================================================================================================================================================================================================================================
 Resources: ready                                                                                                                                                                                                    Health Status:  healthy
 -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
 Resourcename    State    Healthstatus    Details

 controldb       ready    healthy         -
 control         ready    healthy         -
 metricsdc       ready    healthy         DaemonSet metricsdc is healthy
 metricsui       ready    healthy         ReplicaSet metricsui is healthy
 metricsdb       ready    healthy         StatefulSet metricsdb is healthy
 logsui          ready    healthy         ReplicaSet logsui is healthy
 logsdb          ready    healthy         StatefulSet logsdb is healthy
 mgmtproxy       ready    healthy         ReplicaSet mgmtproxy is healthy
```

- `azdata bdc sql status show` では、SQL Server サービスを持つすべてのリソースの正常性状態が返されます
```
azdata bdc sql status show
```
サンプル出力:
```output
Sql: ready                                                                                                                                                                                                          Health Status:  healthy
 ===========================================================================================================================================================================================================================================
 Resources: ready                                                                                                                                                                                                    Health Status:  healthy
 -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
 Resourcename    State    Healthstatus    Details

 master          ready    healthy         StatefulSet master is healthy
 compute-0       ready    healthy         StatefulSet compute-0 is healthy
 data-0          ready    healthy         StatefulSet data-0 is healthy
 storage-0       ready    healthy         StatefulSet storage-0 is healthy
```

> [!IMPORTANT]
> `--all` パラメーターを使用する場合、これらのコマンドからの出力には、より詳細な分析のための Kibana および Grafana ダッシュボードへの URL が含まれています。

[!INCLUDE [azure-data-cli-azdata](../includes/azure-data-cli-azdata.md)] を使用するほかに、Azure Data Studio を利用してエンドポイントと状態情報の両方を検索することもできます。 [!INCLUDE [azure-data-cli-azdata](../includes/azure-data-cli-azdata.md)] および Azure Data Studio を利用したクラスターの状態の表示に関する詳細については、「[ビッグ データ クラスターの状態を表示する方法](view-cluster-status.md)」を参照してください。

## <a name="connect-to-the-cluster"></a><a id="connect"></a> クラスターに接続する

ビッグ データ クラスターに接続する方法の詳細については、「[Azure Data Studio を利用して SQL Server ビッグ データ クラスターに接続する](connect-to-big-data-cluster.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

ビッグ データ クラスターの展開に関する詳細については、次の資料を参照してください。

- [ビッグ データ クラスターの展開設定を構成する](deployment-custom-configuration.md)
- [SQL Server ビッグ データ クラスターのオフライン展開を実行する](deploy-offline.md)
- [ワークショップ: Microsoft [!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)] アーキテクチャ](https://github.com/microsoft/sqlworkshops-bdc)
- [ビッグ データ クラスターに関する FAQ](big-data-cluster-faq.yml)  