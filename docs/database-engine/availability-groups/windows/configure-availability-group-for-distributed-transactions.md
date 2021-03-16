---
title: 可用性グループ用に分散トランザクションを構成する
description: 'Always On 可用性グループ内のデータベースに対して分散トランザクションを構成する方法について説明します。 '
ms.custom: seodec18
ms.date: 02/06/2019
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: how-to
helpviewer_keywords:
- database mirroring [SQL Server], interoperability
- cross-database transactions [SQL Server]
- transactions [database mirroring]
- Availability Groups [SQL Server], interoperability
- troubleshooting [SQL Server], cross-database transactions
ms.assetid: ''
author: cawrites
ms.author: chadam
ms.openlocfilehash: da3071f14be97a4bbbd9ac909926f210c49504d4
ms.sourcegitcommit: 62c7b972db0ac28e3ae457ce44a4566ebd3bbdee
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2021
ms.locfileid: "103231520"
---
# <a name="configure-distributed-transactions-for-an-always-on-availability-group"></a>Always On 可用性グループ用に分散トランザクションを構成する
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]

[!INCLUDE[SQL2017](../../../includes/sssql17-md.md)] は、可用性グループのデータベースを含むすべての分散トランザクションをサポートします。 この記事では、分散トランザクションの可用性グループを構成する方法について説明します。  

分散トランザクションを保証するには、分散トランザクション リソース マネージャーとしてデータベースを登録するように、可用性グループを構成する必要があります。  

>[!NOTE]
>[!INCLUDE[SQL2016](../../../includes/sssql16-md.md)] Service Pack 2 以降では、可用性グループでの分散トランザクションが完全にサポートされます。 [!INCLUDE[SQL2016](../../../includes/sssql16-md.md)] Service Pack 2 より前のバージョンでは、可用性グループ内のデータベースに関連する複数データベースにまたがる分散トランザクション (つまり、同じ SQL Server インスタンスのデータベースを使用するトランザクション) はサポートされません。 [!INCLUDE[SQL2017](../../../includes/sssql17-md.md)] にはこのような制限はありません。 
>
>[!INCLUDE[SQL2016](../../../includes/sssql16-md.md)] での構成手順は [!INCLUDE[SQL2017](../../../includes/sssql17-md.md)] の場合と同じです。

分散トランザクションでは、クライアント アプリケーションは Microsoft 分散トランザクション コーディネーター (MS DTC または DTC) と連携して、複数のデータ ソース間でトランザクションの整合性を保証します。 DTC は、サポートされている Windows Server ベースのオペレーティング システムで使用可能なサービスです。 分散トランザクションの場合は、DTC が "*トランザクション コーディネーター*" です。 通常は、SQL Server インスタンスが "*リソース マネージャー*" です。 データベースが可用性グループ内にある場合、各データベースがそれ自体のリソース マネージャーである必要があります。 

可用性グループが分散トランザクション用に構成されていない場合であっても、[!INCLUDE[SQLServer](../../../includes/ssnoversion-md.md)] は可用性グループ内のデータベースに対する分散トランザクションを妨げません。 ただし、可用性グループが分散トランザクション対応に構成されていない場合は、一部の状況でフェールオーバーが失敗する可能性があります。 具体的には、新しいプライマリ レプリカの [!INCLUDE[SQLServer](../../../includes/ssnoversion-md.md)] インスタンスが、DTC からトランザクションの結果を取得できない場合があります。 [!INCLUDE[SQLServer](../../../includes/ssnoversion-md.md)] インスタンスがフェールオーバー後に DTC から未確定トランザクションの結果を取得できるようにするには、可用性グループを分散トランザクション対応に構成します。 

データベースがフェールオーバー クラスターのメンバーでもある場合を除き、DTC は可用性グループの処理には関与しません。 可用性グループ内では、レプリカ間の整合性は可用性グループのロジックによって維持されます。プライマリはコミットを完了せず、セカンダリが永続的なストレージにログ レコードを保持していることを確認するまで、呼び出し元に対するコミットを承認します。 その後で初めて、プライマリはトランザクションの完了を宣言します。 非同期モードでは、セカンダリからの肯定応答を待たないため、少量のデータが失われる可能性が明示的に存在します。

## <a name="prerequisites"></a>[前提条件]

分散トランザクションをサポートするように可用性グループを構成するには、次の前提条件が満たされている必要があります。

* 分散トランザクションに参加する [!INCLUDE[SQLServer](../../../includes/ssnoversion-md.md)] のすべてのインスタンスが、[!INCLUDE[SQL2016](../../../includes/sssql16-md.md)] またはそれ以降である必要があります。

* 可用性グループは、Windows Server 2016 または Windows Server 2012 R2 上で実行されている必要があります。 Windows Server 2012 R2 の場合は、[https://support.microsoft.com/kb/3090973](https://support.microsoft.com/kb/3090973) で入手できる KB3090973 の更新プログラムをインストールする必要があります。  

## <a name="create-an-availability-group-for-distributed-transactions"></a>分散トランザクション対応の可用性グループを作成する

分散トランザクションをサポートするように可用性グループを構成します。 各データベースがリソース マネージャーとして登録するのを許可するように、可用性グループを設定します。 この記事では、各データベースが DTC のリソース マネージャーになることができるように、可用性グループを構成する方法について説明します。



[!INCLUDE[SQL2016](../../../includes/sssql16-md.md)] 以降では、分散トランザクション対応の可用性グループを作成できます。 分散トランザクション対応の可用性グループを作成するには、可用性グループの定義に `DTC_SUPPORT = PER_DB` を追加します。 次のスクリプトは、分散トランザクション対応の可用性グループを作成します。 

```sql
CREATE AVAILABILITY GROUP MyAG
   WITH (
      DTC_SUPPORT = PER_DB  
      )
   FOR DATABASE DB1, DB2
   REPLICA ON
      'Server1' WITH (
         ENDPOINT_URL = 'TCP://SERVER1.corp.com:5022',  
         AVAILABILITY_MODE = SYNCHRONOUS_COMMIT,  
         FAILOVER_MODE = AUTOMATIC  
         ),
      'Server2' WITH (
         ENDPOINT_URL = 'TCP://SERVER2.corp.com:5022',  
         AVAILABILITY_MODE = SYNCHRONOUS_COMMIT,  
         FAILOVER_MODE = AUTOMATIC  
         )
```

>[!NOTE]
>上のスクリプトは可用性グループの簡単な例であり、特定の運用環境を想定して設計されてはいません。 

## <a name="alter-an-availability-group-for-distributed-transactions"></a>分散トランザクション対応に可用性グループを変更する

[!INCLUDE[SQL2017](../../../includes/sssql17-md.md)] 以降では、分散トランザクション対応に可用性グループを変更できます。 分散トランザクション対応に可用性グループを変更するには、`ALTER AVAILABILITY GROUP` スクリプトに `DTC_SUPPORT = PER_DB` を追加します。 分散トランザクションをサポートするように可用性グループを変更するスクリプトの例を次に示します。 

```sql
ALTER AVAILABILITY GROUP MyaAG
   SET (
      DTC_SUPPORT = PER_DB  
      );
```

>[!NOTE]
>[!INCLUDE[SQL2016](../../../includes/sssql16-md.md)] Service Pack 2 以降では、分散トランザクションの可用性グループを変更できます。 Service Pack 2 より前の [!INCLUDE[SQL2016](../../../includes/sssql16-md.md)] バージョンの場合、可用性グループを削除し、`DTC_SUPPORT = PER_DB` 設定で作り直す必要があります。 

分散トランザクションを無効にするには、次の Transact-SQL コマンドを使います。

```sql
ALTER AVAILABILITY GROUP MyaAG
   SET (
      DTC_SUPPORT = NONE  
      );
```

## <a name="distributed-transactions---technical-concepts"></a><a name="distTran"/>分散トランザクション - 技術的概念

分散トランザクションとは、2 つ以上のデータベースにまたがるトランザクションです。 トランザクション マネージャーとしての DTC は、SQL Server インスタンス間、および他のデータ ソース間でトランザクションを調整します。 [!INCLUDE[SQLServer](../../../includes/ssnoversion-md.md)] データベース エンジンの各インスタンスは、リソース マネージャーとして動作できます。 可用性グループが `DTC_SUPPORT = PER_DB` で構成されていると、データベースはリソース マネージャーとして動作できます。 詳細については、MS DTC のドキュメントを参照してください。

データベース エンジンの 1 つのインスタンスに複数のデータベースが含まれるトランザクションは、実際には分散トランザクションです。 ただし、SQL Server インスタンスは分散トランザクションを内部で処理するため、ユーザーにはローカル トランザクションとして動作しているように見えます。 データベースが `DTC_SUPPORT = PER_DB` で構成された可用性グループ内にある場合は、たとえ SQL Server の 1 つのインスタンス内に存在する場合であっても、[!INCLUDE[SQL2017](../../../includes/sssql17-md.md)] はすべてのクロスデータベース トランザクションを DTC に送ります。 

アプリケーションでは、分散トランザクションはローカル トランザクションとほぼ同様に管理されます。 トランザクションの終了時に、アプリケーションがトランザクションのコミットまたはロールバックを要求します。 ただし、トランザクション マネージャーが分散コミットを別の方法で管理することによって、ネットワーク障害により一部のリソース マネージャーがトランザクションを正常にコミットし、その一方で他のリソース マネージャーがトランザクションをロールバックするという危険性を最小限に抑える必要があります。 これは、コミット処理を準備フェーズとコミット フェーズの 2 フェーズで管理することによって実現されます。これを 2 フェーズ コミットと呼びます。

- **準備フェーズ**
   
   トランザクション マネージャーはコミット要求を受け取ると、そのトランザクションに関連するすべてのリソース マネージャーに準備コマンドを送ります。 各リソース マネージャーは、トランザクションを持続的にするために必要な処理をすべて実行し、そのトランザクションのログ イメージを含むすべてのバッファーをディスクにフラッシュします。 リソース マネージャーの準備フェーズが完了すると、トランザクション マネージャーに準備の成否が通知されます。

- **コミット フェーズ**
   
   トランザクション マネージャーは、すべてのリソース マネージャーから準備の正常完了通知を受け取ると、リソース マネージャーにコミット コマンドを送ります。 これにより、リソース マネージャーはコミットを完了できます。 すべてのリソース マネージャーがコミットの正常完了を報告した場合、トランザクション マネージャーは、アプリケーションに成功通知を送ります。 準備できなかったことを報告するリソース マネージャーがあった場合、トランザクション マネージャーはすべてのリソース マネージャーにロールバック コマンドを送り、アプリケーションにコミットできなかったことを報告します。

### <a name="detailed-steps"></a>詳細な手順

以下では、アプリケーションが DTC と連携して分散トランザクションを実行する方法について説明します。

1. SQL Server インスタンスは DTC トランザクションに登録します。 これは、トランザクションに複数のリソース マネージャーが存在する場合、またはクライアントがトランザクションを DTC トランザクションに昇格するよう要求している場合に行うことができます。
2. クライアントは、DTC トランザクション下の SQL Server インスタンスで何らかの処理を行います。
3. クライアントは、コミットまたは中止を DTC トランザクションに発行します。
    - クライアントが中止を発行した場合、トランザクションはすぐに中止されます。
    - クライアントがコミットを発行した場合、DTC は、トランザクションのすべてのリソース マネージャーにトランザクションの準備を要求することによって、2 フェーズ コミット プロトコルを開始します。
4. すべてのリソース マネージャーから準備フェーズが正常に行われたことを示す確認応答を受け取った後、DTC はトランザクションをコミットするようすべてのリソース マネージャーに通知します。 何らかの理由で正常な確認応答が妨げられると、DTC はトランザクションを中止します。 

### <a name="effects-of-configuring-an-availability-group-for-distributed-transactions"></a>分散トランザクション対応に可用性グループを変更することの効果

分散トランザクションに参加している各エンティティは、リソース マネージャーと呼ばれます。 リソース マネージャーの例を次に示します。

* [!INCLUDE[SQLServer](../../../includes/ssnoversion-md.md)] のインスタンス。 
* 分散トランザクション対応に構成されている可用性グループのデータベース。
* DTC サービスもトランザクション マネージャーになることができます。
* その他のデータ ソース。 

分散トランザクションに参加するため、[!INCLUDE[SQLServer](../../../includes/ssnoversion-md.md)] のインスタンスは DTC に登録します。 通常、[!INCLUDE[SQLServer](../../../includes/ssnoversion-md.md)] のインスタンスはローカル サーバー上の DTC に登録します。 [!INCLUDE[SQLServer](../../../includes/ssnoversion-md.md)] の各インスタンスは、一意のリソース マネージャー ID (RMID) でリソース マネージャーを作成し、それを DTC に登録します。 既定の構成では、[!INCLUDE[SQLServer](../../../includes/ssnoversion-md.md)] のインスタンスのすべてのデータベースが同じ RMID を使います。 

データベースが可用性グループ内にある場合、データベースの読み取り/書き込みコピー (プライマリ レプリカ) は、[!INCLUDE[SQLServer](../../../includes/ssnoversion-md.md)] の異なるインスタンスに移動できます。 この移動中に分散トランザクションをサポートするためには、各データベースが独立したリソース マネージャーとして機能し、一意の RMID を持っている必要があります。 可用性グループで `DTC_SUPPORT = PER_DB` が指定されていると、[!INCLUDE[SQLServer](../../../includes/ssnoversion-md.md)] はデータベースごとにリソース マネージャーを作成し、一意の RMID を使って DTC に登録します。 この構成では、データベースは DTC トランザクションのリソース マネージャーになります。

>[!IMPORTANT]
>DTC では、分散トランザクションあたりの登録が 32 に制限されていることに注意してください。 可用性グループ内の各データベースでは、DTC に個別に登録されるため、トランザクションに 32 を超えるデータベースが含まれている場合は、[!INCLUDE[SQLServer](../../../includes/ssnoversion-md.md)] が 33 番目のデータベースを登録しようとすると、次のエラーが発生することがあります。
>
>`Enlist operation failed: 0x8004d101(XACT_E_TOOMANY_ENLISTMENTS). SQL Server could not register with Microsoft Distributed Transaction Coordinator (MS DTC) as a resource manager for this transaction. The transaction may have been stopped by the client or the resource manager.`

[!INCLUDE[SQLServer](../../../includes/ssnoversion-md.md)] での分散トランザクションについて詳しくは、「[分散トランザクション](#distTran)」をご覧ください。

## <a name="manage-unresolved-transactions"></a>未解決のトランザクションを管理する

RMID の変更中に存在しているアクティブなトランザクションの結果は、フェールオーバー後に復旧できません。 これは、登録に使われた RMID SQL Server と、復旧に使われる RMID SQL Server が異なるためです。 RMID の変更は、次の場合に発生する可能性があります。

* 可用性グループの `DTC_SUPPORT` を変更する。 
* 可用性グループのデータベースを追加または削除する。 
* 可用性グループを削除する。

これらの場合、プライマリ レプリカが [!INCLUDE[SQLServer](../../../includes/ssnoversion-md.md)] の新しいインスタンスにフェールオーバーすると、インスタンスは DTC に接続してトランザクションの結果を取得しようとします。 データベースが復旧中に未確定トランザクションの結果を取得するために使う RMID が以前に登録されていないため、DTC は結果を返すことができません。 そのため、データベースは SUSPECT 状態になります。

[!INCLUDE[SQLServer](../../../includes/ssnoversion-md.md)] の新しいエラー ログには、次の例のようなエントリが含まれます。

```
Microsoft Distributed Transaction Coordinator (MS DTC) 
failed to reenlist citing that the database RMID does 
not match the RMID [xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx] 
associated with the transaction.  Please manually resolve
the transaction.
    
SQL Server detected a DTC/KTM in-doubt transaction with UOW 
{yyyyyyyy-yyyy-yyyy-yyyy-yyyyyyyyyyyy}.Please resolve it 
following the guideline for Troubleshooting DTC Transactions.
```

この例は、DTC がフェールオーバー後に作成されたトランザクションの新しいプライマリ レプリカからデータベースを再登録できなかったことを示します。 [!INCLUDE[SQLServer](../../../includes/ssnoversion-md.md)] のインスタンスは分散トランザクションの結果を判断できないので、データベースを SUSPECT としてマークします。 トランザクションは作業単位 (UOW) としてマークされ、GUID によって参照されます。 データベースを復旧するには、トランザクションを手動でコミットまたはロールバックします。 

>[!WARNING]
>トランザクションを手動でコミットまたはロールバックすると、アプリケーションに影響する可能性があります。 コミットまたはロールバックのアクションが、アプリケーションの要件と整合していることを確認してください。 

次のスクリプトのいずれか 1 つのみを実行します。

   * トランザクションをコミットするには、次のスクリプトを更新して実行します。`yyyyyyyy-yyyy-yyyy-yyyy-yyyyyyyyyyyy` を以前のエラー メッセージに含まれる未確定トランザクションの UOW に置き換えて実行します。

   ```sql
   KILL 'yyyyyyyy-yyyy-yyyy-yyyy-yyyyyyyyyyyy' WITH COMMIT
   ```

   * トランザクションをロールバックするには、次のスクリプトを更新して実行します。`yyyyyyyy-yyyy-yyyy-yyyy-yyyyyyyyyyyy` を以前のエラー メッセージに含まれる未確定トランザクションの UOW に置き換えて実行します。

   ```sql
   KILL 'yyyyyyyy-yyyy-yyyy-yyyy-yyyyyyyyyyyy' WITH ROLLBACK
   ```

トランザクションをコミットまたはロールバックした後は、`ALTER DATABASE` を使ってデータベースをオンラインに設定できます。 次のスクリプトを更新して実行します。データベース名には、問題のあるデータベースの名前を設定します。

   ```sql
   ALTER DATABASE [DB1] SET ONLINE
   ```

未確定トランザクションの解決について詳しくは、「[トランザクションを手動で解決する](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754134(v=ws.10))」をご覧ください。

## <a name="next-steps"></a>次の手順  

[分散トランザクション](/dotnet/framework/data/adonet/distributed-transactions)

[Always On 可用性グループ:相互運用性 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/always-on-availability-groups-interoperability-sql-server.md)  
  
[トランザクション - Always On 可用性グループとデータベース ミラーリング](transactions-always-on-availability-and-database-mirroring.md)  

[XA トランザクションのサポート](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753563(v=ws.10))

[動作のしくみ: DTC トランザクションのセッション/SPID (-2)](/archive/blogs/bobsql/how-it-works-sessionspid-2-for-dtc-transactions)
