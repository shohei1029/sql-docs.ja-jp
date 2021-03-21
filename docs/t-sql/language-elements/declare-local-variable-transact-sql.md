---
title: DECLARE @local_variable (Transact-SQL) | Microsoft Docs
description: DECLARE を使用し、バッチまたはプロシージャで使用するローカル変数を定義するための Transact-SQL リファレンス。
ms.date: 07/24/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- DECLARE
- DECLARE_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- table-valued parameters
- variables [SQL Server], declaring
- DECLARE statement
- declaring variables
ms.assetid: d1635ebb-f751-4de1-8bbc-cae161f90821
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 73074e29b16ef4262e9d61efbaf941ca1c4f9fc0
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104753982"
---
# <a name="declare-local_variable-transact-sql"></a>DECLARE @local_variable (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  変数は、バッチやプロシージャの中で DECLARE ステートメントを使用して宣言し、SET ステートメントまたは SELECT ステートメントを使用して値を割り当てます。 このステートメントでは、他のカーソル関連のステートメントで使用できるカーソル変数を宣言できます。 宣言の一部として値を指定しない場合、宣言の後、すべての変数は NULL として初期化されます。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql  
-- Syntax for SQL Server and Azure SQL Database  
  
DECLARE   
{   
    { @local_variable [AS] data_type  [ = value ] }  
  | { @cursor_variable_name CURSOR }  
} [,...n]   
| { @table_variable_name [AS] <table_type_definition> }   
  
<table_type_definition> ::=   
     TABLE ( { <column_definition> | <table_constraint> } [ ,...n] )   
  
<column_definition> ::=   
     column_name { scalar_data_type | AS computed_column_expression }  
     [ COLLATE collation_name ]   
     [ [ DEFAULT constant_expression ] | IDENTITY [ (seed ,increment ) ] ]   
     [ ROWGUIDCOL ]   
     [ <column_constraint> ]   
  
<column_constraint> ::=   
     { [ NULL | NOT NULL ]   
     | [ PRIMARY KEY | UNIQUE ]   
     | CHECK ( logical_expression )   
     | WITH ( <index_option > )  
     }   
  
<table_constraint> ::=   
     { { PRIMARY KEY | UNIQUE } ( column_name [ ,...n] )   
     | CHECK ( search_condition )   
     }   
  
<index_option> ::=  
See CREATE TABLE for index option syntax.  
  
```  
  
```syntaxsql
-- Syntax for Azure Synapse Analytics and Parallel Data Warehouse  
  
DECLARE   
{{ @local_variable [AS] data_type } [ =value [ COLLATE <collation_name> ] ] } [,...n]  
  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
@*local_variable*  
 変数名を指定します。 変数名は、アット マーク (@) で始める必要があります。 ローカル変数名は、[識別子](../../relational-databases/databases/database-identifiers.md)の規則に従っている必要があります。  
  
*data_type*  
 システム提供の共通言語ランタイム (CLR) ユーザー定義テーブル型または別名データ型を指定します。 変数のデータ型としては **text**、**ntext**、**image** を指定できません。  
  
 システム データ型の詳細については、「[データ型 &#40;Transact-SQL&#41;](../../t-sql/data-types/data-types-transact-sql.md)」を参照してください。 CLR ユーザー定義型または別名データ型の詳細については、「[CREATE TYPE &#40;Transact-SQL&#41;](../../t-sql/statements/create-type-transact-sql.md)」を参照してください。  
  
 =*value*  
 インラインで値を変数に代入します。 値には定数または式を指定できますが、変数宣言の型と同じであるか、その型に暗黙的に変換できる必要があります。 詳細については、「[式 &#40;Transact-SQL&#41;](../../t-sql/language-elements/expressions-transact-sql.md)」を参照してください。  
  
@*cursor_variable_name*  
 カーソル変数の名前を指定します。 カーソル変数名はアット マーク (@) で始まり、識別子の規則に従っている必要があります。  
  
CURSOR  
 変数がローカルのカーソル変数であることを指定します。  
  
@*table_variable_name*  
 **table** 型の変数の名前です。 変数名はアット マーク (@) で始まり、識別子の規則に従っている必要があります。  
  
<table_type_definition>  
**table** データ型を定義します。 テーブルの定義には、列の定義、名前、データ型、制約が含まれます。 指定できる制約の種類は、PRIMARY KEY、UNIQUE、NULL、CHECK のみです。 別名データ型にルールや既定の定義がバインドされている場合、別名データ型を列のスカラー データ型として使用することはできません。
  
\<table_type_definiton> CREATE TABLE でテーブルを定義するために使用する情報のサブセットです。 これには要素と必須の定義が含まれます。 詳細については、「[CREATE TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/create-table-transact-sql.md)」を参照してください。  
  
 *n*  
 複数の変数を指定し、値を割り当てることができることを示すプレースホルダーです。 **table** 変数を宣言する場合、1 つの DECLARE ステートメント内に **table** 変数以外の変数を定義することはできません。  
  
 *column_name*  
 テーブル内の列名を指定します。  
  
 *scalar_data_type*  
 列がスカラー データ型であることを指定します。  
  
 *computed_column_expression*  
 計算列の値を定義する式を指定します。 計算列は、同じテーブルの他の列を使用した式によって計算されます。 たとえば、計算列は **cost** AS **price \* qty** として定義されます。この式には、計算列以外の列の名前、定数、組み込み関数、変数、およびこれらを 1 つ以上の演算子で結合した組み合わせを使用できます。 この式はサブクエリまたはユーザー定義関数にはできません。 この式は CLR ユーザー定義型を参照できません。  
  
 [ COLLATE *collation_name*]  
 列の照合順序を指定します。 *collation_name* には、Windows の照合順序名か SQL の照合順序名を指定できます。これは、データ型が **char**、**varchar**、**text**、**nchar**、**nvarchar**、**ntext** の列にだけ適用できます。 指定しないと、列がユーザー定義データ型である場合はユーザー定義データ型の照合順序、または現在のデータベースの照合順序が割り当てられます。  
  
 Windows と SQL の照合順序名については、「[COLLATE &#40;Transact-SQL&#41;](~/t-sql/statements/collations.md)」を参照してください。  
  
 DEFAULT  
 挿入の際に明示的な値を指定しない場合に、列に入力される値を指定します。 DEFAULT 定義は、**timestamp** として定義された列または IDENTITY プロパティを持つ列以外のすべての列に適用できます。 テーブルが削除されると、DEFAULT 定義は削除されます。 既定値として使用できるのは、文字列などの定数値、SYSTEM_USER() などのシステム関数、または NULL だけです。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の旧バージョンとの互換性を保つため、DEFAULT に制約名を割り当てることができます。  
  
 *constant_expression*  
 列の既定値として使用される定数、NULL またはシステム関数を指定します。  
  
 IDENTITY  
 新しい列が ID 列であることを指定します。 テーブルに行が新しく追加されると、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では列に一意な増分値が設定されます。 ID 列は通常、PRIMARY KEY 制約と組み合わせて使用し、テーブルの一意な行識別子 (ROWID) の役割を果たします。 IDENTITY プロパティは、**tinyint**、**smallint**、**int**、**decimal(p,0)** 、**numeric(p,0)** のいずれかの列に割り当てることができます。 ID 列は 1 つのテーブルにつき 1 つだけ作成できます。 バインドされた既定値および DEFAULT 制約を ID 列と共に使用することはできません。 seed と increment は、両方を指定するか、どちらも指定しないでください。 どちらも指定しないときの既定値は (1,1) です。  
  
 *seed*  
 テーブルに読み込まれる最初の行に使用される値です。  
  
 *increment*  
 既に読み込まれている前の行の ID 値に加算される増分値です。  
  
 ROWGUIDCOL  
 新しい列が行グローバル一意識別子列であることを指定します。 1 つのテーブルにつき、1 つの **uniqueidentifier** 列だけを ROWGUIDCOL 列に指定できます。 ROWGUIDCOL プロパティは **uniqueidentifier** 列にだけ割り当てることができます。  
  
 NULL | NOT NULL  
 変数で NULL 値が許可されるかどうかを示します。 既定値は NULL です。  
  
 PRIMARY KEY  
 特定の 1 つ以上の一意なインデックスによって列にエンティティの整合性を強制する制約です。 PRIMARY KEY 制約は、1 つのテーブルにつき 1 つだけ作成できます。  
  
 UNIQUE  
 一意なインデックスによって、特定の 1 つ以上の列に対してエンティティの整合性を設定する制約です。 1 つのテーブルには複数の UNIQUE 制約を指定できます。  
  
 CHECK  
 1 つ以上の列に入力できる値を制限することによってドメインの整合性を設定する制約です。  
  
 *logical_expression*  
 TRUE または FALSE を返す論理式です。  
  
## <a name="remarks"></a>解説  
 変数は、バッチやプロシージャの中で、WHILE、LOOP、または IF...ELSE ブロックのカウンターとして使用される場合があります。  
  
 変数は式の内部だけで使用できます。オブジェクト名やキーワードの代わりに使用することはできません。 動的 SQL ステートメントを作成するには、EXECUTE を使用します。  
  
 ローカル変数のスコープは、その変数が宣言されるバッチです。  
 
 テーブル変数は、必ずしもメモリ常駐ではありません。 メモリ不足の場合、テーブル変数に属するページは tempdb に移動します。
  
 現在カーソルが割り当てられているカーソル変数は、次のステートメントの中でソースとして参照できます。  
  
-   CLOSE ステートメント。  
  
-   DEALLOCATE ステートメント  
  
-   FETCH ステートメント  
  
-   OPEN ステートメント  
  
-   位置指定の DELETE または UPDATE ステートメント。  
  
-   SET CURSOR 変数ステートメント (右側)。  
  
 どのステートメントの場合も、参照されるカーソル変数は存在するが、現在カーソルが割り当てられていない場合は、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] でエラーが発生します。 参照されているカーソル変数が存在しない場合、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、宣言されていない別の型の変数に対して発生するエラーと同じエラーが発生します。  
  
 カーソル変数には、次の特徴があります。  
  
-   カーソルの種類または別のカーソル変数の対象になります。 詳細については、「[SET @local_variable &#40;Transact-SQL&#41;](../../t-sql/language-elements/set-local-variable-transact-sql.md)」を参照してください。  
  
-   カーソル変数に現在カーソルが割り当てられていない場合、EXECUTE ステートメント内の出力カーソル パラメーターの対象として参照できます。  
  
-   カーソルへのポインターとして扱われます。  
  
## <a name="examples"></a>例  
  
### <a name="a-using-declare"></a>A. DECLARE を使用する  
 次の例では、`@find` という名前のローカル変数を使って、`Man` で始まるすべての姓の連絡先情報を取得します。  
  
```sql  
USE AdventureWorks2012;  
GO  
DECLARE @find VARCHAR(30);   
/* Also allowed:   
DECLARE @find VARCHAR(30) = 'Man%';   
*/  
SET @find = 'Man%';   
SELECT p.LastName, p.FirstName, ph.PhoneNumber  
FROM Person.Person AS p   
JOIN Person.PersonPhone AS ph ON p.BusinessEntityID = ph.BusinessEntityID  
WHERE LastName LIKE @find;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 ```
LastName            FirstName               Phone
------------------- ----------------------- -------------------------
Manchepalli         Ajay                    1 (11) 500 555-0174
Manek               Parul                   1 (11) 500 555-0146
Manzanares          Tomas                   1 (11) 500 555-0178
  
(3 row(s) affected)
```  
  
### <a name="b-using-declare-with-two-variables"></a>B. DECLARE で 2 つの変数を使用する  
 次の例では、北米販売区域に勤務しており、年間売上高が $2,000,000 以上である [!INCLUDE[ssSampleDBCoFull](../../includes/sssampledbcofull-md.md)] 販売担当者の名前を取得します。  
  
```sql  
USE AdventureWorks2012;  
GO  
SET NOCOUNT ON;  
GO  
DECLARE @Group nvarchar(50), @Sales MONEY;  
SET @Group = N'North America';  
SET @Sales = 2000000;  
SET NOCOUNT OFF;  
SELECT FirstName, LastName, SalesYTD  
FROM Sales.vSalesPerson  
WHERE TerritoryGroup = @Group and SalesYTD >= @Sales;  
```  
  
### <a name="c-declaring-a-variable-of-type-table"></a>C. table 型の変数を宣言する  
 次の例では、UPDATE ステートメントの OUTPUT 句で指定される値を格納する `table` 変数を作成します。 この後に、`SELECT` 内の値、および `@MyTableVar` テーブルの更新操作の結果を返す 2 つの `Employee` ステートメントが続きます。 `INSERTED.ModifiedDate` 列の結果が、`Employee` テーブルの `ModifiedDate` 列の値と異なることに注意してください。 これは、`AFTER UPDATE` の値を現在の日付に更新する `ModifiedDate` トリガーが、`Employee` テーブルで定義されるためです。 ただし、`OUTPUT` が返す列には、トリガーが起動される前の値が反映されています。 詳細については、「[OUTPUT 句 &#40;Transact-SQL&#41;](../../t-sql/queries/output-clause-transact-sql.md)」を参照してください。  
  
```sql  
USE AdventureWorks2012;  
GO  
DECLARE @MyTableVar TABLE(  
    EmpID INT NOT NULL,  
    OldVacationHours INT,  
    NewVacationHours INT,  
    ModifiedDate DATETIME);  
UPDATE TOP (10) HumanResources.Employee  
SET VacationHours = VacationHours * 1.25   
OUTPUT INSERTED.BusinessEntityID,  
       DELETED.VacationHours,  
       INSERTED.VacationHours,  
       INSERTED.ModifiedDate  
INTO @MyTableVar;  
--Display the result set of the table variable.  
SELECT EmpID, OldVacationHours, NewVacationHours, ModifiedDate  
FROM @MyTableVar;  
GO  
--Display the result set of the table.  
--Note that ModifiedDate reflects the value generated by an  
--AFTER UPDATE trigger.  
SELECT TOP (10) BusinessEntityID, VacationHours, ModifiedDate  
FROM HumanResources.Employee;  
GO  
```  
  
### <a name="d-declaring-a-variable-of-user-defined-table-type"></a>D. ユーザー定義テーブル型の変数を宣言する  
 次の例では、`@LocationTVP` というテーブル値パラメーターまたはテーブル変数を作成します。 これには、`LocationTableType` という対応するユーザー定義テーブル型が必要です。 ユーザー定義テーブル型の作成方法の詳細については、「[CREATE TYPE &#40;Transact-SQL&#41;](../../t-sql/statements/create-type-transact-sql.md)」を参照してください。 テーブル値パラメーターの詳細については、「[テーブル値パラメーターの使用 &#40;データベース エンジン&#41;](../../relational-databases/tables/use-table-valued-parameters-database-engine.md)」を参照してください。  
  
```sql  
DECLARE @LocationTVP   
AS LocationTableType;  
```  
  
## <a name="examples-sssdwfull-and-sspdw"></a>例: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)]、[!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="e-using-declare"></a>E. DECLARE を使用する  
 次の例では、`@find` という名前のローカル変数を使って、`Walt` で始まるすべての姓の連絡先情報を取得します。  
  
```sql  
-- Uses AdventureWorks  
  
DECLARE @find VARCHAR(30);  
/* Also allowed:   
DECLARE @find VARCHAR(30) = 'Man%';  
*/  
SET @find = 'Walt%';  
  
SELECT LastName, FirstName, Phone  
FROM DimEmployee   
WHERE LastName LIKE @find;  
```  
  
### <a name="f-using-declare-with-two-variables"></a>F. DECLARE で 2 つの変数を使用する  
 次の例では、変数を使用し、`DimEmployee` テーブルにある従業員の名と姓を指定します。  
  
```sql  
-- Uses AdventureWorks  
  
DECLARE @lastName VARCHAR(30), @firstName VARCHAR(30);  
  
SET @lastName = 'Walt%';  
SET @firstName = 'Bryan';  
  
SELECT LastName, FirstName, Phone  
FROM DimEmployee   
WHERE LastName LIKE @lastName AND FirstName LIKE @firstName;  
```  
  
## <a name="see-also"></a>参照  
 [EXECUTE &#40;Transact-SQL&#41;](../../t-sql/language-elements/execute-transact-sql.md)   
 [組み込み関数 &#40;Transact-SQL&#41;](~/t-sql/functions/functions.md)   
 [SELECT &#40;Transact-SQL&#41;](../../t-sql/queries/select-transact-sql.md)   
 [テーブル &#40;Transact-SQL&#41;](../../t-sql/data-types/table-transact-sql.md)   
 [型指定された XML と型指定されていない XML の比較](../../relational-databases/xml/compare-typed-xml-to-untyped-xml.md)  
  
  




