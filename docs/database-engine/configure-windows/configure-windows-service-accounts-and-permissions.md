---
title: Windows サービス アカウントとアクセス許可の構成 | Microsoft Docs
description: SQL Server でサービスを開始および実行するために使用されるサービス アカウントについて理解します。 その構成方法と適切なアクセス許可の割り当て方法を確認します。
ms.custom: contperf-fy20q4
ms.date: 03/17/2020
ms.prod: sql
ms.prod_service: high-availability
ms.reviewer: ''
ms.technology: configuration
ms.topic: reference
helpviewer_keywords:
- startup service states [SQL Server]
- Setup [SQL Server], user accounts
- Windows permissions [SQL Server]
- modifying user accounts
- default accounts
- domains [SQL Server], user accounts
- startup accounts [SQL Server]
- system accounts [SQL Server]
- services [SQL Server], permissions
- ACL (access control list)
- local system accounts [SQL Server]
- instance-aware services [SQL Server]
- permissions [SQL Server], services
- SQL Server Agent service, user accounts
- Windows NT permissions [SQL Server]
- user accounts [SQL Server]
- identifying instance-unaware services [SQL Server]
- installing SQL Server, user accounts
- disabled startup state [SQL Server]
- user accounts [SQL Server], users
- Local Service account [SQL Server]
- SQL Server Installation Wizard
- instance-unaware services [SQL Server]
- services [SQL Server], configuring at installation
- Windows accounts [SQL Server]
- SQL Server services, user accounts
- user accounts [SQL Server], services
- MSSQLServer
- identifying instance-aware services [SQL Server]
- services [SQL Server], accounts
- access control lists
- optional accounts [SQL Server]
- service accounts [SQL Server]
- accounts [SQL Server], services
- built-in system accounts [SQL Server]
- automatic startup state
- domains [SQL Server]
- manual startup state [SQL Server]
- accounts [SQL Server], user
ms.assetid: 309b9dac-0b3a-4617-85ef-c4519ce9d014
author: markingmyname
ms.author: maghan
ms.openlocfilehash: 525f08bada39e02a3dfc4f834f5106be18571e36
ms.sourcegitcommit: 00af0b6448ba58e3685530f40bc622453d3545ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104673976"
---
# <a name="configure-windows-service-accounts-and-permissions"></a>Windows サービス アカウントと権限の構成

 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の各サービスは、Windows で [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 操作の認証を管理するための、1 つのプロセスまたはプロセス セットを表しています。 このトピックでは、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]のこのリリースにおける既定のサービス構成、および [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインストール時およびインストール後に設定できる [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] サービスの構成オプションについて説明します。 このトピックでは、上級ユーザー向けにサービス アカウントの詳細について説明します。

 ほとんどのサービスとそのプロパティは、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 構成マネージャーを使用して構成できます。 最新の 4 つのバージョンへのパスを次に示します (Windows が C ドライブにインストールされている場合)。

|SQL Server のバージョン|Path|
|-|-|
|SQL Server 2019|C:\Windows\SysWOW64\SQLServerManager15.msc|
|SQL Server 2017|C:\Windows\SysWOW64\SQLServerManager14.msc|
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 2016|C:\Windows\SysWOW64\SQLServerManager13.msc|
|[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)]|C:\Windows\SysWOW64\SQLServerManager12.msc|
|[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)]|C:\Windows\SysWOW64\SQLServerManager11.msc|

## <a name="services-installed-by-ssnoversion"></a><a name="Service_Details"></a>[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] でインストールされるサービス

インストールするコンポーネントに応じて、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップによって次のサービスがインストールされます。

- **[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] データベース サービス** - [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] リレーショナル [!INCLUDE[ssDE](../../includes/ssde-md.md)]のサービスです。 実行可能ファイルのパスは \<MSSQLPATH>\MSSQL\Binn\sqlservr.exe です。
- **[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エージェント** - ジョブの実行、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]の監視、および警告の発行を行い、一部の管理タスクを自動化できるようにします。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エージェント サービスは存在しますが、 [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)]のインスタンスでは無効になっています。 実行可能ファイルのパスは \<MSSQLPATH>\MSSQL\Binn\sqlagent.exe です。
- **[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]** - ビジネス インテリジェンス アプリケーション用のオンライン分析処理 (OLAP) とデータ マイニング機能が用意されています。 実行可能ファイルのパスは \<MSSQLPATH>\OLAP\Bin\msmdsrv.exe です。
- **[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]** - レポートの管理、実行、作成、スケジュール、配信を行います。 実行可能ファイルのパスは \<MSSQLPATH>\Reporting Services\ReportServer\Bin\ReportingServicesService.exe です。
- **[!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)]** - [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] パッケージのストレージと実行に対する管理サポートを提供します。 実行可能ファイルのパスは \<MSSQLPATH>\130\DTS\Binn\MsDtsSrvr.exe です。

   [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] には、展開のスケール アウト用に追加のサービスが含まれている場合があります。 詳細については、「[チュートリアル:Integration Services (SSIS) Scale Out を設定する](../../integration-services/scale-out/walkthrough-set-up-integration-services-scale-out.md)」を参照してください。

- **[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Browser** - クライアント コンピューター用の [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 接続情報を提供する名前解決サービスです。 実行可能ファイルは c:\Program Files (x86)\Microsoft SQL Server\90\Shared\sqlbrowser.exe です。
- **フルテキスト検索** - コンテンツに対するフルテキスト インデックスと構造化データおよび半構造化データのプロパティをすばやく作成し、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]に対するドキュメントのフィルター処理および単語区切りを可能にします。
- **SQL ライター** - ボリューム シャドウ コピー サービス (VSS) フレームワークで動作するアプリケーションのバックアップと復元を可能にします。
- **[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Distributed Replay Controller** - 複数の Distributed Replay クライアント コンピューターにトレース再生オーケストレーションを提供します。
- **[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Distributed Replay Client** - [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]のインスタンスに対して同時実行ワークロードをシミュレーションするために Distributed Replay コントローラーと共に動作する、1 つ以上の Distributed Replay クライアント コンピューターです。
- **[!INCLUDE[rsql_launchpad](../../includes/rsql-launchpad-md.md)]** - R サービスまたは Machine Learning Services の一部としてインストールされる R または Python ランタイムなど、Microsoft によって提供される外部の実行可能ファイルをホストする信頼されたサービスです。 サテライト プロセスは Launchpad プロセスによって起動できますが、個々のインスタンスの構成に基づいてリソースが制御されます。 Launchpad サービスは独自のユーザー アカウントで実行され、特定の登録済みランタイムの各サテライト プロセスは Launchpad のユーザー アカウントを継承します。 サテライト プロセスは、実行時に要求に応じて作成および破棄されます。

  ドメイン コントローラーとしても使われているコンピューターに SQL Server をインストールした場合、Launchpad は使用するアカウントを作成できません。 そのため、ドメイン コントローラーでの R Services (データベース内) または Machine Learning サービス (データベース内) のセットアップは失敗します。

- **SQL Server PolyBase エンジン** - 外部データ ソースに対する分散クエリ機能を提供します。
- **SQL Server PolyBase Data Movement Service** - SQL Server と外部データ ソースの間、および PolyBase スケールアウト グループ内の SQL ノードの間のデータ移動を有効にします。

## <a name="service-properties-and-configuration"></a><a name="Serv_Prop"></a> サービスのプロパティおよび構成

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] を開始して実行するために使用する開始アカウントは、 [ドメイン ユーザー アカウント](#Domain_User)、 [ローカル ユーザー アカウント](#Local_User)、 [マネージド サービス アカウント](#MSA)、 [仮想アカウント](#VA_Desc)、または [ビルトイン システム アカウント](#Local_Service)のいずれでも可能です。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の各サービスを開始して実行するには、インストール時に構成された開始アカウントが必要です。

ここでは、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] サービスを開始するために構成できるアカウント、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップで使用される既定値、サービスごとの SID の概念、スタートアップ オプション、およびファイアウォールの構成について説明します。

- [既定のサービス アカウント](#Default_Accts)
- [自動起動](#Auto_Start)
- [サービスのスタートアップの種類の構成](#Configure_services)
- [ファイアウォール ポート](#Firewall)

### <a name="default-service-accounts"></a><a name="Default_Accts"></a> 既定のサービス アカウント

次の表に、すべてのコンポーネントをインストールするときにセットアップで使用される既定のサービス アカウントを示します。 ここに示されている既定のアカウントは、特に断りがない限り、推奨のアカウントです。

**スタンドアロン サーバーまたはドメイン コント ローラー**

|コンポーネント|[!INCLUDE[nextref_longhorn](../../includes/nextref-longhorn-md.md)]|Windows 7 および [!INCLUDE[nextref_longhorn](../../includes/nextref-longhorn-md.md)] R2 以降|
|---------------|------------------------------------|----------------------------------------------------------------|
|[!INCLUDE[ssDE](../../includes/ssde-md.md)]|[ネットワーク サービス](#Network_Service)|[仮想アカウント](#VA_Desc)*|
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エージェント|[ネットワーク サービス](#Network_Service)|[仮想アカウント](#VA_Desc)*|
|[!INCLUDE[ssAS](../../includes/ssas-md.md)]|[ネットワーク サービス](#Network_Service)|[仮想アカウント](#VA_Desc)\* \*\*|
|[!INCLUDE[ssIS](../../includes/ssis-md.md)]|[ネットワーク サービス](#Network_Service)|[仮想アカウント](#VA_Desc)\*|
|[!INCLUDE[ssRS](../../includes/ssrs.md)]|[ネットワーク サービス](#Network_Service)|[仮想アカウント](#VA_Desc)\*|
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 分散再生コントローラー|[ネットワーク サービス](#Network_Service)|[仮想アカウント](#VA_Desc)\*|
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 分散再生クライアント|[ネットワーク サービス](#Network_Service)|[仮想アカウント](#VA_Desc)\*|
|FD ランチャー (フルテキスト検索)|[ローカル サービス](#Local_Service)|[仮想アカウント](#VA_Desc)|
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ブラウザー|[ローカル サービス](#Local_Service)|[ローカル サービス](#Local_Service)|
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] VSS Writer|[ローカル システム](#Local_System)|[ローカル システム](#Local_System)|
|[!INCLUDE[rsql_extensions](../../includes/rsql-extensions-md.md)]|NTSERVICE\MSSQLLaunchpad|NTSERVICE\MSSQLLaunchpad|
|PolyBase エンジン |[ネットワーク サービス](#Network_Service) |[ネットワーク サービス](#Network_Service)|
|PolyBase Data Movement Service |[ネットワーク サービス](#Network_Service) |[ネットワーク サービス](#Network_Service)|

\*[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] コンピューターの外部のリソースが必要になる場合、[!INCLUDE[msCoName](../../includes/msconame-md.md)] では、必要な最小限の特権で構成された、管理されたサービス アカウント (MSA) を使用することをお勧めします。
\*\* ドメイン コントローラーにインストールする場合、サービス アカウントとしての仮想アカウントはサポートされません。

**SQL Server フェールオーバー クラスター インスタンス**

|コンポーネント|[!INCLUDE[nextref_longhorn](../../includes/nextref-longhorn-md.md)]|[!INCLUDE[nextref_longhorn](../../includes/nextref-longhorn-md.md)] R2|
|---------------|------------------------------------|---------------------------------------|
|[!INCLUDE[ssDE](../../includes/ssde-md.md)]|[なし] : [ドメイン ユーザー](#Domain_User) アカウントを提供します。|[ドメイン ユーザー](#Domain_User) アカウントを提供します。|
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エージェント|[なし] : [ドメイン ユーザー](#Domain_User) アカウントを提供します。|[ドメイン ユーザー](#Domain_User) アカウントを提供します。|
|[!INCLUDE[ssAS](../../includes/ssas-md.md)]|[なし] : [ドメイン ユーザー](#Domain_User) アカウントを提供します。|[ドメイン ユーザー](#Domain_User) アカウントを提供します。|
|[!INCLUDE[ssIS](../../includes/ssis-md.md)]|[ネットワーク サービス](#Network_Service)|[仮想アカウント](#VA_Desc)|
|[!INCLUDE[ssRS](../../includes/ssrs.md)]|[ネットワーク サービス](#Network_Service)|[仮想アカウント](#VA_Desc)|
|FD ランチャー (フルテキスト検索)|[ローカル サービス](#Local_Service)|[仮想アカウント](#VA_Desc)|
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ブラウザー|[ローカル サービス](#Local_Service)|[ローカル サービス](#Local_Service)|
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] VSS Writer|[ローカル システム](#Local_System)|[ローカル システム](#Local_System)|

#### <a name="changing-account-properties"></a><a name="Changing_Accounts"></a> アカウント プロパティの変更

> [!IMPORTANT]
>
> - [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のサービスまたは [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エージェントのサービスが使用するアカウントやパスワードを変更する場合は、 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] のツール ( [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 構成マネージャーなど) を必ず使用してください。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 構成マネージャーでは、アカウント名の変更だけでなく、 [!INCLUDE[ssDE](../../includes/ssde-md.md)]サービス マスター キーを保護する Windows ローカル セキュリティ ストアの更新など、付加的な構成も行うことができます。 Windows サービス コントロール マネージャーなどの他のツールでもアカウント名を変更できますが、必要なすべての設定を変更することはできません。
> - SharePoint ファームに配置した [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] インスタンスの場合、 [!INCLUDE[ssGeminiMTS](../../includes/ssgeminimts-md.md)] アプリケーションと [!INCLUDE[ssGeminiSrv](../../includes/ssgeminisrv-md.md)]のサーバー アカウントを変更するときは、必ず SharePoint サーバーの全体管理を使用するようにしてください。 サーバーの全体管理を使用すると、関連の設定と権限が更新され、新しいアカウント情報が使用されます。
> - [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] オプションを変更するには、Reporting Services 構成ツールを使用します。

### <a name="managed-service-accounts-group-managed-service-accounts-and-virtual-accounts"></a><a name="New_Accounts"></a> 管理されたサービス アカウント、グループ管理サービス アカウント、仮想アカウント

管理されたサービス アカウント、グループ管理サービス アカウント、および仮想アカウントは、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] などの重要なアプリケーションをアプリケーション独自のアカウントとは別に提供するために設計され、管理者はこれらのアカウントのサービス プリンシパル名 (SPN) や資格情報を手動で管理する必要はありません。 これらにより、サービス アカウントのユーザー、パスワード、SPN の長期的な管理は非常に簡単になります。

- <a name="MSA"></a>**管理されたサービス アカウント**

  管理されたサービス アカウント (MSA) は、ドメイン コント ローラーによって作成および管理されるドメイン アカウントの一種です。 管理されたサービス アカウントは、サービスの実行に使用する 1 つのメンバー コンピューターに割り当てられます。 パスワードは、ドメイン コント ローラーによって自動的に管理されます。 MSA を使用して、コンピューターにログインすることはできませんが、コンピューターは、MSA を使用して Windows サービスを開始することができます。 MSA では、servicePrincipalName の読み取りおよび書き込み権限が付与されている場合、Active Directory 内でサービス プリンシパル名 (SPN) を登録することができます。 MSA には、 **DOMAIN\ACCOUNTNAME$** など、 **$** サフィックスを伴う名前が付けられます。 MSA を指定する場合は、パスワードを空白のままにします。 MSA は、1 つのコンピューターに割り当てられているため、Windows クラスターの異なるノード上では使用できません。

  > [!NOTE]
  > MSA は、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップが MSA を [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] サービスで使用する前に、ドメイン管理者が Active Directory に作成する必要があります。

- <a name="GMSA"></a> **グループ管理サービス アカウント**

  グループ管理サービス アカウント (gMSA) は、複数サーバーのための MSA です。 Windows は、サーバーのグループで実行されているサービスのサービス アカウントを管理します。 Active Directory は、サービスを再起動することなくグループ管理サービス アカウントのパスワードを自動的に更新します。 グループ管理サービス アカウントのプリンシパルを使用するように SQL Server のサービスを構成できます。 SQL Server 2014 より、SQL Server はスタンドアロン インスタンスのグループ管理サービス アカウントを、SQL Server 2016 以降ではフェールオーバー クラスター インスタンスと可用性グループのグループ管理サービス アカウントをサポートします。

  SQL Server 2014 以降に gMSA を使用するには、オペレーティング システムが Windows Server 2012 R2 以降である必要があります。 Windows Server 2012 R2 のサーバーでは、パスワード変更直後に中断することなくサービスがログインできるためには、 [KB 2998082](https://support.microsoft.com/kb/2998082) を適用する必要があります。

  詳細については、「[グループの管理されたサービス アカウントの概要](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831782(v=ws.11))」を参照してください。

  > [!NOTE]
  > ドメイン管理者は、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のセットアップで [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] サービスに使用できるよう、それより前に gMSA を Active Directory に作成する必要があります。

- <a name="VA_Desc"></a>**Virtual Accounts**

  Windows Server 2008 R2 および Windows 7 で追加された仮想アカウントは *管理されたローカル アカウント* であり、サービスの管理を簡単にする次の機能を使用できます。 仮想アカウントは自動的に管理され、ドメイン環境でネットワークにアクセスすることができます。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のセットアップでサービス アカウントに既定値を使用した場合、**NT SERVICE\\** _\<SERVICENAME>_ の形式でインスタンス名をサービス名として用いる仮想アカウントが使用されます。 仮想アカウントとして実行されるサービスは、 *<ドメイン名>* __\\__ *<コンピューター名>* __$__ の形式で、コンピューター アカウントの資格情報を使用してネットワーク リソースにアクセスします。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] を起動するために仮想アカウントを指定する場合は、パスワードを空白のままにします。 仮想アカウントのサービス プリンシパル名 (SPN) を登録していない場合は、SPN を手動で登録します。 SPN の手動登録の詳細については、 [SPN の手動登録](register-a-service-principal-name-for-kerberos-connections.md)に関するページを参照してください。

  > [!NOTE]
  > 仮想アカウントは、クラスターの各ノードで同じ SID を使用することができないので、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] フェールオーバー クラスター インスタンスでは使用できません。

  次の表に仮想アカウント名の例を示します。

  |サービス|仮想アカウント名|
  |-------------|--------------------------|
  |[!INCLUDE[ssDE](../../includes/ssde-md.md)] サービスの既定のインスタンス|**NT SERVICE\MSSQLSERVER**|
  |**PAYROLL** という名前の [!INCLUDE[ssDE](../../includes/ssde-md.md)] サービスの名前付きインスタンス|**NT SERVICE\MSSQL$PAYROLL**|
  |[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の既定のインスタンスの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エージェント サービス|**NT SERVICE\SQLSERVERAGENT**|
  |**PAYROLL** という名前の [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エージェント サービス|**NT SERVICE\SQLAGENT$PAYROLL**|

管理されたサービス アカウントと仮想アカウントの詳細については、「 **Service Accounts Step-by-Step Guide** 」 (サービス アカウントのステップ バイ ステップ ガイド) の「 [Managed service account and virtual account concepts](https://technet.microsoft.com/library/dd548356\(WS.10\).aspx) 」 (管理されたサービス アカウントと仮想アカウントの概念) および「 [Managed Service Accounts Frequently Asked Questions (FAQ)](https://technet.microsoft.com/library/ff641729\(WS.10\).aspx)」 (管理されたサービス アカウントに関してよく寄せられる質問 (FAQ)) をご覧ください。

**セキュリティに関する注意:** [!INCLUDE[ssNoteLowRights](../../includes/ssnotelowrights-md.md)] 可能な場合は、[MSA](#MSA)、[gMSA](#GMSA)、または [仮想アカウント](#VA_Desc)を使用してください。 MSA、gMSA、または仮想アカウントを使用できない場合は、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] サービス用の共有アカウントの代わりに、特権レベルの低い特定のユーザー アカウントまたはドメイン アカウントを使用します。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] サービスごとに個別のアカウントを使用します。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] サービス アカウントまたはサービス グループに追加の権限を付与しないでください。 権限は、グループのメンバーシップを通じて付与されるか、サービス SID がサポートされている場合にはサービス SID に直接付与されます。

### <a name="automatic-startup"></a><a name="Auto_Start"></a> 自動起動

ユーザー アカウントに加え、各サービスには 3 種類の起動時の状態があります。これらの状態はユーザーによる制御が可能です。

- **無効** サービスがインストールされていますが、現在は実行されていません。
- **手動** サービスがインストールされていますが、別のサービスまたはアプリケーションでその機能が必要な場合のみ開始されます。
- **自動** サービスはオペレーティング システムによって自動的に起動されます。

起動時の状態は、セットアップ中に選択されます。 名前付きインスタンスをインストールする場合は、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Browser サービスが自動的に開始されるように設定する必要があります。

### <a name="configuring-services-during-unattended-installation"></a><a name="Configure_services"></a> 無人インストール中のサービスの構成

次の表に、インストール時に構成できる [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] サービスを示します。 自動インストールを行う場合は、構成ファイルまたはコマンド プロンプトでスイッチを使用できます。

|SQL Server サービス名|自動インストールのスイッチ\*|
|-----------------------------|---------------------------------------------|
|MSSQLSERVER|SQLSVCACCOUNT、SQLSVCPASSWORD、SQLSVCSTARTUPTYPE|
|SQLServerAgent\*\*|AGTSVCACCOUNT、AGTSVCPASSWORD、AGTSVCSTARTUPTYPE|
|MSSQLServerOLAPService|ASSVCACCOUNT、ASSVCPASSWORD、ASSVCSTARTUPTYPE|
|ReportServer|RSSVCACCOUNT、RSSVCPASSWORD、RSSVCSTARTUPTYPE|
|[!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)]|ISSVCACCOUNT、ISSVCPASSWORD、ISSVCSTARTUPTYPE|
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 分散再生コントローラー|DRU_CTLR、CTLRSVCACCOUNT、CTLRSVCPASSWORD、CTLRSTARTUPTYPE、CTLRUSERS|
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 分散再生クライアント|DRU_CLT、CLTSVCACCOUNT、CLTSVCPASSWORD、CLTSTARTUPTYPE、CLTCTLRNAME、CLTWORKINGDIR、CLTRESULTDIR|
|R サービスまたは Machine Learning Services|EXTSVCACCOUNT、EXTSVCPASSWORD、ADVANCEDANALYTICS\*\*\*|
|PolyBase エンジン| PBENGSVCACCOUNT、PBENGSVCPASSWORD、PBENGSVCSTARTUPTYPE、PBDMSSVCACCOUNT、PBDMSSVCPASSWORD、PBDMSSVCSTARTUPTYPE、PBSCALEOUT、PBPORTRANGE

\* 自動インストールの詳細およびサンプル構文については、「[コマンド プロンプトからの SQL Server 2016 のインストール](../install-windows/install-sql-server-from-the-command-prompt.md)」をご覧ください。

\*\*[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エージェント サービスは、[!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] および [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] with Advanced Services のインスタンスで無効になっています。

\*\*\* スイッチだけによる Launchpad のアカウントの設定は、現在はサポートされていません。 アカウントおよび他のサービス設定を変更するには、SQL Server 構成マネージャーを使ってください。

### <a name="firewall-port"></a><a name="Firewall"></a> ファイアウォール ポート

ほとんどの場合、 [!INCLUDE[ssDE](../../includes/ssde-md.md)] は、最初にインストールされると、 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] と同じコンピューターにインストールされている [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]などのツールによって接続できます。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップでは、Windows ファイアウォールでポートが開かれません。 [!INCLUDE[ssDE](../../includes/ssde-md.md)] が TCP ポートでリッスンするように構成され、Windows ファイアウォールで接続用に適切なポートが開かれるまでは、他のコンピューターから接続できない場合があります。 詳細については、[SQL Server アクセスを許可するための Windows ファイアウォールの構成](../../sql-server/install/configure-the-windows-firewall-to-allow-sql-server-access.md)に関する記事を参照してください。

## <a name="service-permissions"></a><a name="Serv_Perm"></a> サービスの権限

このセクションでは、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] サービスのサービスごとの SID のために [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップで構成される権限について説明します。

- [サービスの構成とアクセス制御](#Serv_SID)
- [Windows の特権および権限](#Windows)
- [SQL Server のサービスごとの SID または SQL Server ローカル Windows グループに付与されるファイル システム権限](#Reviewing_ACLs)
- [他の Windows ユーザー アカウントまたはグループに付与されるファイル システム権限](#File_System_Other)
- [通常と異なるディスクの場所に関連するファイル システム権限](#Unusual_Locations)
- [その他の注意点の確認](#Review_additional_considerations)
- [レジストリの権限](#Registry)
- [WMI](#WMI)
- [名前付きパイプ](#Pipes)

### <a name="service-configuration-and-access-control"></a><a name="Serv_SID"></a> サービスの構成とアクセス制御

[!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] の場合、各サービスに対してサービスごとの SID を使用することができます。これによって、サービスを分離し、多層防御を実現できます。 サービスごとの SID は、サービス名から取得されるので、そのサービスに固有です。 たとえば、[!INCLUDE[ssDE](../../includes/ssde-md.md)] サービスの名前付きインスタンスのサービス SID 名は、**NT Service\MSSQL$** _\<InstanceName>_ となります。 サービスを分離すると、高い特権のアカウントを使用したり、オブジェクトのセキュリティ保護を弱めたりすることなく、特定のオブジェクトにアクセスできます。 サービス SID を含むアクセス制御エントリを使用することにより、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] サービスはそのリソースへのアクセスを制限することができます。

> [!NOTE]
> Windows 7 と [!INCLUDE[nextref_longhorn](../../includes/nextref-longhorn-md.md)] R2 以降では、サービスごとの SID は、サービスによって使用される仮想アカウントである場合があります。

ほとんどのコンポーネントでは、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] はサービス アカウントの ACL を直接構成します。したがって、リソース ACL プロセスを繰り返さなくてもサービス アカウントを変更することができます。

[!INCLUDE[ssAS](../../includes/ssas-md.md)]をインストールするときに、 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] サービスに対してサービスごとの SID が作成されます。 ローカル Windows グループは、 **SQLServerMSASUser$** _computer_name_ **$** _instance_name_ という形式の名前で作成されます。 サービスごとの SID **NT SERVICE\MSSQLServerOLAPService** には、ローカル Windows グループのメンバーシップが付与され、ローカル Windows グループには、ACL の適切な権限が付与されます。 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] サービスを開始するために使用されるアカウントが変更される場合は、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 構成マネージャーで一部の Windows 権限 (サービスとしてログオンする権限など) を変更する必要がありますが、サービスごとの SID は変更されていないので、ローカル Windows グループに割り当てられた権限は、更新することなく使用できます。 これにより、アップグレード中に [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] サービスの名前を変更できます。

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインストール中に、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップは、 [!INCLUDE[ssAS](../../includes/ssas-md.md)] のローカル Windows グループと [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Browser サービスを作成します。 これらのサービスに対して、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] はローカル Windows グループの ACL を構成します。

サービス構成に応じて、インストール時またはアップグレード時にサービスまたはサービス SID のサービス アカウントが、サービス グループのメンバーとして追加されます。

### <a name="windows-privileges-and-rights"></a><a name="Windows"></a> Windows の特権および権限

サービスを開始するために割り当てられているアカウントには、サービスを **開始、停止、および一時停止するための権限が必要です**。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップ プログラムはこれを自動的に割り当てます。 最初にリモート サーバー管理ツール (RSAT) をインストールします。 「[Windows 10 用のリモート サーバー管理ツール](https://www.microsoft.com/download/details.aspx?id=45520)」を参照してください。

次の表に、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] コンポーネントが使用するサービスごとの SID またはローカル Windows グループに対して [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップが要求する権限を示します。

|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] サービス|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップで付与される権限|
|---------------------------------------|------------------------------------------------------------|
|**[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]:**<br /><br /> (すべての権限が、サービスごとの SID に付与されます。 既定のインスタンス: **NT SERVICE\MSSQLSERVER**。 名前付きインスタンス: **NT Service\MSSQLServer$** _InstanceName_.)|**サービスとしてログオン** (SeServiceLogonRight)<br /><br /> **プロセス レベル トークンを置き換える** (SeAssignPrimaryTokenPrivilege)<br /><br /> **スキャン チェックを行わない** (SeChangeNotifyPrivilege)<br /><br /> **プロセスに対してメモリ クォータを調整する** (SeIncreaseQuotaPrivilege)<br /><br /> SQL ライターを起動する権限<br /><br /> イベント ログ サービスを読み取る権限<br /><br /> リモート プロシージャ コール サービスを読み取る権限|
|**[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エージェント:** \*<br /><br /> (すべての権限が、サービスごとの SID に付与されます。 既定のインスタンス: **NT Service\SQLSERVERAGENT**。 名前付きインスタンス: **NT Service\SQLAGENT$** _InstanceName_。)|**サービスとしてログオン** (SeServiceLogonRight)<br /><br /> **プロセス レベル トークンを置き換える** (SeAssignPrimaryTokenPrivilege)<br /><br /> **スキャン チェックを行わない** (SeChangeNotifyPrivilege)<br /><br /> **プロセスに対してメモリ クォータを調整する** (SeIncreaseQuotaPrivilege)|
|**[!INCLUDE[ssAS](../../includes/ssas-md.md)]:**<br /><br /> (すべての権限が、ローカル Windows グループに付与されます。 既定のインスタンス: **SQLServerMSASUser$** _ComputerName_ **$MSSQLSERVER**。 名前付きインスタンス: **SQLServerMSASUser$** _ComputerName_ **$** _InstanceName_。 [!INCLUDE[ssGeminiShort](../../includes/ssgeminishort-md.md)] インスタンス: **SQLServerMSASUser$** _ComputerName_ **$** _PowerPivot_。)|**サービスとしてログオン** (SeServiceLogonRight)<br /><br /> テーブルのみ:<br /><br /> **[プロセス ワーキング セットの増加]** (SeIncreaseWorkingSetPrivilege)<br /><br /> **プロセスに対してメモリ クォータを調整する** (SeIncreaseQuotaPrivilege)<br /><br /> **メモリ内のページをロックする** (SeLockMemoryPrivilege) - ページングが完全に無効になっている場合にのみ必要です。<br /><br /> フェールオーバー クラスター インストールのみ:<br /><br /> **スケジューリングでの優先度を上げる** (SeIncreaseBasePriorityPrivilege)|
|**[!INCLUDE[ssRS](../../includes/ssrs.md)]:**<br /><br /> (すべての権限が、サービスごとの SID に付与されます。 既定のインスタンス: **NT SERVICE\ReportServer**。 名前付きインスタンス: **NT SERVICE\\ReportServer$** _InstanceName_。)|**サービスとしてログオン** (SeServiceLogonRight)|
|**[!INCLUDE[ssIS](../../includes/ssis-md.md)]:**<br /><br /> (すべての権限が、サービスごとの SID に付与されます。 既定のインスタンスと名前付きインスタンス: **NT SERVICE\MsDtsServer130**。 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] には名前付きインスタンスに対する個別のプロセスはありません)|**サービスとしてログオン** (SeServiceLogonRight)<br /><br /> アプリケーション イベント ログに書き込む権限<br /><br /> **スキャン チェックを行わない** (SeChangeNotifyPrivilege)<br /><br /> **認証後にクライアントを借用する** (SeImpersonatePrivilege)|
|**フルテキスト検索:**<br /><br /> (すべての権限が、サービスごとの SID に付与されます。 既定のインスタンス: **NT Service\MSSQLFDLauncher**。 名前付きインスタンス: **NT Service\ MSSQLFDLauncher$** _InstanceName_。)|**サービスとしてログオン** (SeServiceLogonRight)<br /><br /> **プロセスに対してメモリ クォータを調整する** (SeIncreaseQuotaPrivilege)<br /><br /> **スキャン チェックを行わない** (SeChangeNotifyPrivilege)|
|**[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ブラウザー:**<br /><br /> (すべての権限が、ローカル Windows グループに付与されます。 既定のインスタンスまたは名前付きインスタンス: **SQLServer2005SQLBrowserUser** _$ComputerName_。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Browser には名前付きインスタンスに対する個別のプロセスはありません。)|**サービスとしてログオン** (SeServiceLogonRight)|
|**[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] VSS Writer:**<br /><br /> (すべての権限が、サービスごとの SID に付与されます。 既定のインスタンスまたは名前付きインスタンス: **NT Service\SQLWriter**。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] VSS Writer には名前付きインスタンスに対する個別のプロセスはありません。)|SQLWriter サービスは、必要なすべての権限を持つ LOCAL SYSTEM アカウントで実行されます。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップでは、このサービスのチェックまたは権限の付与は行いません。|
|**[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 分散再生コントローラー:**|**サービスとしてログオン** (SeServiceLogonRight)|
|**[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 分散再生クライアント:**|**サービスとしてログオン** (SeServiceLogonRight)|
|**PolyBase エンジンと DMS**| **サービスとしてログオン** (SeServiceLogonRight)|
|**スタート パッド:**|**サービスとしてログオン** (SeServiceLogonRight) <br /><br /> **プロセス レベル トークンを置き換える** (SeAssignPrimaryTokenPrivilege)<br /><br />**スキャン チェックを行わない** (SeChangeNotifyPrivilege)<br /><br />**プロセスに対してメモリ クォータを調整する** (SeIncreaseQuotaPrivilege)|
|**R サービス/Machine Learning Services:** **SQLRUserGroup** (SQL 2016 および 2017) |既定では、 **[ローカル ログオンを許可する]** アクセス許可がありません |
|**Machine Learning Services** '**すべてのアプリケーション パッケージ' [AppContainer]** (SQL 2019) |SQL Server 'Binn'、R_Services、および PYTHON_Services ディレクトリに対する **読み取り権限と実行権限** |

 \*[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エージェント サービスは、[!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] のインスタンスで無効になっています。

### <a name="file-system-permissions-granted-to-sql-server-per-service-sids-or-local-windows-groups"></a><a name="Reviewing_ACLs"></a> SQL Server のサービスごとの SID またはローカル Windows グループに付与されるファイル システム権限

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] サービス アカウントは、リソースへのアクセス権を持っている必要があります。 アクセス制御リストは、サービスごとの SID またはローカル Windows グループに対して設定されます。

> [!IMPORTANT]
> フェールオーバー クラスターのインストールの場合、共有ディスク上のリソースをローカル アカウントの ACL に設定する必要があります。

次の表に、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップによって設定される ACL を示します。

|サービス アカウントのサービス|ファイルとフォルダー|アクセス|
|-------------------------|-----------------------|------------|
|MSSQLServer|Instid\MSSQL\backup|フル コントロール|
||Instid\MSSQL\binn|読み取り、実行|
||Instid\MSSQL\data|フル コントロール|
||Instid\MSSQL\FTData|フル コントロール|
||Instid\MSSQL\Install|読み取り、実行|
||Instid\MSSQL\Log|フル コントロール|
||Instid\MSSQL\Repldata|フル コントロール|
||130\shared|読み取り、実行|
||Instid\MSSQL\Template Data ([!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] のみ)|Read|
|SQLServerAgent\*|Instid\MSSQL\binn|フル コントロール|
||Instid\MSSQL\Log|読み取り、書き込み、削除、実行|
||130\com|読み取り、実行|
||130\shared|読み取り、実行|
||130\shared\Errordumps|読み取り、書き込み|
||ServerName\EventLog|フル コントロール|
|FTS|Instid\MSSQL\FTData|フル コントロール|
||Instid\MSSQL\FTRef|読み取り、実行|
||130\shared|読み取り、実行|
||130\shared\Errordumps|読み取り、書き込み|
||Instid\MSSQL\Install|読み取り、実行|
||Instid\MSSQL\jobs|読み取り、書き込み|
|MSSQLServerOLAPService|130\shared\ASConfig|フル コントロール|
||Instid\OLAP|読み取り、実行|
||Instid\Olap\Data|フル コントロール|
||Instid\Olap\Log|読み取り、書き込み|
||Instid\OLAP\Backup|読み取り、書き込み|
||Instid\OLAP\Temp|読み取り、書き込み|
||130\shared\Errordumps|読み取り、書き込み|
|ReportServer|Instid\Reporting Services\Log Files|読み取り、書き込み、削除|
||Instid\Reporting Services\ReportServer|読み取り、実行|
||Instid\Reporting Services\ReportServer\global.asax|フル コントロール|
||Instid\Reporting Services\ReportServer\rsreportserver.config|Read|
||Instid\Reporting Services\RSTempfiles|読み取り、書き込み、実行、削除|
||Instid\Reporting Services\RSWebApp|読み取り、実行|
||130\shared|読み取り、実行|
||130\shared\Errordumps|読み取り、書き込み|
|MSDTSServer100|130\dts\binn\MsDtsSrvr.ini.xml|Read|
||130\dts\binn|読み取り、実行|
||130\shared|読み取り、実行|
||130\shared\Errordumps|読み取り、書き込み|
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ブラウザー|130\shared\ASConfig|Read|
||130\shared|読み取り、実行|
||130\shared\Errordumps|読み取り、書き込み|
|SQLWriter|N/A (ローカル システムとして実行)||
|User|Instid\MSSQL\binn|読み取り、実行|
||Instid\Reporting Services\ReportServer|読み取り、実行、フォルダー内容の一覧表示|
||Instid\Reporting Services\ReportServer\global.asax|Read|
||Instid\Reporting Services\RSWebApp|読み取り、実行、フォルダー内容の一覧表示|
||130\dts|読み取り、実行|
||130\tools|読み取り、実行|
||100\tools|読み取り、実行|
||90\tools|読み取り、実行|
||80\tools|読み取り、実行|
||130\sdk|Read|
||Microsoft SQL Server\130\Setup Bootstrap|読み取り、実行|
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 分散再生コントローラー|\<ToolsDir>DReplayController\Log\ (空のディレクトリ)|読み取り、実行、フォルダー内容の一覧表示|
||\<ToolsDir>\DReplayController\DReplayController.exe|読み取り、実行、フォルダー内容の一覧表示|
||\<ToolsDir>\DReplayController\resources\|読み取り、実行、フォルダー内容の一覧表示|
||\<ToolsDir>\DReplayController\\{すべての dll}|読み取り、実行、フォルダー内容の一覧表示|
||\<ToolsDir>\DReplayController\DReplayController.config|読み取り、実行、フォルダー内容の一覧表示|
||\<ToolsDir>\DReplayController\IRTemplate.tdf|読み取り、実行、フォルダー内容の一覧表示|
||\<ToolsDir>\DReplayController\IRDefinition.xml|読み取り、実行、フォルダー内容の一覧表示|
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 分散再生クライアント|\<ToolsDir>\DReplayClient\Log\|読み取り、実行、フォルダー内容の一覧表示|
||\<ToolsDir>\DReplayClient\DReplayClient.exe|読み取り、実行、フォルダー内容の一覧表示|
||\<ToolsDir>\DReplayClient\resources\|読み取り、実行、フォルダー内容の一覧表示|
||\<ToolsDir>\DReplayClient\ (すべての dll)|読み取り、実行、フォルダー内容の一覧表示|
||\<ToolsDir>\DReplayClient\DReplayClient.config|読み取り、実行、フォルダー内容の一覧表示|
||\<ToolsDir>\DReplayClient\IRTemplate.tdf|読み取り、実行、フォルダー内容の一覧表示|
||\<ToolsDir>\DReplayClient\IRDefinition.xml|読み取り、実行、フォルダー内容の一覧表示|
|スタート パッド|%binn|読み取り、実行|
||ExtensiblilityData|フル コントロール|
||Log\ExtensibilityLog|フル コントロール|

\*[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エージェント サービスは、[!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] および [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] with Advanced Services のインスタンスで無効になっています。

データベース ファイルをユーザー定義の場所に格納する場合は、サービスごとの SID にその場所へのアクセス権を付与する必要があります。 サービスごとの SID にファイル システム権限を付与する方法の詳細については、「 [データベース エンジン アクセスのファイル システム権限の構成](../../database-engine/configure-windows/configure-file-system-permissions-for-database-engine-access.md)」をご覧ください。

### <a name="file-system-permissions-granted-to-other-windows-user-accounts-or-groups"></a><a name="File_System_Other"></a> 他の Windows ユーザー アカウントまたはグループに付与されるファイル システム権限

ビルトイン アカウントや他の [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] サービス アカウントに、いくつかのアクセス制御権限を付与する必要がある場合があります。 次の表は、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップによって追加設定される ACL を示しています。

|要求元コンポーネント|Account|リソース|アクセス許可|
|--------------------------|-------------|--------------|-----------------|
|MSSQLServer|パフォーマンス ログ ユーザー|Instid\MSSQL\binn|フォルダー内容の一覧表示|
||パフォーマンス モニター ユーザー|Instid\MSSQL\binn|フォルダー内容の一覧表示|
||パフォーマンス ログ ユーザー、パフォーマンス監視ユーザー|\WINNT\system32\sqlctr130.dll|読み取り、実行|
||管理者のみ|\\\\.\root\Microsoft\SqlServer\ServerEvents\\<sql_instance_name>\*|フル コントロール|
||管理者、システム|\tools\binn\schemas\sqlserver\2004\07\showplan|フル コントロール|
||ユーザー|\tools\binn\schemas\sqlserver\2004\07\showplan|読み取り、実行|
|[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]|レポート サーバー Windows サービス アカウント|*\<install>* \Reporting Services\LogFiles|DELETE<br /><br /> READ_CONTROL<br /><br /> SYNCHRONIZE<br /><br /> FILE_GENERIC_READ<br /><br /> FILE_GENERIC_WRITE<br /><br /> FILE_READ_DATA<br /><br /> FILE_WRITE_DATA<br /><br /> FILE_APPEND_DATA<br /><br /> FILE_READ_EA<br /><br /> FILE_WRITE_EA<br /><br /> FILE_READ_ATTRIBUTES<br /><br /> FILE_WRITE_ATTRIBUTES|
||レポート サーバー Windows サービス アカウント|*\<install>* \Reporting Services\ReportServer|Read|
||レポート サーバー Windows サービス アカウント|*\<install>* \Reporting Services\ReportServer\global.asax|[完全]|
||レポート サーバー Windows サービス アカウント|*\<install>* Instid\Reporting Services\RSWebApp|読み取り、実行|
||Everyone|*\<install>* \Reporting Services\ReportServer\global.asax|READ_CONTROL<br /><br /> FILE_READ_DATA<br /><br /> FILE_READ_EA<br /><br /> FILE_READ_ATTRIBUTES|
||ReportServer Windows サービス アカウント|*\<install>* \Reporting Services\ReportServer\rsreportserver.config|DELETE<br /><br /> READ_CONTROL<br /><br /> SYNCHRONIZE<br /><br /> FILE_GENERIC_READ<br /><br /> FILE_GENERIC_WRITE<br /><br /> FILE_READ_DATA<br /><br /> FILE_WRITE_DATA<br /><br /> FILE_APPEND_DATA<br /><br /> FILE_READ_EA<br /><br /> FILE_WRITE_EA<br /><br /> FILE_READ_ATTRIBUTES<br /><br /> FILE_WRITE_ATTRIBUTES|
||Everyone|レポート サーバー キー (Instid ハイブ)|値のクエリ<br /><br /> サブキーの列挙<br /><br /> 通知<br /><br /> 読み取り制御|
||ターミナル サービス ユーザー|レポート サーバー キー (Instid ハイブ)|値のクエリ<br /><br /> 値の設定<br /><br /> サブキーの作成<br /><br /> サブキーの列挙<br /><br /> 通知<br /><br /> 削除<br /><br /> 読み取り制御|
||パワー ユーザー|レポート サーバー キー (Instid ハイブ)|値のクエリ<br /><br /> 値の設定<br /><br /> サブキーの作成<br /><br /> サブキーの列挙<br /><br /> 通知<br /><br /> 削除<br /><br /> 読み取り制御|

\* これは WMI プロバイダーの名前空間です。

### <a name="file-system-permissions-related-to-unusual-disk-locations"></a><a name="Unusual_Locations"></a> 通常と異なるディスクの場所に関連するファイル システム権限

インストールの場所の既定のドライブは **systemdrive** (通常はドライブ C) です。このセクションでは、tempdb またはユーザー データベースを通常とは異なる場所にインストールするときの追加の考慮事項について説明します。

**既定以外のドライブ**

既定のドライブではないローカル ドライブにインストールするときは、サービスごとの SID にそのファイルの場所へのアクセス権がある必要があります。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップで、必要なアクセスが準備されます。

**ネットワーク共有**

データベースをネットワーク共有にインストールする場合は、サービス アカウントにユーザー データベースおよび tempdb データベースのファイルの場所へのアクセス権が必要です。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップでは、ネットワーク共有のアクセスを準備することはできません。 セットアップを実行する前に、サービス アカウントの tempdb の場所へのアクセス権を準備する必要があります。 ユーザーは、データベースを作成する前にユーザー データベースの場所へのアクセス権を準備する必要があります。

> [!NOTE]
> 仮想アカウントでは、リモートの場所へアクセスすることはできません。 どの仮想アカウントも、コンピューター アカウントの権限を使用します。 _<domain_name>_ **\\** _<computer_name>_ **$** の形式でコンピューター アカウントを準備してください。

### <a name="reviewing-additional-considerations"></a><a name="Review_additional_considerations"></a> その他の注意点の確認

次の表は、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] サービスが追加の機能を提供するために必要な権限を示しています。

|サービスまたはアプリケーション|機能|必要な権限|
|--------------------------|-------------------|-------------------------|
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (MSSQLSERVER)|xp_sendmail を使用してメール スロットに書き込みます。|ネットワーク書き込み権限。|
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (MSSQLSERVER)|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 管理者以外のユーザーに xp_cmdshell を実行します。|オペレーティング システムの一部としての動作しプロセス レベル トークンを置き換える権限。|
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エージェント (MSSQLSERVER)|再起動の自動化機能を使用します。|Administrators ローカル グループのメンバーである必要があります。|
|[!INCLUDE[ssDE](../../includes/ssde-md.md)] チューニング アドバイザー|最適なクエリ パフォーマンスを得るようにデータベースをチューニングします。|初めて使用する場合は、システム管理者の資格情報を持つユーザーがアプリケーションを初期化する必要があります。 初期化後は、dbo ユーザーが [!INCLUDE[ssDE](../../includes/ssde-md.md)] チューニング アドバイザーを使用して所有するテーブルのみをチューニングできます。 詳細については、 [!INCLUDE[ssDE](../../includes/ssde-md.md)] オンライン ブックの「 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] チューニング アドバイザーの初期化」を参照してください。|

> [!IMPORTANT]
> [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] をアップグレードする前に、SQL Server エージェントを有効にし、必要な既定の構成を確認します。つまり、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エージェント サービス アカウントが [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] **sysadmin** 固定サーバー ロールのメンバーであることを確認します。

### <a name="registry-permissions"></a><a name="Registry"></a> レジストリの権限

インスタンス対応のコンポーネントの場合は、**HKLM\Software\Microsoft\Microsoft SQL Server\\** _<Instance_ID>_ の下にレジストリ ハイブが作成されます。 次に例を示します。

- **HKLM\Software\Microsoft\Microsoft SQL Server\MSSQL13.MyInstance**
- **HKLM\Software\Microsoft\Microsoft SQL Server\MSASSQL13.MyInstance**
- **HKLM\Software\Microsoft\Microsoft SQL Server\MSSQL.130**

このレジストリは、インスタンス ID とインスタンス名のマッピングも管理します。 インスタンス ID とインスタンス名のマッピングは次のようになります。

- **[HKEY_LOCAL_MACHINE\Software\Microsoft\Microsoft SQL Server\Instance Names\SQL] "InstanceName"="MSSQL13"**
- **[HKEY_LOCAL_MACHINE\Software\Microsoft\Microsoft SQL Server\Instance Names\OLAP] "InstanceName"="MSASSQL13"**
- **[HKEY_LOCAL_MACHINE\Software\Microsoft\Microsoft SQL Server\Instance Names\RS] "InstanceName"="MSRSSQL13"**

### <a name="wmi"></a><a name="WMI"></a> WMI

Windows Management Instrumentation (WMI) は、 [!INCLUDE[ssDE](../../includes/ssde-md.md)]に接続できる必要があります。 これをサポートするには、Windows WMI プロバイダーのサービスごとの SID (**NT SERVICE\winmgmt**) が [!INCLUDE[ssDE](../../includes/ssde-md.md)]で準備されている必要があります。

SQL WMI プロバイダーには、以下の最低限のアクセス許可が必要です。

- msdb データベースの **db_ddladmin** 固定データベース ロールまたは **db_owner** 固定データベース ロールのメンバーシップ。
- サーバーの **CREATE DDL EVENT NOTIFICATION** 権限。
- **[!INCLUDE[ssDE](../../includes/ssde-md.md)]** の CREATE TRACE EVENT NOTIFICATION 権限。
- **VIEW ANY DATABASE** サーバーレベル権限。

  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップでは、SQL の WMI 名前空間が作成され、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エージェント サービス SID に読み取り権限が付与されます。

### <a name="named-pipes"></a><a name="Pipes"></a> 名前付きパイプ

どのインストールでも、ローカルの名前付きパイプである共有メモリ プロトコルを通じて [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] へのアクセス権が [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] セットアップで提供されます

## <a name="provisioning"></a><a name="Provisioning"></a> プロビジョニング

ここでは、さまざまな [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] コンポーネント内でアカウントが準備されるしくみについて説明します。

- [データベース エンジンのプロビジョニング](#DE_Prov)

  - [Windows プリンシパル](#Win_Principals)
  - [sa アカウント](#sa)
  - [SQL Server のサービスごとの SID のログインと特権](#Logins)
  - [SQL Server エージェントのログインと特権](#Agent)
  - [HADRON および SQL フェールオーバー クラスター インスタンスと特権](#Hadron)
  - [SQL ライターと特権](#Writer)
  - [SQL WMI と特権](#SQLWMI)
- [SSAS プロビジョニング](#SSAS)
- [SSRS の準備](#SSRS)

### <a name="database-engine-provisioning"></a><a name="DE_Prov"></a> データベース エンジンのプロビジョニング

次のアカウントは、 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]のログインとして追加されます。

#### <a name="windows-principals"></a><a name="Win_Principals"></a> Windows プリンシパル

セットアップ中、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップでは、 **sysadmin** 固定サーバー ロールのメンバーとして、少なくとも 1 つのユーザー アカウントの名前を指定する必要があります。

#### <a name="sa-account"></a><a name="sa"></a> sa アカウント

**sa** アカウントは常に [!INCLUDE[ssDE](../../includes/ssde-md.md)] ログインとして存在し、 **sysadmin** 固定サーバー ロールのメンバーです。 [!INCLUDE[ssDE](../../includes/ssde-md.md)] が Windows 認証のみを使用してインストールされている ([!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 認証が有効でない) 場合は、**sa** ログインがまだ存在していますが、無効になっています。また、パスワードは複雑でランダムです。 **sa** アカウントの有効化の詳細については、「 [サーバーの認証モードの変更](../../database-engine/configure-windows/change-server-authentication-mode.md)」を参照してください。

#### <a name="sql-server-per-service-sid-login-and-privileges"></a><a name="Logins"></a> SQL Server のサービスごとの SID のログインと特権

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] サービスのサービスごとの SID (サービス セキュリティ プリンシパル (SID) とも呼ばれます) は、[!INCLUDE[ssDE](../../includes/ssde-md.md)] ログインとしてプロビジョニングされます。 サービスごとの SID ログインは、 **sysadmin** 固定サーバー ロールのメンバーです。 サービスごとの SID の詳細については、「[サービスの SID を使用して SQL Server のサービスにアクセス許可を付与する](../../relational-databases/security/using-service-sids-to-grant-permissions-to-services-in-sql-server.md)」を参照してください。

#### <a name="sql-server-agent-login-and-privileges"></a><a name="Agent"></a> SQL Server エージェントのログインと特権

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エージェントのサービスごとの SID は、 [!INCLUDE[ssDE](../../includes/ssde-md.md)] ログインとして準備されます。 サービスごとの SID ログインは、 **sysadmin** 固定サーバー ロールのメンバーです。

#### <a name="sshadrc-and-sql-failover-cluster-instance-and-privileges"></a><a name="Hadron"></a> [!INCLUDE[ssHADRc](../../includes/sshadrc-md.md)] および SQL フェールオーバー クラスター インスタンスと特権

[!INCLUDE[ssDE](../../includes/ssde-md.md)] を [!INCLUDE[ssHADR](../../includes/sshadr-md.md)] または SQL フェールオーバー クラスター インスタンス (SQL FCI) としてインストールすると、 **LOCAL SYSTEM** が [!INCLUDE[ssDE](../../includes/ssde-md.md)]に準備されます。 **LOCAL SYSTEM** ログインには、 **ALTER ANY AVAILABILITY GROUP** 権限 ( [!INCLUDE[ssHADR](../../includes/sshadr-md.md)]に対して) および **VIEW SERVER STATE** 権限 (SQL FCI に対して) が付与されます。

#### <a name="sql-writer-and-privileges"></a><a name="Writer"></a> SQL ライターと特権

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] VSS Writer サービスのサービスごとの SID は、 [!INCLUDE[ssDE](../../includes/ssde-md.md)] ログインとして準備されます。 サービスごとの SID ログインは、 **sysadmin** 固定サーバー ロールのメンバーです。

#### <a name="sql-wmi-and-privileges"></a><a name="SQLWMI"></a> SQL WMI と特権

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップでは、 **NT SERVICE\Winmgmt** アカウントが [!INCLUDE[ssDE](../../includes/ssde-md.md)] ログインとして準備され、 **sysadmin** 固定サーバー ロールに追加されます。

#### <a name="ssrs-provisioning"></a>SSRS の準備

セットアップ中に指定されたアカウントは、 **RSExecRole** データベース ロールのメンバーとして準備されます。 詳細については、 [レポート サーバー サービス アカウントの構成 &#40;SSRS 構成マネージャー&#41;](../../reporting-services/install-windows/configure-the-report-server-service-account-ssrs-configuration-manager.md)」を参照してください。

### <a name="ssas-provisioning"></a><a name="SSAS"></a> SSAS の準備

[!INCLUDE[ssAS](../../includes/ssas-md.md)] サービス アカウント要件は、サーバーの配置方法によって異なります。 [!INCLUDE[ssGeminiShort](../../includes/ssgeminishort-md.md)]をインストールする場合、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップでは、 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] サービスをドメイン アカウントで実行するよう構成する必要があります。 ドメイン アカウントは、SharePoint に組み込まれている管理アカウント機能をサポートするために必要です。 このため、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップでは、 [!INCLUDE[ssGeminiShort](../../includes/ssgeminishort-md.md)] のインストールに仮想アカウントなどの既定のサービス アカウントは提供されません。 [!INCLUDE[ssGemini](../../includes/ssgemini-md.md)] for SharePoint のプロビジョニングの詳細については、「 [Power Pivot サービス アカウントの構成](/analysis-services/power-pivot-sharepoint/configure-power-pivot-service-accounts)」をご覧ください。

他のすべてのスタンドアロン [!INCLUDE[ssAS](../../includes/ssas-md.md)] インストールでは、サービスを準備してドメイン アカウント、ビルトイン システム アカウント、管理アカウント、または仮想アカウントで実行することができます。 アカウント プロビジョニングの詳細については、「[サービス アカウントの構成 &#40;Analysis Services&#41;](/analysis-services/instances/configure-service-accounts-analysis-services)」を参照してください。

クラスター化インストールでは、ドメイン アカウントまたはビルトイン システム アカウントを指定する必要があります。 [!INCLUDE[ssAS](../../includes/ssas-md.md)] フェールオーバー クラスターでは、管理アカウントと仮想アカウントはサポートされていません。

どの [!INCLUDE[ssAS](../../includes/ssas-md.md)] インストールでも、 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] インスタンスのシステム管理者を指定する必要があります。 管理者特権は、Analysis Services の **サーバー** ロールで準備されます。

### <a name="ssrs-provisioning"></a><a name="SSRS"></a> SSRS の準備

セットアップ中に指定されたアカウントは、 **RSExecRole** データベース ロールのメンバーとして [!INCLUDE[ssDE](../../includes/ssde-md.md)] で準備されます。 詳細については、 [レポート サーバー サービス アカウントの構成 &#40;SSRS 構成マネージャー&#41;](../../reporting-services/install-windows/configure-the-report-server-service-account-ssrs-configuration-manager.md)」を参照してください。

## <a name="upgrading-from-previous-versions"></a><a name="Upgrade"></a> 以前のバージョンからアップグレード

ここでは、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]の以前のバージョンからのアップグレード中に行われる変更について説明します。

- [!INCLUDE [sssql19-md](../../includes/sssql19-md.md)] には、サポートされている[オペレーティング システム](../../sql-server/install/hardware-and-software-requirements-for-installing-sql-server-ver15.md#operating-system-support)が必要になります。 下位バージョンのオペレーティング システムで実行されている、以前のバージョンの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]をアップグレードする前にオペレーティング システムをアップグレードする必要があります。
- [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] を [!INCLUDE [sssql19-md](../../includes/sssql19-md.md)] にアップグレードしているとき、セットアップによって [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンスが次のように構成されます。

  - [!INCLUDE[ssDE](../../includes/ssde-md.md)] は、サービスごとの SID のセキュリティ コンテキストで実行されます。 サービスごとの SID には、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンス (DATA など) のファイル フォルダーまたは [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] レジストリ キーへのアクセス権が付与されます。
  - [!INCLUDE[ssDE](../../includes/ssde-md.md)] のサービスごとの SID は、 **sysadmin** 固定サーバー ロールのメンバーとして [!INCLUDE[ssDE](../../includes/ssde-md.md)] で準備されます。
  - サービスごとの SID は、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] がフェールオーバー クラスター インスタンスでない場合、ローカルの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Windows グループに追加されます。
  - [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] リソースは、ローカルの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Windows グループに準備されたままになります。
  - サービスのローカル Windows グループの名前が **SQLServer2005MSSQLUser$** _<computer_name>_ **$** _<instance_name>_ から **SQLServerMSSQLUser$** _<computer_name>_ **$** _<instance_name>_ に変更されます。 移行されたデータベースのファイルの場所には、ローカル Windows グループにアクセス制御エントリ (ACE) が設定されます。 新しいデータベースのファイルの場所には、サービスごとの SID に ACE が設定されます。

- [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] からのアップグレード中、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップでは [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] のサービスごとの SID の ACE が保持されます。
- [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] フェールオーバー クラスター インスタンスでは、サービスに構成されたドメイン アカウントの ACE が保持されます。

## <a name="appendix"></a><a name="Appendix"></a> 付録

ここでは、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] サービスの追加情報について説明します。

- [サービス アカウントの説明](#Serv_Accts)
- [インスタンス対応のサービスとインスタンス非対応のサービスの指定](#Identify_instance_aware_and_unaware)
- [ローカライズされたサービス名](#Localized_service_names)

### <a name="description-of-service-accounts"></a><a name="Serv_Accts"></a> サービス アカウントの説明

サービス アカウントは、 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]などの Windows サービスを開始するために使用されるアカウントです。 SQL Server を実行している場合、サービス SID は常に存在し、**sysamin** 固定サーバー ロールのメンバーなので、それに加えて、SQL Server へのログインとしてサービス アカウントを追加する必要はありません。

#### <a name="accounts-available-with-any-operating-system"></a><a name="Any_OS"></a> 任意のオペレーティング システムで利用可能なアカウント

[MSA](#MSA)、[gMSA](#GMSA)、[仮想アカウント](#VA_Desc)に加えて、次のアカウントを使用することができます。

<a name="Domain_User"></a> **ドメイン ユーザー アカウント**

サービスでネットワーク サービスを操作する必要がある場合は、ファイル共有と同様の方法でドメイン リソースにアクセスします。また、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]を実行している他のコンピューターへのリンク サーバー接続をサービスで使用している場合は、最小限の特権しかないドメイン アカウントを使用できます。 多くのサーバー間操作は、ドメイン ユーザー アカウントを使用しなければ実行できません。 このアカウントは、使用している環境のドメイン管理によって事前に作成されている必要があります。

> [!NOTE]
> SQL Server を構成してドメイン アカウントを使用する場合は、サービスの特権を分離することができますが、パスワードを手動で管理するか、これらのパスワードを管理するためのカスタム ソリューションを作成する必要があります。 多くのサーバー アプリケーションがこの方法を使用してセキュリティを強化していますが、この方法では管理対象が多くなり、より複雑になります。 このような配置では、サービス管理者は、Kerberos 認証で必要なサービス パスワードやサービス プリンシパル名 (SPN) の管理などのメンテナンス タスクに相当な時間を費やすことになります。 さらに、これらのメンテナンス タスクによってサービスが中断されることがあります。

<a name="Local_User"></a> **ローカル ユーザー アカウント**

コンピューターがドメインに属していない場合は、Windows 管理者権限のないローカル ユーザー アカウントを使用することをお勧めします。

<a name="Local_Service"></a> **ローカル サービス アカウント**

ローカル サービス アカウントは、リソースおよびオブジェクトへのアクセスについて Users グループのメンバーと同じレベルの権限を持つビルトイン アカウントです。 このアクセス制限により、個々のサービスまたはプロセスに障害が発生した場合にシステムを保護することができます。 ローカル サービス アカウントとして実行されているサービスは、資格情報を使用せずに NULL セッションとしてネットワーク リソースにアクセスします。 
> [!NOTE]
> ローカル サービス アカウントは [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] サービスまたは [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エージェント サービスによってサポートされていません。 ローカル サービスは、これらのサービスを実行するアカウントとしてサポートされていません。ローカル サービスは共有サービスであり、ローカル サービス下で実行中のその他のサービスが [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]に対してシステム管理者のアクセス権を持つためです。
アカウントの実際の名前は、 **NT AUTHORITY\LOCAL SERVICE** です。

<a name="Network_Service"></a> **ネットワーク サービス アカウント**

ネットワーク サービス アカウントは、リソースおよびオブジェクトへのアクセスについて Users グループのメンバーより多くの権限を持つビルトイン アカウントです。 ネットワーク サービス アカウントとして実行されるサービスは、 _<domain_name>_ **\\** _<computer_name>_ **$** の形式で、コンピューター アカウントの資格情報を使用してネットワーク リソースにアクセスします。 アカウントの実際の名前は **NT AUTHORITY\NETWORK SERVICE** です。

<a name="Local_System"></a> **ローカル システム アカウント**

ローカル システムは非常に高い特権を持つビルトイン アカウントです。 ローカル システム上で広範囲の特権を持ち、ネットワーク上のコンピューターとして機能します。 アカウントの実際の名前は **NT AUTHORITY\SYSTEM** です。

### <a name="identifying-instance-aware-and-instance-unaware-services"></a><a name="Identify_instance_aware_and_unaware"></a> インスタンス対応のサービスとインスタンス非対応のサービスの指定

インスタンス対応のサービスとは、特定の [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]インスタンスに関連付けられているサービスを指し、それぞれに独自のレジストリ ハイブがあります。 コンポーネントやサービスごとに [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップを実行すると、インスタンス対応サービスの複数のコピーをインストールできます。 インスタンス非対応サービスは、インストールされているすべての [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンスで共有されます。 インスタンス非対応サービスは、特定のインスタンスに関連付けられておらず、インストールできるのは一度のみであり、サイド バイ サイドでのインストールは行えません。

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、インスタンスに対応するサービスに次のようなものがあります。

- [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]
- [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エージェント

   [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エージェント サービスは、 [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] および [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] with Advanced Services のインスタンスで無効になっていることに注意してください。

- [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]\*
- [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]
- フルテキスト検索

  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、インスタンスに対応しないサービスに次のようなものがあります。

- [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)]
- [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ブラウザー
- SQL ライター

 \* SharePoint 統合モードでの Analysis Services は、単一の名前付きインスタンスである "[!INCLUDE[ssGemini](../../includes/ssgemini-md.md)]" として実行されます。 インスタンス名は固定です。 別の名前を指定することはできません。 "[!INCLUDE[ssGemini](../../includes/ssgemini-md.md)]" として実行される Analysis Services のインスタンスは、物理サーバーごとに 1 つだけインストールできます。

### <a name="localized-service-names"></a><a name="Localized_service_names"></a> ローカライズされたサービス名

次の表は、Windows のローカライズ版で表示されるサービス名を示しています。

|Language|ローカル サービスの名前|ネットワーク サービスの名前|ローカル システムの名前|admin グループの名前|
|--------------|----------------------------|------------------------------|---------------------------|--------------------------|
|英語<br /><br /> 簡体中国語<br /><br /> Traditional Chinese<br /><br /> 韓国語<br /><br /> 日本語|NT AUTHORITY\LOCAL SERVICE|NT AUTHORITY\NETWORK SERVICE|NT AUTHORITY\SYSTEM|BUILTIN\Administrators (BUILTIN\Administrators)|
|ドイツ語|NT-AUTORIT&#xC4;T\LOKALER DIENST|NT-AUTORIT&#xC4;T\NETZWERKDIENST|NT-AUTORIT&#xC4;T\SYSTEM|VORDEFINIERT\Administratoren|
|フランス語|AUTORITE NT\SERVICE LOCAL|AUTORITE NT\SERVICE RÉSEAU|AUTORITE NT\SYSTEM|BUILTIN\Administrators (BUILTIN\Administrators)|
|イタリア語|NT AUTHORITY\SERVIZIO LOCALE|NT AUTHORITY\SERVIZIO DI RETE|NT AUTHORITY\SYSTEM|BUILTIN\Administrators (BUILTIN\Administrators)|
|スペイン語|NT AUTHORITY\SERVICIO LOC|NT AUTHORITY\SERVICIO DE RED|NT AUTHORITY\SYSTEM|BUILTIN\Administradores|
|ロシア語|NT AUTHORITY\LOCAL SERVICE|NT AUTHORITY\NETWORK SERVICE|NT AUTHORITY\СИСТЕМА|BUILTIN\Администраторы|

## <a name="related-content"></a>関連コンテンツ

[SQL Server インストールにおけるセキュリティの考慮事項](../../sql-server/install/security-considerations-for-a-sql-server-installation.md)

[SQL Server の既定のインスタンスおよび名前付きインスタンスのファイルの場所](../../sql-server/install/file-locations-for-default-and-named-instances-of-sql-server.md)

[マスター データ サービスのインストール](../../master-data-services/install-windows/install-master-data-services.md)
