---
title: sp_addsubscription (Transact-sql) |Microsoft Docs
description: パブリケーションにサブスクリプションを追加し、サブスクライバーの状態を設定します。 このストアドプロシージャは、パブリッシャー側のパブリケーションデータベースで実行されます。
ms.date: 03/29/2021
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.custom: ''
ms.technology: replication
ms.topic: reference
f1_keywords:
- sp_addsubscription
- sp_addsubscription_TSQL
helpviewer_keywords:
- sp_addsubscription
ms.assetid: 61ddf287-1fa0-4c1a-8657-ced50cebf0e0
author: markingmyname
ms.author: maghan
ms.openlocfilehash: d7df5a0aa4fa8dedfc4da04bbf6c0991ecac4f8d
ms.sourcegitcommit: 851f47e27512651f809540b77bfbd09e6ddb5362
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105937838"
---
# <a name="sp_addsubscription-transact-sql"></a>sp_addsubscription (Transact-sql)
[!INCLUDE[sql-asdb](../../includes/applies-to-version/sql-asdb.md)]

  パブリケーションにサブスクリプションを追加し、サブスクライバーの状態を設定します。 このストアドプロシージャは、パブリッシャー側でパブリケーションデータベースに対して実行されます。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```  
  
sp_addsubscription [ @publication = ] 'publication'  
    [ , [ @article = ] 'article']  
    [ , [ @subscriber = ] 'subscriber' ]  
    [ , [ @destination_db = ] 'destination_db' ]  
        [ , [ @sync_type = ] 'sync_type' ]  
    [ , [ @status = ] 'status'  
        [ , [ @subscription_type = ] 'subscription_type' ]  
    [ , [ @update_mode = ] 'update_mode' ]  
    [ , [ @loopback_detection = ] 'loopback_detection' ]  
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
    [ , [ @optional_command_line = ] 'optional_command_line' ]  
    [ , [ @reserved = ] 'reserved' ]  
    [ , [ @enabled_for_syncmgr= ] 'enabled_for_syncmgr' ]  
    [ , [ @offloadagent= ] remote_agent_activation]  
    [ , [ @offloadserver= ] 'remote_agent_server_name' ]  
    [ , [ @dts_package_name= ] 'dts_package_name' ]  
    [ , [ @dts_package_password= ] 'dts_package_password' ]  
    [ , [ @dts_package_location= ] 'dts_package_location' ]  
    [ , [ @distribution_job_name= ] 'distribution_job_name' ]  
    [ , [ @publisher = ] 'publisher' ]  
    [ , [ @backupdevicetype = ] 'backupdevicetype' ]  
    [ , [ @backupdevicename = ] 'backupdevicename' ]  
    [ , [ @mediapassword = ] 'mediapassword' ]  
    [ , [ @password = ] 'password' ]  
    [ , [ @fileidhint = ] fileidhint ]  
    [ , [ @unload = ] unload ]  
    [ , [ @subscriptionlsn = ] subscriptionlsn ]  
    [ , [ @subscriptionstreams = ] subscriptionstreams ]  
    [ , [ @subscriber_type = ] subscriber_type ]  
    [ , [ @memory_optimized = ] memory_optimized ]  
```  
  
## <a name="arguments"></a>引数  
 [ @publication =] '*パブリケーション*'  
 パブリケーションの名前です。 *publication* は **sysname**,、既定値はありません。  
  
 [ @article =] '*アーティクル*'  
 パブリケーションがサブスクライブされるアーティクルを指定します。 *アーティクル* は **sysname** で、既定値は all です。 all に設定した場合は、そのパブリケーションのすべてのアーティクルに対してサブスクリプションが加えられます。 Oracle パブリッシャーの場合、サポートされている値は all と NULL だけです。  
  
 [ @subscriber =] '*サブスクライバー*'  
 サブスクライバーの名前です。 *サブスクライバー* の **sysname**,、既定値は NULL です。  

<!--SQL Server 2019 on Linux-->
::: moniker range=">= sql-server-linux-ver15 || >= sql-server-ver15 "

> [!NOTE]
> サーバー名はとして指定でき `<Hostname>,<PortNumber>` ます。 SQL Server が Linux または Windows でカスタムポートを使用して展開され、browser サービスが無効になっている場合は、接続のポート番号を指定する必要があります。 リモートディストリビューターにカスタムポート番号を使用するのは、SQL Server 2019 にのみ適用されます。

::: moniker-end
  
 [ @destination_db =] '*destination_db*'  
 レプリケートされたデータの格納先となるデータベースの名前を指定します。 *destination_db* は **sysname**,、既定値は NULL です。 NULL の場合、 *destination_db* はパブリケーションデータベースの名前に設定されます。 Oracle パブリッシャーの場合は、 *destination_db* を指定する必要があります。 SQL Server 以外のサブスクライバーの場合は、 *destination_db* の値 (既定の転送先) を指定します。  
  
 [ @sync_type =] '*sync_type*'  
 サブスクリプションの同期の種類を示します。 *sync_type* は **nvarchar (255)** で、次のいずれかの値を指定できます。  
  
|Value|説明|  
|-----------|-----------------|  
|なし|サブスクライバーには、パブリッシュされたテーブルのスキーマと初期データが既に存在します。<br /><br /> 注: このオプションは非推奨とされます。 代わりに replication support only を使用してください。|  
|automatic (既定値)|パブリッシュされたテーブルのスキーマと初期データは、最初にサブスクライバーに転送されます。|  
|replication support only|更新サブスクリプションをサポートするアーティクルのカスタム ストアド プロシージャとトリガーがサブスクライバー側で必要に応じて自動的に生成されます。 サブスクライバーがパブリッシュされたテーブルのスキーマと初期データを既に持っていることを前提としています。 ピアツーピアトランザクションレプリケーショントポロジを構成する場合は、トポロジ内のすべてのノードのデータが同一であることを確認します。 詳細については、「[ピア ツー ピア トランザクション レプリケーション](../../relational-databases/replication/transactional/peer-to-peer-transactional-replication.md)」を参照してください。<br /><br /> *SQL Server 以外のパブリケーションへのサブスクリプションではサポートされません。*|  
|バックアップを使用した初期化|パブリッシュされたテーブルのスキーマと初期データは、パブリケーション データベースのバックアップから取得されます。 サブスクライバーがパブリケーションデータベースのバックアップにアクセスできることを前提としています。 バックアップの場所とメディアの種類は、 *backupdevicename* と *backupdevicetype* によって指定されます。 このオプションを使用する場合、ピアツーピアトランザクションレプリケーショントポロジは、構成中に停止する必要はありません。<br /><br /> *SQL Server 以外のパブリケーションへのサブスクリプションではサポートされません。*|  
|lsn から初期化する|ピア ツー ピア トランザクション レプリケーション トポロジにノードを追加するときに使用します。 @subscriptionlsn と共に使用すると、関連するトランザクションのすべてが、新しいノードに確実にレプリケートされます。 サブスクライバーがパブリッシュされたテーブルのスキーマと初期データを既に持っていることを前提としています。 詳細については、「[ピア ツー ピア トランザクション レプリケーション](../../relational-databases/replication/transactional/peer-to-peer-transactional-replication.md)」を参照してください。|  
  
> [!NOTE]  
>  システム テーブルとデータは常に転送されます。  
  
 [ @status =] '*status*'  
 はサブスクリプションの状態です。 *status* の部分は **sysname** で、既定値は NULL です。 このパラメーターが明示的に設定されていない場合、レプリケーションによって、次のいずれかの値に自動的に設定されます。  
  
|Value|説明|  
|-----------|-----------------|  
|active|サブスクリプションが初期化され、変更を受け入れる準備ができました。 このオプションは、 *sync_type* の値が none、initialize with backup、または replication support only の場合に設定されます。|  
|subscribed|サブスクリプションを初期化する必要があります。 このオプションは、 *sync_type* の値が automatic の場合に設定されます。|  
  
 [ @subscription_type =] '*subscription_type*'  
 サブスクリプションの種類を示します。 *subscription_type* は **nvarchar (4)**,、既定値は push です。 push または pull に設定できます。 push サブスクリプションのディストリビューション エージェントはディストリビューター側にあり、pull サブスクリプションのディストリビューション エージェントはサブスクライバー側にあります。 *subscription_type* をプルして、パブリッシャーに知られている名前付きプルサブスクリプションを作成できます。 詳細については、「[パブリケーションのサブスクライブ](../../relational-databases/replication/subscribe-to-publications.md)」をご覧ください。  
  
> [!NOTE]  
>  匿名サブスクリプションでは、このストアドプロシージャを使用する必要はありません。  
  
 [ @update_mode =] '*update_mode*'  
 更新の種類を示します。*update_mode* は **nvarchar (30)** で、次のいずれかの値を指定できます。  
  
|Value|説明|  
|-----------|-----------------|  
|read only (既定値)|サブスクリプションは読み取り専用です。 サブスクライバーでの変更は、パブリッシャーに送信されません。|  
|sync tran|即時更新サブスクリプションのサポートを有効にします。 Oracle パブリッシャーではサポートされていません。|  
|キューに登録されたトランザクション|サブスクリプションのキュー更新を有効にします。 サブスクライバーでデータ変更を行い、キューに格納して、パブリッシャーに反映することができます。 Oracle パブリッシャーではサポートされていません。|  
|failover|キュー更新をフェールオーバーとするサブスクリプションの即時更新を有効にします。 サブスクライバーでデータを変更し、それを直ちにパブリッシャーに配信することができます。 パブリッシャーとサブスクライバーが接続されていない場合は、更新モードを変更することにより、サブスクライバーで加えられたデータの変更を、サブスクライバーとパブリッシャーが接続されるまで、キューに格納することができます。 Oracle パブリッシャーではサポートされていません。|  
|キューに置かれたフェールオーバー|即時更新モードへの変更が可能なキュー更新サブスクリプションとしてサブスクリプションを有効にします。 サブスクライバーでデータを変更し、サブスクライバーとパブリッシャーの間に接続が確立されるまで、キューに格納することができます。 連続接続が確立されると、更新モードを即時更新に変更できます。 Oracle パブリッシャーではサポートされていません。|  
  
 サブスクライブされるパブリケーションで DTS が許可されている場合、値 synctran および queued tran は使用できません。  
  
 [ @loopback_detection =] '*loopback_detection*'  
 ディストリビューションエージェントがサブスクライバーに送信されたトランザクションをサブスクライバーに戻すかどうかを指定します。 *loopback_detection* は **nvarchar (5)** で、次のいずれかの値を指定できます。  
  
|Value|説明|  
|-----------|-----------------|  
|true|ディストリビューションエージェントは、サブスクライバーで発生したトランザクションをサブスクライバーに送り返しません。 双方向トランザクション レプリケーションで使用されます。 詳細については、「 [Bidirectional Transactional Replication](../../relational-databases/replication/transactional/bidirectional-transactional-replication.md)」を参照してください。|  
|false|ディストリビューション エージェントは、サブスクライバーで発生したトランザクションをサブスクライバーに戻します。|  
|NULL (既定値)|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] サブスクライバーの場合は true、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 以外のサブスクライバーの場合は false に自動設定されます。|  
  
 [ @frequency_type =] *frequency_type*  
 ディストリビューションタスクをスケジュールする頻度を指定します。 *frequency_type* は int,、これらの値のいずれかを指定できます。  
  
|Value|説明|  
|-----------|-----------------|  
|1|1 回|  
|2|オン デマンド|  
|4|毎日|  
|8|週次|  
|16|月単位|  
|32|月単位の相対|  
|64 (既定値)|自動開始|  
|128|繰り返し|  
  
 [ @frequency_interval =] *frequency_interval*  
 *Frequency_type* によって設定された頻度に適用する値を指定します。 *frequency_interval* は **int**,、既定値は NULL です。  
  
 [ @frequency_relative_interval =] *frequency_relative_interval*  
 ディストリビューションエージェントの日付を指定します。 このパラメーターは、 *frequency_type* が 32 (月単位) に設定されている場合に使用されます。 *frequency_relative_interval* は **int**,、これらの値のいずれかを指定できます。  
  
|Value|説明|  
|-----------|-----------------|  
|1|First|  
|2|Second|  
|4|Third|  
|8|4 番目|  
|16|末尾|  
|NULL (既定値)||  
  
 [ @frequency_recurrence_factor =] *frequency_recurrence_factor*  
 *Frequency_type* によって使用される定期実行係数です。 *frequency_recurrence_factor* は **int**,、既定値は NULL です。  
  
 [ @frequency_subday =] *frequency_subday*  
 定義した期間にスケジュールを組み直す頻度を分単位で指定します。 *frequency_subday* は **int**,、これらの値のいずれかを指定できます。  
  
|Value|説明|  
|-----------|-----------------|  
|1|1 度|  
|2|Second|  
|4|分|  
|8|時間|  
|NULL||  
  
 [ @frequency_subday_interval =] *frequency_subday_interval*  
 *Frequency_subday* の間隔を指定します。 *frequency_subday_interval* は **int**,、既定値は NULL です。  
  
 [ @active_start_time_of_day =] *active_start_time_of_day*  
 ディストリビューションエージェントを最初にスケジュール設定する時刻を HHMMSS 形式で指定します。 *active_start_time_of_day* は **int**,、既定値は NULL です。  
  
 [ @active_end_time_of_day =] *active_end_time_of_day*  
 ディストリビューションエージェントのスケジュール設定を停止する時刻を HHMMSS 形式で指定します。 *active_end_time_of_day* は **int**,、既定値は NULL です。  
  
 [ @active_start_date =] *active_start_date*  
 ディストリビューションエージェントを最初にスケジュール設定する日付を YYYYMMDD 形式で指定します。 *active_start_date* は **int**,、既定値は NULL です。  
  
 [ @active_end_date =] *active_end_date*  
 ディストリビューション エージェントのスケジュール設定を停止する日付を YYYYMMDD 形式で指定します。 *active_end_date* は **int**,、既定値は NULL です。  
  
 [ @optional_command_line =] '*optional_command_line*'  
 実行するオプションのコマンドプロンプトを指定します。 *optional_command_line* は **nvarchar (4000)**,、既定値は NULL です。  
  
 [ @reserved =] '*reserved*'  
 [!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]  
  
 [ @enabled_for_syncmgr =] '*enabled_for_syncmgr*'  
 Windows 同期マネージャーを使用して、サブスクリプションを同期できるかどうかを指定 [!INCLUDE[msCoName](../../includes/msconame-md.md)] します。 *enabled_for_syncmgr* は **nvarchar (5)**,、既定値は FALSE です。 False の場合、サブスクリプションは Windows 同期マネージャーに登録されていません。 true の場合、サブスクリプションは Windows 同期マネージャーに登録され、[!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] を起動せずに同期させることができます。 Oracle パブリッシャーではサポートされていません。  
  
 [ @offloadagent =] '*remote_agent_activation*'  
 エージェントをリモートでアクティブ化できることを指定します。 *remote_agent_activation* の部分は **bit** で、既定値は0です。  
  
> [!NOTE]  
>  このパラメーターは非推奨とされており、スクリプトの旧バージョンとの互換性のためだけに保持されています。  
  
 [ @offloadserver =] '*remote_agent_server_name*'  
 リモートからのアクティブ化に使用するサーバーのネットワーク名を指定します。 *remote_agent_server_name* は **sysname**,、既定値は NULL です。  
  
 [ @dts_package_name =] '*dts_package_name*'  
 データ変換サービス (DTS) パッケージの名前を指定します。 *dts_package_name* は **sysname** で、既定値は NULL です。 たとえば、DTSPub_Package というパッケージを指定するには、パラメーターを `@dts_package_name = N'DTSPub_Package'` にする必要があります。 このパラメーターは、プッシュサブスクリプションで使用できます。 プル サブスクリプションに DTS パッケージ情報を追加するには、sp_addpullsubscription_agent を使用します。  
  
 [ @dts_package_password =] '*dts_package_password*'  
 パッケージのパスワードを指定します (存在する場合)。 *dts_package_password* は **sysname** で、既定値は NULL です。  
  
> [!NOTE]  
>  *Dts_package_name* が指定されている場合は、パスワードを指定する必要があります。  
  
 [ @dts_package_location =] '*dts_package_location*'  
 パッケージの場所を指定します。 *dts_package_location* は **nvarchar (12)**,、既定値はディストリビューターです。 パッケージの場所としては、distributor または subscriber を指定できます。  
  
 [ @distribution_job_name =] '*distribution_job_name*'  
 [!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]  
  
 [ @publisher =] '*パブリッシャー*'  
 以外のパブリッシャーを指定し [!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。 *publisher* は **sysname** で、既定値は NULL です。  
  
> [!NOTE]  
>  パブリッシャーに対して *パブリッシャー* を指定することはできません [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。  
  
 [ @backupdevicetype =] '*backupdevicetype*'  
 バックアップからサブスクライバーを初期化する際に使用するバックアップ デバイスの種類を指定します。 *backupdevicetype* は **nvarchar (20)**,、次の値のいずれかを指定することができます。  
  
|Value|説明|  
|-----------|-----------------|  
|logical (既定値)|バックアップデバイスは論理デバイスです。|  
|disk|バックアップデバイスはディスクドライブです。|  
|tape|バックアップデバイスがテープドライブである|  
  
 *backupdevicetype* は、 *sync_method* が initialize_with_backup に設定されている場合にのみ使用されます。  
  
 [ @backupdevicename =] '*backupdevicename*'  
 バックアップからサブスクライバーを初期化する際に使用するデバイスの名前を指定します。 *backupdevicename* は **nvarchar (1000)**,、既定値は NULL です。  
  
 [ @mediapassword =] '*mediapassword*'  
 メディア セットのパスワードを指定します (メディアをフォーマットしたときにパスワードを設定した場合)。 *mediapassword* は **sysname**,、既定値は NULL です。  
  
> [!NOTE]  
>  [!INCLUDE[ssNoteDepFutureAvoid](../../includes/ssnotedepfutureavoid-md.md)]  
  
 [ @password =] '*パスワード*'  
 バックアップのパスワードを指定します (バックアップを作成したときにパスワードを設定した場合)。 *パスワード* は **sysname**,、既定値は NULL です。  
  
 [ @fileidhint =] *fileidhint*  
 復元するバックアップセットの序数値を識別します。 *fileidhint* は **int**,、既定値は NULL です。  
  
 [ @unload =] *アンロード*  
 バックアップからの初期化が完了した後テープ バックアップ デバイスをアンロードするかどうかを指定します。 *unload* は **ビット**,、既定値は1です。 1は、テープをアンロードすることを指定します。 *unload* は、 *backupdevicetype* が tape の場合にのみ使用されます。  
  
 [ @subscriptionlsn =] *subscriptionlsn*  
 サブスクリプションがピアツーピアトランザクションレプリケーショントポロジ内のノードへの変更の配信を開始するログシーケンス番号 (LSN) を指定します。 値を initialize from lsn と共に使用し @sync_type て、関連するすべてのトランザクションが新しいノードに確実にレプリケートされるようにします。 詳細については、「[ピア ツー ピア トランザクション レプリケーション](../../relational-databases/replication/transactional/peer-to-peer-transactional-replication.md)」を参照してください。  
  
 [ @subscriptionstreams =] *subscriptionstreams*  
 単一のスレッドを使用しているときに、トランザクションに関連したさまざまな特性を維持しながら、サブスクライバーに対してバッチ変更を適用することのできる、ディストリビューション エージェントあたりの接続数です。 *subscriptionstreams* は **tinyint**,、既定値は NULL です。 サポートされている値の範囲は 1 ～ 64 です。 このパラメーターは、以外の [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] サブスクライバー、Oracle パブリッシャー、またはピアツーピアサブスクリプションではサポートされていません。 サブスクリプション ストリームが使用されるたびに、msreplication_subscriptions テーブルに行が追加され (ストリームごとに 1 行)、agent_id が NULL に設定されます。  
  
> [!NOTE]  
>  Subscriptionstreams は、 [!INCLUDE[tsql](../../includes/tsql-md.md)]を渡すように構成されたアーティクルでは使用できません。 subscriptionstreams を使用するには、代わりにストアド プロシージャの呼び出しを渡すようにアーティクルを構成します。  
  
 [ @subscriber_type =] *subscriber_type*  
 サブスクライバーの種類を示します。 *subscriber_type* は **tinyint** で、次のいずれかの値を指定できます。  
  
|Value|説明|  
|-----------|-----------------|  
|0 (既定値)|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] サブスクライバ|  
|1|ODBC データソースサーバー|  
|2|[!INCLUDE[msCoName](../../includes/msconame-md.md)] Jet データベース|  
|3|OLE DB プロバイダー|  
  
 [ @memory_optimized =] *memory_optimized*  
 サブスクリプションがメモリ最適化テーブルをサポートしていることを示します。 *memory_optimized* は **ビット** です。1は true (サブスクリプションはメモリ最適化テーブルをサポートします) です。  
  
## <a name="return-code-values"></a>リターン コードの値  
 0 (成功) または 1 (失敗)  
  
## <a name="remarks"></a>解説  
 sp_addsubscription は、スナップショット レプリケーションおよびトランザクション レプリケーションで使用します。  
  
 sp_addsubscription が固定サーバー ロール sysadmin のメンバーによってプッシュ サブスクリプションを作成するために実行されると、ディストリビューション エージェントのジョブが暗黙的に作成され、SQL Server エージェント サービス アカウントで実行されます。 とには [sp_addpushsubscription_agent](../../relational-databases/system-stored-procedures/sp-addpushsubscription-agent-transact-sql.md) を実行し、エージェント固有の別の Windows アカウントの資格情報を指定することをお勧めし @job_login @job_password ます。 詳細については、「 [レプリケーション エージェント セキュリティ モデル](../../relational-databases/replication/security/replication-agent-security-model.md)」を参照してください。  
  
 sp_addsubscription によって、ODBC および OLE DB サブスクライバーが次のパブリケーションにアクセスできなくなります。  
  
-   は、 [sp_addpublication](../../relational-databases/system-stored-procedures/sp-addpublication-transact-sql.md)への呼び出しでネイティブ *sync_method* を使用して作成されました。  
  
-   *Pre_creation_cmd* パラメーターの値が 3 (truncate) の [sp_addarticle](../../relational-databases/system-stored-procedures/sp-addarticle-transact-sql.md)ストアドプロシージャを使用してパブリケーションに追加されたアーティクルを格納します。  
  
-   同期処理に *update_mode* を設定しようとしました。  
  
-   パラメーター化されたステートメントを使用するように構成されたアーティクルを含むパブリケーション。  
  
 さらに、パブリケーションの *allow_queued_tran* オプションが true に設定されている場合 (パブリッシャーで適用できるようになるまでサブスクライバーで変更をキューに登録できる)、アーティクル内の timestamp 列は **timestamp** としてスクリプト化され、その列での変更はサブスクライバーに送信されます。 サブスクライバーは、timestamp 列の値を生成して更新します。 ODBC または OLE DB サブスクライバーの場合、 *allow_queued_tran* が true に設定されているパブリケーションと、timestamp 列を含むアーティクルをサブスクライブしようとすると、sp_addsubscription は失敗します。  
  
 サブスクリプションが DTS パッケージを使用していない場合、 *allow_transformable_subscriptions* に設定されているパブリケーションをサブスクライブすることはできません。 パブリケーションのテーブルを DTS サブスクリプションと非 DTS サブスクリプションの両方にレプリケートする必要がある場合は、サブスクリプションの種類ごとに1つずつ、2つの個別のパブリケーションを作成する必要があります。  
  
 **sync_type** オプション *replication support only*、 *initialize with backup*、または *initialize from lsn* を選択した場合、 **sp_addsubscription** の実行後にログ リーダー エージェントを実行して、スクリプトの設定がディストリビューション データベースに書き込まれるようにする必要があります。 ログ リーダー エージェントが、 **sysadmin** 固定サーバー ロールのメンバーであるアカウントで実行されている必要があります。 **sync_type** オプションが *Automatic* に設定されている場合、特別なログ リーダー エージェントの操作は必要ありません。  
  
## <a name="permissions"></a>アクセス許可  
 sp_addsubscription を実行できるのは、固定サーバー ロール sysadmin または固定データベース ロール db_owner のメンバーだけです。 プル サブスクリプションの場合、パブリケーションのアクセス リストにログインのあるユーザーは sp_addsubscription を実行できます。  
  
## <a name="example"></a>例  
 [!code-sql[HowTo#sp_addtranpushsubscription_agent](../../relational-databases/replication/codesnippet/tsql/sp-addsubscription-trans_1.sql)]  
  
## <a name="see-also"></a>参照  
 [ssSDSFull](../../relational-databases/replication/create-a-push-subscription.md)   
 [SQL Server 以外のサブスクライバーのサブスクリプションの作成](../../relational-databases/replication/create-a-subscription-for-a-non-sql-server-subscriber.md)   
 [Subscribe to Publications](../../relational-databases/replication/subscribe-to-publications.md)   
 [sp_addpushsubscription_agent &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-addpushsubscription-agent-transact-sql.md)   
 [sp_changesubstatus &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-changesubstatus-transact-sql.md)   
 [sp_dropsubscription &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-dropsubscription-transact-sql.md)   
 [sp_helpsubscription &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-helpsubscription-transact-sql.md)   
 [システム ストアド プロシージャ &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/system-stored-procedures-transact-sql.md)  
  
  
