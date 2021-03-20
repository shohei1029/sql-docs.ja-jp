---
description: SET @local_variable (Transact-SQL)
title: SET @local_variable (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/16/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
dev_langs:
- TSQL
helpviewer_keywords:
- SET @local_variable
- variables [SQL Server], assigning
- SET statement, @local_variable
- local variables [SQL Server]
ms.assetid: d410e06e-061b-4c25-9973-b2dc9b60bd85
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 16e31d92dc20c795ff7770d9f41563b6622dfa76
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104733489"
---
# <a name="set-local_variable-transact-sql"></a>SET @local_variable (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

DECLARE @ を使用して、以前に作成した、指定のローカル変数を設定する *local_variable* ステートメントは、指定された値にします。  
  
![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
SQL Server と Azure SQL Database の構文:

```syntaxsql
SET   
{ @local_variable  
    [ . { property_name | field_name } ] = { expression | udt_name { . | :: } method_name }  
}  
|  
{ @SQLCLR_local_variable.mutator_method  
}  
|  
{ @local_variable  
    {+= | -= | *= | /= | %= | &= | ^= | |= } expression  
}  
|   
  { @cursor_variable =   
    { @cursor_variable | cursor_name   
    | { CURSOR [ FORWARD_ONLY | SCROLL ]   
        [ STATIC | KEYSET | DYNAMIC | FAST_FORWARD ]   
        [ READ_ONLY | SCROLL_LOCKS | OPTIMISTIC ]   
        [ TYPE_WARNING ]   
    FOR select_statement   
        [ FOR { READ ONLY | UPDATE [ OF column_name [ ,...n ] ] } ]   
      }   
    }  
}   
```  
Azure Synapse Analytics と Parallel Data Warehouse の構文:  
```syntaxsql
SET @local_variable { = | += | -= | *= | /= | %= | &= | ^= | |= } expression  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
**@** _local_variable_  
**cursor**、**text**、**ntext**、**image**、または **table** を除く任意の型の変数の名前。 変数名の先頭には 1 つのアット マーク ( **@** ) を指定します。 変数名は[識別子](../../relational-databases/databases/database-identifiers.md)の規則に従っている必要があります。  
  
*property_name*  
ユーザー定義型のプロパティ。  
  
*field_name*  
ユーザー定義型のパブリック フィールド。  
  
*udt_name*  
共通言語ランタイム (CLR) ユーザー定義型の名前。  
  
`{ . | :: }`  
CLR ユーザー定義型のメソッドを指定します。 静的メソッド以外のインスタンス メソッドでは、ピリオド (**.**) を使用します。 静的メソッドでは、2 つのコロン (**::**) を使用します。 CLR ユーザー定義型のメソッド、プロパティ、またはフィールドを呼び出すには、その型に対する EXECUTE 権限が必要です。  
  
_method_name_ **(** _argument_ [ **,** ... *n* ] **)**  
ユーザー定義型のメソッド。このメソッドでは、ある型のインスタンスの状態を変更するために、1 つ以上の引数を受け取ります。 静的メソッドはパブリックであることが必要です。  
  
**@** _SQLCLR_local_variable_  
型がアセンブリに存在する変数。 詳細については、「[共通言語ランタイム &#40;CLR&#41; 統合のプログラミング概念](../../relational-databases/clr-integration/common-language-runtime-clr-integration-programming-concepts.md)」をご覧ください。  
  
*mutator_method*  
オブジェクトの状態を変更できるアセンブリのメソッド。 このメソッドには SQLMethodAttribute.IsMutator が適用されます。  
  
`{ += | -= | *= | /= | %= | &= | ^= | |= }`  
複合代入演算子です。  
  
 +=              加算して代入  
  
 -=               減算して代入  
  
 *=              乗算して代入  
  
 /=              除算して代入  
  
 %=              剰余を代入  
  
 &=              ビットごとの AND 演算を行って代入  
  
 ^=              ビットごとの XOR 演算を行って代入  
  
 |=              ビットごとの OR 演算を行って代入  
  
*式 (expression)*  
任意の有効な[式](../../t-sql/language-elements/expressions-transact-sql.md)。  
  
*cursor_variable*  
カーソル変数の名前を指定します。 ターゲットのカーソル変数が以前に他のカーソルを参照していた場合は、以前の参照は削除されます。  
  
*cursor_name*  
DECLARE CURSOR ステートメントを使用して宣言したカーソルの名前。  
  
CURSOR  
SET ステートメントにカーソルの宣言が含まれることを指定します。  
  
SCROLL  
カーソルがすべての FETCH オプション (FIRST、LAST、NEXT、PRIOR、RELATIVE、ABSOLUTE) をサポートすることを指定します。 SCROLL と FAST_FORWARD を一緒に指定することはできません。  
  
FORWARD_ONLY  
カーソルで、FETCH NEXT オプションだけがサポートされることを指定します。 カーソルは、最初の行から最後の行への一方向にのみ取得されます。 STATIC、KEYSET、DYNAMIC のいずれのキーワードも指定しないで FORWARD_ONLY を指定した場合、カーソルは DYNAMIC として実装されます。 FORWARD_ONLY も SCROLL も指定しなかった場合は、STATIC、KEYSET、または DYNAMIC キーワードを指定しない限り、FORWARD_ONLY が既定値になります。 STATIC、KEYSET、および DYNAMIC カーソルの既定値は SCROLL です。  
  
STATIC  
データの一時コピーを作成するためのカーソルを定義します。作成されるコピーは、カーソルで使用されます。 カーソルに対するすべての要求は、tempdb 内のこの一時テーブルから応答されます。 その結果、カーソルが開かれた後でベース テーブルに対して行われた変更は、カーソルに対して行われるフェッチによって返されるデータには反映されません。 また、このカーソルでは変更はサポートされていません。  
  
KEYSET  
カーソルを開くときに、カーソル内の行の構成要素と順序が固定されることを指定します。 行を一意に識別するキーのセットは、tempdb の keysettable に組み込まれています。 ベース テーブル内にあるキー以外の値に対する変更が、カーソル所有者によって実行されるか、または他のユーザーによってコミットされると、その変更は、カーソル所有者がカーソルの周囲をスクロールするときに表示されます。 他のユーザーによって行われた挿入は表示されません。[!INCLUDE[tsql](../../includes/tsql-md.md)] サーバー カーソルを介して、挿入を行うことはできません。  
  
行が削除された場合に、その行をフェッチしようとすると、@@FETCH_STATUS が -2 に設定されて返されます。 カーソル外部からキー値を更新するのは、古い行を削除した後で新しい行を挿入するのと同じです。 新しい値の行は表示されず、古い値の行をフェッチしようとすると @@FETCH_STATUS が -2 に設定されて返されます。 WHERE CURRENT OF 句を指定してカーソルから更新を行った場合は、新しい値が表示されます。  
  
DYNAMIC  
結果セット内の行に対して行ったすべてのデータ変更を反映するカーソルを定義します。このデータ変更は、カーソル所有者がカーソルの周囲をスクロールするときに行われたものです。 行のデータ値、順序、メンバーシップは、各フェッチ操作で変化する可能性があります。 動的カーソルでは、絶対フェッチ オプションと相対フェッチ オプションはサポートされません。  
  
FAST_FORWARD  
最適化が有効に設定された FORWARD_ONLY、READ_ONLY カーソルを指定します。 SCROLL も指定した場合、FAST_FORWARD は指定できません。  
  
READ_ONLY  
このカーソルによる更新を禁止します。 UPDATE または DELETE ステートメントの WHERE CURRENT OF 句で、このカーソルを参照することはできません。 このオプションは、更新対象のカーソルの既定の機能をオーバーライドします。  
  
SCROLL LOCKS  
カーソルによって行われる位置指定更新または位置指定削除の成功が保証されることを指定します。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] はカーソルに読み取られた行をロックし、後で変更できることを保証します。 FAST_FORWARD を指定した場合、SCROLL_LOCKS は指定できません。  
  
OPTIMISTIC  
行がカーソルに読み取られてから更新された場合に、カーソルによって行われる位置指定更新または位置指定削除が失敗することを指定します。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、行がカーソルに読み取られるとき、その行はロックされません。 代わりに timestamp 列の値を比較するか、テーブルに timestamp 列がない場合はチェックサム値を使用して、行がカーソルに読み込まれてから変更されたかどうかが判別されます。 行が変更されている場合、位置指定更新または位置指定削除の試行は失敗します。 FAST_FORWARD を指定した場合、OPTIMISTIC は指定できません。  
  
TYPE_WARNING  
カーソルの種類が、要求されたものから別のものに暗黙的に変換された場合、クライアントに警告メッセージが送信されることを指定します。  
  
FOR *select_statement*  
カーソルの結果セットを定義する標準の SELECT ステートメントです。 カーソル宣言の *select_statement* 内で FOR BROWSE および INTO キーワードは許可されません。  
  
DISTINCT、UNION、GROUP BY、または HAVING を使用した場合、または *select_list* に集計式が含まれる場合、カーソルは STATIC として作成されます。  
  
基になる各テーブルに一意なインデックスがなく、ISO SCROLL カーソルまたは [!INCLUDE[tsql](../../includes/tsql-md.md)] KEYSET カーソルが要求された場合、カーソルは自動的に STATIC カーソルになります。  
  
*select_statement* に、列が一意な行識別子 (ROWID) になっていない ORDER BY 句が含まれる場合、DYNAMIC カーソルは KEYSET カーソルに変換されます。KEYSET カーソルを開くことができない場合、DYNAMIC カーソルは STATIC カーソルに変換されます。 STATIC キーワードを指定せず、ISO 構文を使用して定義されたカーソルの場合も、このプロセスが行われます。  
  
READ ONLY  
このカーソルによる更新を禁止します。 UPDATE または DELETE ステートメントの WHERE CURRENT OF 句で、このカーソルを参照することはできません。 このオプションは、更新対象のカーソルの既定の機能をオーバーライドします。 このキーワードは、以前は READ_ONLY と表記していましたが、今後は、READ と ONLY の間にアンダースコアではなくスペースを指定するようになりました。  
  
`UPDATE [OF column_name[ ,... n ] ]`  
カーソル内で更新できる列を定義します。 OF *column_name* [**,**...*n*] を指定した場合は、指定した列に対してのみ更新ができます。 一覧を指定しないと、カーソルを READ ONLY として定義していない限り、すべての列を更新できます。  
  
## <a name="remarks"></a>解説  
変数は宣言後、NULL に初期化されます。 宣言された変数に NULL 以外の値を代入するには、SET ステートメントを使用します。 変数に値を代入する SET ステートメントでは、1 つの値が返されます。 複数の変数を初期化する場合は、各ローカル変数に対して 1 つずつ、SET ステートメントを使用してください。  
  
変数は式の内部だけで使用でき、オブジェクト名やキーワードの代わりに使用することはできません。 動的 [!INCLUDE[tsql](../../includes/tsql-md.md)] ステートメントを作成するには、EXECUTE を使用します。  
  
SET **@** _cursor_variable_ の構文規則に、LOCAL キーワードと GLOBAL キーワードは含まれません。 SET **@** _cursor_variable_ = CURSOR... 構文を使用すると、カーソルは default to local cursor データベース オプションの設定に応じて、GLOBAL または LOCAL として作成されます。  
  
カーソル変数は、グローバル カーソルを参照する場合でも、常にローカルです。 カーソル変数でグローバル カーソルを参照する場合、カーソルに対してグローバル カーソル参照とローカル カーソル参照の両方が行われます。 詳細については、例 C を参照してください。  
  
詳細については、「[DECLARE CURSOR &#40;Transact-SQL&#41;](../../t-sql/language-elements/declare-cursor-transact-sql.md)」を参照してください。  
  
複合代入演算子は、変数や、UPDATE、SELECT、および RECEIVE ステートメントの SET など、演算子の右側にある式で代入を行う任意の場所で使用できます。  
  
SELECT ステートメントで、値を連結する目的で (つまり、集計値を計算する目的で) 変数を使用しないでください。 予期しないクエリ結果が生じる可能性があります。 これは、(代入を含め) SELECT リスト内のすべての式は、出力行ごとに 1 回のみ実行されるとは限らないためです。 詳細については、[サポート技術情報の資料](https://www.betaarchive.com/wiki/index.php?title=Microsoft_KB_Archive/287515)を参照してください。  
  
## <a name="permissions"></a>アクセス許可  
public ロールのメンバーシップが必要です。 すべてのユーザーは、SET **@** _local_variable_ を使用できます。  
  
## <a name="examples"></a>例  
  
### <a name="a-printing-the-value-of-a-variable-initialized-by-using-set"></a>A. SET によって初期化された変数の値を出力する  
次の例では、`@myvar` 変数を作成し、文字列値を代入して、`@myvar` 変数の値を出力します。  
  
```sql  
DECLARE @myvar CHAR(20);  
SET @myvar = 'This is a test';  
SELECT @myvar;  
GO  
```  
  
### <a name="b-using-a-local-variable-assigned-a-value-by-using-set-in-a-select-statement"></a>B. SET によって値が代入されたローカル変数を、SELECT ステートメントで使用する  
次の例では、`@state` という名前のローカル変数を作成し、そのローカル変数を `SELECT` ステートメントで使用して、`Oregon` 州に住む全従業員の姓名を検索します。  
  
```sql  
USE AdventureWorks2012;  
GO  
DECLARE @state CHAR(25);  
SET @state = N'Oregon';  
SELECT RTRIM(FirstName) + ' ' + RTRIM(LastName) AS Name, City  
FROM HumanResources.vEmployee  
WHERE StateProvinceName = @state;  
```  
  
### <a name="c-using-a-compound-assignment-for-a-local-variable"></a>C. ローカル変数に対して複合代入を使用する  
次の 2 つの例では、同じ結果が生成されます。 どちらも `@NewBalance` というローカル変数を作成し、その値に 10 を乗算して、`SELECT` ステートメントでローカル変数の新しい値を表示します。 2 番目の例では、複合代入演算子を使用します。  
  
```sql  
/* Example one */  
DECLARE  @NewBalance  INT ;  
SET  @NewBalance  =  10;  
SET  @NewBalance  =  @NewBalance  *  10;  
SELECT  @NewBalance;  
  
/* Example Two */  
DECLARE @NewBalance INT = 10;  
SET @NewBalance *= 10;  
SELECT @NewBalance;  
```  
  
### <a name="d-using-set-with-a-global-cursor"></a>D. グローバル カーソルに対して SET を使用する  
次の例では、ローカル変数を作成した後、カーソル変数をグローバル カーソル名に設定します。  
  
```sql  
DECLARE my_cursor CURSOR GLOBAL   
FOR SELECT * FROM Purchasing.ShipMethod  
DECLARE @my_variable CURSOR ;  
SET @my_variable = my_cursor ;   
--There is a GLOBAL cursor declared(my_cursor) and a LOCAL variable  
--(@my_variable) set to the my_cursor cursor.  
DEALLOCATE my_cursor;   
--There is now only a LOCAL variable reference  
--(@my_variable) to the my_cursor cursor.  
```  
  
### <a name="e-defining-a-cursor-by-using-set"></a>E. SET を使用してカーソルを定義する  
次の例では、`SET` ステートメントを使用してカーソルを定義します。  
  
```sql  
DECLARE @CursorVar CURSOR;  
  
SET @CursorVar = CURSOR SCROLL DYNAMIC  
FOR  
SELECT LastName, FirstName  
FROM AdventureWorks2012.HumanResources.vEmployee  
WHERE LastName like 'B%';  
  
OPEN @CursorVar;  
  
FETCH NEXT FROM @CursorVar;  
WHILE @@FETCH_STATUS = 0  
BEGIN  
    FETCH NEXT FROM @CursorVar  
END;  
  
CLOSE @CursorVar;  
DEALLOCATE @CursorVar;  
```  
  
### <a name="f-assigning-a-value-from-a-query"></a>F. クエリから値を代入する  
次の例では、クエリを使用して変数に値を代入します。  
  
```sql  
USE AdventureWorks2012;  
GO  
DECLARE @rows INT;  
SET @rows = (SELECT COUNT(*) FROM Sales.Customer);  
SELECT @rows;  
```  
  
### <a name="g-assigning-a-value-to-a-user-defined-type-variable-by-modifying-a-property-of-the-type"></a>G. ユーザー定義型のプロパティを変更してユーザー定義型変数に値を代入する  
次の例では、ユーザー定義型の `Point` に対して `X` プロパティの値を変更し、値を設定します。  
  
```sql  
DECLARE @p Point;  
SET @p.X = @p.X + 1.1;  
SELECT @p;  
GO  
```  
  
### <a name="h-assigning-a-value-to-a-user-defined-type-variable-by-invoking-a-method-of-the-type"></a>H. ユーザー定義型のメソッドを起動してユーザー定義型変数に値を代入する  
次の例では、ユーザー定義型の **point** に対して `SetXY` メソッドを起動し、値を設定します。  
  
```sql  
DECLARE @p Point;  
SET @p=point.SetXY(23.5, 23.5);  
```  
  
### <a name="i-creating-a-variable-for-a-clr-type-and-calling-a-mutator-method"></a>I. CLR 型の変数を作成してミューテーター メソッドを呼び出す  
次の例では、`Point` 型の変数を作成し、`Point` のミューテーター メソッドを実行します。  
  
```sql  
CREATE ASSEMBLY mytest FROM 'c:\test.dll' WITH PERMISSION_SET = SAFE  
CREATE TYPE Point EXTERNAL NAME mytest.Point  
GO  
DECLARE @p Point = CONVERT(Point, '')  
SET @p.SetXY(22, 23);  
```  
  
## <a name="examples-sssdwfull-and-sspdw"></a>例: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)]、[!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="j-printing-the-value-of-a-variable-initialized-by-using-set"></a>J. SET によって初期化された変数の値を出力する  
次の例では、`@myvar` 変数を作成し、文字列値を代入して、`@myvar` 変数の値を出力します。  
  
```sql  
DECLARE @myvar CHAR(20);  
SET @myvar = 'This is a test';  
SELECT TOP 1 @myvar FROM sys.databases;
```  
  
### <a name="k-using-a-local-variable-assigned-a-value-by-using-set-in-a-select-statement"></a>K. SET によって値が代入されたローカル変数を、SELECT ステートメントで使用する  
次の例では、`@dept` という名前のローカル変数を作成し、このローカル変数を `SELECT` ステートメントで使用して、`Marketing` 部門で働く全従業員の姓名を検索します。  
  
```sql  
-- Uses AdventureWorks  
  
DECLARE @dept CHAR(25);  
SET @dept = N'Marketing';  
SELECT RTRIM(FirstName) + ' ' + RTRIM(LastName) AS Name  
FROM DimEmployee   
WHERE DepartmentName = @dept;  
```  
  
### <a name="l-using-a-compound-assignment-for-a-local-variable"></a>L. ローカル変数に対して複合代入を使用する  
次の 2 つの例では、同じ結果が生成されます。 どちらも `@NewBalance` というローカル変数を作成し、その値に `10` を乗算して、`SELECT` ステートメントでローカル変数の新しい値を表示します。 2 番目の例では、複合代入演算子を使用します。  
  
```sql  
/* Example one */  
DECLARE  @NewBalance INT;  
SET  @NewBalance  =  10;  
SET  @NewBalance  =  @NewBalance  *  10;  
SELECT TOP 1 @NewBalance FROM sys.tables;  
  
/* Example Two */  
DECLARE @NewBalance INT = 10;  
SET @NewBalance *= 10;  
SELECT TOP 1 @NewBalance FROM sys.tables;  
```  
  
### <a name="m-assigning-a-value-from-a-query"></a>M. クエリから値を代入する  
次の例では、クエリを使用して変数に値を代入します。  
  
```sql  
-- Uses AdventureWorks  
  
DECLARE @rows INT;  
SET @rows = (SELECT COUNT(*) FROM dbo.DimCustomer);  
SELECT TOP 1 @rows FROM sys.tables;  
```  
  
## <a name="see-also"></a>参照  
[複合演算子 &#40;Transact-SQL&#41;](../../t-sql/language-elements/compound-operators-transact-sql.md)   
[DECLARE @local_variable &#40;Transact-SQL&#41;](../../t-sql/language-elements/declare-local-variable-transact-sql.md)   
[EXECUTE &#40;Transact-SQL&#41;](../../t-sql/language-elements/execute-transact-sql.md)   
[SELECT &#40;Transact-SQL&#41;](../../t-sql/queries/select-transact-sql.md)   
[SET ステートメント &#40;Transact-SQL&#41;](../../t-sql/statements/set-statements-transact-sql.md)  
  
  

