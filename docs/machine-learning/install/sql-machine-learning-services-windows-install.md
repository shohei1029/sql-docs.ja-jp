---
title: Windows へのインストール
description: Windows に SQL Server Machine Learning Services をインストールする方法について説明します。 Machine Learning Services を使用して、データベース内で Python または R スクリプトを実行できます。
ms.prod: sql
ms.technology: machine-learning-services
ms.date: 02/17/2021
ms.topic: how-to
author: dphansen
ms.author: davidph
ms.custom: seo-lt-2019
monikerRange: '>=sql-server-2017'
ms.openlocfilehash: d209daa0932068b41974c7e3597980b15657b1b2
ms.sourcegitcommit: bf7577b3448b7cb0e336808f1112c44fa18c6f33
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104611212"
---
# <a name="install-sql-server-machine-learning-services-python-and-r-on-windows"></a>Windows に SQL Server Machine Learning Services (Python と R) をインストールする

[!INCLUDE [SQL Server 2017 and later](../../includes/applies-to-version/sqlserver2017.md)]

Windows に SQL Server Machine Learning Services をインストールする方法について説明します。 Machine Learning Services を使用して、データベース内で Python または R スクリプトを実行できます。

## <a name="pre-install-checklist"></a><a name="bkmk_prereqs"> </a> インストール前のチェックリスト

+ データベース エンジンのインスタンスが必要です。 Python または R の機能だけをインストールすることはできませんが、既存のインスタンスにそれらを段階的に追加することはできます。

+ ビジネス継続性のために、Machine Learning Services では [Always On 可用性グループ](../../database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server.md)がサポートされています。 各ノードに Machine Learning Services をインストールし、パッケージを構成します。

+ Machine Learning Services のインストールは、SQL Server 2017 の [Always On フェールオーバー クラスター](../../sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server.md)ではサポート "*されていません*"。 SQL Server 2019 以降ではサポートされています。
 
+ Machine Learning Services をドメイン コントローラーにインストールしないでください。 セットアップの Machine Learning Services の部分が失敗します。

+ **共有機能** > **Machine Learning Server (スタンドアロン)** を、データベース内インスタンスを実行しているのと同じコンピューターにインストールしないでください。 スタンドアロン サーバーが同じリソースを求めて競合し、両方のインストールのパフォーマンスが低下します。

+ 他のバージョンの Python および R とのサイドバイサイド インストールはサポートされていますが、お勧めしません。 これがサポートされているのは、SQL Server インスタンスでは、オープンソースの R および Anaconda ディストリビューションの独自のコピーが使用されるからです。 SQL Server 外の SQL Server コンピューター上で Python および R を使用するコードを実行すると、さまざまな問題が発生する可能性があるため、お勧めしません。
    
  + 別のライブラリと実行可能ファイルを使用すると、SQL Server で実行されているものとは異なる結果が作成されます。
  + 外部ライブラリで実行されている R および Python スクリプトは、SQL Server で管理できないため、リソースの競合が発生します。

::: moniker range=">=sql-server-ver15"
> [!NOTE]
> Machine Learning Services は、既定で **SQL Server ビッグ データ クラスター** にインストールされます。 **ビッグ データ クラスター** を使用する場合、この記事の手順を実行する必要はありません。 詳細については、[ビッグ データ クラスターでの Machine Learning Services (Python および R) の使用](../../big-data-cluster/machine-learning-services.md)に関するページを参照してください。
::: moniker-end

> [!IMPORTANT]
> セットアップが完了したら、この記事で説明されている構成後の手順を必ず完了してください。 これらの手順には、SQL Server で外部スクリプトを使用できるようにすることや、ユーザーに代わって SQL Server が R ジョブや Python ジョブを実行するために必要なアカウントを追加することが含まれます。 通常、構成を変更するには、インスタンスを再起動するか、Launchpad サービスを再起動する必要があります。

## <a name="get-the-installation-media"></a>インストール メディアを入手する

[!INCLUDE[GetInstallationMedia](../../includes/getssmedia.md)]

::: moniker range="=sql-server-2017"
Python および R と Machine Learning Services との統合をサポートする SQL Server エディションの詳細については、「[SQL Server 2017 のエディションとサポートされる機能](../../sql-server/editions-and-components-of-sql-server-2017.md)」を参照してください。
::: moniker-end

::: moniker range="=sql-server-ver15"
Python および R と Machine Learning Services との統合をサポートする SQL Server エディションの詳細については、「[SQL Server 2019 (15.x) のエディションとサポートされる機能](../../sql-server/editions-and-components-of-sql-server-version-15.md)」を参照してください。
::: moniker-end

## <a name="run-setup"></a>セットアップを実行する

ローカルでのインストールの場合、管理者としてセットアップを実行する必要があります。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] をリモート共有からインストールする場合は、そのリモート共有に対する読み取り権限と実行権限を持つドメイン アカウントを使用する必要があります。

1. SQL Server のセットアップ ウィザードを開始します。
  
1. **[インストール]** タブで、 **[SQL Server の新規スタンドアロン インストールを実行するか、既存のインストールに機能を追加します]** を選択します。

   ::: moniker range="=sql-server-2017"
   ![SQL Server の新規スタンドアロン インストール](media/2017setup-installation-page-mlsvcs.png)
   ::: moniker-end

   ::: moniker range="=sql-server-ver15"
   ![SQL Server の新規スタンドアロン インストール](media/2019setup-installation-page-mlsvcs.png)
   ::: moniker-end

1. **[機能の選択]** ページで、次のオプションを選択します。

   ::: moniker range="=sql-server-2017"

   - **データベース エンジン サービス**
     
     SQL Server で R および Python を使用するには、データベース エンジンのインスタンスをインストールする必要があります。 既定のインスタンスまたは名前付きインスタンスを使用できます。

   - **Machine Learning Services (データベース内)**
     
     このオプションを選択すると、R および Python スクリプトの実行をサポートするデータベース サービスがインストールされます。

   ::: moniker-end

   ::: moniker range="=sql-server-ver15"

   - **データベース エンジン サービス**
     
     SQL Server で R または Python を使用するには、データベース エンジンのインスタンスをインストールする必要があります。 既定のインスタンスまたは名前付きインスタンスを使用できます。

   - **Machine Learning Services (データベース内)**
     
     このオプションを選択すると、R および Python スクリプトの実行をサポートするデータベース サービスがインストールされます。

   ::: moniker-end

   - **R**
     
     Microsoft R パッケージ、インタープリター、およびオープンソース R を追加するには、このオプションをオンにします。 
     
   - **Python**
     
     Microsoft Python パッケージ、Python 3.5 実行可能ファイルを追加したり、Anaconda ディストリビューションからライブラリを選択したりするには、このオプションをオンにします。
     
   ::: moniker range="=sql-server-ver15"
   Java のインストールと使用の詳細については、「[SQL Server の言語拡張を Windows にインストールする](../../language-extensions/install/windows-java.md)」を参照してください。
   ::: moniker-end
   
   ::: moniker range="=sql-server-2017"
   ![R および Python の機能オプション](media/2017setup-features-page-mls-rpy.PNG "R および Python のセットアップ オプション")
   ::: moniker-end
   
   ::: moniker range="=sql-server-ver15"
   ![R および Python の機能オプション](media/2019setup-features-page-mls-rpy.png "R および Python のセットアップ オプション")
   ::: moniker-end
   
   > [!NOTE]
   > 
   > **[Machine Learning Server (スタンドアロン)]** のオプションは選択しないでください。 **[共有機能]** の下の Machine Learning Server をインストールするオプションは、別のコンピューターでの使用を目的としています。

::: moniker range="=sql-server-2017"

4. **[Microsoft R Open のインストールに同意する]** ページで、 **[同意する]** 、 **[次へ]** の順に選択します。 

使用許諾契約書の対象:
+ Microsoft R Open
+ オープンソース R の基本パッケージとツール
+ Microsoft 開発チームの R パッケージと接続プロバイダーが強化されました。

1. **[Python のインストールに同意する]** ページで、 **[同意する]** 、 **[次へ]** の順に選択します。 Python のオープンソース ライセンス契約では、Anaconda および関連するツールに加えて、Microsoft 開発チームからのいくつかの新しい Python ライブラリも対象となっています。

   > [!NOTE]
   >  ご使用のコンピューターがインターネットにアクセスできない場合は、この時点でセットアップを一時停止して、個別にインストーラーをダウンロードできます。 詳細については、[インターネットへのアクセスなしで機械学習コンポーネントをインストールする](../install/sql-ml-component-install-without-internet-access.md)に関するページを参照してください。

1. **[インストールの準備完了]** ページで、以下が選択されていることを確認した後、 **[インストール]** を選択します。
  
   + データベース エンジン サービス
   + Machine Learning Services (データベース内)
   + R または Python、あるいはその両方

   構成ファイルが格納されている `..\Setup Bootstrap\Log` パスの下にあるフォルダーの場所をメモしておきます。 セットアップが完了したら、インストールされたコンポーネントを概要ファイルで確認できます。

1. セットアップが完了し、コンピューターの再起動を求めるメッセージが表示されたら、再起動してください。 セットアップが完了した時点で、インストール ウィザードによるメッセージを確認することが重要です。 詳細については、「 [SQL Server セットアップ ログ ファイルの表示と読み取り](../../database-engine/install-windows/view-and-read-sql-server-setup-log-files.md)」を参照してください。

::: moniker-end

::: moniker range="=sql-server-ver15"

1. **[Microsoft R Open のインストールに同意する]** ページで、 **[同意する]** 、 **[次へ]** の順に選択します。 このライセンス契約は、Microsoft R Open を対象としており、これには、オープンソース R 基本パッケージとツール、Microsoft 開発チームから提供された R パッケージおよび接続プロバイダーのディストリビューションが含まれています。

2. **[Python のインストールに同意する]** ページで、 **[同意する]** 、 **[次へ]** の順に選択します。 Python のオープンソース ライセンス契約では、Anaconda および関連するツールに加えて、Microsoft 開発チームからのいくつかの新しい Python ライブラリも対象となっています。

3. **[インストールの準備完了]** ページで、以下が選択されていることを確認した後、 **[インストール]** を選択します。
  
   + データベース エンジン サービス
   + Machine Learning Services (データベース内)
   + R と Python

   構成ファイルが格納されている `..\Setup Bootstrap\Log` パスの下にあるフォルダーの場所をメモしておきます。 セットアップが完了したら、インストールされたコンポーネントを概要ファイルで確認できます。

4. セットアップが完了し、コンピューターの再起動を求めるメッセージが表示されたら、再起動してください。 セットアップが完了した時点で、インストール ウィザードによるメッセージを確認することが重要です。 詳細については、「 [SQL Server セットアップ ログ ファイルの表示と読み取り](../../database-engine/install-windows/view-and-read-sql-server-setup-log-files.md)」を参照してください。

::: moniker-end

## <a name="set-environment-variables"></a>環境変数の設定

R 機能の統合のみの場合、**MKL_CBWR** 環境変数を設定して、Intel Math Kernel Library (MKL) 計算からの [一貫した出力を保証](https://software.intel.com/articles/introduction-to-the-conditional-numerical-reproducibility-cnr)する必要があります。

1. コントロール パネルで、 **[システムとセキュリティ]**  >  **[システム]**  >  **[システムの詳細設定]**  >  **[環境変数]** の順にクリックします。

2. 新しいユーザー変数またはシステム変数を作成します。 

   + 変数名を `MKL_CBWR` に設定する
   + 変数値を `AUTO` に設定する

この手順では、サーバーを再起動する必要があります。 スクリプトの実行を有効にしようとしている場合は、すべての構成作業が完了するまで再起動を遅らせることができます。

<a name="bkmk_enableFeature"></a>

## <a name="enable-script-execution"></a>スクリプトの実行を有効にする

1. [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)]を開きます。 

    > [!TIP]
    > 次のページから適切なバージョンをダウンロードしてインストールできます。[SQL Server Management Studio (SSMS) をダウンロードしてください](../../ssms/download-sql-server-management-studio-ssms.md)。
    > 
    > SQL Server に対する管理タスクとクエリをサポートする [Azure Data Studio](../../azure-data-studio/what-is-azure-data-studio.md) を使用することもできます。
  
2. Machine Learning Services をインストールしたインスタンスに接続し、 **[新しいクエリ]** をクリックしてクエリ ウィンドウを開き、次のコマンドを実行します。

    ```sql
    sp_configure
    ```

    プロパティ `external scripts enabled` の値は、この時点では **0** であることが必要です。 この機能は既定でオフになっています。 この機能は、R または Python スクリプトを実行する前に、管理者が明示的に有効にする必要があります。
    
3.  外部スクリプト機能を有効にするには、次のステートメントを実行します。
    
    ```sql
    EXEC sp_configure  'external scripts enabled', 1
    RECONFIGURE WITH OVERRIDE
    ```
    
    R 言語に対してこの機能を既に有効にしている場合は、Python に対して再構成を再度実行しないでください。 基になる拡張機能プラットフォームでは、両方の言語がサポートされています。

## <a name="restart-the-service"></a>サービスを再起動します。

インストールが完了したら、データベース エンジンを再起動します。

サービスを再起動すると、関連する [!INCLUDE[rsql_launchpad](../../includes/rsql-launchpad-md.md)] サービスも自動的に再起動されます。

サービスの再起動は、SSMS でインスタンスの **[再起動]** コマンドを右クリックするか、コントロール パネルの **[サービス]** パネルまたは [SQL Server 構成マネージャー](../../relational-databases/sql-server-configuration-manager.md)を使って行うことができます。

## <a name="verify-installation"></a>インストールの確認

次の手順に従って、外部スクリプトの起動に使用されるすべてのコンポーネントが実行されていることを確認します。

1. SQL Server Management Studio で、新しい [クエリ] ウィンドウを開き、次のコマンドを実行します。
    
   ```sql
   EXECUTE sp_configure  'external scripts enabled'
   ```

   **run_value** は 1 に設定されます。
    
2. **[サービス]** パネルまたは SQL Server 構成マネージャーを開き、**SQL Server Launchpad サービス** が実行されていることを確認します。 R または Python がインストールされているすべてのデータベース エンジンのインスタンスに対して 1 つのサービスがある必要があります。 サービスの詳細については、[機能拡張フレームワーク](../concepts/extensibility-framework.md)に関するページを参照してください。 
   
3. スタート パッドが実行されている場合は、単純な Python および R スクリプトを実行して、外部スクリプト ランタイムから SQL Server と通信できることを確認できます。

   [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] で新しい **[クエリ]** ウィンドウを開き、次のようなスクリプトを実行します。
   + R の場合
   
     ```sql
     EXEC sp_execute_external_script  @language =N'R',
     @script=N'
     OutputDataSet <- InputDataSet;
     ',
     @input_data_1 =N'SELECT 1 AS hello'
     WITH RESULT SETS (([hello] int not null));
     GO
     ```
     
   + Python の場合
     
     ```sql
     EXEC sp_execute_external_script  @language =N'Python',
     @script=N'
     OutputDataSet = InputDataSet;
     ',
     @input_data_1 =N'SELECT 1 AS hello'
     WITH RESULT SETS (([hello] int not null));
     GO
     ```
   
   **結果**

   スクリプトの実行には、最初に外部スクリプト ランタイムが読み込まれるときに少し時間がかかります。 結果は次のようになります。

   | hello |
   |----|
   | 1|

> [!NOTE]
> Python スクリプトで使用される列または見出しは、自動的には返されません。 出力に列名を追加するには、戻り値のデータ セットに対してスキーマを指定する必要があります。 これを行うには、ストアド プロシージャの WITH RESULTS パラメーターを使用して、列に名前を付け、SQL データ型を指定します。
>
> たとえば、次の行を追加して、任意の列名を生成できます。`WITH RESULT SETS ((Col1 AS int))`

::: moniker range=">=sql-server-2017"

<a name="apply-cu"></a>

## <a name="apply-updates"></a>更新プログラムの適用

### <a name="existing-installation"></a>既存のインストール

既存の SQL Server インスタンスに Machine Learning Services を追加していて、以前に累積的な更新プログラム (CU) を適用している場合は、データベース エンジンのバージョンと Machine Learning Services の機能が異なる可能性があります。 これにより、予期しない動作やエラーが発生する可能性があります。 

Machine Learning Services をデータベース エンジンと同じバージョンにするには、次の手順のようにします。

1. データベース エンジンの累積的な更新プログラム (CU) を確認します。 次の T-SQL ステートメントを実行します。

   ```sql
   SELECT @@VERSION
   ```
 
   次に示すのは、SQL Server 2019 の累積的な更新プログラム (CU) 8 からの出力の例です。
 
   *Microsoft SQL Server 2019 (RTM-CU8-GDR) (KB4583459) - 15.0.4083.2 (X64)   Nov  2 2020 18:35:09   Copyright (C) 2019 Microsoft Corporation  Developer Edition (64-bit) on Windows 10 Enterprise 10.0 <X64> (Build 19042: ) (Hypervisor)*

   詳細については、「[SQL Server とそのコンポーネントのバージョン、エディション、更新レベルを確認する](/troubleshoot/sql/general/determine-version-edition-update-level#machine-learning-services)」を参照してください。

1. データベース エンジンにインストールした[累積的な更新プログラム (CU)](../../database-engine/install-windows/latest-updates-for-microsoft-sql-server.md) をダウンロードします。

1. 累積的な更新プログラム (CU) のインストールを実行し、指示に従って Machine Learning Services の CU をインストールします。

### <a name="new-installation"></a>新しいインストール

SQL Server データベース エンジンの新しいインストールで Machine Learning Services をインストールする場合は、データベース エンジンと機械学習の両方のコンポーネントに、最新の累積的な更新プログラムを適用することをお勧めします。

インターネットに接続されているデバイスでは、通常、累積的な更新プログラムは Windows Update によって適用されますが、管理された更新には、次の手順を使用することもできます。 データベース エンジンに更新プログラムを適用すると、同じインスタンスにインストールしたすべての Python または R 機能の累積更新プログラムがセットアップによってプルされます。 

切断されたサーバーには追加の手順が必要です。 詳細については、[インターネット アクセスなしでコンピューターにインストールする > 累積的な更新プログラムを適用する](sql-ml-component-install-without-internet-access.md#apply-cu)を参照してください。

1. 既にインストールされているベースライン インスタンスを使用して開始します: SQL Server の初回リリース。

2. 累積的な更新プログラムの一覧に移動します: [Microsoft SQL Server の最新の更新プログラム](../../database-engine/install-windows/latest-updates-for-microsoft-sql-server.md)。

3. 最新の累積的な更新プログラムを選択します。 実行可能ファイルがダウンロードされ、自動的に抽出されます。

4. セットアップを実行します。 ライセンス条項に同意し、[機能の選択] ページで、累積的な更新プログラムが適用される機能を確認します。 機械学習機能を含む、現在のインスタンスにインストールされているすべての機能が表示されます。 セットアップにより、すべての機能を更新するために必要な CAB ファイルがダウンロードされます。

   ![インストールされている機能の概要](media/cumulative-update-feature-selection.png)

5. ウィザードを続行し、R および Python ディストリビューションのライセンス条項に同意します。 

::: moniker-end

## <a name="additional-configuration"></a>追加構成

外部スクリプトの検証手順が成功した場合は、SQL Server Management Studio、Visual Studio Code、または T-SQL ステートメントをサーバーに送信できる他の任意のクライアントから、R または Python コマンドを実行できます。

コマンドの実行中にエラーが発生した場合は、このセクションの追加の構成手順を確認してください。 場合によっては、サービスまたはデータベースに対して追加の適切な構成を行う必要があります。

インスタンス レベルでは、追加の構成には次のものが含まれます。

* [SQL Server Machine Learning Services のファイアウォール構成](../../machine-learning/security/firewall-configuration.md)
* [追加のネットワーク プロトコルの有効化](../../database-engine/configure-windows/enable-or-disable-a-server-network-protocol.md)
* [リモート接続の有効化](../../database-engine/configure-windows/configure-the-remote-access-server-configuration-option.md)
* [SQLRUserGroup のログインを作成する](../../machine-learning/security/create-a-login-for-sqlrusergroup.md)
* ディスク領域を消費するタスクを外部スクリプトで実行しないようにするための[ディスク クォータの管理](/windows/desktop/fileio/managing-disk-quotas)

::: moniker range=">=sql-server-ver15"
Windows の SQL Server 2019 では、分離メカニズムが変更されています。 このメカニズムは、**SQLRUserGroup**、ファイアウォール規則、ファイルのアクセス許可、および暗黙の認証に影響します。 詳細については、[Machine Learning Services の分離の変更](sql-server-machine-learning-services-2019.md)に関するページを参照してください。
::: moniker-end

<a name="bkmk_configureAccounts"></a> 
<a name="permissions-external-script"></a> 

データベースでは、次の構成の更新が必要になる場合があります。

* [SQL Server Machine Learning Services にユーザー アクセス許可を付与する](../../machine-learning/security/user-permission.md)

> [!NOTE]
> 追加の構成が必要かどうかは、SQL Server をインストールしたセキュリティ スキーマと、ユーザーをどのようにデータベースに接続して外部スクリプトを実行させるかによって異なります。

## <a name="suggested-optimizations"></a>推奨される最適化

これですべてが機能するようになったので、機械学習をサポートするようにサーバーを最適化したり、事前トレーニング済みの機械学習モデルをインストールしたりすることもできます。

::: moniker range="=sql-server-2017"

### <a name="add-more-worker-accounts"></a>ワーカー アカウントを追加する

多くのユーザーが同時にスクリプトを実行する場合は、Launchpad サービスに割り当てられるワーカー アカウントの数を増やすことができます。 詳細については、「[SQL Server Machine Learning Services での外部スクリプトの同時実行のスケーリング](../administration/scale-concurrent-execution-external-scripts.md)」を参照してください。
::: moniker-end

### <a name="optimize-the-server-for-script-execution"></a>スクリプトの実行用にサーバーを最適化する

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のセットアップの既定の設定は、データベース エンジンによってサポートされているさまざまなサービスに対してサーバーのバランスを最適化することを目的としています。サービスには、抽出、変換、および読み込み (ETL) プロセス、レポート、監査、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] データを使うアプリケーションなどが含まれます。 既定の設定では、特にメモリを集中的に使用する操作では、機械学習のリソースが制限または調整されることがあります。

確実に機械学習ジョブの優先順位が適切に設定され、リソースが提供されるようにするには、SQL Server Resource Governor を使って外部リソース プールを構成することをお勧めします。 また、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] データベース エンジンに割り当てられるメモリの量を変更したり、[!INCLUDE[rsql_launchpad](../../includes/rsql-launchpad-md.md)] サービスで実行するアカウントの数を増やしたりすることもできます。

- 外部リソースを管理するためのリソース プールを構成するには、[CREATE EXTERNAL RESOURCE POOL](../../t-sql/statements/create-external-resource-pool-transact-sql.md) に関するページを参照してください。
  
- データベース用に予約されているメモリの量を変更するには、「[サーバー メモリの構成オプション](../../database-engine/configure-windows/server-memory-server-configuration-options.md)」を参照してください。
  
- [!INCLUDE[rsql_launchpad](../../includes/rsql-launchpad-md.md)] によって開始できる R アカウントの数を変更するには、「[SQL Server Machine Learning Services での外部スクリプトの同時実行のスケーリング](../administration/scale-concurrent-execution-external-scripts.md)」を参照してください。

Standard Edition を使用していて、Resource Governor がない場合は、動的管理ビュー (DMV) と拡張イベントに加え、Windows イベント監視を使用してサーバー リソースを管理できます。

### <a name="install-additional-python-and-r-packages"></a>Python と R の追加パッケージをインストールする

SQL Server 用に作成する Python および R ソリューションでは、基本関数、SQL Server と共にインストールされた独自のパッケージの関数、SQL Server によってインストールされたオープンソースの Python および R のバージョンと互換性のあるサードパーティ製のパッケージを呼び出すことができます。

SQL Server で使用するパッケージは、インスタンスによって使用される既定のライブラリにインストールする必要があります。 コンピューター上に Python または R の別のインストールがある場合、またはパッケージをユーザー ライブラリにインストールした場合は、これらのパッケージが T-SQL から使用できなくなります。

追加のパッケージをインストールして管理するには、ユーザー グループを設定してデータベース レベルでパッケージを共有するか、ユーザーが独自のパッケージをインストールできるようにデータベース ロールを構成します。 詳細については、[Python パッケージのインストール](../package-management/install-additional-python-packages-on-sql-server.md)と[新しい R パッケージのインストール](../package-management/install-additional-r-packages-on-sql-server.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Python 開発者は、次のチュートリアルに従って、SQL Server で Python を使用する方法を学習できます。

+ [Python のチュートリアル:SQL Server Machine Learning Services での線形回帰を使用したスキー レンタルの予測](../tutorials/python-ski-rental-linear-regression-deploy-model.md)
+ [Python のチュートリアル:SQL Server Machine Learning Services と K-Means クラスタリングを使用して顧客を分類する](../tutorials/python-clustering-model.md)

R 開発者はいくつかの簡単な例を試して、SQL Server での R の動作方法の基本を確認できます。 次の手順については、以下のリンクを参照してください。

+ [クイック スタート: T-SQL での R の実行](../tutorials/quickstart-r-create-script.md)
+ [チュートリアル:R 開発者向けのデータベース内分析](../tutorials/r-taxi-classification-introduction.md)