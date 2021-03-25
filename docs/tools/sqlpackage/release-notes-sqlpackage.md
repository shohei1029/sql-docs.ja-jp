---
title: DacFx と SqlPackage のリリース ノート
description: Microsoft sqlpackage のリリース ノート。
ms.custom: tools|sos
ms.date: 03/10/2021
ms.prod: sql
ms.reviewer: llali; sstein
ms.prod_service: sql-tools
ms.topic: conceptual
author: dzsquared
ms.author: drskwier
ms.openlocfilehash: ef52bf5f98b948312dcc36acfd438cd9281b25dd
ms.sourcegitcommit: c09ef164007879a904a376eb508004985ba06cf0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104890682"
---
# <a name="release-notes-for-sqlpackageexe"></a>SqlPackage.exe のリリース ノート

**[最新バージョンのダウンロード](sqlpackage-download.md)**

この記事では、SqlPackage.exe のリリースされたバージョンによって提供される機能と修正プログラムを示します。

## <a name="187-sqlpackage"></a>18.7 sqlpackage

|プラットフォーム|ダウンロード|リリース日|Version|Build
|:---|:---|:---|:---|:---|
|Windows|[MSI インストーラー](https://go.microsoft.com/fwlink/?linkid=2157201)|2021 年 3 月 10 日|18.7|15.0.5084.2|
|macOS .NET Core |[zip ファイル](https://go.microsoft.com/fwlink/?linkid=2157203)|2021 年 3 月 10 日| 18.7|15.0.5084.2|
|Linux .NET Core |[zip ファイル](https://go.microsoft.com/fwlink/?linkid=2157202)|2021 年 3 月 10 日| 18.7|15.0.5084.2|
|Windows .NET Core |[zip ファイル](https://go.microsoft.com/fwlink/?linkid=2157302)|2021 年 3 月 10 日| 18.7|15.0.5084.2|

### <a name="features"></a>特徴
| 機能 | 詳細 |
| :------ | :------ |
| デプロイ | Azure Storage との間でビッグ データを抽出/公開します。 詳細については、[ビッグ データ用の SqlPackage](sqlpackage-for-azure-synapse-analytics.md) に関する記事を参照してください |
| Azure Synapse Analytics | 行レベルのセキュリティのサポート (インライン テーブル値関数、セキュリティ ポリシー、セキュリティ述語)  |
| Azure Synapse Analytics | ワークロード分類のサポート |
| Azure SQL Edge | 外部ストリーミング ジョブのサポート |
| Azure SQL Edge | データ保持のためのテーブルとデータベースのオプションが追加されました。 |
| [インポート] | インポート操作のための 2 つの新しいインデックス オプション プロパティが追加されました。 *DisableIndexesForDataPhase* (データを SQL Server にインポートする前にインデックスを無効にする。既定は true) と *RebuildIndexesOfflineForDataPhase* (データを SQL Server にインポートした後にオフラインでインデックスをリビルドする。既定は false) |
| ログ記録 | ログ メッセージ内ですべてのオブジェクト名をハッシュ文字列に変換するすべての操作にプロパティが追加されました (HashObjectNamesInLogs)。 |
| パフォーマンス | インポートとエクスポートのパフォーマンスの向上。たとえば、追加のログはボトルネックの増加の特定に役立ちます。 |
| sqlcmd | デプロイと Schema Compare に SQLCMD 変数を値に置き換えるかどうかを指定するプロパティが追加されました (DoNotEvaluateSqlCmdVariables)。 |



### <a name="fixes"></a>修正
| 機能 | 詳細 |
| :------ | :------ | 
| デプロイ | [Azure SQL](https://techcommunity.microsoft.com/t5/azure-sql/changing-default-maxdop-in-azure-sql-database/ba-p/1538528) の既定の MAXDOP が 0 から 8 に変更され、DacFx でスキーマ モデルの既定値が更新されます | 
| スキーマ比較 | OUT と OUTPUT のキーワードを使用したストアド プロシージャは、差分として無視されます |
| デプロイ | ビッグ データ トークンの追加検証 |
| ビルド/配置 | 最終的に dacpac を整合させるための、一時外部テーブルの完全スキーマ モデル クリーンアップ。  |
| ビルド/配置 | エラー処理と Edge 150 RE 以外の修正が追加されました。 |
| インポート/デプロイ | デプロイ中に復元されるシーケンス値 |
| デプロイ | クラスター化インデックスの圧縮オプションを変更すると、alter index ではなくテーブルが再作成される問題を修正しました。 |
| デプロイ | テーブル列が変更された場合に、クラスター化列ストア インデックスが削除され、再作成される問題を修正しました。 |
| デプロイ | 外部ユーザーがデプロイ中に削除され、再作成される問題を修正しました。 |
| スキーマ比較 | 外部ストリーミング ジョブでの Schema Compare の問題を修正しました。 |
| [インポート] | デプロイ レポートのアンビエント設定 ReliableDdlEnabled スクリプトを有効にすると、Null 参照の例外が発生しました。|
| デプロイ | システムのバージョン管理を含むデプロイ ステップが間違った順序で作成される問題を修正しました。 |
| デプロイ | テンポラル テーブルを含むターゲットが原因で Schema Compare の更新や dacpac のデプロイが失敗する問題を修正しました。 |
| デプロイ | ターゲットの前回の値に基づいてデプロイ後に ID 値を再シードします。 |

### <a name="known-issues"></a>既知の問題
| 機能 | 詳細 | 回避策 |
| :------ | :------ |:------ |
| デプロイ | Azure Synapse Analytics ワークロード管理機能 (ワークロード グループとワークロード分類子) はまだサポートされていません | 該当なし |
| デプロイ | 増分配置シナリオで、ユーザーが一時テーブルを削除すると共に、それに依存するオブジェクト (関数やストアドプロシージャなど) を削除すると、配置が失敗することがあります。 スクリプト生成の順序として、テーブル上で SYSTEM_VERSIONING をオフにする試みが行われます (これはテーブルを削除するための前提条件)。しかし、生成されるステップの順序が正しくありません。 [作業項目:](https://github.com/microsoft/azuredatastudio/issues/14655) | 配置スクリプトを生成し、System_Versioning OFF ステップを、削除するテーブルの直前に移動してから、スクリプトを実行します。 |

## <a name="186-sqlpackage"></a>18.6 sqlpackage

|プラットフォーム|ダウンロード|リリース日|Version|Build
|:---|:---|:---|:---|:---|
|Windows|[MSI インストーラー](https://go.microsoft.com/fwlink/?linkid=2143544)|2020 年 9 月 18 日|18.6|15.0.4897.1|
|macOS .NET Core |[zip ファイル](https://go.microsoft.com/fwlink/?linkid=2143659)|2020 年 9 月 18 日| 18.6|15.0.4897.1|
|Linux .NET Core |[zip ファイル](https://go.microsoft.com/fwlink/?linkid=2143497)|2020 年 9 月 18 日| 18.6|15.0.4897.1|
|Windows .NET Core |[zip ファイル](https://go.microsoft.com/fwlink/?linkid=2143496)|2020 年 9 月 18 日| 18.6|15.0.4897.1|

### <a name="features"></a>特徴
| 機能 | 詳細 |
| :------ | :------ |
| プラットフォーム | .NET Core バージョン用の sqlpackage を .NET Core 3.1 に更新しました |
| Always Encrypted | SQL Server 2019 向けセキュア エンクレーブ インポートおよびエクスポートのサポートが追加されました |
| デプロイ | Azure SQL Database からエクスポートするとき、変更データ キャプチャ対応テーブルを無視するサポートが追加されました |
| デプロイ | Azure SQL Database のインデックス オプション OPTIMIZE_FOR_SEQUENTIAL_KEY 向けサポートが追加されました |
| デプロイ | Azure Synapse Analytics の ID 列のサポートが追加されました | 
| ヘルプ | ヘルプ (/?) で sqlpackage バージョンを出力し、/version パラメーターをサポートします | 

### <a name="fixes"></a>修正
| 機能 | 詳細 |
| :------ | :------ | 
| デプロイ | Azure SQL Managed Instance を非 sysadmin ユーザーとしてターゲットにする場合に正しくないデプロイ スクリプトが生成される問題を解消しました  | 
| デプロイ | スクリプト アクションを実行するとき、デプロイの共同作成者を読み込む問題を解消しました | 
| ヘルプ | 操作に 1 日以上かかるとき、sqlpackage に正しい経過時間を出力します | 
| デプロイ | .NET Core に対してデプロイするときの dacpac 登録の問題を解消しました | 
| デプロイ | /accessToken (/at) パラメーターの .NET Core 処理の sqlpackage を修正しました | 
| デプロイ | 最上位ではないステートメントとしてストアド プロシージャで ALTER TABLE ステートメントを許可します | 
| デプロイ | 具体化されたビューの Azure Synapse Analytics 検証で大文字と小文字を区別しないように修正しました | 

### <a name="known-issues"></a>既知の問題
| 機能 | 詳細 |
| :------ | :------ |
| デプロイ | Azure Synapse Analytics ワークロード管理機能 (ワークロード グループとワークロード分類子) はまだサポートされていません | 

## <a name="1851-sqlpackage"></a>18.5.1 sqlpackage

|プラットフォーム|ダウンロード|リリース日|Version|Build
|:---|:---|:---|:---|:---|
|Windows|[MSI インストーラー](https://go.microsoft.com/fwlink/?linkid=2134206)|2020 年 6 月 24 日|18.5.1|15.0.4826.1|
|macOS .NET Core |[zip ファイル](https://go.microsoft.com/fwlink/?linkid=2134312)|2020 年 6 月 24 日| 18.5.1|15.0.4826.1|
|Linux .NET Core |[zip ファイル](https://go.microsoft.com/fwlink/?linkid=2134311)|2020 年 6 月 24 日| 18.5.1|15.0.4826.1|
|Windows .NET Core |[zip ファイル](https://go.microsoft.com/fwlink/?linkid=2134310)|2020 年 6 月 24 日| 18.5.1|15.0.4826.1|

### <a name="fixes"></a>修正
| 機能 | 詳細 |
| :------ | :------ |
| デプロイ | dacpac をデプロイするとき、またはオンプレミスへの外部ログインを持つユーザーで bacpac をインポートするときに、"'type' 付近に不適切な構文があります" エラーが発生する、18.5 で導入された回帰を修正しました | 

## <a name="185-sqlpackage"></a>18.5 sqlpackage

|プラットフォーム|ダウンロード|リリース日|Version|Build
|:---|:---|:---|:---|:---|
|Windows|[MSI インストーラー](https://go.microsoft.com/fwlink/?linkid=2128142)|2020 年 4 月 28 日|18.5|15.0.4769.1|
|macOS .NET Core |[zip ファイル](https://go.microsoft.com/fwlink/?linkid=2128145)|2020 年 4 月 28 日| 18.5|15.0.4769.1|
|Linux .NET Core |[zip ファイル](https://go.microsoft.com/fwlink/?linkid=2128144)|2020 年 4 月 28 日| 18.5|15.0.4769.1|
|Windows .NET Core |[zip ファイル](https://go.microsoft.com/fwlink/?linkid=2128143)|2020 年 4 月 28 日| 18.5|15.0.4769.1|

### <a name="features"></a>特徴
| 機能 | 詳細 |
| :------ | :------ |
| デプロイ | SQL Server 2008 以降、Azure SQL Database、Azure Synapse Analytics で、データの秘密度の分類がサポートされるようになりました |
| デプロイ | Azure Synapse Analytics でのテーブル制約のサポートを追加しました |
| デプロイ | Azure Synapse Analytics での順序付けされたクラスター化列ストア インデックスのサポートを追加しました |
| デプロイ | 外部データ ソース (Oracle、Teradata、MongoDB/CosmosDB、ODBC、ビッグ データ クラスター用) と SQL Server 2019 ビッグ データ クラスター用の外部テーブルのサポートを追加しました。 |
| デプロイ | サポートされるエディションとして SQL Database Edge インスタンスを追加しました。 |
| デプロイ | '\<server>.\<dnszone>.database.windows.net' 形式の Managed Instance サーバー名をサポートします |
| デプロイ | Azure Synapse Analytics でのコピー コマンドのサポートを追加しました |
| デプロイ | Azure Synapse Analytics でテーブルのパーティション関数に変更がある場合に、テーブルの再作成を回避するために、公開時のデプロイ オプション 'IgnoreTablePartitionOptions' を追加しました |
| .NET Core | sqlpackage の .NET Core バージョンでの Microsoft.Data.SqlClient のサポートを追加しました。 |
| &nbsp; | &nbsp; |

### <a name="fixes"></a>修正
| Fix | 詳細 |
| :-- | :------ |
| デプロイ | JSON パスの式としての解析を修正しました。 |
| デプロイ | AlterAnyDatabaseScopedConfiguration および AlterAnySensitivityClassification 権限の GRANT ステートメントの生成を修正しました。 |
| デプロイ | 認識されない External Script 権限を修正しました。 |
| デプロイ | インライン プロパティの修正 - プロパティの暗黙的な追加では、違いを示す必要はありませんが、明示的な言及ではスクリプトを通じて示す必要があります。 |
| デプロイ | 具体化されたビュー (MV) によって参照されるテーブルを変更すると、Azure Synapse Analytics の MV でサポートされていない ALTER VIEW ステートメントが生成される問題を解決しました |
| デプロイ | Azure Synapse Analytics のデータが含まれるテーブルに列を追加した場合の公開の失敗を修正しました |
| デプロイ | Azure Synapse Analytics のディストリビューション列の種類 (データ損失のシナリオ) を変更した場合に、更新スクリプトでデータを新しいテーブルに移動する必要があるという問題を修正しました |
| ScriptDom | インライン インデックスの後に定義されたインライン制約を認識できない ScriptDom のバグを修正しました。 |
| ScriptDom | ScriptDom で、バッチ ステートメントで SYSTEM_TIME の終わりかっこがない ScriptDom のバグを修正しました。 |
| Always Encrypted | 接続が無効になったときに一時テーブルがなくなるため、sqlpackage を再接続したときに一時テーブルが既にない場合、#tmpErrors テーブルを削除できない問題を修正しました。 |
| &nbsp; | &nbsp; |

### <a name="known-issues"></a>既知の問題
| 機能 | 詳細 |
| :------ | :------ |
| デプロイ |  18.5 で導入された回帰により、dacpac をデプロイするとき、またはオンプレミスへの外部ログインを持つユーザーで bacpac をインポートするときに、"'type' 付近に不適切な構文があります" エラーが発生する。 回避策は、sqlpackage 18.4 を使用することです。これは、次の sqlpackage リリースで修正される予定です。 | 
| .NET Core | Microsoft.Data.SqlClient のこの[既知の問題](https://github.com/dotnet/SqlClient/issues/559)が原因で、機密分類を使用した bacpac のインポートが "内部接続の致命的なエラーです" で失敗します。 これは次の sqlpackage リリースで修正される予定です。 |
| &nbsp; | &nbsp; |

## <a name="1841-sqlpackage"></a>18.4.1 sqlpackage

|プラットフォーム|ダウンロード|リリース日|Version|Build
|:---|:---|:---|:---|:---|
|Windows|[MSI インストーラー](https://go.microsoft.com/fwlink/?linkid=2113703)|2019 年 12 月 13 日|18.4.1|15.0.4630.1|
|macOS .NET Core |[zip ファイル](https://go.microsoft.com/fwlink/?linkid=2113705)|2019 年 12 月 13 日| 18.4.1|15.0.4630.1|
|Linux .NET Core |[zip ファイル](https://go.microsoft.com/fwlink/?linkid=2113331)|2019 年 12 月 13 日| 18.4.1|15.0.4630.1|
|Windows .NET Core |[zip ファイル](https://go.microsoft.com/fwlink/?linkid=2113704)|2019 年 12 月 13 日| 18.4.1|15.0.4630.1|

### <a name="fixes"></a>修正
| Fix | 詳細 |
| :-- | :------ |
| ScriptDom |  18.3.1 で ScriptDom の解析回帰が導入されましたが、"RENAME" が最上位のトークンとして不適切に処理されるため、解析が失敗します。
| &nbsp; | &nbsp; |

### <a name="known-issues"></a>既知の問題 

| 機能 | 詳細 |
| :------ | :------ |
| デプロイ |  18.4.1 で導入された回帰が原因で、"オブジェクト参照がオブジェクトのインスタンスに設定されていません" という エラーが発生します。これは、dacpac をデプロイするとき、または外部ログインを持つユーザーで bacpac をインポートするときに発生します。 回避策は、sqlpackage 18.4 を使用することです。これは、次の sqlpackage リリースで修正される予定です。 | 
| &nbsp; | &nbsp; |

## <a name="184-sqlpackage"></a>18.4 sqlpackage

|プラットフォーム|ダウンロード|リリース日|Version|Build
|:---|:---|:---|:---|:---|
|Windows|[MSI インストーラー](https://go.microsoft.com/fwlink/?linkid=2108813)|2019 年 10 月 29 日|18.4|15.0.4573.2|
|macOS .NET Core |[zip ファイル](https://go.microsoft.com/fwlink/?linkid=2108815)|2019 年 10 月 29 日| 18.4|15.0.4573.2|
|Linux .NET Core |[zip ファイル](https://go.microsoft.com/fwlink/?linkid=2108814)|2019 年 10 月 29 日| 18.4|15.0.4573.2|
|Windows .NET Core |[zip ファイル](https://go.microsoft.com/fwlink/?linkid=2109019)|2019 年 10 月 29 日| 18.4|15.0.4573.2|

### <a name="features"></a>特徴

| 機能 | 詳細 |
| :------ | :------ |
| デプロイ | Azure Synapse Analytics (GA) に配置できるようにサポートを追加しました。 | 
| プラットフォーム | macOS、Linux、および Windows 用の sqlpackage .NET Core GA。 | 
| セキュリティ | SHA1 コード署名を削除しました。 |
| デプロイ | 新たに次の Azure データベース エディションのサポートを追加しました: GeneralPurpose、BusinessCritical、および Hyperscale |
| デプロイ | Azure Active Directory ユーザーとグループに対する Managed Instance のサポートを追加しました。 |
| デプロイ | .NET Core で sqlpackage の /AccessToken パラメーターをサポートします。 |
| &nbsp; | &nbsp; |

### <a name="known-issues"></a>既知の問題 

| 機能 | 詳細 |
| :------ | :------ |
| ScriptDom |  18.3.1 で ScriptDom の解析回帰が導入されましたが、"RENAME" が最上位のトークンとして不適切に処理されるため、解析が失敗します。 これは次の sqlpackage リリースで修正される予定です。 | 
| &nbsp; | &nbsp; |

### <a name="known-issues-for-net-core"></a>.NET Core に関する既知の問題

| 機能 | 詳細 |
| :------ | :------ |
| [インポート] |  圧縮ファイルのサイズが 4 GB を超える bacpac ファイルでは、インポートを実行する際に .NET Core バージョンの sqlpackage を使用することが必要になる場合があります。  これは、.NET Core が ZIP ヘッダーを生成する方法に起因します。ZIP ヘッダーが有効でも、.NET Framework バージョンの sqlpackage では読み取ることができないためです。 | 
| デプロイ | /p: Storage=File パラメーターはサポートされていません。 .NET Core では、Memory のみがサポートされています。 | 
| Always Encrypted | sqlpackage .NET Core では、Always Encrypted 列はサポートされていません。 | 
| Security | sqlpackage .NET Core では、多要素認証用の /ua パラメーターはサポートされていません。 | 
| デプロイ | json データによるシリアル化を使用している古い V2 .dacpac ファイルと .bacpac ファイルがサポートされません。 |
| &nbsp; | &nbsp; |

## <a name="1831-sqlpackage"></a>18.3.1 sqlpackage

|プラットフォーム|ダウンロード|リリース日|Version|Build
|:---|:---|:---|:---|:---|
|Windows|[MSI インストーラー](https://go.microsoft.com/fwlink/?linkid=2102893)|2019 年 9 月 13 日|18.3.1|15.0.4538.1|
|macOS .NET Core (プレビュー)|[zip ファイル](https://go.microsoft.com/fwlink/?linkid=2102894)|2019 年 9 月 13 日| 18.3.1|15.0.4538.1|
|Linux .NET Core (プレビュー)|[zip ファイル](https://go.microsoft.com/fwlink/?linkid=2102978)|2019 年 9 月 13 日| 18.3.1|15.0.4538.1|
|Windows .NET Core (プレビュー)|[zip ファイル](https://go.microsoft.com/fwlink/?linkid=2102979)|2019 年 9 月 13 日| 18.13.1|15.0.4538.1|

### <a name="features"></a>特徴

| 機能 | 詳細 |
| :------ | :------ |
| デプロイ | Azure Synapse Analytics (プレビュー) に配置できるようにサポートを追加しました。 | 
| デプロイ | /p:DatabaseLockTimeout=(INT32 '60') パラメーターを sqlpackage に追加しました。 | 
| デプロイ | /p:LongRunningCommandTimeout=(INT32) パラメーターを sqlpackage に追加しました。 |
| エクスポートおよび抽出 | /p:TempDirectoryForTableData=(STRING) パラメーターを sqlpackage に追加しました。 |
| デプロイ | 配置の共同作成者を他の場所から読み込むことができるようになりました。 配置の共同作成者は、配置されているターゲット .dacpac と同じディレクトリ、sqlpackage .exe バイナリを基準とした Extensions ディレクトリ、および他のディレクトリの場所を指定できる、sqlpackage に追加された /p:AdditionalDeploymentContributorPaths=(STRING) パラメーターから読み込まれます。 |
| デプロイ | OPTIMIZE_FOR_SEQUENTIAL_KEY のサポートを追加しました。 |
| &nbsp; | &nbsp; |

### <a name="fixes"></a>修正

| Fix | 詳細 |
| :-- | :------ |
| デプロイ | 自動インデックスが配置時に削除されないよう、自動インデックスを無視するように修正しました。 | 
| Always Encrypted | Always Encrypted の varchar 列の処理を修正しました。 | 
| ビルド/配置 | xml 列セットの nodes () メソッドが解決されるように修正しました。| 
| ScriptDom | "URL" 文字列が最上位レベルのトークンとして解釈されていたその他のケースが修正されました。 | 
| グラフ | 制約内の擬似列参照に対して生成された TSQL を修正しました。  | 
| エクスポート | 複雑さの要件を満たすランダムなパスワードが生成されるようになりました。 | 
| デプロイ | 制約を取得するときにコマンド タイムアウトに従うように修正しました。 | 
| .NET Core (プレビュー) | ファイルへの診断ログの記録を修正しました。 | 
| .NET Core (プレビュー) | ストリーミングを使用してテーブル データをエクスポートすることで、大きなテーブルがサポートされるようになりました。 | 
| &nbsp; | &nbsp; |

## <a name="182-sqlpackage"></a>18.2 sqlpackage

|プラットフォーム|ダウンロード|リリース日|Version|Build
|:---|:---|:---|:---|:---|
|Windows|[MSI インストーラー](https://go.microsoft.com/fwlink/?linkid=2087429)|2019 年 4 月 15 日|18.2|15.0.4384.2|
|macOS .NET Core (プレビュー)|[zip ファイル](https://go.microsoft.com/fwlink/?linkid=2087247)|2019 年 4 月 15 日 | 18.2 |15.0.4384.2|
|Linux .NET Core (プレビュー)|[zip ファイル](https://go.microsoft.com/fwlink/?linkid=2087431)|2019 年 4 月 15 日 | 18.2 |15.0.4384.2|

### <a name="features"></a>特徴

| 機能 | 詳細 |
| :------ | :------ |
| グラフ | グラフ テーブルのエッジ制約とエッジ制約句のサポートが追加されました。 |
| デプロイ | SQL Server 2016 以降のインデックス キーで 32 列をサポートするためのモデルの検証規則が有効になりました。 |
| &nbsp; | &nbsp; |

### <a name="fixes"></a>修正

| Fix | 詳細 |
| :-- | :------ |
| デプロイ | サポートされていないクエリ ヒントの使用に起因する SQL Server 2016 RTM データベースのリバース エンジニア リングが修正されました。 |
| デプロイ | filegroup ステートメントの作成前に発生するように auto close alter ステートメントの配置順序が修正されました。 |
| ScriptDom | 'URL' 文字列が最上位レベルのトークンとして解釈されていた ScriptDom の回帰解析が修正されました。 |
| デプロイ | alter table add index ステートメントの解析時の null 参照例外が修正されました。 | 
| スキーマ比較 | null 許容の保存される計算列でのスキーマ比較で、常に一致しないとなっていた結果が修正されました。|
| &nbsp; | &nbsp; |

## <a name="181-sqlpackage"></a>18.1 sqlpackage

リリース日: &nbsp;2019 年 2 月 1 日  
ビルド: &nbsp; 15.0.4316.1  
プレビュー リリース。

### <a name="features"></a>特徴

| 機能 | 詳細 |
| :------ | :------ |
| デプロイ | UTF8 照合順序のサポートが追加されました。 |
| デプロイ | インデックス付きビューでの非クラスター化列ストア インデックスが有効になりました。 |
| プラットフォーム | .NET Core 2.2 に移動しました。 | 
| スキーマ比較 | .NET Core でのスキーマ比較でメモリ ストレージが使用されます。 |
| &nbsp; | &nbsp; |

### <a name="fixes"></a>修正

| Fix | 詳細 |
| :-- | :------ |
| パフォーマンス | リバース エンジニアリング クエリでのレガシ カーディナリティ推定機能を使用するためのパフォーマンスが改善されました。 | 
| パフォーマンス | スクリプトを生成する際のスキーマ比較での顕著なパフォーマンス問題が修正されました。 | 
| スキーマ比較 | 特定の拡張イベント (xevent) セッションを無視するスキーマのドリフト検出ロジックが修正されました。 |
| グラフ | グラフ テーブルのインポート順序が修正されました。 | 
| エクスポート | オブジェクトのアクセス許可による外部テーブルのエクスポートが修正されました。 |
| &nbsp; | &nbsp; |

### <a name="known-issues"></a>既知の問題

このリリースには、.NET Core 2.2 をターゲットとする sqlpackage のクロスプラットフォーム プレビュー ビルドが含まれています。 sqlpackage は、macOS および Linux で実行できます。

| 既知の問題 | 詳細 |
| :---------- | :------ |
| デプロイ | .NET Core では、ビルド コントリビューターと配置コントリビューターがサポートされません。 | 
| デプロイ | .NET Core では、json データによるシリアル化を使用している古い .dacpac ファイルと .bacpac ファイルがサポートされません。 | 
| デプロイ | .NET Core では、大文字と小文字が区別されるファイル システムの問題により、参照された .dacpacs (master.dacpac など) が解決されない場合があります。 回避策は、参照ファイルの名前をすべて大文字にすることです (例: MASTER.BACPAC)。 |
| &nbsp; | &nbsp; |

## <a name="180-sqlpackage"></a>18.0 sqlpackage

リリース日: &nbsp;2018 年 10 月 24 日  
ビルド: &nbsp; 15.0.4200.1

### <a name="features"></a>特徴

| 機能 | 詳細 |
| :------ | :------ |
| デプロイ | データベース互換性レベル 150 のサポートが追加されました。 | 
| デプロイ | マネージ インスタンスのサポートが追加されました。 | 
| パフォーマンス | データベース操作の並列処理の程度を指定する MaxParallelism コマンドライン パラメーターが追加されました。 | 
| Security | SQL Server に接続するときの認証トークンを指定する AccessToken コマンドライン パラメーターが追加されました。 | 
| [インポート] | インポートでの BLOB/CLOB データ型のストリーミングのサポートが追加されました。 | 
| デプロイ | スカラー UDF 'INLINE' オプションのサポートが追加されました。 | 
| グラフ | グラフ テーブルの 'MERGE' 構文のサポートが追加されました。 |
| &nbsp; | &nbsp; |

### <a name="fixes"></a>修正

| Fix | 詳細 |
| :-- | :------ |
| グラフ | グラフ テーブルの解決されない擬似列が修正されました。 |
| デプロイ | メモリ最適化テーブル使用時のメモリ最適化されたファイル グループによるデータベースの作成が修正されました。 |
| デプロイ | 外部テーブルでの拡張プロパティの包含が修正されました。 |
| &nbsp; | &nbsp; |

## <a name="178-sqlpackage"></a>17.8 sqlpackage

リリース日: &nbsp;2018 年 6 月 22 日  
ビルド: &nbsp; 14.0.4079.2

### <a name="features"></a>特徴

| 機能 | 詳細 |
| :------ | :------ |
| 診断 | SqlClient の例外メッセージを含めて、接続エラーのエラー メッセージが改良されました。 |
| デプロイ | 単一パーティション インデックスのインポート/エクスポートで、インデックスの圧縮がサポートされます。 |
| &nbsp; | &nbsp; |

### <a name="fixes"></a>修正

| Fix | 詳細 |
| :-- | :------ |
| デプロイ | SQL 2017 以降での XML 列セットのリバース エンジニアリング問題が修正されました。 | 
| デプロイ | データベース互換性レベル 140 のスクリプトが Azure SQL Database で無視される問題が修正されました。 |
| &nbsp; | &nbsp; |

## <a name="1741-sqlpackage"></a>17.4.1 sqlpackage

リリース日: &nbsp;2018 年 1 月 25 日  
ビルド: &nbsp; 14.0.3917.1

### <a name="features"></a>特徴

| 機能 | 詳細 |
| :------ | :------ |
| インポート/エクスポート | 入れ子になったステートメントの数が多い Transact-SQL を解析するための ThreadMaxStackSize コマンドライン パラメーターが追加されました。 |
| デプロイ | データベース カタログ照合順序のサポート。 | 
| &nbsp; | &nbsp; |

### <a name="fixes"></a>修正

| Fix | 詳細 |
| :-- | :------ |
| [インポート] | Azure SQL Database の .bacpac をオンプレミスのインスタンスにインポートする際の、"_パスワードのないデータベース マスター キーは、このバージョンの SQL Server ではサポートされていません_" に起因するエラーが修正されました。 |
| グラフ | グラフ テーブルの解決されない擬似列エラーが修正されました。 |
| スキーマ比較 | スキーマを比較するための SQL 認証を修正しました。 | 
| &nbsp; | &nbsp; |

## <a name="1740-sqlpackage"></a>17.4.0 sqlpackage

リリース日: &nbsp;2017 年 12 月 12 日  
ビルド: &nbsp; 14.0.3881.1

### <a name="features"></a>特徴

| 機能 | 詳細 |
| :------ | :------ |
| デプロイ |  SQL 2017 以降と Azure SQL Database での _テンポラル保持ポリシー_ のサポートが追加されました。 | 
| 診断 | 診断情報を保存するファイル パスを指定する /DiagnosticsFile:"C:\Temp\sqlpackage.log" コマンドライン パラメーターが追加されました。 | 
| 診断 | 診断情報をコンソールにログ記録する /Diagnostics コマンドライン パラメーターが追加されました。 |
| &nbsp; | &nbsp; |

### <a name="fixes"></a>修正

| Fix | 詳細 |
| :-- | :------ |
| デプロイ | 認識できないデータベース互換性レベルに遭遇してもブロックは行われません。 代わりに、最新の Azure SQL Database またはオンプレミスのプラットフォームであるとみなされます。 |
| &nbsp; | &nbsp; |
