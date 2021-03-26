---
title: 自動チューニング
description: SQL Server と Azure SQL Database での自動チューニングについて説明します。
ms.custom: fasttrack-edit
ms.date: 03/12/2021
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: ''
ms.topic: conceptual
helpviewer_keywords:
- performance tuning [SQL Server]
- automatic tuning
- aprc
- automatic plan regression correction
- last known good plan
author: jovanpop-msft
ms.author: jovanpop
monikerRange: =azuresqldb-current||>=sql-server-2017||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 6ea61597a7f9874b7438392167380c5f01b11527
ms.sourcegitcommit: c242f423cc3b776c20268483cfab0f4be54460d4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/25/2021
ms.locfileid: "105551660"
---
# <a name="automatic-tuning"></a>自動チューニング
[!INCLUDE[sqlserver2017-asdb](../../includes/applies-to-version/sqlserver2017-asdb.md)]

自動チューニングは、潜在的なクエリ パフォーマンスの問題に関する洞察を提供し、解決策を推奨して、特定された問題を自動的に解決するデータベース機能です。

で導入された自動チューニングは [!INCLUDE[sssql17-md](../../includes/sssql17-md.md)] 、潜在的なパフォーマンスの問題が検出されるたびに通知を行い、是正措置を適用したり、パフォーマンスの問題を自動的に修正したりできるようにし [!INCLUDE[ssde_md](../../includes/ssde_md.md)] ます。 自動チューニングを [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 使用すると、 **クエリ実行プランの選択の回帰** によって発生するパフォーマンスの問題を特定し、修正することができます。 また、自動チューニングでは [!INCLUDE[ssazure_md](../../includes/ssazure_md.md)] 、必要なインデックスを作成し、未使用のインデックスを削除します。 クエリ実行プランの詳細については、「 [実行プラン](../../relational-databases/performance/execution-plans.md)」を参照してください。

は、 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] データベースで実行されるクエリを監視し、ワークロードのパフォーマンスを自動的に向上させます。 には、 [!INCLUDE[ssde_md](../../includes/ssde_md.md)] データベースをワークロードに動的に適合させることによって、クエリのパフォーマンスを自動的に調整して向上させることができるインテリジェンスメカニズムが組み込まれています。 次の2つの自動チューニング機能を使用できます。

-    **自動プラン修正** で [は、パラメーターの感度やパラメータースニッフィング](../../relational-databases/query-processing-architecture-guide.md#ParamSniffing) の問題など、問題のあるクエリの実行プランが特定され、回帰が発生する前に最後の既知の良好なプランを強制することによって、クエリ実行プランに関連するパフォーマンスの問題が修正されます。 **適用対象**:[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (開始値 [!INCLUDE[sssql17-md](../../includes/sssql17-md.md)]) および [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]

-    **自動インデックス管理** では、データベースに追加する必要があるインデックスと、削除する必要があるインデックスが識別されます。 **適用対象**: [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]

## <a name="why-automatic-tuning"></a>自動チューニングを行う理由

従来のデータベース管理の主な作業の3つは、ワークロードの監視、重要な [!INCLUDE[tsql_md](../../includes/tsql-md.md)] クエリの特定、パフォーマンス向上のために追加する必要があるインデックスの識別、パフォーマンス向上のために削除される可能性があるインデックスの識別です。 では、 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 監視する必要があるクエリとインデックスについて詳細な洞察を得ることができます。 ただし、データベースを常に監視することは厄介で面倒なタスクであり、多数のデータベースを処理する場合は特にそうなります。 非常に多くのデータベースを管理することは、効率的に行うことができない場合があります。 データベースの監視とチューニングを手動で行うのではなく、監視とチューニングアクションの一部を、 [!INCLUDE[ssde_md](../../includes/ssde_md.md)] 自動チューニング機能を使用してに委任することを検討してください。

### <a name="how-does-automatic-tuning-work"></a>自動チューニングのしくみ

自動チューニングは、継続的な監視と分析のプロセスであり、ワークロードの特性を常に把握し、潜在的な問題と改善点を識別します。

![自動チューニング プロセス](./media/tuning-process.png)

このプロセスにより、ワークロードのパフォーマンスを向上させる可能性のあるインデックスとプラン、およびワークロードに影響を与えるインデックスを見つけることで、データベースをワークロードに動的に適応させることができます。 これらの結果に基づいて、自動チューニングによって、ワークロードのパフォーマンスを向上させるチューニング操作が適用されます。 また、自動チューニングでは、ワークロードのパフォーマンスが向上するように変更を実装した後に、データベースのパフォーマンスが継続的に監視されます。 パフォーマンスを向上させなかったアクションは、自動的に元に戻されます。 この検証プロセスは、自動チューニングによって行われたすべての変更がワークロードの全体的なパフォーマンスを低下させないようにするための重要な機能です。

## <a name="automatic-plan-correction"></a>自動プラン修正

自動プラン修正は、 **実行プランの選択の回帰** を識別する自動チューニング機能であり、前回正常起動時のプランを強制することによって自動的に問題を修正します。 クエリ実行プランとクエリオプティマイザーの詳細については、「 [クエリ処理アーキテクチャガイド](../../relational-databases/query-processing-architecture-guide.md)」を参照してください。

> [!IMPORTANT]
> 自動プラン修正は、ワークロード追跡のためにデータベースで有効になっているクエリストアに依存します。 

### <a name="what-is-execution-plan-choice-regression"></a>実行プランの選択の回帰とは

では、 [!INCLUDE[ssdenoversion_md](../../includes/ssdenoversion_md.md)] さまざまな実行プランを使用してクエリを実行でき [!INCLUDE[tsql_md](../../includes/tsql-md.md)] ます。 クエリプランは、統計、インデックス、およびその他の要因によって異なります。 クエリの実行に使用する最適なプランは、 [!INCLUDE[tsql_md](../../includes/tsql-md.md)] これらの要因の変化に応じて、時間の経過と共に変わる可能性があります。 場合によっては、新しいプランが前のプランよりも優れているとは限りません。新しいプランでは、 [パラメーターの感度やパラメータースニッフィング](../../relational-databases/query-processing-architecture-guide.md#ParamSniffing) に関連する問題など、パフォーマンスの回帰が発生する可能性があります。 

 ![クエリ実行プランの選択の回帰](media/plan-choice-regression.png "クエリ実行プランの選択の回帰") 

プラン選択の回帰が発生した場合は常に、以前の良好なプランを見つけて、現在のプランの代わりに使用することをお勧めします。 これを行うには、プロシージャを使用し `sp_query_store_force_plan` ます。 のは、 [!INCLUDE[ssde_md](../../includes/ssde_md.md)] [!INCLUDE[sssql17-md](../../includes/sssql17-md.md)] 低下したプランと推奨される是正措置に関する情報を提供します。 また、では、 [!INCLUDE[ssde_md](../../includes/ssde_md.md)] このプロセスを完全に自動化し、 [!INCLUDE[ssde_md](../../includes/ssde_md.md)] 計画の変更に関連する問題を修正することができます。

> [!IMPORTANT]
> ベースラインがキャプチャされた後のデータベース互換性レベルのアップグレードのスコープで自動プラン修正を使用して、ワークロードアップグレードのリスクを自動的に軽減する必要があります。 このユースケースの詳細については、「 [新しい SQL Server へのアップグレード中にパフォーマンスの安定性を維持する](../../relational-databases/performance/query-store-usage-scenarios.md#CEUpgrade)」を参照してください。 

### <a name="automatic-plan-choice-correction"></a>自動プラン選択の修正

は、 [!INCLUDE[ssde_md](../../includes/ssde_md.md)] プラン選択の回帰が検出されるたびに、最新の既知の正常なプランに自動的に切り替えることができます。

![クエリ実行プランの選択の修正](media/force-last-good-plan.png "クエリ実行プランの選択の修正") 

は、不適切なプラン [!INCLUDE[ssde_md](../../includes/ssde_md.md)] の代わりに使用する必要があるプランを含め、プランの選択の可能性のある回帰を自動的に検出します。 によって、 [!INCLUDE[ssde_md](../../includes/ssde_md.md)] 回帰が発生する前に最後の既知の正常なプランが適用されると、強制されたプランのパフォーマンスが自動的に監視されます。 強制プランが低下したプランよりも適切でない場合、新しいプランは強制されず、は [!INCLUDE[ssde_md](../../includes/ssde_md.md)] 新しいプランをコンパイルします。 によって、強制され [!INCLUDE[ssde_md](../../includes/ssde_md.md)] たプランが低下したプランよりも優れていることが確認されると、強制されたプランが保持されます。 再コンパイルが行われるまで (たとえば、次の統計の更新やスキーマの変更で) 保持されます。 プランの強制および強制可能なプランの種類の詳細については、「 [プランの強制制限](../../relational-databases/system-catalog-views/sys-query-store-plan-transact-sql.md#plan-forcing-limitations)」を参照してください。

> [!NOTE]
> [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]プランの強制実行を確認する前にインスタンスを再起動すると、そのプランは自動的に強制解除されます。 それ以外の場合、プランの強制は再起動時に保持され [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。

### <a name="enabling-automatic-plan-choice-correction"></a>自動プラン選択修正の有効化

データベースごとの自動チューニングを有効にし、プラン変更の回帰が検出されるたびに、最新の良好なプランを強制的に適用するように指定することができます。 自動調整は、次のコマンドを使って有効にします。

```sql   
ALTER DATABASE <yourDatabase>
SET AUTOMATIC_TUNING ( FORCE_LAST_GOOD_PLAN = ON ); 
```

このオプションを有効にすると、では、 [!INCLUDE[ssde_md](../../includes/ssde_md.md)] 推定 CPU 到達率が10秒を超える場合、または新しいプランのエラー数が推奨されるプランのエラー数よりも多い場合、によって自動的に推奨設定が強制されます。また、強制されたプランが現在のプランより優れていることを確認します。

### <a name="alternative---manual-plan-choice-correction"></a>代替 - 手動プラン選択修正

自動チューニングを使用しない場合、ユーザーは定期的にシステムを監視し、低下したのあるクエリを探す必要があります。 プランに低下したがある場合、ユーザーは以前の良好なプランを検索し、プロシージャを使用して、現在のプランではなく強制的に実行する必要があり `sp_query_store_force_plan` ます。 統計やインデックスの変更が原因で古いプランが無効になる可能性があるため、前回の既知の良好なプランを強制的に適用することをお勧めします。 前回正常起動時のプランを強制的に実行するユーザーは、強制プランを使用して実行されるクエリのパフォーマンスを監視し、強制されたプランが想定どおりに動作することを確認する必要があります。 監視と分析の結果に応じて、プランを強制的に適用するか、ユーザーがクエリを最適化する別の方法 (書き直しなど) を見つける必要があります。 は [!INCLUDE[ssde_md](../../includes/ssde_md.md)] 最適なプランを適用できる必要があるため、手動で強制されたプランを強制的に強制しないようにする必要があります。 ユーザーまたは DBA は、最終的にはプロシージャを使用してプランを強制解除し、最適なプランを検索できるようにする必要があり `sp_query_store_unforce_plan` [!INCLUDE[ssde_md](../../includes/ssde_md.md)] ます。 

> [!TIP]
> または、[ **強制されたプランを含むクエリ** クエリストア] ビューを使用して、プランを検索して強制しないようにします。

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] クエリストアのパフォーマンスを監視し、問題を修正するために必要なすべてのビューと手順について説明します。

では [!INCLUDE[sssql15-md](../../includes/sssql16-md.md)] 、クエリストアシステムビューを使用して、プラン選択の回帰を見つけることができます。 以降では、によって、 [!INCLUDE[sssql17-md](../../includes/sssql17-md.md)] [!INCLUDE[ssde_md](../../includes/ssde_md.md)] プランの選択の回帰の可能性が検出され、 [sys.dm_db_tuning_recommendations &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-tuning-recommendations-transact-sql.md) DMV で適用される推奨の操作が示されます。 DMV には、問題に関する情報、問題の重要度、および特定されたクエリ、低下したプランの ID、比較の基準として使用されたプランの ID、および [!INCLUDE[tsql_md](../../includes/tsql-md.md)] 問題を解決するために実行できるステートメントなどの詳細が表示されます。

| 型 | description | DATETIME | score | details | ... |
| --- | --- | --- | --- | --- | --- |
| `FORCE_LAST_GOOD_PLAN` | CPU 時間が4ミリ秒から14ミリ秒に変更されました | 3/17/2017 | 83 | `queryId` `recommendedPlanId` `regressedPlanId` `T-SQL` |   |
| `FORCE_LAST_GOOD_PLAN` | CPU 時間が37ミリ秒から84ミリ秒に変更されました | 3/16/2017 | 26 | `queryId` `recommendedPlanId` `regressedPlanId` `T-SQL` |   |

このビューのいくつかの列について、次の一覧で説明します。
 - 推奨される操作の種類 `FORCE_LAST_GOOD_PLAN` 。
 - [!INCLUDE[ssde_md](../../includes/ssde_md.md)]このプランが変更されたと考えられる場合のパフォーマンスの向上に関する情報が含まれている説明。
 - 可能性のある回帰が検出された日時。
 - この推奨事項のスコア。
 - 検出されたプランの ID、低下したプランの id、問題を修正するために必要なプランの ID、 [!INCLUDE[tsql_md](../../includes/tsql-md.md)] 問題を修正するために適用される可能性のあるスクリプトなどの問題について詳しく説明します。詳細は [JSON 形式](../json/json-data-sql-server.md)で格納されます。

次のクエリを使用して、問題を修正するスクリプトと、推定されるゲインに関する追加情報を取得します。

```sql   
SELECT reason, score,
      script = JSON_VALUE(details, '$.implementationDetails.script'),
      planForceDetails.*,
      estimated_gain = (regressedPlanExecutionCount + recommendedPlanExecutionCount)
                  * (regressedPlanCpuTimeAverage - recommendedPlanCpuTimeAverage)/1000000,
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

[!INCLUDE[ssresult-md](../../includes/ssresult-md.md)]     

| reason | score | script | クエリ \_ id | 現在のプラン \_ id | 推奨されるプラン \_ id | 推定 \_ ゲイン | エラーが発生し \_ やすい
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| CPU 時間が3ミリ秒から46ミリ秒に変更されました | 36 | EXEC sp \_ クエリ \_ ストア \_ force \_ プラン 12, 17; | 12 | 28 | 17 | 11.59 | 0

列は、現在のプランでは `estimated_gain` なくクエリの実行に推奨プランを使用する場合に保存される推定秒数を表します。 ゲインが10秒を超える場合は、現在のプランではなく、推奨されるプランを強制する必要があります。 現在のプランで、推奨されるプランよりも多くのエラー (タイムアウトや中断された実行など) が発生した場合、列は `error_prone` 値に設定され `YES` ます。 エラーが発生しやすい計画は、現在のプランではなく、推奨プランを強制する必要があるもう1つの理由です。

には [!INCLUDE[ssde_md](../../includes/ssde_md.md)] 計画選択の回帰を識別するために必要なすべての情報が用意されていますが、継続的な監視とパフォーマンスの問題の修正は面倒なプロセスになることがあります。 自動チューニングでは、このプロセスがはるかに簡単になります。

> [!NOTE]
> DMV のデータ `sys.dm_db_tuning_recommendations` は、データベースエンジンの再起動後に保持されません。 Sys.dm_os_sys_info の列を使用して、 `sqlserver_start_time` データベースエンジンの最後の起動時刻を検索します。 [](../../relational-databases/system-dynamic-management-views/sys-dm-os-sys-info-transact-sql.md)   

## <a name="automatic-index-management"></a>インデックスの自動管理

で [!INCLUDE[ssazure_md](../../includes/ssazure_md.md)] は、 [!INCLUDE[ssazure_md](../../includes/ssazure_md.md)] ワークロードについて学習し、常に最適なインデックスを作成することにより、インデックスの管理が簡単になります。 適切なインデックスの設計は、ワークロードの最適なパフォーマンスにとって不可欠であり、インデックスの自動管理はインデックスを最適化するのに役立ちます。 インデックスの自動管理では、正しくインデックスが付けられていないデータベースでのパフォーマンスの問題を修正することも、既存のデータベース スキーマのインデックスを維持および改善することもできます。 での自動チューニングで [!INCLUDE[ssazure_md](../../includes/ssazure_md.md)] は、次のアクションが実行されます。

 - テーブルからデータを読み取るクエリのパフォーマンスを向上させることができるインデックスを識別し [!INCLUDE[tsql_md](../../includes/tsql-md.md)] ます。
 - 削除される可能性のある長期間使用されなかった冗長なインデックスまたはインデックスを識別します。 不要なインデックスを削除すると、テーブル内のデータを更新するクエリのパフォーマンスが向上します。

### <a name="why-do-you-need-index-management"></a>インデックスの管理が必要な理由

インデックスを使用すると、テーブルからデータを読み取るクエリの一部が高速化されますが、データを更新するクエリの速度が低下する可能性があります。 インデックスを作成するタイミングと、インデックスに含める必要がある列を慎重に分析する必要があります。 一部のインデックスはしばらくすると必要でなくなる場合があります。 そのため、利点が得られないインデックスを定期的に特定して削除する必要があります。 未使用のインデックスを無視すると、データを読み取るクエリの恩恵を受けることなく、データを更新するクエリのパフォーマンスが低下します。 また、未使用のインデックスはシステムの全体のパフォーマンスに影響します。これは、追加の更新に不要なログが必要になるためです。

テーブルからデータを読み取るクエリのパフォーマンスを向上させ、更新への影響を最小限に抑える最適なインデックスのセットを見つけるには、継続的で複雑な分析が必要になる場合があります。

[!INCLUDE[ssazure_md](../../includes/ssazure_md.md)] では、クエリを分析し、現在のワークロードに最適なインデックスを特定し、削除が必要になる可能性のあるインデックスを識別する、組み込みのインテリジェンスおよび高度なルールを使用します。 Azure SQL Database を使用すると、他のクエリへの影響を最小限に抑えながら、データを読み取るクエリを最適化するために必要な最小限のインデックスセットが確保されます。

### <a name="automatic-index-management"></a>インデックスの自動管理

検出に加え、で [!INCLUDE[ssazure_md](../../includes/ssazure_md.md)] は、特定された推奨事項を自動的に適用することができます。 組み込みのルールによってデータベースのパフォーマンスが向上する場合は、インデックスを自動的に管理することができ [!INCLUDE[ssazure_md](../../includes/ssazure_md.md)] ます。

で自動チューニングを有効に [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] し、自動チューニング機能でワークロードを完全に管理できるようにするには、「 [Azure portal を使用した Azure SQL Database での自動チューニングの有効化](/azure/sql-database/sql-database-automatic-tuning-enable)」を参照してください。

が [!INCLUDE[ssazure_md](../../includes/ssazure_md.md)] CREATE index または DROP INDEX 推奨設定を適用すると、インデックスの影響を受けるクエリのパフォーマンスが自動的に監視されます。 影響を受けるクエリのパフォーマンスが向上した場合にのみ、新しいインデックスが保持されます。 インデックスが存在しないために実行速度が遅いクエリがある場合、削除されたインデックスは自動的に再作成されます。

### <a name="automatic-index-management-considerations"></a>インデックスの自動管理に関する考慮事項

で必要なインデックスを作成するために必要な操作は [!INCLUDE[ssazure_md](../../includes/ssazure_md.md)] 、リソースを消費する可能性があり、ワークロードのパフォーマンスに影響を一時的にます。 インデックスの作成によるワークロードのパフォーマンスへの影響を最小限に抑えるため、では、 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]  任意のインデックス管理操作に適した時間枠が検索されます。 チューニングアクションは、データベースがワークロードを実行するためのリソースを必要とし、メンテナンスタスクに使用できる未使用のリソースがデータベースにある場合に再起動されると、延期されます。 インデックスの自動管理の 1 つの重要な機能は、アクションの検証です。 が [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]  インデックスを作成または削除すると、監視プロセスによってワークロードのパフォーマンスが分析され、アクションによって全体的なパフォーマンスが向上したことが確認されます。 大幅な改善が行われなかった場合は、すぐに操作が元に戻されます。 これに [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]  より、自動チューニングアクションがワークロードのパフォーマンスに悪影響を与えないようにします。 自動チューニングによって作成されたインデックスは、基になるスキーマでのメンテナンス操作に対して透過的です。 自動的に作成されたインデックスがあることで、列の削除や名前変更などのスキーマ変更がブロックされることはありません。 によって自動的に作成されたインデックス [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]  は、関連するテーブルまたは列が削除されるとすぐに削除されます。

### <a name="alternative---manual-index-management"></a>代替-手動によるインデックス管理

自動インデックス管理を使用しない場合、ユーザーまたは DBA は、 [transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-missing-index-details-transact-sql.md) ビューに手動で &#40;sys.dm_db_missing_index_details クエリを実行するか、のパフォーマンスダッシュボードレポートを使用し [!INCLUDE[ssManStudio](../../includes/ssManStudio-md.md)] て、パフォーマンスを向上させる可能性のあるインデックスを検索し、このビューに示された詳細を使用してインデックスを作成し、クエリのパフォーマンスを手動で監視する必要があります。 削除する必要があるインデックスを見つけるために、ユーザーはインデックスの操作の使用状況の統計を監視して、使用頻度の低いインデックスを検索する必要があります。

[!INCLUDE[ssazure_md](../../includes/ssazure_md.md)] このプロセスを簡略化します。 [!INCLUDE[ssazure_md](../../includes/ssazure_md.md)] ワークロードを分析し、新しいインデックスを使用してより高速に実行できるクエリを識別し、未使用または重複したインデックスを識別します。 変更する必要があるインデックスの識別の詳細については、「[Azure Portal を使用した SQL Database Advisor](/azure/sql-database/sql-database-advisor-portal)」参照してください。

## <a name="see-also"></a>関連項目  
 [ALTER DATABASE SET AUTOMATIC_TUNING &#40;Transact-sql&#41;](../../t-sql/statements/alter-database-transact-sql-set-options.md)   
 [sys.database_automatic_tuning_options &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-database-automatic-tuning-options-transact-sql.md)  
 [sys.dm_db_tuning_recommendations &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-tuning-recommendations-transact-sql.md)   
 [sys.dm_db_missing_index_details &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-missing-index-details-transact-sql.md)   
 [sp_query_store_force_plan &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-query-store-force-plan-transact-sql.md)     
 [sp_query_store_unforce_plan &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-query-store-unforce-plan-transact-sql.md)           
 [sys.database_query_store_options &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-database-query-store-options-transact-sql.md)   
 [sys.dm_os_sys_info &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-os-sys-info-transact-sql.md)    
 [JSON 関数](../json/json-data-sql-server.md)    
 [実行プラン](../../relational-databases/performance/execution-plans.md)    
 [パフォーマンスの監視とチューニング](../../relational-databases/performance/monitor-and-tune-for-performance.md)     
 [パフォーマンス監視およびチューニング ツール](../../relational-databases/performance/performance-monitoring-and-tuning-tools.md)     
 [クエリのストアを使用した、パフォーマンスの監視](../../relational-databases/performance/monitoring-performance-by-using-the-query-store.md)  
 [クエリ調整アシスタント](../../relational-databases/performance/upgrade-dbcompat-using-qta.md)    