---
description: ステートメントの使用 (ODBC)
title: ステートメントを使用する (ODBC) |Microsoft Docs
ms.custom: ''
ms.date: 03/06/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- statements [ODBC]
ms.assetid: f7573f8f-6f21-4e03-8dd5-a5f2ea4878cc
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: c20165767d0b516f5c1a13fad06748a5dc979d5d
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104733994"
---
# <a name="use-a-statement-odbc"></a>ステートメントの使用 (ODBC)
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

    
### <a name="to-use-a-statement"></a>ステートメントを使用するには  
  
1.  *HandleType* を SQL_HANDLE_STMT として [SQLAllocHandle](../../../odbc/reference/syntax/sqlallochandle-function.md) を呼び出し、ステートメント ハンドルを割り当てます。  
  
2.  また、[SQLSetStmtAttr](../../../relational-databases/native-client-odbc-api/sqlsetstmtattr.md) を呼び出してステートメント オプションを設定するか、[SQLGetStmtAttr](../../../relational-databases/native-client-odbc-api/sqlgetstmtattr.md) を呼び出してステートメント属性を取得することもできます。  
  
     サーバー カーソルを使用するには、カーソルの属性を既定値以外の値に設定する必要があります。  
  
3.  また、ステートメントを複数回実行する場合は、[SQLPrepare 関数](../../../odbc/reference/syntax/sqlprepare-function.md)を使用して実行するステートメントを準備します。  
  
4.  ステートメントにバインドされたパラメーター マーカーが含まれている場合は、必要に応じて、[SQLBindParameter](../../../relational-databases/native-client-odbc-api/sqlbindparameter.md) を使用してパラメーター マーカーをプログラム変数にバインドします。 ステートメントが準備されている場合は、[SQLNumParams](../../../odbc/reference/syntax/sqlnumparams-function.md) および [SQLDescribeParam](../../../relational-databases/native-client-odbc-api/sqldescribeparam.md) を呼び出して、パラメーターの数と特性を検索できます。  
  
5.  SQLExecDirect を使用してステートメントを直接実行します。  
  
     \- または  
  
     ステートメントが準備されている場合は、[SQLExecute](../../../odbc/reference/syntax/sqlexecute-function.md) を使用してそのステートメントを複数回実行します。  
  
     \- または  
  
     カタログ関数を呼び出すと、結果が返されます。  
  
6.  結果セット列をプログラム変数にバインドするか、[SQLGetData](../../../relational-databases/native-client-odbc-api/sqlgetdata.md) を使用して結果セット列からプログラム変数にデータを移動するか、あるいはこれらの 2 つの方法を組み合わせて結果を処理します。  
  
     ステートメントの結果セットを一度に 1 行ずつフェッチします。  
  
     \- または  
  
     ブロック カーソルを使用して一度に複数行の結果セットをフェッチします。  
  
     \- または  
  
     [SQLRowCount](../../../relational-databases/native-client-odbc-api/sqlrowcount.md) を呼び出して、INSERT、UPDATE、または DELETE ステートメントの影響を受ける行数を確認します。  
  
     SQL ステートメントに複数の結果セットが含まれている可能性がある場合は、各結果セットの最後に [SQLMoreResults](../../../relational-databases/native-client-odbc-api/sqlmoreresults.md) を呼び出して、処理する追加の結果セットがあるかどうかを確認します。  
  
7.  結果が処理されたら、ステートメント ハンドルで新しいステートメントを実行できるように、次のアクションが必要な場合があります。  
  
    -   SQL_NO_DATA が返されるまで [SQLMoreResults](../../../relational-databases/native-client-odbc-api/sqlmoreresults.md) を呼び出さなかった場合は、[SQLCloseCursor](../../../relational-databases/native-client-odbc-api/sqlclosecursor.md) を呼び出してカーソルを閉じます。  
  
    -   パラメーター マーカーをプログラム変数にバインドした場合は、*Option* を SQL_RESET_PARAMS に設定して [SQLFreeStmt](../../../relational-databases/native-client-odbc-api/sqlfreestmt.md) を呼び出し、バインドされたパラメーターを解放します。  
  
    -   結果セット列をプログラム変数にバインドした場合は、*Option* を SQL_UNBIND に設定して [SQLFreeStmt](../../../relational-databases/native-client-odbc-api/sqlfreestmt.md) を呼び出し、バインドされた列を解放します。  
  
    -   ステートメント ハンドルを再利用するには、手順 2. に進みます。  
  
8.  *HandleType* を SQL_HANDLE_STMT として [SQLFreeHandle](../../../relational-databases/native-client-odbc-api/sqlfreehandle.md) を呼び出し、ステートメント ハンドルを解放します。  
  
## <a name="see-also"></a>参照  
 [クエリの実行方法に関するトピック &#40;ODBC&#41;](../../../relational-databases/native-client-odbc-how-to/execute-queries/executing-queries-how-to-topics-odbc.md)  
  
