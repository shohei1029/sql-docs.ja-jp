---
description: sp_executesql (Transact-sql)
title: sp_executesql (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/16/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- sp_executesql
- sp_executesql_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sp_executesql
- dynamic SQL
ms.assetid: a8d68d72-0f4d-4ecb-ae86-1235b962f646
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 48c145f62573b3a4a0388dfbaf3ab0e7ba6aa181
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104750032"
---
# <a name="sp_executesql-transact-sql"></a>sp_executesql (Transact-sql)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  何回も再利用可能な、または動的に作成した [!INCLUDE[tsql](../../includes/tsql-md.md)] ステートメントやバッチを実行します。 [!INCLUDE[tsql](../../includes/tsql-md.md)] ステートメントやバッチには、埋め込みパラメーターを含めることができます。  
  
> [!IMPORTANT]  
>  実行時にコンパイルさ [!INCLUDE[tsql](../../includes/tsql-md.md)] れたステートメントは、アプリケーションを悪意のある攻撃にさらす可能性があります。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql  
-- Syntax for SQL Server, Azure SQL Database, Azure Synapse Analytics, Parallel Data Warehouse  
  
sp_executesql [ @stmt = ] statement  
[   
  { , [ @params = ] N'@parameter_name data_type [ OUT | OUTPUT ][ ,...n ]' }   
     { , [ @param1 = ] 'value1' [ ,...n ] }  
]  
```  
  
## <a name="arguments"></a>引数  
 [ \@ stmt =] *ステートメント*  
 ステートメントまたはバッチを含む Unicode 文字列を指定し [!INCLUDE[tsql](../../includes/tsql-md.md)] ます。 \@stmt は、Unicode 定数または Unicode 変数のいずれかである必要があります。 + 演算子で 2 つの文字列を連結するなどの複雑な Unicode 式は使用できません。 文字定数も使用できません。 Unicode 定数を指定する場合は、先頭に **N** を付ける必要があります。たとえば、Unicode 定数 **N ' sp_who '** は有効ですが、文字定数 **' sp_who '** は有効ではありません。 文字列のサイズは、使用可能なデータベースサーバーのメモリによってのみ制限されます。 64ビットサーバーでは、文字列のサイズは、最大サイズである **nvarchar (max)** の 2 GB に制限されています。  
  
> [!NOTE]  
>  \@stmt には、変数名と同じ形式のパラメーターを含めることができます。次に例を示します。 `N'SELECT * FROM HumanResources.Employee WHERE EmployeeID = @IDParameter'`  
  
 Stmt に含まれる各パラメーターには、 \@ \@ params パラメーター定義リストとパラメーター値リストの両方に対応するエントリが必要です。  
  
 [ \@ params =] N ' \@*parameter_name* *data_type* [,... *n* ] '  
 Stmt に埋め込まれているすべてのパラメーターの定義を含む1つの文字列を指定 \@ します。文字列は、Unicode 定数または Unicode 変数のいずれかである必要があります。 各パラメーター定義は、パラメーター名とデータ型で構成されます。 *n* は、追加のパラメーター定義を示すプレースホルダーです。 Stmt に指定するすべてのパラメーター \@ は、params で定義する必要があり \@ ます。 [!INCLUDE[tsql](../../includes/tsql-md.md)]Stmt のステートメントまたはバッチにパラメーターが含まれていない場合 \@ 、 \@ params は必要ありません。 このパラメーターの既定値は NULL です。  
  
 [ \@ param1 =] '*value1*'  
 パラメーター文字列に定義する最初のパラメーターの値を指定します。 Unicode 定数または Unicode 変数を指定できます。 Stmt に含まれるすべてのパラメーターにパラメーター値が指定されている必要があり \@ ます。 [!INCLUDE[tsql](../../includes/tsql-md.md)] Stmt のステートメントまたはバッチにパラメーターがない場合、値は必要ありません \@ 。  
  
 [ OUT | OUTPUT ]  
 パラメーターが出力パラメーターであることを示します。 **text**、 **ntext**、および **image** パラメーターは、プロシージャが共通言語ランタイム (CLR) プロシージャでない限り、出力パラメーターとして使用できます。 OUTPUT キーワードを使用する出力パラメーターは、プロシージャが CLR プロシージャでない限り、カーソルのプレースホルダーにできます。  
  
 *n*  
 追加のパラメーターの値のプレースホルダーです。 定数または変数のみを指定できます。 値には、関数や演算子を使用して作成された式など、より複雑な式を指定することはできません。  
  
## <a name="return-code-values"></a>リターン コードの値  
 0 (成功) または0以外 (失敗)  
  
## <a name="result-sets"></a>結果セット  
 SQL 文字列に組み込まれているすべての SQL ステートメントからの結果セットを返します。  
  
## <a name="remarks"></a>解説  
 sp_executesql パラメーターは、このトピックの「構文」セクションで説明されているように、特定の順序で入力する必要があります。 パラメーターが順序どおりに入力されていない場合は、エラーメッセージが表示されます。  
  
 sp_executesql は、バッチ、名前の有効範囲、およびデータベース コンテキストに関して、EXECUTE と同じように動作します。 [!INCLUDE[tsql](../../includes/tsql-md.md)]Sp_executesql stmt パラメーターのステートメントまたはバッチ \@ は、sp_executesql ステートメントが実行されるまでコンパイルされません。 次に、stmt の内容を \@ コンパイルして、sp_executesql を呼び出したバッチの実行プランとは別の実行プランとして実行します。 sp_executesql バッチから、sp_executesql を呼び出すバッチ内で宣言されている変数は参照できません。 sp_executesql バッチ内のローカル カーソルまたはローカル変数は、sp_executesql を呼び出すバッチでは認識されません。 データベース コンテキストの変更は、sp_executesql ステートメント終了時まで有効です。  
  
 [!INCLUDE[tsql](../../includes/tsql-md.md)] ステートメントのパラメーター値だけが変わる場合は、ストアド プロシージャの代わりに sp_executesql を使用して、ステートメントを何回でも実行できます。 この場合、パラメーター値が変わるだけで [!INCLUDE[tsql](../../includes/tsql-md.md)] ステートメントそのものは変わらないため、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] クエリ オプティマイザーではステートメントを最初に実行したときに生成した実行プランを再使用できます。  
  
> [!NOTE]  
>  パフォーマンスを向上させるには、ステートメント文字列で完全修飾オブジェクト名を使用します。  
  
 sp_executesql では、次の例に示すように、[!INCLUDE[tsql](../../includes/tsql-md.md)] 文字列とは別にパラメーター値を設定できます。  
  
```sql  
DECLARE @IntVariable INT;  
DECLARE @SQLString NVARCHAR(500);  
DECLARE @ParmDefinition NVARCHAR(500);  
  
/* Build the SQL string one time.*/  
SET @SQLString =  
     N'SELECT BusinessEntityID, NationalIDNumber, JobTitle, LoginID  
       FROM AdventureWorks2012.HumanResources.Employee   
       WHERE BusinessEntityID = @BusinessEntityID';  
SET @ParmDefinition = N'@BusinessEntityID tinyint';  
/* Execute the string with the first parameter value. */  
SET @IntVariable = 197;  
EXECUTE sp_executesql @SQLString, @ParmDefinition,  
                      @BusinessEntityID = @IntVariable;  
/* Execute the same string with the second parameter value. */  
SET @IntVariable = 109;  
EXECUTE sp_executesql @SQLString, @ParmDefinition,  
                      @BusinessEntityID = @IntVariable;  
```  
  
 また、出力パラメーターを sp_executesql で使用することもできます。 次の例では、`AdventureWorks2012.HumanResources.Employee` テーブルから役職名を取得し、それを出力パラメーター `@max_title` に返します。  
  
```sql  
DECLARE @IntVariable INT;  
DECLARE @SQLString NVARCHAR(500);  
DECLARE @ParmDefinition NVARCHAR(500);  
DECLARE @max_title VARCHAR(30);  
  
SET @IntVariable = 197;  
SET @SQLString = N'SELECT @max_titleOUT = max(JobTitle)   
   FROM AdventureWorks2012.HumanResources.Employee  
   WHERE BusinessEntityID = @level';  
SET @ParmDefinition = N'@level TINYINT, @max_titleOUT VARCHAR(30) OUTPUT';  
  
EXECUTE sp_executesql @SQLString, @ParmDefinition, @level = @IntVariable, @max_titleOUT=@max_title OUTPUT;  
SELECT @max_title;  
```  
  
 sp_executesql でパラメーター値を使用すると、EXECUTE ステートメントで文字列を実行する場合と比べて次のような利点があります。  
  
-   sp_executesql 文字列に指定される [!INCLUDE[tsql](../../includes/tsql-md.md)] ステートメントの実際のテキストは実行のたびに変わらないので、クエリ オプティマイザーでは、2 回目の実行で [!INCLUDE[tsql](../../includes/tsql-md.md)] ステートメントと最初の実行時に作成した実行プランが照合される可能性があります。 したがって、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では 2 回目のステートメントをコンパイルする必要がありません。  
  
-   [!INCLUDE[tsql](../../includes/tsql-md.md)]文字列は1回だけ作成されます。  
  
-   整数パラメーターはネイティブ形式で指定します。 Unicode にキャストする必要はありません。  
  
## <a name="permissions"></a>アクセス許可  
 public ロールのメンバーシップが必要です。  
  
## <a name="examples"></a>例  
  
### <a name="a-executing-a-simple-select-statement"></a>A. 簡単な SELECT ステートメントを実行する  
 次の例では、 `SELECT` という名前の埋め込みパラメーターを含む単純なステートメントを作成して実行し `@level` ます。  
  
```sql  
EXECUTE sp_executesql   
          N'SELECT * FROM AdventureWorks2012.HumanResources.Employee   
          WHERE BusinessEntityID = @level',  
          N'@level TINYINT',  
          @level = 109;  
```  
  
### <a name="b-executing-a-dynamically-built-string"></a>B. 動的に作成された文字列の実行  
 次の例では、を使用して `sp_executesql` 、動的に作成された文字列を実行します。 この例で使用するストアド プロシージャでは、特定の年の販売データをパーティション分割するために使用されるテーブル セットにデータを追加します。 年の月ごとに、次の形式のテーブルが1つあります。  
  
```sql  
CREATE TABLE May1998Sales  
    (OrderID INT PRIMARY KEY,  
    CustomerID INT NOT NULL,  
    OrderDate  DATETIME NULL  
        CHECK (DATEPART(yy, OrderDate) = 1998),  
    OrderMonth INT  
        CHECK (OrderMonth = 5),  
    DeliveryDate DATETIME NULL,  
        CHECK (DATEPART(mm, OrderDate) = OrderMonth)  
    )  
```  
  
 この例で使用するストアド プロシージャでは、新規の注文を正しいテーブルに追加する `INSERT` ステートメントを動的に作成し、実行します。 この例では、受注日を使用してデータを格納するテーブルの名前を作成し、この名前を `INSERT` ステートメントに組み込みます。  
  
> [!NOTE]  
>  これは sp_executesql の簡単な使用例です。 この例では、エラー チェックや、テーブル間における注文番号の重複の確認などのビジネス ルール チェックは行いません。  
  
```sql  
CREATE PROCEDURE InsertSales @PrmOrderID INT, @PrmCustomerID INT,  
                 @PrmOrderDate DATETIME, @PrmDeliveryDate DATETIME  
AS  
DECLARE @InsertString NVARCHAR(500)  
DECLARE @OrderMonth INT  
  
-- Build the INSERT statement.  
SET @InsertString = 'INSERT INTO ' +  
       /* Build the name of the table. */  
       SUBSTRING( DATENAME(mm, @PrmOrderDate), 1, 3) +  
       CAST(DATEPART(yy, @PrmOrderDate) AS CHAR(4) ) +  
       'Sales' +  
       /* Build a VALUES clause. */  
       ' VALUES (@InsOrderID, @InsCustID, @InsOrdDate,' +  
       ' @InsOrdMonth, @InsDelDate)'  
  
/* Set the value to use for the order month because  
   functions are not allowed in the sp_executesql parameter  
   list. */  
SET @OrderMonth = DATEPART(mm, @PrmOrderDate)  
  
EXEC sp_executesql @InsertString,  
     N'@InsOrderID INT, @InsCustID INT, @InsOrdDate DATETIME,  
       @InsOrdMonth INT, @InsDelDate DATETIME',  
     @PrmOrderID, @PrmCustomerID, @PrmOrderDate,  
     @OrderMonth, @PrmDeliveryDate  
  
GO  
```  
  
 このプロシージャでは、sp_executesql を使用して文字列を実行しますが、これは EXECUTE を使用する場合と比べて効率的です。 sp_executesql を使用する場合、INSERT 文字列は各月のテーブルごとに 1 つずつ、12 とおり作成されます。 EXECUTE では、パラメーター値が異なるため、各挿入文字列は一意です。 どちらの方法でも作成するバッチの数は同じですが、sp_executesql で作成される INSERT 文字列には類似性があるので、クエリ オプティマイザーで実行プランを再利用しやすくなります。  
  
### <a name="c-using-the-output-parameter"></a>C. OUTPUT パラメーターを使用する  
 次の例では、パラメーターを使用し `OUTPUT` て、ステートメントによって生成された結果セットを `SELECT` パラメーターに格納し `@SQLString` ます。 `SELECT` 次に、パラメーターの値を使用する2つのステートメントが実行され `OUTPUT` ます。  
  
```sql  
USE AdventureWorks2012;  
GO  
DECLARE @SQLString NVARCHAR(500);  
DECLARE @ParmDefinition NVARCHAR(500);  
DECLARE @SalesOrderNumber NVARCHAR(25);  
DECLARE @IntVariable INT;  
SET @SQLString = N'SELECT @SalesOrderOUT = MAX(SalesOrderNumber)  
    FROM Sales.SalesOrderHeader  
    WHERE CustomerID = @CustomerID';  
SET @ParmDefinition = N'@CustomerID INT,  
    @SalesOrderOUT NVARCHAR(25) OUTPUT';  
SET @IntVariable = 22276;  
EXECUTE sp_executesql  
    @SQLString  
    ,@ParmDefinition  
    ,@CustomerID = @IntVariable  
    ,@SalesOrderOUT = @SalesOrderNumber OUTPUT;  
-- This SELECT statement returns the value of the OUTPUT parameter.  
SELECT @SalesOrderNumber;  
-- This SELECT statement uses the value of the OUTPUT parameter in  
-- the WHERE clause.  
SELECT OrderDate, TotalDue  
FROM Sales.SalesOrderHeader  
WHERE SalesOrderNumber = @SalesOrderNumber;  
```  
  
## <a name="examples-sssdwfull-and-sspdw"></a>例: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)]、[!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="d-executing-a-simple-select-statement"></a>D. 簡単な SELECT ステートメントを実行する  
 次の例では、 `SELECT` という名前の埋め込みパラメーターを含む単純なステートメントを作成して実行し `@level` ます。  
  
```sql  
-- Uses AdventureWorks  
  
EXECUTE sp_executesql   
          N'SELECT * FROM AdventureWorksPDW2012.dbo.DimEmployee   
          WHERE EmployeeKey = @level',  
          N'@level TINYINT',  
          @level = 109;  
```  
  
## <a name="see-also"></a>参照  
 [EXECUTE &#40;Transact-SQL&#41;](../../t-sql/language-elements/execute-transact-sql.md)   
 [システム ストアド プロシージャ &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/system-stored-procedures-transact-sql.md)  
  
  
