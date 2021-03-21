---
description: RAISERROR (Transact-SQL)
title: RAISERROR (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 02/21/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- RAISERROR
- RAISERROR_TSQL
- RAISEERROR_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sysmessages system table
- errors [SQL Server], RAISERROR statement
- user-defined error messages [SQL Server]
- system flags
- generating errors [SQL Server]
- TRY block [SQL Server]
- recording errors
- ad hoc messages
- RAISERROR statement
- CATCH block
- messages [SQL Server], RAISERROR statement
ms.assetid: 483588bd-021b-4eae-b4ee-216268003e79
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 8402340c86a6b8fd966d0ee9b00a010c02a5e46f
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104753712"
---
# <a name="raiserror-transact-sql"></a>RAISERROR (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

> [!NOTE]
> **RAISERROR** ステートメントでは、**SET XACT_ABORT** は考慮されません。 新しいアプリケーションでは、**RAISERROR** の代わりに **THROW** を使ってください。

  エラー メッセージを生成し、セッションのエラー処理を開始します。 RAISERROR では、sys.messages カタログ ビューに格納されているユーザー定義のメッセージを参照することも、メッセージを動的に作成することもできます。 メッセージは、サーバー エラー メッセージとして、呼び出し元のアプリケーションまたは関連する TRY...CATCH 構造の CATCH ブロックに返されます。 新しいアプリケーションでは、代わりに [THROW](../../t-sql/language-elements/throw-transact-sql.md) を使用してください。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql
-- Syntax for SQL Server and Azure SQL Database  
  
RAISERROR ( { msg_id | msg_str | @local_variable }  
    { ,severity ,state }  
    [ ,argument [ ,...n ] ] )  
    [ WITH option [ ,...n ] ]  
```  
  
```syntaxsql
-- Syntax for Azure Synapse Analytics and Parallel Data Warehouse  
  
RAISERROR ( { msg_str | @local_variable }  
    { ,severity ,state }  
    [ ,argument [ ,...n ] ] )  
    [ WITH option [ ,...n ] ]  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
 *msg_id*  
 sp_addmessage を使用して sys.messages カタログ ビューに格納されたユーザー定義エラー メッセージ番号です。 ユーザー定義エラー メッセージのエラー番号は、必ず 50000 より大きくなります。 *msg_id* を指定しないと、RAISERROR はエラー番号 50000 のエラー メッセージを表示します。  
  
 *msg_str*  
 C 標準ライブラリに含まれる **printf** 関数に似た形式のユーザー定義メッセージです。 エラー メッセージの長さは最大 2,047 文字です。 メッセージの文字数が 2,048 文字を超えると、2,044 文字までだけ表示され、メッセージが途中で切れていることを示す省略記号が追加されます。 内部的な記憶動作が原因で、書式引数は出力として表示されるより多くの文字を使用することに注意してください。 たとえば、2 という値が割り当てられる書式引数 *%d* は、実際にメッセージ文字列に表示されるのは 1 文字ですが、内部的にはさらに 3 文字分の記憶領域を必要とします。 この記憶領域の要件により、メッセージ出力で使用できる文字数は少なくなります。  
  
 *msg_str* を指定した場合、RAISERROR はエラー番号 50000 のエラー メッセージを表示します。  
  
 *msg_str* は、オプションで変換指定が埋め込まれた文字列です。 それぞれの変換指定で、引数リストの値をどのような形式にするかと、*msg_str* 内の変換指定の場所にあるフィールドにどのように配置するのかを定義します。 変換指定の形式は、次のとおりです。  
  
 % [[*flag*] [*width*] [. *precision*] [{h | l}]] *type*  
  
 *msg_str* では、次のパラメーターを使用します。  
  
 *flag*  
  
 置換された値の間隔と配置を決めるコードです。  
  
|コード|プレフィックスまたは配置|説明|  
|----------|-----------------------------|-----------------|  
|- (マイナス)|左寄せ|指定されたフィールド幅内で引数の値を左寄せします。|  
|+ (プラス)|符号プレフィックス|引数の値が符号付きの場合に、プラス記号 (+) またはマイナス記号 (-) を先頭に付けます。|  
|0 (ゼロ)|0 埋め|幅の最小値になるまで、出力の先頭に 0 を付けます。 0 とマイナス記号 (-) が付いている場合は、0 は無視されます。|  
|# (番号)|16 進型の x または X に対する 0x プレフィックス|番号記号 (#) フラグは、o、x、または X 形式で使われる場合、0 以外の値の先頭に、それぞれ 0、0x、0X を付けます。 d、i、または u に番号記号 (#) フラグが付いている場合、フラグは無視されます。|  
|' ' (空白)|スペース埋め|出力値が符号付きで正の値の場合に、先頭に空白を付けます。 プラス記号 (+) フラグが指定される場合は、この空白は無視されます。|  
  
 *width*  
  
 引数値が配置されるフィールドの幅の最小値を定義する整数です。 引数値の長さが *width* 以上の場合、値は埋め込みなしで出力されます。 値が *width* より短い場合は、*width* で指定した長さまで埋め込まれます。  
  
 アスタリスク (*) は、width が引数リスト内の関連する引数によって指定されることを意味します。この引数は整数値である必要があります。  
  
 *有効桁数 (precision)*  
  
 文字列の値の引数値から取得される最大文字数です。 たとえば、文字列が 5 文字で、precision が 3 の場合、文字列の値の最初の 3 文字のみが使用されます。  
  
 整数値の場合、*precision* は出力される最小桁数です。  
  
 アスタリスク (*) は、precision が引数のリスト内の関連する引数により指定されることを意味します。この引数は整数値である必要があります。  
  
 {h | l} *type*  
  
 d、i、o、s、x、X、u の各文字型で使われ、**shortint** (h) または **longint** (l) の値を作成します。  
  
|型の指定|表す内容|  
|------------------------|----------------|  
|d または i|符号付き整数|  
|o|符号なし 8 進数|  
|s|String|  
|u|符号なし整数|  
|x または X|符号なし 16 進数|  
  
> [!NOTE]  
>  これらの型指定は、C 標準ライブラリの **printf** 関数に対して定義されている型指定に基づいています。 RAISERROR メッセージ文字列に使用される型指定は [!INCLUDE[tsql](../../includes/tsql-md.md)] のデータ型にマップされ、**printf** で使用される型指定は C 言語のデータ型にマップされます。 **printf** で使用される型指定は、関連する C のデータ型に類似するデータ型が [!INCLUDE[tsql](../../includes/tsql-md.md)] にない場合、RAISERROR でサポートされません。 たとえば、ポインターに対する *%p* 指定は、[!INCLUDE[tsql](../../includes/tsql-md.md)] にポインターのデータ型がないため、RAISERROR でサポートされません。  
  
> [!NOTE]  
>  [!INCLUDE[tsql](../../includes/tsql-md.md)] **bigint** データ型に値を変換するには、 **%I64d** を指定します。  
  
 *\@local_variable*  
 *msg_str* と同じ形式の文字列を含む有効な文字データ型の変数です。 *\@local_variable* は、**char** または **varchar** であるか、これらのデータ型に暗黙的に変換できるデータ型である必要があります。  
  
 *severity*  
 このメッセージに関連付けられたユーザー定義[重大度レベル](../../relational-databases/errors-events/database-engine-error-severities.md)です。 sp_addmessage を使用して作成されたユーザー定義メッセージを、*msg_id* を使用して出力するときは、RAISERROR で指定された重大度が sp_addmessage で指定された重大度をオーバーライドします。  
  
 0 から 18 までの重大度レベルはどのユーザーでも指定できます。 19 から 25 までの重大度レベルは、固定サーバー ロールまたはユーザー ALTER TRACE 権限を持つ、sysadmin のメンバーのみが指定できます。 重大度レベル 19 から 25 までは、WITH LOG オプションを必要とします。 0 より小さい重大度レベルは 0 と解釈されます。 25 より大きい重大度レベルは 25 と解釈されます。  
  
> [!CAUTION]  
>  重大度レベル 20 から 25 までは、致命的と見なされます。 この致命的な重大度レベルが発生した場合は、メッセージを受け取った後でクライアントの接続が終了し、エラーがエラー ログおよびアプリケーション ログに記録されます。  
  
 次の例に示すように、エラーに関連付けられた重大度の値を返すには -1 を指定できます。  
  
```sql  
RAISERROR (15600,-1,-1, 'mysp_CreateCustomer');  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 ```
 Msg 15600, Level 15, State 1, Line 1   
 An invalid parameter or option was specified for procedure 'mysp_CreateCustomer'.
 ```  
  
 *状態*  
 0 から 255 までの整数です。 負の値は既定で 1 に設定されます。 255 より大きい値は使用できません。 
  
 同じユーザー定義エラーが複数の場所で発生する場合、それぞれの場所に対して一意の状態番号を使用すると、コードのどのセクションでエラーが発生しているのかを楽に探すことができます。  
  
 *argument*  
 *msg_str* で定義された変数、または *msg_id* に対応するメッセージの書式引数に使用されるパラメーターです。 書式引数は指定しなくても、複数指定してもかまいませんが、合計で 20 を超えることはできません。 書式引数にはローカル変数を指定するか、データ型として **tinyint**、**smallint**、**int**、**char**、**varchar**、**nchar**、**nvarchar**、**binary**、**varbinary** のいずれかを指定できます。 その他のデータ型はサポートされません。  
  
 *オプション*  
 エラーのカスタム オプションです。次の表のいずれかの値をとります。  
  
|値|説明|  
|-----------|-----------------|  
|LOG|[!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] [!INCLUDE[ssDE](../../includes/ssde-md.md)] のインスタンスについて、エラー ログとアプリケーション ログにエラーを記録します。 エラー ログに記録されるエラーは、現在、最高 440 バイトに制限されています。 sysadmin 固定サーバー ロールまたは ALTER TRACE 権限を持つユーザーのメンバーのみが WITH LOG を指定できます。<br /><br /> [!INCLUDE[applies](../../includes/applies-md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)], [!INCLUDE[ssSDS](../../includes/sssds-md.md)]|  
|NOWAIT|クライアントにすぐにメッセージを送信します。<br /><br /> [!INCLUDE[applies](../../includes/applies-md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)], [!INCLUDE[ssSDS](../../includes/sssds-md.md)]|  
|SETERROR|重大度レベルとは無関係に、@@ERROR 値と ERROR_NUMBER 値に *msg_id* または 50000 を設定します。<br /><br /> [!INCLUDE[applies](../../includes/applies-md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)], [!INCLUDE[ssSDS](../../includes/sssds-md.md)]|  
  
## <a name="remarks"></a>解説  
 RAISERROR によって生成されたエラーは、[!INCLUDE[ssDE](../../includes/ssde-md.md)]のコードによって生成されたエラーと同様に機能します。 RAISERROR によって指定された値は、ERROR_LINE、ERROR_MESSAGE、ERROR_NUMBER、ERROR_PROCEDURE、ERROR_SEVERITY、ERROR_STATE、@@ERROR システム関数によりレポートされます。 TRY ブロックで 11 以上の重大度で RAISERROR を実行すると、RAISERROR から、関連する CATCH ブロックに制御が渡されます。 RAISERROR が次の条件で実行されると、呼び出し元にエラーが返されます。  
  
-   TRY ブロックのスコープの外で実行された場合  
  
-   TRY ブロックで 10 以下の重大度で実行された場合  
  
-   データベース接続を終了させる 20 以上の重大度で実行された場合  
  
 CATCH ブロックは、ERROR_NUMBER や ERROR_MESSAGE などのシステム関数を使用して CATCH ブロックを呼び出したエラーを、RAISERROR を使用して再度スローし、元のエラー情報を取得できます。 重大度レベルが 1 から 10 までのメッセージには、@@ERROR の値は既定で 0 に設定されます。  
  
 *msg_id* で sys.messages カタログ ビューから利用可能なユーザー定義メッセージを指定している場合、RAISERROR は、*msg_str* を使用して指定されたユーザー定義メッセージのテキストに適用されているのと同じルールで、テキスト列からのメッセージを処理します。 ユーザー定義メッセージのテキストには変換指定を含めることができます。その変換指定には RAISERROR によって引数値がマップされます。 ユーザー定義エラー メッセージを追加するには sp_addmessage を使用し、ユーザー定義エラー メッセージを削除するには sp_dropmessage を使用します。  
  
 RAISERROR は、呼び出し元のアプリケーションにメッセージを返すために、PRINT の代わりに使用することができます。 RAISERROR では C 標準ライブラリの **printf** 関数の機能に類似した代替文字をサポートしますが、[!INCLUDE[tsql](../../includes/tsql-md.md)] の PRINT ステートメントではサポートしていません。 RAISERROR が TRY ブロック内で 11 から 19 の重大度で実行され、関連する CATCH ブロックに制御を渡す間、PRINT ステートメントは TRY ブロックの影響を受けません。 CATCH ブロックを呼び出さずに TRY ブロックからのメッセージを返すには、重大度を 10 以下に指定して RAISERROR を使用します。  
  
 通常、最初の引数が最初の変換指定を置き換え、2 番目の引数が 2 番目の変換指定を置き換えるというように、連続する引数が連続する変換指定を置き換えます。 たとえば、次の `RAISERROR` ステートメントは、最初の引数 `N'number'` で最初の変換指定 `%s` を置き換え、2 番目の引数 `5` で 2 番目の変換指定 `%d.` を置き換えます。  
  
```sql  
RAISERROR (N'This is message %s %d.', -- Message text.  
           10, -- Severity,  
           1, -- State,  
           N'number', -- First argument.  
           5); -- Second argument.  
-- The message text returned is: This is message number 5.  
GO  
```  
  
 変換指定の width または precision にアスタリスク (*) が指定されている場合、width または precision に使用される値は整数の引数値として指定されます。 この場合、1 つの変換指定で、width、precision、および置換値に対して 1 つずつ、最大 3 つまでの引数を使用できます。  
  
 たとえば、次の `RAISERROR` ステートメントはどちらも同じ文字列を返します。 一方のステートメントでは引数リストで width と precision の値を指定し、もう一方のステートメントでは変換指定でこれらを指定しています。  
  
```sql  
RAISERROR (N'<\<%*.*s>>', -- Message text.  
           10, -- Severity,  
           1, -- State,  
           7, -- First argument used for width.  
           3, -- Second argument used for precision.  
           N'abcde'); -- Third argument supplies the string.  
-- The message text returned is: <<    abc>>.  
GO  
RAISERROR (N'<\<%7.3s>>', -- Message text.  
           10, -- Severity,  
           1, -- State,  
           N'abcde'); -- First argument supplies the string.  
-- The message text returned is: <<    abc>>.  
GO  
```  
  
## <a name="examples"></a>例  
  
### <a name="a-returning-error-information-from-a-catch-block"></a>A. CATCH ブロックからエラー情報を返す  
 次のコード例では、`TRY` ブロック内で `RAISERROR` を使用して、関連付けられている `CATCH` ブロックに実行を移動させる方法を示します。 また、`RAISERROR` を使用して、`CATCH` ブロックを呼び出したエラーについての情報を返す方法も示しています。  
  
> [!NOTE]  
>  RAISERROR では、1 から 127 までの状態番号のエラーだけが生成されます。 [!INCLUDE[ssDE](../../includes/ssde-md.md)]では状態番号 0 のエラーが発生する場合があるため、ERROR_STATE によって返されるエラーの状態番号は、RAISERROR の state パラメーターの値として渡す前に確認することをお勧めします。  
  
```sql  
BEGIN TRY  
    -- RAISERROR with severity 11-19 will cause execution to   
    -- jump to the CATCH block.  
    RAISERROR ('Error raised in TRY block.', -- Message text.  
               16, -- Severity.  
               1 -- State.  
               );  
END TRY  
BEGIN CATCH  
    DECLARE @ErrorMessage NVARCHAR(4000);  
    DECLARE @ErrorSeverity INT;  
    DECLARE @ErrorState INT;  
  
    SELECT   
        @ErrorMessage = ERROR_MESSAGE(),  
        @ErrorSeverity = ERROR_SEVERITY(),  
        @ErrorState = ERROR_STATE();  
  
    -- Use RAISERROR inside the CATCH block to return error  
    -- information about the original error that caused  
    -- execution to jump to the CATCH block.  
    RAISERROR (@ErrorMessage, -- Message text.  
               @ErrorSeverity, -- Severity.  
               @ErrorState -- State.  
               );  
END CATCH;  
```  
  
### <a name="b-creating-an-ad-hoc-message-in-sysmessages"></a>B. sys.messages 内でアドホック メッセージを作成する  
 次の例では、sys.messages カタログ ビューに格納されているメッセージが生成される方法を示します。 メッセージは、`sp_addmessage` システム ストアド プロシージャを使用して、メッセージ番号 `50005` として sys.messages カタログ ビューに追加されています。  
  
```sql  
sp_addmessage @msgnum = 50005,  
              @severity = 10,  
              @msgtext = N'<\<%7.3s>>';  
GO  
RAISERROR (50005, -- Message id.  
           10, -- Severity,  
           1, -- State,  
           N'abcde'); -- First argument supplies the string.  
-- The message text returned is: <<    abc>>.  
GO  
sp_dropmessage @msgnum = 50005;  
GO  
```  
  
### <a name="c-using-a-local-variable-to-supply-the-message-text"></a>C. ローカル変数を使用してメッセージ テキストを指定する  
 次のコード例では、ローカル変数を使用して、`RAISERROR` ステートメントのメッセージ テキストを指定する方法を示します。  
  
```sql  
DECLARE @StringVariable NVARCHAR(50);  
SET @StringVariable = N'<\<%7.3s>>';  
  
RAISERROR (@StringVariable, -- Message text.  
           10, -- Severity,  
           1, -- State,  
           N'abcde'); -- First argument supplies the string.  
-- The message text returned is: <<    abc>>.  
GO  
```  
  
## <a name="see-also"></a>参照  
 [組み込み関数 &#40;Transact-SQL&#41;](~/t-sql/functions/functions.md)   
 [DECLARE @local_variable (Transact-SQL)](../../t-sql/language-elements/declare-local-variable-transact-sql.md)   
 [PRINT &#40;Transact-SQL&#41;](../../t-sql/language-elements/print-transact-sql.md)   
 [sp_addmessage &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-addmessage-transact-sql.md)   
 [sp_dropmessage &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-dropmessage-transact-sql.md)   
 [sys.messages &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/messages-for-errors-catalog-views-sys-messages.md)   
 [xp_logevent &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/xp-logevent-transact-sql.md)   
 [@@ERROR &#40;Transact-SQL&#41;](../../t-sql/functions/error-transact-sql.md)   
 [ERROR_LINE &#40;Transact-SQL&#41;](../../t-sql/functions/error-line-transact-sql.md)   
 [ERROR_MESSAGE &#40;Transact-SQL&#41;](../../t-sql/functions/error-message-transact-sql.md)   
 [ERROR_NUMBER &#40;Transact-SQL&#41;](../../t-sql/functions/error-number-transact-sql.md)   
 [ERROR_PROCEDURE &#40;Transact-SQL&#41;](../../t-sql/functions/error-procedure-transact-sql.md)   
 [ERROR_SEVERITY &#40;Transact-SQL&#41;](../../t-sql/functions/error-severity-transact-sql.md)   
 [ERROR_STATE &#40;Transact-SQL&#41;](../../t-sql/functions/error-state-transact-sql.md)   
 [TRY...CATCH &#40;Transact-SQL&#41;](../../t-sql/language-elements/try-catch-transact-sql.md)  
  
  

