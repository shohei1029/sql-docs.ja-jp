---
title: SSMA for SAP ASE の新機能 (SybaseToSQL) |Microsoft Docs
description: 各リリースの Sybase (SybaseToSQL) の SQL Server Migration Assistant (SSMA) に加えられた変更について説明します。
author: nahk-ivanov
ms.prod: sql
ms.custom: ''
ms.date: 12/17/2020
ms.reviewer: ''
ms.technology: ssma
ms.topic: conceptual
ms.assetid: 2be0cf8d-6dbe-443a-abbd-036249922205
ms.author: alexiva
ms.openlocfilehash: 4732f2ca968788cb59efd14d9410929f99d9423f
ms.sourcegitcommit: 0b37eb7aef2f358f80867cd13830dd6683da8d85
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105980896"
---
# <a name="whats-new-in-ssma-for-sap-ase-sybasetosql"></a>SSMA for SAP ASE の新機能 (SybaseToSQL)

この記事では、各リリースでの SAP ASE (旧称 SSMA for Sybase) の変更 (SSMA) の SQL Server Migration Assistant を示します。

## <a name="ssma-v818"></a>SSMA v 8.18

SSMA for SAP ASE の v 8.18 リリースには、次の変更が含まれています。

* わずかなパフォーマンス向上とバグ修正

## <a name="ssma-v817"></a>SSMA v 8.17

SSMA for SAP ASE の v 8.17 リリースには、次の変更が含まれています。

* 計算列のサポートを追加する
* 最新のエディターを使用して SQL テキストを表示するように HTML 評価レポートを更新する

## <a name="ssma-v816"></a>SSMA v 8.16

SSMA for SAP ASE の v 8.16 リリースには、次の変更が含まれています。

* レガシパーサーのサポートを削除する
* データベースから更新されていないオブジェクトの問題を修正します

## <a name="ssma-v815"></a>SSMA v 8.15

SSMA for SAP ASE の v 8.15 リリースには、いくつかのユーザー補助機能の強化に加えて、次の変更が含まれています。

* 改良評価レポートを最新のブラウザーで動作させる
* Azure AD 認証にデータベースによって提供される権限を使用する
* ファイルから読み込まれたステートメントの名前付けを改善する

## <a name="ssma-v814"></a>SSMA v 8.14

障碍のある方にとってのアクセシビリティを向上させるために、いくつかの改善点に加えて、SSMA for SAP ASE のバージョン8.14 のリリースでは、プロジェクトのアップグレードが必要になりました。これにより、プロジェクトのメタデータには、完全なソース/対象サーバーのバージョンが格納されます。

## <a name="ssma-v813"></a>SSMA v 8.13

SSMA for SAP ASE の v 8.13 リリースには、次の変更が含まれています。

* プロシージャと関数の呼び出しを変換するときに、暗黙的な型キャストを検討します。
* 接続の問題のトラブルシューティングに役立つソース接続文字列のログ記録の向上

## <a name="ssma-v812"></a>SSMA v 8.12

SSMA for SAP ASE の v1.0 リリースには、わずかなパフォーマンス向上とバグ修正が含まれています。

## <a name="ssma-v811"></a>SSMA v 8.11

SSMA for SAP ASE の v 8.11 リリースには、次の変更が含まれています。

* 一時テーブルの変換の修正
* MSAL.NET library を使用して対話型 Azure Active Directory 認証を行う

## <a name="ssma-v810"></a>SSMA v 8.10

SSMA for SAP ASE の v1.0 リリースには、わずかなパフォーマンス向上とバグ修正が含まれています。

## <a name="ssma-v89"></a>SSMA v 8.9

SSMA for SAP ASE の v1.0 リリースには、次の変更が含まれています。

* 日付と時刻の形式の変換の改善
* オブジェクトの SQL 定義に含まれていない文字に関する問題の修正

## <a name="ssma-v88"></a>SSMA v 8.8

SSMA for SAP ASE のバージョン8.8 には次のものが含まれます。

* SQL Server オブジェクトの同期の安定性の向上
* 評価および変換中の GUI パフォーマンスの向上
* オブジェクトの SQL 定義に含まれていない文字に関する問題の修正

## <a name="ssma-v87"></a>SSMA v 8.7

SSMA for SAP ASE のバージョン8.7 リリースでは、グラフィカルユーザーインターフェイスの若干の修正とパフォーマンスの向上が図られています。

> [!IMPORTANT]
> SSMA v1.0 以降では、.NET 4.7.2 はインストールの前提条件です。 このバージョンをインストールする必要がある場合は、 [ここ](https://dotnet.microsoft.com/download/dotnet-framework/net472)からランタイムファイルをダウンロードできます。

## <a name="ssma-v86"></a>SSMA v 8.6

ユーザビリティとパフォーマンスを向上させるために設計された一連の修正に加えて、SSMA for SAP ASE のリリースは、ユーザーが変換されたコードで SSMA の拡張プロパティを省略できるようにする設定を追加することによって強化されています。

Ssma for SAP ASE でこの設定を利用するには、[**ツール**] [プロジェクト] [  >  **設定**]  >  **[全般**  >  **変換**] に移動し、[その **他**] の [**拡張プロパティを省略**] 設定の値を **[はい]** に更新します。

![拡張プロパティの設定を省略する](../sybase/media/ssma-omit-extended-properties.png)

> [!IMPORTANT]
> SSMA v1.0 以降では、.NET 4.7.2 はインストールの前提条件です。 このバージョンをインストールする必要がある場合は、 [ここ](https://dotnet.microsoft.com/download/dotnet-framework/net472)からランタイムファイルをダウンロードできます。

## <a name="ssma-v85"></a>SSMA v1.0

SSMA for SAP ASE の v1.0 リリースは、SQL server での JSON 機能の Azure Active Directory 認証と基本的なサポートがサポートされるようになり、使いやすさとパフォーマンスを向上させるように設計された修正プログラムが対象となりました。

さらに、SSMA for SAP ASE では、システムテーブルとビューを非表示にする (変換から除外する) ことができます。

> [!IMPORTANT]
> SSMA v1.0 では、.NET 4.7.2 はインストールの前提条件です。 このバージョンをインストールする必要がある場合は、 [ここ](https://dotnet.microsoft.com/download/dotnet-framework/net472)からランタイムファイルをダウンロードできます。

## <a name="ssma-v84"></a>SSMA v 8.4

SSMA for SAP ASE の v1.0 リリースは、ユーザー補助の問題に対処し、最大インデックス列 (16 ではなく 32) に関連するバグを修正して、SQL Server 2016 以降のバージョンに対応するように設計された、対象の修正によって強化されています。

> [!IMPORTANT]
> SSMA バージョン 7.4 ~ 8.4 の場合、.NET 4.5.2 はインストールの前提条件です。

## <a name="ssma-v83"></a>SSMA v 8.3

SSMA for SAP ASE の v2.0 リリースは、品質と変換のメトリックを向上させるように設計された対象の修正によって強化されています。 また、SSMA for SAP ASE の今回のリリースでは、次のような修正が行われています。

* アクセシビリティに関する問題の解決
* SQL Server の型の基本サポートを追加する `hierarchyid`

## <a name="ssma-v82"></a>SSMA v 8.2

SSMA for SAP ASE の v2.0 リリースは、品質と変換のメトリックを向上するように設計された修正プログラムのセットと、次のような修正によって強化されています。

* データ移行後の無効化されていない非クラスター化インデックスに関する問題。
* サイレントインストール中の .NET Framework の検出。
* 新しいバージョンがダウンロードされると発生する断続的なクラッシュ。

> [!NOTE]
> 自動更新に関する既知の問題により、SSMA v2.0 から v2.0 への更新が失敗する場合があります。 このエラーが発生した場合は、新しいバージョンをダウンロードし、手動でインストールしてください。

## <a name="ssma-v81"></a>SSMA v 8.1

SSMA for SAP ASE の v2.0 リリースは、品質と変換のメトリックを向上させるように設計された対象の修正によって強化されています。

> [!NOTE]
> 自動更新に関する既知の問題により、SSMA v2.0 から v2.0 への更新が失敗する場合があります。 このエラーが発生した場合は、新しいバージョンをダウンロードし、手動でインストールしてください。

## <a name="ssma-v80"></a>SSMA 8.0

SSMA for SAP ASE の v2.0 リリースは、品質と変換メトリックの向上を目的とした修正を対象として強化されています。 また、このリリースでは、次の新機能が提供されています。

* ターゲットとしての **AZURE SQL Managed Instance** のサポート。 Azure SQL Managed Instance をターゲットとする新しいプロジェクトを作成できるようになりました。

  ![SQL Database MI プロジェクト](../media/ssma-newproject-sqldbmi.png)

* 変換後の **修正アドバイザー**。 詳細について [は、こちら](https://blogs.msdn.microsoft.com/datamigration/2019/02/17/%20accelerate-your-oracle-migrations-with-new-machine-learning-capabilities-in-ssma/)を参照してください。

* データベース/スキーマの事前選択。

  ソースに接続するときに、ユーザーは目的のデータベースまたはスキーマを選択できるようになりました。 移行を計画しているスキーマのみを選択すると、初期接続時に時間が節約され、全体的な SSMA パフォーマンスが向上します。

  ![SSMA フィルターオブジェクト](../media/ssma-filter-objects.png)

## <a name="ssma-v710"></a>SSMA v 7.10

SSMA for SAP ASE のバージョン7.10 リリースは、グローバル要件の変化に対応するための追加のセキュリティとプライバシーの保護を提供するように設計された修正プログラムによって強化されています。

## <a name="ssma-v79"></a>SSMA v 7.9

SSMA for SAP ASE の v1.0 リリースには、次の変更が含まれています。

* 品質と変換のメトリックを向上させる対象の修正。
* SSMA コマンドラインでデータ型マッピングとプロジェクト設定を変更するためのサポート。
* SQL Server Integration Services (SSIS) を使用したデータ移行のサポート。 スキーマを変換した後、右クリックコンテキストメニューオプションを使用して SSIS パッケージを作成できます。
* SSMA の [Azure SQL Database 接続] ダイアログも、完全修飾サーバー名を指定するように変更されています。 以前のバージョンの SSMA では、プロジェクト設定内で Azure SQL Database プレフィックスを明示的に記述する必要がありました。

## <a name="ssma-v78"></a>SSMA v 7.8

SSMA for SAP ASE の v1.0 リリースには、次の変更が含まれています。

* [ **プロジェクトの設定**] で強調表示されている型マッピングを変更します。
* ユーザーがテレメトリを無効にする機能。

## <a name="ssma-v77"></a>SSMA v 7.7

SSMA for SAP ASE のバージョン7.7 リリースには、次の変更が含まれています。

* SSMA for SAP ASE は、品質と変換のメトリックを向上させる対象の修正によって強化されています。
* 一般的な要求に基づいて、SSMA for SAP ASE の32ビットバージョンは戻ります。 以前の実装と比較して (v2.0 より前)、インストーラーパッケージは2つありますが、サイドバイサイドでインストールすることはできません。 そのため、使用している接続コンポーネントに基づいて、最適なバージョンを選択する必要があります。 可能であれば、常に64ビットバージョンを使用することをお勧めします。

## <a name="ssma-v76"></a>SSMA v1.0

SSMA for SAP ASE の v1.1 リリースには、次の変更が含まれています。

* 品質と変換のメトリックを向上させ、SQL Server 2017 (パブリックプレビュー) をサポートするように対象とする修正。 Windows および Linux での SQL Server 2017 のサポートはパブリックプレビュー段階であり、運用環境への移行には使用しないでください。
* Sybase 関数の変換のサポート。

## <a name="ssma-v75"></a>SSMA v 7.5

SSMA for SAP ASE のバージョン7.5 リリース (以前の SSMA for Sybase) には、次の変更が含まれています。

* 障碍のある方にとってアクセシビリティを向上させるためにいくつかの機能強化が行われました。
* 構文のサポート `CREATE OR REPLACE` 。

## <a name="ssma-v74"></a>SSMA v1.0

SSMA for Sybase の v2.0 リリースには、次の変更が含まれています。

* [ **クエリタイムアウト** ] オプションは、ソースおよびターゲットでのスキーマオブジェクトの検出中に使用できるようになりました。

  ![クエリタイムアウトオプション](../media/query-timeout_red.png)
* お客様からのフィードバックに基づいて、品質と換算のメトリックが修正されました。

  > [!IMPORTANT]
  > .NET 4.5.2 は、SSMA v2.0 をインストールするための前提条件です。 さらに、v2.0 以降では、SSMA の32ビットバージョンは廃止されています。

## <a name="ssma-v73"></a>SSMA version 7.3

SSMA for Sybase の version 7.3 リリースには、次の変更が含まれています。

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

SSMA for Sybase のバージョン7.2 には、次の変更が含まれています。

* お客様からのフィードバックに基づいて、対象の修正を含む品質と変換のメトリックが向上しました。
* テレメトリの機能強化により、顧客の問題をトラブルシューティングし、SSMA のコンバージョン率を向上させるためのデータポイントが向上しました。

## <a name="ssma-v71"></a>SSMA 7.1

SSMA for Sybase の v2.0 リリースには、次の変更が含まれています。

* Windows と Linux CTP1 の SQL Server 2017 は、現在、移行のためにサポートされているターゲットプラットフォームです。 この機能は technical preview であり、SQL server を対象とするスキーマとデータの移動をサポートしています。
* 最新バージョンの SSMA が利用可能になるとすぐにダウンロードするための自動更新のサポート。
* SSMA のインストール可能なバイナリが Windows インストーラーパッケージファイル (.msi) を介して配信されるようになりました。

## <a name="may-2016"></a>2016 年 5 月

SSMA for Sybase の2016年5月のリリースには、次の変更が含まれています。

* SQL Server 2016 のサポートが追加されました。
* .NET 2.0 のインストーラーチェックが削除されました。
* .NET 3.5 から .NET 4.0 への拡張パックの依存関係を更新しました。
* `save-project` `open-project` Ssma コンソールのコマンドとコマンドを修正します。
* `securepassword`SSMA コンソールのコマンドを修正します。
* 初期読み込みのオブジェクトのカウントを固定します。
* グローバル設定のバグを修正した。

## <a name="march-2016"></a>2016 年 3 月

SSMA for Sybase の2016年3月のプレビューリリースでは、SQL Server 2016 への移行のサポートが追加されます。

## <a name="january-2016"></a>2016 年 1 月

SSMA for Sybase の2016年1月のメンテナンスリリースには、次の変更が含まれています。

* SSMA (RFC 5706203) に [ログの表示] メニュー項目が追加されました。
* テレメトリを追加しました。

## <a name="july-2014"></a>2014 年 7 月

SSMA for Sybase の2014年7月のリリースには、次の変更が含まれています。

* Azure SQL Database コード変換が改善されました。
* 拡張パックの機能をスキーマに移動し、Azure SQL Database をサポートしました。
* 10,000 を超えるオブジェクトを含むデータベースに対してテストされたパフォーマンスの改善。
* 多数のオブジェクトを処理するための UI の機能強化が追加されました。
* 既知の LOB スキーマを強調表示する機能が追加されました (そのため、変換では無視できます)。
* 変換速度が向上しました。
* UI でオブジェクト数を表示する機能が追加されました。
* レポートのサイズが25% を超えています。
* 未解析のコンストラクトのエラーメッセージが改善されました。

## <a name="april-2014"></a>2014 年 4 月

SSMA for Sybase の2014年4月のリリースには、次の変更が含まれています。

* MS SQL Server 2014 のサポートが追加されました。
* Azure への変換に関するバグを修正した。
* IE 10 の非表示レポートページに関するバグを修正した。

## <a name="january-2012"></a>2012 年 1 月

SSMA for Sybase の2012年1月リリースには、次の変更が含まれています。

* ロールバックトリガー変換のサポートが追加されました。
* `@@ROWCOUNT`とを `@@ERROR` 同じステートメント内で変換するための修正を提供しました `SET` 。

## <a name="july-2011"></a>2011 年 7 月

SSMA for Sybase の2011年7月のリリースでは、データ移行中のエラー報告が改善されています。

## <a name="april-2011"></a>2011年4月

SSMA for Sybase の2011年4月のリリースには、次の変更が含まれています。

* "Ssma for Sybase" 製品が統合さ [!INCLUDE [ssVersion2005](../../includes/ssversion2005-md.md)] れています。これは、、、 [!INCLUDE [ssSQL10](../../includes/sssql10-md.md)] および Azure SQL をサポートし [!INCLUDE [ssSQL11](../../includes/ssSQL11-md.md)] ています。
* への接続と移行のサポートが追加されました [!INCLUDE [ssSQL11](../../includes/sssql11-md.md)] 。
* Sybase データベースを変換して Azure SQL に移行するための新機能が追加されました。
* 拡張されたクライアント側のデータ移行エンジンで、データの並列移行がサポートされています。
* 単純復旧モデルと一括ログ復旧モデルにより、データ移行のパフォーマンスが向上しました。
* 大文字と小文字を区別する Sybase データベースを適切に変換し、大文字と小文字を区別する SQL Server に移行する機能が追加されました。
* Ansi join ステートメントの SQL Server への変換のサポートが追加され、ステートメントを削除および更新できるようになりました。
* Sybase ASE ODBC プロバイダと Sybase ASE ADO.NET プロバイダを使用して Sybase ASE サーバーに接続するための追加の接続オプションが用意されています。
* という別のデータベースへの依存関係を削除しました `SysDB` 。これには、Sybase エミュレーション関数 (拡張パックの一部としてインストールされます) が含まれています。
* SSMA for Sybase Extension Pack をクラスターにインストールする機能が追加されました [!INCLUDE [ssNoVersion](../../includes/ssnoversion-md.md)] 。
* 以前のバージョンの SSMA (v1.0 および v1.1) で作成されたプロジェクトの下位互換性が追加されました。
* Ssma for Sybase version 5.0 製品を旧バージョンの SSMA (v2.0 および v1.1) とサイドバイサイド (SxS) でインストールする機能が追加されました。

## <a name="july-2010"></a>2010 年 7 月

SSMA for Sybase の2010年7月のリリースは、次のように追加されました。

* SQL Server 2008 R2 への移行のサポート。
* コマンドライン実行用の新しい SSMA コンソールアプリケーション。
* Server-Side と Client-Side の両方のデータ移行エンジンを使用したデータ移行のサポート。
* データ移行での "カスタム SELECT" ステートメントのサポート。
* Sybase ASE 15.0.3 と15.5 からの移行のサポート。

## <a name="june-2008"></a>2008年6月

SSMA for Sybase の2008年6月のリリースには、次の変更が含まれています。

* SSMA Tester が追加されました。 ssma テスターは、SSMA によって行われたデータベースオブジェクトの変換とデータ移行を自動的にテストします。 SSMA の移行手順がすべて完了したら、SSMA Tester を使用して、変換されたオブジェクトが同じように動作することと、すべてのデータが適切に転送されたことを確認します。
* SQL 変換前の変換が追加されました。 ユーザーは、変換に使用する各ソースプロシージャの一時テーブル (およびその他のオブジェクト) の宣言を指定できるようになりました。
* オブジェクト変換の機能強化が追加されました。
  * 結合変換の変更。
  * /Group by 句を指定せずに集計と非集計を行います。
  * `IDENTITY`ステートメントを含む関数 `SELECT INTO` 。
  * データのみがロックされているクラスター化された制約とインデックス。
  * によって作成された一時テーブル `SELECT INTO` 。
  * 一時テーブルの制約/インデックス。
  * 新しい [!INCLUDE [ssSQL10](../../includes/sssql10-md.md)] datetime 型がサポートされています。
  * Sybase 15.0 の接続とデータ型のサポート。

## <a name="may-2007"></a>5月2007

SSMA for Sybase の2007年5月のリリースが追加されました。

* プロジェクトを保存するときに、データベースの内容をすばやく読み込むことができます。
* SQL Server 書式設定された SQL モードでのユーザー入力のコメントのサポート。
* オブジェクト変換の機能強化。

## <a name="november-2006"></a>2006年11月

SSMA for Sybase の2006年11月のリリースには、次の変更が含まれています。

* 新しいグローバル設定を追加しました:
  * エディターウィンドウで行番号を表示するように選択できます。
  * SSMA を構成して、重複するオブジェクトの置換を求めるメッセージを表示したり、スキーマの変換中に重複するオブジェクトを常に置き換えることができます。
* 次の状況を SSMA が処理する方法を構成できる新しい変換オプションが追加されました。
  * `CAST` `CONVERT` バイナリ文字列を含むまたはステートメント。
  * 等値式で null 値をチェックします。
  * プロキシテーブル。
  * のユーザーメッセージエラー番号 `RAISERROR` 。
  * `UPDATE` 未解決の識別子を含むステートメント。
* 新しい移行オプションが追加されました。このオプションを使用すると、SSMA が日付範囲外の日付をどのように処理するかを指定でき [!INCLUDE [ssNoVersion](../../includes/ssnoversion-md.md)] ます。
* [ **Sql** ] タブに、書式設定された **sql** 設定が追加されました。これにより、コードの読みやすさが向上します。
* バグの修正 (次を含む):
  * SSMA で `LOCK TABLE <table> IN { SHARED | EXCLUSIVE } MODE` `TABLOCK` は、またはヒントを `TABLOCKX` テーブルの後続のクエリに追加することによって、ステートメントを変換するようになりました `SELECT` 。
  * 文字式でバイナリ型を使用するときに、必要なキャストが追加されるようになりました。
  * メモリとパフォーマンスの向上。

## <a name="july-2006"></a>2006年7月

SSMA for Sybase は、2006 年 7 月のリリースが初回のリリースでした。

## <a name="see-also"></a>関連項目

[SSMA for Sybase のはじめに &#40;SybaseToSQL&#41;](../../ssma/sybase/getting-started-with-ssma-for-sybase-sybasetosql.md)
