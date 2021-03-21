---
description: DBCC FREEPROCCACHE (Transact-SQL)
title: DBCC FREEPROCCACHE (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 10/13/2017
ms.prod: sql
ms.prod_service: synapse-analytics, pdw, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- FREEPROCCACHE_TSQL
- FREEPROCCACHE
- DBCC_FREEPROCCACHE_TSQL
- DBCC FREEPROCCACHE
dev_langs:
- TSQL
helpviewer_keywords:
- freeing procedure cache
- removing procedure cache elements
- deleting procedure cache elements
- DBCC FREEPROCCACHE statement
- procedure cache [SQL Server]
- clearing procedure cache
ms.assetid: 0e09d210-6f23-4129-aedb-3d56b2980683
author: pmasl
ms.author: umajay
monikerRange: '>=aps-pdw-2016||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: a24703ab8d93be9dc3f8b7f5fabb4e8e99e0e74a
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104750522"
---
# <a name="dbcc-freeproccache-transact-sql"></a>DBCC FREEPROCCACHE (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

プラン キャッシュからすべての要素を削除するか、プラン ハンドルまたは SQL ハンドルを指定して特定のプランを削除するか、指定したリソース プールに関連付けられたすべてのキャッシュ エントリを削除します。

> [!NOTE]
> DBCC FREEPROCCACHE では、ネイティブ コンパイル ストアド プロシージャの実行統計は消去されません。 プロシージャ キャッシュには、ネイティブ コンパイル ストアド プロシージャに関する情報は含まれていません。 プロシージャの実行から収集された実行統計は、実行統計の DMV に表示されます。「[sys.dm_exec_procedure_stats &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-procedure-stats-transact-sql.md)」と「[sys.dm_exec_query_plan &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-query-plan-transact-sql.md)」を参照してください。  
  
![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)
  
## <a name="syntax"></a>構文  
[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の構文:

```sql
DBCC FREEPROCCACHE [ ( { plan_handle | sql_handle | pool_name } ) ] [ WITH NO_INFOMSGS ]  
```  

[!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] および [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] の構文:
  
```sql
DBCC FREEPROCCACHE [ ( COMPUTE | ALL ) ] 
     [ WITH NO_INFOMSGS ]   
[;]  
```  

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
 ( { *plan_handle* | *sql_handle* | *pool_name* } )  
*plan_handle* は、既に実行されていて、そのプランがプラン キャッシュに格納されているバッチのクエリ プランを一意に識別します。 *plan_handle* は **varbinary(64)** 型であり、次の動的管理オブジェクトから取得できます。  
 -   [sys.dm_exec_cached_plans](../../relational-databases/system-dynamic-management-views/sys-dm-exec-cached-plans-transact-sql.md)  
 -   [sys.dm_exec_requests](../../relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql.md)  
 -   [sys.dm_exec_query_memory_grants](../../relational-databases/system-dynamic-management-views/sys-dm-exec-query-memory-grants-transact-sql.md)  
 -   [sys.dm_exec_query_stats](../../relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql.md)  

*sql_handle* は、削除するバッチの SQL ハンドルです。 *sql_handle* は **varbinary(64)** 型であり、次の動的管理オブジェクトから取得できます。  
 -   [sys.dm_exec_query_stats](../../relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql.md)  
 -   [sys.dm_exec_requests](../../relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql.md)  
 -   [sys.dm_exec_cursors](../../relational-databases/system-dynamic-management-views/sys-dm-exec-cursors-transact-sql.md)  
 -   [sys.dm_exec_xml_handles](../../relational-databases/system-dynamic-management-views/sys-dm-exec-xml-handles-transact-sql.md)  
 -   [sys.dm_exec_query_memory_grants](../../relational-databases/system-dynamic-management-views/sys-dm-exec-query-memory-grants-transact-sql.md)  

*pool_name* は、Resource Governor リソース共有元の名前です *pool_name* は **sysname** 型であり、[sys.dm_resource_governor_resource_pools](../../relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-transact-sql.md) 動的管理ビューに対してクエリを実行して取得できます。  
 Resource Governor ワークロード グループをリソース共有元に関連付けるには、[sys.dm_resource_governor_workload_groups](../../relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-transact-sql.md) 動的管理ビューに対してクエリを実行します。 セッションのワークロード グループの情報を表示するには、[sys.dm_exec_sessions](../../relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql.md) 動的管理ビューに対してクエリを実行します。  

  
 WITH NO_INFOMSGS  
 すべての情報メッセージを表示しないようにします。  
  
 COMPUTE  
 各コンピューティング ノードからクエリ プラン キャッシュを削除します。 これが既定値です。  
  
 ALL  
 各計算ノードと各制御ノードからクエリ プラン キャッシュを消去します。  

> [!NOTE]
> [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 以降は、`ALTER DATABASE SCOPED CONFIGURATION CLEAR PROCEDURE_CACHE` を使用してスコープ内のデータベースのプロシージャ (プラン) キャッシュをクリアします。

## <a name="remarks"></a>注釈  
DBCC FREEPROCCACHE を使用してプラン キャッシュをクリアする際には注意が必要です。 プロシージャ (プラン) キャッシュをクリアすると、すべてのプランが削除されます。その後にクエリを実行すると、以前にキャッシュされたプランは再利用されず、新しいプランがコンパイルされます。 

そのため、新しいコンパイルの数が増えると、クエリのパフォーマンスが突然一時的に低下する可能性があります。 プラン キャッシュ内のキャッシュストアが消去されるたびに、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エラー ログに、以下の通知メッセージが記録されます。

> `SQL Server has encountered %d occurrence(s) of cachestore flush for the '%s' cachestore (part of plan cache) due to 'DBCC FREEPROCCACHE' or 'DBCC FREESYSTEMCACHE' operations.` 

このメッセージは、5 分以内にキャッシュがフラッシュされる限り、5 分間隔でログに記録されます。

次の再構成操作でもプロシージャ キャッシュはクリアされます。
-   access check cache bucket count  
-   access check cache quota  
-   clr enabled  
-   cost threshold for parallelism  
-   cross db ownership chaining  
-   index create memory  
-   max degree of parallelism  
-   max server memory  
-   max text repl size  
-   max worker threads  
-   min memory per query  
-   min server memory  
-   query governor cost limit  
-   query wait  
-   remote query timeout  
-   user options  
  
## <a name="result-sets"></a>結果セット  
WITH NO_INFOMSGS 句が指定されていない場合は次のメッセージが表示されます。"DBCC の実行が完了しました。 DBCC がエラー メッセージを出力した場合は、システム管理者に相談してください。"
  
## <a name="permissions"></a>アクセス許可  
適用対象: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]、[!INCLUDE[ssPDW](../../includes/sspdw-md.md)] 
- サーバーに対する ALTER SERVER STATE 権限が必要です。  

適用対象: [!INCLUDE[ssSDW](../../includes/sssdw-md.md)]
- DB_OWNER 固定サーバー ロールのメンバーシップが必要です。  

## <a name="general-remarks-for-sssdw-and-sspdw"></a>[!INCLUDE[ssSDW](../../includes/sssdw-md.md)] と [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] に関する全般的な解説  
複数の DBCC FREEPROCCACHE コマンドを同時に実行することができます。
[!INCLUDE[ssSDW](../../includes/sssdw-md.md)] または [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] では、プラン キャッシュをクリアすると、以前にキャッシュされたプランが再利用されず、クエリの実行で新しいプランがコンパイルされるため、、クエリのパフォーマンスが一時的に低下する可能性があります。 

計算ノードで DBCC FREEPROCCACHE (COMPUTE) を実行する場合にのみ、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] でクエリが再コンパイルされます。 [!INCLUDE[ssSDW](../../includes/sssdw-md.md)] または [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] の場合、制御ノードで生成される並列クエリ プランの再コンパイルは実行されません。
実行中に DBCC FREEPROCCACHE をキャンセルできます。
  
## <a name="limitations-and-restrictions-for-sssdw-and-sspdw"></a>[!INCLUDE[ssSDW](../../includes/sssdw-md.md)] および [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] の制限事項と制約事項  
DBCC FREEPROCCACHE はトランザクション内で実行できません。
DBCC FREEPROCCACHE は EXPLAIN ステートメント内でサポートされていません。
  
## <a name="metadata-for-sssdw-and-sspdw"></a>[!INCLUDE[ssSDW](../../includes/sssdw-md.md)] および [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] のメタデータ  
DBCC FREEPROCCACHE を実行すると、新しい行が sys.pdw_exec_requests システム ビューに追加されます。

## <a name="examples-ssnoversion"></a>例: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]  
  
### <a name="a-clearing-a-query-plan-from-the-plan-cache"></a>A. プラン キャッシュから特定のクエリ プランを削除する  
次の例では、クエリ プラン ハンドルを指定して、プラン キャッシュから特定のクエリ プランを削除します。 まず、この例のクエリがプラン キャッシュに含まれるようにするために、クエリを実行します。 次に、動的管理ビューの `sys.dm_exec_cached_plans` および `sys.dm_exec_sql_text` に対してクエリを実行し、このクエリのプラン ハンドルを取得します。 

その後、結果セットのプラン ハンドルの値を `DBCC FREEPROCACHE` ステートメントに挿入して、プラン キャッシュからそのプランのみを削除します。
  
```sql  
USE AdventureWorks2012;  
GO  
SELECT * FROM Person.Address;  
GO  
SELECT plan_handle, st.text  
FROM sys.dm_exec_cached_plans   
CROSS APPLY sys.dm_exec_sql_text(plan_handle) AS st  
WHERE text LIKE N'SELECT * FROM Person.Address%';  
GO  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]

```
plan_handle                                         text  
--------------------------------------------------  -----------------------------  
0x060006001ECA270EC0215D05000000000000000000000000  SELECT * FROM Person.Address;  
  
(1 row(s) affected)
 ```
 
```sql  
-- Remove the specific plan from the cache.  
DBCC FREEPROCCACHE (0x060006001ECA270EC0215D05000000000000000000000000);  
GO  
```  
  
### <a name="b-clearing-all-plans-from-the-plan-cache"></a>B. プラン キャッシュからすべてのプランを削除する  
次の例では、プラン キャッシュからすべての要素を削除します。 WITH `NO_INFOMSGS`句を指定して、情報メッセージが表示されないようにしています。
  
```sql  
DBCC FREEPROCCACHE WITH NO_INFOMSGS;  
```  
  
### <a name="c-clearing-all-cache-entries-associated-with-a-resource-pool"></a>C. リソース プールに関連付けられたすべてのキャッシュ エントリを削除する  
次の例では、指定したリソース プールに関連付けられているすべてのキャッシュ エントリを削除します。 最初に、`sys.dm_resource_governor_resource_pools` ビューに対してクエリを実行し、*pool_name* の値を取得します。
  
```sql  
SELECT * FROM sys.dm_resource_governor_resource_pools;  
GO  
DBCC FREEPROCCACHE ('default');  
GO  
```  
  
## <a name="examples-sssdw-and-sspdw"></a>例: [!INCLUDE[ssSDW](../../includes/sssdw-md.md)]、[!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="d-dbcc-freeproccache-basic-syntax-examples"></a>D. DBCC FREEPROCCACHE の基本的な構文の例  
次の例では、コンピューティング ノードからすべての既存のクエリ プラン キャッシュを削除します。 コンテキストを UserDbSales に設定すると、すべてのデータベースのコンピューティング ノードのクエリ プラン キャッシュが削除されます。 WITH NO_INFOMSGS 句は、情報メッセージが結果に表示されないようにします。  
  
```sql
USE UserDbSales;  
DBCC FREEPROCCACHE (COMPUTE) WITH NO_INFOMSGS;
```  
  
 次の例では、情報メッセージが結果に表示されることを除き、前の例と同じ結果が表示されます。  
  
```sql
USE UserDbSales;  
DBCC FREEPROCCACHE (COMPUTE);  
```  
  
情報メッセージが要求されて実行が成功すると、クエリの結果にはコンピューティング ノードごとに 1 行が含まれます。
  
### <a name="e-granting-permission-to-run-dbcc-freeproccache"></a>E. DBCC FREEPROCCACHE を実行する権限を許可します。  
次の例では、ログイン David に DBCC FREEPROCCACHE を実行するアクセス許可を付与します。  
  
```sql
GRANT ALTER SERVER STATE TO David; 
GO
```  
  
## <a name="see-also"></a>参照  
[DBCC &#40;Transact-SQL&#41;](../../t-sql/database-console-commands/dbcc-transact-sql.md)  
[リソース ガバナー](../../relational-databases/resource-governor/resource-governor.md)  
[ALTER DATABASE SCOPED CONFIGURATION (Transact-SQL)](../../t-sql/statements/alter-database-scoped-configuration-transact-sql.md)
  
  
