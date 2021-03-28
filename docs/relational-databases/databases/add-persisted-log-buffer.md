---
description: 永続化されたログ バッファーをデータベースに追加する
title: 永続化されたログ バッファーをデータベースに追加する
ms.custom: ''
ms.date: 10/30/2019
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: configuration
ms.topic: conceptual
helpviewer_keywords:
- PMEM
- persistent memory
- persisted log buffer
- add log file
- create log buffer
- remove log buffer
ms.assetid: 8ead516a-1334-4f40-84b2-509d0a8ffa45
author: briancarrig
ms.author: brcarrig
manager: amitban
ms.openlocfilehash: 59fdc25c91b1d471d3108988f93de03a5ecbdc7f
ms.sourcegitcommit: 17f05be5c08cf9a503a72b739da5ad8be15baea5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/25/2021
ms.locfileid: "105103769"
---
# <a name="add-persisted-log-buffer-to-a-database"></a>永続化されたログ バッファーをデータベースに追加する
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

このトピックでは、[!INCLUDE[tsql](../../includes/tsql-md.md)] を使用して、[!INCLUDE[sqlv15](../../includes/sssql19-md.md)] 内のデータベースに永続化されたログ バッファーを追加する方法について説明します。  
  
## <a name="permissions"></a>アクセス許可

データベースに対する ALTER 権限が必要です。  

## <a name="configure-persistent-memory-device-linux"></a>永続メモリ デバイスを構成する (Linux)

Linux で永続メモリ デバイスを構成するには、[こちら](../../linux/sql-server-linux-configure-pmem.md)を参照してください。

## <a name="configure-persistent-memory-device-windows"></a>永続メモリ デバイスを構成する (Windows)

Windows で永続メモリ デバイスを構成するには、[こちら](/windows-server/storage/storage-spaces/deploy-pmem/)を参照してください。
  
## <a name="add-a-persisted-log-buffer-to-a-database"></a>永続化されたログ バッファーをデータベースに追加する  

次の例では、永続化されたログ バッファーを追加します。

```sql
ALTER DATABASE <MyDB> 
  ADD LOG FILE 
  (
    NAME = <DAXlog>, 
    FILENAME = '<Filepath to DAX Log File>', 
    SIZE = 20MB
  );
```

DAX ボリューム上のログ ファイルのサイズは、ADD FILE コマンドで指定されたサイズに関係なく 20 MB になることに注意してください。

新しいログ ファイルを配置するボリュームまたはマウントは、DAX でフォーマットされている (NTFS) か、DAX オプションを使用してマウントされている (XFS/EXT4) 必要があります。

## <a name="remove-a-persisted-log-buffer"></a>永続化されたログ バッファーを削除する

永続化されたログ バッファーを安全に削除するには、永続化されたログ バッファーをドレインするために、データベースをシングル ユーザー モードで配置する必要があります。

次の例では、永続化されたログ バッファーを削除します。

```sql
ALTER DATABASE <MyDB> SET SINGLE_USER;
ALTER DATABASE <MyDB> REMOVE FILE <DAXlog>;
ALTER DATABASE <MyDB> SET MULTI_USER;
```

## <a name="limitations"></a>制限事項

[Transparent Data Encryption (TDE)](../security/encryption/transparent-data-encryption.md) は、永続化されたログ バッファーと互換性がありません。

[可用性グループ](../../t-sql/statements/create-availability-group-transact-sql.md)は、プライマリ レプリカでは通常のログ書き込みセマンティクスが必要であるため、セカンダリ レプリカでのみこの機能を使用できます。 ただし、すべてのノード (理想的には、DAX ボリュームまたはマウント) に小さいログ ファイルを作成する必要があります。

## <a name="backup-and-restore-operations"></a>バックアップ操作と復元操作

通常の復元条件が適用されます。 永続化されたログ バッファーを DAX ボリュームまたはマウントに復元すると、バッファーは引き続き機能します。それ以外の場合は安全に削除できます。
  
## <a name="next-steps"></a>次のステップ

- [動作のしくみ (高速実行): 非揮発性メモリ SQL Server に導入されている NVDIMM でのログ末尾のキャッシング](/archive/blogs/bobsql/how-it-works-it-just-runs-faster-non-volatile-memory-sql-server-tail-of-log-caching-on-nvdimm)
- [Data exposed:SQL Server 2016 の待ち時間と持続性](https://channel9.msdn.com/Shows/Data-Exposed/Latency-and-Durability-with-SQL-Server-2016)
- [Windows Server 2016/SQL Server 2016 SP1 のストレージ クラス メモリを使用したトランザクション コミット待機時間の短縮](/archive/blogs/sqlserverstorageengine/transaction-commit-latency-acceleration-using-storage-class-memory-in-windows-server-2016sql-server-2016-sp1)
