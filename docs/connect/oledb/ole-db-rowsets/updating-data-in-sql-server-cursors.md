---
title: カーソルのデータを更新する (OLE DB ドライバー)
description: OLE DB Driver for SQL Server コンシューマー アプリケーションでは、SQL Server カーソルを使用することで、変更可能な行セットの要求を処理します。そのしくみについて説明します。
ms.custom: ''
ms.date: 06/14/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: connectivity
ms.topic: reference
helpviewer_keywords:
- updating data [SQL Server]
- isolation levels [SQL Server]
- delayed update mode [OLE DB]
- immediate update mode [OLE DB]
- cursors [OLE DB]
- data updates [SQL Server], OLE DB
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: 53096897b248205f36dc0a479db0d6750ff4069f
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104755752"
---
# <a name="updating-data-in-sql-server-cursors"></a>SQL Server カーソルでのデータ更新
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[Driver_OLEDB_Download](../../../includes/driver_oledb_download.md)]

  [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] カーソルによってデータをフェッチおよび更新する場合、OLE DB Driver for SQL Server のコンシューマー アプリケーションについても、他のクライアント アプリケーションと同様の考慮事項や制約があてはまります。  
  
 同時実行データアクセス制御に関係するのは、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] カーソル内の行だけです。 コンシューマーから変更可能な行セットが要求されるときは、コンカレンシー制御が DBPROP_LOCKMODE によって制御されます。 同時実行アクセス制御のレベルを変更するには、コンシューマーで行セットを開く前に DBPROP_LOCKMODE プロパティを設定します。  
  
 クライアント アプリケーションのデザインにより、トランザクションが長時間開かれたままの状態になる場合、トランザクション分離レベルが原因で行の位置指定が大幅に遅れる可能性があります。 既定では、OLE DB Driver for SQL Server は、DBPROPVAL_TI_READCOMMITTED によって指定される READ COMMITTED 分離レベルを使用します。 行セットのコンカレンシーが読み取り専用のときは、OLE DB Driver for SQL Server では、ダーティ リード分離がサポートされます。 そのため、コンシューマーは変更可能な行セットで、より高い分離レベルを要求できますが、より低い分離レベルは要求できません。  
  
## <a name="immediate-and-delayed-update-modes"></a>即時更新モードと遅延更新モード  
 即時更新モードでは、**IRowsetChange::SetData** を呼び出すたびに、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] との間にラウンドトリップが発生します。 コンシューマーが 1 つの行に複数の変更を加える場合は、**SetData** を 1 回呼び出してすべての変更を実行する方が効率が高くなります。  
  
 遅延更新モードでは、**IRowsetUpdate::Update** の *cRows* パラメーターと *rghRows* パラメーターに示される行ごとに、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] との間にラウンドトリップが発生します。  
  
 どちらのモードでも、行セットに対してトランザクション オブジェクトが開いていないときは、1 つのラウンドトリップが個別のトランザクションを表します。  
  
 **IRowsetUpdate::Update** を使用している場合、示された各行が OLE DB Driver for SQL Server で処理されます。 任意の行の無効なデータ、長さ、または状態値が原因でエラーが発生しても、OLE DB Driver for SQL Server による処理は停止されません。 更新に関係する他のすべての行が変更されることも、そのような行がまったく変更されないこともあります。 OLE DB Driver for SQL Server から DB_S_ERRORSOCCURRED が返されたときは、コンシューマーでは返された *prgRowStatus* 配列を調べて、特定の行で発生したエラーを判断する必要があります。  
  
 コンシューマーでは、特定の順序で行が処理されることを想定しないでください。 1 行以上の行に対してデータ変更を順序付けて処理することが必要な場合、アプリケーション ロジックで順序を確立し、トランザクションを開いてそのロジックをトランザクション内に含める必要があります。  
  
## <a name="see-also"></a>参照  
 [行セット内のデータの更新](../../oledb/ole-db-rowsets/updating-data-in-rowsets.md)  
  
  
