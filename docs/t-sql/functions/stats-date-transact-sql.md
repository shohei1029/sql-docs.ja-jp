---
description: STATS_DATE (Transact-SQL)
title: STATS_DATE (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 12/18/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- STATS_DATE_TSQL
- STATS_DATE
dev_langs:
- TSQL
helpviewer_keywords:
- statistical information [SQL Server], last time updated
- STATS_DATE function
- query optimization statistics [SQL Server], last time updated
- last time statistics updated
- stats update date
ms.assetid: f9ec3101-1e41-489d-b519-496a0d6089fb
author: julieMSFT
ms.author: jrasnick
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 49aba3a5d9162bc4b48bb2961ae13b17914a9854
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104748022"
---
# <a name="stats_date-transact-sql"></a>STATS_DATE (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  テーブルまたはインデックス付きビューの統計の最終更新日を返します。  
  
 統計の更新について詳しくは、「[統計](../../relational-databases/statistics/statistics.md)」をご覧ください。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql
STATS_DATE ( object_id , stats_id )  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
 *object_id*  
 統計を含むテーブルまたはインデックス付きビューの ID。  
  
 *stats_id*  
 統計オブジェクトの ID。  
  
## <a name="return-types"></a>戻り値の型  
 成功時に **datetime** を返します。 統計 BLOB が作成されていない場合は、**NULL** を返します。  
  
## <a name="remarks"></a>解説  
 システム関数は、選択リストの中、WHERE 句の中、また、式を使える所ならどこにでも使用できます。  
 
 統計の更新日付は、メタデータではなく[統計 BLOB オブジェクト](../../relational-databases/statistics/statistics.md#DefinitionQOStatistics)に[ヒストグラム](../../relational-databases/statistics/statistics.md#histogram)および[密度ベクトル](../../relational-databases/statistics/statistics.md#density)と共に格納されます。 統計データを生成するためのデータが読み取られていない場合、統計 BLOB は作成されず、日付は使用できません。 これは、述語が行を返さないフィルター選択された統計情報や、新しい空のテーブルの場合です。
 
 統計がインデックスに対応する場合、[sys.stats](../../relational-databases/system-catalog-views/sys-stats-transact-sql.md) カタログ ビューの *stats_id* の値は、[sys.indexes](../../relational-databases/system-catalog-views/sys-indexes-transact-sql.md) カタログ ビューの *index_id* の値と同一になります。
  
## <a name="permissions"></a>アクセス許可  
 db_owner 固定データベース ロールのメンバーシップか、テーブルまたはインデックス付きビューのメタデータを表示する権限が必要です。  
  
## <a name="examples"></a>例  
  
### <a name="a-return-the-dates-of-the-most-recent-statistics-for-a-table"></a>A.  テーブルの統計の最終更新日を返す  
 次の例では、`Person.Address` テーブルの各統計オブジェクトの最終更新日を返します。  
  
```sql  
USE AdventureWorks2012;  
GO  
SELECT name AS stats_name,   
    STATS_DATE(object_id, stats_id) AS statistics_update_date  
FROM sys.stats   
WHERE object_id = OBJECT_ID('Person.Address');  
GO  
```  
  
 統計がインデックスに対応する場合、[sys.stats](../../relational-databases/system-catalog-views/sys-stats-transact-sql.md) カタログ ビューの *stats_id* の値は、[sys.indexes](../../relational-databases/system-catalog-views/sys-indexes-transact-sql.md) カタログ ビューの *index_id* の値と同一になります。このため、次に示すクエリは上のクエリと同じ結果を返します。 統計がインデックスに対応しない場合、統計は sys.stats の結果には含まれますが、sys.indexes の結果には含まれません。  
  
```sql  
USE AdventureWorks2012;  
GO  
SELECT name AS index_name,   
    STATS_DATE(object_id, index_id) AS statistics_update_date  
FROM sys.indexes   
WHERE object_id = OBJECT_ID('Person.Address');  
GO  
```  
  
## <a name="examples-sssdwfull-and-sspdw"></a>例: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)]、[!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="b-learn-when-a-named-statistics-was-last-updated"></a>B. 指定された統計の最終更新日時を確認する  
 次の例では、DimCustomer テーブルの LastName 列に統計を作成します。 その後、統計情報の日付を表示するクエリを実行します。 さらに、統計を更新し、もう一度クエリを実行して更新された日付を表示します。  
  
```sql
--First, create a statistics object  
USE AdventureWorksPDW2012;  
GO  
CREATE STATISTICS Customer_LastName_Stats  
ON AdventureWorksPDW2012.dbo.DimCustomer (LastName)  
WITH SAMPLE 50 PERCENT;  
GO  
  
--Return the date when Customer_LastName_Stats was last updated  
USE AdventureWorksPDW2012;  
GO  
SELECT stats_id, name AS stats_name,   
    STATS_DATE(object_id, stats_id) AS statistics_date  
FROM sys.stats s  
WHERE s.object_id = OBJECT_ID('dbo.DimCustomer')  
    AND s.name = 'Customer_LastName_Stats';  
GO  
  
--Update Customer_LastName_Stats so it will have a different timestamp in the next query  
GO  
UPDATE STATISTICS dbo.dimCustomer (Customer_LastName_Stats);  
  
--Return the date when Customer_LastName_Stats was last updated.  
SELECT stats_id, name AS stats_name,   
    STATS_DATE(object_id, stats_id) AS statistics_date  
FROM sys.stats s  
WHERE s.object_id = OBJECT_ID('dbo.DimCustomer')  
    AND s.name = 'Customer_LastName_Stats';  
GO    
```  
  
### <a name="c-view-the-date-of-the-last-update-for-all-statistics-on-a-table"></a>C. テーブルのすべての統計の最後の更新日付を表示する  
 この例は、DimCustomer テーブル上の各統計オブジェクトが最後に更新された日付を返します。  
  
```sql  
--Return the dates all statistics on the table were last updated.  
SELECT stats_id, name AS stats_name,   
    STATS_DATE(object_id, stats_id) AS statistics_date  
FROM sys.stats s  
WHERE s.object_id = OBJECT_ID('dbo.DimCustomer');  
GO  
```  
  
 統計がインデックスに対応する場合、[sys.stats](../../relational-databases/system-catalog-views/sys-stats-transact-sql.md) カタログ ビューの *stats_id* の値は、[sys.indexes](../../relational-databases/system-catalog-views/sys-indexes-transact-sql.md) カタログ ビューの *index_id* の値と同一になります。このため、次に示すクエリは上のクエリと同じ結果を返します。 統計がインデックスに対応しない場合、統計は sys.stats の結果には含まれますが、sys.indexes の結果には含まれません。  
  
```sql  
USE AdventureWorksPDW2012;  
GO  
SELECT name AS index_name,   
    STATS_DATE(object_id, index_id) AS statistics_update_date  
FROM sys.indexes   
WHERE object_id = OBJECT_ID('dbo.DimCustomer');  
GO  
```  
  
## <a name="see-also"></a>参照  
 [システム関数 &#40;Transact-SQL&#41;](../../relational-databases/system-functions/system-functions-category-transact-sql.md)   
 [UPDATE STATISTICS &#40;Transact-SQL&#41;](../../t-sql/statements/update-statistics-transact-sql.md)   
 [sp_autostats &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-autostats-transact-sql.md)   
 [統計](../../relational-databases/statistics/statistics.md)    
 [sys.dm_db_stats_properties &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-stats-properties-transact-sql.md)   
 [sys.stats](../../relational-databases/system-catalog-views/sys-stats-transact-sql.md)   
  
  

