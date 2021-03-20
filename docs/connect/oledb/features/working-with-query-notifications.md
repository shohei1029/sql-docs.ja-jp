---
title: クエリ通知の操作 | Microsoft Docs
description: OLE DB Driver for SQL Server でデータが変更されたときにクエリ通知を使用してアプリケーションに通知する方法について説明します。
ms.custom: ''
ms.date: 06/12/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: connectivity
ms.topic: reference
helpviewer_keywords:
- data access [OLE DB Driver for SQL Server], query notifications
- rowsets [SQL Server], notifications
- OLE DB Driver for SQL Server, query notifications
- notifications [OLE DB Driver for SQL Server]
- query notifications [SQL Server], OLE DB Driver for SQL Server
- canceling rowset changes
- IRowsetNotify interface
- MSOLEDBSQL, query notifications
- OLE DB Driver for SQL Server, query notifications
- consumer notification for rowset changes [OLE DB Driver for SQL Server]
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: 3b21f3a8b17c5174c4e9f8b037aa891bdfcc6d10
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104751072"
---
# <a name="working-with-query-notifications"></a>クエリ通知の操作

[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]

[!INCLUDE[Driver_OLEDB_Download](../../../includes/driver_oledb_download.md)]

クエリ通知は、[!INCLUDE[ssVersion2005](../../../includes/ssversion2005-md.md)] と OLE DB Driver for SQL Server で導入されました。 クエリ通知は SQL Server 2005 (9.x) に導入された SQL Service Broker インフラストラクチャに基づいて構築されており、データが変更されたときにアプリケーションに通知できます。 Web アプリケーションのように、データベースから情報のキャッシュを提供し、ソース データが変更されたときに通知を受ける必要があるアプリケーションでは、この機能が特に有用です。

クエリ通知を使用することで、クエリの基になるデータが変更された場合に、指定されたタイムアウト期間内に通知を要求することができます。 要求する際は、サービス名、メッセージ テキスト、サーバーのタイムアウト値などの通知オプションを指定します。 通知は Service Broker キューを使用して配信されます。アプリケーションではこのキューにポーリングして、使用可能な通知を確認できます。

クエリ通知オプションの構文を次に示します。

`service=<service-name>[;(local database=<database> | broker instance=<broker instance>)]`

 次に例を示します。

`service=mySSBService;local database=mydb`

通知サブスクリプションは、それを開始したプロセスより長い有効期間を持っています。 そのため、アプリケーションは、通知サブスクリプションを作成してから、終了することができます。 サブスクリプションが有効なままなので、サブスクリプションの作成時に指定したタイムアウト期間内にデータが変更されると、通知が行われます。 通知は、実行されるクエリ、通知オプション、およびメッセージ テキストによって識別されます。 そのタイムアウト値を 0 に設定することでキャンセルできます。

通知は、一度だけ送信されます。 データ変更の通知を継続して受け取るには、各通知が処理された後にクエリを再実行して、新しいサブスクリプションを作成します。

OLE DB Driver for SQL Server アプリケーションでは、通常、[!INCLUDE[tsql](../../../includes/tsql-md.md)] の [RECEIVE](../../../t-sql/statements/receive-transact-sql.md) コマンドを使用して通知を受け取ります。 このコマンドを使用して、通知オプションで指定されているサービスに関連付けられたキューから通知を読み取ります。

> [!NOTE]
> 通知を必要とするクエリ内のテーブル名は、修飾された名前にする必要があります。 たとえば、「 `dbo.myTable` 」のように入力します。 テーブル名は、2 つの部分を持つ修飾名にする必要があります。 3 つまたは 4 つの部分を持つ名前を使用すると、サブスクリプションが無効になります。

この通知インフラストラクチャは、[!INCLUDE[ssVersion2005](../../../includes/ssversion2005-md.md)] で導入されたキュー処理機能に基づいて構築されています。 一般的に、サーバーで生成された通知は、後で処理するためにキュー経由で送信されます。

クエリ通知を使用するには、クエリとサービスがサーバー上に存在する必要があります。 これらは、次のような [!INCLUDE[tsql](../../../includes/tsql-md.md)] コマンドを使用して作成できます。

```sql
CREATE QUEUE myQueue
CREATE SERVICE myService ON QUEUE myQueue
([https://schemas.microsoft.com/SQL/Notifications/PostQueryNotification])
```

> [!NOTE]
> 上記のように、サービスでは定義済みのコントラクトを使用する必要があります。

## <a name="ole-db-driver-for-sql-server"></a>OLE DB Driver for SQL Server

OLE DB Driver for SQL Server では、行セットが変更されたときのコンシューマー通知がサポートされています。 コンシューマーは、行セットの変更のすべてのフェーズと、変更が試行されたときに通知を受け取ります。

> [!NOTE]
> OLE DB Driver for SQL Server を使用してクエリ通知をサブスクライブする場合、唯一の有効な方法は、**ICommand::Execute** を使用してサーバーに通知クエリを渡す方法です。

### <a name="dbpropset_sqlserverrowset-property-set"></a>DBPROPSET_SQLSERVERROWSET プロパティ セット

OLE DB によるクエリ通知をサポートするために、次の新しいプロパティが OLE DB Driver for SQL Server の `DBPROPSET_SQLSERVERROWSET` プロパティ セットに追加されました。

|Name|Type|説明|
|----------|----------|-----------------|
|SSPROP_QP_NOTIFICATION_TIMEOUT|VT_UI4|クエリ通知をアクティブのままにしておく秒数。<br /><br /> 既定値は 432,000 秒 (5 日) です。 最小値は 1 秒であり、最大値は 2^31-1 秒です。|
|SSPROP_QP_NOTIFICATION_MSGTEXT|VT_BSTR|通知のメッセージ テキスト。 このテキストはユーザーが定義するため、定義済みの書式はありません。<br /><br /> 既定では、文字列は空です。 1 から 2000 文字を使用してメッセージを指定します。|
|SSPROP_QP_NOTIFICATION_OPTIONS|VT_BSTR|クエリ通知オプション。 これらは *name*=*value* 構文を使用した文字列で指定されます。 ユーザーがサービスを作成して、キューから通知を読み取る必要があります。<br /><br /> 既定値は空の文字列です。|

通知サブスクリプションは常にコミットされます。 これは、ステートメントがユーザー トランザクションで実行されたか自動コミットで実行されたか、また、ステートメントが実行されたトランザクションがコミットされたかロールバックされたかに関係なく行われます。 サーバー通知は、次の無効通知条件のいずれかが最初に発生したときに起動します。通知条件は、基になるデータまたはスキーマが変更されるか、タイムアウト期間に到達するかです。 

通知登録は、起動直後に削除されます。 したがって、通知を受け取った後も引き続き更新するには、アプリケーションで再度サブスクライブする必要があります。

他の接続またはスレッドは、接続先キューに通知があるかどうかを確認できます。 次に例を示します。

```sql
WAITFOR (RECEIVE * FROM MyQueue); -- Where MyQueue is the queue name.
```

> [!NOTE]
> `SELECT *` では、キューからエントリが削除されることはありません。 一方、`RECEIVE * FROM` では行われます。 このため、キューが空の場合は、サーバー スレッドが保留されます。 呼び出しの時点でキューにエントリがある場合は、直ちに返されます。 それ以外の場合、呼び出しはキューにエントリが作成されるまで待機します。

```sql
RECEIVE * FROM MyQueue
```

キューが空の場合、このステートメントでは空の結果セットが直ちに返されます。 それ以外の場合は、すべてのキュー通知が返されます。

`SSPROP_QP_NOTIFICATION_MSGTEXT` と `SSPROP_QP_NOTIFICATION_OPTIONS` が null でも空でもない場合は、上で定義されている 3 つのプロパティが含まれるクエリ通知 TDS ヘッダーがサーバーに送信されます。 これは、コマンドが実行されるたびに行われます。 それらのいずれかが null (または空) の場合、ヘッダーは送信されず、`DB_E_ERRORSOCCURRED` が発生します (または、プロパティが両方ともオプションとしてマークされている場合は、`DB_S_ERRORSOCCURRED` が発生します)。 その後、状態の値は `DBPROPSTATUS_BADVALUE` に設定されます。 実行と準備の時点で検証が行われます。 同様に、クエリ通知プロパティが [!INCLUDE[ssVersion2005](../../../includes/ssversion2005-md.md)] より前のバージョンの [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] への接続に対して設定されている場合は、`DB_S_ERRORSOCCURED` が発生します。 この場合、状態の値は `DBPROPSTATUS_NOTSUPPORTED` です。

サブスクリプションが開始されても、後続のメッセージが正常に配信されるかどうかは保証されません。 指定されたサーバー名の妥当性に関するチェックも行われません。

> [!NOTE]
> ステートメントを準備しても、サブスクリプションが開始されることはありません。 ステートメントを実行したときにのみ開始されます。 OLE DB コア サービスを使用しても、クエリ通知は影響を受けません。

`DBPROPSET_SQLSERVERROWSET` プロパティ セットについて詳しくは、「[行セットのプロパティと動作](../../oledb/ole-db-rowsets/rowset-properties-and-behaviors.md)」をご覧ください。

## <a name="see-also"></a>参照

[OLE DB Driver for SQL Server の機能](../../oledb/features/oledb-driver-for-sql-server-features.md)
