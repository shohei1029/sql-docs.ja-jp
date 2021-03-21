---
title: データの一括インポートでのフォーマット ファイルの使用
description: SQL Server では、一括インポート操作でフォーマット ファイルを使用できます。 フォーマット ファイルでは、データ ファイルのフィールドがテーブルの列にマップされます。
ms.date: 09/20/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: data-movement
ms.topic: conceptual
helpviewer_keywords:
- bulk importing [SQL Server], format files
- format files [SQL Server], importing data using
ms.assetid: 2956df78-833f-45fa-8a10-41d6522562b9
author: MashaMSFT
ms.author: mathoma
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.custom: seo-lt-2019
ms.openlocfilehash: 124b87eca694ef809eff7feffe37e6ae389cdd2e
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104745662"
---
# <a name="use-a-format-file-to-bulk-import-data-sql-server"></a>データの一括インポートでのフォーマット ファイルの使用 (SQL Server)
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

このトピックでは、一括インポート操作でのフォーマット ファイルの使用方法について説明します。  フォーマット ファイルでは、データ ファイルのフィールドがテーブルの列にマップされます。  詳細については、「 [フォーマット ファイルの作成 (SQL Server)](../../relational-databases/import-export/create-a-format-file-sql-server.md) 」を参照してください。
  
## <a name="before-you-begin"></a>開始する前に
* Unicode 文字データ ファイルを操作するフォーマット ファイルの場合、すべての入力フィールドが Unicode テキスト文字列 (つまり、固定サイズの Unicode 文字列または終端文字が指定された Unicode 文字列) でなければなりません。
* [SQLXML](../../relational-databases/import-export/examples-of-bulk-import-and-export-of-xml-documents-sql-server.md) データを一括エクスポートまたは一括インポートする場合、フォーマット ファイルのデータ型には次のいずれかを使用します。
  * SQLCHAR または SQLVARCHAR (データは、クライアント コード ページまたは照合順序で暗黙的に指定されるコード ページで送られます)
  * SQLNCHAR または SQLNVARCHAR (データは Unicode として送信されます)
  * SQLBINARY または SQLVARBIN (データは変換なしで送られます)
* Azure SQL Database と Azure Synapse Analytics では、[bcp](../../tools/bcp-utility.md) のみサポートされます。  追加情報については、次を参照してください。
  * [Azure Synapse Analytics にデータを読み込む](/azure/synapse-analytics/sql-data-warehouse/design-elt-data-loading)
  * [SQL Server から Azure Synapse Analytics にデータを読み込む (フラット ファイル)](/azure/synapse-analytics/sql-data-warehouse/design-elt-data-loading)
  * [データを移行する](/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-develop)

## <a name="example-test-conditions"></a>テスト条件の例
このトピックのフォーマット ファイルの例は、以下に定義されたテーブルとデータ ファイルに基づいています。

### <a name="sample-table"></a>サンプル テーブル
以下のスクリプトでは、テスト データベースと `myFirstImport`という名前のテーブルが作成されます。  Microsoft [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] (SSMS) で次の Transact-SQL を実行します。
```sql
CREATE DATABASE TestDatabase;
GO

USE TestDatabase;
CREATE TABLE dbo.MyFirstImport (
   PersonID smallint,
   FirstName varchar(25),
   LastName varchar(30),
   BirthDate Date
   );
```

### <a name="sample-data-file"></a>サンプル データ ファイル
メモ帳を使用して、空のファイル `D:\BCP\myFirstImport.bcp` を作成し、次のデータを挿入します。
```
1,Anthony,Grosse,1980-02-23
2,Alica,Fatnowna,1963-11-14
3,Stella,Rosenhain,1992-03-02
```

また、次の PowerShell スクリプトを実行して、データ ファイルを作成および設定することもできます。
```powershell
Clear-Host
# revise directory as desired
$dir = 'D:\BCP\';

$bcpFile = Join-Path -Path $dir -ChildPath 'MyFirstImport.bcp';

# Confirm directory exists
IF ((Test-Path -Path $dir) -eq 0)
{
    Write-Host "The path $dir does not exist; please create or modify the directory.";
    RETURN;
};

# clear content, will error if file does not exist, can be ignored
Clear-Content -Path $bcpFile -ErrorAction SilentlyContinue;

# Add data
Add-Content -Path $bcpFile -Value '1,Anthony,Grosse,1980-02-23';
Add-Content -Path $bcpFile -Value '2,Alica,Fatnowna,1963-11-14';
Add-Content -Path $bcpFile -Value '3,Stella,Rosenhain,1992-03-02';

#Review content
Get-Content -Path $bcpFile;
Notepad.exe $bcpfile;
```

## <a name="creating-the-format-files"></a>フォーマット ファイルの作成
SQL Server は、非 XML 形式と XML 形式の 2 種類のフォーマット ファイルをサポートしています。  XML 以外のフォーマットとは、以前のバージョンの SQL Server でサポートされる従来のフォーマットです。

### <a name="creating-a-non-xml-format-file"></a>XML 以外のフォーマット ファイルの作成
詳細については、「 [XML 以外のフォーマット ファイル (SQL Server)](../../relational-databases/import-export/non-xml-format-files-sql-server.md) 」を参照してください。  次のコマンドでは、 [bcp ユーティリティ](../../tools/bcp-utility.md) を使用し、 `myFirstImport.fmt`のスキーマに基づいて XML 以外のフォーマット ファイル `myFirstImport`を生成します。  bcp コマンドを使用してフォーマット ファイルを作成するには、 **format** 引数を指定し、データ ファイルのパスの代わりに **nul** を使用します。  format オプションには、次に示す **-f** オプションが必要です。  さらに、この例では、修飾子 **c** を使用して文字データを指定し、 **t** を使用して [フィールド ターミネータ](../../relational-databases/import-export/specify-field-and-row-terminators-sql-server.md)としてコンマを指定し、 **T** を使用して統合セキュリティによる信頼された接続を指定します。  コマンド プロンプトで、次のコマンドを入力します。

```cmd
bcp TestDatabase.dbo.myFirstImport format nul -c -f D:\BCP\myFirstImport.fmt -t, -T

REM Review file
Notepad D:\BCP\myFirstImport.fmt
```

非 XML フォーマット ファイルの `D:\BCP\myFirstImport.fmt` は次のようになります。
```
13.0
4
1       SQLCHAR             0       7       ","      1     PersonID               ""
2       SQLCHAR             0       25      ","      2     FirstName              SQL_Latin1_General_CP1_CI_AS
3       SQLCHAR             0       30      ","      3     LastName               SQL_Latin1_General_CP1_CI_AS
4       SQLCHAR             0       11      "\r\n"   4     BirthDate              ""
```

> [!IMPORTANT]
> XML 以外のフォーマット ファイルは、キャリッジ リターン\ライン フィードで終わるようにします。  そうしないと、次のエラー メッセージが発生する可能性があります。
> 
> `SQLState = S1000, NativeError = 0`  
> `Error = [Microsoft][ODBC Driver 13 for SQL Server]I/O error while reading BCP format file`

### <a name="creating-an-xml-format-file"></a>XML フォーマット ファイルの作成
詳細については、「 [XML フォーマット ファイル (SQL Server)](../../relational-databases/import-export/xml-format-files-sql-server.md) 」を参照してください。  次のコマンドでは、 [bcp ユーティリティ](../../tools/bcp-utility.md) を使用し、 `myFirstImport.xml`のスキーマに基づいて XML のフォーマット ファイル `myFirstImport`を生成します。 bcp コマンドを使用してフォーマット ファイルを作成するには、 **format** 引数を指定し、データ ファイルのパスの代わりに **nul** を使用します。  format オプションには常に **-f** オプションが必要です。XML フォーマット ファイルを作成するには、 **-x** オプションも指定する必要があります。  さらに、この例では、修飾子 **c** を使用して文字データを指定し、 **t** を使用して [フィールド ターミネータ](../../relational-databases/import-export/specify-field-and-row-terminators-sql-server.md)としてコンマを指定し、 **T** を使用して統合セキュリティによる信頼された接続を指定します。  コマンド プロンプトで、次のコマンドを入力します。
```cmd
bcp TestDatabase.dbo.myFirstImport format nul -c -x -f D:\BCP\myFirstImport.xml -t, -T

REM Review file
Notepad D:\BCP\myFirstImport.xml
```

XML フォーマット ファイルの `D:\BCP\myFirstImport.xml` は次のようになります。
```xml
<?xml version="1.0"?>
<BCPFORMAT xmlns="http://schemas.microsoft.com/sqlserver/2004/bulkload/format" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
 <RECORD>
  <FIELD ID="1" xsi:type="CharTerm" TERMINATOR="," MAX_LENGTH="7"/>
  <FIELD ID="2" xsi:type="CharTerm" TERMINATOR="," MAX_LENGTH="25" COLLATION="SQL_Latin1_General_CP1_CI_AS"/>
  <FIELD ID="3" xsi:type="CharTerm" TERMINATOR="," MAX_LENGTH="30" COLLATION="SQL_Latin1_General_CP1_CI_AS"/>
  <FIELD ID="4" xsi:type="CharTerm" TERMINATOR="\r\n" MAX_LENGTH="11"/>
 </RECORD>
 <ROW>
  <COLUMN SOURCE="1" NAME="PersonID" xsi:type="SQLSMALLINT"/>
  <COLUMN SOURCE="2" NAME="FirstName" xsi:type="SQLVARCHAR"/>
  <COLUMN SOURCE="3" NAME="LastName" xsi:type="SQLVARCHAR"/>
  <COLUMN SOURCE="4" NAME="BirthDate" xsi:type="SQLDATE"/>
 </ROW>
</BCPFORMAT>
```

## <a name="using-a-format-file-to-bulk-import-data"></a>フォーマット ファイルを使用したデータの一括インポート
次の例では、データベース、データ ファイル、および上記で作成したフォーマット ファイルを使用します。

### <a name="using-bcp-and-non-xml-format-file"></a>[bcp](../../tools/bcp-utility.md) と [XML 以外のフォーマット ファイル](../../relational-databases/import-export/non-xml-format-files-sql-server.md)の使用
コマンド プロンプトで、次のコマンドを入力します。
```cmd
REM Truncate table (for testing)
SQLCMD -Q "TRUNCATE TABLE TestDatabase.dbo.MyFirstImport;"

REM Import data
bcp TestDatabase.dbo.myFirstImport IN D:\BCP\myFirstImport.bcp -f D:\BCP\myFirstImport.fmt -T

REM Review results
SQLCMD -Q "SELECT * FROM TestDatabase.dbo.MyFirstImport"
```


### <a name="using-bcp-and-xml-format-file"></a>[bcp](../../tools/bcp-utility.md) と [XML フォーマット ファイル](../../relational-databases/import-export/xml-format-files-sql-server.md)の使用
コマンド プロンプトで、次のコマンドを入力します。
```cmd
REM Truncate table (for testing)
SQLCMD -Q "TRUNCATE TABLE TestDatabase.dbo.MyFirstImport;"

REM Import data
bcp TestDatabase.dbo.myFirstImport IN D:\BCP\myFirstImport.bcp -f D:\BCP\myFirstImport.xml -T

REM Review results
SQLCMD -Q "SELECT * FROM TestDatabase.dbo.MyFirstImport;"
```


### <a name="using-bulk-insert-and-non-xml-format-file"></a>[BULK INSERT](../../t-sql/statements/bulk-insert-transact-sql.md) と [XML 以外のフォーマット ファイル](../../relational-databases/import-export/non-xml-format-files-sql-server.md)の使用
Microsoft [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] (SSMS) で次の Transact-SQL を実行します。
```sql
USE TestDatabase;  
GO

TRUNCATE TABLE myFirstImport; -- (for testing)
BULK INSERT dbo.myFirstImport   
   FROM 'D:\BCP\myFirstImport.bcp'   
   WITH (FORMATFILE = 'D:\BCP\myFirstImport.fmt');  
GO  

-- review results
SELECT * FROM TestDatabase.dbo.myFirstImport;
```

### <a name="using-bulk-insert-and-xml-format-file"></a>[BULK INSERT](../../t-sql/statements/bulk-insert-transact-sql.md) と [XML フォーマット ファイル](../../relational-databases/import-export/xml-format-files-sql-server.md)の使用
Microsoft [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] (SSMS) で次の Transact-SQL を実行します。
```sql
USE TestDatabase;  
GO

TRUNCATE TABLE myFirstImport; -- (for testing)
BULK INSERT dbo.myFirstImport   
   FROM 'D:\BCP\myFirstImport.bcp'   
   WITH (FORMATFILE = 'D:\BCP\myFirstImport.xml');  
GO  

-- review results
SELECT * FROM TestDatabase.dbo.myFirstImport;
```

### <a name="using-openrowsetbulk-and-non-xml-format-file"></a>[OPENROWSET(BULK...)](../../t-sql/functions/openrowset-transact-sql.md) と [XML 以外のフォーマット ファイル](../../relational-databases/import-export/non-xml-format-files-sql-server.md)の使用    
Microsoft [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] (SSMS) で次の Transact-SQL を実行します。
```sql
USE TestDatabase;
GO

TRUNCATE TABLE myFirstImport; -- (for testing)
INSERT INTO dbo.myFirstImport
    SELECT *
    FROM OPENROWSET (
        BULK 'D:\BCP\myFirstImport.bcp',
        FORMATFILE = 'D:\BCP\myFirstImport.fmt'
        ) AS t1;
GO

-- review results
SELECT * FROM TestDatabase.dbo.myFirstImport;
```

### <a name="using-openrowsetbulk-and-xml-format-file"></a>[OPENROWSET(BULK...)](../../t-sql/functions/openrowset-transact-sql.md) と [XML フォーマット ファイル](../../relational-databases/import-export/xml-format-files-sql-server.md)の使用
Microsoft [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] (SSMS) で次の Transact-SQL を実行します。
```sql
USE TestDatabase;  
GO

TRUNCATE TABLE myFirstImport; -- (for testing)
INSERT INTO dbo.myFirstImport 
    SELECT *
    FROM OPENROWSET (
        BULK 'D:\BCP\myFirstImport.bcp',
        FORMATFILE = 'D:\BCP\myFirstImport.xml'  
       ) AS t1;
GO

-- review results
SELECT * FROM TestDatabase.dbo.myFirstImport;
```
  
## <a name="more-examples"></a>他の例について
 [フォーマット ファイルの作成 &#40;SQL Server&#41;](../../relational-databases/import-export/create-a-format-file-sql-server.md)  
 [フォーマット ファイルを使用したテーブル列のスキップ &#40;SQL Server&#41;](../../relational-databases/import-export/use-a-format-file-to-skip-a-table-column-sql-server.md)  
 [フォーマット ファイルを使用したデータ フィールドのスキップ &#40;SQL Server&#41;](../../relational-databases/import-export/use-a-format-file-to-skip-a-data-field-sql-server.md)  
 [フォーマット ファイルを使用したテーブル列とデータ ファイル フィールドのマッピング &#40;SQL Server&#41;](../../relational-databases/import-export/use-a-format-file-to-map-table-columns-to-data-file-fields-sql-server.md)  
  
## <a name="see-also"></a>関連項目  
 [bcp ユーティリティ](../../tools/bcp-utility.md)   
 [BULK INSERT &#40;Transact-SQL&#41;](../../t-sql/statements/bulk-insert-transact-sql.md)   
 [OPENROWSET &#40;Transact-SQL&#41;](../../t-sql/functions/openrowset-transact-sql.md)   
 [XML 以外のフォーマット ファイル &#40;SQL Server&#41;](../../relational-databases/import-export/non-xml-format-files-sql-server.md)   
 [XML フォーマット ファイル &#40;SQL Server&#41;](../../relational-databases/import-export/xml-format-files-sql-server.md)  
  [データのインポートまたはエクスポート用のフォーマット ファイル (SQL Server)](../../relational-databases/import-export/format-files-for-importing-or-exporting-data-sql-server.md)
