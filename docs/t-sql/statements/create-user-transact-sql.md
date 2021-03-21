---
description: CREATE USER (Transact-SQL)
title: CREATE USER (Transact-SQL)
ms.custom: ''
ms.date: 11/06/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- WITHOUT_LOGIN_TSQL
- CREATE_USER_TSQL
- SQL13.SWB.DATABASEUSER.OWNEDSCHEMAS.F1
- WITHOUT LOGIN
- CREATE USER
- ALLOW_ENCRYPTED_VALUE_MODIFICATIONS
- ALLOW_ENCRYPTED_VALUE_MODIFICATIONS_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- adding users
- WITHOUT LOGIN [SQL Server]
- CREATE USER statement
- database user additions [SQL Server]
- USER WITHOUT LOGIN [SQL Server]
- users [SQL Server], adding
- users [SQL Server]
ms.assetid: 01de7476-4b25-4d58-85b7-1118fe64aa80
author: VanMSFT
ms.author: vanto
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 19b2959a977f4fdd96efff6f912da8f8bd682664
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104754362"
---
# <a name="create-user-transact-sql"></a>CREATE USER (Transact-SQL)

[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  現在のデータベースにユーザーを追加します。 12 種類のユーザーの一覧を、最も基本的な構文のサンプルと共に、次に示します。  
  
**master 内のログインに基づくユーザー** - これは最も一般的な種類のユーザーです。  
  
-   Windows Active Directory アカウントに基づくログインに基づくユーザー。 `CREATE USER [Contoso\Fritz];`     
-   Windows グループに基づくログインに基づくユーザー。 `CREATE USER [Contoso\Sales];`   
-   [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 認証を使用したログインに基づくユーザー。 `CREATE USER Mary;`  
  
**データベースで認証されるユーザー** - データベースの移植性を高めるために推奨されます。  
 [!INCLUDE[ssSDS_md](../../includes/sssds-md.md)] で常に許可されます。 [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] の包含データベースでのみ許可されます。  
  
-   ログインのない Windows ユーザーに基づくユーザー。 `CREATE USER [Contoso\Fritz];`    
-   ログインのない Windows グループに基づくユーザー。 `CREATE USER [Contoso\Sales];`  
-   Azure Active Directory ユーザーに基づく [!INCLUDE[ssSDS](../../includes/sssds-md.md)] または [!INCLUDE[ssSDW_md](../../includes/sssdw-md.md)] のユーザー。 `CREATE USER [Fritz@contoso.com] FROM EXTERNAL PROVIDER;`     

-   パスワードを持つ包含データベース ユーザー。 ([!INCLUDE[ssSDW_md](../../includes/sssdw-md.md)] では使用できません。) `CREATE USER Mary WITH PASSWORD = '********';`   
  
**Windows グループ ログインを介して接続する Windows プリンシパルに基づくユーザー**  
  
-   ログインのない Windows ユーザーに基づくユーザーでありながら、Windows グループのメンバーシップを介して[!INCLUDE[ssDE](../../includes/ssde-md.md)]に接続できるユーザー。 `CREATE USER [Contoso\Fritz];`  
  
-   ログインのない Windows グループに基づくユーザーでありながら、別の Windows グループのメンバーシップを介して[!INCLUDE[ssDE](../../includes/ssde-md.md)]に接続できるユーザー。 `CREATE USER [Contoso\Fritz];`  
  
**認証できないユーザー** - これらのユーザーは [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] または [!INCLUDE[ssSDS](../../includes/sssds-md.md)] にログインできません。  
  
-   ログインのないユーザー。 ログインできませんが、権限の付与対象となります。 `CREATE USER CustomApp WITHOUT LOGIN;`    
-   証明書に基づくユーザー。 ログインできませんが、権限の付与対象となり、モジュールに署名できます。 `CREATE USER TestProcess FOR CERTIFICATE CarnationProduction50;`  
-   非対称キーに基づくユーザー。 ログインできませんが、権限の付与対象となり、モジュールに署名できます。 `CREATE User TestProcess FROM ASYMMETRIC KEY PacificSales09;`   
 
![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql
-- Syntax for SQL Server, Azure SQL Database, and Azure SQL Managed Instance
  
-- Syntax Users based on logins in master  
CREATE USER user_name   
    [   
        { FOR | FROM } LOGIN login_name   
    ]  
    [ WITH <limited_options_list> [ ,... ] ]   
[ ; ]  
  
-- Users that authenticate at the database  
CREATE USER   
    {  
      windows_principal [ WITH <options_list> [ ,... ] ]  
  
    | user_name WITH PASSWORD = 'password' [ , <options_list> [ ,... ]   
    | Azure_Active_Directory_principal FROM EXTERNAL PROVIDER   
    }  
  
 [ ; ]  
  
-- Users based on Windows principals that connect through Windows group logins  
CREATE USER   
    {   
          windows_principal [ { FOR | FROM } LOGIN windows_principal ]  
        | user_name { FOR | FROM } LOGIN windows_principal  
}  
    [ WITH <limited_options_list> [ ,... ] ]   
[ ; ]  
  
-- Users that cannot authenticate   
CREATE USER user_name   
    {  
         WITHOUT LOGIN [ WITH <limited_options_list> [ ,... ] ]  
       | { FOR | FROM } CERTIFICATE cert_name   
       | { FOR | FROM } ASYMMETRIC KEY asym_key_name   
    }  
 [ ; ]  
  
<options_list> ::=  
      DEFAULT_SCHEMA = schema_name  
    | DEFAULT_LANGUAGE = { NONE | lcid | language name | language alias }  
    | SID = sid   
    | ALLOW_ENCRYPTED_VALUE_MODIFICATIONS = [ ON | OFF ] ]  
  
<limited_options_list> ::=  
      DEFAULT_SCHEMA = schema_name ]   
    | ALLOW_ENCRYPTED_VALUE_MODIFICATIONS = [ ON | OFF ] ]  
  
-- SQL Database syntax when connected to a federation member  
CREATE USER user_name  
[;]

-- Syntax for users based on Azure AD logins for Azure SQL Managed Instance
CREATE USER user_name   
    [   { FOR | FROM } LOGIN login_name  ]  
    | FROM EXTERNAL PROVIDER
    [ WITH <limited_options_list> [ ,... ] ]   
[ ; ]  

<limited_options_list> ::=  
      DEFAULT_SCHEMA = schema_name 
    | DEFAULT_LANGUAGE = { NONE | lcid | language name | language alias }   
    | ALLOW_ENCRYPTED_VALUE_MODIFICATIONS = [ ON | OFF ] ] 
```

> [!NOTE]
> 作成後の Azure SQL Managed Instance 機能の Azure AD 管理者が変更されました。 詳しくは、「[マネージド インスタンス用の新しい Azure AD 管理機能](/azure/sql-database/sql-database-aad-authentication-configure#new-azure-ad-admin-functionality-for-mi)」をご覧ください。

```syntaxsql
-- Syntax for Azure Synapse Analytics  
  
CREATE USER user_name   
    [ { { FOR | FROM } { LOGIN login_name }   
      | WITHOUT LOGIN  
    ]   
    [ WITH DEFAULT_SCHEMA = schema_name ]  
[;]

CREATE USER Azure_Active_Directory_principal FROM EXTERNAL PROVIDER  
    [ WITH DEFAULT_SCHEMA = schema_name ]  
[;]
``` 
  
```syntaxsql
-- Syntax for Parallel Data Warehouse  
  
CREATE USER user_name   
    [ { { FOR | FROM }  
      {   
        LOGIN login_name   
      }   
      | WITHOUT LOGIN  
    ]   
    [ WITH DEFAULT_SCHEMA = schema_name ]  
[;]  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
 *user_name*  
 データベース内でユーザーを識別する名前を指定します。 *user_name* は、**sysname** です。 半角 128 文字まで指定できます。 Windows プリンシパルに基づいてユーザーを作成する場合、別のユーザー名を指定しないと、Windows プリンシパル名がユーザー名になります。  
  
 LOGIN *login_name*  
 作成するデータベース ユーザーのログインを指定します。 *login_name* は、サーバーで有効なログインである必要があります。 Windows プリンシパルに基づくログイン (ユーザーまたはグループ) か、または [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 認証を使用したログインを指定できます。 この SQL Server ログインをデータベースに対して入力すると、データベースでは、作成されるデータベース ユーザーの名前と ID が取得されます。 Windows プリンシパルからマップされたログインを作成する場合は、 **[** _\<domainName\>_ **\\** _\<loginName\>_ **]** という形式を使用します。 例については、「[構文の概要](#SyntaxSummary)」を参照してください。  
  
 CREATE USER ステートメントが SQL のバッチ内の唯一のステートメントである場合、Azure SQL Database では WITH LOGIN 句がサポートされます。 CREATE USER ステートメントが SQL のバッチ内の唯一のステートメントではない場合、または動的 SQL 内で実行される場合、WITH LOGIN 句はサポートされません。  
  
 WITH DEFAULT_SCHEMA = *schema_name*  
 このデータベース ユーザー用のオブジェクトの名前を解決するときに、サーバーで最初に検索されるスキーマを指定します。  
  
 '*windows_principal*'  
 データベース ユーザーを作成する Windows プリンシパルを指定します。 *windows_principal* には、Windows ユーザーまたは Windows グループを指定できます。 *windows_principal* がログインを持たない場合でも、ユーザーは作成されます。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] に接続するときに、*windows_principal* がログインを持たない場合、ログインを持つ Windows グループのメンバーシップを介して [!INCLUDE[ssDE](../../includes/ssde-md.md)] で Windows プリンシパルを認証するか、または接続文字列で包含データベースを初期カタログとして指定する必要があります。 Windows プリンシパルからユーザーを作成する場合は、 **[** _\<domainName\>_ **\\** _\<loginName\>_ **]** という形式を使用します。 例については、「[構文の概要](#SyntaxSummary)」を参照してください。 Active Directory ユーザーに基づくユーザーは、21 文字未満の名前に制限されます。
  
 '*Azure_Active_Directory_principal*'  
 **適用対象**: [!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)]、[!INCLUDE[ssSDW_md](../../includes/sssdw-md.md)]。  
  
 データベース ユーザーが作成される Azure Active Directory のプリンシパルを指定します。 *Azure_Active_Directory_principal* には、Azure Active Directory ユーザー、Azure Active Directory グループ、Azure Active Directory アプリケーションを指定できます。 (Azure Active Directory ユーザーは、[!INCLUDE[ssSDS](../../includes/sssds-md.md)] に Windows 認証ログインを持つことはできません。データベース ユーザーのみです)。接続文字列では、初期カタログとして、包含データベースを指定する必要があります。

 Azure AD プリンシパルに対して、CREATE USER 構文では次が要求されます。

- Azure AD ユーザー用の Azure AD オブジェクトの UserPrincipalName。

  - `CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;`  
  - `CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;`

- Azure AD のグループと Azure AD アプリケーション用の Azure AD オブジェクトの DisplayName。 "*看護師*" セキュリティ グループがあった場合は、次を使用します。  
  
  - `CREATE USER [Nurses] FROM EXTERNAL PROVIDER;`  
  
 詳細については、[Azure Active Directory 認証を使用した SQL Database への接続](/azure/azure-sql/database/authentication-aad-overview)に関するページを参照してください。  
  
WITH PASSWORD = '*password*'  
 **適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降、[!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)]。  
  
 包含データベースでのみ使用できます。 作成するユーザーのパスワードを指定します。 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降では、保存されたパスワード情報は salt 化パスワードの SHA-512 を使用して計算されます。  
  
WITHOUT LOGIN  
 ユーザーを既存のログインにマップしません。  
  
CERTIFICATE *cert_name*  
 **適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降、[!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)]。  
  
 作成するデータベース ユーザーの証明書を指定します。  
  
ASYMMETRIC KEY *asym_key_name*  
 **適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降、[!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)]。  
  
 作成するデータベース ユーザーの非対称キーを指定します。  
  
DEFAULT_LANGUAGE = *{ NONE \| \<lcid> \| \<language name> \| \<language salias> }*  
 **適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降、[!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)]。  
  
 新しいユーザーの既定の言語を指定します。 ユーザーに対して既定の言語を指定した場合、データベースの既定の言語を後で変更しても、ユーザーの既定の言語は指定した言語のままになります。 既定の言語を指定しない場合、データベースの既定の言語がユーザーの既定の言語として使用されます。 ユーザーに対して既定の言語を指定しない場合、データベースの既定の言語を後で変更すると、ユーザーの既定の言語はデータベースの新しい既定の言語に変更されます。  
  
> [!IMPORTANT]  
>  *DEFAULT_LANGUAGE* は包含データベース ユーザーにのみ使用します。  
  
SID = *sid*  
 **適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降。  
  
 包含データベースにパスワード ([!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 認証) を持つユーザーに対してのみ適用されます。 新しいデータベース ユーザーの SID を指定します。 このオプションを選択しない場合は、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] によって SID が自動的に割り当てられます。 複数のデータベースで同じ ID (SID) を持つユーザーを作成するには SID パラメーターを使用します。 これは、Always On フェールオーバーの準備のために、複数のデータベースにユーザーを作成する場合に役立ちます。 ユーザーの SID を特定するには、sys.database_principals でクエリを実行します。  
  
ALLOW_ENCRYPTED_VALUE_MODIFICATIONS = [ ON | **OFF** ]  
 **適用対象**: [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 以降、[!INCLUDE[ssSDS](../../includes/sssds-md.md)]。  
  
 一括コピー操作でのサーバーの暗号化メタデータ チェックを抑制します。 これによりユーザーは、データを暗号化解除することなく、テーブルまたはデータベース間で暗号化データを一括コピーできます。 既定値は OFF です。  
  
> [!WARNING]  
>  このオプションを不適切に使用すると、データが破損する場合があります。 詳細については、「[Always Encrypted で保護された機微なデータの移行](../../relational-databases/security/encryption/migrate-sensitive-data-protected-by-always-encrypted.md)」を参照してください。  
  
## <a name="remarks"></a>解説  
 FOR LOGIN を省略した場合、新しいデータベース ユーザーは同じユーザー名を持つ [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログインにマップされます。  
  
 既定のスキーマは、このデータベース ユーザー用のオブジェクトの名前を解決するときに、サーバーで最初に検索されるスキーマになります。 特に指定しない限り、このデータベース ユーザーによって作成されたオブジェクトの所有者になるのは、既定のスキーマです。  
  
 ユーザーに既定のスキーマが設定されている場合は、その既定のスキーマが使用されます。 ユーザーに既定のスキーマが指定されておらず、そのユーザーが所属しているグループに既定のスキーマが指定されている場合は、グループの既定のスキーマが使用されます。 ユーザーに既定のスキーマが指定されておらず、そのユーザーが複数のグループに所属している場合、ユーザーの既定のスキーマは、最も小さい principle_id と明示的に設定された既定のスキーマを持つ Windows グループのスキーマになります。 (利用可能な既定のスキーマのいずれかを、使用するスキーマとして明示的に選択することはできません。)ユーザーに対する既定のスキーマを決定できない場合は、**dbo** スキーマが使用されます。  
  
 DEFAULT_SCHEMA は、このオプションが指すスキーマが作成されていなくても設定できます。  
  
 DEFAULT_SCHEMA は、証明書または非対称キーにマップされるユーザーを作成する場合には指定できません。  
  
 ユーザーが固定サーバー ロール sysadmin のメンバーである場合、DEFAULT_SCHEMA の値は無視されます。 固定サーバー ロール sysadmin のすべてのメンバーには、`dbo` の既定のスキーマが割り当てられます。  
  
 WITHOUT LOGIN 句でユーザーを作成する場合、ユーザーは SQL Server ログインにマップされず、 他のデータベースには guest として接続できます。 ログインのないこのユーザーには権限を割り当てることができます。セキュリティ コンテキストがログインのないユーザーに変更されると、元のユーザーはログインのないユーザーの権限を受け取ります。 例については、「[D. ログインのないユーザーを作成して使用する](#withoutLogin)」を参照してください。  
  
 Windows プリンシパルに割り当てられているユーザーにのみ、円記号 ( **\\** ) を含めることができます。
  
 guest ユーザーは各データベース内に既に存在しているため、CREATE USER を使用して guest ユーザーを作成することはできません。 guest ユーザーは、次のように CONNECT 権限を与えることで有効にできます。  
  
```  
GRANT CONNECT TO guest;  
GO  
```  
  
 データベースのユーザーに関する情報については、[sys.database_principals](../../relational-databases/system-catalog-views/sys-database-principals-transact-sql.md) カタログ ビューを参照してください。

SQL Managed Instance でサーバーレベルの Azure AD ログインを作成するために、新しい構文拡張 **FROM EXTERNAL PROVIDER** を利用できます。 Azure AD ログインでは、データベースレベルの Azure AD プリンシパルをサーバーレベルの Azure AD ログインにマッピングできます。 Azure AD ログインから Azure AD ユーザーを作成するには、次の構文を使用します。

`CREATE USER [AAD_principal] FROM LOGIN [Azure AD login]`

SQL Managed Instance データベースでユーザーを作成するとき、login_name が既存の Azure AD ログインに一致する必要があります。一致しない場合、**FROM EXTERNAL PROVIDER** 句を使用したとき、マスター データベースのログインなしで Azure AD ユーザーのみが作成されます。 たとえば、このコマンドでは、包含ユーザーが作成されます。

`CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER`
  
##  <a name="syntax-summary"></a><a name="SyntaxSummary"></a> 構文の概要  
 **master 内のログインに基づくユーザー**  
  
 ログインに基づくユーザーに関して使用できる構文を次に示します。 既定のスキーマ オプションは除外しています。  
  
-   `CREATE USER [Domain1\WindowsUserBarry]`  
-   `CREATE USER [Domain1\WindowsUserBarry] FOR LOGIN Domain1\WindowsUserBarry`  
-   `CREATE USER [Domain1\WindowsUserBarry] FROM LOGIN Domain1\WindowsUserBarry`  
-   `CREATE USER [Domain1\WindowsGroupManagers]`  
-   `CREATE USER [Domain1\WindowsGroupManagers] FOR LOGIN [Domain1\WindowsGroupManagers]`  
-   `CREATE USER [Domain1\WindowsGroupManagers] FROM LOGIN [Domain1\WindowsGroupManagers]`  
-   `CREATE USER SQLAUTHLOGIN`  
-   `CREATE USER SQLAUTHLOGIN FOR LOGIN SQLAUTHLOGIN`  
-   `CREATE USER SQLAUTHLOGIN FROM LOGIN SQLAUTHLOGIN`  
  
**データベースで認証されるユーザー**  
  
 包含データベースでのみ使用できるユーザーに関して使用できる構文を次に示します。 作成されるユーザーは、**master** データベース内のどのログインにも関連付けられません。 既定のスキーマおよび言語オプションは除外しています。  
  
> [!IMPORTANT]  
>  この構文では、データベースへのアクセス許可と[!INCLUDE[ssDE](../../includes/ssde-md.md)]への新しいアクセス許可がユーザーに与えられます。  
  
-   `CREATE USER [Domain1\WindowsUserBarry]`  
-   `CREATE USER [Domain1\WindowsGroupManagers]`  
-   `CREATE USER Barry WITH PASSWORD = 'sdjklalie8rew8337!$d'`  
  
**master にログインのない Windows プリンシパルに基づくユーザー**  
  
 Windows グループを介して [!INCLUDE[ssDE](../../includes/ssde-md.md)] にアクセスできる一方で、**master** にログインを持たないユーザーが使用できる構文を次に示します。 この構文は、あらゆる種類のデータベースで使用できます。 既定のスキーマおよび言語オプションは除外しています。  
  
 この構文は master 内のログインに基づくユーザーの構文と似ていますが、このカテゴリのユーザーは master 内にログインを持ちません。 ユーザーは、Windows グループ ログインを介して[!INCLUDE[ssDE](../../includes/ssde-md.md)]にアクセスする許可が与えられている必要があります。  
  
 この構文は Windows プリンシパルに基づく包含データベース ユーザーの構文に似ていますが、このカテゴリのユーザーには[!INCLUDE[ssDE](../../includes/ssde-md.md)]への新しいアクセス許可は与えられません。  
  
-   `CREATE USER [Domain1\WindowsUserBarry]`  
-   `CREATE USER [Domain1\WindowsUserBarry] FOR LOGIN Domain1\WindowsUserBarry`  
-   `CREATE USER [Domain1\WindowsUserBarry] FROM LOGIN Domain1\WindowsUserBarry`  
-   `CREATE USER [Domain1\WindowsGroupManagers]`  
-   `CREATE USER [Domain1\WindowsGroupManagers] FOR LOGIN [Domain1\WindowsGroupManagers]`  
-   `CREATE USER [Domain1\WindowsGroupManagers] FROM LOGIN [Domain1\WindowsGroupManagers]`  
  
**認証できないユーザー**  
  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] にログインできないユーザーに関して使用できる構文を次に示します。  
  
-   `CREATE USER RIGHTSHOLDER WITHOUT LOGIN`  
-   `CREATE USER CERTUSER FOR CERTIFICATE SpecialCert`  
-   `CREATE USER CERTUSER FROM CERTIFICATE SpecialCert`  
-   `CREATE USER KEYUSER FOR ASYMMETRIC KEY SecureKey`  
-   `CREATE USER KEYUSER FROM ASYMMETRIC KEY SecureKey`  
  
## <a name="security"></a>セキュリティ  
 ユーザーを作成するとデータベースへのアクセス許可が与えられますが、データベース内のオブジェクトに対するアクセス許可は自動的には与えられません。 ユーザーを作成した後の一般的な操作として、データベース オブジェクトに対する権限を持つデータベース ロールにユーザーを追加するか、またはオブジェクト権限をユーザーに付与します。 権限システムの設計の詳細については、「 [データベース エンジンの権限の概要](../../relational-databases/security/authentication-access/getting-started-with-database-engine-permissions.md)」を参照してください。  
  
### <a name="special-considerations-for-contained-databases"></a>包含データベースに関する注意事項  
 包含データベースに接続するときに、ユーザーが **master** データベース内にログインを持たない場合、接続文字列に包含データベース名を初期カタログとして含める必要があります。 初期カタログ パラメーターは、パスワードを持つ包含データベース ユーザーに対して常に必要になります。  
  
 包含データベースでは、ユーザーを作成することにより、データベースと[!INCLUDE[ssDE](../../includes/ssde-md.md)]のインスタンスとを分離して、データベースを [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の別のインスタンスに簡単に移動できるようになります。 詳細については、「[包含データベース](../../relational-databases/databases/contained-databases.md)」および「[包含データベース ユーザー - データベースの可搬性を確保する](../../relational-databases/security/contained-database-users-making-your-database-portable.md)」を参照してください。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 認証ログインに基づくデータベース ユーザーを、パスワードを持つ包含データベース ユーザーに変更する方法については、「[sp_migrate_user_to_contained &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-migrate-user-to-contained-transact-sql.md)」をご覧ください。  
  
 包含データベースでは、ユーザーは **master** データベース内にログインを持つ必要はありません。 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 管理者は、包含データベースに対するアクセス許可を [!INCLUDE[ssDE](../../includes/ssde-md.md)] レベルではなくデータベース レベルで付与できることを理解する必要があります。 詳細については、「 [Security Best Practices with Contained Databases](../../relational-databases/databases/security-best-practices-with-contained-databases.md)」を参照してください。  
  
 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] で包含データベース ユーザーを使用する場合、サーバー レベルのファイアウォール ルールではなく、データベース レベルのファイアウォール ルールを使用してアクセスを構成します。 詳細については、「[sp_set_database_firewall_rule &#40;Azure SQL Database&#41;](../../relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database.md)」を参照してください。
 
[!INCLUDE[ssSDS_md](../../includes/sssds-md.md)] と [!INCLUDE[ssSDW_md](../../includes/sssdw-md.md)] の包含データベースのユーザーについては、SSMS で多要素認証がサポートされます。 詳細については、[SQL Database と [!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] での Azure AD MFA のための SSMS のサポート](https://azure.microsoft.com/documentation/articles/sql-database-ssms-mfa-authentication/)に関するページを参照してください。  
  
### <a name="permissions"></a>アクセス許可  
 データベースに対する ALTER ANY USER 権限が必要です。  
  
## <a name="examples"></a>例  
  
### <a name="a-creating-a-database-user-based-on-a-sql-server-login"></a>A. SQL Server ログインに基づくデータベース ユーザーを作成する  
 次の例では、最初に `AbolrousHazem` という [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログインを作成し、次に対応するデータベース ユーザー `AbolrousHazem` を `AdventureWorks2012` に作成します。  
  
```sql  
CREATE LOGIN AbolrousHazem   
    WITH PASSWORD = '340$Uuxwp7Mcxo7Khy';  
```   
ユーザー データベースに変更します。 たとえば、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では `USE AdventureWorks2012` ステートメントを使用します。 [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] と [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] では、ユーザー データベースへの新しい接続を作成する必要があります。

```sql   
CREATE USER AbolrousHazem FOR LOGIN AbolrousHazem;  
GO   
```  
  
### <a name="b-creating-a-database-user-with-a-default-schema"></a>B. 既定のスキーマでデータベース ユーザーを作成する  
 次の例では、まず `WanidaBenshoof` というサーバー ログインをパスワード付きで作成し、次に対応するデータベース ユーザー `Wanida` を既定のスキーマ `Marketing` で作成します。  
  
```sql  
CREATE LOGIN WanidaBenshoof   
    WITH PASSWORD = '8fdKJl3$nlNv3049jsKK';  
USE AdventureWorks2012;  
CREATE USER Wanida FOR LOGIN WanidaBenshoof   
    WITH DEFAULT_SCHEMA = Marketing;  
GO  
```  
  
### <a name="c-creating-a-database-user-from-a-certificate"></a>C. 証明書からデータベース ユーザーを作成する  
 次の例では、証明書 `JinghaoLiu` からデータベース ユーザー `CarnationProduction50` を作成します。  
  
**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。  
  
```sql  
USE AdventureWorks2012;  
CREATE CERTIFICATE CarnationProduction50  
    WITH SUBJECT = 'Carnation Production Facility Supervisors',  
    EXPIRY_DATE = '11/11/2011';  
GO  
CREATE USER JinghaoLiu FOR CERTIFICATE CarnationProduction50;  
GO   
```  
  
###  <a name="d-creating-and-using-a-user-without-a-login"></a><a name="withoutLogin"></a> D. ログインのないユーザーを作成して使用する  
 次の例では、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログインにマップされないデータベース ユーザー `CustomApp` を作成します。 その後、ユーザー `adventure-works\tengiz0` に、`CustomApp` ユーザーの権限を借用する権限を許可します。  
  
```sql  
USE AdventureWorks2012 ;  
CREATE USER CustomApp WITHOUT LOGIN ;  
GRANT IMPERSONATE ON USER::CustomApp TO [adventure-works\tengiz0] ;  
GO   
```  
  
 ユーザー `CustomApp` が `adventure-works\tengiz0` の資格情報を使用するには、次のステートメントを実行します。  
  
```sql  
EXECUTE AS USER = 'CustomApp' ;  
GO  
```  
  
 `adventure-works\tengiz0` の資格情報に戻すには、次のステートメントを実行します。  
  
```sql  
REVERT ;  
GO  
```  
  
### <a name="e-creating-a-contained-database-user-with-password"></a>E. パスワードを持つ包含データベース ユーザーを作成する  
 次の例では、パスワードを持つ包含データベース ユーザーを作成します。 この例は、包含データベースでのみ実行できます。  
  
**適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降。 この例は DEFAULT_LANGUAGE が削除された場合に [!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)] で機能します。  
  
```sql  
USE AdventureWorks2012 ;  
GO  
CREATE USER Carlo  
WITH PASSWORD='RN92piTCh%$!~3K9844 Bl*'  
    , DEFAULT_LANGUAGE=[Brazilian]  
    , DEFAULT_SCHEMA=[dbo]  
GO   
```  
  
### <a name="f-creating-a-contained-database-user-for-a-domain-login"></a>F. ドメイン ログインのための包含データベース ユーザーを作成する  
 次の例では、Contoso という名前のドメインの Fritz という名前のログインに対する包含データベース ユーザーを作成します。 この例は、包含データベースでのみ実行できます。  
  
**適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降。  
  
```sql  
USE AdventureWorks2012 ;  
GO  
CREATE USER [Contoso\Fritz] ;  
GO   
```  
  
### <a name="g-creating-a-contained-database-user-with-a-specific-sid"></a>G. 特定の SID を持つ包含データベース ユーザーを作成する  
 次の例では、CarmenW という名前の、SQL Server 認証を使用する包含データベース ユーザーを作成します。 この例は、包含データベースでのみ実行できます。  
  
**適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降。  
  
```sql  
USE AdventureWorks2012 ;  
GO  
CREATE USER CarmenW WITH PASSWORD = 'a8ea v*(Rd##+'  
, SID = 0x01050000000000090300000063FF0451A9E7664BA705B10E37DDC4B7;
```  
  
### <a name="h-creating-a-user-to-copy-encrypted-data"></a>H. 暗号化されたデータをコピーするためのユーザーを作成する  
 以下の例では、Always Encrypted 機能によって保護されたデータを、暗号化された列を含む一連のテーブルから、(同じまたは異なるデータベースに) 暗号化された列を持つ別の一連のテーブルにコピーできるユーザーを作成します。  詳細については、「[Always Encrypted で保護された機微なデータの移行](../../relational-databases/security/encryption/migrate-sensitive-data-protected-by-always-encrypted.md)」を参照してください。  
  
**適用対象**: [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 以降、[!INCLUDE[ssSDS](../../includes/sssds-md.md)]。  
  
```sql  
CREATE USER [Chin]   
WITH   
      DEFAULT_SCHEMA = dbo  
    , ALLOW_ENCRYPTED_VALUE_MODIFICATIONS = ON ;  
```

### <a name="i-create-an-azure-ad-user-from-an-azure-ad-login-in-sql-managed-instance"></a>I. SQL Managed Instance 内で Azure AD ログインから Azure AD ユーザーを作成する

 Azure AD ログインから Azure AD ユーザーを作成するには、次の構文を使用します。

 `sysadmin` ロールで付与された Azure AD ログインを使用してマネージド インスタンスにサインインします。 次の命令文ではログイン bob@contoso.com から Azure AD ユーザー bob@contoso.com が作成されます。 このログインは [CREATE LOGIN](./create-login-transact-sql.md#examples) 例で作成されました。

```sql
CREATE USER [bob@contoso.com] FROM LOGIN [bob@contoso.com];
GO
```

> [!IMPORTANT]
> Azure AD ログインから **USER** を作成するとき、**LOGIN** から同じ *login_name* として *user_name* を指定します。

グループである Azure AD ログインからグループとして Azure AD ユーザーを作成できます。

```sql
CREATE USER [AAD group] FROM LOGIN [AAD group];
GO
```

また、グループである Azure AD ログインから Azure AD ユーザーを作成できます。

```sql
CREATE USER [bob@contoso.com] FROM LOGIN [AAD group];
GO
```

### <a name="j-create-an-azure-ad-user-without-an-aad-login-for-the-database"></a>J. データベースの AAD ログインなしで Azure AD ユーザーを作成する

次の構文は、SQL Managed Instance データベース内で Azure AD ユーザー bob@contoso.com を作成するために使用されます (包含ユーザー)。

```sql
CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
GO
```

## <a name="next-steps"></a>次のステップ  
ユーザーを作成したら、[ALTER ROLE](../../t-sql/statements/alter-role-transact-sql.md) ステートメントを使用して、ユーザーをデータベース ロールに追加することを検討します。  
ロールに [GRANT (オブジェクト権限の許可)](../../t-sql/statements/grant-object-permissions-transact-sql.md) を適用して、テーブルにアクセスできるようにすることもできます。 SQL Server セキュリティ モデルの概要については、[権限](../../relational-databases/security/permissions-database-engine.md)に関するページを参照してください。   
  
## <a name="see-also"></a>参照  
 [データベース ユーザーの作成](../../relational-databases/security/authentication-access/create-a-database-user.md)   
 [sys.database_principals &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-database-principals-transact-sql.md)   
 [ALTER USER &#40;Transact-SQL&#41;](../../t-sql/statements/alter-user-transact-sql.md)   
 [DROP USER &#40;Transact-SQL&#41;](../../t-sql/statements/drop-user-transact-sql.md)   
 [CREATE LOGIN &#40;Transact-SQL&#41;](../../t-sql/statements/create-login-transact-sql.md)   
 [EVENTDATA &#40;Transact-SQL&#41;](../../t-sql/functions/eventdata-transact-sql.md)   
 [包含データベース](../../relational-databases/databases/contained-databases.md)   
 [Azure Active Directory の認証を使用して、SQL データベースに接続する](/azure/azure-sql/database/authentication-aad-overview)   
 [データベース エンジンの権限の概要](../../relational-databases/security/authentication-access/getting-started-with-database-engine-permissions.md)