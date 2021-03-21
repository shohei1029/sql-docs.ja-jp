---
description: SUSER_NAME (Transact-SQL)
title: SUSER_NAME (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 12/12/2020
ms.prod: sql
ms.prod_service: synapse-analytics, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- SUSER_NAME
- SUSER_NAME_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- security identification names [SQL Server]
- logins [SQL Server], users
- identification names for logins [SQL Server]
- users [SQL Server], logins
- SUSER_NAME function
- logins [SQL Server], names
- names [SQL Server], logins
ms.assetid: ae598d9f-9baa-49b8-b1c1-042854206de4
author: VanMSFT
ms.author: vanto
monikerRange: =azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: f9a204175b54b5349a975ade8926844eced687bd
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104755432"
---
# <a name="suser_name-transact-sql"></a>SUSER_NAME (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

ユーザーのログイン識別名を返します。  
  
![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql
SUSER_NAME ( [ server_user_id ] )   
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
_server\_user\_id_  
ユーザーのログイン ID 番号です。 _server\_user\_id_ (省略可能) は **int** です。_server\_user\_id_ には、任意の [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログインまたは [!INCLUDE[msCoName](../../includes/msconame-md.md)] Windows ユーザーのログイン ID 番号、または [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスに接続するためのアクセス許可があるグループのログイン ID 番号を指定できます。 _server\_user\_id_ を指定しないと、現在のユーザーのログイン ID 名が返されます。 パラメーターに "NULL" という語が含まれていると、NULL が返されます。  
  
## <a name="return-types"></a>戻り値の型  
**nvarchar(128)**  
  
## <a name="remarks"></a>注釈  
[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Version 7.0 では、サーバー ユーザー識別番号 (SUID) の代わりにセキュリティ識別番号 (SID) が使用されます。  
  
SUSER_NAME では、**syslogins** システム テーブル内にエントリがあるログインに対してのみログイン名が返されます。  
  
SUSER_NAME は、選択リストの中、WHERE 句の中、また、式を使える所ならどこにでも使用できます。 パラメーターが指定されていない場合でも、SUSER_NAME の後に括弧を使用します。  

> [!NOTE]
> SUSER_NAME 関数は Azure SQL Database でサポートされていますが、SUSER_NAME と一緒に *Execute as* を使用することは Azure SQL Database ではサポートされていません。 
  
## <a name="examples"></a>例  
次の例では、ログイン識別番号 `1` のユーザーのログイン識別名を返します。  
  
```sql
SELECT SUSER_NAME(1);  
```  
  
## <a name="see-also"></a>関連項目  
[SUSER_ID &#40;Transact-SQL&#41;](../../t-sql/functions/suser-id-transact-sql.md)   
[プリンシパル &#40;データベース エンジン&#41;](../../relational-databases/security/authentication-access/principals-database-engine.md)  
  
  
