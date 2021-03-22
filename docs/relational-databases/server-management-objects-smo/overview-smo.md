---
description: 概要 (SMO)
title: 概要 (SMO) |Microsoft Docs
ms.custom: ''
ms.date: 08/06/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: ''
ms.topic: reference
ms.assetid: e988f9e8-6801-41d1-8069-726f487244d5
author: markingmyname
ms.author: maghan
monikerRange: =azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 8b3a9c15979d162ca345a0d440f7093c4bd15ad9
ms.sourcegitcommit: 1a544cf4dd2720b124c3697d1e62ae7741db757c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2020
ms.locfileid: "97439838"
---
# <a name="overview-smo"></a>概要 (SMO)
[!INCLUDE [SQL Server ASDB, ASDBMI, ASDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa.md)]

  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]管理オブジェクト (SMO) は、をプログラムで管理するために設計されたオブジェクトです [!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。 SMO を使用すると、カスタマイズされた [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 管理アプリケーションを作成することができます。 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] は、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] を管理するための強力で高度なアプリケーションですが、SMO アプリケーションの方が適している場合もあります。  
  
 たとえば、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 管理タスクを制御するユーザー アプリケーションでは、新規ユーザーのニーズを満たし、トレーニング コストを削減するために、単純化を必要とする場合があります。 カスタマイズされた [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] データベースの作成や、インデックスの効率を高めて、効率性を監視するためのアプリケーション作成が必要になることもあります。 また、サード パーティ製のハードウェアやソフトウェアをデータベース管理アプリケーションにシームレスに含めるために、SMO アプリケーションを使用する場合もあります。  
  
 SMO は以降のバージョンと互換性があるため [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] 、複数バージョンの環境を簡単に管理できます。  
  
 SMO の機能には、次のものがあります。  
  
-   キャッシュされたオブジェクト モデルおよび最適化されたオブジェクト インスタンスの作成。 オブジェクトは、参照が指定される場合にのみ読み込まれます。 オブジェクト プロパティは、オブジェクトが作成された場合にのみ部分的に読み込まれます。 残りのオブジェクトおよびプロパティは、直接参照される場合に読み込まれます。  
  
-   [!INCLUDE[tsql](../../includes/tsql-md.md)] ステートメントのバッチ実行。 ステートメントがバッチ化されて、ネットワーク パフォーマンスが向上します。  
  
-   [!INCLUDE[tsql](../../includes/tsql-md.md)] ステートメントのキャプチャ。 任意の操作をキャプチャしてスクリプトを作成できるようになります。 [!INCLUDE[ssManStudio](../../includes/ssmanstudio-md.md)] では、操作を直ちに実行する代わりに、この機能を使用してスクリプト化を行います。  
  
-   WMI プロバイダーを使用した [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] SQL サービスの管理。 プログラムを使用して、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] サービスの起動、停止および一時停止を行うことができます。  
  
-   高度なスクリプティング。 [!INCLUDE[tsql](../../includes/tsql-md.md)] スクリプトを生成して、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンス上のその他のオブジェクトに対するリレーションシップを記述する [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] オブジェクトを再作成することができます。  
  
-   URN (Unique Resource Name) の使用。 URN を使用すると、SMO オブジェクトのインスタンスを作成して、それを参照することができます。  
  
 また、SMO では、[!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] で導入されたいくつかの機能およびコンポーネントが、新しいオブジェクトやプロパティとして表現されています。 これらの新しい機能およびコンポーネントには次のものがあります。  
  
-   パーティション構成にデータを格納するためのテーブルおよびインデックスのパーティション分割。 詳細については、「 [Partitioned Tables and Indexes](../../relational-databases/partitions/partitioned-tables-and-indexes.md)」を参照してください。  
  
-   SOAP 要求を管理するための HTTP エンドポイント。 詳細については、「 [エンドポイントの実装](../../relational-databases/server-management-objects-smo/tasks/implementing-endpoints.md)」を参照してください。  
  
-   コンカレンシーを高めるためのスナップショット分離と行レベルのバージョニング。 詳細については、「[スナップショット分離を使用した作業](../../relational-databases/native-client/features/working-with-snapshot-isolation.md)」を参照してください。  
  
-   XML データの検証と格納を可能にする、XML スキーマ コレクション、XML インデックス、および XML データ型。 詳細については、「 [Xml スキーマコレクション &#40;SQL Server&#41;](../../relational-databases/xml/xml-schema-collections-sql-server.md) 」および「 [Xml スキーマの使用](../../relational-databases/server-management-objects-smo/tasks/using-xml-schemas.md)」を参照してください。  
  
-   データベースの読み取り専用コピーを作成するためのスナップショット データベース。  
  
-   メッセージ ベースの通信に対する [!INCLUDE[ssSB](../../includes/sssb-md.md)] サポート。 詳細については、「 [SQL Server Service Broker](../../database-engine/configure-windows/sql-server-service-broker.md)」を参照してください。  
  
-   [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] データベース オブジェクトの複数の名前に対するシノニム サポート。 詳細については、「 [シノニム &#40;データベースエンジン&#41;](../../relational-databases/synonyms/synonyms-database-engine.md)」を参照してください。  
  
-   [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] での電子メール サービス、電子メール プロファイル、および電子メール アカウントの作成を可能にするデータベース メールの管理。 詳細については、「[データベース メール](../../relational-databases/database-mail/database-mail.md)」を参照してください。  
  
-   接続情報登録のための登録サーバーのサポート。 詳細については、「 [サーバーの登録](../../ssms/register-servers/register-servers.md)」を参照してください。  
  
-   [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] イベントのトレースおよび再生。 詳細については、「 [SQL Server Profiler](../../tools/sql-server-profiler/sql-server-profiler.md)、 [SQL トレース](../../relational-databases/sql-trace/sql-trace.md)、 [SQL Server 分散再生](../../tools/distributed-replay/sql-server-distributed-replay.md)、および [拡張イベント](../../relational-databases/extended-events/extended-events.md)」を参照してください。  
  
-   セキュリティ コントロールのための証明書およびキーのサポート。 詳細については、「 [Encryption Hierarchy](../../relational-databases/security/encryption/encryption-hierarchy.md)」を参照してください。  
  
-   DDL イベント発生時に機能を追加するための DDL トリガー。 詳細については、「 [DDL トリガー](../../relational-databases/triggers/ddl-triggers.md)」を参照してください。  
  
 SMO の名前空間は、<xref:Microsoft.SqlServer.Management.Smo> です。 SMO は、アセンブリとして実装され [!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[dnprdnshort](../../includes/dnprdnshort-md.md)] ます。 これは [!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[dnprdnshort](../../includes/dnprdnshort-md.md)] 、SMO オブジェクトを使用する前に、バージョン2.0 の共通言語ランタイムをインストールする必要があることを意味します。 SMO アセンブリは、既定では、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] SDK オプションを使用してグローバル アセンブリ キャッシュ (GAC) にインストールされています。 アセンブリは C:\Program are SQL Server\130\SDK\Assemblies\. にあります。 詳細については、[!INCLUDE[vsprvs](../../includes/vsprvs-md.md)][!INCLUDE[dnprdnshort](../../includes/dnprdnshort-md.md)] のドキュメントを参照してください。  
  
## <a name="smo-classes"></a>SMO クラス  
 SMO クラスには、インスタンス クラスおよびユーティリティ クラスの 2 つのカテゴリがあります。  
  
 **インスタンス クラス**  
  
 インスタンス クラスは、サーバー、データベース、テーブル、トリガー、およびストアド プロシージャなどの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] オブジェクトを表現します。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスへの接続を確立し、これに送信されたコマンドのキャプチャ モードを制御するには、<xref:Microsoft.SqlServer.Management.Common.ServerConnection> クラスが使用されます。  
  
 SMO インスタンス オブジェクトは、データベース サーバーの階層を表す階層を形成しています。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスが最上位にあり、その下にデータベース、さらにテーブル、列、トリガーなどが続きます。 1 つまたは複数の列を持つテーブルなど、1 つの親から複数の子へのリレーションシップが論理的に存在している場合、子はオブジェクトのコレクションによって表されます。 それ以外の場合、子は 1 つのオブジェクトによってのみ表されます。  
  
 **ユーティリティクラス**  
  
 ユーティリティ クラスとは、特定のタスクを実行するために明示的に作成されたオブジェクトのグループです。 これらは、関数に基づいて、次のように異なるオブジェクト階層に分割されています。  
  
-   転送クラス。 スキーマおよびデータをその他のデータベースに転送するために使用されます。  
  
-   バックアップおよび復元クラス。 データベースのバックアップおよび復元のために使用されます。  
  
-   スクリプター クラス。 オブジェクトとオブジェクトの依存関係を再生成するためのスクリプト ファイルを作成するために使用されます。  
  
## <a name="smo-features"></a>SMO の機能  
 **最適化されたパフォーマンス**  
  
 SMO アーキテクチャは、オブジェクトが最初に部分的にしかインスタンス化されないため、サーバーから最小プロパティ情報が要求されるため、メモリの観点からは効率的です。 オブジェクトの完全なインスタンス化は、オブジェクトが明示的に参照されるまで行われません。 最初に取得されるプロパティのセットにはないプロパティが要求されたり、そのようなプロパティを要求するメソッドが呼び出された場合に、オブジェクトの完全なインスタンス化が行われます。 部分的にインスタンス化されたオブジェクトと完全にインスタンス化されたオブジェクトの間での移行は、ユーザーに対して透過的です。 また、大量のメモリを使用するプロパティは、明示的に参照されない限り取得されません。 この例として、<xref:Microsoft.SqlServer.Management.Smo.Database.Size%2A> オブジェクト プロパティの <xref:Microsoft.SqlServer.Management.Smo.Database> プロパティがあります。 ただし、部分的なインスタンス化では、より多くのネットワーク ラウンド トリップが必要になるため、使用しているアプリケーションに対して最適なパフォーマンス オプションではない可能性もあります。  
  
 インスタンス化を制御すると、システム環境を最適化することができます。 プロパティの参照時に大量のサーバー要求がトリガーされる場合がありますが、遅延インスタンス化の手法を使用することで、アプリケーションが必要とするメモリ量が最小化されます。  
  
 実際のデータベース オブジェクトを表したオブジェクトであるインスタンス クラスには、インスタンス化の 3 つのレベルがあります。 最小インスタンス化 (必要な最小限のプロパティのみが 1 つのブロックに読み取られる)、部分的インスタンス化 (比較的大量のメモリを使用するすべてのプロパティが 1 つのブロックに読み取られる)、および完全インスタンス化の 3 つです。 従来、インスタンス化の状態は、インスタンスされていないか完全にインスタンス化されているかのいずれかでした。 部分的にインスタンス化されたオブジェクトには、オブジェクト プロパティの完全なセットに対する値が格納されていないため、部分的インスタンス化の状態は効率性の向上に役立ちます。 部分的インスタンス化は、直接参照されないオブジェクトの既定の状態です。 これらのプロパティの 1 つが参照されると、オブジェクトの完全インスタンス化を要求するフォールトが生成されます。  
  
 **キャプチャの実行**  
  
 直接実行は、通常の実行方法です。 ステートメントは、発生した時点で [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスに直接送信されます。 キャプチャ実行は、これに代わる方法です。  
  
 キャプチャ実行では、通常実行される [!INCLUDE[tsql](../../includes/tsql-md.md)] バッチをキャプチャすることができます。 これにより、SMO プログラマは、スクリプトの保留、後で実行するための格納、エンドユーザーへのプレビューの提供を行うことができます。 たとえば、 **create database**、 **create table**、および **create index** ステートメントを1つのバッチで送信し、3つの連続したステップとして実行できます。 この機能は、<xref:Microsoft.SqlServer.Management.Smo.Server.%23ctor%2A> オブジェクトを使用して、ユーザーによって制御されます。  
  
 **WMI プロバイダー**  
  
 WMI プロバイダー オブジェクトは、SMO によってラップされます。 これによって SMO プログラマには、名前空間によって表されるプログラミング モデルおよび [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] WMI プロバイダーの詳細の理解を必要とせずに、SMO クラスと非常によく似た簡単なオブジェクト モデルが提供されます。 WMI プロバイダーでは、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] サービス、別名、クライアントおよびサーバー ネットワーク ライブラリを構成することができます。  
  
 **スクリプトの作成**  
  
 SMO では、スクリプトが拡張され、 **Scripter** クラスに移動されました。 **Scripter** クラスは、依存関係を検出し、オブジェクト間の関係を理解し、依存関係階層を操作できるようにします。 メインスクリプトオブジェクトは、 **Scripter** オブジェクトです。 また、依存関係を処理したり、進行中のイベントやエラー イベントに応答するサポート オブジェクトもいくつかあります。  
  
 **Scripter** オブジェクトでは、次の高度なスクリプト作成オプションがサポートされています。  
  
-   簡単な 1 フェーズ スクリプティング (スクリプトを 1 つの手順で作成)  
  
-   高度な3フェーズスクリプティング (依存関係検出、リスト生成、スクリプト生成の3つの手順でスクリプトを作成します)  
  
-   2 方向の依存関係検索 (依存関係または依存の検索の許可)  
  
-   進行中イベントへの応答  
  
-   エラー イベントへの応答  
  
 **一意のリソース名**  
  
 SMO オブジェクト ライブラリを使用するうえでの鍵となる概念の 1 つが URN (Unique Resource Name) です。 URN は、XPath と同様の構文を使用します。 XPath は、各レベルで修飾子と関数を持つオブジェクトを指定するために使用される階層パスです。 SMO では、URN には、パス、および制限された機能を持つ属性名の 2 つの要素があります。 パスはオブジェクトの場所を指定するために使用され、属性名によってある程度のフィルター操作が可能になります。  
  
 データベースの URN の例は次のようになります。  
  
```  
/Server/Database[@Name='Adventureworks2012']  
```  
  
 オブジェクトの URN は、URN プロパティを参照することで取得することができます。 また、Scripter オブジェクトは、オブジェクト参照を **Scripter** オブジェクトのメソッドに渡すパラメーターとして urn を使用します。 また、**サーバー** オブジェクトの **GETSMOOBJECT** メソッドに URN を指定することもできます。 これは、SMO オブジェクトのインスタンスを作成するために使用します。  
  
## <a name="sql-server-features-represented-in-smo"></a>SMO で表される SQL Server 機能  
 **テーブルとインデックスのパーティション分割**  
  
 インデックス テーブル パーティション分割によって、ファイル グループにまたがるテーブルとインデックスのデータの分散を管理することができます。 SMO オブジェクトには、この新機能が反映されています。  
  
 **EndPoints**  
  
 SOAP およびデータベース ミラーリング要求は、<xref:Microsoft.SqlServer.Management.Smo.Endpoint> オブジェクトを使用して、エンドポイントによって処理されます。  
  
 **スナップショット分離 / 行レベル バージョン**  
  
 スナップショット分離 (行レベル バージョン) は、新しい <xref:Microsoft.SqlServer.Management.Smo.Database> オブジェクト プロパティで表現されます。  
  
 **XML スキーマ名前空間、XML インデックス、および XML データ型**  
  
 XML スキーマ名前空間は、SMO ではオブジェクトのコレクションで表現されます。 XML インデックスは、SMO では **Index** オブジェクトプロパティによって表されます。  
  
 **フルテキスト検索の機能強化**  
  
 SMO には、フルテキスト検索の機能強化を反映した新しいオブジェクトが提供されています。  
  
 **[ページ確認]**  
  
 <xref:Microsoft.SqlServer.Management.Smo.DatabaseOptions.PageVerify%2A> オブジェクトは、データベース ページ確認オプションを表します。  
  
 **スナップショット データベース**  
  
 スナップショット データベースは、特定の時点における、指定されたデータベースの読み取り専用のコピーです。 スナップショット データベースは、<xref:Microsoft.SqlServer.Management.Smo.Database.IsDatabaseSnapshot%2A> オブジェクトの <xref:Microsoft.SqlServer.Management.Smo.Database> プロパティを使用して指定することができます。  
  
 **Service Broker**  
  
 [!INCLUDE[ssSB](../../includes/sssb-md.md)] およびその機能は、オブジェクトのグループで表現されます。  
  
 **インデックスの機能強化**  
  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インデックスの機能強化は、<xref:Microsoft.SqlServer.Management.Smo.Index> オブジェクトの新しいプロパティに反映されています。  
  
## <a name="see-also"></a>参照  
 [レプリケーション管理オブジェクトの概念](../../relational-databases/replication/concepts/replication-management-objects-concepts.md)  
  
