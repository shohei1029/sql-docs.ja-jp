---
description: UPDATE (Transact-SQL)
title: UPDATE (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 05/19/2020
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- UPDATE_TSQL
- UPDATE
dev_langs:
- TSQL
helpviewer_keywords:
- DML [SQL Server], UPDATE statement
- data updates [SQL Server], UPDATE statement
- updating data [SQL Server]
- TOP clause, UPDATE
- OUTPUT clause
- large value data types
- data manipulation language [SQL Server], UPDATE statement
- user-defined types [SQL Server], modifying
- INSTEAD OF triggers
- modifying data [SQL Server], UPDATE statement
- data modifications [SQL Server], UPDATE statement
- large data, modifying
- .WRITE clause
- table modifications [SQL Server], UPDATE statement
- UDTs [SQL Server], modifying
- UPDATE statement [SQL Server], about UPDATE statement
- LOB data [SQL Server], modifying
- modifying LOB data
- UPDATE statement [SQL Server]
- FROM clause, UPDATE statement
- WHERE clause, UPDATE statement
ms.assetid: 40e63302-0c68-4593-af3e-6d190181fee7
author: VanMSFT
ms.author: vanto
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 55d98ecc2024d08cd745828b47ebbd04e82b47fd
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104751512"
---
# <a name="update-transact-sql"></a>UPDATE (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  [!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] のテーブルまたはビュー内の既存のデータを変更します。 例については、「[例](#UpdateExamples)」を参照してください。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql  
-- Syntax for SQL Server and Azure SQL Database  

[ WITH <common_table_expression> [...n] ]  
UPDATE   
    [ TOP ( expression ) [ PERCENT ] ]   
    { { table_alias | <object> | rowset_function_limited   
         [ WITH ( <Table_Hint_Limited> [ ...n ] ) ]  
      }  
      | @table_variable      
    }  
    SET  
        { column_name = { expression | DEFAULT | NULL }  
          | { udt_column_name.{ { property_name = expression  
                                | field_name = expression }  
                                | method_name ( argument [ ,...n ] )  
                              }  
          }  
          | column_name { .WRITE ( expression , @Offset , @Length ) }  
          | @variable = expression  
          | @variable = column = expression  
          | column_name { += | -= | *= | /= | %= | &= | ^= | |= } expression  
          | @variable { += | -= | *= | /= | %= | &= | ^= | |= } expression  
          | @variable = column { += | -= | *= | /= | %= | &= | ^= | |= } expression  
        } [ ,...n ]   
  
    [ <OUTPUT Clause> ]  
    [ FROM{ <table_source> } [ ,...n ] ]   
    [ WHERE { <search_condition>   
            | { [ CURRENT OF   
                  { { [ GLOBAL ] cursor_name }   
                      | cursor_variable_name   
                  }   
                ]  
              }  
            }   
    ]   
    [ OPTION ( <query_hint> [ ,...n ] ) ]  
[ ; ]  
  
<object> ::=  
{   
    [ server_name . database_name . schema_name .   
    | database_name .[ schema_name ] .   
    | schema_name .  
    ]  
    table_or_view_name}  
```  
  
```syntaxsql 
-- Syntax for Azure Synapse Analysis

[ WITH <common_table_expression> [ ,...n ] ]
UPDATE [ database_name . [ schema_name ] . | schema_name . ] table_name
SET { column_name = { expression | NULL } } [ ,...n ]  
FROM [ database_name . [ schema_name ] . | schema_name . ] table_name   
JOIN {<join_table_source>}[ ,...n ] 
ON <join_condition>
[ WHERE <search_condition> ]   
[ OPTION ( LABEL = label_name ) ]  
[;]  

<join_table_source> ::=   
{  
    [ database_name . [ schema_name ] . | schema_name . ] table_or_view_name [ AS ] table_or_view_alias 
    [ <tablesample_clause>]  
    | derived_table [ AS ] table_alias [ ( column_alias [ ,...n ] ) ]  
}  
```

```syntaxsql
-- Syntax for Parallel Data Warehouse

UPDATE [ database_name . [ schema_name ] . | schema_name . ] table_name   
SET { column_name = { expression | NULL } } [ ,...n ]  
[ FROM from_clause ]  
[ WHERE <search_condition> ]   
[ OPTION ( LABEL = label_name ) ]  
[;]  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
 WITH \<common_table_expression>  
 UPDATE ステートメントのスコープ内で定義された、一時的な名前付き結果セットまたはビュー (共通テーブル式 (CTE) とも呼ばれる) を指定します。 CTE 結果セットは単純なクエリから派生し、UPDATE ステートメントで参照されます。  
  
 共通テーブル式は、SELECT、INSERT、DELETE、CREATE VIEW の各ステートメントでも使用できます。 詳細については、「[WITH common_table_expression &#40;Transact-SQL&#41;](../../t-sql/queries/with-common-table-expression-transact-sql.md)」を参照してください。  
  
 TOP **(** _expression_ **)** [ PERCENT ]  
 更新する行の数または比率 (%) を指定します。 *expression* は行数または行の比率 (%) にすることができます。  
  
 INSERT、UPDATE、または DELETE を使用する TOP 式で参照される行は、順序付けされません。  
  
 INSERT、UPDATE、DELETE の各ステートメントで TOP を使用する場合は、*expression* を区切るかっこが必要です。 詳細については、「[TOP &#40;Transact-SQL&#41;](../../t-sql/queries/top-transact-sql.md)」を参照してください。  
  
 *table_alias*  
 FROM 句で指定される別名です。行を更新するテーブルまたはビューを表します。  
  
 *server_name*  
 テーブルまたはビューがあるサーバー名 (リンクされたサーバー名またはサーバー名として [OPENDATASOURCE](../../t-sql/functions/opendatasource-transact-sql.md) 関数を使用) です。 *server_name* が指定されている場合、*database_name* と *schema_name* が必要です。  
  
 *database_name*  
 データベースの名前です。  
  
 *schema_name*  
 テーブルまたはビューが属するスキーマの名前を指定します。  
  
 *table_or_view_name*  
 行を更新するテーブルまたはビューの名前です。 *table_or_view_name* によって参照されるビューは更新可能であることが必要です。また、そのビューの FROM 句ではベース テーブルを 1 つだけ参照している必要があります。 更新可能なビューの詳細については、「[CREATE VIEW &#40;Transact-SQL&#41;](../../t-sql/statements/create-view-transact-sql.md)」を参照してください。  
  
 *rowset_function_limited*  
 プロバイダーの機能によって、 [OPENQUERY](../../t-sql/functions/openquery-transact-sql.md) 関数または [OPENROWSET](../../t-sql/functions/openrowset-transact-sql.md) 関数のいずれかになります。  
  
 WITH **(** \<Table_Hint_Limited> **)**  
 対象のテーブルに設定可能なテーブル ヒントを 1 つ以上指定します。 キーワード WITH とかっこが必要です。 NOLOCK、READUNCOMMITTED、および NOEXPAND は指定できません。 テーブル ヒントの詳細については、「[テーブル ヒント &#40;Transact-SQL&#41;](../../t-sql/queries/hints-transact-sql-table.md)」を参照してください。  
  
 @*table_variable*  
 [table](../../t-sql/data-types/table-transact-sql.md) 変数をテーブル ソースとして指定します。  
  
 SET  
 更新する列名または変数名の一覧を指定します。  
  
 *column_name*  
 変更するデータを含む列です。 *column_name* は *table_or view_name* 内に存在する必要があります。 ID 列は更新できません。  
  
 *式 (expression)*  
 変数、リテラル値、式、または 1 つの値を返すかっこで囲んだサブセレクト ステートメントです。 *expression* で返される値で *column_name* または @*variable* の既存の値が置き換えられます。  
  
> [!NOTE]  
>  Unicode 文字データ型 **nchar**、**nvarchar**、**ntext** を参照している場合は、'expression' の前に大文字の 'N' を付ける必要があります。 'N' が指定されていない場合、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、文字列はデータベースまたは列の既定の照合順序に対応するコード ページに変換されます。 文字列がこのコード ページにない場合は、失われます。  
  
 DEFAULT  
 列に格納された値を列に定義された既定値で置き換えることを指定します。 列に既定値が定義されておらず、NULL 値が許されている場合は、この句を使用して列を NULL に変更できます。  
  
 { **+=** \| **-=** \| **\*=** \| **/=** \| **%=** \| **&=** \| **^=** \| **|=** }  
 複合代入演算子です。  
 +=                       加算して、割り当てる  
 -=                        減算して代入  
 *=                        乗算して代入  
 /=                         除算して代入  
 %=                       剰余を代入  
 &=                        ビットごとの AND 演算を行って代入  
 ^=                        ビットごとの XOR と割り当て  
 |=                         ビットごとの OR と割り当て  
  
 *udt_column_name*  
 ユーザー定義型の列です。  
  
 *property_name* | *field_name*  
 ユーザー定義型のパブリック プロパティまたはパブリック データ メンバーです。  
  
 *method_name* **(** *argument* [ **,** ... *n*] **)**  
 1 つ以上の引数を使用する *udt_column_name* の静的でないパブリック ミューテーター メソッドです。  
  
 **.** WRITE **(** _expression_ **,** @_Offset_ **,** @_Length_ **)**  
 *column_name* の値のセクションを変更することを指定します。 *expression* で *column_name* の @*Offset* から始まる @*Length* 単位が置き換えられます。 **varchar(max)** 、**nvarchar(max)** 、**varbinary(max)** の列だけこの句で指定できます。 *column_name* では NULL 値は許容されません。また、テーブル名やテーブル別名で修飾することもできません。  
  
 *expression* は *column_name* にコピーされる値です。 *expression* は、*column_name* 型に評価されるか、この型に暗黙的にキャストできる必要があります。 *expression* に NULL が設定されている場合、@*Length* は無視され、*column_name* 内の値は指定された @*Offset* で切り捨てられます。  
  
 @*Offset* は、*expression* が書き込まれる、*column_name* に格納されている値の開始位置です。 @*Offset* は、0 から始まる序数バイトの位置です。**bigint** で、負の数は指定できません。 @*Offset* が NULL の場合、更新操作により *expression* は既存の *column_name* 値の最後に追加され、@*Length* は無視されます。 @*Offset* が *column_name* 値のバイト長よりも大きい場合は、[!INCLUDE[ssDE](../../includes/ssde-md.md)] によってエラーが返されます。 *Offset* と @*Length* の和が列の基になる値の終点を超える場合、値の最後の文字までが削除されます。  
  
 @*Length* は列内のセクションの長さです。このセクションは @*Offset* から始まり、*expression* で置き換えられます。 @*Length* は **bigint** で、負の数は指定できません。 @*Length* が NULL の場合、更新操作により *column_name* の値の @*Offset* から最後までのすべてのデータが削除されます。  
  
 詳細については、「[大きな値のデータ型を更新する](#updating-lobs)」を参照してください。  
  
 **@** *variable*  
 *expression* で返される値を設定する、宣言された変数です。  
  
 SET **@** _variable_ = *column* = *expression* は、列と同じ値に変数を設定します。 一方、SET **@** _variable_ = _column_, _column_ = _expression_ は、列の更新前の値に変数を設定します。  
  
 \<OUTPUT_Clause>  
 更新されたデータまたはそれに基づく式を、UPDATE 操作の一部として返します。 OUTPUT 句は、リモート テーブルまたはリモート ビューを対象とした DML ステートメントではサポートされません。 この句の引数と動作について詳しくは、「[OUTPUT 句 &#40;Transact-SQL&#41;](../../t-sql/queries/output-clause-transact-sql.md)」をご覧ください。  
  
 FROM \<table_source>  
 別のテーブル、ビュー、または派生テーブルのソースを使用して更新操作の基になる値を提供することを指定します。 詳細については、「[FROM &#40;Transact-SQL&#41;](../../t-sql/queries/from-transact-sql.md)」を参照してください。  
  
 更新対象のオブジェクトが FROM 句で指定されたオブジェクトと同じで、FROM 句にそのオブジェクトへの参照が 1 つしかない場合、オブジェクトの別名は指定しても指定しなくてもかまいません。 更新対象のオブジェクトが FROM 句に 2 つ以上含まれている場合、そのオブジェクトへの単独の参照でテーブルの別名を指定してはなりません。 FROM 句にあるオブジェクトへの他のすべての参照に、オブジェクトの別名を含める必要があります。  
  
 INSTEAD OF UPDATE トリガーを伴うビューは、FROM 句を伴う UPDATE の対象にはなりません。  
  
> [!NOTE]  
>  FROM 句での OPENDATASOURCE、OPENQUERY、または OPENROWSET の呼び出しは、更新の対象として使用されるこれらの関数の呼び出しとは別に評価されます。これは、両方の呼び出しに同じ引数が指定されている場合にも当てはまります。 特に、いずれか一方の呼び出しの結果に適用されるフィルター条件または結合条件は、もう一方の結果に影響しません。  
  
 WHERE  
 更新する行を制限する条件を指定します。 WHERE 句で使用される形式に基づいて、更新には 2 種類の形式があります。  
  
-   検索更新では、削除する行を識別する検索条件を指定します。  
  
-   位置指定更新では、CURRENT OF 句を使用してカーソルを指定します。 更新操作は、カーソルの現在位置で行われます。  
  
\<search_condition>  
 更新の対象となる行の条件を指定します。 検索条件を結合の基準条件にすることもできます。 検索条件に含まれる述語の数に制限はありません。 述語と検索条件の詳細については、「[検索条件 &#40;Transact-SQL&#41;](../../t-sql/queries/search-condition-transact-sql.md)」を参照してください。  
  
CURRENT OF  
 指定したカーソルの現在位置で更新を行うことを指定します。  
  
 WHERE CURRENT OF 句を使用する位置指定更新では、カーソルの現在位置にある 1 行を更新します。 位置指定更新は、WHERE \<search_condition> 句を使用して更新する行を識別する検索更新よりも正確です。 検索更新は、検索条件が特定の行を識別しない場合に複数の行を変更します。  
  
GLOBAL  
 *cursor_name* でグローバル カーソルを参照することを指定します。  
  
*cursor_name*  
 フェッチが行われる、開いているカーソルの名前です。 *cursor_name* という名前のグローバル カーソルとローカル カーソルの両方がある場合、GLOBAL を指定すると、この引数はグローバル カーソルを参照します。GLOBAL を指定しないと、この引数はローカル カーソルを参照します。 カーソルは、更新可能である必要があります。  
  
*cursor_variable_name*  
 カーソル変数の名前を指定します。 *cursor_variable_name* は、更新可能なカーソルを参照する必要があります。  
  
OPTION **(** \<query_hint> [ **,** ... *n* ] **)**  
 オプティマイザー ヒントを使用して、[!INCLUDE[ssDE](../../includes/ssde-md.md)]がステートメントを処理する方法をカスタマイズすることを指定します。 詳細については、「[クエリ ヒント &#40;Transact-SQL&#41;](../../t-sql/queries/hints-transact-sql-query.md)」を参照してください。  
  
## <a name="best-practices"></a>ベスト プラクティス  
 `@@ROWCOUNT` 関数を使用して、クライアント アプリケーションに挿入される行数を返します。 詳細については、「[@@ROWCOUNT &#40;Transact-SQL&#41;](../../t-sql/functions/rowcount-transact-sql.md)」を参照してください。  
  
 影響を受ける古い値と新しい値を示すために、UPDATE ステートメントの中で変数名を使用することは可能です。ただし、これは UPDATE ステートメントによって影響を受けるのが単一のレコードである場合のみに限定されています。 UPDATE ステートメントで複数のレコードが影響を受ける場合に、各レコードの古い値と新しい値を返すためには、[OUTPUT 句](../../t-sql/queries/output-clause-transact-sql.md)を使用してください。  
  
 FROM 句を指定して更新操作の条件を設定するときは注意が必要です。 ステートメントには、1 つだけを重視する方法は対象の各列が更新されるとするかどうか、UPDATE ステートメントでは決定的であるに使用できるように指定されていない FROM 句が含まれている場合、UPDATE ステートメントの結果は未定義です。 たとえば、次のスクリプトの UPDATE ステートメントでは、`Table1` のどちらの行も UPDATE ステートメントの FROM 句の条件を満たしています。ただし、`Table1` のどちらの行を使用して `Table2.` の行を更新するかは未定義です。  
  
```sql  
USE AdventureWorks2012;  
GO  
IF OBJECT_ID ('dbo.Table1', 'U') IS NOT NULL  
    DROP TABLE dbo.Table1;  
GO  
IF OBJECT_ID ('dbo.Table2', 'U') IS NOT NULL  
    DROP TABLE dbo.Table2;  
GO  
CREATE TABLE dbo.Table1   
    (ColA INT NOT NULL, ColB DECIMAL(10,3) NOT NULL);  
GO  
CREATE TABLE dbo.Table2   
    (ColA INT PRIMARY KEY NOT NULL, ColB DECIMAL(10,3) NOT NULL);  
GO  
INSERT INTO dbo.Table1 VALUES(1, 10.0), (1, 20.0);  
INSERT INTO dbo.Table2 VALUES(1, 0.0);  
GO  
UPDATE dbo.Table2   
SET dbo.Table2.ColB = dbo.Table2.ColB + dbo.Table1.ColB  
FROM dbo.Table2   
    INNER JOIN dbo.Table1   
    ON (dbo.Table2.ColA = dbo.Table1.ColA);  
GO  
SELECT ColA, ColB   
FROM dbo.Table2;  
```  
  
 `FROM` 句と `WHERE CURRENT OF` 句を組み合わせた場合にも同じ問題が発生します。 次の例では、`Table2` のどちらの行も `FROM` ステートメントの `UPDATE` 句の条件を満たします。 `Table2` のどちらの列を使用してテーブル `Table1` の行を更新するかは未定義です。  
  
```sql  
USE AdventureWorks2012;  
GO  
IF OBJECT_ID ('dbo.Table1', 'U') IS NOT NULL  
    DROP TABLE dbo.Table1;  
GO  
IF OBJECT_ID ('dbo.Table2', 'U') IS NOT NULL  
    DROP TABLE dbo.Table2;  
GO  
CREATE TABLE dbo.Table1  
    (c1 INT PRIMARY KEY NOT NULL, c2 INT NOT NULL);  
GO  
CREATE TABLE dbo.Table2  
    (d1 INT PRIMARY KEY NOT NULL, d2 INT NOT NULL);  
GO  
INSERT INTO dbo.Table1 VALUES (1, 10);  
INSERT INTO dbo.Table2 VALUES (1, 20), (2, 30);  
GO  
DECLARE abc CURSOR LOCAL FOR  
    SELECT c1, c2   
    FROM dbo.Table1;  
OPEN abc;  
FETCH abc;  
UPDATE dbo.Table1   
SET c2 = c2 + d2   
FROM dbo.Table2   
WHERE CURRENT OF abc;  
GO  
SELECT c1, c2 FROM dbo.Table1;  
GO  
```  
  
## <a name="compatibility-support"></a>互換性サポート  
 FROM 句を UPDATE または DELETE ステートメントの対象テーブルに適用する場合、この句での READUNCOMMITTED ヒントと NOLOCK ヒントの使用は、将来のバージョンの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] でサポートされなくなる予定です。 新しい開発作業ではこのコンテキストでのヒントの使用を避け、現在このヒントを使用しているアプリケーションは変更を検討してください。  
  
## <a name="data-types"></a>データ型  
 **char** 列と **nchar** 列は、すべて定義された長さになるまで右側に空白が埋め込まれます。  
  
 ANSI_PADDING を OFF に設定した場合、スペースだけの文字列を除いて、**varchar** 列と **nvarchar** 列に挿入したデータからは後続のスペースがすべて削除されます。 スペースだけで構成される文字列は空の文字列に切り捨てられます。 ANSI_PADDING を ON に設定すると、後続にスペースが挿入されます。 Microsoft SQL Server ODBC ドライバーおよび OLE DB Provider for SQL Server は、接続するたびに自動的に SET ANSI_PADDING を ON にします。 これは、ODBC データ ソースで構成するか、または接続属性やプロパティで設定することができます。 詳細については、「[SET ANSI_PADDING &#40;Transact-SQL&#41;](../../t-sql/statements/set-ansi-padding-transact-sql.md)」を参照してください。  
  
### <a name="updating-text-ntext-and-image-columns"></a>text、ntext、image の列を更新する  
 UPDATE で **text**、**ntext**、**image** 型の列を変更する場合、NULL で列を更新しない限り、列が初期化され、有効なテキスト ポインターが割り当てられます。また、少なくとも 1 つのデータ ページが割り当てられます。  
  
 **text**、**ntext**、**image** 型のデータの大きな部分を置換または変更するには、UPDATE ステートメントではなく [WRITETEXT](../../t-sql/queries/writetext-transact-sql.md) または [UPDATETEXT](../../t-sql/queries/updatetext-transact-sql.md) ステートメントを使用してください。  
  
 クラスター化キーと 1 つ以上の **text**、**ntext**、または **image** 列の両方を更新しているときに、UPDATE ステートメントで複数の行を変更する場合は、これらの列に対する部分更新は、値の完全な置き換えとして実行されます。  
  
> [!IMPORTANT]
>  **ntext**、**text**、**image** データ型は、将来の [!INCLUDE[msCoName](../../includes/msconame-md.md)][!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] バージョンで削除される予定です。 新しい開発作業では、これらのデータ型の使用は避け、現在これらのデータ型を使用しているアプリケーションは修正するようにしてください。 代わりに、 [nvarchar(max)](../../t-sql/data-types/nchar-and-nvarchar-transact-sql.md)、 [varchar(max)](../../t-sql/data-types/char-and-varchar-transact-sql.md)、 [varbinary(max)](../../t-sql/data-types/binary-and-varbinary-transact-sql.md) を使用してください。  
  
### <a name="updating-large-value-data-types"></a><a name="updating-lobs"></a> 大きな値のデータ型を更新する  
 **.** WRITE **(** _expression_ **,** @_Offset_ **,** @_Length_ **)** 句を使用し、**varchar(max)** 、**nvarchar(max)** 、**varbinary(max)** データ型の部分または完全更新を実行します。 
 
 たとえば、**varchar(max)** 列の部分的な更新では、列の最初の 200 バイト (ASCII 文字を使用する場合は 200 文字) だけが削除または変更される可能性がありますが、完全な更新では、列のすべてのデータが削除または変更されます。 データベースの復旧モデルに一括ログ復旧モデルまたは単純復旧モデルが設定されている場合、 **.WRITE** で新しいデータを挿入または追加する際には、最小限しかログに記録されません。 既存の値を更新するときには、最小限のログ記録は使用されません。 詳細については、「 [トランザクション ログ &#40;SQL Server&#41;](../../relational-databases/logs/the-transaction-log-sql-server.md)」を参照してください。  
  
 [!INCLUDE[ssDE](../../includes/ssde-md.md)] では、UPDATE ステートメントで次のいずれかのアクションが発生するとき、部分更新が完全更新に変更されます。  
-   パーティション ビューまたはパーティション テーブルのキー列が変更される場合。  
-   複数の行が変更され、定数以外の値に対する一意でないクラスター化インデックスのキーも更新される場合。  
  
**.WRITE** 句を使用して NULL 列を更新したり、*column_name* の値を NULL に設定したりすることはできません。  
  
@*Offset* と @*Length* は、**varbinary** と **varchar** データ型の場合はバイト数で、**nvarchar** データ型の場合はバイト ペアで指定します。 文字列データ型の長さの詳細については、「[char および varchar (Transact-SQL)](../../t-sql/data-types/char-and-varchar-transact-sql.md)」および「[nchar および nvarchar (Transact-SQL)](../../t-sql/data-types/nchar-and-nvarchar-transact-sql.md)」を参照してください。
  
最高のパフォーマンスが得られるよう、8,040 バイトの倍数の単位でデータを挿入または更新することをお勧めします。  
  
**\.WRITE** 句で変更される列が OUTPUT 句で参照されている場合は、列の完全な値 (**deleted.** _column\_name_ の前イメージまたは **inserted.** _column\_name_ の後イメージのどちらか) が、テーブル変数内の指定された列に返されます。 後述する例 R を参照してください。  
  
他の文字型またはバイナリ データ型で **\.WRITE** と同じ機能を実現するには、[STUFF &#40;Transact-SQL&#41;](../../t-sql/functions/stuff-transact-sql.md) を使用します。  
  
### <a name="updating-user-defined-type-columns"></a>ユーザー定義型の列を更新する  
 ユーザー定義型の列の値を更新するには、次のいずれかの方法を使用します。  
  
-   ユーザー定義型で [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] システム データ型からの暗黙的または明示的な変換がサポートされている場合は、そのシステム データ型の値を指定します。 次の例は、文字列からの明示的な変換によって、ユーザー定義型 `Point` の列の値を更新する方法を示します。  
  
    ```sql  
    UPDATE Cities  
    SET Location = CONVERT(Point, '12.3:46.2')  
    WHERE Name = 'Anchorage';  
    ```  
  
-   ユーザー定義型のミューテーターとしてマークされたメソッドを呼び出して更新を行います。 次の例では、`Point` 型の `SetXY` というミューテーター メソッドを呼び出します。 これにより、その型のインスタンスの状態が更新されます。  
  
    ```sql  
    UPDATE Cities  
    SET Location.SetXY(23.5, 23.5)  
    WHERE Name = 'Anchorage';  
    ```  
  
    > [!NOTE]  
    > [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、ミューテーター メソッドを [!INCLUDE[tsql](../../includes/tsql-md.md)] NULL 値で呼び出した場合や、ミューテーター メソッドにより生成された新しい値が NULL である場合、エラーが返されます。  
  
-   ユーザー定義型の登録済みプロパティまたはパブリック データ メンバーの値を変更します。 値を指定する式は、プロパティの型に暗黙的に変換できる必要があります。 次の例では、ユーザー定義型 `X` のプロパティ `Point` の値を変更します。  
  
    ```sql  
    UPDATE Cities  
    SET Location.X = 23.5  
    WHERE Name = 'Anchorage';  
    ```  
  
     同一のユーザー定義型の列のプロパティを複数変更するには、複数の UPDATE ステートメントを実行するか、その型のミューテーター メソッドを呼び出します。  
  
### <a name="updating-filestream-data"></a>FILESTREAM データを更新する  
 UPDATE ステートメントを使用すると、FILESTREAM フィールドを NULL 値、空の値、または比較的少量のインライン データに更新できます。 ただし、大量のデータをファイルにストリーミングする場合は、Win32 インターフェイスを使用する方が効率的です。 FILESTREAM フィールドを更新すると、その基となるファイル システムの BLOB データが変更されます。 FILESTREAM フィールドを NULL に設定すると、フィールドに関連付けられている BLOB データが削除されます。 次のように使用できません。WRITE() は、FILESTREAM データへの部分更新を実行します。 詳細については、「[FILESTREAM &#40;SQL Server&#41;](../../relational-databases/blob/filestream-sql-server.md)」をご覧ください。  
  
## <a name="error-handling"></a>エラー処理  
 行の更新が制約やルールに違反する場合、列の NULL 値の設定に違反する場合、または新しい値が互換性のないデータ型の場合には、ステートメントは取り消され、エラーが返されます。レコードは更新されません。  
  
 式の評価時に UPDATE ステートメントが算術エラー (オーバーフロー、0 による除算、ドメイン エラー) を検出した場合、更新は行われません。 バッチの残りの部分は実行されず、エラー メッセージが返されます。  
  
 クラスター化インデックスに関係する列を更新した結果、クラスター化インデックスと行のサイズが 8,060 バイトを超える場合、更新は失敗し、エラー メッセージが返されます。  
  
## <a name="interoperability"></a>相互運用性  
 UPDATE ステートメントをユーザー定義関数の本文で使用できるのは、変更対象のテーブルがテーブル変数の場合だけです。  
  
 `INSTEAD OF` トリガーが、テーブルに対する UPDATE 操作で定義されている場合は、UPDATE ステートメントの代わりにそのトリガーが実行されます。 以前のバージョンの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、UPDATE およびその他のデータ変更ステートメントでサポートされているのは AFTER トリガーのみです。 FROM 句は、`INSTEAD OF` トリガーが定義されているビューを直接または間接的に参照する UPDATE ステートメントでは指定できません。 INSTEAD OF トリガーの詳細については、「[CREATE TRIGGER &#40;Transact-SQL&#41;](../../t-sql/statements/create-trigger-transact-sql.md)」を参照してください。  
  
## <a name="limitations-and-restrictions"></a>制限事項と制約事項  
 FROM 句は、`INSTEAD OF` トリガーが定義されているビューを直接または間接的に参照する UPDATE ステートメントでは指定できません。 `INSTEAD OF` トリガーの詳細については、「[CREATE TRIGGER &#40;Transact-SQL&#41;](../../t-sql/statements/create-trigger-transact-sql.md)」を参照してください。  
  
 共通テーブル式 (CTE) が UPDATE ステートメントの対象である場合、ステートメント内の CTE に対するすべての参照を一致させる必要があります。 たとえば、FROM 句で CTE に別名を割り当てた場合、CTE に対するすべての参照で別名を使用する必要があります。 CTE はオブジェクト ID を持たないため、CTE の参照は明確にする必要があります。[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、オブジェクト ID を使用して、オブジェクトと別名の暗黙的なリレーションシップを識別します。 このリレーションシップがない場合、クエリ プランで予期しない結合動作やクエリ結果が生成される可能性があります。 次の例では、更新操作の対象オブジェクトとして CTE を指定するときの、適切な方法と不適切な方法を示します。  
  
```sql  
USE tempdb;  
GO  
-- UPDATE statement with CTE references that are correctly matched.  
DECLARE @x TABLE (ID INT, Value INT);  
DECLARE @y TABLE (ID INT, Value INT);  
INSERT @x VALUES (1, 10), (2, 20);  
INSERT @y VALUES (1, 100),(2, 200);  
  
WITH cte AS (SELECT * FROM @x)  
UPDATE x -- cte is referenced by the alias.  
SET Value = y.Value  
FROM cte AS x  -- cte is assigned an alias.  
INNER JOIN @y AS y ON y.ID = x.ID;  
SELECT * FROM @x;  
GO  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 ```  
 ID     Value  
 ------ -----  
 1      100  
 2      200  
 (2 row(s) affected)  
```  

CTE 参照が正しく一致していない UPDATE ステートメント。  

```sql  
USE tempdb;  
GO  
DECLARE @x TABLE (ID INT, Value INT);  
DECLARE @y TABLE (ID INT, Value INT);  
INSERT @x VALUES (1, 10), (2, 20);  
INSERT @y VALUES (1, 100),(2, 200);  
  
WITH cte AS (SELECT * FROM @x)  
UPDATE cte   -- cte is not referenced by the alias.  
SET Value = y.Value  
FROM cte AS x  -- cte is assigned an alias.  
INNER JOIN @y AS y ON y.ID = x.ID;   
SELECT * FROM @x;   
GO  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
ID     Value  
------ -----  
1      100  
2      100  
(2 row(s) affected)  
```  

## <a name="locking-behavior"></a>ロック動作  
 UPDATE ステートメントでは、変更するすべての行について排他 (X) ロックが獲得され、トランザクションが完了するまでそのロックが保持されます。 UPDATE ステートメントのクエリ プラン、変更される行の数、およびトランザクション分離レベルによっては、ロックが行レベルではなく、ページ レベルまたはテーブル レベルで取得される場合があります。 これらの上位レベルのロックを回避するには、何千もの行に影響を与える UPDATE ステートメントをバッチに分割し、結合およびフィルター条件がインデックスによってサポートされるようにすることを検討してください。 SQL Server でのロックのメカニズムの詳細については、「[データベース エンジンのロック](../../relational-databases/sql-server-transaction-locking-and-row-versioning-guide.md#Lock_Engine)」の記事を参照してください。  
  
## <a name="logging-behavior"></a>ログ記録の動作  
 UPDATE ステートメントはログに記録されますが、 **\.WRITE** 句を使用して値の大きなデータ型の一部を更新する場合には、最低限の内容だけがログに記録されます。 詳細については、前のセクション「データ型」の「大きな値のデータ型を更新する」を参照してください。  
  
## <a name="security"></a>セキュリティ  
  
### <a name="permissions"></a>アクセス許可  
 対象のテーブルに対する `UPDATE` 権限が必要です。 UPDATE ステートメントに WHERE 句が含まれる場合や、SET 句の *expression* でテーブル内の列を使用する場合は、`SELECT` 権限も必要です。  
  
 UPDATE 権限は、既定では `sysadmin` 固定サーバー ロール、`db_owner` および `db_datawriter` 固定データベース ロールのメンバー、テーブル所有者に与えられています。 `sysadmin`、`db_owner`、`db_securityadmin` ロールのメンバーと、テーブル所有者は、他のユーザーに権限を譲渡できます。  
  
##  <a name="examples"></a><a name="UpdateExamples"></a> 使用例  
  
|カテゴリ|主な構文要素|  
|--------------|------------------------------|  
|[基本構文](#BasicSyntax)|UPDATE|  
|[更新される行を制限する](#LimitingValues)|WHERE • TOP • WITH 共通テーブル式 • WHERE CURRENT OF|  
|[列の値を設定する](#ColumnValues)|計算値 • 複合演算子 • 既定値 • サブクエリ|  
|[標準的なテーブル以外の対象オブジェクトを指定する](#TargetObjects)|ビュー • テーブル変数 • テーブルの別名|  
|[他のテーブルのデータに基づいてデータを更新する](#OtherTables)|FROM|  
|[リモート テーブルの行を更新する](#RemoteTables)|リンク サーバー • OPENQUERY • OPENDATASOURCE|  
|[ラージ オブジェクト データ型を更新する](#LOBValues)|.WRITE • OPENROWSET|  
|[ユーザー定義型を更新する](#UDTs)|ユーザー定義型|  
|[ヒントを使用してクエリ オプティマイザーの既定の動作をオーバーライドする](#TableHints)|テーブル ヒント • クエリ ヒント|  
|[UPDATE ステートメントの結果をキャプチャする](#CaptureResults)|OUTPUT 句|  
|[その他のステートメントで UPDATE を使用する](#Other)|ストアド プロシージャ • TRY...CATCH|  
  
###  <a name="basic-syntax"></a><a name="BasicSyntax"></a> 基本構文  
 このセクションの例では、最低限必要な構文を使用して UPDATE ステートメントの基本機能を示します。  
  
#### <a name="a-using-a-simple-update-statement"></a>A. 単純な UPDATE ステートメントを使用する  
 次の例では、`Person.Address` テーブル内のすべての行を対象に単一の列を更新します。  
  
```sql  
USE AdventureWorks2012;  
GO  
UPDATE Person.Address  
SET ModifiedDate = GETDATE();  
```  
  
#### <a name="b-updating-multiple-columns"></a>B. 複数の列を更新する  
 次の例では、`Bonus` テーブルのすべての行の `CommissionPct` 列、`SalesQuota` 列、および `SalesPerson` 列の値を更新します。  
  
```sql  
USE AdventureWorks2012;  
GO  
UPDATE Sales.SalesPerson  
SET Bonus = 6000, CommissionPct = .10, SalesQuota = NULL;  
GO  
```  
  
###  <a name="limiting-the-rows-that-are-updated"></a><a name="LimitingValues"></a> 更新される行を制限する  
 このセクションの例では、UPDATE ステートメントによって影響を受ける行の数を制限する方法を示します。  
  
#### <a name="c-using-the-where-clause"></a>C. WHERE 句を使用する  
 次の例では、WHERE 句を使用して更新する行を指定します。 このステートメントは、`Production.Product` テーブルの `Color` 列の値を更新します。`Color` 列の既存の値が 'Red' で、なおかつ `Name` 列の値が 'Road-250' で始まるすべての行が対象となります。  
  
```sql  
USE AdventureWorks2012;  
GO  
UPDATE Production.Product  
SET Color = N'Metallic Red'  
WHERE Name LIKE N'Road-250%' AND Color = N'Red';  
GO  
```  
  
#### <a name="d-using-the-top-clause"></a>D. TOP 句を使用する  
 次の例では、UPDATE ステートメントで変更される行の数を TOP 句で制限します。 UPDATE ステートメントで TOP (*n*) 句を使用した場合、ランダムに選択される '*n*' 行に対して更新操作が実行されます。 次の例では、`VacationHours` テーブル内のランダムな 10 個の行について、`Employee` 列を 25% 増しに更新します。  
  
```sql  
USE AdventureWorks2012;
GO
UPDATE TOP (10) HumanResources.Employee
SET VacationHours = VacationHours * 1.25 ;
GO  
```  
  
 TOP を使用して、意味のある日時順に更新を適用する必要がある場合は、サブセレクト ステートメントに ORDER BY を含めて TOP を使用する必要があります。 次の例では、採用日の古い従業員上位 10 人の休暇時間を更新します。  
  
```sql  
UPDATE HumanResources.Employee  
SET VacationHours = VacationHours + 8  
FROM (SELECT TOP 10 BusinessEntityID FROM HumanResources.Employee  
     ORDER BY HireDate ASC) AS th  
WHERE HumanResources.Employee.BusinessEntityID = th.BusinessEntityID;  
GO  
```  
  
#### <a name="e-using-the-with-common_table_expression-clause"></a>E. WITH common_table_expression 句を使用する  
 次の例では、`PerAssemblyQty` の製造に直接または間接的に使用されるすべての部品およびコンポーネントの `ProductAssemblyID 800` の値を更新します。 共通テーブル式は、`ProductAssemblyID 800` の製造に直接使用される部品やそのコンポーネントの製造に使用される部品などを含む、部品の階層リストを返します。 共通テーブル式が返した行のみが変更されます。  
  
```sql  
USE AdventureWorks2012;  
GO  
WITH Parts(AssemblyID, ComponentID, PerAssemblyQty, EndDate, ComponentLevel) AS  
(  
    SELECT b.ProductAssemblyID, b.ComponentID, b.PerAssemblyQty,  
        b.EndDate, 0 AS ComponentLevel  
    FROM Production.BillOfMaterials AS b  
    WHERE b.ProductAssemblyID = 800  
          AND b.EndDate IS NULL  
    UNION ALL  
    SELECT bom.ProductAssemblyID, bom.ComponentID, p.PerAssemblyQty,  
        bom.EndDate, ComponentLevel + 1  
    FROM Production.BillOfMaterials AS bom   
        INNER JOIN Parts AS p  
        ON bom.ProductAssemblyID = p.ComponentID  
        AND bom.EndDate IS NULL  
)  
UPDATE Production.BillOfMaterials  
SET PerAssemblyQty = c.PerAssemblyQty * 2  
FROM Production.BillOfMaterials AS c  
JOIN Parts AS d ON c.ProductAssemblyID = d.AssemblyID  
WHERE d.ComponentLevel = 0;  
```  
  
#### <a name="f-using-the-where-current-of-clause"></a>F. WHERE CURRENT OF 句を使用する  
 次の例では、WHERE CURRENT OF 句を使用して、カーソルが置かれている行だけを更新します。 カーソルが結合に基づくとき、UPDATE ステートメントで指定した `table_name` のみが変更されます。 この場合、カーソルに関連する他のテーブルには影響ありません。  
  
```sql  
USE AdventureWorks2012;  
GO  
DECLARE complex_cursor CURSOR FOR  
    SELECT a.BusinessEntityID  
    FROM HumanResources.EmployeePayHistory AS a  
    WHERE RateChangeDate <>   
         (SELECT MAX(RateChangeDate)  
          FROM HumanResources.EmployeePayHistory AS b  
          WHERE a.BusinessEntityID = b.BusinessEntityID) ;  
OPEN complex_cursor;  
FETCH FROM complex_cursor;  
UPDATE HumanResources.EmployeePayHistory  
SET PayFrequency = 2   
WHERE CURRENT OF complex_cursor;  
CLOSE complex_cursor;  
DEALLOCATE complex_cursor;  
GO  
```  
  
###  <a name="setting-column-values"></a><a name="ColumnValues"></a> 列の値を設定する  
 このセクションの例では、計算値、サブクエリ、および DEFAULT 値を使用した列の更新を示します。  
  
#### <a name="g-specifying-a-computed-value"></a>G. 計算値を指定する  
 次の例では、UPDATE ステートメントに計算値を使用しています。 `ListPrice` テーブルのすべての行の `Product` 列の値を倍にします。  
  
```sql  
USE AdventureWorks2012 ;  
GO  
UPDATE Production.Product  
SET ListPrice = ListPrice * 2;  
GO  
```  
  
#### <a name="h-specifying-a-compound-operator"></a>H. 複合演算子を指定する  
 次の例では、変数 `@NewPrice` を使用し、赤い自転車の現在の価格を取得して 10 を追加することで、すべての赤い自転車の価格を増加させます。  
  
```sql  
USE AdventureWorks2012;  
GO  
DECLARE @NewPrice INT = 10;  
UPDATE Production.Product  
SET ListPrice += @NewPrice  
WHERE Color = N'Red';  
GO  
```  
  
 次の例では、複合演算子 += を使用して、`' - tool malfunction'` が 10 ～ 12 である行を対象に、`Name` 列の既存の値にデータ `ScrapReasonID` を追加します。  
  
```sql  
USE AdventureWorks2012;  
GO  
UPDATE Production.ScrapReason   
SET Name += ' - tool malfunction'  
WHERE ScrapReasonID BETWEEN 10 and 12;  
```  
  
#### <a name="i-specifying-a-subquery-in-the-set-clause"></a>I. SET 句でサブクエリを指定する  
 次の例では、SET 句にサブクエリを使用して、列の更新に使用する値を決定します。 サブクエリからは、スカラー値 (つまり、1 行につき単一の値) のみが返されます。 この例では、`SalesYTD` テーブルの最新の売上高を反映するように `SalesPerson` テーブルの `SalesOrderHeader` 列を変更します。 サブクエリにより、`UPDATE` ステートメントで各販売員の売上高が集計されます。  
  
```sql  
USE AdventureWorks2012;  
GO  
UPDATE Sales.SalesPerson  
SET SalesYTD = SalesYTD +   
    (SELECT SUM(so.SubTotal)   
     FROM Sales.SalesOrderHeader AS so  
     WHERE so.OrderDate = (SELECT MAX(OrderDate)  
                           FROM Sales.SalesOrderHeader AS so2  
                           WHERE so2.SalesPersonID = so.SalesPersonID)  
     AND Sales.SalesPerson.BusinessEntityID = so.SalesPersonID  
     GROUP BY so.SalesPersonID);  
GO  
```  
  
#### <a name="j-updating-rows-using-default-values"></a>J. DEFAULT 値を使用して行を更新する  
 次の例では、`CostRate` の値が `CostRate` より大きいすべての行を対象に、`20.00` 列をその既定値 (0.00) に設定します。  
  
```sql  
USE AdventureWorks2012;  
GO  
UPDATE Production.Location  
SET CostRate = DEFAULT  
WHERE CostRate > 20.00;  
```  
  
###  <a name="specifying-target-objects-other-than-standard-tables"></a><a name="TargetObjects"></a> 標準的なテーブル以外の対象オブジェクトを指定する  
 このセクションの例では、ビュー、テーブルの別名、またはテーブル変数を指定して行を更新する方法を示します。  
  
#### <a name="k-specifying-a-view-as-the-target-object"></a>K. ビューを対象オブジェクトとして指定する  
 次の例では、対象オブジェクトとしてビューを指定し、テーブルの行を更新します。 ビュー定義では複数のテーブルが参照されますが、UPDATE ステートメントで参照されるのは、基になるいずれかのテーブルの列だけであるため、ステートメントは正常に実行されます。 仮に両方のテーブルの列が指定された場合、UPDATE ステートメントは失敗します。 詳細については、「[ビューを使用したデータ変更](../../relational-databases/views/modify-data-through-a-view.md)」を参照してください。  
  
```sql  
USE AdventureWorks2012;  
GO  
UPDATE Person.vStateProvinceCountryRegion  
SET CountryRegionName = 'United States of America'  
WHERE CountryRegionName = 'United States';  
```  
  
#### <a name="l-specifying-a-table-alias-as-the-target-object"></a>L. テーブルの別名を対象オブジェクトとして指定する  
 次の例では、テーブル `Production.ScrapReason` の行を更新します。 FROM 句で `ScrapReason` に割り当てられたテーブルの別名は、UPDATE 句の対象オブジェクトとして指定されます。  
  
```sql  
USE AdventureWorks2012;  
GO  
UPDATE sr  
SET sr.Name += ' - tool malfunction'  
FROM Production.ScrapReason AS sr  
JOIN Production.WorkOrder AS wo   
     ON sr.ScrapReasonID = wo.ScrapReasonID  
     AND wo.ScrappedQty > 300;  
```  
  
#### <a name="m-specifying-a-table-variable-as-the-target-object"></a>M. テーブル変数を対象オブジェクトとして指定する  
 次の例では、テーブル変数内の行を更新します。  
  
```sql  
USE AdventureWorks2012;  
GO  
-- Create the table variable.  
DECLARE @MyTableVar TABLE (  
    EmpID INT NOT NULL,  
    NewVacationHours INT,  
    ModifiedDate DATETIME);  
  
-- Populate the table variable with employee ID values from HumanResources.Employee.  
INSERT INTO @MyTableVar (EmpID)  
    SELECT BusinessEntityID FROM HumanResources.Employee;  
  
-- Update columns in the table variable.  
UPDATE @MyTableVar  
SET NewVacationHours = e.VacationHours + 20,  
    ModifiedDate = GETDATE()  
FROM HumanResources.Employee AS e   
WHERE e.BusinessEntityID = EmpID;  
  
-- Display the results of the UPDATE statement.  
SELECT EmpID, NewVacationHours, ModifiedDate FROM @MyTableVar  
ORDER BY EmpID;  
GO  
```  
  
###  <a name="updating-data-based-on-data-from-other-tables"></a><a name="OtherTables"></a> 他のテーブルのデータに基づいてデータを更新する  
 このセクションの例では、テーブルの行を別のテーブルの情報に基づいて更新する方法を示します。  
  
#### <a name="n-using-the-update-statement-with-information-from-another-table"></a>北 別のテーブルの情報を使用して UPDATE ステートメントを実行する  
 次の例では、`SalesOrderHeader` テーブルの最新の売上高を反映するように `SalesPerson` テーブルの `SalesYTD` 列を変更します。  
  
```sql  
USE AdventureWorks2012;  
GO  
UPDATE Sales.SalesPerson  
SET SalesYTD = SalesYTD + SubTotal  
FROM Sales.SalesPerson AS sp  
JOIN Sales.SalesOrderHeader AS so  
    ON sp.BusinessEntityID = so.SalesPersonID  
    AND so.OrderDate = (SELECT MAX(OrderDate)  
                        FROM Sales.SalesOrderHeader  
                        WHERE SalesPersonID = sp.BusinessEntityID);  
GO  
```  
  
 前の例では、特定の日付の指定された営業部員の売上は 1 つのみ記録され、更新が最新であるということを前提にしています。 指定された営業部員に対し、同じ日に 2 つ以上の売上が記録される場合は、前の例は正しく動作しません。 この場合、エラーなしで実行されますが、実際に同じ日に登録された売上件数に関係なく、1 つの売上のみを使用して `SalesYTD`の値が更新されます。 これは、1 つの UPDATE ステートメントで同じ行を 2 回更新しないためです。  
  
 同じ日に、指定した販売員の売り上げが複数記録されるような場合は、次の例のように、`UPDATE` ステートメントの中で販売員ごとにすべての売り上げを集計する必要があります。  
  
```sql  
USE AdventureWorks2012;  
GO  
UPDATE Sales.SalesPerson  
SET SalesYTD = SalesYTD +   
    (SELECT SUM(so.SubTotal)   
     FROM Sales.SalesOrderHeader AS so  
     WHERE so.OrderDate = (SELECT MAX(OrderDate)  
                           FROM Sales.SalesOrderHeader AS so2  
                           WHERE so2.SalesPersonID = so.SalesPersonID)  
     AND Sales.SalesPerson.BusinessEntityID = so.SalesPersonID  
     GROUP BY so.SalesPersonID);  
GO  
```  
  
###  <a name="updating-rows-in-a-remote-table"></a><a name="RemoteTables"></a> リモート テーブルの行を更新する  
 このセクションの例では、[リンク サーバー](../../relational-databases/system-stored-procedures/sp-addlinkedserver-transact-sql.md)または[行セット関数](../functions/opendatasource-transact-sql.md)を使用してリモート テーブルを参照し、リモートの対象テーブルの行を更新する方法を示します。  
  
#### <a name="o-updating-data-in-a-remote-table-by-using-a-linked-server"></a>O. リンク サーバーを使用してリモート テーブルのデータを更新する  
 次の例では、リモート サーバー上のテーブルを更新します。 [sp_addlinkedserver](../../relational-databases/system-stored-procedures/sp-addlinkedserver-transact-sql.md) を使用してリモート データ ソースへのリンクを作成した後、 server.catalog.schema.object という形式の 4 部構成のオブジェクト名の一部として、リンク サーバー名 `MyLinkedServer` を指定します。 `@datasrc` には有効なサーバー名を指定する必要があります。  
  
```sql  
USE master;  
GO  
-- Create a link to the remote data source.   
-- Specify a valid server name for @datasrc as 'server_name' or 'server_nameinstance_name'.  
  
EXEC sp_addlinkedserver @server = N'MyLinkedServer',  
    @srvproduct = N' ',  
    @provider = N'SQLNCLI10',   
    @datasrc = N'<server name>',  
    @catalog = N'AdventureWorks2012';  
GO  
USE AdventureWorks2012;  
GO  
-- Specify the remote data source using a four-part name   
-- in the form linked_server.catalog.schema.object.  
  
UPDATE MyLinkedServer.AdventureWorks2012.HumanResources.Department  
SET GroupName = N'Public Relations'  
WHERE DepartmentID = 4;  
```  
  
#### <a name="p-updating-data-in-a-remote-table-by-using-the-openquery-function"></a>P. OPENQUERY 関数を使用してリモート テーブルのデータを更新する  
 次の例では、[OPENQUERY](../../t-sql/functions/openquery-transact-sql.md) 行セット関数を指定してリモート テーブルの行を更新します。 この例では、前の例で作成したリンク サーバー名を使用します。  
  
```sql  
UPDATE OPENQUERY (MyLinkedServer, 'SELECT GroupName FROM HumanResources.Department WHERE DepartmentID = 4')   
SET GroupName = 'Sales and Marketing';  
```  
  
#### <a name="q-updating-data-in-a-remote-table-by-using-the-opendatasource-function"></a>Q. OPENDATASOURCE 関数を使用してリモート テーブルのデータを更新する  
 次の例では、[OPENDATASOURCE](../../t-sql/functions/opendatasource-transact-sql.md) 行セット関数を指定してリモート テーブルの行を更新します。 *server_name* または *server_name\instance_name* という形式を使用して、データ ソースの有効なサーバー名を指定します。 場合によっては、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスを Ad Hoc Distributed Queries 用に構成する必要があります。 詳細については、「[ad hoc distributed queries サーバー構成オプション](../../database-engine/configure-windows/ad-hoc-distributed-queries-server-configuration-option.md)」を参照してください。  

```sql
UPDATE OPENDATASOURCE('SQLNCLI', 'Data Source=<server name>;Integrated Security=SSPI').AdventureWorks2012.HumanResources.Department
SET GroupName = 'Sales and Marketing' WHERE DepartmentID = 4;  
```

###  <a name="updating-large-object-data-types"></a><a name="LOBValues"></a> ラージ オブジェクト データ型を更新する  
 このセクションの例では、ラージ オブジェクト (LOB) データ型で定義された列の値を更新する方法を示します。  
  
#### <a name="r-using-update-with-write-to-modify-data-in-an-nvarcharmax-column"></a>R. UPDATE を .WRITE と共に使用し、nvarchar(max) 列のデータを変更する  
 次の例では、.WRITE 句を使用して、`Production.Document` テーブルの `DocumentSummary` 型の **nvarchar(max)** 列の値を部分的に更新します。 置換する語、既存データ内で置換される語の開始位置 (オフセット)、置換する文字数 (長さ) を指定することにより、`components` という語が、`features` という語で置換されます。 また、この例では、OUTPUT 句を使用して、`DocumentSummary` 列の前イメージと後イメージを `@MyTableVar` テーブル変数に返します。  
  
```sql  
USE AdventureWorks2012;  
GO  
DECLARE @MyTableVar TABLE (  
    SummaryBefore NVARCHAR(max),  
    SummaryAfter NVARCHAR(max));  
UPDATE Production.Document  
SET DocumentSummary .WRITE (N'features',28,10)  
OUTPUT deleted.DocumentSummary,   
       inserted.DocumentSummary   
    INTO @MyTableVar  
WHERE Title = N'Front Reflector Bracket Installation';  
SELECT SummaryBefore, SummaryAfter   
FROM @MyTableVar;  
GO  
```  
  
#### <a name="s-using-update-with-write-to-add-and-remove-data-in-an-nvarcharmax-column"></a>S. UPDATE を .WRITE と共に使用し、nvarchar(max) 列のデータを追加および削除する  
 次の例は、現在、値に NULL が設定されている **nvarchar(max)** 列のデータを追加し、削除します。 .WRITE 句を使用して NULL 列を変更することはできないため、まず列に一時的なデータを設定します。 次に .WRITE 句を使用して、このデータを正しいデータで置換します。 その後の例では、列の値の最後にデータを追加し、列からデータを削除 (切り捨て) し、最後に列から部分的なデータを削除します。 SELECT ステートメントは、各 UPDATE ステートメントで生成されたデータ変更を表示します。  
  
```sql  
USE AdventureWorks2012;  
GO  
-- Replacing NULL value with temporary data.  
UPDATE Production.Document  
SET DocumentSummary = N'Replacing NULL value'  
WHERE Title = N'Crank Arm and Tire Maintenance';  
GO  
SELECT DocumentSummary   
FROM Production.Document  
WHERE Title = N'Crank Arm and Tire Maintenance';  
GO  
-- Replacing temporary data with the correct data. Setting @Length to NULL   
-- truncates all existing data from the @Offset position.  
UPDATE Production.Document  
SET DocumentSummary .WRITE(N'Carefully inspect and maintain the tires and crank arms.',0,NULL)  
WHERE Title = N'Crank Arm and Tire Maintenance';  
GO  
SELECT DocumentSummary   
FROM Production.Document  
WHERE Title = N'Crank Arm and Tire Maintenance';  
GO  
-- Appending additional data to the end of the column by setting   
-- @Offset to NULL.  
UPDATE Production.Document  
SET DocumentSummary .WRITE (N' Appending data to the end of the column.', NULL, 0)  
WHERE Title = N'Crank Arm and Tire Maintenance';  
GO  
SELECT DocumentSummary   
FROM Production.Document  
WHERE Title = N'Crank Arm and Tire Maintenance';  
GO  
-- Removing all data from @Offset to the end of the existing value by   
-- setting expression to NULL.   
UPDATE Production.Document  
SET DocumentSummary .WRITE (NULL, 56, 0)  
WHERE Title = N'Crank Arm and Tire Maintenance';  
GO  
SELECT DocumentSummary   
FROM Production.Document  
WHERE Title = N'Crank Arm and Tire Maintenance';  
GO  
-- Removing partial data beginning at position 9 and ending at   
-- position 21.  
UPDATE Production.Document  
SET DocumentSummary .WRITE ('',9, 12)  
WHERE Title = N'Crank Arm and Tire Maintenance';  
GO  
SELECT DocumentSummary   
FROM Production.Document  
WHERE Title = N'Crank Arm and Tire Maintenance';  
GO  
```  
  
#### <a name="t-using-update-with-openrowset-to-modify-a-varbinarymax-column"></a>T. UPDATE を OPENROWSET と共に使用し、varbinary(max) 列を変更する  
 次の例では、**varbinary(max)** 列に格納されている既存のイメージを新しいイメージで置換します。 [OPENROWSET](../../t-sql/functions/openrowset-transact-sql.md) 関数を BULK オプションと共に使用し、列にイメージを読み込みます。 この例では、`Tires.jpg` という名前のファイルが指定されたファイル パスに存在することを前提としています。  
  
```sql  
USE AdventureWorks2012;  
GO  
UPDATE Production.ProductPhoto  
SET ThumbNailPhoto = (  
    SELECT *  
    FROM OPENROWSET(BULK 'c:Tires.jpg', SINGLE_BLOB) AS x )  
WHERE ProductPhotoID = 1;  
GO  
```  
  
#### <a name="u-using-update-to-modify-filestream-data"></a>U. UPDATE を使用して FILESTREAM データを変更する  
 次の例では、UPDATE ステートメントを使用して、ファイル システムのファイルのデータを変更します。 大量のデータをファイルにストリーミングする場合、この方法はお勧めできません。 適切な Win32 インターフェイスを使用してください。 ファイル レコード内の任意のテキストを、 `Xray 1`というテキストに置換する例を次に示します。 詳細については、「[FILESTREAM &#40;SQL Server&#41;](../../relational-databases/blob/filestream-sql-server.md)」をご覧ください。  
  
```sql  
UPDATE Archive.dbo.Records  
SET [Chart] = CAST('Xray 1' as VARBINARY(max))  
WHERE [SerialNumber] = 2;  
```  
  
###  <a name="updating-user-defined-types"></a><a name="UDTs"></a> ユーザー定義型を更新する  
 次の例では、CLR ユーザー定義型 (UDT) の列の値を変更します。 3 つの方法が示されています。 ユーザー定義の列に関する詳細については、「[CLR ユーザー定義型](../../relational-databases/clr-integration-database-objects-user-defined-types/clr-user-defined-types.md)」を参照してください。  
  
#### <a name="v-using-a-system-data-type"></a>V. システム データ型を使用する  
 ユーザー定義型で [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] システム データ型からの暗黙的または明示的な変換がサポートされている場合は、そのシステム データ型の値を指定することによって、UDT を更新できます。 次の例は、文字列からの明示的な変換によって、ユーザー定義型 `Point` の列の値を更新する方法を示します。  
  
```sql  
UPDATE dbo.Cities  
SET Location = CONVERT(Point, '12.3:46.2')  
WHERE Name = 'Anchorage';  
```  
  
#### <a name="w-invoking-a-method"></a>西 メソッドを呼び出す  
 ミューテーターとしてマークされたユーザー定義型のメソッドを呼び出し、更新を実行することによって、UDT を更新できます。 次の例では、`Point` 型の `SetXY` というミューテーター メソッドを呼び出します。 これにより、その型のインスタンスの状態が更新されます。  
  
```sql  
UPDATE dbo.Cities  
SET Location.SetXY(23.5, 23.5)  
WHERE Name = 'Anchorage';  
```  
  
#### <a name="x-modifying-the-value-of-a-property-or-data-member"></a>X. プロパティまたはデータ メンバーの値を変更する  
 ユーザー定義型の登録済みプロパティまたはパブリック データ メンバーの値を変更することによって、UDT を更新できます。 値を指定する式は、プロパティの型に暗黙的に変換できる必要があります。 次の例では、ユーザー定義型 `X` のプロパティ `Point` の値を変更します。  
  
```sql  
UPDATE dbo.Cities  
SET Location.X = 23.5  
WHERE Name = 'Anchorage';  
```  
  
###  <a name="overriding-the-default-behavior-of-the-query-optimizer-by-using-hints"></a><a name="TableHints"></a> ヒントを使用してクエリ オプティマイザーの既定の動作をオーバーライドする  
 このセクションの例では、テーブル ヒントとクエリ ヒントを使用して、UPDATE ステートメントを処理する際のクエリ オプティマイザーの既定の動作を一時的にオーバーライドする方法を示します。  
  
> [!CAUTION]  
>  通常、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] クエリ オプティマイザーでは、クエリにとって最適な実行プランが選択されるため、ヒントは、経験を積んだ開発者やデータベース管理者が最後の手段としてのみ使用することをお勧めします。  
  
#### <a name="y-specifying-a-table-hint"></a>Y. テーブル ヒントを指定する  
 次の例では、[テーブル ヒント](../../t-sql/queries/hints-transact-sql-table.md) TABLOCK を指定します。 このヒントは、`Production.Product` テーブルに対して共有ロックを使用することと、このロックを UPDATE ステートメントの終了まで保持することを指定します。  
  
```sql  
USE AdventureWorks2012;  
GO  
UPDATE Production.Product  
WITH (TABLOCK)  
SET ListPrice = ListPrice * 1.10  
WHERE ProductNumber LIKE 'BK-%';  
GO  
```  
  
#### <a name="z-specifying-a-query-hint"></a>送信してください。 クエリ ヒントを指定する  
 次の例では、UPDATE ステートメントで[クエリ ヒント](../../t-sql/queries/hints-transact-sql-query.md)`OPTIMIZE FOR (@variable)` を指定します。 このヒントは、クエリをコンパイルおよび最適化するときにローカル変数に対して特定の値を使用するように、クエリ オプティマイザーに指示します。 この値はクエリを最適化する過程でのみ使用され、クエリの実行時には使用されません。  
  
```sql  
USE AdventureWorks2012;  
GO  
CREATE PROCEDURE Production.uspProductUpdate  
@Product NVARCHAR(25)  
AS  
SET NOCOUNT ON;  
UPDATE Production.Product  
SET ListPrice = ListPrice * 1.10  
WHERE ProductNumber LIKE @Product  
OPTION (OPTIMIZE FOR (@Product = 'BK-%') );  
GO  
-- Execute the stored procedure   
EXEC Production.uspProductUpdate 'BK-%';  
```  
  
###  <a name="capturing-the-results-of-the-update-statement"></a><a name="CaptureResults"></a> UPDATE ステートメントの結果をキャプチャする  
 このセクションの例では、[OUTPUT 句](../../t-sql/queries/output-clause-transact-sql.md)を使用して、UPDATE ステートメントの影響を受ける各行の情報や、それらに基づく式を返す方法を示します。 これらの結果は処理アプリケーションに返され、確認メッセージの表示、アーカイブ化、その他のアプリケーション要件で使用することができます。  
  
#### <a name="aa-using-update-with-the-output-clause"></a>AA. UPDATE ステートメントを OUTPUT 句と共に使用する  
 次の例では、最初の 10 行について `VacationHours` テーブルの列 `Employee` を 1.25 倍に更新し、列 `ModifiedDate` の値を現在の日付に設定します。 `OUTPUT` 句は、`VacationHours` を適用する前の `UPDATE` 列の `deleted.VacationHours` の値と、`inserted.VacationHours` 列の更新後の値を `@MyTableVar` テーブル変数に返します。  
  
 この後に、`SELECT` 内の値、および `@MyTableVar` テーブルの更新操作の結果を返す 2 つの `Employee` ステートメントが続きます。 OUTPUT 句の使用例は、「[OUTPUT 句 &#40;Transact-SQL&#41;](../../t-sql/queries/output-clause-transact-sql.md)」を参照してください。  
  
```sql  
USE AdventureWorks2012;  
GO  
DECLARE @MyTableVar TABLE (  
    EmpID INT NOT NULL,  
    OldVacationHours INT,  
    NewVacationHours INT,  
    ModifiedDate datetime);  
UPDATE TOP (10) HumanResources.Employee  
SET VacationHours = VacationHours * 1.25,  
    ModifiedDate = GETDATE()   
OUTPUT inserted.BusinessEntityID,  
       deleted.VacationHours,  
       inserted.VacationHours,  
       inserted.ModifiedDate  
INTO @MyTableVar;  
--Display the result set of the table variable.  
SELECT EmpID, OldVacationHours, NewVacationHours, ModifiedDate  
FROM @MyTableVar;  
GO  
--Display the result set of the table.  
SELECT TOP (10) BusinessEntityID, VacationHours, ModifiedDate  
FROM HumanResources.Employee;  
GO  
```  
  
###  <a name="using-update-in-other-statements"></a><a name="Other"></a> その他のステートメントで UPDATE を使用する  
 このセクションの例では、他のステートメントでの UPDATE の使用方法を示します。  
  
#### <a name="ab-using-update-in-a-stored-procedure"></a>AB. UPDATE をストアド プロシージャで使用する  
 次の例では、UPDATE ステートメントをストアド プロシージャで使用しています。 このプロシージャには、1 つの入力パラメーター `@NewHours` と 1 つの出力パラメーター `@RowCount` があります。 その `@NewHours` パラメーター値を UPDATE ステートメントで使用して、`HumanResources.Employee` テーブルの `VacationHours` 列を更新します。 影響を受けた行数は、`@RowCount` 出力パラメーターを使用して、ローカル変数に返されます。 `VacationHours` に設定する値は、SET 句で CASE 式を使用して条件に応じて決定しています。 従業員の給与が時給ベース (`SalariedFlag` = 0) である場合、`VacationHours` は `@NewHours` で指定された値に現在の時間数を加算した値に設定されます。それ以外の場合は、`VacationHours` は `@NewHours` で指定された値に設定されます。  
  
```sql  
USE AdventureWorks2012;  
GO  
CREATE PROCEDURE HumanResources.Update_VacationHours  
@NewHours SMALLINT  
AS   
SET NOCOUNT ON;  
UPDATE HumanResources.Employee  
SET VacationHours =   
    ( CASE  
         WHEN SalariedFlag = 0 THEN VacationHours + @NewHours  
         ELSE @NewHours  
       END  
    )  
WHERE CurrentFlag = 1;  
GO  
  
EXEC HumanResources.Update_VacationHours 40;  
```  
  
#### <a name="ac-using-update-in-a-trycatch-block"></a>AC. TRY...CATCH ブロックで UPDATE を使用する  
 次の例では、TRY...CATCH ブロックで UPDATE ステートメントを使用して、更新操作中に発生した実行エラーを処理します。  
  
```sql  
USE AdventureWorks2012;  
GO  
BEGIN TRANSACTION;  
  
BEGIN TRY  
    -- Intentionally generate a constraint violation error.  
    UPDATE HumanResources.Department  
    SET Name = N'MyNewName'  
    WHERE DepartmentID BETWEEN 1 AND 2;  
END TRY  
BEGIN CATCH  
    SELECT   
         ERROR_NUMBER() AS ErrorNumber  
        ,ERROR_SEVERITY() AS ErrorSeverity  
        ,ERROR_STATE() AS ErrorState  
        ,ERROR_PROCEDURE() AS ErrorProcedure  
        ,ERROR_LINE() AS ErrorLine  
        ,ERROR_MESSAGE() AS ErrorMessage;  
  
    IF @@TRANCOUNT > 0  
        ROLLBACK TRANSACTION;  
END CATCH;  
  
IF @@TRANCOUNT > 0  
    COMMIT TRANSACTION;  
GO  
```  
  
## <a name="examples-sssdw-and-sspdw"></a>例: [!INCLUDE[ssSDW](../../includes/sssdw-md.md)]、[!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="ad-using-a-simple-update-statement"></a>AD. 単純な UPDATE ステートメントを使用する  
 次の例は、更新する行の指定に WHERE 句が使用されなかった場合に、すべての行がどのように影響を受けるかを示します。  
  
 この例では、`DimEmployee` テーブルのすべての行の `EndDate` 列と `CurrentFlag` 列の値を更新します。  
  
```sql  
-- Uses AdventureWorks  
  
UPDATE DimEmployee  
SET EndDate = '2010-12-31', CurrentFlag='False';  
```  
  
 UPDATE ステートメントでは計算値を使用することもできます。 次の例は、`ListPrice` テーブルのすべての行の `Product` 列の値を倍にします。  
  
```sql  
-- Uses AdventureWorks  
  
UPDATE DimEmployee  
SET BaseRate = BaseRate * 2;  
```  
  
### <a name="ae-using-the-update-statement-with-a-where-clause"></a>AE. UPDATE ステートメントを WHERE 句と共に使用する  
 次の例では、WHERE 句を使用して更新する行を指定します。  
  
```sql  
-- Uses AdventureWorks  
  
UPDATE DimEmployee  
SET FirstName = 'Gail'  
WHERE EmployeeKey = 500;  
```  
  
### <a name="af-using-the-update-statement-with-label"></a>AF. UPDATE ステートメントをラベルと共に使用する  
 次の例では、UPDATE ステートメントのラベルの使用を示します。  
  
```sql  
-- Uses AdventureWorks  
  
UPDATE DimProduct  
SET ProductSubcategoryKey = 2   
WHERE ProductKey = 313  
OPTION (LABEL = N'label1');  
```  
  
### <a name="ag-using-the-update-statement-with-information-from-another-table"></a>AG. 別のテーブルの情報を使用して UPDATE ステートメントを実行する  
 この例では、年度別の売上合計を格納するテーブルを作成します。 FactInternetSales テーブルに SELECT ステートメントを実行することで、2004 年度の売上合計を更新します。  
  
```sql  
-- Uses AdventureWorks  
  
CREATE TABLE YearlyTotalSales (  
    YearlySalesAmount MONEY NOT NULL,  
    Year SMALLINT NOT NULL )  
WITH ( DISTRIBUTION = REPLICATE );  
  
INSERT INTO YearlyTotalSales VALUES (0, 2004);  
INSERT INTO YearlyTotalSales VALUES (0, 2005);  
INSERT INTO YearlyTotalSales VALUES (0, 2006);  
  
UPDATE YearlyTotalSales  
SET YearlySalesAmount=  
(SELECT SUM(SalesAmount) FROM FactInternetSales WHERE OrderDateKey >=20040000 AND OrderDateKey < 20050000)  
WHERE Year=2004;  
  
SELECT * FROM YearlyTotalSales;   
```  

### <a name="ah-ansi-join-for-update-statements"></a>AH. 更新ステートメントの ANSI 結合
この例は、別のテーブルとの結合結果に基づいてデータを更新する方法を示しています。

```sql
CREATE TABLE dbo.Table1   
    (ColA INT NOT NULL, ColB DECIMAL(10,3) NOT NULL);  
GO  
CREATE TABLE dbo.Table2   
    (ColA INT NOT NULL, ColB DECIMAL(10,3) NOT NULL);  
GO  
INSERT INTO dbo.Table1 VALUES(1, 10.0);  
INSERT INTO dbo.Table2 VALUES(1, 0.0);  
GO  
UPDATE dbo.Table2   
SET dbo.Table2.ColB = dbo.Table2.ColB + dbo.Table1.ColB  
FROM dbo.Table2   
    INNER JOIN dbo.Table1   
    ON (dbo.Table2.ColA = dbo.Table1.ColA);  
GO  
SELECT ColA, ColB   
FROM dbo.Table2;
GO
```

## <a name="see-also"></a>参照  
 [CREATE TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/create-table-transact-sql.md)   
 [CREATE TRIGGER &#40;Transact-SQL&#41;](../../t-sql/statements/create-trigger-transact-sql.md)   
 [カーソル &#40;Transact-SQL&#41;](../../t-sql/language-elements/cursors-transact-sql.md)   
 [DELETE &#40;Transact-SQL&#41;](../../t-sql/statements/delete-transact-sql.md)   
 [INSERT &#40;Transact-SQL&#41;](../../t-sql/statements/insert-transact-sql.md)   
 [テキスト関数とイメージ関数 &#40;Transact-SQL&#41;](../functions/text-and-image-functions-textptr-transact-sql.md)   
 [WITH common_table_expression &#40;Transact-SQL&#41;](../../t-sql/queries/with-common-table-expression-transact-sql.md)   
 [FILESTREAM &#40;SQL Server&#41;](../../relational-databases/blob/filestream-sql-server.md)  
 [照合順序と Unicode のサポート](../../relational-databases/collations/collation-and-unicode-support.md)    
 [1 バイト文字セットとマルチバイト文字セット](/cpp/c-runtime-library/single-byte-and-multibyte-character-sets)  
