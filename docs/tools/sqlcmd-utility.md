---
title: sqlcmd ユーティリティ
description: sqlcmd ユーティリティを使用すると、Transact-SQL ステートメント、システム プロシージャ、およびスクリプト ファイルを異なるモードを使用して入力し、ODBC を使用して Transact-SQL バッチを実行できます。
ms.prod: sql
ms.prod_service: sql-tools
ms.technology: tools-other
ms.topic: conceptual
helpviewer_keywords:
- statements [SQL Server], command prompt
- QUIT command
- Transact-SQL statements, command prompt
- EXIT command
- sqlcmd commands
- ED command
- sqlcmd utility
- command prompt utilities [SQL Server], sqlcmd
- '!! command'
- stored procedures [SQL Server], command prompt
- system stored procedures [SQL Server], command prompt
- sqlcmd utility, about sqlcmd utility
- scripts [SQL Server], command prompt
- RESET command
- GO command
ms.assetid: e1728707-5215-4c04-8320-e36f161b834a
author: markingmyname
ms.author: maghan
ms.reviewer: ''
ms.custom: seo-lt-2019
ms.date: 02/24/2021
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017'
ms.openlocfilehash: ad1d3e98d45dc0395da92ee331e48b3fbfc161e6
ms.sourcegitcommit: 295b9dfc758471ef7d238a2b0f92f93e34acbb1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106054787"
---
# <a name="sqlcmd-utility"></a>sqlcmd ユーティリティ

[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

> SQL Server 2014 以前については、「[sqlcmd ユーティリティ](/previous-versions/sql/2014/tools/sqlcmd-utility?view=sql-server-2014&preserve-view=true)」を参照してください。
>
> Linux 上で sqlcmd を使用する場合は、[Linux への sqlcmd と bcp のインストール](../linux/sql-server-linux-setup-tools.md)に関する記事を参照してください。

 **sqlcmd** ユーティリティを使用すると、Transact-SQL ステートメントやシステム プロシージャ、スクリプト ファイルを使用可能なさまざまなモードで入力できます。

- コマンド プロンプト。
- **クエリ エディター** での SQLCMD モード。
- Windows スクリプト ファイル。
- SQL Server エージェント ジョブのオペレーティング システム (Cmd.exe) ジョブ ステップ。

このユーティリティでは、ODBC を使用して、Transact-SQL バッチを実行します。

## <a name="download-the-latest-version-of-sqlcmd-utility"></a>最新バージョンの sqlcmd ユーティリティをダウンロードする

**[![x64 用 sqlcmd のダウンロード](../ssdt/media/download.png) Microsoft Command Line Utilities 15 for SQL Server (x64) (2.6 MB) のダウンロード](https://go.microsoft.com/fwlink/?linkid=2142258)**
<br>**[![x86 用 sqlcmd のダウンロード](../ssdt/media/download.png) Microsoft Command Line Utilities 15 for SQL Server (x86) (2.3 MB) のダウンロード](https://go.microsoft.com/fwlink/?linkid=2142257)**

コマンドライン ツールは一般提供 (GA) ですが、[!INCLUDE[sql-server-2019](../includes/sssql19-md.md)] のインストーラー パッケージと共にリリースされています。

**バージョン情報**

リリース番号:15.0.2<br>
ビルド番号:15.0.2000.5<br>
リリース日: 2020 年 9 月 11 日

新しいバージョンの SQLCMD では、SQL Database、Azure Synapse Analytics、Always Encrypted 機能の Multi-Factor Authentication (MFA) のサポートを含め、Azure AD 認証がサポートされています。
新しい BCP では、SQL Database と Azure Synapse Analytics の Multi-Factor Authentication (MFA) のサポートを含め、Azure AD 認証がサポートされています。

**システム要件** Windows 10、Windows 7、Windows 8、Windows 8.1、Windows Server 2008-2019。

このコンポーネントには、[Windows インストーラー 4.5](https://windows-installer.soft32.com/) と [Microsoft ODBC Driver for SQL Server 17](../connect/odbc/download-odbc-driver-for-sql-server.md) の両方が必要です。
 
SQLCMD のバージョンを確認するには、`sqlcmd -?` コマンドを実行し、15.0.2000.5 以降のバージョンが使用されていることを確認します。

> [!NOTE]
> Always Encrypted (`-g`) と Azure Active Directory 認証 (`-G`) を利用するには、バージョン 13.1 以降が必要です。 (お使いのコンピューターには複数のバージョンの sqlcmd.exe がインストールされている可能性があります。 必ず正しいバージョンを使用してください。 バージョンを判断するには、 `sqlcmd -?`を実行します。)

sqlcmd ユーティリティは既定でプレインストールされているため、Azure Cloud Shell から試してみることができます。[![Cloud Shell の起動](https://shell.azure.com/images/launchcloudshell.png "Cloud Shell の起動")](https://shell.azure.com)

SSMS で sqlcmd ステートメントを実行するには、上部のナビゲーションの [クエリ] メニューのドロップダウン リストから SQLCMD モードを選択します。  

> [!IMPORTANT]
> [!INCLUDE[ssManStudioFull_md](../includes/ssmanstudiofull-md.md)] (SSMS) では、**クエリ エディター** の標準モードと SQLCMD モードでの実行に Microsoft [!INCLUDE[dnprdnshort_md](../includes/dnprdnshort-md.md)] SqlClient を使用します。 コマンド ラインから **sqlcmd** を実行する場合、**sqlcmd** では ODBC ドライバーが使用されます。 同じクエリでも、 [!INCLUDE[ssManStudioFull_md](../includes/ssmanstudiofull-md.md)] の SQLCMD モードで実行する場合と **sqlcmd** ユーティリティで実行する場合とでは、適用される既定のオプションが異なるので、動作も異なる可能性があります。  
>

現在、**sqlcmd** ではコマンドライン オプションと値の間に空白を入れる必要はありません。 ただし、将来のリリースでは、コマンドライン オプションと値の間に空白が必要になる可能性があります。  

 その他のトピック: 

- [sqlcmd ユーティリティの起動](../ssms/scripting/sqlcmd-start-the-utility.md)
- [sqlcmd ユーティリティの使用](../ssms/scripting/sqlcmd-use-the-utility.md)
  
## <a name="syntax"></a>構文

```cmd
sqlcmd
   -a packet_size
   -A (dedicated administrator connection)
   -b (terminate batch job if there is an error)
   -c batch_terminator
   -C (trust the server certificate)
   -d db_name
   -D
   -e (echo input)
   -E (use trusted connection)
   -f codepage | i:codepage[,o:codepage] | o:codepage[,i:codepage]
   -g (enable column encryption)
   -G (use Azure Active Directory for authentication)
   -h rows_per_header
   -H workstation_name
   -i input_file
   -I (enable quoted identifiers)
   -j (Print raw error messages)
   -k[1 | 2] (remove or replace control characters)  
   -K application_intent  
   -l login_timeout  
   -L[c] (list servers, optional clean output)  
   -m error_level  
   -M multisubnet_failover  
   -N (encrypt connection)  
   -o output_file  
   -p[1] (print statistics, optional colon format)  
   -P password  
   -q "cmdline query"  
   -Q "cmdline query" (and exit)  
   -r[0 | 1] (msgs to stderr)  
   -R (use client regional settings)  
   -s col_separator  
   -S [protocol:]server[instance_name][,port]  
   -t query_timeout  
   -u (unicode output file)  
   -U login_id  
   -v var = "value"
   -V error_severity_level
   -w column_width
   -W (remove trailing spaces)
   -x (disable variable substitution)
   -X[1] (disable commands, startup script, environment variables, optional exit)
   -y variable_length_type_display_width
   -Y fixed_length_type_display_width
   -z new_password
   -Z new_password (and exit)
   -? (usage)
```
  
## <a name="command-line-options"></a>コマンド ライン オプション

**ログイン関連のオプション**

**-A**  
専用管理者接続 (DAC) を使用して SQL Server にサインインします。 この種類の接続は、サーバーのトラブルシューティングで使用されます。 この接続は、DAC をサポートしているサーバー コンピューターでのみ機能します。 DAC を使用できない場合は、**sqlcmd** はエラー メッセージを生成して終了します。 DAC の詳細については、「 [データベース管理者用の診断接続](../database-engine/configure-windows/diagnostic-connection-for-database-administrators.md)」を参照してください。 -A オプションは -G オプションではサポートされていません。 -A を使用して SQL Database に接続する場合は、SQL Server 管理者である必要があります。 Azure Active Directory 管理者は DAC を使用できません。

**-C**  
クライアントでこのスイッチを使用して、サーバーの証明書を検証せずに暗黙的に信頼するようにクライアントを構成できます。 このオプションは、ADO.NET オプションの `TRUSTSERVERCERTIFICATE = true`と同等です。  

**-d** _db_name_  
**sqlcmd** の開始時に `USE` *db_name* ステートメントを実行します。 このオプションにより、 **sqlcmd** スクリプト変数 SQLCMDDBNAME が設定されます。 このパラメーターにより初期データベースが指定されます。 既定値は、ログインの既定データベースのプロパティです。 データベースが存在しない場合は、エラー メッセージが生成され、 **sqlcmd** は終了します。  

**-D**  
-S に指定されたサーバー名を、ホスト名としてではなく、DSN として解釈します。 詳細については、「[sqlcmd による接続](../connect/odbc/linux-mac/connecting-with-sqlcmd.md)」の「_sqlcmd および bcp の DSN サポート_」を参照してください。

> [!NOTE]
> -D オプションは、Linux および MacOS クライアントでのみ使用できます。 これは、Windows クライアントでは以前、廃止済みオプションとされていましたが、これまでに削除され、現在は無視されています。

**-l** _login_timeout_  
サーバーに接続を試みたときに、 **sqlcmd** が ODBC ドライバーにログインするまでのタイムアウトを秒数で指定します。 このオプションにより、 **sqlcmd** スクリプト変数 SQLCMDLOGINTIMEOUT が設定されます。 **sqlcmd** でのログインに関する既定のタイムアウトは 8 秒です。 **-G** オプションを使用して、SQL Database または Azure Synapse Analytics に接続し、Azure Active Directory で認証する場合は、タイムアウト値を 30 秒以上にすることをお勧めします。 ログイン タイムアウトは、0 ～ 65,534 の数値にする必要があります。 指定した値が数値以外の場合、または範囲外の場合、 **sqlcmd** はエラー メッセージを生成します。 この値に 0 を指定すると、タイムアウトは無制限になります。

**-E**  
ユーザー名とパスワードを使用せずにセキュリティ接続を使用して SQL Server にサインインします。 既定では、 **-E** を指定しないと、 **sqlcmd** ではセキュリティ接続オプションが使用されます。  

**-E** オプションを使用すると、SQLCMDPASSWORD などのユーザー名とパスワード用に使用できる環境変数の設定が無視されます。 **-E** オプションが **-U** オプションまたは **-P** オプションと共に使用されると、エラー メッセージが生成されます。  

**-g**  
列の暗号化設定を `Enabled`に設定します。 詳細については、「 [Always Encrypted](../relational-databases/security/encryption/always-encrypted-database-engine.md)」を参照してください。 Windows 証明書ストアに格納されているマスター キーのみがサポートされます。 -g スイッチには、 **sqlcmd** バージョン [13.1](https://go.microsoft.com/fwlink/?LinkID=825643)以上が必要です。 バージョンを判断するには、 `sqlcmd -?`を実行します。

**-G**  
このスイッチは、SQL Database または Azure Synapse Analytics に接続し、Azure Active Directory 認証を使用してユーザーを認証するように指定する場合に、クライアントによって使用されます。 このオプションにより、 **sqlcmd** スクリプト変数 SQLCMDUSEAAD = true が設定されます。 -G スイッチには、 **sqlcmd** バージョン [13.1](https://go.microsoft.com/fwlink/?LinkID=825643)以上が必要です。 バージョンを判断するには、 `sqlcmd -?`を実行します。 詳細については、[Azure Active Directory 認証を使用した SQL Database または Azure Synapse Analytics への接続](/azure/azure-sql/database/authentication-aad-overview)に関する記事を参照してください。 -A オプションは -G オプションではサポートされていません。

> [!IMPORTANT]
> `-G` オプションは、Azure SQL Database と Azure Synapse Analytics にのみ適用されます。
> 現在、AAD 対話型認証は、Linux または macOS 上でサポートされていません。 AAD 統合認証には、[Microsoft ODBC Driver 17 for SQL Server](../connect/odbc/download-odbc-driver-for-sql-server.md) バージョン 17.6.1 以降と、適切に[構成された Kerberos 環境](../connect/odbc/linux-mac/using-integrated-authentication.md)が必要です。


- **Azure Active Directory のユーザー名とパスワード:** 

    Azure Active Directory のユーザー名とパスワードを使用には、 **-G** オプションを指定します。ユーザー名とパスワードは、 **-U** オプションと **-P** オプションを指定する方法でも使用できます。

    ```cmd
    Sqlcmd -S testsrv.database.windows.net -d Target_DB_or_DW -U bob@contoso.com -P MyAADPassword -G
    ```

    -G パラメーターにより、バックエンドで次の接続文字列が生成されます。 

    ```cmd
     SERVER = Target_DB_or_DW.testsrv.database.windows.net;UID= bob@contoso.com;PWD=MyAADPassword;AUTHENTICATION = ActiveDirectoryPassword 
    ```

- **Azure Active Directory 統合**

   Azure Active Directory 統合認証の場合、ユーザー名とパスワードなしで **-G** オプションを指定します。
   *AAD 統合認証には、[Microsoft ODBC Driver 17 for SQL Server](../connect/odbc/download-odbc-driver-for-sql-server.md) バージョン 17.6.1 以降と、適切に [構成された Kerberos 環境](../connect/odbc/linux-mac/using-integrated-authentication.md)が必要です。*

    ```cmd
    Sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -G
    ```

    これにより、バックエンドで次の接続文字列が生成されます。 

    ```cmd
    SERVER = Target_DB_or_DW.testsrv.database.windows.net Authentication = ActiveDirectoryIntegrated; Trusted_Connection=NO
    ```

    > [!NOTE]
    > `-E` オプション (Trusted_Connection) と `-G` オプションを共に使用することはできません。

- **Azure Active Directory 対話型**

    Azure SQL Database と Azure Synapse Analytics の Azure AD 対話型認証では、多要素認証をサポートする対話的な方法を使用できます。 詳細については、「[Azure Active Directory 対話型認証](../ssdt/azure-active-directory.md#active-directory-interactive-authentication)」を参照してください。 

   Azure AD 対話型には、**sqlcmd** [バージョン 15.0.1000.34](#download-the-latest-version-of-sqlcmd-utility) 以降と [ODBC バージョン 17.2 以降](../connect/odbc/download-odbc-driver-for-sql-server.md)が必要です。  

   対話型認証を有効にするには、-G オプションにパスワードを指定せず、ユーザー名 (-U) のみを指定します。

   次の例では、Azure AD 対話型モードを使用し、ユーザー名 (ユーザーは AAD アカウントを表します) を指定してデータをエクスポートします。 これは、前のセクション、「*Azure Active Directory のユーザー名とパスワード*」で使用したのと同じ例です。  

   対話モードでは、パスワードを手動で入力する必要があります。また、多要素認証が有効なアカウントの場合は、構成された MFA 認証方法を完了します。

   ```cmd
   sqlcmd -S testsrv.database.windows.net -d Target_DB_or_DW -G -U alice@aadtest.onmicrosoft.com
   ```

   上記のコマンドにより、バックエンドで次の接続文字列が生成されます。  

   ```cmd
   SERVER = Target_DB_or_DW.testsrv.database.windows.net;UID=alice@aadtest.onmicrosoft.com; AUTHENTICATION = ActiveDirectoryInteractive   
   ```

   Azure AD ユーザーが Windows アカウントを使用するドメイン フェデレーション ユーザーの場合、コマンド ラインで必要なユーザー名には (たとえば、以下の joe@contoso.com のように) そのドメイン アカウントが含まれます。

   ```cmd
   sqlcmd -S testsrv.database.windows.net -d Target_DB_or_DW -G -U joe@contoso.com  
   ```

   ゲスト ユーザーが特定の Azure AD に存在し、SQL Database に存在するグループに属し、そのグループが sqlcmd コマンドを実行するデータベース アクセス許可を持つ場合、ゲスト ユーザーの別名が使用されます (たとえば、 *keith0@adventureworks.com* )。

  >[!IMPORTANT]
  >SQLCMD で `-G` および `-U` オプションを使用する場合、既知の問題として、`-G` オプションの前に `-U` オプションを指定すると、認証が失敗する可能性があります。 常に `-G` オプションから始め、`-U` オプションがその後になるようにします。

**-H** _workstation_name_  
 ワークステーション名です。 このオプションにより、 **sqlcmd** スクリプト変数 SQLCMDWORKSTATION が設定されます。 ワークステーション名は **sys.sysprocesses** カタログ ビューの **hostname** 列に一覧表示され、ストアド プロシージャ **sp_who** を使用して取得できます。 このオプションが指定されていない場合の既定値は、現在のコンピューター名になります。 この名前は、異なる 

**sqlcmd** セッションを識別する場合に使用できます。  

**-j** 画面に生のエラー メッセージを出力します。
  
 **-K** _application_intent_  
 アプリケーションがサーバーに接続するときのワークロードのタイプを宣言します。 現在サポートされている値は、 **ReadOnly** だけです。 **-K** を指定しない場合、sqlcmd ユーティリティでは AlwaysOn 可用性グループのセカンダリ レプリカへの接続がサポートされません。 詳細については、「[アクティブなセカンダリ:読み取り可能なセカンダリ レプリカ (AlwaysOn 可用性グループ)](../database-engine/availability-groups/windows/active-secondaries-readable-secondary-replicas-always-on-availability-groups.md)  
  
**-M** _multisubnet_failover_  
 SQL Server 可用性グループまたは SQL Server フェールオーバー クラスター インスタンスの可用性グループ リスナーに接続する際には、必ず **-M** を指定してください。 **-M** を指定すると、(現在) アクティブなサーバーを迅速に検出して接続できます。 **-M** を指定しない場合、 **-M** は無効になります。 詳細については、「[リスナー、クライアント接続、アプリケーションのフェールオーバー](../database-engine/availability-groups/windows/listeners-client-connectivity-application-failover.md)」、「[可用性グループの作成と構成 &#40;SQL Server&#41;](../database-engine/availability-groups/windows/creation-and-configuration-of-availability-groups-sql-server.md)」、「[フェールオーバー クラスタリングと Always On 可用性グループ (SQL Server)](../database-engine/availability-groups/windows/failover-clustering-and-always-on-availability-groups-sql-server.md)」、「[アクティブなセカンダリ:読み取り可能なセカンダリ レプリカ (AlwaysOn 可用性グループ)](../database-engine/availability-groups/windows/active-secondaries-readable-secondary-replicas-always-on-availability-groups.md)。
  
 **-N**  
 クライアントでこのスイッチを使用して、暗号化された接続を要求できます。  
  
 **-P** _password_  
 ユーザーが指定するパスワードです。 パスワードでは大文字と小文字が区別されます。 -U オプションを使用して **-P** オプションを使用せず、SQLCMDPASSWORD 環境変数が設定されていない場合は、 **sqlcmd** はユーザーにパスワードを要求します。 null パスワードは使用しないことをお勧めしますが、パラメーター値に連続する二重引用符のペアを使用して、null パスワードを指定できます。

- **-P ""**

強力なパスワードを使用することをお勧めします。

#### <a name="use-a-strong-password"></a>[**強力なパスワードを使用してください。**](../relational-databases/security/strong-passwords.md)

 パスワード プロンプトは、次のようにパスワード プロンプトをコンソールに出力することによって表示されます。 `Password:`  
  
 ユーザーが行った入力は表示されません。 つまり、入力時には何も表示されず、カーソルが移動しません。  
  
 SQLCMDPASSWORD 環境変数を使用して、現在のセッションに既定のパスワードを設定できます。 したがって、パスワードをバッチ ファイルにハード コードする必要はありません。  
  
 次の例では、まずコマンド プロンプトで SQLCMDPASSWORD 変数を設定してから **sqlcmd** ユーティリティにアクセスします。 コマンド プロンプトに、次のコマンドを入力します。  
  
 `SET SQLCMDPASSWORD= p@a$$w0rd`  
 次のコマンド プロンプトが表示されたら、次のように入力します:  
  
 `sqlcmd`  
  
 ユーザー名とパスワードの組み合わせが正しくない場合は、エラー メッセージが生成されます。  
  
**注:**  OSQLPASSWORD 環境変数は旧バージョンとの互換性が維持されました。 SQLCMDPASSWORD 環境変数は OSQLPASSWORD 環境変数よりも優先されます。 OSQLPASSWORD が共有されなくなったため、**sqlcmd** ユーティリティと **osql** ユーティリティを競合することなく組み合わせて使用できます。 以前のスクリプトは引き続き機能します。  
  
 **-P** オプションが **-E** オプションと共に使用されると、エラー メッセージが生成されます。  
  
 **-P** オプションの後に複数の引数があると、エラー メッセージが生成され、プログラムが終了します。  
  
 **-S** [*protocol*:]*server*[ **\\** _instance\_name_][ **,** _port_]  
 接続先となる SQL Server のインスタンスを指定します。 このオプションにより、 **sqlcmd** スクリプト変数 SQLCMDSERVER が設定されます。  
  
 そのサーバー コンピューター上の SQL Server の既定のインスタンスに接続するには、*server_name* を指定します。 そのサーバー コンピューター上の SQL Server の名前付きインスタンスに接続するには、*server_name* [ **\\** _instance\_name_ ] を指定します。 サーバー コンピューターを指定しない場合、**sqlcmd** は、ローカル コンピューター上にある SQL Server の既定のインスタンスに接続します。 ネットワーク上のリモート コンピューターから **sqlcmd** を実行するときは、このオプションが必要です。  
  
 *protocol* には、 **tcp** (TCP/IP)、 **lpc** (共有メモリ)、または **np** (名前付きパイプ) を指定できます。  
  
 **sqlcmd** を実行するときに *server_name* [ **\\** _instance\_name_ ]を指定しなかった場合は、SQL Server により SQLCMDSERVER 環境変数がチェックされ、その値が使用されます。  
  
> [!NOTE]  
>  OSQLSERVER 環境変数は旧バージョンとの互換性を維持しています。 SQLCMDSERVER 環境変数は OSQLSERVER 環境変数よりも優先されます。これにより、 **sqlcmd** と **osql** を競合することなく組み合わせて使用でき、従来のスクリプトは引き続き機能を実行することができます。  
  
 **-U** _login_id_  
 ログイン名または包含データベースのユーザー名です。 包含データベースのユーザーの場合、データベース名のオプション (-d) を指定する必要があります。  
  
> [!NOTE]  
>  OSQLUSER 環境変数は旧バージョンとの互換性を維持しています。 SQLCMDUSER 環境変数は OSQLUSER 環境変数よりも優先されます。 これにより、 **sqlcmd** と **osql** を競合することなく組み合わせて使用できます。 また、既存の **osql** スクリプトは引き続き実行することができます。  
  
 **-U** オプションも **-P** オプションも指定しないと、**sqlcmd** は Microsoft Windows 認証モードを使用して接続を試みます。 認証は **sqlcmd** を実行しているユーザーの Windows アカウントに基づきます。  
  
 **-U** オプションが **-E** オプション (この記事の後半で説明) と同時に使用されると、エラー メッセージが生成されます。 **-U** オプションの後に複数の引数があると、エラー メッセージが生成され、プログラムが終了します。  
  
 **-z** _new_password_  
 パスワードの変更:  
  
 `sqlcmd -U someuser -P s0mep@ssword -z a_new_p@a$$w0rd`  
  
 **-Z** _new_password_  
 パスワードの変更と終了:  
  
 `sqlcmd -U someuser -P s0mep@ssword -Z a_new_p@a$$w0rd`  
  
 **入力または出力のオプション**  
  **-f** _codepage_ | **i:** _codepage_[ **,o:** _codepage_] | **o:** _codepage_[ **,i:** _codepage_]  
 入力と出力のコード ページを指定します。 コード ページ番号は、インストールされた Windows コード ページを指定する数値です。  
  
 コード ページには次の変換規則があります。  
  
- コード ページを指定しないと、入力ファイルが変換不要の Unicode ファイルでない限り、 **sqlcmd** では、入力ファイルと出力ファイルの両方に現在のコード ページが使用されます。  
  
- **sqlcmd** では、ビッグ エンディアンとリトル エンディアンの両方の Unicode 入力ファイルが自動的に認識されます。 **-u** オプションを指定すると、出力は常にリトル エンディアン Unicode になります。  
  
- 出力ファイルを指定しないと、出力コード ページはコンソールのコード ページになります。 この方法では、コンソールに出力が正しく表示されます。  
  
- 複数の入力ファイルの場合、同じコード ページが指定されているものと見なされます。 Unicode 入力ファイルと Unicode 以外の入力ファイルを混在させることができます。  
  
 Cmd.exe のコード ページを確認するには、コマンド プロンプトに「 **chcp** 」と入力します。  
  
 **-i** _input_file_[ **,** _input\_file2_...]  
 SQL ステートメントまたはストアド プロシージャのバッチを含むファイルを指定します。 複数のファイルを指定すると、それらのファイルは順番に読み取られて処理されます。 ファイル名とファイル名の間には空白を使用しないでください。 **sqlcmd** により、最初に、指定したすべてのファイルが存在しているかどうかがチェックされます。 1 つ以上のファイルが存在していない場合は、 **sqlcmd** は終了します。 -i と -Q/-q オプションは同時に使用できません。  
  
 パスの例:  

```cmd
-i C:\<filename>  
-i \\<Server>\<Share$>\<filename>  
-i "C:\Some Folder\<file name>"  
```
  
 空白を含むファイル パスは、引用符で囲む必要があります。  
  
 このオプションは **-i**_input\_file_ **-I**_I input_file_ のように複数回使用できます。  
  
 **-o** _output_file_  
 **sqlcmd** からの出力を受信するファイルを指定します。  
  
 **-u** が指定されている場合は、 *output_file* は Unicode 形式で格納されます。 ファイル名が無効な場合は、エラー メッセージが生成され、 **sqlcmd** が終了します。 **sqlcmd** は、同じファイルに対する複数の **sqlcmd** プロセスの同時書き込みはサポートしていません。 出力ファイルが破損するか、または不適切なファイルになる可能性があります。 **-f** スイッチもファイル形式に関連します。 このファイルが存在しない場合は作成されます。 以前の **sqlcmd** セッションで同じ名前のファイルが作成されていた場合は、上書きされます。 ここで指定されるファイルは **stdout** ファイルではありません。 **stdout** ファイルが指定されると、このファイルは使用されません。  
  
 パスの例:  

```cmd
-o C:< filename>  
-o \\<Server>\<Share$>\<filename>  
-o "C:\Some Folder\<file name>"  
 ```
 空白を含むファイル パスは、引用符で囲む必要があります。  
  
 **-r**[**0** | **1**]  
 エラー メッセージ出力を画面にリダイレクトします (**stderr**)。 パラメーターを指定しない場合や、 **0** を指定した場合は、重大度レベル 11 以上のエラー メッセージだけがリダイレクトされます。 **1** を指定した場合は、PRINT を含むすべてのエラー メッセージ出力がリダイレクトされます。 -o を使用しても効果はありません。 既定では、メッセージは **stdout** に送られます。  
  
 **-R**  
 **sqlcmd** により、クライアントのロケールに基づいて SQL Server から取得された数値、通貨、日付、および時刻の各列がローカライズされます。 既定では、これらの列はサーバーの地域別設定を使用して表示されます。  
  
 **-u**  
 *input_file* の形式に関係なく、 *output_file* を Unicode 形式で格納するように指定します。  
  
 **クエリ実行オプション**  
  **-e**  
 入力スクリプトを標準出力デバイス (**stdout**) に書き込みます。  
  
 **-I**  
 SET QUOTED_IDENTIFIER 接続オプションを ON に設定します。 既定では、OFF に設定されています。 詳細については、「[SET QUOTED_IDENTIFIER &#40;Transact-SQL&#41;](~/t-sql/statements/set-quoted-identifier-transact-sql.md)」をご覧ください。  
  
 **-q "** _cmdline query_ **"**  
 **sqlcmd** の起動時にクエリを実行しますが、クエリの実行が完了しても **sqlcmd** は終了しません。 セミコロンで区切られた複数のクエリを実行できます。 次の例で示すように、クエリを引用符で囲みます。  
  
 コマンド プロンプトに、次のコマンドを入力します。  
  
 `sqlcmd -d AdventureWorks2012 -q "SELECT FirstName, LastName FROM Person.Person WHERE LastName LIKE 'Whi%';"`  
  
 `sqlcmd -d AdventureWorks2012 -q "SELECT TOP 5 FirstName FROM Person.Person;SELECT TOP 5 LastName FROM Person.Person;"`  
  
> [!IMPORTANT]  
>  クエリでは GO ターミネータを使用しないでください。  
  
 このオプションと共に **-b** を指定すると、 **sqlcmd** はエラーで終了します。 **-b** オプションについては、この記事の後半で説明します。  
  
 **-Q "** _cmdline query_ **"**  
 **sqlcmd** の起動時にクエリを実行し、 **sqlcmd** を即時終了します。 セミコロンで区切られた複数のクエリを実行できます。  
  
 次の例で示すように、クエリを引用符で囲みます。  
  
 コマンド プロンプトに、次のコマンドを入力します。  
  
 `sqlcmd -d AdventureWorks2012 -Q "SELECT FirstName, LastName FROM Person.Person WHERE LastName LIKE 'Whi%';"`  
  
 `sqlcmd -d AdventureWorks2012 -Q "SELECT TOP 5 FirstName FROM Person.Person;SELECT TOP 5 LastName FROM Person.Person;"`  
  
> [!IMPORTANT]  
>  クエリでは GO ターミネータを使用しないでください。  
  
 このオプションと共に **-b** を指定すると、 **sqlcmd** はエラーで終了します。 **-b** オプションについては、この記事の後半で説明します。  
  
 **-t** _query_timeout_  
 コマンド (または SQL ステートメント) の実行待ち時間を秒単位で指定します。このオプションにより、 **sqlcmd** スクリプト変数 SQLCMDSTATTIMEOUT が設定されます。 *time_out* 値を指定しないと、コマンドはタイムアウトしません。*query* *time_out* は、1 から 65,534 の数値にする必要があります。 指定した値が数値以外の場合、または範囲外の場合、 **sqlcmd** はエラー メッセージを生成します。  
  
> [!NOTE]  
>  実際のタイムアウト値は、指定した *time_out* 値より数秒異なる場合があります。  
  
 **-vvar =**  _value_[ **var =** _value_...]  
 **sqlcmd** スクリプトで使用できる **sqlcmd** スクリプト変数を作成します。 値に空白が含まれる場合は、値を引用符で囲みます。 複数の _**var**_= **"** _values_ **"** の値を指定できます。 指定した値にエラーが生じた場合は、 **sqlcmd** は、エラー メッセージを生成してから終了します。  
  
 `sqlcmd -v MyVar1=something MyVar2="some thing"`  
  
 `sqlcmd -v MyVar1=something -v MyVar2="some thing"`  
  
 **-x**  
 **sqlcmd** ではスクリプト変数が無視されます。 このパラメーターは、標準変数と同じ形式の文字列 ($(*variable_name*) など) を含む INSERT ステートメントが、スクリプトに多数含まれている場合に便利です。  
  
 **書式設定のオプション**  
  **-h** _headers_  
 列ヘッダーの間に出力する行数を指定します。 既定では、各クエリの結果に対して、ヘッダーは 1 つだけ表示されます。 このオプションにより、 **sqlcmd** スクリプト変数 SQLCMDHEADERS が設定されます。 ヘッダーを出力しない場合は、 **-1** を指定します。 有効でない値があると、 **sqlcmd** はエラー メッセージを生成してから終了します。  
  
 **-k** [**1** | **2**]  
 タブ、改行文字などのすべての制御文字を出力から削除します。 このパラメーターにより、データが返されたときの列の形式が維持されます。 1 を指定した場合、制御文字は 1 つの空白に置き換えられます。 2 を指定すると、連続する制御文字が 1 つの空白に置き換えられます。 **-k** は **-k1** と同じです。  
  
 **-s** _col_separator_  
 列の区切り文字を指定します。 既定では、空白になっています。 このオプションにより、 **sqlcmd** スクリプト変数 SQLCMDCOLSEP が設定されます。 アンパサンド (&)、セミコロン (;) など、オペレーティング システムで特別な意味を持つ文字を使用する場合は、その文字を引用符 (") で囲みます。 列の区切り文字には、任意の 8 ビットの文字を指定できます。  
  
 **-w** _column_width_  
 出力用の画面幅を指定します。 このオプションにより、 **sqlcmd** スクリプト変数 SQLCMDCOLWIDTH が設定されます。 列幅は 8 よりも大きくかつ 65,536 よりも小さい値にする必要があります。 指定した列幅が範囲外の場合、 **sqlcmd** はエラー メッセージを生成します。 既定の幅は 80 文字です。 指定した列幅を超えると、出力行は次の列に折り返されます。  
  
 **-W**  
 このオプションは、列から後続の空白を削除します。 他のアプリケーションにエクスポートするデータを準備するときは、 **-s** オプションと同時にこのオプションを使用します。 **-y** または **-Y** オプションと共には使用できません。  
  
 **-y** _variable_length_type_display_width_  
 **sqlcmd** スクリプト変数 `SQLCMDMAXVARTYPEWIDTH`を設定します。 既定値は 256 です。 長い可変長のデータ型に返される文字数を制限します。  
  
- **varchar(max)**  
  
- **nvarchar(max)**  
  
- **varbinary(max)**  
  
- **xml**  
  
- **UDT (ユーザー定義のデータ型)**  
  
- **text**  
  
- **ntext**  
  
- **image**  
  
> [!NOTE]
> UDT は実装によって固定長にもなります。 固定長の UDT の長さが *display_width* よりも短い場合は、返される UDT の値は影響を受けません。 ただし、 *display_width* よりも長い場合は、出力は切り捨てられます。  
  
> [!IMPORTANT]
>  返されるデータのサイズによって、サーバーとネットワークの両方に重大なパフォーマンスの問題が発生する可能性があるため、 **-y 0** オプションは十分注意して使用してください。  
  
 **-Y** _fixed_length_type_display_width_  
 **sqlcmd** スクリプト変数 `SQLCMDMAXFIXEDTYPEWIDTH`を設定します。 既定値は 0 (無制限) です。 次のデータ型に返される文字数を制限します。  
  
- **char(** _n_ **)** 、ただし 1<=n<=8000  
  
- **nchar(n** _n_ **)** 、ただし 1<=n<=4000  
  
- **varchar(n** _n_ **)** 、ただし 1<=n<=8000  
  
- **nvarchar(n** _n_ **)** 、ただし 1<=n<=4000  
  
- **varbinary(n** _n_ **)** 、ただし 1<=n\<=4000  
  
- **variant**  
  
 **エラー報告のオプション**  
  **-b**  
 エラーが発生したときに、 **sqlcmd** を終了し、DOS ERRORLEVEL 値を返します。 SQL Server のエラー メッセージの重大度が 10 よりも高い場合、DOS ERRORLEVEL 変数に返される値は **1** です。それ以外の場合は、**0** が返されます。 **-V** オプションが **-b** と共に設定されている場合、 **-V** . を使用して設定した値よりも重大度が低いときは、 **sqlcmd** はエラーを報告しません。 コマンド プロンプト バッチ ファイルにより ERRORLEVEL の値をテストすることができ、エラーを適切に処理できます。 **sqlcmd** は重大度レベル 10 (情報メッセージ) に対してはエラーを報告しません。  
  
 **sqlcmd** スクリプトに正しくないコメントや構文エラーが含まれている場合やスクリプト変数が不足している場合、返される ERRORLEVEL は 1 です。  
  
 **-m** _error_level_  
 **stdout** に送信されるエラー メッセージを制御します。 重大度レベルがこのレベル以上のメッセージが送信されます。 この値を **-1** に設定すると、情報メッセージを含めすべてのメッセージが送信されます。 **-m** と **-1** の間にスペースは使用できません。 たとえば、 **-m-1** は有効で、 **-m-1** は有効でありません。  
  
 このオプションにより、 **sqlcmd** スクリプト変数 SQLCMDERRORLEVEL も設定されます。 この変数の既定値は 0 です。  
  
 **-V** _error_severity_level_  
 ERRORLEVEL 変数を設定するために使用される重大度レベルを制御します。 重大度レベルがこの値以上のエラー メッセージによって、ERRORLEVEL が設定されます。 0 より小さい値は 0 と報告されます。 バッチ ファイルおよび CMD ファイルを使用して、ERRORLEVEL 変数の値をテストできます。  
  
 **その他のオプション**  
  **-a** _packet_size_  
 異なるサイズのパケットを要求します。 このオプションにより、 **sqlcmd** スクリプト変数 SQLCMDPACKETSIZE が設定されます。 *packet_size* には、512 ～ 32767 の値を指定する必要があります。 既定値は 4096 です。 大きなパケット サイズを指定すると、GO コマンド間に多数の SQL ステートメントが含まれているスクリプトの実行パフォーマンスが向上します。 既定値よりも大きいパケット サイズを要求できます。 ただし、要求が拒否された場合、 **sqlcmd** はサーバーの既定のパケット サイズを使用します。  
  
 **-c** _batch_terminator_  
 バッチ ターミネータを指定します。 既定では、"GO" だけが入力されている行があると、コマンドが終了したと見なされ、SQL Server に送られます。 バッチ ターミネータをリセットする場合、Transact-SQL の予約キーワードやオペレーティング システムで特別な意味を持つ文字は、先頭に円記号が付いているかどうかに関係なく、使用しないでください。  
  
 **-L** **[c]**  
 ローカルに構成されたサーバー コンピューターと、ネットワーク上でブロードキャストしているサーバー コンピューター名の一覧を表示します。 このパラメーターは、他のパラメーターと組み合わせて使用することはできません。 一覧表示できるサーバー コンピューターの最大数は 3,000 です。 バッファーのサイズが原因でサーバーの一覧が切り捨てられる場合は、警告メッセージが表示されます。  
  
> [!NOTE]  
> ネットワーク上のブロードキャストの特性によっては、 **sqlcmd** は、一部のサーバーからタイムリーな応答を受信できない場合があります。 そのため、返されるサーバーのリストは、このオプションの実行ごとに異なる可能性があります。  
  
 省略可能なパラメーター **c** を指定すると、出力結果には **Servers:** ヘッダー行が含まれません。このため、各サーバー行は、先頭に空白がない状態で一覧表示されます。 この表示はクリーン アウトプットと呼ばれます。 クリーン アウトプットを使用すると、スクリプト言語の処理パフォーマンスが向上します。  
  
 **-p** **[1]**  
 すべての結果セットのパフォーマンス統計を出力します。 パフォーマンス統計の形式の例を次に示します。  
  
 `Network packet size (bytes): n`  
  
 `x xact[s]:`  
  
 `Clock Time (ms.): total       t1  avg       t2 (t3 xacts per sec.)`  
  
 各値の説明:  
  
 `x` = SQL Server によって処理されるトランザクション数。  
  
 `t1` = すべてのトランザクションにかかる合計時間、  
  
 `t2` = 単一のトランザクションにかかる平均時間。  
  
 `t3` = 1 秒あたりの平均トランザクション数。  
  
 すべての時間はミリ秒単位です。  
  
 省略可能なパラメーター **1** を指定した場合は、統計の出力形式は、スプレッドシートへ容易にインポートできる、またはスクリプトによって処理できる、コロンで区切られた形式となります。  
  
 省略可能なパラメーターが **1** 以外の値の場合は、エラーが生成され、 **sqlcmd** は終了します。  
  
 **-X** **[1]**  
 **sqlcmd** がバッチ ファイルから実行される場合に、システムのセキュリティを損なう可能性のあるコマンドを無効にします。 無効なコマンドも認識されます。 **sqlcmd** は警告メッセージを表示して継続します。 省略可能なパラメーターを **1** に指定すると、 **sqlcmd** はエラー メッセージを生成して終了します。 **-X** オプションを使用した場合に無効になるコマンドは次のとおりです。  
  
- **ED**  
  
- **!!** _command_  
  
 **-X** オプションを指定すると、環境変数が **sqlcmd** に渡されなくなります。 また、SQLCMDINI スクリプト変数を使用して指定した、スタートアップ スクリプトも実行できなくなります。 **sqlcmd** スクリプト変数の詳細については、「 [sqlcmd でのスクリプト変数の使用](~/relational-databases/scripting/sqlcmd-use-with-scripting-variables.md)」を参照してください。  
  
 **-?**  
 **sqlcmd** のバージョンと **sqlcmd** オプションの構文の概要を表示します。  
  
## <a name="remarks"></a>解説

オプションは、構文の例に示されている順序に従って使用する必要はありません。

複数の結果が返される場合は、 **sqlcmd** は同じバッチの各結果セットの間に空白行を 1 行ずつ出力します。 また、 `<x> rows affected` というメッセージは、そのメッセージが実行したステートメントに該当する場合にだけ表示されます。

**sqlcmd** を対話的に使用するには、コマンド プロンプトで、**sqlcmd** を、この記事で前に説明した各オプションと共に入力します。 詳細については、「 [sqlcmd ユーティリティの使用](~/relational-databases/scripting/sqlcmd-use-the-utility.md)」を参照してください。

> [!NOTE]  
>  **-L**、 **-Q**、 **-Z** 、または **-i** のオプションを使用すると、 **sqlcmd** は実行後に終了します。
  
 コマンド環境 (Cmd.exe) での **sqlcmd** コマンドライン全体の長さは、すべての引数と拡張変数を含めて、オペレーティング システムが Cmd.exe のために決めます。
  
## <a name="variable-precedence-low-to-high"></a>変数の優先順位 (低から高)
  
1.  システム レベル環境変数
  
2.  ユーザー レベル環境変数  
  
3.  **sqlcmd** の実行前にコマンド プロンプトで行ったコマンド シェル ( **SET** X=Y) の設定  
  
4.  **sqlcmd-v** X=Y  
  
5.  **:Setvar** X Y  
  
> [!NOTE]  
>  環境変数を表示するには、 **[コントロール パネル]** の **[システム]** アイコンを開き、 **[詳細設定]** タブをクリックします。  
  
## <a name="sqlcmd-scripting-variables"></a>sqlcmd スクリプト変数  
  
|変数|関連スイッチ|R/W|Default|  
|--------------|--------------------|----------|-------------|  
|SQLCMDUSER|-U|R|""|  
|SQLCMDPASSWORD|-P|--|""|  
|SQLCMDSERVER|-S|R|"DefaultLocalInstance"|  
|SQLCMDWORKSTATION|-H|R|"ComputerName"|  
|SQLCMDDBNAME|-d|R|""|  
|SQLCMDLOGINTIMEOUT|-l|R/W|"8" (秒)|  
|SQLCMDSTATTIMEOUT|-t|R/W|"0" = 無制限に待機|  
|SQLCMDHEADERS|-H|R/W|"0"|  
|SQLCMDCOLSEP|-S|R/W|" "|  
|SQLCMDCOLWIDTH|-w|R/W|"0"|  
|SQLCMDPACKETSIZE|-a|R|"4096"|  
|SQLCMDERRORLEVEL|-M|R/W|0|  
|SQLCMDMAXVARTYPEWIDTH|-y|R/W|"256"|  
|SQLCMDMAXFIXEDTYPEWIDTH|-y|R/W|"0" = 無制限|  
|SQLCMDEDITOR||R/W|"edit.com"|  
|SQLCMDINI||R|""|
|SQLCMDUSEAAD  | -G | R/W | "" |  
  
 SQLCMDUSER、SQLCMDPASSWORD、および SQLCMDSERVER は、 **:Connect** が使用されるときに設定されます。  
  
 R は、その値がプログラムの初期化時に一度だけ設定できることを示します。  
  
 R/W は、 **setvar** コマンドを使用して値を変更できること、および後続のコマンドに新しい値が反映されることを示します。  
  
## <a name="sqlcmd-commands"></a>sqlcmd コマンド  
 **sqlcmd** では、Transact-SQL ステートメントの他に次のコマンドも使用できます。  
  
:::row:::
   :::column span="":::
      **GO** [*count*]
   :::column-end:::
   :::column span="":::
      **:List**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      **[:]** **RESET**
   :::column-end:::
   :::column span="":::
      **:Error**  
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      **[:]** **ED**
   :::column-end:::
   :::column span="":::
      **:Out**  
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [**:**] **!!**
   :::column-end:::
   :::column span="":::
      **:Perftrace**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      **[:]** **QUIT**
   :::column-end:::
   :::column span="":::
      **:Connect**  
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      **[:]** **EXIT**
   :::column-end:::
   :::column span="":::
      **:On Error**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      **:r**
   :::column-end:::
   :::column span="":::
      **:Help**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      **:ServerList**
   :::column-end:::
   :::column span="":::
      **:XML** [**ON** &#124; **OFF**]
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      **:Setvar**
   :::column-end:::
   :::column span="":::
      **:Listvar** 
   :::column-end:::
:::row-end:::
  
 **sqlcmd** コマンドを使用するときは、次の点に注意してください。  
  
- GO を除くすべての **sqlcmd** コマンドは、コロン (:) によってプレフィックス指定する必要があります。  
  
    > [!IMPORTANT]  
    >  既存の **osql** スクリプトとの互換性を保つために、一部のコマンドはコロンなしで認識され、 **[:]** によって示されます。
  
- **sqlcmd** コマンドが認識されるのは、コマンドが行の先頭にある場合のみです。  
  
- すべての **sqlcmd** コマンドには、大文字と小文字の区別はありません。  
  
- 各コマンドは個別の行に指定する必要があります。 コマンドの後には、Transact-SQL ステートメントや別のコマンドを指定できません。  
  
- コマンドは即座に実行されます。 Transact-SQL ステートメントのように、実行バッファーに配置されません。  
  
 **編集コマンド**  
  **[:]** **ED**  
 テキスト エディターを開始します。 このエディターは、現在の Transact-SQL バッチまたは最後に実行したバッチを編集する場合に使用できます。 最後に実行したバッチを編集するには、最後のバッチの実行が完了した直後に **ED** コマンドを入力する必要があります。  
  
 テキスト エディターは、SQLCMDEDITOR 環境変数で定義したエディターです。 既定のエディターは "Edit" です。 エディターを変更するには、SQLCMDEDITOR 環境変数を設定します。 たとえば、エディターを Microsoft メモ帳に設定するには、コマンド プロンプトで次のように入力します。  
  
 `SET SQLCMDEDITOR=notepad`  
  
 **[:]** **RESET**  
 ステートメント キャッシュをクリアします。  
  
 **:List**  
 ステートメント キャッシュの内容を出力します。  
  
 **変数**  
  **:Setvar** \<**var**> [ **"** _value_ **"** ]  
 **sqlcmd** スクリプト変数を定義します。 スクリプト変数は `$(VARNAME)`という形式になります。  
  
 変数名では大文字と小文字が区別されません。  
  
 スクリプト変数は次の方法で指定できます。  
  
- コマンド ラインのオプションを暗黙的に使用します。 たとえば、 **-l** オプションでは SQLCMDLOGINTIMEOUT という **sqlcmd** 変数が設定されます。  
  
- **:Setvar** コマンドを明示的に使用します。  
  
- **sqlcmd** の実行前に環境変数を定義します。  
  
> [!NOTE]  
>  **-X** オプションを使用すると、環境変数が **sqlcmd** に渡されなくなります。  
  
 **:Setvar** を使って定義した変数と環境変数が同じ名前の場合は、 **:Setvar** を使用して定義した変数が優先されます。  
  
 変数名には空白文字を含めることはできません。  
  
 また変数名には、$ (var) のような変数表現と同じ形式を使用することはできません。  
  
 スクリプト変数の文字列値に空白文字が含まれる場合は、値を引用符で囲みます。 スクリプト変数の値を設定していない場合は、そのスクリプト変数は削除されます。  
  
 **:Listvar**  
 現在設定されているスクリプト変数の一覧を表示します。  
  
> [!NOTE]  
>  **sqlcmd** によって設定されたスクリプト変数および **:Setvar** コマンドを使用して設定されたスクリプト変数のみが表示されます。  
  
 **出力コマンド**  
  **:Error**   
 _**\<**_  _filename_  **_>|_ STDERR|STDOUT**  
 すべてのエラー出力を、 *file name* によって指定されたファイル、または **stderr** や **stdout** にリダイレクトします。 **Error** コマンドは、スクリプト内で複数回使用できます。 既定では、エラー出力は **stderr** に送られます。  
  
 *ファイル名*  
 出力を受信するファイルを作成して開きます。 ファイルが既に存在している場合は、ファイルは 0 バイトに切り詰められます。 権限またはその他の理由でファイルが使用できない場合は、出力は切り替えられず、最後に指定した出力先または既定の出力先に送信されます。  
  
 **STDERR**  
 エラー出力を **stderr** ストリームに切り替えます。 ストリームがリダイレクトされている場合は、ストリームがリダイレクトされた対象がエラー出力を受信します。  
  
 **STDOUT**  
 エラー出力を **stdout** ストリームに切り替えます。 ストリームがリダイレクトされている場合は、ストリームがリダイレクトされた対象がエラー出力を受信します。  
  
 **:Out \<** _filename_ **>** | **STDERR**| **STDOUT**  
 すべてのクエリ結果を、 *file name* によって指定されたファイル、または **stderr** や **stdout** に作成してリダイレクトします。 既定では、出力は **stdout** に送られます。 ファイルが既に存在している場合は、ファイルは 0 バイトに切り詰められます。 **Out** コマンドは、スクリプト内で複数回使用できます。  
  
 **:Perftrace \<** _filename_ **>** | **STDERR**| **STDOUT**  
 すべてのパフォーマンス トレース情報を、 *file name* によって指定されたファイル、または **stderr** や **stdout** に作成してリダイレクトします。 既定では、パフォーマンス トレース出力は **stdout** に送られます。 ファイルが既に存在している場合は、ファイルは 0 バイトに切り詰められます。 **Perftrace** コマンドは、スクリプト内で複数回使用できます。  
  
 **実行制御コマンド**  
  **:On Error**[ **exit** | **ignore**]  
 スクリプト実行中またはバッチ実行中のエラー発生時に対応するアクションを設定します。  
  
 **exit** オプションを使用した場合、 **sqlcmd** は該当するエラー値を表示して終了します。  
  
 **ignore** オプションを使用すると、 **sqlcmd** はエラーを無視し、バッチまたはスクリプトの実行を続行します。 既定では、エラー メッセージが出力されます。  
  
 **[:]** **QUIT**  
 **sqlcmd** が終了します。  
  
 **[:]** **EXIT**[ **(** _statement_ **)** ]  
 **sqlcmd** からの戻り値に、SELECT ステートメントの結果を使用できます。 数値の場合、結果行の最終行の第 1 列は、4 バイトの (長) 整数に変換されます。 MS-DOS、Linux、および Mac は、下位バイトを親プロセスやオペレーティング システムのエラー レベルに渡します。 Windows 200x では、4 バイトの整数全体を渡します。 の構文は次のとおりです。  
  
 `:EXIT(query)`  
  
 次に例を示します。  
  
 `:EXIT(SELECT @@ROWCOUNT)`  
  
 バッチ ファイルの一部として、 **EXIT** パラメーターを使用することもできます。 たとえば、コマンド プロンプトで次のように入力します。  
  
 `sqlcmd -Q "EXIT(SELECT COUNT(*) FROM '%1')"`  
  
 **sqlcmd** ユーティリティにより、かっこ **()** 内のすべての情報がサーバーに送信されます。 システム ストアド プロシージャで 1 つの値セットを選択し、値を返すように指定した場合、返されるのは選択した値のみです。 かっこ内に何も指定せずに EXIT **()** ステートメントを指定すると、バッチ内のそのステートメントより前にあるものすべてを実行し、戻り値を返さずに終了します。  
  
 不適切なクエリを指定すると、 **sqlcmd** は戻り値を返さずに終了します。  
  
 EXIT の形式を次に示します。  
  
- :EXIT  
  
 バッチを実行せずに直ちに終了し、値を返しません。  
  
- :EXIT ( )  
  
 バッチを実行してから終了し、値を返しません。  
  
- :EXIT (query)  
  
 クエリを含むバッチを実行し、クエリの結果を返して終了します。  
  
 RAISERROR を **sqlcmd** スクリプトの中で使用し、状態 127 が発生すると、 **sqlcmd** は終了し、メッセージ ID をクライアントに返します。 次に例を示します。  
  
 `RAISERROR(50001, 10, 127)`  
  
 このエラーが発生すると、 **sqlcmd** スクリプトは終了し、メッセージ ID 50001 がクライアントに返されます。  
  
 戻り値 -1 から -99 は SQL Server によって予約済みです。また、**sqlcmd** では次のような追加の戻り値を定義しています。  
  
|戻り値|説明|  
|-------------------|-----------------|  
|-100|戻り値を選択する前に、エラーが発生した。|  
|-101|戻り値を選択するときに、行が見つからなかった。|  
|-102|戻り値を選択するときに、変換エラーが発生した。|  
  
 **GO** [*count*]  
 GO は、バッチの終わりとキャッシュされた Transact-SQL ステートメントの実行を知らせます。 バッチは、個別のバッチとして複数回実行されます。 1 回のバッチで変数を複数回宣言することはできません。
  
 **その他のコマンド**  
  **:r \<** _filename_ **>**  
 **\<**_filename_**>** によって指定されたファイルを基に、追加の Transact-SQL ステートメントと **sqlcmd** コマンドを解析し、ステートメント キャッシュ内に登録します。  
  
 **GO** が最後に記述されていない Transact-SQL ステートメントがファイルに含まれている場合は、その行の **:r** の後に **GO** を入力する必要があります。  
  
> [!NOTE]  
>  **\<** _filename_ **>** は、**sqlcmd** が実行されたスタートアップ ディレクトリと関連して読み取られます。  
  
 ファイルは、バッチ ターミネータが検出された後に読み取られ、実行されます。 **:r** コマンドは複数発行できます。 ファイルには、どのような **sqlcmd** コマンドでも含めることができます。 これには、バッチ ターミネータの **GO** も含まれます。  
  
> [!NOTE]  
>  対話モードで表示される行数は、`:r` コマンドが検出されるたびに、1 行ずつ増えます。 `:r` コマンドは、リスト コマンドの出力に表示されます。  
  
 **:Serverlist**  
 ローカルに構成されたサーバーと、ネットワーク上でブロードキャストしているサーバー名の一覧を表示します。  
  
 **:Connect**  _server_name_[ **\\** _instance\_name_] [-l *timeout*] [-U *user_name* [-P *password*]]  
 SQL Server のインスタンスに接続します。 また、現在の接続を終了します。  
  
 タイムアウト オプション :  
  
|値|動作|  
|-|-|  
|0|待機状態を維持|  
|n>0|n 秒間待機|  
  
 SQLCMDSERVER スクリプト変数により、現在のアクティブな接続が反映されます。  
  
 *timeout* を指定しないと、SQLCMDLOGINTIMEOUT 変数の値が既定値になります。  
  
 *user_name* のみを指定した場合 (オプションとして指定した場合も、環境変数として指定した場合も)、ユーザーはパスワードの入力を要求されます。 SQLCMDUSER 環境変数または SQLCMDPASSWORD 環境変数が設定されている場合、ユーザーはパスワードを要求されません。 オプションも環境変数も指定されない場合は、Windows 認証モードを使用してサインインします。 たとえば、統合セキュリティを使用して、SQL Server `myserver` のインスタンス `instance1` に接続するには、次のコマンドを使用します。  
  
 `:connect myserver\instance1`  
  
 スクリプト変数を使用して `myserver` の既定のインスタンスに接続するには、次を使用します。  
  
 `:setvar myusername test`  
  
 `:setvar myservername myserver`  
  
 `:connect $(myservername) $(myusername)`  
  
 **[:]** **!!** < *command*>  
 オペレーティング システムのコマンドを実行します。 オペレーティング システムのコマンドを実行するには、行頭に 2 つの感嘆符 ( **!!** ) を入力し、続けてオペレーティング システムのコマンドを入力します。 次に例を示します。  
  
 `:!! Dir`  
  
> [!NOTE]  
>  コマンドは **sqlcmd** を実行中のコンピューター上で実行されます。  
  
 **:XML** [**ON** | **OFF**]  
 詳細については、このトピックの「[XML 出力形式](#OutputXML)」と「[JSON 出力形式](#OutputJSON)」を参照してください。  
  
 **:Help**  
 **sqlcmd** コマンドと各コマンドの短い説明を一覧表示します。  
  
### <a name="sqlcmd-file-names"></a>sqlcmd のファイル名  
 **sqlcmd** の入力ファイルは **-i** オプションまたは **:r** コマンドで指定できます。 出力ファイルは **-o** オプションまたは **:Error**、 **:Out** 、および **:Perftrace** コマンドで指定できます。 指定するファイルについてのガイドラインを次に示します。  
  
- **:Error**、 **:Out**、および **:Perftrace** を指定するときは、個別に **\<**_filename_**>** を指定します。 同じ **\<**_filename_**>** を使用すると、各コマンドからの入力が混在する場合があります。  
  
- ローカル コンピューターの **sqlcmd** からリモート サーバー上の入力ファイルが呼び出され、ファイルに :Out c:\OutputFile.txt のようにドライブ パスが含まれていると、 出力ファイルはリモート サーバーではなく、ローカル コンピューター上に作成されます。  
  
- 有効なファイル パスは、`C:\<filename>`、`\\<Server>\<Share$>\<filename>`、`"C:\Some Folder\<file name>"` などです。 パスに空白が含まれる場合は、引用符を使用します。  
  
- 各新規 **sqlcmd** セッションにより、同じ名前の既存のファイルが上書きされます。  
  
### <a name="informational-messages"></a>情報メッセージ

**sqlcmd** は、サーバーから送信されたすべての情報メッセージを出力します。 次の例では、Transact-SQL ステートメントが実行された後、情報メッセージが出力されます。
  
コマンド プロンプトで、次のコマンドを入力します。

`sqlcmd`
  
sqlcmd プロンプトで次のように入力します。

`USE AdventureWorks2012;`

`GO`

Enter キーを押すと、次の情報メッセージが出力されます。"データベース コンテキストが 'AdventureWorks2012' に変更されました。"  
  
### <a name="output-format-from-transact-sql-queries"></a>Transact-SQL クエリからの出力形式  
 まず、**sqlcmd** は SELECT リストで指定した列名を含む列ヘッダーを出力します。 列名は、SQLCMDCOLSEP で指定された文字を使用して分割されます。 既定では、空白です。 列名が列幅よりも短い場合は、出力は次の列まで空白で埋められます。  
  
 この行の次には区切り行が出力されます。区切り行は、連続したダッシュ文字です。 次に出力の例を示します。  
  
 **sqlcmd** を開始します。 **sqlcmd** コマンド プロンプトで次のクエリを入力します。  
  
 `USE AdventureWorks2012;`  
  
 `SELECT TOP (2) BusinessEntityID, FirstName, LastName`  
  
 `FROM Person.Person;`  
  
 `GO`  
  
 Enter キーを押すと、次の結果セットが返されます。  
  
 `BusinessEntityID FirstName    LastName`  
  
 `---------------- ------------ ----------`  
  
 `285              Syed         Abbas`  
  
 `293              Catherine    Abel`  
  
 `(2 row(s) affected)`  
  
 `BusinessEntityID` 列には 4 文字分の幅しかありませんが、長い列名に合わせるため拡張されています。 既定では、出力は 80 文字で終了します。 この設定は、 **-w** オプションを使用するか、SQLCMDCOLWIDTH スクリプト変数を設定することで変更できます。  
  
###  <a name="xml-output-format"></a><a name="OutputXML"></a> XML 出力形式  
 FOR XML 句からの結果である XML 出力は、連続するストリームでフォーマットされずに出力されます。  
  
 XML 出力を行うには、 `:XML ON`コマンドを使用します。  
  
> [!NOTE]  
>  **sqlcmd** は、通常の形式のエラー メッセージを返します。 エラー メッセージが XML 形式の XML テキスト ストリームで出力されることにも注意してください。 `:XML ON`を使用すると、 **sqlcmd** は情報メッセージを表示しません。  
  
 XML モードをオフにするには、`:XML OFF` コマンドを使用します。  
  
 XML OFF コマンドは **sqlcmd** を行指向の出力に切り替えるので、XML OFF コマンドの実行前に GO コマンドは指定しないでください。  
  
 XML (ストリーム入力) データと行セットのデータを混在させることはできません。 XML ストリームを出力する Transact-SQL ステートメントの実行前に、XML ON コマンドが実行されていない場合は、正しく出力されません。 XML ON コマンドが実行されている場合、通常の行セットを出力する Transact-SQL ステートメントは実行できません。  
  
> [!NOTE]  
>  `:XML` コマンドは SET STATISTICS XML ステートメントをサポートしません。  
  
###  <a name="json-output-format"></a><a name="OutputJSON"></a> JSON 出力形式  
 JSON 出力を行うには、 `:XML ON`コマンドを使用します。 これを使用しないと、出力には、列名と JSON テキストの両方が含まれます。 この出力は、有効な JSON ではありません。  
  
 XML モードをオフにするには、`:XML OFF` コマンドを使用します。  
  
 詳細については、この記事の「[XML 出力形式](#OutputXML)」を参照してください。  

### <a name="using-azure-active-directory-authentication"></a>Azure Active Directory 認証の利用

Azure Active Directory 認証の利用例

```cmd
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net  -G  -l 30
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -G -U bob@contoso.com -P MyAADPassword -l 30
```
  
## <a name="sqlcmd-best-practices"></a>sqlcmd のベスト プラクティス

次の説明を参考にして、セキュリティと効率を最大にしてください。  

- 統合セキュリティを使用します。  

- 自動化された環境では **-X[1]** を使用します。

- 適切な NTFS ファイル システム権限を使用して、入力ファイルと出力ファイルのセキュリティを保護します。

- パフォーマンスを向上させるには、複数のセッションではなく、1 つの **sqlcmd** セッションの中で、できるだけ作業するようにします。

- バッチまたはクエリ実行のタイムアウト値を、推定所要時間よりも長めに設定します。

次の説明を参考にして、正確さを最大限に高めます。

- **-V16** を使用して、[重大度 16 レベルのメッセージ](../relational-databases/errors-events/database-engine-error-severities.md#levels-of-severity)をログに記録します。  重大度 16 のメッセージは、ユーザーが訂正できる一般的なエラーを示します。

- プロセスが終了した後、終了コードと DOS ERRORLEVEL 変数を確認します。  通常、**sqlcmd** からは 0 が返されます。それ以外の場合は、 **-V** によって構成された ERRORLEVEL が設定されます。  つまり、ERRORLEVEL が SQL Server から報告されるエラー番号と同じ値であると想定しないでください。 エラー番号は、システム関数 [ **@@ERROR**](../t-sql/functions/error-transact-sql.md) に対応する SQL Server 固有の値です。  ERRORLEVEL は、(SQLCMD が) 終了した理由を示す SQLCMD 固有の値であり、その値はコマンド ライン引数 **-b** を指定することによって影響を受けます。

終了コードおよび DOS ERRORLEVEL のチェックと組み合わせて **-V16** を使用すると、自動化された環境 (特に運用リリース前の品質ゲート) でエラーをキャッチできます。

## <a name="next-steps"></a>次のステップ

- [sqlcmd ユーティリティの起動](~/relational-databases/scripting/sqlcmd-start-the-utility.md)
- [sqlcmd を使用した Transact-SQL スクリプト ファイルの実行](~/relational-databases/scripting/sqlcmd-run-transact-sql-script-files.md)
- [sqlcmd ユーティリティの使用](~/relational-databases/scripting/sqlcmd-use-the-utility.md)
- [sqlcmd でのスクリプト変数の使用](~/relational-databases/scripting/sqlcmd-use-with-scripting-variables.md)
- [sqlcmd によるデータベース エンジンへの接続](~/relational-databases/scripting/sqlcmd-connect-to-the-database-engine.md)
- [クエリ エディターによる SQLCMD スクリプトの編集](~/relational-databases/scripting/edit-sqlcmd-scripts-with-query-editor.md)
- [ジョブ ステップの管理](~/ssms/agent/manage-job-steps.md)   
- [CmdExec ジョブ ステップの作成](~/ssms/agent/create-a-cmdexec-job-step.md)  

[!INCLUDE[get-help-options](../includes/paragraph-content/get-help-options.md)]