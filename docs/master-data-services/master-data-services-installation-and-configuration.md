---
title: インストールと構成
description: Windows Server 2012 R2 コンピューターにマスターデータサービスをインストールする方法、MDS データベースと web サイトを構成する方法、およびサンプルモデルとデータを配置する方法について説明します。
ms.custom: ''
ms.date: 07/01/2020
ms.prod: sql
ms.prod_service: mds
ms.reviewer: ''
ms.technology: master-data-services
ms.topic: quickstart
ms.assetid: f6cd850f-b01b-491f-972c-f966b9fe4190
author: lrtoyou1223
ms.author: lle
ms.openlocfilehash: 1d12236bbfd3af474e883c23b975d357e0fd27a8
ms.sourcegitcommit: efce0ed7d1c0ab36a4a9b88585111636134c0fbb
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104833871"
---
# <a name="master-data-services-installation-and-configuration"></a>マスター データ サービスのイントールと構成

[!INCLUDE [SQL Server - Windows only ASDBMI  ](../includes/applies-to-version/sql-windows-only-asdbmi.md)]

  この記事では、Windows Server 2012 R2 コンピューターへの [!INCLUDE[ssMDSshort_md](../includes/ssmdsshort-md.md)] のインストール方法、MDS データベースと Web サイトの設定方法、およびサンプル モデルとデータの配置方法について説明します。 [!INCLUDE[ssMDSshort](../includes/ssmdsshort-md.md)] (MDS) では、組織が信頼されたバージョンのデータを管理できるようにします。   
  
> [!NOTE] 
> [!INCLUDE[ssMDSshort_md](../includes/ssmdsshort-md.md)] をサポートするようになった Developer Edition を使用しているときに Windows 10 マシン上に [!INCLUDE[ssMDSshort_md](../includes/ssmdsshort-md.md)] をインストールすることができます。 
>>さまざまなエディションのオペレーティングシステムのサポートの詳細については、 [SQL Server 2019: ハードウェアとソフトウェアの要件](../sql-server/install/hardware-and-software-requirements-for-installing-sql-server-ver15.md)」を参照してください。

[!INCLUDE[ssMDSshort_md](../includes/ssmdsshort-md.md)]のデータを整理する方法の概要については、 [マスター データ サービスの概要 (MDS)](../master-data-services/master-data-services-overview-mds.md)を参照してください。     
  
 新機能の詳細については、「 [マスターデータサービス &#40;MDS&#41;の新 ](../master-data-services/what-s-new-in-master-data-services-mds.md)機能」を参照してください。  
 
[!INCLUDE[ssMDSshort_md](../includes/ssmdsshort-md.md)]を学習するためのビデオと他のトレーニング リソースへのリンクについては、「 [マスター データ サービスについて学習する](../master-data-services/learn-sql-server-master-data-services.md)」をご覧ください。 
  
> **ダウンロード**  
> -   ダウンロードするに [!INCLUDE[ssnoversion](../includes/ssnoversion-md.md)] は、「 **[ [!INCLUDE[ssnoversion](../includes/ssnoversion-md.md)] ダウンロード](https://www.microsoft.com/sql-server/sql-server-downloads)**」を参照してください。
> -   Azure アカウントをすでにお持ちですか?  次に、SQL Server が既にインストールされている仮想マシンを起動するために **[ [!INCLUDE[ssnoversion](../includes/ssnoversion-md.md)] 、Azure portal の Windows 仮想マシンで作成するクイックスタートに関する](/azure/azure-sql/virtual-machines/windows/sql-vm-create-portal-quickstart)** ページに進んでください。  
> 
> **MDS Web サイトを作成できませんか?**
> >この問題を解決する方法については、この Microsoft サポートの記事を参照してください。
> [SQL Server 2016 の低い特権のアカウントでは、MDS Web サイトを作成することはできません。](https://aka.ms/mdssupport) 

## <a name="internet-explorer-and-silverlight"></a>Internet Explorer と Silverlight
- Windows Server 2012 マシンに [!INCLUDE[ssMDSshort_md](../includes/ssmdsshort-md.md)] をインストールするときに、Web アプリケーション サイトでスクリプトを許可するように Internet Explorer セキュリティ強化を構成しなければならない場合があります。 そうしないと、サーバー コンピューター上のサイトの参照が失敗します。
- Web アプリケーションで作業するには、Silverlight 5 をクライアント コンピューターにインストールする必要があります。 Silverlight の必要なバージョンがない場合、Web アプリケーションで Silverlight を使用する部分に移動したときに、Silverlight をインストールするよう要求されます。 Silverlight 5 は **[ここ](https://www.microsoft.com/silverlight/)** からインストールできます。

## <a name="ssmdsshort_md-on-an-azure-virtual-machine"></a>Azure 仮想マシン上の [!INCLUDE[ssMDSshort_md](../includes/ssmdsshort-md.md)]
既定では、が既にインストールされている Azure 仮想マシンを起動すると [!INCLUDE[ssnoversion_md](../includes/ssnoversion-md.md)] 、 [!INCLUDE[ssMDSshort_md](../includes/ssmdsshort-md.md)] もインストールされます。 

次の手順として、インターネット インフォメーション サービス (IIS) をインストールします。 「[IIS のインストールと構成](#InstallIIS)」を参照してください。 

[!INCLUDE[ssnoversion_md](../includes/ssnoversion-md.md)] のインストールを変更する場合は、既定の場所 (`<drive>`: \SQLServer_13.0_Full) にある setup.exe ファイルを見つけます。
  
## <a name="installing-master-data-services"></a><a name="InstallMDS"></a> Master Data Services のインストール  
 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] のセットアップ インストール ウィザードまたはコマンド プロンプトを使用して、 [!INCLUDE[ssMDSshort](../includes/ssmdsshort-md.md)]をインストールします。  
  
 **[!INCLUDE[ssMDSshort](../includes/ssmdsshort-md.md)]セットアップを使用して [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] Windows Server コンピューターにインストールするには**  
  
1.  Setup.exe をダブルクリックして、インストール ウィザードの手順を実行します。  
  
2.  [!INCLUDE[ssMDSshort](../includes/ssmdsshort-md.md)] [機能の選択] **ページで、** [共有機能] **の [**] を選択します。  
  
     これは [!INCLUDE[ssMDScfgmgr](../includes/ssmdscfgmgr-md.md)]、アセンブリ、Windows PowerShell スナップインのほか、Web アプリケーションおよび Web サービス用のフォルダーおよびファイルがインストールされます。  
  
     ![mds_SQLServer2016Setup_FeatureSelection](../master-data-services/media/mds-sqlserver2016setup-featureselection.png "mds_SQLServer2016Setup_FeatureSelection")  
  
3.  ウィザードの指示に従って操作します。  

## <a name="installing-and-configuring-iis"></a><a name="InstallIIS"></a> IIS のインストールと構成
  
1.  [!INCLUDE[winblue_server_2](../includes/winblue-server-2-md.md)]で、 **デスクトップ** のタスクバーにある **[サーバー マネージャー]** アイコンをクリックします。  
  
     ![Windows Server 2012 タスクバーのサーバーマネージャーのアイコン](../master-data-services/media/mds-windowsservertaskbar-servermanagericon.png "Windows Server 2012 タスクバーのサーバーマネージャーのアイコン")  
  
5.  **サーバー マネージャー** で、 **[管理]** メニューの **[役割と機能の追加]** をクリックします。  
   
     ![[サーバー管理] の [役割と機能の追加] メニューコマンド](../master-data-services/media/mds-servermanagerdashboard-addrolesfeaturesmenu.png "[サーバー管理] の [役割と機能の追加] メニューコマンド")  
  
6.  **役割と機能の追加ウィザード** の **[インストールの種類]** ページで、既定値 (**役割ベースまたは機能ベースのインストール**) を承諾して、 **[次へ]** をクリックします。  
  
7.  **[サーバー プールからサーバーを選択]** をクリックして、 [!INCLUDE[ssMDSshort](../includes/ssmdsshort-md.md)]をインストールしたサーバーをクリックします。  
  
     ![mds_AddRolesFeaturesWizard_ServerSelectionPage](../master-data-services/media/mds-addrolesfeatureswizard-serverselectionpage.png) 
  
8. **[サーバーの役割]** ページで、**[Web サーバー]** をクリックし、**[次へ]** をクリックします。 

   ![mds_AddRolesFeaturesWizard_ServerRolesPage](../master-data-services/media/mds-addrolesfeatureswizard-serverrolespage.png)
   
9. **[機能]** ページで、次の機能が選択されていることを確認し、**[次へ]** をクリックします。 これらの機能は、[!INCLUDE[winblue_server_2_md](../includes/winblue-server-2-md.md)] の [!INCLUDE[ssMDSshort_md](../includes/ssmdsshort-md.md)] に必要です。
  
    |特徴|特徴|  
    |--------------|--------------|  
    |![mds_AddRolesFeaturesWizard_FeaturesPage](../master-data-services/media/mds-addrolesfeatureswizard-featurespage.png)|![mds_AddRolesFeaturesWizard_FeaturesPage_WindowsProcActive](../master-data-services/media/mds-addrolesfeatureswizard-featurespage-windowsprocactive.png)|  

10. 左側のウィンドウで **[Web サーバーの役割 (IIS)]** をクリックし、**[役割サービス]** をクリックします。
11. **[役割サービス]** ページで、次の機能が選択されていることを確認し、**[次へ]** をクリックします。 これらのサービスは、[!INCLUDE[winblue_server_2](../includes/winblue-server-2-md.md)] の [!INCLUDE[ssMDSshort](../includes/ssmdsshort-md.md)] に必要です。

    > [!WARNING]  
    >  [WebDAV 発行] 役割サービスをインストールしないでください。 WebDAV 発行は、 [!INCLUDE[ssMDSshort](../includes/ssmdsshort-md.md)]と互換性がありません。  
  
     |役割サービス|役割サービス|  
    |-----------------------------|-----------------------------|  
    |![mds_AddRolesFeaturesWizard_RoleServicesPage](../master-data-services/media/mds-addrolesfeatureswizard-roleservicespage.png)|![mds_AddRolesFeaturesWizard_RoleServicesPage_PerformSecurity](../master-data-services/media/mds-addrolesfeatureswizard-roleservicespage-performsecurity.png)|  
    |![mds_AddRolesFeaturesWizard_RoleServicesPage_AppDevsection](../master-data-services/media/mds-addrolesfeatureswizard-roleservicespage-appdevsection.png)|![mds_AddRolesFeaturesWizard_RoleServicesPage_ManageToolssection](../master-data-services/media/mds-addrolesfeatureswizard-roleservicespage-managetoolssection.png)|  
    |||  
  
     他のオペレーティング システムで必須の機能と役割サービスの一覧は、「[Web アプリケーションの要件 &#40;マスター データ サービス&#41;](../master-data-services/install-windows/web-application-requirements-master-data-services.md)」を参照してください。   
  
 セットアップを使用した [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] のインストールの詳細については、「[インストール ウィザードからの SQL Server 2016 のインストール (セットアップ)](../database-engine/install-windows/install-sql-server-from-the-installation-wizard-setup.md)」を参照してください。  
  
 コマンド プロンプトを使用した [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] のインストールの詳細については、「 [コマンド プロンプトから SQL Server 2016 をインストール](../database-engine/install-windows/install-sql-server-from-the-command-prompt.md)」を参照してください。 コマンド プロンプトを使用する場合、 [!INCLUDE[ssMDSshort](../includes/ssmdsshort-md.md)] は機能パラメーターとして使用できるようになります。  
  
 インストール前のタスクに関する追加情報へのリンクの簡単な説明については、「 [マスター データ サービスをインストールする](../master-data-services/install-windows/install-master-data-services.md)」を参照してください。  
  
##  <a name="setting-up-the-database-and-website"></a><a name="SetUpWeb"></a> データベースと Web サイトを設定する  
 **を使用してデータベースと web サイトを設定するには [!INCLUDE[ssMDScfgmgr](../includes/ssmdscfgmgr-md.md)]**  

 
> [!WARNING]
>  [!INCLUDE[ssMDSshort_md](../includes/ssmdsshort-md.md)] Configuration Manager を起動する前に [IIS をインストール](#InstallIIS)する必要があります。 それ以外の場合、Configuration Manager にインターネット インフォメーション サービス エラーが表示され、[!INCLUDE[ssMDSshort_md](../includes/ssmdsshort-md.md)] Web アプリケーションを作成できなくなります。  
> 
> **ブラウザーの要件**
> >[!INCLUDE[ssMDSshort_md](../includes/ssmdsshort-md.md)] Web アプリケーションは、Internet Explorer (IE) 9 以降でのみ動作します。 IE 8 以前のバージョン、Microsoft Edge、Chrome はサポートされていません。    
  
1.  [!INCLUDE[ssMDScfgmgr](../includes/ssmdscfgmgr-md.md)]を起動して、 **[データベース構成]** をクリックします。  
  
2.  **[データベースの作成]** をクリックして、 **データベースの作成ウィザード** で **[次へ]** をクリックします。  
  
3.  [ **データベースサーバー** ] ページで、SQL Server インスタンスを指定します。 

    >  [!INCLUDE[sqlv15](../includes/sssql19-md.md)] SQL Server Managed Instance のサポートを追加します。 **SQL Server インスタンス** の値をマネージインスタンスのホストに設定します。 たとえば、「 `xxxxxx.xxxxxx.database.windows.net` 」のように入力します。

4. 認証の **種類** を選択し、[ **接続のテスト** ] をクリックして、選択した認証の種類の資格情報を使用してデータベースに接続できることを確認します。 **[次へ]** をクリックします。

    >[!INCLUDE[sqlv15](../includes/sssql19-md.md)]でマネージインスタンスに接続するには、次のいずれかの認証の種類を使用します。
    >
    >- Azure Active Directory 統合認証: **現在のユーザー– Active Directory 統合**
    >- SQL Server 認証: **SQL Server アカウント**。
    >
    >SQL Managed Instance では、ユーザーは固定サーバーロールのメンバーである必要があり `sysadmin` ます。

    > [!NOTE]  
    >  認証の種類として [ **現在のユーザー-統合セキュリティ** ] を選択すると、[ **ユーザー名** ] ボックスは読み取り専用になり、コンピューターにログオンしている Windows ユーザーアカウントの名前が表示されます。 Azure 仮想マシン (VM) 上で [!INCLUDE[ssnoversion_md](../includes/ssnoversion-md.md)] [!INCLUDE[ssMDSshort_md](../includes/ssmdsshort-md.md)] が実行されている場合、**[ユーザー名]** ボックスに、VM の名前と、VM 上のローカル管理者アカウントのユーザー名が表示されます。 

    ![mds_2016ConfigManager_CreateDatabaseWizard_ServerPage](../master-data-services/media/mds-2016configmanager-createdatabasewizard-serverpage.png)  
  
4.  **[データベース名]** フィールドに名前を入力します。 必要に応じて、Windows 照合順序を選択して、**[SQL Server の既定の照合順序]** チェック ボックスをオフにし、**[大文字小文字を区別する]** などの利用可能なオプションを 1 つ以上クリックします。 **[次へ]** をクリックします。

    ![mds_2016ConfigManager_CreateDatabaseWizard_DatabasePage](../master-data-services/media/mds-2016configmanager-createdatabasewizard-databasepage.png)  
  
     Windows 照合順序の詳細については、「 [Windows 照合順序名 (Transact-SQL)](../t-sql/statements/windows-collation-name-transact-sql.md)」を参照してください。  
  
5.  **[ユーザー名]** フィールドで、マスター データ サービスの既定のスーパー ユーザーになるユーザーの Windows アカウントを指定します。 スーパー ユーザーには、すべての機能領域へのアクセス権があり、すべてのモデルを追加、削除、および更新できます。  

    ![mds_2016ConfigManager_CreateDatabaseWizard_AdminPage](../master-data-services/media/mds-2016configmanager-createdatabasewizard-adminpage.png)  
  
6.  **[次へ]** をクリックして、 [!INCLUDE[ssMDSshort](../includes/ssmdsshort-md.md)] データベースの設定の概要を表示し、 **[次へ]** をもう一度クリックしてデータベースを作成します。 **[続行して完了する]** ページが表示されます。

7. データベースが作成され、構成されたら、**[完了]** をクリックします。  
  
     **データベースの作成ウィザード** の設定の詳細については、「[データベースの作成ウィザード (マスター データ サービス構成マネージャー)](../master-data-services/create-database-wizard-master-data-services-configuration-manager.md)」を参照してください。  
  
7.  の [ **データベースの構成** ] ページで、 [!INCLUDE[ssMDScfgmgr](../includes/ssmdscfgmgr-md.md)] [データベースの **選択**] をクリックします。  
  
8.  **[接続]** をクリックして、手順 7 で作成した [!INCLUDE[ssMDSshort](../includes/ssmdsshort-md.md)] データベースを選択し、**[OK]** をクリックします。 

    ![mds_2016ConfigManager_SelectDatabaseButton_ConnectToDatabaseDialog](../master-data-services/media/mds-2016configmanager-selectdatabasebutton-connecttodatabasedialog.png)  
  
     データベースの設定が完了しました。 **[データベース構成]** ページには、 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 用に接続している [!INCLUDE[ssMDSshort](../includes/ssmdsshort-md.md)]インスタンス、作成したデータベース、および現在のデータベース バージョンが表示されます。  

    ![mds_2016ConfigManager_DatabaseConfig_Completed](../master-data-services/media/mds-2016configmanager-databaseconfig-completed.png)   
  
9. [!INCLUDE[ssMDScfgmgr](../includes/ssmdscfgmgr-md.md)]の左ペインで、 **[Web の構成]** をクリックします。  
  
10. **[Web サイト]** リスト ボックスで、**[既定の Web サイト]****[作成]** の順にクリックして、Web アプリケーションを作成します。  
  
    > [!NOTE]  
    >  **[既定の Web サイト]** を選択した場合、Web アプリケーションを作成する必要があります。 リスト ボックスで **[新しい Web サイトの作成]** を選択した場合、アプリケーションが自動的に作成されます。  

     ![mds_2016ConfigManager_WebConfig](../master-data-services/media/mds-2016configmanager-webconfig.png)  
  
11. **[アプリケーション プール]** セクションで、次のいずれかを実行します。  
  
    -   **管理者アカウント** データベースに対して、手順 5 で入力したのと同じユーザー名を入力し、パスワードを入力して、 **[OK]** をクリックします。  
  
         **もしくは**  
  
    -   別のユーザー名とパスワードを入力し、[OK] をクリックします。  
  
         データベースと Web アプリケーションを作成する場合、同じアカウントを使用する必要はありません。  

        ![mds_2016ConfigManager_WebConfig_CreateWebApplication](../master-data-services/media/mds-2016configmanager-webconfig-createwebapplication.png)   
  
     **[Web アプリケーションの作成]** ダイアログ ボックスの詳細については、「[[Web アプリケーションの作成] ダイアログ ボックス (マスター データ サービス構成マネージャー)](../master-data-services/create-web-application-dialog-box-master-data-services-configuration-manager.md)」を参照してください。  

    > [!NOTE] 
    >  ドメインが [2020 ldap チャネルバインドと Windows の ldap 署名要件を](https://support.microsoft.com/en-us/help/4520412/2020-ldap-channel-binding-and-ldap-signing-requirements-for-windows)実装している場合。 "Active Directory で資格情報を検証できませんでした。" という問題が表示されます。 ドメインアカウントを使用してアプリケーションプールを作成する場合。 回避策として、ドメインユーザーではなく、 **ローカルコンピューターユーザー** を使用します。 これにより、Active Directory による資格情報のチェックを省略できます。 Web アプリケーションを作成した後、 **インターネットインフォメーションサービス (IIS) マネージャー** で id をドメインユーザーに変更できます。
  
12. **[Web アプリケーション]** ボックスの **[Web 構成]** ページで、作成したアプリケーションをクリックして、 **[アプリケーションとデータベースの関連付け]** セクションで  **[選択]** をクリックします。  
  
13. **[接続]** をクリックして、Web アプリケーションと関連付けする必要がある [!INCLUDE[ssMDSshort](../includes/ssmdsshort-md.md)] データベースを選択し、 **[OK]** をクリックします。  
  
     Web サイトの設定が完了しました。 **[Web の構成]** ページには、選択した Web サイト、作成した Web アプリケーション、およびアプリケーションに関連付けられた [!INCLUDE[ssMDSshort](../includes/ssmdsshort-md.md)] データベースが表示されます。  

     ![mds_2016ConfigManager_WebConfig_Completed](../master-data-services/media/mds-2016configmanager-webconfig-completed.png)  
 
     
15. **[Apply]** をクリックします。 **[構成の完了]** メッセージ ボックスが表示されます。 メッセージ ボックスで **[OK]** をクリックして、Web アプリケーションを起動します。 Web サイトのアドレスは、https://*server name* / *web application*/です。 


![mds_2016ConfigurationComplete_MessageBox](../master-data-services/media/mds-2016configurationcomplete-messagebox.png) 
  
[Web の構成] ページの設定に関する詳細については、「[[Web の構成] ページ (マスター データ サービス構成マネージャー)](../master-data-services/web-configuration-page-master-data-services-configuration-manager.md)」を参照してください。  
  
 また、 [!INCLUDE[ssMDScfgmgr](../includes/ssmdscfgmgr-md.md)] を使って、 [!INCLUDE[ssMDSshort](../includes/ssmdsshort-md.md)] データベースに関連付けられている Web アプリケーションとサービスのその他の設定値も指定できます。 たとえば、データの読み込み頻度や検証メールの送信頻度を指定できます。 詳細については、「[システム設定 &#40;マスター データ サービス&#41;](../master-data-services/system-settings-master-data-services.md)」を参照してください。  
  
##  <a name="deploying-sample-models-and-data"></a><a name="deploySample"></a> サンプル モデルとデータを配置する  
 次の 3 つのサンプル モデル パッケージは、  [!INCLUDE[ssMDSshort](../includes/ssmdsshort-md.md)]に付属しています。   これらのサンプル モデルには、データが含まれます。 **サンプル モデル パッケージの既定の場所は、%programfiles%\Microsoft SQL Server\140\Master Data Services\Samples\Packages です。**
  
-   chartofaccounts_en.pkg  
-   customer_en.pkg  
-   product_en.pkg  
  
 このパッケージは、MDSModelDeploy ツールを使用して配置します。 MDSModelDeploy ツールの既定の場所は、 *drive*\Program Files\Microsoft SQL Server\ 140\Master Data Services\Configuration にあります。  
  
 このツールを実行するための前提条件については、「 [MDSModelDeploy を使用したモデルの配置パッケージの配置](../master-data-services/deploy-a-model-deployment-package-by-using-mdsmodeldeploy.md)」を参照してください。  
  
 [!INCLUDE[ssnoversion](../includes/ssnoversion-md.md)][!INCLUDE[ssMDSshort](../includes/ssmdsshort-md.md)] の新機能をサポートするためにデータに加えられた更新については、「[SQL Server のサンプル: モデル配置パッケージ(MDS)](../master-data-services/sql-server-samples-model-deployment-packages-mds.md)」を参照してください。  
  
 **サンプル モデルを配置するには**  
  
1.  サンプル モデル パッケージを *drive*\Program Files\Microsoft SQL Server\140\Master Data Services\Configuration にコピーします。  
  
2.  管理者のコマンド プロンプトを開き、次のコマンドを実行して、MDSModelDeploy.exe のある場所に移動します。  
  
    ```  
    cd c:\Program Files\Microsoft SQL Server\140\Master Data Services\Configuration  
    ```  
  
3.  次の各コマンドを実行して、各サンプル モデルを [!INCLUDE[ssMDSshort](../includes/ssmdsshort-md.md)] に配置します。  
  
    > [!IMPORTANT]  
    >  次の例では、サービスの値に `MDS1` が指定されます。 **Web サイトの設定時に、** [既定の Web サイト] [!INCLUDE[ssMDSshort](../includes/ssmdsshort-md.md)] を選択した場合、この値を使用します。  「 [データベースと Web サイトを設定する](#SetUpWeb) 」セクションを参照してください。  
    >   
    >  新しい Web サイトを作成したか、または別の既存の Web サイトを選択した場合、適切なサービス値を特定するために、まず次のコマンドを実行します。  
    >   
    >  `MDSModelDeploy listservices`  
    >   
    >  戻り値のリスト内の最初のサービス値は、モデルを配置するために指定する値です。  

    > [!NOTE]
    > サンプル モデルのメタデータ情報に関する詳細については、"c:\Program Files\Microsoft SQL Server\140\Master Data Services\Configuration" の場所にある使用可能な readme ファイルを参照してください。
   
     **chartofaccounts_en.pkg サンプル モデルを配置するには**  
  
    ```console
    MDSModelDeploy deploynew -package chartofaccounts_en.pkg -model ChartofAccounts -service MDS1  
    ```  
  
     **customer_en.pkg サンプル モデルを配置するには**  
  
    ```console
    MDSModelDeploy deploynew -package customer_en.pkg -model Customer -service MDS1  
    ```  
  
     **product_en.pkg サンプル モデルを配置するには**  
  
    ```console
    MDSModelDeploy deploynew -package product_en.pkg -model Product -service MDS1  
    ```  
  
     モデルが正常に配置されると、" **MDSModelDeploy 操作は完了しました** " というメッセージが表示されます。  
  
     次の図は、product_en.pkg サンプル モデルを配置するためのコマンドを示します。  
  
     ![製品サンプルモデルを配置するためのコマンドライン](../master-data-services/media/mds-commandprompt-deployingsamplemodel-product.png "製品サンプルモデルを配置するためのコマンドライン")  
  
4.  サンプル モデルを表示するには、次を実行します。  
  
    1.  設定する [!INCLUDE[ssMDSshort](../includes/ssmdsshort-md.md)] Web サイトに移動します。 「 [データベースと Web サイトを設定する](#SetUpWeb) 」セクションを参照してください。  
  
         Web サイトのアドレスは、https://*server name* / *web application*/です。  
  
    2.  **[モデル]** リスト ボックスからモデルを選択して、 **[エクスプローラー]** をクリックします。  
  
         ![MDS Web サイト、ホームページ。](../master-data-services/media/mds-mdswebsite-homepage-selectsamplemodel.png "MDS Web サイト、ホームページ。")  
  
## <a name="next-step"></a>次の手順  
 自分のデータ用に新しいモデルとエンティティを作成します。 「[モデルを作成する (マスター データ サービス)](../master-data-services/create-a-model-master-data-services.md)」と「[エンティティを作成する (マスター データ サービス)](../master-data-services/create-an-entity-master-data-services.md)」を参照してください。  
  
 [!INCLUDE[ssMDSshort](../includes/ssmdsshort-md.md)] のデータの構造を構築するモデルとエンティティを使用する方法の概要については、「[マスター データ サービスの概要 (MDS)](../master-data-services/master-data-services-overview-mds.md)」を参照してください。  
    
## <a name="see-also"></a>参照  
 [マスターデータサービスデータベース](../master-data-services/master-data-services-database.md)   
 [マスターデータマネージャー Web アプリケーション](../master-data-services/master-data-manager-web-application.md)   
 [[データベースの構成] ページ &#40;マスターデータサービス構成マネージャー&#41;](../master-data-services/database-configuration-page-master-data-services-configuration-manager.md)   
 [マスター データ サービス &#40;MDS&#41; の新機能](../master-data-services/what-s-new-in-master-data-services-mds.md)  
  
