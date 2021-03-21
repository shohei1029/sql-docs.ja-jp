---
description: ERROR_NUMBER (Transact-SQL)
title: ERROR_NUMBER (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/16/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- ERROR_NUMBER_TSQL
- ERROR_NUMBER
dev_langs:
- TSQL
helpviewer_keywords:
- errors [SQL Server], line number
- messages [SQL Server], numbers
- TRY...CATCH [SQL Server]
- ERROR_NUMBER function
- CATCH block
ms.assetid: 1de85fff-1ca2-4b31-841b-926e571cb150
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 253d5531d7d296e47cc81bf1578d412383042414
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104747542"
---
# <a name="error_number-transact-sql"></a>ERROR_NUMBER (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

この関数は、TRY...CATCH 構造の CATCH ブロックが実行される原因となったエラーのエラー番号を返します。  

 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql  
ERROR_NUMBER ( )  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="return-types"></a>戻り値の型
 **int**  
  
## <a name="return-value"></a>戻り値  
CATCH ブロックで呼び出されると、`ERROR_NUMBER` は CATCH ブロックの実行を引き起こしたエラーのエラー番号を返します。  

`ERROR_NUMBER` は、CATCH ブロックの範囲外で呼び出されると、NULL を返します。  
  
## <a name="remarks"></a>解説  
`ERROR_NUMBER` は、CATCH ブロックのスコープ内の任意の場所で呼び出すことができます。  
  
`ERROR_NUMBER` は、実行回数に関係なく、あるいは `CATCH` ブロックのスコープ内の実行場所に関係なく、関連エラー番号を返します。 エラーが発生したステートメントの直後のステートメントのエラー番号のみを返す、@@ERROR などの関数とは対照的となります。  

`CATCH` ブロックが入れ子になっている場合、`ERROR_NUMBER` は、`CATCH` ブロックを参照した `CATCH` ブロックのスコープに固有のエラー番号を返します。 たとえば、外側の TRY...CATCH 構造の `CATCH` ブロックの中に `TRY...CATCH` 構造が含まれることがあります。 その内側の `CATCH` ブロック内では、`ERROR_NUMBER` は内側の `CATCH` ブロックを呼び出したエラーの番号を返します。 `ERROR_NUMBER` が外側の `CATCH` ブロック内で実行される場合、外側の `CATCH` ブロックを呼び出したエラーの番号を返します。  
  
## <a name="examples"></a>例  
  
### <a name="a-using-error_number-in-a-catch-block"></a>A. CATCH ブロックで ERROR_NUMBER を使用する  
この例は、0 除算エラーを生成する `SELECT` ステートメントを示しています。 `CATCH` ブロックはエラー番号を返します。  
  
```sql  
BEGIN TRY  
    -- Generate a divide-by-zero error.  
    SELECT 1/0;  
END TRY  
BEGIN CATCH  
    SELECT ERROR_NUMBER() AS ErrorNumber;  
END CATCH;  
GO  
```
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
```
-----------

(0 row(s) affected)

ErrorNumber
-----------
8134

(1 row(s) affected)

```  
  
### <a name="b-using-error_number-in-a-catch-block-with-other-error-handling-tools"></a>B. CATCH ブロックで ERROR_NUMBER を他のエラー処理ツールと一緒に使用する  
この例は、0 除算エラーを生成する `SELECT` ステートメントを示しています。 `CATCH` ブロックは、エラー番号と共にそのエラーに関する情報を返します。  

```sql  
BEGIN TRY  
    -- Generate a divide-by-zero error.  
    SELECT 1/0;  
END TRY  
BEGIN CATCH  
    SELECT  
        ERROR_NUMBER() AS ErrorNumber,  
        ERROR_SEVERITY() AS ErrorSeverity,  
        ERROR_STATE() AS ErrorState,  
        ERROR_PROCEDURE() AS ErrorProcedure,  
        ERROR_LINE() AS ErrorLine,  
        ERROR_MESSAGE() AS ErrorMessage;  
END CATCH;  
GO  
```
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
```
-----------

(0 row(s) affected)

ErrorNumber ErrorSeverity ErrorState  ErrorProcedure   ErrorLine  ErrorMessage
----------- ------------- ----------- ---------------  ---------- ----------------------------------
8134        16            1           NULL             4          Divide by zero error encountered.

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
  
  

