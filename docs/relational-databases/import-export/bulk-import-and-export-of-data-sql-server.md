---
title: データの一括インポートと一括エクスポート (SQL Server) | Microsoft Docs
description: SQL Server では、SQL Server テーブルからのデータの一括エクスポート、および SQL Server テーブルまたはパーティション分割されていないビューへの一括データのインポートがサポートされています。
ms.custom: ''
ms.date: 09/25/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: data-movement
ms.topic: conceptual
helpviewer_keywords:
- exporting data
- bulk importing [SQL Server], about bulk importing
- data [SQL Server], bulk export and import
- transferring data
- importing data, (See also bulk importing [SQL Server])
- copying data [SQL Server]
- bulk exporting [SQL Server]
- importing data, bulk import
- copying data [SQL Server], bulk export and import
- exporting data,(See also bulk exporting [SQL Server])
- bulk exporting [SQL Server], about bulk exporting
- bulk importing [SQL Server]
- importing data
ms.assetid: 19049021-c048-44a2-b38d-186d9f9e4a65
author: MashaMSFT
ms.author: mathoma
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: ca5bb01700d61d3bfa2f5f4b7f7cded3c0820b64
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104752292"
---
# <a name="bulk-import-and-export-of-data-sql-server"></a>データの一括インポートと一括エクスポート (SQL Server)

[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、*テーブルからのデータの一括エクスポート (* 一括データのエクスポート [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] )、および [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] テーブルまたはパーティション分割されていないビューへの一括データのインポートがサポートされています。

- *一括エクスポート* とは、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] テーブルから特定のデータ ファイルにデータをコピーすることです。
- *一括インポート* は、データ ファイルから [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] テーブルにデータを読み込むことを指します。 たとえば、データを [!INCLUDE[msCoName](../../includes/msconame-md.md)] Excel アプリケーションから特定のデータ ファイルにエクスポートした後、そのデータを [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] テーブルに一括インポートできます。

## <a name="methods-for-bulk-importing-and-exporting-data"></a><a name="MethodsForBuliIE"></a> データの一括インポートと一括エクスポートの方法

 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] テーブルからのデータの一括エクスポート、および [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] テーブルまたはパーティション分割されていないビューへのデータの一括インポートがサポートされています。 使用できる基本的な方法を次に示します。

|Method|説明|データのインポート|データのエクスポート|
|------------|-----------------|------------------|------------------|
|[bcp ユーティリティ](../../relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server.md)|データの一括エクスポートと一括インポート、およびフォーマット ファイルの生成を行うコマンド ライン ユーティリティ (Bcp.exe)。|はい|はい|
|[BULK INSERT ステートメント](../../relational-databases/import-export/import-bulk-data-by-using-bulk-insert-or-openrowset-bulk-sql-server.md)|データ ファイルのデータをデータベース テーブルまたはパーティション分割されていないビューに直接インポートする [!INCLUDE[tsql](../../includes/tsql-md.md)] ステートメント。|はい|いいえ|
|[INSERT ...SELECT * FROM OPENROWSET(BULK...) ステートメント](../../relational-databases/import-export/import-bulk-data-by-using-bulk-insert-or-openrowset-bulk-sql-server.md)|INSERT ステートメントでデータを選択するために OPENROWSET(BULK...) 関数を指定することによって、OPENROWSET 一括行セット プロバイダーを使用してデータを [!INCLUDE[tsql](../../includes/tsql-md.md)] テーブルに一括インポートする [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ステートメント。|はい|いいえ|
|[SQL Server インポートおよびエクスポート ウィザード](../../integration-services/import-export-data/import-and-export-data-with-the-sql-server-import-and-export-wizard.md)|このウィザードでは、データセット、スプレッドシート、テキスト ファイルなど、さまざまな一般的なデータ形式の間でデータをインポートまたはエクスポートする簡単なパッケージが作成されます。|はい|はい|

> [!IMPORTANT]
> SQL Server へのデータの一括インポートのデータ ファイルとしてコンマ区切り値 (CSV) ファイルを使用する場合の規則については、「[一括エクスポートまたは一括インポートのデータの準備 (SQL Server)](../../relational-databases/import-export/prepare-data-for-bulk-export-or-import-sql-server.md)」を参照してください。

> [!NOTE]
> Azure Synapse Analytics では、区切りファイルをインポートおよびエクスポートするために、bcp ユーティリティのみがサポートされています。

## <a name="format-files"></a><a name="FFs"></a> フォーマット ファイル

[bcp ユーティリティ](../../tools/bcp-utility.md)、[BULK INSERT](../../t-sql/statements/bulk-insert-transact-sql.md)、および [INSERT ...SELECT * FROM OPENROWSET(BULK...)](../../t-sql/functions/openrowset-transact-sql.md) では、*フォーマット ファイル* という特殊なファイルを使用して、データ ファイル内のフィールドごとにフォーマット情報を格納することができます。 また、フォーマット ファイルには、対応する [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] テーブルに関する情報が含まれる場合もあります。 フォーマット ファイルは、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]のインスタンスからデータを一括エクスポートしたり、このインスタンスにデータを一括インポートしたりするのに必要なすべてのフォーマット情報を指定するために使用できます。

> [!IMPORTANT]
> BCP を使用して、Azure Blob Storage と Azure SQL Database の間でデータをインポートしたりエクスポートしたりすることはできません。 Azure Blob Storage に対してインポートまたはエクスポートするには、[BULK INSERT](../../t-sql/statements/bulk-insert-transact-sql.md) または [OPENROWSET](../../t-sql/functions/openrowset-transact-sql.md) を使用します。

フォーマット ファイルを使用すると、インポートの際にデータ ファイルの形式に従ってデータを解釈したり、エクスポートの際にデータ ファイル内のデータに形式を適用する処理を柔軟に行えるようになります。 これにより、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] または外部アプリケーションの特定の必要性に応じてデータの解釈や再フォーマットを行うことだけを目的としたプログラムを作成する必要がなくなります。 たとえば、コンマ区切り値が必要なアプリケーションに読み込まれるデータを一括インポートする場合、フォーマット ファイルを使用すると、エクスポートされたデータにフィールド ターミネータとしてコンマを挿入できます。

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、次の 2 種類のフォーマット ファイルがサポートされます:XML フォーマット ファイルと XML 以外のフォーマット ファイル。

フォーマット ファイルを生成できるツールは、 [bcp ユーティリティ](../../tools/bcp-utility.md) だけです。 詳細については、「[フォーマット ファイルの作成 &#40;SQL Server&#41;](../../relational-databases/import-export/create-a-format-file-sql-server.md)」をご覧ください。 フォーマット ファイルの使用方法の詳細は、「[データのインポートまたはエクスポート用のフォーマット ファイル &#40;SQL Server&#41;](../../relational-databases/import-export/format-files-for-importing-or-exporting-data-sql-server.md)」を参照してください。

> [!NOTE]
> 一括エクスポート操作または一括インポート操作でフォーマット ファイルが正しく提供されなかった場合に備えて、ユーザーはコマンド ラインで既定の形式をオーバーライドすることもできます。

|関連トピック|
|---|
|[一括エクスポートまたは一括インポートのデータの準備 (SQL Server)](../../relational-databases/import-export/prepare-data-for-bulk-export-or-import-sql-server.md)|
|[一括インポートまたは一括エクスポートのデータ形式 (SQL Server)](../../relational-databases/import-export/data-formats-for-bulk-import-or-bulk-export-sql-server.md)<br />&emsp;&#9679;&emsp;[ネイティブ形式を使用したデータのインポートまたはエクスポート (SQL Server)](../../relational-databases/import-export/use-native-format-to-import-or-export-data-sql-server.md)<br />&emsp;&#9679;&emsp;[文字形式を使用したデータのインポートまたはエクスポート (SQL Server)](../../relational-databases/import-export/use-character-format-to-import-or-export-data-sql-server.md)<br />&emsp;&#9679;&emsp;[Unicode ネイティブ形式を使用したデータのインポートまたはエクスポート (SQL Server)](../../relational-databases/import-export/use-unicode-native-format-to-import-or-export-data-sql-server.md)<br />&emsp;&#9679;&emsp;[Unicode 文字形式を使用したデータのインポートまたはエクスポート (SQL Server)](../../relational-databases/import-export/use-unicode-character-format-to-import-or-export-data-sql-server.md)<br />&emsp;&#9679;&emsp;[以前のバージョンの SQL Server からのネイティブ形式データおよび文字形式データのインポート](../../relational-databases/import-export/import-native-and-character-format-data-from-earlier-versions-of-sql-server.md)|
|[bcp を使用した互換性のためのデータ形式の指定 (SQL Server)](../../relational-databases/import-export/specify-data-formats-for-compatibility-when-using-bcp-sql-server.md)<br />&emsp;&#9679;&emsp;[bcp を使用したファイル ストレージ型の指定 (SQL Server)](../../relational-databases/import-export/specify-file-storage-type-by-using-bcp-sql-server.md)<br />&emsp;&#9679;&emsp;[bcp を使用したデータ ファイルのプレフィックス長の指定 (SQL Server)](../../relational-databases/import-export/specify-prefix-length-in-data-files-by-using-bcp-sql-server.md)<br />&emsp;&#9679;&emsp;[bcp を使用したフィールド長の指定 (SQL Server)](../../relational-databases/import-export/specify-field-length-by-using-bcp-sql-server.md)<br />&emsp;&#9679;&emsp;[フィールド ターミネータと行ターミネータの指定 (SQL Server)](../../relational-databases/import-export/specify-field-and-row-terminators-sql-server.md)|
|[一括インポート中の NULL の保持または既定値の使用 (SQL Server)](../../relational-databases/import-export/keep-nulls-or-use-default-values-during-bulk-import-sql-server.md)|
|[データの一括インポート時の ID 値の保持 (SQL Server)](../../relational-databases/import-export/keep-identity-values-when-bulk-importing-data-sql-server.md)|
|[データのインポートまたはエクスポート用のフォーマット ファイル (SQL Server)](../../relational-databases/import-export/format-files-for-importing-or-exporting-data-sql-server.md)<br />&emsp;&#9679;&emsp;[フォーマット ファイルの作成 (SQL Server)](../../relational-databases/import-export/create-a-format-file-sql-server.md)<br />&emsp;&#9679;&emsp;[データの一括インポートでのフォーマット ファイルの使用 (SQL Server)](../../relational-databases/import-export/use-a-format-file-to-bulk-import-data-sql-server.md)<br />&emsp;&#9679;&emsp;[フォーマット ファイルを使用したテーブル列のスキップ (SQL Server)](../../relational-databases/import-export/use-a-format-file-to-skip-a-table-column-sql-server.md)<br />&emsp;&#9679;&emsp;[フォーマット ファイルを使用したデータ フィールドのスキップ (SQL Server)](../../relational-databases/import-export/use-a-format-file-to-skip-a-data-field-sql-server.md)<br />&emsp;&#9679;&emsp;[フォーマット ファイルを使用したテーブル列とデータ ファイル フィールドのマッピング (SQL Server)](../../relational-databases/import-export/use-a-format-file-to-map-table-columns-to-data-file-fields-sql-server.md)|

## <a name="more-information"></a>詳細情報

- [一括インポートで最小ログ記録を行うための前提条件](../../relational-databases/import-export/prerequisites-for-minimal-logging-in-bulk-import.md)
- [XML ドキュメントの一括インポートと一括エクスポートの例 &#40;SQL Server&#41;](../../relational-databases/import-export/examples-of-bulk-import-and-export-of-xml-documents-sql-server.md)
- [SQL Server Integration Services](../../integration-services/sql-server-integration-services.md)
- [他のサーバーへのデータベースのコピー](../../relational-databases/databases/copy-databases-to-other-servers.md)
- [XML データの一括読み込みの実行 &#40;SQLXML 4.0&#41;](../../relational-databases/sqlxml-annotated-xsd-schemas-xpath-queries/bulk-load-xml/performing-bulk-load-of-xml-data-sqlxml-4-0.md)
- [一括コピー操作の実行](../../relational-databases/native-client/features/performing-bulk-copy-operations.md)
