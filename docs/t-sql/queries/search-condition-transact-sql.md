---
description: 検索条件 (Transact-SQL)
title: 検索条件 (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 01/15/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- search
- Search Condition
- condition
dev_langs:
- TSQL
helpviewer_keywords:
- OR operator [Transact-SQL]
- CONTAINS predicate (Transact-SQL)
- ESCAPE keyword
- operators [Transact-SQL], search condition
- search conditions [SQL Server]
- WHERE clause, search conditions
- ALL keyword
- FREETEXT predicate (Transact-SQL)
- EXISTS keyword
- search conditions [SQL Server], about search conditions
- NOT operator [Transact-SQL]
- BETWEEN operator
- SOME | ANY keyword
- predicates [full-text search]
- AND, about search conditions
- logical operators [SQL Server], about search conditions
- precedence [SQL Server], logical operators
- logical operators [SQL Server], precedence
- LIKE comparisons
ms.assetid: 09974469-c5d2-4be8-bc5a-78e404660b2c
author: VanMSFT
ms.author: vanto
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: b19596b68a6ec8a4dac1eef04b14d186d60318ba
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104754442"
---
# <a name="search-condition-transact-sql"></a>検索条件 (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  論理演算子 AND、OR、および NOT を使用した、1 つ以上の述語の組み合わせです。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql
-- Syntax for SQL Server and Azure SQL Database  
  
<search_condition> ::=  
    MATCH(<graph_search_pattern>) | <search_condition_without_match> | <search_condition> AND <search_condition>

<search_condition_without_match> ::= 
    { [ NOT ] <predicate> | ( <search_condition_without_match> ) }   
    [ { AND | OR } [ NOT ] { <predicate> | ( <search_condition_without_match> ) } ]   
[ ...n ]   
  
<predicate> ::=   
    { expression { = | < > | ! = | > | > = | ! > | < | < = | ! < } expression   
    | string_expression [ NOT ] LIKE string_expression   
  [ ESCAPE 'escape_character' ]   
    | expression [ NOT ] BETWEEN expression AND expression   
    | expression IS [ NOT ] NULL   
    | CONTAINS   
  ( { column | * } , '<contains_search_condition>' )   
    | FREETEXT ( { column | * } , 'freetext_string' )   
    | expression [ NOT ] IN ( subquery | expression [ ,...n ] )   
    | expression { = | < > | ! = | > | > = | ! > | < | < = | ! < }   
  { ALL | SOME | ANY} ( subquery )   
    | EXISTS ( subquery )     }   
    
<graph_search_pattern> ::=
    { <node_alias> { 
                      { <-( <edge_alias> )- } 
                    | { -( <edge_alias> )-> }
                    <node_alias> 
                   } 
    }
  
<node_alias> ::=
    node_table_name | node_table_alias 

<edge_alias> ::=
    edge_table_name | edge_table_alias
```  
  
```syntaxsql
-- Syntax for Azure Synapse Analytics and Parallel Data Warehouse  
  
< search_condition > ::=   
    { [ NOT ] <predicate> | ( <search_condition> ) }   
    [ { AND | OR } [ NOT ] { <predicate> | ( <search_condition> ) } ]   
[ ...n ]   
  
<predicate> ::=   
    { expression { = | < > | ! = | > | > = | < | < = } expression   
    | string_expression [ NOT ] LIKE string_expression   
    | expression [ NOT ] BETWEEN expression AND expression   
    | expression IS [ NOT ] NULL   
    | expression [ NOT ] IN (subquery | expression [ ,...n ] )   
    | expression [ NOT ] EXISTS (subquery)     }   
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
 \<search_condition>  
 SELECT ステートメント、クエリ式、またはサブクエリの場合、結果セットに返す行の条件を指定します。 UPDATE ステートメントの場合、更新する行を指定します。 DELETE ステートメントの場合、削除する行を指定します。 [!INCLUDE[tsql](../../includes/tsql-md.md)] ステートメントの検索条件に含まれる述語の数に制限はありません。  
  
 \<graph_search_pattern>  
 グラフの一致パターンを指定します。 この句の引数の詳細については、「[MATCH &#40;Transact-SQL&#41;](../../t-sql/queries/match-sql-graph.md)」を参照してください
 
 NOT  
 述語によって指定されたブール式を否定します。 詳細については、「[NOT &#40;Transact-SQL&#41;](../../t-sql/language-elements/not-transact-sql.md)」を参照してください。  
  
 AND  
 2 つの条件を結合し、両方の条件が真の場合に TRUE と評価します。 詳細については、「[AND &#40;Transact-SQL&#41;](../../t-sql/language-elements/and-transact-sql.md)」を参照してください。  
  
 OR  
 2 つの条件を結合し、少なくとも片方の条件が真の場合に TRUE と評価します。 詳細については、「[OR &#40;Transact-SQL&#41;](../../t-sql/language-elements/or-transact-sql.md)」を参照してください。  
  
 \< predicate >  
 TRUE、FALSE、または UNKNOWN を返す式です。  
  
 *式 (expression)*  
 列名、定数、関数、変数、スカラー サブクエリ、または 1 つ以上の演算子やサブクエリで接続された列名、定数、関数の組み合わせです。 expression には CASE 式が含まれる場合もあります。  
  
> [!NOTE]  
>  Unicode ではない文字列定数と変数は、データベースの既定の照合順序に対応するコード ページを使用します。 コード ページの変換は、Unicode 以外の文字データのみで作業し、Unicode ではない文字データ型 **char**、**varchar**、および **text** を参照する場合に発生する可能性があります。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] は、コード ページがデータベースの既定照合順序に対応するコード ページと異なる場合に、Unicode 以外の文字列定数および変数を、参照される列または COLLATE を使用して指定された列の照合順序に対応するコード ページに変換します。 [最適なマッピング](https://www.unicode.org/Public/MAPPINGS/VENDORS/MICSFT/WindowsBestFit/)が見つかった場合、新しいコード ページで見つからない文字はすべて類似した文字に変換されます。それ以外の場合は、既定の置換文字 "?" に変換されます。  
>  
> 複数のコード ページを処理する場合、コード ページの変換を避けるために、文字定数の先頭に大文字の 'N' を付け、Unicode 変数を使用することができます。  
  
 =  
 2 つの式が等しいことを調べるのに使用する演算子です。  
  
 <>  
 2 つの式が互いに等しくないという条件を調べるのに使用する演算子です。  
  
 !=  
 2 つの式が互いに等しくないという条件を調べるのに使用する演算子です。  
  
 \>  
 一方の式がもう一方の式より大きいという条件を調べるのに使用する演算子です。  
  
 \>=  
 一方の式がもう一方の式以上であるという条件を調べるのに使用する演算子です。  
  
 !>  
 一方の式がもう一方の式より大きくないという条件を調べるのに使用する演算子です。  
  
 <  
 一方の式がもう一方の式より小さいという条件を調べるのに使用する演算子です。  
  
 <=  
 一方の式がもう一方の式以下であるという条件を調べるのに使用する演算子です。  
  
 !<  
 一方の式がもう一方の式より小さくないという条件を調べるのに使用する演算子です。  
  
 *string_expression*  
 文字列とワイルドカード文字から成る文字列です。  
  
 [ NOT ] LIKE  
 後続の文字列が、パターン照合で使用されます。 詳細については、「[LIKE &#40;Transact-SQL&#41;](../../t-sql/language-elements/like-transact-sql.md)」を参照してください。  
  
 ESCAPE **'** _escape_ character_ *_'_*  
 ワイルドカードとして機能するのではなく、ワイルドカード文字そのものを文字列内で検索できます。 *escape_character* は、ワイルドカード文字の前に付けてこの特別な使用方法を示す文字です。  
  
 [ NOT ] BETWEEN  
 両端を含む値の範囲を指定します。 最初と最後の値を除外するには、AND を使用します。 詳細については、「[BETWEEN &#40;Transact-SQL&#41;](../../t-sql/language-elements/between-transact-sql.md)」を参照してください。  
  
 IS [ NOT ] NULL  
 指定のキーワードに応じて、NULL 値または NULL 値以外の値の検索を指定します。 ビットごとの演算子や算術演算子を使う式は、オペランドの少なくとも 1 つが NULL であれば、NULL と評価されます。  
  
 CONTAINS  
 文字ベースのデータを含む列に対して、単語または語句との完全一致検索またはあいまい (*あいまい一致*) 検索、特定の範囲内での近接検索、および重み付き検索を行います。 このオプションは、SELECT ステートメントでのみ使用できます。 詳細については、「[CONTAINS &#40;Transact-SQL&#41;](../../t-sql/queries/contains-transact-sql.md)」を参照してください。  
  
 FREETEXT  
 文字ベースのデータを含む列で、述語内の文字の並びと正確に一致しなくても意味が合っている値を検索するための、単純形式の自然言語クエリを指定します。 このオプションは、SELECT ステートメントでのみ使用できます。 詳細については、「[FREETEXT &#40;Transact-SQL&#41;](../../t-sql/queries/freetext-transact-sql.md)」を参照してください。  
  
 [ NOT ] IN  
 式がリストに含まれているかどうかに基づいて、式の検索を指定します。 検索式には定数または列名を指定できます。またリストには、一般的にはサブクエリが指定されますが、一連の定数も指定できます。 値のリストはかっこで囲んでください。 詳細については、「[IN &#40;Transact-SQL&#41;](../../t-sql/language-elements/in-transact-sql.md)」を参照してください。  
  
 *subquery*  
 制限付きの SELECT ステートメントと見なすことができます。また、SELECT ステートメントの中の \<query_expression> に似ています。 ORDER BY 句および INTO キーワードは使用できません。 詳細については、「[SELECT &#40;Transact-SQL&#41;](../../t-sql/queries/select-transact-sql.md)」を参照してください。  
  
 ALL  
 比較演算子およびサブクエリと共に使用します。 サブクエリで取得されたすべての値が比較演算子の要件を満たしている場合は、\<predicate> に TRUE を返します。また、少なくとも 1 つの値が比較要件を満たしていない場合、またはサブクエリが外側のステートメントに行を返さなかった場合は、FALSE を返します。 詳細については、「[ALL &#40;Transact-SQL&#41;](../../t-sql/language-elements/all-transact-sql.md)」を参照してください。  
  
 { SOME | ANY }  
 比較演算子およびサブクエリと共に使用します。 サブクエリで取得された値の少なくとも 1 つが比較演算子の要件を満たしている場合は、\<predicate> に TRUE を返します。また、サブクエリの値がいずれも比較要件を満たしていない場合、またはサブクエリが外側のステートメントに行を返さなかった場合は、FALSE を返します。 それ以外の場合には、式は UNKNOWN と評価されます。 詳細については、「[SOME &#124; ANY &#40;Transact-SQL&#41;](../../t-sql/language-elements/some-any-transact-sql.md)」を参照してください。  
  
 EXISTS  
 サブクエリと共に使用され、サブクエリによって返される行が存在するかどうかを調べます。 詳細については、「[EXISTS &#40;Transact-SQL&#41;](../../t-sql/language-elements/exists-transact-sql.md)」を参照してください。  
  
## <a name="remarks"></a>解説  
 論理演算子の優先順位は、高い方から NOT、AND、OR です。 かっこを使用することによって、検索条件におけるこれらの優先順位をオーバーライドすることができます。 論理演算子の評価順序は、クエリ オプティマイザーが行う選択によって異なる場合があります。 論理演算子が論理値を操作する方法の詳細については、「[AND &#40;Transact-SQL&#41;](../../t-sql/language-elements/and-transact-sql.md)」、「[OR &#40;Transact-SQL&#41;](../../t-sql/language-elements/or-transact-sql.md)」、および「[NOT &#40;Transact-SQL&#41;](../../t-sql/language-elements/not-transact-sql.md)」を参照してください。  
  
## <a name="examples"></a>例  
  
### <a name="a-using-where-with-like-and-escape-syntax"></a>A. WHERE を LIKE および ESCAPE 構文と共に使用する  
 次の例では、`LargePhotoFileName` 列に文字列 `green_` が含まれている行を検索します。_ がワイルドカード文字であるため、`ESCAPE` オプションを使用しています。 `ESCAPE` オプションを指定しないと、単語 `green` の後に _ 以外の 1 文字が続く文字列を含む description 列の値もクエリで検索してしまいます。  
  
```sql  
USE AdventureWorks2012 ;  
GO  
SELECT *   
FROM Production.ProductPhoto  
WHERE LargePhotoFileName LIKE '%greena_%' ESCAPE 'a' ;  
```  
  
### <a name="b-using-where-and-like-syntax-with-unicode-data"></a>B. WHERE および LIKE 構文を UNICODE データと共に使用する  
 次の例では、`WHERE` 句を使用し、米国 (`US`) 国外を対象として、名前が `Pa` で始まる都市にあるすべての企業の住所を取得します。  
  
```sql  
USE AdventureWorks2012 ;  
GO  
SELECT AddressLine1, AddressLine2, City, PostalCode, CountryRegionCode    
FROM Person.Address AS a  
JOIN Person.StateProvince AS s ON a.StateProvinceID = s.StateProvinceID  
WHERE CountryRegionCode NOT IN ('US')  
AND City LIKE N'Pa%' ;  
```  
  
## <a name="examples-sssdwfull-and-sspdw"></a>例: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)]、[!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="c-using-where-with-like"></a>C. LIKE を含む WHERE を使用する  
 次の例では、`LastName` 列に `and` という文字列がある行を検索します。  
  
```sql  
-- Uses AdventureWorks  
  
SELECT EmployeeKey, LastName  
FROM DimEmployee  
WHERE LastName LIKE '%and%';  
```  
  
### <a name="d-using-where-and-like-syntax-with-unicode-data"></a>D. WHERE および LIKE 構文を UNICODE データと共に使用する  
 次の例では、`WHERE` 句を使用して `LastName` 列の Unicode 検索を実行します。  
  
```sql  
-- Uses AdventureWorks  
  
SELECT EmployeeKey, LastName  
FROM DimEmployee  
WHERE LastName LIKE N'%and%';  
```  
  
## <a name="see-also"></a>参照  
 [集計関数 &#40;Transact-SQL&#41;](../../t-sql/functions/aggregate-functions-transact-sql.md)   
 [CASE &#40;Transact-SQL&#41;](../../t-sql/language-elements/case-transact-sql.md)   
 [CONTAINSTABLE &#40;Transact-SQL&#41;](../../relational-databases/system-functions/containstable-transact-sql.md)   
 [カーソル &#40;Transact-SQL&#41;](../../t-sql/language-elements/cursors-transact-sql.md)   
 [DELETE &#40;Transact-SQL&#41;](../../t-sql/statements/delete-transact-sql.md)   
 [式 &#40;Transact-SQL&#41;](../../t-sql/language-elements/expressions-transact-sql.md)   
 [FREETEXTTABLE &#40;Transact-SQL&#41;](../../relational-databases/system-functions/freetexttable-transact-sql.md)   
 [FROM &#40;Transact-SQL&#41;](../../t-sql/queries/from-transact-sql.md)   
 [演算子 &#40;Transact-SQL&#41;](../../t-sql/language-elements/operators-transact-sql.md)   
 [UPDATE &#40;Transact-SQL&#41;](../../t-sql/queries/update-transact-sql.md)  
  
  

