---
description: CURRENT_TIMESTAMP (Transact-SQL)
title: CURRENT_TIMESTAMP (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 07/24/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- CURRENT_TIMESTAMP
- CURRENT_TIMESTAMP_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- dates [SQL Server], functions
- niladic functions
- current date and time [SQL Server]
- time [SQL Server], current
- date and time [SQL Server], CURRENT_TIMESTAMP
- functions [SQL Server], time
- system date and time [SQL Server]
- system time [SQL Server]
- functions [SQL Server], date and time
- time [SQL Server], functions
- dates [SQL Server], current date and time
- dates [SQL Server], system date and time
- CURRENT_TIMESTAMP function [SQL Server]
- time [SQL Server], system
ms.assetid: c724d9cc-7b1f-4c71-bdf5-08bc52b33afc
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 594cf2cf8893891deeca89f1f56c76ee1b946caf
ms.sourcegitcommit: 2f971c85d87623c0aed1612406130d840e7bdb2e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "105744528"
---
# <a name="current_timestamp-transact-sql"></a>CURRENT_TIMESTAMP (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

この関数は、現在のデータベース システム タイムスタンプを **datetime** 値として、データベースのタイム ゾーン オフセットなしで返します。 `CURRENT_TIMESTAMP` は、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスが実行されているコンピューターのオペレーティング システムからこの値を派生します。
  
> [!NOTE]  
>  `SYSDATETIME` と `SYSUTCDATE` の場合、1 秒未満の秒の有効桁数で測定され、`GETDATE` や `GETUTCDATE` より精度が高くなります。 `SYSDATETIMEOFFSET` 関数には、システムのタイム ゾーン オフセットが含まれます。 日付と時刻のあらゆる型の変数に `SYSDATETIME`、`SYSUTCDATETIME`、`SYSDATETIMEOFFSET` を割り当てることができます。  
  
この関数には、等価な ANSI SQL [GETDATE](../../t-sql/functions/getdate-transact-sql.md)です。
  
[!INCLUDE[tsql](../../includes/tsql-md.md)] の日付と時刻のデータ型と関数については、[日付と時刻のデータ型と関数](../../t-sql/functions/date-and-time-data-types-and-functions-transact-sql.md)に関するページで概要をご覧ください。
  
![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)
  
## <a name="syntax"></a>構文  
  
```syntaxsql
CURRENT_TIMESTAMP  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
この関数は引数を取りません。
  
## <a name="return-type"></a>戻り値の型  
**datetime**
  
## <a name="remarks"></a>解説  
**datetime** 式を参照できる場所であれば、[!INCLUDE[tsql](../../includes/tsql-md.md)] ステートメントは `CURRENT_TIMESTAMP` を参照できます。
  
`CURRENT_TIMESTAMP` は非決定論的関数です。 この列を参照するビューと式には、インデックスを付けることができません。
  
## <a name="examples"></a>例  
これらの例では、現在の日付値と時刻値を返す 6 つの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] システム関数を使用し、日付、時刻、あるいはその両方を返します。 これらの例では、値が順番に返されるため、秒の小数部が異なることがあります。 返される実際の値では実行の実際の日/時間が反映されます。
  
### <a name="a-get-the-current-system-date-and-time"></a>A. 現在のシステム日付と時刻を取得する  
  
```sql
SELECT SYSDATETIME()  
    ,SYSDATETIMEOFFSET()  
    ,SYSUTCDATETIME()  
    ,CURRENT_TIMESTAMP  
    ,GETDATE()  
    ,GETUTCDATE();  
/* Returned:  
SYSDATETIME()      2007-04-30 13:10:02.0474381  
SYSDATETIMEOFFSET()2007-04-30 13:10:02.0474381 -07:00  
SYSUTCDATETIME()   2007-04-30 20:10:02.0474381  
CURRENT_TIMESTAMP  2007-04-30 13:10:02.047  
GETDATE()          2007-04-30 13:10:02.047  
GETUTCDATE()       2007-04-30 20:10:02.047  
*/
```  
  
### <a name="b-get-the-current-system-date"></a>B. 現在のシステム日付を取得する  
  
```sql
SELECT CONVERT (DATE, SYSDATETIME())  
    ,CONVERT (DATE, SYSDATETIMEOFFSET())  
    ,CONVERT (DATE, SYSUTCDATETIME())  
    ,CONVERT (DATE, CURRENT_TIMESTAMP)  
    ,CONVERT (DATE, GETDATE())  
    ,CONVERT (DATE, GETUTCDATE());  
  
/* Returned   
SYSDATETIME()      2007-05-03  
SYSDATETIMEOFFSET()2007-05-03  
SYSUTCDATETIME()   2007-05-04  
CURRENT_TIMESTAMP  2007-05-03  
GETDATE()          2007-05-03  
GETUTCDATE()       2007-05-04  
*/  
```  
  
### <a name="c-get-the-current-system-time"></a>C. C. 現在のシステム時刻を取得する  
  
```sql
SELECT CONVERT (TIME, SYSDATETIME())  
    ,CONVERT (TIME, SYSDATETIMEOFFSET())  
    ,CONVERT (TIME, SYSUTCDATETIME())  
    ,CONVERT (TIME, CURRENT_TIMESTAMP)  
    ,CONVERT (TIME, GETDATE())  
    ,CONVERT (TIME, GETUTCDATE());  
  
/* Returned  
SYSDATETIME()      13:18:45.3490361  
SYSDATETIMEOFFSET()13:18:45.3490361  
SYSUTCDATETIME()   20:18:45.3490361  
CURRENT_TIMESTAMP  13:18:45.3470000  
GETDATE()          13:18:45.3470000  
GETUTCDATE()       20:18:45.3470000  
*/  
```  
  
## <a name="examples-sssdwfull-and-sspdw"></a>例: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)]、[!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
```sql
SELECT CURRENT_TIMESTAMP;  
```  
  
## <a name="see-also"></a>関連項目
[CAST および CONVERT &#40;Transact-SQL&#41;](../../t-sql/functions/cast-and-convert-transact-sql.md)
  
  

