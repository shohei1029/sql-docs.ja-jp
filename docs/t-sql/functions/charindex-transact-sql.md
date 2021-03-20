---
title: CHARINDEX (Transact-SQL) | Microsoft Docs
description: CHARINDEX 関数の Transact-SQL リファレンス。
ms.date: 07/24/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- CHARINDEX
- CHARINDEX_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- expressions [SQL Server], pattern searching
- CHARINDEX function
- pattern searching [SQL Server]
- starting point of expression in character string
ms.assetid: 78c10341-8373-4b30-b404-3db20e1a3ac4
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 1ec1919c5b9b60e4109b7f76463dab21fd1e50b3
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104750002"
---
# <a name="charindex-transact-sql"></a>CHARINDEX (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

この関数は、2 番目の文字式内の 1 つの文字式を検索して、見つかった場合には最初の式の開始位置を返します。
  
![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)
  
## <a name="syntax"></a>構文  
  
```syntaxsql
CHARINDEX ( expressionToFind , expressionToSearch [ , start_location ] )   
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
*expressionToFind*  
検索するシーケンスを含む文字[式](../../t-sql/language-elements/expressions-transact-sql.md)。 *expressionToFind* には 8000 文字の制限があります。
  
*expressionToSearch*  
検索する文字式。
  
*start_location*  
検索が開始される **integer** 型または **bigint** 型の式。 *start_location* を指定しない場合、または負の値や 0 を指定した場合は、*expressionToSearch* の先頭から検索が開始されます。
  
## <a name="return-types"></a>戻り値の型
*expressionToSearch* が **varchar(max)** 、**nvarchar(max)** 、または **varbinary(max)** データ型の場合は **bigint**、それ以外の場合は **int**。
  
## <a name="remarks"></a>解説  
*expressionToFind* または *expressionToSearch* のいずれかの式が Unicode データ型 (**nchar** または **nvarchar**) の場合で、他の式がそうでない場合、CHARINDEX 関数はその他の式を Unicode データ型に変換します。 CHARINDEX は、**image**、**ntext**、または **text** データ型では使用できません。
  
*expressionToFind* 式または *expressionToSearch* のいずれかに NULL 値がある場合、CHARINDEX は NULL を返します。
  
CHARINDEX によって *expressionToSearch* 内で *expressionToFind* が見つからない場合、CHARINDEX は 0 を返します。
  
CHARINDEX は、入力の照合順序に基づいて比較を行います。 特定の照合順序で比較を行うには、COLLATE を使用して、入力に明示的な照合順序を適用します。
  
開始位置は 0 ではなく 1 を基準とします。
  
0x0000 (**char(0)** ) は Windows 照合順序で未定義の文字であり、CHARINDEX に含めることはできません。
  
## <a name="supplementary-characters-surrogate-pairs"></a>補助文字 (サロゲート ペア)  
SC の照合順序を使用する場合、*start_location* と戻り値では、サロゲート ペアが 2 文字ではなく 1 文字としてカウントされます。 詳細については、「 [Collation and Unicode Support](../../relational-databases/collations/collation-and-unicode-support.md)」を参照してください。
  
## <a name="examples"></a>例  
  
### <a name="a-returning-the-starting-position-of-an-expression"></a>A. 式の開始位置を返す  
この例では、検索された文字列値の変数 `@document` で `bicycle` を検索します。
  
```sql
DECLARE @document VARCHAR(64);  
SELECT @document = 'Reflectors are vital safety' +  
                   ' components of your bicycle.';  
SELECT CHARINDEX('bicycle', @document);  
GO  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
-----------   
48            
```  
  
### <a name="b-searching-from-a-specific-position"></a>B. 特定の位置から検索する  
この例では、オプションの *start_location* パラメーターを使用して、検索された文字列値の変数 `@document` の 5 文字目で `vital` の検索を開始します。
  
```sql
DECLARE @document VARCHAR(64);  
  
SELECT @document = 'Reflectors are vital safety' +  
                   ' components of your bicycle.';  
SELECT CHARINDEX('vital', @document, 5);  
GO  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
-----------   
16            
  
(1 row(s) affected)  
```  
  
### <a name="c-searching-for-a-nonexistent-expression"></a>C. 存在しない式を検索する  
この例では、CHARINDEX が *expressionToSearch* で *expressionToFind* を検出できない場合の結果セットを示します。
  
```sql
DECLARE @document VARCHAR(64);  
  
SELECT @document = 'Reflectors are vital safety' +  
                   ' components of your bicycle.';  
SELECT CHARINDEX('bike', @document);  
GO  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
-----------
0
  
(1 row(s) affected)
```
  
### <a name="d-performing-a-case-sensitive-search"></a>D. 大文字小文字を区別する検索を実行する  
この例では、大文字と小文字を区別して、検索された文字列 `'This is a Test``'` に含まれる文字列 `'TEST'` を検索します。
  
```sql
USE tempdb;  
GO  
--perform a case sensitive search  
SELECT CHARINDEX ( 'TEST',  
       'This is a Test'  
       COLLATE Latin1_General_CS_AS);  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
-----------
0
```  
  
この例では、大文字と小文字を区別して、`'This is a Test'` に含まれる文字列 `'Test'` を検索します。
  
```sql
  
USE tempdb;  
GO  
SELECT CHARINDEX ( 'Test',  
       'This is a Test'  
       COLLATE Latin1_General_CS_AS);  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
-----------
11
```  
  
### <a name="e-performing-a-case-insensitive-search"></a>E. 大文字小文字を区別しない検索を実行する  
この例では、大文字と小文字を区別せずに、`'This is a Test'` に含まれる文字列 `'TEST'` を検索します。
  
```sql
USE tempdb;  
GO  
SELECT CHARINDEX ( 'TEST',  
       'This is a Test'  
       COLLATE Latin1_General_CI_AS);  
GO  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
-----------
11
```  
  
## <a name="examples-sssdwfull-and-sspdw"></a>例: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)]、[!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="f-searching-from-the-start-of-a-string-expression"></a>F. 文字列式の先頭から検索する  
この例では、`This is a string` の位置 1 (最初の文字) から開始して、文字列 `This is a string` に含まれる文字列 `is` の最初の位置を返します。
  
```sql
SELECT CHARINDEX('is', 'This is a string');  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
---------
3
```  
  
### <a name="g-searching-from-a-position-other-than-the-first-position"></a>G. 最初の位置以外の位置から検索する  
この例では、位置 4 (4 番目の文字) から開始して、文字列 `This is a string` に含まれる文字列 `is` の最初の位置を返します。
  
```sql
SELECT CHARINDEX('is', 'This is a string', 4);  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
---------
 6
 ```  
  
### <a name="h-results-when-the-string-is-not-found"></a>H. 文字列が見つからない場合の結果  
この例では、CHARINDEX で文字列 *string_pattern* が検索文字列に見つからない場合の戻り値を示します。
  
```sql
SELECT TOP(1) CHARINDEX('at', 'This is a string') FROM dbo.DimCustomer;  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
---------
0
```  
  
## <a name="see-also"></a>関連項目
 [LEN &#40;Transact-SQL&#41;](../../t-sql/functions/len-transact-sql.md)  
 [PATINDEX &#40;Transact-SQL&#41;](../../t-sql/functions/patindex-transact-sql.md)  
 [文字列関数 &#40;Transact-SQL&#41;](../../t-sql/functions/string-functions-transact-sql.md)  
 [+ &#40;文字列連結&#41; &#40;Transact-SQL&#41;](../../t-sql/language-elements/string-concatenation-transact-sql.md)  
 [照合順序と Unicode のサポート](../../relational-databases/collations/collation-and-unicode-support.md)  
  
  


