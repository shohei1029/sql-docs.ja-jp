---
description: HAS_DBACCESS (Transact-SQL)
title: HAS_DBACCESS (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 10/23/2017
ms.prod: sql
ms.prod_service: synapse-analytics, pdw, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- HAS_DBACCESS_TSQL
- HAS_DBACCESS
dev_langs:
- TSQL
helpviewer_keywords:
- permissions [SQL Server], verifying
- permissions [SQL Server], user access status
- HAS_DBACCESS
- checking permission status
- verifying permission status
- users [SQL Server], access rights status
- testing permissions
- status information [SQL Server], user access
ms.assetid: 99b43a72-0722-4a7b-a493-bdee1c74c7b9
author: VanMSFT
ms.author: vanto
monikerRange: '>=aps-pdw-2016||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: a16d46924d2d41cab775e45d24f72fd117107dd6
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104747012"
---
# <a name="has_dbaccess-transact-sql"></a>HAS_DBACCESS (Transact-SQL)
[!INCLUDE [sql-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdbmi-asa-pdw.md)]

  ユーザーが、指定したデータベースにアクセスできるかどうかに関する情報を返します。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql  
HAS_DBACCESS ( 'database_name' )  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
 '*database_name*'  
 ユーザーのアクセス情報が必要なデータベースの名前を指定します。 *database_name* は **sysname** です。  
  
## <a name="return-types"></a>戻り値の型  
 **int**  
  
## <a name="remarks"></a>解説  
 HAS_DBACCESS は、ユーザーがデータベースにアクセスできる場合は 1、ユーザーがデータベースにアクセスできない場合は 0、およびデータベース名が有効でない場合は NULL を返します。  
  
 データベースがオフラインの場合または異常がある場合は、0 が返されます。  
  
 HAS_DBACCESS は、データベースがシングルユーザー モードで、データベースが別のユーザーによって使用中の場合、0 を返します。  
  
## <a name="permissions"></a>アクセス許可  
 public ロールのメンバーシップが必要です。  
  
## <a name="examples"></a>例  
 次の例では、現在のユーザーが `AdventureWorks2012` データベースにアクセスできるかどうかをテストします。  
  
```sql  
SELECT HAS_DBACCESS('AdventureWorks2012');  
GO  
```  
  
## <a name="examples-sssdwfull-and-sspdw"></a>例: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)]、[!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
 次の例では、現在のユーザーが `AdventureWorksPDW2012` データベースにアクセスできるかどうかをテストします。  
  
```sql  
SELECT HAS_DBACCESS('AdventureWorksPDW2012');  
GO  
```  
  
## <a name="see-also"></a>参照  
 [IS_MEMBER &#40;Transact-SQL&#41;](../../t-sql/functions/is-member-transact-sql.md)   
 [IS_SRVROLEMEMBER &#40;Transact-SQL&#41;](../../t-sql/functions/is-srvrolemember-transact-sql.md)  
  
  

