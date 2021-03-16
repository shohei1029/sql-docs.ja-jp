---
title: SQL Server ビッグ データ クラスターの配置後の構成の概要
titleSuffix: SQL Server big data clusters
description: ビッグ データ クラスターの配置後の構成の概要
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: rahul.ajmera
ms.date: 02/11/2021
ms.topic: reference
ms.prod: sql
ms.technology: big-data-cluster
ms.openlocfilehash: 06b01a8fba30178cf8c8eb5842750de4baff1c82
ms.sourcegitcommit: e2d25f265556af92afcc0acde662929e654bf841
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103489473"
---
# <a name="how-to-configure-bdc-settings-post-deployment"></a>配置後に BDC の設定を構成する方法

[!INCLUDE[SQL Server 2019](../includes/applies-to-version/sqlserver2019.md)]

> [!NOTE]
> 配置後の設定の構成は、BDC CU9 以降の配置でのみ使用できます。 設定の構成には、スケール、ストレージ、エンドポイントの構成は含まれません。 CU9 より前の BDC を構成するためのオプションと手順については、[こちら](configure-bdc-pre-configuration.md)を参照してください。

ビッグ データ クラスターのクラスター、サービス、リソースのスコープの設定は、azdata CLI を使用して配置後に構成できます。 この機能により、BDC 管理者は、常にワークロードの要件を満たすように構成を調整できます。 この記事では、Spark ワークロードの要件を満たすように BDC を構成する手順の例について説明します。 配置後の構成機能は、設定、差分、適用フローに従います。

## <a name="step-by-step-configure-bdc-to-meet-your-spark-workload-requirements"></a>手順: Spark ワークロードの要件を満たすように BDC を構成する

### <a name="view-the-current-configurations-of-the-big-data-cluster-spark-service"></a>ビッグ データ クラスター Spark サービスの現在の構成を表示する
次の例では、ユーザーが構成した Spark サービスの設定を表示する方法を示します。 オプションのパラメーターを使用して、構成可能なすべての設定、システム管理およびすべての構成可能な設定、および保留中の設定を表示できます。 詳細については、「[`azdata bdc spark` ステートメント](../azdata/reference/reference-azdata-bdc-spark-statement.md)」を参照してください。

```bash
azdata bdc spark settings show
```
#### <a name="sample-output"></a>サンプル出力
Spark サービス 

|設定|Running Value|
| --- | --- |
|`spark-defaults-conf.spark.driver.cores`|`1` |
|`spark-defaults-conf.spark.driver.memory`|`1664m` |

### <a name="change-the-default-number-of-cores-and-memory-for-the-spark-driver-across-all-resources-with-spark-ie-for-the-spark-service"></a>Spark を使用するすべてのリソースで Spark ドライバー用の既定のコア数とメモリを変更します (Spark サービスの場合)。
Spark サービスの既定のコア数を 2 に、既定のメモリを 7424m に更新します。

```bash
azdata bdc spark settings set --settings spark-defaults-conf.spark.driver.cores=2,spark-defaults-conf.spark.driver.memory=7424m
```

### <a name="change-the-default-number-of-cores-and-memory-for-the-spark-executors-in-the-storage-pool"></a>記憶域プールの Spark Executor 用の既定のコア数とメモリを変更する
記憶域プールの既定の Executor コア数を 4 に更新します。

```bash
azdata bdc spark settings set --settings spark-defaults-conf.spark.executor.cores=4 --resource=storage-0
```

### <a name="view-the-pending-settings-changes-staged-in-the-bdc"></a>BDC でステージングされている保留中の設定の変更を表示する
BDC クラスター全体について、Spark サービスのみの保留中の設定の変更を表示します。

#### <a name="pending-spark-service-settings"></a>保留中の Spark サービスの設定
```bash
azdata bdc spark settings show --filter-option=pending --include-details
```

### <a name="spark-service"></a>Spark サービス

|設定|Running Value|構成されている値|構成可能|構成済み |最終更新日時|
| --- | --- | --- | --- | --- | --- |
|`spark-defaults-conf.spark.driver.cores`|`1`| `2` | `true` | `true` |
|`spark-defaults-conf.spark.driver.memory`|`1664m`| `7424m` | `true` | `true` |

#### <a name="all-pending-settings"></a>すべての保留中の設定
```bash
azdata bdc settings show --filter-option=pending --include-details --recursive
```

Spark サービスの設定 - 保留中

|設定|Running Value|構成されている値|構成可能|構成済み|最終更新日時|
| --- | --- | --- | --- | --- | --- |
|`spark-defaults-conf.spark.driver.cores`|`1`| `2` | `true` | `true` |
|`spark-defaults-conf.spark.driver.memory`|`1664m`| `7424m` | `true` | `true` |

Storage-0 リソースの Spark の設定 - 保留中

|設定|Running Value|構成されている値|構成可能|構成済み|最終更新日時|
| --- | --- | --- | --- | --- | --- |
|`spark-defaults-conf.spark.executor.cores`|`1`| `4` | `true` | `true` |

### <a name="apply-the-pending-settings-to-the-bdc"></a>保留中の設定を BDC に適用する

```bash
azdata bdc settings apply
```

### <a name="monitor-the-status-of-the-bdc-configuration-update"></a>BDC 構成の更新の状態を監視する

```bash
azdata bdc status show -all
```

## <a name="optional-steps"></a>省略可能な手順

### <a name="revert-pending-configuration-settings"></a>保留中の構成設定を元に戻す

保留中の構成設定を変更する必要がなくなった場合は、これらの設定のステージングを解除できます。 これにより、すべてのスコープで保留中の設定が元に戻されます。

```bash
azdata bdc settings revert
```

### <a name="abort-the-configuration-upgrade"></a>構成のアップグレードを中止する

いずれかのコンポーネントで構成のアップグレードが失敗した場合は、アップグレード プロセスを取り消し、BDC を前の構成に戻すことができます。 アップグレードの間に変更のためにステージングされた設定は、保留中の設定として再び一覧に表示されます。

```bash
azdata bdc settings cancel-apply
```

## <a name="next-steps"></a>次の手順

[SQL Server ビッグ データ クラスターを構成する](configure-bdc-overview.md)
