---
title: Microsoft Drivers for PHP のリリース ノート
description: このページでは、Microsoft Drivers for PHP for SQL Server の各バージョンで変更された内容について説明します。
ms.custom: ''
ms.date: 01/29/2021
ms.prod: sql
ms.prod_service: connectivity
ms.reviewer: ''
ms.technology: connectivity
ms.topic: conceptual
helpviewer_keywords:
- what's new in version 1.1
ms.assetid: 91cca3d2-ba99-4a6d-b0de-beb9699cb3f8
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: c6960778fcbd7eb707b50e18ce4a0c70e477ff28
ms.sourcegitcommit: ecf074e374426c708073c7da88313d4915279fb9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103575239"
---
# <a name="release-notes-for-the-microsoft-drivers-for-php-for-sql-server"></a>Microsoft SQL Server 用 Drivers for PHP のリリース ノート

このページでは、[!INCLUDE[ssDriverPHP](../../includes/ssdriverphp_md.md)] の各バージョンに追加された機能について説明します。  

<!--
Hello, We are standardizing the format of content inside our Release Notes (or What's New) articles.
Instead of bullets (or paragraphs), we have shifted to the 2-column format you see for H2 **What's New in Version 5.6**.
It is not necessary to reformat all the older H2 sections in this Release Notes file, but.....

Going forward, please be sure to use the 2-column format.

Also, all Release Notes .md file names now must begin with 'release-notes-*.md'.  And no filler words.
The 5.6 edition of this file is being renamed.....
FROM:  'release-notes-for-the-php-sql-driver.md'
TO  :  'release-notes-php-sql-driver.md'

For any questions, ask GeneMi or CraigG.
Thanks a lot.  2019-03-28  (DevO= 1467988)
-->

## <a name="59"></a>5.9

![ダウンロード](../../ssms/media/download-icon.png) [Windows パッケージのダウンロード](https://go.microsoft.com/fwlink/?linkid=?????)
[GitHub リリース タグ (こちらから Linux と macOS のパッケージを入手できます)](https://github.com/Microsoft/msphpsql/releases/tag/v5.9.0)

### <a name="version-information"></a>バージョン情報

- リリース番号:5.9.0<sup>1</sup>
- リリース日:2021 年 1 月 29 日

### <a name="whats-new-in-59"></a>5\.9 の新機能

| [新しい項目] | 詳細 |
| :------- | :------ |
| PHP 8.0 のサポートが追加されました。 | &nbsp; |
| PHP 7.2 のサポートを終了しました。 | &nbsp; |
| すべてのプラットフォーム上で Microsoft ODBC Driver 17.7 のサポートが追加されました。 | &nbsp; |
| macOS Big Sur、Ubuntu 20.04、Ubuntu 20.10 および Alpine 3.12 のサポートが追加されました。 | ODBC Driver 17.5 以降が必要になるものもあります。 |
| macOS High Sierra、Debian 8、および Ubuntu 19.10 のサポートを終了しました。 | &nbsp; |
| GB18030 ロケールのサポート。 | &nbsp; |
| 追加の ODBC メッセージ (使用可能な場合) を含めるために PDO `errorinfo` が拡張されました。 | &nbsp; |
| 順位情報を使用したデータ分類のサポート。 | SQL Server 2019 および ODBC Driver 17.4.2 以降が必要になります。 |
| Azure Active Directory サービス プリンシパル認証のサポートが追加されました。 | ODBC ドライバー 17.7 以降が必要です。 |
| 入力または出力として 10 進数を処理するときのパフォーマンスが向上し、数値の不要な変換が行われないようになりました。 | &nbsp; |
| クライアント バッファーを使用して数値をフェッチするときのパフォーマンスが向上しました。 | &nbsp; |
| ロック タイムアウトを使用せずにクエリ タイムアウトを設定します。これにより、サーバーに必要以上に接続せずに済みます。 | &nbsp; |
| &nbsp; | &nbsp; |

<sup>1</sup> このリリースには ODBC Driver 17.4.2 以降が必要です。 これがないと、属性の設定に失敗したことに関する警告が示されます。 この警告は、古い ODBC ドライバーの使用時に抑制されることがあります。 SQLSRV を使用する場合は、「[方法: SQLSRV ドライバーを使用してエラーおよび警告処理を構成する](./how-to-configure-error-and-warning-handling-using-the-sqlsrv-driver.md)」を参照してください。 PDO_SQLSRV を使用する場合、警告は既定で抑制されますが、ログに記録することはできます。 詳細については、「[アクティビティのログ記録](./logging-activity.md)」を確認してください。

## <a name="581"></a>5.8.1

このリリースは、Linux と macOS にのみ適用されます。

[GitHub リリース タグ (こちらから Linux と macOS のパッケージが入手できます)](https://github.com/Microsoft/msphpsql/releases/tag/v5.8.1)

### <a name="version-information"></a>バージョン情報

- リリース番号:5.8.1
- リリース日:2020 年 4 月 15 日

### <a name="whats-new-in-581"></a>5\.8.1 の新機能

| [新しい項目] | 詳細 |
| :------- | :------ |
| バグの修正 | Alpine Linux の既定のロケールの問題を修正しました。 |
| バグの修正 | Alpine Linux でクライアント側のカーソル機能をサポートするために不要なデータ構造を削除しました。 |
| バグの修正 | Alpine Linux で両方のドライバーが有効になっている場合のログ記録に関する問題を修正しました。 |
| &nbsp; | &nbsp; |

## <a name="58"></a>5.8

![ダウンロード](../../ssms/media/download-icon.png) [Windows パッケージのダウンロード](https://go.microsoft.com/fwlink/?linkid=2120362)  
[GitHub リリース タグ (こちらから Linux と macOS のパッケージが入手できます)](https://github.com/Microsoft/msphpsql/releases/tag/v5.8.0)

### <a name="version-information"></a>バージョン情報

- リリース番号:5.8.0
- リリース日:2020 年 1 月 31 日

### <a name="whats-new-in-58"></a>5\.8 の新機能

| [新しい項目] | 詳細 |
| :------- | :------ |
| PHP 7.4 のサポートが追加されました。 | &nbsp; |
| PHP 7.1 のサポートを終了しました。 | &nbsp; |
| すべてのプラットフォーム上で Microsoft ODBC Driver 17.5 のサポートが追加されました。 | &nbsp; |
| Debian 10 および Red Hat 8 のサポートが追加されました。 | 両方とも、ODBC Driver 17.4 以降を必要とします。 |
| macOS Catalina、Alpine Linux 3.11<sup>1</sup>、および Ubuntu 19.10 のサポートが追加されました。 | すべて、ODBC Driver 17.5 以降を必要とします。 |
| SQL Server 2008 R2、macOS Sierra、Ubuntu 18.10、および Ubuntu 19.04 のサポートを終了しました。 | &nbsp; |
| SQL Server に接続するときの言語オプションがサポートされます。 | &nbsp; |
| PHP 7.2 で導入された PHP 拡張文字列型がサポートされます。 | &nbsp; |
| データ分類の秘密度メタデータの取得がサポートされます。 | SQL Server 2019 および ODBC Driver 17.4.2 以降が必要になります。 |
| セキュリティで保護されたエンクレーブが設定された Always Encrypted をサポートします。 | ODBC Driver 17.4 以降が必要になります。 |
| Linux および macOS でのロケール設定用の構成可能なオプションがサポートされます。 |
| フェッチ時にメタデータをキャッシュし、冗長な呼び出しを省略することで、パフォーマンスが向上しました。 | &nbsp; |
| &nbsp; | &nbsp; |

<sup>1</sup> Alpine Linux サポートは、バージョン 5.8 の試験段階です。

## <a name="previous-releases"></a>以前のリリース

## <a name="561"></a>5.6.1

![ダウンロード](../../ssms/media/download-icon.png) [Windows パッケージのダウンロード](https://go.microsoft.com/fwlink/?linkid=2120446)  
[GitHub リリース タグ (こちらから Linux と macOS のパッケージが入手できます)](https://github.com/Microsoft/msphpsql/releases/tag/v5.6.1)

### <a name="version-information"></a>バージョン情報

- リリース番号:5.6.1
- リリース日:2019 年 3 月 19 日

### <a name="whats-new-in-561"></a>5\.6.1 の新機能

| [新しい項目] | 詳細 |
| :------- | :------ |
| バグの修正 | フィールドまたは列のメタデータを計算するときに作成される前提条件が、アプリケーションの終了を引き起こす可能性がある問題を修正しました。 |
| バグの修正 | sqlsrv config ファイルを変更して、pdo_sqlsrv とは別にコンパイルできるようにしました。 |
| バグの修正 | 問題が発生した場合に false を返すように PDOStatement::getColumnMeta() を修正しました。 |
| &nbsp; | &nbsp; |

## <a name="56"></a>5.6

![ダウンロード](../../ssms/media/download-icon.png) [Windows パッケージのダウンロード](https://go.microsoft.com/fwlink/?linkid=2120450)  
[GitHub リリース タグ (こちらから Linux と macOS のパッケージが入手できます)](https://github.com/Microsoft/msphpsql/releases/tag/v5.6.0)

### <a name="version-information"></a>バージョン情報

- リリース番号:5.6.0
- リリース日:2019 年 2 月 21 日

### <a name="whats-new-in-56"></a>5\.6 の新機能

| [新しい項目] | 詳細 |
| :------- | :------ |
| PHP 7.3 のサポート。 | &nbsp; |
| PHP 7.0 のサポートを終了しました。 | &nbsp; |
| すべてのプラットフォーム上で Microsoft ODBC Driver 17.3 がサポートされます。 | &nbsp; |
| macOS Mojave のサポート。 | ODBC Driver 17.3 以降が必要になります。 |
| Ubuntu 18.10 および Suse Linux 15 のサポート。 | 両方とも、ODBC Driver 17.3 以降を必要とします。 |
| Linux Ubuntu 17.10 および macOS El Capitan のサポートを終了しました。 | &nbsp; |
| Azure AD アクセス トークンのサポート。 | Linux および macOS では、ODBC Driver 17.2 以降および unixODBC 2.3.6 以降が必要になります。 |
| Azure リソース用のマネージド ID を使用した Azure AD による認証がサポートされます。 | ODBC Driver 17.3 以降が必要になります。 |
| 新しいフェッチ機能 | &bull; &nbsp; pdo_sqlsrv から datetime をオブジェクトとして返すための新しい PDO::SQLSRV_ATTR_FETCHES_DATETIME_TYPE フラグ。<br/><br/>&bull; &nbsp; ReturnDatesAsStrings オプションを sqlsrv のステートメント レベルに追加します。<br/><br/>&bull; &nbsp; フェッチされた結果内で 10 進数を書式設定するための、両方のドライバー用の接続レベルおよびステートメント レベルでの新しいオプション。 |
| ユーザーがソースからビルドすることを選んだ場合に、ドライバーの静的コンパイルがサポートされます。 | &nbsp; |
| フェッチ時にメタデータをキャッシュし、Unicode 文字列の変換を高速化することで、パフォーマンスが向上しました。 | &nbsp; |
| &nbsp; | &nbsp; |

## <a name="53"></a>5.3

![ダウンロード](../../ssms/media/download-icon.png) [Windows パッケージのダウンロード](https://go.microsoft.com/fwlink/?linkid=2120447)  
[GitHub リリース タグ (こちらから Linux と macOS のパッケージが入手できます)](https://github.com/Microsoft/msphpsql/releases/tag/v5.3.0)

### <a name="version-information"></a>バージョン情報

- リリース番号:5.3.0
- リリース日:2018 年 7 月 20 日

### <a name="whats-new-in-53"></a>5\.3 の新機能

- すべてのプラットフォーム上で Microsoft ODBC Driver 17.2 がサポートされます
- macOS High Sierra のサポート (ODBC Driver 17 以降が必要になります)
- [SQL Server 用 PHP ドライバーと共に Always Encrypted を使用して](using-always-encrypted-php-drivers.md)、サポート対象のすべての Windows、Linux、または macOS プラットフォーム上で Always Encrypted 機能を利用可能にする基本の CRUD 機能に対して、Always Encrypted 対応の Azure Key Vault がサポートされます
- Ubuntu 18.04 LTS がサポートされます (ODBC Driver 17.2 が必要になります)
- Linux または macOS での接続の回復性もサポートされます (ODBC Driver 17.2 が必要になります)

## <a name="52"></a>5.2

![ダウンロード](../../ssms/media/download-icon.png) [Windows パッケージのダウンロード](https://go.microsoft.com/fwlink/?linkid=2120451)  
[GitHub リリース タグ (こちらから Linux と macOS のパッケージが入手できます)](https://github.com/Microsoft/msphpsql/releases/tag/v5.2.0)

### <a name="version-information"></a>バージョン情報

- リリース番号:5.2.0
- リリース日:2018 年 3 月 23 日

### <a name="whats-new-in-52"></a>5\.2 の新機能

- Windows 上では PHP 7.2.1 以降、他のプラットフォーム上では 7.2.0 以降がサポートされます
- Microsoft ODBC Driver 17 のサポート
  - すべてのプラットフォーム上でバージョン 17 が既定になりました
- Ubuntu 17.10、Debian 9、および Suse Enterprise Linux 12 のサポート
- Ubuntu 15.10 のサポートを終了しました
- Windows 上で CRUD 機能を利用した Always Encrypted がサポートされます。 詳しくは、「[SQL Server 用 PHP ドライバーと共に Always Encrypted を使用する](using-always-encrypted-php-drivers.md)」をご覧ください
  - Windows 証明書ストアのサポート
  - Always Encrypted は、Microsoft ODBC Driver 17 以降のみでサポートされます
- Linux および macOS 上で UTF8 以外のロケールがサポートされます
  - Linux および macOS 上での UTF8 以外のロケールは、Microsoft ODBC Driver 17 以降のみでサポートされます
- Azure Synapse Analytics のサポート
- Azure SQL Managed Instance のサポート

## <a name="43"></a>4.3

![ダウンロード](../../ssms/media/download-icon.png) [Windows パッケージのダウンロード](https://go.microsoft.com/fwlink/?linkid=2120616)  
[GitHub リリース タグ (こちらから Linux と macOS のパッケージが入手できます)](https://github.com/Microsoft/msphpsql/releases/tag/v4.3.0)

### <a name="version-information"></a>バージョン情報

- リリース番号:4.3.0
- リリース日:2017 年 7 月 6 日

### <a name="whats-new-in-43"></a>4\.3 の新機能

- PHP 7.1 のサポート
- macOS Sierra および macOS El Capitan のサポート
- Ubuntu 15.10 および Debian 8 のサポート
- Ubuntu 15.04 のサポートを終了しました
- 透過的なネットワーク IP の解決を利用した Always On 可用性グループがサポートされます。 詳細については、「 [Connection Options](connection-options.md)」を参照してください。
- 制限付きの sql_variant データ型のサポートが追加されました。
- Windows での、アイドル状態の接続の回復性のサポート。 詳細については、「 [Connection Options](connection-options.md)」を参照してください。
- Linux と macOS 向けの接続プールのサポート。 詳細については、[接続プール](connection-pooling-microsoft-drivers-for-php-for-sql-server.md)に関するページを参照してください。
- ActiveDirectoryPassword および SqlPassword を利用した Azure Active Directory Authentication がサポートされます。 詳細については、「 [Connection Options](connection-options.md)」を参照してください。

## <a name="40"></a>4.0

![ダウンロード](../../ssms/media/download-icon.png) [Windows パッケージのダウンロード](https://go.microsoft.com/fwlink/?linkid=2120448)  
[GitHub リリース タグ](https://github.com/microsoft/msphpsql/releases/tag/v4.0-RTW)

### <a name="version-information"></a>バージョン情報

- リリース番号:4.0
- リリース日:2016 年 7 月 1 日

### <a name="whats-new-in-40"></a>4\.0 の新機能

- PHP 7.0 のサポート  
- 完全な 64 ビットのサポート
- Ubuntu 15.04、Ubuntu 16.04、および Red Hat 7 のサポート

## <a name="32"></a>3.2

![ダウンロード](../../ssms/media/download-icon.png) [Windows パッケージのダウンロード](https://go.microsoft.com/fwlink/?linkid=2120449)  
[GitHub リリース タグ](https://github.com/microsoft/msphpsql/releases/tag/v3.2.0.0)

### <a name="version-information"></a>バージョン情報

- リリース番号:3.2
- リリース日:2015 年 3 月 9 日

### <a name="whats-new-in-32"></a>3\.2 の新機能

- PHP 5.6 のサポート  
- 旧バージョンの PHP 5.5 および 5.4 最新の更新プログラムが含まれています  
- Microsoft SQL Server 用 ODBC Driver 11 が必要です  

## <a name="31"></a>3.1

![ダウンロード](../../ssms/media/download-icon.png) [Windows パッケージのダウンロード](https://go.microsoft.com/fwlink/?linkid=2143027)  
[GitHub リリース タグ](https://github.com/microsoft/msphpsql/releases/tag/v3.1.0.0)

### <a name="version-information"></a>バージョン情報

- リリース番号:3.1
- リリース日:2014 年 12 月 12 日

### <a name="whats-new-in-31"></a>3\.1 の新機能

- PHP 5.5 のサポート  
- Microsoft SQL Server 用 ODBC Driver 11 が必要です。 以前のバージョンでは、SQL Native Client が必要でした。  

## <a name="30"></a>3.0

![ダウンロード](../../ssms/media/download-icon.png) [Windows パッケージのダウンロード](https://go.microsoft.com/fwlink/?linkid=2143026)  

### <a name="whats-new-in-30"></a>3\.0 の新機能  

- PHP 5.4 のサポート  [!INCLUDE[ssDriverPHP](../../includes/ssdriverphp_md.md)]のバージョン 3 では、PHP 5.2 はサポートされていません。  
- AttachDBFileName 接続オプションが追加されています。 詳細については、「 [Connection Options](connection-options.md)」を参照してください。  
- [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] で追加された LocalDB のサポート。 詳細については、「[LocalDB のサポート](php-driver-for-sql-server-support-for-localdb.md)」を参照してください。
- AttachDBFileName 接続オプションが追加されています。 詳細については、「 [Connection Options](connection-options.md)」を参照してください。  
- 高可用性のディザスター リカバリー機能のサポート。 詳細については、「[高可用性、障害復旧のサポート](php-driver-for-sql-server-support-for-high-availability-disaster-recovery.md)」をご覧ください。
- クライアント側のカーソル (結果セットのメモリ内キャッシュ) のサポート。 詳細については、「[カーソルの種類 &#40;SQLSRV ドライバー&#41;](cursor-types-sqlsrv-driver.md)」および「[カーソルの種類 &#40;PDO_SQLSRV ドライバー&#41;](cursor-types-pdo-sqlsrv-driver.md)」を参照してください。
- PDO::ATTR_EMULATE_PREPARES 属性が追加されました。 詳細については、「[PDO::prepare](pdo-prepare.md)」をご覧ください。  

## <a name="20"></a>2.0

### <a name="whats-new-in-20"></a>2\.0 の新機能

バージョン 2.0 では、PDO_SQLSRV ドライバーのサポートが追加されました。 詳細については、「 [PDO_SQLSRV ドライバー リファレンス](pdo-sqlsrv-driver-reference.md)」を参照してください。  

## <a name="see-also"></a>参照

[Microsoft SQL Server 用 Drivers for PHP の概要](overview-of-the-php-sql-driver.md)