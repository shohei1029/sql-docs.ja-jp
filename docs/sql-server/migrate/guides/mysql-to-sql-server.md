---
title: 'MySQL から SQL Server に: 移行ガイド'
description: 'このガイドでは、SQL Server Migration Assistant for MySQL (SSMA for MySQL) を使用して、MySQL データベースを Microsoft SQL Server に移行する方法について説明します。 '
ms.prod: sql
ms.reviewer: ''
ms.technology: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: 2befdaec3602634faaa2017ae2ca225a938cfab3
ms.sourcegitcommit: 2f971c85d87623c0aed1612406130d840e7bdb2e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "105744503"
---
# <a name="migration-guide-mysql-to-sql-server"></a>移行ガイド:MySQL から SQL Server へ
[!INCLUDE[sqlserver](../../../includes/applies-to-version/sqlserver.md)]

このガイドでは、MySQL データベースを SQL Server に移行する方法について説明します。 

その他の移行ガイドについては、[データベースの移行](https://docs.microsoft.com/data-migration)に関するページを参照してください。 

## <a name="prerequisites"></a>前提条件 

MySQL データベースを SQL Server に移行するには、以下が必要です。

- ソース環境がサポートされていることを確認する。 現時点では、MySQL 5.6 および 5.7 がサポートされています。 
- [SQL Server Migration Assistant for MySQL](https://www.microsoft.com/download/details.aspx?id=54257)。
- ソースとターゲットの両方への接続と、十分なアクセス許可。 

## <a name="pre-migration"></a>移行前 

前提条件を満たしたら、ソースの MySQL 環境を検出し、移行の実現可能性を評価する準備が完了します。

### <a name="assess"></a>アクセス 

SQL Server Migration Assistant (SSMA) for MySQL を使用すると、データベース オブジェクトとデータを確認し、データベースの移行を評価できます。

評価を作成するには、次の手順を行います。 

1. SQL Server Migration Assistant for MySQL を開きます。 
1. **[ファイル]** を選択し、 **[新しいプロジェクト]** を選択します。 
1. プロジェクト名、プロジェクトを保存する場所、移行ターゲットを指定します。 **[Migrate to]** \(移行先\) オプションで **[SQL Server]** を選択します。

   ![[新しいプロジェクト]](./media/mysql-to-sql-server/new-project.png)

1. **[Connect to MySQL]** \(MySQL への接続\) ダイアログ ボックスで接続の詳細を指定し、お使いの MySQL サーバーに接続します。

   ![mysql への接続](./media/mysql-to-sql-server/connect-to-mysql.png)

1. 次のように、移行する MySQL データベースを選択します。

   ![移行する MySQL データベースを選択する](./media/mysql-to-sql-server/select-database.png)

1. **MySQL メタデータ エクスプローラー** で MySQL データベースを右クリックし、 **[レポートの作成]** を選択します。 または、一番上の行のナビゲーション バーから **[レポートの作成]** を選択することもできます。

   ![レポートの作成](./media/mysql-to-sql-server/create-report.png)

1. HTML レポートを確認し、変換の統計情報とエラーまたは警告を把握します。 また、Excel でレポートを開き、MySQL オブジェクトのインベントリとスキーマ変換の実行に必要な作業量を確認することもできます。 レポートの既定の場所は、SSMAProjects 内のレポート フォルダーです。 

   例: `drive:\Users\<username>\Documents\SSMAProjects\MySQLMigration\report\report_2016_11_12T02_47_55\` 
   
   ![変換レポート](./media/mysql-to-sql-server/conversion-report.png)

### <a name="validate-type-mappings"></a>型マッピングの検証

既定のデータ型マッピングを検証し、必要に応じて要件に基づいて変更します。 これを行うには、次のステップに従います。 

1. メニューから **[ツール]** を選択します。 
1. **[プロジェクトの設定]** を選択します。 
1. **[Type mappings]** \(型マッピング\) タブを選択します。

   ![型マッピング](./media/mysql-to-sql-server/type-mappings.png)

1. **MySQL メタデータ エクスプローラー** でテーブルを選択することにより、各テーブルの型マッピングを変更できます。 


SSMA の変換設定の詳細については、[プロジェクトの設定](../../../ssma/mysql/project-settings-conversion-mysqltosql.md)に関するページを参照してください。

### <a name="convert-schema"></a>スキーマの変換

データベース オブジェクトを変換すると、MySQL からオブジェクトの定義が取得され、類似の SQL Server オブジェクトに変換された後、この情報が SSMA メタデータに読み込まれます。 情報は、SQL Server のインスタンスには読み込まれません。 その後、SQL Server メタデータ エクスプローラーを使用して、オブジェクトとそのプロパティを表示できます。

変換中に SSMA によって、出力メッセージは出力ペイン、エラー メッセージはエラー一覧ペインに出力されます。 出力とエラー情報を使用して、目的の変換結果を取得するために MySQL データベースまたは変換プロセスを変更する必要があるかどうかを判断します。

スキーマを変換するには、次の手順を行います。

1. (省略可能) 動的またはアドホックのクエリを変換するには、ノードを右クリックし、 **[ステートメントの追加]** を選択します。 
1. 一番上の行のナビゲーション バーから **[SQL Server への接続]** を選択します。 
     1. お使いの SQL Server インスタンス用に接続詳細を入力します。 
     1. ドロップダウンから自分のターゲット データベースを選択するか、新しい名前を指定します。これにより、データベースはターゲット サーバー上に作成されます。 
     1. 認証の詳細を指定します。 
     1. **[接続]** を選択します。

   ![SQL に接続する](./media/mysql-to-sql-server/connect-to-sql-server.png)

1. **MySQL メタデータ エクスプローラー** で MySQL データベースを右クリックし、 **[スキーマの変換]** を選択します。 または、一番上の行のナビゲーション バーから **[スキーマの変換]** を選択することもできます。

   ![スキーマの変換](./media/mysql-to-sql-server/convert-schema.png)

1. 変換が完了したら、次のように、変換されたオブジェクトと元のオブジェクトを比較および確認して、潜在的な問題を特定し、推奨事項に基づいてそれらを対処します。

   ![オブジェクトの比較と確認](./media/mysql-to-sql-server/table-comparison.png)

   次のように、変換された Transact-SQL テキストを元のコードと比較し、推奨事項を確認します。

   ![変換されたコードの比較と確認](./media/mysql-to-sql-server/procedure-comparison.png)
   
1. [出力] ペインの **[結果の確認]** を選択し、 **[エラー一覧]** ペインでエラーを確認します。 
1. オフライン スキーマ修復の演習のために、プロジェクトをローカルに保存します。 **[ファイル]** メニューから **[プロジェクトの保存]** を選択します。 これにより、スキーマを SQL Server に発行する前に、ソースとターゲットのスキーマをオフラインで評価し、修復を実行する機会が得られます。

詳細については、「[MySQL データベースの変換](../../../ssma/mysql/converting-mysql-databases-mysqltosql.md)」を参照してください。

## <a name="migration"></a>移行 

必要な前提条件を満たし、**移行前** 段階に関連するタスクを完了すると、スキーマとデータの移行を実行する準備が整います。

データを移行するには、次の 2 つのケースが発生します。

- **クライアント側データの移行:**
     -   **クライアント側のデータ移行** を実行するには、 **[プロジェクトの設定]** ダイアログ ボックスで **[Client Side Data Migration Engine]\(クライアント側のデータ移行エンジン\)** オプションを選択します。

    > [!NOTE]
    > ターゲット データベースとして SQL Express エディションを使用する場合、クライアント側のデータ移行のみが許可され、サーバー側のデータ移行はサポートされません。

- **サーバー側データの移行:**
    -   サーバー側でデータ移行を実行する前に、次のことを確保します。
        - SSMA for MySQL Extension Pack が、SQL Server のインスタンスにインストールされている。
        - SQL Server エージェント サービスが、SQL Server のインスタンスで実行されている。 
    -   **サーバー側のデータ移行** を実行するには、 **[プロジェクトの設定]** ダイアログ ボックスで **[Server Side Data Migration Engine]\(サーバー側のデータ移行エンジン\)** オプションを選択します。

> [!IMPORTANT]  
> 使用されているエンジンがサーバー側のデータ移行エンジンである場合、データを移行する前に、SSMA を実行しているコンピューターに SSMA for MySQL Extension Pack と MySQL プロバイダーをインストールする必要があります。 SQL Server エージェント サービスも実行されている必要があります。 拡張機能パックをインストールする方法の詳細については、「[SQL Server での SSMA コンポーネントのインストール (MySQL から SQL)](../../../ssma/mysql/installing-ssma-components-on-sql-server-mysqltosql.md)」を参照してください  

自分のスキーマを発行し、データを移行するには、次の手順を行います。 

1. スキーマを発行する: **SQL Server メタデータ エクスプローラー** でデータベースを右クリックし、 **[データベースとの同期]** を選択します。  この操作により、MySQL データベースが SQL Server インスタンスに発行されます。

   ![データベースと同期する](./media/mysql-to-sql-server/synchronize-database.png)

   自分のソース プロジェクトと自分のターゲット間のマッピングを確認します。

   ![データベースと同期する - 確認](./media/mysql-to-sql-server/synchronize-database-review.png)

1. データを移行する: **MySQL メタデータ エクスプローラー** で、移行するデータベースまたはオブジェクトを右クリックし、 **[データの移行]** を選択します。 または、一番上の行のナビゲーション バーから **[データの移行]** を選択することもできます。 データベース全体のデータを移行するには、データベース名の横にあるチェック ボックスをオンにします。 個々のテーブルからデータを移行するには、データベースを展開し、[テーブル] を展開して、テーブルの横にあるチェック ボックスをオンにします。 個々のテーブルのデータを除外するには、次のチェック ボックスをオフにします。

   ![データの移行](./media/mysql-to-sql-server/migrate-data.png)

1. 移行が完了したら、**データ移行** レポートを表示します。 

   ![データ移行レポート](./media/mysql-to-sql-server/migration-report.png)

1. [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) を使用し、お使いの SQL Server インスタンスに接続し、データとスキーマを確認して移行を検証します。 

   ![SSMA で検証する](./media/mysql-to-sql-server/validate-in-ssms.png)



## <a name="post-migration"></a>移行後 

**移行** 段階が正常に完了したら、移行後の一連のタスクを実行し、すべてが可能な限り円滑かつ効率的に機能していることを保証する必要があります。

### <a name="remediate-applications"></a>アプリケーションを修復する

データがターゲット環境に移行された後、以前にソースを使用していたすべてのアプリケーションは、ターゲットの使用を開始する必要があります。 これを実現するには、場合によってはアプリケーションの変更が必要になります。

### <a name="perform-tests"></a>テストを実行する

データベース移行のテスト アプローチは、次のアクティビティの実行で構成されています。

1. **検証テストを作成します**。 データベースの移行をテストするには、SQL クエリを使用する必要があります。 ソース データベースとターゲット データベースの両方に対して実行する検証クエリを作成する必要があります。 検証クエリには、定義したスコープが含まれている必要があります。

2. **テスト環境を設定します**。 テスト環境には、ソース データベースとターゲット データベースのコピーが含まれている必要があります。 必ずテスト環境を分離してください。

3. **検証テストを実行します**。 ソースとターゲットに対して検証テストを実行してから、結果を分析します。

4. **パフォーマンス テストを実行します**。 ソースとターゲットに対してパフォーマンス テストを実行し、結果を分析して比較します。


### <a name="optimize"></a>最適化

移行後フェーズは、データの精度の問題を調整するため、完全性を確認するため、およびワークロードのパフォーマンスの問題に対処するために非常に重要です。

> [!Note]
> これらの問題と、それらを軽減するための具体的な手順に関する追加の詳細については、「[移行後の検証および最適化ガイド](../../../relational-databases/post-migration-validation-and-optimization-guide.md)」を参照してください。

## <a name="migration-assets"></a>移行資産 

この移行シナリオを完了するための追加のサポートについては、次のリソースを参照してください。これらは、実際の移行プロジェクトの取り組みをサポートするために開発されました。

| タイトルとリンク                    | 説明            |
| ----------------------------- | ---------------------- |
| [データ ワークロード評価モデルとツール](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | このツールを使用すると、特定のワークロードに対して、推奨される "最適な" ターゲット プラットフォーム、クラウドの準備状況、アプリケーションとデータベースの修復レベルがわかります。 シンプルなワンクリックの計算とレポート生成機能があり、自動化された均一なターゲット プラットフォームの決定プロセスが用意されているので、大規模な資産評価の促進に非常に役立ちます。                |

データ SQL エンジニアリング チームが、これらのリソースを開発しました。 このチームの主要な作業は、Microsoft の Azure データ プラットフォームへのデータ プラットフォーム移行プロジェクトの複雑な近代化を容易にし、迅速に進めることです。

## <a name="next-steps"></a>次のステップ

- MySQL データベースを SQL Server に移行する方法の詳細については、[MySQL の SSMA ドキュメント](../../../ssma/mysql/sql-server-migration-assistant-for-mysql-mysqltosql.md)を参照してください。

- さまざまなデータベースとデータの移行シナリオ、および特殊なタスクを支援する Microsoft とサードパーティ製のサービスとツールの表については、[データ移行のためのサービスとツール](https://docs.microsoft.com/azure/dms/dms-tools-matrix)に関する記事を参照してください。

- その他の移行ガイドについては、[データベースの移行](https://datamigration.microsoft.com/)に関するページを参照してください。 

