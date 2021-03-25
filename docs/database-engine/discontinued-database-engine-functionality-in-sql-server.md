---
title: 廃止されたデータベース エンジンの機能
description: SQL Server 2019 (15.x)、SQL Server 2016 (13.x)、および以前のバージョンで廃止されたデータベース エンジンの機能について説明します。
ms.custom: seo-lt-2019
ms.date: 07/22/2020
ms.prod: sql
ms.prod_service: high-availability
ms.reviewer: ''
ms.technology: release-landing
ms.topic: conceptual
helpviewer_keywords:
- VIA protocol
- unsupported features [SQL Server]
- SQL Mail
- discontinued functionality [SQL Server]
- RESTORE WITH DBO_ONLY
- BACKUP WITH PASSWORD
- user instances enabled
- BACKUP WITH MEDIAPASSWORD
- AWE
- SQL-DMO
- '*= and =*'
- 80 compatibility levels
- COMPUTE BY
- user instance timeout
- sp_dropalias
- COMPUTE
- SSL
- WITH APPEND
- sys.database_principal_aliases
- sp_dboption
- DATABASEPROPERTY
- FASTFIRSTROW hint
- SET DISABLE_DEF_CNST_CHK
ms.assetid: d686cdf0-d11d-4dba-9ec8-de1a5f189f25
author: MikeRayMSFT
ms.author: mikeray
monikerRange: '>= sql-server-linux-2017  || >= sql-server-2016'
ms.openlocfilehash: e1c5f8b15d40ff26cf1a164e806f7fa735b28319
ms.sourcegitcommit: 00af0b6448ba58e3685530f40bc622453d3545ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104673460"
---
# <a name="discontinued-database-engine-functionality-in-sql-server"></a>SQL Server で廃止されたデータベース エンジンの機能
[!INCLUDE[sqlserver](../includes/applies-to-version/sqlserver.md)]

  このトピックでは、 [!INCLUDE[ssDE](../includes/ssde-md.md)] で使用できなくなった [!INCLUDE[ssnoversion](../includes/ssnoversion-md.md)]の機能について説明します。  

## <a name="discontinued-features-in-sssql19"></a>[!INCLUDE[sssql19](../includes/sssql19-md.md)] で廃止された機能  

- 次のデータベース スコープ構成オプションは廃止されました。

  - `DISABLE_BATCH_MODE_ADAPTIVE_JOIN`
  - `DISABLE_BATCH_MODE_MEMORY_GRANT_FEEDBACK`
  - `DISABLE_INTERLEAVED_EXECUTION_TVF`

現在の構成オプションについては、「[ALTER DATABASE SCOPED CONFIGURATION (Transact-SQL)](../t-sql/statements/alter-database-scoped-configuration-transact-sql.md)」を参照してください。

>[!NOTE]
>[!INCLUDE[sssql14](../includes/sssql17-md.md)] で廃止された機能はありません。

## <a name="discontinued-features-in-sssql15-md"></a>[!INCLUDE[sssql15-md](../includes/sssql16-md.md)] で廃止された機能

- [!INCLUDE[sssql15-md](../includes/sssql16-md.md)] は 64 ビット アプリケーションです。 32 ビット版のインストールは廃止されましたが、いくつかの要素が 32 ビット コンポーネントとして実行されます。  

- 互換性レベル 90 は廃止されました。 詳細については、「[ALTER DATABASE 互換性レベル &#40;Transact-SQL&#41;](../t-sql/statements/alter-database-transact-sql-compatibility-level.md)」を参照してください。  

- ActiveX サブシステムは廃止されました。 代わりに、コマンド ラインまたは PowerShell スクリプトを使用してください。

- 起動時のパラメーター **-h** および **-g**。 詳細については、「 [データベース エンジン サービスのスタートアップ オプション](/previous-versions/sql/2014/database-engine/configure-windows/database-engine-service-startup-options?view=sql-server-2014&preserve-view=true)」を参照してください。

- Secure Sockets Layer (SSL) での暗号化は廃止されました。 代わりに、トランスポート層セキュリティ (TLS) を使用してください。 詳細については、「[データベース エンジンへの暗号化接続の有効化](../database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine.md)」を参照してください。

- `precompute rank` Server 構成オプションは SQL Server 2008 以降、廃止となりました。 この記事はドキュメントから削除されています。

## <a name="previous-versions"></a>以前のバージョン

- [SQL Server 2014 で廃止されたデータベース エンジンの機能](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016?view=sql-server-2014&preserve-view=true)

### <a name="see-also"></a>関連項目

- [SQL Server 2019 データベース エンジンの非推奨の機能](deprecated-database-engine-features-in-sql-server-version-15.md)
- [SQL Server 2017 データベース エンジンの非推奨の機能](deprecated-database-engine-features-in-sql-server-2017.md)
- [SQL Server 2016 データベース エンジンの非推奨の機能](../database-engine/deprecated-database-engine-features-in-sql-server-2016.md)
- [SQL Server 2019 におけるデータベース エンジン機能の重大な変更](breaking-changes-to-database-engine-features-in-sql-server-version-15.md)
- [SQL Server 2017 におけるデータベース エンジン機能の重大な変更](breaking-changes-to-database-engine-features-in-sql-server-2017.md)
- [SQL Server 2016 におけるデータベース エンジン機能の重大な変更](breaking-changes-to-database-engine-features-in-sql-server-2016.md)
- [SQL Server レプリケーションの非推奨の機能](../relational-databases/replication/deprecated-features-in-sql-server-replication.md)