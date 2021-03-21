---
description: SET XACT_ABORT (Transact-SQL)
title: SET XACT_ABORT (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 10/03/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- XACT_ABORT_TSQL
- XACT_ABORT
- SET XACT_ABORT
- SET_XACT_ABORT_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- transaction rollbacks [SQL Server]
- XACT_ABORT option
- automatic transaction roll backs
- transactions [SQL Server], rolling back
- rolling back transactions, SET XACT_ABORT
- roll back transactions [SQL Server]
- SET XACT_ABORT statement
ms.assetid: cbcaa433-58f2-4dc3-a077-27273bef65b5
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 14498e36a5b3ae8e4790f5f6122d721af093faea
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104754432"
---
# <a name="set-xact_abort-transact-sql"></a>SET XACT_ABORT (Transact-SQL)

[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

> [!NOTE]
> **THROW** ステートメントは **SET XACT_ABORT** に従いますが、 **RAISERROR** は従いません。 新しいアプリケーションでは、**RAISERROR** の代わりに **THROW** を使ってください。

[!INCLUDE[tsql](../../includes/tsql-md.md)] ステートメントによって実行時エラーが発生した場合に、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] が自動的に現在のトランザクションをロールバックするかどうかを指定します。

![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)

## <a name="syntax"></a>構文

```syntaxsql
SET XACT_ABORT { ON | OFF }
```

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="remarks"></a>解説

SET XACT_ABORT が ON の場合は、[!INCLUDE[tsql](../../includes/tsql-md.md)] ステートメントで実行時エラーが発生すると、トランザクション全体が終了し、ロールバックされます。

SET XACT_ABORT が OFF の場合は、エラーが発生した [!INCLUDE[tsql](../../includes/tsql-md.md)] ステートメントのみがロールバックされ、トランザクションの処理は継続される場合があります。 SET XACT_ABORT が OFF であっても、エラーの重大度によってはトランザクション全体がロールバックされる場合があります。 OFF は T-SQL ステートメントの既定の設定で、ON はトリガーの既定の設定です。

構文エラーなどのコンパイル エラーは、SET XACT_ABORT の影響を受けません。

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] などのほとんどの OLE DB プロバイダーに対する、明示的または暗黙的なトランザクションのデータ変更ステートメントでは、XACT_ABORT は必ず ON にします。 このオプションが要求されないのは、入れ子になったトランザクションをプロバイダーがサポートしている場合のみです。

ANSI_WARNINGS=OFF の場合、権限違反によってトランザクションは中止されます。

SET XACT_ABORT は、解析時ではなく実行時に設定されます。

この設定の現在の設定を表示するには、次のクエリを実行します。

```sql
DECLARE @XACT_ABORT VARCHAR(3) = 'OFF';
IF ( (16384 & @@OPTIONS) = 16384 ) SET @XACT_ABORT = 'ON';
SELECT @XACT_ABORT AS XACT_ABORT;

```

## <a name="examples"></a>例

次の例では、他の [!INCLUDE[tsql](../../includes/tsql-md.md)] ステートメントを含むトランザクションで、外部キー違反エラーが発生します。 最初のステートメント セットでは、エラーが生成されますが、他のステートメントは正常に実行され、トランザクションは正常にコミットされます。 2 番目のステートメントでは、`SET XACT_ABORT` は `ON` に設定されます。 このため、ステートメント エラーが発生すると、バッチが終了し、トランザクションはロールバックされます。

```sql
IF OBJECT_ID(N't2', N'U') IS NOT NULL
    DROP TABLE t2;
GO
IF OBJECT_ID(N't1', N'U') IS NOT NULL
    DROP TABLE t1;
GO  
CREATE TABLE t1
    (a INT NOT NULL PRIMARY KEY);
CREATE TABLE t2
    (a INT NOT NULL REFERENCES t1(a));
GO
INSERT INTO t1 VALUES (1);
INSERT INTO t1 VALUES (3);
INSERT INTO t1 VALUES (4);
INSERT INTO t1 VALUES (6);
GO
SET XACT_ABORT OFF;
GO
BEGIN TRANSACTION;
INSERT INTO t2 VALUES (1);
INSERT INTO t2 VALUES (2); -- Foreign key error.
INSERT INTO t2 VALUES (3);
COMMIT TRANSACTION;
GO
SET XACT_ABORT ON;
GO
BEGIN TRANSACTION;
INSERT INTO t2 VALUES (4);
INSERT INTO t2 VALUES (5); -- Foreign key error.
INSERT INTO t2 VALUES (6);
COMMIT TRANSACTION;
GO
-- SELECT shows only keys 1 and 3 added.
-- Key 2 insert failed and was rolled back, but
-- XACT_ABORT was OFF and rest of transaction
-- succeeded.
-- Key 5 insert error with XACT_ABORT ON caused
-- all of the second transaction to roll back.
SELECT *
 FROM t2;
GO
```

## <a name="see-also"></a>参照

- [THROW &#40;Transact-SQL&#41;](../../t-sql/language-elements/throw-transact-sql.md)
- [BEGIN TRANSACTION &#40;Transact-SQL&#41;](../../t-sql/language-elements/begin-transaction-transact-sql.md)
- [COMMIT TRANSACTION &#40;Transact-SQL&#41;](../../t-sql/language-elements/commit-transaction-transact-sql.md)
- [ROLLBACK TRANSACTION &#40;Transact-SQL&#41;](../../t-sql/language-elements/rollback-transaction-transact-sql.md)
- [SET ステートメント &#40;Transact-SQL&#41;](../../t-sql/statements/set-statements-transact-sql.md)
- [@@TRANCOUNT &#40;Transact-SQL&#41;](../../t-sql/functions/trancount-transact-sql.md)  
