---
description: カーソル用の SQL ステートメントの作成
title: カーソルの SQL ステートメントの構築 |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- cursors [ODBC], statement construction
- ODBC applications, cursors
- SQL Server Native Client ODBC driver, statements
- statements [ODBC], constructing
- ODBC applications, statements
- statements [ODBC], cursors
ms.assetid: 134003fd-9c93-4f5c-a988-045990933b80
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: b294b77280449b275800ea75d12ca997ed1569b5
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104748002"
---
# <a name="constructing-sql-statements-for-cursors"></a>カーソル用の SQL ステートメントの作成
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]Native CLIENT odbc ドライバーでは、odbc 仕様で定義されているカーソル機能を実装するために、サーバーカーソルを使用します。 ODBC アプリケーションでは、 [SQLSetStmtAttr](../../relational-databases/native-client-odbc-api/sqlsetstmtattr.md) を使用して異なるステートメント属性を設定することによって、カーソル動作を制御します。 次に、属性とその既定値を示します。  
  
|属性|Default|  
|---------------|-------------|  
|SQL_ATTR_CONCURRENCY|SQL_CONCUR_READ_ONLY|  
|SQL_ATTR_CURSOR_TYPE|SQL_CURSOR_FORWARD_ONLY|  
|SQL_ATTR_CURSOR_SCROLLABLE|SQL_NONSCROLLABLE|  
|SQL_ATTR_CURSOR_SENSITIVITY|SQL_UNSPECIFIED|  
|SQL_ATTR_ROW_ARRAY_SIZE|1|  
  
 SQL ステートメントの実行時にこれらのオプションが既定値に設定されている場合、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native CLIENT ODBC ドライバーは、サーバーカーソルを使用して結果セットを実装するのではなく、既定の結果セットを使用します。 SQL ステートメントの実行時に、これらのオプションのいずれかが既定値から変更された場合、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native CLIENT ODBC ドライバーは、サーバーカーソルを使用して結果セットを実装しようとします。  
  
 既定の結果セットは、すべての [!INCLUDE[tsql](../../includes/tsql-md.md)] ステートメントをサポートします。 既定の結果セットを使用するときに実行できる SQL ステートメントの種類に制限はありません。  
  
 サーバー カーソルはすべての [!INCLUDE[tsql](../../includes/tsql-md.md)] ステートメントをサポートしているわけではありません。 複数の結果セットを生成する SQL ステートメントはサポートされません。  
  
 次に示す種類のステートメントは、サーバー カーソルではサポートされません。  
  
-   バッチ  
  
     2 つ以上の個別の SQL SELECT ステートメントから構成される SQL ステートメント。次に例を示します。  
  
    ```  
    SELECT * FROM Authors; SELECT * FROM Titles  
    ```  
  
-   複数の SELECT ステートメントを含むストアド プロシージャ  
  
     複数の SELECT ステートメントを含むストアド プロシージャを実行する SQL ステートメント。 パラメーターまたは変数を設定する SELECT ステートメントも該当します。  
  
-   キーワード  
  
     キーワード FOR BROWSE または INTO を伴う SELECT ステートメント。  
  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] で、上記の条件のいずれかに該当する SQL ステートメントをサーバー カーソルを使用して実行した場合、サーバー カーソルは暗黙的に既定の結果セットに変換されます。 **SQLExecDirect** または **sqlexecute** が SQL_SUCCESS_WITH_INFO を返すと、カーソル属性が既定の設定に戻ります。  
  
 上記の分類に該当しない SQL ステートメントは、ステートメント属性の設定がどのようであっても実行できます。既定の結果セット、サーバー カーソルを問わず、どちらも正常に機能します。  
  
## <a name="errors"></a>エラー  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 7.0 以降のバージョンで複数の結果セットが生成されるステートメントを実行すると、SQL_SUCCESS_WITH INFO および次のメッセージが生成されます。  
  
```  
SqlState: 01S02"  
pfNative: 0  
szErrorMsgString: "[Microsoft][SQL Server Native Client][SQL Server]  
               Cursor type changed."  
```  
  
 このメッセージを受信する ODBC アプリケーションは、 [SQLGetStmtAttr](../../relational-databases/native-client-odbc-api/sqlgetstmtattr.md) を呼び出して、現在のカーソル設定を確認できます。  
  
 サーバー カーソルを使用しているときに、複数の SELECT ステートメントから構成されるプロシージャを実行すると、次のエラーが発生します。  
  
```  
SqlState: 42000  
pfNative: 16937  
szErrorMsgString: [Microsoft][SQL Server Native Client][SQL Server]  
               A server cursor is not allowed on a stored procedure  
               with more than one SELECT statement in it. Use a  
               default result set or client cursor.  
```  
  
 サーバー カーソルを使用しているときに、複数の SELECT ステートメントから構成されるバッチを実行すると、次のエラーが発生します。  
  
```  
SqlState: 42000  
pfNative: 16938  
szErrorMsgString: [Microsoft][SQL Server Native Client][SQL Server]  
               sp_cursoropen. The statement parameter can only  
               be a single SELECT statement or a single stored   
               procedure.  
```  
  
 ODBC アプリケーションで上記のエラーが発生した場合、カーソルのすべてのステートメント属性を既定値に戻してからステートメントを実行する必要があります。  
  
## <a name="see-also"></a>参照  
 [ODBC&#41;&#40;クエリの実行 ](../../relational-databases/native-client-odbc-queries/executing-queries-odbc.md)  
  
  
