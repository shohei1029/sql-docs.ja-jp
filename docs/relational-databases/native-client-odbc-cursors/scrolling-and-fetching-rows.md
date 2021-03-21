---
description: 行のスクロールとフェッチ
title: 行のスクロールとフェッチ |Microsoft Docs
ms.custom: ''
ms.date: 03/06/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- scrollable cursors [SQL Server]
- SQL Server Native Client ODBC driver, cursors
- SQLFetchScroll function
- ODBC applications, cursors
- cursors [ODBC], fetching rows
- ODBC cursors, fetching rows
- cursors [ODBC], scrolling rows
- fetching [ODBC]
- ODBC cursors, scrolling rows
ms.assetid: 9109f10d-326b-4a6d-8c97-831f60da8c4c
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 88b9ef45cbfcd4bb87c7827d4601fa6c4e545bd0
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104756172"
---
# <a name="scrolling-and-fetching-rows"></a>行のスクロールとフェッチ
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  スクロール可能なカーソルを使用するには、ODBC アプリケーションでは次の操作を行う必要があります。  
  
-   [SQLSetStmtAttr](../../relational-databases/native-client-odbc-api/sqlsetstmtattr.md)を使用してカーソル機能を設定します。  
  
-   **Sqlexecute** または **SQLExecDirect** を使用してカーソルを開きます。  
  
-   **Sqlfetch** または [sqlfetchscroll](../../relational-databases/native-client-odbc-api/sqlfetchscroll.md)を使用して行をスクロールおよびフェッチします。  
  
 **Sqlfetch** と **Sqlfetchsroll** は、同時に行のブロックをフェッチできます。 返される行の数は、SQL_ATTR_ROW_ARRAY_SIZE パラメーターを設定するために **SQLSetStmtAttr** を使用して指定します。  
  
 ODBC アプリケーションでは、 **Sqlfetch** を使用して、順方向専用カーソルを介してフェッチできます。  
  
 カーソルをスクロールするには、 **Sqlfetchscroll** を使用します。 **Sqlfetchscroll** は、相対フェッチ (現在の行セットの先頭から行セット *n* の行をフェッチ) と絶対フェッチ (行 *n* で始まる行セットのフェッチ) に加えて、次の行セット、以前の行セット、最初の行セット、および最後の行セットのフェッチをサポートしています。 絶対フェッチで *n* が負の値の場合、行は結果セットの末尾からカウントされます。 たとえば、行 -1 の絶対フェッチは、結果セット内にある最後の行を起点とした行セットをフェッチします。  
  
 レポートなどのブロックカーソル機能に対してのみ **Sqlfetchscroll** を使用するアプリケーションは、次の行セットをフェッチするオプションのみを使用して、結果セットを1回だけ通過する可能性があります。 一方、画面ベースのアプリケーションでは、 **Sqlfetchscroll** のすべての機能を利用できます。 アプリケーションで、行セットのサイズを画面に表示される行数に設定し、画面バッファーを結果セットにバインドすると、スクロールバーの操作を **Sqlfetchscroll** の呼び出しに直接変換できます。  
  
|スクロール バーの操作|SQLFetchScroll のスクロール操作|  
|--------------------------|-------------------------------------|  
|1 画面分上へ移動 (PageUp)|SQL_FETCH_PRIOR|  
|1 画面分下へ移動 (PageDown)|SQL_FETCH_NEXT|  
|1 行上へ移動|FetchOffset が-1 に等しい SQL_FETCH_RELATIVE|  
|1 行下へ移動|FetchOffset に 1 を指定した SQL_FETCH_RELATIVE |  
|スクロール ボックスを先頭に移動|SQL_FETCH_FIRST|  
|スクロール ボックスを末尾に移動|SQL_FETCH_LAST|  
|スクロール ボックスを任意の位置に移動|SQL_FETCH_ABSOLUTE|  
  
## <a name="in-this-section"></a>このセクションの内容  
  
-   [ODBC での行のブックマーク](../../relational-databases/native-client-odbc-cursors/scrolling-and-fetching-rows-bookmarking-rows-in-odbc.md)  
  
## <a name="see-also"></a>参照  
 [ODBC&#41;&#40;カーソルの使用 ](../../relational-databases/native-client-odbc-cursors/using-cursors-odbc.md)  
  
  
