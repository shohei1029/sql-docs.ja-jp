---
description: セッション プロパティ - SQL Server Native Client OLE DB プロバイダー
title: セッションのプロパティ OLE DB
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- sessions [OLE DB]
- SQL Server Native Client OLE DB provider, sessions
ms.assetid: 2498fbad-b3db-4bea-8fc6-fef5317d3eba
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 4c914de9a6dcbc26444266365fce291d917e1370
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104748492"
---
# <a name="session-properties---sql-server-native-client-ole-db-provider"></a>セッション プロパティ - SQL Server Native Client OLE DB プロバイダー
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]Native Client OLE DB プロバイダーは、OLE DB セッションプロパティを次のように解釈します。  
  
|プロパティ ID|説明|  
|-----------------|-----------------|  
|DBPROP_SESS_AUTOCOMMITISOLEVELS|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]Native Client OLE DB プロバイダーは、混乱レベルの DBPROPVAL_TI_CHAOS を除き、すべての自動コミットトランザクション分離レベルをサポートします。|  
|||

 プロバイダー固有のプロパティセット DBPROPSET_SQLSERVERSESSION では、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client OLE DB プロバイダーによって、次の追加のセッションプロパティが定義されます。  
  
|プロパティ ID|説明|  
|-----------------|-----------------|  
|SSPROP_QUOTEDCATALOGNAMES|型 : VT_BOOL<br /><br /> R/W:読み取り/書き込み<br /><br /> 既定値 : VARIANT_FALSE<br /><br /> 説明 : CATALOG 制約で、引用符で囲まれた識別子を許可するかどうかを指定します。<br /><br /> VARIANT_TRUE: 分散クエリをサポートするスキーマ行セットのカタログ制約で、引用符で囲まれた識別子が認識されます。<br /><br /> VARIANT_FALSE: 分散クエリをサポートするスキーマ行セットのカタログ制約で、引用符で囲まれた識別子が認識されません。<br /><br /> 分散クエリをサポートするスキーマ行セットの詳細については、「[スキーマ行セットでの分散クエリのサポート](../../relational-databases/native-client/ole-db/schema-rowsets-distributed-query-support.md)」を参照してください。|  
|SSPROP_ALLOWNATIVEVARIANT|型: VT_BOOL<br /><br /> R/W:読み取り/書き込み<br /><br /> 既定値はVARIANT_FALSE<br /><br /> 説明 : データを DBTYPE_VARIANT と DBTYPE_SQLVARIANT のどちらとしてフェッチするかを決定します。<br /><br /> VARIANT_TRUE: 列の型は DBTYPE_SQLVARIANT として返され、バッファーには SSVARIANT 構造体が保持されます。<br /><br /> VARIANT_FALSE: 列の型は DBTYPE_VARIANT として返され、バッファーには VARIANT 構造体が保持されます。|  
|SSPROP_ASYNCH_BULKCOPY|非同期モードを使用するには、プロバイダー固有のセッション プロパティ SSPROP_ASYNCH_BULKCOPY を VARIANT_TRUE に設定してから、BCPExec メソッドを呼び出します。 このプロパティは、DBPROPSET_SQLSERVERSESSION プロパティ セットに含まれています。|  
|||

## <a name="see-also"></a>参照  
 [データ ソース オブジェクト &#40;OLE DB&#41;](../../relational-databases/native-client-ole-db-data-source-objects/data-source-objects-ole-db.md)  
  
  
