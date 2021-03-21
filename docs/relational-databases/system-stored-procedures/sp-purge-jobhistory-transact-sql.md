---
description: sp_purge_jobhistory (Transact-SQL)
title: sp_purge_jobhistory (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- sp_purge_jobhistory_TSQL
- sp_purge_jobhistory
dev_langs:
- TSQL
helpviewer_keywords:
- sp_purge_jobhistory
ms.assetid: 237f9bad-636d-4262-9bfb-66c034a43e88
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 55f8c3d0bd3c5812a123ec0b3d437171fd2a3d71
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104748248"
---
# <a name="sp_purge_jobhistory-transact-sql"></a>sp_purge_jobhistory (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  ジョブの履歴レコードを削除します。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```  
  
sp_purge_jobhistory   
   {   [ @job_name = ] 'job_name' |   
     | [ @job_id = ] job_id }  
   [ , [ @oldest_date = ] oldest_date ]  
```  
  
## <a name="arguments"></a>引数  
`[ @job_name = ] 'job_name'` 履歴レコードを削除するジョブの名前を指定します。 *job_name* は **sysname**,、既定値は NULL です。 *Job_id* または *job_name* のいずれかを指定する必要がありますが、両方を指定することはできません。  
  
> [!NOTE]  
>  **Sysadmin** 固定サーバーロールのメンバー、または **Sqlagentoperatorrole** 固定データベースロールのメンバーは、 *job_name* または *job_id* を指定せずに **sp_purge_jobhistory** を実行できます。 **Sysadmin** ユーザーがこれらの引数を指定しない場合、すべてのローカルジョブとマルチサーバージョブのジョブ履歴が *oldest_date* によって指定された時間内に削除されます。 **Sqlagentoperatorrole** ユーザーがこれらの引数を指定しない場合、すべてのローカルジョブのジョブ履歴は *oldest_date* で指定された時間内に削除されます。  
  
`[ @job_id = ] job_id` 削除するレコードのジョブのジョブ識別番号を指定します。 *job_id* は **uniqueidentifier**,、既定値は NULL です。 *Job_id* または *job_name* のいずれかを指定する必要がありますが、両方を指定することはできません。 **Sysadmin** または **Sqlagentoperatorrole** ユーザーがこの引数を使用する方法については、 **\@ job_name** の説明にある注を参照してください。  
  
`[ @oldest_date = ] oldest_date` 履歴に保持する最も古いレコード。 *oldest_date* は **datetime**,、既定値は NULL です。 *Oldest_date* を指定した場合、 **sp_purge_jobhistory** 指定された値よりも古いレコードだけが削除されます。  
  
## <a name="return-code-values"></a>リターン コードの値  
 **0** (成功) または **1** (失敗)  
  
## <a name="result-sets"></a>結果セット  
 なし  
  
## <a name="remarks"></a>解説  
 **Sp_purge_jobhistory** が正常に完了すると、メッセージが返されます。  
  
## <a name="permissions"></a>権限  
 既定では、このストアドプロシージャを実行できるのは、 **sysadmin** 固定サーバーロールまたは **Sqlagentoperatorrole** 固定データベースロールのメンバーだけです。 **Sysadmin** のメンバーは、すべてのローカルジョブとマルチサーバージョブのジョブ履歴を削除できます。 **Sqlagentoperatorrole** のメンバーは、すべてのローカルジョブのジョブ履歴のみを削除できます。  
  
 **SQLAgentUserRole** のメンバーや **SQLAgentReaderRole** のメンバーなど、他のユーザーには、 **sp_purge_jobhistory** に対する EXECUTE 権限が明示的に付与されている必要があります。 このストアド プロシージャに対する EXECUTE 権限が許可されていると、これらのユーザーは自分が所有しているジョブの履歴だけを削除できます。  
  
 **SQLAgentUserRole**、 **SQLAgentReaderRole**、および **Sqlagentoperatorrole** の固定データベースロールは、 **msdb** データベースにあります。 権限の詳細については、「 [SQL Server エージェント固定データベースロール](../../ssms/agent/sql-server-agent-fixed-database-roles.md)」を参照してください。  
  
## <a name="examples"></a>例  
  
### <a name="a-remove-history-for-a-specific-job"></a>A. 特定のジョブの履歴を削除する  
 次の例では、という名前のジョブの履歴を削除し `NightlyBackups` ます。  
  
```  
USE msdb ;  
GO  
  
EXEC dbo.sp_purge_jobhistory  
    @job_name = N'NightlyBackups' ;  
GO  
```  
  
### <a name="b-remove-history-for-all-jobs"></a>B. すべてのジョブの履歴を削除する  
  
> [!NOTE]  
>  **Sysadmin** 固定サーバーロールのメンバー、および **Sqlagentoperatorrole** のメンバーだけが、すべてのジョブの履歴を削除できます。 **Sysadmin** ユーザーがパラメーターを使用せずにこのストアドプロシージャを実行すると、すべてのローカルジョブとマルチサーバージョブのジョブ履歴が削除されます。 **Sqlagentoperatorrole** ユーザーがパラメーターを使用せずにこのストアドプロシージャを実行すると、すべてのローカルジョブのジョブ履歴のみが削除されます。  
  
 次の例では、パラメーターを使用せずにプロシージャを実行して、すべての履歴レコードを削除します。  
  
```  
USE msdb ;  
GO  
  
EXEC dbo.sp_purge_jobhistory ;  
GO  
```  
  
## <a name="see-also"></a>参照  
 [sp_help_job &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-help-job-transact-sql.md)   
 [sp_help_jobhistory &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-help-jobhistory-transact-sql.md)   
 [システム ストアド プロシージャ &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/system-stored-procedures-transact-sql.md)   
 [GRANT (オブジェクトの権限の許可) &#40;Transact-SQL&#41;](../../t-sql/statements/grant-object-permissions-transact-sql.md)  
  
  
