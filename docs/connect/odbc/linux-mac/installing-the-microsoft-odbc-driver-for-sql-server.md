---
title: Microsoft ODBC Driver for SQL Server をインストールする (Linux)
description: Linux クライアントに Microsoft ODBC Driver for SQL Server をインストールして、データベース接続を有効にする方法について説明します。
ms.date: 01/29/2021
ms.prod: sql
ms.prod_service: connectivity
ms.technology: connectivity
ms.topic: conceptual
helpviewer_keywords:
- driver, installing
ms.assetid: f78b81ed-5214-43ec-a600-9bfe51c5745a
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: 7aee66b9b00763e441bd445688f6c7195e17a224
ms.sourcegitcommit: ecf074e374426c708073c7da88313d4915279fb9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103575158"
---
# <a name="install-the-microsoft-odbc-driver-for-sql-server-linux"></a>Microsoft ODBC Driver for SQL Server をインストールする (Linux)

この記事では、Microsoft ODBC Driver for SQL Server を Linux にインストール方法について説明します。 また、SQL Server 用のオプションのコマンドライン ツール (`bcp` および `sqlcmd`) と、unixODBC 開発ヘッダーについても説明します。

この記事では、Bash シェルから ODBC ドライバーをインストールするためのコマンドについて説明します。 パッケージを直接ダウンロードする場合は、「[ODBC Driver for SQL Server のダウンロード](../download-odbc-driver-for-sql-server.md)」を参照してください。

## <a name="microsoft-odbc-17"></a><a id="17"></a> Microsoft ODBC 17

以下のセクションでは、さまざまな Linux ディストリビューション用の Bash シェルから Microsoft ODBC Driver 17 をインストールする方法について説明します。

- [Alpine Linux](#alpine17)
- [Debian](#debian17)
- [Red Hat Enterprise Linux と Oracle](#redhat17)
- [SUSE Linux Enterprise Server](#suse17)
- [Ubuntu](#ubuntu17)

> [!IMPORTANT]
> 短期間利用可能だった v17 `msodbcsql` パッケージをインストールしている場合は、`msodbcsql17` パッケージのインストール前に削除する必要があります。 これにより、競合が回避されます。 `msodbcsql17` パッケージは `msodbcsql` v13 パッケージと並行してインストールできます。

### <a name="alpine-linux"></a><a id="alpine17"></a> Alpine Linux

```bash
#Download the desired package(s)
curl -O https://download.microsoft.com/download/e/4/e/e4e67866-dffd-428c-aac7-8d28ddafb39b/msodbcsql17_17.7.2.1-1_amd64.apk
curl -O https://download.microsoft.com/download/e/4/e/e4e67866-dffd-428c-aac7-8d28ddafb39b/mssql-tools_17.7.1.1-1_amd64.apk


#(Optional) Verify signature, if 'gpg' is missing install it using 'apk add gnupg':
curl -O https://download.microsoft.com/download/e/4/e/e4e67866-dffd-428c-aac7-8d28ddafb39b/msodbcsql17_17.7.2.1-1_amd64.sig
curl -O https://download.microsoft.com/download/e/4/e/e4e67866-dffd-428c-aac7-8d28ddafb39b/mssql-tools_17.7.1.1-1_amd64.sig

curl https://packages.microsoft.com/keys/microsoft.asc  | gpg --import -
gpg --verify msodbcsql17_17.7.2.1-1_amd64.sig msodbcsql17_17.7.2.1-1_amd64.apk
gpg --verify mssql-tools_17.7.1.1-1_amd64.sig mssql-tools_17.7.1.1-1_amd64.apk


#Install the package(s)
sudo apk add --allow-untrusted msodbcsql17_17.7.2.1-1_amd64.apk
sudo apk add --allow-untrusted mssql-tools_17.7.1.1-1_amd64.apk
```

> [!NOTE]
> Alpine のサポートには、ドライバー バージョン 17.5 以降が必要です。

### <a name="debian"></a><a id="debian17"></a> Debian

```bash
sudo su
curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add -

#Download appropriate package for the OS version
#Choose only ONE of the following, corresponding to your OS version

#Debian 8
curl https://packages.microsoft.com/config/debian/8/prod.list > /etc/apt/sources.list.d/mssql-release.list

#Debian 9
curl https://packages.microsoft.com/config/debian/9/prod.list > /etc/apt/sources.list.d/mssql-release.list

#Debian 10
curl https://packages.microsoft.com/config/debian/10/prod.list > /etc/apt/sources.list.d/mssql-release.list

exit
sudo apt-get update
sudo ACCEPT_EULA=Y apt-get install -y msodbcsql17
# optional: for bcp and sqlcmd
sudo ACCEPT_EULA=Y apt-get install -y mssql-tools
echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc
source ~/.bashrc
# optional: for unixODBC development headers
sudo apt-get install -y unixodbc-dev
# optional: kerberos library for debian-slim distributions
sudo apt-get install -y libgssapi-krb5-2
```

> [!NOTE]
> `echo msodbcsql17 msodbcsql/ACCEPT_EULA boolean true | sudo debconf-set-selections` のように、環境変数 'ACCEPT_EULA' の設定を、debconf 変数 'msodbcsql/ACCEPT_EULA' の設定と置き換えることができます。

### <a name="red-hat-enterprise-server-and-oracle-linux"></a><a id="redhat17"></a> Red Hat Enterprise Server と Oracle Linux

```bash
sudo su

#Download appropriate package for the OS version
#Choose only ONE of the following, corresponding to your OS version

#Red Hat Enterprise Server 6
curl https://packages.microsoft.com/config/rhel/6/prod.repo > /etc/yum.repos.d/mssql-release.repo

#Red Hat Enterprise Server 7 and Oracle Linux 7
curl https://packages.microsoft.com/config/rhel/7/prod.repo > /etc/yum.repos.d/mssql-release.repo

#Red Hat Enterprise Server 8 and Oracle Linux 8
curl https://packages.microsoft.com/config/rhel/8/prod.repo > /etc/yum.repos.d/mssql-release.repo

exit
sudo yum remove unixODBC-utf16 unixODBC-utf16-devel #to avoid conflicts
sudo ACCEPT_EULA=Y yum install -y msodbcsql17
# optional: for bcp and sqlcmd
sudo ACCEPT_EULA=Y yum install -y mssql-tools
echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc
source ~/.bashrc
# optional: for unixODBC development headers
sudo yum install -y unixODBC-devel
```

### <a name="suse-linux-enterprise-server"></a><a id="suse17"></a> SUSE Linux Enterprise Server

```bash
sudo su
curl -O https://packages.microsoft.com/keys/microsoft.asc
rpm --import microsoft.asc

#Download appropriate package for the OS version
#Choose only ONE of the following, corresponding to your OS version

#SUSE Linux Enterprise Server 11 SP4
#Ensure SUSE Linux Enterprise 11 Security Module has been installed 
zypper ar https://packages.microsoft.com/config/sles/11/prod.repo

#SUSE Linux Enterprise Server 12
zypper ar https://packages.microsoft.com/config/sles/12/prod.repo

#SUSE Linux Enterprise Server 15
zypper ar https://packages.microsoft.com/config/sles/15/prod.repo
#(Only for driver 17.3 and below)
SUSEConnect -p sle-module-legacy/15/x86_64

exit
sudo ACCEPT_EULA=Y zypper install -y msodbcsql17
# optional: for bcp and sqlcmd
sudo ACCEPT_EULA=Y zypper install -y mssql-tools
echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc
source ~/.bashrc
# optional: for unixODBC development headers
sudo zypper install -y unixODBC-devel
```

### <a name="ubuntu"></a><a id="ubuntu17"></a> Ubuntu

```bash
sudo su
curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add -

#Download appropriate package for the OS version
#Choose only ONE of the following, corresponding to your OS version

#Ubuntu 16.04
curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > /etc/apt/sources.list.d/mssql-release.list

#Ubuntu 18.04
curl https://packages.microsoft.com/config/ubuntu/18.04/prod.list > /etc/apt/sources.list.d/mssql-release.list

#Ubuntu 20.04
curl https://packages.microsoft.com/config/ubuntu/20.04/prod.list > /etc/apt/sources.list.d/mssql-release.list

#Ubuntu 20.10
curl https://packages.microsoft.com/config/ubuntu/20.10/prod.list > /etc/apt/sources.list.d/mssql-release.list

exit
sudo apt-get update
sudo ACCEPT_EULA=Y apt-get install -y msodbcsql17
# optional: for bcp and sqlcmd
sudo ACCEPT_EULA=Y apt-get install -y mssql-tools
echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc
source ~/.bashrc
# optional: for unixODBC development headers
sudo apt-get install -y unixodbc-dev
```

> [!NOTE]
>
> - Ubuntu 18.04 のサポートには、ドライバー バージョン 17.2 以降が必要です。
> - Ubuntu 18.10 のサポートには、ドライバー バージョン 17.3 以降が必要です。

> [!NOTE]
> `echo msodbcsql17 msodbcsql/ACCEPT_EULA boolean true | sudo debconf-set-selections` のように、環境変数 'ACCEPT_EULA' の設定を、debconf 変数 'msodbcsql/ACCEPT_EULA' の設定と置き換えることができます。

## <a name="previous-versions"></a>以前のバージョン

以下のセクションでは、以前のバージョンの Microsoft ODBC Driver を Linux にインストールする手順について説明します。 次のバージョンのドライバーが対象です。

- [Microsoft ODBC Driver 13.1 for SQL Server](#13.1)
- [Microsoft ODBC Driver 13 for SQL Server](#13)
- [Microsoft ODBC Driver 11 for SQL Server](#11)

## <a name="odbc-131"></a><a id="13.1"></a> ODBC 13.1

以下のセクションでは、さまざまな Linux ディストリビューション用の Bash シェルから Microsoft ODBC Driver 13.1 をインストールする方法について説明します。

### <a name="debian-8"></a>Debian 8

```bash
sudo su 
curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add -
curl https://packages.microsoft.com/config/debian/8/prod.list > /etc/apt/sources.list.d/mssql-release.list
exit
sudo apt-get update
sudo ACCEPT_EULA=Y apt-get install msodbcsql
# optional: for bcp and sqlcmd
sudo ACCEPT_EULA=Y apt-get install mssql-tools
echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc
source ~/.bashrc
# optional: for unixODBC development headers
sudo apt-get install unixodbc-dev
```

### <a name="red-hat-enterprise-server-6"></a>Red Hat Enterprise Server 6

```bash
sudo su
curl https://packages.microsoft.com/config/rhel/6/prod.repo > /etc/yum.repos.d/mssql-release.repo
exit
sudo yum remove unixODBC-utf16 unixODBC-utf16-devel #to avoid conflicts
sudo ACCEPT_EULA=Y yum install msodbcsql
# optional: for bcp and sqlcmd
sudo ACCEPT_EULA=Y yum install mssql-tools
echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc
source ~/.bashrc
# optional: for unixODBC development headers
sudo yum install unixODBC-devel
```

### <a name="red-hat-enterprise-server-7"></a>Red Hat Enterprise Server 7

```bash
sudo su
curl https://packages.microsoft.com/config/rhel/7/prod.repo > /etc/yum.repos.d/mssql-release.repo
exit
sudo yum remove unixODBC-utf16 unixODBC-utf16-devel #to avoid conflicts
sudo ACCEPT_EULA=Y yum install msodbcsql
# optional: for bcp and sqlcmd
sudo ACCEPT_EULA=Y yum install mssql-tools
echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc
source ~/.bashrc
# optional: for unixODBC development headers
sudo yum install unixODBC-devel
```

### <a name="suse-linux-enterprise-server-11"></a>SUSE Linux Enterprise Server 11

```bash
sudo su
zypper ar https://packages.microsoft.com/config/sles/11/prod.repo
exit
sudo ACCEPT_EULA=Y zypper install msodbcsql
# optional: for bcp and sqlcmd
sudo ACCEPT_EULA=Y zypper install mssql-tools
echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc
source ~/.bashrc
# optional: for unixODBC development headers
sudo zypper install unixODBC-devel
```

### <a name="suse-linux-enterprise-server-12"></a>SUSE Linux Enterprise Server 12

```bash
sudo su
zypper ar https://packages.microsoft.com/config/sles/12/prod.repo
exit
sudo ACCEPT_EULA=Y zypper install msodbcsql
# optional: for bcp and sqlcmd
sudo ACCEPT_EULA=Y zypper install mssql-tools
echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc
source ~/.bashrc
# optional: for unixODBC development headers
sudo zypper install unixODBC-devel
```

### <a name="ubuntu-1510"></a>Ubuntu 15.10

```bash
sudo su 
curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add -
curl https://packages.microsoft.com/config/ubuntu/15.10/prod.list > /etc/apt/sources.list.d/mssql-release.list
exit
sudo apt-get update
sudo ACCEPT_EULA=Y apt-get install msodbcsql
# optional: for bcp and sqlcmd
sudo ACCEPT_EULA=Y apt-get install mssql-tools
echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc
source ~/.bashrc
# optional: for unixODBC development headers
sudo apt-get install unixodbc-dev
```

### <a name="ubuntu-1604"></a>Ubuntu 16.04

```bash
sudo su
curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add -
curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > /etc/apt/sources.list.d/mssql-release.list
exit
sudo apt-get update
sudo ACCEPT_EULA=Y apt-get install msodbcsql
# optional: for bcp and sqlcmd
sudo ACCEPT_EULA=Y apt-get install mssql-tools
echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc
source ~/.bashrc
# optional: for unixODBC development headers
sudo apt-get install unixodbc-dev
```

### <a name="ubuntu-1610"></a>Ubuntu 16.10

```bash
sudo su
curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add -
curl https://packages.microsoft.com/config/ubuntu/16.10/prod.list > /etc/apt/sources.list.d/mssql-release.list
exit
sudo apt-get update
sudo ACCEPT_EULA=Y apt-get install msodbcsql
# optional: for bcp and sqlcmd
sudo ACCEPT_EULA=Y apt-get install mssql-tools
echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc
source ~/.bashrc
# optional: for unixODBC development headers
sudo apt-get install unixodbc-dev
```

## <a name="odbc-13"></a><a id="13"></a> ODBC 13

以下のセクションでは、さまざまな Linux ディストリビューション用の Bash シェルから Microsoft ODBC Driver 13 をインストールする方法について説明します。

### <a name="red-hat-enterprise-server-6"></a>Red Hat Enterprise Server 6

```bash
sudo su
curl https://packages.microsoft.com/config/rhel/6/prod.repo > /etc/yum.repos.d/mssql-release.repo
exit
sudo yum update
sudo yum remove unixODBC #to avoid conflicts
sudo ACCEPT_EULA=Y yum install msodbcsql-13.0.1.0-1 mssql-tools-14.0.2.0-1
sudo yum install unixODBC-utf16-devel #this step is optional but recommended*
#Create symlinks for tools
ln -sfn /opt/mssql-tools/bin/sqlcmd-13.0.1.0 /usr/bin/sqlcmd 
ln -sfn /opt/mssql-tools/bin/bcp-13.0.1.0 /usr/bin/bcp
```

### <a name="red-hat-enterprise-server-7"></a>Red Hat Enterprise Server 7

```bash
sudo su
curl https://packages.microsoft.com/config/rhel/7/prod.repo > /etc/yum.repos.d/mssql-release.repo
exit
sudo yum update
sudo yum remove unixODBC #to avoid conflicts
sudo ACCEPT_EULA=Y yum install msodbcsql-13.0.1.0-1 mssql-tools-14.0.2.0-1
sudo yum install unixODBC-utf16-devel #this step is optional but recommended*
#Create symlinks for tools
ln -sfn /opt/mssql-tools/bin/sqlcmd-13.0.1.0 /usr/bin/sqlcmd 
ln -sfn /opt/mssql-tools/bin/bcp-13.0.1.0 /usr/bin/bcp
```

### <a name="ubuntu-1510"></a>Ubuntu 15.10

```bash
sudo su 
curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add -
curl https://packages.microsoft.com/config/ubuntu/15.10/prod.list > /etc/apt/sources.list.d/mssql-release.list
exit
sudo apt-get update
sudo ACCEPT_EULA=Y apt-get install msodbcsql=13.0.1.0-1 mssql-tools=14.0.2.0-1
sudo apt-get install unixodbc-dev-utf16 #this step is optional but recommended*
#Create symlinks for tools
ln -sfn /opt/mssql-tools/bin/sqlcmd-13.0.1.0 /usr/bin/sqlcmd 
ln -sfn /opt/mssql-tools/bin/bcp-13.0.1.0 /usr/bin/bcp
```

### <a name="ubuntu-1604"></a>Ubuntu 16.04

```bash
sudo su 
curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add -
curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > /etc/apt/sources.list.d/mssql-release.list
exit
sudo apt-get update
sudo ACCEPT_EULA=Y apt-get install msodbcsql=13.0.1.0-1 mssql-tools=14.0.2.0-1
sudo apt-get install unixodbc-dev-utf16 #this step is optional but recommended*
#Create symlinks for tools
ln -sfn /opt/mssql-tools/bin/sqlcmd-13.0.1.0 /usr/bin/sqlcmd 
ln -sfn /opt/mssql-tools/bin/bcp-13.0.1.0 /usr/bin/bcp
```

### <a name="suse-linux-enterprise-server-12"></a>SUSE Linux Enterprise Server 12

```bash
sudo su 
zypper ar https://packages.microsoft.com/config/sles/12/prod.repo 
zypper update 
sudo ACCEPT_EULA=Y zypper install msodbcsql-13.0.1.0-1 mssql-tools-14.0.2.0-1
zypper install unixODBC-utf16-devel
#Create symlinks for tools
ln -sfn /opt/mssql-tools/bin/sqlcmd-13.0.1.0 /usr/bin/sqlcmd 
ln -sfn /opt/mssql-tools/bin/bcp-13.0.1.0 /usr/bin/bcp
```

### <a name="offline-installation"></a>オフライン インストール

インターネット接続なしでコンピューターに [!INCLUDE[msCoName](../../../includes/msconame_md.md)] ODBC Driver 13 をインストールする場合は、パッケージの依存関係を手動で解決する必要があります。 [!INCLUDE[msCoName](../../../includes/msconame_md.md)] ODBC Driver 13 には、次の直接的な依存関係があります。

- Ubuntu: libc6 (>= 2.21)、libstdc++6 (>= 4.9)、libkrb5-3、libcurl3、openssl、debconf (>= 0.5)、unixodbc (>= 2.3.1-1)
- Red Hat: ```glibc, e2fsprogs, krb5-libs, openssl, unixODBC```
- SUSE: ```glibc, libuuid1, krb5, openssl, unixODBC```

これらの各パッケージにはそれぞれ独自の依存関係があり、システムに存在する場合と存在しない場合があります。 この問題の一般的な解決策については、ディストリビューションのパッケージ マネージャーの [Red Hat](https://wiki.centos.org/HowTos/CreateLocalRepos)、[Ubuntu](https://unix.stackexchange.com/questions/87130/how-to-quickly-create-a-local-apt-repository-for-random-packages-using-a-debian)、および [SUSE](https://en.opensuse.org/Portal:Zypper) に関するドキュメントを参照してください。

また、すべての従属パッケージを手動でダウンロードし、インストール コンピューターにまとめて配置してから、各パッケージを順に手動でインストールし、[!INCLUDE[msCoName](../../../includes/msconame_md.md)] ODBC Driver 13 パッケージを終了する方法も一般的です。

#### <a name="red-hat-linux-enterprise-server-7"></a>Red Hat Linux Enterprise Server 7

- 最新の `msodbcsql``.rpm` を [https://packages.microsoft.com/rhel/7/prod/](https://packages.microsoft.com/rhel/7/prod/) からダウンロードします。
- 依存関係とドライバーをインストールします。
  
```bash
yum install glibc e2fsprogs krb5-libs openssl unixODBC unixODBC-devel #install dependencies
sudo rpm -i  msodbcsql-13.1.X.X-X.x86_64.rpm #install the Driver
```

#### <a name="ubuntu-1604"></a>Ubuntu 16.04

- 最新の `msodbcsql``.deb` を [https://packages.microsoft.com/ubuntu/16.04/prod/pool/main/m/msodbcsql/](https://packages.microsoft.com/ubuntu/16.04/prod/pool/main/m/msodbcsql/) からダウンロードします。
- 依存関係とドライバーをインストールします。

```bash
sudo apt-get install libc6 libstdc++6 libkrb5-3 libcurl3 openssl debconf unixodbc unixodbc-dev #install dependencies
sudo dpkg -i msodbcsql_13.1.X.X-X_amd64.deb #install the Driver
```

#### <a name="suse-linux-enterprise-server-12"></a>SUSE Linux Enterprise Server 12

- 最新の `msodbcsql``.rpm` を [https://packages.microsoft.com/sles/12/prod/](https://packages.microsoft.com/sles/12/prod/) からダウンロードします。
- 依存関係とドライバーをインストールします。

```bash
zypper install glibc, libuuid1, krb5, openssl, unixODBC unixODBC-devel #install dependencies
sudo rpm -i  msodbcsql-13.1.X.X-X.x86_64.rpm #install the Driver
```

パッケージのインストールを完了したら、ldd を実行し、不足しているライブラリがないか出力を調べることで、[!INCLUDE[msCoName](../../../includes/msconame_md.md)] ODBC Driver 13 がすべての依存関係を検出できることを確認できます。

```bash
ldd /opt/microsoft/msodbcsql/lib64/libmsodbcsql-*
```

## <a name="odbc-11"></a><a id="11"></a> ODBC 11

次のセクションでは、Microsoft ODBC Driver 11 を Linux にインストールする方法について説明します。 ドライバーを使用する前に、unixODBC ドライバー マネージャーをインストールしておく必要があります。 詳細については、「[ドライバー マネージャーのインストール](../../../connect/odbc/linux-mac/installing-the-driver-manager.md)」を参照してください。

### <a name="installation-steps"></a>インストール手順  

> [!IMPORTANT]  
> 次の手順では、Red Hat Linux 用のインストール ファイル `msodbcsql-11.0.2270.0.tar.gz` を参照しています。 Preview for SUSE Linux をインストールする場合のファイル名は `msodbcsql-11.0.2260.0.tar.gz` です。  
  
ドライバーをインストールするには:

1. ルートのアクセス許可があることを確認します。  

2. ダウンロードでファイル `msodbcsql-11.0.2270.0.tar.gz` を配置したディレクトリに切り替えます。 使用している Linux のバージョンに対応する \*.tar.gz ファイルがあることを確認します。 ファイルを解凍するには、コマンド `tar xvzf msodbcsql-11.0.2270.0.tar.gz` を実行します。  
  
3. `msodbcsql-11.0.2270.0` のディレクトリに移動すると、ディレクトリ内に **install.sh** というファイルがあることを確認できます。  
  
4. 使用可能なインストール オプションの一覧を表示するには、コマンド **./install.sh** を実行します。  
  
5. **odbcinst.ini** のバックアップを作成します。 ドライバーのインストールで、 **odbcinst.ini** を更新します。 odbcinst.ini には、unixODBC ドライバー マネージャーで登録されたドライバーの一覧が含まれます。 コンピューターの odbcinst.ini の場所を検出するには、コマンド ```odbc_config --odbcinstini``` を実行します。  
  
6. ドライバーをインストールする前に、コマンド `./install.sh verify` を実行します。 Linux で ODBC ドライバーをサポートするためにコンピューターに必要なソフトウェアがある場合、`./install.sh verify` の出力でレポートされます。  
  
7. Linux に ODBC ドライバーをインストールする準備が整ったら、コマンド `./install.sh install` を実行します。 インストール コマンド (`bin-dir` または `lib-dir`) を指定する必要がある場合は、**install** オプションの後にコマンドを指定してください。  
  
8. 使用許諾契約を確認し、 **YES** と入力してインストールを続行します。  
  
インストールで、ドライバーが `/opt/microsoft/msodbcsql/11.0.2270.0` に配置されます。 `/opt/microsoft/msodbcsql/11.0.2270.0` には、ドライバーとそのサポート ファイルが保存されている必要があります。  
  
Linux の Microsoft ODBC ドライバーが正常に登録されたことを確認するには、コマンド ```odbcinst -q -d -n "ODBC Driver 11 for SQL Server"``` を実行します。  
  
### <a name="uninstall"></a>アンインストール  
  
Linux の ODBC ドライバー 11 をアンインストールするには、次のコマンドを実行します。  
  
1. `rm -f /usr/bin/sqlcmd`
  
2. `rm -f /usr/bin/bcp`  
  
3. `rm -rf /opt/microsoft/msodbcsql`  
  
4. `odbcinst -u -d -n "ODBC Driver 11 for SQL Server"`
  
## <a name="driver-files"></a>ドライバー ファイル

Linux 上の ODBC ドライバーは、次のコンポーネントで構成されています。

|コンポーネント|説明|  
|---------------|-----------------|  
|libmsodbcsql-17.X.so.X.X または libmsodbcsql-13.X.so.X.X|ドライバーのすべての機能を含む共有オブジェクト (`so`) ダイナミック ライブラリ ファイル。 このファイルは、Driver 17 では `/opt/microsoft/msodbcsql17/lib64/`、Driver 13 では `/opt/microsoft/msodbcsql/lib64/` にインストールされます。|  
|`msodbcsqlr17.rll` または `msodbcsqlr13.rll`|ドライバー ライブラリに付随するリソース ファイル。 このファイルは `[driver .so directory]../share/resources/en_US/` にインストールされます|
|msodbcsql.h|ドライバーを使用するために必要な新しい定義がすべて含まれているヘッダー ファイル。<br /><br /> **注:** msodbcsql.h と odbcss.h を同じプログラムで参照することはできません。<br /><br /> msodbcsql.h は、Driver 17 では `/opt/microsoft/msodbcsql17/include/`、Driver 13 では `/opt/microsoft/msodbcsql/include/` にインストールされます。 |
|LICENSE.txt|使用許諾契約書の条項を含むテキスト ファイル。 このファイルは、Driver 17 では `/usr/share/doc/msodbcsql17/`、Driver 13 では `/usr/share/doc/msodbcsql/` に配置されます。|
|RELEASE_NOTES|リリース ノートを含むテキスト ファイル。 このファイルは、Driver 17 では `/usr/share/doc/msodbcsql17/`、Driver 13 では `/usr/share/doc/msodbcsql/` に配置されます。|

## <a name="resource-file-loading"></a>リソース ファイルの読み込み

ドライバーが機能するには、リソース ファイルを読み込む必要があります。 このファイルは、ドライバーのバージョンに応じて `msodbcsqlr17.rll` または `msodbcsqlr13.rll` という名前になります。 `.rll` ファイルの場所は、上の表に示されているとおり、ドライバー自体の場所 (`so` または `dylib`) に対して相対的です。 バージョン 17.1 の時点で、相対パスからの読み込みが失敗した場合、ドライバーは既定のディレクトリからも `.rll` の読み込みを試みます。 Linux での既定のリソース ファイル パスは `/opt/microsoft/msodbcsql17/share/resources/en_US/` です。

## <a name="troubleshooting"></a>トラブルシューティング

ODBC ドライバーを使用して SQL Server に接続できない場合は、[接続の問題のトラブルシューティング](known-issues-in-this-version-of-the-driver.md#connectivity)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

ドライバーをインストールした後は、[C++ の ODBC サンプル アプリケーション](../../odbc/cpp-code-example-app-connect-access-sql-db.md)を試すことができます。 ODBC アプリケーションの開発に関する詳細については、「[アプリケーションの開発](../../../odbc/reference/develop-app/developing-applications.md)」を参照してください。

詳細については、ODBC ドライバーの[リリース ノート](release-notes-odbc-sql-server-linux-mac.md)と[システム要件](system-requirements.md)に関するページを参照してください。
