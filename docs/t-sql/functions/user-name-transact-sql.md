---
description: USER_NAME (Transact-SQL)
title: USER_NAME (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/06/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- USER_NAME
- USER_NAME_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- usernames [SQL Server]
- IDs [SQL Server], databases
- USER_NAME function
- users [SQL Server], database username
- names [SQL Server], database users
- identification numbers [SQL Server], databases
- database usernames [SQL Server]
ms.assetid: ab32d644-4228-449a-9ef0-5a975c305775
author: VanMSFT
ms.author: vanto
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 1b794ed88f9375a0b7f018db72c43a0406f7651c
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104756402"
---
# <a name="user_name-transact-sql"></a>USER_NAME (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  指定した識別番号から、データベース ユーザー名を返します。  
  
 ![記事リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "記事リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql  
USER_NAME ( [ id ] )  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
 *id*  
 データベース ユーザーに関連付けられている識別番号を指定します。 *id* は **int** です。かっこが必要です。  
  
## <a name="return-types"></a>戻り値の型  
 **nvarchar(128)**  
  
## <a name="remarks"></a>注釈  
 *id* を省略した場合は、現在のコンテキストの現在のユーザーであると見なされます。 パラメーターに "NULL" という語が含まれていると、NULL が返されます。 EXECUTE AS ステートメントの実行後に *id* を指定せずに USER_NAME を呼び出した場合、USER_NAME では権限を借用したユーザーの名前が返されます。 Windows プリンシパルがグループのメンバーシップを使ってデータベースにアクセスした場合、USER_NAME はグループではなく Windows プリンシパルの名前を返します。  
 
> [!NOTE]
> USER_NAME 関数は Azure SQL Database でサポートされていますが、USER_NAME と一緒に *Execute as* を使用することは Azure SQL Database ではサポートされていません。 
  
## <a name="examples"></a>例  
  
### <a name="a-using-user_name"></a>A. USER_NAME を使用する  
 次の例では、ユーザー ID `13` のユーザー名を返します。  
  
```sql  
SELECT USER_NAME(13);  
GO  
```  
  
### <a name="b-using-user_name-without-an-id"></a>B. ID を指定せずに USER_NAME を使用する  
 次の例では、ID を指定せずに、現在のユーザーの名前を検索します。  
  
```sql  
SELECT USER_NAME();  
GO  
```  
  
 次に、ユーザーが sysadmin 固定サーバー ロールのメンバーである場合の結果セットを示します。  
  
 ```
------------------------------  
dbo  
  
(1 row(s) affected)
```  
  
### <a name="c-using-user_name-in-the-where-clause"></a>C. WHERE 句で USER_NAME を使用する  
 次の例では、`sysusers` の行を検索します。検索される名前は、ユーザー識別番号 `USER_NAME` に対してシステム関数 `1` を適用した結果と同じになります。  
  
```sql  
SELECT name FROM sysusers WHERE name = USER_NAME(1);  
GO  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 ```
name  
------------------------------  
dbo  
  
(1 row(s) affected)
```  
  
### <a name="d-calling-user_name-during-impersonation-with-execute-as"></a>D. EXECUTE AS での権限借用中に USER_NAME を呼び出す  
 次の例では、権限借用中の `USER_NAME` の動作を示します。  
  
```sql  
SELECT USER_NAME();  
GO  
EXECUTE AS USER = 'Zelig';  
GO  
SELECT USER_NAME();  
GO  
REVERT;  
GO  
SELECT USER_NAME();  
GO  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 ```
DBO  
Zelig  
DBO
```  
  
## <a name="examples-sssdwfull-and-sspdw"></a>例: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)]、[!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="e-using-user_name-without-an-id"></a>E. ID を指定せずに USER_NAME を使用する  
 次の例では、ID を指定せずに、現在のユーザーの名前を検索します。  
  
```sql  
SELECT USER_NAME();  
```  
  
 これは現在のログイン ユーザーの結果セットです。  
  
```  
------------------------------   
User7                              
```  
  
### <a name="f-using-user_name-in-the-where-clause"></a>F. WHERE 句で USER_NAME を使用する  
 次の例では、`sysusers` の行を検索します。検索される名前は、ユーザー識別番号 `USER_NAME` に対してシステム関数 `1` を適用した結果と同じになります。  
  
```sql  
SELECT name FROM sysusers WHERE name = USER_NAME(1);  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
name                             
------------------------------   
User7                              
```  
  
## <a name="see-also"></a>参照  
 [ALTER TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-table-transact-sql.md)   
 [CREATE TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/create-table-transact-sql.md)   
 [CURRENT_TIMESTAMP &#40;Transact-SQL&#41;](../../t-sql/functions/current-timestamp-transact-sql.md)   
 [CURRENT_USER &#40;Transact-SQL&#41;](../../t-sql/functions/current-user-transact-sql.md)   
 [SESSION_USER &#40;Transact-SQL&#41;](../../t-sql/functions/session-user-transact-sql.md)   
 [システム関数 &#40;Transact-SQL&#41;](../../relational-databases/system-functions/system-functions-category-transact-sql.md)   
 [SYSTEM_USER &#40;Transact-SQL&#41;](../../t-sql/functions/system-user-transact-sql.md)  
  
