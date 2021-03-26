---
title: sys.dm_db_tuning_recommendations (Transact-sql)
description: SQL Server と Azure SQL Database で、潜在的なパフォーマンスの問題と推奨される修正を見つける方法について説明します
ms.custom: ''
ms.date: 03/12/2021
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- sys.dm_db_tuning_recommendations
- dm_db_tuning_recommendations
- sys.dm_db_tuning_recommendations_TSQL
- dm_db_tuning_recommendations_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- database tuning recommendations feature [SQL Server], sys.dm_db_tuning_recommendations dynamic management view
- sys.dm_db_tuning_recommendations dynamic management view
author: jovanpop-msft
ms.author: jovanpop
monikerRange: =azuresqldb-current||>=sql-server-2017||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 44c4b9bbc975074d1852f0e7a0f074e3c3acc979
ms.sourcegitcommit: c242f423cc3b776c20268483cfab0f4be54460d4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/25/2021
ms.locfileid: "105551663"
---
# <a name="sysdm_db_tuning_recommendations-transact-sql"></a>sys.dm \_ db \_ チューニングに \_ 関する推奨事項 (transact-sql)
[!INCLUDE[sqlserver2017-asdb](../../includes/applies-to-version/sqlserver2017-asdb.md)]

  チューニングの推奨設定に関する詳細情報を返します。  
  
 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] では、動的管理ビューは、データベースの包含に影響する情報を公開することも、ユーザーがアクセスできる他のデータベースに関する情報を公開することもできません。 この情報を公開しないように、接続されたテナントに属していないデータを含むすべての行がフィルターで除外されます。

| **列名** | **データの種類** | **説明** |
| --- | --- | --- |
| **name** | **nvarchar (4000)** | 推奨事項の一意の名前。 |
| **type** | **nvarchar (4000)** | 推奨設定を生成した自動チューニングオプションの名前。たとえば、次のようになります。 `FORCE_LAST_GOOD_PLAN` |
| **reason** | **nvarchar (4000)** | この推奨事項が提供された理由。 |
| **有効 \_ 期限** | **datetime2** | この推奨事項が初めて生成されたとき。 |
| **最終 \_ 更新** | **datetime2** | この推奨事項が最後に生成された時刻。 |
| **状態** | **nvarchar (4000)** | 推奨事項の状態を説明する JSON ドキュメント。 次のフィールドを使用できます。<br />-   `currentValue` -推奨事項の現在の状態。<br />-   `reason` -推奨設定が現在の状態である理由を示す定数。|
| **\_実行可能な \_ アクション** | **bit** | 1 = 推奨事項は、スクリプトを使用してデータベースに対して実行でき [!INCLUDE[tsql_md](../../includes/tsql-md.md)] ます。<br />0 = データベースに対して推奨設定を実行することはできません (例: 情報のみ、または推奨事項を元に戻します)。 |
| **is \_ revertable \_ action** | **bit** | 1 = 推奨事項は、データベースエンジンによって自動的に監視および元に戻すことができます。<br />0 = 推奨事項を自動的に監視して元に戻すことはできません。 ほとんど &quot; の実行可能な &quot; アクションは revertable になり &quot; &quot; ます。 |
| **実行 \_ アクションの \_ 開始 \_ 時刻** | **datetime2** | 推奨事項が適用される日付。 |
| **\_アクション実行 \_ 期間** | **time** | 実行アクションの期間。 |
| **実行 \_ アクションの \_ 開始 \_ 者** | **nvarchar (4000)** | `User` = ユーザーは、推奨事項に手動で強制されたプランです。 <br /> `System` = システムに自動的に適用される推奨事項。 |
| **アクションの実行 \_ \_ 開始 \_ 時刻** | **datetime2** | 推奨事項が適用された日付。 |
| **\_アクションの \_ 開始 \_ 時刻を元に戻す** | **datetime2** | 推奨事項が元に戻された日付。 |
| **元に戻す \_ 操作の \_ 期間** | **time** | 元に戻す操作の期間。 |
| **元 \_ に戻す操作の \_ 開始 \_** | **nvarchar (4000)** | `User` = ユーザー手動で強制されていない推奨プラン。 <br /> `System` = システムは自動的に推奨設定を戻しました。 |
| **\_アクション \_ 開始 \_ 時刻を元に戻す** | **datetime2** | 推奨事項が元に戻された日付。 |
| **スコア** | **int** | 0-100 スケールでのこの推奨事項の推定値/影響 (より優れたもの) |
| **住所** | **nvarchar(max)** | 推奨事項についての詳細が記載された JSON ドキュメント。 次のフィールドを使用できます。<br /><br />`planForceDetails`<br />-    `queryId` - \_ 低下したクエリのクエリ id。<br />-    `regressedPlanId` -低下したプランの plan_id。<br />-   `regressedPlanExecutionCount` -回帰が検出されるまでの低下した plan を使用したクエリの実行回数。<br />-    `regressedPlanAbortedCount` -低下したプランの実行中に検出されたエラーの数。<br />-    `regressedPlanCpuTimeAverage` -回帰が検出される前に低下したクエリによって消費される平均 CPU 時間 (マイクロ秒)。<br />-    `regressedPlanCpuTimeStddev` -回帰が検出される前に低下したクエリによって消費される CPU 時間の標準偏差。<br />-    `recommendedPlanId` -強制する計画の plan_id。<br />-   `recommendedPlanExecutionCount`-回帰が検出される前に強制される必要があるプランを持つクエリの実行回数。<br />-    `recommendedPlanAbortedCount` -プランの実行中に強制される必要がある検出されたエラーの数。<br />-    `recommendedPlanCpuTimeAverage` -強制的に実行する必要がある (回帰が検出される前に計算された) プランで実行されたクエリによって消費される平均 CPU 時間 (マイクロ秒)。<br />-    `recommendedPlanCpuTimeStddev` 回帰が検出される前に低下したクエリによって消費される CPU 時間の標準偏差。<br /><br />`implementationDetails`<br />-  `method` -回帰を修正するために使用する必要があるメソッド。 値は常に `TSql` です。<br />-    `script` - [!INCLUDE[tsql_md](../../includes/tsql-md.md)] 推奨されるプランを強制するために実行するスクリプト。 |
  
## <a name="remarks"></a>解説  
 によって返される情報 `sys.dm_db_tuning_recommendations` は、データベースエンジンによってクエリパフォーマンスの潜在的な回帰が識別され、保存されない場合に更新されます。 推奨事項は、データベースエンジンが再起動されるまで保持されます。 Sys.dm_os_sys_info の列を使用して、 `sqlserver_start_time` データベースエンジンの最後の起動時刻を検索します。 [](sys-dm-os-sys-info-transact-sql.md)  サーバーの再利用後に保持する場合は、データベース管理者がチューニングの推奨設定のバックアップコピーを定期的に作成する必要があります。   

 列のフィールドには `currentValue` `state` 、次の値が含まれる場合があります。
 
 | Status | 説明 |
 |--------|-------------|
 | `Active` | 推奨事項はアクティブであり、まだ適用されていません。 ユーザーは推奨設定のスクリプトを取得し、手動で実行できます。 |
 | `Verifying` | 推奨事項はによって適用され [!INCLUDE[ssde_md](../../includes/ssde_md.md)] 、内部検証プロセスは、強制されたプランのパフォーマンスを低下したプランと比較します。 |
 | `Success` | 推奨事項が正常に適用されました。 |
 | `Reverted` | パフォーマンスを大幅に向上させることができないため、推奨事項は元に戻されます。 |
 | `Expired` | 推奨事項の有効期限が切れているため、もう適用できません。 |

[JSON ドキュメントの `state` 列に含まれるのは、現在の状態の推奨事項である理由を示しています。 [理由] フィールドの値は次のようになります。 

| 理由 | 説明 |
|--------|-------------|
| `SchemaChanged` | 参照されるテーブルのスキーマが変更されたため、推奨設定の有効期限が切れました。 新しいスキーマで新しいクエリプランの回帰が検出されると、新しい推奨事項が作成されます。 |
| `StatisticsChanged`| 参照されるテーブルの統計が変更されたため、推奨設定の有効期限が切れました。 新しい統計に基づいて新しいクエリプランの回帰が検出されると、新しい推奨事項が作成されます。 |
| `ForcingFailed` | クエリで推奨されるプランを強制することはできません。 `last_force_failure_reason` [Sys.query_store_plan](../../relational-databases/system-catalog-views/sys-query-store-plan-transact-sql.md)ビューでを見つけて、エラーの原因を見つけます。 |
| `AutomaticTuningOptionDisabled` | `FORCE_LAST_GOOD_PLAN` オプションは、検証プロセス中にユーザーによって無効にされます。 `FORCE_LAST_GOOD_PLAN` [ALTER database SET AUTOMATIC_TUNING](../../t-sql/statements/alter-database-transact-sql-set-options.md)使用してオプションを有効にするか &#40;transact-sql&#41;ステートメントを使用するか、または列のスクリプトを使用して手動でプランを強制して `details` ください。 |
| `UnsupportedStatementType` | クエリに対してプランを強制することはできません。 サポートされていないクエリの例としては、カーソルと `INSERT BULK` ステートメントがあります。 |
| `LastGoodPlanForced` | 推奨事項が正常に適用されました。 |
| `AutomaticTuningOptionNotEnabled`| [!INCLUDE[ssde_md](../../includes/ssde_md.md)] パフォーマンスが低下する可能性があることを確認しましたが、 `FORCE_LAST_GOOD_PLAN` オプションが有効になっていません。 [ALTER database SET AUTOMATIC_TUNING &#40;transact-sql&#41;](../../t-sql/statements/alter-database-transact-sql-set-options.md)を参照してください。 推奨設定を手動で適用するか、オプションを有効に `FORCE_LAST_GOOD_PLAN` します。 |
| `VerificationAborted`| 再起動またはクエリストアクリーンアップが原因で、検証プロセスが中止されました。 |
| `VerificationForcedQueryRecompile`| パフォーマンスが大幅に改善されないため、クエリが再コンパイルされます。 |
| `PlanForcedByUser`| ユーザーは [sp_query_store_force_plan &#40;transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-query-store-force-plan-transact-sql.md) プロシージャを使用して手動でプランを強制しています。 ユーザーが明示的に何らかのプランを強制する場合、データベースエンジンは推奨設定を適用しません。 |
| `PlanUnforcedByUser` | ユーザーは [sp_query_store_unforce_plan &#40;transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-query-store-unforce-plan-transact-sql.md) プロシージャを使用して、手動でプランを強制解除します。 ユーザーが明示的に推奨プランを元に戻したため、データベースエンジンは現在のプランをそのまま使用し、将来、何らかのプラン回帰が発生した場合に新しい推奨設定を生成します。 |
| `UserForcedDifferentPlan` | ユーザーは [sp_query_store_force_plan &#40;transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-query-store-force-plan-transact-sql.md) プロシージャを使用して手動で別のプランを強制しています。 ユーザーが明示的に何らかのプランを強制する場合、データベースエンジンは推奨設定を適用しません。 |
| `TempTableChanged` | プランで使用された一時テーブルが変更されます。 |

 列の統計情報に `details` は、ランタイムプランの統計情報 (現在の CPU 時間など) は表示されません。 推奨事項の詳細は、回帰の検出時に取得され、特定されたパフォーマンスの回帰の理由を説明し [!INCLUDE[ssde_md](../../includes/ssde_md.md)] ます。 およびを使用し `regressedPlanId` て `recommendedPlanId` [クエリストアカタログビュー](../../relational-databases/performance/how-query-store-collects-data.md) に対してクエリを実行し、正確なランタイムプランの統計情報を検索します。

## <a name="examples-of-using-tuning-recommendations-information"></a>チューニングの推奨設定情報の使用例  

### <a name="example-1"></a>例 1
次の例では、 [!INCLUDE[tsql](../../includes/tsql-md.md)] 特定のクエリに対して適切なプランを強制する、生成されたスクリプトを取得します。  
 
```sql
SELECT name, reason, score,
    JSON_VALUE(details, '$.implementationDetails.script') AS script,
    details.* 
FROM sys.dm_db_tuning_recommendations
CROSS APPLY OPENJSON(details, '$.planForceDetails')
    WITH (    [query_id] int '$.queryId',
            regressed_plan_id int '$.regressedPlanId',
            last_good_plan_id int '$.recommendedPlanId') AS details
WHERE JSON_VALUE(state, '$.currentValue') = 'Active';
```
### <a name="example-2"></a>例 2
次の例では、 [!INCLUDE[tsql](../../includes/tsql-md.md)] 特定のクエリに対して適切なプランを強制する、生成されたスクリプトと、推定されるゲインに関する追加情報を取得します。

```sql
SELECT reason, score,
      script = JSON_VALUE(details, '$.implementationDetails.script'),
      planForceDetails.*,
      estimated_gain = (regressedPlanExecutionCount + recommendedPlanExecutionCount)
                  *(regressedPlanCpuTimeAverage - recommendedPlanCpuTimeAverage)/1000000,
      error_prone = IIF(regressedPlanErrorCount > recommendedPlanErrorCount, 'YES','NO')
FROM sys.dm_db_tuning_recommendations
CROSS APPLY OPENJSON (Details, '$.planForceDetails')
    WITH (  [query_id] int '$.queryId',
            regressedPlanId int '$.regressedPlanId',
            recommendedPlanId int '$.recommendedPlanId',
            regressedPlanErrorCount int,
            recommendedPlanErrorCount int,
            regressedPlanExecutionCount int,
            regressedPlanCpuTimeAverage float,
            recommendedPlanExecutionCount int,
            recommendedPlanCpuTimeAverage float
          ) AS planForceDetails;
```

### <a name="example-3"></a>例 3
次の例では、 [!INCLUDE[tsql](../../includes/tsql-md.md)] 特定のクエリに対して適切なプランを強制する、生成されたスクリプトと、クエリストアに格納されているクエリテキストとクエリプランを含む追加情報を取得します。

```sql
WITH cte_db_tuning_recommendations
AS (SELECT reason,
        score,
        query_id,
        regressedPlanId,
        recommendedPlanId,
        current_state = JSON_VALUE(state, '$.currentValue'),
        current_state_reason = JSON_VALUE(state, '$.reason'),
        script = JSON_VALUE(details, '$.implementationDetails.script'),
        estimated_gain = (regressedPlanExecutionCount + recommendedPlanExecutionCount)
                * (regressedPlanCpuTimeAverage - recommendedPlanCpuTimeAverage)/1000000,
        error_prone = IIF(regressedPlanErrorCount > recommendedPlanErrorCount, 'YES','NO')
    FROM sys.dm_db_tuning_recommendations
    CROSS APPLY OPENJSON(Details, '$.planForceDetails')
    WITH ([query_id] int '$.queryId',
        regressedPlanId int '$.regressedPlanId',
        recommendedPlanId int '$.recommendedPlanId',
        regressedPlanErrorCount int,    
        recommendedPlanErrorCount int,
        regressedPlanExecutionCount int,
        regressedPlanCpuTimeAverage float,
        recommendedPlanExecutionCount int,
        recommendedPlanCpuTimeAverage float
        )
    )
SELECT qsq.query_id,
    qsqt.query_sql_text,
    dtr.*,
    CAST(rp.query_plan AS XML) AS RegressedPlan,
    CAST(sp.query_plan AS XML) AS SuggestedPlan
FROM cte_db_tuning_recommendations AS dtr
INNER JOIN sys.query_store_plan AS rp ON rp.query_id = dtr.query_id
    AND rp.plan_id = dtr.regressedPlanId
INNER JOIN sys.query_store_plan AS sp ON sp.query_id = dtr.query_id
    AND sp.plan_id = dtr.recommendedPlanId
INNER JOIN sys.query_store_query AS qsq ON qsq.query_id = rp.query_id
INNER JOIN sys.query_store_query_text AS qsqt ON qsqt.query_text_id = qsq.query_text_id;
```

推奨事項ビューの値のクエリに使用できる JSON 関数の詳細については、「」の「 [Json サポート](../json/json-data-sql-server.md) 」を参照してください [!INCLUDE[ssde_md](../../includes/ssde_md.md)] 。
  
## <a name="permissions"></a>アクセス許可  

`VIEW SERVER STATE`には権限が必要です [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] 。   
では、データベースに対する権限が必要です `VIEW DATABASE STATE` [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 。   

## <a name="see-also"></a>関連項目  
 [自動チューニング](../../relational-databases/automatic-tuning/automatic-tuning.md)   
 [sys.database_automatic_tuning_options &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-database-automatic-tuning-options-transact-sql.md)   
 [sys.database_query_store_options &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-database-query-store-options-transact-sql.md)   
 [JSON サポート](../json/json-data-sql-server.md) [sys.dm_os_sys_info &#40;transact-sql&#41;](sys-dm-os-sys-info-transact-sql.md)    