---
description: sp_adddistpublisher (Transact-SQL)
title: sp_adddistpublisher (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/29/2021
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: replication
ms.topic: reference
f1_keywords:
- sp_adddistpublisher
- sp_adddistpublisher_TSQL
helpviewer_keywords:
- sp_adddistpublisher
ms.assetid: 04e15011-a902-4074-b38c-3ec2fc73b838
author: mashamsft
ms.author: mathoma
ms.openlocfilehash: c25bc50edbbf1c6826f091d2ea1570b423ddaf30
ms.sourcegitcommit: 851f47e27512651f809540b77bfbd09e6ddb5362
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105937825"
---
# <a name="sp_adddistpublisher-transact-sql"></a>sp_adddistpublisher (Transact-SQL)
[!INCLUDE [SQL Server SQL MI](../../includes/applies-to-version/sql-asdbmi.md)]

  指定されたディストリビューション データベースを使用するように、パブリッシャーを構成します。 このストアドプロシージャは、ディストリビューター側で任意のデータベースに対して実行されます。 このストアドプロシージャを使用する前に、transact-sql [&#41;&#40;](../../relational-databases/system-stored-procedures/sp-adddistributor-transact-sql.md) ストアドプロシージャを sp_adddistributor し、 [transact-sql &#40;を sp_adddistributiondb ](../../relational-databases/system-stored-procedures/sp-adddistributiondb-transact-sql.md) する必要があることに注意してください。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```  
  
sp_adddistpublisher [ @publisher= ] 'publisher'   
        , [ @distribution_db= ] 'distribution_db'   
    [ , [ @security_mode= ] security_mode ]   
    [ , [ @login= ] 'login' ]   
    [ , [ @password= ] 'password' ]   
    [ , [ @working_directory= ] 'working_directory' ]   
    [ , [ @storage_connection_string= ] 'storage_connection_string']
    [ , [ @trusted= ] 'trusted' ]   
    [ , [ @encrypted_password= ] encrypted_password ]   
    [ , [ @thirdparty_flag = ] thirdparty_flag ]  
    [ , [ @publisher_type = ] 'publisher_type' ]  
```  
  
## <a name="arguments"></a>引数  
`[ @publisher = ] 'publisher'` パブリッシャーの名前を指定します。 *publisher* は **sysname** で、既定値はありません。  

<!--SQL Server 2019 on Linux-->
::: moniker range=">= sql-server-linux-ver15 || >= sql-server-ver15 "

> [!NOTE]
> サーバー名はとして指定でき `<Hostname>,<PortNumber>` ます。 SQL Server が Linux または Windows でカスタムポートを使用して展開され、browser サービスが無効になっている場合は、接続のポート番号を指定する必要があります。 リモートディストリビューターにカスタムポート番号を使用するのは、SQL Server 2019 にのみ適用されます。

::: moniker-end
  
`[ @distribution_db = ] 'distribution_db'` ディストリビューションデータベースの名前を指定します。 *distributor_db* は **sysname** であり、既定値はありません。 このパラメーターは、レプリケーションエージェントがパブリッシャーに接続するために使用されます。  
  
`[ @security_mode = ] security_mode` 実装されているセキュリティモードです。 このパラメーターは、キュー更新サブスクリプションのパブリッシャーまたは以外のパブリッシャーに接続するために、レプリケーションエージェントによってのみ使用され [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。 *security_mode* は **int**,、これらの値のいずれかを指定できます。  
  
|値|説明|  
|-----------|-----------------|  
|**0**|ディストリビューター側のレプリケーションエージェントは [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 、認証を使用してパブリッシャーに接続します。|  
|**1** (既定値)|ディストリビューター側のレプリケーション エージェントは Windows 認証を使用してパブリッシャーに接続します。|  
  
`[ @login = ] 'login'` ログインを示します。 *Security_mode* が **0** の場合、このパラメーターは必須です。 *login* のデータ型は **sysname** で、既定値は NULL です。 このパラメーターは、レプリケーションエージェントがパブリッシャーに接続するために使用されます。  
  
`[ @password = ] 'password']` パスワードを入力します。 *パスワード* は **sysname**,、既定値は NULL です。 このパラメーターは、レプリケーションエージェントがパブリッシャーに接続するために使用されます。  
  
> [!IMPORTANT]  
>  空白のパスワードは使用しないでください。 強力なパスワードを使用してください。  
  
`[ @working_directory = ] 'working_directory'` パブリケーションのデータとスキーマファイルを格納するために使用する作業ディレクトリの名前を指定します。 *working_directory* は **nvarchar (255)** で、既定値は、のこのインスタンスの ReplData フォルダー [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] です。たとえば、のように `C:\Program Files\Microsoft SQL Server\MSSQL\MSSQ.1\ReplData` なります。 名前は UNC 形式で指定する必要があります。  

 Azure SQL Database には、を使用 `\\<storage_account>.file.core.windows.net\<share>` します。

`[ @storage_connection_string = ] 'storage_connection_string'` は SQL Database に必要です。 Azure Portal からアクセスキーを使用して、[ストレージ > 設定] を表示します。

 > [!INCLUDE[Azure SQL Database link](../../includes/azure-sql-db-repl-for-more-information.md)]

`[ @trusted = ] 'trusted'` このパラメーターは非推奨とされており、旧バージョンとの互換性のためだけに用意されています。 *信頼* されているのは **nvarchar (5)** です。これを **false** に設定すると、エラーが発生します。  
  
`[ @encrypted_password = ] encrypted_password`*Encrypted_password* の設定はサポートされなくなりました。 この **ビット** パラメーターを **1** に設定しようとすると、エラーが発生します。  
  
`[ @thirdparty_flag = ] thirdparty_flag` パブリッシャーがの場合 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。 *thirdparty_flag* は **ビット** で、次のいずれかの値を指定できます。  
  
|値|説明|  
|-----------|-----------------|  
|**0** (既定値)|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] データベース.|  
|**1**|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 以外のデータベース|  
  
`[ @publisher_type = ] 'publisher_type'` パブリッシャーがでない場合に、パブリッシャーの種類を指定し [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。 *publisher_type* は sysname で、次のいずれかの値を指定できます。  
  
|値|説明|  
|-----------|-----------------|  
|**MS**<br /><br /> (既定値)。|パブリッシャーを指定し [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。|  
|**ORACLE11I**|標準の Oracle パブリッシャーを指定します。|  
|**ORACLE GATEWAY**|Oracle ゲートウェイ パブリッシャーを指定します。|  
  
 Oracle パブリッシャーと Oracle ゲートウェイパブリッシャーの違いの詳細については、「 [Oracle パブリッシャーの構成](../../relational-databases/replication/non-sql/configure-an-oracle-publisher.md)」を参照してください。  
  
## <a name="return-code-values"></a>リターン コードの値  
 0 (成功) または 1 (失敗)  
  
## <a name="remarks"></a>解説  
 **sp_adddistpublisher** は、スナップショットレプリケーション、トランザクションレプリケーション、およびマージレプリケーションで使用されます。  
  
## <a name="example"></a>例  
 [!code-sql[HowTo#AddDistPub](../../relational-databases/replication/codesnippet/tsql/sp-adddistpublisher-tran_1.sql)]  
  
## <a name="permissions"></a>アクセス許可  
 **Sp_adddistpublisher** を実行できるのは、 **sysadmin** 固定サーバーロールのメンバーだけです。  
  
## <a name="see-also"></a>参照  
 [パブリッシングとディストリビューションの構成](../../relational-databases/replication/configure-publishing-and-distribution.md)   
 [sp_changedistpublisher (Transact-SQL)](../../relational-databases/system-stored-procedures/sp-changedistpublisher-transact-sql.md)   
 [sp_dropdistpublisher &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-dropdistpublisher-transact-sql.md)   
 [sp_helpdistpublisher &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-helpdistpublisher-transact-sql.md)   
 [システム ストアド プロシージャ &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/system-stored-procedures-transact-sql.md)   
 [[ディストリビューションの構成]](../../relational-databases/replication/configure-distribution.md)  
  
  
