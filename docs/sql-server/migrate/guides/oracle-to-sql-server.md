---
title: 'Oracle から SQL Server に: 移行ガイド'
description: 'このガイドでは、SQL Server Migration Assistant for Oracle (SSMA for Oracle) を使用して、Oracle スキーマを Microsoft SQL Server に移行する方法について説明します。 '
ms.prod: sql
ms.reviewer: ''
ms.technology: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: 675086012398d03e3ed93fbe179de62e0a955cb6
ms.sourcegitcommit: 00af0b6448ba58e3685530f40bc622453d3545ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104673669"
---
# <a name="migration-guide-oracle-to-sql-server"></a>移行ガイド: Oracle から SQL Server に
[!INCLUDE[sqlserver](../../../includes/applies-to-version/sqlserver.md)]

このガイドでは、SQL Server Migration Assistant for Oracle を使用して、Oracle データベースを SQL Server に移行する方法について説明します。 

その他のシナリオについては、[データベース移行ガイド](https://datamigration.microsoft.com/)を参照してください。

## <a name="prerequisites"></a>前提条件 

Oracle データベースを SQL Server に移行するには、以下が必要です。

- ソース環境がサポートされていることを確認する。
- [SQL Server](https://www.microsoft.com/evalcenter/evaluate-sql-server-2019?filetype=EXE) をインストールする。
- [SQL Server Migration Assistant (SSMA) for Oracle](https://www.microsoft.com/download/details.aspx?id=54258) をダウンロードする。
- [SSMA for Oracle に必要なアクセス許可](/sql/ssma/oracle/connecting-to-oracle-database-oracletosql)と[プロバイダー](/sql/ssma/oracle/connect-to-oracle-oracletosql)。


## <a name="pre-migration"></a>移行前

クラウドへの移行の準備をする場合には、ソース環境がサポートされていること、および前提条件に対応していることを確認します。 これは、確実に効率よく移行を成功させるのに役立ちます。

プロセスのこの部分では、移行する必要があるデータベースのインベントリを実行し、移行に関する潜在的な問題や障害の有無についてそれらのデータベースを評価し、発見していなかったおそれがあるすべての項目を解決します。 

> [!Important]
> SQL Server Migration Assistant for Oracle では、すべての Oracle 機能の移行がサポートされているわけではありません。 関連する回避策については、[選択した Oracle 機能の移行アプローチ](https://blogs.msdn.microsoft.com/datamigration/2017/05/10/migration-approach-for-oracle-features/)に関する記事を参照してください


### <a name="discover"></a>発見

[MAP Toolkit](https://go.microsoft.com/fwlink/?LinkID=316883) を使用して、既存のデータソースを特定し、ビジネスで使用されている機能の詳細を確認して、移行について理解を深め、移行を計画します。 このプロセスには、ネットワークをスキャンして、組織内のすべての Oracle インスタンスと共に、使用しているバージョンと機能を特定することが含まれます。

MAP Toolkit を使用してインベントリ スキャンを実行するには、次の手順に従います。 

1. [MAP Toolkit](https://go.microsoft.com/fwlink/?LinkID=316883) を開きます。
1. **[Create/Select database]\(データベースの作成/選択\)** を選択します。

   ![データベースの選択](./media/oracle-to-sql-server/select-database.png)

1. **[インベントリ データベースの作成]** を選択し、新しく作成するインベントリ データベースの名前を入力し、簡単な説明を入力して、 **[OK]** を選択します。 

   :::image type="content" source="media/oracle-to-sql-server/create-inventory-database.png" alt-text="インベントリ データベースを作成する":::

1. **[インベントリ データの収集]** を選択して、**インベントリと評価ウィザード** を開きます。 

   :::image type="content" source="media/oracle-to-sql-server/collect-inventory-data.png" alt-text="インベントリ データを収集する":::

1. **インベントリと評価ウィザード** で、 **[Oracle]** 、 **[次へ]** の順に選択します。 

   ![Oracle を選択する](./media/oracle-to-sql-server/choose-oracle.png)

1. ビジネス ニーズおよびご使用の環境に最も適したコンピューター検索オプションを選択し、 **[次へ]** を選択します。 

   ![ビジネス ニーズに最適なコンピューター検索オプションを選択する](./media/oracle-to-sql-server/choose-search-option.png)

1. 探索するシステムの資格情報を入力するか、新しい資格情報を作成し、 **[次へ]** を選択します。

    ![資格情報を入力する](./media/oracle-to-sql-server/choose-credentials.png)

1. 資格情報の順序を設定し、 **[次へ]** を選択します。 

   ![資格情報の順序を設定する](./media/oracle-to-sql-server/set-credential-order.png)  

1. 検出する各コンピューターの資格情報を指定します。 コンピューターまたはマシンごとに固有の資格情報を使用するか、 **[すべてのコンピューターの資格情報]** の一覧を使用することを選択できます。  

   ![検出する各コンピューターの資格情報を指定する](./media/oracle-to-sql-server/specify-credentials-for-each-computer.png)

1. 選択の概要を確認し、 **[完了]** を選択します。

   ![概要の確認](./media/oracle-to-sql-server/review-summary.png)

1. スキャンが完了したら、 **[データ収集]** 概要レポートを表示します。 スキャンには数分かかりますが、時間はデータベースの数によって異なります。 完了したら、 **[閉じる]** を選択します。 

   ![コレクションの概要レポート](./media/oracle-to-sql-server/collection-summary-report.png)

1. **[オプション]** を選択して、Oracle の評価とデータベースの詳細に関するレポートを生成します。 両方のレポートを生成するには、オプションを 1 つずつ選択します。




### <a name="assess"></a>アクセス

データ ソースを特定したら、[SQL Server Migration Assistant (SSMA) for Oracle](https://www.microsoft.com/download/details.aspx?id=54258) を使用して、2 つの間のギャップを理解するために、SQL Server VM に移行する Oracle インスタンスを評価します。 Migration Assistant を使用すると、データベース オブジェクトとデータを確認し、データベースの移行の評価を行った上で、データベース オブジェクトを SQL Server に移行した後、データを SQL Server に移行することができます。

評価を作成するには、次の手順を行います。 

1. [SQL Server Migration Assistant (SSMA) for Oracle](https://www.microsoft.com/download/details.aspx?id=54258) を開きます。 
1. **[ファイル]** を選択し、 **[新しいプロジェクト]** を選択します。 
1. プロジェクト名とプロジェクトを保存する場所を指定し、ドロップダウンから SQL Server 移行ターゲットを選択します。 **[OK]** を選択します。 

   ![新しいプロジェクト](./media/oracle-to-sql-server/new-project.png)

1. **[Oracle への接続]** ダイアログ ボックスで、Oracle 接続の詳細の値を入力します。

   ![Oracle への接続](./media/oracle-to-sql-server/connect-to-oracle.png)

   移行する Oracle スキーマを選択する:

   ![読み込むスキーマを選択する](./media/oracle-to-sql-server/select-schema.png)

1. **Oracle メタデータ エクスプローラー** で、Oracle スキーマを選択し、 **[レポートの作成]** を選択して、変換の統計とエラーまたは警告 (ある場合) を含む HTML レポートを生成します。

   ![レポートの作成](./media/oracle-to-sql-server/create-report.png)

1. HTML レポートで変換の統計、およびエラーと警告を確認します。 これを分析して、変換の問題点と解決策を把握します。

   このレポートには、最初の画面で選択した SSMA プロジェクトのフォルダーからもアクセスできます。 上記の例では、report.xml ファイルを次の場所から見つけます。 

   `drive:\<username>\Documents\SSMAProjects\MyOracleMigration\report\report_2016_11_12T02_47_55\`

    これを Excel で開き、Oracle オブジェクトのインベントリと、スキーマ変換の実行に必要な作業を把握します。

   ![変換レポート](./media/oracle-to-sql-server/conversion-report.png)


### <a name="validate-data-types"></a>データ型を検証する

既定のデータ型マッピングを検証し、必要に応じて要件に基づいて変更します。 これを行うには、次のステップに従います。 

1. メニューから **[ツール]** を選択します。 
1. **[プロジェクトの設定]** を選択します。 
1. **[Type mappings]\(型のマッピング\)** タブを選択します。 

   ![型マッピング](./media/oracle-to-sql-server/type-mappings.png)

1. 各テーブルの型マッピングを変更するには、**Oracle メタデータ エクスプローラー** でテーブルを選択します。 



### <a name="convert-schema"></a>スキーマの変換

スキーマを変換するには、次の手順を行います。 

1. (省略可能) 動的またはアドホックのクエリを変換するには、ノードを右クリックし、 **[ステートメントの追加]** を選択します。
1. 一番上の行のナビゲーション バーから **[SQL Server への接続]** を選択し、SQL Server の接続の詳細を指定します。 既存のデータベースへの接続を選択することも、新しい名前を指定することもできます。後者の場合、データベースはターゲット サーバー上に作成されます。

   ![SQL に接続する](./media/oracle-to-sql-server/connect-to-sql.png)

1. スキーマを右クリックして、 **[スキーマの変換]** を選択します。

   ![スキーマの変換](./media/oracle-to-sql-server/convert-schema.png)

1. スキーマの変換が完了したら、スキーマの構造を確認し、潜在的な問題を特定します。

   変換されたオブジェクトを元のオブジェクトと比較する: 

   ![Schema Compare の変換とオブジェクト コードの確認](./media/oracle-to-sql-server/table-mapping.png)

   変換されたプロシージャを元のプロシージャと比較する: 

   ![変換されたプロシージャの確認](./media/oracle-to-sql-server/procedure-comparison.png)

   オフライン スキーマ修復の演習のために、プロジェクトをローカルに保存することができます。 このためには、 **[ファイル]** メニューから **[プロジェクトの保存]** を選択します。 これにより、スキーマを SQL Server に発行する前に、ソースとターゲットのスキーマをオフラインで評価し、修復を実行する機会が得られます。


## <a name="migrate"></a>Migrate

必要な前提条件を満たし、**移行前** 段階に関連するタスクを完了すると、スキーマとデータの移行を実行する準備が整います。 移行には、スキーマの公開とデータの移行という 2 つの手順が必要です。 


スキーマを発行し、データを移行するには、次の手順に従います。 

1. **SQL Server メタデータ エクスプローラー** でデータベースを右クリックし、 **[データベースとの同期]** を選択します。 この操作により、Oracle スキーマが SQL Server に公開されます。 

   ![データベースと同期する](./media/oracle-to-sql-server/synchronize-database.png)

   データベースとの同期を確認する:

   ![データベースと同期する - マッピングの確認](./media/oracle-to-sql-server/synchronize-database-review.png)

1. **Oracle メタデータ エクスプローラー** で Oracle スキーマを右クリックし、 **[データの移行]** を選択します。 または、一番上の行のナビゲーションから [データの移行] を選択することもできます。

   ![データの移行](./media/oracle-to-sql-server/migrate-data.png)

1. ダイアログ ボックスで Oracle と SQL Server の接続の詳細を指定します。
1. 移行が完了したら、データ移行レポートを表示します。

    ![データ移行レポート](./media/oracle-to-sql-server/data-migration-report.png)

1. [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) を使用して SQL Server に接続し、SQL Server インスタンス上のデータとスキーマを確認します。 

   ![SSMA で検証する](./media/oracle-to-sql-server/validate-in-ssms.png)


SSMA を使用するだけでなく、SQL Server Integration Services (SSIS) を使用してデータを移行することもできます。 詳細については、次を参照してください。 
- ブログ「[SQL Server Migration Assistant: Microsoft 以外のデータ プラットフォームのデータを評価して SQL Server に移行する方法](https://blogs.msdn.microsoft.com/datamigration/2016/11/16/sql-server-migration-assistant-how-to-assess-and-migrate-databases-from-non-microsoft-data-platforms-to-sql-server/)」。
- [SQL Server Integration Services の概要](https://docs.microsoft.com//sql/integration-services/sql-server-integration-services)に関する記事。
- ホワイト ペーパー「[SQL Server Integration Services: Azure の SSIS とハイブリッド データ移動](https://download.microsoft.com/download/D/2/0/D20E1C5F-72EA-4505-9F26-FEF9550EFD44/SSIS%20Hybrid%20and%20Azure.docx)」。



## <a name="post-migration"></a>移行後 

**移行** 段階が正常に完了したら、移行後の一連のタスクを実行し、すべてが可能な限り円滑かつ効率的に機能していることを保証する必要があります。

### <a name="remediate-applications"></a>アプリケーションを修復する

データがターゲット環境に移行された後、以前にソースを使用していたすべてのアプリケーションは、ターゲットの使用を開始する必要があります。 これを実現するには、場合によってはアプリケーションの変更が必要になります。

[Data Access Migration Toolkit](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) は、Java ソース コードを分析し、データ アクセス API の呼び出しとクエリを検出できるようにする Visual Studio Code の拡張機能であり、新しいデータベース バックエンドをサポートするために対処する必要があるものを 1 つのペイン ビューで提供します。 詳細については、[Oracle からの Java アプリケーションの移行](https://techcommunity.microsoft.com/t5/microsoft-data-migration/migrate-your-java-applications-from-oracle-to-sql-server-with/ba-p/368727)に関するブログを参照してください。 

### <a name="perform-tests"></a>テストを実行する

データベース移行のテスト アプローチは、次のアクティビティの実行で構成されています。

1. **検証テストを作成します**。 データベースの移行をテストするには、SQL クエリを使用する必要があります。 ソース データベースとターゲット データベースの両方に対して実行する検証クエリを作成する必要があります。 検証クエリには、定義したスコープが含まれている必要があります。

2. **テスト環境を設定します**。 テスト環境には、ソース データベースとターゲット データベースのコピーが含まれている必要があります。 必ずテスト環境を分離してください。

3. **検証テストを実行します**。 ソースとターゲットに対して検証テストを実行してから、結果を分析します。

4. **パフォーマンス テストを実行します**。 ソースとターゲットに対してパフォーマンス テストを実行し、結果を分析して比較します。


### <a name="optimize"></a>最適化

移行後フェーズは、データの精度の問題を調整するため、完全性を確認するため、およびワークロードのパフォーマンスの問題に対処するために非常に重要です。

> [!Note]
> これらの問題と、それらを軽減するための具体的な手順に関する追加の詳細については、「[移行後の検証および最適化ガイド](https://docs.microsoft.com//sql/relational-databases/post-migration-validation-and-optimization-guide)」を参照してください。


## <a name="migration-assets"></a>移行資産 

この移行シナリオを完了するための追加のサポートについては、次のリソースを参照してください。これらは、実際の移行プロジェクトの取り組みをサポートするために開発されました。


| **タイトルとリンク**                                                                                                                                          | **説明**                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [データ ワークロード評価モデルとツール](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | このツールを使用すると、特定のワークロードに対して、推奨される "最適な" ターゲット プラットフォーム、クラウドの準備状況、アプリケーションとデータベースの修復レベルがわかります。 シンプルなワンクリックの計算とレポート生成機能があり、自動化された均一なターゲット プラットフォームの決定プロセスが用意されているので、大規模な資産評価の促進に非常に役立ちます。                                                          |
| [Oracle インベントリ スクリプト成果物](https://github.com/Microsoft/DataMigrationTeam/tree/master/Oracle%20Inventory%20Script%20Artifacts)                 | この資産には、Oracle システム テーブルにヒットし、スキーマの種類、オブジェクトの種類、および状態別にオブジェクトの数を提供する PL/SQL クエリが含まれています。 また、各スキーマの "生データ" の概算値と、各スキーマ内のテーブルのサイズ設定も提供します。結果は CSV 形式で格納されます。                                                                                                               |
| [SSMA Oracle 評価コレクションと統合の自動化](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Automate%20SSMA%20Oracle%20Assessment%20Collection%20%26%20Consolidation)                                             | このリソースのセットは、エントリとして .csv ファイル (プロジェクト フォルダー内の sources.csv) を使用して、コンソール モードで SSMA 評価を実行するために必要な xml ファイルを生成します。 source.csv は、既存の Oracle インスタンスのインベントリに基づいて、顧客によって提供されます。 出力ファイルは、AssessmentReportGeneration_source_1.xml、ServersConnectionFile.xml、および VariableValueFile.xml です。|
| [SSMA for Oracle の一般的なエラーとその修正方法](https://aka.ms/dmj-wp-ssma-oracle-errors)                                                           | Oracle では、WHERE 句に非スカラー条件を割り当てることができます。 しかし、SQL Server ではこの種類の条件がサポートされていません。 その結果、SQL Server Migration Assistant (SSMA) for Oracle では、WHERE 句に非スカラー条件を含むクエリは変換されず、エラー O2SS0001 が生成されます。 このホワイト ペーパーでは、この問題とその解決方法について詳しく説明しています。          |
| [Oracle から SQL Server への移行に関するハンドブック](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20SQL%20Server%20Migration%20Handbook.pdf)                | このドキュメントは、Oracle スキーマを最新バージョンの SQL Server ベースに移行する場合に関連するタスクに焦点を当てています。 移行によって機能の変更が必要な場合は、そのデータベースを使用するアプリケーションに対する各変更によって生じる可能性のある影響について慎重に検討する必要があります。                                                     |

これらのリソースは、Azure Data Group エンジニアリング チームがスポンサーである Data SQL Ninja プログラムの一部として開発されました。 Data SQL Ninja プログラムの中核となるのは、複雑なモダン化のブロックを解除して加速し、データ プラットフォームを Microsoft の Azure Data プラットフォームに移行する機会を獲得することです。 組織が Data SQL Ninja プログラムへの参加に関心があると思われる場合は、アカウント チームに連絡し、申請を提出するよう依頼してください。

## <a name="next-steps"></a>次の手順

移行後は、[移行後の検証と最適化ガイド](../../../relational-databases/post-migration-validation-and-optimization-guide.md)を確認してください。 

さまざまなデータベースとデータの移行シナリオ、および特殊なタスクを支援するために使用できる Microsoft とサードパーティのサービスとツールのマトリックスについては、[データ移行サービスとツール](/azure/dms/dms-tools-matrix)に関するページを参照してください。

その他の移行ガイドについては、[データベースの移行](https://datamigration.microsoft.com/)に関するページを参照してください。 

ビデオ コンテンツについては、以下をご覧ください。
- [移行の過程の概要](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)
