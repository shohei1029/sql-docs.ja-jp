---
description: CREATE WORKLOAD CLASSIFIER (Transact-SQL)
title: CREATE WORKLOAD CLASSIFIER (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/11/2020
ms.prod: sql
ms.prod_service: synapse-analytics
ms.reviewer: jrasnick
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- WORKLOAD CLASSIFIER
- WORKLOAD_CLASSIFIER_TSQL
- CREATE WORKLOAD CLASSIFIER
- CREATE_WORKLOAD_CLASSIFIER_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- CREATE WORKLOAD CLASSIFIER statement
ms.assetid: ''
author: ronortloff
ms.author: rortloff
monikerRange: =azure-sqldw-latest
ms.openlocfilehash: 5040da21c46a1f835f3a5744c7cd0bd1ff76a546
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104755402"
---
# <a name="create-workload-classifier-transact-sql"></a>CREATE WORKLOAD CLASSIFIER (Transact-SQL)

[!INCLUDE [asa](../../includes/applies-to-version/asa.md)]

ワークロード管理で使用する分類子オブジェクトを作成します。  分類子では、受信要求が分類子ステートメントの定義に指定されたパラメーターに基づいて、ワークロード グループに割り当てられます。  分類子は要求が送信されるごとに評価されます。  要求が分類子と一致しない場合、要求は既定のワークロード グループに割り当てられます。  既定のワークロード グループは、smallrc リソース クラスです。

> [!NOTE]
> ワークロード分類子は、sp_addrolemember リソース クラスの割り当ての代わりになります。  ワークロード分類子を作成した後、sp_droprolemember を実行して、冗長なリソース クラスのマッピングを削除します。

 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)。  
  
## <a name="syntax"></a>構文

```syntaxsql
CREATE WORKLOAD CLASSIFIER classifier_name  
WITH  
    (   WORKLOAD_GROUP = 'name'  
    ,   MEMBERNAME = 'security_account' 
[ [ , ] WLM_LABEL = 'label' ]  
[ [ , ] WLM_CONTEXT = 'context' ]  
[ [ , ] START_TIME = 'HH:MM' ]  
[ [ , ] END_TIME = 'HH:MM' ]  
  
[ [ , ] IMPORTANCE = { LOW | BELOW_NORMAL | NORMAL | ABOVE_NORMAL | HIGH }]) 
[;]
```
[!INCLUDE[synapse-analytics-od-unsupported-syntax](../../includes/synapse-analytics-od-unsupported-syntax.md)]

## <a name="arguments"></a>引数

 *classifier_name*  
 ワークロード分類子を識別する名前を指定します。  classifier_name は sysname です。  これは長さを最大で 128 文字とすることができ、インスタンス内では一意である必要があります。

 *WORKLOAD_GROUP* =  *'name'*    
 分類子の規則によって条件が満たされると、name によって要求がワークロード グループにマップされます。  name は sysname です。  これは長さを最大で 128 文字とすることができ、分類子作成時には有効なワークロード グループ名とする必要があります。

 使用可能なワークロード グループは、[sys.workload_management_workload_groups](../../relational-databases/system-catalog-views/sys-workload-management-workload-groups-transact-sql.md) カタログ ビューで見つけることができます。

 *MEMBERNAME* =  *'security_account'*     
 分類に使用するセキュリティ アカウント。  security_account は sysname です。既定値はありません。 security_account は、データベース ユーザー、データベース ロール、Azure Active Directory ログイン、または Azure Active Directory グループとすることができます。
 
 *WLM_LABEL*   
 要求を分類できるラベル値を指定します。  ラベルは、nvarchar(255) 型の省略可能なパラメーターです。  要求の [OPTION (LABEL)](/azure/sql-data-warehouse/sql-data-warehouse-develop-label) を使用して、分類子の構成を一致させます。

例:

```sql
CREATE WORKLOAD CLASSIFIER wcELTLoads WITH  
( WORKLOAD_GROUP = 'wgDataLoad'
 ,MEMBERNAME     = 'ELTRole'  
 ,WLM_LABEL      = 'dimension_loads' )

SELECT COUNT(*) 
  FROM DimCustomer
  OPTION (LABEL = 'dimension_loads')
```

*WLM_CONTEXT*  
要求を分類できるセッション コンテキスト値を指定します。  コンテキストは、nvarchar(255) 型の省略可能なパラメーターです。  セッション コンテキストを設定する要求を送信する前に、[ と同じ変数名と共に ](../../relational-databases/system-stored-procedures/sp-set-session-context-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)sys. sp_set_session_context`wlm_context` を使用します。

例:

```sql
CREATE WORKLOAD CLASSIFIER wcDataLoad WITH  
( WORKLOAD_GROUP = 'wgDataLoad'
 ,MEMBERNAME     = 'ELTRole'
 ,WLM_CONTEXT    = 'dim_load' )
 
--set session context
EXEC sys.sp_set_session_context @key = 'wlm_context', @value = 'dim_load'

--run multiple statements using the wlm_context setting
SELECT COUNT(*) FROM stg.daily_customer_load
SELECT COUNT(*) FROM stg.daily_sales_load

--turn off the wlm_context session setting
EXEC sys.sp_set_session_context @key = 'wlm_context', @value = null
```

*START_TIME* と *END_TIME*  
要求を分類できる start_time と end_time を指定します。  start_time と end_time はどちらも UTC タイムゾーンの HH:MM 形式です。  start_time と end_time は、一緒に指定する必要があります。

例:

```sql
CREATE WORKLOAD CLASSIFIER wcELTLoads WITH  
( WORKLOAD_GROUP = 'wgDataLoads'
 ,MEMBERNAME     = 'ELTRole'  
 ,START_TIME     = '22:00'
 ,END_TIME       = '02:00' )
```

*IMPORTANCE* = { LOW \| BELOW_NORMAL \| NORMAL \| ABOVE_NORMAL \| HIGH }  
要求の相対的な重要度を指定します。  重要度は次のいずれかです。

- LOW
- BELOW_NORMAL
- NORMAL (既定値)
- ABOVE_NORMAL
- HIGH  

重要度が指定されていない場合は、ワークロード グループの重要度の設定が使用されます。  既定のワークロード グループの重要度は NORMAL です。  重要度は要求がスケジュールされる順番に影響します。それによって、リソースおよびロックへの最初のアクセスが指定されます。

## <a name="classification-parameter-weighting"></a>分類パラメーターの重み付け

要求は、複数の分類子と照合できます。  分類子パラメーターには重み付けがあります。  ワークロード グループと重要度の割り当てには、重み付けが高く、一致する分類子が使用されます。  重み付けは次のようになります。

|分類子パラメーター |Weight   |
|---------------------|---------|
|User                 |64       |
|ROLE                 |32       |
|WLM_LABEL            |16       |
|WLM_CONTEXT          |8        |
|START_TIME/END_TIME  |4        |

次の分類子構成について考えてみます。

```sql
CREATE WORKLOAD CLASSIFIER classifierA WITH  
( WORKLOAD_GROUP = 'wgDashboards'  
 ,MEMBERNAME     = 'userloginA'
 ,IMPORTANCE     = HIGH
 ,WLM_LABEL      = 'salereport' )

CREATE WORKLOAD CLASSIFIER classifierB WITH  
( WORKLOAD_GROUP = 'wgUserQueries'  
 ,MEMBERNAME     = 'userloginA'
 ,IMPORTANCE     = LOW
 ,START_TIME     = '18:00'
 ,END_TIME       = '07:00' )
```

ユーザー `userloginA` は両方の分類子で構成されています。  userloginA が UTC の午後 6 時から午前 7 時までの間に `salesreport` と等しいラベルを持つクエリを実行すると、要求は wgDashboards ワークロード グループに分類され、重要度が HIGH になります。  時間外報告の重要度が LOW である wgUserQueries に要求が分類されることが予想されますが、WLM_LABEL の重み付けは START_TIME/END_TIME よりも高くなります。  classifierA の重み付けは 80 (ユーザーの 64 + WLM_LABEL の 16) です。  classifierB の重み付けは 68 (ユーザーの 64 + START_TIME/END_TIME の 4) です。  この場合、WLM_LABEL を classifierB に追加できます。

 詳細については、[ワークロードの重み付け](/azure/sql-data-warehouse/sql-data-warehouse-workload-classification#classification-weighting)に関するページをご覧ください。

## <a name="permissions"></a>アクセス許可

 CONTROL DATABASE アクセス許可が必須です。  
  
## <a name="examples"></a>例

 次の例は、`wgcELTRole` という名前のワークロード分類子を作成する方法を示します。 この例では、ワークロード グループ staticrc20 およびユーザー `ELTRole` が使用され、重要度が `above_normal` に設定されています。

```sql
CREATE WORKLOAD CLASSIFIER wgcELTRole
  WITH (WORKLOAD_GROUP = 'staticrc20'
       ,MEMBERNAME = 'ELTRole'
      ,IMPORTANCE = above_normal);
```

## <a name="see-also"></a>参照

[[!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] 分類](/azure/sql-data-warehouse/sql-data-warehouse-workload-classification)</br>
[DROP WORKLOAD CLASSIFIER &#40;Transact-SQL&#41;](../../t-sql/statements/drop-workload-classifier-transact-sql.md)</br>
[sys.workload_management_workload_classifiers](../../relational-databases/system-catalog-views/sys-workload-management-workload-classifiers-transact-sql.md)</br>
[sys.workload_management_workload_classifier_details](../../relational-databases/system-catalog-views/sys-workload-management-workload-classifier-details-transact-sql.md)

