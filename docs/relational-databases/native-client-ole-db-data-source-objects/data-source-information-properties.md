---
description: データソース情報のプロパティ (Native Client OLE DB プロバイダー)
title: データソース情報のプロパティ (Native Client OLE DB provider) |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- SQL Server Native Client OLE DB provider, data source properties
- properties [OLE DB]
- data source properties [OLE DB]
- information properties [OLE DB]
- OLE DB data source properties [SQL Server Native Client]
ms.assetid: 7fd80e47-5bd9-41e2-a3d3-091a7c8c5f2b
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 8adc170ab669bafb77b859aca3b36dbc93fae1f2
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104749052"
---
#  <a name="sql-server-native-client-data-source-information-properties"></a>データソース情報のプロパティの SQL Server Native Client
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  プロバイダー固有のプロパティ セット DBPROPSET_SQLSERVERDATASOURCEINFO には、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client OLE DB プロバイダーにより、次のデータ ソース情報プロパティが定義されます。  
  
|プロパティ ID|説明|  
|-----------------|-----------------|  
|SSPROP_COLUMNLEVELCOLLATION|型 : VT_BOOL<br /><br /> R/W: 読み取り<br /><br /> 既定値 : VARIANT_TRUE<br /><br /> 説明 : 列の照合順序がサポートされるかどうかの判断に使用されます。<br /><br /> VARIANT_TRUE: 列レベルの照合順序がサポートされます。<br /><br /> VARIANT_FALSE: 列レベルの照合順序はサポートされません。|  
|SSPROP_UNICODELCID|型 : VT_I4 R/W: 読み取り<br /><br /> 説明 : Unicode ロケール ID です。<br /><br /> これは、Unicode データの並べ替えに使われるロケールです。|  
|SSPROP_UNICODECOMPARISONSTYLE|型 : VT_I4 R/W: 読み取り<br /><br /> 説明 : Unicode 比較形式です。<br /><br /> Unicode データの並べ替えに使われる並べ替えオプションです。|  
  
 プロバイダー固有のプロパティ セット DBPROPSET_SQLSERVERSTREAM には、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client OLE DB プロバイダーにより、次の追加プロパティが定義されます。  
  
|プロパティ ID|説明|  
|-----------------|-----------------|  
|SSPROP_STREAM_XMLROOT|型 : VT_BSTR R/W: 読み取り/書き込み<br /><br /> 説明: FOR XML クエリの結果に、整形式でないドキュメントを許可します。 このプロパティを指定すると、'select ... for XML' クエリの結果はこのプロパティが提供するルート タグにラップされて、整形式の XML ドキュメントが返されます。 クエリがブラウザーから実行されている場合、結果の読み込み時にブラウザーがパーサー エラーを表示する場合があります。 このエラーを回避するために、SQL ISAPI はキーワード ROOT をサポートしています。 このキーワードは SSPROP_STREAM_XMLROOT プロパティにマップされます。|  
  
## <a name="see-also"></a>参照  
 [データ ソース オブジェクト &#40;OLE DB&#41;](../../relational-databases/native-client-ole-db-data-source-objects/data-source-objects-ole-db.md)  
  
  
