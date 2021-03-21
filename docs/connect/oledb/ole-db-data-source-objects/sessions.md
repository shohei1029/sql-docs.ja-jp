---
title: セッション (OLE DB ドライバー)
description: OLE DB Driver for SQL Server を使用して SQL Server データベースに接続する方法について説明します。 セッションは、SQL Server のインスタンスへの 1 つの接続を表します。
ms.custom: ''
ms.date: 06/14/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: connectivity
ms.topic: reference
helpviewer_keywords:
- sessions [OLE DB]
- OLE DB Driver for SQL Server, sessions
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: 0edc091bf52dbb5113300b258e63f207b301f069
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104755552"
---
# <a name="sessions"></a>セッション
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[Driver_OLEDB_Download](../../../includes/driver_oledb_download.md)]

  OLE DB Driver for SQL Server のセッションは、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] のインスタンスへの 1 つの接続を表します。  
  
 OLE DB Driver for SQL Server では、セッションでデータ ソースのトランザクション領域を区切ることが必要です。 特定のセッション オブジェクトから作成されたすべてのコマンド オブジェクトは、そのセッション オブジェクトのローカル トランザクションまたは分散トランザクションに関係します。  
  
 初期化されたデータ ソースで最初に作成されるセッション オブジェクトが、初期化時に確立された [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 接続を受け取ります。 そのセッション オブジェクトのインターフェイスのすべての参照が解放されると、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] のインスタンスへの接続で、データ ソースで作成される別のセッション オブジェクトを使用できるようになります。  
  
 データ ソースで新たに作成されるセッション オブジェクトは、データ ソースでの指定に従って、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] のインスタンスへの独自の接続を確立します。 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] のインスタンスへの接続は、そのセッションを作成したオブジェクトに対するすべての参照がアプリケーションによって解放された時点で削除されます。  
  
 次の例では、OLE DB Driver for SQL Server を使用して [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] データベースに接続する方法について説明します。  
  
```  
int main()  
{  
    // Interfaces used in the example.  
    IDBInitialize*      pIDBInitialize      = NULL;  
    IDBCreateSession*   pIDBCreateSession   = NULL;  
    IDBCreateCommand*   pICreateCmd1        = NULL;  
    IDBCreateCommand*   pICreateCmd2        = NULL;  
    IDBCreateCommand*   pICreateCmd3        = NULL;  
  
    // Initialize COM.  
    if (FAILED(CoInitialize(NULL)))  
    {  
        // Display error from CoInitialize.  
        return (-1);  
    }  
  
    // Get the memory allocator for this task.  
    if (FAILED(CoGetMalloc(MEMCTX_TASK, &g_pIMalloc)))  
    {  
        // Display error from CoGetMalloc.  
        goto EXIT;  
    }  
  
    // Create an instance of the data source object.  
    if (FAILED(CoCreateInstance(CLSID_MSOLEDBSQL, NULL,  
        CLSCTX_INPROC_SERVER, IID_IDBInitialize, (void**)  
        &pIDBInitialize)))  
    {  
        // Display error from CoCreateInstance.  
        goto EXIT;  
    }  
  
    // The InitFromPersistedDS function   
    // performs IDBInitialize->Initialize() establishing  
    // the first application connection to the instance of SQL Server.  
    if (FAILED(InitFromPersistedDS(pIDBInitialize, L"MyDataSource",  
        NULL, NULL)))  
    {  
        goto EXIT;  
    }  
  
    // The IDBCreateSession interface is implemented on the data source  
    // object. Maintaining the reference received maintains the   
    // connection of the data source to the instance of SQL Server.  
    if (FAILED(pIDBInitialize->QueryInterface(IID_IDBCreateSession,  
        (void**) &pIDBCreateSession)))  
    {  
        // Display error from pIDBInitialize.  
        goto EXIT;  
    }  
  
    // Releasing this has no effect on the SQL Server connection  
    // of the data source object because of the reference maintained by  
    // pIDBCreateSession.  
    pIDBInitialize->Release();  
    pIDBInitialize = NULL;  
  
    // The session created next receives the SQL Server connection of  
    // the data source object. No new connection is established.  
    if (FAILED(pIDBCreateSession->CreateSession(NULL,  
        IID_IDBCreateCommand, (IUnknown**) &pICreateCmd1)))  
    {  
        // Display error from pIDBCreateSession.  
        goto EXIT;  
    }  
  
    // A new connection to the instance of SQL Server is established to support the  
    // next session object created. On successful completion, the  
    // application has two active connections on the SQL Server.  
    if (FAILED(pIDBCreateSession->CreateSession(NULL,  
        IID_IDBCreateCommand, (IUnknown**) &pICreateCmd2)))  
    {  
        // Display error from pIDBCreateSession.  
        goto EXIT;  
    }  
  
    // pICreateCmd1 has the data source connection. Because the  
    // reference on the IDBCreateSession interface of the data source  
    // has not been released, releasing the reference on the session  
    // object does not terminate a connection to the instance of SQL Server.  
    // However, the connection of the data source object is now   
    // available to another session object. After a successful call to   
    // Release, the application still has two active connections to the   
    // instance of SQL Server.  
    pICreateCmd1->Release();  
    pICreateCmd1 = NULL;  
  
    // The next session created gets the SQL Server connection  
    // of the data source object. The application has two active  
    // connections to the instance of SQL Server.  
    if (FAILED(pIDBCreateSession->CreateSession(NULL,  
        IID_IDBCreateCommand, (IUnknown**) &pICreateCmd3)))  
    {  
        // Display error from pIDBCreateSession.  
        goto EXIT;  
    }  
  
EXIT:  
    // Even on error, this does not terminate a SQL Server connection   
    // because pICreateCmd1 has the connection of the data source   
    // object.  
    if (pICreateCmd1 != NULL)  
        pICreateCmd1->Release();  
  
    // Releasing the reference on pICreateCmd2 terminates the SQL  
    // Server connection supporting the session object. The application  
    // now has only a single active connection on the instance of SQL Server.  
    if (pICreateCmd2 != NULL)  
        pICreateCmd2->Release();  
  
    // Even on error, this does not terminate a SQL Server connection   
    // because pICreateCmd3 has the connection of the   
    // data source object.  
    if (pICreateCmd3 != NULL)  
        pICreateCmd3->Release();  
  
    // On release of the last reference on a data source interface, the  
    // connection of the data source object to the instance of SQL Server is broken.  
    // The example application now has no SQL Server connections active.  
    if (pIDBCreateSession != NULL)  
        pIDBCreateSession->Release();  
  
    // Called only if an error occurred while attempting to get a   
    // reference on the IDBCreateSession interface of the data source.  
    // If so, the call to IDBInitialize::Uninitialize terminates the   
    // connection of the data source object to the instance of SQL Server.  
    if (pIDBInitialize != NULL)  
    {  
        if (FAILED(pIDBInitialize->Uninitialize()))  
        {  
            // Uninitialize is not required, but it fails if an  
            // interface has not been released. Use it for  
            // debugging.  
        }  
        pIDBInitialize->Release();  
    }  
  
    if (g_pIMalloc != NULL)  
        g_pIMalloc->Release();  
  
    CoUninitialize();  
  
    return (0);  
}  
```  
  
 OLE DB Driver for SQL Server のセッション オブジェクトを [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] のインスタンスに接続すると、セッション オブジェクトの作成と解放を頻繁に行うアプリケーションでは、大きなオーバーヘッドが発生する場合があります。 OLE DB Driver for SQL Server のセッション オブジェクトを効率的に管理することで、このオーバーヘッドを最小限に抑えることができます。 OLE DB Driver for SQL Server アプリケーションでは、オブジェクトのインターフェイスへの参照を少なくとも 1 つ保持することにより、セッション オブジェクトの [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 接続をアクティブにしておくことができます。  
  
 たとえば、コマンド作成オブジェクト参照のプールを保持することで、プール内にあるセッション オブジェクトのアクティブな接続が維持されます。 セッション オブジェクトが必要になると、プールを保持するコードは、セッションを必要とするアプリケーション メソッドに対して、有効な **IDBCreateCommand** インターフェイス ポインターを渡します。 アプリケーション メソッドでセッションが不要になると、このメソッドは、プールを保持するコードにインターフェイス ポインターを返します。ただし、コマンド作成オブジェクトへのアプリケーションの参照は解放されません。  
  
> [!NOTE]  
>  上記の例で **IDBCreateCommand** インターフェイスを使用したのは、**ICommand** インターフェイスに **GetDBSession** メソッドが実装されているためです。このメソッドは、オブジェクトが作成されたセッションを特定できる、コマンドまたは行セットのスコープ内にある唯一のメソッドです。 したがって、コマンド オブジェクトを使用すると、追加のセッションを作成できるデータ ソース オブジェクト ポインターをアプリケーションで取得できます。  
  
## <a name="see-also"></a>参照  
 [データ ソース オブジェクト &#40;OLE DB&#41;](../../oledb/ole-db-data-source-objects/data-source-objects-ole-db.md)  
  
  
