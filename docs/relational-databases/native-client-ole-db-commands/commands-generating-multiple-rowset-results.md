---
description: 複数行セットの結果を生成するコマンド (Native Client OLE DB プロバイダー)
title: 複数行セットの結果を生成するコマンド (Native Client OLE DB provider) |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- multiple rowsets
- rowsets [OLE DB], multiple
- SQL Server Native Client OLE DB provider, commands
- SQL Server Native Client OLE DB provider, multiple rowsets
- commands [OLE DB]
- multiple-rowset results
ms.assetid: 4567668d-35fd-4162-b61f-f7536862cdcb
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 0cff167a88007d52d19ed89400eebe60497351de
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104737274"
---
# <a name="sql-server-native-client-commands-generating-multiple-rowset-results"></a>Multiple-Rowset 結果を生成するコマンドの SQL Server Native Client
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]Native Client OLE DB プロバイダーは、ステートメントから複数の行セットを返すことができ [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のステートメントは、次の条件が満たされた場合に複数行セットの結果を返します。  
  
-   バッチにまとめられた SQL ステートメントが 1 つのコマンドとして実行される場合。  
  
-   ストアド プロシージャが SQL ステートメントのバッチを実装している場合。  
  
## <a name="batches"></a>バッチ  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]Native Client OLE DB プロバイダーは、SQL ステートメントのバッチ区切り記号としてセミコロン文字を認識します。  
  
```  
WCHAR*       wSQLString = L"SELECT * FROM Categories; "  
                          L"SELECT * FROM Products";  
```  
  
 複数の SQL ステートメントを 1 つのバッチにまとめて送信する方が、各 SQL ステートメントを個別に実行するよりも効率的です。 1 つのバッチを送信することで、クライアントからサーバーへのネットワーク ラウンド トリップが減少するためです。  
  
## <a name="stored-procedures"></a>ストアド プロシージャ  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] は、ストアド プロシージャ内のステートメントごとに結果セットを返します。このため、大半の [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ストアド プロシージャは複数の結果セットを返します。  
  
## <a name="in-this-section"></a>このセクションの内容  
  
-   [IMultipleResults を使用した複数の結果セットの処理](../../relational-databases/native-client-ole-db-commands/using-imultipleresults-to-process-multiple-result-sets.md)  
  
## <a name="see-also"></a>参照  
 [コマンド](../../relational-databases/native-client-ole-db-commands/commands.md)  
  
  
