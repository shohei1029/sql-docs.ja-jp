---
title: Unicode 文字形式を使用したデータのインポートおよびエクスポート
description: Unicode 文字データ形式を使用すると、クライアントで使用しているコード ページとは異なるコード ページを使用して、SQL Server インスタンスからデータをエクスポートできます。
ms.date: 09/30/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: data-movement
ms.topic: conceptual
helpviewer_keywords:
- data formats [SQL Server], Unicode character
- Unicode [SQL Server], bulk importing and exporting
ms.assetid: 74342a11-c1c0-4746-b482-7f3537744a70
author: MashaMSFT
ms.author: mathoma
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.custom: seo-lt-2019
ms.openlocfilehash: 8e763f88431b2ac605a39c7bd5d43b3d51176d2a
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104751462"
---
# <a name="use-unicode-character-format-to-import-or-export-data-sql-server"></a>Unicode 文字形式を使用したデータのインポートまたはエクスポート (SQL Server)
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]
拡張文字や DBCS 文字を含むデータ ファイルを使用して [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の複数のインスタンス間でデータを一括転送する場合は、Unicode 文字形式を使用することをお勧めします。 Unicode 文字データ形式を使用すると、操作を実行するクライアントで使用しているコード ページとは異なるコード ページを使用して、サーバーからデータをエクスポートできます。 このような場合、Unicode 文字形式を使用すると、次の利点があります。  
  
* 転送元のデータと転送先のデータが Unicode データ型の場合、Unicode 文字形式を使用するとすべての文字データが保持されます。  
  
* 転送元のデータと転送先のデータが Unicode 以外のデータ型の場合、Unicode 文字形式を使用すると、転送先のデータで表現できない転送元のデータに含まれる拡張文字の損失を最小限に抑えられます。

|このトピックの内容|
|---|
|[Unicode 文字形式の使用に関する注意点](#considerations)|
|[Unicode 文字形式、bcp、フォーマット ファイルの使用に関する特別な注意点](#special_considerations)|
|[Unicode 文字形式のコマンド オプション](#command_options)|
|[テスト条件の例](#etc)<br />&emsp;&#9679;&emsp;[サンプル テーブル](#sample_table)<br />&emsp;&#9679;&emsp;[XML 形式以外のフォーマット ファイルのサンプル](#nonxml_format_file)|
|[使用例](#examples)<br />&emsp;&#9679;&emsp;[bcp と Unicode 文字形式を使用したデータのエクスポート](#bcp_widechar_export)<br />&emsp;&#9679;&emsp;[フォーマット ファイルなしで bcp と Unicode文字形式を使用してデータをインポートする方法](#bcp_widechar_import)<br />&emsp;&#9679;&emsp;[XML 形式以外のフォーマット ファイルで bcp と Unicode文字形式を使用してデータをインポートする方法](#bcp_widechar_import_fmt)<br />&emsp;&#9679;&emsp;[フォーマット ファイルなしで BULK INSERT と Unicode文字形式を使用する方法](#bulk_widechar)<br />&emsp;&#9679;&emsp;[XML 形式以外のフォーマット ファイルで BULK INSERT と Unicode 文字形式を使用する方法](#bulk_widechar_fmt)<br />&emsp;&#9679;&emsp;[XML 形式以外のフォーマット ファイルで OPENROWSET と Unicode 文字形式を使用する方法](#openrowset_widechar_fmt)|
|[関連タスク](#RelatedTasks)<p>                                                                                                                                                                                                                  </p>|
 
## <a name="considerations-for-using-unicode-character-format"></a>Unicode 文字形式の使用に関する注意点<a name="considerations"></a>
Unicode 文字形式を使用するときは、以下の点をご考慮ください。  

* 既定では、 [bcp ユーティリティ](../../tools/bcp-utility.md) は、文字データ フィールド間の区切りにはタブ文字を、レコードの終わりには改行文字を使用します。  別のターミネータの指定方法の詳細については、「[フィールド ターミネータと行ターミネータの指定 &#40;SQL Server&#41;](../../relational-databases/import-export/specify-field-and-row-terminators-sql-server.md)」を参照してください。

* Unicode 文字形式のデータ ファイルに格納されている [sql_variant](../../t-sql/data-types/sql-variant-transact-sql.md) データの動作は、 [char](../../t-sql/data-types/nchar-and-nvarchar-transact-sql.md) データではなく [nchar](../../t-sql/data-types/char-and-varchar-transact-sql.md) データとして格納されている点を除いて、文字形式のデータ ファイルの場合と同様になります。 文字形式の詳細については、「 [Collation and Unicode Support](../../relational-databases/collations/collation-and-unicode-support.md)」を参照してください。  

## <a name="special-considerations-for-using-unicode-character-format-bcp-and-a-format-file"></a>Unicode 文字形式、bcp、フォーマット ファイルの使用に関する特別な注意点<a name="special_considerations"></a>
Unicode 文字形式のデータ ファイルは、Unicode ファイルの規則に従います。  ファイルの先頭の 2 バイトは、16 進数の 0xFFFE です。  これらのバイトは、バイト順マーク (BOM) としての役割を果たし、高位のバイトをファイルの先頭に格納するか、最後に格納するかを指定します。  [bcp ユーティリティ](../../tools/bcp-utility.md) が BOM の解釈を間違えてインポート プロセスの一部が失敗し、次のようなエラー メッセージが表示される場合があります。
```
Starting copy...
SQLState = 22005, NativeError = 0
Error = [Microsoft][ODBC Driver 13 for SQL Server]Invalid character value for cast specification
```

次の条件下で BOM の解釈を間違える可能性があります。
* [cp ユーティリティ](../../tools/bcp-utility.md) を使用しており、 **-w** スイッチを使用して Unicode 文字を指定している

* フォーマット ファイルを使用している

* データ ファイルの最初のフィールドが文字以外である

*特定の* 状況に次の回避策のいずれかを使用できるかどうかご検討ください。
* フォーマット ファイルを使用しないでください。  この回避策の例については、以下の「 [フォーマット ファイルなしで bcp と Unicode文字形式を使用してデータをインポートする方法](#bcp_widechar_import)」をご覧ください。

* **-w** スイッチの代わりに **-c** スイッチを使用します。

* ネイティブ形式を使用して、データをもう一度エクスポートします。

* [BULK INSERT](../../t-sql/statements/bulk-insert-transact-sql.md) または [OPENROWSET](../../t-sql/functions/openrowset-transact-sql.md)を使用します。  これらの回避策の例については、以下の「 [XML 形式以外のフォーマット ファイルで BULK INSERT と Unicode 文字形式を使用する方法](#bulk_widechar_fmt) 」と「 [XML 形式以外のフォーマット ファイルで OPENROWSET と Unicode 文字形式を使用する方法](#openrowset_widechar_fmt)」をご覧ください。
 
* 転送先のテーブルに手動で最初のレコードを挿入し、 **-F 2** スイッチを使用して、2 番目のレコードからインポートを開始します。

* データ ファイルに手動でダミーの最初のレコードを挿入し、 **-F 2** スイッチを使用して、2 番目のレコードからインポートを開始します。  この回避策の例については、「 [XML 形式以外のフォーマット ファイルで bcp と Unicode文字形式を使用してデータをインポートする方法](#bcp_widechar_import_fmt)」をご覧ください。

* 最初の列が文字データ型のステージング テーブルを使用します。

* データをもう一度エクスポートし、最初のデータ フィールドが文字になるように、データ フィールドの順序を変更します。  その後、フォーマット ファイルを使用して、データ フィールドをテーブルの実際の順序にもう一度マッピングします。  例については、「 [フォーマット ファイルを使用したテーブル列とデータ ファイル フィールドのマッピング (SQL Server)](../../relational-databases/import-export/use-a-format-file-to-map-table-columns-to-data-file-fields-sql-server.md)」をご覧ください。
  
## <a name="command-options-for-unicode-character-format"></a>Unicode 文字形式のコマンド オプション<a name="command_options"></a>  
Unicode 文字形式のデータは [bcp](../../tools/bcp-utility.md)、[BULK INSERT](../../t-sql/statements/bulk-insert-transact-sql.md)、または [INSERT ...SELECT * FROM OPENROWSET(BULK...) を使用してテーブルにインポートできます](../../t-sql/functions/openrowset-transact-sql.md)。[bcp](../../tools/bcp-utility.md) コマンドまたは [BULK INSERT](../../t-sql/statements/bulk-insert-transact-sql.md) ステートメントの場合は、ステートメントでデータ形式を指定できます。  [INSERT ...SELECT * FROM OPENROWSET(BULK...)](../../t-sql/functions/openrowset-transact-sql.md) ステートメントの場合は、フォーマット ファイルでデータ形式を指定する必要があります。  
  
Unicode 文字形式は、次のコマンド オプションでサポートされています。  
  
|command|オプション|説明|  
|-------------|------------|-----------------|  
|bcp|**-w**|Unicode 文字形式を使用します。|  
|BULK INSERT|DATAFILETYPE **='widechar'**|データの一括インポート時に Unicode 文字形式を使用します。|  
|OPENROWSET|該当なし|フォーマット ファイルを使用する必要があります|
  
> [!NOTE]
>  また、フォーマット ファイルでフィールドごとに形式を指定することもできます。 詳細については、「 [データのインポートまたはエクスポート用のフォーマット ファイル &#40;SQL Server&#41;](../../relational-databases/import-export/format-files-for-importing-or-exporting-data-sql-server.md)」を参照してください。
  
## <a name="example-test-conditions"></a>テスト条件の例<a name="etc"></a>  
このトピックの例は、以下に定義されたテーブルとフォーマット ファイルに基づいています。

### <a name="sample-table"></a>**サンプル テーブル**<a name="sample_table"></a>
次のスクリプトは、 `myWidechar` という名前のテーブルのテスト データベースを作成し、テーブルにいくつかの初期値を設定します。  Microsoft [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] (SSMS) で次の Transact-SQL を実行します。
```sql
CREATE DATABASE TestDatabase;
GO

USE TestDatabase;
CREATE TABLE dbo.myWidechar ( 
    PersonID smallint NOT NULL,
    FirstName nvarchar(25) NOT NULL,
    LastName nvarchar(30) NOT NULL,
    BirthDate date,
    AnnualSalary money
);

-- Populate table
INSERT TestDatabase.dbo.myWidechar
VALUES 
(1, N'ϴAnthony', N'Grosse', '02-23-1980', 65000.00),
(2, N'❤Alica', N'Fatnowna', '11-14-1963', 45000.00),
(3, N'☎Stella', N'Rossenhain', '03-02-1992', 120000.00);

-- Review Data
SELECT * FROM TestDatabase.dbo.myWidechar;
```

### <a name="sample-non-xml-format-file"></a>**XML 形式以外のフォーマット ファイルのサンプル**<a name="nonxml_format_file"></a>
SQL Server は、非 XML 形式と XML 形式の 2 種類のフォーマット ファイルをサポートしています。  XML 以外のフォーマットとは、以前のバージョンの SQL Server でサポートされる従来のフォーマットです。  詳細については、「 [XML 以外のフォーマット ファイル (SQL Server)](../../relational-databases/import-export/non-xml-format-files-sql-server.md) 」を参照してください。  次のコマンドでは、 [bcp ユーティリティ](../../tools/bcp-utility.md) を使用し、 `myWidechar.fmt`のスキーマに基づいて XML 以外のフォーマット ファイル `myWidechar`を生成します。  [bcp](../../tools/bcp-utility.md) コマンドを使用してフォーマット ファイルを作成するには、 **format** 引数を指定し、データ ファイルのパスの代わりに **nul** を使用します。  format オプションには、次に示す **-f** オプションが必要です。  さらに、この例では、修飾子 **c** を使用して文字データを指定し、 **T** を使用して統合セキュリティによる信頼関係接続を指定します。  コマンド プロンプトで、次のコマンドを入力します。

```
bcp TestDatabase.dbo.myWidechar format nul -f D:\BCP\myWidechar.fmt -T -w

REM Review file
Notepad D:\BCP\myWidechar.fmt
```

> [!IMPORTANT]
> XML 以外のフォーマット ファイルは、キャリッジ リターン\ライン フィードで終わるようにします。  そうしないと、次のエラー メッセージが発生する可能性があります。
> 
> `SQLState = S1000, NativeError = 0`  
> `Error = [Microsoft][ODBC Driver 13 for SQL Server]I/O error while reading BCP format file`


## <a name="examples"></a>使用例<a name="examples"></a>
次の例では、上記で作成したデータベースとフォーマット ファイルを使用します。

### <a name="using-bcp-and-unicode-character-format-to-export-data"></a>**bcp と Unicode 文字形式を使用したデータのエクスポート**<a name="bcp_widechar_export"></a>
**-w** スイッチと **OUT** コマンドです。  注: この例で作成するデータ ファイルをその後のすべての例で使用します。  コマンド プロンプトで、次のコマンドを入力します。
```
bcp TestDatabase.dbo.myWidechar OUT D:\BCP\myWidechar.bcp -T -w

REM Review results
NOTEPAD D:\BCP\myWidechar.bcp
```

### <a name="using-bcp-and-unicode-character-format-to-import-data-without-a-format-file"></a>**フォーマット ファイルなしで bcp と Unicode文字形式を使用してデータをインポートする方法**<a name="bcp_widechar_import"></a>
**-w** スイッチと **IN** コマンドです。  コマンド プロンプトで、次のコマンドを入力します。
```
REM Truncate table (for testing)
SQLCMD -Q "TRUNCATE TABLE TestDatabase.dbo.myWidechar;"

REM Import data
bcp TestDatabase.dbo.myWidechar IN D:\BCP\myWidechar.bcp -T -w

REM Review results is SSMS
```

### <a name="using-bcp-and-unicode-character-format-to-import-data-with-a-non-xml-format-file"></a>**XML 形式以外のフォーマット ファイルで bcp と Unicode文字形式を使用してデータをインポートする方法**<a name="bcp_widechar_import_fmt"></a>
**-w** および **-f** スイッチと **IN** コマンドです。  この例は bcp、フォーマット ファイル、Unicode 文字を使用し、かつデータ ファイル内の最初のデータ フィールドが文字でないため、回避策を実行する必要があります。  上記の「 [Unicode 文字形式、bcp、フォーマット ファイルの使用に関する特別な注意点](#special_considerations)」をご覧ください。  データ ファイル `myWidechar.bcp` は、"ダミー" のレコードとして追加のレコードを追加して変更されます。このレコードはその後、`-F 2` スイッチでスキップされます。

コマンド プロンプトで次のコマンドを入力し、変更手順を実行します。
```
REM Truncate table (for testing)
SQLCMD -Q "TRUNCATE TABLE TestDatabase.dbo.myWidechar;"

REM Open data file
Notepad D:\BCP\myWidechar.bcp
REM Copy first record and then paste as new first record.  This additional record is the "dummy" record.
REM Close file.

REM Import data instructing bcp to skip dummy record with the -F 2 switch.
bcp TestDatabase.dbo.myWidechar IN D:\BCP\myWidechar.bcp -f D:\BCP\myWidechar.fmt -T -F 2

REM Review results is SSMS

REM Return data file to original state for usage in other examples
bcp TestDatabase.dbo.myWidechar OUT D:\BCP\myWidechar.bcp -T -w
```
  
### <a name="using-bulk-insert-and-unicode-character-format-without-a-format-file"></a>**フォーマット ファイルなしで BULK INSERT と Unicode文字形式を使用する方法**<a name="bulk_widechar"></a>
**DATAFILETYPE** 引数です。  Microsoft [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] (SSMS) で次の Transact-SQL を実行します。
```sql
TRUNCATE TABLE TestDatabase.dbo.myWidechar; -- for testing
BULK INSERT TestDatabase.dbo.myWidechar
    FROM 'D:\BCP\myWidechar.bcp'
    WITH (
        DATAFILETYPE = 'widechar'
        );

-- review results
SELECT * FROM TestDatabase.dbo.myWidechar;
```
  
### <a name="using-bulk-insert-and-unicode-character-format-with-a-non-xml-format-file"></a>**XML 形式以外のフォーマット ファイルで BULK INSERT と Unicode 文字形式を使用する方法**<a name="bulk_widechar_fmt"></a>
**FORMATFILE** 引数。  Microsoft [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] (SSMS) で次の Transact-SQL を実行します。
```sql
TRUNCATE TABLE TestDatabase.dbo.myWidechar; -- for testing
BULK INSERT TestDatabase.dbo.myWidechar
   FROM 'D:\BCP\myWidechar.bcp'
   WITH (
        FORMATFILE = 'D:\BCP\myWidechar.fmt'
        );

-- review results
SELECT * FROM TestDatabase.dbo.myWidechar;
```
  
### <a name="using-openrowset-and-unicode-character-format-with-a-non-xml-format-file"></a>**XML 形式以外のフォーマット ファイルで OPENROWSET と Unicode 文字形式を使用する方法**<a name="openrowset_widechar_fmt"></a>
**FORMATFILE** 引数。  Microsoft [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] (SSMS) で次の Transact-SQL を実行します。
```sql
TRUNCATE TABLE TestDatabase.dbo.myWidechar;  -- for testing
INSERT INTO TestDatabase.dbo.myWidechar
    SELECT *
    FROM OPENROWSET (
        BULK 'D:\BCP\myWidechar.bcp', 
        FORMATFILE = 'D:\BCP\myWidechar.fmt'  
        ) AS t1;

-- review results
SELECT * FROM TestDatabase.dbo.myWidechar;
```
 
  
## <a name="related-tasks"></a>関連タスク<a name="RelatedTasks"></a>
一括インポートまたは一括エクスポートのデータ形式を使用するには  
-   [以前のバージョンの SQL Server からのネイティブ形式データおよび文字形式データのインポート](../../relational-databases/import-export/import-native-and-character-format-data-from-earlier-versions-of-sql-server.md)  
  
-   [文字形式を使用したデータのインポートまたはエクスポート &#40;SQL Server&#41;](../../relational-databases/import-export/use-character-format-to-import-or-export-data-sql-server.md)  
  
-   [ネイティブ形式を使用したデータのインポートまたはエクスポート &#40;SQL Server&#41;](../../relational-databases/import-export/use-native-format-to-import-or-export-data-sql-server.md)  
  
-   [Unicode ネイティブ形式を使用したデータのインポートまたはエクスポート &#40;SQL Server&#41;](../../relational-databases/import-export/use-unicode-native-format-to-import-or-export-data-sql-server.md)  
  
## <a name="see-also"></a>参照  
 [bcp ユーティリティ](../../tools/bcp-utility.md)   
 [BULK INSERT &#40;Transact-SQL&#41;](../../t-sql/statements/bulk-insert-transact-sql.md)   
 [OPENROWSET &#40;Transact-SQL&#41;](../../t-sql/functions/openrowset-transact-sql.md)   
 [データ型 &#40;Transact-SQL&#41;](../../t-sql/data-types/data-types-transact-sql.md)   
 [照合順序と Unicode のサポート](../../relational-databases/collations/collation-and-unicode-support.md)  
  
  
