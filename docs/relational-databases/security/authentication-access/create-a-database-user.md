---
title: データベース ユーザーの作成 | Microsoft Docs
description: SQL Server Management Studio または Transact-SQL を使用して、最も一般的な種類のデータベース ユーザーを作成する方法について説明します。
ms.custom: ''
ms.date: 04/24/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: security
ms.topic: conceptual
f1_keywords:
- sql13.swb.user.securables.f1
- SQL13.SWB.DATABASEUSER.GENERAL.F1
helpviewer_keywords:
- database users, creating
- creating users with Management Studio
- mapping users
- users [SQL Server], creating
- database user additions [SQL Server]
- database users, mapping
- CREATE USER [Management Studio]
- users [SQL Server], adding
- mapping database users
ms.assetid: 782798d3-9552-4514-9f58-e87be4b264e4
author: VanMSFT
ms.author: vanto
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 4bda735ddb48098b5b1248003b53dd075b602f2e
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104752252"
---
# <a name="create-a-database-user"></a>データベース ユーザーの作成
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  このトピックでは、最も一般的な種類のデータベース ユーザーを作成する方法について説明します。 ユーザーには種類が 7 つあります。 完全な一覧については、「[CREATE USER &#40;Transact-SQL&#41;](../../../t-sql/statements/create-user-transact-sql.md)」を参照してください。 すべての種類の [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] ではデータベース ユーザーがサポートされますが、すべての種類のユーザーがサポートされているとは限りません。  
  
 データベース ユーザーを作成するには、 [!INCLUDE[ssManStudioFull](../../../includes/ssmanstudiofull-md.md)] または [!INCLUDE[tsql](../../../includes/tsql-md.md)]を使用します。  
  
##  <a name="understanding-the-types-of-users"></a><a name="Understanding"></a> ユーザーの種類について  
 [!INCLUDE[ssManStudio](../../../includes/ssmanstudio-md.md)] は、データベース ユーザーを作成するときに 6 つのオプションを表示します。 次の図に、6 つのオプションを緑色のボックスに示し、それらが何を表すかを示します。  
  
 ![さまざまな種類のユーザーについて説明する図。](../../../relational-databases/security/authentication-access/media/typesofusers.png "TypesOfUsers")  
  
### <a name="selecting-the-type-of-user"></a>ユーザーの種類の選択  
 **ログインまたはログインにマップされていないユーザー**  
  
 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]を初めて使用する場合は、作成するユーザーの種類を決定するのが難しい可能性があります。 まず、データベースにアクセスする必要があるユーザーまたはグループがログインを持っているかどうかを確認します。 master データベース内のログインは、 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] を管理するユーザー、および [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]のインスタンスで複数またはすべてのデータベースにアクセスする必要があるユーザーにとっては一般的です。 ここでは、 **ログインを持つ SQL ユーザー** を作成します。 データベース ユーザーは、ログインの ID として、データベースへの接続時に使用されます。 データベース ユーザーとログインには同じ名前を使用できますが、必ずしもその必要はありません。 このトピックは、既存のログインが [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]に存在することを前提としています。 ログインの作成方法の詳細については、「 [ログインの作成](../../../relational-databases/security/authentication-access/create-a-login.md)」を参照してください。  
  
 データベースにアクセスする必要があるユーザーまたはグループがログインを持っていない場合、かつ 1 つまたは複数のデータベースにだけアクセスする必要がある場合は、 **Windows ユーザー** または **パスワードを持つ SQL ユーザー** を作成します。 このユーザーは包含データベース ユーザーとも呼ばれ、master データベース内のログインに関連付けられません。 これは、 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]のインスタンス間でデータベースを簡単に移動できるようにするときに最適な選択肢です。 [!INCLUDE[sssql16-md](../../../includes/sssql16-md.md)]でこのオプションを使用するには、管理者は最初に [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]用の包含データベースを有効にし、データベースの包含を有効にする必要があります。 詳細については、「 [包含データベース ユーザー - データベースの可搬性を確保する](../../../relational-databases/security/contained-database-users-making-your-database-portable.md)」を参照してください。  
  
> **重要:** 包含データベース ユーザーとして接続するときには、接続文字列の一部としてデータベースの名前を指定する必要があります。 [!INCLUDE[ssManStudio](../../../includes/ssmanstudio-md.md)]でデータベースを指定するには、 **[接続先]** ダイアログ ボックスで **[オプション]** をクリックし、 **[接続プロパティ]** タブをクリックします。  
  
 接続するユーザーが Windows で認証できない場合は、 **SQL Server 認証ログイン** に基づいて **[パスワードを持つ SQL ユーザー]** または **[ログインを持つ SQL ユーザー]** を選択します。 これは、組織外のユーザー (例えば、顧客など) が組織の [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]に接続するときに一般的です。  
  
> **ヒント!** 組織内のユーザーの場合は、Windows 認証の方が適切です。これは、別のパスワードを覚える必要がないため、および Windows 認証は Kerberos などの他のセキュリティ機能を提供するためです。  
  
##  <a name="background"></a><a name="Restrictions"></a> 背景情報  
 ユーザーは、データベース レベルのセキュリティ プリンシパルです。 データベースに接続するためには、データベース ユーザーにログインをマップする必要があります。 異なるデータベースには、1 つのログインを異なるユーザーとしてマップすることができますが、各データベースでは 1 人のユーザーとしてのみマップできます。 部分的包含データベースでは、ログインを持たないユーザーを作成できます。 包含データベース ユーザーの詳細については、「[CREATE USER &#40;Transact-SQL&#41;](../../../t-sql/statements/create-user-transact-sql.md)」を参照してください。 データベースで guest ユーザーが有効になっている場合は、データベース ユーザーにマップされていないログインでも、guest ユーザーとしてそのデータベースにアクセスすることができます。  
  
> **重要:** guest ユーザーは無効にするのが一般的です。 必要な場合を除き、guest ユーザーは有効にしないでください。  
  
 セキュリティ プリンシパルとして、ユーザーには権限を許可することができます。 ユーザーのスコープはデータベースです。 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]のインスタンス上の特定のデータベースに接続するには、データベース ユーザーにログインをマップする必要があります。 データベース内の権限を許可したり拒否したりする際に、その対象となるのは、ログインではなく、データベース ユーザーです。  
  
##  <a name="permissions"></a><a name="Permissions"></a> Permissions  
 データベースに対する **ALTER ANY USER** 権限が必要です。  
  
##  <a name="create-a-user-with-ssms"></a><a name="SSMSProcedure"></a> SSMS でユーザーを作成する  
  
 
1.  オブジェクト エクスプローラーで、 **[データベース]** フォルダーを展開します。  
  
2.  新しいデータベース ユーザーを作成するデータベースを展開します。  
  
3.  **[セキュリティ]** フォルダーを右クリックし、 **[新規作成]** をポイントして、 **[ユーザー]** を選択します。  
  
4.  **[データベース ユーザー - 新規]** ダイアログ ボックスの **[全般]** ページで、 **[ユーザーの種類]** ボックスの一覧で次のユーザーの種類のいずれかを選択します。  
  
    -   **ログインを持つ SQL ユーザー**  
  
    -   **パスワードを持つ SQL ユーザー**  
  
    -   **ログインを持たない SQL ユーザー**  
  
    -   **証明書にマップされたユーザー**  
  
    -   **非対称キーにマップされたユーザー**  
  
    -   **Windows ユーザー**  
  
5.  オプションを選択すると、ダイアログ ボックスの残りのオプションを変更できるようになります。 一部のオプションは、特定の種類のデータベース ユーザーにのみ適用されます。 一部のオプションは空白にすることができますが、その場合は既定値が使用されます。  
  
     **ユーザー名**  
     新しいユーザーのユーザー名を入力します。 **[ユーザーの種類]** で一覧から **[Windows ユーザー]** を選択した場合は、省略記号 **[...]** をクリックして、 **[ユーザーまたはグループの選択]** ダイアログ ボックスを開くこともできます。  
  
     **[ログイン名]**  
     ユーザーのログインを入力します。 または、省略記号 **[...]** をクリックして **[ログインの選択]** ダイアログ ボックスを開きます。 **[ログイン名]** は、 **[ユーザーの種類]** で一覧から **[ログインを持つ SQL ユーザー]** または **[Windows ユーザー]** を選択した場合に使用できます。  
  
     **[パスワード]** と **[パスワードの確認入力]**  
     データベースで認証するユーザーのパスワードを入力します。  
  
     **既定の言語**  
     ユーザーの既定の言語を入力します。  
  
     **既定のスキーマ**  
     このユーザーが作成したオブジェクトを所有するスキーマを入力します。 または、省略記号 **[...]** をクリックして **[スキーマの選択]** ダイアログ ボックスを開きます。 **[既定のスキーマ]** は、 **[ユーザーの種類]** で一覧から **[ログインを持つ SQL ユーザー]** 、 **[ログインを持たない SQL ユーザー]** 、または **[Windows ユーザー]** を選択した場合に使用できます。  
  
     **[証明書名]**  
     データベース ユーザーに使用する証明書を入力します。 または、省略記号 **[...]** をクリックして **[証明書の選択]** ダイアログ ボックスを開きます。 **[証明書名]** は、 **[ユーザーの種類]** で一覧から **[証明書にマップされたユーザー]** を選択した場合に使用できます。  
  
     **[非対称キー名]**  
     データベース ユーザーに使用するキーを入力します。 または、省略記号 **[...]** をクリックして **[非対称キーの選択]** ダイアログ ボックスを開きます。 **[非対称キー名]** は、 **[ユーザーの種類]** で一覧から **[非対称キーにマップされたユーザー]** を選択した場合に使用できます。  
  
6.  [!INCLUDE[clickOK](../../../includes/clickok-md.md)]  
  
### <a name="additional-options"></a>追加オプション  
 **[データベース ユーザー - 新規]** ダイアログ ボックスには、次の 4 つの追加ページで使用できるオプションも用意されています。 **[所有されているスキーマ]** 、 **[メンバーシップ]** 、 **[セキュリティ保護可能なリソース]** 、 **[拡張プロパティ]** です。  
  
-   **[所有されているスキーマ]** ページには、新しいデータベース ユーザーが所有できるすべてのスキーマが一覧表示されます。 データベース ユーザーのスキーマを追加または削除するには、 **[このユーザーが所有するスキーマ]** で、スキーマの横のチェック ボックスをオンまたはオフにします。  
  
-   **[メンバーシップ]** ページには、新しいデータベース ユーザーが所有できるすべてのデータベース メンバーシップ ロールが一覧表示されます。 データベース ユーザーのロールを追加または削除するには、 **[データベース ロールのメンバーシップ]** で、ロールの横のチェック ボックスをオンまたはオフにします。  
  
-   **[セキュリティ保護可能なリソース]** ページには、すべてのセキュリティ保護可能なリソースと、ログインに付与できる、セキュリティ保護可能なリソースに対する権限が一覧表示されます。  
  
-   **[拡張プロパティ]** ページでは、カスタム プロパティをデータベース ユーザーに追加できます。 このページで使用できるオプションを次に示します。  
  
     **[データベース]**  
     選択しているデータベースの名前が表示されます。 このフィールドは読み取り専用です。  
  
     **Collation**  
     選択されているデータベースに使用する照合順序を表示します。 このフィールドは読み取り専用です。  
  
     **Properties**  
     オブジェクトの拡張プロパティを表示または指定します。 各拡張プロパティは、オブジェクトに関連付けられたメタデータの名前/値ペアで構成されています。  
  
     **省略記号 (...)**  
     **[値]** の後ろにある省略記号 **[...]** をクリックすると、 **[拡張プロパティの値]** ダイアログ ボックスが開きます。 ここでは、より大きなテキスト ボックスを使用して拡張プロパティの値を入力または表示できます。 詳細については、「 [拡張プロパティの値ダイアログ ボックス](../../databases/value-for-extended-property-dialog-box.md)」を参照してください。  
  
     **削除**  
     選択されている拡張プロパティを削除します。  
  
##  <a name="create-a-user-using-t-sql"></a><a name="TsqlProcedure"></a> T-SQL を使用してユーザーを作成する  
    
1.  **オブジェクト エクスプローラー** で、 [!INCLUDE[ssDE](../../../includes/ssde-md.md)]のインスタンスに接続します。  
  
2.  **[標準]** ツール バーの **[新しいクエリ]** をクリックします。  
  
3.  次の例をコピーしてクエリ ウィンドウに貼り付け、 **[実行]** をクリックします。  
  
    ```  
    -- Creates the login AbolrousHazem with password '340$Uuxwp7Mcxo7Khy'.  
    CREATE LOGIN AbolrousHazem   
        WITH PASSWORD = '340$Uuxwp7Mcxo7Khy';  
    GO  
  
    -- Creates a database user for the login created above.  
    CREATE USER AbolrousHazem FOR LOGIN AbolrousHazem;  
    GO  
    ```  
  
 詳細については、「[CREATE USER &#40;Transact-SQL&#41;](../../../t-sql/statements/create-user-transact-sql.md)」を参照してください。この記事には、さらに多くの [!INCLUDE[tsql](../../../includes/tsql-md.md)] の例が掲載されています。  
  
## <a name="see-also"></a>参照  
 [プリンシパル &#40;データベース エンジン&#41;](../../../relational-databases/security/authentication-access/principals-database-engine.md)   
 [ログインの作成](../../../relational-databases/security/authentication-access/create-a-login.md)   
 [CREATE LOGIN &#40;Transact-SQL&#41;](../../../t-sql/statements/create-login-transact-sql.md)  
  
  
