---
title: CREATE DATABASE (Transact-SQL) | Microsoft Docs
description: SQL Server、Azure SQL Database、Azure Synapse Analytics、Analytics Platform System のデータベース構文を作成します。
ms.custom: references_regions
ms.date: 12/11/2020
ms.prod: sql
ms.prod_service: sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- DATABASE_TSQL
- DATABASE
- CONTAINMENT_TSQL
- CREATE DATABASE
- CREATE_DATABASE_TSQL
- CONTAINS_FILESTREAM_TSQL
- CONTAINS FILESTREAM
- CONTAINMENT
dev_langs:
- TSQL
helpviewer_keywords:
- snapshots [SQL Server database snapshots], creating
- databases [SQL Server], creating
- model database [SQL Server], database creation
- mounted drives [SQL Server]
- CREATE DATABASE
- CREATE DATABASE statement
- file creation [SQL Server]
- creating databases
- containment
- filegroups [SQL Server], database creation
- database creation [SQL Server], CREATE DATABASE statement
- moving databases
- attaching databases [SQL Server], CREATE DATABASE...FOR ATTACH
ms.assetid: 29ddac46-7a0f-4151-bd94-75c1908c89f8
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-current||=azuresqldb-mi-current||=azure-sqldw-latest||>=aps-pdw-2016'
ms.openlocfilehash: d89869b8584e9df8959daf3c1745e19295a2a2cd
ms.sourcegitcommit: 295b9dfc758471ef7d238a2b0f92f93e34acbb1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106054826"
---
# <a name="create-database"></a>CREATE DATABASE

新しいデータベースを作成します。

お使いの特定バージョンの SQL の構文、引数、注釈、権限、例を表示するには、以下のいずれかのタブをクリックします。

構文表記規則の詳細については、「[Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)」を参照してください。

[!INCLUDE[select-product](../../includes/select-product.md)]

::: moniker range=">=sql-server-2016||>=sql-server-linux-2017"

:::row:::
    :::column:::
        **_\* SQL Server \*_** &nbsp;
    :::column-end:::
    :::column:::
        [SQL Database](create-database-transact-sql.md?view=azuresqldb-current&preserve-view=true)
    :::column-end:::
    :::column:::
        [SQL Managed Instance](create-database-transact-sql.md?view=azuresqldb-mi-current&preserve-view=true)
    :::column-end:::
    :::column:::
        [Azure Synapse<br />Analytics](create-database-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)
    :::column-end:::
    :::column:::
        [Analytics Platform<br />System (PDW)](create-database-transact-sql.md?view=aps-pdw-2016&preserve-view=true)
    :::column-end:::
:::row-end:::

&nbsp;

## <a name="sql-server"></a>SQL Server

## <a name="overview"></a>概要

SQL Server では、このステートメントは、新しいデータベース、使用されるファイル、そのファイル グループを作成します。 また、データベース スナップショットを作成したり、別のデータベースのデタッチされたファイルからデータベースを作成するためにデータベース ファイルをアタッチするためにも使用できます。

## <a name="syntax"></a>構文

データベースを作成する

```syntaxsql
CREATE DATABASE database_name
[ CONTAINMENT = { NONE | PARTIAL } ]
[ ON
      [ PRIMARY ] <filespec> [ ,...n ]
      [ , <filegroup> [ ,...n ] ]
      [ LOG ON <filespec> [ ,...n ] ]
]
[ COLLATE collation_name ]
[ WITH <option> [,...n ] ]
[;]

<option> ::=
{
      FILESTREAM ( <filestream_option> [,...n ] )
    | DEFAULT_FULLTEXT_LANGUAGE = { lcid | language_name | language_alias }
    | DEFAULT_LANGUAGE = { lcid | language_name | language_alias }
    | NESTED_TRIGGERS = { OFF | ON }
    | TRANSFORM_NOISE_WORDS = { OFF | ON}
    | TWO_DIGIT_YEAR_CUTOFF = <two_digit_year_cutoff>
    | DB_CHAINING { OFF | ON }
    | TRUSTWORTHY { OFF | ON }
    | PERSISTENT_LOG_BUFFER=ON ( DIRECTORY_NAME='<Filepath to folder on DAX formatted volume>' )
}

<filestream_option> ::=
{
      NON_TRANSACTED_ACCESS = { OFF | READ_ONLY | FULL }
    | DIRECTORY_NAME = 'directory_name'
}

<filespec> ::=
{
(
    NAME = logical_file_name ,
    FILENAME = { 'os_file_name' | 'filestream_path' }
    [ , SIZE = size [ KB | MB | GB | TB ] ]
    [ , MAXSIZE = { max_size [ KB | MB | GB | TB ] | UNLIMITED } ]
    [ , FILEGROWTH = growth_increment [ KB | MB | GB | TB | % ] ]
)
}

<filegroup> ::=
{
FILEGROUP filegroup name [ [ CONTAINS FILESTREAM ] [ DEFAULT ] | CONTAINS MEMORY_OPTIMIZED_DATA ]
    <filespec> [ ,...n ]
}
```

データベースのアタッチ

```syntaxsql
CREATE DATABASE database_name
    ON <filespec> [ ,...n ]
    FOR { { ATTACH [ WITH <attach_database_option> [ , ...n ] ] }
        | ATTACH_REBUILD_LOG }
[;]

<attach_database_option> ::=
{
      <service_broker_option>
    | RESTRICTED_USER
    | FILESTREAM ( DIRECTORY_NAME = { 'directory_name' | NULL } )
}

<service_broker_option> ::=
{
    ENABLE_BROKER
  | NEW_BROKER
  | ERROR_BROKER_CONVERSATIONS
}
```

データベース スナップショットの作成

```syntaxsql
CREATE DATABASE database_snapshot_name
    ON
    (
        NAME = logical_file_name,
        FILENAME = 'os_file_name'
    ) [ ,...n ]
    AS SNAPSHOT OF
[;]
```

## <a name="arguments"></a>引数

*database_name*: 新しいデータベースの名前です。 データベース名は、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンス内で一意であり、[識別子](../../relational-databases/databases/database-identifiers.md)のルールに従っている必要があります。

ログ ファイルに論理名が指定されていない場合を除き、*database_name* には、最大 128 文字まで指定できます。 ログ ファイルの論理名が指定されていない場合、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] は、*database_name* にサフィックスを付加することにより、ログの *logical_file_name* および *os_file_name* を生成します。 生成された論理ファイル名が 128 文字を超えないようにするため、*database_name* は 123 文字に制限されます。

データ ファイル名が指定されていない場合、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、*logical_file_name* および *os_file_name* の両方に *database_name* を使用します。 既定のパスはレジストリから取得されます。 既定のパスを変更するには、[!INCLUDE[ssManStudio](../../includes/ssmanstudio-md.md)] の **[サーバーのプロパティ] ([データベースの設定] ページ)** を使用します。 既定のパスを変更するには、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] を再起動する必要があります。

CONTAINMENT = { NONE | PARTIAL }

**適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降

データベースの包含状態を指定します。 NONE = 非包含データベース。 PARTIAL = 部分的包含データベース。

ON: データベースのデータ部分の格納に使用するディスク ファイル (データ ファイル) を明示的に定義するように指定します。 プライマリ ファイル グループのデータ ファイルを定義する \<filespec> 項目のコンマ区切りリストが続く場合は、ON にします。 プライマリ ファイル グループ内のファイルのリストに続き、ユーザー ファイル グループおよびそれらのファイルを定義する省略可能な \<filegroup> 項目のコンマ区切りリストを記述できます。

PRIMARY: 関連付けられた \<filespec> リストによってプライマリ ファイルを定義するように指定します。 プライマリ ファイル グループ内の \<filespec> エントリに最初に指定されたファイルが、プライマリ ファイルとなります。 データベースはプライマリ ファイルを 1 つだけ保有することができます。 詳細については、「 [Database Files and Filegroups](../../relational-databases/databases/database-files-and-filegroups.md)」を参照してください。

PRIMARY を指定しないと、CREATE DATABASE ステートメント内に記述された最初のファイルがプライマリ ファイルになります。

LOG ON: データベース ログの格納に使用するディスク ファイル (ログ ファイル) を明示的に定義するように指定します。 LOG ON に続けて、ログ ファイルを定義する \<filespec> 項目のコンマ区切りリストを記述します。 LOG ON が指定されていない場合、データベースのすべてのデータ ファイルのサイズの合計の 25%、または、512 KB のいずれか大きい方のサイズのログ ファイルが 1 つ自動的に作成されます。 このファイルは既定のログ ファイルの場所に保存されます。 この場所については、[データ ファイルとログ ファイルの既定の場所の表示または変更 - SSMS](../../database-engine/configure-windows/view-or-change-the-default-locations-for-data-and-log-files.md) に関するページを参照してください。

LOG ON はデータベース スナップショットでは指定できません。

COLLATE *collation_name*: データベースの既定の照合順序を指定します。 照合順序名には、Windows 照合順序名または SQL 照合順序名を指定できます。 指定しない場合は、データベースに [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンスの既定の照合順序が割り当てられます。 照合順序名は、データベース スナップショットでは指定できません。

照合順序名は、FOR ATTACH 句や FOR ATTACH_REBUILD_LOG 句と共に指定することはできません。 アタッチされたデータベースの照合順序を変更する方法の詳細については、この [Microsoft Web サイト](https://go.microsoft.com/fwlink/?linkid=16419&kbid=325335)を参照してください。

Windows 照合順序名および SQL 照合順序名について詳しくは、「[COLLATE](~/t-sql/statements/collations.md)」をご覧ください。

> [!NOTE]
> 包含データベースは、非包含データベースとは異なる方法で照合されます。 詳細については、「[包含データベースの照合順序](../../relational-databases/databases/contained-database-collations.md)」を参照してください。

WITH \<option>
 **\<filestream_option>**

NON_TRANSACTED_ACCESS = { **OFF** | READ_ONLY | FULL } **適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降。

データベースに対する非トランザクション FILESTREAM アクセスのレベルを指定します。

|値|説明|
|-----------|-----------------|
|OFF|非トランザクション アクセスは無効です。|
|READONLY|このデータベース内の FILESTREAM データは、非トランザクション プロセスによって読み取ることができます。|
|FULL|FILESTREAM FileTable に対する完全な非トランザクション アクセスは有効です。|

DIRECTORY_NAME = \<directory_name>
**適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降

Windows と互換性のあるディレクトリ名です。 この名前は、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンス内のすべての Database_Directory 名の中で一意である必要があります。 一意性の比較では、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 照合順序の設定とは関係なく、大文字と小文字は区別されません。 このオプションは、このデータベース内に FileTable を作成する前に設定する必要があります。

次のオプションは、CONTAINMENT が PARTIAL に設定されている場合にのみ使用できます。 CONTAINMENT が NONE に設定されている場合、エラーが発生します。

- **DEFAULT_FULLTEXT_LANGUAGE = \<lcid> | \<language name> | \<language alias>**

  **適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降

  このオプションの詳細については、「[default full-text language サーバー構成オプションの構成](../../database-engine/configure-windows/configure-the-default-full-text-language-server-configuration-option.md)」を参照してください。

- **DEFAULT_LANGUAGE = \<lcid> | \<language name> | \<language alias>**

  **適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降

  このオプションの詳細については、「[default language サーバー構成オプションの構成](../../database-engine/configure-windows/configure-the-default-language-server-configuration-option.md)」を参照してください。

- **NESTED_TRIGGERS = { OFF | ON}**

  **適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降

  このオプションの詳細については、「[nested triggers サーバー構成オプションの構成](../../database-engine/configure-windows/configure-the-nested-triggers-server-configuration-option.md)」を参照してください。

- **TRANSFORM_NOISE_WORDS = { OFF | ON}**

  **適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降

  このオプションの詳細については、「[transform noise words サーバー構成オプション](../../database-engine/configure-windows/transform-noise-words-server-configuration-option.md)」を参照してください。

- **TWO_DIGIT_YEAR_CUTOFF = { 2049 | \<any year between 1753 and 9999> }**

  年を表す 4 桁の数字。 既定値は 2049 です。 このオプションの詳細については、「[two digit year cutoff サーバー構成オプションの構成](../../database-engine/configure-windows/configure-the-two-digit-year-cutoff-server-configuration-option.md)」を参照してください。

- **DB_CHAINING { OFF | ON }**

  ON が指定されている場合、データベースを、複数データベースの組み合わせ所有権のソース データベースまたは対象データベースとして使用できます。

  OFF の場合、データベースは、複数データベースの組み合わせ所有権に参加することはできません。 既定値は OFF です。

  > [!IMPORTANT]
  > [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスでは、cross db ownership chaining サーバー オプションが 0 (OFF) の場合に、この設定が認識されます。 cross db ownership chaining が 1 (ON) の場合は、このオプションの値にかかわらず、すべてのユーザー データベースが複数データベースの組み合わせ所有権に参加できます。 このオプションは、[sp_configure](../../relational-databases/system-stored-procedures/sp-configure-transact-sql.md) を使用して設定します。

  このオプションを設定するには、sysadmin 固定サーバー ロールのメンバーシップが必要です。 master、model、tempdb のシステム データベースでは、DB_CHAINING オプションを設定することはできません。

- **TRUSTWORTHY { OFF | ON }**

  ON が指定されている場合、権限借用のコンテキストを使用するデータベース モジュール (ビュー、ユーザー定義関数、ストアド プロシージャなど) は、データベース外のリソースにアクセスできます。

  OFF の場合、権限借用のコンテキスト内のデータベース モジュールは、データベース外のリソースにアクセスできません。 既定値は OFF です。

  データベースがアタッチされている場合は常に、TRUSTWORTHY は OFF に設定されます。

  既定では、msdb データベースを除くすべてのシステム データベースで TRUSTWORTHY は OFF に設定されています。 model および tempdb データベースでは、この値は変更できません。 master データベースでは、TRUSTWORTHY オプションを ON に設定しないことを強くお勧めします。

- **PERSISTENT_LOG_BUFFER=ON ( DIRECTORY_NAME='' )**

  このオプションを指定すると、ストレージ クラス メモリ (NVDIMM-N 不揮発性ストレージ、永続的ログ バッファーとも呼ばれます) によってバックアップされるディスク デバイス上のボリュームに、トランザクション ログ バッファーが作成されます。 詳しくは、「[Transaction Commit latency acceleration using Storage Class Memory](/archive/blogs/sqlserverstorageengine/transaction-commit-latency-acceleration-using-storage-class-memory-in-windows-server-2016sql-server-2016-sp1)」(ストレージ クラス メモリを使用したトランザクション コミット待機時間の短縮) をご覧ください。 **適用対象**: [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 以降。

FOR ATTACH [ WITH \< attach_database_option > ]: 既存のオペレーティング システム ファイルのセットを[アタッチ](../../relational-databases/databases/database-detach-and-attach-sql-server.md)することによりデータベースを作成するように指定します。 プライマリ ファイルを指定する \<filespec> エントリが必要です。 他に必要な \<filespec> エントリは、データベースが最初に作成されたとき、または最後にアタッチされたときからパスが変わったファイルのエントリだけです。 これらのファイルの \<filespec> エントリを指定する必要があります。

FOR ATTACH では、以下のことが必要です。

- すべてのデータ ファイル (MDF および NDF) が有効であること。
- ログ ファイルが複数存在する場合は、これらがすべて使用可能であること。

読み取り/書き込みデータベースに現在利用できないログ ファイルが 1 つある場合、アタッチ操作の前に、ユーザーも、開かれたトランザクションもない状態でデータベースをシャットダウンすると、FOR ATTACH は自動的にログ ファイルを再構築し、プライマリ ファイルを更新します。 これに対し、読み取り専用データベースの場合、プライマリ ファイルを更新できないため、ログは再構築できません。 したがって、ログが利用できない読み取り専用データベースをアタッチする場合、ログ ファイルまたはファイルを FOR ATTACH 句に指定する必要があります。

> [!NOTE]
> 新しいバージョンの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] で作成したデータベースは、それ以前のバージョンでアタッチすることはできません。

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、アタッチされるデータベースに含まれているフルテキスト ファイルも、すべてデータベースと共にアタッチされます。 フルテキスト カタログの新しいパスを指定するには、フルテキストのオペレーティング システム ファイル名を含めずに新しい場所を指定します。 詳細については、「例」のセクションを参照してください。

"ディレクトリ名" の FILESTREAM オプションを含むデータベースを [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンスにアタッチすると、Database_Directory 名が一意であることを確認する要求が [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] に対して行われます。 そうではない場合、アタッチ操作は失敗し、"FILESTREAM Database_Directory 名 \<name> は、この SQL Server インスタンスで一意ではありません" というエラーが出力されます。 このエラーを回避するには、省略可能なパラメーターの *directory_name* をこの操作に渡す必要があります。

FOR ATTACH はデータベース スナップショットでは指定できません。

FOR ATTACH は RESTRICTED_USER オプションを指定できます。 RESTRICTED_USER モードでは、db_owner 固定データベース ロールと、dbcreator 固定サーバー ロールおよび sysadmin 固定サーバー ロールのメンバーのみが、データベースに接続できます。ただし、接続ユーザー数に制限はありません。 修飾されていないユーザーによる試行が拒否されます。

データベースで [!INCLUDE[ssSB](../../includes/sssb-md.md)] が使用される場合は、FOR ATTACH 句で WITH \<service_broker_option> を使用します。

\<service_broker_option>: [!INCLUDE[ssSB](../../includes/sssb-md.md)] のメッセージの配信と、データベースの [!INCLUDE[ssSB](../../includes/sssb-md.md)] 識別子を制御します。 [!INCLUDE[ssSB](../../includes/sssb-md.md)] オプションは、FOR ATTACH 句が使用されている場合にのみ指定できます。

ENABLE_BROKER: 指定したデータベースに対して [!INCLUDE[ssSB](../../includes/sssb-md.md)] を有効にします。 つまり、メッセージ配信が開始され、sys.databases カタログ ビューで is_broker_enabled が TRUE に設定されます。 データベースは、既存の [!INCLUDE[ssSB](../../includes/sssb-md.md)] 識別子を保持します。

NEW_BROKER: sys.databases と復元されたデータベースの両方に新しい service_broker_guid 値を作成し、すべてのメッセージ交換エンドポイントをクリーンアップして終了します。 ブローカーは有効ですが、リモートのメッセージ交換エンドポイントにメッセージは送信されません。 古い [!INCLUDE[ssSB](../../includes/sssb-md.md)] 識別子を参照するルートは、新しい識別子を使用して作成し直す必要があります。

ERROR_BROKER_CONVERSATIONS: データベースがアタッチまたは復元されていることを示すエラーと共に、すべてのメッセージ交換を終了します。 ブローカーはこの操作が完了するまで無効になり、その後、有効になります。 データベースは、既存の [!INCLUDE[ssSB](../../includes/sssb-md.md)] 識別子を保持します。

レプリケートされたデータベースをアタッチするとき、そのデータベースがデタッチではなくコピーされたものである場合は、次の点を考慮してください。

- 元のデータベースと同じサーバー インスタンスおよびバージョンにデータベースをアタッチする場合は、必要な追加手順はありません。
- 同じサーバー インスタンスのアップグレードされたバージョンにデータベースをアタッチする場合は、アタッチ操作が完了した後、[sp_vupgrade_replication](../../relational-databases/system-stored-procedures/sp-vupgrade-replication-transact-sql.md) を実行してレプリケーションをアップグレードする必要があります。
- バージョンに関係なく、別のサーバー インスタンスにデータベースをアタッチする場合は、アタッチ操作が完了した後、[sp_removedbreplication](../../relational-databases/system-stored-procedures/sp-removedbreplication-transact-sql.md) を実行してレプリケーションを削除する必要があります。

> [!NOTE]
> アタッチは **vardecimal** ストレージ形式で動作しますが、[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] を [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] SP2 以降にアップグレードする必要があります。 vardecimal ストレージ形式を使用するデータベースを以前のバージョンの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] にアタッチすることはできません。 **vardecimal** ストレージ形式の詳細については、「[データ圧縮](../../relational-databases/data-compression/data-compression.md)」を参照してください。

データベースが最初に [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]の新しいインスタンスにアタッチまたは復元されるとき、データベース マスター キー (サービス マスター キーにより暗号化されたもの) のコピーはまだサーバーに格納されていません。 **OPEN MASTER KEY** を使用して、データベース マスター キー (DMK) を暗号化解除する必要があります。 DMK の暗号化が解除されると、 **ALTER MASTER KEY REGENERATE** ステートメントを使用して、サービス マスター キー (SMK) で暗号化された DMK のコピーをサーバーに提供することにより、将来、自動的に暗号化解除することも可能となります。 データベースを以前のバージョンからアップグレードした場合、新しい AES アルゴリズムを使用するように DMK を再作成する必要があります。 DMK を再作成する方法の詳細については、[ALTER MASTER KEY](../../t-sql/statements/alter-master-key-transact-sql.md) に関するページを参照してください。 DMK キーを再作成して AES にアップグレードするのに必要な時間は、DMK によって保護されているオブジェクトの数によって異なります。 DMK キーを再作成して AES にアップグレードする作業は、1 回限りで済み、今後のキー ローテーション方法には影響を与えません。 アタッチを使用してデータベースをアップグレードする方法については、[デタッチとアタッチを使用したデータベースのアップグレード](../../relational-databases/databases/upgrade-a-database-using-detach-and-attach-transact-sql.md)に関するページを参照してください。

> [!IMPORTANT]
> 不明なソースや信頼されていないソースからのデータベースはアタッチしないことをお勧めします。 こうしたデータベースには、意図しない [!INCLUDE[tsql](../../includes/tsql-md.md)] コードを実行したり、スキーマまたは物理データベース構造を変更してエラーを発生させるような、悪意のあるコードが含まれている可能性があります。 不明または信頼できないソースのデータベースを使用する前には、運用サーバー以外のサーバーでそのデータベースに対し [DBCC CHECKDB](../../t-sql/database-console-commands/dbcc-checkdb-transact-sql.md) を実行し、さらに、そのデータベースのストアド プロシージャやその他のユーザー定義コードなどのコードを調べます。
> [!NOTE]
> データベースをアタッチするときに、**TRUSTWORTHY** オプションおよび **DB_CHAINING** オプションによる効果は発生しません。

FOR ATTACH_REBUILD_LOG: 既存のオペレーティング システム ファイルのセットをアタッチすることによりデータベースを作成するように指定します。 このオプションは読み取り/書き込みデータベースに限定されます。 プライマリ ファイルを指定する *\<filespec>* エントリが必要です。 1 つ以上のトランザクション ログ ファイルが見つからない場合、ログ ファイルは再構築されます。 ATTACH_REBUILD_LOG を指定すると、1 MB のログ ファイルが自動的に新規作成されます。 このファイルは既定のログ ファイルの場所に保存されます。 この場所については、[データ ファイルとログ ファイルの既定の場所の表示または変更 - SSMS](../../database-engine/configure-windows/view-or-change-the-default-locations-for-data-and-log-files.md) に関するページを参照してください。

> [!NOTE]
> ログ ファイルが利用可能な場合は、[!INCLUDE[ssDE](../../includes/ssde-md.md)]はログ ファイルを再構築せず、それらのファイルを使用します。

FOR ATTACH_REBUILD_LOG では、以下のことが必要です。

- データベースのクリーン シャットダウン。
- すべてのデータ ファイル (MDF および NDF) が有効であること。

> [!IMPORTANT]
> この操作により、連続したログ バックアップが中断されます。 操作が完了したら、データベース全体のバックアップを行うことをお勧めします。 詳細については、[バックアップ](../../t-sql/statements/backup-transact-sql.md)に関するページを参照してください。

通常、FOR ATTACH_REBUILD_LOG は、大きなログを持つ読み取り/書き込みデータベースを別のサーバーにコピーする場合に使用します。このようなサーバーでは、コピーしたデータベースが、多くの場合読み取り操作に使用されます (または読み取り操作でのみ使用されます)。このため、元のデータベースほどログ領域を必要としません。

FOR ATTACH_REBUILD_LOG はデータベース スナップショットでは指定できません。

データベースのアタッチおよびデタッチの詳細については、[データベースのデタッチとアタッチ](../../relational-databases/databases/database-detach-and-attach-sql-server.md)に関するページを参照してください。

\<filespec>: ファイル プロパティを制御します。

NAME *logical_file_name*: ファイルの論理名を指定します。 NAME は、FOR ATTACH 句の 1 つを指定する場合以外に、FILENAME が指定されるときに必要です。 FILESTREAM ファイル グループの名前を PRIMARY にすることはできません。

*logical_file_name*: ファイルを参照するときに [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] で使用される論理名を指定します。 *Logical_file_name* は、データベース内で一意であり、[識別子](../../relational-databases/databases/database-identifiers.md)の規則に従っている必要があります。 この名前は、文字定数、UNICODE 定数、標準の識別子、区切られた識別子のいずれでもかまいません。

FILENAME { **'** _os\_file\_name_ **'** | **'** _filestream\_path_ **'** }: オペレーティング システムの (物理) ファイル名です。

**'** *os_file_name* **'** : ファイルを作成する際にオペレーティング システムが使用するパスとファイル名です。 ファイルは、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] がインストールされているローカル サーバー、ストレージ エリア ネットワーク (SAN)、または、iSCSI ベースのネットワークのうちのいずれかのデバイスに存在する必要があります。 指定したパスは、CREATE DATABASE ステートメントを実行する前に存在する必要があります。 詳細については、「解説」セクションの「データベース ファイルとファイル グループ」を参照してください。

ファイルに対して UNC パスが指定されている場合は、SIZE、MAXSIZE、および FILEGROWTH パラメーターを設定できます。

ファイルが未処理のパーティション上にある場合、*os_file_name* には、未処理になっている既存のパーティションのドライブ文字のみを指定する必要があります。 1 つの未処理のパーティションに作成できるのは 1 つのデータ ファイルだけです。

ファイルが読み取り専用のセカンダリ ファイルであるか、データベースが読み取り専用である場合を除き、データ ファイルを圧縮ファイル システム上に置かないでください。 ログ ファイルは、圧縮ファイル システム上に置くことはできません。

**'** *filestream_path* **'** : FILESTREAM ファイル グループの場合、FILENAME は FILESTREAM データが格納されるパスを参照します。 最後のフォルダーまでのパスが存在する必要がありますが、最後のフォルダーは存在できません。 たとえば、パス C:\MyFiles\MyFilestreamData を指定する場合は、ALTER DATABASE を実行するとき、C:\MyFiles は既に存在している必要がありますが、MyFilestreamData フォルダーは存在してはなりません。

ファイル グループとファイル (`<filespec>`) は、同じステートメントで作成する必要があります。

SIZE プロパティおよび FILEGROWTH プロパティは、FILESTREAM ファイル グループには適用されません。

SIZE *size*: ファイルのサイズを指定します。

*os_file_name* が UNC パスとして指定されている場合、SIZE を指定することはできません。 SIZE は、FILESTREAM ファイル グループには適用されません。

*size*: ファイルの初期サイズです。

プライマリ ファイルに *size* が指定されていない場合、[!INCLUDE[ssDE](../../includes/ssde-md.md)] では、model データベースのプライマリ ファイルのサイズを使用します。 モデルの既定のサイズは 8 MB ([!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 以降) または 1 MB (それより前のバージョン) です。 セカンダリ データ ファイルまたはログ ファイルが指定されているにもかかわらず、そのファイルに対して *size* サイズが指定されていない場合、[!INCLUDE[ssDE](../../includes/ssde-md.md)] では、そのファイルのサイズが 8 MB ([!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 以降) または 1 MB (それより前のバージョン) になります。 なお、プライマリ ファイルに対して指定するサイズは、model データベースのプライマリ ファイルのサイズ以上である必要があります。

サフィックスとして、キロバイト (KB)、メガバイト (MB)、ギガバイト (GB)、またはテラバイト (TB) を使用できます。 既定値は MB です。 整数を指定します。小数を含めないでください。 *size* は整数値です。 2,147,483,647 を超える値に対しては、より大きな単位を使用してください。

MAXSIZE *max_size*: ファイルのサイズの上限を指定します。 *os_file_name* が UNC パスとして指定されている場合、MAXSIZE を指定することはできません。

*max_size*: ファイルの最大サイズです。 サフィックスとして、KB、MB、GB、および TB を使用できます。 既定値は MB です。 整数を指定します。小数を含めないでください。 *max_size* を指定しないと、ファイルはディスク領域がなくなるまで拡張されます。 *max_size* は整数値です。 2,147,483,647 を超える値に対しては、より大きな単位を使用してください。

UNLIMITED: ディスクがいっぱいになるまでファイルを拡張するように指定します。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、無制限に拡張するファイル固有のログの最大サイズは 2 TB で、データ ファイルの最大サイズは 16 TB です。

> [!NOTE]
> FILESTREAM コンテナーに対してこのオプションを指定した場合、最大サイズはありません。 ディスクがいっぱいになるまでファイル サイズが拡張します。

FILEGROWTH *growth_increment*: ファイルを自動拡張するときの増加量を指定します。 ファイルの FILEGROWTH の設定を MAXSIZE の設定より大きくすることはできません。 *os_file_name* が UNC パスとして指定されている場合、FILEGROWTH を指定することはできません。 FILEGROWTH は、FILESTREAM ファイル グループには適用されません。

*growth_increment*: 新しい領域が必要とされるたびにファイルに追加される領域の容量です。

値は MB、KB、GB、TB または % の単位で指定できます。 サフィックス MB、KB、または % を付けないで数値を指定した場合の既定値は MB です。 % を指定すると、1 回の増加量は、増加時のファイル サイズに指定されたパーセンテージを掛けた値になります。 指定されたサイズは、最も近い 64 KB 単位の値に丸められ、最小値は 64 KB になります。

0 は、自動拡張がオフで、領域を追加できないことを示します。

FILEGROWTH が指定されていない場合、既定値は次のとおりです。

|Version|既定値|
|-------------|--------------------|
|[!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 以降|データ 64 MB。 ログ ファイル 64 MB。|
|[!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] 以降|データ 1 MB。 ログ ファイル 10%。|
|[!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] の前|データ 10%。 ログ ファイル 10%。|

\<filegroup>: ファイル グループ プロパティを制御します。 ファイル グループは、データベース スナップショットでは指定できません。

FILEGROUP *filegroup_name*: ファイル グループの論理名です。

*filegroup_name*
*filegroup_name* はデータベース内で一意である必要があり、システムで提示された名前である PRIMARY や PRIMARY_LOG にすることはできません。 この名前は、文字定数、UNICODE 定数、標準の識別子、区切られた識別子のいずれでもかまいません。 名前は、[識別子](../../relational-databases/databases/database-identifiers.md)のルールに従っている必要があります。

CONTAINS FILESTREAM: ファイル グループで FILESTREAM バイナリ ラージ オブジェクト (BLOB) をファイル システムに格納することを指定します。

CONTAINS MEMORY_OPTIMIZED_DATA

**適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降

ファイル グループで memory_optimized データをファイル システムに格納することを指定します。 詳細については、「 [インメモリ OLTP - インメモリ最適化](../../relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization.md)」を参照してください。 MEMORY_OPTIMIZED_DATA ファイル グループは、1 つのデータベースにつき 1 つしか許可されません。 メモリ最適化データを格納するファイルグループを作成するコード サンプルについては、「[メモリ最適化テーブルおよびネイティブ コンパイル ストアド プロシージャの作成](../../relational-databases/in-memory-oltp/creating-a-memory-optimized-table-and-a-natively-compiled-stored-procedure.md)」を参照してください。

DEFAULT: 指定されたファイル グループが、データベースの既定のファイル グループであることを指定します。

*database_snapshot_name*: 新しいデータベース スナップショットの名前です。 データベース スナップショット名は、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンス内で一意であり、識別子のルールに従っている必要があります。 *database_snapshot_name* は 128 文字以下です。

ON **(** NAME **=** _logical\_file\_name_ **,** FILENAME **='** _os\_file\_name_ **')** [ **,** ... *n* ]: データベース スナップショットを作成するには、ソース データベースのファイルのリストを指定します。 スナップショットが機能するためには、すべてのデータ ファイルを個別に指定する必要があります。 ただし、データベース スナップショットにログ ファイルは指定できません。 FILESTREAM ファイル グループは、データベース スナップショットではサポートされていません。 CREATE DATABASE ON 句に FILESTREAM データ ファイルが含まれていると、ステートメントが失敗してエラーが発生します。

NAME、FILENAME、およびそれらの値については、相当する \<filespec> 値の説明を参照してください。

> [!NOTE]
> データベース スナップショットを作成する場合、他の \<filespec> オプションおよびキーワード PRIMARY は許可されません。

AS SNAPSHOT OF *source_database_name*: 作成されるデータベースが、*source_database_name* によって指定されたソース データベースのデータベース スナップショットであることを指定します。 スナップショットとソース データベースは同じインスタンス上に存在する必要があります。

詳細については、「解説」セクションの「[データベース スナップショット](#database-snapshots)」を参照してください。

## <a name="remarks"></a>解説

[master データベース](../../relational-databases/databases/master-database.md)は、ユーザー データベースが作成、変更、または削除されるたびにバックアップする必要があります。

`CREATE DATABASE` ステートメントは自動コミット モード (既定のトランザクション管理モード) で実行する必要があり、明示的または暗黙的なトランザクションでは許可されません。

1 つの `CREATE DATABASE` ステートメントを使用して、データベースおよびデータベースを格納するファイルを作成することができます。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、次の手順を使用して CREATE DATABASE ステートメントを実装します。

1. [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] で、[model データベース](../../relational-databases/databases/model-database.md)のコピーを使用して、データベースとそのメタデータを初期化します。
2. Service Broker GUID がデータベースに割り当てられます。
3. 次に、[!INCLUDE[ssDE](../../includes/ssde-md.md)] は、データベース内の領域の使用状況を記録する内部データが格納されるページを除いて、データベースの残りの部分に空のページを挿入します。

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスには、最大 32,767 個のデータベースを指定できます。

各データベースには、データベース内で特殊な操作を実行できる所有者が存在します。 所有者はデータベースを作成するユーザーです。 [ALTER AUTHORIZATION](../../t-sql/statements/alter-authorization-transact-sql.md) を使用して、データベース所有者を変更できます。

一部のデータベース機能は、データベースの機能をすべて利用するためにファイル システムに存在する機能または能力に依存しています。 ファイル システムの機能セットに依存する機能の例をいくつか挙げます。

- DBCC CHECKDB
- FileStream
- VSS とファイルのスナップショットを使用したオンライン バックアップ
- データベース スナップショットの作成
- メモリ最適化データ ファイル グループ

## <a name="database-files-and-filegroups"></a>データベース ファイルとファイル グループ

すべてのデータベースには、*プライマリ ファイル* と *トランザクション ログ ファイル* という少なくとも 2 つのファイル、および少なくとも 1 つのファイル グループがあります。 各データベースに、最大 32,767 のファイルと 32,767 のファイル グループを指定できます。

データベースを作成する際に、データ ファイルのサイズは、データベースに記述されるデータの最大量を基に可能な限り大きく設定しておきます。

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] データベース ファイルのストレージには、ストレージ エリア ネットワーク (SAN)、iSCSI ベースのネットワーク、または、ローカルにアタッチされたディスクを使用することをお勧めします。この構成により、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のパフォーマンスと信頼性を最適化することができるためです。

## <a name="database-snapshots"></a>データベース スナップショット

`CREATE DATABASE` ステートメントを使用して、"*ソース データベース*" の読み取り専用の静的ビューである "*データベース スナップショット*" を作成できます。 データベース スナップショットは、スナップショットが作成された時点で存在していたソース データベースと、トランザクション的に一貫性があります。 ソース データベースは複数のスナップショットを持つことができます。

> [!NOTE]
> データベース スナップショットを作成する際、`CREATE DATABASE` ステートメントを使用して、ログ ファイル、オフライン ファイル、復元ファイル、および現存しないファイルを参照することはできません。

データベース スナップショットの作成に失敗した場合、スナップショットは問題のある状態となり、削除する必要があります。 詳細については、[DROP DATABASE](../../t-sql/statements/drop-database-transact-sql.md) に関するページを参照してください。

各スナップショットは、`DROP DATABASE` を使用して削除されるまで保持されます。

詳細については、[データベース スナップショット](../../relational-databases/databases/database-snapshots-sql-server.md)に関するページを参照してください。

## <a name="database-options"></a>データベース オプション

データベースを作成するたびに、いくつかのデータベース オプションが自動的に設定されます。 これらのオプションの一覧については、[ALTER DATABASE SET オプション](../../t-sql/statements/alter-database-transact-sql-set-options.md)に関するページを参照してください。

## <a name="the-model-database-and-creating-new-databases"></a>model データベースと新しいデータベースの作成

[model データベース](../../relational-databases/databases/model-database.md)内にあるすべてのユーザー定義のオブジェクトは、新しく作成されたすべてのデータベースにコピーされます。 テーブル、ビュー、ストアド プロシージャ、データ型など、あらゆるオブジェクトを model データベースに追加し、新しく作成されたすべてのデータベースに含めることができます。

`CREATE DATABASE <database_name>` ステートメントがサイズ パラメーターを追加せずに指定されている場合、プライマリ データ ファイルは、model データベースのプライマリ ファイルと同じサイズになります。

`FOR ATTACH` が指定されていない限り、それぞれの新しいデータベースは、model データベースからデータベース オプションの設定を継承します。 たとえば、auto shrink データベース オプションは、model データベースにおいても、作成するどの新規データベースにおいても、**true** に設定されます。 model データベースのオプションを変更すると、これらの新しいオプション設定が、作成する新規のデータベースで使用されます。 model データベースの操作の変更は、既存のデータベースには影響を与えません。 CREATE DATABASE ステートメントで FOR ATTACH を指定すると、新しいデータベースは元のデータベースからデータベース オプションの設定を継承します。

## <a name="viewing-database-information"></a>データベース情報の表示

カタログ ビュー、システム関数、およびシステム ストアド プロシージャを使用して、データベース、ファイルおよびファイル グループについての情報を返すことができます。 詳細については、[システム ビュー](../language-reference.md)に関するページを参照してください。

## <a name="permissions"></a>アクセス許可

`CREATE DATABASE`、`CREATE ANY DATABASE`、または `ALTER ANY DATABASE` アクセス許可が必要です。

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]のインスタンス上のディスク使用量を管理するため、通常、データベースを作成する権限をいくつかのログイン アカウントに制限します。

次の例は、データベース ユーザー Fay にデータベースを作成する権限を与えます。

```sql
USE master;
GO
GRANT CREATE DATABASE TO [Fay];
GO
```

### <a name="permissions-on-data-and-log-files"></a>データおよびログ ファイルに対する権限

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、各データベースのデータ ファイルとログ ファイルに一定の権限が設定されます。 次の操作がデータベースに適用されるたびに、次の権限が設定されます。

- アタッチ
- バックアップ
- 作成済み
- デタッチ
- 変更して新しいファイルを追加
- 復元

この権限は、開く権限のあるディレクトリにファイルが存在する場合に、そのファイルが誤って書き換えられるのを防ぎます。

> [!NOTE]
> [!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssExpressEd2005](../../includes/ssexpressed2005-md.md)] では、データ ファイルとログ ファイルの権限は設定されません。

## <a name="examples"></a>例

### <a name="a-creating-a-database-without-specifying-files"></a>A. ファイルを指定せずにデータベースを作成する

次の例では、`mytest` データベースを作成し、対応するプライマリ ファイルおよびトランザクション ログ ファイルを作成します。 ステートメントに \<filespec> 項目が含まれていないため、データベースのプライマリ ファイルは、model データベースのプライマリ ファイルと同じサイズになります。 トランザクション ログは、次の値の大きい方に設定されます。512 KB か、プライマリ データ ファイルのサイズの 25%。 MAXSIZE が指定されていないため、ファイルはディスク上のすべての使用可能な領域いっぱいまで拡張することができます。 この例では、`mytest` という名前のデータベースが既に存在する場合はそれを削除してから、`mytest` データベースを作成する方法も示します。

```sql
USE master;
GO
IF DB_ID (N'mytest') IS NOT NULL
DROP DATABASE mytest;
GO
CREATE DATABASE mytest;
GO
-- Verify the database files and sizes
SELECT name, size, size*1.0/128 AS [Size in MBs]
FROM sys.master_files
WHERE name = N'mytest';
GO
```

### <a name="b-creating-a-database-that-specifies-the-data-and-transaction-log-files"></a>B. データ ファイルとトランザクション ログ ファイルを指定してデータベースを作成する

次の例では、`Sales` データベースを作成します。 PRIMARY キーワードが使用されていないので、最初のファイル (`Sales_dat`) がプライマリ ファイルになります。 `Sales_dat` ファイルの SIZE パラメーターに MB も KB も指定されていないため、ファイルは MB を使用し、メガバイト単位で割り当てられます。 `Sales_log` ファイルは、`SIZE` パラメーターに `MB` サフィックスが明示的に指定されているため、メガバイト単位で割り当てられます。

```sql
USE master;
GO
CREATE DATABASE Sales
ON
( NAME = Sales_dat,
    FILENAME = 'C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\MSSQL\DATA\saledat.mdf',
    SIZE = 10,
    MAXSIZE = 50,
    FILEGROWTH = 5 )
LOG ON
( NAME = Sales_log,
    FILENAME = 'C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\MSSQL\DATA\salelog.ldf',
    SIZE = 5MB,
    MAXSIZE = 25MB,
    FILEGROWTH = 5MB ) ;
GO
```

### <a name="c-creating-a-database-by-specifying-multiple-data-and-transaction-log-files"></a>C. 複数のデータ ファイルとトランザクション ログ ファイルを指定してデータベースを作成する

次の例では、3 つの `Archive` のデータ ファイルと 2 つの `100-MB` のトランザクション ログ ファイルがある `100-MB` データベースを作成します。 プライマリ ファイルはリストの最初のファイルであり、`PRIMARY` キーワードによって明示的に指定されます。 トランザクション ログ ファイルは、`LOG ON` キーワードに続けて指定されます。 `FILENAME` オプションでファイルに使用される拡張子のうち、`.mdf` はプライマリ データ ファイルに、`.ndf` はセカンダリ データ ファイルに、`.ldf` はトランザクション ログ ファイルに、それぞれ使用されます。 この例では、作成するデータベースは、`D:` データベースと同じ場所ではなく `master` ドライブに格納します。

```sql
USE master;
GO
CREATE DATABASE Archive
ON
PRIMARY
    (NAME = Arch1,
    FILENAME = 'D:\SalesData\archdat1.mdf',
    SIZE = 100MB,
    MAXSIZE = 200,
    FILEGROWTH = 20),
    ( NAME = Arch2,
    FILENAME = 'D:\SalesData\archdat2.ndf',
    SIZE = 100MB,
    MAXSIZE = 200,
    FILEGROWTH = 20),
    ( NAME = Arch3,
    FILENAME = 'D:\SalesData\archdat3.ndf',
    SIZE = 100MB,
    MAXSIZE = 200,
    FILEGROWTH = 20)
LOG ON
  (NAME = Archlog1,
    FILENAME = 'D:\SalesData\archlog1.ldf',
    SIZE = 100MB,
    MAXSIZE = 200,
    FILEGROWTH = 20),
  (NAME = Archlog2,
    FILENAME = 'D:\SalesData\archlog2.ldf',
    SIZE = 100MB,
    MAXSIZE = 200,
    FILEGROWTH = 20) ;
GO
```

### <a name="d-creating-a-database-that-has-filegroups"></a>D. ファイル グループのあるデータベースを作成する

次の例では、以下のファイル グループがある `Sales` データベースを作成します。

- `Spri1_dat` ファイルおよび `Spri2_dat` ファイルのあるプライマリ ファイル グループ。 これらのファイルの FILEGROWTH 増加量は、`15%` に指定されています。
- `SGrp1Fi1` ファイルおよび `SGrp1Fi2` ファイルのある、`SalesGroup1` というファイル グループ。
- `SGrp2Fi1` ファイルおよび `SGrp2Fi2` ファイルのある、`SalesGroup2` というファイル グループ。

この例では、データ ファイルとログ ファイルは、パフォーマンスを向上させるために別のディスクに格納します。

```sql
USE master;
GO
CREATE DATABASE Sales
ON PRIMARY
( NAME = SPri1_dat,
    FILENAME = 'D:\SalesData\SPri1dat.mdf',
    SIZE = 10,
    MAXSIZE = 50,
    FILEGROWTH = 15% ),
( NAME = SPri2_dat,
    FILENAME = 'D:\SalesData\SPri2dt.ndf',
    SIZE = 10,
    MAXSIZE = 50,
    FILEGROWTH = 15% ),
FILEGROUP SalesGroup1
( NAME = SGrp1Fi1_dat,
    FILENAME = 'D:\SalesData\SG1Fi1dt.ndf',
    SIZE = 10,
    MAXSIZE = 50,
    FILEGROWTH = 5 ),
( NAME = SGrp1Fi2_dat,
    FILENAME = 'D:\SalesData\SG1Fi2dt.ndf',
    SIZE = 10,
    MAXSIZE = 50,
    FILEGROWTH = 5 ),
FILEGROUP SalesGroup2
( NAME = SGrp2Fi1_dat,
    FILENAME = 'D:\SalesData\SG2Fi1dt.ndf',
    SIZE = 10,
    MAXSIZE = 50,
    FILEGROWTH = 5 ),
( NAME = SGrp2Fi2_dat,
    FILENAME = 'D:\SalesData\SG2Fi2dt.ndf',
    SIZE = 10,
    MAXSIZE = 50,
    FILEGROWTH = 5 )
LOG ON
( NAME = Sales_log,
    FILENAME = 'E:\SalesLog\salelog.ldf',
    SIZE = 5MB,
    MAXSIZE = 25MB,
    FILEGROWTH = 5MB ) ;
GO
```

### <a name="e-attaching-a-database"></a>E. データベースをアタッチする

次の例では、例 D で作成された `Archive` データベースをデタッチしてから、`FOR ATTACH` 句を使用してこのデータベースをアタッチします。 `Archive` は、複数のデータおよびログ ファイルを保有するように定義されています。 しかし、ファイルの場所が作成時から変更されていないため、`FOR ATTACH` 句に指定する必要があるのは、プライマリ ファイルのみです。 [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] 以降では、アタッチされているデータベースの一部であるフルテキスト ファイルは、データベースと共にアタッチされます。

```sql
USE master;
GO
sp_detach_db Archive;
GO
CREATE DATABASE Archive
  ON (FILENAME = 'D:\SalesData\archdat1.mdf')
  FOR ATTACH ;
GO
```

### <a name="f-creating-a-database-snapshot"></a>F. データベース スナップショットを作成する

次の例では、`sales_snapshot0600` データベース スナップショットを作成します。 データベース スナップショットは読み取り専用であるため、ログ ファイルは指定できません。 構文に準拠して、ソース データベース内のすべてのファイルが指定され、ファイル グループは指定されません。

この例で使用するソース データベースは、例 D で作成された `Sales` データベースです。

```sql
USE master;
GO
CREATE DATABASE sales_snapshot0600 ON
    ( NAME = SPri1_dat, FILENAME = 'D:\SalesData\SPri1dat_0600.ss'),
    ( NAME = SPri2_dat, FILENAME = 'D:\SalesData\SPri2dt_0600.ss'),
    ( NAME = SGrp1Fi1_dat, FILENAME = 'D:\SalesData\SG1Fi1dt_0600.ss'),
    ( NAME = SGrp1Fi2_dat, FILENAME = 'D:\SalesData\SG1Fi2dt_0600.ss'),
    ( NAME = SGrp2Fi1_dat, FILENAME = 'D:\SalesData\SG2Fi1dt_0600.ss'),
    ( NAME = SGrp2Fi2_dat, FILENAME = 'D:\SalesData\SG2Fi2dt_0600.ss')
AS SNAPSHOT OF Sales ;
GO
```

### <a name="g-creating-a-database-and-specifying-a-collation-name-and-options"></a>G. データベースを作成し、照合順序名とオプションを指定する

次の例では、`MyOptionsTest` データベースを作成します。 照合順序名が指定され、`TRUSTYWORTHY` および `DB_CHAINING` オプションが `ON` に設定されます。

```sql
USE master;
GO
IF DB_ID (N'MyOptionsTest') IS NOT NULL
DROP DATABASE MyOptionsTest;
GO
CREATE DATABASE MyOptionsTest
COLLATE French_CI_AI
WITH TRUSTWORTHY ON, DB_CHAINING ON;
GO
--Verifying collation and option settings.
SELECT name, collation_name, is_trustworthy_on, is_db_chaining_on
FROM sys.databases
WHERE name = N'MyOptionsTest';
GO
```

### <a name="h-attaching-a-full-text-catalog-that-has-been-moved"></a>H. 移動されたフルテキスト カタログをアタッチする

次の例では、フルテキスト カタログ `AdvWksFtCat` を `AdventureWorks2012` のデータおよびログ ファイルと共にアタッチします。 この例では、フルテキスト カタログは、既定の場所から新しい場所、`c:\myFTCatalogs` に移されます。 データおよびログ ファイルは、それぞれの既定の場所に残ります。

```sql
USE master;
GO
--Detach the AdventureWorks2012 database
sp_detach_db AdventureWorks2012;
GO
-- Physically move the full text catalog to the new location.
--Attach the AdventureWorks2012 database and specify the new location of the full-text catalog.
CREATE DATABASE AdventureWorks2012 ON
    (FILENAME = 'c:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\MSSQL\Data\AdventureWorks2012_data.mdf'),
    (FILENAME = 'c:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\MSSQL\Data\AdventureWorks2012_log.ldf'),
    (FILENAME = 'c:\myFTCatalogs\AdvWksFtCat')
FOR ATTACH;
GO
```

### <a name="i-creating-a-database-that-specifies-a-row-filegroup-and-two-filestream-filegroups"></a>I. 1 つの ROW ファイル グループと 2 つの FILESTREAM ファイル グループを指定してデータベースを作成する

次の例では、`FileStreamDB` データベースを作成します。 データベースは、1 つの ROW ファイル グループと 2 つの FILESTREAM ファイル グループを使用して作成されます。 各ファイル グループには、1 つのファイルが含まれます。

- `FileStreamDB_data` には行データが含まれます。 これには、既定のパスを指定した 1 つのファイル `FileStreamDB_data.mdf` が含まれます。
- `FileStreamPhotos` には FILESTREAM データが含まれます。 これには、`FSPhotos` (`C:\MyFSfolder\Photos` にある) と `FSPhotos2` (`D:\MyFSfolder\Photos` にある) の 2 つの FILESTREAM データ コンテナーが含まれます。 また、既定の FILESTREAM ファイル グループとしてマークされます。
- `FileStreamResumes` には FILESTREAM データが含まれます。 これには、1 つの FILESTREAM データ コンテナー `FSResumes` (`C:\MyFSfolder\Resumes` にある) が含まれます。

```sql
USE master;
GO
-- Get the SQL Server data path.
DECLARE @data_path nvarchar(256);
SET @data_path = (SELECT SUBSTRING(physical_name, 1, CHARINDEX(N'master.mdf', LOWER(physical_name)) - 1)
      FROM master.sys.master_files
      WHERE database_id = 1 AND file_id = 1);

 -- Execute the CREATE DATABASE statement.
EXECUTE ('CREATE DATABASE FileStreamDB
ON PRIMARY
    (
    NAME = FileStreamDB_data
    ,FILENAME = ''' + @data_path + 'FileStreamDB_data.mdf''
    ,SIZE = 10MB
    ,MAXSIZE = 50MB
    ,FILEGROWTH = 15%
    ),
FILEGROUP FileStreamPhotos CONTAINS FILESTREAM DEFAULT
    (
    NAME = FSPhotos
    ,FILENAME = ''C:\MyFSfolder\Photos''
-- SIZE and FILEGROWTH should not be specified here.
-- If they are specified an error will be raised.
, MAXSIZE = 5000 MB
    ),
    (
      NAME = FSPhotos2
      , FILENAME = ''D:\MyFSfolder\Photos''
      , MAXSIZE = 10000 MB
     ),
FILEGROUP FileStreamResumes CONTAINS FILESTREAM
    (
    NAME = FileStreamResumes
    ,FILENAME = ''C:\MyFSfolder\Resumes''
    )
LOG ON
    (
    NAME = FileStream_log
    ,FILENAME = ''' + @data_path + 'FileStreamDB_log.ldf''
    ,SIZE = 5MB
    ,MAXSIZE = 25MB
    ,FILEGROWTH = 5MB
    )'
);
GO
```

### <a name="j-creating-a-database-that-has-a-filestream-filegroup-with-multiple-files"></a>J. 複数のファイルを含む FILESTREAM ファイル グループのあるデータベースを作成する

次の例では、`BlobStore1` データベースを作成します。 データベースは、1 つの ROW ファイル グループと、`FS` という 1 つの FILESTREAM ファイル グループを使用して作成されます。 FILESTREAM ファイル グループには、`FS1` および `FS2` の 2 つのファイルが含まれています。 その後 `FS3` という 3 つ目のファイルが FILESTREAM ファイルグループに追加されると、データベースが変更されます。

```sql
USE master;
GO

CREATE DATABASE [BlobStore1]
CONTAINMENT = NONE
ON PRIMARY
(
    NAME = N'BlobStore1',
    FILENAME = N'C:\BlobStore\BlobStore1.mdf',
    SIZE = 100MB,
    MAXSIZE = UNLIMITED,
    FILEGROWTH = 1MB
),
FILEGROUP [FS] CONTAINS FILESTREAM DEFAULT
(  
    NAME = N'FS1',
    FILENAME = N'C:\BlobStore\FS1',
    MAXSIZE = UNLIMITED
),
(
    NAME = N'FS2',
    FILENAME = N'C:\BlobStore\FS2',
    MAXSIZE = 100MB
)
LOG ON
(
    NAME = N'BlobStore1_log',
    FILENAME = N'C:\BlobStore\BlobStore1_log.ldf',
    SIZE = 100MB,
    MAXSIZE = 1GB,
    FILEGROWTH = 1MB
);
GO

ALTER DATABASE [BlobStore1]
ADD FILE
(
    NAME = N'FS3',
    FILENAME = N'C:\BlobStore\FS3',
    MAXSIZE = 100MB
)
TO FILEGROUP [FS];
GO
```

## <a name="see-also"></a>参照

- [ALTER DATABASE](../../t-sql/statements/alter-database-transact-sql.md)
- [データベースのデタッチとアタッチ](../../relational-databases/databases/database-detach-and-attach-sql-server.md)
- [DROP DATABASE](../../t-sql/statements/drop-database-transact-sql.md)
- [EVENTDATA](../../t-sql/functions/eventdata-transact-sql.md)
- [ALTER AUTHORIZATION](../../t-sql/statements/alter-authorization-transact-sql.md)
- [sp_detach_db](../../relational-databases/system-stored-procedures/sp-detach-db-transact-sql.md)
- [sp_removedbreplication](../../relational-databases/system-stored-procedures/sp-removedbreplication-transact-sql.md)
- [データベース スナップショット](../../relational-databases/databases/database-snapshots-sql-server.md)
- [データベース ファイルの移動](../../relational-databases/databases/move-database-files.md)
- [データベース](../../relational-databases/databases/databases.md)
- [バイナリ ラージ オブジェクト - BLOB データ](../../relational-databases/blob/binary-large-object-blob-data-sql-server.md)

::: moniker-end
::: moniker range="=azuresqldb-current"

:::row:::
    :::column:::
        [SQL Server](create-database-transact-sql.md?view=sql-server-ver15&preserve-view=true)
    :::column-end:::
    :::column:::
        **_\* SQL Database \*_**
    :::column-end:::
    :::column:::
        [SQL Managed Instance](create-database-transact-sql.md?view=azuresqldb-mi-current&preserve-view=true)
    :::column-end:::
    :::column:::
        [Azure Synapse<br />Analytics](create-database-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)
    :::column-end:::
    :::column:::
        [Analytics Platform<br />System (PDW)](create-database-transact-sql.md?view=aps-pdw-2016&preserve-view=true)
    :::column-end:::
:::row-end:::

&nbsp;

## <a name="sql-database"></a>SQL Database

## <a name="overview"></a>概要

[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] では、Azure SQL サーバーでこのステートメントを使って、単一のデータベースまたはエラスティック プール内のデータベースを作成できます。 このステートメントで、データベース名、照合順序、最大サイズ、エディション、サービス目標、および該当する場合は新しいデータベースのエラスティック プールを指定します。 また、エラスティック プールにデータベースを作成することにも使用できます。 さらに、別の SQL Database サーバー上にデータベースのコピーを作成することもできます。

## <a name="syntax"></a>構文

### <a name="create-a-database"></a>データベースを作成する

```syntaxsql
CREATE DATABASE database_name [ COLLATE collation_name ]
{
  (<edition_options> [, ...n])
}
[ WITH <with_options> [,..n]]
[;]

<with_options> ::=
{
  CATALOG_COLLATION = { DATABASE_DEFAULT | SQL_Latin1_General_CP1_CI_AS }
  | BACKUP_STORAGE_REDUNDANCY = { 'LOCAL' | 'ZONE' | 'GEO' }
}

<edition_options> ::=
{

  MAXSIZE = { 100 MB | 500 MB | 1 ... 1024 ... 4096 GB }
  | ( EDITION = { 'Basic' | 'Standard' | 'Premium' | 'GeneralPurpose' | 'BusinessCritical' | 'Hyperscale' }
  | SERVICE_OBJECTIVE =
    { 'Basic' | 'S0' | 'S1' | 'S2' | 'S3' | 'S4'| 'S6'| 'S7'| 'S9'| 'S12'
      | 'P1' | 'P2' | 'P4'| 'P6' | 'P11' | 'P15'
      | 'GP_Gen4_1' | 'GP_Gen4_2' | 'GP_Gen4_3' | 'GP_Gen4_4' | 'GP_Gen4_5' | 'GP_Gen4_6'
      | 'GP_Gen4_7' | 'GP_Gen4_8' | 'GP_Gen4_9' | 'GP_Gen4_10' | 'GP_Gen4_16' | 'GP_Gen4_24'
      | 'GP_Gen5_2' | 'GP_Gen5_4' | 'GP_Gen5_6' | 'GP_Gen5_8' | 'GP_Gen5_10' | 'GP_Gen5_12' | 'GP_Gen5_14'
      | 'GP_Gen5_16' | 'GP_Gen5_18' | 'GP_Gen5_20' | 'GP_Gen5_24' | 'GP_Gen5_32' | 'GP_Gen5_40' | 'GP_Gen5_80'
      | 'GP_Fsv2_8' | 'GP_Fsv2_10' | 'GP_Fsv2_12' | 'GP_Fsv2_14' | 'GP_Fsv2_16' | 'GP_Fsv2_18'
      | 'GP_Fsv2_20' | 'GP_Fsv2_24' | 'GP_Fsv2_32' | 'GP_Fsv2_36' | 'GP_Fsv2_72'
      | 'GP_S_Gen5_1' | 'GP_S_Gen5_2' | 'GP_S_Gen5_4' | 'GP_S_Gen5_6' | 'GP_S_Gen5_8'
      | 'GP_S_Gen5_10' | 'GP_S_Gen5_12' | 'GP_S_Gen5_14' | 'GP_S_Gen5_16'
      | 'GP_S_Gen5_18' | 'GP_S_Gen5_20' | 'GP_S_Gen5_24' | 'GP_S_Gen5_32' | 'GP_S_Gen5_40'
      | 'BC_Gen4_1' | 'BC_Gen4_2' | 'BC_Gen4_3' | 'BC_Gen4_4' | 'BC_Gen4_5' | 'BC_Gen4_6'
      | 'BC_Gen4_7' | 'BC_Gen4_8' | 'BC_Gen4_9' | 'BC_Gen4_10' | 'BC_Gen4_16' | 'BC_Gen4_24'
      | 'BC_Gen5_2' | 'BC_Gen5_4' | 'BC_Gen5_6' | 'BC_Gen5_8' | 'BC_Gen5_10' | 'BC_Gen5_12' | 'BC_Gen5_14'
      | 'BC_Gen5_16' | 'BC_Gen5_18' | 'BC_Gen5_20' | 'BC_Gen5_24' | 'BC_Gen5_32' | 'BC_Gen5_40' | 'BC_Gen5_80'
      | 'BC_M_8' | 'BC_M_10' | 'BC_M_12' | 'BC_M_14' | 'BC_M_16' | 'BC_M_18'
      | 'BC_M_20' | 'BC_M_24' | 'BC_M_32' | 'BC_M_64' | 'BC_M_128'
      | 'HS_GEN4_1' | 'HS_GEN4_2' | 'HS_GEN4_4' | 'HS_GEN4_8' | 'HS_GEN4_16' | 'HS_GEN4_24'
      | 'HS_GEN5_2' | 'HS_GEN5_4' | 'HS_GEN5_8' | 'HS_GEN5_16' | 'HS_GEN5_24' | 'HS_GEN5_32' | 'HS_GEN5_48' | 'HS_GEN5_80'
      | { ELASTIC_POOL(name = <elastic_pool_name>) } })
}
```

### <a name="copy-a-database"></a>データベースをコピーする

```syntaxsql
CREATE DATABASE database_name
    AS COPY OF [source_server_name.] source_database_name
    [ ( SERVICE_OBJECTIVE =
      { 'Basic' |'S0' | 'S1' | 'S2' | 'S3'| 'S4'| 'S6'| 'S7'| 'S9'| 'S12'
      | 'P1' | 'P2' | 'P4'| 'P6' | 'P11' | 'P15'
      | 'GP_Gen4_1' | 'GP_Gen4_2' | 'GP_Gen4_3' | 'GP_Gen4_4' | 'GP_Gen4_5' | 'GP_Gen4_6'
      | 'GP_Gen4_7' | 'GP_Gen4_8' | 'GP_Gen4_9' | 'GP_Gen4_10' | 'GP_Gen4_16' | 'GP_Gen4_24'
      | 'GP_Gen5_2' | 'GP_Gen5_4' | 'GP_Gen5_6' | 'GP_Gen5_8' | 'GP_Gen5_10' | 'GP_Gen5_12' | 'GP_Gen5_14'
      | 'GP_Gen5_16' | 'GP_Gen5_18' | 'GP_Gen5_20' | 'GP_Gen5_24' | 'GP_Gen5_32' | 'GP_Gen5_40' | 'GP_Gen5_80'
      | 'GP_Fsv2_8' | 'GP_Fsv2_10' | 'GP_Fsv2_12' | 'GP_Fsv2_14' | 'GP_Fsv2_16' | 'GP_Fsv2_18'
      | 'GP_Fsv2_20' | 'GP_Fsv2_24' | 'GP_Fsv2_32' | 'GP_Fsv2_36' | 'GP_Fsv2_72'
      | 'GP_S_Gen5_1' | 'GP_S_Gen5_2' | 'GP_S_Gen5_4' | 'GP_S_Gen5_6' | 'GP_S_Gen5_8'
      | 'GP_S_Gen5_10' | 'GP_S_Gen5_12' | 'GP_S_Gen5_14' | 'GP_S_Gen5_16'
      | 'GP_S_Gen5_18' | 'GP_S_Gen5_20' | 'GP_S_Gen5_24' | 'GP_S_Gen5_32' | 'GP_S_Gen5_40'
      | 'BC_Gen4_1' | 'BC_Gen4_2' | 'BC_Gen4_3' | 'BC_Gen4_4' | 'BC_Gen4_5' | 'BC_Gen4_6'
      | 'BC_Gen4_7' | 'BC_Gen4_8' | 'BC_Gen4_9' | 'BC_Gen4_10' | 'BC_Gen4_16' | 'BC_Gen4_24'
      | 'BC_Gen5_2' | 'BC_Gen5_4' | 'BC_Gen5_6' | 'BC_Gen5_8' | 'BC_Gen5_10' | 'BC_Gen5_12' | 'BC_Gen5_14'
      | 'BC_Gen5_16' | 'BC_Gen5_18' | 'BC_Gen5_20' | 'BC_Gen5_24' | 'BC_Gen5_32' | 'BC_Gen5_40' | 'BC_Gen5_80'
      | 'BC_M_8' | 'BC_M_10' | 'BC_M_12' | 'BC_M_14' | 'BC_M_16' | 'BC_M_18'
      | 'BC_M_20' | 'BC_M_24' | 'BC_M_32' | 'BC_M_64' | 'BC_M_128'
      | 'HS_GEN4_1' | 'HS_GEN4_2' | 'HS_GEN4_4' | 'HS_GEN4_8' | 'HS_GEN4_16' | 'HS_GEN4_24'
      | 'HS_GEN5_2' | 'HS_GEN5_4' | 'HS_GEN5_8' | 'HS_GEN5_16' | 'HS_GEN5_24' | 'HS_GEN5_32' | 'HS_GEN5_48' | 'HS_GEN5_80'
      | { ELASTIC_POOL(name = <elastic_pool_name>) } })
   ]
[;]
```

## <a name="arguments"></a>引数

*database_name*: 新しいデータベースの名前です。 この名前は、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 上で一意であり、識別子に関する [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の規則に準拠している必要があります。 詳細については、「[データベース識別子](../../relational-databases/databases/database-identifiers.md)」を参照してください。

*Collation_name*: データベースの既定の照合順序を指定します。 照合順序名には、Windows 照合順序名または SQL 照合順序名を指定できます。 指定しない場合、既定の照合順序である SQL_Latin1_General_CP1_CI_AS がデータベースに割り当てられます。

Windows と SQL の照合順序名の詳細については、[COLLATE (Transact-SQL)](../../t-sql/statements/collations.md) に関するページを参照してください。

CATALOG_COLLATION: メタデータ カタログの既定の照合順序を指定します。 *DATABASE_DEFAULT* では、データベースの既定の照合順序と一致させるためにシステム ビューとシステム テーブルで使用されたメタデータ カタログが照合されることが指定されます。 これは、SQL Server で見られる動作です。

*SQL_Latin1_General_CP1_CI_AS* では、固定照合順序 SQL_Latin1_General_CP1_CI_AS に対してシステム ビューとテーブルで使用されたメタデータ カタログが照合されることが指定されます。 指定がない場合、これは Azure SQL Database の既定の設定です。

BACKUP_STORAGE_REDUNDANCY により、データベースのポイントインタイム リストア バックアップおよび長期保有バックアップがどのように複製されるかが指定されます。 Geo リストアまたは地域的な障害からの復旧機能は、データベースが "GEO" バックアップ ストレージの冗長性を使用して作成されている場合にのみ使用できます。 明示的に指定しない限り、T-SQL で作成されたデータベースは geo 冗長バックアップ ストレージを使用します。 

> [!IMPORTANT]
> Azure SQL Database の BACKUP_STORAGE_REDUNDANCY オプションは、ブラジル南部ではパブリック プレビューとして利用でき、一般公開されているのは東南アジアの Azure リージョンのみです。  

EDITION: データベースのサービス層を指定します。

単一およびプールされたデータベース。 使用できる値は次のとおりです。'Basic'、'Standard'、'Premium'、'GeneralPurpose'、'BusinessCritical'、'Hyperscale'。

MAXSIZE: データベースの最大サイズを指定します。 MAXSIZE は、指定されている EDITION (サービス層) に対して有効である必要があります。各サービス層でサポートされる MAXSIZE 値と既定値 (D) は次のとおりです。

> [!NOTE]
> **MAXSIZE** 引数は、ハイパースケール サービス層の単一データベースには適用されません。 ハイパースケール層のデータベースは、必要に応じて 100 TB まで拡張できます。 SQL Database サービスによってストレージが自動的に追加されます。最大サイズを設定する必要はありません。

**SQL Database サーバー上の単一のデータベースおよびプールされたデータベースの DTU モデル**

|**MAXSIZE**|**Basic**|**S0-S2**|**S3-S12**|**P1-P6**| **P11-P15** |
|:---|:---|:---|:---|:---|:---|
|100 MB|√|√|√|√|√|
|500 MB|√|√|√|√|√|
|1 GB|√|√|√|√|√|
|2 GB|√ (D)|√|√|√|√|
|5 GB|該当なし|√|√|√|√|
|10 GB|該当なし|√|√|√|√|
|20 GB|該当なし|√|√|√|√|
|30 GB|該当なし|√|√|√|√|
|40 GB|該当なし|√|√|√|√|
|50 GB|該当なし|√|√|√|√|
|100 GB|該当なし|√|√|√|√|
|150 GB|該当なし|√|√|√|√|
|200 GB|該当なし|√|√|√|√|
|250 GB|該当なし|√ (D)|√ (D)|√|√|
|300 GB|該当なし|該当なし|√|√|√|
|400 GB|該当なし|該当なし|√|√|√|
|500 GB|該当なし|該当なし|√|√ (D)|√|
|750 GB|該当なし|該当なし|√|√|√|
|1024 GB|該当なし|該当なし|√|√|√ (D)|
|1024 GB から 4096 GB (256 GB ずつ増分)* |該当なし|なし|なし|該当なし|√|

\* P11 と P15 では 1024 GB を既定のサイズとして MAXSIZE が 4 TB まで許可されます。 P11 と P15 では、追加料金なしで付属のストレージを 4 TB まで使用できます。 次の地域の Premium レベルでは、現在 1 TB を超える MAXSIZE を使用できます: 米国東部 2、米国西部、US Gov バージニア、西ヨーロッパ、ドイツ中部、東南アジア、東日本、オーストラリア東部、カナダ中部、カナダ東部。 DTU モデルのリソースの制限事項に関する詳細については、[DTU リソースの制限](/azure/sql-database/sql-database-dtu-resource-limits)に関する記事を参照してください。

DTU モデルの MAXSIZE 値。指定される場合は、上記の表に示すように指定されたサービス レベルで有効な値である必要があります。

**仮想コア モデル**

**General Purpose - プロビジョニング済みコンピューティング - Gen4 (パート 1)**

|MAXSIZE|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6|
|:----- | ------: |-------: |-------: |-------: |-------: |--------:|
|データの最大サイズ (GB)|1024|1024|1024|1536|1536|1536|

**General Purpose - プロビジョニング済みコンピューティング - Gen4 (パート 2)**

|MAXSIZE|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24
|:----- | ------: |-------: |-------: |-------: |-------: |--------:|
|データの最大サイズ (GB)|1536|3072|3072|3072|4096|4096|

**General Purpose - プロビジョニング済みコンピューティング - Gen5 (パート 1)**

|MAXSIZE|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:----- | ------: |-------: |-------: |-------: |--------: |---------:|--------: |
|データの最大サイズ (GB)|1024|1024|1024|1536|1536|1536|1536|

**General Purpose - プロビジョニング済みコンピューティング - Gen5 (パート 2)**

|MAXSIZE|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:----- | ------: |-------: |-------: |-------: |--------: |---------:|--------: |
|データの最大サイズ (GB)|3072|3072|3072|4096|4096|4096|4096|

**General Purpose - プロビジョニング済みコンピューティング - Fsv2 シリーズ (パート 1)**

|MAXSIZE|GP_Fsv2_8|GP_Fsv2_10|GP_Fsv2_12|GP_Fsv2_14|GP_Fsv2_16|GP_Fsv2_18|
|:----- | ------: | ------: | ------: | ------: | ------: | ------: |
|データの最大サイズ (GB)|1024|1024|1024|1024|1536|1536|

**General Purpose - プロビジョニング済みコンピューティング - Fsv2 シリーズ (パート 2)**

|MAXSIZE|GP_Fsv2_20|GP_Fsv2_24|GP_Fsv2_32|GP_Fsv2_36|GP_Fsv2_72|
|:----- | ------: | ------: | ------: | ------: | ------: |
|データの最大サイズ (GB)|1536|1536|3072|3072|4096|

**General Purpose - サーバーレス コンピューティング - Gen5 (パート 1)**

|MAXSIZE|GP_S_Gen5_1|GP_S_Gen5_2|GP_S_Gen5_4|GP_S_Gen5_6|GP_S_Gen5_8|
|:----- | ------: |-------: |-------: |-------: |--------: |
|最大仮想コア数|1|2|4|6|8|

**General Purpose - サーバーレス コンピューティング - Gen5 (パート 2)**

|MAXSIZE|GP_S_Gen5_10|GP_S_Gen5_12|GP_S_Gen5_14|GP_S_Gen5_16|
|:----- | ------: |-------: |-------: |-------: |
|最大仮想コア数|10|12|14|16|

**General Purpose - サーバーレス コンピューティング - Gen5 (パート 3)**

|MAXSIZE|GP_S_Gen5_18|GP_S_Gen5_20|GP_S_Gen5_24|GP_S_Gen5_32|GP_S_Gen5_40|
|:----- | ------: |-------: |-------: |-------: |--------: |
|最大仮想コア数|18|20|24|32|40|

**Business Critical - プロビジョニング済みコンピューティング - Gen4 (パート 1)**

|コンピューティング サイズ (サービス目標)|BC_Gen4_1|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--------------- | ------: |-------: |-------: |-------: |-------: |-------: |
|データの最大サイズ (GB)|1024|1024|1024|1024|1024|1024|

**Business Critical - プロビジョニング済みコンピューティング - Gen4 (パート 2)**

|コンピューティング サイズ (サービス目標)|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--------------- | ------: |-------: |-------: |--------: |--------: |--------: |
|データの最大サイズ (GB)|1024|1024|1024|1024|1024|1024|

**Business Critical - プロビジョニング済みコンピューティング - Gen5 (パート 1)**

|MAXSIZE|BC_Gen5_2|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:----- | ------: |-------: |-------: |-------: |---------: |--------:|--------: |
|データの最大サイズ (GB)|1024|1024|1024|1536|1536|1536|1536|

**Business Critical - プロビジョニング済みコンピューティング - Gen5 (パート 2)**

|MAXSIZE|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:----- | -------: |--------: |--------: |--------: |--------: |---------:|--------: |
|データの最大サイズ (GB)|3072|3072|3072|4096|4096|4096|4096|

**Business Critical - プロビジョニング済みコンピューティング - M シリーズ (パート 1)**

|MAXSIZE|BC_M_8|BC_M_10|BC_M_12|BC_M_14|BC_M_16|BC_M_18|
|:----- | -------: | -------: | -------: | -------: | -------: | -------: |
|データの最大サイズ (GB)|512|640|768|896|1024|1152|

**Business Critical - プロビジョニング済みコンピューティング - M シリーズ (パート 2)**

|MAXSIZE|BC_M_20|BC_M_24|BC_M_32|BC_M_64|BC_M_128|
|:----- | -------: | -------: | -------: | -------: | -------: |
|データの最大サイズ (GB)|1280|1536|2048|4096|4096|

仮想コア モデルを使用するときに `MAXSIZE` 値が設定されていない場合、既定値は 32 GB です。 仮想コア モデルのリソースの制限事項の詳細については、[仮想コア リソースの制限](/azure/sql-database/sql-database-dtu-resource-limits)に関するページを参照してください。

引数 MAXSIZE および EDITION には、以下の規則が適用されます。

- EDITION が指定され、MAXSIZE が指定されていない場合は、エディションの既定値が使用されます。 たとえば、EDITION が Standard に設定され、MAXSIZE が指定されていない場合、MAXSIZE は自動的に 250 MB に設定されます。
- MAXSIZE も EDITION も指定されていない場合、EDITION は `GeneralPurpose` に設定され、MAXSIZE は 32 GB に設定されます。

SERVICE_OBJECTIVE

- **単一のデータベースおよびプールされたデータベースの場合**

  - コンピューティング サイズ (サービス目標) を指定します。 サービスの目標に使用できる値は、`S0`、`S1`、`S2`、`S3`、`S4`、`S6`、`S7`、`S9`、`S12`、`P1`、`P2`、`P4`、`P6`、`P11`、`P15`、`GP_GEN4_1`、`GP_GEN4_2`、`GP_GEN4_3`、`GP_GEN4_4`、`GP_GEN4_5`、`GP_GEN4_6`、`GP_GEN4_7`、`GP_GEN4_8`、`GP_GEN4_7`、`GP_GEN4_8`、`GP_GEN4_9`、`GP_GEN4_10`、`GP_GEN4_16`、`GP_GEN4_24`、`BC_GEN4_1`、`BC_GEN4_2`、`BC_GEN4_3`、`BC_GEN4_4`、`BC_GEN4_5`、`BC_GEN4_6`、`BC_GEN4_7`、`BC_GEN4_8`、`BC_GEN4_9`、`BC_GEN4_10`、`BC_GEN4_16`、`BC_GEN4_24`、`GP_Gen5_2`、`GP_Gen5_4`、`GP_Gen5_6`、`GP_Gen5_8`、`GP_Gen5_10`、`GP_Gen5_12`、`GP_Gen5_14`、`GP_Gen5_16`、`GP_Gen5_18`、`GP_Gen5_20`、`GP_Gen5_24`、`GP_Gen5_32`、`GP_Gen5_40`、`GP_Gen5_80`、`GP_Fsv2_8`、`GP_Fsv2_10`、`GP_Fsv2_12`、`GP_Fsv2_14`、`GP_Fsv2_16`、`GP_Fsv2_18`、`GP_Fsv2_20`、`GP_Fsv2_24`、`GP_Fsv2_32`、`GP_Fsv2_36`、`GP_Fsv2_72`、`BC_Gen5_2`、`BC_Gen5_4`、`BC_Gen5_6`、`BC_Gen5_8`、`BC_Gen5_10`、`BC_Gen5_12`、`BC_Gen5_14`、`BC_Gen5_16`、`BC_Gen5_18`、`BC_Gen5_20`、`BC_Gen5_24`、`BC_Gen5_32`、`BC_Gen5_40`、`BC_Gen5_80`、`BC_M_8`、`BC_M_10`、`BC_M_12`、`BC_M_14`、`BC_M_16`、`BC_M_18`、`BC_M_20`、`BC_M_24`、`BC_M_32`、`BC_M_64`、`BC_M_128` です。

- **サーバーレス データベースの場合**
- 
  - コンピューティング サイズ (サービス目標) を指定します。 サービスの目標に使用できる値は、`GP_S_Gen5_1`、`GP_S_Gen5_2`、`GP_S_Gen5_4`、`GP_S_Gen5_6`、`GP_S_Gen5_8`、`GP_S_Gen5_10`、`GP_S_Gen5_12`、`GP_S_Gen5_14`、`GP_S_Gen5_16`、`GP_S_Gen5_18`、`GP_S_Gen5_20`、`GP_S_Gen5_24`、`GP_S_Gen5_32`、`GP_S_Gen5_40` です。

- **ハイパースケール サービス層の単一データベースの場合**

  - コンピューティング サイズ (サービス目標) を指定します。 サービスの目標に使用できる値は、`HS_GEN4_1`、`HS_GEN4_2`、`HS_GEN4_4`、`HS_GEN4_8`、`HS_GEN4_16`、`HS_GEN4_24`、`HS_Gen5_2`、`HS_Gen5_4`、`HS_Gen5_8`、`HS_Gen5_16`、`HS_Gen5_24`、`HS_Gen5_32`、`HS_Gen5_48`、`HS_Gen5_80` です。

サービス目標に関する説明およびサイズ、エディション、サービス目標の組み合わせの詳細については、「[Azure SQL データベースのサービス階層](/azure/sql-database/sql-database-service-tiers)」をご覧ください。 指定した SERVICE_OBJECTIVE が EDITION によってサポートされていない場合は、エラーが返されます。 SERVICE_OBJECTIVE の値をある階層から別の階層に変更する場合 (たとえば、S1 から P1) は、EDITION の値も変更する必要があります。 サービス目標に関する説明およびサイズ、エディション、サービス目標の組み合わせの詳細については、[Azure SQL Database サービス レベルとパフォーマンス レベル](/azure/azure-sql/database/purchasing-models)、[DTU リソースの制限](/azure/sql-database/sql-database-dtu-resource-limits)、[仮想コア リソースの制限](/azure/sql-database/sql-database-dtu-resource-limits)に関する記事を参照してください。 PRS サービスの目標のサポートはなくなりました。 質問については、電子メール エイリアス premium-rs@microsoft.com を使用してください。

ELASTIC_POOL (name = \<elastic_pool_name>) **適用対象:** 単一のデータベースおよびプールされたデータベースのみ。 ハイパースケール サービス層のデータベースには適用されません。
弾力性のあるデータベース プールで新しいデータベースを作成するには、データベースの SERVICE_OBJECTIVE を ELASTIC_POOL に設定し、プールの名前を指定します。 詳しくは、[SQL Database エラスティック プールの作成と管理](/azure/azure-sql/database/elastic-pool-overview)に関するページをご覧ください。

AS COPY OF [source_server_name.]source_database_name **適用対象:** 単一のデータベースおよびプールされたデータベースのみ。
データベースを同じ [!INCLUDE[ssSDS](../../includes/sssds-md.md)] サーバーにコピーするか別のサーバーにコピーするかを指定します。

*source_server_name*: ソース データベースが配置されている [!INCLUDE[ssSDS](../../includes/sssds-md.md)] サーバーの名前。 このパラメーターは、ソース データベースと対象データベースが同じ [!INCLUDE[ssSDS](../../includes/sssds-md.md)] サーバー上にある場合は省略可能です。

> [!NOTE]
> `AS COPY OF` 引数で一意の完全修飾ドメイン名を使用することはできません。 つまり、サーバーの完全修飾ドメイン名が `serverName.database.windows.net` である場合は、`serverName` のみをデータベース コピー中に使用します。

*source_database_name*

コピーするデータベースの名前。

## <a name="remarks"></a>解説

[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 内のデータベースについては、データベースの作成時に設定される既定の設定がいくつかあります。 これらの既定の設定の詳細については、「[DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md)」の値の一覧を参照してください。

`MAXSIZE` を使用して、データベースのサイズを制限できます。 データベースのサイズが `MAXSIZE` に達すると、エラー コード 40544 が返されます。 このエラーが発生すると、データを挿入、更新したり、新しいオブジェクト (テーブル、ストアド プロシージャ、ビュー、関数など) を作成したりできなくなります。 データの読み取りと削除、テーブルの切り捨て、テーブルとインデックスの削除、およびインデックスの再構築は引き続き可能です。 これを解決するには、`MAXSIZE` を現在のデータベースのサイズより大きい値に更新するか、一部のデータを削除してストレージ領域を解放します。 新しいデータを挿入できるようになるまでに、最大で 15 分の遅延が生じる可能性があります。

後からサイズ、エディション、またはサービス目標の値を変更するには、[ALTER DATABASE - Azure SQL Database](../../t-sql/statements/alter-database-transact-sql.md?view=azuresqldb-current&preserve-view=true) を使用します。

`CATALOG_COLLATION` 引数はデータベースの作成中にのみ使用できます。

## <a name="database-copies"></a>データベース コピー

**適用対象:** 単一のデータベースおよびプールされたデータベースのみ。

`CREATE DATABASE` ステートメントを使用したデータベースのコピーは、非同期操作です。 したがって、コピー プロセスが完了するまで [!INCLUDE[ssSDS](../../includes/sssds-md.md)] サーバーに接続している必要はありません。 `CREATE DATABASE` ステートメントは、sys.databases へのエントリが作成された後、データベース コピー操作が完了する前に、制御をユーザーに戻します。 つまり、`CREATE DATABASE` ステートメントは、データベース コピーがまだ進行しているときに正常に復帰します。

- [!INCLUDE[ssSDS_md](../../includes/sssds-md.md)] サーバーでのコピー プロセスの監視:[dm_database_copies](../../relational-databases/system-dynamic-management-views/sys-dm-database-copies-azure-sql-database.md) の `percentage_complete` または `replication_state_desc` 列、もしくは **sys.databases** ビューの `state` 列に対するクエリを実行します。 [sys.dm_operation_status](../../relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database.md) ビューを使用できるだけでなく、このビューからデータベース コピーを含むデータベース操作の状態も返されます。

コピー プロセスが正常に完了した時点で、ソース データベースに対するトランザクションが対象データベースに反映されています。

`AS COPY OF` 引数を使用する際、次の構文および意味上の規則が適用されます。

- コピー先のサーバー名としてコピー元のサーバー名と同じ名前を使用することも別の名前を使用することもできます。 名前が同じである場合、このパラメーターは省略可能であり、現在のセッションのサーバー コンテキストが既定で使用されます。
- ソース データベースと対象データベースの名前を指定する必要があります。これらの名前は、一意であり、識別子に関する [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の規則に準拠している必要があります。 詳細については、「[データベース識別子](../../relational-databases/databases/database-identifiers.md)」を参照してください。
- `CREATE DATABASE` ステートメントは、新しいデータベースが作成される [!INCLUDE[ssSDS](../../includes/sssds-md.md)] サーバーの master データベースのコンテキスト内で実行される必要があります。
- コピーの完了後、対象データベースは個別のデータベースとして管理する必要があります。 `ALTER DATABASE` ステートメントと `DROP DATABASE` ステートメントは、ソース データベースに影響を与えることなく、新しいデータベースに対して実行できます。 新しいデータベースを別の新しいデータベースにコピーすることもできます。
- データベース コピーが進行中でも、ソース データベースには引き続きアクセスできます。

詳細については、[Transact-SQL を使った Azure SQL Database のコピーの作成](/azure/azure-sql/database/database-copy)に関するページを参照してください。

> [!IMPORTANT]
> 既定では、データベース コピーのバックアップ ストレージ冗長性は、そのソース データベースと同じ冗長性で作成されます。 データベース コピーの作成時のバックアップ ストレージの冗長性の変更は、T-SQL によってサポートされていません。 


## <a name="permissions"></a>アクセス許可

データベースを作成するには、次のいずれかでログインする必要があります。

- サーバー レベル プリンシパル ログイン
- ローカルの Azure SQL Server の Azure AD 管理者
- `dbmanager` データベース ロールのメンバーであるログイン

**`CREATE DATABASE ... AS COPY OF` 構文を使用するための追加要件:** ローカル サーバー上でステートメントを実行しているログインは少なくともソース サーバー上の `db_owner` である必要があります。 ログインが [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 認証に基づいている場合、ローカル サーバー上でステートメントを実行しているログインが、ソース [!INCLUDE[ssSDS](../../includes/sssds-md.md)] サーバー上にも同じ名前とパスワードで含まれている必要があります。

## <a name="examples"></a>例

### <a name="simple-example"></a>簡単な例

データベースを作成するための簡単な例です。

```sql
CREATE DATABASE TestDB1;
```

### <a name="simple-example-with-edition"></a>エディションでの簡単な例

汎用データベースを作成するための簡単な例です。

```sql
CREATE DATABASE TestDB2
( EDITION = 'GeneralPurpose' );
```

### <a name="example-with-additional-options"></a>追加のオプションの使用例

複数のオプションを使用する例です。

```sql
CREATE DATABASE hito
COLLATE Japanese_Bushu_Kakusu_100_CS_AS_KS_WS
( MAXSIZE = 500 MB, EDITION = 'GeneralPurpose', SERVICE_OBJECTIVE = 'GP_GEN4_8' ) ;
```

### <a name="creating-a-copy"></a>コピーの作成

データベースのコピーを作成する例です。

**適用対象:** 単一のデータベースおよびプールされたデータベースのみ。

```sql
CREATE DATABASE escuela
AS COPY OF school;
```

### <a name="creating-a-database-in-an-elastic-pool"></a>エラスティック プール内でのデータベースの作成

S3M100 という名前のプール内に新しいデータベースを作成します。

**適用対象:** 単一のデータベースおよびプールされたデータベースのみ。

```sql
CREATE DATABASE db1 ( SERVICE_OBJECTIVE = ELASTIC_POOL ( name = S3M100 ) ) ;
```

### <a name="creating-a-copy-of-a-database-on-another-server"></a>別のサーバー上でのデータベースのコピーの作成

次の例では、単一データベースの P2 コンピューティング サイズ (サービス目標) で db_original database のコピーである named db_copy を作成します。 これは、db_original がエラスティック プール内にあるかどうかや、単一データベースのコンピューティング サイズ (サービス目標) に関係なく当てはまります。

**適用対象:** 単一のデータベースおよびプールされたデータベースのみ。

```sql
CREATE DATABASE db_copy
  AS COPY OF ozabzw7545.db_original ( SERVICE_OBJECTIVE = 'P2' );
```

次の例では、ep1 という名前のエラスティック プールに db_original database のコピーである named db_copy を作成します。 これは、db_original がエラスティック プール内にあるかどうかや、単一データベースのコンピューティング サイズ (サービス目標) に関係なく当てはまります。 db_original が異なる名前のエラスティック プールにある場合、db_copy は引き続き ep1 に作成されます。

**適用対象:** 単一のデータベースおよびプールされたデータベースのみ。

```sql
CREATE DATABASE db_copy
  AS COPY OF ozabzw7545.db_original
  (SERVICE_OBJECTIVE = ELASTIC_POOL( name = ep1 ) ) ;
```

### <a name="create-database-with-specified-catalog-collation-value"></a>指定したカタログ照合順序の値のデータベースを作成する

次の例では、データベース作成中のカタログ照合順序を DATABASE_DEFAULT に設定します。これにより、カタログ照合順序がデータベースの照合順序と同じに設定されます。

```sql
CREATE DATABASE TestDB3 COLLATE Japanese_XJIS_140 (MAXSIZE = 100 MB, EDITION = 'Basic')
  WITH CATALOG_COLLATION = DATABASE_DEFAULT
```

### <a name="create-database-using-zone-redundancy-for-backups"></a>バックアップにゾーンの冗長性を使用したデータベースの作成

次の例では、データベース バックアップのゾーン冗長が設定されます。 ポイントインタイム リストア バックアップおよび (構成されている場合) 長期保有バックアップのいずれも、同じバックアップ ストレージの冗長性が使用されます。

```sql
CREATE DATABASE test_zone_redundancy 
  WITH BACKUP_STORAGE_REDUNDANCY = 'ZONE';
```

## <a name="see-also"></a>関連項目

- [sys.dm_database_copies - Azure SQL Database](../../relational-databases/system-dynamic-management-views/sys-dm-database-copies-azure-sql-database.md)
- [ALTER DATABASE - Azure SQL Database](alter-database-transact-sql.md?view=azuresqldb-current&preserve-view=true)

::: moniker-end
::: moniker range="=azuresqldb-mi-current"

:::row:::
    :::column:::
        [SQL Server](create-database-transact-sql.md?view=sql-server-ver15&preserve-view=true)
    :::column-end:::
    :::column:::
        [SQL Database](create-database-transact-sql.md?view=azuresqldb-current&preserve-view=true)
    :::column-end:::
    :::column:::
        **_\* SQL Managed Instance \*_**
    :::column-end:::
    :::column:::
        [Azure Synapse<br />Analytics](create-database-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)
    :::column-end:::
    :::column:::
        [Analytics Platform<br />System (PDW)](create-database-transact-sql.md?view=aps-pdw-2016&preserve-view=true)
    :::column-end:::
:::row-end:::

&nbsp;

## <a name="azure-sql-managed-instance"></a>Azure SQL Managed Instance

## <a name="overview"></a>概要

Azure SQL Managed Instance では、データベースを作成するためにこのステートメントを使用します。 マネージド インスタンス上にデータベースを作成するときは、データベース名と照合順序を指定します。

## <a name="syntax"></a>構文

```syntaxsql
CREATE DATABASE database_name [ COLLATE collation_name ]
[;]
```

> [!IMPORTANT]
> マネージ インスタンスでファイルを追加するかデータベースの包含を設定するには、[ALTER DATABASE](alter-database-transact-sql.md?tabs=sqldbmi) ステートメントを使います。

## <a name="arguments"></a>引数

*database_name*: 新しいデータベースの名前です。 この名前は、SQL Server で一意であり、識別子に関する [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の規則に準拠している必要があります。 詳細については、「[データベース識別子](../../relational-databases/databases/database-identifiers.md)」を参照してください。

*Collation_name*: データベースの既定の照合順序を指定します。 照合順序名には、Windows 照合順序名または SQL 照合順序名を指定できます。 指定しない場合、既定の照合順序である SQL_Latin1_General_CP1_CI_AS がデータベースに割り当てられます。

Windows と SQL の照合順序名の詳細については、[COLLATE (Transact-SQL)](../../t-sql/statements/collations.md) に関するページを参照してください。

## <a name="remarks"></a>解説

[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 内のデータベースについては、データベースの作成時に設定される既定の設定がいくつかあります。 これらの既定の設定の詳細については、「[DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md)」の値の一覧を参照してください。

> [!IMPORTANT]
> `CREATE DATABASE` ステートメントが [!INCLUDE[tsql](../../includes/tsql-md.md)] バッチ内の唯一のステートメントである必要があります。

`CREATE DATABASE` には、次の制限があります。

- ファイルおよびファイル グループを定義することはできません。
- `WITH` オプションはサポートされていません。

  > [!TIP]
  > 回避策としては、[ALTER DATABASE](../../t-sql/statements/alter-database-transact-sql.md?view=azuresqldb-mi-current&preserve-view=true) を使い、 `CREATE DATABASE` の後でデータベース オプションを設定したり、ファイルを追加したりします。

## <a name="permissions"></a>アクセス許可

データベースを作成するには、次のいずれかでログインする必要があります。

- サーバー レベル プリンシパル ログイン
- ローカルの Azure SQL Server の Azure AD 管理者
- `dbcreator` データベース ロールのメンバーであるログイン

## <a name="examples"></a>例

### <a name="simple-example"></a>簡単な例

データベースを作成するための簡単な例です。

```sql
CREATE DATABASE TestDB1;
```

## <a name="see-also"></a>関連項目

「[ALTER DATABASE](alter-database-transact-sql.md?view=azuresqldb-mi-current&preserve-view=true)」をご覧ください。

::: moniker-end
::: moniker range="=azure-sqldw-latest"

:::row:::
    :::column:::
        [SQL Server](create-database-transact-sql.md?view=sql-server-ver15&preserve-view=true)
    :::column-end:::
    :::column:::
        [SQL Database](create-database-transact-sql.md?view=azuresqldb-current&preserve-view=true)
    :::column-end:::
    :::column:::
        [SQL Managed Instance](create-database-transact-sql.md?view=azuresqldb-mi-current&preserve-view=true)
    :::column-end:::
    :::column:::
        **_\* Azure Synapse<br />Analytics \*_**
    :::column-end:::
    :::column:::
        [Analytics Platform<br />System (PDW)](create-database-transact-sql.md?view=aps-pdw-2016&preserve-view=true)
    :::column-end:::
:::row-end:::

&nbsp;

## <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

## <a name="overview"></a>概要

Azure Synapse では、Azure SQL Database サーバーでこのステートメントを使用し、SQL Analytics データベースを作成できます。 このステートメントでは、データベース名、照合順序、最大サイズ、エディション、およびサービス目標を指定します。

## <a name="syntax"></a>構文

### <a name="dedicated-sql-pool"></a>[専用 SQL プール](#tab/sqlpool)
```syntaxsql
CREATE DATABASE database_name [ COLLATE collation_name ]
(
    [ MAXSIZE = {
          250 | 500 | 750 | 1024 | 5120 | 10240 | 20480 | 30720
        | 40960 | 51200 | 61440 | 71680 | 81920 | 92160 | 102400
        | 153600 | 204800 | 245760
      } GB ,
    ]
    EDITION = 'datawarehouse',
    SERVICE_OBJECTIVE = {
         'DW100' | 'DW200' | 'DW300' | 'DW400' | 'DW500' | 'DW600'
        | 'DW1000' | 'DW1200' | 'DW1500' | 'DW2000' | 'DW3000' | 'DW6000'
        |'DW100c' | 'DW200c' | 'DW300c' | 'DW400c' | 'DW500c'
        | 'DW1000c' | 'DW1500c' | 'DW2000c' | 'DW2500c' | 'DW3000c' | 'DW5000c'
        | 'DW6000c' | 'DW7500c' | 'DW10000c' | 'DW15000c' | 'DW30000c'
    }
)
[;]
```
### <a name="serverless-sql-pool"></a>[サーバーレス SQL プール](#tab/sqlod)
```syntaxsql
CREATE DATABASE database_name [ COLLATE collation_name ]
[;] 
```
---

## <a name="arguments"></a>引数

*database_name*: 新しいデータベースの名前です。 この名前は、[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] データベースと [!INCLUDE[ssSDW](../../includes/sssdw-md.md)] データベースの両方をホストでき、ID の [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の規則に従う、SQL Server に固有のものである必要があります。 詳細については、「[データベース識別子](../../relational-databases/databases/database-identifiers.md)」を参照してください。

*collation_name*: データベースの既定の照合順序を指定します。 照合順序名には、Windows 照合順序名または SQL 照合順序名を指定できます。 指定しない場合、既定の照合順序である SQL_Latin1_General_CP1_CI_AS がデータベースに割り当てられます。

Windows と SQL の照合順序名の詳細については、[COLLATE (Transact-SQL)](./collations.md) に関するページを参照してください。

*EDITION*: データベースのサービス層を指定します。 [!INCLUDE[ssSDW](../../includes/sssdw-md.md)] の場合は、"datawarehouse" を使用します。

*MAXSIZE*: 既定値は 245,760 GB (240 TB) です。

**適用対象:** Gen1 コンピューティングに最適化

データベースの最大許容サイズ。 データベースは MAXSIZE を超えることはできません。

**適用対象:** Gen2 コンピューティングに最適化

データベースの行ストア データの最大許容サイズ。 行ストア テーブル、列ストア インデックスのデルタストア、またはクラスター化列ストア インデックス上の非クラスター化インデックスに格納されたデータは、MAXSIZE を超えて大きくなることはできません。列ストア形式に圧縮されたデータは、サイズ制限がなく、MAXSIZE に制約されません。

SERVICE_OBJECTIVE: コンピューティング サイズ (サービス目標) を指定します。 Azure Synapse のサービス目標の詳細については、「[Data Warehouse ユニット (DWU)](/azure/sql-data-warehouse/what-is-a-data-warehouse-unit-dwu-cdwu)」を参照してください。

## <a name="general-remarks"></a>全般的な解説

データベースのプロパティを参照するには、[DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md) を使用します。

後で最大サイズ、またはサービス目標の値を変更するには、[ALTER DATABASE - Azure Synapse Analytics](../../t-sql/statements/alter-database-transact-sql.md?view=aps-pdw-2016-au7&preserve-view=true) を使用します。

Azure Synapse は COMPATIBILITY_LEVEL 130 に設定されており、変更することはできません。 詳細については、「[ALTER DATABASE (TRANSACT-SQL) の互換性レベル](./alter-database-transact-sql-compatibility-level.md)」を参照してください。

## <a name="permissions"></a>アクセス許可

必要なアクセスを許可:

- プロビジョニング プロセスによって作成されたサーバー レベル プリンシパル ログイン、または
- `dbmanager` データベース ロールのメンバー。

## <a name="error-handling"></a>エラー処理

データベースのサイズが MAXSIZE に達するとエラー コード 40544 が表示されます。 この場合、データを挿入、更新したり、新しいオブジェクト (テーブル、ストアド プロシージャ、ビュー、関数など) を作成したりすることはできません。 ただし、データの読み取りと削除、テーブルの切り捨て、テーブルとインデックスの削除、およびインデックスの再構築は引き続き可能です。 これを解決するには、MAXSIZE を現在のデータベースのサイズより大きい値にするか、一部のデータを削除してストレージ領域を解放します。 新しいデータを挿入できるようになるまでに、最大で 15 分の遅延が生じる可能性があります。

## <a name="limitations-and-restrictions"></a>制限事項と制約事項

新しいデータベースを作成するには、master データベースに接続している必要があります。

`CREATE DATABASE` ステートメントが [!INCLUDE[tsql](../../includes/tsql-md.md)] バッチ内の唯一のステートメントである必要があります。

データベースを作成した後、データベースの照合順序を変更することはできません。

## <a name="examples-sssdwfull"></a>例: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)]

### <a name="a-simple-example"></a>A. 簡単な例
データ ウェアハウスのデータベースを作成するための簡単な例です。 これにより、10240 GB、既定の照合順序は SQL_Latin1_General_CP1_CI_AS、最小のコンピューティング能力が DW100 の、最小の最大サイズのデータベースが作成されます。

```sql
CREATE DATABASE TestDW
(EDITION = 'datawarehouse', SERVICE_OBJECTIVE='DW100');
```

### <a name="b-create-a-data-warehouse-database-with-all-the-options"></a>B. すべてのオプションを使用して、データ ウェアハウスのデータベースを作成
すべてのオプションを使用して 10 テラバイトのデータ ウェアハウスを作成する例。

```sql
CREATE DATABASE TestDW COLLATE Latin1_General_100_CI_AS_KS_WS
(MAXSIZE = 10240 GB, EDITION = 'datawarehouse', SERVICE_OBJECTIVE = 'DW1000');
```

## <a name="see-also"></a>参照

- [ALTER DATABASE- Azure Synapse Analytics](../../t-sql/statements/alter-database-transact-sql.md?view=aps-pdw-2016-au7&preserve-view=true)
- [CREATE TABLE- Azure Synapse Analytics](../../t-sql/statements/create-table-azure-sql-data-warehouse.md)
- [DROP DATABASE - Transact-SQL](../../t-sql/statements/drop-database-transact-sql.md)

::: moniker-end
::: moniker range=">=aps-pdw-2016"

:::row:::
    :::column:::
        [SQL Server](create-database-transact-sql.md?view=sql-server-ver15&preserve-view=true)
    :::column-end:::
    :::column:::
        [SQL Database](create-database-transact-sql.md?view=azuresqldb-current&preserve-view=true)
    :::column-end:::
    :::column:::
        [SQL Managed Instance](create-database-transact-sql.md?view=azuresqldb-mi-current&preserve-view=true)
    :::column-end:::
    :::column:::
        [Azure Synapse<br />Analytics](create-database-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)
    :::column-end:::
    :::column:::
        **_\* Analytics Platform<br />System (PDW) \*_**
    :::column-end:::
:::row-end:::

&nbsp;

## <a name="analytics-platform-system"></a>分析プラットフォーム システム

## <a name="overview"></a>概要

Analytics Platform System の場合、このステートメントは、Analytics Platform System アプライアンス上で新しいデータベースを作成するために使用されます。 このステートメントを使用し、アプライアンス データベースに関連付けられているすべてのファイルを作成し、データベース テーブルとトランザクション ログの最大サイズと自動増加オプションを設定します。

## <a name="syntax"></a>構文

```syntaxsql
CREATE DATABASE database_name
WITH (
    [ AUTOGROW = ON | OFF , ]
    REPLICATED_SIZE = replicated_size [ GB ] ,
    DISTRIBUTED_SIZE = distributed_size [ GB ] ,
    LOG_SIZE = log_size [ GB ] )
[;]
```

## <a name="arguments"></a>引数

*database_name*: 新しいデータベースの名前です。 許容されるデータベース名の詳細については、[!INCLUDE[pdw-product-documentation](../../includes/pdw-product-documentation-md.md)]の "オブジェクトの名前付け規則" と "予約済みデータベース名" を参照してください。

AUTOGROW = ON | **OFF** このデータベースの *replicated_size* *distributed_size*、および *log_size* パラメーターが、必要な場合に指定のサイズを超えて自動的に増加するかどうかを指定します。 既定値は **OFF** です。

AUTOGROW が ON の場合、*replicated_size*、*distributed_size*、*log_size* は、割り当て以上の記憶域を必要とするデータの挿入、更新、その他のアクションごとに、必要に応じて増加します (最初に指定したサイズのブロックではなく)。

AUTOGROW が OFF の場合、サイズは自動増加しません。 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] は、*replicated_size*、*distributed_size*、*log_size* がその指定値を超えて増加することが要求されるアクションを試行するとエラーを返します。

AUTOGROW はすべてのサイズに対して ON にするか、すべてのサイズに対して OFF にします。 たとえば、*log_size* に対して AUTOGROW を ON に設定し、*replicated_size* に対しては ON に設定しないということはできません。

*replicated_size* [ GB ]: 正の数です。 *計算ノードごとに*、複製テーブルと対応データに割り当てる合計領域のサイズを設定します (整数または 10 進数の GB)。 *replicated_size* の最小要件と最大要件については、[!INCLUDE[pdw-product-documentation](../../includes/pdw-product-documentation-md.md)] の "最小値と最大値" を参照してください。

AUTOGROW が ON の場合、複製テーブルはこの上限を超えて増加できます。

AUTOGROW が OFF の場合、ユーザーが新しい複製テーブルを作成しようとしたり、既存の複製テーブルにデータを挿入しようとしたり、サイズが *replicated_size* を超えるのに既存の複製テーブルを更新しようとするとエラーが返されます。

*distributed_size* [ GB ]: 正の数です。 *アプライアンス全体で* 分散テーブル (と対応データ) に割り当てる合計領域のサイズ (整数または 10 進数の GB)。 *distributed_size* の最小要件と最大要件については、[!INCLUDE[pdw-product-documentation](../../includes/pdw-product-documentation-md.md)] の "最小値と最大値" を参照してください。

AUTOGROW が ON の場合、分散テーブルはこの上限を超えて増加できます。

AUTOGROW が OFF の場合、ユーザーが新しい分散テーブルを作成しようとしたり、既存の分散テーブルにデータを挿入しようとしたり、サイズが *distributed_size* を超えるのに既存の分散テーブルを更新しようとするとエラーが返されます。

*log_size* [ GB ]: 正の数です。 *アプライアンス全体* のトランザクション ログのサイズ (整数または 10 進数の GB)。

*log_size* の最小要件と最大要件については、[!INCLUDE[pdw-product-documentation](../../includes/pdw-product-documentation-md.md)] の "最小値と最大値" を参照してください。

AUTOGROW が ON の場合、ログ ファイルはこの上限を超えて増加できます。 ログ ファイルのサイズを元のサイズまで減らすには、[DBCC SHRINKLOG (Azure Synapse Analytics)](../../t-sql/database-console-commands/dbcc-shrinklog-azure-sql-data-warehouse.md) ステートメントを使用します。

AUTOGROW が OFF の場合、個々の計算ノードで、ログ サイズが *log_size* を超えて増加するようなアクションが行われると、ユーザーにエラーが返されます。

## <a name="permissions"></a>アクセス許可

master データベース内の `CREATE ANY DATABASE` アクセス許可、または **sysadmin** 固定サーバー ロールでのメンバーシップが必要です。

次の例は、データベース ユーザー Fay にデータベースを作成する権限を与えます。

```sql
USE master;
GO
GRANT CREATE ANY DATABASE TO [Fay];
GO
```

## <a name="general-remarks"></a>全般的な解説

データベースはデータベース互換性レベル 120 で作成されます。これは [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] の互換性レベルです。 この互換性によって、PDW で使用されるすべての [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 機能をデータベースで使用できます。

## <a name="limitations-and-restrictions"></a>制限事項と制約事項

CREATE DATABASE ステートメントは、明示的なトランザクションでは使用できません。 詳細については、「[ステートメント](../../t-sql/statements/statements.md)」を参照してください。

データベースの制約の最小値と最大値については、[!INCLUDE[pdw-product-documentation](../../includes/pdw-product-documentation-md.md)] の "最小値と最大値" を参照してください。

データベースを作成するときは、次のサイズの組み合わせ合計を割り当てるために、*計算ノードごとに* 十分な空き領域が必要になります。

- テーブルのサイズが *replicated_table_size* の [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] データベース。
- テーブルのサイズが (*distributed_table_size* / 計算ノードの数) の [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] データベース。
- ログのサイズが (*log_size* / 計算ノードの数) の [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]。

## <a name="locking"></a>ロック

DATABASE オブジェクトを共有ロックします。

## <a name="metadata"></a>Metadata

この操作に成功すると、このデータベースのエントリがメタデータ ビューの [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) および [sys.objects](../../relational-databases/system-catalog-views/sys-objects-transact-sql.md) に表示されます。

## <a name="examples-sspdw"></a>例: [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]

### <a name="a-basic-database-creation-examples"></a>A. 基本的データベース作成の例

次の例では、データベース `mytest` を作成します。複製テーブルの記憶域割り当ては計算ノードごとに 100 GB、分散テーブルの記憶域割り当てはアプライアンスごとに 500 GB、トランザクション ログの記憶域割り当てはアプライアンスごとに 100 GB です。 この例では、AUTOGROW は既定の OFF です。

```sql
CREATE DATABASE mytest
  WITH
    (REPLICATED_SIZE = 100 GB,
    DISTRIBUTED_SIZE = 500 GB,
    LOG_SIZE = 100 GB );
```

次の例では上の例と同じパラメーターでデータベース `mytest` を作成しますが、AUTOGROW を ON にします。 指定サイズ パラメーターを超えてデータベースは増加できます。

```sql
CREATE DATABASE mytest
  WITH
    (AUTOGROW = ON,
    REPLICATED_SIZE = 100 GB,
    DISTRIBUTED_SIZE = 500 GB,
    LOG_SIZE = 100 GB);
```

### <a name="b-creating-a-database-with-partial-gigabyte-sizes"></a>B. 小数点以下を含む GB サイズでデータベースを作成する

次の例では、データベース `mytest` を作成します。AUTOGROW は OFF です。複製テーブルの記憶域割り当ては計算ノードごとに 1.5 GB、分散テーブルの記憶域割り当てはアプライアンスごとに 5.25 GB、トランザクション ログの記憶域割り当てはアプライアンスごとに 10 GB です。

```sql
CREATE DATABASE mytest
  WITH
    (REPLICATED_SIZE = 1.5 GB,
    DISTRIBUTED_SIZE = 5.25 GB,
    LOG_SIZE = 10 GB);
```

## <a name="see-also"></a>参照

- [ALTER DATABASE - Analytics Platform System](../../t-sql/statements/alter-database-transact-sql.md?view=aps-pdw-2016-au7&preserve-view=true)
- [DROP DATABASE](../../t-sql/statements/drop-database-transact-sql.md)

::: moniker-end