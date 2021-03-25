---
title: Azure Data Studio リリース ノート
description: この記事では、2017 年 11 月から現在までの Azure Data Studio リリースのリリース ノートを取り上げています。 まとめられている問題の多くについては、追加詳細のリンクがあります。
ms.prod: azure-data-studio
ms.technology: azure-data-studio
ms.topic: conceptual
author: yualan
ms.author: alayu
ms.reviewer: maghan
ms.custom: seodec18
ms.date: 3/17/2021
ms.openlocfilehash: 4cc6431ec73a002c015b8aac0dfe19b726cb354f
ms.sourcegitcommit: bf7577b3448b7cb0e336808f1112c44fa18c6f33
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104610701"
---
# <a name="release-notes-for-azure-data-studio"></a>Azure Data Studio のリリース ノート

**[最新リリースのダウンロードとインストール](./download-azure-data-studio.md)**

## <a name="march-2021"></a>2021 年 3 月

2021 年 3 月 17 日 &nbsp; / &nbsp; バージョン: 1.27.0

&nbsp;

| Change | 詳細 |
| ------ | ------- |
| バグの修正 | 修正の全一覧については、[GitHub のバグと問題](https://github.com/microsoft/azuredatastudio/issues?q=is%3Aissue+is%3Aclosed+milestone%3A%22March+2021+Release%22)に関するページを参照してください。 |
| 拡張機能の更新 | [Dacpac](extensions/sql-server-dacpac-extension.md) </br> [SQLDatabaseProjects](extensions/sql-database-project-extension.md) |
| Notebook の新機能 | ブックの作成ダイアログを追加しました |

## <a name="february-2021"></a>2021 年 2 月

2021 年 2 月 18 日 &nbsp; / &nbsp; バージョン: 1.26.0

&nbsp;

| Change | 詳細 |
| ------ | ------- |
| バグの修正 | 修正の全一覧については、[GitHub のバグと問題](https://github.com/microsoft/azuredatastudio/issues?q=is%3Aissue+milestone%3A%22February+2021+Release%22+is%3Aclosed)に関するページを参照してください。 |
| 拡張機能の更新 | [Dacpac](extensions/sql-server-dacpac-extension.md) <br/> [Kusto (KQL)](extensions/kusto-extension.md) </br> [MachineLearning](extensions/machine-learning-extension.md) </br> [Profiler](extensions/sql-server-profiler-extension.md) </br> [SchemaCompare](extensions/schema-compare-extension.md) </br> [SQLDatabaseProjects](extensions/sql-database-project-extension.md) |
| Azure Arc の新しい機能 | 複数のデータ コントローラーがサポートされるようになりました <br/> Kube 構成ファイルのような新しい接続ダイアログ オプション <br/> Postgres ダッシュボードの機能強化 |
| Notebook の新機能 | Windows で 50% 向上した Jupyter サーバーの起動時間 <br/> 右クリックで Jupyter Books を編集するサポートの追加 <br/> URI ノートブック パラメーター化サポートの追加と、[ノートブック パラメーター化のドキュメントの追加](./notebooks/notebooks-parameterization.md) |

## <a name="december-2020-hotfix"></a>2020 年 12 月 (修正プログラム)

2021 年 2 月 10 日 &nbsp; / &nbsp; バージョン: 1.25.3

| Change | 詳細 |
| ------ | ------- |
| バグの修正 [#13899](https://github.com/microsoft/azuredatastudio/issues/13899) | Notebook 内の適切な相互参照リンクまでスクロールする |
| 重要なバグの修正プログラムを組み込むために Electron をアップグレードする| 該当なし |

## <a name="december-2020"></a>2020 年 12 月

2020 年 12 月 9 日 &nbsp; / &nbsp; バージョン:1.25.0

&nbsp;

| Change | 詳細 |
| ------ | ------- |
| バグの修正 | 修正の全一覧については、[GitHub のバグと問題](https://github.com/microsoft/azuredatastudio/issues?q=is%3Aissue+is%3Aclosed+milestone%3A%22December+2020+Release%22)に関するページを参照してください。 |
| データベース プロジェクトの拡張機能の更新 | ワークスペースの追加とサイドバーの改良 |

## <a name="november-2020"></a>2020 年 11 月

2020 年 11 月 12 日 &nbsp; / &nbsp; バージョン:1.24.0

&nbsp;

| Change | 詳細 |
| ------ | ------- |
| バグの修正 | 修正の全一覧については、[GitHub のバグと問題](https://github.com/microsoft/azuredatastudio/issues?q=is%3Aissue+milestone%3A%22November+2020+Release%22+is%3Aclosed)に関するページを参照してください。 |
| 接続ダイアログ | 接続ダイアログの新しい参照タブを追加しました。 |
| 拡張機能の更新 | Postgres 拡張機能の更新プログラムをリリースしました。 |
| ノートブックの新機能 | ノートブックへの SQL のサポートに新機能を追加しました。 <br/> Notebook のパラメーター化のサポートに新機能を追加しました。 <br/>  SQL Notebook の結果ストリーミングに新機能を追加しました。 |
| Python のインストール | PROSE パッケージが既定の Python インストールから削除されました。 |

### <a name="known-issues-1240"></a>既知の問題 (1.24.0)

| New Item | 詳細 | 回避策 |
|----------|---------|------------|
| Azure Arc の拡張機能 | [既知の問題:](https://github.com/microsoft/azuredatastudio/issues/13319)Arc MIAA & PG デプロイの [ノートブックへのスクリプト] ボタンを選択しても、ノートブックのスクリプトを作成する前にフィールドの検証が行われません。 これは、ユーザーがパスワードの確認入力に間違ったパスワードを入力した場合、パスワードの値が間違っているノートブックが生成される可能性があることを意味します。| ただし [デプロイ] ボタンは想定どおりに動作するため、ユーザーは代わりにこれを使用する必要があります。 |
| オブジェクト エクスプローラー | [Azure Synapse Analytics サーバーレス SQL プール](/azure/synapse-analytics/sql/on-demand-workspace-overview)に関連するエンジンの変更により、1.24.0 より前の ADS のリリースでは、オブジェクト エクスプローラーに破壊的変更があります。 | Azure Synapse Analytics サーバーレス SQL プールと共に Azure Data Studio でオブジェクト エクスプローラーを引き続き利用するには、Azure Data Studio 1.24.0 以降を使用する必要があります。 |

他の既知の問題について、および製品チームにフィードバックを提供するには、[Azure Data Studio フィードバック](https://github.com/microsoft/azuredatastudio)を参照してください。

## <a name="october-2020"></a>2020 年 10 月

2020 年 10 月 14 日 &nbsp; / &nbsp; バージョン: 1.23.0

&nbsp;

| Change | 詳細 |
| ------ | ------- |
| Azure SQL Edge | Azure SQL Edge オブジェクトのサポート。 |
| バグの修正 | 修正の全一覧については、[GitHub のバグと問題](https://github.com/microsoft/azuredatastudio/issues?q=is:issue+milestone:%22October+2020+Release%22+is:closed)に関するページを参照してください。 |
| データベース| 同じデータベース参照のサポート。 |
| 拡張機能の更新プログラム | [Azure Arc](extensions/azure-arc-extension.md)</br>[azdata](../azdata/install/deploy-install-azdata.md)</br>[Machine Learning](extensions/machine-learning-extension.md)</br>[Kusto (KQL)](extensions/kusto-extension.md)</br>[スキーマ比較](extensions/schema-compare-extension.md)</br>SQL の評価</br>[SQL Database プロジェクト](extensions/sql-database-project-extension.md)</br>[SQL Server インポート](extensions/sql-server-import-extension.md) |
| デプロイの新機能 | Azure SQL DB と VM のデプロイが追加されました。 |
| PowerShell | PowerShell カーネル結果のストリーミングのサポートが追加されました。 |

## <a name="september-2020-hotfix"></a>2020 年 9 月 (hotfix)

2020 年 9 月 30 日 &nbsp; / &nbsp; バージョン:1.22.1

&nbsp;

| Change | 詳細 |
| ------ | ------- |
| バグと問題が解決されました | 修正の全一覧については、[GitHub のバグと問題](https://github.com/microsoft/azuredatastudio/releases/tag/1.22.1)に関するページを参照してください。 |

## <a name="september-2020"></a>2020 年 9 月

2020 年 9 月 22 日 &nbsp; / &nbsp; バージョン:1.22.0

&nbsp;

| Change | 詳細 |
| ------ | ------- |
| ノートブックの新機能 | <br/> &bull; &nbsp; リッチ テキストの書式設定とマークダウンへのシームレスな変換に基づいたまったく新しいテキスト セル編集エクスペリエンスをサポートします (WYSIWYG (What You See Is What You Get) ツールバーとも呼ばれます) <br/> &bull; &nbsp; Kusto カーネルをサポートします <br/> &bull; &nbsp; ノートブックのピン留めをサポートします <br/> &bull; &nbsp; Jupyter ブックの新しいバージョンのサポートを追加しました <br/> &bull; &nbsp; Jupyter のショートカットを強化しました <br/> &bull; &nbsp; パフォーマンスの読み込みの強化を導入しました |
| SQL Database プロジェクトの拡張機能 | SQL Database プロジェクトの拡張機能を使用すると、Azure Data Studio でプロジェクトベースのデータベースを開発できるようになります。 このプレビュー リリースでは、SQL プロジェクトを Azure Data Studio から作成し、発行することができます。 |
| Kusto (KQL) の拡張機能 | Azure Data Studio にネイティブの Kusto エクスペリエンスを提供し、Azure Data Explorer で、格納されている大量のリアルタイム ストリーミング データに対してデータ探索とデータ分析を実行できるようになります。 このプレビュー リリースは、Azure Data Explorer クラスターの接続と参照、KQL クエリの作成、Kusto カーネルを使用したノートブックの作成をサポートしています。 |
| Azure Arc の拡張機能 | ユーザーは、Azure Data Studio を通じて Azure Arc パブリック プレビューを試すことができます。 これには次のものが含まれます <br/> &bull; &nbsp; データ コントローラーをデプロイする <br/> &bull; &nbsp; Postgres をデプロイする <br/> &bull; &nbsp; Azure Arc 用 Managed Instance をデプロイする <br/> &bull; &nbsp; データ コントローラーに接続する <br/> &bull; &nbsp; データ サービス ダッシュボードにアクセスする <br/> &bull; &nbsp; Azure Arc Jupyter ブック |
| デプロイ オプション | <br/> &bull; &nbsp; Azure SQL Database Edge <br/> (Edge には Azure SQL Edge デプロイの拡張機能が必要です) |
| SQL Server インポート拡張機能の一般提供 | SQL Server インポート拡張機能の一般提供が発表され、機能はプレビュー段階ではなくなりました。 この拡張機能を使用すると、csv/txt ファイルのインポートが簡単になります。 拡張機能の詳細については、[この記事](./extensions/sql-server-import-extension.md)を参照してください。 |
| バグと問題が解決されました | 修正の完全な一覧については、[GitHubの「バグと問題」](https://github.com/microsoft/azuredatastudio/issues?q=is%3Aissue+milestone%3A%22September+2020+Release%22+is%3Aclosed)を参照してください。 |

## <a name="august-2020"></a>2020 年 8 月

2020 年 8 月 12 日&nbsp; / &nbsp;バージョン: 1.21.0

&nbsp;

| Change | 詳細 |
| :----- | :------ |
| ノートブックの新機能 | &bull; &nbsp; セルの場所を移動する <br/> &bull; &nbsp; セルをテキスト セルまたはコード セルに変換する
| Jupyter Book ピッカー | ユーザーは、GitHub リリースから Jupyter ブックを選択し、Azure Data Studio でシームレスに開くことができるようになりました |
| Notebooks Viewlet に追加された検索 | ユーザーは、ノートブックや Jupyter Book 全体でコンテンツを簡単に検索できます |
| バグと問題が解決されました | 修正の完全な一覧については、[GitHubの「バグと問題」](https://github.com/microsoft/azuredatastudio/issues?q=is%3Aissue+milestone%3A%22August+2020+Release%22+is%3Aclosed)を参照してください。 |
| &nbsp; | &nbsp; |

## <a name="july-2020-hotfix"></a>2020 年 7 月 (修正プログラム)

2020 年 7 月 17 日 &nbsp; / &nbsp; バージョン: 1.20.1

&nbsp;

| Change | 詳細 |
| :----- | :------ |
| バグの修正 #11372 オブジェクト エクスプローラーのドラッグ アンド ドロップ テーブルでテーブル名が正しくラップされない | [#11372](https://github.com/microsoft/azuredatastudio/issues/11372) |
| バグの修正 #11356 ダーク テーマが既定のテーマになってしまう | [#11356](https://github.com/microsoft/azuredatastudio/issues/11356) |
| &nbsp; | &nbsp; |

### <a name="known-issue"></a>既知の問題

- 一部のユーザーが、このリリースに含まれている新しい Microsoft.Data.SqlClient v2.0.0 からの接続エラーを報告しました。 ユーザーは、[次の手順に従う](https://github.com/microsoft/azuredatastudio/issues/11367#issuecomment-659614111)ことで、接続が正常に行われることを発見しました

## <a name="july-2020"></a>2020 年 7 月

2020 年 7 月 15 日 &nbsp; / &nbsp; バージョン:1.20.0

&nbsp;

| Change | 詳細 |
| :----- | :------ |
| 新しい機能ツアーの追加 | Connections Viewlet、Notebook viewlet、Extensions Marketplace などのよく利用される機能のチュートリアルを、ホーム ページとコマンド パレットから機能ツアーを起動して、ユーザーが開始できるようになりました |
| ノートブックの新機能 | &bull; &nbsp; Markdown ツールバーでのヘッダーのサポート<br/> &bull; &nbsp; テキスト セルでの Markdown の横に並べてのプレビュー
| クエリ エディターでの列とテーブルのドラッグ アンド ドロップ | ユーザーは、Connections Viewlet からクエリ エディターに、列とテーブルを直接ドラッグ アンド ドロップできるようになりました |
| アクティビティ バーへの Azure アカウント アイコンの追加 | Azure にサインインする場所をユーザーが簡単に確認できるようになりました |
| バグと問題が解決されました | 修正の完全な一覧については、[GitHubの「バグと問題」](https://github.com/microsoft/azuredatastudio/issues?q=is%3Aissue+milestone%3A%22July+2020+Release%22+is%3Aclosed)を参照してください。 |
| &nbsp; | &nbsp; |

## <a name="june-2020"></a>2020 年 6 月

2020 年 6 月 15 日 &nbsp; / &nbsp; バージョン:1.19.0

&nbsp;

| Change | 詳細 |
| :----- | :------ |
| Azure portal 統合に Azure Data Studio が追加されました | ユーザーは Azure SQL Database 接続、Azure Postgres などから、Azure portal を直接起動できるようになりました。 |
| ノートブックの新機能 | &bull; &nbsp; 新しいノートブック ツール バー <br/> &bull; &nbsp; 新しい [セルの編集] ツールバー <br/> &bull; &nbsp; Python 依存関係ウィザード UX の更新 <br/> &bull; &nbsp; ノートブック間のスペースの向上 |
| SQL Assessment API 拡張機能の発表 | この拡張機能は、ADS における SQL Server ベスト プラクティス評価を追加します。 以前は PowerShell SqlServer モジュールおよび SMO でしか使用できなかった SQL Assessment API が公開されています。これにより SQL Server インスタンスを評価したり、SQL Server チームによるそれらの推奨事項を受け取ったりすることができるようになります。 [こちらの記事](../tools/sql-assessment-api/sql-assessment-api-overview.md)で、SQL Assessment API の詳細と、この拡張機能でできることについて説明します。 |
| [Machine Learning 拡張機能の改善点](./extensions/machine-learning-extension.md) | Azure SQL Managed Instance がサポートされるようになりました。 |
| データ仮想化拡張機能の改善点 | MongoDB と Teradata がサポートされるようになりました |
| Postgres 拡張機能のバグの修正 | Azure MFA が修正されました |
| バグと問題が解決されました | 修正の完全な一覧については、[GitHubの「バグと問題」](https://github.com/microsoft/azuredatastudio/issues?q=is%3Aissue+milestone%3A%22June+2020+Release%22+is%3Aclosed)を参照してください。 |
| &nbsp; | &nbsp; |

## <a name="may-2020-hotfix"></a>2020 年 5 月 (修正プログラム)

2020 年 5 月 27 日 &nbsp; / &nbsp; バージョン:1.18.1

&nbsp;

| Change | 詳細 |
| :----- | :------ |
| バグの修正 #10538 "現在のクエリの実行" キーバインドが予期したとおりに動作しなくなった | [#10538](https://github.com/microsoft/azuredatastudio/issues/10538)  |
| バグの修正 #10537 バージョン 1.18 で新規または既存の SQL ファイルを開くことができない | [#10537](https://github.com/microsoft/azuredatastudio/issues/10537)  |
| &nbsp; | &nbsp; |

## <a name="may-2020"></a>2020 年 5 月

2020 年 5 月 20 日 &nbsp; / &nbsp; バージョン:1.18.0

&nbsp;

| Change | 詳細 |
| :----- | :------ |
| Redgate SQL Prompt 拡張機能の発表 | この拡張機能を使用すると、Azure Data Studio 内で書式スタイルを直接管理できるため、IDE を離れることなくスタイルを作成および編集できます。 |
| Machine Learning 拡張機能の発表 | この拡張機能を使用すると、次のことができます。 <br/> &bull; &nbsp; SQL Server Machine Learning Services と Azure Data Studio を使用して Python および R パッケージを管理する。<br/> &bull; &nbsp; ONNX モデルを使用して Azure SQL Edge で予測を行う。<br/> &bull; &nbsp; Azure SQL Edge データベースで ONNX モデルを表示する。 <br/> &bull; &nbsp; ONNX モデルをファイルまたは Azure Machine Learning から Azure SQL Edge データベースにインポートする。 <br/> &bull; &nbsp; ノートブックを作成して実験を実行する。 |
| ノートブックの新機能 | &bull; &nbsp; Python の依存関係を簡単にインストールできるようにする新しい Python 依存関係ウィザードが追加されました <br/> &bull; &nbsp; Markdown ツール バーのアンダーライン サポートが追加されました |
| Always Encrypted のパラメーター化 | 暗号化されたデータベース列で挿入、更新、またはフィルター処理を行うクエリを実行できます。|
| バグと問題が解決されました | 修正の完全な一覧については、[GitHubの「バグと問題」](https://github.com/microsoft/azuredatastudio/issues?q=is%3Aissue+milestone%3A%22May+2020+Release%22+is%3Aclosed)を参照してください。 |
| &nbsp; | &nbsp; |

## <a name="april-2020-hotfix"></a>2020 年 4 月 (修正プログラム)

2020 年 4 月 30 日 &nbsp; / &nbsp; バージョン:1.17.1

&nbsp;

| Change | 詳細 |
| :----- | :------ |
| MFA 経由で接続できないバグ #10197 の修正 | [#10197](https://github.com/microsoft/azuredatastudio/issues/10197)  |
| &nbsp; | &nbsp; |

## <a name="april-2020"></a>2020 年 4 月

2020 年 4 月 27 日 &nbsp; / &nbsp; バージョン:1.17.0

&nbsp;

| Change | 詳細 |
| :----- | :------ |
| ウェルカム ページの改善 | 一般的なアクションや拡張機能の強調表示を見やすくするため、ウェルカム ページの UI が更新されました。 |
| ノートブックの新機能 | &bull; &nbsp; Markdown での記述を支援するため、テキスト セルを編集するときに Markdown ツールバーが追加されました <br/> &bull; &nbsp; Jupyter Books viewlet が刷新され、Jupyter Books とノートブックを一緒に管理できる Notebooks viewlet になりました <br/>&bull; &nbsp; ノートブックを保存するときのグラフの保持に関するサポートが追加されました <br/> &bull; &nbsp; Python ノートブックでの KQL マジックのサポートが追加されました|
| ダッシュボードの強化 | Azure Data Studio 全体のダッシュボードが、アクション ツールバーなどの最新の設計パターンで更新されました。 これは、多くの拡張機能にも適用されます。 |
| Azure ビューに Cloud Shell 統合の追加 | |
| Always Encrypted とセキュリティで保護されたエンクレーブが設定された Always Encrypted のサポート | |
| バグと問題が解決されました | 修正の完全な一覧については、[GitHubの「バグと問題」](https://github.com/microsoft/azuredatastudio/issues?q=is%3Aissue+is%3Aclosed+milestone%3A%22April+2020+Release%22)を参照してください。 |
| &nbsp; | &nbsp; |
| バグと問題が解決されました | 修正の完全な一覧については、[GitHubの「バグと問題」](https://github.com/microsoft/azuredatastudio/issues?q=is%3Aissue+is%3Aclosed+milestone%3A%22April+2020+Release%22)を参照してください。 |
| &nbsp; | &nbsp; |

## <a name="march-2020"></a>2020 年 3 月

2020 年 3 月 18 日 &nbsp; / &nbsp; バージョン:1.16.0 

&nbsp;

| Change | 詳細 |
| :----- | :------ |
| SQL Notebook でのグラフ作成サポートの追加 | コード セルで SQL クエリを実行しているときに、ユーザーがグラフを作成して保存できるようになりました。 |
| Jupyter Book の作成エクスペリエンスの追加 | ユーザーはノートブックを使用して独自の Jupyter Book を作成できるようになりました。 |
| Postgres 拡張機能の Azure AD サポートの追加 | |
| アクセシビリティに関する多くのバグを修正 | [アクセシビリティに関するバグのリスト](https://github.com/microsoft/azuredatastudio/issues?page=1&q=is%3Aissue+is%3Aclosed+milestone%3A%22S360+-+Accessibility%22+label%3AA11y_AzureDataStudio) |
| 1\.42 への VS Code のマージ | このリリースには、以前の 3 つの VS Code リリースからの VS Code の更新プログラムが含まれています。 詳細については、[それらのリリース ノートをお読みください](https://code.visualstudio.com/updates/v1_42)。 |
| バグと問題が解決されました | 修正の完全な一覧については、[GitHubの「バグと問題」](https://github.com/microsoft/azuredatastudio/issues?q=is%3Aissue+milestone%3A%22March+2020%22+is%3Aclosed)を参照してください。 |
| &nbsp; | &nbsp; |

## <a name="february-hotfix"></a>2 月 (修正プログラム)

2020 年 2 月 19 日 &nbsp; / &nbsp; バージョン:1.15.1

&nbsp;

| Change | 詳細 |
| :----- | :------ |
| バグの修正 #9149 アクティブな接続を表示 | [#9149](https://github.com/microsoft/azuredatastudio/issues/9149)  |
| バグの修正 #9061 SQL ペインの表示と非表示を切り替えたときに [データの編集] グリッドで適切にサイズ変更が行われない | [#9061](https://github.com/microsoft/azuredatastudio/issues/9061)  |
| &nbsp; | &nbsp; |

## <a name="february-2020"></a>2020 年 2 月

2020 年 2 月 13 日 &nbsp; / &nbsp; バージョン:1.15.0

&nbsp;

| Change | 詳細 |
| :----- | :------ |
| Azure サインインの新しい改善 | 改善された Azure サインイン エクスペリエンスを追加しました。よりシームレスな接続エクスペリエンスを実現するための、デバイス コードのコピーまたは貼り付けの削除などが含まれます。 |
| Notebook サポートでの検索 | ユーザーはノートブック内で Ctrl + F キーを使用できるようになりました。 Notebook サポートでの検索では、コードとテキスト セルの両方で 1 行ずつ検索が行われます。 |
| 1\.38 から 1.42 への VS Code のマージ | このリリースには、以前の 3 つの VS Code リリースからの VS Code の更新プログラムが含まれています。 詳細については、[それらのリリース ノートをお読みください](https://code.visualstudio.com/updates/v1_42)。 |
| 多くのユーザーから報告されている ["画面が白くなるまたは空になる"](https://github.com/microsoft/azuredatastudio/issues/8775) という問題を修正しました。 | |
| バグと問題が解決されました | 修正の完全な一覧については、[GitHubの「バグと問題」](https://github.com/microsoft/azuredatastudio/issues?q=is%3Aissue+is%3Aclosed+milestone%3A%22February+2020%22)を参照してください。 |
| &nbsp; | &nbsp; |

### <a name="known-issue"></a>既知の問題

- macOS Catalina のユーザーは Azure Data Studio を右クリックして、[開く] をクリックする必要があります。

## <a name="december-2019-hotfix"></a>2019 年 12 月 (修正プログラム)

2019 年 12 月 26 日 &nbsp; / &nbsp; バージョン:1.14.1

&nbsp;

| Change | 詳細 |
| :----- | :------ |
| バグ #8747 OE 拡張失敗の修正 | [#8747](https://github.com/microsoft/azuredatastudio/issues/8747)  |
| &nbsp; | &nbsp; |

## <a name="december-2019"></a>2019 年 12 月

2019 年 12 月 19 日 &nbsp; / &nbsp; バージョン:1.14.0 

&nbsp;

| Change | 詳細 |
| :----- | :------ |
| 現在アクティブな接続のみを一覧表示するように Notebooks の [attach to connection]\(接続にアタッチ\) ドロップダウンを変更しました | [#8129](https://github.com/microsoft/azuredatastudio/issues/8129) |
| BDC に接続するとき、TLS/SSL 検証エラーを無視することを許可する目的で bigdatacluster.ignoreSslVerification 設定を追加しました | [#8582](https://github.com/microsoft/azuredatastudio/pull/8582) |
| オフライン クエリ エディターの既定の言語フレーバーを変更することを許可 | [#8419](https://github.com/microsoft/azuredatastudio/pull/8419) |
| ビッグ データ クラスター/SQL 2019 機能の GA ステータス | [#8269](https://github.com/microsoft/azuredatastudio/issues/8269) |
| バグと問題が解決されました | 修正の完全な一覧については、[GitHubの「バグと問題」](https://github.com/microsoft/azuredatastudio/milestone/44?closed=1)を参照してください。 |
| &nbsp; | &nbsp; |


## <a name="november-2019-hotfix"></a>2019 年 11 月 (修正プログラム)

2019 年 11 月 15 日 &nbsp; / &nbsp; バージョン:1.13.1

&nbsp;

| Change | 詳細 |
| :----- | :------ |
| コピー/貼り付けの結果の順序が乱れるバグ #8210 の修正 |  |
| &nbsp; | &nbsp; |

## <a name="november-2019"></a>2019 年 11 月

2019 年 11 月 4 日 &nbsp; / &nbsp; バージョン:1.13.0 

&nbsp;

| Change | 詳細 |
| :----- | :------ |
| 新しい SQL Server 2019 のサポート | &bull; &nbsp; BDC 展開ウィザードを使用して、SQL Server 2019 ビッグ データ クラスターを展開する <br/>&bull; &nbsp; コントローラー ダッシュボードを使用してクラスターの正常性を管理する <br/>&bull; &nbsp; [Security ACLs]\(セキュリティ ACL)\ ダイアログを使用して HDFS アクセス制御リストを管理する <br/> &bull; &nbsp; [HDFS Tiering]\(HDFS 階層\) ダイアログを使用してマウントを追加する <br/> &bull; &nbsp; 組み込みの Jupyter Book、SQL Server 2019 ガイドを使用してトラブルシューティングを行う <br/> &bull; &nbsp; SQL vNext 拡張データ仮想化の拡張機能に名前変更された <br/> &bull; &nbsp; 外部テーブル ウィザードに Teradata および Mongo サポートが追加された|
| ノートブックの新機能 | &bull; &nbsp; PowerShell ノートブックの発表 <br/> &bull; &nbsp; 折りたたみ可能なコード セルの発表 <br/>&bull; &nbsp; ノートブックのパフォーマンスが向上 <br/> &bull; &nbsp; 機能強化の完全な一覧については、[こちら](https://github.com/microsoft/azuredatastudio/issues?q=is%3Aissue+milestone%3A%22November+2019+Release%22+is%3Aclosed+label%3A%22Area+-+Notebooks%22)を参照してください |
| Jupyter Books の発表  | Jupyter Books は、目次に整理されたノートブック ファイルとマークダウン ファイルのコレクションです。 |
| 新しい SQL Server 展開ウィザード  | 以下の展開のサポートが追加されました。 <br/> &bull; &nbsp; Windows 上の SQL Server 2019 <br/> &bull; &nbsp; Windows 上の SQL Server 2017 <br/> &bull; &nbsp; Docker 上の SQL Server 2019 <br/> &bull; &nbsp; Docker 上の SQL Server 2017 |
| スキーマ比較拡張機能の GA の発表| &bull; &nbsp; SQLCMD モード <br/> &bull; &nbsp; ローカライズ サポート <br/> &bull; &nbsp; アクセシビリティに関する修正 <br/> &bull; &nbsp; セキュリティに関するバグ  |
| SQL Server Dacpac 拡張機能の GA の発表| <br/> &bull; &nbsp; ローカライズ サポート <br/> &bull; &nbsp; アクセシビリティに関する修正 <br/> &bull; &nbsp; セキュリティに関するバグ |
| Visual Studio IntelliCode 拡張機能の発表 | Visual Studio IntelliCode では、SQL がサポートされるようになりました。これにより、予約キーワードをよりスマートに提示できます。 |
| バグと問題が解決されました | 修正の完全な一覧については、[GitHubの「バグと問題」](https://github.com/microsoft/azuredatastudio/issues?q=is%3Aissue+milestone%3A%22November+2019+Release%22+is%3Aclosed)を参照してください。 |
| &nbsp; | &nbsp; |

## <a name="october-2019-hotfix-2"></a>2019 年 10 月 (修正プログラム x 2)

2019 年 10 月 11 日 &nbsp; / &nbsp; バージョン: 1.12.2

&nbsp;

| Change | 詳細 |
| :----- | :------ |
| 検査モードで自動的に EH を開始しないようにする |  |
| &nbsp; | &nbsp; |

## <a name="october-2019-hotfix"></a>2019 年 10 月 (修正プログラム)

2019 年 10 月 8 日 &nbsp; / &nbsp; バージョン: 1.12.1

&nbsp;

| Change | 詳細 |
| :----- | :------ |
| ノートブックの引用符とバックスラッシュが正しくエスケープされるよう、問題を修正しました。 |  |
| &nbsp; | &nbsp; |

## <a name="october-2019"></a>2019 年 10 月

2019 年 10 月 2 日 &nbsp; / &nbsp; バージョン: 1.12.0

&nbsp;

| Change | 詳細 |
| :----- | :------ |
| クエリ履歴拡張機能のリリース | SQL 履歴拡張機能では、Azure Data Studio セッションで実行された過去のクエリがすべて保存され、実行順に一覧表示されます。 ユーザーは、クエリの作成、クエリの実行、クエリの削除、クエリ履歴の一時停止、すべてのクエリ履歴エントリの削除を確認することができます。 |
| 結果の新しいコピー/貼り付け | 結果グリッドから結果をコピーして貼り付けるための追加の方法が加えられました。 |
| PowerShell 拡張機能の更新 |  |
| バグと問題が解決されました | 修正の完全な一覧については、[GitHubの「バグと問題」](https://github.com/microsoft/azuredatastudio/milestone/42?closed=1)を参照してください。 |
| &nbsp; | &nbsp; |

### <a name="known-issues"></a>既知の問題

- ノートブック
  - [7080](https://github.com/microsoft/azuredatastudio/issues/7080) Notebook が正しくシリアル化されないまれなケース

## <a name="september-2019"></a>2019 年 9 月

2019 年 9 月 10 日 &nbsp; / &nbsp; バージョン:1.11.0 

&nbsp;

| Change | 詳細 |
| :----- | :------ |
| SQLCMD モードの有効化 | クエリ エディターで SQLCMD モードを切り替えて、クエリを SQLCMD スクリプトとして記述および編集できるようになりました |
| コミュニティ拡張機能:クエリ エディター ブースト | クエリ エディター ブーストとは、Azure Data Studio クエリ エディターを、クエリを頻繁に記述するユーザー向けに拡張したオープン ソースの拡張機能です。 &bull; &nbsp; 現在のクエリをスニペットとして保存する <br/>&bull; &nbsp; Ctrl + U を使用してデータベースを切り替える <br/> &bull; &nbsp; テンプレートから新しいクエリ <br/> &bull; &nbsp; 機能強化の完全な一覧については、[こちら](https://github.com/dzsquared/query-editor-boost)を参照してください |
| ノートブックの機能強化 | &bull; &nbsp; より大きなノートブック ファイルをサポートするためのパフォーマンスの強化 <br/> &bull; &nbsp; 機能強化の完全な一覧については、[こちら](https://github.com/microsoft/azuredatastudio/issues?q=is%3Aissue+milestone%3A%22September+2019+Release%22+label%3A%22Area%3A+Notebooks%22+is%3Aclosed)を参照してください |
| Visual Studio Code August Release Merge 1.38 | 最新の機能強化については、[こちら](https://code.visualstudio.com/updates/v1_38)を参照してください。 |
| バグと問題が解決されました | 修正の完全な一覧については、[GitHubの「バグと問題」](https://github.com/microsoft/azuredatastudio/milestone/39?closed=1)を参照してください。 |
| &nbsp; | &nbsp; |

### <a name="known-issues"></a>既知の問題

- ノートブック
  - [7080](https://github.com/microsoft/azuredatastudio/issues/7080) Notebook が正しくシリアル化されないまれなケース

## <a name="august-2019"></a>2019 年 8 月

2019 年 8 月 15 日 &nbsp; / &nbsp; バージョン:1.10.0 

&nbsp;

| Change | 詳細 |
| :----- | :------ |
| SandDance 1.3.1 拡張機能のリリース | &bull; &nbsp; スマート グラフ検出 <br/>&bull; &nbsp; 3D 視覚化 <br/> &bull; &nbsp; データ フィルタリング |
| ノートブックの機能強化 | &bull; &nbsp; コードまたはテキスト セルのインラインでの追加 <br/>&bull; &nbsp; SQL 結果グリッドを右クリックして結果を CSV、JSON などとして保存する機能を追加しました <br/> &bull; &nbsp; JSON をより高速に読み込むためのノートブック読み込みのパフォーマンスの向上 <br/> &bull; &nbsp; 機能強化の完全な一覧については、[こちら](https://github.com/microsoft/azuredatastudio/issues?q=is%3Aissue+label%3A%22Area%3A+Notebooks%22+milestone%3A%22August+2019+Release%22+is%3Aclosed)を参照してください |
| SQL Server 2019 のサポート |  このリリースには、次のような追加の SQL Server 2019 ビッグ データ クラスター機能のサポートが含まれています。 <br/> &bull; &nbsp; オブジェクト マッピング ページ上でテーブルと列の情報の読み込みにかかる時間が短縮されました。 <br/> &bull; &nbsp; 接続の詳細ページ上で、既存のデータベース スコープの資格情報の読み込みに関するバグを修正しました。 <br/> &bull; &nbsp; PROSE の解析に使用される既定のサンプル サイズが増加しました。 | 
| Dacpac 拡張機能での Azure AD のサポート | 
| Visual Studio Code July Release Merge 1.37 | 最新の機能強化については、[こちら](https://code.visualstudio.com/updates/v1_37)を参照してください。 |
| バグと問題が解決されました | 修正の完全な一覧については、[GitHubの「バグと問題」](https://github.com/microsoft/azuredatastudio/milestone/39?closed=1)を参照してください。 |
| &nbsp; | &nbsp; |

## <a name="july-2019"></a>2019 年 7 月

2019 年 7 月 11 日 &nbsp; / &nbsp; バージョン:1.9.0 

&nbsp;

| Change | 詳細 |
| :----- | :------ |
| SentryOne Plan Explorer 拡張機能のリリース | Microsoft のパートナーである SentryOne は、[Azure Data Studio 向けの SentryOne Plan Explorer 拡張機能](https://www.sentryone.com/products/sentryone-plan-explorer-extension-azure-data-studio)をリリースする予定です。 <br> この無料の拡張機能では、Azure Data Studio で実行されるクエリの高度なプラン図を取得できます。また、最適化されたレイアウト アルゴリズムと直感的な色分けにより、クエリのパフォーマンスに影響を与える、最も高コストな演算子をすばやく識別できます。 この拡張機能の詳細については、SentryOne のブログ記事 ([こちら](https://sqlperformance.com/2019/07/sentryone/plan-explorer-extension-azure-data-studio)) をご覧ください。 |
| スキーマ比較の新機能 | &bull; &nbsp; スキーマ比較ファイルのサポート (.SCMP) <br/>&bull; &nbsp; スキーマ比較サポートのキャンセル <br/>&bull; &nbsp; 詳細な変更内容については、[こちら](https://github.com/microsoft/azuredatastudio/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A%22July+2019+Release%22+label%3A%22Area%3A+Schema+Compare%22+is%3Aclosed+)を参照してください|
| ノートブックの機能強化 | &bull; &nbsp; Plotly Python のサポート <br/>&bull; &nbsp; ブラウザーからノートブックを開く <br/> &bull; &nbsp; Python パッケージの管理ダイアログ <br/> &bull; &nbsp; パフォーマンスとマークダウンの機能強化 <br/> &bull; &nbsp; キーボード ショートカットの更新 <br/>  &bull; &nbsp; バグ修正とマイナー機能については、[こちら](https://github.com/microsoft/azuredatastudio/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A%22July+2019+Release%22+is%3Aclosed+label%3A%22Area%3A+Notebooks%22+)を参照してください |
| SQL Server 2019 のサポート |  このリリースには、次のような追加の SQL Server 2019 ビッグ データ クラスター機能のサポートが含まれています。 <br/> &bull; &nbsp; 管理ダッシュボード内のサービス エンドポイント テーブル。クラスター内のすべての主要なサービスが一覧表示されます。 <br/> &bull; &nbsp; クラスター状態ノートブックでは、すべてのサービスとポッドを対象にクラスターの状態を照会し、トラブルシューティングを行う方法が示されます。| 
| 更新された言語パックが利用可能| 拡張機能マネージャー マーケットプレースでは、10 個の言語パックが新たに提供されています。 拡張機能マーケットプレースを使用して特定の言語を検索し、インストールできます。 選択した言語をインストールすると、新しい言語での再起動を求めるメッセージが Azure Data Studio に表示されます。 |
| SQL Server プロファイラーの更新 | SQL Server プロファイル拡張機能が更新され、次のような新機能が追加されました。 <br/> &bull; &nbsp; データベース名によるフィルター処理 <br/> &bull; &nbsp; コピー/貼り付けのサポート <br/> &bull; &nbsp; フィルターの保存/読み込み <br/>SQL Server プロファイラー拡張機能の機能強化の完全な一覧については、[こちら](https://github.com/microsoft/azuredatastudio/issues?utf8=%E2%9C%93&q=is%3Aissue+is%3Aclosed+milestone%3A%22July+2019+Release%22+label%3A%22Area%3A+SQL+Profiler%22+)を参照してください。  |
| Visual Studio Code May Release Merge 1.35 | 最新の機能強化については、[こちら](https://code.visualstudio.com/updates/v1_35)を参照してください。 |
| バグと問題が解決されました | 以前のリリースの Azure Data Studio では、接続ダイアログからの接続時にユーザー データベースが選択された場合、取得されるオブジェクト エクスプローラー エントリのスコープが、その 1 つのデータベースに完全に制限されていました。 今回のリリースからは、この動作が変更され、サーバー レベルのプロパティもオブジェクト エクスプローラーに表示されるようになりました。 <br/> 修正の完全な一覧については、[GitHubの「バグと問題」](https://github.com/microsoft/azuredatastudio/milestone/35?closed=1)を参照してください。 |
| &nbsp; | &nbsp; |

## <a name="june-2019"></a>2019 年 6 月

2019 年 6 月 6 日 &nbsp; / &nbsp; バージョン:1.8.0

&nbsp;

| Change | 詳細 |
| :----- | :------ |
| 中央管理サーバー (CMS) 拡張機能のリリース | 中央管理サーバーには、1 つ以上の中央管理サーバー グループに編成される SQL Server インスタンスの一覧が格納されます。 ユーザーは、自分の既存の CMS サーバーに接続し、サーバーの追加や削除を行って、サーバーを管理できます。 詳細については、[こちら](../relational-databases/administer-multiple-servers-using-central-management-servers.md)を参照してください |
| Database Administration Tool Extensions for Windows 拡張機能のリリース | この拡張機能では、Azure Data Studio の SQL Server Management Studio で最も使用されている、2 つのエクスペリエンスが提供されます。 ユーザーは、さまざまなオブジェクト (データベース、テーブル、列、ビューなど) を右クリックし、[プロパティ] を選択して、そのオブジェクトの SSMS プロパティ ダイアログを表示することができます。 また、データベースを右クリックして [スクリプトの生成] を選択し、なじみ深い SSMS スクリプト生成ウィザードを起動することもできます。 
| スキーマ比較の機能強化 | &bull; &nbsp; [除外]/[含める] オプションが追加されました <br/>&bull; &nbsp; [スクリプトの生成] で、生成後にスクリプトが開くようになりました <br/>&bull; &nbsp; 二重のスクロール バーが削除されました  <br/>&bull; &nbsp; 書式設定とレイアウトの機能強化 <br/>&bull; &nbsp; 詳細な変更内容については、[こちら](https://github.com/microsoft/azuredatastudio/issues?q=is%3Aissue+milestone%3A%22June+2019+Release%22+label%3A%22Area%3A+Schema+Compare%22+is%3Aclosed)を参照してください|
| [メッセージ] セクションが独自のタブに移動されました | 以前は、ユーザーが SQL クエリを実行すると、結果とメッセージがスタック パネルに表示されていました。 これからは、SSMS のように、1 つのパネル内で個別のタブに表示されます。 |
| SQL ノートブックの機能強化 | &bull; &nbsp; ユーザーが、ノートブックで独自の Python 3 または Anaconda インストールを使用できるようになりました <br/>&bull; &nbsp; 安定性とフィット感/しあげ感に関する複数の修正 <br/> &bull; &nbsp; 機能強化の完全な一覧については、[こちら](https://github.com/microsoft/azuredatastudio/issues?q=is%3Aissue+milestone%3A%22June+2019+Release%22+is%3Aclosed+label%3A%22Area%3A+Notebooks%22)を参照してください|
| Visual Studio Code April Release Merge 1.34 | 最新の機能強化については、[こちら](https://code.visualstudio.com/updates/v1_34)を参照してください |
| バグと問題が解決されました。 | [GitHub の「バグと問題」](https://github.com/microsoft/azuredatastudio/milestone/32?closed=1)を参照してください。 |
| &nbsp; | &nbsp; |

### <a name="known-issues"></a>既知の問題

- Database Administration Tool Extensions for Windows
    - 切断されたサーバー ノードからプロパティを起動できない
    - Azure サーバーのプロパティを起動できない
    - 必ずしもすべてのオブジェクトにプロパティ ダイアログがない
    - ダイアログの起動に時間がかかる
    - 一部の種類の接続 (Azure AD など) を使用してサーバーを起動するときにエラーが発生する
- ノートブック
    - [5838](https://github.com/microsoft/azuredatastudio/issues/5838) ユーザーがノートブック用にシステム Python を使用できるようにする
- スキーマ比較
    - [5804](https://github.com/microsoft/azuredatastudio/issues/5804) Schema Compare タスクで、既定のキャンセル コンテキスト メニューが表示されるが、何も実行されない

## <a name="may-2019"></a>2019 年 5 月

2019 年 5 月 8 日 &nbsp; / &nbsp; バージョン:1.7.0 

&nbsp;

| Change | 詳細 |
| :----- | :------ |
| スキーマ比較拡張機能のリリース | スキーマ比較は SQL Server Data Tools (SSDT) のよく知られた機能であり、主なユースケースは、データベースと .dacpac ファイルの違いを比較して視覚化し、それらを同じにするためのアクションを実行することです。 |
| タスク ビューが出力ウィンドウに移動されしました | ユーザーは、出力ウィンドウのタスク ビューで、実行時間の長いタスク (バックアップ、復元、スキーマ比較など) の状態を確認できるようになりました
| ウェルカム ページの追加 | &bull; &nbsp; [新しいクエリ]、[新しいファイル]、[新しいノートブック] などの一般的なアクションへのリンク <br/>&bull; &nbsp; ドキュメントと GitHub へのリンク |
| SQL ノートブックの機能強化 | &bull; &nbsp; マークダウン レンダリングの機能強化 (メモとテーブルのサポート強化など) <br/>&bull; &nbsp; ツールバーの使いやすさの向上 <br/>&bull; &nbsp; 信頼されたノートブックに対するマークダウン リンクで、Cmd/Ctrl + クリックが不要になり、直接クリックできるようになりました <br/>&bull; &nbsp; ノートブックを閉じた後に Jupyter プロセスをクリーンアップして、複数のノートブックを同時に起動したときのエラーを減らす機能が強化されました <br/>&bull; &nbsp; SQL ノートブック接続が機能強化され、同じデータベースに対して 2 つのノートブックを実行したときにエラーが発生しないようになりました <br/>&bull; &nbsp; ノートブックが機能強化され、ツールバーの [セルの実行] ボタンをクリックしたときに、現在実行中のセルに自動的にスクロールするようになりました <br/>&bull; &nbsp; 一般的な安定性とパフォーマンスの向上 |
| バグと問題が解決されました。 | [GitHub の「バグと問題」](https://github.com/microsoft/azuredatastudio/milestone/31?closed=1)を参照してください。 |
| &nbsp; | &nbsp; |

## <a name="april-2019"></a>2019 年 4 月

2019 年 4 月 18 日 &nbsp; / &nbsp; バージョン:1.6.0 

&nbsp;

| Change | 詳細 |
| :----- | :------ |
| **[サーバー]** タブの名前が **[接続]** に変更されました | |
| Azure Resource Explorer を [接続] の Azure viewlet として 移動しました | ユーザーは、[接続] ビューの Azure viewlet を使用して Azure SQL インスタンスを表示し、展開して各サーバーやデータベース下のオブジェクトを表示できるようになりました。|
| SQL ノートブックの機能強化 | &bull; &nbsp; すべてのセルの出力をクリアするためのボタンがツールバーに追加されました <br/>&bull; &nbsp; すべてのセルを実行するためのボタンがツールバーに追加されました <br/>&bull; &nbsp; [アタッチ先] ドロップダウンで、サーバー名 (設定されている場合) が接続名に修正されました <br/>&bull; &nbsp; 相対イメージ パスを使用している場合に、マークダウン内のイメージがレンダリングされない問題が修正されました <br/>&bull; &nbsp; ダブルクリックによる列サイズの自動変更が追加され、マウスホイールのサポートが強化されたことで、ノートブック グリッド内の機能性が向上しました <br/>&bull; &nbsp; ノートブックを使用して python をインストールする際のエラー処理と、python インストールの回復性が強化されました <br/>&bull; &nbsp; ノートブックのセルを選択するときの "すべて選択" 機能が強化されました <br/>&bull; &nbsp; ノートブックが閉じられたり、オブジェクト エクスプローラーへの接続に影響が及ばないよう、ノートブック接続の機能が強化されました <br/>&bull; &nbsp; ノートブックが切断され、セルを実行するために接続が必要になったときに、ユーザーにメッセージが表示されるよう、ノートブックのエクスペリエンスが改善されました<br/>&bull; &nbsp; ADS の再開時に、保存されていないノートブックが ADS で復元されるよう、サポートが強化されました |
| バグと問題が解決されました。 | [GitHub の「バグと問題」](https://github.com/Microsoft/azuredatastudio/milestone/26?closed=1)を参照してください。 |
| &nbsp; | &nbsp; |

## <a name="march-2019-hotfix"></a>2019 年 3 月 (修正プログラム)

2019 年 3 月 22 日 &nbsp; / &nbsp; バージョン:1.5.2 &nbsp; / &nbsp; 修正プログラムのリリース

&nbsp;

| Change | 詳細 |
| :----- | :------ |
| 1\.5.1 で発見されたいくつかの問題を修正しました。 | [「3 月の修正プログラム リリース」(GitHub)](https://github.com/Microsoft/azuredatastudio/milestone/28) を参照してください。<br/> <br/>&bull; &nbsp; ダッシュボードの "ノートブック​​を開く" タスクで開かれたノートブックをユーザーが閉じることができない問題を修正しました <br/>&bull; &nbsp; 保存後に、ノートブック JSON に余分な } がある問題を修正しました <br/>&bull; &nbsp; ノートブック グリッドがテーマの変更に応答しなかった問題を修正しました <br/>&bull; &nbsp; タブ ヘッダーにノートブックの完全なパスが表示される問題を修正しました。 今後は、ファイル名のみが表示されます。 |
| &nbsp; | &nbsp; |

## <a name="march-2019"></a>2019 年 3 月

2019 年3月18日 &nbsp; / &nbsp; バージョン:1.5.1

&nbsp;

| Change | 詳細 |
| :----- | :------ |
| [Azure Data Studio 用の PostgreSQL 拡張機能](./extensions/postgres-extension.md)が追加されました | サポートされている機能: <br/>&bull; &nbsp; 接続ダイアログ <br/>&bull; &nbsp; オブジェクト エクスプローラー <br/>&bull; &nbsp; クエリ エディター <br/>&bull; &nbsp; グラフ作成 <br/>&bull; &nbsp; ダッシュボード <br/>&bull; &nbsp; スニペット <br/>&bull; &nbsp; データの編集 <br/>&bull; &nbsp; ノートブック |
| 追加された SQL ノートブック | 組み込みの Notebook ビューアーに SQL カーネルのサポートが追加されました。 <br/>&bull; &nbsp; T-SQL のサポート <br/>&bull; &nbsp; PGSQL のサポート |
| 追加された PowerShell 拡張機能 | VS Code から [PowerShell 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell)を利用できます。  |
| 追加された SQL Server dacpac の拡張機能  | SQL Server インポート拡張機能からデータ層アプリケーション ウィザードが削除され、新しい拡張機能が導入されました。  |
| 追加されたコミュニティ拡張機能 QueryPlan.show | クエリ プランを視覚化するための統合サポートが追加されました  |
| 更新された SQL Server 2019 Preview 拡張機能 | &bull; &nbsp; Jupyter Notebook のサポート (特に Python3 と Spark カーネル) が、コアの Azure Data Studio ツールに移動されました。 <br/>&bull; &nbsp; 外部データ ウィザードのバグ修正 |
| バグと問題が解決されました。 | [GitHub の「バグと問題」](https://github.com/Microsoft/azuredatastudio/milestone/25?closed=1)を参照してください。 |
| &nbsp; | &nbsp; |

### <a name="known-issues"></a>既知の問題
- [#4427](https://github.com/Microsoft/azuredatastudio/issues/4427):カーネルで Spark の準備が整う前にセルで実行をクリックすると、致命的なエラーが発生する **回避策:** カーネルが読み込まれるまで待機し、その後、セルを実行します
- [#4493](https://github.com/Microsoft/azuredatastudio/issues/4493):SQL 認証を使用して SSMS から ADS を起動すると、ユーザーのパスワードが求められる **回避策:** 当面は Windows 認証を使用してください。 
- [#4494](https://github.com/Microsoft/azuredatastudio/issues/4494):SQL ノートブック機能をインストールできない <br/>
**対処法:** [こちら](https://github.com/Microsoft/azuredatastudio/issues/4494#issuecomment-473043832)の回避手順に従ってください。 
- [#4503](https://github.com/Microsoft/azuredatastudio/issues/4503):Azure Data Studio をダウンロード フォルダーから直接開くことができない (Mac) <br />
**対処法:** アプリの解凍後にコンピューターを再起動してください。 今後調査される予定です。 
- [#4539](https://github.com/Microsoft/azuredatastudio/issues/4539):ノートブックに名前を付けて保存すると、接続コンテキストが失われる <br />
**対処法:** 次のリリースで修正される予定です。 
- [#4458](https://github.com/Microsoft/azuredatastudio/issues/4458):無効なバージョンが使用されている場合、dacpac で抽出を使用すると、SqlToolsService がクラッシュする <br/>
**対処法:** Azure Data Studio を再起動し、正しいバージョンが使用されていることを確認してください。
- 新しいノートブックと開いているノートブックのアイコンが失われる <br/>
**対処法:** レガシの接続の種類は非推奨とされています。 SQL Server エンドポイントに接続することをお勧めします。これにより、すべてのアクション (新しいノートブック、Spark ジョブ) が期待どおりに動作します。 

## <a name="february-2019"></a>2019 年 2 月

2019 年 2 月 13 日 &nbsp; / &nbsp; バージョン:1.4.5

&nbsp;

| Change | 詳細 |
| :----- | :------ |
| 拡張パック **Admin pack for SQL Server** が追加されました。 | これにより、SQL Server の管理に関連する拡張機能がインストールしやすくなります。 これには次のものが含まれます<br/>&bull; &nbsp; [SQL Server エージェント](./extensions/sql-server-agent-extension.md)<br/>&bull; &nbsp; [SQL Server プロファイラー](./extensions/sql-server-profiler-extension.md)<br/>&bull; &nbsp; [SQL Server インポート](./extensions/sql-server-import-extension.md) |
| プロファイラー拡張機能に、拡張イベントのフィルター処理のサポートが追加されました。 | &nbsp; |
| T-SQL の結果を XML として保存できる "XML として保存" 機能が追加されました。 | &nbsp; |
| データ層アプリケーション ウィザードの機能強化が追加されました。 | &bull; &nbsp; [スクリプトの生成] ボタンが追加されました<br/>&bull; &nbsp; デプロイ中にデータ損失の可能性があることを警告するためのビューが追加されました。 |
| SQL Server 2019 Preview 拡張機能が更新されました。 | 「[データ仮想化の拡張機能](./extensions/data-virtualization-extension.md)」を参照してください。 |
| 実行時間の長いクエリでは、結果のストリーミングが既定で有効になります。 | &nbsp; |
| バグと問題が解決されました。 | [GitHub の「バグと問題」](https://github.com/Microsoft/azuredatastudio/milestone/23?closed=1)を参照してください。 |
| &nbsp; | &nbsp; |

## <a name="january-2019-hotfix"></a>2019 年 1 月 (修正プログラム)

2019 年 1 月 16 日 &nbsp; / &nbsp; バージョン:1.3.9 &nbsp; / &nbsp; 修正プログラムのリリース

&nbsp;

| Change | 詳細 |
| :----- | :------ |
| 1\.3.8 で発見されたいくつかの問題を修正しました。 | [「1 月の修正プログラム リリース」(GitHub)](https://github.com/Microsoft/azuredatastudio/milestone/24?closed=1) を参照してください。<br/><br/>詳細については、以下を参照してください。<br/>&bull; &nbsp; [変更ログ (GitHub)](https://github.com/Microsoft/azuredatastudio/blob/main/CHANGELOG.md)。<br/>&bull; &nbsp; [リリース (GitHub)](https://github.com/Microsoft/azuredatastudio/releases)。 |
| &nbsp; | &nbsp; |

## <a name="january-2019"></a>2019 年 1 月

2019 年 1 月 9 日 &nbsp; / &nbsp; バージョン:1.3.8

&nbsp;

| Change | 詳細 |
| :----- | :------ |
| Windows 用の新しいユーザー インストーラーが追加されました。 | 既存のシステム インストーラーとは異なり、新しいユーザー インストーラーでは管理者特権は必要ありません。 これにより、管理者以外の方も簡単にアップグレードが行えるようになります。 |
| Azure Active Directory 認証のサポートが追加されました。 | &nbsp; |
| Idera SQL DM Performance Insights (プレビュー) の発表。 | &nbsp; |
| SQL Server インポート拡張機能でのデータ層アプリケーション ウィザードのサポート。 | &nbsp; |
| SQL Server 2019 Preview 拡張機能への更新。 | 「[データ仮想化の拡張機能](./extensions/data-virtualization-extension.md)」を参照してください。 |
| SQL Server プロファイラーの機能強化。 | &nbsp; |
| 大規模なクエリの結果のストリーミング (プレビュー)。 | &nbsp; |
| コミュニティ拡張機能: sp_executesql から sql および新規データベースへ。 | &nbsp; |
| バグと問題が解決されました。 | [GitHub の「バグと問題」](https://github.com/Microsoft/azuredatastudio/milestone/19?closed=1)を参照してください。 |
| &nbsp; | &nbsp; |

## <a name="november-2018"></a>2018 年 11 月

2018 年 11 月 6 日 &nbsp; / &nbsp; バージョン:1.2.4

&nbsp;

| Change | 詳細 |
| :----- | :------ |
| SQL Server 2019 Preview 拡張機能への更新。 | 「[データ仮想化の拡張機能](./extensions/data-virtualization-extension.md)」を参照してください。 |
| Paste the Plan 拡張機能の導入。 | &nbsp; |
| High Color クエリ拡張機能の導入 (SSMS エディター テーマを含む)。 | &nbsp; |
| SQL Server エージェント、プロファイラー、およびインポート拡張機能の修正。 | &nbsp; |
| macOS で非アクティブな接続が削除される原因となった、.NET Core Socket KeepAlive の問題の修正。 | &nbsp; |
| SQL Tools Service を .NET Core 2.2 Preview 3 (最終的な Azure AD サポート用) にアップグレード。 | &nbsp; |
| &nbsp; | &nbsp; |

### <a name="bug-fixes-november-2018"></a>バグの修正、2018 年 11 月

- 修正: [問題 #2933](https://github.com/Microsoft/azuredatastudio/issues/2933):Azure SQL Database への接続が失われた
- 修正: [問題 #2914](https://github.com/Microsoft/azuredatastudio/issues/2914):OE データベース ノードを展開する際の "無効な引数" 例外
- 修正: [問題 #2935](https://github.com/Microsoft/azuredatastudio/pull/2935):クエリ結果で複数行のメッセージを正しく表示する
- 修正: [問題 #2906](https://github.com/Microsoft/azuredatastudio/pull/2906):テーブル名に特殊文字が含まれている場合に、データの編集ドキュメント名を修正する
- 修正: [問題 #2929](https://github.com/Microsoft/azuredatastudio/issues/2929):組み込み拡張機能 changelog で、VSCode のリリース ノートを読んで変更点を確認するように求めるメッセージが表示される
- 修正: [問題 #2719](https://github.com/Microsoft/azuredatastudio/issues/2719):ハイ コントラスト テーマでアイコンが二重/三重に表示される
- 修正: [問題 #3047](https://github.com/Microsoft/azuredatastudio/pull/3047):SQL Server に接続するためのコマンド ライン インターフェイスを追加する
- 修正: [問題 #3031](https://github.com/Microsoft/azuredatastudio/pull/3031):クエリ プラン テーマのサポートを追加する

## <a name="october-2018"></a>2018 年 10 月

2018 年 10 月 29 日 &nbsp; / &nbsp; バージョン:1.1.4

&nbsp;

| Change | 詳細 |
| :----- | :------ |
| Azure SQL Database を参照するための Azure Resource Explorer の導入。 | &nbsp; |
| オブジェクト エクスプローラーとクエリ エディターの接続性の堅牢性が向上しました。 | &nbsp; |
| SQL エージェント拡張機能の強化。 | &nbsp; |
| SQL Server 2019 Preview 拡張機能への更新。 | 「[データ仮想化の拡張機能](./extensions/data-virtualization-extension.md)」を参照してください。 |
| &nbsp; | &nbsp; |

### <a name="bug-fixes-october-2018"></a>バグの修正、2018 年 10 月

- 修正: [問題 #2717](https://github.com/Microsoft/azuredatastudio/issues/2717):XML 列の結果をクリックした際の書式設定
- 修正: [問題 #2993](https://github.com/Microsoft/azuredatastudio/issues/2993):結果ウィンドウの幅が不完全
- 修正: [問題 #2999](https://github.com/Microsoft/azuredatastudio/issues/2999):DB に接続しているときに、Mac で System.Diagnostics.Tracing ファイルを読み込むことができなかった
- 修正: [問題 #2851](https://github.com/Microsoft/azuredatastudio/issues/2851):TimeSeries グラフが正しく表示されない
- 修正: [問題 #2996](https://github.com/Microsoft/azuredatastudio/issues/2996):急なセッション変更によって一時テーブルが失われる

詳細については、「[変更ログ](https://github.com/Microsoft/azuredatastudio/blob/main/CHANGELOG.md)」と「[リリース](https://github.com/Microsoft/azuredatastudio/releases)」を参照してください。

## <a name="september-2018-ga-release"></a>2018 年 9月 (GA リリース)

2018 年 9 月 24 日 &nbsp; / &nbsp; バージョン:1.0 &nbsp; / &nbsp; GA リリース

Azure Data Studio (旧称 SQL Operations Studio) の一般提供リリース。

&nbsp;

| Change | 詳細 |
| :----- | :------ |
| 大量の結果セットに対するクエリ結果グリッドのパフォーマンスと UX の向上。 | &nbsp; |
| Visual Studio Code のソース コードが 1.23 から 1.26.1 に更新され、グリッド レイアウトと設定エディター (プレビュー) が改善されました。 | &nbsp; |
| スクリーン リーダー、キーボード ナビゲーション、およびハイコントラストのアクセシビリティが向上しました。 | &nbsp; |
| Servers view-let で別の表示名を指定するための `Connection name` オプションが追加されました。 | &nbsp; |
| &nbsp; | &nbsp; |

### <a name="announcing-the-sql-server-2019-preview-extension"></a>SQL Server 2019 Preview 拡張機能の発表

&nbsp;

| Change | 詳細 |
| :----- | :------ |
| [ビッグ データ クラスター](../big-data-cluster/big-data-cluster-overview.md)のサポートを含む、SQL Server 2019 プレビュー機能のサポート。 | SQL Server 2019 preview に付属している HDFS/Spark ゲートウェイに接続できます。<br/><br/>HDFS の参照、ファイルのアップロード、ファイルの保存、便利な操作の起動 (ノートブックでの CSV ファイルの分析など) が行なえます。<br/><br/>ダッシュボードから Spark ジョブを送信したり、オブジェクト エクスプローラーで HDFS/Spark 接続を右クリックすることができます。 |
| Azure Data Studio ノートブック。 | 統合されたノートブック ビューアーを使用して、ノートブックを作成したり、開いたりできます。 このリリースのノートブック ビューアーでは、ローカル カーネルと SQL Server 2019 ビッグ データ クラスターへの接続のみがサポートされています。<br/><br/>ノートブックで PROSE コード アクセラレータ ライブラリを使用して、ファイル形式とデータ型について学習し、データ準備を迅速化することができます。 |
| Azure Resource Explorer。 | Azure Resource Explorer ビューを使用すると、Azure アカウントのデータ関連のエンドポイントを参照し、オブジェクト エクスプローラーでそれらへの接続を作成できます。 このリリースでは、Azure SQL Database がサポートされています。 |
| SQL Server PolyBase の外部テーブル作成ウィザード。 | 使いやすいウィザードを使用して、外部テーブルとそれに関連するメタデータ構造を作成できます。 このリリースでは、リモート SQL Server と Oracle サーバーがサポートされています。 |
| &nbsp; | &nbsp; |

### <a name="bug-fixes-september-2018"></a>バグの修正、2018 年 9 月

- 修正: [問題 #2647](https://github.com/Microsoft/azuredatastudio/issues/143):グラフの表示が、かなり前のバージョンに後退した。
- 修正: [問題 #2648](https://github.com/Microsoft/azuredatastudio/issues/143):SELECT で、列全体に JSON ハイパーリンクが返される。

詳細については、「[変更ログ](https://github.com/Microsoft/azuredatastudio/blob/main/CHANGELOG.md)」と「[リリース](https://github.com/Microsoft/azuredatastudio/releases)」を参照してください。

## <a name="august-2018"></a>2018 年 8 月

2018 年 8 月 30 日 &nbsp; / &nbsp; バージョン:0.32.8 &nbsp; / &nbsp; パブリック プレビュー

"*8 月のパブリック プレビュー*" では、バグの修正、製品安定化、および既存シナリオでのギャップの解消に重点が置かれています。

_0.32.8 には、0.32.7 で検出されたいくつかの回帰の修正が含まれています ([#1971](https://github.com/Microsoft/azuredatastudio/issues/1971)、[#2372](https://github.com/Microsoft/azuredatastudio/issues/2372))_

&nbsp;

| Change | 詳細 |
| :----- | :------ |
| SQL Server インポート拡張機能の発表。 | &nbsp; |
| SQL Server プロファイラーのセッション管理。 | &nbsp; |
| SQL Server プロファイラーのセッション テンプレートのサポート。 | &nbsp; |
| SQL Server エージェントの機能強化。 | &nbsp; |
| 新しいコミュニティ拡張機能:ファースト レスポンダー キット。 | &nbsp; |
| クオリティ オブ ライフの改善:Connection strings | &nbsp; |
| &nbsp; | &nbsp; |

### <a name="bug-fixes-august-2018"></a>バグの修正、2018 年 8 月

- `Parse Syntax` コマンドを使用して、クエリ エディター ウィンドウで SQL を解析する。
- 修正: [問題 #143](https://github.com/Microsoft/azuredatastudio/issues/143):ダブルクリックしたときに、変数名の @ が選択されない。
- 修正: [問題 #387](https://github.com/Microsoft/azuredatastudio/issues/387):SQL タブの DB アイコンが赤になる。
- 修正: [問題 #825](https://github.com/Microsoft/azuredatastudio/issues/825):要求:[ALTER としてスクリプト] を選択した後に、現在のサーバーに自動接続する 
- 修正: [問題 #1278](https://github.com/Microsoft/azuredatastudio/issues/1278): sqlops.desktop [デスクトップ エントリ] - 名前とコメントの値が冗長。
- 修正: [問題 #1285](https://github.com/Microsoft/azuredatastudio/issues/1285):更新すると、Windows でアプリケーション アイコンが削除/置換される。
- 修正: [問題 #1317](https://github.com/Microsoft/azuredatastudio/issues/1317):小数点区切り文字を修正する。
- 修正: [問題 #1474](https://github.com/Microsoft/azuredatastudio/issues/1474):接続の変更を取り消すと、現在の接続が切断される。
- 修正: [問題 #1497](https://github.com/Microsoft/azuredatastudio/issues/1497):グラフ表示のオプションが下部で表示からはみ出る。
- 修正: [問題 #1524](https://github.com/Microsoft/azuredatastudio/issues/1524):シェル/ダッシュボード:メインの viewlet アイコンがドラッグ可能であり、アプリがクラッシュすることがある。
- 修正: [問題 #1578](https://github.com/Microsoft/azuredatastudio/issues/1578):名前をクリックして、リモート ファイル ブラウザー フォルダーを展開したり折りたたんだりすることができない。
- 修正: [問題 #1620](https://github.com/Microsoft/azuredatastudio/issues/1620):機能の提案:既存の接続の接続文字列を取得する。
- 修正: [問題 #1624](https://github.com/Microsoft/azuredatastudio/issues/1624):SelectBox で、無効になっているときに色が変更されない。
- 修正: [問題 #1728](https://github.com/Microsoft/azuredatastudio/issues/1728):JSON/EXCEL/CSV として保存する操作が機能しない。
- 修正: [問題 #1744](https://github.com/Microsoft/azuredatastudio/issues/1744):タブ間を切り替えると、結果ペインのスクロール位置が失われる。
- 修正: [問題 #1748](https://github.com/Microsoft/azuredatastudio/issues/1748):Excel ファイルの 2 回目以降の保存時にエラー メッセージが表示される。
- 修正: [問題 #1782](https://github.com/Microsoft/azuredatastudio/issues/1782):データの編集: Esc キーを押しても、セルが元の値に戻らない。
- 修正: [問題 #1836](https://github.com/Microsoft/azuredatastudio/issues/1836): .sql ファイルが SQL Operations Studio に関連付けられない。
- 修正: [問題 #1850](https://github.com/Microsoft/azuredatastudio/issues/1850):N'' と入力すると、オートコンプリートで N''' になる。
- 修正: [問題 #1985](https://github.com/Microsoft/azuredatastudio/issues/1985):クエリ結果グリッドからのコピーが 1 列ずれる。
- 修正: [問題 #1998](https://github.com/Microsoft/azuredatastudio/pull/1998):[バージョン情報] ダイアログに VS Code のバージョンを追加する。
- 修正: [問題 #2042](https://github.com/Microsoft/azuredatastudio/pull/2042):エージェント:[有効] ボタンを使用して、sql ファイルからクエリをインポートする。
- 修正: [問題 #2091](https://github.com/Microsoft/azuredatastudio/issues/2091):Ctrl + C キーを使用して、結果ペインから結果をコピーすることができない。
- 修正: [問題 #2099](https://github.com/Microsoft/azuredatastudio/pull/2099):SaveAsCsv オプションが追加された。
- 修正: [問題 #2107](https://github.com/Microsoft/azuredatastudio/issues/2107):ダッシュボードおよびプロファイラー ドキュメントのドキュメント アイコンを更新する。
- 修正: [問題 #2129](https://github.com/Microsoft/azuredatastudio/pull/2129):タブを切り替えたときに、[データの編集] のスクロール位置が保存される。
- 修正: [問題 #2152](https://github.com/Microsoft/azuredatastudio/issues/2152):結果グリッドの行インジケーターが 0 から始まる。

### <a name="known-issues-august-2018"></a>既知の問題、2018 年 8 月

- [問題 #2371](https://github.com/Microsoft/azuredatastudio/issues/2371) [Excel として保存] を選択しても、データの最初の行しか保存されない
- [問題 #2150](https://github.com/Microsoft/azuredatastudio/issues/2150):Ubuntu 16.04 でコンテナー内の SQL に接続できない

## <a name="july-2018"></a>2018 年 7 月

2018 年 7 月 19 日 &nbsp; / &nbsp; バージョン:0.31.4 &nbsp; / &nbsp; パブリック プレビュー

"*7 月のパブリック プレビュー*" では、次の項目に重点が置かれています。

- SQL Server エージェントの構成シナリオの初期リリース。
- SQL Server プロファイラー セッションとビュー テンプレートの機能強化。
- お客様から報告された GitHub の問題に対する継続的なバグ修正。

&nbsp;

| Change | 詳細 |
| :----- | :------ |
| [SQL Server Agent for SQL Operations Studio 拡張機能](./extensions/sql-server-agent-extension.md)の機能強化。 | アラート、演算子、およびプロキシのビューと、左ペインのアイコンが追加されました。<br/><br/>新しいジョブ、新しいジョブ ステップ、新しいアラート、および新しい演算子のためのダイアログが追加されました。<br/><br/>[ジョブの削除]、[アラートの削除]、[Delete Operator]\(演算子の削除\)(右クリック) が追加されました。<br/><br/>前回の実行を表示する機能が追加されました。<br/><br/>列名ごとのフィルターが追加されました。 |
| [SQL Server Profiler for SQL Operations Studio 拡張機能](./extensions/sql-server-profiler-extension.md)の機能強化。 | 拡張イベントを表示するための 5 つの既定のテンプレートが追加されました。<br/><br/>サーバー/データベース接続名が追加されました。<br/><br/>Azure SQL Database インスタンスのサポートが追加されました。<br/><br/>プロファイラーの実行中にタブが閉じられたときに、プロファイラーを終了するかどうか尋ねるメッセージが追加されました。 |
| スクリプト結合拡張機能のリリース。 | &nbsp; |
| 拡張機能の作成者用に、ウィザードとダイアログ機能拡張ポイントが追加されました。 | &nbsp; |
| &nbsp; | &nbsp; |

### <a name="bug-fixes-july-2018"></a>バグの修正、2018 年 7 月

- 修正: [問題 728](https://github.com/Microsoft/azuredatastudio/issues/728):MacOS で [接続の追加] への応答がない
- 修正: [問題 1612](https://github.com/Microsoft/azuredatastudio/issues/1612):結果グリッドのテキスト表示が国際文字によって失敗する
- 修正: [問題 1693](https://github.com/Microsoft/azuredatastudio/issues/1693):バックアップ ダイアログ:ファイル ブラウザーの UI が壊れている
- 修正: [問題 1713](https://github.com/Microsoft/azuredatastudio/issues/1713):影響を受ける行の数
- 修正: [問題 1718](https://github.com/Microsoft/azuredatastudio/issues/1718):どのデータソースにも接続できない
- 修正: [問題 1719](https://github.com/Microsoft/azuredatastudio/issues/1719):サーバーに接続するときの TypeError
- 修正: [問題 1724](https://github.com/Microsoft/azuredatastudio/issues/1724):拡張機能のダイアログが動作を停止した
- 修正: [問題 1749](https://github.com/Microsoft/azuredatastudio/issues/1749):バグ:列内の HTML データが解釈される
- 修正: [問題 1789](https://github.com/Microsoft/azuredatastudio/issues/1789):機能拡張: 接続プロバイダーを追加した場合、アンインストールしても一覧から削除されない
- 修正: [問題 1791](https://github.com/Microsoft/azuredatastudio/issues/1791):sqlops 拡張機能: queryeditor.connect() でターゲット データベースに接続しても、UI にはエディターが接続されていることが表示されない
- 修正: [問題 1799](https://github.com/Microsoft/azuredatastudio/issues/1799):大文字と小文字が区別されるインスタンスでは、上位 10 件の DB サイズのグラフが機能しない
- 修正: [問題 1814](https://github.com/Microsoft/azuredatastudio/issues/1814): sqlops の入力ミスによって、暗黙的な "any" 型の定義が発生する
- 修正: [問題 1817](https://github.com/Microsoft/azuredatastudio/issues/1817):スペルの誤り
- 修正: [問題 1830](https://github.com/Microsoft/azuredatastudio/issues/1830):component() が呼び出された後に ButtonComponent の iconPath を設定しても、アイコンが変更されない
- 修正: [問題 1843](https://github.com/Microsoft/azuredatastudio/issues/1843):テーブル編成の改善

## <a name="june-2018"></a>2018 年 6 月

2018 年 6 月 20 日 &nbsp; / &nbsp; バージョン:0.30.6 &nbsp; / &nbsp; パブリック プレビュー

&nbsp;

| Change | 詳細 |
| :----- | :------ |
| **SQL Server Profiler for SQL Operations Studio _Preview_** 拡張機能の初期リリース。 | &nbsp; |
| 新しい **SQL Data Warehouse** 拡張機能には、データウェアハウスに分析情報を提示する、高機能でカスタマイズ可能なダッシュボード ウィジェットが含まれています。 | これにより、データウェアハウスの管理とチューニングに関する主要なシナリオに対応できるようになり、データを最適化して一貫したパフォーマンスを提供できるようになります。 |
| **データ編集での "フィルター処理と並べ替え"** のサポート。 | &nbsp; |
| **SQL Server Agent for SQL Operations Studio _Preview_** 拡張機能の機能強化 ([ジョブ] および [ジョブ履歴] ビュー)。 | &nbsp; |
| **Wizard & Dialog UI Builder Framework** 拡張性 API の機能強化。 | &nbsp; |
| VS Code プラットフォームのソース コードの更新。 | 次のリリースが統合されました。<br/>&bull; &nbsp; [2018 年 3 月 (1.22)](https://code.visualstudio.com/updates/v1_22)<br/>&bull; &nbsp; [2018 年 4 月 (1.23)](https://code.visualstudio.com/updates/v1_23) |
| &nbsp; | &nbsp; |

### <a name="github-issues-fixes-june-2018"></a>GitHub の問題の修正、2018 年 6 月

- 機能の要求 ([問題 1204](https://github.com/Microsoft/azuredatastudio/issues/1204)):結果グリッドの列幅がデータに対して自動調整されるようにし、同じクエリを再実行する際に、手動での変更が記憶されるようにしてください。
- 修正: [問題 1398](https://github.com/Microsoft/azuredatastudio/issues/1398):リンクされたアカウントが空の場合に、[メッセージの追加] ボタンと [アカウントの追加] ボタンが表示されるようにしてください。
- 修正: [問題 1399](https://github.com/Microsoft/azuredatastudio/issues/1399):ビューが折りたたまれている場合に、[リンクされたアカウント] タブが正しく表示されない。
- 修正: [問題 1374](https://github.com/Microsoft/azuredatastudio/issues/1374):ディスクから .sql ファイルを開くと、SQL Tools Service がクラッシュする。
- 修正: [問題 1372](https://github.com/Microsoft/azuredatastudio/issues/1372):SQL キーワード "BETWEEN" がない。
- 修正: [問題 1395](https://github.com/Microsoft/azuredatastudio/issues/1395):' MATCH ' キーワードによって SQL Tools Service がクラッシュする。
- 修正: [問題 1496](https://github.com/Microsoft/azuredatastudio/issues/1496):オブジェクト エクスプローラーのコンテキスト メニューで [新しいプロファイラー] オプションを選択しても何も実行されない。
- 修正: [問題 1495](https://github.com/Microsoft/azuredatastudio/issues/1495):クエリ エディターの "Explain" クエリ プランが壊れている。

## <a name="may-2018"></a>2018 年 5 月

2018 年 5 月 7 日 &nbsp; / &nbsp; バージョン:0.29.3 &nbsp; / &nbsp; パブリック プレビュー

"*5 月のパブリック プレビュー*" では、安定化とバグ修正に重点が置かれています。

&nbsp;

| Change | 詳細 |
| :----- | :------ |
| 拡張機能マネージャーで使用できる Redgate SQL Search 拡張機能の発表。 | &nbsp; |
| 10 言語に対応したコミュニティ ローカライズを利用できます。 | ドイツ語、スペイン語、フランス語、イタリア語、日本語、韓国語、ポルトガル語、ロシア語、中国語 (簡体字)、および中国語 (繁体字)。 |
| テレメトリ収集が変更されました。 | &bull; &nbsp; テレメトリ収集の削減。<br/>&bull; &nbsp; オプトアウトのエクスペリエンス改善。<br/>&bull; &nbsp; プライバシーに関する声明への製品内リンク。 |
| 拡張機能マネージャーの Marketplace エクスペリエンスが改善されました。 | コミュニティ拡張機能をより簡単に見つけることができます。 |
| SQL エージェント拡張機能。 | &bull; &nbsp; ジョブ。<br/>&bull; &nbsp; [ジョブ履歴] ビューの改善。 |
| whoisactive および Server Reports 拡張機能の更新。 | &nbsp; |
| [Manage Dashboard Properties]\(ダッシュボード プロパティの管理\) のスクロールが改善されました。 | &nbsp; |
| &nbsp; | &nbsp; |

### <a name="fix-github-issues"></a>GitHub の問題の修正

- 修正: [問題 703](https://github.com/Microsoft/azuredatastudio/issues/703):データの編集で HTML のようなテキストを入力すると、更新するまで値が誤って表示される
- 修正: [問題 821](https://github.com/Microsoft/azuredatastudio/issues/821): azuredatastudio.deb パッケージの依存関係
- 修正: [問題 1260](https://github.com/Microsoft/azuredatastudio/issues/1260):キーワード "distinct" が強調表示されない
- 修正: [問題 1332](https://github.com/Microsoft/azuredatastudio/issues/1332):データの編集で、行を元に戻す操作が機能しない
- 修正: [問題 1215](https://github.com/Microsoft/azuredatastudio/issues/1215):SQL エージェント拡張機能とステータス バー
- 修正: [問題 1316](https://github.com/Microsoft/azuredatastudio/issues/1316):ウィンドウのサイズを変更した後、SQL エージェントでサイズ変更が行われない

## <a name="april-2018"></a>2018 年 4 月

2018 年 4 月 25 日 &nbsp; / &nbsp; バージョン:0.28.6 &nbsp; / &nbsp; パブリック プレビュー

"*4 月のパブリック プレビュー*" には、バグ修正と機能強化が含まれています。

&nbsp;

| Change | 詳細 |
| :----- | :------ |
| SQL エージェント プレビュー拡張機能の機能強化: | &nbsp; |
| &nbsp; &nbsp; &nbsp; ファイルのサポートが強化されました。 | &bull; &nbsp; サイズの大きなファイル。<br/>&bull; &nbsp; 保護されたファイル (管理者権限で保護されたファイルの保存用)。<br/>&bull; &nbsp; SQL Operations Studio 内での、\> 256 MB のファイルの格納。 |
| &nbsp; &nbsp; &nbsp; 統合型のターミナル分割。 | 複数の開いている端末を同時に操作できます。 |
| &nbsp; &nbsp; &nbsp; インストールと起動にかかる時間の短縮。 | ディスク上のファイル数について、インストールのフットプリントが削減されました。 |
| &nbsp; | &nbsp; |

### <a name="fix-github-issues-april-2018"></a>GitHub の問題の修正、2018 年 4 月

- 修正: [問題 37](https://github.com/Microsoft/azuredatastudio/issues/37):グラフ ビューアーでエラーがスローされると、予期しない動作が発生する。
- 修正: [問題 462](https://github.com/Microsoft/azuredatastudio/issues/462):機能の要求:サーバー グループが既定で展開されるオプション。
- 修正: [問題 606](https://github.com/Microsoft/azuredatastudio/issues/606): intellisense - "update" コマンドの不適切な候補。
- 修正: [問題 967](https://github.com/Microsoft/azuredatastudio/issues/967):結果グリッドで [XML プラン表示] を選択したときに、クエリ プランが表示されるようにして欲しい。
- 修正: [問題 1023](https://github.com/Microsoft/azuredatastudio/issues/1023):flyfishingdba からの ms_foreachdb の呼び出しに対して角かっこを追加する。
- 修正: [問題 1048](https://github.com/Microsoft/azuredatastudio/issues/1048):ログイン前の SSL/TLS ハンドシェイクのエラー。
- 修正: [問題 1050](https://github.com/Microsoft/azuredatastudio/issues/1050):エラーを表示する前に分析情報ビューをクリアする。
- 修正: [問題 1057](https://github.com/Microsoft/azuredatastudio/issues/1057):エクスプローラーウィジェットの [復元] と [新しいクエリ] のアクションが壊れている。
- 修正: [問題 1068](https://github.com/Microsoft/azuredatastudio/issues/1068):ダッシュボードの出力ウィンドウが、Azure SQL Database についてのエラー メッセージと共にポップアップ表示される。
- 修正: [問題 1069](https://github.com/Microsoft/azuredatastudio/issues/1069):接続ダイアログが最初に表示されたときに、サーバーは必須であるというエラーが表示される。
- 修正: [問題 1070](https://github.com/Microsoft/azuredatastudio/issues/1070):サーバー グループを展開するのに、ダブルクリックしなければならなくなった。
- 修正: [問題 1072](https://github.com/Microsoft/azuredatastudio/issues/1072):選択コントロールの背景が半透明である。
- 修正: [問題 1115](https://github.com/Microsoft/azuredatastudio/issues/1115):SQL Operations Studio での、ハイ コントラストによるアクセシビリティの問題をすべて修正する。
- 修正: [問題 1101](https://github.com/Microsoft/azuredatastudio/issues/1101):拡張機能のアップグレードに失敗した際、[手動でダウンロードする] リンクが間違った場所にリンクする。
- 修正: [問題 1103](https://github.com/Microsoft/azuredatastudio/issues/1103):[ホーム] タブで V Scroll が機能していない。
- 修正: [問題 1104](https://github.com/Microsoft/azuredatastudio/issues/1104):SQL 拡張機能のタブが動作しなくなった。

### <a name="visual-studio-code-121-platform"></a>Visual Studio Code 1.21 プラットフォーム

4 月のパブリック プレビューのハイライトは、Visual Studio Code 1.21 プラットフォームのソースコードが更新されている点です。 これにより、以前の 1.19 同期ポイントのコア エディターとワークベンチにいくつかの更新がもたらされます。 例として、次のようなものがあります。

&nbsp;

| Change | 詳細 |
| :----- | :------ |
| [新しい通知 UI](https://code.visualstudio.com/updates/v1_21#_new-notifications-ui)。 | SQL Operations Studio の通知を簡単に管理して確認できます。 |
| [統合型のターミナル分割](https://code.visualstudio.com/updates/v1_21#_split-terminals)。 | 複数の開いている端末を同時に操作できます。 |
| [大きなファイルと保護されたファイルの保存](https://code.visualstudio.com/updates/v1_20#_save-files-that-need-admin-privileges)。 | 管理者権限で保護された \>256M のファイルを SQL Operations Studio 内に保存できます。 |
| [大きなファイルのサポートの改善](https://code.visualstudio.com/updates/v1_21#_text-buffer-improvements)。 | 大きなファイルのテキスト バッファーが最適化されました。 |
| [設定検索の改善](https://code.visualstudio.com/updates/v1_20#_settings-search)。 | 自然言語検索で適切な設定を簡単に見つけることができます。 |
| [グローバル スニペット](https://code.visualstudio.com/updates/v1_20#_global-snippets)。 | すべての種類のファイルで使用できるスニペットを作成できます。 |
| [エクスプローラーでの複数選択](https://code.visualstudio.com/updates/v1_20#_multi-select-in-the-explorer)。 | 一度に複数のファイルに対してアクションを実行できます。 |
| [エクスプローラーでのエラーと警告](https://code.visualstudio.com/updates/v1_20#_error-indicators-in-the-explorer)。 | コード ベースのエラーにすばやく移動できます。 |
| [ウィンドウ間でのドラッグ アンド ドロップとコピー/貼り付け](https://code.visualstudio.com/updates/v1_21#_better-drag-and-drop-support)。 | 開いている SQL Operations Studio ウィンドウ間でファイルを移動できます。 |
| [Git サブモジュールのサポート](https://code.visualstudio.com/updates/v1_20#_git-submodules)。 | 入れ子になった Git リポジトリに対して Git 操作を実行できます。 |
| [ターミナル スクリーン リーダーのサポート](https://code.visualstudio.com/updates/v1_20#_screen-reader-support)。 | 統合ターミナルに、 **[Screen Reader Optimized]\(スクリーン リーダー最適化\)** モードが追加されました。 |
| [中央配置のエディター レイアウト](https://code.visualstudio.com/updates/v1_21#_centered-editor-layout)。 | コード表示画面を最大限に活用できます。 |
| [水平方向の検索結果 (プレビュー)](https://code.visualstudio.com/updates/v1_21#_horizontal-search)。 | 検索結果を水平方向のパネルで表示できるようになりました。 |
| &nbsp; | &nbsp; |

詳細については、[Visual Studio Code の 2 月のリリース ノート](https://code.visualstudio.com/updates/v1_21)と、[Visual Studio Code の 1 月のリリース ノート](https://code.visualstudio.com/updates/v1_20)をご確認ください。

その他の詳細については、「[変更ログ](https://github.com/Microsoft/azuredatastudio/blob/main/CHANGELOG.md)」を参照してください。

## <a name="march-2018"></a>2018 年 3 月

2018 年 3 月 28 日 &nbsp; / &nbsp; バージョン:0.27.3 &nbsp; / &nbsp; パブリック プレビュー

"*3 月のパブリック プレビュー*" では、GitHub での優先度の高い問題に対処しながら、機能拡張のエクスペリエンスを改善することに重点が置かれています。 具体的には、拡張機能マネージャーの有効化、ダッシュボード管理の改善、SQL エージェントとインサイト拡張機能の提供が行われています。 このリリースには、次の機能強化が含まれています。

&nbsp;

| Change | 詳細 |
| :----- | :------ |
| ダッシュボードの拡張性モデルが強化され、タブ付きの分析情報と構成ウィンドウがサポートされました。 | 拡張機能マネージャーを使用すると、拡張機能を簡単に取得できます。<br/><br/>[whoisactive.com](http://www.whoisactive.com) の sp\_whoisactive 用のダッシュボード拡張機能。<br/><br/>詳細については、「[SQL Operations Studio の機能を拡張する](./extensions/add-extensions.md)」を参照してください。 |
| [接続とオブジェクト エクスプローラー管理のための機能拡張 API](https://github.com/Microsoft/azuredatastudio/wiki/Extensibility-API) が追加されました。 | &nbsp; |
| お客様に影響を与える重要な [GitHub の問題](https://github.com/Microsoft/azuredatastudio/issues)が引き続き修正されています。 | &nbsp; |
| &nbsp; | &nbsp; |

## <a name="february-2018"></a>2018 年 2 月

2018 年 2 月 15 日 &nbsp; / &nbsp; バージョン:0.26.7 &nbsp; / &nbsp; パブリック プレビュー

"*2 月のパブリック プレビュー*" には、いくつかの機能の提案と、優先度の高いバグの修正が含まれています。 このリリースには、次の機能強化が含まれています。

&nbsp;

| Change | 詳細 |
| :----- | :------ |
| 自動更新インストールの導入。これにより、新しいリリースがダウンロード可能になったときに通知を受信できます。 | &nbsp; |
| 接続ダイアログの **[データベース]** フィールドに、指定したサーバーから入力されたデータベースの一覧を表示するドロップダウン リストが動的に表示されるようになりました。 | &nbsp; |
| 接続拡張性 API の導入。 | &nbsp; |
| VS Code Editor 1.19 の統合。 | &nbsp; |
| JustinPealing/html-query-plan コンポーネントが更新され、クエリ プラン ビューアーの機能強化が複数追加されました。 | &nbsp; |
| &nbsp; | &nbsp; |

### <a name="fixed-issues-february-2018"></a>修正済みの問題、2018 年 2 月

- 修正: [問題 6](https://github.com/Microsoft/azuredatastudio/issues/6):[新しいクエリ] タブを開くときに、接続と選択したデータベースを保持する。
- 修正: [問題 22](https://github.com/Microsoft/azuredatastudio/issues/22):[サーバー名] と [データベース名] - これらをテキスト ボックスではなくドロップ ダウンにすることはできますか?
- 修正: [問題 549](https://github.com/Microsoft/azuredatastudio/issues/549):Silent/Very Silent でインストールを実行すると、インストール後にアプリケーションが起動する。
- 修正: [問題 481](https://github.com/Microsoft/azuredatastudio/issues/481):[更新プログラムの確認] オプションを追加する。
- SQL Editor の色づけおよびオートコンプリートの修正:
  - 修正: [問題 584](https://github.com/Microsoft/azuredatastudio/issues/584):キーワード "FULL" が IntelliSense によって強調表示されない。
  - 修正: [問題 345](https://github.com/Microsoft/azuredatastudio/issues/345):エディター内で SQL 関数を色分けする。
  - 修正: [問題 300](https://github.com/Microsoft/azuredatastudio/issues/300): [#tempData] の最後の "]" が緑色で表示される。
  - 修正: [問題 225](https://github.com/Microsoft/azuredatastudio/issues/225):キーワードの色が間違っている。
  - 修正: [問題 60](https://github.com/Microsoft/azuredatastudio/issues/60):FROM 句で一時テーブルを使用する際に、SQL 構文が間違った色で強調表示される。

## <a name="january-2018"></a>2018 年 1 月

2018 年 1 月 17 日 &nbsp; / &nbsp; バージョン:0.25.4 &nbsp; / &nbsp; パブリック プレビュー

"*1 月のパブリック プレビュー*" には、いくつかの機能の提案と、優先度の高いバグの修正が含まれています。 このリリースには、次の機能強化が含まれています。

&nbsp;

| Change | 詳細 |
| :----- | :------ |
| 接続ダイアログで、保存されたサーバー接続が使用できるようになりました。 | &nbsp; |
| Hot Exit を有効化できるようになりました。 既定では、Hot Exit は無効になっています。 有効にするには、[Hot Exit の設定](settings.md#hot-exit)に関するセクションを参照してください。 | &nbsp; |
| サーバー グループに基づくタブの色づけ。 タブの色づけは、既定では無効になっています。 有効にするには、[タブの色の設定](settings.md#tab-color)に関するセクションを参照してください。 | &nbsp; |
| 接続ダイアログの *[サーバー名]* が *[サーバー]* に変更されました。 | &nbsp; |
| 壊れていた *[Run Current Query]\(現在のクエリを実行\)*  コマンドが修正されました。 | &nbsp; |
| ドラッグ アンド ドロップによるスクリプト作成のバグが修正されました。 | &nbsp; |
| 間違ってピン留めされていた [スタート メニュー] アイコンが修正されました。 | &nbsp; |
| 不足していた Azure アカウントのブランド化アイコンが修正されました。 | &nbsp; |
| &nbsp; | &nbsp; |

## <a name="december-2017"></a>2017 年 12 月

2017 年 12 月 19 日 &nbsp; / &nbsp; バージョン:0.24.1 &nbsp; / &nbsp; パブリック プレビュー

"*12 月のパブリック プレビュー*" には、すべての機能領域にわたるいくつかのバグ修正と、次の機能強化が含まれています。

&nbsp;

| Change | 詳細 |
| :----- | :------ |
| Azure SQL Database と Azure Synapse Analytics への接続に役立つ [ファイアウォール規則の作成] ダイアログ ボックスを使用できるようになりました。 | &nbsp; |
| Windows セットアップと、Linux DEB および RPM のインストール パッケージが追加されました。 | &nbsp; |
| [ダッシュボードの管理] ビジュアル レイアウト エディター。 | &nbsp; |
| *[ALTER としてスクリプト]* および *[Script As Execute]\(EXECUTE としてスクリプト\)* コマンド。 | &nbsp; |
| *[Run Current Query with Actual Plan]\(現在のクエリを実際のプランで実行\)* コマンド。 | &nbsp; |
| VS Code 1.18.1 エディター プラットフォームの統合。 | &nbsp; |
| VSIX 拡張機能ファイルのサイドローディングの有効化。 | &nbsp; |
| "GO N" バッチ イテレーション構文のサポート。 | &nbsp; |
| &nbsp; | &nbsp; |

## <a name="november-2017"></a>2017 年 11 月

2017 年 11 月 15 日 &nbsp; / &nbsp; バージョン:0.23.6

- Azure Data Studio の最初のリリースです。

## <a name="next-steps"></a>次の手順

作業を開始するには、次のクイック スタートのいずれかを参照してください。

- [SQL Server に対する接続およびクエリ](quickstart-sql-server.md)
- [Azure SQL Database に対する接続およびクエリ](quickstart-sql-database.md)
- [Azure Synapse Analytics に対する接続およびクエリ](quickstart-sql-dw.md)

Azure Data Studio への投稿:

- [https://github.com/Microsoft/azuredatastudio](https://github.com/Microsoft/azuredatastudio)