---
title: user connections サーバー構成オプションの構成 | Microsoft Docs
description: "\"user connections\" オプションについて説明します。 このオプションを使用することにより、コンカレント接続数が多すぎて SQL Server のインスタンスが過負荷になるのを回避する方法について説明します。"
ms.custom: ''
ms.date: 03/02/2017
ms.prod: sql
ms.prod_service: high-availability
ms.reviewer: ''
ms.technology: configuration
ms.topic: conceptual
helpviewer_keywords:
- simultaneous connections [SQL Server]
- user connections option [SQL Server]
- users [SQL Server], simultaneous connections
- maximum number of simultaneous user connections
- connections [SQL Server], simultaneous
ms.assetid: 53beee6e-59fe-4276-9abb-8f1cec2a3508
author: markingmyname
ms.author: maghan
ms.openlocfilehash: c5a6e6b2a47151a967980b6e75766bdec39e88ae
ms.sourcegitcommit: 00af0b6448ba58e3685530f40bc622453d3545ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104673657"
---
# <a name="configure-the-user-connections-server-configuration-option"></a>user connections サーバー構成オプションの構成
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  このトピックでは、 **で** または [!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] を使用して、 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] user connections [!INCLUDE[tsql](../../includes/tsql-md.md)]サーバー構成オプションを構成する方法について説明します。 **user connections** オプションは、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]のインスタンスで許可される同時ユーザー接続の最大数を指定します。 許可される実際のユーザー接続数は、使用している [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のバージョンと、使用しているアプリケーションおよびハードウェアの制限によって異なります。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、最大 32,767 個のユーザー接続を確立できます。 **User Connections** はサーバー自身が構成する動的なオプションなので、ユーザー接続の最大数は必要に応じて自動的に [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] によって最大許容範囲内で調整されます。 たとえば、10 人のユーザーがログインしている場合、10 個のユーザー接続オブジェクトが割り当てられます。 ほとんどの場合は、このオプションの値を変更する必要はありません。 既定は 0 で、最大数 (32,767) のユーザー接続を許可することを示します。  
  
 システムで許可されるユーザー接続の最大数を決定するには、 [sp_configure](../../relational-databases/system-stored-procedures/sp-configure-transact-sql.md) を使用するか、または [sys.configuration](../../relational-databases/system-catalog-views/sys-configurations-transact-sql.md) カタログ ビューに対してクエリを実行します。  
  
 **このトピックの内容**  
  
-   **作業を開始する準備:**  
  
     [Recommendations (推奨事項)](#Recommendations)  
  
     [Security](#Security)  
  
-   **以下を使用して user connections オプションを構成するには:**  
  
     [SQL Server Management Studio](#SSMSProcedure)  
  
     [Transact-SQL](#TsqlProcedure)  
  
-   **補足情報:** [user connections オプションを構成した後](#FollowUp)  
  
##  <a name="before-you-begin"></a><a name="BeforeYouBegin"></a> はじめに  
  
###  <a name="recommendations"></a><a name="Recommendations"></a> 推奨事項  
  
-   このオプションは詳細設定オプションであるため、熟練したデータベース管理者または認定された [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] プロフェッショナルだけが変更するようにしてください。  
  
-   **User Connections** オプションを使用すると、コンカレント接続数が多すぎてサーバーが過負荷になるのを防ぐことができます。 接続数は、システムやユーザーの必要条件に基づいて予測できます。 たとえば、ユーザー数が多いシステムの場合、通常はどのユーザーにも専用の接続が必要なわけではありません。 接続は、ユーザー間で共有できます。 OLE DB アプリケーションを実行しているユーザーの場合は、開いている接続オブジェクトごとに 1 つの接続が必要であり、ODBC (Open Database Connectivity) アプリケーションを実行しているユーザーの場合は、アプリケーション内のアクティブな接続ハンドルごとに 1 つの接続が必要です。また、DB-Library アプリケーションを実行しているユーザーの場合は、DB-Library の **dbopen** 関数を呼び出すプロセスが開始されるたびに 1 つの接続が必要です。  
  
    > [!IMPORTANT]  
    >  このオプションを使用する必要がある場合、値を大きくしすぎないでください。1 つ接続すると、その接続が使用中であるかどうかに関係なくオーバーヘッドが生じます。 ユーザー接続の最大数を超えると、エラー メッセージが表示され、別の接続が使用可能になるまで接続できなくなります。  
  
###  <a name="security"></a><a name="Security"></a> セキュリティ  
  
####  <a name="permissions"></a><a name="Permissions"></a> Permissions  
 パラメーターなしで、または最初のパラメーターだけを指定して **sp_configure** を実行する権限は、既定ですべてのユーザーに付与されます。 両方のパラメーターを指定して **sp_configure** を実行し構成オプションを変更したり RECONFIGURE ステートメントを実行したりするには、ALTER SETTINGS サーバーレベル権限がユーザーに付与されている必要があります。 ALTER SETTINGS 権限は、 **sysadmin** 固定サーバー ロールと **serveradmin** 固定サーバー ロールでは暗黙のうちに付与されています。  
  
##  <a name="using-sql-server-management-studio"></a><a name="SSMSProcedure"></a> SQL Server Management Studio の使用  
  
#### <a name="to-configure-the-user-connections-option"></a>user connections オプションを構成するには  
  
1.  オブジェクト エクスプローラーで、サーバーを右クリックして **[プロパティ]** をクリックします。  
  
2.  **[接続]** ノードをクリックします。  
  
3.  **[接続]** の **[コンカレント接続の最大数]** ボックスで 0 から 32767 までの値を入力するか選択し、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]のインスタンスに同時に接続できるユーザーの最大数を指定します。  
  
4.  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]を再起動します。  
  
##  <a name="using-transact-sql"></a><a name="TsqlProcedure"></a> Transact-SQL の使用  
  
#### <a name="to-configure-the-user-connections-option"></a>user connections オプションを構成するには  
  
1.  [!INCLUDE[ssDE](../../includes/ssde-md.md)]に接続します。  
  
2.  [標準] ツール バーの **[新しいクエリ]** をクリックします。  
  
3.  次の例をコピーしてクエリ ウィンドウに貼り付け、 **[実行]** をクリックします。 この例では、 [sp_configure](../../relational-databases/system-stored-procedures/sp-configure-transact-sql.md) を使用して `user connections` オプションの値を `325` ユーザーに設定する方法を示します。  
  
```sql  
USE AdventureWorks2012 ;  
GO  
EXEC sp_configure 'show advanced options', 1;  
GO  
RECONFIGURE ;  
GO  
EXEC sp_configure 'user connections', 325 ;  
GO  
RECONFIGURE;  
GO  
  
```  
  
 詳細については、「 [サーバー構成オプション &#40;SQL Server&#41;](../../database-engine/configure-windows/server-configuration-options-sql-server.md)」を参照してください。  
  
##  <a name="follow-up-after-you-configure-the-user-connections-option"></a><a name="FollowUp"></a>補足情報: user connections オプションを構成した後  
 設定を有効にするには、SQL インスタンスを再起動する必要があります。  
  
## <a name="see-also"></a>参照  
 [RECONFIGURE &#40;Transact-SQL&#41;](../../t-sql/language-elements/reconfigure-transact-sql.md)   
 [サーバー構成オプション &#40;SQL Server&#41;](../../database-engine/configure-windows/server-configuration-options-sql-server.md)   
 [sp_configure &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-configure-transact-sql.md)  
  
  
