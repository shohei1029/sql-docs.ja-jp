---
title: Linux および macOS 上の ODBC Driver for SQL Server のリリース ノート
description: Microsoft ODBC Driver for SQL Server のリリース済みバージョンにおける新機能と変更内容について学習します。
ms.custom: ''
ms.date: 01/29/2021
ms.prod: sql
ms.prod_service: connectivity
ms.reviewer: v-daenge
ms.technology: connectivity
ms.topic: conceptual
author: v-chojas
ms.author: v-chojas
manager: kenvh
ms.openlocfilehash: 3dbd2571eb468f0fcc5806a80b81828d803f2244
ms.sourcegitcommit: ecf074e374426c708073c7da88313d4915279fb9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103575248"
---
# <a name="release-notes-for-the-microsoft-odbc-driver-for-sql-server-on-linux-and-macos"></a>Linux および macOS 上の Microsoft ODBC Driver for SQL Server のリリース ノートです

[!INCLUDE[Driver_ODBC_Download](../../../includes/driver_odbc_download.md)]

この記事では、Linux および macOS 上の [!INCLUDE[msCoName](../../../includes/msconame_md.md)] ODBC ドライバー[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]のバージョン管理されたリリースの新機能を一覧で説明します。

<!--
Going forward, please use the new 2-column markdown table for each new H2 version section.
And we are keeping the H2 titles much shorter, partly by removing redundant or obvious info.
We want to track the Month YYYY each new version H2 section is added, at the end of the H2 title.
This is the new standard format for Release Notes article content.

OLD     FILE NAME:    linux-mac/release-notes.md
NOW NEW FILE NAME:    linux-mac/release-notes-odbc-sql-server-linux-mac.md

Thank you.
GeneMi.  2019/04/03.
-->

## <a name="1772-march-2021"></a>17.7.2 - 2021 年 3 月

| [新しい項目] | 詳細 |
| :------- | :------ |
| バグが修正されました。 | [バグの修正](../bug-fixes.md)。 |
| &nbsp; | &nbsp; |

## <a name="177-january-2021"></a>17.7 - 2021 年 1 月

| [新しい項目] | 詳細 |
| :------- | :------ |
| 新しいディストリビューションのサポート。 | Ubuntu 20.10、macOS Big Sur (11.0)、Oracle Linux 7 |
| サービス プリンシパル認証 | [DSN および接続文字列の属性とキーワード](../dsn-connection-string-attribute.md)に関する記事を参照してください。 |
| 暗号化された money 列と smallmoney 列に挿入する機能 | [Always Encrypted の使用](../using-always-encrypted-with-the-odbc-driver.md)に関するページを参照してください。 |
| バグが修正されました。 | [バグの修正](../bug-fixes.md)。 |
| &nbsp; | &nbsp; |

## <a name="176-july-2020"></a>17.6、2020 年 7 月

| [新しい項目] | 詳細 |
| :------- | :------ |
| 新しいディストリビューションのサポート。 | Ubuntu 20.04 |
| フェデレーション認証のサポート | [Azure Active Directory の使用](../using-azure-active-directory.md)に関するページを参照してください。 |
| 準備されたステートメントのメタデータ キャッシュ | [Always Encrypted の使用](../using-always-encrypted-with-the-odbc-driver.md)に関するページを参照してください。 |
| 自動 BEGIN TRANSACTION が ROLLBACK または COMMIT の後に行われるかどうかを制御する SQL_COPT_SS_AUTOBEGINTXN 接続属性 | [DSN および接続文字列の属性とキーワード](../dsn-connection-string-attribute.md)に関する記事を参照してください。 |
| バグが修正されました。 | [バグの修正](../bug-fixes.md)。 |
| &nbsp; | &nbsp; |

## <a name="17522-april-2020-alpine-linux-only"></a>17.5.2.2、2020 年 4 月 (Alpine Linux のみ)

| 追加された機能 | 詳細 |
| :------------ | :------ |
| バグが修正されました。 | 「[Bug fixes (バグの修正)](../bug-fixes.md)」をご覧ください。 |
| &nbsp; | &nbsp; |

## <a name="1752-march-2020"></a>17.5.2、2020 年 3 月

| 追加された機能 | 詳細 |
| :------------ | :------ |
| Managed Identity for Azure Key Vault を使用した認証のサポート | [ODBC ドライバーでの Always Encrypted の使用](../using-always-encrypted-with-the-odbc-driver.md)に関するページをご覧ください。 |
| その他の Azure Key Vault エンドポイントのサポート | [ODBC ドライバーでの Always Encrypted の使用](../using-always-encrypted-with-the-odbc-driver.md)に関するページをご覧ください。 |
| バグが修正されました。 | 「[Bug fixes (バグの修正)](../bug-fixes.md)」をご覧ください。 |
| &nbsp; | &nbsp; |

## <a name="175-january-2020"></a>17.5、2020 年 1 月

| 追加された機能 | 詳細 |
| :------------ | :------ |
| サーバーへのラウンド トリップなしで SPID を取得する SQL_COPT_SS_SPID 接続属性 | [DSN および接続文字列の属性とキーワード](../dsn-connection-string-attribute.md)に関する記事を参照してください。 |
| Debian および Ubuntu 上で `debconf` を介して EULA への同意を示すサポート | [ドライバーのインストール](./installing-the-microsoft-odbc-driver-for-sql-server.md)に関する記事を参照してください。 |
| 新しいディストリビューションのサポート。 | &bull; &nbsp; &nbsp; Alpine Linux (3.10、3.11)<br/>&bull; &nbsp; &nbsp; Oracle Linux 8<br/>&bull; &nbsp; &nbsp; Ubuntu 19.10<br/>&bull; &nbsp; &nbsp; macOS 10.15 |
| バグが修正されました。 | 「[Bug fixes (バグの修正)](../bug-fixes.md)」をご覧ください。 |
| &nbsp; | &nbsp; |

## <a name="1742-october-2019"></a>17.4.2、2019 年 10 月

| 追加された機能 | 詳細 |
| :------------ | :------ |
| その他の Azure Key Vault エンドポイントのサポート | [ODBC ドライバーでの Always Encrypted の使用](../using-always-encrypted-with-the-odbc-driver.md)に関するページをご覧ください。 |
| データ分類バージョンの設定のサポート | 「[データ分類](../data-classification.md#bkmk-version)」を参照してください。 |
| バグが修正されました。 | 「[Bug fixes (バグの修正)](../bug-fixes.md)」をご覧ください。 |
| &nbsp; | &nbsp; |

**既知の問題:**

セキュリティで保護されたエンクレーブと Azure Key Vault と共に Always Encrypted を使用し、キーのパスの長さが奇数の場合、CMK 署名検証エラーが発生する可能性があります。 この問題が発生した場合は、AKV キーの名前を変更して、キー パスの長さを 1 文字ずつ変更してみてください。

## <a name="174-august-2019"></a>17.4、2019 年 8 月

| 追加された機能 | 詳細 |
| :------------ | :------ |
| セキュリティで保護されたエンクレーブが設定された Always Encrypted | [ODBC ドライバーでの Always Encrypted の使用](../using-always-encrypted-with-the-odbc-driver.md)に関するページをご覧ください。 |
| OpenSSL の動的読み込み | [プログラミング ガイドライン](programming-guidelines.md#bkmk-openssl)に関するページをご覧ください。 |
| 構成可能な TCP キープアライブ設定。 | 「[SQL Server への接続](connection-string-keywords-and-data-source-names-dsns.md)」をご覧ください。 |
| バグが修正されました。 | 「[Bug fixes (バグの修正)](../bug-fixes.md)」をご覧ください。 |
| &nbsp; | &nbsp; |

## <a name="173-february-2019"></a>17.3、2019 年 2 月

| [新しい項目] | 詳細 |
| :------- | :------ |
| 新しいディストリビューションのサポート。 | &bull; &nbsp; &nbsp; SUSE 15<br/>&bull; &nbsp; &nbsp; Ubuntu 18.10<br/>&bull; &nbsp; &nbsp; macOS 10.14 |
| Azure Active Directory マネージド ID (システムおよびユーザー割り当て) 認証モード。 | 「[ODBC ドライバーでの Azure Active Directory の使用](../using-azure-active-directory.md)」を参照してください。 |
| Always Encrypted 列に対して入力パラメーターをストリーム配信する機能。 | 詳細については、「[Always Encrypted を使用するときの ODBC ドライバーの制限事項](../using-always-encrypted-with-the-odbc-driver.md#limitations-of-the-odbc-driver-when-using-always-encrypted)」を参照してください。 |
| XA 分散トランザクション。 | 「[XA トランザクションの使用](../use-xa-with-dtc.md)」を参照してください。<br/><br/>XA は _eXtended Architecture_ の頭字語です。これは、複数のサーバー側データ ストレージ システムにアクセスするグローバル トランザクションの実行の標準です。 |
| &nbsp; | &nbsp; |

## <a name="172-july-2018"></a>17.2、2018 年 7 月

| [新しい項目] | 詳細 |
| :------- | :------ |
| 新しいディストリビューションのサポート。 | &bull; &nbsp; &nbsp; Ubuntu 18.04 |
| Azure SQL Database と SQL Server のデータ分類。 | 「[データ分類](../data-classification.md)」を参照してください。 |
| UTF-8 サーバー エンコードのサポート。 | &nbsp; |
| `SQLBrowseConnect` | &nbsp; |
| `libcurl` への動的な依存関係。 | このバージョン以降、`libcurl` パッケージは明示的な依存関係ではなくなりました。<br/>Azure Key Vault または Azure Active Directory 認証を使用する場合は、OpenSSL または NSS 用の `libcurl` パッケージが必要です。<br/>`libcurl` に関するエラーが発生する場合は、それがインストールされていることを確認してください。 |
| 接続文字列に ConnectRetryCount キーワードと ConnectRetryInterval キーワードを含むアイドル状態の接続の回復性。 | &bull; &nbsp; &nbsp; `SQL_COPT_SS_CONNECT_RETRY_COUNT` (読み取り専用) を使用して、接続の再試行回数を取得します。<br/><br/>&bull; &nbsp; &nbsp; `SQL_COPT_SS_CONNECT_RETRY_INTERVAL` (読み取り専用) を使用して、接続の再試行間隔の長さを取得します。<br/><br/>「[Windows ODBC ドライバーの接続レジリエンシー](../windows/connection-resiliency-in-the-windows-odbc-driver.md)」を参照してください。 |
| バグが修正されました。 | [バグの修正](../bug-fixes.md)。 |
| &nbsp; | &nbsp; |

## <a name="171-march-2018"></a>17.1、2018 年 3 月

| [新しい項目] | 詳細 |
| :------- | :------ |
| `SQL_COPT_SS_CEKCACHETTL` および `SQL_COPT_SS_TRUSTEDCMKPATHS` 接続属性のサポート。 | &bull; &nbsp; &nbsp; `SQL_COPT_SS_CEKCACHETTL` を使用すると、列暗号化キーのローカル キャッシュが存在する時間の制御とフラッシュを行うことができます。<br/><br/>&bull; &nbsp; &nbsp; `SQL_COPT_SS_TRUSTEDCMKPATHS` を使用すると、アプリケーションで、指定したリストの列マスター キーのみを使用するように Always Encrypted 操作を制限できます。<br/><br/>「[SQL Server 用 ODBC ドライバーと共に Always Encrypted を使用する](../using-always-encrypted-with-the-odbc-driver.md)」を参照してください。 |
| 既定の場所から `.rll` を読み込む処理のサポート。 | [インストール ドキュメントの「リソース ファイルの読み込み」セクション](installing-the-microsoft-odbc-driver-for-sql-server.md#resource-file-loading)を参照してください。 |
| バグが修正されました。 | [バグの修正](../bug-fixes.md)。 |
| &nbsp; | &nbsp; |

## <a name="17"></a>17

**新しいディストリビューションのサポート**: macOS High Sierra および Ubuntu 17.10

**パフォーマンスの強化**:ドライバーが UTF-8 と 16 の間で変換されるときのパフォーマンスの向上が 10 倍を超えました。

**機能の追加**:

BCP API の Always Encrypted のサポート

新しい接続文字列属性 UseFMTOnly により、一時テーブルを必要とする特別なケースで以前のメタデータがドライバーで使用されます。

Azure SQL Managed Instance のサポート。

> [!NOTE]
> Managed Instance を使用するときはいくつかの相違点があります。
>
> - FILESTREAM はサポートされていません。
> - ローカル ファイル システムのアクセスはサポートされていませんが、トレース ファイルなどの場合は必要です
> - ローカル パスからの UDT の作成はサポートされていません
> - Windows 統合認証はサポートされていません
> - DTC はサポートされていません
> - 'sa' アカウントは存在しません (既定のアカウントは 'cloudSA' という名前です)
> - TDS トークン エラー (0xAA) では、正しくないサーバー名が返されます
> - データベース名の特殊文字はサポートされていません
> - ALTER DATABASE [dbname1] MODIFY NAME = [dbname2] はサポートされていません
> - 言語設定に関係なく、エラー メッセージは常に英語で表示されます (Azure と同じ)

## <a name="131-for-ssnoversion-on-linux-and-macos-may-2017"></a>13.1、Linux および macOS 上の [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]、2017 年 5 月

ODBC Driver 13.1 for [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] では、Microsoft SQL Server 2016 と組み合わせて使用される場合に、Always Encrypted および Azure Active Directory のサポートが追加されました。

**新しいディストリビューションのサポート**:OS X 10.11 および macOS 10.12 は、macOS 上の ODBC ドライバーの最初のリリースでサポートされています。 Red Hat 6、7、SUSE 12 に加え、Ubuntu 16.10 のサポートも追加されました。 各プラットフォームには、インストールと構成が容易になるプラットフォーム関連パッケージ (RPM または DEB) があります。 詳細については、[Linux](../../../connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server.md) と [macOS](../../../connect/odbc/linux-mac/install-microsoft-odbc-driver-sql-server-macos.md) での ODBC ドライバーのインストール手順を参照してください。

**unixODBC Driver Manager 2.3.1 のサポートの変更**: ODBC ドライバーは、(Red Hat 6 を除き) unixODBC Driver Manager のカスタム パッケージに依存しなくなり、代わりにディストリビューション パッケージ マネージャーを利用してディストリビューションのリポジトリの UnixODBC の依存関係を解決するようになりました。

**BCP API のサポート**:Linux および macOS ODBC ドライバーは [BCP API 関数 (**bcp_init** など)](../../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/sql-server-driver-extensions-bulk-copy-functions.md) の使用をサポートするようになりました。

## <a name="130-for-ssnoversion-on-linux"></a>13.0、Linux 上の [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]

Microsoft ODBC Driver 13.0 for SQL Server を使用することで、SQL Server 2014 および SQL Server 2016 もサポートされます。  

**新しいディストリビューションのサポート**:

Red Hat、SUSE に加え、Ubuntu のサポートが追加されました。 各プラットフォームには、インストールと構成が容易になるプラットフォーム関連パッケージ (RPM または DEB) があります。  インストール手順については、[ドライバーのインストール](../../../connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server.md)に関するページを参照してください。

**unixODBC Driver Manager 2.3.1 のサポート**:新しいドライバー マネージャーに加え、インストールと構成が容易になる、この依存関係をインストールするためのパッケージも追加されました。  

**透過的ネットワーク IP 解決**:透過的ネットワーク IP 解決は、ホスト名の最初に解決された IP が応答せず、そのホスト名に関連付けられている IP が複数ある場合に、ドライバーの接続シーケンスに影響する既存のマルチサブネット フェールオーバー機能が改訂されたものです。

**TLS 1.2 のサポート**:SQL Server との通信がセキュリティで保護されている場合、Linux 上の Microsoft ODBC Driver 13.0 for SQL Server は TLS 1.2 をサポートするようになりました。

## <a name="11-for-ssnoversion-on-linux"></a>11、Linux 上の [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]

ODBC Driver on SUSE Linux (Preview) は、64 ビット SUSE Linux Enterprise 11 Service Pack 2 をサポートします。 詳しくは、「[System Requirements](../../../connect/odbc/linux-mac/system-requirements.md)」(システム要件) をご覧ください。  

Linux 上の ODBC ドライバーは、[!INCLUDE[ssHADR](../../../includes/sshadr_md.md)]をサポートしています。 詳細については、[Linux 上の ODBC ドライバーでの高可用性とディザスター リカバリーのサポート](../../../connect/odbc/linux-mac/odbc-driver-on-linux-support-for-high-availability-disaster-recovery.md)に関するページを参照してください。  

Linux での ODBC ドライバーは、Azure SQL Database への接続をサポートします。

`-l` オプション (ログインのタイムアウト) が `bcp` に追加されました。 詳しくは、「[Connecting with **bcp**](../../../connect/odbc/linux-mac/connecting-with-bcp.md)」(bcp による接続) をご覧ください。
