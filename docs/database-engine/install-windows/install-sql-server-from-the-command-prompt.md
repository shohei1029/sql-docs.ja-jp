---
title: SQL Server のインストール - コマンド プロンプト パラメーター
description: この記事では、SQL Server インストールのコマンド パラメーターについて説明します。 インストールして構成する機能を指定できます。
ms.prod: sql
ms.technology: install
ms.topic: conceptual
helpviewer_keywords:
- installing SQL Server, command prompt
- installation scripts [SQL Server]
- maintenance scripts [SQL Server]
- REMOVENODE property
- components [SQL Server], removing
- command prompt [SQL Server], SQL Server installations
- ASACCOUNT parameter
- failover clustering [SQL Server], installing
- master database [SQL Server], rebuilding
- SQLCOLLATION parameter
- clusters [SQL Server], installing
- unattended installations [SQL Server]
- modifying collations
- AGTPASSWORD parameter
- USESYSDB parameter
- RSPASSWORD parameter
- AUTOSTART parameter
- ASPASSWORD parameter
- stand-alone installations [SQL Server]
- SAMPLEDATABASESERVER parameter
- adding components
- SAPWD parameter
- scripts [SQL Server], uninstallations
- remote installations [SQL Server]
- components [SQL Server], installing
- TARGETCOMPUTER parameter
- REMOVENODE parameter
- REINSTALLMODE parameter
- scripts [SQL Server], maintenance
- rebuilding registry
- SQLPASSWORD parameter
- rebuilding databases
- IP property
- PIDKEY parameter
- RSCONFIGURATION parameter
- ADDLOCAL parameter
- Setup [SQL Server], command prompt
- REBUILDDATABASE parameter
- SECURITYMODE parameter
- REMOVE property
- DISABLENETWORKPROTOCOLS parameter
- INSTALLDATADIR parameter
- REMOVE parameter
- removing components
- SQLACCOUNT parameter
- parameters [SQL Server], SQL Server installations
- UPGRADE parameter
- shortcuts [SQL Server]
- updating components
- removing SQL Server
- clustered instance of SQL Server
- INSTALLSQLDATADIR parameter
- RSACCOUNT parameter
- ADMINPASSWORD parameter
- GROUP property
- ERRORREPORTING property
- uninstallation scripts [SQL Server]
- AGTACCOUNT parameter
- SAVESYSDB parameter
- INSTALLVS parameter
- INSTANCENAME parameter
- scripts [SQL Server], installations
- rebuilding database, master
- uninstalling SQL Server
- ASCOLLATION parameter
- .ini files
- ADDNODE parameter
- command line installations [SQL Server]
- VS parameter
- INSTALLASDATADIR parameter
- INSTALLSQLDIR parameter
- nodes [Faillover Clustering], command prompt
- INSTALLSQLSHAREDDIR parameter
ms.assetid: df40c888-691c-4962-a420-78a57852364d
author: cawrites
ms.author: chadam
ms.reviewer: ''
ms.custom: ''
ms.date: 01/07/2020
monikerRange: '>=sql-server-2016'
ms.openlocfilehash: 559bb922735dd6cd56a417bc09964f47297ab0ca
ms.sourcegitcommit: bf7577b3448b7cb0e336808f1112c44fa18c6f33
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104611069"
---
# <a name="install-sql-server-from-the-command-prompt"></a>コマンド プロンプトからの SQL Server のインストール

[!INCLUDE [SQL Server -Windows Only](../../includes/applies-to-version/sql-windows-only.md)]

SQL Server のセットアップを実行する前に、「 [SQL Server のインストール計画](../../sql-server/install/planning-a-sql-server-installation.md)」を参照してください。  

SQL Server の新しいインスタンスをコマンド プロンプトでインストールすると、インストールする機能とその機能の構成を指定できます。 また、セットアップのユーザー インターフェイスに、サイレント モード、基本的な対話方式、または完全な対話方式を指定できます。  

コマンド プロンプトからインストールするには、管理コマンド プロンプトを開き、[SQL Server セットアップ メディア](https://www.microsoft.com/sql-server/sql-server-downloads)内の setup.exe がある場所に移動します。 実行しようとしている操作を達成するための、必須のパラメーターと省略可能なパラメーターと共に、`setup.exe` コマンドを実行します。

`C:\SQLMedia\SQLServer2019> setup.exe /[Option] /[Option] = {value}`

次の例では、Quiet モードで SQL Server データベース エンジン、SQL Server Analysis Services、SQL Server Integration Services、SQL Server ツールがインストールされます。

```console
C:\SQLMedia\SQLServer2019> setup.exe /Q /IACCEPTSQLSERVERLICENSETERMS /ACTION="install" /PID="AAAAA-BBBBB-CCCCC-DDDDD-EEEEE" /FEATURES=SQL,AS,IS,Tools
/INSTANCENAME=MSSQLSERVER /SQLSVCACCOUNT="MyDomain\MyAccount"
/SQLSVCPASSWORD="************" /SQLSYSADMINACCOUNTS="MyDomain\MyAccount "
/AGTSVCACCOUNT="MyDomain\MyAccount" /AGTSVCPASSWORD="************"
/ASSVCACCOUNT="MyDomain\MyAccount" /ASSVCPASSWORD="************"
/ISSVCAccount="MyDomain\MyAccount" /ISSVCPASSWORD="************"
/ASSYSADMINACCOUNTS="MyDomain\MyAccount"
```

コンソール内で使用可能なすべてのコマンドの一覧を表示するには、/help フラグを指定して実行可能ファイルを実行します。 

```console
C:\SQLMedia\SQLServer2019> setup.exe /help
```

この記事の残りの部分では、使用可能なパラメーターの詳細について説明します。 

  
> [!NOTE]
> コマンド プロンプトを使用してインストールする場合、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、/Q パラメーターを使用した非表示モード、または /QS パラメーターを使用した簡易非表示モードがサポートされます。 /QS スイッチでは、進捗状況のみが表示され、入力はできません。また、該当する場合でもエラー メッセージは表示されません。 /QS パラメーターは、/Action=install を指定した場合にのみサポートされます。  
  
 どのインストール方法を使用するかにかかわらず、個人として、または組織を代表して、ソフトウェア ライセンス条項に同意するかどうかを確認する必要があります。ただし、ソフトウェアの使用に別の契約 (マイクロソフト ボリューム ライセンス契約や、ISV または OEM とのサード パーティ契約など) が適用される場合を除きます。  
  
 ライセンス条項は、セットアップのユーザー インターフェイスで確認し、同意することができます。 /Q または /QS パラメーターを使用した自動インストールでは、/IACCEPTSQLSERVERLICENSETERMS パラメーターを指定する必要があります。 ライセンス条項は、「 [マイクロソフト ソフトウェア ライセンス条項](https://go.microsoft.com/fwlink/?LinkId=148209)」で別途確認できます。  
  
> [!NOTE] 
> ソフトウェアの入手方法 (マイクロソフトのボリューム ライセンスを通じて入手した場合など) によっては、ソフトウェアの使用に追加の条件が課されることがあります。  
  
 コマンド プロンプトによるインストールは、次のシナリオで使用されます。  
  
-   コマンド プロンプトから構文とパラメーターを指定して、ローカル コンピューター上で SQL Server のインスタンスと共有コンポーネントのインストール、アップグレード、または削除を行う場合。    
-   フェールオーバー クラスター インスタンスのインストール、アップグレード、または削除を行う場合。   
-   [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のあるエディションから [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]の別のエディションにアップグレードする場合。    
-   構成ファイルに指定された構文とパラメーターを使用して、ローカル コンピューターに [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスをインストールする場合。 この方法を使用すると、インストール構成を複数のコンピューターにコピーしたり、フェールオーバー クラスターのインストールで複数のノードをインストールしたりすることができます。  
  
 
> [!NOTE]
> ローカル インストールの場合は、セットアップを管理者として実行する必要があります。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] をリモート共有からインストールする場合は、そのリモート共有に対する読み取り権限と実行権限を持つドメイン アカウントを使用する必要があります。 フェールオーバー クラスターのインストールの場合は、サービスとしてログインする権限、およびすべてのフェールオーバー クラスター ノード上のオペレーティング システムの一部として動作する権限を持つローカル管理者である必要があります。  
  
##  <a name="proper-use-of-setup-parameters"></a><a name="ProperUse"></a> セットアップ パラメーターの適切な使用法  
正しい構文でインストール コマンドを作成するには、次のガイドラインに従ってください。  
  
-   /PARAMETER (例: `/INDICATEPROGRESS`)
-   /PARAMETER=true/false (例: `/SQLSVCINSTANTFILEINIT=True`)
-   /PARAMETER=1/0 (ブール型用) (例: `/TCPENABLED=1`)
-   /PARAMETER="値" (すべての単一値パラメーター用)。 (例: `/PID="PID" /SQLSVCSTARTUPTYPE="Automatic"`)
    - パスを必要とするパラメーターの場合、`/INSTANCEDIR=c:\Path` または `/INSTANCEDIR="c:\Path"` がサポートされています。  
-   /PARAMETER="値 1" "値 2" "値 3" (すべての複数値パラメーター用)。 (例: `/SQLSYSADMINACCOUNTS="Contoso\John" "Contoso\Mary"`)
    - **例外**: `/FEATURES`。これは複数値を持つパラメーターですが、その書式はスペースのないコンマ区切りの `/FEATURES=AS,RS,IS` です。 

  
> [!IMPORTANT]  
> [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] をインストールするときに、INSTANCEDIR と SQLUSERDBDIR と同じディレクトリ パスを指定した場合、SQL Server エージェントとフルテキスト検索は、アクセス許可がないために開始されません。  
  
> [!NOTE]  
> リレーショナル サーバーの値では、1 つまたは 2 つの円記号を追加して終了する形式のパスがサポートされています。  

次のセクションでは、インストール、更新、修復のシナリオのためのコマンドライン インストール スクリプトを作成するパラメーターについて示します。  
  
[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] コンポーネント用として挙げられているパラメーターは、そのコンポーネント専用です。 SQL Server エージェント パラメーターおよび SQL Server ブラウザー パラメーターは、 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]をインストールする場合に適用されます。  


##  <a name="installation-parameters"></a><a name="Install"></a> インストール パラメーター  
 次の表にあるパラメーターを使用して、インストール用のコマンドライン スクリプトを作成します。  
  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] コンポーネント|パラメーター|説明|  
|-----------------------------------------|---------------|-----------------|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップ コントロール|/ACTION<br /><br /> **必須**|インストール ワークフローを示すために必要です。<br /><br /> サポートされる値:**インストール**。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/SUPPRESSPRIVACYSTATEMENTNOTICE<br /><br /> **自動インストールのために /Q パラメーターまたは /QS パラメーターを指定した場合にのみ必須です。**|プライバシーに関する声明を非表示にします。 このフラグを使用すると、[プライバシーに関する声明](../../sql-server/sql-server-privacy.md)に同意したことになります。  |  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/IACCEPTSQLSERVERLICENSETERMS<br /><br /> **自動インストールのために /Q パラメーターまたは /QS パラメーターを指定した場合にのみ必須です。**|ライセンス条項への同意を確認するために必要です。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Python セットアップ コントロール|/IACCEPTPYTHONLICENSETERMS <br /><br /> **Anaconda Python パッケージを含む自動インストールのために /Q パラメーターまたは /QS パラメーターを指定した場合にのみ必須です。**|ライセンス条項への同意を確認するために必要です。| 
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] R セットアップ コントロール|/IACCEPTROPENLICENSETERMS <br /><br /> **Microsoft R Open パッケージを含む自動インストールのために /Q パラメーターまたは /QS パラメーターを指定した場合にのみ必須です。**|ライセンス条項への同意を確認するために必要です。| 
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップ コントロール|/ENU<br /><br /> **省略可能**|ローカライズされたオペレーティング システムに [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の英語版をインストールする場合に、オペレーティング システムに対応する言語と英語の両方の言語パックがインストール メディアに含まれているときは、このパラメーターを使用します。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップ コントロール|/UpdateEnabled<br /><br /> **省略可能**|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップが製品の更新プログラムを検出し、それらを含める必要があるかどうかを指定します。 有効値は True および False または 1 および 0 です。 既定では、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップには検出された更新プログラムが含まれます。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップ コントロール|/UpdateSource<br /><br /> **省略可能**|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップが製品の更新プログラムを取得する場所を指定します。 有効値は [!INCLUDE[msCoName](../../includes/msconame-md.md)] Update を検索する "MU"、有効なフォルダー パス、相対パス (`.\MyUpdates` など)、または UNC 共有です。 既定では、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップは [!INCLUDE[msCoName](../../includes/msconame-md.md)] Update、または Windows Server Update Services を介して Windows Update Service を検索します。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップ コントロール|/CONFIGURATIONFILE<br /><br /> **省略可能**|使用する [ConfigurationFile](./install-sql-server-using-a-configuration-file.md) を指定します。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップ コントロール|/ERRORREPORTING<br /><br /> **省略可能**|[!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 以降には影響しません。 <br/><br/> エラーのフィードバックを Microsoft に送信する方法を管理するには、「[SQL Server 2016 以降を構成し、フィードバックを Microsoft に送信する方法](https://support.microsoft.com/kb/3153756)」を参照してください。 <br/><br/>以前のバージョンでは、これにより SQL Server のエラー報告が指定されます。<br /><br /> 詳細については、「 [Microsoft エラー報告サービスのプライバシーに関する声明](../../sql-server/sql-server-privacy.md)」を参照してください。<br /><br /> サポートされる値:<br /><br /> 0 = 無効<br /><br /> 1 = 有効|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップ コントロール|/FEATURES<br /><br /> - または -<br /><br /> /ROLE<br /><br /> **必須**|インストールするコンポーネントを指定します。<br /><br /> インストールする [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] コンポーネントを個別に指定するには、 **/FEATURES** を選びます。 詳細については、「 [機能パラメーター](#Feature) 」を参照してください。<br /><br /> セットアップ ロールを指定するには、 **/ROLE** を選びます。 セットアップ ロールは、事前に定義された構成で [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] をインストールします。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップ コントロール|/HELP、?<br /><br /> **省略可能**|インストール パラメーターの使用方法を表示します。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップ コントロール|/INDICATEPROGRESS<br /><br /> **省略可能**|詳細なセットアップ ログ ファイルがコンソールにパイプされるように指定します。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップ コントロール|/INSTALLSHAREDDIR<br /><br /> **省略可能**|共有コンポーネント (64 ビット) の既定以外のインストール ディレクトリを指定します。<br /><br /> 既定値は `%Program Files%\Microsoft SQL Server` です<br /><br /> `%Program Files(x86)%\Microsoft SQL Server` に設定することはできません|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップ コントロール|/INSTALLSHAREDWOWDIR<br /><br /> **省略可能**|共有コンポーネント (32 ビット) の既定以外のインストール ディレクトリを指定します。 64 ビット システムのみでサポートされます。<br /><br /> 既定値は `%Program Files(x86)%\Microsoft SQL Server` です<br /><br /> `%Program Files%\Microsoft SQL Server` に設定することはできません|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップ コントロール|/INSTANCEDIR<br /><br /> **省略可能**|インスタンス専用のコンポーネントについて既定以外のインストール ディレクトリを指定します。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップ コントロール|/INSTANCEID<br /><br /> **省略可能**|[InstanceID](#InstanceID)の既定値以外の値を指定します。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップ コントロール|/INSTANCENAME<br /><br /> **必須**|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンス名を指定します。<br /><br /> 詳細については、「 [Instance Configuration](../../sql-server/install/instance-configuration.md)」を参照してください。|  
|PolyBase|/PBENGSVCACCOUNT<br /><br /> **省略可能**|エンジン サービスのアカウントを指定します。 既定値は、 **NT Authority\NETWORK SERVICE** です。|  
|PolyBase|/PBDMSSVCPASSWORD<br /><br /> **省略可能**|エンジン サービス アカウントのパスワードを指定します。 管理されたサービス アカウント、仮想アカウント、またはビルトイン アカウントの使用時、このパラメーターは省略できます。|  
|PolyBase|/PBENGSVCSTARTUPTYPE<br /><br /> **省略可能**|PolyBase エンジン サービスのスタートアップ モードを指定します Automatic (既定値)、Disabled、Manual。|  
|PolyBase|/PBPORTRANGE<br /><br /> **省略可能**|PolyBase サービスのポート範囲 (6 ポート以上) を指定します。 例:<br /><br /> `/PBPORTRANGE=16450-16460`|  
|PolyBase|/PBSCALEOUT<br /><br /> **省略可能**|PolyBase スケールアウト計算グループの一部として [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] インスタンスを使用するかどうかを指定します。 ヘッド ノードを含む PolyBase スケール アウト計算グループを構成する場合は、このオプションを使用します。 サポートされる値:**True**、**False**|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップ コントロール|/PID<br /><br /> **省略可能**|SQL Server のエディション用のプロダクト キーを指定します。 このパラメーターが指定されていない場合、Evaluation が使用されます。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップ コントロール|/Q または /QUIET<br /><br /> **省略可能**|セットアップが、ユーザー インターフェイスなしで、非表示モードで実行されるように指定します。 このパラメーターは、自動インストールに使用されます。 /Q パラメーターによって /QS パラメーターの入力がオーバーライドされます。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップ コントロール|/QS または /QUIETSIMPLE <br /><br /> **省略可能**|セットアップが UI を使用して実行され、UI を使用して進捗状況が表示されるように指定します。さらに、セットアップで入力ができないように、またはエラー メッセージが表示されないように指定します。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップ コントロール|/UIMODE<br /><br /> **省略可能**|セットアップ時に表示するダイアログ ボックスの数を最小限に抑えるかどうかを指定します。<br /><br /> **/UIMode** は、 **/ACTION=INSTALL** および **UPGRADE** の各パラメーターと共に使用する必要があります。 サポートされる値:<br /><br /> **/UIMODE=Normal** は、Express 以外のエディションの既定値で、選ばれた機能のセットアップ ダイアログ ボックスをすべて表示します。<br /><br /> **/UIMODE=AutoAdvance** は、Express エディションの既定値で、重要でないダイアログ ボックスをスキップします。<br /><br /> <br /><br /> **UIMODE** は、他のパラメーターと組み合わせて使用するとオーバーライドされます。 たとえば、 **/UIMODE=AutoAdvance** と **/ADDCURRENTUSERASSQLADMIN=FALSE** の両方を指定した場合、準備ダイアログ ボックスには現在のユーザーの情報が自動入力されません。<br /><br /> **UIMode** 設定を **/Q** または **/QS** の各パラメーターと共に使用することはできません。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップ コントロール|/SQMREPORTING<br /><br /> **省略可能**|[!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] には影響しません。 <br/><br/>エラーのフィードバックを Microsoft に送信する方法を管理するには、「[フィードバックをマイクロソフトに送信するように [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] を構成する方法](https://support.microsoft.com/kb/3153756)」を参照してください。 <br/><br/>以前のバージョンでは、これにより SQL Server の機能の使用状況レポートが指定されます。<br /><br />サポートされる値:<br /><br /> 0 = 無効<br /><br /> 1 = 有効|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップ コントロール|/HIDECONSOLE<br /><br /> **省略可能**|コンソール ウィンドウを非表示にするか閉じる場合に指定します。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エージェント|/AGTSVCACCOUNT<br /><br /> **必須**|SQL Server エージェント サービスのアカウントを指定します。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エージェント|/AGTSVCPASSWORD<br /><br /> [必須](#Accounts)|SQL Server エージェント サービス アカウントのパスワードを指定します。 管理されたサービス アカウント、仮想アカウント、またはビルトイン アカウントの使用時、このパラメーターは省略できます。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エージェント|/AGTSVCSTARTUPTYPE<br /><br /> **省略可能**|SQL Server エージェント サービスの [スタートアップ](#Accounts) モードを指定します。<br /><br /> サポートされる値:<br /><br /> **自動**<br /><br /> **Disabled**<br /><br /> **手動**|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASBACKUPDIR<br /><br /> **省略可能**|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] バックアップ ファイルのディレクトリを指定します。 既定値:<br /><br /> 64 ビットの WOW モード: `%Program Files(x86)%\Microsoft SQL Server\<INSTANCEDIR>\<ASInstanceID>\OLAP\Backup`<br /><br /> 他のすべてのインストール: `%Program Files%\Microsoft SQL Server\<INSTANCEDIR>\<ASInstanceID>\OLAP\Backup`|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASCOLLATION<br /><br /> **省略可能**|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]の照合順序の設定を指定します。<br /><br /> 既定値:**Latin1_General_CI_AS**|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASCONFIGDIR<br /><br /> **省略可能**|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] 構成ファイルのディレクトリを指定します。 既定値:<br /><br /> 64 ビットの WOW モード: `%Program Files(x86)%\Microsoft SQL Server\<INSTANCEDIR>\<ASInstanceID>\OLAP\Config`<br /><br /> 他のすべてのインストール: `%Program Files%\Microsoft SQL Server\<INSTANCEDIR>\<ASInstanceID>\OLAP\Config`|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASDATADIR<br /><br /> **省略可能**|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] データ ファイルのディレクトリを指定します。 既定値:<br /><br /> 64 ビットの WOW モード: `%Program Files(x86)%\Microsoft SQL Server\<INSTANCEDIR>\<ASInstanceID>\OLAP\Data`<br /><br /> 他のすべてのインストール: `%Program Files%\Microsoft SQL Server\<INSTANCEDIR>\<ASInstanceID>\OLAP\Data`|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASLOGDIR<br /><br /> **省略可能**|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] ログ ファイルのディレクトリを指定します。 既定値:<br /><br /> 64 ビットの WOW モード: `%Program Files(x86)%\Microsoft SQL Server\<INSTANCEDIR>\<ASInstanceID>\OLAP\Log`<br /><br /> 他のすべてのインストール: `%Program Files%\Microsoft SQL Server\<INSTANCEDIR>\<ASInstanceID>\OLAP\Log`|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASSERVERMODE<br /><br /> **省略可能**|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] インスタンスのサーバー モードを指定します。 有効な値は、MULTIDIMENSIONAL、POWERPIVOT、または TABULAR です。 **ASSERVERMODE** では、大文字と小文字が区別されます。 値はすべて大文字で指定する必要があります。 有効な値の詳細については、「 [Analysis Services のインストール](/analysis-services/instances/install-windows/install-analysis-services)」をご覧ください。|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASSVCACCOUNT<br /><br /> **必須**|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] サービスのアカウントを指定します。|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASSVCPASSWORD<br /><br /> [必須](#Accounts)|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] サービスのパスワードを指定します。 管理されたサービス アカウント、仮想アカウント、またはビルトイン アカウントの使用時、このパラメーターは省略できます。|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASSVCSTARTUPTYPE<br /><br /> **省略可能**|[サービスの](#Accounts) スタートアップ [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] モードを指定します。 サポートされる値:<br /><br /> **自動**<br /><br /> **Disabled**<br /><br /> **手動**|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASSYSADMINACCOUNTS<br /><br /> **必須**|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]の管理者資格情報を指定します。|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASTEMPDIR<br /><br /> **省略可能**|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] 一時ファイルのディレクトリを指定します。 既定値:<br /><br /> 64 ビットの WOW モード: `%Program Files(x86)%\Microsoft SQL Server \<INSTANCEDIR>\<ASInstanceID>\OLAP\Temp`<br /><br /> 他のすべてのインストール: `%Program Files%\Microsoft SQL Server\<INSTANCEDIR>\<ASInstanceID>\OLAP\Temp`|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASPROVIDERMSOLAP<br /><br /> **省略可能**|MSOLAP プロバイダーをインプロセスで実行できるかどうかを指定します。<br /><br /> 既定値:1 = 有効|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/FARMACCOUNT<br /><br /> **SPI_AS_NewFarm の場合に必須**|SharePoint の全体管理サービスとその他の重要なサービスをファームで実行するためのドメイン ユーザー アカウントを指定します。<br /><br /> このパラメーターは、/ROLE = SPI_AS_NEWFARM でインストールされた [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] インスタンスにのみ使用されます。|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/FARMPASSWORD<br /><br /> **SPI_AS_NewFarm の場合に必須**|ファーム アカウントのパスワードを指定します。 管理されたサービス アカウント、仮想アカウント、またはビルトイン アカウントの使用時、このパラメーターは省略できます。|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/PASSPHRASE<br /><br /> **SPI_AS_NewFarm の場合に必須**|アプリケーション サーバーまたは Web フロントエンド サーバーを SharePoint ファームに追加するために使用されるパスフレーズを指定します。<br /><br /> このパラメーターは、/ROLE = SPI_AS_NEWFARM でインストールされた [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] インスタンスにのみ使用されます。|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/FARMADMINIPORT<br /><br /> **SPI_AS_NewFarm の場合に必須**|SharePoint の全体管理 Web アプリケーションに接続するために使用されるポートを指定します。<br /><br /> このパラメーターは、/ROLE = SPI_AS_NEWFARM でインストールされた [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] インスタンスにのみ使用されます。|  
|SQL Server Browser|/BROWSERSVCSTARTUPTYPE<br /><br /> **省略可能**|SQL Server Browser サービスの [スタートアップ](#Accounts) モードを指定します。 サポートされる値:<br /><br /> **自動**<br /><br /> **Disabled**<br /><br /> **手動**|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/ENABLERANU<br /><br /> **省略可能**|[!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] のインストールの実行アカウント資格情報を有効にします。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/INSTALLSQLDATADIR<br /><br /> **省略可能**|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] データ ファイルのデータ ディレクトリを指定します。 既定値:<br /><br /> 64 ビットの WOW モード: `%Program Files(x86)%\Microsoft SQL Server\`<br /><br /> 他のすべてのインストール: `%Program Files%\Microsoft SQL Server\`|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SAPWD<br /><br /> **/SECURITYMODE=SQL の場合に必須**|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] **SA** アカウントのパスワードを指定します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SECURITYMODE<br /><br /> **省略可能**|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のセキュリティ モードを指定します。<br /><br /> このパラメーターを指定しない場合、Windows 限定の認証モードがサポートされます。<br /><br /> サポートされる値:**SQL**|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLBACKUPDIR<br /><br /> **省略可能**|バックアップ ファイルのディレクトリを指定します。<br /><br /> 既定値:`<InstallSQLDataDir>\<SQLInstanceID>\MSSQL\Backup`|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLCOLLATION<br /><br /> **省略可能**|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]の照合順序の設定を指定します。<br /><br /> 既定のインストール設定はオペレーティング システム (OS) ロケールによって決定されます。 サーバーレベルの照合順序はセットアップ中に変更するか、インストール前に OS ロケールを変更することで変更できます。 既定の照合順序は、特定のロケール別に関連付けられている中で最も古いバージョンに設定されます。 これは下位互換性によるものです。 そのため、これが常に推奨される照合順序になるとは限りません。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の機能を活用するには、Windows 照合順序を使用するように既定のインストール設定を変更します。 たとえば、OS のロケールが **英語 (米国)** (コード ページ 1252) の場合、セットアップ中、既定の照合順序は **SQL_Latin1_General_CP1_CI_AS** になります。これは Windows 照合順序でそれに最も近い **Latin1_General_100_CI_AS_SC** に変更できます。 <br /><br />詳細については、「[照合順序と Unicode のサポート](../../relational-databases/collations/collation-and-unicode-support.md)」を参照してください。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/ADDCURRENTUSERASSQLADMIN<br /><br /> **省略可能**|現在のユーザーを [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] **sysadmin** 固定サーバー ロールに追加します。 /ADDCURRENTUSERASSQLADMIN パラメーターは、Express エディションをインストールする場合、または /Role=ALLFeatures_WithDefaults が指定されている場合に使用できます。 詳細については、後述の /ROLE をご覧ください。<br /><br /> /ADDCURRENTUSERASSQLADMIN の使用はオプションですが、/ADDCURRENTUSERASSQLADMIN または /SQLSYSADMINACCOUNTS のどちらかを指定する必要があります。 既定値:<br /><br /> **のエディション:** True [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)]<br /><br /> 他のすべてのエディション:**False**|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLSVCACCOUNT<br /><br /> **必須**|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] サービスの開始アカウントを指定します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLSVCPASSWORD<br /><br /> [必須](#Accounts)|SQLSVCACCOUNT のパスワードを指定します。 管理されたサービス アカウント、仮想アカウント、またはビルトイン アカウントの使用時、このパラメーターは省略できます。 |  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLSVCSTARTUPTYPE<br /><br /> **省略可能**|[サービスの](#Accounts) スタートアップ [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] モードを指定します。 サポートされる値:<br /><br /> **自動**<br /><br /> **Disabled**<br /><br /> **手動**|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLSYSADMINACCOUNTS<br /><br /> **必須**|sysadmin ロールのメンバーになるためにログインを準備するには、このパラメーターを使用します。<br /><br /> [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 以外の [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] のエディションの場合、/SQLSYSADMINACCOUNTS は必須です。 [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)]のエディションの場合、/SQLSYSADMINACCOUNTS の使用はオプションですが、/SQLSYSADMINACCOUNTS または /ADDCURRENTUSERASSQLADMIN のどちらかを指定する必要があります。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBDIR<br /><br /> **省略可能**|tempdb データ ファイルのディレクトリを指定します。 複数のディレクトリを指定する場合、各ディレクトリを空白で区切ります。 複数のディレクトリが指定されている場合、tempdb データ ファイルはラウンド ロビン形式ですべてのディレクトリにまたがるようになります。<br /><br /> 既定値:`<InstallSQLDataDir>\<SQLInstanceID>\MSSQL\Data` (システム データ ディレクトリ)<br /><br /> **注:** このパラメーターは、RebuildDatabase シナリオにも追加されます。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBLOGDIR<br /><br /> **省略可能**|tempdb のログ ファイルのディレクトリを指定します。<br /><br /> 既定値:`<InstallSQLDataDir>\<SQLInstanceID>\MSSQL\Data` (システム データ ディレクトリ)<br /><br /> 注:このパラメーターは、RebuildDatabase シナリオにも追加されます。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBFILECOUNT<br /><br /> **省略可能**|セットアップで追加する tempdb データ ファイルの数を指定します。 この値はコアの数まで増やすことができます。 既定値:<br /><br /> [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)]: 1<br /><br /> 他のすべてのエディション: 8 またはコアの数のうち、小さい方の値<br /><br /> **重要** tempdb のプライマリ データベース ファイルは引き続き tempdb.mdf になります。 その他の tempdb ファイルには、tempdb_mssql_#.ndf という名前が付けられます。# は、セットアップ中に作成されたその他の各 tempdb データベース ファイルの一意の番号を表します。 この名前付け規則は、各データベース ファイルを一意にすることを目的としています。 SQL Server のインスタンスをアンインストールすると、tempdb_mssql_#.ndf 名前付け規則を使用するファイルが削除されます。 ユーザー データベース ファイルには tempdb_mssql_\*.ndf 名前付け規則を使用しないでください。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBFILESIZE<br /><br /> **省略可能**|[!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] で導入されました。 各 tempdb データ ファイルの初期サイズを指定します。<br/><br/>既定値 = [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] の場合は 4 MB、その他のエディションの場合は 8 MB。<br/><br/>最小値 = (4 MB または 8 MB)。<br/><br/>最大値 = 1024 MB ([!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] の場合は 262,144 MB)。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBFILEGROWTH<br /><br /> **省略可能**|各 tempdb データ ファイルのファイル拡張増分値を MB 単位で指定します。 0 は、自動拡張がオフで、領域を追加できないことを示します。 最大 1024 のサイズまで指定できます。<br /><br /> 既定値:64。 許容範囲: 最小値 = 0、最大値 = 1024|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBLOGFILESIZE<br /><br /> **省略可能**|[!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] で導入されました。 各 tempdb ログ ファイルの初期サイズを指定します。<br/><br/>既定値 = [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] の場合は 4 MB、その他のエディションの場合は 8 MB。<br/><br/>最小値 = (4 MB または 8 MB)。<br/><br/>最大値 = 1024 MB ([!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] の場合は 262,144 MB)。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBLOGFILEGROWTH<br /><br /> **省略可能**|各 tempdb データ ファイルのファイル拡張増分値を MB 単位で指定します。 0 は、自動拡張がオフで、領域を追加できないことを示します。 最大 1024 のサイズまで指定できます。<br /><br /> 既定値:64。 許容範囲: 最小値 = 0、最大値 = 1024|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLUSERDBDIR<br /><br /> **省略可能**|ユーザー データベースのデータ ファイルのディレクトリを指定します。<br /><br /> 既定値:`<InstallSQLDataDir>\<SQLInstanceID>\MSSQL\Data`|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLSVCINSTANTFILEINIT<br /><br /> **省略可能**|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] サービス アカウントのファイルの瞬時初期化を有効にします。 セキュリティとパフォーマンスに関する考慮事項については、「 [データベースのファイルの瞬時初期化](../../relational-databases/databases/database-instant-file-initialization.md)」をご覧ください。<br /><br /> 既定値:"False"<br /><br /> 省略可能な値:"True"|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLUSERDBLOGDIR<br /><br /> **省略可能**|ユーザー データベースのログ ファイルのディレクトリを指定します。<br /><br /> 既定値:`<InstallSQLDataDir>\<SQLInstanceID>\MSSQL\Data`|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLMAXDOP=parameter <br /><br /> **省略可能** <br /> 無人 (サイレント) インストール時に省略した場合、MAXDOP は[並列処理の最大限度のガイドライン](../../database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option.md#Guidelines)に従います。 |並列処理の最大限度を指定します。これにより、1 つのステートメントの実行中に 1 つのステートメントで使用できるプロセッサの数が決まります。 [!INCLUDE[sql-server-2019](../../includes/sssql19-md.md)] 以降でのみ使用できます。 <br /><br /> 既定値は、[max degree of parallelism のガイドライン](../../database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option.md#Guidelines)に従います。|
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/USESQLRECOMMENDEDMEMORYLIMITS<br /><br /> **省略可能** <br /> 無人 (サイレント) インストールで /USESQLRECOMMENDEDMEMORYLIMITS、/SQLMINMEMORY、/SQLMAXMEMORY を省略した場合、[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] では [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の既定のメモリ構成が使用されます。|スタンドアロン [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンスの[サーバー メモリ構成ガイドライン](../../database-engine/configure-windows/server-memory-server-configuration-options.md#manually)と一致する計算された推奨値を [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] で使用することを指定します。 [!INCLUDE[sql-server-2019](../../includes/sssql19-md.md)] 以降でのみ使用できます。<br /><br /> **注:** このパラメーターを /SQLMINMEMORY および /SQLMAXMEMORY と共に使用することはできません。 |  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLMINMEMORY<br /><br /> **省略可能** <br /> 無人 (サイレント) インストールで /USESQLRECOMMENDEDMEMORYLIMITS、/SQLMINMEMORY、/SQLMAXMEMORY を省略した場合、[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] では [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の既定のメモリ構成が使用されます。|Min Server Memory 構成を MB 単位で指定します。 [!INCLUDE[sql-server-2019](../../includes/sssql19-md.md)] 以降でのみ使用できます。<br /><br /> 既定値:0。<br /><br /> **注:** このパラメーターを /USESQLRECOMMENDEDMEMORYLIMITS と共に使用することはできません。 |  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLMAXMEMORY<br /><br /> **省略可能** <br /> 無人 (サイレント) インストールで /USESQLRECOMMENDEDMEMORYLIMITS、/SQLMINMEMORY、/SQLMAXMEMORY を省略した場合、[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] では [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の既定のメモリ構成が使用されます。|Max Server Memory 構成を MB 単位で指定します。 [!INCLUDE[sql-server-2019](../../includes/sssql19-md.md)] 以降でのみ使用できます。<br /><br /> 既定値: スタンドアロン [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンスの[サーバー メモリ構成ガイドライン](../../database-engine/configure-windows/server-memory-server-configuration-options.md#manually)と一致する計算された推奨値。<br /><br /> **注:** このパラメーターを /USESQLRECOMMENDEDMEMORYLIMITS と共に使用することはできません。 |  
|FILESTREAM|/FILESTREAMLEVEL<br /><br /> **省略可能**|FILESTREAM 機能のアクセス レベルを指定します。 サポートされる値:<br /><br /> 0 = [このインスタンスに対する FILESTREAM サポートを無効にする] (既定値)<br /><br /> 1 = [ [!INCLUDE[tsql](../../includes/tsql-md.md)] アクセスに対して FILESTREAM を有効にする]<br /><br /> 2 = [ [!INCLUDE[tsql](../../includes/tsql-md.md)] アクセスおよびファイル I/O ストリーム アクセスに対して FILESTREAM を有効にする] (クラスター シナリオに対しては無効です)<br /><br /> 3 = [リモート クライアントに FILESTREAM データへのストリーム アクセスを許可する]|  
|FILESTREAM|/FILESTREAMSHARENAME<br /><br /> **省略可能**<br /><br /> **FILESTREAMLEVEL が 1 より大きい場合は必須。**|FILESTREAM データを格納する Windows 共有の名前を指定します。|  
|SQL Server フルテキスト|/FTSVCACCOUNT<br /><br /> **省略可能**|フルテキスト フィルター ランチャー サービスのアカウントを指定します。<br /><br /> このパラメーターは、[!INCLUDE[firstref_longhorn](../../includes/firstref-longhorn-md.md)] 以降では無視されます。 ServiceSID は、SQL Server と Full-text Filter Daemon 間の通信を確立するのに使用されます。 この値を指定しない場合、フルテキスト フィルター ランチャー サービスが無効になります。 サービス アカウントを変更し、フルテキスト機能を有効にするには、SQL Server コントロール マネージャーを使用する必要があります。<br /><br /> 既定値:ローカル サービス アカウント|  
|SQL Server フルテキスト|/FTSVCPASSWORD<br /><br /> **省略可能**|フルテキスト フィルター ランチャー サービスのパスワードを指定します。<br /><br /> このパラメーターは、[!INCLUDE[firstref_longhorn](../../includes/firstref-longhorn-md.md)] 以降では無視されます。 管理されたサービス アカウント、仮想アカウント、またはビルトイン アカウントの使用時、このパラメーターは省略できます。|  
|[!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)]|/ISSVCACCOUNT<br /><br /> **必須**|[!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)]のアカウントを指定します。<br /><br /> 既定値:NT AUTHORITY\NETWORK SERVICE|  
|[!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)]|/ISSVCPASSWORD<br /><br /> [必須](#Accounts)|[!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] のパスワードを指定します。 管理されたサービス アカウント、仮想アカウント、またはビルトイン アカウントの使用時、このパラメーターは省略できます。|  
|[!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)]|/ISSVCStartupType<br /><br /> **省略可能**|[サービスの](#Accounts) スタートアップ [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] モードを指定します。|  
|SQL Server のネットワーク構成|/NPENABLED<br /><br /> **省略可能**|SQL Server サービスの名前付きパイプ プロトコルの状態を指定します。 サポートされる値:<br /><br /> 0 = [名前付きパイプ プロトコルを無効にする]<br /><br /> 1 = [名前付きパイプ プロトコルを有効にする]|  
|SQL Server のネットワーク構成|/TCPENABLED<br /><br /> **省略可能**|SQL Server サービスの TCP プロトコルの状態を指定します。 サポートされる値:<br /><br /> 0 = [TCP プロトコルを無効にする]<br /><br /> 1 = [TCP プロトコルを有効にする]|  
|[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]|/RSINSTALLMODE<br /><br /> **省略可能**| SQL Server 2017 以降では適用できなくなりました。  [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]のインストール モードを指定します。 サポートされる値:<br /><br /> **SharePointFilesOnlyMode**<br /><br /> **DefaultNativeMode**<br /><br /> **FilesOnlyMode**<br /><br /> <br /><br /> 注:インストールに SQL Server[!INCLUDE[ssDE](../../includes/ssde-md.md)]が含まれている場合、既定の RSINSTALLMODE は DefaultNativeMode になります。<br /><br /> インストールに SQL Server[!INCLUDE[ssDE](../../includes/ssde-md.md)]が含まれていない場合、既定の RSINSTALLMODE は FilesOnlyMode になります。<br /><br /> DefaultNativeMode を選択しても、インストールに SQL Server[!INCLUDE[ssDE](../../includes/ssde-md.md)]が含まれていない場合、RSINSTALLMODE は自動的に FilesOnlyMode に変更されます。|  
|[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]|/RSSVCACCOUNT<br /><br /> **必須**| SQL Server 2017 以降では適用できなくなりました。  [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]の開始アカウントを指定します。 |  
|[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]|/RSSVCPASSWORD<br /><br /> [必須](#Accounts)|SQL Server 2017 以降では適用できなくなりました。  [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] サービスの開始アカウントのパスワードを指定します。 管理されたサービス アカウント、仮想アカウント、またはビルトイン アカウントの使用時、このパラメーターは省略できます。|  
|[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]|/RSSVCStartupType<br /><br /> **省略可能**|SQL Server 2017 以降では適用できなくなりました。  [の](#Accounts) スタートアップ [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]モードを指定します。 サポートされる値:<br /><br /> **自動**<br /><br /> **Disabled**<br /><br /> **手動**|  
|Python/Machine Learning Services (データベース内)|/MPYCACHEDIRECTORY|将来利用するために予約されています。 インターネットに接続されていないコンピューター上にインストール用の Python .CAB ファイルを格納するには %TEMP% を使用します。 |  
|R/Machine Learning Services (データベース内)|/MRCACHEDIRECTORY|このパラメーターを使用して、SQL Server Machine Learning Services または Machine Learning Server (スタンドアロン) で Microsoft R Open、SQL Server 2016 R Services、SQL Server 2016 R Server (スタンドアロン)、または R の機能をサポートするためのキャッシュ ディレクトリを指定します。 通常、この設定を使用するのは、[インターネット アクセスを使用していないコンピューターでコマンド ラインから](../../machine-learning/install/sql-ml-component-install-without-internet-access.md) R コンポーネントをインストールする場合です。|  
|Java/言語拡張機能| /SQL_INST_JAVA,<br /> /SQLJAVADIR = "パス"<br /><br /> **省略可能** | SQL Server 2019 以降、言語拡張機能と共に Java をインストールすることを指定します。 /SQLJAVADIR パラメーターなしで /SQL_INST_JAVA を指定した場合は、インストール メディアで提供されている Zulu Open JRE をインストールすることが想定されます。 <br /><br /> /SQLJAVADIR のパスを指定すると、既にインストールされている JRE または JDK を使用することができます。 |
  
###### <a name="sample-syntax"></a>サンプル構文:  
 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]、レプリケーション、フルテキスト検索の各コンポーネントが配置された新しいスタンドアロン インスタンスをインストールし、 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]のファイルの瞬時初期化を有効にするには、次の構文を使用します。 
  
```  
setup.exe /q /ACTION=Install /FEATURES=SQL /INSTANCENAME=MSSQLSERVER /SQLSVCACCOUNT="<DomainName\UserName>" /SQLSVCPASSWORD="<StrongPassword>" /SQLSYSADMINACCOUNTS="<DomainName\UserName>" /AGTSVCACCOUNT="NT AUTHORITY\Network Service" /SQLSVCINSTANTFILEINIT="True" /IACCEPTSQLSERVERLICENSETERMS  
```  
  
##  <a name="sysprep-parameters"></a><a name="SysPrep"></a> SysPrep パラメーター  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] SysPrep の詳細については、次を参照してください  
  
 [SysPrep を使用して [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] をインストールする](../../database-engine/install-windows/install-sql-server-using-sysprep.md) 
  
#### <a name="prepare-image-parameters"></a>イメージの準備パラメーター  
 次の表に示すパラメーターは、SQL Server のインスタンスを準備する (構成は行わない) ためのコマンド ライン スクリプトを作成する場合に使用します。 
  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] コンポーネント|パラメーター|説明|  
|-----------------------------------------|---------------|-----------------|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップ コントロール|/ACTION<br /><br /> **必須**|インストール ワークフローを示すために必要です。<br /><br /> サポートされる値:**PrepareImage**|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップ コントロール|/IACCEPTSQLSERVERLICENSETERMS<br /><br /> **自動インストールのために /Q パラメーターまたは /QS パラメーターを指定した場合にのみ必須です。**|ライセンス条項への同意を確認するために必要です。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップ コントロール|/ENU<br /><br /> **省略可能**|ローカライズされたオペレーティング システムに SQL Server の英語版をインストールする場合に、オペレーティング システムに対応する言語と英語の両方の言語パックがインストール メディアに含まれているときは、このパラメーターを使用します。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップ コントロール|/UpdateEnabled<br /><br /> **省略可能**|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップが製品の更新プログラムを検出し、それらを含める必要があるかどうかを指定します。 有効値は True および False または 1 および 0 です。 既定では、SQL Server セットアップには検出された更新プログラムが含まれます。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップ コントロール|/UpdateSource<br /><br /> **省略可能**|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップが製品の更新プログラムを取得する場所を指定します。 有効値は [!INCLUDE[msCoName](../../includes/msconame-md.md)] Update を検索する "MU"、有効なフォルダー パス、相対パス (`.\MyUpdates` など)、または UNC 共有です。 既定では、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップは [!INCLUDE[msCoName](../../includes/msconame-md.md)] Update、または Windows Server Update Services を介して Windows Update Service を検索します。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップ コントロール|/CONFIGURATIONFILE<br /><br /> **省略可能**|使用する [ConfigurationFile](./install-sql-server-using-a-configuration-file.md) を指定します。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップ コントロール|/FEATURES<br /><br /> **必須**|インストールする [コンポーネント](#Feature) を指定します。<br /><br /> サポートされる値: SQLEngine、Replication、FullText、DQ、AS、AS_SPI、RS、RS_SHP、RS_SHPWFE、DQC、Conn、IS、BC、SDK、DREPLAY_CTLR、DREPLAY_CLT、SNAC_SDK、SQLODBC、SQLODBC_SDK、LocalDB、MDS、POLYBASE|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップ コントロール|/HELP、?<br /><br /> **省略可能**|インストール パラメーターの使用方法を表示します。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップ コントロール|/HIDECONSOLE<br /><br /> **省略可能**|コンソール ウィンドウを非表示にするか閉じる場合に指定します。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップ コントロール|/INDICATEPROGRESS<br /><br /> **省略可能**|詳細なセットアップ ログ ファイルがコンソールにパイプされるように指定します。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップ コントロール|/INSTALLSHAREDDIR<br /><br /> **省略可能**|共有コンポーネント (64 ビット) の既定以外のインストール ディレクトリを指定します。<br /><br /> 既定値は `%Program Files%\Microsoft SQL Server` です<br /><br /> `%Program Files(x86)%\Microsoft SQL Server` に設定することはできません|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップ コントロール|/INSTANCEDIR<br /><br /> **省略可能**|インスタンス専用のコンポーネントについて既定以外のインストール ディレクトリを指定します。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップ コントロール|/INSTANCEID<br /><br /> [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)]SP1 CU2 (2013 年 1 月) 以前が **必要**<br /><br /> インスタンス機能には [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] SP1 CU2 **が必要**|準備中のインスタンスのインスタンス ID を指定します。|  
|PolyBase|/PBENGSVCACCOUNT<br /><br /> **省略可能**|エンジン サービスのアカウントを指定します。 既定値は、 **NT Authority\NETWORK SERVICE** です。|  
|PolyBase|/PBDMSSVCPASSWORD<br /><br /> **省略可能**|エンジン サービス アカウントのパスワードを指定します。 管理されたサービス アカウント、仮想アカウント、またはビルトイン アカウントの使用時、このパラメーターは省略できます。|  
|PolyBase|/PBENGSVCSTARTUPTYPE<br /><br /> **省略可能**|PolyBase エンジン サービスのスタートアップ モードを指定します Automatic (既定値)、Disabled、Manual。|  
|PolyBase|/PBPORTRANGE<br /><br /> **省略可能**|PolyBase サービスのポート範囲 (6 ポート以上) を指定します。 例:<br /><br /> `/PBPORTRANGE=16450-16460`|  
|PolyBase|/PBSCALEOUT<br /><br /> **省略可能**|PolyBase スケールアウト計算グループの一部として [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] インスタンスを使用するかどうかを指定します。 サポートされる値:**True**、**False**|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップ コントロール|/Q または /QUIET <br /><br /> **省略可能**|セットアップが、ユーザー インターフェイスなしで、非表示モードで実行されるように指定します。 このパラメーターは、自動インストールに使用されます。 /Q パラメーターによって /QS パラメーターの入力がオーバーライドされます。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップ コントロール|/QS または /QUIETSIMPLE<br /><br /> **省略可能**|セットアップが UI を使用して実行され、UI を使用して進捗状況が表示されるように指定します。さらに、セットアップで入力ができないように、またはエラー メッセージが表示されないように指定します。|  
  
###### <a name="sample-syntax"></a>サンプル構文:  
 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]、レプリケーション、フルテキスト検索の各コンポーネントおよび [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]が配置された新しいスタンドアロン インスタンスを準備するには 
  
```  
setup.exe /q /ACTION=PrepareImage /FEATURES=SQL,RS /InstanceID =<MYINST> /IACCEPTSQLSERVERLICENSETERMS  
```  
  
#### <a name="complete-image-parameters"></a>イメージの完了パラメーター  
 次の表に示すパラメーターは、準備された SQL Server のインスタンスを完了および構成するためのコマンド ライン スクリプトを作成する場合に使用します。 
  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] コンポーネント|パラメーター|説明|  
|-----------------------------------------|---------------|-----------------|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップ コントロール|/ACTION<br /><br /> **必須**|インストール ワークフローを示すために必要です。<br /><br /> サポートされる値:**CompleteImage**|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップ コントロール|/IACCEPTSQLSERVERLICENSETERMS<br /><br /> **自動インストールのために /Q パラメーターまたは /QS パラメーターを指定した場合にのみ必須です。**|ライセンス条項への同意を確認するために必要です。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップ コントロール|/ENU<br /><br /> **省略可能**|ローカライズされたオペレーティング システムに SQL Server の英語版をインストールする場合に、オペレーティング システムに対応する言語と英語の両方の言語パックがインストール メディアに含まれているときは、このパラメーターを使用します。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップ コントロール|/CONFIGURATIONFILE<br /><br /> **省略可能**|使用する [ConfigurationFile](./install-sql-server-using-a-configuration-file.md) を指定します。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップ コントロール|/ERRORREPORTING<br /><br /> **省略可能**|[!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] には影響しません。 <br/><br/>エラーのフィードバックを Microsoft に送信する方法を管理するには、「[フィードバックをマイクロソフトに送信するように [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] を構成する方法](https://support.microsoft.com/kb/3153756)」を参照してください。 <br/><br/>以前のバージョンでは、これにより SQL Server のエラー報告が指定されます。<br /><br /> 詳細については、「 [Microsoft エラー報告サービスのプライバシーに関する声明](../../sql-server/sql-server-privacy.md)」を参照してください。 サポートされる値:<br /><br /> 1 = 有効<br /><br /> 0 = 無効|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップ コントロール|/HELP、?<br /><br /> **省略可能**|インストール パラメーターの使用方法を表示します。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップ コントロール|/INDICATEPROGRESS<br /><br /> **省略可能**|詳細なセットアップ ログ ファイルがコンソールにパイプされるように指定します。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップ コントロール|/INSTANCEID<br /><br /> [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)]SP1 CU2 (2013 年 1 月) 以前が **必要**<br /><br /> [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] SP1 CU2 **は省略可**|イメージの準備手順で指定されたインスタンス ID を使用します。<br /><br /> サポートされる値:準備されたインスタンスのインスタンス ID。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップ コントロール|/INSTANCENAME<br /><br /> [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)]SP1 CU2 (2013 年 1 月) 以前が **必要**<br /><br /> [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] SP1 CU2 **は省略可**|完了させるインスタンスの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンス名を指定します。<br /><br /> 詳細については、「 [Instance Configuration](../../sql-server/install/instance-configuration.md)」を参照してください。|  
|PolyBase|/PBENGSVCACCOUNT<br /><br /> **省略可能**|エンジン サービスのアカウントを指定します。 既定値は、 **NT Authority\NETWORK SERVICE** です。|  
|PolyBase|/PBDMSSVCPASSWORD<br /><br /> **省略可能**|エンジン サービス アカウントのパスワードを指定します。 管理されたサービス アカウント、仮想アカウント、またはビルトイン アカウントの使用時、このパラメーターは省略できます。|  
|PolyBase|/PBENGSVCSTARTUPTYPE<br /><br /> **省略可能**|PolyBase エンジン サービスのスタートアップ モードを指定します (Automatic (既定)、Disabled、Manual)。|  
|PolyBase|/PBPORTRANGE<br /><br /> **省略可能**|PolyBase サービスのポート範囲 (6 ポート以上) を指定します。 例:<br /><br /> `/PBPORTRANGE=16450-16460`|  
|PolyBase|/PBSCALEOUT<br /><br /> **省略可能**|PolyBase スケールアウト計算グループの一部として [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] インスタンスを使用するかどうかを指定します。 サポートされる値:**True**、**False**|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップ コントロール|/PID<br /><br /> **省略可能**|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のエディション用のプロダクト キーを指定します。 このパラメーターが指定されていない場合、Evaluation が使用されます。<br /><br /> **注:** [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)]、[!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] with Tools、または [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] with Advanced Services をインストールする場合、PID は事前に定義されます。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップ コントロール|/Q または /QUIET <br /><br /> **省略可能**|セットアップが、ユーザー インターフェイスなしで、非表示モードで実行されるように指定します。 このパラメーターは、自動インストールに使用されます。 /Q パラメーターによって /QS パラメーターの入力がオーバーライドされます。 |  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/QS または /QUIETSIMPLE<br /><br /> **省略可能**|セットアップが UI を使用して実行され、UI を使用して進捗状況が表示されるように指定します。さらに、セットアップで入力ができないように、またはエラー メッセージが表示されないように指定します。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップ コントロール|/SQMREPORTING<br /><br /> **省略可能**|[!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] には影響しません。 <br/><br/>エラーのフィードバックを Microsoft に送信する方法を管理するには、「[フィードバックをマイクロソフトに送信するように [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] を構成する方法](https://support.microsoft.com/kb/3153756)」を参照してください。 <br/><br/>以前のバージョンでは、これにより SQL Server の機能の使用状況レポートが指定されます。<br /><br />サポートされる値:<br /><br /> 0 = 無効<br /><br /> 1 = 有効|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップ コントロール|/HIDECONSOLE<br /><br /> **省略可能**|コンソール ウィンドウを非表示にするか閉じる場合に指定します。|  
|SQL Server エージェント|/AGTSVCACCOUNT<br /><br /> **必須**|SQL Server エージェント サービスのアカウントを指定します。|  
|SQL Server エージェント|/AGTSVCPASSWORD<br /><br /> [必須](#Accounts)|SQL Server エージェント サービス アカウントのパスワードを指定します。 管理されたサービス アカウント、仮想アカウント、またはビルトイン アカウントの使用時、このパラメーターは省略できます。|  
|SQL Server エージェント|/AGTSVCSTARTUPTYPE<br /><br /> **省略可能**|SQL Server エージェント サービスの [スタートアップ](#Accounts) モードを指定します。 サポートされる値:<br /><br /> **自動**<br /><br /> **Disabled**<br /><br /> **手動**|  
|SQL Server Browser|/BROWSERSVCSTARTUPTYPE<br /><br /> **省略可能**|SQL Server Browser サービスの [スタートアップ](#Accounts) モードを指定します。 サポートされる値:<br /><br /> **自動**<br /><br /> **Disabled**<br /><br /> **手動**|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/ENABLERANU<br /><br /> **省略可能**|[!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] のインストールの実行アカウント資格情報を有効にします。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/INSTALLSQLDATADIR<br /><br /> **省略可能**|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] データ ファイルのデータ ディレクトリを指定します。 既定値:<br /><br /> 64 ビットの WOW モード: `%Program Files(x86)%\Microsoft SQL Server\`<br /><br /> 他のすべてのインストール: `%Program Files%\Microsoft SQL Server\`|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SAPWD<br /><br /> **/SECURITYMODE=SQL の場合に必須**|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] **SA** アカウントのパスワードを指定します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SECURITYMODE<br /><br /> **省略可能**|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のセキュリティ モードを指定します。<br /><br /> このパラメーターを指定しない場合、Windows 限定の認証モードがサポートされます。<br /><br /> サポートされる値:**SQL**|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLBACKUPDIR<br /><br /> **省略可能**|バックアップ ファイルのディレクトリを指定します。<br /><br /> 既定値:<br /><br /> `<InstallSQLDataDir>\<SQLInstanceID>\MSSQL\Backup`|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLCOLLATION<br /><br /> **省略可能**|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]の照合順序の設定を指定します。<br /><br /> 既定値は、Windows オペレーティング システムのロケールに基づいています。 詳細については、「 [セットアップでの照合順序の設定](../../relational-databases/collations/collation-and-unicode-support.md)」を参照してください。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLSVCACCOUNT<br /><br /> **必須**|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] サービスの開始アカウントを指定します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLSVCPASSWORD<br /><br /> [必須](#Accounts)|SQLSVCACCOUNT のパスワードを指定します。 管理されたサービス アカウント、仮想アカウント、またはビルトイン アカウントの使用時、このパラメーターは省略できます。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLSVCSTARTUPTYPE<br /><br /> **省略可能**|[サービスの](#Accounts) スタートアップ [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] モードを指定します。 サポートされる値:<br /><br /> **自動**<br /><br /> **Disabled**<br /><br /> **手動**|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLSYSADMINACCOUNTS<br /><br /> **必須**|sysadmin ロールのメンバーになるためにログインを準備するには、このパラメーターを使用します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBDIR<br /><br /> **省略可能**|tempdb データ ファイルのディレクトリを指定します。 複数のディレクトリを指定する場合、各ディレクトリを空白で区切ります。 複数のディレクトリが指定されている場合、tempdb データ ファイルはラウンド ロビン形式ですべてのディレクトリにまたがるようになります。<br /><br /> 既定値:`<InstallSQLDataDir>\<SQLInstanceID>\MSSQL\Data` (システム データ ディレクトリ)<br /><br /> 注:このパラメーターは、RebuildDatabase シナリオにも追加されます。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBLOGDIR<br /><br /> **省略可能**|tempdb のログ ファイルのディレクトリを指定します。<br /><br /> 既定値:`<InstallSQLDataDir>\<SQLInstanceID>\MSSQL\Data` (システム データ ディレクトリ)<br /><br /> 注:このパラメーターは、RebuildDatabase シナリオにも追加されます。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBFILESIZE<br /><br /> **省略可能**|[!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] で導入されました。 各 tempdb データ ファイルの初期サイズを指定します。<br/><br/>既定値 = [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] の場合は 4 MB、その他のエディションの場合は 8 MB。<br/><br/>最小値 = (4 MB または 8 MB)。<br/><br/>最大値 = 1024 MB ([!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] の場合は 262,144 MB)。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBFILEGROWTH<br /><br /> **省略可能**|各 tempdb データ ファイルのファイル拡張増分値を MB 単位で指定します。 0 は、自動拡張がオフで、領域を追加できないことを示します。 最大 1024 のサイズまで指定できます。<br /><br /> 既定値:64<br /><br /> 許容範囲: 最小値 = 0、最大値 = 1024|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBLOGFILESIZE<br /><br /> **省略可能**|tempdb ログ ファイルの初期サイズを MB 単位で指定します。 最大 1024 のサイズまで指定できます。<br /><br /> 既定値:<br /><br /> [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)]: 4<br /><br /> 他のすべてのエディション: 8<br /><br /> 許容範囲: 最小値 = 既定値 (4 または 8)、最大値 = 1024|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBLOGFILEGROWTH<br /><br /> **省略可能**|[!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] で導入されました。 各 tempdb ログ ファイルの初期サイズを指定します。<br/><br/>既定値 = [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] の場合は 4 MB、その他のエディションの場合は 8 MB。<br/><br/>最小値 = (4 MB または 8 MB)。<br/><br/>最大値 = 1024 MB ([!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] の場合は 262,144 MB)。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBFILECOUNT<br /><br /> **省略可能**|セットアップで追加する tempdb データ ファイルの数を指定します。 この値はコアの数まで増やすことができます。 既定値:<br /><br /> [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)]: 1<br /><br /> 他のすべてのエディション: 8 またはコアの数のうち、小さい方の値<br /><br /> **重要:** tempdb のプライマリ データベース ファイルは引き続き tempdb.mdf になります。 その他の tempdb ファイルには、tempdb_mssql_#.ndf という名前が付けられます。# は、セットアップ中に作成されたその他の各 tempdb データベース ファイルの一意の番号を表します。 この名前付け規則は、各データベース ファイルを一意にすることを目的としています。 SQL Server のインスタンスをアンインストールすると、tempdb_mssql_#.ndf 名前付け規則を使用するファイルが削除されます。 ユーザー データベース ファイルには tempdb_mssql_\*.ndf 名前付け規則を使用しないでください。<br /><br /> **警告:** このパラメーターの構成では [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] はサポートされていません。 セットアップでインストールされる tempdb データ ファイルは 1 つだけです。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLUSERDBDIR<br /><br /> **省略可能**|ユーザー データベースのデータ ファイルのディレクトリを指定します。<br /><br /> 既定値:`<InstallSQLDataDir>\<SQLInstanceID>\MSSQL\Data`|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLUSERDBLOGDIR<br /><br /> **省略可能**|ユーザー データベースのログ ファイルのディレクトリを指定します。<br /><br /> 既定値:`<InstallSQLDataDir>\<SQLInstanceID>\MSSQL\Data`|  
|FILESTREAM|/FILESTREAMLEVEL<br /><br /> **省略可能**|FILESTREAM 機能のアクセス レベルを指定します。 サポートされる値:<br /><br /> 0 = [このインスタンスに対する FILESTREAM サポートを無効にする] (既定値)<br /><br /> 1 = [ [!INCLUDE[tsql](../../includes/tsql-md.md)] アクセスに対して FILESTREAM を有効にする]<br /><br /> 2 = [ [!INCLUDE[tsql](../../includes/tsql-md.md)] アクセスおよびファイル I/O ストリーム アクセスに対して FILESTREAM を有効にする] (クラスター シナリオに対しては無効です)<br /><br /> 3 = [リモート クライアントに FILESTREAM データへのストリーム アクセスを許可する]|  
|FILESTREAM|/FILESTREAMSHARENAME<br /><br /> **省略可能**<br /><br /> **FILESTREAMLEVEL が 1 より大きい場合は必須。**|FILESTREAM データを格納する Windows 共有の名前を指定します。|  
|SQL Server フルテキスト|/FTSVCACCOUNT<br /><br /> **省略可能**|フルテキスト フィルター ランチャー サービスのアカウントを指定します。<br /><br /> このパラメーターは、[!INCLUDE[firstref_longhorn](../../includes/firstref-longhorn-md.md)] 以降では無視されます。 ServiceSID は、SQL Server と Full-text Filter Daemon 間の通信を確立するのに使用されます。 この値を指定しない場合、フルテキスト フィルター ランチャー サービスが無効になります。 サービス アカウントを変更し、フルテキスト機能を有効にするには、SQL Server コントロール マネージャーを使用する必要があります。<br /><br /> 既定値:ローカル サービス アカウント|  
|SQL Server フルテキスト|/FTSVCPASSWORD<br /><br /> **省略可能**|フルテキスト フィルター ランチャー サービスのパスワードを指定します。<br /><br /> このパラメーターは、[!INCLUDE[firstref_longhorn](../../includes/firstref-longhorn-md.md)] 以降では無視されます。 管理されたサービス アカウント、仮想アカウント、またはビルトイン アカウントの使用時、このパラメーターは省略できます。 |  
|SQL Server のネットワーク構成|/NPENABLED<br /><br /> **省略可能**|SQL Server サービスの名前付きパイプ プロトコルの状態を指定します。 サポートされる値:<br /><br /> 0 = [名前付きパイプ プロトコルを無効にする]<br /><br /> 1 = [名前付きパイプ プロトコルを有効にする]|  
|SQL Server のネットワーク構成|/TCPENABLED<br /><br /> **省略可能**|SQL Server サービスの TCP プロトコルの状態を指定します。 サポートされる値:<br /><br /> 0 = [TCP プロトコルを無効にする]<br /><br /> 1 = [TCP プロトコルを有効にする]|  
|[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]|/RSINSTALLMODE<br /><br /> **省略可能**|SQL Server 2017 以降では適用できなくなりました。  [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]のインストール モードを指定します。|  
|[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]|/RSSVCACCOUNT<br /><br /> **必須**|SQL Server 2017 以降では適用できなくなりました。  [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]の開始アカウントを指定します。|  
|[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]|/RSSVCPASSWORD<br /><br /> [必須](#Accounts)| SQL Server 2017 以降では適用できなくなりました。  [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] サービスの開始アカウントのパスワードを指定します。 管理されたサービス アカウント、仮想アカウント、またはビルトイン アカウントの使用時、このパラメーターは省略できます。|  
|[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]|/RSSVCStartupType<br /><br /> **省略可能**|SQL Server 2017 以降では適用できなくなりました。  [の](#Accounts) スタートアップ [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]モードを指定します。 サポートされる値:<br /><br /> **自動**<br /><br /> **Disabled**<br /><br /> **手動**|  
  
###### <a name="sample-syntax"></a>サンプル構文:  
 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]、レプリケーション、フルテキスト検索の各コンポーネントが配置された、準備済みのスタンドアロン インスタンスを完了するには 
  
```  
setup.exe /q /ACTION=CompleteImage /INSTANCENAME=MYNEWINST /INSTANCEID=<MYINST> /SQLSVCACCOUNT="<DomainName\UserName>" /SQLSVCPASSWORD="<StrongPassword>" /SQLSYSADMINACCOUNTS="<DomainName\UserName>" /AGTSVCACCOUNT="NT AUTHORITY\Network Service" /IACCEPTSQLSERVERLICENSETERMS  
```  
  
##  <a name="upgrade-parameters"></a><a name="Upgrade"></a> アップグレード パラメーター  
 次の表に示すパラメーターは、アップグレード用のコマンド ライン スクリプトを作成する場合に使用します。 
  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] コンポーネント|パラメーター|説明|  
|-----------------------------------------|---------------|-----------------|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/ACTION<br /><br /> **必須**|インストール ワークフローを示すために必要です。 サポートされる値:<br /><br /> **アップグレード**<br /><br /> **EditionUpgrade**<br /><br /> <br /><br /> 値 **EditionUpgrade** は、 [!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] の既存のエディションを別のエディションにアップグレードするときに使用します。 サポートされるバージョンとエディションのアップグレードについては、「 [Supported Version and Edition Upgrades](../../database-engine/install-windows/supported-version-and-edition-upgrades.md)」を参照してください。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/IACCEPTSQLSERVERLICENSETERMS<br /><br /> **自動インストールのために /Q パラメーターまたは /QS パラメーターを指定した場合にのみ必須です。**|ライセンス条項への同意を確認するために必要です。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/ENU<br /><br /> **省略可能**|ローカライズされたオペレーティング システムに SQL Server の英語版をインストールする場合に、オペレーティング システムに対応する言語と英語の両方の言語パックがインストール メディアに含まれているときは、このパラメーターを使用します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/*UpdateEnabled*<br /><br /> **省略可能**|SQL Server セットアップが製品の更新プログラムを検出し、それらを含める必要があるかどうかを指定します。 有効値は True および False または 1 および 0 です。 既定では、SQL Server セットアップには検出された更新プログラムが含まれます。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/*UpdateSource*<br /><br /> **省略可能**|SQL Server セットアップが製品の更新プログラムを取得する場所を指定します。 有効値は [!INCLUDE[msCoName](../../includes/msconame-md.md)] Update を検索する "MU"、有効なフォルダー パス、相対パス (.\MyUpdates など)、または UNC 共有です。 既定では、SQL Server セットアップは [!INCLUDE[msCoName](../../includes/msconame-md.md)] Update または Windows Server Update Services を介して Windows Update Service を検索します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/CONFIGURATIONFILE<br /><br /> **省略可能**|使用する [ConfigurationFile](./install-sql-server-using-a-configuration-file.md) を指定します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/ERRORREPORTING<br /><br /> **省略可能**|[!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] には影響しません。 <br/><br/>エラーのフィードバックを Microsoft に送信する方法を管理するには、「[フィードバックをマイクロソフトに送信するように [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] を構成する方法](https://support.microsoft.com/kb/3153756)」を参照してください。 <br/><br/>以前のバージョンでは、これにより SQL Server のエラー報告が指定されます。<br /><br /> 詳細については、「 [Microsoft エラー報告サービスのプライバシーに関する声明](../../sql-server/sql-server-privacy.md)」を参照してください。 サポートされる値:<br /><br /> 1 = 有効<br /><br /> 0 = 無効|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/HELP、?<br /><br /> **省略可能**|パラメーターの使用方法を表示します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/INDICATEPROGRESS<br /><br /> **省略可能**|詳細なセットアップ ログ ファイルがコンソールにパイプされるように指定します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/ INSTANCEDIR<br /><br /> **省略可能**|共有コンポーネントの既定以外のインストール ディレクトリを指定します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/INSTANCEID<br /><br /> **[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降からアップグレードする場合は必須** です。<br /><br /> **次をアップグレードする場合はオプション: [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)]**|[InstanceID](#InstanceID)の既定値以外の値を指定します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/INSTANCENAME<br /><br /> **必須**|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] インスタンス名を指定します。<br /><br /> 詳細については、「 [Instance Configuration](../../sql-server/install/instance-configuration.md)」を参照してください。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/PID<br /><br /> **省略可能**|SQL Server のエディション用のプロダクト キーを指定します。 このパラメーターが指定されていない場合、Evaluation が使用されます。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/Q<br /><br /> **省略可能**|セットアップが、ユーザー インターフェイスなしで、非表示モードで実行されるように指定します。 このパラメーターは、自動インストールに使用されます。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/UIMODE<br /><br /> **省略可能**|セットアップ時に表示するダイアログ ボックスの数を最小限に抑えるかどうかを指定します。 <br />                **/UIMode** は、 **/ACTION=INSTALL** および **UPGRADE** の各パラメーターと共に使用する必要があります。 サポートされる値:<br /><br /> **/UIMODE=Normal** は、Express 以外のエディションの既定値で、選ばれた機能のセットアップ ダイアログ ボックスをすべて表示します。<br /><br /> **/UIMODE=AutoAdvance** は、Express エディションの既定値で、重要でないダイアログ ボックスをスキップします。<br /><br /> **UIMode** 設定は、 **/Q** パラメーターまたは **/QS** パラメーターと共に使用することはできません。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/SQMREPORTING<br /><br /> **省略可能**|[!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] には影響しません。 <br/><br/>エラーのフィードバックを Microsoft に送信する方法を管理するには、「[フィードバックをマイクロソフトに送信するように [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] を構成する方法](https://support.microsoft.com/kb/3153756)」を参照してください。 <br/><br/>以前のバージョンでは、これにより SQL Server の機能の使用状況レポートが指定されます。<br /><br />サポートされる値:<br /><br /> 1 = 有効<br /><br /> 0 = 無効|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/HIDECONSOLE<br /><br /> **省略可能**|コンソール ウィンドウを非表示にするか閉じる場合に指定します。|  
|SQL Server Browser Service|/BROWSERSVCSTARTUPTYPE<br /><br /> **省略可能**|SQL Server Browser サービスの [スタートアップ](#Accounts) モードを指定します。 サポートされる値:<br /><br /> **自動**<br /><br /> **Disabled**<br /><br /> **手動**|  
|SQL Server フルテキスト|/FTUPGRADEOPTION<br /><br /> **省略可能**|フルテキスト カタログのアップグレード オプションを指定します。 サポートされる値:<br /><br /> **REBUILD**<br /><br /> **RESET**<br /><br /> **IMPORT**|  
|[!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)]|/ISSVCACCOUNT<br /><br /> **必須**|[!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)]のアカウントを指定します。<br /><br /> 既定値:NT AUTHORITY\NETWORK SERVICE|  
|[!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)]|/ISSVCPASSWORD<br /><br /> [必須](#Accounts)|[!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] のパスワードを指定します。 管理されたサービス アカウント、仮想アカウント、またはビルトイン アカウントの使用時、このパラメーターは省略できます。|  
|[!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)]|/ISSVCStartupType<br /><br /> **省略可能**|[サービスの](#Accounts) スタートアップ [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] モードを指定します。|  
|[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]|/RSUPGRADEDATABASEACCOUNT<br /><br /> **省略可能**|プロパティは、2008 R2 以前のバージョンの SharePoint モードのレポート サーバーをアップグレードする場合にのみ使用されます。 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] で変更された古い SharePoint モード アーキテクチャを使用するレポート サーバーに対しては、追加のアップグレード操作が実行されます。 このオプションがコマンドライン インストールに含まれていない場合、古いレポート サーバー インスタンス用の既定のサービス アカウントが使用されます。 このプロパティが使用されている場合は、 **/RSUPGRADEPASSWORD** プロパティを使用してアカウントのパスワードを指定します。|  
|[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]|/RSUPGRADEPASSWORD<br /><br /> **省略可能**|既存のレポート サーバー サービス アカウントのパスワード。 管理されたサービス アカウント、仮想アカウント、またはビルトイン アカウントの使用時、このパラメーターは省略できます。|  
|[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]|/ALLOWUPGRADEFORSSRSSHAREPOINTMODE|SharePoint 共有サービス アーキテクチャに基づく SharePoint モードのインストールをアップグレードする場合、このスイッチが必要です。 スイッチは、非共有サービス バージョンの [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]をアップグレードする場合には必要ありません。|  
  
###### <a name="sample-syntax"></a>サンプル構文:  
 既存のインスタンスまたはフェールオーバー クラスター ノードを [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] の以前のバージョンからアップグレードするためのサンプル構文は次のとおりです。  
  
```  
setup.exe /q /ACTION=upgrade /INSTANCEID = <INSTANCEID>/INSTANCENAME=MSSQLSERVER /RSUPGRADEDATABASEACCOUNT="<Provide a SQL Server logon account that can connect to the report server during upgrade>" /RSUPGRADEPASSWORD="<Provide a password for the report server upgrade account>" /ISSVCAccount="NT Authority\Network Service" /IACCEPTSQLSERVERLICENSETERMS  
```  
  
##  <a name="repair-parameters"></a><a name="Repair"></a> 修復パラメーター  
 次の表に示すパラメーターは、修復用のコマンド ライン スクリプトを作成する場合に使用します。 
  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] コンポーネント|パラメーター|説明|  
|-----------------------------------------|---------------|-----------------|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/ACTION<br /><br /> **必須**|修復ワークフローを示すために必要です。<br /><br /> サポートされる値:**修復**|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/ENU<br /><br /> **省略可能**|ローカライズされたオペレーティング システムに [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] の英語版をインストールする場合に、オペレーティング システムに対応する言語と英語の両方の言語パックがインストール メディアに含まれているときは、このパラメーターを使用します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/FEATURES<br /><br /> **必須**|修復する [コンポーネント](#Feature) を指定します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/INSTANCENAME<br /><br /> **必須**|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] インスタンス名を指定します。<br /><br /> 詳細については、「 [Instance Configuration](../../sql-server/install/instance-configuration.md)」を参照してください。|  
|PolyBase|/PBENGSVCACCOUNT<br /><br /> **省略可能**|エンジン サービスのアカウントを指定します。 既定値は、 **NT Authority\NETWORK SERVICE** です。|  
|PolyBase|/PBDMSSVCPASSWORD<br /><br /> **省略可能**|エンジン サービス アカウントのパスワードを指定します。 管理されたサービス アカウント、仮想アカウント、またはビルトイン アカウントの使用時、このパラメーターは省略できます。|  
|PolyBase|/PBENGSVCSTARTUPTYPE<br /><br /> **省略可能**|PolyBase エンジン サービスのスタートアップ モードを指定します (Automatic (既定)、Disabled、Manual)。|  
|PolyBase|/PBPORTRANGE<br /><br /> **省略可能**|PolyBase サービスのポート範囲 (6 ポート以上) を指定します。 例:<br /><br /> `/PBPORTRANGE=16450-16460`|  
|PolyBase|/PBSCALEOUT<br /><br /> **省略可能**|PolyBase スケールアウト計算グループの一部として [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] インスタンスを使用するかどうかを指定します。 サポートされる値:**True**、**False**|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/Q または /QUIET <br /><br /> **省略可能**|セットアップが、ユーザー インターフェイスなしで、非表示モードで実行されるように指定します。 このパラメーターは、自動インストールに使用されます。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/HIDECONSOLE<br /><br /> **省略可能**|コンソール ウィンドウを非表示にするか閉じる場合に指定します。|  
  
###### <a name="sample-syntax"></a>サンプル構文:  
 インスタンスと共有コンポーネントを修復します。 
  
```  
setup.exe /q /ACTION=Repair /INSTANCENAME=<instancename>  
```  
  
##  <a name="rebuild-system-database-parameters"></a><a name="Rebuild"></a> 再構築システム データベース パラメーター  
 次の表に示すパラメーターは、master、model、msdb、および tempdb の各システム データベースを再構築するコマンド ライン スクリプトを作成する場合に使用します。 詳細については、「 [システム データベースの再構築](../../relational-databases/databases/rebuild-system-databases.md)」を参照してください。 
  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] コンポーネント|パラメーター|説明|  
|-----------------------------------------|---------------|-----------------|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/ACTION<br /><br /> **必須**|データベースの再構築に関するワークフローを示すのに必要です。<br /><br /> サポートされる値:**Rebuilddatabase**|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/INSTANCENAME<br /><br /> **必須**|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] インスタンス名を指定します。<br /><br /> 詳細については、「 [Instance Configuration](../../sql-server/install/instance-configuration.md)」を参照してください。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/Q または /QUIET <br /><br /> **省略可能**|セットアップが、ユーザー インターフェイスなしで、非表示モードで実行されるように指定します。 このパラメーターは、自動インストールに使用されます。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLCOLLATION<br /><br /> **省略可能**|新しいサーバー レベルの照合順序を指定します。<br /><br /> 既定値は、Windows オペレーティング システムのロケールに基づいています。 詳細については、「 [セットアップでの照合順序の設定](../../relational-databases/collations/collation-and-unicode-support.md)」を参照してください。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SAPWD<br /><br /> **インスタンスのインストール中に /SECURITYMODE=SQL が指定された場合に必須。**|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] **SA** アカウントのパスワードを指定します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLSYSADMINACCOUNTS<br /><br /> **必須**|sysadmin ロールのメンバーになるためにログインを準備するには、このパラメーターを使用します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBDIR<br /><br /> **省略可能**|tempdb データ ファイルのディレクトリを指定します。 複数のディレクトリを指定する場合、各ディレクトリを空白で区切ります。 複数のディレクトリが指定されている場合、tempdb データ ファイルはラウンド ロビン形式ですべてのディレクトリにまたがるようになります。<br /><br /> 既定値:`<InstallSQLDataDir>\<SQLInstanceID>\MSSQL\Data` (システム データ ディレクトリ)<br /><br /> 注:このパラメーターは、RebuildDatabase シナリオにも追加されます。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBLOGDIR<br /><br /> **省略可能**|tempdb のログ ファイルのディレクトリを指定します。<br /><br /> 既定値:`<InstallSQLDataDir>\<SQLInstanceID>\MSSQL\Data` (システム データ ディレクトリ)<br /><br /> **注:** このパラメーターは、RebuildDatabase シナリオにも追加されます。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBFILECOUNT<br /><br /> **省略可能**|セットアップで追加する tempdb データ ファイルの数を指定します。 この値はコアの数まで増やすことができます。 既定値:<br /><br /> [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)]: 1<br /><br /> 他のすべてのエディション: 8 またはコアの数のうち、小さい方の値<br /><br /> **重要:** tempdb のプライマリ データベース ファイルは引き続き tempdb.mdf になります。 その他の tempdb ファイルには、tempdb_mssql_#.ndf という名前が付けられます。# は、セットアップ中に作成されたその他の各 tempdb データベース ファイルの一意の番号を表します。 この名前付け規則は、各データベース ファイルを一意にすることを目的としています。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスをアンインストールすると、tempdb_mssql_#.ndf 名前付け規則を使用するファイルが削除されます。 ユーザー データベース ファイルには tempdb_mssql_\*.ndf 名前付け規則を使用しないでください。<br /><br /> **警告:** このパラメーターの構成では [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] はサポートされていません。 セットアップでインストールされる tempdb データ ファイルは 1 つだけです。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBFILESIZE<br /><br /> **省略可能**|[!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] で導入されました。 各 tempdb データ ファイルの初期サイズを指定します。<br/><br/>既定値 = [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] の場合は 4 MB、その他のエディションの場合は 8 MB。<br/><br/>最小値 = (4 MB または 8 MB)。<br/><br/>最大値 = 1024 MB ([!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] の場合は 262,144 MB)。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBFILEGROWTH<br /><br /> **省略可能**|各 tempdb データ ファイルのファイル拡張増分値を MB 単位で指定します。 0 は、自動拡張がオフで、領域を追加できないことを示します。 最大 1024 のサイズまで指定できます。<br /><br /> 既定値:64<br /><br /> 許容範囲: 最小値 = 0、最大値 = 1024|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBLOGFILESIZE<br /><br /> **省略可能**|tempdb ログ ファイルの初期サイズを MB 単位で指定します。 最大 1024 のサイズまで指定できます。 既定値:<br /><br /> [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)]: 4<br /><br /> 他のすべてのエディション: 8<br /><br /> 許容範囲: 最小値 = 既定値 (4 または 8)、最大値 = 1024|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBLOGFILEGROWTH<br /><br /> **省略可能**|[!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] で導入されました。 各 tempdb ログ ファイルの初期サイズを指定します。<br/><br/>既定値 = [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] の場合は 4 MB、その他のエディションの場合は 8 MB。<br/><br/>最小値 = (4 MB または 8 MB)。<br/><br/>最大値 = 1024 MB ([!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] の場合は 262,144 MB)。|  
  
##  <a name="uninstall-parameters"></a><a name="Uninstall"></a> アンインストール パラメーター  
 次の表に示すパラメーターは、アンインストール用のコマンド ライン スクリプトを作成する場合に使用します。 
  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] コンポーネント|パラメーター|説明|  
|-----------------------------------------|---------------|-----------------|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/ACTION<br /><br /> **必須**|アンインストール ワークフローを示すために必要です。<br /><br /> サポートされる値:**アンインストール**|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/CONFIGURATIONFILE<br /><br /> **省略可能**|使用する [ConfigurationFile](./install-sql-server-using-a-configuration-file.md) を指定します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/FEATURES<br /><br /> **必須**|アンインストールする [コンポーネント](#Feature) を指定します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/HELP、H、?<br /><br /> **省略可能**|パラメーターの使用方法を表示します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/INDICATEPROGRESS<br /><br /> **省略可能**|詳細なセットアップ ログ ファイルがコンソールにパイプされるように指定します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/INSTANCENAME<br /><br /> **必須**|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] インスタンス名を指定します。<br /><br /> 詳細については、「 [Instance Configuration](../../sql-server/install/instance-configuration.md)」を参照してください。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/Q または /QUIET <br /><br /> **省略可能**|セットアップが、ユーザー インターフェイスなしで、非表示モードで実行されるように指定します。 このパラメーターは、自動インストールに使用されます。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/HIDECONSOLE<br /><br /> **省略可能**|コンソール ウィンドウを非表示にするか閉じる場合に指定します。|  
  
###### <a name="sample-syntax"></a>サンプル構文:  
 SQL Server の既存のインスタンスをアンインストールする場合。 
  
```  
setup.exe /Action=Uninstall /FEATURES=SQL,AS,RS,IS,Tools /INSTANCENAME=MSSQLSERVER  
```  
  
 名前付きインスタンスを削除する場合は、この記事で前に示した例の "MSSQLSERVER" の代わりにインスタンス名を指定します。 
  
##  <a name="failover-cluster-parameters"></a><a name="ClusterInstall"></a> フェールオーバー クラスター パラメーター  
 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] フェールオーバー クラスター インスタンスをインストールする前に、次の記事を確認してください。  
  
-   [[!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] のインストールに必要なハードウェアおよびソフトウェア](../../sql-server/install/hardware-and-software-requirements-for-installing-sql-server.md)  
  
-   [SQL Server インストールにおけるセキュリティの考慮事項](../../sql-server/install/security-considerations-for-a-sql-server-installation.md)
  
-   [フェールオーバー クラスタリングをインストールする前に](../../sql-server/failover-clusters/install/before-installing-failover-clustering.md)  
  
-   [Always On フェールオーバー クラスター インスタンス &#40;SQL Server&#41;](../../sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server.md)  
  
    > [!IMPORTANT]  
    > フェールオーバー クラスターのすべてのインストール コマンドには、基になる Windows クラスターが必要です。 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] フェールオーバー クラスターに含まれるすべてのノードは、同じ Windows クラスターに含まれている必要があります。 
  
 次のフェールオーバー クラスターのインストール スクリプトをテストし、必要に応じて変更してください。 
  
#### <a name="integrated-install-failover-cluster-parameters"></a>フェールオーバー クラスターの統合インストール パラメーター  
 次の表に示すパラメーターは、フェールオーバー クラスターのインストール用コマンド ライン スクリプトを作成する場合に使用します。 
  
 統合インストールの詳細については、「[Always On フェールオーバー クラスター インスタンス &#40;SQL Server&#41;](../../sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server.md)」をご覧ください。 
  
> [!NOTE]
> インストール後にノードを追加するには、[ノードの追加](#AddNode)操作を使用します。 
  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] コンポーネント|パラメーター|詳細|  
|-----------------------------------------|---------------|-------------|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/ACTION<br /><br /> **必須**|フェールオーバー クラスター インストール ワークフローを示すために必要です。<br /><br /> サポートされる値:**InstallFailoverCluster**|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/IACCEPTSQLSERVERLICENSETERMS<br /><br /> **自動インストールのために /Q パラメーターまたは /QS パラメーターを指定した場合にのみ必須です。**|ライセンス条項への同意を確認するために必要です。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/ENU<br /><br /> **省略可能**|ローカライズされたオペレーティング システムに SQL Server の英語版をインストールする場合に、オペレーティング システムに対応する言語と英語の両方の言語パックがインストール メディアに含まれているときは、このパラメーターを使用します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/FAILOVERCLUSTERGROUP<br /><br /> **省略可能**|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] フェールオーバー クラスターに使用されるリソース グループの名前を指定します。 名前は、既存のクラスター グループの名前か新規のリソース グループの名前のどちらかになります。<br /><br /> 既定値:<br /><br /> SQL Server (\<InstanceName>)|  
|PolyBase|/PBENGSVCACCOUNT<br /><br /> **省略可能**|エンジン サービスのアカウントを指定します。 既定値は、 **NT Authority\NETWORK SERVICE** です。|  
|PolyBase|/PBDMSSVCPASSWORD<br /><br /> **省略可能**|エンジン サービス アカウントのパスワードを指定します。 管理されたサービス アカウント、仮想アカウント、またはビルトイン アカウントの使用時、このパラメーターは省略できます。 |  
|PolyBase|/PBENGSVCSTARTUPTYPE<br /><br /> **省略可能**|PolyBase エンジン サービスのスタートアップ モードを指定します Automatic (既定値)、Disabled、Manual。|  
|PolyBase|/PBPORTRANGE<br /><br /> **省略可能**|PolyBase サービスのポート範囲 (6 ポート以上) を指定します。 例:<br /><br /> `/PBPORTRANGE=16450-16460`|  
|PolyBase|/PBSCALEOUT<br /><br /> **省略可能**|PolyBase スケールアウト計算グループの一部として [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] インスタンスを使用するかどうかを指定します。 サポートされる値:**True**、**False**|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/*UpdateEnabled*<br /><br /> **省略可能**|SQL Server セットアップが製品の更新プログラムを検出し、それらを含める必要があるかどうかを指定します。 有効値は True および False または 1 および 0 です。 既定では、SQL Server セットアップには検出された更新プログラムが含まれます。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/*UpdateSource*<br /><br /> **省略可能**|SQL Server セットアップが製品の更新プログラムを取得する場所を指定します。 有効値は [!INCLUDE[msCoName](../../includes/msconame-md.md)] Update を検索する "MU"、有効なフォルダー パス、相対パス (.\MyUpdates など)、または UNC 共有です。 既定では、SQL Server セットアップは [!INCLUDE[msCoName](../../includes/msconame-md.md)] Update または Windows Server Update Services を介して Windows Update Service を検索します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/CONFIGURATIONFILE<br /><br /> **省略可能**|使用する [ConfigurationFile](./install-sql-server-using-a-configuration-file.md) を指定します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/ERRORREPORTING<br /><br /> **省略可能**|[!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] には影響しません。 <br/><br/>エラーのフィードバックを Microsoft に送信する方法を管理するには、「[フィードバックをマイクロソフトに送信するように [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] を構成する方法](https://support.microsoft.com/kb/3153756)」を参照してください。 <br/><br/>以前のバージョンでは、これにより SQL Server のエラー報告が指定されます。<br /><br /> 詳細については、「 [Microsoft エラー報告サービスのプライバシーに関する声明](../../sql-server/sql-server-privacy.md)」を参照してください。 サポートされる値:<br /><br /> 1 = 有効<br /><br /> 0 = 無効|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/FEATURES<br /><br /> **必須**|インストールする [コンポーネント](#Feature) を指定します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/HELP、?<br /><br /> **省略可能**|パラメーターの使用方法を表示します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/INDICATEPROGRESS<br /><br /> **省略可能**|詳細なセットアップ ログ ファイルがコンソールにパイプされるように指定します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/INSTALLSHAREDDIR<br /><br /> **省略可能**|共有コンポーネント (64 ビット) の既定以外のインストール ディレクトリを指定します。<br /><br /> 既定値は `%Program Files%\Microsoft SQL Server` です<br /><br /> `%Program Files(x86)%\Microsoft SQL Server` に設定することはできません|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/INSTALLSHAREDWOWDIR<br /><br /> **省略可能**|共有コンポーネント (32 ビット) の既定以外のインストール ディレクトリを指定します。 64 ビット システムのみでサポートされます。<br /><br /> 既定値は `%Program Files(x86)%\Microsoft SQL Server` です<br /><br /> `%Program Files%\Microsoft SQL Server` に設定することはできません|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/INSTANCEDIR<br /><br /> **省略可能**|インスタンス専用のコンポーネントについて既定以外のインストール ディレクトリを指定します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/INSTANCEID<br /><br /> **省略可能**|[InstanceID](#InstanceID)の既定値以外の値を指定します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/INSTANCENAME<br /><br /> **必須**|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] インスタンス名を指定します。<br /><br /> 詳細については、「 [Instance Configuration](../../sql-server/install/instance-configuration.md)」を参照してください。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/PID<br /><br /> **省略可能**|SQL Server のエディション用のプロダクト キーを指定します。 このパラメーターが指定されていない場合、Evaluation が使用されます。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/Q または /QUIET <br /><br /> **省略可能**|セットアップが、ユーザー インターフェイスなしで、非表示モードで実行されるように指定します。 このパラメーターは、自動インストールに使用されます。 /Q パラメーターによって /QS パラメーターの入力がオーバーライドされます。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/QS または /QUIETSIMPLE <br /><br /> **省略可能**|セットアップが UI を使用して実行され、UI を使用して進捗状況が表示されるように指定します。さらに、セットアップで入力ができないように、またはエラー メッセージが表示されないように指定します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/SQMREPORTING<br /><br /> **省略可能**|[!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] には影響しません。 <br/><br/>エラーのフィードバックを Microsoft に送信する方法を管理するには、「[フィードバックをマイクロソフトに送信するように [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] を構成する方法](https://support.microsoft.com/kb/3153756)」を参照してください。 <br/><br/>以前のバージョンでは、これにより SQL Server の機能の使用状況レポートが指定されます。<br /><br />サポートされる値:<br /><br /> 1 = 有効<br /><br /> 0 = 無効|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/HIDECONSOLE<br /><br /> **省略可能**|コンソール ウィンドウを非表示にするか閉じる場合に指定します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/FAILOVERCLUSTERDISKS<br /><br /> **省略可能**|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] フェールオーバー クラスター リソース グループに含まれる共有ディスクの一覧を指定します。<br /><br /> 既定値:最初のドライブは、すべてのデータベースに対して既定のドライブとして使用されます。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/FAILOVERCLUSTERIPADDRESSES<br /><br /> **必須**|エンコードされた IP アドレスを指定します。 エンコードはセミコロン (;) で区切られ、\<IP Type>;\<address>;\<network name>;\<subnet mask> という形式に従います。 サポートされている IP の種類には、DHCP、IPv4、および IPv6 があります。<br />フェールオーバー クラスターの IP アドレスを複数指定するには、間にスペースを入れます。 次の例を参照してください。<br /><br /> `FAILOVERCLUSTERIPADDRESSES=DEFAULT`<br /><br /> `FAILOVERCLUSTERIPADDRESSES=IPv4;DHCP;ClusterNetwork1`<br /><br /> `FAILOVERCLUSTERIPADDRESSES=IPv4;172.16.0.0;ClusterNetwork1;172.31.255.255`<br /><br /> `FAILOVERCLUSTERIPADDRESSES=IPv6;DHCP;ClusterNetwork1`<br /><br /> `FAILOVERCLUSTERIPADDRESSES=IPv6;2001:db8:23:1002:20f:1fff:feff:b3a3;ClusterNetwork1`|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/FAILOVERCLUSTERNETWORKNAME<br /><br /> **必須**|新しい [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] フェールオーバー クラスターのネットワーク名を指定します。 このネットワーク名は、ネットワーク上で新しい [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] フェールオーバー クラスター インスタンスを識別するために使用されます。|  
|SQL Server エージェント|/AGTSVCACCOUNT<br /><br /> **必須**|SQL Server エージェント サービスのアカウントを指定します。|  
|SQL Server エージェント|/AGTSVCPASSWORD<br /><br /> [必須](#Accounts)|SQL Server エージェント サービス アカウントのパスワードを指定します。 管理されたサービス アカウント、仮想アカウント、またはビルトイン アカウントの使用時、このパラメーターは省略できます。|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASBACKUPDIR<br /><br /> **省略可能**|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] バックアップ ファイルのディレクトリを指定します。 既定値:<br /><br /> 64 ビットの WOW モード: `%Program Files(x86)%\Microsoft SQL Server\<INSTANCEDIR>\<ASInstanceID>\OLAP\Backup`<br /><br /> 他のすべてのインストール: `%Program Files%\Microsoft SQL Server\<INSTANCEDIR>\<ASInstanceID>\OLAP\Backup`|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASCOLLATION<br /><br /> **省略可能**|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]の照合順序の設定を指定します。<br /><br /> 既定値:**Latin1_General_CI_AS**|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASCONFIGDIR<br /><br /> **省略可能**|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] 構成ファイルのディレクトリを指定します。 既定値:<br /><br /> 64 ビットの WOW モード: `%Program Files(x86)%\Microsoft SQL Server\<INSTANCEDIR>\<ASInstanceID>\OLAP\Config`<br /><br /> 他のすべてのインストール: `%Program Files%\Microsoft SQL Server\<INSTANCEDIR>\<ASInstanceID>\OLAP\Config`|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASDATADIR<br /><br /> **省略可能**|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] データ ファイルのディレクトリを指定します。 既定値:<br /><br /> 64 ビットの WOW モード: `%Program Files(x86)%\Microsoft SQL Server\<INSTANCEDIR>\<ASInstanceID>\OLAP\Data`<br /><br /> 他のすべてのインストール: `%Program Files%\Microsoft SQL Server\<INSTANCEDIR>\<ASInstanceID>\OLAP\Data`|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASLOGDIR<br /><br /> **省略可能**|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] ログ ファイルのディレクトリを指定します。 既定値:<br /><br /> 64 ビットの WOW モード: `%Program Files(x86)%\Microsoft SQL Server\<INSTANCEDIR>\<ASInstanceID>\OLAP\Log`<br /><br /> 他のすべてのインストール: `%Program Files%\Microsoft SQL Server\<INSTANCEDIR>\<ASInstanceID>\OLAP\Log`|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASSYSADMINACCOUNTS<br /><br /> **必須**|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]の管理者資格情報を指定します。|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASTEMPDIR<br /><br /> **省略可能**|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] 一時ファイルのディレクトリを指定します。 既定値:<br /><br /> 64 ビットの WOW モード: `%Program Files(x86)%\Microsoft SQL Server\<INSTANCEDIR>\<ASInstanceID>\OLAP\Temp`<br /><br /> 他のすべてのインストール: `%Program Files%\Microsoft SQL Server\<INSTANCEDIR>\<ASInstanceID>\OLAP\Temp`|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASPROVIDERMSOLAP<br /><br /> **省略可能**|MSOLAP プロバイダーをインプロセスで実行できるかどうかを指定します。<br /><br /> 既定値:1 = 有効|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASSERVERMODE<br /><br /> **省略可能**|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] インスタンスのサーバー モードを指定します。 クラスターのシナリオで有効な値は、MULTIDIMENSIONAL または TABULAR です。 **ASSERVERMODE** では、大文字と小文字が区別されます。 値はすべて大文字で指定する必要があります。 有効な値の詳細については、「表形式モードでの Analysis Services のインストール」を参照してください。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/INSTALLSQLDATADIR<br /><br /> **必須**|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] データ ファイルのデータ ディレクトリを指定します。<br /><br /> データ ディレクトリは、共有クラスター ディスク上に指定する必要があります。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SAPWD<br /><br /> **/SECURITYMODE=SQL の場合に必須**|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] **SA** アカウントのパスワードを指定します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SECURITYMODE<br /><br /> **省略可能**|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のセキュリティ モードを指定します。<br /><br /> このパラメーターを指定しない場合、Windows 限定の認証モードがサポートされます。<br /><br /> サポートされる値:**SQL**|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLBACKUPDIR<br /><br /> **省略可能**|バックアップ ファイルのディレクトリを指定します。<br /><br /> 既定値:`<InstallSQLDataDir>\<SQLInstanceID>\MSSQL\Backup`|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLCOLLATION<br /><br /> **省略可能**|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]の照合順序の設定を指定します。<br /><br /> 既定値は、Windows オペレーティング システムのロケールに基づいています。 詳細については、「 [セットアップでの照合順序の設定](../../relational-databases/collations/collation-and-unicode-support.md)」を参照してください。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLSVCACCOUNT<br /><br /> **必須**|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] サービスの開始アカウントを指定します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLSVCPASSWORD<br /><br /> [必須](#Accounts)|SQLSVCACCOUNT のパスワードを指定します。 管理されたサービス アカウント、仮想アカウント、またはビルトイン アカウントの使用時、このパラメーターは省略できます。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLSYSADMINACCOUNTS<br /><br /> **必須**|**sysadmin** ロールのメンバーになるためにログインを準備するには、このパラメーターを使用します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLUSERDBDIR<br /><br /> **省略可能**|ユーザー データベースのデータ ファイルのディレクトリを指定します。<br /><br /> 既定値:`<InstallSQLDataDir>\<SQLInstanceID>\MSSQL\Data`|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBDIR<br /><br /> **省略可能**|tempdb データ ファイルのディレクトリを指定します。 複数のディレクトリを指定する場合、各ディレクトリを空白で区切ります。 複数のディレクトリが指定されている場合、tempdb データ ファイルはラウンド ロビン形式ですべてのディレクトリにまたがるようになります。<br /><br /> 既定値:`<InstallSQLDataDir>\<SQLInstanceID>\MSSQL\Data` (システム データ ディレクトリ)<br /><br /> 注:このパラメーターは、RebuildDatabase シナリオにも追加されます。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBLOGDIR<br /><br /> **省略可能**|tempdb のログ ファイルのディレクトリを指定します。<br /><br /> 既定値:`<InstallSQLDataDir>\<SQLInstanceID>\MSSQL\Data` (システム データ ディレクトリ)<br /><br /> 注:このパラメーターは、RebuildDatabase シナリオにも追加されます。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBFILECOUNT<br /><br /> **省略可能**|セットアップで追加する tempdb データ ファイルの数を指定します。 この値はコアの数まで増やすことができます。 既定値:<br /><br /> [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)]: 1<br /><br /> 他のすべてのエディション: 8 またはコアの数のうち、小さい方の値<br /><br /> **重要:** tempdb のプライマリ データベース ファイルは引き続き tempdb.mdf になります。 その他の tempdb ファイルには、tempdb_mssql_#.ndf という名前が付けられます。# は、セットアップ中に作成されたその他の各 tempdb データベース ファイルの一意の番号を表します。 この名前付け規則は、各データベース ファイルを一意にすることを目的としています。 SQL Server のインスタンスをアンインストールすると、tempdb_mssql_#.ndf 名前付け規則を使用するファイルが削除されます。 ユーザー データベース ファイルには tempdb_mssql_\*.ndf 名前付け規則を使用しないでください。<br /><br /> **警告:** このパラメーターの構成では [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] はサポートされていません。 セットアップでインストールされる tempdb データ ファイルは 1 つだけです。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBFILESIZE<br /><br /> **省略可能**|[!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] で導入されました。 各 tempdb データ ファイルの初期サイズを指定します。<br/><br/>既定値 = 8 MB。<br/><br/>最小値 = 8 MB。<br/><br/>最大値 = 1024 MB ([!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] の場合は 262,144 MB)。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBFILEGROWTH<br /><br /> **省略可能**|各 tempdb データ ファイルのファイル拡張増分値を MB 単位で指定します。 0 は、自動拡張がオフで、領域を追加できないことを示します。 最大 1024 のサイズまで指定できます。<br /><br /> 既定値:64<br /><br /> 許容範囲: 最小値 = 0、最大値 = 1024|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBLOGFILESIZE<br /><br /> **省略可能**|tempdb ログ ファイルの初期サイズを MB 単位で指定します。 最大 1024 のサイズまで指定できます。 <br /> 既定値:<br /><br /> [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)]: 4<br /><br /> 他のすべてのエディション: 8<br /><br /> 許容範囲: 最小値 = 既定値 (4 または 8)、最大値 = 1024|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBLOGFILEGROWTH<br /><br /> **省略可能**|[!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] で導入されました。 各 tempdb ログ ファイルの初期サイズを指定します。<br/><br/>既定値 = [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] の場合は 4 MB、その他のエディションの場合は 8 MB。<br/><br/>最小値 = (4 MB または 8 MB)。<br/><br/>最大値 = 1024 MB ([!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] の場合は 262,144 MB)。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLUSERDBLOGDIR<br /><br /> **省略可能**|ユーザー データベースのログ ファイルのディレクトリを指定します。<br /><br /> 既定値:`<InstallSQLDataDir>\<SQLInstanceID>\MSSQL\Data`|  
|FILESTREAM|/FILESTREAMLEVEL<br /><br /> **省略可能**|FILESTREAM 機能のアクセス レベルを指定します。 サポートされる値:<br /><br /> 0 = [このインスタンスに対する FILESTREAM サポートを無効にする] (既定値)<br /><br /> 1 = [ [!INCLUDE[tsql](../../includes/tsql-md.md)] アクセスに対して FILESTREAM を有効にする]<br /><br /> 2 = [ [!INCLUDE[tsql](../../includes/tsql-md.md)] アクセスおよびファイル I/O ストリーム アクセスに対して FILESTREAM を有効にする] (クラスター シナリオに対しては無効です)<br /><br /> 3 = [リモート クライアントに FILESTREAM データへのストリーム アクセスを許可する]|  
|FILESTREAM|/FILESTREAMSHARENAME<br /><br /> **省略可能**<br /><br /> **FILESTREAMLEVEL が 1 より大きい場合は必須。**|FILESTREAM データを格納する Windows 共有の名前を指定します。|  
|SQL Server フルテキスト|/FTSVCACCOUNT<br /><br /> **省略可能**|フルテキスト フィルター ランチャー サービスのアカウントを指定します。<br /><br /> このパラメーターは、[!INCLUDE[firstref_longhorn](../../includes/firstref-longhorn-md.md)] 以降では無視されます。 ServiceSID は、SQL Server と Full-text Filter Daemon 間の通信を確立するのに使用されます。<br /><br /> この値を指定しない場合、フルテキスト フィルター ランチャー サービスが無効になります。 サービス アカウントを変更し、フルテキスト機能を有効にするには、SQL Server コントロール マネージャーを使用する必要があります。<br /><br /> 既定値:ローカル サービス アカウント|  
|SQL Server フルテキスト|/FTSVCPASSWORD<br /><br /> **省略可能**|フルテキスト フィルター ランチャー サービスのパスワードを指定します。<br /><br /> このパラメーターは、[!INCLUDE[firstref_longhorn](../../includes/firstref-longhorn-md.md)] 以降では無視されます。 管理されたサービス アカウント、仮想アカウント、またはビルトイン アカウントの使用時、このパラメーターは省略できます。|  
|[!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)]|/ISSVCACCOUNT<br /><br /> **必須**|[!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)]のアカウントを指定します。<br /><br /> 既定値:NT AUTHORITY\NETWORK SERVICE|  
|[!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)]|/ISSVCPASSWORD<br /><br /> [必須](#Accounts)|[!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] のパスワードを指定します。 管理されたサービス アカウント、仮想アカウント、またはビルトイン アカウントの使用時、このパラメーターは省略できます。|  
|[!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)]|/ISSVCStartupType<br /><br /> **省略可能**|[サービスの](#Accounts) スタートアップ [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] モードを指定します。|  
|[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]|/RSINSTALLMODE<br /><br /> **省略可能**|SQL Server 2017 以降では適用できなくなりました。  [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]のインストール モードを指定します。|  
|[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]|/RSSVCACCOUNT<br /><br /> **必須**|SQL Server 2017 以降では適用できなくなりました。  [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]の開始アカウントを指定します。|  
|[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]|/RSSVCPASSWORD<br /><br /> [必須](#Accounts)| SQL Server 2017 以降では適用できなくなりました。  [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] サービスの開始アカウントのパスワードを指定します。 管理されたサービス アカウント、仮想アカウント、またはビルトイン アカウントの使用時、このパラメーターは省略できます。|  
|[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]|/RSSVCStartupType<br /><br /> **省略可能**|SQL Server 2017 以降では適用できなくなりました。  [の](#Accounts) スタートアップ [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]モードを指定します。 サポートされる値:<br /><br /> **自動**<br /><br /> **Disabled**<br /><br /> **手動**|  
  
 ドメイン グループの代わりに、サービス SID を使用することをお勧めします。 
  
##### <a name="additional-notes"></a>追加情報:  
 クラスター対応のコンポーネントは、 [!INCLUDE[ssDE](../../includes/ssde-md.md)] と [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] だけです。 他の機能はクラスターに対応していないので、フェールオーバーに対する可用性があまり高くありません。 
  
###### <a name="sample-syntax"></a>サンプル構文:  
 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]および [!INCLUDE[ssDE](../../includes/ssde-md.md)] が配置された単一ノードの [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] フェールオーバー クラスター インスタンスを既定のインスタンスとしてインストールするには 
  
```  
setup.exe /q /ACTION=InstallFailoverCluster /InstanceName=MSSQLSERVER /INDICATEPROGRESS /ASSYSADMINACCOUNTS="<DomainName\UserName>" /ASDATADIR=<Drive>:\OLAP\Data /ASLOGDIR=<Drive>:\OLAP\Log /ASBACKUPDIR=<Drive>:\OLAP\Backup /ASCONFIGDIR=<Drive>:\OLAP\Config /ASTEMPDIR=<Drive>:\OLAP\Temp /FAILOVERCLUSTERDISKS="<Cluster Disk Resource Name - for example, 'Disk S:'" /FAILOVERCLUSTERNETWORKNAME="<Insert Network Name>" /FAILOVERCLUSTERIPADDRESSES="IPv4;xx.xxx.xx.xx;Cluster Network;xxx.xxx.xxx.x" /FAILOVERCLUSTERGROUP="MSSQLSERVER" /Features=AS,SQL /ASSVCACCOUNT="<DomainName\UserName>" /ASSVCPASSWORD="xxxxxxxxxxx" /AGTSVCACCOUNT="<DomainName\UserName>" /AGTSVCPASSWORD="xxxxxxxxxxx" /INSTALLSQLDATADIR="<Drive>:\<Path>\MSSQLSERVER" /SQLCOLLATION="SQL_Latin1_General_CP1_CS_AS" /SQLSVCACCOUNT="<DomainName\UserName>" /SQLSVCPASSWORD="xxxxxxxxxxx" /SQLSYSADMINACCOUNTS="<DomainName\UserName> /IACCEPTSQLSERVERLICENSETERMS  
```  
  
#### <a name="prepare-failover-cluster-parameters"></a>フェールオーバー クラスターの準備パラメーター  
 次の表に示すパラメーターは、フェールオーバー クラスターを準備するコマンド ライン スクリプトを作成する場合に使用します。 ここでは、フェールオーバー クラスターのすべてのノードにフェールオーバー クラスター インスタンスを準備するのに必要な、クラスターの高度なインストールの最初の手順がわかります。 詳細については、「[Always On フェールオーバー クラスター インスタンス &#40;SQL Server&#41;](../../sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server.md)」を参照してください。 
  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] コンポーネント|パラメーター|説明|  
|-----------------------------------------|---------------|-----------------|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/ACTION<br /><br /> **必須**|フェールオーバー クラスターの準備に関するワークフローを示すために必要です。<br /><br /> サポートされる値:**PrepareFailoverCluster**|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/IACCEPTSQLSERVERLICENSETERMS<br /><br /> **自動インストールのために /Q パラメーターまたは /QS パラメーターを指定した場合にのみ必須です。**|ライセンス条項への同意を確認するために必要です。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/ENU<br /><br /> **省略可能**|ローカライズされたオペレーティング システムに SQL Server の英語版をインストールする場合に、オペレーティング システムに対応する言語と英語の両方の言語パックがインストール メディアに含まれているときは、このパラメーターを使用します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/*UpdateEnabled*<br /><br /> **省略可能**|SQL Server セットアップが製品の更新プログラムを検出し、それらを含める必要があるかどうかを指定します。 有効値は True および False または 1 および 0 です。 既定では、SQL Server セットアップには検出された更新プログラムが含まれます。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/*UpdateSource*<br /><br /> **省略可能**|SQL Server セットアップが製品の更新プログラムを取得する場所を指定します。 有効値は [!INCLUDE[msCoName](../../includes/msconame-md.md)] Update を検索する "MU"、有効なフォルダー パス、相対パス (`.\MyUpdates` など)、または UNC 共有です。 既定では、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップは [!INCLUDE[msCoName](../../includes/msconame-md.md)] Update、または Windows Server Update Services を介して Windows Update Service を検索します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/CONFIGURATIONFILE<br /><br /> **省略可能**|使用する [ConfigurationFile](./install-sql-server-using-a-configuration-file.md) を指定します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/ERRORREPORTING<br /><br /> **省略可能**|[!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] には影響しません。 <br/><br/>エラーのフィードバックを Microsoft に送信する方法を管理するには、「[フィードバックをマイクロソフトに送信するように [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] を構成する方法](https://support.microsoft.com/kb/3153756)」を参照してください。 <br/><br/>以前のバージョンでは、これにより SQL Server のエラー報告が指定されます。<br /><br /> 詳細については、「 [Microsoft エラー報告サービスのプライバシーに関する声明](../../sql-server/sql-server-privacy.md)」を参照してください。 サポートされる値:<br /><br /> 0 = 無効<br /><br /> 1 = 有効|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/FEATURES<br /><br /> **必須**|インストールする [コンポーネント](#Feature) を指定します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/HELP、?<br /><br /> **省略可能**|パラメーターの使用方法を表示します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/INDICATEPROGRESS<br /><br /> **省略可能**|詳細なセットアップ ログ ファイルがコンソールにパイプされるように指定します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/INSTALLSHAREDDIR<br /><br /> **省略可能**|共有コンポーネント (64 ビット) の既定以外のインストール ディレクトリを指定します。<br /><br /> 既定値は `%Program Files%\Microsoft SQL Server` です<br /><br /> `%Program Files(x86)%\Microsoft SQL Server` に設定することはできません|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/INSTALLSHAREDWOWDIR<br /><br /> **省略可能**|共有コンポーネント (32 ビット) の既定以外のインストール ディレクトリを指定します。 64 ビット システムのみでサポートされます。<br /><br /> 既定値は `%Program Files(x86)%\Microsoft SQL Server` です<br /><br /> `%Program Files%\Microsoft SQL Server` に設定することはできません|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/INSTANCEDIR<br /><br /> **省略可能**|インスタンス専用のコンポーネントについて既定以外のインストール ディレクトリを指定します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/INSTANCEID<br /><br /> **省略可能**|[InstanceID](#InstanceID)の既定値以外の値を指定します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/INSTANCENAME<br /><br /> **必須**|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] インスタンス名を指定します。<br /><br /> 詳細については、「 [Instance Configuration](../../sql-server/install/instance-configuration.md)」を参照してください。|  
|PolyBase|/PBENGSVCACCOUNT<br /><br /> **省略可能**|エンジン サービスのアカウントを指定します。 既定値は、 **NT Authority\NETWORK SERVICE** です。|  
|PolyBase|/PBDMSSVCPASSWORD<br /><br /> **省略可能**|エンジン サービス アカウントのパスワードを指定します。 管理されたサービス アカウント、仮想アカウント、またはビルトイン アカウントの使用時、このパラメーターは省略できます。|  
|PolyBase|/PBENGSVCSTARTUPTYPE<br /><br /> **省略可能**|PolyBase エンジン サービスのスタートアップ モードを指定します (Automatic (既定)、Disabled、Manual)。|  
|PolyBase|/PBPORTRANGE<br /><br /> **省略可能**|PolyBase サービスのポート範囲 (6 ポート以上) を指定します。 例:<br /><br /> `/PBPORTRANGE=16450-16460`|  
|PolyBase|/PBSCALEOUT<br /><br /> **省略可能**|PolyBase スケールアウト計算グループの一部として [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] インスタンスを使用するかどうかを指定します。 サポートされる値:**True**、**False**|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/PID<br /><br /> **省略可能**|SQL Server のエディション用のプロダクト キーを指定します。 このパラメーターが指定されていない場合、<br /><br /> Evaluation が使用されます。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/Q または /QUIET <br /><br /> **省略可能**|セットアップが、ユーザー インターフェイスなしで、非表示モードで実行されるように指定します。 このパラメーターは、自動インストールに使用されます。 /Q パラメーターによって /QS パラメーターの入力がオーバーライドされます。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/QS または /QUIETSIMPLE <br /><br /> **省略可能**|セットアップが UI を使用して実行され、UI を使用して進捗状況が表示されるように指定します。さらに、セットアップで入力ができないように、またはエラー メッセージが表示されないように指定します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/SQMREPORTING<br /><br /> **省略可能**|[!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] には影響しません。 <br/><br/>エラーのフィードバックを Microsoft に送信する方法を管理するには、「[フィードバックをマイクロソフトに送信するように [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] を構成する方法](https://support.microsoft.com/kb/3153756)」を参照してください。 <br/><br/>以前のバージョンでは、これにより SQL Server の機能の使用状況レポートが指定されます。<br /><br />サポートされる値:<br /><br /> 0 = 無効<br /><br /> 1 = 有効|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/HIDECONSOLE<br /><br /> **省略可能**|コンソール ウィンドウを非表示にするか閉じる場合に指定します。|  
|SQL Server エージェント|/AGTSVCACCOUNT<br /><br /> **必須**|SQL Server エージェント サービスのアカウントを指定します。|  
|SQL Server エージェント|/AGTSVCPASSWORD<br /><br /> [必須](#Accounts)|SQL Server エージェント サービス アカウントのパスワードを指定します。 管理されたサービス アカウント、仮想アカウント、またはビルトイン アカウントの使用時、このパラメーターは省略できます。 |  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASSVCACCOUNT<br /><br /> **必須**|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] サービスのアカウントを指定します。|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASSVCPASSWORD<br /><br /> [必須](#Accounts)|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] サービスのパスワードを指定します。 管理されたサービス アカウント、仮想アカウント、またはビルトイン アカウントの使用時、このパラメーターは省略できます。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLSVCACCOUNT<br /><br /> **必須**|SQL Server サービスの開始アカウントを指定します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLSVCPASSWORD<br /><br /> [必須](#Accounts)|SQLSVCACCOUNT のパスワードを指定します。 管理されたサービス アカウント、仮想アカウント、またはビルトイン アカウントの使用時、このパラメーターは省略できます。|  
|FILESTREAM|/FILESTREAMLEVEL<br /><br /> **省略可能**|FILESTREAM 機能のアクセス レベルを指定します。 サポートされる値:<br /><br /> 0 = [このインスタンスに対する FILESTREAM サポートを無効にする] (既定値)<br /><br /> 1 = [ [!INCLUDE[tsql](../../includes/tsql-md.md)] アクセスに対して FILESTREAM を有効にする]<br /><br /> 2 = [ [!INCLUDE[tsql](../../includes/tsql-md.md)] アクセスおよびファイル I/O ストリーム アクセスに対して FILESTREAM を有効にする] (クラスター シナリオに対しては無効です)<br /><br /> 3 = [リモート クライアントに FILESTREAM データへのストリーム アクセスを許可する]|  
|FILESTREAM|/FILESTREAMSHARENAME<br /><br /> **省略可能**<br /><br /> FILESTREAMLEVEL が 1 より大きい場合は **必須** 。|FILESTREAM データを格納する Windows 共有の名前を指定します。|  
|SQL Server フルテキスト|/FTSVCACCOUNT<br /><br /> **省略可能**|フルテキスト フィルター ランチャー サービスのアカウントを指定します。<br /><br /> このパラメーターは、[!INCLUDE[firstref_longhorn](../../includes/firstref-longhorn-md.md)] 以降では無視されます。 ServiceSID は、SQL Server と Full-text Filter Daemon 間の通信を確立するのに使用されます。<br /><br /> この値を指定しない場合、フルテキスト フィルター ランチャー サービスが無効になります。 サービス アカウントを変更し、フルテキスト機能を有効にするには、SQL Server コントロール マネージャーを使用する必要があります。<br /><br /> 既定値:ローカル サービス アカウント|  
|SQL Server フルテキスト|/FTSVCPASSWORD<br /><br /> **省略可能**|フルテキスト フィルター ランチャー サービスのパスワードを指定します。<br /><br /> このパラメーターは、[!INCLUDE[firstref_longhorn](../../includes/firstref-longhorn-md.md)] 以降では無視されます。 管理されたサービス アカウント、仮想アカウント、またはビルトイン アカウントの使用時、このパラメーターは省略できます。|  
|[!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)]|/ISSVCACCOUNT<br /><br /> **必須**|[!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)]のアカウントを指定します。<br /><br /> 既定値:NT AUTHORITY\NETWORK SERVICE|  
|[!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)]|/ISSVCPASSWORD<br /><br /> [必須](#Accounts)|[!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] のパスワードを指定します。 管理されたサービス アカウント、仮想アカウント、またはビルトイン アカウントの使用時、このパラメーターは省略できます。|  
|[!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)]|/ISSVCStartupType<br /><br /> **省略可能**|[サービスの](#Accounts) スタートアップ [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] モードを指定します。|  
|[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]|/RSINSTALLMODE<br /><br /> **ファイル専用モードのみで使用可**| SQL Server 2017 以降では適用できなくなりました。  [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]のインストール モードを指定します。|  
|[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]|/RSSVCACCOUNT<br /><br /> **必須**| SQL Server 2017 以降では適用できなくなりました。  [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]の開始アカウントを指定します。|  
|[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]|/RSSVCPASSWORD<br /><br /> [必須](#Accounts)| SQL Server 2017 以降では適用できなくなりました。  [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] サービスの開始アカウントのパスワードを指定します。 管理されたサービス アカウント、仮想アカウント、またはビルトイン アカウントの使用時、このパラメーターは省略できます。|  
|[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]|/RSSVCStartupType<br /><br /> **省略可能**| SQL Server 2017 以降では適用できなくなりました。 [の](#Accounts) スタートアップ [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]モードを指定します。 サポートされる値:<br /><br /> **自動**<br /><br /> **Disabled**<br /><br /> **手動**|  
  
 ドメイン グループの代わりに、サービス SID を使用することをお勧めします。 
  
###### <a name="sample-syntax"></a>サンプル構文:  
 [!INCLUDE[ssDE](../../includes/ssde-md.md)] および [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]用のフェールオーバー クラスターの高度なインストール シナリオで "準備" 手順を実行するには 
  
 既定のインスタンスを準備するには、コマンド プロンプトで次のコマンドを実行します。  
  
```  
setup.exe /q /ACTION=PrepareFailoverCluster /InstanceName=MSSQLSERVER /Features=AS,SQL /INDICATEPROGRESS /ASSVCACCOUNT="<DomainName\UserName>" /ASSVCPASSWORD="xxxxxxxxxxx" /SQLSVCACCOUNT="<DomainName\UserName>" /SQLSVCPASSWORD="xxxxxxxxxxx" /AGTSVCACCOUNT="<DomainName\UserName>" /AGTSVCPASSWORD="xxxxxxxxxxx" /IACCEPTSQLSERVERLICENSETERMS  
```  
  
 名前付きインスタンスを準備するには、コマンド プロンプトで次のコマンドを実行します。  
  
```  
setup.exe /q /ACTION=PrepareFailoverCluster /InstanceName="<Insert Instance name>" /Features=AS,SQL /INDICATEPROGRESS /ASSVCACCOUNT="<DomainName\UserName>" /ASSVCPASSWORD="xxxxxxxxxxx" /SQLSVCACCOUNT="<DomainName\UserName>" /SQLSVCPASSWORD="xxxxxxxxxxx" /AGTSVCACCOUNT="<DomainName\UserName>" /AGTSVCPASSWORD="xxxxxxxxxxx" /IACCEPTSQLSERVERLICENSETERMS  
```  
  
#### <a name="complete-failover-cluster-parameters"></a>フェールオーバー クラスターの完了パラメーター  
 次の表に示すパラメーターは、フェールオーバー クラスターを完了するコマンド ライン スクリプトを作成する場合に使用します。 ここでは、フェールオーバー クラスターの高度なインストール オプションについて、2 番目の手順がわかります。 すべてのフェールオーバー クラスター ノードで準備を実行した後、共有ディスクを所有するノードでこのコマンドを実行します。 詳細については、「[Always On フェールオーバー クラスター インスタンス &#40;SQL Server&#41;](../../sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server.md)」を参照してください。 
  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] コンポーネント|パラメーター|説明|  
|-----------------------------------------|---------------|-----------------|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/ACTION<br /><br /> **必須**|フェールオーバー クラスターの完了に関するワークフローを示すために必要です。<br /><br /> サポートされる値:**CompleteFailoverCluster**|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/ENU<br /><br /> **省略可能**|ローカライズされたオペレーティング システムに SQL Server の英語版をインストールする場合に、オペレーティング システムに対応する言語と英語の両方の言語パックがインストール メディアに含まれているときは、このパラメーターを使用します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/FAILOVERCLUSTERGROUP<br /><br /> **省略可能**|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] フェールオーバー クラスターに使用されるリソース グループの名前を指定します。 名前は、既存のクラスター グループの名前か新規のリソース グループの名前のどちらかになります。<br /><br /> 既定値:<br /><br /> [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (\<InstanceName>)|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/CONFIGURATIONFILE<br /><br /> **省略可能**|使用する [ConfigurationFile](./install-sql-server-using-a-configuration-file.md) を指定します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/ERRORREPORTING<br /><br /> **省略可能**|[!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] には影響しません。 <br/><br/>エラーのフィードバックを Microsoft に送信する方法を管理するには、「[フィードバックをマイクロソフトに送信するように [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] を構成する方法](https://support.microsoft.com/kb/3153756)」を参照してください。 <br/><br/>以前のバージョンでは、これにより [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のエラー報告が指定されます。<br /><br /> 詳細については、「 [Microsoft エラー報告サービスのプライバシーに関する声明](../../sql-server/sql-server-privacy.md)」を参照してください。 サポートされる値:<br /><br /> 1 = 有効<br /><br /> 0 = 無効|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/HELP、?<br /><br /> **省略可能**|パラメーターの使用方法を表示します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/INDICATEPROGRESS<br /><br /> **省略可能**|詳細なセットアップ ログ ファイルがコンソールにパイプされるように指定します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/INSTANCENAME<br /><br /> **必須**|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] インスタンス名を指定します。<br /><br /> 詳細については、[インスタンスの構成](../../sql-server/install/instance-configuration.md)に関するページを参照してください  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/PID<br /><br /> **省略可能**|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のエディション用のプロダクト キーを指定します。 このパラメーターが指定されていない場合、Evaluation が使用されます。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/Q または /QUIET <br /><br /> **省略可能**|セットアップが、ユーザー インターフェイスなしで、非表示モードで実行されるように指定します。 このパラメーターは、自動インストールに使用されます。 /Q パラメーターによって /QS パラメーターの入力がオーバーライドされます。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/QS または /QUIETSIMPLE <br /><br /> **省略可能**|セットアップが UI を使用して実行され、UI を使用して進捗状況が表示されるように指定します。さらに、セットアップで入力ができないように、またはエラー メッセージが表示されないように指定します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/SQMREPORTING<br /><br /> **省略可能**|[!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] には影響しません。 <br/><br/>エラーのフィードバックを Microsoft に送信する方法を管理するには、「[フィードバックをマイクロソフトに送信するように [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] を構成する方法](https://support.microsoft.com/kb/3153756)」を参照してください。 <br/><br/>以前のバージョンでは、これにより [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の機能の使用状況レポートが指定されます。<br /><br />サポートされる値:<br /><br /> 1 = 有効<br /><br /> 0 = 無効|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/HIDECONSOLE<br /><br /> **省略可能**|コンソール ウィンドウを非表示にするか閉じる場合に指定します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/FAILOVERCLUSTERDISKS<br /><br /> **省略可能**|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] フェールオーバー クラスター リソース グループに含まれる共有ディスクの一覧を指定します。<br /><br /> 既定値:<br /><br /> 最初のドライブは、すべてのデータベースに対して既定のドライブとして使用されます。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/FAILOVERCLUSTERIPADDRESSES<br /><br /> **必須**|エンコードされた IP アドレスを指定します。 エンコードはセミコロン (;) で区切られ、\<IP Type>;\<address>;\<network name>;\<subnet mask> という形式に従います。 サポートされている IP の種類には、DHCP、IPv4、および IPv6 があります。<br />フェールオーバー クラスターの IP アドレスを複数指定するには、間にスペースを入れます。 次の例を参照してください。<br /><br /> `FAILOVERCLUSTERIPADDRESSES=DEFAULT`<br /><br /> `FAILOVERCLUSTERIPADDRESSES=IPv4;DHCP;ClusterNetwork1`<br /><br /> `FAILOVERCLUSTERIPADDRESSES=IPv4;172.16.0.0;ClusterNetwork1;172.31.255.255`<br /><br /> `FAILOVERCLUSTERIPADDRESSES=IPv6;DHCP;ClusterNetwork1`<br /><br /> `FAILOVERCLUSTERIPADDRESSES=IPv6;2001:db8:23:1002:20f:1fff:feff:b3a3;ClusterNetwork1`|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/FAILOVERCLUSTERNETWORKNAME<br /><br /> **必須**|新しい [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] フェールオーバー クラスターのネットワーク名を指定します。 このネットワーク名は、ネットワーク上で新しい [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] フェールオーバー クラスター インスタンスを識別するために使用されます。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/CONFIRMIPDEPENDENCYCHANGE|マルチサブネット フェールオーバー クラスターについて、IP アドレス リソースの依存関係を OR に設定することを示します。 詳細については、「[新しい SQL Server フェールオーバー クラスターの作成 &#40;セットアップ&#41;](../../sql-server/failover-clusters/install/create-a-new-sql-server-failover-cluster-setup.md)」をご覧ください。 サポートされる値:<br /><br /> 0 = False (既定値)<br /><br /> 1 = True|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASBACKUPDIR<br /><br /> **省略可能**|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] バックアップ ファイルのディレクトリを指定します。 既定値:<br /><br /> 64 ビットの WOW モード: `%Program Files(x86)%\Microsoft SQL Server\<INSTANCEDIR>\<ASInstanceID>\OLAP\Backup`<br /><br /> 他のすべてのインストール: `%Program Files%\Microsoft SQL Server\<INSTANCEDIR>\<ASInstanceID>\OLAP\Backup`|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASCOLLATION<br /><br /> **省略可能**|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]の照合順序の設定を指定します。<br /><br /> 既定値:**Latin1_General_CI_AS**|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASCONFIGDIR<br /><br /> **省略可能**|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] 構成ファイルのディレクトリを指定します。 既定値:<br /><br /> 64 ビットの WOW モード: `%Program Files(x86)%\Microsoft SQL Server\<INSTANCEDIR>\<ASInstanceID>\OLAP\Config`<br /><br /> 他のすべてのインストール: `%Program Files%\Microsoft SQL Server\<INSTANCEDIR>\<ASInstanceID>\OLAP\Config`|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASDATADIR<br /><br /> **省略可能**|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] データ ファイルのディレクトリを指定します。 既定値:<br /><br /> 64 ビットの WOW モード: `%Program Files(x86)%\Microsoft SQL Server\<INSTANCEDIR>\<ASInstanceID>\OLAP\Data`<br /><br /> 他のすべてのインストール: `%Program Files%\Microsoft SQL Server\<INSTANCEDIR>\<ASInstanceID>\OLAP\Data`|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASLOGDIR<br /><br /> **省略可能**|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] ログ ファイルのディレクトリを指定します。 既定値:<br /><br /> 64 ビットの WOW モード: `%Program Files(x86)%\Microsoft SQL Server\<INSTANCEDIR>\<ASInstanceID>\OLAP\Log`<br /><br /> 他のすべてのインストール: `%Program Files%\Microsoft SQL Server\<INSTANCEDIR>\<ASInstanceID>\OLAP\Log`|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASSERVERMODE<br /><br /> **省略可能**|Analysis Services インスタンスのサーバー モードを指定します。 クラスターのシナリオで有効な値は、MULTIDIMENSIONAL または TABULAR です。 **ASSERVERMODE** では、大文字と小文字が区別されます。 値はすべて大文字で指定する必要があります。 有効な値の詳細については、「表形式モードでの Analysis Services のインストール」を参照してください。|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASSYSADMINACCOUNTS<br /><br /> **必須**|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]の管理者資格情報を指定します。|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASTEMPDIR<br /><br /> **省略可能**|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] 一時ファイルのディレクトリを指定します。 既定値:<br /><br /> 64 ビットの WOW モード: `%Program Files(x86)%\Microsoft SQL Server\<INSTANCEDIR>\<ASInstanceID>\OLAP\Temp`<br /><br /> 他のすべてのインストール: `%Program Files%\Microsoft SQL Server\<INSTANCEDIR>\<ASInstanceID>\OLAP\Temp`|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASPROVIDERMSOLAP<br /><br /> **省略可能**|MSOLAP プロバイダーをインプロセスで実行できるかどうかを指定します。<br /><br /> 既定値:1 = 有効|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/INSTALLSQLDATADIR<br /><br /> **必須**|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] データ ファイルのデータ ディレクトリを指定します。<br /><br /> データ ディレクトリは、共有クラスター ディスク上に指定する必要があります。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SAPWD<br /><br /> **/SECURITYMODE=SQL の場合に必須**|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] **SA** アカウントのパスワードを指定します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SECURITYMODE<br /><br /> **省略可能**|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のセキュリティ モードを指定します。<br /><br /> このパラメーターを指定しない場合、Windows 限定の認証モードがサポートされます。<br /><br /> サポートされる値:**SQL**|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLBACKUPDIR<br /><br /> **省略可能**|バックアップ ファイルのディレクトリを指定します。<br /><br /> 既定値:`<InstallSQLDataDir>\<SQLInstanceID>\MSSQL\Backup`|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLCOLLATION<br /><br /> **省略可能**|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]の照合順序の設定を指定します。<br /><br /> 既定値は、Windows オペレーティング システムのロケールに基づいています。 詳細については、「 [セットアップでの照合順序の設定](../../relational-databases/collations/collation-and-unicode-support.md)」を参照してください。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLSYSADMINACCOUNTS<br /><br /> **必須**|sysadmin ロールのメンバーになるためにログインを準備するには、このパラメーターを使用します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLUSERDBDIR<br /><br /> **省略可能**|ユーザー データベースのデータ ファイルのディレクトリを指定します。<br /><br /> 既定値:`<InstallSQLDataDir>\<SQLInstanceID>\MSSQL\Data`|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLUSERDBLOGDIR<br /><br /> **省略可能**|ユーザー データベースのログ ファイルのディレクトリを指定します。<br /><br /> 既定値:`<InstallSQLDataDir>\<SQLInstanceID>\MSSQL\Data`|  
|[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]|/RSINSTALLMODE<br /><br /> **ファイル専用モードのみで使用可**| SQL Server 2017 以降では適用できなくなりました。  [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]のインストール モードを指定します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBDIR<br /><br /> **省略可能**|tempdb データ ファイルのディレクトリを指定します。 複数のディレクトリを指定する場合、各ディレクトリを空白で区切ります。 複数のディレクトリが指定されている場合、tempdb データ ファイルはラウンド ロビン形式ですべてのディレクトリにまたがるようになります。<br /><br /> 既定値:`<InstallSQLDataDir>\<SQLInstanceID>\MSSQL\Data` (システム データ ディレクトリ)<br /><br /> 注:このパラメーターは、RebuildDatabase シナリオにも追加されます。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBLOGDIR<br /><br /> **省略可能**|tempdb のログ ファイルのディレクトリを指定します。<br /><br /> 既定値:`<InstallSQLDataDir>\<SQLInstanceID>\MSSQL\Data` (システム データ ディレクトリ)<br /><br /> 注:このパラメーターは、RebuildDatabase シナリオにも追加されます。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBFILECOUNT<br /><br /> **省略可能**|セットアップで追加する tempdb データ ファイルの数を指定します。 この値はコアの数まで増やすことができます。 既定値:<br /><br /> [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)]: 1<br /><br /> 他のすべてのエディション: 8 またはコアの数のうち、小さい方の値<br /><br /> **重要:** tempdb のプライマリ データベース ファイルは引き続き tempdb.mdf になります。 その他の tempdb ファイルには、tempdb_mssql_#.ndf という名前が付けられます。# は、セットアップ中に作成されたその他の各 tempdb データベース ファイルの一意の番号を表します。 この名前付け規則は、各データベース ファイルを一意にすることを目的としています。 SQL Server のインスタンスをアンインストールすると、tempdb_mssql_#.ndf 名前付け規則を使用するファイルが削除されます。 ユーザー データベース ファイルには tempdb_mssql_\*.ndf 名前付け規則を使用しないでください。<br /><br /> **警告:** このパラメーターの構成では [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] はサポートされていません。 セットアップでインストールされる tempdb データ ファイルは 1 つだけです。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBFILESIZE<br /><br /> **省略可能**|[!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] で導入されました。 各 tempdb データ ファイルの初期サイズを指定します。<br/><br/>既定値 = 8 MB。<br/><br/>最小値 = 8 MB。<br/><br/>最大値 = 1024 MB ([!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] の場合は 262,144 MB)。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBFILEGROWTH<br /><br /> **省略可能**|各 tempdb データ ファイルのファイル拡張増分値を MB 単位で指定します。 0 は、自動拡張がオフで、領域を追加できないことを示します。 最大 1024 のサイズまで指定できます。<br /><br /> 既定値:64<br /><br /> 許容範囲: 最小値 = 0、最大値 = 1024|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBLOGFILESIZE<br /><br /> **省略可能**|tempdb ログ ファイルの初期サイズを MB 単位で指定します。 最大 1024 のサイズまで指定できます。 <br /> 既定値:<br /><br /> [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)]: 4<br /><br /> 他のすべてのエディション: 8<br /><br /> 許容範囲: 最小値 = 既定値 (4 または 8)、最大値 = 1024|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBLOGFILEGROWTH<br /><br /> **省略可能**|[!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] で導入されました。 各 tempdb ログ ファイルの初期サイズを指定します。<br/><br/>既定値 = [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] の場合は 4 MB、その他のエディションの場合は 8 MB。<br/><br/>最小値 = (4 MB または 8 MB)。<br/><br/>最大値 = 1024 MB ([!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] の場合は 262,144 MB)。|  
  
###### <a name="sample-syntax"></a>サンプル構文:  
 [!INCLUDE[ssDE](../../includes/ssde-md.md)] および [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]用のフェールオーバー クラスターの高度なインストール シナリオで "完了" 手順を実行するには、 フェールオーバー クラスター内のアクティブなノードにするコンピューター上で次のコマンドを実行して、ノードを使用できるようにします。 また、 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] フェールオーバー クラスター内の共有ディスクを所有するノードで "CompleteFailoverCluster" アクションを実行する必要があります。 
  
 既定のインスタンスについてフェールオーバー クラスターのインストールを完了するには、コマンド プロンプトで次のコマンドを実行します。  
  
```  
setup.exe /q /ACTION=CompleteFailoverCluster /InstanceName=MSSQLSERVER /INDICATEPROGRESS /ASSYSADMINACCOUNTS="<DomainName\Username>" /ASDATADIR=<Drive>:\OLAP\Data /ASLOGDIR=<Drive>:\OLAP\Log /ASBACKUPDIR=<Drive>:\OLAP\Backup /ASCONFIGDIR=<Drive>:\OLAP\Config /ASTEMPDIR=<Drive>:\OLAP\Temp /FAILOVERCLUSTERDISKS="<Cluster Disk Resource Name - for example, 'Disk S:'>:" /FAILOVERCLUSTERNETWORKNAME="<Insert FOI Network Name>" /FAILOVERCLUSTERIPADDRESSES="IPv4;xx.xxx.xx.xx;Cluster Network;xxx.xxx.xxx.x" /FAILOVERCLUSTERGROUP="MSSQLSERVER" /INSTALLSQLDATADIR="<Drive>:\<Path>\MSSQLSERVER" /SQLCOLLATION="SQL_Latin1_General_CP1_CS_AS" /SQLSYSADMINACCOUNTS="<DomainName\UserName>"  
```  
  
 名前付きインスタンスに対してフェールオーバー クラスターのインストールを完了するには、コマンド プロンプトで次のコマンドを実行します。  
  
```  
setup.exe /q /ACTION=CompleteFailoverCluster /InstanceName="<Insert Instance Name>" /INDICATEPROGRESS /ASSYSADMINACCOUNTS="<DomainName\UserName>" /ASDATADIR=<Drive>:\KATMAI\Data /ASLOGDIR=<drive>:\KATMAI\Log /ASBACKUPDIR=<Drive>:\KATMAI\Backup /ASCONFIGDIR=<Drive>:\KATMAI\Config /ASTEMPDIR=<Drive>:\KATMAI\Temp /FAILOVERCLUSTERDISKS="<Cluster Disk Resource Name - for example, 'Disk S:'>" /FAILOVERCLUSTERNETWORKNAME="CompNamedFOI" /FAILOVERCLUSTERIPADDRESSES="IPv4;xx.xxx.xx.xx;ClusterNetwork1;xxx.xxx.xxx.x" /FAILOVERCLUSTERGROUP="<Insert New Group Name>" /INSTALLSQLDATADIR="<Drive>:\<Path>\MSSQLSERVER_KATMAI" /SQLCOLLATION="SQL_Latin1_General_CP1_CS_AS" /SQLSYSADMINACCOUNTS="<DomainName\Username>"  
```  
  
#### <a name="upgrade-failover-cluster-parameters"></a>フェールオーバー クラスターのアップグレード パラメーター  
 次の表に示すパラメーターは、フェールオーバー クラスターのアップグレード用コマンド ライン スクリプトを作成する場合に使用します。 詳細については、[[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]フェールオーバー クラスター インスタンスのアップグレード &#40;セットアップ&#41;](../../sql-server/failover-clusters/windows/upgrade-a-sql-server-failover-cluster-instance-setup.md)に関するページと「[Always On フェールオーバー クラスター インスタンス &#40;SQL Server&#41;](../../sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server.md)」をご覧ください。 
  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] コンポーネント|パラメーター|説明|  
|-----------------------------------------|---------------|-----------------|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/ACTION<br /><br /> **必須**|インストール ワークフローを示すために必要です。<br /><br /> サポートされる値:**アップグレード**|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/IACCEPTSQLSERVERLICENSETERMS<br /><br /> **自動インストールのために /Q パラメーターまたは /QS パラメーターを指定した場合にのみ必須です。**|ライセンス条項への同意を確認するために必要です。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/ENU<br /><br /> **省略可能**|ローカライズされたオペレーティング システムに SQL Server の英語版をインストールする場合に、オペレーティング システムに対応する言語と英語の両方の言語パックがインストール メディアに含まれているときは、このパラメーターを使用します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/*UpdateEnabled*<br /><br /> **省略可能**|SQL Server セットアップが製品の更新プログラムを検出し、それらを含める必要があるかどうかを指定します。 有効値は True および False または 1 および 0 です。 既定では、SQL Server セットアップには検出された更新プログラムが含まれます。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/*UpdateSource*<br /><br /> **省略可能**|SQL Server セットアップが製品の更新プログラムを取得する場所を指定します。 有効値は [!INCLUDE[msCoName](../../includes/msconame-md.md)] Update を検索する "MU"、有効なフォルダー パス、相対パス (`.\MyUpdates` など)、または UNC 共有です。 既定では、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップは [!INCLUDE[msCoName](../../includes/msconame-md.md)] Update、または Windows Server Update Services を介して Windows Update Service を検索します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/CONFIGURATIONFILE<br /><br /> **省略可能**|使用する [ConfigurationFile](./install-sql-server-using-a-configuration-file.md) を指定します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/ERRORREPORTING<br /><br /> **省略可能**|[!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] には影響しません。 <br/><br/>エラーのフィードバックを Microsoft に送信する方法を管理するには、「[フィードバックをマイクロソフトに送信するように [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] を構成する方法](https://support.microsoft.com/kb/3153756)」を参照してください。 <br/><br/>以前のバージョンでは、これにより SQL Server のエラー報告が指定されます。<br /><br /> 詳細については、「 [Microsoft エラー報告サービスのプライバシーに関する声明](../../sql-server/sql-server-privacy.md)」を参照してください。 サポートされる値:<br /><br /> 0 = 無効<br /><br /> 1 = 有効|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/HELP、?<br /><br /> **省略可能**|パラメーターの使用方法を表示します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/INDICATEPROGRESS<br /><br /> **省略可能**|詳細なセットアップ ログ ファイルがコンソールにパイプされるように指定します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/ INSTANCEDIR<br /><br /> **省略可能**|共有コンポーネントの既定以外のインストール ディレクトリを指定します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/INSTANCEID<br /><br /> **[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降からアップグレードする場合に必須**<br /><br /> **次をアップグレードする場合はオプション: [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)]**|[InstanceID](#InstanceID)の既定値以外の値を指定します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/INSTANCENAME<br /><br /> **必須**|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] インスタンス名を指定します。<br /><br /> 詳細については、「 [Instance Configuration](../../sql-server/install/instance-configuration.md)」を参照してください。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/PID<br /><br /> **省略可能**|SQL Server のエディション用のプロダクト キーを指定します。 このパラメーターが指定されていない場合、Evaluation が使用されます。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/Q または /QUIET <br /><br /> **省略可能**|セットアップが、ユーザー インターフェイスなしで、非表示モードで実行されるように指定します。 このパラメーターは、自動インストールに使用されます。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/SQMREPORTING<br /><br /> **省略可能**|[!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] には影響しません。 以前のバージョンでは、これにより SQL Server の機能の使用状況レポートが指定されます。<br /><br />サポートされる値:<br /><br /> 0 = 無効<br /><br /> 1 = 有効|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/HIDECONSOLE<br /><br /> **省略可能**|コンソール ウィンドウを非表示にするか閉じる場合に指定します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/FAILOVERCLUSTERROLLOWNERSHIP|アップデート中の [フェールオーバーの動作](#RollOwnership) を指定します。|  
|SQL Server Browser Service|/BROWSERSVCSTARTUPTYPE<br /><br /> **省略可能**|SQL Server Browser サービスの [スタートアップ](#Accounts) モードを指定します。 サポートされる値:<br /><br /> **自動**<br /><br /> **Disabled**<br /><br /> **手動**|  
|SQL Server フルテキスト|/FTUPGRADEOPTION<br /><br /> **省略可能**|フルテキスト カタログのアップグレード オプションを指定します。 サポートされる値:<br /><br /> **REBUILD**<br /><br /> **RESET**<br /><br /> **IMPORT**|  
|[!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)]|/ISSVCACCOUNT<br /><br /> **必須**|[!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)]のアカウントを指定します。<br /><br /> 既定値:NT AUTHORITY\NETWORK SERVICE|  
|[!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)]|/ISSVCPASSWORD<br /><br /> [必須](#Accounts)|[!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] のパスワードを指定します。 管理されたサービス アカウント、仮想アカウント、またはビルトイン アカウントの使用時、このパラメーターは省略できます。|  
|[!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)]|/ISSVCStartupType<br /><br /> **省略可能**|[サービスの](#Accounts) スタートアップ [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] モードを指定します。|  
|[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]|/RSUPGRADEDATABASEACCOUNT<br /><br /> **省略可能**|プロパティは、2008 R2 以前のバージョンの SharePoint モードのレポート サーバーをアップグレードする場合にのみ使用されます。 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] で変更された古い SharePoint モード アーキテクチャを使用するレポート サーバーに対しては、追加のアップグレード操作が実行されます。 このオプションがコマンドライン インストールに含まれていない場合、古いレポート サーバー インスタンス用の既定のサービス アカウントが使用されます。 このプロパティが使用されている場合は、 **/RSUPGRADEPASSWORD** プロパティを使用してアカウントのパスワードを指定します。|  
|[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]|/RSUPGRADEPASSWORD<br /><br /> **省略可能**|既存のレポート サーバー サービス アカウントのパスワード。|  
  
####  <a name="add-node-parameters"></a><a name="AddNode"></a> ノード追加パラメーター  
 次の表に示すパラメーターは、ノード追加用のコマンド ライン スクリプトを作成する場合に使用します。 
  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] コンポーネント|パラメーター|説明|  
|-----------------------------------------|---------------|-----------------|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/ACTION<br /><br /> **必須**|AddNode ワークフローを示すために必要です。<br /><br /> サポートされる値:**AddNode**|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/IACCEPTSQLSERVERLICENSETERMS<br /><br /> **自動インストールのために /Q パラメーターまたは /QS パラメーターを指定した場合にのみ必須です。**|ライセンス条項への同意を確認するために必要です。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/ENU<br /><br /> **省略可能**|ローカライズされたオペレーティング システムに [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の英語版をインストールする場合に、オペレーティング システムに対応する言語と英語の両方の言語パックがインストール メディアに含まれているときは、このパラメーターを使用します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/*UpdateEnabled*<br /><br /> **省略可能**|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップが製品の更新プログラムを検出し、それらを含める必要があるかどうかを指定します。 有効値は True および False または 1 および 0 です。 既定では、SQL Server セットアップには検出された更新プログラムが含まれます。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/*UpdateSource*<br /><br /> **省略可能**|SQL Server セットアップが製品の更新プログラムを取得する場所を指定します。 有効値は [!INCLUDE[msCoName](../../includes/msconame-md.md)] Update を検索する "MU"、有効なフォルダー パス、相対パス (`.\MyUpdates` など)、または UNC 共有です。 既定では、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップは [!INCLUDE[msCoName](../../includes/msconame-md.md)] Update、または Windows Server Update Services を介して Windows Update Service を検索します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/CONFIGURATIONFILE<br /><br /> **省略可能**|使用する [ConfigurationFile](./install-sql-server-using-a-configuration-file.md) を指定します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/HELP、?<br /><br /> **省略可能**|パラメーターの使用方法を表示します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/INDICATEPROGRESS<br /><br /> **省略可能**|詳細なセットアップ ログ ファイルがコンソールにパイプされるように指定します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/INSTANCENAME<br /><br /> **必須**|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] インスタンス名を指定します。<br /><br /> 詳細については、「 [Instance Configuration](../../sql-server/install/instance-configuration.md)」を参照してください。|  
|PolyBase|/PBENGSVCACCOUNT<br /><br /> **省略可能**|エンジン サービスのアカウントを指定します。 既定値は、 **NT Authority\NETWORK SERVICE** です。|  
|PolyBase|/PBDMSSVCPASSWORD<br /><br /> **省略可能**|エンジン サービス アカウントのパスワードを指定します。 管理されたサービス アカウント、仮想アカウント、またはビルトイン アカウントの使用時、このパラメーターは省略できます。|  
|PolyBase|/PBENGSVCSTARTUPTYPE<br /><br /> **省略可能**|PolyBase エンジン サービスのスタートアップ モードを指定します (Automatic (既定)、Disabled、Manual)。|  
|PolyBase|/PBPORTRANGE<br /><br /> **省略可能**|PolyBase サービスのポート範囲 (6 ポート以上) を指定します。 例:<br /><br /> `/PBPORTRANGE=16450-16460`|  
|PolyBase|/PBSCALEOUT<br /><br /> **省略可能**|PolyBase スケールアウト計算グループの一部として [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] インスタンスを使用するかどうかを指定します。 サポートされる値:**True**、**False**|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/PID<br /><br /> **省略可能**|SQL Server のエディション用のプロダクト キーを指定します。 このパラメーターが指定されていない場合、Evaluation が使用されます。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/Q または /QUIET <br /><br /> **省略可能**|セットアップが、ユーザー インターフェイスなしで、非表示モードで実行されるように指定します。 このパラメーターは、自動インストールに使用されます。 /Q パラメーターによって /QS パラメーターの入力がオーバーライドされます。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/QS または /QUIETSIMPLE <br /><br /> **省略可能**|セットアップが UI を使用して実行され、UI を使用して進捗状況が表示されるように指定します。さらに、セットアップで入力ができないように、またはエラー メッセージが表示されないように指定します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/HIDECONSOLE<br /><br /> **省略可能**|コンソール ウィンドウを非表示にするか閉じる場合に指定します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/FAILOVERCLUSTERIPADDRESSES<br /><br /> **必須**|エンコードされた IP アドレスを指定します。 エンコードはセミコロン (;) で区切られ、\<IP Type>;\<address>;\<network name>;\<subnet mask> という形式に従います。 サポートされている IP の種類には、DHCP、IPv4、および IPv6 があります。<br />フェールオーバー クラスターの IP アドレスを複数指定するには、間にスペースを入れます。 次の例を参照してください。<br /><br /> `FAILOVERCLUSTERIPADDRESSES=DEFAULT`<br /><br /> `FAILOVERCLUSTERIPADDRESSES=IPv4;DHCP;ClusterNetwork1`<br /><br /> `FAILOVERCLUSTERIPADDRESSES=IPv4;172.16.0.0;ClusterNetwork1;172.31.255.255`<br /><br /> `FAILOVERCLUSTERIPADDRESSES=IPv6;DHCP;ClusterNetwork1`<br /><br /> `FAILOVERCLUSTERIPADDRESSES=IPv6;2001:db8:23:1002:20f:1fff:feff:b3a3;ClusterNetwork1`<br /><br /> <br /><br /> 詳細については、「[[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] フェールオーバー クラスターでのノードの追加または削除 &#40;セットアップ&#41;](../../sql-server/failover-clusters/install/add-or-remove-nodes-in-a-sql-server-failover-cluster-setup.md)」を参照してください。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/CONFIRMIPDEPENDENCYCHANGE<br /><br /> **必須**|マルチサブネット フェールオーバー クラスターについて、IP アドレス リソースの依存関係を OR に設定することを示します。 詳細については、「[[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] フェールオーバー クラスターでのノードの追加または削除 &#40;セットアップ&#41;](../../sql-server/failover-clusters/install/add-or-remove-nodes-in-a-sql-server-failover-cluster-setup.md)」を参照してください。 サポートされる値:<br /><br /> 0 = False (既定値)<br /><br /> 1 = True|  
|SQL Server エージェント|/AGTSVCACCOUNT<br /><br /> **必須**|SQL Server エージェント サービスのアカウントを指定します。|  
|SQL Server エージェント|/AGTSVCPASSWORD<br /><br /> [必須](#Accounts)|SQL Server エージェント サービス アカウントのパスワードを指定します。 管理されたサービス アカウント、仮想アカウント、またはビルトイン アカウントの使用時、このパラメーターは省略できます。|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASSVCACCOUNT<br /><br /> **必須**|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] サービスのアカウントを指定します。|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASSVCPASSWORD<br /><br /> [必須](#Accounts)|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] サービスのパスワードを指定します。 管理されたサービス アカウント、仮想アカウント、またはビルトイン アカウントの使用時、このパラメーターは省略できます。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLSVCACCOUNT<br /><br /> **必須**|SQL Server サービスの開始アカウントを指定します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLSVCPASSWORD<br /><br /> [必須](#Accounts)|SQLSVCACCOUNT のパスワードを指定します。 管理されたサービス アカウント、仮想アカウント、またはビルトイン アカウントの使用時、このパラメーターは省略できます。|  
|[!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)]|/ISSVCPASSWORD<br /><br /> [必須](#Accounts)|[!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] のパスワードを指定します。 管理されたサービス アカウント、仮想アカウント、またはビルトイン アカウントの使用時、このパラメーターは省略できます。|  
|[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]|/RSINSTALLMODE<br /><br /> **ファイル専用モードのみで使用可**| SQL Server 2017 以降では適用できなくなりました。  [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]のインストール モードを指定します。|  
|[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]|/RSSVCPASSWORD<br /><br /> [必須](#Accounts)|SQL Server 2017 以降では適用できなくなりました。  [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] サービスの開始アカウントのパスワードを指定します。 管理されたサービス アカウント、仮想アカウント、またはビルトイン アカウントの使用時、このパラメーターは省略できます。|  
  
##### <a name="additional-notes"></a>追加情報:  
 クラスター対応のコンポーネントは、 [!INCLUDE[ssDE](../../includes/ssde-md.md)] と [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] だけです。 他の機能はクラスターに対応していないので、フェールオーバーに対する可用性があまり高くありません。 
  
###### <a name="sample-syntax"></a>サンプル構文:  
 [!INCLUDE[ssDE](../../includes/ssde-md.md)] および [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]が配置された既存のフェールオーバー クラスター インスタンスにノードを追加するには 
  
```  
setup.exe /q /ACTION=AddNode /INSTANCENAME="<Insert Instance Name>" /SQLSVCACCOUNT="<SQL account that is used on other nodes>" /SQLSVCPASSWORD="<password for SQL account>" /AGTSVCACCOUNT="<SQL Server Agent account that is used on other nodes>", /AGTSVCPASSWORD="<SQL Server Agent account password>" /ASSVCACCOUNT="<AS account that is used on other nodes>" /ASSVCPASSWORD="<password for AS account>" /INDICATEPROGRESS /IACCEPTSQLSERVERLICENSETERMS /FAILOVERCLUSTERIPADDRESSES="IPv4;xx.xxx.xx.xx;ClusterNetwork1;xxx.xxx.xxx.x" /CONFIRMIPDEPENDENCYCHANGE=0  
```  
  
#### <a name="remove-node-parameters"></a>ノード削除パラメーター  
 次の表に示すパラメーターは、ノード削除用のコマンド ライン スクリプトを作成する場合に使用します。 フェールオーバー クラスターをアンインストールするには、各フェールオーバー クラスター ノードで RemoveNode を実行する必要があります。 詳細については、「[Always On フェールオーバー クラスター インスタンス &#40;SQL Server&#41;](../../sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server.md)」を参照してください。 
  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] コンポーネント|パラメーター|説明|  
|-----------------------------------------|---------------|-----------------|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/ACTION<br /><br /> **必須**|RemoveNode ワークフローを示すために必要です。<br /><br /> サポートされる値:**RemoveNode**|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/CONFIGURATIONFILE<br /><br /> **省略可能**|使用する [ConfigurationFile](./install-sql-server-using-a-configuration-file.md) を指定します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/HELP、?<br /><br /> **省略可能**|パラメーターの使用方法を表示します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/INDICATEPROGRESS<br /><br /> **省略可能**|詳細なセットアップ ログ ファイルがコンソールにパイプされるように指定します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/INSTANCENAME<br /><br /> **必須**|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] インスタンス名を指定します。<br /><br /> 詳細については、「 [Instance Configuration](../../sql-server/install/instance-configuration.md)」を参照してください。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/Q または /QUIET <br /><br /> **省略可能**|セットアップが、ユーザー インターフェイスなしで、非表示モードで実行されるように指定します。 このパラメーターは、自動インストールに使用されます。 /Q パラメーターによって /QS パラメーターの入力がオーバーライドされます。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/QS または /QUIETSIMPLE<br /><br /> **省略可能**|セットアップが UI を使用して実行され、UI を使用して進捗状況が表示されるように指定します。さらに、セットアップで入力ができないように、またはエラー メッセージが表示されないように指定します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/HIDECONSOLE<br /><br /> **省略可能**|コンソール ウィンドウを非表示にするか閉じる場合に指定します。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップ コントロール|/CONFIRMIPDEPENDENCYCHANGE<br /><br /> **必須**|マルチサブネット フェールオーバー クラスターについて、IP アドレス リソースの依存関係を OR から AND に設定することを示します。 詳細については、「[[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] フェールオーバー クラスターでのノードの追加または削除 &#40;セットアップ&#41;](../../sql-server/failover-clusters/install/add-or-remove-nodes-in-a-sql-server-failover-cluster-setup.md)」を参照してください。 サポートされる値:<br /><br /> 0 = False (既定値)<br /><br /> 1 = True|  
  
###### <a name="sample-syntax"></a>サンプル構文:  
 [!INCLUDE[ssDE](../../includes/ssde-md.md)] および [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]が配置された既存のフェールオーバー クラスター インスタンスからノードを削除するには 
  
```  
setup.exe /q /ACTION=RemoveNode /INSTANCENAME="<Insert Instance Name>" [/INDICATEPROGRESS] /CONFIRMIPDEPENDENCYCHANGE=0  
```  
  
##  <a name="service-account-parameters"></a><a name="Accounts"></a> サービス アカウント パラメーター  
 ビルトイン アカウント、ローカル アカウント、またはドメイン アカウントを使用して、SQL Server サービスを構成できます。 
  
> [!NOTE] 
> 管理されたサービス アカウント、仮想アカウント、またはビルトイン アカウントを使用する場合、対応するパスワード パラメーターを指定しないでください。 これらのサービス アカウントの詳細については、「[Windows サービス アカウントとアクセス許可の構成](../../database-engine/configure-windows/configure-windows-service-accounts-and-permissions.md)」の「 **[!INCLUDE[win7](../../includes/win7-md.md)] と [!INCLUDE[winserver2008r2](../../includes/winserver2008r2-md.md)] で利用可能な新しいアカウントの種類**」セクションをご覧ください。 
  
 サービス アカウント構成の詳細については、「[Windows サービス アカウントとアクセス許可の構成](../../database-engine/configure-windows/configure-windows-service-accounts-and-permissions.md)」をご覧ください。 
  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] コンポーネント|アカウント パラメーター|パスワード パラメーター|スタートアップの種類|  
|-----------------------------------------|-----------------------|------------------------|------------------|  
|SQL Server エージェント|/AGTSVCACCOUNT|/AGTSVCPASSWORD|/AGTSVCSTARTUPTYPE|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASSVCACCOUNT|/ASSVCPASSWORD|/ASSVCSTARTUPTYPE|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLSVCACCOUNT|/SQLSVCPASSWORD|/SQLSVCSTARTUPTYPE|  
|[!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)]|/ISSVCACCOUNT|/ISSVCPASSWORD|/ISSVCStartupType|  
|[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]|/RSSVCACCOUNT|/RSSVCPASSWORD|/RSSVCStartupType|  
  
  > [!NOTE]
  > Reporting Services の機能は SQL Server 2017 から削除されました。 SQL Server Reporting Services のアカウント パラメーターは、SQL Server 2017 より前のバージョンにのみ適用されます。 


##  <a name="feature-parameters"></a><a name="Feature"></a> 機能パラメーター  
 特定の機能をインストールするには、/FEATURES パラメーターを使用して、以下の表の親機能の値または機能の値を指定します。 SQL Server の各エディションでサポートされる機能の一覧については、「[[!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] の各エディションとサポートされている機能](../../sql-server/editions-and-components-of-sql-server-2016.md)」、「[[!INCLUDE[sssql17](../../includes/sssql17-md.md)] の各エディションとサポートされている機能](../../sql-server/editions-and-components-of-sql-server-2017.md)」、または「[[!INCLUDE[sssql19](../../includes/sssql19-md.md)] の各エディションとサポートされている機能](../../sql-server/editions-and-components-of-sql-server-version-15.md)」を参照してください。
  
|親機能パラメーター|機能パラメーター|説明|  
|:---|:---|:---|  
|SQL||[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]、レプリケーション、フルテキスト、および [!INCLUDE[ssDQSServer](../../includes/ssdqsserver-md.md)]をインストールします。|  
||SQLEngine|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]のみをインストールします。|  
||レプリケーション|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]と共にレプリケーション コンポーネントをインストールします。|  
||FullText|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]と共にフルテキスト コンポーネントをインストールします。|  
||DQ|[!INCLUDE[ssDQSServer](../../includes/ssdqsserver-md.md)] のインストールを完了するために必要なファイルをコピーします。 SQL Server のインストールが完了したら、DQSInstaller.exe ファイルを実行して、 [!INCLUDE[ssDQSServer](../../includes/ssdqsserver-md.md)] のインストールを完了させる必要があります。 詳細については、「 [Data Quality Server のインストールを完了するための DQSInstaller.exe の実行](../../data-quality-services/install-windows/run-dqsinstaller-exe-to-complete-data-quality-server-installation.md)」をご覧ください。 このパラメーターでは、 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]もインストールされます。|  
||PolyBase |PolyBase コンポーネントをインストールします。|
||PolyBaseCore | SQL Server 2019 以降では、Oracle、Teradata、SQL Server、その他のリレーショナル データと非リレーショナル データで標準 T-SQL ステートメントを使った十分に統合されたクエリを可能にする Polybase テクノロジをインストールするには、**PolyBase** と併用してください。 |
|| PolyBaseJava | SQL Server 2019 以降では、HDFS データで標準 T-SQL ステートメントを使った十分に統合されたクエリを可能にする PolyBase Java Connector をインストールするには、**PolyBase** と併用してください。
||AdvancedAnalytics |[SQL Server Machine Learning Services](../../machine-learning/install/sql-machine-learning-services-windows-install.md) または [SQL Server 2016 R Services (データベース内)](../../machine-learning/install/sql-r-services-windows-install.md) をインストールします。|  
||SQL_INST_MR |[SQL Server Machine Learning Services](../../machine-learning/install/sql-machine-learning-services-windows-install.md) (2017 以降) および [SQL Server 2016 R Services (データベース内)](../../machine-learning/install/sql-r-services-windows-install.md) に該当します。 R Open と専用の R パッケージをインストールするには、**AdvancedAnalytics** と併用してください。|  
||SQL_INST_MPY|[SQL Server Machine Learning Services](../../machine-learning/install/sql-machine-learning-services-windows-install.md) (2017 以降) に該当します。 Anaconda と専用の Python パッケージをインストールするには、**AdvancedAnalytics** と併用してください。|  
||SQL_INST_JAVA |[SQL Server Java 言語拡張](../../language-extensions/install/windows-java.md) (2019 以降) に該当します。 標準 T-SQL ステートメントを使った Java との統合を可能にする拡張機能をインストールするには、**AdvancedAnalytics** と併用してください。|  
|AS||すべての [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] コンポーネントをインストールします。|  
|RS||すべての [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] コンポーネントをインストールします。 SQL Server 2017 以降で削除されました。 |  
|RS_SHP||SharePoint 用の [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] コンポーネントをインストールします。 SQL Server 2017 以降で削除されました。|  
|RS_SHPWFE||SharePoint 製品用の [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] アドインをインストールします。 SQL Server 2017 以降で削除されました。 |  
|DQC||[!INCLUDE[ssDQSClient](../../includes/ssdqsclient-md.md)] がインストールされます。|  
|IS||すべての [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] コンポーネントをインストールします。|  
||IS_Master|Integration Services Scale Out のスケール アウト マスターが含まれています。| 
||IS_Worker|Integration Services Scale Out のスケール アウト ワーカーが含まれています。| 
|MDS||[!INCLUDE[ssMDSshort](../../includes/ssmdsshort-md.md)] がインストールされます。|  
|SQL_SHARED_MPY||[SQL Server 2017 Machine Learning Server (スタンドアロン)](../../machine-learning/install/sql-machine-learning-standalone-windows-install.md) の Python パッケージをインストールします。 |  
|SQL_SHARED_MR||[SQL Server 2016 R Server (スタンドアロン)](../../machine-learning/install/sql-machine-learning-standalone-windows-install.md) または [SQL Server Machine Learning Server (スタンドアロン)](../../machine-learning/install/sql-machine-learning-standalone-windows-install.md) の R パッケージをインストールします。 |  
|Tools*||クライアント ツールおよび SQL Server オンライン ブック コンポーネントをインストールします。|  
||BC|旧バージョンとの互換性コンポーネントをインストールします。|  
||Conn|接続コンポーネントをインストールします。|
||DREPLAY_CTLR|分散再生コントローラーをインストールします。|  
||DREPLAY_CLT|分散再生クライアントをインストールします。|  
||SNAC_SDK|[!INCLUDE[msCoName](../../includes/msconame-md.md)] SQL Server Native Client 用 SDK をインストールします。|  
||SDK|ソフトウェア開発キットをインストールします。|  
||LocalDB**|LocalDB (プログラムの開発者を対象とした [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] の実行モード) をインストールします。|  

*[!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] (SSMS) は、現在、[!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] のインストーラーとは別のスタンドアロン インストーラー内にあります。 詳細については、[SQL Server Management Studio (SSMS) のダウンロード](../../ssms/download-sql-server-management-studio-ssms.md)に関するページを参照してください。

### <a name="feature-parameter-examples"></a>機能パラメーターの例:
  
|パラメーターおよび値|説明| 
|---------------|-----------------|  
|/FEATURES=SQLEngine|レプリケーションおよびフルテキストなしの [!INCLUDE[ssDE](../../includes/ssde-md.md)] をインストールします。|  
|/FEATURES=SQLEngine,FullText|[!INCLUDE[ssDE](../../includes/ssde-md.md)] とフルテキストをインストールします。|  
|/FEATURES=SQL,Tools|完全な [!INCLUDE[ssDE](../../includes/ssde-md.md)] およびすべてのツールをインストールします。|  
|/FEATURES=BOL|ヘルプ コンテンツを表示および管理するための SQL Server オンライン ブック コンポーネントをインストールします。|  
|/FEATURES=SQLEngine,PolyBase|PolyBase エンジンをインストールします。|  
  
##  <a name="role-parameters"></a><a name="RoleParameters"></a> ロール パラメーター

セットアップ ロール パラメーター (/Role パラメーター) は、あらかじめ構成された機能の選択内容をインストールするために使用されます。 SSAS ロールでは、既存の SharePoint ファーム、または新しい未構成のファームのどちらかに SSAS インスタンスがインストールされます。 これらのシナリオをサポートするために、2 つのセットアップ ロールが用意されています。 インストールするために選択できるセットアップ ロールは一度に 1 つだけです。 セットアップ ロールを選択すると、そのロールに所属する機能とコンポーネントがセットアップによってインストールされます。 ロールに指定されている機能とコンポーネントは変更できません。 機能ロール パラメーターの使用方法の詳細については、「 [コマンド プロンプトからの Power Pivot のインストール](/analysis-services/instances/install-windows/install-analysis-services-in-power-pivot-mode)」をご覧ください。

AllFeatures_WithDefaults ロールは、 [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] のエディションの既定の動作であり、このロールを指定した場合は、ユーザーに対して表示されるダイアログ ボックスの数が減少します。 このロールは、 [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)]以外の SQL Server エディションをインストールするときに、コマンド ラインから指定できます。

|Role|説明|インストールされる機能|  
|----------|-----------------|---------------|  
|SPI_AS_ExistingFarm|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] を [!INCLUDE[ssGemini](../../includes/ssgemini-md.md)] 名前付きインスタンスとして、既存の [!INCLUDE[SPS2010](../../includes/sps2010-md.md)] ファームまたはスタンドアロン サーバーにインストールします。|メモリ内のデータの格納と処理用にあらかじめ構成された、[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] 計算エンジン。<br /><br /> [!INCLUDE[ssGemini](../../includes/ssgemini-md.md)] ソリューション パッケージ<br /><br /> [!INCLUDE[ssGeminiClient](../../includes/ssgeminiclient-md.md)] のインストーラー プログラム<br /><br /> SQL Server オンライン ブック|  
|SPI_AS_NewFarm|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] および [!INCLUDE[ssDE](../../includes/ssde-md.md)] を [!INCLUDE[ssGemini](../../includes/ssgemini-md.md)] の名前付きインスタンスとして、新しい未構成の Office [!INCLUDE[SPS2010](../../includes/sps2010-md.md)] ファームまたはスタンドアロン サーバーにインストールします。 SQL Server セットアップは、機能ロールのインストール時にファームを構成します。|メモリ内のデータの格納と処理用にあらかじめ構成された、[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] 計算エンジン。<br /><br /> [!INCLUDE[ssGemini](../../includes/ssgemini-md.md)] ソリューション パッケージ<br /><br /> SQL Server オンライン ブック<br /><br /> [!INCLUDE[ssDE](../../includes/ssde-md.md)]<br /><br /> 構成ツール (Configuration Tools)<br /><br /> [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)]|  
|AllFeatures_WithDefaults|現在のエディションで使用できるすべての機能をインストールします。<br /><br /> 現在のユーザーを SQL Server **sysadmin** 固定サーバー ロールに追加します。<br /><br /> [!INCLUDE[firstref_longhorn](../../includes/firstref-longhorn-md.md)] 以降を使用していて、そのオペレーティング システムがドメイン コントローラーでない場合、 [!INCLUDE[ssDE](../../includes/ssde-md.md)]と [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] は既定で NTAUTHORITY\NETWORK SERVICE アカウントを使用し、 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] は既定で NTAUTHORITY\NETWORK SERVICE アカウントを使用します。<br /><br /> このロールは、 [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)]のエディションで、既定で有効になっています。 その他のエディションの場合、このロールは有効になっていませんが、UI またはコマンドライン パラメーターを使用して指定できます。|[!INCLUDE[ssExpress](../../includes/ssexpress-md.md)]のエディションの場合は、そのエディションで使用できる機能のみがインストールされます。 その他のエディションの場合は、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のすべての機能がインストールされます。<br /><br /> **AllFeatures_WithDefaults** パラメーターは、**AllFeatures_WithDefaults** パラメーターの設定をオーバーライドする他のパラメーターと組み合わせることができます。 たとえば、 **AllFeatures_WithDefaults** パラメーターと **/Features=RS** パラメーターを組み合わせて使用すると、すべての機能をインストールするコマンドがオーバーライドされ [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]のみがインストールされますが、 **AllFeatures_WithDefaults** パラメーターを適用することで、 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]に対して既定のサービス アカウントが使用されます。<br /><br /> **AllFeatures_WithDefaults** パラメーターを **/ADDCURRENTUSERASSQLADMIN=FALSE** と共に使用すると、準備ダイアログには現在のユーザーに関する情報が自動入力されません。 SQL Server エージェントのサービス アカウントとパスワードを指定するには、 **/AGTSVCACCOUNT** と **/AGTSVCPASSWORD** を追加します。|

## <a name="controlling-failover-behavior-using-the-failoverclusterrollownership-parameter"></a><a name="RollOwnership"></a> /FAILOVERCLUSTERROLLOWNERSHIP パラメーターを使用したフェールオーバーの動作の制御  
[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] フェールオーバー クラスターをアップグレードするには、フェールオーバー クラスター ノードのパッシブ ノードから開始して、1 ノードごとにセットアップを実行する必要があります。 フェールオーバー クラスター インスタンスのノード総数と、アップグレード済みのノードの数との違いに応じて、セットアップがアップグレード済みのノードにフェールオーバーする時期が決まります。 ノード総数の半数以上がアップグレード済みの場合、既定のセットアップにより、アップグレード済みのノードにフェールオーバーが発生します。

アップグレード プロセス中にクラスター ノードのフェールオーバーの動作を制御するには、コマンド プロンプトでアップグレード操作を実行して /FAILOVERCLUSTERROLLOWNERSHIP パラメーターを使用し、アップグレード操作によってノードがオフラインになる前にフェールオーバーの動作を制御します。 このパラメーターの使用方法は次のとおりです。

- /FAILOVERCLUSTERROLLOWNERSHIP=0 を指定すると、クラスターの所有権 (グループの移動) がアップグレード済みのノードに移動しないため、このノードはアップグレード終了時に SQL Server クラスターの実行可能な所有者の一覧に追加されません。 

- /FAILOVERCLUSTERROLLOWNERSHIP=1 を指定すると、クラスターの所有権 (グループの移動) がアップグレードされたノードに移動するため、このノードはアップグレード終了時に SQL Server クラスターの実行可能な所有者の一覧に追加されます。 

- /FAILOVERCLUSTERROLLOWNERSHIP=2 は既定の設定です。 これは、このパラメーターが指定されていない場合に使用されます。 この設定は、SQL Server セットアップによってクラスターの所有権 (グループの移動) が必要に応じて管理されることを示しています。 

##  <a name="instance-id-or-instanceid-configuration"></a><a name="InstanceID"></a> インスタンス ID (InstanceID) の構成

インスタンス ID (/InstanceID) パラメーターは、インスタンス コンポーネントのインストール先と、インスタンスのレジストリ パスを指定するために使用されます。 INSTANCEID の値は文字列で、一意である必要があります。 

- SQL インスタンス ID: `MSSQLxx.<INSTANCEID>`

- AS インスタンス ID: `MSASxx.<INSTANCEID>`

- RS インスタンス ID: `MSRSxx.<INSTANCEID>`

インスタンス対応のコンポーネントは次の場所に格納されます。  

`%Program Files%\Microsoft SQL Server\<SQLInstanceID>`

`%Program Files%\Microsoft SQL Server\<ASInstanceID>`

`%Program Files%\Microsoft SQL Server\<RSInstanceID>`

> [!NOTE]
> INSTANCEID をコマンド ラインで指定しない場合、既定のセットアップでは、\<INSTANCEID> の代わりに \<INSTANCENAME> が使用されます。 

## <a name="see-also"></a>参照
- [インストール ウィザードからの SQL Server 2016 のインストール](../../database-engine/install-windows/install-sql-server-from-the-installation-wizard-setup.md)
- [SQL Server フェールオーバー クラスターのインストール](../../sql-server/failover-clusters/install/sql-server-failover-cluster-installation.md)
- [SQL Server 2016 のビジネス インテリジェンス機能のインストール](../../sql-server/install/install-sql-server-business-intelligence-features.md)
