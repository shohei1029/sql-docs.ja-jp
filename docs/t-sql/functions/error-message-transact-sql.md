---
description: ERROR_MESSAGE (Transact-SQL)
title: ERROR_MESSAGE (Transact-SQL)
ms.custom: ''
ms.date: 03/16/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- ERROR_MESSAGE_TSQL
- ERROR_MESSAGE
dev_langs:
- TSQL
helpviewer_keywords:
- ERROR_MESSAGE function
- errors [SQL Server], text of
- messages [SQL Server], text of
- TRY...CATCH [SQL Server]
- CATCH block
ms.assetid: f32877a6-5f17-418c-a32c-5a1a344b3c45
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 5e2a943c525a637d6d6c092593e55782ba677d2e
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104747562"
---
# <a name="error_message-transact-sql"></a>ERROR_MESSAGE (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

この関数は、TRY...CATCH 構造の CATCH ブロックが実行される原因となったエラーのメッセージ テキストを返します。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql  
ERROR_MESSAGE ( )   
```  

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="return-types"></a>戻り値の型
 **nvarchar (4000)**  
  
## <a name="return-value"></a>戻り値  
`ERROR_MESSAGE` は、CATCH ブロック内で呼び出されると、`CATCH` ブロックが実行される原因となったエラー メッセージの全テキストを返します。 このテキストには、長さ、オブジェクト名、時刻など、置換可能なパラメーターに指定された値が含まれます。  
  
`ERROR_MESSAGE` は、CATCH ブロックの範囲外で呼び出されると、NULL を返します。  
  
## <a name="remarks"></a>解説  
`ERROR_MESSAGE` は、CATCH ブロックのスコープ内の任意の場所で呼び出すことができます。  
  
`ERROR_MESSAGE` は、実行回数に関係なく、あるいは `CATCH` ブロックのスコープ内の実行場所に関係なく、関連エラー メッセージを返します。 エラーが発生したステートメントの直後のステートメントのエラー番号のみを返す、@@ERROR などの関数とは対照的となります。  
  
`CATCH` ブロックが入れ子になっている場合、`ERROR_MESSAGE` は、`CATCH` ブロックを参照した `CATCH` ブロックのスコープに固有のエラー メッセージを返します。 たとえば、外側の TRY...CATCH 構造の `CATCH` ブロックの中に `TRY...CATCH` 構造が含まれることがあります。 その内側の `CATCH` ブロック内では、`ERROR_MESSAGE` は内側の `CATCH` ブロックを呼び出したエラーからのメッセージを返します。 `ERROR_MESSAGE` が外側の `CATCH` ブロック内で実行される場合、外側の `CATCH` ブロックを呼び出したエラーからのメッセージを返します。  
  
## <a name="examples"></a>例  
  
### <a name="a-using-error_message-in-a-catch-block"></a>A. CATCH ブロックで ERROR_MESSAGE を使用する  
この例は、0 除算エラーを生成する `SELECT` ステートメントを示しています。 `CATCH` ブロックはエラー メッセージを返します。  
  
```sql   
BEGIN TRY  
    -- Generate a divide-by-zero error.  
    SELECT 1/0;  
END TRY  
BEGIN CATCH  
    SELECT ERROR_MESSAGE() AS ErrorMessage;  
END CATCH;  
GO  
```
[!INCLUDE[ssResult](../../includes/ssresult-md.md)] 
```
-----------

(0 row(s) affected)

ErrorMessage
----------------------------------
Divide by zero error encountered.

(1 row(s) affected)

```  
  
### <a name="b-using-error_message-in-a-catch-block-with-other-error-handling-tools"></a>B.  CATCH ブロックで、別のエラー処理ツールと一緒に ERROR_MESSAGE を使用する  
この例は、0 除算エラーを生成する `SELECT` ステートメントを示しています。 `CATCH` ブロックは、エラー メッセージと共にそのエラーに関する情報を返します。  
  
```sql  
BEGIN TRY  
    -- Generate a divide-by-zero error.  
    SELECT 1/0;  
END TRY  
BEGIN CATCH  
    SELECT  
        ERROR_NUMBER() AS ErrorNumber  
        ,ERROR_SEVERITY() AS ErrorSeverity  
        ,ERROR_STATE() AS ErrorState  
        ,ERROR_PROCEDURE() AS ErrorProcedure  
        ,ERROR_LINE() AS ErrorLine  
        ,ERROR_MESSAGE() AS ErrorMessage;  
END CATCH;  
GO  
```
[!INCLUDE[ssResult](../../includes/ssresult-md.md)] 
```
-----------

(0 row(s) affected)

ErrorNumber ErrorSeverity ErrorState  ErrorProcedure  ErrorLine  ErrorMessage
----------- ------------- ----------- --------------- ---------- ----------------------------------
8134        16            1           NULL            4          Divide by zero error encountered.

(1 row(s) affected)

```
  
## <a name="see-also"></a>参照  
 [sys.messages &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/messages-for-errors-catalog-views-sys-messages.md)   
 [TRY...CATCH &#40;Transact-SQL&#41;](../../t-sql/language-elements/try-catch-transact-sql.md)   
 [ERROR_LINE &#40;Transact-SQL&#41;](../../t-sql/functions/error-line-transact-sql.md)   
 [ERROR_MESSAGE &#40;Transact-SQL&#41;](../../t-sql/functions/error-message-transact-sql.md)   
 [ERROR_PROCEDURE &#40;Transact-SQL&#41;](../../t-sql/functions/error-procedure-transact-sql.md)   
 [ERROR_SEVERITY &#40;Transact-SQL&#41;](../../t-sql/functions/error-severity-transact-sql.md)   
 [ERROR_STATE &#40;Transact-SQL&#41;](../../t-sql/functions/error-state-transact-sql.md)   
 [RAISERROR &#40;Transact-SQL&#41;](../../t-sql/language-elements/raiserror-transact-sql.md)   
 [@@ERROR &#40;Transact-SQL&#41;](../../t-sql/functions/error-transact-sql.md)     
 [エラーとイベントのリファレンス &#40;データベース エンジン&#41;](../../relational-databases/errors-events/errors-and-events-reference-database-engine.md)     
  
    

