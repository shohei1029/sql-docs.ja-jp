---
title: データベース エンジンのアップグレード計画の策定およびテスト | Microsoft Docs
description: この記事では、SQL Server のアップグレードを開始する前に立てる必要のある計画について説明します。これには、計画チェックリスト、アップグレード計画の作成とテストが含まれます。
ms.custom: ''
ms.date: 05/18/2018
ms.prod: sql
ms.technology: install
ms.reviewer: ''
ms.topic: conceptual
ms.assetid: 19c5b725-7400-4881-af8f-fd232ca28234
author: cawrites
ms.author: chadam
monikerRange: '>=sql-server-2016'
ms.openlocfilehash: 38044ef31208fa5a84c7aa4335c48408425b45a4
ms.sourcegitcommit: bf7577b3448b7cb0e336808f1112c44fa18c6f33
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104610948"
---
# <a name="plan-and-test-the-database-engine-upgrade-plan"></a>データベース エンジンのアップグレード計画の策定およびテスト

[!INCLUDE [SQL Server -Windows Only](../../includes/applies-to-version/sql-windows-only.md)]
  
 [!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] のアップグレードを成功させるには、どんな手法であろうと、適切な計画を立てる必要があります。  
  
## <a name="release-notes-and-known-upgrade-issues"></a>リリース ノートとアップグレードの既知の問題  
 [!INCLUDE[ssDE](../../includes/ssde-md.md)] をアップグレードする前に次を確認してください。

- [SQL Server 2019 リリース ノート](../../sql-server/sql-server-version-15-release-notes.md)
- [SQL Server 2017 リリース ノート](../../sql-server/sql-server-2017-release-notes.md) 
- [SQL Server 2016 リリース ノート](../../sql-server/sql-server-2016-release-notes.md)
- 「[SQL Server データベース エンジンの旧バージョンとの互換性](../discontinued-database-engine-functionality-in-sql-server.md)」の記事。  
  
## <a name="pre-upgrade-planning-checklist"></a>アップグレード前の計画チェックリスト  
 [!INCLUDE[ssDE](../../includes/ssde-md.md)]をアップグレードする前に、次のチェックリストと関連する記事を確認します。 これらの記事は、アップグレードの方法に関係なく、すべてのアップグレードに適用され、次の中から最適なアップグレード方法を決定するのに役立ちます: ローリング アップグレード、新しいインストールのアップグレード、またはインプレース アップグレード。 たとえば、オペレーティング システムのアップグレード、SQL Server 2005 からのアップグレード、または SQL Server の 32 ビット バージョンからアップグレードの場合、インプレース アップグレードまたはローリング アップグレードは実行できない場合があります。 デシジョン ツリーについては、「 [Choose a Database Engine Upgrade Method](../../database-engine/install-windows/choose-a-database-engine-upgrade-method.md)」を参照してください。  
  
-   **ハードウェアとソフトウェアの要件:** [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] をインストールするためのハードウェアとソフトウェアの要件を確認します。 これらの要件は、「[SQL Server のインストールに必要なハードウェアおよびソフトウェア](../../sql-server/install/hardware-and-software-requirements-for-installing-sql-server.md)」で確認できます。 アップグレードの計画サイクルの一環として、ハードウェアのアップグレード (ハードウェアが新しくなると、速度も速くなり、またプロセッサ数の不足またはデータベースとサーバーの統合が原因でライセンス処理が低下する場合があります) およびオペレーティング システムのアップグレードを検討する必要があります。 この種のハードウェアとソフトウェアの変更は、アップグレード方法の種類の選定に影響を与えます。  
  
-   **現在の環境:** 現在の環境を調査して、使用されている [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] コンポーネントと、実際の環境に接続されているクライアントを把握します。  
  
    -   **クライアント プロバイダー:** アップグレード中はクライアントごとにプロバイダーを更新する必要はありませんが、更新してもかまいません。 [!INCLUDE[sql14](../../includes/sssql14-md.md)] 以前からアップグレードする場合、次の [!INCLUDE[sql15](../../includes/sssql16-md.md)] 機能では、追加機能を利用するために更新されたプロバイダー、またはクライアントごとに更新されたプロバイダーが必要です。  
  
       -   [Always Encrypted &#40;データベース エンジン&#41;](../../relational-databases/security/encryption/always-encrypted-database-engine.md)  
  
       -   [Stretch Database](../../sql-server/stretch-database/stretch-database.md)  
  
       -   [可用性グループ リスナー、クライアント接続、およびアプリケーションのフェールオーバー &#40;SQL Server&#41;](../../database-engine/availability-groups/windows/listeners-client-connectivity-application-failover.md)  
  
       -   TLS セキュリティ更新プログラム  

-   **サードパーティ コンポーネント:** 統合バックアップなどのサードパーティ製のコンポーネントの互換性を確認します。  
  
-   **ターゲット環境:** ターゲット環境がハードウェアとソフトウェアの要件を満たしていることと、元のシステム要件をサポートできることを確認します。 たとえば、アップグレードでは、複数の SQL Server インスタンスを 1 つの新しい [!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] インスタンスに統合すること、または [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 環境をプライベートまたはパブリック クラウドに対して仮想化することが必要な場合があります。  
  
-   **エディション:** アップグレードで必要な [!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] の適切なエディションを決定し、アップグレードのための有効なアップグレード パスを決定します。 詳細については、「 [Supported Version and Edition Upgrades](../../database-engine/install-windows/supported-version-and-edition-upgrades.md)」をご覧ください。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のいずれかのエディションから別のエディションへアップグレードする前に、現在使用している機能がアップグレード先のエディションでサポートされているかどうかを確認します。  
  
    > [!NOTE]  
    >  [!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] を前のバージョンの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Enterprise エディションからアップグレードする場合は、Enterprise エディションとコアベース ライセンスの Enterprise エディションのいずれかを選ぶことができます。 これらの Enterprise エディションは、ライセンス モードのみが異なります。 詳細については、「 [Compute Capacity Limits by Edition of SQL Server](../../sql-server/compute-capacity-limits-by-edition-of-sql-server.md)」を参照してください。  
  
-   **下位互換性:** [!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] データベース エンジンの下位互換性に関する記事を参照して、[!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] とアップグレード対象の [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] バージョンとの間の動作の違いについて確認します。 「 [SQL Server Database Engine Backward Compatibility](../discontinued-database-engine-functionality-in-sql-server.md)」を参照してください。  
  
-   **Data Migration Assistant:** アップグレード プロセスを妨げるおそれのある問題、あるいは既存のスクリプトまたはアプリケーションを修正しなければならない可能性がある重大な変更については、Data Migration Assistant を実行して診断を行うことができます。
    Data Migration Assistant は[ここ](https://aka.ms/get-dma)からダウンロードできます。  
  
-   **システム構成チェッカー:** アップグレードをスケジュールする前に、[!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] システム構成チェッカー (SCC) を実行して、SQL Server セットアップ プログラムでセットアップに支障をきたす問題が検出されていないか判定します。 詳細については、「 [Check Parameters for the System Configuration Checker](../../database-engine/install-windows/check-parameters-for-the-system-configuration-checker.md)」を参照してください。  
  
-   **メモリ最適化テーブルのアップグレード:** メモリ最適化テーブルを含む SQL Server 2014 データベース インスタンスを SQL Server 2016 にアップグレードする場合、アップグレード プロセスでは、メモリ最適化テーブルをディスク上の新しいフォーマットに変換するために余分な時間が必要になります (これらの手順が実行されている間、データベースはオフラインになります)。   所要時間は、メモリ最適化テーブルのサイズと、I/O サブシステムの速度によって異なります。 インプレース アップグレードと新規インストール アップグレードの場合は、3 種類のデータ操作が必要です (ローリング アップグレードでは手順 1 は不要、手順 2 と手順 3 は必要です)。  
  
    1.  古いディスク上のフォーマットを使用してデータベースの回復を実行する (メモリ最適化テーブル内のすべてのデータを、ディスクからメモリに読み込む操作を含む)  
  
    2.  データを新しいディスク上のフォーマットでディスクにシリアル化する  
  
    3.  新しいフォーマットを使用してデータベースの回復を実行する (メモリ最適化テーブル内のすべてのデータを、ディスクからメモリに読み込む操作を含む)  
  
     さらに、このプロセス中にディスク上に十分な空き領域がないと、回復は失敗します。 既存のデータベースを格納するのに十分な領域がディスク上に存在することに加えて、インプレース アップグレードを実行するとき、または SQL Server 2016 インスタンスに SQL Server 2014 データベースをアタッチするときはデータベースの MEMORY_OPTIMIZED_DATA ファイル グループにあるコンテナーの現在のサイズと同じ容量のストレージが別途存在することを確認してください。 次のクエリを使用すると、MEMORY_OPTIMIZED_DATA ファイル グループ用に現在必要なディスク領域と、アップグレードを正常に行うために必要なディスクの空き領域を確認できます。  
  
    ```  
    select cast(sum(size) as float)*8/1024/1024 'size in GB'   
    from sys.database_files  
    where data_space_id in (select data_space_id from sys.filegroups where type=N'FX')  
    ```  
  
## <a name="develop-and-test-the-upgrade-plan"></a>アップグレード計画の作成とテスト  
 最良の手法は、IT プロジェクトの場合と同様に、アップグレードを処理することです。 アップグレードを行う上で必要なスキル (データベース管理、ネットワーク、抽出、変換、読み込み (ETL) など) を備えたアップグレード チームを編成してください。 チームは次のことを行う必要があります。  
  
-   **アップグレード方法を選択する:** 「[データベース エンジンのアップグレード方法の選択](../../database-engine/install-windows/choose-a-database-engine-upgrade-method.md)」を参照してください。  
  
-   **ロールバック計画を作成する:** ロールバックする必要がある場合、この計画を実行すると、元の環境を復元できます。  
  
-   **受け入れ基準を決定する:** アップグレードが正常に完了したことを確認してから、ユーザーをアップグレードされた環境にカットオーバーします。  
  
-   **アップグレード計画をテストする:** 実際のワークロードを使用してパフォーマンスをテストするには、Microsoft SQL Server 分散再生ユーティリティを使用します。 このユーティリティでは、複数のコンピューターを使用してトレース データを再生し、ミッションクリティカルなワークロードをシミュレートできます。 SQL Server のアップグレードの前後でテスト サーバーの再生を実行することで、パフォーマンスの差を測定したり、アップグレード時にアプリケーションに非互換性が発生するかどうかを調べたりすることができます。 詳細については、「[SQL Server Distributed Replay](../../tools/distributed-replay/sql-server-distributed-replay.md)」と「[管理ツール コマンド ライン オプション &#40;Distributed Replay Utility&#41;](../../tools/distributed-replay/administration-tool-command-line-options-distributed-replay-utility.md)」を参照してください。  
  
## <a name="next-steps"></a>次のステップ  
[データベース エンジンのアップグレード](../../database-engine/install-windows/upgrade-database-engine.md) 
  
## <a name="additional-resources"></a>その他のリソース 
[データベース移行ガイド](https://aka.ms/datamigration)