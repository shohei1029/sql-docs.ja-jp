---
description: ISSCommandWithParameters (Native Client OLE DB provider)
title: ISSCommandWithParameters (Native Client OLE DB provider)
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
apiname:
- ISSCommandWithParameters (OLE DB)
apitype: COM
helpviewer_keywords:
- ISSCommandWithParameters interface
ms.assetid: 3419b7f3-36a3-4863-816e-e641e4e90496
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: af46c20c873feb845773aa2a4349771689d8e575
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104750132"
---
# <a name="isscommandwithparameters-native-client-ole-db-provider"></a>ISSCommandWithParameters (Native Client OLE DB provider)
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  **Isscommandwithparameters** は [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 、XML およびユーザー定義型 (UDT) のサポートを公開します。 これは、コア OLE DB インターフェイス **ICommandWithParameters** から継承する省略可能なインターフェイスです。 **ICommandWithParameters** から継承された3つのメソッドに加えて、**Getparameterinfo**、 **Mapparameternames**、および **setparameterinfo**;**Isscommandwithparameters** には、サーバー固有のデータ型を処理するために使用される2つの新しいメソッドが用意されています。  
  
> [!NOTE]  
>  **Isscommandwithparameters** インターフェイスは、サービスコンポーネントが使用されている場合に使用できますが、サービスコンポーネント自体はこのインターフェイスを使用しません。  
  
|メソッド|説明|  
|------------|-----------------|  
|[ISSCommandWithParameters::GetParameterProperties &#40;OLE DB&#41;](../../relational-databases/native-client-ole-db-interfaces/isscommandwithparameters-getparameterproperties-ole-db.md)|コマンドに渡された各 UDT パラメーターまたは XML パラメーターごとに、1 つの **SSPARAMPROPS** プロパティ セット構造体を返します。他の型のパラメーターの場合、戻り値はありません。|  
|[ISSCommandWithParameters::SetParameterProperties &#40;OLE DB&#41;](../../relational-databases/native-client-ole-db-interfaces/isscommandwithparameters-setparameterproperties-ole-db.md)|序数順に各パラメーターのパラメーター プロパティを設定するか、**SSPARAMPROPS** 構造体の配列を指定して、一括でパラメーター プロパティを設定します。|  
  
## <a name="see-also"></a>参照  
 [インターフェイス &#40;OLE DB&#41;](./sql-server-native-client-ole-db-interfaces.md)   
 [XML データ型の使用](../../relational-databases/native-client/features/using-xml-data-types.md)   
 [ユーザー定義型の使用](../../relational-databases/native-client/features/using-user-defined-types.md)  
  
