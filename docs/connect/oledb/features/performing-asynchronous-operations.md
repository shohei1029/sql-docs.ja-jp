---
title: 非同期操作の実行 | Microsoft Docs
description: OLE DB Driver for SQL Server は、非同期データベース操作をサポートしています。これにより、メソッドでは、呼び出し側のスレッドをブロックせずに操作を戻すことができます。
ms.custom: ''
ms.date: 06/12/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: connectivity
ms.topic: reference
helpviewer_keywords:
- initialization [OLE DB Driver for SQL Server]
- database connections [OLE DB Driver for SQL Server]
- data access [OLE DB Driver for SQL Server], asynchronous operations
- connections [OLE DB Driver for SQL Server]
- asynchronous operations [OLE DB Driver for SQL Server]
- rowsets [SQL Server], initializing
- MSOLEDBSQL, asynchronous operations
- OLE DB Driver for SQL Server, asynchronous operations
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: d71b9884b8a87ae05060073f9c2d47eb044387a5
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104742292"
---
# <a name="performing-asynchronous-operations"></a>非同期操作の実行
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[Driver_OLEDB_Download](../../../includes/driver_oledb_download.md)]

  [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] では、アプリケーションは非同期のデータベース操作を実行できます。 非同期処理により、呼び出し側のスレッドをブロックしないで直ちに制御を返すことができるようになります。 これは、マルチスレッドの持つ能力と柔軟性を大きく引き出し、開発者が明示的にスレッドを作成したり、同期を処理する手間を省くことができる機能です。 アプリケーションは、データベース接続を初期化するときや、コマンドの実行結果を初期化するときに、非同期処理を要求します。  
  
## <a name="opening-and-closing-a-database-connection"></a>データベース接続の開閉  
 OLE DB Driver for SQL Server を使用している場合、データ ソース オブジェクトを非同期に初期化するようにデザインされているアプリケーションは、**IDBInitialize::Initialize** を呼び出す前に DBPROP_INIT_ASYNCH プロパティの DBPROPVAL_ASYNCH_INITIALIZE ビットに設定できます。 このプロパティが設定されると、プロバイダーは **Initialize** の呼び出しからすぐに制御を戻し、操作が直ちに完了した場合は S_OK、初期化が非同期に続行される場合は DB_S_ASYNCHRONOUS を返します。 アプリケーションは、データ ソース オブジェクトの **IDBAsynchStatus** または [ISSAsynchStatus](../../oledb/ole-db-interfaces/issasynchstatus-ole-db.md) インターフェイスをクエリし、**IDBAsynchStatus::GetStatus** または [ISSAsynchStatus::WaitForAsynchCompletion](../../oledb/ole-db-interfaces/issasynchstatus-waitforasynchcompletion-ole-db.md) を呼び出して初期化の状態を取得できます。  
  
 また、DBPROPSET_SQLSERVERROWSET プロパティ セットに SSPROP_ISSAsynchStatus プロパティが追加されています。 **ISSAsynchStatus** インターフェイスをサポートするプロバイダーは、値 VARIANT_TRUE を指定してこのプロパティを実装する必要があります。  
  
 **IDBAsynchStatus::Abort** または [ISSAsynchStatus::Abort](../../oledb/ole-db-interfaces/issasynchstatus-abort-ole-db.md) を呼び出すと、非同期 **Initialize** 呼び出しを中止できます。 コンシューマーは、明示的にデータ ソースの非同期の初期化を要求できます。 この要求を行わない場合、**IDBInitialize::Initialize** はデータ ソース オブジェクトが完全に初期化されるまで、制御を戻しません。  
  
> [!NOTE]  
>  接続プールに使われるデータ ソース オブジェクトは、OLE DB Driver for SQL Server から **ISSAsynchStatus** インターフェイスを呼び出せません。 **ISSAsynchStatus** インターフェイスは、プールされたデータ ソース オブジェクトには公開されません。  
>   
>  アプリケーションが明示的にカーソル エンジンの使用を設定している場合、**IOpenRowset::OpenRowset** と **IMultipleResults::GetResult** は非同期処理をサポートしません。  
>   
>  また、リモート処理プロキシ/スタブの dll (MDAC 2.8) は、OLE DB Driver for SQL Server から **ISSAsynchStatus** インターフェイスを呼び出せません。 **ISSAsynchStatus** インターフェイスは、リモート処理経由では公開されません。  
>   
>  サービス コンポーネントは、**ISSAsynchStatus** をサポートしません。  
  
## <a name="execution-and-rowset-initialization"></a>実行と行セットの初期化  
 コマンドの実行結果を非同期に開くようデザインされているアプリケーションは、DBPROP_ROWSET_ASYNCH プロパティに DBPROPVAL_ASYNCH_INITIALIZE ビットを設定できます。 **IDBInitialize::Initialize**、**ICommand::Execute**、**IOpenRowset::OpenRowset** または **IMultipleResults::GetResult** を呼び出す前にこのビットを設定するときは、*riid* 引数を IID_IDBAsynchStatus、IID_ISSAsynchStatus、または IID_IUnknown に設定する必要があります。  
  
 メソッドはすぐに制御を戻し、行セットの初期化が直ちに完了した場合は S_OK、行セットの初期化が非同期に続行される場合は DB_S_ASYNCHRONOUS を返して、*ppRowset* を行セット上の要求されたインターフェイスに設定します。 OLE DB Driver for SQL Server の場合、このインターフェイスは **IDBAsynchStatus** または **ISSAsynchStatus** のいずれかです。 このインターフェイスは、行セットが完全に初期化されるまでは中断状態にあるかのように動作し、**IID_IDBAsynchStatus** または **IID_ISSAsynchStatus** 以外のインターフェイスに対して **QueryInterface** が呼び出された場合、E_NOINTERFACE を返すことがあります。 コンシューマーが明示的に非同期処理を要求しない限り、行セットは同期的に初期化されます。 **IDBAsynchStaus::GetStatus** または **ISSAsynchStatus::WaitForAsynchCompletion** が非同期操作が完了したことを示す値を返した場合、要求したすべてのインターフェイスを使用できます。 これは、必ずしも行セットに完全にデータが格納されたことを意味するものではありませんが、行セットは完成し、完全に機能します。  
  
 実行されたコマンドが行セットを返さない場合でも、このコマンドは、**IDBAsynchStatus** をサポートするオブジェクトを直ちに返します。  
  
 非同期コマンドの実行により複数の結果を取得する必要がある場合は、次の操作を行います。  
  
-   コマンドを実行する前に、DBPROP_ROWSET_ASYNCH プロパティに DBPROPVAL_ASYNCH_INITIALIZE ビットを設定します。  
  
-   **ICommand::Execute** を呼び出し、**IMultipleResults** を要求します。  
  
 その結果、**QueryInterface** を使用して複数の結果のインターフェイスをクエリすることで、**IDBAsynchStatus** および **ISSAsynchStatus** インターフェイスを取得できるようになります。  
  
 コマンドの実行が完了すると、**IMultipleResults** を通常どおり使用できます。ただし、非同期処理の場合は 1 つだけ例外があり、DB_S_ASYNCHRONOUS が返される可能性があります。この場合、**IDBAsynchStatus** または **ISSAsynchStatus** を使用して、操作が完了しているかどうかを確認できます。  
  
## <a name="examples"></a>例  
 次の例では、アプリケーションが非ブロッキング メソッドを呼び出し、いくつか他の処理を実行し、制御を戻して結果を処理します。 **ISSAsynchStatus::WaitForAsynchCompletion** は、非同期実行操作が完了するか、*dwMilisecTimeOut* により指定された時間が経過するまで、内部イベント オブジェクト上で待機します。  
  
```  
// Set the DBPROPVAL_ASYNCH_INITIALIZE bit in the   
// DBPROP_ROWSET_ASYNCH property before calling Execute().  
  
DBPROPSET CmdPropset[1];  
DBPROP CmdProperties[1];  
  
CmdPropset[0].rgProperties = CmdProperties;  
CmdPropset[0].cProperties = 1;  
CmdPropset[0].guidPropertySet = DBPROPSET_ROWSET;  
  
// Set asynch mode for command.  
CmdProperties[0].dwPropertyID = DBPROP_ROWSET_ASYNCH;  
CmdProperties[0].vValue.vt = VT_I4;  
CmdProperties[0].vValue.lVal = DBPROPVAL_ASYNCH_INITIALIZE;  
CmdProperties[0].dwOptions = DBPROPOPTIONS_REQUIRED;  
  
hr = pICommandProps->SetProperties(1, CmdPropset);  
  
hr = pICommand->Execute(  
   pUnkOuter,  
   IID_ISSAsynchStatus,  
   pParams,  
   pcRowsAffected,  
   (IUnknown**)&pISSAsynchStatus);  
  
if (hr == DB_S_ASYNCHRONOUS)  
{  
   // Do some work here...  
  
   hr = pISSAsynchStatus->WaitForAsynchCompletion(dwMilisecTimeOut);  
   if ( hr == S_OK)  
   {  
      hr = pISSAsynchStatus->QueryInterface(IID_IRowset, (void**)&pRowset);  
      pISSAsynchStatus->Release();  
   }  
}  
```  
  
 **ISSAsynchStatus::WaitForAsynchCompletion** は、非同期実行操作が完了するか、*dwMilisecTimeOut* の値が示す時間が経過するまで、内部イベント オブジェクト上で待機します。  
  
 次の例は、複数の結果セットを返す非同期処理のコードです。  
  
```  
DBPROP CmdProperties[1];  
  
// Set asynch mode for command.  
CmdProperties[0].dwPropertyID = DBPROP_ROWSET_ASYNCH;  
CmdProperties[0].vValue.vt = VT_I4;  
CmdProperties[0].vValue.lVal = DBPROPVAL_ASYNCH_INITIALIZE;  
  
hr = pICommand->Execute(  
   pUnkOuter,  
   IID_IMultipleResults,  
   pParams,  
   pcRowsAffected,  
   (IUnknown**)&pIMultipleResults);  
  
// Use GetResults for ISSAsynchStatus.  
hr = pIMultipleResults->GetResult(IID_ISSAsynchStatus, (void **) &pISSAsynchStatus);  
  
if (hr == DB_S_ASYNCHRONOUS)  
{  
   // Do some work here...  
  
   hr = pISSAsynchStatus->WaitForAsynchCompletion(dwMilisecTimeOut);  
   if (hr == S_OK)  
   {  
      hr = pISSAsynchStatus->QueryInterface(IID_IRowset, (void**)&pRowset);  
      pISSAsynchStatus->Release();  
   }  
}  
```  
  
 ブロックを防ぐため、次の例のように、クライアントでは実行中の非同期操作の状態を確認できます。  
  
```  
// Set the DBPROPVAL_ASYNCH_INITIALIZE bit in the   
// DBPROP_ROWSET_ASYNCH property before calling Execute().  
hr = pICommand->Execute(  
   pUnkOuter,  
   IID_ISSAsynchStatus,  
   pParams,  
   pcRowsAffected,  
   (IUnknown**)&pISSAsynchStatus);   
  
if (hr == DB_S_ASYNCHRONOUS)  
{  
   do{  
      // Do some work...  
      hr = pISSAsynchStatus->GetStatus(DB_NULL_HCHAPTER, DBASYNCHOP_OPEN, NULL, NULL, &ulAsynchPhase, NULL);  
   }while (DBASYNCHPHASE_COMPLETE != ulAsynchPhase)  
   if SUCCEEDED(hr)  
   {  
      hr = pISSAsynchStatus->QueryInterface(IID_IRowset, (void**)&pRowset);  
   }  
   pIDBAsynchStatus->Release();  
}  
```  
  
 次の例は、現在実行中の非同期操作を中止する方法を示しています。  
  
```  
// Set the DBPROPVAL_ASYNCH_INITIALIZE bit in the   
// DBPROP_ROWSET_ASYNCH property before calling Execute().  
hr = pICommand->Execute(  
   pUnkOuter,  
   IID_ISSAsynchStatus,  
   pParams,  
   pcRowsAffected,  
   (IUnknown**)&pISSAsynchStatus);  
  
if (hr == DB_S_ASYNCHRONOUS)  
{  
   // Do some work...  
   hr = pISSAsynchStatus->Abort(DB_NULL_HCHAPTER, DBASYNCHOP_OPEN);  
}  
```  
  
## <a name="see-also"></a>参照  
 [OLE DB Driver for SQL Server の機能](../../oledb/features/oledb-driver-for-sql-server-features.md)   
 [行セットのプロパティと動作](../../oledb/ole-db-rowsets/rowset-properties-and-behaviors.md)   
 [ISSAsynchStatus &#40;OLE DB&#41;](../../oledb/ole-db-interfaces/issasynchstatus-ole-db.md)  
  
  
