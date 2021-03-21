---
description: 高速順方向専用カーソル (ODBC)
title: 高速 Forward-Only カーソル (ODBC) |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- fast forward-only cursors
- forward-only cursors
- cursors [ODBC], fast forward-only
- ODBC cursors, fast forward-only
ms.assetid: 0707d07e-fc95-42ed-9280-b7e508ac8c62
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 3b0d9030c4e2bd86612e41c2fad97905a29f11de
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104756232"
---
# <a name="fast-forward-only-cursors-odbc"></a>高速順方向専用カーソル (ODBC)
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  Native Client ODBC ドライバーは、のインスタンスに接続されている場合、 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 順方向専用の読み取り専用カーソルのパフォーマンスの最適化をサポートしています。 高速順方向専用カーソルは、既定の結果セットに類似した方法で、ドライバーとサーバーによって内部的に実装されます。 高速順方向専用カーソルには、パフォーマンスが高いこと以外にも、次のような特性があります。  
  
-   [SQLGetData](../../../relational-databases/native-client-odbc-api/sqlgetdata.md) はサポートされていません。 結果セットの列を、プログラム変数にバインドする必要があります。  
  
-   サーバーはカーソルの最後に達したことを検出すると、そのカーソルを自動的に閉じます。 アプリケーションは引き続き[SqlcloSQLFreeStmt](../../../relational-databases/native-client-odbc-api/sqlclosecursor.md)または[](../../../relational-databases/native-client-odbc-api/sqlfreestmt.md)(SQL_CLOSE) を呼び出す必要がありますが、ドライバーからサーバーにクローズ要求を送信する必要はありません。 この動作により、ネットワーク経由でのサーバーとのやり取りが抑えられます。  
  
 アプリケーションでは、ドライバー固有のステートメント属性 SQL_SOPT_SS_CURSOR_OPTIONS を使用して高速順方向専用カーソルを要求します。 SQL_SOPT_SS_CURSOR_OPTIONS 属性に SQL_CO_FFO を設定すると、高速順方向専用カーソルが有効になりますが、autofetch オプションは有効になりません。 SQL_CO_FFO_AF を設定すると、autofetch オプションも有効になります。 Autofetch の詳細については、「 [Using autofetch WITH ODBC](../../../relational-databases/native-client-odbc-cursors/programming/using-autofetch-with-odbc-cursors.md)cursor」を参照してください。  
  
 autofetch オプションを有効にした高速順方向専用カーソルは、サーバーとのやり取りを 1 度だけ行って小さい結果セットを取得する場合に使用できます。 次の手順では、 *n* が返される行の数を示します。  
  
1.  SQL_SOPT_SS_CURSOR_OPTIONS を SQL_CO_FFO_AF に設定します。  
  
2.  SQL_ATTR_ROW_ARRAY_SIZE を *n* + 1 に設定します。  
  
3.  結果列を *n* + 1 個の要素の配列にバインドします ( *n* + 1 行が実際にフェッチされた場合は安全です)。  
  
4.  **SQLExecDirect** または **sqlexecute** のいずれかを使用してカーソルを開きます。  
  
5.  返された状態が SQL_SUCCESS の場合は、 **SQLFreeStmt** または **sqlcloを** 呼び出してカーソルを閉じます。 行のすべてのデータが、バインドされたプログラム変数に格納されます。  
  
 これらの手順で、 **SQLExecDirect** または **sqlexecute** は、autofetch オプションが有効になっている状態で、cursor open 要求を送信します。 クライアントからこの要求を 1 つ受け取ると、サーバーでは次の手順が実行されます。  
  
-   カーソルを開きます。  
  
-   結果セットを作成してクライアントに行を送信します。  
  
-   行セットのサイズを結果セットの行数よりも 1 だけ多いサイズに設定したので、サーバーでカーソルが最後まで達したことが検出され、そのカーソルが閉じられます。  
  
## <a name="see-also"></a>参照  
 [ODBC&#41;&#40;のカーソルプログラミングの詳細 ](../../../relational-databases/native-client-odbc-cursors/programming/cursor-programming-details-odbc.md)  
  
  
