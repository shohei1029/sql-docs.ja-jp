---
title: ゴースト クリーンアップ プロセスのガイド | Microsoft Docs
description: SQL Server で削除対象としてマークされているページからレコードを削除するバックグラウンド プロセスである、ゴースト クリーンアップ プロセスについて説明します。
ms.custom: ''
ms.date: 05/02/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: supportability
ms.topic: conceptual
helpviewer_keywords:
- ghost cleanup
- ghost records
- ghost clean up process
author: MashaMSFT
ms.author: mathoma
ms.openlocfilehash: 05113205c693de928321880f1586996f81b76a66
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104750362"
---
# <a name="ghost-cleanup-process-guide"></a>ゴースト クリーンアップ プロセスのガイド

ゴースト クリーンアップ プロセスは、削除対象としてマークされているページからレコードを削除する、シングル スレッドのバックグラウンド プロセスです。 以下の記事に、このプロセスの概要を示します。

## <a name="ghost-records"></a>ゴースト レコード

インデックス ページのリーフ レベルから削除されたレコードは、物理的にはページから削除されません。代わりに、レコードに "削除対象" (つまり、*ゴースト*) としてマークされます。 つまり、行はページで保持されますが、行ヘッダーのビットが変更され、行が実際にはゴーストであることが示されます。 これは、削除操作中のパフォーマンスを最適化するためのものです。 ゴーストは行レベルのロックに必要ですが、古いバージョンの行を維持する必要があるスナップショットの分離にも必要です。

## <a name="ghost-record-cleanup-task"></a>ゴースト レコードのクリーンアップ タスク

削除対象 (つまり、*ゴースト*) としてマークされているレコードは、バックグラウンド ゴースト クリーンアップ プロセスでクリーンアップされます。 このバックグラウンド プロセスは、削除トランザクションがコミットされた後、しばらくしてから実行され、ページから物理的にゴースト レコードを削除します。 ゴースト クリーンアップ プロセスは自動的に一定間隔 (SQL Server 2012 以降の場合は 5 秒ごと、SQL Server 2008/2008R2 の場合は 10 秒ごと) で実行され、ページがゴースト レコードでマークされているかどうかを確認します。 何かが検出された場合、削除対象 (つまり、*ゴースト*) としてマークされているレコードが削除されます。実行ごとに最大 10 ページが処理されます。

ゴースト レコードである場合、データベースはゴースト エントリがあるとマークされ、ゴースト クリーンアップ プロセスではそのデータベースのみがスキャンされます。 また、ゴースト クリーンアップ プロセスでは、すべてのゴースト レコードが削除されると、データベースは "ゴースト レコードがない" とマークされ、次回の実行時にこのデータベースをスキップします。 さらに、このプロセスでは、共有ロックできないすべてのデータベースをスキップし、次回の実行時に再試行します。

以下のクエリでは、単一のデータベースに存在するゴースト レコードの数を特定できます。 

 ```sql
 SELECT sum(ghost_record_count) total_ghost_records, db_name(database_id) 
 FROM sys.dm_db_index_physical_stats (NULL, NULL, NULL, NULL, 'SAMPLED')
 group by database_id
 order by total_ghost_records desc
```

## <a name="disable-the-ghost-cleanup"></a>ゴースト クリーンアップを無効にする

削除が多く行われる負荷の高いシステムでは、ゴースト クリーンアップ プロセスにより、バッファー プールでページが保持され、IO が生成され、パフォーマンスの問題が発生する可能性があります。 そのため、[トレース フラグ 661](../t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql.md) を使用して、このプロセスを無効にすることができます。 ただし、プロセスを無効にするとパフォーマンスに影響します。

ゴースト クリーンアップ プロセスを無効にすると、データベースのサイズが不必要に大きくなる可能性があり、パフォーマンスの問題につながる場合があります。 ゴースト クリーンアップ プロセスでは、ゴーストとしてマークされているレコードが削除されるため、プロセスを無効にすると、これらのレコードはページに残り、SQL Server でこのスペースを再利用できなくなります。 これにより、SQL Server では代わりに新しいページにデータが強制的に追加され、データベース ファイルの肥大化につながり、また、[ページ分割](indexes/specify-fill-factor-for-an-index.md)の原因ともなります。 ページ分割は、実行プランの作成時と、スキャン操作の実行時にパフォーマンスの問題につながります。 

ゴースト クリーンアップ プロセスを無効にした場合、ゴースト レコードを削除するためになんらかのアクションを実行する必要があります。 その場合、インデックスの再構築を実行して、ページのデータを移動することができます。 また、手動で [sp_clean_db_free_space](system-stored-procedures/sp-clean-db-free-space-transact-sql.md) (すべてのデータベースのデータ ファイルをクリーンアップする場合) または [sp_clean_db_file_free_space](system-stored-procedures/sp-clean-db-file-free-space-transact-sql.md) (単一のデータベースのデータ ファイルをクリーンアップする場合) を実行して、ゴースト レコードを削除することもできます。

 >[!warning]
 > ゴースト クリーンアップ プロセスを無効にすることは、一般には推奨されません。 これを行う場合は、運用環境で完全に実装する前に、制御された環境で十分テストする必要があります。


## <a name="next-steps"></a>次のステップ  
[ゴースト クリーンアップ プロセスの無効化](https://support.microsoft.com/help/920093/tuning-options-for-sql-server-when-running-in-high-performance-workloa)
<br>[単一のデータベースのファイルからゴースト レコードを削除する](system-stored-procedures/sp-clean-db-file-free-space-transact-sql.md)
<br>[すべてのデータベースのデータ ファイルからゴースト レコードを削除する](system-stored-procedures/sp-clean-db-free-space-transact-sql.md)


