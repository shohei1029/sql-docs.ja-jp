---
description: SQL Server エージェントの構成
title: SQL Server エージェントの構成
ms.prod: sql
ms.prod_service: sql-tools
ms.technology: ssms
ms.topic: conceptual
helpviewer_keywords:
- SQL Server Agent, configuring
- accounts [SQL Server], SQL Server Agent
- SQL Server Agent, permissions
- security [SQL Server], SQL Server Agent
ms.assetid: 2e361a62-9e92-4fcd-80d7-d6960f127900
author: markingmyname
ms.author: maghan
ms.reviewer: ''
ms.custom: seo-lt-2019
ms.date: 01/19/2017
monikerRange: = azuresqldb-mi-current || >= sql-server-2016
ms.openlocfilehash: 1de20e6f195c9ad38a66eeec53f21c64cafb6cbf
ms.sourcegitcommit: c09ef164007879a904a376eb508004985ba06cf0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104890770"
---
# <a name="configure-sql-server-agent"></a>SQL Server エージェントの構成

[!INCLUDE [SQL Server SQL MI](../../includes/applies-to-version/sql-asdbmi.md)]

> [!IMPORTANT]  
> 現在、[Azure SQL Managed Instance](/azure/sql-database/sql-database-managed-instance) によって、すべてではありませんが、ほとんどの SQL Server エージェントの機能がサポートされています。 SQL Managed Instance では現在、SQL Server エージェントの有効化/無効化はサポートされていません。 SQL エージェントは常に実行されています。 詳細については、[SQL Managed Instance T-SQL と SQL Server の相違点](/azure/sql-database/sql-database-managed-instance-transact-sql-information#sql-server-agent)に関する記事を参照してください。

このトピックでは、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインストール中に [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]エージェントのいくつかの構成オプションを指定する方法について説明します。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エージェントの構成オプションの完全なセットは、 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)]、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 管理オブジェクト (SMO)、または [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エージェント ストアド プロシージャでのみ使用できます。
  
## <a name="before-you-begin"></a><a name="BeforeYouBegin"></a>開始する前に
  
### <a name="limitations-and-restrictions"></a><a name="Restrictions"></a>制限事項と制約事項
  
-   ジョブ、オペレーター、警告、および [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エージェント サービスを管理するには、[!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] のオブジェクト エクスプローラーで **[SQL Server エージェント]** を選択します。 ただし、オブジェクト エクスプローラーに [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エージェント ノードが表示されるのは、このノードの使用権限がある場合に限られます。
  
-   フェールオーバー クラスター インスタンスの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] サービスまたは [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エージェント サービスでは自動再起動を有効にしないでください。
  
### <a name="security"></a><a name="Security"></a>セキュリティ
  
#### <a name="permissions"></a><a name="Permissions"></a>アクセス許可
[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エージェントの機能を実行するには、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]固定サーバー ロールの **sysadmin** のメンバーであるアカウントの資格情報を使用するように構成する必要があります。 このアカウントには、次の Windows 権限が必要です。  
  
-   サービスとしてログオン (SeServiceLogonRight)
  
-   プロセス レベル トークンを置き換える (SeAssignPrimaryTokenPrivilege)
  
-   スキャン チェックを行わない (SeChangeNotifyPrivilege)
  
-   プロセスに対してメモリ クォータを調整する (SeIncreaseQuotaPrivilege)
  
[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エージェント サービス アカウントに必要な Windows 権限の詳細については、「 [SQL Server エージェント サービスのアカウントの選択](../../ssms/agent/select-an-account-for-the-sql-server-agent-service.md) 」および「 [Windows サービス アカウントと権限の構成](../../database-engine/configure-windows/configure-windows-service-accounts-and-permissions.md)」を参照してください。
  
## <a name="to-configure-sql-server-agent"></a>SQL Server エージェントを構成するには

1. **[スタート]** ボタンを選択します。次に、 **[スタート]** メニューの **[コントロール パネル]** を選択します。  
2. [コントロール パネル] で、 **[システムとセキュリティ]** を選択し、 **[管理ツール]** を選択します。次に **[ローカル セキュリティ ポリシー]** を選択します。
3. [ローカル セキュリティ ポリシー] で、シェブロンを選択して **[ローカル ポリシー]** フォルダーを展開します。次に **[ユーザー権利の割り当て]** フォルダーを選択します。
4. [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] での使用のために構成する権限を右クリックし、 **[プロパティ]** を選択します。
5. 権限のプロパティ ダイアログ ボックスで、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エージェントの実行に使用するアカウントが表示されていることを確認します。 表示されていない場合は、 **[ユーザーまたはグループの追加]** を選択し、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エージェントの実行に使用するアカウントを **[ユーザー、コンピューター、サービス アカウント、またはグループの選択]** ダイアログ ボックスに入力してから、 **[OK]** を選択します。
6. [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エージェントで実行するために追加するそれぞれの権限に対して、この操作を繰り返します。 終わったら、 **[OK]** を選択します。
