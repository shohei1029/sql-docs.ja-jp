---
description: SYSDATETIMEOFFSET (Transact-SQL)
title: SYSDATETIMEOFFSET (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- SYSDATETIMEOFFSET_TSQL
- SYSDATETIMEOFFSET
dev_langs:
- TSQL
helpviewer_keywords:
- date and time [SQL Server], SYSDATETIMEOFFSET
- dates [SQL Server], functions
- current date and time [SQL Server]
- functions [SQL Server], time
- system date and time [SQL Server]
- system time [SQL Server]
- SYSDATETIMEOFFSET function [SQL Server]
- functions [SQL Server], date and time
- time [SQL Server], functions
- dates [SQL Server], current date and time
- dates [SQL Server], system date and time
- time zones [SQL Server]
- time [SQL Server], system
ms.assetid: 8423c753-cebe-4edd-871d-0138e092199f
author: julieMSFT
ms.author: jrasnick
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: a9112864a873712e176d3c68fc17854e99df5a54
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104747922"
---
# <a name="sysdatetimeoffset-transact-sql"></a>SYSDATETIMEOFFSET (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスを実行しているコンピューターの日付と時刻を含む **datetimeoffset(7)** 値を返します。 タイム ゾーン オフセットが含まれます。  
  
 すべての [!INCLUDE[tsql](../../includes/tsql-md.md)] 日付および時刻のデータ型と関数の概要については、「[日付と時刻のデータ型および関数 &#40;Transact-SQL&#41;](../../t-sql/functions/date-and-time-data-types-and-functions-transact-sql.md)」を参照してください。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql
SYSDATETIMEOFFSET ( )  
```  

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="return-type"></a>戻り値の型  
 **datetimeoffset(7)**  
  
## <a name="remarks"></a>解説  
 [!INCLUDE[tsql](../../includes/tsql-md.md)] ステートメントでは、**datetimeoffset** 式を参照できる場所であればどこでも、SYSDATETIMEOFFSET を参照できます。  
  
 SYSDATETIMEOFFSET は非決定的関数です。 この関数を列内で参照するビューと式には、インデックスを付けることができません。  
  
> [!NOTE]  
>  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] は、GetSystemTimeAsFileTime() Windows API を使用して日付と時刻の値を取得します。 精度は、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスが実行されているコンピューター ハードウェアおよび Windows のバージョンによって異なります。 この API の精度は 100 ナノ秒で固定されます。 精度は、GetSystemTimeAdjustment() Windows API を使用して確認できます。  
  
## <a name="examples"></a>例  
 次の例では、現在の日付と時刻を返す 6 つの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] システム関数を使用して、日付、時刻、またはその両方を取得しています。 値は順番に返されるため、秒の小数部が異なる可能性があります。  
  
### <a name="a-showing-the-formats-that-are-returned-by-the-date-and-time-functions"></a>A. 日付および時刻の関数から返される形式を表示する  
 次の例では、日付と時刻関数から返されるさまざまな形式を示します。  
  
```sql
SELECT SYSDATETIME() AS [SYSDATETIME()]  
    ,SYSDATETIMEOFFSET() AS [SYSDATETIMEOFFSET()]  
    ,SYSUTCDATETIME() AS [SYSUTCDATETIME()]  
    ,CURRENT_TIMESTAMP AS [CURRENT_TIMESTAMP]  
    ,GETDATE() AS [GETDATE()]  
    ,GETUTCDATE() AS [GETUTCDATE()];  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 ```
SYSDATETIME()      2007-04-30 13:10:02.0474381
SYSDATETIMEOFFSET()2007-04-30 13:10:02.0474381 -07:00
SYSUTCDATETIME()   2007-04-30 20:10:02.0474381
CURRENT_TIMESTAMP  2007-04-30 13:10:02.047
GETDATE()          2007-04-30 13:10:02.047
GETUTCDATE()       2007-04-30 20:10:02.047
```  
  
### <a name="b-converting-date-and-time-to-date"></a>B. 日付と時刻を日付に変換する  
 次の例では、日付と時刻の値を `date` に変換する方法を示します。  
  
```sql
SELECT CONVERT (date, SYSDATETIME())  
    ,CONVERT (date, SYSDATETIMEOFFSET())  
    ,CONVERT (date, SYSUTCDATETIME())  
    ,CONVERT (date, CURRENT_TIMESTAMP)  
    ,CONVERT (date, GETDATE())  
    ,CONVERT (date, GETUTCDATE());  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 ```
2007-04-30
2007-04-30
2007-04-30
2007-04-30
2007-04-30
2007-04-30
```  
  
### <a name="c-converting-date-and-time-to-times"></a>C. 日付と時刻を時刻に変換する  
 次の例では、日付と時刻の値を `time` に変換する方法を示します。  
  
```sql
SELECT CONVERT (time, SYSDATETIME()) AS [SYSDATETIME()]  
    ,CONVERT (time, SYSDATETIMEOFFSET()) AS [SYSDATETIMEOFFSET()]  
    ,CONVERT (time, SYSUTCDATETIME()) AS [SYSUTCDATETIME()]  
    ,CONVERT (time, CURRENT_TIMESTAMP) AS [CURRENT_TIMESTAMP]  
    ,CONVERT (time, GETDATE()) AS [GETDATE()]  
    ,CONVERT (time, GETUTCDATE()) AS [GETUTCDATE()];  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 ```
SYSDATETIME()      13:18:45.3490361
SYSDATETIMEOFFSET()13:18:45.3490361
SYSUTCDATETIME()   20:18:45.3490361
CURRENT_TIMESTAMP  13:18:45.3470000
GETDATE()          13:18:45.3470000
GETUTCDATE()       20:18:45.3470000
```  
  
## <a name="see-also"></a>関連項目  
 [CAST および CONVERT &#40;Transact-SQL&#41;](../../t-sql/functions/cast-and-convert-transact-sql.md)   
 [日付と時刻のデータ型および関数 &#40;Transact-SQL&#41;](../../t-sql/functions/date-and-time-data-types-and-functions-transact-sql.md)  
  
  

