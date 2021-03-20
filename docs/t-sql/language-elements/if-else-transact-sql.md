---
title: IF...ELSE (Transact-SQL) | Microsoft Docs
description: Transact-SQL ステートメントで制御フローを指定する IF-ELSE ステートメントの Transact-SQL 言語参照。
ms.date: 07/11/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- IF_TSQL
- IF
dev_langs:
- TSQL
helpviewer_keywords:
- IF...ELSE keyword
- ELSE (IF...ELSE) keyword
- ELSE keyword
- IF keyword
ms.assetid: 676c881f-dee1-417a-bc51-55da62398e81
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 27444d6caae36530c18907bf128b5ff255df0852
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104749862"
---
# <a name="ifelse-transact-sql"></a>IF...ELSE (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[tsql](../../includes/tsql-md.md)] ステートメントを実行する条件を設定します。 IF キーワードおよびその条件に続く [!INCLUDE[tsql](../../includes/tsql-md.md)] ステートメントは、条件が満たされる (ブール式から TRUE が返される) 場合に実行されます。 オプションの ELSE キーワードは、IF 条件が満たされない (ブール式から FALSE が返される) 場合に実行される別の [!INCLUDE[tsql](../../includes/tsql-md.md)] ステートメントを指定します。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql
IF Boolean_expression   
     { sql_statement | statement_block }   
[ ELSE   
     { sql_statement | statement_block } ]   
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
 *Boolean_expression*  
 TRUE または FALSE を返す式です。 ブール式が SELECT ステートメントを含む場合は、SELECT ステートメントをかっこで囲む必要があります。  
  
 { *sql_statement*| *statement_block* }  
 1 つの [!INCLUDE[tsql](../../includes/tsql-md.md)] ステートメント、またはステートメント ブロックを使用して定義した一連のステートメントです。 ステートメント ブロックを使用しない限り、IF または ELSE 条件は 1 つの [!INCLUDE[tsql](../../includes/tsql-md.md)] ステートメントのパフォーマンスにしか影響しません。  
  
 ステートメント ブロックを定義するには、流れ制御キーワードの BEGIN と END を使用してください。  
  
## <a name="remarks"></a>解説  
 IF...ELSE 構造は、バッチ、ストアド プロシージャ、およびアドホック クエリ内で使うことができます。 この構造がストアド プロシージャで使用される場合、あるパラメーターの存在を調べるためによく使用されます。  
  
 IF テストは、他の IF の後、または ELSE の後で入れ子にすることができます。 入れ子のレベルの制限は、使用可能なメモリによって異なります。  
  
## <a name="example"></a>例  
  
```sql
IF DATENAME(weekday, GETDATE()) IN (N'Saturday', N'Sunday')
       SELECT 'Weekend';
ELSE 
       SELECT 'Weekday';
```  
  
 例については、「[ELSE &#40;IF...ELSE&#41; &#40;Transact-SQL&#41;](../../t-sql/language-elements/else-if-else-transact-sql.md)」を参照してください。  
  
## <a name="examples-sssdwfull-and-sspdw"></a>例: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)]、[!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
 次の例では、`IF...ELSE` を使用し、`DimProduct` テーブルの項目の重み付けを基にして、2 つの応答のどちらをユーザーに表示するかを決定します。  
  
```sql
-- Uses AdventureWorksDW  

DECLARE @maxWeight FLOAT, @productKey INTEGER  
SET @maxWeight = 100.00  
SET @productKey = 424  
IF @maxWeight <= (SELECT Weight from DimProduct WHERE ProductKey = @productKey)   
    SELECT @productKey AS ProductKey, EnglishDescription, Weight, 'This product is too heavy to ship and is only available for pickup.' 
        AS ShippingStatus
    FROM DimProduct WHERE ProductKey = @productKey
ELSE  
    SELECT @productKey AS ProductKey, EnglishDescription, Weight, 'This product is available for shipping or pickup.' 
        AS ShippingStatus
    FROM DimProduct WHERE ProductKey = @productKey
```  
  
## <a name="see-also"></a>参照  
 [BEGIN...END &#40;Transact-SQL&#41;](../../t-sql/language-elements/begin-end-transact-sql.md)   
 [END &#40;BEGIN...END&#41; &#40;Transact-SQL&#41;](../../t-sql/language-elements/end-begin-end-transact-sql.md)   
 [SELECT &#40;Transact-SQL&#41;](../../t-sql/queries/select-transact-sql.md)   
 [WHILE &#40;Transact-SQL&#41;](../../t-sql/language-elements/while-transact-sql.md)   
 [CASE &#40;Transact-SQL&#41;](../../t-sql/language-elements/case-transact-sql.md)   
 [フロー制御言語 &#40;Transact-SQL&#41;](~/t-sql/language-elements/control-of-flow.md) [ELSE &#40;IF...ELSE&#41; &#40;Transact-SQL&#41;](../../t-sql/language-elements/else-if-else-transact-sql.md) 
