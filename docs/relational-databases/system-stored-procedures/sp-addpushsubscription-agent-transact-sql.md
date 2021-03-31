---
description: sp_addpushsubscription_agent (Transact-SQL)
title: sp_addpushsubscription_agent (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/29/2021
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: replication
ms.topic: reference
f1_keywords:
- sp_addpushsubscription_agent_TSQL
- sp_addpushsubscription_agent
helpviewer_keywords:
- sp_addpushsubscription_agent
ms.assetid: 1fdd2052-50d8-4318-8aa7-fc635d5cad18
author: markingmyname
ms.author: maghan
ms.openlocfilehash: 81bf487607d0cfea9f7ab623e12d732e4463aded
ms.sourcegitcommit: 851f47e27512651f809540b77bfbd09e6ddb5362
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105937836"
---
# <a name="sp_addpushsubscription_agent-transact-sql"></a>sp_addpushsubscription_agent (Transact-SQL)
[!INCLUDE [SQL Server SQL MI](../../includes/applies-to-version/sql-asdbmi.md)]

  トランザクション パブリケーションに対してプッシュ サブスクリプションを同期するために使用する、新たにスケジュールされたエージェント ジョブを追加します。 このストアドプロシージャは、パブリッシャー側でパブリケーションデータベースに対して実行されます。  
  
> [!IMPORTANT]  
>  リモート ディストリビューターを使用するパブリッシャーを構成する場合は、 *job_login* および *job_password* を含むすべてのパラメーターに指定された値がディストリビューターにプレーン テキストとして送信されます。 このストアド プロシージャを実行する前に、パブリッシャーとリモート ディストリビューターの間の接続を暗号化する必要があります。 詳細については、「[データベース エンジンへの暗号化接続の有効化 &#40;SQL Server 構成マネージャー&#41;](../../database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine.md)」を参照してください。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```  
  
sp_addpushsubscription_agent [ @publication= ] 'publication'  
    [ , [ @subscriber = ] 'subscriber' ]  
    [ , [ @subscriber_db = ] 'subscriber_db' ]  
    [ , [ @subscriber_security_mode = ] subscriber_security_mode ]  
    [ , [ @subscriber_login = ] 'subscriber_login' ]  
    [ , [ @subscriber_password = ] 'subscriber_password' ]  
    [ , [ @job_login = ] 'job_login' ]   
    [ , [ @job_password = ] 'job_password' ]   
    [ , [ @job_name = ] 'job_name' ]   
    [ , [ @frequency_type = ] frequency_type ]  
    [ , [ @frequency_interval = ] frequency_interval ]  
    [ , [ @frequency_relative_interval = ] frequency_relative_interval ]  
    [ , [ @frequency_recurrence_factor = ] frequency_recurrence_factor ]  
    [ , [ @frequency_subday = ] frequency_subday ]  
    [ , [ @frequency_subday_interval = ] frequency_subday_interval ]  
    [ , [ @active_start_time_of_day = ] active_start_time_of_day ]  
    [ , [ @active_end_time_of_day = ] active_end_time_of_day ]  
    [ , [ @active_start_date = ] active_start_date ]  
    [ , [ @active_end_date = ] active_end_date ]  
    [ , [ @dts_package_name = ] 'dts_package_name' ]  
    [ , [ @dts_package_password = ] 'dts_package_password' ]  
    [ , [ @dts_package_location = ] 'dts_package_location' ]  
    [ , [ @enabled_for_syncmgr = ] 'enabled_for_syncmgr' ]  
    [ , [ @distribution_job_name = ] 'distribution_job_name' ]  
    [ , [ @publisher = ] 'publisher' ]  
    [ , [ @subscriber_provider = ] 'subscriber_provider' ]   
    [ , [ @subscriber_datasrc = ] 'subscriber_datasrc' ]   
    [ , [ @subscriber_location = ] 'subscriber_location' ]  
    [ , [ @subscriber_provider_string = ] 'subscriber_provider_string' ]   
    [ , [ @subscriber_catalog = ] 'subscriber_catalog' ]  
```  
  
## <a name="arguments"></a>引数  
`[ @publication = ] 'publication'` パブリケーションの名前を指定します。 *publication* は **sysname**,、既定値はありません。  
  
`[ @subscriber = ] 'subscriber'` サブスクライバーインスタンスの名前を指定します。または、サブスクライバーデータベースが可用性グループの場合は、AG リスナーの名前を指定します。 *サブスクライバー* の **sysname**,、既定値は NULL です。 

<!--SQL Server 2019 on Linux-->
::: moniker range=">= sql-server-linux-ver15 || >= sql-server-ver15 "

> [!NOTE]
> サーバー名はとして指定でき `<Hostname>,<PortNumber>` ます。 SQL Server が Linux または Windows でカスタムポートを使用して展開され、browser サービスが無効になっている場合は、接続のポート番号を指定する必要があります。 リモートディストリビューターにカスタムポート番号を使用するのは、SQL Server 2019 にのみ適用されます。

::: moniker-end

`[ @subscriber_db = ] 'subscriber_db'` サブスクリプションデータベースの名前を指定します。 *subscriber_db* は **sysname**,、既定値は NULL です。 SQL Server 以外のサブスクライバーの場合は、 *subscriber_db* の値 **(既定の転送先)** を指定します。  
  
`[ @subscriber_security_mode = ] subscriber_security_mode` 同期時にサブスクライバーに接続するときに使用するセキュリティモードを示します。 *subscriber_security_mode* は **int**,、既定値は1です。 **0** は認証を指定し [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。 **1** Windows 認証を指定します。  
  
> [!IMPORTANT]  
>  キュー更新サブスクリプションの場合、サブスクライバーへの接続には [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 認証を使用し、各サブスクライバーへの接続にはそれぞれ異なるアカウントを指定してください。 他のすべてのサブスクリプションでは、Windows 認証を使用します。  
  
`[ @subscriber_login = ] 'subscriber_login'` サブスクライバーに接続して同期するときに使用するサブスクライバーログインを示します。 *subscriber_login* は **sysname**,、既定値は NULL です。  
  
`[ @subscriber_password = ] 'subscriber_password'` サブスクライバーパスワードを入力します。 *subscriber_security_mode* が **0** に設定されている場合は *subscriber_password* が必要です。 *subscriber_password* は **sysname**,、既定値は NULL です。 サブスクライバーパスワードが使用されている場合は、自動的に暗号化されます。  
  
> [!IMPORTANT]  
>  空白のパスワードは使用しないでください。 強力なパスワードを使用してください。 可能であれば、実行時、ユーザーに対してセキュリティ資格情報の入力を要求します。 スクリプト ファイルに資格情報を格納する必要がある場合は、不正アクセスを防ぐために、ファイルを保護します。  
  
`[ @job_login = ] 'job_login'` エージェントを実行するアカウントのログインを指定します。 Azure SQL Managed Instance SQL Server アカウントを使用します。 *job_login* は **nvarchar (257)** で、既定値は NULL です。 この Windows アカウントは、エージェントがディストリビューターに接続するとき、および Windows 統合認証を使用してサブスクライバーに接続するときに必ず使用されます。  
  
`[ @job_password = ] 'job_password'` エージェントを実行するアカウントのパスワードを指定します。 *job_password* は **sysname** であり、既定値はありません。  
  
> [!IMPORTANT]  
>  可能であれば、実行時、ユーザーに対してセキュリティ資格情報の入力を要求します。 スクリプト ファイルに資格情報を格納する必要がある場合は、不正アクセスを防ぐために、ファイルを保護します。  
  
`[ @job_name = ] 'job_name'` 既存のエージェントジョブの名前を指定します。 *job_name* は **sysname** で、既定値は NULL です。 このパラメーターは、新しく作成されたジョブ (既定値) ではなく、既存のジョブを使用してサブスクリプションを同期する場合にのみ指定します。 **Sysadmin** 固定サーバーロールのメンバーでない場合は、 *job_name* を指定するときに *job_login* と *job_password* を指定する必要があります。  
  
`[ @frequency_type = ] frequency_type` ディストリビューションエージェントをスケジュールする頻度を指定します。 *frequency_type* は **int**,、値は次のいずれかを指定することができます。  
  
|Value|説明|  
|-----------|-----------------|  
|**1**|1 回|  
|**2**|オン デマンド|  
|**4**|毎日|  
|**8**|週次|  
|**16**|月単位|  
|**32**|月単位の相対|  
|**64** (既定値)|自動開始|  
|**128**|繰り返し|  
  
> [!NOTE]  
>  値 **64** を指定すると、ディストリビューションエージェントが連続モードで実行されます。 これは、エージェントの **-Continuous** パラメーターの設定に対応しています。 詳細については、「 [Replication Distribution Agent](../../relational-databases/replication/agents/replication-distribution-agent.md)」を参照してください。  
  
`[ @frequency_interval = ] frequency_interval`*Frequency_type* によって設定された頻度に適用する値を指定します。 *frequency_interval* は **int**,、既定値は1です。  
  
`[ @frequency_relative_interval = ] frequency_relative_interval` ディストリビューションエージェントの日付を指定します。 このパラメーターは、 *frequency_type* が **32** (月単位) に設定されている場合に使用されます。 *frequency_relative_interval* は **int**,、値は次のいずれかを指定することができます。  
  
|Value|説明|  
|-----------|-----------------|  
|**1** (既定値)|First|  
|**2**|Second|  
|**4**|Third|  
|**8**|4 番目|  
|**16**|末尾|  
  
`[ @frequency_recurrence_factor = ] frequency_recurrence_factor`*Frequency_type* によって使用される定期実行係数です。 *frequency_recurrence_factor* は **int**,、既定値は0です。  
  
`[ @frequency_subday = ] frequency_subday` 定義した期間中に再スケジュールする頻度を指定します。 *frequency_subday* は **int**,、値は次のいずれかを指定することができます。  
  
|Value|説明|  
|-----------|-----------------|  
|**1**|1 度|  
|**2**|Second|  
|**4** (既定値)|分|  
|**8**|時間|  
  
`[ @frequency_subday_interval = ] frequency_subday_interval`*Frequency_subday* の間隔を指定します。 *frequency_subday_interval* は **int**,、既定値は5です。  
  
`[ @active_start_time_of_day = ] active_start_time_of_day` ディストリビューションエージェントを最初にスケジュール設定する時刻を HHMMSS 形式で指定します。 *active_start_time_of_day* は **int**,、既定値は0です。  
  
`[ @active_end_time_of_day = ] active_end_time_of_day` ディストリビューションエージェントのスケジュール設定を停止する時刻を HHMMSS 形式で指定します。 *active_end_time_of_day* は **int**,、既定値は235959です。  
  
`[ @active_start_date = ] active_start_date` ディストリビューションエージェントを最初にスケジュール設定する日付を YYYYMMDD 形式で指定します。 *active_start_date* は **int**,、既定値は0です。  
  
`[ @active_end_date = ] active_end_date` ディストリビューションエージェントのスケジュール設定を停止する日付を YYYYMMDD 形式で指定します。 *active_end_date* は **int**,、既定値は99991231です。  
  
`[ @dts_package_name = ] 'dts_package_name'` データ変換サービス (DTS) パッケージの名前を指定します。 *dts_package_name* は **sysname** で、既定値は NULL です。 たとえば、というパッケージ名を指定する場合、パラメーターはになり `DTSPub_Package` `@dts_package_name = N'DTSPub_Package'` ます。  
  
`[ @dts_package_password = ] 'dts_package_password'` パッケージを実行するために必要なパスワードを指定します。 *dts_package_password* は **sysname** で、既定値は NULL です。  
  
> [!NOTE]  
>  *Dts_package_name* が指定されている場合は、パスワードを指定する必要があります。  
  
`[ @dts_package_location = ] 'dts_package_location'` パッケージの場所を指定します。 *dts_package_location* は **nvarchar (12)**,、既定値はディストリビューターです。 パッケージの場所には、 **ディストリビューター** または **サブスクライバー** を指定できます。  
  
`[ @enabled_for_syncmgr = ] 'enabled_for_syncmgr'` 同期マネージャーを使用してサブスクリプションを同期できるかどうかを指定 [!INCLUDE[msCoName](../../includes/msconame-md.md)] します。 *enabled_for_syncmgr* は **nvarchar (5)**,、既定値は FALSE です。 **False** の場合、サブスクリプションは同期マネージャーに登録されていません。 **True** の場合、サブスクリプションは同期マネージャーに登録され、を起動せずに同期でき [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] ます。  
  
`[ @distribution_job_name = ] 'distribution_job_name'` [!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]  
  
`[ @publisher = ] 'publisher'` パブリッシャーの名前を指定します。 *publisher* は **sysname** で、既定値は NULL です。  
  
`[ @subscriber_provider = ] 'subscriber_provider'` 以外のデータソースの OLE DB プロバイダーが登録されている一意のプログラム識別子 (PROGID) を指定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] します。 *subscriber_provider* は **sysname** で、既定値は NULL です。 *subscriber_provider* は、ディストリビューターにインストールされている OLE DB プロバイダーに対して一意である必要があります。 *subscriber_provider* は、以外のサブスクライバーに対してのみサポートされ [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。  
  
`[ @subscriber_datasrc = ] 'subscriber_datasrc'` OLE DB プロバイダーによって認識されるデータソースの名前を指定します。 *subscriber_datasrc* は **nvarchar (4000)**,、既定値は NULL です。 OLE DB プロバイダーを初期化するために、DBPROP_INIT_DATASOURCE プロパティとして *subscriber_datasrc* が渡されます。 *subscriber_datasrc* は、以外のサブスクライバーに対してのみサポートされ [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。  
  
`[ @subscriber_location = ] 'subscriber_location'` OLE DB プロバイダーによって認識されるデータベースの場所を指定します。 *subscriber_location* は **nvarchar (4000)**,、既定値は NULL です。 OLE DB プロバイダーを初期化するために、DBPROP_INIT_LOCATION プロパティとして *subscriber_location* が渡されます。 *subscriber_location* は、以外のサブスクライバーに対してのみサポートされ [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。  
  
`[ @subscriber_provider_string = ] 'subscriber_provider_string'` データソースを識別する OLE DB プロバイダー固有の接続文字列を指定します。 *subscriber_provider_string* は **nvarchar (4000)**,、既定値は NULL です。 *subscriber_provider_string* は、IDataInitialize に渡されるか、DBPROP_INIT_PROVIDERSTRING プロパティとして設定され、OLE DB プロバイダーが初期化されます。 *subscriber_provider_string* は、以外のサブスクライバーに対してのみサポートされ [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。  
  
`[ @subscriber_catalog = ] 'subscriber_catalog'` OLE DB プロバイダーに接続するときに使用するカタログを指定します。 *subscriber_catalog* は **sysname** で、既定値は NULL です。 OLE DB プロバイダーを初期化するために、DBPROP_INIT_CATALOG プロパティとして *subscriber_catalog* が渡されます。 *subscriber_catalog* は、以外のサブスクライバーに対してのみサポートされ [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。  
  
## <a name="return-code-values"></a>リターン コードの値  
 **0** (成功) または **1** (失敗)  
  
## <a name="remarks"></a>Remarks  
 **sp_addpushsubscription_agent** は、スナップショットレプリケーションおよびトランザクションレプリケーションで使用します。  
  
## <a name="example"></a>例  
 [!code-sql[HowTo#sp_addtranpushsubscription_agent](../../relational-databases/replication/codesnippet/tsql/sp-addpushsubscription-a_1.sql)]  
  
## <a name="permissions"></a>アクセス許可  
 **Sp_addpushsubscription_agent** を実行できるのは、固定サーバーロール **sysadmin** または固定データベースロール **db_owner** のメンバーだけです。  
  
## <a name="see-also"></a>参照  
 [ssSDSFull](../../relational-databases/replication/create-a-push-subscription.md)   
 [SQL Server 以外のサブスクライバーのサブスクリプションの作成](../../relational-databases/replication/create-a-subscription-for-a-non-sql-server-subscriber.md)   
 [Subscribe to Publications](../../relational-databases/replication/subscribe-to-publications.md)   
 [レプリケーション ストアド プロシージャ &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/replication-stored-procedures-transact-sql.md)   
 [sp_addsubscription &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-addsubscription-transact-sql.md)   
 [sp_changesubscription &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-changesubscription-transact-sql.md)   
 [sp_dropsubscription &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-dropsubscription-transact-sql.md)   
 [sp_helpsubscription &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-helpsubscription-transact-sql.md)  
  
  
