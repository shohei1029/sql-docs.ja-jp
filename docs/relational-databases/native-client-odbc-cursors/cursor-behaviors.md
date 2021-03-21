---
description: カーソル動作
title: カーソルの動作 |Microsoft Docs
ms.custom: ''
ms.date: 10/24/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- scrollable cursors [SQL Server]
- SQL Server Native Client ODBC driver, cursors
- version-based optimistic concurrency
- cursors [ODBC], cursor behaviors
- ODBC applications, cursors
- SQL_ATTR_CURSOR_SENSITIVITY option
- SQL_ATTR_CURSOR_SCROLLABLE option
- sensitivity behavior of cursor
- ODBC cursors, cursor behaviors
ms.assetid: 742ddcd2-232b-4aa1-9212-027df120ad35
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 6e0a9e029152b35b0ea7b00bc2cde9c9b8ee9add
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104756322"
---
# <a name="cursor-behaviors"></a>カーソル動作
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  ODBC では、カーソルのスクロールの可否と感度を設定することでカーソル動作を指定する、ISO オプションがサポートされます。 これらの動作は、 [SQLSetStmtAttr](../../relational-databases/native-client-odbc-api/sqlsetstmtattr.md)の呼び出しで SQL_ATTR_CURSOR_SCROLLABLE オプションと SQL_ATTR_CURSOR_SENSITIVITY オプションを設定することによって指定します。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client ODBC ドライバーでは、次の特性を持つサーバー カーソルを要求して、これらのオプションを実装します。  
  
|カーソル動作の設定|要求されるサーバー カーソルの特性|  
|------------------------------|---------------------------------------------|  
|SQL_SCROLLABLE と SQL_SENSITIVE|キーセット ドリブン カーソルとバージョンに基づくオプティミスティック コンカレンシー|  
|SQL_SCROLLABLE と SQL_INSENSITIVE|静的カーソルと読み取り専用コンカレンシー|  
|SQL_SCROLLABLE と SQL_UNSPECIFIED|静的カーソルと読み取り専用コンカレンシー|  
|SQL_NONSCROLLABLE と SQL_SENSITIVE|順方向専用カーソルとバージョンに基づくオプティミスティック コンカレンシー|  
|SQL_NONSCROLLABLE と SQL_INSENSITIVE|既定の結果セット (順方向専用、読み取り専用)|  
|SQL_NONSCROLLABLE と SQL_UNSPECIFIED|既定の結果セット (順方向専用、読み取り専用)|  
  
 バージョンベースのオプティミスティック同時実行制御には、基になるテーブルに **timestamp** 列が必要です。 **Timestamp** 列のないテーブルに対してバージョンベースのオプティミスティック同時実行制御が要求された場合、サーバーは値に基づくオプティミスティック同時実行制御を使用します。  
  
## <a name="scrollability"></a>スクロール可能  
 SQL_ATTR_CURSOR_SCROLLABLE が SQL_SCROLLABLE に設定されている場合、カーソルは [Sqlfetchscroll](../../relational-databases/native-client-odbc-api/sqlfetchscroll.md)の *fetchorientation* パラメーターのさまざまな値をすべてサポートします。 SQL_ATTR_CURSOR_SCROLLABLE が SQL_NONSCROLLABLE に設定されている場合、カーソルは SQL_FETCH_NEXT の *Fetchorientation* 値のみをサポートします。  
  
## <a name="sensitivity"></a>感度  
 SQL_ATTR_CURSOR_SENSITIVITY が SQL_SENSITIVE に設定されているとき、現在のユーザーが行ったデータ変更または他のユーザーがコミットしたデータ変更がカーソルに反映されます。 SQL_ATTR_CURSOR_SENSITIVITY が SQL_INSENSITIVE に設定されているときは、データ変更がカーソルに反映されません。  
  
## <a name="see-also"></a>参照  
 カーソル[(ODBC)](../../relational-databases/native-client-odbc-cursors/using-cursors-odbc.md) [カーソルプロパティ](properties/cursor-properties.md)の使用 
  
  
