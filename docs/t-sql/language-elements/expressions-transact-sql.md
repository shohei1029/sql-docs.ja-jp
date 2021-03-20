---
description: 式 (Transact-SQL)
title: 式 (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
dev_langs:
- TSQL
helpviewer_keywords:
- Boolean expressions
- expressions [SQL Server], about expressions
- combining expressions
- Transact-SQL expressions
- expressions [SQL Server], combining
- simple expressions [SQL Server]
- complex expressions [SQL Server]
ms.assetid: ee53c5c8-e36c-40f9-8cd1-d933791b98fa
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 49c83c49a8175afbc089e2bd8321cab01c0ecfc7
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104749922"
---
# <a name="expressions-transact-sql"></a>式 (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]によって 1 つのデータ値を取得するために評価される、記号と演算子の組み合わせです。 単純式には、1 つの定数、変数、列、またはスカラー関数を指定できます。 演算子を使用すると、2 つ以上の単純式を結合して、複合式を作成できます。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql
-- Syntax for SQL Server and Azure SQL Database  
  
{ constant | scalar_function | [ table_name. ] column | variable   
    | ( expression ) | ( scalar_subquery )   
    | { unary_operator } expression   
    | expression { binary_operator } expression   
    | ranking_windowed_function | aggregate_windowed_function  
}  
```  
  
```syntaxsql
-- Syntax for Azure Synapse Analytics and Parallel Data Warehouse  

-- Expression in a SELECT statement  
<expression> ::=   
{  
    constant   
    | scalar_function   
    | column  
    | variable  
    | ( expression  )  
    | { unary_operator } expression   
    | expression { binary_operator } expression   
}  
[ COLLATE Windows_collation_name ]  
  
-- Scalar Expression in a DECLARE, SET, IF...ELSE, or WHILE statement  
<scalar_expression> ::=  
{  
    constant   
    | scalar_function   
    | variable  
    | ( expression  )  
    | (scalar_subquery )  
    | { unary_operator } expression   
    | expression { binary_operator } expression   
}  
[ COLLATE { Windows_collation_name ]  
  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
  
|期間|定義|  
|----------|----------------|  
|*constant*|1 つの特定のデータ値を表す記号です。 詳細については、「[定数 &#40;Transact-SQL&#41;](../../t-sql/data-types/constants-transact-sql.md)」を参照してください。|  
|*scalar_function*|特定のサービスを提供し、単一の値を返す [!INCLUDE[tsql](../../includes/tsql-md.md)] 構文の単位です。 *scalar_function* には、組み込みの SUM 関数、GETDATE 関数、または CAST 関数、スカラー ユーザー定義関数などのスカラー関数を指定できます。|  
|[ _table_name_ **.** ]|テーブルの名前または別名です。|  
|*column*|列の名前です。 式では列の名前だけが許可されます。|  
|*variable*|変数名、またはパラメーターです。 詳細については、「[DECLARE @local_variable &#40;Transact-SQL&#41;](../../t-sql/language-elements/declare-local-variable-transact-sql.md)」を参照してください。|  
| **(** _expression_  **)**|このトピックで定義されている有効な式を指定します。 かっこはグループ化の演算子です。かっこ内の式のすべての演算子は最初に評価され、その後で結果の式が別の式と結合されます。|  
|**(** _scalar_subquery_ **)**|1 つの値を返すサブクエリを指定します。 次に例を示します。<br /><br /> `SELECT MAX(UnitPrice)`<br /><br /> `FROM Products`|  
|{ *unary_operator* }|単項演算子を適用できるのは、数値型に属するいずれかのデータ型に評価される式だけです。 1 つの数値オペランドだけを含む演算子を指定します。<br /><br /> + は正の値を示します。<br /><br /> - は負の値を示します。<br /><br /> ~ は 1 の補数演算子を示します。|  
|{ *binary_operator* }|2 つの式を結合して 1 つの結果を生成する方法を定義する演算子を指定します。 *binary_operator* には、算術演算子、代入演算子 (=)、ビットごとの演算子、比較演算子、論理演算子、文字列の連結演算子 (+)、または単項演算子を指定できます。 演算子の詳細については、「[演算子 &#40;Transact-SQL&#41;](../../t-sql/language-elements/operators-transact-sql.md)」を参照してください。|  
|*ranking_windowed_function*|任意の [!INCLUDE[tsql](../../includes/tsql-md.md)] 順位付け関数です。 詳細については、「[順位付け関数 &#40;Transact-SQL&#41;](../../t-sql/functions/ranking-functions-transact-sql.md)」を参照してください。|  
|*aggregate_windowed_function*|OVER 句を含む任意の [!INCLUDE[tsql](../../includes/tsql-md.md)] 集計関数です。 詳細については、を参照してください。 [OVER 句 &#40;Transact-SQL&#41;](../../t-sql/queries/select-over-clause-transact-sql.md).|  
  
## <a name="expression-results"></a>式の結果  
 1 つの定数、変数、スカラー関数、または列名で構成される単純式の場合、式のデータ型、照合順序、有効桁数、小数点以下桁数、および値は、参照される要素のデータ型、照合順序、有効桁数、小数点以下桁数、および値になります。  
  
 2 つの式が比較演算子または論理演算子で結合される場合、取得される結果のデータ型は Boolean で、TRUE、FALSE、UNKNOWN のいずれかの値をとります。 ブール データ型の詳細については、「[比較演算子 &#40;Transact-SQL&#41;](../../t-sql/language-elements/comparison-operators-transact-sql.md)」を参照してください。  
  
 2 つの式が、算術演算子、ビットごとの演算子、または文字列演算子で結合される場合、取得される結果のデータ型は演算子によって決まります。  
  
 複数の記号と演算子で構成される複合式は、単一の値をとる結果に評価されます。 結果の式のデータ型、照合順序、有効桁数、および値は、構成要素の式を一度に 2 つずつ結合して取得される最終結果によって決まります。 式の結合順序は、式の中の演算子の優先順位で定義されます。  
  
## <a name="remarks"></a>注釈  
 2 つの式を演算子で結合できるのは、その演算子で両方のデータ型がサポートされており、次に示す条件の少なくとも 1 つが TRUE の場合です。  
  
-   式のデータ型が等しい。  
  
-   優先順位の低いデータ型を、優先順位の高いデータ型に暗黙的に変換できる。  
  
 式がこれらの条件を満たしていない場合は、CAST 関数または CONVERT 関数によって、低い優先順位のデータ型から高い優先順位のデータ型へ明示的に変換できます。または、高い優先順位のデータ型への暗黙的な変換が可能な中間のデータ型へ変換できます。  
  
 暗黙的または明示的な変換がサポートされない場合、2 つの式を結合することはできません。  
  
 文字列として評価される式の照合順序は、照合順序の優先順位の規則に従って設定されます。 詳細については、「[照合順序の優先順位 &#40;Transact-SQL&#41;](../../t-sql/statements/collation-precedence-transact-sql.md)」を参照してください。  
  
 C や [!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[vbprvb](../../includes/vbprvb-md.md)] などのプログラミング言語の場合、式は常に単一の結果に評価されます。 [!INCLUDE[tsql](../../includes/tsql-md.md)] 選択リスト内の式の場合は、この規則の限りではありません。式は、結果セット内の各行に対して個別に評価されます。 1 つの式が結果セット内の各行でそれぞれ異なる値をとることもあります。ただし、各行の値は式に対して 1 つだけです。 たとえば、次の `SELECT` ステートメントにおいて、選択リスト内の `ProductID` への参照と `1+2` の項は両方とも式です。  
  
```sql  
USE AdventureWorks2012;  
GO  
SELECT ProductID, 1+2  
FROM Production.Product;  
GO  
```  
  
 式 `1+2` は、結果セット内の各行で `3` と評価されます。 式 `ProductID` は、結果セットの各行で一意な値をとりますが、各行に格納される `ProductID` の値は 1 つだけです。  
 
- [!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] によって各スレッドに固定された最大メモリ量が割り当てられるため、スレッドによってすべてのメモリが使い尽くされることはありません。  このメモリの一部は、クエリの式を格納するために使用されます。  クエリに含まれる式が多すぎて、必要なメモリが内部制限を超えた場合、そのクエリはエンジンによって実行されません。  この問題を回避するために、ユーザーはクエリを複数のクエリに変更して、それぞれのクエリに含まれる式の数を減らすことができます。 たとえば、次のように WHERE 句に式の長いリストを含むクエリがあるとします。 

```sql
DELETE FROM dbo.MyTable 
WHERE
(c1 = '0000001' AND c2 = 'A000001') or
(c1 = '0000002' AND c2 = 'A000002') or
(c1 = '0000003' AND c2 = 'A000003') or
...

```
このクエリは次のように変更します。

```sql
DELETE FROM dbo.MyTable WHERE (c1 = '0000001' AND c2 = 'A000001');
DELETE FROM dbo.MyTable WHERE (c1 = '0000002' AND c2 = 'A000002');
DELETE FROM dbo.MyTable WHERE (c1 = '0000003' AND c2 = 'A000003');
...
```

## <a name="see-also"></a>関連項目  
 [AT TIME ZONE &#40;Transact-SQL&#41;](../../t-sql/queries/at-time-zone-transact-sql.md)   
 [CASE &#40;Transact-SQL&#41;](../../t-sql/language-elements/case-transact-sql.md)   
 [CAST および CONVERT &#40;Transact-SQL&#41;](../../t-sql/functions/cast-and-convert-transact-sql.md)   
 [COALESCE &#40;Transact-SQL&#41;](../../t-sql/language-elements/coalesce-transact-sql.md)   
 [データ型の変換 &#40;データベース エンジン&#41;](../../t-sql/data-types/data-type-conversion-database-engine.md)   
 [データ型の優先順位 &#40;Transact-SQL&#41;](../../t-sql/data-types/data-type-precedence-transact-sql.md)   
 [データ型 &#40;Transact-SQL&#41;](../../t-sql/data-types/data-types-transact-sql.md)   
 [組み込み関数 &#40;Transact-SQL&#41;](~/t-sql/functions/functions.md)   
 [LIKE &#40;Transact-SQL&#41;](../../t-sql/language-elements/like-transact-sql.md)   
 [NULLIF &#40;Transact-SQL&#41;](../../t-sql/language-elements/nullif-transact-sql.md)   
 [SELECT &#40;Transact-SQL&#41;](../../t-sql/queries/select-transact-sql.md)   
 [WHERE &#40;Transact-SQL&#41;](../../t-sql/queries/where-transact-sql.md)  
  
  
