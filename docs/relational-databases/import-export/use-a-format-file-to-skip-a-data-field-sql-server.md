---
title: フォーマット ファイルを使用したデータ フィールドのスキップ
description: テーブル列よりも多くのフィールドを持つデータ ファイルでフォーマット ファイルを使用できます。 これにより、テーブル列が対応するデータ フィールドにマップされ、余分なフィールドは無視されます。
ms.date: 09/19/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: data-movement
ms.topic: conceptual
helpviewer_keywords:
- format files [SQL Server], skipping data fields
- skipping data fields when importing
ms.assetid: 6a76517e-983b-47a1-8f02-661b99859a8b
author: MashaMSFT
ms.author: mathoma
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.custom: seo-lt-2019
ms.openlocfilehash: d939948fe5644b4d8f347bf36a962b52fe22e35c
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104745412"
---
# <a name="use-a-format-file-to-skip-a-data-field-sql-server"></a>フォーマット ファイルを使用したデータ フィールドのスキップ (SQL Server)
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]
データ ファイルには、テーブルの列数よりも多くのフィールドを格納できます。 このトピックでは、XML 以外のフォーマット ファイルと XML フォーマット ファイルの両方を変更し、データ ファイルに多くのフィールドを格納する方法について説明します。この操作は、テーブル列を対応するデータ フィールドにマップし、余分なフィールドを無視することによって行います。  詳細については、「 [フォーマット ファイルの作成 (SQL Server)](../../relational-databases/import-export/create-a-format-file-sql-server.md) 」を参照してください。

|[外枠]|
|---|
|[テスト条件の例](#etc)<br />&emsp;&#9679;&emsp;[サンプル テーブル](#sample_table)<br />&emsp;&#9679;&emsp;[サンプル データ ファイル](#sample_data_file)<br />[フォーマット ファイルの作成](#create_format_file)<br />&emsp;&#9679;&emsp;[XML 以外のフォーマット ファイルの作成](#nonxml_format_file)<br />&emsp;&#9679;&emsp;[XML 以外のフォーマット ファイルの変更](#modify_nonxml_format_file)<br />&emsp;&#9679;&emsp;[XML フォーマット ファイルの作成](#xml_format_file)<br />&emsp;&#9679;&emsp;[XML フォーマット ファイルの変更](#modify_xml_format_file)<br />[データ フィールドをスキップするためのフォーマット ファイルを使用したデータのインポート](#import_data)<br />&emsp;&#9679;&emsp;[bcp と XML 以外のフォーマット ファイルの使用](#bcp_nonxml)<br />&emsp;&#9679;&emsp;[bcp と XML フォーマット ファイルの使用](#bcp_xml)<br />&emsp;&#9679;&emsp;[BULK INSERT と XML 以外のフォーマット ファイルの使用](#bulk_nonxml)<br />&emsp;&#9679;&emsp;[BULK INSERT と XML フォーマット ファイルの使用](#bulk_xml)<br />&emsp;&#9679;&emsp;[OPENROWSET(BULK...) と XML 以外のフォーマット ファイルの使用](#openrowset_nonxml)<br />&emsp;&#9679;&emsp;[OPENROWSET(BULK...) と XML フォーマット ファイルの使用](#openrowset_xml)<p>                                                                                                                                                                                                                  </p>|
  
> [!NOTE]
>  XML 以外のフォーマット ファイルまたは XML フォーマット ファイルを使用して、データ ファイルをテーブルに一括インポートできます。その場合、[bcp ユーティリティ](../../tools/bcp-utility.md) コマンド、[BULK INSERT](../../t-sql/statements/bulk-insert-transact-sql.md) ステートメント、または INSERT ...SELECT * FROM [OPENROWSET(BULK...)](../../t-sql/functions/openrowset-transact-sql.md) ステートメント。 詳細については、「[データの一括インポートでのフォーマット ファイルの使用 &#40;SQL Server&#41;](../../relational-databases/import-export/use-a-format-file-to-bulk-import-data-sql-server.md)」を参照してください。

## <a name="example-test-conditions"></a>テスト条件の例<a name="etc"></a>  
このトピックの変更するフォーマット ファイルの例は、以下に定義されたテーブルとデータ ファイルに基づいています。
  
### <a name="sample-table"></a>サンプル テーブル<a name="sample_table"></a>
以下のスクリプトでは、テスト データベースと `myTestSkipField`という名前のテーブルが作成されます。  Microsoft SQL Server Management Studio (SSMS) で、次の Transact SQL を実行します。
 
```sql
CREATE DATABASE TestDatabase;
GO

USE TestDatabase;
CREATE TABLE myTestSkipField
   (
   PersonID smallint,
   FirstName varchar(25),
   LastName varchar(30)
   );
```
  
### <a name="sample-data-file"></a>サンプル データ ファイル<a name="sample_data_file"></a>
空のファイル `D:\BCP\myTestSkipField.bcp` を作成し、次のデータを挿入します。 
```
1,SkipMe,Anthony,Grosse
2,SkipMe,Alica,Fatnowna
3,SkipMe,Stella,Rosenhain
```
  
## <a name="creating-the-format-files"></a>フォーマット ファイルの作成<a name="create_format_file"></a>
`myTestSkipField.bcp` から `myTestSkipField` テーブルにデータを一括インポートするには、フォーマット ファイルで次の操作を行う必要があります。
* 最初のデータ フィールドを最初の列 `PersonID`にマップします。
* 2 番目のデータ フィールドをスキップします。
* 3 番目のデータ フィールドを 2 番目の列 `FirstName`にマップします。
* 4 番目のデータ フィールドを 3 番目の列 `LastName`にマップします。  

フォーマット ファイルを作成する最も簡単な方法は、 [bcp ユーティリティ](../../tools/bcp-utility.md)を使用することです。  最初に、既存のテーブルからベース フォーマット ファイルを作成します。  次に、実際のデータ ファイルを反映するようにベース フォーマット ファイルを変更します。
  
### <a name="creating-a-non-xml-format-file"></a><a name="nonxml_format_file"></a>XML 以外のフォーマット ファイルの作成 
詳細については、「 [XML 以外のフォーマット ファイル (SQL Server)](../../relational-databases/import-export/non-xml-format-files-sql-server.md) 」を参照してください。 次のコマンドでは、 [bcp ユーティリティ](../../tools/bcp-utility.md) を使用し、 `myTestSkipField.fmt`のスキーマに基づいて XML 以外のフォーマット ファイル `myTestSkipField`を生成します。  さらに、修飾子 `c` を使用して文字データを指定し、 `t,` を使用してフィールド ターミネータとしてコンマを指定し、 `T` を使用して統合セキュリティによる信頼された接続を指定します。  コマンド プロンプトで、次のコマンドを入力します。

```
bcp TestDatabase.dbo.myTestSkipField format nul -c -f D:\BCP\myTestSkipField.fmt -t, -T
```

### <a name="modifying-the-non-xml-format-file"></a>XML 以外のフォーマット ファイルの変更 <a name="modify_nonxml_format_file"></a>
用語については、「 [XML 以外のフォーマット ファイルの構造](../../relational-databases/import-export/non-xml-format-files-sql-server.md#Structure) 」を参照してください。 メモ帳で `D:\BCP\myTestSkipField.fmt` を開き、次のように変更します。
1) `FirstName` のフォーマット ファイル行全体をコピーし、次の行の `FirstName` のすぐ後ろに貼り付けます。
2) 新しい行とすべての後続行のホスト ファイル フィールドの順序の値を 1 増やします。
3) データ ファイル内の実際のフィールド数が反映されるように、列数の値を増やします。
3) 2 番目のフォーマット ファイル行のサーバー列の順序を `2` から `0` に変更します。 

加えた変更を比較します。  
**変更前**
```
13.0
3
1       SQLCHAR 0       7       ","      1     PersonID     ""
2       SQLCHAR 0       25      ","      2     FirstName    SQL_Latin1_General_CP1_CI_AS
3       SQLCHAR 0       30      "\r\n"   3     LastName     SQL_Latin1_General_CP1_CI_AS
```
**変更後**
```
13.0
4
1       SQLCHAR 0       7       ","      1     PersonID     ""
2       SQLCHAR 0       25      ","      0     FirstName    SQL_Latin1_General_CP1_CI_AS
3       SQLCHAR 0       25      ","      2     FirstName    SQL_Latin1_General_CP1_CI_AS
4       SQLCHAR 0       50      "\r\n"   3     LastName     SQL_Latin1_General_CP1_CI_AS

```

変更されたフォーマット ファイルは次のように反映されます。
* データ フィールドが 4 つになります。
* `myTestSkipField.bcp` の最初のデータ フィールドは最初の列にマップされます: `myTestSkipField.. PersonID`
* `myTestSkipField.bcp` の 2 番目のデータ フィールドはどの列にもマップされません。
* `myTestSkipField.bcp` の 3 番目のデータ フィールドは 2 番目の列にマップされます: `myTestSkipField.. FirstName`
* `myTestSkipField.bcp` の 4 番目のデータ フィールドは 3 番目の列にマップされます: `myTestSkipField.. LastName`

### <a name="creating-an-xml-format-file"></a>XML フォーマット ファイルの作成 <a name="xml_format_file"></a>  
詳細については、「 [XML フォーマット ファイル (SQL Server)](../../relational-databases/import-export/xml-format-files-sql-server.md) 」を参照してください。  次のコマンドでは、 [bcp ユーティリティ](../../tools/bcp-utility.md) を使用し、 `myTestSkipField.xml`のスキーマに基づいて XML のフォーマット ファイル `myTestSkipField`を生成します。  さらに、修飾子 `c` を使用して文字データを指定し、 `t,` を使用してフィールド ターミネータとしてコンマを指定し、 `T` を使用して統合セキュリティによる信頼された接続を指定します。  XML ベースのフォーマット ファイルを生成する場合は、 `x` 修飾子を使用する必要があります。  コマンド プロンプトで、次のコマンドを入力します。

```
bcp TestDatabase.dbo.myTestSkipField format nul -c -x -f D:\BCP\myTestSkipField.xml -t, -T
```

### <a name="modifying-the-xml-format-file"></a>XML フォーマット ファイルの変更 <a name="modify_xml_format_file"></a>
用語については、「 [XML フォーマット ファイルのスキーマ構文](../../relational-databases/import-export/xml-format-files-sql-server.md#StructureOfXmlFFs) 」を参照してください。  メモ帳で `D:\BCP\myTestSkipField.xml` を開き、次のように変更します。
1) 2 番目のフィールド全体をコピーし、次の行の 2 番目のフィールドのすぐ後ろに貼り付けます。
2) 新しい FIELD と後続の各 FIELD の "FIELD ID" 値を 1 増やします。
3) `FirstName`と `LastName` の "COLUMN SOURCE" 値を 1 増やし、変更されたマッピングを反映するようにします。

加えた変更を比較します。  
**変更前**
```
\<?xml version="1.0"?>
\<BCPFORMAT xmlns="https://schemas.microsoft.com/sqlserver/2004/bulkload/format" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
 <RECORD>
  \<FIELD ID="1" xsi:type="CharTerm" TERMINATOR="," MAX_LENGTH="7"/>
  \<FIELD ID="2" xsi:type="CharTerm" TERMINATOR="," MAX_LENGTH="25" COLLATION="SQL_Latin1_General_CP1_CI_AS"/>
  \<FIELD ID="3" xsi:type="CharTerm" TERMINATOR="\r\n" MAX_LENGTH="30" COLLATION="SQL_Latin1_General_CP1_CI_AS"/>
 </RECORD>
 <ROW>
  \<COLUMN SOURCE="1" NAME="PersonID" xsi:type="SQLSMALLINT"/>
  \<COLUMN SOURCE="2" NAME="FirstName" xsi:type="SQLVARYCHAR"/>
  \<COLUMN SOURCE="3" NAME="LastName" xsi:type="SQLVARYCHAR"/>
 </ROW>
</BCPFORMAT>
```

**変更後**
```
\<?xml version="1.0"?>
\<BCPFORMAT xmlns="https://schemas.microsoft.com/sqlserver/2004/bulkload/format" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
 <RECORD>
  \<FIELD ID="1" xsi:type="CharTerm" TERMINATOR="," MAX_LENGTH="7"/>
  \<FIELD ID="2" xsi:type="CharTerm" TERMINATOR="," MAX_LENGTH="25" COLLATION="SQL_Latin1_General_CP1_CI_AS"/>
  \<FIELD ID="3" xsi:type="CharTerm" TERMINATOR="," MAX_LENGTH="25" COLLATION="SQL_Latin1_General_CP1_CI_AS"/>
  \<FIELD ID="4" xsi:type="CharTerm" TERMINATOR="\r\n" MAX_LENGTH="30" COLLATION="SQL_Latin1_General_CP1_CI_AS"/>
 </RECORD>
 <ROW>
  \<COLUMN SOURCE="1" NAME="PersonID" xsi:type="SQLSMALLINT"/>
  \<COLUMN SOURCE="3" NAME="FirstName" xsi:type="SQLVARYCHAR"/>
  \<COLUMN SOURCE="4" NAME="LastName" xsi:type="SQLVARYCHAR"/>
 </ROW>
</BCPFORMAT>
```

変更されたフォーマット ファイルは次のように反映されます。
* データ フィールドが 4 つになります。
* COLUMN 1 に対応する FIELD 1 は、最初のテーブル列にマップされます: `myTestSkipField.. PersonID`
* FIELD 2 はどの COLUMN にも対応していないため、どのテーブル列にもマップされません。
* COLUMN 3 に対応する FIELD 3 は、2 番目のテーブル列にマップされます: `myTestSkipField.. FirstName`
* COLUMN 4 に対応する FIELD 4 は、3 番目のテーブル列にマップされます: `myTestSkipField.. LastName`

## <a name="importing-data-with-a-format-file-to-skip-a-data-field"></a>データ フィールドをスキップするためのフォーマット ファイルを使用したデータのインポート<a name="import_data"></a>
次の例では、データベース、データ ファイル、および上記で作成したフォーマット ファイルを使用します。

### <a name="using-bcp-and-non-xml-format-file"></a>[bcp](../../tools/bcp-utility.md) と [XML 以外のフォーマット ファイル](../../relational-databases/import-export/non-xml-format-files-sql-server.md)の使用<a name="bcp_nonxml"></a>
コマンド プロンプトで、次のコマンドを入力します。
```
bcp TestDatabase.dbo.myTestSkipField IN D:\BCP\myTestSkipField.bcp -f D:\BCP\myTestSkipField.fmt -T
```

### <a name="using-bcp-and-xml-format-file"></a>[bcp](../../tools/bcp-utility.md) と [XML フォーマット ファイル](../../relational-databases/import-export/xml-format-files-sql-server.md)の使用<a name="bcp_xml"></a>
コマンド プロンプトで、次のコマンドを入力します。
```
bcp TestDatabase.dbo.myTestSkipField IN D:\BCP\myTestSkipField.bcp -f D:\BCP\myTestSkipField.xml -T
```

### <a name="using-bulk-insert-and-non-xml-format-file"></a>[BULK INSERT](../../t-sql/statements/bulk-insert-transact-sql.md) と [XML 以外のフォーマット ファイル](../../relational-databases/import-export/non-xml-format-files-sql-server.md)の使用<a name="bulk_nonxml"></a>
Microsoft SQL Server Management Studio (SSMS) で、次の Transact SQL を実行します。
```sql
USE TestDatabase;  
GO

TRUNCATE TABLE myTestSkipField;
BULK INSERT dbo.myTestSkipField   
   FROM 'D:\BCP\myTestSkipField.bcp'   
   WITH (FORMATFILE = 'D:\BCP\myTestSkipField.fmt');  
GO  

-- review results
SELECT * FROM TestDatabase.dbo.myTestSkipField;
```

### <a name="using-bulk-insert-and-xml-format-file"></a>[BULK INSERT](../../t-sql/statements/bulk-insert-transact-sql.md) と [XML フォーマット ファイル](../../relational-databases/import-export/xml-format-files-sql-server.md)の使用<a name="bulk_xml"></a>
Microsoft SQL Server Management Studio (SSMS) で、次の Transact SQL を実行します。
```sql
USE TestDatabase;  
GO

TRUNCATE TABLE myTestSkipField;
BULK INSERT dbo.myTestSkipField   
   FROM 'D:\BCP\myTestSkipField.bcp'   
   WITH (FORMATFILE = 'D:\BCP\myTestSkipField.xml');  
GO  

-- review results
SELECT * FROM TestDatabase.dbo.myTestSkipField;
```

### <a name="using-openrowsetbulk-and-non-xml-format-file"></a>[OPENROWSET(BULK...)](../../t-sql/functions/openrowset-transact-sql.md) と[ XML 以外のフォーマット ファイル](../../relational-databases/import-export/non-xml-format-files-sql-server.md)の使用<a name="openrowset_nonxml"></a>    
Microsoft SQL Server Management Studio (SSMS) で、次の Transact SQL を実行します。
```sql
USE TestDatabase;
GO

TRUNCATE TABLE myTestSkipField;
INSERT INTO dbo.myTestSkipField
    SELECT *
    FROM OPENROWSET (
        BULK 'D:\BCP\myTestSkipField.bcp',
        FORMATFILE = 'D:\BCP\myTestSkipField.fmt'
        ) AS t1;
GO

-- review results
SELECT * FROM TestDatabase.dbo.myTestSkipField;
```

### <a name="using-openrowsetbulk-and-xml-format-file"></a>[OPENROWSET(BULK...)](../../t-sql/functions/openrowset-transact-sql.md) と [XML フォーマット ファイル](../../relational-databases/import-export/xml-format-files-sql-server.md)の使用<a name="openrowset_xml"></a>
Microsoft SQL Server Management Studio (SSMS) で、次の Transact SQL を実行します。
```sql
USE TestDatabase;  
GO

TRUNCATE TABLE myTestSkipField;
INSERT INTO dbo.myTestSkipField 
    SELECT *
    FROM OPENROWSET (
        BULK 'D:\BCP\myTestSkipField.bcp',
        FORMATFILE = 'D:\BCP\myTestSkipField.xml'  
       ) AS t1;
GO

-- review results
SELECT * FROM TestDatabase.dbo.myTestSkipField;
```

  
## <a name="see-also"></a>参照  
 [bcp ユーティリティ](../../tools/bcp-utility.md)   
 [BULK INSERT &#40;Transact-SQL&#41;](../../t-sql/statements/bulk-insert-transact-sql.md)   
 [OPENROWSET &#40;Transact-SQL&#41;](../../t-sql/functions/openrowset-transact-sql.md)   
 [フォーマット ファイルを使用したテーブル列のスキップ &#40;SQL Server&#41;](../../relational-databases/import-export/use-a-format-file-to-skip-a-table-column-sql-server.md)   
 [フォーマット ファイルを使用したテーブル列とデータ ファイル フィールドのマッピング &#40;SQL Server&#41;](../../relational-databases/import-export/use-a-format-file-to-map-table-columns-to-data-file-fields-sql-server.md)  
  
  
