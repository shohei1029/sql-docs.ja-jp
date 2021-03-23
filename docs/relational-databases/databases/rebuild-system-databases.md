---
description: システム データベースの再構築
title: システム データベースの再構築 | Microsoft Docs
ms.custom: ''
ms.date: 06/06/2016
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: ''
ms.topic: conceptual
helpviewer_keywords:
- master database [SQL Server], rebuilding
- REBUILDDATABASE parameter
- rebuilding databases, master
- system databases [SQL Server], rebuilding
ms.assetid: af457ecd-523e-4809-9652-bdf2e81bd876
author: stevestein
ms.author: sstein
ms.openlocfilehash: 1f02a50bf9fa7082c05a9e0c4149d0a699d133b6
ms.sourcegitcommit: c09ef164007879a904a376eb508004985ba06cf0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104890727"
---
# <a name="rebuild-system-databases"></a>システム データベースの再構築
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
  [master](../../relational-databases/databases/master-database.md)、 [model](../../relational-databases/databases/model-database.md)、 [msdb](../../relational-databases/databases/msdb-database.md)、または [resource](../../relational-databases/databases/resource-database.md) の各システム データベースの損傷による問題を解決するか、既定のサーバー レベルの照合順序を変更するには、システム データベースを再構築する必要があります。 このトピックでは、 [!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)]でシステム データベースを再構築する手順について説明します。  
  
 **このトピックの内容**  
  
   - **作業を開始する準備:**  
  
     [制限事項と制約事項](#Restrictions)  
  
     [前提条件](#Prerequisites)  
  
   - **手順:**  
  
     [システム データベースの再構築](#RebuildProcedure)  
  
     [resource データベースの再構築](#Resource)  
  
     [新しい msdb データベースの作成](#CreateMSDB) 

     [tempdb データベースをリビルドする](#RebuildTempdb)  
  
   - **補足情報:**  
  
     [再構築エラーのトラブルシューティング](#Troubleshoot)  
  
##  <a name="before-you-begin"></a><a name="BeforeYouBegin"></a> はじめに  
  
###  <a name="limitations-and-restrictions"></a><a name="Restrictions"></a> 制限事項と制約事項  
 master、model、msdb、および tempdb の各システム データベースを再構築する場合は、元の場所からデータベースを削除して再作成する必要があります。 再構築ステートメントに新しい照合順序を指定する場合は、その照合順序の設定でシステム データベースが作成されます。 これらのデータベースに対するユーザーの変更はすべて失われます。 たとえば、master データベースのユーザー定義オブジェクト、msdb にスケジュールされたジョブ、または model データベースの既定のデータベース設定に対する変更が対象になります。  
  
###  <a name="prerequisites"></a><a name="Prerequisites"></a> 前提条件  
 システム データベースを再構築する前に次の作業を行い、システム データベースを現在の設定に復元できるようにしておいてください。  
  
1. サーバー全体のすべての構成値を記録します。  
  
    ```SQL  
    SELECT * FROM sys.configurations;  
    ```  
  
2.  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスと現在の照合順序に適用されているすべての修正プログラムを記録します。 システム データベースの再構築後にこれらの修正プログラムを再適用する必要があります。  
  
    ```SQL  
    SELECT  
    SERVERPROPERTY('ProductVersion ') AS ProductVersion,  
    SERVERPROPERTY('ProductLevel') AS ProductLevel,  
    SERVERPROPERTY('ResourceVersion') AS ResourceVersion,  
    SERVERPROPERTY('ResourceLastUpdateDateTime') AS ResourceLastUpdateDateTime,  
    SERVERPROPERTY('Collation') AS Collation;  
    ```  
  
3.  システム データベースのすべてのデータとログ ファイルの現在の場所を記録します。 システム データベースを再構築すると、すべてのシステム データベースがそれぞれ元の場所にインストールされます。 システム データベースのデータまたはログ ファイルを別の場所に移動していた場合は、そのファイルを再度移動する必要があります。  
  
    ```SQL  
    SELECT name, physical_name AS current_file_location  
    FROM sys.master_files  
    WHERE database_id IN (DB_ID('master'), DB_ID('model'), DB_ID('msdb'), DB_ID('tempdb'));  
    ```  
  
4.  master、model、および msdb の各データベースの現在のバックアップを探します。  
  
5.  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスがレプリケーション ディストリビューターとして構成されている場合は、ディストリビューション データベースの現在のバックアップを探します。  
  
6.  システム データベースの再構築に必要な権限を持っていることを確認してください。 この操作を実行するには、 **sysadmin** 固定サーバー ロールのメンバーである必要があります。 詳細については、「 [サーバー レベルのロール](../../relational-databases/security/authentication-access/server-level-roles.md)」を参照してください。  
  
7.  master、model、msdb のデータおよびログのテンプレート ファイルのコピーがローカル サーバーに存在することを確認します。 テンプレート ファイルの既定の場所は、C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\MSSQL\Binn\Templates です。 これらのファイルは再構築プロセスで使用され、セットアップを成功させるにはこれらのファイルが存在する必要があります。 これらのファイルがない場合は、セットアップの修復機能を実行するか、インストール メディアからファイルを手動でコピーします。 インストール メディアのファイルを探すには、適切なプラットフォーム ディレクトリ (x86 または x64) に移動し、次に setup\sql_engine_core_inst_msi\Pfiles\SqlServr\MSSQL.X\MSSQL\Binn\Templates に移動します。  
  
##  <a name="rebuild-system-databases"></a><a name="RebuildProcedure"></a> システム データベースの再構築  
 次の手順では、master、model、msdb、および tempdb の各システム データベースを再構築します。 再構築するシステム データベースを指定することはできません。 クラスター化されたインスタンスの場合、この手順はアクティブ ノードで実行する必要があります。また、手順を実行する前に、対応するクラスター アプリケーション グループ内の [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] リソースをオフラインにしておく必要があります。  
  
 この手順では resource データベースが再構築されません。 このトピックで後述する「resource データベースの再構築手順」セクションを参照してください。  
  
#### <a name="to-rebuild-system-databases-for-an-instance-of-sql-server"></a>SQL Server のインスタンスのシステム データベースを再構築するには  
  
1.  [!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] インストール メディアをディスク ドライブに挿入するか、コマンド プロンプトから、ディレクトリをローカル サーバーの setup.exe ファイルがある場所に変更します。 サーバーの既定の場所は C:\Program Files\Microsoft SQL Server\130\Setup Bootstrap\SQLServer2016 です。  
  
2.  コマンド プロンプト ウィンドウから、次のコマンドを入力します。 角かっこは省略可能なパラメーターであることを示します。 角かっこは入力しません。 ユーザー アカウント制御 (UAC) が有効な Windows オペレーション システムを使用する場合は、セットアップの実行に高度な特権が必要になります。 管理者としてコマンド プロンプトを実行する必要があります。  
  
     **Setup /QUIET /ACTION=REBUILDDATABASE /INSTANCENAME=InstanceName /SQLSYSADMINACCOUNTS=accounts [ /SAPWD= StrongPassword ] [ /SQLCOLLATION=CollationName]**  
  
    |パラメーター名|説明|  
    |--------------------|-----------------|  
    |/QUIET または /Q|セットアップがユーザー インターフェイスなしで実行されるように指定します。|  
    |/ACTION=REBUILDDATABASE|セットアップでシステム データベースを再作成することを指定します。|  
    |/INSTANCENAME=*InstanceName*|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]のインスタンスの名前を指定します。 既定のインスタンスには「MSSQLSERVER」と入力します。|  
    |/SQLSYSADMINACCOUNTS=*accounts*|**sysadmin** 固定サーバー ロールに追加する Windows グループまたは個々のアカウントを指定します。 複数のアカウントを指定する場合、各アカウントを空白で区切ります。 たとえば、「 **BUILTIN\Administrators MyDomain\MyUser**」と入力します。 アカウント名に空白を含むアカウントを指定する場合は、アカウントを二重引用符で囲みます。 たとえば、「 **NT AUTHORITY\SYSTEM**」と入力します。|  
    |[ /SAPWD=*StrongPassword* ]|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] **sa** アカウントのパスワードを指定します。 このパラメーターは、インスタンスが混合モード認証 ([!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] および Windows 認証) を使用する場合に必要になります。<br /><br /> **&#42;&#42; セキュリティ メモ &#42;&#42;** **sa** アカウントは、よく知られた [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] アカウントで、悪意のあるユーザーの攻撃対象となることが少なくありません。 **sa** ログインには、複雑なパスワードを使用することが非常に重要です。<br /><br /> Windows 認証モードにはこのパラメーターを指定しないでください。|  
    |[ /SQLCOLLATION=*CollationName* ]|新しいサーバー レベルの照合順序を指定します。 このパラメーターは省略可能です。 これを指定しない場合は、サーバーの現在の照合順序が使用されます。<br /><br /> **\*\* 重要 \*\*** サーバー レベルの照合順序を変更しても、既存のユーザー データベースの照合順序は変更されません。 新しく作成されたすべてのユーザー データベースには、既定で新しい照合順序が使用されます。<br /><br /> 詳細については、「 [サーバーの照合順序の設定または変更](../../relational-databases/collations/set-or-change-the-server-collation.md)」を参照してください。|  
    |[ /SQLTEMPDBFILECOUNT=NumberOfFiles ]|tempdb データ ファイルの数を設定します。 この値は 8 またはコアの数の、どちらか大きい方まで増やすことができます。<br /><br /> 既定値:8 またはコアの数のうち、小さい方の値。|  
    |[ /SQLTEMPDBFILESIZE=FileSizeInMB ]|各 tempdb データ ファイルの初期サイズを MB 単位で指定します。 最大 1024 MB まで指定できます。<br /><br /> 既定値:8|  
    |[ /SQLTEMPDBFILEGROWTH=FileSizeInMB ]|各 tempdb データ ファイルのファイル拡張増分値を MB 単位で指定します。 0 は、自動拡張がオフで、領域を追加できないことを示します。 最大 1024 MB まで指定できます。<br /><br /> 既定値:64|  
    |[ /SQLTEMPDBLOGFILESIZE=FileSizeInMB ]|tempdb ログ ファイルの初期サイズを MB 単位で指定します。 最大 1024 MB まで指定できます。<br /><br /> 既定値:8.<br /><br /> 許容範囲: 最小値 = 8、最大値 = 1024 です。|  
    |[ /SQLTEMPDBLOGFILEGROWTH=FileSizeInMB ]|tempdb ログ ファイルのファイル拡張増分値を MB 単位で指定します。 0 は、自動拡張がオフで、領域を追加できないことを示します。 最大 1024 MB まで指定できます。<br /><br /> 既定値:64<br /><br /> 許容範囲: 最小値 = 8、最大値 = 1024 です。|  
    |[ /SQLTEMPDBDIR=Directories ]|tempdb データ ファイルのディレクトリを指定します。 複数のディレクトリを指定する場合、各ディレクトリを空白で区切ります。 複数のディレクトリが指定されている場合、tempdb データ ファイルはラウンド ロビン形式ですべてのディレクトリにまたがるようになります。<br /><br /> 既定値:システム データ ディレクトリ|  
    |[ /SQLTEMPDBLOGDIR=Directory ]|tempdb のログ ファイルのディレクトリを指定します。<br /><br /> 既定値:システム データ ディレクトリ|  
  
3.  セットアップによりシステム データベースの再構築が完了すると、メッセージが表示されることなく、コマンド プロンプトに戻ります。 Summary.txt ログ ファイルを調査し、プロセスが正常に完了したことを確認します。 このファイルは C:\Program Files\Microsoft SQL Server\130\Setup Bootstrap\Logs にあります。  
  
4.  RebuildDatabase シナリオでは、システム データベースが削除され、クリーンな状態で再インストールされます。 tempdb ファイルの数の設定は保持されないため、tempdb ファイルの数の値はセットアップ中には不明です。 したがって、RebuildDatabase シナリオでは再度追加される tempdb ファイルの数を特定できません。 tempdb ファイルの数の値は、SQLTEMPDBFILECOUNT パラメーターで再度指定できます。 パラメーターが指定されていない場合、RebuildDatabase により、既定の数の tempdb ファイル、つまり CPU の数と 8 のうち少ない方の数の tempdb ファイルが追加されます。  
  
## <a name="post-rebuild-tasks"></a>再構築後の作業  
 データベースを再構築した後は、次の追加作業の実行が必要になる場合があります。  
  
-   master、model、および msdb の各データベースを最新の完全バックアップの状態に復元します。 詳細については、「[システム データベースのバックアップと復元 &#40;SQL Server&#41;](../../relational-databases/backup-restore/back-up-and-restore-of-system-databases-sql-server.md)」を参照してください。  
  
    > [!IMPORTANT]  
    >  サーバー照合順序を変更した場合は、システム データベースを復元しないでください。 復元すると、新しい照合順序が元の照合順序の設定に置き換わります。  
  
     バックアップが存在しないか、復元されたバックアップが最新のものでない場合は、存在しないエントリを再作成します。 たとえば、ユーザー データベース、バックアップ デバイス、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログイン、エンドポイントなどの存在しないエントリをすべて再作成します。 エントリを再作成する場合、エントリの作成時と同じスクリプトを実行してください。  
  
> [!IMPORTANT]  
>  不正ユーザーによる変更を防ぐために、スクリプトを保護しておくことをお勧めします。  
  
-   [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスがレプリケーション ディストリビューターとして構成されている場合は、ディストリビューション データベースを復元する必要があります。 詳細については、「 [レプリケートされたデータベースのバックアップと復元](../../relational-databases/replication/administration/back-up-and-restore-replicated-databases.md)」を参照してください。  
  
-   システム データベースを記録した以前の場所に移動します。 詳細については、「 [システム データベースの移動](../../relational-databases/databases/move-system-databases.md)」を参照してください。  
  
-   サーバー全体の構成値が記録した以前の値と一致することを確認します。  
  
##  <a name="rebuild-the-resource-database"></a><a name="Resource"></a> resource データベースの再構築  
 次の手順では、resource システム データベースを再構築します。 resource システム データベースを再構築する場合は、すべての修正プログラムが失われるため、再適用する必要があります。  
  
#### <a name="to-rebuild-the-resource-system-database"></a>resource システム データベースを再構築するには  
  
1.  配布メディアから、 [!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] セットアップ プログラム (setup.exe) を起動します。  
  
2.  左側のナビゲーション領域の **[メンテナンス]** をクリックし、 **[修復]** をクリックします。  
  
3.  セットアップ サポート ルールおよびセットアップ サポート ファイルのルーチンが実行されて、システムに必須コンポーネントがインストールされていること、およびコンピューターがセットアップの検証ルールに合格していることが確認されます。 続行するには、 **[OK]** または **[インストール]** をクリックします。  
  
4.  [インスタンスの選択] ページで、修復するインスタンスを選択し、 **[次へ]** をクリックします。  
  
5.  修復ルールが実行され、操作が検証されます。 続行するには、 **[次へ]** をクリックします。  
  
6.  **[修復の準備完了]** ページで **[修復]** をクリックします。 [完了] ページでは、操作が完了したことが示されます。  
  
##  <a name="create-a-new-msdb-database"></a><a name="CreateMSDB"></a> 新しい msdb データベースの作成  

 **msdb** データベースが破損した場合に、 **msdb** データベースのバックアップがなければ、 **instmsdb** スクリプトを使用して新しい **msdb** データベースを作成することができます。  
  
> [!WARNING]  
>  **instmsdb** スクリプトを使用して **msdb** データベースを再構築すると、ジョブ、警告、オペレーター、メンテナンス プラン、バックアップ履歴、ポリシー ベースの管理設定、データベース メール、パフォーマンス データ ウェアハウスなど、 **msdb** に格納されていた情報がすべて消去されます。  
  
1.  [!INCLUDE[ssDE](../../includes/ssde-md.md)]エージェント、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 、および [!INCLUDE[ssRS](../../includes/ssrs.md)]と、 [!INCLUDE[ssIS](../../includes/ssis-md.md)]をデータ ストアとして使用しているすべてのアプリケーションを含め、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] に接続しているすべてのサービスを停止します。  
  
2.  というコマンドを使用して、コマンド ラインから [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] を開始します。 `NET START MSSQLSERVER /T3608`  
  
     詳細については、「 [データベース エンジン、SQL Server エージェント、SQL Server Browser サービスの開始、停止、一時停止、再開、および再起動](../../database-engine/configure-windows/start-stop-pause-resume-restart-sql-server-services.md) 」を参照してください。  
  
3.  別のコマンド ライン ウィンドウで、**msdb** データベースをデタッチします。これには、`SQLCMD -E -S<servername> -dmaster -Q"EXEC sp_detach_db msdb"` というコマンドを実行します ( *\<servername>* は [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスに置き換えます)  
  
4.  Windows エクスプローラーを使用して、 **msdb** データベースの各ファイルの名前を変更します。 これらのファイルは、既定で [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンスの DATA サブフォルダーにあります。  
  
5.  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 構成マネージャーを使用して、 [!INCLUDE[ssDE](../../includes/ssde-md.md)] サービスを通常の方法で停止および再起動します。  
  
6.  コマンド ライン ウィンドウで、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] に接続し、コマンド `SQLCMD -E -S<servername> -i"C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\MSSQL\Install\instmsdb.sql" -o"C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\MSSQL\Install\instmsdb.out"`  
  
     *\<servername>* は [!INCLUDE[ssDE](../../includes/ssde-md.md)] のインスタンスに置き換えます。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]のインスタンスのファイル システム パスを使用してください。  
  
7.  Windows のメモ帳を使用して **instmsdb.out** ファイルを開き、エラーの出力を確認します。  
  
8.  インスタンスにインストールされているすべての修正プログラムを再適用します。  
  
9. ジョブや警告など、 **msdb** データベースに格納されていたユーザー コンテンツを再作成します。  
  
10. **msdb** データベースをバックアップします。  

##  <a name="rebuild-the-tempdb-database"></a><a name="RebuildTempdb"></a> tempdb データベースをリビルドする  

**tempdb** データベースが破損していて、データベース エンジンを起動できない場合に、すべてのシステム データベースをリビルドすることなく、**tempdb** をリビルドすることができます。
  
1. 現在の tempdb.mdf ファイルおよび templog.ldf ファイルが欠落しいていない場合は、名前を変更します。 
1. 次のコマンドを使用して、コマンド プロンプトから [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] を開始します。 

   ```sql
   sqlservr -c -f -T3608 -T4022 -s <instance> -mSQLCMD
   ```

   既定のインスタンス名には、MSSQLSERVER を使用します。名前付きインスタンスには、MSSQL$<instance_name> を使用します。 トレース フラグ 4022 によって、スタートアップ ストアド プロシージャの実行が無効になります。 -mSQLCMD を使用すると、[sqlcmd.exe](../../ssms/scripting/sqlcmd-use-the-utility.md) のみがサーバーに接続できるようになります (「[他のスタートアップ オプション](../../database-engine/configure-windows/database-engine-service-startup-options.md#other-startup-options)」を参照)

   > [!Note] 
   > [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] が開始された後も、コマンド プロンプト ウィンドウが開いたままになっていることを確認してください。 コマンド プロンプト ウィンドウを閉じると、プロセスが終了します。

1. **sqlcmd** を使用してサーバーに接続し、次のストアド プロシージャを使用して tempdb データベースの状態をリセットします。

   ```sql
   exec master..sp_resetstatus tempdb
   ```

1. コマンド プロンプト ウィンドウで Ctrl + C を押してサーバーをシャットダウンします

1. [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] サービスを再開します。 これにより、tempdb データベース ファイルの新しいセットが作成され、tempdb データベースが復旧します。

  
##  <a name="troubleshoot-rebuild-errors"></a><a name="Troubleshoot"></a> 再構築エラーのトラブルシューティング  
 コマンド プロンプト ウィンドウには、構文エラーおよびその他の実行時エラーが表示されます。 セットアップ ステートメントに次の構文エラーがないか調査してください。  
  
-   パラメーター名の前のスラッシュ記号 (/) の欠如。  
  
-   パラメーター名とパラメーター値の間の等号 (=) の欠如。  
  
-   パラメーター名と等号の間の空白文字の存在。  
  
-   構文に指定されていないコンマ (,) またはその他の文字の存在。  
  
 再構築操作が完了した後で、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログにエラーがないか調査します。 ログの既定の場所は C:\Program Files\Microsoft SQL Server\130\Setup Bootstrap\Logs です。 再構築プロセスの結果を含むログ ファイルを探すには、ディレクトリをコマンド プロンプトから Logs フォルダーに変更し、 `findstr /s RebuildDatabase summary*.*`を実行します。 この検索により、システム データベースの再構築結果を含むログ ファイルが検出されます。 ログ ファイルを開き、該当するエラー メッセージがないか調査します。  
  
## <a name="see-also"></a>参照  
 [システム データベース](../../relational-databases/databases/system-databases.md)  
  
  
