---
title: SQL Server のデータベース ミラーリング
description: データベース ミラーリングについて、開発中の ADO.NET アプリケーションでのフェールオーバー イベントなどの考慮事項について説明します。
ms.date: 09/30/2019
dev_langs:
- csharp
ms.assetid: 89befaff-bb46-4290-8382-e67cdb0e3de9
ms.prod: sql
ms.prod_service: connectivity
ms.technology: connectivity
ms.topic: conceptual
author: David-Engel
ms.author: v-daenge
ms.reviewer: v-kaywon
ms.openlocfilehash: afa10c2c657ba2f67d6a3521768a364e89f7268b
ms.sourcegitcommit: 524a0f0cc9533188f4b14d2e78ba1cfe816b3b9a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2021
ms.locfileid: "105633231"
---
# <a name="database-mirroring-in-sql-server"></a>SQL Server のデータベース ミラーリング

[!INCLUDE[Driver_ADONET_Download](../../../includes/driver_adonet_download.md)]

SQL Server のデータベース ミラーリング機能を使用すると、スタンバイ サーバー上に SQL Server データベースのコピー、つまりミラーを保持できます。 ミラーリングを使用すると、データの 2 つの個別のコピーを常に存在させることによって、高可用性と完全なデータ冗長性を確実に実現できます。 Microsoft SqlClient Provider for SQL Server では、データベース ミラーリングが暗黙的にサポートされています。 クライアントが SQL Server データベース用に構成されている場合、開発者は何もする必要はありません。 また、<xref:Microsoft.Data.SqlClient.SqlConnection> オブジェクトでは明示的な接続モードもサポートされています。この場合は、<xref:Microsoft.Data.SqlClient.SqlConnection.ConnectionString%2A> でフェールオーバー パートナー サーバーの名前を指定できます。

簡略化された次の一連のイベントは、ミラーリング用に構成されたデータベースをターゲットにする <xref:Microsoft.Data.SqlClient.SqlConnection> オブジェクトに対して発生します。

1. クライアント アプリケーションをプリンシパル データベースに正常に接続すると、サーバーからパートナー サーバーの名前が送り返され、クライアントでそれがキャッシュされます。
2. プリンシパル データベースを含むサーバーに障害が発生、または接続が中断された場合、接続およびトランザクション状態は失われます。 クライアント アプリケーションがプリンシパル データベースへの接続を再確立しようとして失敗します。
3. 次に、クライアント アプリケーションがパートナー サーバー上のミラー データベースへの接続を透過的に確立しようとします。 成功した場合は、接続がミラー データベースにリダイレクトされ、それが新しいプリンシパル データベースになります。

## <a name="specifying-the-failover-partner-in-the-connection-string"></a>接続文字列でのフェールオーバー パートナーの指定

接続文字列でフェールオーバー パートナー サーバーの名前を指定すると、クライアント アプリケーションの接続時にプリンシパル データベースが使用できない場合、クライアントがそのフェールオーバー パートナーとの接続を透過的に試行します。

```csharp
";Failover Partner=PartnerServerName"
```

フェールオーバー パートナー サーバーの名前を省略すると、クライアント アプリケーションの最初の接続時にプリンシパル データベースが使用できない場合、<xref:Microsoft.Data.SqlClient.SqlException> が生成されます。

<xref:Microsoft.Data.SqlClient.SqlConnection> が正常に開かれた場合は、サーバーからフェールオーバー パートナー名が返され、接続文字列で指定されたすべての値と置き換わります。

> [!NOTE]
> データベース ミラーリングのシナリオでは、接続文字列で初期カタログまたはデータベース名を明示的に指定する必要があります。 クライアントが、初期カタログやデータベースが明示的に指定されていない接続に関するフェールオーバー情報を受信する場合は、このフェールオーバー情報はキャッシュされず、アプリケーションはプリンシパル サーバーでの障害発生時にフェールオーバーを試行しません。 接続文字列にフェールオーバー パートナーの値が含まれていても、初期カタログまたはデータベースの値が存在しない場合は、`InvalidArgumentException` が生成されます。

## <a name="retrieving-the-current-server-name"></a>現在のサーバー名の取得

フェールオーバーが発生した場合は、<xref:Microsoft.Data.SqlClient.SqlConnection> オブジェクトの <xref:Microsoft.Data.SqlClient.SqlConnection.DataSource%2A> プロパティを使用して、現在の接続が接続されているサーバーの名前を取得できます。 次のコード フラグメントでは、接続変数が開いている <xref:Microsoft.Data.SqlClient.SqlConnection> を参照していることを前提として、アクティブなサーバーの名前を取得します。

フェールオーバー イベントが発生し、接続がミラー サーバーに切り替えられた場合は、**DataSource** プロパティがそのミラー名を反映するように更新されます。

```csharp
string activeServer = connection.DataSource;
```

## <a name="sqlclient-mirroring-behavior"></a>SqlClient ミラーリングの動作

クライアントからは、常にプリンシパル サーバーへの接続が試みられます。 それが失敗すると、フェールオーバー パートナーを試します。 ミラー データベースが既にパートナー サーバー上のプリンシパル ロールに切り替えられている場合、接続は成功し、新しいプリンシパル/ミラーのマッピングがクライアントに送信され、<xref:System.AppDomain> の呼び出しが有効な間はキャッシュされます。 このマッピングは永続ストレージには格納されないため、別の **AppDomain** 内またはプロセス内で今後の接続に使用することはできません。 ただし、同一の **AppDomain** 内では、後での接続に使用できます。 同一または別のコンピューター上で実行されている、別の **AppDomain** またはプロセスには常に接続のプールがあり、これらの接続はリセットされません。 このとき、プライマリ データベースがダウンし、各プロセスまたは **AppDomain** が失敗した場合、プールは自動的に消去されます。

> [!NOTE]
> サーバー上のミラーリングのサポートは、データベースごとに構成されます。 マルチパート名を使用するか、または現在のデータベースを変更し、プリンシパル/ミラー セットに含まれていない他のデータベースに対してデータ操作が行われた場合、障害が発生したときは、これらの他のデータベースへの変更は伝播されません。 ミラー化されていないデータベースでデータが変更されても、エラーは生成されません。 開発者は、このような操作で考えられる影響を評価する必要があります。

## <a name="next-steps"></a>次のステップ

### <a name="database-mirroring-resources"></a>データベース ミラーリングのリソース

ミラーリングの構成、配置、管理の概念に関するドキュメントや情報については、SQL Server ドキュメントの次のリソースを参照してください。

|リソース|説明|
|--------------|-----------------|
|[データベース ミラーリング](../../../database-engine/database-mirroring/database-mirroring-sql-server.md)|SQL Server でのミラーリングの設定と構成方法について説明します。|
