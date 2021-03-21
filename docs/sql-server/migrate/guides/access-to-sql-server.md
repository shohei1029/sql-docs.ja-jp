---
title: '移行ガイド: SQL Server へのアクセス'
description: 'このガイドでは、SQL Server Migration Assistant for Access (SSMA for Access) を使用して、Microsoft Access データベースを Microsoft SQL Server に移行する方法について説明します。 '
ms.prod: sql
ms.reviewer: ''
ms.technology: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: 3e49909b26d845c0edfaf9e5d50528b077fb2593
ms.sourcegitcommit: ecf074e374426c708073c7da88313d4915279fb9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103603323"
---
# <a name="migration-guide-access-to-sql-server"></a>移行ガイド: SQL Server へのアクセス
[!INCLUDE[sqlserver](../../../includes/applies-to-version/sqlserver.md)]

この移行ガイドでは、SQL Server Migration Assistant for Access (SSMA for Access) を使用して、Microsoft Access データベースを SQL Server に移行する方法について説明します。 

その他の移行ガイドについては、[データベースの移行](https://datamigration.microsoft.com/)に関するページを参照してください。 

## <a name="prerequisites"></a>前提条件 

Access データベースを SQL Server に移行するには、以下が必要です。

- ソース環境がサポートされていることを確認する。 
- [SQL Server Migration Assistant for Access](https://www.microsoft.com/download/details.aspx?id=54255). 


## <a name="pre-migration"></a>移行前 


前提条件を満たすと、環境のトポロジを検出し、移行の可能性を評価する準備は完了です。


### <a name="assess"></a>アクセス

SQL Server Migration Assistant (SSMA) for Access を使用すると、データベース オブジェクトとデータを確認し、移行についてデータベースを評価できます。 ツールの詳細については、[SQL Server Migration Assistant for Access](/sql/ssma/access/sql-server-migration-assistant-for-access-accesstosql) に関するページを参照してください。

評価を作成するには、次の手順を行います。

1. [SQL Server Migration Assistant for Access](https://www.microsoft.com/download/details.aspx?id=54255) を開きます。 
1. **[ファイル]** を選択し、 **[新しいプロジェクト]** を選択します。 移行プロジェクトの名前を指定します。 
1. **[データベースの追加]** を選択し、プロジェクトに追加するデータベースを選択します。 
1. **[Access Metadata Explorer]** で、評価するデータベースを右クリックして、 **[レポートの作成]** を選択します。 
1. 評価レポートを確認します。 次に例を示します。 

### <a name="convert"></a>Convert 

データベース オブジェクトを変換するには、次の手順に従います。 

1. **[Azure SQL Database に接続する]** を選択し、接続の詳細を指定します。 
1. **[Access Metadata Explorer]** 内でデータベースを右クリックし、 **[スキーマの変換]** を選択します。  
1. (省略可能) 個々のオブジェクトを変換するには、オブジェクトを右クリックし、 **[スキーマの変換]** を選択します。 変換されたオブジェクトは、 **[Access Metadata Explorer]** 内に太字で表示されます。 
1. [出力] ペインの **[結果の確認]** を選択し、 **[エラー一覧]** ペインでエラーを確認します。 


## <a name="migrate"></a>Migrate

データベースの評価と不整合への対処が完了したら、次の手順は移行プロセスの実行です。 データの移行は、トランザクション内でデータの行を SQL Server に移動する一括読み込み操作です。 各トランザクションで SQL Server に読み込まれる行数は、プロジェクトの設定で構成されます。

SSMA for Access を使用してデータを移行するには、次の手順に従います。 

1. まだ行っていない場合は、 **[Azure SQL Database への接続]** を選択し、接続の詳細を指定します。 
1. **[Azure SQL Database Metadata Explorer]** でデータベースを右クリックし、 **[データベースとの同期]** を選択します。 この操作により、MySQL スキーマが Azure SQL Database に公開されます。
1. **[Access Metadata Explorer]** を使用して、移行する項目の横にあるチェック ボックスをオンにします。 データベース全体を移行する場合は、データベースの横にあるチェック ボックスをオンにします。 
1. 移行するデータベースまたはオブジェクトを右クリックし、 **[データの移行]** を選択します。 
   データベース全体のデータを移行するには、データベース名の横にあるチェック ボックスをオンにします。 個々のテーブルからデータを移行するには、データベースを展開し、[テーブル] を展開して、テーブルの横にあるチェック ボックスをオンにします。 個々のテーブルのデータを除外するには、このチェック ボックスをオフにします。


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
> これらの問題と、それらを軽減するための具体的な手順に関する追加の詳細については、「[移行後の検証および最適化ガイド](https://docs.microsoft.com/sql/relational-databases/post-migration-validation-and-optimization-guide)」を参照してください。

## <a name="migration-assets"></a>移行資産 

この移行シナリオを完了するための追加のサポートについては、次のリソースを参照してください。これらは、実際の移行プロジェクトの取り組みをサポートするために開発されました。

| **タイトルとリンク** | **説明** |
| -------------- | --------------- |
| [データ ワークロード評価モデルとツール](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | このツールを使用すると、特定のワークロードに対して、推奨される "最適な" ターゲット プラットフォーム、クラウドの準備状況、アプリケーションとデータベースの修復レベルがわかります。 シンプルなワンクリックの計算とレポート生成機能があり、自動化された均一なターゲット プラットフォームの決定プロセスが用意されているので、大規模な資産評価の促進に非常に役立ちます。 |

これらのリソースは、Azure Data Group エンジニアリング チームがスポンサーである Data SQL Ninja プログラムの一部として開発されました。 Data SQL Ninja プログラムの中核となるのは、複雑なモダン化のブロックを解除して加速し、データ プラットフォームを Microsoft の Azure Data プラットフォームに移行する機会を獲得することです。 組織が Data SQL Ninja プログラムへの参加に関心があると思われる場合は、アカウント チームに連絡し、申請を提出するよう依頼してください。

## <a name="next-steps"></a>次の手順

移行後は、[移行後の検証と最適化ガイド](/sql/relational-databases/post-migration-validation-and-optimization-guide)を確認してください。 

さまざまなデータベースとデータの移行シナリオ、および特殊なタスクを支援するために使用できる Microsoft とサードパーティのサービスとツールのマトリックスについては、[データ移行サービスとツール](/azure/dms/dms-tools-matrix)に関するページを参照してください。

その他の移行ガイドについては、[データベースの移行](https://datamigration.microsoft.com/)に関するページを参照してください。 

ビデオ コンテンツについては、以下をご覧ください。
- [移行の過程の概要](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)
