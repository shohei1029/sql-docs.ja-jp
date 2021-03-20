---
title: 次のフェッチ位置 (OLE DB ドライバー) | Microsoft Docs
description: OLE DB Driver for SQL Server は次のフェッチ位置を追跡します。その結果、GetNextRows メソッドの一連の呼び出しでは行セット全体が読み取られます。
ms.custom: ''
ms.date: 06/14/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: connectivity
ms.topic: reference
helpviewer_keywords:
- fetching rows
- OLE DB rowsets, fetching
- next fetch position
- rowsets [OLE DB], fetching
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: c2917b9ce4377b77f45b8a288f459113da5744f9
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104753082"
---
# <a name="fetching-rows---next-fetch-position-ole-db-driver"></a>行のフェッチ - 次のフェッチ位置 (OLE DB ドライバー)
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[Driver_OLEDB_Download](../../../includes/driver_oledb_download.md)]

  OLE DB Driver for SQL Server は次のフェッチ位置を追跡します。その結果、**GetNextRows** メソッドの一連の呼び出しでは、行をスキップしたり、同じ行を繰り返すことなく、行セット全体が読み取られます。この場合、この一連の呼び出しの間に、スキップされたり、読み取る方向が変更されたり、**FindNextRow**、**Seek**、または **RestartPosition** の各メソッドの呼び出しによって中断されることはありません。 **IRowset::GetNextRows**、**IRowset::RestartPosition**、または **IRowsetIndex::Seek** を呼び出すか、*pBookmark* 値に NULL を指定して **FindNextRow** を呼び出すことにより、次のフェッチ位置が変更されます。 *pBookmark* 値に NULL 以外の値を指定して **FindNextRow** を呼び出しても、次のフェッチ位置には影響しません。  
  
## <a name="see-also"></a>参照  
 [行のフェッチ](../../oledb/ole-db-rowsets/fetching-rows.md)  
  
  
