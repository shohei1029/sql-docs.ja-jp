---
description: TODATETIMEOFFSET (Transact-SQL)
title: TODATETIMEOFFSET (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 04/22/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- TO_DATETIMEOFFSET_TSQL
- SWITCH_TZ_TSQL
- SWITCH_TZ
- TO_DATETIMEOFFSET
dev_langs:
- TSQL
helpviewer_keywords:
- date and time [SQL Server], TODATETIMEOFFSET
- TODATETIMEOFFSET function
- functions [SQL Server], time
- functions [SQL Server], date and time
- time [SQL Server], functions
ms.assetid: b5fafc08-efd4-4a3b-a0b3-068981a0a685
author: julieMSFT
ms.author: jrasnick
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: edd9789d9aecc31b8f8e2dcf5682b08612df124b
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104739012"
---
# <a name="todatetimeoffset-transact-sql"></a>TODATETIMEOFFSET (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  返します、 **datetimeoffset** 値から変換されたです、 **datetime2** 式です。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql
TODATETIMEOFFSET ( datetime_expression , timezoneoffset_expression )  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
 *datetime_expression*  
 [datetime2](../../t-sql/data-types/datetime2-transact-sql.md) 値に解決される[式](../../t-sql/language-elements/expressions-transact-sql.md)です。  
  
> [!NOTE]  
>  型の式をすることはできません **テキスト**, 、**ntext**, 、または **イメージ** に、これらの型を暗黙的に変換できないため **varchar** または **nvarchar** です。  
  
 *timezoneoffset_expression*  
 タイム ゾーン オフセットを表す式です。たとえば、分単位で表す式 (整数の場合) は -120、時間と分単位で表す式 (文字列の場合) は '+13:00' です。 範囲は +14 ～ -14 (時間) です。 式は、指定された timezoneoffset_expression のローカル時刻で解釈されます。  

  
> [!NOTE]  
>  式が文字列の場合、{+|-}TZH:THM の形式にする必要があります。  
  
## <a name="return-type"></a>戻り値の型  
 **datetimeoffset**. 小数部の有効桁数が同じ *datetime_expression* 引数。  
  
## <a name="examples"></a>例  
  
### <a name="a-changing-the-time-zone-offset-of-the-current-date-and-time"></a>A. 現在の日付と時刻のタイム ゾーン オフセットを変更する  
 次の例では、現在の日付と時刻のゾーン オフセットを、タイム ゾーン `-07:00` に変更します。  
  
```sql  
DECLARE @todaysDateTime DATETIME2;  
SET @todaysDateTime = GETDATE();  
SELECT TODATETIMEOFFSET (@todaysDateTime, '-07:00');  
-- RETURNS 2019-04-22 16:23:51.7666667 -07:00  
```  
  
### <a name="b-changing-the-time-zone-offset-in-minutes"></a>B. タイム ゾーン オフセットを分単位で変更する  
 次の例では、現在のタイム ゾーンを `-120` 分に変更します。  
  
```sql  
SELECT TODATETIMEOFFSET(SYSDATETIME(), -120)
-- RETURNS: 2019-04-22 11:39:21.6986813 -02:00  
```  
  
### <a name="c-adding-a-13-hour-time-zone-offset"></a>C. 13 時間のタイム ゾーン オフセットを追加する  
 次の例では、日付と時刻に 13 時間のタイム ゾーン オフセットを追加します。  
  
```sql  
SELECT TODATETIMEOFFSET(SYSDATETIME(), '+13:00')
-- RETURNS: 2019-04-22 11:39:29.0339301 +13:00
```  
  
## <a name="see-also"></a>関連項目  
 [CAST および CONVERT &#40;Transact-SQL&#41;](../../t-sql/functions/cast-and-convert-transact-sql.md)   
 [日付と時刻のデータ型および関数 (Transact-SQL)](../../t-sql/functions/date-and-time-data-types-and-functions-transact-sql.md)   
 [AT TIME ZONE &#40;Transact-SQL&#41;](../../t-sql/queries/at-time-zone-transact-sql.md)  
  
  

