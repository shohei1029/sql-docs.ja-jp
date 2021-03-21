---
description: 位置指定更新 (ODBC)
title: 位置指定更新 (ODBC) |Microsoft Docs
ms.custom: ''
ms.date: 03/06/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- SQL Server Native Client ODBC driver, cursors
- SQLSetPos function
- SQLSetCursorName function
- ODBC applications, cursors
- cursors [ODBC], positioned updates
- positioned updates [ODBC]
- ODBC cursors, positioned updates
ms.assetid: ff404e02-630f-474d-b5d4-06442b756991
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 4b75d3ca4e493804325680954375bf3ae2850ca8
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104756272"
---
# <a name="positioned-updates-odbc"></a>位置指定更新 (ODBC)
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  ODBC では、カーソルで位置指定更新を実行するために、次の 2 とおりの方法をサポートします。  
  
-   **SQLSetPos**  
  
-   WHERE CURRENT OF 句  
  
 最も一般的な方法は、 **SQLSetPos** を使用することです。 次のオプションがあります。  
  
 SQL_POSITION  
 現在の行セットの特定行にカーソルを位置付けます。  
  
 SQL_REFRESH  
 カーソルが現在位置付けられている行から取り出した値で、結果セット列にバインドされているプログラム変数を更新します。  
  
 SQL_UPDATE  
 結果セット列にバインドされているプログラム変数に格納されている値で、カーソルの現在行を更新します。  
  
 SQL_DELETE  
 カーソルの現在行を削除します。  
  
 **SQLSetPos** は、ステートメントハンドルカーソル属性がサーバーカーソルを使用するように設定されている場合に、任意のステートメントの結果セットと共に使用できます。 結果セットの列を、プログラム変数にバインドする必要があります。 アプリケーションから行がフェッチされるとすぐに、 **SQLSetPos**(SQL_POSTION) を呼び出して、行にカーソルを置きます。 その後、アプリケーションは SQLSetPos(SQL_DELETE) を呼び出して現在行を削除するか、新しいデータ値をバインドされているプログラム変数に移動し、SQLSetPos(SQL_UPDATE) を呼び出して現在行を更新します。  
  
 アプリケーションは、 **SQLSetPos** を使用して行セット内の任意の行を更新または削除できます。 **SQLSetPos** の呼び出しは、SQL ステートメントの構築と実行に代わる便利な方法です。 **SQLSetPos** は現在の行セットに対して動作し、 [sqlfetchscroll](../../relational-databases/native-client-odbc-api/sqlfetchscroll.md)の呼び出しの後にのみ使用できます。  
  
 行セットサイズは、SQL_ATTR_ROW_ARRAY_SIZE の属性引数を使用して [SQLSetStmtAttr](../../relational-databases/native-client-odbc-api/sqlsetstmtattr.md) を呼び出すことによって設定されます。 **SQLSetPos** は新しい行セットサイズを使用しますが、 **sqlfetch** または **sqlfetchscroll** を呼び出した後に限られます。 たとえば、行セットのサイズが変更された場合、 **SQLSetPos** が呼び出され、 **Sqlfetch** または **sqlfetchscroll** が呼び出されます。 **SQLSetPos** の呼び出しでは、古い行セットサイズが使用されますが、 **sqlfetch** または **sqlfetchscroll** では新しい行セットサイズが使用されます。  
  
 行セット内の先頭行の行番号は 1 です。 **SQLSetPos** の RowNumber 引数は、行セット内の行を識別する必要があります。つまり、その値は、1から最後にフェッチされた行の数までの範囲で指定する必要があります。 この値には、行セット サイズよりも小さい値を指定できます。 RowNumber が 0 の場合、操作は行セット内のすべての行に適用されます。  
  
 **SQLSetPos** の削除操作により、データソースはテーブルの選択された1つ以上の行を削除します。 **Sqlsetpos** を使用して行を削除するには、アプリケーションは、操作が SQL_DELETE に設定された **sqlsetpos** を呼び出し、RowNumber を削除する行の番号に設定します。 RowNumber が 0 の場合は、行セット内のすべての行が削除されます。  
  
 **SQLSetPos** が戻った後、削除された行が現在の行になり、その状態が SQL_ROW_DELETED になります。 この行は、 [SQLGetData](../../relational-databases/native-client-odbc-api/sqlgetdata.md) や **SQLSetPos** の呼び出しなど、その他の位置指定操作では使用できません。  
  
 行セットのすべての行を削除すると (RowNumber は0に等しくなります)、アプリケーションでは、 **SQLSetPos** の更新操作と同様に、行操作配列を使用してドライバーが特定の行を削除できないようにすることができます。  
  
 削除対象の各行は、行セット内に存在する行でなければなりません。 フェッチによってアプリケーション バッファーが設定され、行の状態配列が維持されている場合は、これら各行位置の行の状態値が SQL_ROW_DELETED、SQL_ROW_ERROR、または SQL_ROW_NOROW であってはなりません。  
  
 位置指定更新は、UPDATE、DELETE、および INSERT の各ステートメントに WHERE CURRENT OF 句を使用することによっても実行できます。 の現在のの部分には、 [Sqlgetcursor name](../../relational-databases/native-client-odbc-api/sqlgetcursorname.md) 関数が呼び出されたとき、または **SQLSetCursorName** を呼び出して指定できる、ODBC によって生成されるカーソル名が必要です。 次に、ODBC アプリケーションで WHERE CURRENT OF 更新の実行に使用する一般的な手順を示します。  
  
-   **SQLSetCursorName** を呼び出して、ステートメントハンドルのカーソル名を設定します。  
  
-   FOR UPDATE OF 句を指定した SELECT ステートメントを作成し、実行します。  
  
-   行を取得するには、 **Sqlfetchscroll** を呼び出して行セットまたは **sqlfetch** を取得します。  
  
-   **SQLSetPos** (SQL_POSITION) を呼び出して、行にカーソルを置きます。  
  
-   **SQLSetCursorName** で設定したカーソル名を使用して、WHERE CURRENT OF 句を指定した UPDATE ステートメントをビルドして実行します。  
  
 または、SELECT ステートメントを実行する前に **SQLSetCursorName** を呼び出すのではなく、select ステートメントを実行した後に **Sqlgetカーソル名** を呼び出すこともできます。 **SQLSetCursorName** を使用してカーソル名を設定しなかった場合、 **SQLGETCURSOR name** は ODBC によって割り当てられた既定のカーソル名を返します。  
  
 サーバーカーソルを使用している場合は、 **SQLSetPos** を使用することをお勧めします。 ODBC カーソル ライブラリで静的で更新可能なカーソルを使用している場合、カーソル ライブラリは、基になるテーブルのキー値を指定した WHERE 句を追加することで、WHERE CURRENT OF 更新を実装します。 テーブル内のキーが一意でない場合、意図しない更新が行われることがあります。  
  
## <a name="see-also"></a>参照  
 [ODBC&#41;&#40;カーソルの使用 ](../../relational-databases/native-client-odbc-cursors/using-cursors-odbc.md)  
  
  
