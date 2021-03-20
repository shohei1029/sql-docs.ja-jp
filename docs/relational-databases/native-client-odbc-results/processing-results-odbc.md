---
title: 結果の処理 (ODBC) |Microsoft Docs
description: ODBC アプリケーションが SQL ステートメントを送信するときに SQL Server が返すデータの処理について説明します。
ms.custom: ''
ms.date: 03/16/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- result sets [ODBC], about result sets
- SQLRowCount function
- SQL Server Native Client ODBC driver, result sets
- ODBC applications, result sets
- COMPUTE clause
- result sets [ODBC]
- COMPUTE BY clause
ms.assetid: 61a8db19-6571-47dd-84e8-fcc97cb60b45
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: c3c5b8bad410418a95a9ba180ab3921eb02e0644
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104752772"
---
# <a name="processing-results-odbc"></a>結果の処理 (ODBC)
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  アプリケーションで SQL ステートメントが実行されると、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では結果として生成されるすべてのデータを 1 つ以上の結果セットとして返します。 結果セットとは、クエリの条件に一致する行と列の集まりです。 SELECT ステートメント、カタログ関数、および一部のストアド プロシージャでは、アプリケーションで使用できる結果セットが表形式で生成されます。 実行される SQL ステートメントがストアド プロシージャ、複数のコマンドを含むバッチ、またはキーワードを含む SELECT ステートメントの場合、処理対象の結果セットが複数生成されます。  
  
 ODBC カタログ関数では、データを取得することもできます。 たとえば、 [Sqlcolumns](../../relational-databases/native-client-odbc-api/sqlcolumns.md) はデータソース内の列に関するデータを取得します。 これらの結果セットには、0 行以上の行を含めることができます。  
  
 GRANT や REVOKE など、結果セットが返されない SQL ステートメントもあります。 これらのステートメントでは、通常、 **Sqlexecute** または **SQLExecDirect** からのリターンコードだけがステートメントが成功したことを示しています。  
  
 INSERT ステートメント、UPDATE ステートメント、および DELETE ステートメントからは、変更によって処理された行数だけを含む結果セットが返されます。 この数は、アプリケーションが [SQLRowCount](../../relational-databases/native-client-odbc-api/sqlrowcount.md)を呼び出したときに使用できるようになります。 ODBC 3.*x* アプリケーションは、 **SQLRowCount** を呼び出して結果セットを取得するか、 [sqlmoreresults](../../relational-databases/native-client-odbc-api/sqlmoreresults.md) を呼び出して取り消す必要があります。 複数の INSERT、UPDATE、または DELETE ステートメントを含むバッチまたはストアドプロシージャを実行する場合は、各変更ステートメントの結果セットを **SQLRowCount** を使用して処理するか、 **Sqlmoreresults** を使用して取り消す必要があります。 バッチやストアド プロシージャに SET NOCOUNT ON ステートメントを含めることで、これらの数をキャンセルできます。  
  
 Transact-SQL には、NOCOUNT ステートメントが含まれています。 NOCOUNT オプションが on に設定されている場合、SQL Server は、ステートメントの影響を受ける行の数を返しません。また、 **SQLRowCount** は0を返します。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]Native CLIENT ODBC ドライバーのバージョンでは、SQL_SOPT_SS_NOCOUNT_STATUS、NOCOUNT オプションがオンとオフのどちらであるかを報告するために、ドライバー固有の[SQLGetStmtAttr](../../relational-databases/native-client-odbc-api/sqlgetstmtattr.md)オプションが導入されています。 **SQLRowCount** は常に0を返し、アプリケーションは SQL_SOPT_SS_NOCOUNT_STATUS をテストする必要があります。 SQL_NC_ON が返された場合、 **SQLRowCount** の値が0の場合は、SQL Server によって行数が返されなかったことを示します。 SQL_NC_OFF が返された場合は、NOCOUNT が無効になっていることを意味し、 **SQLRowCount** からの値が0の場合は、ステートメントが行に影響していないことを示します。 SQL_SOPT_SS_NOCOUNT_STATUS が SQL_NC_OFF 場合、アプリケーションには **SQLRowCount** の値が表示されません。 大きなバッチやストアド プロシージャには、複数の SET NOCOUNT ステートメントが含まれていることがあるので、プログラマは SQL_SOPT_SS_NOCOUNT_STATUS が一定であると想定することはできません。 **SQLRowCount** が0を返すたびに、オプションをテストする必要があります。  
  
 他のいくつかの Transact-SQL ステートメントは、結果セットではなく、メッセージにデータを含めて返します。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]Native CLIENT ODBC ドライバーは、これらのメッセージを受信すると SQL_SUCCESS_WITH_INFO を返して、情報メッセージが使用可能であることをアプリケーションに知らせます。 その後、アプリケーションは **SQLGetDiagRec** を呼び出してこれらのメッセージを取得できます。 このように機能する [!INCLUDE[tsql](../../includes/tsql-md.md)] ステートメントを次に示します。  
  
-   DBCC  
  
-   SET SHOWPLAN (以前のバージョンの SQL Server で使用可能)  
  
-   SET STATISTICS  
  
-   PRINT  
  
-   RAISERROR  
  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]Native CLIENT ODBC ドライバーは、重大度が11以上の RAISERROR で SQL_ERROR を返します。 RAISERROR の重大度が 19 以上の場合は、接続も削除されます。  
  
 アプリケーションでは、SQL ステートメントから返される結果セットを処理するために次の処理を行います。  
  
-   結果セットの特性を判断します。  
  
-   プログラム変数に列をバインドします。  
  
-   1 つの値、値の行全体、または値の複数の行を取得します。  
  
-   さらに結果セットが存在するかどうかを確認するテストを行い、存在する場合は、新しい結果セットの特性を判断する処理まで戻って、それ以降をループ処理します。  
  
 データ ソースから行を取得し、取得した行をアプリケーションに返す処理をフェッチと呼びます。  
  
## <a name="in-this-section"></a>このセクションの内容  
  
-   [ODBC&#41;&#40;結果セットの特性の決定 ](../../relational-databases/native-client-odbc-results/determining-the-characteristics-of-a-result-set-odbc.md)  
  
-   [ストレージの割り当て](../../relational-databases/native-client-odbc-results/assigning-storage.md)  
  
-   [結果データのフェッチ](../../relational-databases/native-client-odbc-results/fetching-result-data.md)  
  
-   [ODBC&#41;&#40;のデータ型のマッピング ](../../relational-databases/native-client-odbc-results/mapping-data-types-odbc.md)  
  
-   [データ型の使用方法](../../relational-databases/native-client-odbc-results/data-type-usage.md)  
  
-   [文字データの自動変換](../../relational-databases/native-client-odbc-results/autotranslation-of-character-data.md)  
  
## <a name="see-also"></a>参照  
 [SQL Server Native Client &#40;ODBC&#41;](../../relational-databases/native-client/odbc/sql-server-native-client-odbc.md)   
 [結果の処理方法に関するトピック &#40;ODBC&#41;](../native-client-odbc-how-to/processing-results-process-results.md)  
  
