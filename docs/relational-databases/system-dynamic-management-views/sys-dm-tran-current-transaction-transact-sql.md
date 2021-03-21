---
description: sys.dm_tran_current_transaction (Transact-sql)
title: sys.dm_tran_current_transaction (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- sys.dm_tran_current_transaction
- sys.dm_tran_current_transaction_TSQL
- dm_tran_current_transaction_TSQL
- dm_tran_current_transaction
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_tran_current_transaction dynamic management view
ms.assetid: 75d5697d-b390-4963-99b8-fa0b4244a40c
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 4cf6d4f749256ae3bb8d7fb237ec0960f816aab6
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104735992"
---
# <a name="sysdm_tran_current_transaction-transact-sql"></a>sys.dm_tran_current_transaction (Transact-sql)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  現在のセッションのトランザクションの状態情報を表示する1行のデータを返します。  
  
> [!NOTE]  
>  またはからこれを呼び出すに [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] は [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] 、 **sys.dm_pdw_nodes_tran_current_transaction** という名前を使用します。  
  
## <a name="syntax"></a>構文  
  
```  
  
sys.dm_tran_current_transaction  
```  
  
## <a name="table-returned"></a>返されるテーブル  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|**transaction_id**|**bigint**|現在のスナップショットのトランザクション ID。|  
|**transaction_sequence_num**|**bigint**|レコードのバージョンを生成するトランザクションのシーケンス番号。|  
|**transaction_is_snapshot**|**bit**|スナップショット分離の状態。 トランザクションがスナップショット分離に基づいて開始された場合、この値は 1 です。 それ以外の場合、値は0です。|  
|**first_snapshot_sequence_num**|**bigint**|スナップショット取得時にアクティブだったトランザクションの最小トランザクションシーケンス番号。 実行時には、スナップショットトランザクションは、その時点でアクティブなすべてのトランザクションのスナップショットを取得します。 スナップショット以外のトランザクションの場合、この列には0が表示されます。|  
|**last_transaction_sequence_num**|**bigint**|グローバルシーケンス番号。 この値は、システムにより生成された最後のトランザクション シーケンス番号を表します。|  
|**first_useful_sequence_num**|**bigint**|グローバルシーケンス番号。 この値は、行バージョンをバージョン ストアに保持する必要があるトランザクションの、最も古いトランザクション シーケンス番号を表します。 以前のトランザクションで作成された行バージョンは削除できます。|  
|**pdw_node_id**|**int**|**適用対象**: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 、 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]<br /><br /> このディストリビューションが配置されているノードの識別子。|  
  
## <a name="permissions"></a>権限

で [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] は、 `VIEW SERVER STATE` 権限が必要です。   
SQL Database Basic、S0、S1 のサービス目標、およびエラスティックプール内のデータベースについては、 [サーバー管理者](/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database) アカウントまたは [Azure Active Directory 管理者](/azure/azure-sql/database/authentication-aad-overview#administrator-structure) アカウントが必要です。 その他のすべての SQL Database サービスの目的で `VIEW DATABASE STATE` は、データベースで権限が必要になります。   
  
## <a name="examples"></a>例  
 次の例では、4 つの同時実行トランザクションが存在するテスト シナリオを使用します。これらのトランザクションはそれぞれトランザクション シーケンス番号 (XSN) で識別され、ALLOW_SNAPSHOT_ISOLATION オプションと READ_COMMITTED_SNAPSHOT オプションが ON に設定されているデータベース内で実行されます。 実行されるトランザクションは次のとおりです。  
  
-   XSN-57。SERIALIZABLE 分離での更新操作です。  
  
-   XSN-58 は、XSN-57 と同じです。  
  
-   XSN-59 は、スナップショット分離での選択操作です。  
  
-   XSN-60 は、XSN-59 と同じです。  
  
 次のクエリは、各トランザクションのスコープ内で実行されます。  
  
```  
SELECT   
    transaction_id  
   ,transaction_sequence_num  
   ,transaction_is_snapshot  
   ,first_snapshot_sequence_num  
   ,last_transaction_sequence_num  
   ,first_useful_sequence_num  
  FROM sys.dm_tran_current_transaction;  
```  
  
 XSN-59 の結果を次に示します。  
  
```  
transaction_id       transaction_sequence_num transaction_is_snapshot  
-------------------- ------------------------ -----------------------  
9387                 59                       1                         
  
first_snapshot_sequence_num last_transaction_sequence_num  
--------------------------- -----------------------------  
57                               61                        
  
first_useful_sequence_num  
-------------------------  
57  
```  
  
 この出力は、xsn-59 が、xsn-59 が開始されたときにアクティブだった最初のトランザクションとして XSN-57 を使用するスナップショットトランザクションであることを示しています。 つまり、XSN-59 では、トランザクション シーケンス番号が XSN-57 より低いトランザクションによってコミットされたデータが読み取られます。  
  
 XSN-57 の結果を次に示します。  
  
```  
transaction_id       transaction_sequence_num transaction_is_snapshot  
-------------------- ------------------------ -----------------------  
9295                 57                       0  
  
first_snapshot_sequence_num last_transaction_sequence_num  
--------------------------- -----------------------------  
NULL                        61  
  
first_useful_sequence_num  
-------------------------  
57  
```  
  
 XSN-57 はスナップショットトランザクションではないため、 `first_snapshot_sequence_num` は `NULL` です。  
  
## <a name="see-also"></a>参照  
 [動的管理ビューと動的管理関数 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)   
 [トランザクション関連の動的管理ビューおよび関数 &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/transaction-related-dynamic-management-views-and-functions-transact-sql.md)  
  
