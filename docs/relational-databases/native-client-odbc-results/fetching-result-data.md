---
description: 結果データのフェッチ
title: 結果データをフェッチしています |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- SQLFetchScroll function
- SQL Server Native Client ODBC driver, result sets
- ODBC applications, result sets
- data types [ODBC], fetching
- SQLBindCol function
- result sets [ODBC], fetching
- fetching [ODBC]
- ODBC data types, fetching
- SQLFetch function
- SQL Server Native Client ODBC driver, data types
- SQLGetData function
ms.assetid: b289c7fb-5017-4d7e-a2d3-19401e9fc4cd
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 1f151fefdb895259720904b9eda81719be1703bb
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104752782"
---
# <a name="fetching-result-data"></a>結果データのフェッチ
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  ODBC アプリケーションでは、結果データのフェッチを 3 つの方法で実行できます。  
  
 最初のオプションは、 [SQLBindCol](../../relational-databases/native-client-odbc-api/sqlbindcol.md)に基づいています。 アプリケーションでは、結果セットをフェッチする前に **SQLBindCol** を使用して、結果セット内の各列をプログラム変数にバインドします。 列がバインドされると、ドライバーは、アプリケーションが **sqlfetch** または [sqlfetchscroll](../../relational-databases/native-client-odbc-api/sqlfetchscroll.md)を呼び出すたびに、結果セットの列にバインドされた変数に現在の行のデータを転送します。 結果セット列のデータ型とプログラム変数のデータ型が異なる場合、ドライバーによってデータ変換が処理されます。 アプリケーションの設定 SQL_ATTR_ROW_ARRAY_SIZE が1より大きい場合は、結果列を変数の配列にバインドできます。これは、 **Sqlfetchscroll** を呼び出すたびにすべて入力されます。  
  
 2番目のオプションは、 [SQLGetData](../../relational-databases/native-client-odbc-api/sqlgetdata.md)に基づいています。 アプリケーションでは、結果セット列をプログラム変数にバインドするために **SQLBindCol** を使用しません。 **Sqlfetch** を呼び出すたびに、アプリケーションは、結果セットの各列に対して **SQLGetData** を1回呼び出します。 **SQLGetData** は、特定の結果セット列から特定のプログラム変数にデータを転送するようにドライバーに指示し、列と変数のデータ型を指定します。 そのため、結果列のデータ型とプログラム変数のデータ型が異なる場合は、ドライバーでデータを変換できます。 **Text** 型、 **ntext** 型、および **image** 型の列は、通常は大きすぎてプログラム変数には収まりませんが、 **SQLGetData** を使用して取得することはできます。 結果列の **text**、 **ntext**、または **image** データがプログラム変数よりも大きい場合、 **SQLGetData** は SQL_SUCCESS_WITH_INFO と SQLSTATE 01004 (文字列データ、右側が切り捨てられます) を返します。 **SQLGetData** を連続して呼び出すと、**テキスト** または **イメージ** データの連続するチャンクが返されます。 データの末尾に到達すると、 **SQLGetData** は SQL_SUCCESS を返します。 SQL_ATTR_ROW_ARRAY_SIZE に 1 よりも大きい値を指定すると、フェッチのたびに行セットが返されます。 **SQLGetData** を使用する前に、まず **SQLSetPos** を使用して、行セット内の特定の行を現在の行として指定する必要があります。  
  
 3番目のオプションは、 **SQLBindCol** と **SQLGetData** を組み合わせて使用する方法です。 たとえば、アプリケーションでは、結果セットの最初の10列をバインドしてから、各フェッチで **SQLGetData** を3回呼び出して、バインドされていない3つの列からデータを取得することができます。 これは通常、結果セットに1つ以上の **text** 列または **image** 列が含まれている場合に使用されます。  
  
 結果セットに設定されているカーソルオプションによっては、アプリケーションは **Sqlfetchscroll** のスクロールオプションを使用して、結果セットをスクロールすることもできます。  
  
 **SQLBindCol** を使用して結果セット列をプログラム変数にバインドすると、 **SQLBindCol** によって ODBC ドライバーによってメモリが割り当てられるため、コストが高くなります。 結果列を変数にバインドすると、 [Sqlfreehandle](../../relational-databases/native-client-odbc-api/sqlfreehandle.md)を呼び出してステートメントハンドルを解放するか、 *foption* を SQL_UNBIND に設定して [SQLFreeStmt](../../relational-databases/native-client-odbc-api/sqlfreestmt.md)を呼び出すまで、そのバインドは有効のままになります。 バインドは、ステートメントが完了しても自動的には元に戻りません。  
  
 このロジックにより、異なるパラメーターを指定して同じ SELECT ステートメントを何度も実行することで、効率を上げることができます。 結果セットは同じ構造を保持するので、結果セットを1回バインドし、すべての SELECT ステートメントを処理し、最後の実行後に SQL_UNBIND に *Foption* を設定して **SQLFreeStmt** を呼び出すことができます。 *Foption* を SQL_UNBIND に設定して **SQLFreeStmt** を呼び出して、以前のバインドをすべて解放しないで、結果セットの列をバインドするために **SQLBindCol** を呼び出さないでください。  
  
 **SQLBindCol** を使用する場合は、行方向または列方向のバインドを行うことができます。 行方向のバインドの方が、列方向のバインドよりも処理がやや高速になります。  
  
 **SQLGetData** を使用すると、 **SQLBindCol** を使用して結果セット列をバインドするのではなく、列ごとにデータを取得できます。 結果セットに含まれる行が少ない場合は、 **SQLBindCol** ではなく **SQLGetData** を使用する方が高速です。それ以外の場合は、 **SQLBindCol** によって最適なパフォーマンスが得られます。 常に同じ変数セットにデータを格納しない場合は、常に再バインドするのではなく、 **SQLGetData** を使用する必要があります。 **SQLGetData** は、すべての列が **SQLBindCol** にバインドされた後に、選択リスト内の列に対してのみ使用できます。 また、 **SQLGetData** を既に使用している列の後にも列が表示されます。  
  
 **SQLGetData**、 **SQLBindCol**、 [SQLBindParameter](../../relational-databases/native-client-odbc-api/sqlbindparameter.md)などのプログラム変数へのデータの移動を処理する ODBC 関数は、暗黙的なデータ型変換をサポートしています。 たとえば、整数列を文字列プログラム変数にバインドするアプリケーションでは、プログラム変数にデータを格納する前に、ドライバーが自動的にそのデータを整数から文字に変換します。  
  
 アプリケーションでのデータ変換は最小限に抑える必要があります。 列やパラメーターは、アプリケーションでの処理にデータ変換が必要な場合を除き、同じデータ型のプログラム変数にバインドする必要があります。 ただし、あるデータ型から別のデータ型に変換する必要がある場合は、アプリケーションでのデータ変換よりもドライバーによるデータ変換の方が効率的です。 通常、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client ODBC ドライバーは、ネットワーク バッファーからアプリケーションの変数にデータを直接転送します。 データ変換を行うようにドライバーに要求すると、ドライバーはデータをバッファーに格納し、CPU サイクルを使用してデータを変換します。  
  
 プログラム変数は、 **text**、 **ntext**、および **image** 型のデータを除く、列から転送されるデータを保持するのに十分な大きさにする必要があります。 結果セットのデータを取得してそのデータを変数に格納するときに、その変数が小さすぎてデータを保持できない場合、ドライバーは警告を生成します。 その結果、ドライバーでは警告メッセージ用のメモリを割り当て、ドライバーとアプリケーションの両方で、警告メッセージの処理やエラーの処理に CPU サイクルが必要になります。 アプリケーションでは、取得するデータを保持するのに十分なサイズの変数を割り当てるか、選択リストで SUBSTRING 関数を使用して結果セット内の列のサイズを小さくする必要があります。  
  
 SQL_C_DEFAULT を使用して C 変数のデータ型を指定する場合は注意が必要です。 SQL_C_DEFAULT は、C 変数のデータ型と、列やパラメーターの SQL データ型を一致させることを指定します。 SQL_C_DEFAULT が **ntext**、 **nchar**、または **nvarchar** 列に対して指定されている場合、Unicode データがアプリケーションに返されます。 そのため、アプリケーションが Unicode データを処理するようにコーディングされていないと、さまざまな問題が発生する可能性があります。 **Uniqueidentifier** (SQL_GUID) データ型でも、同じ種類の問題が発生する可能性があります。  
  
 通常、 **text**、 **ntext**、および **image** 型のデータは、1つのプログラム変数に格納するには大きすぎるため、通常は **SQLBindCol** ではなく **SQLGetData** を使用して処理されます。 サーバーカーソルを使用する場合、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native CLIENT ODBC ドライバーは、行がフェッチされるときに、バインドされていない **text**、 **ntext**、または **image** 型の列のデータを転送しないように最適化されています。 **Text** 型、 **ntext** 型、または **image** 型のデータは、アプリケーションが列に対して **SQLGetData** を発行するまで、実際にはサーバーから取得されません。  
  
 この最適化をアプリケーションに適用すると、ユーザーがカーソルを上下にスクロールしている間に **text**、 **ntext**、または **image** データが表示されないようにすることができます。 ユーザーが行を選択すると、アプリケーションは **SQLGetData** を呼び出して、 **text**、 **ntext**、または **image** 型のデータを取得できます。 これにより、ユーザーが選択していない行の **text** 型、 **ntext** 型、または **image** 型のデータが送信され、大量のデータの転送を保存できるようになります。  
  
## <a name="see-also"></a>参照  
 [ODBC&#41;&#40;結果の処理 ](../../relational-databases/native-client-odbc-results/processing-results-odbc.md)  
  
  
