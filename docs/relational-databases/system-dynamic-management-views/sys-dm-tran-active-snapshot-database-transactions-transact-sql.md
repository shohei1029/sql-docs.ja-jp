---
description: sys.dm_tran_active_snapshot_database_transactions (Transact-sql)
title: sys.dm_tran_active_snapshot_database_transactions (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- sys.dm_tran_active_snapshot_database_transactions_TSQL
- dm_tran_active_snapshot_database_transactions
- sys.dm_tran_active_snapshot_database_transactions
- dm_tran_active_snapshot_database_transactions_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_tran_active_snapshot_database_transactions dynamic management view
ms.assetid: 55b83f9c-da10-4e65-9846-f4ef3c0c0f36
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: f766ed71d42f761e03b1b63c74aad638f022c177
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104756472"
---
# <a name="sysdm_tran_active_snapshot_database_transactions-transact-sql"></a>sys.dm_tran_active_snapshot_database_transactions (Transact-sql)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  インスタンスで [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] は、この動的管理ビューは、行バージョンを生成またはアクセスする可能性のあるすべてのアクティブなトランザクションの仮想テーブルを返します。 トランザクションは、次の条件を 1 つ以上満たします。  
  
-   ALLOW_SNAPSHOT_ISOLATION データベース オプションと READ_COMMITTED_SNAPSHOT データベース オプションのいずれかまたは両方が ON に設定されている場合、  
  
    -   スナップショット分離レベルで実行されているトランザクションごとに1行、または行のバージョン管理を使用する read committed 分離レベルがあります。  
  
    -   現在のデータベースに行バージョンを作成するトランザクションごとに、1 行のデータが存在する。 たとえば、トランザクションでは、現在のデータベースの行を更新または削除することによって行バージョンを生成します。  
  
-   トリガーが起動される場合、トリガーが実行されるトランザクションごとに 1 行のデータが存在する。  
  
-   オンライン インデックス処理中に、インデックスを作成しているトランザクションごとに 1 行のデータが存在する。  
  
-   複数のアクティブな結果セット (MARS) セッションが有効になっている場合は、行バージョンにアクセスしているトランザクションごとに1つの行が存在します。  
  
 この動的管理ビューには、システムトランザクションは含まれません。  
  
> [!NOTE]  
>  またはからこれを呼び出すに [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] は [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] 、 **sys.dm_pdw_nodes_tran_active_snapshot_database_transactions** という名前を使用します。  
  
## <a name="syntax"></a>構文  
  
```  
  
sys.dm_tran_active_snapshot_database_transactions  
```  
  
## <a name="table-returned"></a>返されるテーブル  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|**transaction_id**|**bigint**|トランザクションに割り当てられている一意な識別番号。 トランザクション ID は主に、ロック操作でトランザクションを識別するために使用されます。|  
|**transaction_sequence_num**|**bigint**|トランザクションのシーケンス番号。 これは、トランザクションが開始されるときに、トランザクションに割り当てられる一意のシーケンス番号です。 バージョン レコードを生成せず、スナップショット スキャンを行わないトランザクションには、トランザクション シーケンス番号は割り当てられません。|  
|**commit_sequence_num**|**bigint**|トランザクションが完了 (コミットまたは停止) したことを示すシーケンス番号。 アクティブなトランザクションの場合、値は NULL です。|  
|**is_snapshot**|**int**|0 = スナップショット分離トランザクションではありません。<br /><br /> 1 = スナップショット分離トランザクションです。|  
|**session_id**|**int**|トランザクションを開始したセッションの ID。|  
|**first_snapshot_sequence_num**|**bigint**|スナップショット取得時にアクティブだったトランザクションの最小トランザクションシーケンス番号。 実行時には、スナップショットトランザクションは、その時点でアクティブなすべてのトランザクションのスナップショットを取得します。 スナップショット以外のトランザクションの場合、この列には0が表示されます。|  
|**max_version_chain_traversed**|**int**|トランザクション全体で一貫性のあるバージョンを検索するためにスキャンされるバージョン チェーンの最大長。|  
|**average_version_chain_traversed**|**real**|走査されるバージョンチェーン内の行バージョンの平均数。|  
|**elapsed_time_seconds**|**bigint**|トランザクションがトランザクションシーケンス番号を取得してから経過した時間。|  
|**pdw_node_id**|**int**|**適用対象**: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 、 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]<br /><br /> このディストリビューションが配置されているノードの識別子。|  
  
## <a name="permissions"></a>権限

で [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] は、 `VIEW SERVER STATE` 権限が必要です。   
SQL Database Basic、S0、S1 のサービス目標、およびエラスティックプール内のデータベースについては、 [サーバー管理者](/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database) アカウントまたは [Azure Active Directory 管理者](/azure/azure-sql/database/authentication-aad-overview#administrator-structure) アカウントが必要です。 その他のすべての SQL Database サービスの目的で `VIEW DATABASE STATE` は、データベースで権限が必要になります。   

## <a name="remarks"></a>解説  
 **sys.dm_tran_active_snapshot_database_transactions** は、トランザクションシーケンス番号 (XSN) が割り当てられているトランザクションを報告します。 XSN は、トランザクションが最初にバージョンストアにアクセスしたときに割り当てられます。 行のバージョン管理を使用するスナップショット分離または READ COMMITTED 分離が有効なデータベースにおいて、XSN がトランザクションに割り当てられるときの例を次に示します。  
  
-   トランザクションが serializable 分離レベルで実行されている場合、更新操作などのステートメントをトランザクションが最初に実行するときに XSN が割り当てられ、行バージョンが作成されます。  
  
-   トランザクションがスナップショット分離で実行されている場合、SELECT 操作を含むデータ操作言語 (DML) ステートメントが実行されると、XSN が割り当てられます。  
  
 トランザクション シーケンス番号は、[!INCLUDE[ssDE](../../includes/ssde-md.md)] インスタンスでトランザクションが開始されるたびに増分されます。  
  
## <a name="examples"></a>例  
 次の例では、4 つの同時実行トランザクションが存在するテスト シナリオを使用します。これらのトランザクションはそれぞれトランザクション シーケンス番号 (XSN) で識別され、ALLOW_SNAPSHOT_ISOLATION オプションと READ_COMMITTED_SNAPSHOT オプションが ON に設定されているデータベース内で実行されます。 実行されるトランザクションは次のとおりです。  
  
-   XSN-57。SERIALIZABLE 分離での更新操作です。  
  
-   XSN-58 は、XSN-57 と同じです。  
  
-   XSN-59 はスナップショット分離の選択操作です  
  
-   XSN-60 は XSN-59 と同じです。  
  
 次のクエリが実行されます。  
  
```  
SELECT   
    transaction_id,  
    transaction_sequence_num,  
    commit_sequence_num,  
    is_snapshot session_id,  
    first_snapshot_sequence_num,  
    max_version_chain_traversed,  
    average_version_chain_traversed,  
    elapsed_time_seconds  
  FROM sys.dm_tran_active_snapshot_database_transactions;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
transaction_id  transaction_sequence_num  commit_sequence_num  
--------------  ------------------------  -------------------  
9295            57                        NULL  
9324            58                        NULL  
9387            59                        NULL  
9400            60                        NULL  
  
is_snapshot  session_id   first_snapshot_sequence_num  
-----------  -----------  ---------------------------  
0            54           0  
0            53           0  
1            52           57  
1            51           57  
  
max_version_chain_traversed  average_version_chain_traversed  
---------------------------  -------------------------------  
0                            0  
0                            0  
1                            1  
1                            1  
  
elapsed_time_seconds  
--------------------  
419  
397  
359  
333  
```  
  
 次の情報は、 **sys.dm_tran_active_snapshot_database_transactions** の結果を評価します。  
  
-   XSN-57: このトランザクションはスナップショット分離で実行されていないため、 `is_snapshot` 値と `first_snapshot_sequence_num` は `0` です。 `transaction_sequence_num` ALLOW_SNAPSHOT_ISOLATION または READ_COMMITTED_SNAPSHOT データベースオプションのいずれかまたは両方がオンになっているため、トランザクションシーケンス番号がこのトランザクションに割り当てられていることを示します。  
  
-   XSN-58: このトランザクションはスナップショット分離で実行されていません。 XSN-57 についても同じ情報が適用されます。  
  
-   XSN-59: これは、スナップショット分離で実行されている最初のアクティブなトランザクションです。 このトランザクションは、によって示されているように、XSN-57 より前にコミットされたデータを読み取り `first_snapshot_sequence_num` ます。 このトランザクションの出力では、1 行にスキャンされるバージョン チェーンの最大長が `1` で、アクセスした行ごとに平均で `1` つのバージョンがスキャンされていることも示されています。 これは、トランザクション XSN-57、XSN-58、および XSN-60 では行が変更されずコミットされていないことを表します。  
  
-   XSN-60: これは、スナップショット分離で実行されている2番目のトランザクションです。 出力では、XSN-59 と同じ情報が示されます。  
  
## <a name="see-also"></a>参照  
 [SET TRANSACTION ISOLATION LEVEL &#40;Transact-SQL&#41;](../../t-sql/statements/set-transaction-isolation-level-transact-sql.md)   
 [動的管理ビューと動的管理関数 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)   
 [トランザクション関連の動的管理ビューおよび関数 &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/transaction-related-dynamic-management-views-and-functions-transact-sql.md)  
  
