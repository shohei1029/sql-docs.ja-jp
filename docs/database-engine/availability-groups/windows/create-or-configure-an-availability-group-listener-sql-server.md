---
title: 可用性グループのリスナーの構成
description: 'PowerShell または SQL Server Management Studio を使用して、Always On 可用性グループのリスナーを構成する手順について説明します。 '
ms.custom: seo-lt-2019
ms.date: 05/17/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: how-to
f1_keywords:
- sql13.swb.availabilitygroup.newaglistener.general.f1
helpviewer_keywords:
- Availability Groups [SQL Server], listeners
- Availability Groups [SQL Server], client connectivity
ms.assetid: 2bc294f6-2312-4b6b-9478-2fb8a656e645
author: cawrites
ms.author: chadam
manager: erikre
ms.openlocfilehash: 90dcdcc2f6f7224718a9f2069e112b6f9a7a77e5
ms.sourcegitcommit: bacd45c349d1b33abef66db47e5aa809218af4ea
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104793104"
---
# <a name="configure-a-listener-for-an-always-on-availability-group"></a>Always On 可用性グループのリスナーの構成
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]
  このトピックでは、 *で* 、 [!INCLUDE[ssManStudioFull](../../../includes/ssmanstudiofull-md.md)]、または PowerShell を使用して、AlwaysOn 可用性グループに対して 1 つの [!INCLUDE[tsql](../../../includes/tsql-md.md)]可用性グループ リスナー [!INCLUDE[ssnoversion](../../../includes/ssnoversion-md.md)]を作成または構成する方法について説明します。  
  
> [!IMPORTANT]  
>  可用性グループの最初の可用性グループ リスナーを作成するには、[!INCLUDE[ssManStudioFull](../../../includes/ssmanstudiofull-md.md)]、[!INCLUDE[tsql](../../../includes/tsql-md.md)]、または [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] PowerShell を使用することを強くお勧めします。 必要な場合 (追加リスナーを作成する場合など) を除いて、WSFC クラスターでリスナーを直接作成することは避けてください。  
  
 
##  <a name="does-a-listener-exist-for-this-availability-group-already"></a><a name="DoesListenerExist"></a> この可用性グループに既にリスナーが存在するか  

 **可用性グループにリスナーが既に存在するかどうかを確認するには**  
  
-   [可用性グループ リスナーのプロパティの表示 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/view-availability-group-listener-properties-sql-server.md)  
  
> [!NOTE]  
>  リスナーが既に存在するときに追加リスナーを作成する場合は、このトピックの「 [可用性グループの追加のリスナーを作成するには (省略可能)](#CreateAdditionalListener)」を参照してください。  
  
##  <a name="limitations-and-restrictions"></a><a name="Restrictions"></a> 制限事項と制約事項  
  
-   [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]で、可用性グループに対して作成できるリスナーは 1 つのみです。 通常、それぞれの可用性グループで必要なリスナーは 1 つだけです。 ただし、一部の顧客シナリオでは、1 つの可用性グループで複数のリスナーが必要です。   SQL Server で 1 つのリスナーを作成した後、フェールオーバー クラスターの Windows PowerShell または WSFC フェールオーバー クラスター マネージャーを使用して、追加のリスナーを作成します。 詳細については、このトピックの後の「 [可用性グループの追加のリスナーを作成するには (省略可能)](#CreateAdditionalListener)」を参照してください。  
  
##  <a name="recommendations"></a><a name="Recommendations"></a> 推奨事項  
 複数のサブネットを構成する場合は、必須ではありませんが、静的 IP アドレスの使用をお勧めします。  
  
##  <a name="prerequisites"></a><a name="Prerequisites"></a> 前提条件  
  
-   プライマリ レプリカをホストするサーバー インスタンスに接続されている必要があります。  
  
-   複数のサブネットにわたる可用性グループ リスナーを設定し、静的 IP アドレスを使用する場合は、リスナーを作成する可用性グループの可用性レプリカがホストされているすべてのサブネットの静的 IP アドレスを取得する必要があります。 通常は、静的 IP アドレスをネットワーク管理者に問い合わせる必要があります。  
  
> [!IMPORTANT]  
>  初めてリスナーを作成する前に、「[AlwaysOn クライアント接続 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/always-on-client-connectivity-sql-server.md)」を読むことを強くお勧めします。  
  
###  <a name="requirements-for-the-dns-name-of-an-availability-group-listener"></a><a name="DNSnameReqs"></a> 可用性グループ リスナーの DNS 名の要件  
 各可用性グループ リスナーは、ドメインおよび NetBIOS 内で一意の DNS ホスト名を必要とします。 DNS 名は、文字列値です。 この名前には、英数字、ダッシュ/ハイフン (-)、およびアンダースコア (_) のみを任意の順序で含めることができます。 DNS ホスト名では大文字と小文字は区別されません。 最大長は 63 文字です。ただし、 [!INCLUDE[ssManStudioFull](../../../includes/ssmanstudiofull-md.md)]で指定できる最大長は 15 文字です。  
  
 意味のある文字列を指定することをお勧めします。 たとえば、可用性グループの名前が `AG1`の場合は、 `ag1-listener`のような意味のある DNS ホスト名にします。  
  
> [!IMPORTANT]  
>  NetBIOS では、dns_name の最初の 15 文字のみが認識されます。 同じ Active Directory で制御されている 2 つの WSFC クラスターがあり、両方のクラスターで可用性グループ リスナーを作成しようとする場合、15 文字より長い名前を使用して、15 文字のプレフィックスが同一であると、仮想ネットワーク名リソースをオンラインにできなかったことを示すエラーが表示されます。 DNS 名のプレフィックスに対する名前付け規則の詳細については、「 [ドメイン名を割り当てる](https://technet.microsoft.com/library/cc731265\(WS.10\).aspx)」を参照してください。  
  
##  <a name="windows-permissions"></a><a name="WinPermissions"></a> Windows 権限  
  
|アクセス許可|Link|  
|-----------------|----------|  
|可用性グループをホストしている WSFC クラスターのクラスター名オブジェクト (CNO) には、**Create Computer objects** アクセス許可が必要です。<br /><br /> Active Directory では、CNO は既定で **Create Computer objects** アクセス許可を明示的に持たず、仮想コンピューター オブジェクト (VCO) を最大で 10 個作成できます。 VCO を 10 個作成した後、追加で VCO を作成しようとしても失敗します。 この問題は、WSFC クラスターの CNO に権限を明示的に与えることで回避できます。 削除した可用性グループの VCO は Active Directory 内で自動的に削除されず、手動で削除しない限り、VCO の 10 個の既定の制限の対象としてカウントされます。<br /><br /> 注: 組織によっては、 **Create Computer objects** 権限を個別のユーザー アカウントに付与することがセキュリティ ポリシーで禁止されている場合があります。|*クラスターをインストールするユーザーのアカウントを構成する手順:* 「 [フェールオーバー クラスター ステップ バイ ステップ ガイド: Active Directory のアカウントの構成](https://technet.microsoft.com/library/cc731002\(WS.10\).aspx#BKMK_steps_installer)<br /><br /> *クラスター名アカウントを事前設定する手順:* 「 [フェールオーバー クラスター ステップ バイ ステップ ガイド: Active Directory のアカウントの構成](https://technet.microsoft.com/library/cc731002\(WS.10\).aspx#BKMK_steps_precreating)|  
|リスナーの仮想ネットワーク名にコンピューター アカウントを事前設定する必要がある場合は、 **Account Operator** グループのメンバーシップが必要です。または、ドメイン管理者に依頼する必要があります。|*クラスター化されたサービスまたはアプリケーションのアカウントを事前設定する手順:* 「 [フェールオーバー クラスター ステップ バイ ステップ ガイド: Active Directory のアカウントの構成](https://technet.microsoft.com/library/cc731002\(WS.10\).aspx#BKMK_steps_precreating2)」|  
  
> [!TIP]  
>  一般的には、リスナーの仮想ネットワーク名にコンピューター アカウントを事前設定しないことが最も簡単です。 可能な場合は、WSFC の高可用性ウィザードを実行する際にアカウントが自動的に作成および構成されるように構成します。  
  
##  <a name="sql-server-permissions"></a><a name="SqlPermissions"></a> SQL Server 権限  
  
|タスク|アクセス許可|  
|----------|-----------------|  
|可用性グループ リスナーを作成するには|**sysadmin** 固定サーバー ロールのメンバーシップと、CREATE AVAILABILITY GROUP サーバー権限、ALTER ANY AVAILABILITY GROUP 権限、CONTROL SERVER 権限のいずれかが必要です。|  
|既存の可用性グループ リスナーを変更するには|可用性グループの ALTER AVAILABILITY GROUP 権限、CONTROL AVAILABILITY GROUP 権限、ALTER ANY AVAILABILITY GROUP 権限、または CONTROL SERVER 権限が必要です。|  
  
##  <a name="using-sql-server-management-studio"></a><a name="SSMSProcedure"></a> SQL Server Management Studio の使用  
  
> [!TIP]  
>  [新しい可用性グループ ウィザード](../../../database-engine/availability-groups/windows/use-the-new-availability-group-dialog-box-sql-server-management-studio.md) は、新しい可用性グループに対するリスナーの作成をサポートします。  
  
 **可用性グループ リスナーを作成または構成するには**  
  
1.  オブジェクト エクスプローラーで、可用性グループのプライマリ レプリカをホストするサーバー インスタンスに接続し、サーバー名をクリックしてサーバー ツリーを展開します。  
  
2.  **[AlwaysOn 高可用性]** ノードと **[可用性グループ]** ノードを展開します。  
  
3.  リスナーを構成する可用性グループをクリックし、次のいずれかを選択します。  
  
    -   リスナーを作成するには、 **[可用性グループ リスナー]** ノードを右クリックし、 **[新しいリスナー]** をクリックします。 これにより、 **[新しい可用性グループ リスナー]** ダイアログ ボックスが開きます。 詳細については、このトピックの「 [[新しい可用性グループ リスナー] (ダイアログ ボックス)](#AddAgListenerDialog)」を参照してください。  
  
    -   既存のリスナーのポート番号を変更するには、 **[可用性グループ リスナー]** ノードを展開し、リスナーを右クリックして、 **[プロパティ]** をクリックします。 **[ポート]** フィールドに新しいポート番号を入力し、 **[OK]** をクリックします。  
  
###  <a name="new-availability-group-listener-dialog-box"></a><a name="AddAgListenerDialog"></a> [新しい可用性グループ リスナー] (ダイアログ ボックス)  
 **[リスナーの DNS 名]**  
 可用性グループ リスナーの DNS ホスト名を指定します。 DNS 名は、ドメインおよび NetBIOS 内で一意であることが必要な文字列です。 この名前には、英数字、ダッシュ (-)、およびハイフン (_) のみを任意の順序で含めることができます。 DNS ホスト名では大文字と小文字は区別されません。 最大長は 15 文字です。  
  
 詳細については、このトピックの前の「 [可用性グループ リスナーの DNS 名の要件](#DNSnameReqs)」を参照してください。  
  
 **[ポート]**  
 このリスナーで使用される TCP ポート。  
  
 **[ネットワーク モード]**  
 リスナーで使用される TCP プロトコルを指定します。次のいずれかです。  
  
 **[DHCP]**  
 リスナーは、動的ホスト構成プロトコル (DHCP) を実行しているサーバーによって割り当てられる動的 IP アドレスを使用します。 DHCP は、単一のサブネットに制限されます。  
  
> [!IMPORTANT]  
>  運用環境での DHCP の使用はお勧めしません。 ダウンタイムが発生して DHCP IP のリース期限が切れると、リスナーの DNS 名に関連付けられている新しい DHCP のネットワーク IP アドレスの登録に余分な時間がかかり、クライアント接続に影響が及びます。 ただし、開発環境とテスト環境を設定して可用性グループの基本機能を確認する場合や、アプリケーションとの統合の場合には DHCP が適しています。  
  
 **[静的 IP]**  
 リスナーは、1 つまたは複数の静的 IP アドレスを使用します。 必要に応じて追加の IP アドレスを指定できます。 複数のサブネットにわたる可用性グループ リスナーを作成するには、各サブネットのリスナー構成に静的 IP アドレスを指定する必要があります。 これらの静的 IP アドレスを取得するには、ネットワーク管理者に問い合わせてください。  
  
 **[静的 IP]** を選択した場合、 **[ネットワーク モード]** フィールドの下にサブネット グリッドが表示されます。 このグリッドに、この可用性グループ リスナーによってアクセスできる各サブネットについての情報が表示されます。 このグリッドは、 **[追加]** をクリックして静的 IP アドレスを追加するまでは空です。  
  
 次の列で構成されます。  
  
 **サブネット**  
 可用性グループ リスナーに追加される各サブネットの識別子が表示されます。  
  
 **IP アドレス**  
 特定のサブネットの IP アドレスが表示されます。  サブネットの IP アドレスには、IPv4 アドレスまたは IPv6 アドレスを使用できます。  
  
 **追加**  
 選択したサブネットまたはこのリスナーの別のサブネットに静的 IP アドレスを追加する場合にクリックします。 クリックすると、 **[IP アドレスの追加]** ダイアログ ボックスが開きます。 詳細については、「[[IP アドレスの追加] ダイアログ ボックス &#40;SQL Server Management Studio&#41;](../../../database-engine/availability-groups/windows/add-ip-address-dialog-box-sql-server-management-studio.md)」ヘルプ トピックを参照してください。  
  
 **Remove**  
 選択したサブネットをこのリスナーから削除する場合にクリックします。  
  
 **[OK]**  
 指定した可用性グループ リスナーを作成する場合にクリックします。  
  
##  <a name="using-transact-sql"></a><a name="TsqlProcedure"></a> Transact-SQL の使用  
 **可用性グループ リスナーを作成または構成するには**  
  
1.  プライマリ レプリカをホストするサーバー インスタンスに接続します。  
  
2.  [CREATE AVAILABILITY GROUP](../../../t-sql/statements/create-availability-group-transact-sql.md) ステートメントの LISTENER オプションまたは [ALTER AVAILABILITY GROUP](../../../t-sql/statements/alter-availability-group-transact-sql.md) ステートメントの ADD LISTENER オプションを使用します。  
  
     次の例では、可用性グループ リスナーを `MyAg2`という名前の既存の可用性グループに追加します。 このリスナーには、一意の DNS 名 `MyAg2ListenerIvP6`を指定します。 2 つのレプリカが異なるサブネット上に存在するため、(推奨されるように) リスナーで静的 IP アドレスを使用します。 WITH IP 句には、2 つの可用性レプリカについて、それぞれ IPv6 形式の静的 IP アドレス ( `2001:4898:f0:f00f::cf3c and 2001:4898:e0:f213::4ce2`) を指定します。 また、この例では、オプションの PORT 引数を使用して、 `60173` をリスナー ポートとして指定しています。  
  
    ```  
    ALTER AVAILABILITY GROUP MyAg2   
          ADD LISTENER 'MyAg2ListenerIvP6' ( WITH IP ( ('2001:db88:f0:f00f::cf3c'),('2001:4898:e0:f213::4ce2') ) , PORT = 60173 );   
    GO  
  
    ```  
  
##  <a name="using-powershell"></a><a name="PowerShellProcedure"></a> PowerShell の使用  
 **可用性グループ リスナーを作成または構成するには**  
  
1.  プライマリ レプリカをホストするサーバー インスタンスにディレクトリを変更 (**cd**) します 。  
  
2.  可用性グループ リスナーを作成または変更するには、次のコマンドレットのいずれかを使用します。  
  
     **New-SqlAvailabilityGroupListener**  
     新しい可用性グループ リスナーを作成して、既存の可用性グループにアタッチします。  
  
     たとえば、次の **New-SqlAvailabilityGroupListener** コマンドは、可用性グループ `MyListener` に対し、 `MyAg`という名前の可用性グループ リスナーを作成します。 このリスナーは、 **-StaticIp** パラメーターに渡された IPv4 アドレスを仮想 IP アドレスとして使用します。  
  
    ```  
    New-SqlAvailabilityGroupListener -Name MyListener `   
    -StaticIp '192.168.3.1/255.255.252.0' `   
    -Path SQLSERVER:\Sql\Computer\Instance\AvailabilityGroups\MyAg  
  
    ```  
  
     **Set-SqlAvailabilityGroupListener**  
     既存の可用性グループ リスナーのポート設定を変更します。  
  
     たとえば、次の **Set-SqlAvailabilityGroupListener** コマンドは、 `MyListener` という名前の可用性グループ リスナーのポート番号を `1535`に設定します。 このポートは、リスナーへの接続をリッスンするために使用されます。  
  
    ```  
    Set-SqlAvailabilityGroupListener -Port 1535 `   
    -Path SQLSERVER:\Sql\PrimaryServer\InstanceName\AvailabilityGroups\MyAg\AGListeners\MyListener  
  
    ```  
  
     **Add-SqlAGListenerstaticIp**  
     既存の可用性グループ リスナー構成に静的 IP アドレスを追加します。 IP アドレスには、サブネットを含む IPv4 アドレス、または IPv6 アドレスを指定できます。  
  
     たとえば、次の **Add-SqlAGListenerstaticIp** コマンドは、可用性グループ `MyListener` の可用性グループ リスナー `MyAg`に静的 IPv4 アドレスを追加します。 この IPv6 アドレスは、サブネット `255.255.252.0`でリスナーの仮想 IP アドレスとして機能します。 可用性グループが複数のサブネットにまたがっている場合は、各サブネットの静的 IP アドレスをリスナーに追加する必要があります。  
  
    ```  
    $path = "SQLSERVER:\SQL\PrimaryServer\InstanceName\AvailabilityGroups\MyAg\AGListeners\ MyListener" `   
    Add-SqlAGListenerstaticIp -Path $path `   
    -StaticIp "2001:0db8:85a3:0000:0000:8a2e:0370:7334"  
    ```  
  
    > [!NOTE]  
    >  コマンドレットの構文を表示するには、 **PowerShell 環境で**  Get-Help [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] コマンドレットを使用します。 詳細については、「 [Get Help SQL Server PowerShell](../../../powershell/sql-server-powershell.md)」を参照してください。  
  
 **SQL Server PowerShell プロバイダーを設定して使用するには**  
  
-   [SQL Server PowerShell プロバイダー](../../../powershell/sql-server-powershell-provider.md)  
  
## <a name="troubleshooting"></a>トラブルシューティング  
  
###  <a name="failure-to-create-an-availability-group-listener-because-of-active-directory-quotas"></a><a name="ADQuotas"></a> Active Directory クォータが原因で可用性グループ リスナーの作成が失敗する問題  
 新しい可用性グループ リスナーの作成は、参加しているクラスター ノード マシン アカウントの Active Directory クォータに達したため、失敗する場合があります。  詳細については、次の記事を参照してください。  
  
-   [コンピューター オブジェクト変更時のクラスター サービス アカウントのトラブルシューティング方法](https://support.microsoft.com/kb/307532)  
  
-   [Active Directory クォータ](https://technet.microsoft.com/library/cc904295\(WS.10\).aspx)  
  
##  <a name="follow-up-after-creating-an-availability-group-listener"></a><a name="FollowUp"></a> 補足情報: 可用性グループ リスナーの作成後  
  
###  <a name="multisubnetfailover-keyword-and-associated-features"></a><a name="MultiSubnetFailover"></a> MultiSubnetFailover のキーワードおよび関連機能  
 **MultiSubnetFailover** は、SQL Server 2012 の AlwaysOn 可用性グループおよび AlwaysOn フェールオーバー クラスター インスタンスに対して高速フェールオーバーを有効にするために使用する新しい接続文字列キーワードです。 接続文字列で `MultiSubnetFailover=True` が設定されていると、次の 3 つのサブ機能が有効になります。  
  
-   AlwaysOn 可用性グループまたはフェールオーバー クラスター インスタンスに対する複数サブネット リスナーへのより高速なマルチサブネット フェールオーバー。  
  
-   AlwaysOn 可用性グループまたはフェールオーバー クラスター インスタンスの単一サブネット リスナーへのより高速な単一サブネット フェールオーバー。  
  
    -   この機能は、単一のサブネット内に単一の IP を持つリスナーに接続する場合に使用されます。 TCP 接続の再試行をより積極的に実行して、単一サブネット フェールオーバーを高速化します。  
  
-   マルチサブネット AlwaysOn フェールオーバー クラスター インスタンスへの名前付きインスタンスの解決。  
  
    -   複数サブネット エンドポイントを持つ AlwaysOn フェールオーバー クラスター インスタンスの名前付きインスタンス解決サポートを追加します。  
  
 **.NET Framework 3.5 および OLEDB で MultiSubnetFailover=True はサポートされない**  
  
 **問題点:** 異なるサブネットからの複数の IP アドレスに応じて可用性グループまたはフェールオーバー クラスター インスタンスにリスナー名 (ネットワーク名または WSFC クラスター マネージャーのクライアント アクセス ポイント) がある場合に、ADO.NET with .NET Framework 3.5SP1 または SQL Native Client 11.0 OLEDB を使用していると、可用性グループ リスナーに対するクライアント接続要求の 50% が接続タイムアウトに達する可能性があります。  
  
 **回避策:** 次のいずれかのタスクを実行することをお勧めします。  
  
-   クラスター リソースを操作する権限がない場合は、接続タイムアウトを 30 秒に設定します (この値は結果として、20 秒の TCP タイムアウトと 10 秒のバッファーになります)。  
  
     **長所:** :クロスサブネット フェールオーバーが発生した場合、クライアントの復旧時間が短くなります。  
  
     **短所**:半数のクライアント接続に 20 秒以上要します  
  
-   クラスター リソースを操作する権限がある場合は、可用性グループ リスナーのネットワーク名を `RegisterAllProvidersIP=0`に設定する方法をお勧めします。 詳細については、このセクションの「RegisterAllProvidersIP の設定」を参照してください。  
  
     **長所:** クライアント接続のタイムアウト値を大きくする必要がありません。  
  
     **短所:** クロスサブネット フェールオーバーが発生した場合、クロスサイト DNS/AD レプリケーション スケジュールの **HostRecordTTL** 設定によっては、クライアントの復旧時間が 15 分以上になる可能性があります。  
  
###  <a name="registerallprovidersip-setting"></a><a name="RegisterAllProvidersIP"></a> RegisterAllProvidersIP の設定  
 [!INCLUDE[ssManStudioFull](../../../includes/ssmanstudiofull-md.md)]、 [!INCLUDE[tsql](../../../includes/tsql-md.md)]、または PowerShell を使用して可用性グループ リスナーを作成すると、WSFC にクライアント アクセス ポイントが作成され、その **RegisterAllProvidersIP** プロパティが 1 (true) に設定されます。 このプロパティ値の効果は、次に示すように、クライアント接続文字列によって異なります。  
  
-   **MultiSubnetFailover** を true に設定する接続文字列  
  
     [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] は、クライアント接続文字列で  **(推奨どおり) を指定しているクライアントのフェールオーバー後の再接続時間を短縮するために、** RegisterAllProvidersIP `MultiSubnetFailover = True`プロパティを 1 に設定します。 リスナーのマルチサブネット機能を活用するには、クライアントに **MultiSubnetFailover** キーワードをサポートするデータ プロバイダーが必要な場合があります。 マルチサブネット フェールオーバーのドライバー サポートについては、「[AlwaysOn クライアント接続 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/always-on-client-connectivity-sql-server.md)」を参照してください。  
  
     マルチサブネット クラスタリングについては、「 [SQL Server マルチサブネット クラスタリング &#40;SQL Server&#41;](../../../sql-server/failover-clusters/windows/sql-server-multi-subnet-clustering-sql-server.md)を作成または構成する方法について説明します。  
  
    > [!TIP]  
    >  `RegisterAllProvidersIP = 1`のときに、WSFC クラスターに対して WSFC の構成の検証ウィザードを実行すると、次の警告メッセージが表示されます。  
    >   
    >  "ネットワーク名 'Name:<network_name>' の RegisterAllProviderIP プロパティは 1 に設定されています。現在のクラスター構成の場合、この値は 0 に設定する必要があります。"  
    >   
    >  このメッセージは無視してください。  
  
-   **MultiSubnetFailover** を true に設定しない接続文字列  
  
     `RegisterAllProvidersIP = 1`の場合、接続文字列で `MultiSubnetFailover = True`を使用しないクライアントは、接続の待機時間が長くなります。 これが発生するのは、このようなクライアントはすべての IP への接続を順に試行するためです。 これに対し、 **RegisterAllProvidersIP** を 0 に変更すると、WSFC クラスターのクライアント アクセス ポイントにアクティブな IP アドレスが登録され、レガシ クライアントの待機時間が短縮されます。 したがって、可用性グループ リスナーに接続する必要がり、**MultiSubnetFailover** プロパティを使用できないレガシ クライアントがある場合は、**RegisterAllProvidersIP** を 0 に変更することをお勧めします。  
  
    > [!IMPORTANT]  
    >  WSFC クラスター (フェールオーバー クラスター マネージャーの GUI) を通して可用性グループ リスナーを作成すると、 **RegisterAllProvidersIP** は既定で 0 (false) になります。  
  
###  <a name="hostrecordttl-setting"></a><a name="HostRecordTTL"></a> HostRecordTTL の設定  
 既定では、クライアントは 20 分間、クラスター DNS レコードをキャッシュします。  **HostRecordTTL** の値 (キャッシュするレコードの有効期限 (TTL)) を小さくすると、レガシ クライアントはよりすばやく再接続できるようになります。  ただし、**HostRecordTTL** の設定を小さくすると、DNS サーバーへのトラフィックが増加する可能性があります。  
  
###  <a name="sample-powershell-script-to-disable-registerallprovidersip-and-reduce-ttl"></a><a name="SampleScript"></a> RegisterAllProvidersIP を無効にし、TTL を短縮する PowerShell サンプル スクリプト  
 次の PowerShell の例では、リスナー リソースに対する **RegisterAllProvidersIP** クラスター パラメーターと **HostRecordTTL** クラスター パラメーターの両方を構成する方法を示しています。  DNS レコードは、既定の 20 分間ではなく、5 分間キャッシュされます。  両方のクラスター パラメーターを変更すると、 **MultiSubnetFailover** パラメーターを使用できないレガシ クライアントのフェールオーバーが発生した後に、適切な IP アドレスに接続する時間が短縮される可能性があります。  `yourListenerName` は、変更対象のリスナーの名前に置き換えてください。  
  
```  
Import-Module FailoverClusters  
Get-ClusterResource yourListenerName | Set-ClusterParameter RegisterAllProvidersIP 0   
Get-ClusterResource yourListenerName | Set-ClusterParameter HostRecordTTL 300  
Stop-ClusterResource yourListenerName  
Start-ClusterResource yourListenerName  
Start-Clustergroup yourListenerGroupName
```  
  
 フェールオーバー中の復旧時間の詳細については、「 [Client Recovery Latency During Failover](../../../sql-server/failover-clusters/windows/sql-server-multi-subnet-clustering-sql-server.md#DNS)」を参照してください。  
  
###  <a name="follow-up-recommendations"></a><a name="FollowUpRecommendations"></a> 補足情報: 推奨事項  
 可用性グループ リスナーを作成した後:  
  
-   リスナーの IP アドレスが排他的に使用されるように確保することを、ネットワーク管理者に依頼します。  
  
-   この可用性グループへのクライアント接続を要求するときの接続文字列で使用できるよう、リスナーの DNS ホスト名をアプリケーション開発者に通知します。  
  
-   可能であれば、 `MultiSubnetFailover = True`を指定するようにクライアント接続文字列を更新することを開発者に勧めます。 マルチサブネット フェールオーバーのドライバー サポートについては、「[AlwaysOn クライアント接続 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/always-on-client-connectivity-sql-server.md)」を参照してください。  
  
###  <a name="create-an-additional-listener-for-an-availability-group-optional"></a><a name="CreateAdditionalListener"></a> 可用性グループの追加のリスナーの作成 (省略可能)  
 SQL Server で 1 つのリスナーを作成した後、次の手順で追加のリスナーを追加できます。  
  
1.  次のツールのいずれかを使用してリスナーを作成します。  
  
    -   **WSFC フェールオーバー クラスター マネージャーの使用:**  
  
        1.  クライアント アクセス ポイントを追加し、IP アドレスを構成します。  
  
        2.  リスナーをオンラインにします。  
  
        3.  WSFC 可用性グループに対する依存関係を追加します。  
  
         フェールオーバー クラスター マネージャーのダイアログ ボックスおよびタブの詳細については、「 [ユーザー インターフェイス: フェールオーバー クラスター マネージャー スナップイン](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc772502(v=ws.11))」を参照してください。  
  
    -   **フェールオーバー クラスターの Windows PowerShell の使用:**  
  
        1.  [Add-ClusterResource](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee460983(v=technet.10)) を使用して、ネットワーク名と IP アドレス リソースを作成します。  
  
        2.  [Start-ClusterResource](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee461056(v=technet.10)) を使用して、ネットワーク名リソースを開始します。  
  
        3.  [Add-ClusterResourceDependency](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee461014(v=technet.10)) を使用して、ネットワーク名と、既存の SQL Server 可用性グループ リソース間の依存関係を設定します。  
  
         フェールオーバー クラスターの Windows PowerShell の詳細については、「 [サーバー マネージャーのコマンドの概要](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc732757(v=ws.11)#BKMK_wps)」を参照してください。  
  
2.  [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] で新しいリスナーのリッスンを開始します。 追加リスナーを作成した後、プライマリ レプリカをホストする [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] のインスタンスに接続し、 [!INCLUDE[ssManStudioFull](../../../includes/ssmanstudiofull-md.md)]、 [!INCLUDE[tsql](../../../includes/tsql-md.md)]、または PowerShell を使用してリスナー ポートを変更します。  
  
 詳細については、「 [同じ可用性グループの複数のリスナーを作成する方法](/archive/blogs/sqlalwayson/how-to-create-multiple-listeners-for-same-availability-group-goden-yao) 」(SQL Server AlwaysOn チームのブログ) を参照してください。  
  
 
  
## <a name="next-steps"></a>次の手順

これでリスナーを作成したので、[リスナーに接続する](listeners-client-connectivity-application-failover.md)ようにアプリケーションを構成します。 また、可用性グループの正常性を確保するために、さまざまな[可用性グループの監視戦略](monitoring-of-availability-groups-sql-server.md)を確認することもできます。

また、必要に応じて、[リスナーのプロパティを表示](view-availability-group-listener-properties-sql-server.md)したり、[リスナーを削除する](remove-an-availability-group-listener-sql-server.md)方法を学習したりすることもできます。
