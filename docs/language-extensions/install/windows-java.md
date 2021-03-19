---
title: Windows 上に Java 言語拡張を インストールする
titleSuffix: SQL Server Language Extensions
description: SQL Server の Java 言語拡張機能を Windows にインストールする方法について説明します。
author: dphansen
ms.author: davidph
ms.date: 11/11/2020
ms.topic: how-to
ms.prod: sql
ms.technology: language-extensions
monikerRange: '>=sql-server-ver15'
ms.openlocfilehash: a3f7e3efb0037aa9bb4e18467142eb03fa9658f2
ms.sourcegitcommit: bf7577b3448b7cb0e336808f1112c44fa18c6f33
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104611142"
---
# <a name="install-sql-server-java-language-extension-on-windows"></a>Windows 上に SQL Server の Java 言語拡張を インストールする

[!INCLUDE [SQL Server 2019 and later](../../includes/applies-to-version/sqlserver2019.md)]

SQL Server の [Java 言語拡張](../java-overview.md)コンポーネントを Windows にインストールする方法について説明します。 Java 言語拡張は、[SQL Server 言語拡張](../language-extensions-overview.md)の一部です。

> [!NOTE]
> この記事は、Windows への SQL Server の Java 言語拡張のインストールに関するものです。 Linux については、「[Linux 上に SQL Server の Java 言語拡張をインストールする](../../linux/sql-server-linux-setup-language-extensions-java.md)」をご覧ください

<a name="prerequisites"></a>

## <a name="pre-install-checklist"></a>インストール前のチェックリスト

+ Java 言語拡張のサポートをインストールする場合は、SQL Server 2019 セットアップが必要です。

+ データベース エンジンのインスタンスが必要です。 Java 言語拡張だけをインストールすることはできませんが、既存のインスタンスにそれらを段階的に追加することはできます。

+ ビジネス継続性のために、言語拡張では [Always On 可用性グループ](../../database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server.md)がサポートされています。 各ノードに言語拡張をインストールし、パッケージを構成する必要があります。

+ Java 言語拡張のインストールは、SQL Server 2019 のフェールオーバー クラスターでサポートされています。

+ ドメイン コントローラーには、SQL Server 言語拡張も Java 言語拡張もインストールしないでください。 セットアップの言語拡張の部分が失敗します。

+ 言語拡張と [Machine Learning Services](../../machine-learning/index.yml) は、SQL Server ビッグ データ クラスターには既定でインストールされます。 ビッグ データ クラスターを使用する場合、この記事の手順を行う必要はありません。 詳細については、[ビッグ データ クラスターでの Machine Learning Services (Python および R) の使用](../../big-data-cluster/machine-learning-services.md)に関するページを参照してください。

> [!IMPORTANT]
> セットアップが完了したら、この記事で説明されている構成後の手順を必ず完了してください。 これらの手順には、SQL Server で外部コードを使用できるようにすることや、ユーザーに代わって SQL Server が Java コードを実行するために必要なアカウントを追加することが含まれます。 通常、構成を変更するには、インスタンスを再起動するか、Launchpad サービスを再起動する必要があります。

<a name="java-jre-jdk"></a>

## <a name="java-jre-or-jdk"></a>Java JRE または JDK

SQL Server と共に Java をインストールして使用する方法は 2 つあります。

1. 既定の Java ランタイムである Zulu Open JRE バージョン 11.0.3 を使用します。 このランタイムは、SQL Server のインストールでサポートされており、インストールに含まれます。

1. 既定の Java ランタイムではなく、好みの Java ディストリビューションを使用します。

    現在、Java 11 は Windows でサポートされているバージョンです。 最小要件は Java Runtime Environment (JRE) ですが、Java Development Kit (JDK) があると Java コンパイラと開発パッケージが必要な場合に便利です。 JDK にはすべてが含まれているため、JDK をインストールすれば、JRE は必要ありません。 Windows では、可能であれば、既定の `/Program Files/` フォルダーに JDK をインストールすることをお勧めします。 そうしないと、実行可能ファイルにアクセス許可を付与するために余分な構成が必要になります。 詳しくは、このドキュメントの[アクセス許可の付与 (Windows)](#perms-nonwindows) に関するセクションをご覧ください。

    > [!NOTE]
    > Java に下位互換性がある場合は、以前のバージョンでも動作する可能性がありますが、SQL Server 2019 でサポートおよびテストされているバージョンは Java 11 です。

## <a name="get-the-installation-media"></a>インストール メディアを入手する

[!INCLUDE[GetInstallationMedia](../../includes/getssmedia.md)]

## <a name="run-setup"></a>セットアップの実行

ローカル インストールの場合は、セットアップを管理者として実行する必要があります。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] をリモート共有からインストールする場合は、そのリモート共有に対する読み取り権限と実行権限を持つドメイン アカウントを使用する必要があります。

1. SQL Server 2019 のセットアップ ウィザードを開始します。
  
1. **[インストール]** タブで、 **[SQL Server の新規スタンドアロン インストールを実行するか、既存のインストールに機能を追加します]** を選択します。

    ![SQL Server 2019 のインストール](../media/sql-install.png) 

1. **[機能の選択]** ページで、次のオプションを選択します。
  
    - **データベース エンジン サービス**
  
        SQL Server で言語拡張を使用するには、データベース エンジンのインスタンスをインストールする必要があります。 既定のインスタンスまたは名前付きインスタンスを使用できます。
  
    - **Machine Learning Services および言語の拡張**
  
        このオプションを選択すると、Java コードの実行をサポートする言語拡張コンポーネントがインストールされます。

        - 既定の Java ランタイムである Zulu Open JRE 11.0.3 をインストールする場合は、 **[Machine Learning Services および言語の拡張]** と **[Java]** を選択します。

        - 独自の Java ランタイムを使用する場合は、 **[Machine Learning Services および言語の拡張]** を選択します。 **[Java]** は選択しないでください。

        R および Python を使用する場合は、[Windows への SQL Server Machine Learning Services のインストール](../../machine-learning/install/sql-machine-learning-services-windows-install.md)に関する記事をご覧ください。

    ![言語拡張の機能オプション](../media/sql-install-feature-selection.png)

1. 前の手順で既定の Java ランタイムをインストールする **[Java]** を選択した場合は、 **[Java のインストール場所]** ページが表示されます。

    **[Install Open JRE 11.0.3 included with this installation]\(このインストールに含まれている Open JRE 11.0.3 をインストールします\)** を選択します。

    ![Java をインストールする場所を選択する](../media/sql-install-openjdk.png)

    > [!NOTE]
    > 言語拡張では、 **[このコンピューターにインストールされている別のバージョンの場所を指定する]** は使用しません。

1. **[インストールの準備完了]** ページで、以下が選択されていることを確認した後、 **[インストール]** を選択します。
  
    + データベース エンジン サービス
    + Machine Learning Services および言語の拡張

    構成ファイルが格納されている `..\Setup Bootstrap\Log` パスの下にあるフォルダーの場所をメモしておきます。 セットアップが完了したら、インストールされたコンポーネントを概要ファイルで確認できます。

6. セットアップが完了し、コンピューターの再起動を求めるメッセージが表示されたら、再起動してください。 セットアップが完了した時点で、インストール ウィザードによるメッセージを確認することが重要です。 詳細については、「 [SQL Server セットアップ ログ ファイルの表示と読み取り](../../database-engine/install-windows/view-and-read-sql-server-setup-log-files.md)」を参照してください。

## <a name="add-the-jre_home-variable"></a>JRE_HOME 変数を追加する

`JRE_HOME` は、Java インタープリターの場所を指定するシステム環境変数です。 この手順では、Windows 上でそれに対するシステム環境変数を作成します。

1. JRE ホーム パスを調べてコピーします。

    たとえば、既定の Java ランタイム Zulu JRE 11.0.3 の JRE ホーム パスは、`C:\Program Files\Microsoft SQL Server\MSSQL15.MSSQLSERVER\MSSQL\Binn\AZUL-OpenJDK-JRE\` です。

    SQL Server のインストール パスによっては、または別の Java ランタイムを選択した場合は、JDK または JRE の場所が上記のパスの例と異なる場合があります。 JDK をインストールした場合でも、そのインストールの一部として JRE サブ フォルダーを取得することがよくあるため、その場合はその JRE フォルダーを示します。 Java 拡張機能により、パス `%JRE_HOME%\bin\server` からの `jvm.dll` の読み込みが試みられます。

1. コントロール パネルで **[システムとセキュリティ]** を開き、 **[システム]** を開いて、 **[システムの詳細設定]** を選択します。

1. **[環境変数]** を選択します。

1. 値を (手順 1 で確認した) JDK/JRE のパスにして、`JRE_HOME` に対する新しいシステム変数を作成します。

1. [Launchpad](../concepts/extensibility-framework.md#launchpad) を開始し直します。

    1. [[SQL Server 構成マネージャー](../../relational-databases/sql-server-configuration-manager.md)] を開きます。

    1. [SQL Server サービス] で [SQL Server スタート パッド] を右クリックして、 **[再起動]** を選択します。

<a name="perms-nonwindows"></a>

## <a name="grant-access-to-non-default-jre-folder"></a>既定以外の JRE フォルダーへのアクセスを許可する

SQL Server に付属する既定の Zulu Open JRE も、プログラム ファイルの下の JDK または JRE もインストールしなかった場合は、次の手順を実行する必要があります。 "*elevated*" の行から **icacls** コマンドを実行して、**SQLRUsergroup** と SQL Server サービス アカウント (**ALL_APPLICATION_PACKAGES** 内) に、JRE にアクセスするためのアクセス権を許可します。 そのコマンドでは、指定したディレクトリ パスの下にあるすべてのファイルとフォルダーへのアクセス権が再帰的に許可されます。

1. SQLRUserGroup にアクセス許可を付与する

    名前付きインスタンスの場合は、SQLRUsergroup にインスタンス名を追加します (例: `SQLRUsergroupINSTANCENAME`)。

    ```cmd
    icacls "<PATH to JRE>" /grant "SQLRUsergroup":(OI)(CI)RX /T
    ```
    
    Windows の [プログラム ファイル] の下にある既定のフォルダーに JDK/JRE をインストールした場合は、この手順を省略できます。

1. AppContainer のアクセス許可を付与します

    ```cmd
    icacls "<PATH to JRE>" /grant *S-1-15-2-1:(OI)(CI)RX /T
    ```

    > [!NOTE]
    > 上記のコマンドを実行すると、コンピューター SID **S-1-15-2-1** にアクセス許可が付与されます。これは、英語版の Windows における **ALL APPLICATION PACKAGES** と同等です。 または、英語版の Windows で `icacls "<PATH to JRE>" /grant "ALL APPLICATION PACKAGES":(OI)(CI)RX /T` を使用することもできます。

## <a name="restart-the-service"></a>サービスを再起動します。

インストールが完了したら、スクリプトの実行を有効にする次の手順に進む前に、データベース エンジンを再起動します。

サービスを再起動すると、関連する SQL Server Launchpad サービスも自動的に再起動されます。

サービスの再起動は、SSMS でインスタンスの **[再起動]** コマンドを右クリックするか、コントロール パネルの **[サービス]** パネルまたは [SQL Server 構成マネージャー](../../relational-databases/sql-server-configuration-manager.md)を使って行うことができます。

## <a name="enable-script-execution"></a>スクリプトの実行を有効にする

1. [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)]を開きます。 

1. 言語拡張をインストールしたインスタンスに接続し、**[新しいクエリ]** をクリックしてクエリ ウィンドウを開き、次のコマンドを実行します。

    ```sql
    sp_configure
    ```

    プロパティ `external scripts enabled` の値は、この時点では **0** であることが必要です。 機能は既定でオフになっており、管理者は Java コードを実行する前に明示的に有効にする必要があります。

1. 外部スクリプト機能を有効にするには、次のステートメントを実行します。

    ```sql
    EXEC sp_configure 'external scripts enabled', 1
    RECONFIGURE WITH OVERRIDE
    ```

    Machine Learning Services に対してこの機能を既に有効にしている場合は、言語拡張に対して再構成を再度実行しないでください。 基になる拡張機能プラットフォームでは、両方がサポートされています。

<a name="register_external_language"></a>

## <a name="register-external-language"></a>外部言語を登録する

言語拡張を利用する各データベースには、[CREATE EXTERNAL LANGUAGE](../../t-sql/statements/create-external-language-transact-sql.md) を使って外部言語を登録する必要があります。

次の例では、Windows 上の SQL Server のデータベースに、Java という名前の外部言語を追加します。

```SQL
CREATE EXTERNAL LANGUAGE Java
FROM (CONTENT = N'<path-to-zip>', FILE_NAME = 'javaextension.dll');
GO
```

詳しくは、「[CREATE EXTERNAL LANGUAGE](../../t-sql/statements/create-external-language-transact-sql.md)」をご覧ください。

## <a name="verify-installation"></a>インストールの確認

セットアップ ログで、インスタンスのインストール状態を確認します。

次の手順に従って、外部スクリプトの起動に使用されるすべてのコンポーネントが実行されていることを確認します。

1. SQL Server Management Studio または Azure Data Studio で、新しいクエリ ウィンドウを開き、次のステートメントを実行します。

    ```sql
    EXEC sp_configure 'external scripts enabled'
    ```

    **run_value** が 1 に設定されています。

1. **[サービス]** パネルまたは SQL Server 構成マネージャーを開き、**SQL Server Launchpad サービス** が実行されていることを確認します。 言語拡張がインストールされているすべてのデータベース エンジンのインスタンスに対して、1 つのサービスが存在する必要があります。 サービスの詳細については、[機能拡張フレームワーク](../concepts/extensibility-framework.md)に関するページを参照してください。

## <a name="additional-configuration"></a>追加構成

検証手順が成功した場合は、SQL Server Management Studio、Azure Data Studio、Visual Studio Code、または T-SQL ステートメントをサーバーに送信できる他の任意のクライアントから、Java コードを実行できます。

コマンドの実行中にエラーが発生した場合は、このセクションの追加の構成手順を確認してください。 場合によっては、サービスまたはデータベースに対して追加の適切な構成を行う必要があります。

インスタンス レベルでは、追加の構成には次のものが含まれます。

+ [SQL Server Machine Learning Services のファイアウォール構成](../../machine-learning/security/firewall-configuration.md)
+ [追加のネットワーク プロトコルの有効化](../../database-engine/configure-windows/enable-or-disable-a-server-network-protocol.md)
+ [リモート接続の有効化](../../database-engine/configure-windows/configure-the-remote-access-server-configuration-option.md)
+ [SQLRUserGroup のログインを作成する](../../machine-learning/security/create-a-login-for-sqlrusergroup.md)

<a name="bkmk_configureAccounts"></a>
<a name="permissions-external-script"></a>

データベースでは、次の構成の更新が必要になる場合があります。

+ [SQL Server Machine Learning Services にユーザー アクセス許可を付与する](../../machine-learning/security/user-permission.md)
+ [特定の言語を実行するためのアクセス許可をユーザーに付与する](../../t-sql/statements/create-external-language-transact-sql.md#permissions)

> [!NOTE]
> 追加の構成が必要かどうかは、SQL Server をインストールしたセキュリティ スキーマと、ユーザーをどのようにデータベースに接続して外部スクリプトを実行させるかによって異なります。

## <a name="suggested-optimizations"></a>推奨される最適化

すべてが機能するようになったので、Java 言語拡張をサポートするようにサーバーを最適化することもできます。

### <a name="optimize-the-server-for-java-language-extension"></a>Java 言語拡張用にサーバーを最適化する

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のセットアップの既定の設定は、データベース エンジンによってサポートされているさまざまなサービスに対してサーバーのバランスを最適化することを目的としています。サービスには、抽出、変換、および読み込み (ETL) プロセス、レポート、監査、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] データを使うアプリケーションなどが含まれます。 そのため、既定の設定では、場合によって言語拡張用のリソースが制限または調整されることがあります (特に、メモリを大量に使用する操作の場合)。

言語拡張ジョブが適切に優先順位を設定されて、リソースを提供されるようにするため、SQL Server Resource Governor を使って外部リソース プールを構成することをお勧めします。 また、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] データベース エンジンに割り当てられるメモリの量を変更したり、[!INCLUDE[rsql_launchpad](../../includes/rsql-launchpad-md.md)] サービスで実行するアカウントの数を増やしたりすることもできます。

+ 外部リソースを管理するためのリソース プールを構成するには、[CREATE EXTERNAL RESOURCE POOL](../../t-sql/statements/create-external-resource-pool-transact-sql.md) に関するページを参照してください。
  
+ データベース用に予約されているメモリの量を変更するには、「[サーバー メモリの構成オプション](../../database-engine/configure-windows/server-memory-server-configuration-options.md)」を参照してください。
  
Standard Edition を使用していて、Resource Governor がない場合は、動的管理ビュー (DMV) と拡張イベントに加え、Windows イベント監視を使用してサーバー リソースを管理できます。

## <a name="next-steps"></a>次のステップ

Java 開発者はいくつかの簡単な例を試して、SQL Server での Java の動作方法の基本を確認できます。 次の手順については、以下のリンクを参照してください。

+ [チュートリアル:Java での正規表現](../tutorials/search-for-string-using-regular-expressions-in-java.md)
