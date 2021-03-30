---
title: SQL Server Service Broker | Microsoft Docs
description: Service Broker について説明します。 これにより、どのように SQL Server データベース エンジンおよび Azure SQL Managed Instance でのメッセージングがネイティブにサポートされるかを確認します。
ms.custom: ''
ms.date: 03/17/2021
ms.prod: sql
ms.prod_service: high-availability
ms.reviewer: ''
ms.technology: configuration
ms.topic: conceptual
f1_keywords:
- SQL13.SWB.SSBMSGTYPEPROPERTIES.GENERAL.F1
- SQL13.SWB.SSBCONTRACTPROPERTIES.GENERAL.F1
- SQL13.SWB.SSBQUEUEPROPERTIES.GENERAL.F1
- SQL13.SWB.SSBREMSVCBINDPROPERTIES.GENERAL.F1
- SQL13.SWB.SSBROUTEPROPERTIES.GENERAL.F1
- SQL13.SWB.SSBPRIORITYPROPERTIES.GENERAL.F1
- SQL13.SWB.SSBSERVICEPROPERTIES.GENERAL.F1
helpviewer_keywords:
- Broker See Service Broker
- SQL Server Service Broker
- Service Broker
ms.assetid: 8b8b3b57-fd46-44de-9a4e-e3a8e3999c1e
author: markingmyname
ms.author: maghan
monikerRange: =azuresqldb-mi-current||>=sql-server-2016||>=sql-server-linux-2017
ms.openlocfilehash: 0eab287319fdfa263cbf5cba44546ab3b792bdad
ms.sourcegitcommit: 00af0b6448ba58e3685530f40bc622453d3545ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104673902"
---
# <a name="service-broker"></a>Service Broker
[!INCLUDE [SQL Server SQL MI](../../includes/applies-to-version/sql-asdbmi.md)]

  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] [!INCLUDE[ssSB](../../includes/sssb-md.md)] では、[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] および [Azure SQL Database Managed Instance](/azure/sql-database/sql-database-managed-instance-index) でのメッセージングとキューのネイティブ サポートが提供されています。 開発者は、[!INCLUDE[ssDE](../../includes/ssde-md.md)] コンポーネントを使用して異種データベース間の通信を行う高度なアプリケーションを簡単に作成し、分散型で信頼できるアプリケーションをビルドすることができます。  
  
## <a name="when-to-use-service-broker"></a>Service Broker を使用する場合

 Service Broker コンポーネントを使用して、ネイティブのデータベース内非同期メッセージ処理機能を実装します。 アプリケーション開発者は、 [!INCLUDE[ssSB](../../includes/sssb-md.md)] を使用すれば、通信やメッセージングの複雑な内部のプログラミングを行わなくても、データ ワークロードを複数のデータベースに分散できます。 [!INCLUDE[ssSB](../../includes/sssb-md.md)] によってメッセージ交換のコンテキスト内で通信パスが処理されるので、Service Broker によって開発やテストの作業が軽減されます。 また、パフォーマンスも向上します。 たとえば、Web サイトをサポートするフロントエンド データベースで情報の記録を行い、処理負荷の高いタスクはバックエンド データベースのキューに送信できます。 [!INCLUDE[ssSB](../../includes/sssb-md.md)] では、すべてのタスクがトランザクションのコンテキストで管理されるため、信頼性と技術的な一貫性を確保できます。  
  
## <a name="overview"></a>概要

  Service Broker は、ネイティブのデータベース内サービス指向アプリケーションを作成することができるメッセージ配信フレームワークです。 テーブルから常にデータを読み取り、クエリのライフサイクル中に処理する従来のクエリ処理機能とは異なり、サービス指向アプリケーションには、メッセージを交換するデータベース サービスが備えられています。 すべてのサービスには、メッセージが処理されるまで配置されるキューがあります。
  
![Service Broker](media/service-broker.png)
  
  Transact-SQL `RECEIVE` コマンドを使用するか、またはメッセージがキューに到着するたびに呼び出されるアクティブ化プロシージャによって、キュー内のメッセージをフェッチすることができます。
  
### <a name="creating-services"></a>サービスの作成
 
  データベース サービスは、[CREATE SERVICE](../../t-sql/statements/create-service-transact-sql.md) Transact-SQL ステートメントを使用して作成されます。 サービスは、[CREATE QUEUE](../../t-sql/statements/create-queue-transact-sql.md) ステートメントを使用して作成されたメッセージ キューに関連付けることができます。
  
```sql
CREATE QUEUE dbo.ExpenseQueue;
GO
CREATE SERVICE ExpensesService
    ON QUEUE dbo.ExpenseQueue; 
```

### <a name="sending-messages"></a>メッセージを送信する
  
  メッセージは、[SEND](../../t-sql/statements/send-transact-sql.md) Transact-SQL ステートメントを使用してサービス間のメッセージ交換で送信されます。 メッセージ交換は、`BEGIN DIALOG` Transact-SQL ステートメントを使用してサービス間で確立される通信チャネルです。 
  
```sql
DECLARE @dialog_handle UNIQUEIDENTIFIER;

BEGIN DIALOG @dialog_handle  
FROM SERVICE ExpensesClient  
TO SERVICE 'ExpensesService';  
  
SEND ON CONVERSATION @dialog_handle (@Message) ;  
```
   メッセージは `ExpenssesService` に送信され、`dbo.ExpenseQueue` に配置されます。 このキューに関連付けられているアクティブ化プロシージャがないため、メッセージは、誰かが読み取るまでキュー内に留まります。

### <a name="processing-messages"></a>メッセージの処理

   キューに配置されているメッセージは、標準の `SELECT` クエリを使用して選択できます。 `SELECT` ステートメントでは、キューが変更されたりメッセージが削除されたりしません。 キューからメッセージを読み取ってプルするには、[RECEIVE](../../t-sql/statements/receive-transact-sql.md) Transact-SQL ステートメントを使用します。

```sql
RECEIVE conversation_handle, message_type_name, message_body  
FROM ExpenseQueue; 
```

  キューからのすべてのメッセージを処理したら、[END CONVERSATION](../../t-sql/statements/end-conversation-transact-sql.md) Transact-SQL ステートメントを使用してメッセージ交換を終了する必要があります。

## <a name="where-is-the-documentation-for-service-broker"></a>Service Broker のドキュメントの格納場所  
 [!INCLUDE[ssSB](../../includes/sssb-md.md)] のリファレンス ドキュメントは [!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] のドキュメントに含まれています。 リファレンス ドキュメントには次のセクションがあります。  
  
-   CREATE、ALTER、および DROP ステートメントの[データ定義言語 &#40;DDL&#41; ステートメント &#40;Transact-SQL&#41;](../../t-sql/statements/statements.md)  
  
-   [Service Broker のステートメント](../../t-sql/statements/statements.md)  
  
-   [Service Broker カタログ ビュー &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/service-broker-catalog-views-transact-sql.md)  
  
-   [Service Broker 関連の動的管理ビュー &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/service-broker-related-dynamic-management-views-transact-sql.md)  
  
-   [ssbdiagnose ユーティリティ &#40;Service Broker&#41;](../../tools/ssbdiagnose/ssbdiagnose-utility-service-broker.md)  
  
 [の概念、開発作業、および管理作業については、](/previous-versions/sql/sql-server-2008-r2/bb522893(v=sql.105)) 以前に公開されたドキュメント [!INCLUDE[ssSB](../../includes/sssb-md.md)] を参照してください。 このドキュメントは、[!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] の最新バージョンの [!INCLUDE[ssSB](../../includes/sssb-md.md)] ではわずかにしか変更されていないため、[!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] 用のドキュメントは作成されていません。  
  
## <a name="whats-new-in-service-broker"></a>Service Broker の新機能  

### <a name="service-broker-and-azure-sql-managed-instance"></a>Service Broker と Azure SQL Managed Instance

クロス インスタンス Service Broker メッセージ交換は、Azure SQL Managed Instance 間でのみサポートされます。

- `CREATE ROUTE`: LOCAL 以外の ADDRESS、または別の SQL Managed Instance の DNS 名を指定して CREATE ROUTE を使用することはできません。 指定するポートは 4022 である必要があります。 [CREATE ROUTE](https://docs.microsoft.com/sql/t-sql/statements/create-route-transact-sql) をご覧ください。
- `ALTER ROUTE`: LOCAL 以外の ADDRESS、または別の SQL Managed Instance の DNS 名を指定して ALTER ROUTE を使用することはできません。 指定するポートは 4022 である必要があります。 [ALTER ROUTE](https://docs.microsoft.com/sql/t-sql/statements/alter-route-transact-sql) に関する記事をご覧ください。

トランスポート セキュリティはサポートされていますが、ダイアログ セキュリティはサポートされていません。

- `CREATE REMOTE SERVICE BINDING` がサポートされていません。

Service Broker は既定で有効になっており、無効にできません。 次の ALTER DATABASE オプションはサポートされていません。

- `ENABLE_BROKER`
- `DISABLE_BROKER`

[!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] で導入された大きな変更はありません。  [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)]では、以下の変更が導入されました。 

### <a name="messages-can-be-sent-to-multiple-target-services-multicast"></a>メッセージを複数の対象サービスに送信可能 (マルチキャスト)  
 [SEND &#40;Transact-SQL&#41;](../../t-sql/statements/send-transact-sql.md) ステートメントの構文が拡張され、複数のメッセージ交換ハンドルをサポートすることにより、マルチキャストが有効になりました。  
  
### <a name="queues-expose-the-message-enqueued-time"></a>メッセージがエンキューされている時間の公開  
 メッセージがキューに格納されている時間を示す新しい列 **message_enqueue_time** がキューに追加されました。  
  
### <a name="poison-message-handling-can-be-disabled"></a>有害メッセージの処理の無効化が可能  
 [CREATE QUEUE &#40;Transact-SQL&#41;](../../t-sql/statements/create-queue-transact-sql.md) ステートメントおよび [ALTER QUEUE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-queue-transact-sql.md) ステートメントでは、`POISON_MESSAGE_HANDLING (STATUS = ON | OFF)` 句を追加することによって有害メッセージの処理を無効化できるようになりました。 カタログ ビュー **sys.service_queues** に、有害メッセージの処理が有効であるか無効であるかを示す **is_poison_message_handling_enabled** 列が追加されました。  
  
### <a name="always-on-support-in-service-broker"></a>Service Broker での Always On のサポート  
 詳細については、「 [Service Broker と Always On 可用性グループ (SQL Server)](../../database-engine/availability-groups/windows/service-broker-with-always-on-availability-groups-sql-server.md)」を参照してください。  
  
  
## <a name="next-steps"></a>次のステップ

Service Broker の最も一般的な用途は、[イベント通知](../../relational-databases/service-broker/event-notifications.md)に使用することです。 [イベント通知を実装する](../../relational-databases/service-broker/implement-event-notifications.md)方法、[ダイアログ セキュリティを構成する](../../relational-databases/service-broker/configure-dialog-security-for-event-notifications.md)方法、[詳細情報](../../relational-databases/service-broker/get-information-about-event-notifications.md)を確認してください。
