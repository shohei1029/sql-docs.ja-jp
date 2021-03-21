---
description: ISNUMERIC (Transact-SQL)
title: ISNUMERIC (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/13/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- ISNUMERIC
- ISNUMERIC_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- expressions [SQL Server], valid numeric type
- numeric data
- ISNUMERIC function
- verifying valid numeric type
- valid numeric type [SQL Server]
- checking valid numeric type
ms.assetid: 7aa816de-529a-4f6c-a99f-4d5a9ef599eb
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 8ac57fb4dcb2e76797a8fb705ba97064cfbbd0e7
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104746882"
---
# <a name="isnumeric-transact-sql"></a>ISNUMERIC (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  式が数値型として有効かどうかを調べます。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql 
ISNUMERIC ( expression )  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
 *式 (expression)*  
 評価対象となる[式](../../t-sql/language-elements/expressions-transact-sql.md)を指定します。  
  
## <a name="return-types"></a>戻り値の型  
 **int**  
  
## <a name="remarks"></a>解説  
 ISNUMERIC は、入力式が有効な数値データ型であると判断される場合に 1 を返します。それ以外の場合は 0 を返します。 有効な[数値データ型](../../t-sql/data-types/numeric-types.md)は次のとおりです。  

| 領域 | 数値データ型 |
|-|-|
| [真数](../../t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql.md) | **bigint**、**int**、**smallint**、**tinyint**、**bit** |
| [固定有効桁数](../../t-sql/data-types/decimal-and-numeric-transact-sql.md) | **decimal**、**numeric** |
| [概数](../../t-sql/data-types/float-and-real-transact-sql.md) | **float**、**real** |
| [通貨値](../../t-sql/data-types/money-and-smallmoney-transact-sql.md) | **money**、 **smallmoney** |

> [!NOTE]  
> ISNUMERIC は、数字以外の一部の文字に対して 1 を返します。たとえばプラス (+)、マイナス (-)、ドル記号 ($) などの通貨記号がこれに該当します。 通貨記号の完全な一覧を参照してください[ money および smallmoney &#40;Transact-SQL&#41;](../../t-sql/data-types/money-and-smallmoney-transact-sql.md).  
  
## <a name="examples"></a>例  
 この例では、`ISNUMERIC` を使用して数値型でないすべての郵便番号を返しています。  
  
```sql
USE AdventureWorks2012;  
GO  
SELECT City, PostalCode  
FROM Person.Address   
WHERE ISNUMERIC(PostalCode) <> 1;  
GO  
```  
  
## <a name="examples-sssdwfull-and-sspdw"></a>例: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)]、[!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
 この例では、`ISNUMERIC` を使用して数値型でないすべての郵便番号を返しています。  
  
```sql
USE master;  
GO  
SELECT name, ISNUMERIC(name) AS IsNameANumber, database_id, ISNUMERIC(database_id) AS IsIdANumber   
FROM sys.databases;  
GO  
```  
  
## <a name="see-also"></a>関連項目

- [式 &#40;Transact-SQL&#41;](../../t-sql/language-elements/expressions-transact-sql.md)
- [システム関数 &#40;Transact-SQL&#41;](../../relational-databases/system-functions/system-functions-category-transact-sql.md)
- [データ型 &#40;Transact-SQL&#41;](../../t-sql/data-types/data-types-transact-sql.md)
