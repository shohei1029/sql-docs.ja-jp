---
title: ユニバーサル データ リンク (UDL) の構成 | Microsoft Docs
description: '[接続] タブを使用し、OLE DB Driver for SQL Server を使用してデータに接続する方法を指定する方法について説明します。'
ms.custom: ''
ms.date: 09/30/2020
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: v-daenge
ms.technology: connectivity
ms.topic: conceptual
ms.author: v-beaziz
author: bazizi
ms.openlocfilehash: e9370ec3d095308741f2b587c507b6d8994fd35b
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104751062"
---
# <a name="universal-data-link-udl-configuration"></a>ユニバーサル データ リンク (UDL) の構成
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[Driver_OLEDB_Download](../../../includes/driver_oledb_download.md)]

## <a name="connection-tab"></a>[接続] タブ
Microsoft OLE DB Driver for SQL Server を使用してデータに接続する方法を指定するには、[接続] タブを使用します。

![OLE DB データ リンク ページ - [接続] タブのスクリーンショット](../media/data-link-pages-connection-tab.png)

[接続] タブはプロバイダー固有であり、Microsoft OLE DB Driver for SQL Server に必要な接続プロパティだけが表示されます。

|オプション|説明|
|---   |---        |
|[サーバー名の選択または入力]|ドロップダウン リストからサーバー名を選択するか、アクセスするデータベースが格納されているサーバーの場所を入力します。 サーバー上のデータベースを選択するのは別の操作です。 一覧を更新するには、"更新" をクリックします。
|Enter information to sign in to the server (サーバーにサインインするための情報の入力)|このドロップダウン リストから、次の認証オプションを選択できます。 <ul><li>`Windows Authentication:` 現在ログインしているユーザーの Windows アカウントの資格情報を使用した SQL Server に対する認証。</li><li>`SQL Server Authentication:` ログイン ID とパスワードを使用した認証。</li><li>`Active Directory - Integrated:` Azure Active Directory の ID による統合認証。 このモードは、SQL Server に対する Windows 認証でも使用できます。</li><li>`Active Directory - Password:` Azure Active Directory の ID によるユーザー ID とパスワードの認証。</li><li>`Active Directory - Universal with MFA support:` Azure Active Directory の ID による対話型認証。 このモードを使用すると、Azure Active Directory Multi-Factor Authentication (MFA) がサポートされます。</li><li>`Active Directory - Service Principal:` Azure Active Directory サービス プリンシパルを使用した認証。 **ユーザー名** はアプリケーション (クライアント) ID に設定する必要があります。 **パスワード** はアプリケーション (クライアント) シークレットに設定する必要があります。</li></ul>|
|サーバー SPN|セキュリティ接続を使用する場合、サーバーのサービス プリンシパル名 (SPN) を指定できます。|
|ユーザー名|データ ソースにサインインするときに認証に使用するユーザー ID を入力します。|
|Password|データ ソースにサインインするときに認証に使用するパスワードを入力します。|
|パスワードを空白にする|オンにすると、指定したプロバイダーが接続文字列で空白のパスワードを使用できるようになります。|
|パスワードを保存する|オンにすると、接続文字列と共にパスワードを保存できるようになります。 パスワードが接続文字列に含まれるかどうかは、呼び出し元アプリケーションの機能によって決まります。 <br/><br/>**注:** 保存する場合、パスワードはマスクも暗号化もされない状態で返されて保存されます。|
|[データに強力な暗号を使用する]|オンにすると、接続を介して渡されるデータが暗号化されます。|
|[サーバー証明書を信頼する]|オンにすると、サーバーの証明書が検証されます。 サーバーの証明書は、サーバーの正しいホスト名を含み、信頼された証明機関によって発行されている必要があります。|
|データベースを選択します|アクセスするデータベースの名前を選択または入力します。|
|データベース ファイルをデータベース名として添付する|アタッチできるデータベースのプライマリ ファイルの名前を指定します。 このデータベースがアタッチされ、データ ソースの既定のデータベースとして使用されます。 このセクションの最初のテキスト ボックスに、アタッチされるデータベース ファイルに使用するデータベース名を入力します。<br/><br/>`Using the filename` というラベルが付いたテキスト ボックスに、アタッチされるデータベース ファイルへの完全なパスを入力するか、`...` ボタンをクリックしてデータベース ファイルを参照します。|
|パスワードの変更|[SQL Server パスワードの変更] ダイアログを表示します。 |
|接続をテスト|指定したデータ ソースへの接続を試行するときにクリックします。 接続に失敗した場合、設定が正しいかどうかを確認します。 たとえば、スペル ミスや大文字と小文字の区別は接続の失敗の原因になる場合があります。|

## <a name="advanced-tab"></a>[詳細設定] タブ
追加の初期化プロパティを表示および設定するには、[詳細設定] タブを使用します。

![OLE DB データ リンク ページ - [詳細設定] タブのスクリーンショット](../media/data-link-pages-advanced-tab.png)

|オプション|説明|
|---   |---        |
| Connect timeout | Microsoft OLE DB Driver for SQL Server が初期化の完了を待機する時間 (秒数) を指定します。 初期化がタイムアウトすると、エラーが返され、接続は作成されません。|


> [!NOTE]  
>  一般的なデータ リンク接続情報については、「[Data Link API の概要](/previous-versions/windows/desktop/ms718102(v=vs.85))」を参照してください。

## <a name="next-steps"></a>次のステップ
- OLE DB ドライバーを使用して [Azure Active Directory に対する認証を行います](../features/using-azure-active-directory.md)。

- OLE DB ドライバーを使用して[認証資格情報をユーザーに要求します](../help-topics/sql-server-login-dialog.md)。