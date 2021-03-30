---
description: JDBC ドライバーを使用してアプリケーションを開発する際のアプリケーションのセキュリティと java ポリシーのアクセス許可について説明します。
title: アプリケーションのセキュリティ
ms.custom: ''
ms.date: 08/12/2019
ms.prod: sql
ms.prod_service: connectivity
ms.reviewer: ''
ms.technology: connectivity
ms.topic: conceptual
ms.assetid: 940879b4-aa0f-41ce-a369-6cfc0e78e01d
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: 03f20db06095da4c805a349c8be2b1a70afc71d4
ms.sourcegitcommit: 524a0f0cc9533188f4b14d2e78ba1cfe816b3b9a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2021
ms.locfileid: "105633273"
---
# <a name="application-security"></a>アプリケーションのセキュリティ

[!INCLUDE[Driver_JDBC_Download](../../includes/driver_jdbc_download.md)]

[!INCLUDE[jdbcNoVersion](../../includes/jdbcnoversion_md.md)] を使用するときは、アプリケーションのセキュリティを確保するための対策を講じることが重要です。 以下のセクションでは、アプリケーションをセキュリティ保護するために実行できる手順に関する情報を提供します。

## <a name="using-java-policy-permissions"></a>Java のポリシー アクセス許可の使用

[!INCLUDE[jdbcNoVersion](../../includes/jdbcnoversion_md.md)] を使用するときは、JDBC ドライバーに必要な Java のポリシー アクセス許可を指定することが重要です。 Java Runtime Environment (JRE) には、広範なセキュリティ モデルが用意されています。 このモデルは、リソースにアクセスできるスレッドであかどうかを判断するために、実行時に使用できます。 セキュリティ ポリシー ファイルでこのアクセスを制御することができます。 ポリシー ファイル自体は、コンテナーの配置担当者とシステム管理者によって管理されます。 この記事に一覧されているアクセス許可は、JDBC ドライバーの機能に影響を与えるものです。

ポリシー ファイルの一般的なアクセス許可は、次のようになっています。

```config
// Example policy file entry.
grant [signedBy <signer>,] [codeBase <code source>] {
   permission  <class>  [<name> [, <action list>]];
};
```

 付与する特権の数を最小限に抑えるために、次のコードベースは JDBC ドライバーのコードベースに限定してください。

```config
grant codeBase "file:/install_dir/lib/-" {

// Grant access to data source.
permission java.util.PropertyPermission "java.naming.*", "read,write";

// Specify which hosts can be connected to.
permission java.net.socketPermission "host:port", "connect";

// Logger permission to take advantage of logging.
permission java.util.logging.LoggingPermission;

// Grant listen/connect/accept permissions to the driver if
// connecting to a named instance as the client driver.
// This connects to a udp service and listens for a response.
permission java.net.SocketPermission "*", "listen, connect, accept";
};
```

> [!NOTE]
> コード "file:/install_dir/lib/-" は、JDBC ドライバーのインストール ディレクトリを指します。

## <a name="protecting-server-communication"></a>サーバーとの通信の保護

JDBC ドライバーを使用して [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] データベースと通信する場合は、通信チャネルをセキュリティで保護することが重要です。 インターネット プロトコル セキュリティ (IPSec) または TLS (トランスポート層セキュリティ) (以前の SSL (Secure Sockets Layer)) のいずれかを使用して、あるいは両方を使用してチャネルをセキュリティで保護することができます。

TLS のサポートを使用すれば、IPSec 以外の追加の保護レベルを提供できます。 TLS の使用方法の詳細については、[暗号化の使用](using-ssl-encryption.md)に関するページを参照してください。

## <a name="see-also"></a>関連項目

[JDBC ドライバー アプリケーションのセキュリティ保護](securing-jdbc-driver-applications.md)
