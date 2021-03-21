---
description: LEFT (Transact-SQL)
title: LEFT (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/13/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- LEFT
- LEFT_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- character strings [SQL Server], LEFT
- characters [SQL Server], leftmost
- LEFT function
- leftmost character of expression
ms.assetid: 44a8c71b-63d8-458b-8b5d-99d570067c3c
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 92014df1a2c761690c0b5371f16f4f29f8b9e167
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104749312"
---
# <a name="left-transact-sql"></a>LEFT (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  文字列の左端から、指定された数だけ文字を返します。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql
LEFT ( character_expression , integer_expression )  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
 *character_expression*  
 文字データまたはバイナリ データの[式](../../t-sql/language-elements/expressions-transact-sql.md)を指定します。 *character_expression* には定数、変数、または列を指定できます。 *character_expression* には、**text** または **ntext** を除く任意のデータ型 (暗黙的に **varchar** または **nvarchar** に変換できるデータ型) を使用できます。 それ以外の場合は、[CAST](../../t-sql/functions/cast-and-convert-transact-sql.md) 関数を使用して *character_expression* を明示的に変換します。  
 
> [!NOTE]  
> *string_expression* の型が **binary** か **varbinary** の場合、LEFT では、**varchar** への暗黙的変換が実行され、そのため、バイナリ入力は保持されません。  
  
 *integer_expression*  
 返される *character_expression* の文字数を指定する正の整数です。 *integer_expression* が負の場合、エラーが返されます。 *integer_expression* が **bigint** 型で、値が大きい場合、*character_expression* には **varchar(max)** などのラージ データ型を使用する必要があります。  
  
 *integer_expression* パラメーターは、UTF-16 サロゲート文字を 1 文字としてカウントします。  
  
## <a name="return-types"></a>戻り値の型  
 *character_expression* が非 Unicode 文字データ型の場合は、**varchar** を返します。  
  
 *character_expression* が Unicode 文字データ型の場合は、**nvarchar** を返します。  
  
## <a name="remarks"></a>解説  
 SC 照合順序を使用する場合、*integer_expression* パラメーターは UTF-16 サロゲート ペアを 1 文字としてカウントします。 詳細については、「 [Collation and Unicode Support](../../relational-databases/collations/collation-and-unicode-support.md)」を参照してください。  
  
## <a name="examples"></a>例  
  
### <a name="a-using-left-with-a-column"></a>A. 列を指定した LEFT を使用する  
 次の例では、[!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] データベースの `Product` テーブル内の各製品名の左端から 5 文字が返されます。  
  
```sql  
SELECT LEFT(Name, 5)   
FROM Production.Product  
ORDER BY ProductID;  
GO  
```  
  
### <a name="b-using-left-with-a-character-string"></a>B. 文字列を指定した LEFT を使用する  
 次の例では、`LEFT` を使用して `abcdefg` という文字列の左端の 2 文字を返します。  
  
```sql  
SELECT LEFT('abcdefg',2);  
GO  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
--   
ab   
  
(1 row(s) affected)  
```  
  
## <a name="examples-sssdwfull-and-sspdw"></a>例: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)]、[!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="c-using-left-with-a-column"></a>C. 列を指定した LEFT を使用する  
 次の例では、各製品名の左端の 5 文字を返します。  
  
```sql  
-- Uses AdventureWorks  
  
SELECT LEFT(EnglishProductName, 5)   
FROM dbo.DimProduct  
ORDER BY ProductKey;  
```  
  
### <a name="d-using-left-with-a-character-string"></a>D. 文字列を指定した LEFT を使用する  
 次の例では、`LEFT` を使用して `abcdefg` という文字列の左端の 2 文字を返します。  
  
```sql  
-- Uses AdventureWorks  
  
SELECT LEFT('abcdefg',2) FROM dbo.DimProduct;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
--   
ab  
```  
  
## <a name="see-also"></a>参照  
 [LTRIM &#40;Transact-SQL&#41;](../../t-sql/functions/ltrim-transact-sql.md)  
 [RIGHT &#40;Transact-SQL&#41;](../../t-sql/functions/right-transact-sql.md)  
 [RTRIM &#40;Transact-SQL&#41;](../../t-sql/functions/rtrim-transact-sql.md)  
 [STRING_SPLIT &#40;Transact-SQL&#41;](../../t-sql/functions/string-split-transact-sql.md)  
 [SUBSTRING &#40;Transact-SQL&#41;](../../t-sql/functions/substring-transact-sql.md)  
 [TRIM &#40;Transact-SQL&#41;](../../t-sql/functions/trim-transact-sql.md)  
 [CAST および CONVERT &#40;Transact-SQL&#41;](../../t-sql/functions/cast-and-convert-transact-sql.md)   
 [データ型 &#40;Transact-SQL&#41;](../../t-sql/data-types/data-types-transact-sql.md)   
 [文字列関数 &#40;Transact-SQL&#41;](../../t-sql/functions/string-functions-transact-sql.md)  
  
  

