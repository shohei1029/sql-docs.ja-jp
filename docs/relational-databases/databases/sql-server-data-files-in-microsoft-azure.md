---
title: Microsoft Azure 内の SQL Server データ ファイル | Microsoft Docs
description: SQL Server データ ファイルを Microsoft Azure Storage サービスに格納する場合の重要な概念と考慮事項、および Azure Storage を使用する利点について説明します。
ms.custom: ''
ms.date: 12/04/2019
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: supportability
ms.topic: conceptual
ms.assetid: 38ffd9c2-18a5-43d2-b674-e425addec4e4
author: MikeRayMSFT
ms.author: mikeray
ms.openlocfilehash: f3d52fbd349a880ba9045a0ed1c7685e0015bcac
ms.sourcegitcommit: e2dbe5639b0d1e1dd7cb4cdf0b86f1b212b532b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2021
ms.locfileid: "105008423"
---
# <a name="sql-server-data-files-in-microsoft-azure"></a>Microsoft Azure 内の SQL Server データ ファイル
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
  ![Azure 上のデータ ファイル](../../relational-databases/databases/media/data-files-on-azure.png "Azure 上のデータ ファイル")  
  
Microsoft Azure 内の SQL Server データ ファイルにより、BLOB として格納された SQL Server データベース ファイルに対するネイティブ サポートが有効になります。 この機能を使用すると、オンプレミスの環境または Microsoft Azure 仮想マシンで実行されている SQL Server でデータベースを作成し、Microsoft Azure BLOB ストレージに専用のデータ保存場所を用意できます。 また、コンピューター間でデータベースを移動するプロセスが簡略化されます。 1 台のコンピューターからデータベースをデタッチして、別のコンピューターにアタッチすることができます。 また、Microsoft Azure ストレージを復元元または復元先として使用することで、データベースのバックアップ ファイルに代替の格納場所が提供されます。 このため、データの仮想化、移動、セキュリティ、および可用性の面での利点と、低コストと容易なメンテナンスで実現できる高可用性と柔軟なスケーリングにより、いくつかのハイブリッド ソリューションが有効になります。
 
> [!IMPORTANT]  
>  システム データベースを Azure Blob Storage に格納することは推奨されず、サポートされていません。 

 このトピックでは、SQL Server データ ファイルを Microsoft Azure ストレージ サービスに格納する場合の重要な概念と考慮事項について説明します。  
  
この機能の使用方法に関する実際の実地体験については、「[チュートリアル: Azure Blob Storage サービスと SQL Server 2016 データベースの使用](../tutorial-use-azure-blob-storage-service-with-sql-server-2016.md)」を参照してください。  
  
## <a name="why-use-sql-server-data-files-in-microsoft-azure"></a>Microsoft Azure で SQL Server データ ファイルを使用する理由 

- **容易で迅速な移行の利点:** この機能を使うと、オンプレミスのコンピューターの間でも、またオンプレミス環境とクラウド環境の間でも、アプリケーションを変更することなくデータベースを 1 つずつ移動するため、移行プロセスが容易になります。 このため、既存の内部設置型のインフラストラクチャを維持しつつ、インクリメンタルな移行を行うことができます。 さらに、内部設置型の環境の複数拠点でアプリケーションを実行する必要がある場合も、集中管理されたデータ ストレージにアクセスできることで、アプリケーション ロジックが簡素化されます。 場合によっては、地理的に分散した場所のそれぞれでコンピューター センターを短期間でセットアップし、多種多様なソースからデータを収集することもできます。 Azure Data Files により、データをある場所から別の場所に移動する代わりに、多数のデータベースを Microsoft Azure ページ BLOB として格納しておき、Transact-SQL スクリプトを実行して、ローカル コンピューターまたは仮想マシンにデータベースを作成することができます。

- **コストと無制限のストレージの利点:** この機能を使用すると、オンプレミスのコンピューティング リソースを活用しつつ、Microsoft Azure のオフサイト ストレージを無制限に確保できます。 格納場所として Microsoft Azure を使用すると、ハードウェア管理のオーバーヘッドがなく、アプリケーション ロジックに専念することが容易になります。 内部設置型のコンピューティング ノードが失われた場合は、データを移動することなく新しいノードをセットアップできます。

- **高可用性とディザスター リカバリーの利点:** Microsoft Azure の機能の SQL Server データ ファイルを使用すると、高可用性とディザスター リカバリーのソリューションを単純化できる場合があります。 たとえば、Microsoft Azure の仮想マシンまたは SQL Server のインスタンスがクラッシュした場合、BLOB へのリンクを再設定するだけで、新しい SQL Server インスタンスにデータベースを再作成できます。

- **セキュリティ上の利点:**  Azure で SQL Server データ ファイルを使用すると、コンピューティング インスタンスをストレージ インスタンスから分離できます。 データベースをフルに暗号化し、暗号化解除をストレージ インスタンスではなくコンピューティング インスタンスのみで行うことができます。 つまり、データとは物理的に分離された TDE (Transparent Data Encryption: 透過的なデータ暗号化) 証明書を使用して、パブリック クラウドに置くすべてのデータを暗号化できます。 TDE キーは、物理的に安全なオンプレミスのコンピューターに格納され、ローカルでバックアップされている master データベースに保存できます。 これらのローカル キーを使用して、Microsoft Azure ストレージ上に存在するデータを暗号化することができます。 クラウド内のストレージ アカウントの資格情報が盗用されても、TDE 証明書は常に内部設置型の環境にあるため、データの安全性が維持されます。

- **スナップショット バックアップ:** この機能では、Azure BLOB ストレージ サービスを使用して格納したデータベース ファイルを、Azure スナップショットを使用してほぼ瞬時にバックアップし、迅速に復元できます。 この機能により、バックアップと復元のポリシーを簡素化することができます。 詳細については、「 [Azure でのデータベース ファイルのファイル スナップショット バックアップ](../../relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure.md)」を参照してください。  

## <a name="concepts-and-requirements"></a>概念と要件  
  
Azure ディスクは、エンタープライズ規模のビジネスの継続性やディザスター リカバリー ソリューションに対応しています。 データベースを BLOB または Azure Premium ファイルに直接格納する場合は、インフラストラクチャ、管理、および監視のための VM にデータが自動的に関連付けられることはありません。

ページ BLOB にデータベース ファイルを配置することは、シンプルでわかりやすい Azure ディスクを使用するよりも機能としては高度なものです。

基本的なガイダンスでは、データのコピーをバックアップ用に作成したり、データのサイズに左右される操作として復元したりすることを回避する必要のあるシナリオの場合を除き、Azure ディスクを使用します。 また、高可用性とディザスター リカバリーでは、ファイル スナップショットのバックアップよりも、URL への定期的なバックアップ、または BLOB ストレージへのマネージド バックアップを使用する方がはるかに便利です。これは、ライフサイクル管理、複数リージョンのサポート、論理的な削除、バックアップの BLOB ストレージのその他すべての機能を利用できるためです。

### <a name="azure-storage-concepts"></a>Azure Storage の概念  

Azure の機能で SQL Server データ ファイルを使用する場合は、Azure 内でストレージ アカウントとコンテナーを作成する必要があります。 次に、SQL Server 資格情報を作成する必要があります。これには、コンテナーのポリシーに関する情報と、コンテナーにアクセスするために必要な Shared Access Signature が含まれます。  

[Microsoft Azure](https://azure.microsoft.com) の [Azure Storage](https://azure.microsoft.com/services/storage/) アカウントは、BLOB にアクセスするための名前空間の最高レベルを表します。 ストレージ アカウントには、合計サイズがストレージの制限内であれば、無制限の数のコンテナーを含めることができます。 ストレージの制限に関する最新情報については、「 [Azure のサブスクリプションとサービスの制限、クォータ、および制約](/azure/azure-subscription-service-limits)」をご覧ください。 コンテナーには、一連の [BLOB](/azure/storage/common/storage-introduction#blob-storage) をグループ化するコンテナーが用意されています。 すべての BLOB は 1 つのコンテナーに存在する必要があります。 アカウントには、無制限の数のコンテナーを含めることができます。 同様に、コンテナーには、BLOB を無制限に格納できます。 

Azure Storage に格納できる BLOB には、ブロック BLOB とページ BLOB の 2 種類があります。 この機能ではページ BLOB が使用されます。ファイル内のバイト範囲が頻繁に変更される場合はこちらの方が効率的です。 BLOB には、`https://storageaccount.blob.core.windows.net/<container>/<blob>` という URL 形式を使用してアクセスできます。  

> [!NOTE]
> SQL Server データ ファイルにはブロック BLOB を使用できません。 [ページ BLOB](/azure/storage/blobs/storage-blob-pageblob-overview) を使用してください。 

### <a name="azure-billing-considerations"></a>Azure の課金に関する注意点  

Azure サービスの使用コストを見積もることは、意思決定および計画のプロセスにおいて重要です。 Azure Storage に SQL Server データ ファイルを格納する場合は、ストレージとトランザクションに関連するコストを支払う必要があります。 さらに、Azure Storage の機能に SQL Server データ ファイルの格納を実装する場合は、45 から 60 秒ごとに BLOB リースの暗黙的な更新が必要になります。 その結果、.mdf、.ldf などのデータベース ファイルごとのトランザクション コストも必要になります。 Azure Storage と Azure Virtual Machines の使用に関する月額コストを見積もるには、「[Azure の価格](https://azure.microsoft.com/pricing/) 」の情報をご覧ください。  
  
### <a name="sql-server-concepts"></a>SQL サーバーの概念  

SQL Server データ ファイルに Azure ページ BLOB を使用するには:

- コンテナーのポリシーを作成し、Shared Access Signature (SAS) キーを生成します。

- データ ファイルまたはログ ファイルによって使用されるコンテナーごとに、名前がコンテナーのパスに一致する SQL Server 資格情報を作成します。

- Azure Storage コンテナー、関連するポリシー名、および SAS キーに関する情報を SQL Server 資格情報ストアに格納します。

次の例では、Azure ストレージ コンテナーが作成され、読み取り、書き込み、および一覧表示の権限でポリシーが作成されていることを前提としています。 コンテナーでポリシーを作成すると、SAS キーが生成されます。SAS キーは、暗号化なしにメモリ内に安全に保持され、SQL Server がコンテナー内の BLOB ファイルにアクセスするときに必要となります。 

次のコード スニペットでは、`'<your SAS key>'` を SAS キーに置き換えます。 SAS キーは、`'sr=c&si=<MYPOLICYNAME>&sig=<THESHAREDACCESSSIGNATURE>'` のようになります。

```sql
CREATE CREDENTIAL [https://testdb.blob.core.windows.net/data]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = '<your SAS key>'  
  
CREATE DATABASE testdb   
ON  
( NAME = testdb_dat,  
    FILENAME = 'https://testdb.blob.core.windows.net/data/TestData.mdf' )  
 LOG ON  
( NAME = testdb_log,  
    FILENAME =  'https://testdb.blob.core.windows.net/data/TestLog.ldf')  
```  

>[!IMPORTANT]
>コンテナーのデータ ファイルに対するアクティブな参照が存在する場合、対応する SQL Server 資格情報を削除しようとすると失敗します。

詳細については、「 [Azure のストレージ リソースへのアクセスの管理](/azure/storage/blobs/storage-manage-access-to-resources)」をご覧ください。  

### <a name="security"></a>セキュリティ  
 Azure Storage に SQL Server データ ファイルを格納する場合のセキュリティに関する考慮事項と要件は次のとおりです。

- Azure BLOB ストレージ サービスのコンテナーを作成する際は、アクセス権を private に設定することをお勧めします。 アクセス権を private に設定すると、コンテナーと BLOB データを読み取ることができるのは Azure アカウントの所有者だけになります。

- SQL Server データベース ファイルを Azure Storage に格納する際には、Shared Access Signature (コンテナー、BLOB、キュー、およびテーブルへの制限付きアクセス権を付与する URI) を使用する必要があります。 Shared Access Signature を使用することで、Azure Storage アカウント キーを共有せずに、SQL Server からストレージ アカウント内のリソースへのアクセスを可能にすることができます。

- これに加えて、従来型の内部設置環境でのセキュリティ対策もデータベースに適用することをお勧めします。  
  
### <a name="installation-prerequisites"></a>設置の前提条件  
 Azure に SQL Server データ ファイルを格納する場合のインストールに関する前提条件は次のとおりです。

- **オンプレミスの SQL Server:** この機能は、SQL Server 2016 以降に含まれています。 最新バージョンの SQL Server をダウンロードする方法については、[SQL Server](https://www.microsoft.com/sql-server/sql-server-downloads) に関するページを参照してください。

- Azure 仮想マシンで実行されている SQL Server:[Azure Virtual Machine に SQL Server](https://azuremarketplace.microsoft.com/marketplace/apps?search=sql%20server&page=1) をインストールする場合は、SQL Server 2016 をインストールするか、既存のインスタンスを更新します。 同様に、SQL Server 2016 CTP2 のプラットフォーム イメージを使用して Azure に新しい仮想マシンを作成することもできます。

  
###  <a name="limitations"></a><a name="bkmk_Limitations"></a> 制限事項  
  
- SQL Server のワークロードのパフォーマンス特性により、Azure Blob Storage で SQL Server のデータ ファイルはページ BLOB として実装されます。 ブロック BLOB や [Azure Data Lake Storage](/azure/storage/blobs/data-lake-storage-introduction) などその他の BLOB ストレージはサポートされていません。

- この機能の現在のリリースでは、Azure Storage に **FileStream** データを格納することはできません。 **FileStream** データは、Azure Storage に格納されているデータ ファイルを含むデータベースに格納することも可能ですが、FileStream のデータ ファイルはすべてローカルのストレージに格納する必要があります。  FileStream データはローカルのストレージに存在する必要があるので、それを Azure Storage を使用するコンピューター間で移動することはできません。そのため、FileStream に関連付けられているデータを別のコンピューターに移動するには、[従来の手法](../../relational-databases/blob/move-a-filestream-enabled-database.md)を使用することをお勧めします。  
  
- 現時点では、Azure Storage 内の特定のデータベース ファイルに一度にアクセスできるのは、1 つの SQL Server インスタンスだけです。 InstanceA がアクティブなデータベース ファイルとオンライン接続されているときに、誤って起動された InstanceB に同じデータ ファイルを指すデータベースがある場合、2 番目のインスタンスでは、次のエラー コードにより、データベースの起動に失敗します: **5120 物理ファイル "%.\*ls" を開けません。オペレーティング システム エラー %d: "%ls"** 。  
  
- Azure 機能で SQL Server データ ファイルを使用して Azure Storage 内に格納できるのは、.mdf、.ldf、.ndf ファイルのみです。  
  
- Azure 機能で SQL Server データ ファイルを使用する場合は、ストレージ アカウントに対する地理的レプリケーションはサポートされません。 ストレージ アカウントで地理的レプリケーションが実行されている場合に、地理的フェールオーバーが発生すると、データの破損が発生する可能性があります。  
  
- 容量の制限については、「[Blob Storage の概要](/azure/storage/blobs/storage-blobs-introduction)」を参照してください。  
  
- Azure Storage 機能で SQL Server データ ファイルを使用する場合は、インメモリ OLTP データを BLOB ストレージ内に格納することはできません。 これは、インメモリ OLTP に **FileStream** に対する依存関係があり、この機能の現在のリリースでは、Azure Storage に **FileStream** データを格納することがサポートされていないためです。  
  
- Azure 機能で SQL Server データ ファイルを使用する場合、SQL Server は、**master** データベース内で設定された照合順序を使用して、すべての URL 比較またはパス比較を実行します。  
  
- **AlwaysOn 可用性グループ** は、プライマリ レプリカのデータベースに新しいデータベース ファイルを追加しない限りサポートされます。 データベース操作により、プライマリ レプリカのデータベースに新しいファイルを作成する必要がある場合は、最初にセカンダリ ノードで可用性グループを無効にします。 それから、データベースに対してデータベース操作を実行し、プライマリ レプリカでデータベースをバックアップします。 次に、データベースをセカンダリ レプリカに復元します。 この操作が終了したら、セカンダリ ノードで Always On 可用性グループを再び有効にします。 

   >[!NOTE]
   >Azure 機能で SQL Server データ ファイルを使用する場合は、Always On フェールオーバー クラスター インスタンスはサポートされません。
  
- 通常の運用中、SQL Server では、一時リースを使用して BLOB をストレージ用に予約し、各 BLOB リースを 45 から 60 秒ごとに更新します。 サーバーがクラッシュし、同じ BLOB を使用するように構成された別の SQL Server インスタンスが起動された場合、新しいインスタンスは、BLOB の既存リース期限が切れるまで最大 60 秒間待機します。 リース期限が 60 秒以内に切れるのを待機できず、データベースを別のインスタンスにアタッチするには、BLOB のリースを明示的にリリースします。
  
## <a name="tools-and-programming-reference-support"></a>ツールおよびプログラミング リファレンスのサポート  
 ここでは、Azure 機能で SQL Server データ ファイルを使用する場合に使用可能なツールとプログラミング リファレンス ライブラリについて説明します。  
  
### <a name="powershell-support"></a>PowerShell のサポート  
 ファイル パスの代わりに BLOB ストレージの URL パスを参照して、SQL Server データ ファイルを BLOB ストレージ サービスに格納するには、PowerShell コマンドレットを使用します。 BLOB には、`https://storageaccount.blob.core.windows.net/<container>/<blob>` という URL 形式を使用してアクセスします。  
  
### <a name="sql-server-object-and-performance-counters-support"></a>SQL Server オブジェクトとパフォーマンス カウンターのサポート  

SQL Server 2014 以降では、Azure Storage 機能内の SQL Server データ ファイルと組み合わせて使用する目的で、1 つの新しい SQL Server オブジェクトが追加されました。 新しい SQL Server オブジェクトは [SQL Server, HTTP_STORAGE_OBJECT](../../relational-databases/performance-monitor/sql-server-http-storage-object.md) という名前です。これをシステム モニターで使用すると、SQL Server を Azure Storage と共に使用する場合のアクティビティを監視できます。  
  
### <a name="sql-server-management-studio-support"></a>SQL Server Management Studio のサポート  
 SQL Server Management Studio では、複数のダイアログ ウィンドウでこの機能を使用することができます。 たとえば、`https://teststorageaccnt.blob.core.windows.net/testcontainer/` は、ストレージ コンテナーの URL パスを表します。
 
 **[新しいデータベース]** 、 **[データベースのアタッチ]** 、 **[データベースの復元]** などの複数のダイアログ ウィンドウの **[パス]** として入力できます。 詳細については、[Microsoft Azure Blob Storage サービスと SQL Server 2016 データベースの使用](../tutorial-use-azure-blob-storage-service-with-sql-server-2016.md)」をご覧ください。  
  
### <a name="sql-server-management-objects-smo-support"></a>SQL Server 管理オブジェクト (SMO) のサポート  
 Azure 機能で SQL Server データ ファイルを使用する場合は、すべての SQL Server 管理オブジェクト (SMO) がサポートされます。 SMO オブジェクトにファイル パスが必要であれば、ローカル ファイル パスの代わりに BLOB の URL 形式 ( `https://teststorageaccnt.blob.core.windows.net/testcontainer/`など) を使用します。 SQL Server 管理オブジェクト (SMO) の詳細については、SQL Server オンライン ブックの「[SQL Server 管理オブジェクト &#40;SMO&#41; プログラミング ガイド](../../relational-databases/server-management-objects-smo/sql-server-management-objects-smo-programming-guide.md) 」をご覧ください。  
  
### <a name="transact-sql-support"></a>Transact-SQL のサポート  
 この機能の追加により、Transact-SQL の表層が次のように変更されました。

- **sys.master_files** システム ビューに、新しい **int** 列 **credential_id** が追加されました。 **credential_id** 列は、Azure Storage データ ファイルが自身の資格状態を使用するために `sys.credentials` への相互参照を有効にする目的で使用されます。 資格情報を使用するデータベース ファイルが存在するが、この資格情報を削除できないという場合などに、トラブルシューティングとして使用できます。  
  
##  <a name="troubleshooting-for-sql-server-data-files-in-microsoft-azure"></a><a name="bkmk_Troubleshooting"></a> Microsoft Azure で SQL Server データ ファイルを使用する場合のトラブルシューティング  
 サポートされていない機能または制限事項によるエラーを回避するために、まず「 [Limitations](../../relational-databases/databases/sql-server-data-files-in-microsoft-azure.md#bkmk_Limitations)」をご確認ください。  
  
 Azure Storage 機能で SQL Server データ ファイルを使用する場合に発生する可能性のあるエラーの一覧は、次のとおりです。  
  
 **認証エラー**  
  
- *資格情報 '%.\*ls' を削除できません。この資格情報は、アクティブなデータベース ファイルで使用されています。*    
    解決策:Azure Storage にあるアクティブなデータベース ファイルで使用中の資格情報を削除しようとすると、このエラーが発生することがあります。 資格情報を削除するには、まずこのデータベース ファイルのある関連 BLOB を削除する必要があります。 アクティブなリースを保持している BLOB を削除するには、先にリースを終了する必要があります。  
  
- *コンテナーに対して Shared Access Signature が正しく作成されていません。*    
     解決策:コンテナーに対して Shared Access Signature が正しく作成されていることを確認します。 「[チュートリアル:Microsoft Azure Blob Storage サービスと SQL Server 2016 データベースの使用](../tutorial-use-azure-blob-storage-service-with-sql-server-2016.md#2---create-a-sql-server-credential-using-a-shared-access-signature)」をご覧ください。  
  
- *SQL Server 資格情報が正しく作成されていません。*    
    解決策: **[ID]** フィールドに 'Shared Access Signature' を使用して、シークレットが正しく作成されたことを確認します。 「[チュートリアル:Microsoft Azure Blob Storage サービスと SQL Server 2016 データベースの使用](../tutorial-use-azure-blob-storage-service-with-sql-server-2016.md#3---database-backup-to-url)」をご覧ください。  
  
**BLOB リース エラー**  
  
- 同じ BLOB ファイルを使用する別のインスタンスの後に起動しようとして SQL Server がクラッシュしました。 解決策:通常の運用中、SQL Server では、一時リースを使用して BLOB をストレージ用に予約し、各 BLOB リースを 45 から 60 秒ごとに更新します。 サーバーがクラッシュし、同じ BLOB を使用するように構成された別の SQL Server インスタンスが起動された場合、新しいインスタンスは、BLOB の既存リース期限が切れるまで最大 60 秒間待機します。 リース期限が 60 秒以内に切れるのを待機できず、データベースを別のインスタンスにアタッチするには、BLOB のリースを明示的にリリースして、アタッチ操作の失敗を回避します。  
  
**データベース エラー**  
  
**データベースの作成中にエラーが発生しました** 解決策: 「[チュートリアル:Microsoft Azure Blob Storage サービスと SQL Server 2016 データベースの使用](../tutorial-use-azure-blob-storage-service-with-sql-server-2016.md#4----restore-database-to-virtual-machine-from-url)」をご覧ください。  
  
**ALTER ステートメントの実行中にエラーが発生しました。** 解決策: ALTER DATABASE ステートメントは、必ずデータベースがオンライン状態のときに実行してください。 データ ファイルを Azure Storage にコピーするときは常に、ブロック BLOB ではなくページ BLOB を作成します。 そうしないと、ALTER DATABASE は失敗します。 「[チュートリアル:Microsoft Azure Blob Storage サービスと SQL Server 2016 データベースの使用](../tutorial-use-azure-blob-storage-service-with-sql-server-2016.md)」をご覧ください。  
  
**エラー コード - 5120 物理ファイル "%.\*ls" を開けません。オペレーティング システム エラー %d: "%ls"**   

解決策: この機能では、Azure Storage 内の同じデータベース ファイルに複数の SQL Server インスタンスで同時にアクセスすることはできません。 InstanceA がアクティブなデータベース ファイルとオンライン接続されているときに、起動された InstanceB に同じデータ ファイルを指すデータベースがある場合、2 番目のインスタンスでは、次のエラー コードにより、データベースの起動に失敗します: *5120 物理ファイル "%.\*ls" を開けません。オペレーティング システム エラー %d: "%ls"* 。  
  
この問題を解決するには、まず、Azure Storage 内のデータベース ファイルに ServerA からアクセスする必要があるかどうかを決定します。 そうでない場合は、InstanceA と Azure Storage 内のデータベース ファイルとの接続を削除します。 その手順は次のとおりです。  

1.  ALTER DATABASE ステートメントを使用して、InstanceA のファイル パスをローカル フォルダーに設定します。  

2.  InstanceA でデータベースをオフラインに設定します。  

3.  次に、Azure Storage から InstanceA のローカル フォルダーにデータベース ファイルをコピーします。 これにより、InstanceA でデータベースのローカル コピーを確保できます。  

4.  データベースをオンラインに設定します。

**エラー コード 833 - 完了までに 15 秒を超える I/O 要求** 
   
   このエラーは、ストレージ システムで SQL Server ワークロードの要求を満たせないことを示しています。 アプリケーション層から IO アクティビティを減らすか、ストレージ層のスループット機能を強化してください。 詳細については、[エラー 833](../errors-events/mssqlserver-833-database-engine-error.md) に関するページを参照してください。 パフォーマンスの問題が解決しない場合は、Premium や UltraSSD などの別のストレージ層にファイルを移動することを検討してください。 Azure VM 上の SQL Server については、[ストレージ パフォーマンスの最適化](/azure/virtual-machines/premium-storage-performance)に関する記述を参照してください。


## <a name="next-steps"></a>次のステップ  
  
[データベースの作成](create-a-database.md)
