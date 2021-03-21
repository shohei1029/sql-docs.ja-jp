---
title: SQL Server テーブルから列を削除する (OLE DB ドライバー)
description: OLE DB Driver for SQL Server からは ITableDefinition::DropColumn 関数が公開されます。この関数を利用すると、コンシューマーは SQL Server テーブルから列を削除できます。
ms.custom: ''
ms.date: 06/14/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: connectivity
ms.topic: reference
helpviewer_keywords:
- columns [OLE DB]
- removing columns
- DropColumn function
- OLE DB Driver for SQL Server, columns
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: 09c1e60323334f80de047f9dece028ad77921860
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104754262"
---
# <a name="removing-a-column-from-a-sql-server-table"></a>SQL Server テーブルからの列の削除
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[Driver_OLEDB_Download](../../../includes/driver_oledb_download.md)]

  OLE DB Driver for SQL Server では、**ITableDefinition::DropColumn** 関数が公開されます。 コンシューマーはこの関数を使用して、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] テーブルから列を削除できます。  
  
 コンシューマーはテーブル名は、*pTableID* パラメーターの *uName* 共用体の *pwszName* メンバーに Unicode 文字列で指定します。 *pTableID* の *eKind* メンバーを DBKIND_NAME にする必要があります。  
  
 列名は *pColumnID* パラメーターの *uName* 共用体の *pwszName* メンバーに指定します。 列名は Unicode 文字列で指定します。 *pColumnID* の *eKind* メンバーを DBKIND_NAME にする必要があります。  
  
## <a name="example"></a>例  
  
### <a name="code"></a>コード  
  
```  
DBID TableID;  
DBID ColumnID;  
HRESULT hr;  
  
TableID.eKind = DBKIND_NAME;  
TableID.uName.pwszName = L"MyTableName";  
  
ColumnID.eKind = DBKIND_NAME;  
ColumnID.uName.pwszName = L"MyColumnName";  
  
hr = m_pITableDefinition->DropColumn(&TableID, &ColumnID);  
```  
  
## <a name="see-also"></a>参照  
 [テーブルとインデックス](../../oledb/ole-db-tables-indexes/tables-and-indexes.md)  
  
  
