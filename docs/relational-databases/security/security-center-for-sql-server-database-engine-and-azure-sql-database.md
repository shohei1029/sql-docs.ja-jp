---
title: SQL Server と Azure SQL Database に関するセキュリティ ドキュメント
description: SQL Server と Azure SQL Database に関するセキュリティと保護関連のコンテンツのリファレンス。
ms.custom: seo-lt-2019
ms.date: 09/27/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: security
ms.topic: conceptual
f1_keywords:
- Security [SQL Server]
helpviewer_keywords:
- SQL Server, security
- security [SQL Server]
- database security [SQL Server]
- databases [SQL Server], security
ms.assetid: dfb39d16-722a-4734-94bb-98e61e014ee7
author: VanMSFT
ms.author: vanto
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 799f00bfd7ddf0763212cefb00d8fea72a7dce27
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104739922"
---
# <a name="security-center-for-sql-server-database-engine-and-azure-sql-database"></a>SQL Server データベース エンジンと Azure SQL Database のセキュリティ センター
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  このページでは、 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] および [!INCLUDE[ssSDSFull](../../includes/sssdsfull-md.md)]でのセキュリティと保護に関して必要になる情報の検索に役立つリンクを示します。  
  
 **凡例**  
  
 ![使用可能な機能アイコンについて説明している凡例のスクリーンショット。](../performance/media/security-center-legend.PNG "security-center-legend")  
  
##  <a name="authentication-who-are-you"></a><a name="Who"></a> 認証:ユーザーはだれか  
  
|||  
|-|-|  
|**だれが認証したか**<br /><br /> :::image type="icon" source="../performance/media/security-center-sqlserver.png"::: Windows 認証<br /><br /> :::image type="icon" source="../performance/media/security-center-both.png"::: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 認証<br /><br /> :::image type="icon" source="../../relational-databases/security/media/security-center-sqldb.png":::  Azure Active Directory|だれが認証したか (Windows または [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)])<br /><br /> [認証モードの選択](../../relational-databases/security/choose-an-authentication-mode.md)<br /><br /> [Azure Active Directory の認証を使用して、SQL データベースに接続します。](/azure/azure-sql/database/authentication-aad-overview)|  
|**どこで認証されたか**<br /><br /> :::image type="icon" source="../performance/media/security-center-both.png"::: マスター データベース:ログインと DB ユーザー<br /><br /> :::image type="icon" source="../performance/media/security-center-both.png"::: ユーザー データベース:包含 DB ユーザー|マスター データベースでの認証 (ログインとデータベース ユーザー)<br /><br /> [SQL Server ログインの作成](../../relational-databases/security/authentication-access/create-a-login.md)<br /><br /> [Azure SQL Database におけるデータベースとログインの管理](/previous-versions/azure/ee336235(v=azure.100))<br /><br /> [データベース ユーザーの作成](../../relational-databases/security/authentication-access/create-a-database-user.md)<br /><br /> <br /><br /> ユーザー データベースでの認証<br /><br /> [包含データベース ユーザー - データベースの可搬性を確保する](../../relational-databases/security/contained-database-users-making-your-database-portable.md)|  
|**他の ID の使用**<br /><br /> :::image type="icon" source="../performance/media/security-center-both.png"::: [資格情報]<br /><br /> :::image type="icon" source="../performance/media/security-center-sqlserver.png"::: 別のログインとして実行<br /><br /> :::image type="icon" source="../performance/media/security-center-both.png"::: 別のデータベース ユーザーとして実行|[資格情報 &#40;データベース エンジン&#41;](../../relational-databases/security/authentication-access/credentials-database-engine.md)<br /><br /> [別のログインとして実行](../../t-sql/statements/execute-as-transact-sql.md)<br /><br /> [別のデータベース ユーザーとして実行](../../t-sql/statements/execute-as-transact-sql.md)|  
  
##  <a name="authorization-what-can-you-do"></a><a name="What"></a> 認可:何を実行できるか  
  
|||  
|-|-|  
|**権限の許可、取り消し、および拒否**<br /><br /> :::image type="icon" source="../performance/media/security-center-both.png"::: セキュリティ保護可能なクラス<br /><br /> :::image type="icon" source="../performance/media/security-center-sqlserver.png"::: 詳細なサーバーのアクセス許可<br /><br /> :::image type="icon" source="../performance/media/security-center-both.png"::: 詳細なデータベースのアクセス許可|[権限の階層 &#40;データベース エンジン&#41;](../../relational-databases/security/permissions-hierarchy-database-engine.md)<br /><br /> [アクセス許可](../../relational-databases/security/permissions-database-engine.md)<br /><br /> [セキュリティ保護可能](../../relational-databases/security/securables.md)<br /><br /> [データベース エンジンの権限の概要](../../relational-databases/security/authentication-access/getting-started-with-database-engine-permissions.md)|  
|**ロールによるセキュリティ**<br /><br /> :::image type="icon" source="../performance/media/security-center-sqlserver.png"::: サーバー レベルのロール<br /><br /> :::image type="icon" source="../performance/media/security-center-both.png"::: データベース レベルのロール|[サーバーレベルのロール](../../relational-databases/security/authentication-access/server-level-roles.md)<br /><br /> [データベース レベルのロール](../../relational-databases/security/authentication-access/database-level-roles.md)|  
|**選んだデータ要素へのデータ アクセスを制限する**<br /><br /> :::image type="icon" source="../performance/media/security-center-both.png"::: ビュー/プロシージャを使用したデータ アクセスの制限<br /><br /> :::image type="icon" source="../performance/media/security-center-both.png"::: 行レベルのセキュリティ<br /><br /> :::image type="icon" source="../performance/media/security-center-both.png"::: 動的データ マスキング<br /><br /> :::image type="icon" source="../performance/media/security-center-both.png"::: 署名済みオブジェクト|[ビュー](../../relational-databases/views/views.md) と [プロシージャ](../../relational-databases/stored-procedures/stored-procedures-database-engine.md)を使用したデータ アクセスの制限<br /><br /> [行レベルのセキュリティ (SQL Server)](../../relational-databases/security/row-level-security.md)<br /><br /> [行レベルのセキュリティ (Azure SQL Database)](./row-level-security.md)<br /><br /> [動的なデータ マスキング (SQL Server)](../../relational-databases/security/dynamic-data-masking.md)<br /><br /> [動的なデータ マスキング (Azure SQL Database)](/azure/azure-sql/database/dynamic-data-masking-overview)<br /><br /> [署名済みオブジェクト](../../t-sql/statements/add-signature-transact-sql.md)|  
  
##  <a name="encryption-storing-secret-data"></a><a name="Encrypt"></a> 暗号化:秘密データの格納  
  
|||  
|-|-|  
|**ファイルの暗号化**<br /><br /> :::image type="icon" source="../performance/media/security-center-sqlserver.png"::: BitLocker 暗号化 (ドライブ レベル)<br /><br /> :::image type="icon" source="../performance/media/security-center-sqlserver.png"::: NTFS 暗号化 (フォルダー レベル)<br /><br /> :::image type="icon" source="../performance/media/security-center-both.png"::: Transparent Data Encryption (ファイル レベル)<br /><br /> :::image type="icon" source="../performance/media/security-center-both.png"::: バックアップの暗号化 (ファイル レベル)|[BitLocker (ドライブ レベル)](https://support.microsoft.com/kb/2855131)<br /><br /> [NTFS 暗号化 (フォルダー レベル)](/previous-versions/tn-archive/dd163562(v=technet.10))<br /><br /> [透過的なデータ暗号化 (ファイル レベル)](../../relational-databases/security/encryption/transparent-data-encryption.md)<br /><br /> [バックアップの暗号化 (ファイル レベル)](../../relational-databases/backup-restore/backup-encryption.md)|  
|**ソースの暗号化**<br /><br /> :::image type="icon" source="../performance/media/security-center-sqlserver.png"::: 拡張キー管理モジュール<br /><br /> :::image type="icon" source="../performance/media/security-center-sqlserver.png"::: Azure Key Vault に格納されたキー<br /><br /> :::image type="icon" source="../performance/media/security-center-both.png"::: Always Encrypted|[拡張キー管理モジュール](../../relational-databases/security/encryption/extensible-key-management-ekm.md)<br /><br /> [Azure キー コンテナーに格納されたキー](../../relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server.md)<br /><br /> [常に暗号化](../../relational-databases/security/encryption/always-encrypted-database-engine.md)|  
|**列、データ、キーの暗号化**<br /><br /> :::image type="icon" source="../performance/media/security-center-both.png"::: 証明書による暗号化<br /><br /> :::image type="icon" source="../performance/media/security-center-both.png"::: 対称キーによる暗号化<br /><br /> :::image type="icon" source="../performance/media/security-center-both.png"::: 非対称キーによる暗号化<br /><br /> :::image type="icon" source="../performance/media/security-center-both.png"::: パスフレーズによる暗号化|[証明書による暗号化](../../t-sql/functions/encryptbycert-transact-sql.md)<br /><br /> [非対称キーによる暗号化](../../t-sql/functions/encryptbyasymkey-transact-sql.md)<br /><br /> [対称キーによる暗号化](../../t-sql/functions/encryptbykey-transact-sql.md)<br /><br /> [パスフレーズによる暗号化](../../t-sql/functions/encryptbypassphrase-transact-sql.md)<br /><br /> [データの列の暗号化](../../relational-databases/security/encryption/encrypt-a-column-of-data.md)|  
  
##  <a name="connection-security-restricting-and-securing"></a><a name="Connect"></a> 接続のセキュリティ:制限と保護  
  
|||  
|-|-|  
|**ファイアウォールによる防御**<br /><br /> :::image type="icon" source="../performance/media/security-center-sqlserver.png"::: Windows ファイアウォールの設定<br /><br /> :::image type="icon" source="../../relational-databases/security/media/security-center-sqldb.png"::: Azure サービスのファイアウォール設定<br /><br /> :::image type="icon" source="../../relational-databases/security/media/security-center-sqldb.png"::: データベース ファイアウォールの設定|[データベース エンジン アクセスを有効にするための Windows ファイアウォールを構成する](../../database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access.md)<br /><br /> [Azure SQL データベースのファイアウォール設定](../../relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database.md)<br /><br /> [Azure サービスのファイアウォール設定](../../relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database.md)|  
|**転送中のデータの暗号化**<br /><br /> :::image type="icon" source="../performance/media/security-center-both.png"::: 強制 SSL 接続<br /><br /> :::image type="icon" source="../performance/media/security-center-sqlserver.png"::: SSL 接続 (オプション)|[データベース エンジンへの暗号化接続の有効化](../../database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine.md)<br /><br /> [データベース エンジンへの暗号化接続の有効化](../../database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine.md)、[ネットワーク セキュリティ](/azure/sql-database/sql-database-security-best-practice#network-security) <br /><br /> [Microsoft SQL Server の TLS 1.2 サポート](https://support.microsoft.com/kb/3135244)|  
  
##  <a name="auditing-recording-access"></a><a name="Audit"></a> 監査:アクセスの記録  
  
|||  
|-|-|  
|**自動監査**<br /><br /> :::image type="icon" source="../../relational-databases/performance/media/security-center-sqlserver.png"::: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Audit (Server と DB レベル)<br /><br /> :::image type="icon" source="../../relational-databases/security/media/security-center-sqldb.png"::: [!INCLUDE[ssSDS](../../includes/sssds-md.md)] Audit (データベース レベル)<br /><br /> :::image type="icon" source="../../relational-databases/security/media/security-center-sqldb.png"::: 脅威の検出| <br /><br /> [SQL Server Audit &#40;Database Engine&#41;](../../relational-databases/security/auditing/sql-server-audit-database-engine.md)<br /><br /> [SQL データベースの監査](/azure/azure-sql/database/auditing-overview)<br /><br /> [SQL Database の Advanced Threat Protection の概要](/azure/azure-sql/database/threat-detection-configure) <br /><br /> [SQL データベースの脆弱性評価](/azure/sql-database/sql-vulnerability-assessment) |  
|**カスタム監査**<br /><br /> :::image type="icon" source="../../relational-databases/performance/media/security-center-both.png"::: のトリガー|カスタム監査の実装:[DDL Triggers](../../relational-databases/triggers/ddl-triggers.md) と [DML Triggers](../../relational-databases/triggers/dml-triggers.md)の作成|  
|**コンプライアンス**<br /><br /> :::image type="icon" source="../../relational-databases/performance/media/security-center-both.png"::: コンプライアンス|SQL Server:<br />                        [情報セキュリティ国際評価基準](https://go.microsoft.com/fwlink/?LinkId=616319)<br /><br /> SQL データベース:<br />                        [Microsoft Azure セキュリティ センター:機能ごとのコンプライアンス](https://azure.microsoft.com/support/trust-center/services/)|  
  
##  <a name="sql-injection"></a><a name="SQLInjection"></a> SQL インジェクション  
 SQL インジェクションとは、後で [!INCLUDE[ssDE](../../includes/ssde-md.md)] に渡され解析および実行が行われる文字列に、有害なコードを挿入するという攻撃です。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、構文的に有効であれば受信したクエリがすべて実行されるため、SQL ステートメントを構成するすべてのプロシージャにおいて、インジェクションに対する脆弱性を検証する必要があります。 すべてのデータベース システムに SQL インジェクションのリスクがあり、脆弱性の多くが、 [!INCLUDE[ssDE](../../includes/ssde-md.md)]にクエリを実行するアプリケーションで確認されています。 SQL インジェクション攻撃を阻止するには、ストアド プロシージャとパラメーター化コマンドを使用し、動的 SQL を回避して、すべてのユーザーのアクセス許可を制限します。  詳細については、「 [SQL Injection](../../relational-databases/security/sql-injection.md)」を参照してください。  
  
 アプリケーション プログラマ向けのその他のリンク:  
  
-   [SQL Server におけるアプリケーション セキュリティのシナリオ](/dotnet/framework/data/adonet/sql/application-security-scenarios-in-sql-server)  
  
-   [SQL Server での安全な動的 SQL の作成](/dotnet/framework/data/adonet/sql/writing-secure-dynamic-sql-in-sql-server)  
  
-   [方法: ASP.NET で SQL インジェクションから保護する](/previous-versions/msp-n-p/ff648339(v=pandp.10))  
  
## <a name="see-also"></a>参照  
 [データベース エンジンの権限の概要](../../relational-databases/security/authentication-access/getting-started-with-database-engine-permissions.md)   
 [SQL Server の保護](../../relational-databases/security/securing-sql-server.md)   
 [プリンシパル &#40;データベース エンジン&#41;](../../relational-databases/security/authentication-access/principals-database-engine.md)   
 [SQL Server の証明書と非対称キー](../../relational-databases/security/sql-server-certificates-and-asymmetric-keys.md)   
 [SQL Server の暗号化](../../relational-databases/security/encryption/sql-server-encryption.md)   
 [セキュリティ構成](../../relational-databases/security/surface-area-configuration.md)   
 [強力なパスワード](../../relational-databases/security/strong-passwords.md)   
 [TRUSTWORTHY データベース プロパティ](../../relational-databases/security/trustworthy-database-property.md)   
 [データベース エンジンの機能とタスク](../../sql-server/what-s-new-in-sql-server-ver15.md)  
 [SQL Server の知的所有権の保護](../../relational-databases/security/protecting-your-sql-server-intellectual-property.md)   
  
[!INCLUDE[get-help-security](../../includes/get-help-security.md)]