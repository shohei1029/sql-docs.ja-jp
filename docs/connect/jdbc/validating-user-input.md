---
description: SQL インジェクション攻撃からアプリケーションを守る上でユーザー入力の検証が非常に重要となる理由について学びます。
title: ユーザー入力の検証
ms.custom: ''
ms.date: 08/12/2019
ms.prod: sql
ms.prod_service: connectivity
ms.reviewer: ''
ms.technology: connectivity
ms.topic: conceptual
ms.assetid: 8aa867b0-e6f0-49eb-93d3-817ae2ed8f77
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: 00aa7d14354ca29859806611550540674a798e48
ms.sourcegitcommit: 00af0b6448ba58e3685530f40bc622453d3545ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104673936"
---
# <a name="validating-user-input"></a>ユーザー入力の検証

[!INCLUDE[Driver_JDBC_Download](../../includes/driver_jdbc_download.md)]

データにアクセスするアプリケーションを作成する場合は、すべてのユーザー入力について、悪意がないと確認されるまでは、悪意があるものと見なす必要があります。 攻撃に対する脆弱性をアプリケーションから取り除くことはできません。 発生する可能性がある攻撃の一種に、SQL インジェクションと呼ばれるものがあります。これは、後で [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスに渡され解析および実行が行われる文字列に、悪意のあるコードを挿入するというものです。 この攻撃を防ぐには、パラメータを持つストアド プロシージャを可能な限り使用し、ユーザー入力を常に検証する必要があります。

サーバーへの無駄なラウンド トリップを行わないようにするには、ユーザー入力の検証をクライアント コードで行うことが重要です。 同様に、ストアド プロシージャへのパラメータをサーバー上で検証し、有効でない入力や、クライアント側の検証をバイパスする入力を検出することも重要です。

SQL インジェクションと、この攻撃を避ける方法の詳細については、「[SQL インジェクション](../../relational-databases/security/sql-injection.md)」を参照してください。 ストアド プロシージャのパラメーターの検証の詳細については、「[ストアド プロシージャ](../../relational-databases/stored-procedures/stored-procedures-database-engine.md)」と関連記事を参照してください。

## <a name="see-also"></a>関連項目

[JDBC ドライバー アプリケーションのセキュリティ保護](securing-jdbc-driver-applications.md)
