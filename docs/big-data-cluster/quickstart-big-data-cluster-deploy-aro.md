---
title: Azure Red Hat OpenShift Python スクリプトで展開する
titleSuffix: SQL Server Big Data Clusters
description: 展開スクリプトを使用して SQL Server ビッグ データ クラスターを Azure Red Hat OpenShift (ARO) に展開する方法について説明します。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: mihaelab
ms.date: 06/22/2020
ms.topic: conceptual
ms.prod: sql
ms.technology: big-data-cluster
ms.openlocfilehash: 29e59beaf30a3de548da2c0ed591cd0223e65dc4
ms.sourcegitcommit: bf7577b3448b7cb0e336808f1112c44fa18c6f33
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104610990"
---
# <a name="use-a-python-script-to-deploy-a-sql-server-big-data-cluster-on-azure-red-hat-openshift-aro"></a>python スクリプトを使用して SQL Server ビッグ データ クラスターを Azure Red Hat OpenShift (ARO) に展開する

[!INCLUDE[SQL Server 2019](../includes/applies-to-version/sqlserver2019.md)]

このチュートリアルでは、サンプルの Python 展開スクリプトを使用して、[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ver15.md)] を [Azure Red Hat OpenShift (ARO)](/azure/virtual-machines/linux/openshift-get-started) に展開します。 この展開オプションは SQL Server 2019 CU5 以降でサポートされています。

> [!TIP]
> ARO は、ビッグ データ クラスター用の Kubernetes をホストするための選択肢の 1 つにすぎません。 その他の展開オプションと、展開オプションをカスタマイズする方法の詳細については、「[Kubernetes に [!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)]を展開する方法](deployment-guidance.md)」を参照してください。


> [!WARNING]
> 組み込みのストレージ クラスである *managed-premium* によって作成された永続ボリュームには、*Delete* の再要求ポリシーが用意されています。 そのため、SQL Server ビッグ データ クラスターを削除すると、永続ボリューム要求は、永続ボリュームと同様に削除されます。 [ストレージの概念](/azure/aks/concepts-storage/#storage-classes)に関する記事に示すように、*Retain* 再要求ポリシーによって azure-disk プロビジョナーを使用して、カスタム ストレージ クラスを作成してください。 下のスクリプトでは、*managed-premium* ストレージ クラスを使用しています。 詳細については、[データ永続化](concept-data-persistence.md)に関するトピックをご覧ください。

ここで使用される既定のビッグ データ クラスターの展開は、1 つの SQL マスター インスタンス、1 つのコンピューティング プール インスタンス、2 つのデータ プール インスタンス、2 つの記憶域プール インスタンスで構成されています。 データは、ARO の既定の記憶域クラスを使用する Kubernetes 永続ボリュームを使用して永続化されます。 このチュートリアルで使用する既定の構成は、開発/テスト環境に適しています。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。
- [oc](https://docs.openshift.com/container-platform/4.4/cli_reference/openshift_cli/getting-started-cli.html)
- [Python 最小バージョン 3.0](https://www.python.org/downloads)
- [`az` CLI](/cli/azure/install-azure-cli/)
- [[!INCLUDE [azure-data-cli-azdata](../includes/azure-data-cli-azdata.md)]](../azdata/install/deploy-install-azdata.md)
- **Azure Data Studio**

## <a name="log-in-to-your-azure-account"></a>Azure アカウントにログインする

このスクリプトでは、ARO クラスターの作成を自動化するために Azure CLI を使用します。 スクリプトを実行する前に、少なくとも 1 回は Azure CLI を使用して Azure アカウントにログインする必要があります。 コマンド プロンプトで次のコマンドを実行します。

```azurecli
az login
```

## <a name="instructions"></a>Instructions

1. Python スクリプト `deploy-sql-big-data-aro.py` と yaml ファイル `bdc-scc.yaml` の両方をダウンロードします。

   >これらのファイルは、この記事の以下のセクションに示しています。
   - [`deploy-sql-big-data-aro.py`](#deploy-sql-big-data-aropy)
   - [`bdc-scc.yaml`](#bdc-sccyaml)

1. 以下を使用してスクリプトを実行します。

```console
python deploy-sql-big-data-aro.py
```

プロンプトが表示されたら、Azure サブスクリプション ID と Azure リソース グループにご自分の入力内容を指定して、リソースを作成します。 必要に応じて、他の構成にご自分の入力内容を指定したり、用意されている既定値を使用したりすることもできます。 次に例を示します。

- `azure_region`
- OpenShift ワーカー ノード用の `vm_size`。 基本的なシナリオを検証すると同時に最適なエクスペリエンスを実現するには、クラスター内のすべてのワーカー ノードに対して少なくとも 8 つの vCPU と 64 GB メモリを使用することをお勧めします。 このスクリプトでは、`Standard_D8s_v3` と 3 つのワーカー ノードを既定値として使用します。 ビッグ データ クラスター用の既定のサイズ構成では、すべてのコンポーネントを対象にして永続ボリューム要求のために約 24 個のディスクも使用されます。
- OpenShift クラスター展開のためのネットワーク構成 - 各パラメーターの詳細については、[ARO の展開に関する記事](\azure\openshift\tutorial-create-cluster)を参照してください。
- `cluster_name` - この値は、ARO クラスターと、ARO の上に作成される SQL Server ビッグ データ クラスターの両方に使用されます。 SQL ビッグ データ クラスターの名前は、Kubernetes 名前空間になることに注意してください。
- `username ` - これは、コントローラー管理者アカウント、SQL Server マスター インスタンス アカウント、およびゲートウェイに対する展開中にプロビジョニングされるアカウントのユーザー名です。 ベスト プラクティスとして、`sa` SQL Server アカウントが自動的に無効になることに注意してください。
- `password` - すべてのアカウントで同じ値が使用されます。

SQL Server ビッグ データ クラスターが ARO に展開されるようになりました。 これで、Azure Data Studio を使用してクラスターに接続できるようになりました。 詳細については、「[Azure Data Studio を使用して SQL Server ビッグ データ クラスターに接続する](connect-to-big-data-cluster.md)」を参照してください。

## <a name="clean-up"></a>クリーンアップ

Azure で [!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)] をテストしている場合は、予期しない料金が発生しないように、完了時に ARO クラスターを削除するようにします。 クラスターを引き続き使用する場合は、削除しないでください。

> [!WARNING]
> 次の手順では、ARO クラスターを破棄します。これにより、SQL Server ビッグ データ クラスターも削除されます。 保持するデータベースまたは HDFS データがある場合は、そのデータをバックアップしてからクラスターを削除してください。

次の Azure CLI コマンドを実行して、Azure のビッグ データ クラスターと ARO サービスを削除します (`<resource group name>` は、展開スクリプトで指定した **Azure リソース グループ** に置き換えます)。

```azurecli
az group delete -n <resource group name>
```

## `deploy-sql-big-data-aro.py` 

このセクションのスクリプトでは、SQL Server ビッグ データ クラスターが Azure Red Hat OpenShift に展開されます。 スクリプトをご利用のワークステーションにコピーし、展開を開始する前にそれを `deploy-sql-big-data-aro.py` として保存します。

```python
#
# Prerequisites: 
# 
# Azure CLI (https://docs.microsoft.com/en-us/cli/azure/install-azure-cli), Azure Data CLI (`azdata`) (https://docs.microsoft.com/en-us/sql/big-data-cluster/deploy-install-azdata), oc CLI (https://www.openshift.com/blog/installing-oc-tools-windows)
#
# Run `az login` at least once BEFORE running this script
#

from subprocess import check_output, CalledProcessError, STDOUT, Popen, PIPE, getoutput
from time import sleep
import os
import getpass
import json

def executeCmd (cmd):
    if os.name=="nt":
        process = Popen(cmd.split(),stdin=PIPE, shell=True)
    else:
        process = Popen(cmd.split(),stdin=PIPE)
    stdout, stderr = process.communicate()
    if (stderr is not None):
        raise Exception(stderr)

#
# MUST INPUT THESE VALUES!!!!!
#
SUBSCRIPTION_ID = input("Provide your Azure subscription ID:").strip()
GROUP_NAME = input("Provide Azure resource group name to be created:").strip()
#
# This password will be use for Controller user, Gateway user and SQL Server Master SA accounts
AZDATA_USERNAME=input("Provide username to be used for Controller, SQL Server and Gateway endpoints - Press ENTER for using  `admin`:").strip() or "admin"
AZDATA_PASSWORD = getpass.getpass("Provide password to be used for Controller user, Gateway user and SQL Server Master accounts:")
#
# Optionally change these configuration settings
#
AZURE_REGION=input("Provide Azure region - Press ENTER for using `westus2`:").strip() or "westus2"
# MASTER_VM_SIZE=input("Provide VM size for master nodes for the ARO cluster - Press ENTER for using  `Standard_D2s_v3`:").strip() or "Standard_D2s_v3"
WORKER_VM_SIZE=input("Provide VM size for the worker nodes for the ARO cluster - Press ENTER for using  `Standard_D8s_v3`:").strip() or "Standard_D8s_v3"
OC_NODE_COUNT=input("Provide number of worker nodes for ARO cluster - Press ENTER for using  `3`:").strip() or "3"
VNET_NAME=input("Provide name of Virtual Network for ARO cluster - Press ENTER for using  `aro-vnet`:").strip() or "aro-vnet"
VNET_ADDRESS_SPACE=input("Provide Virtual Network Address Space for ARO cluster - Press ENTER for using  `10.0.0.0/16`:").strip() or "10.0.0.0/16"
MASTER_SUBNET_NAME=input("Provide Master Subnet Name for ARO cluster - Press ENTER for using  `master-subnet`:").strip() or "master-subnet"
MASTER_SUBNET_IP_RANGE=input("Provide address range of Master Subnet for ARO cluster - Press ENTER for using  `10.0.0.0/23`:").strip() or "10.0.0.0/23"
WORKER_SUBNET_NAME=input("Provide Worker Subnet Name for ARO cluster - Press ENTER for using  `worker-subnet`:").strip() or "worker-subnet"
WORKER_SUBNET_IP_RANGE=input("Provide address range of Worker Subnet for ARO cluster - Press ENTER for using  `10.0.2.0/23`:").strip() or "10.0.2.0/23"
#
# This is both Kubernetes cluster name and SQL Big Data cluster name
CLUSTER_NAME=input("Provide name of OpenShift cluster and SQL big data cluster - Press ENTER for using  `sqlbigdata`:").strip() or "sqlbigdata"
#
# Deploy the ARO cluster
#  
print ("Set azure context to subscription: "+SUBSCRIPTION_ID)
command = "az account set -s "+ SUBSCRIPTION_ID
executeCmd (command)
print ("Creating azure resource group: "+GROUP_NAME)
command="az group create --name "+GROUP_NAME+" --location "+AZURE_REGION
executeCmd (command)
command = "az network vnet create --resource-group "+GROUP_NAME+" --name "+VNET_NAME+" --address-prefixes "+VNET_ADDRESS_SPACE
print("Creating Virtual Network: "+VNET_NAME)
executeCmd(command)
command = "az network vnet subnet create --resource-group "+GROUP_NAME+" --vnet-name "+VNET_NAME+" --name "+MASTER_SUBNET_NAME+" --address-prefixes "+MASTER_SUBNET_IP_RANGE+" --service-endpoints Microsoft.ContainerRegistry"
print("Creating Master Subnet: "+MASTER_SUBNET_NAME)
executeCmd(command)
command = "az network vnet subnet create --resource-group "+GROUP_NAME+" --vnet-name "+VNET_NAME+" --name "+WORKER_SUBNET_NAME+" --address-prefixes "+WORKER_SUBNET_IP_RANGE+" --service-endpoints Microsoft.ContainerRegistry"
print("Creating Worker Subnet: "+WORKER_SUBNET_NAME)
executeCmd(command)
command = "az network vnet subnet update --name "+MASTER_SUBNET_NAME+" --resource-group "+GROUP_NAME+" --vnet-name "+VNET_NAME+" --disable-private-link-service-network-policies true"
print("Updating Master Subnet by disabling Private Link Policies")
executeCmd(command)
command = "az aro create --resource-group "+GROUP_NAME+" --name "+CLUSTER_NAME+" --vnet "+VNET_NAME+" --master-subnet "+MASTER_SUBNET_NAME+" --worker-subnet "+WORKER_SUBNET_NAME+" --worker-count "+OC_NODE_COUNT+" --worker-vm-size "+WORKER_VM_SIZE +" --only-show-errors"
print("Creating OpenShift cluster: "+CLUSTER_NAME)
executeCmd (command)
#
# Login to oc console
#
command = "az aro list-credentials --name "+CLUSTER_NAME+" --resource-group "+GROUP_NAME +" --only-show-errors"
output=json.loads(getoutput(command))
OC_CLUSTER_USERNAME = str(output['kubeadminUsername'])
OC_CLUSTER_PASSWORD = str(output['kubeadminPassword'])
command = "az aro show --name "+CLUSTER_NAME+" --resource-group "+GROUP_NAME +" --only-show-errors"
output=json.loads(getoutput(command))
APISERVER = str(output['apiserverProfile']['url'])
command = "oc login "+ APISERVER+ " -u " + OC_CLUSTER_USERNAME + " -p "+ OC_CLUSTER_PASSWORD
executeCmd (command)
#
# Setup pre-requisites for deploying BDC on OpenShift
#
#
#Creating new project/namespace
command = "oc new-project "+ CLUSTER_NAME
executeCmd (command)
#
# create custom SCC for BDC
command = "oc apply -f bdc-scc.yaml"
executeCmd (command)
#
#Bind the custom scc with service accounts in the BDC namespace
command = "oc create clusterrole bdc-role --verb=use --resource=scc --resource-name=bdc-scc -n " + CLUSTER_NAME
executeCmd (command)
command = "oc create rolebinding bdc-rbac --clusterrole=bdc-role --group=system:serviceaccounts:" + CLUSTER_NAME
executeCmd (command)
#
# Deploy big data cluster
#
print ('Set environment variables for credentials')
os.environ['AZDATA_PASSWORD'] = AZDATA_PASSWORD
os.environ['AZDATA_USERNAME'] = AZDATA_USERNAME
os.environ['ACCEPT_EULA']="Yes"
#
#Creating azdata configuration with aro-dev-test profile
command = "azdata bdc config init --source aro-dev-test --target custom --force"
executeCmd (command)
command="azdata bdc config replace -c custom/bdc.json -j ""metadata.name=" + CLUSTER_NAME + ""
executeCmd (command)
#
# Create BDC
command = "azdata bdc create --config-profile custom --accept-eula yes"
executeCmd(command)
#login into BDC cluster and list endpoints
command="azdata login -n " + CLUSTER_NAME
executeCmd (command)
print("")
print("SQL Server big data cluster endpoints: ")
command="azdata bdc endpoint list -o table"
executeCmd(command)
```

## `bdc-scc.yaml`

次の .yaml マニフェストでは、ビッグ データ クラスター デプロイ用のカスタム セキュリティ コンテキスト制約 (SCC) が定義されます。 それを `deploy-sql-big-data-aro.py` と同じディレクトリにコピーします。

```yaml
allowHostDirVolumePlugin: false
allowHostIPC: false
allowHostNetwork: false
allowHostPID: false
allowHostPorts: false
allowPrivilegeEscalation: true
allowPrivilegedContainer: false
allowedCapabilities:
  - SETUID
  - SETGID
  - CHOWN
  - SYS_PTRACE
apiVersion: security.openshift.io/v1
defaultAddCapabilities: null
fsGroup:
  type: RunAsAny
kind: SecurityContextConstraints
metadata:
  annotations:
    kubernetes.io/description: SQL Server BDC custom scc is based on 'nonroot' scc plus additional capabilities required by BDC.
  generation: 2
  name: bdc-scc
readOnlyRootFilesystem: false
requiredDropCapabilities:
  - KILL
  - MKNOD
runAsUser:
  type: MustRunAsNonRoot
seLinuxContext:
  type: MustRunAs
supplementalGroups:
  type: RunAsAny
volumes:
  - configMap
  - downwardAPI
  - emptyDir
  - persistentVolumeClaim
  - projected
  - secret
```

## <a name="next-steps"></a>次のステップ

展開スクリプトで Azure Kubernetes サービスを構成し、SQL Server 2019 ビッグ データ クラスターも展開しました。 また、手動インストールを使用して、今後の展開をカスタマイズすることもできます。 ビッグ データ クラスターの展開方法と展開をカスタマイズする方法の詳細については、「[Kubernetes に [!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)]を展開する方法](deployment-guidance.md)」を参照してください。

SQL Server ビッグ データ クラスターが展開されたので、サンプル データを読み込んでチュートリアルを調べることができます。

> [!div class="nextstepaction"]
> [チュートリアル:SQL Server 2019 ビッグ データ クラスターにサンプル データを読み込む](tutorial-load-sample-data.md)
