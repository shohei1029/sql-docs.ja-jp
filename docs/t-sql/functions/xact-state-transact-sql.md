---
description: XACT_STATE (Transact-SQL)
title: XACT_STATE (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/16/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- XACT_STATE
- XACT_STATE_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- states [SQL Server], transactions
- uncommittable transactions
- sessions [SQL Server], active transactions
- XACT_STATE function
- transactions [SQL Server], state
- active transactions
ms.assetid: e9300827-e793-4eb6-9042-ffa0204aeb50
author: julieMSFT
ms.author: jrasnick
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: dfaf2b06022579eaeb58fdfe7c2aad13c8b0df83
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104754002"
---
# <a name="xact_state-transact-sql"></a>XACT_STATE (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  現在実行されている要求のユーザー トランザクション状態を報告するスカラー関数です。 XACT_STATE は、要求にアクティブなユーザー トランザクションがあるかどうか、およびそのトランザクションがコミット可能かどうかを示します。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql  
XACT_STATE()  
```  

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

[!INCLUDE[synapse-analytics-od-unsupported-syntax](../../includes/synapse-analytics-od-unsupported-syntax.md)]

## <a name="return-type"></a>戻り値の型  
 **smallint**  
  
## <a name="remarks"></a>注釈  
 XACT_STATE では次の値が返されます。  
  
|戻り値|説明|  
|------------------|-------------|  
|1|現在の要求にアクティブなユーザー トランザクションが存在する。 要求では、データ書き込み、トランザクションのコミットなど、任意の操作を実行できます。|  
|0|現在の要求にアクティブなユーザー トランザクションが存在しない。|  
|-1|現在の要求にアクティブなユーザー トランザクションが存在するが、コミットできないトランザクションとして分類されるエラーが発生した。 要求でトランザクションのコミットまたはセーブポイントへのロールバックは行えず、トランザクションの完全ロールバックだけを要求できます。 要求では、トランザクションをロールバックするまで書き込み操作は実行できず、 読み取り操作だけを実行できます。 トランザクションがロールバックされた後は、要求では読み取り、書き込み両方の操作を実行でき、新しいトランザクションを開始できます。<br /><br /> 外側のバッチの実行が完了すると、コミット不可能なトランザクションは、[!INCLUDE[ssDE](../../includes/ssde-md.md)] によって自動的にロールバックされます。 トランザクションがコミット不可能な状態になったときにエラー メッセージが送信されなかった場合、バッチが完了すると、エラー メッセージがクライアント アプリケーションに送信されます。 このメッセージは、コミット不可能なトランザクションが検出され、ロールバックされたことを示します。|  
  
 現在の要求にアクティブなユーザー トランザクションがあるかどうかを検出するには、XACT_STATE 関数と @@TRANCOUNT 関数の両方を使用できます。 ただし、@@TRANCOUNT では、検出されたトランザクションがコミット不可能なトランザクションとして分類されているかどうかは判断できません。 また、XACT_STATE では、入れ子になったトランザクションが存在するかどうかは判断できません。  
  
## <a name="examples"></a>例  
 次の例では、`XACT_STATE` 構造の `CATCH` ブロックで `TRY...CATCH` を使用し、トランザクションをコミットまたはロールバックするかどうかを確認します。 ここでは `SET XACT_ABORT` が `ON` に設定されているため、制約違反エラーによってトランザクションはコミットできない状態になります。  
  
```sql  
USE AdventureWorks2012;  
GO  
  
-- SET XACT_ABORT ON will render the transaction uncommittable  
-- when the constraint violation occurs.  
SET XACT_ABORT ON;  
  
BEGIN TRY  
    BEGIN TRANSACTION;  
        -- A FOREIGN KEY constraint exists on this table. This   
        -- statement will generate a constraint violation error.  
        DELETE FROM Production.Product  
            WHERE ProductID = 980;  
  
    -- If the delete operation succeeds, commit the transaction. The CATCH  
    -- block will not execute.  
    COMMIT TRANSACTION;  
END TRY  
BEGIN CATCH  
    -- Test XACT_STATE for 0, 1, or -1.  
    -- If 1, the transaction is committable.  
    -- If -1, the transaction is uncommittable and should   
    --     be rolled back.  
    -- XACT_STATE = 0 means there is no transaction and  
    --     a commit or rollback operation would generate an error.  
  
    -- Test whether the transaction is uncommittable.  
    IF (XACT_STATE()) = -1  
    BEGIN  
        PRINT 'The transaction is in an uncommittable state.' +  
              ' Rolling back transaction.'  
        ROLLBACK TRANSACTION;  
    END;  
  
    -- Test whether the transaction is active and valid.  
    IF (XACT_STATE()) = 1  
    BEGIN  
        PRINT 'The transaction is committable.' +   
              ' Committing transaction.'  
        COMMIT TRANSACTION;     
    END;  
END CATCH;  
GO  
```  
  
## <a name="see-also"></a>参照  
 [@@TRANCOUNT &#40;Transact-SQL&#41;](../../t-sql/functions/trancount-transact-sql.md)   
 [BEGIN TRANSACTION &#40;Transact-SQL&#41;](../../t-sql/language-elements/begin-transaction-transact-sql.md)   
 [COMMIT TRANSACTION &#40;Transact-SQL&#41;](../../t-sql/language-elements/commit-transaction-transact-sql.md)   
 [ROLLBACK TRANSACTION &#40;Transact-SQL&#41;](../../t-sql/language-elements/rollback-transaction-transact-sql.md)   
 [SAVE TRANSACTION &#40;Transact-SQL&#41;](../../t-sql/language-elements/save-transaction-transact-sql.md)   
 [TRY...CATCH &#40;Transact-SQL&#41;](../../t-sql/language-elements/try-catch-transact-sql.md)  
  
  
