---
title: 行ブックマーク (OLE DB ドライバー) | Microsoft Docs
description: OLE DB Driver for SQL Server のコンシューマーでブックマークを使用して行にすばやく戻る方法について説明します。そのためには、ブックマーク値に基づいて行にランダムにアクセスします。
ms.custom: ''
ms.date: 06/14/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: connectivity
ms.topic: reference
helpviewer_keywords:
- bookmarks [OLE DB]
- OLE DB Driver for SQL Server, bookmarks
- rowsets [OLE DB], bookmarks
- OLE DB rowsets, bookmarks
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: ca8688c797f304ae99f0382a3f2f242a0cc21e8e
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104753122"
---
# <a name="bookmarks"></a>ブックマーク
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[Driver_OLEDB_Download](../../../includes/driver_oledb_download.md)]

  ブックマークを使用すると、コンシューマーは行をすばやく返すことができます。 コンシューマーはブックマークの値を基に、行にランダムにアクセスできます。 ブックマーク列は、行セットの列 0 です。 コンシューマーは、バインド構造体の dwFlag フィールド値に DBCOLUMNSINFO_ISBOOKMARK を設定して、その列がブックマークに使用されることを示します。 また、コンシューマーは行セット プロパティ DBPROP_BOOKMARKS に VARIANT_TRUE を設定します。 その結果、行セットに列 0 が存在できるようになります。 **IRowsetLocate::GetRowsAt** メソッドを使用すると、ブックマークからのオフセットで指定された行で始まる行が取り出されます。  
  
## <a name="see-also"></a>参照  
 [行セット](../../oledb/ole-db-rowsets/rowsets.md)  
  
  
