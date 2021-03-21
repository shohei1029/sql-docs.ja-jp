---
description: ビューの変更
title: ビューの変更 | Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: table-view-index
ms.topic: conceptual
helpviewer_keywords:
- views [SQL Server], renaming
- views [SQL Server], modifying
- modifying views
- renaming views
ms.assetid: 2d3c14dc-43e5-4324-b8fb-f2692d330b16
author: stevestein
ms.author: sstein
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 15440cf20c759ee0bf2c1217d211c83d8ed9189b
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104755182"
---
# <a name="modify-views"></a>ビューの変更
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]
  [!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] では、ビューの定義後にビューの削除や再作成を行わずに、 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] または [!INCLUDE[tsql](../../includes/tsql-md.md)]を使用して、ビューの名前または定義を変更できます。  
  
 **このトピックの内容**  
  
-   **作業を開始する準備:**  
  
     [制限事項と制約事項](#Restrictions)  
  
     [Security](#Security)  
  
-   **以下を使用してビューを変更するには:**  
  
     [SQL Server Management Studio](#SSMSProcedure)  
  
     [Transact-SQL](#TsqlProcedure)  
  
##  <a name="before-you-begin"></a><a name="BeforeYouBegin"></a> はじめに  
  
###  <a name="limitations-and-restrictions"></a><a name="Restrictions"></a> 制限事項と制約事項  
  
-   依存オブジェクトが無効になるような変更を行わない限り、ビューを変更しても、ストアド プロシージャやトリガーなどの依存オブジェクトには影響しません。  
  
-   現在使用されているビューを ALTER VIEW を使用して変更する場合、 [!INCLUDE[ssDE](../../includes/ssde-md.md)] はビューに対して排他スキーマ ロックを設定します。 ロックが許可され、ビューのアクティブ ユーザーがいなくなると、 [!INCLUDE[ssDE](../../includes/ssde-md.md)] はプロシージャ キャッシュからビューのすべてのコピーを削除します。 ビューを参照している既存のプランはキャッシュに残りますが、呼び出されると再コンパイルされます。  
  
-   ALTER VIEW は、インデックス付きビューに適用できますが、そのビューのすべてのインデックスを無条件で削除します。  
  
###  <a name="security"></a><a name="Security"></a> セキュリティ  
  
####  <a name="permissions"></a><a name="Permissions"></a> Permissions  
 ALTER VIEW を実行するには、少なくとも OBJECT に対する ALTER 権限が必要です。  
  
##  <a name="using-sql-server-management-studio"></a><a name="SSMSProcedure"></a> SQL Server Management Studio の使用  
  
#### <a name="to-modify-a-view"></a>ビューを変更するには  
  
1.  **オブジェクト エクスプローラー** で、ビューがあるデータベースの横にあるプラス記号をクリックし、 **[ビュー]** フォルダーの横にあるプラス記号をクリックします。  
  
2.  変更するビューを右クリックし、 **[デザイン]** を選択します。  
  
3.  クエリ デザイナーのダイアグラム ペインで、次の方法を使用してビューを変更します。  
  
    1.  追加または削除する要素のチェック ボックスをオンまたはオフにします。  
  
    2.  ダイアグラム ペイン内で右クリックし、**[テーブルの追加]** を選択します。次に、**[テーブルの追加]** ダイアログ ボックスで、ビューに追加する列を選択します。  
  
    3.  削除するテーブルのタイトル バーを右クリックし、 **[削除]** をクリックします。  
  
4.  **ファイル** メニューの **view name**_の保存_ をクリックします。  
  
##  <a name="using-transact-sql"></a><a name="TsqlProcedure"></a> Transact-SQL の使用  
  
#### <a name="to-modify-a-view"></a>ビューを変更するには  
  
1.  **オブジェクト エクスプローラー** で、 [!INCLUDE[ssDE](../../includes/ssde-md.md)]のインスタンスに接続します。  
  
2.  [標準] ツール バーの **[新しいクエリ]** をクリックします。  
  
3.  次の例をコピーしてクエリ ウィンドウに貼り付け、 **[実行]** をクリックします。 この例では、ビューを作成した後、ALTER VIEW を使用してビューを変更します。 ビュー定義に WHERE 句が追加されています。  
  
    ```  
    USE AdventureWorks2012 ;  
    GO  
    -- Create a view.  
    CREATE VIEW HumanResources.EmployeeHireDate  
    AS  
    SELECT p.FirstName, p.LastName, e.HireDate  
    FROM HumanResources.Employee AS e JOIN Person.Person AS  p  
    ON e.BusinessEntityID = p.BusinessEntityID ;   
  
    -- Modify the view by adding a WHERE clause to limit the rows returned.  
    ALTER VIEW HumanResources.EmployeeHireDate  
    AS  
    SELECT p.FirstName, p.LastName, e.HireDate  
    FROM HumanResources.Employee AS e JOIN Person.Person AS  p  
    ON e.BusinessEntityID = p.BusinessEntityID  
    WHERE HireDate < CONVERT(DATETIME,'20020101',101) ;   
    GO  
    ```  
  
 詳細については、「[ALTER VIEW &#40;Transact-SQL&#41;](../../t-sql/statements/alter-view-transact-sql.md)」を参照してください。  
  
  
