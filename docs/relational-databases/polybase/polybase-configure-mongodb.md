---
title: 外部データへのアクセス:MongoDB - PolyBase
description: この記事では、SQL Server インスタンス上で PolyBase を使用して、MongoDB 上の外部データに対してクエリを実行する方法について説明します。 外部データを参照する外部テーブルを作成します。
ms.date: 03/05/2021
ms.metadata: seo-lt-2019
ms.prod: sql
ms.technology: polybase
ms.topic: conceptual
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: mikeray
monikerRange: '>= sql-server-linux-ver15 || >= sql-server-ver15'
ms.openlocfilehash: 6fa6be71b0dde8184ed2c807d4170135b60d3557
ms.sourcegitcommit: 295b9dfc758471ef7d238a2b0f92f93e34acbb1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106054626"
---
# <a name="configure-polybase-to-access-external-data-in-mongodb"></a>MongoDB 上の外部データにアクセスするための PolyBase の構成

 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

この記事では、SQL Server インスタンス上で PolyBase を使用して、MongoDB 上の外部データに対してクエリを実行する方法について説明します。

## <a name="prerequisites"></a>前提条件

PolyBase をインストールしていない場合は、「[PolyBase のインストール](polybase-installation.md)」をご覧ください。

データベース スコープ資格情報より前に、[マスター キー](../../t-sql/statements/create-master-key-transact-sql.md)を作成しておく必要があります。 
    

## <a name="configure-a-mongodb-external-data-source"></a>MongoDB の外部データ ソースを構成する

MongoDB データ ソースのデータに対してクエリを実行するには、外部テーブルを作成して外部データを参照する必要があります。 このセクションでは、これらの外部テーブルを作成するサンプル コードを示します。

このセクションでは以下の Transact-SQL コマンドが使用されます。

- [CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)](../../t-sql/statements/create-database-scoped-credential-transact-sql.md)
- [CREATE EXTERNAL DATA SOURCE (Transact-SQL)](../../t-sql/statements/create-external-data-source-transact-sql.md) 
- [CREATE STATISTICS (Transact-SQL)](../../t-sql/statements/create-statistics-transact-sql.md)

1. MongoDB ソースにアクセスするために、データベース スコープ資格情報を作成します。

   次のスクリプトは、データベース スコープ資格情報を作成します。 スクリプトを実行する前に、お使いの環境に合わせて更新します。

    - `<credential_name>` を資格情報の名前に置き換えます。
    - `<username>` を外部ソースのユーザー名に置き換えます。
    - `<password>` を適切なパスワードに置き換えます。 

    ```sql
    CREATE DATABASE SCOPED CREDENTIAL <credential_name> WITH IDENTITY = '<username>', Secret = '<password>';
    ```

   > [!IMPORTANT]
   > PolyBase 用の MongoDB ODBC コネクタでサポートされるのは、Kerberos 認証ではなく、基本認証のみです。

1. 外部データ ソースを作成します。

    次のスクリプトは、外部データ ソースを作成します。 参考情報については、「[CREATE EXTERNAL DATA SOURCE](../../t-sql/statements/create-external-data-source-transact-sql.md)」を参照してください。 スクリプトを実行する前に、お使いの環境に合わせて更新します。

    - 場所を更新します。 お使いの環境に合わせて `<server>` と `<port>` を設定します。
    - `<credential_name>` を前のステップで作成した資格情報の名前に置き換えます。
    - 外部ソースに対してプッシュダウン計算を指定する場合は、必要に応じて `PUSHDOWN = ON` または `PUSHDOWN = OFF` を指定できます。

    ```sql
    CREATE EXTERNAL DATA SOURCE external_data_source_name
    WITH (LOCATION = '<mongodb://<server>[:<port>]>'
    [ [ , ] CREDENTIAL = <credential_name> ]
    [ [ , ] CONNECTION_OPTIONS = '<key_value_pairs>'[,...]]
    [ [ , ] PUSHDOWN = { ON | OFF } ])
    [ ; ]
    ```

1. **省略可能:** 外部テーブルの統計を作成します。

    最適なクエリのパフォーマンスを得るために、外部テーブルの列、特に結合、フィルター、集計に使用される列に対して統計を作成することをお勧めします。

    ```sql
    CREATE STATISTICS statistics_name ON customer (C_CUSTKEY) WITH FULLSCAN; 
    ```

>[!IMPORTANT]
>外部データ ソースを作成すると、[CREATE EXTERNAL TABLE](../../t-sql/statements/create-external-table-transact-sql.md) コマンドを使用して、そのソース上でクエリ可能なテーブルを作成することができます。
>
>例については、[MongoDB の外部テーブルの作成](../../t-sql/statements/create-external-table-transact-sql.md#k-create-an-external-table-for-mongodb)に関する記述を参照してください。

## <a name="mongodb-connection-options"></a>MongoDB の接続オプション

MongoDB の接続オプションの詳細については、[接続文字列の URI 形式に関する MongoDB のドキュメント](https://docs.mongodb.com/manual/reference/connection-string/#connection-string-options)を照してください。

## <a name="flattening"></a>フラット化

フラット化は、MongoDB ドキュメント コレクションの入れ子になったデータと繰り返しデータに対して有効になります。 ユーザーは、入れ子になったデータや繰り返しデータを含む可能性のある MongoDB ドキュメント コレクションに対して `create an external table` を有効にし、リレーショナル スキーマを明示的に指定する必要があります。 JSON の入れ子になったデータ型/繰り返しデータ型は次のようにフラット化されます

* オブジェクト: 中かっこで囲まれている順序付けられていないキー/値のコレクション (入れ子)

   - SQL Server では、各オブジェクト キーのテーブル列が作成されます

     * 列名: objectname_keyname

* 配列: コンマで区切られ、角かっこで囲まれた、順序付けられた値 (繰り返し)

   - SQL Server では、配列項目ごとに新しいテーブル行が追加されます

   - SQL Server では、配列ごとの列を作成して配列項目のインデックスが格納されます

     * 列名: arrayname_index

     * データ型: bigint

この手法にはいくつかの潜在的な問題があります。そのうちの 2 つを次に示します。

* 空の繰り返しフィールドは、同じレコードのフラット フィールドに含まれるデータを事実上マスクする

* 複数の繰り返しフィールドが存在すると、生成される行数が急増することがある

たとえば、SQL Server では、非リレーショナルの JSON 形式で格納されている MongoDB のサンプル データセット レストラン コレクションが評価されます。 各レストランには、入れ子になった住所フィールドと、異なる日に割り当てられた採点の配列があります。 次の図は、入れ子になった住所と入れ子になった繰り返しの採点がある一般的なレストランを示しています。

![MongoDB のフラット化](../../relational-databases/polybase/media/mongo-flattening.png "MongoDB レストランのフラット化")

住所オブジェクトは次のようにフラット化されます。

- 入れ子になったフィールド `restaurant.address.building` は `restaurant.address_building` になります
- 入れ子になったフィールド `restaurant.address.coord` は `restaurant.address_coord` になります
- 入れ子になったフィールド `restaurant.address.street` は `restaurant.address_street` になります
- 入れ子になったフィールド `restaurant.address.zipcode` は `restaurant.address_zipcode` になります

採点配列は次のようにフラット化されます。

| grades_date | grades_grade  | games_score | 
| ------------- | ------------------------- | -------------- |
|1393804800000 |A |2|
|1378857600000|A |6|
|135898560000 |A |10|
|1322006400000|A |9|
|1299715200000 |B |14|

## <a name="cosmos-db-connection"></a>Cosmos DB 接続

Cosmos DB の Mongo API および Mongo DB PolyBase コネクタを使用すると、**Cosmos DB インスタンス** の外部テーブルを作成することができます。 これは、上記と同じ手順に従って行います。 データベースのスコープ資格情報、サーバーのアドレス、ポート、場所の文字列が Cosmos DB サーバーのものを反映していることを確認してください。

## <a name="examples"></a>例

次の例では、次のパラメーターを使用して外部データ ソースを作成します。

| パラメーター | 値|
|---|---|
| 名前 | `external_data_source_name`|
| サービス | `mongodb0.example.com`|
| インスタンス | `27017`|
| レプリカ セット | `myRepl`|
| TLS | `true`|
| プッシュダウン計算 | `On`|

```sql
CREATE EXTERNAL DATA SOURCE external_data_source_name
    WITH (LOCATION = 'mongodb://mongodb0.example.com:27017',
    CONNECTION_OPTIONS = 'replicaSet=myRepl; tls=true',
    PUSHDOWN = ON ,
    CREDENTIAL = credential_name);
```

## <a name="next-steps"></a>次のステップ

PolyBase の詳細については、[SQL Server PolyBase の概要](polybase-guide.md)に関する記事をご覧ください。
