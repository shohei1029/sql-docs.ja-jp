---
title: 行セット内のデータの更新 (OLE DB ドライバー)
description: SQL Server 用の OLE DB ドライバーでは、コンシューマーが SQL Server のデータを含む変更可能な行セットを更新するときに、該当するデータが更新されます。そのしくみについて説明します。
ms.custom: ''
ms.date: 06/14/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: connectivity
ms.topic: reference
helpviewer_keywords:
- updating data [SQL Server]
- rowsets [OLE DB], updating data
- OLE DB Driver for SQL Server, rowsets
- OLE DB rowsets, updating data
- OLE DB Driver for SQL Server, data updates
- data updates [SQL Server], OLE DB
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: 3a9bb3a04af6f75eda05a46da655a971bd60cc71
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104755772"
---
# <a name="updating-data-in-rowsets"></a>行セット内のデータの更新
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[Driver_OLEDB_Download](../../../includes/driver_oledb_download.md)]

  SQL Server 用の OLE DB ドライバーでは、コンシューマーが [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] のデータを含む変更可能な行セットを更新するときに、該当するデータが更新されます。 コンシューマーが **IRowsetChange** インターフェイスまたは **IRowsetUpdate** インターフェイスのいずれかのサポートを要求すると、変更可能な行セットが作成されます。  
  
 OLE DB Driver for SQL Server による変更が可能な行セットはすべて、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] カーソルを使用して、行セットがサポートされます。 行セット プロパティ DBPROP_LOCKMODE は、カーソルでの [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] のコンカレンシー制御動作を変更し、更新可能な行セット内の行をフェッチする動作や、その行セット内のデータの整合性に関するエラーを生成する動作を決定します。  
  
 OLE DB Driver for SQL Server では、更新前と更新後の行の同期がサポートされます。  
  
> [!NOTE]  
>  IRowChange::SetColumns を使用して、行オブジェクトの 1 つ以上の名前付き列の値を設定できます。  
  
## <a name="in-this-section"></a>このセクションの内容  
  
-   [SQL Server カーソルでのデータ更新](../../oledb/ole-db-rowsets/updating-data-in-sql-server-cursors.md)  
  
-   [行の再同期](../../oledb/ole-db-rowsets/updating-data-in-rowsets-resynchronizing-rows.md)  
  
## <a name="see-also"></a>参照  
 [行セット](../../oledb/ole-db-rowsets/rowsets.md)  
  
  
