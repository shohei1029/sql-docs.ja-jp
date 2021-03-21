---
title: bcp による一括データのインポートおよびエクスポート
description: bcp を使用して、SELECT が機能する SQL Server データベース内の任意の場所からデータをエクスポートします。 テーブルまたはクエリからデータを一括エクスポートし、ファイルから一括インポートします。
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.technology: data-movement
ms.topic: conceptual
helpviewer_keywords:
- bulk exporting [SQL Server], bcp utility
- bulk importing [SQL Server], bcp utility
- bcp utility [SQL Server], about bcp utility
ms.assetid: 73e949de-67a3-4c84-9735-7da1ad4ba34a
author: markingmyname
ms.author: maghan
ms.reviewer: ''
ms.date: 09/28/2016
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.custom: seo-lt-2019
ms.openlocfilehash: f2186cea32347c0e5418c2360371a2985f2ac9d6
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104749662"
---
# <a name="import-and-export-bulk-data-using-bcp-sql-server"></a>bcp を使用した一括データのインポートおよびエクスポート (SQL Server)

[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

このトピックでは、 [bcp ユーティリティ](../../tools/bcp-utility.md) を使用して、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] データベース内の SELECT ステートメントで指定できる任意の場所 (パーティション ビューを含む) からデータをエクスポートする方法について説明します。  
  
 bcp ユーティリティ (Bcp.exe) は、一括コピー プログラム (BCP) API を使用するコマンド ライン ツールです。 bcp ユーティリティは次のタスクを実行します。  
  
-   [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] テーブルからデータ ファイルへのデータの一括エクスポート  
  
-   クエリからのデータの一括エクスポート  
  
-   データ ファイルから [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] テーブルへのデータの一括インポート  
  
-   フォーマット ファイルの生成  
  
 bcp ユーティリティには、 **bcp** コマンドを使用してアクセスします。 **bcp** コマンドを使用してデータを一括インポートするには、テーブルのスキーマとテーブル列のデータ型を理解しておく必要があります (既存のフォーマット ファイルを使用する場合を除く)。  
  
 bcp ユーティリティでは、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] テーブルのデータを他のプログラムで使用できるようにデータ ファイルにエクスポートできます。 このユーティリティでは、別のデータベース管理システム (DBMS) など、別のプログラムのデータを [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] テーブルにインポートすることもできます。 データは、まずエクスポート元プログラムからデータ ファイルにエクスポートされ、その後に別の操作として、データ ファイルから [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] テーブルにコピーされます。  
  
 **bcp** コマンドには、データ ファイルのデータ型やその他の情報を指定するためのスイッチがあります。 これらのスイッチを指定しなかった場合は、データ ファイルに含まれているデータ フィールドの型などのフォーマット情報を要求されます。 その後、対話型の応答内容を含んだフォーマット ファイルを作成するかどうかをたずねるメッセージが表示されます。 一括インポート操作や一括エクスポート操作を後で行う場合は、フォーマット ファイルを使用すると便利です。 フォーマット ファイルは、同等のデータ ファイルに対して **bcp** コマンドを後で実行するときに指定できます。 詳細については、「[bcp を使用した互換性のためのデータ形式の指定 &#40;SQL Server&#41;](../../relational-databases/import-export/specify-data-formats-for-compatibility-when-using-bcp-sql-server.md)」を参照してください。  
  
>**メモ** bcp ユーティリティは ODBC 一括コピーを使用して書き込まれます。
  
 **bcp** コマンドの構文の説明については、「 [bcp Utility](../../tools/bcp-utility.md)」を参照してください。  
  
## <a name="examples"></a>例  

|次のトピックでは、bcp の使用例を示します。 |
|---|
|[bcp ユーティリティ](../../tools/bcp-utility.md)<br /><br />一括インポートまたは一括エクスポートのデータ形式 (SQL Server)<br />&emsp;&#9679;&emsp;[ネイティブ形式を使用したデータのインポートまたはエクスポート (SQL Server)](../../relational-databases/import-export/use-native-format-to-import-or-export-data-sql-server.md)<br />&emsp;&#9679;&emsp;[文字形式を使用したデータのインポートまたはエクスポート (SQL Server)](../../relational-databases/import-export/use-character-format-to-import-or-export-data-sql-server.md)<br />&emsp;&#9679;&emsp;[Unicode ネイティブ形式を使用したデータのインポートまたはエクスポート (SQL Server)](../../relational-databases/import-export/use-unicode-native-format-to-import-or-export-data-sql-server.md)<br />&emsp;&#9679;&emsp;[Unicode 文字形式を使用したデータのインポートまたはエクスポート (SQL Server)](../../relational-databases/import-export/use-unicode-character-format-to-import-or-export-data-sql-server.md)<br /><br />[フィールド ターミネータと行ターミネータの指定 (SQL Server)](../../relational-databases/import-export/specify-field-and-row-terminators-sql-server.md)<br /><br />[一括インポート中の NULL の保持または既定値の使用 (SQL Server)](../../relational-databases/import-export/keep-nulls-or-use-default-values-during-bulk-import-sql-server.md)<br /><br />[データの一括インポート時の ID 値の保持 (SQL Server)](../../relational-databases/import-export/keep-identity-values-when-bulk-importing-data-sql-server.md)<br /><br />データのインポートまたはエクスポート用のフォーマット ファイル (SQL Server)<br />&emsp;&#9679;&emsp;[フォーマット ファイルの作成 (SQL Server)](../../relational-databases/import-export/create-a-format-file-sql-server.md)<br />&emsp;&#9679;&emsp;[データの一括インポートでのフォーマット ファイルの使用 (SQL Server)](../../relational-databases/import-export/use-a-format-file-to-bulk-import-data-sql-server.md)<br />&emsp;&#9679;&emsp;[フォーマット ファイルを使用したテーブル列のスキップ (SQL Server)](../../relational-databases/import-export/use-a-format-file-to-skip-a-table-column-sql-server.md)<br />&emsp;&#9679;&emsp;[フォーマット ファイルを使用したデータ フィールドのスキップ (SQL Server)](../../relational-databases/import-export/use-a-format-file-to-skip-a-data-field-sql-server.md)<br />&emsp;&#9679;&emsp;[フォーマット ファイルを使用したテーブル列とデータ ファイル フィールドのマッピング (SQL Server)](../../relational-databases/import-export/use-a-format-file-to-map-table-columns-to-data-file-fields-sql-server.md)<br /><br />[XML ドキュメントの一括インポートと一括エクスポートの例 (SQL Server)](../../relational-databases/import-export/examples-of-bulk-import-and-export-of-xml-documents-sql-server.md)<br /><p>                                                                                                                                                                                                                  </p>|

## <a name="more-examples-and-information"></a>参照

- [INSERT &#40;Transact-SQL&#41;](../../t-sql/statements/insert-transact-sql.md)

- [SELECT 句 &#40;Transact-SQL&#41;](../../t-sql/queries/select-clause-transact-sql.md)

- [bcp ユーティリティ](../../tools/bcp-utility.md)

- [データの一括インポートの準備 &#40;SQL Server&#41;](../../relational-databases/import-export/prepare-to-bulk-import-data-sql-server.md)

- [BULK INSERT &#40;Transact-SQL&#41;](../../t-sql/statements/bulk-insert-transact-sql.md)

- [データの一括インポートと一括エクスポート &#40;SQL Server&#41;](../../relational-databases/import-export/bulk-import-and-export-of-data-sql-server.md)

- [OPENROWSET &#40;Transact-SQL&#41;](../../t-sql/functions/openrowset-transact-sql.md)

- [フォーマット ファイルの作成 &#40;SQL Server&#41;](../../relational-databases/import-export/create-a-format-file-sql-server.md)