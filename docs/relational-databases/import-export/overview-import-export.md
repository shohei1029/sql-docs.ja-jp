---
title: SQL Server および Azure SQL Database のデータをインポートおよびエクスポートする
description: Transact-SQL、コマンドライン ツール、ウィザードを使用して、SQL Server および Azure SQL Database のデータをさまざまなデータ形式でインポートおよびエクスポートすることができます。
ms.date: 10/27/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: data-movement
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.custom: seo-lt-2019
ms.openlocfilehash: 85b4c92a1158bf1b0a8870137ef254dc472dec54
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104737652"
---
# <a name="import-and-export-data-from-sql-server-and-azure-sql-database"></a>SQL Server および Azure SQL Database のデータをインポートおよびエクスポートする
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]
さまざまな方法を使って、SQL Server と Azure SQL Database のデータをインポートしておよびエクスポートできます。 Transact-SQL ステートメント、コマンドライン ツール、ウィザードなどの方法があります。

また、インポートおよびエクスポートするデータの形式にもさまざまな種類があります。 フラット ファイル、Excel、主要なリレーショナル データベース、さまざまなクラウド サービスなどの形式があります。

## <a name="methods-for-importing-and-exporting-data"></a>データのインポートとエクスポートの方法

### <a name="use-transact-sql-statements"></a>Transact-SQL ステートメントを使用する
`BULK INSERT` または `OPENROWSET(BULK...)` コマンドでデータをインポートできます。 通常は、SQL Server Management Studio (SSMS) でこれらのコマンドを実行します。 詳しくは、「[BULK INSERT または OPENROWSET(BULK...) を使用した一括データのインポート](import-bulk-data-by-using-bulk-insert-or-openrowset-bulk-sql-server.md)」をご覧ください。

### <a name="use-bcp-from-the-command-prompt"></a>コマンド プロンプトから BCP を使用する
BCP コマンドライン ユーティリティを使って、データをインポートおよびエクスポートできます。 詳しくは、「[bcp ユーティリティを使用した一括データのインポートとエクスポート](import-and-export-bulk-data-by-using-the-bcp-utility-sql-server.md)」をご覧ください。

### <a name="use-the-import-flat-file-wizard"></a>フラット ファイルのインポート ウィザードを使用する
インポート ウィザードやエクスポート ウィザードおよびその他のツールで使用可能な構成オプションが必ずしもすべて必要ではない場合、SQL Server Management Studio (SSMS) の **フラット ファイルのインポート ウィザード** を使用して、テキスト ファイルを SQL Server にインポートできます。 詳細については、次の記事を参照してください。
- [SQL のフラット ファイルのインポート ウィザード](import-flat-file-wizard.md)
- [SQL Server Management Studio 17.3 の新機能](https://blogs.technet.microsoft.com/dataplatforminsider/2017/10/10/whats-new-in-sql-server-management-studio-17-3/)
- [SSMS 17.3 の新しいフラット ファイルのインポート ウィザードの概要](https://channel9.msdn.com/Shows/Data-Exposed/Introducing-the-new-Import-Flat-File-Wizard-in-SSMS-173)

### <a name="use-the-sql-server-import-and-export-wizard"></a>SQL Server インポートおよびエクスポート ウィザードを使用する
SQL Server インポートおよびエクスポート ウィザードを使って、さまざまなソースとデスティネーションの間で、データをインポートおよびエクスポートできます。 ウィザードを使うには、SQL Server Integration Services (SSIS) または SQL Server Data Tools (SSDT) がインストールされている必要があります。 詳しくは、「[SQL Server インポートおよびエクスポート ウィザードを使用してデータをインポートおよびエクスポートする](../../integration-services/import-export-data/import-and-export-data-with-the-sql-server-import-and-export-wizard.md)」をご覧ください。

### <a name="design-your-own-import-or-export"></a>独自のインポートまたはエクスポートを設計する
カスタム データ インポートを設計する場合は、次のいずれかの機能またはサービスを使うことができます。
-   SQL Server Integration Services。 詳しくは、「[SQL Server Integration Services](../../integration-services/sql-server-integration-services.md)」をご覧ください。
-   Azure Data Factory。 詳しくは、「[Azure Data Factory の概要](/azure/data-factory/data-factory-introduction)」をご覧ください。

## <a name="data-formats-for-import-and-export"></a>インポートおよびエクスポートのデータ形式

### <a name="supported-formats"></a>サポートされるフォーマット

フラット ファイルまたはその他のさまざまなファイル形式、リレーショナル データベース、およびクラウド サービスで、データをインポートおよびエクスポートできます。 特定のツールでのこれらのオプションについて詳しくは、次のトピックをご覧ください。
-   SQL Server インポートおよびエクスポート ウィザードについては、「[Connect to Data Sources with the SQL Server Import and Export Wizard](../../integration-services/import-export-data/connect-to-data-sources-with-the-sql-server-import-and-export-wizard.md)」(SQL Server インポートおよびエクスポート ウィザードを使用してデータ ソースに接続する) をご覧ください。
-   SQL Server Integration Services については、「[Integration Services (SSIS) の接続](../../integration-services/connection-manager/integration-services-ssis-connections.md)」をご覧ください。
-   Azure Data Factory については、「[Azure Data Factory を使用して Amazon Redshift からデータを移動する](/azure/data-factory/data-factory-amazon-redshift-connector)」をご覧ください。

### <a name="commonly-used-data-formats"></a>よく使われるデータ形式

一部のよく使われるデータ形式には、特別な考慮事項と例があります。 これらのデータ形式について詳しくは、次のトピックをご覧ください。
-   Excel については、「[Excel から SQL Server または Azure SQL Database にデータをインポートする](import-data-from-excel-to-sql.md)」をご覧ください。
-   JSON については、「[JSON ドキュメントのインポート](../json/import-json-documents-into-sql-server.md)」をご覧ください。
-   XML については、「[XML ドキュメントのインポートおよびエクスポート](examples-of-bulk-import-and-export-of-xml-documents-sql-server.md)」をご覧ください。
-   Azure Blob Storage については、「[Azure BLOB ストレージからのインポートおよびエクスポート](examples-of-bulk-access-to-data-in-azure-blob-storage.md)」をご覧ください。

## <a name="next-steps"></a>次のステップ
インポートまたはエクスポート タスクをどこから始めればよいかわからない場合は、SQL Server インポートおよびエクスポート ウィザードを検討してください。 概要については、「[簡単な例によるインポートおよびエクスポート ウィザードの概要](../../integration-services/import-export-data/get-started-with-this-simple-example-of-the-import-and-export-wizard.md)」をご覧ください。