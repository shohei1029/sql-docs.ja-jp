---
title: 'SAP ASE から SQL Server に: 移行ガイド'
description: 'このガイドでは、SQL Server Migration Assistant for SAP ASE (SSMA for SAP ASE) を使用して、SAP ASE データベースを Microsoft SQL Server に移行する方法について説明します。 '
ms.prod: sql
ms.reviewer: ''
ms.technology: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: a549b0e28da092bc1320f621c29307772fc5d69b
ms.sourcegitcommit: bf7577b3448b7cb0e336808f1112c44fa18c6f33
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104611034"
---
# <a name="migration-guide-sap-ase-to-sql-server"></a>移行ガイド: SAP ASE から SQL Server
[!INCLUDE[sqlserver](../../../includes/applies-to-version/sqlserver.md)]

このガイドでは、SQL Server Migration Assistant for SAP ASE を使用して、SAP ASE データベースを SQL Server に移行する方法について説明します。

その他のシナリオについては、[データベース移行ガイド](https://datamigration.microsoft.com/)を参照してください。

## <a name="prerequisites"></a>前提条件 

SAP SE データベースを SQL Server に移行するには、以下が必要です。

- ソース環境がサポートされていることを確認する。 
- [SQL Server Migration Assistant for SAP Adaptive Server Enterprise (以前の SAP Sybase ASE)](https://www.microsoft.com/download/details.aspx?id=54256). 

## <a name="pre-migration"></a>移行前

前提条件を満たすと、環境のトポロジを検出し、移行の可能性を評価する準備は完了です。

### <a name="assess"></a>アクセス

[SQL Server Migration Assistant (SSMA) for SAP Adaptive Server Enterprise (以前の SAP Sybase ASE)](https://www.microsoft.com/download/details.aspx?id=54256) を使用して、データベース オブジェクトとデータを確認し、データベースの移行の評価を行った上で、Sybase データベース オブジェクトを SQL Server に移行した後、データを SQL Server に移行します。 詳細については、「[SQL Server Migration Assistant for Sybase (SybaseToSQL)](../../../ssma/sybase/sql-server-migration-assistant-for-sybase-sybasetosql.md)」を参照してください。

評価を作成するには、次の手順を行います。 

1. **SSMA for Sybase** を開きます。 
1. **[ファイル]** を選択し、 **[新しいプロジェクト]** を選択します。 
1. プロジェクト名とプロジェクトを保存する場所を指定し、ドロップダウンから移行ターゲットとして SQL Server を選択します。 **[OK]** を選択します。
1. **[Sybase への接続]** ダイアログ ボックスで、SAP 接続の詳細の値を入力します。 
1. 移行する SAP データベースを右クリックし、 **[レポートの作成]** を選択します。 これにより、HTML レポートが生成されます。
1. HTML レポートを確認し、変換の統計情報とエラーまたは警告を把握します。 また、Excel でレポートを開き、SAP ASE オブジェクトのインベントリとスキーマ変換の実行に必要な作業を確認することもできます。 レポートの既定の場所は、SSMAProjects 内のレポート フォルダーです。

   (例: `drive:\<username>\Documents\SSMAProjects\MyDB2Migration\report\report_<date>`)。 


### <a name="validate-type-mappings"></a>型マッピングの検証

スキーマ変換を実行する前に、既定のデータ型マッピングを検証するか、要件に基づいて変更します。 これを行うには、 **[ツール]** メニューに移動して **[プロジェクトの設定]** を選択するか、**SAP ASE メタデータ エクスプローラー** でテーブルを選択して各テーブルの型マッピングを変更します。


### <a name="convert-schema"></a>スキーマの変換

スキーマを変換するには、次の手順を行います。

1. (省略可能) 動的またはアドホックのクエリを変換するには、ノードを右クリックし、 **[ステートメントの追加]** を選択します。 
1. 一番上の行のナビゲーション バーで **[SQL Server への接続]** を選択し、SQL Server の詳細を指定します。 既存のデータベースへの接続を選択することも、新しい名前を指定することもできます。後者の場合、データベースはターゲット サーバー上に作成されます。
1. **Sybase メタデータ エクスプローラー** で SAP ASE スキーマを右クリックし、 **[スキーマの変換]** を選択します。 または、一番上の行のナビゲーション バーから **[スキーマの変換]** を選択することもできます。 
1. スキーマの構造を比較および確認して、潜在的な問題を特定します。 

   スキーマの変換後、オフライン スキーマ修復の演習用にこのプロジェクトをローカルに保存できます。 **[ファイル]** メニューから **[プロジェクトの保存]** を選択します。 これにより、スキーマを SQL Server に発行する前に、ソースとターゲットのスキーマをオフラインで評価し、修復を実行する機会が得られます。

詳細については、[スキーマの変換](../../../ssma/sybase/converting-sybase-ase-database-objects-sybasetosql.md)に関する記事を参照してください


## <a name="migrate"></a>移行 

必要な前提条件を満たし、**移行前** 段階に関連するタスクを完了すると、スキーマとデータの移行を実行する準備が整います。

スキーマを発行し、データを移行するには、次の手順に従います。 

1. **SQL Server メタデータ エクスプローラー** でデータベースを右クリックし、 **[データベースとの同期]** を選択します。  この操作により、SAP ASE スキーマが SQL Server インスタンスに発行されます。
1. **SAP ASE メタデータ エクスプローラー** で SAP ASE スキーマを右クリックし、 **[データの移行]** を選択します。  または、一番上の行のナビゲーション バーから **[データの移行]** を選択することもできます。  
1. 移行が完了したら、**データ移行レポート** を表示します。 
1. SQL Server Management Studio (SSMS) を使用して SQL Server インスタンスのデータとスキーマを確認し、移行を検証します。


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
> これらの問題と、それらを軽減するための具体的な手順に関する追加の詳細については、「[移行後の検証および最適化ガイド](/sql/relational-databases/post-migration-validation-and-optimization-guide)」を参照してください。


## <a name="migration-assets"></a>移行資産

この移行シナリオを完了するための追加のサポートについては、次のリソースを参照してください。これらは、実際の移行プロジェクトの取り組みをサポートするために開発されました。

| **タイトルとリンク**                                                                                                        | **説明**                                                                                                                                                                                                                                                                                                                                                                                                     |
| --------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [.NET および SQL Server に再コンパイルされたメインフレーム アプリおよびデータの最適化ガイド](https://aka.ms/dmj-wp-mainframe-optimize) | このガイドでは、.NET から SQL Server に対して可能な限り効率的にポイント検索を実行するための最適化に関するアドバイスを提供します。 メインフレーム データベースから SQL Server への移行を希望するお客様は、特にサードパーティ製ツール (Raincode コンパイラなど) を使用してメインフレーム コード (COBOL、JCL など) を T-SQL および C# .NET に自動的に移行する場合、既存のメインフレーム最適化設計パターンの移行を希望される可能性があります。 |

> [!NOTE]
> これらのリソースは、Azure Data Group エンジニアリング チームがスポンサーである Data Migration Jumpstart Program (DM Jumpstart) の一部として開発されました。 DM Jumpstart の中核となるのは、複雑な最新化のブロックを解除して加速し、データ プラットフォームを Microsoft の Azure Data プラットフォームに移行する機会を獲得することです。 組織が DM Jumpstart への参加に関心があると思われる場合は、アカウント チームに連絡し、申請を提出するよう依頼してください。

## <a name="next-steps"></a>次のステップ

- Azure データベース移行ガイドとその内容の概要については、ビデオ「[データ移行ガイドの使い方](https://azure.microsoft.com/resources/videos/how-to-use-the-azure-database-migration-guide/)」を参照してください。

- さまざまなデータベースとデータの移行シナリオ、および特殊なタスクを支援する Microsoft とサードパーティ製のサービスとツールの表については、[データ移行のためのサービスとツール](https://docs.microsoft.com/azure/dms/dms-tools-matrix)に関する記事を参照してください。

- その他の移行ガイドについては、[データベースの移行](https://datamigration.microsoft.com/)に関するページを参照してください。 

ビデオについては、以下を参照してください。 
- [移行の行程の概要、および評価と移行を実行するために推奨されるツールとサービス](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)。
