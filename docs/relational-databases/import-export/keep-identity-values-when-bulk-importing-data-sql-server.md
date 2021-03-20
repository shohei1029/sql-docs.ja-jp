---
title: データの一括インポート時の ID 値の保持
description: ID 値を含むデータを SQL Server インスタンスに一括インポートする場合、既定では新しい値が割り当てられます。 元の値を保持することができます。
ms.date: 09/21/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: data-movement
ms.topic: conceptual
helpviewer_keywords:
- identity values [SQL Server], bulk imports
- data formats [SQL Server], identity values
- bulk importing [SQL Server], identity values
ms.assetid: 45894a3f-2d8a-4edd-9568-afa7d0d3061f
author: MashaMSFT
ms.author: mathoma
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.custom: seo-lt-2019
ms.openlocfilehash: aa721f4597836068c07e5ee969c1eeb97dd20670
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104752532"
---
# <a name="keep-identity-values-when-bulk-importing-data-sql-server"></a>データの一括インポート時の ID 値の保持 (SQL Server)
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]
ID 値を含んでいるデータ ファイルを Microsoft SQL Server のインスタンスに一括インポートできます。  既定では、インポートされたデータ ファイルの ID 列の値は無視され、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] によって固有の値が自動的に割り当てられます。  固有の値は、テーブル作成時に指定されたシード値と増分値に基づいています。

データ ファイルにテーブルの ID 列の値が含まれていない場合は、フォーマット ファイルを使用してデータをインポートするときにテーブルの ID 列をスキップするように指定します。  詳細については、「 [フォーマット ファイルを使用したテーブル列のスキップ (SQL Server)](../../relational-databases/import-export/use-a-format-file-to-skip-a-table-column-sql-server.md) 」を参照してください。

|[外枠]|
|---|
|[ID 値を維持する](#keep_identity)<br />[テスト条件の例](#etc)<br />&emsp;&#9679;&emsp;[サンプル テーブル](#sample_table)<br />&emsp;&#9679;&emsp;[サンプル データ ファイル](#sample_data_file)<br />&emsp;&#9679;&emsp;[XML 形式以外のフォーマット ファイルのサンプル](#nonxml_format_file)<br />[使用例](#examples)<br />&emsp;&#9679;&emsp;[フォーマット ファイルなしで bcp を使用して ID 値を維持する方法](#bcp_identity)<br />&emsp;&#9679;&emsp;[XML 形式以外のフォーマット ファイルで bcp を使用して ID 値を維持する方法](#bcp_identity_fmt)<br />&emsp;&#9679;&emsp;[フォーマット ファイルなしで bcp と生成される ID 値を使用する方法](#bcp_default)<br />&emsp;&#9679;&emsp;[XML 形式以外のフォーマット ファイルで bcp と生成される ID 値を使用する方法](#bcp_default_fmt)<br />&emsp;&#9679;&emsp;[フォーマット ファイルなしで BULK INSERT を使用して ID 値を維持する方法](#bulk_identity)<br />&emsp;&#9679;&emsp;[XML 形式以外のフォーマット ファイルで BULK INSERT を使用して ID 値を維持する方法](#bulk_identity_fmt)<br />&emsp;&#9679;&emsp;[フォーマット ファイルなしで BULK INSERT と生成される ID 値を使用する方法](#bulk_default)<br />&emsp;&#9679;&emsp;[XML 形式以外のフォーマット ファイルで BULK INSERT と生成される ID 値を使用する方法](#bulk_default_fmt)<br />&emsp;&#9679;&emsp;[XML 形式以外のフォーマット ファイルで OPENROWSET を使用して ID 値を維持する方法](#openrowset_identity_fmt)<br />&emsp;&#9679;&emsp;[XML 形式以外のフォーマット ファイルで OPENROWSET と生成される ID 値を使用する方法](#openrowset_default_fmt)<br /><p>                                                                                                                                                                                                                  </p>|

## <a name="keep-identity-values"></a>ID 値を維持する <a name="keep_identity"></a>  
テーブルにデータ行を一括インポートするときに [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] が ID 値を割り当てないようにするには、適切な keep-identity コマンド修飾子を使用します。  keep-identity 修飾子を指定すると、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ではデータ ファイルの ID 値を使用します。  このような修飾子は次のとおりです。

|command|Keep-identity 修飾子|修飾子の種類|  
|-------------|------------------------------|--------------------|  
|bcp|-E|Switch|  
|BULK INSERT|KEEPIDENTITY|引数|  
|INSERT ...SELECT * FROM OPENROWSET(BULK...)|KEEPIDENTITY|テーブル ヒント|  
   
 詳細については、「[bcp Utility](../../tools/bcp-utility.md)」、「[BULK INSERT &#40;Transact-SQL&#41;](../../t-sql/statements/bulk-insert-transact-sql.md)」、「[OPENROWSET &#40;Transact-SQL&#41;](../../t-sql/functions/openrowset-transact-sql.md)」、「[INSERT &#40;Transact-SQL&#41;](../../t-sql/statements/insert-transact-sql.md)」、「[SELECT &#40;Transact-SQL&#41;](../../t-sql/queries/select-transact-sql.md)」、「[Table Hints &#40;Transact-SQL&#41;](../../t-sql/queries/hints-transact-sql-table.md)」を参照してください。  

> [!NOTE]
>  複数のテーブルで使用できる自動的に増分する番号、またはテーブルを参照せずにアプリケーションから呼び出すことができる自動的に増分する番号を作成するには、「[シーケンス番号](../../relational-databases/sequence-numbers/sequence-numbers.md)」を参照してください。
 
## <a name="example-test-conditions"></a>テスト条件の例<a name="etc"></a>  
このトピックの例は、以下に定義されたテーブル、データ ファイル、およびフォーマット ファイルに基づいています。

### <a name="sample-table"></a>**サンプル テーブル**<a name="sample_table"></a>
以下のスクリプトでは、テスト データベースと `myIdentity`という名前のテーブルが作成されます。  Microsoft [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] (SSMS) で次の Transact-SQL を実行します。
```sql
CREATE DATABASE TestDatabase;
GO

USE TestDatabase;
CREATE TABLE dbo.myIdentity ( 
   PersonID smallint IDENTITY(1,1) NOT NULL,
   FirstName varchar(25) NOT NULL,
   LastName varchar(30) NOT NULL,
   BirthDate date
   );
```
 
### <a name="sample-data-file"></a>**サンプル データ ファイル**<a name="sample_data_file"></a>
メモ帳を使用して、空のファイル `D:\BCP\myIdentity.bcp` を作成し、次のデータを挿入します。  
```
3,Anthony,Grosse,1980-02-23
2,Alica,Fatnowna,1963-11-14
1,Stella,Rosenhain,1992-03-02
4,Miller,Dylan,1954-01-05
```
  
また、次の PowerShell スクリプトを実行して、データ ファイルを作成および設定することもできます。
```powershell
cls
# revise directory as desired
$dir = 'D:\BCP\';

$bcpFile = $dir + 'myIdentity.bcp';

# Confirm directory exists
IF ((Test-Path -Path $dir) -eq 0)
{
    Write-Host "The path $dir does not exist; please create or modify the directory.";
    RETURN;
};

# clear content, will error if file does not exist, can be ignored
Clear-Content -Path $bcpFile -ErrorAction SilentlyContinue;

# Add data
Add-Content -Path $bcpFile -Value '3,Anthony,Grosse,1980-02-23';
Add-Content -Path $bcpFile -Value '2,Alica,Fatnowna,1963-11-14';
Add-Content -Path $bcpFile -Value '1,Stella,Rosenhain,1992-03-02';
Add-Content -Path $bcpFile -Value '4,Miller,Dylan,1954-01-05';

#Review content
Get-Content -Path $bcpFile;
Invoke-Item $bcpFile;
```

### <a name="sample-non-xml-format-file"></a>**XML 形式以外のフォーマット ファイルのサンプル**<a name="nonxml_format_file"></a>
SQL Server は、非 XML 形式と XML 形式の 2 種類のフォーマット ファイルをサポートしています。  XML 以外のフォーマットとは、以前のバージョンの SQL Server でサポートされる従来のフォーマットです。  詳細については、「 [XML 以外のフォーマット ファイル (SQL Server)](../../relational-databases/import-export/non-xml-format-files-sql-server.md) 」を参照してください。  次のコマンドでは、 [bcp ユーティリティ](../../tools/bcp-utility.md) を使用し、 `myIdentity.fmt`のスキーマに基づいて XML 以外のフォーマット ファイル `myIdentity`を生成します。  [bcp](../../tools/bcp-utility.md) コマンドを使用してフォーマット ファイルを作成するには、 **format** 引数を指定し、データ ファイルのパスの代わりに **nul** を使用します。  format オプションには、次に示す **-f** オプションが必要です。  さらに、この例では、修飾子 **c** を使用して文字データを指定し、 **t** を使用して [フィールド ターミネータ](../../relational-databases/import-export/specify-field-and-row-terminators-sql-server.md)としてコンマを指定し、 **T** を使用して統合セキュリティによる信頼された接続を指定します。  コマンド プロンプトで、次のコマンドを入力します。
  
```
bcp TestDatabase.dbo.myIdentity format nul -c -f D:\BCP\myIdentity.fmt -t, -T

REM Review file
Notepad D:\BCP\myIdentity.fmt
```
 
> [!IMPORTANT]
> XML 以外のフォーマット ファイルは、キャリッジ リターン\ライン フィードで終わるようにします。  そうしないと、次のエラー メッセージが発生する可能性があります。
> 
> `SQLState = S1000, NativeError = 0`  
> `Error = [Microsoft][ODBC Driver 13 for SQL Server]I/O error while reading BCP format file`

## <a name="examples"></a>使用例<a name="examples"></a>
次の例では、データベース、データ ファイル、および上記で作成したフォーマット ファイルを使用します。
  
### <a name="using-bcp-and-keeping-identity-values-without-a-format-file"></a>**フォーマット ファイルなしで [bcp](../../tools/bcp-utility.md) を使用して ID 値を維持する方法**<a name="bcp_identity"></a>
**-E** スイッチ。  コマンド プロンプトで、次のコマンドを入力します。
```
REM Truncate table (for testing)
SQLCMD -Q "TRUNCATE TABLE TestDatabase.dbo.myIdentity;"

REM Import data
bcp TestDatabase.dbo.myIdentity IN D:\BCP\myIdentity.bcp -T -c -t, -E

REM Review results
SQLCMD -Q "SELECT * FROM TestDatabase.dbo.myIdentity;"
```

### <a name="using-bcp-and-keeping-identity-values-with-a-non-xml-format-file"></a>**[XML 形式以外のフォーマット ファイル](../../relational-databases/import-export/non-xml-format-files-sql-server.md)で [bcp](../../tools/bcp-utility.md) を使用して ID 値を維持する方法**<a name="bcp_identity_fmt"></a>
**-E** スイッチと **-f** スイッチ。  コマンド プロンプトで、次のコマンドを入力します。
```
REM Truncate table (for testing)
SQLCMD -Q "TRUNCATE TABLE TestDatabase.dbo.myIdentity;"

REM Import data
bcp TestDatabase.dbo.myIdentity IN D:\BCP\myIdentity.bcp -f D:\BCP\myIdentity.fmt -T -E

REM Review results
SQLCMD -Q "SELECT * FROM TestDatabase.dbo.myIdentity;"
```
 
### <a name="using-bcp-and-generated-identity-values-without-a-format-file"></a>**フォーマット ファイルなしで [bcp](../../tools/bcp-utility.md) と生成される ID 値を使用する方法**<a name="bcp_default"></a>
既定値を使用します。  コマンド プロンプトで、次のコマンドを入力します。
```
REM Truncate table (for testing)
SQLCMD -Q "TRUNCATE TABLE TestDatabase.dbo.myIdentity;"

REM Import data
bcp TestDatabase.dbo.myIdentity IN D:\BCP\myIdentity.bcp -T -c -t,

REM Review results
SQLCMD -Q "SELECT * FROM TestDatabase.dbo.myIdentity;"
```
  
### <a name="using-bcp-and-generated-identity-values-with-a-non-xml-format-file"></a>**[XML 形式以外のフォーマット ファイル](../../relational-databases/import-export/non-xml-format-files-sql-server.md)で [bcp](../../tools/bcp-utility.md) と生成された ID 値を使用する方法**<a name="bcp_default_fmt"></a>
既定値と **-f** スイッチを使用します。  コマンド プロンプトで、次のコマンドを入力します。
```
REM Truncate table (for testing)
SQLCMD -Q "TRUNCATE TABLE TestDatabase.dbo.myIdentity;"

REM Import data
bcp TestDatabase.dbo.myIdentity IN D:\BCP\myIdentity.bcp -f D:\BCP\myIdentity.fmt -T

REM Review results
SQLCMD -Q "SELECT * FROM TestDatabase.dbo.myIdentity;"
```
  
### <a name="using-bulk-insert-and-keeping-identity-values-without-a-format-file"></a>**フォーマット ファイルなしで [BULK INSERT](../../t-sql/statements/bulk-insert-transact-sql.md) を使用して ID 値を維持する方法**<a name="bulk_identity"></a>
**KEEPIDENTITY** 引数。  Microsoft [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] (SSMS) で次の Transact-SQL を実行します。
```sql
USE TestDatabase;
GO

TRUNCATE TABLE dbo.myIdentity; -- for testing
BULK INSERT dbo.myIdentity
    FROM 'D:\BCP\myIdentity.bcp'
    WITH (
        DATAFILETYPE = 'char',  
        FIELDTERMINATOR = ',',  
        KEEPIDENTITY
        );

-- review results
SELECT * FROM TestDatabase.dbo.myIdentity;
```
  
### <a name="using-bulk-insert-and-keeping-identity-values-with-a-non-xml-format-file"></a>**[XML 形式以外のフォーマット ファイル](../../relational-databases/import-export/non-xml-format-files-sql-server.md)で [BULK INSERT](../../t-sql/statements/bulk-insert-transact-sql.md) を使用して ID 値を維持する方法**<a name="bulk_identity_fmt"></a>
**KEEPIDENTITY** 引数と **FORMATFILE** 引数。  Microsoft [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] (SSMS) で次の Transact-SQL を実行します。
```sql
USE TestDatabase;
GO

TRUNCATE TABLE dbo.myIdentity; -- for testing
BULK INSERT dbo.myIdentity
   FROM 'D:\BCP\myIdentity.bcp'
   WITH (
        FORMATFILE = 'D:\BCP\myIdentity.fmt',
        KEEPIDENTITY
        );

-- review results
SELECT * FROM TestDatabase.dbo.myIdentity;
```
  
### <a name="using-bulk-insert-and-generated-identity-values-without-a-format-file"></a>**フォーマット ファイルなしで [BULK INSERT](../../t-sql/statements/bulk-insert-transact-sql.md) と生成される ID 値を使用する方法**<a name="bulk_default"></a>
既定値を使用します。  Microsoft [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] (SSMS) で次の Transact-SQL を実行します。
```sql
USE TestDatabase;
GO

TRUNCATE TABLE dbo.myIdentity;  -- for testing
BULK INSERT dbo.myIdentity
   FROM 'D:\BCP\myIdentity.bcp'
   WITH (
      DATAFILETYPE = 'char',  
      FIELDTERMINATOR = ','
      );

-- review results
SELECT * FROM TestDatabase.dbo.myIdentity;
```
  
### <a name="using-bulk-insert-and-generated-identity-values-with-a-non-xml-format-file"></a>**[XML 形式以外のフォーマット ファイル](../../relational-databases/import-export/non-xml-format-files-sql-server.md)で [BULK INSERT](../../t-sql/statements/bulk-insert-transact-sql.md) と生成された ID 値を使用する方法**<a name="bulk_default_fmt"></a>
既定値と **FORMATFILE** 引数を使用します。  Microsoft [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] (SSMS) で次の Transact-SQL を実行します。
```sql
USE TestDatabase;
GO

TRUNCATE TABLE dbo.myIdentity;  -- for testing
BULK INSERT dbo.myIdentity
   FROM 'D:\BCP\myIdentity.bcp'
   WITH (
        FORMATFILE = 'D:\BCP\myIdentity.fmt'
        );

-- review results
SELECT * FROM TestDatabase.dbo.myIdentity;
```
  
### <a name="using-openrowsetbulk-and-keeping-identity-values-with-a-non-xml-format-file"></a>**Using [OPENROWSET(BULK...)](../../t-sql/functions/openrowset-transact-sql.md) and Keeping Identity Values with a [Non-XML Format File](../../relational-databases/import-export/non-xml-format-files-sql-server.md)** <a name="openrowset_identity_fmt"></a>
**KEEPIDENTITY** テーブル ヒントと **FORMATFILE** 引数。  Microsoft [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] (SSMS) で次の Transact-SQL を実行します。
```sql
USE TestDatabase;
GO

TRUNCATE TABLE dbo.myIdentity;  -- for testing
INSERT INTO dbo.myIdentity
WITH (KEEPIDENTITY) 
(PersonID, FirstName, LastName, BirthDate)
    SELECT *
    FROM OPENROWSET (
        BULK 'D:\BCP\myIdentity.bcp', 
        FORMATFILE = 'D:\BCP\myIdentity.fmt'  
        ) AS t1;

-- review results
SELECT * FROM TestDatabase.dbo.myIdentity;
```
 
### <a name="using-openrowsetbulk-and-generated-identity-values-with-a-non-xml-format-file"></a>**Using [OPENROWSET(BULK...)](../../t-sql/functions/openrowset-transact-sql.md) and Generated Identity Values with a [Non-XML Format File](../../relational-databases/import-export/non-xml-format-files-sql-server.md)** <a name="openrowset_default_fmt"></a>
既定値と **FORMATFILE** 引数を使用します。  Microsoft [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] (SSMS) で次の Transact-SQL を実行します。
```sql
USE TestDatabase;
GO

TRUNCATE TABLE dbo.myIdentity;  -- for testing
INSERT INTO dbo.myIdentity
(FirstName, LastName, BirthDate)
    SELECT FirstName, LastName, BirthDate
    FROM OPENROWSET (
        BULK 'D:\BCP\myIdentity.bcp', 
        FORMATFILE = 'D:\BCP\myIdentity.fmt'  
        ) AS t1;

-- review results
SELECT * FROM TestDatabase.dbo.myIdentity;
```
  
##  <a name="related-tasks"></a><a name="RelatedTasks"></a> 関連タスク  
  
-   [一括インポート中の NULL の保持または既定値の使用 &#40;SQL Server&#41;](../../relational-databases/import-export/keep-nulls-or-use-default-values-during-bulk-import-sql-server.md)  
  
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
  
1.  [フィールド ターミネータと行ターミネータの指定 &#40;SQL Server&#41;](../../relational-databases/import-export/specify-field-and-row-terminators-sql-server.md)  
  
2.  [bcp を使用したデータ ファイルのプレフィックス長の指定 &#40;SQL Server&#41;](../../relational-databases/import-export/specify-prefix-length-in-data-files-by-using-bcp-sql-server.md)  
  
3.  [bcp を使用したファイル ストレージ型の指定 &#40;SQL Server&#41;](../../relational-databases/import-export/specify-file-storage-type-by-using-bcp-sql-server.md)  
  
## <a name="see-also"></a>参照  
 [BACKUP &#40;Transact-SQL&#41;](../../t-sql/statements/backup-transact-sql.md)   
 [bcp ユーティリティ](../../tools/bcp-utility.md)   
 [BULK INSERT &#40;Transact-SQL&#41;](../../t-sql/statements/bulk-insert-transact-sql.md)   
 [OPENROWSET &#40;Transact-SQL&#41;](../../t-sql/functions/openrowset-transact-sql.md)   
 [テーブル ヒント &#40;Transact-SQL&#41;](../../t-sql/queries/hints-transact-sql-table.md)  
[データのインポートまたはエクスポート用のフォーマット ファイル (SQL Server)](../../relational-databases/import-export/format-files-for-importing-or-exporting-data-sql-server.md)  
  
