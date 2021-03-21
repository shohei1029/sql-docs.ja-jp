---
title: ODBC Driver for SQL Server のダウンロード
description: SQL Server や Azure SQL Database に接続するネイティブ コード アプリケーションを開発するには、Microsoft ODBC Driver for SQL Server をダウンロードします。
ms.date: 01/29/2021
ms.prod: sql
ms.prod_service: connectivity
ms.reviewer: ''
ms.technology: connectivity
ms.topic: conceptual
ms.assetid: 53b09784-bb9d-4fd4-99d3-0492b3308ac4
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: c74f1d0d42b339a51254e582023d15a69d9891af
ms.sourcegitcommit: ecf074e374426c708073c7da88313d4915279fb9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103575306"
---
# <a name="download-odbc-driver-for-sql-server"></a>ODBC Driver for SQL Server のダウンロード

[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

Microsoft ODBC Driver for SQL Server は、ネイティブ コード API を使用して SQL Server に接続するアプリケーション用のランタイム サポートが含まれる単一のダイナミックリンク ライブラリ (DLL) です。 Microsoft ODBC Driver 17 for SQL Server は、新しいアプリケーションを作成したり、SQL Server の新機能を利用する必要のある既存のアプリケーションを拡張したりする場合に使用します。

## <a name="download-for-windows"></a>Windows 用のダウンロード

Microsoft ODBC Driver 17 for SQL Server の再頒布可能インストーラーでは、新しい SQL Server 機能を利用するために実行時に必要なクライアント コンポーネントがインストールされます。 必要に応じて、ODBC API を使用するアプリケーションの開発に必要なヘッダー ファイルをインストールします。 バージョン 17.4.2 以降のインストーラーには、Microsoft Active Directory 認証ライブラリ (ADAL.dll) も含まれていてインストールされます。

最新の一般提供 (GA) バージョンはバージョン 17.7.2 です。 以前のバージョンの Microsoft ODBC Driver 17 for SQL Server がインストールされている場合は、17.7.2 をインストールすると 17.7.2 にアップグレードされます。

**[![ダウンロード](../../ssms/media/download-icon.png) Microsoft ODBC Driver 17 for SQL Server (x64) のダウンロード](https://go.microsoft.com/fwlink/?linkid=2156851)**  
**[![ダウンロード](../../ssms/media/download-icon.png) Microsoft ODBC Driver 17 for SQL Server (x86) のダウンロード](https://go.microsoft.com/fwlink/?linkid=2156749)**  

### <a name="version-information"></a>バージョン情報

- リリース番号: 17.7.2.1
- リリース日: 2021 年 3 月 10 日

> [!Note]
> 英語以外のバージョンからこのページにアクセスしていて、最新の内容を見たい場合は、[サイトの英語 (米国) 版](https://aka.ms/downloadmsodbcsqlenglish)をご覧ください。 [使用できる言語](#available-languages)を選択して、英語 (米国) 版のサイトから別の言語をダウンロードできます。ます。

## <a name="available-languages"></a>使用できる言語

Microsoft ODBC Driver for SQL Server のこのリリースは、次の言語でインストールできます。

Microsoft ODBC Driver 17.7.2 for SQL Server (x64):  
[簡体中国語](https://go.microsoft.com/fwlink/?linkid=2156851&clcid=0x804) | [繁体中国語](https://go.microsoft.com/fwlink/?linkid=2156851&clcid=0x404) | [英語 (米国)](https://go.microsoft.com/fwlink/?linkid=2156851&clcid=0x409) | [フランス語](https://go.microsoft.com/fwlink/?linkid=2156851&clcid=0x40c) | [ドイツ語](https://go.microsoft.com/fwlink/?linkid=2156851&clcid=0x407) | [イタリア語](https://go.microsoft.com/fwlink/?linkid=2156851&clcid=0x410) | [日本語](https://go.microsoft.com/fwlink/?linkid=2156851&clcid=0x411) | [韓国語](https://go.microsoft.com/fwlink/?linkid=2156851&clcid=0x412) | [ポルトガル語 (ブラジル)](https://go.microsoft.com/fwlink/?linkid=2156851&clcid=0x416) | [ロシア語](https://go.microsoft.com/fwlink/?linkid=2156851&clcid=0x419) | [スペイン語](https://go.microsoft.com/fwlink/?linkid=2156851&clcid=0x40a)

Microsoft ODBC Driver 17.7.2 for SQL Server (x86):  
[簡体中国語](https://go.microsoft.com/fwlink/?linkid=2156749&clcid=0x804) | [繁体中国語](https://go.microsoft.com/fwlink/?linkid=2156749&clcid=0x404) | [英語 (米国)](https://go.microsoft.com/fwlink/?linkid=2156749&clcid=0x409) | [フランス語](https://go.microsoft.com/fwlink/?linkid=2156749&clcid=0x40c) | [ドイツ語](https://go.microsoft.com/fwlink/?linkid=2156749&clcid=0x407) | [イタリア語](https://go.microsoft.com/fwlink/?linkid=2156749&clcid=0x410) | [日本語](https://go.microsoft.com/fwlink/?linkid=2156749&clcid=0x411) | [韓国語](https://go.microsoft.com/fwlink/?linkid=2156749&clcid=0x412) | [ポルトガル語 (ブラジル)](https://go.microsoft.com/fwlink/?linkid=2156749&clcid=0x416) | [ロシア語](https://go.microsoft.com/fwlink/?linkid=2156749&clcid=0x419) | [スペイン語](https://go.microsoft.com/fwlink/?linkid=2156749&clcid=0x40a)

### <a name="release-notes-for-windows"></a>Windows 用リリース ノート

Windows でのこのリリースの詳細については、[Windows のリリース ノート](windows\release-notes-odbc-sql-server-windows.md)に関するページをご覧ください。

### <a name="previous-releases-for-windows"></a>Windows の以前のリリース

Windows 用の以前のリリースをダウンロードするには、[以前の Microsoft ODBC Driver for SQL Server のリリース](windows\release-notes-odbc-sql-server-windows.md#previous-releases)に関する記事を参照してください。

## <a name="download-for-linux-and-macos"></a>Linux および macOS 用のダウンロード

Microsoft ODBC Driver for SQL Server は、関連するインストール手順に従い、Linux および macOS 用のパッケージ マネージャーを使用して、ダウンロードおよびインストールできます。  
[ODBC Driver for SQL Server をインストールする (Linux)](linux-mac\installing-the-microsoft-odbc-driver-for-sql-server.md)  
[ODBC Driver for SQL Server をインストールする (macOS)](linux-mac\install-microsoft-odbc-driver-sql-server-macos.md)  

オフライン インストール用のパッケージをダウンロードする必要がある場合は、次のリンクからすべてのバージョンを入手できます。

> [!Note]
> `msodbcsql17-*` という名前のパッケージが最新バージョンです。 `msodbcsql-*` という名前のパッケージは、バージョン 13 のドライバーです。

### <a name="alpine"></a>Alpine

- [17.7.2.1 Alpine パッケージ](https://download.microsoft.com/download/e/4/e/e4e67866-dffd-428c-aac7-8d28ddafb39b/msodbcsql17_17.7.2.1-1_amd64.apk) ([PGP 署名](https://download.microsoft.com/download/e/4/e/e4e67866-dffd-428c-aac7-8d28ddafb39b/msodbcsql17_17.7.2.1-1_amd64.sig))
- [17.7.1.1 Alpine パッケージ](https://download.microsoft.com/download/e/4/e/e4e67866-dffd-428c-aac7-8d28ddafb39b/msodbcsql17_17.7.1.1-1_amd64.apk) ([PGP 署名](https://download.microsoft.com/download/e/4/e/e4e67866-dffd-428c-aac7-8d28ddafb39b/msodbcsql17_17.7.1.1-1_amd64.sig))
- [17.6.1.1 Alpine パッケージ](https://download.microsoft.com/download/e/4/e/e4e67866-dffd-428c-aac7-8d28ddafb39b/msodbcsql17_17.6.1.1-1_amd64.apk) ([PGP 署名](https://download.microsoft.com/download/e/4/e/e4e67866-dffd-428c-aac7-8d28ddafb39b/msodbcsql17_17.6.1.1-1_amd64.sig))
- [17.5.2.2 Alpine パッケージ](https://download.microsoft.com/download/e/4/e/e4e67866-dffd-428c-aac7-8d28ddafb39b/msodbcsql17_17.5.2.2-1_amd64.apk) ([PGP 署名](https://download.microsoft.com/download/e/4/e/e4e67866-dffd-428c-aac7-8d28ddafb39b/msodbcsql17_17.5.2.2-1_amd64.sig))
- [17.5.2.1 Alpine パッケージ](https://download.microsoft.com/download/e/4/e/e4e67866-dffd-428c-aac7-8d28ddafb39b/msodbcsql17_17.5.2.1-1_amd64.apk) ([PGP 署名](https://download.microsoft.com/download/e/4/e/e4e67866-dffd-428c-aac7-8d28ddafb39b/msodbcsql17_17.5.2.1-1_amd64.sig))
- [17.5.1.1 Alpine パッケージ](https://download.microsoft.com/download/e/4/e/e4e67866-dffd-428c-aac7-8d28ddafb39b/msodbcsql17_17.5.1.1-1_amd64.apk) ([PGP 署名](https://download.microsoft.com/download/e/4/e/e4e67866-dffd-428c-aac7-8d28ddafb39b/msodbcsql17_17.5.1.1-1_amd64.sig))

### <a name="debian"></a>Debian

- [Debian 10 .deb パッケージ](https://packages.microsoft.com/debian/10/prod/pool/main/m/msodbcsql17/)
- [Debian 9 .deb パッケージ](https://packages.microsoft.com/debian/9/prod/pool/main/m/msodbcsql17/)
- [Debian 8 .deb パッケージ](https://packages.microsoft.com/debian/8/prod/pool/main/m/msodbcsql17/)
- [Debian 8 .deb パッケージ (msodbcsql 13.x)](https://packages.microsoft.com/debian/8/prod/pool/main/m/msodbcsql/)

### <a name="red-hat"></a>Red Hat

- [Red Hat 8 .rpm パッケージ](https://packages.microsoft.com/rhel/8/prod/)
- [Red Hat 7 .rpm パッケージ](https://packages.microsoft.com/rhel/7/prod/)
- [Red Hat 6 .rpm パッケージ](https://packages.microsoft.com/rhel/6/prod/)

### <a name="suse"></a>Suse

- [SuSE 15 .rpm パッケージ](https://packages.microsoft.com/sles/15/prod/)
- [SuSE 12 .rpm パッケージ](https://packages.microsoft.com/sles/12/prod/)
- [SuSE 11 .rpm パッケージ](https://packages.microsoft.com/sles/11/prod/)

### <a name="ubuntu"></a>Ubuntu

- [Ubuntu 20.10 .deb パッケージ](https://packages.microsoft.com/ubuntu/20.10/prod/pool/main/m/msodbcsql17/)
- [Ubuntu 20.04 .deb パッケージ](https://packages.microsoft.com/ubuntu/20.04/prod/pool/main/m/msodbcsql17/)
- [Ubuntu 18.04 .deb パッケージ](https://packages.microsoft.com/ubuntu/18.04/prod/pool/main/m/msodbcsql17/)
- [Ubuntu 16.04 .deb パッケージ](https://packages.microsoft.com/ubuntu/16.04/prod/pool/main/m/msodbcsql17/)
- [Ubuntu 14.04 .deb パッケージ](https://packages.microsoft.com/ubuntu/14.04/prod/pool/main/m/msodbcsql17/)
- [Ubuntu 16.04 .deb パッケージ (msodbcsql 13.x)](https://packages.microsoft.com/ubuntu/16.04/prod/pool/main/m/msodbcsql/)
- [Ubuntu 14.04 .deb パッケージ (msodbcsql 13.x)](https://packages.microsoft.com/ubuntu/14.04/prod/pool/main/m/msodbcsql/)

[Linux ドライバーのインストール](linux-mac/installing-the-microsoft-odbc-driver-for-sql-server.md)に関する記事も参照してください。

### <a name="macos"></a>macOS

- 詳細については、「[Homebrew 式](https://github.com/Microsoft/homebrew-mssql-release)」を参照してください。

[macOS ドライバーのインストール](linux-mac/install-microsoft-odbc-driver-sql-server-macos.md)に関する記事も参照してください。

### <a name="older-linux-releases"></a>古い Linux リリース

- **Red Hat Enterprise Linux 5 および 6 (64 ビット)**  - [Microsoft ODBC Driver 11 for SQL Server をダウンロードする - Red Hat Linux](https://go.microsoft.com/fwlink/?LinkId=267321)  
- **SUSE Linux Enterprise 11 Service Pack 2 (64 ビット)**  - [Microsoft ODBC Driver 11 Preview for SQL Server - SUSE Linux](https://go.microsoft.com/fwlink/?LinkId=264916)

### <a name="release-notes-for-linux-and-macos"></a>Linux および macOS のリリース ノート

Linux および macOS のリリースの詳細については、[Linux と macOS のリリース ノート](linux-mac\release-notes-odbc-sql-server-linux-mac.md)に関する記事を参照してください。
