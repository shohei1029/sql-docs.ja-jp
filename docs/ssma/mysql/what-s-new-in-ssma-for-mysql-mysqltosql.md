---
title: SSMA for MySQL の新機能 (MySQLToSql) |Microsoft Docs
description: リリースごとに SQL Server Migration Assistant (SSMA) for MySQL (MySQLToSQL) の変更点を確認してください。
author: nahk-ivanov
ms.prod: sql
ms.custom: ''
ms.date: 12/17/2020
ms.reviewer: ''
ms.technology: ssma
ms.topic: conceptual
ms.assetid: 1451a0b0-6713-4d0c-954f-ea3d8fce1d31
ms.author: alexiva
ms.openlocfilehash: e84ab2f4fe9e19d08a8728bd8ca985561497252d
ms.sourcegitcommit: 0b37eb7aef2f358f80867cd13830dd6683da8d85
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105981048"
---
# <a name="whats-new-in-ssma-for-mysql-mysqltosql"></a>SSMA for MySQL の新機能 (MySqlToSql)

この記事では、各リリースでの MySQL 変更の SQL Server Migration Assistant (SSMA) を示します。

## <a name="ssma-v818"></a>SSMA v 8.18

SSMA for MySQL の v 8.18 リリースには、次の変更が含まれています。

* わずかなパフォーマンス向上とバグ修正

## <a name="ssma-v817"></a>SSMA v 8.17

SSMA for MySQL の v 8.17 リリースには、次の変更が含まれています。

* 最新のエディターを使用して SQL テキストを表示するように HTML 評価レポートを更新する

## <a name="ssma-v816"></a>SSMA v 8.16

SSMA for MySQL の v 8.16 リリースには、次の変更が含まれています。

* 計算列のサポートを追加する
* `INSERT`Unique 制約と主キーを持つテーブルに対してステートメントを変換するときの問題を修正する
* `ANSI_QUOTES`およびサーバーモードに合わせたパーサーの更新 `NO_BACKSLASH_ESCAPES`
* レガシパーサーのサポートを削除する
* データベースから更新されていないオブジェクトの問題を修正します

## <a name="ssma-v815"></a>SSMA v 8.15

SSMA for MySQL の v 8.15 リリースには、いくつかのユーザー補助機能の強化に加えて、次の変更が含まれています。

* 改良評価レポートを最新のブラウザーで動作させる
* Azure AD 認証にデータベースによって提供される権限を使用する
* ファイルから読み込まれたステートメントの名前付けを改善する

## <a name="ssma-v814"></a>SSMA v 8.14

障碍のある方にとってアクセシビリティを向上させるために、いくつかの機能強化に加えて、SSMA for MySQL のリリースでは、プロジェクトのメタデータに完全なソース/ターゲットサーバーバージョンが格納されるため、プロジェクトのアップグレードが必要になりました。

## <a name="ssma-v813"></a>SSMA v 8.13

SSMA for MySQL の v 8.13 リリースには、次の変更が含まれています。

* プロシージャと関数の呼び出しを変換するときに、暗黙的な型キャストを検討します。
* 接続の問題のトラブルシューティングに役立つソース接続文字列のログ記録の向上

## <a name="ssma-v812"></a>SSMA v 8.12

SSMA for MySQL の v1.0 リリースには、次の変更が含まれています。

* 一時テーブル DDL の変換

## <a name="ssma-v811"></a>SSMA v 8.11

SSMA for MySQL の v 8.11 リリースには、次の変更が含まれています。

* MSAL.NET library を使用して対話型 Azure Active Directory 認証を行う

## <a name="ssma-v810"></a>SSMA v 8.10

SSMA for MySQL の v1.0 リリースには、わずかなパフォーマンス向上とバグ修正が含まれています。

## <a name="ssma-v89"></a>SSMA v 8.9

SSMA for MySQL の v1.0 リリースには、次の変更が含まれています。

* 空間型のデータ移行の修正
* プロジェクト名に特殊文字が含まれている問題の修正

## <a name="ssma-v88"></a>SSMA v 8.8

SSMA for MySQL のバージョン8.8 には次のものが含まれています。

* SQL Server オブジェクトの同期の安定性の向上
* 評価および変換中の GUI パフォーマンスの向上

## <a name="ssma-v87"></a>SSMA v 8.7

SSMA for MySQL のバージョン8.7 リリースでは、グラフィカルユーザーインターフェイスが多少修正され、パフォーマンスが向上しています。

さらに、SSMA for MySQL では、 `LIMIT` AZURE SQL を対象とする場合に句の変換が提供されるようになりました。

> [!IMPORTANT]
> SSMA v1.0 以降では、.NET 4.7.2 はインストールの前提条件です。 このバージョンをインストールする必要がある場合は、 [ここ](https://dotnet.microsoft.com/download/dotnet-framework/net472)からランタイムファイルをダウンロードできます。

## <a name="ssma-v86"></a>SSMA v 8.6

ユーザビリティとパフォーマンスを向上させるために設計された一連の修正に加えて、SSMA for MySQL のリリースは、ユーザーが変換されたコードで SSMA の拡張プロパティを省略できるようにする設定を追加することによって強化されています。

Ssma for MySQL でこの設定を利用するには、[**ツール**] [プロジェクト] [  >  **設定**]  >  **[全般**  >  **変換**] に移動し、[その **他**] の [**拡張プロパティを省略** する] 設定の値を **[はい]** に更新します。

![拡張プロパティの設定を省略する](../mysql/media/ssma-omit-extended-properties.png)

> [!IMPORTANT]
> SSMA v1.0 以降では、.NET 4.7.2 はインストールの前提条件です。 このバージョンをインストールする必要がある場合は、 [ここ](https://dotnet.microsoft.com/download/dotnet-framework/net472)からランタイムファイルをダウンロードできます。

## <a name="ssma-v85"></a>SSMA v1.0

SSMA for MySQL の v2.0 リリースは、SQL server での JSON 機能の Azure Active Directory 認証と基本的なサポートに加えて、ユーザビリティとパフォーマンスを向上させるように設計された一連の修正をサポートするように強化されています。

> [!IMPORTANT]
> SSMA v1.0 では、.NET 4.7.2 はインストールの前提条件です。 このバージョンをインストールする必要がある場合は、 [ここ](https://dotnet.microsoft.com/download/dotnet-framework/net472)からランタイムファイルをダウンロードできます。

## <a name="ssma-v84"></a>SSMA v 8.4

SSMA for MySQL の v2.0 リリースは、ユーザー補助の問題に対処し、最大インデックス列 (16 ではなく 32) に関連するバグを修正するように設計された、SQL Server 2016 以降のバージョンで強化されています。

> [!IMPORTANT]
> SSMA バージョン 8.4 7.4 では、.NET 4.5.2 はインストールの前提条件です。

## <a name="ssma-v83"></a>SSMA v 8.3

SSMA for MySQL の v2.0 リリースは、品質と変換のメトリックを向上させるように設計された対象の修正によって強化されています。 また、SSMA for MySQL のこのリリースでは、次のような修正が行われています。

* アクセシビリティの問題に対処します。
* SQL Server に種類の基本サポートを追加 `hierarchyid` します。

## <a name="ssma-v82"></a>SSMA v 8.2

SSMA for MySQL の v2.0 リリースは、品質と変換のメトリックを改善するように設計された修正プログラムのセットと、次のような修正によって強化されています。

* データ移行後の無効化されていない非クラスター化インデックスに関する問題。
* サイレントインストール中の .NET Framework の検出。
* 新しいバージョンがダウンロードされると発生する断続的なクラッシュ。

> [!NOTE]
> 自動更新に関する既知の問題により、SSMA v2.0 から v2.0 への更新が失敗する場合があります。 このエラーが発生した場合は、新しいバージョンをダウンロードし、手動でインストールしてください。

## <a name="ssma-v81"></a>SSMA v 8.1

SSMA for MySQL の v2.0 リリースは、品質と変換のメトリックを向上させるように設計された対象の修正によって強化されています。

> [!NOTE]
> 自動更新に関する既知の問題により、SSMA v2.0 から v2.0 への更新が失敗する場合があります。 このエラーが発生した場合は、新しいバージョンをダウンロードし、手動でインストールしてください。

## <a name="ssma-v80"></a>SSMA 8.0

SSMA for MySQL の v2.0 リリースは、品質と変換メトリックの向上を目的とした修正を対象として強化されています。 このリリースには、次の新機能も用意されています。

* ターゲットとしての **AZURE SQL Managed Instance** のサポート。 Azure SQL Managed Instance をターゲットとする新しいプロジェクトを作成できるようになりました。

  ![SQL MI プロジェクト](../media/ssma-newproject-sqldbmi.png)

* 変換後の **修正アドバイザー**。 詳細について [は、こちら](https://blogs.msdn.microsoft.com/datamigration/2019/02/17/%20accelerate-your-oracle-migrations-with-new-machine-learning-capabilities-in-ssma/)を参照してください。

* データベース/スキーマの事前選択。

  ソースに接続するときに、ユーザーは目的のデータベースまたはスキーマを選択できるようになりました。 移行を計画しているスキーマのみを選択すると、初期接続時に時間が節約され、全体的な SSMA パフォーマンスが向上します。

  ![SSMA フィルターオブジェクト](../media/ssma-filter-objects.png)

## <a name="ssma-v710"></a>SSMA v 7.10

SSMA for MySQL のバージョン7.10 リリースには、次の変更が含まれています。

* グローバル要件の変化に対応するために、追加のセキュリティとプライバシーの保護を提供するように設計された修正。
* 関数名と引数リストの間のスペースの変換を修正します。

## <a name="ssma-v79"></a>SSMA v 7.9

SSMA for MySQL の v1.0 リリースには、次の変更が含まれています。

* 品質と変換のメトリックを向上させる対象の修正。
* 空間データ型を MySQL から Azure SQL Database に移行するための部分的なサポート。
* SSMA コマンドラインでデータ型マッピングとプロジェクト設定を変更するためのサポート。
* SQL Server Integration Services (SSIS) を使用したデータ移行のサポート。 スキーマを変換した後、右クリックコンテキストメニューオプションを使用して SSIS パッケージを作成できます。
* SSMA の [Azure SQL Database 接続] ダイアログも、完全修飾サーバー名を指定するように変更されています。 以前のバージョンの SSMA では、プロジェクト設定内で Azure SQL Database プレフィックスを明示的に記述する必要がありました。

## <a name="ssma-v78"></a>SSMA v 7.8

SSMA for MySQL の v1.0 リリースには、次の変更が含まれています。

* [プロジェクトの設定] で強調表示されている型マッピングを変更します。
* ユーザーがテレメトリを無効にする機能。

## <a name="ssma-v77"></a>SSMA v 7.7

SSMA for MySQL のバージョン7.7 リリースには、次の変更が含まれています。

* SSMA for MySQL は、品質と変換のメトリックを向上させる対象の修正によって強化されています。
* 一般的な要求に基づいて、SSMA for MySQL の32ビットバージョンは戻ります。 以前の実装と比較して (v2.0 より前)、インストーラーパッケージは2つありますが、サイドバイサイドでインストールすることはできません。 そのため、使用している接続コンポーネントに基づいて、最適なバージョンを選択する必要があります。 可能であれば、常に64ビットバージョンを使用することをお勧めします。
* SSMA for MySQL に ODBC 接続文字列接続モードが用意され、MySQL と互換性のあるサードパーティ製の ODBC ドライバーを使用できるようになりました。

## <a name="ssma-v76"></a>SSMA v1.0

SSMA for MySQL の v1.1 リリースは、品質と変換のメトリックを向上させ、SQL Server 2017 (パブリックプレビュー) をサポートする対象の修正によって強化されています。 Windows および Linux での SQL Server 2017 のサポートはパブリックプレビュー段階であり、運用環境への移行には使用しないでください。

## <a name="ssma-v75"></a>SSMA v 7.5

SSMA for MySQL のバージョン7.5 リリースは、障碍のある方にとってアクセシビリティを向上させるためにいくつかの機能強化が施されています。

## <a name="ssma-v74"></a>SSMA v1.0

SSMA for MySQL の v2.0 リリースには、次の変更が含まれています。

* [ **クエリタイムアウト** ] オプションは、ソースおよびターゲットでのスキーマオブジェクトの検出中に使用できるようになりました。

    ![クエリタイムアウトオプション](../media/query-timeout_red.png)
* お客様からのフィードバックに基づいて、品質と換算のメトリックが修正されました。

> [!IMPORTANT]
> .NET 4.5.2 は、SSMA v2.0 をインストールするための前提条件です。 さらに、v2.0 以降では、SSMA の32ビットバージョンは廃止されています。

## <a name="ssma-v73"></a>SSMA version 7.3

SSMA for MySQL の version 7.3 リリースには、次の変更が含まれています。

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

SSMA for MySQL のバージョン7.2 には、次の変更が含まれています。

* お客様からのフィードバックに基づいて、対象の修正を含む品質と変換のメトリックが向上しました。
* テレメトリの機能強化により、顧客の問題をトラブルシューティングし、SSMA のコンバージョン率を向上させるためのデータポイントが向上しました。

## <a name="ssma-v71"></a>SSMA 7.1

SSMA for MySQL の v2.0 リリースには、次の変更が含まれています。

* Windows と Linux CTP1 の SQL Server 2017 は、現在、移行のためにサポートされているターゲットプラットフォームです。 この機能は technical preview にあり、SQL server を対象とするスキーマとデータの移動を可能にします。
* SSMA は、最新バージョンの SSMA を利用できるようになったらすぐにダウンロードできる自動更新をサポートするようになりました。
* SSMA のインストール可能なバイナリが Windows インストーラーパッケージファイル (.msi) を介して配信されるようになりました。

## <a name="may-2016"></a>2016 年 5 月

SSMA for MySQL の2016年5月のリリースには、次の変更が含まれています。

* SQL Server 2016 のサポートが追加されました。
* パーサーと競合回避モジュールが向上しました。
* .NET 2.0 のインストーラーチェックが削除されました。
* .NET 3.5 から .NET 4.0 への拡張パックの依存関係を更新しました。
* MySql の既定の BigInt 型マッピングを修正します。
* `save-project` `open-project` Ssma コンソールのコマンドとコマンドを修正します。
* `securepassword`SSMA コンソールのコマンドを修正します。
* 初期読み込みのオブジェクトのカウントを固定します。
* MsSql オブジェクトの読み込みを修正した。
* グローバル設定のバグを修正した。

## <a name="march-2016"></a>2016 年 3 月

SSMA for MySQL の2016年3月のプレビューリリースでは、SQL Server 2016 への移行のサポートが追加されています。
  
## <a name="january-2016"></a>2016 年 1 月

SSMA for MySQL の2016年1月のメンテナンスリリースには、次の変更が含まれています。

* SSMA (RFC 5706203) に [ログの表示] メニュー項目が追加されました。
* テレメトリを追加しました。
  
## <a name="july-2014"></a>2014 年 7 月

SSMA for MySQL の2014年7月のリリースには、次の変更が含まれています。
  
* Azure SQL Database コード変換が改善されました。
* 拡張パックの機能は、Azure SQL Database をサポートするためにスキーマに移行されました。
* 10,000 を超えるオブジェクトがあるデータベースでテストされたパフォーマンスの向上。
* 多数のオブジェクトを処理するための UI の機能強化。
* 既知の LOB スキーマの強調表示 (そのため、変換では無視できます)。
* 変換速度が向上しました。
* UI にオブジェクト数を表示します。
* 25% を超えるレポートサイズの削減。
* 未解析のコンストラクトのエラーメッセージが改善されました。
  
## <a name="april-2014"></a>2014 年 4 月

SSMA for MySQL の2014年4月のリリースには、次の変更が含まれています。
  
* SQL Server 2014 のサポートが追加されました。
* Azure への変換に関するバグを修正した。
* IE 10 の非表示レポートページに関するバグを修正した。
  
## <a name="july-2011"></a>2011 年 7 月

SSMA for MySQL の2011年7月のリリースには、次の変更が含まれています。
  
* からへの変換のサポート `LIMIT` [!INCLUDE [ssSQL11](../../includes/sssql11-md.md)] `OFFSET` 。
* データ移行中のエラー報告の向上。
  
## <a name="april-2011"></a>2011年4月

SSMA for MySQL の2011年4月のリリースには、次の変更が含まれています。
  
* [!INCLUDE [ssVersion2005](../../includes/ssversion2005-md.md)]、、 [!INCLUDE [ssSQL10](../../includes/sssql10-md.md)] および Azure SQL をサポートする "ssma for MySQL" の単一インストール可能 [!INCLUDE [ssSQL11](../../includes/sssql11-md.md)] 。
* 接続する権限 [!INCLUDE [ssSQL11](../../includes/sssql11-md.md)] です。
* 拡張されたクライアント側のデータ移行エンジンで、データの並列移行がサポートされています。
* 単純復旧モデルと一括ログ復旧モデルにより、データ移行のパフォーマンスが向上しました。
* SSMA for MySQL コンソールのバージョンでは、旧バージョンとの互換性がサポートされています。 以前のバージョンで作成されたプロジェクトを SSMA v1.0 に開くことができます。
* SSMA for MySQL version 5.0 製品は、旧バージョンの SSMA 製品とサイドバイサイド (SxS) でインストールできます。
  
## <a name="july-2010"></a>2010 年 7 月

SSMA for MySQL の2010年7月のリリースには、次の機能が含まれています。
  
**1. ユーザーインターフェイスの機能強化:**

* MySQL データベースオブジェクトの [SQL モード] タブ
* MySQL データベースオブジェクトの [設定] タブ
* MySQL テーブルの [データ] タブ
* 変換および移行ページのプロジェクト設定が更新されました
* テーブルレベルでの ' データ移行設定 '
  
**2. MySQL および SQL Server に接続するための機能強化:**
  
* MySQL での SSL 接続
* SQL Server での暗号化された接続
  
**3. MySQL メタベースエクスプローラーの機能強化:**
  
* すべての MySQL データベースオブジェクトとそれぞれのタブを読み込みます。
  
**4. オブジェクト変換の機能強化:**
  
* MySQL メタベースオブジェクトの変換 (プロシージャ、関数、ビュー、トリガー、ステートメント)。
* テーブルでの空間データ型のサポートが制限されています。
* MySQL 関数を SQL Server ストアドプロシージャに変換するオプション
* オブジェクトの変換中に SQL モードと文字セットのマッピングを適用するオプション
  
**5. データ移行の機能強化:**  
  
* Server-Side と Client-Side の両方のデータ移行エンジンを使用したデータ移行のサポート
* 空間データ移行のサポート
* テーブルのデータ移行のためのカスタム SQL
  
**6. SSMA for MySQL コンソール:**  
  
* SSMA for MySQL のサポートコンソール機能  
* Script-Level インターフェイスのサポート  
  
## <a name="january-2010"></a>2010年1月

SSMA for MySQL の2010年1月のリリースは、最初のリリースでした。 これには次の機能が含まれていました。
  
* オンプレミスの SQL Server と Azure SQL の両方への移行のサポートが追加されました。  
  
* **機能スナップショット:** MySQL のテーブル/インデックス/制約のスキーマとデータの移行。
