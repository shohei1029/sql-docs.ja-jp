---
description: CREATE MASTER KEY (Transact-SQL)
title: CREATE MASTER KEY (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 09/12/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- CREATE_MASTER_KEY_TSQL
- MASTER_KEY_TSQL
- MASTER KEY
- CREATE MASTER KEY
dev_langs:
- TSQL
helpviewer_keywords:
- encryption [SQL Server], Database Master Key
- database master key [SQL Server]
- CREATE MASTER KEY statement
- cryptography [SQL Server], Database Master Key
- database master key [SQL Server], creating
ms.assetid: 1710a305-1a4f-48ec-836c-11ffd0356d76
author: VanMSFT
ms.author: vanto
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: b327dd053659378674400329661e9a1ee7410bcd
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104748206"
---
# <a name="create-master-key-transact-sql"></a>CREATE MASTER KEY (Transact-SQL)

[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

master データベースで、データベース マスター キーを作成します。

![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)

## <a name="syntax"></a>構文

```syntaxsql
CREATE MASTER KEY [ ENCRYPTION BY PASSWORD ='password' ]
[ ; ]
```

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数

PASSWORD ='*password*' は、データベース内のマスター キーの暗号化に使用されているパスワードです。 *password* は、Windows のパスワード ポリシーが [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスを実行するコンピューターに要求する条件を満足する必要があります。 *password* は、[!INCLUDE[ssSDS](../../includes/sssds-md.md)] および [!INCLUDE[ssSDW_md](../../includes/sssdw-md.md)] では省略可能です。

## <a name="remarks"></a>注釈

データベース マスター キーは対称キーで、証明書の秘密キーやデータベース内に存在する非対称キーを保護するときに使用します。 データベース マスター キーを作成するときには、AES_256 アルゴリズムとユーザー指定のパスワードを使用してマスター キーを暗号化します。 [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] および [!INCLUDE[ssKilimanjaro](../../includes/sskilimanjaro-md.md)] では、トリプル DES アルゴリズムを使用します。 マスター キーの暗号化を自動的に解除できるようにするには、サービス マスター キーを使用してキーのコピーを暗号化し、データベースと master の両方に格納します。 通常、master に格納されたコピーは、マスター キーが変更されるたびに暗黙的に更新されます。 この既定の設定は、[ALTER MASTER KEY](../../t-sql/statements/alter-master-key-transact-sql.md) の DROP ENCRYPTION BY SERVICE MASTER KEY オプションを使って変更できます。 サービス マスター キーによって暗号化されていないマスター キーは、[OPEN MASTER KEY](../../t-sql/statements/open-master-key-transact-sql.md) ステートメントとパスワードを使って開かれている必要があります。

master 内の sys.databases カタログ ビューの is_master_key_encrypted_by_server 列には、データベース マスター キーがサービス マスター キーによって暗号化されているかどうかが示されます。

データベース マスター キーに関する情報は、sys.symmetric_keys カタログ ビューで確認できます。

SQL Server および Parallel Data Warehouse では、通常、マスター キーはサービス マスター キーと少なくとも 1 つのパスワードによって保護されています。 データベースが異なるサーバーに物理的に移動される場合 (ログ配布、バックアップの復元など)、データベースには、元のサーバーのサービス マスター キーによって暗号化されたマスター キーのコピーと (この暗号化が `ALTER MASTER KEY DDL` を使って明示的に削除されていない場合)、`CREATE MASTER KEY` または後続の `ALTER MASTER KEY DDL` 操作の間に指定された各パスワードによって暗号化されたそのコピーが含まれます。 データベースを移動した後、マスター キーと、マスター キーを使って暗号化されたすべてのデータを、キー階層のルートとして復旧するには、新しいサーバーで、マスター キーの保護に使われているパスワードの 1 つを指定して `OPEN MASTER KEY` ステートメントを使うか、マスター キーのバックアップを復元するか、または元のサービス マスター キーのバックアップを復元します。

[!INCLUDE[ssSDS](../../includes/sssds-md.md)] および [!INCLUDE[ssSDW_md](../../includes/sssdw-md.md)] の場合、マスター キーでのサービス マスター キーの保護は Microsoft Azure プラットフォームによって管理されるので、パスワード保護は、データベースがサーバー間で移動される可能性がある場合のデータ損失シナリオを防ぐための安全なメカニズムとは見なされません。 したがって、マスター キーのパスワードは、[!INCLUDE[ssSDS](../../includes/sssds-md.md)] および [!INCLUDE[ssSDW_md](../../includes/sssdw-md.md)] では省略可能です。

> [!IMPORTANT]
> マスター キーは [BACKUP MASTER KEY](../../t-sql/statements/backup-master-key-transact-sql.md) を使用してバックアップし、安全な別の場所に保存する必要があります。

サービス マスター キーとデータベース マスター キーは、AES-256 アルゴリズムを使用して保護されます。

## <a name="permissions"></a>アクセス許可

データベースに対する CONTROL 権限が必要です。

## <a name="examples"></a>例

`master` データベースにデータベース マスター キーを作成するには、次の例を使用します。 このキーは、パスワード `23987hxJ#KL95234nl0zBe` を使用して暗号化されます。

```sql
CREATE MASTER KEY ENCRYPTION BY PASSWORD = '23987hxJ#KL95234nl0zBe';
GO
```

## <a name="see-also"></a>関連項目

- [sys.symmetric_keys &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-symmetric-keys-transact-sql.md)
- [sys.databases &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md)
- [OPEN MASTER KEY &#40;Transact-SQL&#41;](../../t-sql/statements/open-master-key-transact-sql.md)
- [ALTER MASTER KEY &#40;Transact-SQL&#41;](../../t-sql/statements/alter-master-key-transact-sql.md)
- [DROP MASTER KEY &#40;Transact-SQL&#41;](../../t-sql/statements/drop-master-key-transact-sql.md)
- [CLOSE MASTER KEY &#40;Transact-SQL&#41;](../../t-sql/statements/close-master-key-transact-sql.md)
- [暗号化階層](../../relational-databases/security/encryption/encryption-hierarchy.md)
