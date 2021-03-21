---
description: ビューに関する情報の取得
title: ビューに関する情報の取得 | Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: table-view-index
ms.topic: conceptual
f1_keywords:
- sql13.swb.viewproperties.general.f1
helpviewer_keywords:
- views [SQL Server], status information
- metadata [SQL Server], views
- dependencies [SQL Server], views
- displaying view information
- views [SQL Server], metadata
- viewing view information
- status information [SQL Server], views
- view dependencies
ms.assetid: 05a73e33-8f85-4fb6-80c1-1b659e753403
author: stevestein
ms.author: sstein
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: b969656bdb0ccaeebf1d800f88e6c1989310ceac
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104734925"
---
# <a name="get-information-about-a-view"></a>ビューに関する情報の取得
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]
  [!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] のビューの定義またはプロパティに関する情報は、[!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] または [!INCLUDE[tsql](../../includes/tsql-md.md)] を使用して取得できます。 ビューのデータが元のテーブルからどのように抽出されているのかを理解したり、ビューで定義されているデータを確認するために、ビューの定義を調べたい場合があります。  
  
> [!IMPORTANT]  
>  ビューから参照しているオブジェクトの名前を変更する場合は、ビューのテキストに新しいオブジェクト名が反映されるようにビューを変更する必要があります。 オブジェクト名を変更する前には、まずオブジェクトの依存関係を表示して、その変更により影響を受けるビューがないかどうかを確認してください。  
  
 **このトピックの内容**  
  
-   **作業を開始する準備:**  
  
     [Security](#Security)  
  
-   **以下を使用してビューに関する情報を取得するには:**  
  
     [SQL Server Management Studio](#SSMSProcedure)  
  
     [Transact-SQL](#TsqlProcedure)  
  
##  <a name="before-you-begin"></a><a name="BeforeYouBegin"></a> はじめに  
  
###  <a name="security"></a><a name="Security"></a> セキュリティ  
  
####  <a name="permissions"></a><a name="Permissions"></a> Permissions  
 `sp_helptext` を使用してビューの定義を返すには、 **public** ロールのメンバーシップが必要です。 `sys.sql_expression_dependencies` を使用してビューのすべての依存関係を見つけるには、データベースに対する VIEW DEFINITION 権限とデータベースの `sys.sql_expression_dependencies` に対する SELECT 権限が必要です。 SELECT OBJECT_DEFINITION で返されるようなシステム オブジェクトの定義は公開されます。  
  
##  <a name="using-sql-server-management-studio"></a><a name="SSMSProcedure"></a> SQL Server Management Studio の使用  
  
#### <a name="get-view-properties-by-using-object-explorer"></a>オブジェクト エクスプローラーを使用してビューのプロパティを取得する  
  
1.  **オブジェクト エクスプローラー** で、プロパティを表示するビューを含むデータベースの横にあるプラス記号をクリックします。次に、プラス記号をクリックして **[ビュー]** フォルダーを展開します。  
  
2.  プロパティを表示するビューを右クリックし、 **[プロパティ]** を選択します。  

     **[ビューのプロパティ]** ダイアログ ボックスに次のプロパティが表示されます。  
  
     **[データベース]**  
     このビューを含むデータベースの名前です。  
  
     **サーバー**  
     現在のサーバー インスタンスの名前です。  
  
     **User**  
     この接続のユーザーの名前です。  
  
     **[作成日]**  
     ビューが作成された日付を表示します。  
  
     **Name**  
     現在のビューの名前です。  
  
     **[スキーマ]**  
     ビューを所有するスキーマを表示します。  
  
     **[システム オブジェクト]**  
     ビューがシステム オブジェクトかどうかを指定します。 値は True と False です。  
  
     **[ANSI NULL]**  
     オブジェクトが ANSI NULL オプションで作成されたかどうかを指定します。  
  
     **Encrypted**  
     ビューが暗号化されているかどうかを指定します。 値は True と False です。  
  
     **[引用符で囲まれた識別子]**  
     オブジェクトが引用符で囲まれた識別子オプションで作成されたかどうかを指定します。  
  
     **[スキーマ バインド]**  
     ビューがスキーマ バインドされているかどうかを指定します。 値は True と False です。 スキーマ バインド ビューの詳細については、「[CREATE VIEW &#40;Transact-SQL&#41;](../../t-sql/statements/create-view-transact-sql.md)」の SCHEMABINDING のトピックを参照してください。  
  
#### <a name="getting-view-properties-by-using-the-view-designer-tool"></a>ビュー デザイナー ツールを使用したビューのプロパティの取得  
  
1.  **オブジェクト エクスプローラー** で、プロパティを表示するビューを含むデータベースを展開します。次に、 **[ビュー]** フォルダーを展開します。  
  
2.  プロパティを表示するビューを右クリックし、 **[デザイン]** を選択します。  
  
3.  ダイアグラム ペインの空白領域を右クリックし、 **[プロパティ]** をクリックします。  
  
     **[プロパティ]** ウィンドウに次のプロパティが表示されます。  
  
     **[(名前)]**  
     現在のビューの名前です。  
  
     **データベース名**  
     このビューを含むデータベースの名前です。  
  
     **説明**  
     現在のビューの簡単な説明です。  
  
     **[スキーマ]**  
     ビューを所有するスキーマを表示します。  
  
     **[サーバー名]**  
     現在のサーバー インスタンスの名前です。  
  
     **[スキーマにバインド]**  
     このビューに関連する既定のオブジェクトをユーザーが変更した結果ビュー定義が無効化されるのを防止します。  
  
     **決定的**  
     選択した列のデータ型を明確に決定できるかどうかが表示されます。  
  
     **[DISTINCT 値]**  
     クエリでビューの重複する値を除外することを指定します。 このオプションは、テーブルの一部の列だけを使用するときに、使用する列に重複した値が含まれる可能性のある場合、または 2 つ以上のテーブルを結合するプロセスによって結果セットに重複した行が生成される場合に便利です。 このオプションを選択することは、SQL ペインでステートメントに DISTINCT という単語を挿入することと同じです。  
  
     **[GROUP BY 拡張子]**  
     集計クエリに基づくビューの追加オプションが使用できるように指定します。  
  
     **[すべての列を出力]**  
     選択したビューによってすべての列が返されるかどうかを示します。 これは、ビューの作成時に設定されます。  
  
     **[SQL コメント]**  
     SQL ステートメントの説明を表示します。 説明全体を表示したり、説明を編集したりするには、説明をクリックして、プロパティの右側にある省略記号 ( **[...]** ) をクリックします。 ビューの使用者やビューをいつ使用するかなどの情報をコメントに含めることもできます。  
  
     **[TOP の指定]**  
     展開すると、 **[TOP]**、 **[式]**、 **[パーセント]**、および **[With Ties]** の各プロパティのプロパティが表示されます。  
  
     **[(Top)]**  
     ビューに TOP 句が含まれるように指定します。この場合、最初の n 行または最初の n% の行だけが結果セットに返されます。 既定では、ビューは最初の 10 行を結果セットに返します。 返される行数を変更するか、異なるパーセントを指定する場合に使用します。  
  
     **[式]**  
     ビューによって返されるパーセント ( **[パーセント]** が **[はい]** に設定されている場合) またはレコード数 ( **[パーセント]** が **[いいえ]** に設定されている場合) を示します。  
  
     **Percent**  
     クエリに **TOP** 句が含まれるように指定します。この場合、最初の n% の行だけが結果セットに返されます。  
  
     **[With Ties]**  
     ビューに **WITH TIES** 句が含まれるように指定します。 **WITH TIES** は、ビューに **ORDER BY** 句とパーセンテージに基づく **TOP** 句が含まれる場合に便利です。 このオプションを設定すると、 **ORDER BY** 句の列で同一の値を持つ行がセットになり、セットの一部の行がパーセンテージによる制限で切り捨てられてしまう場合は、すべての行が含まれるように、ビューで指定されるパーセンテージが増やされます。  
  
     **[更新の指定]**  
     展開すると、 **[表示ルールを使用して更新]** プロパティと **[オプションのチェック]** プロパティのプロパティが表示されます。  
  
     **(表示ルールを使用して更新)**  
     Microsoft Data Access Components (MDAC) によって、すべての更新およびビューへの挿入は、ビューのベース テーブルを直接参照する SQL ステートメントではなく、ビューを参照する SQL ステートメントに変換されます。  
  
     MDAC マニフェストは、更新およびビューの挿入操作を、ビューの基になるベース テーブルに対する更新および挿入操作として見なす場合があります。 **[表示ルールを使用して更新]** を選択することにより、MDAC がビュー自体に対する更新および挿入操作を生成することが保証されます。  
  
     **[オプションのチェック]**  
     このビューを開き、 **[結果]** ウィンドウを変更すると、データ ソースによって、追加または変更されたデータがビュー定義の **WHERE** 句を満たすかどうかがチェックされます。 変更内容が **WHERE** 句を満たさない場合、エラーが詳細情報と共に表示されます。  
  
#### <a name="to-get-dependencies-on-the-view"></a>ビューの依存関係を取得するには  
  
1.  **オブジェクト エクスプローラー** で、プロパティを表示するビューを含むデータベースを展開します。次に、 **[ビュー]** フォルダーを展開します。  
  
2.  プロパティを表示するビューを右クリックし、 **[依存関係の表示]** を選択します。  
  
3.  ビューを参照するオブジェクトを表示するには、 **[[ビュー名] に依存するオブジェクト]** を選択します。  
  
4.  ビューによって参照されるオブジェクトを表示するには、 **[[ビュー名] が依存するオブジェクト]** を選択します。  
  
##  <a name="using-transact-sql"></a><a name="TsqlProcedure"></a> Transact-SQL の使用  
  
#### <a name="to-get-the-definition-and-properties-of-a-view"></a>ビューの定義およびプロパティを取得するには  
  
1.  **オブジェクト エクスプローラー** で、 [!INCLUDE[ssDE](../../includes/ssde-md.md)]のインスタンスに接続します。  
  
2.  [標準] ツール バーの **[新しいクエリ]** をクリックします。  
  
3.  次のいずれかの例をコピーしてクエリ ウィンドウに貼り付け、 **[実行]** をクリックします。  
  
    ```  
    USE AdventureWorks2012;  
    GO  
    SELECT definition, uses_ansi_nulls, uses_quoted_identifier, is_schema_bound  
    FROM sys.sql_modules  
    WHERE object_id = OBJECT_ID('HumanResources.vEmployee');   
    GO  
    ```  
  
    ```  
    USE AdventureWorks2012;   
    GO  
    SELECT OBJECT_DEFINITION (OBJECT_ID('HumanResources.vEmployee')) AS ObjectDefinition;   
    GO  
    ```  
  
    ```  
    EXEC sp_helptext 'HumanResources.vEmployee';  
    ```  
  
 詳細については、「[sys.sql_modules &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-sql-modules-transact-sql.md)」、「[OBJECT_DEFINITION &#40;Transact-SQL&#41;](../../t-sql/functions/object-definition-transact-sql.md)」、および「[sp_helptext &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-helptext-transact-sql.md)」を参照してください。  
  
#### <a name="to-get-the-dependencies-of-a-view"></a>ビューの依存関係を取得するには  
  
1.  **オブジェクト エクスプローラー** で、 [!INCLUDE[ssDE](../../includes/ssde-md.md)]のインスタンスに接続します。  
  
2.  [標準] ツール バーの **[新しいクエリ]** をクリックします。  
  
3.  次の例をコピーしてクエリ ウィンドウに貼り付け、 **[実行]** をクリックします。  
  
    ```  
    USE AdventureWorks2012;  
    GO  
    SELECT OBJECT_NAME(referencing_id) AS referencing_entity_name,   
        o.type_desc AS referencing_desciption,   
        COALESCE(COL_NAME(referencing_id, referencing_minor_id), '(n/a)') AS referencing_minor_id,   
        referencing_class_desc, referenced_class_desc,  
        referenced_server_name, referenced_database_name, referenced_schema_name,  
        referenced_entity_name,   
        COALESCE(COL_NAME(referenced_id, referenced_minor_id), '(n/a)') AS referenced_column_name,  
        is_caller_dependent, is_ambiguous  
    FROM sys.sql_expression_dependencies AS sed  
    INNER JOIN sys.objects AS o ON sed.referencing_id = o.object_id  
    WHERE referencing_id = OBJECT_ID(N'Production.vProductAndDescription');  
    GO  
    ```  
  
 詳細については、「[sys.sql_expression_dependencies &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-sql-expression-dependencies-transact-sql.md)」および「[sys.objects &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-objects-transact-sql.md)」を参照してください。  
  
  
