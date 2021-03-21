---
title: セッションのプロパティ - OLE DB Driver for SQL Server | Microsoft Docs
description: OLE DB Driver for SQL Server でプロバイダー固有のプロパティ セットを含む OLE DB セッション プロパティがどのように解釈されるかについて説明します。
ms.custom: ''
ms.date: 06/14/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: connectivity
ms.topic: reference
helpviewer_keywords:
- sessions [OLE DB]
- OLE DB Driver for SQL Server, sessions
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: 326b0d09fbcf071d981db56440956393eda93e73
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104755632"
---
# <a name="session-properties---ole-db-driver-for-sql-server"></a>セッションのプロパティ - OLE DB Driver for SQL Server
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[Driver_OLEDB_Download](../../../includes/driver_oledb_download.md)]

  OLE DB Driver for SQL Server では OLE DB セッション プロパティが次のように解釈されます。  
  
|プロパティ ID|説明|  
|-----------------|-----------------|  
|DBPROP_SESS_AUTOCOMMITISOLEVELS|OLE DB Driver for SQL Server では、カオス レベル DBPROPVAL_TI_CHAOS を除くすべての自動コミット トランザクション分離レベルがサポートされます。|  
  
 プロバイダー固有のプロパティ セット DBPROPSET_SQLSERVERSESSION には、OLE DB Driver for SQL Server によって、次の追加のセッション プロパティが定義されます。  
  
|プロパティ ID|説明|  
|-----------------|-----------------|  
|SSPROP_QUOTEDCATALOGNAMES|型 : VT_BOOL<br /><br /> R/W:読み取り/書き込み<br /><br /> 既定値 : VARIANT_FALSE<br /><br /> 説明 : CATALOG 制約で、引用符で囲まれた識別子を許可するかどうかを指定します。<br /><br /> VARIANT_TRUE: 分散クエリをサポートするスキーマ行セットのカタログ制約で、引用符で囲まれた識別子が認識されます。<br /><br /> VARIANT_FALSE: 分散クエリをサポートするスキーマ行セットのカタログ制約で、引用符で囲まれた識別子が認識されません。<br /><br /> 分散クエリをサポートするスキーマ行セットの詳細については、「[スキーマ行セットでの分散クエリのサポート](../../oledb/ole-db/schema-rowsets-distributed-query-support.md)」を参照してください。|  
|SSPROP_ALLOWNATIVEVARIANT|型: VT_BOOL<br /><br /> R/W:読み取り/書き込み<br /><br /> 既定値はVARIANT_FALSE<br /><br /> 説明 : データを DBTYPE_VARIANT と DBTYPE_SQLVARIANT のどちらとしてフェッチするかを決定します。<br /><br /> VARIANT_TRUE: 列の型は DBTYPE_SQLVARIANT として返され、バッファーには SSVARIANT 構造体が保持されます。<br /><br /> VARIANT_FALSE: 列の型は DBTYPE_VARIANT として返され、バッファーには VARIANT 構造体が保持されます。|  
|SSPROP_ASYNCH_BULKCOPY|非同期モードを使用するには、プロバイダー固有のセッション プロパティ SSPROP_ASYNCH_BULKCOPY を VARIANT_TRUE に設定してから、BCPExec メソッドを呼び出します。 このプロパティは、DBPROPSET_SQLSERVERSESSION プロパティ セットに含まれています。|  
  
## <a name="see-also"></a>参照  
 [データ ソース オブジェクト &#40;OLE DB&#41;](../../oledb/ole-db-data-source-objects/data-source-objects-ole-db.md)  
  
  
