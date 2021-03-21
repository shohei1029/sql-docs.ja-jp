---
title: コマンドの準備 (OLE DB ドライバー)
description: 1 つのコマンドが複数回実行される場合、OLE DB Driver for SQL Server ではパフォーマンスを向上させるためにコマンドの準備がサポートされています。
ms.custom: ''
ms.date: 06/14/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: connectivity
ms.topic: reference
helpviewer_keywords:
- OLE DB Driver for SQL Server, commands
- prepared statements [OLE DB Driver for SQL Server]
- commands [OLE DB]
- command preparation [OLE DB Driver for SQL Server]
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: 6b682c54374bed8645ff45f7f4fadc59b9d0f828
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104735508"
---
# <a name="preparing-commands"></a>コマンドの準備
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[Driver_OLEDB_Download](../../../includes/driver_oledb_download.md)]

  OLE DB Driver for SQL Server では、1 つのコマンドを最適化された状態で複数回実行できるように、コマンドを準備できます。ただし、コマンドを準備することでオーバーヘッドが生じるので、コンシューマーではコマンドを複数回実行する場合は準備する必要はありません。 一般的には、コマンドを 4 回以上実行する場合に準備します。  
  
 パフォーマンス上の理由から、コマンドの準備は、コマンドが実行されるまで遅延されます。 これは既定の動作です。 準備中のコマンドのエラーは、コマンドまたはメタプロパティ操作が実行されるまで認識されません。 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] の SSPROP_DEFERPREPARE プロパティを FALSE に設定すると、この既定の動作を無効にできます。  
  
 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] では、コマンドを (最初に準備しないで) 直接実行すると、実行プランが作成され、キャッシュされます。 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] には新しいステートメントをキャッシュ内の既存の実行プランと照合する効率的なアルゴリズムがあるので、SQL ステートメントを再実行すると、そのステートメントの実行プランが再利用されます。  
  
 準備されたコマンドに対して、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] ではコマンド ステートメントの準備と実行に関するネイティブ サポートが提供されます。 ステートメントを準備すると、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] では実行プランが作成されてキャッシュされ、この実行プランのハンドルがプロバイダーに返されます。 その後、プロバイダーはこのハンドルを使用して、ステートメントを繰り返し実行します。 ストアド プロシージャは作成されません。 直接実行する場合のように SQL ステートメントをキャッシュ内の実行プランと照合するのではなく、ハンドルで SQL ステートメントの実行プランを直接識別するので、そのステートメントを複数回実行することがわかっている場合は、ステートメントを準備する方が直接実行するよりも効率的です。  
  
 [!INCLUDE[ssVersion2005](../../../includes/ssversion2005-md.md)] では、準備されたステートメントを一時オブジェクトの作成に使用できません。また、準備されたステートメントから、一時テーブルなどの一時オブジェクトを作成するシステム ストアド プロシージャを参照できません。 このようなプロシージャは、直接実行する必要があります。  
  
 コマンドによっては、準備してはいけないものがあります。 たとえば、ストアド プロシージャの実行を指定するコマンドや、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] ストアド プロシージャの作成に関して無効なテキストを含むコマンドは、準備しないようにしてください。  
  
 OLE DB Driver for SQL Server では、一時ストアド プロシージャが作成されると、その一時ストアド プロシージャを実行して、そのステートメント自体が実行されたかのように結果を返します。  
  
 一時ストアド プロシージャの作成は、OLE DB Driver for SQL Server 固有の初期化プロパティである SSPROP_INIT_USEPROCFORPREP によって制御されます。 プロパティ値が SSPROPVAL_USEPROCFORPREP_ON または SSPROPVAL_USEPROCFORPREP_ON_DROP の場合、コマンドが準備されると、OLE DB Driver for SQL Server は、ストアド プロシージャの作成を試みます。 ストアド プロシージャの作成は、アプリケーション ユーザーが適切な [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 権限を所持している場合に成功します。  
  
 ほとんど切断しないコンシューマーの場合、一時ストアド プロシージャの作成には、**tempdb** の大量のリソースが必要になることがあります。tempdb は、一時オブジェクトが作成される [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] システム データベースです。 SSPROP_INIT_USEPROCFORPREP の値が SSPROPVAL_USEPROCFORPREP_ ON の場合、コマンドを作成したセッションで [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] のインスタンスへの接続が失われたときにだけ、OLE DB Driver for SQL Server で作成された一時ストアド プロシージャが削除されます。 その接続がデータ ソースの初期化時に作成された既定の接続の場合は、データ ソースの初期化が解除されたときのみ、一時ストアド プロシージャが削除されます。  
  
 SSPROP_INIT_USEPROCFORPREP の値が SSPROPVAL_USEPROCFORPREP_ON_DROP の場合、次のいずれかの時点で、OLE DB Driver for SQL Server の一時ストアド プロシージャが削除されます。  
  
-   コンシューマーが **ICommandText::SetCommandText** を使用して新しいコマンドを指定したとき。  
  
-   コンシューマーが **ICommandPrepare::Unprepare** を使用して、コマンド テキストが必要なくなったことを指定したとき。  
  
-   コンシューマーが一時ストアド プロシージャを使用して、コマンド オブジェクトへのすべての参照を解放したとき。  
  
 コマンド オブジェクトは、最大で 1 つだけ一時ストアド プロシージャを **tempdb** に保持します。 既存の一時ストアド プロシージャは、特定のコマンド オブジェクトに関する現在のコマンド テキストを表します。  
  
## <a name="see-also"></a>参照  
 [コマンド](../../oledb/ole-db-commands/commands.md)  
  
  
