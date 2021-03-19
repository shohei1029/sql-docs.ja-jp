---
description: sys.dm_tran_active_transactions (Transact-SQL)
title: sys.dm_tran_active_transactions (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/30/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- sys.dm_tran_active_transactions
- sys.dm_tran_active_transactions_TSQL
- dm_tran_active_transactions_TSQL
- dm_tran_active_transactions
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_tran_active_transactions dynamic management view
ms.assetid: 154ad6ae-5455-4ed2-b014-e443abe2c6ee
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 7fb50e96cba3ff7cf9dfc3ab98c1808f1569f051
ms.sourcegitcommit: bf7577b3448b7cb0e336808f1112c44fa18c6f33
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104610904"
---
# <a name="sysdm_tran_active_transactions-transact-sql"></a>sys.dm_tran_active_transactions (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスのトランザクションに関する情報を返します。  
  
> [!NOTE]  
>  またはからこれを呼び出すに [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] は [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] 、 **sys.dm_pdw_nodes_tran_active_transactions** という名前を使用します。  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|transaction_id|**bigint**|データベース レベルではなくインスタンス レベルのトランザクションの ID。 これはインスタンス内のすべてのデータベースでのみ一意ですが、すべてのサーバー インスタンスにおいては一意ではありません。|  
|name|**nvarchar(32)**|トランザクション名。 トランザクションがマークされている場合、この名前が上書きされます。マークされた名前がトランザクション名と置き換わります。|  
|transaction_begin_time|**datetime**|トランザクションの開始時刻。|  
|transaction_type|**int**|トランザクションの種類。<br /><br /> 1 = 読み取り/書き込みトランザクション<br /><br /> 2 = 読み取り専用トランザクション<br /><br /> 3 = システム トランザクション<br /><br /> 4 = 分散トランザクション|  
|transaction_uow|**uniqueidentifier**|分散トランザクションの、トランザクション作業単位 (UOW) 識別子。 MS DTC では UOW 識別子を使って分散トランザクションが処理されます。|  
|transaction_state|**int**|0 = トランザクションはまだ完全に初期化されていません。<br /><br /> 1 = トランザクションは初期化されていますが、開始されていません。<br /><br /> 2 = トランザクションはアクティブです。<br /><br /> 3 = トランザクションは終了しました。 これは、読み取り専用トランザクションに使用されます。<br /><br /> 4 = 分散トランザクションでコミット処理が開始されています。 これは、分散トランザクションのみに使用されます。 分散トランザクションはまだアクティブですが、追加の処理は行えません。<br /><br /> 5 = トランザクションは準備された状態で解決を待機しています。<br /><br /> 6 = トランザクションはコミットされました。<br /><br /> 7 = トランザクションはロールバック中です。<br /><br /> 8 = トランザクションはロールバックされました。|  
|transaction_status |**int**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|transaction_status2|**int**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|dtc_state|**int**|**適用対象**: [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] (最初のリリースから [現在のリリース](/previous-versions/azure/ee336279(v=azure.100))まで)。<br /><br /> 1 = アクティブ<br /><br /> 2 = PREPARED<br /><br /> 3 = COMMITTED<br /><br /> 4 = 中止<br /><br /> 5 = 回復済み|  
|dtc_status|**int**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|dtc_isolation_level|**int**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|filestream_transaction_id|**varbinary (128)**|**適用対象**: [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] (最初のリリースから [現在のリリース](/previous-versions/azure/ee336279(v=azure.100))まで)。<br /><br /> [!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|pdw_node_id|**int**|**適用対象**: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 、 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]<br /><br /> このディストリビューションが配置されているノードの識別子。|  
  
## <a name="permissions"></a>アクセス許可

で [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] は、 `VIEW SERVER STATE` 権限が必要です。   
SQL Database Basic、S0、S1 のサービス目標、およびエラスティックプール内のデータベースについては、 [サーバー管理者](/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database) アカウントまたは [Azure Active Directory 管理者](/azure/azure-sql/database/authentication-aad-overview#administrator-structure) アカウントが必要です。 その他のすべての SQL Database サービスの目的で `VIEW DATABASE STATE` は、データベースで権限が必要になります。   


## <a name="examples"></a>例  
  
### <a name="a-using-sysdm_tran_active_transactions-with-other-dmvs-to-find-information-about-active-transactions"></a>A. Sys.dm_tran_active_transactions を他の Dmv と共に使用して、アクティブなトランザクションに関する情報を検索する
 次の例は、システム上のアクティブなトランザクションを示しています。また、トランザクション、ユーザーセッション、送信されたアプリケーション、それを開始したクエリなどの詳細情報を提供します。  
  
```sql  
select
  getdate() as now,
  DATEDIFF(SECOND, transaction_begin_time, GETDATE()) as tran_elapsed_time_seconds,
  *
FROM
  sys.dm_tran_active_transactions at
  JOIN sys.dm_tran_session_transactions st ON st.transaction_id = at.transaction_id
  INNER JOIN sys.sysprocesses sp ON st.session_id = sp.spid 
    CROSS APPLY sys.dm_exec_sql_text(sql_handle) txt
order by
  tran_elapsed_time_seconds desc
```


## <a name="see-also"></a>参照  
 [sys.dm_tran_session_transactions &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-tran-session-transactions-transact-sql.md)   
 [sys.dm_tran_database_transactions &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-tran-database-transactions-transact-sql.md)   
 [動的管理ビューと動的管理関数 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)   
 [トランザクション関連の動的管理ビューおよび関数 &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/transaction-related-dynamic-management-views-and-functions-transact-sql.md)  
