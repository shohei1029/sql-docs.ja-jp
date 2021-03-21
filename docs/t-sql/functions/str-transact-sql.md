---
description: STR (Transact-SQL)
title: STR (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/16/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- STR
- STR_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- converting numbers to characters
- characters [SQL Server], converting
- character data [SQL Server]
- STR function
ms.assetid: de03531b-d9e7-4c3c-9604-14e582ac20c6
author: julieMSFT
ms.author: jrasnick
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 1151dd72f4f1ccf12ed38d39143a79d4180fc70a
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104753422"
---
# <a name="str-transact-sql"></a>STR (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  数値データから変換された文字データを返します。 文字データは右揃えで、長さと小数点以下の精度が指定されています。 
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql
STR ( float_expression [ , length [ , decimal ] ] )  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
 *float_expression*  
 小数点付きの概数型 (**float**) の式を指定します。  
  
 *length*  
 全体の長さを指定します。 これには小数点、符号、数字、空白文字も含まれます。 既定値は 10 です。  
  
 *decimal*  
 小数点以下の桁数を指定します。 *decimal* は 16 以下である必要があります。 *decimal* に 16 を超える値を指定した場合、結果は小数点以下 16 桁に切り捨てられます。  
  
## <a name="return-types"></a>戻り値の型  
 **varchar**  
  
## <a name="remarks"></a>解説  
 STR で *length* パラメーターと *decimal* パラメーターを指定する場合は、正の値を指定する必要があります。 数値は、既定では整数に丸められます。小数点以下桁数に 0 が指定された場合も同様です。 指定する長さは、その数字の小数点より前の桁数に、符号があればその符号部分を加えた数以上にする必要があります。 短い *float_expression* は、指定された長さで右揃えされ、長い *float_expression* は、指定された小数点以下桁数に切り捨てられます。 たとえば、STR(12, 10) の結果は 12 になります。 これは結果セットの中で右揃えされます。 一方、STR(1223, 2) と指定すると、結果は切り捨てられて \*\* になります。 文字列関数は入れ子にすることができます。  
  
> [!NOTE]  
>  Unicode データに変換するには、CONVERT または [CAST](../../t-sql/functions/cast-and-convert-transact-sql.md) 変換関数の中で STR を使用します。  
  
## <a name="examples"></a>例  
 次の例では、5 つの数字と小数点で構成される式を、6 桁の文字列に変換します。 値の小数部は、小数点以下 1 桁になるように丸められます。  
  
```sql
SELECT STR(123.45, 6, 1);  
GO  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
------  
 123.5  
  
(1 row(s) affected)  
```  
  
 式が指定した長さを超えた場合、STR では `**` が指定の長さだけ返されます。  
  
```sql
SELECT STR(123.45, 2, 2);  
GO  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
--  
**  
  
(1 row(s) affected)  
```  
  
 `STR` の中で数値データが入れ子にされていても、結果は指定した形式の文字データになります。  
  
```sql
SELECT STR (FLOOR (123.45), 8, 3);
GO  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
--------  
 123.000  
  
(1 row(s) affected)  
```  
  
## <a name="see-also"></a>参照  
 [CAST および CONVERT &#40;Transact-SQL&#41;](../../t-sql/functions/cast-and-convert-transact-sql.md)  
 [FORMAT &#40;Transact-SQL&#41;](../../t-sql/functions/format-transact-sql.md)  
 [文字列関数 &#40;Transact-SQL&#41;](../../t-sql/functions/string-functions-transact-sql.md)  
  
  

