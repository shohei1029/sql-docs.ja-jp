---
description: カーソルの使用 (OLE DB)
title: カーソルの使用 (ODBC) |Microsoft Docs
ms.custom: ''
ms.date: 03/06/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- cursors [ODBC], how to topics
ms.assetid: c502736f-bca0-45c3-ae25-d2ad52d296bf
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: dfe86065fc3e24282ee656a31966a7e4d3c71d9b
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104752492"
---
# <a name="use-cursors-odbc"></a>カーソルの使用 (OLE DB)
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

    
### <a name="to-use-cursors"></a>カーソルを使用するには  
  
1.  [SQLSetStmtAttr](../../../relational-databases/native-client-odbc-api/sqlsetstmtattr.md) を呼び出して、目的のカーソル属性を設定します。  
  
     SQL_ATTR_CURSOR_TYPE および SQL_ATTR_CONCURRENCY 属性を設定します (これは推奨オプションです)。  
  
     または  
  
     SQL_CURSOR_SCROLLABLE および SQL_CURSOR_SENSITIVITY 属性を設定します。  
  
2.  [SQLSetStmtAttr](../../../relational-databases/native-client-odbc-api/sqlsetstmtattr.md) を呼び出して、SQL_ATTR_ROW_ARRAY_SIZE 属性を使用して行セットのサイズを設定します。  
  
3.  WHERE CURRENT OF 句を使用して位置指定更新を実行する場合は、[SQLSetCursorName](../../../odbc/reference/syntax/sqlsetcursorname-function.md) を呼び出して、カーソル名を設定することもできます。  
  
4.  SQL ステートメントを実行します。  
  
5.  WHERE CURRENT OF 句を使用して位置指定更新を実行する場合、手順 3. で [SQLSetCursorName](../../../odbc/reference/syntax/sqlsetcursorname-function.md) を使用してカーソル名を指定していなかったときは、[SQLGetCursorName](../../../relational-databases/native-client-odbc-api/sqlgetcursorname.md) を呼び出してカーソル名を取得することもできます。  
  
6.  [SQLNumResultCols](../../../relational-databases/native-client-odbc-api/sqlnumresultcols.md) を呼び出して、行セット内の列数 (C) を取得します。  
  
     列方向のバインドを使用します。  
  
     \- または  
  
     行方向のバインドを使用します。  
  
7.  必要に応じてカーソルから行セットをフェッチします。  
  
8.  [SQLMoreResults](../../../relational-databases/native-client-odbc-api/sqlmoreresults.md) を呼び出して、別の結果セットが使用可能かどうかを判断します。  
  
    -   SQL_SUCCESS が返された場合は、別の結果セットを使用できます。  
  
    -   SQL_NO_DATA が返された場合は、他に使用できる結果セットはありません。  
  
    -   SQL_SUCCESS_WITH_INFO または SQL_ERROR が返された場合は、[SQLGetDiagRec](../../../odbc/reference/syntax/sqlgetdiagrec-function.md) を呼び出して、PRINT ステートメントまたは RAISERROR ステートメントからの出力が使用可能かどうかを確認します。  
  
     バインドされたステートメントのパラメーターが出力パラメーターまたはストアド プロシージャの戻り値に使用されている場合は、バインドされたパラメーターのバッファーでデータを使用できるようになります。  
  
     バインドされたパラメーターが使用される場合は、[SQLExecute](../../../odbc/reference/syntax/sqlexecute-function.md) または [SQLExecDirect](../../../odbc/reference/syntax/sqlexecdirect-function.md) への各呼び出しで、SQL ステートメントが S 回実行されます。S は、バインドされたパラメーターの配列内にある要素数です。 つまり、処理する結果のセットが S 個あることを意味します。これらの結果の各セットには、結果セット、出力パラメーター、および通常 SQL ステートメントの 1 回の実行で返されるリターン コードがすべて含まれます。  
  
     結果セットに計算行が含まれている場合、各計算行は個々の結果セットとして使用できるようになります。 これらの計算結果セットは標準行内に点在し、標準行は複数の結果セットに分割されます。  
  
9. 必要に応じて、SQL_UNBIND を使用して [SQLFreeStmt](../../../relational-databases/native-client-odbc-api/sqlfreestmt.md) を呼び出し、バインドされた列バッファーを解放します。  
  
10. 別の結果セットが使用できる場合は、手順 6. に進みます。  
  
     手順 9. で、部分的に処理された結果セットで [SQLMoreResults](../../../relational-databases/native-client-odbc-api/sqlmoreresults.md) を呼び出すと、残りの結果セットがクリアされます。 [SQLCloseCursor](../../../relational-databases/native-client-odbc-api/sqlclosecursor.md) を呼び出してクリアする方法もあります。  
  
     使用するカーソルの種類を制御するには、SQL_ATTR_CURSOR_TYPE と SQL_ATTR_CONCURRENCY を設定するか、SQL_ATTR_CURSOR_SENSITIVITY と SQL_ATTR_CURSOR_SCROLLABLE を設定します。 カーソルの動作を指定するこの 2 つの方法を組み合わせて実行しないでください。  
  
## <a name="see-also"></a>参照  
 [カーソルの使用方法に関するトピック &#40;ODBC&#41;](../../../relational-databases/native-client-odbc-how-to/cursors/using-cursors-how-to-topics-odbc.md)  
  
