---
description: SQL Server への新しい ODBC 接続を作成する目的でデータ ソース ウィザードで認証方法を選択する方法について学びます。
title: データ ソース ウィザード画面 2 (ODBC Driver for SQL Server)
ms.custom: ''
ms.date: 01/29/2021
ms.prod: sql
ms.prod_service: connectivity
ms.reviewer: v-daenge
ms.technology: connectivity
ms.topic: conceptual
ms.assetid: 76326eeb-1144-4b9f-85db-50524c655d30
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: 7db7d1d8e01929f9c43b78cd4d74e63dcc1b6e12
ms.sourcegitcommit: 00af0b6448ba58e3685530f40bc622453d3545ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104673386"
---
# <a name="data-source-wizard-screen-2"></a>データ ソース ウィザード画面 2

データ ソースの構成時に ODBC Driver for SQL Server で SQL Server への接続に使用される認証方法の指定、Microsoft SQL Server アドバンスト クライアント エントリのセットアップ、およびログインとパスワードの設定を行います。

## <a name="options"></a>オプション

### <a name="with-integrated-windows-authentication"></a>統合 Windows 認証を使う

ドライバーが SQL Server へのセキュリティで保護された (信頼された) 接続を要求するよう指定します。 オンにすると、SQL Server では、サーバーの現在のログイン セキュリティ モードに関係なく、統合ログイン セキュリティを使用して、このデータ ソースを使用する接続を確立します。 指定された任意のログイン ID またはパスワードは無視されます。 SQL Server システム管理者は、Windows ログインを SQL Server ログイン ID と関連付けておく必要があります (たとえば、SQL Server Management Studio を使用します)。

必要であれば、サーバーのサービス プリンシパル名 (SPN) を指定できます。

### <a name="with-active-directory-integrated-authentication"></a>Active Directory 統合認証を使用する

ドライバーが Azure Active Directory を使用して SQL Server に対する認証を行うように指定します。 オンにすると、SQL Server では、サーバーの現在のログイン セキュリティ モードに関係なく、Azure Active Directory 統合ログイン セキュリティを使用して、このデータ ソースを使用する接続を確立します。

### <a name="with-sql-server-authentication"></a>SQL Server 認証を使用する

ドライバーがログイン ID とパスワードを使用して SQL Server に対する認証を行うように指定します。

### <a name="with-active-directory-password-authentication"></a>Active Directory パスワード認証を使用する

ドライバーが Azure Active Directory のログイン ID とパスワードを使用して SQL Server に対する認証を行うように指定します。

### <a name="with-active-directory-interactive-authentication"></a>Active Directory 対話型認証を使用する

ドライバーが Azure Active Directory 対話型モードを使用してログイン ID を指定することによって SQL Server に対する認証を行うように指定します。 このオプションにより、Azure 認証のプロンプト ダイアログがトリガーされます。

### <a name="with-managed-identity-authentication"></a>マネージド ID の認証を使用する

ドライバーがマネージド ID を使用して SQL Server に対する認証を行うように指定します。

### <a name="with-active-directory-service-principal-authentication"></a>Active Directory サービス プリンシパル認証の使用

ドライバーが Azure Active Directory のサービス プリンシパルを使用して SQL Server に対する認証を行うように指定します。

### <a name="login-id"></a>Login ID

**[ユーザーが入力する SQL Server 用のログイン ID とパスワードを使う]**、**[With Active Directory Password authentication using a login ID and password entered by the user]\(ユーザーが入力する Active Directory パスワード認証用のログイン ID とパスワードを使う\)**、または **[With Active Directory Interactive authentication using a login ID entered by the user]\(ユーザーが入力する Active Directory 対話型認証用のログイン ID を使う\)** が選択されている場合、SQL Server への接続時にドライバーが使用するログイン ID を指定します。 **[With Managed Identity authentication]\(マネージド ID の認証を使用する\)** が選択されている場合は、マネージド ID のオブジェクト ID を指定するか、空白のままにして既定の ID を使用します。 このフィールドは、サーバーの既定の設定を確認するために確立された接続のみに適用されます。そのため、マネージド ID の認証を使用する場合を除き、データ ソースが作成された後にそれを使用して確立された以降の接続には適用されません。

### <a name="password"></a>パスワード

**[ユーザーが入力する SQL Server 用のログイン ID とパスワードを使う]** または **[With Active Directory Password authentication using a login ID and password entered by the user]\(ユーザーが入力する Active Directory パスワード認証用のログイン ID とパスワードを使う\)** が選択されている場合、SQL Server への接続時にドライバーが使用するパスワードを指定します。 このフィールドは、サーバーの既定の設定を確認するために確立された接続のみに適用されます。そのため、新しいデータ ソースを使用して確立された以降の接続には適用されません。

**[統合 Windows 認証を使う]** または **[With Active Directory Integrated authentication]\(Active Directory 統合認証を使う\)** が選択されている場合、**[ログイン ID]** と **[パスワード]** ボックスは両方ともオフになります。

### <a name="next"></a>次へ

ウィザードの次の画面に進みます。

### <a name="back"></a>戻る

ウィザードの前の画面に戻ります。

## <a name="next-steps"></a>次のステップ

[データ ソース ウィザード画面 1](dsn-wizard-1.md)  
[データ ソース ウィザード画面 3](dsn-wizard-3.md)  
