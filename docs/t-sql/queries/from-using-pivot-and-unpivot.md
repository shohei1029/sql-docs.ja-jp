---
title: PIVOT および UNPIVOT の使用 | Microsoft Docs
description: PIVOT および UNPIVOT 関係演算子の Transact-SQL リファレンス。 SELECT ステートメントでこれらの演算子を使用すると、テーブル値式を別のテーブルに変更できます。
ms.date: 10/14/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- PIVOT_TSQL
helpviewer_keywords:
- FROM clause, UNPIVOT operator
- unpivoting tables
- table pivoting [SQL Server]
- UNPIVOT operator
- crosstab query
- PIVOT operator
- rotating table-valued expressions
- pivoting tables
- FROM clause, PIVOT operator
- rotating columns
ms.assetid: 24ba54fc-98f7-4d35-8881-b5158aac1d66
author: VanMSFT
ms.author: vanto
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 26d7cc4a646f4dcb9b0c4abd716c389608f47836
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104751572"
---
# <a name="from---using-pivot-and-unpivot"></a>FROM - PIVOT および UNPIVOT の使用

[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

関係演算子 `PIVOT` および `UNPIVOT` を使用すると、テーブル値式を別のテーブルに変更できます。 `PIVOT` では、式内の 1 つの列にある複数の一意の値を出力内の複数の列に変えることにより、テーブル値式が行列変換されます。 また、`PIVOT` で集計が実行されるのは、最終出力で必要な残りの任意の列値に対して集計が必要な場合です。 `UNPIVOT` 関係演算子の機能は PIVOT 関係演算子の逆で、テーブル値式の複数の列を列値に行列変換します。  
  
`PIVOT` の構文は、`SELECT...CASE` ステートメントを複雑に組み合わせて同じ操作を指定する場合よりも単純で読みやすくなります。 `PIVOT` の構文の詳細な説明については、「[FROM (Transact-SQL)](../../t-sql/queries/from-transact-sql.md)」を参照してください。  
  
## <a name="syntax"></a>構文  
次の構文では、`PIVOT` 演算子を使用する方法について説明します。  
  
```syntaxsql  
SELECT <non-pivoted column>,  
    [first pivoted column] AS <column name>,  
    [second pivoted column] AS <column name>,  
    ...  
    [last pivoted column] AS <column name>  
FROM  
    (<SELECT query that produces the data>)   
    AS <alias for the source query>  
PIVOT  
(  
    <aggregation function>(<column being aggregated>)  
FOR   
[<column that contains the values that will become column headers>]   
    IN ( [first pivoted column], [second pivoted column],  
    ... [last pivoted column])  
) AS <alias for the pivot table>  
<optional ORDER BY clause>;  
```  

## <a name="remarks"></a>解説  
`UNPIVOT` 句内の列識別子は、カタログ照合順序に従います。 [!INCLUDE[ssSDS_md](../../includes/sssds-md.md)] の場合、照合順序は常に `SQL_Latin1_General_CP1_CI_AS` です。 [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] の部分的包含データベースの場合、照合順序は常に `Latin1_General_100_CI_AS_KS_WS_SC` です。 列が他の列と結合されている場合、競合を回避するために COLLATE 句 (`COLLATE DATABASE_DEFAULT`) が必要です。  

  
## <a name="basic-pivot-example"></a>PIVOT の基本的な例  
次のコード例では、4 行、2 列で構成されるテーブルを生成します。  
  
```sql
USE AdventureWorks2014 ;  
GO  
SELECT DaysToManufacture, AVG(StandardCost) AS AverageCost   
FROM Production.Product  
GROUP BY DaysToManufacture;  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```
DaysToManufacture AverageCost
----------------- -----------
0                 5.0885
1                 223.88
2                 359.1082
4                 949.4105
```
  
`DaysToManufacture` の値が 3 である製品が定義されていません。  
  
次のコードでは、同じ結果が、`DaysToManufacture` 値を列見出しにピボット処理して表示されます。 結果が `[3]` であっても、3 日目 `NULL` の列が表示されます。  
  
```sql
-- Pivot table with one row and five columns  
SELECT 'AverageCost' AS Cost_Sorted_By_Production_Days,   
[0], [1], [2], [3], [4]  
FROM  
(SELECT DaysToManufacture, StandardCost   
    FROM Production.Product) AS SourceTable  
PIVOT  
(  
AVG(StandardCost)  
FOR DaysToManufacture IN ([0], [1], [2], [3], [4])  
) AS PivotTable;  
  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```
Cost_Sorted_By_Production_Days 0           1           2           3           4         
------------------------------ ----------- ----------- ----------- ----------- -----------
AverageCost                    5.0885      223.88      359.1082    NULL        949.4105
```
  
## <a name="complex-pivot-example"></a>PIVOT の複雑な例  
`PIVOT` 関係演算子が役立つ一般的なシナリオは、データの概要を示すためのクロス集計レポートを生成する場合です。 たとえば、`AdventureWorks2014` サンプル データベースの `PurchaseOrderHeader` テーブルにクエリを実行し、特定の従業員の発注数を抽出するとします。 このレポートを仕入先別に返すクエリを次に示します。  
  
```sql
USE AdventureWorks2014;  
GO  
SELECT VendorID, [250] AS Emp1, [251] AS Emp2, [256] AS Emp3, [257] AS Emp4, [260] AS Emp5  
FROM   
(SELECT PurchaseOrderID, EmployeeID, VendorID  
FROM Purchasing.PurchaseOrderHeader) p  
PIVOT  
(  
COUNT (PurchaseOrderID)  
FOR EmployeeID IN  
( [250], [251], [256], [257], [260] )  
) AS pvt  
ORDER BY pvt.VendorID;  
```  
  
次に結果セットの一部を示します。  
  
```
VendorID    Emp1        Emp2        Emp3        Emp4        Emp5  
----------- ----------- ----------- ----------- ----------- -----------
1492        2           5           4           4           4
1494        2           5           4           5           4
1496        2           4           4           5           5
1498        2           5           4           4           4
1500        3           4           4           5           4
```
  
このサブセレクト ステートメントにより返される結果は `EmployeeID` 列でピボット処理されています。  
  
```sql
SELECT PurchaseOrderID, EmployeeID, VendorID  
FROM PurchaseOrderHeader;  
```  
  
`EmployeeID` 列から返される一意の値が、最終的な結果セットのフィールドになります。 そのため、PIVOT 句で指定した `EmployeeID` 番号 (この例では、`250`、`251`、`256`、`257`、および `260`) ごとに列ができます。 `PurchaseOrderID` 列は、最終的な出力に返される列をグループ化する (この列をグループ化列といいます) のための値列です。 この例では、グループ化列を `COUNT` 関数で集計しています。 従業員ごとに `PurchaseOrderID` 関数を計算する際に `COUNT` 列に表示されている NULL 値を無視したことを示す警告メッセージが表示されます。  
  
> [!IMPORTANT]  
>  `PIVOT` 関係演算子と集計関数を併用する場合、値列に存在する NULL 値は集計を実行する際に無視されます。  

## <a name="unpivot-example"></a>UNPIVOT の例
  
`UNPIVOT` 関係演算子で行われる操作は、基本的に `PIVOT` 演算子の逆で、列を行に変換します。 上記の例で作成されたテーブルが `pvt` という名前でデータベースに保存されていて、列 ID `Emp1`、`Emp2`、`Emp3`、`Emp4`、および `Emp5` を、特定の仕入先に対応する行の値に行列変換するとします。 そのため、さらに 2 つの列を指定する必要があります。 行列変換する列値 (`Emp1`、`Emp2`、...) を格納する列を `Employee` と呼び、行列変換する列に現在存在している値を保持する列を `Orders` と呼びます。 これらの列は、それぞれ [!INCLUDE[tsql](../../includes/tsql-md.md)] 定義の *pivot_column* と *value_column* に対応します。 このクエリは次のようになります。  
  
```sql
-- Create the table and insert values as portrayed in the previous example.  
CREATE TABLE pvt (VendorID INT, Emp1 INT, Emp2 INT,  
    Emp3 INT, Emp4 INT, Emp5 INT);  
GO  
INSERT INTO pvt VALUES (1,4,3,5,4,4);  
INSERT INTO pvt VALUES (2,4,1,5,5,5);  
INSERT INTO pvt VALUES (3,4,3,5,4,4);  
INSERT INTO pvt VALUES (4,4,2,5,5,4);  
INSERT INTO pvt VALUES (5,5,1,5,5,5);  
GO  
-- Unpivot the table.  
SELECT VendorID, Employee, Orders  
FROM   
   (SELECT VendorID, Emp1, Emp2, Emp3, Emp4, Emp5  
   FROM pvt) p  
UNPIVOT  
   (Orders FOR Employee IN   
      (Emp1, Emp2, Emp3, Emp4, Emp5)  
)AS unpvt;  
GO  
```  
  
次に結果セットの一部を示します。  
  
```
VendorID    Employee    Orders
----------- ----------- ------
1            Emp1       4
1            Emp2       3 
1            Emp3       5
1            Emp4       4
1            Emp5       4
2            Emp1       4
2            Emp2       1
2            Emp3       5
2            Emp4       5
2            Emp5       5
...
```
  
`UNPIVOT` 関係演算子の動作は `PIVOT` 関係演算子の動作と正反対ではないことに注意してください。 `PIVOT` 関係演算子を実行すると集計が行われ、複数である可能性のある行が出力では 1 つの行にマージされます。 `UNPIVOT` 関係演算子を実行しても、行が既にマージされているので、最初のテーブル値式の結果を再現することはできません。 さらに、`UNPIVOT` の入力に含まれる NULL 値は、出力には表示されません。 値が表示されない場合、それは `PIVOT` 操作の前の入力に、NULL 値が含まれている可能性があることを示しています。  
  
[!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)] サンプル データベースのビュー `Sales.vSalesPersonSalesByFiscalYears` では、`PIVOT` 関係演算子を使用して会計年度別に販売員ごとの総売上を返します。 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] でビューをスクリプト化するには、**オブジェクト エクスプローラー** の [!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)] データベースの **[ビュー]** フォルダーで、スクリプト化するビューを探します。 ビュー名を右クリックし、 **[ビューをスクリプト化]** をクリックします。  
  
## <a name="see-also"></a>参照  
[FROM (Transact-SQL)](../../t-sql/queries/from-transact-sql.md)   
[CASE (Transact-SQL)](../../t-sql/language-elements/case-transact-sql.md)  
  
