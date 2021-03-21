---
title: 複数行セットの結果を生成するコマンド (OLE DB ドライバー) | Microsoft Docs
description: バッチ処理された SQL ステートメントの複数の行セットを OLE DB Driver for SQL Server が返す方法、およびバッチ処理された SQL ステートメントがストアド プロシージャによって実装される場合について説明します。
ms.custom: ''
ms.date: 06/14/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: connectivity
ms.topic: reference
helpviewer_keywords:
- multiple rowsets
- rowsets [OLE DB], multiple
- OLE DB Driver for SQL Server, commands
- OLE DB Driver for SQL Server, multiple rowsets
- commands [OLE DB]
- multiple-rowset results
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: 1389e2728ac52e6b61ddbd360cadfb99e473e146
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104737803"
---
# <a name="commands-generating-multiple-rowset-results"></a>複数行セットの結果を生成するコマンド
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[Driver_OLEDB_Download](../../../includes/driver_oledb_download.md)]

  OLE DB Driver for SQL Server では、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] のステートメントから複数の行セットを返すことができます。 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] のステートメントは、次の条件が満たされた場合に複数行セットの結果を返します。  
  
-   バッチにまとめられた SQL ステートメントが 1 つのコマンドとして実行される場合。  
  
-   ストアド プロシージャが SQL ステートメントのバッチを実装している場合。  
  
## <a name="batches"></a>バッチ  
 OLE DB Driver for SQL Server では、セミコロンを SQL ステートメントのバッチ区切り記号として認識します。  
  
```  
WCHAR*       wSQLString = L"SELECT * FROM Categories; "  
                          L"SELECT * FROM Products";  
```  
  
 複数の SQL ステートメントを 1 つのバッチにまとめて送信する方が、各 SQL ステートメントを個別に実行するよりも効率的です。 1 つのバッチを送信することで、クライアントからサーバーへのネットワーク ラウンド トリップが減少するためです。  
  
## <a name="stored-procedures"></a>ストアド プロシージャ  
 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] は、ストアド プロシージャ内のステートメントごとに結果セットを返します。このため、大半の [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] ストアド プロシージャは複数の結果セットを返します。  
  
## <a name="in-this-section"></a>このセクションの内容  
  
-   [IMultipleResults を使用した複数の結果セットの処理](../../oledb/ole-db-commands/using-imultipleresults-to-process-multiple-result-sets.md)  
  
## <a name="see-also"></a>参照  
 [コマンド](../../oledb/ole-db-commands/commands.md)  
  
  
