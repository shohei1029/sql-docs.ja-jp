---
title: データベース エンジンのアップグレードの完了 | Microsoft Docs
description: この記事では、SQL Server のデータベース エンジンのアップグレードを完了した後に実行する必要がある追加の手順について説明します。
ms.custom: ''
ms.date: 10/23/2017
ms.prod: sql
ms.technology: install
ms.reviewer: ''
ms.topic: conceptual
ms.assetid: 3f08087e-e532-416c-8caa-e0ec88c57596
author: cawrites
ms.author: chadam
monikerRange: '>=sql-server-2016'
ms.openlocfilehash: f9c9848bdfdd9aa9cb951b0a7b419353ea32a6f6
ms.sourcegitcommit: bf7577b3448b7cb0e336808f1112c44fa18c6f33
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104611096"
---
# <a name="complete-the-database-engine-upgrade"></a>データベース エンジンのアップグレードの完了

[!INCLUDE [SQL Server -Windows Only](../../includes/applies-to-version/sql-windows-only.md)]

SQL Server へのアップグレードが完了したら、必要に応じて追加の手順をいくつか実行する必要があります。 コーディネートは次のとおりです。  
  
[!INCLUDE[ssDE](../../includes/ssde-md.md)]をアップグレードした後は、次の作業を実行します。  
  
- **データベースのバックアップ:** 各データベースの完全バックアップを実行します。  

- **新機能の有効化:** SQL Server 2016、2017 および 2019 の一部の変更は、データベースの DATABASE_COMPATIBILITY レベルが 130 以上に変更された場合にのみ有効になります。  詳細と推奨ワークフローについては、「 [データベース互換性モードの変更とクエリ ストアの使用](../../database-engine/install-windows/change-the-database-compatibility-mode-and-use-the-query-store.md)」を参照してください。 データベースに SQL Server 2014 で作成されたメモリ最適化テーブルがある場合は、「[メモリ最適化テーブルの統計](../../relational-databases/in-memory-oltp/statistics-for-memory-optimized-tables.md)」を確認してください。
  
- **Integration Services:**  
  
     Integration Services パッケージを最新の形式に移行します。 詳細については、「 [Upgrade Integration Services Packages](../../integration-services/install-windows/upgrade-integration-services-packages.md)」を参照してください。  
  
- **Reporting Services:** 新しいインストールのアップグレードの場合は、Reporting Services の暗号化キーを復元します。 詳細については、「 [Back Up and Restore Reporting Services Encryption Keys](../../reporting-services/install-windows/ssrs-encryption-keys-back-up-and-restore-encryption-keys.md)」を参照してください。  
  
- **マスター データ サービス:** MDS データベース スキーマをアップグレードし、SQL Server 2019 の Web アプリケーションを作成します。 詳細については、「 [Upgrade Master Data Services](../../database-engine/install-windows/upgrade-master-data-services.md)」を参照してください。  
  
- **Data Quality Services:** DQS データベース スキーマをアップグレードし、DQS データベース スキーマのアップグレードを確認します。 詳細については、「 [Upgrade Data Quality Services](../../database-engine/install-windows/upgrade-data-quality-services.md)」を参照してください。  
  
- **フルテキスト検索:** クエリ結果のセマンティクスの一貫性を維持するために、フルテキスト カタログを再作成します。 詳細については、「 [フルテキスト インデックスの作成](../../relational-databases/search/populate-full-text-indexes.md)」をご覧ください。  
  
