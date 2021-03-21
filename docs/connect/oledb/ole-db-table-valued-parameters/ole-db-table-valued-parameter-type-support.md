---
title: テーブル値パラメーターの型のサポート (OLE DB ドライバー)
description: OLE DB Driver for SQL Server でテーブル値パラメーターの特殊な行セット オブジェクトを作成する方法について説明します。
ms.custom: ''
ms.date: 06/14/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: connectivity
ms.topic: reference
helpviewer_keywords:
- table-valued parameters (OLE DB), API support (OLE DB)
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: 5cbbf3b0319208c6f467f4236824197e22f2a71a
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104750812"
---
# <a name="ole-db-table-valued-parameter-type-support"></a>OLE DB テーブル値パラメーターの型のサポート
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[Driver_OLEDB_Download](../../../includes/driver_oledb_download.md)]

  この記事では、テーブル値パラメーターに対する OLE DB 型のサポートについて説明します。  
  
## <a name="table-valued-parameter-rowset-object"></a>テーブル値パラメーターの行セット オブジェクト  
 テーブル値パラメーターの特殊な行セット オブジェクトを作成できます。 テーブル値パラメーターの行セット オブジェクトを作成するには、ITableDefinitionWithConstraints::CreateTableWithConstraints または IOpenRowset::OpenRowset を使用します。 そのためには、*pTableID* パラメーターの *eKind* メンバーに DBKIND_GUID_NAME を設定し、CLSID_ROWSET_INMEMORY を *guid* メンバーとして指定します。 テーブル値パラメーターのサーバーの型名は、IOpenRowset::OpenRowset の使用時に *pTableID* の *pwszName* メンバーに指定する必要があります。 テーブル値パラメーターの行セット オブジェクトは、標準の OLE DB Driver for SQL Server オブジェクトと同様に動作します。  
  
```  
const GUID CLSID_ROWSET_TVP =   
{0xc7ef28d5, 0x7bee, 0x443f, {0x86, 0xda, 0xe3, 0x98, 0x4f, 0xcd, 0x4d, 0xf9}};  
  
CoType RowsetTVP  
{  
[mandatory] interface IAccessor;  
[mandatory] interface IColumnsInfo;  
[mandatory] interface IConvertType;  
[mandatory] interface IRowset;  
[mandatory] interface IRowsetInfo;  
[optional]  interface IColumnsRowset;  
[optional]  interface IRowsetChange;  
[optional]  interface ISupportErrorInfo;  
};  
```  
  
## <a name="dbtype_table"></a>DBTYPE_TABLE  
 新しい型 DBTYPE_TABLE はテーブル型を表します。 この型は、DBTYPE を必要とするさまざまな OLE DB インターフェイスのテーブル値パラメーターを示します。  
  
```  
#define DBTYPE_TABLE (143)  
```  
  
 DBTYPE_TABLE の形式は、DBTYPE_IUNKNOWN と同じです。 これは、データ バッファー内のオブジェクトへのポインターです。 バインドでの完全な指定のため、コンシューマーは、行セット オブジェクト インターフェイス (IID_IRowset) の 1 つに *iid* を設定して、DBOBJECT バッファーをいっぱいにします。 DBOBJECT がバインドで指定されない場合は、IID_IRowset が想定されます。  
  
 DBTYPE_TABLE とその他の型との間の変換はサポートされません。 IConvertType::CanConvert は、DBTYPE_TABLE から DBTYPE_TABLE への変換以外の要求に対するサポートされない変換について、S_FALSE を返します。 これは、Command オブジェクトの DBCONVERTFLAGS_PARAMETER を想定します。  
  
## <a name="methods"></a>メソッド  
 テーブル値パラメーターをサポートする OLE DB メソッドの詳細については、「[OLE DB テーブル値パラメーターの型のサポート (メソッド)](../../oledb/ole-db-table-valued-parameters/ole-db-table-valued-parameter-type-support-methods.md)」を参照してください。  
  
## <a name="properties"></a>Properties  
 テーブル値パラメーターをサポートする OLE DB プロパティの詳細については、「[OLE DB テーブル値パラメーターの型のサポート (プロパティ)](../../oledb/ole-db-table-valued-parameters/ole-db-table-valued-parameter-type-support-properties.md)」を参照してください。  
  
## <a name="see-also"></a>参照  
 [テーブル値パラメーター &#40;OLE DB&#41;](../../oledb/ole-db-table-valued-parameters/table-valued-parameters-ole-db.md)   
 [テーブル値パラメーターの使用 &#40;OLE DB&#41;](../../oledb/ole-db-how-to/use-table-valued-parameters-ole-db.md)  
  
  
