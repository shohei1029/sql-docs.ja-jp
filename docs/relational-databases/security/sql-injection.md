---
title: SQL インジェクション | Microsoft Docs
description: SQL インジェクション攻撃のしくみについて説明します。 SQL Server への入力を検証し、コードを調べて SQL インジェクションがないかを確認することで、このような攻撃を軽減します。
ms.custom: ''
ms.date: 03/16/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: security
ms.topic: conceptual
helpviewer_keywords:
- SQL Injection
ms.assetid: eb507065-ac58-4f18-8601-e5b7f44213ab
author: VanMSFT
ms.author: vanto
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 2678b0eda377060e45a30cf47d4fa7e780e4a595
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104739832"
---
# <a name="sql-injection"></a>SQL インジェクション
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]
  SQL インジェクションとは、後で SQL Server のインスタンスに渡して解析と実行の対象とする文字列に、悪意のあるコードが挿入される攻撃です。 SQL Server では、構文的に有効であれば受信したクエリがすべて実行されるため、SQL ステートメントを構成するすべてのプロシージャにおいて、インジェクションに対する脆弱性を検証する必要があります。 高いスキルを持つ決然たる攻撃者は、パラメーター化されたデータであっても操作できるのです。  
  
## <a name="how-sql-injection-works"></a>SQL インジェクションのしくみ  
 SQL インジェクションは主に、SQL コマンドと連結されて実行されるユーザー入力変数にコードを直接挿入することにより行われます。 それほど直接的ではない攻撃では、悪意のあるコードが、テーブル内の記憶領域に格納される文字列に挿入されたり、メタデータとして挿入されたりします。 格納された文字列が動的な SQL コマンドに後で連結された場合、悪意のあるコードが実行されます。  
  
 インジェクション プロセスは、途中でテキスト文字列を終了し、新しいコマンドを追加することによって行われます。 挿入されたコマンドが実行される前に別の文字列が追加される可能性があるため、攻撃者は挿入する文字列をコメント記号 "--" で終了させます。 後続のテキストは実行時には無視されます。  
  
 次のスクリプトは、単純な SQL インジェクションを示しています。 ハードコードされた文字列とユーザー入力の文字列を連結することによって、SQL クエリが作成されます。  
  
```csharp
var Shipcity;  
ShipCity = Request.form ("ShipCity");  
var sql = "select * from OrdersTable where ShipCity = '" + ShipCity + "'";  
```  
  
 ユーザーは、都市の名前を入力するように要求されます。 ユーザーが「 `Redmond`」と入力した場合、スクリプトによって構築されたクエリは次のようになります。  
  
```sql
SELECT * FROM OrdersTable WHERE ShipCity = 'Redmond'  
```  
  
 ただし、ユーザーが次のように入力するとします。  
  
```sql
Redmond'; drop table OrdersTable--  
```  
  
 この場合、スクリプトによって構築されたクエリは次のようになります。  
  
```sql
SELECT * FROM OrdersTable WHERE ShipCity = 'Redmond';drop table OrdersTable--'  
```  
  
 セミコロン (;) は、前のクエリの終了と次のクエリの開始の区切りを示します。 2 つのハイフン (--) は、現在行の残りの部分はコメントであるため無視されることを意味します。 変更されたコードが構文的に正しい場合、このコードはサーバーによって実行されます。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] がこのステートメントを処理する場合、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] はまず `OrdersTable` から、 `ShipCity` が `Redmond`であるレコードをすべて選択します。 その後、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] は `OrdersTable`を削除します。  
  
 挿入された SQL コードが構文的に正しい限り、改ざんをプログラムによって検出するのは不可能です。 このため、すべてのユーザー入力を検証し、使用しているサーバーで作成された SQL コマンドを実行するコードを注意深く確認する必要があります。 このトピックの次のセクションでは、コーディングのベスト プラクティスについて説明します。  
  
## <a name="validate-all-input"></a>すべての入力の検証  
 型、長さ、形式、および範囲をテストすることによってユーザー入力を必ず検証してください。 悪意のある入力を未然に防ぐには、アプリケーションのアーキテクチャおよび配置シナリオについて検討する必要があります。 セキュリティで保護された環境で実行するようにデザインされたプログラムでも、セキュリティで保護されていない環境にコピーされる可能性があることに注意してください。 次の提案をベスト プラクティスとして検討してください。  
  
-   アプリケーションによって受信されるデータのサイズ、型、内容を推測で処理しない。 たとえば、次のような評価を行う必要があります。  
  
    -   郵便番号の入力がアプリケーションから期待されている場所に対して、ユーザーの誤りあるいは悪意のあるユーザーによって 10 MB の MPEG ファイルが入力された場合、アプリケーションはどのような動作をするか。  
  
    -   `DROP TABLE` ステートメントがテキスト フィールドに埋め込まれている場合、アプリケーションはどのような動作をするか。  
  
-   入力のサイズとデータ型をテストし、適切な制限を適用する。 これは、意図的なバッファー オーバーランを防ぐのに役立ちます。  
  
-   文字列変数の内容をテストし、予測される値のみを受け入れる。 バイナリ データ、エスケープ シーケンス、およびコメント文字を含む入力は拒否します。 これは、スクリプト インジェクションを防ぐのに役立ち、バッファー オーバーランをねらった攻撃に対する防御にもなります。  
  
-   XML ドキュメントを扱う場合、入力時にすべてのデータをスキーマに照らして検証する。  
  
-   [!INCLUDE[tsql](../../includes/tsql-md.md)] ステートメントをユーザー入力から直接構築しない。  
  
-   ストアド プロシージャを使用して、ユーザー入力を検証する。  
  
-   多層環境では、信頼関係ゾーンへ入る前にすべてのデータを検証する必要がある。 検証プロセスをパスしないデータは拒否し、直前の層にエラーを返す必要があります。  
  
-   複数層の検証を実装する。 軽い悪意を持つユーザーに対する予防策は、決然たる攻撃者に対しては有効ではありません。 より適切な実践方法は、ユーザー インターフェイスを介した入力時に検証を行い、その後の信頼境界を越えるすべてのポイントでも検証を行うことです。   
    たとえば、クライアント側アプリケーションでのデータ検証によって、単純なスクリプト インジェクションを防ぐことができます。 ただし、次の層で、この入力が既に検証済みであると推測されると、クライアントを迂回する能力を持つ悪意のあるユーザーは、システムへ無制限にアクセスできることになります。  
  
-   検証されていないユーザー入力は連結しない。 文字列の連結は、スクリプト インジェクションを行うための主なポイントとなります。  
  
-   ファイル名の作成に使用できるフィールドでは、次の文字列を受け付けない:AUX、CLOCK$、COM1 から COM8、CON、CONFIG$、LPT1 から LPT8、NUL、PRN。  
  
 可能であれば、次の文字を含む入力は受け入れないでください。  
  
|入力文字|Transact-SQL での意味|  
|---------------------|------------------------------|  
|**;**|クエリの区切り記号。|  
|**'**|文字データ文字列の区切り記号。|  
|**--**|単一行コメントの区切り記号。 **--** 以降、その行の末尾までのテキストは、サーバーによって評価されません。|  
|**/\**_ ... _*\*/**|コメントの区切り記号。 **/\**と*\*/** の間にあるテキストについては、サーバーによる評価は行われません。|  
|**xp_**|`xp_cmdshell`など、カタログ拡張ストアド プロシージャ名の先頭に使用します。|  
  
### <a name="use-type-safe-sql-parameters"></a>Type-Safe SQL パラメーターの使用  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の **Parameters** コレクションは、型のチェックおよび長さの検証に使用できます。 **Parameters** コレクションを使用する場合、入力は実行可能コードとしてではなくリテラル値として扱われます。 **Parameters** コレクションを使用することのもう 1 つの利点は、型のチェックおよび長さのチェックを適用できることです。 範囲外の値が入力されると例外が発生します。 次のコード フラグメントは、 **Parameters** コレクションの使用方法を示しています。  
  
```csharp
SqlDataAdapter myCommand = new SqlDataAdapter("AuthorLogin", conn);  
myCommand.SelectCommand.CommandType = CommandType.StoredProcedure;  
SqlParameter parm = myCommand.SelectCommand.Parameters.Add("@au_id",  
     SqlDbType.VarChar, 11);  
parm.Value = Login.Text;  
```  
  
 この例では、 `@au_id` パラメーターは実行可能コードとしてではなくリテラル値として扱われます。 この値は型および長さについてチェックされます。 `@au_id` の値が指定された型および長さの制約に従っていない場合は、例外がスローされます。  
  
### <a name="use-parameterized-input-with-stored-procedures"></a>ストアド プロシージャとパラメーター化された入力の使用  
 ストアド プロシージャがフィルターされていない入力を使用する場合、このストアド プロシージャは SQL インジェクションの影響を受けやすくなります。 たとえば、次のコードには脆弱性があります。  
  
```csharp
SqlDataAdapter myCommand =   
new SqlDataAdapter("LoginStoredProcedure '" +   
                               Login.Text + "'", conn);  
```  
  
 ストアド プロシージャを使用する場合、パラメーターをストアド プロシージャの入力として使用する必要があります。  
  
### <a name="use-the-parameters-collection-with-dynamic-sql"></a>動的な SQL を使用した Parameters コレクションの使用  
 ストアド プロシージャを使用できない場合でも、次のコード例に示すようにパラメーターを使用することができます。  
  
```csharp
SqlDataAdapter myCommand = new SqlDataAdapter(  
"SELECT au_lname, au_fname FROM Authors WHERE au_id = @au_id", conn);  
SQLParameter parm = myCommand.SelectCommand.Parameters.Add("@au_id",   
                        SqlDbType.VarChar, 11);  
Parm.Value = Login.Text;  
```  
  
### <a name="filtering-input"></a>入力のフィルター  
 入力をフィルターすると、エスケープ文字が削除されることで SQL インジェクションの防御に役立ちます。 ただし、問題となり得る文字は数が多いため、信頼性の高い防御策にはなりません。 次の例では、文字の文字列区切り記号を検索しています。  
  
```csharp
private string SafeSqlLiteral(string inputSQL)  
{  
  return inputSQL.Replace("'", "''");  
}  
```  
  
### <a name="like-clauses"></a>LIKE 句  
 `LIKE` 句を使用している場合、ワイルドカード文字もエスケープする必要があることに注意してください。  
  
```csharp
s = s.Replace("[", "[[]");  
s = s.Replace("%", "[%]");  
s = s.Replace("_", "[_]");  
```  
  
## <a name="reviewing-code-for-sql-injection"></a>SQL インジェクションのコードの確認  
 `EXECUTE`、 `EXEC`、または `sp_executesql`を呼び出すすべてのコードを確認する必要があります。 次のようなクエリを使用すると、これらのステートメントを含むプロシージャの識別に役立てることができます。 このクエリは、 `EXECUTE` または `EXEC`という語の後に 1 ～ 4 個のスペースがあるかどうかをチェックします。  
  
```sql
SELECT object_Name(id) FROM syscomments  
WHERE UPPER(text) LIKE '%EXECUTE (%'  
OR UPPER(text) LIKE '%EXECUTE  (%'  
OR UPPER(text) LIKE '%EXECUTE   (%'  
OR UPPER(text) LIKE '%EXECUTE    (%'  
OR UPPER(text) LIKE '%EXEC (%'  
OR UPPER(text) LIKE '%EXEC  (%'  
OR UPPER(text) LIKE '%EXEC   (%'  
OR UPPER(text) LIKE '%EXEC    (%'  
OR UPPER(text) LIKE '%SP_EXECUTESQL%';  
```  
  
### <a name="wrapping-parameters-with-quotename-and-replace"></a>QUOTENAME() および REPLACE() でのパラメーターのラップ  
 選択された各ストアド プロシージャで、動的な Transact-SQL で使用されるすべての変数が正しく処理されることを確認します。 ストアド プロシージャの入力パラメーターから取得するデータ、またはテーブルから読み取るデータは、QUOTENAME() または REPLACE() でラップする必要があります。 QUOTENAME() に渡される @variable の値のデータ型は sysname であり、文字列の最大長は 128 文字であることに注意してください。  
  
|\@variable|推奨ラッパー|  
|---------------|-------------------------|  
|セキュリティ保護可能なリソースの名前|`QUOTENAME(@variable)`|  
|128 文字以下の文字列|`QUOTENAME(@variable, '''')`|  
|128 文字より長い文字列|`REPLACE(@variable,'''', '''''')`|  
  
 この方法を使用すると、SET ステートメントを次のように変更できます。  
  
```sql
-- Before:  
SET @temp = N'SELECT * FROM authors WHERE au_lname ='''   
 + @au_lname + N'''';  
  
-- After:  
SET @temp = N'SELECT * FROM authors WHERE au_lname = '''   
 + REPLACE(@au_lname,'''','''''') + N'''';  
```  
  
### <a name="injection-enabled-by-data-truncation"></a>データの切り捨てによって有効になるインジェクション  
 変数に代入されるすべての動的な [!INCLUDE[tsql](../../includes/tsql-md.md)] は、その変数に割り当てられているバッファーよりも大きい場合は切り捨てられます。 予期しない長さの文字列をストアド プロシージャに渡すことで、強制的にステートメントの切り捨てを行うことができれば、攻撃者が結果を操作することも可能になります。 たとえば、次のスクリプトによって作成されるストアド プロシージャは、切り捨てによって有効になるインジェクションの影響を受けやすくなります。  
  
```sql
CREATE PROCEDURE sp_MySetPassword  
@loginname sysname,  
@old sysname,  
@new sysname  
AS  
-- Declare variable.  
-- Note that the buffer here is only 200 characters long.   
DECLARE @command varchar(200)  
-- Construct the dynamic Transact-SQL.  
-- In the following statement, we need a total of 154 characters   
-- to set the password of 'sa'.   
-- 26 for UPDATE statement, 16 for WHERE clause, 4 for 'sa', and 2 for  
-- quotation marks surrounded by QUOTENAME(@loginname):  
-- 200 - 26 - 16 - 4 - 2 = 154.  
-- But because @new is declared as a sysname, this variable can only hold  
-- 128 characters.   
-- We can overcome this by passing some single quotation marks in @new.  
SET @command= 'update Users set password='   
    + QUOTENAME(@new, '''') + ' where username='   
    + QUOTENAME(@loginname, '''') + ' AND password = '   
    + QUOTENAME(@old, '''')  
  
-- Execute the command.  
EXEC (@command)  
GO  
```  
  
 攻撃者は、sa の古いパスワードがわからなくても、128 文字バッファーに 154 文字を渡して新しいパスワードを設定できます。  
  
```sql
EXEC sp_MySetPassword 'sa', 'dummy',   
'123456789012345678901234567890123456789012345678901234567890123456789012345678901234567890123456789012'''''''''''''''''''''''''''''''''''''''''''''''''''   
```  
  
 このため、コマンド変数には大きなバッファーを使用するか、 [!INCLUDE[tsql](../../includes/tsql-md.md)] は直接 `EXECUTE` ステートメント内で動的に実行する必要があります。  
  
### <a name="truncation-when-quotenamevariable--and-replace-are-used"></a>QUOTENAME(@variable, '''') および REPLACE() 使用時の切り捨て  
 QUOTENAME() および REPLACE() から返される文字列は、割り当てられている領域よりも大きくなると、暗黙に切り捨てられます。 次の例で作成されるストアド プロシージャは、行われる可能性がある処理を示しています。  
  
```sql
CREATE PROCEDURE sp_MySetPassword  
    @loginname sysname,  
    @old sysname,  
    @new sysname  
AS  
  
-- Declare variables.  
    DECLARE @login sysname  
    DECLARE @newpassword sysname  
    DECLARE @oldpassword sysname  
    DECLARE @command varchar(2000)  
  
-- In the following statements, the data stored in temp variables  
-- will be truncated because the buffer size of @login, @oldpassword,  
-- and @newpassword is only 128 characters, but QUOTENAME() can return  
-- up to 258 characters.  
    SET @login = QUOTENAME(@loginname, '''')  
    SET @oldpassword = QUOTENAME(@old, '''')  
    SET @newpassword = QUOTENAME(@new, '''')  
  
-- Construct the dynamic Transact-SQL.  
-- If @new contains 128 characters, then @newpassword will be '123... n  
-- where n is the 127th character.   
-- Because the string returned by QUOTENAME() will be truncated,   
-- it can be made to look like the following statement:  
-- UPDATE Users SET password ='1234. . .[127] WHERE username=' -- other stuff here  
    SET @command = 'UPDATE Users set password = ' + @newpassword   
     + ' where username =' + @login + ' AND password = ' + @oldpassword;  
  
-- Execute the command.  
EXEC (@command);  
GO  
```  
  
 このため、次のステートメントでは、すべてのユーザーのパスワードを前のコードで渡された値に設定します  
  
```sql
EXEC sp_MyProc '--', 'dummy', '12345678901234567890123456789012345678901234567890123456789012345678901234567890123456789012345678901234567890123456789012345678'  
```  
  
 REPLACE() を使用すると、割り当てられたバッファー領域よりも大きくすることで強制的に文字列の切り捨てを行うことができます。 次の例で作成されるストアド プロシージャは、行われる可能性がある処理を示しています。  
  
```sql
CREATE PROCEDURE sp_MySetPassword  
    @loginname sysname,  
    @old sysname,  
    @new sysname  
AS  
  
-- Declare variables.  
    DECLARE @login sysname  
    DECLARE @newpassword sysname  
    DECLARE @oldpassword sysname  
    DECLARE @command varchar(2000)  
  
-- In the following statements, data will be truncated because   
-- the buffers allocated for @login, @oldpassword and @newpassword   
-- can hold only 128 characters, but QUOTENAME() can return   
-- up to 258 characters.   
    SET @login = REPLACE(@loginname, '''', '''''')  
    SET @oldpassword = REPLACE(@old, '''', '''''')  
    SET @newpassword = REPLACE(@new, '''', '''''')  
  
-- Construct the dynamic Transact-SQL.  
-- If @new contains 128 characters, @newpassword will be '123...n   
-- where n is the 127th character.   
-- Because the string returned by QUOTENAME() will be truncated, it  
-- can be made to look like the following statement:  
-- UPDATE Users SET password='1234...[127] WHERE username=' -- other stuff here   
    SET @command= 'update Users set password = ''' + @newpassword + ''' where username='''   
     + @login + ''' AND password = ''' + @oldpassword + '''';  
  
-- Execute the command.  
EXEC (@command);  
GO  
```  
  
 QUOTENAME() と同様に、すべての場合に対して十分な大きさである一時変数を宣言することで、REPLACE() による文字列の切り捨てを回避できます。 可能であれば、動的な [!INCLUDE[tsql](../../includes/tsql-md.md)]内で QUOTENAME() または REPLACE() を直接呼び出すことをお勧めします。 あるいは、必要なバッファー サイズを次のように計算できます。 `@outbuffer = QUOTENAME(@input)`の場合、 `@outbuffer` のサイズは `2*(len(@input)+1)`にする必要があります。 前の例のように、 `REPLACE()` を使用し、二重引用符を繰り返すときは、バッファー サイズは `2*len(@input)` で十分です。  
  
 次の計算は、すべての場合に対応します。  
  
```sql
WHILE LEN(@find_string) > 0, required buffer size =  
ROUND(LEN(@input)/LEN(@find_string),0) * LEN(@new_string)   
 + (LEN(@input) % LEN(@find_string))  
```  
  
### <a name="truncation-when-quotenamevariable--is-used"></a>QUOTENAME(@variable, ']') 使用時の切り捨て  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のセキュリティ保護可能なリソースの名前が `QUOTENAME(@variable, ']')`という形式のステートメントに渡されると、切り捨てが発生する可能性があります。 次に例を示します。  
  
```sql
CREATE PROCEDURE sp_MyProc  
    @schemaname sysname,  
    @tablename sysname,  
AS  
  
-- Declare a variable as sysname. The variable will be 128 characters.  
-- But @objectname actually must allow for 2*258+1 characters.   
DECLARE @objectname sysname  
SET @objectname = QUOTENAME(@schemaname)+'.'+ QUOTENAME(@tablename)   
-- Do some operations.  
GO  
```  
  
 型 sysname の値を連結する場合、値ごとに最大 128 文字を十分保持できる大きさの一時変数を使用する必要があります。 可能であれば、動的な `QUOTENAME()` 内で [!INCLUDE[tsql](../../includes/tsql-md.md)]を直接呼び出します。 あるいは、前述のように必要なバッファー サイズを計算できます。  
  
## <a name="see-also"></a>参照  
 [EXECUTE &#40;Transact-SQL&#41;](../../t-sql/language-elements/execute-transact-sql.md)   
 [REPLACE &#40;Transact-SQL&#41;](../../t-sql/functions/replace-transact-sql.md)   
 [QUOTENAME &#40;Transact-SQL&#41;](../../t-sql/functions/quotename-transact-sql.md)   
 [sp_executesql &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-executesql-transact-sql.md)   
 [SQL Server の保護](../../relational-databases/security/securing-sql-server.md)  
  
  
