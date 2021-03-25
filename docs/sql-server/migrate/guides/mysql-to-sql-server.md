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
ms.openlocfilehash: 454466e2f387f7bc11d80660eb0534f67aecb7a8
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104751582"
---
# <a name="migration-guide-mysql-to-sql-server"></a>移行ガイド:MySQL から SQL Server へ
[!INCLUDE[sqlserver](../../../includes/applies-to-version/sqlserver.md)]

このガイドでは、MySQL データベースを SQL Server に移行する方法について説明します。 

その他の移行ガイドについては、[データベースの移行](https://datamigration.microsoft.com/)に関するページを参照してください。 

## <a name="prerequisites"></a>前提条件 

MySQL データベースを SQL Server に移行するには、以下が必要です。

- 評価と変換を実行できるように、ソースの MySQL データベースに接続する。
- [SQL Server Migration Assistant for MySQL](https://aka.ms/ssmaformysql)。

## <a name="pre-migration"></a>移行前 

前提条件を満たしたら、ソースの MySQL 環境を検出し、移行の実現可能性を評価する準備が完了します。

### <a name="assess"></a>アクセス 

[SSMA for MySQL](https://aka.ms/ssmaformysql) を使用して評価を作成するには、次の手順を行います。 

1. SQL Server Migration Assistant for MySQL を開きます。 
1. **[ファイル]** を選択し、 **[新しいプロジェクト]** を選択します。 プロジェクト名、プロジェクトを保存する場所、移行ターゲットを指定します。 **[Migrate to]\(移行先\)** オプションで **[SQL Server]** を選択します。 

   ![[新しいプロジェクト]](./media/mysql-to-sql-server/new-project.png)

1. **[Connect to MySQL]\(MySQL への接続\)** ダイアログ ボックスで接続の詳細を指定し、MySQL サーバーに接続します。 

   ![mysql への接続](./media/mysql-to-sql-server/connect-to-mysql.png)

1. **MySQL メタデータ エクスプローラー** で MySQL スキーマを右クリックし、 **[レポートの作成]** を選択します。 または、一番上の行のナビゲーション バーから **[レポートの作成]** を選択することもできます。 

   ![レポートの作成](./media/mysql-to-sql-server/create-report.png)

1. 変換の統計情報、エラー、警告を含む HTML レポートを確認します。 このレポートを分析して、変換の問題とその解決策について把握します。 

   このレポートには、最初の画面で選択した SSMA プロジェクトのフォルダーからもアクセスできます。 上記の例では、report.xml ファイルを次の場所から見つけます。

   `drive:\Users\<username>\Documents\SSMAProjects\MySQLMigration\report\report_2016_11_12T02_47_55\`
 
   これを Excel で開き、MySQL オブジェクトのインベントリとスキーマ変換の実行に必要な作業を把握します。

   ![変換レポート](./media/mysql-to-sql-server/conversion-report.png)

### <a name="validate-type-mappings"></a>型マッピングの検証

既定のデータ型マッピングを検証し、必要に応じて要件に基づいて変更します。 これを行うには、次のステップに従います。 

1. メニューから **[ツール]** を選択します。 
1. **[プロジェクトの設定]** を選択します。 
1. **[Type mappings]\(型のマッピング\)** タブを選択します。 

   ![型マッピング](./media/mysql-to-sql-server/type-mappings.png)

1. **MySQL メタデータ エクスプローラー** でテーブルを選択することにより、各テーブルの型マッピングを変更できます。 


SSMA の変換設定の詳細については、[プロジェクトの設定](../../../ssma/mysql/project-settings-conversion-mysqltosql.md)に関するページを参照してください。

### <a name="convert-schema"></a>スキーマの変換

データベース オブジェクトを変換すると、MySQL からオブジェクトの定義が取得され、類似の SQL Server オブジェクトに変換された後、この情報が SSMA メタデータに読み込まれます。 情報は、SQL Server のインスタンスには読み込まれません。 その後、SQL Server メタデータ エクスプローラーを使用して、オブジェクトとそのプロパティを表示できます。

変換中に SSMA によって、出力メッセージは出力ペイン、エラー メッセージはエラー一覧ペインに出力されます。 出力とエラー情報を使用して、目的の変換結果を取得するために MySQL データベースまたは変換プロセスを変更する必要があるかどうかを判断します。

スキーマを変換するには、次の手順を行います。

1. (省略可能) 動的またはアドホックのクエリを変換するには、ノードを右クリックし、 **[ステートメントの追加]** を選択します。 
1. 一番上の行のナビゲーション バーで **[SQL Server への接続]** を選択し、SQL Server の詳細を指定します。 既存のデータベースへの接続を選択することも、新しい名前を指定することもできます。後者の場合、データベースはターゲット サーバー上に作成されます。

   ![SQL に接続する](./media/mysql-to-sql-server/connect-to-sql-server.png)

1. **MySQL メタデータ エクスプローラー** で MySQL スキーマを右クリックし、 **[スキーマの変換]** を選択します。 または、一番上の行のナビゲーション バーから **[スキーマの変換]** を選択することもできます。 

   ![スキーマの変換](./media/mysql-to-sql-server/convert-schema.png)

1. スキーマの構造を比較および確認して、潜在的な問題を特定します。 

   変換されたオブジェクトを元のオブジェクトと比較する: 

   ![オブジェクトの比較と確認](./media/mysql-to-sql-server/table-comparison.png)

   変換されたビューと元のビューを比較する: 

   ![変換されたコードの比較と確認](./media/mysql-to-sql-server/procedure-comparison.png)
   
   
   **[ファイル]** メニューから **[プロジェクトの保存]** を選択します。 これにより、スキーマを SQL Server に発行する前に、ソースとターゲットのスキーマをオフラインで評価し、修復を実行する機会が得られます。

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

スキーマを発行し、データを移行するには、次の手順に従います。 

1. **SQL Server メタデータ エクスプローラー** でデータベースを右クリックし、 **[データベースとの同期]** を選択します。  この操作により、MySQL スキーマが SQL Server インスタンスに発行されます。

   ![データベースと同期する](./media/mysql-to-sql-server/synchronize-database.png)

   データベースとの同期を確認する:

   ![データベースと同期する - 確認](./media/mysql-to-sql-server/synchronize-database-review.png)

1. **MySQL メタデータ エクスプローラー** で MySQL スキーマを右クリックし、 **[データの移行]** を選択します。  または、一番上の行のナビゲーション バーから **[データの移行]** を選択することもできます。 

   ![データの移行](./media/mysql-to-sql-server/migrate-data.png)

1. 移行が完了したら、**データ移行レポート** を表示します。 

   ![データ移行レポート](./media/mysql-to-sql-server/migration-report.png)

1. SQL Server Management Studio (SSMS) を使用して SQL Server インスタンスのデータとスキーマを確認し、移行を検証します。

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

> [!NOTE] 
> これらの問題と、それらを軽減するための具体的な手順に関する追加の詳細については、「[移行後の検証および最適化ガイド](https://docs.microsoft.com/sql/relational-databases/post-migration-validation-and-optimization-guide)」を参照してください。

## <a name="migration-assets"></a>移行資産 

この移行シナリオを完了するための追加のサポートについては、次のリソースを参照してください。これらは、実際の移行プロジェクトの取り組みをサポートするために開発されました。

| タイトルとリンク                    | 説明            |
| ----------------------------- | ---------------------- |
| [データ ワークロード評価モデルとツール](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | このツールを使用すると、特定のワークロードに対して、推奨される "最適な" ターゲット プラットフォーム、クラウドの準備状況、アプリケーションとデータベースの修復レベルがわかります。 シンプルなワンクリックの計算とレポート生成機能があり、自動化された均一なターゲット プラットフォームの決定プロセスが用意されているので、大規模な資産評価の促進に非常に役立ちます。                |

これらのリソースは、Azure Data Group エンジニアリング チームがスポンサーである Data SQL Ninja プログラムの一部として開発されました。 Data SQL Ninja プログラムの中核となるのは、複雑なモダン化のブロックを解除して加速し、データ プラットフォームを Microsoft の Azure Data プラットフォームに移行する機会を獲得することです。 組織が Data SQL Ninja プログラムへの参加に関心があると思われる場合は、アカウント チームに連絡し、申請を提出するよう依頼してください。

## <a name="next-steps"></a>次のステップ

- MySQL データベースを SQL Server に移行する方法の詳細については、[MySQL の SSMA ドキュメント](../../../ssma/mysql/sql-server-migration-assistant-for-mysql-mysqltosql.md)を参照してください。

- さまざまなデータベースとデータの移行シナリオ、および特殊なタスクを支援する Microsoft とサードパーティ製のサービスとツールの表については、[データ移行のためのサービスとツール](https://docs.microsoft.com/azure/dms/dms-tools-matrix)に関する記事を参照してください。

- その他の移行ガイドについては、[データベースの移行](https://datamigration.microsoft.com/)に関するページを参照してください。 

