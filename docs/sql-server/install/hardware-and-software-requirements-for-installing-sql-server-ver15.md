---
title: SQL Server 2019:ハードウェアとソフトウェアの要件
description: SQL Server 2019 をインストールして実行するためのハードウェア、ソフトウェア、およびオペレーティング システムの要件の一覧。
ms.custom: sqlfreshmay19
ms.date: 09/01/2020
ms.prod: sql
ms.reviewer: ''
ms.technology: release-landing
ms.topic: conceptual
helpviewer_keywords:
- Setup [SQL Server], software
- software [SQL Server]
- installing SQL Server, software
- operating systems [SQL Server], SQL Server requirements
- Setup [SQL Server], cross-language support
- operating systems [SQL Server], cross-language support
- network connections [SQL Server], requirements
- disk space [SQL Server], SQL Server installations
- WOW [SQL Server]
- Setup [SQL Server], hardware
- dependencies [SQL Server], SQL Server installations
- cluster hardware requirements [SQL Server]
- endpoints [SQL Server], SQL Server installations
- Internet [SQL Server], SQL Server installations
- hardware [SQL Server]
- Windows on Windows [SQL Server]
- installing SQL Server, hardware
- Setup Configuration Checker
- SCC [SQL Server]
- operating systems [SQL Server]
- space [SQL Server], SQL Server installations
- system configuration checker
- installing SQL Server, cross-language support
- Internet [SQL Server]
- space [SQL Server]
- extended system support [SQL Server]
- 64-bit edition [SQL Server]
- failover clustering [SQL Server]
- failover clustering [SQL Server], hardware requirements
- 32-bit edition [SQL Server]
- locales [SQL Server], SQL Server installations
- cross-language support
- disk space [SQL Server]
- localized SQL Server versions
ms.assetid: 09bcf20b-0a40-4131-907f-b61479d5e4d8
ms.author: chadam
author: cawrites
ms.openlocfilehash: b7def7a7f58d3fe2124545171c5858a012e4642b
ms.sourcegitcommit: 00af0b6448ba58e3685530f40bc622453d3545ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104673986"
---
# <a name="sql-server-2019-hardware-and-software-requirements"></a>SQL Server 2019:ハードウェアとソフトウェアの要件
[!INCLUDE [SQL Server Windows Only - ASDBMI ](../../includes/applies-to-version/sql-windows-only-asdbmi.md)]

この記事では、Windows オペレーティング システムでの [!INCLUDE [sssql19-md](../../includes/sssql19-md.md)] のインストールと実行に必要な最低限のハードウェア要件とソフトウェア要件について説明します。

他のバージョンの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のハードウェア要件とソフトウェア要件については、以下を参照してください。
- [[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 2016 および 2017](hardware-and-software-requirements-for-installing-sql-server.md)
- [Linux の [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]](../../linux/sql-server-linux-setup.md#system)
- [ビッグ データ クラスター](../../big-data-cluster/deployment-guidance.md)

##  <a name="hardware-requirements"></a><a name="pmosr"></a> ハードウェア要件  
 次に示すメモリおよびプロセッサの要件は [!INCLUDE[ssCurrent](../../includes/ssnoversion-md.md)]のすべてのエディションに適用されます。  
  
|コンポーネント|要件|  
|---------------|-----------------|  
|ハード ディスク|[!INCLUDE[ssCurrent](../../includes/ssnoversion-md.md)] では 6 GB 以上のハード ディスク空き容量が必要です。<br/><br/> 必要となるディスク空き容量は、インストールする [!INCLUDE[ssCurrent](../../includes/ssnoversion-md.md)] のコンポーネントに応じて異なります。 詳細については、この記事で後述する「[必要なハード ディスク空き容量](../../sql-server/install/hardware-and-software-requirements-for-installing-sql-server.md#HardDiskSpace)」を参照してください。 データ ファイルでサポートされているストレージの種類の詳細については、「 [Storage Types for Data Files](../../sql-server/install/hardware-and-software-requirements-for-installing-sql-server.md#StorageTypes)」を参照してください。|  
|モニター|[!INCLUDE[ssCurrent](../../includes/ssnoversion-md.md)] では Super-VGA (800x600) 以上の解像度のモニターが必要です。|  
|インターネット|インターネット機能にはインターネット アクセス (有料) が必要です。|  
|メモリ \*|**最小:**<br/><br/> Express Edition: 512 MB<br/><br/> 他のすべてのエディション: 1 GB<br/><br/> **推奨:**<br/><br/> Express Edition: 1 GB<br/><br/> 他のすべてのエディション: 4 GB 以上。最適なパフォーマンスを確保するために、データベースのサイズが大きくなるにつれて増やす必要があります。|  
|プロセッサの速度|**最小:** x64 プロセッサ:1.4 GHz<br/><br/> **推奨:** 2.0 GHz 以上|  
|プロセッサの種類|x64 プロセッサ: AMD Opteron、AMD Athlon 64、Intel Xeon (Intel EM64T 対応)、Intel Pentium IV (EM64T 対応)|  
  
> [!NOTE]  
> [!INCLUDE[ssCurrent](../../includes/ssnoversion-md.md)] は x64 プロセッサでのみイントールできます。 X86 プロセッサではインストールできません。  
  
 \*[!INCLUDE[ssDQSnoversion](../../includes/ssdqsnoversion-md.md)] (DQS) の [!INCLUDE[ssDQSServer](../../includes/ssdqsserver-md.md)] コンポーネントをインストールする場合の最小メモリ要件は 2 GB の RAM で、[!INCLUDE[ssCurrent](../../includes/ssnoversion-md.md)] の最小メモリ要件とは異なります。 DQS のインストールの詳細については、「 [Install Data Quality Services](../../data-quality-services/install-windows/install-data-quality-services.md)」を参照してください。  


##  <a name="software-requirements"></a><a name="hwswr"></a> ソフトウェア要件  

次の要件は、すべてのインストールに適用されます。  
  
|コンポーネント|要件|  
|---------------|-----------------|  
|オペレーティング システム|Windows 10 TH1 1507 以降<br/><br>Windows Server 2016 以降<br/><br/>
|.NET Framework|最小限のオペレーティング システムには、最小限の .NET Framework が含まれています。|  
|ネットワーク ソフトウェア|[!INCLUDE[ssCurrent](../../includes/ssnoversion-md.md)] でサポートされるオペレーティング システムにはネットワーク ソフトウェアが組み込まれています。 スタンドアロン インストールの名前付きおよび既定のインスタンスでは、次のネットワーク プロトコルがサポートされています。共有メモリ、名前付きパイプ、および TCP/IP。<br/><br/> |  

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップを実行すると、製品に必要な以下のソフトウェア コンポーネントがインストールされます。  
  
   - [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client    
   - [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップ サポート ファイル  


> [!IMPORTANT]
> PolyBase 機能には、ハードウェアとソフトウェアに追加の要件があります。 詳細については、「 [PolyBase 入門](../../relational-databases/polybase/polybase-guide.md)」を参照してください。  
  

## <a name="operating-system-support"></a>オペレーティング システムのサポート

次の表では、Windows のバージョンと [!INCLUDE [sssql19-md](../../includes/sssql19-md.md)] のエディションとの互換性を示します。  
  

| [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エディション:               | Enterprise | Developer | Standard | Web | Express |  
| :------------------------         | :--------- | :-------- | :------- | :-- | :------ | 
| Windows Server 2019 Datacenter    |    はい     |    はい    |    はい   | はい |   はい   |
| Windows Server 2019 Standard      |    はい     |    はい    |    はい   | はい |   はい   |
| Windows Server 2019 Essentials    |    はい     |    はい    |    はい   | はい |   はい   |
| Windows Server 2016 Datacenter    |    はい     |    はい    |    はい   | はい |   はい   |
| Windows Server 2016 Standard      |    はい     |    はい    |    はい   | はい |   はい   |
| Windows Server 2016 Essentials    |    はい     |    はい    |    はい   | はい |   はい   |
| Windows 10 IoT Enterprise         |    いいえ      |    はい    |    はい   | いいえ  |   はい   |
| Windows 10 Enterprise             |    いいえ      |    はい    |    はい   | いいえ  |   はい   |
| Windows 10 Professional           |    いいえ      |    はい    |    はい   | いいえ  |   はい   |
| Windows 10 Home                   |    いいえ      |    はい    |    はい   | いいえ  |   はい   |
| &nbsp; | &nbsp; |

### <a name="server-core-support"></a>Server Core サポート

[!INCLUDE [sssql19-md](../../includes/sssql19-md.md)] のインストールは、次のエディションの Windows Server の Server Core モードでサポートされます。

:::row:::
    :::column:::
        Windows Server 2019 Core
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        Windows Server 2016 Core
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
    :::column-end:::
:::row-end:::

Server Core への [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインストールの詳細については、「[Server Core への [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインストール](../../database-engine/install-windows/install-sql-server-on-server-core.md)」を参照してください。 


##  <a name="cross-language-support"></a><a name="CrossLanguageSupport"></a> 言語間サポート  
 各言語にローカライズされた [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] をインストールする場合の言語間サポートと注意点の詳細については、「[[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のローカル言語版](../../sql-server/install/local-language-versions-in-sql-server.md)」を参照してください。  
  
##  <a name="disk-space-requirements"></a><a name="HardDiskSpace"></a> 必要なディスク領域  
 [!INCLUDE[ssCurrent](../../includes/ssnoversion-md.md)]のインストール中は、Windows インストーラーによってシステム ドライブ上に一時ファイルが作成されます。 したがって、セットアップを実行して [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]をインストールまたはアップグレードする前に、これらの一時ファイル用に 6.0 GB 以上の空き容量がシステム ドライブにあることを確認してください。 この要件は、既定以外のドライブに [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] コンポーネントをインストールする場合にも適用されます。  
  
 実際に必要なハード ディスク空き容量は、システム構成と、インストールする機能によって異なります。 次の表は、 [!INCLUDE[ssCurrent](../../includes/ssnoversion-md.md)] の各コンポーネントに必要なディスク空き容量を示しています。  
  
|**機能**|**必要なディスク空き容量**|  
|-----------------|--------------------------------|  
|[!INCLUDE[ssDE](../../includes/ssde-md.md)] とデータ ファイル、レプリケーション、フルテキスト検索、および Data Quality Services|1480 MB|  
|[!INCLUDE[ssDE](../../includes/ssde-md.md)] (上記と同じ) と R Services (データベース内)|2744 MB|  
|[!INCLUDE[ssDE](../../includes/ssde-md.md)] (上記と同じ) と外部データ用 PolyBase クエリ サービス|4194 MB|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] およびデータ ファイル|698 MB|  
|[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]|967 MB|  
|[!INCLUDE[rsql_platform](../../includes/rsql-platform-md.md)] (スタンドアロン)|280 MB|  
|[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] - SharePoint|1203 MB|  
|[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] SharePoint 製品用アドイン|325 MB|  
|[!INCLUDE[ssDQSClient](../../includes/ssdqsclient-md.md)]|121 MB|  
|クライアント ツール接続|328 MB|  
|[!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)]|306 MB|  
|クライアント コンポーネント ( [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] オンライン ブック コンポーネントと Integration Services ツール以外)|445 MB|  
|[!INCLUDE[ssMDSshort](../../includes/ssmdsshort-md.md)]|280 MB|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] オンライン ブック コンポーネント (ヘルプ コンテンツを表示し、管理する)*|27 MB|  
|すべての機能|8030 MB|  
  
 *ダウンロードされるオンライン ブック コンテンツに必要なディスク領域は 200 MB です。  
  
##  <a name="storage-types-for-data-files"></a><a name="StorageTypes"></a> データ ファイルのストレージの種類  
 データ ファイルでサポートされているストレージの種類は、次のとおりです。  
  
- ローカル ディスク 
    - [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] は現在、標準のネイティブ セクター サイズである 512 バイトと 4 KB のディスク ドライブに対応しています。  ハード ディスクのセクター サイズが 4 KB を超える場合、それに [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] データ ファイルを格納しようとするとエラーが発生することがあります。  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] でサポートされているハード ディスク セクターのサイズに関する詳細については、[[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] でのハード ディスク ドライブ セクターのサイズのサポート範囲](https://support.microsoft.com/kb/926930)に関するページを参照してください。 
    - [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] フェールオーバー クラスターのインストールでは、tempdb ファイルをインストールする場合のみローカル ディスクがサポートされます。 tempdb のデータ ファイルおよびログ ファイルに指定されたパスが、すべてのクラスター ノードで有効であることを確認してください。 フェールオーバー中に、tempdb のディレクトリがフェールオーバーのターゲット ノード上で利用できない場合、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] リソースはオンラインへの移行に失敗します。
- ストレージの共有  
- [記憶域スペース ダイレクト \(S2D\)](/windows-server/storage/storage-spaces/storage-spaces-direct-overview)  
- SMB ファイル共有  
    - スタンドアロン インストールやクラスター化されたインストールでは、 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] データ ファイルに対して SMB ストレージはサポートされていません。 代わりに、直接アタッチされたストレージ、ストレージ エリア ネットワーク、または S2D を使用してください。 
    - SMB ストレージは、Windows ファイル サーバーまたはサード パーティ SMB ストレージ デバイスによってホストされます。 Windows ファイル サーバーが使用される場合、Windows ファイル サーバーのバージョンは 2008 以降である必要があります。 ストレージ オプションとして SMB ファイル共有をとして使用する [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインストールの詳細については、[ストレージ オプションとして SMB ファイル共有ストレージを使用して [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] をインストールする](../../database-engine/install-windows/install-sql-server-with-smb-fileshare-as-a-storage-option.md)方法に関するページを参照してください。  
  
  
  
##  <a name="installing-ssnoversion-on-a-domain-controller"></a><a name="DC_support"></a> ドメイン コントローラーへの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインストール  
 セキュリティ上の理由から、ドメイン コントローラーには [!INCLUDE[ssCurrent](../../includes/ssnoversion-md.md)] をインストールしないことをお勧めします。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のセットアップ時にインストールが中止されることはありませんが、次の制限事項が適用されます。  
  
- ローカル サービス アカウントを使用して、ドメイン コントローラー上で [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] サービスを実行することはできません。    
- コンピューターに [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] をインストールした後で、そのコンピューターをドメイン メンバーからドメイン コントローラーに変更することはできません。 ホスト コンピューターをドメイン コントローラーに変更する前に、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] をアンインストールする必要があります。    
- コンピューターに [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] をインストールした後で、そのコンピューターをドメイン コントローラーからドメイン メンバーに変更することはできません。 ホスト コンピューターをドメイン メンバーに変更する前に、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] をアンインストールする必要があります。   
- [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] フェールオーバー クラスター インスタンスは、クラスター ノードがドメイン コントローラーの場合はサポートされません。   
- [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] は、読み取り専用ドメイン コントローラーではサポートされません。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップでは、読み取り専用ドメイン コントローラーにセキュリティ グループを作成したり [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] サービス アカウントを準備したりすることはできません。 この場合、セットアップは失敗します。 
- [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] フェールオーバー クラスター インスタンスは、読み取り専用ドメイン コントローラーのみにアクセスできる環境ではサポートされません。 
  
## <a name="installation-media"></a>インストール メディア

関連するインストール メディアは、次の場所から入手できます。 
  
- [[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 評価センター](https://www.microsoft.com/evalcenter/evaluate-sql-server-2019)
- [最新の累積的な更新プログラム](../../database-engine/install-windows/latest-updates-for-microsoft-sql-server.md)

または、[[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] を既に実行している Azure 仮想マシン](/azure/virtual-machines/windows/sql/quickstart-sql-vm-create-portal)を作成することもできます。ただし、仮想マシン上の [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] は、仮想化のオーバーヘッドのためにネイティブで実行するよりも低速になります。


## <a name="next-steps"></a>次のステップ

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインストールのためのハードウェアとソフトウェアの要件を確認したら、[[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインストール計画](../../sql-server/install/planning-a-sql-server-installation.md)を開始したり、[[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のセキュリティに関する考慮事項](../../sql-server/install/security-considerations-for-a-sql-server-installation.md)を確認したりできます。