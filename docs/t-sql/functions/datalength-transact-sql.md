---
description: DATALENGTH (Transact-SQL)
title: DATALENGTH (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 08/20/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- DATALENGTH_TSQL
- DATALENGTH
dev_langs:
- TSQL
helpviewer_keywords:
- number of bytes representing expression
- data types [SQL Server], length
- DATALENGTH function
- expressions [SQL Server], length
- lengths [SQL Server], data
ms.assetid: 00f377f1-cc3e-4eac-be47-b3e3f80267c9
author: pmasl
ms.author: pelopes
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 93e028748976e1b382231c07c2df08ff6efb88d8
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104754662"
---
# <a name="datalength-transact-sql"></a>DATALENGTH (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

この関数では、式を表すために必要なバイト数が返されます。

> [!NOTE]
> 文字列式内の文字数を取得するには、[LEN](../../t-sql/functions/len-transact-sql.md) 関数を使用します。
  
![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)
  
## <a name="syntax"></a>構文  
  
```syntaxsql
DATALENGTH ( expression )   
```  

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
*式 (expression)*  
任意の型の[式](../../t-sql/language-elements/expressions-transact-sql.md)。
  
## <a name="return-types"></a>戻り値の型
*expression* が **varchar(max)**、**nvarchar(max)**、**varbinary(max)** データ型の場合は **bigint**、それ以外の場合は **int**。
  
## <a name="remarks"></a>解説  
次のような可変長データを格納できるデータ型で使用すると、`DATALENGTH` は非常に便利です。
- **image**
- **ntext**
- **nvarchar**
- **text**
- **varbinary**
- **varchar**
  
NULL 値の場合、`DATALENGTH` は NULL を返します。
  
> [!NOTE]  
> 戻り値は、互換性レベルによって変わることがあります。 互換性レベルの詳細については、「[ALTER DATABASE 互換性レベル &#40;Transact-SQL&#41;](../../t-sql/statements/alter-database-transact-sql-compatibility-level.md)」を参照してください。  

> [!NOTE]
> 特定の文字列式にエンコードされた文字数を取得するには [LEN](../../t-sql/functions/len-transact-sql.md) を使用し、特定の文字列式のバイト サイズを取得するには [DATALENGTH](../../t-sql/functions/datalength-transact-sql.md) を使用します。 これらの出力は、データ型および列で使用されているエンコードの種類によっては、異なる場合があります。 異なる種類のエンコードでの記憶域の違いについて詳しくは、「[照合順序と Unicode のサポート](../../relational-databases/collations/collation-and-unicode-support.md)」をご覧ください。

## <a name="examples"></a>例  
この例では、`Product` テーブルの `Name` 列の長さが返されます。
  
```sql
USE AdventureWorks2016  
GO
SELECT length = DATALENGTH(EnglishProductName), EnglishProductName  
FROM dbo.DimProduct  
ORDER BY EnglishProductName;  
GO  
```  
  
## <a name="see-also"></a>関連項目
[LEN &#40;Transact-SQL&#41;](../../t-sql/functions/len-transact-sql.md)  
[CAST および CONVERT &#40;Transact-SQL&#41;](../../t-sql/functions/cast-and-convert-transact-sql.md)  
[データ型 &#40;Transact-SQL&#41;](../../t-sql/data-types/data-types-transact-sql.md)  
[システム関数 &#40;Transact-SQL&#41;](../../relational-databases/system-functions/system-functions-category-transact-sql.md)
