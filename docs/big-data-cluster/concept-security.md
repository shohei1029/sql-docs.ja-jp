---
title: セキュリティの概念
titleSuffix: SQL Server big data clusters
description: この記事では、SQL Server ビッグ データ クラスターのセキュリティの概念について説明します。 このコンテンツには、クラスター エンドポイントとクラスター認証の説明が含まれます。
author: nelgson
ms.author: negust
ms.reviewer: mikeray
ms.date: 06/22/2020
ms.topic: conceptual
ms.prod: sql
ms.technology: big-data-cluster
ms.openlocfilehash: 485ae050a4456e846d8455fef71875198ddf4655
ms.sourcegitcommit: c242f423cc3b776c20268483cfab0f4be54460d4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/25/2021
ms.locfileid: "105551523"
---
# <a name="security-concepts-for-big-data-clusters-2019"></a>[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)]のセキュリティの概念

[!INCLUDE[SQL Server 2019](../includes/applies-to-version/sqlserver2019.md)]

この記事では、ビッグ データ クラスターのセキュリティに関する重要な概念について説明します

[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)]では、明確で一貫性のある承認と認証が提供されます。 ビッグ データ クラスターは、既存のドメインに対して Active Directory (AD) 統合をセットアップする完全に自動化されたデプロイを通じて、AD と統合できます。 AD 統合を使用してビッグ データ クラスターを構成すると、既存の ID とユーザー グループを活用して、すべてのエンドポイントにわたる統合アクセスを実現できます。 また、SQL Server 内で外部テーブルを作成した後は、外部テーブルへのアクセスを AD ユーザーとグループに許可することで、データ ソースへのアクセスを制御できます。これにより、データ アクセス ポリシーが 1 か所に一元化されます。

この 14 分のビデオでは、ビッグ データ クラスター セキュリティの概要を把握できます。

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Overview-Big-Data-Cluster-Security/player?WT.mc_id=dataexposed-c9-niner]


## <a name="authentication"></a>認証

外部クラスター エンドポイントでは、AD 認証がサポートされます。 AD ID を使用してビッグ データ クラスターを認証します。

### <a name="cluster-endpoints"></a>クラスター エンドポイント

ビッグ データ クラスターには 5 つのエントリ ポイントがあります

* マスター インスタンス - SSMS や Azure Data Studio など、データベース ツールとアプリケーションを使用してクラスター内の SQL Server マスター インスタンスにアクセスするための TDS エンドポイント。 [!INCLUDE [azure-data-cli-azdata](../includes/azure-data-cli-azdata.md)] から HDFS または SQL Server コマンドを使用する場合、操作に応じて、ツールは他のエンドポイントに接続します。

* HDFS ファイル、Spark (Knox) にアクセスするためのゲートウェイ - webHDFS や Spark などのサービスにアクセスするための HTTPS エンドポイントです。

* クラスター管理サービス (コントローラー) エンドポイント - クラスターを管理するための REST API を公開しているビッグ データ クラスター管理サービスです。 Azdata ツールは、このエンドポイントに接続する必要があります。

* 管理プロキシ - ログ検索ダッシュボードとメトリック ダッシュボードにアクセスするために使用します。

* アプリケーション プロキシ - ビッグ データ クラスター内に展開されたアプリケーションを管理するためのエンドポイント。

![クラスター エンドポイント](media/concept-security/cluster_endpoints.png)

現時点では、外部からクラスターにアクセスするための追加のポートを開くオプションはありません。

## <a name="authorization"></a>承認

クラスター全体におよぶさまざまなコンポーネント間の統合セキュリティにより、Spark と SQL Server から HDFS までクエリを発行するときに元のユーザーの ID を渡すことができます。 前述のように、さまざまな外部クラスター エンドポイントで AD 認証がサポートされています。

クラスターには、データ アクセスを管理するための 2 レベルの承認チェックがあります。 ビッグ データのコンテキストでの承認は、オブジェクトに対する従来の SQL Server アクセス許可を使用して SQL Server 内で、また、ユーザー ID を特定のアクセス許可に関連付ける制御リスト (ACL) を使用して HDFS 内で行われます。

セキュリティで保護されたビッグ データ クラスターとは、SQL Server と HDFS/Spark の両方にまたがる、認証と承認のシナリオに対する一貫性のあるわかりやすいサポートを意味します。 認証とは、ユーザーまたはサービスの身元を確認し、ユーザーまたはサービスが本物であることを確認するプロセスです。 承認とは、要求元のユーザーの ID に基づいて、特定のリソースへのアクセスを許可または拒否することを意味します。 この手順は、ユーザーが認証によって識別された後に実行されます。

ビッグ データのコンテキストにおける承認は、ユーザー ID と特定のアクセス許可を関連付けるアクセス制御リスト (ACL) を使用して実行されます。 HDFS は、サービス API、HDFS ファイル、およびジョブ実行に対するアクセスを制限することで、承認をサポートするものです。

## <a name="encryption-in-flight-and-other-security-mechanisms"></a>フライトとその他のセキュリティ メカニズムでの暗号化

クライアントと外部エンドポイント間およびクラスター内のコンポーネント間の通信の暗号化は、証明書を使用して TLS/SSL で保護されます。

データ プールと通信する SQL マスター インスタンスなど、SQL Server 間のすべての通信は、SQL ログインを使用して保護されます。

> [!IMPORTANT]
>  ビッグ データ クラスターでは、`etcd` を使用して資格情報が保存されます。 ベスト プラクティスとして、Kubernetes クラスターで保存時に `etcd` 暗号化が使用されるように、確実に構成する必要があります。 既定では、`etcd` に格納されるシークレットは暗号化されません。 Kubernetes のドキュメントでは、この管理タスクの詳細について説明しています (https://kubernetes.io/docs/tasks/administer-cluster/kms-provider/ と https://kubernetes.io/docs/tasks/administer-cluster/encrypt-data/ )。

## <a name="data-encryption-at-rest"></a>保存データの暗号化

SQL Server および HDFS コンポーネントのアプリケーション レベルの暗号化の主要なシナリオは、SQL Server ビッグ データ クラスターの保存時の暗号化機能セットによってサポートされています。 包括的な機能の使用ガイドについては、[保存時の暗号化の概念と構成ガイド](encryption-at-rest-concepts-and-configuration.md)の記事を参照してください。

> [!IMPORTANT]
> ボリューム暗号化は、すべての SQL Server ビッグ データ クラスターのデプロイに推奨されます。 保管時のデータ暗号化への包括的なアプローチとして、Kubernetes クラスターで構成されたお客様提供のストレージ ボリュームも暗号化する必要があります。 SQL Server ビッグ データ クラスターの保存時の暗号化機能は追加のセキュリティ レイヤーであり、SQL Server データとログ ファイルのアプリケーション レベルの暗号化と HDFS 暗号化ゾーンのサポートが提供されます。


## <a name="basic-administrator-login"></a>基本管理者ログイン

クラスターを AD モードで展開するか、基本管理者ログインのみを使用して展開するかを選択できます。 基本管理者ログインのみを使用することは、運用環境でサポートされているセキュリティ モードではなく、製品の評価を目的としています。

Active Directory モードを選択した場合でも、クラスター管理者に対して基本ログインが作成されます。 この機能により、AD 接続が停止した場合の代替アクセスが提供されます。

展開時に、この基本ログインにはクラスター内での管理者権限が付与されます。 ログイン ユーザーは SQL Server マスター インスタンス内のシステム管理者となり、クラスター コントローラー内の管理者になります。
Hadoop コンポーネントでは混合モード認証がサポートされません。つまり、基本管理者ログインを使用してゲートウェイ (Knox) に対する認証を行うことはできません。

デプロイ時に定義する必要があるログイン資格情報には、以下が含まれます。

クラスター管理者のユーザー名:

 + `AZDATA_USERNAME=<username>`

クラスター管理者のパスワード:  
 + `AZDATA_PASSWORD=<password>`

> [!NOTE]
> 非 AD モードでは、HDFS/Spark にアクセスするためにゲートウェイ (Knox) に対して認証を行うために、ユーザー名を上記のパスワードと組み合わせて使用する必要があることにご注意ください。 SQL Server 2019 CU5 より前では、ユーザー名は `root` でした。
> 
> [!INCLUDE [big-data-cluster-root-user](../includes/big-data-cluster-root-user.md)]

## <a name="next-steps"></a>次のステップ

[[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ver15.md)]とは](big-data-cluster-overview.md)

[ワークショップ: Microsoft [!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)] アーキテクチャ](https://github.com/microsoft/sqlworkshops-bdc)

[Kubernetes RBAC](kubernetes-rbac.md)  

[ビッグ データ クラスターに関する FAQ](big-data-cluster-faq.yml)  