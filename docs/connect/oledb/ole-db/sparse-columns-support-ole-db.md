---
title: スパース列のサポート (OLE DB) | Microsoft Docs
description: null 値を格納するために最適化された列であるスパース列に対する OLE DB Driver for SQL Server サポートについて説明します。
ms.custom: ''
ms.date: 06/12/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: connectivity
ms.topic: reference
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: 12503fa6339dbf3f16a6564cab899b8802f8e3fb
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104755312"
---
# <a name="sparse-columns-support-ole-db"></a>スパース列のサポート (OLE DB)
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[Driver_OLEDB_Download](../../../includes/driver_oledb_download.md)]

  このトピックでは、OLE DB Driver for SQL Server のスパース列のサポートに関する情報を提供します。 スパース列の詳細については、「[OLE DB Driver for SQL Server のスパース列のサポート](../../oledb/features/sparse-columns-support-in-oledb-driver-for-sql-server.md)」を参照してください。 サンプルについては、「[スパース列に対する列およびカタログ メタデータの表示 &#40;OLE DB&#41;](../../oledb/ole-db-how-to/display-column-and-catalog-metadata-for-sparse-columns-ole-db.md)」を参照してください。  
  
## <a name="ole-db-statement-metadata"></a>OLE DB ステートメント メタデータ  
 [!INCLUDE[ssKatmai](../../../includes/sskatmai-md.md)] 以降では、新しい DBCOLUMNFLAGS フラグ値である DBCOLUMNFLAGS_SS_ISCOLUMNSET を使用できます。 この値は、**column_set** 値である列に対して設定する必要があります。 DBCOLUMNFLAGS フラグは、IColumnsInfo::GetColumnsInfo の *dwFlags* パラメーターと、IColumnsRowset::GetColumnsRowset から返される行セットの DBCOLUMN_FLAGS 列を使用して取得できます。  
  
## <a name="ole-db-catalog-metadata"></a>OLE DB カタログ メタデータ  
 DBSCHEMA_COLUMNS に、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 固有の列が 2 つ追加されています。  
  
|列名|データ型|値およびコメント|  
|-----------------|---------------|---------------------|  
|SS_IS_SPARSE|DBTYPE_BOOL|列がスパース列の場合は VARIANT_TRUE、それ以外の場合は VARIANT_FALSE になります。|  
|SS_IS_COLUMN_SET|DBTYPE_BOOL|列がスパース **column_set** 列の場合は VARIANT_TRUE、それ以外の場合は VARIANT_FALSE になります。|  
  
 また、2 つのスキーマ行セットが追加されています。 これらの行セットは、構造は DBSCHEMA_COLUMNS と同じですが、返される内容が異なります。 DBSCHEMA_COLUMNS_EXTENDED は、スパース列かどうか、**column_set** のメンバーかどうかに関係なく、すべての列を返します。 DBSCHEMA_SPARSE_COLUMN_SET は、スパース **column_set** のメンバーである列のみを返します。  
  
## <a name="ole-db-datatypecompatibility-behavior"></a>OLE DB DataTypeCompatibility の動作  
 (接続文字列内に) **DataTypeCompatibility=80** を使用した場合の動作は、次のように [!INCLUDE[ssVersion2000](../../../includes/ssversion2000-md.md)] クライアントと一貫性があります。  
  
-   新しいスキーマ行セットは表示されず、スキーマ行セットの行セットにそれらのスキーマ行セットの行は含まれません。  
  
-   COLUMNS 行セット内の新しい列は表示されません。  
  
-   DBCOLUMNFLAGS_SS_ISCOLUMNSET が **column_set** 列に対して設定されません。  
  
-   DBCOMPUTEMODE_NOTCOMPUTED が **column_set** 列に対して設定されます。  
  
## <a name="ole-db-support-for-sparse-columns"></a>OLE DB によるスパース列のサポート  
 次の OLE DB インターフェイスは、OLE DB Driver for SQL Server でスパース列をサポートするように修正されました。  
  
|型またはメンバー関数|説明|  
|-----------------------------|-----------------|  
|IColumnsInfo::GetColumnsInfo|*dwFlags* で、新しい DBCOLUMNFLAGS フラグ値である DBCOLUMNFLAGS_SS_ISCOLUMNSET が **column_set** 列に対して設定されます。<br /><br /> DBCOLUMNFLAGS_WRITE が **column_set** 列に対して設定されます。|  
|IColumsRowset::GetColumnsRowset|DBCOLUMN_FLAGS で、新しい DBCOLUMNFLAGS フラグ値である DBCOLUMNFLAGS_SS_ISCOLUMNSET が **column_set** 列に対して設定されます。<br /><br /> **column_set** 列に対して DBCOLUMN_COMPUTEMODE が DBCOMPUTEMODE_DYNAMIC に設定されます。|  
|IDBSchemaRowset::GetSchemaRowset|DBSCHEMA_COLUMNS が、SS_IS_COLUMN_SET と SS_IS_SPARSE という 2 つの新しい列を返します。<br /><br /> DBSCHEMA_COLUMNS は、**column_set** のメンバーでない列のみを返します。<br /><br /> 2 つの新しいスキーマ行セットが追加されています。DBSCHEMA_COLUMNS_EXTENDED は、スパース **column_set** のメンバーシップに関係なくすべての列を返します。 DBSCHEMA_SPARSE_COLUMN_SET は、**column_set** のメンバーである列のみを返します。 これらの新しい行セットの列と制限は DBSCHEMA_COLUMNS と同じです。|  
|IDBSchemaRowset::GetSchemas|IDBSchemaRowset::GetSchemas の使用可能なスキーマ行セットの一覧に、新しい行セットである DBSCHEMA_COLUMNS_EXTENDED と DBSCHEMA_SPARSE_COLUMN_SET の GUID が含まれます。|  
|ICommand::Execute|**select \* from** *table* を使用すると、スパース **column_set** のメンバーでないすべての列と、スパース **column_set** のメンバーであるすべての NULL 以外の列の値を含む XML 列が返されます (存在する場合)。|  
|IOpenRowset::OpenRowset|同じテーブルに対して **select\*** クエリを使用すると、IOpenRowset::OpenRowset から ICommand::Execute と同じ列を持つ行セットが返されます。|  
|ITableDefinition|このインターフェイスには、スパース列や **column_set** 列のための変更はありません。 スキーマを変更する必要のあるアプリケーションでは、適切な [!INCLUDE[tsql](../../../includes/tsql-md.md)] を直接実行する必要があります。|  
  
## <a name="see-also"></a>参照  
 [OLE DB Driver for SQL Server のプログラミング](../../oledb/ole-db/oledb-driver-for-sql-server-programming.md)  
  
  
