---
title: ISSAsynchStatus (OLE DB ドライバー) | Microsoft Docs
description: OLE DB Driver for SQL Server で ISSAsynchStatus を使用して SQL Server の非同期操作をサポートする方法について説明します。
ms.custom: ''
ms.date: 06/14/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: connectivity
ms.topic: reference
apiname:
- ISSAsynchStatus (OLE DB)
apitype: COM
helpviewer_keywords:
- ISSAsynchStatus interface
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: 1fa0d508fb2f4d6ba5724c98a3b643a929b40782
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104752322"
---
# <a name="issasynchstatus-ole-db"></a>ISSAsynchStatus (OLE DB)
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[Driver_OLEDB_Download](../../../includes/driver_oledb_download.md)]

  **ISSAsynchStatus** インターフェイスでは、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] の非同期操作のサポートが公開されます。 これは、主要な OLE DB インターフェイスである **IDBAsynchStatus** から継承される省略可能なインターフェイスです。 **ISSAsynchStatus** には、**IDBAsynchStatus** から継承される **Abort** メソッドと **GetStatus** メソッドに加えて、非同期操作が完了するかタイムアウトが発生するまで待機する際に使用する新しいメソッドが 1 つ用意されています。  
  
|方法|説明|  
|------------|-----------------|  
|[ISSAsynchStatus::Abort &#40;OLE DB&#41;](../../oledb/ole-db-interfaces/issasynchstatus-abort-ole-db.md)|非同期に実行されている操作を取り消します。|  
|[ISSAsynchStatus::GetStatus &#40;OLE DB&#41;](../../oledb/ole-db-interfaces/issasynchstatus-getstatus-ole-db.md)|非同期に実行されている操作の状態を返します。|  
|[ISSAsynchStatus::WaitForAsynchCompletion &#40;OLE DB&#41;](../../oledb/ole-db-interfaces/issasynchstatus-waitforasynchcompletion-ole-db.md)|非同期に実行されている操作が完了するかタイムアウトが発生するまで待機します。|  
  
## <a name="remarks"></a>解説  
 **ISSAsynchStatus** に実装される **ISSAsynchStatus::GetStatus** メソッドは、**IDBAsynchStatus::GetStatus** メソッドと同じです。ただし、データ ソース オブジェクトの初期化が中止された場合、DB_E_CANCELED ではなく E_UNEXPECTED が返される点が異なります (**ISSAsynchStatus::WaitForAsynchCompletion** は、DB_E_CANCELED を返します)。 これは、初期化の中止後、追加の初期化操作が試行される場合に備えて、データ ソース オブジェクトの状態が通常の状態のままにならないためです。  
  
 次に、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] の非同期実行の使用をサポートしているメソッドを示します。  
  
-   **ICommand::Execute**  
  
-   **IOpenRowset::OpenRowset**  
  
-   **IMultipleResults::GetResult**  
  
## <a name="see-also"></a>参照  
 [インターフェイス &#40;OLE DB&#41;](../../oledb/ole-db-interfaces/oledb-driver-for-sql-server-ole-db-interfaces.md)    
 [非同期操作の実行](../../oledb/features/performing-asynchronous-operations.md)  
  
  
