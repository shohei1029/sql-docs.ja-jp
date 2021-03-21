---
description: STUFF (Transact-SQL)
title: STUFF (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 11/17/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- STUFF
- STUFF_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- deleting characters
- STUFF function
- length characters
- removing characters
- replacing characters
- characters [SQL Server], replacing
- inserting data
ms.assetid: abb0afa9-44f6-42a2-a871-5f471dfb222b
author: julieMSFT
ms.author: jrasnick
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 4b04c5833274d897daaf7ba9e9e7579de174350f
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104753412"
---
# <a name="stuff-transact-sql"></a>STUFF (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  STUFF 関数では、文字列が別の文字列に挿入されます。 まず、1 番目の文字列の指定された開始位置から指定された長さの文字を削除し、次に、2 番目の文字列を 1 番目の文字列の指定された開始位置に挿入します。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql
STUFF ( character_expression , start , length , replaceWith_expression )  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
 *character_expression*  
 文字データの[式](../../t-sql/language-elements/expressions-transact-sql.md)です。 *character_expression* には、文字データまたはバイナリ データの定数、変数、または列を使用できます。  
  
 *start*  
 削除と挿入を開始する位置を指定する整数値です。 *start* が負の値またはゼロの場合は、null 文字列が返されます。 *start* が最初の *character_expression* よりも長い場合は、null 文字列が返されます。 *start* には **bigint** 型を使用できます。  
  
 *length*  
 削除する文字数を指定する整数です。 *length* が負の値の場合は、null 文字列が返されます。 *length* が最初の *character_expression* よりも長い場合、最後の *character_expression* の末尾の文字まで削除が実行されます。  *length* がゼロの場合、挿入は *start* の場所で行われ、文字は一切削除されません。 *length* には **bigint** 型を使用できます。

 *replaceWith_expression*  
 文字データの[式](../../t-sql/language-elements/expressions-transact-sql.md)です。 *character_expression* には、文字データまたはバイナリ データの定数、変数、または列を使用できます。 この式は、*character_expression* の *start* から始まる *length* 文字を置き換えます。 `NULL` に *replaceWith_expression* を指定すると、何も挿入されず、文字が削除されます。   
  
## <a name="return-types"></a>戻り値の型  
 *character_expression* が、サポートされている文字データ型のいずれかの場合は、文字データが返されます。 *character_expression* が、サポートされているバイナリ データ型のいずれかの場合は、バイナリ データが返されます。  
  
## <a name="remarks"></a>解説  
 開始位置または長さが負の場合、または開始位置が 1 番目の文字列の長さを超える場合は NULL 文字列が返されます。 開始位置が 0 の場合は、NULL 値が返されます。 削除する長さが 1 番目の文字列より長い場合は、1 番目の文字列の最初の文字まで削除されます。  

結果値が、戻り値の型でサポートされている最大値より大きい場合は、エラーが発生します。  
  
## <a name="supplementary-characters-surrogate-pairs"></a>補助文字 (サロゲート ペア)  
 SC の照合順序を使用する場合、*character_expression* と *replaceWith_expression* の両方にサロゲート ペアを含めることができます。 length パラメーターでは、*character_expression* 内の各サロゲートは 1 文字としてカウントされます。  
  
## <a name="examples"></a>例  
 次の例では、最初の文字列 (`abcdef`) の位置 `2` (`b`) から 3 文字を削除し、その位置に 2 番目に指定した文字列を挿入して生成される文字列を返します。  
  
```sql  
SELECT STUFF('abcdef', 2, 3, 'ijklmn');  
GO  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
---------   
aijklmnef   
  
(1 row(s) affected)  
```  
  
## <a name="see-also"></a>参照  
 [CONCAT &#40;Transact-SQL&#41;](../../t-sql/functions/concat-transact-sql.md)  
 [CONCAT_WS &#40;Transact-SQL&#41;](../../t-sql/functions/concat-ws-transact-sql.md)  
 [FORMATMESSAGE &#40;Transact-SQL&#41;](../../t-sql/functions/formatmessage-transact-sql.md)  
 [QUOTENAME &#40;Transact-SQL&#41;](../../t-sql/functions/quotename-transact-sql.md)  
 [REPLACE &#40;Transact-SQL&#41;](../../t-sql/functions/replace-transact-sql.md)  
 [REVERSE &#40;Transact-SQL&#41;](../../t-sql/functions/reverse-transact-sql.md)  
 [STRING_AGG &#40;Transact-SQL&#41;](../../t-sql/functions/string-agg-transact-sql.md)  
 [STRING_ESCAPE &#40;Transact-SQL&#41;](../../t-sql/functions/string-escape-transact-sql.md)  
 [TRANSLATE &#40;Transact-SQL&#41;](../../t-sql/functions/translate-transact-sql.md)  
 [データ型 &#40;Transact-SQL&#41;](../../t-sql/data-types/data-types-transact-sql.md)   
 [文字列関数 &#40;Transact-SQL&#41;](../../t-sql/functions/string-functions-transact-sql.md)  
