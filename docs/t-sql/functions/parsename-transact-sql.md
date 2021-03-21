---
description: PARSENAME (Transact-SQL)
title: PARSENAME (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 06/02/2020
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- PARSENAME_TSQL
- PARSENAME
dev_langs:
- TSQL
helpviewer_keywords:
- PARSENAME function
- parsing [SQL Server], PARSENAME function
- names [SQL Server], objects
- objects [SQL Server], names
- part of object names [SQL Server]
ms.assetid: abf34f99-9ee9-460b-85b2-930ca5c4b5ae
author: julieMSFT
ms.author: jrasnick
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 5dc1bfa0b5fa3c2c6990ef4d29976d2434e690db
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104752982"
---
# <a name="parsename-transact-sql"></a>PARSENAME (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  オブジェクト名の指定した部分を返します。 取得できるオブジェクトの部分は、オブジェクト名、スキーマ名、データベース名、およびサーバー名です。 
  
> [!NOTE]  
>  PARSENAME 関数では、指定した名前のオブジェクトが存在するかどうかは示されず、 PARSENAME は、指定したオブジェクト名の指定した部分だけを返します。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql 
PARSENAME ('object_name' , object_piece )
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数

*'object_name'* は、取得対象となるオブジェクトの名前を保持するパラメーターです。 このパラメーターは、必要に応じて修飾されたオブジェクト名です。 オブジェクト名のすべての部分が修飾される場合、この名前には、サーバー名、データベース名、スキーマ名、オブジェクト名の 4 つの部分を指定可能です。  'object_name' 文字列の各部分は、nvarchar(128) または 256 バイトに相当する *sysname* 型です。 文字列のいずれかの部分が 256 バイトを超える場合、有効な sysname ではないため、PARSENAME はその部分に対して NULL を返します。
  
*object_piece*  
返すオブジェクトの部分を指定します。 *object_piece* のデータ型は **int**, 、これらの値を持つことができます。  
    1 = オブジェクト名  
    2 = スキーマ名  
    3 = データベース名  
    4 = サーバー名  
  
## <a name="return-type"></a>戻り値の型

 **sysname**
  
## <a name="remarks"></a>解説

 次のいずれかの条件に該当する場合、PARSENAME は NULL を返します。  
  
-   いずれか *object_name* または *object_piece* は NULL です。  
  
-   構文エラーが発生した。  
  
 要求したオブジェクトの部分の長さが 0 で、有効な [!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 識別子ではない。 長さ 0 のオブジェクト名は完全修飾名を無効とします。  
  
## <a name="examples"></a>例

 次の例では使用 `PARSENAME` 情報を返す、 `Person` テーブルに、 `AdventureWorks2012` データベース。  
  
```sql  
-- Uses AdventureWorks  
  
SELECT PARSENAME('AdventureWorksPDW2012.dbo.DimCustomer', 1) AS 'Object Name';  
SELECT PARSENAME('AdventureWorksPDW2012.dbo.DimCustomer', 2) AS 'Schema Name';  
SELECT PARSENAME('AdventureWorksPDW2012.dbo.DimCustomer', 3) AS 'Database Name';  
SELECT PARSENAME('AdventureWorksPDW2012.dbo.DimCustomer', 4) AS 'Server Name';  
GO  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
```
Object Name
------------------------------
DimCustomer

(1 row(s) affected)

Schema Name
------------------------------
dbo

(1 row(s) affected)

Database Name
------------------------------
AdventureWorksPDW2012

(1 row(s) affected)

Server Name
------------------------------
(null)

(1 row(s) affected)
```
  
## <a name="see-also"></a>参照

- [QUOTENAME &#40;Transact-SQL&#41;](../../t-sql/functions/quotename-transact-sql.md)  
- [ALTER TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-table-transact-sql.md)   
- [CREATE TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/create-table-transact-sql.md)   
- [システム関数 &#40;Transact-SQL&#41;](../../relational-databases/system-functions/system-functions-category-transact-sql.md)  
  
  

