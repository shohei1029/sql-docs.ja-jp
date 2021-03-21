---
description: SET DEADLOCK_PRIORITY (Transact-SQL)
title: SET DEADLOCK_PRIORITY (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.prod_service: synapse-analytics, database-engine, pdw, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- SET DEADLOCK_PRIORITY
- DEADLOCK_PRIORITY_TSQL
- SET_DEADLOCK_PRIORITY_TSQL
- DEADLOCK_PRIORITY
dev_langs:
- TSQL
helpviewer_keywords:
- deadlocks [SQL Server], priority settings
- DEADLOCK_PRIORITY option
- locking [SQL Server], deadlocks
- priority deadlock settings [SQL Server]
- SET DEADLOCK_PRIORITY statement
ms.assetid: 810a3a8e-3da3-4bf9-bb15-7b069685a1b6
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 58b66c429257da0667b0da73dc91feb38cc9a136
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104751492"
---
# <a name="set-deadlock_priority-transact-sql"></a>SET DEADLOCK_PRIORITY (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  別のセッションとデッドロックとなる場合、現在のセッションでの処理の継続の相対的な優先度を指定します。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql
  
SET DEADLOCK_PRIORITY { LOW | NORMAL | HIGH | <numeric-priority> | @deadlock_var | @deadlock_intvar }  
  
<numeric-priority> ::= { -10 | -9 | -8 | ... | 0 | ... | 8 | 9 | 10 }  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
 LOW  
 現在のセッションがデッドロックに含まれ、そのデッドロック チェーンに含まれる他のセッションのデッドロックの優先度が NORMAL または HIGH または -5 を超える整数値のいずれかである場合、現在のセッションがデッドロックの対象になることが指定されます。 他のセッションのデッドロックの優先度の設定が -5 未満の整数値である場合、現在のセッションはデッドロックの対象とはなりません。 また、別のセッションのデッドロックの優先度が LOW または -5 と等しい整数値に設定されている場合、現在のセッションがデッドロックの対象になることが指定されます。  
  
 NORMAL  
 デッドロック チェーンに含まれる他のセッションが、デッドロック優先度を HIGH または 0 より大きい整数値に設定している場合、現在のセッションがデッドロックの対象になります。ただし、他のセッションがデッドロックの優先度を LOW または 0 より小さい整数値に設定している場合は、現在のセッションはデッドロックの対象にはなりません。 また、別の他のセッションのデッドロックの優先度が NORMAL または 0 と等しい整数値に設定されている場合、現在のセッションがデッドロックの対象になることが指定されます。 既定の優先度は NORMAL です。  
  
 HIGH  
 デッドロック チェーンに含まれる他のセッションのデッドロックの優先度が 5 よりも大きい整数値に設定されている場合、現在のセッションがデッドロックの対象になることが指定されます。または別のセッションでもデッドロックの優先度が HIGH または 5 と等しい整数値である場合、現在のセッションはデッドロックの対象となります。  
  
 \<numeric-priority>  
 -10 ～ 10 の範囲の整数値です。デッドロックの優先度を 21 段階のレベルで示します。 デッドロック チェーンに含まれる他のセッションが、高いデッドロック優先度値で実行中の場合、現在のセッションはデッドロックの対象になりますが、他のセッションが現在のセッションの値より低いデッドロック優先度値で実行中の場合は、現在のセッションはデッドロックの対象にはなりません。 また、別のセッションの実行のデッドロックの優先度の値が現在のセッションと同じである場合、現在のセッションがデッドロックの対象になることが指定されます。 LOW は -5 に、NORMAL は 0 に、HIGH は 5 にマップされます。  
  
 **@** *deadlock_var*  
 デッドロックの優先度を表す文字変数を指定します。 変数は、'LOW'、'NORMAL' または 'HIGH' の値に設定する必要があります。 変数は文字列全体を十分格納できるサイズであることが必要です。  
  
 **@** *deadlock_intvar*  
 デッドロックの優先度を表す整数変数を指定します。 変数は -10 ～ 10 の範囲の整数値に設定する必要があります。  
  
## <a name="remarks"></a>解説  
 デッドロックは、2 つのセッションが両方とも、一方のセッションによりロックされているリソースへのアクセスを待機しているときに発生します。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスにより、2 つのセッションがデッドロックされていることが検出されると、セッションの 1 つをデッドロックの対象として選択することにより、デッドロックが解決します。 デッドロック対象セッションの現在のトランザクションがロールバックされて、デッドロック エラー メッセージ 1205 がクライアントに返されます。 これによって、そのセッションが保持しているすべてのロックが解放され、他のセッションを続行できるようになります。  
  
 どのセッションがデッドロックの対象となるかは、各セッションのデッドロックの優先度によります。  
  
-   両方のセッションが同じデッドロック優先度の場合、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスは、デッドロック対象としてロールバックするのに負担がかからない方のセッションを選択します。 たとえば、両セッションのデッドロックの優先度が HIGH に設定されている場合、インスタンスはロールバックに最もコストがかからないと予測するセッションを対象に選択します。 コストは、各トランザクションでその時点に書き込まれたログのバイト数を比較することによって決定されます。 (この値はデッドロック グラフの "使用されたログ" として参照できます)。
  
-   セッションのデッドロックの優先度が異なる場合、優先度の最も低いセッションがデッドロックの対象として選択されます。  
  
 SET DEADLOCK_PRIORITY は、解析時ではなく実行時に設定されます。  
  
## <a name="permissions"></a>アクセス許可  
 ロール **public** のメンバーシップが必要です。  
  
## <a name="examples"></a>例  
 次の例では、デッドロック優先度を `LOW` に設定する変数を使用します。  
  
```sql
DECLARE @deadlock_var NCHAR(3);  
SET @deadlock_var = N'LOW';  
  
SET DEADLOCK_PRIORITY @deadlock_var;  
GO  
```  
  
 次の例では、デッドロック優先度を `NORMAL` に設定します。  
  
```sql
SET DEADLOCK_PRIORITY NORMAL;  
GO  
```  
  
## <a name="see-also"></a>参照  
 [@@LOCK_TIMEOUT &#40;Transact-SQL&#41;](../../t-sql/functions/lock-timeout-transact-sql.md)   
 [SET ステートメント &#40;Transact-SQL&#41;](../../t-sql/statements/set-statements-transact-sql.md)   
 [SET LOCK_TIMEOUT &#40;Transact-SQL&#41;](../../t-sql/statements/set-lock-timeout-transact-sql.md)  
  
  
