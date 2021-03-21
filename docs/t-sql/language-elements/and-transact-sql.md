---
description: AND (Transact-SQL)
title: AND (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- AND_TSQL
- AND
dev_langs:
- TSQL
helpviewer_keywords:
- values [SQL Server], TRUE
- "TRUE"
- AND, about AND operators
- AND
- combining expressions
ms.assetid: b61d7f8d-5a51-49b7-91dd-f6190a5a0fb9
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 04d1945753618e5552750af7959e62519113015f
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104754042"
---
# <a name="and-transact-sql"></a>AND (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  2 つのブール式を結合し、両方の式が **TRUE** の場合、**TRUE** を返します。 1 つのステートメントの中で複数の論理演算子が使われている場合は、**AND** 演算子が最初に評価されます。 かっこを使うと、演算の順序を変更することができます。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql
boolean_expression AND boolean_expression  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
 *boolean_expression*  
 **TRUE**、**FALSE** または、**UNKNOWN** のブール値を返す有効な [式](../../t-sql/language-elements/expressions-transact-sql.md)を指定します。  
  
## <a name="result-types"></a>戻り値の型  
 **Boolean**  
  
## <a name="result-value"></a>結果の値  
 両方の式が TRUE の場合、TRUE を返します。  
  
## <a name="remarks"></a>解説  
 次の表は、TRUE 値と FALSE 値を AND 演算子を使用して比較する場合の結果です。  
  
||true|false|UNKNOWN|  
|------|----------|-----------|-------------|  
|**TRUE**|true|false|UNKNOWN|  
|**FALSE**|FALSE|false|false|  
|**UNKNOWN**|UNKNOWN|FALSE|UNKNOWN|  
  
## <a name="examples"></a>例  
  
### <a name="a-using-the-and-operator"></a>A. AND 演算子の使用  
 次の例では、役職が `Marketing Assistant` で、なおかつ、利用可能な休暇時間数が `41` 時間を超える従業員の情報を選択します。  
  
```sql  
-- Uses AdventureWorks  
  
SELECT  BusinessEntityID, LoginID, JobTitle, VacationHours   
FROM HumanResources.Employee  
WHERE JobTitle = 'Marketing Assistant'  
AND VacationHours > 41 ;  
```  
  
### <a name="b-using-the-and-operator-in-an-if-statement"></a>B. IF ステートメントでの AND 演算子の使用  
 次の例は、IF ステートメントでの AND の使用方法を示しています。 1 つ目のステートメントでは、`1 = 1` と `2 = 2` の両方が true であるため、結果は true です。 2 つ目の例では、引数 `2 = 17` が false であるため、結果は false です。  
  
```sql  
IF 1 = 1 AND 2 = 2  
BEGIN  
   PRINT 'First Example is TRUE'  
END  
ELSE PRINT 'First Example is FALSE' ;  
GO  
  
IF 1 = 1 AND 2 = 17  
BEGIN  
   PRINT 'Second Example is TRUE'  
END  
ELSE PRINT 'Second Example is FALSE' ;  
GO  
```  
  
## <a name="see-also"></a>参照  
 [組み込み関数 &#40;Transact-SQL&#41;](~/t-sql/functions/functions.md)   
 [演算子 &#40;Transact-SQL&#41;](../../t-sql/language-elements/operators-transact-sql.md)   
 [SELECT &#40;Transact-SQL&#41;](../../t-sql/queries/select-transact-sql.md)   
 [WHERE &#40;Transact-SQL&#41;](../../t-sql/queries/where-transact-sql.md)  
  
  
