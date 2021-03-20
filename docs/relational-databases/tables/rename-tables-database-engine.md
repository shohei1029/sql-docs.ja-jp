---
description: テーブル名の変更 (データベース エンジン)
title: テーブル名の変更 (データベース エンジン) | Microsoft Docs
ms.custom: ''
ms.date: 02/23/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: table-view-index
ms.topic: conceptual
helpviewer_keywords:
- table renaming [SQL Server]
- table names [SQL Server]
- tables [SQL Server], Visual Database Tools
- renaming tables
author: stevestein
ms.author: sstein
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: c1f38616cf33ee2a759ada71b7873d1a0c0e3c20
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104752382"
---
# <a name="rename-tables-database-engine"></a>テーブル名の変更 (データベース エンジン)

[!INCLUDE [sqlserver2016-asdb-asdbmi](../../includes/applies-to-version/sqlserver2016-asdb-asdbmi.md)]

SQL Server または Azure SQL Database のテーブル名を変更します。

Azure Synapse Analytics または Parallel Data Warehouse でテーブルの名前を変更するには、t-sql [RENAME OBJECT](../../t-sql/statements/rename-transact-sql.md) ステートメントを使用します。 
  
> [!CAUTION]  
>  テーブル名の変更については、十分に検討してください。 そのテーブルを参照するクエリ、ビュー、ユーザー定義関数、ストアド プロシージャ、またはプログラムが存在する場合、テーブル名を変更すると、各オブジェクトが無効になります。  
  
 **このトピックの内容**  
  
-   **作業を開始する準備:**  
  
     [制限事項と制約事項](#Restrictions)  
  
     [Security](#Security)  
  
-   **テーブル名を変更する方法:**  
  
     [SQL Server Management Studio](#SSMSProcedure)  
  
     [Transact-SQL](#TsqlProcedure)  
  
##  <a name="before-you-begin"></a><a name="BeforeYouBegin"></a> はじめに  
  
###  <a name="limitations-and-restrictions"></a><a name="Restrictions"></a> 制限事項と制約事項  
 テーブル名を変更しても、そのテーブルに対する参照の名前は自動的には変更されません。 名前を変更したテーブルを参照しているオブジェクトに対しては、手動で変更を加える必要があります。 たとえば、テーブルの名前を変更するとき、そのテーブルがトリガーで参照されている場合は、新しいテーブル名が反映されるようにトリガーに変更を加える必要があります。 オブジェクトの名前を変更する前には、 [sys.sql_expression_dependencies](../../relational-databases/system-catalog-views/sys-sql-expression-dependencies-transact-sql.md) を使ってテーブルの従属関係を一覧表示できます。  
  
###  <a name="security"></a><a name="Security"></a> セキュリティ  
  
####  <a name="permissions"></a><a name="Permissions"></a> Permissions  
 テーブルに対する ALTER 権限が必要です。  
  
##  <a name="using-sql-server-management-studio"></a><a name="SSMSProcedure"></a> SQL Server Management Studio の使用  
  
#### <a name="to-rename-a-table"></a>テーブル名を変更するには  
  
1.  オブジェクト エクスプローラーで、名前を変更するテーブルを右クリックし、ショートカット メニューの **[デザイン]** をクリックします。  
  
2.  **[表示]** メニューの **[プロパティ]** をクリックします。  
  
3.  **[プロパティ]** ウィンドウの **[オブジェクト名]** ボックスに、テーブルの新しい名前を入力します。  
  
4.  この操作を取り消すには、このフィールド外に移動する前に Esc キーを押します。  
  
5.  **[ファイル]** メニューの **[ _<テーブル名>_ を保存]** をクリックします。  

##  <a name="using-transact-sql"></a><a name="TsqlProcedure"></a> Transact-SQL の使用  
  
#### <a name="to-rename-a-table"></a>テーブル名を変更するには  
  
1.  **オブジェクト エクスプローラー** で、 [!INCLUDE[ssDE](../../includes/ssde-md.md)]のインスタンスに接続します。  
  
2.  [標準] ツール バーの **[新しいクエリ]** をクリックします。  
  
3.  次の例では、`Sales` スキーマの `SalesTerritory` テーブルの名前を `SalesTerr` に変更します。 次の例をコピーしてクエリ ウィンドウに貼り付け、 **[実行]** をクリックします。  
  
    ```  
    USE AdventureWorks2012;   
    GO  
    EXEC sp_rename 'Sales.SalesTerritory', 'SalesTerr';  
    ```  
  
 その他の例については、「[sp_rename &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-rename-transact-sql.md)」を参照してください。  
  
  
