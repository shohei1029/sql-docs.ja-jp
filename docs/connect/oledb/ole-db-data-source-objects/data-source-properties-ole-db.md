---
title: データ ソースのプロパティ (OLE DB ドライバー) | Microsoft Docs
description: OLE DB Driver for SQL Server でのデータ ソース プロパティの実装方法について説明します。これには、追加のデータ ソース プロパティを持つプロバイダー固有のプロパティ セットが含まれます。
ms.custom: ''
ms.date: 06/14/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: connectivity
ms.topic: reference
helpviewer_keywords:
- OLE DB Driver for SQL Server, data source properties
- properties [OLE DB]
- data source properties [OLE DB]
- OLE DB data source properties [OLE DB Driver for SQL Server]
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: 392f6de131f6db7e9a956787e180409770beff0e
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104753662"
---
# <a name="data-source-properties-ole-db"></a>データ ソースのプロパティ (OLE DB)
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[Driver_OLEDB_Download](../../../includes/driver_oledb_download.md)]

  OLE DB Driver for SQL Server では、データ ソースのプロパティが次のように実装されます。  
  
|プロパティ ID|説明|  
|-----------------|-----------------|  
|DBPROP_CURRENTCATALOG|R/W: 読み取り/書き込み&lt;br&gt;&lt;/br&gt;既定値 : なし<br /><br /> 説明 : DBPROP_CURRENTCATALOG の値により、OLE DB Driver for SQL Server セッションの現在のデータベースが報告されます。 このプロパティ値を設定することは、[!INCLUDE[tsql](../../../includes/tsql-md.md)] USE *database* ステートメントを使用して現在のデータベースを設定するのと同じ効果があります。<br /><br /> [!INCLUDE[ssVersion2005](../../../includes/ssversion2005-md.md)] 以降のバージョンでは、データベース名を小文字で指定して [sp_defaultdb](../../../relational-databases/system-stored-procedures/sp-defaultdb-transact-sql.md) を呼び出すと、大文字と小文字が混在する名前を使用してデータベースが作成されている場合でも、DBPROP_CURRENTCATALOG では名前が小文字で返されます。 以前のバージョンの [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] では、大文字と小文字が混在する名前が DBPROP_CURRENTCATALOG によって返されます。|  
|DBPROP_MULTIPLECONNECTIONS|R/W : 読み取り/書き込み&lt;br&gt;&lt;/br&gt;既定値 : VARIANT_FALSE<br /><br /> 説明 : 行セットを生成しないコマンドまたはサーバー カーソルではない行セットを生成するコマンドが接続で実行されているときに、ユーザーが別のコマンドを実行すると、DBPROP_MULTIPLECONNECTIONS が VARIANT_TRUE に設定されている場合は、新しいコマンドを実行するために新しい接続が作成されます。<br /><br /> OLE DB Driver for SQL Server では、DBPROP_MULTIPLECONNECTION が VARIANT_FALSE に設定されている場合、または接続でトランザクションがアクティブな場合は、別の接続は作成されません。 DBPROP_MULTIPLECONNECTIONS が VARIANT_FALSE に設定されている場合は OLE DB Driver for SQL Server から DB_E_OBJECTOPEN が返され、アクティブなトランザクションがある場合は E_FAIL が返されます。 トランザクションとロックは、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] によって接続ごとに管理されます。 2 番目の接続を作成する場合は、個別の接続のコマンドではロックは共有されません。 あるコマンドで別のコマンドがブロックされないようにするには、他のコマンドによって要求される行にロックを設定します。 このことは、複数のセッションを作成する場合にも適用されます。<br /><br /> 各セッションには個別の接続があります。|  
  
 プロバイダー固有のプロパティ セット DBPROPSET_SQLSERVERDATASOURCE には、OLE DB Driver for SQL Server により、次の追加のデータ ソース プロパティが定義されます。  
  
|プロパティ ID|説明|  
|-----------------|-----------------|  
|SSPROP_ENABLEFASTLOAD|R/W : 読み取り/書き込み&lt;br&gt;&lt;/br&gt;既定値 : VARIANT_FALSE<br /><br /> 説明 : メモリからの一括コピーを有効にするには、SSPROP_ENABLEFASTLOAD プロパティを VARIANT_TRUE に設定する必要があります。 このプロパティをデータ ソースで設定すると、新しく作成されたセッションを使用して、コンシューマーから [IRowsetFastLoad](../../oledb/ole-db-interfaces/irowsetfastload-ole-db.md) インターフェイスにアクセスできます。<br /><br /> このプロパティを VARIANT_TRUE に設定している場合、**IID_IRowsetFastLoad** インターフェイスを要求するか、**SSPROP_IRowsetFastLoad** を VARIANT_TRUE に設定すると、**IOpenRowset::OpenRowset** を介して **IRowsetFastLoad** インターフェイスが使用可能になります。|  
|SSPROP_ENABLEBULKCOPY|R/W : 読み取り/書き込み&lt;br&gt;&lt;/br&gt;既定値 : VARIANT_FALSE<br /><br /> 説明 : ファイルからの一括コピーを有効にするには、SSPROP_ENABLEBULKCOPY プロパティを VARIANT_TRUE に設定する必要があります。 データ ソースにこのプロパティを設定すると、コンシューマーから IBCPSession インターフェイスにセッションと同じレベルでアクセスできるようになります。<br /><br /> また、SSPROP_IRowsetFastLoad を VARIANT_TRUE に設定する必要があります。|  
  
## <a name="see-also"></a>参照  
 [データ ソース オブジェクト &#40;OLE DB&#41;](../../oledb/ole-db-data-source-objects/data-source-objects-ole-db.md)  
  
  
