---
title: 大きな値型の使用
description: OLE DB Driver for SQL Serve で varchar(max)、varbinary(max)、nvarchar(max) 型を使用して大きな値を取るデータ型をサポートする方法について説明します。
ms.custom: ''
ms.date: 06/12/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: connectivity
ms.topic: reference
helpviewer_keywords:
- large value data types
- MSOLEDBSQL, large value types
- OLE DB Driver for SQL Server, large value types
- data access [OLE DB Driver for SQL Server], large value types
- OLE DB Driver for SQL Server, large value data types
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: cf390ad99ea840a2f464402e18a7660d6fab0e4d
ms.sourcegitcommit: bacd45c349d1b33abef66db47e5aa809218af4ea
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104793092"
---
# <a name="using-large-value-types"></a>大きな値型の使用

[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[Driver_OLEDB_Download](../../../includes/driver_oledb_download.md)]

[!INCLUDE[ssVersion2005](../../../includes/ssversion2005-md.md)] より前のバージョンでは、大きな値をとるデータ型を使用して作業する場合に特別な処理が必要でした。 大きな値をとるデータ型とは、最大行サイズが 8 KB を超えるデータ型のことです。 [!INCLUDE[ssVersion2005](../../../includes/ssversion2005-md.md)] では、2^31 -1 バイトまでの大きさの値を格納できる、**varchar** データ型、**nvarchar** データ型、**varbinary** データ型用の **max** 指定子が導入されました。 テーブル列や [!INCLUDE[tsql](../../../includes/tsql-md.md)] 変数に、**varchar(max)** データ型、**nvarchar(max)** データ型、または **varbinary(max)** データ型を指定できます。

> [!NOTE]
> 大きな値をとるデータ型は、最大サイズを 1 KB から 8 KB に制限できます。また、サイズを無制限にすることもできます。

以前は、**text**、**ntext**、**image** などの [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] データ型のみが非常に大きくなる可能性がありました。 **varchar**、**nvarchar**、**varbinary** に **max** 指定子が導入されたことで、これらのデータ型の存在は冗長になりました。 ただし、これらの大きなデータ型も引き続き使用できるので、OLE DB データ アクセス コンポーネントに対する大部分のインターフェイスは変更されません。 以前のリリースとの下位互換性を維持するために、OLE DB Driver for SQL Server では DBCOLUMNFLAGS_ISLONG フラグが引き続き使用されます。 [!INCLUDE[ssVersion2005](../../../includes/ssversion2005-md.md)] 以降用に作成されたプロバイダーやドライバーでは、最大長を無制限に設定する場合、新しいデータ型に対して、DBCOLUMNFLAGS_ISLONG や SQL_LONGVARCHAR といった表現を引き続き使用できます。

> [!NOTE]
> **varchar(max)** データ型、**nvarchar(max)** データ型、**varbinary(max)** データ型を、ストアド プロシージャの入力パラメーターの型や出力パラメーターの型、または関数の戻り値の型として指定したり、[CAST と CONVERT](../../../t-sql/functions/cast-and-convert-transact-sql.md) の各関数に指定することもできます。

> [!NOTE]
> データをレプリケートする場合は、[max text repl size サーバー構成オプション](../../../database-engine/configure-windows/configure-the-max-text-repl-size-server-configuration-option.md)を -1 に構成する必要があります。

## <a name="ole-db-driver-for-sql-server"></a>OLE DB Driver for SQL Server

OLE DB Driver for SQL Server では、**varchar(max)** 型、**varbinary(max)** 型、**nvarchar(max)** 型が、それぞれ DBTYPE_STR、DBTYPE_BYTES、DBTYPE_WSTR として公開されます。

**max** のサイズが無制限に設定されている列の **varchar(max)** データ型、**varbinary(max)** データ型、**nvarchar(max)** データ型は、列のデータ型を返す主要な OLE DB スキーマ行セットやインターフェイスでは ISLONG で表されます。

コマンド オブジェクトの **IAccessor** の実装は、DBTYPE_IUNKNOWN としてバインドできるように変更されています。 コンシューマーが DBTYPE_IUNKNOWN を指定し、*pObject* を NULL に設定すると、プロバイダーからコンシューマーに **ISequentialStream** インターフェイスが返されます。これにより、コンシューマーは **varchar(max)** データ、**nvarchar(max)** データ、または **varbinary(max)** データを出力変数からストリームできます。

ストリーミングされる出力パラメーターの値は、すべての結果行の後に返されます。 アプリケーションが、返される出力パラメーター値をすべて使用しないうちに **IMultipleResults::GetResult** を呼び出して次の結果セットに移動しようとすると、DB_E_OBJECTOPEN が返されます。

OLE DB Driver for SQL Server では、ストリーミングをサポートするために、可変長パラメーターに順番にアクセスする必要があります。 この要件は、**varchar(max)** 列、**nvarchchar(max)** 列、**varbinary(max)** 列や出力パラメーターを DBTYPE_IUNKNOWN. にバインドするときは必ず、DBPROP_ACCESSORDER を DBPROPVAL_AO_SEQUENTIALSTORAGEOBJECTS または DBPROPVAL_AO_SEQUENTIAL のいずれかに設定する必要があることを意味します。 このアクセス順序の制限に従わないと、**IRowset::GetData** への呼び出しは DBSTATUS_E_UNAVAILABLE で失敗します。 DBTYPE_IUNKNOWN を使用する出力バインドがない場合は、この制限は適用されません。

OLE DB Driver for SQL Server では、大きな値をとるデータ型の出力パラメーターを DBTYPE_IUNKNOWN としてバインドすることもサポートされます。これにより、ストアド プロシージャからクライアントに、DBTYPE_IUNKNOWN で公開される戻り値として大きな値をとるデータ型を返すようなシナリオを容易に実現できます。

このようなデータ型を使用して作業するために、アプリケーションでは次のような操作を行えます。

- 列の基本型にバインド可能な型としてバインドします (たとえば nvarchar(max) の場合は、nvarchar にバインド可能な型としてバインドします)。 以前よりも大きな値を格納できるようになりましたが、バッファーのサイズが十分でない場合は、基本型に応じて値が切り捨てられます。
- 列の基本型に変換可能な型としてバインドし、DBTYPE_BYREF も指定します。
- DBTYPE_IUNKNOWN としてバインドし、ストリーミングを使用します。

OLE DB Driver for SQL Server では、列の最大サイズを報告するときに次が報告されます。

- 定義された最大サイズ。たとえば、**varchar(** 2000 **)** 列の場合は 2000 になります。
- **varchar(max)** 列の値 "unlimited" は、~0 と同等です。 この値は、DBCOLUMN_COLUMNSIZE メタデータのプロパティに設定されます。

標準の変換規則が **varchar(max)** 列に適用されます。つまり、**varchar(** 2000 **)** 列で有効なすべての変換は、**varchar(max)** 列でも有効です。 **nvarchar(max)** 列と **varbinary(max)** 列についても同じことが当てはまります。

大きな値をとるデータ型を取得するとき最も効果的な方法は、DBTYPE_IUNKNOWN としてバインドし、行セット プロパティ DBPROP_ACCESSORDER を DBPROPVAL_AO_SEQUENTIALSTORAGEOBJECTS に設定することです。 この設定により、次の例で示すように、値が中間バッファーなしでネットワークから直接ストリーミングされます。

```cpp
#define UNICODE
#define _UNICODE
#define DBINITCONSTANTS
#define INITGUID
#define OLEDBVER 0x0250  // To include the correct interfaces.

#include <stdio.h>
#include <tchar.h>
#include <stddef.h>
#include <iostream>

using std::cout;
using std::endl;

#include <windows.h>

#include <oledb.h>
#include "msoledbsql.h"
#include <oledberr.h>

#define CHKHR_GOTO(hr, errMsg, Label) \
   if (FAILED(hr)) \
   { \
      cout << errMsg << endl; \
      goto Label; \
   }

#define MAX_COL_SIZE 8000

// ROUNDUP on all platforms pointers must be aligned properly.
#define ROUNDUP_AMOUNT 8
#define ROUNDUP_(size,amount) (((ULONG)(size)+((amount)-1))&~((amount)-1))
#define ROUNDUP(size) ROUNDUP_(size, ROUNDUP_AMOUNT)

HRESULT InitializeAndEstablishConnection(IDBInitialize** ppIDBInitialize);
void UnInitializeConnection(IDBInitialize* pIDBInitialize);
HRESULT CreateAndSetCommand(IDBInitialize* pIDBInitialize, ICommandText** ppICommandText);
HRESULT ProcessResultSet(IRowset* pIRowset);

void DisplayTime()
{
   SYSTEMTIME st;
   GetSystemTime(&st);
   cout<< st.wHour << ":" << st.wMinute << ":" << st.wSecond << "." << st.wMilliseconds << endl;
}

void main()
{
   HRESULT hr;
   IDBInitialize* pIDBInitialize = NULL;
   ICommandText* pICommandText = NULL;
   IMultipleResults* pIMultipleResults = NULL;
   IRowset* pIRowset = NULL;

   hr = InitializeAndEstablishConnection(&pIDBInitialize);
   CHKHR_GOTO(hr, L"Failed to establish connection.", _ExitMain);

   hr = CreateAndSetCommand(pIDBInitialize, &pICommandText);
   CHKHR_GOTO(hr, L"Failed to set up command object.", _ExitMain);

   DisplayTime();

   hr = pICommandText->Execute(NULL,
      IID_IMultipleResults,
      NULL,
      NULL,
     (IUnknown **) &pIMultipleResults);

   CHKHR_GOTO(hr, L"Failed to execute command.", _ExitMain);

   while (1)
   {
      hr = pIMultipleResults->GetResult(
         NULL,
         DBRESULTFLAG_DEFAULT,
         IID_IRowset,
         NULL,
         (IUnknown**)&pIRowset);

   CHKHR_GOTO(hr, L"Failed to obtain a results from MR object.", _ExitMain);

   if (hr == DB_S_NORESULT)
      break;

      if (pIRowset)
      {
         hr = ProcessResultSet(pIRowset);
         CHKHR_GOTO(hr, L"Failed to process the current Rowset.", _ExitMain);

         pIRowset->Release();
         pIRowset = NULL;
      }
   }

   DisplayTime();

_ExitMain:

   if (pIRowset)
   {
      pIRowset->Release();
      pIRowset = NULL;
   }

   if (pIMultipleResults)
   {
      pIMultipleResults->Release();
      pIMultipleResults = NULL;
   }

   if (pICommandText)
   {
      pICommandText->Release();
      pICommandText = NULL;
   }

   UnInitializeConnection(pIDBInitialize);
   return;
};

HRESULT InitializeAndEstablishConnection(IDBInitialize** ppIDBInitialize)
{
   HRESULT hr;
   IDBInitialize* pIDBInitialize = NULL;
   IDBProperties* pIDBProperties = NULL;

   const int NUM_DBINIT_PROPS = 3;
   const wchar_t* const g_wszServer = L".";
   const wchar_t* const g_wszCatalog = L"AdventureWorks";
   const wchar_t* const g_wszSecurity = L"SSPI";

   DBPROPSET rgdbPropSetInit[1];
   DBPROP rgdbPropInit [NUM_DBINIT_PROPS];

   *ppIDBInitialize = NULL;
   hr = CoInitialize(NULL);
   CHKHR_GOTO(hr, L"Failed to initialize COM.", _ExitInitialize);

   hr = CoCreateInstance(CLSID_MSOLEDBSQL,
      NULL,
      CLSCTX_INPROC_SERVER,
      IID_IDBInitialize,
      (void**)&pIDBInitialize);

   CHKHR_GOTO(hr, L"Failed to create MSOLEDBSQL DataSource object.", _ExitInitialize);

   for(int idxProp = 0; idxProp < NUM_DBINIT_PROPS; idxProp++)
   {
      VariantInit(&rgdbPropInit[idxProp].vValue);
   }

   rgdbPropInit[0].dwPropertyID = DBPROP_INIT_DATASOURCE;
   rgdbPropInit[0].vValue.vt = VT_BSTR;
   rgdbPropInit[0].vValue.bstrVal= SysAllocString(g_wszServer);
   rgdbPropInit[0].dwOptions = DBPROPOPTIONS_REQUIRED;
   rgdbPropInit[0].colid = DB_NULLID;

   if (rgdbPropInit[0].vValue.bstrVal == NULL)
   {
      hr = E_OUTOFMEMORY;
      goto _ExitInitialize;
   }

   rgdbPropInit[1].dwPropertyID = DBPROP_INIT_CATALOG;
   rgdbPropInit[1].vValue.vt = VT_BSTR;
   rgdbPropInit[1].vValue.bstrVal= SysAllocString(g_wszCatalog);
   rgdbPropInit[1].dwOptions = DBPROPOPTIONS_REQUIRED;
   rgdbPropInit[1].colid = DB_NULLID;

   if (rgdbPropInit[1].vValue.bstrVal == NULL)
   {
      hr = E_OUTOFMEMORY;
      goto _ExitInitialize;
   }

   rgdbPropInit[2].dwPropertyID = DBPROP_AUTH_INTEGRATED;
   rgdbPropInit[2].vValue.vt = VT_BSTR;
   rgdbPropInit[2].vValue.bstrVal= SysAllocString(g_wszSecurity);
   rgdbPropInit[2].dwOptions = DBPROPOPTIONS_REQUIRED;
   rgdbPropInit[2].colid = DB_NULLID;

   if (rgdbPropInit[2].vValue.bstrVal == NULL)
   {
      hr = E_OUTOFMEMORY;
      goto _ExitInitialize;
   }

   rgdbPropSetInit[0].guidPropertySet = DBPROPSET_DBINIT;
   rgdbPropSetInit[0].cProperties = NUM_DBINIT_PROPS;
   rgdbPropSetInit[0].rgProperties = rgdbPropInit;

   hr = pIDBInitialize->QueryInterface(IID_IDBProperties, (void **)&pIDBProperties);
   CHKHR_GOTO(hr, L"Failed to QI DataSource object for IDBProperties.", _ExitInitialize);

   hr = pIDBProperties->SetProperties(1, rgdbPropSetInit);
   CHKHR_GOTO(hr, L"Failed to set DataSource object Properties.", _ExitInitialize);

   pIDBProperties->Release();
   pIDBProperties = NULL;

   hr = pIDBInitialize->Initialize();
   CHKHR_GOTO(hr, L"Failed to establish connection with the server.", _ExitInitialize);

_ExitInitialize:

   if (pIDBProperties)
   {
      pIDBProperties->Release();
      pIDBProperties = NULL;
   }

   if (FAILED(hr))
   {
      if (pIDBInitialize)
      {
         pIDBInitialize->Release();
         pIDBInitialize = NULL;
      }
   }

   *ppIDBInitialize = pIDBInitialize;
   return hr;
}

void UnInitializeConnection(IDBInitialize* pIDBInitialize)
{
   if (pIDBInitialize)
   {
      pIDBInitialize->Uninitialize();
      pIDBInitialize->Release();
      pIDBInitialize = NULL;
   }
   CoUninitialize();
}

HRESULT CreateAndSetCommand(IDBInitialize* pIDBInitialize, ICommandText** ppICommandText)
{
   HRESULT hr;
   IDBCreateSession* pIDBCreateSession = NULL;
   IDBCreateCommand* pIDBCreateCommand = NULL;
   ICommandText* pICommandText = NULL;
   ICommandProperties* pICommandProperties = NULL;
   DBPROPSET rgCmdPropSet[1];
   DBPROP rgCmdProperties[1];

const wchar_t* const g_wCmdString = L"declare @x xml, @y nvarchar(max); select @x = (SELECT * FROM Sales.SalesOrderHeader FOR XML AUTO); select @x;";

   *ppICommandText = NULL;

   if (!pIDBInitialize)
   {
      hr = E_FAIL;
      goto _ExitCreateAndSetCommand;
   }

   hr = pIDBInitialize->QueryInterface(IID_IDBCreateSession, (void**) &pIDBCreateSession);
   CHKHR_GOTO(hr, L"Failed to obtain IDBCreateSession interface from DSO.", _ExitCreateAndSetCommand);

   hr = pIDBCreateSession->CreateSession(
      NULL,
      IID_IDBCreateCommand,
      (IUnknown**) &pIDBCreateCommand);

   CHKHR_GOTO(hr, L"Failed to Create a Session for command execution.", _ExitCreateAndSetCommand);

   hr = pIDBCreateCommand->CreateCommand(
      NULL,
      IID_ICommandText,
      (IUnknown**)&pICommandText);

   CHKHR_GOTO(hr, L"Failed to Create a Command object.", _ExitCreateAndSetCommand);

   hr = pICommandText->SetCommandText(DBGUID_DBSQL, g_wCmdString);
   CHKHR_GOTO(hr, L"Failed to Set Command Text.", _ExitCreateAndSetCommand);

   hr = pICommandText->QueryInterface(IID_ICommandProperties, (void**) &pICommandProperties);
   CHKHR_GOTO(hr, L"Failed to obtain ICommandProperties interface from the command object.", _ExitCreateAndSetCommand);

   rgCmdProperties[0].dwPropertyID = DBPROP_ACCESSORDER;
   rgCmdProperties[0].vValue.vt = VT_I4;
   rgCmdProperties[0].vValue.lVal = DBPROPVAL_AO_SEQUENTIAL;
   rgCmdProperties[0].dwOptions = DBPROPOPTIONS_REQUIRED;
   rgCmdProperties[0].colid = DB_NULLID;

   rgCmdPropSet[0].guidPropertySet = DBPROPSET_ROWSET;
   rgCmdPropSet[0].cProperties = 1;
   rgCmdPropSet[0].rgProperties = rgCmdProperties;

   hr = pICommandProperties->SetProperties(1, rgCmdPropSet);
   CHKHR_GOTO(hr, L"Failed to Set Command object Properties.", _ExitCreateAndSetCommand);

_ExitCreateAndSetCommand:

   if (pICommandProperties)
   {
      pICommandProperties->Release();
      pICommandProperties = NULL;
   }

   if (pIDBCreateCommand)
   {
      pIDBCreateCommand->Release();
      pIDBCreateCommand = NULL;
   }

   if (pIDBCreateSession)
   {
      pIDBCreateSession->Release();
      pIDBCreateSession = NULL;
   }

   if (FAILED(hr))
   {
      if (pICommandText)
      {
         pICommandText->Release();
         pICommandText = NULL;
      }
   }

   *ppICommandText = pICommandText;
   return hr;
}

HRESULT ProcessResultSet(IRowset* pIRowset)
{
   HRESULT hr;

   IColumnsInfo* pIColumnsInfo = NULL;
   DBCOLUMNINFO* pDBColumnInfo = NULL;
   ULONG lNumCols = 0;
   wchar_t* pStringsBuffer = NULL;

   DBBINDING* pBindings = NULL;
   DBOBJECT dbobj;
   ULONG idxBinding;
   IAccessor* pIAccessor = NULL;
   HACCESSOR hAccessor = DB_NULL_HACCESSOR;
   HROW hRows[1] = {DB_NULL_HROW};
   HROW* pRow = &hRows[0];
   BYTE* pBuffer = NULL;

   ULONG lNumRowsRetrieved;
   DBLENGTH dwOffset = 0;

   hr = pIRowset->QueryInterface(IID_IColumnsInfo, (void **)&pIColumnsInfo);
   CHKHR_GOTO(hr, L"Failed to QI Rowset for IColumnsInfo.", _ExitProcessResultSet);

   hr = pIColumnsInfo->GetColumnInfo(&lNumCols, &pDBColumnInfo, &pStringsBuffer);
   CHKHR_GOTO(hr, L"Failed to obtain Column Information.", _ExitProcessResultSet);

   pBindings = new DBBINDING[lNumCols];

   if (!pBindings)
   {
      hr = E_OUTOFMEMORY;
      goto _ExitProcessResultSet;
   }

   memset(pBindings, 0, sizeof(DBBINDING) * lNumCols);

   dbobj.dwFlags = STGM_READ;
   dbobj.iid = IID_ISequentialStream;

   for (idxBinding = 0; idxBinding < lNumCols; idxBinding++)
   {
      pBindings[idxBinding].iOrdinal = idxBinding + 1;
      pBindings[idxBinding].obStatus = dwOffset;
      pBindings[idxBinding].obLength = dwOffset + sizeof(DBSTATUS);
      pBindings[idxBinding].obValue = dwOffset + sizeof(DBSTATUS) + sizeof(DBLENGTH);

      pBindings[idxBinding].pTypeInfo = NULL;
      pBindings[idxBinding].pBindExt = NULL;
      pBindings[idxBinding].dwPart = DBPART_VALUE | DBPART_LENGTH | DBPART_STATUS;
      pBindings[idxBinding].dwMemOwner = DBMEMOWNER_CLIENTOWNED;
      pBindings[idxBinding].eParamIO = DBPARAMIO_NOTPARAM;
      pBindings[idxBinding].bPrecision = pDBColumnInfo[idxBinding].bPrecision;
      pBindings[idxBinding].bScale = pDBColumnInfo[idxBinding].bScale;

      pBindings[idxBinding].cbMaxLen = 0;
      pBindings[idxBinding].wType = DBTYPE_WSTR;

   // Determine the maximum number of bytes required in our buffer to
   // contain the Unicode string representation of the provider's native
   // data type, including room for the NULL-termination character
   switch( pDBColumnInfo[idxBinding].wType )
   {
      case DBTYPE_NULL:
      case DBTYPE_EMPTY:
      case DBTYPE_I1:
      case DBTYPE_I2:
      case DBTYPE_I4:
      case DBTYPE_UI1:
      case DBTYPE_UI2:
      case DBTYPE_UI4:
      case DBTYPE_R4:
      case DBTYPE_BOOL:
      case DBTYPE_I8:
      case DBTYPE_UI8:
      case DBTYPE_R8:
      case DBTYPE_CY:
      case DBTYPE_ERROR:
      // When the above types are converted to a string, they
      // will all fit into 25 characters, so use that plus space
      // for the NULL-terminator.

      pBindings[idxBinding].cbMaxLen = (25 + 1) * sizeof(WCHAR);
      break;

      case DBTYPE_DECIMAL:
      case DBTYPE_NUMERIC:
      case DBTYPE_DATE:
      case DBTYPE_DBDATE:
      case DBTYPE_DBTIMESTAMP:
      case DBTYPE_GUID:
      // Converted to a string, the above types will all fit into
      // 50 characters, so use that plus space for the terminator.

      pBindings[idxBinding].cbMaxLen = (50 + 1) * sizeof(WCHAR);
      break;

      case DBTYPE_BYTES:
      // In converting DBTYPE_BYTES to a string, each byte
      // becomes two characters (e.g. 0xFF -> "FF"), so we
      // will use double the maximum size of the column plus
      // include space for the NULL-terminator.

      pBindings[idxBinding].cbMaxLen = (pDBColumnInfo[idxBinding].ulColumnSize * 2 + 1) * sizeof(WCHAR);
      break;

      case DBTYPE_STR:
      case DBTYPE_WSTR:
      case DBTYPE_BSTR:
      // Going from a string to our string representation,
      // we can just take the maximum size of the column,
      // a count of characters, and include space for the
      // terminator, which is not included in the column size.

      pBindings[idxBinding].cbMaxLen = (pDBColumnInfo[idxBinding].ulColumnSize + 1) * sizeof(WCHAR);
      break;

      default:
      // For any other type, we will simply use our maximum
      // column buffer size, since the display size of these
      // columns may be variable (e.g. DBTYPE_VARIANT) or
      // unknown (e.g. provider-specific types).
      pBindings[idxBinding].cbMaxLen = MAX_COL_SIZE;
      break;
   }

   // If the provider's native data type for this column is
   // DBTYPE_IUNKNOWN or this is a BLOB column and the user
   // has requested that we bind BLOB columns as ISequentialStream
   // objects, bind this column as an ISequentialStream object if
   // the provider supports our creating another ISequentialStream
   // binding.
   if(pDBColumnInfo[idxBinding].dwFlags & DBCOLUMNFLAGS_ISLONG)
   {
      pBindings[idxBinding].wType = DBTYPE_IUNKNOWN;

      pBindings[idxBinding].cbMaxLen = sizeof(ISequentialStream*);

      pBindings[idxBinding].pObject = (DBOBJECT *)CoTaskMemAlloc(sizeof(DBOBJECT));

      if (!pBindings[idxBinding].pObject)
      {
         hr = E_OUTOFMEMORY;
         goto _ExitProcessResultSet;
      }

      // Direct the provider to create an ISequentialStream
      // object over the data for this column.
      pBindings[idxBinding].pObject->iid = IID_ISequentialStream;

      // We want read access on the ISequentialStream
      // object that the provider will create for us
      pBindings[idxBinding].pObject->dwFlags = STGM_READ;
      }

      // Ensure that the bound maximum length is no more than the
      // maximum column size in bytes that we've defined.
      pBindings[idxBinding].cbMaxLen = min(pBindings[idxBinding].cbMaxLen, MAX_COL_SIZE);

      // Update the offset past the end of this column's data, so
      // that the next column will begin in the correct place in
      // the buffer.
      dwOffset = pBindings[idxBinding].cbMaxLen + pBindings[idxBinding].obValue;

      // Ensure that the data for the next column will be correctly
      // aligned for all platforms, or, if we're done with columns,
      // that if we allocate space for multiple rows that the data
      // for every row is correctly aligned.
      dwOffset = ROUNDUP(dwOffset);
   }

   hr = pIRowset->QueryInterface(IID_IAccessor, (void **) &pIAccessor);
   CHKHR_GOTO(hr, L"Failed to obtain Accessor interface", _ExitProcessResultSet);

   hr = pIAccessor->CreateAccessor(DBACCESSOR_ROWDATA,
      lNumCols,
      pBindings,
      0,
      &hAccessor,
      NULL);

   CHKHR_GOTO(hr, L"Failed to create Accessor", _ExitProcessResultSet);
   for (idxBinding = 0; idxBinding < lNumCols; idxBinding++)
   {
      cout << pDBColumnInfo[idxBinding].pwszName << endl;
   }

   lNumRowsRetrieved = 0;

   hr = pIRowset->GetNextRows(
      NULL,
      0,
      1,
      &lNumRowsRetrieved,
      &pRow);

   CHKHR_GOTO(hr, L"Failed to fetch a row from the rowset", _ExitProcessResultSet);

   pBuffer = new BYTE[sizeof(DBSTATUS) + sizeof(DBLENGTH) + sizeof(IUnknown*)];

   if (!pBuffer)
   {
      hr = E_OUTOFMEMORY;
      goto _ExitProcessResultSet;
   }

   while(lNumRowsRetrieved && hr != DB_S_ENDOFROWSET)
   {
      memset(pBuffer, 0, sizeof(DBSTATUS) + sizeof(DBLENGTH) + sizeof(IUnknown*));

      hr = pIRowset->GetData(hRows[0], hAccessor, pBuffer);
      CHKHR_GOTO(hr, L"Failed to obtain row data", _ExitProcessResultSet);

      for (idxBinding = 0; idxBinding < lNumCols; idxBinding++)
      {
         if (pBindings[idxBinding].wType == DBTYPE_IUNKNOWN)
         {
            BYTE pbBuff[3000];
            ULONG cbNeeded = sizeof(pbBuff)/sizeof(BYTE);
            ULONG cbRead;
            ULONG cbReadTotal = 0;
            ISequentialStream* pISequentialStream = NULL;

            IUnknown* pIUnknown = *((IUnknown**)(pBuffer + pBindings[idxBinding].obValue));
            pIUnknown->QueryInterface(IID_ISequentialStream, (void**)&pISequentialStream);

            do
            {
               hr = pISequentialStream->Read(pbBuff, cbNeeded, &cbRead);
               cbReadTotal += cbRead;
            }
            while (SUCCEEDED(hr) && hr != S_FALSE && cbRead == cbNeeded);

               cout << "Total Bytes Read: " << cbReadTotal << endl;

               pISequentialStream->Release();
               pISequentialStream = NULL;
               pIUnknown->Release();
               pIUnknown = NULL;
            }
         }

         pIRowset->ReleaseRows(1, pRow, NULL, NULL, NULL);

         hr = pIRowset->GetNextRows(NULL,
            0,
            1,
            &lNumRowsRetrieved,
            &pRow);

         CHKHR_GOTO(hr, L"Failed to fetch a row from the rowset.", _ExitProcessResultSet);
   }

_ExitProcessResultSet:

   pIRowset->ReleaseRows(1, pRow, NULL, NULL, NULL);
   delete [] pBuffer;

   if (pIAccessor)
   {
      if (hAccessor != DB_NULL_HACCESSOR)
      {
         pIAccessor->ReleaseAccessor(hAccessor, NULL);
      }

      pIAccessor->Release();
      pIAccessor = NULL;
   }

   if (pBindings)
   {
      for (idxBinding = 0; idxBinding < lNumCols; idxBinding++)
      {
         if (pBindings[idxBinding].pObject)
         CoTaskMemFree(pBindings[idxBinding].pObject);
      }
   }

   delete [] pBindings;

   CoTaskMemFree(pDBColumnInfo);
   CoTaskMemFree(pStringsBuffer);

   if (pIColumnsInfo)
   {
      pIColumnsInfo->Release();
      pIColumnsInfo = NULL;
   }

   return hr;
}
```

OLE DB Driver for SQL Server が大きな値のデータ型を公開する方法の詳細については、「[BLOB と OLE オブジェクト](../../oledb/ole-db-blobs/blobs-and-ole-objects.md)」を参照してください。

## <a name="see-also"></a>参照

[OLE DB Driver for SQL Server の機能](../../oledb/features/oledb-driver-for-sql-server-features.md)
