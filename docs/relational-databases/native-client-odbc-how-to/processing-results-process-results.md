---
description: 結果の処理 - 処理結果
title: 結果の処理 (ODBC) |Microsoft Docs
ms.custom: ''
ms.date: 03/03/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- processing results [ODBC]
ms.assetid: 4810fe3f-78ee-4f0d-8bcc-a4659fbcf46f
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 6bf12c65d5867b358b7493eadb300752a5f862aa
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104750122"
---
# <a name="processing-results---process-results"></a>結果の処理 - 処理結果
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

ODBC アプリケーションでの結果の処理では、最初に結果セットの特性を判断し、次に [SQLBindCol](../../relational-databases/native-client-odbc-api/sqlbindcol.md) または [SQLGetData](../../relational-databases/native-client-odbc-api/sqlgetdata.md)のいずれかを使用してプログラム変数にデータを取得します。  
  
### <a name="to-process-results"></a>結果を処理するには  
  
1.  結果セットの情報を取得します。  
  
2.  バインドされた列が使用されている場合は、バインド先の列ごとに[SQLBindCol](../../relational-databases/native-client-odbc-api/sqlbindcol.md) を呼び出して、プログラム バッファーを列にバインドします。  
  
3.  結果セット内の各行に対して次の操作を行います。  
  
    -   [SQLFetch](../../odbc/reference/syntax/sqlfetch-function.md) を呼び出して次の行を取得します。  
  
    -   バインドされた列が使用されている場合は、バインドされた列のバッファー内で現在使用可能なデータを使用します。  
  
    -   バインドされていない列が使用されている場合は、[SQLGetData](../../relational-databases/native-client-odbc-api/sqlgetdata.md) を 1 回以上呼び出して、バインドされている最後の列の後に、バインドされていない列のデータを取得します。 **SQLGetData** の呼び出しは、列番号の昇順にする必要があります。  
  
    -   **SQLGetData** を複数回呼び出して、text または image 列からデータを取得します。  
  
4.  [SQLFetch](../../odbc/reference/syntax/sqlfetch-function.md) が SQL_NO_DATA を返すことによって結果セットの終了を示したら、[SQLMoreResults](../../relational-databases/native-client-odbc-api/sqlmoreresults.md) を呼び出して、使用可能な結果セットが他にあるかどうかを確認します。  
  
    -   SQL_SUCCESS が返された場合は、別の結果セットを使用できます。  
  
    -   SQL_NO_DATA が返された場合は、他に使用できる結果セットはありません。  
  
    -   SQL_SUCCESS_WITH_INFO または SQL_ERROR が返された場合は、[SQLGetDiagRec](../../odbc/reference/syntax/sqlgetdiagrec-function.md) を呼び出して、PRINT ステートメントまたは RAISERROR ステートメントからの出力が使用可能かどうかを確認します。  
  
         バインドされたステートメントのパラメーターが出力パラメーターまたはストアド プロシージャの戻り値に使用されている場合は、バインドされたパラメーターのバッファーでデータを使用できるようになります。 バインドされたパラメーターが使用される場合は、[SQLExecute](../../odbc/reference/syntax/sqlexecute-function.md) または [SQLExecDirect](../../odbc/reference/syntax/sqlexecdirect-function.md) への各呼び出しで、SQL ステートメントが *S* 回実行されます。*S* は、バインドされたパラメーターの配列内にある要素の数です。 つまり、処理する結果のセットが *S* 個あることを意味します。これらの結果の各セットには、結果セット、出力パラメーター、および通常 SQL ステートメントの 1 回の実行で返されるリターン コードがすべて含まれます。  
  
    > [!NOTE]  
    >  結果セットに計算行が含まれている場合、各計算行は個々の結果セットとして使用できるようになります。 これらの計算結果セットは標準行内に点在し、標準行は複数の結果セットに分割されます。  
  
5.  必要に応じて、SQL_UNBIND を使用して [SQLFreeStmt](../../relational-databases/native-client-odbc-api/sqlfreestmt.md) を呼び出し、バインドされた列バッファーを解放します。  
  
6.  別の結果セットが使用できる場合は、手順 1 に戻ります。  

> [!NOTE]  
>  [SQLFetch](../../odbc/reference/syntax/sqlfetch-function.md) によって SQL_NO_DATA が返される前に結果セットの処理を取り消すには、[SQLCloseCursor](../../relational-databases/native-client-odbc-api/sqlclosecursor.md) を呼び出します。  
  
## <a name="see-also"></a>参照  
[ODBC&#41;&#40;結果セットの情報を取得します。 ](../../relational-databases/native-client-odbc-how-to/processing-results-retrieve-result-set-information.md)   
  
