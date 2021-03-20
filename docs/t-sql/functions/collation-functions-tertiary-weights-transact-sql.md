---
description: 照合順序関数 - TERTIARY_WEIGHTS (Transact-SQL)
title: TERTIARY_WEIGHTS (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 07/24/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- TERTIARY_WEIGHTS_TSQL
- TERTIARY_WEIGHTS
dev_langs:
- TSQL
helpviewer_keywords:
- weights [SQL Server]
- SQL tertiary collations
- TERTIARY_WEIGHTS function
ms.assetid: 7e1f5350-260b-4c61-8c84-69bb1a214f1f
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 0996a8dc6daa42b85153adc5ca44ef431e8ecdb6
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104753462"
---
# <a name="collation-functions---tertiary_weights-transact-sql"></a>照合順序関数 - TERTIARY_WEIGHTS (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

この関数は、第 3 の SQL 照合順序で定義された、Unicode 以外の文字列式内の文字ごとに、バイナリ文字列の重みを返します。
  
![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)
  
## <a name="syntax"></a>構文  
  
```syntaxsql
TERTIARY_WEIGHTS( non_Unicode_character_string_expression )  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
*non_Unicode_character_string_expression*  
第 3 の SQL 照合順序で定義される **char**、**varchar**、または **varchar(max)** 型の文字列 [式](../../t-sql/language-elements/expressions-transact-sql.md) です。 これらの照合順序の一覧については、「解説」を参照してください。
  
## <a name="return-types"></a>戻り値の型
`TERTIARY_WEIGHTS` は、*non_Unicode_character_string_expression* が **char** または **varchar** であるときに **varbinary** を返し、*non_Unicode_character_string_expression* に **varchar(max)** データ型が含まれるときには **varbinary(max)** を返します。
  
## <a name="remarks"></a>解説  
第 3 の SQL 照合順序で *non_Unicode_character_string_expression* が定義されていない場合、`TERTIARY_WEIGHTS` は NULL を返します。 次の表に、第 3 の SQL 照合順序を示します。
  
|並べ替え順 ID|SQL 照合順序|  
|---|---|
|33|SQL_Latin1_General_Pref_CP437_CI_AS|  
|34|SQL_Latin1_General_CP437_CI_AI|  
|43|SQL_Latin1_General_Pref_CP850_CI_AS|  
|44|SQL_Latin1_General_CP850_CI_AI|  
|49|SQL_1xCompat_CP850_CI_AS|  
|53|SQL_Latin1_General_Pref_CP1_CI_AS|  
|54|SQL_Latin1_General_CP1_CI_AI|  
|56|SQL_AltDiction_Pref_CP850_CI_AS|  
|57|SQL_AltDiction_CP850_CI_AI|  
|58|SQL_Scandinavian_Pref_CP850_CI_AS|  
|82|SQL_Latin1_General_CP1250_CI_AS|  
|84|SQL_Czech_CP1250_CI_AS|  
|86|SQL_Hungarian_CP1250_CI_AS|  
|88|SQL_Polish_CP1250_CI_AS|  
|90|SQL_Romanian_CP1250_CI_AS|  
|92|SQL_Croatian_CP1250_CI_AS|  
|94|SQL_Slovak_CP1250_CI_AS|  
|96|SQL_Slovenian_CP1250_CI_AS|  
|106|SQL_Latin1_General_CP1251_CI_AS|  
|108|SQL_Ukrainian_CP1251_CI_AS|  
|113|SQL_Latin1_General_CP1253_CS_AS|  
|114|SQL_Latin1_General_CP1253_CI_AS|  
|130|SQL_Latin1_General_CP1254_CI_AS|  
|146|SQL_Latin1_General_CP1256_CI_AS|  
|154|SQL_Latin1_General_CP1257_CI_AS|  
|156|SQL_Estonian_CP1257_CI_AS|  
|158|SQL_Latvian_CP1257_CI_AS|  
|160|SQL_Lithuanian_CP1257_CI_AS|  
|183|SQL_Danish_Pref_CP1_CI_AS|  
|184|SQL_SwedishPhone_Pref_CP1_CI_AS|  
|185|SQL_SwedishStd_Pref_CP1_CI_AS|  
|186|SQL_Icelandic_Pref_CP1_CI_AS|  
  
**char**、**varchar**、または **varchar (max)** 列の値で定義されている計算列の定義に対して `TERTIARY_WEIGHTS` を使用します。 クエリの ORDER BY 句で **char**、**varchar**、または **varchar(max)** 列を指定する場合は、計算列と、**char**、**varchar**、または **varchar(max)** 列の両方に対するインデックス定義によって、パフォーマンスを向上させることができます。
  
## <a name="examples"></a>例  
この例では、`TERTIARY_WEIGHTS` 関数を `char` 列の値に適用するテーブルに計算列を作成します。
  
```sql
CREATE TABLE TertColTable  
(Col1 char(15) COLLATE SQL_Latin1_General_Pref_CP437_CI_AS,  
Col2 AS TERTIARY_WEIGHTS(Col1));  
GO   
```  
  
## <a name="see-also"></a>関連項目
[ORDER BY 句 &#40;Transact-SQL&#41;](../../t-sql/queries/select-order-by-clause-transact-sql.md)
  
  
