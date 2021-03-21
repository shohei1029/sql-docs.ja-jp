---
description: ページの書き込み
title: ページの書き込み | Microsoft Docs
ms.custom: ''
ms.date: 03/01/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: ''
ms.topic: conceptual
helpviewer_keywords:
- pages
ms.assetid: 409c8753-03c4-436d-839c-6a5879971551
author: pmasl
ms.author: pelopes
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: d0d41d9f67a1c9b210c9b3697e061d55dd3517b5
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104753212"
---
# <a name="writing-pages"></a>ページの書き込み
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[ssDE](../includes/ssde-md.md)] のインスタンスからの I/O には論理書き込みと物理書き込みがあります。 論理書き込みは、バッファー キャッシュ内のページのデータが変更されるときに行われます。 物理書き込みは、そのページを [バッファー キャッシュ](../relational-databases/memory-management-architecture-guide.md) からディスクに書き込むときに行われます。

ページがバッファー キャッシュ内で変更されたとき、その変更が直ちにディスクに書き戻されるわけではありません。代わりに、そのページはダーティとマークされます。 これはページが物理的にディスクに書き込まれる前に複数回の論理書き込みが行われていることを意味します。 論理書き込みを行うたびに、トランザクション ログ レコードが、変更を記録するログ キャッシュに挿入されます。 ログ レコードは、関連付けられているダーティ ページがバッファー キャッシュから削除されディスクに書き込まれる前に、ディスクに書き込まれる必要があります。 SQL Server では、先行書き込みログと呼ばれている手法が利用され、関連ログ レコードがディスクに書き込まれる前にダーティ ページが書き込まれることを防止します。 これは復旧マネージャーが正しく機能するために必要不可欠です。 詳細については、「 [先行書き込みトランザクション ログ](../relational-databases/sql-server-transaction-log-architecture-and-management-guide.md)」を参照してください。

次の図は、変更されたデータ ページが書き込まれるプロセスを示しています。

![Writing_Pages](../relational-databases/media/writing-pages.gif)

バッファー マネージャーでは、ページを書き込むときに、1 回のギャザー書き込み操作に含めることができる隣接するダーティ ページを検索します。 隣接するページとは、ページ ID が連続していて、同じファイルに含まれているページです。メモリ内で連続している必要はありません。 検索は、次に示すいずれかの状況が発生するまで、前後両方向に続行されます。

 * クリーンなページが見つかった。
 * 32 ページ分見つかった。
 * ログ内でまだフラッシュされていないログ シーケンス番号 (LSN) を含むダーティ ページが見つかった。
 * 直ちにラッチできないページが見つかった。

このようにして、ページのセット全体を 1 回のギャザー書き操作でディスクに書き込むことができます。 

ページが書き込まれる直前に、データベースで指定されたページ保護の形式が、ページに追加されます。 破損ページ保護が追加される場合、I/O に対してそのページを排他的にラッチする必要があります。 これは、破損ページ保護によってページが変更された結果、他のスレッドから読み取るのに不適切な形式となるためです。 チェックサム ページ保護が追加される場合、またはデータベースがページ保護を使用していない場合は、I/O に対してそのページを更新 (UP) ラッチでラッチします。 このラッチでは、書き込み中は他からのページ変更を防ぎますが、読み取りは許可します。 ディスク I/O のページ保護オプションの詳細については、「 [バッファー管理](../relational-databases/memory-management-architecture-guide.md)」を参照してください。

ダーティ ベージは次の 3 つの方法のいずれかでディスクに書き込まれます。 

* レイジー書き込み   
 レイジー ライターは、使用頻度の少ないページをバッファー キャッシュから削除することで空きバッファーを使用可能にするシステム プロセスです。 ダーティ ページが最初にディスクに書き込まれます。 

* 集中書き込み   
 集中書き込みプロセスでは、一括挿入や SELECT INTO 操作など、ログに最小限記録される操作に関連付けられているダーティ データ ページを書き込みます。 このプロセスにより、新しいページの作成と書き込みを並列に実行できます。 つまり、ページからディスクへの書き込み操作全体が完了するまで呼び出し操作が待機する必要はありません。

* Checkpoint   
 チェックポイント プロセスでは、指定されたデータベースからのページを含むバッファーのバッファー キャッシュを定期的にスキャンし、ダーティ ページをすべてディスクに書き込みます。 チェックポイントは、すべてのダーティ ページがディスクに書き込まれたことを確認するために作成されるポイントで、その後の復旧の時間を短縮します。 ユーザーは、CHECKPOINT コマンドを使用することでチェックポイント操作を要求できます。または、使用済みのログ容量と最後にチェックポイント操作が行われてからの経過時間に基づいて、 [!INCLUDE[ssDE](../includes/ssde-md.md)] によって自動的にチェックポイントが生成される場合もあります。 さらに、特定の利用状況が発生したときに、チェックポイントが生成されます。 たとえば、データ ファイルやログ ファイルがデータベースに追加されたり、データベースから削除されたりするとき、または SQL Server インスタンスが停止したときなどです。 詳細については、「 [チェックポイントとログのアクティブな部分](../relational-databases/sql-server-transaction-log-architecture-and-management-guide.md)」を参照してください。

レイジー書き込み、集中書き込み、チェックポイント プロセスは I/O 操作の完了を待機しません。 これらは、常に、非同期 (重複) I/O を使用し、他の作業と並列して続行され、I/O の成功は後でチェックします。 このため、SQL Server では CPU リソースと I/O リソースの両方を適切なタスクに最大限に利用できます。

## <a name="see-also"></a>参照
[ページとエクステントのアーキテクチャ ガイド](../relational-databases/pages-and-extents-architecture-guide.md)   
 [ページの読み取り](../relational-databases/reading-pages.md)
