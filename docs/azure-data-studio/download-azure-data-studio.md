---
title: Azure Data Studio のダウンロードとインストール
description: Windows、macOS、Linux 向けの Azure Data Studio をダウンロードし、インストールします。 この記事では、リリース日付、バージョン番号、システム要件、ダウンロード リンクを提供します。
ms.prod: azure-data-studio
ms.technology: azure-data-studio
ms.topic: overview
author: yualan
ms.author: alayu
ms.reviewer: maghan
ms.custom: seodec18
ms.date: 3/17/2021
ms.openlocfilehash: 0a88fcbdd027215b8ce1bcd6242f48833ec0c6b2
ms.sourcegitcommit: bf7577b3448b7cb0e336808f1112c44fa18c6f33
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104610821"
---
# <a name="download-and-install-azure-data-studio"></a>Azure Data Studio のダウンロードとインストール

Azure Data Studio とは、Windows、macOS、Linux 上のデータ プラットフォームがオンプレミスとクラウドである、データ プロフェッショナルを対象にした、クロスプラットフォーム データベース ツールです。

Azure Data Studio では、IntelliSense、コード スニペット、ソース管理の統合、統合されたターミナルを含む最新のエディター エクスペリエンスが提供されています。 これは、データ プラットフォームのユーザーを念頭に置いて設計されており、クエリ結果セットのグラフ化機能とカスタマイズ可能なダッシュボードが組み込まれています。 Azure Data Studio の詳細については、「[Azure Data Studio とは](what-is-azure-data-studio.md)」を参照してください。

## <a name="download-the-latest-release"></a>最新リリースをダウンロードする

| プラットフォーム | ダウンロード | リリース日 | Version |
|----------|----------|--------------|---------|
| Windows | [ユーザー インストーラー (推奨)](https://go.microsoft.com/fwlink/?linkid=2157460)<br>[システム インストーラー](https://go.microsoft.com/fwlink/?linkid=2157459)<br>[.zip](https://go.microsoft.com/fwlink/?linkid=2157458) | 2021 年 3 月 17 日 | 1.27.0 |
| macOS | [.zip](https://go.microsoft.com/fwlink/?linkid=2157456) | 2021 年 3 月 17 日 | 1.27.0 |
| Linux | [.deb](https://go.microsoft.com/fwlink/?linkid=2157352)<br>[.rpm](https://go.microsoft.com/fwlink/?linkid=2157248)<br>[.tar.gz](https://go.microsoft.com/fwlink/?linkid=2157353) | 2021 年 3 月 17 日 | 1.27.0 |

**最新リリースに関する詳細については、[リリース ノート](./release-notes-azure-data-studio.md)をご覧ください。**

## <a name="get-azure-data-studio-for-windows"></a>Azure Data Studio for Windows を取得する

[!INCLUDE [ssms-ads-install](../includes/ssms-azure-data-studio-install.md)]

Azure Data Studio のこのリリースには、標準の Windows インストーラーのエクスペリエンスと、.zip ファイルが含まれています。

管理者特権を必要としないため、"*ユーザー インストーラー*" をお勧めします。これにより、インストールとアップグレードの両方が簡略化されます。 場所はユーザーの Local AppData (LOCALAPPDATA) フォルダーの下にあるため、ユーザー インストーラーに管理者特権は必要ありません。 また、ユーザーインストーラーでは、より滑らかなバックグラウンドの更新エクスペリエンスも提供されます。 詳細については、[Windows 用のユーザーのセットアップ](https://code.visualstudio.com/updates/v1_26#_user-setup-for-windows)に関するセクションを参照してください。

**ユーザー インストーラー** (推奨)

1. [Windows 用の Azure Data Studio "*ユーザー*" インストーラー](https://go.microsoft.com/fwlink/?linkid=2157460)をダウンロードして実行します。
2. Azure Data Studio アプリを起動します。

**システム インストーラー**

1. [Windows 用の Azure Data Studio "*システム*" インストーラー](https://go.microsoft.com/fwlink/?linkid=2157459) をダウンロードして実行します。
2. Azure Data Studio アプリを起動します。

**zip ファイル**

1. [Windows 用の Azure Data Studio.zip](https://go.microsoft.com/fwlink/?linkid=2157458) をダウンロードします。
2. ダウンロードしたファイルを参照して抽出します。
3. `\azuredatastudio-windows\azuredatastudio.exe` を実行します。

## <a name="get-azure-data-studio-for-macos"></a>Azure Data Studio for macOS を取得する

1. [Azure Data Studio for macOS](https://go.microsoft.com/fwlink/?linkid=2157456) をダウンロードします。
2. zip のコンテンツを展開するには、ダブルクリックします。
3. Azure Data Studio を *スタート パッド* で使用できるようにするには、*Azure Data Studio.app* を *[アプリケーション]* フォルダーにドラッグします。

## <a name="get-azure-data-studio-for-linux"></a>Linux 用の Azure Data Studio を取得する

1. インストーラーのいずれか、または tar.gz アーカイブを使用することで、Linux 用の Azure Data Studio をダウンロードします。
    - [.deb](https://go.microsoft.com/fwlink/?linkid=2157352)
    - [.rpm](https://go.microsoft.com/fwlink/?linkid=2157248)
    - [.tar.gz](https://go.microsoft.com/fwlink/?linkid=2157353)
1. ファイルを抽出して Azure Data Studio を起動するには、新しいターミナル ウィンドウを開いて次のコマンドを入力します。

   **Debian のインストール:**

   ```bash
   cd ~
   sudo dpkg -i ./Downloads/azuredatastudio-linux-<version string>.deb

   azuredatastudio
   ```

   **rpm のインストール:**

   ```bash
   cd ~
   yum install ./Downloads/azuredatastudio-linux-<version string>.rpm

   azuredatastudio
   ```

   **tar.gz のインストール:**

   ```bash
   cd ~
   cp ~/Downloads/azuredatastudio-linux-<version string>.tar.gz ~ 
   tar -xvf ~/azuredatastudio-linux-<version string>.tar.gz 
   echo 'export PATH="$PATH:~/azuredatastudio-linux-x64"' >> ~/.bashrc
   source ~/.bashrc
   azuredatastudio
   ```

   > [!NOTE]
   > Debian、Redhat、および Ubuntu では、依存関係が不足する場合があります。 ご自身の Linux のバージョンに応じて、次のコマンドを使ってこれらの依存関係をインストールします。

   **Debian:**

   ```bash
   sudo apt-get install libunwind8
   ```

   **Redhat:**

   ```bash
   yum install libXScrnSaver
   ```

   **Ubuntu:**

   ```bash
   sudo apt-get install libxss1

   sudo apt-get install libgconf-2-4

   sudo apt-get install libunwind8
   ```

## <a name="download-insiders-build-of-azure-data-studio"></a>Azure Data Studio の Insider ビルドをダウンロードする

一般に、ユーザーは上記の Azure Data Studio の安定したリリースをダウンロードする必要があります。 ただし、ベータ版の機能を試して、フィードバックを送信する場合は、[Azure Data Studio の Insider ビルド](https://github.com/microsoft/azuredatastudio#try-out-the-latest-insiders-build-from-main)をダウンロードしてください。

## <a name="supported-operating-systems"></a>サポートされるオペレーティング システム

Azure Data Studio は、Windows、macOS、および Linux 上で実行されます。また、次のプラットフォーム上でサポートされています。

### <a name="windows"></a>Windows

- Windows 10 (64 ビット)
- Windows 8.1 (64 ビット)
- Windows 8 (64 ビット)
- Windows 7 (SP1)
- Windows Server 2019
- Windows Server 2016
- Windows Server 2012 R2 (64 ビット)
- Windows Server 2012 (64 ビット)
- Windows Server 2008 R2 (64 ビット)

### <a name="macos"></a>macOS

- macOS 10.15 Catalina
- macOS 10.14 Mojave
- macOS 10.13 High Sierra
- macOS 10.12 Sierra
- macOS 11.1 Big Sur

### <a name="linux"></a>Linux

- Red Hat Enterprise Linux 7.4
- Red Hat Enterprise Linux 7.3
- SUSE Linux Enterprise Server v12 SP2
- Ubuntu 16.04

## <a name="recommended-system-requirements"></a>推奨されるシステム要件

| 推奨の最小構成 | CPU コア | メモリ/RAM |
|---------------------|-----------|------------|
|     推奨     |     4     |   8 GB     |
|     最小値         |     2     |   4 GB     |

## <a name="check-for-updates"></a>更新プログラムをチェックする

最新の更新プログラムを確認するには、ウィンドウの左下にある歯車アイコンを選択し、 **[更新プログラムの確認]** を選択します。

オフライン環境の更新プログラムを適用するには、前にインストールされていたバージョンの上に直接[最新バージョンをインストール](#download-and-install-azure-data-studio)します。 以前のバージョンの Azure Data Studio をアンインストールする必要はありません。 現在インストールされているアプリケーションがインストーラーによって更新されます (存在する場合)。

## <a name="supported-sql-offerings"></a>サポートされる SQL 製品

- このバージョンの Azure Data Studio は、すべての[サポート対象バージョンである [!INCLUDE [sssql14-md](../includes/sssql14-md.md)] - [!INCLUDE[sql-server-2019](../includes/sssql19-md.md)]](https://support.microsoft.com/lifecycle?C2=1044) で動作し、Azure SQL Database および Azure Synapse Analytics で最新のクラウド機能と連携するためのサポートが提供されます。 Azure Data Studio では、Azure SQL Managed Instance のプレビューもサポートされています。

## <a name="move-user-settings"></a>ユーザー設定を移動する

SQL Operations Studio を Azure Data Studio に更新していて、設定、キーボード ショートカット、またはコード スニペットを保持する場合は、以下のステップに従ってください。

*既に Azure Data Studio がある場合、または SQL Operations Studio をインストールまたはカスタマイズしたことがない場合は、このセクションを無視することができます。*

1. 左下の歯車を選択し、 **[設定]** を選択することで、[設定] を開きます。

   ![Azure Data Studio の設定の編集](./media/download/open-settings.png)

2. 上部の **[ユーザー設定]** タブを右クリックし、 **[Explorer で表示]** を選択します。

   ![エクスプローラーを起動すると、ローカル ファイル システムに移動する](./media/download/reveal-in-explorer.png)

3. このフォルダー内のファイルをすべてコピーし、[ドキュメント] フォルダーのように、ローカル ドライブ上の検索しやすい場所に保存します。

   ![ファイルを使用してコピーします。](./media/download/copy-settings.png)

4. 新しいバージョンの Azure Data Studio で、ステップ 1 から 2 に従い、ステップ 3 で保存した内容をそのフォルダーに貼り付けます。 また、設定、キー バインド、またはスニペットをそれぞれの場所に手動でコピーすることもできます。

5. 既存のインストールをオーバーライドする場合は、インストールの前に古いインストール ディレクトリを削除して、リソース エクスプローラーの Azure アカウントに接続する際のエラーを回避します。

## <a name="unattended-install-for-windows"></a>Windows 用の無人インストール

コマンド プロンプト スクリプトを使用して Azure Data Studio をインストールすることもできます。

GUI プロンプトを使用せずにバックグラウンドで Azure Data Studio をインストールする必要があり、Windows プラットフォームを使用している場合は、次のステップに従います。

1. 管理者特権を使用してコマンド プロンプトを起動します。

2. コマンド プロンプトで、次のコマンドを入力します。

    ```console
    <path where the azuredatastudio-windows-user-setup-x.xx.x.exe file is located> /VERYSILENT /MERGETASKS=!runcode>
    ```

    例:

    ```console
    %systemdrive%\azuredatastudio-windows-user-setup-1.24.0.exe /VERYSILENT /MERGETASKS=!runcode
    ```

    > [!Note]
    > この例では、システム インストーラー ファイルも操作します。
    > 
    > ```console
    > <path where the azuredatastudio-windows-setup-x.xx.x.exe file is located> /VERYSILENT /MERGETASKS=!runcode>
    > ```

    また、 */VERYSILENT* ではなく */SILENT* を渡して、セットアップ UI を表示することもできます。

3. すべてがうまくいけば、Azure Data Studio がインストールされているとみなすことができます。

## <a name="uninstall-azure-data-studio"></a>Azure Data Studio のアンインストール

Windows インストーラーを使用して Azure Data Studio をインストールした場合は、Windows アプリケーションを削除するのと同じ方法でアンインストールします。

.zip またはその他のアーカイブを使用して Azure Data Studio をインストールした場合は、該当するファイルを削除します。

## <a name="next-steps"></a>次の手順

作業を開始するには、次のクイック スタートのいずれかを参照してください。

- [Azure Data Studio とは](what-is-azure-data-studio.md)
- [Azure Data Studio リリース ノート](release-notes-azure-data-studio.md)
- [SQL Server に対する接続およびクエリ](quickstart-sql-server.md)
- [Azure SQL Database に対する接続およびクエリ](quickstart-sql-database.md)
- [Azure Synapse Analytics に対する接続およびクエリ](quickstart-sql-dw.md)

[!INCLUDE[get-help-sql-tools](../includes/paragraph-content/get-help-sql-tools.md)]

[!INCLUDE[contribute-to-content](../includes/paragraph-content/contribute-to-content.md)]

[Microsoft のプライバシーに関する声明](https://go.microsoft.com/fwlink/?LinkId=521839)と[利用状況データの収集](usage-data-collection.md)。
