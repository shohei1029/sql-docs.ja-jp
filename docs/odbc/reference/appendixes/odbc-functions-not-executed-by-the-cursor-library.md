---
description: カーソル ライブラリによって実行されない ODBC 関数
title: カーソルライブラリによって実行されない ODBC 関数 |Microsoft Docs
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
ms.assetid: f2941522-75eb-4db9-9468-4800b884dac2
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: db4fac295d8f2426637ca90584df86267eb20dc9
ms.sourcegitcommit: 0b37eb7aef2f358f80867cd13830dd6683da8d85
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105980872"
---
# <a name="odbc-functions-not-executed-by-the-cursor-library"></a>カーソル ライブラリによって実行されない ODBC 関数
> [!IMPORTANT]  
>  この機能は、今後のバージョンの Windows では削除される予定です。 新しい開発作業ではこの機能の使用を避け、現在この機能を使用しているアプリケーションの変更を検討してください。 Microsoft では、ドライバーのカーソル機能を使用することをお勧めします。  
  
 カーソルライブラリでは、次の関数は実行されません。 アプリケーションがこれらの関数のいずれかを呼び出すと、ドライバーマネージャーはカーソルライブラリではなくドライバーを呼び出します。  
  
:::row:::
   :::column span="":::
      **SQLFetch**<br>      **SQLGetConnectAttr**<br>      **SQLGetDiagField**<br>      **SQLGetDiagRec**
   :::column-end:::
   :::column span="":::
      **SQLGetEnvAttr**<br>      **SQLSetDescRec**<br>      **SQLSetEnvAttr**  
   :::column-end:::
:::row-end:::

