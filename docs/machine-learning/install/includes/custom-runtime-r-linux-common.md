---
ms.prod: sql
ms.technology: machine-learning-services
ms.date: 02/18/2021
ms.topic: include
author: dphansen
ms.author: davidph
ms.openlocfilehash: b62528c2ef43f2d5cbf860ad50bb94b2d81dc732
ms.sourcegitcommit: efce0ed7d1c0ab36a4a9b88585111636134c0fbb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104833752"
---
## <a name="custom-installation-of-r"></a>R のカスタム インストール

::: zone pivot="platform-linux-ubuntu"
> [!NOTE]
> 既定の場所 **/usr/lib/R** に R がインストールされている場合は、このセクションをスキップし、「[Rcpp パッケージをインストールする](#install-rcpp-package-linux)」セクションに進んでかまいません。
::: zone-end

### <a name="update-the-environment-variables"></a>環境変数を更新する

最初に、**mssql-launchpadd** サービスを編集して、**R_HOME** 環境変数を `/etc/systemd/system/mssql-launchpadd.service.d/override.conf` ファイルに追加します

1. systemctl を使用してファイルを開きます

    ```bash
    sudo systemctl edit mssql-launchpadd
    ```

1. 開かれた `/etc/systemd/system/mssql-launchpadd.service.d/override.conf` ファイルに、次のテキストを挿入します。 **R_HOME** の値を、カスタム R のインストール パスに設定します。

    ```text
    [Service]
    Environment="R_HOME=<path to R>"
    ```

1. 保存して閉じます。

次に、`libR.so` を読み込めることを確認します。

1. **/etc/ld.so.conf.d** にカスタム custom-r.conf ファイルを作成します。

    ```bash
    sudo vi /etc/ld.so.conf.d/custom-r.conf
    ```

1. 開かれたファイルで、カスタム R のインストールから **libR.so** へのパスを追加します。

    ```
    <path to the R lib>
    ```

1. 新しいファイルを保存し、エディターを閉じます。

1. `ldconfig` を実行し、次のコマンドを実行することで **libR.so** を読み込めることを確認し、すべての依存ライブラリが見つかることを確認します。

    ```bash
    sudo ldconfig
    ldd <path to the R lib>/libR.so
    ```

### <a name="grant-access-to-the-custom-r-installation-folder"></a>カスタム R インストール フォルダーへのアクセスを許可する

`/var/opt/mssql/mssql.conf` ファイルの extensibility セクションにある `datadirectories` オプションを、カスタム R のインストールに設定します。

```bash
sudo /opt/mssql/bin/mssql-conf set extensibility.datadirectories <path to R>
```

### <a name="restart-mssql-launchpadd-service"></a>mssql-launchpadd サービスを再起動する

次のコマンドを実行して、**mssql-launchpadd** を再起動します。

```bash
sudo systemctl restart mssql-launchpadd
```

<a name="install-rcpp-package-linux"></a>

## <a name="install-rcpp-package"></a>Rcpp パッケージをインストールする

以下の手順のようにして、**Rcpp** パッケージをインストールします。

1. シェルから R を起動します。

    ```bash
    sudo ${R_HOME}/bin/R
    ```

1. 次のスクリプトを実行して、${R_HOME}\library フォルダーに Rcpp パッケージをインストールします。

  ```R
  install.packages("Rcpp", lib = "${R_HOME}/library");
  ```

## <a name="register-language-extension"></a>言語拡張機能を登録する

次の手順のようにして、R カスタム ランタイムに使用される R 言語拡張機能をダウンロードして登録します。

1. [SQL Server 言語拡張機能の GitHub リポジトリ](https://github.com/microsoft/sql-server-language-extensions/releases)から **R-lang-extension-linux-release.zip** ファイルをダウンロードします。

    開発環境やテスト環境では、代わりにデバッグ バージョン (**R-lang-extension-linux-debug.zip**) を使用できます。 デバッグ バージョンでは、エラーを調査するための詳細なログ情報が提供されます。これは運用環境では推奨されません。

1. [Azure Data Studio](../../../azure-data-studio/what-is-azure-data-studio.md) を使用してお使いの SQL Server インスタンスに接続し、次の T-SQL コマンドを実行して、[CREATE EXTERNAL LANGUAGE](../../../t-sql/statements/create-external-language-transact-sql.md) で R 言語拡張機能を登録します。 

    このステートメントのパスを変更して、ダウンロードした言語拡張機能の zip ファイル (**R-lang-extension-linux-release.zip**) の場所を反映します。

    ```sql
    CREATE EXTERNAL LANGUAGE [myR]
    FROM (CONTENT = N'/path/to/R-lang-extension-linux-release.zip', FILE_NAME = 'libRExtension.so.1.1');
    GO
    ```

    R 言語拡張機能を使用するデータベースごとに、このステートメントを実行します。

    > [!NOTE]
    > **R** は予約語であり、新しい外部言語の名前として使用することはできません。 別の名前を使用してください。 たとえば、上記のステートメントでは **myR** が使用されています。
