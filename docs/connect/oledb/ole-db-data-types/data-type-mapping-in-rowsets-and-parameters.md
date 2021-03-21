---
title: 行セットとパラメーターでのデータ型マッピング (OLE DB ドライバー) | Microsoft Docs
description: OLE DB Driver for SQL Server で、OLE DB で定義されるデータ型を使用して行セット内またはパラメーター値の SQL Server データがどのように表されるかについて説明します。
ms.custom: ''
ms.date: 02/21/2020
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: connectivity
ms.topic: reference
helpviewer_keywords:
- mapping data types [OLE DB]
- DBTYPE_SQLVARIANT data type
- OLE DB Driver for SQL Server, data types
- rowsets [OLE DB], data type mapping
- data types [OLE DB]
- GetColumnInfo function
- parameters [OLE DB]
- SSPROP_ALLOWNATIVEVARIANT property
- GetParameterInfo function
- OLE DB, data types
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: 466e379b2ce809f72fb39c4d7594a85ce85ebad5
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104749162"
---
# <a name="data-type-mapping-in-rowsets-and-parameters"></a>行セットとパラメーターでのデータ型マッピング
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[Driver_OLEDB_Download](../../../includes/driver_oledb_download.md)]

  OLE DB Driver for SQL Server では、OLE DB で定義される次のデータ型を使用して、行セット内やパラメーター値として、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] データを表します。これらのデータ型は、関数 **IColumnsInfo::GetColumnInfo** と **ICommandWithParameters::GetParameterInfo** で報告されます。  
  
|SQL Server のデータ型|OLE DB データ型|  
|--------------------------|----------------------|  
|**bigint**|DBTYPE_I8|  
|**[バイナリ]**|DBTYPE_BYTES|  
|**bit**|DBTYPE_BOOL|  
|**char**|DBTYPE_STR|  
|**datetime**|DBTYPE_DBTIMESTAMP|  
|**datetime2**|DBTYPE_DBTIMESTAMP|  
|**decimal**|DBTYPE_NUMERIC|  
|**float**|DBTYPE_R8|  
|**画像**|DBTYPE_BYTES|  
|**int**|DBTYPE_I4|  
|**money**|DBTYPE_CY|  
|**nchar**|DBTYPE_WSTR|  
|**ntext**|DBTYPE_WSTR|  
|**numeric**|DBTYPE_NUMERIC|  
|**nvarchar**|DBTYPE_WSTR|  
|**real**|DBTYPE_R4|  
|**smalldatetime**|DBTYPE_DBTIMESTAMP|  
|**smallint**|DBTYPE_I2|  
|**smallmoney**|DBTYPE_CY|  
|**sql_variant**|DBTYPE_VARIANT、DBTYPE_SQLVARIANT|  
|**sysname**|DBTYPE_WSTR|  
|**text**|DBTYPE_STR|  
|**timestamp**|DBTYPE_BYTES|  
|**tinyint**|DBTYPE_UI1|  
|**UDT**|DBTYPE_UDT|  
|**uniqueidentifier**|DBTYPE_GUID|  
|**varbinary**|DBTYPE_BYTES|  
|**varchar**|DBTYPE_STR|  
|**XML**|DBTYPE_XML|  
  
 OLE DB Driver for SQL Server では、次の図に示すように、コンシューマーが要求するデータ変換がサポートされています。  
  
 **sql_variant** オブジェクトは、text、ntext、image、varchar(max)、nvarchar(max)、varbinary(max)、xml、timestamp、および Microsoft .NET Framework 共通言語ランタイム (CLR) のユーザー定義型を除く、任意の [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] データ型のデータを保持できます。 また、sql_variant データのインスタンスは、その基になる基本データ型として sql_variant を保持できません。 たとえば、一部の行の列に **smallint** 型の値を格納し、他の行の列に **float** 型の値を格納して、残りの行の列に **char**/**nchar** 型の値を格納できます。  
  
> [!NOTE]  
>  **sql_variant** データ型は、Microsoft Visual Basic® の Variant データ型や、OLE DB の DBTYPE_VARIANT および DBTYPE_SQLVARIANT に似ています。  
  
 **sql_variant** 型のデータを DBTYPE_VARIANT としてフェッチすると、このデータはバッファーの VARIANT 構造体内に格納されます。 ただし、VARIANT 構造体内のサブタイプは、**sql_variant** データ型で定義されているサブタイプにマップされない場合があります。 このため、すべてのサブタイプを一致させるには、**sql_variant** 型のデータを DBTYPE_SQLVARIANT としてフェッチする必要があります。  
  
## <a name="dbtype_sqlvariant-data-type"></a>DBTYPE_SQLVARIANT データ型  
 OLE DB Driver for SQL Server では、**sql_variant** データ型をサポートするために、DBTYPE_SQLVARIANT と呼ばれるプロバイダー固有のデータ型を公開しています。 **sql_variant** 型のデータを DBTYPE_SQLVARIANT としてフェッチすると、データはプロバイダー固有の SSVARIANT 構造体内に格納されます。 SSVARIANT 構造体には、**sql_variant** データ型のサブタイプに一致するすべてのサブタイプが含まれています。  
  
 また、セッション プロパティ SSPROP_ALLOWNATIVEVARIANT を TRUE に設定する必要もあります。  
  
## <a name="provider-specific-property-ssprop_allownativevariant"></a>プロバイダー固有のプロパティ SSPROP_ALLOWNATIVEVARIANT  
 データをフェッチするときに、列またはパラメーターに返すデータ型を明示的に指定できます。 また、**IColumnsInfo** を使用して列情報を取得し、その情報をバインドに使用することもできます。 **IColumnsInfo** を使用してバインド用の列情報を取得するときに、SSPROP_ALLOWNATIVEVARIANT セッション プロパティが FALSE (既定値) の場合、**sql_variant** 型の列には DBTYPE_VARIANT が返されます。 SSPROP_ALLOWNATIVEVARIANT プロパティが FALSE の場合、DBTYPE_SQLVARIANT はサポートされません。 SSPROP_ALLOWNATIVEVARIANT プロパティを TRUE に設定すると、列の型は DBTYPE_SQLVARIANT として返されます。この場合、バッファーには SSVARIANT 構造体が保持されます。 **sql_variant** 型のデータを DBTYPE_SQLVARIANT としてフェッチするときは、セッション プロパティ SSPROP_ALLOWNATIVEVARIANT が TRUE に設定されている必要があります。  
  
 SSPROP_ALLOWNATIVEVARIANT プロパティはプロバイダー固有の DBPROPSET_SQLSERVERSESSION プロパティ セットの一部であり、セッション プロパティです。  
  
 DBTYPE_VARIANT は、他のすべての OLE DB プロバイダーに適用されます。  
  
## <a name="ssprop_allownativevariant"></a>SSPROP_ALLOWNATIVEVARIANT  
 SSPROP_ALLOWNATIVEVARIANT はセッション プロパティで、DBPROPSET_SQLSERVERSESSION プロパティ セットの一部です。  
  
|プロパティ|説明|  
|-|-|  
|SSPROP_ALLOWNATIVEVARIANT|型: VT_BOOL<br /><br /> R/W:読み取り/書き込み<br /><br /> 既定値はVARIANT_FALSE<br /><br /> 説明 : データを DBTYPE_VARIANT と DBTYPE_SQLVARIANT のどちらとしてフェッチするかを決定します。<br /><br /> VARIANT_TRUE: 列の型は DBTYPE_SQLVARIANT として返され、バッファーには SSVARIANT 構造体が保持されます。<br /><br /> VARIANT_FALSE:列の型は DBTYPE_VARIANT として返され、バッファーには VARIANT 構造体が保持されます。|  
  
## <a name="see-also"></a>参照  
 [データ型 &#40;OLE DB&#41;](../../oledb/ole-db-data-types/data-types-ole-db.md)  
  
  
