---
title: 文字形式を使用したデータのインポートおよびエクスポート
description: 文字形式では、すべての列に文字データ形式が使用されます。 これは、他のプログラムを操作したり、別のデータベース ベンダーからインスタンスにコピーしたりする場合に便利です。
ms.date: 09/29/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: data-movement
ms.topic: conceptual
helpviewer_keywords:
- data formats [SQL Server], character
- character formats [SQL Server]
ms.assetid: d925e66a-1a73-43cd-bc06-1cbdf8174a4d
author: MashaMSFT
ms.author: mathoma
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.custom: seo-lt-2019
ms.openlocfilehash: cb3a2d60145ceeadd1a7d13cb8a6cb788e2a0131
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104753652"
---
# <a name="use-character-format-to-import-or-export-data-sql-server"></a>文字形式を使用したデータのインポートまたはエクスポート (SQL Server)
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]
後で別のプログラムで使われるテキスト ファイルにデータを一括エクスポートする場合や、別のプログラムにより生成されたテキスト ファイルからデータを一括インポートする場合は、文字形式の使用をお勧めします。  

文字形式では、すべての列に文字データ形式が使用されます。 データがスプレッドシートなどの別のプログラムで使用されるとき、または Oracle など別のデータベース ベンダーの製品から [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスにデータをコピーする必要があるときは、文字形式で情報を格納すると便利です。  
  
> [!NOTE]
>  [!INCLUDE[msCoName](../../includes/msconame-md.md)][!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスと、拡張文字や DBCS 文字を含まない Unicode 文字データを含むデータ ファイル間でデータの一括転送を行う場合は、Unicode 文字形式を使用します。 詳細については、「 [Unicode 文字形式を使用したデータのインポートまたはエクスポート &#40;SQL Server&#41;](../../relational-databases/import-export/use-unicode-character-format-to-import-or-export-data-sql-server.md)」をご覧ください。
  
|このトピックの内容|
|---|
|[文字形式の使用に関する注意点](#considerations)|
|[文字形式のコマンド オプション](#command_options)|
|[テスト条件の例](#etc)<br />&emsp;&#9679;&emsp;[サンプル テーブル](#sample_table)<br />&emsp;&#9679;&emsp;[XML 形式以外のフォーマット ファイルのサンプル](#nonxml_format_file)<br />|
|[使用例](#examples)<br />&emsp;&#9679;&emsp;[bcp と文字形式を使用したデータのエクスポート](#bcp_char_export)<br />&emsp;&#9679;&emsp;[フォーマット ファイルなしで bcp と文字形式を使用してデータをインポートする方法](#bcp_char_import)<br />&emsp;&#9679;&emsp;[XML 形式以外のフォーマット ファイルで bcp と文字形式を使用してデータをインポートする方法](#bcp_char_import_fmt)<br />&emsp;&#9679;&emsp;[フォーマット ファイルなしで BULK INSERT と文字形式を使用する方法](#bulk_char)<br />&emsp;&#9679;&emsp;[XML 形式以外のフォーマット ファイルで BULK INSERT と文字形式を使用する方法](#bulk_char_fmt)<br />&emsp;&#9679;&emsp;[XML 形式以外のフォーマット ファイルで OPENROWSET と文字形式を使用する方法](#openrowset_char_fmt)|
|[関連タスク](#RelatedTasks)<p>                                                                                                                                                                                                                  </p>|


  
## <a name="considerations-for-using-character-format"></a>文字形式の使用に関する注意点<a name="considerations"></a>
文字形式を使用するときは、以下の点を考慮してください。  
  
-   既定では、 [bcp ユーティリティ](../../tools/bcp-utility.md) は、文字データ フィールド間の区切りにはタブ文字を、レコードの終わりには改行文字を使用します。  別のターミネータの指定方法の詳細については、「[フィールド ターミネータと行ターミネータの指定 &#40;SQL Server&#41;](../../relational-databases/import-export/specify-field-and-row-terminators-sql-server.md)」を参照してください。  
  
-   既定では、キャラクター モードのデータの一括エクスポートまたは一括インポートを行う前に、次の変換が実行されます。  
  
    |一括操作の方向|変換|  
    |---------------------------------|----------------|  
    |エクスポート|データを文字表現に変換します。 明示的に要求された場合は、データが文字の列ごとに要求されたコード ページに変換されます。 コード ページが指定されていない場合は、文字データはクライアント コンピューターの OEM コード ページを使用して変換されます。|  
    |[インポート]|必要に応じて、文字データをネイティブ表現に変換し、クライアントのコード ページから目的の列のコード ページに変換します。|  
  
-   変換中に拡張文字が失われないようにするには、Unicode 文字形式を使用するか、コード ページを指定します。  
  
-   [sql_variant](../../t-sql/data-types/sql-variant-transact-sql.md) データが文字形式ファイルに保存される場合は、メタデータなしで保存されます。 各データ値は、暗黙的なデータ変換の規則に従って [char](../../t-sql/data-types/char-and-varchar-transact-sql.md) 形式に変換されます。 [sql_variant](../../t-sql/data-types/sql-variant-transact-sql.md) 型の列にインポートされるときは、 [char](../../t-sql/data-types/char-and-varchar-transact-sql.md)型のデータとしてインポートされます。 [sql_variant](../../t-sql/data-types/sql-variant-transact-sql.md)型以外のデータ型の列にインポートされるときは、暗黙の変換を使用してデータが [char](../../t-sql/data-types/char-and-varchar-transact-sql.md) から変換されます。 詳細については、「[データ型の変換 &#40;データベース エンジン&#41;](../../t-sql/data-types/data-type-conversion-database-engine.md)」を参照してください。  
  
-   [bcp ユーティリティ](../../tools/bcp-utility.md) は [money](../../t-sql/data-types/money-and-smallmoney-transact-sql.md) 型の値をエクスポートする場合、コンマなどの桁区切り文字で区切らずに、小数点以下 4 桁の文字形式データ ファイルとしてエクスポートします。 たとえば、値 1,234,567.123456 を含む [money](../../t-sql/data-types/money-and-smallmoney-transact-sql.md) 型の列は、文字列 1234567.1235 としてデータ ファイルに一括エクスポートされます。  
  
## <a name="command-options-for-character-format"></a>文字形式のコマンド オプション<a name="command_options"></a>  
文字形式のデータは、[bcp](../../tools/bcp-utility.md)、[BULK INSERT](../../t-sql/statements/bulk-insert-transact-sql.md)、または [INSERT ...SELECT * FROM OPENROWSET(BULK...) を使用してテーブルにインポートできます](../../t-sql/functions/openrowset-transact-sql.md)。[bcp](../../tools/bcp-utility.md) コマンドまたは [BULK INSERT](../../t-sql/statements/bulk-insert-transact-sql.md) ステートメントの場合は、ステートメントでデータ形式を指定できます。  [INSERT ...SELECT * FROM OPENROWSET(BULK...)](../../t-sql/functions/openrowset-transact-sql.md) ステートメントの場合は、フォーマット ファイルでデータ形式を指定する必要があります。  
  
文字形式は、次のコマンド オプションでサポートされています。  
  
|command|オプション|説明|  
|-------------|------------|-----------------|  
|bcp|**-c**|bcp ユーティリティが文字データを使用するようにします。\*|  
|BULK INSERT|DATAFILETYPE **='char'**|データの一括インポート時に文字形式を使用します。|  
|OPENROWSET|該当なし|フォーマット ファイルを使用する必要があります|
  
 \* 文字 ( **-c**) データを、先行バージョンの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] クライアントと互換性のある形式で読み込むには、 **-V** スイッチを使用します。 詳細については、「 [以前のバージョンの SQL Server からのネイティブ形式データおよび文字形式データのインポート](../../relational-databases/import-export/import-native-and-character-format-data-from-earlier-versions-of-sql-server.md)」をご覧ください。  
   
> [!NOTE]
>  また、フォーマット ファイルでフィールドごとに形式を指定することもできます。 詳細については、「 [データのインポートまたはエクスポート用のフォーマット ファイル &#40;SQL Server&#41;](../../relational-databases/import-export/format-files-for-importing-or-exporting-data-sql-server.md)」を参照してください。

## <a name="example-test-conditions"></a>テスト条件の例<a name="etc"></a>  
このトピックの例は、以下に定義されたテーブルとフォーマット ファイルに基づいています。

### <a name="sample-table"></a>**サンプル テーブル**<a name="sample_table"></a>
次のスクリプトは、 `myChar` という名前のテーブルのテスト データベースを作成し、テーブルにいくつかの初期値を設定します。  Microsoft [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] (SSMS) で次の Transact-SQL を実行します。

```sql
CREATE DATABASE TestDatabase;
GO

USE TestDatabase;
CREATE TABLE dbo.myChar ( 
   PersonID smallint NOT NULL,
   FirstName varchar(25) NOT NULL,
   LastName varchar(30) NOT NULL,
   BirthDate date,
   AnnualSalary money
   );

-- Populate table
INSERT TestDatabase.dbo.myChar
VALUES 
(1, 'Anthony', 'Grosse', '1980-02-23', 65000.00),
(2, 'Alica', 'Fatnowna', '1963-11-14', 45000.00),
(3, 'Stella', 'Rossenhain', '1992-03-02', 120000.00);

-- Review Data
SELECT * FROM TestDatabase.dbo.myChar;
```

### <a name="sample-non-xml-format-file"></a>**XML 形式以外のフォーマット ファイルのサンプル**<a name="nonxml_format_file"></a>
SQL Server は、非 XML 形式と XML 形式の 2 種類のフォーマット ファイルをサポートしています。  XML 以外のフォーマットとは、以前のバージョンの SQL Server でサポートされる従来のフォーマットです。  詳細については、「 [XML 以外のフォーマット ファイル (SQL Server)](../../relational-databases/import-export/non-xml-format-files-sql-server.md) 」を参照してください。  次のコマンドでは、 [bcp ユーティリティ](../../tools/bcp-utility.md) を使用し、 `myChar.fmt`のスキーマに基づいて XML 以外のフォーマット ファイル `myChar`を生成します。  [bcp](../../tools/bcp-utility.md) コマンドを使用してフォーマット ファイルを作成するには、 **format** 引数を指定し、データ ファイルのパスの代わりに **nul** を使用します。  format オプションには、次に示す **-f** オプションが必要です。  さらに、この例では、修飾子 **c** を使用して文字データを指定し、 **T** を使用して統合セキュリティによる信頼関係接続を指定します。  コマンド プロンプトで、次のコマンドを入力します。

```cmd
bcp TestDatabase.dbo.myChar format nul -f D:\BCP\myChar.fmt -T -c 

REM Review file
Notepad D:\BCP\myChar.fmt
```

> [!IMPORTANT]
> XML 以外のフォーマット ファイルは、キャリッジ リターン\ライン フィードで終わるようにします。  そうしないと、次のエラー メッセージが発生する可能性があります。
> 
> `SQLState = S1000, NativeError = 0`  
> `Error = [Microsoft][ODBC Driver 13 for SQL Server]I/O error while reading BCP format file`

## <a name="examples"></a>使用例<a name="examples"></a>
次の例では、上記で作成したデータベースとフォーマット ファイルを使用します。

### <a name="using-bcp-and-character-format-to-export-data"></a>**bcp と文字形式を使用したデータのエクスポート**<a name="bcp_char_export"></a>
**-c** スイッチと **OUT** コマンドです。  注: この例で作成するデータ ファイルをその後のすべての例で使用します。  コマンド プロンプトで、次のコマンドを入力します。

```cmd
bcp TestDatabase.dbo.myChar OUT D:\BCP\myChar.bcp -T -c

REM Review results
NOTEPAD D:\BCP\myChar.bcp
```

### <a name="using-bcp-and-character-format-to-import-data-without-a-format-file"></a>**フォーマット ファイルなしで bcp と文字形式を使用してデータをインポートする方法**<a name="bcp_char_import"></a>
**-c** スイッチと **IN** コマンドです。  コマンド プロンプトで、次のコマンドを入力します。

```cmd
REM Truncate table (for testing)
SQLCMD -Q "TRUNCATE TABLE TestDatabase.dbo.myChar;"

REM Import data
bcp TestDatabase.dbo.myChar IN D:\BCP\myChar.bcp -T -c

REM Review results
SQLCMD -Q "SELECT * FROM TestDatabase.dbo.myChar;"
```

### <a name="using-bcp-and-character-format-to-import-data-with-a-non-xml-format-file"></a>**XML 形式以外のフォーマット ファイルで bcp と文字形式を使用してデータをインポートする方法**<a name="bcp_char_import_fmt"></a>
**-c** および **-f** スイッチと **IN** コマンドです。  コマンド プロンプトで、次のコマンドを入力します。

```cmd
REM Truncate table (for testing)
SQLCMD -Q "TRUNCATE TABLE TestDatabase.dbo.myChar;"

REM Import data
bcp TestDatabase.dbo.myChar IN D:\BCP\myChar.bcp -f D:\BCP\myChar.fmt -T

REM Review results
SQLCMD -Q "SELECT * FROM TestDatabase.dbo.myChar;"
```

### <a name="using-bulk-insert-and-character-format-without-a-format-file"></a>**フォーマット ファイルなしで BULK INSERT と文字形式を使用する方法**<a name="bulk_char"></a>
**DATAFILETYPE** 引数です。  Microsoft [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] (SSMS) で次の Transact-SQL を実行します。

```sql
TRUNCATE TABLE TestDatabase.dbo.myChar; -- for testing
BULK INSERT TestDatabase.dbo.myChar
    FROM 'D:\BCP\myChar.bcp'
    WITH (
        DATAFILETYPE = 'Char'
        );

-- review results
SELECT * FROM TestDatabase.dbo.myChar;
```

### <a name="using-bulk-insert-and-character-format-with-a-non-xml-format-file"></a>**XML 形式以外のフォーマット ファイルで BULK INSERT と文字形式を使用する方法**<a name="bulk_char_fmt"></a>
**FORMATFILE** 引数。  Microsoft [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] (SSMS) で次の Transact-SQL を実行します。

```sql
TRUNCATE TABLE TestDatabase.dbo.myChar; -- for testing
BULK INSERT TestDatabase.dbo.myChar
   FROM 'D:\BCP\myChar.bcp'
   WITH (
        FORMATFILE = 'D:\BCP\myChar.fmt'
        );

-- review results
SELECT * FROM TestDatabase.dbo.myChar;
```

### <a name="using-openrowset-and-character-format-with-a-non-xml-format-file"></a>**XML 形式以外のフォーマット ファイルで OPENROWSET と文字形式を使用する方法**<a name="openrowset_char_fmt"></a>
**FORMATFILE** 引数。  Microsoft [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] (SSMS) で次の Transact-SQL を実行します。

```sql
TRUNCATE TABLE TestDatabase.dbo.myChar;  -- for testing
INSERT INTO TestDatabase.dbo.myChar
    SELECT *
    FROM OPENROWSET (
        BULK 'D:\BCP\myChar.bcp', 
        FORMATFILE = 'D:\BCP\myChar.fmt'  
        ) AS t1;

-- review results
SELECT * FROM TestDatabase.dbo.myChar;
```
  
## <a name="related-tasks"></a>関連タスク<a name="RelatedTasks"></a>  
一括インポートまたは一括エクスポートのデータ形式を使用するには 
  
-   [以前のバージョンの SQL Server からのネイティブ形式データおよび文字形式データのインポート](../../relational-databases/import-export/import-native-and-character-format-data-from-earlier-versions-of-sql-server.md)  
  
-   [ネイティブ形式を使用したデータのインポートまたはエクスポート &#40;SQL Server&#41;](../../relational-databases/import-export/use-native-format-to-import-or-export-data-sql-server.md)  
  
-   [Unicode 文字形式を使用したデータのインポートまたはエクスポート &#40;SQL Server&#41;](../../relational-databases/import-export/use-unicode-character-format-to-import-or-export-data-sql-server.md)  
  
-   [Unicode ネイティブ形式を使用したデータのインポートまたはエクスポート &#40;SQL Server&#41;](../../relational-databases/import-export/use-unicode-native-format-to-import-or-export-data-sql-server.md)  
  
## <a name="see-also"></a>参照  
 [bcp ユーティリティ](../../tools/bcp-utility.md)   
 [BULK INSERT &#40;Transact-SQL&#41;](../../t-sql/statements/bulk-insert-transact-sql.md)   
 [OPENROWSET &#40;Transact-SQL&#41;](../../t-sql/functions/openrowset-transact-sql.md)   
 [データ型 &#40;Transact-SQL&#41;](../../t-sql/data-types/data-types-transact-sql.md)   
 [以前のバージョンの SQL Server からのネイティブ形式データおよび文字形式データのインポート](../../relational-databases/import-export/import-native-and-character-format-data-from-earlier-versions-of-sql-server.md)  
  
  
