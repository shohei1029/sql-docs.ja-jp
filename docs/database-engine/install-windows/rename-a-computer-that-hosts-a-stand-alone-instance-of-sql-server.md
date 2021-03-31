---
title: インスタンスをホストするコンピューターの名前変更
description: SQL Server のインスタンスをホストするコンピューターの名前を変更する場合は、sys.servers に格納されているシステム メタデータを更新します。
ms.custom: seo-lt-2019
ms.date: 12/13/2019
ms.prod: sql
ms.reviewer: ''
ms.technology: install
ms.topic: conceptual
helpviewer_keywords:
- remote login errors [SQL Server]
- standalone computer names [SQL Server]
- names [SQL Server], standalone instances of SQL Server
- renaming standalone instances of SQL Server
- sysservers system table
- removing remote logins
- deleting remote logins
- dropping remote logins
ms.assetid: bbaf1445-b8a2-4ebf-babe-17d8cf20b037
author: cawrites
ms.author: chadam
monikerRange: '>=sql-server-2016'
ms.openlocfilehash: debdeffa2409642c01f9abdc502a897d6ea3dac3
ms.sourcegitcommit: 2f971c85d87623c0aed1612406130d840e7bdb2e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "105744458"
---
# <a name="rename-a-computer-that-hosts-a-stand-alone-instance-of-sql-server"></a>SQL Server のスタンドアロン インスタンスをホストするコンピューターの名前変更

[!INCLUDE [SQL Server -Windows Only](../../includes/applies-to-version/sql-windows-only.md)]

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]を実行するコンピューターの名前を変更した場合、変更後の名前は [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の起動時に認識されます。 コンピューター名を再設定するためにセットアップを再度実行する必要はありません。 代わりに次の手順を実行して、sys.servers に格納され、システム関数 @@SERVERNAME でレポートされるシステム メタデータを更新します。 @@SERVERNAME を使用するか、sys.servers からサーバー名のクエリを実行するリモート接続およびリモート アプリケーションのコンピューター名の変更を反映するには、システム メタデータを更新します。  
  
ここで示す手順を実行しても、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]のインスタンスの名前を変更することはできません。 変更できるのは、インスタンス名のうち、コンピューター名に対応する部分のみです。 たとえば、Instance1 という [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスをホストする MB1 というコンピューターを、MB2 のような別の名前に変更できます。 ただし、名前のインスタンスの部分 (Instance1) は変更されません。 この例では、 \\\\*ComputerName*\\*InstanceName* が、 \\\MB1\Instance1 から \\\MB2\Instance1 に変更されます。  
  
 **Before you begin**  
  
 名前変更のプロセスを開始する前に、次の情報を確認します。  
  
-   [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスが [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] フェールオーバー クラスターに含まれている場合、コンピューターの名前変更のプロセスは、スタンドアロン インスタンスをホストするコンピューターとは異なります。 詳細については、「[SQL Server のフェールオーバー クラスター インスタンスの名前変更](../../sql-server/failover-clusters/install/rename-a-sql-server-failover-cluster-instance.md)」を参照してください。
  
-   [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] レプリケーションでログ配布を使用する場合を除き、レプリケーションに関連するコンピューターの名前は変更できません。 プライマリ コンピューターが完全に存在しなくなった場合は、ログ配布のセカンダリ コンピューターの名前を変更できます。 詳細については、[ログ配布とレプリケーション &#40;SQL Server&#41;](../../database-engine/log-shipping/log-shipping-and-replication-sql-server.md) を参照してください。  
  
-   [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] を使用するように構成されたコンピューターの名前を変更すると、コンピューター名の変更後、[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] を使用できないことがあります。 詳細については、「 [Server Web Service の名前の変更](../../reporting-services/report-server/rename-a-report-server-computer.md)」を参照してください。  
  
-   データベース ミラーリングを使用する構成のコンピューターの名前を変更するときは、名前変更操作の前にデータベース ミラーリングを無効にする必要があります。 次に、新しいコンピューター名でデータベース ミラーリングを再確立します。 データベース ミラーリングのメタデータは、新しいコンピューター名に自動的には更新されません。 次の手順を実行してシステム メタデータを更新します。  
  
-   コンピューター名へのハードコード参照を使用する Windows グループをとおして [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] に接続するユーザーは、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]に接続できなくなることがあります。 この状況は、名前を変更した場合に、Windows グループで変更前のコンピューター名が指定されていると発生する可能性があります。 このような Windows グループが名前変更の後でも [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 接続を確立できるようにするには、新しいコンピューター名を指定するように Windows グループを更新します。  
  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] を再起動した後、新しいコンピューター名を使用して [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]に接続できます。 @@SERVERNAME がローカル サーバー インスタンスの更新後の名前を返すことを確認するには、シナリオに応じて次のプロシージャを手動で実行する必要があります。 使用するプロシージャは、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]の既定のインスタンスと名前付きインスタンスのどちらをホストするコンピューターを更新しているかによって決まります。  
  
## <a name="rename-a-computer-that-hosts-a-stand-alone-instance-of-ssnoversion"></a>[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のスタンドアロン インスタンスをホストするコンピューターの名前を変更する  
  
-   [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]の既定のインスタンスをホストする名前変更されたコンピューターの場合は、次のプロシージャを実行します。  
  
    ```sql
    EXEC sp_dropserver '<old_name>';  
    GO  
    EXEC sp_addserver '<new_name>', local;  
    GO  
    ```  
  
     [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]のインスタンスを再起動します。  
  
-   [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]の名前付きインスタンスをホストする名前変更されたコンピューターの場合は、次のプロシージャを実行します。  
  
    ```sql
    EXEC sp_dropserver '<old_name\instancename>';  
    GO  
    EXEC sp_addserver '<new_name\instancename>', local;  
    GO  
    ```  
  
     [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]のインスタンスを再起動します。  
  
## <a name="after-the-renaming-operation"></a>名前変更操作の後  
 コンピューターの名前を変更した場合は、変更前のコンピューター名を使用している接続はすべて、変更後の名前で接続する必要があります。  
  
## <a name="verify-renaming-operation"></a>名前変更操作を確認する  
  
-   @@SERVERNAME または sys.servers で情報を確認できます。 @@SERVERNAME 関数では新しい名前が返されます。sys.servers テーブルには新しい名前が表示されます。 次は @@SERVERNAME の使用例です。  
  
    ```  
    SELECT @@SERVERNAME AS 'Server Name';  
    ```  
  
## <a name="additional-considerations"></a>その他の注意点  
 **リモート ログイン** - コンピューターでリモート ログインを行っている場合に、 **sp_dropserver** を実行すると次のようなエラーが発生することがあります:  
  
 `Server: Msg 15190, Level 16, State 1, Procedure sp_dropserver, Line 44 There are still remote logins for the server 'SERVER1'.`  
  
 このエラーを解決するには、このサーバーに対するリモート ログインを削除する必要があります。  
  
### <a name="drop-remote-logins"></a>リモート ログインを削除する  
  
-   既定のインスタンスの場合は、次のプロシージャを実行します。  
  
    ```sql
    EXEC sp_dropremotelogin old_name;  
    GO  
    ```  
  
-   名前付きインスタンスの場合は、次のプロシージャを実行します。  
  
    ```sql
    EXEC sp_dropremotelogin old_name\instancename;  
    GO  
    ```  
  
 **リンク サーバー構成** - リンク サーバー構成はコンピューター名の変更操作の影響を受けます。 **sp_addlinkedserver** または **sp_setnetname** を使用してコンピューターの名前参照を更新します。 詳細については、「[sp_addlinkedserver &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-addlinkedserver-transact-sql.md)」または「[sp_setnetname &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-setnetname-transact-sql.md)」を参照してください。  
  
 **クライアントのエイリアス** - 名前付きパイプを使用するクライアントのエイリアスはコンピューター名の変更操作の影響を受けます。 たとえば、SRVR1 に対するエイリアス "PROD_SRVR" が作成され、名前付きパイプのプロトコルが使用されている場合、パイプ名は `\\SRVR1\pipe\sql\query`のようになります。 コンピューター名が変更された後は、名前付きパイプのパスは無効になります。 名前付きパイプの詳細については、「 [名前付きパイプを使用した有効な接続文字列の作成](/previous-versions/sql/sql-server-2008/ms189307(v=sql.100))」を参照してください。  
  
## <a name="see-also"></a>関連項目  
 [SQL Server のインストール](../../database-engine/install-windows/install-sql-server.md)  
  
