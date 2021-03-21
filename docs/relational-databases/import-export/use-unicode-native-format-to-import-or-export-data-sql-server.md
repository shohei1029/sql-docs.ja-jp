---
title: Unicode ネイティブ形式を使用したデータのインポートまたはエクスポート (SQL Server) | Microsoft Docs
description: SQL Server のインスタンス間でデータを一括転送するには、Unicode ネイティブ形式を使用します。これにより、文字形式との間でデータ型を変換することがなくなります。
ms.custom: ''
ms.date: 09/30/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: data-movement
ms.topic: conceptual
helpviewer_keywords:
- Unicode [SQL Server], bulk importing and exporting
- data formats [SQL Server], Unicode native
ms.assetid: a6213308-f3d5-406e-9029-19d8bb3367f3
author: MashaMSFT
ms.author: mathoma
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 623877af2ee11eb95a3cb75f2d661bf8017a80a1
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104748502"
---
# <a name="use-unicode-native-format-to-import-or-export-data-sql-server"></a>Unicode ネイティブ形式を使用したデータのインポートまたはエクスポート (SQL Server)
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]
Unicode ネイティブ形式は、[!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインストール環境間で情報をコピーする必要がある場合に役立ちます。 非文字データに対してネイティブ形式を使用すると、時間を節約でき、文字形式との間でデータ型の不要な変換が行われなくなります。 すべての文字データに対して Unicode 文字形式を使用すると、異なるコード ページを使用している複数のサーバー間でデータを一括転送するときに、拡張文字の損失を防ぐことができます。 Unicode ネイティブ形式のデータ ファイルは、すべての一括インポート方法で読み取ることができます。  
  
 拡張文字や DBCS 文字を含むデータ ファイルを使用して [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の複数のインスタンス間でデータを一括転送する場合は、Unicode ネイティブ形式を使用することをお勧めします。 非文字データの場合、Unicode ネイティブ形式ではネイティブ (データベース) データ型が使用されます。 [char](../../t-sql/data-types/char-and-varchar-transact-sql.md)、 [nchar](../../t-sql/data-types/nchar-and-nvarchar-transact-sql.md)、 [varchar](../../t-sql/data-types/char-and-varchar-transact-sql.md)、 [nvarchar](../../t-sql/data-types/nchar-and-nvarchar-transact-sql.md)、 [text](../../t-sql/data-types/ntext-text-and-image-transact-sql.md)、 [varchar(max)](../../t-sql/data-types/char-and-varchar-transact-sql.md)、 [nvarchar(max)](../../t-sql/data-types/nchar-and-nvarchar-transact-sql.md)、 [ntext](../../t-sql/data-types/ntext-text-and-image-transact-sql.md)などの文字データの場合、Unicode ネイティブ形式では Unicode 文字データ形式が使用されます。  
  
 Unicode ネイティブ形式のデータ ファイルに SQLVARIANT として格納される [sql_variant](../../t-sql/data-types/sql-variant-transact-sql.md) データは、ネイティブ形式のデータ ファイルに格納される場合と同様に動作します。ただし、 [char](../../t-sql/data-types/char-and-varchar-transact-sql.md) と [varchar](../../t-sql/data-types/char-and-varchar-transact-sql.md) の値がそれぞれ [nchar](../../t-sql/data-types/nchar-and-nvarchar-transact-sql.md) と [nvarchar](../../t-sql/data-types/nchar-and-nvarchar-transact-sql.md)に変換される点を除きます。この場合、影響を受ける列で 2 倍のストレージが必要になります。 元のメタデータは保持され、値はテーブル列に一括インポートされるときに、元の [char](../../t-sql/data-types/char-and-varchar-transact-sql.md) データ型や [varchar](../../t-sql/data-types/char-and-varchar-transact-sql.md) データ型に再び変換されます。  
 
 |このトピックの内容|
|---|
|[Unicode ネイティブ形式のコマンド オプション](#command_options)|
|[テスト条件の例](#etc)<br />&emsp;&#9679;&emsp;[サンプル テーブル](#sample_table)<br />&emsp;&#9679;&emsp;[XML 形式以外のフォーマット ファイルのサンプル](#nonxml_format_file)|
|[使用例](#examples)<br />&emsp;&#9679;&emsp;[bcp と Unicode ネイティブ形式を使用したデータのエクスポート](#bcp_widenative_export)<br />&emsp;&#9679;&emsp;[フォーマット ファイルなしで bcp と Unicode ネイティブ形式を使用してデータをインポートする方法](#bcp_widenative_import)<br />&emsp;&#9679;&emsp;[XML 形式以外のフォーマット ファイルで bcp と Unicode ネイティブ形式を使用してデータをインポートする方法](#bcp_widenative_import_fmt)<br />&emsp;&#9679;&emsp;[フォーマット ファイルなしで BULK INSERT と Unicode ネイティブ形式を使用する方法](#bulk_widenative)<br />&emsp;&#9679;&emsp;[XML 形式以外のフォーマット ファイルで BULK INSERT と Unicode ネイティブ形式を使用する方法](#bulk_widenative_fmt)<br />&emsp;&#9679;&emsp;[XML 形式以外のフォーマット ファイルで OPENROWSET と Unicode ネイティブ形式を使用する方法](#openrowset_widenative_fmt)|
|[関連タスク](#RelatedTasks)<p>                                                                                                                                                                                                                  </p>|
  
## <a name="command-options-for-unicode-native-format"></a>Unicode ネイティブ形式のコマンド オプション<a name="command_options"></a>  
Unicode ネイティブ形式のデータは、[bcp](../../tools/bcp-utility.md)、[BULK INSERT](../../t-sql/statements/bulk-insert-transact-sql.md)、または [INSERT ...SELECT * FROM OPENROWSET(BULK...) を使用してテーブルにインポートできます](../../t-sql/functions/openrowset-transact-sql.md)。[bcp](../../tools/bcp-utility.md) コマンドまたは [BULK INSERT](../../t-sql/statements/bulk-insert-transact-sql.md) ステートメントの場合は、ステートメントでデータ形式を指定できます。  [INSERT ...SELECT * FROM OPENROWSET(BULK...)](../../t-sql/functions/openrowset-transact-sql.md) ステートメントの場合は、フォーマット ファイルでデータ形式を指定する必要があります。  
  
Unicode ネイティブ形式は、次のコマンド オプションでサポートされています。  
  
|command|オプション|説明|  
|-------------|------------|-----------------|  
|bcp|**-N**|**bcp** ユーティリティで Unicode ネイティブ形式が使用されるようにします。Unicode ネイティブ形式では、すべての非文字データに対してネイティブ (データベース) データ型が使用され、すべての文字 (**char**、 **nchar**、 **varchar**、 **nvarchar**、 **text**、 **ntext**) データに対して Unicode 文字データ形式が使用されます。|  
|BULK INSERT|DATAFILETYPE **='widenative'**|データの一括インポート時に Unicode ネイティブ形式を使用します。|  
|OPENROWSET|該当なし|フォーマット ファイルを使用する必要があります|
    
> [!NOTE]
>  また、フォーマット ファイルでフィールドごとに形式を指定することもできます。 詳細については、「 [データのインポートまたはエクスポート用のフォーマット ファイル &#40;SQL Server&#41;](../../relational-databases/import-export/format-files-for-importing-or-exporting-data-sql-server.md)」を参照してください。
  
## <a name="example-test-conditions"></a>テスト条件の例<a name="etc"></a>  
このトピックの例は、以下に定義されたテーブルとフォーマット ファイルに基づいています。

### <a name="sample-table"></a>**サンプル テーブル**<a name="sample_table"></a>
次のスクリプトは、 `myWidenative` という名前のテーブルのテスト データベースを作成し、テーブルにいくつかの初期値を設定します。  Microsoft [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] (SSMS) で次の Transact-SQL を実行します。
```sql
CREATE DATABASE TestDatabase;
GO

USE TestDatabase;
CREATE TABLE dbo.myWidenative ( 
    PersonID smallint NOT NULL,
    FirstName nvarchar(25) NOT NULL,
    LastName nvarchar(30) NOT NULL,
    BirthDate date,
    AnnualSalary money
);

-- Populate table
INSERT TestDatabase.dbo.myWidenative
VALUES 
(1, N'ϴAnthony', N'Grosse', '02-23-1980', 65000.00),
(2, N'❤Alica', N'Fatnowna', '11-14-1963', 45000.00),
(3, N'☎Stella', N'Rossenhain', '03-02-1992', 120000.00);

-- Review Data
SELECT * FROM TestDatabase.dbo.myWidenative;
```

### <a name="sample-non-xml-format-file"></a>**XML 形式以外のフォーマット ファイルのサンプル**<a name="nonxml_format_file"></a>
SQL Server は、非 XML 形式と XML 形式の 2 種類のフォーマット ファイルをサポートしています。  XML 以外のフォーマットとは、以前のバージョンの SQL Server でサポートされる従来のフォーマットです。  詳細については、「 [XML 以外のフォーマット ファイル (SQL Server)](../../relational-databases/import-export/non-xml-format-files-sql-server.md) 」を参照してください。  次のコマンドでは、 [bcp ユーティリティ](../../tools/bcp-utility.md) を使用し、 `myWidenative.fmt`のスキーマに基づいて XML 以外のフォーマット ファイル `myWidenative`を生成します。  [bcp](../../tools/bcp-utility.md) コマンドを使用してフォーマット ファイルを作成するには、 **format** 引数を指定し、データ ファイルのパスの代わりに **nul** を使用します。  format オプションには、次に示す **-f** オプションが必要です。  さらに、この例では、修飾子 **c** を使用して文字データを指定し、 **T** を使用して統合セキュリティによる信頼関係接続を指定します。  コマンド プロンプトで、次のコマンドを入力します。

```
bcp TestDatabase.dbo.myWidenative format nul -f D:\BCP\myWidenative.fmt -T -N

REM Review file
Notepad D:\BCP\myWidenative.fmt
```

> [!IMPORTANT]
> XML 以外のフォーマット ファイルは、キャリッジ リターン\ライン フィードで終わるようにします。  そうしないと、次のエラー メッセージが発生する可能性があります。
> 
> `SQLState = S1000, NativeError = 0`  
> `Error = [Microsoft][ODBC Driver 13 for SQL Server]I/O error while reading BCP format file`

## <a name="examples"></a>使用例<a name="examples"></a>
次の例では、上記で作成したデータベースとフォーマット ファイルを使用します。

### <a name="using-bcp-and-unicode-native-format-to-export-data"></a>**bcp と Unicode ネイティブ形式を使用したデータのエクスポート**<a name="bcp_widenative_export"></a>
**-N** スイッチと **OUT** コマンドです。  注: この例で作成するデータ ファイルをその後のすべての例で使用します。  コマンド プロンプトで、次のコマンドを入力します。
```
bcp TestDatabase.dbo.myWidenative OUT D:\BCP\myWidenative.bcp -T -N

REM Review results
NOTEPAD D:\BCP\myWidenative.bcp
```

### <a name="using-bcp-and-unicode-native-format-to-import-data-without-a-format-file"></a>**フォーマット ファイルなしで bcp と Unicode ネイティブ形式を使用してデータをインポートする方法**<a name="bcp_widenative_import"></a>
**-N** スイッチと **IN** コマンドです。  コマンド プロンプトで、次のコマンドを入力します。
```
REM Truncate table (for testing)
SQLCMD -Q "TRUNCATE TABLE TestDatabase.dbo.myWidenative;"

REM Import data
bcp TestDatabase.dbo.myWidenative IN D:\BCP\myWidenative.bcp -T -N

REM Review results is SSMS
```

### <a name="using-bcp-and-unicode-native-format-to-import-data-with-a-non-xml-format-file"></a>**XML 形式以外のフォーマット ファイルで bcp と Unicode ネイティブ形式を使用してデータをインポートする方法**<a name="bcp_widenative_import_fmt"></a>
**-N** および **-f** スイッチと **IN** コマンドです。  コマンド プロンプトで、次のコマンドを入力します。
```
REM Truncate table (for testing)
SQLCMD -Q "TRUNCATE TABLE TestDatabase.dbo.myWidenative;"

REM Import data
bcp TestDatabase.dbo.myWidenative IN D:\BCP\myWidenative.bcp -f D:\BCP\myWidenative.fmt -T -N

REM Review results is SSMS
```

### <a name="using-bulk-insert-and-unicode-native-format-without-a-format-file"></a>**フォーマット ファイルなしで BULK INSERT と Unicode ネイティブ形式を使用する方法**<a name="bulk_widenative"></a>
**DATAFILETYPE** 引数です。  Microsoft [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] (SSMS) で次の Transact-SQL を実行します。
```sql
TRUNCATE TABLE TestDatabase.dbo.myWidenative; -- for testing
BULK INSERT TestDatabase.dbo.myWidenative
    FROM 'D:\BCP\myWidenative.bcp'
    WITH (
        DATAFILETYPE = 'widenative'
        );

-- review results
SELECT * FROM TestDatabase.dbo.myWidenative;
```

### <a name="using-bulk-insert-and-unicode-native-format-with-a-non-xml-format-file"></a>**XML 形式以外のフォーマット ファイルで BULK INSERT と Unicode ネイティブ形式を使用する方法**<a name="bulk_widenative_fmt"></a>
**FORMATFILE** 引数。  Microsoft [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] (SSMS) で次の Transact-SQL を実行します。
```sql
TRUNCATE TABLE TestDatabase.dbo.myWidenative; -- for testing
BULK INSERT TestDatabase.dbo.myWidenative
   FROM 'D:\BCP\myWidenative.bcp'
   WITH (
        FORMATFILE = 'D:\BCP\myWidenative.fmt'
        );

-- review results
SELECT * FROM TestDatabase.dbo.myWidenative;
```

### <a name="using-openrowset-and-unicode-native-format-with-a-non-xml-format-file"></a>**XML 形式以外のフォーマット ファイルで OPENROWSET と Unicode ネイティブ形式を使用する方法**<a name="openrowset_widenative_fmt"></a>
**FORMATFILE** 引数。  Microsoft [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] (SSMS) で次の Transact-SQL を実行します。
```sql
TRUNCATE TABLE TestDatabase.dbo.myWidenative;  -- for testing
INSERT INTO TestDatabase.dbo.myWidenative
    SELECT *
    FROM OPENROWSET (
        BULK 'D:\BCP\myWidenative.bcp', 
        FORMATFILE = 'D:\BCP\myWidenative.fmt'  
        ) AS t1;

-- review results
SELECT * FROM TestDatabase.dbo.myWidenative;
```

## <a name="related-tasks"></a>関連タスク<a name="RelatedTasks"></a>
一括インポートまたは一括エクスポートのデータ形式を使用するには  
-   [以前のバージョンの SQL Server からのネイティブ形式データおよび文字形式データのインポート](../../relational-databases/import-export/import-native-and-character-format-data-from-earlier-versions-of-sql-server.md)  
  
-   [文字形式を使用したデータのインポートまたはエクスポート &#40;SQL Server&#41;](../../relational-databases/import-export/use-character-format-to-import-or-export-data-sql-server.md)  
  
-   [ネイティブ形式を使用したデータのインポートまたはエクスポート &#40;SQL Server&#41;](../../relational-databases/import-export/use-native-format-to-import-or-export-data-sql-server.md)  
  
-   [Unicode 文字形式を使用したデータのインポートまたはエクスポート &#40;SQL Server&#41;](../../relational-databases/import-export/use-unicode-character-format-to-import-or-export-data-sql-server.md)  
  
## <a name="see-also"></a>参照  
 [bcp ユーティリティ](../../tools/bcp-utility.md)   
 [BULK INSERT &#40;Transact-SQL&#41;](../../t-sql/statements/bulk-insert-transact-sql.md)   
 [OPENROWSET &#40;Transact-SQL&#41;](../../t-sql/functions/openrowset-transact-sql.md)   
 [データ型 &#40;Transact-SQL&#41;](../../t-sql/data-types/data-types-transact-sql.md)  
  
  
