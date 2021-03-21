---
title: スキーマ行セットのサポート (OLE DB) | Microsoft Docs
description: OLE DB Driver for SQL Server では、Transact-SQL 分散クエリを処理する際に、リンク サーバーからスキーマ情報を返すことができます。
ms.custom: ''
ms.date: 06/12/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: connectivity
ms.topic: reference
helpviewer_keywords:
- schema rowsets [OLE DB]
- OLE DB, schema rowsets
- OLE DB rowsets, schema
- OLE DB Driver for SQL Server, schema rowsets
- rowsets [OLE DB], schema
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: 700d5b770176f9aefdab1ac7dfd43eee21f78d91
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104754162"
---
# <a name="schema-rowset-support-ole-db"></a>スキーマ行セットのサポート (OLE DB)
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[Driver_OLEDB_Download](../../../includes/driver_oledb_download.md)]

  OLE DB Driver for SQL Server では、[!INCLUDE[tsql](../../../includes/tsql-md.md)] 分散クエリを処理する際に、リンク サーバーからスキーマ情報を返すこともできます。  
  
> [!NOTE]  
>  [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] はシノニムをサポートしていますが、シノニムのメタデータは OLE DB Driver for SQL Server から返されません。  
  
 次の表に、OLE DB Driver for SQL Server でサポートされるスキーマ行セットと制限列を示します。  
  
|スキーマ行セット|制限列|  
|-------------------|-------------------------|  
|DBSCHEMA_CATALOGS|CATALOG_NAME|  
|DBSCHEMA_COLUMN_PRIVILEGES|すべての制限がサポートされます。<br /><br /> TABLE_CATALOG、TABLE_SCHEMA、TABLE_NAME、COLUMN_NAME、GRANTOR、GRANTEE|  
|DBSCHEMA_COLUMNS|すべての制限がサポートされます。<br /><br /> TABLE_CATALOG、TABLE_SCHEMA、TABLE_NAME、COLUMN_NAME<br /><br /> 次の追加の列は [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] に固有のものです。<br /><br /> COLUMN_LCID。照合順序のロケール ID です。 COLUMN_LCID の値は Windows LCID と同じです。<br /><br /> COLUMN_COMPFLAGS。照合順序でサポートされる比較を定義します。 データ形式は DBPROB_FINDCOMPAREOPS と同じです。<br /><br /> COLUMN_SORTID。照合順序の [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 並べ替えスタイルです。<br /><br /> COLUMN_TDSCOLLATION。列の [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 照合順序です。<br /><br /> IS_COMPUTED。列が計算列の場合は VARIANT_TRUE、それ以外の場合は VARIANT_FALSE になります。|  
|DBSCHEMA_FOREIGN_KEYS|すべての制限がサポートされます。<br /><br /> PK_TABLE_CATALOG、PK_TABLE_SCHEMA、PK_TABLE_NAME、FK_TABLE_CATALOG、FK_TABLE_SCHEMA、FK_TABLE_NAME|  
|DBSCHEMA_INDEXES|制限 1、2、3、および 5 がサポートされます。<br /><br /> TABLE_CATALOG、TABLE_SCHEMA、INDEX_NAME、TABLE_NAME|  
|DBSCHEMA_PRIMARY_KEYS|すべての制限がサポートされます。<br /><br /> TABLE_CATALOG、TABLE_SCHEMA、TABLE_NAME|  
|DBSCHEMA_PROCEDURE_PARAMETERS|すべての制限がサポートされます。<br /><br /> PROCEDURE_CATALOG、PROCEDURE_SCHEMA、PROCEDURE_NAME、PARAMETER_NAME|  
|DBSCHEMA_PROCEDURES|制限 1、2、および 3 がサポートされます。<br /><br /> PROCEDURE_CATALOG、PROCEDURE_SCHEMA、PROCEDURE_NAME<br /><br /> DBSCHEMA_PROCEDURES は、現在のユーザーによって実行可能なプロシージャまたは現在のユーザーに VIEW DEFINITION 権限が付与されているプロシージャのみを返します。|  
|DBSCHEMA_PROVIDER_TYPES|すべての制限がサポートされます。<br /><br /> DATA_TYPE、BEST_MATCH|  
|DBSCHEMA_SCHEMATA|すべての制限がサポートされます。<br /><br /> CATALOG_NAME、SCHEMA_NAME、SCHEMA_OWNER|  
|DBSCHEMA_STATISTICS|すべての制限がサポートされます。<br /><br /> TABLE_CATALOG、TABLE_SCHEMA、TABLE_NAME|  
|DBSCHEMA_TABLE_CONSTRAINTS|すべての制限がサポートされます。<br /><br /> CONSTRAINT_CATALOG、CONSTRAINT_SCHEMA、CONSTRAINT_NAME、TABLE_CATALOG、TABLE_SCHEMA、TABLE_NAME、CONSTRAINT_TYPE|  
|DBSCHEMA_TABLE_PRIVILEGES|すべての制限がサポートされます。<br /><br /> TABLE_CATALOG、TABLE_SCHEMA、TABLE_NAME、GRANTOR、GRANTEE|  
|DBSCHEMA_TABLES|すべての制限がサポートされます。<br /><br /> TABLE_CATALOG、TABLE_SCHEMA、TABLE_NAME、TABLE_TYPE|  
|DBSCHEMA_TABLES_INFO|すべての制限がサポートされます。<br /><br /> TABLE_CATALOG、TABLE_SCHEMA、TABLE_NAME、TABLE_TYPE|  
  
## <a name="in-this-section"></a>このセクションの内容  
 [スキーマ行セットでの分散クエリのサポート](../../oledb/ole-db/schema-rowsets-distributed-query-support.md)  
  
 [LINKEDSERVERS 行セット &#40;OLE DB&#41;](../../oledb/ole-db/schema-rowsets-linkedservers-rowset.md)  
  
## <a name="see-also"></a>参照  
 [OLE DB Driver for SQL Server のプログラミング](../../oledb/ole-db/oledb-driver-for-sql-server-programming.md)   
 [ユーザー定義型の使用](../../oledb/features/using-user-defined-types.md)  
  
  
