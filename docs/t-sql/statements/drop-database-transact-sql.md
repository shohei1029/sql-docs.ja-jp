---
description: DROP DATABASE (Transact-SQL)
title: DROP DATABASE (Transact-SQL)
ms.custom: ''
ms.date: 02/21/2019
ms.prod: sql
ms.prod_service: synapse-analytics, database-engine, pdw, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- DROP DATABASE
- DROP_DATABASE_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- snapshots [SQL Server database snapshots], deleting
- removing databases
- database snapshots [SQL Server], removing
- deleting databases
- dropping databases
- databases [SQL Server], dropping
- DROP DATABASE statement
- database removal [SQL Server], DROP DATABASE statement
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 3c15b6aaf3244672232e4e6cdd4551564d11d9e2
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104750472"
---
# <a name="drop-database-transact-sql"></a>DROP DATABASE (Transact-SQL)

[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

1 つ以上のユーザー データベースまたはデータベース スナップショットを [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスから削除します。

![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)

## <a name="syntax"></a>構文

```syntaxsql
-- SQL Server Syntax
DROP DATABASE [ IF EXISTS ] { database_name | database_snapshot_name } [ ,...n ] [;]
```

```syntaxsql
-- Azure SQL Database, Azure Synapse Analytics and Analytics Platform System Syntax
DROP DATABASE database_name [;]
```

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数

 *IF EXISTS*  
**適用対象**: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] から [現在のバージョン](/troubleshoot/sql/general/determine-version-edition-update-level)まで)。

条件付きでは既に存在する場合にのみ、データベースを削除します。

*database_name* 削除するデータベースの名前を指定します。 データベースの一覧を表示するには、[sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) カタログ ビューを使用します。

*database_snapshot_name*
**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。

削除するデータベース スナップショットの名前を指定します。

## <a name="general-remarks"></a>全般的な解説

データベースは、オフライン、読み取り専用、未確認などの状態に関係なくドロップすることができます。 データベースの現在の状態を表示するには、**sys.databases** カタログ ビューを使用します。

削除されたデータベースは、バックアップを復元することによってのみ、再作成できます。 データベース スナップショットはバックアップできません。したがって復元もできません。

データベースを削除する場合は、[master データベース](../../relational-databases/databases/master-database.md)をバックアップする必要があります。

データベースを削除すると、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスからそのデータベースが削除され、そのデータベースで使用されている物理ディスク ファイルも削除されます。 削除の際にデータベースまたはディスク ファイルのいずれかがオフラインの場合、ディスク ファイルは削除されません。 これらのファイルは Windows エクスプローラーを使用して手動で削除できます。 ファイルをファイル システムから削除せずにデータベースを現在のサーバーから削除するには、[sp_detach_db](../../relational-databases/system-stored-procedures/sp-detach-db-transact-sql.md) を使用します。

> [!WARNING]
> FILE_SNAPSHOT を持つデータベースを削除すると、関連付けられているバックアップは成功しますが、スナップショットが関連付けられているデータベース ファイルは、これらのデータベース ファイルを参照するバックアップの無効化を回避するためには削除されません。 ファイルは切り捨てられますが、FILE_SNAPSHOT のバックアップをそのままの状態に保つために物理的には削除されません。 詳細については、「[Windows Azure BLOB ストレージ サービスを使用した SQL Server のバックアップと復元](../../relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service.md)」を参照してください。 **適用対象**: [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] から [現在のバージョン](/troubleshoot/sql/general/determine-version-edition-update-level)まで。

### [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]

データベース スナップショットを削除すると、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスからデータベース スナップショットが削除され、スナップショットで使用されている物理的な NTFS ファイル システムのスパース ファイルが削除されます。 データベース スナップショットによるスパース ファイルの使用の詳細については、[データベース スナップショット](../../relational-databases/databases/database-snapshots-sql-server.md)に関するページを参照してください。 データベース スナップショットを削除すると、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスのプラン キャッシュが消去されます。 プラン キャッシュが消去されると、後続のすべての実行プランが再コンパイルされ、場合によっては、クエリ パフォーマンスが一時的に急激に低下します。 プラン キャッシュ内のキャッシュストアが消去されるたびに、"[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] は、一部のデータベース メンテナンス操作または再構成操作により、'%s' キャッシュストア (プラン キャッシュの一部) のキャッシュストア フラッシュを %d 個検出しました" という情報メッセージが [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エラー ログに含まれます。 このメッセージは、5 分以内にキャッシュがフラッシュされる限り、5 分間隔でログに記録されます。

## <a name="interoperability"></a>相互運用性

### <a name="sql-server"></a>SQL Server

トランザクション レプリケーションに対してパブリッシュされたデータベース、あるいは、マージ レプリケーションに対してパブリッシュまたはサブスクライブされたデータベースを削除するには、まず、データベースからレプリケーションを削除する必要があります。 データベースが損傷している場合またはレプリケーションを最初に削除できない場合、あるいはその両方の場合であっても、ALTER DATABASE を使用してデータベースをオフラインに設定してから削除すると、ほとんどの場合、データベースを削除できます。

データベースがログ配布に関与している場合は、データベースを削除する前にログ配布を削除します。 詳しくは、「 [ログ配布について](../../database-engine/log-shipping/about-log-shipping-sql-server.md)」をご覧ください。

## <a name="limitations-and-restrictions"></a>制限事項と制約事項

[システム データベース](../../relational-databases/databases/system-databases.md)は削除できません。

DROP DATABASE ステートメントは、自動コミット モードで実行する必要があり、明示的または暗黙的なトランザクションでは許可されません。 自動コミット モードは、既定のトランザクション管理モードです。

使用中のデータベースは削除できません。 使用中のデータベースとは、1 人以上のユーザーが読み込みまたは書き込みのために開いているデータベースです。 データベースからユーザーを削除するには、ALTER DATABASE を使用して、データベースを SINGLE_USER に設定する方法があります。

> [!WARNING]
> これは、任意のスレッドによる最初の連続する接続で SINGLE_USER スレッドを受け取り、接続できなくなるため、失敗しない方法です。 SQL Server では、負荷時にデータベースを削除する組み込みの方法は提供されていません。

### [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]

データベースを削除する前に、そのデータベース上のすべてのデータベース スナップショットを削除する必要があります。

Stretch データベースのデータベースの有効化を削除しても、リモートのデータは削除されません。 リモートのデータを削除する場合は、手動で削除する必要があります。

### [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]

データベースを削除するには、master データベースに接続する必要があります。

 DROP DATABASE ステートメントは SQL バッチ内の唯一のステートメントである必要があります。また、一度に削除できるデータベースは 1 つだけです。

### [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)]

データベースを削除するには、master データベースに接続する必要があります。

DROP DATABASE ステートメントは SQL バッチ内の唯一のステートメントである必要があります。また、一度に削除できるデータベースは 1 つだけです。

## <a name="permissions"></a>アクセス許可

### [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]

データベースの **CONTROL** 権限、**ALTER ANY DATABASE** 権限、または **db_owner** 固定データベース ロールのメンバーシップが必要です。

### [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]

データベースを削除できるのは、(プロビジョニング プロセスによって作成される) サーバーレベル プリンシパルのログイン、または **dbmanager** データベース ロールのメンバーだけです。

### [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]

データベースの **CONTROL** 権限、**ALTER ANY DATABASE** 権限、または **db_owner** 固定データベース ロールのメンバーシップが必要です。

## <a name="examples"></a>例

### <a name="a-dropping-a-single-database"></a>A. 1 つのデータベースを削除する

次の例では、`Sales` データベースを削除します。

```sql
DROP DATABASE Sales;
```

### <a name="b-dropping-multiple-databases"></a>B. 複数のデータベースを削除する

**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。

次の例では、一覧表示された各データベースを削除します。

```sql
DROP DATABASE Sales, NewSales;
```

### <a name="c-dropping-a-database-snapshot"></a>C. データベース スナップショットを削除する

**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。

次の例では、`sales_snapshot0600` というデータベース スナップショットを、ソース データベースには影響を与えずに削除します。

```sql
DROP DATABASE sales_snapshot0600;
```

## <a name="see-also"></a>参照

- [ALTER DATABASE](../../t-sql/statements/alter-database-transact-sql.md)
- [CREATE DATABASE](../../t-sql/statements/create-database-transact-sql.md)
- [EVENTDATA](../../t-sql/functions/eventdata-transact-sql.md)
- [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md)