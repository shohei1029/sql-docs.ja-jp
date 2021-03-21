---
description: テーブル内の列の順序の変更
title: テーブル内の列の順序の変更 | Microsoft Docs
ms.custom: ''
ms.date: 06/15/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: table-view-index
ms.topic: conceptual
helpviewer_keywords:
- columns [SQL Server], change order in a table
- column order, change
ms.assetid: cd99ef56-9085-431a-a0fc-58e7add5399f
author: stevestein
ms.author: sstein
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: a7d3ca1b424b3fae26ad84e172dc6413731cc0be
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104735021"
---
# <a name="change-column-order-in-a-table"></a>テーブル内の列の順序の変更

[!INCLUDE [sqlserver2016-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sqlserver2016-asdb-asdbmi-asa-pdw.md)]

  [!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] では、 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)]を使用して、テーブル デザイナーで列の順序を変更できます。  
  
> [!CAUTION]  
>  テーブルの列の順序を変更すると、特定の列の順序に依存するコードやアプリケーションに影響する場合があります。 これには、クエリ、ビュー、ストアド プロシージャ、ユーザー定義関数、およびクライアント アプリケーションが含まれます。 列の順序に変更を加える場合は、十分注意して行う必要があります。 列が返される順序をアプリケーションおよびクエリ レベルで指定することをお勧めします。 テーブルで定義されている順序に基づいて、すべての列が予想される順序で返されるようにするために、SELECT * の使用に依存しないでください。 クエリまたはアプリケーションでは必ず、表示される順序で列の名前を指定してください。  
  
 **このトピックの内容**  
  
-   **列の順序を変更する方法:**  
  
     [SQL Server Management Studio](#SSMSProcedure)  
  
     [Transact-SQL](#TsqlProcedure)  
  
##  <a name="using-sql-server-management-studio"></a><a name="SSMSProcedure"></a> SQL Server Management Studio の使用  
  
#### <a name="to-change-the-column-order"></a>列の順序を変更するには  
  
1.  **オブジェクト エクスプローラー** で、並べ替える列が含まれているテーブルを右クリックし、 **[デザイン]** をクリックします。  
  
2.  順序を変更する列の名前の左側にあるボックスを選択します。  
  
3.  その列をテーブル内の別の場所にドラッグします。  
  
##  <a name="using-transact-sql"></a><a name="TsqlProcedure"></a> Transact-SQL の使用  
 **列の順序を変更するには**  
  
 このタスクは、Transact-SQL ステートメントを使用した場合はサポートされません。  
  
###  <a name="TsqlExample"></a>  
