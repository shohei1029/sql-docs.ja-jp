---
description: TOP (Transact-SQL)
title: TOP (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/16/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- TOP_TSQL
- TOP
dev_langs:
- TSQL
helpviewer_keywords:
- TOP clause
- first set of query result rows [SQL Server]
- TOP clause, about TOP clause
- queries [SQL Server], results
ms.assetid: da983c0a-06c5-4cf8-a6a4-7f9d66f34f2c
author: VanMSFT
ms.author: vanto
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 23f9bd7c9e0bddc53128b8ac6d8117a4cb609168
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104748902"
---
# <a name="top-transact-sql"></a>TOP (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] で、クエリの結果セットとして返される行を、指定した行の数または割合に制限します。 TOP と ORDER BY 句を使用すると、結果セットは並べ替えられた行の先頭の *N* 行に制限されます。 それ以外の場合、TOP では特定の順序になっていない先頭の *N* 行が返されます。 この句を使用して、SELECT ステートメントから返される行の数を指定します。 または、TOP を使用して、INSERT、UPDATE、MERGE、DELETE ステートメントによって影響を受ける行数を指定します。  
  
![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
 
 SQL Server および Azure SQL Database での構文は次のとおりです。

```syntaxsql  
[   
    TOP (expression) [PERCENT]  
    [ WITH TIES ]  
]  
```  

[!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] と [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] の構文を次に示します。

```syntaxsql  
[   
    TOP ( expression )   
    [ WITH TIES ]  
]  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
*式 (expression)*  
返す行数を指定する数値式。 PERCENT を指定すると、*expression* は暗黙で **float** 値に変換されます。 それ以外の場合、*expression* は **bigint** に変換されます。  
  
PERCENT  
クエリの結果セットの先頭から、*expression* で指定したパーセントの行のみを返すように指定します。 小数値は整数値に切り上げられます。  
  
WITH TIES  
制限された結果セットの最後の場所にある複数の行を返します。 この引数は、**ORDER BY** 句と共に使用する必要があります。 **WITH TIES** を使用すると、*expression* で指定した値より多くの行が返されることがあります。 たとえば、*expression* を 5 に設定した場合、5 行目の **ORDER BY** 列の値と一致する行が他に 2 行ある場合、結果セットに含まれる行は 7 行になります。  
  
TOP 句で WITH TIES 引数を指定できるのは、SELECT ステートメントの中だけで、ORDER BY 句も指定した場合のみです。 同順位のレコードが返される順序は任意です。 ORDER BY はこの規則に影響しません。  
  
## <a name="best-practices"></a>推奨する運用方法  
SELECT ステートメントでは、必ず ORDER BY 句と TOP 句を使用してください。 これは、TOP によって処理される行を予想して指定するための唯一の方法です。  
  
クエリ ページング ソリューションを実装するには、TOP 句ではなく ORDER BY 句で OFFSET と FETCH を使用します。 OFFSET 句と FETCH 句を使用した方が、ページング ソリューション (データのチャンクつまり "ページ" をクライアントに送信するソリューション) を容易に実装できます。 詳細については、「[ORDER BY 句 (Transact-SQL)](../../t-sql/queries/select-order-by-clause-transact-sql.md)」を参照してください。  
  
返される行の数を制限するには、SET ROWCOUNT ではなく TOP (または OFFSET と FETCH) を使用します。 SET ROWCOUNT を使用するよりこれらの方法を使用することをお勧めする理由は、以下のとおりです。  
  
-   クエリ オプティマイザーでは、クエリを最適化する際に、TOP 句または FETCH 句の *expression* の値を SELECT ステートメントの一部として認識できます。 SET ROWCOUNT はクエリを実行するステートメントの外部で使用するので、その値をクエリ プランで認識することはできません。  
  
## <a name="compatibility-support"></a>互換性サポート  
下位互換性のために、式が整数の定数の場合、SELECT ステートメントではかっこは省略可能です。 SELECT ステートメントの TOP では常にかっこを使用することをお勧めします。 そうすることで、かっこが必要な INSERT、UPDATE、MERGE、DELETE ステートメントとの一貫性を保つことができます。 
  
## <a name="interoperability"></a>相互運用性  
TOP 式は、トリガーによって実行される可能性があるステートメントには影響しません。 トリガーによって **挿入** および **削除** されたテーブルからは、INSERT、UPDATE、MERGE、または DELETE ステートメントによって実際に影響を受けた行のみが返されます。 たとえば、TOP 句が含まれる INSERT ステートメントの結果として INSERT TRIGGER が起動されるような場合です。  
  
[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、ビューを使用した行の更新が可能です。 ビュー定義に TOP 句を含めることができるので、更新のために行が TOP 式の要件を満たさなくなると、特定の行がビューに表示されなくなる場合があります。  
  
MERGE ステートメントで指定した TOP 句は、ソース テーブル全体とターゲット テーブル全体が結合された "*後*" で適用されます。 また、結合された行で挿入、更新、または削除操作の対象にならないものは削除されます。 TOP 句を使用すると、結合された行の数が指定の値まで減少し、挿入、更新、または削除操作が残りの結合された行に順序付けなしで適用されます。 つまり、WHEN 句に定義された操作に行が割り当てられる順序は決まっていません。 たとえば、TOP (10) と指定して 10 行が影響を受けた場合、そのうちの 7 行は更新されたもので、3 行は挿入されたものかもしれません。 または、1 行が削除され、5 行が更新され、4 行が挿入されている場合もあります。 MERGE ステートメントではソース テーブルとターゲット テーブルの両方のフル テーブル スキャンが行われるので、TOP 句を使用し、複数のバッチを作成して大きなテーブルを変更すると、I/O パフォーマンスが影響を受ける場合があります。 このシナリオでは、連続するすべてのバッチで確実に新しい行が対象になっていることが重要です。  
  
UNION、UNION ALL、EXCEPT、または INTERSECT 演算子を含む TOP 句をクエリで指定する場合は注意が必要です。 これらの演算子を選択操作で使用するとき、TOP 句および ORDER BY 句が論理的に処理される順序は必ずしも直感的ではないため、記述したクエリによって予期しない結果が返される場合があります。 たとえば、次のテーブルとデータがある場合に、最も安価な赤色の自動車と青色の自動車  (つまり赤色のセダンと青色のバン) を取得する必要があるとします。  
  
```sql  
CREATE TABLE dbo.Cars(Model VARCHAR(15), Price MONEY, Color VARCHAR(10));  
INSERT dbo.Cars VALUES  
    ('sedan', 10000, 'red'), ('convertible', 15000, 'blue'),   
    ('coupe', 20000, 'red'), ('van', 8000, 'blue');  
```  
  
これらの結果を得るためには、次のクエリを記述します。  
  
```sql  
SELECT TOP(1) Model, Color, Price  
FROM dbo.Cars  
WHERE Color = 'red'  
UNION ALL  
SELECT TOP(1) Model, Color, Price  
FROM dbo.Cars  
WHERE Color = 'blue'  
ORDER BY Price ASC;  
GO    
```  
  
結果セットは次のようになります。  
  
 ```
 Model         Color      Price  
 ------------- ---------- -------  
 sedan         red        10000.00  
 convertible   blue       15000.00
 ```  
  
TOP 句は論理的には ORDER BY 句の前に実行され、演算子 (この場合は UNION ALL) の結果が並べ替えられるため、予期しない結果が返されます。 したがって、前のクエリでは任意の赤色の自動車と青色の自動車が 1 つずつ返され、その和集合の結果が価格で並べ替えられます。 目的の結果を得るための正しいクエリの例を次に示します。  
  
```sql  
SELECT Model, Color, Price  
FROM (SELECT TOP(1) Model, Color, Price  
      FROM dbo.Cars  
      WHERE Color = 'red'  
      ORDER BY Price ASC) AS a  
UNION ALL  
SELECT Model, Color, Price  
FROM (SELECT TOP(1) Model, Color, Price  
      FROM dbo.Cars  
      WHERE Color = 'blue'  
      ORDER BY Price ASC) AS b;  
GO    
```  
  
TOP と ORDER BY をサブセレクト操作で使用することにより、ORDER BY 句の結果が TOP 句に適用され、UNION 操作の結果の並べ替えには使用されなくなります。  
  
 結果セットは次のようになります。  
  
 ```
 Model         Color      Price  
 ------------- ---------- -------  
 sedan         red        10000.00  
 van           blue        8000.00
 ```  
  
## <a name="limitations-and-restrictions"></a>制限事項と制約事項  
INSERT、UPDATE、MERGE、または DELETE で TOP を使用すると、参照される行はどのような順序でも並べ替えられません。 また、これらのステートメントで ORDER BY 句を直接指定することはできません。 TOP を使用して意味のある順序で行を挿入、削除、変更する必要がある場合は、サブセレクト ステートメントで指定された ORDER BY 句で TOP を使用します。 この記事で後述する例のセクションを参照してください。  
  
パーティション ビューの UPDATE および DELETE ステートメントでは、TOP を使用できません。  
  
(同じクエリ スコープ内の) 同じクエリ式で TOP を OFFSET および FETCH と組み合わせて使用することはできません。 詳細については、「[ORDER BY 句 (Transact-SQL)](../../t-sql/queries/select-order-by-clause-transact-sql.md)」を参照してください。  
  
## <a name="examples"></a>例  
  
|カテゴリ|主な構文要素|  
|--------------|------------------------------|  
|[基本構文](#BasicSyntax)|TOP、PERCENT|  
|[同順位の値を含む](#tie)|WITH TIES|  
|[DELETE、INSERT、または UPDATE の影響を受ける行の制限](#DML)|DELETE、INSERT、UPDATE|  
  
###  <a name="basic-syntax"></a><a name="BasicSyntax"></a> 基本構文  
このセクションの例では、最低限必要な構文を使用して ORDER BY 句の基本機能を示します。  
  
#### <a name="a-using-top-with-a-constant-value"></a>A. 定数値を指定して TOP を使用する  
次の例では、クエリの結果セットで返される従業員の数を、定数値を使用して指定します。 最初の例では、ORDER BY 句が使用されていないため、先頭の 10 行が任意の順序で返されます。 2 番目の例では、ORDER BY 句が使用されているため、新規採用者が新しい方から 10 名返されます。  
  
```sql  
USE AdventureWorks2012;  
GO  
-- Select the first 10 random employees.  
SELECT TOP(10)JobTitle, HireDate  
FROM HumanResources.Employee;  
GO  
-- Select the first 10 employees hired most recently.  
SELECT TOP(10)JobTitle, HireDate  
FROM HumanResources.Employee  
ORDER BY HireDate DESC;  
GO  
```  
  
#### <a name="b-using-top-with-a-variable"></a>B. 変数を指定して TOP を使用する  
次の例では、クエリの結果セットで返される従業員の数を、変数を使用して指定します。  
  
```sql  
USE AdventureWorks2012;  
GO  
DECLARE @p AS INT = 10;  
SELECT TOP(@p)JobTitle, HireDate, VacationHours  
FROM HumanResources.Employee  
ORDER BY VacationHours DESC;  
GO  
```  
  
#### <a name="c-specifying-a-percentage"></a>C. 割合を指定する  
次の例では、クエリの結果セットで返される従業員の数を、PERCENT を使用して指定します。 `HumanResources.Employee` テーブルには、290 人の従業員が記録されています。 290 の 5% は小数を含む値なので、整数に切り上げられます。  
  
```sql  
USE AdventureWorks2012;  
GO  
SELECT TOP(5)PERCENT JobTitle, HireDate  
FROM HumanResources.Employee  
ORDER BY HireDate DESC;  
GO    
```  
  
###  <a name="including-tie-values"></a><a name="tie"></a> 同順位の値を含む  
  
#### <a name="a-using-with-ties-to-include-rows-that-match-the-values-in-the-last-row"></a>A. WITH TIES を使用して、最後の行の値と一致する行を含める  
次の例では、給与の高い上位 `10` % の全従業員を取得し、結果を給与の降順で返します。 `WITH TIES` を指定すると、返された最も低い給与 (最後の行) と同じ給与の従業員も返されるので、結果は全従業員の `10` % を超える場合があります。  
  
```sql  
USE AdventureWorks2012;  
GO  
SELECT TOP(10) PERCENT WITH TIES  
pp.FirstName, pp.LastName, e.JobTitle, e.Gender, r.Rate  
FROM Person.Person AS pp   
    INNER JOIN HumanResources.Employee AS e  
        ON pp.BusinessEntityID = e.BusinessEntityID  
    INNER JOIN HumanResources.EmployeePayHistory AS r  
        ON r.BusinessEntityID = e.BusinessEntityID  
ORDER BY Rate DESC;  
GO    
```  
  
###  <a name="limiting-the-rows-affected-by-delete-insert-or-update"></a><a name="DML"></a> DELETE、INSERT、または UPDATE の影響を受ける行の制限  
  
#### <a name="a-using-top-to-limit-the-number-of-rows-deleted"></a>A. TOP を使用して削除する行数を制限する  
DELETE で TOP (*n*) 句を使用した場合、任意に選択される *n* 行に対して削除操作が実行されます。 つまり、DELETE ステートメントは WHERE 句で定義した条件を満たす任意の数 (*n*) の行を選択します。 次の例では、納期が 2002 年 7 月 1 日より早い `20` 行を `PurchaseOrderDetail` テーブルから選択して削除します。  
  
```sql  
USE AdventureWorks2012;  
GO  
DELETE TOP (20)   
FROM Purchasing.PurchaseOrderDetail  
WHERE DueDate < '20020701';  
GO  
```  
  
TOP を使用して、意味のある順序で行を削除する必要がある場合は、サブセレクト ステートメントで TOP と ORDER BY を使用します。 次のクエリでは、納期が早いものから 10 行を `PurchaseOrderDetail` テーブルから削除します。 10 行だけを確実に削除するために、サブセレクト ステートメントではテーブルの主キーの列 (`PurchaseOrderID`) を指定しています。 サブセレクト ステートメントで非キー列を指定すると、指定した列に重複する値が含まれる場合、10 行以上の行が削除される可能性があります。  
  
```sql  
USE AdventureWorks2012;  
GO  
DELETE FROM Purchasing.PurchaseOrderDetail  
WHERE PurchaseOrderDetailID IN  
   (SELECT TOP 10 PurchaseOrderDetailID   
    FROM Purchasing.PurchaseOrderDetail   
    ORDER BY DueDate ASC);  
GO  
```  
  
#### <a name="b-using-top-to-limit-the-number-of-rows-inserted"></a>B. TOP を使用して挿入する行数を制限する  
次の例では、`EmployeeSales` テーブルを作成し、上位 5 人の従業員の名前と年度累計売り上げデータを `HumanResources.Employee` テーブルから挿入します。 INSERT ステートメントは、`SELECT` ステートメントで返された、WHERE 句で定義した条件を満たす任意の 5 行を選択します。 OUTPUT 句は、`EmployeeSales` テーブルに挿入される行を表示します。 上位 5 人の従業員を特定するために SELECT ステートメントの ORDER BY 句を使用することはありません。  
  
```sql  
USE AdventureWorks2012 ;  
GO  
IF OBJECT_ID ('dbo.EmployeeSales', 'U') IS NOT NULL  
    DROP TABLE dbo.EmployeeSales;  
GO  
CREATE TABLE dbo.EmployeeSales  
( EmployeeID   NVARCHAR(11) NOT NULL,  
  LastName     NVARCHAR(20) NOT NULL,  
  FirstName    NVARCHAR(20) NOT NULL,  
  YearlySales  MONEY NOT NULL  
 );  
GO  
INSERT TOP(5)INTO dbo.EmployeeSales  
    OUTPUT inserted.EmployeeID, inserted.FirstName, inserted.LastName, inserted.YearlySales  
    SELECT sp.BusinessEntityID, c.LastName, c.FirstName, sp.SalesYTD   
    FROM Sales.SalesPerson AS sp  
    INNER JOIN Person.Person AS c  
        ON sp.BusinessEntityID = c.BusinessEntityID  
    WHERE sp.SalesYTD > 250000.00  
    ORDER BY sp.SalesYTD DESC;  
GO    
```  
  
TOP を使用して、意味のある順序で行を挿入する必要がある場合は、サブセレクト ステートメントで TOP と ORDER BY を使用します。 その方法を次の例に示します。 OUTPUT 句は、`EmployeeSales` テーブルに挿入される行を表示します。 未定義の行の代わりに、ORDER BY 句の結果に基づいて、上位 5 人の従業員が挿入されます。  
  
```sql  
INSERT INTO dbo.EmployeeSales  
    OUTPUT inserted.EmployeeID, inserted.FirstName, inserted.LastName, inserted.YearlySales  
    SELECT TOP (5) sp.BusinessEntityID, c.LastName, c.FirstName, sp.SalesYTD   
    FROM Sales.SalesPerson AS sp  
    INNER JOIN Person.Person AS c  
        ON sp.BusinessEntityID = c.BusinessEntityID  
    WHERE sp.SalesYTD > 250000.00  
    ORDER BY sp.SalesYTD DESC;  
GO    
```  
  
#### <a name="c-using-top-to-limit-the-number-of-rows-updated"></a>C. TOP を使用して更新する行数を制限する  
次の例では、TOP 句を使用してテーブルの行を更新します。 UPDATE ステートメントで TOP (*n*) 句を使用した場合、任意の数の行に対して更新操作が実行されます。 つまり、UPDATE ステートメントは WHERE 句で定義した条件を満たす任意の数 (*n*) の行を選択します。 次の例では、ある販売員の顧客から 10 人を抽出して別の販売員に割り当てています。  
  
```sql  
USE AdventureWorks2012;  
UPDATE TOP (10) Sales.Store  
SET SalesPersonID = 276  
WHERE SalesPersonID = 275;  
GO  
```  
  
TOP を使用して意味のある日時順での更新を適用する場合は、サブセレクト ステートメントで ORDER BY TOP を使用する必要があります。 次の例では、採用日の古い従業員上位 10 人の休暇時間を更新します。  
  
```sql  
UPDATE HumanResources.Employee  
SET VacationHours = VacationHours + 8  
FROM (SELECT TOP 10 BusinessEntityID FROM HumanResources.Employee  
     ORDER BY HireDate ASC) AS th  
WHERE HumanResources.Employee.BusinessEntityID = th.BusinessEntityID;  
GO  
```  
  
## <a name="examples-sssdwfull-and-sspdw"></a>例: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)]、[!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
次の例では、クエリ条件に一致する上位 31 行を返します。 **ORDER BY** 句により、返される 31 行は、`LastName` 列のアルファベット順に基づく最初の 31 行になります。  
  
ties を指定せずに **TOP** を使用する。  
  
```sql  
SELECT TOP (31) FirstName, LastName   
FROM DimEmployee ORDER BY LastName;  
```  
  
結果: 31 行が返されます。  
  
WITH TIES を指定して TOP を使用する。  
  
```sql  
SELECT TOP (31) WITH TIES FirstName, LastName   
FROM DimEmployee ORDER BY LastName;  
```  
  
結果:33 行が返されます。これは、Brown という名前の 3 人の従業員が 31 番目の行と同順位であるためです。  
  
## <a name="see-also"></a>参照  
 [SELECT &#40;Transact-SQL&#41;](../../t-sql/queries/select-transact-sql.md)   
 [INSERT &#40;Transact-SQL&#41;](../../t-sql/statements/insert-transact-sql.md)   
 [UPDATE &#40;Transact-SQL&#41;](../../t-sql/queries/update-transact-sql.md)   
 [DELETE &#40;Transact-SQL&#41;](../../t-sql/statements/delete-transact-sql.md)   
 [ORDER BY 句 &#40;Transact-SQL&#41;](../../t-sql/queries/select-order-by-clause-transact-sql.md)   
 [SET ROWCOUNT &#40;Transact-SQL&#41;](../../t-sql/statements/set-rowcount-transact-sql.md)   
 [MERGE &#40;Transact-SQL&#41;](../../t-sql/statements/merge-transact-sql.md)  
  
 
