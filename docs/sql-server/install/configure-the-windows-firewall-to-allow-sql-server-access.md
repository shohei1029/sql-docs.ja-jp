---
title: Windows ファイアウォールの構成
description: ファイアウォールを経由して SQL Server のインスタンスへのアクセスを許可するように Windows ファイアウォールを構成する方法。
ms.custom: contperf-fy21q3
ms.date: 03/26/2021
ms.prod: sql
ms.reviewer: ''
ms.technology: install
ms.topic: conceptual
helpviewer_keywords:
- Windows Firewall ports
- WMI firewall ports
- Windows Firewall [Database Engine]
- firewall systems, configuring
- advfirewall
- firewall systems
- rules firewall
- firewall systems, overview and port list
- 1433 TCP port
- portopening using netsh
- ports [SQL Server], TCP
- netsh to open firewall ports
ms.assetid: f55c6a0e-b6bd-4803-b51a-f3a419803024
author: cawrites
ms.author: chadam
ms.openlocfilehash: 5f16ca40ec73ab02b539d19a4e271d3a08ccc2c7
ms.sourcegitcommit: 2f971c85d87623c0aed1612406130d840e7bdb2e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "105744499"
---
# <a name="configure-the-windows-firewall-to-allow-sql-server-access"></a>Configure the Windows Firewall to Allow SQL Server Access
[!INCLUDE [SQL Server Windows Only - ASDBMI ](../../includes/applies-to-version/sql-windows-only-asdbmi.md)]

ファイアウォール システムは、コンピューター リソースへの不正アクセスを防ぐのに役立ちます。 ファイアウォールがオンになっているが、正しく構成されていない場合、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] への接続の試行がブロックされる可能性があります。  
  
ファイアウォールを経由して [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスにアクセスするには、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]」などのファイアウォールのマニュアルを参照してください。 ファイアウォールは [!INCLUDE[msCoName](../../includes/msconame-md.md)] Windows のコンポーネントです。 他社製のファイアウォール プログラムをインストールすることもできます。 この記事では、Windows ファイアウォールを構成する方法について説明しますが、基本的な原則は他のファイアウォール プログラムにも適用されます。  
  
> [!NOTE]  
>  この記事では、ファイアウォール構成の概要について説明し、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 管理者を対象とした情報がまとめられています。 ファイアウォールの詳細および管理ファイアウォール情報については、[Windows ファイアウォール セキュリティ展開ガイド](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security-deployment-guide)などのファイアウォールのマニュアルを参照してください。  
  
 **Windows ファイアウォール** の管理に慣れており、構成すべきファイアウォール設定を理解しているユーザーは、より高度な記事にそのまま進むことができます。  
  
-   [データベース エンジン アクセスを有効にするための Windows ファイアウォールを構成する](../../database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access.md)    
-   [Analysis Services のアクセスを許可するための Windows ファイアウォールの構成](/analysis-services/instances/configure-the-windows-firewall-to-allow-analysis-services-access)    
-   [レポート サーバー アクセスに対するファイアウォールの構成](../../reporting-services/report-server/configure-a-firewall-for-report-server-access.md)  
  
##  <a name="basic-firewall-information"></a><a name="BKMK_basic"></a> ファイアウォールに関する基本情報  
 ファイアウォールは、受信パケットを調べて一連の規則と比較することによって機能します。
- 規則で指定された基準をパケットが満たしている場合、パケットはファイアウォールを通過して TCP/IP プロトコルに渡され、さらに処理が行われます。 
- パケットが規則で指定された標準を満たしていない。
    - この場合、ファイアウォールはパケットを破棄します。ログが有効になっている場合は、ファイアウォールのログ ファイルにエントリが作成されます。  
  
 次のいずれかの方法で、許可されたトラフィックの一覧が追加されます。  

- **自動**: ファイアウォールが有効になっているコンピューターで通信が開始されると、それに対する応答が許可されるよう、ファイアウォールによって一覧内にエントリが作成されます。 応答は、要請されたトラフィックと見なされるため、何も構成する必要はありません。 
  
-   **手動**: 管理者がファイアウォールの例外を構成します。 これにより、指定されたプログラムへの、またはコンピューター上のポートへのアクセスが許可されます。 この場合、サーバー、リスナー、またはピアとして機能しているコンピューターは、要請されていない受信トラフィックを受け入れます。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] に接続するように構成を完了する必要があります。  
  
 ファイアウォール戦略の選択は、特定のポートを開くか閉じるかを単に決定するよりも複雑です。 企業に合ったファイアウォール戦略を策定するときは、使用できるすべての規則と構成オプションについて検討してください。 この記事では、可能なファイアウォール オプションすべてを検討するわけではありません。 次のドキュメントを確認することをお勧めします。  
  
 [Windows ファイアウォール展開ガイド](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security-deployment-guide)    
 [Windows ファイアウォール設計ガイド](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security-design-guide)    
 [サーバーとドメインの分離の概要](/windows/security/threat-protection/windows-firewall/domain-isolation-policy-design)  
  
##  <a name="default-firewall-settings"></a><a name="BKMK_default"></a> 既定のファイアウォール設定  
 ファイアウォール構成を計画するには、まずオペレーティング システムのファイアウォールについて現在の状態を確認します。 オペレーティング システムが前のバージョンからアップグレードされた場合、以前のファイアウォール設定が維持されている可能性があります。 グループ ポリシーまたは管理者は、ドメイン内のファイアウォール設定を変更できます。
  
> [!NOTE]  
>  ファイアウォールをオンにすると、ファイルとプリンターの共有やリモート デスクトップ接続など、このコンピューターにアクセスする他のプログラムに影響を与えます。 管理者はファイアウォール設定を調整する前に、コンピューターで実行されているすべてのアプリケーションについて検討する必要があります。  
  
##  <a name="programs-to-configure-the-firewall"></a><a name="BKMK_programs"></a> ファイアウォールを構成するためのプログラム  
**Microsoft 管理コンソール** または **netsh** のいずれかを使用して Windows ファイアウォール設定を構成します。  

-  **Microsoft 管理コンソール (MMC)**  
  
     セキュリティが強化された Windows ファイアウォールの MMC スナップインを使用して、詳細なファイアウォール設定を構成できます。 このスナップインは、ほとんどのファイアウォール オプションが使いやすい形式で表示され、すべてのファイアウォール プロファイルが提供されます。 詳しくは、後の「[セキュリティが強化された Windows ファイアウォールのスナップインの使用](#BKMK_WF_msc)」をご覧ください。  
  
-   **netsh**  
  
     **netsh.exe** は、コマンド プロンプトまたはバッチ ファイルで Windows ベースのコンピューターの構成および監視を行うための管理者ツールです **。** **netsh** ツールを使用すれば、入力したコンテキスト コマンドを適切なヘルパーに渡し、ヘルパーによってコマンドを実行できます。 ヘルパーは、機能を拡張するダイナミック リンク ライブラリ (.dll) ファイルです。 ヘルパーは、**netsh** ツールの 1 つ以上のサービス、ユーティリティ、またはプロトコルの構成、監視、およびサポートを提供します。

     [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] をサポートしているすべてのオペレーティング システムには、ファイアウォール ヘルパーが組み込まれています。 [!INCLUDE[firstref_longhorn](../../includes/firstref-longhorn-md.md)] には、 **advfirewall** という高度なファイアウォール ヘルパーも組み込まれています。 説明する構成オプションの多くは、**netsh** を使用して構成できます。 たとえば、コマンド プロンプトで次のスクリプトを実行すると、TCP ポート 1433 を開くことができます。  
  
    ```  
    netsh firewall set portopening protocol = TCP port = 1433 name = SQLPort mode = ENABLE scope = SUBNET profile = CURRENT  
    ```  
  
     セキュリティを強化するための Windows ファイアウォール ヘルパーを使用した同様の例  
  
    ```  
    netsh advfirewall firewall add rule name = SQLPort dir = in protocol = tcp action = allow localport = 1433 remoteip = localsubnet profile = DOMAIN  
    ```  
  
     **netsh** の詳細については、次のリンクを参照してください。  
  
    -   [Netsh コマンドの構文、コンテキスト、および書式設定](/windows-server/networking/technologies/netsh/netsh-contexts)    
    -   ["netsh firewall" コンテキストの代わりに "netsh advfirewall firewall" コンテキストを使用して、Windows Server 2008 および Windows Vista で Windows ファイアウォールの動作を制御する方法](https://support.microsoft.com/kb/947709)    

    
- **Linux の場合**:Linux では、アクセスするサービスに関連付けられたポートを開く必要もあります。 Linux の異なるディストリビューションと異なるファイアウォールには、独自のプロシージャがあります。 2 つの例については、[Red Hat での SQL Server](../../linux/quickstart-install-connect-red-hat.md) に関するページと [SUSE での SQL Server](../../linux/quickstart-install-connect-suse.md) に関するページを参照してください。 
  
## <a name="ports-used-by-ssnoversion"></a>で使用されるポート [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]  
 次の表で、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]で使用されるポートを確認できます。  
  
###  <a name="ports-used-by-the-database-engine"></a><a name="BKMK_ssde"></a> Ports Used By the Database Engine  
 

既定では、SQL Server と関連データベース サービスで使用される一般的なポート:TCP **1433**、**4022**、**135**、**1434**、UDP **1434**。 次の表では、これらのポートについてより詳細に説明します。 名前付きインスタンスでは[動的ポート](#BKMK_dynamic_ports)が使用されます。
 
 次の表に、 [!INCLUDE[ssDE](../../includes/ssde-md.md)]で頻繁に使用されるポートの一覧を示します。  
  
|シナリオ|Port|説明|  
|--------------|----------|--------------|  
|TCP 経由で実行されている既定のインスタンス|TCP ポート 1433|ファイアウォールを通過することを許可されている最も一般的なポートです。 [!INCLUDE[ssDE](../../includes/ssde-md.md)]の既定のインストール、またはコンピューターで実行中の唯一のインスタンスである名前付きインスタンスへの、通常の接続に適用されます (名前付きインスタンスには注意事項があります。 後の「[動的ポート](#BKMK_dynamic_ports)」を参照してください)。|  
|既定のポートを持つ名前付きインスタンス|TCP ポートは、 [!INCLUDE[ssDE](../../includes/ssde-md.md)] の起動時に決定される動的ポートです。|後の「 [動的ポート](#BKMK_dynamic_ports)」の説明を参照してください。 名前付きインスタンスを使用している場合、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Browser サービスで UDP ポート 1434 が必要になる可能性があります。|  
| 固定ポートを持つ名前付きインスタンス |管理者が構成するポート番号|後の「 [動的ポート](#BKMK_dynamic_ports)」の説明を参照してください。|  
|専用管理者接続|TCP ポート 1434 (既定のインスタンスに使用)。 その他のポートは名前付きインスタンスに使用されます。 ポート番号については、エラー ログを確認してください。|既定では、専用管理者接続 (DAC) へのリモート接続は有効になっていません。 リモート DAC を有効にするには、セキュリティ構成ファセットを使用します。 詳細については、「 [Surface Area Configuration](../../relational-databases/security/surface-area-configuration.md)」を参照してください。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Browser サービス|UDP ポート 1434|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Browser サービスは、名前付きインスタンスへの着信接続をリッスンします。 
サービスは、その名前付きインスタンスに対応する TCP ポート番号をクライアントに提供します。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の名前付きインスタンスが使用されている場合は、通常 [!INCLUDE[ssDE](../../includes/ssde-md.md)] Browser サービスを開始します。 名前付きインスタンスの特定のポートに接続するようにクライアントが構成されている場合、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Browser サービスは必要ありません。|  
|HTTP エンドポイントを持つインスタンス|HTTP エンドポイントの作成時に指定できます。 既定の TCP ポートは、CLEAR_PORT トラフィックでは 80、SSL_PORT トラフィックでは 443 です。|URL を使用した HTTP 接続に使用されます。|  
|HTTPS エンドポイントを持つ既定のインスタンス |TCP ポート 443|URL を使用した HTTPS 接続に使用されます。 HTTPS は、トランスポート層セキュリティ (TLS) (旧称 Secure Sockets Layer (SSL)) を使用する HTTP 接続です。|  
|[!INCLUDE[ssSB](../../includes/sssb-md.md)]|TCP ポート 4022。 使用されるポートを確認するには、次のクエリを実行します。<br /><br /> `SELECT name, protocol_desc, port, state_desc`<br /><br /> `FROM sys.tcp_endpoints`<br /><br /> `WHERE type_desc = 'SERVICE_BROKER'`|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] [!INCLUDE[ssSB](../../includes/sssb-md.md)] の既定のポートはありません。オンライン ブックの例では、従来の構成を使用しています。|  
|データベース ミラーリング|管理者が選択したポート。 ポートを特定するには、次のクエリを実行します。<br /><br /> `SELECT name, protocol_desc, port, state_desc FROM sys.tcp_endpoints`<br /><br /> `WHERE type_desc = 'DATABASE_MIRRORING'`|データベース ミラーリングに既定のポートはありませんが、オンライン ブックの例では、TCP ポート 5022 または 7022 を使用しています。 特に自動フェールオーバーを伴う高い安全性モードでは、使用中のミラーリング エンドポイントが中断しないようにすることが重要です。 ファイアウォール構成によりクォーラムが分割されないようにする必要があります。 詳細については、「 [サーバー ネットワーク アドレスの指定 &#40;データベース ミラーリング&#41;](../../database-engine/database-mirroring/specify-a-server-network-address-database-mirroring.md)を使用します。|  
|レプリケーション|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] へのレプリケーション接続では、一般的な正規の [!INCLUDE[ssDE](../../includes/ssde-md.md)] ポートを使用します (既定のインスタンスは TCP ポート 1433 です)<br /><br /> レプリケーション スナップショットのための Web 同期と FTP/UNC アクセスには、ファイアウォール上で追加のポートを開く必要があります。 ある場所から別の場所に初期データおよびスキーマを転送するために、レプリケーションでは FTP (TCP ポート 21)、HTTP (TCP ポート 80) を使用した同期、またはファイル共有を使用できます。 ファイル共有を NetBIOS と共に使用する場合は、UDP ポート 137 と 138、および TCP ポート 139 が使用されます。 ファイル共有は TCP ポート 445 を使用します。|HTTP を使用した同期の場合、レプリケーションでは IIS エンドポイント (構成可能、既定はポート 80) を使用しますが、IIS プロセスは標準のポート (既定のインスタンスの場合は 1433) を使用してバックエンドの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] に接続します。<br /><br /> FTP を使用した Web 同期時は、サブスクライバーと IIS の間ではなく、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 発行者と IIS の間で FTP 転送が行われます。|  
|[!INCLUDE[tsql](../../includes/tsql-md.md)] デバッガー|TCP ポート 135<br /><br /> 「 [ポート 135 に関する注意事項](#BKMK_port_135)」を参照してください。<br /><br /> [IPsec](#BKMK_IPsec) の例外が必要な場合もあります。|[!INCLUDE[vsprvs](../../includes/vsprvs-md.md)]ホスト コンピューターで [!INCLUDE[vsprvs](../../includes/vsprvs-md.md)] を使用している場合は、 **Devenv.exe** を例外リストに追加し、TCP ポート 135 を開く必要もあります。<br /><br /> [!INCLUDE[ssManStudio](../../includes/ssmanstudio-md.md)]ホスト コンピューターで [!INCLUDE[ssManStudio](../../includes/ssmanstudio-md.md)] を使用している場合は、 **ssms.exe** を例外リストに追加し、TCP ポート 135 を開く必要もあります。 詳細については、「 [Transact-SQL デバッガーの構成](../../ssms/scripting/configure-firewall-rules-before-running-the-tsql-debugger.md)」を参照してください。|  
  
 [!INCLUDE[ssDE](../../includes/ssde-md.md)]で Windows ファイアウォールを構成する詳細な手順については、「[データベース エンジン アクセスを有効にするための Windows ファイアウォールを構成する](../../database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access.md)」を参照してください。  
  
####  <a name="dynamic-ports"></a><a name="BKMK_dynamic_ports"></a> 動的ポート  
 既定では、名前付きインスタンス ( [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)]を含む) では動的ポートを使用します。  したがって、[!INCLUDE[ssDE](../../includes/ssde-md.md)] が起動するたびに使用可能なポートが特定され、そのポート番号が使用されます。 インストールされている [!INCLUDE[ssDE](../../includes/ssde-md.md)] のインスタンスが名前付きインスタンスのみの場合、通常は TCP ポート 1433 が使用されます。 [!INCLUDE[ssDE](../../includes/ssde-md.md)] の他のインスタンスがインストールされている場合は、別の TCP ポートが使用される可能性が高くなります。 選択されたポートは、[!INCLUDE[ssDE](../../includes/ssde-md.md)] が起動するたびに変わる可能性があるので、正しいポート番号にアクセスできるようにファイアウォールを構成することは容易ではありません。 ファイアウォールを使用する場合は、毎回同じポート番号を使用するように [!INCLUDE[ssDE](../../includes/ssde-md.md)] を再構成することをお勧めします。 固定ポートまたは静的ポートをお勧めします。 詳細については、「[特定の TCP ポートで受信待ちするようにサーバーを構成する方法 &#40;SQL Server 構成マネージャー&#41;](../../database-engine/configure-windows/configure-a-server-to-listen-on-a-specific-tcp-port.md)」を参照してください。  
  
 固定ポートでリッスンするように名前付きインスタンスを構成する代わりに、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] sqlservr.exe **(** ) などの [!INCLUDE[ssDE](../../includes/ssde-md.md)]プログラムを対象としてファイアウォールで例外を作成することもできます。 セキュリティが強化された Windows ファイアウォールの MMC スナップインを使用しているときは、 **[受信の規則]** ページの **[ローカル ポート]** 列にポート番号が表示されません。 どのポートが開いているかを調べるのが難しくなる可能性があります。 もう 1 つの注意事項は、Service Pack または累積された更新によって [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 実行可能ファイルへのパスが変更され、ファイアウォール規則が無効になる可能性があるということです。  
  
##### <a name="to-add-a-program-exception-to-the-firewall-using-windows-defender-firewall-with-advanced-security"></a>セキュリティが強化された Windows Defender ファイアウォールを使用して、ファイアウォールにプログラムの例外を追加するには
  
1. [スタート] メニューから「*wf.msc*」と入力します。 Enter キーを押すか、検索結果の wf .msc を選択し、**セキュリティが強化された Windows Defender ファイアウォール** を開きます。
1. 左側のウィンドウで、 **[受信の規則]** を選択します。
1. 右側のウィンドウで、 **[アクション]** の **[新しい規則]** を選択します。**新規の受信の規則ウィザード** が開きます。
1. **[規則の種類]** で  **[プログラム]** を選択します。 **[次へ]** を選択します。
1. **[プログラム]** で **[このプログラムのパス]** を選択します。 **[参照]** を選択して SQL Server のインスタンスを検索します。 sqlservr.exe というプログラムです。 通常は以下の場所にあります。

   `C:\Program Files\Microsoft SQL Server\MSSQL15.<InstanceName>\MSSQL\Binn\sqlservr.exe`

   **[次へ]** を選択します。

1. **[操作]** で、 **[接続を許可する]** を選択します。 **[次へ]** を選択します。
1. **[プロファイル]** で 3 つのプロフィールすべてを含めます。 **[次へ]** を選択します。
1. **[名前]** に規則の名前を入力します。 **[完了]** を選択します。

エンドポイントの詳細については、「[複数の TCP ポートでリッスンするデータベース エンジンの構成](../../database-engine/configure-windows/configure-the-database-engine-to-listen-on-multiple-tcp-ports.md)」と「[エンドポイントのカタログ ビュー &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/endpoints-catalog-views-transact-sql.md)」を参照してください。 
  
###  <a name="ports-used-by-analysis-services"></a><a name="BKMK_ssas"></a> Analysis Services で使用されるポート  
 
既定では、SQL Server Analysis Services と関連サービスで使用される一般的なポート:TCP **2382**、**2383**、**80**、**443**。 次の表では、これらのポートについてより詳細に説明します。  
 
 次の表に、 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]で頻繁に使用されるポートの一覧を示します。  
  
|機能|Port|説明|  
|-------------|----------|--------------|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|TCP ポート 2383 (既定のインスタンスに使用)|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]の既定のインスタンスに使用される標準ポートです。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Browser サービス|TCP ポート 2382 ( [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] の名前付きインスタンスにのみ必要)|ポート番号を指定せずに [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] の名前付きインスタンスに対してクライアントが接続要求を行うと、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Browser がリッスンするポート 2382 が指定されます。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Browser は、名前付きインスタンスが使用するポートに要求をリダイレクトします。|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] IIS/HTTP 経由で使用するように構成された<br /><br /> (PivotTable® サービスでは HTTP または HTTPS が使用されます)|TCP ポート 80|URL を使用した HTTP 接続に使用されます。|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] IIS/HTTPS 経由で使用するように構成された<br /><br /> (PivotTable® サービスでは HTTP または HTTPS が使用されます)|TCP ポート 443|URL を使用した HTTPS 接続に使用されます。 HTTPS は、TLS を使用する HTTP 接続です。|  
  
 ユーザーが IIS やインターネットを経由して [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] にアクセスする場合は、IIS がリッスンするポートを開く必要があります。 次に、クライアントの接続文字列にポートを指定します。 この場合、 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]に直接アクセスするためのポートを開く必要はありません。 必要のない他のすべてのポートと共に、既定のポート 2389 およびポート 2382 を制限する必要があります。  
  
 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] で Windows ファイアウォールを構成する詳細な手順については、「[Analysis Services のアクセスを許可するための Windows ファイアウォールの構成](/analysis-services/instances/configure-the-windows-firewall-to-allow-analysis-services-access)」を参照してください。  
  
###  <a name="ports-used-by-reporting-services"></a><a name="BKMK_ssrs"></a> Reporting Services で使用されるポート  

既定では、SQL Server Reporting Services と関連サービスで使用される一般的なポートは TCP **80**、**443** です。 次の表では、これらのポートについてより詳細に説明します。 


次の表に、 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]で頻繁に使用されるポートの一覧を示します。  
  
|機能|Port|説明|  
|-------------|----------|--------------|  
|[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] Web サービス|TCP ポート 80|URL を使用した [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] への HTTP 接続に使用されます。 **[World Wide Web サービス (HTTP)]** のあらかじめ構成された規則は使用しないことをお勧めします。 詳細については、後の「 [その他のファイアウォール ルールの操作](#BKMK_other_rules) 」を参照してください。|  
|[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] HTTPS 経由で使用するように構成された|TCP ポート 443|URL を使用した HTTPS 接続に使用されます。 HTTPS は、TLS を使用する HTTP 接続です。 **[セキュア World Wide Web サービス (HTTPS)]** のあらかじめ構成された規則は使用しないことをお勧めします。 詳細については、後の「 [その他のファイアウォール ルールの操作](#BKMK_other_rules) 」を参照してください。|  
  
[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] から [!INCLUDE[ssDE](../../includes/ssde-md.md)] または [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]のインスタンスに接続する場合、そのサービス用の適切なポートを開く必要もあります。 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]で Windows ファイアウォールを構成する詳細な手順については、「 [レポート サーバー アクセスに対するファイアウォールの構成](../../reporting-services/report-server/configure-a-firewall-for-report-server-access.md)」を参照してください。  
  
###  <a name="ports-used-by-integration-services"></a><a name="BKMK_ssis"></a> Integration Services で使用されるポート  
 次の表に、 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] サービスで使用されるポートの一覧を示します。  
  
|機能|Port|説明|  
|-------------|----------|--------------|  
|[!INCLUDE[msCoName](../../includes/msconame-md.md)] リモート プロシージャ呼び出し (MS RPC)<br /><br /> [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] ランタイムで使用されます。|TCP ポート 135<br /><br /> 「 [ポート 135 に関する注意事項](#BKMK_port_135)」を参照してください。|[!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] サービスでは、ポート 135 で DCOM を使用します。 サービス コントロール マネージャーではポート 135 を使用して、[!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] サービスの開始と停止、実行中のサービスに対する制御要求の転送などのタスクを実行します。 ポート番号を変更することはできません。<br /><br /> このポートは、[!INCLUDE[ssManStudio](../../includes/ssmanstudio-md.md)] またはカスタム アプリケーションから [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] サービスのリモート インスタンスに接続する場合にのみ、開く必要があります。|  
  
[!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] で Windows ファイアウォールを構成する詳細な手順については、[Integration Services サービス、SSIS サービス](/previous-versions/sql/sql-server-2012/ms137861(v=sql.110))に関するページをご覧ください。  
  
###  <a name="another-ports-and-services"></a><a name="BKMK_additional_ports"></a> もう 1 つのポートとサービス  
次の表に、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] が依存している可能性があるポートとサービスの一覧を示します。  

|シナリオ|Port|説明|  
|--------------|----------|--------------|  
|Windows Management Instrumentation (Windows Management Instrumentation)<br /><br /> **Windows Management Instrumentation (WMI)** の詳細については、「[構成管理用の WMI プロバイダー](../../relational-databases/wmi-provider-configuration/wmi-provider-for-configuration-management.md)」を参照してください|WMI は、DCOM によってポートが割り当てられている共有サービス ホストの一部として実行されます。 WMI では TCP ポート 135 を使用している可能性があります。<br /><br /> 「 [ポート 135 に関する注意事項](#BKMK_port_135)」を参照してください。|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 構成マネージャーでは、WMI を使用してサービスの一覧を表示し、管理します。 **[Windows Management Instrumentation (WMI)]** のあらかじめ構成されたルール グループを使用することをお勧めします。 詳細については、後の「 [その他のファイアウォール ルールの操作](#BKMK_other_rules) 」を参照してください。|  
|[!INCLUDE[msCoName](../../includes/msconame-md.md)] 分散トランザクション コーディネーター (MS DTC)|TCP ポート 135<br /><br /> 「 [ポート 135 に関する注意事項](#BKMK_port_135)」を参照してください。|アプリケーションで分散トランザクションを使用する場合は、 [!INCLUDE[msCoName](../../includes/msconame-md.md)] 分散トランザクション コーディネーター (MS DTC) トラフィックが、各 MS DTC インスタンス間、および [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]などのリソース マネージャーと MS DTC との間を流れるように、ファイアウォールを構成することが必要になる可能性があります。 **[分散トランザクション コーディネーター]** のあらかじめ構成されたルール グループを使用することをお勧めします。<br /><br /> 個別のリソース グループのクラスター全体に対して 1 つの共有 MS DTC が構成されている場合は、ファイアウォールに sqlservr.exe を例外として追加する必要があります。|  
|[!INCLUDE[ssManStudio](../../includes/ssmanstudio-md.md)] の参照ボタンを押すと、UDP を使用して [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Browser サービスに接続できます。 詳細については、「[SQL Server Browser サービス &#40;データベース エンジンと SSAS&#41;](../../database-engine/configure-windows/sql-server-browser-service-database-engine-and-ssas.md)」を参照してください。参照してください。|UDP ポート 1434|UDP はコネクションレスのプロトコルです。<br /><br /> ファイアウォールの設定 ([INetFwProfile インターフェイスの UnicastResponsesToMulticastBroadcastDisabled プロパティ](/windows/win32/api/netfw/nf-netfw-inetfwprofile-get_unicastresponsestomulticastbroadcastdisabled)) により、ブロードキャスト (またはマルチキャスト) UDP 要求へのファイアウォールとユニキャスト応答の動作が制御されます。  この設定には次の 2 つの動作があります。<br /><br /> 設定が TRUE の場合、ブロードキャスト要求へのユニキャスト応答はまったく許可されません。 サービスの列挙は失敗します。<br /><br /> 設定が FALSE (既定) の場合、ユニキャスト応答が 3 秒間許可されます。 この時間の長さは構成できません。 混雑しているネットワークや待機時間の長いネットワークまたは負荷の大きいサーバーで [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスを列挙しようとすると、一覧の一部しか返されない可能性があり、ユーザーの混乱を招くことがあります。|  
|<a name="BKMK_IPsec"></a> IPsec トラフィック|UDP ポート 500 および UDP ポート 4500|ドメインのポリシーにより IPsec 経由でネットワーク通信を行う必要がある場合は、UDP ポート 4500 と UDP ポート 500 も例外の一覧に追加する必要があります。 IPsec は、Windows ファイアウォール スナップインの **新規の受信の規則ウィザード** を使用するオプションです。 詳細については、後の「 [セキュリティが強化された Windows ファイアウォールのスナップインの使用](#BKMK_WF_msc) 」を参照してください。|  
|信頼されたドメインでの Windows 認証の使用|認証要求を許可するようにファイアウォールを構成する必要があります。|詳細については、「 [ドメインの信頼関係を使用するためのファイアウォールの構成方法](https://support.microsoft.com/kb/179442/)」を参照してください。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] と Windows のクラスタリング|クラスタリングでは、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] に直接関連付けられていない追加のポートが必要です。|詳細については、「 [クラスターで使用するネットワークを有効にする](/previous-versions/windows/it-pro/windows-server-2003/cc728293(v=ws.10))」を参照してください。|  
|HTTP サーバー API (HTTP.SYS) で予約された URL 名前空間|通常は TCP ポート 80 を使用しますが、他のポートに構成することもできます。 一般的な情報については、「 [HTTP および HTTPS の構成](/dotnet/framework/wcf/feature-details/configuring-http-and-https)」を参照してください。|HttpCfg.exe を使用した HTTP.SYS エンドポイントの予約に関する [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 固有の情報については、「[URL の予約と登録について &#40;SSRS 構成マネージャー&#41;](../../reporting-services/install-windows/about-url-reservations-and-registration-ssrs-configuration-manager.md)」を参照してください。|  
  
##  <a name="special-considerations-for-port-135"></a><a name="BKMK_port_135"></a> ポート 135 に関する注意事項  
 TCP/IP で RPC を使用する場合、または UDP/IP をトランスポートとして使用する場合は、必要に応じて受信ポートがシステム サービスに動的に割り当てられます。 ポート 1024 以上の TCP/IP および UDP/IP ポートが使用されます。 これらのポートは、"ランダム RPC ポート" と呼ばれます。 これらの場合、RPC クライアントは RPC エンドポイント マッパーを使用して、サーバーにどの動的ポートが割り当てられたかを指示します。 RPC ベースのサービスによっては、RPC によってポートが動的に割り当てられるのではなく、ユーザーが特定のポートを構成できます。 サービスに関係なく、RPC によって動的に割り当てられるポートを狭い範囲に制限することもできます。 ポート 135 は多くのサービスで使用されるので、悪意のあるユーザーによって頻繁に攻撃されます。 ポート 135 を開く場合は、ファイアウォール ルールのスコープを制限することを検討してください。  
  
 ポート 135 の詳細については、次の資料を参照してください。  
  
-   [Windows サーバー システムのサービス概要とネットワーク ポート要件](https://support.microsoft.com/kb/832017)   
-   [製品 CD-ROM の Windows Server 2003 サポート ツールを使用した、RPC エンドポイント マッパー エラーのトラブルシューティング](https://mskb.pkisolutions.com/kb/839880)  
-   [リモート プロシージャ呼び出し (RPC)](/previous-versions/ms950395(v=msdn.10))    
-   [ファイアウォールで動作するように RPC の動的ポート割り当てを構成する方法](https://support.microsoft.com/kb/154596/)  
  
##  <a name="interaction-with-other-firewall-rules"></a><a name="BKMK_other_rules"></a> その他のファイアウォール ルールの操作  
 Windows ファイアウォールでは、ルールおよびルール グループを使用して、その構成が設定されます。 各規則または規則グループは、特定のプログラムやサービスに関連付けられており、そのプログラムやサービスによって、ユーザーの知らない間にその規則が変更されたり削除されたりする場合があります。 たとえば、ルール グループ **[World Wide Web サービス (HTTP)]** と **[セキュア World Wide Web サービス (HTTPS)]** は IIS に関連付けられています。 これらのルールを有効にすると、ポート 80 とポート 443 が開き、これらのルールが有効になっている場合にポート 80 とポート 443 に依存する [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 機能が有効になります。 ただし、IIS を構成する管理者が、それらのルールを変更するか無効にする可能性があります。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] にポート 80 またはポート 443 を使用している場合は、他の IIS ルールとは別に希望のポート構成を維持する独自の規則または規則グループを作成する必要があります。  
  
 セキュリティが強化された Windows ファイアウォールの MMC スナップインを使用して、該当する許可ルールと一致するトラフィックを許可できます。 そのため、両方がポート 80 に適用される (パラメーターが異なる) 2 つの規則がある場合、 どちらかの規則に一致するトラフィックが許可されます。 そのため、一方の規則でローカル サブネットからのポート 80 経由のトラフィックが許可され、もう一方の規則で任意のアドレスからのトラフィックが許可される場合、結果として、発信元に関係なくポート 80 へのすべてのトラフィックが許可されます。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]へのアクセスを有効に管理するために、管理者はサーバーで有効になっているすべてのファイアウォール ルールを定期的に確認する必要があります。  
  
##  <a name="overview-of-firewall-profiles"></a><a name="BKMK_profiles"></a> ファイアウォール プロファイルの概要  
 オペレーティング システムではファイアウォール プロファイルを使用して、接続性、接続状態、およびカテゴリごとに、各ネットワークが識別され記憶されます。  
  
 セキュリティが強化された Windows ファイアウォールでは、ネットワークの場所として 3 種類の設定があります。  
  
-   **ドメイン**:Windows では、コンピューターが参加しているドメインのドメイン コントローラーへのアクセスを認証できます。
-   **パブリック**: ドメイン ネットワーク以外のすべてのネットワークがパブリックとして最初に分類されます。 インターネットへの直接接続となるネットワーク、または空港やコーヒー ショップのような公共の場所にあるネットワークです。
-   **プライベート**: ユーザーまたはアプリケーションによってプライベートと見なされるネットワークです。 信頼されたネットワークだけをプライベート ネットワークと見なす必要があります。 通常は、ホーム ネットワークまたは小規模ビジネス ネットワークをプライベートと見なすことができます。  
  
 管理者はネットワークの場所の種類ごとにプロファイルを作成できます。各プロファイルに、さまざまなファイアウォール ポリシーを指定できます。 どの時点においても 1 つのプロファイルのみが適用されます。 次のようなプロファイル順序が適用されます。  
  
1.  そのコンピューターがメンバーとなっているドメイン コントローラーへのアクセスに対してすべてのインターフェイスが認証される場合、ドメイン プロファイルが適用されます。
2.  すべてのインターフェイスがドメイン コントローラーに対して認証されるか、プライベート ネットワークの場所として分類されるネットワークに接続されている場合、プライベート プロファイルが適用されます。    
3.  それ以外の場合は、パブリック プロファイルが適用されます。  
  
 セキュリティが強化された Windows ファイアウォールの MMC スナップインを使用して、すべてのファイアウォール プロファイルを表示し、構成できます。 コントロール パネルの **[Windows ファイアウォール]** では、現在のプロファイルのみを構成できます。  
  
##  <a name="additional-firewall-settings-using-the-windows-firewall-item-in-control-panel"></a><a name="BKMK_additional_settings"></a> コントロール パネルの [Windows ファイアウォール] を使用した追加のファイアウォール設定  
 追加されたファイアウォールは、特定のコンピューターまたはローカル サブネットからの着信接続のみに対してポートを開くように制限できます。 ポートを開くスコープを制限すると、コンピューターが悪意のあるユーザーの攻撃にさらされるリスクを軽減できます。  
  
> [!NOTE]  
>  コントロール パネルの **[Windows ファイアウォール]** では、現在のファイアウォール プロファイルのみを構成できます。  
  
### <a name="to-change-the-scope-of-a-firewall-exception-using-the-windows-firewall-item-in-control-panel"></a>コントロール パネルの [Windows ファイアウォール] を使用して、ファイアウォールの例外のスコープを変更するには  
  
1.  コントロール パネルの **[Windows ファイアウォール]** の **[例外]** タブでプログラムまたはポートを選択し、 **[プロパティ]** または **[編集]** をクリックします。  
  
2.  **[プログラムの編集]** または **[ポートの編集]** ダイアログ ボックスの **[スコープの変更]** を選択します。  
  
3.  次のいずれかのオプションを選択します。  
  
    -   **[任意のコンピューター (インターネット上のコンピューターを含む)]** : 推奨されません。 自分のコンピューターのアドレスを指定したすべてのコンピューターが、指定したプログラムまたはポートに接続します。 このような設定は、インターネット上の匿名ユーザーに情報を提供できるようにするために必要な場合がありますが、悪意のあるユーザーの攻撃にさらされるリスクが大きくなります。 この設定を有効にすると、[エッジトラバーサルを許可する] オプションなどのネットワーク アドレス変換 (NAT) トラバーサルが許可され、攻撃にさらされるリスクが大きくなります。  
  
    -   **[ユーザーのネットワーク (サブネット) のみ]** : **[任意のコンピューター]** より安全な設定です。 ネットワークのローカル サブネット上のコンピューターのみがプログラムまたはポートに接続できます。  
  
    -   **[カスタムの一覧]** : 一覧に IP アドレスが載っているコンピューターだけが接続できます。 安全な設定を行うと、 **[ユーザーのネットワーク (サブネット) のみ]** よりもさらに安全ですが、DHCP を使用するクライアント コンピューターでは、その IP アドレスが変更され、接続できなくなることがあります。 その代わり、認証の対象として意図していなかった別のコンピューターに一覧の IP アドレスが割り当てられ、そのコンピューターが接続してしまう可能性があります。 **[カスタムの一覧]** は固定 IP アドレスを使用するように構成された他のサーバーを一覧表示する場合に適しています。
    IP アドレスは、侵入者によってスプーフィングされる可能性があります。 ファイアウォール ルールを制限することは、最低限のネットワーク インフラストラクチャです。  
  
##  <a name="using-the-windows-firewall-with-advanced-security-snap-in"></a><a name="BKMK_WF_msc"></a> セキュリティが強化された Windows ファイアウォールのスナップインの使用  
 セキュリティが強化された Windows ファイアウォールの MMC スナップインを使用して、詳細なファイアウォール設定を構成できます。 スナップインには規則ウィザードや、コントロール パネルの **[Windows ファイアウォール]** では使用できない設定も含まれます。 設定は次のとおりです。  
  
-   暗号化の設定  
-   サービスの制限   
-   名前によってコンピューターの接続を制限する    
-   特定のユーザーまたはプロファイルに接続を制限する  
-   エッジ トラバーサルにより、トラフィックが NAT (Network Address Translation) ルーターをバイパスするのを許可する    
-   送信ルールを構成する    
-   セキュリティ ルールを構成する    
-   着信接続用に IPsec を要求する  
  
### <a name="to-create-a-new-firewall-rule-using-the-new-rule-wizard"></a>新しい規則ウィザードを使用して、新しいファイアウォール ルールを作成するには  
  
1.  [スタート] メニューで **[ファイル名を指定して実行]** を選択し、「**WF.msc**」と入力して、 **[OK]** を選択します。    
2.  **[セキュリティが強化された Windows ファイアウォール]** の左ウィンドウにある **[受信の規則]** を右クリックし、 **[新しい規則]** を選択します。   
3.  **新規の受信の規則ウィザード** で、必要な設定を行います。  
  
##  <a name="troubleshooting-firewall-settings"></a><a name="BKMK_troubleshooting"></a> ファイアウォール設定のトラブルシューティング  
 ファイアウォールの問題をトラブルシューティングする場合は、次のツールと手法が役立ちます。  
  
-   有効なポート ステータスは、ポートに関連付けられているすべてのルールの和集合です。 ポートへのアクセスをブロックしようとするときは、そのポート番号を参照しているすべての規則を確認することが役立つ場合があります。 セキュリティが強化された Windows ファイアウォールの MMC スナップインを使用して規則を確認し、ポート番号によってインバウンドおよびアウトバウンド規則を並べ替えます。  
  
-   [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] を実行しているコンピューターでアクティブになっているポートを確認します。 確認処理では、リッスンしている TCP/IP ポートの確認とポートの状態の確認も行います。  
  
     リッスンしているポートを確認するには、アクティブな TCP 接続と IP の統計情報を表示し、**netstat** コマンドライン ユーティリティを使用します。  
  
    #### <a name="to-list-which-tcpip-ports-are-listening"></a>リッスンしている TCP/IP ポートの一覧を表示するには  
  
    1.  コマンド プロンプト ウィンドウを開きます。  
  
    2.  コマンド プロンプトで、「 **netstat -n -a**」と入力します。  
  
         **-n** スイッチは、 **netstat** に対して、アクティブな TCP 接続のアドレスおよびポート番号を数字で表示するように指示します。 **-a** スイッチは、 **netstat** に対して、コンピューターがリッスンしている TCP ポートおよび UDP ポートを表示するように指示します。  
  
-   **PortQry** ユーティリティを使用して、TCP/IP ポートのステータスを LISTENING、NOT LISTENING、FILTERED としてレポートできます。 (フィルター処理された状態の場合、ユーティリティはポートからの応答を受信しない可能性があります)。**PortQry** ユーティリティは、[Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=17148)からダウンロードできます。  
  
## <a name="see-also"></a>参照  
 [Windows サーバー システムのサービス概要とネットワーク ポート要件](https://support.microsoft.com/kb/832017)   
 [方法: ファイアウォール設定を構成する (Azure SQL Database)](/azure/azure-sql/database/firewall-configure)  
  
