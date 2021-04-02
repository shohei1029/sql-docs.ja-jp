---
title: SQL Server on Linux のインストール ガイド
titleSuffix: SQL Server
description: SQL Server on Linux のインストール、更新、およびアンインストールを行います。 この記事では、オンライン、オフライン、および自動実行の各シナリオについて説明します。
author: VanMSFT
ms.author: vanto
ms.date: 06/22/2020
ms.topic: conceptual
ms.prod: sql
ms.custom: sqlfreshmay19
ms.technology: linux
ms.assetid: 565156c3-7256-4e63-aaf0-884522ef2a52
ms.openlocfilehash: 856cfc6549e3a79fdfe8ee9d5fb750d12058df95
ms.sourcegitcommit: 0b37eb7aef2f358f80867cd13830dd6683da8d85
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105980995"
---
# <a name="installation-guidance-for-sql-server-on-linux"></a>SQL Server on Linux のインストール ガイド

[!INCLUDE [SQL Server - Linux](../includes/applies-to-version/sql-linux.md)]

この記事では、Linux 上に SQL Server 2017 および SQL Server 2019 のインストール、更新、およびアンインストールを行うためのガイダンスを提供します。

その他の展開シナリオは次のとおりです。

- [Windows](../database-engine/install-windows/install-sql-server.md)
- [Docker コンテナー](./sql-server-linux-docker-container-deployment.md)
- [Kubernetes - ビッグ データ クラスター](../big-data-cluster/deploy-get-started.md)

> [!TIP]
> このガイドでは、さまざまなデプロイ シナリオについて説明します。 インストール手順の詳細を確認するだけの場合は、次のいずれかのクイックスタートを参照してください。
> - [RHEL クイックスタート](quickstart-install-connect-red-hat.md)
> - [SLES クイック スタート](quickstart-install-connect-suse.md)
> - [Ubuntu クイックスタート](quickstart-install-connect-ubuntu.md)
> - [Docker クイック スタート](quickstart-install-connect-docker.md)

よく寄せられる質問に対する回答については、「[SQL Server on Linux に関する FAQ](../linux/sql-server-linux-faq.yml)」を参照してください。

## <a name="supported-platforms"></a><a id="supportedplatforms"></a> サポートされているプラットフォーム

SQL Server は、Red Hat Enterprise Linux (RHEL)、SUSE Linux Enterprise Server (SLES)、および Ubuntu でサポートされています。 Docker Engine on Linux または Docker for Windows/Mac 上で実行できる Docker イメージとしてもサポートされています。

<!--SQL Server 2017 on Linux-->
::: moniker range="= sql-server-linux-2017 || = sql-server-2017"

| プラットフォーム | サポートされているバージョン | 取得
|-----|-----|-----
| **Red Hat Enterprise Linux** | 7.3、7.4、7.5、7.6、8  | [RHEL 7.6 を取得する](https://access.redhat.com/products/red-hat-enterprise-linux/evaluation)
| **SUSE Linux Enterprise Server** | v12 SP2 | [SLES v12 SP2 を取得する](https://www.suse.com/products/server)
| **Ubuntu** | 16.04 | [Ubuntu 16.04 を取得する](http://releases.ubuntu.com/xenial/)
| **Docker Engine** | 1.8 以降 | [Docker を取得する](https://www.docker.com/get-started)

::: moniker-end

<!--SQL Server 2019 on Linux-->
::: moniker range=">= sql-server-linux-ver15 || >= sql-server-ver15 "

[!INCLUDE [linux-supported-platfoms-2019](../includes/linux-supported-platfoms-2019.md)]

::: moniker-end

Microsoft では、OpenShift と Kubernetes の使用による SQL Server コンテナーのデプロイと管理もサポートしています。

> [!NOTE]
> SQL Server は、前述のディストリビューションの Linux 上でテストが行われ、それらでサポートされています。 サポートされていないオペレーティング システムに SQL Server をインストールすることを選択した場合は、サポートの影響を理解するために、「[Microsoft SQL Server のテクニカル サポート ポリシー](https://support.microsoft.com/help/4047326/support-policy-for-microsoft-sql-server)」の「**サポート ポリシー** 」セクションを確認してください。

## <a name="system-requirements"></a><a id="system"></a> システム要件

SQL Server には、Linux に対する次のシステム要件があります。

||要件|
|-----|-----|
| **[メモリ]** | 2 GB |
| **ファイル システム** | **XFS** または **EXT4** (**BTRFS** などの他のファイル システムはサポートされていません) |
| **ディスク領域** | 6 GB |
| **プロセッサの速度** | 2 GHz |
| **プロセッサのコア数** | 2 コア |
| **プロセッサの種類** | x64 互換のみ |

運用環境で **Network File System (NFS)** のリモート共有を使用する場合は、次のサポート要件に注意してください。

- NFS バージョン **4.2 以上** を使用してください。 前のバージョンの NFS では、最新のファイル システムに共通する fallocate やスパース ファイルの作成などの必要な機能がサポートされていません。
- NFS マウント上の **/var/opt/mssql** ディレクトリのみが検索されます。 SQL Server システム バイナリなどの他のファイルはサポートされていません。
- リモート共有をマウントするときに NFS クライアントが 'nolock' オプションを使用していることを確認してください。

## <a name="configure-source-repositories"></a><a id="repositories"></a> ソース リポジトリ を構成する

SQL Server をインストールまたはアップグレードすると、構成されている Microsoft リポジトリから最新バージョンの SQL Server が取得されます。 このクイックスタートでは、SQL Server の累積的な更新プログラム (**CU**) リポジトリを使用します。 ただし、代わりに **GDR** リポジトリを構成することもできます。 リポジトリの詳細とそれらの構成方法については、[SQL Server on Linux 用のリポジトリの構成](sql-server-linux-change-repo.md)に関する記事を参照してください。

## <a name="install-sql-server"></a><a id="platforms"></a> SQL Server をインストールする

コマンドラインから SQL Server 2017 または SQL Server 2019 on Linux をインストールできます。 詳細な手順については、次のクイックスタートのいずれかを参照してください。

| プラットフォーム | インストールのクイックスタート |
|---|---|
| Red Hat Enterprise Linux (RHEL) | [2017](quickstart-install-connect-red-hat.md?view=sql-server-2017&preserve-view=true) \| [2019](quickstart-install-connect-red-hat.md?view=sql-server-linux-ver15&preserve-view=true) |
| SUSE Linux Enterprise Server (SLES) | [2017](quickstart-install-connect-suse.md?view=sql-server-2017&preserve-view=true) \| [2019](quickstart-install-connect-suse.md?view=sql-server-linux-ver15&preserve-view=true) |
| Ubuntu | [2017](quickstart-install-connect-ubuntu.md?view=sql-server-2017&preserve-view=true) \| [2019](quickstart-install-connect-ubuntu.md?view=sql-server-linux-ver15&preserve-view=true) |
| Docker | [2017](quickstart-install-connect-docker.md?view=sql-server-2017&preserve-view=true) \| [2019](quickstart-install-connect-docker.md?view=sql-server-linux-ver15&preserve-view=true) |

Azure 仮想マシンで SQL Server on Linux を実行することもできます。 詳細については、[Azure での SQL VM のプロビジョニング](/azure/virtual-machines/linux/sql/provision-sql-server-linux-virtual-machine?toc=/sql/toc/toc.json)に関する記事を参照してください。

インストールした後、最適なパフォーマンスを得るために追加の構成変更を行うことを検討してください。 詳細については、「[パフォーマンスのベスト プラクティスと SQL Server on Linux の構成ガイドライン](sql-server-linux-performance-best-practices.md)」を参照してください。

## <a name="update-or-upgrade-sql-server"></a><a id="upgrade"></a> SQL Server の更新またはアップグレード

**mssql-server** パッケージを最新のリリースに更新するには、お使いのプラットフォームに基づいて次のいずれかのコマンドを使用します。

| プラットフォーム | パッケージの更新コマンド |
|-----|-----|
| RHEL | `sudo yum update mssql-server` |
| SLES | `sudo zypper update mssql-server` |
| Ubuntu | `sudo apt-get update`<br/>`sudo apt-get install mssql-server` |

これらのコマンドによって最新のパッケージがダウンロードされ、`/opt/mssql/` にあるバイナリが置き換えられます。 ユーザーが生成したデータベースとシステム データベースは、この操作の影響を受けることはありません。

SQL Server をアップグレードするには、まず、[構成済みのリポジトリを目的のバージョンの SQL Server に変更](sql-server-linux-change-repo.md)します。 次に、同じ **update** コマンドを使用して、SQL Server のバージョンをアップグレードします。 これは、2 つのリポジトリ間でアップグレード パスがサポートされている場合にのみ当てはまります。

## <a name="rollback-sql-server"></a><a id="rollback"></a> SQL Server をロールバックする

SQL Server を前のリリースにロールバックまたはダウングレードするには、次の手順に従います。

1. ダウングレードする SQL Server パッケージのバージョン番号を識別します。 パッケージ番号の一覧については、[リリースノート](../linux/sql-server-linux-release-notes.md)を参照してください。

1. SQL Server の前のバージョンにダウングレードします。 次のコマンドで、`<version_number>` を手順 1 で識別した SQL Server のバージョン番号に置き換えます。

   | プラットフォーム | パッケージの更新コマンド |
   |-----|-----|
   | RHEL | `sudo yum downgrade mssql-server-<version_number>.x86_64` |
   | SLES | `sudo zypper install --oldpackage mssql-server=<version_number>` |
   | Ubuntu | `sudo apt-get install mssql-server=<version_number>`<br/>`sudo systemctl start mssql-server` |

> [!NOTE]
> 同じメジャー バージョン (例: SQL Server 2019) 内のリリースへのダウングレードのみがサポートされています。

## <a name="check-installed-sql-server-version"></a><a id="versioncheck"></a> インストールされている SQL Server のバージョンを確認する

SQL Server on Linux の現在のバージョンとエディションを確認するには、次の手順を使用します。

1. まだインストールしていなければ、[SQL Server コマンドライン ツール](sql-server-linux-setup-tools.md)をインストールします。

1. **sqlcmd** を使用して、SQL Server のバージョンとエディションを表示する Transact-SQL コマンドを実行します。

   ```bash
   sqlcmd -S localhost -U SA -Q 'select @@VERSION'
   ```

## <a name="uninstall-sql-server"></a><a id="uninstall"></a> SQL Server をアンインストールする

Linux 上の **mssql-server** パッケージを削除するには、お使いのプラットフォームに基づいて次のいずれかのコマンドを使用します。

| プラットフォーム | パッケージの削除コマンド |
|-----|-----|
| RHEL | `sudo yum remove mssql-server` |
| SLES | `sudo zypper remove mssql-server` |
| Ubuntu | `sudo apt-get remove mssql-server` |

パッケージを削除しても、生成されたデータベース ファイルは削除されません。 データベース ファイルを削除する場合は、次のコマンドを使用します。

```bash
sudo rm -rf /var/opt/mssql/
```

## <a name="unattended-install"></a><a id="unattended"></a> 自動実行インストール

次の方法で自動実行インストールを実行できます。

- [クイックスタート](#platforms)の最初の手順に従ってリポジトリを登録し、SQL Server をインストールします。
- `mssql-conf setup` を実行するときに、[環境変数](sql-server-linux-configure-environment-variables.md)を設定し、`-n` (プロンプトなし) オプションを使用します。

次の例では、**MSSQL_PID** 環境変数を使用して SQL Server の Developer エディションを構成します。 ライセンス条項に同意し **(** ACCEPT_EULA)、SA ユーザーのパスワード **(MSSQL_SA_PASSWORD**) も設定します。 `-n` パラメーター によってプロンプトなしのインストールが実行され、環境変数から構成値が取得されます。

```bash
sudo MSSQL_PID=Developer ACCEPT_EULA=Y MSSQL_SA_PASSWORD='<YourStrong!Passw0rd>' /opt/mssql/bin/mssql-conf -n setup
```

その他のアクションを実行するスクリプトを作成することもできます。 たとえば、別の SQL Server パッケージをインストールできます。

より詳細なサンプル スクリプトについては、次の例を参照してください。

- [Red Hat 自動実行インストール スクリプト](sample-unattended-install-redhat.md)
- [SUSE 自動実行インストール スクリプト](sample-unattended-install-suse.md)
- [Ubuntu 自動実行インストール スクリプト](sample-unattended-install-ubuntu.md)

## <a name="offline-install"></a><a id="offline"></a>オフライン インストール

お使いの Linux コンピューターでこの[クイックスタート](#platforms)で使用されているオンライン リポジトリにアクセスできない場合は、パッケージ ファイルを直接ダウンロードできます。 これらのパッケージは、Microsoft リポジトリ ([https://packages.microsoft.com](https://packages.microsoft.com)) にあります。

> [!TIP]
> このクイック スタートの手順で正常にインストールされた場合は、SQL Server パッケージをダウンロードしたり手動でインストールしたりする必要はありません。 このセクションでは、オフライン シナリオのみを対象としています。

1. **お使いのプラットフォーム用のデータベース エンジン パッケージをダウンロード** します。 [リリース ノート](../linux/sql-server-linux-release-notes.md)の「パッケージの詳細」セクションで、パッケージのダウンロード リンクを見つけます。

1. **ダウンロードしたパッケージをお使いの Linux コンピュータに移動します**。 別のコンピューターを使用してパッケージをダウンロードした場合、パッケージをお使いの Linux コンピューターに移動する 1 つの方法は **scp** コマンドを使用することです。

1. **データベース エンジン パッケージをインストールします**。 お使いのプラットフォームに基づいて、次のいずれかのコマンドを使用します。 この例に含まれるパッケージ ファイルの名前を、ダウンロードしたパッケージ ファイルの名前に置き換えます。

   | プラットフォーム | パッケージのインストール コマンド |
   |-----|-----|
   | RHEL | `sudo yum localinstall mssql-server_versionnumber.x86_64.rpm` |
   | SLES | `sudo zypper install mssql-server_versionnumber.x86_64.rpm` |
   | Ubuntu | `sudo dpkg -i mssql-server_versionnumber_amd64.deb` |

    > [!NOTE]
    > `rpm -ivh` コマンドを使用して RPM パッケージ (RHEL および SLES) をインストールすることもできますが、前の表のコマンドでは、承認されたリポジトリから入手できる場合は、依存関係が自動的にインストールされます。

1. **不足している依存関係を解決します**。この時点で、依存関係が不足している場合があります。 そうでない場合は、この手順は省略できます。 Ubuntu では、これらの依存関係を含む承認されたリポジトリにアクセスできる場合は、`apt-get -f install` コマンドを使用することが最も簡単な解決方法です。 このコマンドでは、SQL Server のインストールも完了します。 依存関係を手動で検査するには、次のコマンドを使用します。

   | プラットフォーム | 依存関係の表示コマンド |
   |-----|-----|
   | RHEL | `rpm -qpR mssql-server_versionnumber.x86_64.rpm` |
   | SLES | `rpm -qpR mssql-server_versionnumber.x86_64.rpm` |
   | Ubuntu | `dpkg -I mssql-server_versionnumber_amd64.deb` |

   不足している依存関係を解決したら、mssql-server パッケージのインストールを再試行します。

1. **SQL Server のセットアップを完了します。** **mssql-conf** を使用して、SQL Server のセットアップを完了します。

   ```bash
   sudo /opt/mssql/bin/mssql-conf setup
   ```

## <a name="licensing-and-pricing"></a>ライセンスと価格

SQL Server のライセンスは、Linux でも Windows でも同じです。 SQL Server のライセンスと価格の詳細については、[SQL Server のライセンス方法](https://www.microsoft.com/sql-server/sql-server-2017-pricing)に関する記事を参照してください。

## <a name="optional-sql-server-features"></a>SQL Server のオプション機能

インストール後に、SQL Server のオプション機能をインストールしたり有効にしたりできます。

- [SQL Server コマンドライン ツール](sql-server-linux-setup-tools.md)
- [SQL Server エージェント](sql-server-linux-setup-sql-agent.md)
- [SQL Server フルテキスト検索](sql-server-linux-setup-full-text-search.md)
- [Machine Learning Services (R、Python)](sql-server-linux-setup-machine-learning.md)
- [SQL Server Integration Services](sql-server-linux-setup-ssis.md)

[!INCLUDE[Get Help Options](../includes/paragraph-content/get-help-options.md)]

> [!TIP]
> よく寄せられる質問に対する回答については、「[SQL Server on Linux に関する FAQ](sql-server-linux-faq.yml)」を参照してください。
