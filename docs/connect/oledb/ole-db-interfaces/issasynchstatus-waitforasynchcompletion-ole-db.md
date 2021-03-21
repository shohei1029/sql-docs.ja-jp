---
title: ISSAsynchStatus::WaitForAsynchCompletion (OLE DB ドライバー) | Microsoft Docs
description: ISSAsynchStatus::WaitForAsynchCompletion メソッドが、非同期操作が完了するか OLE DB Driver for SQL Server でタイムアウトが発生するまで待機する方法について説明します。
ms.custom: ''
ms.date: 06/14/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: connectivity
ms.topic: reference
apiname:
- ISSAsynchStatus::WaitForAsynchCompletion (OLE DB)
apitype: COM
helpviewer_keywords:
- WaitForAsynchCompletion method
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: e9355b7f054c7ebb8e19104236293c79eb05302e
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104752342"
---
# <a name="issasynchstatuswaitforasynchcompletion-ole-db"></a>ISSAsynchStatus::WaitForAsynchCompletion (OLE DB)
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[Driver_OLEDB_Download](../../../includes/driver_oledb_download.md)]

  非同期で実行している操作が完了するまで、またはタイムアウトが発生するまで待機します。  
  
## <a name="syntax"></a>構文  
  
```  
  
HRESULT WaitForAsynchCompletion(   
        DWORD dwMillisecTimeOut);  
```  
  
## <a name="arguments"></a>引数  
 *dwMillisecTimeOut*[in]  
 ミリ秒単位のタイムアウト。  
  
## <a name="return-code-values"></a>リターン コードの値  
 S_OK  
 メソッドが成功しました。  
  
 E_UNEXPECTED  
 **ITransaction::Commit** または **ITransaction::Abort** が呼び出されたか、初期化フェーズ中に行セットが取り消されたので、行セットが未使用状態です。  
  
 DB_E_CANCELED  
 行セットの設定中またはデータ ソース オブジェクトの初期化中に、非同期処理が取り消されました。  
  
 DB_S_ASYNCHRONOUS  
 指定したタイムアウトに達しても、操作が完了していません。  
  
> [!NOTE]  
>  **ISSAsynchStatus::WaitForAsynchCompletion** メソッドでは、上記のリターン コード値以外に、主要な OLEDB **ICommand::Execute** メソッドや **IDBInitialize::Initialize** メソッドによって返されたリターン コード値もサポートします。  
  
## <a name="remarks"></a>解説  
 タイムアウト値 (ミリ秒) が経過するか、保留になっている操作が完了するまでは、**ISSAsynchStatus::WaitForAsynchCompletion** メソッドから制御が戻りません。 **Command** オブジェクトには、タイムアウトまでにクエリが実行される秒数を制御する **CommandTimeout** プロパティがあります。**ISSAsynchStatus::WaitForAsynchCompletion** メソッドと組み合わせて使用すると、**CommandTimeout** プロパティは無視されます。  
  
 非同期操作では、タイムアウト プロパティが無視されます。 **ISSAsynchStatus::WaitForAsynchCompletion** のタイムアウト パラメーターに、制御が呼び出し元に返されるまでに経過する最大時間を指定します。 タイムアウトが発生すると、DB_S_ASYNCHRONOUS が返されます。 タイムアウトによって非同期操作が取り消されることはありません。 タイムアウト期間内に完了しない非同期操作をアプリケーションで取り消す必要がある場合、タイムアウトを待機後、DB_S_ASYNCHRONOUS が返されたときに明示的に操作を取り消す必要があります。  
  
> [!NOTE]  
>  OLE DB サービス コンポーネントを使用していて、DB_S_ASYNCHRONOUS を待機しているときに S_OK が返されることがあります。そのため、S_OK または DB_S_ASYNCHRONOUS が返される場合は、[ISSAsynchStatus::GetStatus](../../oledb/ole-db-interfaces/issasynchstatus-getstatus-ole-db.md) を呼び出して完了をチェックする必要があります。  
  
 *dwMillisecTimeOut* の値を INFINITE に設定すると、**ISSAsynchStatus::WaitForAsynchCompletion** メソッドは操作が完了するまでブロックされます。 *dwMillisecTimeOut* の値を 0 に設定すると、メソッドからすぐに制御が戻り、保留中の操作の状態が返されます。 操作が完了する前にタイムアウトが発生すると、DB_S_ASYNCHRONOUS が返されます。  
  
 タイムアウトが発生する前に操作が完了すると、返される HRESULT には操作から返された HRESULT が設定されます (返される HRESULT は、操作が同期的に実行された場合に返される HRESULT になります)。  
  
 また、DBPROPSET_SQLSERVERROWSET プロパティ セットに SSPROP_ISSAsynchStatus プロパティが追加されています。 [ISSAsynchStatus](../../oledb/ole-db-interfaces/issasynchstatus-ole-db.md) インターフェイスをサポートするプロバイダーは、値 VARIANT_TRUE を指定してこのプロパティを実装する必要があります。  
  
## <a name="see-also"></a>参照  
 [非同期操作の実行](../../oledb/features/performing-asynchronous-operations.md)   
 [ISSAsynchStatus &#40;OLE DB&#41;](../../oledb/ole-db-interfaces/issasynchstatus-ole-db.md)  
  
  
