---
title: フォーマット ファイルを使用したテーブル列とデータ ファイル フィールドのマッピング
description: SQL Server では、XML 以外のフォーマット ファイルと XML フォーマット ファイルに、テーブル列とは異なる順序にフィールドを並べ替えたデータ ファイルを格納できます。
ms.date: 09/19/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: data-movement
ms.topic: conceptual
helpviewer_keywords:
- mapping columns to fields during import [SQL Server]
- format files [SQL Server], mapping columns to fields
ms.assetid: e7ee4f7e-24c4-4eb7-84d2-41e57ccc1ef1
author: MashaMSFT
ms.author: mathoma
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.custom: seo-lt-2019
ms.openlocfilehash: 6b2a501eafa04901aa72e6adedbdcd99854377db
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104745782"
---
# <a name="use-a-format-file-to-map-table-columns-to-data-file-fields-sql-server"></a>フォーマット ファイルを使用したテーブル列とデータ ファイル フィールドのマッピング (SQL Server)
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]
データ ファイルに含めるフィールドは、対応するテーブル内の列とは異なる順序に並べ替えることができます。 このトピックでは、テーブル列とは異なる順序にフィールドを並べ替えたデータ ファイルを格納できるように変更した XML フォーマット ファイルと XML 以外のフォーマット ファイルについて説明します。 変更したフォーマット ファイルのデータ フィールドは、対応するテーブル列にマッピングされます。  詳細については、「 [フォーマット ファイルの作成 (SQL Server)](../../relational-databases/import-export/create-a-format-file-sql-server.md) 」を参照してください。

|[外枠]|
|---|
|[テスト条件の例](#etc)<br />&emsp;&#9679;&emsp;[サンプル テーブル](#sample_table)<br />&emsp;&#9679;&emsp;[サンプル データ ファイル](#sample_data_file)<br />[フォーマット ファイルの作成](#create_format_file)<br />&emsp;&#9679;&emsp;[XML 以外のフォーマット ファイルの作成](#nonxml_format_file)<br />&emsp;&#9679;&emsp;[XML 以外のフォーマット ファイルの変更](#modify_nonxml_format_file)<br />&emsp;&#9679;&emsp;[XML フォーマット ファイルの作成](#xml_format_file)<br />&emsp;&#9679;&emsp;[XML フォーマット ファイルの変更](#modify_xml_format_file)<br />[フォーマット ファイルを使用してデータをインポートして、テーブル列をデータ ファイル フィールドにマッピングする](#import_data)<br />&emsp;&#9679;&emsp;[bcp と XML 以外のフォーマット ファイルの使用](#bcp_nonxml)<br />&emsp;&#9679;&emsp;[bcp と XML フォーマット ファイルの使用](#bcp_xml)<br />&emsp;&#9679;&emsp;[BULK INSERT と XML 以外のフォーマット ファイルの使用](#bulk_nonxml)<br />&emsp;&#9679;&emsp;[BULK INSERT と XML フォーマット ファイルの使用](#bulk_xml)<br />&emsp;&#9679;&emsp;[OPENROWSET(BULK...) と XML 以外のフォーマット ファイルの使用](#openrowset_nonxml)<br />&emsp;&#9679;&emsp;[OPENROWSET(BULK...) と XML フォーマット ファイルの使用](#openrowset_xml)|

> [!NOTE]  
>  XML 以外のフォーマット ファイルまたは XML フォーマット ファイルを使用して、データ ファイルをテーブルに一括インポートできます。その場合、[bcp ユーティリティ](../../tools/bcp-utility.md) コマンド、[BULK INSERT](../../t-sql/statements/bulk-insert-transact-sql.md) ステートメント、または INSERT ...SELECT * FROM [OPENROWSET(BULK...)](../../t-sql/functions/openrowset-transact-sql.md) ステートメント。 詳細については、「[データの一括インポートでのフォーマット ファイルの使用 &#40;SQL Server&#41;](../../relational-databases/import-export/use-a-format-file-to-bulk-import-data-sql-server.md)」を参照してください。  

## <a name="example-test-conditions"></a>テスト条件の例<a name="etc"></a>  
このトピックの変更するフォーマット ファイルの例は、以下に定義されたテーブルとデータ ファイルに基づいています。

### <a name="sample-table"></a>サンプル テーブル<a name="sample_table"></a>
以下のスクリプトでは、テスト データベースと `myRemap`という名前のテーブルが作成されます。  Microsoft SQL Server Management Studio (SSMS) で、次の Transact SQL を実行します。
```sql
CREATE DATABASE TestDatabase;
GO

USE TestDatabase;
CREATE TABLE myRemap
   (
   PersonID smallint,
   FirstName varchar(25),
   LastName varchar(30),
   Gender char(1)
   );
```

### <a name="sample-data-file"></a>サンプル データ ファイル<a name="sample_data_file"></a>
以下のデータは、 `FirstName` と `LastName` をテーブル `myRemap`と逆の順序で示しています。  メモ帳を使用して、空のファイル `D:\BCP\myRemap.bcp` を作成し、次のデータを挿入します。
```
1,Grosse,Anthony,M
2,Fatnowna,Alica,F
3,Rosenhain,Stella,F
```

## <a name="creating-the-format-files"></a>フォーマット ファイルの作成<a name="create_format_file"></a>
`myRemap.bcp` から `myRemap` テーブルにデータを一括インポートするには、フォーマット ファイルで次の操作を行う必要があります。
* 最初のデータ フィールドを最初の列 `PersonID`にマップします。
* 2 番目のデータ フィールドを 3 番目の列 `LastName`にマップします。
* 3 番目のデータ フィールドを 2 番目の列 `FirstName`にマップします。
* 4 番目のデータ フィールドを 4 番目の列 `Gender`にマップします。

フォーマット ファイルを作成する最も簡単な方法は、 [bcp ユーティリティ](../../tools/bcp-utility.md)を使用することです。  最初に、既存のテーブルからベース フォーマット ファイルを作成します。  次に、実際のデータ ファイルを反映するようにベース フォーマット ファイルを変更します。

### <a name="creating-a-non-xml-format-file"></a>XML 以外のフォーマット ファイルの作成<a name="nonxml_format_file"></a>
詳細については、「 [XML 以外のフォーマット ファイル (SQL Server)](../../relational-databases/import-export/non-xml-format-files-sql-server.md) 」を参照してください。 次のコマンドでは、 [bcp ユーティリティ](../../tools/bcp-utility.md) を使用し、 `myRemap.fmt`のスキーマに基づいて XML 以外のフォーマット ファイル `myRemap`を生成します。  さらに、修飾子 `c` を使用して文字データを指定し、 `t,` を使用してフィールド ターミネータとしてコンマを指定し、 `T` を使用して統合セキュリティによる信頼された接続を指定します。  コマンド プロンプトで、次のコマンドを入力します。
```
bcp TestDatabase.dbo.myRemap format nul -c -f D:\BCP\myRemap.fmt -t, -T
```
### <a name="modifying-the-non-xml-format-file"></a>XML 以外のフォーマット ファイルの変更 <a name="modify_nonxml_format_file"></a>
用語については、「 [XML 以外のフォーマット ファイルの構造](../../relational-databases/import-export/non-xml-format-files-sql-server.md#Structure) 」を参照してください。  メモ帳で `D:\BCP\myRemap.fmt` を開き、次のように変更します。
1.  行が `myRemap.bcp`のデータと同じ順序になるようにフォーマット ファイルの行の順序を配置し直します。
2.  ホスト ファイル フィールドの順序の値が順番になっていることを確認します。
3.  最後のフォーマット ファイル行の後にキャリッジ リターンがあることを確認します。

変更内容を比較します。     
**変更前**
```
13.0
4
1       SQLCHAR    0       7       ","      1     PersonID               ""
2       SQLCHAR    0       25      ","      2     FirstName              SQL_Latin1_General_CP1_CI_AS
3       SQLCHAR    0       30      ","      3     LastName               SQL_Latin1_General_CP1_CI_AS
4       SQLCHAR    0       1       "\r\n"   4     Gender                 SQL_Latin1_General_CP1_CI_AS

```
**変更後**
```
13.0
4
1       SQLCHAR    0       7       ","      1     PersonID               ""
2       SQLCHAR    0       30      ","      3     LastName               SQL_Latin1_General_CP1_CI_AS
3       SQLCHAR    0       25      ","      2     FirstName              SQL_Latin1_General_CP1_CI_AS
4       SQLCHAR    0       1       "\r\n"   4     Gender                 SQL_Latin1_General_CP1_CI_AS

```
変更されたフォーマット ファイルは次のように反映されます。
* `myRemap.bcp` の最初のデータ フィールドは最初の列にマップされます: `myRemap.. PersonID`
* `myRemap.bcp` の 2 番目のデータ フィールドは 3 番目の列にマップされます: `myRemap.. LastName`
* `myRemap.bcp` の 3 番目のデータ フィールドは 2 番目の列にマップされます: `myRemap.. FirstName`
* `myRemap.bcp` の 4 番目のデータ フィールドは 4 番目の列にマップされます: `myRemap.. Gender`

### <a name="creating-an-xml-format-file"></a>XML フォーマット ファイルの作成 <a name="xml_format_file"></a>  
詳細については、「 [XML フォーマット ファイル (SQL Server)](../../relational-databases/import-export/xml-format-files-sql-server.md) 」を参照してください。  次のコマンドでは、 [bcp ユーティリティ](../../tools/bcp-utility.md) を使用し、 `myRemap.xml`のスキーマに基づいて XML のフォーマット ファイル `myRemap`を生成します。  さらに、修飾子 `c` を使用して文字データを指定し、 `t,` を使用してフィールド ターミネータとしてコンマを指定し、 `T` を使用して統合セキュリティによる信頼された接続を指定します。  XML ベースのフォーマット ファイルを生成する場合は、 `x` 修飾子を使用する必要があります。  コマンド プロンプトで、次のコマンドを入力します。
```
bcp TestDatabase.dbo.myRemap format nul -c -x -f D:\BCP\myRemap.xml -t, -T
```
### <a name="modifying-the-xml-format-file"></a>XML フォーマット ファイルの変更 <a name="modify_xml_format_file"></a>
用語については、「 [XML フォーマット ファイルのスキーマ構文](../../relational-databases/import-export/xml-format-files-sql-server.md#StructureOfXmlFFs) 」を参照してください。  メモ帳で `D:\BCP\myRemap.xml` を開き、次のように変更します。
1. フォーマット ファイルで \<FIELD> 要素が宣言される順序は、データ ファイルでフィールドが表示される順序と同じです。したがって、ID 属性 2 と 3 を使用して \<FIELD> 要素の順序を逆にします。
2. \<FIELD> ID 属性値が順番になっていることを確認します。
3. \<ROW> 要素内の \<COLUMN> 要素の順序により、一括操作でそれらがターゲットに送信される順序が決まります。  XML フォーマット ファイルでは、一括インポート操作の対象になるテーブルの列とのリレーションシップがない各 \<COLUMN> 要素にローカル名が割り当てられます。  \<COLUMN> 要素の順序は、\<RECORD> 定義の \<FIELD> 要素の順序とは関係ありません。  各 \<COLUMN> 要素は、\<FIELD> 要素に対応しています (この要素の ID は、\<COLUMN> 要素の SOURCE 属性で指定されます)。  このため、\<COLUMN> SOURCE の値は、リビジョンを必要とする属性のみとなります。  \<COLUMN> SOURCE 属性 2 および 3 の順序を逆にします。

変更内容を比較します。  
**変更前**
```
<?xml version="1.0"?>
<BCPFORMAT xmlns="https://schemas.microsoft.com/sqlserver/2004/bulkload/format" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
 <RECORD>
  <FIELD ID="1" xsi:type="CharTerm" TERMINATOR="," MAX_LENGTH="7"/>
  <FIELD ID="2" xsi:type="CharTerm" TERMINATOR="," MAX_LENGTH="25" COLLATION="SQL_Latin1_General_CP1_CI_AS"/>
  <FIELD ID="3" xsi:type="CharTerm" TERMINATOR="," MAX_LENGTH="30" COLLATION="SQL_Latin1_General_CP1_CI_AS"/>
  <FIELD ID="4" xsi:type="CharTerm" TERMINATOR="\r\n" MAX_LENGTH="1" COLLATION="SQL_Latin1_General_CP1_CI_AS"/>
 </RECORD>
 <ROW>
  <COLUMN SOURCE="1" NAME="PersonID" xsi:type="SQLSMALLINT"/>
  <COLUMN SOURCE="2" NAME="FirstName" xsi:type="SQLVARYCHAR"/>
  <COLUMN SOURCE="3" NAME="LastName" xsi:type="SQLVARYCHAR"/>
  <COLUMN SOURCE="4" NAME="Gender" xsi:type="SQLCHAR"/>
 </ROW>
</BCPFORMAT>
```
**変更後**
```
<?xml version="1.0"?>
<BCPFORMAT xmlns="https://schemas.microsoft.com/sqlserver/2004/bulkload/format" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
 <RECORD>
  <FIELD ID="1" xsi:type="CharTerm" TERMINATOR="," MAX_LENGTH="7"/>
  <FIELD ID="2" xsi:type="CharTerm" TERMINATOR="," MAX_LENGTH="30" COLLATION="SQL_Latin1_General_CP1_CI_AS"/>
  <FIELD ID="3" xsi:type="CharTerm" TERMINATOR="," MAX_LENGTH="25" COLLATION="SQL_Latin1_General_CP1_CI_AS"/>
  <FIELD ID="4" xsi:type="CharTerm" TERMINATOR="\r\n" MAX_LENGTH="1" COLLATION="SQL_Latin1_General_CP1_CI_AS"/>
 </RECORD>
 <ROW>
  <COLUMN SOURCE="1" NAME="PersonID" xsi:type="SQLSMALLINT"/>
  <COLUMN SOURCE="3" NAME="FirstName" xsi:type="SQLVARYCHAR"/>
  <COLUMN SOURCE="2" NAME="LastName" xsi:type="SQLVARYCHAR"/>
  <COLUMN SOURCE="4" NAME="Gender" xsi:type="SQLCHAR"/>
 </ROW>
</BCPFORMAT>
```
変更されたフォーマット ファイルは次のように反映されます。
* COLUMN 1 に対応する FIELD 1 は、最初のテーブル列にマップされます: `myRemap.. PersonID`
* COLUMN 2 に対応する FIELD 2 は、3 番目のテーブル列に再マップされます: `myRemap.. LastName`
* COLUMN 3 に対応する FIELD 3 は、2 番目のテーブル列に再マップされます: `myRemap.. FirstName`
* COLUMN 4 に対応する FIELD 4 は、4 番目のテーブル列にマップされます: `myRemap.. Gender`


## <a name="importing-data-with-a-format-file-to-map-table-columns-to-data-file-field"></a>フォーマット ファイルを使用してデータをインポートして、テーブル列をデータ ファイル フィールドにマッピングする<a name="import_data"></a>
次の例では、データベース、データ ファイル、および上記で作成したフォーマット ファイルを使用します。

### <a name="using-bcp-and-non-xml-format-file"></a>[bcp](../../tools/bcp-utility.md) と [XML 以外のフォーマット ファイル](../../relational-databases/import-export/non-xml-format-files-sql-server.md)の使用<a name="bcp_nonxml"></a>
コマンド プロンプトで、次のコマンドを入力します。
```
bcp TestDatabase.dbo.myRemap IN D:\BCP\myRemap.bcp -f D:\BCP\myRemap.fmt -T
```

### <a name="using-bcp-and-xml-format-file"></a>[bcp](../../tools/bcp-utility.md) と [XML フォーマット ファイル](../../relational-databases/import-export/xml-format-files-sql-server.md)の使用<a name="bcp_xml"></a>
コマンド プロンプトで、次のコマンドを入力します。
```
bcp TestDatabase.dbo.myRemap IN D:\BCP\myRemap.bcp -f D:\BCP\myRemap.xml -T
```

### <a name="using-bulk-insert-and-non-xml-format-file"></a>[BULK INSERT](../../t-sql/statements/bulk-insert-transact-sql.md) と [XML 以外のフォーマット ファイル](../../relational-databases/import-export/non-xml-format-files-sql-server.md)の使用<a name="bulk_nonxml"></a>
Microsoft SQL Server Management Studio (SSMS) で、次の Transact SQL を実行します。
```sql
USE TestDatabase;  
GO

TRUNCATE TABLE myRemap;
BULK INSERT dbo.myRemap   
   FROM 'D:\BCP\myRemap.bcp'   
   WITH (FORMATFILE = 'D:\BCP\myRemap.fmt');  
GO  

-- review results
SELECT * FROM TestDatabase.dbo.myRemap;
```

### <a name="using-bulk-insert-and-xml-format-file"></a>[BULK INSERT](../../t-sql/statements/bulk-insert-transact-sql.md) と [XML フォーマット ファイル](../../relational-databases/import-export/xml-format-files-sql-server.md)の使用<a name="bulk_xml"></a>
Microsoft SQL Server Management Studio (SSMS) で、次の Transact SQL を実行します。
```sql
USE TestDatabase;  
GO

TRUNCATE TABLE myRemap;
BULK INSERT dbo.myRemap   
   FROM 'D:\BCP\myRemap.bcp'   
   WITH (FORMATFILE = 'D:\BCP\myRemap.xml');  
GO  

-- review results
SELECT * FROM TestDatabase.dbo.myRemap;
```

### <a name="using-openrowsetbulk-and-non-xml-format-file"></a>[OPENROWSET(BULK...)](../../t-sql/functions/openrowset-transact-sql.md) と[ XML 以外のフォーマット ファイル](../../relational-databases/import-export/non-xml-format-files-sql-server.md)の使用<a name="openrowset_nonxml"></a>    
Microsoft SQL Server Management Studio (SSMS) で、次の Transact SQL を実行します。
```sql
USE TestDatabase;
GO

TRUNCATE TABLE myRemap;
INSERT INTO dbo.myRemap
    SELECT *
    FROM OPENROWSET (
        BULK 'D:\BCP\myRemap.bcp',
        FORMATFILE = 'D:\BCP\myRemap.fmt'
        ) AS t1;
GO

-- review results
SELECT * FROM TestDatabase.dbo.myRemap;
```

### <a name="using-openrowsetbulk-and-xml-format-file"></a>[OPENROWSET(BULK...)](../../t-sql/functions/openrowset-transact-sql.md) と [XML フォーマット ファイル](../../relational-databases/import-export/xml-format-files-sql-server.md)の使用<a name="openrowset_xml"></a>
Microsoft SQL Server Management Studio (SSMS) で、次の Transact SQL を実行します。
```sql
USE TestDatabase;  
GO

TRUNCATE TABLE myRemap;
INSERT INTO dbo.myRemap 
    SELECT *
    FROM OPENROWSET (
        BULK 'D:\BCP\myRemap.bcp',
        FORMATFILE = 'D:\BCP\myRemap.xml'  
       ) AS t1;
GO

-- review results
SELECT * FROM TestDatabase.dbo.myRemap;
```


  
## <a name="see-also"></a>参照  
[bcp ユーティリティ](../../tools/bcp-utility.md)   
 [フォーマット ファイルを使用したテーブル列のスキップ &#40;SQL Server&#41;](../../relational-databases/import-export/use-a-format-file-to-skip-a-table-column-sql-server.md)   
 [フォーマット ファイルを使用したデータ フィールドのスキップ &#40;SQL Server&#41;](../../relational-databases/import-export/use-a-format-file-to-skip-a-data-field-sql-server.md)  
  
  
