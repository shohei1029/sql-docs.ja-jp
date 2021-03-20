---
title: ODBC でのトランザクション |Microsoft Docs
description: ODBC では、接続レベルでトランザクションを管理し、自動コミットモードまたは手動コミットモードで、完了したすべての作業をコミットまたはロールバックします。
ms.custom: ''
ms.date: 03/03/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- SQL Server Native Client ODBC driver, transactions
- transactions [ODBC]
- ODBC, transactions
ms.assetid: c5a87fa5-827a-4e6f-a0d9-924bac881eb0
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: c80f182c6d8403c9b685bcf8b80b2e97765a3e33
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104755732"
---
# <a name="performing-transactions-in-odbc"></a>ODBC でのトランザクションの実行
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  ODBC のトランザクションは接続レベルで管理されます。 アプリケーションはトランザクションの完了時に、その接続のすべてのステートメント ハンドルで完了したすべての作業を、コミットまたはロールバックします。 トランザクションをコミットまたはロールバックするには、COMMIT または ROLLBACK ステートメントを送信するのではなく、アプリケーションで [SQLEndTran](../../../relational-databases/native-client-odbc-api/sqlendtran.md) を呼び出す必要があります。  
  
 アプリケーションは、 [SQLSetConnectAttr](../../../relational-databases/native-client-odbc-api/sqlsetconnectattr.md) を呼び出して、トランザクションを管理する2つの ODBC モードを切り替えます。  
  
-   自動コミット モード  
  
     各ステートメントは、正常に完了したときに自動的にコミットされます。 自動コミット モードで実行するときは、他のトランザクション管理関数は必要ありません。  
  
-   手動コミット モード  
  
     実行されたすべてのステートメントは、 **SQLEndTran** を呼び出すことによって明示的に停止されるまで、同じトランザクションに含まれます。  
  
 自動コミット モードは、ODBC の既定のトランザクション モードです。 接続が確立されると、 **SQLSetConnectAttr** が呼び出され、自動コミットモードをオフに設定して手動コミットモードに切り替えるまで、自動コミットモードになります。 アプリケーションが自動コミットを無効にすると、次にデータベースに送信されるステートメントでトランザクションが開始されます。 その後、トランザクションは、アプリケーションが SQL_COMMIT オプションまたは SQL_ROLLBACK オプションを使用して **SQLEndTran** を呼び出すまで有効になります。 **SQLEndTran** の後にデータベースに送信されるコマンドは、次のトランザクションを開始します。  
  
 手動コミット モードから自動コミット モードに切り替えると、ドライバーは接続で現在開かれているすべてのトランザクションをコミットします。  
  
 ODBC アプリケーションで BEGIN TRANSACTION、COMMIT TRANSACTION、ROLLBACK TRANSACTION などの Transact-SQL トランザクション ステートメントを使用すると、ドライバーの動作が不確定になる可能性があるので、このような Transact-SQL トランザクション ステートメントは使用しないでください。 ODBC アプリケーションは自動コミットモードで実行する必要があり、トランザクション管理関数やステートメントを使用したり、手動コミットモードで実行したり、ODBC **SQLEndTran** 関数を使用してトランザクションをコミットまたはロールバックしたりする必要があります。  
  
## <a name="see-also"></a>参照  
 [ODBC&#41;&#40;のトランザクションの実行 ]()  
  
