---
description: 照合順序の優先順位
title: 照合順序の優先順位 | Microsoft Docs
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
- coercible-default collation label
- precedence [SQL Server], collations
- collation sensitive
- collations [SQL Server], precedence
- explicit collation label [SQL Server]
- implicit collation label
- no-collation label
- collation insensitive
- operators [Transact-SQL], collations
- collation labels
- collation coercion rules
- rules [SQL Server], collations
ms.assetid: 58c4e64b-5634-4c29-aa22-33193282dd27
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 79b580e8d4c34ef27e02a00f073c0d79f65e87f2
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104736532"
---
# <a name="collation-precedence"></a>照合順序の優先順位
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  照合順序の優先順位は、照合順序強制適用ルールとも呼ばれ、次の照合順序を決定します。  
  
-   文字列として評価される式の最終的な結果の照合順序。  
  
-   LIKE、IN など、入力として文字列を使用するが文字列を返さない、照合順序依存の演算子によって使用される照合順序  
  
照合順序の優先順位のルールは、**char**、**varchar**、**text**、**nchar**、**nvarchar**、**ntext** の各文字列データ型に対してのみ適用されます。 他のデータ型のオブジェクトは、照合順序の評価の対象にはなりません。  
  
## <a name="collation-labels"></a>照合順序ラベル  
すべてのオブジェクトの照合順序は 4 つのカテゴリに分類されます。これらのカテゴリの一覧とその説明を以下の表に示します。 各カテゴリの名前を、照合順序ラベルと呼びます。  
  
|照合順序ラベル|オブジェクトの種類|  
|---------------------|----------------------|  
|強制可能な既定照合順序|任意の [!INCLUDE[tsql](../../includes/tsql-md.md)] 文字列変数、パラメーター、リテラル、カタログの組み込み関数の出力、または入力に文字列を使用しないが文字列を出力する組み込み関数の出力です。<br /><br /> このオブジェクトが、ユーザー定義関数、ストアド プロシージャ、またはトリガー内で宣言されている場合、そのオブジェクトには、関数、ストアド プロシージャ、またはトリガーが作成されているデータベースの既定の照合順序が割り当てられます。 オブジェクトがバッチ内で宣言されている場合は、その接続に対する現在のデータベースの既定の照合順序が割り当てられます。|  
|暗黙的な照合順序 X|列参照です。 式の照合順序 (X) には、テーブルまたはビューの列に対して定義されている照合順序が使用されます。<br /><br /> CREATE TABLE または CREATE VIEW ステートメントの COLLATE 句を使用して列に明示的に照合順序を割り当てても、その列参照は "暗黙" として分類されます。|  
|明示的な照合順序 X|式の中で、COLLATE 句を使用して特定の照合順序 (X) に明示的にキャストされる式です。|  
|照合順序なし|式の値が、2 つの文字列の照合順序ラベルが "暗黙" で、それぞれの照合順序が競合する場合のその文字列間の操作の結果であることを示します。 式の結果は、照合順序なしとして定義されます。|  
  
## <a name="collation-rules"></a>照合順序ルール  
文字列オブジェクトを 1 つだけ参照する単純な式の場合、その式の照合順序ラベルには、参照先のオブジェクトの照合順序ラベルが使用されます。  
  
同じ照合順序ラベルを持つ 2 つのオペランド式を参照する複雑な式の場合、その式の照合順序ラベルには、オペランド式の照合順序ラベルが使用されます。  
  
異なる照合順序を持つ 2 つのオペランド式を参照する複雑な式の場合、最終的な結果の照合順序ラベルは、次のルールに基づいて決定されます。  
  
-   明示的な照合順序は、暗黙的な照合順序よりも優先されます。 暗黙的な照合順序は、強制可能な既定照合順序よりも優先されます。  
  
     明示的な照合順序 > 暗黙的な照合順序 > 強制可能な既定照合順序  
  
-   異なる照合順序が割り当てられている 2 つの明示的な式を組み合わせると、エラーが発生します。  
  
     明示的な照合順序 X + 明示的な照合順序 Y = エラー  
  
-   異なる照合順序を持つ 2 つの暗黙的な式を組み合わせると、照合順序なしになります。  
  
     暗黙的な照合順序 X + 暗黙的な照合順序 Y = 照合順序なし  
  
-   照合順序なしの式と任意のラベルの式を組み合わせると、照合順序なしのラベルが生成されます。ただし、明示的な照合順序の場合は例外です。それについては次のルールを参照してください。  
  
     照合順序なし + 任意 = 照合順序なし  
  
-   照合順序なしの式を、明示的な照合順序を持つ式と組み合わせると、明示的な照合順序のラベルを持つ式になります。  
  
     照合順序なし + 明示的な照合順序 X = 明示的な照合順序  
  
次の表は、ルールのまとめです。  
  
|オペランド強制ラベル|明示的な照合順序 X|暗黙的な照合順序 X|強制可能な既定照合順序|照合順序なし|  
|----------------------------|----------------|----------------|------------------------|-------------------|  
|**明示的な照合順序 Y**|エラーが生成されます。|結果は、明示的な照合順序 Y です。|結果は、明示的な照合順序 Y です。|結果は、明示的な照合順序 Y です。|  
|**暗黙的な照合順序 Y**|結果は明示的な照合順序 X です。|結果は、照合順序なしです。|結果は、暗黙的な照合順序 Y です。|結果は、照合順序なしです。|  
|**強制可能な既定照合順序**|結果は明示的な照合順序 X です。|結果は暗黙的な照合順序 X です。|結果は、強制可能な既定照合順序です。|結果は、照合順序なしです。|  
|**照合順序なし**|結果は明示的な照合順序 X です。|結果は、照合順序なしです。|結果は、照合順序なしです。|結果は、照合順序なしです。|  
  
また、次の追加のルールも照合順序の優先順位に適用されます。  
  
-   式で明示的に照合順序が指定されている場合、その式にさらに COLLATE 句を指定することはできません。 たとえば、次の `WHERE` 句は、既に明示的に照合順序が指定されている式に対して `COLLATE` 句が指定されているため、不正な式となります。  
  
     `WHERE ColumnA = ( 'abc' COLLATE French_CI_AS) COLLATE French_CS_AS`  
  
-   **text** 型で使用されるコード ページは変換できません。 **text** 式の照合順序をキャストする場合、キャスト前とキャスト後の照合順序のコード ページが異なると、キャストできません。 代入演算子を使用する場合、右側のテキスト オペランドの照合順序のコードページが左側のテキスト オペランドのコード ページと異なると、代入演算子は値を割り当てることができません。  
  
照合順序の優先順位の決定は、データ型変換の後に行われます。 結果の照合順序を提供するオペランドは、最終結果のデータ型を提供するオペランドと異なっていてもかまいません。 たとえば、次のバッチがあるとします。  
  
```sql  
CREATE TABLE TestTab  
   (PrimaryKey int PRIMARY KEY,  
    CharCol char(10) COLLATE French_CI_AS  
   )  
  
SELECT *  
FROM TestTab  
WHERE CharCol LIKE N'abc'  
```  
  
`N'abc'` という単純な式の Unicode データ型は、データ型の優先順位が高いため、 その結果式には `N'abc'` に割り当てられている Unicode データ型が使用されます。 これに対し、式 `CharCol` は暗黙的な照合順序のラベルを持ち、`N'abc'` はそれよりも優先順位の低い強制可能な既定照合順序のラベルを持つため、 照合順序には、`CharCol` の照合順序 `French_CI_AS` が使用されます。  
  
### <a name="examples-of-collation-rules"></a>照合順序ルールの例  
 照合順序ルールについて、以下の例で説明します。 この例を実行するには、次に示すテスト テーブルを作成します。  
  
```sql  
USE tempdb;  
GO  
  
CREATE TABLE TestTab (  
   id int,   
   GreekCol nvarchar(10) collate greek_ci_as,   
   LatinCol nvarchar(10) collate latin1_general_cs_as  
   )  
INSERT TestTab VALUES (1, N'A', N'a');  
GO  
```  
  
#### <a name="collation-conflict-and-error"></a>照合順序の競合とエラー  
 次のクエリの述語に照合順序の競合が発生し、エラーが生成されます。  
  
```sql  
SELECT *   
FROM TestTab   
WHERE GreekCol = LatinCol;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
Msg 448, Level 16, State 9, Line 2  
Cannot resolve collation conflict between 'Latin1_General_CS_AS' and 'Greek_CI_AS' in equal to operation.  
```  
  
#### <a name="explicit-label-vs-implicit-label"></a>明示ラベルと暗黙ラベル  
 次のクエリの述語は、右側の式が明示ラベルを持っているので、照合順序 `greek_ci_as` で評価されます。 右側の式の明示ラベルは、左側の式の暗黙ラベルよりも優先されます。  
  
```sql  
SELECT *   
FROM TestTab   
WHERE GreekCol = LatinCol COLLATE greek_ci_as;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
id          GreekCol             LatinCol  
----------- -------------------- --------------------  
          1 A                    a  
  
(1 row affected)  
```  
  
#### <a name="no-collation-labels"></a>照合順序なしのラベル  
次のクエリの `CASE` 式の照合順序ラベルは、照合順序なしです。したがって、この式を選択リストに指定したり、照合順序依存の演算子によって処理することはできません。 ただし、この式を、照合順序非依存の演算子を使用して処理することはできます。  
  
```sql  
SELECT (CASE WHEN id > 10 THEN GreekCol ELSE LatinCol END)   
FROM TestTab;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
Msg 451, Level 16, State 1, Line 1  
Cannot resolve collation conflict for column 1 in SELECT statement.  
```  
  
```sql  
SELECT PATINDEX((CASE WHEN id > 10 THEN GreekCol ELSE LatinCol END), 'a')  
FROM TestTab;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
Msg 446, Level 16, State 9, Server LEIH2, Line 1  
Cannot resolve collation conflict for patindex operation.  
```  
  
```sql  
SELECT (CASE WHEN id > 10 THEN GreekCol ELSE LatinCol END) COLLATE Latin1_General_CI_AS   
FROM TestTab;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
--------------------  
a  
  
(1 row affected)  
```  
  
## <a name="collation-sensitive-and-collation-insensitive"></a>照合順序依存と照合順序非依存  
演算子と関数は、照合順序依存または非依存のいずれかです。  
  
照合順序依存  
照合順序依存とは、照合順序なしのオペランドを指定するとコンパイル時にエラーとなることを意味します。 その式の結果を、照合順序なしに指定することはできません。  
  
照合順序非依存  
照合順序非依存とは、そのオペランドと結果を照合順序なしに指定できることを意味します。  
  
### <a name="operators-and-collation"></a>演算子と照合順序  
比較演算子、および MAX、MIN、BETWEEN、LIKE、IN の各演算子は、照合順序依存です。 これらの演算子で使用される文字列には、優先順位の高い方のオペランドの照合順序ラベルが割り当てられます。 また、UNION ステートメントも照合順序依存で、すべての文字列オペランドおよび最終結果には、最高の優先順位を持つオペランドの照合順序が割り当てられます。 UNION オペランドと結果の照合順序の優先順位は、列ごとに評価されます。  
  
代入演算子は照合順序非依存で、右側の式が左側の照合順序にキャストされます。  
  
文字列連結演算子は照合順序依存で、2 つの文字列オペランドとその結果には、最高の優先順位を持つオペランドの照合順序ラベルが割り当てられます。 UNION ALL および CASE ステートメントは照合順序非依存で、すべての文字列オペランドとその最終結果には、最高の優先順位を持つオペランドの照合順序ラベルが割り当てられます。 UNION ALL オペランドと結果の照合順序の優先順位は、列ごとに評価されます。  
  
### <a name="functions-and-collation"></a>関数と照合順序  
CAST、CONVERT、および COLLATE の各関数は、**char**、**varchar**、**text** の各データ型に関して照合順序依存です。 CAST 関数および CONVERT 関数の入出力が文字列である場合、出力文字列は、入力文字列の照合順序ラベルを持ちます。 入力が文字列ではない場合、出力文字列は強制可能な既定照合順序となり、その接続の現在のデータベースの照合順序、あるいは CAST または CONVERT を参照しているユーザー定義関数、ストアド プロシージャ、またはトリガーを含むデータベースの照合順序が割り当てられます。  
  
 入力が文字列ではなく文字列を返す組み込み関数の場合、その結果文字列は強制可能な既定照合順序となり、現在のデータベースの照合順序、あるいはその関数を参照しているユーザー定義関数、ストアド プロシージャ、またはトリガーを含むデータベースの照合順序が割り当てられます。  
  
 次の関数は、照合順序依存です。その出力文字列は、入力文字列の照合順序ラベルを持ちます。  

:::row:::
    :::column:::
        CHARINDEX
    :::column-end:::
    :::column:::
        REPLACE
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        DIFFERENCE
    :::column-end:::
    :::column:::
        REVERSE
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        ISNUMERIC
    :::column-end:::
    :::column:::
        RIGHT
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        LEFT
    :::column-end:::
    :::column:::
        SOUNDEX
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        LEN
    :::column-end:::
    :::column:::
        STUFF
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        LOWER
    :::column-end:::
    :::column:::
        SUBSTRING
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        PATINDEX
    :::column-end:::
    :::column:::
        UPPER
    :::column-end:::
:::row-end:::
  
## <a name="see-also"></a>参照  
 [COLLATE &#40;Transact-SQL&#41;](~/t-sql/statements/collations.md)   
 [データ型の変換 &#40;データベース エンジン&#41;](../../t-sql/data-types/data-type-conversion-database-engine.md)   
 [演算子 &#40;Transact-SQL&#41;](../../t-sql/language-elements/operators-transact-sql.md)   
 [式 &#40;Transact-SQL&#41;](../../t-sql/language-elements/expressions-transact-sql.md)  
  
  
