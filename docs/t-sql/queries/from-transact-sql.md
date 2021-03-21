---
title: FROM 句と JOIN、APPLY、PIVOT (T-SQL)
description: FROM 句と JOIN、APPLY、PIVOT (Transact-SQL)
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- JOIN
- FROM_TSQL
- FROM
- JOIN_TSQL
- OUTER_JOIN_TSQL
- INNER_JOIN_TSQL
- CROSS_TSQL
- CROSS_APPLY_TSQL
- APPLY_TSQL
- CROSS_JOIN_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- OUTER APPLY operator
- hints [SQL Server], FROM clause
- SELECT statement [SQL Server], FROM clause
- ISO syntax
- DELETE statement [SQL Server], FROM clause
- CROSS APPLY operator
- FROM clause
- APPLY operator
- joins [SQL Server], FROM clause
- UPDATE statement [SQL Server], FROM clause
- derived tables
ms.assetid: 36b19e68-94f6-4539-aeb1-79f5312e4263
author: VanMSFT
ms.author: vanto
ms.custom: ''
ms.date: 06/01/2019
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 48f59f002589a2a609f38753cf49004367d25412
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104751752"
---
# <a name="from-clause-plus-join-apply-pivot-transact-sql"></a>FROM 句と JOIN、APPLY、PIVOT (Transact-SQL)

[!INCLUDE [sqlserver2016-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sqlserver2016-asdb-asdbmi-asa-pdw.md)]

Transact-SQL では、FROM 句は次のステートメントで利用できます。

- [DELETE](../statements/delete-transact-sql.md)
- [UPDATE](update-transact-sql.md)
- [SELECT](select-transact-sql.md)

FROM 句は通常、SELECT ステートメントで必要です。 例外は、テーブル列がリストアップされず、リストアップされる唯一の項目がリテラルか、変数か、数式の時です。

この記事では、FROM 句で使用できる次のキーワードについても説明します。

- [JOIN](../../relational-databases/performance/joins.md)
- APPLY
- [PIVOT](from-using-pivot-and-unpivot.md)

![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)

## <a name="syntax"></a>構文  
  
```syntaxsql
-- Syntax for SQL Server and Azure SQL Database  
  
[ FROM { <table_source> } [ ,...n ] ]   
<table_source> ::=   
{  
    table_or_view_name [ FOR SYSTEM_TIME <system_time> ] [ AS ] table_alias ]   
        [ <tablesample_clause> ]   
        [ WITH ( < table_hint > [ [ , ]...n ] ) ]   
    | rowset_function [ [ AS ] table_alias ]   
        [ ( bulk_column_alias [ ,...n ] ) ]   
    | user_defined_function [ [ AS ] table_alias ]  
    | OPENXML <openxml_clause>   
    | derived_table [ [ AS ] table_alias ] [ ( column_alias [ ,...n ] ) ]   
    | <joined_table>   
    | <pivoted_table>   
    | <unpivoted_table>  
    | @variable [ [ AS ] table_alias ]  
    | @variable.function_call ( expression [ ,...n ] )   
        [ [ AS ] table_alias ] [ (column_alias [ ,...n ] ) ]     
}  
<tablesample_clause> ::=  
    TABLESAMPLE [SYSTEM] ( sample_number [ PERCENT | ROWS ] )   
        [ REPEATABLE ( repeat_seed ) ]   
  
<joined_table> ::=   
{  
    <table_source> <join_type> <table_source> ON <search_condition>   
    | <table_source> CROSS JOIN <table_source>   
    | left_table_source { CROSS | OUTER } APPLY right_table_source   
    | [ ( ] <joined_table> [ ) ]   
}  
<join_type> ::=   
    [ { INNER | { { LEFT | RIGHT | FULL } [ OUTER ] } } [ <join_hint> ] ]  
    JOIN  
  
<pivoted_table> ::=  
    table_source PIVOT <pivot_clause> [ [ AS ] table_alias ]  
  
<pivot_clause> ::=  
        ( aggregate_function ( value_column [ [ , ]...n ])   
        FOR pivot_column   
        IN ( <column_list> )   
    )   
  
<unpivoted_table> ::=  
    table_source UNPIVOT <unpivot_clause> [ [ AS ] table_alias ]  
  
<unpivot_clause> ::=  
    ( value_column FOR pivot_column IN ( <column_list> ) )   
  
<column_list> ::=  
    column_name [ ,...n ]   
  
<system_time> ::=  
{  
       AS OF <date_time>  
    |  FROM <start_date_time> TO <end_date_time>  
    |  BETWEEN <start_date_time> AND <end_date_time>  
    |  CONTAINED IN (<start_date_time> , <end_date_time>)   
    |  ALL  
}  
  
    <date_time>::=  
        <date_time_literal> | @date_time_variable  
  
    <start_date_time>::=  
        <date_time_literal> | @date_time_variable  
  
    <end_date_time>::=  
        <date_time_literal> | @date_time_variable  
```  
  
```syntaxsql
-- Syntax for Azure Synapse Analytics and Parallel Data Warehouse  
  
FROM { <table_source> [ ,...n ] }  
  
<table_source> ::=   
{  
    [ database_name . [ schema_name ] . | schema_name . ] table_or_view_name [ AS ] table_or_view_alias 
    [<tablesample_clause>]  
    | derived_table [ AS ] table_alias [ ( column_alias [ ,...n ] ) ]  
    | <joined_table>  
}  
  
<tablesample_clause> ::=
    TABLESAMPLE ( sample_number [ PERCENT ] ) -- Azure Synapse Analytics Dedicated SQL pool only  
 
<joined_table> ::=   
{  
    <table_source> <join_type> <table_source> ON search_condition   
    | <table_source> CROSS JOIN <table_source> 
    | left_table_source { CROSS | OUTER } APPLY right_table_source   
    | [ ( ] <joined_table> [ ) ]   
}  
  
<join_type> ::=   
    [ INNER ] [ <join hint> ] JOIN  
    | LEFT  [ OUTER ] JOIN  
    | RIGHT [ OUTER ] JOIN  
    | FULL  [ OUTER ] JOIN  
  
<join_hint> ::=   
    REDUCE  
    | REPLICATE  
    | REDISTRIBUTE  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
\<table_source>  
 [!INCLUDE[tsql](../../includes/tsql-md.md)] ステートメントの中で使用する、テーブル、ビュー、テーブル変数、または派生テーブル ソースを指定します。別名を付けて指定することができます。 1 つのステートメント内で 256 個までのテーブル ソースを使用できますが、この上限値は、使用可能なメモリとクエリ内の他の式の複雑さに応じて変化します。 個別のクエリは、256 個までのテーブル ソースをサポートできません。  
  
> [!NOTE]  
>  クエリのパフォーマンスは、クエリで参照される多くのテーブルによって低下する可能性があります。 コンパイルと最適化にかかる時間も、追加の要素によって影響を受けます。 これらの要素には、各 \<table_source> 上のインデックスとインデックス付きビューの存在、および SELECT ステートメント内の \<select_list> のサイズが含まれます。  
  
 FROM キーワードの後のテーブル ソースの順序は、返される結果セットには影響しません。 FROM 句内に重複した名前を指定すると、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] はエラーを返します。  
  
 *table_or_view_name*  
 テーブルまたはビューの名前です。  
  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の同じインスタンス上の別のデータベース内にテーブルまたはビューが存在する場合は、*database*.*schema*.*object_name* という形式の完全修飾名を使用します。  
  
 テーブルまたはビューが [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンスの外部に存在する場合は、*linked_server*.*catalog*.*schema*.*object* という形式の 4 部構成の名前を使用します。 詳細については、「 [sp_addlinkedserver &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-addlinkedserver-transact-sql.md)からデータにアクセスする方法について説明します。 名前の中のサーバー部分として [OPENDATASOURCE](../../t-sql/functions/opendatasource-transact-sql.md) 関数を使用して構成される 4 部構成の名前は、リモート テーブル ソースを指定するためにも使用できます。 OPENDATASOURCE を指定した場合は、*database_name* および *schema_name* がすべてのデータ ソースに適用されるとは限らず、リモート オブジェクトにアクセスする OLE DB プロバイダーの機能により制限されます。  
  
 [AS] *table_alias*  
 *table_source* の別名です。別名は、便宜上、または自己結合やサブクエリでテーブルまたはビューを区別するために使用できます。 別名にはテーブル名を短縮したものが指定されることが多く、結合されたテーブルの特定の列を参照するために使用されます。 結合された複数のテーブルに同じ列名が存在する場合、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、テーブル名、ビュー名、または別名で列名を修飾する必要があります。 別名が定義されている場合、テーブル名は使用できません。  
  
 派生テーブル、行セット、またはテーブル値関数、または演算子句 (PIVOT や UNPIVOT など) が使用されている場合、句の末尾に必要な *table_alias* は、グループ化列を含む、すべての返された列の関連テーブル名になります。  
  
 WITH (\<table_hint> )  
 クエリ オプティマイザーが、このテーブルを使用して、このステートメントに対し最適化またはロックを使用することを指定します。 詳細については、「[テーブル ヒント &#40;Transact-SQL&#41;](../../t-sql/queries/hints-transact-sql-table.md)」を参照してください。  
  
*rowset_function*  
**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降と [!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)]。  
OPENROWSET など、テーブル参照の代わりに使用できるオブジェクトを返す行セット関数のいずれかを指定します。 行セット関数の一覧の詳細については、「[行セット関数 &#40;Transact-SQL&#41;](../functions/opendatasource-transact-sql.md)」を参照してください。  
  
 OPENROWSET 関数および OPENQUERY 関数を使用したリモート オブジェクトの指定は、そのオブジェクトにアクセスする OLE DB プロバイダーの機能に依存します。  
  
 *bulk_column_alias*  
**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降と [!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)]。  
 結果セット内の列名に対する別名です。このパラメーターは省略可能です。 列の別名は、BULK オプションが指定された OPENROWSET 関数を使用する SELECT ステートメント内でのみ使用できます。 *bulk_column_alias* を使用する場合は、ファイル内の列と同じ順序ですべてのテーブル列に対して別名を指定します。  
  
> [!NOTE]  
>  この別名は、XML 形式のファイルの COLUMN 要素内に NAME 属性が存在する場合は、それをオーバーライドします。  
  
 *user_defined_function*  
 テーブル値関数を指定します。  
  
OPENXML \<openxml_clause>  
**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降と [!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)]。  
XML ドキュメントに対して行セット ビューを提供します。 詳細については、「[OPENXML &#40;Transact-SQL&#41;](../../t-sql/functions/openxml-transact-sql.md)」を参照してください。  
  
 *derived_table*  
 データベースから行を取得するサブクエリです。 *derived_table* は 1 つ上のレベルのクエリへの入力として使用されます。  
  
 *derived_table* では、[!INCLUDE[tsql](../../includes/tsql-md.md)] テーブル値コンストラクター機能を使用して、複数の行を指定できます。 たとえば、「 `SELECT * FROM (VALUES (1, 2), (3, 4), (5, 6), (7, 8), (9, 10) ) AS MyTable(a, b);` 」のように入力します。 詳細については、「[テーブル値コンストラクター &#40;Transact-SQL&#41;](../../t-sql/queries/table-value-constructor-transact-sql.md)」を参照してください。  
  
 *column_alias*  
 派生テーブルの結果セット内の列名に対する別名です。このパラメーターは省略可能です。 選択リストの各列の別名を 1 つずつ含みます。列の別名リスト全体をかっこで囲みます。  
  
 *table_or_view_name* FOR SYSTEM_TIME \<system_time> 適用対象**: [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 以降および [!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)]。  
データの特定のバージョンが、指定された時間的なテーブルとそのシステムのバージョン情報のリンクの履歴テーブルから返されることを指定します  
  
### <a name="tablesample-clause"></a>TABLESAMPLE 句
**適用対象:** SQL Server、SQL Database。テーブルからのデータのサンプルが返すことを指定します。 サンプルは、概数になる可能性があります。 この句は、SELECT または UPDATE ステートメント内の主テーブルまたは結合テーブルで使用できます。 TABLESAMPLE はビューを使用して指定できません。  
  
> [!NOTE]  
>  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] にアップグレードされたデータベースに対して TABLESAMPLE を使用するときは、データベースの互換性レベルは 110 以上に設定され、再帰共通テーブル式 (CTE) クエリでは PIVOT は許可されません。 詳細については、「[ALTER DATABASE 互換性レベル &#40;Transact-SQL&#41;](../../t-sql/statements/alter-database-transact-sql-compatibility-level.md)」を参照してください。  
  
 SYSTEM  
 ISO 標準で指定された、実装に依存するサンプリング方法です。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、これが唯一のサンプリング方法であり、既定で適用されます。 SYSTEM は、サンプルとしてテーブルからページがランダムに選択され、それらのページのすべての行がサンプル サブセットとして返される、ページ ベースのサンプリング方法を適用します。  
  
 *sample_number*  
 行数または行数に対応する割合を表す真数または概数値の定数値式です。 PERCENT を使用して指定されている場合、*sample_number* は、暗黙的に **float** 値に変換されます。それ以外の場合は **bigint** に変換されます。 PERCENT は既定値です。  
  
 PERCENT  
 *sample_number* で指定した割合の行がテーブルから取得されることを指定します。 PERCENT が指定されている場合、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] は指定された割合の概数を返します。 PERCENT が指定されている場合、*sample_number* 式は、0 ～ 100 の値に評価する必要があります。  
  
 ROWS  
 *sample_number* に指定した概数の行が取得されることを指定します。 ROWS が指定されている場合、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] は指定した行数の概数を返します。 ROWS が指定されている場合、*sample_number* 式は、0 より大きい整数値に評価される必要があります。  
  
 REPEATABLE  
 選択されたサンプルを再度返すことができることを示します。 同じ *repeat_seed* 値を使用して指定されている場合、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] はテーブル内の行に変更が行われない限り同じ行のサブセットを返します。 異なる *repeat_seed* 値を使用して指定されている場合、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] は、テーブル内の異なる行のサンプルをいくつか返す可能性があります。 テーブルに対する挿入、更新、削除、インデックスの再構築またはデフラグ、およびデータベースの復元またはアタッチは、変更と見なされます。  
  
 *repeat_seed*  
 乱数を生成するために [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] によって使用される整数の定数式です。 *repeat_seed* は **bigint** です。 *repeat_seed* が指定されていない場合は、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] によってランダムに値が割り当てられます。 テーブルに変更が適用されていない場合は、特定の *repeat_seed* 値に対して、サンプル結果は常に同じになります。 *repeat_seed* 式は、0 より大きい整数値に評価される必要があります。  
  
### <a name="tablesample-clause"></a>TABLESAMPLE 句
**適用対象:** [!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)]

 テーブルからのデータのサンプルが返されることを指定します。 サンプルは、概数になる可能性があります。 この句は、SELECT または UPDATE ステートメント内の主テーブルまたは結合テーブルで使用できます。 TABLESAMPLE はビューを使用して指定できません。 

 PERCENT  
 *sample_number* で指定した割合の行がテーブルから取得されることを指定します。 PERCENT が指定されている場合、[!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] は指定された割合の概数を返します。 PERCENT を指定する場合、*sample_number* 式は 0 ～ 100 の値に評価される必要があります。  


### <a name="joined-table"></a>結合テーブル 
結合テーブルは、2 つ以上のテーブルの積である結果セットです。 複数の結合については、かっこを使って結合の順序を変更できます。  
  
### <a name="join-type"></a>結合の種類
結合操作の種類を指定します。  
  
 INNER  
 一致するすべての行をペアで返すことを指定します。 両方のテーブルで一致しない行は廃棄します。 結合の種類が指定されていない場合は、これが既定値になります。  
  
 FULL [ OUTER ]  
 結合条件に合わない左側または右側のテーブルの行も結果セットに含まれ、他方のテーブルに対応する出力列は NULL に設定されることを指定します。 この処理は、INNER JOIN によって通常返される行も含めて、すべての行を返します。  
  
 LEFT [ OUTER ]  
 内部結合によって返されるすべての行に加えて、結合条件に合わない左側のテーブルのすべての行も結果セットに含まれます。右側のテーブルからの出力列は NULL に設定されることを指定します。  
  
 RIGHT [OUTER]  
 内部結合によって返されるすべての行に加えて、結合条件に合わない右側のテーブルのすべての行も結果セットに含まれます。左側のテーブルからの出力列は NULL に設定されることを指定します。  
  
### <a name="join-hint"></a>結合ヒント  
[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] および [!INCLUDE[ssSDS](../../includes/sssds-md.md)] の場合、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] クエリ オプティマイザーが、クエリの FROM 句で指定される結合ごとに、1 つの結合ヒントまたは実行アルゴリズムを使用することを指定します。 詳細については、「[結合ヒント &#40;Transact-SQL&#41;](../../t-sql/queries/hints-transact-sql-join.md)」を参照してください。  
  
 [!INCLUDE[ssSDW](../../includes/sssdw-md.md)] および [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] の場合、これらの結合ヒントは、2 つの配布互換性のない列での内部結合に適用されます。 これらは、クエリの処理中に発生するデータの移動量を制限することで、クエリのパフォーマンスを向上することができます。 [!INCLUDE[ssSDW](../../includes/sssdw-md.md)] および [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] の許容される結合ヒントは次のとおりです。  
  
 REDUCE  
 2 つの配布互換性のないテーブルを互換にするため、結合の右側にあるテーブルに移動する行の数を減らします。 REDUCE ヒントは、semi-join ヒントとも呼ばれます。  
  
 REPLICATE  
 結合の左側にあるテーブルの結合する列にある値を、すべてのノードにレプリケートされるようにします。 右側のテーブルは、これらの列のレプリケートされたバージョンに結合されます。  
  
 REDISTRIBUTE  
 2 つのデータ ソースの、JOIN 句で指定された列への配布を強制します。 配布されたテーブルに対し、[!INCLUDE[ssPDW](../../includes/sspdw-md.md)] では SHUFFLE_MOVE が実行されます。 レプリケートされたテーブルに対し、[!INCLUDE[ssPDW](../../includes/sspdw-md.md)] では TRIM_MOVE が実行されます。 これらの移動の種類を理解するには、[!INCLUDE[pdw-product-documentation](../../includes/pdw-product-documentation-md.md)] にある「Understanding Query Plans」 (クエリ プランについて) トピックの「DMS Query Plan Operations」 (DMS クエリ プランの操作) セクションを参照してください。 このヒントは、クエリ プランで BROADCAST_MOVE を使用して配布互換性のない結合を解決する際のパフォーマンスを向上させることができます。  
  
 JOIN  
 指定されたテーブル ソースまたはビューの間で、指定された結合操作が行われることを指定します。  
  
 ON \<search_condition>  
 結合するときの条件を指定します。 列と比較演算子はよく使用されますが、条件で任意の結合述語を指定できます。たとえば、次のようになります。  
  
```sql
SELECT p.ProductID, v.BusinessEntityID  
FROM Production.Product AS p   
JOIN Purchasing.ProductVendor AS v  
ON (p.ProductID = v.ProductID);  
  
```  
  
 列に結合条件を指定する場合、使用される列は、同じ列名、同じデータ型である必要はありません。ただし、データ型が異なる場合は、互換性のあるデータ型であるか、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] が暗黙的に変換できるデータ型である必要があります。 データ型が暗黙的に変換されない場合は、CONVERT 関数を使用して明示的に変換する必要があります。  
  
 ON 句に、結合されるテーブルのいずれかだけを指定している述語がある場合があります。 このような述語は、クエリの WHERE 句にもある場合があります。 このように述語を指定した場合、INNER 結合に対しては同じ結果になりますが、OUTER 結合が関係するときは結果が異なることがあります。 ON 句内の述語は結合の前にテーブルに適用されるのに対し、WHERE 句は意味的に結合結果に適用されるためです。  
  
 検索条件および述語の詳細については、「[検索条件 &#40;Transact-SQL&#41;](../../t-sql/queries/search-condition-transact-sql.md)」を参照してください。  
  
 CROSS JOIN  
 2 つのテーブルの結合を指定します。 SQL-92 形式でない旧形式の結合で WHERE 句が指定されていない場合と同じ行が返されます。  
  
 *left_table_source* { CROSS | OUTER } APPLY *right_table_source*  
 APPLY 演算子の *right_table_source* を *left_table_source* の各行に対して評価することを指定します。 この機能は、*right_table_source* に、引数として *left_table_source* から列の値を取得するテーブル値関数が含まれる場合に役立ちます。  
  
 CROSS または OUTER は、APPLY を使用して指定する必要があります。 CROSS を指定した場合は、*left_table_source* の指定行に対して *right_table_source* を評価し、空の結果セットが返されると、行は生成されません。  
  
 OUTER を指定した場合は、*left_table_source* の各行に対して *right_table_source* を評価し、空の結果セットが返されても、各行に対して 1 行が生成されます。  
  
 詳細については、「解説」を参照してください。  
  
 *left_table_source*  
 前の引数で定義されたテーブル ソースです。 詳細については、「解説」を参照してください。  
  
 *right_table_source*  
 前の引数で定義されたテーブル ソースです。 詳細については、「解説」を参照してください。  
  
### <a name="pivot-clause"></a>PIVOT 句

 *table_source* PIVOT \<pivot_clause>  
 *table_source* が *pivot_column* に基づいてピボットされることを指定します。 *table_source* はテーブルまたはテーブル式です。 出力は、*pivot_column* および *value_column* 以外の *table_source* のすべての列を含んでいるテーブルです。 *pivot_column* および *value_column* 以外の *table_source* の列は、ピボット演算子のグループ化列と呼ばれます。 PIVOT および UNPIVOT の詳細については、「[PIVOT および UNPIVOT の使用](../../t-sql/queries/from-using-pivot-and-unpivot.md)」を参照してください。  
  
 PIVOT は、グループ化列に関する入力テーブルに対してグループ化の操作を実行し、グループごとに 1 行のデータを返します。 さらに、出力では、*input_table* の *pivot_column* に表示される *column_list* で指定された値ごとに 1 列のデータが含まれます。  
  
 詳細については、後の「解説」を参照してください。  
  
 *aggregate_function*  
 システムまたはユーザー定義の集計関数で、1 つ以上の入力を受け取ります。 集計関数は、NULL 値に固定されます。 NULL 値に固定された集計関数は、集計値を評価する際に、グループ内の NULL 値を考慮しません。  
  
 COUNT(*) システム集計関数は使用できません。  
  
 *value_column*  
 PIVOT 演算子の値列です。 UNPIVOT と共に使用される場合、*value_column* は、入力 *table_source* 内の既存の列の名前にすることはできません。  
  
 FOR *pivot_column*  
 PIVOT 演算子のピボット列です。 *pivot_column* は、暗黙的または明示的に **nvarchar()** 型に変換できる型である必要があります。 この列は **image** または **rowversion** にすることはできません。  
  
 UNPIVOT が使用される場合、*pivot_column* は、*table_source* から絞り込まれる出力列の名前です。 *table_source* 内に、この名前が付けられている既存の列がないことが条件となります。  
  
 IN (*column_list* )  
 PIVOT 句で使用する場合は、出力テーブルの列名になる *pivot_column* の値の一覧を指定します。 この一覧では、ピボットの対象となっている入力 *table_source* 内に既に存在している列名は指定できません。  
  
 UNPIVOT 句で使用する場合は、単一の *pivot_column* に絞り込まれる *table_source* 内の列の一覧を指定します。  
  
 *table_alias*  
 出力テーブルの別名です。 *pivot_table_alias* を指定する必要があります。  
  
 UNPIVOT \<unpivot_clause>  
 入力テーブルが *column_list* 内の複数の列から *pivot_column* と呼ばれる単一の列に絞り込まれることを指定します。 PIVOT および UNPIVOT の詳細については、「[PIVOT および UNPIVOT の使用](../../t-sql/queries/from-using-pivot-and-unpivot.md)」を参照してください。  
  
AS OF \<date_time>  
**適用対象**: [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 以降と [!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)]。  
これまでの時間内には、指定位置で行ごとに実際の値を含む (現在) の 1 つのレコードを含むテーブルを返します。 内部的には、共用体はテンポラル テーブルとその履歴テーブルの間に実行されます。結果はフィルター処理され、 *\<date_time>* パラメーターによって指定される時点で有効であった行で値を返します。 *system_start_time_column_name* 値が *\<date_time>* パラメーター値と等しいかそれよりも小さく、*system_end_time_column_name* 値が *\<date_time>* パラメーター値より大きい場合に、行の値は有効と見なされます。   
  
FROM \<start_date_time> TO \<end_date_time>
: [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 以降および [!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)]**に該当します。**
FROM 引数の *\<start_date_time>* パラメーター値の前からアクティブ状態が始まったかどうかに関係なく、または、TO 引数の *\<end_date_time>* パラメーター値の後でアクティブ状態を止めたかどうかに関係なく、指定の時間範囲内でアクティブであったすべてのレコード バージョンの値を含むテーブルを返します。 内部的には、共用体が一時的なテーブルとその履歴テーブルの間実行され、結果をフィルター処理すると、指定した時間範囲の中にいつでもにアクティブだったすべての行のバージョンの値を返します。 FROM エンドポイントによって定義されている下限の境界で正確にアクティブになった行のサイズが含まれ、宛先エンドポイントによって定義された境界の上限で正確にアクティブになった行は含まれません。  
  
BETWEEN \<start_date_time> AND \<end_date_time>  
**適用対象**: [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 以降と [!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)]。  
\<end_date_time> エンドポイントで定義された上限の境界によってアクティブになった行が含まれることを除き、上記の **FROM \<start_date_time> TO \<end_date_time>** の説明と同じです。  
  
CONTAINED IN (\<start_date_time> , \<end_date_time>)  
**適用対象**: [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 以降と [!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)]。  
開かれて、CONTAINED IN 引数の 2 つの datetime 値で定義されている指定時間範囲内に閉じられた、すべてのレコードのバージョンの値が含まれるテーブルを返します。 行が下位の境界に正確に有効になったまたは上限の境界上だけでアクティブにされているが中断されることでは、含まれています。  
  
ALL  
現在のテーブルと、履歴テーブルの両方からのすべての行から値を持つテーブルを返します。  
  
## <a name="remarks"></a>解説  
 FROM 句は、結合テーブルと派生テーブルに対して SQL-92-SQL 構文がサポートされています。 SQL-92 構文には、INNER、LEFT OUTER、RIGHT OUTER、FULL OUTER、および CROSS 結合演算子が用意されています。  
  
 FROM 句内での UNION と JOIN は、ビュー内で、および派生テーブルやサブクエリ内でサポートされています。  
  
 自己結合は、そのテーブル自体に対して結合されるテーブルです。 自己結合に基づいた挿入または更新の操作は、FROM 句の順序に従います。  
  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] は列の分布統計を提供するリンク サーバーからの分布統計および基数統計を検討するので、REMOTE 結合ヒントを使用して結合をリモートから評価する必要はありません。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] クエリ プロセッサはリモートの統計量を検討し、リモート結合の方法が適切かどうかを決定します。 REMOTE 結合ヒントは、列の分布統計を提供しないプロバイダーに対しては便利です。  
  
## <a name="using-apply"></a>APPLY の使用  
 APPLY 演算子の左右両方のオペランドはテーブル式です。 これらのオペランドの主な相違点は、*right_table_source* は関数の引数として *left_table_source* から列を取得するテーブル値関数を使用できるということです。 *left_table_source* には、テーブル値関数を含めることができますが、*right_table_source* からの列である引数を含めることはできません。  
  
APPLY 演算子は、FROM 句のテーブル ソースを作成するために、次の方法で動作します。  
  
1.  *left_table_source* の各行に対して *right_table_source* を評価し、行セットを作成します。  
  
    *right_table_source* の値は *left_table_source* に依存します。 *right_table_source* はほぼ `TVF(left_table_source.row)` の方式で表記できます (`TVF` はテーブル値関数)。  
  
2.  UNION ALL 操作を実行し、*right_table_source* の評価の各行に対して作成された結果セットを *left_table_source* に結合します。  
  
    APPLY 演算子の結果として作成される列の一覧は、*right_table_source* からの列の一覧と結合された *left_table_source* からの列セットです。  
  
## <a name="using-pivot-and-unpivot"></a>PIVOT および UNPIVOT の使用  
 *pivot_column* および *value_column* は、PIVOT 演算子によって使用されるグループ化列です。 PIVOT は、次のプロセスに従って出力結果セットを取得します。  
  
1.  グループ化列に対して *input_table* 上で GROUP BY を実行し、各グループに対して 1 行の出力行を作成します。  
  
     出力行内のグループ化列は、*input_table* 内の該当するグループに対応する列の値を取得します。  
  
2.  次の処理を実行して、各出力行の列の一覧内の列に対して値を生成します。  
  
    1.  *pivot_column* に対して前の手順の GROUP BY で生成された行をさらにグループ化します。  
  
         *column_list* 内の各出力列に対して、次の条件を満たすサブグループを選択します。  
  
         `pivot_column = CONVERT(<data type of pivot_column>, 'output_column')`  
  
    2.  *aggregate_function* は、このサブグループ上の *value_column* に対して評価され、その結果は対応する *output_column* の値として返されます。 サブグループが空である場合は、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] は、その *output_column* に対して null 値を生成します。 集計関数が COUNT であり、サブグループが空である場合は、0 が返されます。  

> [!NOTE]
> `UNPIVOT` 句内の列識別子は、カタログ照合順序に従います。 [!INCLUDE[ssSDS_md](../../includes/sssds-md.md)] の場合、照合順序は常に `SQL_Latin1_General_CP1_CI_AS` です。 [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] の部分的包含データベースの場合、照合順序は常に `Latin1_General_100_CI_AS_KS_WS_SC` です。 列が他の列と結合されている場合、競合を回避するために COLLATE 句 (`COLLATE DATABASE_DEFAULT`) が必要です。   
  
 詳細については、PIVOT および UNPIVOT の例を含む、「[PIVOT および UNPIVOT の使用](../../t-sql/queries/from-using-pivot-and-unpivot.md)」を参照してください。  
  
## <a name="permissions"></a>アクセス許可  
 DELETE、SELECT、または UPDATE ステートメントに対する権限が必要です。  
  
## <a name="examples"></a>例  
  
### <a name="a-using-a-simple-from-clause"></a>A. 単純な FROM 句を使用する  
 次の例では、[!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] サンプル データベース内の `TerritoryID` テーブルから `Name` および `SalesTerritory` 列を取得します。  
  
```sql    
SELECT TerritoryID, Name  
FROM Sales.SalesTerritory  
ORDER BY TerritoryID ;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
TerritoryID Name                            
----------- ------------------------------  
1           Northwest                       
2           Northeast                       
3           Central                         
4           Southwest                       
5           Southeast                       
6           Canada                          
7           France                          
8           Germany                         
9           Australia                       
10          United Kingdom                  
(10 row(s) affected)  
```  
  
### <a name="b-using-the-tablock-and-holdlock-optimizer-hints"></a>B. TABLOCK および HOLDLOCK オプティマイザー ヒントを使用する  
 次の部分的なトランザクションでは、明示的な共有テーブル ロックを `Employee` に設定する方法と、インデックスを読み取る方法を示します。 ロックはトランザクション全体をとおして保持されます。  
  
```sql    
BEGIN TRAN  
SELECT COUNT(*)   
FROM HumanResources.Employee WITH (TABLOCK, HOLDLOCK) ;  
```  
  
### <a name="c-using-the-sql-92-cross-join-syntax"></a>C. SQL-92 CROSS JOIN 構文を使用する  
 次の例は、[!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] データベース内の 2 つのテーブル `Employee` と `Department` がクロスした結果を返します。 `BusinessEntityID` 行とすべての `Department` の名前の行を組み合わせた場合に、作成される可能性があるすべての組み合わせの一覧が返されます。  
  
```sql    
SELECT e.BusinessEntityID, d.Name AS Department  
FROM HumanResources.Employee AS e  
CROSS JOIN HumanResources.Department AS d  
ORDER BY e.BusinessEntityID, d.Name ;  
```  
  
### <a name="d-using-the-sql-92-full-outer-join-syntax"></a>D. SQL-92 FULL OUTER JOIN 構文を使用する  
 次の例は、[!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] データベース内の `SalesOrderDetail` テーブル内の製品名、および対応する販売注文を返します。 また、`Product` テーブル内に製品が一覧表示されていない販売注文、および `Product` テーブル内に一覧表示されている製品以外の販売注文に対する製品も返します。  
  
```sql  
-- The OUTER keyword following the FULL keyword is optional.  
SELECT p.Name, sod.SalesOrderID  
FROM Production.Product AS p  
FULL OUTER JOIN Sales.SalesOrderDetail AS sod  
ON p.ProductID = sod.ProductID  
ORDER BY p.Name ;  
```  
  
### <a name="e-using-the-sql-92-left-outer-join-syntax"></a>E. SQL-92 LEFT OUTER JOIN 構文を使用する  
 次の例では、`ProductID` の 2 つのテーブルを結合し、左側のテーブルから一致しない行を取り出します。 `Product` テーブルは、各テーブル内の `SalesOrderDetail` 列について `ProductID` テーブルと照合されます。 注文されたかどうかにかかわらず、すべての製品が結果セットに表示されます。  
  
```sql    
SELECT p.Name, sod.SalesOrderID  
FROM Production.Product AS p  
LEFT OUTER JOIN Sales.SalesOrderDetail AS sod  
ON p.ProductID = sod.ProductID  
ORDER BY p.Name ;  
```  
  
### <a name="f-using-the-sql-92-inner-join-syntax"></a>F. SQL-92 INNER JOIN 構文を使用する  
 次の例では、すべての製品名と販売注文 ID を返します。  
  
```sql    
-- By default, SQL Server performs an INNER JOIN if only the JOIN   
-- keyword is specified.  
SELECT p.Name, sod.SalesOrderID  
FROM Production.Product AS p  
INNER JOIN Sales.SalesOrderDetail AS sod  
ON p.ProductID = sod.ProductID  
ORDER BY p.Name ;  
```  
  
### <a name="g-using-the-sql-92-right-outer-join-syntax"></a>G. SQL-92 RIGHT OUTER JOIN 構文を使用する  
 次の例では、`TerritoryID` の 2 つのテーブルを結合し、右側のテーブルから一致しない行を取り出します。 `SalesTerritory` テーブルは、各テーブル内の `TerritoryID` 列について `SalesPerson` テーブルと照合されます。 販売区域に割り当てられているかどうかに関係なく、すべての販売員は結果セットに表示されます。  
  
```sql    
SELECT st.Name AS Territory, sp.BusinessEntityID  
FROM Sales.SalesTerritory AS st   
RIGHT OUTER JOIN Sales.SalesPerson AS sp  
ON st.TerritoryID = sp.TerritoryID ;  
```  
  
### <a name="h-using-hash-and-merge-join-hints"></a>H. HASH および MERGE 結合ヒントを使用する  
 次の例では、`Product`、`ProductVendor`、および `Vendor` テーブルの 3 つのテーブル結合を実行して、製品とその仕入先の一覧を作成します。 クエリ オプティマイザーは、MERGE 結合を使用して `Product` と `ProductVendor` (`p` と `pv`) を結合します。 次に、`Product` と `ProductVendor` の MERGE 結合の結果が (`p` と `pv`)、`Vendor` テーブルに対して HASH 結合され、(`p` と `pv`) と `v` が作成されます。  
  
> [!IMPORTANT]  
>  結合ヒントを指定すると、INNER キーワードを省略することはできません。INNER JOIN を明示的に指定して、実行する必要があります。  
  
```sql    
SELECT p.Name AS ProductName, v.Name AS VendorName  
FROM Production.Product AS p   
INNER MERGE JOIN Purchasing.ProductVendor AS pv   
ON p.ProductID = pv.ProductID  
INNER HASH JOIN Purchasing.Vendor AS v  
ON pv.BusinessEntityID = v.BusinessEntityID  
ORDER BY p.Name, v.Name ;  
```  
  
### <a name="i-using-a-derived-table"></a>I. 派生テーブルを使用する  
 次の例では、派生テーブル、つまり `SELECT` 句の後に `FROM` ステートメントを使用することで、すべての従業員の姓と名、およびそれぞれの住所のある都市を返します。  
  
```sql    
SELECT RTRIM(p.FirstName) + ' ' + LTRIM(p.LastName) AS Name, d.City  
FROM Person.Person AS p  
INNER JOIN HumanResources.Employee e ON p.BusinessEntityID = e.BusinessEntityID   
INNER JOIN  
   (SELECT bea.BusinessEntityID, a.City   
    FROM Person.Address AS a  
    INNER JOIN Person.BusinessEntityAddress AS bea  
    ON a.AddressID = bea.AddressID) AS d  
ON p.BusinessEntityID = d.BusinessEntityID  
ORDER BY p.LastName, p.FirstName;  
```  
  
### <a name="j-using-tablesample-to-read-data-from-a-sample-of-rows-in-a-table"></a>J. TABLESAMPLE を使用してテーブル内のサンプル行からデータを読み取る  
 次の例では、`TABLESAMPLE` 句内で `FROM` を使用して、`10` テーブル内にあるすべての行の約 `Customer`% を返します。  
  
```sql    
SELECT *  
FROM Sales.Customer TABLESAMPLE SYSTEM (10 PERCENT) ;  
```  
  
### <a name="k-using-apply"></a>K. APPLY の使用  
次の例では、次のテーブルとテーブル値関数がデータベース内に存在することを前提としています。  

|オブジェクト名|[列名]|      
|---|---|   
|Departments|DeptID、DivisionID、DeptName、DeptMgrID|      
|EmpMgr|MgrID、EmpID|     
|Employees|EmpID、EmpLastName、EmpFirstName、EmpSalary|  
|GetReports(MgrID)|EmpID、EmpLastName、EmpSalary|     
  
指定された `MgrID` の直接または間接の監督下にあるすべての従業員の一覧を返す、`GetReports` テーブル値関数。  
  
この例では、`APPLY` を使用して、すべての部門と、各部門内のすべての従業員を返します。 特定の部門に従業員が存在しない場合は、その部門には行が返されません。  
  
```sql
SELECT DeptID, DeptName, DeptMgrID, EmpID, EmpLastName, EmpSalary  
FROM Departments d    
CROSS APPLY dbo.GetReports(d.DeptMgrID) ;  
```  
  
`EmpID`、`EmpLastName`、および `EmpSalary` 列に対して NULL 値を作成する、従業員が存在しない部門に対してもクエリによって行を作成する場合は、代わりに `OUTER APPLY` を使用します。  
  
```sql
SELECT DeptID, DeptName, DeptMgrID, EmpID, EmpLastName, EmpSalary  
FROM Departments d   
OUTER APPLY dbo.GetReports(d.DeptMgrID) ;  
```  
  
### <a name="l-using-cross-apply"></a>L. CROSS APPLY を使用する  
次の例では、`sys.dm_exec_cached_plans` 動的管理ビューに対してクエリを実行し、キャッシュにあるすべてのクエリ プランのプラン ハンドルを取得することによって、プラン キャッシュにあるすべてのクエリ プランのスナップショットを取得します。 これにより、プラン ハンドルを `sys.dm_exec_query_plan` に渡すように `CROSS APPLY` 演算子が指定されます。 現在プラン キャッシュにある各プランの XML プラン表示の出力は、返されるテーブルの `query_plan` 列に格納されます。  
  
```sql
USE master;  
GO  
SELECT dbid, object_id, query_plan   
FROM sys.dm_exec_cached_plans AS cp   
CROSS APPLY sys.dm_exec_query_plan(cp.plan_handle);   
GO  
```  
  
### <a name="m-using-for-system_time"></a>M. FOR SYSTEM_TIME を使用する  
  
**適用対象**: [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 以降と [!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)]。  
次の例では、FOR SYSTEM_TIME AS OF date_time_literal_or_variable 引数を使用して、2014 年 1 月 1 日の時点の実際 (現在) のテーブル行を返します。  
  
```sql
SELECT DepartmentNumber,   
    DepartmentName,   
    ManagerID,   
    ParentDepartmentNumber   
FROM DEPARTMENT  
FOR SYSTEM_TIME AS OF '2014-01-01'  
WHERE ManagerID = 5;
```  
  
次の例では、FOR SYSTEM_TIME FROM date_time_literal_or_variable TO date_time_literal_or_variable 引数を使用して、境界の上限を除く、定義された期間 (2013 年 1 月 1 日から 2014 年 1 月 1 日まで) にアクティブだったすべての行を返します。  
  
```sql
SELECT DepartmentNumber,   
    DepartmentName,   
    ManagerID,   
    ParentDepartmentNumber   
FROM DEPARTMENT  
FOR SYSTEM_TIME FROM '2013-01-01' TO '2014-01-01'  
WHERE ManagerID = 5;
```  
  
 次の例では、FOR SYSTEM_TIME BETWEEN date_time_literal_or_variable AND date_time_literal_or_variable 引数を使用して、境界の上限を含む、定義された期間 (2013 年 1 月 1 日から 2014 年 1 月 1 日まで) にアクティブだったすべての行を返します。  
  
```sql
SELECT DepartmentNumber,   
    DepartmentName,   
    ManagerID,   
    ParentDepartmentNumber   
FROM DEPARTMENT  
FOR SYSTEM_TIME BETWEEN '2013-01-01' AND '2014-01-01'  
WHERE ManagerID = 5;
```  
  
 次の例では、FOR SYSTEM_TIME CONTAINED IN ( date_time_literal_or_variable, date_time_literal_or_variable ) 引数を使用して、定義された期間 (2013 年 1 月 1 日から 2014 年 1 月 1 日まで) に開いて閉じられたすべての行を返します。  
  
```sql
SELECT DepartmentNumber,   
    DepartmentName,   
    ManagerID,   
    ParentDepartmentNumber   
FROM DEPARTMENT  
FOR SYSTEM_TIME CONTAINED IN ( '2013-01-01', '2014-01-01' )  
WHERE ManagerID = 5;
```  
  
 次の例では、リテラルではなく、変数を使用して、クエリの日付の境界値を指定します。  
  
```sql
DECLARE @AsOfFrom datetime2 = dateadd(month,-12, sysutcdatetime());
DECLARE @AsOfTo datetime2 = dateadd(month,-6, sysutcdatetime());
  
SELECT DepartmentNumber,   
    DepartmentName,   
    ManagerID,   
    ParentDepartmentNumber   
FROM DEPARTMENT  
FOR SYSTEM_TIME FROM @AsOfFrom TO @AsOfTo  
WHERE ManagerID = 5;
```  
  
## <a name="examples-sssdwfull-and-sspdw"></a>例: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)]、[!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="n-using-the-inner-join-syntax"></a>北 INNER JOIN 構文を使用する  
 次の例では、`FactInternetSales` テーブルと `DimProduct` テーブルから、結合キー `ProductKey` が両方のテーブルで一致する、`SalesOrderNumber`、`ProductKey`、`EnglishProductName` の列を返します。 `SalesOrderNumber` 列と`EnglishProductName` 列はそれぞれ、どちらか一方のテーブルにしか存在しないため、示されているように、これらの列を持つテーブルの別名を指定する必要はありません。これらの別名は読みやすくするために含まれています。 別名の前の **AS** という単語は必須ではありませんが、読みやすくするためと ANSI 標準に準拠するため、推奨されています。  
  
```sql
-- Uses AdventureWorks  
  
SELECT fis.SalesOrderNumber, dp.ProductKey, dp.EnglishProductName  
FROM FactInternetSales AS fis 
INNER JOIN DimProduct AS dp  
    ON dp.ProductKey = fis.ProductKey;  
```  
  
 内部結合には `INNER` キーワードは必要ないため、これと同じクエリを次のように記述することができます。  
  
```sql
-- Uses AdventureWorks  
  
SELECT fis.SalesOrderNumber, dp.ProductKey, dp.EnglishProductName  
FROM FactInternetSales AS fis 
JOIN DimProduct AS dp  
ON dp.ProductKey = fis.ProductKey;  
```  
  
 このクエリでも `WHERE` 句を使用して、結果を制限することができます。 次の例では、結果を 'SO5000' よりも大きい `SalesOrderNumber` 値に制限します。  
  
```sql
-- Uses AdventureWorks  
  
SELECT fis.SalesOrderNumber, dp.ProductKey, dp.EnglishProductName  
FROM FactInternetSales AS fis 
JOIN DimProduct AS dp  
    ON dp.ProductKey = fis.ProductKey  
WHERE fis.SalesOrderNumber > 'SO50000'  
ORDER BY fis.SalesOrderNumber;  
```  
  
### <a name="o-using-the-left-outer-join-and-right-outer-join-syntax"></a>O. LEFT OUTER JOIN と RIGHT OUTER JOIN 構文を使用する  
 次の例では、`FactInternetSales` テーブルと `DimProduct` テーブルを `ProductKey` 列で結合します。 LEFT OUTER JOIN 構文は、左 (`FactInternetSales`) テーブルからの一致しない行を保持します。 `FactInternetSales` テーブルには `DimProduct` テーブルと一致しない `ProductKey` 値は含まれないため、このクエリは、上記の最初の内部結合例と同じ行を返します。  
  
```sql
-- Uses AdventureWorks  
  
SELECT fis.SalesOrderNumber, dp.ProductKey, dp.EnglishProductName  
FROM FactInternetSales AS fis 
LEFT OUTER JOIN DimProduct AS dp  
    ON dp.ProductKey = fis.ProductKey;  
```  
  
 このクエリは、`OUTER` キーワードを使用しなくても記述できます。  
  
 右外部結合では、右テーブルからの一致しない行が保持されます。 次の例では、上記の左外部結合の例と同じ行を返します。  
  
```sql
-- Uses AdventureWorks  
  
SELECT fis.SalesOrderNumber, dp.ProductKey, dp.EnglishProductName  
FROM DimProduct AS dp 
RIGHT OUTER JOIN FactInternetSales AS fis  
    ON dp.ProductKey = fis.ProductKey;  
```  
  
 次のクエリは、左外部結合の左テーブルとして `DimSalesTerritory` テーブルを使用します。 `FactInternetSales` テーブルから `SalesOrderNumber` 値を取得します。 特定の `SalesTerritoryKey` に注文がない場合は、クエリはその行の `SalesOrderNumber` に対して NULL を返します。 このクエリは `SalesOrderNumber` 列で並べ替えられるため、この列内の NULL がすべて結果の上部に表示されます。  
  
```sql
-- Uses AdventureWorks  
  
SELECT dst.SalesTerritoryKey, dst.SalesTerritoryRegion, fis.SalesOrderNumber  
FROM DimSalesTerritory AS dst 
LEFT OUTER JOIN FactInternetSales AS fis  
    ON dst.SalesTerritoryKey = fis.SalesTerritoryKey  
ORDER BY fis.SalesOrderNumber;  
```  
  
 このクエリは、右外部結合で書き直して同じ結果を取得することができます。  
  
```sql
-- Uses AdventureWorks  
  
SELECT dst.SalesTerritoryKey, dst.SalesTerritoryRegion, fis.SalesOrderNumber  
FROM FactInternetSales AS fis 
RIGHT OUTER JOIN DimSalesTerritory AS dst  
    ON fis.SalesTerritoryKey = dst.SalesTerritoryKey  
ORDER BY fis.SalesOrderNumber;  
```  
  
### <a name="p-using-the-full-outer-join-syntax"></a>P. FULL OUTER JOIN 構文を使用する  
 次の例では、両方の結合テーブルからすべての行を返しますが、別のテーブルと一致しない値には NULL を返す完全外部結合を示します。  
  
```sql
-- Uses AdventureWorks  
  
SELECT dst.SalesTerritoryKey, dst.SalesTerritoryRegion, fis.SalesOrderNumber  
FROM DimSalesTerritory AS dst 
FULL OUTER JOIN FactInternetSales AS fis  
    ON dst.SalesTerritoryKey = fis.SalesTerritoryKey  
ORDER BY fis.SalesOrderNumber;  
```  
  
 このクエリは、`OUTER` キーワードを使用しなくても記述できます。  
  
```sql
-- Uses AdventureWorks  
  
SELECT dst.SalesTerritoryKey, dst.SalesTerritoryRegion, fis.SalesOrderNumber  
FROM DimSalesTerritory AS dst 
FULL JOIN FactInternetSales AS fis  
    ON dst.SalesTerritoryKey = fis.SalesTerritoryKey  
ORDER BY fis.SalesOrderNumber;  
```  
  
### <a name="q-using-the-cross-join-syntax"></a>Q. CROSS JOIN 構文を使用する  
 次の例では、`FactInternetSales` テーブルと `DimSalesTerritory` テーブルのクロス積を返します。 `SalesOrderNumber` と `SalesTerritoryKey` のすべての可能な組み合わせの一覧が返されます。 クロス結合クエリ内に `ON` 句がないことに注目してください。  
  
```sql
-- Uses AdventureWorks  
  
SELECT dst.SalesTerritoryKey, fis.SalesOrderNumber  
FROM DimSalesTerritory AS dst 
CROSS JOIN FactInternetSales AS fis  
ORDER BY fis.SalesOrderNumber;  
```  
  
### <a name="r-using-a-derived-table"></a>R. 派生テーブルを使用する  
 次の例では、派生テーブル (`FROM` 句の後の `SELECT` ステートメント) を使用して、`DimCustomer` テーブル内で、`BirthDate` 値が 1970 年 1 月 1 日以降で、姓が 'Smith' のすべての顧客の `CustomerKey` 列と `LastName` 列を返します。  
  
```sql
-- Uses AdventureWorks  
  
SELECT CustomerKey, LastName  
FROM  
   (SELECT * FROM DimCustomer  
    WHERE BirthDate > '01/01/1970') AS DimCustomerDerivedTable  
WHERE LastName = 'Smith'  
ORDER BY LastName;  
```  
  
### <a name="s-reduce-join-hint-example"></a>S. REDUCE 結合ヒントの例  
 次の例では、`REDUCE` 結合ヒントを使用して、クエリ内で派生テーブルの処理を変更します。 `REDUCE` 結合ヒントを使用する場合、`fis.ProductKey` は予測され、レプリケートされ、区別した後、`ProductKey` での `DimProduct` のシャッフル中に `DimProduct` に結合されます。 結果として得られる派生テーブルは、`fis.ProductKey` に配布されます。  
  
```sql
-- Uses AdventureWorks  
  
EXPLAIN SELECT SalesOrderNumber  
FROM  
   (SELECT fis.SalesOrderNumber, dp.ProductKey, dp.EnglishProductName  
    FROM DimProduct AS dp   
      INNER REDUCE JOIN FactInternetSales AS fis   
          ON dp.ProductKey = fis.ProductKey  
   ) AS dTable  
ORDER BY SalesOrderNumber;  
```  
  
### <a name="t-replicate-join-hint-example"></a>T. REPLICATE 結合ヒントの例  
 次の例は、前の例と同じクエリを示していますが、`REDUCE` 結合ヒントの代わりに `REPLICATE` 結合ヒントを使用している点が異なります。 `REPLICATE` ヒントを使用すると、`FactInternetSales` テーブルの `ProductKey` (結合) 列の値がすべてのノードにレプリケートされます。 `DimProduct` テーブルは、これらの値のレプリケートされたバージョンに結合されます。  
  
```sql
-- Uses AdventureWorks  
  
EXPLAIN SELECT SalesOrderNumber  
FROM  
   (SELECT fis.SalesOrderNumber, dp.ProductKey, dp.EnglishProductName  
    FROM DimProduct AS dp   
      INNER REPLICATE JOIN FactInternetSales AS fis  
          ON dp.ProductKey = fis.ProductKey  
   ) AS dTable  
ORDER BY SalesOrderNumber;  
```  
  
### <a name="u-using-the-redistribute-hint-to-guarantee-a-shuffle-move-for-a-distribution-incompatible-join"></a>U. REDISTRIBUTE ヒントを使用して、配布互換性のある結合に SHUFFLE_MOVE を保証する  
 次のクエリは、配布互換性のある結合で REDISTRIBUTE クエリ ヒントを使用します。 これにより、クエリ オプティマイザーがクエリ プランで SHUFFLE_MOVE を使用することが保証されます。 また、クエリ プランで分散テーブルをレプリケートされたテーブルに移動する、BROADCAST_MOVE を使用しないことも保証されます。  
  
 次の例では、REDISTRIBUTE ヒントが FactInternetSales テーブルでの SHUFFLE_MOVE を強制します。これは、ProductKey は DimProduct のディストリビューション列で、FactInternetSales のディストリビューション列ではないからです。  
  
```sql
-- Uses AdventureWorks  
  
EXPLAIN  
SELECT dp.ProductKey, fis.SalesOrderNumber, fis.TotalProductCost  
FROM DimProduct AS dp 
INNER REDISTRIBUTE JOIN FactInternetSales AS fis  
    ON dp.ProductKey = fis.ProductKey;  
```  

### <a name="v-using-tablesample-to-read-data-from-a-sample-of-rows-in-a-table"></a>V. TABLESAMPLE を使用してテーブル内のサンプル行からデータを読み取る  
 次の例では、`TABLESAMPLE` 句内で `FROM` を使用して、`10` テーブル内にあるすべての行の約 `Customer`% を返します。  
  
```sql    
SELECT *  
FROM Sales.Customer TABLESAMPLE SYSTEM (10 PERCENT) ;
```
  
## <a name="see-also"></a>参照  
 [CONTAINSTABLE &#40;Transact-SQL&#41;](../../relational-databases/system-functions/containstable-transact-sql.md)   
 [FREETEXTTABLE &#40;Transact-SQL&#41;](../../relational-databases/system-functions/freetexttable-transact-sql.md)   
 [INSERT &#40;Transact-SQL&#41;](../../t-sql/statements/insert-transact-sql.md)   
 [OPENQUERY &#40;Transact-SQL&#41;](../../t-sql/functions/openquery-transact-sql.md)   
 [OPENROWSET &#40;Transact-SQL&#41;](../../t-sql/functions/openrowset-transact-sql.md)   
 [演算子 &#40;Transact-SQL&#41;](../../t-sql/language-elements/operators-transact-sql.md)   
 [WHERE &#40;Transact-SQL&#41;](../../t-sql/queries/where-transact-sql.md)
