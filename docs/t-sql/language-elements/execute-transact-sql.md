---
description: EXECUTE (Transact-SQL)
title: EXECUTE (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 08/07/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- EXEC
- EXECUTE_TSQL
- EXECUTE
- EXEC_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- remote stored procedures [SQL Server]
- command strings [SQL Server]
- extended stored procedures [SQL Server], executing
- stored procedures [SQL Server], executing
- Transact-SQL statements, executing
- strings [SQL Server], executing
- statements [SQL Server], executing
- context switching [SQL Server], execution context
- user-defined functions [SQL Server], executing
- character strings [SQL Server], executing
- switching execution context
- EXECUTE statement
ms.assetid: bc806b71-cc55-470a-913e-c5f761d5c4b7
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 37acb46f1e8fd238703d5d6af1d7fd84881f032f
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104749892"
---
# <a name="execute-transact-sql"></a>EXECUTE (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[tsql](../../includes/tsql-md.md)] バッチ内のコマンド文字列や文字の列、またはシステム ストアド プロシージャ、ユーザー定義のストアド プロシージャ、CLR ストアド プロシージャ、スカラー値ユーザー定義関数、または拡張ストアド プロシージャのモジュールのうちのいずれかを実行します。 EXECUTE ステートメントを使用して、パススルー コマンドをリンク サーバーに送信できます。 さらに、文字列またはコマンドを実行するコンテキストを、明示的に設定できるようになりました。 WITH RESULT SETS オプションを使用すると、結果セットのメタデータを定義できます。
  
> [!IMPORTANT]  
>  文字列で EXECUTE を呼び出す前には、文字列を検証してください。 また、検証されていないユーザー入力から作成されるコマンドは実行しないでください。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  

::: moniker range=">=sql-server-ver15"
次のコード ブロックは、SQL Server 2019 の構文を示しています。 または、[SQL Server 2017 以前の構文](execute-transact-sql.md?view=sql-server-2017&preserve-view=true)を参照してください。 

```syntaxsql
-- Syntax for SQL Server 2019
  
Execute a stored procedure or function  
[ { EXEC | EXECUTE } ]  
    {   
      [ @return_status = ]  
      { module_name [ ;number ] | @module_name_var }   
        [ [ @parameter = ] { value   
                           | @variable [ OUTPUT ]   
                           | [ DEFAULT ]   
                           }  
        ]  
      [ ,...n ]  
      [ WITH <execute_option> [ ,...n ] ]  
    }  
[;]  
  
Execute a character string  
{ EXEC | EXECUTE }   
    ( { @string_variable | [ N ]'tsql_string' } [ + ...n ] )  
    [ AS { LOGIN | USER } = ' name ' ]  
[;]  
  
Execute a pass-through command against a linked server  
{ EXEC | EXECUTE }  
    ( { @string_variable | [ N ] 'command_string [ ? ]' } [ + ...n ]  
        [ { , { value | @variable [ OUTPUT ] } } [ ...n ] ]  
    )   
    [ AS { LOGIN | USER } = ' name ' ]  
    [ AT linked_server_name ]  
    [ AT DATA_SOURCE data_source_name ]  
[;]  
  
<execute_option>::=  
{  
        RECOMPILE   
    | { RESULT SETS UNDEFINED }   
    | { RESULT SETS NONE }   
    | { RESULT SETS ( <result_sets_definition> [,...n ] ) }  
}   
  
<result_sets_definition> ::=   
{  
    (  
         { column_name   
           data_type   
         [ COLLATE collation_name ]   
         [ NULL | NOT NULL ] }  
         [,...n ]  
    )  
    | AS OBJECT   
        [ db_name . [ schema_name ] . | schema_name . ]   
        {table_name | view_name | table_valued_function_name }  
    | AS TYPE [ schema_name.]table_type_name  
    | AS FOR XML   
}  
```  
::: moniker-end

::: moniker range=">=sql-server-2016"

次のコード ブロックは、SQL Server 2017 以前の構文を示しています。 または、[SQL Server 2019 の構文](execute-transact-sql.md?view=sql-server-ver15&preserve-view=true)を参照してください。


```syntaxsql
-- Syntax for SQL Server 2017 and earleir  
  
Execute a stored procedure or function  
[ { EXEC | EXECUTE } ]  
    {   
      [ @return_status = ]  
      { module_name [ ;number ] | @module_name_var }   
        [ [ @parameter = ] { value   
                           | @variable [ OUTPUT ]   
                           | [ DEFAULT ]   
                           }  
        ]  
      [ ,...n ]  
      [ WITH <execute_option> [ ,...n ] ]  
    }  
[;]  
  
Execute a character string  
{ EXEC | EXECUTE }   
    ( { @string_variable | [ N ]'tsql_string' } [ + ...n ] )  
    [ AS { LOGIN | USER } = ' name ' ]  
[;]  
  
Execute a pass-through command against a linked server  
{ EXEC | EXECUTE }  
    ( { @string_variable | [ N ] 'command_string [ ? ]' } [ + ...n ]  
        [ { , { value | @variable [ OUTPUT ] } } [ ...n ] ]  
    )   
    [ AS { LOGIN | USER } = ' name ' ]  
    [ AT linked_server_name ]  
[;]  
  
<execute_option>::=  
{  
        RECOMPILE   
    | { RESULT SETS UNDEFINED }   
    | { RESULT SETS NONE }   
    | { RESULT SETS ( <result_sets_definition> [,...n ] ) }  
}   
  
<result_sets_definition> ::=   
{  
    (  
         { column_name   
           data_type   
         [ COLLATE collation_name ]   
         [ NULL | NOT NULL ] }  
         [,...n ]  
    )  
    | AS OBJECT   
        [ db_name . [ schema_name ] . | schema_name . ]   
        {table_name | view_name | table_valued_function_name }  
    | AS TYPE [ schema_name.]table_type_name  
    | AS FOR XML   
}  
```  
::: moniker-end

  
```syntaxsql
-- In-Memory OLTP   

Execute a natively compiled, scalar user-defined function  
[ { EXEC | EXECUTE } ]   
    {   
      [ @return_status = ]   
      { module_name | @module_name_var }   
        [ [ @parameter = ] { value   
                           | @variable   
                           | [ DEFAULT ]   
                           }  
        ]   
      [ ,...n ]   
      [ WITH <execute_option> [ ,...n ] ]   
    }  
<execute_option>::=  
{  
    | { RESULT SETS UNDEFINED }   
    | { RESULT SETS NONE }   
    | { RESULT SETS ( <result_sets_definition> [,...n ] ) }  
}  
```  
  
```syntaxsql
-- Syntax for Azure SQL Database   
  
Execute a stored procedure or function  
[ { EXEC | EXECUTE } ]  
    {   
      [ @return_status = ]  
      { module_name  | @module_name_var }   
        [ [ @parameter = ] { value   
                           | @variable [ OUTPUT ]   
                           | [ DEFAULT ]   
                           }  
        ]  
      [ ,...n ]  
      [ WITH RECOMPILE ]  
    }  
[;]  
  
Execute a character string  
{ EXEC | EXECUTE }   
    ( { @string_variable | [ N ]'tsql_string' } [ + ...n ] )  
    [ AS {  USER } = ' name ' ]  
[;]  
  
<execute_option>::=  
{  
        RECOMPILE   
    | { RESULT SETS UNDEFINED }   
    | { RESULT SETS NONE }   
    | { RESULT SETS ( <result_sets_definition> [,...n ] ) }  
}   
  
<result_sets_definition> ::=   
{  
    (  
         { column_name   
           data_type   
         [ COLLATE collation_name ]   
         [ NULL | NOT NULL ] }  
         [,...n ]  
    )  
    | AS OBJECT   
        [ db_name . [ schema_name ] . | schema_name . ]   
        {table_name | view_name | table_valued_function_name }  
    | AS TYPE [ schema_name.]table_type_name  
    | AS FOR XML  
  
```

  
```syntaxsql
-- Syntax for Azure Synapse Analytics and Parallel Data Warehouse  

-- Execute a stored procedure  
[ { EXEC | EXECUTE } ]  
    procedure_name   
        [ { value | @variable [ OUT | OUTPUT ] } ] [ ,...n ] }  
[;]  
  
-- Execute a SQL string  
{ EXEC | EXECUTE }  
    ( { @string_variable | [ N ] 'tsql_string' } [ +...n ] )  
[;]  
```  


  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
 @*return_status*  
 モジュールから返されるステータス番号を格納する整数型の変数を指定します (省略可能)。 この変数は、EXECUTE ステートメントで使う前に、バッチ、ストアド プロシージャ、または関数の中で宣言する必要があります。  
  
 @*return_status* 変数をユーザー定義のスカラー値関数の呼び出しに使用する場合は、任意のスカラー データ型を指定できます。  
  
 *module_name*  
 呼び出されるストアド プロシージャやユーザー定義のスカラー値関数の、完全修飾名または部分的な修飾名を指定します。 モジュール名は、[識別子](../../relational-databases/databases/database-identifiers.md)の規則に従っている必要があります。 拡張ストアド プロシージャの名前では、サーバーの照合順序に関係なく、常に大文字と小文字が区別されます。  
  
 別のデータベース内で作成されたモジュールを実行するには、実行するユーザーがモジュールを所有しているか、そのデータベース内のモジュールを実行する適切な権限がユーザーに与えられている必要があります。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] を実行している別のサーバーでモジュールを実行するには、実行するユーザーに対して、そのサーバーを使用する適切な権限 (リモート アクセス) と、そのデータベース内のモジュールを実行する適切な権限が与えられている必要があります。 サーバー名だけを指定してデータベース名を指定しない場合、[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]では、ユーザーの既定のデータベース内でモジュールが検索されます。  
  
 ;*number*  
**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降
  
 同じ名前のプロシージャのグループ化に使用される整数です (省略可能)。 このパラメーターは、拡張ストアド プロシージャでは使用できません。  
  
> [!NOTE]  
>  [!INCLUDE[ssNoteDepFutureAvoid](../../includes/ssnotedepfutureavoid-md.md)]  
  
 プロシージャ グループの詳細については、「[CREATE PROCEDURE &#40;Transact-SQL&#41;](../../t-sql/statements/create-procedure-transact-sql.md)」を参照してください。  
  
 @*module_name_var*  
 モジュール名を表すローカル変数の名前を指定します。  
  
 ネイティブ コンパイル、スカラー ユーザー定義関数の名前を保持する変数を指定できます。  
  
 @*parameter*  
 モジュールで定義される、*module_name* 用のパラメーターを指定します。 パラメーター名の先頭にはアット マーク (@) を付ける必要があります。 @*parameter_name*=*value* の形式で使用する場合、パラメーター名と定数は、モジュールで定義されている順序で指定する必要はありません。 ただし、@*parameter_name*=*value* の形式をパラメーターとして使用した場合は、以降のすべてのパラメーターは、この形式で指定する必要があります。  
  
 既定では、パラメーターに NULL 値は許容されます。  
  
 *value*  
 モジュールまたはパススルー コマンドに渡すパラメーターの値を指定します。 パラメーター名を指定しない場合、パラメーター値は、モジュールで定義された順序で指定する必要があります。  
  
 リンク サーバーに対してパススルー コマンドを実行するとき、パラメーター値の順序は、リンク サーバーの OLE DB プロバイダーに依存します。 ほとんどの OLE DB プロバイダーでは、左から右へパラメーターに値がバインドされます。  
  
 パラメーターの値がオブジェクト名や文字列であったり、データベース名やスキーマ名によって修飾されている場合は、その名前全体を単一引用符で囲む必要があります。 パラメーターの値がキーワードの場合は、そのキーワードを二重引用符で囲む必要があります。  
  
`@` で始まらない単語を引用符で囲まずに渡すと (パラメーター名に `@` を忘れた場合など)、この単語は、引用符が不足しているにもかかわらず、nvarchar 文字列として扱われます。

 既定値がモジュール内で定義されている場合、ユーザーはパラメーターを指定せずにモジュールを実行できます。  
  
 既定値を NULL にすることもできます。 パラメーター値が NULL の場合の操作は、通常、モジュールの定義で指定します。  
  
 @*variable*  
 パラメーターと戻りパラメーターを格納する変数です。  
  
 OUTPUT  
 モジュールまたはコマンド文字列でパラメーターを返すよう指定します。 モジュールまたはコマンド文字列内で一致するパラメーターも、キーワード OUTPUT を使って作成されている必要があります。 このキーワードは、カーソル変数をパラメーターとして使用するときに指定します。  
  
 リンク サーバーに対して実行するモジュールの OUTPUT として *value* を定義した場合、対応する @*parameter* に対して、OLE DB プロバイダーによる変更が加えられていると、その変更内容がモジュール実行の最後に変数にコピーされて戻されます。  
  
 OUTPUT パラメーターを使用し、呼び出し元のバッチまたはモジュール内の他のステートメントで戻り値を使用する場合は、パラメーターの値を @*parameter* = @*variable* のように変数として渡す必要があります。 モジュール内で OUTPUT パラメーターとして定義されていないパラメーターには、OUTPUT を指定してモジュールを実行することはできません。 OUTPUT を使って定数をモジュールに引き渡すことはできません。戻りパラメーターには変数名が必要です。 プロシージャを実行する前に、必ず変数のデータ型を宣言し、値を割り当ててください。  
  
 リモート ストアド プロシージャに対して EXECUTE を使用する場合や、リンク サーバーに対してパススルー コマンドを実行する場合は、OUTPUT パラメーターにラージ オブジェクト (LOB) 型を指定することはできません。  
  
 戻りパラメーターには、LOB 型以外のデータ型を指定できます。  
  
 DEFAULT  
 モジュールで定義されているパラメーターの既定値を使用します。 パラメーターに値を必要とするモジュールでパラメーターの既定値が定義されていない場合、パラメーターを指定しなかったり、DEFAULT キーワードを指定したりすると、エラーが発生します。  
  
 @*string_variable*  
 ローカル変数の名前を指定します。 @*string_variable* には、**char**、**varchar**、**nchar**、または **nvarchar** のいずれかのデータ型を指定できます。 これには、 **(max)** データ型が含まれます。  
  
 [N] '*tsql_string*'  
 定数文字列を指定します。 *tsql_string* には、**nvarchar** または **varchar** のいずれかのデータ型を指定できます。 N が含まれる場合、文字列は **nvarchar** データ型として解釈されます。  
  
 AS \<context_specification>  
 ステートメントを実行するコンテキストを指定します。  
  
 Login  
**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降
  
 権限を借用するコンテキストがログインであることを指定します。 権限借用のスコープはサーバーです。  
  
 User  
 権限を借用するコンテキストが、現在のデータベース内のユーザーであることを指定します。 権限借用のスコープは、現在のデータベースに限定されます。 コンテキスト スイッチの対象がデータベース ユーザーであっても、そのユーザーのサーバー レベルの権限は継承されません。  
  
> [!IMPORTANT]  
>  データベース ユーザーに対するコンテキスト スイッチがアクティブであるときに、データベース外部のリソースへアクセスを試みると、ステートメントが失敗する原因となります。 たとえば、USE *database* ステートメントや分散クエリ、3 部または 4 部構成の識別子を使用する別のデータベースを参照するクエリなどは実行しないでください。  
  
 '*name*'  
 有効なユーザーまたはログイン名を指定します。 *name* は、固定サーバー ロール sysadmin のメンバーであるか、[sys.database_principals](../../relational-databases/system-catalog-views/sys-database-principals-transact-sql.md) または [sys.server_principals](../../relational-databases/system-catalog-views/sys-server-principals-transact-sql.md) のプリンシパルとして存在する必要があります。  
  
 *name* には、NT AUTHORITY\LocalService、NT AUTHORITY\NetworkService、NT AUTHORITY\LocalSystem などのビルトイン アカウントは指定できません。  
  
 詳しくは、後の「[ユーザーまたはログイン名の指定](#_user)」をご覧ください。  
  
 [N] '*command_string*'  
 リンク サーバーにパススルーされるコマンドを含む定数文字列を指定します。 N が含まれる場合、文字列は **nvarchar** データ型として解釈されます。  
  
 [?]  
 パススルー コマンドの \<arg-list> で値が提供されるパラメーターを表します。このパススルー コマンドは、EXEC('...', \<arg-list>) AT \<linkedsrv> ステートメントで使用されるものです。  
  
 AT *linked_server_name*  
**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降
  
 *command_string* を *linked_server_name* に対して実行し、結果が返された場合はそれをクライアントに返します。 *linked_server_name* は、ローカル サーバー内の既存のリンク サーバー定義を参照している必要があります。 リンク サーバーは、[sp_addlinkedserver](../../relational-databases/system-stored-procedures/sp-addlinkedserver-transact-sql.md) を使って定義されます。  
  
 WITH \<execute_option>  
 実行オプションは次のとおりです。 RESULT SETS オプションは INSERT...EXEC ステートメントで指定できません。  
 
AT DATA_SOURCE data_source_name **適用対象**: [!INCLUDE[sssql19](../../includes/sssql19-md.md)] 以降
  
 *command_string* を *data_source_name* に対して実行し、結果が返された場合はそれをクライアントに返します。 *data_source_name* は、データベース内の既存の EXTERNAL DATA SOURCE 定義を参照する必要があります。 SQL Server をポイントするデータ ソースのみがサポートされます。 さらに、コンピューティング プールをポイントする SQL Server ビッグ データ クラスター データ ソースの場合、データ プールまたは記憶域プールがサポートされます。 データ ソースは [CREATE EXTERNAL DATA SOURCE](../statements/create-external-data-source-transact-sql.md) を使用して定義されます。  
  
 WITH \<execute_option>  
 実行オプションは次のとおりです。 RESULT SETS オプションは INSERT...EXEC ステートメントで指定できません。  
  
|期間|定義|  
|----------|----------------|  
|RECOMPILE|モジュール実行後に、新しいプランを強制的にコンパイル、使用、および破棄します。 モジュールに既存のクエリ プランがある場合、このプランはキャッシュに残ります。<br /><br /> 指定するパラメーターが一定しない場合であったり、データが大きく変更されたときにこのオプションを使用してください。 このオプションは、拡張ストアド プロシージャには使用しません。 このオプションは負荷を伴うので、あまり使用しないことをお勧めします。<br /><br /> **注:** OPENDATASOURCE 構文を使用するストアド プロシージャを呼び出す場合、WITH RECOMPILE は使用できません。 4 部構成のオブジェクト名が指定されている場合、WITH RECOMPILE オプションは無視されます。<br /><br /> **注:** RECOMPILE は、ネイティブにコンパイルされるスカラー ユーザー定義関数ではサポートされていません。 再コンパイルする必要がある場合は、[sp_recompile &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-recompile-transact-sql.md) を使用してください。|  
|**RESULT SETS UNDEFINED**|**適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降、[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。<br /><br /> このオプションを使用した場合、返される結果の種類は保証されず、定義は指定されません。 ステートメントは、何かの結果が返される場合でも、結果が返されない場合でも、問題なく実行されます。 result_sets_option を指定しない場合は、RESULT SETS UNDEFINED が既定の動作となります。<br /><br /> 解釈されたスカラー ユーザー定義関数、およびネイティブ コンパイルのスカラー ユーザー定義関数の場合、関数が結果セットを返すことがないため、このオプションは機能しません。|  
|RESULT SETS NONE|**適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降、[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。<br /><br /> 実行ステートメントによって結果が一切返されなくなります。 結果が返された場合、バッチは中断されます。<br /><br /> 解釈されたスカラー ユーザー定義関数、およびネイティブ コンパイルのスカラー ユーザー定義関数の場合、関数が結果セットを返すことがないため、このオプションは機能しません。|  
|*\<result_sets_definition>*|**適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降、[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。<br /><br /> result_sets_definition で指定されたとおりに結果が返されるようになります。 複数の結果セットを返すステートメントの場合は、複数の *result_sets_definition* セクションを指定してください。 その際には、各 *result_sets_definition* をかっこで囲み、コンマで区切ります。 詳細については、後述する「\<result_sets_definition>」を参照してください。<br /><br /> ネイティブ コンパイルのスカラー ユーザー定義関数の場合、関数が結果セットを返すことがないため、このオプションの結果は常にエラーになります。|
  
\<result_sets_definition>
**適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降、[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]
  
 実行されたステートメントによって返される結果セットの定義です。 result_sets_definition の句には、次の意味があります。  
  
|期間|定義|  
|----------|----------------|  
|{<br /><br /> column_name<br /><br /> data_type<br /><br /> [ COLLATE collation_name]<br /><br /> [NULL &#124; NOT NULL]<br /><br /> }|次の表を参照してください。|  
|db_name|テーブル、ビュー、またはテーブル値関数を含むデータベースの名前です。|  
|schema_name|テーブル、ビュー、またはテーブル値関数を所有するスキーマの名前です。|  
|table_name &#124; view_name &#124; table_valued_function_name|名前付きのテーブル、ビュー、またはテーブル値関数で指定された列を返すように指定します。 テーブル変数、一時テーブル、およびシノニムは、AS オブジェクト構文ではサポートされていません。|  
|AS TYPE [schema_name.]table_type_name|テーブル型で指定された列を返すように指定します。|  
|AS FOR XML|EXECUTE ステートメントで呼び出されたステートメントまたはストアド プロシージャからの XML の結果を、SELECT ... FOR XML ... ステートメントで生成された場合と同様の形式に変換するように指定します。 元のステートメントの TYPE ディレクティブからのすべての書式設定が削除され、TYPE ディレクティブが指定されなかったものとして、結果が返されます。 AS FOR XML では、実行されたステートメントまたはストアド プロシージャからの XML 以外の表形式の結果は XML に変換されません。|  
  
|期間|定義|  
|----------|----------------|  
|column_name|各列の名前です。 列の数が結果セットと異なる場合は、エラーが発生し、バッチが中断されます。 列の名前が結果セットと異なる場合は、返される列の名前は定義済みの名前に設定されます。|  
|data_type|各列のデータ型です。 データ型が異なる場合は、定義済みのデータ型への暗黙的な変換が行われます。 変換に失敗すると、バッチが中断されます。|  
|COLLATE collation_name|各列の照合順序です。 照合順序の不一致がある場合、暗黙的な照合順序が使用されます。 これに失敗した場合は、バッチが中断されます。|  
|NULL &#124; NOT NULL|各列の NULL 値の許容属性です。 定義済みの NULL 値の許容属性が NOT NULL である場合に、返されるデータに NULL が含まれていると、エラーが発生し、バッチは中断されます。 指定しない場合、既定値は ANSI_NULL_DFLT_ON および ANSI_NULL_DFLT_OFF オプションの設定に従った値になります。|  
  
 実行中に返される実際の結果セットは、結果セットの数、列の数、列の名前、NULL 値の許容属性、およびデータ型のいずれかが WITH RESULT SETS 句を使用して定義された結果とは異なる場合があります。 結果セットの数が異なる場合は、エラーが発生し、バッチが中断されます。  
  
## <a name="remarks"></a>解説  
 *value* を使用するか、@*parameter_name*=*value* を使用し、パラメーターを指定できます。 パラメーターは、トランザクションの一部ではないです。そのため、トランザクションが後でロールバックの値にパラメーターが変更された場合、パラメーター戻すことはできません前の値にします。 呼び出し元に返される値は常に、モジュールから戻る時点での値になります。  
  
 1 つのモジュールで、別のモジュールが呼び出されるか、共通言語ランタイム (CLR) モジュール、ユーザー定義型、または集計の参照によりマネージド コードが実行されるとき、入れ子が発生します。 入れ子のレベルは、呼び出されたモジュールまたはマネージド コード参照の実行開始時に増加し、呼び出されたモジュールやマネージド コード参照の終了時に減少します。 入れ子のレベルが最大値 32 を超えると、呼び出しチェーン全体が失敗します。 現在の入れ子レベルは、@@NESTLEVEL システム関数に格納されます。  
  
 リモート プロシージャと拡張ストアド プロシージャは、BEGIN DISTRIBUTED TRANSACTION ステートメントの中で実行されない限り、または各種の構成オプションと共に使用されない限り、トランザクションのスコープ外となります。したがって、これらのプロシージャを呼び出して実行したコマンドはロールバックできません。 詳細については「[システム ストアド プロシージャ &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/system-stored-procedures-transact-sql.md)」と「[BEGIN DISTRIBUTED TRANSACTION &#40;Transact-SQL&#41;](../../t-sql/language-elements/begin-distributed-transaction-transact-sql.md)」を参照してください。  
  
 カーソル変数を使用するとき、カーソル変数にカーソルを渡すプロシージャを実行し、このカーソルがプロシージャに割り当てられているカーソルであった場合は、エラーが発生します。  
  
 ステートメントがバッチ内の最初のステートメントの場合は、モジュールを実行するときに EXECUTE キーワードを指定する必要はありません。  
  
 CLR ストアド プロシージャに固有のその他の情報については、「CLR ストアド プロシージャ」を参照してください。  
  
## <a name="using-execute-with-stored-procedures"></a>ストアド プロシージャでの EXECUTE の使用  
 ステートメントがバッチ内の最初のステートメントの場合は、ストアド プロシージャを実行するときに EXECUTE キーワードを指定する必要はありません。  
  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] システム ストアド プロシージャは、sp_ で始まります。 システム ストアド プロシージャは、物理的には[リソース データベース](../../relational-databases/databases/resource-database.md)に格納されますが、論理的にはシステムおよびユーザー定義の各データベースの sys スキーマに属します。 システム ストアド プロシージャを、バッチ内、またはユーザー定義ストアド プロシージャや関数などのモジュール内で実行する場合は、ストアド プロシージャ名に sys スキーマ名を追加することをお勧めします。  
  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] システム拡張ストアド プロシージャは、文字 xp_ で始まり、master データベースの dbo スキーマに含まれています。 システム拡張ストアド プロシージャを、バッチ内、またはユーザー定義ストアド プロシージャや関数などのモジュール内で実行する場合は、ストアド プロシージャ名に master.dbo を追加することをお勧めします。  
  
 ユーザー定義ストアド プロシージャを、バッチ内、またはユーザー定義ストアド プロシージャや関数などのモジュール内で実行する場合は、ストアド プロシージャ名にスキーマ名を追加することをお勧めします。 ユーザー定義ストアド プロシージャに、システム ストアド プロシージャと同じ名前を付けることはお勧めしません。 ストアド プロシージャの実行の詳細については、「[ストアド プロシージャの実行](../../relational-databases/stored-procedures/execute-a-stored-procedure.md)」を参照してください。  
  
## <a name="using-execute-with-a-character-string"></a>文字列での EXECUTE の使用  
 以前のバージョンの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、文字列は 8,000 バイトに制限されています。 このため、動的実行では大きな文字列を連結する必要があります。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、**varchar(max)** 型と **nvarchar(max)** 型を指定でき、2 GB までの文字列データを使用できるようになりました。  
  
 データベース コンテキスト内の変更が維持されるのは、EXECUTE ステートメントの終了時までです。 たとえば、次のステートメントの `EXEC` を実行すると、データベース コンテキストは master になります。  
  
```sql  
USE master; EXEC ('USE AdventureWorks2012; SELECT BusinessEntityID, JobTitle FROM HumanResources.Employee;');  
```  
  
## <a name="context-switching"></a>コンテキストの切り替え  
 `AS { LOGIN | USER } = ' name '` 句を使用して、動的ステートメントの実行コンテキストを切り替えることができます。 コンテキスト スイッチを `EXECUTE ('string') AS <context_specification>` のように指定した場合、コンテキスト スイッチは、実行するクエリのスコープでのみ有効になります。  
  
###  <a name="specifying-a-user-or-login-name"></a><a name="_user"></a> ユーザーまたはログイン名の指定  
 `AS { LOGIN | USER } = ' name '` で指定するユーザーまたはログイン名は、sys.database_principals または sys.server_principals の各プリンシパルとして存在する必要があります。存在しない場合、ステートメントは失敗します。 さらに、プリンシパルで IMPERSONATE 権限が許可されている必要があります。 呼び出し元がデータベース所有者または sysadmin 固定サーバー ロールのメンバーでない場合は、ユーザーが Windows グループ メンバーシップによって [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のデータベースやインスタンスにアクセスしているときでも、プリンシパルは存在する必要があります。 たとえば、次のような条件を想定します。  
  
-   CompanyDomain\SQLUsers グループに Sales データベースへのアクセス権がある。  
  
-   CompanyDomain\SqlUser1 は SQLUsers のメンバーであり、したがって Sales データベースへのアクセスが暗黙的に許可されている。  
  
 この場合、CompanyDomain\SqlUser1 は SQLUsers グループのメンバーシップを介してデータベースにアクセスすることができますが、`CompanyDomain\SqlUser1` がプリンシパルとしてデータベースに存在していないので、ステートメント `EXECUTE @string_variable AS USER = 'CompanyDomain\SqlUser1'` は失敗します。  
  
### <a name="best-practices"></a>ベスト プラクティス  
 ステートメントまたはモジュールで定義されている操作の実行に必要な、最小の権限が与えられているログインまたはユーザーを指定します。 たとえば、データベース レベルの権限だけが必要な場合、サーバー レベルの権限が与えられているログイン名は指定しません。また、データベース所有者アカウントは、データベース レベルの権限が必要とされない場合は指定しません。  
  
## <a name="permissions"></a>アクセス許可  
 EXECUTE ステートメントの実行に権限は必要ありませんが、 EXECUTE 文字列内で参照されるセキュリティ保護可能なリソースに対しては権限が必要です。 たとえば、この文字列に INSERT ステートメントが含まれている場合、EXECUTE ステートメントの呼び出し元は対象のテーブルに対する INSERT 権限が必要です。 EXECUTE ステートメントは、モジュール内に含まれている場合でも、検出されるたびに権限が確認されます。  
  
 モジュールの EXECUTE 権限は、既定ではモジュールの所有者に与えられており、所有者はその権限を別のユーザーに譲渡できます。 文字列を実行するモジュールが実行されるとき、権限は、モジュールを作成したユーザーのコンテキストではなく、モジュールを実行しているユーザーのコンテキストに基づいて確認されます。 ただし、呼び出すモジュールと呼び出されるモジュールを所有するユーザーが同じ場合、2 番目のモジュールに対しては EXECUTE 権限の確認は行われません。  
  
 モジュールで他のデータベース オブジェクトにアクセスする場合は、モジュールに対する EXECUTE 権限があり、次のいずれかに該当する場合にのみ、実行は成功します。  
  
-   モジュールが EXECUTE AS USER または SELF としてマークされており、モジュール所有者が、参照されるオブジェクトに対して必要な権限を保持している。 モジュール内の権限借用の詳細については、「[EXECUTE AS 句 &#40;Transact-SQL&#41;](../../t-sql/statements/execute-as-clause-transact-sql.md)」を参照してください。  
  
-   モジュールが EXECUTE AS CALLER としてマークされており、オブジェクトに対して必要な権限を保持している。  
  
-   モジュールが EXECUTE AS *user_name* としてマークされており、*user_name* が、オブジェクトに対して必要な権限を保持している。  
  
### <a name="context-switching-permissions"></a>コンテキスト切り替え権限  
 ログインに EXECUTE AS を指定するには、呼び出し元に、指定のログイン名に対する IMPERSONATE 権限が与えられている必要があります。 データベース ユーザーに EXECUTE AS を指定するには、呼び出し元に、指定のユーザー名に対する IMPERSONATE 権限が与えられている必要があります。 実行コンテキストを指定しない場合、または EXECUTE AS CALLER を指定する場合、IMPERSONATE 権限は必要ありません。  
  
## <a name="examples-sql-server"></a>例 :SQL Server
  
### <a name="a-using-execute-to-pass-a-single-parameter"></a>A. EXECUTE を使用して 1 つのパラメーターを渡す  
 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] データベースの `uspGetEmployeeManagers` ストアド プロシージャでは、1 つのパラメーター (`@EmployeeID`) を設定する必要があります。 次の例では、`Employee ID 6` をパラメーター値として使用し、`uspGetEmployeeManagers` ストアド プロシージャを実行します。  
  
```sql    
EXEC dbo.uspGetEmployeeManagers 6;  
GO  
```  
  
 実行の中で明示的に変数を指定することもできます。  
  
```sql    
EXEC dbo.uspGetEmployeeManagers @EmployeeID = 6;  
GO  
```  
  
 次のコードがバッチまたは **osql** または **sqlcmd** スクリプト内の最初のステートメントの場合、EXEC は必要ありません。  
  
```sql    
dbo.uspGetEmployeeManagers 6;  
GO  
--Or  
dbo.uspGetEmployeeManagers @EmployeeID = 6;  
GO  
```  
  
### <a name="b-using-multiple-parameters"></a>B. 複数のパラメーターを使用する  
 次の例では、[!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] データベースで `spGetWhereUsedProductID` ストアド プロシージャを実行します。 ここでは、製品 ID (`819`) と、`@CheckDate,` 型の値をとる `datetime` の、2 つのパラメーターを引き渡します。  
  
```sql    
DECLARE @CheckDate DATETIME;  
SET @CheckDate = GETDATE();  
EXEC dbo.uspGetWhereUsedProductID 819, @CheckDate;  
GO  
```  
  
### <a name="c-using-execute-tsql_string-with-a-variable"></a>C. EXECUTE 'tsql_string' を変数と共に使用する  
 次の例では、変数を含み、動的に構築される文字列が `EXECUTE` でどのように処理されるかを示します。 この例では、`tables_cursor` カーソルを作成します。このカーソルは、[!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)] データベース内にあるすべてのユーザー定義テーブルの一覧を保持しています。次にその一覧を使用して、テーブルに対してすべてのインデックスを再構築します。  
  
```sql    
DECLARE tables_cursor CURSOR  
   FOR  
   SELECT s.name, t.name   
   FROM sys.objects AS t  
   JOIN sys.schemas AS s ON s.schema_id = t.schema_id  
   WHERE t.type = 'U';  
OPEN tables_cursor;  
DECLARE @schemaname sysname;  
DECLARE @tablename sysname;  
FETCH NEXT FROM tables_cursor INTO @schemaname, @tablename;  
WHILE (@@FETCH_STATUS <> -1)  
BEGIN;  
   EXECUTE ('ALTER INDEX ALL ON ' + @schemaname + '.' + @tablename + ' REBUILD;');  
   FETCH NEXT FROM tables_cursor INTO @schemaname, @tablename;  
END;  
PRINT 'The indexes on all tables have been rebuilt.';  
CLOSE tables_cursor;  
DEALLOCATE tables_cursor;  
GO  
  
```  
  
### <a name="d-using-execute-with-a-remote-stored-procedure"></a>D. EXECUTE をリモート ストアド プロシージャと共に使用する  
 次の例では、リモート サーバー `SQLSERVER1` で `uspGetEmployeeManagers` ストアド プロシージャを実行し、`@retstat` に成功または失敗を示す戻りステータスを格納します。  
  
**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降
  
```sql    
DECLARE @retstat INT;  
EXECUTE @retstat = SQLSERVER1.AdventureWorks2012.dbo.uspGetEmployeeManagers @BusinessEntityID = 6;  
```  
  
### <a name="e-using-execute-with-a-stored-procedure-variable"></a>E. EXECUTE をストアド プロシージャ変数と共に使用する  
 次の例では、ストアド プロシージャ名を表す変数を作成します。  
  
```sql  
DECLARE @proc_name VARCHAR(30);  
SET @proc_name = 'sys.sp_who';  
EXEC @proc_name;  
  
```  
  
### <a name="f-using-execute-with-default"></a>F. EXECUTE を DEFAULT と共に使用する  
 次の例では、第 1 および第 3 パラメーターに既定値を指定して、ストアド プロシージャを作成します。 プロシージャを実行するとき、値を指定しなかったり、既定値が指定されていた場合は、これらの既定値が第 1 および第 3 パラメーターに挿入されます。 `DEFAULT` キーワードはさまざまな方法で使用できます。  
  
```sql    
IF OBJECT_ID(N'dbo.ProcTestDefaults', N'P')IS NOT NULL  
   DROP PROCEDURE dbo.ProcTestDefaults;  
GO  
-- Create the stored procedure.  
CREATE PROCEDURE dbo.ProcTestDefaults (  
@p1 SMALLINT = 42,   
@p2 CHAR(1),   
@p3 VARCHAR(8) = 'CAR')  
AS   
   SET NOCOUNT ON;  
   SELECT @p1, @p2, @p3  
;  
GO  
```  
  
 `Proc_Test_Defaults` ストアド プロシージャは、多くの組み合わせで実行できます。  
  
```sql    
-- Specifying a value only for one parameter (@p2).  
EXECUTE dbo.ProcTestDefaults @p2 = 'A';  
-- Specifying a value for the first two parameters.  
EXECUTE dbo.ProcTestDefaults 68, 'B';  
-- Specifying a value for all three parameters.  
EXECUTE dbo.ProcTestDefaults 68, 'C', 'House';  
-- Using the DEFAULT keyword for the first parameter.  
EXECUTE dbo.ProcTestDefaults @p1 = DEFAULT, @p2 = 'D';  
-- Specifying the parameters in an order different from the order defined in the procedure.  
EXECUTE dbo.ProcTestDefaults DEFAULT, @p3 = 'Local', @p2 = 'E';  
-- Using the DEFAULT keyword for the first and third parameters.  
EXECUTE dbo.ProcTestDefaults DEFAULT, 'H', DEFAULT;  
EXECUTE dbo.ProcTestDefaults DEFAULT, 'I', @p3 = DEFAULT;  
  
```  
  
### <a name="g-using-execute-with-at-linked_server_name"></a>G. EXECUTE を AT linked_server_name と共に使用する  
 次の例では、コマンド文字列をリモート サーバーに渡します。 ここでは、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の別のインスタンスをポイントするリンク サーバー `SeattleSales` を作成し、そのリンク サーバーに対して DDL ステートメント (`CREATE TABLE`) を実行します。  
  
**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降
  
```sql    
EXEC sp_addlinkedserver 'SeattleSales', 'SQL Server'  
GO  
EXECUTE ( 'CREATE TABLE AdventureWorks2012.dbo.SalesTbl   
(SalesID int, SalesName varchar(10)) ; ' ) AT SeattleSales;  
GO  
```  
  
### <a name="h-using-execute-with-recompile"></a>H. EXECUTE WITH RECOMPILE を使用する  
 次の例では、`Proc_Test_Defaults` ストアド プロシージャを実行し、モジュール実行後に新しいクエリ プランを強制的にコンパイル、使用、および破棄します。  
  
```sql    
EXECUTE dbo.Proc_Test_Defaults @p2 = 'A' WITH RECOMPILE;  
GO  
```  
  
### <a name="i-using-execute-with-a-user-defined-function"></a>I. EXECUTE をユーザー定義関数と共に使用する  
 次の例では、[!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] データベースでユーザー定義のスカラー関数 `ufnGetSalesOrderStatusText` を実行します。 ここでは、変数 `@returnstatus` を使用して、関数によって返される値を格納します。 この関数には 1 つの入力パラメーター `@Status` が必要です。 これは **tinyint** データ型として定義されます。  
  
```sql    
DECLARE @returnstatus NVARCHAR(15);  
SET @returnstatus = NULL;  
EXEC @returnstatus = dbo.ufnGetSalesOrderStatusText @Status = 2;  
PRINT @returnstatus;  
GO  
```  
  
### <a name="j-using-execute-to-query-an-oracle-database-on-a-linked-server"></a>J. EXECUTE を使用して、リンク サーバー上の Oracle データベースに対してクエリを実行する  
 次の例では、いくつかの `SELECT` ステートメントを、リモートの Oracle サーバーで実行します。 この例では、まず Oracle サーバーをリンク サーバーとして追加し、リンク サーバー ログインを作成します。  
  
**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降
  
```sql    
-- Setup the linked server.  
EXEC sp_addlinkedserver    
        @server='ORACLE',  
        @srvproduct='Oracle',  
        @provider='OraOLEDB.Oracle',   
        @datasrc='ORACLE10';  
  
EXEC sp_addlinkedsrvlogin   
    @rmtsrvname='ORACLE',  
    @useself='false',   
    @locallogin=null,   
    @rmtuser='scott',   
    @rmtpassword='tiger';  
  
EXEC sp_serveroption 'ORACLE', 'rpc out', true;  
GO  
  
-- Execute several statements on the linked Oracle server.  
EXEC ( 'SELECT * FROM scott.emp') AT ORACLE;  
GO  
EXEC ( 'SELECT * FROM scott.emp WHERE MGR = ?', 7902) AT ORACLE;  
GO  
DECLARE @v INT;   
SET @v = 7902;  
EXEC ( 'SELECT * FROM scott.emp WHERE MGR = ?', @v) AT ORACLE;  
GO   
```  
  
### <a name="k-using-execute-as-user-to-switch-context-to-another-user"></a>K. EXECUTE AS USER を使用して、コンテキストを別のユーザーに切り替える  
 次の例では、テーブルを作成する [!INCLUDE[tsql](../../includes/tsql-md.md)] 文字列を実行し、`AS USER` 句を指定して、ステートメントの実行コンテキストを呼び出し元から `User1` に切り替えます。 [!INCLUDE[ssDE](../../includes/ssde-md.md)] では、ステートメントの実行時に `User1` の権限がチェックされます。 `User1` はデータベース内のユーザーとして存在し、`Sales` スキーマにテーブルを作成する権限が与えられている必要があります。そうでない場合、ステートメントは失敗します。  
  
```sql    
EXECUTE ('CREATE TABLE Sales.SalesTable (SalesID INT, SalesName VARCHAR(10));')  
AS USER = 'User1';  
GO  
```  
  
### <a name="l-using-a-parameter-with-execute-and-at-linked_server_name"></a>L. EXECUTE および AT linked_server_name と共にパラメーターを使用する  
 次の例では、パラメーターのプレースホルダーとして疑問符 (`?`) を使用し、コマンド文字列をリモート サーバーに渡します。 ここでは、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の別のインスタンスをポイントするリンク サーバー `SeattleSales` を作成し、そのリンク サーバーに対して `SELECT` ステートメントを実行します。 `SELECT` ステートメントでは、`ProductID` パラメーター (`952`) のプレースホルダーとして疑問符を使用します。このパラメーターは、ステートメントの後で提供されます。  
  
**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降
  
```sql    
-- Setup the linked server.  
EXEC sp_addlinkedserver 'SeattleSales', 'SQL Server'  
GO  
-- Execute the SELECT statement.  
EXECUTE ('SELECT ProductID, Name   
    FROM AdventureWorks2012.Production.Product  
    WHERE ProductID = ? ', 952) AT SeattleSales;  
GO  
```  
  
### <a name="m-using-execute-to-redefine-a-single-result-set"></a>M. EXECUTE を使用して単一の結果セットを再定義する  
 前のいくつかの例では、7 つの列を返す `EXEC dbo.uspGetEmployeeManagers 6;` を実行しました。 次の例では、`WITH RESULT SET` 構文を使用して、返される結果セットの名前とデータ型を変更する方法を示します。  
  
**適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降、[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]
  
```sql    
EXEC uspGetEmployeeManagers 16  
WITH RESULT SETS  
(   
   ([Reporting Level] INT NOT NULL,  
    [ID of Employee] INT NOT NULL,  
    [Employee First Name] NVARCHAR(50) NOT NULL,  
    [Employee Last Name] NVARCHAR(50) NOT NULL,  
    [Employee ID of Manager] NVARCHAR(max) NOT NULL,  
    [Manager First Name] NVARCHAR(50) NOT NULL,  
    [Manager Last Name] NVARCHAR(50) NOT NULL )  
);  
  
```  
  
### <a name="n-using-execute-to-redefine-a-two-result-sets"></a>北 EXECUTE を使用して 2 つの結果セットを再定義する  
 複数の結果セットを返すステートメントを実行する場合は、予期される各結果セットを定義してください。 [!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)] の次の例では、2 つの結果セットを返すプロシージャを作成します。 作成したプロシージャはその後 **WITH RESULT SETS** 句を使用して実行され、2 つの結果セットの定義が指定されます。  
  
**適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降、[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]
  
```sql    
--Create the procedure  
CREATE PROC Production.ProductList @ProdName NVARCHAR(50)  
AS  
-- First result set  
SELECT ProductID, Name, ListPrice  
    FROM Production.Product  
    WHERE Name LIKE @ProdName;  
-- Second result set   
SELECT Name, COUNT(S.ProductID) AS NumberOfOrders  
    FROM Production.Product AS P  
    JOIN Sales.SalesOrderDetail AS S  
        ON P.ProductID  = S.ProductID   
    WHERE Name LIKE @ProdName  
    GROUP BY Name;  
GO  
  
-- Execute the procedure   
EXEC Production.ProductList '%tire%'  
WITH RESULT SETS   
(  
    (ProductID INT,   -- first result set definition starts here  
    Name NAME,  
    ListPrice MONEY)  
    ,                 -- comma separates result set definitions  
    (Name NAME,       -- second result set definition starts here  
    NumberOfOrders INT)  
);  
  
```  
  ### <a name="o-using-execute-with-at-data_source-data_source_name-to-query-a-remote-sql-server"></a>O. EXECUTE と AT DATA_SOURCE data_source_name を使用してリモート SQL Server に対してクエリを実行する 
  
 次の例では、SQL Server インスタンスをポイントする外部データ ソースにコマンド文字列を渡します。 
  
**適用対象**: [!INCLUDE[sssql19](../../includes/sssql19-md.md)] 以降
  
```sql    
EXECUTE ( 'SELECT @@SERVERNAME' ) AT DATA_SOURCE my_sql_server;  
GO  
```  
  
### <a name="p-using-execute-with-at-data_source-data_source_name-to-query-compute-pool-in-sql-server-big-data-cluster"></a>P. EXECUTE と AT DATA_SOURCE data_source_name を使用して SQL Server ビッグ データ クラスターのコンピューティング プールに対してクエリを実行する 

 次の例では、SQL Server ビッグ データ クラスターのコンピューティング プールをポイントする外部データ ソースにコマンド文字列を渡します。 この例では SQL Server ビッグ データ クラスターのコンピューティング プールに対してデータ ソース `SqlComputePool` を作成し、そのデータ ソースに対して `SELECT` ステートメントを実行します。 
  
**適用対象**: [!INCLUDE[sssql19](../../includes/sssql19-md.md)] 以降
  
```sql  
CREATE EXTERNAL DATA SOURCE SqlComputePool 
WITH (LOCATION = 'sqlcomputepool://controller-svc/default');
EXECUTE ( 'SELECT @@SERVERNAME' ) AT DATA_SOURCE SqlComputePool;  
GO  
```  

### <a name="q-using-execute-with-at-data_source-data_source_name-to-query-data-pool-in-sql-server-big-data-cluster"></a>Q. EXECUTE と AT DATA_SOURCE data_source_name を使用して SQL Server ビッグ データ クラスターのデータ プールに対してクエリを実行する 
 次の例では、SQL Server ビッグ データ クラスターのコンピューティング プールをポイントする外部データ ソースにコマンド文字列を渡します。 この例では SQL Server ビッグ データ クラスターのデータ プールに対してデータ ソース `SqlDataPool` を作成し、そのデータ ソースに対して `SELECT` ステートメントを実行します。 
  
**適用対象**: [!INCLUDE[sssql19](../../includes/sssql19-md.md)] 以降
  
```sql  
CREATE EXTERNAL DATA SOURCE SqlDataPool 
WITH (LOCATION = 'sqldatapool://controller-svc/default');
EXECUTE ( 'SELECT @@SERVERNAME' ) AT DATA_SOURCE SqlDataPool;  
GO  
```

### <a name="r-using-execute-with-at-data_source-data_source_name-to-query-storage-pool-in-sql-server-big-data-cluster"></a>R. EXECUTE と AT DATA_SOURCE data_source_name を使用して SQL Server ビッグ データ クラスターの記憶域プールに対してクエリを実行する 

 次の例では、SQL Server ビッグ データ クラスターのコンピューティング プールをポイントする外部データ ソースにコマンド文字列を渡します。 この例では SQL Server ビッグ データ クラスターのデータ プールに対してデータ ソース `SqlStoragePool` を作成し、そのデータ ソースに対して `SELECT` ステートメントを実行します。 
  
**適用対象**: [!INCLUDE[sssql19](../../includes/sssql19-md.md)] 以降
  
```sql  
CREATE EXTERNAL DATA SOURCE SqlStoragePool
WITH (LOCATION = 'sqlhdfs://controller-svc/default');
EXECUTE ( 'SELECT @@SERVERNAME' ) AT DATA_SOURCE SqlStoragePool;  
GO  
```

  
## <a name="examples-azure-synapse-analytics"></a>例 :Azure Synapse Analytics 
  
### <a name="a-basic-procedure-execution"></a>A:基本プロシージャの実行  
 ストアド プロシージャの実行:  
  
```sql  
EXEC proc1;  
```  
  
 実行時に決定した名前を持つストアド プロシージャの呼び出し:  
  
```sql    
EXEC ('EXEC ' + @var);  
```  
  
 ストアド プロシージャ内からストアド プロシージャの呼び出し:  
  
```sql   
CREATE sp_first AS EXEC sp_second; EXEC sp_third;  
```  
  
### <a name="b-executing-strings"></a>B:文字列の実行  
 SQL 文字列の実行:  
  
```sql   
EXEC ('SELECT * FROM sys.types');  
```  
  
 入れ子になった文字列の実行:  
  
```sql  
EXEC ('EXEC (''SELECT * FROM sys.types'')');  
```  
  
 文字列変数の実行:  
  
```sql  
DECLARE @stringVar NVARCHAR(100);  
SET @stringVar = N'SELECT name FROM' + ' sys.sql_logins';  
EXEC (@stringVar);  
```  
  
### <a name="c-procedures-with-parameters"></a>C: パラメーターを持つプロシージャ  

 次の例では、パラメーターを持つプロシージャを作成し、プロシージャを実行する 3 通りの方法を示しています。  
  
```sql  
-- Uses AdventureWorks  
  
CREATE PROC ProcWithParameters  
    @name NVARCHAR(50),  
@color NVARCHAR(15)  
AS   
SELECT ProductKey, EnglishProductName, Color FROM [dbo].[DimProduct]  
WHERE EnglishProductName LIKE @name  
AND Color = @color;  
GO  
  
-- Executing using positional parameters  
EXEC ProcWithParameters N'%arm%', N'Black';  
-- Executing using named parameters in order  
EXEC ProcWithParameters @name = N'%arm%', @color = N'Black';  
-- Executing using named parameters out of order  
EXEC ProcWithParameters @color = N'Black', @name = N'%arm%';  
GO  
```  
  
## <a name="see-also"></a>参照  
 [@@NESTLEVEL &#40;Transact-SQL&#41;](../../t-sql/functions/nestlevel-transact-sql.md)   
 [DECLARE @local_variable &#40;Transact-SQL&#41;](../../t-sql/language-elements/declare-local-variable-transact-sql.md)   
 [EXECUTE AS 句 &#40;Transact-SQL&#41;](../../t-sql/statements/execute-as-clause-transact-sql.md)   
 [osql ユーティリティ](../../tools/osql-utility.md)   
 [プリンシパル &#40;データベース エンジン&#41;](../../relational-databases/security/authentication-access/principals-database-engine.md)   
 [REVERT &#40;Transact-SQL&#41;](../../t-sql/statements/revert-transact-sql.md)   
 [sp_addlinkedserver &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-addlinkedserver-transact-sql.md)   
 [sqlcmd ユーティリティ](../../tools/sqlcmd-utility.md)   
 [SUSER_NAME &#40;Transact-SQL&#41;](../../t-sql/functions/suser-name-transact-sql.md)   
 [sys.database_principals &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-database-principals-transact-sql.md)   
 [sys.server_principals &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-server-principals-transact-sql.md)   
 [USER_NAME &#40;Transact-SQL&#41;](../../t-sql/functions/user-name-transact-sql.md)   
 [OPENDATASOURCE &#40;Transact-SQL&#41;](../../t-sql/functions/opendatasource-transact-sql.md)   
 [インメモリ OLTP でのユーザー定義のスカラー関数](../../relational-databases/in-memory-oltp/scalar-user-defined-functions-for-in-memory-oltp.md)  
  
  
