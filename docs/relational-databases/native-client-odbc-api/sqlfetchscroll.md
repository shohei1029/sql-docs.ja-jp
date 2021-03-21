---
description: SQLFetchScroll
title: SQLFetchScroll |Microsoft Docs
ms.custom: ''
ms.date: 03/17/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
apitype: DLLExport
helpviewer_keywords:
- SQLFetchScroll function
ms.assetid: 524a3985-a08d-4445-99e0-bb551a666615
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: ee0d1c867474e3d56cac186d527b977167640abc
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104755372"
---
# <a name="sqlfetchscroll"></a>SQLFetchScroll
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  **Sqlfetchscroll** は、データの1つの行セットをアプリケーションに返します。 行セットのサイズは [SQLSetStmtAttr](../../relational-databases/native-client-odbc-api/sqlsetstmtattr.md)を使用して設定されます。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]Native CLIENT ODBC ドライバーでは、定義済みのすべてのフェッチ命令 (SQL_FETCH_RELATIVE など) がサポートされていますが、次の制限があります。  
  
-   ステートメントに順方向専用カーソルを定義する場合は、SQL_FETCH_NEXT が必要です。他の形式でフェッチを試行すると、エラーが返されます。  
  
-   SQL_FETCH_BOOKMARK がサポートされるのは、静的カーソルとキーセット ドリブン カーソルに対してのみです。  
  
## <a name="sqlfetchscroll-support-for-enhanced-date-and-time-features"></a>SQLFetchScroll による機能強化された日付と時刻のサポート  
 Date 型または time 型の結果列の値は、「 [SQL から C への変換](../../relational-databases/native-client-odbc-date-time/datetime-data-type-conversions-from-sql-to-c.md)」で説明されているように変換されます。  
  
 詳細については、「 [日付と時刻の機能強化 &#40;ODBC&#41;](../../relational-databases/native-client-odbc-date-time/date-and-time-improvements-odbc.md)」を参照してください。  
  
## <a name="sqlfetchscroll-support-for-large-clr-udts"></a>SQLFetchScroll による大きな CLR UDT のサポート  
 **Sqlfetchscroll** は、大きな CLR ユーザー定義型 (udt) をサポートしています。 詳細については、「 [LARGE CLR User-Defined Types &#40;ODBC&#41;](../../relational-databases/native-client/odbc/large-clr-user-defined-types-odbc.md)」を参照してください。  
  
## <a name="see-also"></a>参照  
 [SQLFetchScroll 関数](../../odbc/reference/syntax/sqlfetchscroll-function.md)   
 [ODBC API 実装の詳細](../../relational-databases/native-client-odbc-api/odbc-api-implementation-details.md)  
  
