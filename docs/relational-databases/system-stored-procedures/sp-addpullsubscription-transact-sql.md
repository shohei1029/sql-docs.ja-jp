---
description: sp_addpullsubscription (Transact-SQL)
title: sp_addpullsubscription (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/29/2021
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: replication
ms.topic: reference
f1_keywords:
- sp_addpullsubscription
- sp_addpullsubscription_TSQL
helpviewer_keywords:
- sp_addpullsubscription
ms.assetid: 0f4bbedc-0c1c-414a-b82a-6fd47f0a6a7f
author: markingmyname
ms.author: maghan
ms.openlocfilehash: 4c8ca5e48718ab2d1abbb5aff9071955ccb78b18
ms.sourcegitcommit: 851f47e27512651f809540b77bfbd09e6ddb5362
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105937815"
---
# <a name="sp_addpullsubscription-transact-sql"></a>sp_addpullsubscription (Transact-SQL)
[!INCLUDE [SQL Server SQL MI](../../includes/applies-to-version/sql-asdbmi.md)]

  スナップショット パブリケーションまたはトランザクション パブリケーションにプル サブスクリプションを追加します。 このストアド プロシージャは、プル サブスクリプションが作成されるデータベース上のサブスクライバー側で実行されます。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```  
  
sp_addpullsubscription [ @publisher= ] 'publisher'  
    [ , [ @publisher_db= ] 'publisher_db' ]  
        , [ @publication= ] 'publication'  
    [ , [ @independent_agent= ] 'independent_agent' ]  
    [ , [ @subscription_type= ] 'subscription_type' ]  
    [ , [ @description= ] 'description' ]  
    [ , [ @update_mode= ] 'update_mode' ]  
    [ , [ @immediate_sync = ] immediate_sync ]  
```  
  
## <a name="arguments"></a>引数  
`[ @publisher = ] 'publisher'` パブリッシャーの名前を指定します。 *publisher* は **sysname** で、既定値はありません。  

<!--SQL Server 2019 on Linux-->
::: moniker range=">= sql-server-linux-ver15 || >= sql-server-ver15 "

> [!NOTE]
> サーバー名はとして指定でき `<Hostname>,<PortNumber>` ます。 SQL Server が Linux または Windows でカスタムポートを使用して展開され、browser サービスが無効になっている場合は、接続のポート番号を指定する必要があります。 リモートディストリビューターにカスタムポート番号を使用するのは、SQL Server 2019 にのみ適用されます。

::: moniker-end
  
`[ @publisher_db = ] 'publisher_db'` パブリッシャーデータベースの名前を指定します。 *publisher_db* は **sysname**,、既定値は NULL です。 *publisher_db* は、Oracle パブリッシャーでは無視されます。  
  
`[ @publication = ] 'publication'` パブリケーションの名前を指定します。 *publication* は **sysname**,、既定値はありません。  
  
`[ @independent_agent = ] 'independent_agent'` このパブリケーションに対してスタンドアロンのディストリビューションエージェントがあるかどうかを指定します。 *independent_agent* は **nvarchar (5)**,、既定値は TRUE です。 **True** の場合、このパブリケーションにはスタンドアロンのディストリビューションエージェントがあります。 **False** の場合、パブリッシャーデータベース/サブスクライバーデータベースのペアごとに1つのディストリビューションエージェントが存在します。 *independent_agent* はパブリケーションのプロパティであり、パブリッシャー側と同じ値である必要があります。  
  
`[ @subscription_type = ] 'subscription_type'` サブスクリプションの種類を示します。 *subscription_type* は **nvarchar (9)**,、既定値は **anonymous** です。 パブリッシャーでサブスクリプションを登録せずにサブスクリプションを作成する場合を除き、 *subscription_type* には **pull** の値を指定する必要があります。 この場合、 **匿名** の値を指定する必要があります。 これは、サブスクリプションの構成時にパブリッシャーに [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 接続を確立できない場合に必要です。  
  
`[ @description = ] 'description'` パブリケーションの説明を示します。 *説明* は **nvarchar (100)**,、既定値は NULL です。  
  
`[ @update_mode = ] 'update_mode'` 更新の種類を示します。 *update_mode* は **nvarchar (30)** で、次のいずれかの値を指定できます。  
  
|[値]|説明|  
|-----------|-----------------|  
|**読み取り専用** (既定値)|サブスクリプションは読み取り専用です。 サブスクライバーでの変更は、パブリッシャーに送り返されません。 サブスクライバーで更新が行われない場合は、を使用する必要があります。|  
|**synctran**|即時更新サブスクリプションのサポートを有効にします。|  
|**キューに登録されたトランザクション**|サブスクリプションのキュー更新を有効にします。 サブスクライバーでデータ変更を行い、キューに格納して、パブリッシャーに反映することができます。|  
|**用**|キュー更新をフェールオーバーとするサブスクリプションの即時更新を有効にします。 サブスクライバーでデータを変更し、それを直ちにパブリッシャーに配信することができます。 パブリッシャーとサブスクライバーが接続されていない場合は、サブスクライバーとパブリッシャーが接続されるまで、サブスクライバーで加えられたデータの変更をキューに格納することができます。|  
|**キューに置かれたフェールオーバー**|即時更新モードへの変更が可能なキュー更新サブスクリプションとしてサブスクリプションを有効にします。 サブスクライバーでデータを変更し、サブスクライバーとパブリッシャーの間に接続が確立されるまで、キューに格納することができます。 連続接続が確立されると、更新モードを即時更新に変更できます。 *Oracle パブリッシャーではサポートされていません*。|  
  
`[ @immediate_sync = ] immediate_sync` スナップショットエージェントを実行するたびに同期ファイルを作成または再作成するかどうかを指定します。 *immediate_sync* は **ビット** の既定値は1であり、 **sp_addpublication** で *immediate_sync* と同じ値に設定する必要があります。*immediate_sync* はパブリケーションのプロパティであり、パブリッシャー側と同じ値である必要があります。  
  
## <a name="return-code-values"></a>リターン コードの値  
 **0** (成功) または **1** (失敗)  
  
## <a name="remarks"></a>Remarks  
 **sp_addpullsubscription** は、スナップショットレプリケーションおよびトランザクションレプリケーションで使用します。  
  
> [!IMPORTANT]  
>  キュー更新サブスクリプションの場合、サブスクライバーへの接続には [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 認証を使用し、各サブスクライバーへの接続にはそれぞれ異なるアカウントを指定してください。 キュー更新をサポートするプル サブスクリプションを作成する場合は、レプリケーションによって、常に Windows 認証を使用するように接続が設定されます (プル サブスクリプションでは、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 認証を使用するために必要な、サブスクライバー側のメタデータにレプリケーションからアクセスすることはできません)。 この場合は、 [sp_changesubscription](../../relational-databases/system-stored-procedures/sp-changesubscription-transact-sql.md) を実行し [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] て、サブスクリプションの構成後に認証を使用するように接続を変更する必要があります。  
  
 [MSreplication_subscriptions &#40;transact-sql&#41;](../../relational-databases/system-tables/msreplication-subscriptions-transact-sql.md)テーブルがサブスクライバーに存在しない場合は、 **sp_addpullsubscription** 作成します。 また、 [MSreplication_subscriptions &#40;transact-sql&#41;](../../relational-databases/system-tables/msreplication-subscriptions-transact-sql.md) テーブルに行を追加します。 プルサブスクリプションの場合は、最初にパブリッシャーで [sp_addsubscription &#40;transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-addsubscription-transact-sql.md) を呼び出す必要があります。  
  
## <a name="example"></a>例  
 [!code-sql[HowTo#sp_addtranpullsubscriptionagent](../../relational-databases/replication/codesnippet/tsql/sp-addpullsubscription-t_1.sql)]  
  
## <a name="permissions"></a>アクセス許可  
 **Sp_addpullsubscription** を実行できるのは、固定サーバーロール **sysadmin** または固定データベースロール **db_owner** のメンバーだけです。  
  
## <a name="see-also"></a>参照  
 [Create a Pull Subscription](../../relational-databases/replication/create-a-pull-subscription.md)   
 [トランザクションパブリケーションに対する更新可能なサブスクリプションを作成](../../relational-databases/replication/publish/create-an-updatable-subscription-to-a-transactional-publication.md)[するパブリケーションをサブスクライブ](../../relational-databases/replication/subscribe-to-publications.md)する   
 [sp_addpullsubscription_agent &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-addpullsubscription-agent-transact-sql.md)   
 [sp_change_subscription_properties &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-change-subscription-properties-transact-sql.md)   
 [sp_droppullsubscription &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-droppullsubscription-transact-sql.md)   
 [sp_helppullsubscription &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-helppullsubscription-transact-sql.md)   
 [sp_helpsubscription_properties &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-helpsubscription-properties-transact-sql.md)   
 [システム ストアド プロシージャ &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/system-stored-procedures-transact-sql.md)  
  
  
