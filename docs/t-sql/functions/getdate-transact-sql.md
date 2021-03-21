---
title: GETDATE (Transact-SQL) | Microsoft Docs
description: GETDATE 関数の Transact-SQL リファレンス。datetime 値として現在のデータベース システム時刻を返します。
ms.date: 09/07/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- GETDATE_TSQL
- GETDATE
dev_langs:
- TSQL
helpviewer_keywords:
- dates [SQL Server], functions
- GETDATE function [SQL Server]
- current date and time [SQL Server]
- time [SQL Server], current
- functions [SQL Server], time
- system date and time [SQL Server]
- system time [SQL Server]
- functions [SQL Server], date and time
- time [SQL Server], functions
- dates [SQL Server], current date and time
- date and time [SQL Server], GETDATE
- dates [SQL Server], system date and time
- time [SQL Server], system
ms.assetid: bebe3b65-2b3e-4c73-bf80-ff1132c680a7
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: e71aa8575e10483c0a2c2f2a8ec6e430f896c83e
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104747132"
---
# <a name="getdate-transact-sql"></a>GETDATE (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

として現在のデータベース システムのタイムスタンプを返す、 **datetime** 、データベース タイム ゾーン オフセットを持たない値。 この値は、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスが実行されているコンピューターのオペレーティング システムから取得されます。

> [!NOTE]
>  1 秒未満の有効桁数で比較すると、SYSDATETIME と SYSUTCDATETIME の方が GETDATE と GETUTCDATE よりも高い精度を得ることができます。 SYSDATETIMEOFFSET には、システムのタイム ゾーン オフセットが含まれます。 SYSDATETIME、SYSUTCDATETIME、および SYSDATETIMEOFFSET は、date 型と time 型の任意の変数に割り当てることができます。

Azure SQL Database (Azure SQL Managed Instance を除く) と Azure Synapse Analytics は UTC に従います。 UTC 以外のタイム ゾーンの日付と時刻情報を解釈する必要がある場合、Azure SQL Database または Azure Synapse Analytics で [AT TIME ZONE](../../t-sql/queries/at-time-zone-transact-sql.md) を使用します。

 すべての [!INCLUDE[tsql](../../includes/tsql-md.md)] 日付および時刻のデータ型と関数の概要については、「[日付と時刻のデータ型および関数 &#40;Transact-SQL&#41;](../../t-sql/functions/date-and-time-data-types-and-functions-transact-sql.md)」を参照してください。

 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)

## <a name="syntax"></a>構文

```syntaxsql
GETDATE()
```

## <a name="return-type"></a>戻り値の型
 **datetime**

## <a name="remarks"></a>解説
 [!INCLUDE[tsql](../../includes/tsql-md.md)] ステートメントを参照できます GETDATE を参照できる任意の場所、 **datetime** 式です。

 GETDATE は、非決定的関数です。 この関数を列内で参照するビューと式には、インデックスを付けることができません。

 GETDATE() 関数で SWITCHOFFSET を使用すると、クエリの実行速度が低下する場合があります。クエリ オプティマイザーでは、GETDATE 値の正確なカーディナリティの推定を取得できないためです。 GETDATE 値を事前計算して、次の例に示すように、クエリでその値を指定することをお勧めします。 さらに、OPTION (RECOMPILE) クエリ ヒントを使用して、次に同じクエリが実行されるときに、クエリ オプティマイザーでクエリ プランが強制的に再コンパイルされるようにします。 そうすると、オプティマイザーによって、GETDATE() の正確なカーディナリティの推定が取得され、より効率的なクエリ プランが生成されます。

```sql
DECLARE @dt datetimeoffset = switchoffset (CONVERT(datetimeoffset, GETDATE()), '-04:00');
SELECT * FROM t
WHERE c1 > @dt OPTION (RECOMPILE);
```

## <a name="examples"></a>例
 次の例では、現在の日付と時刻を返す 6 つの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] システム関数を使用して、日付、時刻、またはその両方を取得しています。 値は順番に返されるため、秒の小数部が異なる可能性があります。

### <a name="a-getting-the-current-system-date-and-time"></a>A. 現在のシステム日付と時刻を取得する

```sql
SELECT SYSDATETIME()
    ,SYSDATETIMEOFFSET()
    ,SYSUTCDATETIME()
    ,CURRENT_TIMESTAMP
    ,GETDATE()
    ,GETUTCDATE();
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

### <a name="b-getting-the-current-system-date"></a>B. 現在のシステム日付を取得する

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
SYSDATETIME()          2007-05-03
SYSDATETIMEOFFSET()    2007-05-03
SYSUTCDATETIME()       2007-05-04
CURRENT_TIMESTAMP      2007-05-03
GETDATE()              2007-05-03
GETUTCDATE()           2007-05-04
```

### <a name="c-getting-the-current-system-time"></a>C. 現在のシステム時刻を取得する

```sql
SELECT CONVERT (time, SYSDATETIME())
    ,CONVERT (time, SYSDATETIMEOFFSET())
    ,CONVERT (time, SYSUTCDATETIME())
    ,CONVERT (time, CURRENT_TIMESTAMP)
    ,CONVERT (time, GETDATE())
    ,CONVERT (time, GETUTCDATE());
```

 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]

```
SYSDATETIME()       13:18:45.3490361
SYSDATETIMEOFFSET() 13:18:45.3490361
SYSUTCDATETIME()    20:18:45.3490361
CURRENT_TIMESTAMP   13:18:45.3470000
GETDATE()           13:18:45.3470000
GETUTCDATE()        20:18:45.3470000
```

## <a name="examples-sssdwfull-and-sspdw"></a>例: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)]、[!INCLUDE[ssPDW](../../includes/sspdw-md.md)]
 次の例では、現在の日付と時刻を返す 3 つの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] システム関数を使用して、日付、時刻、またはその両方を取得しています。 値は順番に返されるため、秒の小数部が異なる可能性があります。

### <a name="d-getting-the-current-system-date-and-time"></a>D. 現在のシステム日付と時刻を取得する

```sql
SELECT SYSDATETIME()
    ,CURRENT_TIMESTAMP
    ,GETDATE();
```

### <a name="e-getting-the-current-system-date"></a>E. 現在のシステム日付を取得する

```sql
SELECT CONVERT (date, SYSDATETIME())
    ,CONVERT (date, CURRENT_TIMESTAMP)
    ,CONVERT (date, GETDATE());
```

### <a name="f-getting-the-current-system-time"></a>F. 現在のシステム時刻を取得する

```sql
SELECT CONVERT (time, SYSDATETIME())
    ,CONVERT (time, CURRENT_TIMESTAMP)
    ,CONVERT (time, GETDATE());
```

## <a name="see-also"></a>参照
 [CAST および CONVERT &#40;Transact-SQL&#41;](../../t-sql/functions/cast-and-convert-transact-sql.md)
