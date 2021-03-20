---
title: 行セット (Native Client OLE DB プロバイダー)
description: すべてのデータプロバイダーが結果セットのデータを表形式で公開できるようにします。 OLE DB では、データ列を含む行のセットである行セット機能を使用します。
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- rowsets [OLE DB], about rowsets
- SQL Server Native Client OLE DB provider, rowsets
- OLE DB rowsets
- OLE DB rowsets, about rowsets
- rowsets [OLE DB]
ms.assetid: 5e7b3cbe-3670-4e18-8172-2226e0b6b142
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 7b149038a7129b2327156cc28967520f6386d1b6
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104755882"
---
# <a name="rowsets-native-client-ole-db-provider"></a>行セット (Native Client OLE DB プロバイダー)
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  行セットとは、データの列を含む行の集まりです。 行セットは、すべての OLE DB データ プロバイダーが表形式で結果セット データを公開できるようにするための重要な機能を持つオブジェクトです。  
  
 コンシューマーでは **IDBCreateSession::CreateSession** メソッドを使用してセッションを作成した後、そのセッションで **IOpenRowset** インターフェイスまたは **IDBCreateCommand** インターフェイスのいずれかを使用して行セットを作成できます。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]Native Client OLE DB プロバイダーは、これらのインターフェイスの両方をサポートしています。 ここでは、これら両方のメソッドについて説明します。  
  
-   **IOpenRowset::OpenRowset** メソッドを呼び出して行セットを作成します。  
  
     この操作は、1 つのテーブル全体について行セットを作成することと同じです。 このメソッドは、1 つのベース テーブルのすべての行を含む行セットを開き、その行セットを返します。 **OpenRowset** の引数の 1 つは、行セットの作成元となるテーブルを識別するテーブル ID です。  
  
-   **IDBCreateCommand::CreateCommand** メソッドを呼び出して、コマンド オブジェクトを作成します。  
  
     コマンド オブジェクトでは、プロバイダーがサポートするコマンドが実行されます。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client OLE DB プロバイダーでは、SELECT ステートメントなどの任意の [!INCLUDE[tsql](../../includes/tsql-md.md)] ステートメントやストアド プロシージャへの呼び出しをコンシューマーで指定できます。 コマンド オブジェクトを使用して行セットを作成する手順は次のとおりです。  
  
    1.  コンシューマーは、セッションの **IDBCreateCommand::CreateCommand** メソッドを呼び出してコマンド オブジェクトを取得し、そのコマンド オブジェクトの **ICommandText** インターフェイスを要求します。 この **ICommandText** インターフェイスで、実際のコマンド テキストが設定および取得されます。 コンシューマーは、**ICommandText::SetCommandText** メソッドを呼び出して、テキスト コマンドにコマンドを設定します。  
  
    2.  ユーザーがコマンドの **ICommand::Execute** メソッドを呼び出します。 コマンドの実行時に構築される行セット オブジェクトには、コマンドから返される結果セットが含まれます。  
  
 コンシューマーは、**ICommandProperties** インターフェイスを使用して、**ICommand::Execute** インターフェイスで実行されるコマンドから返される行セットのプロパティを取得または設定できます。 最も一般的に要求されるプロパティは、行セットでサポートする必要のあるインターフェイスです。 コンシューマーはインターフェイスだけでなく、行セットやインターフェイスの動作を変更するプロパティも要求できます。  
  
 コンシューマーは **IRowset::Release** メソッドを使用して行セットを解放します。 行セットを解放すると、コンシューマーがその行セットに保持しているすべての行ハンドルも解放されます。 ただし、行セットを解放してもアクセサーは解放されません。 **IAccessor** インターフェイスがある場合は、それを解放する必要があります。  
  
## <a name="in-this-section"></a>このセクションの内容  
  
-   [IOpenRowset を使用した行セットの作成](../../relational-databases/native-client-ole-db-rowsets/creating-a-rowset-with-iopenrowset.md)  
  
-   [ICommand::Execute を使用した行セットの作成](../../relational-databases/native-client-ole-db-rowsets/creating-rowsets-with-icommand-execute.md)  
  
-   [行セットのプロパティと動作](../../relational-databases/native-client-ole-db-rowsets/rowset-properties-and-behaviors.md)  
  
-   [行セットと SQL Server カーソル](../../relational-databases/native-client-ole-db-rowsets/rowsets-and-sql-server-cursors.md)  
  
-   [行のフェッチ](../../relational-databases/native-client-ole-db-rowsets/fetching-rows.md)  
  
-   [IRow による 1 行のフェッチ](../../relational-databases/native-client-ole-db-rowsets/fetching-a-single-row-with-irow.md)  
  
-   [ブックマーク](../../relational-databases/native-client-ole-db-rowsets/bookmarks.md)  
  
-   [行セット内のデータの更新](../../relational-databases/native-client-ole-db-rowsets/updating-data-in-rowsets.md)  
  
## <a name="see-also"></a>参照  
 [SQL Server Native Client &#40;OLE DB&#41;](../../relational-databases/native-client/ole-db/sql-server-native-client-ole-db.md)  
  
  
