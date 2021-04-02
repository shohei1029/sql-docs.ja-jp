---
description: APPLOCK_MODE (Transact-SQL)
title: APPLOCK_MODE (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 07/24/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- APPLOCK_MODE_TSQL
- APPLOCK_MODE
dev_langs:
- TSQL
helpviewer_keywords:
- locking [SQL Server], applications
- applications [SQL Server], locks
- sessions [SQL Server], application locks
- APPLOCK_MODE function
ms.assetid: e43d4917-77f1-45cc-b231-68ba7fee3385
author: cawrites
ms.author: chadam
ms.openlocfilehash: 6103d0d5a80f147eb59f3983a178aaeae9591a1f
ms.sourcegitcommit: 295b9dfc758471ef7d238a2b0f92f93e34acbb1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106054895"
---
# <a name="applock_mode-transact-sql"></a>APPLOCK_MODE (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

この関数は、特定のアプリケーション リソースで、ロック所有者によって保持されているロック モードを返します。 APPLOCK_MODE はアプリケーション ロック関数であり、現在のデータベース上で動作します。 データベースはアプリケーション ロックのスコープです。
  
![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)
  
## <a name="syntax"></a>構文  
  
```syntaxsql
APPLOCK_MODE( 'database_principal' , 'resource_name' , 'lock_owner' )  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
'*database_principal*'  
データベース内のオブジェクトに対するアクセス許可を許可されるユーザー、ロール、またはアプリケーション ロールです。 関数を正常に呼び出すには、関数の呼び出し元が *database_principal*、dbo、または、db_owner 固定データベース ロールのメンバーである必要があります。
  
'*resource_name*'  
クライアント アプリケーションによって指定されたロック リソース名を指定します。 アプリケーション側では、リソース名が一意になるよう管理されている必要があります。 指定した名前は、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ロック マネージャーが内部的に保存できる値に内部的にハッシュされます。 *resource_name* は **nvarchar (255**) であり、既定値はありません。 *resource_name* はバイナリ比較し、現在のデータベースの照合順序の設定に関係なく大文字小文字を区別します。
  
'*lock_owner*'  
ロックの所有者を指定します。これはロックが要求されたときの *lock_owner* 値です。 *lock_owner* は **nvarchar (32)**, 、値には、いずれかを指定して **トランザクション** (既定値) または **セッション** です。
  
## <a name="return-types"></a>戻り値の型
**nvarchar(32)**
  
## <a name="return-value"></a>戻り値
特定のアプリケーション リソースで、ロック所有者によって保持されているロック モードを返します。 ロック モードは次のいずれかの値になります。

:::row:::
   :::column span="":::
      **NoLock**<br>      **アップデート**<br>      **\*SharedIntentExclusive**
   :::column-end:::
   :::column span="":::
      **IntentShared**<br>      **IntentExclusive**<br>      **\*UpdateIntentExclusive**

   :::column-end:::
   :::column span="":::
      **Shared**<br>      **[排他]**
   :::column-end:::
:::row-end:::
  
* このロック モードは他のロック モードの組み合わせであり、sp_getapplock で明示的に取得することはできません。
  
## <a name="function-properties"></a>関数のプロパティ
**非決定的**
  
**インデックス不可**
  
**並列不可**
  
## <a name="examples"></a>例  
セッションが異なる 2 つのユーザー (ユーザー A とユーザー B) の次のシーケンスを実行する [!INCLUDE[tsql](../../includes/tsql-md.md)] ステートメントです。
  
ユーザー A が次のステートメントを実行します。
  
```sql
USE AdventureWorks2012;  
GO  
BEGIN TRAN;  
DECLARE @result INT;  
EXEC @result=sp_getapplock  
    @DbPrincipal='public',  
    @Resource='Form1',  
    @LockMode='Shared',  
    @LockOwner='Transaction';  
SELECT APPLOCK_MODE('public', 'Form1', 'Transaction');  
GO  
```  
  
次に、ユーザー B が次のステートメントを実行します。
  
```sql
Use AdventureWorks2012;  
GO  
BEGIN TRAN;  
SELECT APPLOCK_MODE('public', 'Form1', 'Transaction');  
--Result set: NoLock  
  
SELECT APPLOCK_TEST('public', 'Form1', 'Shared', 'Transaction');  
--Result set: 1 (Lock is grantable.)  
  
SELECT APPLOCK_TEST('public', 'Form1', 'Exclusive', 'Transaction');  
--Result set: 0 (Lock is not grantable.)  
GO  
```  
  
次に、ユーザー A が次のステートメントを実行します。
  
```sql
EXEC sp_releaseapplock @Resource='Form1', @DbPrincipal='public';  
GO  
```  
  
次に、ユーザー B が次のステートメントを実行します。
  
```sql
SELECT APPLOCK_TEST('public', 'Form1', 'Exclusive', 'Transaction');  
--Result set: '1' (The lock is grantable.)  
GO  
```  
  
次に、ユーザー A とユーザー B が次のステートメントを実行します。
  
```sql
COMMIT TRAN;  
GO  
```  
  
## <a name="see-also"></a>関連項目
[APPLOCK_TEST &#40;Transact-SQL&#41;](../../t-sql/functions/applock-test-transact-sql.md)  
[sp_getapplock &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-getapplock-transact-sql.md)  
[sp_releaseapplock &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-releaseapplock-transact-sql.md)
  
  
