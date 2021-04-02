---
title: SSMA for Oracle の新機能 (OracleToSQL) |Microsoft Docs
description: 各リリースの SQL Server Migration Assistant (SSMA) for Oracle (OracleToSQL) の変更点を確認します。
author: nahk-ivanov
ms.prod: sql
ms.custom: ''
ms.date: 12/17/2020
ms.reviewer: ''
ms.technology: ssma
ms.topic: conceptual
ms.assetid: f305ebb6-7393-4a43-abb3-6332b739d690
ms.author: alexiva
ms.openlocfilehash: 46d3a03a219ed10aa38f3e65d31d72faa4c224d7
ms.sourcegitcommit: 0b37eb7aef2f358f80867cd13830dd6683da8d85
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105981078"
---
# <a name="whats-new-in-ssma-for-oracle-oracletosql"></a>SSMA for Oracle の新機能 (OracleToSQL)

この記事では、各リリースでの Oracle の変更の SQL Server Migration Assistant (SSMA) を示します。

## <a name="ssma-v818"></a>SSMA v 8.18

SSMA for Oracle の v 8.18 リリースには、次の変更が含まれています。

* Oracle 12c Release 1 のシーケンスの読み込みを修正する (12.1)

## <a name="ssma-v817"></a>SSMA v 8.17

SSMA for Oracle の v 8.17 リリースには、次の変更が含まれています。

* 計算列のサポートを追加する
* 式でカルチャ固有の演算子文字が使用されている場合にパーサーの問題を修正する
* 最新のエディターを使用して SQL テキストを表示するように HTML 評価レポートを更新する

## <a name="ssma-v816"></a>SSMA v 8.16

SSMA for Oracle の v 8.16 リリースには、次の変更が含まれています。

* Id 列のサポートを追加する
* レガシパーサーのサポートを削除する
* データベースから更新されていないオブジェクトの問題を修正します

## <a name="ssma-v815"></a>SSMA v 8.15

SSMA for Oracle の v 8.15 リリースには、いくつかのユーザー補助機能の強化に加えて、次の変更が含まれています。

* `%type`属性と属性の完全な型指定を使用する設定を追加します `%rowtype`
* 改良評価レポートを最新のブラウザーで動作させる
* Azure AD 認証にデータベースによって提供される権限を使用する
* ファイルから読み込まれたステートメントの名前付けを改善する

## <a name="ssma-v814"></a>SSMA v 8.14

SSMA for Oracle のリリースには、障碍のある方にとってアクセシビリティを向上させるために、次のような変更が加えられています。

* ソース/ターゲットサーバーの完全なバージョンをプロジェクトのメタデータに格納します (プロジェクトのアップグレードが必要)
* 可能な場合は、オブジェクト検出に DBA データ辞書を使用する
* 複数のパーサーの問題 ( `PIVOT` / `UNPIVOT` 、 `MERGE` 、代替引用符) を修正します。
* `INSERTING` / `DELETING` / `UPDATING` トリガー内の特殊な関数の変換の修正

## <a name="ssma-v813"></a>SSMA v 8.13

SSMA for Oracle の v 8.13 リリースには、次の変更が含まれています。

* `SQLCODE` `SQLERRM` ローカルプロシージャでのおよび特別な関数の変換を修正します。
* プロシージャと関数の呼び出しを変換するときに、暗黙的な型キャストを検討します。
* 接続の問題のトラブルシューティングに役立つソース接続文字列のログ記録の向上

## <a name="ssma-v812"></a>SSMA v 8.12

SSMA for Oracle の v1.0 リリースには、次の変更が含まれています。

* `INSERT` / `UPDATE` / `MERGE` / `DELETE` ステートメント内でのサブクエリのファクタリング句のサポート
* `ON DELETE SET NULL`複数パスまたは循環参照の場合の句の変換メッセージ
* 動的 SQL 文字列から作成されたカーソルの変換の改善
* ODP.NET を v 19.8 に更新する

## <a name="ssma-v811"></a>SSMA v 8.11

SSMA for Oracle の v 8.11 リリースには、次の変更が含まれています。

* ステートメントでのサブクエリのサポート `INSERT ... VALUES`
* ステートメントの変換の改善 `COMMIT`
* 句の変換でバグを修正 `CONNECT BY LEVEL`
* パーサーエラー復旧ロジックを最長一致にするように更新しました
* MSAL.NET library を使用して対話型 Azure Active Directory 認証を行う

## <a name="ssma-v810"></a>SSMA v 8.10

SSMA for Oracle の v1.0 リリースには、次のような変更に加えて、わずかなパフォーマンスの向上と、以下の変更が含まれています。

* インデックスが構成されたテーブルに関するテスト担当者の問題の修正
* 拡張機能パックの拡張ストアドプロシージャの名前の修正

## <a name="ssma-v89"></a>SSMA v 8.9

SSMA for Oracle の v1.0 リリースには、次の変更が含まれています。

* 動的 SQL 文字列リテラルの変換
* `LAG`、、 `FIRST_VALUE` および `LAST_VALUE` 分析関数の変換
* 基本 `ALTER TRIGGER` / `ALTER INDEX` DDL (有効/無効など) のサポートを追加します。
* 組み込み関数名に一致する列の変換の改善
* 列に対してフィルター選択された一意のインデックスを生成する `NULL`
* Azure Synapse Analytics の変数宣言変換の機能強化
* プロジェクト名に特殊文字が含まれている問題の修正

## <a name="ssma-v88"></a>SSMA v 8.8

SSMA for Oracle の v1.0 リリースには次のものが含まれます。

* SQL Server オブジェクトの同期の安定性の向上
* 評価および変換中の GUI パフォーマンスの向上
* 分析句の変換の改善 `OVER PARTITION`
* 分析関数の新しい変換 `LEAD`
* サブクエリのファクタリング句の新しい変換
* `REPLICATE`Azure Synapse Analytics の新しいディストリビューションオプション
* 変換のパフォーマンスをさらに向上させるための新しい Oracle 構文パーサー

## <a name="ssma-v87"></a>SSMA v 8.7

SSMA for Oracle のバージョン8.7 リリースでは、グラフィカルユーザーインターフェイスが多少修正され、パフォーマンスが向上しています。

さらに、SSMA for Oracle では、[オブジェクトの選択] ダイアログボックスで、有効状態に基づいてオブジェクトをフィルター処理できます。

> [!IMPORTANT]
> SSMA v1.0 以降では、.NET 4.7.2 はインストールの前提条件です。 このバージョンをインストールする必要がある場合は、 [ここ](https://dotnet.microsoft.com/download/dotnet-framework/net472)からランタイムファイルをダウンロードできます。

## <a name="ssma-v86"></a>SSMA v 8.6

ユーザビリティとパフォーマンスの向上を目的とした一連の修正に加えて、SSMA for Oracle のリリースは、ユーザーが変換されたコードで SSMA 拡張プロパティを省略できるようにする設定を追加することによって強化されています。

Ssma for Oracle でこの設定を利用するには、[**ツール**] [プロジェクト] [設定] [全般変換] の順に移動し、[その  >    >    >  **他**] の [**拡張プロパティを省略** する] 設定の値を **[はい]** に更新します。

![拡張プロパティの設定を省略する](../oracle/media/ssma-omit-extended-properties.png)

さらに、SSMA for Oracle では、句の解析が改善されました `XMLTABLE` 。

> [!IMPORTANT]
> SSMA v1.0 以降では、.NET 4.7.2 はインストールの前提条件です。 このバージョンをインストールする必要がある場合は、 [ここ](https://dotnet.microsoft.com/download/dotnet-framework/net472)からランタイムファイルをダウンロードできます。

## <a name="ssma-v85"></a>SSMA v1.0

SSMA for Oracle の v2.0 リリースは、SQL server での JSON 機能の Azure Active Directory 認証と基本的なサポートがサポートされるようになり、使いやすさとパフォーマンスの向上を目的とした一連の修正が加えられました。

さらに、SSMA for Oracle では、次のサポートが強化されています。

* 探索対象として選択したオブジェクトの数を990に制限する (Oracle の `WHERE .. IN (..)` 句の上限は1000項目)。
* からへのデータ移行 `RAW` `UNIQUEIDENTIFIER` 。
* 句を解析 `PARALLEL_ENABLE` しています。

最後に、SSMA for Oracle の v1.0 リリースでは、次の機能が提供されるようになりました。

* 変換されたパッケージ定数のパフォーマンスが向上しました。
* .NET からバージョン19.5.0 への Oracle Data Provider の更新。

> [!IMPORTANT]
> SSMA v1.0 では、.NET 4.7.2 はインストールの前提条件です。 このバージョンをインストールする必要がある場合は、 [ここ](https://dotnet.microsoft.com/download/dotnet-framework/net472)からランタイムファイルをダウンロードできます。

## <a name="ssma-v84"></a>SSMA v 8.4

SSMA for Oracle の v2.0 リリースは、ユーザー補助の問題に対処し、最大インデックス列 (16 ではなく 32) に関連するバグを修正するように設計された、SQL Server 2016 以降のバージョンで強化されています。

また、SSMA for Oracle のこのリリースでは、 `SYS_REFCURSOR` ストアドプロシージャパラメーターとしての変換が追加されて `OUT` います。

> [!IMPORTANT]
> SSMA バージョン 7.4 ~ 8.4 の場合、.NET 4.5.2 はインストールの前提条件です。

## <a name="ssma-v83"></a>SSMA v 8.3

SSMA for Oracle の v2.0 リリースは、品質と変換のメトリックを向上させるように設計された対象の修正によって強化されています。 また、SSMA for Oracle のこのリリースでは、次のような修正が行われています。

* アクセシビリティの問題に対処します。
* SQL Server に種類の基本サポートを追加 `hierarchyid` します。
* シノニムを通じて呼び出される関数の戻り値の型が不明な問題を解決します。
* ODP.NET を v 19.3 に更新します。

## <a name="ssma-v82"></a>SSMA v 8.2

SSMA for Oracle の v2.0 リリースは次のように強化されています。

* のサポートを追加 `DBMS_OUTPUT.ENABLE` / `DISABLE` します。
* `CAST AS FLOAT`既定の `BINARY_FLOAT` `BINARY_DOUBLE` データ移行クエリでとの列を削除します。
* 現在の値が変更された場合は、シーケンスの更新を修正します。
* `ROWNUM`同じ名前の列が存在する場合は、擬似列 (など) の誤った解釈に関連するバグを修正します。
* `FOR`あいまいな未解決の識別子を使用してループを変換するクラッシュを修正します。

さらに、このバージョンには、品質と変換のメトリックを改善するために設計された修正プログラムのセットと、次のような修正が含まれています。

* データ移行後の無効化されていない非クラスター化インデックスに関する問題。
* サイレントインストール中の .NET Framework の検出。
* 新しいバージョンがダウンロードされると発生する断続的なクラッシュ。

> [!NOTE]
> 自動更新に関する既知の問題により、SSMA v2.0 から v2.0 への更新が失敗する場合があります。 このエラーが発生した場合は、新しいバージョンをダウンロードし、手動でインストールしてください。

## <a name="ssma-v81"></a>SSMA v 8.1

SSMA for Oracle の v2.0 リリースは、品質と変換のメトリックを向上させるように設計された対象の修正によって強化されています。

> [!NOTE]
> 自動更新に関する既知の問題により、SSMA v2.0 から v2.0 への更新が失敗する場合があります。 このエラーが発生した場合は、新しいバージョンをダウンロードし、手動でインストールしてください。

## <a name="ssma-v80"></a>SSMA 8.0

SSMA for Oracle の v2.0 リリースは、品質と変換メトリックの向上を目的とした修正プログラムによって強化されています。 このリリースには、次の新機能も用意されています。

* ターゲットとしての **AZURE SQL Managed Instance** のサポート。 Azure SQL Managed Instance をターゲットとする新しいプロジェクトを作成できるようになりました。

  ![SQL MI プロジェクト](../media/ssma-newproject-sqldbmi.png)

  > [!NOTE]
  > SSMA for Oracle Extension Pack も更新され、Azure SQL Managed Instance でのリモートインストールが可能になりました。
  >
  > ![SSMA for Oracle Extension Pack](../media/ssma-oracle-ext-pack.png)

  テスト担当者やサーバー側のデータ移行を含む一部の機能は、Azure SQL Managed Instance を対象とする場合はサポートされていません。 詳細については、[こちら](https://blogs.msdn.microsoft.com/datamigration/2019/02/17/migrate-your-oracle-database-to-azure-sql-database-managed-instance-using-ssma-8-0/)を参照してください。

* 変換後の **修正アドバイザー**。 詳細について [は、こちら](https://blogs.msdn.microsoft.com/datamigration/2019/02/17/%20accelerate-your-oracle-migrations-with-new-machine-learning-capabilities-in-ssma/)を参照してください。

* データベース/スキーマの事前選択。

  ソースに接続するときに、ユーザーは目的のデータベースまたはスキーマを選択できるようになりました。 移行を計画しているスキーマのみを選択すると、初期接続時に時間が節約され、全体的な SSMA パフォーマンスが向上します。

  ![SSMA フィルターオブジェクト](../media/ssma-filter-objects.png)

* 公式のマネージ .NET ドライバーを使用して Oracle に接続する機能。 OCI ドライバーは、Oracle の SQL Server Migration Assistant を使用するための前提条件ではなくなりました。

* `ROWID`既定でとをマップする機能 `UROWID` `VARCHAR` 。 `uniqueidentifier`明示的な列のデータ移行に対応するように、からに変更されました `ROWID` 。

## <a name="ssma-v710"></a>SSMA v 7.10

SSMA for Oracle のバージョン7.10 リリースには、次の変更が含まれています。

* グローバル要件の変化に対応するために、追加のセキュリティとプライバシーの保護を提供するように設計された修正。
* 階層クエリに関連する変換の改善。

## <a name="ssma-v79"></a>SSMA v 7.9

SSMA for Oracle の v1.0 リリースには、次の変更が含まれています。

* 品質と変換のメトリックを向上させる対象の修正。
* Oracle から SQL Server への "Continue" ステートメントの移行のサポート。
* SSMA コマンドラインでデータ型マッピングとプロジェクト設定を変更するためのサポート。
* SQL Server Integration Services (SSIS) を使用したデータ移行のサポート。 スキーマを変換した後、右クリックコンテキストメニューオプションを使用して SSIS パッケージを作成できます。
* SSMA の [Azure SQL Database 接続] ダイアログも、完全修飾サーバー名を指定するように変更されています。 以前のバージョンの SSMA では、プロジェクト設定内で Azure SQL Database プレフィックスを明示的に記述する必要がありました。

## <a name="ssma-v78"></a>SSMA v 7.8

SSMA for Oracle の v1.0 リリースには、次の変更が含まれています。

* のサポート:
  * 句の行式 `IN` 。
  * 暗黙的な型キャストです。
  * `UID` Azure SQL Database の変換。
* [ **プロジェクトの設定**] で強調表示されている型マッピングを変更します。
* ユーザーがテレメトリを無効にする機能。

## <a name="ssma-v77"></a>SSMA v 7.7

SSMA for Oracle のバージョン7.7 リリースには、次の変更が含まれています。

* SSMA for Oracle は、品質と変換のメトリックを向上させる対象の修正によって強化されています。
* 一般的な要求に基づいて、SSMA for Oracle の32ビットバージョンが返されます。 以前の実装と比較して (v2.0 より前)、インストーラーパッケージは2つありますが、サイドバイサイドでインストールすることはできません。 そのため、使用している接続コンポーネントに基づいて、最適なバージョンを選択する必要があります。 可能であれば、常に64ビットバージョンを使用することをお勧めします。
* SQL Server 2017 のサポートが、Linux でサポートされている Oracle 拡張パック (新しいリモートインストールオプション) で公式になりました。 テスト担当者とサーバー側のデータ移行機能はサポートされていないため、Linux にインストールされている場合は拡張パックの機能が制限されていることに注意してください。
* Ssma for Oracle では、具体化されたビューを通常のテーブルとして移行できます ([**プロジェクト設定** の同期] の設定で構成でき  ->    ->  ます)。具体化された **ビューのバッキングテーブルを検出** します。

## <a name="ssma-v76"></a>SSMA v1.0

SSMA for Oracle の v1.1 リリースは、品質と変換のメトリックを向上させ、SQL Server 2017 (パブリックプレビュー) をサポートする対象の修正によって強化されています。 Windows および Linux での SQL Server 2017 のサポートはパブリックプレビュー段階であり、運用環境への移行には使用しないでください。

## <a name="ssma-v75"></a>SSMA v 7.5

SSMA for Oracle の version 7.5 リリースには、次の変更が含まれています。

* 障碍のある方にとってアクセシビリティを向上させるために、いくつかの機能強化が施されました。
* 顧客からのフィードバックに基づいて、データ移行中の日付データ型と float データ型の処理の改善など、対象の修正によって品質と変換のメトリックが向上しました。

## <a name="ssma-v74"></a>SSMA v1.0

SSMA for Oracle の v2.0 リリースには、次の変更が含まれています。

* SSMA for Oracle では、移行のターゲットプラットフォームとして Azure Synapse Analytics がサポートされるようになりました。

  ![[新しいプロジェクト] ウィンドウ](../media/new-project.png)
  * では、次の図に示すように、データウェアハウスのストレージオプションがサポートされています。

  ![データウェアハウスのストレージオプション](../media/storage-options_red.png)
  * では、次の図に示すように、データ分散オプションがサポートされています。

  ![データウェアハウスのデータ分散](../media/data-distribution_red.png)

* [ **クエリタイムアウト** ] オプションは、ソースおよびターゲットでのスキーマオブジェクトの検出中に使用できるようになりました。

  ![クエリタイムアウトオプション](../media/query-timeout_red.png)

* お客様からのフィードバックに基づいて、品質と換算のメトリックが修正されました。

> [!IMPORTANT]
> .NET 4.5.2 は、SSMA v2.0 をインストールするための前提条件です。 さらに、v2.0 以降では、SSMA の32ビットバージョンは廃止されています。

## <a name="ssma-v73"></a>SSMA version 7.3

SSMA for Oracle の version 7.3 リリースには、次の変更が含まれています。

* お客様からのフィードバックに基づいて、対象の修正を含む品質と変換のメトリックが向上しました。
* SSMA 拡張フレームワークは、次の項目を介して公開されます。
  * SQL Server Data Tools (SSDT) プロジェクトに機能をエクスポートします。
    * SSMA から SSDT プロジェクトにスキーマスクリプトをエクスポートできるようになりました。 スキーマスクリプトを使用すると、追加のスキーマ変更を行ったり、データベースを配置したりすることができます。

      ![SSDT プロジェクトの名前を付けて保存コマンド](../media/export-schema-scripts_red.png)
  * カスタム変換を実行するために SSMA で使用できるライブラリ。
    * SSMA によって以前に処理されなかったカスタム構文変換と変換を処理できるコードを作成できるようになりました。
      * カスタムコンバーターを構築する方法については、このブログの投稿を参照してください。 [SQL Server Migration Assistant の変換機能が拡張](https://blogs.msdn.microsoft.com/datamigration/2017/02/21/2185/)されます。
      * この [ブログの投稿](https://blogs.msdn.microsoft.com/datamigration/ssmafororacleconversionsample/)から、変換用のサンプルプロジェクトをダウンロードします。

## <a name="ssma-v72"></a>SSMA v 7.2

SSMA for Oracle の v2.0 リリースには、次の変更が含まれています。

* お客様からのフィードバックに基づいて、対象の修正を含む品質と変換のメトリックが向上しました。
* テレメトリの機能強化により、顧客の問題をトラブルシューティングし、SSMA のコンバージョン率を向上させるためのデータポイントが向上しました。

## <a name="ssma-v71"></a>SSMA 7.1

SSMA for Oracle の v2.0 リリースには、次の変更が含まれています。

* Windows と Linux CTP1 の SQL Server 2017 は、現在、移行のためにサポートされているターゲットプラットフォームです。 この機能は technical preview にあり、SQL server を対象とするスキーマとデータの移動を可能にします。
* SSMA は、最新バージョンの SSMA を利用できるようになったらすぐにダウンロードできる自動更新をサポートするようになりました。
* SSMA のインストール可能なバイナリが Windows インストーラーパッケージファイル (.msi) を介して配信されるようになりました。

## <a name="may-2016"></a>2016 年 5 月

SSMA for Oracle の2016年5月のリリースには、次の変更が含まれています。

* SQL Server 2016 のサポートが追加されました。
* Oracle フラッシュバックのアーカイブテーブルの SQL Server テンポラルテーブルへの変換が追加されました。

  > [!NOTE]
  > SSMA は、Oracle フラッシュバックデータアーカイブテーブルから履歴データをコピーしません。 そのため、移行プロセス中に履歴データを手動でコピーする必要があります。 また、SSMA では、履歴テーブルはシステムテーブルとして扱われるため SQL Server メタデータエクスプローラーに表示されませんが、SQL Server Management Studio で履歴テーブルを表示できます。
  >
  > SQL Server 2016 では、次のようないくつかの Oracle フラッシュバック機能はサポートされていません。
  >
  >   * Oracle フラッシュバックトランザクションクエリ
  >   * `DBMS_FLASHBACK` [パッケージ]
  >   * フラッシュバックトランザクション
  >   * フラッシュバックデータアーカイブ
  >   * フラッシュバックテーブル
  >   * フラッシュフラッシュの削除
  >   * フラッシュバックデータベース

* Oracle 用の SQL Server ポリシーオブジェクト (Oracle の場合は行レベルセキュリティ) への Oracle VPD ポリシーの変換が追加されました。
* Oracle の初期読み込みの時間が短縮します。
* パーサーと競合回避モジュールが向上しました。
* .NET 2.0 のインストーラーチェックが削除されました。
* .NET 3.5 から .NET 4.0 への拡張パックの依存関係を更新しました。
* `save-project` `open-project` Ssma コンソールのコマンドとコマンドを修正します。
* `securepassword`SSMA コンソールのコマンドを修正します。
* 初期読み込みのオブジェクトのカウントを固定します。
* Oracle の文字データ型の変換を修正します。
* グローバル設定のバグを修正した。

## <a name="march-2016"></a>2016 年 3 月

SSMA for Oracle の2016年3月のプレビューリリースでは、次のサポートが追加されました。

* SQL Server 2016 への移行。
* Oracle 行レベルセキュリティの移行 (いくつかの制限があります)。
* Oracle のメモリテーブルを SQL Server 列ストアに移行しています。

## <a name="january-2016"></a>2016 年 1 月

SSMA for Oracle の2014年1月のメンテナンスリリースには、次の変更が含まれています。

* クラスター化インデックスのサポートが追加されました。
* 低速の Oracle スキーマクエリ (RFC 5076207) を修正します。
* コンソールから Azure への接続を修正します。
* SSMA (RFC 5706203) に [ログの表示] メニュー項目が追加されました。
* テレメトリを追加しました。

## <a name="july-2014"></a>2014 年 7 月

SSMA for Oracle の2014年7月のリリースには、次の変更が含まれています。

* Azure SQL Database のサポートが追加されました。
* 拡張パックの機能は、Azure SQL Database をサポートするためにスキーマに移行されました。
* Oracle の具体化されたビューのサポートを追加しました。
* SQL Server 2014 メモリ最適化テーブルのサポートが追加されました。
* 10,000 を超えるオブジェクトを含むデータベースに対してテストされたパフォーマンスの向上。
* 多数のオブジェクトを処理するための UI の機能強化が追加されました。
* 既知の LOB スキーマの強調表示を追加しました。
* 変換速度の向上が含まれます。
* UI でオブジェクト数を表示するためのサポートを追加しました。
* レポートのサイズが25% を超えています。
* 未解析のコンストラクトのエラーメッセージが改善されました。

## <a name="april-2014"></a>2014 年 4 月

SSMA for Oracle の2014年4月のリリースには、次の変更が含まれています。

* MS SQL Server 2014 のサポートが追加されました。
* Oracle 12 とクエリの最適化のサポートが追加されました。
* Azure への変換に関するバグを修正した。
* IE 10 の非表示レポートページに関するバグを修正した。

## <a name="january-2012"></a>2012 年 1 月

SSMA for Oracle の2012年1月リリースでは、およびの既定の入力パラメーターのサポートが追加されて `RowType` `RecordType` `NULL` います。

## <a name="july-2011"></a>2011 年 7 月

SSMA for Oracle の2011年7月のリリースには、次の変更が含まれています。

* Oracle シーケンスからシーケンスジェネレーターへの変換のサポートが追加されました [!INCLUDE [ssSQL11](../../includes/sssql11-md.md)] 。
* データ移行中のエラー報告の向上。
* 予約語を使用したステートメントの変換が改善されました。
* 関数の日付値の暗黙的な変換が改善されました。

## <a name="april-2011"></a>2011年4月

SSMA for Oracle の2011年4月のリリースには、次の変更が含まれています。

* 、、およびをサポートする統合された "SSMA for Oracle" 製品 [!INCLUDE [ssVersion2005](../../includes/ssversion2005-md.md)] [!INCLUDE [ssSQL10](../../includes/sssql10-md.md)] [!INCLUDE [ssSQL11](../../includes/sssql11-md.md)] 。
* への接続と移行のサポートが追加されました [!INCLUDE [ssSQL11](../../includes/sssql11-md.md)] 。
* 拡張されたクライアント側のデータ移行エンジンで、データの並列移行がサポートされています。
* `Simple`およびログ復旧モデルを使用したデータ移行のパフォーマンスの向上 `Bulk` 。
* 以前のバージョンの SSMA (v1.0 および v1.1) で作成されたプロジェクトの旧バージョンとの互換性のためのサポートを追加しました。
* Ssma for Oracle v1.0 製品を旧バージョンの SSMA (v2.0 および v1.1) とサイドバイサイド (SxS) でインストールする機能が追加されました。
* User-Defined の種類 (サブタイプ、 `VARRAY` 、 `NESTED TABLE` 、オブジェクトテーブル、オブジェクトビューを含む) とその使用状況を、特別なエラーメッセージを含む PL/SQL ブロックで報告するためのサポートが追加されました。

## <a name="july-2010"></a>2010 年 7 月

SSMA for Oracle の2010年7月のリリースには、次のものが追加されました。

* SQL Server 2008 R2 への移行のサポート。
* コマンドライン実行用の新しい SSMA コンソールアプリケーション。
* Server-Side と Client-Side の両方のデータ移行エンジンを使用したデータ移行のサポート。
* データ移行での "カスタム SELECT" ステートメントのサポート。
* Oracle 11g R2 からの移行のサポート。

## <a name="june-2008"></a>2008年6月

SSMA for Oracle の2008年6月のリリースには、次の変更が含まれています。

* シノニムの追加情報、解析可能なオブジェクトの未加工のソース、パネルと SQL Server ロゴの削除、レイアウトの永続化など、評価レポートの機能強化が追加されました。
* オブジェクト変換の機能強化が追加されました。
  * パッケージ `DBMS_LOB` 、 `DBMS_SQL` 変換が追加されました。
  * 結合変換の変更。
  * コレクションとレコードの変換を変更し、フィールドごとに個別の変数を使用してリリースされた単純なケースでレコードを変換できるようになりました。
  * レコードとコレクションの実装の機能強化。
  * ウィンドウ集計関数が追加されました。
  * `ROLLUP`/`CUBE` 句が追加されました。
  * の機能強化 `NEXTVAL` / `CURVAL` 。
  * `SET`句、グループ化セット、およびグループ化 ID の列グループが追加されました。
  * `MERGE` ステートメントが追加されました。
  * 新しい datetime 型をサポートし、CLR データ型としてレコードとコレクションを変換しました。
* テスト担当者の新機能が追加されました。 テーブルは、テスト担当者を使用してオブジェクトとしてテストできるようになりました。テストケース内の複数のテスト可能なオブジェクトの呼び出し順序を変更できます。また、ユーザーは、レコードとコレクションを含むプロシージャと関数をパラメーターと戻り値としてテストできます
  
## <a name="august-2007"></a>2007年8月

SSMA for Oracle の2007年8月のリリースでは、次のことが追加されました。

* 新しい Tester コンポーネントを使用すると、テストケースを作成、管理、および実行して、変換された SQL コードを確認できます。
* SQL コンバーターには、Oracle のサブタイプ、コレクション、およびローカルモジュールの変換がサポートされています。
* 新しい同期機能を使用すると、特定のオブジェクトを SQL Server データベースと同期させることができます。
* 新しい変換オプション。
  
## <a name="april-2007"></a>2007年4月

SSMA for Oracle の2007年4月のリリースは、最初のリリースでした。
