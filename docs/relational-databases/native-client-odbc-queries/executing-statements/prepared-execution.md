---
description: 準備実行
title: 実行準備 |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- deferred statement preparation
- prepared execution [ODBC]
- SQLPrepare function
- ODBC applications, statements
- SQLExecute function
- statements [ODBC], prepared execution
ms.assetid: f3a9d32b-6cd7-4f0c-b38d-c8ccc4ee40c3
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 8da07014b33b710a2036f5e411b3310180eaae02
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104752892"
---
# <a name="prepared-execution"></a>準備実行
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  ODBC API では、準備実行とは、[!INCLUDE[tsql](../../../includes/tsql-md.md)] ステートメントを繰り返し実行する際に関連して生じる解析やコンパイルのオーバーヘッドを削減する方法と定義されています。 アプリケーションでは SQL ステートメントを保持する文字列を構築してから、そのステートメントを 2 段階に分けて実行します。 [SQLPrepare 関数](../../../odbc/reference/syntax/sqlprepare-function.md)を1回呼び出して、ステートメントが解析され、によって実行プランにコンパイルされるようにし [!INCLUDE[ssDE](../../../includes/ssde-md.md)] ます。 次に、準備された実行プランの各実行に対して **Sqlexecute** を呼び出します。 この方法では、各実行にかかる解析とコンパイルのオーバーヘッドが抑制されます。 準備実行は、通常、同一のパラメーター化された SQL ステートメントを繰り返し実行するアプリケーションで使用されます。  
  
 ほとんどのデータベースでは、直接実行されるステートメントが実行のたびにコンパイルされるのに対し、準備実行されるステートメントは 1 回だけコンパイルされるので、準備実行は主に 4、5 回以上実行されるステートメントの場合は直接実行よりも高速になります。 準備実行では、SQL ステートメントが実行されるたびに、ドライバーはステートメント全体ではなく、実行プランの識別子とパラメーター値だけをデータ ソースに送信できるので、ネットワーク トラフィックも削減できます。  
  
 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]**SQLExecDirect** から実行プランを検出して再利用するための強化されたアルゴリズムにより、直接実行と準備実行のパフォーマンスの差異を軽減します。 そのため、直接実行されるステートメントでも、準備実行のパフォーマンス上の利点の一部を利用できるようになります。 詳細については、「 [直接実行](../../../relational-databases/native-client-odbc-queries/executing-statements/direct-execution.md)」を参照してください。  
  
 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] では、準備実行がネイティブにサポートされます。 実行プランは **SQLPrepare** 上に構築され、後で **sqlexecute** が呼び出されたときに実行されます。 で [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] は、 **SQLPrepare** で一時ストアドプロシージャを構築する必要がないため、 **tempdb** のシステムテーブルに余分なオーバーヘッドが発生することはありません。  
  
 パフォーマンス上の理由から、 **Sqlexecute** が呼び出されるか、メタプロパティ操作 (ODBC での [SQLDescribeCol](../../../relational-databases/native-client-odbc-api/sqldescribecol.md) や [SQLDescribeParam](../../../relational-databases/native-client-odbc-api/sqldescribeparam.md) など) が実行されるまで、ステートメントの準備は遅延されます。 これが既定の動作です。 準備されているステートメントのエラーは、ステートメントまたはメタプロパティ操作が実行されるまでわかりません。 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Native Client ODBC ドライバー固有のステートメント属性の SQL_SOPT_SS_DEFER_PREPARE を SQL_DP_OFF に設定することで、この既定の動作を無効にできます。  
  
 準備の遅延が発生した場合、 **Sqlexecute** を呼び出す前に **SQLDescribeCol** または **SQLDescribeParam** を呼び出すと、サーバーへの追加のラウンドトリップが生成されます。 **SQLDescribeCol** では、ドライバーはクエリから WHERE 句を削除し、SET FMTONLY ON を使用してサーバーに送信します。これにより、クエリによって返される最初の結果セットの列の説明が取得されます。 **SQLDescribeParam** では、ドライバーはサーバーを呼び出して、クエリ内の任意のパラメーターマーカーによって参照される式または列の説明を取得します。 この方法には、サブクエリのパラメーターを解決することができないなど、いくつか制限事項もあります。  
  
 Native Client ODBC ドライバーで **SQLPrepare** を過度に使用する [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] と、特に以前のバージョンの SQL Server に接続している場合に、パフォーマンスが低下します。 準備実行は、1 回しか実行されないステートメントには使用しないでください。 準備実行では、クライアントからサーバーへのネットワーク上のやり取りを余分に実行する必要があるため、ステートメントを 1 回しか実行しない場合は直接実行よりも時間がかかります。 以前のバージョンの [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] では、準備実行で一時ストアド プロシージャの生成も行います。  
  
 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] では、準備されたステートメントを使用して一時オブジェクトを作成することはできません。  
  
 一部の初期 ODBC アプリケーションでは、 [SQLBindParameter](../../../relational-databases/native-client-odbc-api/sqlbindparameter.md)を使用したときに **SQLPrepare** が使用されていました。 **SQLBindParameter** では、 **SQLPrepare** を使用する必要はありません。 **SQLExecDirect** と共に使用できます。 たとえば、 **SQLExecDirect** を **SQLBindParameter** と共に使用すると、ストアドプロシージャから返されるリターンコードまたは出力パラメーターが1回だけ実行されます。 同じステートメントが複数回実行される場合を除き、 **SQLPrepare** と **SQLBindParameter** を同時に使用しないでください。  
  
## <a name="see-also"></a>参照  
 [ODBC&#41;&#40;のステートメントの実行 ](../../../relational-databases/native-client-odbc-queries/executing-statements/executing-statements-odbc.md)  
  
