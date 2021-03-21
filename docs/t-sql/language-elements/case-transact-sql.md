---
title: CASE (Transact-SQL)
description: CASE 関数の Transact-SQL リファレンス。 CASE では、条件の一覧が評価され、特定の結果が返されます。
ms.date: 01/26/2021
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- CASE_TSQL
- CASE
dev_langs:
- TSQL
helpviewer_keywords:
- CASE expression [Transact-SQL]
- simple CASE expression
- comparing expressions
- searched CASE expression
ms.assetid: 658039ec-8dc2-4251-bc82-30ea23708cee
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: d3b14e04172d1eaef6f8cdd4ade83978f8e204db
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104755462"
---
# <a name="case-transact-sql"></a>CASE (Transact-SQL)

[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

一連の条件を評価して、考えられる結果式のうちの 1 つを返します。  
  
 CASE 式には 2 つの形式があります。  
  
-   単純 CASE 式では、1 つの式を一連の単純式と比較して結果を決定します。  
  
-   検索 CASE 式では、一連のブール式を評価して結果を判定します。  
  
 どちらの形式も、ELSE 引数 (省略可) をサポートしています。  
  
 CASE は、有効な式を使用できる任意のステートメントや句で使用できます。 たとえば、SELECT、UPDATE、DELETE、SET などのステートメントや、select_list、IN、WHERE、ORDER BY、HAVING などの句で CASE を使用できます。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql
-- Syntax for SQL Server, Azure SQL Database and Azure Synapse Analytics
  
--Simple CASE expression:   
CASE input_expression   
     WHEN when_expression THEN result_expression [ ...n ]   
     [ ELSE else_result_expression ]   
END   

--Searched CASE expression:  
CASE  
     WHEN Boolean_expression THEN result_expression [ ...n ]   
     [ ELSE else_result_expression ]   
END  
```  
  
```syntaxsql
-- Syntax for Parallel Data Warehouse  
  
CASE  
     WHEN when_expression THEN result_expression [ ...n ]   
     [ ELSE else_result_expression ]   
END
```

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数  
 *input_expression*  
 単純 CASE 形式を使用した場合に評価される式です。 *input_expression* は任意の有効な [式](../../t-sql/language-elements/expressions-transact-sql.md)です。  
  
 WHEN *when_expression*  
 単純 CASE 形式を使用した場合に *input_expression* と比較される単純式です。 *when_expression* は任意の有効な式です。 *input_expression* と各 *when_expression* のデータ型は同一であるか、暗黙的な変換によって同一の型になる必要があります。  
  
 THEN *result_expression*  
 *input_expression* = *when_expression* が TRUE に評価されるとき、または *Boolean_expression* が TRUE に評価されるときに返される式です。 *result_expression* は任意の有効な [式](../../t-sql/language-elements/expressions-transact-sql.md)です。  
  
 ELSE *else_result_expression*  
 比較操作の評価がいずれも TRUE でなかった場合に返される式です。 この引数を省略し、比較操作のいずれも TRUE でなかった場合、CASE は NULL を返します。 *else_result_expression* は任意の有効な式です。 *else_result_expression* とすべての *result_expression* のデータ型は同一であるか、暗黙的な変換によって同一の型になる必要があります。  
  
 WHEN *Boolean_expression*  
 検索 CASE 形式で評価するブール式です。 *Boolean_expression* は任意の有効なブール式です。  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="return-types"></a>戻り値の型
 *result_expressions* および省略可能な *else_result_expression* の一連の型から、最も優先順位の高い型を返します。 詳細については、「[データ型の優先順位 &#40;Transact-SQL&#41;](../../t-sql/data-types/data-type-precedence-transact-sql.md)」を参照してください。  
  
### <a name="return-values"></a>戻り値  
 **単純 CASE 式:**  
  
 単純 CASE 式では、最初の式が各 WHEN 句の式と比較されて、等しいかどうかが確認されます。 等しかった場合は、THEN 句の式が返されます。  
  
-   実行できるのは、等しいかどうかのチェックだけです。  
  
-   各 WHEN 句の input_expression = when_expression を指定した順序で評価します。  
  
-   TRUE に評価される最初の *input_expression* = *when_expression* の *result_expression* を返します。  
  
-   *input_expression* = *when_expression* の評価がいずれも TRUE でなかった場合、[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] は、ELSE 句が指定されていれば *else_result_expression* を、ELSE 句が指定されていない場合は NULL を返します。  
  
 **検索 CASE 式:**  
  
-   各 WHEN 句の *Boolean_expression* を指定した順序で評価します。  
  
-   TRUE と評価された最初の *Boolean_expression* の *result_expression* を返します。  
  
-   *Boolean_expression* の評価がいずれも TRUE でなかった場合、[!INCLUDE[ssDE](../../includes/ssde-md.md)] は、ELSE 句が指定されていれば *else_result_expression* を、ELSE 句が指定されていない場合は NULL を返します。  
  
## <a name="remarks"></a>解説  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、Case 式に入れ子にできるのは 10 レベルまでです。  
  
 CASE 式を使用して、Transact-SQL ステートメント、ステートメント ブロック、ユーザー定義関数、およびストアド プロシージャの実行フローを制御することはできません。 フロー制御言語の一覧については、「[フロー制御言語 &#40;Transact-SQL&#41;](~/t-sql/language-elements/control-of-flow.md)」を参照してください。  
  
 CASE 式ではその条件が順に評価され、最初に条件が満たされた時点で評価が停止されます。 CASE 式には、評価された式の結果が入力として渡されることがあります。 こうした式の評価中にエラーが発生する可能性もあります。 CASE 式の WHEN 引数に指定された集計式は、あらかじめ評価されて CASE 式に渡されます。 たとえば、次のクエリでは、MAX 集計値を生成する際に 0 除算エラーが発生します。 このエラーが発生するのは、CASE 式が評価される前です。  
  
```sql  
WITH Data (value) AS   
(   
SELECT 0   
UNION ALL   
SELECT 1   
)   
SELECT   
   CASE   
      WHEN MIN(value) <= 0 THEN 0   
      WHEN MAX(1/value) >= 100 THEN 1   
   END   
FROM Data ;  
```  
  
 WHEN 条件が上から順に評価されるという前提は、スカラー式 (スカラー値を返す非相関サブクエリを含む) では問題ありませんが、集計式では成立しないので注意が必要です。  
  
## <a name="examples"></a>例  
  
### <a name="a-using-a-select-statement-with-a-simple-case-expression"></a>A. SELECT ステートメントを単純 CASE 式と共に使用する  
 `SELECT` ステートメント内では、単純 `CASE` 式は等しいかどうかのチェックだけを実行できます。これ以外の比較操作は実行できません。 `CASE` 式を使用して、製品ラインのカテゴリの表示をわかりやすいものに変更する例を次に示します。  
  
```sql  
USE AdventureWorks2012;  
GO  
SELECT   ProductNumber, Category =  
      CASE ProductLine  
         WHEN 'R' THEN 'Road'  
         WHEN 'M' THEN 'Mountain'  
         WHEN 'T' THEN 'Touring'  
         WHEN 'S' THEN 'Other sale items'  
         ELSE 'Not for sale'  
      END,  
   Name  
FROM Production.Product  
ORDER BY ProductNumber;  
GO  
```  
  
### <a name="b-using-a-select-statement-with-a-searched-case-expression"></a>B. SELECT ステートメントを検索 CASE 式と共に使用する  
 `SELECT` ステートメント内では、検索 `CASE` 式は比較値に基づいて結果セット内で値を置換できます。 次の例では、表示価格を、製品の価格範囲に基づいたテキスト コメントとして表示しています。  
  
```sql  
USE AdventureWorks2012;  
GO  
SELECT   ProductNumber, Name, "Price Range" =   
      CASE   
         WHEN ListPrice =  0 THEN 'Mfg item - not for resale'  
         WHEN ListPrice < 50 THEN 'Under $50'  
         WHEN ListPrice >= 50 and ListPrice < 250 THEN 'Under $250'  
         WHEN ListPrice >= 250 and ListPrice < 1000 THEN 'Under $1000'  
         ELSE 'Over $1000'  
      END  
FROM Production.Product  
ORDER BY ProductNumber ;  
GO  
```  
  
### <a name="c-using-case-in-an-order-by-clause"></a>C. ORDER BY 句で CASE を使用する  
 次の例では、ORDER BY 句で CASE 式を使用して、指定された列の値に基づいて行の並べ替え順序を決定しています。 最初の例では、`SalariedFlag` テーブルの `HumanResources.Employee` 列の値を評価します。 `SalariedFlag` が 1 に設定されている従業員は `BusinessEntityID` の降順で、 `SalariedFlag` が 0 に設定されている従業員は `BusinessEntityID` の昇順で返されます。 2 番目の例では、`TerritoryName` 列が 'United States' と等しい場合は結果セットが `CountryRegionName` 列の順序に従って並べ替えられ、他のすべての列は `CountryRegionName` の順序に従って並べ替えられます。  
  
```sql  
SELECT BusinessEntityID, SalariedFlag  
FROM HumanResources.Employee  
ORDER BY CASE SalariedFlag WHEN 1 THEN BusinessEntityID END DESC  
        ,CASE WHEN SalariedFlag = 0 THEN BusinessEntityID END;  
GO    
```  
  
```sql  
SELECT BusinessEntityID, LastName, TerritoryName, CountryRegionName  
FROM Sales.vSalesPerson  
WHERE TerritoryName IS NOT NULL  
ORDER BY CASE CountryRegionName WHEN 'United States' THEN TerritoryName  
         ELSE CountryRegionName END;  
```  
  
### <a name="d-using-case-in-an-update-statement"></a>D. UPDATE ステートメントで CASE を使用する  
 次の例では、UPDATE ステートメントで CASE 式を使用して、`SalariedFlag` が 0 に設定されている従業員の `VacationHours` 列の値を決定しています。 `VacationHours` の値を 10 時間差し引くと値がマイナスになる場合は `VacationHours` の値を 40 時間増やします。それ以外の場合は、`VacationHours` の値を 20 時間増やします。 OUTPUT 句は、この処理の前後の休暇の値を表示するために使用されています。  
  
```sql  
USE AdventureWorks2012;  
GO  
UPDATE HumanResources.Employee  
SET VacationHours =   
    ( CASE  
         WHEN ((VacationHours - 10.00) < 0) THEN VacationHours + 40  
         ELSE (VacationHours + 20.00)  
       END  
    )  
OUTPUT Deleted.BusinessEntityID, Deleted.VacationHours AS BeforeValue,   
       Inserted.VacationHours AS AfterValue  
WHERE SalariedFlag = 0;   
```  
  
### <a name="e-using-case-in-a-set-statement"></a>E. SET ステートメントで CASE を使用する  
 次の例では、テーブル値関数 `dbo.GetContactInfo` の SET ステートメントで CASE 式を使用しています。 [!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)] データベースでは、人に関連するデータはすべて `Person.Person` テーブルに格納されています。 たとえば、従業員、仕入先の代表者、消費者などはすべて人に関連するデータとして扱われます。 この関数は、指定された `BusinessEntityID` の氏名と連絡先タイプを返します。`ContactType` 列に表示される値は、SET ステートメント内の CASE 式により、`Employee`、`Vendor`、または `Customer` のどのテーブルに `BusinessEntityID` 列が含まれているかに基づいて決定されます。  
  
```sql  
USE AdventureWorks2012;  
GO  
CREATE FUNCTION dbo.GetContactInformation(@BusinessEntityID INT)  
    RETURNS @retContactInformation TABLE   
(  
BusinessEntityID INT NOT NULL,  
FirstName NVARCHAR(50) NULL,  
LastName NVARCHAR(50) NULL,  
ContactType NVARCHAR(50) NULL,  
    PRIMARY KEY CLUSTERED (BusinessEntityID ASC)  
)   
AS   
-- Returns the first name, last name and contact type for the specified contact.  
BEGIN  
    DECLARE   
        @FirstName NVARCHAR(50),   
        @LastName NVARCHAR(50),   
        @ContactType NVARCHAR(50);  
  
    -- Get common contact information  
    SELECT   
        @BusinessEntityID = BusinessEntityID,   
@FirstName = FirstName,   
        @LastName = LastName  
    FROM Person.Person   
    WHERE BusinessEntityID = @BusinessEntityID;  
  
    SET @ContactType =   
        CASE   
            -- Check for employee  
            WHEN EXISTS(SELECT * FROM HumanResources.Employee AS e   
                WHERE e.BusinessEntityID = @BusinessEntityID)   
                THEN 'Employee'  
  
            -- Check for vendor  
            WHEN EXISTS(SELECT * FROM Person.BusinessEntityContact AS bec  
                WHERE bec.BusinessEntityID = @BusinessEntityID)   
                THEN 'Vendor'  
  
            -- Check for store  
            WHEN EXISTS(SELECT * FROM Purchasing.Vendor AS v            
                WHERE v.BusinessEntityID = @BusinessEntityID)   
                THEN 'Store Contact'  
  
            -- Check for individual consumer  
            WHEN EXISTS(SELECT * FROM Sales.Customer AS c   
                WHERE c.PersonID = @BusinessEntityID)   
                THEN 'Consumer'  
        END;  
  
    -- Return the information to the caller  
    IF @BusinessEntityID IS NOT NULL   
    BEGIN  
        INSERT @retContactInformation  
        SELECT @BusinessEntityID, @FirstName, @LastName, @ContactType;  
    END;  
  
    RETURN;  
END;  
GO  
  
SELECT BusinessEntityID, FirstName, LastName, ContactType  
FROM dbo.GetContactInformation(2200);  
GO  
SELECT BusinessEntityID, FirstName, LastName, ContactType  
FROM dbo.GetContactInformation(5);  
```  
  
### <a name="f-using-case-in-a-having-clause"></a>F. HAVING 句で CASE を使用する  
 次の例では、HAVING 句で CASE 式を使用して、SELECT ステートメントで返される行を制限しています。 このステートメントは、`HumanResources.Employee` テーブル内の各役職の最も高い時給を返します。 HAVING 句では、最も高い時給が男性の場合には 40 ドル、女性の場合には 42 ドルを超えている役職のみが返されるように制限しています。  
  
```sql 
USE AdventureWorks2012;  
GO  
SELECT JobTitle, MAX(ph1.Rate)AS MaximumRate  
FROM HumanResources.Employee AS e  
JOIN HumanResources.EmployeePayHistory AS ph1 ON e.BusinessEntityID = ph1.BusinessEntityID  
GROUP BY JobTitle  
HAVING (MAX(CASE WHEN Gender = 'M'   
        THEN ph1.Rate   
        ELSE NULL END) > 40.00  
     OR MAX(CASE WHEN Gender  = 'F'   
        THEN ph1.Rate    
        ELSE NULL END) > 42.00)  
ORDER BY MaximumRate DESC;  
```  
  
## <a name="examples-sssdwfull-and-sspdw"></a>例: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)]、[!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="g-using-a-select-statement-with-a-case-expression"></a>G. SELECT ステートメントを単純 CASE 式と共に使用する  
 SELECT ステートメント内では、CASE 式は比較値に基づいて結果セット内で値を置換できます。 CASE 式を使用して、製品ラインのカテゴリの表示をわかりやすいものに変更する例を次に示します。 値が存在しない場合は、テキスト "Not for sale" が表示されます。  
  
```sql 
-- Uses AdventureWorks  
  
SELECT   ProductAlternateKey, Category =  
      CASE ProductLine  
         WHEN 'R' THEN 'Road'  
         WHEN 'M' THEN 'Mountain'  
         WHEN 'T' THEN 'Touring'  
         WHEN 'S' THEN 'Other sale items'  
         ELSE 'Not for sale'  
      END,  
   EnglishProductName  
FROM dbo.DimProduct  
ORDER BY ProductKey;  
```  
  
### <a name="h-using-case-in-an-update-statement"></a>H. UPDATE ステートメントで CASE を使用する  
 次の例では、UPDATE ステートメントで CASE 式を使用して、`SalariedFlag` が 0 に設定されている従業員の `VacationHours` 列の値を決定しています。 `VacationHours` の値を 10 時間差し引くと値がマイナスになる場合は `VacationHours` の値を 40 時間増やします。それ以外の場合は、`VacationHours` の値を 20 時間増やします。  
  
```sql 
-- Uses AdventureWorks   
  
UPDATE dbo.DimEmployee  
SET VacationHours =   
    ( CASE  
         WHEN ((VacationHours - 10.00) < 0) THEN VacationHours + 40  
         ELSE (VacationHours + 20.00)   
       END  
    )   
WHERE SalariedFlag = 0;  
```  
  
## <a name="see-also"></a>参照  
 [式 &#40;Transact-SQL&#41;](../../t-sql/language-elements/expressions-transact-sql.md)   
 [SELECT &#40;Transact-SQL&#41;](../../t-sql/queries/select-transact-sql.md)   
 [COALESCE &#40;Transact-SQL&#41;](../../t-sql/language-elements/coalesce-transact-sql.md)   
 [IIF &#40;Transact-SQL&#41;](../../t-sql/functions/logical-functions-iif-transact-sql.md)   
 [CHOOSE &#40;Transact-SQL&#41;](../../t-sql/functions/logical-functions-choose-transact-sql.md)  
  
  



