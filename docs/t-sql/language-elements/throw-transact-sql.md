---
description: THROW (Transact-SQL)
title: THROW (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/16/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- THROW_TSQL
- THROW
dev_langs:
- TSQL
helpviewer_keywords:
- THROW statement
ms.assetid: 43661b89-8f13-4480-ad53-70306cbb14c5
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 295950bd40a0747ed06e25ead283b1cb47700607
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104750552"
---
# <a name="throw-transact-sql"></a>THROW (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  [!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] で、例外を発生させ、TRY...CATCH 構造の CATCH ブロックに実行制御を移します。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql
THROW [ { error_number | @local_variable },  
        { message | @local_variable },  
        { state | @local_variable } ]   
[ ; ]  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
 *error_number*  
 例外を表す定数または変数です。 *error_number* は **int** で、50000 以上、2147483647 以下にする必要があります。  
  
 *message*  
 例外を説明する文字列または変数です。 *message* は **nvarchar(2048)** です。  
  
 *状態*  
 メッセージに関連付けられる状態を示す、0 から 255 の範囲の定数または変数です。 *state* は **tinyint** です。  
  
## <a name="remarks"></a>解説  
 THROW ステートメントの前のステートメントの後に、セミコロン (;) ステートメント ターミネータが続く必要があります。  
  
 TRY...CATCH 構造を使用できない場合は、ステートメント バッチが終了します。 例外が発生する行番号およびプロシージャが設定されます。 重大度は 16 に設定されます。  
  
 パラメーターを使用せずに THROW ステートメントを指定する場合は、ステートメントが CATCH ブロック内に存在する必要があります。 これによりキャッチされた例外が発生します。 THROW ステートメント内でエラーが発生すると、ステートメント バッチが終了します。  
  
 % は THROW ステートメントのメッセージ テキストに予約された文字で、エスケープする必要があります。 文字 % を二重にすると、% をメッセージ テキストの一部として返します (例: "増加が元の値の 15% を超えました。")。  
  
## <a name="differences-between-raiserror-and-throw"></a>RAISERROR と THROW の違い  
 次の表に、RAISERROR ステートメントと THROW ステートメントの違いを示します。  
  
|RAISERROR ステートメント|THROW ステートメント|  
|-------------------------|---------------------|  
|*msg_id* が RAISERROR に渡される場合、ID は sys.messages で定義する必要があります。|*error_number* パラメーターを sys.messages で定義する必要はありません。|  
|*msg_str* パラメーターには **printf** 書式スタイルを含めることができます。|*message* パラメーターでは **printf** 書式スタイルは受け入れられません。|  
|*severity* パラメーターでは例外の重大度を指定します。|*severity* パラメーターはありません。 例外を開始するために THROW を使用する場合、重大度は常に 16 に設定されます。 ただし、THROW を使用して既存の例外を再スローする場合、重大度はその例外の重大度レベルに設定されます。|  
  
## <a name="examples"></a>例  
  
### <a name="a-using-throw-to-raise-an-exception"></a>A. THROW を使用して例外を発生させる  
 次の例では、`THROW` ステートメントを使用して例外を発生させる方法を示します。  
  
```sql  
THROW 51000, 'The record does not exist.', 1;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 ```
 Msg 51000, Level 16, State 1, Line 1  
  
 The record does not exist.
 ```  
  
### <a name="b-using-throw-to-raise-an-exception-again"></a>B. THROW を使用して例外を再度発生させる  
 次の例では、`THROW` ステートメントを使用して最後にスローされた例外を再度発生させる方法を示します。  
  
```sql  
USE tempdb;  
GO  
CREATE TABLE dbo.TestRethrow  
(    ID INT PRIMARY KEY  
);  
BEGIN TRY  
    INSERT dbo.TestRethrow(ID) VALUES(1);  
--  Force error 2627, Violation of PRIMARY KEY constraint to be raised.  
    INSERT dbo.TestRethrow(ID) VALUES(1);  
END TRY  
BEGIN CATCH  
  
    PRINT 'In catch block.';  
    THROW;  
END CATCH;  
  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 ```
 In catch block. 
 Msg 2627, Level 14, State 1, Line 1  
 Violation of PRIMARY KEY constraint 'PK__TestReth__3214EC272E3BD7D3'. Cannot insert duplicate key in object 'dbo.TestRethrow'.  
 The statement has been terminated.
 ```  
  
### <a name="c-using-formatmessage-with-throw"></a>C. FORMATMESSAGE を THROW と共に使用する  
 次の例では、`FORMATMESSAGE` 関数を `THROW` と共に使用して、カスタマイズされたエラー メッセージをスローする方法を示します。 この例では、まず、`sp_addmessage` を使用して、ユーザー定義のエラー メッセージを作成します。 THROW ステートメントでは、RAISERROR のように、*message* パラメーターで書式引数が許可されないため、エラー メッセージ 60000 で想定される 3 つのパラメーター値を渡すために FORMATMESSAGE 関数が使用されます。  
  
```sql  
EXEC sys.sp_addmessage  
     @msgnum   = 60000  
    ,@severity = 16  
    ,@msgtext  = N'This is a test message with one numeric parameter (%d), one string parameter (%s), and another string parameter (%s).'  
    ,@lang = 'us_english';   
GO  
  
DECLARE @msg NVARCHAR(2048) = FORMATMESSAGE(60000, 500, N'First string', N'second string');   
  
THROW 60000, @msg, 1;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 ```
 Msg 60000, Level 16, State 1, Line 2  
 This is a test message with one numeric parameter (500), one string parameter (First string), and another string parameter (second string).
 ```  
  
## <a name="see-also"></a>参照  
 [FORMATMESSAGE &#40;Transact-SQL&#41;](../../t-sql/functions/formatmessage-transact-sql.md)   
 [データベース エンジン エラーの重大度](../../relational-databases/errors-events/database-engine-error-severities.md)   
 [ERROR_LINE &#40;Transact-SQL&#41;](../../t-sql/functions/error-line-transact-sql.md)   
 [ERROR_MESSAGE &#40;Transact-SQL&#41;](../../t-sql/functions/error-message-transact-sql.md)   
 [ERROR_NUMBER &#40;Transact-SQL&#41;](../../t-sql/functions/error-number-transact-sql.md)   
 [ERROR_PROCEDURE &#40;Transact-SQL&#41;](../../t-sql/functions/error-procedure-transact-sql.md)   
 [ERROR_SEVERITY &#40;Transact-SQL&#41;](../../t-sql/functions/error-severity-transact-sql.md)   
 [ERROR_STATE &#40;Transact-SQL&#41;](../../t-sql/functions/error-state-transact-sql.md)   
 [RAISERROR &#40;Transact-SQL&#41;](../../t-sql/language-elements/raiserror-transact-sql.md)   
 [@@ERROR &#40;Transact-SQL&#41;](../../t-sql/functions/error-transact-sql.md)   
 [GOTO &#40;Transact-SQL&#41;](../../t-sql/language-elements/goto-transact-sql.md)   
 [BEGIN...END &#40;Transact-SQL&#41;](../../t-sql/language-elements/begin-end-transact-sql.md)   
 [XACT_STATE &#40;Transact-SQL&#41;](../../t-sql/functions/xact-state-transact-sql.md)   
 [SET XACT_ABORT &#40;Transact-SQL&#41;](../../t-sql/statements/set-xact-abort-transact-sql.md)  
  
  

