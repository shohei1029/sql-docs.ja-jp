---
description: カーソル ライブラリによって実行される ODBC 関数
title: カーソルライブラリによって実行される ODBC 関数 |Microsoft Docs
ms.custom: ''
ms.date: 01/19/2017
ms.prod: sql
ms.prod_service: connectivity
ms.reviewer: ''
ms.technology: connectivity
ms.topic: reference
helpviewer_keywords:
- cursor library [ODBC], functions
- functions [ODBC], cursor library
- ODBC functions [ODBC], cursor library
- ODBC cursor library [ODBC], functions
ms.assetid: 2f1d3386-7e59-4d55-a5b4-3440b61343a3
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: 68d5c4f12b2b2a3d408e2bd47d5f3c1d138b105e
ms.sourcegitcommit: 0b37eb7aef2f358f80867cd13830dd6683da8d85
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105980985"
---
# <a name="odbc-functions-executed-by-the-cursor-library"></a>カーソル ライブラリによって実行される ODBC 関数
> [!IMPORTANT]  
>  この機能は、今後のバージョンの Windows では削除される予定です。 新しい開発作業ではこの機能の使用を避け、現在この機能を使用しているアプリケーションの変更を検討してください。 Microsoft では、ドライバーのカーソル機能を使用することをお勧めします。  
  
 カーソルライブラリでは、次の機能が実行されます。 アプリケーションがこの一覧の関数を呼び出すと、ドライバーマネージャーはドライバーではなくカーソルライブラリを呼び出します。 カーソルライブラリは、関数の実行時にドライバーを呼び出すことができることに注意してください。  
  
:::row:::
   :::column span="":::
      **SQLBindCol**<br>      **SQLBindParam**<br>      **SQLBindParameter**<br>      **SQLCloseCursor**<br>      **SQLEndTran**<br>      **SQLExtendedFetch**<br>      **SQLFetchScroll**<br>      **SQLFreeHandle**<br>      **SQLFreeStmt**<br>      **SQLGetData**<br>      **SQLGetDescField**<br>      **SQLGetDescRec**<br>      **SQLGetFunctions**<br>      **SQLGetInfo**<br>      **SQLGetStmtAttr**
   :::column-end:::
   :::column span="":::
      **SQLGetStmtOption**<br>      **SQLNativeSql**<br>      **SQLNumParams**<br>      **SQLParamOptions**<br>      **SQLRowCount**<br>      **SQLSetConnectAttr**<br>      **SQLSetConnectOption**<br>      **SQLSetDescField**<br>      **SQLSetDescRec**<br>      **SQLSetPos**<br>      **SQLSetScrollOptions**<br>      **SQLSetStmtAttr**<br>      **SQLSetStmtOption**<br>      **SQLTransact**
   :::column-end:::
:::row-end:::
