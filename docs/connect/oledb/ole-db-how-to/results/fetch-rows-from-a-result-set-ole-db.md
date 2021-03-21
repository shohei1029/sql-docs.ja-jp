---
title: 結果セットからの行のフェッチ (OLE DB ドライバー) | Microsoft Docs
description: OLE DB Driver for SQL Server を使用して結果セットから行をフェッチする方法について説明します。 この例では、AdventureWorks サンプル データベースを使用します。
ms.custom: ''
ms.date: 06/14/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: connectivity
ms.topic: reference
helpviewer_keywords:
- rows [OLE DB]
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: 21588901b2a2cf0da6bf60e244b6de6a981502ff
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104741212"
---
# <a name="fetch-rows-from-a-result-set-ole-db"></a>結果セットからの行のフェッチ (OLE DB)
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[Driver_OLEDB_Download](../../../../includes/driver_oledb_download.md)]

  このサンプルでは、結果セットから行をフェッチする方法を示します。 このサンプルは IA64 ではサポートされていません。  
  
 このサンプルには AdventureWorks サンプル データベースが必要です。このサンプル データベースは、[Microsoft SQL Server サンプルとコミュニティのプロジェクト](https://go.microsoft.com/fwlink/?LinkID=85384)のホーム ページからダウンロードできます。  
  
> [!IMPORTANT]  
>  可能な場合は、Windows 認証を使用します。 Windows 認証が使用できない場合は、実行時に資格情報を入力するようユーザーに求めます。 資格情報をファイルに保存するのは避けてください。 資格情報を保持する必要がある場合は、[Win32 Crypto API](/windows/win32/seccrypto/cryptography-reference) を使用して暗号化してください。  
  
## <a name="example"></a>例  
  
### <a name="description"></a>説明  
 ole32.lib と oleaut32.lib を使用して次の C++ コード リストをコンパイルし、実行します。 このアプリケーションは、コンピューターの既定の [!INCLUDE[ssNoVersion](../../../../includes/ssnoversion-md.md)] インスタンスに接続します。 一部の Windows オペレーティング システムでは、(localhost) または (local) を実際の [!INCLUDE[ssNoVersion](../../../../includes/ssnoversion-md.md)] インスタンスの名前に変更する必要があります。 名前付きインスタンスに接続するには、接続文字列を L"(local)" から L"(local)\\\name" に変更します。ここで、name は名前付きインスタンスです。 [!INCLUDE[ssNoVersion](../../../../includes/ssnoversion-md.md)] Express は、既定で名前付きインスタンスとしてインストールされます。 INCLUDE 環境変数に、msoledbsql.h が保存されているディレクトリが含まれていることを確認します。  
  
### <a name="code"></a>コード  
  
```  
// compile with: ole32.lib oleaut32.lib  
int InitializeAndEstablishConnection();  
int ProcessResultSet();  
  
#define UNICODE  
#define _UNICODE  
#define DBINITCONSTANTS  
#define INITGUID  
#define OLEDBVER 0x0250   // to include correct interfaces  
  
#include <stdio.h>  
#include <tchar.h>  
#include <stddef.h>  
#include <windows.h>  
#include <iostream>  
#include <oledb.h>  
#include <msoledbsql.h>  
#include <msdadc.h>   // for IDataConvert  
#include <msdaguid.h>   // for IDataConvert  
  
using namespace std;  
  
IDBInitialize* pIDBInitialize = NULL;  
IDBProperties* pIDBProperties = NULL;  
IDBCreateSession* pIDBCreateSession = NULL;  
IDBCreateCommand* pIDBCreateCommand = NULL;  
ICommandText* pICommandText = NULL;  
IRowset* pIRowset = NULL;  
IColumnsInfo* pIColumnsInfo = NULL;  
  
DBCOLUMNINFO* pDBColumnInfo = NULL;  
IAccessor* pIAccessor =  NULL;  
DBPROP InitProperties[4];  
DBPROPSET rgInitPropSet[1];  
  
ULONG i, j;  
HRESULT hr;  
DBROWCOUNT cNumRows = 0;  
DBORDINAL lNumCols;  
WCHAR* pStringsBuffer;  
DBBINDING* pBindings;  
DBLENGTH ConsumerBufColOffset = 0;  
HACCESSOR hAccessor;  
DBCOUNTITEM lNumRowsRetrieved;  
HROW hRows[10];  
HROW* pRows = &hRows[0];  
  
int main() {  
   // The command to execute.  
   WCHAR* wCmdString = OLESTR("SELECT StandardCost, ListPrice FROM Production.Product WHERE ListPrice > 14.00");  
  
   // Call a function to initialize and establish connection.   
   if (InitializeAndEstablishConnection() == -1) {  
      // Handle error.  
      cout << "Failed to initialize and connect to the server.\n";  
      return -1;  
   }  
  
   // Create a session object.  
   if (FAILED(pIDBInitialize->QueryInterface( IID_IDBCreateSession,  
      (void**) &pIDBCreateSession))) {  
         cout << "Failed to obtain IDBCreateSession interface.\n";  
         // Handle error.  
         return -1;  
   }  
  
   if (FAILED(pIDBCreateSession->CreateSession( NULL,   
      IID_IDBCreateCommand,   
      (IUnknown**) &pIDBCreateCommand))) {  
         cout << "pIDBCreateSession->CreateSession failed.\n";  
         // Handle error.  
         return -1;  
   }  
  
   // Access the ICommandText interface.  
   if (FAILED(pIDBCreateCommand->CreateCommand( NULL,   
      IID_ICommandText,   
      (IUnknown**) &pICommandText))) {  
         cout << "Failed to access ICommand interface.\n";  
         // Handle error.  
         return -1;  
   }  
  
   // Use SetCommandText() to specify the command text.  
   if (FAILED(pICommandText->SetCommandText(DBGUID_DBSQL, wCmdString))) {  
      cout << "Failed to set command text.\n";  
      // Handle error.  
      return -1;  
   }  
  
   // Execute the command.  
   if (FAILED(hr = pICommandText->Execute( NULL,   
      IID_IRowset,   
      NULL,   
      &cNumRows,   
      (IUnknown **) &pIRowset))) {  
         cout << "Failed to execute command.\n";  
         // Handle error.  
         return -1;  
   }  
  
   // Process the result set.  
   ProcessResultSet();   
  
   pIRowset->Release();  
  
   // Free up memory.  
   pICommandText->Release();  
   pIDBCreateCommand->Release();  
   pIDBCreateSession->Release();  
  
   if (FAILED(pIDBInitialize->Uninitialize())) {  
      // Uninitialize is not required, but it fails if an interface  
      // has not been released.  This can be used for debugging.  
      cout << "Problem uninitializing.\n";  
   }  
  
   pIDBInitialize->Release();  
   CoUninitialize();  
};  
  
int InitializeAndEstablishConnection() {      
   CoInitialize(NULL);  
  
   // Obtain access to the MSOLEDBSQL provider.  
   hr = CoCreateInstance( CLSID_MSOLEDBSQL,  
      NULL,  
      CLSCTX_INPROC_SERVER,  
      IID_IDBInitialize,  
      (void **) &pIDBInitialize);  
  
   if (FAILED(hr)) {  
      printf("Failed to get IDBInitialize interface.\n");  
      // Handle errors here.  
      return -1;  
   }  
  
   // Initialize the property values needed to establish the connection.  
   for ( i = 0 ; i < 4 ; i++ )  
      VariantInit(&InitProperties[i].vValue);  
  
   // Server name.  
   InitProperties[0].dwPropertyID = DBPROP_INIT_DATASOURCE;  
   InitProperties[0].vValue.vt = VT_BSTR;  
  
   InitProperties[0].vValue.bstrVal= SysAllocString(L"(local)");  
   InitProperties[0].dwOptions = DBPROPOPTIONS_REQUIRED;  
   InitProperties[0].colid = DB_NULLID;  
  
   // Database.  
   InitProperties[1].dwPropertyID = DBPROP_INIT_CATALOG;  
   InitProperties[1].vValue.vt = VT_BSTR;  
   InitProperties[1].vValue.bstrVal = SysAllocString(L"AdventureWorks");  
   InitProperties[1].dwOptions = DBPROPOPTIONS_REQUIRED;  
   InitProperties[1].colid = DB_NULLID;  
  
   InitProperties[2].dwPropertyID = DBPROP_AUTH_INTEGRATED;  
   InitProperties[2].vValue.vt = VT_BSTR;  
   InitProperties[2].vValue.bstrVal = SysAllocString(L"SSPI");  
   InitProperties[2].dwOptions = DBPROPOPTIONS_REQUIRED;  
   InitProperties[2].colid = DB_NULLID;  
  
   // Properties are set, now construct the DBPROPSET structure (rgInitPropSet) used to pass   
   // an array of DBPROP structures (InitProperties) to the SetProperties method.  
   rgInitPropSet[0].guidPropertySet = DBPROPSET_DBINIT;  
   rgInitPropSet[0].cProperties = 4;  
   rgInitPropSet[0].rgProperties = InitProperties;  
  
   // Set initialization properties.  
   hr = pIDBInitialize->QueryInterface(IID_IDBProperties, (void **)&pIDBProperties);  
   if (FAILED(hr)) {  
      cout << "Failed to get IDBProperties interface.\n";  
      // Handle errors here.  
      return -1;  
   }  
  
   hr = pIDBProperties->SetProperties(1, rgInitPropSet);   
   if (FAILED(hr)) {  
      cout << "Failed to set initialization properties.\n";  
      // Handle errors here.  
      return -1;  
   }  
  
   pIDBProperties->Release();  
  
   // Now establish the connection to the data source.  
   if (FAILED(pIDBInitialize->Initialize())) {  
      cout << "Problem in establishing connection to the data"  
         "source.\n";  
      // Handle errors here.  
      return -1;  
   }  
   return 0;  
}  
  
// Retrieve and display data resulting from a query.  
int ProcessResultSet() {  
   // Obtain access to the IColumnInfo interface, from the Rowset object.  
   hr = pIRowset->QueryInterface(IID_IColumnsInfo, (void **)&pIColumnsInfo);  
   if (FAILED(hr)) {  
      cout << "Failed to get IColumnsInfo interface.\n";  
      // Handle errors here.  
      return -1;  
   }   
  
   // Retrieve the column information.  
   pIColumnsInfo->GetColumnInfo(&lNumCols, &pDBColumnInfo, &pStringsBuffer);  
  
   // Free the column information interface.  
   pIColumnsInfo->Release();  
  
   // Create a DBBINDING array.  
   DBBINDING * p = (pBindings = new DBBINDING[lNumCols]);  
   if (!(p /* pBindings = new DBBINDING[lNumCols] */ ))  
      return -1;  
  
   // Using the ColumnInfo structure, fill out the pBindings array.  
   for ( j = 0 ; j < lNumCols ; j++ ) {  
      pBindings[j].iOrdinal = j+1;  
      pBindings[j].obValue = ConsumerBufColOffset;  
      pBindings[j].pTypeInfo = NULL;  
      pBindings[j].pObject = NULL;  
      pBindings[j].pBindExt = NULL;  
      pBindings[j].dwPart = DBPART_VALUE;  
      pBindings[j].dwMemOwner = DBMEMOWNER_CLIENTOWNED;  
      pBindings[j].eParamIO = DBPARAMIO_NOTPARAM;  
      pBindings[j].cbMaxLen = (pDBColumnInfo[j].wType == DBTYPE_WSTR) ? pDBColumnInfo[j].ulColumnSize * 2 : pDBColumnInfo[j].ulColumnSize;  
      pBindings[j].dwFlags = 0;  
      pBindings[j].wType = pDBColumnInfo[j].wType;  
      pBindings[j].bPrecision = pDBColumnInfo[j].bPrecision;  
      pBindings[j].bScale = pDBColumnInfo[j].bScale;  
  
      // Compute the next buffer offset.  
      ConsumerBufColOffset =   
         ConsumerBufColOffset + pBindings[j].cbMaxLen;  
   };  
  
   // Get the IAccessor interface.  
   hr = pIRowset->QueryInterface(IID_IAccessor, (void **) &pIAccessor);  
   if (FAILED(hr)) {  
      cout << "Failed to obtain IAccessor interface.\n";  
      // Handle errors here.  
      return -1;  
   }  
  
   // Create an accessor from the set of bindings (pBindings).  
   pIAccessor->CreateAccessor(DBACCESSOR_ROWDATA, lNumCols, pBindings, 0, &hAccessor, NULL);  
  
   // Print column names.  
   for ( j = 0 ; j < lNumCols ; j++ )  
      printf("%-40S", pDBColumnInfo[j].pwszName);  
  
   printf("\n");   // new line after the column names  
  
   // Get a set of 10 rows.  
   pIRowset->GetNextRows( NULL, 0, 10, &lNumRowsRetrieved, &pRows);  
  
   // Allocate space for the row buffer.  
   BYTE * pBuffer = new BYTE[ConsumerBufColOffset];  
   if (!(pBuffer /* = new BYTE[ConsumerBufColOffset] */ )) {  
      // Free up all allocated memory.  
      pIAccessor->ReleaseAccessor(hAccessor, NULL);  
      pIAccessor->Release();  
      delete [] pBindings;  
  
      return 0;  
   }  
  
   // Create an instance of the data conversion library to convert DBTYPE_CY to string for display  
   IDataConvert* pIDataConvert;  
   CoCreateInstance(CLSID_OLEDB_CONVERSIONLIBRARY,  
      NULL,  
      CLSCTX_INPROC_SERVER,  
      IID_IDataConvert,  
      (void**)&pIDataConvert);  
  
   // variables used in DataConvert  
   DBLENGTH cbDstLength;  
   DBSTATUS dbsStatus;  
   char strCurrency0[25];  
   char strCurrency1[25];  
  
   // Display the rows.  
   while ( lNumRowsRetrieved > 0 ) {  
      // For each row, print the column data.  
      for ( j = 0 ; j < lNumRowsRetrieved ; j++ ) {  
         // Clear the buffer.  
         memset(pBuffer, 0, ConsumerBufColOffset);  
  
         // Get the row data values.  
         pIRowset->GetData(hRows[j], hAccessor, pBuffer);  
  
         // Convert DBTYPE_CY values to string   
         pIDataConvert->DataConvert(DBTYPE_CY,   // wSrcType  
            DBTYPE_STR,   // wDstType  
            sizeof(LARGE_INTEGER),   // cbSrcLength   
            &cbDstLength,   // pcbDstLength  
            &pBuffer[pBindings[0].obValue],   // pSrc  
            strCurrency0,   // pDst  
            sizeof(strCurrency0),   //cbDstMaxLength  
            DBSTATUS_S_OK,   // dbsSrcStatus  
            &dbsStatus,   // pdbsStatus  
            0,   // bPrecision (used for DBTYPE_NUMERIC only)  
            0,   // bScale (used for DBTYPE_NUMERIC only)  
            DBDATACONVERT_DEFAULT);   // dwFlags  
  
         pIDataConvert->DataConvert(DBTYPE_CY,   // wSrcType  
            DBTYPE_STR,   // wDstType  
            sizeof(LARGE_INTEGER),   // cbSrcLength   
            &cbDstLength,   // pcbDstLength  
            &pBuffer[pBindings[1].obValue],   // pSrc  
            strCurrency1,   // pDst  
            sizeof(strCurrency1),   // cbDstMaxLength  
            DBSTATUS_S_OK,   // dbsSrcStatus  
            &dbsStatus,   // pdbsStatus  
            0,   // bPrecision (used for DBTYPE_NUMERIC only)  
            0,   // bScale (used for DBTYPE_NUMERIC only)  
            DBDATACONVERT_DEFAULT); // dwFlags  
  
         // Print cost and price values.  
         printf("%-40s%s\n", strCurrency0, strCurrency1); //sparra  
      };  
  
      // Release the rows retrieved.  
      pIRowset->ReleaseRows(lNumRowsRetrieved, hRows, NULL, NULL, NULL);  
  
      // Get the next set of 10 rows.  
      pIRowset->GetNextRows(NULL, 0, 10, &lNumRowsRetrieved, &pRows);  
   }  
  
   // Free up all allocated memory.  
   delete [] pBuffer;  
   pIAccessor->ReleaseAccessor(hAccessor, NULL);  
   pIAccessor->Release();  
   delete [] pBindings;  
  
   return 0;  
}  
```  
  
## <a name="see-also"></a>参照  
 [結果を処理する方法に関するトピック &#40;OLE DB&#41;](../../../oledb/ole-db-how-to/results/processing-results-how-to-topics-ole-db.md)  
  
