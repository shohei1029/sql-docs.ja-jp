---
description: Power Pivot for SharePoint のアップグレード
title: Power Pivot for SharePoint のアップグレード | Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.reviewer: ''
ms.technology: ''
ms.topic: conceptual
ms.assetid: 80ba9e43-f3f0-4730-9fb1-2afd2dd3e6fc
author: Minewiskan
ms.author: owend
monikerRange: '>=sql-server-2016'
manager: erikre
ms.openlocfilehash: 971c23cfe9b49ad052c3808deca6e6221acb38e5
ms.sourcegitcommit: bf7577b3448b7cb0e336808f1112c44fa18c6f33
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104610935"
---
# <a name="upgrade-power-pivot-for-sharepoint"></a>Power Pivot for SharePoint のアップグレード

[!INCLUDE [SQL Server -Windows Only](../../includes/applies-to-version/sql-windows-only.md)]
  
  この記事では、[!INCLUDE[ssGeminiShort](../../includes/ssgeminishort-md.md)] の配置を [!INCLUDE[ssGeminiLong](../../includes/ssgeminilong-md.md)] にアップグレードするために必要な手順について説明します。 具体的な手順は、お使いの環境で現在実行されている SharePoint のバージョンによって異なります。また、この手順には、 [!INCLUDE[ssGemini](../../includes/ssgemini-md.md)] for SharePoint アドイン (**spPowerPivot.msi**) が含まれます。  
  
 **[!INCLUDE[applies](../../includes/applies-md.md)]**  SharePoint 2010 | SharePoint 2013  
  
 リリース ノートについては、「 [SQL Server 2016 リリース ノート](../../sql-server/sql-server-2016-release-notes.md)」を参照してください。  
  
 **この記事の内容:**  
  
 [前提条件](#bkmk_prereq)  
  
 [既存の SharePoint 2013 ファームのアップグレード](#bkmk_uprgade_sharepoint2013)  
  
 [既存の SharePoint 2010 ファームのアップグレード](#bkmk_uprgade_sharepoint2010)  
  
 [ブック](#bkmk_workbooks)  
  
 [データ更新](#bkmk_datarefresh)  
  
 [Power Pivot のコンポーネントとサービスのバージョンを確認する](#bkmk_verify_versions)  
  
 [SharePoint ファーム内の複数の Power Pivot for SharePoint サーバーをアップグレードする](#geminifarm)  
  
 [ファーム内の Power Pivot インスタンスに QFE を適用する](#qfe)  
  
 [アップグレード後の検証タスク](#verify)  
  
## <a name="background"></a>バックグラウンド  
  
-   複数の [!INCLUDE[ssGeminiShort](../../includes/ssgeminishort-md.md)] インスタンスを含むマルチサーバー SharePoint 2010 ファームをアップグレードする場合は、各サーバーを完全にアップグレードした **後** で、次のサーバーのアップグレードを続行する必要があります。 完全なアップグレードでは、SQL Server セットアップを実行して [!INCLUDE[ssGeminiShort](../../includes/ssgeminishort-md.md)] プログラム ファイルをアップグレードした後、アップグレードしたサービスを構成するアップグレード処理を実行します。 適切な [!INCLUDE[ssGemini](../../includes/ssgemini-md.md)] 構成ツールまたは Windows PowerShell でアップグレード処理を実行するまでは、サーバーの可用性が制限されます。  
  
-   SharePoint 2010 ファーム内の [!INCLUDE[ssGemini](../../includes/ssgemini-md.md)] System サービスおよび Analysis Services のインスタンスはすべて、同じバージョンである必要があります。 バージョンを確認する方法について詳しくは、この記事の「[PowerPivot のコンポーネントとサービスのバージョンを確認する](#bkmk_verify_versions)」をご覧ください。  
  
-   [!INCLUDE[ssGemini](../../includes/ssgemini-md.md)] 構成ツールは SQL Server の共有機能の 1 つで、すべての共有機能は同時にアップグレードされます。 アップグレード プロセス中に、共有機能のアップグレードを必要とする他の SQL Server インスタンスまたは機能を選択すると、 [!INCLUDE[ssGemini](../../includes/ssgemini-md.md)] 構成ツールもアップグレードされます。 [!INCLUDE[ssGemini](../../includes/ssgemini-md.md)] 構成ツールがアップグレードされても [!INCLUDE[ssGemini](../../includes/ssgemini-md.md)] インスタンスがアップグレードされない場合は、問題が発生している可能性があります。 SQL Server の共有機能の詳細については、「[インストール ウィザードを使用した SQL Server 2016 へのアップグレード &#40;セットアップ&#41;](../../database-engine/install-windows/upgrade-sql-server-using-the-installation-wizard-setup.md)」を参照してください。  
  
-   [!INCLUDE[ssGemini](../../includes/ssgemini-md.md)] for SharePoint アドイン (**spPowerPivot.msi**) は、以前のバージョンとサイド バイ サイドでインストールされます。 たとえば、このアドインは `c:\Program Files\Microsoft SQL Server\nnn\Tools\PowerPivotTools` フォルダーにインストールされます。 SQL Server インストール ファイルについては、[ファイルの場所](../../sql-server/install/file-locations-for-default-and-named-instances-of-sql-server.md#shared-files-for-all-instances-of-)に関するページを参照してください。
  
##  <a name="prerequisites"></a><a name="bkmk_prereq"></a> 前提条件  
 **アクセス許可**  
  
-   [!INCLUDE[ssGemini](../../includes/ssgemini-md.md)] for SharePoint のインストールをアップグレードするには、ファームの管理者である必要があります。 SQL Server セットアップを実行するには、ローカル管理者である必要があります。  
  
-   ファーム構成データベースに対する **db_owner** 権限を持っている必要があります。  
  
 **SQL Server:**  
  
-   [!INCLUDE[ssGemini](../../includes/ssgemini-md.md)] の既存のインストールが [!INCLUDE[ssKilimanjaro](../../includes/sskilimanjaro-md.md)] の場合、[!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] [!INCLUDE[ssGeminiShort](../../includes/ssgeminishort-md.md)] にアップグレードするには [!INCLUDE[ssKilimanjaro](../../includes/sskilimanjaro-md.md)] Service Pack 2 (SP2) が必要です。  
  
-   [!INCLUDE[ssGemini](../../includes/ssgemini-md.md)] の既存のインストールが [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] の場合、[!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] [!INCLUDE[ssGeminiShort](../../includes/ssgeminishort-md.md)] にアップグレードするには [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] Service Pack 1 (SP1) が必要です。  
  
 **SharePoint 2010:**  
  
-   既存のインストールで SharePoint 2010 が実行されている場合は、 [!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)][!INCLUDE[ssGeminiShort](../../includes/ssgeminishort-md.md)]。 詳細については、「 [Microsoft SharePoint 2010 Service Pack 2](https://www.microsoft.com/download/details.aspx?id=39672)」を参照してください。 バージョンを確認するには、PowerShell コマンド `(Get-SPfarm).BuildVersion.ToString()` を使用します。 リリース日でビルド バージョンを参照するには、「 [SharePoint 2010 のビルド番号](https://www.toddklindt.com/blog/Lists/Posts/Post.aspx?ID=224)」を参照してください。  
  
##  <a name="upgrade-an-existing-sharepoint-2013-farm"></a><a name="bkmk_uprgade_sharepoint2013"></a> 既存の SharePoint 2013 ファームのアップグレード  
 SharePoint 2013 に配置された [!INCLUDE[ssGeminiShort](../../includes/ssgeminishort-md.md)] をアップグレードするには、次の手順を実行します。  
  
 ![PowerPivot for SharePoint 2013 アップグレード](../../database-engine/install-windows/media/as-powepivot-upgrade-flow-sharepoint2013.png "PowerPivot for SharePoint 2013 アップグレード")  
  
1.  SharePoint モードで [!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] を実行するバックエンド サーバー上で [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] セットアップを実行します。 サーバーで [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]の複数のインスタンスがホストされている場合は、少なくとも **POWERPIVOT** インスタンスをアップグレードします。 [!INCLUDE[ssGemini](../../includes/ssgemini-md.md)] のアップグレードに関連するセットアップ ウィザードの手順の概要を次に示します。  
  
    1.  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップ ウィザードで、 **[インストール]** をクリックします。  
  
    2.  **[SQL Server ... からのアップグレード]** をクリックします。  
  
    3.  **[インスタンスの選択]** ページで、 **POWERPIVOT** インスタンス名を選択し、 **[次へ]** をクリックします。  
  
    4.  詳細については、「[インストール ウィザードを使用した SQL Server 2016 へのアップグレード &#40;セットアップ&#41;](../../database-engine/install-windows/upgrade-sql-server-using-the-installation-wizard-setup.md)」を参照してください。  
  
2.  サーバーを再起動します。  
  
3.  SharePoint 2013 ファーム内の各サーバーで [!INCLUDE[ssGemini](../../includes/ssgemini-md.md)] for SharePoint アドイン (**spPowerPivot.msi**) を実行し、データ プロバイダーをインストールします。 データ プロバイダーもアップグレードする SQL Server セットアップ ウィザードを実行したサーバーは例外です。 詳細については、[Microsoft SQL Server 2014 PowerPivot for Microsoft SharePoint 2013 のダウンロード ページ](https://www.microsoft.com/download/details.aspx?id=42300)および「[PowerPivot for SharePoint アドインのインストールまたはアンインストール &#40;SharePoint 2013&#41;](/analysis-services/instances/install-windows/install-or-uninstall-the-power-pivot-for-sharepoint-add-in-sharepoint-2013)」を参照してください。  
  
4.  **SharePoint 2013 ファーム内の各サーバーで [!INCLUDE[ssGemini](../../includes/ssgemini-md.md)] for SharePoint 2013 構成ツールを実行し** 、アドインによってインストールされた更新済みのソリューション ファイルを使用して SharePoint ファームを構成します。 この手順に SharePoint サーバーの全体管理を使用することはできません。 詳細については、「  
  
    1.  Windows のスタート画面から「 **[!INCLUDE[ssGemini](../../includes/ssgemini-md.md)]** 」と入力し、検索結果で **[!INCLUDE[ssGemini](../../includes/ssgemini-md.md)] for SharePoint 2013 構成** をクリックします。 検索結果には 2 つのバージョンの構成ツールが表示される場合があることに注意してください。  
  
         ![2 つの PowerPivot 構成ツール](/analysis-services/analysis-services/instances/install-windows/media/as-powerpivot-configtools-bothicons.gif "2 つの PowerPivot 構成ツール")  
  
         または  
  
         **[スタート]** ボタンをクリックし、 **[すべてのプログラム]** をポイントして、[ [!INCLUDE[ssCurrentUI](../../includes/sscurrentui-md.md)]]、 **[構成ツール]** 、 **[!INCLUDE[ssGemini](../../includes/ssgemini-md.md)] For SharePoint 2013 構成** の順にクリックします。 このツールは、 [!INCLUDE[ssGeminiShort](../../includes/ssgeminishort-md.md)] がローカル サーバーにインストールされている場合にのみ表示されることに注意してください。  
  
    2.  起動時、構成ツールにより、 [!INCLUDE[ssGemini](../../includes/ssgemini-md.md)] ファーム ソリューションと [!INCLUDE[ssGemini](../../includes/ssgemini-md.md)] Web アプリケーション ソリューションのアップグレード状態がチェックされます。 これらのソリューションの古いバージョンが検出されると、メッセージ "**新しいバージョンの [!INCLUDE[ssGemini](../../includes/ssgemini-md.md)] ソリューション ファイルが検出されました。ファームをアップグレードするために、アップグレード オプションを選択してください。** " と表示されます。 **[OK]** をクリックし、システムの検証メッセージを閉じます。  
  
    3.  **[機能、サービス、アプリケーション、およびソリューションのアップグレード]** をクリックし、 **[OK]** をクリックします。  
  
    4.  左側のペインにあるタスク一覧内のアクションを確認し、ツールで実行しないアクションを除外します。 既定ではすべてのアクションが含まれています。 アクションを削除するには、左側のタスク一覧でアクションを選択し、 **[パラメーター]** ページの **[この操作をタスク一覧に含めます]** チェック ボックスをオフにします。  
  
    5.  必要に応じて、 **[スクリプト]** タブまたは **[出力]** タブの詳細情報を確認します。  
  
         [出力] タブには、ツールによって実行されるアクションの概要が示されます。 この情報は、 `C:\Program Files\Microsoft SQL Server\130\Tools\PowerPivotTools\SPAddinConfiguration\Log`にあるログ ファイルに保存されます。  
  
         [スクリプト] タブには、PowerShell コマンドレットや、ツールが実行する PowerShell スクリプト ファイルが表示されます。  
  
    6.  **[検証]** をクリックして、それぞれのアクションが有効かどうかを確認します。 **[検証]** が使用不可能な場合、システムにおいてすべてのアクションが有効であることを意味します。 **[検証]** が使用可能な場合は、入力値 (たとえば、Excel サービス アプリケーション名) を変更している可能性があるか、または特定のアクションを実行できないことがツールによって検出された可能性があります。 アクションを実行できない場合は、そのアクションを除外するか、またはアクションが無効であることを示すフラグが設定された原因となっている根本的な条件を解決してください。  
  
        > [!IMPORTANT]  
        >  最初のアクションである **[ファーム ソリューションのアップグレード]** は、常に最初に処理する必要があります。 このアクションを実行すると、サーバーの構成に使用する PowerShell コマンドレットを登録できます。 このアクションを実行したときにエラーが発生する場合は、続行しないでください。 タスク一覧の残りのアクションを処理する前に、エラーとして返された情報に基づいて問題を診断し、解決してください。  
  
    7.  **[実行]** をクリックして、このタスクで有効なすべてのアクションを実行します。 **[実行]** は、検証チェックに合格した後でのみ使用可能になります。 **[実行]** をクリックすると、アクションがバッチ モードで処理されることを示す次の警告が表示されます: "**ツールで有効としてフラグが立てられている構成設定はすべて SharePoint ファームに適用されます。続行しますか?** "  
  
    8.  **[はい]** をクリックして続行します。  
  
    9. ファーム内のソリューションおよび機能のアップグレードが完了するまで、数分かかります。 この処理の間に、[!INCLUDE[ssGemini](../../includes/ssgemini-md.md)] データに対する接続要求は **失敗** し、"**データを更新できません**" や "**要求されたアクションを実行しようとしてエラーが発生しました。再試行してください**" のようなエラーが表示されます。 アップグレードが完了すると、サーバーは使用可能になり、これらのエラーは発生しなくなります。  
  
     詳細については、「  
  
    -   [Power Pivot の構成ツール](/analysis-services/power-pivot-sharepoint/power-pivot-configuration-tools)  
  
    -   [PowerPivot for SharePoint 2013 の構成または修復 &#40;PowerPivot 構成ツール&#41;](/analysis-services/power-pivot-sharepoint/configure-or-repair-power-pivot-for-sharepoint-2013)  
  
    -   [Windows PowerShell を使用した Power Pivot の構成](/analysis-services/power-pivot-sharepoint/power-pivot-configuration-using-windows-powershell)  
  
    -   [Power Pivot for SharePoint 用 PowerShell リファレンス](/analysis-services/powershell/powershell-reference-for-power-pivot-for-sharepoint)  
  
5.  アップグレード後の手順を実行し、ファーム内の [!INCLUDE[ssGemini](../../includes/ssgemini-md.md)] サーバーのバージョンを確認して、アップグレードが成功したことを確認します。 詳しくは、この記事の「[アップグレード後の検証タスク](#verify)」および次のセクションをご覧ください。  
  
##  <a name="upgrade-an-existing-sharepoint-2010-farm"></a><a name="bkmk_uprgade_sharepoint2010"></a> 既存の SharePoint 2010 ファームのアップグレード  
 SharePoint 2010 に配置された [!INCLUDE[ssGeminiShort](../../includes/ssgeminishort-md.md)] をアップグレードするには、次の手順を実行します。  
  
 ![PowerPivot for SharePoint 2010 アップグレード](../../database-engine/install-windows/media/as-powepivot-upgrade-flow-sharepoint2010.png "PowerPivot for SharePoint 2010 アップグレード")  
  
1.  [Microsoft SharePoint 2010 Service Pack 2](https://www.microsoft.com/download/details.aspx?id=39672) をダウンロードして、ファーム内のすべてのサーバーに適用します。 SharePoint SP2 のインストールが成功したことを確認します。 [サーバーの全体管理] の [アップグレードと移行] ページで、[製品および更新プログラムのインストール状態の確認] ページを開いて SP2 に関連するステータス メッセージを参照します。  
  
2.  SharePoint 2010 Administration Windows Service が実行されていることを確認します。  
  
    ```  
    Get-Service | where {$_.displayname -like "*SharePoint*"}  
    ```  
  
3.  **SharePoint** サービスの **SQL Server Analysis Services** と **SQL Server [!INCLUDE[ssGemini](../../includes/ssgemini-md.md)] System サービス** が SharePoint サーバーの全体管理で開始されていることを確認するか、次の PowerShell コマンドを使用します。  
  
    ```  
    get-SPserviceinstance | where {$_.typename -like "*sql*"}  
    ```  
  
4.  **Windows** サービスの **SQL Server Analysis Services ([!INCLUDE[ssGemini](../../includes/ssgemini-md.md)])** が実行されていることを確認します。  
  
    ```  
    Get-Service | where {$_.displayname -like "*powerpivot*"}  
    ```  
  
5.  **SQL Server Analysis Services ([!INCLUDE[ssGemini](../../includes/ssgemini-md.md)])** Windows サービスを実行する最初の SharePoint アプリケーション サーバーで、 **[!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] セットアップを実行** して、POWERPIVOT インスタンスをアップグレードします。 SQL Server セットアップ ウィザードの [インストール] ページで、アップグレード オプションを選択します。 詳細については、「 [インストール ウィザードを使用した SQL Server 2016 へのアップグレード &#40;セットアップ&#41;](../../database-engine/install-windows/upgrade-sql-server-using-the-installation-wizard-setup.md)」を参照してください。  
  
6.  構成ツールを実行する前に **サーバーを再起動します** 。 これにより、SQLServer セットアップによってインストールされた更新プログラムや必須コンポーネントがシステムで完全に構成されます。  
  
7.  **SharePoint 2013 ファーム内の各サーバーで [!INCLUDE[ssGemini](../../includes/ssgemini-md.md)] ) サービスを実行する最初の SharePoint アプリケーション サーバーで、** 構成ツールを実行[!INCLUDE[ssGemini](../../includes/ssgemini-md.md)]して、SharePoint のソリューションと Web サービスをアップグレードします。 この手順にサーバーの全体管理を使用することはできません。  
  
    1.  **[スタート]** メニューの **[すべてのプログラム]** をポイントし、[ [!INCLUDE[ssCurrentUI](../../includes/sscurrentui-md.md)]] をクリックします。次に、 **[構成ツール]** をクリックし、 **[!INCLUDE[ssGemini](../../includes/ssgemini-md.md)] 構成ツール** をクリックします。 このツールは、 [!INCLUDE[ssGeminiShort](../../includes/ssgeminishort-md.md)] がローカル サーバーにインストールされている場合にのみ表示されることに注意してください。  
  
    2.  起動時、構成ツールにより、 [!INCLUDE[ssGemini](../../includes/ssgemini-md.md)] ファーム ソリューションと [!INCLUDE[ssGemini](../../includes/ssgemini-md.md)] Web アプリケーション ソリューションのアップグレード状態がチェックされます。 これらのソリューションの古いバージョンが検出されると、メッセージ "新しいバージョンの [!INCLUDE[ssGemini](../../includes/ssgemini-md.md)] ソリューション ファイルが検出されました。 ファームをアップグレードするために、アップグレード オプションを選択してください。" と表示されます。 **[OK]** をクリックして、このメッセージを閉じます。  
  
    3.  **[機能、サービス、アプリケーション、およびソリューションのアップグレード]** をクリックし、 **[OK]** をクリックして続行します。  
  
    4.  次の警告が表示されます: "[!INCLUDE[ssGemini](../../includes/ssgemini-md.md)] 管理ダッシュボードのブックを最新のバージョンにアップグレードしようとしています。 既存のブックに加えたカスタマイズは失われます。 続行しますか?"  
  
         これは、データ更新操作を報告する [!INCLUDE[ssGemini](../../includes/ssgemini-md.md)] 管理ダッシュボードのブックに関する警告です。 これらのブックをカスタマイズしている場合、これらのブックに加えた変更は、既存のファイルを新しいバージョンで置き換えるときに失われます。  
  
         ブックを新しいバージョンで上書きする場合は、 **[はい]** をクリックします。 それ以外の場合は、 **[いいえ]** をクリックしてホーム ページに戻ります。 ブックを別の場所に保存してコピーを作成します。続行する準備が整ったら、この手順に戻ります。  
  
         ダッシュボードで使用するブックをカスタマイズする方法の詳細については、「 [Power Pivot 管理ダッシュボードのカスタマイズ](/previous-versions/sql/sql-server-2008-r2/ff718155(v=sql.105))」を参照してください。  
  
    5.  タスク一覧内のアクションを確認し、ツールで実行しないアクションを除外します。 既定ではすべてのアクションが含まれています。 アクションを削除するには、タスク一覧でアクションを選択し、[パラメーター] ページの **[この操作をタスク一覧に含めます]** チェック ボックスをオンにします。  
  
    6.  必要に応じて、 **[出力]** タブまたは **[スクリプト]** タブの詳細情報を確認します。  
  
         [出力] タブには、ツールによって実行されるアクションの概要が示されます。 この情報は、 `c:\Program Files\Microsoft SQL Server\130\Tools\PowerPivotTools\ConfigurationTool\Log`にあるログ ファイルに保存されます。  
  
         [スクリプト] タブには、PowerShell コマンドレットや、ツールが実行する PowerShell スクリプト ファイルが表示されます。  
  
    7.  **[検証]** をクリックして、それぞれのアクションが有効かどうかを確認します。 **[検証]** が使用不可能な場合、システムにおいてすべてのアクションが有効であることを意味します。 **[検証]** が使用可能な場合は、入力値 (たとえば、Excel サービス アプリケーション名) を変更している可能性があるか、または特定のアクションを実行できないことがツールによって検出された可能性があります。 アクションを実行できない場合は、そのアクションを除外するか、またはアクションが無効であることを示すフラグが設定された原因となっている根本的な条件を解決してください。  
  
        > [!IMPORTANT]  
        >  最初のアクションである **[ファーム ソリューションのアップグレード]** は、常に最初に処理する必要があります。 このアクションを実行すると、サーバーの構成に使用する PowerShell コマンドレットを登録できます。 このアクションを実行したときにエラーが発生する場合は、続行しないでください。 タスク一覧の残りのアクションを処理する前に、エラーとして返された情報に基づいて問題を診断し、解決してください。  
  
    8.  **[実行]** をクリックして、このタスクで有効なすべてのアクションを実行します。 **[実行]** は、検証チェックに合格した後でのみ使用可能になります。 **[実行]** をクリックすると、アクションがバッチ モードで処理されることを示す次の警告が表示されます: "ツールで有効としてフラグが立てられている構成設定はすべて SharePoint ファームに適用されます。 続行しますか?"  
  
    9. **[はい]** をクリックして続行します。  
  
    10. ファーム内のソリューションおよび機能のアップグレードが完了するまで、数分かかります。 この間、[!INCLUDE[ssGemini](../../includes/ssgemini-md.md)] データに対する接続要求は、"データを更新できません" または "要求されたアクションを実行しようとしたときにエラーが発生しました。 再試行してください。" アップグレードが完了すると、サーバーは使用可能になり、これらのエラーは発生しなくなります。  
  
8.  ファーム内の各 SQL Server Analysis Services ([!INCLUDE[ssGemini](../../includes/ssgemini-md.md)]) サービスに対して次の **処理を繰り返します**。1) SQL Server セットアップを実行する、2)[!INCLUDE[ssGemini](../../includes/ssgemini-md.md)] 構成ツールを実行する。  
  
9. アップグレード後の手順を実行し、ファーム内の [!INCLUDE[ssGemini](../../includes/ssgemini-md.md)] サーバーのバージョンを確認して、アップグレードが成功したことを確認します。 詳しくは、この記事の「[アップグレード後の検証タスク](#verify)」および次のセクションをご覧ください。  
  
10. **エラーのトラブルシューティング**  
  
     各アクションのパラメーター ペインには、エラー情報が表示されます。  
  
     ソリューションの配置または取り消しに関連する問題に対しては、SharePoint 2010 Administrator サービスが開始されていることを確認します。 このサービスは、ファームの構成の変更をトリガーするタイマー ジョブを実行します。 サービスが実行されていない場合、ソリューションの配置または取り消しは失敗します。 永続的なエラーは、既存の配置または取り消しジョブが既にキューに格納されていて、構成ツールの他のアクションがブロックされていることを示します。  
  
    1.  SharePoint 2010 管理シェルを管理者として起動し、次のコマンドを実行してキュー内のジョブを表示します。  
  
        ```  
        Stsadm -o enumdeployments  
        ```  
  
    2.  既存の配置について、 **[種類]** が "取り消し" または "展開" であり、 **[ファイル]** が powerpivotwebapp.wsp または powerpivotfarm.wsp であることを確認します。  
  
    3.  [!INCLUDE[ssGemini](../../includes/ssgemini-md.md)] ソリューションに関連するデプロイまたは取り消しの場合、 **[JobId]** の GUID 値をコピーし、次のコマンドに貼り付けます (シェルの [編集] メニューの [マーク]、[コピー]、および [貼り付け] を使用して GUID をコピーします)。  
  
        ```  
        Stsadm -o canceldeployment -id "<GUID>"  
        ```  
  
    4.  構成ツールで **[検証]** に続けて **[実行]** をクリックして、タスクを再試行します。  
  
     他のエラーについては、ULS ログを確認します。 詳細については、「[SharePoint ログ ファイルと診断ログの構成と表示 &#40;Power Pivot for SharePoint&#41;](/analysis-services/power-pivot-sharepoint/configure-and-view-sharepoint-and-diagnostic-logging)」を参照してください。  
  
##  <a name="workbooks"></a><a name="bkmk_workbooks"></a> ブック  
 サーバーをアップグレードしても、サーバー上で実行される [!INCLUDE[ssGemini](../../includes/ssgemini-md.md)] ブックがアップグレードされない場合がありますが、前のバージョンの [!INCLUDE[ssGemini](../../includes/ssgemini-md.md)] for Excel で作成された古いブックは、そのリリースで利用できる機能を使用して以前と同様に機能し続けます。 ブックが機能し続けるのは、以前のインストールに含まれていた Analysis Services OLE DB プロバイダーのバージョンがアップグレードしたサーバーに残っているためです。  
  
##  <a name="data-refresh"></a><a name="bkmk_datarefresh"></a> データ更新  
 アップグレードはデータ更新操作に影響を及ぼします。 サーバーでのスケジュールされたデータ更新は、そのサーバー バージョンに対応するブックに対してのみ使用できます。 以前のバージョンのブックをホストしている場合、それらのブックに対してデータ更新が機能しなくなる可能性があります。 データ更新を再び有効にするには、ブックをアップグレードする必要があります。 [!INCLUDE[ssGemini](../../includes/ssgemini-md.md)] for Excel で各ブックを手動でアップグレードするか、SharePoint 2010 でデータ更新機能の自動アップグレードを有効にすることができます。 自動アップグレードでは、データ更新を実行する前にブックが最新のバージョンにアップグレードされます。これにより、データ更新操作をスケジュールどおり行うことができます。  
  
##  <a name="verify-the-versions-of-power-pivot-components-and-services"></a><a name="bkmk_verify_versions"></a> Power Pivot のコンポーネントとサービスのバージョンを確認する  
 [!INCLUDE[ssGemini](../../includes/ssgemini-md.md)] System サービスと Analysis Services のインスタンスはすべて、同じバージョンである必要があります。 すべてのサーバー コンポーネントが同じバージョンであることを確認するには、次のコンポーネントのバージョン情報を確認します。  
  
### <a name="verify-the-version-of-power-pivot-solutions-and-the-power-pivot-system-service"></a>Power Pivot ソリューションと Power Pivot System サービスのバージョンを確認する  
 次の PowerShell コマンドを実行します。  
  
```  
Get-PowerPivotSystemService  
```  
  
 **CurrentSolutionVersion** を確認します。 [!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] のバージョンは 13.0.\<major build>.\<minor build> です  
  
### <a name="verify-the-version-of-the-analysis-services-windows-service"></a>Analysis Services Windows サービスのバージョン確認  
 SharePoint 2010 ファーム内の [!INCLUDE[ssGeminiShort](../../includes/ssgeminishort-md.md)] サーバーの一部のみをアップグレードした場合は、アップグレードされていないサーバー上の [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] インスタンスがファームで想定されるバージョンより古くなります。 すべてのサーバーを同じバージョンにアップグレードする必要があります。 各コンピューターの SQL Server Analysis Services ([!INCLUDE[ssGemini](../../includes/ssgemini-md.md)]) Windows サービスのバージョンを確認するには、次のいずれかの方法を使用します。  
  
 **エクスプローラーを使用した場合**:  
  
1.  **インスタンスの** Bin [!INCLUDE[ssGemini](../../includes/ssgemini-md.md)] フォルダーに移動します。 たとえば、「 `C:\Program Files\Microsoft SQL Server\MSAS13.POWERPIVOT\OLAP\bin` 」のように指定します。  
  
2.  `msmdsrv.exe`を右クリックし、 **[プロパティ]** をクリックします。  
  
3.  **[詳細]** をクリックします。  
  
4.  [!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] ファイルのバージョンは 13.00.\<major build>.\<minor build> になります。  
  
5.  この数値が [!INCLUDE[ssGemini](../../includes/ssgemini-md.md)] ソリューションおよびシステム サービスのバージョンと同じであることを確認します。  
  
 **サービスの開始情報を使用した場合:**  
  
 [!INCLUDE[ssGemini](../../includes/ssgemini-md.md)] サービスが開始されると、Windows イベント ログにバージョン情報が書き込まれます。  
  
1.  Windows の `eventvwr`  
  
2.  ソースの `MSOLAP$POWERPIVOT`のフィルターを作成します。  
  
3.  次のような情報レベルのイベントを探します。  
  
     サービスが開始されました。 Microsoft SQL Server Analysis Services 64 ビット Evaluation (x64) RTM **13.0.2000.8**。  
  
 **PowerShell を使用したファイル バージョンの確認**  
  
 PowerShell を使用して製品バージョンを確認できます。 バージョンの確認をスクリプト化または自動化する場合は、PowerShell を使用すると便利です。  
  
```  
(get-childitem "C:\Program Files\Microsoft SQL Server\MSAS13.POWERPIVOT2000\OLAP\bin\msmdsrv.exe").VersionInfo  
```  
  
 上記の PowerShell コマンドによって、次のような情報が返されます。  
  
 ProductVersion   FileVersion           FileName  
  
 **13.0.2000.8** 2016.0130.200    C:\Program Files\Microsoft SQL Server\MSAS13.POWERPIVOT2000\OLAP\bin\msmdsrv.exe  
  
### <a name="verify-the-msolap-data-provider-version-on-sharepoint"></a>SharePoint での MSOLAP データ プロバイダーのバージョン確認  
 Excel Services によって信頼されている Analysis Services OLE DB プロバイダーのバージョンを確認するには、次の手順に従います。 Excel Services の信頼できるデータ プロバイダーの設定を確認するには、ファームまたはサービス アプリケーションの管理者である必要があります。  
  
1.  サーバーの全体管理で、[アプリケーション構成の管理] の **[サービス アプリケーションの管理]** をクリックします。  
  
2.  Excel Services サービス アプリケーションの名前 (たとえば **ExcelServiceApp1**) をクリックします。  
  
3.  **[信頼できるデータ プロバイダー]** をクリックします。 MSOLAP.5 (Microsoft OLE DB プロバイダー (OLAP Services 11.0 用)) が表示されます。 [!INCLUDE[ssGeminiShort](../../includes/ssgeminishort-md.md)] をアップグレードした場合、以前のバージョンの MSOLAP.4 も表示されます。  
  
4.  詳細については、「 [Excel Services で信頼できるデータ プロバイダーとして MSOLAP.5 を追加](/analysis-services/power-pivot-sharepoint/add-msolap-5-as-a-trusted-data-provider-in-excel-services)」を参照してください。  
  
 MSOLAP.4 には、"Microsoft OLE DB プロバイダー (OLAP Services 10.0 用)" という説明が表示されます。 このバージョンは、Excel Services と共にインストールされる [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] に付属する既定のバージョンか、 [!INCLUDE[ssKilimanjaro](../../includes/sskilimanjaro-md.md)] バージョンである可能性があります。 SharePoint によってインストールされる既定のバージョンでは、 [!INCLUDE[ssGemini](../../includes/ssgemini-md.md)] データ アクセスはサポートされません。 SharePoint で [!INCLUDE[ssKilimanjaro](../../includes/sskilimanjaro-md.md)] ブックに接続するには、 [!INCLUDE[ssGemini](../../includes/ssgemini-md.md)] 以降のバージョンが必要です。 [!INCLUDE[ssKilimanjaro](../../includes/sskilimanjaro-md.md)] バージョンがインストールされていることを確認するには、前のセクションの手順に従って、ファイルのプロパティでバージョンを確認してください。  
  
### <a name="verify-the-adomdnet-data-provider-version"></a>ADOMD.NET データ プロバイダーのバージョン確認  
 インストールされている ADOMD.NET のバージョンを確認するには、次の手順に従います。 Excel Services の信頼できるデータ プロバイダーの設定を確認するには、ファームまたはサービス アプリケーションの管理者である必要があります。  
  
1.  SharePoint アプリケーション サーバーで、 `c:\Windows\Assembly`を参照します。  
  
2.  アセンブリ名で並べ替えて、 **Microsoft.Analysis Services.Adomd.Client** を見つけます。  
  
3.  バージョン 13.0.\<build number> であることを確認します。  
  
##  <a name="upgrading-multiple-power-pivot-for-sharepoint-servers-in-a-sharepoint-farm"></a><a name="geminifarm"></a> SharePoint ファーム内の複数の Power Pivot for SharePoint サーバーをアップグレードする  
 複数の [!INCLUDE[ssGeminiShort](../../includes/ssgeminishort-md.md)] サーバーを含むマルチサーバー トポロジでは、すべてのサーバー インスタンスとコンポーネントで同じバージョンを使用する必要があります。 最新バージョンのソフトウェアを実行するサーバーによって、ファーム内のすべてのサーバーのレベルが設定されます。 一部のサーバーのみをアップグレードすると、古いバージョンのソフトウェアを実行するサーバーは、同様にアップグレードを行うまで使用できなくなります。  
  
 最初のサーバーをアップグレードした後は、まだアップグレードされていない他のサーバーが **使用できなくなります**。 すべてのサーバーが同じレベルで実行されるようになると、再び使用可能になります。  
  
 SQL Server セットアップによって [!INCLUDE[ssGemini](../../includes/ssgemini-md.md)] ソリューション ファイルが物理コンピューター上の適切な位置でアップグレードされますが、ファームで使用中のソリューションをアップグレードするには、この記事の前のセクションで説明した [!INCLUDE[ssGemini](../../includes/ssgemini-md.md)] 構成ツールを使う必要があります。  
  
##  <a name="applying-a-qfe-to-a-power-pivot-instance-in-the-farm"></a><a name="qfe"></a> ファーム内の Power Pivot インスタンスに QFE を適用する  
 [!INCLUDE[ssGemini](../../includes/ssgemini-md.md)] for SharePoint サーバーに修正プログラムを適用すると、既存のプログラム ファイルが、特定の問題の修正を含む新しいバージョンに更新されます。 QFE をマルチサーバー トポロジに適用するときに、最初に使用すべきプライマリ サーバーはありません。 ファーム内の他の [!INCLUDE[ssGemini](../../includes/ssgemini-md.md)] サーバーに同じ QFE を適用している限り、どのサーバーからでも開始できます。  
  
 QFE を適用した場合は、ファーム構成データベース内のサーバー バージョン情報を更新する構成手順も実行する必要があります。 修正プログラムを適用したサーバーのバージョンがファームに対して新たに想定されるバージョンになります。 すべてのコンピューターに QFE が適用され、構成が完了するまで、QFE が適用されていない [!INCLUDE[ssGemini](../../includes/ssgemini-md.md)] for SharePoint インスタンスを使用して [!INCLUDE[ssGemini](../../includes/ssgemini-md.md)] データの要求を処理することはできません。  
  
 QFE を適切に適用および構成するには、次の手順に従います。  
  
1.  QFE で提供されている手順に従って修正プログラムをインストールします。  
  
2.  [!INCLUDE[ssGemini](../../includes/ssgemini-md.md)] 構成ツールを起動します。  
  
3.  **[機能、サービス、アプリケーション、およびソリューションのアップグレード]** をクリックし、 **[OK]** をクリックします。  
  
4.  アップグレード タスクに含まれているアクションを確認し、 **[検証]** をクリックします。  
  
5.  **[実行]** をクリックしてアクションを適用します。  
  
6.  ファーム内の追加の [!INCLUDE[ssGemini](../../includes/ssgemini-md.md)] for SharePoint インスタンスに対して操作を繰り返します。  
  
    > [!IMPORTANT]  
    >  マルチサーバー配置では、各インスタンスの修正を適用して構成した後で、次のコンピューターに進んでください。 [!INCLUDE[ssGemini](../../includes/ssgemini-md.md)] 構成ツールでは、1 つのインスタンスのアップグレード タスクを完了してから、次のインスタンスに進む必要があります。  
  
 ファーム内のサービスのバージョン情報をチェックするには、サーバーの全体管理の [アップグレードと修正プログラムの管理] セクションにある **[製品と修正プログラムのインストール状態のチェック]** ページを使用します。  
  
##  <a name="post-upgrade-verification-tasks"></a><a name="verify"></a> アップグレード後の検証タスク  
 アップグレードが完了したら、次の手順を実行して、サーバーが動作可能であることを確認します。  
  
|タスク|Link|  
|----------|----------|  
|[!INCLUDE[ssGemini](../../includes/ssgemini-md.md)] for SharePoint を実行するすべてのコンピューターでサービスが実行されていることを確認します。|[PowerPivot for SharePoint サーバーの開始または停止](/analysis-services/power-pivot-sharepoint/start-or-stop-a-power-pivot-for-sharepoint-server)|  
|サイト コレクション レベルでの機能のアクティブ化を確認します。|[サイト コレクションを対象とした Power Pivot 機能の統合をサーバーの全体管理でアクティブ化する方法](/analysis-services/power-pivot-sharepoint/activate-power-pivot-integration-for-site-collections-in-ca)|  
|ブックを開き、フィルターおよびスライサーをクリックしてクエリを開始することで、個々の [!INCLUDE[ssGemini](../../includes/ssgemini-md.md)] ブックが適切に読み込まれることを確認します。|ハード ドライブのキャッシュ ファイルの存在をチェックします。 キャッシュ ファイルが存在する場合は、データ ファイルがその物理サーバーに読み込まれたことを示します。 c:\Program Files\Microsoft SQL Server\MSAS13.POWERPIVOT\OLAP\Backup フォルダー内のキャッシュ ファイルを探します。|  
|データ更新が構成されているブックを選択して、データ更新をテストします。|データ更新をテストする最も簡単な方法は、データ更新スケジュールを変更することです。具体的には、 **[さらに、できるだけ早く更新を行います]** チェック ボックスをオンにして、データ更新がすぐに実行されるようにします。 この手順によって、データ更新が現在のブックに対して成功するかどうかが判明します。 他のよく使用されるブックにもこの手順を実行して、データ更新が機能することを確認します。 データ更新のスケジュール設定については、「 [データ更新のスケジュール (PowerPivot for SharePoint)](/sharepoint/administration/data-refresh-using-the-unattended-data-refresh-account)」を参照してください。|  
|随時、 [!INCLUDE[ssGemini](../../includes/ssgemini-md.md)] 管理ダッシュボードのデータ更新レポートを監視して、データ更新エラーがないことを確認します。|[Power Pivot 管理ダッシュボードと使用状況データ](/analysis-services/power-pivot-sharepoint/power-pivot-management-dashboard-and-usage-data)|  
  
 [!INCLUDE[ssGemini](../../includes/ssgemini-md.md)] 設定と機能を構成する方法の詳細については、「 [サーバーの全体管理での PowerPivot サーバーの管理と構成](/analysis-services/power-pivot-sharepoint/power-pivot-server-administration-and-configuration-in-central-administration)」を参照してください。  
  
 インストール後のすべての構成タスクについて手順を追った説明については、「 [初期構成 (PowerPivot for SharePoint)](/sharepoint/administration/configure-power-pivot-for-sharepoint-2013)」を参照してください。  
  
## <a name="see-also"></a>参照  
 [SQL Server 2016 のエディションとサポートされている機能](../../sql-server/editions-and-components-of-sql-server-2016.md)   
 [Power Pivot for SharePoint 2010 のインストール](https://sharepointgeorge.com/2012/installing-sql-server-powerpivot-sharepointstep-step-guide/)  
  
