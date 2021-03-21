---
description: ALTER LOGIN (Transact-SQL)
title: ALTER LOGIN (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 01/10/2020
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- ALTER_LOGIN_TSQL
- ALTER LOGIN
dev_langs:
- TSQL
helpviewer_keywords:
- ALTER LOGIN statement
- change password
- mapping logins [SQL Server]
- logins [SQL Server], modifying
- passwords [SQL Server], modifying
- names [SQL Server], logins
- modifying login accounts
ms.assetid: e247b84e-c99e-4af8-8b50-57586e1cb1c5
author: VanMSFT
ms.author: vanto
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: cfe89693b365924faabe52254ed09fef075be831
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104742712"
---
# <a name="alter-login-transact-sql"></a>ALTER LOGIN (Transact-SQL)

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログイン アカウントのプロパティを変更します。

![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)

[!INCLUDE[select-product](../../includes/select-product.md)]

::: moniker range=">=sql-server-2016||>=sql-server-linux-2017"

:::row:::
    :::column:::
        **_\* SQL Server \*_** &nbsp;
    :::column-end:::
    :::column:::
        [SQL Database](alter-login-transact-sql.md?view=azuresqldb-current&preserve-view=true)
    :::column-end:::
    :::column:::
        [SQL Managed Instance](alter-login-transact-sql.md?view=azuresqldb-mi-current&preserve-view=true)
    :::column-end:::
    :::column:::
        [Azure Synapse<br />Analytics](alter-login-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)
    :::column-end:::
    :::column:::
        [Analytics Platform<br />System (PDW)](alter-login-transact-sql.md?view=aps-pdw-2016&preserve-view=true)
    :::column-end:::
:::row-end:::

&nbsp;

## <a name="sql-server"></a>SQL Server

## <a name="syntax"></a>構文

```syntaxsql
-- Syntax for SQL Server

ALTER LOGIN login_name
    {
    <status_option>
    | WITH <set_option> [ ,... ]
    | <cryptographic_credential_option>
    }
[;]

<status_option> ::=
        ENABLE | DISABLE

<set_option> ::=
    PASSWORD = 'password' | hashed_password HASHED
    [
      OLD_PASSWORD = 'oldpassword'
      | <password_option> [<password_option> ]
    ]
    | DEFAULT_DATABASE = database
    | DEFAULT_LANGUAGE = language
    | NAME = login_name
    | CHECK_POLICY = { ON | OFF }
    | CHECK_EXPIRATION = { ON | OFF }
    | CREDENTIAL = credential_name
    | NO CREDENTIAL

<password_option> ::=
    MUST_CHANGE | UNLOCK
  
<cryptographic_credentials_option> ::=
    ADD CREDENTIAL credential_name
  | DROP CREDENTIAL credential_name
```

## <a name="arguments"></a>引数

*login_name*: 変更する [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログインの名前を指定します。 ドメイン ログインは角かっこで囲み、[domain\user] の形式で表す必要があります。

ENABLE | DISABLE: このログインを有効にするか無効にするかを指定します。 ログインを無効にしても、既に接続されているログインの動作には影響しません。 (`KILL` ステートメントを使用して、既存の接続を終了します。)無効にしたログインの権限を保持したまま、偽装を継続することができます。

PASSWORD **='** _password_ **'** : [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログインにのみ適用されます。 変更するログインのパスワードを指定します。 パスワードでは大文字と小文字が区別されます。

PASSWORD **=** _hashed\_password_: HASHED キーワードにのみ適用されます。 作成するログインのパスワードのハッシュ値を指定します。

> [!IMPORTANT]
> ログイン (または包含データベース ユーザー) が接続して認証されると、接続にはログインに関する ID 情報がキャッシュされます。 Windows 認証ログインの場合、これには Windows グループのメンバーシップに関する情報も含まれます。 接続が維持されている限り、ログインの ID が認証された状態は継続します。 パスワードのリセットや Windows グループのメンバーシップの変更など、ID に関する変更を適用するには、認証機関 (Windows または [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]) からログオフしてもう一度ログインする必要があります。 **sysadmin** 固定サーバー ロールのメンバーまたは **ALTER ANY CONNECTION** 権限を持つすべてのログインは、 **KILL** コマンドを使用して接続を終了し、ログインの再接続を強制することができます。 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] でオブジェクト エクスプ ローラーおよびクエリ エディター ウィンドウに複数の接続を開くときに、接続情報を再利用できます。 再接続を強制するには、すべての接続を閉じます。

HASHED: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログインにのみ適用されます。 PASSWORD 引数の後に入力されたパスワードが、ハッシュ済みであることを示します。 このオプションを選択しなかった場合、パスワードはハッシュされてからデータベースに格納されます。 このオプションは、2 つのサーバー間でログインを同期する場合にのみ使用してください。 パスワードを定期的に変更する場合は HASHED オプションを使用しないでください。

OLD_PASSWORD **='** _oldpassword_ **'** : [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログインにのみ適用されます。 新しいパスワードを割り当てるログインの、現在のパスワードを指定します。 パスワードでは大文字と小文字が区別されます。

MUST_CHANGE: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログインにのみ適用されます。 このオプションを指定した場合、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] で変更後のログインを最初に使用するときには新しいパスワードの入力が求められます。

DEFAULT_DATABASE **=** _database_: ログインに割り当てられる既定のデータベースを指定します。

DEFAULT_LANGUAGE **=** _language_: ログインに割り当てられる既定の言語を指定します。 すべての SQL Database ログインの既定の言語は英語で、変更できません。 Linux では、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の `sa` ログインの既定の言語は英語ですが、変更することができます。

NAME = *login_name*: ログインの名前を変更する場合、新しい名前を指定します。 Windows ログインの場合は、新しい名前に対応する Windows プリンシパルの SID と、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 内のログインに関連付けられている SID が一致する必要があります。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログインの新しい名前には、円記号 (\\) は使用できません。

CHECK_EXPIRATION = { ON | **OFF** } [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログインにのみ適用されます。 このログインに、パスワードの有効期限ポリシーを適用するかどうかを指定します。 既定値は OFF です。

CHECK_POLICY **=** { **ON** | OFF }: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログインにのみ適用されます。 このログインに、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] を実行しているコンピューターの、Windows のパスワード ポリシーを適用するかどうかを指定します。 既定値は ON です。

CREDENTIAL = *credential_name*: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログインにマップする資格情報の名前を指定します。 この資格情報は、サーバー内に既に存在している必要があります。 詳細については、[資格情報](../../relational-databases/security/authentication-access/credentials-database-engine.md)に関するページをご覧ください。 資格情報を sa ログインにマップすることはできません。

NO CREDENTIAL: ログインからサーバー資格情報への既存のマッピングをすべて削除します。 詳細については、[資格情報](../../relational-databases/security/authentication-access/credentials-database-engine.md)に関するページをご覧ください。

UNLOCK: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログインにのみ適用されます。 ロックされているログインのロックを解除する必要があることを指定します。

ADD CREDENTIAL: 拡張キー管理 (EKM) プロバイダー資格情報をログインに追加します。 詳しくは、「[拡張キー管理 (EKM)](../../relational-databases/security/encryption/extensible-key-management-ekm.md)」をご覧ください。

DROP CREDENTIAL: ログインから拡張キー管理 (EKM) プロバイダー資格情報を削除します。 詳しくは、[拡張キー管理 (EKM)] (../.. /relational-databases/security/encryption/extensible-key-management-ekm.md) を参照してください。

## <a name="remarks"></a>解説

CHECK_POLICY が ON に設定されている場合、HASHED 引数は使用できません。

CHECK_POLICY を ON に変更した場合、次の動作が発生します。

- パスワードの履歴が、現在のパスワード ハッシュの値に初期化されます。

  CHECK_POLICY を OFF に変更した場合、次の動作が発生します。

- CHECK_EXPIRATION も OFF に設定されます。
- パスワード履歴は消去されます。
- 値 *lockout_time* がリセットされます。

MUST_CHANGE が指定された場合、CHECK_EXPIRATION および CHECK_POLICY は ON に設定されなければなりません。 ON に設定しない場合、ステートメントは失敗します。

CHECK_POLICY を OFF に設定した場合、CHECK_EXPIRATION を ON に設定することはできません。 このオプションの組み合わせで ALTER LOGIN ステートメントを実行すると、ステートメントは失敗します。

ALTER LOGIN を DISABLE 引数と共に使用しても Windows グループへのアクセスを拒否することはできません。 たとえば、ALTER LOGIN [*domain\group*] DISABLE を実行すると、次のエラー メッセージが返されます。

`"Msg 15151, Level 16, State 1, Line 1
"Cannot alter the login '*Domain\Group*', because it does not exist or you do not have permission."`

これは仕様です。
  
[!INCLUDE[ssSDS](../../includes/sssds-md.md)] では、接続の認証に必要なログイン データおよびサーバー レベルのファイアウォール規則は、各データベースで一時的にキャッシュされます。 このキャッシュは定期的に更新されます。 認証キャッシュを強制的に更新し、データベースに最新バージョンのログイン テーブルがあることを確認するには、[DBCC FLUSHAUTHCACHE](../../t-sql/database-console-commands/dbcc-flushauthcache-transact-sql.md) を実行します。

## <a name="permissions"></a>アクセス許可

ALTER ANY LOGIN 権限が必要です。

CREDENTIAL オプションを使用する場合は、ALTER ANY CREDENTIAL 権限も必要です。

変更するログインが固定サーバー ロール **sysadmin** のメンバーであるか、ログインに CONTROL SERVER 権限が与えられている場合、次の変更を行うには CONTROL SERVER 権限も必要になります。

- 以前のパスワードを指定せずにパスワードをリセットする。
- MUST_CHANGE、CHECK_POLICY、または CHECK_EXPIRATION を有効にする。
- ログイン名を変更する。
- ログインを有効または無効にする。
- ログインを別の資格情報にマップする。

プリンシパルでは、その独自のログインのパスワード、既定の言語、および既定のデータベースを変更できます。

## <a name="examples"></a>例

### <a name="a-enabling-a-disabled-login"></a>A. 無効なログインを有効にする

次の例では、ログイン `Mary5` を有効にします。

```sql
ALTER LOGIN Mary5 ENABLE;
```

### <a name="b-changing-the-password-of-a-login"></a>B. ログインのパスワードを変更する

次の例では、ログイン `Mary5` のパスワードを強力なパスワードに変更します。

```sql
ALTER LOGIN Mary5 WITH PASSWORD = '<enterStrongPasswordHere>';
```

### <a name="c-changing-the-password-of-a-login-when-logged-in-as-the-login"></a>C. ログインとしてログインしたときにログインのパスワードを変更する

現在使用しているログインのパスワードを変更しようとして、`ALTER ANY LOGIN` アクセス許可がない場合は、`OLD_PASSWORD` オプションを指定する必要があります。

```sql
ALTER LOGIN Mary5 WITH PASSWORD = '<enterStrongPasswordHere>' OLD_PASSWORD = '<oldWeakPasswordHere>';
```

### <a name="d-changing-the-name-of-a-login"></a>D. ログインの名前を変更する

次の例では、ログイン `Mary5` の名前を `John2` に変更します。

```sql
ALTER LOGIN Mary5 WITH NAME = John2;
```

### <a name="e-mapping-a-login-to-a-credential"></a>E. ログインを資格情報にマップする

次の例では、ログイン `John2` を資格情報 `Custodian04` にマップします。

```sql
ALTER LOGIN John2 WITH CREDENTIAL = Custodian04;
```

### <a name="f-mapping-a-login-to-an-extensible-key-management-credential"></a>F. ログインを拡張キー管理資格情報にマップする

次の例では、ログイン `Mary5` を EKM 資格情報 `EKMProvider1` にマップします。

```sql
ALTER LOGIN Mary5
ADD CREDENTIAL EKMProvider1;
GO
```

### <a name="f-unlocking-a-login"></a>F. ログインのロックを解除する

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログインのロックを解除するには、\*\*\*\* を必要なアカウントのパスワードに置き換えて、次のステートメントを実行します。

```sql
ALTER LOGIN [Mary5] WITH PASSWORD = '****' UNLOCK ;

GO
```

パスワードを変更しないでログインのロックを解除するには、チェック ポリシーをオフにしてからもう一度オンにします。

```sql
ALTER LOGIN [Mary5] WITH CHECK_POLICY = OFF;
ALTER LOGIN [Mary5] WITH CHECK_POLICY = ON;
GO
```

### <a name="g-changing-the-password-of-a-login-using-hashed"></a>G. HASHED を使用してログインのパスワードを変更する

次の例では、`TestUser` ログインのパスワードを既にハッシュされた値に変更します。

```sql
ALTER LOGIN TestUser WITH
PASSWORD = 0x01000CF35567C60BFB41EBDE4CF700A985A13D773D6B45B90900 HASHED ;
GO
```

## <a name="see-also"></a>参照

- [資格情報](../../relational-databases/security/authentication-access/credentials-database-engine.md)
- [CREATE LOGIN](../../t-sql/statements/create-login-transact-sql.md)
- [DROP LOGIN](../../t-sql/statements/drop-login-transact-sql.md)
- [CREATE CREDENTIAL](../../t-sql/statements/create-credential-transact-sql.md)
- [EVENTDATA](../../t-sql/functions/eventdata-transact-sql.md)
- [拡張キー管理 (EKM)](../../relational-databases/security/encryption/extensible-key-management-ekm.md)

::: moniker-end
::: moniker range="=azuresqldb-current"

:::row:::
    :::column:::
        [SQL Server](alter-login-transact-sql.md?view=sql-server-ver15&preserve-view=true)
    :::column-end:::
    :::column:::
        **_\* SQL Database \*_**
    :::column-end:::
    :::column:::
        [SQL Managed Instance](alter-login-transact-sql.md?view=azuresqldb-mi-current&preserve-view=true)
    :::column-end:::
    :::column:::
        [Azure Synapse<br />Analytics](alter-login-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)
    :::column-end:::
    :::column:::
        [Analytics Platform<br />System (PDW)](alter-login-transact-sql.md?view=aps-pdw-2016&preserve-view=true)
    :::column-end:::
:::row-end:::

&nbsp;

## <a name="sql-database"></a>SQL Database

## <a name="sql-server"></a>SQL Server

## <a name="syntax"></a>構文

```syntaxsql
-- Syntax for Azure SQL Database

ALTER LOGIN login_name
  {
      <status_option>
    | WITH <set_option> [ ,.. .n ]
  }
[;]

<status_option> ::=
    ENABLE | DISABLE

<set_option> ::=
    PASSWORD ='password'
    [
      OLD_PASSWORD ='oldpassword'
    ]
    | NAME = login_name
```

## <a name="arguments"></a>引数

*login_name*: 変更する [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログインの名前を指定します。 ドメイン ログインは角かっこで囲み、[domain\user] の形式で表す必要があります。

ENABLE | DISABLE: このログインを有効にするか無効にするかを指定します。 ログインを無効にしても、既に接続されているログインの動作には影響しません。 (`KILL` ステートメントを使用して、既存の接続を終了します。)無効にしたログインの権限を保持したまま、偽装を継続することができます。

PASSWORD **='** _password_ **'** : [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログインにのみ適用されます。 変更するログインのパスワードを指定します。 パスワードでは大文字と小文字が区別されます。

SQL Database への接続を継続的にアクティブにするには、少なくとも 10 時間ごとに (データベース エンジンによって実行される) 再認証が必要です。 データベース エンジンは、最初に送信されたパスワードを使用して再認証を試行するので、ユーザー入力は不要です。 パスワードが SQL Database でリセットされた場合、接続プーリングのために接続がリセットされても、パフォーマンス上の理由から接続は再認証されません。 これは、オンプレミスの SQL Server の動作とは異なります。 接続が最初に承認された後でパスワードが変更されている場合は、接続を終了し、新しいパスワードを使用して新しい接続を行う必要があります。 KILL DATABASE CONNECTION 権限を持つユーザーは、KILL コマンドを使用して SQL Database への接続を明示的に終了できます。 詳細については、[KILL](../../t-sql/language-elements/kill-transact-sql.md) に関するページをご覧ください。

> [!IMPORTANT]
> ログイン (または包含データベース ユーザー) が接続して認証されると、接続にはログインに関する ID 情報がキャッシュされます。 Windows 認証ログインの場合、これには Windows グループのメンバーシップに関する情報も含まれます。 接続が維持されている限り、ログインの ID が認証された状態は継続します。 パスワードのリセットや Windows グループのメンバーシップの変更など、ID に関する変更を適用するには、認証機関 (Windows または [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]) からログオフしてもう一度ログインする必要があります。 **sysadmin** 固定サーバー ロールのメンバーまたは **ALTER ANY CONNECTION** 権限を持つすべてのログインは、 **KILL** コマンドを使用して接続を終了し、ログインの再接続を強制することができます。 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] でオブジェクト エクスプ ローラーおよびクエリ エディター ウィンドウに複数の接続を開くときに、接続情報を再利用できます。 再接続を強制するには、すべての接続を閉じます。

OLD_PASSWORD **='** _oldpassword_ **'** : [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログインにのみ適用されます。 新しいパスワードを割り当てるログインの、現在のパスワードを指定します。 パスワードでは大文字と小文字が区別されます。

NAME = *login_name*: ログインの名前を変更する場合、新しい名前を指定します。 Windows ログインの場合は、新しい名前に対応する Windows プリンシパルの SID と、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 内のログインに関連付けられている SID が一致する必要があります。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログインの新しい名前には、円記号 (\\) は使用できません。

## <a name="remarks"></a>解説

[!INCLUDE[ssSDS](../../includes/sssds-md.md)] では、接続の認証に必要なログイン データおよびサーバー レベルのファイアウォール規則は、各データベースで一時的にキャッシュされます。 このキャッシュは定期的に更新されます。 認証キャッシュを強制的に更新し、データベースに最新バージョンのログイン テーブルがあることを確認するには、[DBCC FLUSHAUTHCACHE](../../t-sql/database-console-commands/dbcc-flushauthcache-transact-sql.md) を実行します。

## <a name="permissions"></a>アクセス許可

ALTER ANY LOGIN 権限が必要です。

変更するログインが固定サーバー ロール **sysadmin** のメンバーであるか、ログインに CONTROL SERVER 権限が与えられている場合、次の変更を行うには CONTROL SERVER 権限も必要になります。

- 以前のパスワードを指定せずにパスワードをリセットする。
- ログイン名を変更する。
- ログインを有効または無効にする。
- ログインを別の資格情報にマップする。

プリンシパルは、独自のログインのパスワードを変更できます。

## <a name="examples"></a>例

これらの例には、他の SQL 製品の使用例も含まれます。 上記でどの引数がサポートされているかを確認してください。

### <a name="a-enabling-a-disabled-login"></a>A. 無効なログインを有効にする

次の例では、ログイン `Mary5` を有効にします。

```sql
ALTER LOGIN Mary5 ENABLE;
```

### <a name="b-changing-the-password-of-a-login"></a>B. ログインのパスワードを変更する

次の例では、ログイン `Mary5` のパスワードを強力なパスワードに変更します。

```sql
ALTER LOGIN Mary5 WITH PASSWORD = '<enterStrongPasswordHere>';
```

### <a name="c-changing-the-name-of-a-login"></a>C. ログインの名前を変更する

次の例では、ログイン `Mary5` の名前を `John2` に変更します。

```sql
ALTER LOGIN Mary5 WITH NAME = John2;
```

### <a name="d-mapping-a-login-to-a-credential"></a>D. ログインを資格情報にマップする

次の例では、ログイン `John2` を資格情報 `Custodian04` にマップします。

```sql
ALTER LOGIN John2 WITH CREDENTIAL = Custodian04;
```

### <a name="e-mapping-a-login-to-an-extensible-key-management-credential"></a>E. ログインを拡張キー管理資格情報にマップする

次の例では、ログイン `Mary5` を EKM 資格情報 `EKMProvider1` にマップします。


**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。

```sql
ALTER LOGIN Mary5
ADD CREDENTIAL EKMProvider1;
GO
```

### <a name="f-unlocking-a-login"></a>F. ログインのロックを解除する

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログインのロックを解除するには、\*\*\*\* を必要なアカウントのパスワードに置き換えて、次のステートメントを実行します。

```sql
ALTER LOGIN [Mary5] WITH PASSWORD = '****' UNLOCK ;

GO
```

パスワードを変更しないでログインのロックを解除するには、チェック ポリシーをオフにしてからもう一度オンにします。

```sql
ALTER LOGIN [Mary5] WITH CHECK_POLICY = OFF;
ALTER LOGIN [Mary5] WITH CHECK_POLICY = ON;
GO
```

### <a name="g-changing-the-password-of-a-login-using-hashed"></a>G. HASHED を使用してログインのパスワードを変更する

次の例では、`TestUser` ログインのパスワードを既にハッシュされた値に変更します。

**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。

```sql
ALTER LOGIN TestUser WITH
PASSWORD = 0x01000CF35567C60BFB41EBDE4CF700A985A13D773D6B45B90900 HASHED ;
GO
```

## <a name="see-also"></a>参照

- [資格情報](../../relational-databases/security/authentication-access/credentials-database-engine.md)
- [CREATE LOGIN](../../t-sql/statements/create-login-transact-sql.md)
- [DROP LOGIN](../../t-sql/statements/drop-login-transact-sql.md)
- [CREATE CREDENTIAL](../../t-sql/statements/create-credential-transact-sql.md)
- [EVENTDATA](../../t-sql/functions/eventdata-transact-sql.md)
- [拡張キー管理 (EKM)](../../relational-databases/security/encryption/extensible-key-management-ekm.md)

::: moniker-end
::: moniker range="=azuresqldb-mi-current"

:::row:::
    :::column:::
        [SQL Server](alter-login-transact-sql.md?view=sql-server-ver15&preserve-view=true)
    :::column-end:::
    :::column:::
        [SQL Database](alter-login-transact-sql.md?view=azuresqldb-current&preserve-view=true)
    :::column-end:::
    :::column:::
        **_\* SQL Managed Instance \*_**
    :::column-end:::
    :::column:::
        [Azure Synapse<br />Analytics](alter-login-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)
    :::column-end:::
    :::column:::
        [Analytics Platform<br />System (PDW)](alter-login-transact-sql.md?view=aps-pdw-2016&preserve-view=true)
    :::column-end:::
:::row-end:::

&nbsp;

## <a name="azure-sql-managed-instance"></a>Azure SQL Managed Instance

## <a name="syntax"></a>構文

```syntaxsql
-- Syntax for SQL Server and Azure SQL Managed Instance

ALTER LOGIN login_name
    {
    <status_option>
    | WITH <set_option> [ ,... ]
    | <cryptographic_credential_option>
    }
[;]

<status_option> ::=
        ENABLE | DISABLE
  
<set_option> ::=
    PASSWORD = 'password' | hashed_password HASHED
    [
      OLD_PASSWORD = 'oldpassword'
      | <password_option> [<password_option> ]
    ]
    | DEFAULT_DATABASE = database
    | DEFAULT_LANGUAGE = language
    | NAME = login_name
    | CHECK_POLICY = { ON | OFF }
    | CHECK_EXPIRATION = { ON | OFF }
    | CREDENTIAL = credential_name
    | NO CREDENTIAL

<password_option> ::=
    MUST_CHANGE | UNLOCK

<cryptographic_credentials_option> ::=
    ADD CREDENTIAL credential_name
  | DROP CREDENTIAL credential_name
```

> [!NOTE]
> 作成後にマネージド インスタンス機能の Azure AD 管理者が変更されました。 詳しくは、「[マネージド インスタンス用の新しい Azure AD 管理機能](/azure/sql-database/sql-database-aad-authentication-configure#new-azure-ad-admin-functionality-for-mi)」をご覧ください。

```syntaxsql
-- Syntax for Azure SQL Managed Instance using Azure AD logins

ALTER LOGIN login_name
  {
      <status_option>
    | WITH <set_option> [ ,.. .n ]
  }
[;]

<status_option> ::=
    ENABLE | DISABLE

<set_option> ::=
     DEFAULT_DATABASE = database
   | DEFAULT_LANGUAGE = language
```

## <a name="arguments"></a>引数

### <a name="arguments-applicable-to-sql-and-azure-ad-logins"></a>SQL と Azure AD のログインに適用可能な引数

*login_name*: 変更する [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログインの名前を指定します。 Azure AD ログインは user@domain として指定する必要があります。 たとえば、john.smith@contoso.com、または Azure AD グループまたはアプリケーション名として指定します。 Azure AD ログインの場合、*login_name* は master データベースで作成された既存の Azure AD ログインに対応している必要があります。

ENABLE | DISABLE: このログインを有効にするか無効にするかを指定します。 ログインを無効にしても、既に接続されているログインの動作には影響しません。 (`KILL` ステートメントを使用して、既存の接続を終了します。)無効にしたログインの権限を保持したまま、偽装を継続することができます。

DEFAULT_DATABASE **=** _database_: ログインに割り当てられる既定のデータベースを指定します。

DEFAULT_LANGUAGE **=** _language_: ログインに割り当てられる既定の言語を指定します。 すべての SQL Database ログインの既定の言語は英語で、変更できません。 Linux では、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の `sa` ログインの既定の言語は英語ですが、変更することができます。

### <a name="arguments-applicable-only-to-sql-logins"></a>SQL ログインのみに適用可能な引数

PASSWORD **='** _password_ **'** : [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログインにのみ適用されます。 変更するログインのパスワードを指定します。 パスワードでは大文字と小文字が区別されます。 パスワードは、Azure AD ログインなどの外部ログインで使用されている場合にも適用されません。

SQL Database への接続を継続的にアクティブにするには、少なくとも 10 時間ごとに (データベース エンジンによって実行される) 再認証が必要です。 データベース エンジンは、最初に送信されたパスワードを使用して再認証を試行するので、ユーザー入力は不要です。 パスワードが SQL Database でリセットされた場合、接続プーリングのために接続がリセットされても、パフォーマンス上の理由から接続は再認証されません。 これは、オンプレミスの SQL Server の動作とは異なります。 接続が最初に承認された後でパスワードが変更されている場合は、接続を終了し、新しいパスワードを使用して新しい接続を行う必要があります。 KILL DATABASE CONNECTION 権限を持つユーザーは、KILL コマンドを使用して SQL Database への接続を明示的に終了できます。 詳細については、[KILL](../../t-sql/language-elements/kill-transact-sql.md) に関するページをご覧ください。

PASSWORD **=** _hashed\_password_: HASHED キーワードにのみ適用されます。 作成するログインのパスワードのハッシュ値を指定します。

HASHED: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログインにのみ適用されます。 PASSWORD 引数の後に入力されたパスワードが、ハッシュ済みであることを示します。 このオプションを選択しなかった場合、パスワードはハッシュされてからデータベースに格納されます。 このオプションは、2 つのサーバー間でログインを同期する場合にのみ使用してください。 パスワードを定期的に変更する場合は HASHED オプションを使用しないでください。

OLD_PASSWORD **='** _oldpassword_ **'** : [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログインにのみ適用されます。 新しいパスワードを割り当てるログインの、現在のパスワードを指定します。 パスワードでは大文字と小文字が区別されます。

MUST_CHANGE<br>
[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログインにのみ適用されます。 このオプションを指定した場合、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] で変更後のログインを最初に使用するときには新しいパスワードの入力が求められます。

NAME = *login_name*: ログインの名前を変更する場合、新しい名前を指定します。 ログインが Windows ログインの場合は、新しい名前に対応する Windows プリンシパルの SID と、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 内のログインに関連付けられている SID が一致する必要があります。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログインの新しい名前には、円記号 (\\) は使用できません。

CHECK_EXPIRATION = { ON | **OFF** } [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログインにのみ適用されます。 このログインに、パスワードの有効期限ポリシーを適用するかどうかを指定します。 既定値は OFF です。

CHECK_POLICY **=** { **ON** | OFF }: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログインにのみ適用されます。 このログインに、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] を実行しているコンピューターの、Windows のパスワード ポリシーを適用するかどうかを指定します。 既定値は ON です。

CREDENTIAL = *credential_name*: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログインにマップする資格情報の名前を指定します。 この資格情報は、サーバー内に既に存在している必要があります。 詳細については、[資格情報](../../relational-databases/security/authentication-access/credentials-database-engine.md)に関するページをご覧ください。 資格情報を sa ログインにマップすることはできません。

NO CREDENTIAL: ログインからサーバー資格情報への既存のマッピングをすべて削除します。 詳細については、[資格情報](../../relational-databases/security/authentication-access/credentials-database-engine.md)に関するページをご覧ください。

UNLOCK: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログインにのみ適用されます。 ロックされているログインのロックを解除する必要があることを指定します。

ADD CREDENTIAL: 拡張キー管理 (EKM) プロバイダー資格情報をログインに追加します。 詳しくは、「[拡張キー管理 (EKM)](../../relational-databases/security/encryption/extensible-key-management-ekm.md)」をご覧ください。

DROP CREDENTIAL: ログインから拡張キー管理 (EKM) プロバイダー資格情報を削除します。 詳しくは、「[拡張キー管理 (EKM)](../../relational-databases/security/encryption/extensible-key-management-ekm.md)」をご覧ください。

## <a name="remarks"></a>解説

CHECK_POLICY が ON に設定されている場合、HASHED 引数は使用できません。

CHECK_POLICY を ON に変更した場合、次の動作が発生します。

- パスワードの履歴が、現在のパスワード ハッシュの値に初期化されます。

  CHECK_POLICY を OFF に変更した場合、次の動作が発生します。

- CHECK_EXPIRATION も OFF に設定されます。
- パスワード履歴は消去されます。
- 値 *lockout_time* がリセットされます。

MUST_CHANGE が指定された場合、CHECK_EXPIRATION および CHECK_POLICY は ON に設定されなければなりません。 ON に設定しない場合、ステートメントは失敗します。

CHECK_POLICY を OFF に設定した場合、CHECK_EXPIRATION を ON に設定することはできません。 このオプションの組み合わせで ALTER LOGIN ステートメントを実行すると、ステートメントは失敗します。

ALTER_LOGIN を DISABLE 引数と共に使用して Windows グループへのアクセスを拒否することはできません。 これは仕様です。 たとえば、ALTER_LOGIN [*domain\group*] DISABLE を実行すると次のエラー メッセージが返されます。

`"Msg 15151, Level 16, State 1, Line 1
"Cannot alter the login '*Domain\Group*', because it does not exist or you do not have permission."`

[!INCLUDE[ssSDS](../../includes/sssds-md.md)] では、接続の認証に必要なログイン データおよびサーバー レベルのファイアウォール規則は、各データベースで一時的にキャッシュされます。 このキャッシュは定期的に更新されます。 認証キャッシュを強制的に更新し、データベースに最新バージョンのログイン テーブルがあることを確認するには、[DBCC FLUSHAUTHCACHE](../../t-sql/database-console-commands/dbcc-flushauthcache-transact-sql.md) を実行します。

## <a name="permissions"></a>アクセス許可

ALTER ANY LOGIN 権限が必要です。

CREDENTIAL オプションを使用する場合は、ALTER ANY CREDENTIAL 権限も必要です。

変更するログインが固定サーバー ロール **sysadmin** のメンバーであるか、ログインに CONTROL SERVER 権限が与えられている場合、次の変更を行うには CONTROL SERVER 権限も必要になります。

- 以前のパスワードを指定せずにパスワードをリセットする。
- MUST_CHANGE、CHECK_POLICY、または CHECK_EXPIRATION を有効にする。
- ログイン名を変更する。
- ログインを有効または無効にする。
- ログインを別の資格情報にマップする。

プリンシパルでは、その独自のログインのパスワード、既定の言語、および既定のデータベースを変更できます。

`sysadmin` 特権を持つ SQL プリンシパルのみが Azure AD ログインに対して ALTER LOGIN コマンドを実行できます。

## <a name="examples"></a>例

これらの例には、他の SQL 製品の使用例も含まれます。 上記でどの引数がサポートされているかを確認してください。

### <a name="a-enabling-a-disabled-login"></a>A. 無効なログインを有効にする

次の例では、ログイン `Mary5` を有効にします。

```sql
ALTER LOGIN Mary5 ENABLE;
```

### <a name="b-changing-the-password-of-a-login"></a>B. ログインのパスワードを変更する

次の例では、ログイン `Mary5` のパスワードを強力なパスワードに変更します。

```sql
ALTER LOGIN Mary5 WITH PASSWORD = '<enterStrongPasswordHere>';
```

### <a name="c-changing-the-name-of-a-login"></a>C. ログインの名前を変更する

次の例では、ログイン `Mary5` の名前を `John2` に変更します。

```sql
ALTER LOGIN Mary5 WITH NAME = John2;
```

### <a name="d-mapping-a-login-to-a-credential"></a>D. ログインを資格情報にマップする

次の例では、ログイン `John2` を資格情報 `Custodian04` にマップします。

```sql
ALTER LOGIN John2 WITH CREDENTIAL = Custodian04;
```

### <a name="e-mapping-a-login-to-an-extensible-key-management-credential"></a>E. ログインを拡張キー管理資格情報にマップする

次の例では、ログイン `Mary5` を EKM 資格情報 `EKMProvider1` にマップします。

**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降、および Azure SQL Managed Instance。

```sql
ALTER LOGIN Mary5
ADD CREDENTIAL EKMProvider1;
GO
```

### <a name="f-unlocking-a-login"></a>F. ログインのロックを解除する

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログインのロックを解除するには、\*\*\*\* を必要なアカウントのパスワードに置き換えて、次のステートメントを実行します。

```sql
ALTER LOGIN [Mary5] WITH PASSWORD = '****' UNLOCK ;

GO
```

パスワードを変更しないでログインのロックを解除するには、チェック ポリシーをオフにしてからもう一度オンにします。

```sql
ALTER LOGIN [Mary5] WITH CHECK_POLICY = OFF;
ALTER LOGIN [Mary5] WITH CHECK_POLICY = ON;
GO
```

### <a name="g-changing-the-password-of-a-login-using-hashed"></a>G. HASHED を使用してログインのパスワードを変更する

次の例では、`TestUser` ログインのパスワードを既にハッシュされた値に変更します。

**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降、および Azure SQL Managed Instance。

```sql
ALTER LOGIN TestUser WITH
PASSWORD = 0x01000CF35567C60BFB41EBDE4CF700A985A13D773D6B45B90900 HASHED ;
GO
```

### <a name="h-disabling-the-login-of-an-azure-ad-user"></a>H. Azure AD ユーザーのログインを無効にする

次の例では、Azure AD ユーザー joe@contoso.com のログインを無効にします。

```sql
ALTER LOGIN [joe@contoso.com] DISABLE
```

## <a name="see-also"></a>参照

- [資格情報](../../relational-databases/security/authentication-access/credentials-database-engine.md)
- [CREATE LOGIN](../../t-sql/statements/create-login-transact-sql.md)
- [DROP LOGIN](../../t-sql/statements/drop-login-transact-sql.md)
- [CREATE CREDENTIAL](../../t-sql/statements/create-credential-transact-sql.md)
- [EVENTDATA](../../t-sql/functions/eventdata-transact-sql.md)
- [拡張キー管理 (EKM)](../../relational-databases/security/encryption/extensible-key-management-ekm.md)

::: moniker-end
::: moniker range="=azure-sqldw-latest"

:::row:::
    :::column:::
        [SQL Server](alter-login-transact-sql.md?view=sql-server-ver15&preserve-view=true)
    :::column-end:::
    :::column:::
        [SQL Database](alter-login-transact-sql.md?view=azuresqldb-current&preserve-view=true)
    :::column-end:::
    :::column:::
        [SQL Managed Instance](alter-login-transact-sql.md?view=azuresqldb-mi-current&preserve-view=true)
    :::column-end:::
    :::column:::
        **_\* Azure Synapse<br />Analytics \*_**
    :::column-end:::
    :::column:::
        [Analytics Platform<br />System (PDW)](alter-login-transact-sql.md?view=aps-pdw-2016&preserve-view=true)
    :::column-end:::
:::row-end:::

&nbsp;

## <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

## <a name="syntax"></a>構文

```syntaxsql
-- Syntax for Azure Synapse

ALTER LOGIN login_name
  {
      <status_option>
    | WITH <set_option> [ ,.. .n ]
  }
[;]

<status_option> ::=
    ENABLE | DISABLE
  
<set_option> ::=
    PASSWORD ='password'
    [
      OLD_PASSWORD ='oldpassword'
    ]
    | NAME = login_name
```

## <a name="arguments"></a>引数

*login_name*: 変更する [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログインの名前を指定します。 ドメイン ログインは角かっこで囲み、[domain\user] の形式で表す必要があります。

ENABLE | DISABLE: このログインを有効にするか無効にするかを指定します。 ログインを無効にしても、既に接続されているログインの動作には影響しません。 (`KILL` ステートメントを使用して、既存の接続を終了します。)無効にしたログインの権限を保持したまま、偽装を継続することができます。

PASSWORD **='** _password_ **'** : [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログインにのみ適用されます。 変更するログインのパスワードを指定します。 パスワードでは大文字と小文字が区別されます。

SQL Database への接続を継続的にアクティブにするには、少なくとも 10 時間ごとに (データベース エンジンによって実行される) 再認証が必要です。 データベース エンジンは、最初に送信されたパスワードを使用して再認証を試行するので、ユーザー入力は不要です。 パスワードが SQL Database でリセットされた場合、接続プーリングのために接続がリセットされても、パフォーマンス上の理由から接続は再認証されません。 これは、オンプレミスの SQL Server の動作とは異なります。 接続が最初に承認された後でパスワードが変更されている場合は、接続を終了し、新しいパスワードを使用して新しい接続を行う必要があります。 KILL DATABASE CONNECTION 権限を持つユーザーは、KILL コマンドを使用して SQL Database への接続を明示的に終了できます。 詳細については、[KILL](../../t-sql/language-elements/kill-transact-sql.md) に関するページをご覧ください。

> [!IMPORTANT]
> ログイン (または包含データベース ユーザー) が接続して認証されると、接続にはログインに関する ID 情報がキャッシュされます。 Windows 認証ログインの場合、これには Windows グループのメンバーシップに関する情報も含まれます。 接続が維持されている限り、ログインの ID が認証された状態は継続します。 パスワードのリセットや Windows グループのメンバーシップの変更など、ID に関する変更を適用するには、認証機関 (Windows または [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]) からログオフしてもう一度ログインする必要があります。 **sysadmin** 固定サーバー ロールのメンバーまたは **ALTER ANY CONNECTION** 権限を持つすべてのログインは、 **KILL** コマンドを使用して接続を終了し、ログインの再接続を強制することができます。 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] でオブジェクト エクスプ ローラーおよびクエリ エディター ウィンドウに複数の接続を開くときに、接続情報を再利用できます。 再接続を強制するには、すべての接続を閉じます。

OLD_PASSWORD **='** _oldpassword_ **'** : [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログインにのみ適用されます。 新しいパスワードを割り当てるログインの、現在のパスワードを指定します。 パスワードでは大文字と小文字が区別されます。

NAME = *login_name*: ログインの名前を変更する場合、新しい名前を指定します。 Windows ログインの場合は、新しい名前に対応する Windows プリンシパルの SID と、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 内のログインに関連付けられている SID が一致する必要があります。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログインの新しい名前には、円記号 (\\) は使用できません。

## <a name="remarks"></a>解説

[!INCLUDE[ssSDS](../../includes/sssds-md.md)] では、接続の認証に必要なログイン データおよびサーバー レベルのファイアウォール規則は、各データベースで一時的にキャッシュされます。 このキャッシュは定期的に更新されます。 認証キャッシュを強制的に更新し、データベースに最新バージョンのログイン テーブルが確実にあるようにするには、[DBCC FLUSHAUTHCACHE](../../t-sql/database-console-commands/dbcc-flushauthcache-transact-sql.md) を実行します。

## <a name="permissions"></a>アクセス許可

ALTER ANY LOGIN 権限が必要です。

変更するログインが固定サーバー ロール **sysadmin** のメンバーであるか、ログインに CONTROL SERVER 権限が与えられている場合、次の変更を行うには CONTROL SERVER 権限も必要になります。

- 以前のパスワードを指定せずにパスワードをリセットする。
- ログイン名を変更する。
- ログインを有効または無効にする。
- ログインを別の資格情報にマップする。

プリンシパルは、独自のログインのパスワードを変更できます。

## <a name="examples"></a>例

これらの例には、他の SQL 製品の使用例も含まれます。 上記でどの引数がサポートされているかを確認してください。

### <a name="a-enabling-a-disabled-login"></a>A. 無効なログインを有効にする

次の例では、ログイン `Mary5` を有効にします。

```sql
ALTER LOGIN Mary5 ENABLE;
```

### <a name="b-changing-the-password-of-a-login"></a>B. ログインのパスワードを変更する

次の例では、ログイン `Mary5` のパスワードを強力なパスワードに変更します。

```sql
ALTER LOGIN Mary5 WITH PASSWORD = '<enterStrongPasswordHere>';
```

### <a name="c-changing-the-name-of-a-login"></a>C. ログインの名前を変更する

次の例では、ログイン `Mary5` の名前を `John2` に変更します。

```sql
ALTER LOGIN Mary5 WITH NAME = John2;
```

### <a name="d-mapping-a-login-to-a-credential"></a>D. ログインを資格情報にマップする

次の例では、ログイン `John2` を資格情報 `Custodian04` にマップします。

```sql
ALTER LOGIN John2 WITH CREDENTIAL = Custodian04;
```

### <a name="e-mapping-a-login-to-an-extensible-key-management-credential"></a>E. ログインを拡張キー管理資格情報にマップする

次の例では、ログイン `Mary5` を EKM 資格情報 `EKMProvider1` にマップします。

**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。

```sql
ALTER LOGIN Mary5
ADD CREDENTIAL EKMProvider1;
GO
```

### <a name="f-unlocking-a-login"></a>F. ログインのロックを解除する

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログインのロックを解除するには、\*\*\*\* を必要なアカウントのパスワードに置き換えて、次のステートメントを実行します。

```sql
ALTER LOGIN [Mary5] WITH PASSWORD = '****' UNLOCK ;

GO
```

パスワードを変更しないでログインのロックを解除するには、チェック ポリシーをオフにしてからもう一度オンにします。

```sql
ALTER LOGIN [Mary5] WITH CHECK_POLICY = OFF;
ALTER LOGIN [Mary5] WITH CHECK_POLICY = ON;
GO
```

### <a name="g-changing-the-password-of-a-login-using-hashed"></a>G. HASHED を使用してログインのパスワードを変更する

次の例では、`TestUser` ログインのパスワードを既にハッシュされた値に変更します。

**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。

```sql
ALTER LOGIN TestUser WITH
PASSWORD = 0x01000CF35567C60BFB41EBDE4CF700A985A13D773D6B45B90900 HASHED ;
GO
```

## <a name="see-also"></a>参照

- [資格情報](../../relational-databases/security/authentication-access/credentials-database-engine.md)
- [CREATE LOGIN](../../t-sql/statements/create-login-transact-sql.md)
- [DROP LOGIN](../../t-sql/statements/drop-login-transact-sql.md)
- [CREATE CREDENTIAL](../../t-sql/statements/create-credential-transact-sql.md)
- [EVENTDATA](../../t-sql/functions/eventdata-transact-sql.md)
- [拡張キー管理 (EKM)](../../relational-databases/security/encryption/extensible-key-management-ekm.md)

::: moniker-end
::: moniker range=">=aps-pdw-2016"

:::row:::
    :::column:::
        [SQL Server](alter-login-transact-sql.md?view=sql-server-ver15&preserve-view=true)
    :::column-end:::
    :::column:::
        [SQL Database](alter-login-transact-sql.md?view=azuresqldb-current&preserve-view=true)
    :::column-end:::
    :::column:::
        [SQL Managed Instance](alter-login-transact-sql.md?view=azuresqldb-mi-current&preserve-view=true)
    :::column-end:::
    :::column:::
        [Azure Synapse<br />Analytics](alter-login-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)
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

ALTER LOGIN login_name
    {
    <status_option>
    | WITH <set_option> [ ,... ]
    }

<status_option> ::=ENABLE | DISABLE

<set_option> ::=
    PASSWORD ='password'
    [
      OLD_PASSWORD ='oldpassword'
      | <password_option> [<password_option> ]
    ]
    | NAME = login_name
    | CHECK_POLICY = { ON | OFF }
    | CHECK_EXPIRATION = { ON | OFF }

<password_option> ::=
    MUST_CHANGE | UNLOCK
```

## <a name="arguments"></a>引数

*login_name*: 変更する [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログインの名前を指定します。 ドメイン ログインは角かっこで囲み、[domain\user] の形式で表す必要があります。

ENABLE | DISABLE: このログインを有効にするか無効にするかを指定します。 ログインを無効にしても、既に接続されているログインの動作には影響しません。 (`KILL` ステートメントを使用して、既存の接続を終了します。)無効にしたログインの権限を保持したまま、偽装を継続することができます。

PASSWORD **='** _password_ **'** : [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログインにのみ適用されます。 変更するログインのパスワードを指定します。 パスワードでは大文字と小文字が区別されます。

> [!IMPORTANT]
> ログイン (または包含データベース ユーザー) が接続して認証されると、接続にはログインに関する ID 情報がキャッシュされます。 Windows 認証ログインの場合、これには Windows グループのメンバーシップに関する情報も含まれます。 接続が維持されている限り、ログインの ID が認証された状態は継続します。 パスワードのリセットや Windows グループのメンバーシップの変更など、ID に関する変更を適用するには、認証機関 (Windows または [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]) からログオフしてもう一度ログインする必要があります。 **sysadmin** 固定サーバー ロールのメンバーまたは **ALTER ANY CONNECTION** 権限を持つすべてのログインは、 **KILL** コマンドを使用して接続を終了し、ログインの再接続を強制することができます。 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] でオブジェクト エクスプ ローラーおよびクエリ エディター ウィンドウに複数の接続を開くときに、接続情報を再利用できます。 再接続を強制するには、すべての接続を閉じます。

OLD_PASSWORD **='** _oldpassword_ **'** : [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログインにのみ適用されます。 新しいパスワードを割り当てるログインの、現在のパスワードを指定します。 パスワードでは大文字と小文字が区別されます。

MUST_CHANGE: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログインにのみ適用されます。 このオプションを指定した場合、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] で変更後のログインを最初に使用するときには新しいパスワードの入力が求められます。

NAME = *login_name*: ログインの名前を変更する場合、新しい名前を指定します。 ログインが Windows ログインの場合は、新しい名前に対応する Windows プリンシパルの SID と、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 内のログインに関連付けられている SID が一致する必要があります。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログインの新しい名前には、円記号 (\\) は使用できません。

CHECK_EXPIRATION = { ON | **OFF** } [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログインにのみ適用されます。 このログインに、パスワードの有効期限ポリシーを適用するかどうかを指定します。 既定値は OFF です。

CHECK_POLICY **=** { **ON** | OFF }: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログインにのみ適用されます。 このログインに、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] を実行しているコンピューターの、Windows のパスワード ポリシーを適用するかどうかを指定します。 既定値は ON です。

UNLOCK: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログインにのみ適用されます。 ロックされているログインのロックを解除する必要があることを指定します。

## <a name="remarks"></a>解説

CHECK_POLICY が ON に設定されている場合、HASHED 引数は使用できません。

CHECK_POLICY を ON に変更した場合、次の動作が発生します。

- パスワードの履歴が、現在のパスワード ハッシュの値に初期化されます。

  CHECK_POLICY を OFF に変更した場合、次の動作が発生します。

- CHECK_EXPIRATION も OFF に設定されます。
- パスワード履歴は消去されます。
- 値 *lockout_time* がリセットされます。

MUST_CHANGE が指定された場合、CHECK_EXPIRATION および CHECK_POLICY は ON に設定されなければなりません。 ON に設定しない場合、ステートメントは失敗します。

CHECK_POLICY を OFF に設定した場合、CHECK_EXPIRATION を ON に設定することはできません。 このオプションの組み合わせで ALTER LOGIN ステートメントを実行すると、ステートメントは失敗します。

ALTER_LOGIN を DISABLE 引数と共に使用して Windows グループへのアクセスを拒否することはできません。 これは仕様です。 たとえば、ALTER_LOGIN [*domain\group*] DISABLE を実行すると次のエラー メッセージが返されます。

`"Msg 15151, Level 16, State 1, Line 1
"Cannot alter the login '*Domain\Group*', because it does not exist or you do not have permission."`

[!INCLUDE[ssSDS](../../includes/sssds-md.md)] では、接続の認証に必要なログイン データおよびサーバー レベルのファイアウォール規則は、各データベースで一時的にキャッシュされます。 このキャッシュは定期的に更新されます。 認証キャッシュを強制的に更新し、データベースに最新バージョンのログイン テーブルがあることを確認するには、[DBCC FLUSHAUTHCACHE](../../t-sql/database-console-commands/dbcc-flushauthcache-transact-sql.md) を実行します。

## <a name="permissions"></a>アクセス許可

ALTER ANY LOGIN 権限が必要です。

CREDENTIAL オプションを使用する場合は、ALTER ANY CREDENTIAL 権限も必要です。

変更するログインが固定サーバー ロール **sysadmin** のメンバーであるか、ログインに CONTROL SERVER 権限が与えられている場合、次の変更を行うには CONTROL SERVER 権限も必要になります。

- 以前のパスワードを指定せずにパスワードをリセットする。
- MUST_CHANGE、CHECK_POLICY、または CHECK_EXPIRATION を有効にする。
- ログイン名を変更する。
- ログインを有効または無効にする。
- ログインを別の資格情報にマップする。

プリンシパルでは、その独自のログインのパスワード、既定の言語、および既定のデータベースを変更できます。

## <a name="examples"></a>例

これらの例には、他の SQL 製品の使用例も含まれます。 上記でどの引数がサポートされているかを確認してください。

### <a name="a-enabling-a-disabled-login"></a>A. 無効なログインを有効にする

次の例では、ログイン `Mary5` を有効にします。

```sql
ALTER LOGIN Mary5 ENABLE;
```

### <a name="b-changing-the-password-of-a-login"></a>B. ログインのパスワードを変更する

次の例では、ログイン `Mary5` のパスワードを強力なパスワードに変更します。

```sql
ALTER LOGIN Mary5 WITH PASSWORD = '<enterStrongPasswordHere>';
```

### <a name="c-changing-the-name-of-a-login"></a>C. ログインの名前を変更する

次の例では、ログイン `Mary5` の名前を `John2` に変更します。

```sql
ALTER LOGIN Mary5 WITH NAME = John2;
```

### <a name="d-mapping-a-login-to-a-credential"></a>D. ログインを資格情報にマップする

次の例では、ログイン `John2` を資格情報 `Custodian04` にマップします。

```sql
ALTER LOGIN John2 WITH CREDENTIAL = Custodian04;
```

### <a name="e-mapping-a-login-to-an-extensible-key-management-credential"></a>E. ログインを拡張キー管理資格情報にマップする

次の例では、ログイン `Mary5` を EKM 資格情報 `EKMProvider1` にマップします。

**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。

```sql
ALTER LOGIN Mary5
ADD CREDENTIAL EKMProvider1;
GO
```

### <a name="f-unlocking-a-login"></a>F. ログインのロックを解除する

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログインのロックを解除するには、\*\*\*\* を必要なアカウントのパスワードに置き換えて、次のステートメントを実行します。

```sql
ALTER LOGIN [Mary5] WITH PASSWORD = '****' UNLOCK ;

GO
```

パスワードを変更しないでログインのロックを解除するには、チェック ポリシーをオフにしてからもう一度オンにします。

```sql
ALTER LOGIN [Mary5] WITH CHECK_POLICY = OFF;
ALTER LOGIN [Mary5] WITH CHECK_POLICY = ON;
GO
```

### <a name="g-changing-the-password-of-a-login-using-hashed"></a>G. HASHED を使用してログインのパスワードを変更する

次の例では、`TestUser` ログインのパスワードを既にハッシュされた値に変更します。

**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。

```sql
ALTER LOGIN TestUser WITH
PASSWORD = 0x01000CF35567C60BFB41EBDE4CF700A985A13D773D6B45B90900 HASHED ;
GO
```

## <a name="see-also"></a>参照

- [資格情報](../../relational-databases/security/authentication-access/credentials-database-engine.md)
- [CREATE LOGIN](../../t-sql/statements/create-login-transact-sql.md)
- [DROP LOGIN](../../t-sql/statements/drop-login-transact-sql.md)
- [CREATE CREDENTIAL](../../t-sql/statements/create-credential-transact-sql.md)
- [EVENTDATA](../../t-sql/functions/eventdata-transact-sql.md)
- [拡張キー管理 (EKM)](../../relational-databases/security/encryption/extensible-key-management-ekm.md)

::: moniker-end
