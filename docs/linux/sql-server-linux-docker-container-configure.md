---
title: SQL Server Docker コンテナーを構成およびカスタマイズする
description: SQL Server Docker コンテナーをカスタマイズするさまざまな方法と、自分の要件に基づいてそれを構成する方法について説明します
author: vin-yu
ms.author: vinsonyu
ms.reviewer: vanto
ms.custom: contperf-fy21q1
ms.date: 03/22/2021
ms.topic: conceptual
ms.prod: sql
ms.technology: linux
moniker: '>= sql-server-linux-2017 || >= sql-server-2017 '
zone_pivot_groups: cs1-command-shell
ms.openlocfilehash: 9d3fa1989b44bcdf53b8f80dedc1aa3bba5bfedb
ms.sourcegitcommit: efce0ed7d1c0ab36a4a9b88585111636134c0fbb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104833926"
---
# <a name="configure-and-customize-sql-server-docker-containers"></a>SQL Server Docker コンテナーを構成およびカスタマイズする

[!INCLUDE [SQL Server - Linux](../includes/applies-to-version/sql-linux.md)]

この記事では、お使いのデータの永続化、コンテナーからのファイルの移動、既定の設定の変更など、SQL Server Docker コンテナーを構成およびカスタマイズする方法について説明します。

## <a name="create-a-customized-container"></a><a id="customcontainer"></a> カスタマイズしたコンテナーを作成する

独自の [Dockerfile](https://docs.docker.com/engine/reference/builder/#usage) を作成して、カスタマイズされた SQL Server コンテナーを作成することができます。 詳しくは、[SQL Server とノード アプリケーションを組み合わせたデモ](https://github.com/twright-msft/mssql-node-docker-demo-app)をご覧ください。 独自の Dockerfile を作成する場合は、フォアグラウンド プロセスに注意してください。このプロセスによってコンテナーの有効期間が制御されるためです。 それが存在する場合、コンテナーはシャットダウンされます。 たとえば、スクリプトを実行して SQL Server を開始する場合は、SQL Server プロセスが一番右のコマンドであることを確認します。 その他のコマンドはすべてバックグラウンドで実行されます。 次のコマンドは、Dockerfile 内でのこのことを示しています。

```bash
/usr/src/app/do-my-sql-commands.sh & /opt/mssql/bin/sqlservr
```

前の例のコマンドを元に戻した場合、do-my-sql-commands.sh スクリプトの完了時にコンテナーはシャットダウンされます。

## <a name="persist-your-data"></a><a id="persist"></a> データを保持する

`docker stop` と `docker start` を使用してコンテナーを再起動しても、SQL Server の構成変更とデータベース ファイルはコンテナーに保持されています。 一方、`docker rm` を使用してコンテナーを削除すると、SQL Server とデータベースを含め、コンテナーの内容がすべて削除されます。 次のセクションでは、関連付けられているコンテナーが削除された場合でも、**データ ボリューム** を使用してデータベース ファイルを保持する方法について説明します。

> [!IMPORTANT]
> SQL Server の場合、Docker 内でのデータの保持について理解しておくことが重要です。 このセクションの説明に加えて、[Docker コンテナー内でデータを管理する方法](https://docs.docker.com/engine/tutorials/dockervolumes/)については、Docker のドキュメントをご覧ください。

### <a name="mount-a-host-directory-as-data-volume"></a>ホスト ディレクトリをデータ ボリュームとしてマウントする

1 つ目のオプションは、ホスト上のディレクトリをコンテナー内のデータ ボリュームとしてマウントすることです。 これを行うには、`docker run` コマンドを `-v <host directory>:/var/opt/mssql` フラグと共に使用します。 これにより、コンテナーの実行間でデータを復元できます。

> [!NOTE]
> SQL Server 2019 コンテナーは自動的に非ルートとして起動するのに対し、SQL Server 2017 コンテナーは既定でルートとして起動します。 SQL Server コンテナーを非ルートとして実行する方法の詳細については、[セキュリティの構成](sql-server-linux-docker-container-security.md)に関するページを参照してください。

<!--SQL Server 2017 on Linux -->
::: moniker range="= sql-server-linux-2017 || = sql-server-2017"

::: zone pivot="cs1-bash"
```bash
docker run -e 'ACCEPT_EULA=Y' -e 'MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>' -p 1433:1433 -v <host directory>/data:/var/opt/mssql/data -v <host directory>/log:/var/opt/mssql/log -v <host directory>/secrets:/var/opt/mssql/secrets -d mcr.microsoft.com/mssql/server:2017-latest
```
::: zone-end

::: zone pivot="cs1-powershell"
```PowerShell
docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>" -p 1433:1433 -v <host directory>/data:/var/opt/mssql/data -v <host directory>/log:/var/opt/mssql/log -v <host directory>/secrets:/var/opt/mssql/secrets -d mcr.microsoft.com/mssql/server:2017-latest
```
::: zone-end

::: zone pivot="cs1-cmd"
```cmd
docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>" -p 1433:1433 -v <host directory>/data:/var/opt/mssql/data -v <host directory>/log:/var/opt/mssql/log -v <host directory>/secrets:/var/opt/mssql/secrets -d mcr.microsoft.com/mssql/server:2017-latest
```
::: zone-end

::: moniker-end

<!--SQL Server 2019 on Linux-->
::: moniker range=">= sql-server-linux-ver15 || >= sql-server-ver15 "

::: zone pivot="cs1-bash"
```bash
docker run -e 'ACCEPT_EULA=Y' -e 'MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>' -p 1433:1433 -v <host directory>/data:/var/opt/mssql/data -v <host directory>/log:/var/opt/mssql/log -v <host directory>/secrets:/var/opt/mssql/secrets -d mcr.microsoft.com/mssql/server:2019-latest
```
::: zone-end

::: zone pivot="cs1-powershell"
```PowerShell
docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>" -p 1433:1433 -v <host directory>/data:/var/opt/mssql/data -v <host directory>/log:/var/opt/mssql/log -v <host directory>/secrets:/var/opt/mssql/secrets -d mcr.microsoft.com/mssql/server:2019-latest
```
::: zone-end

::: zone pivot="cs1-cmd"
```cmd
docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>" -p 1433:1433 -v <host directory>/data:/var/opt/mssql/data -v <host directory>/log:/var/opt/mssql/log -v <host directory>/secrets:/var/opt/mssql/secrets -d mcr.microsoft.com/mssql/server:2019-latest
```
::: zone-end

::: moniker-end

この手法では、Docker の外部にあるホスト上のファイルを共有して表示することもできます。

> [!IMPORTANT]
> **Windows 上の Docker** のホスト ボリューム マッピングでは現在のところ、完全な `/var/opt/mssql` ディレクトリをマッピングできません。 ただし、`/var/opt/mssql/data` など、サブディレクトリをホスト コンピューターにマッピングできます。
>
> 現時点では、SQL Server on Linux イメージを使用した **Mac 上の Docker** のホスト ボリューム マッピングはサポートされていません。 代わりにデータ ボリューム コンテナーを使用してください。 この制限は、`/var/opt/mssql` ディレクトリに固有のものです。 マウントされたディレクトリからの読み取りは正常に機能します。 たとえば、Mac 上で -v を使用してホスト ディレクトリをマウントし、ホスト上に存在する .bak ファイルからバックアップを復元することができます。

### <a name="use-data-volume-containers"></a>データ ボリューム コンテナーを使用する

2 つ目のオプションは、データ ボリューム コンテナーを使用することです。 `-v` パラメーターを使用してホスト ディレクトリではなくボリューム名を指定して、データ ボリューム コンテナーを作成できます。 次の例では、**sqlvolume** という名前の共有データ ボリュームを作成します。

<!--SQL Server 2017 on Linux -->
::: moniker range="= sql-server-linux-2017 || = sql-server-2017"

::: zone pivot="cs1-bash"
```bash
docker run -e 'ACCEPT_EULA=Y' -e 'MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>' -p 1433:1433 -v sqlvolume:/var/opt/mssql -d mcr.microsoft.com/mssql/server:2017-latest
```
::: zone-end

::: zone pivot="cs1-powershell"
```PowerShell
docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>" -p 1433:1433 -v sqlvolume:/var/opt/mssql -d mcr.microsoft.com/mssql/server:2017-latest
```
::: zone-end

::: zone pivot="cs1-cmd"
```cmd
docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>" -p 1433:1433 -v sqlvolume:/var/opt/mssql -d mcr.microsoft.com/mssql/server:2017-latest
```
::: zone-end

::: moniker-end
<!--SQL Server 2019 on Linux-->
::: moniker range=">= sql-server-linux-ver15 || >= sql-server-ver15 "

::: zone pivot="cs1-bash"
```bash
docker run -e 'ACCEPT_EULA=Y' -e 'MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>' -p 1433:1433 -v sqlvolume:/var/opt/mssql -d mcr.microsoft.com/mssql/server:2019-latest
```
::: zone-end

::: zone pivot="cs1-powershell"
```PowerShell
docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>" -p 1433:1433 -v sqlvolume:/var/opt/mssql -d mcr.microsoft.com/mssql/server:2019-latest
```
::: zone-end

::: zone pivot="cs1-cmd"
```cmd
docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>" -p 1433:1433 -v sqlvolume:/var/opt/mssql -d mcr.microsoft.com/mssql/server:2019-latest
```
::: zone-end

::: moniker-end

> [!NOTE]
> 実行コマンド内でデータ ボリュームを暗黙的に作成するためのこの手法は、以前のバージョンの Docker では動作しません。 その場合は、Docker のドキュメント、「[データ ボリューム コンテナーの作成とマウント](https://docs.docker.com/engine/tutorials/dockervolumes/#creating-and-mounting-a-data-volume-container)」に概説されている明示的な手順を使用します。

このコンテナーを停止して削除しても、データ ボリュームは保持されます。 `docker volume ls` コマンドを使用して表示できます。

```command
docker volume ls
```

その後、同じボリューム名を持つ別のコンテナーを作成すると、新しいコンテナーでは、ボリュームに含まれているのと同じ SQL Server データが使用されます。

データ ボリューム コンテナーを削除するには、`docker volume rm` コマンドを使用します。

> [!WARNING]
> データ ボリューム コンテナーを削除すると、コンテナー内のすべての SQL Server データがすべて *完全に* 削除されます。

### <a name="backup-and-restore"></a>バックアップと復元

これらのコンテナー手法に加えて、標準の SQL Server バックアップと復元の手法を使用することもできます。 バックアップ ファイルを使用して、データを保護したり、別の SQL Server インスタンスにデータを移動したりすることができます。 詳細については、「[Linux 上での SQL Server データベースのバックアップと復元](sql-server-linux-backup-and-restore-database.md)」を参照してください。

> [!WARNING]
> バックアップを作成する場合は、必ずコンテナーの外部でバックアップ ファイルを作成またはコピーしてください。 そうしないと、コンテナーが削除された場合に、バックアップ ファイルも削除されます。

## <a name="copy-files-from-a-container"></a>コンテナーからファイルをコピーする

コンテナーからファイルをコピーするには、次のコマンドを使用します。

```command
docker cp <Container ID>:<Container path> <host path>
```

コンテナー ID は、`docker ps -a` コマンドの実行で取得できます。

**例:**

::: zone pivot="cs1-bash"
```bash
docker cp d6b75213ef80:/var/opt/mssql/log/errorlog /tmp/errorlog
```
::: zone-end

::: zone pivot="cs1-powershell"
```PowerShell
docker cp d6b75213ef80:/var/opt/mssql/log/errorlog C:\Temp\errorlog
```
::: zone-end

::: zone pivot="cs1-cmd"
```cmd
docker cp d6b75213ef80:/var/opt/mssql/log/errorlog C:\Temp\errorlog
```
::: zone-end

## <a name="copy-files-into-a-container"></a>コンテナーにファイルをコピーする

コンテナーにファイルをコピーするには、次のコマンドを使用します。

```command
docker cp <Host path> <Container ID>:<Container path>
```

**例:**

::: zone pivot="cs1-bash"
```bash
docker cp /tmp/mydb.mdf d6b75213ef80:/var/opt/mssql/data
```
::: zone-end

::: zone pivot="cs1-powershell"
```PowerShell
docker cp C:\Temp\mydb.mdf d6b75213ef80:/var/opt/mssql/data
```
::: zone-end

::: zone pivot="cs1-cmd"
```cmd
docker cp C:\Temp\mydb.mdf d6b75213ef80:/var/opt/mssql/data
```
::: zone-end

## <a name="configure-the-timezone"></a><a id="tz"></a> タイムゾーンを構成する

特定のタイムゾーンを持つ Linux コンテナー内で SQL Server を実行するには、`TZ` 環境変数を構成します。 適切なタイムゾーン値を検索するには、Linux bash プロンプトから `tzselect` コマンドを実行します。

```command
tzselect
```

タイムゾーンを選択すると、`tzselect` に次のような出力が表示されます。

```output
The following information has been given:

        United States
        Pacific

Therefore TZ='America/Los_Angeles' will be used.
```

この情報を使用して、Linux コンテナー内で同じ環境変数を設定できます。 次の例は、`Americas/Los_Angeles` タイムゾーンのコンテナー内で SQL Server を実行する方法を示しています。

<!--SQL Server 2017 on Linux -->
::: moniker range="= sql-server-linux-2017 || = sql-server-2017"

::: zone pivot="cs1-bash"
```bash
sudo docker run -e 'ACCEPT_EULA=Y' -e 'A_PASSWORD=<YourStrong!Passw0rd>' \
-p 1433:1433 --name sql1 \
-e 'TZ=America/Los_Angeles'\
-d mcr.microsoft.com/mssql/server:2017-latest
```
::: zone-end

::: zone pivot="cs1-powershell"
```PowerShell
sudo docker run -e 'ACCEPT_EULA=Y' -e "SA_PASSWORD=<YourStrong!Passw0rd>" `
-p 1433:1433 --name sql1 `
-e "TZ=America/Los_Angeles" `
-d mcr.microsoft.com/mssql/server:2017-latest 
```
::: zone-end

::: zone pivot="cs1-cmd"
```cmd
sudo docker run -e 'ACCEPT_EULA=Y' -e "SA_PASSWORD=<YourStrong!Passw0rd>" `
-p 1433:1433 --name sql1 ^
-e "TZ=America/Los_Angeles" ^
-d mcr.microsoft.com/mssql/server:2017-latest 
```
::: zone-end

::: moniker-end
<!--SQL Server 2019 on Linux-->
::: moniker range=">= sql-server-linux-ver15 || >= sql-server-ver15 "

::: zone pivot="cs1-bash"
```bash
sudo docker run -e 'ACCEPT_EULA=Y' -e 'MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>' \
   -p 1433:1433 --name sql1 \
   -e 'TZ=America/Los_Angeles'\
   -d mcr.microsoft.com/mssql/server:2019-latest
```
::: zone-end

::: zone pivot="cs1-powershell"
```PowerShell
sudo docker run -e 'ACCEPT_EULA=Y' -e "MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>" `
   -p 1433:1433 --name sql1 `
   -e "TZ=America/Los_Angeles" `
   -d mcr.microsoft.com/mssql/server:2019-latest
```
::: zone-end

::: zone pivot="cs1-cmd"
```cmd
sudo docker run -e 'ACCEPT_EULA=Y' -e "MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>" `
   -p 1433:1433 --name sql1 `
   -e "TZ=America/Los_Angeles" `
   -d mcr.microsoft.com/mssql/server:2019-latest
```
::: zone-end

::: moniker-end

## <a name="change-the-default-file-location"></a><a id="changefilelocation"></a> 既定のファイルの場所を変更する

ご利用の `docker run` コマンド内にデータ ディレクトリを変更するための `MSSQL_DATA_DIR` 変数を追加してしてから、ご利用のコンテナーのユーザーがアクセスできるその場所にボリュームをマウントします。

<!--SQL Server 2017 on Linux -->
::: moniker range="= sql-server-linux-2017 || = sql-server-2017"

::: zone pivot="cs1-bash"
```bash
docker run -e 'ACCEPT_EULA=Y' -e 'SA_PASSWORD=MyStrongPassword' -e 'MSSQL_DATA_DIR=/my/file/path' -v /my/host/path:/my/file/path -p 1433:1433 -d mcr.microsoft.com/mssql/server:2017-latest
```
::: zone-end

::: zone pivot="cs1-powershell"
```PowerShell
docker run -e 'ACCEPT_EULA=Y' -e "SA_PASSWORD=MyStrongPassword" -e "MSSQL_DATA_DIR=/my/file/path" -v /my/host/path:/my/file/path -p 1433:1433 -d mcr.microsoft.com/mssql/server:2017-latest
```
::: zone-end

::: zone pivot="cs1-cmd"
```cmd
docker run -e "ACCEPT_EULA=Y" -e "SA_PASSWORD=MyStrongPassword" -e "MSSQL_DATA_DIR=/my/file/path" -v /my/host/path:/my/file/path -p 1433:1433 -d mcr.microsoft.com/mssql/server:2017-latest
```
::: zone-end

::: moniker-end

<!--SQL Server 2019 on Linux-->
::: moniker range=">= sql-server-linux-ver15 || >= sql-server-ver15 "

::: zone pivot="cs1-bash"
```bash
docker run -e 'ACCEPT_EULA=Y' -e 'SA_PASSWORD=MyStrongPassword' -e 'MSSQL_DATA_DIR=/my/file/path' -v /my/host/path:/my/file/path -p 1433:1433 -d mcr.microsoft.com/mssql/server:2019-latest
```
::: zone-end

::: zone pivot="cs1-powershell"
```PowerShell
docker run -e 'ACCEPT_EULA=Y' -e "SA_PASSWORD=MyStrongPassword" -e "MSSQL_DATA_DIR=/my/file/path" -v /my/host/path:/my/file/path -p 1433:1433 -d mcr.microsoft.com/mssql/server:2019-latest
```
::: zone-end

::: zone pivot="cs1-cmd"
```cmd
docker run -e "ACCEPT_EULA=Y" -e "SA_PASSWORD=MyStrongPassword" -e "MSSQL_DATA_DIR=/my/file/path" -v /my/host/path:/my/file/path -p 1433:1433 -d mcr.microsoft.com/mssql/server:2019-latest
```
::: zone-end

::: moniker-end

## <a name="next-steps"></a>次のステップ

<!--SQL Server 2017 on Linux -->
::: moniker range="= sql-server-linux-2017 || = sql-server-2017"

- [クイックスタート](quickstart-install-connect-docker.md?view=sql-server-2017&preserve-view=true)に従って、Docker 上で SQL Server 2017 のコンテナー イメージを開始する

::: moniker-end

<!--SQL Server 2019 on Linux-->
::: moniker range=">= sql-server-linux-ver15 || >= sql-server-ver15 "

- [クイックスタート](quickstart-install-connect-docker.md)に従って、Docker 上で SQL Server 2019 のコンテナー イメージを開始する

::: moniker-end

- [SQL Server Docker コンテナーをデプロイして接続する](sql-server-linux-docker-container-deployment.md)

- [SQL Server Docker コンテナーのトラブルシューティング](sql-server-linux-docker-container-troubleshooting.md)

- [SQL Server Docker コンテナーをセキュリティで保護する](sql-server-linux-docker-container-security.md)
