---
title: SQL Server テーブルの削除 (OLE DB ドライバー) |Microsoft Docs
description: OLE DB Driver for SQL Server の ITableDefinition::DropTable 関数を使用し、データベースから SQL Server テーブルを削除する方法について説明します。
ms.custom: ''
ms.date: 06/14/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: connectivity
ms.topic: reference
helpviewer_keywords:
- tables [OLE DB]
- deleting tables
- OLE DB Driver for SQL Server, tables
- removing tables
- dropping tables
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: d1527835952024f2a10807f98749db85a8244f3a
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104754252"
---
# <a name="dropping-a-sql-server-table"></a>SQL Server テーブルの削除
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[Driver_OLEDB_Download](../../../includes/driver_oledb_download.md)]

  OLE DB Driver for SQL Server では、データベースから [!INCLUDE[msCoName](../../../includes/msconame-md.md)] [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] テーブルを削除するための **ITableDefinition::DropTable** 関数が公開されます。  
  
 テーブル名は、*pTableID* パラメーターの *uName* 共用体の *pwszName* メンバーに Unicode 文字列で指定します。 *pTableID* の *eKind* メンバーを DBKIND_NAME にする必要があります。  
  
## <a name="see-also"></a>参照  
 [テーブルとインデックス](../../oledb/ole-db-tables-indexes/tables-and-indexes.md)  
  
  
