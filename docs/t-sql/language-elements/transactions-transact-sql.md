---
description: トランザクション (Transact-SQL)
title: トランザクション (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 09/25/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- Transactions
- Transactions_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- transactions [SQL Server]
- transactions [SQL Server], about transactions
- UOW [SQL Server]
- unit of work [SQL Server]
ms.assetid: 1485c375-921a-42af-a871-bb333cc08d3e
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 0100e60ea692251ec246046211eba5483936130e
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104734421"
---
# <a name="transactions-transact-sql"></a>トランザクション (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  トランザクションは、1 つの作業を表す単位です。 トランザクションが成功すると、トランザクションの実行中に行われたすべてのデータ変更がコミットされ、データベースの変更が確定します。 エラーが発生したため、トランザクションを取り消すか、またはロールバックする必要がある場合、すべてのデータ変更は消去されます。  
  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] は、次のトランザクション モードで動作します。  
  
 自動コミット トランザクション  
 各ステートメントは 1 つのトランザクションです。  
  
 明示的なトランザクション  
 各トランザクションは、BEGIN TRANSACTION ステートメントで明示的に開始し、COMMIT または ROLLBACK ステートメントで明示的に終了します。  
  
 暗黙のトランザクション  
 新しいトランザクションは、前のトランザクションが終了すると暗黙的に開始しますが、各トランザクションは COMMIT または ROLLBACK ステートメントで明示的に終了します。  
  
 バッチスコープのトランザクション  
 複数のアクティブな結果セット (MARS) にのみ該当します。MARS セッションで開始された [!INCLUDE[tsql](../../includes/tsql-md.md)] の明示的または暗黙的なトランザクションは、バッチスコープのトランザクションになります。 バッチの完了時にコミットまたはロールバックされていないバッチスコープのトランザクションは、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] により自動的にロールバックされます。  

> [!NOTE] 
> Data Warehouse 製品に関連する特別な考慮事項については、「[トランザクション (Azure Synapse Analytics)](transactions-sql-data-warehouse.md)」を参照してください。   

## <a name="in-this-section"></a>このセクションの内容  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] には、次のトランザクション ステートメントが用意されています。  
  
:::row:::
    :::column:::
        [BEGIN DISTRIBUTED TRANSACTION](../../t-sql/language-elements/begin-distributed-transaction-transact-sql.md)
    :::column-end:::
    :::column:::
        [ROLLBACK TRANSACTION](../../t-sql/language-elements/rollback-transaction-transact-sql.md)
    :::column-end:::
:::row-end:::  
:::row:::
    :::column:::
        [BEGIN TRANSACTION](../../t-sql/language-elements/begin-transaction-transact-sql.md)
    :::column-end:::
    :::column:::
        [ROLLBACK WORK](../../t-sql/language-elements/rollback-work-transact-sql.md)
    :::column-end:::
:::row-end:::  
:::row:::
    :::column:::
        [COMMIT TRANSACTION](../../t-sql/language-elements/commit-transaction-transact-sql.md)
    :::column-end:::
    :::column:::
        [SAVE TRANSACTION](../../t-sql/language-elements/save-transaction-transact-sql.md)
    :::column-end:::
:::row-end:::  
:::row:::
    :::column:::
        [COMMIT WORK](../../t-sql/language-elements/commit-work-transact-sql.md)
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::
  
## <a name="see-also"></a>関連項目  
 [SET IMPLICIT_TRANSACTIONS &#40;Transact-SQL&#41;](../../t-sql/statements/set-implicit-transactions-transact-sql.md)   
 [@@TRANCOUNT &#40;Transact-SQL&#41;](../../t-sql/functions/trancount-transact-sql.md)  
  
  
