---
description: '&#x40;&#x40;ERROR (Transact-SQL)'
title: '@@ERROR (Transact-SQL) | Microsoft Docs'
ms.custom: ''
ms.date: 08/29/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- '@@ERROR'
- '@@ERROR_TSQL'
dev_langs:
- TSQL
helpviewer_keywords:
- '@@ERROR function'
- errors [SQL Server], Transact-SQL
- error numbers [SQL Server]
ms.assetid: c8b43477-b6c0-49bf-a608-394a0b6cc7a2
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 97975059ad4aee3d6c64a0b31bb53fd1dc21ad36
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104747322"
---
# <a name="x40x40error-transact-sql"></a>&#x40;&#x40;ERROR (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  最後に実行した [!INCLUDE[tsql](../../includes/tsql-md.md)] ステートメントのエラー番号を返します。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```  
@@ERROR  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="return-types"></a>戻り値の型
 整数 (integer)  
  
## <a name="remarks"></a>解説  
 直前の [!INCLUDE[tsql](../../includes/tsql-md.md)] ステートメントでエラーが発生しなかった場合は、0 が返されます。  
  
 直前のステートメントでエラーが発生した場合は、エラー番号が返されます。 エラーが sys.messages カタログ ビューのエラーであった場合、そのエラーに対応する sys.messages.message_id 列の値が @@ERROR に含まれます。 @@ERROR エラー番号に関連付けられているテキストは、sys.messages で表示できます。  
  
 各ステートメントの実行時に @@ERROR はクリアおよびリセットされるので、ステートメントの検証を行った直後にこの変数の値を調べるか、または後で調べることができるようローカル変数に保存してください。  
  
 TRY...CATCH 構造を使用してエラーを処理します。 TRY...CATCH 構造では、その他のシステム関数 (ERROR_LINE、ERROR_MESSAGE、ERROR_PROCEDURE、ERROR_SEVERITY、ERROR_STATE) もサポートされます。これらの関数では、@@ERROR よりも多くのエラー情報が返されます。 また、ERROR_NUMBER 関数もサポートされます。この関数では、エラーが発生したステートメントの直後のステートメントでエラー番号を返すなどの操作を行えます。 詳細については、「 [TRY...CATCH &#40;Transact-SQL&#41;](../../t-sql/language-elements/try-catch-transact-sql.md)」を参照してください。  
  
## <a name="examples"></a>例  
  
### <a name="a-using-error-to-detect-a-specific-error"></a>A. @@ERROR を使用して特定のエラーを検出する  
 次の例では、`@@ERROR` を使用して、`UPDATE` ステートメントに CHECK 制約の違反 (エラー #547) がないかどうかを調べます。  
  
```sql  
USE AdventureWorks2012;  
GO  
UPDATE HumanResources.EmployeePayHistory  
    SET PayFrequency = 4  
    WHERE BusinessEntityID = 1;  
IF @@ERROR = 547
    BEGIN
    PRINT N'A check constraint violation occurred.';
    END
GO  
```  
  
### <a name="b-using-error-to-conditionally-exit-a-procedure"></a>B. @@ERROR を使用して条件的にプロシージャを終了する  
 次の例では、`IF...ELSE` ステートメントを使用して、ストアド プロシージャ内で `DELETE` ステートメントの後にある `@@ERROR` をテストします。 `@@ERROR` 変数の値は、呼び出し元のプログラムに返されるリターン コードとして採用され、プロシージャ実行の成否を示します。  
  
```sql  
USE AdventureWorks2012;  
GO  
-- Drop the procedure if it already exists.  
IF OBJECT_ID(N'HumanResources.usp_DeleteCandidate', N'P') IS NOT NULL  
    DROP PROCEDURE HumanResources.usp_DeleteCandidate;  
GO  
-- Create the procedure.  
CREATE PROCEDURE HumanResources.usp_DeleteCandidate   
    (  
    @CandidateID INT  
    )  
AS  
-- Execute the DELETE statement.  
DELETE FROM HumanResources.JobCandidate  
    WHERE JobCandidateID = @CandidateID;  
-- Test the error value.  
IF @@ERROR <> 0   
    BEGIN  
        -- Return 99 to the calling program to indicate failure.  
        PRINT N'An error occurred deleting the candidate information.';  
        RETURN 99;  
    END  
ELSE  
    BEGIN  
        -- Return 0 to the calling program to indicate success.  
        PRINT N'The job candidate has been deleted.';  
        RETURN 0;  
    END;  
GO  
```  
  
### <a name="c-using-error-with-rowcount"></a>C. @@ERROR を @@ROWCOUNT と共に使用する  
 次の例では、`@@ERROR` と `@@ROWCOUNT` を使用して、`UPDATE` ステートメントの操作を検証します。 ここでは、`@@ERROR` の値にエラーがないかどうかを調べ、`@@ROWCOUNT` を使用して、テーブルの行を正しく更新できたかどうかを確認します。  
  
```sql  
USE AdventureWorks2012;  
GO  
IF OBJECT_ID(N'Purchasing.usp_ChangePurchaseOrderHeader',N'P')IS NOT NULL  
    DROP PROCEDURE Purchasing.usp_ChangePurchaseOrderHeader;  
GO  
CREATE PROCEDURE Purchasing.usp_ChangePurchaseOrderHeader  
    (  
    @PurchaseOrderID INT  
    ,@BusinessEntityID INT  
   )  
AS  
-- Declare variables used in error checking.  
DECLARE @ErrorVar INT;  
DECLARE @RowCountVar INT;  
  
-- Execute the UPDATE statement.  
UPDATE PurchaseOrderHeader   
    SET BusinessEntityID = @BusinessEntityID   
    WHERE PurchaseOrderID = @PurchaseOrderID;  
  
-- Save the @@ERROR and @@ROWCOUNT values in local   
-- variables before they are cleared.  
SELECT @ErrorVar = @@ERROR  
    ,@RowCountVar = @@ROWCOUNT;  
  
-- Check for errors. If an invalid @BusinessEntityID was specified,  
-- the UPDATE statement returns a foreign key violation error #547.  
IF @ErrorVar <> 0  
    BEGIN  
        IF @ErrorVar = 547  
            BEGIN  
                PRINT N'ERROR: Invalid ID specified for new employee.';  
                 RETURN 1;  
            END  
        ELSE  
            BEGIN  
                PRINT N'ERROR: error '  
                    + RTRIM(CAST(@ErrorVar AS NVARCHAR(10)))  
                    + N' occurred.';  
                RETURN 2;  
            END  
    END  
  
-- Check the row count. @RowCountVar is set to 0   
-- if an invalid @PurchaseOrderID was specified.  
IF @RowCountVar = 0  
    BEGIN  
        PRINT 'Warning: The BusinessEntityID specified is not valid';  
        RETURN 1;  
    END  
ELSE  
    BEGIN  
        PRINT 'Purchase order updated with the new employee';  
        RETURN 0;  
    END;  
GO  
```  

  
## <a name="see-also"></a>参照  
 [TRY...CATCH &#40;Transact-SQL&#41;](../../t-sql/language-elements/try-catch-transact-sql.md)   
 [ERROR_LINE &#40;Transact-SQL&#41;](../../t-sql/functions/error-line-transact-sql.md)   
 [ERROR_MESSAGE &#40;Transact-SQL&#41;](../../t-sql/functions/error-message-transact-sql.md)   
 [ERROR_NUMBER &#40;Transact-SQL&#41;](../../t-sql/functions/error-number-transact-sql.md)   
 [ERROR_PROCEDURE &#40;Transact-SQL&#41;](../../t-sql/functions/error-procedure-transact-sql.md)   
 [ERROR_SEVERITY &#40;Transact-SQL&#41;](../../t-sql/functions/error-severity-transact-sql.md)   
 [ERROR_STATE &#40;Transact-SQL&#41;](../../t-sql/functions/error-state-transact-sql.md)   
 [@@ROWCOUNT &#40;Transact-SQL&#41;](../../t-sql/functions/rowcount-transact-sql.md)   
 [sys.messages &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/messages-for-errors-catalog-views-sys-messages.md)     
 [エラーとイベントのリファレンス &#40;データベース エンジン&#41;](../../relational-databases/errors-events/errors-and-events-reference-database-engine.md)     
  

