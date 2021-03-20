---
title: ログインの作成 | Microsoft Docs
description: SQL Server Management Studio または Transact-SQL を使用して SQL Server または Azure SQL Database でログインを作成する方法について説明します。
ms.custom: ''
ms.date: 08/01/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: security
ms.topic: conceptual
f1_keywords:
- sql13.swb.login.status.f1
- sql13.swb.login.effectivepermissions.f1
- sql13.swb.login.general.f1
- sql13.swb.login.databaseaccess.f1
- sql13.swb.login.serverroles.f1
helpviewer_keywords:
- authentication [SQL Server], logins
- logins [SQL Server], creating
- creating logins with Management Studio
- Create login [SQL Server]
- SQL Server logins
ms.assetid: fb163e47-1546-4682-abaa-8c9494e9ddc7
author: VanMSFT
ms.author: vanto
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 25abb0069aa14105f27c581614917a11a83ea127
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104755902"
---
# <a name="create-a-login"></a>ログインの作成
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  このトピックでは、 [!INCLUDE[ssnoversion](../../../includes/ssnoversion-md.md)] または [!INCLUDE[ssSDS](../../../includes/sssds-md.md)] を使用して、 [!INCLUDE[ssManStudioFull](../../../includes/ssmanstudiofull-md.md)] または [!INCLUDE[tsql](../../../includes/tsql-md.md)]にログインを作成する方法について説明します ログインとは、 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]のインスタンスに接続しようとしている人またはプロセスの ID を指します。  
  
##  <a name="background"></a><a name="Background"></a> 背景情報  
 ログインは、セキュリティ プリンシパル、またはセキュリティで保護されたシステムで認証できるエンティティです。 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]に接続するためには、ユーザーにログインが必要です。 Windows プリンシパル (ドメイン ユーザーや Windows ドメイン グループなど) に基づいてログインを作成することも、Windows プリンシパルに基づかないログイン ( [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] ログインなど) を作成することもできます。  
  
> **注:** [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 認証を使用するためには、[!INCLUDE[ssDE](../../../includes/ssde-md.md)]に混合モード認証が使用されている必要があります。 詳細については、「 [認証モードの選択](../../../relational-databases/security/choose-an-authentication-mode.md)」を参照してください。  
  
 セキュリティ プリンシパルとして、ログインには権限を許可することができます。 ログインのスコープは、 [!INCLUDE[ssDE](../../../includes/ssde-md.md)]全体です。 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]のインスタンス上の特定のデータベースに接続するには、データベース ユーザーにログインをマップする必要があります。 データベース内の権限を許可したり拒否したりする際に、その対象となるのは、ログインではなく、データベース ユーザーです。 ログインには、 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] のインスタンス全体をスコープとして持つ権限 ( **CREATE ENDPOINT** 権限など) を許可することができます。  
  
> **注:** ログインが [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] に接続するとき、その ID がマスター データベースで検証されます。 包含データベース ユーザーを使用して [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] と [!INCLUDE[ssSDS](../../../includes/sssds-md.md)] のデータベース レベルでの接続が認証されます。 包含データベース ユーザーを使用する場合、ログインは必要ありません。 包含データベースは、他のデータベース、およびデータベースをホストする [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]/ [!INCLUDE[ssSDS](../../../includes/sssds-md.md)] (および master データベース) のインスタンスから分離されたデータベースです。 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] では、Windows 認証と [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 認証の両方で包含データベース  ユーザーがサポートされます。 [!INCLUDE[ssSDS](../../../includes/sssds-md.md)]を使用して、包含データベース ユーザーとデータベース レベルのファイアウォール規則を結合します。 詳細については、「 [包含データベース ユーザー - データベースの可搬性を確保する](../../../relational-databases/security/contained-database-users-making-your-database-portable.md)」を参照してください。  
  
##  <a name="security"></a><a name="Security"></a> セキュリティ  

 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] サーバーに対する **ALTER ANY LOGIN** 権限または **ALTER LOGIN** 権限が必要です。  
  
 [!INCLUDE[ssSDS](../../../includes/sssds-md.md)]**loginmanager** ロールのメンバーシップが必要です。  
  
##  <a name="create-a-login-using-ssms"></a><a name="SSMSProcedure"></a> SSMS を使用してログインを作成する  
  
  
1.  オブジェクト エクスプローラーで、新しいログインを作成するサーバー インスタンスのフォルダーを展開します。  
  
2.  **[セキュリティ]** フォルダーを右クリックし、 **[新規作成]** をポイントして、 **[ログイン]** をクリックします。  
  
3.  **[ログイン - 新規作成]** ダイアログ ボックスの **[全般]** ページで、 **[ログイン名]** ボックスにユーザーの名前を入力します。 または、 **[検索]** をクリックして **[ユーザーまたはグループの選択]** ダイアログ ボックスを開きます。  
  
     **[検索]** をクリックした場合:  
  
    1.  **[このオブジェクトの種類を選択してください]** で、 **[オブジェクトの種類]** をクリックして **[オブジェクトの種類]** ダイアログ ボックスを開き、 **[ビルトイン セキュリティ プリンシパル]** 、 **[グループ]** 、 **[ユーザー]** のいずれかまたはすべてを選択します。 **[ビルトイン セキュリティ プリンシパル]** と **[ユーザー]** が既定で選択されます。 完了したら、 **[OK]** をクリックします。  
  
    2.  **[場所を指定してください]** で、 **[場所]** をクリックして **[場所]** ダイアログ ボックスを開き、使用可能なサーバー場所の 1 つを選択します。 完了したら、 **[OK]** をクリックします。  
  
    3.  **[選択するオブジェクト名を入力してください (例)]** で、検索するユーザー名またはグループ名を入力します。 詳細については、「 [[ユーザー、コンピューターまたはグループの選択] ダイアログ ボックス](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771712(v=ws.11))」を参照してください。  
  
    4.  詳細検索オプションを表示するには **[詳細]** をクリックします。 詳細については、「 [[ユーザー、コンピューターまたはグループの選択] ダイアログ ボックス - [詳細設定] ページ](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc733110(v=ws.11))」を参照してください。  
  
    5.  **[OK]** をクリックします。  
  
4.  Windows プリンシパル上に基づいてログインを作成するには、 **[Windows 認証]** を選択します。 これは既定値です。  
  
5.  [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] データベースに保存されるログインを作成するには、 **[SQL Server 認証]** を選択します。  
  
    1.  **[パスワード]** ボックスに、新しいユーザーのパスワードを入力します。 **[パスワードの確認]** ボックスに、パスワードを再度入力します。  
  
    2.  既存のパスワードを変更する場合は、 **[古いパスワードを指定する]** を選択し、古いパスワードを **[古いパスワード]** ボックスに入力します。  
  
    3.  複雑さと施行のパスワード ポリシー オプションを適用するには、 **[パスワード ポリシーを適用する]** を選択します。 詳細については、「 [Password Policy](../../../relational-databases/security/password-policy.md)」をご参照ください。 **[SQL Server 認証]** が選択されている場合、これは既定のオプションです。  
  
    4.  失効に関するパスワード ポリシー オプションを適用するには、 **[パスワードの期限を適用する]** を選択します。 このチェック ボックスをオンにする場合は、 **[パスワード ポリシーを適用する]** がオンになっている必要があります。 **[SQL Server 認証]** が選択されている場合、これは既定のオプションです。  
  
    5.  ユーザーにログインの初回使用後に新しいパスワードの作成を強制するには、 **[ユーザーは次回ログイン時にパスワードを変更する]** を選択します。 このチェック ボックスをオンにする場合は、 **[パスワードの期限を適用する]** がオンになっている必要があります。 **[SQL Server 認証]** が選択されている場合、これは既定のオプションです。  
  
6.  ログインをスタンドアロン セキュリティ証明書に関連付けるには、 **[証明書にマップ済み]** を選択し、一覧から既存の証明書の名前を選択します。  
  
7.  ログインをスタンドアロン非対称キーに関連付けるには、 **[非対称キーにマップ済み]** を選択し、一覧から既存のキーの名前を選択します。  
  
8.  ログインをセキュリティ資格情報に関連付けるには、 **[資格情報にマップ済み]** チェック ボックスをオンにし、一覧から既存の資格情報を選択するか、 **[追加]** をクリックして新しい資格情報を作成します。 ログインからセキュリティ資格情報へのマッピングを削除するには、 **[マップされた資格情報]** から資格情報を選択し、 **[削除]** をクリックします。 全般的な資格情報の詳細については、「[資格情報 &#40;データベース エンジン&#41;](../../../relational-databases/security/authentication-access/credentials-database-engine.md)」を参照してください。  
  
9. **[既定のデータベース]** の一覧から、ログインの既定のデータベースを選択します。 **[マスター]** はこのオプションの既定値です。  
  
10. **[既定の言語]** の一覧から、ログインの既定の言語を選択します。  
  
11. [!INCLUDE[clickOK](../../../includes/clickok-md.md)]  

### <a name="additional-options"></a>追加オプション  
 **[ログイン - 新規作成]** ダイアログ ボックスでは、 **[サーバー ロール]** 、 **[ユーザー マッピング]** 、 **[セキュリティ保護可能なリソース]** 、 **[状態]** の 4 つの追加ページにもオプションがあります。  
  
### <a name="server-roles"></a>[サーバー ロール]  
 **[サーバー ロール]** ページには、新しいログインに割り当てることができるすべての可能なロールが一覧表示されます。 次のオプションを使用できます。  
  
 **[bulkadmin]** チェック ボックス  
 **bulkadmin** 固定サーバー ロールのメンバーは、BULK INSERT ステートメントを実行できます。  
  
 **[dbcreator]** チェック ボックス  
 **dbcreator** 固定サーバー ロールのメンバーは、任意のデータベースを作成、変更、削除、および復元できます。  
  
 **[diskadmin]** チェック ボックス  
 **diskadmin** 固定サーバー ロールのメンバーは、ディスク ファイルを管理できます。  
  
 **[processadmin]** チェック ボックス  
 **processadmin** 固定サーバー ロールのメンバーは、 [!INCLUDE[ssDE](../../../includes/ssde-md.md)]のインスタンスを実行しているプロセスを終了できます。  
  
 **[public]** チェック ボックス  
 すべて SQL Server ユーザー、グループ、およびロールは、既定で **public** 固定サーバー ロールに属します。  
  
 **[securityadmin]** チェック ボックス  
 **securityadmin** 固定サーバー ロールのメンバーは、ログインとログインのプロパティを管理します。 このメンバーは、サーバー レベルの権限を許可、拒否、および禁止できます。 また、データベース レベルの権限も許可、拒否、および禁止できます。 また、 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] ログインのパスワードをリセットできます。  
  
 **[serveradmin]** チェック ボックス  
 **serveradmin** 固定サーバー ロールのメンバーは、サーバー全体の構成オプションを変更したり、サーバーをシャットダウンしたりできます。  
  
 **[setupadmin]** チェック ボックス  
 **setupadmin** 固定サーバー ロールのメンバーは、リンク サーバーを追加および削除でき、一部のシステム ストアド プロシージャを実行することもできます。  
  
 **[sysadmin]** チェック ボックス  
 **sysadmin** 固定サーバー ロールのメンバーは、 [!INCLUDE[ssDE](../../../includes/ssde-md.md)]の任意の動作を実行できます。  
  
### <a name="user-mapping"></a>[ユーザー マッピング]  
 **[ユーザー マッピング]** ページには、すべての可能なデータベースと、ログインに適用できるデータベースに対するデータベース ロール メンバーシップが一覧表示されます。 選択したデータベースによって、ログインに使用できるロールのメンバーシップが決まります。 このページで使用できるオプションを次に示します。  
  
 **[このログインにマップされたユーザー]**  
 このログインでアクセスできるデータベースを選択します。 データベースを選択すると、 **[_database_name_ のデータベース ロール メンバーシップ]** ペインに有効なデータベース ロールが表示されます。  
  
 **Map**  
 下の一覧にあるデータベースへのアクセスを、ログインに許可します。  
  
 **[データベース]**  
 サーバーで利用できるデータベースを一覧表示します。  
  
 **User**  
 ログインにマップするデータベース ユーザーを指定します。 既定では、データベース ユーザーの名前はログインと同じになります。  
  
 **[既定のスキーマ]**  
 ユーザーの既定のスキーマを指定します。 ユーザーが最初に作成されるときの既定のスキーマは、 **dbo** です。 存在しない既定のスキーマを指定することもできます。 Windows グループ、証明書、非対称キーにマップされるユーザーに対して既定のスキーマを指定することはできません。  
  
 **[_database_name_ では guest アカウントが有効]**  
 選択したデータベースで guest アカウントが有効かどうかを示す読み取り専用属性です。 guest アカウントを有効または無効にするには、guest アカウントの **[ログインのプロパティ]** ダイアログ ボックスの **[状態]** ページを使用します。  
  
 **[_database_name_ のデータベース ロール メンバーシップ]**  
 指定されたデータベースにおけるユーザーのロールを選択します。 どのデータベースでも、ユーザーはすべて **public** ロールのメンバーになり、削除できません。 データベース ロールの詳細については、「 [データベース レベルのロール](../../../relational-databases/security/authentication-access/database-level-roles.md)」を参照してください。  
  
### <a name="securables"></a>[セキュリティ保護可能なリソース]  
 **[セキュリティ保護可能なリソース]** ページには、すべてのセキュリティ保護可能なリソースと、ログインに付与できる、セキュリティ保護可能なリソースに対する権限が一覧表示されます。 このページで使用できるオプションを次に示します。  
  
 **上のグリッド**  
 権限を設定できるアイテムが 1 つ以上表示されます。 上のグリッドに表示される列は、プリンシパルまたはセキュリティ保護可能なリソースによって異なります。  
  
 アイテムを上のグリッドに追加するには:  
  
1.  **[検索]** をクリックします。  
  
2.  **[オブジェクトの追加]** ダイアログ ボックスで、 **[特定のオブジェクト]** 、 **[この種類のすべてのオブジェクト]** 、 **[サーバー**_server\_name]_ のいずれかのオプションを選択します。 [!INCLUDE[clickOK](../../../includes/clickok-md.md)]  
  
    > **注:** **[サーバー** _server\_name]_ を選択すると、そのサーバーのセキュリティ保護可能なすべてのオブジェクトが上のグリッドに自動的に入力されます。  
  
3.  **[特定のオブジェクト]** を選択した場合:  
  
    1.  **[オブジェクトの選択]** ダイアログ ボックスの **[以下のオブジェクトの種類を選択]** で、 **[オブジェクトの種類]** をクリックします。  
  
    2.  **[オブジェクトの種類を選択]** ダイアログ ボックスで、 **[エンドポイント]** 、 **[ログイン]** 、 **[サーバー]** 、 **[可用性グループ]** 、 **[サーバー ロール]** のいずれかまたはすべてをオブジェクトの種類として選択します。 [!INCLUDE[clickOK](../../../includes/clickok-md.md)]  
  
    3.  **[選択するオブジェクト名を入力してください (例)]** で、 **[参照]** をクリックします。  
  
    4.  **[オブジェクトの参照]** ダイアログ ボックスで、 **[オブジェクトの種類を選択]** ダイアログ ボックスで選択した種類の使用可能なオブジェクトを選択し、 **[OK]** をクリックします。  
  
    5.  **[オブジェクトの選択]** ダイアログ ボックスで **[OK]** をクリックします。  
  
4.  **[オブジェクトの種類を選択]** ダイアログ ボックスで **[この種類のすべてのオブジェクト]** を選択した場合は、 **[エンドポイント]** 、 **[ログイン]** 、 **[サーバー]** 、 **[可用性グループ]** 、 **[サーバー ロール]** のいずれかまたはすべてをオブジェクトの種類として選択します。 [!INCLUDE[clickOK](../../../includes/clickok-md.md)]  
  
 **名前**  
 グリッドに追加される各プリンシパルまたはセキュリティ保護可能なリソースの名前です。  
  
 **Type**  
 各アイテムの種類について説明します。  
  
 **[明示的] タブ**  
 上のグリッドで選択されているセキュリティ保護可能なリソースに適用できる権限が表示されます。 すべての明示的な権限に対してすべてのオプションを使用できるわけではありません。  
  
 **アクセス許可**  
 権限の名前です。  
  
 **Grantor**  
 権限を許可したプリンシパルです。  
  
 **Grant**  
 この権限をログインに対して許可する場合はオンにします。 この権限を取り消す場合はオフにします。  
  
 **[許可の有無]**  
 一覧表示された権限に対する WITH GRANT オプションの状態を反映します。 このボックスは読み取り専用です。 この権限を適用するには、 [GRANT](../../../t-sql/statements/grant-transact-sql.md) ステートメントを使用します。  
  
 **Deny**  
 この権限をログインに対して拒否する場合はオンにします。 この権限を取り消す場合はオフにします。  
  
### <a name="status"></a>Status  
 **[状態]** ページには、選択した [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] ログインに対して構成できる認証オプションと承認オプションの一部が表示されます。  
  
 このページで使用できるオプションを次に示します。  
  
 **[データベース エンジンに接続する権限]**  
 この設定を操作するときは、選択したログインを、セキュリティ保護可能なリソースに対する権限を許可または拒否できるプリンシパルと考えます。  
  
 ログインに CONNECT SQL 権限を付与する場合は、 **[許可]** を選択します。 ログインに CONNECT SQL 権限を与えない場合は、 **[拒否]** を選択します。  
  
 **Login**  
 この設定を操作するときは、選択したログインを、テーブル内のレコードと考えます。 ここに一覧表示される値を変更すると、それらの変更がレコードに適用されます。  
  
 無効になっているログインも、レコードとして存在し続けます。 ただし、無効になっているログインから [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]に接続を試みても、そのログインは認証されません。  
  
 このオプションを選択して、このログインを有効または無効にします。 このオプションは、ENABLE オプションまたは DISABLE オプションを指定した ALTER LOGIN ステートメントを実行します。  
  
 **SQL Server 認証**  
 **[ログインをロックアウトする]** チェック ボックスは、選択したログインから [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 認証を使用して接続する場合のみ使用できます。このチェック ボックスは、そのログインがロックアウトされていることを示します。この設定は読み取り専用です。 ロックアウトされたログインのロックを解除するには、UNLOCK オプションを指定して ALTER LOGIN を実行します。  
  
##  <a name="create-a-login-using-windows-authentication-using-t-sql"></a><a name="TsqlProcedure"></a> Windows 認証と T-SQL を使用してログインを作成する  
  
 
1.  **オブジェクト エクスプローラー** で、 [!INCLUDE[ssDE](../../../includes/ssde-md.md)]のインスタンスに接続します。  
  
2.  [標準] ツール バーの **[新しいクエリ]** をクリックします。  
  
3.  次の例をコピーしてクエリ ウィンドウに貼り付け、 **[実行]** をクリックします。  
  
    ```  
    -- Create a login for SQL Server by specifying a server name and a Windows domain account name.  
  
    CREATE LOGIN [<domainName>\<loginName>] FROM WINDOWS;  
    GO  
  
    ```  
  
## <a name="create-a-login-using-sql-server-authentication-using-t-sql"></a>SQL Server 認証と T-SQL を使用してログインを作成する
  
1.  **オブジェクト エクスプローラー** で、 [!INCLUDE[ssDE](../../../includes/ssde-md.md)]のインスタンスに接続します。  
  
2.  [標準] ツール バーの **[新しいクエリ]** をクリックします。  
  
3.  次の例をコピーしてクエリ ウィンドウに貼り付け、 **[実行]** をクリックします。  
  
    ```  
    -- Creates the user "shcooper" for SQL Server using the security credential "RestrictedFaculty"   
    -- The user login starts with the password "Baz1nga," but that password must be changed after the first login.  
  
    CREATE LOGIN shcooper   
       WITH PASSWORD = 'Baz1nga' MUST_CHANGE,  
       CREDENTIAL = RestrictedFaculty;  
    GO  
    ```  
  
 詳細については、「[CREATE LOGIN &#40;Transact-SQL&#41;](../../../t-sql/statements/create-login-transact-sql.md)」を参照してください。  
  
##  <a name="follow-up-steps-to-take-after-you-create-a-login"></a><a name="FollowUp"></a>補足情報: ログインの作成後に実行する手順  
 ログインの作成が済むと、そのログインで [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]に接続できるようになりますが、実際の作業を行うための十分な権限があるとは限りません。 ログインに関して一般的に行われる操作について説明するトピックへのリンクを次に示します。  
  
-   ログインをロールに参加させるには、「 [ロールの追加](../../../relational-databases/security/authentication-access/join-a-role.md)」を参照してください。  
  
-   データベースの使用をログインに承認するには、「 [データベース ユーザーの作成](../../../relational-databases/security/authentication-access/create-a-database-user.md)」を参照してください。  
  
-   ログインに権限を付与するには、「 [プリンシパルに対する権限の許可](../../../relational-databases/security/authentication-access/grant-a-permission-to-a-principal.md)」を参照してください。  
  
## <a name="see-also"></a>参照  
 [SQL Server データベース エンジンと Azure SQL Database のセキュリティ センター](../../../relational-databases/security/security-center-for-sql-server-database-engine-and-azure-sql-database.md)  
  
