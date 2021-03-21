---
description: SYSTEM_USER (Transact-SQL)
title: SYSTEM_USER (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: synapse-analytics, pdw, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- SYSTEM_USER_TSQL
- SYSTEM_USER
dev_langs:
- TSQL
helpviewer_keywords:
- current user names
- system-supplied user names [SQL Server]
- users [SQL Server], logins
- logins [SQL Server], identification name
- current system user names
- SYSTEM_USER function
- inserting system user name into table
- system usernames [SQL Server]
- users [SQL Server], names
ms.assetid: 565984cd-60c6-4df7-83ea-2349b838ccb2
author: julieMSFT
ms.author: jrasnick
monikerRange: '>=aps-pdw-2016||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 8a718ab0e86ed0fa8bafdca88968760a3c103cca
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104754502"
---
# <a name="system_user-transact-sql"></a>SYSTEM_USER (Transact-SQL)
[!INCLUDE [sql-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdbmi-asa-pdw.md)]

  既定値が指定されていない場合、現在のユーザー用のシステム定義の値をテーブルに挿入することができます。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql
SYSTEM_USER  
```  

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="return-types"></a>戻り値の型  
 **nvarchar(128)**  
  
## <a name="remarks"></a>注釈  
 SYSTEM_USER 関数は、CREATE TABLE および ALTER TABLE ステートメント内で DEFAULT 制約と共に使用できます。 標準的な関数としても使用できます。  
  
 ユーザー名とログイン名が異なる場合、SYSTEM_USER ではログイン名が返されます。  
  
 現在のユーザーが Windows 認証によって [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] にログインされた場合、SYSTEM_USER から、次の形式で Windows ログインの識別名が返されます。*DOMAIN*\\*user_login_name*。 現在のユーザーが SQL Server 認証によって [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] にログインした場合は、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログインの識別名が返されます。たとえば、`WillisJo` としてログインしたユーザーの場合は `WillisJo` が返されます。  
  
 SYSTEM_USER では、現在の実行コンテキストの名前が返されます。 EXECUTE AS ステートメントを使用してコンテキストを切り替えた場合、SYSTEM_USER では権限を借用したコンテキストの名前が返されます。  

 SYSTEM_USER 権限で実行することはできません。

## <a name="examples"></a>例  
  
### <a name="a-using-system_user-to-return-the-current-system-user-name"></a>A.  SYSTEM_USER を使用して現在のシステム ユーザー名を返す  
 次の例では、`char` 変数を宣言し、`SYSTEM_USER` の現在の値をこの変数に格納した後、変数に格納されている値を出力します。  
  
```sql
DECLARE @sys_usr CHAR(30);  
SET @sys_usr = SYSTEM_USER;  
SELECT 'The current system user is: '+ @sys_usr;  
GO  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 ```
----------------------------------------------------------
The current system user is: WillisJo

(1 row(s) affected)
 ```  
  
### <a name="b-using-system_user-with-default-constraints"></a>B.  SYSTEM_USER を DEFAULT 制約と共に使用する  
 次の例では、テーブルを作成し、`SYSTEM_USER` 列の `DEFAULT` 制約として `SRep_tracking_user` を使用します。  
  
```sql
USE AdventureWorks2012;  
GO  
CREATE TABLE Sales.Sales_Tracking  
(  
    Territory_id INT IDENTITY(2000, 1) NOT NULL,  
    Rep_id INT NOT NULL,  
    Last_sale DATETIME NOT NULL DEFAULT GETDATE(),  
    SRep_tracking_user VARCHAR(30) NOT NULL DEFAULT SYSTEM_USER  
);  
GO  
INSERT Sales.Sales_Tracking (Rep_id)  
VALUES (151);  
INSERT Sales.Sales_Tracking (Rep_id, Last_sale)  
VALUES (293, '19980515');  
INSERT Sales.Sales_Tracking (Rep_id, Last_sale)  
VALUES (27882, '19980620');  
INSERT Sales.Sales_Tracking (Rep_id)  
VALUES (21392);  
INSERT Sales.Sales_Tracking (Rep_id, Last_sale)  
VALUES (24283, '19981130');  
GO  
```  
  
 次のクエリでは、`Sales_Tracking` テーブルのすべての情報を選択します。  
  
```sql
SELECT * FROM Sales_Tracking ORDER BY Rep_id;  
GO  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 ```
Territory_id Rep_id Last_sale            SRep_tracking_user
-----------  ------ -------------------- ------------------
2000         151    Mar 4 1998 10:36AM   ArvinDak
2001         293    May 15 1998 12:00AM  ArvinDak
2003         21392  Mar 4 1998 10:36AM   ArvinDak
2004         24283  Nov 3 1998 12:00AM   ArvinDak
2002         27882  Jun 20 1998 12:00AM  ArvinDak
  
(5 row(s) affected)
 ```  
  
## <a name="examples-sssdwfull-and-sspdw"></a>例: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)]、[!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="c-using-system_user-to-return-the-current-system-user-name"></a>C: SYSTEM_USER を使用して現在のシステム ユーザー名を返す  
 次の例では、`SYSTEM_USER` の現在の値を返します。  
  
```sql
SELECT SYSTEM_USER;  
```  
  
## <a name="see-also"></a>参照  
 [ALTER TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-table-transact-sql.md)   
 [CREATE TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/create-table-transact-sql.md)   
 [CURRENT_TIMESTAMP &#40;Transact-SQL&#41;](../../t-sql/functions/current-timestamp-transact-sql.md)   
 [CURRENT_USER &#40;Transact-SQL&#41;](../../t-sql/functions/current-user-transact-sql.md)   
 [SESSION_USER &#40;Transact-SQL&#41;](../../t-sql/functions/session-user-transact-sql.md)   
 [システム関数 &#40;Transact-SQL&#41;](../../relational-databases/system-functions/system-functions-category-transact-sql.md)   
 [USER &#40;Transact-SQL&#41;](../../t-sql/functions/user-transact-sql.md)  
  
  

