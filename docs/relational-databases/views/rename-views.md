---
description: ビューの名前の変更
title: ビューの名前の変更 | Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: table-view-index
ms.topic: conceptual
helpviewer_keywords:
- views [SQL Server], renaming
- renaming views
ms.assetid: 5eed0488-81d2-40e8-8fdf-b0a640a591d0
author: stevestein
ms.author: sstein
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 9132b99b3217cf99bdcf6073c02a0c1798a1502d
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104749242"
---
# <a name="rename-views"></a>ビューの名前の変更
[!INCLUDE[tsql-appliesto-ss2008-asdb-xxxx-pdw-md](../../includes/tsql-appliesto-ss2008-asdb-xxxx-pdw-md.md)]
  [!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] では、 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] または [!INCLUDE[tsql](../../includes/tsql-md.md)]を使用して、ビューの名前を変更できます。  
  
> [!WARNING]  
>  ビューの名前を変更すると、そのビューに依存するコードやアプリケーションの実行が失敗する場合があります。 これには、他のビュー、クエリ、ストアド プロシージャ、ユーザー定義関数、およびクライアント アプリケーションが含まれます。 このようなエラーは連鎖するので注意が必要です。  
  
 **このトピックの内容**  
  
-   **作業を開始する準備:**  
  
     [前提条件](#Prerequisites)  
  
     [Security](#Security)  
  
-   **以下を使用してビューの名前を変更するには:**  
  
     [SQL Server Management Studio](#SSMSProcedure)  
  
     [Transact-SQL](#TsqlProcedure)  
  
-   **Follow Up:**  [After renaming a view](#FollowUp)  
  
##  <a name="before-you-begin"></a><a name="BeforeYouBegin"></a> はじめに  
  
###  <a name="prerequisites"></a><a name="Prerequisites"></a> 前提条件  
 ビューのすべての依存関係の一覧を取得します。 ビューを参照するすべてのオブジェクト、スクリプト、またはアプリケーションは、ビューの新しい名前を反映するように変更する必要があります。 詳しくは、「 [Get Information About a View](../../relational-databases/views/get-information-about-a-view.md)」をご覧ください。 ビューの名前を変更するのではなく、ビューを削除してから新しい名前で作成し直すことをお勧めします。 ビューを再作成することにより、ビューで参照されているオブジェクトの依存情報が更新されます。  
  
###  <a name="security"></a><a name="Security"></a> セキュリティ  
  
####  <a name="permissions"></a><a name="Permissions"></a> Permissions  
 SCHEMA に対する ALTER 権限または OBJECT に対する CONTROL 権限と、データベースの CREATE VIEW 権限が必要です。  
  
##  <a name="using-sql-server-management-studio"></a><a name="SSMSProcedure"></a> SQL Server Management Studio の使用  
  
#### <a name="to-rename-a-view"></a>ビューの名前を変更するには  
  
1.  **オブジェクト エクスプローラー** で、名前を変更するビューを含むデータベースを展開します。次に、 **[ビュー]** フォルダーを展開します。  
  
2.  名前を変更するビューを右クリックし、 **[名前の変更]** を選択します。  
  
3.  ビューの新しい名前を入力します。  

##  <a name="using-transact-sql"></a><a name="TsqlProcedure"></a> Transact-SQL の使用  
 **ビューの名前を変更するには**  
  
 ビューの名前は **sp_rename** を使用して変更できますが、既存のビューを削除した後、新しい名前でビューを再作成することをお勧めします。  
  
 詳細については、「[CREATE VIEW &#40;Transact-SQL&#41;](../../t-sql/statements/create-view-transact-sql.md)」および「[DROP VIEW &#40;Transact-SQL&#41;](../../t-sql/statements/drop-view-transact-sql.md)」を参照してください。  
  
##  <a name="follow-up-after-renaming-a-view"></a><a name="FollowUp"></a>補足情報: ビューの名を変更した後  
 ビューの古い名前を参照するすべてのオブジェクト、スクリプト、およびアプリケーションで新しい名前が使用されていることを確認します。  
  
  
