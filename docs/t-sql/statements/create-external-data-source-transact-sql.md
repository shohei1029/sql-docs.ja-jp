---
description: CREATE EXTERNAL DATA SOURCE (Transact-SQL)
title: CREATE EXTERNAL DATA SOURCE (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/05/2021
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- CREATE EXTERNAL DATA SOURCE
- CREATE_EXTERNAL_DATA_SOURCE
dev_langs:
- TSQL
helpviewer_keywords:
- External
- External, data source
- PolyBase, create data source
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 55513a6f32ad1dfc6a8af102b829175a7c242e07
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104742662"
---
# <a name="create-external-data-source-transact-sql"></a>CREATE EXTERNAL DATA SOURCE (Transact-SQL)

SQL Server、SQL Database、Azure Synapse Analytics、または Analytics Platform System (Parallel Data Warehouse つまり PDW) を使用してクエリを実行するための外部データ ソースを作成します。

この記事では、選択した SQL 製品について、構文、引数、注釈、アクセス許可、例を紹介します。

構文表記規則の詳細については、「[Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)」を参照してください。

[!INCLUDE[select-product](../../includes/select-product.md)]

::: moniker range=">=sql-server-2016||>=sql-server-linux-2017"

:::row:::
    :::column:::
        **_\* SQL Server \*_** &nbsp;
    :::column-end:::
    :::column:::
        [SQL Database](create-external-data-source-transact-sql.md?view=azuresqldb-current&preserve-view=true)
    :::column-end:::
    :::column:::
        [Azure Synapse<br />Analytics](create-external-data-source-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)
    :::column-end:::
    :::column:::
        [Analytics Platform<br />System (PDW)](create-external-data-source-transact-sql.md?view=aps-pdw-2016-au7&preserve-view=true)
    :::column-end:::
:::row-end:::

&nbsp;

## <a name="overview-sql-server"></a>概要:SQL Server

PolyBase クエリ用の外部データ ソースを作成します。 外部データ ソースを使用して接続を確立し、次の主なユース ケースをサポートします。

- [PolyBase][intro_pb] を使用したデータ仮想化とデータ読み込み
- `BULK INSERT` または `OPENROWSET` を使用した一括読み込み操作

**適用対象**:[!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 以降

## <a name="syntax"></a>構文

```syntaxsql
CREATE EXTERNAL DATA SOURCE <data_source_name>
WITH
  ( [ LOCATION = '<prefix>://<path>[:<port>]' ]
    [ [ , ] CONNECTION_OPTIONS = '<key_value_pairs>'[,...]]
    [ [ , ] CREDENTIAL = <credential_name> ]
    [ [ , ] PUSHDOWN = { ON | OFF } ]
    [ [ , ] TYPE = { HADOOP | BLOB_STORAGE } ]
    [ [ , ] RESOURCE_MANAGER_LOCATION = '<resource_manager>[:<port>]' )
[ ; ]
```

## <a name="arguments"></a>引数

### <a name="data_source_name"></a>data_source_name

データ ソースのユーザー定義の名前を指定します。 名前は、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のデータベース内で一意である必要があります。

### <a name="location--prefixpathport"></a>LOCATION = *`'<prefix>://<path[:port]>'`*

接続プロトコルと外部データ ソースへのパスを指定します。

| 外部データ ソース    | 場所プレフィックス | ロケーション パス                                         | 製品/サービスでサポートされている場所 |
| ----------------------- | --------------- | ----------------------------------------------------- | ---------------------------------------- |
| Cloudera または Hortonworks | `hdfs`          | `<Namenode>[:port]`                                   | [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 以降                       |
| Azure Storage アカウント (V2) | `wasb[s]`       | `<container>@<storage_account>.blob.core.windows.net` | [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 以降         階層型名前空間はサポート **されていません** |
| [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]              | `sqlserver`     | `<server_name>[\<instance_name>][:port]`              | [!INCLUDE[sql-server-2019](../../includes/sssql19-md.md)] 以降                       |
| Oracle                  | `oracle`        | `<server_name>[:port]`                                | [!INCLUDE[sql-server-2019](../../includes/sssql19-md.md)] 以降                       |
| Teradata                | `teradata`      | `<server_name>[:port]`                                | [!INCLUDE[sql-server-2019](../../includes/sssql19-md.md)] 以降                       |
| MongoDB または CosmosDB     | `mongodb`       | `<server_name>[:port]`                                | [!INCLUDE[sql-server-2019](../../includes/sssql19-md.md)] 以降                       |
| ODBC                    | `odbc`          | `<server_name>[:port]`                                | [!INCLUDE[sql-server-2019](../../includes/sssql19-md.md)] 以降 - Windows のみ        |
| 一括操作         | `https`         | `<storage_account>.blob.core.windows.net/<container>` | [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 以降                        |
| Edge ハブ         | `edgehub`         | 適用外 | EdgeHub は [Azure SQL Edge](/azure/azure-sql-edge/overview/) のインスタンスに対して常にローカルです。 そのため、パスまたはポート値を指定する必要はありません。 Azure SQL Edge でのみ使用できます。                      |
| Kafka        | `kafka`         | `<Kafka IP Address>[:port]` | Azure SQL Edge でのみ使用できます。                      |

場所のパス:

- `<`Namenode`>` = Hadoop クラスター内の `Namenode` のマシン名、ネーム サービス URI、または IP アドレス。 PolyBase では Hadoop クラスターで使用されているすべての DNS 名が解決される必要があります。 <!-- For highly available Hadoop configurations, provide the Nameservice ID as the `LOCATION`. -->
- `port` = 外部データ ソースがリッスンしているポート。 Hadoop では、`fs.defaultFS` 構成パラメーターを使用してポートを見つけることができます。 既定値は 8020 です。
- `<container>` = データを保持するストレージ アカウントのコンテナー。 ルート コンテナーは読み取り専用で、このコンテナーにデータを書き込むことはできません。
- `<storage_account>` = Azure リソースのストレージ アカウント名。
- `<server_name>` = ホスト名。
- `<instance_name>` = SQL Server の名前付きインスタンスの名前。 ターゲット インスタンスで実行中の SQL Server Browser サービスがある場合に使用されます。

場所を設定する場合の追加の注意事項とガイダンス:

- [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] では、オブジェクトの作成時に、外部データ ソースの存在が検証されません。 検証するには、外部データ ソースを使用して外部テーブルを作成します。
- 一貫性のあるクエリ セマンティクスを確保するため、Hadoop をクエリする際は、すべてのテーブルに同じ外部データ ソースを使用します。
- `sqlserver` 場所プレフィックスを使用して、[!INCLUDE[sql-server-2019](../../includes/sssql19-md.md)] を別の [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]、[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]、または Azure Synapse Analytics に接続することができます。
- `Driver={<Name of Driver>}` 経由で接続する際に `ODBC` を指定します。
- `wasbs` は省略可能ですが、セキュリティで保護された TLS/SSL 接続を使用してデータが送信されるため、Azure Storage アカウントにアクセスする場合には推奨されています。
- Azure Storage アカウントにアクセスする場合、`abfs` または `abfss` API はサポートされていません。
- Azure Storage アカウント (V2) の階層型名前空間オプションはサポートされていません。 このオプションが **無効になっている** ことを確認してください。
- Hadoop `Namenode` のフェールオーバー時に、PolyBase クエリを確実に成功させるため、Hadoop クラスターの `Namenode` に仮想 IP アドレスを使用することを検討してください。 使用しない場合は、[ALTER EXTERNAL DATA SOURCE][alter_eds] コマンドを実行して新しい場所を示します。

### <a name="connection_options--key_value_pair"></a>CONNECTION_OPTIONS = *key_value_pair*

`ODBC` 経由での外部データ ソースへの接続時に、追加のオプションを指定します。 複数の接続オプションを使用するには、セミコロンで区切ります。


汎用の `ODBC` 接続と、SQL Server、Oracle、Teradata、MongoDB、CosmosDB の組み込みの `ODBC` コネクタの両方に適用されます。

`key_value_pair` は、キーワードであり、特定の接続オプションの値です。 使用可能なキーワードと値は、外部データ ソースの種類によって異なります。最低でもドライバーの名前が必要ですが、設定に便利で、トラブルシューティングに役立てることができる `APP='<your_application_name>'` や `ApplicationIntent= ReadOnly|ReadWrite` などの他のオプションがあります。

追加情報については、次を参照してください。

- [接続文字列キーワードの使用][connection_options]
- [ODBC ドライバー接続文字列キーワード][connection_option_keyword]

### <a name="pushdown--on--off"></a>PUSHDOWN = *ON | OFF*

外部データ ソースに計算をプッシュ ダウンできるかどうかを示します。 既定でオンです。

外部データ ソース レベルで SQL Server、Oracle、Teradata、MongoDB、または ODBC に接続するときに `PUSHDOWN` がサポートされます。

クエリ レベルでプッシュ ダウンを有効にするか、無効にするかは、[ヒント][hint_pb]によって実現します。

### <a name="credential--credential_name"></a>CREDENTIAL = *credential_name*

外部データ ソースへの認証の資格情報のデータベース スコープを指定します。

資格情報の作成時の追加の注意事項とガイダンス:

- `CREDENTIAL` は、データがセキュリティ保護されている場合にのみ必須です。 匿名アクセスを許可するデータ セットには、`CREDENTIAL` は必要ありません。
- `TYPE` = `BLOB_STORAGE` の場合、`SHARED ACCESS SIGNATURE` を ID として使用して、資格情報を作成する必要があります。 さらに、SAS トークンを次のように構成する必要があります。
  - シークレットとして構成されている場合、先頭の `?` を除外する
  - 読み込む必要のあるファイル (たとえば `srt=o&sp=r`) に対して少なくとも読み取りアクセス許可がある
  - 有効な有効期限を使用する (すべての日付が UTC 時間)。

`SHARED ACCESS SIGNATURE` と `TYPE` = `BLOB_STORAGE` で、`CREDENTIAL` を使用する例については、[一括操作を実行し、Azure Storage から SQL Database にデータを取得するための外部データ ソースの作成](#i-create-an-external-data-source-for-bulk-operations-retrieving-data-from-azure-storage)に関するセクションを参照してください。

データベース スコープ資格情報を作成するには、「[CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)][create_dsc]」を参照してください。

### <a name="type---hadoop--blob_storage-"></a>TYPE = *[ HADOOP | BLOB_STORAGE ]*

構成されている外部データ ソースの種類を指定します。 このパラメーターは常に必要ではありません。

- 外部データ ソースが Cloudera、Hortonworks、Azure Storage アカウントの場合は、HADOOP を使用します。
- [!INCLUDE [sssql17-md](../../includes/sssql17-md.md)] で [BULK INSERT][bulk_insert] または [OPENROWSET][openrowset] を使用して、Azure Storage アカウントから一括操作を実行する場合は、BLOB_STORAGE を使用します。

> [!IMPORTANT]
> 他の外部データ ソースを使用する場合は、`TYPE` を設定しないでください。

`TYPE` = `HADOOP` を使用して Azure Storage アカウントからデータを読み込む例については、「[wasb:// インターフェイスを使用して Azure Storage のデータにアクセスするための外部データソースを作成する](#e-create-external-data-source-to-access-data-in-azure-storage-using-the-wasb-interface)」を参照してください。 <!--[Create external data source to reference Azure Storage](#e-create-external-data-source-to-reference-azure-storage).-->

### <a name="resource_manager_location--resourcemanager_uriport"></a>RESOURCE_MANAGER_LOCATION = *'ResourceManager_URI[:port]'*

Hortonworks または Cloudera に接続する場合は、このオプションの値を構成します。

`RESOURCE_MANAGER_LOCATION` が定義されている場合、クエリ オプティマイザーでは、パフォーマンスを向上させるためにコストに基づいて決定を行います。 MapReduce ジョブを使用して、Hadoop に計算をプッシュ ダウンできます。 `RESOURCE_MANAGER_LOCATION` を指定すると、Hadoop と [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の間で転送されるデータの量が大幅に減少し、それによってクエリのパフォーマンスが向上する可能性があります。

Resource Manager を指定しない場合、Hadoop への計算のプッシュが、PolyBase クエリに対して無効になります。

ポートが指定されていない場合、'hadoop connectivity' 構成の現在の設定を使用して、既定値が選択されます。

| Hadoop Connectivity | Resource Manager の既定のポート |
| ------------------- | ----------------------------- |
| 1                   | 50300                         |
| 2                   | 50300                         |
| 3                   | 8021                          |
| 4                   | 8032                          |
| 5                   | 8050                          |
| 6                   | 8032                          |
| 7                   | 8050                          |

サポートされている Hadoop バージョンの完全な一覧については、「[PolyBase 接続構成 (Transact-SQL)][connectivity_pb]」を参照してください。

> [!IMPORTANT]  
> RESOURCE_MANAGER_LOCATION 値は、外部データ ソースを作成するときに検証されません。 正しくない値を入力すると、指定された値で解決できないため、プッシュ ダウンが試行されるたびに実行時にクエリ エラーが発生する可能性があります。

「[プッシュダウンが有効になっている Hadoop を参照する外部データ ソースを作成する](#c-create-external-data-source-to-reference-hadoop-with-push-down-enabled)」では、具体的な例と追加のガイダンスを提供しています。

## <a name="permissions"></a>アクセス許可

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のデータベースに対する `CONTROL` アクセス許可が必要です。

## <a name="locking"></a>ロック

`EXTERNAL DATA SOURCE` オブジェクトを共有ロックします。

## <a name="security"></a>セキュリティ

PolyBase では、ほとんどの外部データ ソースにプロキシ ベースの認証をサポートします。 データベース スコープ資格情報を作成して、プロキシ アカウントを作成します。

SQL Server ビッグ データ クラスターでストレージまたはデータ プールに接続すると、ユーザーの資格情報が、バックエンド システムに渡されます。 データ プール自体にログインを作成し、パススルー認証を有効にします。

現在 `HADOOP` 型の SAS トークンはサポートされていません。 それはストレージ アカウントのアクセス キーでのみサポートされます。 `HADOOP` 型と SAS 資格情報で外部データ ソースを作成しようとすると、次のエラーで失敗します。

`Msg 105019, Level 16, State 1 - EXTERNAL TABLE access failed due to internal error: 'Java exception raised on call to HdfsBridge_Connect. Java exception message: Parameters provided to connect to the Azure storage account are not valid.: Error [Parameters provided to connect to the Azure storage account are not valid.] occurred while accessing external file.'`

## <a name="examples-starting-with-sssql16-md"></a>例 ([!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 以降)

> [!IMPORTANT]
> PolyBase をインストールして有効にする方法については、「[Windows への PolyBase のインストール](../../relational-databases/polybase/polybase-installation.md)」を参照してください

### <a name="a-create-external-data-source-in-sql-server-2019-to-reference-oracle"></a>A. SQL Server 2019 で Oracle を参照する外部データ ソースを作成する

Oracle を参照する外部データ ソースを作成するには、データベース スコープ資格情報があることを確認します。 オプションで、このデータ ソースに対して計算のプッシュ ダウンを有効または無効にすることもできます。

```sql
-- Create a database master key if one does not already exist, using your own password. This key is used to encrypt the credential secret in next step.
CREATE MASTER KEY ENCRYPTION BY PASSWORD = '!MyC0mpl3xP@ssw0rd!' ;

-- Create a database scoped credential with Azure storage account key as the secret.
CREATE DATABASE SCOPED CREDENTIAL OracleProxyAccount
WITH
     IDENTITY = 'oracle_username',
     SECRET = 'oracle_password' ;

CREATE EXTERNAL DATA SOURCE MyOracleServer
WITH
  ( LOCATION = 'oracle://145.145.145.145:1521',
    CREDENTIAL = OracleProxyAccount,
    PUSHDOWN = ON
  ) ;
```

MongoDB などの他のデータ ソースの追加の例については、「[MongoDB 上の外部データにアクセスするための PolyBase の構成][mongodb_pb]」を参照してください

### <a name="b-create-external-data-source-to-reference-hadoop"></a>B. Hadoop を参照する外部データ ソースを作成する

Hortonworks または Cloudera Hadoop クラスターを参照する外部データ ソースを作成するには、Hadoop `Namenode` のマシン名または IP アドレスとポートを指定します。 <!-- Provide the Nameservice ID as the `LOCATION` for highly available configurations. -->

```sql
CREATE EXTERNAL DATA SOURCE MyHadoopCluster
WITH
  ( LOCATION = 'hdfs://10.10.10.10:8050' ,
    TYPE = HADOOP
  ) ;
```

### <a name="c-create-external-data-source-to-reference-hadoop-with-push-down-enabled"></a>C. プッシュダウンが有効になっている Hadoop を参照する外部データ ソースを作成する

`RESOURCE_MANAGER_LOCATION` オプションを指定して、PolyBase クエリの Hadoop への計算のプッシュダウンを有効にします。 有効にすると、PolyBase によって、クエリの計算を Hadoop にプッシュするかどうかがコストに基づいて決定されます。

```sql
CREATE EXTERNAL DATA SOURCE MyHadoopCluster
WITH
  ( LOCATION = 'hdfs://10.10.10.10:8020' ,
    TYPE = HADOOP ,
    RESOURCE_MANAGER_LOCATION = '10.10.10.10:8050'
  ) ;
```

### <a name="d-create-external-data-source-to-reference-kerberos-secured-hadoop"></a>D. Kerberos でセキュリティ保護された Hadoop を参照する外部データ ソースを作成する

Hadoop クラスターが Kerberos でセキュリティ保護されていることを確認するには、Hadoop core-site.xml で hadoop.security.authentication プロパティの値を確認します。 Kerberos でセキュリティ保護された Hadoop クラスターを参照するには、ご自分の Kerberos ユーザー名とパスワードを含むデータベース スコープの資格情報を指定する必要があります。 データベース マスター キーは、データベース スコープの資格情報シークレットの暗号化に使用されます。

```sql
-- Create a database master key if one does not already exist, using your own password. This key is used to encrypt the credential secret in next step.
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'S0me!nfo' ;

-- Create a database scoped credential with Kerberos user name and password.
CREATE DATABASE SCOPED CREDENTIAL HadoopUser1
WITH
     IDENTITY = '<hadoop_user_name>',
     SECRET = '<hadoop_password>' ;

-- Create an external data source with CREDENTIAL option.
CREATE EXTERNAL DATA SOURCE MyHadoopCluster
WITH
  ( LOCATION = 'hdfs://10.10.10.10:8050' ,
    CREDENTIAL = HadoopUser1 ,
    TYPE = HADOOP ,
    RESOURCE_MANAGER_LOCATION = '10.10.10.10:8050'
  );
```

### <a name="e-create-external-data-source-to-access-data-in-azure-storage-using-the-wasb-interface"></a>E. wasb:// インターフェイスを使用して Azure Storage のデータにアクセスするための外部データソースを作成する
この例では、外部データ ソースは、`logs` という名前の Azure V2 Storage アカウントです。 コンテナーは `daily`と呼ばれます。 Azure Storage の外部データ ソースはデータ転送専用です。 述語のプッシュ ダウンはサポートされません。 `wasb://` インターフェイスを使用してデータにアクセスする場合、階層型名前空間はサポートされません。

この例では、Azure V2 Storage アカウントへの認証用にデータベース スコープ資格情報を作成する方法を示します。 データベースの資格情報シークレットで、Azure Storage アカウント キーを指定します。 Azure Storage への認証時に使用されないため、データベース スコープ資格情報 ID には任意の文字列を指定できます。

```sql
-- Create a database master key if one does not already exist, using your own password. This key is used to encrypt the credential secret in next step.
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'S0me!nfo' ;

-- Create a database scoped credential with Azure storage account key as the secret.
CREATE DATABASE SCOPED CREDENTIAL AzureStorageCredential
WITH
  IDENTITY = '<my_account>' ,
  SECRET = '<azure_storage_account_key>' ;

-- Create an external data source with CREDENTIAL option.
CREATE EXTERNAL DATA SOURCE MyAzureStorage
WITH
  ( LOCATION = 'wasbs://daily@logs.blob.core.windows.net/' ,
    CREDENTIAL = AzureStorageCredential ,
    TYPE = HADOOP
  ) ;
```

### <a name="f-create-external-data-source-to-reference-a-sql-server-named-instance-via-polybase-connectivity-sql-server-2019"></a>F. PolyBase 接続を使用して SQL Server 名前付きインスタンスを参照する外部データソースを作成する ([!INCLUDE[sql-server-2019](../../includes/sssql19-md.md)])

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の名前付きインスタンスを参照する外部データ ソースを作成するには、`CONNECTION_OPTIONS` を使用してインスタンス名を指定します。 

以下の例では、`WINSQL2019` がホスト名で、`SQL2019` がインスタンス名になります。 `'Server=%s\SQL2019'` はキーと値のペアです。

```sql
CREATE EXTERNAL DATA SOURCE SQLServerInstance2
WITH (
  LOCATION = 'sqlserver://WINSQL2019' ,
  CONNECTION_OPTIONS = 'Server=%s\SQL2019' ,
  CREDENTIAL = SQLServerCredentials
) ;
```

ポートを使用して、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンスに接続することもできます。

```sql
CREATE EXTERNAL DATA SOURCE SQLServerInstance2
WITH (
  LOCATION = 'sqlserver://WINSQL2019:58137' ,
  CREDENTIAL = SQLServerCredentials
) ;
```

### <a name="g-create-external-data-source-to-reference-kafka"></a>G. Kafka を参照する外部データ ソースを作成する

この例では、外部データ ソースは、IP アドレス xxx.xxx.xxx.xxx を持つ Kafak サーバーであり、ポート 1900 でリッスンします。 Kafka 外部データ ソースはデータ ストリーミング専用であり、述語のプッシュ ダウンはサポートされていません。

```sql
-- Create an External Data Source for Kafka
CREATE EXTERNAL DATA SOURCE MyKafkaServer WITH (
    LOCATION = 'kafka://xxx.xxx.xxx.xxx:1900'
)
go
```

### <a name="h-create-external-data-source-to-reference-edgehub"></a>H. EdgeHub を参照する外部データ ソースを作成する

この例では、外部データ ソースは、Azure SQL Edge と同じエッジ デバイスで実行されている EdgeHub になります。 EdgeHub 外部データ ソースはデータ ストリーミング専用であり、述語のプッシュ ダウンはサポートされていません。

```sql
-- Create an External Data Source for Kafka
CREATE EXTERNAL DATA SOURCE MyEdgeHub WITH (
    LOCATION = 'edgehub://'
)
go
```

## <a name="examples-bulk-operations"></a>例 :一括操作

> [!IMPORTANT]
> 一括操作用の外部データ ソースの構成時に、`LOCATION` URL の末尾に、 **/** 、ファイル名、Shared Access Signature パラメーターを追加しないでください。

### <a name="i-create-an-external-data-source-for-bulk-operations-retrieving-data-from-azure-storage"></a>I. Azure Storage からデータを取得する一括操作用の外部データ ソースを作成する

**適用対象**: [!INCLUDE [sssql17-md](../../includes/sssql17-md.md)]。
[BULK INSERT][bulk_insert] または [OPENROWSET][openrowset] を使用する一括操作に対し、次のデータ ソースを使用します。 資格情報は、`SHARED ACCESS SIGNATURE` を ID として設定する必要があり、SAS トークンの先頭に `?` があってはなりません。また、読み込む必要のあるファイル (たとえば `srt=o&sp=r`) に対して少なくとも読み取りアクセス許可が必要で、有効期限が有効である必要があります (すべての日付は UTC 時間です)。 Shared Access Signature に関する詳細については、「[Shared Access Signature (SAS) を使用][sas_token]」を参照してください。

```sql
CREATE DATABASE SCOPED CREDENTIAL AccessAzureInvoices
WITH
  IDENTITY = 'SHARED ACCESS SIGNATURE',
  -- Remove ? from the beginning of the SAS token
  SECRET = '******srt=sco&sp=rwac&se=2017-02-01T00:55:34Z&st=2016-12-29T16:55:34Z***************' ;

CREATE EXTERNAL DATA SOURCE MyAzureInvoices
WITH
  ( LOCATION = 'https://newinvoices.blob.core.windows.net/week3' ,
    CREDENTIAL = AccessAzureInvoices ,
    TYPE = BLOB_STORAGE
  ) ;
```

この使用例については、[BULK INSERT][bulk_insert_example] の例を参照してください。

## <a name="see-also"></a>参照

- [ALTER EXTERNAL DATA SOURCE (Transact-SQL)][alter_eds]
- [CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)][create_dsc]
- [CREATE EXTERNAL FILE FORMAT (Transact-SQL)][create_eff]
- [CREATE EXTERNAL TABLE (Transact-SQL)][create_etb]
- [sys.external_data_sources (Transact-SQL)][cat_eds]
- [Shared Access Signatures (SAS) の使用][sas_token]

<!-- links to external pages -->
<!-- SQL Docs -->
[bulk_insert]: ./bulk-insert-transact-sql.md
[bulk_insert_example]: ./bulk-insert-transact-sql.md#f-importing-data-from-a-file-in-azure-blob-storage
[openrowset]: ../functions/openrowset-transact-sql.md

[create_dsc]: ./create-database-scoped-credential-transact-sql.md
[create_eff]: ./create-external-file-format-transact-sql.md
[create_etb]: ./create-external-table-transact-sql.md
[create_etb_as_sel]: ./create-external-table-as-select-transact-sql.md?view=azure-sqldw-latest&preserve-view=true
[create_tbl_as_sel]: ./create-table-as-select-azure-sql-data-warehouse.md?view=azure-sqldw-latest&preserve-view=true

[alter_eds]: ./alter-external-data-source-transact-sql.md

[cat_eds]: ../../relational-databases/system-catalog-views/sys-external-data-sources-transact-sql.md
<!-- PolyBase docs -->
[intro_pb]: ../../relational-databases/polybase/polybase-guide.md
[mongodb_pb]: ../../relational-databases/polybase/polybase-configure-mongodb.md
[connectivity_pb]:https://docs.microsoft.com/sql/database-engine/configure-windows/polybase-connectivity-configuration-transact-sql
[connection_options]: ../../relational-databases/native-client/applications/using-connection-string-keywords-with-sql-server-native-client.md
[hint_pb]: ../../relational-databases/polybase/polybase-pushdown-computation.md#force-pushdown

<!-- Azure Docs -->
[sas_token]: /azure/storage/storage-dotnet-shared-access-signature-part-1

::: moniker-end
::: moniker range="=azuresqldb-current"

:::row:::
    :::column:::
        [SQL Server](create-external-data-source-transact-sql.md?view=sql-server-ver15&preserve-view=true)
    :::column-end:::
    :::column:::
        **_\* SQL Database \*_** &nbsp;
    :::column-end:::
    :::column:::
        [Azure Synapse<br />Analytics](create-external-data-source-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)
    :::column-end:::
    :::column:::
        [Analytics Platform<br />System (PDW)](create-external-data-source-transact-sql.md?view=aps-pdw-2016-au7&preserve-view=true)
    :::column-end:::
:::row-end:::

&nbsp;

## <a name="overview-azure-sql-database"></a>概要:Azure SQL データベース

エラスティック クエリ用の外部データ ソースを作成します。 外部データ ソースを使用して接続を確立し、次の主なユース ケースをサポートします。

- `BULK INSERT` または `OPENROWSET` を使用した一括読み込み操作
- [エラスティック クエリ][remote_eq]で SQL Database を使用してリモートの SQL Database または Azure Synapse インスタンスをクエリする
- [エラスティック クエリ][sharded_eq]を使用してシャード化された Azure SQL Database をクエリする

## <a name="syntax"></a>構文

```syntaxsql
CREATE EXTERNAL DATA SOURCE <data_source_name>
WITH
  ( [ LOCATION = '<prefix>://<path>[:<port>]' ]
    [ [ , ] CREDENTIAL = <credential_name> ]
    [ [ , ] TYPE = { BLOB_STORAGE | RDBMS | SHARD_MAP_MANAGER } ]
    [ [ , ] DATABASE_NAME = '<database_name>' ]
    [ [ , ] SHARD_MAP_NAME = '<shard_map_manager>' ] )
[ ; ]
```

## <a name="arguments"></a>引数

### <a name="data_source_name"></a>data_source_name

データ ソースのユーザー定義の名前を指定します。 この名前は、SQL Database のデータベース内で一意になる必要があります。

### <a name="location--prefixpathport"></a>LOCATION = *`'<prefix>://<path[:port]>'`*

接続プロトコルと外部データ ソースへのパスを指定します。

| 外部データ ソース   | 場所プレフィックス | ロケーション パス                                         |
| ---------------------- | --------------- | ----------------------------------------------------- |
| 一括操作        | `https`         | `<storage_account>.blob.core.windows.net/<container>` |
| エラスティック クエリ (シャード)  | 必要なし    | `<shard_map_server_name>.database.windows.net`        |
| エラスティック クエリ (リモート) | 必要なし    | `<remote_server_name>.database.windows.net`           |

場所のパス:

- `<shard_map_server_name>` = シャード マップ マネージャーをホストしている Azure での論理サーバー名。 `DATABASE_NAME` 引数は、シャード マップをホストするために使用するデータベースを指定し、`SHARD_MAP_NAME` はシャード マップ自体に使用します。
- `<remote_server_name>` = エラスティック クエリのターゲット論理サーバー名。 データベース名は、`DATABASE_NAME` 引数を使用して指定します。

場所を設定する場合の追加の注意事項とガイダンス:

- [!INCLUDE[ssde_md](../../includes/ssde_md.md)] では、オブジェクトの作成時に、外部データ ソースの存在が検証されません。 検証するには、外部データ ソースを使用して外部テーブルを作成します。

### <a name="credential--credential_name"></a>CREDENTIAL = *credential_name*

外部データ ソースへの認証の資格情報のデータベース スコープを指定します。

資格情報の作成時の追加の注意事項とガイダンス:

- Azure Storage から [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] にデータを読み込むには、Shared Access Signature (SAS トークン) を使用します。
- `CREDENTIAL` は、データがセキュリティ保護されている場合にのみ必須です。 匿名アクセスを許可するデータ セットには、`CREDENTIAL` は必要ありません。
- `TYPE` = `BLOB_STORAGE` の場合、`SHARED ACCESS SIGNATURE` を ID として使用して、資格情報を作成する必要があります。 さらに、SAS トークンを次のように構成する必要があります。
  - シークレットとして構成されている場合、先頭の `?` を除外する
  - 読み込む必要のあるファイル (たとえば `srt=o&sp=r`) に対して少なくとも読み取りアクセス許可がある
  - 有効な有効期限を使用する (すべての日付が UTC 時間)。

`SHARED ACCESS SIGNATURE` と `TYPE` = `BLOB_STORAGE` で、`CREDENTIAL` を使用する例については、[一括操作を実行し、Azure Storage から SQL Database にデータを取得するための外部データ ソースの作成](#c-create-an-external-data-source-for-bulk-operations-retrieving-data-from-azure-storage)に関するセクションを参照してください。

データベース スコープ資格情報を作成するには、「[CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)][create_dsc]」を参照してください。

### <a name="type---blob_storage--rdbms--shard_map_manager"></a>TYPE = *[ BLOB_STORAGE | RDBMS | SHARD_MAP_MANAGER]*

構成されている外部データ ソースの種類を指定します。 このパラメーターは常に必要ではありません。

- SQL Database からのエラスティック クエリを使用したクロスデータベース クエリには、`RDBMS` を使用します。
- シャード化された SQL Database への接続時に外部データ ソースを作成する場合は、`SHARD_MAP_MANAGER` を使用します。
- [BULK INSERT][bulk_insert] または [OPENROWSET][openrowset] を使用して一括操作を実行する場合は、`BLOB_STORAGE` を使用します。

> [!IMPORTANT]
> 他の外部データ ソースを使用する場合は、`TYPE` を設定しないでください。

### <a name="database_name--database_name"></a>DATABASE_NAME = *database_name*

この引数は、`TYPE` が `RDBMS` または `SHARD_MAP_MANAGER` に設定されている場合に構成します。

| TYPE              | DATABASE_NAME の値                                       |
| ----------------- | ------------------------------------------------------------ |
| RDBMS             | `LOCATION` を使用して指定されたサーバー上のリモート データベースの名前 |
| SHARD_MAP_MANAGER | シャード マップ マネージャーとして動作しているデータベースの名前      |

`TYPE` = `RDBMS` の外部データ ソースを作成する方法を示す例については、「[RDBMS 外部データ ソースを作成する](#b-create-an-rdbms-external-data-source)」を参照してください

### <a name="shard_map_name--shard_map_name"></a>SHARD_MAP_NAME = *shard_map_name*

`TYPE` 引数がシャード マップの名前を設定するためだけに `SHARD_MAP_MANAGER` に設定されている場合に使用します。

`TYPE` = `SHARD_MAP_MANAGER` の外部データ ソースを作成する方法を示す例については、「[Shard Map Manager の外部データ ソースを作成する](#a-create-a-shard-map-manager-external-data-source)」を参照してください

## <a name="permissions"></a>アクセス許可

[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] のデータベースに対する `CONTROL` アクセス許可が必要です。

## <a name="locking"></a>ロック

`EXTERNAL DATA SOURCE` オブジェクトを共有ロックします。

## <a name="examples"></a>例 :

### <a name="a-create-a-shard-map-manager-external-data-source"></a>A. Shard Map Manager の外部データ ソースを作成する

SHARD_MAP_MANAGER を参照する外部データ ソースを作成するには、SQL Database または仮想マシン上の SQL Server データベースで Shard Map Manager をホストする SQL Database サーバー名を指定します。

```sql
CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<password>' ;

CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred
WITH
  IDENTITY = '<username>',
  SECRET = '<password>' ;

CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc
WITH
  ( TYPE = SHARD_MAP_MANAGER ,
    LOCATION = '<server_name>.database.windows.net' ,
    DATABASE_NAME = 'ElasticScaleStarterKit_ShardMapManagerDb' ,
    CREDENTIAL = ElasticDBQueryCred ,
    SHARD_MAP_NAME = 'CustomerIDShardMap'
  ) ;
```

チュートリアルについては、[シャーディングのエラスティック クエリの概要 (行方向のパーティション分割)][sharded_eq_tutorial] のトピックを参照してください。

### <a name="b-create-an-rdbms-external-data-source"></a>B. RDBMS の外部データ ソースを作成する

RDBMS を参照する外部データ ソースを作成するには、SQL Database でリモート データベースの SQL Database サーバー名を指定します。

```sql
CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<password>' ;

CREATE DATABASE SCOPED CREDENTIAL SQL_Credential
WITH
  IDENTITY = '<username>' ,
  SECRET = '<password>' ;

CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc
WITH
  ( TYPE = RDBMS ,
    LOCATION = '<server_name>.database.windows.net' ,
    DATABASE_NAME = 'Customers' ,
    CREDENTIAL = SQL_Credential
  ) ;
```

RDBMS のチュートリアルについては、[クロスデータベース クエリの概要 (列方向のパーティション分割)][remote_eq_tutorial] のトピックを参照してください。

## <a name="examples-bulk-operations"></a>例 :一括操作

> [!IMPORTANT]
> 一括操作用の外部データ ソースの構成時に、`LOCATION` URL の末尾に、 **/** 、ファイル名、Shared Access Signature パラメーターを追加しないでください。

### <a name="c-create-an-external-data-source-for-bulk-operations-retrieving-data-from-azure-storage"></a>C. Azure Storage からデータを取得する一括操作用の外部データ ソースを作成する

[BULK INSERT][bulk_insert] または [OPENROWSET][openrowset] を使用する一括操作に対し、次のデータ ソースを使用します。 資格情報は、`SHARED ACCESS SIGNATURE` を ID として設定する必要があり、SAS トークンの先頭に `?` があってはなりません。また、読み込む必要のあるファイル (たとえば `srt=o&sp=r`) に対して少なくとも読み取りアクセス許可が必要で、有効期限が有効である必要があります (すべての日付は UTC 時間です)。 Shared Access Signature に関する詳細については、「[Shared Access Signature (SAS) を使用][sas_token]」を参照してください。

```sql
CREATE DATABASE SCOPED CREDENTIAL AccessAzureInvoices
WITH
  IDENTITY = 'SHARED ACCESS SIGNATURE',
  -- Remove ? from the beginning of the SAS token
  SECRET = '******srt=sco&sp=rwac&se=2017-02-01T00:55:34Z&st=2016-12-29T16:55:34Z***************' ;

CREATE EXTERNAL DATA SOURCE MyAzureInvoices
WITH
  ( LOCATION = 'https://newinvoices.blob.core.windows.net/week3' ,
    CREDENTIAL = AccessAzureInvoices ,
    TYPE = BLOB_STORAGE
  ) ;
```

この使用例については、「[BULK INSERT][bulk_insert_example]」をご覧ください。

## <a name="see-also"></a>参照

- [CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)][create_dsc]
- [CREATE EXTERNAL TABLE (Transact-SQL)][create_etb]
- [sys.external_data_sources (Transact-SQL)][cat_eds]
- [Shared Access Signatures (SAS) の使用][sas_token]
- [エラスティック クエリの概要][intro_eq]

<!-- links to external pages -->
<!-- SQL Docs -->
[bulk_insert]: ./bulk-insert-transact-sql.md
[bulk_insert_example]: ./bulk-insert-transact-sql.md#f-importing-data-from-a-file-in-azure-blob-storage
[openrowset]: ../functions/openrowset-transact-sql.md
[create_dsc]: ./create-database-scoped-credential-transact-sql.md
[create_etb]: https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source
[alter_eds]: ./alter-external-data-source-transact-sql.md
[cat_eds]: ../../relational-databases/system-catalog-views/sys-external-data-sources-transact-sql.md
<!-- PolyBase docs -->
[intro_pb]: ../../relational-databases/polybase/polybase-guide.md
[mongodb_pb]: ../../relational-databases/polybase/polybase-configure-mongodb.md
[connectivity_pb]:https://docs.microsoft.com/sql/database-engine/configure-windows/polybase-connectivity-configuration-transact-sql
[connection_options]: ../../relational-databases/native-client/applications/using-connection-string-keywords-with-sql-server-native-client.md
[hint_pb]: ../../relational-databases/polybase/polybase-pushdown-computation.md#force-pushdown
<!-- Elastic Query Docs -->
[intro_eq]: /azure/azure-sql/database/elastic-query-overview
[remote_eq]: /azure/azure-sql/database/elastic-query-getting-started-vertical
[remote_eq_tutorial]: /azure/azure-sql/database/elastic-query-getting-started-vertical
[sharded_eq]: /azure/azure-sql/database/elastic-query-getting-started
[sharded_eq_tutorial]: /azure/azure-sql/database/elastic-query-getting-started

<!-- Azure Docs -->
[azure_ad]: /azure/data-lake-store/data-lake-store-authenticate-using-active-directory
[sas_token]: /azure/storage/storage-dotnet-shared-access-signature-part-1

::: moniker-end
::: moniker range="=azure-sqldw-latest"

:::row:::
    :::column:::
        [SQL Server](create-external-data-source-transact-sql.md?view=sql-server-ver15&preserve-view=true)
    :::column-end:::
    :::column:::
        [SQL Database](create-external-data-source-transact-sql.md?view=azuresqldb-current&preserve-view=true)
    :::column-end:::
    :::column:::
        **_\* Azure Synapse<br />Analytics \*_** &nbsp;
    :::column-end:::
    :::column:::
        [Analytics Platform<br />System (PDW)](create-external-data-source-transact-sql.md?view=aps-pdw-2016-au7&preserve-view=true)
    :::column-end:::
:::row-end:::

&nbsp;

## <a name="overview-azure-synapse-analytics"></a>概要:Azure Synapse Analytics

PolyBase 用の外部データ ソースを作成します。 外部データ ソースを使用して接続を確立し、次の主なユース ケースをサポートします。[PolyBase][intro_pb] を使用したデータ仮想化とデータ読み込み

> [!IMPORTANT]  
> Azure SQL Database と[エラスティック クエリ][remote_eq]を使用して SQL Analytics リソースに対してクエリを実行するために外部データ ソースを作成するには、「[SQL Database](create-external-data-source-transact-sql.md?view=azuresqldb-current&preserve-view=true)」を参照してください。

## <a name="syntax"></a>構文

### [[!INCLUDE[sss-dedicated-pool-md.md](../../includes/sss-dedicated-pool-md.md)]](#tab/dedicated)
```syntaxsql
CREATE EXTERNAL DATA SOURCE <data_source_name>
WITH
  ( [ LOCATION = '<prefix>://<path>[:<port>]' ]
    [ [ , ] CREDENTIAL = <credential_name> ]
    [ [ , ] TYPE = HADOOP ]
[ ; ]
```
### [[!INCLUDE[sssod-md.md](../../includes/sssod-md.md)]](#tab/serverless)
```syntaxsql
CREATE EXTERNAL DATA SOURCE <data_source_name>  
WITH
(    LOCATION = '<prefix>://<path>[:<port>]'
) 
[;]
```
---

## <a name="arguments"></a>引数

### <a name="data_source_name"></a>data_source_name

データ ソースのユーザー定義の名前を指定します。 名前は、[!INCLUDE[ssSDW](../../includes/sssdw-md.md)] の [!INCLUDE[ssazure_md](../../includes/ssazure_md.md)] 内で一意である必要があります。

### <a name="location--prefixpathport"></a>LOCATION = *`'<prefix>://<path[:port]>'`*

接続プロトコルと外部データ ソースへのパスを指定します。

| 外部データ ソース        | 場所プレフィックス | ロケーション パス                                         |
| --------------------------- | --------------- | ----------------------------------------------------- |
| Azure Data Lake Store Gen 1 | `adl`           | `<storage_account>.azuredatalake.net`                 |
| Azure Data Lake Store Gen 2 | `abfs[s]`       | `<container>@<storage_account>.dfs.core.windows.net`  |
| Azure V2 ストレージ アカウント    | `wasb[s]`       | `<container>@<storage_account>.blob.core.windows.net` |

場所のパス:

- `<container>` = データを保持するストレージ アカウントのコンテナー。 ルート コンテナーは読み取り専用で、このコンテナーにデータを書き込むことはできません。
- `<storage_account>` = Azure リソースのストレージ アカウント名。

場所を設定する場合の追加の注意事項とガイダンス:

- 既定のオプションでは、[Azure Data Lake Storage Gen 2 のプロビジョニング時に`enable secure SSL connections`] を使用します。 この設定を有効にした場合は、セキュリティで保護された TLS/SSL 接続を選択したときに `abfss` を使用する必要があります。 注意 `abfss` は、セキュリティで保護されていない TLS 接続にも使用できます。
- Azure Synapse では、オブジェクトの作成時に、外部データ ソースの存在が検証されません。 . 検証するには、外部データ ソースを使用して外部テーブルを作成します。
- 一貫性のあるクエリ セマンティクスを確保するため、Hadoop をクエリする際は、すべてのテーブルに同じ外部データ ソースを使用します。
- `wasbs` は、セキュリティで保護された TLS 接続を使用してデータが送信されるため、推奨されます。
- wasb:// インターフェイスを使用して PolyBase 経由でデータにアクセスする場合、Azure V2 Storage アカウントでは階層型名前空間はサポートされません。

### <a name="credential--credential_name"></a>CREDENTIAL = *credential_name*

外部データ ソースへの認証の資格情報のデータベース スコープを指定します。

資格情報の作成時の追加の注意事項とガイダンス:

- Azure Storage または Azure Data Lake Store (ADLS) Gen 2 から Azure Synapse Analytics にデータを読み込むには、Azure Storage キーを使用します。
- `CREDENTIAL` は、データがセキュリティ保護されている場合にのみ必須です。 匿名アクセスを許可するデータ セットには、`CREDENTIAL` は必要ありません。

データベース スコープ資格情報を作成するには、「[CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)][create_dsc]」を参照してください。

### <a name="type--hadoop"></a>TYPE = *HADOOP*

構成されている外部データ ソースの種類を指定します。 このパラメーターは常に必要ではありません。

- 外部データ ソースが Azure Storage、ADLS Gen 1、または ADLS Gen 2 の場合、HADOOP を使用します。

`TYPE` = `HADOOP` を使用して、Azure Storage からデータを読み込む例については、「[サービス プリンシパルを使用して Azure Data Lake Store Gen 1 または 2 を参照する外部データ ソースを作成する](#b-create-external-data-source-to-reference-azure-data-lake-store-gen-1-or-2-using-a-service-principal)」を参照してください。

## <a name="permissions"></a>アクセス許可

データベースに対する `CONTROL` 権限が必要です。

## <a name="locking"></a>ロック

`EXTERNAL DATA SOURCE` オブジェクトを共有ロックします。

## <a name="security"></a>セキュリティ

PolyBase では、ほとんどの外部データ ソースにプロキシ ベースの認証をサポートします。 データベース スコープ資格情報を作成して、プロキシ アカウントを作成します。

SQL Server ビッグ データ クラスターでストレージまたはデータ プールに接続すると、ユーザーの資格情報が、バックエンド システムに渡されます。 データ プール自体にログインを作成し、パススルー認証を有効にします。

現在 `HADOOP` 型の SAS トークンはサポートされていません。 それはストレージ アカウントのアクセス キーでのみサポートされます。 `HADOOP` 型と SAS 資格情報で外部データ ソースを作成しようとすると、次のエラーで失敗します。

`Msg 105019, Level 16, State 1 - EXTERNAL TABLE access failed due to internal error: 'Java exception raised on call to HdfsBridge_Connect. Java exception message: Parameters provided to connect to the Azure storage account are not valid.: Error [Parameters provided to connect to the Azure storage account are not valid.] occurred while accessing external file.'`

## <a name="examples"></a>例 :

### <a name="a-create-external-data-source-to-access-data-in-azure-storage-using-the-wasb-interface"></a>A. wasb:// インターフェイスを使用して Azure Storage のデータにアクセスするための外部データソースを作成する
この例では、外部データ ソースは、`logs` という名前の Azure V2 Storage アカウントです。 コンテナーは `daily`と呼ばれます。 Azure Storage の外部データ ソースはデータ転送専用です。 述語のプッシュ ダウンはサポートされません。 `wasb://` インターフェイスを使用してデータにアクセスする場合、階層型名前空間はサポートされません。

この例では、Azure Storage への認証用にデータベース スコープ資格情報を作成する方法を示します。 データベースの資格情報シークレットで、Azure Storage アカウント キーを指定します。 Azure ストレージへの認証時に使用されないため、データベース スコープ資格情報 ID には任意の文字列を指定できます。

```sql
-- Create a database master key if one does not already exist, using your own password. This key is used to encrypt the credential secret in next step.
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'S0me!nfo' ;

-- Create a database scoped credential with Azure storage account key as the secret.
CREATE DATABASE SCOPED CREDENTIAL AzureStorageCredential
WITH
  IDENTITY = '<my_account>',
  SECRET = '<azure_storage_account_key>' ;

-- Create an external data source with CREDENTIAL option.
CREATE EXTERNAL DATA SOURCE MyAzureStorage
WITH
  ( LOCATION = 'wasbs://daily@logs.blob.core.windows.net/' ,
    CREDENTIAL = AzureStorageCredential ,
    TYPE = HADOOP
  ) ;
```

### <a name="b-create-external-data-source-to-reference-azure-data-lake-store-gen-1-or-2-using-a-service-principal"></a>B. サービス プリンシパルを使用して、Azure Data Lake Store Gen 1 または 2 を参照する外部データ ソースを作成する

Azure Data Lake Store の接続は、お使いの ADLS URI と Azure Active Directory アプリケーションのサービス プリンシパルに基づいています。 このアプリケーションの作成方法については、[Active Directory を使用した Data Lake Store 認証][azure_ad]に関するページを参照してください。

```sql
-- If you do not have a Master Key on your DW you will need to create one.
CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<password>' ;

-- These values come from your Azure Active Directory Application used to authenticate to ADLS
CREATE DATABASE SCOPED CREDENTIAL ADLS_credential
WITH
  -- IDENTITY = '<clientID>@<OAuth2.0TokenEndPoint>' ,
  IDENTITY = '536540b4-4239-45fe-b9a3-629f97591c0c@https://login.microsoftonline.com/42f988bf-85f1-41af-91ab-2d2cd011da47/oauth2/token' ,
  -- SECRET = '<KEY>'
  SECRET = 'BjdIlmtKp4Fpyh9hIvr8HJlUida/seM5kQ3EpLAmeDI=' 
;

-- For Gen 1 - Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure Data Lake Storage.
-- LOCATION: Provide Data Lake Storage Gen 1 account name and URI
-- CREDENTIAL: Provide the credential created in the previous step
CREATE EXTERNAL DATA SOURCE AzureDataLakeStore
WITH
  ( LOCATION = 'adl://newyorktaxidataset.azuredatalakestore.net' ,
    CREDENTIAL = ADLS_credential ,
    TYPE = HADOOP
  ) ;

-- For Gen 2 - Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure Data Lake Storage.
-- LOCATION: Provide Data Lake Storage Gen 2 account name and URI
-- CREDENTIAL: Provide the credential created in the previous step
CREATE EXTERNAL DATA SOURCE AzureDataLakeStore
WITH
  -- Please note the abfss endpoint when your account has secure transfer enabled
  ( LOCATION = 'abfss://data@newyorktaxidataset.dfs.core.windows.net' , 
    CREDENTIAL = ADLS_credential ,
    TYPE = HADOOP
  ) ;
```

### <a name="c-create-external-data-source-to-reference-azure-data-lake-store-gen-2-using-the-storage-account-key"></a>C. ストレージ アカウント キーを使用して、Azure Data Lake Store Gen 2 を参照する外部データ ソースを作成する

```sql
-- If you do not have a Master Key on your DW you will need to create one.
CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<password>' ;

CREATE DATABASE SCOPED CREDENTIAL ADLS_credential
WITH
-- IDENTITY = '<storage_account_name>' ,
  IDENTITY = 'newyorktaxidata' ,
-- SECRET = '<storage_account_key>'
  SECRET = 'yz5N4+bxSb89McdiysJAzo+9hgEHcJRJuXbF/uC3mhbezES/oe00vXnZEl14U0lN3vxrFKsphKov16C0w6aiTQ=='
;

-- Note this example uses a Gen 2 secured endpoint (abfss)
CREATE EXTERNAL DATA SOURCE <data_source_name>
WITH
  ( LOCATION = 'abfss://2013@newyorktaxidataset.dfs.core.windows.net' ,
    CREDENTIAL = ADLS_credential ,
    TYPE = HADOOP
  ) ;
```

### <a name="d-create-external-data-source-to-reference-polybase-connectivity-to-azure-data-lake-store-gen-2-using-abfs"></a>D. abfs:// を使用して Azure Data Lake Store Gen 2 への Polybase 接続を参照する外部データ ソースを作成する

[マネージド ID](/azure/active-directory/managed-identities-azure-resources/overview
) メカニズムで Azure Data Lake Store Gen2 アカウントに接続するとき、SECRET を指定する必要はありません。

```sql
-- If you do not have a Master Key on your DW you will need to create one
CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<password>' ;

--Create database scoped credential with **IDENTITY = 'Managed Service Identity'**

CREATE DATABASE SCOPED CREDENTIAL msi_cred 
WITH IDENTITY = 'Managed Service Identity' ;

--Create external data source with abfss:// scheme for connecting to your Azure Data Lake Store Gen2 account

CREATE EXTERNAL DATA SOURCE ext_datasource_with_abfss 
WITH 
  ( TYPE = HADOOP , 
    LOCATION = 'abfss://myfile@mystorageaccount.dfs.core.windows.net' , 
    CREDENTIAL = msi_cred
  ) ;
```

## <a name="see-also"></a>参照

- [CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)][create_dsc]
- [CREATE EXTERNAL FILE FORMAT (Transact-SQL)][create_eff]
- [CREATE EXTERNAL TABLE (Transact-SQL)][create_etb]
- [CREATE EXTERNAL TABLE AS SELECT (Azure Synapse Analytics)][create_etb_as_sel]
- [CREATE TABLE AS SELECT (Azure Synapse Analytics)][create_tbl_as_sel]
- [sys.external_data_sources (Transact-SQL)][cat_eds]
- [Shared Access Signatures (SAS) の使用][sas_token]

<!-- links to external pages -->
<!-- SQL Docs -->
[bulk_insert]: ./bulk-insert-transact-sql.md
[bulk_insert_example]: ./bulk-insert-transact-sql.md#f-importing-data-from-a-file-in-azure-blob-storage
[openrowset]: ../functions/openrowset-transact-sql.md

[create_dsc]: ./create-database-scoped-credential-transact-sql.md
[create_eff]: ./create-external-file-format-transact-sql.md
[create_etb]: https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source
[create_etb_as_sel]: ./create-external-table-as-select-transact-sql.md?view=azure-sqldw-latest&preserve-view=true
[create_tbl_as_sel]: ./create-table-as-select-azure-sql-data-warehouse.md?view=azure-sqldw-latest&preserve-view=true

[alter_eds]: ./alter-external-data-source-transact-sql.md

[cat_eds]: ../../relational-databases/system-catalog-views/sys-external-data-sources-transact-sql.md
<!-- PolyBase docs -->
[intro_pb]: ../../relational-databases/polybase/polybase-guide.md
[mongodb_pb]: ../../relational-databases/polybase/polybase-configure-mongodb.md
[connectivity_pb]:https://docs.microsoft.com/sql/database-engine/configure-windows/polybase-connectivity-configuration-transact-sql
[connection_options]: ../../relational-databases/native-client/applications/using-connection-string-keywords-with-sql-server-native-client.md
[hint_pb]: ../../relational-databases/polybase/polybase-pushdown-computation.md#force-pushdown
<!-- Elastic Query Docs -->
[intro_eq]: /azure/azure-sql/database/elastic-query-overview
[remote_eq]: /azure/azure-sql/database/elastic-query-getting-started-vertical
[remote_eq_tutorial]: /azure/azure-sql/database/elastic-query-getting-started-vertical
[sharded_eq]: /azure/azure-sql/database/elastic-query-getting-started
[sharded_eq_tutorial]: /azure/azure-sql/database/elastic-query-getting-started

<!-- Azure Docs -->
[azure_ad]: /azure/data-lake-store/data-lake-store-authenticate-using-active-directory
[sas_token]: /azure/storage/storage-dotnet-shared-access-signature-part-1

::: moniker-end
::: moniker range=">=aps-pdw-2016"

:::row:::
    :::column:::
        [SQL Server](create-external-data-source-transact-sql.md?view=sql-server-ver15&preserve-view=true)
    :::column-end:::
    :::column:::
        [SQL Database](create-external-data-source-transact-sql.md?view=azuresqldb-current&preserve-view=true)
    :::column-end:::
    :::column:::
        [Azure Synapse<br />Analytics](create-external-data-source-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)
    :::column-end:::
    :::column:::
        **_\* Analytics<br />Platform System (PDW) \*_** &nbsp;
    :::column-end:::
:::row-end:::

&nbsp;

## <a name="overview-analytics-platform-system"></a>概要:分析プラットフォーム システム

PolyBase クエリ用の外部データ ソースを作成します。 外部データ ソースを使用して接続を確立し、次のユース ケースをサポートします。[PolyBase][intro_pb] を使用したデータ仮想化とデータ読み込み

## <a name="syntax"></a>構文

```syntaxsql
CREATE EXTERNAL DATA SOURCE <data_source_name>
WITH
  ( [ LOCATION = '<prefix>://<path>[:<port>]' ]
    [ [ , ] CREDENTIAL = <credential_name> ]
    [ [ , ] TYPE = HADOOP ]
    [ [ , ] RESOURCE_MANAGER_LOCATION = '<resource_manager>[:<port>]' )
[ ; ]
```

## <a name="arguments"></a>引数

### <a name="data_source_name"></a>data_source_name

データ ソースのユーザー定義の名前を指定します。 名前は、[!INCLUDE[ssPDW](../../includes/sspdw-md.md)] のサーバー内で一意である必要があります。

### <a name="location--prefixpathport"></a>LOCATION = *`'<prefix>://<path[:port]>'`*

接続プロトコルと外部データ ソースへのパスを指定します。

| 外部データ ソース    | 場所プレフィックス | ロケーション パス                                         |
| ----------------------- | --------------- | ----------------------------------------------------- |
| Cloudera または Hortonworks | `hdfs`          | `<Namenode>[:port]`                                   |
| Azure Storage アカウント   | `wasb[s]`       | `<container>@<storage_account>.blob.core.windows.net` |

場所のパス:

- `<Namenode>` = Hadoop クラスター内の `Namenode` のマシン名、ネーム サービス URI、または IP アドレス。 PolyBase では Hadoop クラスターで使用されているすべての DNS 名が解決される必要があります。 <!-- For highly available Hadoop configurations, provide the Nameservice ID as the `LOCATION`. -->
- `port` = 外部データ ソースがリッスンしているポート。 Hadoop では、`fs.defaultFS` 構成パラメーターを使用してポートを見つけることができます。 既定値は 8020 です。
- `<container>` = データを保持するストレージ アカウントのコンテナー。 ルート コンテナーは読み取り専用で、このコンテナーにデータを書き込むことはできません。
- `<storage_account>` = Azure リソースのストレージ アカウント名。

場所を設定する場合の追加の注意事項とガイダンス:

- PDW エンジンでは、オブジェクトの作成時に、外部データ ソースの存在が検証されません。 検証するには、外部データ ソースを使用して外部テーブルを作成します。
- 一貫性のあるクエリ セマンティクスを確保するため、Hadoop をクエリする際は、すべてのテーブルに同じ外部データ ソースを使用します。
- `wasbs` は、セキュリティで保護された TLS 接続を使用してデータが送信されるため、推奨されます。
- 階層型名前空間は、wasb:// 経由で Azure Storage アカウントと一緒に使用する場合はサポートされません。
- Hadoop `Namenode` のフェールオーバー時に、PolyBase クエリを確実に成功させるため、Hadoop クラスターの `Namenode` に仮想 IP アドレスを使用することを検討してください。 使用しない場合は、[ALTER EXTERNAL DATA SOURCE][alter_eds] コマンドを実行して新しい場所を示します。

### <a name="credential--credential_name"></a>CREDENTIAL = *credential_name*

外部データ ソースへの認証の資格情報のデータベース スコープを指定します。

資格情報の作成時の追加の注意事項とガイダンス:

- Azure Storage から Azure Synapse または PDW にデータを読み込むには、Azure Storage キーを使用します。
- `CREDENTIAL` は、データがセキュリティ保護されている場合にのみ必須です。 匿名アクセスを許可するデータ セットには、`CREDENTIAL` は必要ありません。

### <a name="type---hadoop-"></a>TYPE = *[ HADOOP ]*

構成されている外部データ ソースの種類を指定します。 このパラメーターは常に必要ではありません。

- 外部データ ソースが Cloudera、Hortonworks、Azure Storage の場合は、HADOOP を使用します。

`TYPE` = `HADOOP` を使用して、Azure Storage からデータを読み込む例については、「[Hadoop を参照する外部データ ソースを作成する](#a-create-external-data-source-to-reference-hadoop)」を参照してください。

### <a name="resource_manager_location--resourcemanager_uriport"></a>RESOURCE_MANAGER_LOCATION = *'ResourceManager_URI[:port]'*

Hortonworks または Cloudera に接続する場合は、このオプションの値を構成します。

`RESOURCE_MANAGER_LOCATION` が定義されている場合、クエリ オプティマイザーでは、パフォーマンスを向上させるためにコストに基づいて決定が下されます。 MapReduce ジョブを使用して、Hadoop に計算をプッシュ ダウンできます。 `RESOURCE_MANAGER_LOCATION` を指定すると、Hadoop と SQL の間で転送されるデータ量が大幅に減少し、それによってクエリのパフォーマンスが向上する可能性があります。

Resource Manager を指定しない場合、Hadoop への計算のプッシュが、PolyBase クエリに対して無効になります。

ポートが指定されていない場合、'hadoop connectivity' 構成の現在の設定を使用して、既定値が選択されます。

| Hadoop Connectivity | Resource Manager の既定のポート |
| ------------------- | ----------------------------- |
| 1                   | 50300                         |
| 2                   | 50300                         |
| 3                   | 8021                          |
| 4                   | 8032                          |
| 5                   | 8050                          |
| 6                   | 8032                          |
| 7                   | 8050                          |

サポートされている Hadoop バージョンの完全な一覧については、「[PolyBase 接続構成 (Transact-SQL)][connectivity_pb]」を参照してください。

> [!IMPORTANT]
> `RESOURCE_MANAGER_LOCATION` 値は、外部データ ソースを作成するときに検証されません。 正しくない値を入力すると、指定された値で解決できないため、プッシュ ダウンが試行されるたびに実行時にクエリ エラーが発生する可能性があります。

「[プッシュダウンが有効になっている Hadoop を参照する外部データ ソースを作成する](#b-create-external-data-source-to-reference-hadoop-with-push-down-enabled)」では、具体的な例と追加のガイダンスを提供しています。

## <a name="permissions"></a>アクセス許可

[!INCLUDE[ssPDW](../../includes/sspdw-md.md)] のデータベースに対する `CONTROL` アクセス許可が必要です。

> [!NOTE]
> PDW の以前のリリースでは、外部データ ソースの作成には、`ALTER ANY EXTERNAL DATA SOURCE` アクセス許可が必要でした。

## <a name="locking"></a>ロック

`EXTERNAL DATA SOURCE` オブジェクトを共有ロックします。

## <a name="security"></a>セキュリティ

PolyBase では、ほとんどの外部データ ソースにプロキシ ベースの認証をサポートします。 データベース スコープ資格情報を作成して、プロキシ アカウントを作成します。

現在 `HADOOP` 型の SAS トークンはサポートされていません。 それはストレージ アカウントのアクセス キーでのみサポートされます。 `HADOOP` 型と SAS 資格情報で外部データ ソースを作成しようとすると、次のエラーで失敗します。

`Msg 105019, Level 16, State 1 - EXTERNAL TABLE access failed due to internal error: 'Java exception raised on call to HdfsBridge_Connect. Java exception message: Parameters provided to connect to the Azure storage account are not valid.: Error [Parameters provided to connect to the Azure storage account are not valid.] occurred while accessing external file.'`

## <a name="examples"></a>例 :

### <a name="a-create-external-data-source-to-reference-hadoop"></a>A. Hadoop を参照する外部データ ソースを作成する

Hortonworks または Cloudera Hadoop クラスターを参照する外部データ ソースを作成するには、Hadoop `Namenode` のマシン名または IP アドレスとポートを指定します。 <!-- Provide the Nameservice ID as the `LOCATION` for highly available configurations. -->

```sql
CREATE EXTERNAL DATA SOURCE MyHadoopCluster
WITH
  ( LOCATION = 'hdfs://10.10.10.10:8050' ,
    TYPE = HADOOP
  ) ;
```

### <a name="b-create-external-data-source-to-reference-hadoop-with-push-down-enabled"></a>B. プッシュダウンが有効になっている Hadoop を参照する外部データ ソースを作成する

`RESOURCE_MANAGER_LOCATION` オプションを指定して、PolyBase クエリの Hadoop への計算のプッシュダウンを有効にします。 有効にすると、PolyBase によって、クエリの計算を Hadoop にプッシュするかどうかがコストに基づいて決定されます。

```sql
CREATE EXTERNAL DATA SOURCE MyHadoopCluster
WITH
  ( LOCATION = 'hdfs://10.10.10.10:8020'
    TYPE = HADOOP
    RESOURCE_MANAGER_LOCATION = '10.10.10.10:8050'
) ;
```

### <a name="c-create-external-data-source-to-reference-kerberos-secured-hadoop"></a>C. Kerberos でセキュリティ保護された Hadoop を参照する外部データ ソースを作成する

Hadoop クラスターが Kerberos でセキュリティ保護されていることを確認するには、Hadoop core-site.xml で hadoop.security.authentication プロパティの値を確認します。 Kerberos でセキュリティ保護された Hadoop クラスターを参照するには、ご自分の Kerberos ユーザー名とパスワードを含むデータベース スコープの資格情報を指定する必要があります。 データベース マスター キーは、データベース スコープの資格情報シークレットの暗号化に使用されます。

```sql
-- Create a database master key if one does not already exist, using your own password. This key is used to encrypt the credential secret in next step.
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'S0me!nfo' ;

-- Create a database scoped credential with Kerberos user name and password.
CREATE DATABASE SCOPED CREDENTIAL HadoopUser1
WITH
  IDENTITY = '<hadoop_user_name>' ,
  SECRET = '<hadoop_password>' ;

-- Create an external data source with CREDENTIAL option.
CREATE EXTERNAL DATA SOURCE MyHadoopCluster
WITH
  ( LOCATION = 'hdfs://10.10.10.10:8050' ,
    CREDENTIAL = HadoopUser1 ,
    TYPE = HADOOP ,
    RESOURCE_MANAGER_LOCATION = '10.10.10.10:8050'
  ) ;
```

### <a name="d-create-external-data-source-to-access-data-in-azure-storage-using-the-wasb-interface"></a>D. wasb:// インターフェイスを使用して Azure Storage のデータにアクセスするための外部データソースを作成する

この例では、外部データ ソースは、`logs` という名前の Azure V2 Storage アカウントです。 コンテナーは `daily`と呼ばれます。 Azure Storage の外部データ ソースはデータ転送専用です。 述語のプッシュ ダウンはサポートされません。 `wasb://` インターフェイスを使用してデータにアクセスする場合、階層型名前空間はサポートされません。

この例では、Azure ストレージへの認証用にデータベース スコープ資格情報を作成する方法を示します。 データベースの資格情報シークレットで、Azure ストレージ アカウント キーを指定します。 Azure ストレージへの認証時に使用されないため、データベース スコープ資格情報 ID には任意の文字列を指定できます。

```sql
-- Create a database master key if one does not already exist, using your own password. This key is used to encrypt the credential secret in next step.
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'S0me!nfo' ;

-- Create a database scoped credential with Azure storage account key as the secret.
CREATE DATABASE SCOPED CREDENTIAL AzureStorageCredential
WITH
  IDENTITY = '<my_account>' ,
  SECRET = '<azure_storage_account_key>' ;

-- Create an external data source with CREDENTIAL option.
CREATE EXTERNAL DATA SOURCE MyAzureStorage
WITH
  ( LOCATION = 'wasbs://daily@logs.blob.core.windows.net/'
    CREDENTIAL = AzureStorageCredential
    TYPE = HADOOP
  ) ;
```


## <a name="see-also"></a>参照

- [CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)][create_dsc]
- [CREATE EXTERNAL FILE FORMAT (Transact-SQL)][create_eff]
- [CREATE EXTERNAL TABLE (Transact-SQL)][create_etb]
- [sys.external_data_sources (Transact-SQL)][cat_eds]
- [Shared Access Signatures (SAS) の使用][sas_token]

<!-- links to external pages -->
<!-- SQL Docs -->
[bulk_insert]: ./bulk-insert-transact-sql.md
[bulk_insert_example]: ./bulk-insert-transact-sql.md#f-importing-data-from-a-file-in-azure-blob-storage
[openrowset]: ../functions/openrowset-transact-sql.md

[create_dsc]: ./create-database-scoped-credential-transact-sql.md
[create_eff]: ./create-external-file-format-transact-sql.md
[create_etb]: https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source
[create_etb_as_sel]: ./create-external-table-as-select-transact-sql.md?view=azure-sqldw-latest&preserve-view=true
[create_tbl_as_sel]: ./create-table-as-select-azure-sql-data-warehouse.md?view=azure-sqldw-latest&preserve-view=true

[alter_eds]: ./alter-external-data-source-transact-sql.md

[cat_eds]: ../../relational-databases/system-catalog-views/sys-external-data-sources-transact-sql.md
<!-- PolyBase docs -->
[intro_pb]: ../../relational-databases/polybase/polybase-guide.md
[mongodb_pb]: ../../relational-databases/polybase/polybase-configure-mongodb.md
[connectivity_pb]:https://docs.microsoft.com/sql/database-engine/configure-windows/polybase-connectivity-configuration-transact-sql
[connection_options]: ../../relational-databases/native-client/applications/using-connection-string-keywords-with-sql-server-native-client.md
[connection_option_keyword]: ../../relational-databases/native-client/applications/using-connection-string-keywords-with-sql-server-native-client.md#odbc-driver-connection-string-keywords
[hint_pb]: ../../relational-databases/polybase/polybase-pushdown-computation.md#force-pushdown
<!-- Elastic Query Docs -->
[intro_eq]: /azure/azure-sql/database/elastic-query-overview
[remote_eq]: /azure/azure-sql/database/elastic-query-getting-started-vertical
[remote_eq_tutorial]: /azure/azure-sql/database/elastic-query-getting-started-vertical
[sharded_eq]: /azure/azure-sql/database/elastic-query-getting-started
[sharded_eq_tutorial]: /azure/azure-sql/database/elastic-query-getting-started

<!-- Azure Docs -->
[azure_ad]: /azure/data-lake-store/data-lake-store-authenticate-using-active-directory
[sas_token]: /azure/storage/storage-dotnet-shared-access-signature-part-1

::: moniker-end
