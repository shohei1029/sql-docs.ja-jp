---
title: ISSCommandWithParameters (OLE DB ドライバー)
description: ISSCommandWithParameters インターフェイスで OLE DB Driver for SQL Server の SQL Server XML およびユーザー定義型をサポートする方法について説明します。
ms.custom: ''
ms.date: 06/14/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: connectivity
ms.topic: reference
apiname:
- ISSCommandWithParameters (OLE DB)
apitype: COM
helpviewer_keywords:
- ISSCommandWithParameters interface
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: 455387587cc73c3b86b9f94ac84920ed27e127c8
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104753152"
---
# <a name="isscommandwithparameters-ole-db"></a>ISSCommandWithParameters (OLE DB)
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[Driver_OLEDB_Download](../../../includes/driver_oledb_download.md)]

  **ISSCommandWithParameters** インターフェイスでは、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] の XML と UDT (ユーザー定義型) のサポートが公開されます。 これは、主要な OLE DB インターフェイス **ICommandWithParameters** から継承される省略可能なインターフェイスです。 **ISSCommandWithParameters** には、**ICommandWithParameters** から継承される **GetParameterInfo**、**MapParameterNames**、および **SetParameterInfo** という 3 つのメソッドに加えて、サーバー固有のデータ型の処理に使用する 2 つの新しいメソッドが用意されています。  
  
> [!NOTE]  
>  **ISSCommandWithParameters** インターフェイスは、サービス コンポーネントを使用している場合に使用できますが、サービス コンポーネントがこのインターフェイスを使用することはありません。  
  
|方法|説明|  
|------------|-----------------|  
|[ISSCommandWithParameters::GetParameterProperties &#40;OLE DB&#41;](../../oledb/ole-db-interfaces/isscommandwithparameters-getparameterproperties-ole-db.md)|コマンドに渡された各 UDT パラメーターまたは XML パラメーターごとに、1 つの **SSPARAMPROPS** プロパティ セット構造体を返します。他の型のパラメーターの場合、戻り値はありません。|  
|[ISSCommandWithParameters::SetParameterProperties &#40;OLE DB&#41;](../../oledb/ole-db-interfaces/isscommandwithparameters-setparameterproperties-ole-db.md)|序数順に各パラメーターのパラメーター プロパティを設定するか、**SSPARAMPROPS** 構造体の配列を指定して、一括でパラメーター プロパティを設定します。|  
  
## <a name="see-also"></a>参照  
 [インターフェイス &#40;OLE DB&#41;](../../oledb/ole-db-interfaces/oledb-driver-for-sql-server-ole-db-interfaces.md)    
 [XML データ型の使用](../../oledb/features/using-xml-data-types.md)   
 [ユーザー定義型の使用](../../oledb/features/using-user-defined-types.md)  
  
  
