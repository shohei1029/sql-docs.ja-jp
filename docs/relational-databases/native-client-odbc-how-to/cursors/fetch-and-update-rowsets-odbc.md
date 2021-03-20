---
description: 行セットのフェッチおよび更新 (ODBC)
title: 行セットのフェッチおよび更新 (ODBC) |Microsoft Docs
ms.custom: ''
ms.date: 03/06/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- rowsets [ODBC]
ms.assetid: cf0eb3b4-8b72-49fc-a845-95edc360cf93
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: adb000e81724279868713fb78d4550d68c308f9b
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104752512"
---
# <a name="fetch-and-update-rowsets-odbc"></a>行セットのフェッチおよび更新 (ODBC)
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

    
### <a name="to-fetch-and-update-rowsets"></a>行セットをフェッチおよび更新するには  
  
1.  必要に応じて、SQL_ROW_ARRAY_SIZE を指定して [SQLSetStmtAttr](../../../relational-databases/native-client-odbc-api/sqlsetstmtattr.md) を呼び出し、行セット内の行数 (R) を変更します。  
  
2.  [Sqlfetch](../../../odbc/reference/syntax/sqlfetch-function.md)または[sqlfetchscroll](../../../relational-databases/native-client-odbc-api/sqlfetchscroll.md)を呼び出して、行セットを取得します。  
  
3.  バインドされた列が使用されている場合は、行セットのバインドされた列のバッファーでデータ値とデータの長さが使用できるようになります。  
  
     バインドされていない列を使用する場合は、行ごとに [SQLSetPos](../../../odbc/reference/syntax/sqlsetpos-function.md) を呼び出し、カーソル位置を設定するために SQL_POSITION を使用します。次に、バインド解除した列ごとに、次のようにします。  
  
    -   [SQLGetData](../../../relational-databases/native-client-odbc-api/sqlgetdata.md)を1回以上呼び出して、行セットの最後にバインドされた列の後にバインドされていない列のデータを取得します。 [SQLGetData](../../../relational-databases/native-client-odbc-api/sqlgetdata.md)の呼び出しは、列番号の昇順にする必要があります。  
  
    -   [SQLGetData](../../../relational-databases/native-client-odbc-api/sqlgetdata.md) を複数回呼び出して、text または image 列からデータを取得します。  
  
4.  実行時データ text または image 列をセットアップします。  
  
5.  [SQLSetPos](../../../odbc/reference/syntax/sqlsetpos-function.md)または[sqlbulkoperations](../../../odbc/reference/syntax/sqlbulkoperations-function.md)を呼び出して、行セット内のカーソル位置の設定、更新、更新、削除、または行の追加を行います。  
  
     実行時データ text または image 列が更新または追加操作に使用されている場合は、それらの列を処理します。  
  
6.  必要に応じて、位置指定の UPDATE または DELETE ステートメントを実行して、カーソル名 ( [Sqlgetcursor name](../../../relational-databases/native-client-odbc-api/sqlgetcursorname.md)から使用可能) を指定し、同じ接続で別のステートメントハンドルを使用します。  
  
## <a name="see-also"></a>参照  
 [カーソルの使用方法に関するトピック &#40;ODBC&#41;](../../../relational-databases/native-client-odbc-how-to/cursors/using-cursors-how-to-topics-odbc.md)  
  
