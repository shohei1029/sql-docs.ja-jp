---
description: ストレージの割り当て
title: ストレージの割り当て |Microsoft Docs
ms.custom: ''
ms.date: 03/06/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- column-wise binding [ODBC]
- SQL Server Native Client ODBC driver, result sets
- ODBC applications, result sets
- SQLBindCol function
- storing data [ODBC]
- result sets [ODBC], storage
- SQL Server Native Client ODBC driver, data storage
- row-wise binding
- binding result sets [SQL Server Native Client]
- array binding
ms.assetid: 11c81955-5300-495f-925f-9256f2587b58
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 9fc7a4bd577783042b0b234f04bd83f8c1f0e030
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104752792"
---
# <a name="assigning-storage"></a>ストレージの割り当て
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  アプリケーションでは、SQL ステートメントの実行前後に、結果用のストレージを割り当てることができます。 アプリケーションで最初に SQL ステートメントを準備または実行すると、結果のストレージを割り当てる前に、結果セットに関する情報を取得できます。 たとえば、結果セットが不明であれば、アプリケーションでは、列にストレージを割り当てる前に、列数を取得する必要があります。  
  
 データの列にストレージを関連付けるために、アプリケーションは [SQLBindCol](../../relational-databases/native-client-odbc-api/sqlbindcol.md)を呼び出し、それを渡します。  
  
-   データの変換先のデータ型。  
  
-   データの出力バッファーのアドレス。  
  
     アプリケーションではこのバッファーを割り当てる必要があります。このバッファーは、変換後の形式でデータを保持するのに十分な大きさを確保する必要があります。  
  
-   出力バッファーの長さ。  
  
     この値は、返されるデータが C の固定幅データ (整数、実数、日付構造体など) の場合は無視されます。  
  
-   使用できるデータのバイト数を返すストレージ バッファーのアドレス。  
  
 また、アプリケーションは、結果セットの列をプログラム変数の配列にバインドして、結果セットの行をブロック単位でフェッチする機能をサポートすることもできます。 配列バインディングには、次の2種類があります。  
  
-   列方向のバインドは、各列を変数の独自の配列にバインドすると終了します。  
  
     列方向のバインドは、*属性* を SQL_ATTR_ROW_BIND_TYPE に設定し、 *valueptr* を SQL_BIND_BY_COLUMN に設定して、 [SQLSetStmtAttr](../../relational-databases/native-client-odbc-api/sqlsetstmtattr.md)を呼び出すことによって指定します。 すべての配列には、同じ数の要素を保持する必要があります。  
  
-   行方向のバインドは、SQL ステートメント内のすべてのパラメーターを 1 単位として、パラメーターの各変数を保持する構造体の配列にバインドすると終了します。  
  
     行 **方向のバインド** を指定するには、 *属性* を SQL_ATTR_ROW_BIND_TYPE に設定し、 *valueptr* に結果セットの列を受け取る変数を保持する構造体のサイズを設定します。  
  
 また、アプリケーションでは、SQL_ATTR_ROW_ARRAY_SIZE を列または行の配列内の要素数に設定し、SQL_ATTR_ROW_STATUS_PTR と SQL_ATTR_ROWS_FETCHED_PTR も設定します。  
  
## <a name="see-also"></a>参照  
 [ODBC&#41;&#40;結果の処理 ](../../relational-databases/native-client-odbc-results/processing-results-odbc.md)  
  
  
