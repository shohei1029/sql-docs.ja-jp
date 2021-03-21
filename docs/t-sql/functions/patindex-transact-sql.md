---
description: PATINDEX (Transact-SQL)
title: PATINDEX (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 07/19/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- PATINDEX
- PATINDEX_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- first occurrence of pattern [SQL Server]
- searches [SQL Server], pattern starting position
- starting position of patten search
- pattern searching [SQL Server]
- PATINDEX function
ms.assetid: c0dfb17f-2230-4e36-98da-a9b630bab656
author: julieMSFT
ms.author: jrasnick
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 10610617de5844e637a264335cd96889aae53f03
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104749262"
---
# <a name="patindex-transact-sql"></a>PATINDEX (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  すべて有効なテキストおよび文字データ型について、指定された式の中で、パターンが最初に現れる先頭位置を返します。パターンが見つからない場合は、0 を返します。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```  
PATINDEX ( '%pattern%' , expression )  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
 *pattern*  
 検索するシーケンスを含む文字式です。 ワイルドカード文字も指定できますが、(先頭の文字または最後の文字を検索する場合を除き) *pattern* を % 文字で囲む必要があります。 *pattern* は文字列データ型に分類される式です。 *pattern* の上限は 8,000 文字です。

 > [!NOTE]
 > 従来の正規表現は、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] でネイティブにサポートされていませんが、さまざまなワイルドカード表現を使用すると、同様の複雑なパターン マッチングを実現することができます。 ワイルドカード構文の詳細については、ドキュメント「[文字列演算子](../../t-sql/language-elements/string-operators-transact-sql.md)」を参照してください。
  
 *式 (expression)*  
 [式](../../t-sql/language-elements/expressions-transact-sql.md)です。通常は、指定したパターンで検索する列です。 *式* は文字列データ型に分類されます。  
  
## <a name="return-types"></a>戻り値の型  
*expression* が **varchar(max)** または **nvarchar(max)** データ型の場合は **bigint**。それ以外の場合は **int**。  
  
## <a name="remarks"></a>注釈  
*pattern* または *式* が NULL の場合、PATINDEX は NULL を返します。  
 
PATINDEX の開始位置は 1 です。
 
PATINDEX では、入力の照合順序に基づいて比較が行われます。 指定した照合順序で比較を実行するには、COLLATE を使って入力に明示的な照合順序を適用できます。  
  
## <a name="supplementary-characters-surrogate-pairs"></a>補助文字 (サロゲート ペア)  
SC の照合順序を使用する場合、戻り値では、*expression* パラメーターの UTF-16 サロゲート ペアが 1 文字としてカウントされます。 詳細については、「 [Collation and Unicode Support](../../relational-databases/collations/collation-and-unicode-support.md)」を参照してください。  
  
0x0000 (**char(0)**) の Windows 照合順序で未定義の文字は、PATINDEX に含めることができません。  
  
## <a name="examples"></a>例  
  
### <a name="a-simple-patindex-example"></a>A. 簡単な PATINDEX の例  
 次の例では、文字 `ter` の開始位置の短い文字列 (`interesting data`) を確認します。  
  
```sql  
SELECT position = PATINDEX('%ter%', 'interesting data');  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]  

```
position
--------
3
```
  
### <a name="b-using-a-pattern-with-patindex"></a>B. PATINDEX でパターンを使用する  
次の例では、[!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] データベースの `ensure` テーブルにある `DocumentSummary` 列の特定の行で、パターン `Document` が始まる位置を検出します。  
  
```sql  
SELECT position = PATINDEX('%ensure%',DocumentSummary)  
FROM Production.Document  
WHERE DocumentNode = 0x7B40;  
GO   
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```
position
--------  
64  
```  
  
検索する行を `WHERE` 句で限定しない場合は、クエリによりテーブル内のすべての行が返されます。パターンが見つかった行は 0 以外の値に、パターンが見つからなかったすべての行は 0 になります。  
  
### <a name="c-using-wildcard-characters-with-patindex"></a>C. PATINDEX でワイルドカード文字を使用する  
 次の例では、ワイルドカードの % と _ を使用して、指定した文字列で任意の 1 文字と `'en'` が続くパターン `'ure'` が始まる位置を探します (インデックスは 1 から開始)。  
  
```sql  
SELECT position = PATINDEX('%en_ure%', 'Please ensure the door is locked!');  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```
position
--------  
8  
```  
  
`PATINDEX` は `LIKE` と同様の機能を持つので、任意のワイルドカードを使用できます。 パターンを % で囲む必要はありません。 `PATINDEX('a%', 'abc')` は 1 を返し、`PATINDEX('%a', 'cba')` は 3 を返します。  
  
 `LIKE` とは異なり、`PATINDEX` は `CHARINDEX` と同様に位置を返します。  

### <a name="d-using-complex-wildcard-expressions-with-patindex"></a>D. PATINDEX で複雑なワイルドカード式を使用する 
次の例では、`[^]` [文字列演算子](../../t-sql/language-elements/wildcard-character-s-not-to-match-transact-sql.md)を使用して、数字、文字、またはスペース以外の文字の位置を検索します。

```sql
SELECT position = PATINDEX('%[^ 0-9A-z]%', 'Please ensure the door is locked!'); 
```
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]  

```
position
--------
33
```

### <a name="e-using-collate-with-patindex"></a>E. PATINDEX で COLLATE を使用する  
 次の例では、`COLLATE` 関数を使って、検索する式の照合順序を明示的に指定します。  
  
```sql  
USE tempdb;  
GO  
SELECT PATINDEX ( '%ein%', 'Das ist ein Test'  COLLATE Latin1_General_BIN) ;  
GO  
```  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]  

```
position
--------
9
```

### <a name="f-using-a-variable-to-specify-the-pattern"></a>F. 変数を使用してパターンを指定する  
次の例では、変数を使用して *pattern* パラメーターに値を渡します。 この例では、[!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] データベースを使用します。  
  
```sql  
DECLARE @MyValue VARCHAR(10) = 'safety';   
SELECT position = PATINDEX('%' + @MyValue + '%', DocumentSummary)   
FROM Production.Document  
WHERE DocumentNode = 0x7B40;  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```
position
--------  
22
```  
  
## <a name="see-also"></a>関連項目  
 [LIKE &#40;Transact-SQL&#41;](../../t-sql/language-elements/like-transact-sql.md)   
 [CHARINDEX &#40;Transact-SQL&#41;](../../t-sql/functions/charindex-transact-sql.md)  
 [LEN &#40;Transact-SQL&#41;](../../t-sql/functions/len-transact-sql.md)  
 [データ型 &#40;Transact-SQL&#41;](../../t-sql/data-types/data-types-transact-sql.md)   
 [文字列関数 &#40;Transact-SQL&#41;](../../t-sql/functions/string-functions-transact-sql.md)   
 [[ ] &#40;ワイルドカード - 一致する文字列&#41; &#40;Transact-SQL&#41;](../../t-sql/language-elements/wildcard-character-s-to-match-transact-sql.md)   
 [[^] &#40;ワイルドカード - 一致しない文字列&#41; &#40;Transact-SQL&#41;](../../t-sql/language-elements/wildcard-character-s-not-to-match-transact-sql.md)   
 [_ &#40;ワイルドカード - 1 文字に一致&#41; &#40;Transact-SQL&#41;](../../t-sql/language-elements/wildcard-match-one-character-transact-sql.md)   
 [パーセント文字 &#40;ワイルドカード - 一致する文字列&#41; &#40;Transact-SQL&#41;](../../t-sql/language-elements/percent-character-wildcard-character-s-to-match-transact-sql.md)  
  
  


