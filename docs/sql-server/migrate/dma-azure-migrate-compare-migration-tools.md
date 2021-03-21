---
title: SQL データ移行ツールを比較する
titleSuffix: SQL Server
description: '次のような SQL データ移行ツールを比較して、ビジネス ニーズに最も適しているツールを特定します: Data Migration Assistant (DMA)、Azure Migrate、Azure Database Migration Service、SQL Server Migration Assistant (SSMA)、Database Experimentation Assistant (DEA)。 '
ms.date: 03/15/2021
ms.prod: sql
ms.prod_service: dma
ms.reviewer: ''
ms.technology: dma
ms.topic: conceptual
keywords: ''
helpviewer_keywords:
- Data Migration Assistant, on-premises SQL Server
ms.assetid: ''
author: rajpo
ms.author: rajpo
ms.openlocfilehash: fc406a39dc0b5d1a80e4f357a28b6945da0bf453
ms.sourcegitcommit: ecf074e374426c708073c7da88313d4915279fb9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103603312"
---
# <a name="compare-sql-data-migration-tools"></a>SQL データ移行ツールを比較する

Microsoft は、ユーザーがさまざまな種類のソース データベースをさまざまなターゲット環境に移行するのを支援するために設計された一連のツールとサービスを提供しています。 

この記事では、SQL Server および Azure SQL への移行に使用できるツールの概要を説明します。 

## <a name="azure-migrate"></a>Azure Migrate

Azure Migrate には、オンプレミスのサーバー、インフラストラクチャ、アプリケーション、およびデータを検出し評価して Azure への大規模な移行に備えるための一元的なハブが用意されています。  Azure Migrate では、ご利用のサーバー、データベース、およびアプリケーションにわたる移行が統合されています。 

Azure Migrate を使用すれば、ご利用のデータ センター全体のすべての SQL Server を検出し、アプリケーションの依存関係を評価し、Azure SQL に移行するこれらの SQL Server の準備状態を把握し、Microsoft の推奨事項を取得し、さらにご利用のワークロードのパフォーマンス ニーズに応じた最適な Azure SQL デプロイ オプションと適切な SKU を取得することができます。  また、ライセンス特典に応じて、これらのデータベースを Azure SQL 上で実行して月単位の見積もりを取得することもできます。 

Azure Migrate は、次のシナリオで使用します。 
- ご利用の SQL Server データ資産を評価および検出する。 
- Azure SQL デプロイに関する推奨事項、ターゲットのサイズ設定、月単位の見積もりを取得する。
- ご利用のデータ資産全体を Azure VM 上の SQL Server にリフト アンド シフトする。 

詳細については、「[Azure Migrate のドキュメント](/azure/migrate/)」を参照してください。 

## <a name="data-migration-assistant-dma"></a>Data Migration Assistant (DMA)

Data Migration Assistant (DMA) を使用すると、新しいバージョンの SQL Server または Azure SQL Database でのデータベース機能に影響するおそれがある互換性の問題が検出されるため、最新のデータ プラットフォームへのアップグレードが楽になります。 DMA は、ターゲット環境のパフォーマンスと信頼性を高めるための推奨事項を提案し、スキーマ、データ、非コンテナー化オブジェクトをソース サーバーからターゲット サーバーに移動できるようにします。

DMA は次のシナリオで使用します。 
- SQL Server 2005 以降のバージョンを Windows および Linux 上の SQL Server 2012、SQL Server 2014、SQL Server 2016、SQL Server 2017、および SQL Server 2019 に、また Azure VM 上の SQL Server にアップグレードする。 
- より新しいターゲット バージョンの SQL Server または Azure SQL のデータベース機能に影響するおそれがある互換性の問題を検出し、軽減手順を示す。 
- スキーマ、データ、および非包含オブジェクトをソース サーバーから SQL Server または Azure SQL に移動する。 

詳細については、[Data Migration Assistant のドキュメント](../../dma/dma-overview.md)を参照してください。 

## <a name="sql-server-migration-assistant-ssma"></a>SQL Server Migration Assistant (SSMA)

SQL Server Migration Assistant (SSMA) は、別のデータベース エンジンから SQL Server および Azure SQL にデータベースを自動的に移行できるように設計されたツールです。 

SSMA は次のシナリオで使用します。
- Microsoft Access、DB2、MySQL、Oracle、SAP ASE の各データベースを SQL Server に移行する。
- Microsoft Access、DB2、MySQL、Oracle、SAP ASE の各データベースを Azure SQL に移行する。

詳細については、[SQL Server Migration Assistant のドキュメント](../../ssma/sql-server-migration-assistant.md)を参照してください。

## <a name="azure-database-migration-service-dms"></a>Azure Database Migration Service (DMS)

Azure Database Migration Service を使用すると、複数のデータベース ソースから Azure データ プラットフォームに、ダウンタイムを最小限に抑えながらシームレスに移行できます。  Database Migration Service では、移行プロセス全体においてユーザーの関与を最小限に抑え回復性と信頼性に優れた移行パイプラインを提供します。 

Database Migration Service は次のシナリオで使用します。
- 両方のデータベースを Azure SQL に移行し (特に大規模に)、大規模な (データベースの数とサイズの観点から) 移行に対応する。 
- データベースを Azure Database に移行する。

詳細については、「[Azure Database Migration Service のドキュメント](/azure/dms/)」を参照してください。 

## <a name="database-experimentation-assistant-dea"></a>Database Experimentation Assistant (DEA)

Database Experimentation Assistant (DEA) は、SQL Server アップグレード用の実験ソリューションです。 DEAは、特定のワークロードについて、ターゲットとするバージョンの SQL Server を評価するのに役立ちます。 お客様が以前のバージョンの SQL Server (2005 以降) から最近のバージョンの SQL Server にアップグレードする場合、このツールによって提供される分析メトリックを使用できます。

Database Experimentation Assistant は次のシナリオで使用します。
- ソース SQL Server 環境のワークロードをキャプチャし、ソース SQL Server 上のワークロードを評価して、移行の準備を整える 
- 使用する SQL Server の移行シナリオにおいて互換性エラーと、低下しているおそれがあるクエリを特定します。 

詳細については、[Database Experimentation Assistant のドキュメント](../../dea/database-experimentation-assistant-overview.md)を参照してください。


## <a name="quick-comparison"></a>比較早見表

SQL 移行ツールの機能を比較するには、次の表を使用してください。


| 機能 |Azure Migrate  |Data Migration Assistant (DMA)  |SQL Server Migration Assistant (SSMA)  | Azure Database Migration Service (DMS) | Database Experimentation Assistant (DEA)|
|---------|---------|---------|---------|---|---|
|SQL データ資産の検出と評価| 大規模 | はい |いいえ |いいえ|いいえ|
|SQL Server オブジェクトを SQL Database または SQL Managed Instance に移行する| いいえ| はい | いいえ  | はい|いいえ|
|SQL Server を Azure VM 上の SQL Server にリフト アンド シフトする | はい | いいえ | いいえ | いいえ| いいえ |
|SQL Server を Azure VM 上の SQL Server に移行 (またはアップグレード) する | いいえ | はい| いいえ | いいえ | いいえ| 
|SQL 以外のオブジェクトを移行する </br> (Oracle、Access、DB2 など) | いいえ |いいえ|はい|いいえ|いいえ|
|オープン ソース データベースを移行する </br> (MySQL、PostgreSQL、MariaDB など)| いいえ | いいえ | いいえ | はい | いいえ|
|ソースとターゲットの SQL Server 間でワークロードを比較する |いいえ|いいえ|いいえ|いいえ|はい|
|||||||

## <a name="next-steps"></a>次の手順

別のデータベース エンジンから [SQL Server](../../ssma/sql-server-migration-assistant.md) への移行を開始するか、[Azure SQL](/azure/azure-sql/migration-guides/) に移行するか、または [Azure Migrate](/azure/migrate/how-to-create-azure-sql-assessment) を使用して SQL データ資産を評価します。 

