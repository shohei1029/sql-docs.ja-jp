---
title: 'Db2 から SQL Server に: 移行ガイド'
description: 'このガイドでは、SQL Server Migration Assistant for Db2(SSMA for Db2) を使用して、お使いの Db2 データベースを Microsoft SQL Server に移行する方法について説明します。 '
ms.custom: ''
ms.date: 03/19/2021
ms.prod: sql
ms.reviewer: ''
ms.technology: migration-guide
ms.topic: how-to
author: MashaMSFT
ms.author: mathoma
ms.openlocfilehash: 5d2a314c89fd162b675435e9d981869a4b1f0518
ms.sourcegitcommit: 17f05be5c08cf9a503a72b739da5ad8be15baea5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/25/2021
ms.locfileid: "105103845"
---
# <a name="migration-guide-db2-to-sql-server"></a>移行ガイド: Db2 から SQL Server
[!INCLUDE[sqlserver](../../../includes/applies-to-version/sqlserver.md)]

この移行ガイドでは、SQL Server Migration Assistant for Db2 を使用して、お使いのユーザー データベースを Db2 から SQL Server に移行する方法について説明します。 

その他の移行ガイドについては、[データベースの移行](https://docs.microsoft.com/data-migration)に関するページを参照してください。 


## <a name="prerequisites"></a>前提条件

お使いの Db2 データベースを SQL Server に移行するには、以下が必要です。

- お使いの[ソース環境のサポート](../../../ssma/db2/installing-ssma-for-db2-client-db2tosql.md#prerequisites)の確認。
- [SQL Server Migration Assistant (SSMA) for Db2](https://www.microsoft.com/download/details.aspx?id=54254)。
- ソースとターゲットの両方への接続と、十分なアクセス許可。 



## <a name="pre-migration"></a>移行前

前提条件を満たすと、環境のトポロジを検出し、移行の可能性を評価する準備は完了です。 

### <a name="assess"></a>アクセス 

SQL Server Migration Assistant (SSMA) for Db2 を使用すると、データベース オブジェクトとデータを確認し、データベースの移行を評価できます。 

評価を作成するには、次の手順を行います。

1. SQL Server Migration Assistant (SSMA) for Db2 を開きます。 
1. **[ファイル]** を選択し、 **[新しいプロジェクト]** を選択します。 
1. プロジェクト名とプロジェクトを保存する場所を指定し、ドロップダウンから SQL Server 移行ターゲットを選択します。 **[OK]** を選択します。 

   :::image type="content" source="media/db2-to-sql-server/new-project.png" alt-text="プロジェクトの詳細を入力し、[OK] を選択して保存します。":::


1. **[Db2 への接続]** ダイアログ ボックスで、Db2 接続の詳細の値を入力します。

   :::image type="content" source="media/db2-to-sql-server/connect-to-db2.png" alt-text="お使いの Db2 インスタンスに接続する":::


1. 移行する Db2 スキーマを右クリックし、 **[レポートの作成]** を選択します。 これにより、HTML レポートが生成されます。 また、スキーマの選択後、ナビゲーション バーの **[レポートの作成]** を選択することもできます。

   :::image type="content" source="media/db2-to-sql-server/create-report.png" alt-text="スキーマを右クリックし、[レポートの作成] を選択する":::

1. HTML レポートを確認し、変換の統計情報とエラーまたは警告を把握します。 また、Excel でレポートを開き、Db2 オブジェクトのインベントリとスキーマ変換の実行に必要な作業量を確認することもできます。 レポートの既定の場所は、SSMAProjects 内のレポート フォルダーです。

   (例: `drive:\<username>\Documents\SSMAProjects\MyDb2Migration\report\report_<date>`)。 

   :::image type="content" source="media/db2-to-sql-server/report.png" alt-text="レポートを確認して、エラーや警告を特定する":::


### <a name="validate-data-types"></a>データ型を検証する

既定のデータ型マッピングを検証し、必要に応じて要件に基づいて変更します。 これを行うには、次のステップに従います。 

1. メニューから **[ツール]** を選択します。 
1. **[プロジェクトの設定]** を選択します。 
1. **[Type mappings]** \(型マッピング\) タブを選択します。

   :::image type="content" source="media/db2-to-sql-server/type-mapping.png" alt-text="スキーマ、型マッピングの順に選択する":::

1. **Db2 メタデータ エクスプローラー** でテーブルを選択し、各テーブルの型マッピングを変更します。 

### <a name="convert-schema"></a>スキーマの変換 

スキーマを変換するには、次の手順を行います。

1. (省略可能) 動的またはアドホック クエリをステートメントに追加します。 ノードを右クリックし、 **[Add statements]\(ステートメントの追加\)** を選択します。 
1. **[SQL Server への接続]** を選択します。 
    1. 接続の詳細を入力して、SQL Server インスタンスに接続します。 
    1. ターゲット サーバー上の既存のデータベースに接続するか、新しい名前を指定してターゲット サーバー上に新しいデータベースを作成するかを選択します。 
    1. 認証の詳細を指定します。 
    1. **[接続]** を選択します。

   :::image type="content" source="media/db2-to-sql-server/connect-to-sql-server.png" alt-text="SQL Server に接続するための詳細を入力する":::

1. スキーマを右クリックして、 **[スキーマの変換]** を選択します。 または、お使いのスキーマを選択した後、上部のナビゲーション バーから **[スキーマの変換]** を選択することもできます。

   :::image type="content" source="media/db2-to-sql-server/convert-schema.png" alt-text="スキーマを右クリックして、[スキーマの変換] を選択する":::

1. 変換が完了したら、スキーマの構造を比較および確認して、潜在的な問題を特定し、推奨事項に基づいてそれに対処します。

   :::image type="content" source="media/db2-to-sql-server/compare-review-schema-structure.png" alt-text="スキーマの構造を比較し、確認して、潜在的な問題を特定し、推奨事項に基づいて対処する。":::

1. [出力] ペインの **[結果の確認]** を選択し、 **[エラー一覧]** ペインでエラーを確認します。 
1. オフライン スキーマ修復の演習のために、プロジェクトをローカルに保存します。 **[ファイル]** メニューから **[プロジェクトの保存]** を選択します。 これにより、スキーマを SQL Server に発行する前に、ソースとターゲットのスキーマをオフラインで評価し、修復を実行する機会が得られます。


## <a name="migrate"></a>Migrate

データベースの評価と不整合への対処が完了したら、次の手順は移行プロセスの実行です。

スキーマを発行し、データを移行するには、次の手順を行います。

1. スキーマを発行する: **SQL Server メタデータ エクスプローラー** の **[データベース]** ノードからデータベースを右クリックし、 **[データベースとの同期]** を選択します。

   :::image type="content" source="media/db2-to-sql-server/synchronize-with-database.png" alt-text="データベースを右クリックし、[データベースとの同期] を選択する":::

1. データを移行する: **Db2 メタデータ エクスプローラー** で、移行するデータベースまたはオブジェクトを右クリックし、 **[データの移行]** を選択します。 または、一番上の行のナビゲーション バーから **[データの移行]** を選択することもできます。 データベース全体のデータを移行するには、データベース名の横にあるチェック ボックスをオンにします。 個々のテーブルからデータを移行するには、データベースを展開し、[テーブル] を展開して、テーブルの横にあるチェック ボックスをオンにします。 個々のテーブルのデータを除外するには、次のチェック ボックスをオフにします。

   :::image type="content" source="media/db2-to-sql-server/migrate-data.png" alt-text="スキーマを右クリックし、データの移行を選択する":::

1. Db2 と SQL Server インスタンスの両方の接続の詳細を指定します。 
1. 移行が完了したら、**データ移行** レポートを表示します。  

   :::image type="content" source="media/db2-to-sql-server/data-migration-report.png" alt-text="データ移行レポートを確認する":::

1. [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) を使用し、お使いの SQL Server インスタンスに接続し、データとスキーマを確認して移行を検証します。 

   :::image type="content" source="media/db2-to-sql-server/compare-schema-in-ssms.png" alt-text="SSMS でスキーマを比較する":::

## <a name="post-migration"></a>移行後 

移行段階が正常に完了したら、移行後の一連のタスクを実行し、確実にすべてが可能な限り円滑かつ効率的に機能するようにする必要があります。

### <a name="remediate-applications"></a>アプリケーションを修復する 

データがターゲット環境に移行された後、以前にソースを使用していたすべてのアプリケーションは、ターゲットの使用を開始する必要があります。 これを実現するには、場合によってはアプリケーションの変更が必要になります。

### <a name="perform-tests"></a>テストを実行する

データベース移行のテストア プローチは、次のアクティビティで構成されています。

1. **検証テストを作成する**: データベースの移行をテストするには、SQL クエリを使用する必要があります。 ソース データベースとターゲット データベースの両方に対して実行する検証クエリを作成する必要があります。 検証クエリには、定義したスコープが含まれている必要があります。
1. **テスト環境を設定する**: テスト環境には、ソース データベースとターゲット データベースのコピーが含まれている必要があります。 必ずテスト環境を分離してください。
1. **検証テストを実行する**: ソースとターゲットに対して検証テストを実行してから、結果を分析します。
1. **パフォーマンス テストを実行する**: ソースとターゲットに対してパフォーマンス テストを実行し、結果を分析して比較します。

## <a name="migration-assets"></a>移行資産 

詳細については、次のリソースを参照してください。これらは、実際の移行プロジェクトの取り組みをサポートするために開発されました。

|Asset  |説明  |
|---------|---------|
|[データ ワークロード評価モデルとツール](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| このツールを使用すると、特定のワークロードに対して、推奨される "最適な" ターゲット プラットフォーム、クラウドの準備状況、アプリケーションとデータベースの修復レベルがわかります。 シンプルなワンクリックの計算とレポート生成機能があり、自動化された均一なターゲット プラットフォームの決定プロセスが用意されているので、大規模な不動産評価を加速させることができます。|
|[Db2 zOS データ資産の検出および評価パッケージ](https://github.com/Microsoft/DataMigrationTeam/tree/master/Db2%20zOS%20Data%20Assets%20Discovery%20and%20Assessment%20Package)|データベース上で SQL スクリプトを実行した後、結果をファイル システム上のファイルにエクスポートできます。 スプレッドシートなど、外部ツールで結果をキャプチャできるように、*.csv などの複数のファイル形式がサポートされています。 この方法を使用すると、ワークベンチをインストールしていないチームと結果を簡単に共有することができます。|
|[IBM Db2 LUW インベントリ スクリプトと成果物](https://github.com/Microsoft/DataMigrationTeam/tree/master/IBM%20Db2%20LUW%20Inventory%20Scripts%20and%20Artifacts)|この資産には、IBM Db2 LUW バージョン 11.1 システム テーブルを対象とする、スキーマおよびオブジェクトの種類ごとのオブジェクトの数、各スキーマの "生データ" の概算値、および各スキーマのテーブルのサイズを提供し、結果を CSV 形式で格納する SQL クエリが含まれます。|
|[Azure 上の Db2 LUW pureScale - 設定ガイド](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Db2%20PureScale%20on%20Azure.pdf)|このガイドは、Db2 の実装計画の開始点として役立ちます。 業務要件は違っても、同じ基本パターンが適用されます。 このアーキテクチャ パターンは、Azure 上の OLAP アプリケーションにも使用できます。|

これらのリソースは、Azure Data Group エンジニアリング チームがスポンサーである Data SQL Ninja プログラムの一部として開発されました。 Data SQL Ninja プログラムの中核となるのは、複雑なモダン化のブロックを解除して加速し、データ プラットフォームを Microsoft の Azure Data プラットフォームに移行する機会を獲得することです。 組織が Data SQL Ninja プログラムへの参加に関心があると思われる場合は、アカウント チームに連絡し、申請を提出するよう依頼してください。

## <a name="next-steps"></a>次の手順

移行後は、[移行後の検証と最適化ガイド](../../../relational-databases/post-migration-validation-and-optimization-guide.md)を確認してください。 

さまざまなデータベースとデータの移行シナリオ、および特殊なタスクを支援するために使用できる Microsoft とサードパーティのサービスとツールのマトリックスについては、[データ移行サービスとツール](/azure/dms/dms-tools-matrix)に関するページを参照してください。

その他の移行ガイドについては、[データベースの移行](https://datamigration.microsoft.com/)に関するページを参照してください。 

ビデオ コンテンツについては、以下をご覧ください。
- [移行の過程の概要](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)
