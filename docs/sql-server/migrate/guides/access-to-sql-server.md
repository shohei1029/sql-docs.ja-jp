---
title: 'SQL Server へのアクセス: 移行ガイド'
description: 'このガイドでは、SQL Server Migration Assistant for Access (SSMA for Access) を使用して、Microsoft Access データベースを Microsoft SQL Server に移行する方法について説明します。 '
ms.prod: sql
ms.reviewer: ''
ms.technology: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: 2dc96d9f6afe6ca221577ac04ebc5f82f6ea5ade
ms.sourcegitcommit: 2f971c85d87623c0aed1612406130d840e7bdb2e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "105744510"
---
# <a name="migration-guide-access-to-sql-server"></a>移行ガイド: SQL Server へのアクセス
[!INCLUDE[sqlserver](../../../includes/applies-to-version/sqlserver.md)]

この移行ガイドでは、SQL Server Migration Assistant for Access (SSMA for Access) を使用して、Microsoft Access データベースを SQL Server に移行する方法について説明します。 

その他の移行ガイドについては、[データベースの移行](https://datamigration.microsoft.com/)に関するページを参照してください。 

## <a name="prerequisites"></a>前提条件 

Access データベースを SQL Server に移行するには、以下が必要です。

- ソース環境がサポートされていることを確認する。 
- [SQL Server Migration Assistant for Access](https://www.microsoft.com/download/details.aspx?id=54255). 
- ソースとターゲットの両方への接続と、十分なアクセス許可。 


## <a name="pre-migration"></a>移行前 


前提条件を満たすと、環境のトポロジを検出し、移行の可能性を評価する準備は完了です。


### <a name="assess"></a>アクセス

SQL Server Migration Assistant (SSMA) for Access を使用すると、データベース オブジェクトとデータを確認し、データベースの移行を評価できます。 このツールの詳細については、[SQL Server Migration Assistant for Access](/sql/ssma/access/sql-server-migration-assistant-for-access-accesstosql) に関するページを参照してください。 

評価を作成するには、次の手順を行います。

1. [SQL Server Migration Assistant for Access](https://www.microsoft.com/download/details.aspx?id=54255) を開きます。 
1. **[ファイル]** を選択し、 **[新しいプロジェクト]** を選択します。 
1. プロジェクト名とプロジェクトを保存する場所を指定し、ドロップダウンから SQL Server 移行ターゲットを選択します。 **[OK]** を選択します。

   ![[新しいプロジェクト]](./media/access-to-sql-server/new-project.png)

1. **[データベースの追加]** を選択し、自分のプロジェクトに追加するデータベースを選択します。

   ![[データベースの追加]](./media/access-to-sql-server/add-databases.png)

1. **[Access Metadata Explorer]** で、評価するデータベースを右クリックして、 **[レポートの作成]** を選択します。 また、スキーマの選択後、ナビゲーション バーの **[レポートの作成]** を選択することもできます。

   ![レポートの作成](./media/access-to-sql-server/create-report.png)

1. HTML レポートを確認し、変換の統計情報とエラーまたは警告を把握します。 また、Excel でレポートを開き、Access オブジェクトのインベントリとスキーマ変換の実行に必要な作業量を確認することもできます。 レポートの既定の場所は、SSMAProjects 内のレポート フォルダーです。

   例: `drive:\<username>\Documents\SSMAProjects\MyAccessMigration\report\report_2020_11_12T02_47_55\`

   ![サンプル レポート](./media/access-to-sql-server/sample-report.png)

### <a name="validate-data-types"></a>データ型を検証する

既定のデータ型マッピングを検証し、必要に応じて要件に基づいて変更します。 これを行うには、次のステップに従います。 

1. メニューから **[ツール]** を選択します。 
1. **[プロジェクトの設定]** を選択します。 
1. **[Type mappings]** \(型マッピング\) タブを選択します。

   ![型マッピング](./media/access-to-sql-server/type-mappings.png)

1. 各テーブルの型マッピングを変更するには、**Oracle メタデータ エクスプローラー** でテーブルを選択します。 


### <a name="convert"></a>Convert 

データベース オブジェクトを変換するには、次の手順に従います。 

1. **[SQL Server への接続]** を選択し、接続の詳細を指定します。

   ![SQL Server への接続](./media/access-to-sql-server/connect-to-sql-server.png)

1. **[Access Metadata Explorer]** 内でデータベースを右クリックし、 **[スキーマの変換]** を選択します。 あるいは、お使いのデータベースを選択した後、上部のナビゲーション バーから **[スキーマの変換]** を選択できます。

   ![スキーマの変換](./media/access-to-sql-server/convert-schema.png)

1. 変換が完了したら、次のように、変換されたオブジェクトと元のオブジェクトを比較および確認して、潜在的な問題を特定し、推奨事項に基づいてそれらを対処します。

   ![変換されたクエリの比較 ](./media/access-to-sql-server/query-comparison.png)

   次のように、変換された Transact-SQL テキストを元のコードと比較し、推奨事項を確認します。

   ![変換されたオブジェクトの確認](./media/access-to-sql-server/table-comparison.png)

1. (省略可能) 個々のオブジェクトを変換するには、オブジェクトを右クリックし、 **[スキーマの変換]** を選択します。 変換されたオブジェクトは、 **[Access Metadata Explorer]** 内に太字で表示されます。 

   ![メタデータ エクスプローラーの太字のオブジェクトが変換されています](./media/access-to-sql-server/converted-items-bold.png)
 
1. [出力] ペインの **[結果の確認]** を選択し、 **[エラー一覧]** ペインでエラーを確認します。 
1. オフライン スキーマ修復の演習のために、プロジェクトをローカルに保存します。 **[ファイル]** メニューから **[プロジェクトの保存]** を選択します。 これにより、スキーマを SQL Server に発行する前に、ソースとターゲットのスキーマをオフラインで評価し、修復を実行する機会が得られます。


## <a name="migrate"></a>Migrate

データベースの評価と不整合への対処が完了したら、次の手順は移行プロセスの実行です。 データの移行は、トランザクション内でデータの行を SQL Server に移動する一括読み込み操作です。 各トランザクションで SQL Server に読み込まれる行数は、プロジェクトの設定で構成されます。

SSMA for Access を使用してお使いのスキーマを発行し、データを移行するには、次の手順を実行します。 

1. まだ行っていない場合は、 **[Azure SQL Database への接続]** を選択し、接続の詳細を指定します。 

1. スキーマを発行する: **SQL Server メタデータ エクスプローラー** からデータベースを右クリックし、 **[データベースとの同期]** を選択します。 この操作により、MySQL スキーマが SQL Server に公開されます。

   ![データベースと同期する](./media/access-to-sql-server/synchronize-with-database.png)

   自分のソース プロジェクトと自分のターゲット間のマッピングを確認します。

   ![データベースとの同期を確認する](./media/access-to-sql-server/synchronize-with-database-review.png)

1. データを移行する: **Access メタデータ エクスプローラー** で、移行するデータベースまたはオブジェクトを右クリックし、 **[データの移行]** を選択します。 または、一番上の行のナビゲーション バーから **[データの移行]** を選択することもできます。 データベース全体のデータを移行するには、データベース名の横にあるチェック ボックスをオンにします。 個々のテーブルからデータを移行するには、データベースを展開し、[テーブル] を展開して、テーブルの横にあるチェック ボックスをオンにします。 個々のテーブルのデータを除外するには、次のチェック ボックスをオフにします。

   ![データの移行](./media/access-to-sql-server/migrate-data.png)

1. 移行が完了したら、**データ移行** レポートを表示します。  

   ![データ移行の確認](./media/access-to-sql-server/migrate-data-review.png)

1. [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) を使用し、お使いの SQL Server インスタンスに接続し、データとスキーマを確認して移行を検証します。 

   ![SSMA で検証する](./media/access-to-sql-server/validate-in-ssms.png)



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

| **タイトルとリンク** | **説明** |
| -------------- | --------------- |
| [データ ワークロード評価モデルとツール](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | このツールを使用すると、特定のワークロードに対して、推奨される "最適な" ターゲット プラットフォーム、クラウドの準備状況、アプリケーションとデータベースの修復レベルがわかります。 シンプルなワンクリックの計算とレポート生成機能があり、自動化された均一なターゲット プラットフォームの決定プロセスが用意されているので、大規模な資産評価の促進に非常に役立ちます。 |

データ SQL エンジニアリング チームが、これらのリソースを開発しました。 このチームの主要な作業は、Microsoft の Azure データ プラットフォームへのデータ プラットフォーム移行プロジェクトの複雑な近代化を容易にし、迅速に進めることです。

## <a name="next-steps"></a>次の手順

移行後は、[移行後の検証と最適化ガイド](/sql/relational-databases/post-migration-validation-and-optimization-guide)を確認してください。 

さまざまなデータベースとデータの移行シナリオ、および特殊なタスクを支援するために使用できる Microsoft とサードパーティのサービスとツールのマトリックスについては、[データ移行サービスとツール](/azure/dms/dms-tools-matrix)に関するページを参照してください。

その他の移行ガイドについては、[データベースの移行](https://datamigration.microsoft.com/)に関するページを参照してください。 

ビデオ コンテンツについては、以下をご覧ください。
- [移行の過程の概要](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)
