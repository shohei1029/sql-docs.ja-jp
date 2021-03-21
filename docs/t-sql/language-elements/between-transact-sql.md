---
description: BETWEEN (Transact-SQL)
title: BETWEEN (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 08/28/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- BETWEEN
- BETWEEN_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- inclusive ranges
- testing range
- exclusive ranges
- NOT BETWEEN operator
- BETWEEN operator
- range to test [SQL Server]
ms.assetid: a5d5b050-203e-4355-ac85-e08ef5ca7823
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 8f8df816ba1885e6dd713d8b77affcd4b1175c3c
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104747862"
---
# <a name="between-transact-sql"></a>BETWEEN (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  テスト範囲を指定します。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql
test_expression [ NOT ] BETWEEN begin_expression AND end_expression  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
 *test_expression*  
 *begin_expression* と *end_expression* で定義した範囲内でテストする [式](../../t-sql/language-elements/expressions-transact-sql.md)を指定します。 *test_expression* のデータ型は、*begin_expression* および *end_expression* の両方と同じにする必要があります。  
  
 NOT  
 述語の結果を否定することを指定します。  
  
 *begin_expression*  
 任意の有効な式です。 *begin_expression* のデータ型は、*test_expression* および *end_expression* の両方と同じにする必要があります。  
  
 *end_expression*  
 任意の有効な式です。 *end_expression* のデータ型は、*test_expression* および *begin_expression* の両方と同じにする必要があります。  
  
 AND  
 *begin_expression* と *end_expression* で表される範囲内で *test_expression* をテストする必要があることを示すプレースホルダーとして動作します。  
  
## <a name="result-types"></a>戻り値の型  
 **Boolean**  
  
## <a name="result-value"></a>結果の値  
 *test_expression* の値が *begin_expression* の値以上で *end_expression* の値以下の場合、BETWEEN は **TRUE** を返します。  
  
 *test_expression* の値が *begin_expression* の値より小さく *end_expression* の値より大きい場合、NOT BETWEEN は **TRUE** を返します。  
  
## <a name="remarks"></a>解説  
 両端を除いた範囲を指定するには、より大きいことを表す演算子 (>) と、より小さいことを表す演算子 (<) を使用します。 BETWEEN または NOT BETWEEN の述語に対する入力が NULL の場合、結果は UNKNOWN になります。  
  
## <a name="examples"></a>例  
  
### <a name="a-using-between"></a>A. BETWEEN を使用する  
 次の例は、データベース内のデータベース ロールに関する情報を返します。 最初のクエリはすべてのロールを返します。 2 つ目の例では、`BETWEEN` 句を使用して、指定された `database_id` 値にロールを制限します。  
  
```sql  
SELECT principal_id, name 
FROM sys.database_principals
WHERE type = 'R';

SELECT principal_id, name 
FROM sys.database_principals
WHERE type = 'R'
AND principal_id BETWEEN 16385 AND 16390;
GO  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]   
```  
principal_id    name
------------  ---- 
0               public
16384           db_owner
16385           db_accessadmin
16386           db_securityadmin
16387           db_ddladmin
16389           db_backupoperator
16390           db_datareader
16391           db_datawriter
16392           db_denydatareader
16393           db_denydatawriter
```  
```  
principal_id    name
------------  ---- 
16385           db_accessadmin
16386           db_securityadmin
16387           db_ddladmin
16389           db_backupoperator
16390           db_datareader
```  
  
### <a name="b-using--and--instead-of-between"></a>B. BETWEEN の代わりに > と < を使用する  
 次の例では、より大きいことを表す演算子 (`>`) と、より小さいことを表す演算子 (`<`) を使用します。どちらも両端を含まないため、前の例の 10 行とは異なり、9 行が返されます。  
  
```sql  
-- Uses AdventureWorks  
  
SELECT e.FirstName, e.LastName, ep.Rate  
FROM HumanResources.vEmployee e   
JOIN HumanResources.EmployeePayHistory ep   
    ON e.BusinessEntityID = ep.BusinessEntityID  
WHERE ep.Rate > 27 AND ep.Rate < 30  
ORDER BY ep.Rate;  
GO  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 ```  
 FirstName   LastName             Rate  
 ---------   -------------------  ---------  
 Paula       Barreto de Mattos    27.1394  
 Janaina     Bueno                27.4038  
 Dan         Bacon                27.4038  
 Ramesh      Meyyappan            27.4038  
 Karen       Berg                 27.4038  
 David       Bradley              28.7500  
 Hazem       Abolrous             28.8462  
 Ovidiu      Cracium              28.8462  
 Rob         Walters              29.8462  
 ```    
  
### <a name="c-using-not-between"></a>C. NOT BETWEEN を使用する  
 次の例では、指定した範囲 `27` ～ `30` に該当しないすべての行を検索します。  
  
```sql  
-- Uses AdventureWorks  
  
SELECT e.FirstName, e.LastName, ep.Rate  
FROM HumanResources.vEmployee e   
JOIN HumanResources.EmployeePayHistory ep   
    ON e.BusinessEntityID = ep.BusinessEntityID  
WHERE ep.Rate NOT BETWEEN 27 AND 30  
ORDER BY ep.Rate;  
GO  
```  
  
### <a name="d-using-between-with-datetime-values"></a>D. BETWEEN を datetime 値と共に使用する  
 次の例では、**datetime** 値が `'20011212'` から `'20020105'` までの範囲内にある行を取得します。  
  
```sql  
-- Uses AdventureWorks  
  
SELECT BusinessEntityID, RateChangeDate  
FROM HumanResources.EmployeePayHistory  
WHERE RateChangeDate BETWEEN '20011212' AND '20020105';  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 ```  
 BusinessEntityID RateChangeDate  
 ----------- -----------------------  
 3           2001-12-12 00:00:00.000  
 4           2002-01-05 00:00:00.000  
 ```  
 
 クエリ内の日付値と `RateChangeDate` 列の **datetime** 値は、時刻部分なしで日付が指定されているため、このクエリは予測される行を取得します。 時刻部分は、指定されていなければ、既定で午前 12 時になります。 時刻部分として 2002 年 1 月 5 日の午前 12 時よりも後の値が格納されている行は、 2002-01-05 は範囲外になるため、このクエリでは返されません。  
  
  
## <a name="see-also"></a>参照  
 [&#62; &#40;より大きい&#41; &#40;Transact-SQL&#41;](../../t-sql/language-elements/greater-than-transact-sql.md)   
 [&#60; &#40;より小さい&#41; &#40;Transact-SQL&#41;](../../t-sql/language-elements/less-than-transact-sql.md)   
 [式 &#40;Transact-SQL&#41;](../../t-sql/language-elements/expressions-transact-sql.md)   
 [組み込み関数 &#40;Transact-SQL&#41;](~/t-sql/functions/functions.md)   
 [演算子 &#40;Transact-SQL&#41;](../../t-sql/language-elements/operators-transact-sql.md)   
 [SELECT &#40;Transact-SQL&#41;](../../t-sql/queries/select-transact-sql.md)   
 [WHERE &#40;Transact-SQL&#41;](../../t-sql/queries/where-transact-sql.md)  
  
  


