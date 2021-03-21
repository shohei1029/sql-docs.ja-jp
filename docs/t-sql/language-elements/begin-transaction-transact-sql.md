---
description: BEGIN TRANSACTION (Transact-SQL)
title: BEGIN TRANSACTION (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.prod_service: synapse-analytics, database-engine, pdw, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- BEGIN_TRANSACTION_TSQL
- TRANSACTION_TSQL
- TRANSACTION
- BEGIN TRANSACTION
- BEGIN TRAN
- BEGIN_TRAN_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- transaction logs [SQL Server], BEGIN TRANSACTION statement
- marked transactions [SQL Server], BEGIN TRANSACTION statement
- BEGIN TRANSACTION statement
- local transactions [SQL Server]
- marking transactions [SQL Server]
- transactions [SQL Server], starting
- transaction names [SQL Server]
- starting point marked for transactions
- starting transactions
ms.assetid: c6258df4-11f1-416a-816b-54f98c11145e
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 3c93301d165b8adbd00942b92c1ebdeb87701ffa
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104747912"
---
# <a name="begin-transaction-transact-sql"></a>BEGIN TRANSACTION (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  明示的なローカル トランザクションの開始位置をマークします。 明示的なトランザクションは、BEGIN TRANSACTION ステートメントで始まり、COMMIT または ROLLBACK ステートメントで終了します。  

 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql
--Applies to SQL Server and Azure SQL Database
 
BEGIN { TRAN | TRANSACTION }   
    [ { transaction_name | @tran_name_variable }  
      [ WITH MARK [ 'description' ] ]  
    ]  
[ ; ]  
```  
 
```syntaxsql
--Applies to Azure Synapse Analytics and Parallel Data Warehouse
 
BEGIN { TRAN | TRANSACTION }   
[ ; ]  
```  

  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
 *transaction_name*  
 **適用対象:** SQL Server (2008 以降)、Azure SQL Database
 
 トランザクションに割り当てられた名前を指定します。 *transaction_name* は識別子の規則に従っている必要があり、32 文字以内で指定する必要があります。 トランザクション名は、入れ子にされた BEGIN...COMMIT ステートメントまたは BEGIN...ROLLBACK ステートメントの最も外側の組だけに使用します。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスで大文字と小文字が区別されない場合であっても、*transaction_name* では常に大文字と小文字が区別されます。  
  
 @*tran_name_variable*  
 **適用対象:** SQL Server (2008 以降)、Azure SQL Database
 
 有効なトランザクション名を格納しているユーザー定義変数の名前を指定します。 変数は、**char**、**varchar**、**nchar**、または **nvarchar** データ型を使用して宣言する必要があります。 変数に 32 文字を超える文字が渡された場合は、最初の 32 文字だけが使用され、残りの文字は切り捨てられます。  
  
 WITH MARK [ '*description*' ]  
**適用対象:** SQL Server (2008 以降)、Azure SQL Database

ログの中でトランザクションにマークを付けます。 *description* は、マーク名を示す文字列です。 *description* が 128 文字を超えている場合は、128 文字に切り捨てられてから msdb.dbo.logmarkhistory テーブルに格納されます。  
  
 WITH MARK を使用する場合は、トランザクション名を指定する必要があります。 WITH MARK によって、トランザクション ログを指定のマークに復元できます。  
  
## <a name="general-remarks"></a>全般的な解説
BEGIN TRANSACTION では、@@TRANCOUNT は 1 ずつ増えます。
  
BEGIN TRANSACTION は、接続で参照されるデータが論理的にも物理的にも一貫している位置を表します。 エラーが発生した場合は、BEGIN TRANSACTION 以降に加えられたすべてのデータ修正をロールバックし、一貫性が確認されている状態にデータを戻すことができます。 それぞれのトランザクションの終了時点は、エラーなく完了した場合は COMMIT TRANSACTION を実行して修正内容をデータベースに永久保存した時点になり、エラーが発生した場合は ROLLBACK TRANSACTION ステートメントですべての修正内容を消去した時点になります。  
  
BEGIN TRANSACTION では、ステートメントを実行する接続のローカル トランザクションが開始されます。 この接続で実行される [!INCLUDE[tsql](../../includes/tsql-md.md)] ステートメントをサポートするため、トランザクションでは多くのリソースが取得されますが、これらのリソースは現在のトランザクション分離レベルの設定に応じてロックされ、COMMIT TRANSACTION または ROLLBACK TRANSACTION ステートメントのいずれかでトランザクションが完了するまでロックされたままになります。 トランザクションが完了するまでの間、他のユーザーはロックされたリソースにアクセスできなくなります。また、ログの切り捨ても行われません。  
  
 BEGIN TRANSACTION ではローカル トランザクションが開始されますが、これはその後アプリケーションで INSERT、UPDATE、DELETE ステートメントなど、ログへの記録が必要な操作が行われるまで、トランザクション ログに記録されません。 アプリケーションでは、ロックの取得などの操作を行って、SELECT ステートメントのトランザクション分離レベルを保護することができます。ただし、アプリケーションで変更操作が行われるまで、ログには何も記録されません。  
  
 1 つのトランザクション名で、何重にも入れ子になったトランザクション内の複数のトランザクションを指定しても、トランザクションに影響はほとんどありません。 システムに登録されるのは、最初の (最も外側の) トランザクション名だけです。 他の名前にロールバックするとエラーが発生します。ただし、有効なセーブポイント名へのロールバックではエラーは発生しません。 このエラーが発生した場合、ロールバック前に実行されたステートメントは一切ロールバックされません。 ステートメントは、外側のトランザクションがロールバックされた場合にのみロールバックされます。  
  
 BEGIN TRANSACTION ステートメントがコミットまたはロールバックされる前に次の操作を実行すると、このステートメントで開始されたローカル トランザクションは、分散トランザクションにエスカレートされます。  
  
-   リンク サーバー上のリモート テーブルを参照する INSERT、DELETE、または UPDATE ステートメントを実行する。 リンク サーバーへのアクセスに使用する OLE DB プロバイダーで ITransactionJoin インターフェイスがサポートされていない場合、INSERT、UPDATE、または DELETE ステートメントは失敗します。  
  
-   REMOTE_PROC_TRANSACTIONS オプションが ON のときに、リモート ストアド プロシージャを呼び出す。  
  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のローカル コピーはトランザクションのコントローラーになり、[!INCLUDE[msCoName](../../includes/msconame-md.md)] 分散トランザクション コーディネーター (MS DTC) を使用して分散トランザクションを管理します。  
  
 BEGIN DISTRIBUTED TRANSACTION を使用して、トランザクションを分散トランザクションとして明示的に実行できます。 詳細については、「[BEGIN DISTRIBUTED TRANSACTION &#40;Transact-SQL&#41;](../../t-sql/language-elements/begin-distributed-transaction-transact-sql.md)」を参照してください。  
  
 SET IMPLICIT_TRANSACTIONS が ON に設定されている場合、BEGIN TRANSACTION ステートメントは 2 つの入れ子構造のトランザクションを作成します。 詳細については、「[SET IMPLICIT_TRANSACTIONS &#40;Transact-SQL&#41;](../../t-sql/statements/set-implicit-transactions-transact-sql.md)」を参照してください。  
  
## <a name="marked-transactions"></a>マーク付きのトランザクション  
 WITH MARK オプションを使用すると、トランザクション名がトランザクション ログに挿入されます。 データベースを以前の状態に復元する場合は、日付や時刻の代わりにマークが付いたトランザクションを使用できます。 詳細については、「[マークされたトランザクションを使用して関連するデータベースを一貫した状態に復元する方法 &#40;完全復旧モデル&#41;](../../relational-databases/backup-restore/use-marked-transactions-to-recover-related-databases-consistently.md)」と「[RESTORE &#40;Transact-SQL&#41;](../../t-sql/statements/restore-statements-transact-sql.md)」を参照してください。  
  
 さらに、関連するデータベースのセットを論理的に一貫した状態に復元する必要がある場合は、トランザクション ログ マークが必要です。 分散トランザクションによって、関連するデータベースのトランザクション ログにマークを設定できます。 関連するデータベースのセットをこれらのマークに復元すると、トランザクションとして一貫性のあるデータベースのセットが作成されます。 関連するデータベースにマークを設定するには、特別な手順が必要です。  
  
 トランザクション ログにマークが設定されるのは、マーク付きのトランザクションによってデータベースが更新される場合のみです。 データを変更しないトランザクションには、マークは付きません。  
  
 BEGIN TRAN *new_name* WITH MARK は、マークが付いていない既存のトランザクション内で入れ子にできます。 この場合、トランザクションに既に割り当てられている名前に関係なく、*new_name* がトランザクションのマーク名になります。 次の例では、`M2` がマーク名になります。  
  
```sql  
BEGIN TRAN T1;  
UPDATE table1 ...;  
BEGIN TRAN M2 WITH MARK;  
UPDATE table2 ...;  
SELECT * from table1;  
COMMIT TRAN M2;  
UPDATE table3 ...;  
COMMIT TRAN T1;  
```  
  
 トランザクションを入れ子にしている場合、既にマークが付いているトランザクションにマークを付けようとすると、エラーではなく警告のメッセージが表示されます。  
  
 "BEGIN TRAN T1 WITH MARK ...;"  
  
 "UPDATE table1 ...;"  
  
 "BEGIN TRAN M2 WITH MARK ...;"  
  
 "Server:Msg 3920, Level 16, State 1, Line 3"  
  
 "WITH MARK オプションだけが最初の BEGIN TRAN WITH MARK ステートメントに適用されます。"  
  
 "オプションは無視されます。"  
  
## <a name="permissions"></a>アクセス許可  
 public ロールのメンバーシップが必要です。  
  
## <a name="examples"></a>例  
  
### <a name="a-using-an-explicit-transaction"></a>A. 明示的なトランザクションを使用します。
**適用対象:** SQL Server (2008 以降)、Azure SQL Database、[!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)]、Parallel Data Warehouse

この例では、AdventureWorks を使用します。 

```sql
BEGIN TRANSACTION;  
DELETE FROM HumanResources.JobCandidate  
    WHERE JobCandidateID = 13;  
COMMIT;  
```

### <a name="b-rolling-back-a-transaction"></a>B. トランザクションのロールバック
**適用対象:** SQL Server (2008 以降)、Azure SQL Database、[!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)]、Parallel Data Warehouse

次の例では、トランザクションのロールバックの効果を示します。 この例で ROLLBACK ステートメントがロールバックされます、INSERT ステートメントでは、作成されたテーブルはそのまま残ります。

```sql
CREATE TABLE ValueTable (id INT);  
BEGIN TRANSACTION;  
       INSERT INTO ValueTable VALUES(1);  
       INSERT INTO ValueTable VALUES(2);  
ROLLBACK;  

```

### <a name="c-naming-a-transaction"></a>C. トランザクションの名前を指定する 
**適用対象:** SQL Server (2008 以降)、Azure SQL Database

次の例では、トランザクションの名前を指定する方法を示します。  
  
```sql
DECLARE @TranName VARCHAR(20);  
SELECT @TranName = 'MyTransaction';  
  
BEGIN TRANSACTION @TranName;  
USE AdventureWorks2012;  
DELETE FROM AdventureWorks2012.HumanResources.JobCandidate  
    WHERE JobCandidateID = 13;  
  
COMMIT TRANSACTION @TranName;  
GO  
```  
  
### <a name="d-marking-a-transaction"></a>D. トランザクションにマークを付ける  
**適用対象:** SQL Server (2008 以降)、Azure SQL Database

次の例では、トランザクションにマークを付ける方法を示します。 トランザクション `CandidateDelete` にマークが付けられています。  
  
```sql  
BEGIN TRANSACTION CandidateDelete  
    WITH MARK N'Deleting a Job Candidate';  
GO  
USE AdventureWorks2012;  
GO  
DELETE FROM AdventureWorks2012.HumanResources.JobCandidate  
    WHERE JobCandidateID = 13;  
GO  
COMMIT TRANSACTION CandidateDelete;  
GO  
```  
  
## <a name="see-also"></a>参照  
 [BEGIN DISTRIBUTED TRANSACTION &#40;Transact-SQL&#41;](../../t-sql/language-elements/begin-distributed-transaction-transact-sql.md)   
 [COMMIT TRANSACTION &#40;Transact-SQL&#41;](../../t-sql/language-elements/commit-transaction-transact-sql.md)   
 [COMMIT WORK &#40;Transact-SQL&#41;](../../t-sql/language-elements/commit-work-transact-sql.md)   
 [ROLLBACK TRANSACTION &#40;Transact-SQL&#41;](../../t-sql/language-elements/rollback-transaction-transact-sql.md)   
 [ROLLBACK WORK &#40;Transact-SQL&#41;](../../t-sql/language-elements/rollback-work-transact-sql.md)   
 [SAVE TRANSACTION &#40;Transact-SQL&#41;](../../t-sql/language-elements/save-transaction-transact-sql.md)  
  
  
