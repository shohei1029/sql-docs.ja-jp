---
description: CREATE LOGIN (Transact-SQL)
title: CREATE LOGIN (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 07/29/2020
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- CREATE_LOGIN_TSQL
- CREATE LOGIN
- LOGIN_TSQL
- LOGIN
dev_langs:
- TSQL
helpviewer_keywords:
- passwords [SQL Server], logins
- mapping logins [SQL Server]
- logins [SQL Server], creating
- CREATE LOGIN statement
- permissions [SQL Server], logins
- Windows domain accounts [SQL Server]
- re-hashing passwords
- certificates [SQL Server], logins
ms.assetid: eb737149-7c92-4552-946b-91085d8b1b01
author: VanMSFT
ms.author: vanto
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: c105ab80588f469a6b6adaa9c819f9ba6d7c4425
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104755122"
---
# <a name="create-login-transact-sql"></a>CREATE LOGIN (Transact-SQL)

SQL Server、SQL Database、Azure Synapse Analytics、または Analytics Platform System データベースのログインを作成します。 特定のバージョンの構文、引数、注釈、権限、例を表示するには、以下のいずれかのタブをクリックします。

CREATE LOGIN はトランザクションに参加します。 CREATE LOGIN がトランザクション内で実行されており、トランザクションがロールバックされると、ログインの作成がロールバックされます。 トランザクション内で実行された場合、作成されたログインは、トランザクションがコミットされるまで使用できません。

構文表記規則の詳細については、「[Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)」を参照してください。

[!INCLUDE[select-product](../../includes/select-product.md)]

::: moniker range=">=sql-server-2016||>=sql-server-linux-2017"

:::row:::
    :::column:::
        **_\* SQL Server \*_** &nbsp;
    :::column-end:::
    :::column:::
        [Azure SQL Database](create-login-transact-sql.md?view=azuresqldb-current&preserve-view=true)
    :::column-end:::
    :::column:::
        [Azure SQL<br />Managed Instance](create-login-transact-sql.md?view=azuresqldb-mi-current&preserve-view=true)
    :::column-end:::
    :::column:::
        [Azure Synapse<br />Analytics](create-login-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)
    :::column-end:::
    :::column:::
        [Analytics Platform<br />System (PDW)](create-login-transact-sql.md?view=aps-pdw-2016&preserve-view=true)
    :::column-end:::
:::row-end:::

&nbsp;

## <a name="sql-server"></a>SQL Server

## <a name="syntax"></a>構文

```syntaxsql
-- Syntax for SQL Server
CREATE LOGIN login_name { WITH <option_list1> | FROM <sources> }

<option_list1> ::=
    PASSWORD = { 'password' | hashed_password HASHED } [ MUST_CHANGE ]
    [ , <option_list2> [ ,... ] ]

<option_list2> ::=
    SID = sid
    | DEFAULT_DATABASE = database
    | DEFAULT_LANGUAGE = language
    | CHECK_EXPIRATION = { ON | OFF}
    | CHECK_POLICY = { ON | OFF}
    | CREDENTIAL = credential_name

<sources> ::=
    WINDOWS [ WITH <windows_options>[ ,... ] ]
    | CERTIFICATE certname
    | ASYMMETRIC KEY asym_key_name
  
<windows_options> ::=
    DEFAULT_DATABASE = database
    | DEFAULT_LANGUAGE = language
```

## <a name="arguments"></a>引数

*login_name*: 作成するログインの名前を指定します。 ログインには 4 つの種類があります。SQL Server ログイン、Windows ログイン、証明書マッピング ログイン、非対称キー マッピング ログインです。 Windows ドメイン アカウントからマップされるログインを作成する場合は、Windows 2000 よりも前で使用されていた [\<domainName>\\<login_name>] 形式のユーザー ログイン名を使用する必要があります。 login_name@DomainName 形式の UPN は使用できません。 例については、この記事の後半の例 D を参照してください。 認証ログインは **sysname** 型であり、[識別子](../../relational-databases/databases/database-identifiers.md) のルールに従っている必要があります。このログインに " **\\** " を含めることはできません。 Windows ログインには " **\\** " を含めることができます。 Active Directory ユーザーに基づくログインは、21 文字未満の名前に制限されます。

PASSWORD **=** '*password*' SQL Server ログインにのみ適用されます。 作成するログインのパスワードを指定します。 強力なパスワードを使用してください。 詳細については、「[強力なパスワード](../../relational-databases/security/strong-passwords.md)」と「[パスワード ポリシー](../../relational-databases/security/password-policy.md)」を参照してください。 SQL Server 2012 (11.x) 以降では、保存されたパスワード情報は salt 化パスワードの SHA-512 を使用して計算されます。

パスワードでは大文字と小文字が区別されます。 パスワードの長さは 10 文字以上 128 文字以下である必要があります。 パスワードには、a-z、A-Z、0-9 およびほとんどの英数字以外の文字を含めることができます。 パスワードには、単一引用符、または *login_name* を含めることはできません。

PASSWORD **=** *hashed\_password*: HASHED キーワードにのみ適用されます。 作成するログインのパスワードのハッシュ値を指定します。

HASHED SQL Server ログインにのみ適用されます。 PASSWORD 引数の後に入力されたパスワードが、ハッシュ済みであることを示します。 このオプションを選択しなかった場合、パスワードとして入力した文字列は、ハッシュされてからデータベースに格納されます。 このオプションは、あるサーバーから別のサーバーにデータベースを移行する場合にのみ使用してください。 新しいログインを作成する場合は HASHED オプションを使用しないでください。 HASHED オプションは SQL 7 以前で作成されたハッシュでは使用できません。

MUST_CHANGE SQL Server ログインにのみ適用されます。 このオプションが含まれている場合、SQL Server では、新しいログインが最初に使用されたときに、ユーザーに新しいパスワードの入力が求められます。

CREDENTIAL **=** _credential\_name_: 新しい SQL Server ログインにマップする資格情報の名前。 この資格情報は、サーバー内に既に存在している必要があります。 現在このオプションは、資格情報をログインに関連付けるだけです。 資格情報をシステム管理者 (sa) ログインにマップすることはできません。

SID = *sid*: ログインの再作成に使用されます。 Windows 認証ログインではなく、SQL Server 認証ログインにのみ適用されます。 新しい SQL Server 認証ログインの SID を指定します。 このオプションが使用されていない場合、SQL Server で自動的に SID が割り当てられます。 SID 構造体は、SQL Server のバージョンに依存します。 SQL Server ログイン SID: GUID に基づく 16 バイト (**binary (16)** ) のリテラル値。 たとえば、「 `SID = 0x14585E90117152449347750164BA00A7` 」のように入力します。

DEFAULT_DATABASE **=** _database_: ログインに割り当てられる既定のデータベースを指定します。 このオプションを指定しない場合は、既定のデータベースが master に設定されます。

DEFAULT_LANGUAGE **=** _language_: ログインに割り当てられる既定の言語を指定します。 このオプションを指定しない場合は、サーバーの現在の既定の言語が既定の言語になります。 サーバーの既定の言語が将来変更されても、ログインの既定の言語は変更されません。

CHECK_EXPIRATION **=** { ON | **OFF** }: SQL Server ログインにのみ適用されます。 このログインに、パスワードの有効期限ポリシーを適用するかどうかを指定します。 既定値は OFF です。

CHECK_POLICY **=** { **ON** | OFF }: SQL Server ログインにのみ適用されます。 SQL Server を実行しているコンピューターの Windows パスワード ポリシーをこのログインに適用するかどうかを指定します。 既定値は ON です。

Windows のポリシーで強力なパスワードが求められる場合は、次の 4 つの特性のうちの少なくとも 3 つをパスワードに含める必要があります。

- 大文字 (A-Z)。
- 小文字 (a-z)。
- 数字 (0-9)。
- スペース、_、@、*、^、%、!、$、#、& などの英数字以外の文字。

WINDOWS: ログインを Windows ログインにマップするよう指定します。

CERTIFICATE *certname*: ログインに関連付ける証明書の名前を指定します。 この証明書は、master データベース内に既に存在する必要があります。

ASYMMETRIC KEY *asym_key_name*: ログインに関連付ける非対称キーの名前を指定します。 このキーは、master データベース内に既に存在する必要があります。

## <a name="remarks"></a>解説

- パスワードでは大文字と小文字が区別されます。
- パスワードの事前ハッシュは、SQL Server ログインを作成する場合にのみサポートされます。
- MUST_CHANGE が指定された場合、CHECK_EXPIRATION および CHECK_POLICY は ON に設定されなければなりません。 ON に設定しない場合、ステートメントは失敗します。
- CHECK_POLICY = OFF と CHECK_EXPIRATION = ON の組み合わせはサポートされていません。
- CHECK_POLICY を OFF に設定すると、*lockout_time* はリセットされ、CHECK_EXPIRATION は OFF に設定されます。

> [!IMPORTANT]
> CHECK_EXPIRATION および CHECK_POLICY は、Windows Server 2003 以降でのみ適用されます。 詳細については、「 [Password Policy](../../relational-databases/security/password-policy.md)」をご参照ください。

- 証明書または非対称キーから作成されたログインはコード署名用にのみ使用されます。 SQL Server への接続には使用できません。 証明書または非対称キーからログインを作成できるのは、その証明書または非対称キーが master に存在している場合のみです。
- スクリプトでログインを転送する場合は、「[SQL Server 2005 のインスタンス間でログインおよびパスワードを転送する方法](https://support.microsoft.com/kb/918992)」を参照してください。
- ログインを作成すると、自動的に新しいログインが有効になり、ログインにサーバー レベルの **CONNECT SQL** 権限が与えられます。
- アクセスを許可するにはサーバーの[認証モード](../../relational-databases/security/choose-an-authentication-mode.md)がログインの種類に一致する必要があります。
- 権限システムの設計の詳細については、「 [データベース エンジンの権限の概要](../../relational-databases/security/authentication-access/getting-started-with-database-engine-permissions.md)」を参照してください。

## <a name="permissions"></a>アクセス許可

- ログインを作成できるのは、サーバーに対する **ALTER ANY LOGIN** 権限、または **securityadmin** 固定サーバー ロールのメンバーシップを持つユーザーのみとなります。 詳細については、[サーバー レベルのロール](/azure/sql-database/sql-database-manage-logins#groups-and-roles)と [ALTER SERVER ROLE](../../t-sql/statements/alter-server-role-transact-sql.md) に関するページを参照してください。
- **CREDENTIAL** オプションを使用する場合は、サーバーに対する **ALTER ANY CREDENTIAL** 権限も必要です。

## <a name="after-creating-a-login"></a>ログインを作成した後

ログインが作成されたら、ログインは SQL Server に接続できますが、**public** ロールに与えられた権限しか持ちません。 次の操作のいくつかを実行することを検討してください。

- データベースに接続するには、ログイン用のデータベース ユーザーを作成する必要があります。 詳細については、「[CREATE USER](../../t-sql/statements/create-user-transact-sql.md)」を参照してください。
- [CREATE SERVER ROLE](../../t-sql/statements/create-server-role-transact-sql.md) を使用して、ユーザー定義サーバー ロールを作成します。 **ALTER SERVER ROLE** ... ADD MEMBER を使用して、**メンバーの追加** をユーザー定義サーバー ロールに、新しいログインを追加します。 詳細については、[CREATE SERVER ROLE](../../t-sql/statements/create-server-role-transact-sql.md) と [ALTER SERVER ROLE](../../t-sql/statements/alter-server-role-transact-sql.md) に関するページを参照してください。
- 固定サーバー ロールにログインを追加するには、**sp_addsrvrolemember** を使用します。 詳細については、「[サーバー レベルのロール](../../relational-databases/security/authentication-access/server-level-roles.md)」と「[sp_addsrvrolemember](../../relational-databases/system-stored-procedures/sp-addsrvrolemember-transact-sql.md)」を参照してください。
- 新しいログインまたはログインを含むロールにサーバー レベルの権限を許可するには、**GRANT** ステートメントを使用します。 詳細については、「[GRANT](../../t-sql/statements/grant-transact-sql.md)」を参照してください。

## <a name="examples"></a>例

### <a name="a-creating-a-login-with-a-password"></a>A. パスワード付きのログインを作成する

次の例では、特定のユーザーのログインを作成し、パスワードを割り当てます。

```sql
CREATE LOGIN <login_name> WITH PASSWORD = '<enterStrongPasswordHere>';
GO
```

### <a name="b-creating-a-login-with-a-password-that-must-be-changed"></a>B. 変更する必要のあるパスワード付きのログインを作成する

次の例では、特定のユーザーのログインを作成し、パスワードを割り当てます。 `MUST_CHANGE` オプションが指定されているため、ユーザーは、最初にサーバーに接続するときにこのパスワードを変更する必要があります。

**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。

```sql
CREATE LOGIN <login_name> WITH PASSWORD = '<enterStrongPasswordHere>'
    MUST_CHANGE, CHECK_EXPIRATION = ON;
GO
```

> [!NOTE]
> CHECK_EXPIRATION が OFF のとき、MUST_CHANGE オプションは使用できません。

### <a name="c-creating-a-login-mapped-to-a-credential"></a>C. 資格情報にマップされるログインを作成する

次の例では、ユーザーを使用して、特定のユーザーのログインを作成します。 このログインは資格情報にマップされます。

**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。

```sql
CREATE LOGIN <login_name> WITH PASSWORD = '<enterStrongPasswordHere>',
    CREDENTIAL = <credentialName>;
GO
```

### <a name="d-creating-a-login-from-a-certificate"></a>D. 証明書からログインを作成する

次の例では、master データベースでの証明書から特定のユーザーのログインを作成します。

**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。

```sql
USE MASTER;
CREATE CERTIFICATE <certificateName>
    WITH SUBJECT = '<login_name> certificate in master database',
    EXPIRY_DATE = '12/05/2025';
GO
CREATE LOGIN <login_name> FROM CERTIFICATE <certificateName>;
GO
```

### <a name="e-creating-a-login-from-a-windows-domain-account"></a>E. Windows ドメイン アカウントからログインを作成する

次の例では、Windows ドメイン アカウントからログインを作成します。

**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。

```sql
CREATE LOGIN [<domainName>\<login_name>] FROM WINDOWS;
GO
```

### <a name="f-creating-a-login-from-a-sid"></a>F. SID からログインを作成する

次の例では、まず、SQL Server 認証のログインを作成し、ログインの SID を調べています。

```sql
CREATE LOGIN TestLogin WITH PASSWORD = 'SuperSecret52&&';
SELECT name, sid FROM sys.sql_logins WHERE name = 'TestLogin';
GO
```

このクエリは、SID として 0x241C11948AEEB749B0D22646DB1A19F2 を返します。 皆さんがクエリを実行すると、これとは異なる値が返されます。 次のステートメントは、ログインを削除し、ログインを作成し直します。 前のクエリの SID を使用します。

```sql
DROP LOGIN TestLogin;
GO

CREATE LOGIN TestLogin
WITH PASSWORD = 'SuperSecret52&&', SID = 0x241C11948AEEB749B0D22646DB1A19F2;

SELECT * FROM sys.sql_logins WHERE name = 'TestLogin';
GO
```

### <a name="g-creating-a-login-with-multiple-arguments"></a>G. 複数の引数を使用してログインを作成する

次の例では、各引数の間にコンマを使用して、複数の引数を連結する方法を示します。

```sql
CREATE LOGIN [MyUser]
WITH PASSWORD = 'MyPassword',
DEFAULT_DATABASE = MyDatabase,
CHECK_POLICY = OFF,
CHECK_EXPIRATION = OFF ;
```

## <a name="see-also"></a>参照

- [データベース エンジンの権限の概要](../../relational-databases/security/authentication-access/getting-started-with-database-engine-permissions.md)
- [プリンシパル](../../relational-databases/security/authentication-access/principals-database-engine.md)
- [パスワード ポリシー](../../relational-databases/security/password-policy.md)
- [ALTER LOGIN](../../t-sql/statements/alter-login-transact-sql.md)
- [DROP LOGIN](../../t-sql/statements/drop-login-transact-sql.md)
- [EVENTDATA](../../t-sql/functions/eventdata-transact-sql.md)
- [ログインの作成](../../relational-databases/security/authentication-access/create-a-login.md)

::: moniker-end
::: moniker range="=azuresqldb-current"

:::row:::
    :::column:::
        [SQL Server](create-login-transact-sql.md?view=sql-server-ver15&preserve-view=true)
    :::column-end:::
    :::column:::
        **_\* Azure SQL Database \*_**
    :::column-end:::
    :::column:::
        [Azure SQL<br />Managed Instance](create-login-transact-sql.md?view=azuresqldb-mi-current&preserve-view=true)
    :::column-end:::
    :::column:::
        [Azure Synapse<br />Analytics](create-login-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)
    :::column-end:::
    :::column:::
        [Analytics Platform<br />System (PDW)](create-login-transact-sql.md?view=aps-pdw-2016&preserve-view=true)
    :::column-end:::
:::row-end:::

&nbsp;

## <a name="sql-database"></a>SQL Database

## <a name="syntax"></a>構文

```syntaxsql
-- Syntax for Azure SQL Database
CREATE LOGIN login_name
 { WITH <option_list> }

<option_list> ::=
    PASSWORD = { 'password' }
    [ , SID = sid ]
```

## <a name="arguments"></a>引数

*login_name*: 作成するログインの名前を指定します。 Azure SQL Database のシングル データベースとプールされたデータベース、および [!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] のデータベースでは SQL ログインのみがサポートされています。 Azure Active Directory ユーザー用のアカウントを作成する、またはログインに関連付けられていないユーザー アカウントを作成するには、[CREATE USER](create-user-transact-sql.md) ステートメントを使用します。 詳細については、[Azure SQL Database でのログインの管理](/azure/sql-database/sql-database-manage-logins)に関する記事を参照してください。

PASSWORD **='** password* *'* : 作成する SQL ログインのパスワードを指定します。 強力なパスワードを使用してください。 詳細については、「[強力なパスワード](../../relational-databases/security/strong-passwords.md)」と「[パスワード ポリシー](../../relational-databases/security/password-policy.md)」を参照してください。 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降では、保存されたパスワード情報は salt 化パスワードの SHA-512 を使用して計算されます。

パスワードでは大文字と小文字が区別されます。 パスワードの長さは 10 文字以上 128 文字以下である必要があります。 パスワードには、a-z、A-Z、0-9 およびほとんどの英数字以外の文字を含めることができます。 パスワードには、単一引用符、または *login_name* を含めることはできません。

SID = *sid*: ログインの再作成に使用されます。 Windows 認証ログインではなく、SQL Server 認証ログインにのみ適用されます。 新しい SQL Server 認証ログインの SID を指定します。 このオプションが使用されていない場合、SQL Server で自動的に SID が割り当てられます。 SID 構造体は、SQL Server のバージョンに依存します。 SQL Database の場合、これは、`0x01060000000000640000000000000000` と、GUID を表す 16 バイトで構成される 32 バイト (**binary(32)** ) のリテラルです。 たとえば、「 `SID = 0x0106000000000064000000000000000014585E90117152449347750164BA00A7` 」のように入力します。

## <a name="remarks"></a>解説

- パスワードでは大文字と小文字が区別されます。
- ログインを作成すると、自動的に新しいログインが有効になり、ログインにサーバー レベルの **CONNECT SQL** 権限が与えられます。

> [!IMPORTANT]
> Azure SQL Database でログインとユーザーを操作する方法の詳細については、[Azure SQL Database でのログインの管理](/azure/sql-database/sql-database-manage-logins)に関する記事を参照してください。

## <a name="login"></a>ログイン

### <a name="sql-database-logins"></a>SQL データベース ログイン

**CREATE LOGIN** ステートメントはバッチ内の唯一のステートメントである必要があります。

**sqlcmd** などの SQL Database に接続するいくつかのメソッドでは、 *\<login>* @ *\<server>* の表記法を使用して、接続文字列のログイン名に SQL Database サーバー名を追加する必要があります。 たとえば、ログインが `login1` で、SQL Database サーバーの完全修飾名が `servername.database.windows.net` である場合、接続文字列の *username* パラメーターは `login1@servername` となる必要があります。 の合計の長さ、 *username* パラメーターには、128 文字まで *login_name* サーバー名の長さマイナス 127 文字に制限されます。 この例では、`login_name` が 10 文字であるため、`servername` には 117 文字までしか指定できません。

SQL Database では、ログインを作成するには、適切なアクセス許可を使用して master データベースに接続する必要があります。 詳細については、「[追加のログインと管理アクセス許可を持つユーザーを作成する](/azure/sql-database/sql-database-manage-logins#create-additional-logins-and-users-having-administrative-permissions)」を参照してください。

SQL Server ルールを使用すると、\<loginname>@\<servername> 形式の SQL Server 認証ログインを作成できます。 [!INCLUDE[ssSDS](../../includes/sssds-md.md)] サーバーが **myazureserver** で、ログインが **myemail@live.com** である場合、 **myemail@live.com@myazureserver** としてログインを指定する必要があります。

SQL Database では、接続の認証に必要なログイン データおよびサーバー レベルのファイアウォール規則は、各データベースで一時的にキャッシュされます。 このキャッシュは定期的に更新されます。 認証キャッシュを強制的に更新し、データベースに最新バージョンのログイン テーブルがあることを確認するには、[DBCC FLUSHAUTHCACHE](../../t-sql/database-console-commands/dbcc-flushauthcache-transact-sql.md) を実行します。

## <a name="permissions"></a>アクセス許可

サーバーレベル プリンシパルのログイン (準備プロセスで作成) または master データベースの `loginmanager` データベース ロールのメンバーだけが新しいログインを作成できます。 詳細については、「[追加のログインと管理アクセス許可を持つユーザーを作成する](/azure/sql-database/sql-database-manage-logins#create-additional-logins-and-users-having-administrative-permissions)」を参照してください。

## <a name="examples"></a>例

### <a name="a-creating-a-login-with-a-password"></a>A. パスワード付きのログインを作成する

次の例では、特定のユーザーのログインを作成し、パスワードを割り当てます。

```sql
CREATE LOGIN <login_name> WITH PASSWORD = '<enterStrongPasswordHere>';
GO
```

### <a name="b-creating-a-login-from-a-sid"></a>B. SID からログインを作成する

次の例では、まず、SQL Server 認証のログインを作成し、ログインの SID を調べています。

```sql
CREATE LOGIN TestLogin WITH PASSWORD = 'SuperSecret52&&';

SELECT name, sid FROM sys.sql_logins WHERE name = 'TestLogin';
GO
```

このクエリは、SID として 0x241C11948AEEB749B0D22646DB1A19F2 を返します。 皆さんがクエリを実行すると、これとは異なる値が返されます。 次のステートメントは、ログインを削除し、ログインを作成し直します。 前のクエリの SID を使用します。

```sql
DROP LOGIN TestLogin;
GO

CREATE LOGIN TestLogin
WITH PASSWORD = 'SuperSecret52&&', SID = 0x241C11948AEEB749B0D22646DB1A19F2;

SELECT * FROM sys.sql_logins WHERE name = 'TestLogin';
GO
```

## <a name="see-also"></a>参照

- [データベース エンジンの権限の概要](../../relational-databases/security/authentication-access/getting-started-with-database-engine-permissions.md)
- [プリンシパル](../../relational-databases/security/authentication-access/principals-database-engine.md)
- [パスワード ポリシー](../../relational-databases/security/password-policy.md)
- [ALTER LOGIN](../../t-sql/statements/alter-login-transact-sql.md)
- [DROP LOGIN](../../t-sql/statements/drop-login-transact-sql.md)
- [EVENTDATA](../../t-sql/functions/eventdata-transact-sql.md)
- [ログインの作成](../../relational-databases/security/authentication-access/create-a-login.md)

::: moniker-end
::: moniker range="=azuresqldb-mi-current"

:::row:::
    :::column:::
        [SQL Server](create-login-transact-sql.md?view=sql-server-ver15&preserve-view=true)
    :::column-end:::
    :::column:::
        [Azure SQL Database](create-login-transact-sql.md?view=azuresqldb-current&preserve-view=true)
    :::column-end:::
    :::column:::
        **_\* Azure SQL<br />Managed Instance \*_**
    :::column-end:::
    :::column:::
        [Azure Synapse<br />Analytics](create-login-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)
    :::column-end:::
    :::column:::
        [Analytics Platform<br />System (PDW)](create-login-transact-sql.md?view=aps-pdw-2016&preserve-view=true)
    :::column-end:::
:::row-end:::

&nbsp;

## <a name="azure-sql-managed-instance"></a>Azure SQL Managed Instance

## <a name="syntax"></a>構文

```syntaxsql
-- Syntax for Azure SQL Managed Instance
CREATE LOGIN login_name [FROM EXTERNAL PROVIDER] { WITH <option_list> [,..]}

<option_list> ::=
    PASSWORD = {'password'}
    | SID = sid
    | DEFAULT_DATABASE = database
    | DEFAULT_LANGUAGE = language
```

## <a name="arguments"></a>引数

*login_name*: **FROM EXTERNAL PROVIDER** 句と共に使用すると、ログインで Azure Active Directory (AD) プリンシパル (Azure AD ユーザー、グループ、またはアプリケーション) が指定されます。 それ以外の場合、ログインは作成された SQL ログインの名前を示します。

FROM EXTERNAL PROVIDER </br>
ログインが Azure AD Authentication 用であることを指定します。

PASSWORD **=** '*password*': 作成する SQL ログインのパスワードを指定します。 強力なパスワードを使用してください。 詳細については、「[強力なパスワード](../../relational-databases/security/strong-passwords.md)」と「[パスワード ポリシー](../../relational-databases/security/password-policy.md)」を参照してください。 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降では、保存されたパスワード情報は salt 化パスワードの SHA-512 を使用して計算されます。

パスワードでは大文字と小文字が区別されます。 パスワードの長さは 10 文字以上 128 文字以下である必要があります。 パスワードには、a-z、A-Z、0-9 およびほとんどの英数字以外の文字を含めることができます。 パスワードには、単一引用符、または *login_name* を含めることはできません。

SID **=** *sid*: ログインの再作成に使用されます。 SQL Server 認証ログインにのみ適用されます。 新しい SQL Server 認証ログインの SID を指定します。 このオプションが使用されていない場合、SQL Server で自動的に SID が割り当てられます。 SID 構造体は、SQL Server のバージョンに依存します。 SQL Database の場合、これは、`0x01060000000000640000000000000000` と、GUID を表す 16 バイトで構成される 32 バイト (**binary(32)** ) のリテラルです。 たとえば、「 `SID = 0x0106000000000064000000000000000014585E90117152449347750164BA00A7` 」のように入力します。

## <a name="remarks"></a>解説

- パスワードでは大文字と小文字が区別されます。
- Azure AD アカウントにマップされたサーバーレベルのプリンシパルを作成するための新しい構文が導入されました (**FROM EXTERNAL PROVIDER**)
- **FROM EXTERNAL PROVIDER** を指定した場合:

  - login_name は、現在の Azure SQL Managed Instance によって Azure AD 内でアクセスできる既存の Azure AD アカウント (ユーザー、グループ、またはアプリケーション) を表す必要があります。 Azure AD プリンシパルに対して、CREATE LOGIN 構文では次が要求されます。
    - Azure AD ユーザー用の Azure AD オブジェクトの UserPrincipalName。
    - Azure AD のグループと Azure AD アプリケーション用の Azure AD オブジェクトの DisplayName。
  - **PASSWORD** オプションは使用できません。
- **FROM EXTERNAL PROVIDER** 句を省略すると、既定で通常の SQL ログインが作成されます。
- Azure AD ログインは sys.server_principals で表示することができます。Azure AD ユーザーにマップされたログインの場合、type 列の値は **E** に設定され、type_desc は **EXTERNAL_LOGIN** に設定されます。Azure AD グループにマップされたログインの場合、type 列の値は **X** に設定され、type_desc 値は **EXTERNAL_GROUP** に設定されます。
- スクリプトでログインを転送する場合は、「[SQL Server 2005 のインスタンス間でログインおよびパスワードを転送する方法](https://support.microsoft.com/kb/918992)」を参照してください。
- ログインを作成すると、自動的に新しいログインが有効になり、ログインにサーバー レベルの **CONNECT SQL** 権限が与えられます。

> [!IMPORTANT]
> Azure SQL Database でログインとユーザーを操作する方法の詳細については、[Azure SQL Database でのログインの管理](/azure/sql-database/sql-database-manage-logins)に関する記事を参照してください。

## <a name="logins-and-permissions"></a>ログインとアクセス許可

サーバーレベル プリンシパルのログイン (準備プロセスで作成) または master データベースの `securityadmin` または `sysadmin` データベース ロールのメンバーだけが新しいログインを作成できます。 詳細については、[サーバー レベルのロール](/azure/sql-database/sql-database-manage-logins#groups-and-roles)と [ALTER SERVER ROLE](../../t-sql/statements/alter-server-role-transact-sql.md) に関するページを参照してください。

既定で、マスターに新しく作成された Azure AD ログインに付与される標準のアクセス許可は **CONNECT SQL** と **VIEW ANY DATABASE** です。

### <a name="sql-managed-instance-logins"></a>SQL Managed Instance ログイン

- サーバーに対する **ALTER ANY LOGIN** アクセス許可、または固定サーバー ロール `securityadmin` または `sysadmin` のいずれかのメンバーシップが必要です。 create コマンドを実行できるのは、サーバーに対する **ALTER ANY LOGIN** アクセス許可またはそのようなロールのいずれかのメンバーシップを持つ Azure Active Directory (Azure AD) アカウントのみです。
- ログインが SQL プリンシパルの場合、作成コマンドを使用して Azure AD アカウントのログインを作成できるのは、`sysadmin` ロールに属しているログインのみです。
- Azure SQL Managed Instance で使用されるのと同じディレクトリ内の Azure AD のメンバーである必要があります。

## <a name="after-creating-a-login"></a>ログインを作成した後

> [!NOTE]
> 作成後の Azure SQL Managed Instance 機能の Azure AD 管理者が変更されました。 詳しくは、「[マネージド インスタンス用の新しい Azure AD 管理機能](/azure/sql-database/sql-database-aad-authentication-configure#new-azure-ad-admin-functionality-for-mi)」をご覧ください。

ログインが作成されたら、ログインはマネージド インスタンスに接続できますが、**public** ロールに与えられた権限しか持ちません。 次の操作のいくつかを実行することを検討してください。

- Azure AD ログインから Azure AD ユーザーを作成するには、「[CREATE USER](../../t-sql/statements/create-user-transact-sql.md)」を参照してください。
- データベースのユーザーに権限を付与するには、**ALTER SERVER ROLE** ... **ADD MEMBER** ステートメントを使用して組み込みデータベース ロールのいずれか、またはカスタム ロールにユーザーを追加するか、[GRANT](../../t-sql/statements/grant-transact-sql.md) ステートメントを使用して直接ユーザーに権限を付与します。 詳細については、[管理者以外のロール](/azure/sql-database/sql-database-manage-logins#non-administrator-users)、[追加のサーバー レベルの管理者ロール](/azure/sql-database/sql-database-manage-logins#additional-server-level-administrative-roles)、[ALTER SERVER ROLE](../../t-sql/statements/alter-server-role-transact-sql.md)、および [GRANT](grant-transact-sql.md) ステートメントに関するページを参照してください。
- サーバー全体の権限を付与するには、master データベースにデータベース ユーザーを作成し、**ALTER SERVER ROLE** ... **ADD MEMBER** ステートメントを使用して、管理サーバー ロールのいずれかにユーザーを追加します。 詳細については、[サーバー レベルのロール](/azure/sql-database/sql-database-manage-logins#groups-and-roles)、[ALTER SERVER ROLE](../../t-sql/statements/alter-server-role-transact-sql.md)、および[サーバー ロール](/azure/sql-database/sql-database-manage-logins#additional-server-level-administrative-roles)に関するページを参照してください。
  - 次のコマンドを使用して、Azure AD ログインに `sysadmin` ロールを追加します。`ALTER SERVER ROLE sysadmin ADD MEMBER [AzureAD_Login_name]`
- 新しいログインまたはログインを含むロールにサーバー レベルの権限を許可するには、**GRANT** ステートメントを使用します。 詳細については、「[GRANT](../../t-sql/statements/grant-transact-sql.md)」を参照してください。

## <a name="limitations"></a>制限事項

- Azure AD グループにマップされた Azure AD ログインをデータベース所有者として設定することはサポートされていません。
- [EXECUTE AS](execute-as-transact-sql.md) 句など、他の Azure AD プリンシパルを使用した Azure AD サーバー レベル プリンシパルの権限の借用はサポートされています。
- Azure AD プリンシパルを対象とした次の操作を実行できるのは、`sysadmin` ロールの一部である SQL サーバーレベルのプリンシパル (ログイン) のみです。
  - EXECUTE AS USER
  - EXECUTE AS LOGIN
- 別の Azure AD ディレクトリからインポートされた外部 (ゲスト) ユーザーを、Azure portal で SQL Managed Instance の Azure AD 管理者として直接構成することはできません。 代わりに、外部ユーザーを Azure AD のセキュリティが有効なグループに参加させ、そのグループをインスタンス管理者として構成します。 PowerShell または Azure CLI を使用して、個々のゲスト ユーザーをインスタンス管理者として設定できます。
- ログインは、フェールオーバー グループのセカンダリ インスタンスにはレプリケートされません。 ログインは、システム データベースである master データベースに保存されるため、geo レプリケートされません。 これを解決するには、ユーザーはセカンダリ インスタンスに同じ SID を使用したログインを作成する必要があります。

```SQL
-- Code to create login on the secondary instance
CREATE LOGIN foo WITH PASSWORD = '<enterStrongPasswordHere>', SID = <login_sid>;
```

## <a name="examples"></a>例

### <a name="a-creating-a-login-with-a-password"></a>A. パスワード付きのログインを作成する

次の例では、特定のユーザーのログインを作成し、パスワードを割り当てます。

 ```sql
 CREATE LOGIN <login_name> WITH PASSWORD = '<enterStrongPasswordHere>';
 GO
 ```

### <a name="b-creating-a-login-from-a-sid"></a>B. SID からログインを作成する

 次の例では、まず、SQL Server 認証のログインを作成し、ログインの SID を調べています。

 ```sql
 CREATE LOGIN TestLogin WITH PASSWORD = 'SuperSecret52&&';

 SELECT name, sid FROM sys.sql_logins WHERE name = 'TestLogin';
 GO
 ```

このクエリは、SID として 0x241C11948AEEB749B0D22646DB1A19F2 を返します。 皆さんがクエリを実行すると、これとは異なる値が返されます。 次のステートメントは、ログインを削除し、ログインを作成し直します。 前のクエリの SID を使用します。

 ```sql
 DROP LOGIN TestLogin;
 GO

 CREATE LOGIN TestLogin
 WITH PASSWORD = 'SuperSecret52&&', SID = 0x241C11948AEEB749B0D22646DB1A19F2;

 SELECT * FROM sys.sql_logins WHERE name = 'TestLogin';
 GO
 ```

### <a name="c-creating-a-login-for-a-local-azure-ad-account"></a>C. ローカル Azure AD アカウントのログインを作成する

 次の例では、Azure AD に存在する *myaad* の Azure AD アカウント joe@myaad.onmicrosoft.com のログインを作成します。

```sql
CREATE LOGIN [joe@myaad.onmicrosoft.com] FROM EXTERNAL PROVIDER
GO
```

### <a name="d-creating-a-login-for-a-federated-azure-ad-account"></a>D. フェデレーション Azure AD アカウントのログインを作成する

 次の例では、*contoso* という Azure AD に存在するフェデレーション Azure AD アカウント bob@contoso.com のログインを作成します。 ユーザー bob はゲスト ユーザーである可能性もあります。

```sql
CREATE LOGIN [bob@contoso.com] FROM EXTERNAL PROVIDER
GO
```

### <a name="e-creating-a-login-for-an-azure-ad-group"></a>E. Azure AD グループのログインを作成する

 次の例では、Azure AD に存在する *myaad* の Azure AD グループ *mygroup* のログインを作成します。

```sql
CREATE LOGIN [mygroup] FROM EXTERNAL PROVIDER
GO
```

### <a name="f-creating-a-login-for-an-azure-ad-application"></a>F. Azure AD アプリケーションのログインを作成する

次の例では、Azure AD に存在する *myaad* の Azure AD アプリケーション *myapp* のログインを作成します。

```sql
CREATE LOGIN [myapp] FROM EXTERNAL PROVIDER
```

### <a name="g-check-newly-added-logins"></a>G. 新しく追加されたログインを確認する

新しく追加されたログインを確認するには、次の T-SQL コマンドを実行します。

```sql
SELECT *
FROM sys.server_principals;
GO
```

## <a name="see-also"></a>参照

- [データベース エンジンの権限の概要](../../relational-databases/security/authentication-access/getting-started-with-database-engine-permissions.md)
- [プリンシパル](../../relational-databases/security/authentication-access/principals-database-engine.md)
- [パスワード ポリシー](../../relational-databases/security/password-policy.md)
- [ALTER LOGIN](../../t-sql/statements/alter-login-transact-sql.md)
- [DROP LOGIN](../../t-sql/statements/drop-login-transact-sql.md)
- [EVENTDATA](../../t-sql/functions/eventdata-transact-sql.md)
- [ログインの作成](../../relational-databases/security/authentication-access/create-a-login.md)

::: moniker-end
::: moniker range="=azure-sqldw-latest"

:::row:::
    :::column:::
        [SQL Server](create-login-transact-sql.md?view=sql-server-ver15&preserve-view=true)
    :::column-end:::
    :::column:::
        [Azure SQL Database](create-login-transact-sql.md?view=azuresqldb-current&preserve-view=true)
    :::column-end:::
    :::column:::
        [Azure SQL<br />Managed Instance](create-login-transact-sql.md?view=azuresqldb-mi-current&preserve-view=true)
    :::column-end:::
    :::column:::
        **_\* Azure Synapse<br />Analytics \*_**
    :::column-end:::
    :::column:::
        [Analytics Platform<br />System (PDW)](create-login-transact-sql.md?view=aps-pdw-2016&preserve-view=true)
    :::column-end:::
:::row-end:::

&nbsp;

## <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

## <a name="syntax"></a>構文

```syntaxsql
-- Syntax for Azure Synapse Analytics
CREATE LOGIN login_name
 { WITH <option_list> }

<option_list> ::=
    PASSWORD = { 'password' }
    [ , SID = sid ]
```

## <a name="arguments"></a>引数

*login_name*: 作成するログインの名前を指定します。 Azure Synapse の SQL Analytics では SQL ログインのみサポートされています。 Azure Active Directory ユーザーのアカウントを作成するには、[CREATE USER](create-user-transact-sql.md) ステートメントを使用します。

PASSWORD **='** password* *'* : 作成する SQL ログインのパスワードを指定します。 強力なパスワードを使用してください。 詳細については、「[強力なパスワード](../../relational-databases/security/strong-passwords.md)」と「[パスワード ポリシー](../../relational-databases/security/password-policy.md)」を参照してください。 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降では、保存されたパスワード情報は salt 化パスワードの SHA-512 を使用して計算されます。

パスワードでは大文字と小文字が区別されます。 パスワードの長さは 10 文字以上 128 文字以下である必要があります。 パスワードには、a-z、A-Z、0-9 およびほとんどの英数字以外の文字を含めることができます。 パスワードには、単一引用符、または *login_name* を含めることはできません。

 SID = *sid*: ログインの再作成に使用されます。 Windows 認証ログインではなく、SQL Server 認証ログインにのみ適用されます。 新しい SQL Server 認証ログインの SID を指定します。 このオプションが使用されていない場合、SQL Server で自動的に SID が割り当てられます。 SID 構造体は、SQL Server のバージョンに依存します。 SQL Analytics の場合、これは、`0x01060000000000640000000000000000` と、GUID を表す 16 バイトで構成される 32 バイト (**binary(32)** ) のリテラルです。 たとえば、「 `SID = 0x0106000000000064000000000000000014585E90117152449347750164BA00A7` 」のように入力します。

## <a name="remarks"></a>解説

- パスワードでは大文字と小文字が区別されます。
- スクリプトでログインを転送する場合は、「[SQL Server 2005 のインスタンス間でログインおよびパスワードを転送する方法](https://support.microsoft.com/kb/918992)」を参照してください。
- ログインを作成すると、自動的に新しいログインが有効になり、ログインにサーバー レベルの **CONNECT SQL** 権限が与えられます。
- アクセスを許可するにはサーバーの[認証モード](../../relational-databases/security/choose-an-authentication-mode.md)がログインの種類に一致する必要があります。
- 権限システムの設計の詳細については、「 [データベース エンジンの権限の概要](../../relational-databases/security/authentication-access/getting-started-with-database-engine-permissions.md)」を参照してください。

## <a name="logins"></a>Login

**CREATE LOGIN** ステートメントはバッチ内の唯一のステートメントである必要があります。

**sqlcmd** などのツールを利用して Azure Synapse に接続するとき、 *\<login>* @ *\<server>* の表記法を使用し、接続文字列のログイン名に SQL Analytics サーバー名を追加する必要があります。 たとえば、ログインが `login1` で、SQL Analytics サーバーの完全修飾名が `servername.database.windows.net` である場合、接続文字列の *username* パラメーターは `login1@servername` となる必要があります。 の合計の長さ、 *username* パラメーターには、128 文字まで *login_name* サーバー名の長さマイナス 127 文字に制限されます。 この例では、`login_name` が 10 文字であるため、`servername` には 117 文字までしか指定できません。

ログインを作成するには master データベースに接続する必要があります。

SQL Server ルールを使用すると、\<loginname>@\<servername> 形式の SQL Server 認証ログインを作成できます。 [!INCLUDE[ssSDS](../../includes/sssds-md.md)] サーバーが **myazureserver** で、ログインが **myemail@live.com** である場合、 **myemail@live.com@myazureserver** としてログインを指定する必要があります。

接続の認証に必要なログイン データおよびサーバーレベルのファイアウォール規則は、各データベースで一時的にキャッシュされます。 このキャッシュは定期的に更新されます。 認証キャッシュを強制的に更新し、データベースに最新バージョンのログイン テーブルがあることを確認するには、[DBCC FLUSHAUTHCACHE](../../t-sql/database-console-commands/dbcc-flushauthcache-transact-sql.md) を実行します。

ログインの詳細については、「[データベースとログインの管理](/azure/sql-database/sql-database-manage-logins)」を参照してください。

## <a name="permissions"></a>アクセス許可

サーバーレベル プリンシパルのログイン (準備プロセスで作成) または master データベースの `loginmanager` データベース ロールのメンバーだけが新しいログインを作成できます。 詳細については、[サーバー レベルのロール](/azure/sql-database/sql-database-manage-logins#groups-and-roles)と [ALTER SERVER ROLE](../../t-sql/statements/alter-server-role-transact-sql.md) に関するページを参照してください。

## <a name="after-creating-a-login"></a>ログインを作成した後

ログインを作成すると、そのログインで Azure Synapse に接続できますが、**public** ロールに付与される権限しか与えられません。 次の操作のいくつかを実行することを検討してください。

- データベースに接続するには、ログイン用のデータベース ユーザーを作成する必要があります。 詳細については、「[CREATE USER](../../t-sql/statements/create-user-transact-sql.md)」を参照してください。
- データベースのユーザーに権限を付与するには、**ALTER SERVER ROLE** ... **ADD MEMBER** ステートメントを使用して組み込みデータベース ロールのいずれか、またはカスタム ロールにユーザーを追加するか、[GRANT](grant-transact-sql.md) ステートメントを使用して直接ユーザーに権限を付与します。 詳細については、[管理者以外のロール](/azure/sql-database/sql-database-manage-logins#non-administrator-users)、[追加のサーバー レベルの管理者ロール](/azure/sql-database/sql-database-manage-logins#additional-server-level-administrative-roles)、[ALTER SERVER ROLE](../../t-sql/statements/alter-server-role-transact-sql.md)、および [GRANT](grant-transact-sql.md) ステートメントに関するページを参照してください。
- サーバー全体の権限を付与するには、master データベースにデータベース ユーザーを作成し、**ALTER SERVER ROLE** ... **ADD MEMBER** ステートメントを使用して、管理サーバー ロールのいずれかにユーザーを追加します。 詳細については、[サーバー レベルのロール](/azure/sql-database/sql-database-manage-logins#groups-and-roles)、[ALTER SERVER ROLE](../../t-sql/statements/alter-server-role-transact-sql.md)、および[サーバー ロール](/azure/sql-database/sql-database-manage-logins#additional-server-level-administrative-roles)に関するページを参照してください。

- 新しいログインまたはログインを含むロールにサーバー レベルの権限を許可するには、**GRANT** ステートメントを使用します。 詳細については、「[GRANT](../../t-sql/statements/grant-transact-sql.md)」を参照してください。

## <a name="examples"></a>例

### <a name="a-creating-a-login-with-a-password"></a>A. パスワード付きのログインを作成する

次の例では、特定のユーザーのログインを作成し、パスワードを割り当てます。

```sql
CREATE LOGIN <login_name> WITH PASSWORD = '<enterStrongPasswordHere>';
GO
```

### <a name="b-creating-a-login-from-a-sid"></a>B. SID からログインを作成する

 次の例では、まず、SQL Server 認証のログインを作成し、ログインの SID を調べています。

```sql
CREATE LOGIN TestLogin WITH PASSWORD = 'SuperSecret52&&';

SELECT name, sid FROM sys.sql_logins WHERE name = 'TestLogin';
GO
```

このクエリは、SID として 0x241C11948AEEB749B0D22646DB1A19F2 を返します。 皆さんがクエリを実行すると、これとは異なる値が返されます。 次のステートメントは、ログインを削除し、ログインを作成し直します。 前のクエリの SID を使用します。

```sql
DROP LOGIN TestLogin;
GO

CREATE LOGIN TestLogin
WITH PASSWORD = 'SuperSecret52&&', SID = 0x241C11948AEEB749B0D22646DB1A19F2;

SELECT * FROM sys.sql_logins WHERE name = 'TestLogin';
GO
```

## <a name="see-also"></a>参照

- [データベース エンジンの権限の概要](../../relational-databases/security/authentication-access/getting-started-with-database-engine-permissions.md)
- [プリンシパル](../../relational-databases/security/authentication-access/principals-database-engine.md)
- [パスワード ポリシー](../../relational-databases/security/password-policy.md)
- [ALTER LOGIN](../../t-sql/statements/alter-login-transact-sql.md)
- [DROP LOGIN](../../t-sql/statements/drop-login-transact-sql.md)
- [EVENTDATA](../../t-sql/functions/eventdata-transact-sql.md)
- [ログインの作成](../../relational-databases/security/authentication-access/create-a-login.md)

::: moniker-end
::: moniker range=">=aps-pdw-2016"

:::row:::
    :::column:::
        [SQL Server](create-login-transact-sql.md?view=sql-server-ver15&preserve-view=true)
    :::column-end:::
    :::column:::
        [Azure SQL Database](create-login-transact-sql.md?view=azuresqldb-current&preserve-view=true)
    :::column-end:::
    :::column:::
        [Azure SQL<br />Managed Instance](create-login-transact-sql.md?view=azuresqldb-mi-current&preserve-view=true)
    :::column-end:::
    :::column:::
        [Azure Synapse<br />Analytics](create-login-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)
    :::column-end:::
    :::column:::
        **_\* Analytics<br />Platform System (PDW) \*_**
    :::column-end:::
:::row-end:::

&nbsp;

## <a name="analytics-platform-system"></a>分析プラットフォーム システム

## <a name="syntax"></a>構文

```syntaxsql
-- Syntax for Analytics Platform System
CREATE LOGIN loginName { WITH <option_list1> | FROM WINDOWS }

<option_list1> ::=
    PASSWORD = { 'password' } [ MUST_CHANGE ]
    [ , <option_list> [ ,... ] ]
  
<option_list> ::=
      CHECK_EXPIRATION = { ON | OFF}
    | CHECK_POLICY = { ON | OFF}
```

## <a name="arguments"></a>引数

*login_name*: 作成するログインの名前を指定します。 ログインには 4 つの種類があります。SQL Server ログイン、Windows ログイン、証明書マッピング ログイン、非対称キー マッピング ログインです。 Windows ドメイン アカウントからマップされるログインを作成する場合は、Windows 2000 よりも前で使用されていた [\<domainName>\\<login_name>] 形式のユーザー ログイン名を使用する必要があります。 login_name@DomainName 形式の UPN は使用できません。 例については、この記事の後半の例 D を参照してください。 認証ログインは **sysname** 型であり、[識別子](../../relational-databases/databases/database-identifiers.md) のルールに従っている必要があります。このログインに " **\\** " を含めることはできません。 Windows ログインには " **\\** " を含めることができます。 Active Directory ユーザーに基づくログインは、21 文字未満の名前に制限されます。

PASSWORD **='** _password_' SQL Server ログインにのみ適用されます。 作成するログインのパスワードを指定します。 強力なパスワードを使用してください。 詳細については、「[強力なパスワード](../../relational-databases/security/strong-passwords.md)」と「[パスワード ポリシー](../../relational-databases/security/password-policy.md)」を参照してください。 SQL Server 2012 (11.x) 以降では、保存されたパスワード情報は salt 化パスワードの SHA-512 を使用して計算されます。

パスワードでは大文字と小文字が区別されます。 パスワードの長さは 10 文字以上 128 文字以下である必要があります。 パスワードには、a-z、A-Z、0-9 およびほとんどの英数字以外の文字を含めることができます。 パスワードには、単一引用符、または *login_name* を含めることはできません。

MUST_CHANGE SQL Server ログインにのみ適用されます。 このオプションが含まれている場合、SQL Server では、新しいログインが最初に使用されたときに、ユーザーに新しいパスワードの入力が求められます。

CHECK_EXPIRATION **=** { ON | **OFF** }: SQL Server ログインにのみ適用されます。 このログインに、パスワードの有効期限ポリシーを適用するかどうかを指定します。 既定値は OFF です。

CHECK_POLICY **=** { **ON** | OFF }: SQL Server ログインにのみ適用されます。 SQL Server を実行しているコンピューターの Windows パスワード ポリシーをこのログインに適用するかどうかを指定します。 既定値は ON です。

Windows のポリシーで強力なパスワードが求められる場合は、次の 4 つの特性のうちの少なくとも 3 つをパスワードに含める必要があります。

- 大文字 (A-Z)。
- 小文字 (a-z)。
- 数字 (0-9)。
- スペース、_、@、*、^、%、!、$、#、& などの英数字以外の文字。

WINDOWS: ログインを Windows ログインにマップするよう指定します。

## <a name="remarks"></a>解説

- パスワードでは大文字と小文字が区別されます。
- MUST_CHANGE が指定された場合、CHECK_EXPIRATION および CHECK_POLICY は ON に設定されなければなりません。 ON に設定しない場合、ステートメントは失敗します。
- CHECK_POLICY = OFF と CHECK_EXPIRATION = ON の組み合わせはサポートされていません。
- CHECK_POLICY を OFF に設定すると、*lockout_time* はリセットされ、CHECK_EXPIRATION は OFF に設定されます。

> [!IMPORTANT]
> CHECK_EXPIRATION および CHECK_POLICY は、Windows Server 2003 以降でのみ適用されます。 詳細については、「 [Password Policy](../../relational-databases/security/password-policy.md)」をご参照ください。

- スクリプトでログインを転送する場合は、「[SQL Server 2005 のインスタンス間でログインおよびパスワードを転送する方法](https://support.microsoft.com/kb/918992)」を参照してください。
- ログインを作成すると、自動的に新しいログインが有効になり、ログインにサーバー レベルの **CONNECT SQL** 権限が与えられます。
- 権限システムの設計の詳細については、「 [データベース エンジンの権限の概要](../../relational-databases/security/authentication-access/getting-started-with-database-engine-permissions.md)」を参照してください。

## <a name="permissions"></a>アクセス許可

ログインを作成できるのは、サーバーに対する **ALTER ANY LOGIN** 権限、または **securityadmin** 固定サーバー ロールのメンバーシップを持つユーザーのみとなります。 詳細については、[サーバー レベルのロール](/azure/sql-database/sql-database-manage-logins#groups-and-roles)と [ALTER SERVER ROLE](../../t-sql/statements/alter-server-role-transact-sql.md) に関するページを参照してください。

## <a name="after-creating-a-login"></a>ログインを作成した後

ログインを作成すると、そのログインで Azure Synapse Analytics に接続できますが、**public** ロールに付与される権限しか与えられません。 次の操作のいくつかを実行することを検討してください。

- データベースに接続するには、ログイン用のデータベース ユーザーを作成する必要があります。 詳細については、「[CREATE USER](../../t-sql/statements/create-user-transact-sql.md)」を参照してください。
- [CREATE SERVER ROLE](../../t-sql/statements/create-server-role-transact-sql.md) を使用して、ユーザー定義サーバー ロールを作成します。 **ALTER SERVER ROLE** ... ADD MEMBER を使用して、**メンバーの追加** をユーザー定義サーバー ロールに、新しいログインを追加します。 詳細については、[CREATE SERVER ROLE](../../t-sql/statements/create-server-role-transact-sql.md) と [ALTER SERVER ROLE](../../t-sql/statements/alter-server-role-transact-sql.md) に関するページを参照してください。
- 固定サーバー ロールにログインを追加するには、**sp_addsrvrolemember** を使用します。 詳細については、「[サーバー レベルのロール](../../relational-databases/security/authentication-access/server-level-roles.md)」と「[sp_addsrvrolemember](../../relational-databases/system-stored-procedures/sp-addsrvrolemember-transact-sql.md)」を参照してください。
- 新しいログインまたはログインを含むロールにサーバー レベルの権限を許可するには、**GRANT** ステートメントを使用します。 詳細については、「[GRANT](../../t-sql/statements/grant-transact-sql.md)」を参照してください。

## <a name="examples"></a>例

### <a name="g-creating-a-sql-server-authentication-login-with-a-password"></a>G. パスワード付きで SQL Server 認証ログインを作成する

次の例では、パスワード `A2c3456` のログイン `Mary7`を作成します。

```sql
CREATE LOGIN Mary7 WITH PASSWORD = 'A2c3456$#' ;
```

### <a name="h-using-options"></a>H. オプションを使用する

次の例では、ログイン `Mary8` をパスワードおよびいくつかのオプションの引数と共に作成します。

```sql
CREATE LOGIN Mary8 WITH PASSWORD = 'A2c3456$#' MUST_CHANGE,
CHECK_EXPIRATION = ON,
CHECK_POLICY = ON;
```

### <a name="i-creating-a-login-from-a-windows-domain-account"></a>I. Windows ドメイン アカウントからログインを作成する

次の例では、`Contoso` ドメイン内の `Mary` という名前の Windows ドメイン アカウントからログインを作成します。

```sql
CREATE LOGIN [Contoso\Mary] FROM WINDOWS;
GO
```

## <a name="see-also"></a>参照

- [データベース エンジンの権限の概要](../../relational-databases/security/authentication-access/getting-started-with-database-engine-permissions.md)
- [プリンシパル](../../relational-databases/security/authentication-access/principals-database-engine.md)
- [パスワード ポリシー](../../relational-databases/security/password-policy.md)
- [ALTER LOGIN](../../t-sql/statements/alter-login-transact-sql.md)
- [DROP LOGIN](../../t-sql/statements/drop-login-transact-sql.md)
- [EVENTDATA](../../t-sql/functions/eventdata-transact-sql.md)
- [ログインの作成](../../relational-databases/security/authentication-access/create-a-login.md)

---

::: moniker-end
