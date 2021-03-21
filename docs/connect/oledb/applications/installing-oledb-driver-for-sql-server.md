---
title: OLE DB Driver for SQL Server のインストール | Microsoft Docs
description: OLE DB Driver for SQL Server のインストールとアンインストール。 OLE DB Driver for SQL Server をインストールするには、msoledbsql.msi インストーラーが必要です。
ms.custom: ''
ms.date: 02/12/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: connectivity
ms.topic: reference
helpviewer_keywords:
- OLE DB Driver for SQL Server, uninstalling
- MSOLEDBSQL, installing
- MSOLEDBSQL, uninstalling
- Setup [OLE DB Driver for SQL Server]
- uninstalling OLE DB Driver for SQL Server
- data access [OLE DB Driver for SQL Server], uninstalling OLE DB Driver for SQL Server
- installing OLE DB Driver for SQL Server
- OLE DB Driver for SQL Server, installing
- data access [OLE DB Driver for SQL Server], installing OLE DB Driver for SQL Server
- removing OLE DB Driver for SQL Server
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: f3fa22e03525119a5aa1b66fa81f11c6dc6e9023
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104746372"
---
# <a name="installing-ole-db-driver-for-sql-server"></a>OLE DB Driver for SQL Server のインストール
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[Driver_OLEDB_Download](../../../includes/driver_oledb_download.md)]

OLE DB Driver for SQL Server をインストールするには、msoledbsql.msi インストーラーが必要です。
インストーラーを実行し、適切な選択を行います。 OLE DB Driver for SQL Server は、以前のバージョンの Microsoft OLE DB プロバイダーとサイドバイサイドでインストールできます。

OLE DB Driver for SQL Server のファイル (msoledbsql.dll、msoledbsqlr.rll) は `%SYSTEMROOT%\system32\` にインストールされます。 また、x64 msoledbsql.msi では 32 ビット バイナリが `%SYSTEMROOT%\SysWOW64\` にインストールされます。

> [!NOTE]  
> OLE DB Driver for SQL Server の適切なレジストリ設定はすべて、インストール処理の一部として行われます。  

OLE DB Driver for SQL Server のヘッダー ファイルとライブラリ ファイル (msoledbsql.h と msoledbsql.lib) は `%PROGRAMFILES%\Microsoft SQL Server\Client SDK\OLEDB\182\SDK` にインストールされます。 また、x64 msoledbsql.msi では同じファイルが `%PROGRAMFILES(x86)%\Microsoft SQL Server\Client SDK\OLEDB\182\SDK` にインストールされます。  

msoledbsql.msi を使用して OLE DB Driver for SQL Server を配布できます。 アプリケーションを配置する際には、OLE DB Driver for SQL Server のインストールが必要になる場合があります。 チェイナーとブートストラップのテクノロジを使用すると、ユーザーが 1 回のインストール手順に従うだけで複数のパッケージをまとめてインストールできるようになります。 詳細については、「[Visual Studio 2005 用のカスタム ブートストラップ パッケージの作成](/previous-versions/aa730839(v=vs.80))」および「[カスタムの必須コンポーネントの追加](/visualstudio/deployment/creating-bootstrapper-packages)」をご覧ください。  
  
x64 msoledbsql.msi では、32 ビット バージョンの OLE DB Driver for SQL Server もインストールされます。 アプリケーションが、開発時に使用したものとは異なるプラットフォームを対象としている場合、x64 および x86 用のバージョンの msoledbsql.msi をダウンロードできます。

msoledbsql.msi を呼び出すと、既定ではクライアント コンポーネントだけがインストールされます。 クライアント コンポーネントは、OLE DB Driver for SQL Server を使用して開発されたアプリケーションの実行をサポートするファイルです。 SDK コンポーネントもインストールするには、コマンド ラインで `ADDLOCAL=All` を指定します。 次に例を示します。  

`msiexec /i msoledbsql.msi ADDLOCAL=ALL`  

## <a name="silent-install"></a>サイレント インストール  
 msiexec で /passive、/qn、/qb、または /qr オプションを指定する場合、IACCEPTMSOLEDBSQLLICENSETERMS=YES も指定して、使用許諾契約の条件に同意することを明示的に指定する必要があります。 このオプションは、すべて大文字で指定する必要があります。  

## <a name="installing-ole-db-driver-for-sql-server-as-a-dependency"></a>依存関係としての OLE DB Driver for SQL Server のインストール  
すべての依存アプリケーションがアンインストールされるまで、OLE DB Driver for SQL Server をアンインストールしないようにしてください。 アプリケーションが OLE DB Driver for SQL Server に依存していることを示す警告をユーザーに表示するには、次のように MSI で APPGUID インストール オプションを使用します。  

 `msiexec /i msoledbsql.msi APPGUID={0CC618CE-F36A-415E-84B4-FB1BFF6967E1}`  

APPGUID に渡す値は、特定の製品コードです。 Microsoft インストーラーを使用してアプリケーションのセットアップ プログラムをバンドルするときは、製品コードを作成する必要があります。
APPGUID オプションでは、管理者特権でのコマンド プロンプトからインストーラーを実行する必要があります。

## <a name="see-also"></a>参照  
 [OLE DB Driver for SQL Server を使用したアプリケーションの構築](../../oledb/applications/building-applications-with-oledb-driver-for-sql-server.md)