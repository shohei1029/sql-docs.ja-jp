---
description: SQL Server への新しい ODBC 接続を作成する目的でデータ ソース ウィザードで詳細オプションを定義する方法について学びます。
title: データ ソース ウィザード画面 4 (ODBC Driver for SQL Server)
ms.custom: ''
ms.date: 09/27/2017
ms.prod: sql
ms.prod_service: connectivity
ms.reviewer: ''
ms.technology: connectivity
ms.topic: conceptual
ms.assetid: 76326eeb-1144-4b9f-85db-50524c655d30
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: 5152012e385e5e28ebccc967eb30af42852cf640
ms.sourcegitcommit: 00af0b6448ba58e3685530f40bc622453d3545ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104673336"
---
# <a name="data-source-wizard-screen-4"></a>データ ソース ウィザード画面 4

SQL Server メッセージに使用する言語、文字セットの翻訳、および ODBC Driver for SQL Server で地域設定を使用するかどうかを指定します。 また、実行時間の長いクエリのログ記録、およびドライバー統計情報の設定を制御することもできます。

## <a name="options"></a>オプション

### <a name="change-the-language-of-sql-server-system-messages-to"></a>[SQL Server のシステム メッセージを以下の言語に変更する]

SQL Server の各インスタンスには、システム メッセージの複数のセットを含めることができ、各セットは異なる言語 (英語、スペイン語、フランス語など) で指定されています。 システム メッセージの複数のセットを含むサーバーに対してデータ ソースを定義する場合、システム メッセージに使用する言語を指定できます。 一覧から言語を選択します。 SQL Server に言語が 1 つしかインストールされてない場合、このオプションは使用できません。

### <a name="use-strong-encryption-for-data"></a>[データに強力な暗号を使用する]

オンにすると、この DSN を使用して確立された接続を介して渡されたデータが暗号化されます。 このチェック ボックスがオフの場合でも、既定では、ログインが暗号化されます。

### <a name="trust-server-certificate"></a>[サーバー証明書を信頼する]

このオプションは、 **[データに強力な暗号化を使用する]** が有効になっている場合にのみ適用されます。 このオプションを選択すると、サーバーの証明書は、サーバーの正しいホスト名を持ち、信頼された証明機関によって発行されたことを検証されません。

### <a name="perform-translation-for-character-data"></a>[文字データを変換する]

このチェック ボックスをオンにすると、ODBC Driver for SQL Server により、クライアント コンピューターと SQL Server の間で送信された ANSI 文字列が Unicode を使用して変換されます。 ODBC ドライバーでは、SQL Server コード ページとクライアント コンピューターの Unicode の間で変換することもあります。 このオプションを使用するには、SQL Server で使用されるコード ページがクライアント コンピューターで使用できるコード ページの 1 つである必要があります。

このチェック ボックスがオフの場合、ANSI 文字列の拡張文字は、クライアント アプリケーションとサーバーの間で送信されても変換されません。 クライアント コンピューターが SQL Server コード ページと異なる ANSI コード ページ (ACP) を使用している場合、ANSI 文字列の拡張文字の解釈が正しく行われない可能性があります。 SQL Server が使用している ACP のコード ページをクライアント コンピューターで使用している場合、拡張文字は正しく解釈されます。

### <a name="use-regional-settings-when-outputting-currency-numbers-dates-and-times"></a>[出力時の通貨、数値、日付、時刻の形式にはシステムの地域設定を使用する]

ドライバーが文字出力文字列に通貨、数値、日付、時刻の形式にクライアント コンピューターの地域設定を使用するよう指定します。 ドライバーは、データ ソースを介して接続しているユーザーの Windows ログイン アカウントの既定の地域設定を使用します。 このオプションは、データを処理するアプリケーションではなく、データを表示するだけのアプリケーションに選択してください。

### <a name="save-long-running-queries-to-the-log-file"></a>[実行時間が長いクエリを以下のログ ファイルに保存する]

ドライバーが、保存するクエリの **最短所用時間** の値よりも長い時間がかかっているすべてのクエリをログに記録するように指定します。 実行時間の長いクエリのログは、指定されたファイルに記録されます。 ログ ファイルを指定するには、ボックスに完全なパスとファイル名を入力するか、 **[参照]** を選択し、既存のファイル ディレクトリ内を移動してログ ファイルを選択します。

### <a name="long-query-time-milliseconds"></a>[保存するクエリの最短所要時間 (ミリ秒)]

実行時間の長いクエリをログに記録するためのしきい値をミリ秒単位で指定します。 このミリ秒単位の時間より実行時間の長いクエリはすべてログに記録されます。

### <a name="log-odbc-driver-statistics-to-the-log-file"></a>[ODBC ドライバーの統計情報ログを以下のログ ファイルに保存する]

統計情報がログに記録されるよう指定します。 統計情報のログは、指定したファイルに記録されます。 ログ ファイルを指定するには、ボックスに完全なパスとファイル名を入力するか、 **[参照]** を選択し、既存のファイル ディレクトリ内を移動してログ ファイルを選択します。

統計情報ログはタブで区切られており、タブ区切りのファイルをサポートする Microsoft Excel やその他のアプリケーションで分析できます。

### <a name="connect-retry-count"></a>接続再試行回数

失敗した接続試行を再試行する回数を指定します。

### <a name="connect-retry-interval-seconds"></a>接続再試行間隔(秒)

接続再試行間の秒数を指定します。 このオプションと **[接続再試行回数]** オプションの操作の詳細については、「[Windows ODBC ドライバーの接続の回復性](connection-resiliency-in-the-windows-odbc-driver.md)」を参照してください。

### <a name="back"></a>戻る

ウィザードの前のページに戻るには、このボタンを選択します。

### <a name="finish"></a>[完了]

この画面で指定された情報が完了すると、 **[完了]** を選択できます。 このウィザードのこの画面および他の画面で指定されたすべての属性を使用して DSN が作成され、新しく作成された DSN をテストする機会が与えられます。

## <a name="next-steps"></a>次のステップ

[データ ソース ウィザード画面 3](dsn-wizard-3.md)
