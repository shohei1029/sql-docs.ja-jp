---
ms.prod: sql
ms.technology: machine-learning-services
ms.date: 02/08/2021
ms.topic: include
author: dphansen
ms.author: davidph
ms.openlocfilehash: a79f648631ade25b94c5f0af37bd493127d92eac
ms.sourcegitcommit: efce0ed7d1c0ab36a4a9b88585111636134c0fbb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104879715"
---
## <a name="custom-installation-of-python"></a>Python のカスタム インストール

> [!NOTE]
> `/usr/lib/python3.7` の既定の場所に Python 3.7 がインストールされている場合は、このセクションをスキップし、「[言語拡張機能を登録する](#register-language-extension-linux)」セクションに進むことができます。

独自のバージョンの Python 3.7 をビルドした場合は、次のコマンドを使用して、SQL Server にカスタム インストールを把握させます。

### <a name="add-environment-variable"></a>環境変数を追加する

まず、**mssql-launchpadd** サービスを編集して、**PYTHONHOME** 環境変数を `/etc/systemd/system/mssql-launchpadd.service.d/override.conf` ファイルに追加します

1. systemctl を使用してファイルを開きます

    ```bash
    sudo systemctl edit mssql-launchpadd
    ```

1. 開かれた `/etc/systemd/system/mssql-launchpadd.service.d/override.conf` ファイルに、次のテキストを挿入します。 **PYTHONHOME** の値をカスタムの Python インストール パスに設定します。

    ```
    [Service]
    Environment="PYTHONHOME=<path to the python3.7 lib>"
    ```

1. ファイルを保存して、エディターを閉じます。

次に、`libpython3.7m.so.1.0` を読み込めることを確認します。

1. `/etc/ld.so.conf.d` 内に custom-python.conf ファイルを作成します。

    ```bash
    sudo vi /etc/ld.so.conf.d/custom-python.conf
    ```

1. 開かれたファイルに、カスタムの Python インストールから **libpython3.7m.so.1.0** へのパスを追加します。

    ```
    <path to the python3.7 lib>
    ```

1. 新しいファイルを保存し、エディターを閉じます。

1. `ldconfig` を実行し、次のコマンドを実行することで `libpython3.7m.so.1.0` を読み込めることを確認し、すべての依存ライブラリが見つかることを確認します。

    ```bash
    sudo ldconfig
    ldd <path to the python3.7 lib>/libpython3.7m.so.1.0
    ```

### <a name="grant-access-to-python-folder"></a>Python フォルダーへのアクセスを許可する

`/var/opt/mssql/mssql.conf` ファイルの extensibility セクションにある `datadirectories` オプションを、カスタム Python のインストールに設定します。

```bash
sudo /opt/mssql/bin/mssql-conf set extensibility.datadirectories <path to python3.7>
```

### <a name="restart-mssql-launchpadd"></a>Mssql-launchpadd を再起動する

次のコマンドを実行して、**mssql-launchpadd** を再起動します。

```bash
sudo systemctl restart mssql-launchpadd
```

<a name="register-language-extension-linux"></a>

## <a name="register-language-extension"></a>言語拡張機能を登録する

Python 言語拡張機能 (Python カスタム ランタイムに使用されるもの) をダウンロードして登録するには、次の手順に従います。

1. [SQL Server 言語拡張機能の GitHub リポジトリ](https://github.com/microsoft/sql-server-language-extensions/releases)から **python-lang-extension-linux-release.zip** ファイルをダウンロードします。

    開発環境やテスト環境では、デバッグ バージョン (**python-lang-extension-linux-debug.zip**) を使用することもできます。 デバッグ バージョンでは、エラーを調査するための詳細なログ情報が提供されます。これは運用環境では推奨されません。

1. [Azure Data Studio](../../../azure-data-studio/what-is-azure-data-studio.md) を使用して SQL Server インスタンスに接続し、次の T-SQL コマンドを実行して、Python 言語拡張機能を [CREATE EXTERNAL LANGUAGE](../../../t-sql/statements/create-external-language-transact-sql.md) に登録します。 

    このステートメントのパスを変更して、ダウンロードした言語拡張機能の zip ファイル (**python-lang-extension-linux-release.zip**) の場所を反映します。

    ```sql
    CREATE EXTERNAL LANGUAGE [myPython]
    FROM (CONTENT = N'/path/to/python-lang-extension-linux-release.zip', FILE_NAME = 'libPythonExtension.so.1.1');
    GO
    ```

    Python 言語拡張機能を使用するデータベースごとに、このステートメントを実行します。

    > [!NOTE]
    > **Python** は予約語であり、新しい外部言語の名前として使用することはできません。 別の名前を使用してください。 たとえば、上記のステートメントでは **myPython** が使用されています。
