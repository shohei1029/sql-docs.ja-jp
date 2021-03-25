---
description: トランザクション サイズを管理して、他のユーザーをブロックするロックをアプリケーションに導入しないようにする方法について説明します。
title: トランザクション サイズの管理
ms.custom: ''
ms.date: 08/12/2019
ms.prod: sql
ms.prod_service: connectivity
ms.reviewer: ''
ms.technology: connectivity
ms.topic: conceptual
ms.assetid: 82900342-bc80-445f-98a4-468a303aae1e
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: 2289e35116fc9ae39210db1b2af7dcb6e7dfad7a
ms.sourcegitcommit: bacd45c349d1b33abef66db47e5aa809218af4ea
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104793035"
---
# <a name="managing-transaction-size"></a>トランザクション サイズの管理

[!INCLUDE[Driver_JDBC_Download](../../includes/driver_jdbc_download.md)]

トランザクションを扱うときは、トランザクションをできるだけ短くすることが重要です。 [setAutoCommit](reference/setautocommit-method-sqlserverconnection.md) メソッドを使用して有効または無効にすることが可能な、既定の自動コミット モードでは、すべてのアクションが自動的にコミットされます。 このモードは、ほとんどの開発者にとっては最も簡単に扱えるモードです。

手動トランザクションを使用するときは、トランザクションを可能な限り迅速にコミットする必要があります。 開いているトランザクションを保持していると、他のユーザーがデータにアクセスできなくなります。 たとえば、ロールバックの呼び出しを catch ブロック内に配置し、コミットの呼び出しを `finally` ブロック内に配置するというプログラミング手法が望ましい場合があります。 ただしこの方法は、アプリケーションの設計によって異なります。

コンカレンシーを向上させるには、トランザクションのサイズを小さくしておきます。 たとえば、手動トランザクションを開始し、20,000 行のテーブル内の 10,000 行を変更すると、他のすべてのユーザーは、たとえデータを読み取ろうとしているだけであっても、テーブルの半分にアクセスできなくなります。 変更する行を 2,000 行に減らせば、テーブルの 90% はアクセス可能になります。

さらに、アプリケーションでブロックの問題が発生する可能性がある場合は、ロック タイムアウトの設定を使用してください。 タイムアウトを設定するには、[setLockTimeout](reference/setlocktimeout-method-sqlserverdatasource.md) メソッドを使用します。 ロック タイムアウトの既定値は -1 です。これは、ロックを待機している間に無期限にブロックすることを意味します。 ロック タイムアウトを 30 秒に設定すると、別の接続によってブロックされている接続が 30 秒でタイムアウトになります。

## <a name="see-also"></a>関連項目

[JDBC ドライバーによるパフォーマンスと信頼性の強化](improving-performance-and-reliability-with-the-jdbc-driver.md)
