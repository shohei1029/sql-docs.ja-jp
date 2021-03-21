---
description: レプリケーションに対して外部キー制約を無効にする方法
title: レプリケーションに対して外部キー制約を無効にする方法 | Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: table-view-index
ms.topic: conceptual
helpviewer_keywords:
- constraints [SQL Server], foreign keys
- foreign keys [SQL Server], disabling constraints
- disabling constraints
ms.assetid: 4211f2fd-d16a-4081-995c-43f1f0827f0b
author: stevestein
ms.author: sstein
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 26fe20c258d3d0ad46e5affc037dd46a30603a4b
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104747732"
---
# <a name="disable-foreign-key-constraints-for-replication"></a>レプリケーションに対して外部キー制約を無効にする方法
[!INCLUDE [sqlserver2016-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sqlserver2016-asdb-asdbmi-asa-pdw.md)]

  [!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] では、 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] または [!INCLUDE[tsql](../../includes/tsql-md.md)]を使用して、レプリケーションに対する外部キー制約を無効にできます。 これは、以前のバージョンの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]からのデータをパブリッシュする場合に便利です。  
  
> [!NOTE]  
>  レプリケーションを使用してテーブルをパブリッシュした場合、レプリケーション エージェントが実行する操作については外部キー制約が自動的に無効になります。 レプリケーション エージェントがサブスクライバー側で挿入、更新、または削除を実行した場合、制約のチェックは行われません。ユーザーが挿入、更新、または削除を実行した場合は、制約のチェックが行われます。 制約がレプリケーション エージェントに対して無効になるのは、データが最初に挿入、更新、または削除された際に、発行者側で既に制約がチェックされているためです。  
  
 **このトピックの内容**  
  
-   **作業を開始する準備:**  
  
     [Security](#Security)  
  
-   **以下を使用してレプリケーションに対する外部キー制約を無効にするには**  
  
     [SQL Server Management Studio](#SSMSProcedure)  
  
     [Transact-SQL](#TsqlProcedure)  
  
##  <a name="before-you-begin"></a><a name="BeforeYouBegin"></a> はじめに  
  
###  <a name="security"></a><a name="Security"></a> セキュリティ  
  
####  <a name="permissions"></a><a name="Permissions"></a> Permissions  
 テーブルに対する ALTER 権限が必要です。  
  
##  <a name="using-sql-server-management-studio"></a><a name="SSMSProcedure"></a> SQL Server Management Studio の使用  
  
#### <a name="to-disable-a-foreign-key-constraint-for-replication"></a>レプリケーションに対して外部キー制約を無効にするには  
  
1.  **オブジェクト エクスプローラー** で、変更する外部キー制約が含まれているテーブルを展開し、 **[キー]** フォルダーを展開します。  
  
2.  外部キー制約を右クリックし、 **[変更]** をクリックします。  
  
3.  **[外部キーのリレーションシップ]** ダイアログ ボックスで、 **[レプリケーションに対して適用]** の値として **[いいえ]** を選択します。  
  
4.  **[閉じる]** をクリックします。  

##  <a name="using-transact-sql"></a><a name="TsqlProcedure"></a> Transact-SQL の使用  
  
#### <a name="to-disable-a-foreign-key-constraint-for-replication"></a>レプリケーションに対して外部キー制約を無効にするには  
  
1.  [!INCLUDE[tsql](../../includes/tsql-md.md)]でこの作業を実行するには、外部キー制約を削除します。 次に、新しい外部キー制約を追加し、NOT FOR REPLICATION オプションを指定します。  
  
 詳細については、「[ALTER TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-table-transact-sql.md)」を参照してください。  
  
###  <a name="TsqlExample"></a>  
