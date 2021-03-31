---
description: sp_adddistributor (Transact-sql)
title: sp_adddistributor (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/29/2021
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: replication
ms.topic: reference
f1_keywords:
- sp_adddistributor
- sp_adddistributor_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sp_adddistributor
ms.assetid: 35415502-68d0-40f6-993c-180e50004f1e
author: mashamsft
ms.author: mathoma
ms.openlocfilehash: a6a47480afbb16cd37bd260ac20c97d2b532165a
ms.sourcegitcommit: 851f47e27512651f809540b77bfbd09e6ddb5362
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105937803"
---
# <a name="sp_adddistributor-transact-sql"></a>sp_adddistributor (Transact-sql)
[!INCLUDE [SQL Server SQL MI](../../includes/applies-to-version/sql-asdbmi.md)]

  [sys.sysservers](../../relational-databases/system-compatibility-views/sys-sysservers-transact-sql.md)テーブルにエントリを作成します (存在しない場合)。は、サーバーエントリをディストリビューターとしてマークし、プロパティ情報を格納します。 このストアドプロシージャは、ディストリビューター側で master データベースに対して実行され、サーバーをディストリビューターとして登録してマークします。 リモート ディストリビューターの場合、このストアド プロシージャは、master データベースからパブリッシャー側でも実行され、リモート ディストリビューターを登録します。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```  
  
sp_adddistributor [ @distributor= ] 'distributor'   
    [ , [ @heartbeat_interval= ] heartbeat_interval ]   
    [ , [ @password= ] 'password' ]   
    [ , [ @from_scripting= ] from_scripting ]  
```  
  
## <a name="arguments"></a>引数  
`[ @distributor = ] 'distributor'` ディストリビューションサーバーの名前を指定します。 *ディストリビューター* は **sysname**,、既定値はありません。 このパラメーターは、リモート ディストリビューターを設定する場合にのみ使用します。 このメソッドは、ディストリビューターのプロパティのエントリを msdb. に追加します。 **MSdistributor** テーブル。  

<!--SQL Server 2019 on Linux-->
::: moniker range=">= sql-server-linux-ver15 || >= sql-server-ver15 "

> [!NOTE]
> サーバー名はとして指定でき `<Hostname>,<PortNumber>` ます。 SQL Server が Linux または Windows でカスタムポートを使用して展開され、browser サービスが無効になっている場合は、接続のポート番号を指定する必要があります。 リモートディストリビューターにカスタムポート番号を使用するのは、SQL Server 2019 にのみ適用されます。

::: moniker-end

`[ @heartbeat_interval = ] heartbeat_interval` エージェントが進行状況メッセージをログに記録しなくてもよい最大時間を分単位で指定します。 *heartbeat_interval* は **int**,、既定値は10分です。 この間隔で実行され、実行されているレプリケーションエージェントの状態を確認する SQL Server エージェントジョブが作成されます。  
  
`[ @password = ] 'password']`**Distributor_admin** ログインのパスワードを入力します。 *パスワード* は **sysname**,、既定値は NULL です。 NULL または空の文字列の場合、パスワードはランダムな値にリセットされます。 パスワードは最初のリモート ディストリビューターを追加するときに構成する必要があります。 **distributor_admin** ログインと *パスワード* は、ローカル接続を含む *ディストリビューター* RPC 接続に使用されるリンクサーバーエントリ用に格納されます。 *ディストリビューター* がローカルの場合、 **distributor_admin** のパスワードは新しい値に設定されます。 リモートディストリビューターを持つパブリッシャーの場合、パブリッシャーとディストリビューターの両方で **sp_adddistributor** を実行するときに、 *password* に同じ値を指定する必要があります。 [sp_changedistributor_password](../../relational-databases/system-stored-procedures/sp-changedistributor-password-transact-sql.md) を使用して、ディストリビューターのパスワードを変更できます。  
  
> [!IMPORTANT]  
>  可能であれば、実行時、ユーザーに対してセキュリティ資格情報の入力を要求します。 スクリプト ファイルに資格情報を格納する必要がある場合は、不正アクセスを防ぐために、ファイルを保護します。  
  
`[ @from_scripting = ] from_scripting` [!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]  
  
## <a name="return-code-values"></a>リターン コードの値  
 0 (成功) または 1 (失敗)  
  
## <a name="remarks"></a>解説  
 **sp_adddistributor** は、スナップショットレプリケーション、トランザクションレプリケーション、およびマージレプリケーションで使用します。  
  
## <a name="example"></a>例  
 [!code-sql[HowTo#AddDistPub](../../relational-databases/replication/codesnippet/tsql/sp-adddistributor-transa_1.sql)]  
  
## <a name="permissions"></a>アクセス許可  
 **Sp_adddistributor** を実行できるのは、 **sysadmin** 固定サーバーロールのメンバーだけです。  
  
## <a name="see-also"></a>参照  
 [パブリッシングとディストリビューションの構成](../../relational-databases/replication/configure-publishing-and-distribution.md)   
 [sp_changedistributor_property &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-changedistributor-property-transact-sql.md)   
 [sp_dropdistributor &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-dropdistributor-transact-sql.md)   
 [sp_helpdistributor &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-helpdistributor-transact-sql.md)   
 [システム ストアド プロシージャ &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/system-stored-procedures-transact-sql.md)   
 [[ディストリビューションの構成]](../../relational-databases/replication/configure-distribution.md)  
  
  
