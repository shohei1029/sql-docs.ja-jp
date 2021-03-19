---
title: Server Core への SQL Server のインストール | Microsoft Docs
description: Server Core インストールに SQL Server をインストールできます。 Server Core インストール オプションでは、特定のサーバー ロールを実行するための最低限の環境が提供されます。
ms.custom: ''
ms.date: 06/29/2020
ms.prod: sql
ms.reviewer: ''
ms.technology: install
ms.topic: conceptual
ms.assetid: 1dd294cc-5b69-4d0c-9005-3e307b75678b
author: cawrites
ms.author: chadam
monikerRange: '>=sql-server-2016'
ms.openlocfilehash: 5ceba605fc11adad95a720be8122bf995ec458f1
ms.sourcegitcommit: bf7577b3448b7cb0e336808f1112c44fa18c6f33
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104611082"
---
# <a name="install-sql-server-on-server-core"></a>Server Core への SQL Server のインストール

[!INCLUDE [SQL Server -Windows Only](../../includes/applies-to-version/sql-windows-only.md)]

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] は、Server Core インストールにインストールできます。   
  
Server Core インストール オプションでは、特定のサーバー ロールを実行するための最低限の環境が提供されます。 これにより、必要な保守と管理が減り、これらのサーバー ロールに対する攻撃の危険性が軽減されます。 Server Core の詳細については、「[Server Core のインストール](/windows-server/get-started/getting-started-with-server-core)」を参照してください。 [!INCLUDE[win8srv](../../includes/win8srv-md.md)] に実装された Server Core の詳細については、[Windows Server 2012 の Server Core](https://msdn.microsoft.com/library/hh846323\(VS.85\).aspx) に関するページ (https://msdn.microsoft.com/library/hh846323(VS.85).aspx) を参照してください。  
  
 現在サポートされているオペレーティング システムの一覧については、「[SQL Server のインストールに必要なハードウェアおよびソフトウェア](../../sql-server/install/hardware-and-software-requirements-for-installing-sql-server.md)」を参照してください。

## <a name="prerequisites"></a>前提条件  
  
|要件|インストール方法|  
|-----------------|--------------------|  
|[!INCLUDE[dnprdnshort](../../includes/dnprdnshort-md.md)] 4.6.1 |[!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] 以外のすべてのエディションの [!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] では、セットアップには、[!INCLUDE[dnprdnshort](../../includes/dnprdnshort-md.md)] 4.6.1 Server Core Profile が必要です。 まだインストールされていない場合、SQL Server セットアップを実行すると自動的にインストールされます。 インストールには再起動が必要です。 再起動を回避するには、セットアップを実行する前に [!INCLUDE[dnprdnshort](../../includes/dnprdnshort-md.md)] をインストールします。|  
|Windows インストーラー 4.5|Server Core インストールに付属しています。|  
|Windows PowerShell|Server Core インストールに付属しています。|  
|Java ランタイム |PolyBase を使用するには、適切な Java ランタイムをインストールする必要があります。 詳細については、「[PolyBase のインストール](../../relational-databases/polybase/polybase-installation.md)」を参照してください。|
  
##  <a name="supported-features"></a><a name="BK_SupportedFeatures"></a> サポートされている機能  
 Server Core インストールの [!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] でサポートされている機能については、次の表を参照してください。  
  
|機能|サポートされています|追加情報|  
|-------------|---------------|----------------------------|  
|[!INCLUDE[ssDE](../../includes/ssde-md.md)] サービス|はい||  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] レプリケーション|はい||  
|フルテキスト検索|はい||  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|はい||  
|[!INCLUDE[rsql_productname_md](../../includes/rsql-productname-md.md)]|はい||  
|[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]|いいえ||  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Data Tools (SSDT)|いいえ||  
|クライアント ツール接続|はい||  
|Integration Services サーバー|はい||  
|クライアント ツールの旧バージョンとの互換性|いいえ||  
|クライアント ツール SDK|いいえ||  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] オンライン ブック|いいえ||  
|管理ツール - 基本|リモートのみ|Server Core へのこれらの機能のインストールはサポートされていません。 これらのコンポーネントは、Server Core ではない別のサーバーにインストールし、Server Core にインストールされている [!INCLUDE[ssDE](../../includes/ssde-md.md)] サービスに接続できます。|  
|管理ツール - 完全|リモートのみ|Server Core へのこれらの機能のインストールはサポートされていません。 これらのコンポーネントは、Server Core ではない別のサーバーにインストールし、Server Core にインストールされている [!INCLUDE[ssDE](../../includes/ssde-md.md)] サービスに接続できます。|  
|分散再生コントローラー|いいえ||  
|分散再生クライアント|リモートのみ|Server Core へのこれらの機能のインストールはサポートされていません。 これらのコンポーネントは、Server Core ではない別のサーバーにインストールし、Server Core にインストールされている [!INCLUDE[ssDE](../../includes/ssde-md.md)] サービスに接続できます。|  
|SQL クライアント接続 SDK|はい||  
|Microsoft Sync Framework|はい|Microsoft Sync Framework は、 [!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] インストール パッケージに含まれていません。 適切なバージョンの Sync Framework は、この [Microsoft ダウンロード センター ページ](https://go.microsoft.com/fwlink/?LinkId=221788) (https://go.microsoft.com/fwlink/?LinkId=221788) からダウンロードして、Server Core を実行しているコンピューターにインストールできます。|  
|[!INCLUDE[ssMDSshort](../../includes/ssmdsshort-md.md)]|いいえ||  
|[!INCLUDE[ssDQSnoversion](../../includes/ssdqsnoversion-md.md)]|いいえ||  
  
## <a name="supported-scenarios"></a>サポートされるシナリオ  
 次の表では、Server Core に [!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] をインストールする場合のサポートされるシナリオのマトリックスを示します。  
  
| インストール | 有効なターゲット |  
|-|-|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のエディション|すべての [!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] 64 ビット エディション |  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の言語|すべての言語|  
|OS の言語とロケール (組み合わせ) での[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の言語|JPN (日本語) Windows への ENG [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]<br /><br /> GER (ドイツ語) Windows への ENG [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]<br /><br /> CHS (中国語 - 中国) Windows への ENG [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]<br /><br /> ARA (アラビア語 (SA)) Windows への ENG [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]<br /><br /> THA (タイ語) Windows への ENG [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]<br /><br /> TRK (トルコ語) Windows への ENG [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]<br /><br /> pt-PT (ポルトガル語 - ポルトガル) Windows への ENG [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]<br /><br /> ENG (英語) Windows への ENG [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]|  
|Windows のエディション|Windows Server 2019 Datacenter <br/><br/> Windows Server 2019 Standard <br /><br />  [!INCLUDE[winserver2016_datacenter_md](../../includes/winserver2016-datacenter-md.md)]<br/><br/>[!INCLUDE[winserver2016_standard_md](../../includes/winserver2016-standard-md.md)]<br/><br/>[!INCLUDE[win8srv](../../includes/win8srv-md.md)] R2 Datacenter<br/><br/>[!INCLUDE[win8srv](../../includes/win8srv-md.md)] R2 Standard<br/><br/>[!INCLUDE[win8srv](../../includes/win8srv-md.md)] R2 Essentials<br/><br/>[!INCLUDE[win8srv](../../includes/win8srv-md.md)] R2 Foundation<br/><br/>[!INCLUDE[win8srv](../../includes/win8srv-md.md)] Datacenter<br/><br/>[!INCLUDE[win8srv](../../includes/win8srv-md.md)] Standard<br/><br/>[!INCLUDE[win8srv](../../includes/win8srv-md.md)] Essentials<br/><br/>[!INCLUDE[win8srv](../../includes/win8srv-md.md)] Foundation|  
  
## <a name="upgrade"></a>アップグレード 
 Server Core インストールでは、 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] から [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] へのアップグレードがサポートされています。  
  
## <a name="install"></a>インストール  
 [!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] では、Server Core オペレーティング システムでのインストール ウィザードを使用したセットアップはサポートされていません。 Server Core にインストールする場合、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップでは、/Q パラメーターを使用した非表示モード、または /QS パラメーターを使用した簡易非表示モードがサポートされます。 詳細については、「 [コマンド プロンプトからの SQL Server のインストール](./install-sql-server-from-the-command-prompt.md)」を参照してください。  
  
 どのインストール方法を使用するかにかかわらず、個人として、または組織を代表して、ソフトウェア ライセンス条項に同意するかどうかを確認する必要があります。ただし、ソフトウェアの使用に別の契約 ( [!INCLUDE[msCoName](../../includes/msconame-md.md)] ボリューム ライセンス契約、ISV や OEM とのサード パーティ契約など) が適用される場合を除きます。  
  
 ライセンス条項は、セットアップのユーザー インターフェイスで確認し、同意することができます。 /Q または /QS パラメーターを使用した自動インストールでは、/IACCEPTSQLSERVERLICENSETERMS パラメーターを指定する必要があります。 ライセンス条項は、「 [マイクロソフト ソフトウェア ライセンス条項](https://go.microsoft.com/fwlink/?LinkId=148209)」で別途確認できます。  
  
> [!NOTE]  
>  ソフトウェアの入手方法 ( [!INCLUDE[msCoName](../../includes/msconame-md.md)] のボリューム ライセンスを通じて入手した場合など) によっては、ソフトウェアの使用に追加の条件が課されることがあります。  
  
 特定の機能をインストールするには、/FEATURES パラメーターを使用して、親機能の値または機能の値を指定します。 機能パラメーターとその使用の詳細については、次のセクションを参照してください。  
  
### <a name="feature-parameters"></a>機能パラメーター  
  
|機能パラメーター|説明|  
|-----------------------|-----------------|  
|SQLENGINE|[!INCLUDE[ssDE](../../includes/ssde-md.md)]のみをインストールします。|  
|レプリケーション|[!INCLUDE[ssDE](../../includes/ssde-md.md)]と共にレプリケーション コンポーネントをインストールします。|  
|FULLTEXT|[!INCLUDE[ssDE](../../includes/ssde-md.md)]と共にフルテキスト コンポーネントをインストールします。|  
|AS|すべての [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] コンポーネントをインストールします。|  
|IS|すべての [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] コンポーネントをインストールします。|  
|CONN|接続コンポーネントをインストールします。| 
|ADVANCEDANALYTICS |R Services をインストールします。データベース エンジンが必要です。 自動インストールでは、/IACCEPTROPENLICENSETERMS パラメーターが必要です。  |


 機能パラメーターの使い方の例を参照してください。  
  
|パラメーターおよび値|説明|  
|--------------------------|-----------------|  
|/FEATURES=SQLEngine|[!INCLUDE[ssDE](../../includes/ssde-md.md)]のみをインストールします。|  
|/FEATURES=SQLEngine,FullText|[!INCLUDE[ssDE](../../includes/ssde-md.md)] とフルテキストをインストールします。|  
|/FEATURES=SQLEngine,Conn|[!INCLUDE[ssDE](../../includes/ssde-md.md)] および接続コンポーネントをインストールします。|  
|/FEATURES=SQLEngine,AS,IS,Conn|[!INCLUDE[ssDE](../../includes/ssde-md.md)]、 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]、 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)]、および接続コンポーネントをインストールします。|  
|/FEATURES=SQLENGINE,ADVANCEDANALYTICS /IACCEPTROPENLICENSETERMS |[!INCLUDE[ssDE](../../includes/ssde-md.md)] と [!INCLUDE[rsql_productname_md](../../includes/rsql-productname-md.md)]をインストールします。|  

  
### <a name="installation-options"></a>インストール オプション  
 Server Core オペレーティング システムへの [!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] のインストール中に、セットアップで次のインストール オプションをサポートします。  
  
1.  **コマンド ラインからのインストール**  
  
     コマンド プロンプトのインストール オプションを使用して特定の機能をインストールするには、/FEATURES パラメーターを使用して、親機能の値または機能の値を指定します。 コマンド ラインからパラメーターを使用する方法の例を次に示します。  
  
    ```console
    Setup.exe /qs /ACTION=Install /FEATURES=SQLEngine,Replication /INSTANCENAME=MSSQLSERVER /SQLSVCACCOUNT="<DomainName\UserName>" /SQLSVCPASSWORD="<StrongPassword>" /SQLSYSADMINACCOUNTS="<DomainName\UserName>" /AGTSVCACCOUNT="NT AUTHORITY\Network Service" /TCPENABLED=1 /IACCEPTSQLSERVERLICENSETERMS  
    ```  
  
2.  **構成ファイルを使用したインストール**  
  
     セットアップでは、コマンド プロンプトからのみ構成ファイルを使用できます。 構成ファイルは、パラメーター (名前と値のペア) と説明のコメントの基本構造を持つテキスト ファイルです。 コマンド プロンプトで指定した構成ファイルには、.INI のファイル名拡張子が必要です。 次の例の ConfigurationFile.INI を参照してください。  
  
    - [!INCLUDE[ssDE](../../includes/ssde-md.md)]のインストール。 
    
    次の例では、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)][!INCLUDE[ssDE](../../includes/ssde-md.md)]を含む新しいスタンドアロン インスタンスをインストールする方法を示します。  

    ```console
    ; SQL Server Configuration File  
    [OPTIONS]  
    
    ; Specifies a Setup work flow, like INSTALL, UNINSTALL, or UPGRADE. This is a required parameter.   
    
    ACTION="Install"  
    
    ; Specifies features to install, uninstall, or upgrade. The lists of features include SQLEngine, FullText, Replication, AS, IS, and Conn.   
    
    FEATURES=SQLENGINE  
    
    ; Specify a default or named instance. MSSQLSERVER is the default instance for non-Express editions and SQLExpress for Express editions. This parameter is required when installing the ssNoVersion Database Engine, and Analysis Services (AS).  
    
    INSTANCENAME="MSSQLSERVER"  
    
    ; Specify the Instance ID for the ssNoVersion features you have specified. ssNoVersion directory structure, registry structure, and service names will incorporate the instance ID of the ssNoVersion instance.   
    
    INSTANCEID="MSSQLSERVER"  
    
    ; Account for ssNoVersion service: Domain\User or system account.   
    
    SQLSVCACCOUNT="NT Service\MSSQLSERVER"  
    
    ; Windows account(s) to provision as ssNoVersion system administrators.   
    
    SQLSYSADMINACCOUNTS="\<DomainName\UserName>"  
    
    ; Accept the License agreement to continue with Installation  
    
    IAcceptSQLServerLicenseTerms="True"  
    
    ```

    -   接続コンポーネントのインストール。 次の例では、接続コンポーネントをインストールする方法を示します。  
  
        ```console
        ; SQL Server Configuration File  
        [OPTIONS]  
  
        ; Specifies a Setup work flow, like INSTALL, UNINSTALL, or UPGRADE. This is a required parameter.   
  
        ACTION="Install"  
  
        ; Specifies features to install, uninstall, or upgrade. The lists of features include SQLEngine, FullText, Replication, AS, IS, and Conn.   
  
        FEATURES=Conn  
  
        ; Specifies acceptance of License Terms  
  
        IAcceptSQLServerLicenseTerms="True  
  
        ```  
  
    -   サポートされるすべての機能のインストール  
  
        次の例では、 [!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] のサポートされる全機能を Server Core にインストールする方法を示します。  
  
        ```console
        ; SQL Server Configuration File  
        [OPTIONS]  
        ; Specifies a Setup work flow, like INSTALL, UNINSTALL, or UPGRADE. This is a required parameter.   
  
        ACTION="Install"  
  
        ; Specifies features to install, uninstall, or upgrade. The lists of features include SQLEngine, FullText, Replication, AS, IS, and Conn.   
  
        FEATURES=SQLENGINE,FullText,Replication,AS,IS,Conn  
  
        ; Specify a default or named instance. MSSQLSERVER is the default instance for non-Express editions and SQLExpress for Express editions. This parameter is required when installing the ssNoVersion Database Engine (SQL), or Analysis Services (AS).  
  
        INSTANCENAME="MSSQLSERVER"  
  
        ; Specify the Instance ID for the ssNoVersion features you have specified. ssNoVersion directory structure, registry structure, and service names will incorporate the instance ID of the ssNoVersion instance.   
  
        INSTANCEID="MSSQLSERVER"  
  
        ; Account for ssNoVersion service: Domain\User or system account.   
  
        SQLSVCACCOUNT="NT Service\MSSQLSERVER"  
  
        ; Windows account(s) to provision as ssNoVersion system administrators.   
  
        SQLSYSADMINACCOUNTS="\<DomainName\UserName>"  
  
        ; The name of the account that the Analysis Services service runs under.   
  
        ASSVCACCOUNT= "NT Service\MSSQLServerOLAPService"  
  
        ; Specifies the list of administrator accounts that need to be provisioned.   
  
        ASSYSADMINACCOUNTS="\<DomainName\UserName>"  
  
        ; Specifies the server mode of the Analysis Services instance. Valid values are MULTIDIMENSIONAL, POWERPIVOT or TABULAR. ASSERVERMODE is case-sensitive. All values must be expressed in upper case.   
  
        ASSERVERMODE="MULTIDIMENSIONAL"  
  
        ; Optional value, which specifies the state of the TCP protocol for the ssNoVersion service. Supported values are: 0 to disable the TCP protocol, and 1 to enable the TCP protocol.  
  
        TCPENABLED=1  
  
        ;Specifies acceptance of License Terms  
  
        IAcceptSQLServerLicenseTerms="True"  
        ```  
  
     以下に、カスタムまたは既定の構成ファイルを使用してセットアップを起動する方法を示します。  
  
    -   カスタム構成ファイルを使用したセットアップの起動:  
  
         コマンド プロンプトで構成ファイルを指定するには  
  
        ```console
        Setup.exe /QS /ConfigurationFile=MyConfigurationFile.INI  
        ```  
  
         構成ファイルの代わりにコマンド プロンプトでパスワードを指定するには  
  
        ```console
        Setup.exe /QS /SQLSVCPASSWORD="************" /ASSVCPASSWORD="************"  /ConfigurationFile=MyConfigurationFile.INI  
        ```  
  
    -   DefaultSetup.ini を使用したセットアップの起動:  
  
         [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ソース メディアのルート レベルの \x86 および \x64 フォルダーに DefaultSetup.ini ファイルがある場合は、DefaultSetup.ini ファイルを開き、 *Features* パラメーターをファイルに追加します。  
  
         DefaultSetup.ini ファイルが存在しない場合は、作成し、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ソース メディアのルート レベルの \x86 および \x64 フォルダーにコピーできます。  
  
## <a name="configure-remote-access-of-ssnoversion-on-server-core"></a>Server Core で [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のリモート アクセスを構成する  
 Server Core で実行している [!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] インスタンスのリモート アクセスを構成するには、以下で説明する操作を実行します。  
  
### <a name="enable-remote-connections-on-the-instance-of-ssnoversion"></a>のインスタンスでリモート接続を有効にする [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]  

リモート接続を有効にするには、SQLCMD.exe をローカルで使用して、Server Core インスタンスに対して次のステートメントを実行します。  

```sql
EXEC sys.sp_configure N'remote access', N'1'  
GO
RECONFIGURE WITH OVERRIDE
GO
```  
  
### <a name="enable-and-start-the-ssnoversion-browser-service"></a>[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] browser service  
 Browser サービスは、既定では無効になっています。  Server Core で実行している [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンスで無効になっている場合、このサービスを有効にするには、コマンド プロンプトから次のコマンドを実行します。  
  
 `sc config SQLBROWSER start= auto`  
  
 有効にした後は、コマンド プロンプトから次のコマンドを実行して、サービスを開始します。  
  
 `net start SQLBROWSER`  
  
### <a name="create-exceptions-in-windows-firewall"></a>Windows ファイアウォールで例外を作成する  
 Windows ファイアウォールで [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] アクセスの例外を作成するには、「 [SQL Server のアクセスを許可するための Windows ファイアウォールの構成](../../sql-server/install/configure-the-windows-firewall-to-allow-sql-server-access.md)」で指定されている手順に従います。  
  
### <a name="enable-tcpip-on-the-instance-of-ssnoversion"></a>[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]  
 Server Core 上の [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンスに対して TCP/IP プロトコルを有効にするには、Windows PowerShell を使用します。 次の手順に従います。  
  
1.  PowerShell では次のとおりです。Import-Module SQLPS  
  
2.  **[Microsoft [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Powershell]** ウィンドウで、次のスクリプトを実行して TCP/IP プロトコルを有効にします。  
  
```powershell  
$smo = 'Microsoft.SqlServer.Management.Smo.'  
$wmi = new-object ($smo + 'Wmi.ManagedComputer')  
# Enable the TCP protocol on the default instance.  If the instance is named, replace MSSQLSERVER with the instance name in the following line.  
$uri = "ManagedComputer[@Name='" + (get-item env:\computername).Value + "']/ServerInstance[@Name='MSSQLSERVER']/ServerProtocol[@Name='Tcp']"  
$Tcp = $wmi.GetSmoObject($uri)  
$Tcp.IsEnabled = $true  
$Tcp.Alter()  
$Tcp  
```  
  
## <a name="uninstall"></a>アンインストール

 Server Core を実行しているコンピューターにログオンした後は、管理者コマンド プロンプトのデスクトップ環境に制限されます。 [!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] のアンインストールを起動するときに、このコマンド プロンプトを使用できます。 [!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)]のインスタンスをアンインストールするには、/Q パラメーターを使用した非表示モード、または /QS パラメーターを使用した簡易非表示モードで、コマンド プロンプトからアンインストールを起動します。 /QS パラメーターを指定すると、UI に進捗状況が表示されますが、入力は受け付けられません。 /Q は、ユーザー インターフェイスのない非表示モードで実行します。  
  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]の既存のインスタンスをアンインストールするには、次のコマンドを実行します。  
  
```console
Setup.exe /Q /Action=Uninstall /FEATURES=SQLEngine,AS,IS /INSTANCENAME=MSSQLSERVER  
```  
  
 名前付きインスタンスを削除する場合は、前に示した例の `MSSQLSERVER` の代わりにインスタンス名を指定します。  
  
## <a name="start-a-new-command-prompt"></a>新しいコマンド プロンプトを起動する

誤ってコマンド プロンプトを閉じた場合は、次の手順で新しいコマンド プロンプトを開始できます。  
 
1.  Ctrl + Shift + Esc キーを押して、タスク マネージャーを表示します。  
2.  **[アプリケーション]** タブで、 **[新しいタスク]** をクリックします。  
3.  **[新しいタスクの作成]** ダイアログ ボックスで、 **[名前]** フィールドに「 **cmd** 」と入力して、 [!INCLUDE[clickOK](../../includes/clickok-md.md)]。  
  
## <a name="see-also"></a>関連項目  
 [構成ファイルを使用した SQL Server のインストール](./install-sql-server-using-a-configuration-file.md)   
 [コマンド プロンプトからの SQL Server のインストール](./install-sql-server-from-the-command-prompt.md)   
 [エディションと SQL Server 2017 のサポートされる機能](../../sql-server/editions-and-components-of-sql-server-2017.md)   
 [Server Core のインストール](/windows-server/get-started/getting-started-with-server-core)   
 [Sconfig.cmd を使用して Windows Server 2016 の Server Core インストールを構成する](/windows-server/get-started/sconfig-on-ws2016)   
 [Windows PowerShell でのフェールオーバー クラスター コマンドレット](/powershell/module/failoverclusters/)

  
