---
description: IDBProperties (Native Client OLE DB プロバイダー)
title: IDBProperties (Native Client OLE DB provider) |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
ms.assetid: 2e5a4fd8-5164-495a-9986-3477aef8d8a5
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 59e98bfcdec717aaed8312d6c5c066873e219d3e
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104756072"
---
# <a name="idbproperties-native-client-ole-db-provider"></a>IDBProperties (Native Client OLE DB プロバイダー)
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  OLE DB 標準仕様では、プロバイダーが **DBPROPINFO::vValues** に VT_EMPTY を指定することを認めています。 ただし、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] で **IDBProperties::GetPropertyInfo** を呼び出して行セット プロパティを取得すると、 **DBPROPSET_ROWSETALL** Native Client OLE DB は、常に VT_EMPTY を返します。  
  
## <a name="see-also"></a>参照  
 [インターフェイス &#40;OLE DB&#41;](./sql-server-native-client-ole-db-interfaces.md)  
  
