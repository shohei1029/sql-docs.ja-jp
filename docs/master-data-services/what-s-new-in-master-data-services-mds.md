---
title: 新機能
description: マスターデータサービスの SQL Server 2019 リリースの変更点、改善点、および更新プログラムについて説明します。
ms.custom: ''
ms.date: 07/08/2016
ms.prod: sql
ms.prod_service: mds
ms.reviewer: ''
ms.technology: master-data-services
ms.topic: conceptual
ms.assetid: ad530f60-d480-4457-ba7a-93a10c8a1695
author: lrtoyou1223
ms.author: lle
ms.openlocfilehash: e57616613c37c568a72b20369acf3d3fd54521ed
ms.sourcegitcommit: efce0ed7d1c0ab36a4a9b88585111636134c0fbb
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104833876"
---
# <a name="what39s-new-in-master-data-services-mds"></a>マスター データ サービス (MDS) の新機能

[!INCLUDE [SQL Server - Windows only ASDBMI  ](../includes/applies-to-version/sql-windows-only-asdbmi.md)]

  このトピックでは、の最新リリースにおける変更と更新の概要を [!INCLUDE[ssnoversion](../includes/ssnoversion-md.md)] [!INCLUDE[ssMDSshort](../includes/ssmdsshort-md.md)] 示します。 
  
 [!INCLUDE[ssMDSshort_md](../includes/ssmdsshort-md.md)]のデータを整理する方法の概要については、 [マスター データ サービスの概要](../master-data-services/master-data-services-overview-mds.md)を参照してください。 
  
 **マスター データ サービスをインストールし、データベースと Web サイトをセットアップして、サンプル モデルをデプロイするには、「** [Master Data Services Overview (MDS) (マスター データ サービスの概要 (MDS))](../master-data-services/master-data-services-overview-mds.md)」を参照してください。  
  
 **ダウンロード**  
  
-   [!INCLUDE[sssql15-md](../includes/sssql16-md.md)]をダウンロードするには、  **[評価センター](https://www.microsoft.com/evalcenter/evaluate-sql-server-2016)** に移動してください。  
  
-   Azure アカウントをすでにお持ちですか?  次に **[](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2019-ws2019?tab=Overview)** 、が [!INCLUDE[ssnoversion](../includes/ssnoversion-md.md)] 既にインストールされている仮想マシンを起動するには、ここにアクセスします。  
  
##  <a name="improved-performance"></a>パフォーマンスの向上  
  
 パフォーマンスが向上したことで、さらに大きなモデルを作成できます。また、データ読み込みの効率性と全体的なパフォーマンスも向上しました。 たとえば、Microsoft Excel 用アドインのパフォーマンスが改善したことでデータの読み込み時間が短縮され、より大きなエンティティをアドインが処理できます。  
  
 Microsoft Excel 用アドインの詳細については、「 [Microsoft Excel 用マスター データ サービス アドイン](../master-data-services/microsoft-excel-add-in/master-data-services-add-in-for-microsoft-excel.md)」を参照してください。  
  
 強化された機能を次に示します。  
  
-   エンティティ レベルでデータを圧縮できます。これは既定で有効になっています。 このデータ圧縮が有効な場合は、エンティティ関連のすべてのテーブルとインデックスが、SQL 行レベル圧縮機能によって圧縮されます。 これにより、マスター データの読み取りまたは更新時、特にマスター データの行数が数百万に及ぶ場合や、NULL 値の列が多数含まれる場合に、ディスク I/O が大幅に減少します。  
  
     SQL Server エンジン側の CPU 使用率は少し上昇するため、CPU がサーバーにバインドされている場合は、エンティティを編集してデータ圧縮をオフにできます。  
  
     詳細については、「[Create an Entity (Master Data Services) (エンティティを作成する (マスター データ サービス))](../master-data-services/create-an-entity-master-data-services.md)」および「[データの圧縮](../relational-databases/data-compression/data-compression.md)」を参照してください。  
  
-   動的なコンテンツ圧縮の IIS 機能は既定で有効になっています。 CPU 使用率は増加しますが、XML 応答サイズを大幅に縮小し、ネットワーク I/O を節約できます。 CPU がサーバーにバインドされている場合は、次の設定を [!INCLUDE[ssMDSshort](../includes/ssmdsshort-md.md)] Web.config ファイルに追加することで、データ圧縮をオフにできます。  
  
    ```  
    <configuration>  
       \<system.webServer>  
          <urlCompression doStaticCompression="true" doDynamicCompression="false " />  
       \</system.webServer>  
    </configuration>  
  
    ```  
  
     詳細については、「 [URL Compression (URL 圧縮)](https://www.iis.net/configreference/system.webserver/urlcompression)」を参照してください。  
  
-   次の新しい SQL Server エージェント ジョブはインデックスとログを管理します。  
  
    -   MDS_MDM_Sample_Index_Maintenace  
  
    -   MDS_MDM_Sample_Log_Maintenace  
  
 既定では、MDS_MDM_Sample_Index_Maintenance ジョブは毎週実行されます。 このスケジュールは変更できます。 また、UdpDefragmentation ストアド プロシージャを使用すると、いつでも手動でジョブを実行できます。 ストアド プロシージャは、大量のマスター データが挿入または更新されるたびに、あるいは既存のバージョンから新しいバージョンが作成された後に実行することをお勧めします。  
  
 インデックスは断片化が 30% を超えると、オンラインで再作成されます。 再作成中は、同じテーブルでの CRUD 操作のパフォーマンスが影響を受けます。 パフォーマンスの低下が心配な場合は、営業時間外にストアド プロシージャを実行することをお勧めします。 インデックスの断片化の詳細については、「 [Reorganize and Rebuild Indexes](../relational-databases/indexes/reorganize-and-rebuild-indexes.md)」を参照してください。  
  
 詳細については、マスター データ サービスのブログ投稿「 [Performance and Scale Improvement in SQL Server 2016 (SQL Server 2016 のパフォーマンスとスケーラビリティの向上)](https://techcommunity.microsoft.com/t5/sql-server-integration-services/performance-and-scale-improvement-in-sql-server-2016/ba-p/388225)」を参照してください。  
  
##  <a name="improved-security"></a>セキュリティの強化  
  
 新しいスーパー ユーザー機能権限により、前リリースの [!INCLUDE[ssMDSshort](../includes/ssmdsshort-md.md)]のサーバー管理権限と同じものがユーザーまたはグループに付与されます。 スーパー ユーザー権限は、複数のユーザーとグループに割り当てることができます。 以前のリリースでは、最初に [!INCLUDE[ssMDSshort](../includes/ssmdsshort-md.md)] をインストールしたユーザーがサーバー管理者になり、その権限を別のユーザーまたはグループに委譲することは困難でした。 詳細については、「 [機能領域のアクセス許可 &#40;マスターデータサービス&#41;](../master-data-services/functional-area-permissions-master-data-services.md)」を参照してください。  
  
 このリリースでは、ユーザーに対して管理権限をモデル レベルで明示的に割り当てることができます。 つまり、エンティティ レベルなどのモデル サブツリーで後からユーザーに権限が割り当てられた場合、ユーザーはこの管理権限を保持し続けます。  
  
 この [!INCLUDE[ssMDSshort](../includes/ssmdsshort-md.md)] リリースでは、読み取り、作成、更新、削除の各権限が新しく導入され、権限レベルがさらに多く提供されています。 たとえば、ユーザーに更新権限しかない場合、そのユーザーはマスター データを更新できますが、作成や削除はできません。 作成、更新、または削除権限をユーザーに付与すると、そのユーザーには読み取り権限が自動的に割り当てられます。 読み取り、作成、更新、削除の各権限を組み合わせることもできます。  
  
 次の表に示すように、 [!INCLUDE[sssql15-md](../includes/sssql16-md.md)][!INCLUDE[ssMDSshort](../includes/ssmdsshort-md.md)]にアップグレードすると、以前の権限が新しい権限に変換されます。  
  
|以前のリリースの権限|新しい権限|  
|------------------------------------|--------------------|  
|[!INCLUDE[ssMDSshort](../includes/ssmdsshort-md.md)] を最初にインストールしたユーザーにサーバー管理権限がある。|ユーザーにスーパー ユーザー機能権限がある|  
|ユーザーにモデル レベルの更新権限があり、モデル サブツリーの権限はないため、暗黙的にモデル管理者になる。|ユーザーにモデル レベルの明示的な管理権限がある。|  
|ユーザーに読み取り専用権限がある。|ユーザーに読み取りアクセス権限がある。|  
|ユーザーに更新権限がある。|ユーザーに 4 つの権限 (作成、更新、削除、読み取り) がすべてある。|  
|ユーザーに拒否権限がある。|ユーザーに拒否権限がある。|  
  
 権限の詳細については、「[Security (Master Data Services) (セキュリティ (マスター データ サービス))](../master-data-services/security-master-data-services.md)」を参照してください。  
  
##  <a name="improved-transaction-log-maintenance"></a>トランザクション ログのメンテナンスの強化  
  
 トランザクション ログを、あらかじめ決められた間隔で、またはスケジュールに従って消去できます。この消去はモデル レベルで行われ、システム設定が使用されます。 データ変更と ETL プロセスが多い MDS システムでは、こうしたテーブルは急激に拡大し、これがパフォーマンス低下とストレージ領域の問題につながることがあります。  
  
 ログから削除できるデータの種類を次に示します。  
  
-   指定した日数を経過したトランザクションの履歴。  
  
-   指定した日数を経過した検証の問題の履歴。  
  
-   指定した日数よりも前に実行されたステージング バッチ。  
  
 トランザクション ログからデータを削除する頻度は、システム設定を使用してモデル レベルで構成します。 詳細については、「[システム設定 (マスター データ サービス)](../master-data-services/system-settings-master-data-services.md)」および「[Create a Model (Master Data Services) (モデルの作成 (マスター データ サービス))](../master-data-services/create-a-model-master-data-services.md)」を参照してください。 詳細については、「[トランザクション (マスター データ サービス)](../master-data-services/transactions-master-data-services.md)」を参照してください。  
  
 SQL Server エージェント ジョブの MDS_MDM_Sample_Log_Maintenace は、トランザクション ログのクリーンアップをトリガーし、毎晩実行します。 このジョブのスケジュールを変更するには、SQL Server エージェントを使用します。  
  
 ストアド プロシージャを呼び出して、トランザクション ログを消去することもできます。 詳細については、「[トランザクション (マスター データ サービス)](../master-data-services/transactions-master-data-services.md)」を参照してください。  
  
## <a name="improved-troubleshooting"></a>トラブルシューティングの向上  
  
 [!INCLUDE[sssql15-md](../includes/sssql16-md.md)][!INCLUDE[ssMDSshort](../includes/ssmdsshort-md.md)]では、デバッグを改善し、問題のトラブルシューティングをさらに容易にするための機能が追加されました。 詳細については、「[Tracing (Master Data Services) (トレース (マスター データ サービス))](../master-data-services/tracing-master-data-services.md)」を参照してください。  
  
## <a name="improved-manageability"></a>管理の容易性の向上  
  
 管理の容易性の向上は、メンテナンス コストの削減に役立つほか、投資収益率 (ROI) に良い影響を与えます。 たとえば、トランザクション ログのメンテナンスやセキュリティが強化されたほか、次の機能が新しく追加されました。  
  
-   50 文字を超える属性名の使用  
  
-   Name 属性と Code 属性の名前変更と非表示  
  
 詳細については、次の各トピックを参照してください。  
  
-   [モデル (マスター データ サービス)](../master-data-services/models-master-data-services.md)  
  
-   [エンティティ (マスター データ サービス)](../master-data-services/entities-master-data-services.md)  
  
-   [トランザクション (マスター データ サービス)](../master-data-services/transactions-master-data-services.md)  
  
-   [セキュリティ (マスター データ サービス)](../master-data-services/security-master-data-services.md)  

## <a name="business-rule-improvements"></a>ビジネス ルールの強化
 **ビジネス ルールの管理 (Excel 用 MDS アドイン)**  
  
 Excel 用マスター データ サービス アドインでは、ビジネス ルールの作成、編集など、ビジネス ルールを管理できます。 ビジネス ルールは、データの検証に使用されます。  
 
 **Business Rules Extension**  
  
 ユーザー定義の SQL スクリプトを、ビジネス ルールの条件とアクションの拡張機能として適用できます。 SQL 関数を条件として、 SQL ストアド プロシージャをアクションとして使用できます。 詳細については、「[Business Rules Extension (Master Data Services) (ビジネス ルールの拡張機能 (マスター データ サービス))](../master-data-services/business-rules-extension-master-data-services.md)」を参照してください。 
 
 **ビジネス ルール管理エクスペリエンスの設計変更**  
  
 MDS のビジネス ルール管理エクスペリエンスの設計が完全に変更され、エクスペリエンスが向上しました。 この機能の詳細については、「[Business Rules (Master Data Services) (ビジネス ルール (マスター データ サービス))](../master-data-services/business-rules-master-data-services.md)」を参照してください。  
  
 **Excel 用 MDS アドインからのビジネス ルール管理機能の削除**  
  
 エクスペリエンスの設計が変更されたため、ビジネス ルール管理機能が Excel 用 MDS アドインから削除されました。    

 **新しいビジネス ルール条件**  
  
 完全な条件セットを提供する&7; つのビジネス ルール条件が新しく追加されました。 詳細については、「[ビジネス ルール条件 (マスター データ サービス)](../master-data-services/business-rule-conditions-master-data-services.md)」を参照してください。  

## <a name="derived-hierarchy-improvements"></a>派生階層の強化

 **派生階層の多対多リレーションシップ**  
  
 派生階層を作成して、多対多リレーションシップを表示できます。 2 つのエンティティ間の多対多リレーションシップは、両者間のマッピングを提供する第 3 のエンティティを使用してモデリングできます。 マッピング エンティティは、他のエンティティを参照するドメイン ベースの属性が 2 つ以上含まれているエンティティです。  
  
 たとえば、エンティティ M のドメイン ベースの属性が A と B をそれぞれ参照しています。A から B への階層は、マッピング エンティティを使用して作成できます。  
  
 詳細については、「[派生階層 (Master Data Services) の多対多リレーションシップを表示する](../master-data-services/show-many-to-many-relationships-in-derived-hierarchies-master-data-services.md)」を参照してください。  
 
 **派生階層の多対多リレーションシップの編集**  
  
 多対多リレーションシップを編集するには、マッピング エンティティのメンバーを変更します。 詳細については、「 [派生階層 &#40;マスターデータサービス&#41;の多対多リレーションシップの表示 ](../master-data-services/show-many-to-many-relationships-in-derived-hierarchies-master-data-services.md)」を参照してください。  
 
 **派生階層管理エクスペリエンスの向上**  
  
 MDS の派生階層管理エクスペリエンスが改善されました。 この機能の詳細については、「[派生階層を作成する (マスター データ サービス)](../master-data-services/create-a-derived-hierarchy-master-data-services.md)」を参照してください。  
  
 エクスペリエンスの設計が変更されたため、ビジネス ルール管理機能が Excel 用 MDS アドインから削除されました。  
 
## <a name="attribute-improvements"></a>属性の強化   
    
 **カスタム インデックス**  
  
 1つの属性 (単一のインデックス) または属性の一覧 (複合インデックス) で、エンティティ内の非クラスター化インデックスを作成して、クエリのパフォーマンスを向上させることができます。 詳細については、「[カスタム インデックス (マスター データ サービス)](../master-data-services/custom-index-master-data-services.md)」を参照してください。  
 
  **属性フィルター**  
  
 ドメイン ベースの属性の場合、リーフ メンバーについては、フィルターの親属性を使用して、ドメイン ベースの属性の許容値を制限できます。 詳細については、「[ドメイン ベースの属性を作成する (マスター データ サービス)](../master-data-services/create-a-domain-based-attribute-master-data-services.md)」を参照してください。  
 
## <a name="entity-and-member-improvements"></a>エンティティとメンバーの強化 
  
 **エンティティの同期関係**  
  
 エンティティの同期関係を作成することで、さまざまなモデル間でエンティティ データを共有できます。 詳細については、「[Entity Sync Relationship (Master Data Service) (エンティティの同期関係 (マスター データ サービス))](../master-data-services/entity-sync-relationship-master-data-services.md)」を参照してください。  
  
 **論理削除されたメンバーのパージ**  
  
 モデル バージョンで論理削除されたすべてのメンバーをパージ (完全に削除) できるようになりました。 メンバーを削除しても、そのメンバーは非アクティブ化 (論理削除) されるだけです。 詳細については、「[Purge Version Members (Master Data Service) (バージョン メンバーのパージ (マスター データ サービス))](../master-data-services/purge-version-members-master-data-services.md)」を参照してください。  
 
## <a name="improvements-for-managing-changes"></a>変更の管理に関する強化 
  
 **メンバー リビジョン履歴**  
  
 メンバーが変更されると、メンバー リビジョン履歴が記録されます。 リビジョン履歴のロールバックや、リビジョンの表示、注釈設定が可能です。 **ログ保有期間** のプロパティを使用すると、履歴データを保持する期間を指定できます。 詳細については、「[Member Revision History (Master Data Service) (メンバー リビジョン履歴 (マスター データ サービス))](../master-data-services/member-revision-history-master-data-services.md)」を参照してください。  
  
 **競合のマージ**  
  
 他のユーザーによって変更されているデータをパブリッシュしようとしている場合、パブリッシュ操作は競合エラーで失敗します。 このエラーを解決するには、競合のマージを実行した後で変更を再パブリッシュします。 詳細については、「 [Merge Conflicts (Master Data Service) (競合のマージ (マスター データ サービス)](../master-data-services/merge-conflicts-master-data-services.md) 」および「 [競合のマージ (MDS Add-in for Excel)](../master-data-services/microsoft-excel-add-in/merge-conflicts-mds-add-in-for-excel.md)」を参照してください。  
  
 **変更セット**  
  
 変更セットを使用して、エンティティに対する保留中の変更を保存できます。また、保留中の変更は表示および変更できます。 エンティティの変更に承認が必要な場合は、保留中の変更を変更セットに保存して送信し、管理者の承認を受ける必要があります。 詳細については、「 [変更セット &#40;マスターデータサービス&#41;](../master-data-services/changesets-master-data-services.md)」を参照してください。  
  
 **変更セットの電子メールと管理**  
  
 このリリースでは、モデルおよびバージョンごとのすべての変更を表示および管理できます。 また、承認が必要なエンティティについて変更セットの状態が変更されるたびに、電子メール通知を受け取ることもできます。 詳細については、「[Manage Changesets (Master Data Services) (変更セットの管理 (マスター データ サービス)](../master-data-services/manage-changesets-master-data-services.md)」および「[通知 (Master Data Services)](../master-data-services/notifications-master-data-services.md)」を参照してください。  
  
 **リビジョン履歴の表示と管理**  
  
 リビジョン履歴を、エンティティ単位およびメンバー単位で表示して管理できます。 更新権限を持っている場合は、メンバーを以前のバージョンにロールバックできます。 詳細については、「[Member Revision History (Master Data Service) (メンバー リビジョン履歴 (マスター データ サービス))](../master-data-services/member-revision-history-master-data-services.md)」を参照してください。  
 
## <a name="tool-and-sample-improvements"></a>ツールとサンプルの強化 
  
 **Excel 用 MDS アドインでクエリ ファイルを保存する、または開く**  
  
 エンティティ エクスプローラー ページで **[Excel]** をクリックして、ショートカット クエリ ファイルを保存できます。 または、Excel 用 MDS アドインで、コンピューターに保存されたクエリ ファイルを開くことができます。 保存されているファイルを開くには、QueryOpener アプリケーションを使用します。 詳細については、「[Shortcut Query Files (MDS Add-in for Excel) (ショートカット クエリ ファイル (Excel 用 MDS アドイン))](../master-data-services/microsoft-excel-add-in/shortcut-query-files-mds-add-in-for-excel.md)」を参照してください。  
  
 クエリ ファイルには、エクスプローラー ページのフィルターと階層情報が含まれています。  
   
 **モデル配置パッケージ サンプルの更新**  
  
 新しいシナリオをサポートするためにサンプル パッケージが更新されました。 詳細については、「 [SQL Server サンプル: モデルの配置パッケージ (マスター データ サービス)](../master-data-services/sql-server-samples-model-deployment-packages-mds.md)」を参照してください。  

[!INCLUDE[get-help-options](../includes/paragraph-content/get-help-options.md)]

[!INCLUDE[contribute-to-content](../includes/paragraph-content/contribute-to-content.md)]
  
## <a name="see-also"></a>参照  
 [マスター データ サービスと SQL Server 2016 のエディションでサポートされるデータ品質サービス機能](../master-data-services/master-data-services-and-data-quality-services-features-support.md)  
 [非推奨のマスター データ サービス機能](../master-data-services/deprecated-master-data-services-features.md)  
 [提供が中止されたマスター データ サービス機能](../master-data-services/discontinued-master-data-services-features.md)
