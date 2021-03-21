---
description: 行データのない、テーブルの複製コピーを作成します。
title: テーブルを複製する | Microsoft Docs
ms.custom: ''
ms.date: 10/05/2020
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: table-view-index
ms.topic: conceptual
helpviewer_keywords:
- copying tables
- tables [SQL Server], duplicating
- duplicating tables
- duplicating table structures
- table copying [SQL Server]
ms.assetid: c6b07423-d1e5-4e5e-8681-5088921f5df3
author: stevestein
ms.author: sstein
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 9974f91b955e30831c4bfbdbcb97ab5e72fdeb2a
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104743082"
---
# <a name="duplicate-tables"></a>テーブルを複製する
[!INCLUDE [sqlserver2016-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sqlserver2016-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] では、 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] または [!INCLUDE[tsql](../../includes/tsql-md.md)] を使用して、新しいテーブルを作成して既存のテーブルから列情報をコピーすることで既存のテーブルを複製できます。  
  
> [!IMPORTANT]  
>  この操作によって複製されるのはテーブルの構造のみです。テーブル行は複製されません。  
  
 **このトピックの内容**  
  
-   **作業を開始する準備:**  
  
     [Security](#Security)  
  
-   **テーブルを複製するための方法:**  
  
     [SQL Server Management Studio](#SSMSProcedure)  
  
     [Transact-SQL](#TsqlProcedure)  
  
##  <a name="before-you-begin"></a><a name="BeforeYouBegin"></a> はじめに  
  
###  <a name="security"></a><a name="Security"></a> セキュリティ  
  
####  <a name="permissions"></a><a name="Permissions"></a> Permissions  
 対象となるデータベースの CREATE TABLE 権限が必要です。  
  
##  <a name="using-sql-server-management-studio"></a><a name="SSMSProcedure"></a> SQL Server Management Studio の使用  
  
#### <a name="to-duplicate-a-table"></a>テーブルを複製するには  
  
1.  テーブルを作成するデータベースに接続していること、およびそのデータベースがオブジェクト エクスプローラーで選択されていることを確認します。  
  
2.  オブジェクト エクスプローラーで、 **[テーブル]** を右クリックし、 **[新しいテーブル]** をクリックします。  
  
3.  オブジェクト エクスプローラーで、コピーするテーブルを右クリックし、 **[デザイン]** をクリックします。  
  
4.  既存のテーブルの列を選択し、 **[編集]** メニューの **[コピー]** をクリックします。  
  
5.  新しいテーブルに戻り、1 行目を選択します。  
  
6.  **[編集]** メニューの **[貼り付け]** をクリックします。  
  
7.  **[ファイル]** メニューの **[<_テーブル名_> を保存]** をクリックします。  
  
8.  **[名前の選択]** ダイアログ ボックスで、新しいテーブルの名前を入力し、 **[OK]** をクリックします。  

##  <a name="using-transact-sql"></a><a name="TsqlProcedure"></a> Transact-SQL の使用  
  
#### <a name="to-duplicate-a-table-in-query-editor"></a>クエリ エディターでテーブルを複製するには  
  
1.  テーブルを作成するデータベースに接続していること、およびそのデータベースがオブジェクト エクスプローラーで選択されていることを確認します。  
  
2.  複製するテーブルを右クリックし、 **[テーブルをスクリプト化]** をポイントして、 **[CREATE]** をポイントします。次に、 **[新しいクエリ エディター ウィンドウ]** をクリックします。  
  
3.  テーブルの名前を変更します。  
  
4.  新しいテーブルに必要でない列をすべて削除します。  
  
5.  **[実行]** をクリックします。  
