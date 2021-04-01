---
description: sp_addpullsubscription_agent (Transact-SQL)
title: sp_addpullsubscription_agent (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/29/2021
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: replication
ms.topic: reference
f1_keywords:
- sp_addpullsubscription_agent
- sp_addpullsubscription_agent_TSQL
helpviewer_keywords:
- sp_addpullsubscription_agent
ms.assetid: b9c2eaed-6d2d-4b78-ae9b-73633133180b
author: markingmyname
ms.author: maghan
ms.openlocfilehash: 8bf3e90f4f1b360a45427241cc2da480765d4da5
ms.sourcegitcommit: 851f47e27512651f809540b77bfbd09e6ddb5362
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105937811"
---
# <a name="sp_addpullsubscription_agent-transact-sql"></a>sp_addpullsubscription_agent (Transact-SQL)
[!INCLUDE [SQL Server SQL MI](../../includes/applies-to-version/sql-asdbmi.md)]
 
  トランザクション パブリケーションに対するプル サブスクリプションの同期で使用される、スケジュールされたエージェント ジョブを追加します。 このストアドプロシージャは、サブスクライバー側のサブスクリプションデータベースで実行されます。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```  
  
sp_addpullsubscription_agent [ @publisher = ] 'publisher'  
    [ , [ @publisher_db = ] 'publisher_db' ]          , [ @publication = ] 'publication'  
    [ , [ @subscriber = ] 'subscriber' ]  
    [ , [ @subscriber_db = ] 'subscriber_db' ]  
    [ , [ @subscriber_security_mode = ] subscriber_security_mode ]  
    [ , [ @subscriber_login = ] 'subscriber_login' ]  
    [ , [ @subscriber_password = ] 'subscriber_password' ]  
    [ , [ @distributor = ] 'distributor' ]  
    [ , [ @distribution_db = ] 'distribution_db' ]  
    [ , [ @distributor_security_mode = ] distributor_security_mode ]  
    [ , [ @distributor_login = ] 'distributor_login' ]  
    [ , [ @distributor_password = ] 'distributor_password' ]  
    [ , [ @optional_command_line = ] 'optional_command_line' ]  
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
    [ , [ @distribution_jobid = ] distribution_jobid OUTPUT ]  
    [ , [ @encrypted_distributor_password = ] encrypted_distributor_password ]  
    [ , [ @enabled_for_syncmgr = ] 'enabled_for_syncmgr' ]  
    [ , [ @ftp_address = ] 'ftp_address' ]  
    [ , [ @ftp_port = ] ftp_port ]  
    [ , [ @ftp_login = ] 'ftp_login' ]  
    [ , [ @ftp_password = ] 'ftp_password' ]  
    [ , [ @alt_snapshot_folder = ] 'alternate_snapshot_folder' ]  
    [ , [ @working_directory = ] 'working_directory' ]  
    [ , [ @use_ftp = ] 'use_ftp' ]  
    [ , [ @publication_type = ] publication_type ]  
    [ , [ @dts_package_name = ] 'dts_package_name' ]  
    [ , [ @dts_package_password = ] 'dts_package_password' ]  
    [ , [ @dts_package_location = ] 'dts_package_location' ]  
    [ , [ @reserved = ] 'reserved' ]  
    [ , [ @offloadagent = ] 'remote_agent_activation' ]  
    [ , [ @offloadserver = ] 'remote_agent_server_name']  
    [ , [ @job_name = ] 'job_name' ]  
    [ , [ @job_login = ] 'job_login' ]   
    [ , [ @job_password = ] 'job_password' ]   
```  
  
## <a name="arguments"></a>引数  
`[ @publisher = ] 'publisher'` パブリッシャーの名前を指定します。 *publisher* は **sysname** で、既定値はありません。  

<!--SQL Server 2019 on Linux-->
::: moniker range=">= sql-server-linux-ver15 || >= sql-server-ver15 "

> [!NOTE]
> サーバー名はとして指定でき `<Hostname>,<PortNumber>` ます。 SQL Server が Linux または Windows でカスタムポートを使用して展開され、browser サービスが無効になっている場合は、接続のポート番号を指定する必要があります。 リモートディストリビューターにカスタムポート番号を使用するのは、SQL Server 2019 にのみ適用されます。

::: moniker-end
  
`[ @publisher_db = ] 'publisher_db'_` パブリッシャーデータベースの名前を指定します。 *publisher_db* は **sysname** で、既定値は NULL です。 *publisher_db* は、Oracle パブリッシャーでは無視されます。  
  
`[ @publication = ] 'publication'` パブリケーションの名前を指定します。 *publication* は **sysname**,、既定値はありません。  
  
`[ @subscriber = ] 'subscriber'` サブスクライバーインスタンスの名前を指定します。または、サブスクライバーデータベースが可用性グループ内にある場合は、AG リスナーの名前を指定します。 *サブスクライバー* の **sysname**,、既定値は NULL です。  
  
> [!NOTE]  
>  このパラメーターは非推奨とされており、スクリプトの旧バージョンとの互換性のために保持されています。  
  
`[ @subscriber_db = ] 'subscriber_db'` サブスクリプションデータベースの名前を指定します。 *subscriber_db* は **sysname**,、既定値は NULL です。  
  
> [!NOTE]  
>  このパラメーターは非推奨とされており、スクリプトの旧バージョンとの互換性のために保持されています。  
  
`[ @subscriber_security_mode = ] subscriber_security_mode` 同期時にサブスクライバーに接続するときに使用するセキュリティモードを示します。 *subscriber_security_mode* は **int,、** 既定値は NULL です。 **0** は認証を指定し [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。 **1** Windows 認証を指定します。  
  
> [!NOTE]  
>  このパラメーターは非推奨とされており、スクリプトの旧バージョンとの互換性のために保持されています。 ディストリビューションエージェントは、常に Windows 認証を使用してローカルサブスクライバーに接続します。 このパラメーターに NULL または **1** 以外の値が指定されている場合は、警告メッセージが返されます。  
  
`[ @subscriber_login = ] 'subscriber_login'` サブスクライバーに接続して同期するときに使用するサブスクライバーログインを示します。*subscriber_login* は **sysname**,、既定値は NULL です。  
  
> [!NOTE]  
>  このパラメーターは非推奨とされており、スクリプトの旧バージョンとの互換性のために保持されています。 このパラメーターに値が指定されている場合は、警告メッセージが返されますが、値は無視されます。  
  
`[ @subscriber_password = ] 'subscriber_password'` サブスクライバーパスワードを入力します。 *subscriber_security_mode* が **0** に設定されている場合は *subscriber_password* が必要です。 *subscriber_password* は **sysname**,、既定値は NULL です。 サブスクライバーパスワードが使用されている場合は、自動的に暗号化されます。  
  
> [!NOTE]  
>  このパラメーターは非推奨とされており、スクリプトの旧バージョンとの互換性のために保持されています。 このパラメーターに値が指定されている場合は、警告メッセージが返されますが、値は無視されます。  
  
`[ @distributor = ] 'distributor'` ディストリビューターの名前を指定します。 *ディストリビューター* は **sysname** で、既定値は *publisher* によって指定された値です。  
  
`[ @distribution_db = ] 'distribution_db'` ディストリビューションデータベースの名前を指定します。 *distribution_db* は **sysname** で、既定値は NULL です。  
  
`[ @distributor_security_mode = ] distributor_security_mode` は、同期時にディストリビューターに接続するときに使用するセキュリティモードです。 *distributor_security_mode* は **int**,、既定値は **1** です。 **0** は認証を指定し [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。 **1** Windows 認証を指定します。  
  
> [!IMPORTANT]  
>  [!INCLUDE[ssNoteWinAuthentication](../../includes/ssnotewinauthentication-md.md)]  
  
`[ @distributor_login = ] 'distributor_login'` ディストリビューターへの接続時に、同期時に使用するディストリビューターログインを示します。 *distributor_security_mode* が **0** に設定されている場合は *distributor_login* が必要です。 *distributor_login* は **sysname**,、既定値は NULL です。  
  
`[ @distributor_password = ] 'distributor_password'` ディストリビューターパスワードを入力します。 *distributor_security_mode* が **0** に設定されている場合は *distributor_password* が必要です。 *distributor_password* は **sysname**,、既定値は NULL です。  
  
> [!IMPORTANT]  
>  空白のパスワードは使用しないでください。 強力なパスワードを使用してください。 可能であれば、実行時、ユーザーに対してセキュリティ資格情報の入力を要求します。 スクリプト ファイルに資格情報を格納する必要がある場合は、不正アクセスを防ぐために、ファイルを保護します。  
  
`[ @optional_command_line = ] 'optional_command_line'` ディストリビューションエージェントに指定された省略可能なコマンドプロンプトです。 たとえば、 **-definitionfile** C:\Distdef.txt または **-commitbatchsize** 10 のようになります。 *optional_command_line* は **nvarchar (4000)**,、既定値は空の文字列です。  
  
`[ @frequency_type = ] frequency_type` ディストリビューションエージェントをスケジュールする頻度を指定します。 *frequency_type* は **int**,、値は次のいずれかを指定することができます。  
  
|値|説明|  
|-----------|-----------------|  
|**1**|1 回|  
|**2** (既定値)|オン デマンド|  
|**4**|毎日|  
|**8**|週次|  
|**16**|月単位|  
|**32**|月単位の相対|  
|**64**|自動開始|  
|**128**|繰り返し|  
  
> [!NOTE]  
>  値 **64** を指定すると、ディストリビューションエージェントが連続モードで実行されます。 これは、エージェントの **-Continuous** パラメーターの設定に対応しています。 詳細については、「 [Replication Distribution Agent](../../relational-databases/replication/agents/replication-distribution-agent.md)」を参照してください。  
  
`[ @frequency_interval = ] frequency_interval`*Frequency_type* によって設定された頻度に適用する値を指定します。 *frequency_interval* は **int**,、既定値は1です。  
  
`[ @frequency_relative_interval = ] frequency_relative_interval` ディストリビューションエージェントの日付を指定します。 このパラメーターは、 *frequency_type* が **32** (月単位) に設定されている場合に使用されます。 *frequency_relative_interval* は **int**,、値は次のいずれかを指定することができます。  
  
|値|説明|  
|-----------|-----------------|  
|**1** (既定値)|First|  
|**2**|Second|  
|**4**|Third|  
|**8**|4 番目|  
|**16**|末尾|  
  
`[ @frequency_recurrence_factor = ] frequency_recurrence_factor`*Frequency_type* によって使用される定期実行係数です。 *frequency_recurrence_factor* は **int**,、既定値は **1** です。  
  
`[ @frequency_subday = ] frequency_subday` 定義した期間中に再スケジュールする頻度を指定します。 *frequency_subday* は **int**,、値は次のいずれかを指定することができます。  
  
|値|説明|  
|-----------|-----------------|  
|**1** (既定値)|1 度|  
|**2**|Second|  
|**4**|分|  
|**8**|時間|  
  
`[ @frequency_subday_interval = ] frequency_subday_interval`*Frequency_subday* の間隔を指定します。 *frequency_subday_interval* は **int**,、既定値は **1** です。  
  
`[ @active_start_time_of_day = ] active_start_time_of_day` ディストリビューションエージェントを最初にスケジュール設定する時刻を HHMMSS 形式で指定します。 *active_start_time_of_day* は **int**,、既定値は **0** です。  
  
`[ @active_end_time_of_day = ] active_end_time_of_day` ディストリビューションエージェントのスケジュール設定を停止する時刻を HHMMSS 形式で指定します。 *active_end_time_of_day* は **int**,、既定値は **0** です。  
  
`[ @active_start_date = ] active_start_date` ディストリビューションエージェントを最初にスケジュール設定する日付を YYYYMMDD 形式で指定します。 *active_start_date* は **int**,、既定値は **0** です。  
  
`[ @active_end_date = ] active_end_date` ディストリビューションエージェントのスケジュール設定を停止する日付を YYYYMMDD 形式で指定します。 *active_end_date* は **int**,、既定値は **0** です。  
  
`[ @distribution_jobid = ] _distribution_jobidOUTPUT` このジョブのディストリビューションエージェントの ID を示します。 *distribution_jobid* は **バイナリ (16)** で、既定値は NULL です。これは出力パラメーターです。  
  
`[ @encrypted_distributor_password = ] encrypted_distributor_password`*Encrypted_distributor_password* の設定はサポートされなくなりました。 この **ビット** パラメーターを **1** に設定しようとすると、エラーが発生します。  
  
`[ @enabled_for_syncmgr = ] 'enabled_for_syncmgr'` 同期マネージャーを使用してサブスクリプションを同期できるかどうかを指定 [!INCLUDE[msCoName](../../includes/msconame-md.md)] します。 *enabled_for_syncmgr* は **nvarchar (5)**,、既定値は FALSE です。 **False** の場合、サブスクリプションは同期マネージャーに登録されていません。 **True** の場合、サブスクリプションは同期マネージャーに登録され、を起動せずに同期でき [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] ます。  
  
`[ @ftp_address = ] 'ftp_address'` 旧バージョンとの互換性のためにのみ使用します。  
  
`[ @ftp_port = ] ftp_port` 旧バージョンとの互換性のためにのみ使用します。  
  
`[ @ftp_login = ] 'ftp_login'` 旧バージョンとの互換性のためにのみ使用します。  
  
`[ @ftp_password = ] 'ftp_password'` 旧バージョンとの互換性のためにのみ使用します。  
  
`[ @alt_snapshot_folder = ] 'alternate_snapshot_folder'_` スナップショットの代替フォルダーの場所を指定します。 *alternate_snapshot_folder* は **nvarchar (255)**,、既定値は NULL です。  
  
`[ @working_directory = ] 'working_director'` パブリケーションのデータとスキーマファイルを格納するために使用する作業ディレクトリの名前を指定します。 *working_directory* は **nvarchar (255)**,、既定値は NULL です。 名前は UNC 形式で指定する必要があります。  
  
`[ @use_ftp = ] 'use_ftp'` スナップショットを取得するために通常のプロトコルではなく FTP を使用することを指定します。 *use_ftp* は **nvarchar (5)**,、既定値は FALSE です。  
  
`[ @publication_type = ] publication_type` パブリケーションのレプリケーションの種類を指定します。 *publication_type* は **tinyint** で、既定値は **0** です。 **0** の場合、パブリケーションはトランザクションの種類です。 **1** の場合、パブリケーションはスナップショットの種類です。 **2** の場合、publication はマージの種類です。  
  
`[ @dts_package_name = ] 'dts_package_name'` DTS パッケージの名前を指定します。 *dts_package_name* は **sysname** で、既定値は NULL です。 たとえば、`DTSPub_Package` というパッケージを指定するには、パラメーターを `@dts_package_name = N'DTSPub_Package'` と設定します。  
  
`[ @dts_package_password = ] 'dts_package_password'` パッケージのパスワードを指定します (存在する場合)。 *dts_package_password* は **sysname** で、既定値は NULL です。これは、パスワードがパッケージに含まれていないことを意味します。  
  
> [!NOTE]  
>  *Dts_package_name* が指定されている場合は、パスワードを指定する必要があります。  
  
`[ @dts_package_location = ] 'dts_package_location'` パッケージの場所を指定します。 *dts_package_location* は **nvarchar (12)**,、既定値は **サブスクライバー** です。 パッケージの場所には、 **ディストリビューター** または **サブスクライバー** を指定できます。  
  
`[ @reserved = ] 'reserved'` [!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]  
  
`[ @offloadagent = ] 'remote_agent_activation'`
 > [!NOTE]  
>  リモートエージェントのアクティブ化は非推奨とされており、サポートされなくなりました。 このパラメーターは、スクリプトの旧バージョンとの互換性を維持するためにのみサポートされています。 *Remote_agent_activation* を **false** 以外の値に設定すると、エラーが発生します。  
  
`[ @offloadserver = ] 'remote_agent_server_name'`
 > [!NOTE]  
>  リモートエージェントのアクティブ化は非推奨とされており、サポートされなくなりました。 このパラメーターは、スクリプトの旧バージョンとの互換性を維持するためにのみサポートされています。 *Remote_agent_server_name* を NULL 以外の値に設定すると、エラーが発生します。  
  
`[ @job_name = ] 'job_name'` 既存のエージェントジョブの名前を指定します。 *job_name* は **sysname** で、既定値は NULL です。 このパラメーターは、新しく作成されたジョブ (既定値) ではなく、既存のジョブを使用してサブスクリプションを同期する場合にのみ指定します。 **Sysadmin** 固定サーバーロールのメンバーでない場合は、 *job_name* を指定するときに *job_login* と *job_password* を指定する必要があります。  
  
`[ @job_login = ] 'job_login'` エージェントを実行する Windows アカウントのログインを指定します。 *job_login* は **nvarchar (257)**,、既定値はありません。 この Windows アカウントはサブスクライバーへのエージェント接続で常に使用されます。  
  
`[ @job_password = ] 'job_password'` エージェントを実行する Windows アカウントのパスワードを指定します。 *job_password* は **sysname** であり、既定値はありません。  
  
> [!IMPORTANT]  
>  可能であれば、実行時、ユーザーに対してセキュリティ資格情報の入力を要求します。 スクリプト ファイルに資格情報を格納する必要がある場合は、不正アクセスを防ぐために、ファイルを保護します。  
  
## <a name="return-code-values"></a>リターン コードの値  
 **0** (成功) または **1** (失敗)  
  
## <a name="remarks"></a>Remarks  
 **sp_addpullsubscription_agent** は、スナップショットレプリケーションおよびトランザクションレプリケーションで使用します。  
  
## <a name="example"></a>例  
 [!code-sql[HowTo#sp_addtranpullsubscriptionagent](../../relational-databases/replication/codesnippet/tsql/sp-addpullsubscription-a_1.sql)]  
  
## <a name="permissions"></a>アクセス許可  
 **Sp_addpullsubscription_agent** を実行できるのは、固定サーバーロール **sysadmin** または固定データベースロール **db_owner** のメンバーだけです。  
  
## <a name="see-also"></a>参照  
 [Create a Pull Subscription](../../relational-databases/replication/create-a-pull-subscription.md)   
 [Subscribe to Publications](../../relational-databases/replication/subscribe-to-publications.md)   
 [sp_addpullsubscription &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-addpullsubscription-transact-sql.md)   
 [sp_change_subscription_properties &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-change-subscription-properties-transact-sql.md)   
 [sp_droppullsubscription &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-droppullsubscription-transact-sql.md)   
 [sp_helppullsubscription &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-helppullsubscription-transact-sql.md)   
 [sp_helpsubscription_properties &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-helpsubscription-properties-transact-sql.md)  
  
  
