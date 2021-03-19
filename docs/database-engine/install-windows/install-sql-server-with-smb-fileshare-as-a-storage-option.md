---
title: SMB ファイル共有ストレージを使用して SQL Server をインストールする | Microsoft Docs
description: SQL Server では、ストレージ オプションとしてサーバー メッセージ ブロック (SMB) ファイル サーバーを指定して、システム データベースとデータベース エンジンのユーザー データベースをインストールできます。
ms.custom: ''
ms.date: 09/05/2017
ms.prod: sql
ms.reviewer: ''
ms.technology: install
ms.topic: conceptual
ms.assetid: 8b7810b2-637e-46a3-9fe1-d055898ba639
author: cawrites
ms.author: chadam
monikerRange: '>=sql-server-2016'
ms.openlocfilehash: 5f7bbccc12c8d3f522703421542ba6188e826ea1
ms.sourcegitcommit: bf7577b3448b7cb0e336808f1112c44fa18c6f33
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104610971"
---
# <a name="install-sql-server-with-smb-fileshare-storage"></a>SMB ファイル共有ストレージを使用して SQL Server をインストールする

[!INCLUDE [SQL Server -Windows Only](../../includes/applies-to-version/sql-windows-only.md)]

[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)]以降では、システム データベース (Master、Model、MSDB、TempDB) と [!INCLUDE[ssDE](../../includes/ssde-md.md)] ユーザー データベースをストレージ オプションとしてサーバー メッセージ ブロック (SMB) ファイル サーバーにインストールできます。 これは、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] スタンドアロン インストールと [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] フェールオーバー クラスター インストール (FCI) の両方に当てはまります。  
  
> [!NOTE]  
>  Filestream は現在、SMB ファイル共有ではサポートされていません。  
  
## <a name="installation-considerations"></a>インストールに関する注意点  
  
### <a name="smb-fileshare-formats"></a>SMB ファイル共有の形式:  
 SMB ファイル共有を指定する際にスタンドアロンおよび FCI データベースでサポートされる汎用名前付け規則 (SMB) パスの形式を次に示します。  
  
-   \\\ServerName\ShareName\  
  
-   \\\ServerName\ShareName  
  
 汎用名前付け規則の詳細については、「[UNC](/openspecs/windows_protocols/ms-dtyp/62e862f4-2a51-452e-8eeb-dc4ff5ee33cc)」を参照してください。  
  
 ループバック UNC パス (サーバー名が localhost、127.0.0.1、またはローカル コンピューター名である UNC パス) はサポートされません。 特別なケースとして、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] と同じノードでホストされたファイル サーバー クラスターを使用している [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] もサポートされません。 この状況を避けるため、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] とファイル サーバー クラスターは、別個の Windows クラスターに作成することをお勧めします。  
  
 次の UNC パス形式はサポートされていません。  
  
-   ループバック パス ( \\\localhost\\..\ または \\\127.0.0.1\\...\ など)  
  
-   管理用共用 ( \\\servername\x$ など)  
  
-   その他の形式の UNC パス ( \\\\?\x:\ など)  
  
-   割り当て済みのネットワーク ドライブ  
  
### <a name="supported-data-definition-language-ddl-statements"></a>サポートされるデータ定義言語 (DDL) ステートメント  
 SMB ファイル共有をサポートする [!INCLUDE[tsql](../../includes/tsql-md.md)] DDL ステートメントおよびデータベース エンジン ストアド プロシージャを次に示します。  
  
1.  [CREATE DATABASE &#40;SQL Server Transact-SQL&#41;](../../t-sql/statements/create-database-transact-sql.md)  
  
2.  [ALTER DATABASE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-database-transact-sql.md)  
  
3.  [RESTORE &#40;Transact-SQL&#41;](../../t-sql/statements/restore-statements-transact-sql.md)  
  
4.  [BACKUP &#40;Transact-SQL&#41;](../../t-sql/statements/backup-transact-sql.md)  
  
### <a name="installation-options"></a>インストール オプション  
  
-   セットアップ UI の [データベース エンジンの構成] ページの [データ ディレクトリ] タブで、[データ ルート ディレクトリ] パラメーターを "\\\fileserver1\share1\"" に設定します。  
  
-   コマンド プロンプトでインストールする場合、"/INSTALLSQLDATADIR" に "\\\fileserver1\share1\"" を指定します。  
  
     次に、SMB ファイル共有オプションを使ってスタンドアロン サーバーに [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] をインストールするためのサンプル構文を示します。  
  
    ```  
    Setup.exe /q /ACTION=Install /FEATURES=SQL /INSTANCENAME=MSSQLSERVER /SQLSVCACCOUNT="<DomainName\UserName>" /SQLSVCPASSWORD="<StrongPassword>" /SQLSYSADMINACCOUNTS="<DomainName\UserName>" /AGTSVCACCOUNT="<DomainName\UserName>" /AGTSVCPASSWORD="<StrongPassword>" /INSTALLSQLDATADIR="\\FileServer\Share1\" /IACCEPTSQLSERVERLICENSETERMS  
    ```  
  
     [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] および [!INCLUDE[ssDE](../../includes/ssde-md.md)] が配置された単一ノードの [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]フェールオーバー クラスター インスタンスを既定のインスタンスとしてインストールするには、次のコマンドを使用します。  
  
    ```  
    setup.exe /q /ACTION=InstallFailoverCluster /InstanceName=MSSQLSERVER /INDICATEPROGRESS /ASSYSADMINACCOUNTS="<DomainName\UserName>" /ASDATADIR=<Drive>:\OLAP\Data /ASLOGDIR=<Drive>:\OLAP\Log /ASBACKUPDIR=<Drive>:\OLAP\Backup /ASCONFIGDIR=<Drive>:\OLAP\Config /ASTEMPDIR=<Drive>:\OLAP\Temp /FAILOVERCLUSTERDISKS="<Cluster Disk Resource Name - for example, 'Disk S:'" /FAILOVERCLUSTERNETWORKNAME="<Insert Network Name>" /FAILOVERCLUSTERIPADDRESSES="IPv4;xx.xxx.xx.xx;Cluster Network;xxx.xxx.xxx.x" /FAILOVERCLUSTERGROUP="MSSQLSERVER" /Features=AS,SQL /ASSVCACCOUNT="<DomainName\UserName>" /ASSVCPASSWORD="xxxxxxxxxxx" /AGTSVCACCOUNT="<DomainName\UserName>" /AGTSVCPASSWORD="xxxxxxxxxxx" /INSTALLSQLDATADIR="\\FileServer\Share1\" /SQLCOLLATION="SQL_Latin1_General_CP1_CS_AS" /SQLSVCACCOUNT="<DomainName\UserName>" /SQLSVCPASSWORD="xxxxxxxxxxx" /SQLSYSADMINACCOUNTS="<DomainName\UserName> /IACCEPTSQLSERVERLICENSETERMS  
    ```  
  
     [!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)]におけるさまざまなコマンド ライン パラメーター オプションの使用方法については、「 [コマンド プロンプトからの SQL Server 2016 のインストール](./install-sql-server-from-the-command-prompt.md)」を参照してください。  
  
## <a name="operating-system-considerations-smb-protocol-vs-ssnoversion"></a>オペレーティング システムに関する注意事項 (SMB プロトコルと [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)])  
 Windows オペレーティング システムのバージョンによって SMB プロトコルのバージョンも異なりますが、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]は、どのバージョンの SMB プロトコルにも対応します。 SMB プロトコルの各バージョンによって、 [!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)]に対するメリットも異なります。  
  
|オペレーティング システム|SMB2 プロトコルのバージョン|に対するメリット [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]|  
|----------------------|---------------------------|-------------------------------------------|  
|[!INCLUDE[firstref_longhorn](../../includes/firstref-longhorn-md.md)] SP 2|2.0|従来のバージョンの SMB よりもパフォーマンスに優れています。<br /><br /> 持続性が高いため、一時的なネットワーク障害もスムーズに復旧できます。|  
|[!INCLUDE[winserver2008r2](../../includes/winserver2008r2-md.md)] SP 1 (Server Core を含む)|2.1|大きい MTU をサポートするため、SQL のバックアップや復元など、大規模なデータ転送が高速化されます。 この機能を使用するには、ユーザーが機能を有効にする必要があります。 この機能を有効にする方法の詳細については、「[What's New in SMB](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ff625695(v=ws.10))」(SMB の新機能) (https://go.microsoft.com/fwlink/?LinkID=237319) ) を参照してください。<br /><br /> 大幅なパフォーマンス向上。特に、SQL OLTP スタイルのワークロードに対して効果的です。 パフォーマンスを向上するには、修正プログラムを適用する必要があります。 この修正プログラムの詳細については、[こちら](https://mskb.pkisolutions.com/kb/2536493) (https://mskb.pkisolutions.com/kb/2536493) を参照してください。|  
|[!INCLUDE[win8srv](../../includes/win8srv-md.md)](Server Core を含む)|3.0|ファイル サーバー クラスター構成の SQL DBA またはファイル サーバー管理者に必要な、ファイル共有の透過的フェールオーバー (管理者の操作が不要でダウンタイムが発生しないフェールオーバー) をサポートします。<br /><br /> 複数のネットワーク インターフェイスを同時使用する IO をサポートします。また、ネットワーク インターフェイスの障害に対する耐性も優れています。<br /><br /> RDMA 機能を備えたネットワーク インターフェイスをサポートします。<br /><br /> これらの機能およびサーバー メッセージ ブロックの詳細については、「[Server Message Block overview](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831795(v=ws.11))」(サーバー メッセージ ブロックの概要) (https://go.microsoft.com/fwlink/?LinkId=253174) を参照してください。<br /><br /> 継続的可用性機能を備えたスケールアウト ファイル サーバー (SoFS) をサポートします。|  
|[!INCLUDE[win8srv](../../includes/win8srv-md.md)] R2 (Server Core を含む)|3.2|ファイル サーバー クラスター構成の SQL DBA またはファイル サーバー管理者に必要な、ファイル共有の透過的フェールオーバー (管理者の操作が不要でダウンタイムが発生しないフェールオーバー) をサポートします。<br /><br /> 複数のネットワーク インターフェイスを同時使用する IO をサポートします。また、ネットワーク インターフェイスの障害に対する耐性も優れています (SMB マルチチャネルを使用した場合)。<br /><br /> RDMA 機能を備えたネットワーク インターフェイスをサポートします (SMB ダイレクトを使用した場合)。<br /><br /> これらの機能およびサーバー メッセージ ブロックの詳細については、「[Server Message Block overview](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831795(v=ws.11))」(サーバー メッセージ ブロックの概要) (https://go.microsoft.com/fwlink/?LinkId=253174) を参照してください。<br /><br /> 継続的可用性機能を備えたスケールアウト ファイル サーバー (SoFS) をサポートします。<br /><br /> [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] OLTP に共通する小規模なランダム読み取り/書き込み I/O 向けに最適化されます。<br /><br /> 最大転送単位 (MTU) が既定で有効になっています。これにより、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] データ ウェアハウス、データベースのバックアップと復元など、大規模なシーケンシャル転送のパフォーマンスが大幅に向上します。|  
  
## <a name="security-considerations"></a>セキュリティに関する考慮事項  
  
-   [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] サービス アカウントと [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エージェント サービス アカウントには、SMB 共有フォルダーに対するフル コントロールの共有権限と NTFS 権限が必要です。 SMB ファイル サーバーを使用する場合、ドメイン アカウントまたはシステム アカウントを [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] サービス アカウントにすることができます。 共有および NTFS アクセス許可の詳細については、「[ファイル サーバーの共有アクセス許可と NTFS アクセス許可](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754178(v=ws.11))」 (https://go.microsoft.com/fwlink/?LinkId=245535) を参照してください。  
  
    > [!NOTE]  
    >  SMB 共有フォルダーに対するフル コントロールの共有権限と NTFS 権限は、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] サービス アカウント、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エージェント サービス アカウント、および管理サーバー ロールを持つ Windows ユーザーに制限する必要があります。  
  
     ドメイン アカウントを [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] サービス アカウントとして使用することをお勧めします。 システム アカウントをサービス アカウントとして使用した場合、コンピューター アカウントのアクセス許可を \<*domain_name*>\\<*computer_name*>\*$* という形式で指定できます。  
  
    > [!NOTE]  
    >  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップで、ストレージ オプションとして SMB ファイル共有を指定した場合は、ドメイン アカウントをサービス アカウントとして指定する必要があります。 SMB ファイル共有を使用する場合、システム アカウントは [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインストール後にのみサービス アカウントとして指定することができます。  
    >   
    >  仮想アカウントでは、リモートの場所へアクセスすることはできません。 どの仮想アカウントも、コンピューター アカウントの権限を使用します。 \<*domain_name*>\\<*computer_name*>\*$* の形式でコンピューター アカウントを準備してください。  
  
-   [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインストールに使用するアカウントには、クラスター セットアップの際にデータ ディレクトリとして使用される、SMB ファイル共有フォルダーおよび他のすべてのデータ フォルダー (ユーザー データベース ディレクトリ、ユーザー データベース ログ ディレクトリ、TempDB ディレクトリ、TempDB ログ ディレクトリ、バックアップ ディレクトリ) に対して、フル コントロール権限が必要です。  
  
-   [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインストールに使用するアカウントには、SMB ファイル サーバーに対する SeSecurityPrivilege 特権を付与する必要があります。 この特権を付与するには、ファイル サーバーで [ローカル セキュリティ ポリシー] コンソールを使用して、監査とセキュリティ ログの管理ポリシーに [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップ アカウントを追加します。 この設定は、[ローカル セキュリティ ポリシー] コンソールの [ローカル ポリシー] にある [ユーザー権利の割り当て] セクションで行うことができます。  
  
## <a name="known-issues"></a>既知の問題  
  
-   ネットワークにアタッチされたストレージ上に存在する [!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] データベースをデタッチした後で、その [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] データベースを再アタッチしようとすると、データベース権限の問題が発生する場合があります。 詳細については、[エラー 5120](../../relational-databases/errors-events/mssqlserver-5120-database-engine-error.md) を参照してください。
  
-   [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]のクラスター化されたインスタンスのストレージ オプションとして SMB ファイル共有が使用されている場合、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Resource DLL にはこのファイル共有に対する読み取り/書き込み権限がないため、既定では、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] フェールオーバー クラスター診断ログをファイル共有に書き込むことができません。 この問題を解決するには、次のいずれかの方法を試してください。  
  
    1.  ファイル共有に対する読み取り/書き込み権限をクラスター内のすべてのコンピューター オブジェクトに付与する。  
  
    2.  診断ログの場所をローカル ファイル パスに設定する。 次の例を参照してください。  
  
        ```sql  
        ALTER SERVER CONFIGURATION  
        SET DIAGNOSTICS LOG PATH = 'C:\logs';  
        ```  
  
## <a name="see-also"></a>関連項目  
 [SQL Server のインストール計画](../../sql-server/install/planning-a-sql-server-installation.md)   
 [Windows サービス アカウントと権限の構成](../../database-engine/configure-windows/configure-windows-service-accounts-and-permissions.md)  
  
