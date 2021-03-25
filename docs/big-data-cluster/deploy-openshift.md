---
title: OpenShift にデプロイする
titleSuffix: SQL Server Big Data Cluster
description: OpenShift 上の SQL Server ビッグ データ クラスターをアップグレードする方法について説明します。
author: mihaelablendea
ms.author: mihaelab
ms.reviewer: mikeray
ms.date: 06/22/2020
ms.topic: conceptual
ms.prod: sql
ms.technology: big-data-cluster
ms.openlocfilehash: 918dd8e746984d9bdc6a619cc2692bdfbab158a0
ms.sourcegitcommit: bf7577b3448b7cb0e336808f1112c44fa18c6f33
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104610825"
---
# <a name="deploy-big-data-clusters-2019-on-openshift-on-premises-and-azure-red-hat-openshift"></a>オンプレミスの OpenShift および Azure Red Hat OpenShift に [!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)]をデプロイする

[!INCLUDE[SQL Server 2019](../includes/applies-to-version/sqlserver2019.md)]

この記事では、オンプレミスまたは Azure Red Hat OpenShift (ARO) 上の OpenShift 環境に SQL Server ビッグ データ クラスター (BDC) をデプロイする方法について説明します。

> [!TIP]
> ARO を使用してサンプル環境をブートストラップし、このプラットフォームに BDC を簡単にデプロイするには、[こちら](quickstart-big-data-cluster-deploy-aro.md)で入手できる Python スクリプトを使用できます。


SQL Server 2019 CU5 では、OpenShift での SQL Server ビッグ データ クラスターのサポートが導入されています。 オンプレミスの OpenShift または Azure Red Hat OpenShift (ARO) に、ビッグ データ クラスターをデプロイできます。 デプロイには、OpenShift クラスター バージョン 4.3 以降が必要です。 デプロイのワークフローは、他の Kubernetes ベースのプラットフォーム ([kubeadm](deploy-with-kubeadm.md) や [AKS](deploy-on-aks.md)) でのデプロイに似ていますが、いくつかの違いがあります。 この違いは主に、アプリケーションをルート以外のユーザーとして実行することと、名前空間 BDC のデプロイに使用されるセキュリティ コンテキストに関連するものです。

オンプレミスに OpenShift クラスターをデプロイする場合は、[Red Hat OpenShift のドキュメント](https://docs.openshift.com/container-platform/4.3/release_notes/ocp-4-3-release-notes.html#ocp-4-3-installation-and-upgrade)を参照してください。 ARO のデプロイについては、「[Azure Red Hat OpenShift](/azure/openshift/intro-openshift)」を参照してください。

この記事では、OpenShift プラットフォームに固有のデプロイ手順について説明し、ターゲット環境にアクセスするためのオプションと、ビッグ データ クラスターのデプロイに使用する名前空間を示します。

## <a name="pre-requisites"></a>前提条件

> [!IMPORTANT]
> 以下の前提条件は、これらのクラスター レベル オブジェクトを作成するための十分なアクセス許可を持つ OpenShift クラスター管理者 (クラスター管理者クラスター ロール) によって実行される必要があります。 OpenShift でのクラスター ロールの詳細については、「[Using RBAC to define and apply permissions](https://docs.openshift.com/container-platform/4.4/authentication/using-rbac.html)」 (アクセス許可を定義して適用するための RBAC の使用) を参照してください。

1. OpenShift の `pidsLimit` 設定が、SQL Server のワークロードに対応するように更新されていることを確認します。 OpenShift の既定値は、ワークロードのような運用環境には低すぎます。 少なくとも `4096` で開始しますが、最適な値は SQL Server での `max worker threads` の設定と、OpenShift ホスト ノード上の CPU プロセッサの数によって決まります。 
    - OpenShift クラスターに対する `pidsLimit` を更新する方法については、[こちらの手順]( https://github.com/openshift/machine-config-operator/blob/master/docs/ContainerRuntimeConfigDesign.md)を参照してください。 `4.3.5` より前のバージョンの OpenShift には欠陥があり、更新した値が反映されないことに注意してください。 必ず、OpenShift を最新バージョンにアップグレードしてください。 
    - 環境および予定されている SQL Server ワークロードに応じた最適な値を計算するには、次の推定と例を使用できます。

    |プロセッサの数|既定の最大ワーカー スレッド数|既定のプロセッサあたりワーカー数|pidsLimit の最小値|
    |--------------------|--------------------------|-----------------------------|-----------------------|
    |          64        |           512            |             16              | 512 + (64 *16) = 1536 |
    |         128        |           512            |             32              | 512 + (128*32) = 4608 |

    > [!NOTE]
    > 他のプロセス (例: バックアップ、CLR、フルテキスト、SQLAgent) によってもオーバーヘッドが加わるので、推定値にバッファーを追加します。

1. カスタム セキュリティ コンテキスト制約 (SCC) [`bdc-scc.yaml`](#bdc-sccyaml-file) をダウンロードします。

    ```console
    curl https://raw.githubusercontent.com/microsoft/sql-server-samples/master/samples/features/sql-big-data-cluster/deployment/openshift/bdc-scc.yaml -o bdc-scc.yaml
    ```

1. SCC をクラスターに適用します。

    ```console
    oc apply -f bdc-scc.yaml
    ```

    > [!NOTE]
    > BDC に対するカスタム SCC は、OpenShift に組み込まれた "`nonroot`" SCC と追加のアクセス許可に基づきます。 OpenShift でのセキュリティ コンテキスト制約の詳細については、「[Managing Security Context Constraints](https://docs.openshift.com/container-platform/4.3/authentication/managing-security-context-constraints.html)」 (セキュリティ コンテキスト制約の管理) を参照してください。 "`nonroot`" SCC に追加する、ビッグ データ クラスターに必要なアクセス許可の詳細については、[こちら](https://aka.ms/sql-bdc-openshift-security)のホワイトペーパーをダウンロードしてください。

3. 名前空間とプロジェクトを作成します。

   ```console
   oc new-project <namespaceName>
   ```

4. カスタム SCC を、BDC がデプロイされる名前空間内のサービス アカウントとバインドします。

   ```console
   oc create clusterrole bdc-role --verb=use --resource=scc --resource-name=bdc-scc -n <namespaceName>
   oc create rolebinding bdc-rbac --clusterrole=bdc-role --group=system:serviceaccounts:mssql-bdc
   ```

5. BDC をデプロイするユーザーに、適切なアクセス許可を割り当てます。 次のいずれかの操作を行います。 

   - BDC をデプロイするユーザーがクラスター管理者ロールを持っている場合は、「[ビッグ データ クラスターをデプロイする](#deploy-big-data-cluster)」に進んでください。

   - BDC をデプロイするユーザーが名前空間管理者である場合は、作成される名前空間に対するクラスター管理者ローカル ロールをユーザーに割り当てます。 これは、ビッグ データ クラスターのデプロイと管理を行うユーザーが名前空間レベルの管理アクセス許可を持つ場合に推奨されるオプションです。

   ```console
   oc create rolebinding bdc-user-rbac --clusterrole=cluster-admin --user=<userName> -n <namespaceName>
   ```

   ビッグ データ クラスターをデプロイするユーザーは、OpenShift コンソールにログインする必要があります。

   ```console
   oc login -u <deployingUser> -p <password>
   ```

## <a name="deploy-big-data-cluster"></a>ビッグ データ クラスターをデプロイする

1. 最新の [azdata](../azdata/install/deploy-install-azdata.md) をインストールします。

1. ターゲット環境 (オンプレミスの OpenShift または ARO) とデプロイ シナリオに応じて、OpenShift 用の組み込み構成ファイルの 1 つを複製します。 組み込みの構成ファイルで OpenShift に固有の設定については、後の「*デプロイ構成ファイルでの OpenShift 固有の設定*」セクションを参照してください。 使用可能な構成ファイルの詳細については、[展開のガイダンス](deployment-guidance.md)に関するページを参照してください。

   使用可能なすべての組み込み構成ファイルの一覧を表示します。

   ```console
   azdata bdc config list
   ```

   組み込み構成ファイルの 1 つを複製するには、次のコマンドを実行します (必要に応じて、対象のプラットフォームまたはシナリオに基づいてプロファイルを置き換えることができます)。

   ```console
   azdata bdc config init --source openshift-dev-test --target custom-openshift
   ```

   ARO へのデプロイの場合は、`aro-` プロファイルのいずれかを使用して開始します。それには、この環境に適した `serviceType` と `storageClass` の既定値が含まれます。 次に例を示します。

   ```console
   azdata bdc config init --source aro-dev-test --target custom-openshift
   ```

1. 構成ファイル control.json と bdc.json をカスタマイズします。 さまざまなユース ケースでのサポートされるカスタマイズのガイドについては、次のリソースを参照してください。

   - [Storage](concept-data-persistence.md)
   - [AD 関連の設定](active-directory-deploy.md)
   - [その他のカスタマイズ](deployment-custom-configuration.md)

   > [!NOTE]
   > BDC 向け Azure Active Directory との統合はサポートされていないため、ARO にデプロイするときはこの認証方法を使用できません。

1. [環境変数](deployment-guidance.md#env)を設定します

1. ビッグ データ クラスターをデプロイします

   ```console
   azdata bdc create --config custom-openshift --accept-eula yes
   ```

1. デプロイが正常に完了したら、ログインして外部のクラスター エンドポイントの一覧を表示することができます。

   ```console
      azdata login -n mssql-cluster
      azdata bdc endpoint list
   ```

## <a name="openshift-specific-settings-in-the-deployment-configuration-files"></a>デプロイ構成ファイルでの OpenShift 固有の設定

SQL Server 2019 CU5 では、ポッドとノードのメトリックのコレクションを制御する 2 つの機能スイッチが導入されました。 これらのパラメーターは、OpenShift 用の組み込みプロファイルでは既定で `false` に設定されています。これは、監視コンテナーでは[特権付きセキュリティ コンテキスト](https://www.openshift.com/blog/managing-sccs-in-openshift)が必要であるためです。これにより、BDC がデプロイされる名前空間に対するセキュリティ制約の一部が緩和されます。

```json
    "security": {
      "allowNodeMetricsCollection": false,
      "allowPodMetricsCollection": false
}
```

ARO での既定のストレージ クラスの名前は managed-premium です (これに対し、AKS での既定のストレージ クラスの名前は default です)。 これは、`aro-dev-test` と `aro-dev-test-ha` に対応する `control.json` で確認できます。

```json
    },
    "storage": {
      "data": {
        "className": "managed-premium",
        "accessMode": "ReadWriteOnce",
        "size": "15Gi"
      },
      "logs": {
        "className": "managed-premium",
        "accessMode": "ReadWriteOnce",
        "size": "10Gi"
      }
```

## <a name="bdc-sccyaml-file"></a>`bdc-scc.yaml` ファイル

このデプロイの SCC ファイルは次のとおりです。

:::code language="yaml" source="../../sql-server-samples/samples/features/sql-big-data-cluster/deployment/openshift/bdc-scc.yaml":::

## <a name="next-steps"></a>次のステップ

[チュートリアル:SQL Server ビッグ データ クラスターにサンプル データを読み込む](tutorial-load-sample-data.md)
