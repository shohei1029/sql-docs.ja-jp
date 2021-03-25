---
title: '[ ] 文字列と一致するワイルドカード'
description: ワイルドカードを使用して、1 つ以上の文字と一致させます。
titleSuffix: SQL Server (Transact-SQL)
ms.custom: seo-lt-2019
ms.date: 12/06/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- Match
- wildcard
- '[ ]'
- '[_]_TSQL'
dev_langs:
- TSQL
helpviewer_keywords:
- wildcard characters [SQL Server]
- '[ ] (wildcard - character(s) to match)'
ms.assetid: 57817576-0bf1-49ed-b05d-fac27e8fed7a
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 7530d9c705a63054a36fdb73ed50994bee8937ec
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104753402"
---
# <a name="--wildcard---characters-to-match-transact-sql"></a>\[ \] (ワイルドカード - 一致する文字列) (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

角かっこ `[ ]` で指定された範囲または集合の任意の 1 文字に一致します。 これらのワイルドカード文字は、`LIKE` や `PATINDEX` などのパターン検索を含む文字列比較で使用できます。  

 
## <a name="examples"></a>例  
### <a name="a-simple-example"></a>A: 簡単な例   
次の例では、文字 `m` で始まる名前が返されます。 `[n-z]` は、2 番目の文字が `n` から `z` の範囲に含まれる必要があることを指定します。 パーセントのワイルドカード `%` は、2 文字に 0 個以上の任意の文字列が続くことを指定します。 `model` データベースと `msdb` データベースがこの条件を満たしています。 `master` データベースは条件を満たさず、結果セットから除外されます。
 
```sql
SELECT name FROM sys.databases
WHERE name LIKE 'm[n-z]%';
```
[!INCLUDE[ssResult_md](../../includes/ssresult-md.md)]  

```
name
-----
model
msdb
```   
 他にも該当するデータベースがインストールされている可能性があります。


### <a name="b-more-complex-example"></a>B: より複雑な例   
 次の例では、4 桁の郵便番号付きの住所を持つ [!INCLUDE[ssSampleDBCoShort](../../includes/sssampledbcoshort-md.md)] のすべての従業員の ID と名前を [] 演算子を使用して検索します。  
  
```sql  
-- Uses AdventureWorks  
  
SELECT e.BusinessEntityID, p.FirstName, p.LastName, a.PostalCode  
FROM HumanResources.Employee AS e  
INNER JOIN Person.Person AS p ON e.BusinessEntityID = p.BusinessEntityID  
INNER JOIN Person.BusinessEntityAddress AS ea ON e.BusinessEntityID = ea.BusinessEntityID  
INNER JOIN Person.Address AS a ON a.AddressID = ea.AddressID  
WHERE a.PostalCode LIKE '[0-9][0-9][0-9][0-9]';  
```  
  
[!INCLUDE[ssResult_md](../../includes/ssresult-md.md)]  
  
```  
EmployeeID      FirstName      LastName      PostalCode  
----------      ---------      ---------     ----------  
290             Lynn           Tsoflias      3000  
```  

### <a name="c-using-a-set-that-combines-ranges-and-single-characters"></a>C: 範囲と 1 文字を組み合わせたセットを使用する

ワイルドカード セットには、1 文字と範囲の両方を含めることができます。 次の例では、[] 演算子を使用して、1 つの数字と一連の特殊文字で始まる文字列を検索します。

```sql
SELECT [object_id], OBJECT_NAME(object_id) AS [object_name], name, column_id 
FROM sys.columns 
WHERE name LIKE '[0-9!@#$.,;_]%';
```

[!INCLUDE[ssResult_md](../../includes/ssresult-md.md)]  

```
object_id     object_name                         name  column_id
---------     -----------                         ----  ---------
615673241     vSalesPersonSalesByFiscalYears      2002  5
615673241     vSalesPersonSalesByFiscalYears      2003  6
615673241     vSalesPersonSalesByFiscalYears      2004  7
1591676718    JunkTable                           _xyz  1
```
  
## <a name="see-also"></a>参照  
 [LIKE &#40;Transact-SQL&#41;](../../t-sql/language-elements/like-transact-sql.md)   
 [PATINDEX &#40;Transact-SQL&#41;](../../t-sql/functions/patindex-transact-sql.md)   
  [% &#40;ワイルドカード - 一致する文字列&#41; &#40;Transact-SQL&#41;](../../t-sql/language-elements/percent-character-wildcard-character-s-to-match-transact-sql.md)   
 [&#91;^&#93; &#40;ワイルドカード - 一致しない文字列&#41; &#40;Transact-SQL&#41;](../../t-sql/language-elements/wildcard-character-s-not-to-match-transact-sql.md)     
 [\_ &#40;ワイルドカード - 1 文字に一致&#41; &#40;Transact-SQL&#41;](../../t-sql/language-elements/wildcard-match-one-character-transact-sql.md)  
    
  
