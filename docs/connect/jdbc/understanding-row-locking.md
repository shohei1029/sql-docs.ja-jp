---
description: 同時ユーザーが異なる接続からデータに同時アクセスするとき、そのアクセスを制御する目的で行ロックを使用する方法について学びます。
title: 行ロックについて
ms.custom: ''
ms.date: 12/08/2020
ms.prod: sql
ms.prod_service: connectivity
ms.reviewer: ''
ms.technology: connectivity
ms.topic: conceptual
ms.assetid: 63c76a2f-f2b9-461f-8904-acbda0169ac3
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: 589700b7d3f4a08958e306f59e87249263f86675
ms.sourcegitcommit: 00af0b6448ba58e3685530f40bc622453d3545ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104673146"
---
# <a name="understanding-row-locking"></a>行ロックについて

[!INCLUDE[Driver_JDBC_Download](../../includes/driver_jdbc_download.md)]

[!INCLUDE[jdbcNoVersion](../../includes/jdbcnoversion_md.md)] は、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の行ロックを使用します。 これらには、データベースへの変更を複数のユーザーが同時に行う際のコンカレンシー制御が実装されています。 既定では、トランザクションとロックは接続ごとに管理されます。 たとえば、アプリケーションで 2 つの JDBC 接続が開かれている場合、一方の接続で取得したロックをもう一方の接続と共有することはできません。 どちらの接続でも、もう一方の接続によって保持されているロックと競合するロックは取得できません。

> [!NOTE]  
> 行ロックの使用時にはフェッチ バッファー内のすべての行がロックされるため、フェッチ サイズを非常に大きく設定すると、コンカレンシーに影響を与える可能性があります。

ロックは、トランザクションの整合性とデータベースの一貫性を保つために使用します。 ロックを使用すると、他のユーザーがデータを変更している最中にそのデータを読み取ったり、複数のユーザーが同時に同一のデータを変更したりする危険性を回避できます。 ロックを使用しないと、データベース内のデータが論理的に正しくなくなったり、そのデータに対して実行したクエリから予期しない結果が返されたりする可能性があります。

> [!NOTE]  
> [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の行ロックの詳細については、「[[!INCLUDE[ssDE](../../includes/ssde_md.md)]のロック](../../relational-databases/sql-server-transaction-locking-and-row-versioning-guide.md#Lock_Engine)」を参照してください。

## <a name="see-also"></a>関連項目

[JDBC ドライバーによる結果セットの管理](managing-result-sets-with-the-jdbc-driver.md)
