---
description: ISSAsynchStatus (Native Client OLE DB プロバイダー)
title: ISSAsynchStatus (Native Client OLE DB provider) |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
apiname:
- ISSAsynchStatus (OLE DB)
apitype: COM
helpviewer_keywords:
- ISSAsynchStatus interface
ms.assetid: c643f09f-9ccc-4d8b-9243-3cde86c2bd46
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: cbc39aef7018c449e38455e80785be223f6d4ff2
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104748992"
---
# <a name="issasynchstatus-native-client-ole-db-provider"></a>ISSAsynchStatus (Native Client OLE DB プロバイダー)
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  **ISSAsynchStatus** は、非同期操作のサポートを公開 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] します。 これは、コア OLE DB インターフェイス **id** から継承される省略可能なインターフェイスです。 **ISSAsynchStatus** には、**IDBAsynchStatus** から継承される **Abort** メソッドと **GetStatus** メソッドに加えて、非同期操作が完了するかタイムアウトが発生するまで待機する際に使用する新しいメソッドが 1 つ用意されています。  
  
|方法|説明|  
|------------|-----------------|  
|[ISSAsynchStatus::Abort &#40;OLE DB&#41;](../../relational-databases/native-client-ole-db-interfaces/issasynchstatus-abort-ole-db.md)|非同期に実行されている操作を取り消します。|  
|[ISSAsynchStatus::GetStatus &#40;OLE DB&#41;](../../relational-databases/native-client-ole-db-interfaces/issasynchstatus-getstatus-ole-db.md)|非同期に実行されている操作の状態を返します。|  
|[ISSAsynchStatus::WaitForAsynchCompletion &#40;OLE DB&#41;](../../relational-databases/native-client-ole-db-interfaces/issasynchstatus-waitforasynchcompletion-ole-db.md)|非同期に実行されている操作が完了するかタイムアウトが発生するまで待機します。|  
  
## <a name="remarks"></a>解説  
 **ISSAsynchStatus** に実装される **ISSAsynchStatus::GetStatus** メソッドは、**IDBAsynchStatus::GetStatus** メソッドと同じです。ただし、データ ソース オブジェクトの初期化が中止された場合、DB_E_CANCELED ではなく E_UNEXPECTED が返される点が異なります (**ISSAsynchStatus::WaitForAsynchCompletion** は、DB_E_CANCELED を返します)。 これは、初期化の中止後、追加の初期化操作が試行される場合に備えて、データ ソース オブジェクトの状態が通常の状態のままにならないためです。  
  
 次に、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の非同期実行の使用をサポートしているメソッドを示します。  
  
-   **ICommand::Execute**  
  
-   **IOpenRowset::OpenRowset**  
  
-   **IMultipleResults::GetResult**  
  
## <a name="see-also"></a>参照  
 [インターフェイス &#40;OLE DB&#41;](./sql-server-native-client-ole-db-interfaces.md)   
 [非同期操作の実行](../../relational-databases/native-client/features/performing-asynchronous-operations.md)  
  
