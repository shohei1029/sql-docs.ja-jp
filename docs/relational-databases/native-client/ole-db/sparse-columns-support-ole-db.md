---
description: SQL Server Native Client でのスパース列のサポート (OLE DB)
title: スパース列のサポート (OLE DB) | Microsoft Docs
ms.custom: ''
ms.date: 03/17/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
ms.assetid: 918574b3-c62e-4937-9e5f-37310dedc8f9
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: f336c36293224b16528367c820ded5a6b0c496b0
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104744852"
---
# <a name="sparse-columns-support-in-sql-server-native-client-ole-db"></a>SQL Server Native Client でのスパース列のサポート (OLE DB)
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  このトピックでは、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Native Client OLE DB でのスパース列のサポートについて説明します。 スパース列の詳細については、「 [SQL Server Native Client でのスパース列のサポート](../../../relational-databases/native-client/features/sparse-columns-support-in-sql-server-native-client.md)」を参照してください。 サンプルについては、「[スパース列に対する列およびカタログ メタデータの表示 &#40;OLE DB&#41;](../../../relational-databases/native-client-ole-db-how-to/display-column-and-catalog-metadata-for-sparse-columns-ole-db.md)」を参照してください。  
  
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
 スパース列をサポートするために、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Native Client で次の OLE DB インターフェイスが変更されています。  
  
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
 [SQL Server Native Client &#40;OLE DB&#41;](../../../relational-databases/native-client/ole-db/sql-server-native-client-ole-db.md)  
  
  
