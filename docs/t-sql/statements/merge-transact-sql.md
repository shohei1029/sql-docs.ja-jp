---
description: MERGE (Transact-SQL)
title: MERGE (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 02/27/2021
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- MERGE
- MERGE_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- updating data [SQL Server]
- modifying data [SQL Server], MERGE statement
- MERGE statement [SQL Server]
- adding data
- DML [SQL Server], MERGE statement
- table modifications [SQL Server], MERGE statement
- data manipulation language [SQL Server], MERGE statement
- inserting data
ms.assetid: c17996d6-56a6-482f-80d8-086a3423eecc
author: XiaoyuMSFT
ms.author: XiaoyuL
monikerRange: = azuresqldb-current || = azuresqldb-mi-current || >= sql-server-2016 || >= sql-server-linux-2017 ||  azure-sqldw-latest
ms.openlocfilehash: 96461f56445562b444ca775b24320d3c34661687
ms.sourcegitcommit: e2d25f265556af92afcc0acde662929e654bf841
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103489765"
---
# <a name="merge-transact-sql"></a>MERGE (Transact-SQL)

[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb-asa.md)]

ソース テーブルとの結合結果から、挿入、更新、または削除操作を対象テーブルに対して実行します。 たとえば、他のテーブルとの違いに基づいて、あるテーブル内の行を挿入、更新、または削除することにより、2 つのテーブルを同期します。 

> [!NOTE]
> MERGE は現在、Azure Synapse Analytics を対象としたプレビューの段階です。
  
**パフォーマンスのヒント:** 説明した MERGE ステートメントの条件付きの動作は、一致する特性が 2 つのテーブルで複雑に組み合わされている場合に最適です。 たとえば、存在しない場合は行を挿入し、一致しない場合は行を更新します。 別のテーブルの行に基づいて 1 つのテーブルを更新するだけで、基本的な INSERT、UPDATE、および DELETE ステートメントのパフォーマンスとスケーラビリティが向上します。 次に例を示します。  
  
```sql
INSERT tbl_A (col, col2)  
SELECT col, col2
FROM tbl_B
WHERE NOT EXISTS (SELECT col FROM tbl_A A2 WHERE A2.col = tbl_B.col);  
```  
  
![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql

-- SQL Server and Azure SQL Database
[ WITH <common_table_expression> [,...n] ]  
MERGE
    [ TOP ( expression ) [ PERCENT ] ]
    [ INTO ] <target_table> [ WITH ( <merge_hint> ) ] [ [ AS ] table_alias ]  
    USING <table_source> [ [ AS ] table_alias ]
    ON <merge_search_condition>  
    [ WHEN MATCHED [ AND <clause_search_condition> ]  
        THEN <merge_matched> ] [ ...n ]  
    [ WHEN NOT MATCHED [ BY TARGET ] [ AND <clause_search_condition> ]  
        THEN <merge_not_matched> ]  
    [ WHEN NOT MATCHED BY SOURCE [ AND <clause_search_condition> ]  
        THEN <merge_matched> ] [ ...n ]  
    [ <output_clause> ]  
    [ OPTION ( <query_hint> [ ,...n ] ) ]
;  
  
<target_table> ::=  
{
    [ database_name . schema_name . | schema_name . ]  
  target_table  
}  
  
<merge_hint>::=  
{  
    { [ <table_hint_limited> [ ,...n ] ]  
    [ [ , ] INDEX ( index_val [ ,...n ] ) ] }  
}  

<merge_search_condition> ::=  
    <search_condition>  
  
<merge_matched>::=  
    { UPDATE SET <set_clause> | DELETE }  
  
<merge_not_matched>::=  
{  
    INSERT [ ( column_list ) ]
        { VALUES ( values_list )  
        | DEFAULT VALUES }  
}  
  
<clause_search_condition> ::=  
    <search_condition> 
```

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

```syntaxsql
-- MERGE (Preview) for Azure Synapse Analytics 
[ WITH <common_table_expression> [,...n] ]  
MERGE
    [ INTO ] <target_table> [ [ AS ] table_alias ]  
    USING <table_source> [ [ AS ] table_alias ]
    ON <merge_search_condition>  
    [ WHEN MATCHED [ AND <clause_search_condition> ]  
        THEN <merge_matched> ] [ ...n ]  
    [ WHEN NOT MATCHED [ BY TARGET ] [ AND <clause_search_condition> ]  
        THEN <merge_not_matched> ]  
    [ WHEN NOT MATCHED BY SOURCE [ AND <clause_search_condition> ]  
        THEN <merge_matched> ] [ ...n ]
    [ OPTION ( <query_hint> [ ,...n ] ) ]
;  -- The semi-colon is required, or the query will return a syntax error. 
```

## <a name="arguments"></a>引数

WITH \<common_table_expression>  
MERGE ステートメントのスコープ内で定義された、一時的な名前付き結果セットまたはビュー (共通テーブル式とも呼ばれます) を指定します。 結果セットは単純なクエリから派生し、MERGE ステートメントで参照されます。 詳細については、「[WITH common_table_expression &#40;Transact-SQL&#41;](../../t-sql/queries/with-common-table-expression-transact-sql.md)」を参照してください。  
  
TOP ( *expression* ) [ PERCENT ]  
影響を受ける行の数またはパーセンテージを指定します。 *expression* には、行数または行のパーセンテージを指定できます。 TOP 式で参照される行は順序付けされません。 詳細については、「[TOP &#40;Transact-SQL&#41;](../../t-sql/queries/top-transact-sql.md)」を参照してください。  
  
TOP 句は、ソース テーブル全体と対象テーブル全体が結合され、挿入、更新、または削除操作の対象とならない結合された行が削除された後に適用されます。 さらに、TOP 句は、結合された行の数を、指定した値に減らします。 挿入、更新、または削除操作は、残りの結合された行に対して、順不同の方法で適用されます。 つまり、WHEN 句に定義された操作に行が割り当てられる順序は決まっていません。 たとえば、TOP (10) を指定すると、10 行が影響を受けます。 これらの行では、7 行が更新されて 3 行が挿入される場合も、1 行が削除され、5 行が更新され、4 行が挿入される場合もあります。  
  
MERGE ステートメントはソース テーブルと対象テーブルの両方のフル テーブル スキャンを実行するので、TOP 句を使用し、複数のバッチを作成して大きなテーブルを変更すると、I/O パフォーマンスが影響を受ける場合があります。 このシナリオでは、連続するすべてのバッチで確実に新しい行が対象になっていることが重要です。  
  
*database_name*  
*target_table* があるデータベースの名前です。  
  
*schema_name*  
*target_table* が属しているスキーマの名前です。  
  
*target_table*  
\<clause_search_condition> に基づいて \<table_source> のデータ行が照合されるテーブルまたはビューです。 *target_table* は、MERGE ステートメントの WHEN 句で指定された挿入、更新、削除のいずれかの操作の対象です。  
  
*target_table* がビューの場合、これに対するアクションはビューの更新条件を満たす必要があります。 詳細については、「[ビューを使用したデータ変更](../../relational-databases/views/modify-data-through-a-view.md)」を参照してください。  
  
*target_table* にリモート テーブルを指定することはできません。 *target_table* に対してルールを定義することはできません。  
  
[ AS ] *table_alias*  
*target_table* のテーブルを参照するための代替名です。  
  
USING \<table_source>  
\<merge_search condition> に基づいて *target_table* 内のデータ行と照合するデータ ソースを指定します。 この照合結果によって、MERGE ステートメントの WHEN 句で実行される操作が決まります。 \<table_source> には、リモート テーブルを指定することも、リモート テーブルにアクセスする派生テーブルを指定することもできます。
  
\<table_source> には派生テーブルを指定できます。このテーブルでは、複数の行を指定してテーブルを作成する [!INCLUDE[tsql](../../includes/tsql-md.md)] [テーブル値コンストラクター](../../t-sql/queries/table-value-constructor-transact-sql.md)を使用します。  
  
 [ AS ] *table_alias*  
table_source のテーブルを参照するための代替名です。   
  
この句の構文および引数の詳細については、「[FROM &#40;Transact-SQL&#41;](../../t-sql/queries/from-transact-sql.md)」を参照してください。  
  
ON \<merge_search_condition>  
\<table_source> と *target_table* を結合するための一致箇所を特定する条件を指定します。
  
> [!CAUTION]  
> 照合目的で使用する対象テーブルの列だけを指定することが重要です。 つまり、対象テーブルのうち、ソース テーブルの対応する列と比較する列を指定します。 `AND NOT target_table.column_x = value` と指定するなど、ON 句で対象テーブル内の行にフィルターを適用することによって、クエリ パフォーマンスを向上させようとしないでください。 この場合、予期しない無効な結果が返されることがあります。  
  
WHEN MATCHED THEN \<merge_matched>  
\<table_source> ON \<merge_search_condition> で返される行に一致し、追加の検索条件を満たす *target_table のすべての行を、\<merge_matched> 句に従って更新または削除するように指定します。  
  
MERGE ステートメントには、最大 2 つの WHEN MATCHED 句を指定できます。 句を 2 つ指定する場合、最初の句は AND \<search_condition> 句と共に使用する必要があります。 任意の行に対し、最初の WHEN MATCHED 句が適用されなかった場合にのみ、2 番目の WHEN MATCHED 句が適用されます。 WHEN MATCHED 句が 2 つある場合は、一方で UPDATE 操作を、もう一方で DELETE 操作を指定する必要があります。 \<merge_matched> 句で UPDATE が指定されており、\<merge_search_condition> に基づいて \<table_source> の複数の行が *target_table* の 1 つの行に一致する場合、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] からエラーが返されます。 MERGE ステートメントで、同じ行を複数回更新することや、同じ行の更新と削除を行うことはできません。  
  
WHEN NOT MATCHED [ BY TARGET ] THEN \<merge_not_matched>  
\<table_source> ON \<merge_search_condition> で返される行のうち、*target_table* 内の行とは一致しないが、追加の検索条件 (存在する場合) は満たす行ごとに、*target_table* に 1 行を挿入するように指定します。 挿入する値は、\<merge_not_matched> 句で指定します。 MERGE ステートメントに指定できる WHEN NOT MATCHED [ BY TARGET ] 句は 1 つだけです。

WHEN NOT MATCHED BY SOURCE THEN \<merge_matched>  
\<table_source> ON \<merge_search_condition> で返される行に一致せず、追加の検索条件を満たす *target_table のすべての行を、\<merge_matched> 句に従って更新または削除するように指定します。  
  
MERGE ステートメントには、最大 2 つの WHEN NOT MATCHED BY SOURCE 句を指定できます。 句を 2 つ指定する場合、最初の句は AND \<clause_search_condition> 句と共に使用する必要があります。 任意の行に対し、最初の WHEN NOT MATCHED BY SOURCE 句が適用されなかった場合にのみ、2 番目の WHEN NOT MATCHED BY SOURCE 句が適用されます。 WHEN NOT MATCHED BY SOURCE 句が 2 つある場合は、一方で UPDATE 操作を、もう一方で DELETE 操作を指定する必要があります。 \<clause_search_condition> では対象テーブルの列のみを参照できます。  
  
\<table_source> から返される行がない場合、ソース テーブルの列にアクセスすることはできません。 \<merge_matched> 句に指定した更新操作または削除操作でソース テーブルの列を参照していると、エラー 207 (無効な列名) が返されます。 たとえば、ソース テーブルの `WHEN NOT MATCHED BY SOURCE THEN UPDATE SET TargetTable.Col1 = SourceTable.Col1` にアクセスできないために、`Col1` 句によってステートメントが失敗することがあります。  
  
AND \<clause_search_condition>  
任意の有効な検索条件を指定します。 詳しくは、「[検索条件 &#40;Transact-SQL&#41;](../../t-sql/queries/search-condition-transact-sql.md)」をご覧ください。  
  
\<table_hint_limited>  
MERGE ステートメントによって実行される挿入、更新、削除の各操作に対し、対象テーブルに適用される 1 つ以上のテーブル ヒントを指定します。 キーワード WITH とかっこが必要です。  
  
NOLOCK および READUNCOMMITTED は指定できません。 テーブル ヒントの詳細については、「[テーブル ヒント &#40;Transact-SQL&#41;](../../t-sql/queries/hints-transact-sql-table.md)」を参照してください。  
  
INSERT ステートメントの対象であるテーブルに対して TABLOCK ヒントを指定すると、TABLOCKX ヒントを指定した場合と同じ効果を得られます。 テーブルに対して、排他ロックが取得されます。 FORCESEEK を指定すると、ソース テーブルに結合された対象テーブルの暗黙のインスタンスに対して適用されます。  
  
> [!CAUTION]  
> WHEN NOT MATCHED [ BY TARGET ] THEN INSERT に READPAST を指定すると、UNIQUE 制約に違反する INSERT 操作が発生する場合があります。  
  
INDEX ( index_val [ ,...n ] )  
ソース テーブルとの暗黙の結合を実行するための、対象テーブルの 1 つ以上のインデックスの名前または ID を指定します。 詳細については、「[テーブル ヒント &#40;Transact-SQL&#41;](../../t-sql/queries/hints-transact-sql-table.md)」を参照してください。  
  
\<output_clause>  
*target_table* 内の更新、挿入、または削除される行ごとに 1 行を返します。この場合、特定の順序はありません。 **$action** は、OUTPUT 句に指定することができます。 **$action** は、行に対して実行されたアクションに従って次のいずれかの値をそれぞれの行について返す、**nvarchar(10)** 型の列です:"INSERT"、"UPDATE"、または "DELETE"。その行で実行されるアクションに従います。 この句の引数と動作について詳しくは、「[OUTPUT 句 &#40;Transact-SQL&#41;](../../t-sql/queries/output-clause-transact-sql.md)」をご覧ください。  
  
OPTION ( \<query_hint> [ ,...n ] )  
オプティマイザー ヒントを使用して、データベース エンジンがステートメントを処理する方法をカスタマイズすることを指定します。 詳細については、「[クエリ ヒント &#40;Transact-SQL&#41;](../../t-sql/queries/hints-transact-sql-query.md)」を参照してください。  
  
\<merge_matched>  
\<table_source> ON \<merge_search_condition> で返される行には一致しないが、追加の検索条件は満たす *target_table* のすべての行に対して適用する更新操作または削除操作を指定します。  
  
UPDATE SET \<set_clause>  
対象テーブル内で更新する列名または変数名の一覧と、それらの更新に使用する値を指定します。  
  
この句の引数について詳しくは、「[UPDATE &#40;Transact-SQL&#41;](../../t-sql/queries/update-transact-sql.md)」をご覧ください。 列と同じ値を変数に設定することはできません。  
  
DELETE  
*target_table* 内の行に一致する行を削除するように指定します。  
  
\<merge_not_matched>  
対象テーブルに挿入する値を指定します。  
  
(*column_list*)  
対象テーブルのデータを挿入する 1 つ以上の列で構成されるリストを指定します。 列は 1 部構成の名前で指定する必要があります。そうしないと MERGE ステートメントが失敗します。 *column_list* はかっこで囲み、コンマで区切る必要があります。  
  
VALUES ( *values_list*)  
対象テーブルに挿入する値を返す定数、変数、または式を、コンマ区切りのリストで指定します。 式に EXECUTE ステートメントを含めることはできません。  
  
DEFAULT VALUES  
挿入される行が、各列に対して定義されている既定値で構成されることを指定します。  
  
この句について詳しくは、「[INSERT &#40;Transact-SQL&#41;](../../t-sql/statements/insert-transact-sql.md)」をご覧ください。  
  
\<search_condition>  
\<merge_search_condition> または \<clause_search_condition> を指定する検索条件を指定します。 この句の引数について詳しくは、「[検索条件 &#40;Transact-SQL&#41;](../../t-sql/queries/search-condition-transact-sql.md)」をご覧ください。  

\<graph search pattern>  
グラフの一致パターンを指定します。 この句の引数の詳細については、「[MATCH &#40;Transact-SQL&#41;](../../t-sql/queries/match-sql-graph.md)」を参照してください
   
## <a name="remarks"></a>解説
>[!NOTE]
> Azure Synapse Analytics での MERGE コマンド (プレビュー) は、SQL サーバーや Azure SQL データベースと比べて次のような違いがあります。  
> - MERGE 更新は、削除と挿入のペアとして実装されます。 MERGE 更新の影響を受ける行の数には、削除される行と挿入される行が含まれます。 
> - プレビュー期間中は、IDENTITY 列を含むテーブルに対する MERGE…WHEN NOT MATCHED INSERT はサポートされていません。  
> - 各種の分散タイプでのテーブルのサポートについては、次の表で説明しています。
>
>|Azure Synapse Analytics での MERGE CLAUSE|サポートされる TARGE 分散テーブル| サポートされる SOURCE 分散テーブル|コメント|  
>|-----------------|---------------|-----------------|-----------|  
>|**WHEN MATCHED**| すべての分散タイプ |すべての分散タイプ||  
>|**NOT MATCHED BY TARGET**|HASH |すべての分散タイプ|UPDATE/DELETE FROM…JOIN を使用して、2 つのテーブルを同期します。 |
>|**NOT MATCHED BY SOURCE**|すべての分散タイプ|すべての分散タイプ|||  

>[!IMPORTANT]
> プレビュー機能はテストのみを目的としているため、運用インスタンスや運用データでは使用しないでください。 また、データが重要な場合は、テスト データのコピーも保持してください。
>
> Azure Synapse Analytics では、MERGE コマンド (現在プレビュー段階) を使用すると、特定の条件下でターゲット テーブルが不整合な状態のままになり、行が間違った分散に配置され、後のクエリで誤った結果が返される可能性があります。 この問題は、次の 2 つの条件が満たされた場合に発生する可能性があります。
>
> - MERGE T-SQL ステートメントが、Azure Synapse SQL データベースのハッシュ分散ターゲット テーブルで実行された。および
> - マージの TARGET テーブルに、セカンダリ インデックスまたは UNIQUE 制約がある。
>
> この問題は、Synapse SQL バージョン ***10.0.15563.0*** 以降で修正されています。    
> - 確認するには、SQL Server Management Studio (SSMS) を介して Synapse SQL データベースに接続し、```SELECT @@VERSION``` を実行します。  修正プログラムが適用されていない場合は、Synapse SQL プールを手動で一時停止して再開し、修正プログラムを取得します。 
> - Synapse SQL プールに修正プログラムが適用されていることを確認するまでは、セカンダリ インデックスまたは UNIQUE 制約があるハッシュ分散ターゲット テーブルで MERGE コマンドを使用しないでください。
> - この修正プログラムによって、MERGE に関する問題の影響を既に受けているテーブルが修復されることはありません。  以下のスクリプトを使用して、影響を受けたテーブルを手動で特定して修復します。

> この問題が原因で MERGE 操作を使用できない、データベース内のハッシュ分散テーブルを確認するには、次のステートメントを実行します。
>```sql
> select a.name, c.distribution_policy_desc, b.type from sys.tables a join sys.indexes b
> on a.object_id = b.object_id
> join
> sys.pdw_table_distribution_properties c
> on a.object_id = c.object_id
> where b.type = 2 and c.distribution_policy_desc = 'HASH'
> ```
> 
> MERGE のハッシュ分散ターゲット テーブルがこの問題の影響を受けるかどうかを確認するには、次の手順のようにして、正しくない分散で配置された行がテーブルに含まれるかどうかを調べます。  "no need for repair" (修復の必要なし) が返された場合、このテーブルは影響を受けません。  
>
>```sql
> if object_id('[check_table_1]', 'U') is not null
> drop table [check_table_1]
> go
> if object_id('[check_table_2]', 'U') is not null
> drop table [check_table_2]
> go
>
> create table [check_table_1] with(distribution = round_robin) as
> select <DISTRIBUTION_COLUMN> as x from <MERGE_TARGET_TABLE> group by <DISTRIBUTION_COLUMN>;
> go
>
> create table [check_table_2] with(distribution = hash(x)) as
> select x from [check_table_1];
>go
>
> if not exists(select top 1 * from (select <DISTRIBUTION_COLUMN> as x from <MERGE_TARGET_TABLE> except select x from 
> [check_table_2]) as tmp)
> select 'no need for repair' as result
> else select 'needs repair' as result
> go
>
> if object_id('[check_table_1]', 'U') is not null
> drop table [check_table_1]
> go
> if object_id('[check_table_2]', 'U') is not null
> drop table [check_table_2]
> go
>```
>影響を受けるテーブルを修復するには、次のステートメントを実行して、古いテーブルのすべての行を新しいテーブルにコピーします。
>```sql
> if object_id('[repair_table_temp]', 'U') is not null
> drop table [repair_table_temp];
> go
> if object_id('[repair_table]', 'U') is not null
> drop table [repair_table];
> go
> create table [repair_table_temp] with(distribution = round_robin) as select * from <MERGE_TARGET_TABLE>;
> go
>
> -- [repair_table] will hold the repaired table generated from <MERGE_TARGET_TABLE>
> create table [repair_table] with(distribution = hash(<DISTRIBUTION_COLUMN>)) as select * from [repair_table_temp];
> go
>if object_id('[repair_table_temp]', 'U') is not null
> drop table [repair_table_temp];
> go
> ```   

3 つの MATCHED 句のうち、少なくとも 1 つは指定する必要があります。これらの句は、任意の順序で指定できます。 1 つの MATCHED 句で 1 つの変数を複数回更新することはできません。  
  
MERGE ステートメントによって対象テーブルに指定された挿入、更新、削除の各操作は、連鎖参照整合性制約など、定義されている制約の制限を受けます。 対象テーブルの一意インデックスで IGNORE_DUP_KEY が ON に設定されていると、MERGE ではこの設定が無視されます。  
  
MERGE ステートメントでは、ステートメントのターミネータとしてセミコロン (;) が必要です。 MERGE ステートメントにターミネータを付けずに実行すると、エラー 10713 が生成されます。  
  
MERGE の後に [@@ROWCOUNT &#40;Transact-SQL&#41;](../../t-sql/functions/rowcount-transact-sql.md) を使用すると、クライアントに対して挿入、更新、および削除された行の合計数が返されます。  
  
データベースの互換性レベルが 100 以上に設定されている場合、MERGE は完全に予約されたキーワードです。 MERGE ステートメントはデータベースの互換性レベルが 90 と 100 の両方で使用できますが、データベースの互換性レベルが 90 に設定されている場合、MERGE キーワードは完全には予約されません。  
  
**MERGE** ステートメントは、キュー更新レプリケーションの使用時には使用しないでください。 **MERGE** とキュー更新トリガーには互換性がありません。 **MERGE** ステートメントは、挿入ステートメントまたは更新ステートメントと置き換えてください。  

## <a name="trigger-implementation"></a>トリガーの実装

MERGE ステートメントに指定された挿入、更新、削除の各操作に対し、対象テーブルで定義された対応する AFTER トリガーが [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] によって起動されますが、どの操作に対するトリガーからどのような順序で起動されるかは決まっていません。 同じ操作に対して定義された複数のトリガーは、指定した順序に従います。 トリガー起動順序の設定の詳細については、「[最初と最後のトリガーの指定](../../relational-databases/triggers/specify-first-and-last-triggers.md)」を参照してください。  
  
MERGE ステートメントによって実行される挿入、更新、削除のいずれかの操作に対して、有効な INSTEAD OF トリガーが対象テーブルに定義されている場合、MERGE ステートメントに指定されたすべての操作に有効な INSTEAD OF トリガーを定義する必要があります。  
  
INSTEAD OF UPDATE トリガーや INSTEAD OF DELETE トリガーが *target_table* に定義されている場合、更新操作や削除操作は実行されません。 代わりにトリガーが起動され、**inserted** テーブルと **deleted** テーブルに適切なデータが設定されます。  
  
INSTEAD OF INSERT トリガーが *target_table* に定義されている場合、挿入操作は実行されません。 代わりに、テーブルに適切なデータが設定されます。  
  
## <a name="permissions"></a>アクセス許可

ソース テーブルに対する SELECT 権限と、対象テーブルに対する INSERT、UPDATE、または DELETE 権限が必要です。 詳しくは、「[SELECT](../../t-sql/queries/select-transact-sql.md)」、「[INSERT](../../t-sql/statements/insert-transact-sql.md)」、「[UPDATE](../../t-sql/queries/update-transact-sql.md)」、「[DELETE](../../t-sql/statements/delete-transact-sql.md)」の各記事の「権限」をご覧ください。  
  
## <a name="optimizing-merge-statement-performance"></a>MERGE ステートメントのパフォーマンスの最適化

MERGE ステートメントを使用すると、個々の DML ステートメントを単一のステートメントに置き換えることができます。 これにより、操作が単一のステートメント内で実行されてソース テーブルと対象テーブルのデータの処理回数が最小限に抑えられるので、クエリのパフォーマンスが向上します。 ただし、パフォーマンスが向上するかどうかは、インデックスが正しいか、結合が存在するかなど、いくつかの考慮事項によって決まります。

### <a name="index-best-practices"></a>インデックスに関するベスト プラクティス

MERGE ステートメントのパフォーマンスを向上させるには、次のインデックスのガイドラインに従うことをお勧めします。

- ソース テーブルの結合列に一意なカバリング インデックスを作成します。
- 対象テーブルの結合列に一意なクラスター化インデックスを作成します。

これらのインデックスによって結合キーが一意になり、テーブルのデータが並べ替えられるようになります。 クエリ オプティマイザーが重複行を見つけて更新するために追加の検証処理を実行する必要がなく、追加の並べ替え操作が不要になるので、クエリのパフォーマンスが向上します。

### <a name="join-best-practices"></a>JOIN に関するベスト プラクティス

MERGE ステートメントのパフォーマンスを向上させて正しい結果が得られるようにするには、次の結合のガイドラインに従うことをお勧めします。

- <merge_search_condition> 句で、ソース テーブルと対象テーブルのデータを照合する基準を判断する検索条件のみを指定します。 つまり、対象テーブルのうち、ソース テーブル内の対応する列と比較する列のみを指定します。 
- 定数などのその他の値との比較は指定しないでください。

ソース テーブルまたは対象テーブルから行を除外するには、次のいずれかの方法を使用します。

- 適切な WHEN 句で、行をフィルター選択するための検索条件を指定します。 たとえば、WHEN NOT MATCHED AND S.EmployeeName LIKE 'S%' THEN INSERT... のようにします。
- フィルター選択された行を返すソースまたは対象のビューを定義して、そのビューをソース テーブルまたは対象テーブルとして参照します。 ビューが対象テーブルに対して定義されている場合、これに対するアクションはビューの更新条件を満たす必要があります。 ビューを使用してデータを更新する方法の詳細については、「ビューを使用したデータ変更」を参照してください。
- `WITH <common table expression>` 句を使用して、ソース テーブルまたは対象テーブルから行を除外します。 この方法は、ON 句で追加の検索条件を指定する方法に似ており、不適切な結果が返される可能性があります。 この方法を使用しないようにするか、十分にテストしてから実装することをお勧めします。

MERGE ステートメントでの結合操作は、SELECT ステートメントでの結合と同じ方法で最適化されます。 つまり、SQL Server で結合を処理する場合、クエリ オプティマイザーは、複数の候補の中から最も効率的な結合の処理方法を選択します。 ソースと対象が同じようなサイズで、前に説明したインデックスのガイドラインがソース テーブルと対象テーブルに適用されている場合は、Merge Join 操作が最も効率的なクエリ プランになります。 これは、両方のテーブルが 1 回だけスキャンされ、データを並べ替える必要がないためです。 ソース テーブルが対象テーブルよりも小さい場合は、Nested Loops 操作をお勧めします。

MERGE ステートメントで `OPTION (<query_hint>)` 句を指定することで、特定の結合を強制的に使用することができます。 MERGE ステートメントのクエリ ヒントとしてハッシュ結合を使用しないことをお勧めします。この種類の結合ではインデックスが使用されないためです。

### <a name="parameterization-best-practices"></a>パラメーター化に関するベスト プラクティス

パラメーターを指定せずに SELECT、INSERT、UPDATE、または DELETE ステートメントを実行した場合、SQL Server のクエリ オプティマイザーの内部でステートメントがパラメーター化される場合があります。 これは、クエリに含まれるリテラル値がすべてパラメーターに置き換えられることを意味します。 たとえば、ステートメント INSERT dbo.MyTable (Col1, Col2) VALUES (1, 10) は、内部で INSERT dbo.MyTable (Col1, Col2) VALUES (@p1, @p2) と実装される場合があります。 簡易パラメーター化と呼ばれるこの処理によって、新しい SQL ステートメントと既存のコンパイル済みの実行プランとを照合するリレーショナル エンジンの機能が向上します。 クエリをコンパイルおよび再コンパイルする頻度が下がるので、クエリのパフォーマンスが向上する場合があります。 クエリ オプティマイザーでは、簡易パラメーター化処理は MERGE ステートメントには適用されません。 したがって、MERGE ステートメントが実行されるたびに新しいプランがコンパイルされるので、リテラル値を含む MERGE ステートメントは、個々の INSERT、UPDATE、または DELETE ステートメントよりもパフォーマンスが低くなる可能性があります。

クエリのパフォーマンスを向上させるには、次のパラメーター化のガイドラインに従うことをお勧めします。

- MERGE ステートメントの `ON <merge_search_condition>` 句と `WHEN` 句ですべてのリテラル値をパラメーター化します。 たとえば、リテラル値を適切な入力パラメーターに置き換えるストアド プロシージャに MERGE ステートメントを組み込むことができます。
- ステートメントをパラメーター化できない場合は、`TEMPLATE` 型のプラン ガイドを作成し、そのプラン ガイドで `PARAMETERIZATION FORCED` クエリ ヒントを指定します。
- MERGE ステートメントがデータベースで頻繁に実行される場合は、データベースの PARAMETERIZATION オプションを FORCED に設定することを検討します。 このオプションを設定する場合は注意が必要です。 `PARAMETERIZATION` オプションはデータベース レベルの設定で、データベースに対するすべてのクエリの処理方法に影響します。

### <a name="top-clause-best-practices"></a>TOP 句に関するベスト プラクティス

MERGE ステートメントの TOP 句では、ソース テーブルと対象テーブルが結合され、挿入、更新、または削除操作の対象とならない行が削除された後に影響を受ける、行の数または割合を指定します。 TOP 句を使用すると、結合された行の数が指定の値まで減少し、挿入、更新、または削除操作が残りの結合された行に順序付けなしで適用されます。 つまり、WHEN 句で定義された各操作に行を割り当てる順序に決まりはありません。 たとえば、TOP (10) と指定すると 10 行に影響しますが、そのうち 7 行が更新されて 3 行が挿入される場合も、1 行が削除、5 行が更新され、4 行が挿入される場合もあります。

一般に、TOP 句は、データ操作言語 (DML) 操作を大きなテーブルに対してバッチで実行するために使用します。 このために MERGE ステートメントで TOP 句を使用する場合は、次の影響について理解しておくことが重要です。

- I/O のパフォーマンスに影響する場合があります。

  MERGE ステートメントでは、ソース テーブルと対象テーブルの両方のフル テーブル スキャンが実行されます。 操作をバッチに分割すると、バッチごとに実行される書き込み操作の数は減少しますが、各バッチでソース テーブルと対象テーブルのフル テーブル スキャンが実行されます。 結果として行われる読み取り操作が、クエリのパフォーマンスに影響する場合があります。

- 不適切な結果になる可能性があります。

  一連のすべてのバッチが新しい行を対象としていることを確認してください。新しい行を対象としていない場合は、対象テーブルに重複行が誤って挿入されるなどの不適切な動作が発生する可能性があります。 このような動作は、対象バッチには存在しないが対象テーブル全体には存在する行がソース テーブルに含まれている場合に発生する可能性があります。

- 正しい結果を得るには、次の操作を実行します。

  - ON 句を使用して、既存の対象行に影響するソース行と本当に新しい行を特定します。
  - WHEN MATCHED 句で追加条件を使用して、対象行が以前のバッチで既に更新されているかどうかを調べます。

TOP 句はこれらの句が適用された後にのみ適用されるので、実行ごとに本当に一致しない 1 行が挿入されるか、既存の 1 行が更新されます。

### <a name="bulk-load-best-practices"></a>一括読み込みに関するベスト プラクティス

MERGE ステートメントを使用すると、`OPENROWSET(BULK…)` 句をテーブル ソースとして指定することによって、ソース データ ファイルから対象テーブルにデータを効率よく一括読み込みできます。 これにより、ファイル全体が単一のバッチで処理されます。

一括マージ処理のパフォーマンスを向上させるには、次のガイドラインに従うことをお勧めします。

- 対象テーブルの結合列にクラスター化インデックスを作成します。
- `OPENROWSET(BULK…)` 句で ORDER ヒントと UNIQUE ヒントを使用して、ソース データ ファイルの並べ替え方法を指定します。

  既定では、一括操作はデータ ファイルが並べ替えられていないことを前提に実行されます。 そのため、ソース データが対象テーブルのクラスター化インデックスに従って並べ替えられることと、クエリ オプティマイザーでより効率的なクエリ プランを生成できるように ORDER ヒントを使用して順序を指定することが重要です。 ヒントは実行時に検証されます。データ ストリームが指定されたヒントに適合していない場合は、エラーが発生します。

これらのガイドラインによって結合キーが一意になり、ソース ファイルのデータの並べ替え順が対象テーブルと一致するようになります。 追加の並べ替え操作が不要になり、不要なデータのコピーが必要なくなるので、クエリのパフォーマンスが向上します。

### <a name="measuring-and-diagnosing-merge-performance"></a>MERGE のパフォーマンスの測定と診断

次の機能を使用すると、MERGE ステートメントのパフォーマンスの測定と診断に役立ちます。

- [sys.dm_exec_query_optimizer_info](../../relational-databases/system-dynamic-management-views/sys-dm-exec-query-optimizer-info-transact-sql.md) 動的管理ビューの merge stmt カウンターを使用すると、MERGE ステートメントに対するクエリの最適化の数が返されます。
- [sys.dm_exec_plan_attributes](../../relational-databases/system-dynamic-management-views/sys-dm-exec-plan-attributes-transact-sql.md) 動的管理ビューの merge_action_type 属性を使用すると、MERGE ステートメントの結果として使用するトリガーの実行プランの種類が返されます。
- SQL トレースを使用すると、MERGE ステートメントのトラブルシューティング データが、その他のデータ操作言語 (DML) ステートメントの場合と同じ方法で収集されます。 詳細については、「 [SQL Trace](../../relational-databases/sql-trace/sql-trace.md)」を参照してください。

## <a name="examples"></a>例  

### <a name="a-using-merge-to-do-insert-and-update-operations-on-a-table-in-a-single-statement"></a>A. MERGE を使用して、単一のステートメントでテーブルに INSERT 操作と UPDATE 操作を実行する

一般的なシナリオは、一致する行が存在する場合、テーブル内の 1 つまたは複数の列を更新することです。 または、一致する行が存在しない場合は、データを新しい行として挿入します。 通常は、適切な UPDATE ステートメントと INSERT ステートメントを含むストアド プロシージャにパラメーターを渡すことで、どちらかを実行します。 MERGE ステートメントを使用すると、単一のステートメントで両方のタスクを実行できます。 次の例は、INSERT ステートメントと UPDATE ステートメントの両方を含む [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] データベースのストアド プロシージャを示しています。 このプロシージャを、単一の MERGE ステートメントで同等の操作を実行するように変更します。  
  
```sql  
CREATE PROCEDURE dbo.InsertUnitMeasure  
    @UnitMeasureCode nchar(3),  
    @Name nvarchar(25)  
AS
BEGIN  
    SET NOCOUNT ON;  
-- Update the row if it exists.
    UPDATE Production.UnitMeasure  
SET Name = @Name  
WHERE UnitMeasureCode = @UnitMeasureCode  
-- Insert the row if the UPDATE statement failed.  
IF (@@ROWCOUNT = 0 )  
BEGIN  
    INSERT INTO Production.UnitMeasure (UnitMeasureCode, Name)  
    VALUES (@UnitMeasureCode, @Name)  
END  
END;  
GO  
-- Test the procedure and return the results.  
EXEC InsertUnitMeasure @UnitMeasureCode = 'ABC', @Name = 'Test Value';  
SELECT UnitMeasureCode, Name FROM Production.UnitMeasure  
WHERE UnitMeasureCode = 'ABC';  
GO  
  
-- Rewrite the procedure to perform the same operations using the
-- MERGE statement.  
-- Create a temporary table to hold the updated or inserted values
-- from the OUTPUT clause.  
CREATE TABLE #MyTempTable  
    (ExistingCode nchar(3),  
     ExistingName nvarchar(50),  
     ExistingDate datetime,  
     ActionTaken nvarchar(10),  
     NewCode nchar(3),  
     NewName nvarchar(50),  
     NewDate datetime  
    );  
GO  
ALTER PROCEDURE dbo.InsertUnitMeasure  
    @UnitMeasureCode nchar(3),  
    @Name nvarchar(25)  
AS
BEGIN  
    SET NOCOUNT ON;  
  
    MERGE Production.UnitMeasure AS target  
    USING (SELECT @UnitMeasureCode, @Name) AS source (UnitMeasureCode, Name)  
    ON (target.UnitMeasureCode = source.UnitMeasureCode)  
    WHEN MATCHED THEN
        UPDATE SET Name = source.Name  
    WHEN NOT MATCHED THEN  
        INSERT (UnitMeasureCode, Name)  
        VALUES (source.UnitMeasureCode, source.Name)  
    OUTPUT deleted.*, $action, inserted.* INTO #MyTempTable;  
END;  
GO  
-- Test the procedure and return the results.  
EXEC InsertUnitMeasure @UnitMeasureCode = 'ABC', @Name = 'New Test Value';  
EXEC InsertUnitMeasure @UnitMeasureCode = 'XYZ', @Name = 'Test Value';  
EXEC InsertUnitMeasure @UnitMeasureCode = 'ABC', @Name = 'Another Test Value';  
  
SELECT * FROM #MyTempTable;  
-- Cleanup
DELETE FROM Production.UnitMeasure WHERE UnitMeasureCode IN ('ABC','XYZ');  
DROP TABLE #MyTempTable;  
GO  
```  
  
### <a name="b-using-merge-to-do-update-and-delete-operations-on-a-table-in-a-single-statement"></a>B. MERGE を使用して、単一のステートメントでテーブルに UPDATE 操作と DELETE 操作を実行する

次の例では、MERGE を使用して、`ProductInventory` テーブルで処理される注文に基づいて、[!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] サンプル データベース内の `SalesOrderDetail` テーブルを毎日更新します。 `Quantity` テーブルで各製品のその日の注文数を差し引くことで、`ProductInventory` テーブルの `SalesOrderDetail` 列を更新します。 製品の注文数によって、製品の在庫レベルが 0 以下に低下した場合は、その製品の行が `ProductInventory` テーブルから削除されます。  
  
```sql  
CREATE PROCEDURE Production.usp_UpdateInventory  
    @OrderDate datetime  
AS  
MERGE Production.ProductInventory AS target  
USING (SELECT ProductID, SUM(OrderQty) FROM Sales.SalesOrderDetail AS sod  
    JOIN Sales.SalesOrderHeader AS soh  
    ON sod.SalesOrderID = soh.SalesOrderID  
    AND soh.OrderDate = @OrderDate  
    GROUP BY ProductID) AS source (ProductID, OrderQty)  
ON (target.ProductID = source.ProductID)  
WHEN MATCHED AND target.Quantity - source.OrderQty <= 0  
    THEN DELETE  
WHEN MATCHED
    THEN UPDATE SET target.Quantity = target.Quantity - source.OrderQty,
                    target.ModifiedDate = GETDATE()  
OUTPUT $action, Inserted.ProductID, Inserted.Quantity,
    Inserted.ModifiedDate, Deleted.ProductID,  
    Deleted.Quantity, Deleted.ModifiedDate;  
GO  
  
EXECUTE Production.usp_UpdateInventory '20030501'  
```  
  
### <a name="c-using-merge-to-do-update-and-insert-operations-on-a-target-table-by-using-a-derived-source-table"></a>C. MERGE で、派生ソース テーブルを使用して対象テーブルに UPDATE 操作と INSERT 操作を実行する

次の例は、MERGE を使用し、行を更新または挿入することで [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] データベース内の `SalesReason` テーブルを変更します。 ソース テーブルの `NewName` の値が対象テーブル (`SalesReason`) の `Name` 列の値と一致すると、対象テーブルの `ReasonType` 列が更新されます。 `NewName` の値が一致しない場合は、ソース行が対象テーブルに挿入されます。 ソース テーブルは、[!INCLUDE[tsql](../../includes/tsql-md.md)] テーブル値コンストラクターを使用して複数の行を指定する派生テーブルです。 派生テーブルでテーブル値コンストラクターを使用する方法について詳しくは、「[テーブル値コンストラクター &#40;Transact-SQL&#41;](../../t-sql/queries/table-value-constructor-transact-sql.md)」をご覧ください。 この例では、テーブル変数内の OUTPUT 句の結果を格納する方法も示します。 さらに、挿入された行と更新された行の数を返す単純な選択操作を実行して、MERGE ステートメントの結果を集計します。  
  
```sql  
-- Create a temporary table variable to hold the output actions.  
DECLARE @SummaryOfChanges TABLE(Change VARCHAR(20));  
  
MERGE INTO Sales.SalesReason AS Target  
USING (VALUES ('Recommendation','Other'), ('Review', 'Marketing'),
              ('Internet', 'Promotion'))  
       AS Source (NewName, NewReasonType)  
ON Target.Name = Source.NewName  
WHEN MATCHED THEN  
UPDATE SET ReasonType = Source.NewReasonType  
WHEN NOT MATCHED BY TARGET THEN  
INSERT (Name, ReasonType) VALUES (NewName, NewReasonType)  
OUTPUT $action INTO @SummaryOfChanges;  
  
-- Query the results of the table variable.  
SELECT Change, COUNT(*) AS CountPerChange  
FROM @SummaryOfChanges  
GROUP BY Change;  
```  
  
### <a name="d-inserting-the-results-of-the-merge-statement-into-another-table"></a>D. MERGE ステートメントの結果を別のテーブルに挿入する

次の例では、MERGE ステートメントの OUTPUT 句から返されたデータをキャプチャし、そのデータを別のテーブルに挿入します。 MERGE ステートメントは、`Quantity` テーブル内で処理される注文に基づいて、[!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] データベース内の `ProductInventory` テーブルの `SalesOrderDetail` 列を毎日更新します。 この例では、更新された行をキャプチャし、在庫変更の追跡に使用する別のテーブルに挿入します。  
  
```sql  
CREATE TABLE Production.UpdatedInventory  
    (ProductID INT NOT NULL, LocationID int, NewQty int, PreviousQty int,  
     CONSTRAINT PK_Inventory PRIMARY KEY CLUSTERED (ProductID, LocationID));  
GO  
INSERT INTO Production.UpdatedInventory  
SELECT ProductID, LocationID, NewQty, PreviousQty
FROM  
(    MERGE Production.ProductInventory AS pi  
     USING (SELECT ProductID, SUM(OrderQty)
            FROM Sales.SalesOrderDetail AS sod  
            JOIN Sales.SalesOrderHeader AS soh  
            ON sod.SalesOrderID = soh.SalesOrderID  
            AND soh.OrderDate BETWEEN '20030701' AND '20030731'  
            GROUP BY ProductID) AS src (ProductID, OrderQty)  
     ON pi.ProductID = src.ProductID  
    WHEN MATCHED AND pi.Quantity - src.OrderQty >= 0
        THEN UPDATE SET pi.Quantity = pi.Quantity - src.OrderQty  
    WHEN MATCHED AND pi.Quantity - src.OrderQty <= 0
        THEN DELETE  
    OUTPUT $action, Inserted.ProductID, Inserted.LocationID,
        Inserted.Quantity AS NewQty, Deleted.Quantity AS PreviousQty)  
 AS Changes (Action, ProductID, LocationID, NewQty, PreviousQty)
 WHERE Action = 'UPDATE';  
GO  
```  

### <a name="e-using-merge-to-do-insert-or-update-on-a-target-edge-table-in-a-graph-database"></a>E. MERGE を使用して、グラフ データベース内のターゲット エッジ テーブルに対する INSERT または UPDATE を実行する

この例では、ノード テーブル `Person` および `City` と、エッジ テーブル `livesIn` を作成します。 `livesIn` エッジに対して MERGE ステートメントを使用し、`Person` と `City` の間にエッジがまだ存在していなければ、新しい行を挿入します。 エッジが既に存在する場合は、`livesIn` エッジに対して StreetAddress 属性の更新を行います。

```sql
-- CREATE node and edge tables
CREATE TABLE Person
    (
        ID INTEGER PRIMARY KEY,
        PersonName VARCHAR(100)
    )
AS NODE
GO

CREATE TABLE City
    (
        ID INTEGER PRIMARY KEY,
        CityName VARCHAR(100),
        StateName VARCHAR(100)
    )
AS NODE
GO

CREATE TABLE livesIn
    (
        StreetAddress VARCHAR(100)
    )
AS EDGE
GO

-- INSERT some test data into node and edge tables
INSERT INTO Person VALUES (1, 'Ron'), (2, 'David'), (3, 'Nancy')
GO

INSERT INTO City VALUES (1, 'Redmond', 'Washington'), (2, 'Seattle', 'Washington')
GO

INSERT livesIn SELECT P.$node_id, C.$node_id, c
FROM Person P, City C, (values (1,1, '123 Avenue'), (2,2,'Main Street')) v(a,b,c)
WHERE P.id = a AND C.id = b
GO

-- Use MERGE to update/insert edge data
CREATE OR ALTER PROCEDURE mergeEdge
    @PersonId integer,
    @CityId integer,
    @StreetAddress varchar(100)
AS
BEGIN
    MERGE livesIn
        USING ((SELECT @PersonId, @CityId, @StreetAddress) AS T (PersonId, CityId, StreetAddress)
                JOIN Person ON T.PersonId = Person.ID
                JOIN City ON T.CityId = City.ID)
        ON MATCH (Person-(livesIn)->City)
    WHEN MATCHED THEN
        UPDATE SET StreetAddress = @StreetAddress
    WHEN NOT MATCHED THEN
        INSERT ($from_id, $to_id, StreetAddress)
        VALUES (Person.$node_id, City.$node_id, @StreetAddress) ;
END
GO

-- Following will insert a new edge in the livesIn edge table
EXEC mergeEdge 3, 2, '4444th Avenue'
GO

-- Following will update the StreetAddress on the edge that connects Ron to Redmond
EXEC mergeEdge 1, 1, '321 Avenue'
GO

-- Verify that all the address were added/updated correctly
SELECT PersonName, CityName, StreetAddress
FROM Person , City , livesIn
WHERE MATCH(Person-(livesIn)->city)
GO
```
  
## <a name="see-also"></a>参照

- [SELECT &#40;Transact-SQL&#41;](../../t-sql/queries/select-transact-sql.md)
- [INSERT &#40;Transact-SQL&#41;](../../t-sql/statements/insert-transact-sql.md)
- [UPDATE &#40;Transact-SQL&#41;](../../t-sql/queries/update-transact-sql.md)
- [DELETE &#40;Transact-SQL&#41;](../../t-sql/statements/delete-transact-sql.md)
- [OUTPUT 句 &#40;Transact-SQL&#41;](../../t-sql/queries/output-clause-transact-sql.md)
- [Integration Services パッケージで MERGE を実行する](../../integration-services/control-flow/merge-in-integration-services-packages.md)
- [FROM &#40;Transact-SQL&#41;](../../t-sql/queries/from-transact-sql.md)
- [テーブル値コンストラクター &#40;Transact-SQL&#41;](../../t-sql/queries/table-value-constructor-transact-sql.md)  
