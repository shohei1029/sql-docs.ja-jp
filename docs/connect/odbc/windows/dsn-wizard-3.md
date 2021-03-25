---
description: SQL Server への新しい ODBC 接続を作成する目的で、データ ソース ウィザードで既定のデータベースなどを定義する方法について学びます。
title: データ ソース ウィザード画面 3 (ODBC Driver for SQL Server)
ms.custom: ''
ms.date: 09/27/2017
ms.prod: sql
ms.prod_service: connectivity
ms.reviewer: ''
ms.technology: connectivity
ms.topic: conceptual
ms.assetid: 76326eeb-1144-4b9f-85db-50524c655d30
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: 2263cfde2f48da1562e4a23015cb0e575a23cc4e
ms.sourcegitcommit: 00af0b6448ba58e3685530f40bc622453d3545ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104673126"
---
# <a name="data-source-wizard-screen-3"></a>データ ソース ウィザード画面 3

既定のデータベース、ドライバーで使用されるさまざまな ANSI オプション、およびミラー サーバーの名前を指定します。

## <a name="options"></a>オプション

### <a name="change-the-default-database-to"></a>既定のデータベースを以下に変更する

データ ソースを使用して確立された任意の接続に既定のデータベース名を指定します。 このチェック ボックスがオフの場合、接続は、サーバーのログイン ID に対して定義されている既定のデータベースを使用します。 このチェック ボックスがオンの場合、ボックスで指定されたデータベースが、ログイン ID に対して定義されている既定のデータベースをオーバーライドします。 **[データベース ファイル名を添付する]** ボックスにプライマリ ファイルの名前が指定されている場合、プライマリ ファイルで示されるデータベースは、**[既定のデータベースを以下のものに変更する]** ボックスに指定されているデータベース名を使用したデータベースとしてアタッチされます。

ログイン ID の既定のデータベースを使用するのは、ODBC データ ソースに既定のデータベースを指定するよりも効率的です。

### <a name="mirror-server"></a>ミラー サーバー

ミラー化するデータベースのフェールオーバー パートナーの名前を指定します。 **[既定のデータベースを以下のものに変更する]** ボックスにデータベース名が表示されていない場合、または表示されている名前が既定のデータベースの場合、**[ミラー サーバー]** はグレーで表示されます。

必要に応じて、ミラー サーバーにサーバー プリンシパル名 (SPN) を指定できます。 ミラー サーバーの SPN は、クライアントとサーバー間の相互認証に使用されます。

### <a name="attach-database-filename"></a>データベース ファイル名を添付する

アタッチできるデータベースのプライマリ ファイルの名前を指定します。 このデータベースがアタッチされ、データ ソースの既定のデータベースとして使用されます。 プライマリ ファイルの完全なパスとファイル名を指定します。 **[既定のデータベースを以下のものに変更する]** ボックスに指定されたデータベース名は、アタッチされたデータベースの名前として使用されます。

### <a name="use-ansi-quoted-identifiers"></a>ANSI の引用符付き識別子を使用する

ODBC Driver for SQL Server で接続するときに `QUOTED_IDENTIFIERS` をオンに設定するように指定します。 このチェック ボックスがオンの場合、SQL Server では、引用符に関する ANSI 規則が適用されます。 二重引用符は、列名やテーブル名など、識別子のみに使用できます。 文字列は単一引用符で囲む必要があります。

```sql
SELECT "LastName"
FROM "Person.Contact"
WHERE "LastName" = 'O''Brien'
```

このチェック ボックスがオフの場合、Microsoft Excel に付属の Microsoft Query ユーティリティなど、引用符で囲まれた識別子を使用するアプリケーションでは、引用符で囲まれた識別子を使用した SQL ステートメントを生成するとエラーが発生します。

### <a name="use-ansi-nulls-paddings-and-warnings"></a>ANSI の NULL、埋め込み文字、警告を使用する

ODBC Driver for SQL Server で接続する際に ANSI_NULLS、ANSI_WARNINGS、および ANSI_PADDINGS の各オプションをオンに設定するように指定します。

ANSI_NULLS がオンに設定されている場合、サーバーでは、NULL に対する列の比較に関する ANSI 規則が適用されます。 ANSI 構文 "IS NULL" または "IS NOT NULL" は、すべての NULL 比較に使用する必要があります。 Transact-SQL 構文 "= NULL" はサポートされていません。

ANSI_WARNINGS がオンに設定されている場合、SQL Server では、ANSI 規則に違反していても Transact-SQL の規則には違反していない状況で警告メッセージが表示されます。 このようなエラーの例として、INSERT ステートメントまたは UPDATE ステートメント実行時のデータの切り捨て、または集計関数での NULL 値の検出があります。

ANSI_PADDING がオンに設定されている場合、**varchar** 値の末尾の空白と **varbinary** 値の末尾のゼロは自動的に切り捨てられます。

### <a name="application-intent"></a>[アプリケーション インテント]

アプリケーションがサーバーに接続するときのワークロードのタイプを宣言します。 有効値は、**ReadOnly** と **ReadWrite** です。

### <a name="multi-subnet-failover"></a>マルチサブネット フェールオーバー

アプリケーションが異なるサブネット上の高可用性、ディザスター リカバリー (AlwaysOn 可用性グループ) 可用性グループ (AG) に接続している場合は、**マルチサブネット フェールオーバー** を有効にします。 (現在) アクティブなサーバーをより迅速に検出し、接続するように ODBC Driver for SQL Server を構成します。

### <a name="transparent-network-ip-resolution"></a>Transparent Network IP Resolution

**マルチサブネット フェールオーバー** の動作を変更して、フェールオーバー中の再接続を高速化します。 詳しくは、「[透過的なネットワーク IP の解決の使用](../using-transparent-network-ip-resolution.md)」をご覧ください。

### <a name="column-encryption"></a>列暗号化

SQL Server 2016 以降で使用可能な [Always Encrypted](../using-always-encrypted-with-the-odbc-driver.md) 機能を使用して、暗号化された列との間のデータ転送を自動的に復号化および暗号化できます。

### <a name="use-fmtonly-metadata-discovery"></a>FMTONLY メタデータの検出を使用する

SQL Server 2012 以降に接続する場合は、従来の SET FMTONLY メタデータの検出メソッドを使用します。 [sp_describe_first_result_set](../../../relational-databases/system-stored-procedures/sp-describe-first-result-set-transact-sql.md) でサポートされていないクエリ (一時テーブルを含むクエリなど) を使用する場合にのみ、このオプションを有効にします。

### <a name="next"></a>次へ

ウィザードの次の画面に進みます。

### <a name="back"></a>戻る

ウィザードの前の画面に戻ります。

## <a name="next-steps"></a>次のステップ

[データ ソース ウィザード画面 2](dsn-wizard-2.md)  
[データ ソース ウィザード画面 4](dsn-wizard-4.md)  
