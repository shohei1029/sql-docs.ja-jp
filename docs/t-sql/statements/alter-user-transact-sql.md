---
description: ALTER USER (Transact-SQL)
title: ALTER USER (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 01/10/2020
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- ALTER_USER_TSQL
- ALTER USER
dev_langs:
- TSQL
helpviewer_keywords:
- modifying database users
- ALTER USER statement
- renaming databases
- schemas [SQL Server], default
- database user names [SQL Server]
- names [SQL Server], database users
- default schemas
- modifying default schemas
ms.assetid: 344fc6ce-a008-47c8-a02e-47fae66cc590
author: VanMSFT
ms.author: vanto
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 16859439d0ff11e167c60bc772212dbc5494f473
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104748782"
---
# <a name="alter-user-transact-sql"></a>ALTER USER (Transact-SQL)

データベース ユーザーの名前、またはデータベースの既定のスキーマを変更します。

 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)

[!INCLUDE[select-product](../../includes/select-product.md)]

::: moniker range=">=sql-server-2016||>=sql-server-linux-2017"

:::row:::
    :::column:::
        **_\* SQL Server \*_** &nbsp;
    :::column-end:::
    :::column:::
        [SQL Database](alter-user-transact-sql.md?view=azuresqldb-current&preserve-view=true)
    :::column-end:::
    :::column:::
        [SQL Managed Instance](alter-user-transact-sql.md?view=azuresqldb-mi-current&preserve-view=true)
    :::column-end:::
    :::column:::
        [Azure Synapse<br />Analytics](alter-user-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)
    :::column-end:::
    :::column:::
        [Analytics Platform<br />System (PDW)](alter-user-transact-sql.md?view=aps-pdw-2016&preserve-view=true)
    :::column-end:::
:::row-end:::

&nbsp;

## <a name="sql-server"></a>SQL Server

## <a name="syntax"></a>構文

```syntaxsql
-- Syntax for SQL Server

ALTER USER userName
 WITH <set_item> [ ,...n ]
[;]

<set_item> ::=
NAME = newUserName
| DEFAULT_SCHEMA = { schemaName | NULL }
| LOGIN = loginName
| PASSWORD = 'password' [ OLD_PASSWORD = 'oldpassword' ]
| DEFAULT_LANGUAGE = { NONE | <lcid> | <language name> | <language alias> }
| ALLOW_ENCRYPTED_VALUE_MODIFICATIONS = [ ON | OFF ]
```

## <a name="arguments"></a>引数

 *userName*: データベース内でユーザーを識別する名前を指定します。

 LOGIN **=** _loginName_: ユーザーのセキュリティ識別子 (SID) を別のログインの SID に一致するように変更することで、ユーザーを別のログインに再マッピングします。

 NAME **=** _newUserName_: このユーザーの新しい名前を指定します。 *newUserName* は現在のデータベースに存在しない名前にする必要があります。

 DEFAULT_SCHEMA **=** { *schemaName* | NULL } このユーザー用のオブジェクトの名前を解決するときに、サーバーで最初に検索されるスキーマを指定します。 既定のスキーマを NULL に設定すると、既定のスキーマが Windows グループから削除されます。 Windows ユーザーでは NULL オプションは使用できません。

 PASSWORD **=** '*password*' **適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降、[!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)]。

 変更するユーザーのパスワードを指定します。 パスワードでは大文字と小文字が区別されます。

> [!NOTE]
> このオプションは、包含ユーザーに対してのみ使用できます。 詳しくは、「[包含データベース](../../relational-databases/databases/contained-databases.md)」および「[sp_migrate_user_to_contained &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-migrate-user-to-contained-transact-sql.md)」をご覧ください。

 OLD_PASSWORD **=** _'oldpassword'_ **適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降、[!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)]。

 '*password*' で置き換える現在のユーザー パスワードです。 パスワードでは大文字と小文字が区別されます。 **ALTER ANY USER** 権限がない場合、パスワードを変更するには、*OLD_PASSWORD* が必要です。 *OLD_PASSWORD* を必須にすることで、**IMPERSONATION** 権限を持つユーザーによるパスワードの変更を防止できます。

> [!NOTE]
> このオプションは、包含ユーザーに対してのみ使用できます。

 DEFAULT_LANGUAGE **=** _{ NONE \| \<lcid> \| \<language name> \| \<language alias> }_ **適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降。

 ユーザーに割り当てる既定の言語を指定します。 このオプションを NONE に設定した場合、既定の言語はデータベースの現在の既定の言語に設定されます。 データベースの既定の言語が将来変更されても、ユーザーの既定の言語は変更されません。 *DEFAULT_LANGUAGE* には、ローカル ID (LCID)、言語の名前、または言語の別名を指定できます。

> [!NOTE]
> このオプションは包含データベースでのみ指定でき、また、包含ユーザーに対してのみ指定できます。

 ALLOW_ENCRYPTED_VALUE_MODIFICATIONS = [ ON | **OFF** ] **適用対象**: [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 以降、[!INCLUDE[ssSDS](../../includes/sssds-md.md)]。

 一括コピー操作でのサーバーの暗号化メタデータ チェックを抑制します。 これによりユーザーは、データを暗号化解除することなく、テーブルまたはデータベース間で暗号化データを一括コピーできます。 既定値は OFF です。

> [!WARNING]
> このオプションを不適切に使用すると、データが破損する場合があります。 詳細については、「[Always Encrypted で保護された機微なデータの移行](../../relational-databases/security/encryption/migrate-sensitive-data-protected-by-always-encrypted.md)」を参照してください。

## <a name="remarks"></a>解説

 既定のスキーマは、このデータベース ユーザー用のオブジェクトの名前を解決するときに、サーバーで最初に検索されるスキーマになります。 特に指定しない限り、このデータベース ユーザーによって作成されたオブジェクトの所有者になるのは、既定のスキーマです。

 ユーザーに既定のスキーマが指定されている場合は、その既定のスキーマが使用されます。 ユーザーに既定のスキーマが指定されておらず、そのユーザーが所属しているグループに既定のスキーマが指定されている場合は、グループの既定のスキーマが使用されます。 ユーザーに既定のスキーマが指定されておらず、そのユーザーが複数のグループに所属している場合、そのユーザーの既定のスキーマは、最も小さい principle_id と明示的に設定された既定のスキーマを持つ Windows グループのスキーマになります。 ユーザーに対する既定のスキーマを決定できない場合は、**dbo** スキーマが使用されます。

 DEFAULT_SCHEMA には、現在データベースに存在しないスキーマも設定できます。 したがって、スキーマが作成される前に DEFAULT_SCHEMA をユーザーに割り当てることができます。

 証明書または非対称キーにマップされているユーザーに対して、DEFAULT_SCHEMA を指定することはできません。

> [!IMPORTANT]
> ユーザーが固定サーバー ロール **sysadmin** のメンバーである場合、DEFAULT_SCHEMA の値は無視されます。 固定サーバー ロール **sysadmin** のすべてのメンバーには、`dbo` の既定のスキーマが割り当てられます。

 Windows のログインまたはグループにマップされているユーザーの名前を変更できるのは、新しいユーザー名の SID とデータベースに記録されている SID が一致する場合だけです。 この条件により、データベースにおける Windows ログインのなりすましを防止できます。

 WITH LOGIN 句を使用すると、ユーザーを別のログインに再マップできます。 ログインのないユーザー、証明書にマップされているユーザー、非対称キーにマップされているユーザーを、この句を使って再マップすることはできません。 SQL ユーザーおよび Windows ユーザー (またはグループ) のみ再マップできます。 WITH LOGIN 句は、Windows アカウントを [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログインに変更するなど、ユーザーの種類の変更には使用できません。

 次の条件を満たす場合、ユーザーの名前はログイン名に自動的に変更されます。

- ユーザーが Windows ユーザーである。

- 名前が Windows 名である (円記号を含む)。

- 新しい名前が指定されていない。

- 現在の名前がログイン名とは異なる。

 そうでない場合は、呼び出し元が NAME 句を追加で呼び出さない限り、ユーザーの名前は変更されません。

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のログイン、証明書、非対称キーにマップされているユーザーの名前には、円記号 (\\) を含めることはできません。

> [!CAUTION]
> [!INCLUDE[ssCautionUserSchema](../../includes/sscautionuserschema-md.md)]

## <a name="security"></a>セキュリティ

> [!NOTE]
> **ALTER ANY USER** 権限を持つユーザーは、任意のユーザーの既定のスキーマを変更できます。 変更されたスキーマを所有するユーザーは、知らずに間違ったテーブルからデータを選択したり、間違ったスキーマからコードを実行する可能性があります。

### <a name="permissions"></a>アクセス許可

 ユーザーの名前を変更するには、**ALTER ANY USER** 権限が必要です。

 ターゲットを変更するには、ユーザーのログインにデータベースの **CONTROL** 権限が必要です。

 データベースに対する **CONTROL** 権限を持つユーザーのユーザー名を変更するには、データベースに対する **CONTROL** 権限が必要です。

 既定のスキーマまたは言語を変更するには、ユーザーに対する **ALTER** 権限が必要です。 ユーザーは自分が所有する既定のスキーマまたは言語を変更できます。

## <a name="examples"></a>例

すべての例は、ユーザー データベースで実行されます。

### <a name="a-changing-the-name-of-a-database-user"></a>A. データベース ユーザーの名前を変更する

 次の例では、データベース ユーザー `Mary5` の名前を `Mary51` に変更します。

```sql
ALTER USER Mary5 WITH NAME = Mary51;
GO
```

### <a name="b-changing-the-default-schema-of-a-user"></a>B. ユーザーの既定のスキーマを変更する

 次の例では、ユーザー `Mary51` の既定のスキーマを `Purchasing` に変更します。

```sql
ALTER USER Mary51 WITH DEFAULT_SCHEMA = Purchasing;
GO
```

### <a name="c-changing-several-options-at-once"></a>C. 複数のオプションを一度に変更する

 次の例では、包含データベース ユーザーに対する複数のオプションを 1 つのステートメントで変更します。

**適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降。

```sql
ALTER USER Philip
WITH NAME = Philipe
, DEFAULT_SCHEMA = Development
, PASSWORD = 'W1r77TT98%ab@#' OLD_PASSWORD = 'New Devel0per'
, DEFAULT_LANGUAGE= French ;
GO
```

## <a name="see-also"></a>関連項目

- [CREATE USER &#40;Transact-SQL&#41;](../../t-sql/statements/create-user-transact-sql.md)
- [DROP USER (Transact-SQL)](../../t-sql/statements/drop-user-transact-sql.md)
- [包含データベース](../../relational-databases/databases/contained-databases.md)
- [EVENTDATA &#40;Transact-SQL&#41;](../../t-sql/functions/eventdata-transact-sql.md)
- [sp_migrate_user_to_contained &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-migrate-user-to-contained-transact-sql.md)

::: moniker-end
::: moniker range="=azuresqldb-current"

:::row:::
    :::column:::
        [SQL Server](alter-user-transact-sql.md?view=sql-server-ver15&preserve-view=true)
    :::column-end:::
    :::column:::
        **_\* SQL Database \*_**
    :::column-end:::
    :::column:::
        [SQL Managed Instance](alter-user-transact-sql.md?view=azuresqldb-mi-current&preserve-view=true)
    :::column-end:::
    :::column:::
        [Azure Synapse<br />Analytics](alter-user-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)
    :::column-end:::
    :::column:::
        [Analytics Platform<br />System (PDW)](alter-user-transact-sql.md?view=aps-pdw-2016&preserve-view=true)
    :::column-end:::
:::row-end:::

&nbsp;

## <a name="sql-database"></a>SQL Database

## <a name="syntax"></a>構文

```syntaxsql
-- Syntax for Azure SQL Database

ALTER USER userName
 WITH <set_item> [ ,...n ]

<set_item> ::=
NAME = newUserName
| DEFAULT_SCHEMA = schemaName
| LOGIN = loginName
| ALLOW_ENCRYPTED_VALUE_MODIFICATIONS = [ ON | OFF ]
[;]

-- Azure SQL Database Update Syntax
ALTER USER userName
 WITH <set_item> [ ,...n ]
[;]

<set_item> ::=
NAME = newUserName
| DEFAULT_SCHEMA = { schemaName | NULL }
| LOGIN = loginName
| PASSWORD = 'password' [ OLD_PASSWORD = 'oldpassword' ]
| ALLOW_ENCRYPTED_VALUE_MODIFICATIONS = [ ON | OFF ]

-- SQL Database syntax when connected to a federation member
ALTER USER userName
 WITH <set_item> [ ,... n ]
[;]

<set_item> ::=
 NAME = newUserName
```

## <a name="arguments"></a>引数

 *userName*: データベース内でユーザーを識別する名前を指定します。

 LOGIN **=** _loginName_: ユーザーのセキュリティ識別子 (SID) を別のログインの SID に一致するように変更することで、ユーザーを別のログインに再マッピングします。

 ALTER USER ステートメントが SQL のバッチ内の唯一のステートメントである場合、Azure SQL Database では WITH LOGIN 句がサポートされます。 ALTER USER ステートメントが SQL バッチ内の唯一のステートメントではない場合、または動的 SQL で実行されている場合、WITH LOGIN 句はサポートされません。

 NAME **=** _newUserName_: このユーザーの新しい名前を指定します。 *newUserName* は現在のデータベースに存在しない名前にする必要があります。

 DEFAULT_SCHEMA **=** { *schemaName* | NULL } このユーザー用のオブジェクトの名前を解決するときに、サーバーで最初に検索されるスキーマを指定します。 既定のスキーマを NULL に設定すると、既定のスキーマが Windows グループから削除されます。 Windows ユーザーでは、NULL オプションは使用できません。

 PASSWORD **=** '*password*' **適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降、[!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)]。

 変更するユーザーのパスワードを指定します。 パスワードでは大文字と小文字が区別されます。

> [!NOTE]
> このオプションは、包含ユーザーに対してのみ使用できます。 詳しくは、「[包含データベース](../../relational-databases/databases/contained-databases.md)」および「[sp_migrate_user_to_contained &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-migrate-user-to-contained-transact-sql.md)」をご覧ください。

 OLD_PASSWORD **=** _'oldpassword'_ **適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降、[!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)]。

 '*password*' で置き換える現在のユーザー パスワードです。 パスワードでは大文字と小文字が区別されます。 **ALTER ANY USER** 権限がない場合、パスワードを変更するには、*OLD_PASSWORD* が必要です。 *OLD_PASSWORD* を必須にすることで、**IMPERSONATION** 権限を持つユーザーによるパスワードの変更を防止できます。

> [!NOTE]
> このオプションは、包含ユーザーに対してのみ使用できます。

 ALLOW_ENCRYPTED_VALUE_MODIFICATIONS = [ ON | **OFF** ] **適用対象**: [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 以降、[!INCLUDE[ssSDS](../../includes/sssds-md.md)]。

 一括コピー操作でのサーバーの暗号化メタデータ チェックを抑制します。 これによりユーザーは、データを暗号化解除することなく、テーブルまたはデータベース間で暗号化データを一括コピーできます。 既定値は OFF です。

> [!WARNING]
> このオプションを不適切に使用すると、データが破損する場合があります。 詳細については、「[Always Encrypted で保護された機微なデータの移行](../../relational-databases/security/encryption/migrate-sensitive-data-protected-by-always-encrypted.md)」を参照してください。

## <a name="remarks"></a>解説

 既定のスキーマは、このデータベース ユーザー用のオブジェクトの名前を解決するときに、サーバーで最初に検索されるスキーマになります。 特に指定しない限り、このデータベース ユーザーによって作成されたオブジェクトの所有者になるのは、既定のスキーマです。

 ユーザーに既定のスキーマが設定されている場合は、その既定のスキーマが使用されます。 ユーザーに既定のスキーマが指定されておらず、そのユーザーが所属しているグループに既定のスキーマが指定されている場合は、グループの既定のスキーマが使用されます。 ユーザーに既定のスキーマが指定されておらず、そのユーザーが複数のグループに所属している場合、そのユーザーの既定のスキーマは、最も小さい principle_id と明示的に設定された既定のスキーマを持つ Windows グループのスキーマになります。 ユーザーに対する既定のスキーマを決定できない場合は、**dbo** スキーマが使用されます。

 DEFAULT_SCHEMA には、現在データベースに存在しないスキーマも設定できます。 したがって、スキーマが作成される前に DEFAULT_SCHEMA をユーザーに割り当てることができます。

 証明書または非対称キーにマップされているユーザーに対して、DEFAULT_SCHEMA を指定することはできません。

> [!IMPORTANT]
> ユーザーが固定サーバー ロール **sysadmin** のメンバーである場合、DEFAULT_SCHEMA の値は無視されます。 固定サーバー ロール **sysadmin** のすべてのメンバーには、`dbo` の既定のスキーマが割り当てられます。

 Windows のログインまたはグループにマップされているユーザーの名前を変更できるのは、新しいユーザー名の SID とデータベースに記録されている SID が一致する場合だけです。 この条件により、データベースにおける Windows ログインのなりすましを防止できます。

 WITH LOGIN 句を使用すると、ユーザーを別のログインに再マップできます。 ログインのないユーザー、証明書にマップされているユーザー、非対称キーにマップされているユーザーを、この句を使って再マップすることはできません。 SQL ユーザーおよび Windows ユーザー (またはグループ) のみ再マップできます。 WITH LOGIN 句は、Windows アカウントを [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログインに変更するなど、ユーザーの種類の変更には使用できません。

 次の条件を満たす場合、ユーザーの名前はログイン名に自動的に変更されます。

- ユーザーが Windows ユーザーである。

- 名前が Windows 名である (円記号を含む)。

- 新しい名前が指定されていない。

- 現在の名前がログイン名とは異なる。

 そうでない場合は、呼び出し元が NAME 句を追加で呼び出さない限り、ユーザーの名前は変更されません。

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のログイン、証明書、非対称キーにマップされているユーザーの名前には、円記号 (\\) を含めることはできません。

> [!CAUTION]
> [!INCLUDE[ssCautionUserSchema](../../includes/sscautionuserschema-md.md)]

## <a name="security"></a>セキュリティ

> [!NOTE]
> **ALTER ANY USER** 権限を持つユーザーは、任意のユーザーの既定のスキーマを変更できます。 変更されたスキーマを所有するユーザーは、知らずに間違ったテーブルからデータを選択したり、間違ったスキーマからコードを実行する可能性があります。

### <a name="permissions"></a>アクセス許可

 ユーザーの名前を変更するには、**ALTER ANY USER** 権限が必要です。

 ターゲットを変更するには、ユーザーのログインにデータベースの **CONTROL** 権限が必要です。

 データベースに対する **CONTROL** 権限を持つユーザーのユーザー名を変更するには、データベースに対する **CONTROL** 権限が必要です。

 既定のスキーマまたは言語を変更するには、ユーザーに対する **ALTER** 権限が必要です。 ユーザーは自分が所有する既定のスキーマまたは言語を変更できます。

## <a name="examples"></a>例

すべての例は、ユーザー データベースで実行されます。

### <a name="a-changing-the-name-of-a-database-user"></a>A. データベース ユーザーの名前を変更する

 次の例では、データベース ユーザー `Mary5` の名前を `Mary51` に変更します。

```sql
ALTER USER Mary5 WITH NAME = Mary51;
GO
```

### <a name="b-changing-the-default-schema-of-a-user"></a>B. ユーザーの既定のスキーマを変更する

 次の例では、ユーザー `Mary51` の既定のスキーマを `Purchasing` に変更します。

```sql
ALTER USER Mary51 WITH DEFAULT_SCHEMA = Purchasing;
GO
```

### <a name="c-changing-several-options-at-once"></a>C. 複数のオプションを一度に変更する

 次の例では、包含データベース ユーザーに対する複数のオプションを 1 つのステートメントで変更します。

```sql
ALTER USER Philip
WITHNAME = Philipe
, DEFAULT_SCHEMA = Development
, PASSWORD = 'W1r77TT98%ab@#' OLD_PASSWORD = 'New Devel0per';
GO
```

## <a name="see-also"></a>関連項目

- [CREATE USER &#40;Transact-SQL&#41;](../../t-sql/statements/create-user-transact-sql.md)
- [DROP USER (Transact-SQL)](../../t-sql/statements/drop-user-transact-sql.md)
- [包含データベース](../../relational-databases/databases/contained-databases.md)
- [EVENTDATA &#40;Transact-SQL&#41;](../../t-sql/functions/eventdata-transact-sql.md)
- [sp_migrate_user_to_contained &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-migrate-user-to-contained-transact-sql.md)

::: moniker-end
::: moniker range="=azuresqldb-mi-current"

:::row:::
    :::column:::
        [SQL Server](alter-user-transact-sql.md?view=sql-server-ver15&preserve-view=true)
    :::column-end:::
    :::column:::
        [SQL Database](alter-user-transact-sql.md?view=azuresqldb-current&preserve-view=true)
    :::column-end:::
    :::column:::
        **_\* SQL Managed Instance \*_**
    :::column-end:::
    :::column:::
        [Azure Synapse<br />Analytics](alter-user-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)
    :::column-end:::
    :::column:::
        [Analytics Platform<br />System (PDW)](alter-user-transact-sql.md?view=aps-pdw-2016&preserve-view=true)
    :::column-end:::
:::row-end:::

&nbsp;

## <a name="azure-sql-managed-instance"></a>Azure SQL Managed Instance

## <a name="syntax"></a>構文

> [!IMPORTANT]
> Azure AD ログインのユーザーに適用するとき、Azure SQL Managed Instance では、オプション `DEFAULT_SCHEMA = { schemaName | NULL }` と `DEFAULT_LANGUAGE = { NONE | lcid | language name | language alias }` のみサポートされます
> </br> </br> Azure SQL Managed Instance に移行されたデータベース内のユーザーを再マップするために、新しい構文拡張機能が追加されました。 ALTER USER 構文を使用すると、Azure AD を使ってフェデレーションおよび同期されたドメインのデータベース ユーザーを、Azure AD ログインにマップできます。

```syntaxsql
-- Syntax for SQL Managed Instance
ALTER USER userName
 { WITH <set_item> [ ,...n ] | FROM EXTERNAL PROVIDER }
[;]

<set_item> ::=
NAME = newUserName
| DEFAULT_SCHEMA = { schemaName | NULL }
| LOGIN = loginName
| PASSWORD = 'password' [ OLD_PASSWORD = 'oldpassword' ]
| DEFAULT_LANGUAGE = { NONE | <lcid> | <language name> | <language alias> }
| ALLOW_ENCRYPTED_VALUE_MODIFICATIONS = [ ON | OFF ]

-- Users or groups that are migrated as federated and synchronized with Azure AD have the following syntax:

/** Applies to Windows users that were migrated and have the following user names:
- Windows user <domain\user>
- Windows group <domain\MyWindowsGroup>
- Windows alias <MyWindowsAlias>
**/

ALTER USER userName
 { WITH <set_item> [ ,...n ] | FROM EXTERNAL PROVIDER }
[;]

<set_item> ::=
 NAME = newUserName
| DEFAULT_SCHEMA = { schemaName | NULL }
| LOGIN = loginName
| DEFAULT_LANGUAGE = { NONE | <lcid> | <language name> | <language alias> }
```

## <a name="arguments"></a>引数

 *userName*: データベース内でユーザーを識別する名前を指定します。

 LOGIN **=** _loginName_: ユーザーのセキュリティ識別子 (SID) を別のログインの SID に一致するように変更することで、ユーザーを別のログインに再マッピングします。

 ALTER USER ステートメントが SQL のバッチ内の唯一のステートメントである場合、Azure SQL Database では WITH LOGIN 句がサポートされます。 ALTER USER ステートメントが SQL バッチ内の唯一のステートメントではない場合、または動的 SQL で実行されている場合、WITH LOGIN 句はサポートされません。

 NAME **=** _newUserName_: このユーザーの新しい名前を指定します。 *newUserName* は現在のデータベースに存在しない名前にする必要があります。

 DEFAULT_SCHEMA **=** { *schemaName* | NULL } このユーザー用のオブジェクトの名前を解決するときに、サーバーで最初に検索されるスキーマを指定します。 既定のスキーマを NULL に設定すると、既定のスキーマが Windows グループから削除されます。 Windows ユーザーでは、NULL オプションは使用できません。

 PASSWORD **=** '*password*'

 変更するユーザーのパスワードを指定します。 パスワードでは大文字と小文字が区別されます。

> [!NOTE]
> このオプションは、包含ユーザーに対してのみ使用できます。 詳しくは、「[包含データベース](../../relational-databases/databases/contained-databases.md)」および「[sp_migrate_user_to_contained &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-migrate-user-to-contained-transact-sql.md)」をご覧ください。

 OLD_PASSWORD **=** _'oldpassword'_

 '*password*' で置き換える現在のユーザー パスワードです。 パスワードでは大文字と小文字が区別されます。 **ALTER ANY USER** 権限がない場合、パスワードを変更するには、*OLD_PASSWORD* が必要です。 *OLD_PASSWORD* を必須にすることで、**IMPERSONATION** 権限を持つユーザーによるパスワードの変更を防止できます。

> [!NOTE]
> このオプションは、包含ユーザーに対してのみ使用できます。

 DEFAULT_LANGUAGE **=** _{ NONE | \<lcid> | \<language name> | \<language alias> }_

 ユーザーに割り当てる既定の言語を指定します。 このオプションを NONE に設定した場合、既定の言語はデータベースの現在の既定の言語に設定されます。 データベースの既定の言語が将来変更されても、ユーザーの既定の言語は変更されません。 *DEFAULT_LANGUAGE* には、ローカル ID (LCID)、言語の名前、または言語の別名を指定できます。

> [!NOTE]
> このオプションは包含データベースでのみ指定でき、また、包含ユーザーに対してのみ指定できます。

 ALLOW_ENCRYPTED_VALUE_MODIFICATIONS = [ ON | **OFF** ]

 一括コピー操作でのサーバーの暗号化メタデータ チェックを抑制します。 これによりユーザーは、データを暗号化解除することなく、テーブルまたはデータベース間で暗号化データを一括コピーできます。 既定値は OFF です。

> [!WARNING]
> このオプションを不適切に使用すると、データが破損する場合があります。 詳細については、「[Always Encrypted で保護された機微なデータの移行](../../relational-databases/security/encryption/migrate-sensitive-data-protected-by-always-encrypted.md)」を参照してください。

## <a name="remarks"></a>解説

 既定のスキーマは、このデータベース ユーザー用のオブジェクトの名前を解決するときに、サーバーで最初に検索されるスキーマになります。 特に指定しない限り、このデータベース ユーザーによって作成されたオブジェクトの所有者になるのは、既定のスキーマです。

 ユーザーに既定のスキーマが設定されている場合は、その既定のスキーマが使用されます。 ユーザーに既定のスキーマが指定されておらず、そのユーザーが所属しているグループに既定のスキーマが指定されている場合は、グループの既定のスキーマが使用されます。 ユーザーに既定のスキーマが指定されておらず、そのユーザーが複数のグループに所属している場合、そのユーザーの既定のスキーマは、最も小さい principle_id と明示的に設定された既定のスキーマを持つ Windows グループのスキーマになります。 ユーザーに対する既定のスキーマを決定できない場合は、**dbo** スキーマが使用されます。

 DEFAULT_SCHEMA には、現在データベースに存在しないスキーマも設定できます。 したがって、スキーマが作成される前に DEFAULT_SCHEMA をユーザーに割り当てることができます。

 証明書または非対称キーにマップされているユーザーに対して、DEFAULT_SCHEMA を指定することはできません。

> [!IMPORTANT]
> ユーザーが固定サーバー ロール **sysadmin** のメンバーである場合、DEFAULT_SCHEMA の値は無視されます。 固定サーバー ロール **sysadmin** のすべてのメンバーには、`dbo` の既定のスキーマが割り当てられます。

 Windows のログインまたはグループにマップされているユーザーの名前を変更できるのは、新しいユーザー名の SID とデータベースに記録されている SID が一致する場合だけです。 この条件により、データベースにおける Windows ログインのなりすましを防止できます。

 WITH LOGIN 句を使用すると、ユーザーを別のログインに再マップできます。 ログインのないユーザー、証明書にマップされているユーザー、非対称キーにマップされているユーザーを、この句を使って再マップすることはできません。 SQL ユーザーおよび Windows ユーザー (またはグループ) のみ再マップできます。 WITH LOGIN 句は、Windows アカウントを [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログインに変更するなど、ユーザーの種類の変更には使用できません。 唯一の例外は、Windows ユーザーを Azure AD ユーザーに変更する場合です。

> [!NOTE]
> 以下の規則は、Azure SQL Managed Instance 上の Windows ユーザーには適用されません。Azure SQL Managed Instance での Windows ログインの作成はサポートされていないためです。 WITH LOGIN オプションは、Azure AD ログインが存在する場合にのみ使用できます。

 次の条件を満たす場合、ユーザーの名前はログイン名に自動的に変更されます。

- ユーザーが Windows ユーザーである。

- 名前が Windows 名である (円記号を含む)。

- 新しい名前が指定されていない。

- 現在の名前がログイン名とは異なる。

 そうでない場合は、呼び出し元が NAME 句を追加で呼び出さない限り、ユーザーの名前は変更されません。

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のログイン、証明書、非対称キーにマップされているユーザーの名前には、円記号 (\\) を含めることはできません。

> [!CAUTION]
> [!INCLUDE[ssCautionUserSchema](../../includes/sscautionuserschema-md.md)]

### <a name="remarks-for-windows-users-in-sql-on-premises-migrated-to-azure-sql-managed-instance"></a>Azure SQL Managed Instance に移行された SQL オンプレミスの Windows ユーザーに関する解説

次の解説は、Azure AD とのフェデレーションおよび同期を行った Windows ユーザーとしての認証に適用されます。

> [!NOTE]
> 作成後の Azure SQL Managed Instance 機能の Azure AD 管理者が変更されました。 詳しくは、「[マネージド インスタンス用の新しい Azure AD 管理機能](/azure/sql-database/sql-database-aad-authentication-configure#new-azure-ad-admin-functionality-for-mi)」をご覧ください。

- Azure AD にマップされる Windows ユーザーまたはグループの検証は、既定では、移行の目的で使用されるすべてのバージョンの ALTER USER 構文で、Graph API によって実行されます。
- 別名が設定された (元の Windows アカウントとは異なる名前を使用する) オンプレミスのユーザーは、別名が設定された名前を保持します。
- Azure AD 認証では、LOGIN パラメーターは Azure SQL Managed Instance にのみ適用され、SQL Database では使用できません。
- Azure AD プリンシパルのログインを表示するには、次のコマンドを使います: `select * from sys.server_principals`。
- ログインの指定された種類が `E` または `X` であることを確認します。
- PASSWORD オプションは、Azure AD ユーザーには使用できません。
- 移行のあらゆるケースにおいて、Windows ユーザーまたはグループのロールとアクセス許可は、新しい Azure AD ユーザーまたはグループに自動的に転送されます。
- Windows ユーザーおよびグループを、SQL オンプレミスから Azure AD ユーザーおよびグループに変更するために、新しい構文拡張機能 **FROM EXTERNAL PROVIDER** を使用できます。 この拡張機能を使用する場合、Windows ドメインが Azure AD とフェデレーションされていて、かつすべての Windows ドメイン メンバーが Azure AD 内に存在している必要があります。 **FROM EXTERNAL PROVIDER** 構文は Azure SQL Managed Instance に適用されます。これは、Windows ユーザーが元の SQL インスタンスに対するログインを持っておらず、スタンドアロンの Azure AD データベース ユーザーにマップする必要がある場合に使用する必要があります。
- この場合、許可されるユーザー名は次のようになります。
- Widows ユーザー (_domain\user_)。
- Windows グループ (_MyWidnowsGroup_)。
- Windows の別名 (_MyWindowsAlias_)。
- ALTER コマンドの結果によって、古い userName が、古い userName の元の SID に基づいて Azure AD で検索された対応する名前に置き換えられます。 変更された名前は置き換えられ、データベースのメタデータに格納されます。
- (_domain\user_) は、Azure AD user@domain.com に置き換えられます。
- (_domain\\MyWidnowsGroup_) は、Azure AD グループに置き換えられます。
- (_MyWindowsAlias_) は変更されませんが、このユーザーの SID は Azure AD でチェックされます。

> [!NOTE]
> objectID に変換された元のユーザーの SID が Azure AD 内に見つからない場合、ALTER USER コマンドは失敗します。

- 変更されたユーザーを表示するには、次のコマンドを使います: `select * from sys.database_principals`
- ユーザーの指定された種類 `E` または `X` を確認します。
- Windows ユーザーを Azure AD ユーザーに移行するために NAME を使用する場合は、次の制限が適用されます。
- 有効な LOGIN を指定する必要があります。
- NAME は Azure AD でチェックされ、次のいずれかである必要があります。
- LOGIN の名前。
- 別名 - 名前は Azure AD に存在できません。
- その他のすべての場合、この構文は失敗します。

## <a name="security"></a>セキュリティ

> [!NOTE]
> **ALTER ANY USER** 権限を持つユーザーは、任意のユーザーの既定のスキーマを変更できます。 変更されたスキーマを所有するユーザーは、知らずに間違ったテーブルからデータを選択したり、間違ったスキーマからコードを実行する可能性があります。

### <a name="permissions"></a>アクセス許可

 ユーザーの名前を変更するには、**ALTER ANY USER** 権限が必要です。

 ターゲットを変更するには、ユーザーのログインにデータベースの **CONTROL** 権限が必要です。

 データベースに対する **CONTROL** 権限を持つユーザーのユーザー名を変更するには、データベースに対する **CONTROL** 権限が必要です。

 既定のスキーマまたは言語を変更するには、ユーザーに対する **ALTER** 権限が必要です。 ユーザーは自分が所有する既定のスキーマまたは言語を変更できます。

## <a name="examples"></a>例

すべての例は、ユーザー データベースで実行されます。

### <a name="a-changing-the-name-of-a-database-user"></a>A. データベース ユーザーの名前を変更する

 次の例では、データベース ユーザー `Mary5` の名前を `Mary51` に変更します。

```sql
ALTER USER Mary5 WITH NAME = Mary51;
GO
```

### <a name="b-changing-the-default-schema-of-a-user"></a>B. ユーザーの既定のスキーマを変更する

 次の例では、ユーザー `Mary51` の既定のスキーマを `Purchasing` に変更します。

```sql
ALTER USER Mary51 WITH DEFAULT_SCHEMA = Purchasing;
GO
```

### <a name="c-changing-several-options-at-once"></a>C. 複数のオプションを一度に変更する

 次の例では、包含データベース ユーザーに対する複数のオプションを 1 つのステートメントで変更します。

```sql
ALTER USER Philip
WITH NAME = Philipe
, DEFAULT_SCHEMA = Development
, PASSWORD = 'W1r77TT98%ab@#' OLD_PASSWORD = 'New Devel0per'
, DEFAULT_LANGUAGE= French ;
GO
```

### <a name="d-map-the-user-in-the-database-to-an-azure-ad-login-after-migration"></a>D. 移行後にデータベース内のユーザーを Azure AD ログインにマップする

次の例では、ユーザーが再マップされ、`westus/joe` が Azure AD ユーザー `joe@westus.com` にマップされます。 この例は、マネージド インスタンスに既に存在しているログインを対象としています。 これは、データベースを Azure SQL Managed Instance に移行した後、Azure AD ログインを使って認証する必要がある場合に実行する必要があります。

```sql
ALTER USER [westus/joe] WITH LOGIN = joe@westus.com
```

### <a name="e-map-an-old-windows-user-in-the-database-without-a-login-in-azure-sql-managed-instance-to-an-azure-ad-user"></a>E. Azure SQL Managed Instance でのログインを持たないデータベース内の古い Windows ユーザーを、Azure AD ユーザーにマップする

次の例では、ログインを持たないユーザー `westus/joe` が、Azure AD ユーザー `joe@westus.com` に再マップされます。 フェデレーション ユーザーは Azure AD 内に存在している必要があります。

```sql
ALTER USER [westus/joe] FROM EXTERNAL PROVIDER
```

### <a name="f-map-the-user-alias-to-an-existing-azure-ad-login"></a>F. ユーザーの別名を既存の Azure AD ログインにマップする

次の例では、ユーザー名 `westus\joe` が `joe_alias` に再マップされます。 この場合、対応する Azure AD ログインは `joe@westus.com` です。

```sql
ALTER USER [westus/joe] WITH LOGIN = joe@westus.com, name= joe_alias
```

### <a name="g-map-a-windows-group-that-was-migrated-in-azure-sql-managed-instance-to-an-azure-ad-group"></a>G. Azure SQL Managed Instance で移行された Windows グループを Azure AD グループにマップする

次の例では、古いオンプレミスのグループ `westus\mygroup` を、マネージド インスタンスの Azure AD グループ `mygroup` に再マップします。 このグループは Azure AD 内に存在している必要があります。

```sql
ALTER USER [westus\mygroup] WITH LOGIN = mygroup
```

## <a name="see-also"></a>関連項目

- [CREATE USER &#40;Transact-SQL&#41;](../../t-sql/statements/create-user-transact-sql.md)
- [DROP USER (Transact-SQL)](../../t-sql/statements/drop-user-transact-sql.md)
- [包含データベース](../../relational-databases/databases/contained-databases.md)
- [EVENTDATA &#40;Transact-SQL&#41;](../../t-sql/functions/eventdata-transact-sql.md)
- [sp_migrate_user_to_contained &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-migrate-user-to-contained-transact-sql.md)
- [チュートリアル:T-SQL DDL 構文を使用して、SQL Server オンプレミスの Windows ユーザーとグループを SQL Managed Instance に移行する](/azure/sql-database/tutorial-managed-instance-azure-active-directory-migration)

::: moniker-end
::: moniker range="=azure-sqldw-latest"

:::row:::
    :::column:::
        [SQL Server](alter-user-transact-sql.md?view=sql-server-ver15&preserve-view=true)
    :::column-end:::
    :::column:::
        [SQL Database](alter-user-transact-sql.md?view=azuresqldb-current&preserve-view=true)
    :::column-end:::
    :::column:::
        [SQL Managed Instance](alter-user-transact-sql.md?view=azuresqldb-mi-current&preserve-view=true)
    :::column-end:::
    :::column:::
        **_\* Azure Synapse<br />Analytics \*_**
    :::column-end:::
    :::column:::
        [Analytics Platform<br />System (PDW)](alter-user-transact-sql.md?view=aps-pdw-2016&preserve-view=true)
    :::column-end:::
:::row-end:::

&nbsp;

## <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

## <a name="syntax"></a>構文

```syntaxsql
-- Syntax for Azure Synapse

ALTER USER userName
 WITH <set_item> [ ,...n ]

<set_item> ::=
 NAME = newUserName
 | LOGIN = loginName
 | DEFAULT_SCHEMA = schema_name
[;]
```

## <a name="arguments"></a>引数

 *userName*: データベース内でユーザーを識別する名前を指定します。

 LOGIN **=** _loginName_: ユーザーのセキュリティ識別子 (SID) を別のログインの SID に一致するように変更することで、ユーザーを別のログインに再マッピングします。

 ALTER USER ステートメントが SQL のバッチ内の唯一のステートメントである場合、Azure SQL Database では WITH LOGIN 句がサポートされます。 ALTER USER ステートメントが SQL バッチ内の唯一のステートメントではない場合、または動的 SQL で実行されている場合、WITH LOGIN 句はサポートされません。

 NAME **=** _newUserName_: このユーザーの新しい名前を指定します。 *newUserName* は現在のデータベースに存在しない名前にする必要があります。

 DEFAULT_SCHEMA **=** { *schemaName* | NULL } このユーザー用のオブジェクトの名前を解決するときに、サーバーで最初に検索されるスキーマを指定します。 既定のスキーマを NULL に設定すると、既定のスキーマが Windows グループから削除されます。 Windows ユーザーでは、NULL オプションは使用できません。

## <a name="remarks"></a>解説

 既定のスキーマは、このデータベース ユーザー用のオブジェクトの名前を解決するときに、サーバーで最初に検索されるスキーマになります。 特に指定しない限り、このデータベース ユーザーによって作成されたオブジェクトの所有者になるのは、既定のスキーマです。

 ユーザーに既定のスキーマが設定されている場合は、その既定のスキーマが使用されます。 ユーザーに既定のスキーマが指定されておらず、そのユーザーが所属しているグループに既定のスキーマが指定されている場合は、グループの既定のスキーマが使用されます。 ユーザーに既定のスキーマが指定されておらず、そのユーザーが複数のグループに所属している場合、そのユーザーの既定のスキーマは、最も小さい principle_id と明示的に設定された既定のスキーマを持つ Windows グループのスキーマになります。 ユーザーに対する既定のスキーマを決定できない場合は、**dbo** スキーマが使用されます。

 DEFAULT_SCHEMA には、現在データベースに存在しないスキーマも設定できます。 したがって、スキーマが作成される前に DEFAULT_SCHEMA をユーザーに割り当てることができます。

 証明書または非対称キーにマップされているユーザーに対して、DEFAULT_SCHEMA を指定することはできません。

> [!IMPORTANT]
> ユーザーが固定サーバー ロール **sysadmin** のメンバーである場合、DEFAULT_SCHEMA の値は無視されます。 固定サーバー ロール **sysadmin** のすべてのメンバーには、`dbo` の既定のスキーマが割り当てられます。

 WITH LOGIN 句を使用すると、ユーザーを別のログインに再マップできます。 ログインのないユーザー、証明書にマップされているユーザー、非対称キーにマップされているユーザーを、この句を使って再マップすることはできません。 SQL ユーザーおよび Windows ユーザー (またはグループ) のみ再マップできます。 WITH LOGIN 句は、Windows アカウントを [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログインに変更するなど、ユーザーの種類の変更には使用できません。

 次の条件を満たす場合、ユーザーの名前はログイン名に自動的に変更されます。

- 新しい名前が指定されていない。

- 現在の名前がログイン名とは異なる。

 そうでない場合は、呼び出し元が NAME 句を追加で呼び出さない限り、ユーザーの名前は変更されません。

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のログイン、証明書、非対称キーにマップされているユーザーの名前には、円記号 (\\) を含めることはできません。

> [!CAUTION]
> [!INCLUDE[ssCautionUserSchema](../../includes/sscautionuserschema-md.md)]

## <a name="security"></a>セキュリティ

> [!NOTE]
> **ALTER ANY USER** 権限を持つユーザーは、任意のユーザーの既定のスキーマを変更できます。 変更されたスキーマを所有するユーザーは、知らずに間違ったテーブルからデータを選択したり、間違ったスキーマからコードを実行する可能性があります。

### <a name="permissions"></a>アクセス許可

 ユーザーの名前を変更するには、**ALTER ANY USER** 権限が必要です。

 ターゲットを変更するには、ユーザーのログインにデータベースの **CONTROL** 権限が必要です。

 データベースに対する **CONTROL** 権限を持つユーザーのユーザー名を変更するには、データベースに対する **CONTROL** 権限が必要です。

 既定のスキーマまたは言語を変更するには、ユーザーに対する **ALTER** 権限が必要です。 ユーザーは自分が所有する既定のスキーマまたは言語を変更できます。

## <a name="examples"></a>例

すべての例は、ユーザー データベースで実行されます。

### <a name="a-changing-the-name-of-a-database-user"></a>A. データベース ユーザーの名前を変更する

 次の例では、データベース ユーザー `Mary5` の名前を `Mary51` に変更します。

```sql
ALTER USER Mary5 WITH NAME = Mary51;
GO
```

### <a name="b-changing-the-default-schema-of-a-user"></a>B. ユーザーの既定のスキーマを変更する

次の例では、ユーザー `Mary51` の既定のスキーマを `Purchasing` に変更します。

```sql
ALTER USER Mary51 WITH DEFAULT_SCHEMA = Purchasing;
GO
```

## <a name="see-also"></a>関連項目

- [CREATE USER &#40;Transact-SQL&#41;](../../t-sql/statements/create-user-transact-sql.md)
- [DROP USER (Transact-SQL)](../../t-sql/statements/drop-user-transact-sql.md)
- [包含データベース](../../relational-databases/databases/contained-databases.md)
- [EVENTDATA &#40;Transact-SQL&#41;](../../t-sql/functions/eventdata-transact-sql.md)
- [sp_migrate_user_to_contained &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-migrate-user-to-contained-transact-sql.md)

::: moniker-end
::: moniker range=">=aps-pdw-2016"

:::row:::
    :::column:::
        [SQL Server](alter-user-transact-sql.md?view=sql-server-ver15&preserve-view=true)
    :::column-end:::
    :::column:::
        [SQL Database](alter-user-transact-sql.md?view=azuresqldb-current&preserve-view=true)
    :::column-end:::
    :::column:::
        [SQL Managed Instance](alter-user-transact-sql.md?view=azuresqldb-mi-current&preserve-view=true)
    :::column-end:::
    :::column:::
        [Azure Synapse<br />Analytics](alter-user-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)
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

ALTER USER userName
 WITH <set_item> [ ,...n ]

<set_item> ::=
 NAME = newUserName
 | LOGIN = loginName
 | DEFAULT_SCHEMA = schema_name
[;]
```

## <a name="arguments"></a>引数

 *userName*: データベース内でユーザーを識別する名前を指定します。

 LOGIN **=** _loginName_: ユーザーのセキュリティ識別子 (SID) を別のログインの SID に一致するように変更することで、ユーザーを別のログインに再マッピングします。

 ALTER USER ステートメントが SQL のバッチ内の唯一のステートメントである場合、Azure SQL Database では WITH LOGIN 句がサポートされます。 ALTER USER ステートメントが SQL バッチ内の唯一のステートメントではない場合、または動的 SQL で実行されている場合、WITH LOGIN 句はサポートされません。

 NAME **=** _newUserName_: このユーザーの新しい名前を指定します。 *newUserName* は現在のデータベースに存在しない名前にする必要があります。

 DEFAULT_SCHEMA **=** { *schemaName* | NULL } このユーザー用のオブジェクトの名前を解決するときに、サーバーで最初に検索されるスキーマを指定します。 既定のスキーマを NULL に設定すると、既定のスキーマが Windows グループから削除されます。 Windows ユーザーでは、NULL オプションは使用できません。

## <a name="remarks"></a>解説

 既定のスキーマは、このデータベース ユーザー用のオブジェクトの名前を解決するときに、サーバーで最初に検索されるスキーマになります。 特に指定しない限り、このデータベース ユーザーによって作成されたオブジェクトの所有者になるのは、既定のスキーマです。

 ユーザーに既定のスキーマが設定されている場合は、その既定のスキーマが使用されます。 ユーザーに既定のスキーマが指定されておらず、そのユーザーが所属しているグループに既定のスキーマが指定されている場合は、グループの既定のスキーマが使用されます。 ユーザーに既定のスキーマが指定されておらず、そのユーザーが複数のグループに所属している場合、そのユーザーの既定のスキーマは、最も小さい principle_id と明示的に設定された既定のスキーマを持つ Windows グループのスキーマになります。 ユーザーに対する既定のスキーマを決定できない場合は、**dbo** スキーマが使用されます。

 DEFAULT_SCHEMA には、現在データベースに存在しないスキーマも設定できます。 したがって、スキーマが作成される前に DEFAULT_SCHEMA をユーザーに割り当てることができます。

 証明書または非対称キーにマップされているユーザーに対して、DEFAULT_SCHEMA を指定することはできません。

> [!IMPORTANT]
> ユーザーが固定サーバー ロール **sysadmin** のメンバーである場合、DEFAULT_SCHEMA の値は無視されます。 固定サーバー ロール **sysadmin** のすべてのメンバーには、`dbo` の既定のスキーマが割り当てられます。

 WITH LOGIN 句を使用すると、ユーザーを別のログインに再マップできます。 ログインのないユーザー、証明書にマップされているユーザー、非対称キーにマップされているユーザーを、この句を使って再マップすることはできません。 SQL ユーザーおよび Windows ユーザー (またはグループ) のみ再マップできます。 WITH LOGIN 句は、Windows アカウントを [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログインに変更するなど、ユーザーの種類の変更には使用できません。

 次の条件を満たす場合、ユーザーの名前はログイン名に自動的に変更されます。

- 新しい名前が指定されていない。

- 現在の名前がログイン名とは異なる。

 そうでない場合は、呼び出し元が NAME 句を追加で呼び出さない限り、ユーザーの名前は変更されません。

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のログイン、証明書、非対称キーにマップされているユーザーの名前には、円記号 (\\) を含めることはできません。

> [!CAUTION]
> [!INCLUDE[ssCautionUserSchema](../../includes/sscautionuserschema-md.md)]

## <a name="security"></a>セキュリティ

> [!NOTE]
> **ALTER ANY USER** 権限を持つユーザーは、任意のユーザーの既定のスキーマを変更できます。 変更されたスキーマを所有するユーザーは、知らずに間違ったテーブルからデータを選択したり、間違ったスキーマからコードを実行する可能性があります。

### <a name="permissions"></a>アクセス許可

 ユーザーの名前を変更するには、**ALTER ANY USER** 権限が必要です。

 ターゲットを変更するには、ユーザーのログインにデータベースの **CONTROL** 権限が必要です。

 データベースに対する **CONTROL** 権限を持つユーザーのユーザー名を変更するには、データベースに対する **CONTROL** 権限が必要です。

 既定のスキーマまたは言語を変更するには、ユーザーに対する **ALTER** 権限が必要です。 ユーザーは自分が所有する既定のスキーマまたは言語を変更できます。

## <a name="examples"></a>例

すべての例は、ユーザー データベースで実行されます。

### <a name="a-changing-the-name-of-a-database-user"></a>A. データベース ユーザーの名前を変更する

 次の例では、データベース ユーザー `Mary5` の名前を `Mary51` に変更します。

```sql
ALTER USER Mary5 WITH NAME = Mary51;
GO
```

### <a name="b-changing-the-default-schema-of-a-user"></a>B. ユーザーの既定のスキーマを変更する
 次の例では、ユーザー `Mary51` の既定のスキーマを `Purchasing` に変更します。

```sql
ALTER USER Mary51 WITH DEFAULT_SCHEMA = Purchasing;
GO
```

## <a name="see-also"></a>関連項目

- [CREATE USER &#40;Transact-SQL&#41;](../../t-sql/statements/create-user-transact-sql.md)
- [DROP USER (Transact-SQL)](../../t-sql/statements/drop-user-transact-sql.md)
- [包含データベース](../../relational-databases/databases/contained-databases.md)
- [EVENTDATA &#40;Transact-SQL&#41;](../../t-sql/functions/eventdata-transact-sql.md)
- [sp_migrate_user_to_contained &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-migrate-user-to-contained-transact-sql.md)

::: moniker-end
