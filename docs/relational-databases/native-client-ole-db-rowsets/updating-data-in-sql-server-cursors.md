---
description: SQL Server Native Client 内の SQL Server カーソルのデータの更新
title: カーソルのデータを更新する (Native Client OLE DB プロバイダー)
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- updating data [SQL Server]
- isolation levels [SQL Server]
- delayed update mode [OLE DB]
- immediate update mode [OLE DB]
- cursors [OLE DB]
- data updates [SQL Server], OLE DB
ms.assetid: 732dafee-f2d5-4aef-aad7-3a8bf3b1e876
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: df6788d1883ed5105e5a008fb59e8e0c2d26a0f2
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104743942"
---
# <a name="updating-data-in-sql-server-cursors-in-sql-server-native-client"></a>SQL Server Native Client 内の SQL Server カーソルのデータの更新
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  カーソルを使用してデータをフェッチおよび更新する場合 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native client OLE DB プロバイダーコンシューマーアプリケーションは、他のクライアントアプリケーションに適用されるのと同じ考慮事項と制約によってバインドされます。  
  
 同時実行データアクセス制御に関係するのは、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] カーソル内の行だけです。 コンシューマーから変更可能な行セットが要求されるときは、コンカレンシー制御が DBPROP_LOCKMODE によって制御されます。 同時実行アクセス制御のレベルを変更するには、コンシューマーで行セットを開く前に DBPROP_LOCKMODE プロパティを設定します。  
  
 クライアント アプリケーションのデザインにより、トランザクションが長時間開かれたままの状態になる場合、トランザクション分離レベルが原因で行の位置指定が大幅に遅れる可能性があります。 既定では、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client OLE DB プロバイダーは、DBPROPVAL_TI_READCOMMITTED によって指定された read committed 分離レベルを使用します。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]Native Client OLE DB プロバイダーは、行セットの同時実行が読み取り専用の場合は、ダーティ読み取り分離をサポートします。 そのため、コンシューマーは変更可能な行セットで、より高い分離レベルを要求できますが、より低い分離レベルは要求できません。  
  
## <a name="immediate-and-delayed-update-modes"></a>即時更新モードと遅延更新モード  
 即時更新モードでは、**IRowsetChange::SetData** を呼び出すたびに、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] との間にラウンドトリップが発生します。 コンシューマーが 1 つの行に複数の変更を加える場合は、**SetData** を 1 回呼び出してすべての変更を実行する方が効率が高くなります。  
  
 遅延更新モードでは、**IRowsetUpdate::Update** の *cRows* パラメーターと *rghRows* パラメーターに示される行ごとに、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] との間にラウンドトリップが発生します。  
  
 どちらのモードでも、行セットに対してトランザクション オブジェクトが開いていないときは、1 つのラウンドトリップが個別のトランザクションを表します。  
  
 **IRowsetUpdate:: Update** を使用する場合、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client OLE DB プロバイダーは、指定された各行を処理しようとします。 行に対して無効なデータ、長さ、または状態の値が原因でエラーが発生しても、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client OLE DB プロバイダーの処理は停止されません。 更新に関係する他のすべての行が変更されることも、そのような行がまったく変更されないこともあります。 コンシューマーは、返された *Prgrowstatus* 配列を調べて、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client OLE DB プロバイダーが DB_S_ERRORSOCCURRED を返したときに特定の行のエラーを特定する必要があります。  
  
 コンシューマーでは、特定の順序で行が処理されることを想定しないでください。 1 行以上の行に対してデータ変更を順序付けて処理することが必要な場合、アプリケーション ロジックで順序を確立し、トランザクションを開いてそのロジックをトランザクション内に含める必要があります。  
  
## <a name="see-also"></a>参照  
 [行セット内のデータの更新](../../relational-databases/native-client-ole-db-rowsets/updating-data-in-rowsets.md)  
  
  
