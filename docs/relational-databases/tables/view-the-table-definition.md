---
description: テーブルの定義の表示
title: テーブルの定義の表示
ms.custom: ''
ms.date: 03/09/2021
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: table-view-index
ms.topic: conceptual
helpviewer_keywords:
- showing table properties
- displaying table properties
- tables [SQL Server], properties
- viewing table properties
author: stevestein
ms.author: sstein
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 0ffdea67493c7d14255cfc543b1a8518bb3a8b4d
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104748892"
---
# <a name="view-the-table-definition"></a>テーブルの定義の表示
[!INCLUDE [sqlserver2016-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sqlserver2016-asdb-asdbmi-asa-pdw.md)]

  [!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] では、 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] または [!INCLUDE[tsql](../../includes/tsql-md.md)]を使用してテーブルのプロパティを表示できます。  
  
 **このトピックの内容**  
  
-   **作業を開始する準備:**  
  
     [Security](#Security)  
  
-   **テーブルのプロパティを表示する方法:**  
  
     [SQL Server Management Studio](#SSMSProcedure)  
  
     [Transact-SQL](#TsqlProcedure)  
  
##  <a name="before-you-begin"></a><a name="BeforeYouBegin"></a> はじめに  
  
###  <a name="security"></a><a name="Security"></a> セキュリティ  
  
####  <a name="permissions"></a><a name="Permissions"></a> Permissions  
 ユーザーは、ユーザーが所有しているか権限が与えられているテーブルのプロパティのみを表示できます。  
  
##  <a name="using-sql-server-management-studio"></a><a name="SSMSProcedure"></a> SQL Server Management Studio の使用  
  
#### <a name="to-show-table-properties-in-the-properties-window"></a>[プロパティ] ウィンドウにテーブルのプロパティを表示するには  
  
1.  オブジェクト エクスプローラーで、プロパティを表示するテーブルを選択します。  
  
2.  テーブルを右クリックし、ショートカット メニューの **[プロパティ]** をクリックします。 詳細については、「 [テーブルのプロパティ](../../relational-databases/tables/table-properties-ssms.md)」を参照してください。  

##  <a name="using-transact-sql"></a><a name="TsqlProcedure"></a> Transact-SQL の使用  
  
#### <a name="to-show-table-properties"></a>テーブルのプロパティを表示するには  
  
1.  **オブジェクト エクスプローラー** で、 [!INCLUDE[ssDE](../../includes/ssde-md.md)]のインスタンスに接続します。  
  
2.  [標準] ツール バーの **[新しいクエリ]** をクリックします。  
  
3.  次の例をコピーしてクエリ ウィンドウに貼り付け、 **[実行]** をクリックします。 この例では、システム ストアド プロシージャ sp_help を実行して、指定されたオブジェクトのすべての列情報が返されます。  
  
```sql  
EXEC sp_help 'dbo.mytable';
```  
    
 詳細については、「[sp_help (Transact-SQL)](../../relational-databases/system-stored-procedures/sp-help-transact-sql.md)」を参照してください。

 または、システム カタログ ビューに直接クエリを実行して、テーブル、スキーマ、および列に関するオブジェクト メタデータ情報をクエリすることもできます。 次に例を示します。  
  
```sql
SELECT s.name, t.name, c.* FROM sys.columns AS c
INNER JOIN sys.tables AS t ON t.object_id = c.object_id
INNER JOIN sys.schemas AS s ON s.schema_id = t.schema_id
WHERE t.object_id = object_id('mytable') AND s.name = 'dbo';
```
    
 詳細については、次を参照してください。 

* [sys.columns &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-columns-transact-sql.md)    
* [sys.tables &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-tables-transact-sql.md)    
* [sys.schemas &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/schemas-catalog-views-sys-schemas.md)     

