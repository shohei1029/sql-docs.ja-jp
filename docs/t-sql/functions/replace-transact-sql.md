---
title: REPLACE (Transact-SQL) | Microsoft Docs
description: REPLACE 関数の Transact-SQL リファレンス。指定された文字列値をすべて別の文字列値に置き換えます。
ms.date: 08/23/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- REPLACE_TSQL
- REPLACE
dev_langs:
- TSQL
helpviewer_keywords:
- first string expression [SQL Server]
- replacing string expression
- third string expressions [SQL Server]
- second string expressions [SQL Server]
- REPLACE function
ms.assetid: 8a7aaaf2-62e3-46c0-8e44-fa22290dd86b
author: julieMSFT
ms.author: jrasnick
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: caef51295d4d38f1021f28f575bd96195cdbab63
ms.sourcegitcommit: efce0ed7d1c0ab36a4a9b88585111636134c0fbb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104833848"
---
# <a name="replace-transact-sql"></a>REPLACE (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

指定した文字列値をすべて別の文字列値に置き換えます。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql
REPLACE ( string_expression , string_pattern , string_replacement )  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
 *string_expression*  
 検索する文字列[式](../../t-sql/language-elements/expressions-transact-sql.md)を指定します。 *string_expression* 文字またはバイナリ データ型であることができます。  
  
 *string\_pattern*  
 検索するサブストリングです。 *string_pattern* 文字またはバイナリ データ型であることができます。 *string_pattern* は、1 ページに収まる最大バイト数を超えないようにしてください。 *string_pattern* が空の文字列 ('') の場合、*string_expression* は変更されずに返されます。 
  
 *string\_replacement*  
 置き換え後の文字列です。 *string_replacement* 文字またはバイナリ データ型であることができます。  
  
## <a name="return-types"></a>戻り値の型  
 返します **nvarchar** が、入力引数のいずれかの場合、 **nvarchar** データが入力のそれ以外の場合を返します。 を置き換える **varchar** です。  
  
 いずれかの引数が NULL の場合は、NULL を返します。  
  
 場合 *string_expression* の種類はありません **varchar (max)** または **nvarchar (max)、** 置換 は 8,000 バイトで戻り値を切り捨てます。 8,000 バイトを超える値を返すには、大きな値を格納できるデータ型に *string_expression* を明示的にキャストする必要があります。  
  
## <a name="remarks"></a>解説  
 REPLACE は、入力の照合順序に基づいて比較を行います。 特定の照合順序で比較を行うには、[COLLATE](~/t-sql/statements/collations.md) を使用して、入力に明示的な照合順序を適用します。  
  
 0x0000 (**char(0)** ) の Windows 照合順序で未定義の文字は、REPLACE に含めることができません。  
  
## <a name="examples"></a>例  
 次の例では、`abcdefghi` にある文字列 `cde` を `xxx` に置換します。  
  
```sql  
SELECT REPLACE('abcdefghicde','cde','xxx');  
GO  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
------------  
abxxxfghixxx  
(1 row(s) affected)  
```  
  
 次の例では、`COLLATE` 関数を使用します。  
  
```sql  
SELECT REPLACE('This is a Test'  COLLATE Latin1_General_BIN,  
'Test', 'desk' );  
GO  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
------------  
This is a desk  
(1 row(s) affected)  
```  

  
## <a name="see-also"></a>参照  
 [CONCAT &#40;Transact-SQL&#41;](../../t-sql/functions/concat-transact-sql.md)  
 [CONCAT_WS &#40;Transact-SQL&#41;](../../t-sql/functions/concat-ws-transact-sql.md)  
 [FORMATMESSAGE &#40;Transact-SQL&#41;](../../t-sql/functions/formatmessage-transact-sql.md)  
 [QUOTENAME &#40;Transact-SQL&#41;](../../t-sql/functions/quotename-transact-sql.md)  
 [REVERSE &#40;Transact-SQL&#41;](../../t-sql/functions/reverse-transact-sql.md)  
 [STRING_AGG &#40;Transact-SQL&#41;](../../t-sql/functions/string-agg-transact-sql.md)  
 [STRING_ESCAPE &#40;Transact-SQL&#41;](../../t-sql/functions/string-escape-transact-sql.md)  
 [STUFF &#40;Transact-SQL&#41;](../../t-sql/functions/stuff-transact-sql.md)  
 [TRANSLATE &#40;Transact-SQL&#41;](../../t-sql/functions/translate-transact-sql.md)  
 [データ型 &#40;Transact-SQL&#41;](../../t-sql/data-types/data-types-transact-sql.md)   
 [文字列関数 &#40;Transact-SQL&#41;](../../t-sql/functions/string-functions-transact-sql.md)  
  
