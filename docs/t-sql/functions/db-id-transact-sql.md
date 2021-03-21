---
description: DB_ID (Transact-SQL)
title: DB_ID (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 08/13/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- DB_ID_TSQL
- DB_ID
dev_langs:
- TSQL
helpviewer_keywords:
- viewing database ID numbers
- IDs [SQL Server], databases
- database IDs [SQL Server]
- identification numbers [SQL Server], databases
- displaying database ID numbers
- DB_ID function
ms.assetid: 7b3aef89-a6fd-4144-b468-bf87ebf381b8
author: VanMSFT
ms.author: vanto
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 77ee79ff9c1090872030391a2b029f2411b0cdac
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104752082"
---
# <a name="db_id-transact-sql"></a>DB_ID (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

この関数は、指定されたデータベースのデータベース識別 (ID) 番号を返します。
  
![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)
  
## <a name="syntax"></a>構文  
  
```syntaxsql
DB_ID ( [ 'database_name' ] )   
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
'*database_name*'  
データベース ID 番号 `DB_ID` を持つデータベースの名前が返されます。 `DB_ID` の呼び出しで *database_name* が省略された場合、`DB_ID` は現在のデータベースの ID を返します。
  
## <a name="return-types"></a>戻り値の型
**int**

## <a name="remarks"></a>解説
`DB_ID` は、Azure SQL Database 内の現在のデータベースのデータベース ID を取得するためにのみ使用できます。 指定したデータベース名が現在のデータベースではない場合は、NULL が返されます。

> [!NOTE]
> Azure SQL Database と共に使用する場合、`DB_ID` では、**sys.databases** からの `database_id` のクエリと同じ結果が返されないことがあります。 `DB_ID` の呼び出し元で結果が他の **sys** ビューと比較されている場合は、代わりに **sys.databases** のクエリを実行する必要があります。
  
## <a name="permissions"></a>アクセス許可  
`DB_ID` の呼び出し元が、**マスター** 以外または **tempdb** 以外の特定データベースを所有していない場合は、対応する `DB_ID` 行を確認するために、少なくとも、サーバー レベルの `ALTER ANY DATABASE` または `VIEW ANY DATABASE` 権限が必要です。 **マスター** データベースの場合、`DB_ID` には少なくとも `CREATE DATABASE` 権限が必要です。 呼び出し元が接続するデータベースは常に、**sys.databases** 内で確認できます。
  
> [!IMPORTANT]  
>  既定では、public ロールは、すべてのログインにデータベース情報の表示を許可する `VIEW ANY DATABASE` 権限を持っています。 ログインでデータベースが検出されるのを阻止するには、public から `VIEW ANY DATABASE` を `REVOKE` するか、または、個別のログインに対する `VIEW ANY DATABASE` を `DENY` します。  
  
## <a name="examples"></a>例  
  
### <a name="a-returning-the-database-id-of-the-current-database"></a>A. 現在のデータベースのデータベース ID を返す  
この例では、現在のデータベースのデータベース ID を返します。
  
```sql
SELECT DB_ID() AS [Database ID];  
GO  
```  
  
### <a name="b-returning-the-database-id-of-a-specified-database"></a>B. 指定したデータベースのデータベース ID を返す  
この例では、[!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)] データベースのデータベース ID を返します。
  
```sql
SELECT DB_ID(N'AdventureWorks2008R2') AS [Database ID];  
GO  
```  
  
### <a name="c-using-db_id-to-specify-the-value-of-a-system-function-parameter"></a>C. DB_ID を使用してシステム関数パラメーターの値を指定する  
この例では、`DB_ID` を使用して、[!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)] データベースのデータベース ID をシステム関数 `sys.dm_db_index_operational_stats` で返します。 この関数はデータベース ID を最初のパラメーターとしてとります。
  
```sql
DECLARE @db_id INT;  
DECLARE @object_id INT;  
SET @db_id = DB_ID(N'AdventureWorks2012');  
SET @object_id = OBJECT_ID(N'AdventureWorks2012.Person.Address');  
IF @db_id IS NULL   
  BEGIN;  
    PRINT N'Invalid database';  
  END;  
ELSE IF @object_id IS NULL  
  BEGIN;  
    PRINT N'Invalid object';  
  END;  
ELSE  
  BEGIN;  
    SELECT * FROM sys.dm_db_index_operational_stats(@db_id, @object_id, NULL, NULL);  
  END;  
GO  
```  
  
## <a name="examples-sssdwfull-and-sspdw"></a>例: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)]、[!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="d-return-the-id-of-the-current-database"></a>D. 現在のデータベースの ID を返す  
この例では、現在のデータベースのデータベース ID を返します。
  
```sql
SELECT DB_ID();  
```  
  
### <a name="e-return-the-id-of-a-named-database"></a>E. 指定したデータベースの ID を返す  
この例では、AdventureWorksDW2012 データベースのデータベース ID を返します。
  
```sql
SELECT DB_ID('AdventureWorksPDW2012');  
```  
  
## <a name="see-also"></a>関連項目
[DB_NAME &#40;Transact-SQL&#41;](../../t-sql/functions/db-name-transact-sql.md)  
[メタデータ関数 &#40;Transact-SQL&#41;](../../t-sql/functions/metadata-functions-transact-sql.md)  
[sys.databases &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md)  
[sys.dm_db_index_operational_stats &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-index-operational-stats-transact-sql.md)
  
  

