---
description: GETUTCDATE (Transact-SQL)
title: GETUTCDATE (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 12/02/2015
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- GETUTCDATE
- GETUTCDATE_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- time [SQL Server], UTC time
- dates [SQL Server], functions
- UTC time [SQL Server]
- date and time [SQL Server], GETUTCDATE
- Universal Time Coordinate [SQL Server]
- GETUTCDATE function [SQL Server]
- current date and time [SQL Server]
- time [SQL Server], current
- Greenwich Mean Time [SQL Server]
- functions [SQL Server], time
- system date and time [SQL Server]
- current UTC date and time [SQL Server]
- system time [SQL Server]
- functions [SQL Server], date and time
- time [SQL Server], functions
- dates [SQL Server], current date and time
- dates [SQL Server], system date and time
- time [SQL Server], system
ms.assetid: 48a5b230-102e-4a89-bb2a-fcf0cac862bb
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 41e757c8a3f46b12cbadde80f43f5d1ea004c3ac
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104747113"
---
# <a name="getutcdate-transact-sql"></a>GETUTCDATE (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  として現在のデータベース システムのタイムスタンプを返す、 **datetime** 値。 データベースのタイム ゾーン オフセットは含まれません。 この値は、現在の UTC 時 (協定世界時) を表します。 この値は、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスが実行されているコンピューターのオペレーティング システムから取得されます。  
  
> [!NOTE]  
>  1 秒未満の有効桁数で比較すると、SYSDATETIME と SYSUTCDATETIME の方が GETDATE と GETUTCDATE よりも高い精度を得ることができます。 SYSDATETIMEOFFSET には、システムのタイム ゾーン オフセットが含まれます。 SYSDATETIME、SYSUTCDATETIME、および SYSDATETIMEOFFSET は、date 型と time 型の任意の変数に割り当てることができます。  
  
 すべての [!INCLUDE[tsql](../../includes/tsql-md.md)] 日付および時刻のデータ型と関数の概要については、「[日付と時刻のデータ型および関数 &#40;Transact-SQL&#41;](../../t-sql/functions/date-and-time-data-types-and-functions-transact-sql.md)」を参照してください。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql  
GETUTCDATE()  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="return-types"></a>戻り値の型
 **datetime**  
  
## <a name="remarks"></a>解説  
 [!INCLUDE[tsql](../../includes/tsql-md.md)] ステートメントを参照できます GETUTCDATE を参照できる任意の場所、 **datetime** 式です。  
  
 GETUTCDATE は、非決定的関数です。 この関数を列内で参照するビューと式には、インデックスを付けることができません。  
  
## <a name="examples"></a>例  
 次の例では、現在の日付と時刻を返す 6 つの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] システム関数を使用して、日付、時刻、またはその両方を取得しています。 値は順番に返されるため、秒の小数部が異なる可能性があります。  
  
### <a name="a-getting-the-current-system-date-and-time"></a>A. 現在のシステム日付と時刻を取得する  
  
```sql  
SELECT 'SYSDATETIME()      ', SYSDATETIME();  
SELECT 'SYSDATETIMEOFFSET()', SYSDATETIMEOFFSET();  
SELECT 'SYSUTCDATETIME()   ', SYSUTCDATETIME();  
SELECT 'CURRENT_TIMESTAMP  ', CURRENT_TIMESTAMP;  
SELECT 'GETDATE()          ', GETDATE();  
SELECT 'GETUTCDATE()       ', GETUTCDATE();  
/* Returned:  
SYSDATETIME()            2007-05-03 18:34:11.9351421  
SYSDATETIMEOFFSET()      2007-05-03 18:34:11.9351421 -07:00  
SYSUTCDATETIME()         2007-05-04 01:34:11.9351421  
CURRENT_TIMESTAMP        2007-05-03 18:34:11.933  
GETDATE()                2007-05-03 18:34:11.933  
GETUTCDATE()             2007-05-04 01:34:11.933  
*/  
```  
  
### <a name="b-getting-the-current-system-date"></a>B. 現在のシステム日付を取得する  
  
```  
SELECT 'SYSDATETIME()      ', CONVERT (date, SYSDATETIME());  
SELECT 'SYSDATETIMEOFFSET()', CONVERT (date, SYSDATETIMEOFFSET());  
SELECT 'SYSUTCDATETIME()   ', CONVERT (date, SYSUTCDATETIME());  
SELECT 'CURRENT_TIMESTAMP  ', CONVERT (date, CURRENT_TIMESTAMP);  
SELECT 'GETDATE()          ', CONVERT (date, GETDATE());  
SELECT 'GETUTCDATE()       ', CONVERT (date, GETUTCDATE());  
  
/* Returned:   
SYSDATETIME()            2007-05-03  
SYSDATETIMEOFFSET()      2007-05-03  
SYSUTCDATETIME()         2007-05-04  
CURRENT_TIMESTAMP        2007-05-03  
GETDATE()                2007-05-03  
GETUTCDATE()             2007-05-04  
*/  
```  
  
### <a name="c-getting-the-current-system-time"></a>C. 現在のシステム時刻を取得する  
  
```sql  
SELECT 'SYSDATETIME()      ', CONVERT (time, SYSDATETIME());  
SELECT 'SYSDATETIMEOFFSET()', CONVERT (time, SYSDATETIMEOFFSET());  
SELECT 'SYSUTCDATETIME()   ', CONVERT (time, SYSUTCDATETIME());  
SELECT 'CURRENT_TIMESTAMP  ', CONVERT (time, CURRENT_TIMESTAMP);  
SELECT 'GETDATE()          ', CONVERT (time, GETDATE());  
SELECT 'GETUTCDATE()       ', CONVERT (time, GETUTCDATE());  
/* Returned  
SYSDATETIME()            18:25:01.6958841  
SYSDATETIMEOFFSET()      18:25:01.6958841  
SYSUTCDATETIME()         01:25:01.6958841  
CURRENT_TIMESTAMP        18:25:01.6930000  
GETDATE()                18:25:01.6930000  
GETUTCDATE()             01:25:01.6930000  
*/  
```  
  
## <a name="see-also"></a>参照  
 [CAST および CONVERT &#40;Transact-SQL&#41;](../../t-sql/functions/cast-and-convert-transact-sql.md)   
 [AT TIME ZONE &#40;Transact-SQL&#41;](../../t-sql/queries/at-time-zone-transact-sql.md)  
  
  


