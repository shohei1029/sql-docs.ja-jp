---
title: クエリの実行 (ODBC) |Microsoft Docs
description: ODBC アプリケーションでは、接続ハンドルを初期化し、データソースに接続することによって、SQL Server インスタンスでステートメントを実行できます。
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- ODBC applications, executing queries
- SQL Server Native Client ODBC driver, statements
- ODBC applications, statements
- SQL Server Native Client ODBC driver, queries
- queries [ODBC]
ms.assetid: d935bcba-8ce6-4159-8395-6c86431602ad
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: b686da10b222ed26c5a234c81f6f2314d9e9a2f1
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104751292"
---
# <a name="executing-queries-odbc"></a>クエリの実行 (ODBC)
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  ODBC アプリケーションでは、接続ハンドルを初期化してデータ ソースに接続した後、その接続ハンドルに 1 つ以上のステートメント ハンドルを割り当てます。 その後、アプリケーションは [!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ステートメントハンドルに対してステートメントを実行できます。 次に、SQL ステートメントを実行するときの一般的な手順を示します。  
  
1.  必要なステートメント属性を設定します。  
  
2.  ステートメントを構築します。  
  
3.  ステートメントを実行します。  
  
4.  任意の結果セットを取得します。  
  
 アプリケーションは、SQL ステートメントから返されたすべての結果セット内にあるすべての行を取得した後、同一ステートメント ハンドルで別のクエリを実行できます。 アプリケーションで、特定の結果セット内のすべての行を取得する必要がないと判断した場合は、 [Sqlmoreresults](../../relational-databases/native-client-odbc-api/sqlmoreresults.md) または [sqlcloの](../../relational-databases/native-client-odbc-api/sqlclosecursor.md)いずれかを呼び出すことによって、結果セットの残りの部分を取り消すことができます。  
  
 ODBC アプリケーションで、異なるデータを使用して同じ SQL ステートメントを複数回実行する必要がある場合は、SQL ステートメントの構築時に、次のように疑問符 (?) で表されるパラメーター マーカーを使用します。  
  
```  
INSERT INTO MyTable VALUES (?, ?, ?)  
```  
  
 その後、 [SQLBindParameter](../../relational-databases/native-client-odbc-api/sqlbindparameter.md)を呼び出すことによって、各パラメーターマーカーをプログラム変数にバインドできます。  
  
 アプリケーションは、すべての SQL ステートメントの実行と結果セットの処理を完了後、ステートメント ハンドルを解放します。  
  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]Native CLIENT ODBC ドライバーでは、接続ハンドルごとに複数のステートメントハンドルがサポートされています。 また、トランザクションは接続レベルで管理されます。これにより、1 つの接続ハンドルのすべてのステートメント ハンドルで実行されるすべての作業が、同一のトランザクションの一部として管理されます。  
  
## <a name="in-this-section"></a>このセクションの内容  
  
-   [ステートメント ハンドルの割り当て](../../relational-databases/native-client-odbc-queries/allocating-a-statement-handle.md)  
  
-   [ODBC&#41;&#40;SQL ステートメントの構築 ](../../relational-databases/native-client-odbc-queries/constructing-an-sql-statement-odbc.md)  
  
-   [カーソル用の SQL ステートメントの作成](../../relational-databases/native-client-odbc-queries/constructing-sql-statements-for-cursors.md)  
  
-   [ステートメント パラメーターの使用](../../relational-databases/native-client-odbc-queries/using-statement-parameters.md)  
  
-   [ODBC&#41;&#40;のステートメントの実行 ](../../relational-databases/native-client-odbc-queries/executing-statements/executing-statements-odbc.md)  
  
-   [ステートメント ハンドルの解放](../../relational-databases/native-client-odbc-queries/freeing-a-statement-handle.md)  
  
## <a name="see-also"></a>参照  
 [SQL Server Native Client &#40;ODBC&#41;](../../relational-databases/native-client/odbc/sql-server-native-client-odbc.md)  
  
  
