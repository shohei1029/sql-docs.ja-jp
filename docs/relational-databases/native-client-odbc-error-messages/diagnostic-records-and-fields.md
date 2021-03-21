---
description: 診断レコードと診断フィールド
title: 診断レコードとフィールド |Microsoft Docs
ms.custom: ''
ms.date: 03/17/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- header records [ODBC]
- SQL Server Native Client ODBC driver, errors
- messages [ODBC], diagnostic records
- ODBC error handling, diagnostic records
- SQLGetDiagField function
- diagnostic records [ODBC]
- errors [ODBC], diagnostic records
- fields [ODBC]
- status information [ODBC]
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 2b0cbc0573f733a1fd7f0c225160e90f9dd206ac
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104737682"
---
# <a name="diagnostic-records-and-fields"></a>診断レコードと診断フィールド
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  診断レコードは、ODBC 環境、接続、ステートメント、または記述子ハンドルに関連付けられています。 ODBC 関数から SQL_SUCCESS または SQL_INVALID_HANDLE 以外のリターン コードが返されるときは、その関数で呼び出されたハンドルに、情報メッセージまたはエラー メッセージが格納された診断レコードが関連付けられます。 これらの診断レコードは、同じハンドルを使用して別の関数が呼び出されるまで保持され、別の呼び出しが行われた時点で破棄されます。 1 つのハンドルに同時に関連付けることができる診断レコードの数に制限はありません。  
  
 診断レコードには、ヘッダー レコードと状態レコードの 2 種類があります。 ヘッダー レコードはレコード 0 です。状態レコードが存在する場合は、レコード 1 以降が状態レコードになります。 診断レコードのヘッダー レコードと状態レコードには、それぞれ異なるフィールドが含まれています。 また、ODBC コンポーネントでは、診断レコードに独自のフィールドを定義することもできます。  
  
 ヘッダー レコード内のフィールドには、リターン コード、行数、状態レコードの数、実行したステートメントの種類など、関数の実行に関する一般的な情報が保存されます。 ヘッダー レコードは、ODBC 関数から SQL_INVALID_HANDLE が返されない限り、常に作成されます。 ヘッダーレコード内のフィールドの完全な一覧については、「 [SQLGetDiagField](../../relational-databases/native-client-odbc-api/sqlgetdiagfield.md)」を参照してください。  
  
 状態レコード内のフィールドには、SQLSTATE、ネイティブ エラー番号、診断メッセージ、列番号、行番号など、ODBC ドライバー マネージャー、ドライバー、またはデータ ソースから返される特定のエラーや警告に関する情報が保存されます。 状態レコードは、関数から SQL_ERROR、SQL_SUCCESS_WITH_INFO、SQL_NO_DATA、SQL_NEED_DATA、または SQL_STILL_EXECUTING が返された場合のみ作成されます。 状態レコードのフィールドの完全な一覧については、「 **SQLGetDiagField**」を参照してください。  
  
 **SQLGetDiagRec** は、1つの診断レコードと、その ODBC SQLSTATE、ネイティブエラー番号、および診断メッセージフィールドを取得します。 この機能は、ODBC 2 に似ています。_x_**SQLError** 関数。 ODBC 3 の最も単純なエラー処理関数です。*x* は、 *recnumber* パラメーターを1に設定して **SQLGetDiagRec** を繰り返し呼び出し、 **SQLGetDiagRec** が SQL_NO_DATA を返すまで、 *recnumber* を1ずつインクリメントします。 これは、ODBC 2 に相当します。SQL_NO_DATA_FOUND が返されるまで、 **SQLError** を呼び出す *x* アプリケーション。  
  
 ODBC 3.*x* では、ODBC 2 よりも多くの診断情報がサポートされています。*x*。 この情報は、 **SQLGetDiagField** を使用して取得した診断レコードの追加フィールドに格納されます。  
  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]Native CLIENT ODBC ドライバーには、 **SQLGetDiagField** を使用して取得できるドライバー固有の診断フィールドがあります。 これらのドライバー固有のフィールドのラベルは、sqlncli.h で定義されています。 これらのラベルを使用して、各診断レコードに関連付けられた [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の状態、重大度レベル、サーバー名、プロシージャ名、および行番号を取得します。 また、sqlncli には、アプリケーションが *DiagIdentifier* を SQL_DIAG_DYNAMIC_FUNCTION_CODE に設定して **SQLGetDiagField** を呼び出す場合に、transact-sql ステートメントを識別するためにドライバーが使用するコードの定義が含まれています。  
  
 **SQLGetDiagField** は、基になるドライバーからキャッシュするエラー情報を使用して、ODBC ドライバーマネージャーによって処理されます。 ODBC ドライバー マネージャーでは、接続が正しく確立されるまでドライバー固有の診断フィールドをキャッシュしません。 **SQLGetDiagField** は、正常に接続が完了する前にドライバー固有の診断フィールドを取得するために呼び出された場合に SQL_ERROR を返します。 ODBC 接続関数から SQL_SUCCESS_WITH_INFO が返されても、その接続関数のドライバー固有の診断フィールドはまだ使用できません。 接続関数の後に別の ODBC 関数呼び出しを行った後でのみ、ドライバー固有の診断フィールドに対して **SQLGetDiagField** の呼び出しを開始できます。  
  
 Native Client ODBC ドライバーによって報告されるほとんどのエラー [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] は、 **SQLGetDiagRec** によって返された情報のみを使用して、効果的に診断することができます。 ただし、ドライバー固有の診断フィールドから返される情報がエラーを診断するうえで重要になることもあります。 Native Client ODBC ドライバーを使用してアプリケーションの ODBC エラーハンドラをコーディングする場合 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] は、 **SQLGetDiagField** を使用して、少なくとも SQL_DIAG_SS_MSGSTATE および SQL_DIAG_SS_SEVERITY ドライバ固有のフィールドを取得することをお勧めします。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] コード内の複数の箇所で特定のエラーが発生した可能性がある場合は、SQL_DIAG_SS_MSGSTATE により、エラーの厳密な発生箇所がマイクロソフトのサポート エンジニアに報告されます。この情報は、問題の診断に役立つことがあります。  
  
## <a name="see-also"></a>参照  
 [エラーとメッセージの処理](../../relational-databases/native-client-odbc-error-messages/handling-errors-and-messages.md)  
  
  
