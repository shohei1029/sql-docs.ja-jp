---
title: Apache Spark と Apache Hadoop の構成プロパティ
titleSuffix: SQL Server big data clusters
description: Apache Spark と Apache Hadoop (HDFS) の構成プロパティに関するリファレンス記事。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: rahul.ajmera
ms.date: 03/23/2021
ms.topic: reference
ms.prod: sql
ms.technology: big-data-cluster
ms.openlocfilehash: 231381eccc46430b31b1af7d3001f4fd6134363b
ms.sourcegitcommit: c242f423cc3b776c20268483cfab0f4be54460d4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/25/2021
ms.locfileid: "105551267"
---
# <a name="apache-spark--apache-hadoop-hdfs-configuration-properties"></a>Apache Spark と Apache Hadoop (HDFS) の構成プロパティ

[!INCLUDE[SQL Server 2019](../includes/applies-to-version/sqlserver2019.md)]

ビッグ データ クラスターは、サービスおよびリソースのスコープでの Apache Spark および Hadoop コンポーネントのデプロイ時とデプロイ後の構成をサポートします。 ビッグ データ クラスターでは、ほとんどの設定で、それぞれのオープン ソース プロジェクトと同じ既定の構成値が使用されます。 変更する設定は、説明とその既定値と共に、下に一覧表示されています。 ゲートウェイ リソース以外は、サービス スコープとリソース スコープで構成可能な設定に違いはありません。

それぞれの考えられるすべての構成および既定値については、関連する Apache ドキュメント サイトを参照してください。
- Apache Spark: https://spark.apache.org/docs/latest/configuration.html
- Apache Hadoop:
  - HDFS HDFS サイト: https://hadoop.apache.org/docs/r2.7.1/hadoop-project-dist/hadoop-hdfs/hdfs-default.xml
  - HDFS コアサイト: https://hadoop.apache.org/docs/r2.8.0/hadoop-project-dist/hadoop-common/core-default.xml  
  - Yarn: https://hadoop.apache.org/docs/r3.1.1/hadoop-yarn/hadoop-yarn-site/ResourceModel.html
- Hive: https://cwiki.apache.org/confluence/display/Hive/Configuration+Properties#ConfigurationProperties-MetaStore
- Livy: https://github.com/cloudera/livy/blob/master/conf/livy.conf.template
- Apache Knox ゲートウェイ: https://knox.apache.org/books/knox-0-14-0/user-guide.html#Gateway+Details

構成をサポートしていない設定も下に一覧表示しています。

> [!NOTE]
> Spark を記憶域プールに含めるには、`spec.resources.storage-0.spec.settings.spark` で `bdc.json` 構成ファイルのブール値 `includeSpark` を設定します。 手順については、「[ビッグ データ クラスターで Apache Spark と Apache Hadoop を構成する](configure-spark-hdfs.md)」を参照してください。


##  <a name="big-data-clusters-specific-default-spark-settings"></a>ビッグ データ クラスター固有の既定の Spark 設定
以下の Spark 設定は、BDC 固有の既定値を持ちながら、ユーザーが構成できるものです。 システム管理の設定は含まれません。

| 設定名                                                                                 | 説明                                                                                                                                                           | Type   | 既定値                                                                                                                              |
| ------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------ | ------------------------------------------------------------------------------------------------------------------------------------------ |
| capacity-scheduler.yarn.scheduler.capacity.maximum-applications                      | 実行中と保留中の両方で同時にアクティブにできるシステム内のアプリケーションの最大数。                                                               | INT    | 10000                                                                                                                                      |
| capacity-scheduler.yarn.scheduler.capacity.resource-calculator                       | スケジューラ内のリソースを比較するために使用される ResourceCalculator の実装。                                                                               | string | org.apache.hadoop.yarn.util.resource.DominantResourceCalculator                                                                            |
| capacity-scheduler.yarn.scheduler.capacity.root.queues                               | ルートと呼ばれるキューが事前定義されている容量スケジューラ。                                                                                                              | string | default                                                                                                                                    |
| capacity-scheduler.yarn.scheduler.capacity.root.default.capacity                     | ルート キューの絶対リソース キューの最小容量としての、パーセント単位でのキュー容量 (%)。                                                                          | INT    | 100                                                                                                                                        |
| spark-defaults-conf.spark.driver.cores                                               | クラスター モードでのみ、ドライバー プロセスに使用するコアの数。                                                                                                  | INT    | 1                                                                                                                                          |
| spark-defaults-conf.spark.driver.memoryOverhead                                      | クラスター モードでドライバーごとに割り当てられる、非ヒープ メモリの量。                                                                                             | INT    | 384                                                                                                                                        |
| spark-defaults-conf.spark.executor.instances                                         | 静的割り当てのための Executor の数。                                                                                                                        | INT    | 1                                                                                                                                          |
| spark-defaults-conf.spark.executor.cores                                             | Executor ごとに使用するコアの数。                                                                                                                          | INT    | 1                                                                                                                                          |
| spark-defaults-conf.spark.driver.memory                                              | ドライバー プロセスに使用するメモリの量。                                                                                                                       | string | 1g                                                                                                                                         |
| spark-defaults-conf.spark.executor.memory                                            | Executor プロセスごとに使用するメモリの量。                                                                                                                         | string | 1g                                                                                                                                         |
| spark-defaults-conf.spark.executor.memoryOverhead                                    | Executor ごとに割り当てられる、非ヒープ メモリの量。                                                                                                           | INT    | 384                                                                                                                                        |
| yarn-site.yarn.nodemanager.resource.memory-mb                                        | コンテナーに割り当てることができる物理メモリの量 (MB)。                                                                                               | INT    | 8192                                                                                                                                       |
| yarn-site.yarn.scheduler.maximum-allocation-mb                                       | リソース マネージャーでのすべてのコンテナー要求に対する最大割り当て。                                                                                           | INT    | 8192                                                                                                                                       |
| yarn-site.yarn.nodemanager.resource.cpu-vcores                                       | コンテナーに割り当てることができる CPU コア数。                                                                                                             | INT    | 32                                                                                                                                         |
| yarn-site.yarn.scheduler.maximum-allocation-vcores                                   | リソース マネージャーでのすべてのコンテナー要求に対する最大割り当て (仮想 CPU コアに関して)。                                                            | INT    | 8                                                                                                                                          |
| yarn-site.yarn.nodemanager.linux-container-executor.secure-mode.pool-user-count      | セキュア モードでの Linux コンテナー Executor のプール ユーザー数。                                                                                             | INT    | 6                                                                                                                                          |
| yarn-site.yarn.scheduler.capacity.maximum-am-resource-percent                        | アプリケーション マスターの実行に使用できる、クラスター内のリソースの最大割合。                                                                              | float  | 0.1                                                                                                                                        |
| yarn-site.yarn.nodemanager.container-executor.class                                  | 特定のオペレーティング システムのコンテナーの Executor。                                                                                                               | string | org.apache.hadoop.yarn.server.nodemanager.LinuxContainerExecutor                                                                           |
| capacity-scheduler.yarn.scheduler.capacity.root.default.user-limit-factor            | 1 人のユーザーがより多くのリソースを取得できるように構成できる、キュー容量の倍数。                                                          | INT    | 1                                                                                                                                          |
| capacity-scheduler.yarn.scheduler.capacity.root.default.maximum-capacity             | パーセント単位でのキューの最大容量 (%)。浮動値または絶対リソース キューの最大容量です。 この値を -1 に設定すると、最大容量が 100% に設定されます。           | INT    | 100                                                                                                                                        |
| capacity-scheduler.yarn.scheduler.capacity.root.default.state                        | キューの状態は、実行中または停止済みのどちらかになります。                                                                                                                      | string | RUNNING                                                                                                                                    |
| capacity-scheduler.yarn.scheduler.capacity.root.default.maximum-application-lifetime | キューに送信されるアプリケーションの最大有効期間 (秒)。 0 以下の値は、無効と見なされます。                     | INT    | -1                                                                                                                                         |
| capacity-scheduler.yarn.scheduler.capacity.root.default.default-application-lifetime | キューに送信されるアプリケーションの既定有効期間 (秒)。 0 以下の値は、無効と見なされます。                     | INT    | -1                                                                                                                                         |
| capacity-scheduler.yarn.scheduler.capacity.node-locality-delay                       | 実行されなかったスケジュール機会の数。これを過ぎると、CapacityScheduler によってラックローカル コンテナーのスケジュール設定が試行されます。                                               | INT    | 40                                                                                                                                         |
| capacity-scheduler.yarn.scheduler.capacity.rack-locality-additional-delay            | node-locality-delay の場合の他に、実行されなかったスケジュール機会の数。これを過ぎると、CapacityScheduler によってオフスイッチ コンテナーのスケジュール設定が試行されます。 | INT    | -1                                                                                                                                         |
| hadoop-env.HADOOP_HEAPSIZE_MAX                                                       | すべての Hadoop JVM プロセスの既定の最大ヒープ サイズ。                                                                                                                 | INT    | 2048                                                                                                                                       |
| yarn-env.YARN_RESOURCEMANAGER_HEAPSIZE                                               | Yarn ResourceManager のヒープ サイズ。                                                                                                                                     | INT    | 2048                                                                                                                                       |
| yarn-env.YARN_NODEMANAGER_HEAPSIZE                                                   | Yarn NodeManager のヒープ サイズ。                                                                                                                                         | INT    | 2048                                                                                                                                       |
| mapred-env.HADOOP_JOB_HISTORYSERVER_HEAPSIZE                                         | Hadoop ジョブ履歴サーバーのヒープ サイズ。                                                                                                                                 | INT    | 2048                                                                                                                                       |
| hive-env.HADOOP_HEAPSIZE                                                             | Hive 用 Hadoop のヒープ サイズ。                                                                                                                                          | INT    | 2048                                                                                                                                       |
| livy-conf.livy.server.session.timeout-check                                          | Livy サーバー セッションのタイムアウトのチェック。                                                                                                                                | bool   | true                                                                                                                                       |
| livy-conf.livy.server.session.timeout-check.skip-busy                                | Livy サーバー セッションのタイムアウト チェックでのスキップビジー。                                                                                                                  | bool   | true                                                                                                                                       |
| livy-conf.livy.server.session.timeout                                                | livy サーバー セッションのタイムアウト (ミリ秒/秒/分 \| 分/時間/日/年)。                                                                                                              | string | 2h                                                                                                                                         |
| livy-conf.livy.server.yarn.poll-interval                                             | Livy サーバーでの yarn のポーリング間隔 (ミリ秒/秒/分 \| 分/時間/日/年)。                                                                                                     | string | 500ms                                                                                                                                      |
| livy-conf.livy.rsc.jars                                                              | Livy RSC の jar。                                                                                                                                                        | string | local:/opt/livy/rsc-jars/livy-api.jar,local:/opt/livy/rsc-jars/livy-rsc.jar,local:/opt/livy/rsc-jars/netty-all.jar                         |
| livy-conf.livy.repl.jars                                                             | Lvy repl jar。                                                                                                                                                       | string | local:/opt/livy/repl_2.11-jars/livy-core.jar,local:/opt/livy/repl_2.11-jars/livy-repl.jar,local:/opt/livy/repl_2.11-jars/commons-codec.jar |
| livy-conf.livy.rsc.sparkr.package                                                    | Livy RSC SparkR パッケージ。                                                                                                                                              | string | hdfs:///system/livy/sparkr.zip                                                                                                             |
| livy-env.LIVY_SERVER_JAVA_OPTS                                                       | Livy サーバーの Java オプション。                                                                                                                                             | string | -Xmx2g                                                                                                                                     |
| spark-defaults-conf.spark.r.backendConnectionTimeout                                 | R プロセスによって RBackend エンドへの接続時に設定された接続のタイムアウト (秒)。                                                                                         | INT    | 86400                                                                                                                                      |
| spark-defaults-conf.spark.pyspark.python                                             | Spark の Python オプション。                                                                                                                                              | string | /opt/bin/python3                                                                                                                           |
| spark-defaults-conf.spark.yarn.jars                                                  | Yarn の jar。                                                                                                                                                            | string | local:/opt/spark/jars/*                                                                                                                    |
| spark-history-server-conf.spark.history.fs.cleaner.maxAge                            | ジョブ履歴ファイルがファイルシステム履歴クリーナーによって削除されるまでの最大経過時間 (ミリ秒/秒/分 \| 分/時間/日/年)。                                                   | string | 7d                                                                                                                                         |
| spark-history-server-conf.spark.history.fs.cleaner.interval                          | spark 履歴のクリーナーの間隔 (ミリ秒/秒/分 \| 分/時間/日/年)。                                                                                                        | string | 12h                                                                                                                                        |
| hadoop-env.HADOOP_CLASSPATH                                                          | 追加の Hadoop クラスパスを設定します。                                                                                                                                 | string |                                                                                                                                            |
| spark-env.SPARK_DAEMON_MEMORY                                                        | Spark デーモンのメモリ。                                                                                                                                                  | string | 2g                                                                                                                                         |
| yarn-site.yarn.log-aggregation.retain-seconds                                        | ログの集計が有効になっている場合、このプロパティによって、ログを保持する秒数が決定されます。                                                                       | INT    | 604800                                                                                                                                     |
| yarn-site.yarn.nodemanager.log-aggregation.compression-type                          | Yarn NodeManager のログ集計の圧縮の種類。                                                                                                            | string | gz                                                                                                                                         |
| yarn-site.yarn.nodemanager.log-aggregation.roll-monitoring-interval-seconds          | NodeManager のログ集計でのロール監視の間隔 (秒)。                                                                                                  | INT    | 3600                                                                                                                                       |
| yarn-site.yarn.scheduler.minimum-allocation-mb                                       | リソース マネージャーでのすべてのコンテナー要求に対する最小割り当て (MB)。                                                                                   | INT    | 512                                                                                                                                        |
| yarn-site.yarn.scheduler.minimum-allocation-vcores                                   | リソース マネージャーでのすべてのコンテナー要求に対する最小割り当て (仮想 CPU コアに関して)。                                                             | INT    | 1                                                                                                                                          |
| yarn-site.yarn.nm.liveness-monitor.expiry-interval-ms                                | ノード マネージャーが停止していると見なされるまでの待機時間。                                                                                                             | INT    | 180000                                                                                                                                     |
| yarn-site.yarn.resourcemanager.zk-timeout-ms                                         | 'ZooKeeper' セッションのタイムアウト (ミリ秒)。                                                                                                                          | INT    | 40000                                                                                                                                      |
| capacity-scheduler.yarn.scheduler.capacity.root.default.acl_application_max_priority | 優先順位が構成されたアプリケーションを送信できるユーザーの ACL。 例: [user={name} group={name} max_priority={priority} default_priority={priority}]。             | string | *                                                                                                                                          |
| includeSpark                                                                         | ストレージ プールで Spark ジョブを実行できるかどうかを構成するブール値。                                                                                           | bool   | true                                                                                                                                       |
| enableSparkOnK8s                                                                     | K8 で Spark を有効にするかどうかを構成するブール値。これにより、Spark ヘッドに K8 のコンテナーが追加されます。                                                               | [bool]   | false                                                                                                                                      |
| sparkVersion                                                                         | Spark のバージョン                                                                                                                                                  | string | 2.4                                                                                                                                        |
| spark-env.PYSPARK_ARCHIVES_PATH                                                      | Spark ジョブで使用される pyspark archive jar へのパス。                                                                                                                      | string | local:/opt/spark/python/lib/pyspark.zip,local:/opt/spark/python/lib/py4j-0.10.7-src.zip                                                    |

次のセクションでは、サポートされていない構成を示します。

##  <a name="big-data-clusters-specific-default-hdfs-settings"></a>ビッグ データ クラスター固有の既定の HDFS 設定
以下の HDFS 設定は、BDC 固有の既定値を持ちながら、ユーザーが構成できるものです。 システム管理の設定は含まれません。

| 設定名                                                                       | 説明                                                                                         | Type   | 既定値                                                                    |
| -------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------- | ------ | -------------------------------------------------------------------------------------- |
| hdfs-site.dfs.replication                                                  | 既定ブロックのレプリケーション。                                                                          | INT    | 2                                                                                      |
| hdfs-site.dfs.namenode.provided.enabled                                    | 指定されたストレージを名前ノードで処理できるようにします。                                                   | bool   | true                                                                                   |
| hdfs.site.dfs.namenode.mount.acls.enabled |  マウント時にリモート ストアから ACL (アクセス制御リスト) を継承する場合、true に設定します。| [bool] | false |
| hdfs-site.dfs.datanode.provided.enabled                                    | 指定されたストレージをデータ ノードで処理できるようにします。                                                   | bool   | true                                                                                   |
| hdfs-site.dfs.datanode.provided.volume.lazy.load                           | 指定されたストレージのデータ ノードでの遅延読み込みを有効にします。                                                 | bool   | true                                                                                   |
| hdfs-site.dfs.provided.aliasmap.inmemory.enabled                           | 指定されたストレージのメモリ内別名マップを有効にします。                                                     | bool   | true                                                                                   |
| hdfs-site.dfs.provided.aliasmap.class                                      | 指定されたストレージ上のブロックの入力形式を指定するために使用されるクラス。              | string | org.apache.hadoop.hdfs.server.common.blockaliasmap.impl.InMemoryLevelDBAliasMapClient  |
| hdfs-site.dfs.namenode.provided.aliasmap.class                             | namenode に指定されたストレージ上のブロックの入力形式を指定するために使用されるクラス。 | string | org.apache.hadoop.hdfs.server.common.blockaliasmap.impl.NamenodeInMemoryAliasMapClient |
| hdfs-site.dfs.provided.aliasmap.load.retries                               | 指定された別名マップを読み込むためのデータノードの再試行回数。                                    | INT    | 0                                                                                      |
| hdfs-site.dfs.provided.aliasmap.inmemory.batch-size                        | 別名マップをバックアップしているデータベースを反復処理するときのバッチ サイズ。                               | INT    | 500                                                                                    |
| hdfs-site.dfs.datanode.provided.volume.readthrough                         | データノードで指定されたストレージに対してリードスルーを有効にします。                                               | bool   | true                                                                                   |
| hdfs-site.dfs.provided.cache.capacity.mount                                | 指定されたストレージのキャッシュ容量マウントを有効にします。                                                  | bool   | true                                                                                   |
| hdfs-site.dfs.provided.overreplication.factor                              | 指定されたストレージに対するオーバーレプリケーション係数。  リモート HDFS ブロックごとに作成された BDC 上のキャッシュ ブロックの数。                                                     | float  | 1                                                                                      |
| hdfs-site.dfs.provided.cache.capacity.fraction                             | 指定されたストレージのキャッシュ容量の割合。 指定されたストアからのデータのキャッシュに使用できる、クラスターの合計容量の割合。                                                      | float  | 0.01                                                                                   |
| hdfs-site.dfs.provided.cache.capacity.bytes | 指定したブロックのキャッシュ領域として使用するクラスター容量 (バイト単位)。 | INT | -1 | 
| hdfs-site.dfs.ls.limit                                                     | ls によって印刷されるファイルの数を制限します。                                                            | INT    | 500                                                                                    |
| hdfs-env.HDFS_NAMENODE_OPTS                                                | HDFS Namenode オプション。                                                                              | string | -Dhadoop.security.logger=INFO,RFAS -Xmx2g                                              |
| hdfs-env.HDFS_DATANODE_OPTS                                                | HDFS Datanode オプション。                                                                              | string | -Dhadoop.security.logger=ERROR,RFAS -Xmx2g                                             |
| hdfs-env.HDFS_ZKFC_OPTS                                                    | HDFS ZKFC オプション。                                                                                  | string | -Xmx1g                                                                                 |
| hdfs-env.HDFS_JOURNALNODE_OPTS                                             | HDFS JournalNode オプション。                                                                           | string | -Xmx2g                                                                                 |
| hdfs-env.HDFS_AUDIT_LOGGER                                                 | HDFS Audit Logger オプション。                                                                          | string | INFO、RFAAUDIT                                                                          |
| core-site.hadoop.security.group.mapping.ldap.search.group.hierarchy.levels | コア サイトの Hadoop LDAP 検索グループの階層レベル。                                            | INT    | 10                                                                                     |
| core-site.fs.permissions.umask-mode                                        | アクセス許可の umask モード。                                                                              | string | 077                                                                                    |
| core-site.hadoop.security.kms.client.failover.max.retries                  | クライアントのフェールオーバーの最大再試行回数。                                                                    | INT    | 20                                                                                     |
| zoo-cfg.tickTime                                       | 'ZooKeeper' 構成のティック時間。                         | INT    | 2000                |
| zoo-cfg.initLimit                                      | 'ZooKeeper' 構成の初期時間。                         | INT    | 10                  |
| zoo-cfg.syncLimit                                      | 'ZooKeeper' 構成の同期時間。                         | INT    | 5                   |
| zoo-cfg.maxClientCnxns                                 | 'ZooKeeper' 構成の最大クライアント接続数。            | INT    | 60                  |
| zoo-cfg.minSessionTimeout                              | 'ZooKeeper' 構成の最小セッション タイムアウト。           | INT    | 4000                |
| zoo-cfg.maxSessionTimeout                              | 'ZooKeeper' 構成の最大セッション タイムアウト。           | INT    | 40000               |
| zoo-cfg.autopurge.snapRetainCount                      | 自動消去 'ZooKeeper' 構成でのスナップ保持数。       | INT    | 3                   |
| zoo-cfg.autopurge.purgeInterval                        | 自動消去 'ZooKeeper' 構成での消去間隔。          | INT    | 0                   |
| zookeeper-java-env.JVMFLAGS                            | 'ZooKeeper' の Java 環境の JVM フラグ。            | string | -Xmx1G -Xms1G       |
| zookeeper-log4j-properties.zookeeper.console.threshold | 'ZooKeeper' の log4j コンソールのしきい値。               | string | INFO                |
| zoo-cfg.zookeeper.request.timeout                      | 'ZooKeeper' 要求のタイムアウトをミリ秒単位で制御します。 | INT    | 40000               |
| kms-site.hadoop.security.kms.encrypted.key.cache.size | Hadoop KMS で暗号化されたキーのキャッシュ サイズ。 | INT  | 500 |
    

##  <a name="big-data-clusters-specific-default-gateway-settings"></a>ビッグ データ クラスター固有の既定のゲートウェイ設定
以下のゲートウェイ設定は、BDC 固有の既定値を持ちながら、ユーザーが構成できるものです。 システム管理の設定は含まれません。 ゲートウェイの設定は、"**リソース**" スコープでのみ構成できます。

| 設定名                                          | 説明                                            | Type   | 既定値 |
| --------------------------------------------- | ------------------------------------------------------ | ------ | ------------------- |
| gateway-site.gateway.httpclient.socketTimeout | ゲートウェイ内の HTTP クライアントのソケット タイムアウト (ミリ秒/秒/分)。 | string | 90s                 |
| gateway-site.sun.security.krb5.debug          | Kerberos セキュリティのデバッグ。                           | bool   | true                |
| knox-env.KNOX_GATEWAY_MEM_OPTS                | Knox ゲートウェイのメモリ オプション。                           | string | -Xmx2g              |

## <a name="unsupported-spark-configurations"></a>サポートされていない Spark 構成

次の `spark` 構成はサポートされていないため、ビッグ データ クラスターのコンテキストで変更することはできません。

| カテゴリ  | サブカテゴリ               | ファイル                       | サポートされていない構成                                              |
|-----------|----------------------------|----------------------------|-------------------------------------------------------------------------|
|           | yarn-site                  | yarn-site.xml              | yarn.log-aggregation-enable                                             |
|           |                            |                            | yarn.log.server.url                                                     |
|           |                            |                            | yarn.nodemanager.pmem-check-enabled                                     |
|           |                            |                            | yarn.nodemanager.vmem-check-enabled                                     |
|           |                            |                            | yarn.nodemanager.aux-services                                           |
|           |                            |                            | yarn.resourcemanager.address                                            |
|           |                            |                            | yarn.nodemanager.address                                                |
|           |                            |                            | yarn.client.failover-no-ha-proxy-provider                               |
|           |                            |                            | yarn.client.failover-proxy-provider                                     |
|           |                            |                            | yarn.http.policy                                                        |
|           |                            |                            | yarn.nodemanager.linux-container-executor.secure-mode.use-pool-user     |
|           |                            |                            | yarn.nodemanager.linux-container-executor.secure-mode.pool-user-prefix  |
|           |                            |                            | yarn.nodemanager.linux-container-executor.nonsecure-mode.local-user     |
|           |                            |                            | yarn.acl.enable                                                         |
|           |                            |                            | yarn.admin.acl                                                          |
|           |                            |                            | yarn.resourcemanager.hostname                                           |
|           |                            |                            | yarn.resourcemanager.principal                                          |
|           |                            |                            | yarn.resourcemanager.keytab                                             |
|           |                            |                            | yarn.resourcemanager.webapp.spnego-keytab-file                          |
|           |                            |                            | yarn.resourcemanager.webapp.spnego-principal                            |
|           |                            |                            | yarn.nodemanager.principal                                              |
|           |                            |                            | yarn.nodemanager.keytab                                                 |
|           |                            |                            | yarn.nodemanager.webapp.spnego-keytab-file                              |
|           |                            |                            | yarn.nodemanager.webapp.spnego-principal                                |
|           |                            |                            | yarn.resourcemanager.ha.enabled                                         |
|           |                            |                            | yarn.resourcemanager.cluster-id                                         |
|           |                            |                            | yarn.resourcemanager.zk-address                                         |
|           |                            |                            | yarn.resourcemanager.ha.rm-ids                                          |
|           |                            |                            | yarn.resourcemanager.hostname.*                                         |
|           | capacity-scheduler         | capacity-scheduler.xml     | yarn.scheduler.capacity.root.acl_submit_applications                    |
|           |                            |                            | yarn.scheduler.capacity.root.acl_administer_queue                       |
|           |                            |                            | yarn.scheduler.capacity.root.default.acl_application_max_priority       |
|           | yarn-env                   | yarn-env.sh                |                                                                         |
|           | spark-defaults-conf        | spark-defaults.conf        | spark.yarn.archive                                                      |
|           |                            |                            | spark.yarn.historyServer.address                                        |
|           |                            |                            | spark.eventLog.enabled                                                  |
|           |                            |                            | spark.eventLog.dir                                                      |
|           |                            |                            | spark.sql.warehouse.dir                                                 |
|           |                            |                            | spark.sql.hive.metastore.version                                        |
|           |                            |                            | spark.sql.hive.metastore.jars                                           |
|           |                            |                            | spark.extraListeners                                                    |
|           |                            |                            | spark.metrics.conf                                                      |
|           |                            |                            | spark.ssl.enabled                                                       |
|           |                            |                            | spark.authenticate                                                      |
|           |                            |                            | spark.network.crypto.enabled                                            |
|           |                            |                            | spark.ssl.keyStore                                                      |
|           |                            |                            | spark.ssl.keyStorePassword  
|           |                            |                            | spark.ui.enabled                                                        |
|           | spark-env                  | spark-env.sh               | SPARK_NO_DAEMONIZE                                                      |
|           |                            |                            | SPARK_DIST_CLASSPATH                                                    |
|           | spark-history-server-conf  | spark-history-server.conf  | spark.history.fs.logDirectory                                           |
|           |                            |                            | spark.ui.proxyBase                                                      |
|           |                            |                            | spark.history.fs.cleaner.enabled                                        |
|           |                            |                            | spark.ssl.enabled                                                       |
|           |                            |                            | spark.authenticate                                                      |
|           |                            |                            | spark.network.crypto.enabled                                            |
|           |                            |                            | spark.ssl.keyStore                                                      |
|           |                            |                            | spark.ssl.keyStorePassword                                              |
|           |                            |                            | spark.history.kerberos.enabled                                          |
|           |                            |                            | spark.history.kerberos.principal                                        |
|           |                            |                            | spark.history.kerberos.keytab                                           |
|           |                            |                            | spark.ui.filters                                                        |
|           |                            |                            | spark.acls.enable                                                       |
|           |                            |                            | spark.history.ui.acls.enable                                            |
|           |                            |                            | spark.history.ui.admin.acls                                             |
|           |                            |                            | spark.history.ui.admin.acls.groups                                      |
|           | livy-conf                  | livy.conf                  | livy.keystore                                                           |
|           |                            |                            | livy.keystore.password                                                  |
|           |                            |                            | livy.spark.master                                                       |
|           |                            |                            | livy.spark.deploy-mode                                                  |
|           |                            |                            | livy.rsc.jars                                                           |
|           |                            |                            | livy.repl.jars                                                          |
|           |                            |                            | livy.rsc.pyspark.archives                                               |
|           |                            |                            | livy.rsc.sparkr.package                                                 |
|           |                            |                            | livy.repl.enable-hive-context                                           |
|           |                            |                            | livy.superusers                                                         |
|           |                            |                            | livy.server.auth.type                                                   |
|           |                            |                            | livy.server.launch.kerberos.keytab                                      |
|           |                            |                            | livy.server.launch.kerberos.principal                                   |
|           |                            |                            | livy.server.auth.kerberos.principal                                     |
|           |                            |                            | livy.server.auth.kerberos.keytab                                        |
|           |                            |                            | livy.impersonation.enabled                                              |
|           |                            |                            | livy.server.access-control.enabled                                      |
|           |                            |                            | livy.server.access-control.*                                            |
|           | livy-env                   | livy-env.sh                |                                                                         |
|           | hive-site                  | hive-site.xml              | javax.jdo.option.ConnectionURL                                          |
|           |                            |                            | javax.jdo.option.ConnectionDriverName                                   |
|           |                            |                            | javax.jdo.option.ConnectionUserName                                     |
|           |                            |                            | javax.jdo.option.ConnectionPassword                                     |
|           |                            |                            | hive.metastore.uris                                                     |
|           |                            |                            | hive.metastore.pre.event.listeners                                      |
|           |                            |                            | hive.security.authorization.enabled                                     |
|           |                            |                            | hive.security.metastore.authenticator.manager                           |
|           |                            |                            | hive.security.metastore.authorization.manager                           |
|           |                            |                            | hive.metastore.use.SSL                                                  |
|           |                            |                            | hive.metastore.keystore.path                                            |
|           |                            |                            | hive.metastore.keystore.password                                        |
|           |                            |                            | hive.metastore.truststore.path                                          |
|           |                            |                            | hive.metastore.truststore.password                                      |
|           |                            |                            | hive.metastore.kerberos.keytab.file                                     |
|           |                            |                            | hive.metastore.kerberos.principal                                       |
|           |                            |                            | hive.metastore.sasl.enabled                                             |
|           |                            |                            | hive.metastore.execute.setugi                                           |
|           |                            |                            | hive.cluster.delegation.token.store.class                               |
|           | hive-env                   | hive-env.sh                | |

## <a name="unsupported-hdfs-configurations"></a>サポートされていない HDFS 構成

次の `hdfs` 構成はサポートされていないため、ビッグ データ クラスターのコンテキストで変更することはできません。

| カテゴリ  | サブカテゴリ               | ファイル                       | サポートされていない構成                                              |
|-----------|----------------------------|----------------------------|-------------------------------------------------------------------------|
|          | core-site                   | core-site.xml                 | fs.defaultFS                                          |
|          |                             |                               | ha.zookeeper.quorum                                   |
|          |                             |                               | hadoop.tmp.dir                                        |
|          |                             |                               | hadoop.rpc.protection                                 |
|          |                             |                               | hadoop.security.auth_to_local                         |
|          |                             |                               | hadoop.security.authentication                        |
|          |                             |                               | hadoop.security.authorization                         |
|          |                             |                               | hadoop.http.authentication.simple.anonymous.allowed   |
|          |                             |                               | hadoop.http.authentication.type                       |
|          |                             |                               | hadoop.http.authentication.kerberos.principal         |
|          |                             |                               | hadoop.http.authentication.kerberos.keytab            |
|          |                             |                               | hadoop.http.filter.initializers                       |
|          |                             |                               | hadoop.security.group.mapping.*                       |                               
|          |                             |                               | hadoop.security.key.provider.path                     |                               
|          | mapred-env                  | mapred-env.sh                 |                                                       |
|          | hdfs-site                   | hdfs-site.xml                 | dfs.namenode.name.dir                                 |
|          |                             |                               | dfs.datanode.data.dir                                 |
|          |                             |                               | dfs.namenode.acls.enabled                             |
|          |                             |                               | dfs.namenode.datanode.registration.ip-hostname-check  |
|          |                             |                               | dfs.client.retry.policy.enabled                       |
|          |                             |                               | dfs.permissions.enabled                               |
|          |                             |                               | dfs.nameservices                                      |
|          |                             |                               | dfs.ha.namenodes.nmnode-0                             |
|          |                             |                               | dfs.namenode.rpc-address.nmnode-0.*                   |
|          |                             |                               | dfs.namenode.shared.edits.dir                         |
|          |                             |                               | dfs.ha.automatic-failover.enabled                     |
|          |                             |                               | dfs.ha.fencing.methods                                |
|          |                             |                               | dfs.journalnode.edits.dir                             |
|          |                             |                               | dfs.client.failover.proxy.provider.nmnode-0           |
|          |                             |                               | dfs.namenode.http-address                             |
|          |                             |                               | dfs.namenode.httpS-address                            |
|          |                             |                               | dfs.http.policy                                       |
|          |                             |                               | dfs.encrypt.data.transfer                             |
|          |                             |                               | dfs.block.access.token.enable                         |
|          |                             |                               | dfs.data.transfer.protection                          |
|          |                             |                               | dfs.encrypt.data.transfer.cipher.suites               |
|          |                             |                               | dfs.https.port                                        |
|          |                             |                               | dfs.namenode.keytab.file                              |
|          |                             |                               | dfs.namenode.kerberos.principal                       |
|          |                             |                               | dfs.namenode.kerberos.internal.spnego.principal       |
|          |                             |                               | dfs.datanode.data.dir.perm                            |
|          |                             |                               | dfs.datanode.address                                  |
|          |                             |                               | dfs.datanode.http.address                             |
|          |                             |                               | dfs.datanode.ipc.address                              |
|          |                             |                               | dfs.datanode.https.address                            |
|          |                             |                               | dfs.datanode.keytab.file                              |
|          |                             |                               | dfs.datanode.kerberos.principal                       |
|          |                             |                               | dfs.journalnode.keytab.file                           |
|          |                             |                               | dfs.journalnode.kerberos.principal                    |
|          |                             |                               | dfs.journalnode.kerberos.internal.spnego.principal    |
|          |                             |                               | dfs.web.authentication.kerberos.keytab                |
|          |                             |                               | dfs.web.authentication.kerberos.principal             |
|          |                             |                               | dfs.webhdfs.enabled                                   |
|          |                             |                               | dfs.permissions.superusergroup                        |
|          | hdfs-env                    | hdfs-env.sh                   | HADOOP_HEAPSIZE_MAX                                   |
|          | zoo-cfg                     | zoo.cfg                       | secureClientPort                                      |
|          |                             |                               | clientPort                                            |
|          |                             |                               | dataDir                                               |
|          |                             |                               | dataLogDir                                            |
|          |                             |                               | 4lw.commands.whitelist                                |
|          | zookeeper-java-env          | java.env                      | ZK_LOG_DIR                                            |
|          |                             |                               | SERVER_JVMFLAGS                                       |
|          | zookeeper-log4j-properties  | log4j.properties (zookeeper)  | log4j.rootLogger                                      |
|          |                             |                               | log4j.appender.CONSOLE.*                              |

> [!NOTE]
> この記事には、"*ホワイトリスト*" という用語が含まれていますが、Microsoft はこのコンテキストにおいてこの用語がセンシティブではないと考えます。 これはソフトウェアに現在表示されるものであるため、この記事に出現します。 ソフトウェアからこの用語が削除された時点で、この記事から削除します。

## <a name="unsupported-gateway-configurations"></a>サポートされていない `gateway` 構成

次の `gateway` 構成はサポートされていないため、ビッグ データ クラスターのコンテキストで変更することはできません。

| カテゴリ  | サブカテゴリ               | ファイル                       | サポートされていない構成                                              |
|-----------|----------------------------|----------------------------|-------------------------------------------------------------------------|
|          | gateway-site                | gateway-site.xml              | gateway.port                                          |
|          |                             |                               | gateway.path                                          |
|          |                             |                               | gateway.gateway.conf.dir                              |
|          |                             |                               | gateway.hadoop.kerberos.secured                       |
|          |                             |                               | java.security.krb5.conf                               |
|          |                             |                               | java.security.auth.login.config                       |
|          |                             |                               | gateway.websocket.feature.enabled                     |
|          |                             |                               | gateway.scope.cookies.feature.enabled                 |
|          |                             |                               | ssl.exclude.protocols                                 |
|          |                             |                               | ssl.include.ciphers                                   |

## <a name="next-steps"></a>次のステップ

[SQL Server ビッグ データ クラスターを構成する](configure-bdc-overview.md)
