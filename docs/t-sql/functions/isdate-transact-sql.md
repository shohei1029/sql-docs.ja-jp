---
description: ISDATE (Transact-SQL)
title: ISDATE (Transact-SQL)
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- ISDATETIME
- ISDATE_TSQL
- ISDATE
- ISDATETIME_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- dates [SQL Server], functions
- date and time [SQL Server], ISDATE
- validate dates times [SQL Server]
- formats [SQL Server], time
- dates [SQL Server], validate
- verify dates times [SQL Server]
- functions [SQL Server], time
- formats [SQL Server], dates
- functions [SQL Server], date and time
- time [SQL Server], functions
- time [SQL Server], validate
- ISDATE function [SQL Server]
ms.assetid: 8e2c9ee7-388a-432f-b2c9-7b398f26bf85
author: cawrites
ms.author: chadam
ms.reviewer: ''
ms.custom: ''
ms.date: 03/14/2017
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: b4e5166cba81e1fd1b7d08c9de742e5c7088a7c3
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104746812"
---
# <a name="isdate-transact-sql"></a>ISDATE (Transact-SQL)

[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  *expression* が有効な **date**、**time**、または **datetime** 値の場合は 1 を返し、それ以外の場合は 0 を返します。  
  
 *expression* が **datetime2** 値の場合、ISDATE は 0 を返します。  
  
 すべての [!INCLUDE[tsql](../../includes/tsql-md.md)] 日付および時刻のデータ型と関数の概要については、「[日付と時刻のデータ型および関数 &#40;Transact-SQL&#41;](../../t-sql/functions/date-and-time-data-types-and-functions-transact-sql.md)」を参照してください。 datetime データの範囲は 1753-01-01 から 9999-12-31、date データの範囲は 0001-01-01 から 9999-12-31 です。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql
ISDATE ( expression )
```

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
 *式 (expression)*  
 文字列、または文字列に変換できる[式](../../t-sql/language-elements/expressions-transact-sql.md)です。 式は 4,000 文字未満にする必要があります。 datetime および smalldatetime を除く日付および時刻データ型は、ISDATE の引数として使用できません。  
  
## <a name="return-type"></a>戻り値の型  
 **int**  
  
## <a name="remarks"></a>解説  
 ISDATE は、[CONVERT](../../t-sql/functions/cast-and-convert-transact-sql.md) 関数と共に使用され、CONVERT スタイル パラメーターが指定されており、スタイルが 0、100、9、または 109 と等しくない場合にのみ決定的関数になります。  
  
 ISDATE の戻り値は、[SET DATEFORMAT](../../t-sql/statements/set-dateformat-transact-sql.md)、[SET LANGUAGE](../../t-sql/statements/set-language-transact-sql.md)、および [default language サーバー構成オプションの構成](../../database-engine/configure-windows/configure-the-default-language-server-configuration-option.md)の設定に依存します。  
  
## <a name="isdate-expression-formats"></a>ISDATE 式の形式  
 ISDATE で 1 が返される有効な形式の例については、「[datetime](../../t-sql/data-types/datetime-transact-sql.md)」および「[smalldatetime](../../t-sql/data-types/smalldatetime-transact-sql.md)」の各トピックにある「datetime でサポートされる文字列リテラル形式」を参照してください。 その他の例については、「[CAST および CONVERT](../../t-sql/functions/cast-and-convert-transact-sql.md)」の「引数」セクションの入力/出力列を参照してください。  
  
 次の表は、無効な式の形式をまとめたものです。このような式を入力値として渡すと、0 またはエラーが返されます。  
  
|ISDATE 式|ISDATE 戻り値|  
|-----------------------|-------------------------|  
|NULL|0|  
|「[データ型](../../t-sql/data-types/data-types-transact-sql.md)」に列挙されているデータ型のうち、文字列、Unicode 文字列、日付/時刻以外のデータ型カテゴリの値。|0|  
|**text**、**ntext**、または **image** データ型の値。|0|  
|3 を超える秒の有効桁数のスケールを持つ任意の値 (.0000 から 0000000...n)。 ISDATE は、*expression* が **datetime2** 値の場合は 0 を返し、*expression* が有効な **datetime** 値の場合は 1 を返します。|0|  
|有効な日付と無効な値を組み合わせた任意の値 (1995-10-1a など)。|0|  
  
## <a name="examples"></a>例  
  
### <a name="a-using-isdate-to-test-for-a-valid-datetime-expression"></a>A. ISDATE を使用して datetime 式が有効かどうかをテストする  
 次の例は、`ISDATE` を使用して、文字列が有効な **datetime** かどうかをテストする方法を示しています。  
  
```sql  
IF ISDATE('2009-05-12 10:19:41.177') = 1  
    PRINT 'VALID'  
ELSE  
    PRINT 'INVALID';  
```  
  
### <a name="b-showing-the-effects-of-the-set-dateformat-and-set-language-settings-on-return-values"></a>B. SET DATEFORMAT と SET LANGUAGE 設定が戻り値に与える影響を確認する  
 次のステートメントでは、`SET DATEFORMAT` および `SET LANGUAGE` の各種設定と、その結果として返される値の関係を示しています。  
  
```sql  
/* Use these sessions settings. */  
SET LANGUAGE us_english;  
SET DATEFORMAT mdy;  
/* Expression in mdy dateformat */  
SELECT ISDATE('04/15/2008'); --Returns 1.  
/* Expression in mdy dateformat */  
SELECT ISDATE('04-15-2008'); --Returns 1.   
/* Expression in mdy dateformat */  
SELECT ISDATE('04.15.2008'); --Returns 1.   
/* Expression in myd  dateformat */  
SELECT ISDATE('04/2008/15'); --Returns 1.  
  
SET DATEFORMAT mdy;  
SELECT ISDATE('15/04/2008'); --Returns 0.  
SET DATEFORMAT mdy;  
SELECT ISDATE('15/2008/04'); --Returns 0.  
SET DATEFORMAT mdy;  
SELECT ISDATE('2008/15/04'); --Returns 0.  
SET DATEFORMAT mdy;  
SELECT ISDATE('2008/04/15'); --Returns 1.  
  
SET DATEFORMAT dmy;  
SELECT ISDATE('15/04/2008'); --Returns 1.  
SET DATEFORMAT dym;  
SELECT ISDATE('15/2008/04'); --Returns 1.  
SET DATEFORMAT ydm;  
SELECT ISDATE('2008/15/04'); --Returns 1.  
SET DATEFORMAT ymd;  
SELECT ISDATE('2008/04/15'); --Returns 1.  
  
SET LANGUAGE English;  
SELECT ISDATE('15/04/2008'); --Returns 0.  
SET LANGUAGE Hungarian;  
SELECT ISDATE('15/2008/04'); --Returns 0.  
SET LANGUAGE Swedish;  
SELECT ISDATE('2008/15/04'); --Returns 0.  
SET LANGUAGE Italian;  
SELECT ISDATE('2008/04/15'); --Returns 1.  
  
/* Return to these sessions settings. */  
SET LANGUAGE us_english;  
SET DATEFORMAT mdy;  
```  
  
## <a name="examples-sssdwfull-and-sspdw"></a>例: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)]、[!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="c-using-isdate-to-test-for-a-valid-datetime-expression"></a>C. ISDATE を使用して datetime 式が有効かどうかをテストする  
 次の例は、`ISDATE` を使用して、文字列が有効な **datetime** かどうかをテストする方法を示しています。  
  
```sql  
IF ISDATE('2009-05-12 10:19:41.177') = 1  
    SELECT 'VALID';  
ELSE  
    SELECT 'INVALID';  
```  
  
## <a name="see-also"></a>参照  
 [CAST および CONVERT &#40;Transact-SQL&#41;](../../t-sql/functions/cast-and-convert-transact-sql.md)
