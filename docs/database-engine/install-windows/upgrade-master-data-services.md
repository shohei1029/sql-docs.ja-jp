---
title: マスター データ サービスのアップグレード | Microsoft Docs
description: Microsoft SQL Server マスター データ サービスのアップグレードについて 4 つのシナリオを確認します。 ファイルの場所とアップグレードのトラブルシューティングについて説明します。
ms.custom: ''
ms.date: 07/21/2017
ms.prod: sql
ms.reviewer: ''
ms.technology: master-data-services
ms.topic: conceptual
ms.assetid: 9c3543f3-3eb9-455d-a9bf-f17e9506ad21
author: cawrites
ms.author: chadam
monikerRange: '>=sql-server-2016'
ms.openlocfilehash: 94935c3cbba73a9d628dabefabdd2188a00cbc27
ms.sourcegitcommit: bf7577b3448b7cb0e336808f1112c44fa18c6f33
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104610956"
---
# <a name="upgrade-master-data-services"></a>マスター データ サービスのアップグレード

[!INCLUDE [SQL Server -Windows Only](../../includes/applies-to-version/sql-windows-only.md)]
  
  Microsoft [!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] マスター データ サービスのアップグレードには、次のシナリオがあります。  
  
-   [データベース エンジンのアップグレードを伴わないアップグレード](../../database-engine/install-windows/upgrade-master-data-services.md#noengine)  
  
-   [データベース エンジンのアップグレードを伴うアップグレード](../../database-engine/install-windows/upgrade-master-data-services.md#engine)  
  
-   [2 台のコンピューターのシナリオでのアップグレード](../../database-engine/install-windows/upgrade-master-data-services.md#twocomputer)  
  
-   [アップグレードおよびバックアップからのデータベースの復元](../../database-engine/install-windows/upgrade-master-data-services.md#restore)  
  
> [!IMPORTANT]  
> -   アップグレードを実行する前にデータベースをバックアップしてください。  
> -   アップグレード プロセスでは、ストアド プロシージャを再作成し、 [!INCLUDE[ssMDSshort](../../includes/ssmdsshort-md.md)]で使用されるテーブルをアップグレードします。 これらのコンポーネントのいずれかに加えたカスタマイズは失われる場合があります。  
> -   モデル配置パッケージは作成されたエディションの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のみで使用できます。 [!INCLUDE[ssKilimanjaro](../../includes/sskilimanjaro-md.md)]、 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)]、または [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] で作成されたモデル配置パッケージを [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)]に配置することはできません。  
> -   Data Quality Services およびマスター データ サービスを [!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] の最新バージョンにアップグレードした後は、以前のバージョンの Excel 用マスター データ サービス アドインは機能しなくなります。 Excel 用の [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] マスター データ サービス アドインは、「[Microsoft Excel 用マスター データ サービス アドイン](../../master-data-services/microsoft-excel-add-in/master-data-services-add-in-for-microsoft-excel.md)」からダウンロードできます。  
  
##  <a name="file-location"></a><a name="fileLocation"></a> ファイルの場所  
  
-   [!INCLUDE[ss2017](../../includes/sssql17-md.md)]では、既定で、ファイルが *drive*:\Program Files\Microsoft SQL Server\140\Master Data Services にインストールされます。  

-   [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)]では、既定で、ファイルが *drive*:\Program Files\Microsoft SQL Server\130\Master Data Services にインストールされます。  
  
-   [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)]では、既定で、ファイルが *drive*:\Program Files\Microsoft SQL Server\120\Master Data Services にインストールされます。  
  
-   [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)]では、既定で、ファイルが *drive*:\Program Files\Microsoft SQL Server\110\Master Data Services にインストールされます。  
  
-   [!INCLUDE[ssKilimanjaro](../../includes/sskilimanjaro-md.md)]では、既定で、ファイルが *drive*:\Program Files\Microsoft SQL Server\Master Data Services にインストールされます。  
  
##  <a name="upgrade-without-database-engine-upgrade"></a><a name="noengine"></a> データベース エンジンのアップグレードを伴わないアップグレード  
 このシナリオでは、引き続き [!INCLUDE[ssKilimanjaro](../../includes/sskilimanjaro-md.md)]、[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)]、[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)]、または [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] を使用して、MDS データベースをホストします。 ただし、MDS データベースのスキーマをアップグレードしてから、現在の [!INCLUDE[ssNoversion](../../includes/ssnoversion-md.md)] Web アプリケーションを作成して MDS データベースにアクセスする必要があります。 アップグレード後、以前の Web アプリケーションからは MDS データベースにアクセスできなくなります。  
  
 現在の [!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] と以前のバージョンの [!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] を同じコンピューターにインストールできます。 ファイルは、 [ファイルの場所](#fileLocation)に示すように、異なる場所にインストールされます。  
  
 **データベース エンジンをアップグレードしないでアップグレードするには**  
  
1.  [!INCLUDE[ssMDSshort](../../includes/ssmdsshort-md.md)] とその他の必要な機能をインストールします。  
  
    1.  [!INCLUDE[ssNoversion](../../includes/ssnoversion-md.md)] セットアップ ウィザードを開きます。  
  
    2.  左ペインで、 **[インストール]** をクリックします。  
  
    3.  右ペインで、 **[SQL Server の新規スタンドアロン インストールを実行するか、既存のインストールに機能を追加]** をクリックします。  
  
    4.  **[機能の選択]** ページで、 **[!INCLUDE[ssMDSshort](../../includes/ssmdsshort-md.md)]** とその他にインストールする機能を選択します。  
  
    5.  ウィザードを完了します。  
  
2.  MDS データベース スキーマをアップグレードします。  
  
    1.  現在の [!INCLUDE[ssNoversion](../../includes/ssnoversion-md.md)] [!INCLUDE[ssMDScfgmgr](../../includes/ssmdscfgmgr-md.md)] を開きます。  
  
        > [!IMPORTANT]  
        >  MDS データベース スキーマをアップグレードするには、MDS データベースの作成時に指定した管理者アカウントでログインする必要があります。 MDS データベースの mdm.tblUser で、このユーザーは **1** の **ID** 値を持ちます。  
  
    2.  左ペインで **[データベース構成]** をクリックします。  
  
    3.  右ペインで、 **[データベースの選択]** をクリックし、[!INCLUDE[ssKilimanjaro](../../includes/sskilimanjaro-md.md)]、[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)]、[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)]、または [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] のデータベース インスタンスの情報を指定します。  
  
    4.  **[データベースのアップグレード]** をクリックして、 **データベースのアップグレード ウィザード** を起動します。 詳細については、「[データベースのアップグレード ウィザード  &#40;Master Data Services 構成マネージャー&#41;](../../master-data-services/upgrade-database-wizard-master-data-services-configuration-manager.md)」を参照してください。  
  
3.  Web アプリケーションを作成します。  
  
    1.  現在の [!INCLUDE[ssNoversion](../../includes/ssnoversion-md.md)] [!INCLUDE[ssMDScfgmgr](../../includes/ssmdscfgmgr-md.md)] を開きます。  
  
    2.  左ペインで **[Web の構成]** をクリックします。  
  
    3.  右ペインで、 **[Web サイト]** ボックスの一覧から次のいずれかのオプションを選択します。  
  
        -   **[既定の Web サイト]** 。その後、 **[アプリケーションの作成]** をクリックします。  
  
        -   **[新しいサイトの作成]** 。 Web サイトを作成すると、新しい Web アプリケーションが自動的に作成されます。  
  
        > [!IMPORTANT]  
        >  以前のバージョンの SQL Server ([!INCLUDE[ssKilimanjaro](../../includes/sskilimanjaro-md.md)]、 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)]、[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)]、または [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)]) から取得した既存の MDS Web アプリケーションを、マスター データ サービス構成マネージャーの [!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] バージョンで選択できます。 既存の Web アプリケーションを選択することはできません。代わりに MDS に対応する [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] Web アプリケーションを作成する必要があります。 それ以外の場合は、アップグレード後の MDS データベースに Web アプリケーションを関連付けようとすると、ページに関連付けられた構成データが無効であるため、要求したページにアクセスできないことを示すエラーが返されます。  
        >   
        >  既存の ([!INCLUDE[ssKilimanjaro](../../includes/sskilimanjaro-md.md)]、[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)]、[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)]、または [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)]) Web アプリケーションと同じ名前 (別名) を、MDS Web アプリケーションに使用する場合は、まず Web アプリケーションおよびそれに関連付けられているアプリケーション プールを IIS から削除し、次にマスター データ サービス構成マネージャーの [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] バージョンを使用して同じ名前の Web アプリケーションを作成します。 Web アプリケーションとアプリケーション プールを IIS から削除する方法については、「 [Remove an Application (IIS 7)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771205(v=ws.10)) 」 (アプリケーションを削除する (IIS 7))、および「 [Remove an Application Pool (IIS 7)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc772406(v=ws.10))」(アプリケーション プールを削除する (IIS 7)) を参照してください。  
  
4.  新しい Web アプリケーションをアップグレード後の MDS データベースに関連付けます。  
  
    1.  **[アプリケーションとデータベースの関連付け]** セクションで、 **[選択]** をクリックします。  
  
    2.  MDS データベースを選択します。  
  
    3.  **[Apply]** をクリックします。  
  
##  <a name="upgrade-with-database-engine-upgrade"></a><a name="engine"></a> データベース エンジンのアップグレードを伴うアップグレード  
 このシナリオでは、データベース エンジンと[!INCLUDE[ssMDSshort](../../includes/ssmdsshort-md.md)] アプリケーションの両方を、以前のバージョンから [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] または [!INCLUDE[ssSQL16](../../includes/sssql17-md.md)] のいずれかにアップグレードします。  
  
 **データベース エンジンをアップグレードしてアップグレードするには**  
  
1.  **[!INCLUDE[ssKilimanjaro](../../includes/sskilimanjaro-md.md)] の場合のみ**: **[コントロール パネル]**  >  **[プログラムと機能]** の順に開き、Microsoft [!INCLUDE[ssKilimanjaro](../../includes/sskilimanjaro-md.md)][!INCLUDE[ssMDSshort](../../includes/ssmdsshort-md.md)]をアンインストールします。  
  
2.  データベース エンジンを [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] または [!INCLUDE[ssSQL16](../../includes/sssql17-md.md)] にアップグレードします。 詳細については、「 [Choose a Database Engine Upgrade Method](../../database-engine/install-windows/choose-a-database-engine-upgrade-method.md)」を参照してください。  
  
3.  「 [データベース エンジンのアップグレードを伴わないアップグレード](#noengine) 」のすべての手順を完了します。  
  
##  <a name="upgrade-in-two-computer-scenario"></a><a name="twocomputer"></a> 2 台のコンピューターのシナリオでのアップグレード  
 このシナリオでは、SQL Server が 2 台のコンピューターにインストールされているシステムをアップグレードします。1 台は [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] または [!INCLUDE[ssSQL16](../../includes/sssql17-md.md)]、もう 1 台は以前のバージョンの [!INCLUDE[ssNoversion](../../includes/ssnoversion-md.md)] です。  
  
 以前のバージョンの [!INCLUDE[ssNoversion](../../includes/ssnoversion-md.md)] がインストールされている場合は、引き続き以前のバージョンを使用して、1 台のコンピューターで MDS データベースをホストします。 ただし、MDS データベースのスキーマをアップグレードしてから、[!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] または [!INCLUDE[ssSQL16](../../includes/sssql17-md.md)] Web アプリケーションをそれぞれ使用して、MDS データベースにアクセスする必要があります。 以前のバージョンの Web アプリケーションからは、MDS データベースにアクセスできなくなります。  
  
 **2 台のコンピューターのシナリオでアップグレードするには**  
  
-   「 [データベース エンジンのアップグレードを伴わないアップグレード](#noengine)」のすべての手順を完了します。  
  
##  <a name="upgrade-with-restoring-a-database-from-backup"></a><a name="restore"></a> アップグレードおよびバックアップからのデータベースの復元  
 このシナリオでは、1 台の同じコンピューターまたは 2 台の異なるコンピューターに、[!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] または [!INCLUDE[ssSQL16](../../includes/sssql17-md.md)] が、以前のバージョンと共にインストールされています。 アップグレードの前に、[!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] または [!INCLUDE[ssSQL16](../../includes/sssql17-md.md)] リリースより前のバージョンでデータベースがバックアップされており、そのデータベースを復元する必要があります。  
  
 **バックアップからのデータベースを復元してアップグレードするには**  
  
1.  [!INCLUDE[ssMDSshort](../../includes/ssmdsshort-md.md)] とその他の必要な機能をインストールします。  
  
    1.  [!INCLUDE[sssnoversion](../../includes/ssnoversion-md.md)] セットアップ ウィザードを開きます。  
  
    2.  左ペインで、 **[インストール]** をクリックします。  
  
    3.  右ペインで、 **[SQL Server の新規スタンドアロン インストールを実行するか、既存のインストールに機能を追加]** をクリックします。  
  
    4.  **[機能の選択]** ページで、 **[!INCLUDE[ssMDSshort](../../includes/ssmdsshort-md.md)]** とその他にインストールする機能を選択します。  
  
    5.  ウィザードを完了します。  
  
2.  バックアップされたデータベースを復元します。  
  
3.  MDS データベース スキーマをアップグレートして、Web アプリケーションを作成し、新しい Web アプリケーションとアップグレードされた MDS データベースを関連付けます。 手順については、「 [データベース エンジンのアップグレードを伴わないアップグレード](#noengine)」の手順 2 ～ 4 を参照してください。  
  
## <a name="troubleshooting"></a>トラブルシューティング  
 **問題:** [!INCLUDE[ssKilimanjaro](../../includes/sskilimanjaro-md.md)]、[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)]、[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)]、または [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] Web アプリケーションを開くと、"クライアントのバージョンがデータベースのバージョンと互換性がない" ことを示すエラー メッセージが表示される。  
  
 **解決方法:** この問題は、[!INCLUDE[ssKilimanjaro](../../includes/sskilimanjaro-md.md)]、[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)]、[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)]、または [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] のマスター データ マネージャー Web アプリケーションが、[!INCLUDE[ssSQL16](../../includes/sssql17-md.md)] マスター データ サービスにアップグレードされたデータベースにアクセスしようとするときに発生します。 代わりに、[!INCLUDE[ssSQL16](../../includes/sssql17-md.md)] Web アプリケーションを使用する必要があります。  
  
 この問題はまた、MDS データベース スキーマをアップグレードするときに、IIS で **MDS アプリケーション プール** を停止および再起動しなかった場合にも発生する可能性があります。 その場合は、 **MDS アプリケーション プール** を再起動して問題を解決します。  
  
## <a name="see-also"></a>参照  
 [マスター データ サービスのインストール](../../master-data-services/install-windows/install-master-data-services.md)  
  
