---
description: ROLLBACK TRANSACTION (Transact-SQL)
title: ROLLBACK TRANSACTION (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 09/12/2017
ms.prod: sql
ms.prod_service: synapse-analytics, database-engine, pdw, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- ROLLBACK TRANSACTION
- ROLLBACK
- ROLLBACK_TSQL
- ROLLBACK_TRANSACTION_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- transaction rollbacks [SQL Server]
- ROLLBACK TRANSACTION statement
- erasing data modifications [SQL Server]
- rolling back transactions, ROLLBACK TRANSACTION
- roll back transactions [SQL Server]
- savepoints [SQL Server]
ms.assetid: 6882c5bc-ff74-476a-984b-164aeb036c66
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 1d61827c2493379bd71d184949d963ddc24daf2d
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104748592"
---
# <a name="rollback-transaction-transact-sql"></a>ROLLBACK TRANSACTION (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  明示的または暗黙的なトランザクションを、トランザクションの開始位置またはトランザクション内のセーブポイントまでロールバックします。 ROLLBACK TRANSACTION を使用して、トランザクションの開始またはセーブポイント発行時点以降に行われたすべてのデータ変更を消去できます。 トランザクションが保持していたリソースも解放されます。  
  
  これには、ローカル変数またはテーブル変数に加えられた変更は含まれません。 これらは、このステートメントによって消去されることはありません。
  

 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql
ROLLBACK { TRAN | TRANSACTION }   
     [ transaction_name | @tran_name_variable  
     | savepoint_name | @savepoint_variable ]   
[ ; ]  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
 *transaction_name*  
 BEGIN TRANSACTION においてトランザクションに割り当てられた名前です。 *transaction_name* は識別子のルールに従っている必要があります。ただし、使用されるのはトランザクション名の先頭の 32 文字だけです。 トランザクションを入れ子にしている場合は、*transaction_name* は最も外側の BEGIN TRANSACTION ステートメントの名前である必要があります。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスで大文字と小文字が区別されない場合であっても、*transaction_name* では常に大文字と小文字が区別されます。  
  
 **@** *tran_name_variable*  
 有効なトランザクション名を格納しているユーザー定義変数の名前を指定します。 変数は、**char**、**varchar**、**nchar**、または **nvarchar** データ型を使用して宣言する必要があります。  
  
 *savepoint_name*  
 SAVE TRANSACTION ステートメントの *savepoint_name* です。 *savepoint_name* は、識別子のルールに従っている必要があります。 *savepoint_name* は、条件付きのロールバックがトランザクションの一部にしか影響しない場合に使用します。  
  
 **@** *savepoint_variable*  
 有効なセーブポイント名を格納しているユーザー定義変数の名前です。 変数は、**char**、**varchar**、**nchar**、または **nvarchar** データ型を使用して宣言する必要があります。  
  
## <a name="error-handling"></a>エラー処理  
 ROLLBACK TRANSACTION ステートメントでは、ユーザーに対するメッセージは生成されません。 ストアド プロシージャまたはトリガーで警告が必要な場合は、RAISERROR ステートメントまたは PRINT ステートメントを使用します。 エラーを指示するには、RAISERROR ステートメントの方が適しています。  
  
## <a name="general-remarks"></a>全般的な解説  
 *savepoint_name* または *transaction_name* を指定せずに ROLLBACK TRANSACTION を実行すると、トランザクションの先頭までロールバックします。 トランザクションを入れ子にしている場合は、同じステートメントが、内部のすべてのトランザクションを、最も外側の BEGIN TRANSACTION ステートメントにロールバックします。 どちらの場合も、ROLLBACK TRANSACTION では @@TRANCOUNT システム関数が 0 に減らされます。 ROLLBACK TRANSACTION *savepoint_name* で @@TRANCOUNT が減少することはありません。  
  
 ROLLBACK TRANSACTION では、BEGIN DISTRIBUTED TRANSACTION によって明示的にまたはローカル トランザクションのエスカレートにより開始された分散型トランザクション内の *savepoint_name* は参照できません。  
  
 COMMIT TRANSACTION ステートメントの実行後は、トランザクションをロールバックできません。ただし、COMMIT TRANSACTION が、ロールバックするトランザクション内に含まれている入れ子にされたトランザクションに関連付けられている場合を除きます。 この場合、COMMIT TRANSACTION を発行していたとしても、入れ子になったトランザクションはロールバックされます。  
  
 トランザクションの中で、同じセーブポイント名を重複して指定することができます。しかし、重複するセーブポイント名を使用する ROLLBACK TRANSACTION は、そのセーブポイント名を使用する最新の SAVE TRANSACTION にしかロールバックできません。  
  
## <a name="interoperability"></a>相互運用性  
 ストアド プロシージャの中で、*savepoint_name* または *transaction_name* を指定せずに ROLLBACK TRANSACTION ステートメントを実行すると、すべてのステートメントが最も外側の BEGIN TRANSACTION までロールバックされます。 ROLLBACK TRANSACTION ステートメントを実行するストアド プロシージャで、終了時の @@TRANCOUNT の値と呼び出し時の @@TRANCOUNT の値が異なる場合は、情報メッセージが出力されます。 このメッセージが出力されても、その後の処理に影響はありません。  
  
 ROLLBACK TRANSACTION がトリガー内で実行された場合  
  
-   現在のトランザクションのその時点までに加えられたすべてのデータ変更 (トリガーによって行われた変更も含む) がロールバックされます。  
  
-   ROLLBACK ステートメントの実行後、トリガーによって残りのすべてのステートメントが継続して実行されます。 これらのステートメントのいずれかがデータを変更する場合、その変更はロールバックされません。 残りのステートメントを実行しても入れ子にしたトリガーは起動されません。  
  
-   トリガーを起動したステートメントの後で、バッチ内のステートメントは実行されません。  
  
@@TRANCOUNT は、自動コミット モードの場合であっても、トリガーに入るたびに 1 ずつ増加します。 つまり、トリガーは暗黙の入れ子にされたトランザクションとして扱われます。  
  
ストアド プロシージャで ROLLBACK TRANSACTION ステートメントを実行しても、そのプロシージャを呼び出したバッチ内にある次のステートメントには影響しません。 トリガーの中の ROLLBACK TRANSACTION ステートメントは、トリガーを起動したステートメントを含むバッチを終了します。バッチ内の以降のステートメントは実行されません。  
  
カーソル上での ROLLBACK の効果は、次の 3 つのルールによって定義されます。  
  
1.  CURSOR_CLOSE_ON_COMMIT が ON の場合、ROLLBACK ではオープンしているすべてのカーソルをクローズしますが、その割り当ては解除しません。  
  
2.  CURSOR_CLOSE_ON_COMMIT を OFF に設定した場合、ROLLBACK は、オープンしていて同期している静的カーソルおよび非反映型カーソル、あるいは完全に生成されている非同期な静的カーソルのいずれにも影響しません。 他のタイプのオープン カーソルは、クローズしますが、割り当ては解除されません。  
  
3.  バッチを終了し、内部のロールバックを生成するエラーは、エラー ステートメントを含むバッチの中で宣言された、すべてのカーソルの割り当てを解除します。 すべてのカーソルは、そのタイプや CURSOR_CLOSE_ON_COMMIT の設定にかかわらず、割り当てを解除されます。 これには、エラー バッチによって呼び出されるストアド プロシージャで宣言されたカーソルも含まれます。 エラー バッチの前にバッチ内で宣言されたカーソルは、ルール 1 および 2 に従います。 このタイプのエラーの例としてはデッドロック エラーがあります。 トリガーの中で実行される ROLLBACK ステートメントも、このタイプのエラーを自動的に生成します。  
  
## <a name="locking-behavior"></a>ロック動作  
 ROLLBACK TRANSACTION ステートメントで *savepoint_name* を指定している場合、そのセーブポイントより後の時点で取得されたロックは解放されます。ただし、エスカレーションおよび変換が行われた場合を除きます。 これらのロックは解放されず、前のロック モードに戻ることはありません。  
  
## <a name="permissions"></a>アクセス許可  
 ロール **public** のメンバーシップが必要です。  
  
## <a name="examples"></a>例  
 次の例では、名前付きトランザクションをロールバックした場合の影響を示します。 テーブルを作成した後、次のステートメントで名前付きトランザクションが開始され、2 行が挿入され、変数 @TransactionName に指定されたトランザクションがロールバックされます。 名前付きトランザクション外の外側にあるもう 1 つのステートメントで 2 行が挿入されます。 クエリによって、前のステートメントの結果が返されます。   
  
```sql    
USE tempdb;  
GO  
CREATE TABLE ValueTable ([value] INT);  
GO  
  
DECLARE @TransactionName VARCHAR(20) = 'Transaction1';  
  
BEGIN TRAN @TransactionName  
       INSERT INTO ValueTable VALUES(1), (2);  
ROLLBACK TRAN @TransactionName;  
  
INSERT INTO ValueTable VALUES(3),(4);  
  
SELECT [value] FROM ValueTable;  
  
DROP TABLE ValueTable;  
```  
[!INCLUDE[ssresult-md](../../includes/ssresult-md.md)]  
```  
value  
-----   
3    
4  
```  
  
  
## <a name="see-also"></a>参照  
 [BEGIN DISTRIBUTED TRANSACTION &#40;Transact-SQL&#41;](../../t-sql/language-elements/begin-distributed-transaction-transact-sql.md)   
 [BEGIN TRANSACTION &#40;Transact-SQL&#41;](../../t-sql/language-elements/begin-transaction-transact-sql.md)   
 [COMMIT TRANSACTION &#40;Transact-SQL&#41;](../../t-sql/language-elements/commit-transaction-transact-sql.md)   
 [COMMIT WORK &#40;Transact-SQL&#41;](../../t-sql/language-elements/commit-work-transact-sql.md)   
 [ROLLBACK WORK &#40;Transact-SQL&#41;](../../t-sql/language-elements/rollback-work-transact-sql.md)   
 [SAVE TRANSACTION &#40;Transact-SQL&#41;](../../t-sql/language-elements/save-transaction-transact-sql.md)  
  
  
