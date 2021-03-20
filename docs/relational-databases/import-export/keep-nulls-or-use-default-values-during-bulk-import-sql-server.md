---
title: 一括インポート中の NULL または既定値の保持
description: SQL Server での一括インポートの場合、bcp と BULK INSERT の両方で NULL 値を置き換える既定値が読み込まれます。 どちらの場合も、NULL 値を保持することを選択できます。
ms.date: 09/20/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: data-movement
ms.topic: conceptual
helpviewer_keywords:
- bulk importing [SQL Server], null values
- bulk importing [SQL Server], default values
- data formats [SQL Server], null values
- bulk rowset providers [SQL Server]
- bcp utility [SQL Server], null values
- BULK INSERT statement
- default values
- OPENROWSET function, bulk importing
- data formats [SQL Server], default values
ms.assetid: 6b91d762-337b-4345-a159-88abb3e64a81
author: MashaMSFT
ms.author: mathoma
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.custom: seo-lt-2019
ms.openlocfilehash: 724b8c0df07cac555158661db6d00ccf98e7134c
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104752542"
---
# <a name="keep-nulls-or-default-values-during-bulk-import-sql-server"></a>一括インポート中の NULL または既定値の保持 (SQL Server)
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

既定では、データをテーブルにインポートするとき、 [bcp](../../tools/bcp-utility.md) コマンドと [BULK INSERT](../../t-sql/statements/bulk-insert-transact-sql.md) ステートメントによって、テーブルの列に対して定義されているすべての既定値が監視されます。  たとえば、データ ファイルに NULL フィールドがある場合は、NULL 値の代わりにその列の既定値が読み込まれます。  [bcp](../../tools/bcp-utility.md) コマンドと [BULK INSERT](../../t-sql/statements/bulk-insert-transact-sql.md) ステートメントの両方で、NULL 値を保持することを指定することもできます。

これに対し、通常の INSERT ステートメントでは、既定値が挿入されるのではなく、NULL 値が保持されます。 INSERT ...SELECT * FROM [OPENROWSET(BULK...)](../../t-sql/functions/openrowset-transact-sql.md) ステートメントでは、通常の INSERT と同じ基本的な動作に加えて、既定値を挿入するための[テーブル ヒント](../../t-sql/queries/hints-transact-sql-table.md)がサポートされます。

|[外枠]|
|---|
|[Null 値を維持する](#keep_nulls)<br />[既定値と INSERT ... を使用するSELECT * FROM OPENROWSET(BULK...)](#keep_default)<br />[テスト条件の例](#etc)<br />&emsp;&#9679;&emsp;[サンプル テーブル](#sample_table)<br />&emsp;&#9679;&emsp;[サンプル データ ファイル](#sample_data_file)<br />&emsp;&#9679;&emsp;[XML 形式以外のフォーマット ファイルのサンプル](#nonxml_format_file)<br />[一括インポート中の NULL の保持または既定値の使用](#import_data)<br />&emsp;&#9679;&emsp;[フォーマット ファイルなしで bcp を使用して Null 値を維持する方法](#bcp_null)<br />&emsp;&#9679;&emsp;[XML 形式以外のフォーマット ファイルで bcp を使用して Null 値を維持する方法](#bcp_null_fmt)<br />&emsp;&#9679;&emsp;[フォーマット ファイルなしで bcp と既定値を使用する方法](#bcp_default)<br />&emsp;&#9679;&emsp;[XML 形式以外のフォーマット ファイルで bcp と既定値を使用する方法](#bcp_default_fmt)<br />&emsp;&#9679;&emsp;[フォーマット ファイルなしで BULK INSERT を使用して Null 値を維持する方法](#bulk_null)<br />&emsp;&#9679;&emsp;[XML 形式以外のフォーマット ファイルで BULK INSERT を使用して Null 値を維持する方法](#bulk_null_fmt)<br />&emsp;&#9679;&emsp;[フォーマット ファイルなしで BULK INSERT と既定値を使用する方法](#bulk_default)<br />&emsp;&#9679;&emsp;[XML 形式以外のフォーマット ファイルで BULK INSERT と既定値を使用する方法](#bulk_default_fmt)<br />&emsp;&#9679;&emsp;[XML 形式以外のフォーマット ファイルで OPENROWSET(BULK...) を使用して Null 値を維持する方法](#openrowset__null_fmt)<br />&emsp;&#9679;&emsp;[XML 形式以外のフォーマット ファイルで OPENROWSET(BULK...) と既定値を使用する方法](#openrowset__default_fmt)

## <a name="keeping-null-values"></a>Null 値を維持する<a name="keep_nulls"></a>  
以下の修飾子は、一括インポート操作中、テーブル列の既定値がある場合にその既定値を継承するのではなく、データ ファイルの空のフィールドにそのフィールドの NULL 値を保持することを指定しています。  [OPENROWSET](../../t-sql/functions/openrowset-transact-sql.md)の場合、既定では、一括読み込み操作で指定されていないすべての列が NULL に設定されます。
  
|command|Qualifier|修飾子の種類|  
|-------------|---------------|--------------------|  
|bcp|-k|Switch|  
|BULK INSERT|KEEPNULLS\*|引数|  
|INSERT ...SELECT * FROM OPENROWSET(BULK...)|該当なし|該当なし|  
  
\*[BULK INSERT](../../t-sql/statements/bulk-insert-transact-sql.md)では、既定値を使用できない場合、NULL 値を許容するようにテーブル列を定義する必要があります。 
  
> [!NOTE]
> 上記の修飾子は、一括インポート コマンドによるテーブルでの DEFAULT 定義の確認を無効にします。  ただし、同時に実行するすべての INSERT ステートメントでは、DEFAULT 定義が必要です。
 
## <a name="using-default-values-with-insert--select--from-openrowsetbulk"></a>既定値と INSERT ... を使用するSELECT * FROM [OPENROWSET(BULK...)](../../t-sql/functions/openrowset-transact-sql.md)<a name="keep_default"></a>  
データ ファイルのフィールドが空の場合、対応するテーブル列に既定値があるときはその列で既定値を使用することを指定できます。  既定値を使用するには、テーブル ヒント [KEEPDEFAULTS](../../t-sql/queries/hints-transact-sql-table.md)を使用します。
 
> [!NOTE]
>  詳細については、「[INSERT &#40;Transact-SQL&#41;](../../t-sql/statements/insert-transact-sql.md)」、「[SELECT &#40;Transact-SQL&#41;](../../t-sql/queries/select-transact-sql.md)」、「[OPENROWSET &#40;Transact-SQL&#41;](../../t-sql/functions/openrowset-transact-sql.md)」、「[テーブル ヒント &#40;Transact-SQL&#41;](../../t-sql/queries/hints-transact-sql-table.md)」を参照してください。

## <a name="example-test-conditions"></a>テスト条件の例<a name="etc"></a>  
このトピックの例は、以下に定義されたテーブル、データ ファイル、およびフォーマット ファイルに基づいています。

### <a name="sample-table"></a>**サンプル テーブル**<a name="sample_table"></a>
以下のスクリプトでは、テスト データベースと `myNulls`という名前のテーブルが作成されます。  4 番目のテーブル列 `Kids`には既定値があることに注意してください。  Microsoft [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] (SSMS) で次の Transact-SQL を実行します。

```sql
CREATE DATABASE TestDatabase;
GO

USE TestDatabase;
CREATE TABLE dbo.myNulls ( 
   PersonID smallint not null,
   FirstName varchar(25),
   LastName varchar(30),
   Kids varchar(13) DEFAULT 'Default Value',
   BirthDate date
   );
```

### <a name="sample-data-file"></a>**サンプル データ ファイル**<a name="sample_data_file"></a>
メモ帳を使用して、空のファイル `D:\BCP\myNulls.bcp` を作成し、次のデータを挿入します。  4 列目の 3 つ目のレコードに値はありません。

```
1,Anthony,Grosse,Yes,1980-02-23
2,Alica,Fatnowna,No,1963-11-14
3,Stella,Rosenhain,,1992-03-02
```

また、次の PowerShell スクリプトを実行して、データ ファイルを作成および設定することもできます。

```powershell
cls
# revise directory as desired
$dir = 'D:\BCP\';

$bcpFile = $dir + 'MyNulls.bcp';

# Confirm directory exists
IF ((Test-Path -Path $dir) -eq 0)
{
    Write-Host "The path $dir does not exist; please create or modify the directory.";
    RETURN;
};

# clear content, will error if file does not exist, can be ignored
Clear-Content -Path $bcpFile -ErrorAction SilentlyContinue;

# Add data
Add-Content -Path $bcpFile -Value '1,Anthony,Grosse,Yes,1980-02-23';
Add-Content -Path $bcpFile -Value '2,Alica,Fatnowna,No,1963-11-14';
Add-Content -Path $bcpFile -Value '3,Stella,Rosenhain,,1992-03-02';

#Review content
Get-Content -Path $bcpFile;
Invoke-Item $bcpFile;
```
  
### <a name="sample-non-xml-format-file"></a>**XML 形式以外のフォーマット ファイルのサンプル**<a name="nonxml_format_file"></a>
SQL Server は、非 XML 形式と XML 形式の 2 種類のフォーマット ファイルをサポートしています。  XML 以外のフォーマットとは、以前のバージョンの SQL Server でサポートされる従来のフォーマットです。  詳細については、「 [XML 以外のフォーマット ファイル (SQL Server)](../../relational-databases/import-export/non-xml-format-files-sql-server.md) 」を参照してください。  次のコマンドでは、 [bcp ユーティリティ](../../tools/bcp-utility.md) を使用し、 `myNulls.fmt`のスキーマに基づいて XML 以外のフォーマット ファイル `myNulls`を生成します。  [bcp](../../tools/bcp-utility.md) コマンドを使用してフォーマット ファイルを作成するには、 **format** 引数を指定し、データ ファイルのパスの代わりに **nul** を使用します。  format オプションには、次に示す **-f** オプションが必要です。  さらに、この例では、修飾子 **c** を使用して文字データを指定し、 **t** を使用して [フィールド ターミネータ](../../relational-databases/import-export/specify-field-and-row-terminators-sql-server.md)としてコンマを指定し、 **T** を使用して統合セキュリティによる信頼された接続を指定します。  コマンド プロンプトで、次のコマンドを入力します。

```cmd
bcp TestDatabase.dbo.myNulls format nul -c -f D:\BCP\myNulls.fmt -t, -T

REM Review file
Notepad D:\BCP\myNulls.fmt
```

> [!IMPORTANT]
> XML 以外のフォーマット ファイルは、キャリッジ リターン\ライン フィードで終わるようにします。  そうしないと、次のエラー メッセージが発生する可能性があります。
> 
> `SQLState = S1000, NativeError = 0`  
> `Error = [Microsoft][ODBC Driver 13 for SQL Server]I/O error while reading BCP format file`

 フォーマット ファイルの作成方法の詳細については、「 [フォーマット ファイルの作成 &#40;SQL Server&#41;](../../relational-databases/import-export/create-a-format-file-sql-server.md)を使用します。  
  
## <a name="keep-nulls-or-use-default-values-during-bulk-import"></a>一括インポート中の NULL の保持または既定値の使用<a name="import_data"></a>
次の例では、データベース、データ ファイル、および上記で作成したフォーマット ファイルを使用します。

### <a name="using-bcp-and-keeping-null-values-without-a-format-file"></a>**フォーマット ファイルなしで [bcp](../../tools/bcp-utility.md) を使用して Null 値を維持する方法**<a name="bcp_null"></a>

**-k** スイッチ。  コマンド プロンプトで、次のコマンドを入力します。

```cmd
REM Truncate table (for testing)
SQLCMD -Q "TRUNCATE TABLE TestDatabase.dbo.myNulls;"

REM Import data
bcp TestDatabase.dbo.myNulls IN D:\BCP\myNulls.bcp -c -t, -T -k

REM Review results
SQLCMD -Q "SELECT * FROM TestDatabase.dbo.myNulls;"
```
  
### <a name="using-bcp-and-keeping-null-values-with-a-non-xml-format-file"></a>**フォーマット ファイルなしで [bcp](../../tools/bcp-utility.md) で [bcp](../../relational-databases/import-export/non-xml-format-files-sql-server.md)** <a name="bcp_null_fmt"></a>
**-k** スイッチと **-f** スイッチ。 コマンド プロンプトで、次のコマンドを入力します。

```cmd
REM Truncate table (for testing)
SQLCMD -Q "TRUNCATE TABLE TestDatabase.dbo.myNulls;"

REM Import data
bcp TestDatabase.dbo.myNulls IN D:\BCP\myNulls.bcp -f D:\BCP\myNulls.fmt -T -k

REM Review results
SQLCMD -Q "SELECT * FROM TestDatabase.dbo.myNulls;"
```

### <a name="using-bcp-and-using-default-values-without-a-format-file"></a>**フォーマット ファイルなしで [bcp](../../tools/bcp-utility.md) と既定値を使用する方法**<a name="bcp_default"></a>
コマンド プロンプトで、次のコマンドを入力します。

```cmd
REM Truncate table (for testing)
SQLCMD -Q "TRUNCATE TABLE TestDatabase.dbo.myNulls;"

REM Import data
bcp TestDatabase.dbo.myNulls IN D:\BCP\myNulls.bcp -c -t, -T

REM Review results
SQLCMD -Q "SELECT * FROM TestDatabase.dbo.myNulls;"
```
  
### <a name="using-bcp-and-using-default-values-with-a-non-xml-format-file"></a>**フォーマット ファイルなしで [bcp](../../tools/bcp-utility.md) で [bcp](../../relational-databases/import-export/non-xml-format-files-sql-server.md)** <a name="bcp_default_fmt"></a>
**-f** スイッチ。  コマンド プロンプトで、次のコマンドを入力します。

```cmd
REM Truncate table (for testing)
SQLCMD -Q "TRUNCATE TABLE TestDatabase.dbo.myNulls;"

REM Import data
bcp TestDatabase.dbo.myNulls IN D:\BCP\myNulls.bcp -f D:\BCP\myNulls.fmt -T

REM Review results
SQLCMD -Q "SELECT * FROM TestDatabase.dbo.myNulls;"
```

### <a name="using-bulk-insert-and-keeping-null-values-without-a-format-file"></a>**フォーマット ファイルなしで [BULK INSERT](../../t-sql/statements/bulk-insert-transact-sql.md) を使用して Null 値を維持する方法**<a name="bulk_null"></a>
**KEEPNULLS** 引数。  Microsoft [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] (SSMS) で次の Transact-SQL を実行します。

```sql
USE TestDatabase;
GO
TRUNCATE TABLE dbo.myNulls; -- for testing
BULK INSERT dbo.myNulls
    FROM 'D:\BCP\myNulls.bcp'
    WITH (
        DATAFILETYPE = 'char',  
        FIELDTERMINATOR = ',',  
        KEEPNULLS
        );

-- review results
SELECT * FROM TestDatabase.dbo.myNulls;
```

### <a name="using-bulk-insert-and-keeping-null-values-with-a-non-xml-format-file"></a>**フォーマット ファイルなしで [BULK INSERT](../../t-sql/statements/bulk-insert-transact-sql.md) で [bcp](../../relational-databases/import-export/non-xml-format-files-sql-server.md)** <a name="bulk_null_fmt"></a>
**KEEPNULLS** 引数と **FORMATFILE** 引数。  Microsoft [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] (SSMS) で次の Transact-SQL を実行します。

```sql
USE TestDatabase;
GO

TRUNCATE TABLE dbo.myNulls; -- for testing
BULK INSERT dbo.myNulls
   FROM 'D:\BCP\myNulls.bcp'
   WITH (
        FORMATFILE = 'D:\BCP\myNulls.fmt',
        KEEPNULLS
        );

-- review results
SELECT * FROM TestDatabase.dbo.myNulls;
```

### <a name="using-bulk-insert-and-using-default-values-without-a-format-file"></a>**フォーマット ファイルなしで [BULK INSERT](../../t-sql/statements/bulk-insert-transact-sql.md) と既定値を使用する方法**<a name="bulk_default"></a>
Microsoft [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] (SSMS) で次の Transact-SQL を実行します。

```sql
USE TestDatabase;
GO

TRUNCATE TABLE dbo.myNulls;  -- for testing
BULK INSERT dbo.myNulls
   FROM 'D:\BCP\myNulls.bcp'
   WITH (
      DATAFILETYPE = 'char',  
      FIELDTERMINATOR = ','
      );

-- review results
SELECT * FROM TestDatabase.dbo.myNulls;
```

### <a name="using-bulk-insert-and-using-default-values-with-a-non-xml-format-file"></a>**フォーマット ファイルなしで [BULK INSERT](../../t-sql/statements/bulk-insert-transact-sql.md) で [bcp](../../relational-databases/import-export/non-xml-format-files-sql-server.md)** <a name="bulk_default_fmt"></a>
**FORMATFILE** 引数。  Microsoft [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] (SSMS) で次の Transact-SQL を実行します。

```sql
USE TestDatabase;
GO

TRUNCATE TABLE dbo.myNulls;  -- for testing
BULK INSERT dbo.myNulls
   FROM 'D:\BCP\myNulls.bcp'
   WITH (
        FORMATFILE = 'D:\BCP\myNulls.fmt'
        );

-- review results
SELECT * FROM TestDatabase.dbo.myNulls;
```

### <a name="using-openrowsetbulk-and-keeping-null-values-with-a-non-xml-format-file"></a>**フォーマット ファイルなしで [OPENROWSET(BULK...)](../../t-sql/functions/openrowset-transact-sql.md) で [bcp](../../relational-databases/import-export/non-xml-format-files-sql-server.md)** <a name="openrowset__null_fmt"></a>
**FORMATFILE** 引数。  Microsoft [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] (SSMS) で次の Transact-SQL を実行します。

```sql
USE TestDatabase;
GO

TRUNCATE TABLE dbo.myNulls;  -- for testing
INSERT INTO dbo.myNulls
    SELECT *
    FROM OPENROWSET (
        BULK 'D:\BCP\myNulls.bcp', 
        FORMATFILE = 'D:\BCP\myNulls.fmt'  
        ) AS t1;

-- review results
SELECT * FROM TestDatabase.dbo.myNulls;
```

### <a name="using-openrowsetbulk-and-using-default-values-with-a-non-xml-format-file"></a>**フォーマット ファイルなしで [OPENROWSET(BULK...)](../../t-sql/functions/openrowset-transact-sql.md) で [bcp](../../relational-databases/import-export/non-xml-format-files-sql-server.md)** <a name="openrowset__default_fmt"></a>
**KEEPDEFAULTS** テーブル ヒントと **FORMATFILE** 引数。  Microsoft [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] (SSMS) で次の Transact-SQL を実行します。

```sql
USE TestDatabase;
GO

TRUNCATE TABLE dbo.myNulls;  -- for testing
INSERT INTO dbo.myNulls
WITH (KEEPDEFAULTS) 
    SELECT *
    FROM OPENROWSET (
        BULK 'D:\BCP\myNulls.bcp', 
        FORMATFILE = 'D:\BCP\myNulls.fmt'  
        ) AS t1;

-- review results
SELECT * FROM TestDatabase.dbo.myNulls;
```

  
##  <a name="related-tasks"></a><a name="RelatedTasks"></a> 関連タスク  
  
-   [データの一括インポート時の ID 値の保持 &#40;SQL Server&#41;](../../relational-databases/import-export/keep-identity-values-when-bulk-importing-data-sql-server.md)  
  
-   [一括エクスポートまたは一括インポートのデータの準備 &#40;SQL Server&#41;](../../relational-databases/import-export/prepare-data-for-bulk-export-or-import-sql-server.md)  
  
 **フォーマット ファイルを作成するには**  
  
-   [フォーマット ファイルの作成 &#40;SQL Server&#41;](../../relational-databases/import-export/create-a-format-file-sql-server.md)  
  
-   [データの一括インポートでのフォーマット ファイルの使用 &#40;SQL Server&#41;](../../relational-databases/import-export/use-a-format-file-to-bulk-import-data-sql-server.md)  
  
-   [フォーマット ファイルを使用したテーブル列とデータ ファイル フィールドのマッピング &#40;SQL Server&#41;](../../relational-databases/import-export/use-a-format-file-to-map-table-columns-to-data-file-fields-sql-server.md)  
  
-   [フォーマット ファイルを使用したデータ フィールドのスキップ &#40;SQL Server&#41;](../../relational-databases/import-export/use-a-format-file-to-skip-a-data-field-sql-server.md)  
  
-   [フォーマット ファイルを使用したテーブル列のスキップ &#40;SQL Server&#41;](../../relational-databases/import-export/use-a-format-file-to-skip-a-table-column-sql-server.md)  
  
 **一括インポートまたは一括エクスポートのデータ形式を使用するには**  
  
-   [以前のバージョンの SQL Server からのネイティブ形式データおよび文字形式データのインポート](../../relational-databases/import-export/import-native-and-character-format-data-from-earlier-versions-of-sql-server.md)  
  
-   [文字形式を使用したデータのインポートまたはエクスポート &#40;SQL Server&#41;](../../relational-databases/import-export/use-character-format-to-import-or-export-data-sql-server.md)  
  
-   [ネイティブ形式を使用したデータのインポートまたはエクスポート &#40;SQL Server&#41;](../../relational-databases/import-export/use-native-format-to-import-or-export-data-sql-server.md)  
  
-   [Unicode 文字形式を使用したデータのインポートまたはエクスポート &#40;SQL Server&#41;](../../relational-databases/import-export/use-unicode-character-format-to-import-or-export-data-sql-server.md)  
  
-   [Unicode ネイティブ形式を使用したデータのインポートまたはエクスポート &#40;SQL Server&#41;](../../relational-databases/import-export/use-unicode-native-format-to-import-or-export-data-sql-server.md)  
  
 **bcp を使用した互換性のためのデータ形式を指定するには**  
  
-   [フィールド ターミネータと行ターミネータの指定 &#40;SQL Server&#41;](../../relational-databases/import-export/specify-field-and-row-terminators-sql-server.md)  
  
-   [bcp を使用したデータ ファイルのプレフィックス長の指定 &#40;SQL Server&#41;](../../relational-databases/import-export/specify-prefix-length-in-data-files-by-using-bcp-sql-server.md)  
  
-   [bcp を使用したファイル ストレージ型の指定 &#40;SQL Server&#41;](../../relational-databases/import-export/specify-file-storage-type-by-using-bcp-sql-server.md)  
  
## <a name="see-also"></a>参照  
 [BACKUP &#40;Transact-SQL&#41;](../../t-sql/statements/backup-transact-sql.md)   
 [OPENROWSET &#40;Transact-SQL&#41;](../../t-sql/functions/openrowset-transact-sql.md)   
 [bcp ユーティリティ](../../tools/bcp-utility.md)   
 [BULK INSERT &#40;Transact-SQL&#41;](../../t-sql/statements/bulk-insert-transact-sql.md)   
 [テーブル ヒント &#40;Transact-SQL&#41;](../../t-sql/queries/hints-transact-sql-table.md)  
  
  
