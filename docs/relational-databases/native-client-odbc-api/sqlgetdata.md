---
description: SQLGetData
title: SQLGetData |Microsoft Docs
ms.custom: ''
ms.date: 03/17/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
apitype: DLLExport
helpviewer_keywords:
- SQLGetData function
ms.assetid: 204848be-8787-45b4-816f-a60ac9d56fcf
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 9790c6dce40e1e1be2e60cd49e90d734ae450a0a
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104749112"
---
# <a name="sqlgetdata"></a>SQLGetData
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  **SQLGetData** は、列の値をバインドせずに結果セットのデータを取得するために使用します。 **SQLGetData** を同じ列に対して連続して呼び出すと、 **text**、 **ntext**、または **image** データ型の列から大量のデータを取得できます。  
  
 アプリケーションでは、変数をバインドして結果セット データをフェッチする必要はありません。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] **SQLGetData** を使用して、Native Client ODBC ドライバーから任意の列のデータを取得できます。  
  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]Native CLIENT ODBC ドライバーでは、ランダム列の順序でデータを取得するための **SQLGetData** の使用はサポートされていません。 **SQLGetData** で処理されるすべてのバインドされていない列には、結果セット内のバインドされた列よりも大きな列序数が必要です。 アプリケーションでは、バインドされていない列の値を、列序数の小さい列から大きい列へと処理する必要があります。 前に処理した列よりも列序数が小さい列からデータを取得しようとすると、エラーが発生します。 アプリケーションで、結果セット行を報告するためにサーバー カーソルを使用している場合は、現在の行を再フェッチしてから列の値をフェッチできます。 ステートメントが既定の読み取り専用、順方向専用カーソルで実行される場合、ステートメントを再実行して **SQLGetData** をバックアップする必要があります。  
  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]Native CLIENT ODBC ドライバーでは、 **SQLGetData** を使用して取得した **text** 型、 **ntext** 型、および **image** 型のデータの長さが正確に報告されます。 アプリケーションでは、 *StrLen_or_IndPtr* パラメーターの戻り値を使用して、長いデータを迅速に取得することができます。  
  
> [!NOTE]  
>  大きな値型の場合、 *StrLen_or_IndPtr* はデータの切り捨て時に SQL_NO_TOTAL を返します。  
  
## <a name="sqlgetdata-support-for-enhanced-date-and-time-features"></a>SQLGetData による機能強化された日付と時刻のサポート  
 Date 型または time 型の結果列の値は、「 [SQL から C への変換](../../relational-databases/native-client-odbc-date-time/datetime-data-type-conversions-from-sql-to-c.md)」で説明されているように変換されます。  
  
 詳細については、「 [日付と時刻の機能強化 &#40;ODBC&#41;](../../relational-databases/native-client-odbc-date-time/date-and-time-improvements-odbc.md)」を参照してください。  
  
## <a name="sqlgetdata-support-for-large-clr-udts"></a>SQLGetData による大きな CLR UDT のサポート  
 **SQLGetData** は、大きな CLR ユーザー定義型 (udt) をサポートしています。 詳細については、「 [LARGE CLR User-Defined Types &#40;ODBC&#41;](../../relational-databases/native-client/odbc/large-clr-user-defined-types-odbc.md)」を参照してください。  
  
## <a name="example"></a>例  
  
```  
SQLHDBC     hDbc = NULL;  
SQLHSTMT    hStmt = NULL;  
long        lEmpID;  
PBYTE       pPicture;  
SQLINTEGER  pIndicators[2];  
  
// Get an environment, connection, and so on.  
...  
  
// Get a statement handle and execute a command.  
SQLAllocHandle(SQL_HANDLE_STMT, hDbc, &hStmt);  
  
if (SQLExecDirect(hStmt,  
    (SQLCHAR*) "SELECT EmployeeID, Photo FROM Employees",  
    SQL_NTS) == SQL_ERROR)  
    {  
    // Handle error and return.  
    }  
  
// Retrieve data from row set.  
SQLBindCol(hStmt, 1, SQL_C_LONG, (SQLPOINTER) &lEmpID, sizeof(long),  
    &pIndicators[0]);  
  
while (SQLFetch(hStmt) == SQL_SUCCESS)  
    {  
    cout << "EmployeeID: " << lEmpID << "\n";  
  
    // Call SQLGetData to determine the amount of data that's waiting.  
    if (SQLGetData(hStmt, 2, SQL_C_BINARY, pPicture, 0, &pIndicators[1])  
        == SQL_SUCCESS_WITH_INFO)  
        {  
        cout << "Photo size: " pIndicators[1] << "\n\n";  
  
        // Get all the data at once.  
        pPicture = new BYTE[pIndicators[1]];  
        if (SQLGetData(hStmt, 2, SQL_C_DEFAULT, pPicture,  
            pIndicators[1], &pIndicators[1]) != SQL_SUCCESS)  
            {  
            // Handle error and continue.  
            }  
  
        delete [] pPicture;  
        }  
    else  
        {  
        // Handle error on attempt to get data length.  
        }  
    }  
```  
  
## <a name="see-also"></a>参照  
 [SQLGetData 関数](../../odbc/reference/syntax/sqlgetdata-function.md)   
 [ODBC API 実装の詳細](../../relational-databases/native-client-odbc-api/odbc-api-implementation-details.md)  
  
