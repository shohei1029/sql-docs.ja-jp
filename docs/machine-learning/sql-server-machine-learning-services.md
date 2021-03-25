---
title: SQL Server Machine Learning Services とは (Python と R)
titleSuffix: ''
description: Machine Learning Services は、リレーショナル データを使用して Python および R スクリプトを実行できるようになる SQL Server の機能です。 オープンソースのパッケージとフレームワーク、および予測分析と機械学習用の Microsoft Python および R パッケージを使用できます。 スクリプトは、SQL Server の外部またはネットワーク経由でデータを移動することなく、データベース内で実行されます。 この記事では、SQL Server Machine Learning Services の基本と開始方法について説明します。
ms.prod: sql
ms.technology: machine-learning-services
ms.date: 03/17/2021
ms.topic: overview
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2017||>=sql-server-linux-ver15'
ms.openlocfilehash: 730dfb2421732f2a7e51cd5d99425c84953501d1
ms.sourcegitcommit: bf7577b3448b7cb0e336808f1112c44fa18c6f33
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104610512"
---
# <a name="what-is-sql-server-machine-learning-services-with-python-and-r"></a>SQL Server Machine Learning Services (Python と R) とは
[!INCLUDE [SQL Server 2017 SQL](../includes/applies-to-version/sqlserver2017.md)]

Machine Learning Services は、リレーショナル データを使用して Python および R スクリプトを実行できるようになる SQL Server の機能です。 オープンソースのパッケージとフレームワーク、および予測分析と機械学習用の [Microsoft Python および R パッケージ](#packages)を使用できます。 スクリプトは、SQL Server の外部またはネットワーク経由でデータを移動することなく、データベース内で実行されます。 この記事では、SQL Server Machine Learning Services の基本と開始方法について説明します。

::: moniker range="=sql-server-2017"
> [!NOTE]
> Machine Learning Services は、[Azure SQL Managed Instance](/azure/azure-sql/managed-instance/machine-learning-services-overview) でもご利用いただけます。 他の SQL プラットフォームの機械学習については、[SQL 機械学習のドキュメント](index.yml)を参照してください。
::: moniker-end
::: moniker range=">=sql-server-ver15||>=sql-server-linux-ver15"
> [!NOTE]
> Machine Learning Services は、[Azure SQL Managed Instance](/azure/azure-sql/managed-instance/machine-learning-services-overview) でもご利用いただけます。 他の SQL プラットフォームの機械学習については、[SQL 機械学習のドキュメント](index.yml)を参照してください。
>
> SQL Server で Java を実行する方法については、[Java 言語拡張のドキュメント](../language-extensions/java-overview.md)を参照してください。
::: moniker-end

## <a name="execute-python-and-r-scripts-in-sql-server"></a>SQL Server で Python および R スクリプトを実行する

SQL Server Machine Learning Services では、データベース内で Python および R スクリプトを実行できます。 この機能を使用して、データの準備とクリーンアップ、特徴エンジニアリング、およびデータベース内での機械学習モデルのトレーニング、評価、およびデプロイを行うことができます。 この機能により、データが存在する場所でスクリプトが実行され、ネットワークを介して別のサーバーにデータが転送されなくなります。

ストアド プロシージャ [sp_execute_external_script](../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md) を使用して、SQL Server インスタンスで Python および R のスクリプトを実行できます。

Python と R のベース ディストリビューションは Machine Learning Services に含まれています。 Microsoft パッケージに加え、PyTorch、TensorFlow、scikit-learn などのオープンソースのパッケージとフレームワークをインストールして使用できます。

Machine Learning Services では、SQL Server での Python および R スクリプトの実行に拡張フレーム ワークを使用します。 このしくみについては以下を参照してください。

+ [機能拡張フレームワーク](concepts/extensibility-framework.md)
+ [Python の拡張機能](concepts/extension-python.md)
+ [R の拡張機能](concepts/extension-r.md)

## <a name="get-started-with-machine-learning-services"></a>Machine Learning Services の概要

::: moniker range=">=sql-server-ver15||>=sql-server-linux-ver15"
1. [Linux](../linux/sql-server-linux-setup-machine-learning.md?toc=/sql/machine-learning/toc.json)または [Windows に SQL Server Machine Learning Services をインストールする](install/sql-machine-learning-services-windows-install.md)。 また、[ビッグ データ クラスターで Machine Learning Services](../big-data-cluster/machine-learning-services.md) を使用したり、[Azure SQL Managed Instance で Machine Learning Services ](/azure/azure-sql/managed-instance/machine-learning-services-overview)を使用したりすることもできます。

1. 開発ツールを構成します。 「[Azure Data Studio のノートブックで Python スクリプトと R スクリプトを実行する](install/sql-machine-learning-azure-data-studio.md)」を使用できます。 [Azure Data Studio](../azure-data-studio/what-is-azure-data-studio.md) で T-SQL を実行することもできます。

1. 初めての Python または R スクリプトを作成する。

   + [SQL 機械学習用の Python チュートリアル](tutorials/python-tutorials.md)
   + [SQL 機械学習用の R チュートリアル](tutorials/r-tutorials.md)
::: moniker-end

::: moniker range="=sql-server-2017"
1. [Windows に SQL Server Machine Learning Services をインストールする](install/sql-machine-learning-services-windows-install.md)。

1. 開発ツールを構成します。 「[Azure Data Studio のノートブックで Python スクリプトと R スクリプトを実行する](install/sql-machine-learning-azure-data-studio.md)」を使用できます。 [Azure Data Studio](../azure-data-studio/what-is-azure-data-studio.md) で T-SQL を使用することもできます。

1. 初めての Python または R スクリプトを作成する。

   + [SQL 機械学習用の Python チュートリアル](tutorials/python-tutorials.md)
   + [SQL 機械学習用の R チュートリアル](tutorials/r-tutorials.md)
::: moniker-end

<a name="versions"></a>

## <a name="python-and-r-versions"></a>Python および R のバージョン

以下に、Machine Learning Services に含まれる Python および R のバージョンの一覧を示します。

| SQL Server のバージョン | 累積的な更新プログラム | Python ランタイムのバージョン | R ランタイムのバージョン |
|-|-|-|-|
| SQL Server 2019 | RTM 以降 | 3.7.1 | 3.5.2 |
| SQL Server 2017 | CU22 以降 | 3.5.2 および 3.7.2 | 3.3.3 および 3.5.2 |
| SQL Server 2017 | RTM - CU21 | 3.5.2 | 3.3.3 |

SQL Server 2016 の R バージョンについては、[「R Services とは」の「R バージョン」セクション](r/sql-server-r-services.md?view=sql-server-2016&preserve-view=true#version)を参照してください。

<a name="packages"></a>

## <a name="python-and-r-packages"></a>Python および R パッケージ

Microsoft のエンタープライズ パッケージに加えて、オープンソース パッケージとフレームワークを使用できます。 最も一般的なオープンソースの Python および R パッケージは、Machine Learning Services にプレインストールされています。 Microsoft の次の Python および R パッケージも含まれています。

| Language | Package | 説明 |
|-|-|-|
| Python | [revoscalepy](python/ref-py-revoscalepy.md) | スケーラブルな Python 用のプライマリ パッケージ。 データの変換と操作、統計の概要、視覚化、および多くの形式のモデリング。 さらに、このパッケージの関数により、並列処理に使用できるコア全体にワークロードが自動的に分散されます。 |
| Python | [microsoftml](python/ref-py-microsoftml.md) | テキスト分析、画像分析、感情分析のカスタム モデルを作成する機械学習アルゴリズムを追加します。 | 
| R | [RevoScaleR](r/ref-r-revoscaler.md) | スケーラブルな R の主要パッケージ。データの変換と操作、統計の概要、視覚化、多くの形式のモデリング。 さらに、このパッケージの関数により、並列処理に使用できるコア全体にワークロードが自動的に分散されます。 |
| R | [MicrosoftML (R)](r/ref-r-microsoftml.md) | テキスト分析、画像分析、感情分析のカスタム モデルを作成する機械学習アルゴリズムを追加します。 |
| R | [olapR](r/ref-r-olapr.md) | SQL Server Analysis Services OLAP キューブに対する MDX クエリに使用される R 関数。 |
| R | [sqlrutils](r/ref-r-sqlrutils.md) | T-SQL ストアド プロシージャで R スクリプトを使用し、そのストアド プロシージャをデータベースに登録し、[R 開発環境](r/set-up-a-data-science-client.md)からストアド プロシージャを実行するメカニズム。 |
| R | [Microsoft R Open](https://mran.microsoft.com/rro) | Microsoft R Open (MRO) は、Microsoft の R の拡張ディストリビューションです。 これは、統計分析とデータ サイエンスの機能が一式そろったオープンソース プラットフォームです。 R に基づき、100% の互換性があり、パフォーマンスと再現性を向上させる追加機能が含まれています。 |

Machine Learning Services と共にインストールされるパッケージと、他のパッケージをインストールする方法の詳細については、以下を参照してください。

::: moniker range=">=sql-server-ver15||>=sql-server-linux-ver15"
+ [Python パッケージ情報の取得](package-management/python-package-information.md)
+ [sqlmlutils を使用した Python パッケージのインストール](package-management/install-additional-python-packages-on-sql-server.md)
+ [R パッケージ情報の取得](package-management/r-package-information.md)
+ [sqlmlutils を使用した新しい R パッケージのインストール](package-management/install-additional-r-packages-on-sql-server.md)
::: moniker-end
::: moniker range="=sql-server-2017"
+ [Python パッケージ情報の取得](package-management/python-package-information.md)
+ [Python ツールを使用して SQL Server 上にパッケージをインストールする](package-management/install-python-packages-standard-tools.md)
+ [R パッケージ情報の取得](package-management/r-package-information.md)
+ [T-SQL (CREATE EXTERNAL LIBRARY) を使用して SQL Server に R パッケージをインストールする](package-management/install-r-packages-with-tsql.md)。
::: moniker-end

## <a name="next-steps"></a>次のステップ

+ [Linux](../linux/sql-server-linux-setup-machine-learning.md?toc=/sql/machine-learning/toc.json)または [Windows に SQL Server Machine Learning Services をインストールする](install/sql-machine-learning-services-windows-install.md)。
+ [SQL 機械学習用の Python チュートリアル](tutorials/python-tutorials.md)
+ [SQL 機械学習用の R チュートリアル](tutorials/r-tutorials.md)