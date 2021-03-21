---
description: ITableDefinition (Native Client OLE DB provider) でのデータ型マッピング
title: ITableDefinition (Native Client OLE DB provider) でのデータ型マッピング |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- mapping data types [OLE DB]
- SQL Server Native Client OLE DB provider, data types
- ITableDefinition interface
- DBCOLUMNDESC structure
- data types [OLE DB]
- CreateTable function
- OLE DB, data types
ms.assetid: 13292d1f-c17e-4d11-bf98-3460a10cbb18
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 1069b28011fa4a3319c6effc26cb6fd0211b4ca3
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104748452"
---
# <a name="sql-server-native-client-data-type-mapping-in-itabledefinition"></a>ITableDefinition でのデータ型マッピングの SQL Server Native Client
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  **Itabledefinition:: CreateTable** 関数を使用してテーブルを作成する場合、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client OLE DB プロバイダーコンシューマーは、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 渡される Dbcolumndesc 配列の *pwszTypeName* メンバーにデータ型を指定できます。 コンシューマーが列のデータ型を名前で指定する場合、DBCOLUMNDESC 構造体の *wType* メンバーで示される OLE DB データ型のマッピングは無視されます。  
  
 DBCOLUMNDESC 構造の *Wtype* メンバーを使用して OLE DB データ型で新しい列のデータ型を指定する場合、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client OLE DB プロバイダーは OLE DB のデータ型を次のようにマップします。  
  
|OLE DB データ型|SQL Server<br /><br /> データ型 (data type)|関連情報|  
|----------------------|------------------------------|----------------------------|  
|DBTYPE_BOOL|**bit**||  
|DBTYPE_BYTES|**binary**、**varbinary**、**image**、**varbinary(max)**|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]Native Client OLE DB プロバイダーは、DBCOLUMNDESC 構造体の *Ulcolumnsize* メンバーを検査します。 Native Client OLE DB プロバイダーは、値とインスタンスのバージョンに基づいて、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 型を **image** にマップします。<br /><br /> *Ulcolumnsize* の値が **binary** データ型の列の最大長よりも小さい場合、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] NATIVE CLIENT OLE DB プロバイダーは dbcolumndesc *rgPropertySets* メンバーを検査します。 DBPROP_COL_FIXEDLENGTH が VARIANT_TRUE 場合は、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client OLE DB プロバイダーによって型が **バイナリ** にマップされます。 プロパティの値が VARIANT_FALSE 場合、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client OLE DB プロバイダーはその型を **varbinary** にマップします。 いずれの場合も、DBCOLUMNDESC の *ulColumnSize* メンバーによって、作成される SQL Server の列の幅が決まります。|  
|DBTYPE_CY|**money**||  
|DBTYPE_DBTIMESTAMP|**datetime**||  
|DBTYPE_GUID|**uniqueidentifier**||  
|DBTYPE_I2|**smallint**||  
|DBTYPE_I4|**int**||  
|DBTYPE_NUMERIC|**numeric**|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]Native Client OLE DB プロバイダーは、**数値** 列の有効桁数と小数点以下桁数を決定するために、Dbcolumn desc *Bprecision* メンバーと *bprecision* メンバーを検査します。|  
|DBTYPE_R4|**real**||  
|DBTYPE_R8|**float**||  
|DBTYPE_STR|**char**、**varchar**、**text**、**varchar(max)**|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]Native Client OLE DB プロバイダーは、DBCOLUMNDESC 構造体の *Ulcolumnsize* メンバーを検査します。 Native Client OLE DB プロバイダーは、インスタンスの値とバージョンに基づいて、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 型を **テキスト** にマップします。<br /><br /> *Ulcolumnsize* の値がマルチバイト文字のデータ型の列の最大長よりも小さい場合、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client OLE DB プロバイダーは Dbcolumndesc *rgPropertySets* メンバーを検査します。 DBPROP_COL_FIXEDLENGTH が VARIANT_TRUE 場合、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client OLE DB プロバイダーは型を **char** にマップします。 プロパティの値が VARIANT_FALSE 場合、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client OLE DB プロバイダーは型を **varchar** にマップします。 いずれの場合も、DBCOLUMNDESC の *ulColumnSize* メンバーによって、作成される [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の列の幅が決まります。|  
|DBTYPE_UDT|**UDT**|UDT 列が必要な場合、**ITableDefinition::CreateTable** では、**DBCOLUMNDESC** 構造体の以下の情報が使用されます。<br /><br /> *pwSzTypeName* が無視されます。<br /><br /> *rgPropertySets* には、「[ユーザー定義型の使用](../../relational-databases/native-client/features/using-user-defined-types.md)」の **DBPROPSET_SQLSERVERCOLUMN** に関するセクションにある説明のとおり、**DBPROPSET_SQLSERVERCOLUMN** プロパティが設定されている必要があります。|  
|DBTYPE_UI1|**tinyint**||  
|DBTYPE_WSTR|**nchar**、**nvarchar**、**ntext**、**nvarchar(max)**|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]Native Client OLE DB プロバイダーは、DBCOLUMNDESC 構造体の *Ulcolumnsize* メンバーを検査します。 Native Client OLE DB プロバイダーは、値に基づいて、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 型を **ntext** にマップします。<br /><br /> *Ulcolumnsize* の値が Unicode 文字のデータ型の列の最大長よりも小さい場合、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client OLE DB プロバイダーは Dbcolumndesc *rgPropertySets* メンバーを検査します。 DBPROP_COL_FIXEDLENGTH が VARIANT_TRUE 場合、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client OLE DB プロバイダーはその型を **nchar** にマップします。 プロパティの値が VARIANT_FALSE 場合は、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client OLE DB プロバイダーによって型が **nvarchar** にマップされます。 いずれの場合も、DBCOLUMNDESC の *ulColumnSize* メンバーによって、作成される [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の列の幅が決まります。|  
|DBTYPE_XML|**XML**||  
  
> [!NOTE]  
>  新しいテーブルの作成時には、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client OLE DB プロバイダーによって上記の表に示した OLE DB データ型の列挙値のみがマップされます。 それ以外の OLE DB データ型の列が含まれたテーブルを作成すると、エラーが発生します。  
  
## <a name="see-also"></a>参照  
 [データ型 &#40;OLE DB&#41;](../../relational-databases/native-client-ole-db-data-types/data-types-ole-db.md)  
  
  
