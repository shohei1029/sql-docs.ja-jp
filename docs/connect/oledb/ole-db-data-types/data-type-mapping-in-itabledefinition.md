---
title: ITableDefinition でのデータ型のマッピング (OLE DB ドライバー) | Microsoft Docs
description: OLE DB Driver for SQL Server コンシューマーで ITableDefinition::CreateTable メソッドを使用してテーブルを作成するときに SQL Server データ型を指定する方法について説明します。
ms.custom: ''
ms.date: 06/14/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: connectivity
ms.topic: reference
helpviewer_keywords:
- mapping data types [OLE DB]
- OLE DB Driver for SQL Server, data types
- ITableDefinition interface
- DBCOLUMNDESC structure
- data types [OLE DB]
- CreateTable function
- OLE DB, data types
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: e111f1760415a3511686dce17d8dec5784c9a991
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104755562"
---
# <a name="data-type-mapping-in-itabledefinition"></a>ITableDefinition でのデータ型のマッピング
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[Driver_OLEDB_Download](../../../includes/driver_oledb_download.md)]

  OLE DB Driver for SQL Server のコンシューマーは、**ITableDefinition::CreateTable** 関数を使用してテーブルを作成するときに、渡される DBCOLUMNDESC 配列の *pwszTypeName* メンバーに [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] のデータ型を指定できます。 コンシューマーが列のデータ型を名前で指定する場合、DBCOLUMNDESC 構造体の *wType* メンバーで示される OLE DB データ型のマッピングは無視されます。  
  
 DBCOLUMNDESC 構造体の *wType* メンバーを使用して、列の新しいデータ型を OLE DB データ型で指定するときは、OLE DB Driver for SQL Server では OLE DB データ型が次のようにマップされます。  
  
|OLE DB データ型|SQL Server<br /><br /> データ型 (data type)|関連情報|  
|----------------------|------------------------------|----------------------------|  
|DBTYPE_BOOL|**bit**||  
|DBTYPE_BYTES|**binary**、**varbinary**、**image**、**varbinary(max)**|OLE DB Driver for SQL Server によって、DBCOLUMNDESC 構造体の *ulColumnSize* メンバーが確認されます。 この値と [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] インスタンスのバージョンを基に、OLE DB Driver for SQL Server によって型が **image** にマップされます。<br /><br /> *ulColumnSize* の値が **binary** データ型の列の最大長よりも小さい場合、OLE DB Driver for SQL Server によって、DBCOLUMNDESC の *rgPropertySets* メンバーが調査されます。 DBPROP_COL_FIXEDLENGTH が VARIANT_TRUE の場合、OLE DB Driver for SQL Server によって型が **binary** にマップされます。 プロパティの値が VARIANT_FALSE の場合、OLE DB Driver for SQL Server によって型が **varbinary** にマップされます。 いずれの場合も、DBCOLUMNDESC の *ulColumnSize* メンバーによって、作成される SQL Server の列の幅が決まります。|  
|DBTYPE_CY|**money**||  
|DBTYPE_DBTIMESTAMP|**datetime2**||  
|DBTYPE_GUID|**uniqueidentifier**||  
|DBTYPE_I2|**smallint**||  
|DBTYPE_I4|**int**||  
|DBTYPE_I8|**bigint**||
|DBTYPE_NUMERIC|**numeric**|OLE DB Driver for SQL Server によって DBCOLUMDESC の *bPrecision* メンバーと *bScale* メンバーが調査され、**numeric** 型の列の有効桁数と小数点以下桁数が決定されます。|  
|DBTYPE_R4|**real**||  
|DBTYPE_R8|**float**||  
|DBTYPE_STR|**char**、**varchar**、**text**、**varchar(max)**|OLE DB Driver for SQL Server によって、DBCOLUMNDESC 構造体の *ulColumnSize* メンバーが確認されます。 この値と [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] インスタンスのバージョンを基に、OLE DB Driver for SQL Server によって型が **text** にマップされます。<br /><br /> *ulColumnSize* の値がマルチバイト文字のデータ型の列の最大長よりも小さい場合、OLE DB Driver for SQL Server によって、DBCOLUMNDESC の *rgPropertySets* メンバーが調査されます。 DBPROP_COL_FIXEDLENGTH が VARIANT_TRUE の場合、OLE DB Driver for SQL Server によって型が **char** にマップされます。 プロパティの値が VARIANT_FALSE の場合、OLE DB Driver for SQL Server によって型が **varchar** にマップされます。 いずれの場合も、DBCOLUMNDESC の *ulColumnSize* メンバーによって、作成される [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] の列の幅が決まります。|  
|DBTYPE_UDT|**UDT**|UDT 列が必要な場合、**ITableDefinition::CreateTable** では、**DBCOLUMNDESC** 構造体の以下の情報が使用されます。<br /><br /> *pwSzTypeName* が無視されます。<br /><br /> *rgPropertySets* には、「[ユーザー定義型の使用](../../oledb/features/using-user-defined-types.md)」の **DBPROPSET_SQLSERVERCOLUMN** に関するセクションにある説明のとおり、**DBPROPSET_SQLSERVERCOLUMN** プロパティが設定されている必要があります。|  
|DBTYPE_UI1|**tinyint**||  
|DBTYPE_VARIANT|**sql_variant**||
|DBTYPE_WSTR|**nchar**、**nvarchar**、**ntext**、**nvarchar(max)**|OLE DB Driver for SQL Server によって、DBCOLUMNDESC 構造体の *ulColumnSize* メンバーが確認されます。 この値に基づき、OLE DB Driver for SQL Server によって型が **ntext** にマップされます。<br /><br /> *ulColumnSize* の値が Unicode 文字のデータ型の列の最大長よりも小さい場合、OLE DB Driver for SQL Server によって、DBCOLUMNDESC の *rgPropertySets* メンバーが調査されます。 DBPROP_COL_FIXEDLENGTH が VARIANT_TRUE の場合、OLE DB Driver for SQL Server によって型が **nchar** にマップされます。 プロパティの値が VARIANT_FALSE の場合、OLE DB Driver for SQL Server によって型が **nvarchar** にマップされます。 いずれの場合も、DBCOLUMNDESC の *ulColumnSize* メンバーによって、作成される [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] の列の幅が決まります。|  
|DBTYPE_XML|**XML**||  

> [!NOTE]  
>  新しいテーブルの作成時には、OLE DB Driver for SQL Server によって、上記の表に示した OLE DB データ型の列挙値のみがマップされます。 それ以外の OLE DB データ型の列が含まれたテーブルを作成すると、エラーが発生します。  

## <a name="see-also"></a>参照  
 [データ型 &#40;OLE DB&#41;](../../oledb/ole-db-data-types/data-types-ole-db.md)  
  
  
