---
description: sys.dm_exec_plan_attributes (Transact-SQL)
title: sys.dm_exec_plan_attributes (Transact-SQL)
ms.custom: ''
ms.date: 02/24/2021
ms.prod: sql
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- sys.dm_exec_plan_attributes_TSQL
- dm_exec_plan_attributes_TSQL
- dm_exec_plan_attributes
- sys.dm_exec_plan_attributes
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_exec_plan_attributes dynamic management function
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 7ac93d0225cde8b2b24647f6de672fcfeba2f31f
ms.sourcegitcommit: bf7577b3448b7cb0e336808f1112c44fa18c6f33
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104611043"
---
# <a name="sysdm_exec_plan_attributes-transact-sql"></a>sys.dm_exec_plan_attributes (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  プランハンドルで指定されたプランのプラン属性ごとに1行の値を返します。 このテーブル値関数を使用すると、キャッシュキー値やプランの現在の同時実行数など、特定のプランに関する詳細情報を取得できます。  
  
> [!NOTE]  
>  この関数によって返される一部の情報は、 [sys.syscacheobjects](../../relational-databases/system-compatibility-views/sys-syscacheobjects-transact-sql.md) の下位互換性ビューにマップされます。

## <a name="syntax"></a>構文  
```syntaxsql
sys.dm_exec_plan_attributes ( plan_handle )  
```  
  
## <a name="arguments"></a>引数  
 *plan_handle*  
 実行され、そのプランがプランキャッシュ内に存在するバッチのクエリプランを一意に識別します。 *plan_handle* は **varbinary (64)** です。 プランハンドルは [sys.dm_exec_cached_plans](../../relational-databases/system-dynamic-management-views/sys-dm-exec-cached-plans-transact-sql.md) 動的管理ビューから取得できます。  
  
## <a name="table-returned"></a>返されるテーブル  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|属性 (attribute)|**varchar(128)**|このプランに関連付けられている属性の名前。 このテーブルのすぐ下の表は、使用可能な属性とそのデータ型とその説明を示しています。|  
|value|**sql_variant**|このプランに関連付けられている属性の値。|  
|is_cache_key|**bit**|プランのキャッシュ参照キーの一部として属性を使用するかどうかを示します。|  

上記の表では、 **属性** の値は次のようになります。

|属性|データ型|説明|  
|---------------|---------------|-----------------|  
|set_options|**int**|プランをコンパイルしたオプションの値を示します。|  
|objectid|**int**|キャッシュ内のオブジェクトを検索するために使用される主キーの1つ。 これは、データベースオブジェクト (プロシージャ、ビュー、トリガーなど) のために、 [sys. オブジェクト](../../relational-databases/system-catalog-views/sys-objects-transact-sql.md) に格納されているオブジェクト ID です。 "アドホック プラン" または "準備されたプラン" では、バッチ テキストの内部ハッシュです。|  
|dbid|**int**|プランが参照するエンティティを含むデータベースの ID を示します。<br /><br /> アドホックまたは準備されたプランの場合は、バッチの実行元のデータベース ID です。|  
|dbid_execute|**int**|**リソース** データベースに格納されているシステムオブジェクトの場合、キャッシュされたプランの実行元のデータベース ID。 その他の場合は 0 になります。|  
|user_id|**int**|値-2 は、送信されたバッチが暗黙的な名前解決に依存せず、異なるユーザー間で共有できることを示します。 可能であればこの方法の使用をお勧めします。 他の値は、データベースのクエリを送っているユーザーのユーザー ID を示します。| 
|language_id|**smallint**|キャッシュオブジェクトを作成した接続の言語の ID。 詳細については、「 [sys.sys言語 &#40;transact-sql&#41;](../../relational-databases/system-compatibility-views/sys-syslanguages-transact-sql.md)」を参照してください。|  
|date_format|**smallint**|キャッシュオブジェクトを作成した接続の日付形式。 詳しくは、「[SET DATEFORMAT &#40;Transact-SQL&#41;](../../t-sql/statements/set-dateformat-transact-sql.md)」をご覧ください。|  
|date_first|**tinyint**|日付の最初の値。 詳しくは、「[SET DATEFIRST &#40;Transact-SQL&#41;](../../t-sql/statements/set-datefirst-transact-sql.md)」をご覧ください。|  
|compat_level|**tinyint**|クエリプランがコンパイルされたコンテキストを持つデータベースで設定された互換性レベルを表します。 返される互換性レベルは、アドホックステートメントの現在のデータベースコンテキストの互換性レベルであり、 [QUERY_OPTIMIZER_COMPATIBILITY_LEVEL_n](../../t-sql/queries/hints-transact-sql-query.md)クエリヒントの影響を受けません。 ストアドプロシージャまたは関数に含まれているステートメントでは、ストアドプロシージャまたは関数が作成されたデータベースの互換性レベルに対応しています。| 
|status|**int**|キャッシュ参照キーの一部である内部ステータス ビットです。|  
|required_cursor_options|**int**|カーソルの種類など、ユーザーによって指定されたカーソルオプション。|  
|acceptable_cursor_options|**int**|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] がステートメントの実行をサポートするために暗黙的に変換できるカーソル オプションです。 たとえば、ユーザーは動的カーソルを指定することができますが、クエリオプティマイザーでは、このカーソルの種類を静的カーソルに変換することが許可されています。|  
|merge_action_type|**smallint**|MERGE ステートメントの結果として使用されるトリガー実行プランの種類です。<br /><br /> 0は、非トリガープラン、MERGE ステートメントの結果として実行されないトリガープラン、または DELETE アクションのみを指定する MERGE ステートメントの結果として実行されるトリガープランを示します。<br /><br /> 1は、MERGE ステートメントの結果として実行される INSERT トリガープランを示します。<br /><br /> 2 は、MERGE ステートメントの結果として実行される UPDATE トリガー プランを示します。<br /><br /> 3 は、対応する INSERT アクションまたは UPDATE アクションを含む MERGE ステートメントの結果として実行される DELETE トリガー プランを示します。<br /><br /> 連鎖アクションによって実行される入れ子になったトリガーの場合、この値は cascade の原因となった MERGE ステートメントのアクションです。|  
|is_replication_specific|**int**|このプランがコンパイルされたセッションは、ドキュメント化されていない接続プロパティを使用して SQL Server のインスタンスに接続されたセッションであり、サーバーがレプリケーションコンポーネントによって作成されたものとしてセッションを識別できるようにします| 
|optional_spid|**smallint**|再コンパイルの回数を減らすために、接続 session_id (spid) がキャッシュキーの一部になります。 これにより、動的にバインドされていない一時テーブルを含む、1つのセッションでのプランの再コンパイルを防止できます。|
|optional_clr_trigger_dbid|**int**|CLR DML トリガーの場合にのみ設定されます。 エンティティを含むデータベースの ID。 <BR><BR>他の種類のオブジェクトの場合、は0を返します。 | 
|optional_clr_trigger_objid|**int** |CLR DML トリガーの場合にのみ設定されます。 [Sys. オブジェクト](../../relational-databases/system-catalog-views/sys-objects-transact-sql.md)に格納されているオブジェクト ID。<BR><BR>他の種類のオブジェクトの場合、は0を返します。| 
|parent_plan_handle|**varbinary(64)**|常に NULL です。| 
|is_azure_user_plan|**tinyint** | 1 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] ユーザーによって開始されたセッションからで実行されるクエリの場合は1。 <BR><BR>0。エンドユーザーによって開始されていないセッションから実行されたクエリについては、テレメトリの収集または管理タスクの実行を目的としてクエリを発行する Azure インフラストラクチャ内から実行されているアプリケーションによって発生します。 Is_azure_user_plan = 0 のクエリで消費されるリソースに対しては課金されません。<BR><BR>**[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]** 専用.|
|inuse_exec_context|**int**|クエリプランを使用している現在実行中のバッチの数。|  
|free_exec_context|**int**|現在使用されていないクエリプランのキャッシュされた実行コンテキストの数。|  
|hits_exec_context|**int**|実行コンテキストがプランキャッシュから取得され再利用された回数。これにより、SQL ステートメントを再コンパイルするオーバーヘッドが削減されます。 この値は、これまでのすべてのバッチ実行の集計です。|  
|misses_exec_context|**int**|実行コンテキストがプランキャッシュ内に見つからなかった回数。これにより、バッチ実行用の新しい実行コンテキストが作成されます。|  
|removed_exec_context|**int**|キャッシュされたプランのメモリ不足のために削除された実行コンテキストの数。|  
|inuse_cursors|**int**|キャッシュされたプランを使用している1つ以上のカーソルを含む現在実行中のバッチの数。|  
|free_cursors|**int**|キャッシュされたプランのアイドルカーソルまたは空きカーソルの数。|  
|hits_cursors|**int**|非アクティブなカーソルがキャッシュされたプランから取得され、再利用された回数。 この値は、これまでのすべてのバッチ実行の集計です。|  
|misses_cursors|**int**|非アクティブなカーソルがキャッシュ内に見つからなかった回数。|  
|removed_cursors|**int**|キャッシュされたプランのメモリが不足しているために削除されたカーソルの数。|  
|sql_handle|**varbinary**(64)|バッチの SQL ハンドルです。|  

## <a name="permissions"></a>アクセス許可  

で [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] は、 `VIEW SERVER STATE` 権限が必要です。

Azure SQL Database Basic、S0、S1 のサービス目標、およびエラスティックプール内のデータベースについては、 [サーバー管理者](/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database) アカウントまたは [Azure Active Directory 管理者](/azure/azure-sql/database/authentication-aad-overview#administrator-structure) アカウントが必要です。 その他のすべての SQL Database サービスの目的で `VIEW DATABASE STATE` は、データベースで権限が必要になります。   

## <a name="remarks"></a>解説  
  
## <a name="set-options"></a>SET オプション  
 同じコンパイル済みプランのコピーは、 **set_options** 列の値によってのみ異なる場合があります。 これは、異なる接続が同じクエリに対して異なる SET オプションセットを使用していることを示します。 通常、異なるオプション セットを使用することは望ましくありません。異なるオプション セットを使用すると、余分なコンパイルが発生し、プランの再利用が減少して、キャッシュ内にプランの複数のコピーが存在することが原因でプラン キャッシュが増加します。  
  
### <a name="evaluating-set-options"></a>Set オプションの評価  
 **Set_options** に返された値を、プランをコンパイルしたオプションに変換するには、 **set_options** 値から値を減算します。値の最大値は、可能な限り多く、0に達するまで続きます。 減算する各値は、クエリ プランに使用されたオプションに対応しています。 たとえば、 **set_options** の値が251の場合、プランをコンパイルしたオプションは ANSI_NULL_DFLT_ON (128)、QUOTED_IDENTIFIER (64)、ANSI_NULLS (32)、ANSI_WARNINGS (16)、CONCAT_NULL_YIELDS_NULL (8)、並列プラン (2)、および ANSI_PADDING (1) になります。  
  
|オプション|値|  
|------------|-----------|  
|ANSI_PADDING|1|  
|ParallelPlan<br /><br /> プランの並列化オプションが変更されたことを示します。|2|  
|FORCEPLAN|4|  
|CONCAT_NULL_YIELDS_NULL|8|  
|ANSI_WARNINGS|16|  
|ANSI_NULLS|32|  
|QUOTED_IDENTIFIER|64|  
|ANSI_NULL_DFLT_ON|128|  
|ANSI_NULL_DFLT_OFF|256|  
|NoBrowseTable<br /><br /> プランが参照操作のを実装するために作業テーブルを使用しないことを示します。|512|  
|TriggerOneRow<br /><br /> プランに、AFTER トリガーデルタテーブルの1行の最適化が含まれていることを示します。|1024|  
|ResyncQuery<br /><br /> クエリが内部システムストアドプロシージャによって送信されたことを示します。|2048|  
|ARITH_ABORT|4096|  
|NUMERIC_ROUNDABORT|8192|  
|DATEFIRST|16384|  
|DATEFORMAT|32768|  
|LanguageID|65536|  
|基本<br /><br /> プランがコンパイルされたときに、データベースオプションのパラメーター化が FORCED に設定されたことを示します。|131072|  
|ROWCOUNT|**適用対象:** [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 宛先 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)]<br /><br /> 262144|  
  
## <a name="cursors"></a>カーソル  
 非アクティブなカーソルは、カーソルの格納に使用されたメモリをカーソルの同時ユーザーが再利用できるように、コンパイル済みプランにキャッシュされます。 たとえば、カーソルの割り当てを解除せずに、バッチでそのカーソルを宣言して使用するとします。 2 人のユーザーが同じバッチを実行している場合、アクティブなカーソルが 2 つになります。 カーソルの割り当てが解除されると (場合によっては、別のバッチに存在する可能性があります)、カーソルの格納に使用されるメモリはキャッシュされ、解放されません。 この非アクティブなカーソルの一覧は、コンパイル済みのプランに保持されます。 次にユーザーがバッチを実行するときに、キャッシュされたカーソルのメモリが再利用され、アクティブなカーソルとして適切に初期化されます。  
  
### <a name="evaluating-cursor-options"></a>カーソルオプションの評価  
 **Acceptable_cursor_options** **required_cursor_options** に返された値を、プランをコンパイルしたオプションに変換するには、列の値から、可能な最大値から順に、0に達するまで値を減算します。 減算する各値は、クエリ プランに使用されたカーソル オプションに対応しています。  
  
|オプション|値|  
|------------|-----------|  
|なし|0|  
|INSENSITIVE|1|  
|SCROLL|2|  
|READ ONLY|4|  
|FOR UPDATE|8|  
|LOCAL|16|  
|GLOBAL|32|  
|FORWARD_ONLY|64|  
|KEYSET|128|  
|DYNAMIC|256|  
|SCROLL_LOCKS|512|  
|OPTIMISTIC|1024|  
|STATIC|2048|  
|FAST_FORWARD|4096|  
|IN PLACE|8192|  
|FOR *select_statement*|16384|  
  
## <a name="examples"></a>例  
  
### <a name="a-returning-the-attributes-for-a-specific-plan"></a>A. 特定のプランの属性を返す  
 次の例では、指定されたプランのすべてのプラン属性を返します。 `sys.dm_exec_cached_plans`指定されたプランのプランハンドルを取得するために、最初に動的管理ビューが照会されます。 2番目のクエリでは、を `<plan_handle>` 最初のクエリのプランハンドル値に置き換えます。  
  
```sql  
SELECT plan_handle, refcounts, usecounts, size_in_bytes, cacheobjtype, objtype   
FROM sys.dm_exec_cached_plans;  
GO  
SELECT attribute, [value], is_cache_key  
FROM sys.dm_exec_plan_attributes(<plan_handle>);  
GO  
```  
  
### <a name="b-returning-the-set-options-for-compiled-plans-and-the-sql-handle-for-cached-plans"></a>B. コンパイル済みプランの SET オプションとキャッシュされたプランの SQL ハンドルを返す  
 次の例では、各プランがコンパイルされたオプションを表す値を返します。 さらに、キャッシュされたすべてのプランの SQL ハンドルが返されます。  
  
```sql  
SELECT plan_handle, pvt.set_options, pvt.sql_handle  
FROM (  
    SELECT plan_handle, epa.attribute, epa.value   
    FROM sys.dm_exec_cached_plans   
        OUTER APPLY sys.dm_exec_plan_attributes(plan_handle) AS epa  
    WHERE cacheobjtype = 'Compiled Plan') AS ecpa   
PIVOT (MAX(ecpa.value) FOR ecpa.attribute IN ("set_options", "sql_handle")) AS pvt;  
GO  
```  
  
## <a name="see-also"></a>参照  
 [動的管理ビューと動的管理関数 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)   
 [実行関連の動的管理ビューおよび関数 &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/execution-related-dynamic-management-views-and-functions-transact-sql.md)   
 [sys.dm_exec_cached_plans &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-cached-plans-transact-sql.md)   
 [sys.databases &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md)   
 [sys.objects &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-objects-transact-sql.md)  
  
