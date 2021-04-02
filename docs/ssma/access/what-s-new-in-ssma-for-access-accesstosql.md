---
title: SSMA for Access の新機能 (アクセス可能な Sql) |Microsoft Docs
description: 各リリースのアクセス用 (SSMA) の SQL Server Migration Assistant (SSMA) に対する変更について説明します。
author: nahk-ivanov
ms.prod: sql
ms.custom: ''
ms.date: 12/17/2020
ms.reviewer: ''
ms.technology: ssma
ms.topic: conceptual
ms.assetid: a24d3fc0-6911-4bfa-828a-197abf222e02
ms.author: alexiva
ms.openlocfilehash: 446d436a01a222c85e0e0e60083b973a7e16413c
ms.sourcegitcommit: 0b37eb7aef2f358f80867cd13830dd6683da8d85
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105981088"
---
# <a name="whats-new-in-ssma-for-access-accesstosql"></a>SSMA for Access の新機能 (アクセス可能な Sql)

この記事では、各リリースでのアクセス変更の SQL Server Migration Assistant (SSMA) を示します。

## <a name="ssma-v818"></a>SSMA v 8.18

SSMA for Access の v 8.18 リリースには、次の変更が含まれています。

* わずかなパフォーマンス向上とバグ修正

## <a name="ssma-v817"></a>SSMA v 8.17

SSMA for Access の v 8.17 リリースには、次の変更が含まれています。

* 最新のエディターを使用して SQL テキストを表示するように HTML 評価レポートを更新する

## <a name="ssma-v816"></a>SSMA v 8.16

SSMA for Access の v 8.16 リリースには、次の変更が含まれています。

* HTML 変換レポートでクエリの SQL テキストを表示する
* レガシパーサーのサポートを削除する
* データベースから更新されていないオブジェクトの問題を修正します

## <a name="ssma-v815"></a>SSMA v 8.15

ユーザー補助機能の強化に加えて、SSMA for Access の v 8.15 リリースには、次の変更が含まれています。

* 外部キーに対して自動作成されたインデックスを無視する
* 改良評価レポートを最新のブラウザーで動作させる
* Azure AD 認証にデータベースによって提供される権限を使用する
* ファイルから読み込まれたステートメントの名前付けを改善する

## <a name="ssma-v814"></a>SSMA v 8.14

障碍のある方にとってアクセシビリティを向上させるための機能強化に加えて、SSMA for Access のリリースでは、プロジェクトのメタデータに完全なソース/ターゲットサーバーバージョンが格納されるようになったため、プロジェクトのアップグレードが必要になりました。

## <a name="ssma-v813"></a>SSMA v 8.13

SSMA for Access の v 8.13 リリースには、次の変更が含まれています。

* `ORDER BY`句を使用した変換の修正 `UNION`
* フィルター選択された一意のインデックスのサポート
* プロシージャと関数の呼び出しを変換するときに、暗黙的な型キャストを検討します。

## <a name="ssma-v812"></a>SSMA v 8.12

SSMA for Access の v1.0 リリースには、次の変更が含まれています。

* `BigInt`( `Large Number` ) データ型のサポート
* 列の型の解決の向上
* 列検証ルールの変換の改善
* データ移行に使用可能な最新の ACE OLE DB プロバイダーの使用

## <a name="ssma-v811"></a>SSMA v 8.11

SSMA for Access の v 8.11 リリースには、次の変更が含まれています。

* MSAL.NET library を使用して対話型 Azure Active Directory 認証を行う

## <a name="ssma-v810"></a>SSMA v 8.10

SSMA for Access のバージョン8.10 リリースには、わずかなパフォーマンス向上とバグ修正が含まれています。

## <a name="ssma-v89"></a>SSMA v 8.9

SSMA for Access の v1.0 リリースには、次の変更が含まれています。

* 自己参照型クエリの変換の改善
* プロジェクト名に特殊文字が含まれている問題の修正

## <a name="ssma-v88"></a>SSMA v 8.8

SSMA for Access のバージョン8.8 には次のものが含まれます。

* SQL Server オブジェクトの同期の安定性の向上
* 評価および変換中の GUI パフォーマンスの向上
* 変換のパフォーマンスをさらに向上させるための新しいアクセス構文パーサー

## <a name="ssma-v87"></a>SSMA v 8.7

SSMA for Access のリリースでは、クエリでの関数の変換が改善され、 `IIF` グラフィカルユーザーインターフェイスでの修正とパフォーマンスの向上が向上しています。

> [!IMPORTANT]
> SSMA v1.0 以降では、.NET 4.7.2 はインストールの前提条件です。 このバージョンをインストールする必要がある場合は、 [ここ](https://dotnet.microsoft.com/download/dotnet-framework/net472)からランタイムファイルをダウンロードできます。

## <a name="ssma-v86"></a>SSMA v 8.6

ユーザビリティとパフォーマンスを向上させるために設計された一連の修正に加え、SSMA の SSMA リリースは、ユーザーが変換されたコードで SSMA 拡張プロパティを省略できるようにする設定を追加することによって強化されています。

Ssma for Access でこの設定を利用するには、[**ツール**] [  >  **プロジェクト設定**] [  >  **全般**  >  **変換**] の順に移動し、[その **他**] の [**拡張プロパティを省略**] 設定の値を **[はい]** に更新します。

![拡張プロパティの設定を省略する](../access/media/ssma-omit-extended-properties.png)

> [!IMPORTANT]
> SSMA v1.0 以降では、.NET 4.7.2 はインストールの前提条件です。 このバージョンをインストールする必要がある場合は、 [ここ](https://dotnet.microsoft.com/download/dotnet-framework/net472)からランタイムファイルをダウンロードできます。

## <a name="ssma-v85"></a>SSMA v1.0

SSMA for Access の v1.0 リリースは、SQL server での JSON 機能の Azure Active Directory 認証と基本的なサポートに加え、使いやすさとパフォーマンスを向上させるように設計された一連の修正をサポートすることによって強化されています。

さらに、SSMA for Access では、複数の標準関数 (、など) の変換がサポートされるようになりました `ISNULL` `IIF` 。

> [!IMPORTANT]
> SSMA v1.0 では、.NET 4.7.2 はインストールの前提条件です。 このバージョンをインストールする必要がある場合は、 [ここ](https://dotnet.microsoft.com/download/dotnet-framework/net472)からランタイムファイルをダウンロードできます。

## <a name="ssma-v84"></a>SSMA v 8.4

SSMA for Access のリリースは、ユーザー補助の問題に対処し、最大インデックス列 (16 ではなく 32) に関連するバグを修正して、SQL Server 2016 以降のバージョンに対応するように設計された、対象の修正によって強化されています。

> [!IMPORTANT]
> SSMA バージョン 8.4 7.4 では、.NET 4.5.2 はインストールの前提条件です。

## <a name="ssma-v83"></a>SSMA v 8.3

SSMA for Access の v2.0 リリースは、品質と変換メトリックの向上を目的とした修正を適用して強化されています。 また、SSMA for Access のこのリリースでは、次のような修正が行われています。

* アクセシビリティの問題に対処します。
* SQL Server に種類の基本サポートを追加 `hierarchyid` します。

## <a name="ssma-v82"></a>SSMA v 8.2

SSMA for Access のリリースは、品質と変換メトリックの向上を目的とした修正が適用され、強化されています。

> [!NOTE]
> 自動更新に関する既知の問題により、SSMA v2.0 から v2.0 への更新が失敗する場合があります。 このエラーが発生した場合は、新しいバージョンをダウンロードし、手動でインストールしてください。

## <a name="ssma-v81"></a>SSMA v 8.1

SSMA for Access の v2.0 リリースは、品質と変換メトリックの向上を目的とした修正を対象として強化されています。

> [!NOTE]
> 自動更新に関する既知の問題により、SSMA v2.0 から v2.0 への更新が失敗する場合があります。 このエラーが発生した場合は、新しいバージョンをダウンロードし、手動でインストールしてください。

## <a name="ssma-v80"></a>SSMA 8.0

SSMA for Access の v2.0 リリースは、品質と変換メトリックの向上を目的とした修正を対象として強化されています。 このリリースには、次の新機能も用意されています。

* ターゲットとしての **AZURE SQL Managed Instance** のサポート。 Azure SQL Managed Instance をターゲットとする新しいプロジェクトを作成できるようになりました。

  ![SQL MI プロジェクト](../media/ssma-newproject-sqldbmi.png)

* 変換後の **修正アドバイザー**。 詳細について [は、こちら](https://techcommunity.microsoft.com/t5/Microsoft-Data-Migration/Accelerate-your-Oracle-migrations-with-new-machine-learning/ba-p/368733)を参照してください。

* データベース/スキーマの事前選択。

    ソースに接続するときに、ユーザーは目的のデータベースまたはスキーマを選択できるようになりました。 移行を計画しているスキーマのみを選択すると、初期接続時に時間が節約され、全体的な SSMA パフォーマンスが向上します。

   ![SSMA フィルターオブジェクト](../media/ssma-filter-objects.png)

## <a name="ssma-v710"></a>SSMA v 7.10

SSMA for Access のバージョン7.10 リリースは、グローバル要件の変化に対応するための追加のセキュリティとプライバシーの保護を提供するように設計された修正によって強化されています。

## <a name="ssma-v79"></a>SSMA v 7.9

SSMA for Access のバージョン7.9 リリースには、次の変更が含まれています。

* 品質と変換のメトリックを向上させる対象の修正。
* SSMA コマンドラインでデータ型マッピングとプロジェクト設定を変更するためのサポート。
* SSMA の [Azure SQL Database 接続] ダイアログも、完全修飾サーバー名を指定するように変更されています。 以前のバージョンの SSMA では、プロジェクト設定内で Azure SQL Database プレフィックスを明示的に記述する必要がありました。

## <a name="ssma-v78"></a>SSMA v 7.8

SSMA for Access の v1.0 リリースには、次の変更が含まれています。

* [プロジェクトの設定] で強調表示されている型マッピングを変更します。
* ユーザーがテレメトリを無効にする機能。

## <a name="ssma-v77"></a>SSMA v 7.7

SSMA for Access のバージョン7.7 リリースには、次の変更が含まれています。

* 品質と変換のメトリックを向上させる対象の修正。
* 一般的な要求に基づいて、32ビット版の SSMA for Access が返されます。 以前の実装と比較して (v2.0 より前)、インストーラーパッケージは2つありますが、サイドバイサイドでインストールすることはできません。 そのため、使用している接続コンポーネントに基づいて、最適なバージョンを選択する必要があります。 可能であれば、常に64ビットバージョンを使用することをお勧めします。

## <a name="ssma-v76"></a>SSMA v1.0

SSMA for Access のリリースは、品質と変換のメトリックを向上させ、SQL Server 2017 (パブリックプレビュー) をサポートする対象の修正によって強化されています。 Windows および Linux での SQL Server 2017 のサポートはパブリックプレビュー段階であり、運用環境への移行には使用しないでください。

## <a name="ssma-v75"></a>SSMA v 7.5

SSMA for Access のバージョン7.5 リリースは、障碍のある方にとってアクセシビリティを向上させるためにいくつかの機能強化が施されています。

## <a name="ssma-v74"></a>SSMA v1.0

SSMA for Access のバージョン7.4 リリースには、次の変更が含まれています。

* [ **クエリタイムアウト** ] オプションは、ソースおよびターゲットでのスキーマオブジェクトの検出中に使用できるようになりました。

  ![クエリタイムアウトオプション](../media/query-timeout_red.png)

* お客様からのフィードバックに基づいて、品質と換算のメトリックが修正されました。

  > [!IMPORTANT]
  > .NET 4.5.2 は、SSMA v2.0 をインストールするための前提条件です。 さらに、v2.0 以降では、SSMA の32ビットバージョンは廃止されました。

## <a name="ssma-v73"></a>SSMA version 7.3

SSMA for Access のバージョン7.3 リリースには、次の変更が含まれています。

* お客様からのフィードバックに基づいて、対象の修正を含む品質と変換のメトリックが向上しました。
* SSMA 拡張フレームワークは、次の項目を介して公開されます。
  * SQL Server Data Tools (SSDT) プロジェクトに機能をエクスポートします。
    * SSMA から SSDT プロジェクトにスキーマスクリプトをエクスポートできるようになりました。 スキーマスクリプトを使用すると、追加のスキーマ変更を行ったり、データベースを配置したりすることができます。

        ![SSDT プロジェクトの名前を付けて保存コマンド](../media/export-schema-scripts_red.png)
  * カスタム変換を実行するために SSMA で使用できるライブラリ。
    * SSMA によって以前に処理されなかったカスタム構文変換と変換を処理できるコードを作成できるようになりました。
      * 変換用のサンプルプロジェクトと共にカスタムコンバーターを構築する方法については、 [SQL Server Migration Assistant の変換機能の拡張](https://techcommunity.microsoft.com/t5/Microsoft-Data-Migration/Extending-SQL-Server-Migration-Assistant-s-conversion/ba-p/1004181)に関するブログ記事を参照してください。

## <a name="ssma-v72"></a>SSMA v 7.2

SSMA for Access のバージョン7.2 には、次の変更が含まれています。

* お客様からのフィードバックに基づいて、対象の修正を含む品質と変換のメトリックが向上しました。
* テレメトリの機能強化により、顧客の問題をトラブルシューティングし、SSMA のコンバージョン率を向上させるためのデータポイントが向上しました。

## <a name="ssma-v71"></a>SSMA 7.1

SSMA for Access のバージョン7.1 リリースには、次の変更が含まれています。

* Windows と Linux CTP1 の SQL Server 2017 は、現在、移行のためにサポートされているターゲットプラットフォームです。 この機能は technical preview であり、SQL server を対象とするスキーマとデータの移動をサポートしています。
* SSMA は、最新バージョンの SSMA を利用できるようになったらすぐにダウンロードできる自動更新をサポートするようになりました。
* SSMA のインストール可能なバイナリが Windows インストーラーパッケージファイル (.msi) を介して配信されるようになりました。

## <a name="may-2016"></a>2016 年 5 月

SSMA for Access の2016年5月のリリースには、次の変更が含まれています。

* SQL Server 2016 の公式サポートを追加しました。
* .NET 2.0 のインストーラーチェックが削除されました。
* `save-project` `open-project` Ssma コンソールのコマンドとコマンドを修正します。
* `securepassword`SSMA コンソールのコマンドを修正します。
* 初期読み込みのオブジェクトのカウントを固定します。
* アクセス用の UI タブのデータ読み込みを修正したテーブル。
* グローバル設定のバグを修正した。

## <a name="march-2016"></a>2016 年 3 月

SSMA for Access の2016年3月のプレビューリリースでは、SQL Server 2016 への移行のサポートが追加されます。

## <a name="january-2016"></a>2016 年 1 月

SSMA for Access の2016年1月のメンテナンスリリースには、次の変更が含まれています。

* GUID フィールドの既定の無効な関数 (RFC 3894811) を修正しています。
* SQL Database (Azure) にレコードをインポートするときにシステムが応答しなくなる問題を修正した (RFC 4919573)。
* SSMA (RFC 5706203) に [ログの表示] メニュー項目が追加されました。
* テレメトリを追加しました。

## <a name="july-2014"></a>2014 年 7 月

SSMA for Access の2014年7月のリリースには、次の変更が含まれています。

* Azure SQL Database コード変換が改善されました。
* 拡張パックの機能をスキーマに移動し、Azure SQL Database をサポートしました。
* 10,000 を超えるオブジェクトを含むデータベースのパフォーマンスの向上をテストしました。
* 多数のオブジェクトを処理するための UI の機能強化が追加されました。
* "既知の" LOB スキーマの強調表示のサポートが追加されました (そのため、変換では無視できます)。
* 変換速度が向上しました。
* UI でオブジェクト数を表示するためのサポートを追加しました。
* レポートのサイズが25% を超えています。
* 未解析のコンストラクトのエラーメッセージが改善されました。

## <a name="april-2014"></a>2014 年 4 月

SSMA for Access の2014年4月のリリースには、次の変更が含まれています。

* MS SQL Server 2014 のサポートが追加されました。
* Azure への変換に関連するバグを修正した。
* IE 10 の非表示レポートページに関連するバグが修正されています。

## <a name="january-2012"></a>2012 年 1 月

SSMA for Access の2012年1月リリースには、次の変更が含まれています。

* 移行後に MS Access のリンクテーブルのユーザー名とパスワードを保持しないオプションが用意されています。
* 循環参照の連鎖アクションを No Action に設定します。
* 循環参照の cascade アクションが No Action に設定されていることを示す適切なメッセージを提供しました。

## <a name="july-2011"></a>2011 年 7 月

SSMA for Access の2011年7月のリリースでは、データ移行中に改善されたエラーレポートが追加されます。

## <a name="april-2011"></a>2011年4月

SSMA for Access の2011年4月のリリースには、次の変更が含まれています。

* "Ssma for Access" のインストール可能な単一のを追加しました。これは [!INCLUDE [ssVersion2005](../../includes/ssversion2005-md.md)] 、、、 [!INCLUDE [ssSQL10](../../includes/sssql10-md.md)] および Azure SQL をサポートし [!INCLUDE [ssSQL11](../../includes/sssql11-md.md)] ています。
* に接続する機能が追加されました [!INCLUDE [ssSQL11](../../includes/sssql11-md.md)] 。
* 旧バージョンとの互換性のために SSMA for Access コンソールのバージョンサポートが追加されました。 以前のバージョンで作成されたプロジェクトを SSMA v1.0 に開くことができます。
* Ssma 製品の旧バージョンとサイドバイサイド (SxS) をインストールする機能が追加されました。

## <a name="july-2010"></a>2010 年 7 月

SSMA for Access の2010年7月のリリースには、次の変更が含まれています。

* SQL Server 2008 R2 および Azure SQL への移行のサポートが追加されました。
* SQL Server と Azure SQL の両方にセキュリティで保護された接続を追加しました。
* Access 2010 データベースのサポートが追加されました。
* コマンドライン実行用の新しい SSMA コンソールアプリケーションが追加されました。
* SQL Server データ型のサポートを追加しました `DateTime2` 。

## <a name="june-2008"></a>2008年6月

SSMA for Access の2008年6月リリースでは、Access 2007 データベースのサポートが追加されます。

## <a name="may-2007"></a>5月2007

SSMA for Access の2007年5月のリリースには、次の変更が含まれています。

* ワークグループポリシーを使用する Access データベースのサポートが追加されました。
* SQL Server メタデータエクスプローラーから変換されたオブジェクトを削除する機能を提供します。
* SQL Server 形式の SQL モードでユーザーが入力したコメントのサポートが追加されました。
* オブジェクト変換の機能強化が追加されました。

## <a name="november-2006"></a>2006年11月

SSMA for Access の11月2006リリースには、次の変更が含まれています。

* 新しいデータベース移行ウィザードが追加されました。このウィザードでは、へのアクセスからへの1つのデータベースの移行を段階的に実行でき [!INCLUDE [ssNoVersion](../../includes/ssnoversion-md.md)] ます。
* Access データベースを変換し、変換されたオブジェクトをに読み込んで、 [!INCLUDE [ssNoVersion](../../includes/ssnoversion-md.md)] データを [!INCLUDE [ssNoVersion](../../includes/ssnoversion-md.md)] すべて1回の手順で移行する、新しい Convert、Load、Migrate コマンドを追加しました。
* クエリの移行が改善されました。 クエリの移行により、より多くの SELECT クエリがビューに変換されるようになりました。 詳細については、「 [Access データベースオブジェクトの変換](converting-access-database-objects-accesstosql.md)」を参照してください。
* [ [!INCLUDE [ssNoVersion](../../includes/ssnoversion-md.md)] **テーブル** ] タブでテーブルとインデックスのプロパティを編集する機能が追加されました。
* 新しいグローバル設定を追加しました:
  * エディターウィンドウで行番号を表示するように選択できます。
  * SSMA を構成して、重複するオブジェクトの置換を求めるメッセージを表示したり、スキーマの変換中に重複するオブジェクトを常に置き換えることができます。
* 複雑なクエリにワイルドカードが含まれている場合に SSMA が警告を表示するかどうかを指定できる新しい変換オプションが追加されました。

## <a name="july-2006"></a>2006年7月

SSMA for Access の2006年7月のリリースは、最初のリリースでした。
