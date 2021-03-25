---
title: SysPrep を使用して SQL Server をインストールする
description: SQL Server の Sysprep を使用すると、コンピューター上で SQL Server のスタンドアロン インスタンスを準備し、後で構成を実行できます。
ms.custom: seo-lt-2019
ms.date: 12/13/2019
ms.prod: sql
ms.reviewer: ''
ms.technology: install
ms.topic: conceptual
ms.assetid: e1792eeb-2874-4653-b20e-3063f4eb4e5d
author: cawrites
ms.author: chadam
monikerRange: '>=sql-server-2016'
ms.openlocfilehash: ff653258a42883d2942170f9b26d04f288fcb7f3
ms.sourcegitcommit: bf7577b3448b7cb0e336808f1112c44fa18c6f33
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104611111"
---
# <a name="considerations-for-installing-sql-server-using-sysprep"></a>SysPrep を使用した SQL Server のインストールに関する注意点

[!INCLUDE [SQL Server -Windows Only](../../includes/applies-to-version/sql-windows-only.md)]

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Sysprep を使用すると、コンピューター上で [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のスタンドアロン インスタンスを準備し、後で構成を完了できます。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Sysprep では、2 段階のプロセスで [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]のスタンドアロン インスタンスを構成します。 手順は次のとおりです。  
  
- [イメージの準備](#BKMK_PrepareImage)  
  
    この手順では、製品バイナリのインストール後に、準備中の [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンスのコンピューター、ネットワーク、およびアカウントに固有の情報を構成せずに、インストール処理が停止します。  
  
- [イメージの完了](#BKMK_CompleteImage)  
  
    この手順では、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]の準備済みインスタンスの構成を完了できます。 ここでは、コンピューター、ネットワーク、およびアカウントに固有の情報を入力できます。  
  
SysPrep を使用して [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] をインストールする方法の詳細については、「[SysPrep を使用して SQL Server をインストールする](../../database-engine/install-windows/install-sql-server-using-sysprep.md)」を参照してください。  
  
## <a name="common-uses-for-ssnoversion-sysprep"></a>[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] SysPrep の一般的な使用方法  
[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] SysPrep 機能は、次のいずれかの方法で使用できます。  
  
- イメージの準備手順を使用して、同じコンピューター上に 1 つ以上の未構成の [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンスを準備できます。 同じコンピューターでイメージの完了手順を使用することで、これらの準備済みインスタンスを構成できます。  
  
- 準備済みインスタンスの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップ構成ファイルをキャプチャし、そのファイルを使用して、複数のコンピューター上で後で構成する追加の未構成の [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンスを準備することができます。  
  
- Windows System Preparation ツール ("Windows SysPrep" ともいいます) と組み合わせると、ソース コンピューター上で未構成の準備済み [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンスを格納したオペレーティング システムのイメージを作成できます。 これにより、オペレーティング システム イメージを複数のコンピューター上に配置できます。 オペレーティング システムの構成が完了したら、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップのイメージの完了手順を使用して、準備済みインスタンスを構成できます。  
  
    Windows オペレーティング システムのイメージを準備するには、Windows SysPrep ツールを使用します。 このツールは、組織全体に配置するためにオペレーティング システムのカスタマイズされたイメージをキャプチャする場合に使用されます。 SysPrep とその使用方法の詳細については、[Sysprep](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview) に関するページを参照してください。  
  
## <a name="installation-media-considerations"></a>インストール メディアに関する注意点  
 完全バージョンの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]を使用している場合は、次のことを考慮してください。  
  
- [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]Express Edition 以外のエディション:  
  
    - イメージの準備手順では、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Evaluation Edition を使用して製品バイナリがインストールされます。 インスタンスが完了すると、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のエディションはイメージの完了手順で入力した製品 ID に応じて決定されます。  
  
    - Evaluation Edition の製品 ID を入力すると、準備済みインスタンスの完了から 180 日で期限が切れるように評価期間が設定されます。  
  
- [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]Express Edition:  
  
    - [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Express Edition のインスタンスを準備するには、Express のインストール メディアを使用します。  
  
    - [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Express Edition の準備済みインスタンスには、製品 ID を指定できません。  
  
## <a name="supported-ssnoversion-installations"></a>サポートされる [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインストール  
[!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] の SysPrep では、ツールを含むすべての機能がサポートされます。  
  
[!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] またはそれ以前のバージョンをサイド バイ サイドでインストールするために、複数のインスタンスを準備できます。 これらのインスタンスの機能は、SysPrep をサポートしている必要があります。  
  
[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client は、イメージの準備手順の終了時に自動的にインストールされ、完了します。  
  
[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Browser と [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Writer は、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]インスタンスを準備すると自動的に準備され、 イメージの完了手順で [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンスを完了させると完了します。  
  
[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] でサポートされるエディションについては、「[SQL Server 2017 の各エディションとサポートされている機能](../../sql-server/editions-and-components-of-sql-server-2017.md)」および [SQL Server 2019](../../sql-server/editions-and-components-of-sql-server-version-15.md) に関するページを参照してください。  
  
準備済み [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]インスタンスの構成時に、エディションをアップグレードできます。 このオプションは [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Express Edition ではサポートされていません。  
  
[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)]以降では、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の SysPrep はコマンド ラインからの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] フェールオーバー クラスターのインストールをサポートしています。  
  
## <a name="ssnoversion-sysprep-limitations"></a>[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] SysPrep の制限事項  
準備済みインスタンスの修復はサポートされていません。 イメージの準備手順またはイメージの完了手順でセットアップが失敗した場合は、アンインストールを実行する必要があります。  
  
##  <a name="prepare-image"></a><a name="BKMK_PrepareImage"></a> イメージの準備  
イメージの準備手順では、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 製品と機能がインストールされますが、インストールの構成は行われません。  
  
インストールする [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の機能と [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 製品のインストール ファイルのインストール場所は、この手順で指定できます。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスを準備するには、 **[インストール センター]** の **[詳細設定]** ページにある **[SysPrep 配置のスタンドアロン インスタンスのイメージの準備]** を使用するか、コマンド プロンプトを使用します。  
  
- 同じコンピューター上で、後で完了できる [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の複数のインスタンスを準備できます。  
  
- [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]の既存の準備済みインスタンスに対して、SysPrep を使用したインストールでサポートされている機能を追加または削除できます。  
  
 インスタンスの準備が終了すると、 **[スタート]** メニューのショートカットから準備済み [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]インスタンスの構成を完了できるようになります。  
  
##  <a name="complete-image"></a><a name="BKMK_CompleteImage"></a> イメージの完了  
次のいずれかの方法を使用して、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の準備済みインスタンスを完了できます。  
  
- [スタート] メニューのショートカットを使用します。  
  
- **[インストール センター]** の **[詳細設定]** ページにある **[準備済みスタンドアロン インスタンスのイメージの完了]** をクリックします。  
  
## <a name="see-also"></a>参照  
[SQL Server のインストール計画](../../sql-server/install/planning-a-sql-server-installation.md)