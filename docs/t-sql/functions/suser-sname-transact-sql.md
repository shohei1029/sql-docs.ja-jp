---
description: SUSER_SNAME (Transact-SQL)
title: SUSER_SNAME (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 07/29/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- SUSER_SNAME_TSQL
- SUSER_SNAME
dev_langs:
- TSQL
helpviewer_keywords:
- security identification names [SQL Server]
- logins [SQL Server], users
- SIDs [SQL Server]
- SUSER_SNAME function
- users [SQL Server], logins
- logins [SQL Server], names
- IDs [SQL Server], logins
- identification numbers [SQL Server], logins
- names [SQL Server], logins
ms.assetid: 11ec7d86-d429-4004-a436-da25df9f8761
author: julieMSFT
ms.author: jrasnick
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 7106bfe0e6b5c7de06367493920a622ef2633be5
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104736992"
---
# <a name="suser_sname-transact-sql"></a>SUSER_SNAME (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  セキュリティ ID 番号 (SID) に関連付けられているログイン名を返します。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql
SUSER_SNAME ( [ server_user_sid ] )   
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
 *server_user_sid*  
**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降
  
 オプションのログイン セキュリティ ID 番号を指定します。 *server_user_sid* は **varbinary(85)** です。 *server_user_sid* には、任意の [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログイン名または [!INCLUDE[msCoName](../../includes/msconame-md.md)] Windows ユーザーやグループのセキュリティ ID 番号を指定できます。 *server_user_sid* の指定を省略すると、現在のユーザーについての情報が返されます。 パラメーターに "NULL" という語が含まれていると、NULL が返されます。  
  
## <a name="return-types"></a>戻り値の型  
 **nvarchar(128)**  
  
## <a name="remarks"></a>注釈  
 SUSER_SNAME は、ALTER TABLE または CREATE TABLE の中で、DEFAULT 制約として使用できます。 SUSER_SNAME は、選択リストの中、WHERE 句の中、また、式を使える所ならどこにでも使用できます。 SUSER_SNAME の後には、パラメーターを指定しない場合も含め、常にかっこが必要です。  
  
 SUSER_SNAME を引数なしで呼び出すと、現在のセキュリティ コンテキストの名前が返されます。 EXECUTE AS を使用してコンテキストを切り替えたバッチ内で SUSER_SNAME を引数なしで呼び出すと、権限を借用したコンテキストの名前が返されます。 権限を借用したコンテキストから ORIGINAL_LOGIN を呼び出すと、元のコンテキストの名前が返されます。  
  
## <a name="sssdsfull-remarks"></a>[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 解説  
 SUSER_NAME では、常に現在のセキュリティ コンテキストのログイン名が返されます。  
  
 SUSER_SNAME ステートメントでは、EXECUTE AS で借用したセキュリティ コンテキストを使用した実行がサポートされません。  
  
## <a name="examples"></a>例  
  
### <a name="a-using-suser_sname"></a>A. SUSER_SNAME を使用する  
 次の例では、現在のセキュリティ コンテキストのログイン名が返されます。  
  
```sql
SELECT SUSER_SNAME();  
GO  
```  
  
### <a name="b-using-suser_sname-with-a-windows-user-security-id"></a>B. SUSER_SNAME を Windows ユーザーのセキュリティ ID と共に使用する  
 次の例では、Windows セキュリティ ID 番号に関連付けられているログイン名が返されます。  
  
**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降
  
```sql
SELECT SUSER_SNAME(0x010500000000000515000000a065cf7e784b9b5fe77c87705a2e0000);  
GO  
```  
  
### <a name="c-using-suser_sname-as-a-default-constraint"></a>C. SUSER_SNAME を DEFAULT 制約として使用する  
 次の例では、`SUSER_SNAME` ステートメントで `DEFAULT` を `CREATE TABLE` 制約として使用しています。  
  
```sql
USE AdventureWorks2012;  
GO  
CREATE TABLE sname_example  
(  
login_sname sysname DEFAULT SUSER_SNAME(),  
employee_id uniqueidentifier DEFAULT NEWID(),  
login_date  datetime DEFAULT GETDATE()  
);   
GO  
INSERT sname_example DEFAULT VALUES;  
GO  
```  
  
### <a name="d-calling-suser_sname-in-combination-with-execute-as"></a>D. SUSER_SNAME を EXECUTE AS と組み合わせて呼び出す  
 この例は、権限を借用したコンテキストから呼び出した場合の SUSER_SNAME の動作を示しています。  
  
**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降
  
```sql
SELECT SUSER_SNAME();  
GO  
EXECUTE AS LOGIN = 'WanidaBenShoof';  
SELECT SUSER_SNAME();  
REVERT;  
GO  
SELECT SUSER_SNAME();  
GO 
```  
  
 以下に結果を示します。  
  
 ```
sa  
WanidaBenShoof  
sa
```  
  
## <a name="examples-sssdwfull-and-sspdw"></a>例: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)]、[!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="e-using-suser_sname"></a>E. SUSER_SNAME を使用する  
 次の例では、セキュリティ ID 番号が `0x01` のログイン名を返します。  
  
```sql
SELECT SUSER_SNAME(0x01);  
GO  
```  
  
### <a name="f-returning-the-current-login"></a>F. 現在のログインを返す  
 次の例では、現在のログインのログイン名を返します。  
  
```sql
SELECT SUSER_SNAME() AS CurrentLogin;  
GO  
```  
  
## <a name="see-also"></a>関連項目  
 [SUSER_SID &#40;Transact-SQL&#41;](../../t-sql/functions/suser-sid-transact-sql.md)   
 [プリンシパル &#40;データベース エンジン&#41;](../../relational-databases/security/authentication-access/principals-database-engine.md)   
 [sys.server_principals &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-server-principals-transact-sql.md)   
 [EXECUTE AS &#40;Transact-SQL&#41;](../../t-sql/statements/execute-as-transact-sql.md)  
  
  

