---
description: sys.dm_exec_query_optimizer_info (Transact-sql)
title: sys.dm_exec_query_optimizer_info (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- dm_exec_query_optimizer_info_TSQL
- dm_exec_query_optimizer_info
- sys.dm_exec_query_optimizer_info_TSQL
- sys.dm_exec_query_optimizer_info
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_exec_query_optimizer_info dynamic management view
ms.assetid: 1d72cef1-22d8-4ae0-91db-6694fe918c9e
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: a3e769d427581ea888fc0f6a226f5072c93bc3e8
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104750572"
---
# <a name="sysdm_exec_query_optimizer_info-transact-sql"></a>sys.dm_exec_query_optimizer_info (Transact-sql)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] クエリ オプティマイザーの操作に関する詳細な統計を返します。 ワークロードをチューニングするときにこのビューを使用すると、クエリの最適化に関する問題や改善点を特定できます。 たとえば、最適化の合計数、所要時間、および最終的なコストを使用して、現在のワークロードのクエリの最適化と、チューニング処理中に確認された変更を比較できます。 一部のカウンターでは、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の内部診断で使用するためだけに適用されるデータを提供します。 このようなカウンターには、"内部使用のみ" と記載してあります。  
  
> [!NOTE]  
>  またはからこれを呼び出すに [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] は [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] 、 **sys.dm_pdw_nodes_exec_query_optimizer_info** という名前を使用します。  
  
|名前|データ型|説明|  
|----------|---------------|-----------------|  
|**対抗**|**nvarchar (4000)**|オプティマイザーの統計イベントの名前。|  
|**occurrence**|**bigint**|このカウンターの最適化イベントの発生回数。|  
|**value**|**float**|イベント発生ごとの平均プロパティ値。|  
|**pdw_node_id**|**int**|**適用対象**: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 、 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]<br /><br /> このディストリビューションが配置されているノードの識別子。|  
  
## <a name="permissions"></a>権限  

で [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] は、 `VIEW SERVER STATE` 権限が必要です。   
SQL Database Basic、S0、S1 のサービス目標、およびエラスティックプール内のデータベースについては、 [サーバー管理者](/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database) アカウントまたは [Azure Active Directory 管理者](/azure/azure-sql/database/authentication-aad-overview#administrator-structure) アカウントが必要です。 その他のすべての SQL Database サービスの目的で `VIEW DATABASE STATE` は、データベースで権限が必要になります。   
    
## <a name="remarks"></a>解説  
 **sys.dm_exec_query_optimizer_info** には、次のプロパティ (カウンター) が含まれています。 発生した値はすべて累積され、システムの再起動時に0に設定されます。 値フィールドのすべての値は、システムの再起動時に NULL に設定されます。 平均を示す列のすべての値では、平均計算の分母として、同一行を基にした発生回数の値が使用されます。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]では、ユーザーとシステムが生成したクエリの両方を含め、 **dm_exec_query_optimizer_info** の変更を決定するときに、すべてのクエリの最適化が測定されます。 既にキャッシュされているプランの実行では、 **dm_exec_query_optimizer_info** の値は変更されません。最適化のみが重要です。  
  
|カウンタ|個数|値|  
|-------------|----------------|-----------|  
|最適化|最適化の合計数。|適用なし|  
|elapsed time|最適化の合計数。|個別のステートメント (クエリ) の最適化ごとの平均経過時間 (秒単位)。|  
|最終的なコスト|最適化の合計数。|内部コスト単位での最適化されたプランの平均推定コスト。|  
|簡易プラン|内部使用のみ|内部使用のみ|  
|tasks|内部使用のみ|内部使用のみ|  
|no plan|内部使用のみ|内部使用のみ|  
|検索0|内部使用のみ|内部使用のみ|  
|search 0 time|内部使用のみ|内部使用のみ|  
|search 0 tasks|内部使用のみ|内部使用のみ|  
|検索1|内部使用のみ|内部使用のみ|  
|1回検索|内部使用のみ|内部使用のみ|  
|1つのタスクの検索|内部使用のみ|内部使用のみ|  
|検索2|内部使用のみ|内部使用のみ|  
|search 2 time|内部使用のみ|内部使用のみ|  
|検索2タスク|内部使用のみ|内部使用のみ|  
|gain stage 0 to stage 1|内部使用のみ|内部使用のみ|  
|gain stage 1 to stage 2|内部使用のみ|内部使用のみ|  
|timeout|内部使用のみ|内部使用のみ|  
|memory limit exceeded|内部使用のみ|内部使用のみ|  
|stmt の挿入|INSERT ステートメントの最適化の数。|適用なし|  
|stmt の削除|DELETE ステートメントに対する最適化の数。|適用なし|  
|update stmt|UPDATE ステートメントに対する最適化の数。|適用なし|  
|contains subquery|少なくとも1つのサブクエリを含むクエリの最適化の数。|適用なし|  
|unnest failed|内部使用のみ|内部使用のみ|  
|表|最適化の合計数。|最適化された 1 つのクエリあたりの、参照テーブルの平均数。|  
|ヒント|ヒントが指定された回数。 カウントされるヒントには、JOIN、GROUP、UNION、および FORCE ORDER クエリヒント、FORCE PLAN set オプション、および結合ヒントがあります。|適用なし|  
|order hint|FORCE ORDER ヒントが指定された回数。|適用なし|  
|結合ヒント|結合のヒントによって結合アルゴリズムが強制的に適用された回数。|適用なし|  
|view reference|ビューがクエリで参照された回数。|適用なし|  
|remote query|4部構成の名前または OPENROWSET の結果を含むテーブルなど、クエリが少なくとも1つのリモートデータソースを参照した最適化の数。|適用なし|  
|最大 DOP|最適化の合計数。|最適化されたプランの平均有効 MAXDOP 値。 既定では、有効な MAXDOP は [ **並列処理の最大限度** ] サーバー構成オプションによって決定され、maxdop クエリヒントの値によって特定のクエリに対して上書きされる可能性があります。|  
|maximum recursion level|クエリ ヒントで 0 より大きい MAXRECURSION レベルが指定された最適化の数。|クエリヒントで最大再帰レベルが指定されている最適化の平均 MAXRECURSION レベル。|  
|indexed views loaded|内部使用のみ|内部使用のみ|  
|indexed views matched|1 つ以上のインデックス付きビューが一致した、最適化の数。|一致したビューの平均数。|  
|使用されるインデックス付きビュー|出力プラン内で照合された後に 1 つ以上のインデックス付きビューが使用されている、最適化の数。|使用されたビューの平均数。|  
|更新されたインデックス付きビュー|1 つ以上のインデックス付きビューを管理するプランを作成する DML ステートメントの最適化の数。|管理されるビューの平均数。|  
|動的カーソル要求|動的カーソルの要求が指定された最適化の数。|適用なし|  
|高速順方向カーソル要求|高速順方向カーソルの要求が指定された最適化の数。|適用なし|  
|マージ stmt|MERGE ステートメントの最適化の数。|適用なし|  
  
## <a name="examples"></a>例  
  
### <a name="a-viewing-statistics-on-optimizer-execution"></a>A. オプティマイザー実行の統計の表示  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスに対する、現在のオプティマイザー実行の統計を表示します。  
  
```  
SELECT * FROM sys.dm_exec_query_optimizer_info;  
```  
  
### <a name="b-viewing-the-total-number-of-optimizations"></a>B. 最適化の合計数を表示する  
 実行される最適化の数を確認できます。  
  
```  
SELECT occurrence AS Optimizations FROM sys.dm_exec_query_optimizer_info  
WHERE counter = 'optimizations';  
```  
  
### <a name="c-average-elapsed-time-per-optimization"></a>C. 最適化ごとの平均経過時間  
 最適化ごとの平均経過時間を表示します。  
  
```  
SELECT ISNULL(value,0.0) AS ElapsedTimePerOptimization  
FROM sys.dm_exec_query_optimizer_info WHERE counter = 'elapsed time';  
```  
  
### <a name="d-fraction-of-optimizations-that-involve-subqueries"></a>D. サブクエリを含む最適化の割合  
 サブクエリを含む最適化されたクエリの割合を表示します。  
  
```  
SELECT (SELECT CAST (occurrence AS float) FROM sys.dm_exec_query_optimizer_info WHERE counter = 'contains subquery') /  
       (SELECT CAST (occurrence AS float)   
        FROM sys.dm_exec_query_optimizer_info WHERE counter = 'optimizations')  
        AS ContainsSubqueryFraction;  
```  
  
## <a name="see-also"></a>参照  
 [動的管理ビューと動的管理関数 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)   
 [実行関連の動的管理ビューおよび関数 &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/execution-related-dynamic-management-views-and-functions-transact-sql.md)  
  
