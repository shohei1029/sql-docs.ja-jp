---
title: IOpenRowset を使用して行セットを作成する (OLE DB ドライバー) | Microsoft Docs
description: OLE DB Driver for SQL Server でサポートされている IOpenRowset::OpenRowset メソッドを使用して行セットを返す方法と、その使用上の制限事項について説明します。
ms.custom: ''
ms.date: 06/14/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: connectivity
ms.topic: reference
helpviewer_keywords:
- IOpenRowset interface
- rowsets [OLE DB], creating
- OLE DB Driver for SQL Server, rowsets
- OLE DB rowsets, creating
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: 2c962294f0b2c6411b0fccf87d6af0c6273a05b9
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104736362"
---
# <a name="creating-a-rowset-with-iopenrowset"></a>IOpenRowset を使用した行セットの作成
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[Driver_OLEDB_Download](../../../includes/driver_oledb_download.md)]

  OLE DB Driver for SQL Server では、**IOpenRowset::OpenRowset** メソッドをサポートしますが、次の制限があります。  
  
-   *pTableID* パラメーターが指すデータベース ID (DBID) 構造体に、ベース テーブルまたはビューを指定する必要があります。  
  
-   DBID の *eKind* メンバーに DBKIND_NAME を指定する必要があります。  
  
-   DBID の *uName* メンバーには、既存のベース テーブルまたはビューの名前を Unicode 文字列で指定する必要があります。  
  
-   **OpenRowset** の *pIndexID* パラメーターには NULL を指定する必要があります。  
  
 **IOpenRowset::OpenRowset** の結果セットには、1 つの行セットが含まれます。 1 つの行セットが含まれる結果セットは、[!INCLUDE[msCoName](../../../includes/msconame-md.md)] [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] カーソルでサポートできます。 開発者はカーソル サポートによって、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] のコンカレンシー メカニズムを使用できます。  
  
## <a name="see-also"></a>参照  
 [行セット](../../oledb/ole-db-rowsets/rowsets.md)  
  
  
