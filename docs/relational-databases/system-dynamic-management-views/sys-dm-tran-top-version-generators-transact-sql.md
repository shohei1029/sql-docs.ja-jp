---
description: sys.dm_tran_top_version_generators (Transact-SQL)
title: sys.dm_tran_top_version_generators (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- dm_tran_top_version_generators
- sys.dm_tran_top_version_generators
- dm_tran_top_version_generators_TSQL
- sys.dm_tran_top_version_generators_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_tran_top_version_generators dynamic management view
ms.assetid: cec7809b-ba8a-4df9-b5bb-d4f651ff1a86
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 0f05f5666cffbac5156be75f6ffc26f3eb86e6ce
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104753752"
---
# <a name="sysdm_tran_top_version_generators-transact-sql"></a>sys.dm_tran_top_version_generators (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  バージョン ストアに最も多くバージョンを作成しているオブジェクトの仮想テーブルを返します。 **sys.dm_tran_top_version_generators** **database_id** と **rowset_id** によってグループ化された、集計されたレコードの256最大長を返します。 **sys.dm_tran_top_version_generators** は、 **dm_tran_version_store** 仮想テーブルに対してクエリを実行してデータを取得します。 このビューはバージョンストアに対してクエリを実行し、バージョンストアは非常に大きくなる可能性があるため、 **sys.dm_tran_top_version_generators** は実行する非効率的なビューです。 この関数を使用して、バージョンストアの最大のコンシューマーを検索することをお勧めします。  
  
> [!NOTE]  
>  またはからこれを呼び出すに [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] は [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] 、 **sys.dm_pdw_nodes_tran_top_version_generators** という名前を使用します。  
  
## <a name="syntax"></a>構文  
  
```  
  
sys.dm_tran_top_version_generators  
```  
  
## <a name="table-returned"></a>返されるテーブル  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|**database_id**|**int**|データベース ID。|  
|**rowset_id**|**bigint**|行セット ID。|  
|**aggregated_record_length_in_bytes**|**int**|バージョンストア内の各 **database_id** および **rowset_id ペア** のレコード長の合計。|  
|**pdw_node_id**|**int**|**適用対象**: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 、 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]<br /><br /> このディストリビューションが配置されているノードの識別子。|  
  
## <a name="permissions"></a>権限

で [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] は、 `VIEW SERVER STATE` 権限が必要です。   
SQL Database Basic、S0、S1 のサービス目標、およびエラスティックプール内のデータベースについては、 [サーバー管理者](/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database) アカウントまたは [Azure Active Directory 管理者](/azure/azure-sql/database/authentication-aad-overview#administrator-structure) アカウントが必要です。 その他のすべての SQL Database サービスの目的で `VIEW DATABASE STATE` は、データベースで権限が必要になります。   

## <a name="remarks"></a>解説  
 **Sys.dm_tran_top_version_generators** は、バージョンストア全体をスキャンするときに多くのページを読み取る必要があるため、 **sys.dm_tran_top_version_generators** を実行すると、システムのパフォーマンスが低下する可能性があります。  
  
## <a name="examples"></a>例  
 次の例では、4 つの同時実行トランザクションが存在するテスト シナリオを使用します。これらのトランザクションはそれぞれトランザクション シーケンス番号 (XSN) で識別され、ALLOW_SNAPSHOT_ISOLATION オプションと READ_COMMITTED_SNAPSHOT オプションが ON に設定されているデータベース内で実行されます。 実行されるトランザクションは次のとおりです。  
  
-   XSN-57。SERIALIZABLE 分離での更新操作です。  
  
-   XSN-58 は、XSN-57 と同じです。  
  
-   XSN-59 は、スナップショット分離での選択操作です。  
  
-   XSN-60 は、XSN-59 と同じです。  
  
 次のクエリが実行されます。  
  
```  
SELECT  
    database_id,  
    rowset_id,  
    aggregated_record_length_in_bytes  
  FROM sys.dm_tran_top_version_generators;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
database_id rowset_id            aggregated_record_length_in_bytes  
----------- -------------------- ---------------------------------  
9           72057594038321152    87  
9           72057594038386688    33  
```  
  
 出力には、すべてのバージョンがによって作成され、バージョンが2つのテーブルから生成されることが示されて `database_id``9` います。  
  
## <a name="see-also"></a>参照  
 [動的管理ビューと動的管理関数 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)   
 [トランザクション関連の動的管理ビューおよび関数 &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/transaction-related-dynamic-management-views-and-functions-transact-sql.md)  
  
