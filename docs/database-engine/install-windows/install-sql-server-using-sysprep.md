---
title: SysPrep を使用して SQL Server をインストールする | Microsoft Docs
description: この記事では、SQL Server インストールで Sysprep を使用して、イメージを準備し、完了する方法を説明します。
ms.custom: ''
ms.date: 09/07/2017
ms.prod: sql
ms.reviewer: ''
ms.technology: install
ms.topic: conceptual
ms.assetid: 11f4ed8a-aaa9-417b-bdd5-204f551c6bb6
author: cawrites
ms.author: chadam
monikerRange: '>=sql-server-2016'
ms.openlocfilehash: 385c1efc0dd11813b2aeb94fa087891b1496cffd
ms.sourcegitcommit: bf7577b3448b7cb0e336808f1112c44fa18c6f33
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104610997"
---
# <a name="install-sql-server-with-sysprep"></a>SysPrep を使用して SQL Server をインストールする

[!INCLUDE [SQL Server -Windows Only](../../includes/applies-to-version/sql-windows-only.md)]

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] SysPrep に関連するセットアップ操作には、インストール センターからアクセスできます。 **[インストール センター]** の **[詳細設定]** ページには、 **[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] [のスタンドアロン インスタンスのイメージの準備]** と **[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] [の準備済みスタンドアロン インスタンスのイメージの完了]** の 2 つのオプションがあります。 [準備](#prepare)のセクションと[完了](#complete)のセクションで、インストール プロセスについて詳しく説明します。 詳細については、「 [SysPrep を使用した SQL Server のインストールに関する注意点](../../database-engine/install-windows/considerations-for-installing-sql-server-using-sysprep.md)」を参照してください。 
  
コマンド プロンプトまたは構成ファイルを使用して、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンスの準備および完了を行うこともできます。 詳細については、次を参照してください。  
  
- [コマンド プロンプトからの SQL Server のインストール](../../database-engine/install-windows/install-sql-server-from-the-command-prompt.md)  
  
- [構成ファイルを使用した SQL Server のインストール](../../database-engine/install-windows/install-sql-server-using-a-configuration-file.md)  
  
## <a name="prerequisites"></a>前提条件  
[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] をインストールする前に、「[SQL サーバーのインストール計画](../../sql-server/install/planning-a-sql-server-installation.md)」の記事をご覧ください。 
  
[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のエディションおよびハードウェアとソフトウェアの要件について詳しくは、「[SQL Server のインストールに必要なハードウェアおよびソフトウェア](../../sql-server/install/hardware-and-software-requirements-for-installing-sql-server.md)」をご覧ください。 
    
##  <a name="ssnoversion-sysprep-cluster-support"></a><a name="sysprep"></a> [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] SysPrep クラスター サポート  
 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)]以降、SysPrep は、コマンド ラインからのインストールでクラスター化された [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンスをサポートしています。 詳細については、「 [Sysprep とは](https://msdn.microsoft.com/library/cc721940\(v=WS.10\).aspx)」を参照してください。 
  
### <a name="to-prepare-a-ssnoversion-failover-cluster-unattended"></a>[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] フェールオーバー クラスターを準備するには (自動実行)  
  
1. イメージを準備し (「 [SysPrep を使用した SQL Server のインストールに関する注意点](../../database-engine/install-windows/considerations-for-installing-sql-server-using-sysprep.md)」で説明)、Sysprep による一般化で Windows イメージをキャプチャします。 イメージを準備するサンプルを次に示します。  
  
    ```  
    Setup.exe /q /ACTION=PrepareImage l /FEATURES=SQLEngine /InstanceID =<MYINST> /IACCEPTSQLSERVERLICENSETERMS  
    ```  
  
     次に、Windows SysPrep による一般化を実行します。 
  
2. Windows SysPrep 実行によって、イメージを配置します。 
  
3. Windows フェールオーバー クラスターを作成します。 
  
4. すべてのノードで、 **/ACTION=PrepareFailoverCluster** を指定して setup.exe を実行します。 次に例を示します。  
  
    ```  
    setup.exe /q /ACTION=PrepareFailoverCluster /InstanceName=<InstanceName> /Features=SQLEngine  /SQLSVCACCOUNT="<DomainName\UserName>" /SQLSVCPASSWORD="xxxxxxxxxxx"  /IACCEPTSQLSERVERLICENSETERMS  
    ```  
  
### <a name="complete-a-ssnoversion-failover-cluster-unattended"></a>[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] フェールオーバー クラスターの完了 (自動実行)  
  
1. 使用可能なストレージ グループを所有するノードで、次のように **/ACTION=CompleteFailoverCluster** を指定して setup.exe を実行します。  
  
    ```  
    setup.exe /q /ACTION=CompleteFailoverCluster /InstanceName=<InstanceName>  /FAILOVERCLUSTERDISKS="<Cluster Disk Resource Name - for example, 'Disk S:'>:" /FAILOVERCLUSTERNETWORKNAME="<Insert FOI Network Name>" /FAILOVERCLUSTERIPADDRESSES="IPv4;xx.xxx.xx.xx;Cluster Network;xxx.xxx.xxx.x" /FAILOVERCLUSTERGROUP="MSSQLSERVER" /INSTALLSQLDATADIR="<Drive>:\<Path>\MSSQLSERVER" /SQLCOLLATION="SQL_Latin1_General_CP1_CS_AS" /SQLSYSADMINACCOUNTS="<DomainName\UserName>"  
    ```  
  
### <a name="adding-a-node-to-an-existing-ssnoversion-failover-cluster-unattended"></a>既存の [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] フェールオーバー クラスターへのノードの追加 (自動実行)  
  
1. Windows SysPrep 実行によって、イメージを配置します。 
  
2. Windows フェールオーバー クラスターを結合します。 
  
3. すべてのノードで、 **/ACTION=AddNode** を指定して setup.exe を実行します。  
  
    ```  
    setup.exe /q /ACTION=AddNode /InstanceName=<InstanceName> /Features=SQLEngine  /SQLSVCACCOUNT="<DomainName\UserName>" /SQLSVCPASSWORD="xxxxxxxxxxx"  /IACCEPTSQLSERVERLICENSETERMS  
    ```  
  
##  <a name="prepare-image"></a><a name="prepare"></a> イメージの準備  
  
### <a name="prepare-a-stand-alone-instance-of-ssnoversion"></a>[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]のスタンドアロン インスタンスの準備 
  
1. [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインストール メディアを挿入します。 ルート フォルダーの Setup.exe をダブルクリックします。 ネットワーク共有からインストールするには、ネットワーク共有上のルート フォルダーに移動し、Setup.exe をダブルクリックします。 
  
2. インストール ウィザードで [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インストール センターが実行されます。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスを準備するには、 **[詳細設定]** ページの **[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] [のスタンドアロン インスタンスのイメージの準備]** をクリックします。 
  
3. システム構成チェッカーにより、コンピューターで検出処理が実行されます。 続行するには、 **[OK]** をクリックします。 画面に詳細を表示するには、 **[詳細の表示]** をクリックするか、または **[詳細レポートの表示]** をクリックして HTML レポートを表示します。 
  
4. [製品の更新プログラム] ページに、使用できる最新の [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 製品の更新プログラムが表示されます。 更新プログラムを含めない場合は、 **[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] [製品の更新プログラムを含める]** チェック ボックスをオフにします。 製品の更新プログラムが検出されない場合、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップではこのページは表示されず、 **[セットアップ ファイルのインストール]** ページに自動的に移動します。 
  
5. [セットアップ ファイルのインストール] ページのセットアップには、セットアップ ファイルのダウンロード、抽出、およびインストールの進行状況が表示されます。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップの更新プログラムが検出され、含まれるように指定されている場合は、その更新プログラムもインストールされます。 
  
6. セットアップを続行する前に、システム構成チェッカーによってコンピューターのシステムの状態が確認されます。 画面に詳細を表示するには、 **[詳細の表示]** をクリックするか、または **[詳細レポートの表示]** をクリックして HTML レポートを表示します。 
  
7. **[イメージの種類の準備]** ページで、 **[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] [の新しいインスタンスを準備する]** をクリックします。 
  
     **[イメージの種類の準備]** ページは、コンピューター上に既存の未構成の準備済み [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンスが存在する場合にのみ表示されます。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の新しいインスタンスを準備するか、SysPrep でサポートされている機能をコンピューター上の既存の準備済み [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンスに追加するかを選択できます。 機能を準備済み [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンスに追加する方法については、「 [準備済みインスタンスへの機能の追加](#AddFeatures)」を参照してください。 
  
8. **[ライセンス条項]** ページで使用許諾契約書を読み、使用許諾条件に同意する場合は対応するチェック ボックスをオンにします。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]の機能向上に役立てるため、機能の使用状況オプションを有効にしてレポートを [!INCLUDE[msCoName](../../includes/msconame-md.md)]に送信することもできます。 
  
9. **[機能の選択]** ページで、インストールするコンポーネントを選択します。  
  
    |インストール|コンポーネント|  
    |-|-|  
    |[!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] SysPrep|[!INCLUDE[ssDE](../../includes/ssde-md.md)]<br /><br /> [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] レプリケーション<br /><br /> フルテキスト機能<br /><br /> Data Quality Services<br /><br /> [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] ネイティブ モード<br /><br /> [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]<br /><br /> 再頒布可能な機能<br /><br /> 共有機能|  
  
     機能名を強調表示すると、右側のペインに各コンポーネント グループの説明が表示されます。 チェック ボックスはいくつでもオンにできます。 詳細については、「[SQL Server 2017 の各エディションとサポートされている機能](../../sql-server/editions-and-components-of-sql-server-2017.md)」および [SQL Server 2019](../../sql-server/editions-and-components-of-sql-server-version-15.md) に関するページを参照してください。
  
     選択した機能の必須コンポーネントが、右側のペインに表示されます。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップでは、この手順の後半で説明するインストール手順の間に、まだインストールされていない必須コンポーネントをインストールします。 
  
10. **[イメージの準備ルール]** ページで、セットアップを続行する前に、システム構成チェッカーによってコンピューターのシステムの状態が確認されます。 画面に詳細を表示するには、 **[詳細の表示]** をクリックするか、または **[詳細レポートの表示]** をクリックして HTML レポートを表示します。 
  
11. [インスタンスの構成] ページで、インスタンスのインスタンス ID を指定します。 **[次へ]** をクリックして次に進みます。 
  
     **[インスタンス ID]** - インスタンス ID は、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンスのインストール ディレクトリとレジストリ キーを識別するために使用されます。 これは、既定のインスタンスの場合も名前付きインスタンスの場合も同様です。 準備済みのインスタンスが完了手順で既定のインスタンスとして完了すると、インスタンス名は MSSQLSERVER として上書きされます。 インスタンス ID は指定したものと変わりません。 
  
     **[インスタンス ルート ディレクトリ]** - 既定では、インスタンス ルート ディレクトリは [!INCLUDE[ssInstallPath](../../includes/ssinstallpath-md.md)] になります。 既定以外のルート ディレクトリを指定するには、表示されたフィールドを使用するか、 **[参照]** をクリックしてインストール フォルダーを検索します。 準備手順で指定したディレクトリは、完了手順の構成時に使用されます。 
  
     すべての [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Service Pack とアップグレードは [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]のインスタンスの各コンポーネントに適用されます。 
  
     **[インストール済みのインスタンス]** - セットアップを実行中のコンピューター上にある [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスがグリッドに表示されます。 
  
12. **[必要なディスク領域]** ページでは、指定した機能に必要なディスク領域が計算されます。 その後、必要なディスク領域が使用可能なディスク領域と比較されます。 
  
13. システム構成チェッカーによってイメージの準備のルールが実行され、コンピューターの構成と指定した [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 機能が検証されます。 画面に詳細を表示するには、 **[詳細の表示]** をクリックするか、または **[詳細レポートの表示]** をクリックして HTML レポートを表示します。 
  
14. **[イメージの準備の準備完了]** ページには、セットアップ時に指定したインストール オプションのツリー ビューが表示されます。 このページで、セットアップは製品の更新プログラム機能が有効/無効であるか、および最終バージョンの更新プログラムであるかどうかを示します。 続行するには、 **[準備]** をクリックします。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップは、選択した機能の必須コンポーネントを最初にインストールし、その後で機能をインストールします。 
  
15. インストール中は、セットアップの進行に合わせてインストールの進行状況を監視できるように、 **[イメージの準備の進行状況]** ページに状態が表示されます。 
  
16. インストールが終了すると、 **[完了]** ページにインストールの概要ログ ファイルへのリンクと、その他の重要な注意事項が表示されます。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインストール プロセスを完了するには、 **[閉じる]** をクリックします。 
  
17. コンピューターの再起動を求めるメッセージが表示されたら、再起動してください。 セットアップが完了した時点で、インストール ウィザードによるメッセージを確認することが重要です。 詳細については、「 [SQL Server セットアップ ログ ファイルの表示と読み取り](../../database-engine/install-windows/view-and-read-sql-server-setup-log-files.md)」を参照してください。 
  
18. これで準備手順が終了します。 「 [SysPrep を使用した SQL Server のインストールに関する注意点](../../database-engine/install-windows/considerations-for-installing-sql-server-using-sysprep.md)」に記載されている説明に従って、イメージの完了または準備済みのイメージの配置を行うことができます。 
  
##  <a name="complete-image"></a><a name="complete"></a> イメージの完了  
  
### <a name="complete-a-prepared-instance-of-ssnoversion"></a>準備済みインスタンスの完了 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]  
  
1. [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の準備済みインスタンスをコンピューターのイメージに含めた場合は、[スタート] メニューにショートカットが表示されます。 インストール センターを起動して、 **[詳細設定]** ページの **[準備済みスタンドアロン インスタンスのイメージの完了]** をクリックすることもできます。 
  
2. システム構成チェッカーにより、コンピューターで検出処理が実行されます。 続行するには、 **[OK]** をクリックします。 画面に詳細を表示するには、 **[詳細の表示]** をクリックするか、または **[詳細レポートの表示]** をクリックして HTML レポートを表示します。 
  
3. **[セットアップ サポート ファイル]** ページで **[インストール]** をクリックして、セットアップ サポート ファイルをインストールします。 
  
4. セットアップを続行する前に、システム構成チェッカーによってコンピューターのシステムの状態が確認されます。 確認が完了したら、 **[次へ]** をクリックして続行します。 画面に詳細を表示するには、 **[詳細の表示]** をクリックするか、または **[詳細レポートの表示]** をクリックして HTML レポートを表示します。 
  
5. **[プロダクト キー]** ページでオプション ボタンをクリックして、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]の無償のエディション、または PID キーを持つ製品版のどちらをインストールするかを指定します。 詳しくは、「[SQL Server の各エディションとサポートされている機能](../../sql-server/editions-and-components-of-sql-server-2017.md)」をご覧ください。 Evaluation Edition をインストールする場合、180 日の試用期間はこの手順が完了した時点から開始します。 
  
6. **[ライセンス条項]** ページで使用許諾契約書を読み、使用許諾条件に同意する場合は対応するチェック ボックスをオンにします。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]の機能向上に役立てるため、機能の使用状況オプションを有効にしてレポートを [!INCLUDE[msCoName](../../includes/msconame-md.md)]に送信することもできます。 
  
7. **[準備済みインスタンスの選択]** ページで、ドロップダウン ボックスから完了させる準備済みインスタンスを選択します。 **[インスタンス ID]** ボックスから未構成のインスタンスを選択します。 
  
     **[インストール済みのインスタンス]:** このコンピューター上の準備済みインスタンスを含むすべてのインスタンスがグリッドに表示されます。 
  
8. **[機能の確認]** ページには、準備手順で選択したインストールに含まれる機能とコンポーネントが表示されます。 準備済みインスタンスに含まれていない機能を [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンスに追加する場合は、まずこの手順で [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンスを完了させてから、 **[インストール センター]** の **[機能の追加]** から機能を追加します。 
  
    > [!NOTE]  
    >  インストールする製品バージョンで利用可能な機能を追加できます。 詳しくは、「[SQL Server の各エディションとサポートされている機能](../../sql-server/editions-and-components-of-sql-server-2017.md)」をご覧ください。  
  
9. [インスタンスの構成] ページで、準備済みインスタンスのインスタンス名を指定します。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]の構成が完了すると、これがインスタンスの名前になります。 **[次へ]** をクリックして次に進みます。 
  
     **[インスタンス ID]** - インスタンス ID は、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンスのインストール ディレクトリとレジストリ キーを識別するために使用されます。 これは、既定のインスタンスの場合も名前付きインスタンスの場合も同様です。 準備済みのインスタンスが完了手順で既定のインスタンスとして完了すると、インスタンス名は MSSQLSERVER として上書きされます。 インスタンス ID は準備手順で指定したものと変わりません。 
  
     **[インスタンス ルート ディレクトリ]** - 準備手順で指定したディレクトリが使用されます。この手順ではディレクトリを変更できません。 
  
     すべての [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Service Pack とアップグレードは [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]のインスタンスの各コンポーネントに適用されます。 
  
     **[インストール済みのインスタンス]** : セットアップを実行中のコンピューター上にある [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスがグリッドに表示されます。 
  
10. この記事の残りの部分のワーク フローは、準備手順で選択した機能によって異なります。 選択した機能によっては、表示されないページもあります。 
  
11. **[サーバーの構成 - サービス アカウント]** ページで、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] サービスのログイン アカウントを指定します。 このページで構成する実際のサービスは、インストール時に選択した機能によって異なります。 
  
     すべての [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] サービスに同じログイン アカウントを割り当てることも、各サービス アカウントを個々に構成することもできます。 サービスを自動的に開始するか、手動で開始するか、または無効にするかを指定することもできます。 [!INCLUDE[msCoName](../../includes/msconame-md.md)] では、各サービスに最小の権限を与えるためにはサービス アカウントを個別に構成することをお勧めします。サービス アカウントを個別に構成すると、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] サービスには、サービスでのタスクの実行に必要な最小権限が付与されます。 詳細については、「 [サーバー構成 - サービス アカウント](./install-sql-server.md) 」および「 [Windows サービス アカウントと権限の構成](../../database-engine/configure-windows/configure-windows-service-accounts-and-permissions.md)」を参照してください。 
  
     [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]のこのインスタンスに含まれるすべてのサービス アカウントに同じログオン アカウントを指定する場合は、ページの下部にあるフィールドに資格情報を指定します。 
  
     **セキュリティに関する注意:** [!INCLUDE[ssNoteStrongPass](../../includes/ssnotestrongpass-md.md)]  
  
     [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] サービスのログイン情報を指定したら、 **[次へ]** をクリックします。 
  
12. **[サーバーの構成 - 照合順序]** タブを使用して、 [!INCLUDE[ssDE](../../includes/ssde-md.md)] および [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]に既定以外の照合順序を指定します。 詳細については、「 [サーバー構成 - 照会順序](./install-sql-server.md)」を参照してください。 
  
13. [!INCLUDE[ssDE](../../includes/ssde-md.md)] [の構成 - アカウントの準備] ページを使用して、次の項目を指定します。  
  
    - [セキュリティ モード] - [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]のインスタンス用に Windows 認証または混合モード認証を選択します。 混合モード認証を選択した場合は、組み込みの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] システム管理者アカウントの強力なパスワードを入力する必要があります。 
  
         デバイスが [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]との接続を正常に確立した後のセキュリティ メカニズムは Windows 認証モード、混合モードのどちらの場合も同じです。 詳細については、「 [データベース エンジンの構成 - サーバー構成](./install-sql-server.md)」を参照してください。 
  
    - [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] [管理者] - [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]のインスタンスの 1 人以上のシステム管理者を指定する必要があります。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップを実行しているアカウントを追加するには、 **[現在のユーザーの追加]** をクリックします。 システム管理者の一覧に対してアカウントを追加または削除するには、 **[追加]** または **[削除]** をクリックし、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]のインスタンスについて管理者特権を持っているユーザー、グループ、またはコンピューターの一覧を編集します。 詳細については、「 [データベース エンジンの構成 - サーバー構成](./install-sql-server.md)」を参照してください。 
  
     一覧の編集が完了したら、 **[OK]** をクリックします。 構成ダイアログ ボックスの管理者の一覧を確認します。 一覧が完成したら、 **[次へ]** をクリックします。 
  
14. [!INCLUDE[ssDE](../../includes/ssde-md.md)] [の構成 - データ ディレクトリ] ページを使用して、既定以外のインストール ディレクトリを指定します。 既定のディレクトリにインストールする場合は、 **[次へ]** をクリックします。 
  
    > [!IMPORTANT]  
    >  既定以外のインストール ディレクトリを指定する場合は、インストール フォルダーがこの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]インスタンスに対して一意であることを確認します。 このダイアログ ボックスのディレクトリは、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]の他のインスタンスのディレクトリと共有できません。 
  
     詳細については、「 [データベース エンジンの構成 - データ ディレクトリ](./install-sql-server.md)」を参照してください。 
  
15. [!INCLUDE[ssDE](../../includes/ssde-md.md)] [の構成 - FILESTREAM] ページを使用して、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]インスタンスに対する FILESTREAM を有効にします。 詳細については、「 [データベース エンジンの構成 - Filestream](./install-sql-server.md)」を参照してください。 
  
16. 作成する [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] インストールの種類を指定するには、 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] [の構成] ページを使用します。 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] 構成モードの詳細については、「[Reporting Services 構成オプション &#40;SSRS&#41;](./install-sql-server.md)」を参照してください。 
  
17. **[エラー レポート]** ページで、 [!INCLUDE[msCoName](../../includes/msconame-md.md)] に送信する、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]の機能向上に役立つ情報を指定します。 既定では、エラー レポートのオプションは有効になっています。 
  
18. **[イメージの完了ルール]** ページで、システム構成チェッカーによってイメージの完了のルールが実行され、コンピューターの構成と指定した [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 構成が検証されます。 画面に詳細を表示するには、 **[詳細の表示]** をクリックするか、または **[詳細レポートの表示]** をクリックして HTML レポートを表示します。 
  
19. **[イメージの完了の準備完了]** ページには、セットアップ時に指定したインストール オプションのツリー ビューが表示されます。 続行するには、 **[インストール]** をクリックします。 
  
20. インストール中は、セットアップの進行に合わせてインストールの進行状況を監視できるように、 **[イメージの完了の進行状況]** ページに状態が表示されます。 
  
21. インストールが終了すると、 **[完了]** ページにインストールの概要ログ ファイルへのリンクと、その他の重要な注意事項が表示されます。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインストール プロセスを完了するには、 **[閉じる]** をクリックします。 
  
22. コンピューターの再起動を求めるメッセージが表示されたら、再起動してください。 セットアップが完了した時点で、インストール ウィザードによるメッセージを確認することが重要です。 詳細については、「 [SQL Server セットアップ ログ ファイルの表示と読み取り](../../database-engine/install-windows/view-and-read-sql-server-setup-log-files.md)」を参照してください。 
  
23. この手順で準備済み [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンスの構成が終了し、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]のインストールは完了です。 
  
##  <a name="add-features-to-a-prepared-instance"></a><a name="AddFeatures"></a> Add Features to a Prepared Instance  
  
### <a name="add-features-to-a-prepared-instance-of-ssnoversion"></a>準備済みインスタンスへの機能の追加 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]  
  
1. [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインストール メディアを挿入します。 ルート フォルダーの Setup.exe をダブルクリックします。 ネットワーク共有からインストールするには、ネットワーク共有上のルート フォルダーに移動し、Setup.exe をダブルクリックします。 
  
2. インストール ウィザードで [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インストール センターが実行されます。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の準備済みインスタンスに機能を追加するには、 **[詳細設定]** ページの **[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] [のスタンドアロン インスタンスのイメージの準備]** をクリックします。 
  
3. システム構成チェッカーにより、コンピューターで検出処理が実行されます。 続行するには、 **[OK]** をクリックします。 画面に詳細を表示するには、 **[詳細の表示]** をクリックするか、または **[詳細レポートの表示]** をクリックして HTML レポートを表示します。 
  
4. [セットアップ サポート ファイル] ページで **[インストール]** をクリックして、セットアップ サポート ファイルをインストールします。 
  
5. **[イメージの種類の準備]** ページで、**既存の準備済み [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] [インスタンスに機能を追加する]** オプションをクリックします。 使用可能な準備済みインスタンスのドロップダウン リストから、機能を追加する特定の準備済みインスタンスを選択します。 
  
6. **[機能の選択]** ページで、指定した準備済みインスタンスに追加する機能を指定します。 
  
     選択した機能の必須コンポーネントが、右側のペインに表示されます。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップでは、この手順の後半で説明するインストール手順の間に、まだインストールされていない必須コンポーネントをインストールします。 
  
7. **[イメージの準備ルール]** ページで、セットアップを続行する前に、システム構成チェッカーによってコンピューターのシステムの状態が確認されます。 画面に詳細を表示するには、 **[詳細の表示]** をクリックするか、または **[詳細レポートの表示]** をクリックして HTML レポートを表示します。 
  
8. [必要なディスク領域] ページでは、指定した機能に必要なディスク領域が計算されます。 その後、必要なディスク領域が使用可能なディスク領域と比較されます。 
  
9. **[イメージの準備ルール]** ページで、システム構成チェッカーによってイメージの準備のルールが実行され、コンピューターの構成と指定した [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 機能が検証されます。 画面に詳細を表示するには、 **[詳細の表示]** をクリックするか、または **[詳細レポートの表示]** をクリックして HTML レポートを表示します。 
  
10. **[イメージの準備の準備完了]** ページには、セットアップ時に指定したインストール オプションのツリー ビューが表示されます。 続行するには、 **[インストール]** をクリックします。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップは、選択した機能の必須コンポーネントを最初にインストールし、その後で機能をインストールします。 
  
11. インストール中は、セットアップの進行に合わせてインストールの進行状況を監視できるように、 **[イメージの準備の進行状況]** ページに状態が表示されます。 
  
12. インストールが終了すると、 **[完了]** ページにインストールの概要ログ ファイルへのリンクと、その他の重要な注意事項が表示されます。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインストール プロセスを完了するには、 **[閉じる]** をクリックします。 
  
13. コンピューターの再起動を求めるメッセージが表示されたら、再起動してください。 セットアップが完了した時点で、インストール ウィザードによるメッセージを確認することが重要です。 詳細については、「 [SQL Server セットアップ ログ ファイルの表示と読み取り](../../database-engine/install-windows/view-and-read-sql-server-setup-log-files.md)」を参照してください。 
  
##  <a name="remove-features-from-a-prepare-instance"></a><a name="RemoveFeatures"></a> 準備済みインスタンスからの機能の削除  
  
### <a name="removing-features-from-a-prepared-instance-of-ssnoversion"></a>準備済みインスタンスからの機能の削除 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]  
  
1. アンインストール プロセスを開始するには、 **[スタート]** ボタンをクリックし、 **[コントロール パネル]** をクリックして、 **[プログラムと機能]** をダブルクリックします。 
  
2. アンインストールする [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] コンポーネントをダブルクリックして、 **[削除]** をクリックします。 
  
3. セットアップ サポート ルールが実行され、コンピューターの構成が確認されます。 続行するには、 **[OK]** をクリックします。 
  
4. **[インスタンスの選択]** ページで、変更する準備済みインスタンスを選択します。 準備済みインスタンスの名前は "未構成の PreparedInstanceID" として表示されます。PreparedInstanceID は選択したインスタンスです。 
  
5. **[機能の選択]** ページで、指定した [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンスから削除する機能を指定します。 **[次へ]** をクリックして次に進みます。 
  
6. 削除ルールが実行され、操作を正常に完了できることが確認されます。 
  
7. **[削除の準備完了]** ページで、アンインストールされるコンポーネントおよび機能の一覧を確認します。 
  
8. **[削除の進行状況]** ページに、処理の進行状況が表示されます。 
  
9. **[完了]** ページで、処理の完了状態を確認できます。 **[閉じる]** をクリックして、インストール ウィザードを終了します。 
  
##  <a name="uninstalling-a-prepared-instance"></a><a name="Uninstall"></a> 準備済みインスタンスのアンインストール  
  
### <a name="uninstall-a-prepared-instance-of-ssnoversion"></a>準備済みインスタンスのアンインストール [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]  
  
1. アンインストール プロセスを開始するには、 **[スタート]** ボタンをクリックし、 **[コントロール パネル]** をクリックして、 **[プログラムと機能]** をダブルクリックします。 
  
2. アンインストールする [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] コンポーネントをダブルクリックして、 **[削除]** をクリックします。 
  
3. セットアップ サポート ルールが実行され、コンピューターの構成が確認されます。 続行するには、 **[OK]** をクリックします。 
  
4. **[インスタンスの選択]** ページで、変更する準備済みインスタンスを選択します。 準備済みインスタンスの名前は "未構成の PreparedInstanceID" として表示されます。PreparedInstanceID は選択したインスタンスです。 
  
5. **[機能の選択]** ページで、指定した [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンスから削除する機能を指定します。 **[次へ]** をクリックして次に進みます。 
  
6. **[削除ルール]** ページで、セットアップによってルールが実行され、操作を正常に完了できることが確認されます。 
  
7. **[削除の準備完了]** ページで、アンインストールされるコンポーネントおよび機能の一覧を確認します。 
  
8. **[削除の進行状況]** ページに、処理の進行状況が表示されます。 
  
9. **[完了]** ページで、処理の完了状態を確認できます。 **[閉じる]** をクリックして、インストール ウィザードを終了します。 
  
10. すべての [!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] コンポーネントが削除されるまで、手順 1. ～ 9. を繰り返します。 
  
##  <a name="modifying-or-uninstalling-a-completed-instance-of-ssnoversion"></a><a name="bk_Modifying_Uninstalling"></a>[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]の完了したインスタンスの変更またはアンインストール 
 機能の追加や削除、または完了した [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンスのアンインストールを行う処理は、インストールされた [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]のインスタンスに対して行う処理に似ています。 詳細については、次の記事を参照してください。  
  
- [SQL Server のインスタンスへの機能の追加 &#40;セットアップ&#41;](./add-features-to-an-instance-of-sql-server-setup.md)  
  
- [SQL Server の既存のインスタンスのアンインストール &#40;セットアップ&#41;](../../sql-server/install/uninstall-an-existing-instance-of-sql-server-setup.md)  
  
## <a name="see-also"></a>参照  
 [Sysprep とは](/previous-versions/windows/it-pro/windows-vista/cc721940(v=ws.10))   
 [Sysprep のしくみ](/previous-versions/windows/it-pro/windows-vista/cc766514(v=ws.10))  
  
