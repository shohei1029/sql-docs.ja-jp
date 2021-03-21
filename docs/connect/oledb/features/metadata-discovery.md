---
title: メタデータの検出 | Microsoft Docs
description: メタデータ検出の機能強化により、OLE DB Driver for SQL Server のアプリケーションでメタデータの互換性をどのように確保できるかについて説明します。
ms.custom: ''
ms.date: 06/12/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: connectivity
ms.topic: reference
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: d0b4a7d53f857f34f2d7fea85e0213145efd1699
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104742462"
---
# <a name="metadata-discovery"></a>メタデータの検出
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[Driver_OLEDB_Download](../../../includes/driver_oledb_download.md)]

  [!INCLUDE[ssSQL11](../../../includes/sssql11-md.md)] ではメタデータ検出機能が強化されているため、OLE DB Driver for SQL Server アプリケーションでは、クエリの実行によって返された列またはパラメーター メタデータがクエリの実行前に指定したメタデータ形式と同じであるか、その形式と互換性があることを確認できます。 クエリの実行後に返されたメタデータにクエリの実行前に指定したメタデータ形式との互換性がない場合は、エラーが発生します。  
  
 bcp 関数と IBCPSession および IBCPSession2 インターフェイスでは、遅延読み取り (遅延メタデータ検出) を指定して、クエリ出力操作でメタデータ検出を回避できます。 その結果、パフォーマンスが向上し、メタデータ検出のエラーを回避できます。  
  
 OLE DB Driver for SQL Server を使用してアプリケーションを開発し、[!INCLUDE[ssSQL11](../../../includes/sssql11-md.md)] より前のバージョンのサーバーに接続する場合、メタデータ検出機能はそのバージョンのサーバーに対応したものとなります。  
  
## <a name="remarks"></a>解説   
 [!INCLUDE[ssSQL11](../../../includes/sssql11-md.md)] では次の OLE DB メンバー関数が機能強化され、メタデータ検出機能が向上しています。  
  
-   IColumnsInfo::GetColumnInfo  
  
-   IColumnsRowset::GetColumnsRowset  
  
-   ICommandWithParameters::GetParameterInfo (詳細は「[ICommandWithParameters](../../oledb/ole-db-interfaces/icommandwithparameters.md)」を参照)  
  
 IBCPSession::BCPSetBulkMode を使用してメタデータ形式を指定したときのパフォーマンスも向上しています。  
  
 OLE DB Driver for SQL Server でのメタデータ検出機能の強化は、[!INCLUDE[ssSQL11](../../../includes/sssql11-md.md)] への次の 2 つのストアド プロシージャの追加により実現されました。  
  
-   sp_describe_first_result_set  
  
-   sp_describe_undeclared_parameters  
  
## <a name="see-also"></a>参照  
 [OLE DB Driver for SQL Server の機能](../../oledb/features/oledb-driver-for-sql-server-features.md)  
  
  
