---
description: SET LOCK_TIMEOUT (Transact-SQL)
title: SET LOCK_TIMEOUT (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 09/11/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- LOCK_TIMEOUT_TSQL
- SET_LOCK_TIMEOUT_TSQL
- SET LOCK_TIMEOUT
- LOCK_TIMEOUT
dev_langs:
- TSQL
helpviewer_keywords:
- timeout options [SQL Server], locks
- releasing locks
- LOCK_TIMEOUT option
- SET LOCK_TIMEOUT statement
- locking [SQL Server], time-outs
- wait time for lock releases [SQL Server]
ms.assetid: dd0c389e-956d-435e-bf71-e16624a0a215
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 2ed18868e9b5509e4e8d47e6bf5b80d55bc4bf62
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104754292"
---
# <a name="set-lock_timeout-transact-sql"></a>SET LOCK_TIMEOUT (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  ロックが解除されるまでのステートメントの待ち時間をミリ秒単位で指定します。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql
SET LOCK_TIMEOUT timeout_period  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
 *timeout_period*  
 [!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] がロック エラーを返すまでの経過時間をミリ秒単位で指定します。 値が -1 (既定値) の場合は、タイムアウトはなく、無期限に待機します。  
  
 ロックの待ち時間がタイムアウト値を超えると、エラーが返されます。 値が 0 の場合は、待ち時間はなく、ロックがかかるとすぐにメッセージが返されます。  
  
## <a name="remarks"></a>注釈  
 この設定では、接続の開始時に -1 が割り当てられます。 この値が変更されると、その接続の残りの期間については、新しい設定が適用されます。  
  
 SET LOCK_TIMEOUT は、解析時ではなく実行時に設定されます。  
  
 SET オプションの代わりに READPAST ロック ヒントを使用できます。  
  
 CREATE DATABASE、ALTER DATABASE、および DROP DATABASE ステートメントでは、SET LOCK_TIMEOUT の設定は無視されます。  
  
## <a name="permissions"></a>アクセス許可  
 ロール **public** のメンバーシップが必要です。  
  
## <a name="examples"></a>例  
  
### <a name="a-set-the-lock-timeout-to-1800-milliseconds"></a>A: ロック タイムアウトを 1,800 ミリ秒に設定する  
 次の例では、ロック タイムアウトの待ち時間を `1800` ミリ秒に設定します。  
  
```sql  
SET LOCK_TIMEOUT 1800;  
GO  
```  
  
## <a name="examples-sssdwfull-and-sspdw"></a>例: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)]、[!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="b-set-the-lock-timeout-to-wait-forever-for-a-lock-to-be-released"></a>B. ロックが解放されるまで無限に待機するようにロック タイムアウトを設定する。  
 次の例では、ロック タイムアウトを無期限に待機して期限切れにならないように設定します。 これは、各接続の開始時に既に設定されている既定の動作です。  
  
```sql  
SET LOCK_TIMEOUT -1;  
```  
  
 次の例では、ロック タイムアウトの待ち時間を `1800` ミリ秒に設定します。 このリリースの [!INCLUDE[ssDW](../../includes/ssdw-md.md)] は、ステートメントを正常に解析しますが、1800 という値は無視して既定の動作を使い続けます。  
  
```sql  
SET LOCK_TIMEOUT 1800;  
```  
  
## <a name="see-also"></a>参照  
 [@@LOCK_TIMEOUT &#40;Transact-SQL&#41;](../../t-sql/functions/lock-timeout-transact-sql.md)   
 [SET ステートメント &#40;Transact-SQL&#41;](../../t-sql/statements/set-statements-transact-sql.md)  
  
  

