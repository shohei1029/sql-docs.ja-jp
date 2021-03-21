---
title: Drivers for PHP 用の Linux と macOS のインストール
description: この手順では、SQL Server on Linux または macOS 用の Microsoft Drivers for PHP をインストールする方法について説明します。
ms.date: 03/15/2021
ms.prod: sql
ms.prod_service: connectivity
ms.custom: ''
ms.technology: connectivity
ms.topic: conceptual
author: David-Engel
ms.author: v-daenge
manager: v-mabarw
ms.openlocfilehash: 33cb8913e240b83e1ada40ebf262acca85472e9b
ms.sourcegitcommit: ecf074e374426c708073c7da88313d4915279fb9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103575273"
---
# <a name="linux-and-macos-installation-tutorial-for-the-microsoft-drivers-for-php-for-sql-server"></a>Microsoft Drivers for PHP for SQL Server の Linux および macOS インストール チュートリアル

次の手順では、クリーンな環境が想定されています。Ubuntu、Red Hat、Debian、Suse、Alpine、および macOS に PHP 8.0、Microsoft ODBC ドライバー、Apache Web サーバー、および Microsoft SQL Server 用 Drivers for PHP をインストールする方法について説明します。 これらの手順では、PECL を使用してドライバーをインストールすることをお勧めしていますが、[Microsoft Drivers for PHP for SQL Server](https://github.com/Microsoft/msphpsql/releases) GitHub プロジェクト ページから事前構築済みバイナリをダウンロードし、「[Microsoft Drivers for PHP for SQL Server の読み込み](../../connect/php/loading-the-php-sql-driver.md)」の手順に従ってそれらをインストールすることもできます。 拡張機能の読み込みおよび php.ini に拡張機能を追加しない理由の説明については、[ドライバーの読み込み](../../connect/php/loading-the-php-sql-driver.md#loading-the-driver-at-php-startup)に関するセクションを参照してください。

次の手順では、PHP 8.0 パッケージが使用可能な場合に、既定で `pecl install` を使用して PHP 8.0 をインストールします。 最初に `pecl channel-update pecl.php.net` を実行することが必要な場合があります。 サポートされている一部の Linux ディストリビューションでは、既定で PHP 7.1 が設定されますが、これは最新バージョンの SQL Server 用 PHP ドライバーではサポートされていません。 各セクションの先頭にある注記を参照して、代わりに PHP 7.4 または 7.3 をインストールしてください。

Ubuntu に PHP FastCGI Process Manager (PHP FPM) をインストールする手順も記載されています。 Apache ではなく nginx Web サーバーを使用する場合は、PHP-FPM が必要です。

これらの手順には SQLSRV ドライバーと PDO_SQLSRV ドライバーの両方をインストールするコマンドが含まれていますが、ドライバーは非依存でインストールし、機能できます。 構成のカスタマイズになれているユーザーは、SQLSRV または PDO_SQLSRV をだけを対象にしてこれらの手順を調整できます。 いずれのドライバーにも、下記に別途記載する部分を除き、同じ依存関係が与えられます。

## <a name="installing-on-ubuntu"></a>Ubuntu へのインストール

Ubuntu バージョン 16.04、18.04、および 20.04 がサポートされています。

> [!NOTE]
> PHP 7.4 または 7.3 をインストールするには、次のコマンドの 8.0 を 7.4 または 7.3 に置き換えます。

### <a name="step-1-install-php-ubuntu"></a>手順 1. PHP をインストールする (Ubuntu)

```bash
sudo su
add-apt-repository ppa:ondrej/php -y
apt-get update
apt-get install php8.0 php8.0-dev php8.0-xml -y --allow-unauthenticated
```

### <a name="step-2-install-prerequisites-ubuntu"></a>手順 2. 必須コンポーネントをインストールする (Ubuntu)

「[Microsoft ODBC Driver for SQL Server をインストールする (Linux)](../../connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server.md)」の手順に従って、Ubuntu 用 ODBC ドライバーをインストールします。 オプションの `unixodbc-dev` パッケージも必ずインストールしてください。 これは、PHP ドライバーをインストールするために `pecl` コマンドによって使用されます。

### <a name="step-3-install-the-php-drivers-for-microsoft-sql-server-ubuntu"></a>手順 3. Microsoft SQL Server 用 PHP ドライバーをインストールする (Ubuntu)

```bash
sudo pecl install sqlsrv
sudo pecl install pdo_sqlsrv
sudo su
printf "; priority=20\nextension=sqlsrv.so\n" > /etc/php/8.0/mods-available/sqlsrv.ini
printf "; priority=30\nextension=pdo_sqlsrv.so\n" > /etc/php/8.0/mods-available/pdo_sqlsrv.ini
exit
sudo phpenmod -v 8.0 sqlsrv pdo_sqlsrv
```

システムに PHP バージョンが 1 つしかない場合は、最後の手順を `phpenmod sqlsrv pdo_sqlsrv` に簡略化できます。

### <a name="step-4-install-apache-and-configure-driver-loading-ubuntu"></a>手順 4. Apache をインストールし、ドライバーの読み込みを構成する (Ubuntu)

```bash
sudo su
apt-get install libapache2-mod-php8.0 apache2
a2dismod mpm_event
a2enmod mpm_prefork
a2enmod php8.0
exit
```

### <a name="step-5-restart-apache-and-test-the-sample-script-ubuntu"></a>手順 5. Apache を再起動し、サンプル スクリプトをテストする (Ubuntu)

```bash
sudo service apache2 restart
```

インストールをテストするには、このドキュメントの最後の「[インストールのテスト](#testing-your-installation)」を参照してください。

## <a name="installing-on-ubuntu-with-php-fpm"></a>PHP-FPM がインストールされた Ubuntu へのインストール

Ubuntu バージョン 16.04、18.04、および 20.04 がサポートされています。

> [!NOTE]
> PHP 7.4 または 7.3 をインストールするには、次のコマンドの 8.0 を 7.4 または 7.3 に置き換えます。

### <a name="step-1-install-php-ubuntu-with-php-fpm"></a>手順 1. PHP をインストールする (PHP-FPM がインストールされた Ubuntu)

```bash
sudo su
add-apt-repository ppa:ondrej/php -y
apt-get update
apt-get install php8.0 php8.0-dev php8.0-fpm php8.0-xml -y --allow-unauthenticated
```

以下を実行して、PHP-FPM サービスの状態を確認します。

```bash
systemctl status php8.0-fpm
```

### <a name="step-2-install-prerequisites-ubuntu-with-php-fpm"></a>手順 2. 必須コンポーネントをインストールする (PHP-FPM がインストールされた Ubuntu)

「[Microsoft ODBC Driver for SQL Server をインストールする (Linux)](../../connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server.md)」の手順に従って、Ubuntu 用 ODBC ドライバーをインストールします。 オプションの `unixodbc-dev` パッケージも必ずインストールしてください。 これは、PHP ドライバーをインストールするために `pecl` コマンドによって使用されます。

### <a name="step-3-install-the-php-drivers-for-microsoft-sql-server-ubuntu-with-php-fpm"></a>手順 3. Microsoft SQL Server 用 PHP ドライバーをインストールする (PHP-FPM がインストールされた Ubuntu)

```bash
sudo pecl config-set php_ini /etc/php/8.0/fpm/php.ini
sudo pecl install sqlsrv
sudo pecl install pdo_sqlsrv
sudo su
printf "; priority=20\nextension=sqlsrv.so\n" > /etc/php/8.0/mods-available/sqlsrv.ini
printf "; priority=30\nextension=pdo_sqlsrv.so\n" > /etc/php/8.0/mods-available/pdo_sqlsrv.ini
exit
sudo phpenmod -v 8.0 sqlsrv pdo_sqlsrv
```

システムに PHP バージョンが 1 つしかない場合は、最後の手順を `phpenmod sqlsrv pdo_sqlsrv` に簡略化できます。

`sqlsrv.ini` と `pdo_sqlsrv.ini` が `/etc/php/8.0/fpm/conf.d/` に配置されていることを確認します。

```bash
ls /etc/php/8.0/fpm/conf.d/*sqlsrv.ini
```

PHP-FPM サービスを再起動します。

```bash
sudo systemctl restart php8.0-fpm
```

### <a name="step-4-install-and-configure-nginx-ubuntu-with-php-fpm"></a>手順 4. nginx をインストールして構成する (PHP-FPM がインストールされた Ubuntu)

```bash
sudo apt-get update
sudo apt-get install nginx
sudo systemctl status nginx
```

nginx を構成するには、`/etc/nginx/sites-available/default` ファイルを編集する必要があります。 `# Add index.php to the list if you are using PHP` というセクションの下にある一覧に `index.php` を追加します。

```config
# Add index.php to the list if you are using PHP
index index.html index.htm index.nginx-debian.html index.php;
```

次に、以下のように `# pass PHP scripts to FastCGI server` に続くセクションをコメント解除して変更します。

```config
# pass PHP scripts to FastCGI server
#
location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/run/php/php8.0-fpm.sock;
}
```

### <a name="step-5-restart-nginx-and-test-the-sample-script-ubuntu-with-php-fpm"></a>手順 5. nginx を再起動し、サンプル スクリプトをテストする (PHP-FPM がインストールされた Ubuntu)

```bash
sudo systemctl restart nginx.service
```

インストールをテストするには、このドキュメントの最後の「[インストールのテスト](#testing-your-installation)」を参照してください。

## <a name="installing-on-red-hat"></a>Red Hat へのインストール

Red Hat バージョン 7 と 8 がサポートされています。

### <a name="step-1-install-php-red-hat"></a>手順 1. PHP をインストールする (Red Hat)

PHP を Red Hat 7 にインストールするには、次のコマンドを実行します。
> [!NOTE]
> PHP 7.4 または 7.3 をインストールするには、次のコマンドの remi-php80 を remi-php74 または remi-php73 に置き換えます。

```bash
sudo su
yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
yum install https://rpms.remirepo.net/enterprise/remi-release-7.rpm
subscription-manager repos --enable=rhel-7-server-optional-rpms
yum install yum-utils
yum-config-manager --enable remi-php80
yum update
# Note: The php-pdo package is required only for the PDO_SQLSRV driver
yum install php php-pdo php-xml php-pear php-devel re2c gcc-c++ gcc
```

PHP を Red Hat 8 にインストールするには、次のコマンドを実行します。
> [!NOTE]
> PHP 7.4 または 7.3 をインストールするには、次のコマンドの remi-8.0 を remi-7.4 または remi-7.3 に置き換えます。

```bash
sudo su
dnf install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
dnf install https://rpms.remirepo.net/enterprise/remi-release-8.rpm
dnf install yum-utils
dnf module reset php
dnf module install php:remi-8.0
subscription-manager repos --enable codeready-builder-for-rhel-8-x86_64-rpms
dnf update
# Note: The php-pdo package is required only for the PDO_SQLSRV driver
dnf install php-pdo php-pear php-devel
```

### <a name="step-2-install-prerequisites-red-hat"></a>手順 2. 必須コンポーネントをインストールする (Red Hat)

「[Microsoft ODBC Driver for SQL Server をインストールする (Linux)](../../connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server.md)」の手順に従って、Red Hat 7 または 8 用の ODBC ドライバーをインストールします。 オプションの `unixodbc-dev` パッケージも必ずインストールしてください。 これは、PHP ドライバーをインストールするために `pecl` コマンドによって使用されます。

### <a name="step-3-install-the-php-drivers-for-microsoft-sql-server-red-hat"></a>手順 3. Microsoft SQL Server 用 PHP ドライバーをインストールする (Red Hat)

```bash
sudo pecl install sqlsrv
sudo pecl install pdo_sqlsrv
sudo su
echo extension=pdo_sqlsrv.so >> `php --ini | grep "Scan for additional .ini files" | sed -e "s|.*:\s*||"`/30-pdo_sqlsrv.ini
echo extension=sqlsrv.so >> `php --ini | grep "Scan for additional .ini files" | sed -e "s|.*:\s*||"`/20-sqlsrv.ini
exit
```

Remi リポジトリからインストールすることもできます。

```bash
sudo yum install php-sqlsrv
```

### <a name="step-4-install-apache-red-hat"></a>手順 4. Apache をインストールする (Red Hat)

```bash
sudo yum install httpd
```

SELinux は既定でインストールされ、Enforcing モードで実行します。 Apache が SELinux 経由でデータベースに接続できるようにするには、次のコマンドを実行します。

```bash
sudo setsebool -P httpd_can_network_connect_db 1
```

### <a name="step-5-restart-apache-and-test-the-sample-script-red-hat"></a>手順 5. Apache を再起動し、サンプル スクリプトをテストする (Red Hat)

```bash
sudo apachectl restart
```

インストールをテストするには、このドキュメントの最後の「[インストールのテスト](#testing-your-installation)」を参照してください。

## <a name="installing-on-debian"></a>Debian へのインストール

Debian バージョン 9 と 10 がサポートされています。

> [!NOTE]
> PHP 7.4 または 7.3 をインストールするには、次のコマンドの 8.0 を 7.4 または 7.3 に置き換えます。

### <a name="step-1-install-php-debian"></a>手順 1. PHP をインストールする (Debian)

```bash
sudo su
apt-get install curl apt-transport-https
wget -O /etc/apt/trusted.gpg.d/php.gpg https://packages.sury.org/php/apt.gpg
echo "deb https://packages.sury.org/php/ $(lsb_release -sc) main" > /etc/apt/sources.list.d/php.list
apt-get update
apt-get install -y php8.0 php8.0-dev php8.0-xml php8.0-intl
```

### <a name="step-2-install-prerequisites-debian"></a>手順 2. 必須コンポーネントをインストールする (Debian)

「[Microsoft ODBC Driver for SQL Server をインストールする (Linux)](../../connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server.md)」の手順に従って、Debian 用 ODBC ドライバーをインストールします。  オプションの `unixodbc-dev` パッケージも必ずインストールしてください。 これは、PHP ドライバーをインストールするために `pecl` コマンドによって使用されます。

また、PHP の出力をブラウザーで正しく表示させるために、正しいロケールを生成する必要がある場合もあります。 たとえば、en_US UTF-8 ロケールの場合、次のコマンドを実行します。

```bash
sudo su
sed -i 's/# en_US.UTF-8 UTF-8/en_US.UTF-8 UTF-8/g' /etc/locale.gen
locale-gen
```

`$PATH` への `/usr/sbin` の追加が必要になる場合があります。これは `locale-gen` 実行可能ファイルがその場所に配置されるためです。

### <a name="step-3-install-the-php-drivers-for-microsoft-sql-server-debian"></a>手順 3. Microsoft SQL Server 用 PHP ドライバーをインストールする (Debian)

```bash
sudo pecl install sqlsrv
sudo pecl install pdo_sqlsrv
sudo su
printf "; priority=20\nextension=sqlsrv.so\n" > /etc/php/8.0/mods-available/sqlsrv.ini
printf "; priority=30\nextension=pdo_sqlsrv.so\n" > /etc/php/8.0/mods-available/pdo_sqlsrv.ini
exit
sudo phpenmod -v 8.0 sqlsrv pdo_sqlsrv
```

システムに PHP バージョンが 1 つしかない場合は、最後の手順を `phpenmod sqlsrv pdo_sqlsrv` に簡略化できます。 `locale-gen` と同様に、`phpenmod` も `/usr/sbin` に配置されるため、このディレクトリの `$PATH` への追加が必要になる場合があります。

### <a name="step-4-install-apache-and-configure-driver-loading-debian"></a>手順 4. Apache をインストールし、ドライバーの読み込みを構成する (Debian)

```bash
sudo su
apt-get install libapache2-mod-php8.0 apache2
a2dismod mpm_event
a2enmod mpm_prefork
a2enmod php8.0
```

### <a name="step-5-restart-apache-and-test-the-sample-script-debian"></a>手順 5. Apache を再起動し、サンプル スクリプトをテストする (Debian)

```bash
sudo service apache2 restart
```

インストールをテストするには、このドキュメントの最後の「[インストールのテスト](#testing-your-installation)」を参照してください。

## <a name="installing-on-suse"></a>Suse へのインストール

Suse Enterprise Linux バージョン 12 と 15 がサポートされています。

> [!NOTE]
> 次の手順では、`<SuseVersion>` を、使用している Suse のバージョンに置き換えます。 Suse Enterprise Linux 15 を使用している場合は、SLE_15_SP1 または SLE_15_SP2 になります。 Suse 12 の場合は、SLE_12_SP4 (妥当な場合はそれ以降) を使用します。 Suse Linux のすべてのバージョンで、PHP のすべてのバージョンが利用できるわけではありません。 既定のバージョンの PHP を使用できる Suse のバージョンを確認するには、`http://download.opensuse.org/repositories/devel:/languages:/php` を参照してください。または、どのバージョンの Suse で、PHP の他のどのバージョンが使用できるかを確認するには、`http://download.opensuse.org/repositories/devel:/languages:/php:/` を参照してください。

> [!NOTE]
> Suse 12 では、PHP 7.4 以降のパッケージは使用できません。Suse 15 では、PHP 8.0 のパッケージはまだ使用できません。
> PHP 7.3 をインストールするには、リポジトリの URL を次の URL に置き換えます。`https://download.opensuse.org/repositories/devel:/languages:/php:/php73/<SuseVersion>/devel:languages:php:php73.repo`

### <a name="step-1-install-php-suse"></a>手順 1. PHP をインストールする (Suse)

```bash
sudo su
zypper -n ar -f https://download.opensuse.org/repositories/devel:languages:php/<SuseVersion>/devel:languages:php.repo
zypper --gpg-auto-import-keys refresh
zypper -n install php7 php7-devel php7-openssl
```

### <a name="step-2-install-prerequisites-suse"></a>手順 2. 必須コンポーネントをインストールする (Suse)

「[Microsoft ODBC Driver for SQL Server をインストールする (Linux)](../../connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server.md)」の手順に従って、Suse 用 ODBC ドライバーをインストールします。 オプションの `unixodbc-dev` パッケージも必ずインストールしてください。 これは、PHP ドライバーをインストールするために `pecl` コマンドによって使用されます。

### <a name="step-3-install-the-php-drivers-for-microsoft-sql-server-suse"></a>手順 3. Microsoft SQL Server 用 PHP ドライバーをインストールする (Suse)

> [!NOTE]
> `Connection to 'pecl.php.net:443' failed: Unable to find the socket transport "ssl"` というエラー メッセージが表示された場合、/usr/bin/pecl にある pecl スクリプトを編集し、最後の行の `-n` スイッチを削除します。 このスイッチによって、PHP が呼び出されたときに、PECL の ini ファイルの読み込みが妨げられ、それによって、OpenSSL 拡張機能の読み込みが妨げられます。

```bash
sudo pecl install sqlsrv
sudo pecl install pdo_sqlsrv
sudo su
echo extension=pdo_sqlsrv.so >> `php --ini | grep "Scan for additional .ini files" | sed -e "s|.*:\s*||"`/pdo_sqlsrv.ini
echo extension=sqlsrv.so >> `php --ini | grep "Scan for additional .ini files" | sed -e "s|.*:\s*||"`/sqlsrv.ini
exit
```

### <a name="step-4-install-apache-and-configure-driver-loading-suse"></a>手順 4. Apache をインストールし、ドライバーの読み込みを構成する (Suse)

```bash
sudo su
zypper install apache2 apache2-mod_php7
a2enmod php7
echo "extension=sqlsrv.so" >> /etc/php7/apache2/php.ini
echo "extension=pdo_sqlsrv.so" >> /etc/php7/apache2/php.ini
exit
```

### <a name="step-5-restart-apache-and-test-the-sample-script-suse"></a>手順 5. Apache を再起動し、サンプル スクリプトをテストする (Suse)

```bash
sudo systemctl restart apache2
```

インストールをテストするには、このドキュメントの最後の「[インストールのテスト](#testing-your-installation)」を参照してください。

## <a name="installing-on-alpine"></a>Alpine へのインストール

Alpine バージョン 3.11 と 3.12 がサポートされています。

> [!NOTE]
> PHP の既定のバージョンは 7.3 です。 PHP 7.4 以降は、Alpine のテストまたはエッジ リポジトリから入手できる場合があります。 代わりに、ソースから PHP をコンパイルできます。

### <a name="step-1-install-php-alpine"></a>手順 1. PHP をインストールする (Alpine)

Alpine 用の PHP パッケージは、`edge/community` リポジトリにあります。 WIKI ページで「[コミュニティ リポジトリの有効化](https://wiki.alpinelinux.org/wiki/Enable_Community_Repository)」を確認してください。 次の行の `<mirror>` を Alpine リポジトリ ミラーの URL に置き換えて、`/etc/apk/repositories` に追加します。

```bash
http://<mirror>/alpine/edge/community
```

次に、次のコマンドを実行します。

```bash
sudo su
apk update
# Note: The php7-pdo package is required only for the PDO_SQLSRV driver
apk add php7 php7-dev php7-pear php7-pdo php7-openssl autoconf make g++
```

### <a name="step-2-install-prerequisites-alpine"></a>手順 2. 必須コンポーネントをインストールする (Alpine)

「[Microsoft ODBC Driver for SQL Server をインストールする (Linux)](../../connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server.md)」の手順に従って、Alpine 用 ODBC ドライバーをインストールします。  必ず `unixodbc-dev` パッケージ (`sudo apk add unixodbc-dev`) もインストールします。 これは、PHP ドライバーをインストールするために `pecl` コマンドによって使用されます。

### <a name="step-3-install-the-php-drivers-for-microsoft-sql-server-alpine"></a>手順 3. Microsoft SQL Server 用 PHP ドライバーをインストールする (Alpine)

```bash
sudo pecl install sqlsrv
sudo pecl install pdo_sqlsrv
sudo su
echo extension=pdo_sqlsrv.so >> `php --ini | grep "Scan for additional .ini files" | sed -e "s|.*:\s*||"`/10_pdo_sqlsrv.ini
echo extension=sqlsrv.so >> `php --ini | grep "Scan for additional .ini files" | sed -e "s|.*:\s*||"`/00_sqlsrv.ini
```

### <a name="step-4-install-apache-and-configure-driver-loading-alpine"></a>手順 4. Apache をインストールし、ドライバーの読み込みを構成する (Alpine)

```bash
sudo apk add php7-apache2 apache2
```

### <a name="step-5-restart-apache-and-test-the-sample-script-alpine"></a>手順 5. Apache を再起動し、サンプル スクリプトをテストする (Alpine)

```bash
sudo rc-service apache2 restart
```

インストールをテストするには、このドキュメントの最後の「[インストールのテスト](#testing-your-installation)」を参照してください。

## <a name="installing-on-macos"></a>macOS へのインストール

MacOS バージョン 10.14 (Mojave)、10.15 (Catalina)、および 11.0 (Big Sur) がサポートされています。

brew がまだない場合は、次のようにそれをインストールします。

```bash
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

> [!NOTE]
> PHP 7.4 または 7.3 をインストールするには、次のコマンドの php@8.0 を php@7.4 または php@7.3 に置き換えます。

### <a name="step-1-install-php-macos"></a>手順 1. PHP をインストールする (macOS)

```bash
brew tap
brew tap homebrew/core
brew install php@8.0
```

PHP がパスに含まれるようになったはずです。 `php -v` を実行して、正しいバージョンの PHP が実行されていることを確認します。 PHP がパスにないか、または正しいバージョンでない場合は、次のコマンドを実行します。

```bash
brew link --force --overwrite php@8.0
```

### <a name="step-2-install-prerequisites-macos"></a>手順 2. 必須コンポーネントをインストールする (macOS)

「[Microsoft ODBC Driver for SQL Server をインストールする (macOS)](../../connect/odbc/linux-mac/install-microsoft-odbc-driver-sql-server-macos.md)」の手順に従って、macOS 用 ODBC ドライバーをインストールします。

さらに、GNU make ツールをインストールする必要がある場合があります。

```bash
brew install autoconf automake libtool
```

### <a name="step-3-install-the-php-drivers-for-microsoft-sql-server-macos"></a>手順 3. Microsoft SQL Server 用 PHP ドライバーをインストールする (macOS)

```bash
sudo pecl install sqlsrv
sudo pecl install pdo_sqlsrv
```

### <a name="step-4-install-apache-and-configure-driver-loading-macos"></a>手順 4. Apache をインストールし、ドライバーの読み込みを構成する (macOS)

```bash
brew install apache2
```

Apache インストールの Apache 構成ファイルである `httpd.conf` を検索するには、以下を実行します。

```bash
/usr/local/bin/apachectl -V | grep SERVER_CONFIG_FILE
```

次のコマンドを使用して、必要な構成を `httpd.conf` に追加します。 必ず `/usr/local/etc/httpd/httpd.conf` を前のコマンドで返されたパスに置き換えてください。

```bash
echo "LoadModule php7_module /usr/local/opt/php@8.0/lib/httpd/modules/libphp7.so" >> /usr/local/etc/httpd/httpd.conf
(echo "<FilesMatch .php$>"; echo "SetHandler application/x-httpd-php"; echo "</FilesMatch>";) >> /usr/local/etc/httpd/httpd.conf
```

### <a name="step-5-restart-apache-and-test-the-sample-script-macos"></a>手順 5. Apache を再起動し、サンプル スクリプトをテストする (macOS)

```bash
sudo apachectl restart
```

インストールをテストするには、このドキュメントの最後の「[インストールのテスト](#testing-your-installation)」を参照してください。

## <a name="testing-your-installation"></a>インストールのテスト

このサンプル スクリプトをテストするには、システムのドキュメント ルートに testsql.php というファイルを作成します。 このパスは、Ubuntu、Debian、および Red Hat では `/var/www/html/`、SUSE では `/srv/www/htdocs`、Alpine では `/var/www/localhost/htdocs`、macOS では `/usr/local/var/www` です。 次のスクリプトをそれにコピーし、該当する場合にサーバー、データベース、ユーザー名、およびパスワードを置き換えます。

### <a name="sqlsrv-example"></a>SQLSRV の例

```php
<?php
$serverName = "yourServername";
$connectionOptions = array(
    "database" => "yourDatabase",
    "uid" => "yourUsername",
    "pwd" => "yourPassword"
);

function exception_handler($exception) {
    echo "<h1>Failure</h1>";
    echo "Uncaught exception: " , $exception->getMessage();
    echo "<h1>PHP Info for troubleshooting</h1>";
    phpinfo();
}

set_exception_handler('exception_handler');

// Establishes the connection
$conn = sqlsrv_connect($serverName, $connectionOptions);
if ($conn === false) {
    die(formatErrors(sqlsrv_errors()));
}

// Select Query
$tsql = "SELECT @@Version AS SQL_VERSION";

// Executes the query
$stmt = sqlsrv_query($conn, $tsql);

// Error handling
if ($stmt === false) {
    die(formatErrors(sqlsrv_errors()));
}
?>

<h1> Success Results : </h1>

<?php
while ($row = sqlsrv_fetch_array($stmt, SQLSRV_FETCH_ASSOC)) {
    echo $row['SQL_VERSION'] . PHP_EOL;
}

sqlsrv_free_stmt($stmt);
sqlsrv_close($conn);

function formatErrors($errors)
{
    // Display errors
    echo "<h1>SQL Error:</h1>";
    echo "Error information: <br/>";
    foreach ($errors as $error) {
        echo "SQLSTATE: ". $error['SQLSTATE'] . "<br/>";
        echo "Code: ". $error['code'] . "<br/>";
        echo "Message: ". $error['message'] . "<br/>";
    }
}
?>
```

### <a name="pdo_sqlsrv-example"></a>PDO_SQLSRV の例

```php
<?php
try {
    $serverName = "yourServername";
    $databaseName = "yourDatabase";
    $uid = "yourUsername";
    $pwd = "yourPassword";
    
    $conn = new PDO("sqlsrv:server = $serverName; Database = $databaseName;", $uid, $pwd);

    // Select Query
    $tsql = "SELECT @@Version AS SQL_VERSION";

    // Executes the query
    $stmt = $conn->query($tsql);
} catch (PDOException $exception1) {
    echo "<h1>Caught PDO exception:</h1>";
    echo $exception1->getMessage() . PHP_EOL;
    echo "<h1>PHP Info for troubleshooting</h1>";
    phpinfo();
}

?>

<h1> Success Results : </h1>

<?php
try {
    while ($row = $stmt->fetch(PDO::FETCH_ASSOC)) {
        echo $row['SQL_VERSION'] . PHP_EOL;
    }
} catch (PDOException $exception2) {
    // Display errors
    echo "<h1>Caught PDO exception:</h1>";
    echo $exception2->getMessage() . PHP_EOL;
}

unset($stmt);
unset($conn);
?>
```

ブラウザーで `https://localhost/testsql.php` (macOS では `https://localhost:8080/testsql.php` ) をポイントします。 SQL Server または Azure SQL データベースに接続できるようになったはずです。 SQL のバージョン情報を示す成功メッセージが表示されない場合は、コマンド ラインからスクリプトを実行することで、いくつかの基本的なトラブルシューティングを行うことができます。

```bash
php testsql.php
```

コマンド ラインからの実行は成功するがブラウザーに何も表示されない場合は、[Apache ログ ファイル](https://linuxize.com/post/apache-log-files/#location-of-the-log-files)を確認します。 その他のヘルプについては、[サポート リソース](support-resources-for-the-php-sql-driver.md)に関するページを参照してください。

## <a name="see-also"></a>参照

[Microsoft Drivers for PHP for SQL Server の概要](../../connect/php/getting-started-with-the-php-sql-driver.md)

[Microsoft SQL Server 用 Drivers for PHP を読み込む](../../connect/php/loading-the-php-sql-driver.md)

[Microsoft SQL Server 用 Drivers for PHP のシステム要件](../../connect/php/system-requirements-for-the-php-sql-driver.md)
