---
title: IBCPSession2::BCPSetBulkMode (OLE DB ドライバー) | Microsoft Docs
description: OLE DB Driver for SQL Server で IBCPSession2::BCPSetBulkMode メソッドを使用してクエリまたはテーブルから一括コピーを作成する方法について説明します。
ms.custom: ''
ms.date: 06/14/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: connectivity
ms.topic: reference
helpviewer_keywords:
- BCPSetBulkMode function
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: 4174a7c769b7eb08ac88606c1787bc2ea4ed9634
ms.sourcegitcommit: 295b9dfc758471ef7d238a2b0f92f93e34acbb1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106054595"
---
# <a name="ibcpsession2bcpsetbulkmode"></a>IBCPSession2::BCPSetBulkMode
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[Driver_OLEDB_Download](../../../includes/driver_oledb_download.md)]

  IBCPSession2::BCPSetBulkMode には、列形式を指定するために [IBCPSession::BCPColFmt &#40;OLE DB&#41;](../../oledb/ole-db-interfaces/ibcpsession-bcpcolfmt-ole-db.md) の代替手段が用意されています。 個々の列形式属性を設定する IBCPSession::BCPColFmt とは異なり、IBCPSession2::BCPSetBulkMode では、すべての属性を設定します。  
  
## <a name="syntax"></a>構文  
  
```cpp  
  
HRESULT BCPSetBulkMode (  
      int property,  
   void * pField,  
   int cbField,  
   void * pRow,  
   int cbRow  
);  
```  
  
## <a name="arguments"></a>引数  
 *property*  
 BYTE 型の定数です。 定数の一覧については、「解説」の表を参照してください。  
  
 *pField*  
 フィールド ターミネータ値を指すポインターです。  
  
 cbField  
 フィールド ターミネータ値の長さです (バイト単位)。  
  
 pRow  
 行ターミネータ値を指すポインターです。  
  
 cbRow  
 行ターミネータ値の長さです (バイト単位)。  
  
## <a name="returns"></a>戻り値  
 IBCPSession2::BCPSetBulkMode では、次のいずれかを返すことができます。  
  
|Message|説明|  
|-|-|  
|**S_OK**|メソッドが成功しました。|  
|**E_FAIL**|プロバイダー固有のエラーが発生しました。詳細を確認するには、ISQLServerErrorInfo インターフェイスを使用してください。|  
|**E_UNEXPECTED**|メソッドの呼び出しが予期されませんでした。 たとえば、IBCPSession2::BCPSetBulkMode が呼び出される前に、**IBCPSession2::BCPInit** メソッドが呼び出されなかった場合などです。|  
|**E_INVALIDARG**|引数が無効です。|  
|**E_OUTOFMEMORY**|メモリ不足エラー。|  
  
## <a name="remarks"></a>解説  
 IBCPSession2::BCPSetBulkMode を使用して、クエリまたはテーブルを一括コピーできます。 IBCPSession2::BCPSetBulkMode を使用してクエリ ステートメントを一括コピー出力する場合は、`IBCPSession::BCPControl(BCP_OPTIONS_HINTS, ...)` を呼び出してクエリ ステートメントを指定する前に、これを呼び出す必要があります。  
  
 RPC 呼び出し構文とバッチ クエリ構文 (`{rpc func};SELECT * from Tbl` など) を 1 つのコマンド テキスト内で組み合わせて使用しないでください。  ICommandPrepare::Prepare からエラーが返され、メタデータを取得できなくなるためです。 ストアド プロシージャの実行とバッチ クエリを 1 つのコマンド テキストで組み合わせて使用する必要がある場合は、ODBC CALL 構文 (`{call func}; SELECT * from Tbl` など) を使用します。  
  
 *property* パラメーターとして使用できる定数の一覧を次の表に示します。  
  
|プロパティ|説明|  
|--------------|-----------------|  
|BCP_OUT_CHARACTER_MODE|文字出力モードを指定します。<br /><br /> BCP.EXE の -c オプションおよび、*eUserDataType* プロパティを **BCP_TYPE_SQLCHARACTER** に設定した IBCPSession::BCPColFmt に対応します。|  
|BCP_OUT_WIDE_CHARACTER_MODE|Unicode 出力モードを指定します。<br /><br /> BCP.EXE の -w オプションおよび、*eUserDataType* プロパティを **BCP_TYPE_SQLNCHAR** に設定した IBCPSession::BCPColFmt に対応します。|  
|BCP_OUT_NATIVE_TEXT_MODE|文字型以外にネイティブ型を指定し、文字型に Unicode を指定します。<br /><br /> BCP.EXE の -N オプションおよび、*eUserDataType* プロパティを、列の型が文字列の場合は **BCP_TYPE_SQLNCHAR**、文字列でない場合は **BCP_TYPE_DEFAULT** に設定した IBCPSession::BCPColFmt に対応します。|  
|BCP_OUT_NATIVE_MODE|ネイティブ データベース型を指定します。<br /><br /> BCP.EXE の -n オプションおよび、*eUserDataType* プロパティを **BCP_TYPE_DEFAULT** に設定した IBCPSession::BCPColFmt に対応します。|  
  
 IBCPSession::BCPControl と IBCPSession2::BCPSetBulkMode は、IBCPSession2::BCPSetBulkMode と競合しない IBCPSession::BCPControl オプションに対して呼び出すことができます。 たとえば、**BCP_OPTION_FIRST** を使用した IBCPSession::BCPControl と IBCPSession2::BCPSetBulkMode を呼び出すことができます。  
  
 **BCP_OPTION_TEXTFILE** を使用した IBCPSession::BCPControl と IBCPSession2::BCPSetBulkMode を呼び出すことはできません。  
  
 IBCPSession::BCPColFmt、IBCPSession::BCPControl、および IBCPSession::BCPReadFmt が含まれる関数呼び出しのシーケンスを使用して IBCPSession2::BCPSetBulkMode を呼び出そうとすると、関数呼び出しの 1 つでシーケンス エラーが返されます。 このエラーを解決するには、IBCPSession::BCPInit を呼び出して設定をリセットし、最初からやり直してください。  
  
 次に、関数のシーケンス エラーが発生する関数呼び出しの例をいくつか示します。  
  
```cpp  
BCPInit("table", "dataFile", "errorFile", BCP_DIRECTION_IN);  
BCPSetBulkMode();  
```  
  
```cpp  
BCPInit("table", "dataFile", "errorFile", BCP_DIRECTION_OUT);  
BCPSetBulkMode();  
BCPReadFmt();  
```  
  
```cpp  
BCPInit(NULL, "dataFile", "errorFile", BCP_DIRECTION_OUT);  
BCPControl(BCP_OPTION_HINTS, "select ...");  
BCPSetBulkMode();  
```  
  
```cpp  
BCPInit("table", "dataFile", "errorFile", BCP_DIRECTION_OUT);  
BCPSetBulkMode();  
BCPColFmt();  
```  
  
```cpp  
BCPInit("table", "dataFile", "errorFile", BCP_DIRECTION_OUT);  
BCPControl(BCP_OPTION_DELAYREADFMT, true);  
BCPReadFmt();  
BCPColFmt();  
```  
  
```cpp  
BCPInit(NULL, "dataFile", "errorFile", BCP_DIRECTION_OUT);  
BCPControl(BCP_OPTION_DELAYREADFMT, true);  
BCPSetBulkMode();  
BCPControl(BCP_OPTION_HINTS, "select ...");  
BCPReadFmt();  
```  
  
```cpp  
BCPInit("table", "dataFile", "errorFile", BCP_DIRECTION_OUT);  
BCPControl(BCP_OPTION_DELAYREADFMT, true);  
BCPColumns();  
```  
  
```cpp  
BCPInit("table", "dataFile", "errorFile", BCP_DIRECTION_OUT);  
BCPControl(BCP_OPTION_DELAYREADFMT, true);  
BCPSetColFmt();  
```  
  
## <a name="example"></a>例  
 次のサンプルでは、IBCPSession2::BCPSetBulkMode の異なる設定を使用して、4 つのファイルを作成します。  
  
```cpp  
  
// compile with: oleaut32.lib ole32.lib  
  
#include <stdio.h>  
#include "msoledbsql.h"  
  
IDBInitialize*  g_pIDBInitialize = NULL;  
IBCPSession2 * g_pIBcpSession = NULL;  
class COLEDBPropSet : public DBPROPSET {  
public:  
   COLEDBPropSet() {  
      rgProperties = NULL;  
      cProperties = 0;  
   };  
   COLEDBPropSet(const GUID& guid) {  
      rgProperties = NULL;  
      cProperties = 0;  
      guidPropertySet = guid;  
   };  
   ~COLEDBPropSet() {  
      for ( ULONG i = 0 ; i < cProperties ; i++ )  
         VariantClear(&rgProperties[i].vValue);  
      CoTaskMemFree(rgProperties);  
   }  
   void SetGUID(const GUID& guid) {  
      guidPropertySet = guid;  
   };  
   bool AddProperty(DWORD dwPropertyID, bool bValue) {  
      if (!Add())  
         return false;  
      rgProperties[cProperties].dwPropertyID = dwPropertyID;  
      rgProperties[cProperties].vValue.vt = VT_BOOL;  
      rgProperties[cProperties].vValue.boolVal = (bValue) ? VARIANT_TRUE : VARIANT_FALSE;  
      cProperties++;  
      return true;  
   };  
   bool AddProperty(DWORD dwPropertyID, long nValue) {  
      if (!Add())  
         return false;  
      rgProperties[cProperties].dwPropertyID  = dwPropertyID;  
      rgProperties[cProperties].vValue.vt     = VT_I4;  
      rgProperties[cProperties].vValue.lVal   = nValue;  
      cProperties++;  
      return true;  
   };  
   bool AddProperty(DWORD dwPropertyID,LPCWSTR szValue) {  
      if (!Add())  
         return false;  
      rgProperties[cProperties].dwPropertyID = dwPropertyID;  
      rgProperties[cProperties].vValue.vt = VT_BSTR;  
      rgProperties[cProperties].vValue.bstrVal = SysAllocString(szValue);  
      cProperties++;  
      return true;  
   };  
   bool Add() {  
      DBPROP* p = (DBPROP*)CoTaskMemRealloc(rgProperties, (cProperties + 1) * sizeof(DBPROP));  
      if (p != NULL) {  
         rgProperties = p;  
         rgProperties[cProperties].dwOptions = DBPROPOPTIONS_REQUIRED;  
         rgProperties[cProperties].colid = DB_NULLID;  
         rgProperties[cProperties].vValue.vt = VT_EMPTY;  
         return true;  
      }  
      else  
         return false;  
   };  
};  
  
void OLEDBCleanUp() {  
   if (g_pIDBInitialize) {  
      g_pIDBInitialize->Release();  
      g_pIDBInitialize = NULL;  
   }  
   if (g_pIBcpSession) {  
      g_pIBcpSession->Release();  
      g_pIBcpSession = NULL;  
   }  
}  
  
BOOL MakeOLEDBConnect(LPWSTR  pServer) {  
   BOOL ret = true;  
   IDBProperties * pIDBProperties = NULL;  
   IDBCreateSession * pIDBCreateSession = NULL;  
   COLEDBPropSet PropSet(DBPROPSET_DBINIT);  
   COLEDBPropSet BcpProperty(DBPROPSET_SQLSERVERDATASOURCE);  
   try {  
      HRESULT hr = CoInitializeEx(NULL,COINIT_MULTITHREADED);   
      hr = CoCreateInstance(MSOLEDBSQL_CLSID, NULL, CLSCTX_INPROC_SERVER, IID_IDBInitialize, (LPVOID *)&g_pIDBInitialize);  
      if (FAILED(hr)) {  
         printf("CoCreateInstance failed\n");  
         return false;  
      }  
      PropSet.AddProperty(DBPROP_INIT_DATASOURCE, (LPWSTR)pServer);  
      PropSet.AddProperty(DBPROP_AUTH_INTEGRATED, L"SSPI");  
      hr = g_pIDBInitialize->QueryInterface(IID_IDBProperties, (void**) &pIDBProperties);  
      if (FAILED(hr)) {  
         printf("g_pIDBInitialize->->QueryInterface(IID_IDBProperties...) failed\n");  
         throw false;  
      }  
      hr = pIDBProperties->SetProperties(1, &PropSet);  
      if (FAILED(hr)) {  
         printf("g_pIDBInitialize->->SetProperties(...) failed\n");  
         throw false;  
      }  
      hr = g_pIDBInitialize->Initialize();  
      if (FAILED(hr)) {  
         printf("g_pIDBInitialize->->Initialize() failed\n");  
         throw false;  
      }  
      BcpProperty.AddProperty(SSPROP_ENABLEFASTLOAD, true);  
      BcpProperty.AddProperty(SSPROP_ENABLEBULKCOPY, true);  
      hr = pIDBProperties->SetProperties(1, &BcpProperty);  
      if (FAILED(hr)) {  
         printf("g_pIDBInitialize->->SetProperties() for bcp failed\n");  
         throw false;  
      }  
      hr = g_pIDBInitialize->QueryInterface(IID_IDBCreateSession, (void**) &pIDBCreateSession);  
      if (FAILED(hr)) {  
         printf("g_pIDBInitialize->QueryInterface(IID_IDBCreateSession..) failed\n");  
         throw false;  
      }  
  
      hr = pIDBCreateSession->CreateSession(NULL, IID_IBCPSession2, (IUnknown**) &g_pIBcpSession);  
      if (FAILED(hr)) {  
         printf("g_pIDBCreateSession->CreateSession() failed\n");  
         throw false;  
      }  
   }  
   catch(...) {  
      ret = false;  
   }  
   if (pIDBProperties)  
      pIDBProperties->Release();  
   if (pIDBCreateSession)  
      pIDBCreateSession->Release();  
   return ret;  
}  
  
BOOL BCPSetBulkMode(LPWSTR pszServer, LPTSTR pszQureryOut, char BCPType, LPWSTR pszDataFile) {  
   HRESULThr;  
   if (!MakeOLEDBConnect(pszServer))  
      return false;  
   hr = g_pIBcpSession->BCPInit(NULL, pszDataFile, NULL, BCP_DIRECTION_OUT );   // bcp init for queryout  
   if (FAILED(hr)) {  
      printf("BCP init failed\n");  
      OLEDBCleanUp();  
      return false;  
   }  
   // setbulkmode  
   char ColTerm[] = "\t";  
   char RowTerm[] = "\r\n";  
   wchar_t wColTerm[] = L"\t";  
   wchar_t wRowTerm[] = L"\r\n";  
   BYTE * pColTerm = NULL;  
   int cbColTerm = NULL;  
   BYTE * pRowTerm = 0;  
   int cbRowTerm = 0;  
   int bulkmode = -1;  
  
   if(BCPType == 'c') {   // bcp -c  
      pColTerm = (BYTE*)ColTerm;  
      pRowTerm = (BYTE*)RowTerm;  
      cbColTerm = 1;  
      cbRowTerm = 2;  
      bulkmode = BCP_OUT_CHARACTER_MODE;  
   }  
   else  
      if(BCPType == 'w') {   // bcp -w  
         pColTerm = (BYTE*)wColTerm;  
         pRowTerm = (BYTE*)wRowTerm;  
         cbColTerm = 2;  
         cbRowTerm = 4;  
         bulkmode = BCP_OUT_WIDE_CHARACTER_MODE;  
      }  
      else  
         if (BCPType == 'n')   // bcp -n  
            bulkmode = BCP_OUT_NATIVE_MODE;  
         else  
            if (BCPType == 'N')   // bcp -n  
               bulkmode = BCP_OUT_NATIVE_TEXT_MODE;  
            else {  
               printf("unknown bcp mode\n");  
               OLEDBCleanUp();  
               return false;  
            }  
            hr = g_pIBcpSession->BCPSetBulkMode(bulkmode, pColTerm, cbColTerm, pRowTerm, cbRowTerm);  
            if (FAILED(hr)) {  
               printf("BCPSetBulkMode failed\n");  
               OLEDBCleanUp();  
               return false;  
            }  
  
            // set queryout TSQL statement  
            hr = g_pIBcpSession->BCPControl(BCP_OPTION_HINTS, pszQureryOut);  
            if (FAILED(hr)) {  
               printf("BCPControl failed\n");  
               OLEDBCleanUp();  
               return false;  
            }  
            // bcp copy  
            DBROWCOUNT nRowsInserted = 0;  
            hr = g_pIBcpSession->BCPExec(&nRowsInserted);  
            if (FAILED(hr)) {  
               printf("BCPExec failed\n");  
               OLEDBCleanUp();  
               return false;  
            }  
            printf("bcp done\n");  
            OLEDBCleanUp();  
            return true;  
}  
  
int main() {  
   BCPSetBulkMode(L"localhost", TEXT("SELECT 'this is a bcp -c test', 1,2") , 'c', L"bcpc.dat");  
   BCPSetBulkMode(L"localhost", TEXT("SELECT 'this is a bcp -w test', 1,2") , 'w', L"bcpw.dat");  
   BCPSetBulkMode(L"localhost", TEXT("SELECT 'this is a bcp -c test', 1,2") , 'n', L"bcpn.dat");  
   BCPSetBulkMode(L"localhost", TEXT("SELECT 'this is a bcp -w test', 1,2") , 'N', L"bcp_N.dat");  
}  
```  
  
## <a name="see-also"></a>参照  
 [IBCPSession2 &#40;OLE DB&#41;](../../oledb/ole-db-interfaces/ibcpsession2-ole-db.md)  
  
  
