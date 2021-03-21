---
description: SQLGetDescField
title: SQLGetDescField |Microsoft Docs
ms.custom: ''
ms.date: 03/17/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
apitype: DLLExport
helpviewer_keywords:
- SQLGetDescField function
ms.assetid: 3e59a37a-28ee-4c91-8968-7fe3b966739d
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 8cd09522f8d42d2a6dd6e8290e7330f9fe638e7e
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104749122"
---
# <a name="sqlgetdescfield"></a>SQLGetDescField
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]Native CLIENT ODBC ドライバーでは、実装行記述子 (IRD) に対してのみドライバー固有の記述子フィールドが公開されます。 IRD 内で [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] は、ドライバー固有の列属性を介して記述子フィールドが参照されます。 使用可能なドライバー固有の記述子フィールドの完全な一覧については、「 [Sqlcolattribute](../../relational-databases/native-client-odbc-api/sqlcolattribute.md)」を参照してください。  
  
 列 ID 文字列を含む記述子フィールドは、多くの場合、長さが 0 の文字列になります。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 固有のすべての記述子フィールドの値は読み取り専用です。  
  
 SQLColAttribute で取得される属性と同様に、行レベルの属性 (SQL_CA_SS_COMPUTE_ID など) を報告する記述子フィールドは、結果セットのすべての列について報告されます。  
  
## <a name="sqlgetdescfield-and-table-valued-parameters"></a>SQLGetDescField とテーブル値パラメーター  
 SQLGetDescField を使用して、テーブル値パラメーターおよびテーブル値パラメーターの列の拡張属性の値を取得できます。 テーブル値パラメーターの詳細については、「 [テーブル値パラメーター &#40;ODBC&#41;](../../relational-databases/native-client-odbc-table-valued-parameters/table-valued-parameters-odbc.md)」を参照してください。  
  
## <a name="sqlgetdescfield-support-for-enhanced-date-and-time-features"></a>SQLGetDescField による機能強化された日付と時刻のサポート  
 新しい日付型または時刻型で使用できる記述子フィールドの詳細については、「 [パラメーターと結果のメタデータ](../../relational-databases/native-client-odbc-date-time/metadata-parameter-and-result.md)」を参照してください。  
  
 詳細については、「 [日付と時刻の機能強化 &#40;ODBC&#41;](../../relational-databases/native-client-odbc-date-time/date-and-time-improvements-odbc.md)」を参照してください。  
  
 以降で [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] は、アプリケーションで ODBC 3.8 が使用されている場合、SQLGetDescField は **SQL_C_BINARY** ではなく **SQL_C_SS_TIME2** ( **time** 型の場合) または **SQL_C_SS_TIMESTAMPOFFSET** ( **datetimeoffset** の場合) を返すことができます。  
  
## <a name="sqlgetdescfield-support-for-large-clr-udts"></a>SQLGetDescField による大きな CLR UDT のサポート  
 **SQLGetDescField** は、大きな CLR ユーザー定義型 (udt) をサポートしています。 詳細については、「 [LARGE CLR User-Defined Types &#40;ODBC&#41;](../../relational-databases/native-client/odbc/large-clr-user-defined-types-odbc.md)」を参照してください。  
  
## <a name="sqlgetdescfield-support-for-sparse-columns"></a>SQLGetDescField によるスパース列のサポート  
 SQLGetDescField を使用すると、新しい IRD フィールド SQL_CA_SS_IS_COLUMN_SET に対してクエリを実行し、列が **column_set** 列かどうかを判断できます。  
  
 詳細については、「 [スパース列のサポート &#40;ODBC&#41;](../../relational-databases/native-client/odbc/sparse-columns-support-odbc.md)」を参照してください。  
  
## <a name="example"></a>例  
  
```  
typedef struct tagCOMPUTEBYLIST  
    {  
    SQLSMALLINT nBys;  
    SQLSMALLINT aByList[1];  
    } COMPUTEBYLIST;  
typedef COMPUTEBYLIST* PCOMPUTEBYLIST;   
  
SQLHDESC    hIRD;   
SQLINTEGER  cbIRD;   
SQLINTEGER  nSet = 0;   
  
// . . .  
// Execute a statement that contains a COMPUTE clause,  
//  then get the descriptor handle of the IRD and  
//  get some IRD values.  
  
SQLGetStmtAttr(g_hStmt, SQL_ATTR_IMP_ROW_DESC,  
    (SQLPOINTER) &hIRD, sizeof(SQLHDESC), &cbIRD);  
  
// For statement-wide column attributes, any  
//  descriptor record will do. You know that 1 exists,  
//  so use it.  
SQLGetDescField(hIRD, 1, SQL_CA_SS_NUM_COMPUTES,  
    (SQLPOINTER) &nComputes, SQL_IS_INTEGER, &cbIRD);  
  
if (nSet == 0)  
    {  
    SQLINTEGER      nOrderID;  
  
    printf_s("Normal result set.\n");  
  
    for (nCol = 0; nCol < nCols; nCol++)  
        {  
        SQLGetDescField(hIRD, nCol+1,  
            SQL_CA_SS_COLUMN_ORDER,  
            (SQLPOINTER) &nOrderID, SQL_IS_INTEGER,  
            &cbIRD);  
  
        if (nOrderID != 0)  
            {  
            printf_s("Col in ORDER BY, pos: %ld",  
                nOrderID);  
            }  
            printf_s("\n");  
        }  
  
    printf_s("\n");  
    }  
else  
    {  
    PCOMPUTEBYLIST  pByList;  
    SQLSMALLINT     nBy;  
    SQLINTEGER      nColID;  
  
    printf_s("Computed result set number: %lu\n",  
        nSet);  
  
    SQLGetDescField(hIRD, 1, SQL_CA_SS_COMPUTE_BYLIST,  
        (SQLPOINTER) &pByList, SQL_IS_INTEGER,  
        &cbIRD);  
  
    if (pByList != NULL)  
        {  
        printf_s("Clause ordered by columns: ");  
        for (nBy = 0; nBy < pByList->nBys; )  
            {  
            printf_s("%u", pByList->aByList[nBy]);  
            nBy++;  
  
            if (nBy == pByList->nBys)  
                {  
                printf_s("\n");  
                }  
            else  
                {  
                printf_s(", ");  
                }  
            }  
        }  
    else  
        {  
        printf_s("Compute clause set not ordered.\n");  
        }  
  
    for (nCol = 0; nCol < nCols; nCol++)  
        {  
        SQLGetDescField(hIRD, nCol+1,  
            SQL_CA_SS_COLUMN_ID, (SQLPOINTER) &nColID,  
            SQL_IS_INTEGER, &cbIRD);  
        printf_s("ColumnID: %lu, nColID);  
        }  
    printf_s("\n");  
    }  
  
if (SQLMoreResults(g_hStmt) == SQL_SUCCESS)  
    {  
    // Determine the result set indicator.  
    SQLGetDescField(hIRD, 1, SQL_CA_SS_COMPUTE_ID,  
        (SQLPOINTER) &nSet, SQL_IS_INTEGER, &cbIRD);  
    }  
```  
  
## <a name="see-also"></a>参照  
 [SQLGetDescField 関数](../../odbc/reference/syntax/sqlgetdescfield-function.md)   
 [ODBC API 実装の詳細](../../relational-databases/native-client-odbc-api/odbc-api-implementation-details.md)  
  
