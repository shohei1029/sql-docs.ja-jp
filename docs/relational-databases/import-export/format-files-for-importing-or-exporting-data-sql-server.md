---
title: データをインポートおよびエクスポートするためのフォーマット ファイル
description: SQL Server テーブルに一括インポートする場合、またはテーブルから一括エクスポートする場合、フォーマット ファイルには、テーブルに対応するデータ ファイルのフィールド形式情報を格納できます。
ms.date: 09/25/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: data-movement
ms.topic: conceptual
helpviewer_keywords:
- bulk exporting [SQL Server], format files
- bulk importing [SQL Server], format files
- format files [SQL Server]
ms.assetid: b7b97d68-4336-4091-aee4-1941fab568e3
author: MashaMSFT
ms.author: mathoma
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.custom: seo-lt-2019
ms.openlocfilehash: bfd6056469ee1a61696ffe0f51178a3e1a065419
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104749672"
---
# <a name="format-files-to-import-or-export-data-sql-server"></a>データをインポートまたはエクスポートするためのフォーマット ファイル (SQL Server)

[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]
[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] テーブルにデータを一括インポートしたり、テーブルからデータを一括エクスポートしたりする場合、 *フォーマット ファイル* を使用して、データの一括エクスポートと一括インポートに必要なすべてのフォーマット情報を格納できます。 これには、そのテーブルに対応するデータ ファイル内の各フィールドのフォーマット情報が含まれます。

[!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] では、次の 2 種類のフォーマット ファイルがサポートされます: XML の形式と XML 以外のフォーマット ファイル。 XML 以外のフォーマット ファイルにも XML フォーマット ファイルにもデータ ファイル内のすべてのフィールドの説明が含まれており、XML フォーマット ファイルには対応するテーブル列の説明も含まれています。 通常は、XML フォーマット ファイルと XML 以外のフォーマット ファイルの間には互換性があります。 ただし、XML フォーマット ファイルの方が XML 以外のフォーマット ファイルよりも優れた点がいくつかあるので、新しいフォーマット ファイルには XML 構文を使用することをお勧めします。 詳細については、「 [XML フォーマット ファイル &#40;SQL Server&#41;](../../relational-databases/import-export/xml-format-files-sql-server.md)です。

## <a name="benefits-of-format-files"></a><a name="Benefits"></a> フォーマット ファイルの利点

- 他のデータ形式に準拠したり、他のソフトウェアからデータ ファイルを読み取るための編集をほとんど (あるいはまったく) 行うことなく、データ ファイルを出力できる柔軟なシステムが実現します。
- 不要なデータを追加または削除したり、データ ファイル内の既存のデータを並べ替えたりしなくても、データを一括インポートできます。 フォーマット ファイルは、データ ファイルのフィールドとテーブルの列間に不一致がある場合に特に役立ちます。

## <a name="examples-of-format-files"></a><a name="ExamplesOfFFs"></a> フォーマット ファイルの例

次の例では、XML 以外のフォーマット ファイルと XML フォーマット ファイルのレイアウトを示します。 これらのフォーマット ファイルは、 `HumanResources.myTeam` サンプル データベースの [!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)] テーブルに対応しています。 このテーブルには、 `EmployeeID`、 `Name`、 `Title`、および `ModifiedDate`という 4 つの列があります。

> [!NOTE]
> このテーブルの詳細とテーブルを作成する方法については、「[HumanResources.myTeam サンプル テーブル &#40;SQL Server&#41;](../../relational-databases/import-export/humanresources-myteam-sample-table-sql-server.md)」を参照してください。

### <a name="a-using-a-non-xml-format-file"></a>A. XML 以外のフォーマット ファイルの使用

次に示す XML 以外のフォーマット ファイルでは、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] テーブルに `HumanResources.myTeam` ネイティブ データ形式を使用します。 このフォーマット ファイルは、次の `bcp` コマンドを使用して作成されました。

```cmd
bcp AdventureWorks.HumanResources.myTeam format nul -f myTeam.Fmt -n -T
The contents of this format file are as follows: 9.0
4
1       SQLSMALLINT   0       2       ""   1     EmployeeID               ""  
2       SQLNCHAR      2       100     ""   2     Name                     SQL_Latin1_General_CP1_CI_AS  
3       SQLNCHAR      2       100     ""   3     Title                    SQL_Latin1_General_CP1_CI_AS  
4       SQLNCHAR      2       100     ""   4     Background               SQL_Latin1_General_CP1_CI_AS  
```  

詳細については、「 [XML 以外のフォーマット ファイル &#40;SQL Server&#41;](../../relational-databases/import-export/non-xml-format-files-sql-server.md)でサポートされる従来のフォーマットです。

### <a name="b-using-an-xml-format-file"></a>B. XML フォーマット ファイルの使用

次に示す XML フォーマット ファイルでは、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] テーブルに `HumanResources.myTeam` ネイティブ データ形式を使用します。 このフォーマット ファイルは、次の `bcp` コマンドを使用して作成されました。

```cmd
bcp AdventureWorks.HumanResources.myTeam format nul -f myTeam.Xml -x -n -T
```

このフォーマット ファイルの内容を次に示します。

```xml
<?xml version="1.0"?>
<BCPFORMAT xmlns="https://schemas.microsoft.com/sqlserver/2004/bulkload/format" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
<RECORD>
  <FIELD ID="1" xsi:type="NativePrefix" LENGTH="1"/>
  <FIELD ID="2" xsi:type="NCharPrefix" PREFIX_LENGTH="2" MAX_LENGTH="100" COLLATION="SQL_Latin1_General_CP1_CI_AS"/>
  <FIELD ID="3" xsi:type="NCharPrefix" PREFIX_LENGTH="2" MAX_LENGTH="100" COLLATION="SQL_Latin1_General_CP1_CI_AS"/>
  <FIELD ID="4" xsi:type="NCharPrefix" PREFIX_LENGTH="2" MAX_LENGTH="100" COLLATION="SQL_Latin1_General_CP1_CI_AS"/>
 </RECORD>
 <ROW>
  <COLUMN SOURCE="1" NAME="EmployeeID" xsi:type="SQLSMALLINT"/>
  <COLUMN SOURCE="2" NAME="Name" xsi:type="SQLNVARCHAR"/>
  <COLUMN SOURCE="3" NAME="Title" xsi:type="SQLNVARCHAR"/>
  <COLUMN SOURCE="4" NAME="Background" xsi:type="SQLNVARCHAR"/>
</ROW>
</BCPFORMAT>
```

詳細については、「 [XML フォーマット ファイル &#40;SQL Server&#41;](../../relational-databases/import-export/xml-format-files-sql-server.md)です。 

## <a name="when-is-a-format-file-required"></a><a name="WhenFFrequired"></a> フォーマット ファイルが必要になるケース

- INSERT ...SELECT * FROM OPENROWSET(BULK...) ステートメントでは、常にフォーマット ファイルが必要です。
- **bcp** または BULK INSERT の場合、単純な状況では、フォーマット ファイルを使用しなくてもかまいません。必要になることはほとんどありません。 ただし、複雑な一括インポート操作を実行する場合は、頻繁にフォーマット ファイルが必要になります。

次の場合は、フォーマット ファイルが必要です。

- 1 つのデータ ファイルが、スキーマが異なる複数のテーブルのソースとして使用される場合。
- データ ファイルのフィールド数と対象のテーブルの列数が異なる場合。次に例を示します。

  - 対象のテーブルに、既定値が定義されているか、NULL 値が許可されている列が 1 つ以上含まれている。
  - ユーザーがテーブルの 1 つ以上の列に対する SELECT/INSERT 権限を持っていない。
  - スキーマが異なる複数のテーブルで、1 つのデータ ファイルが使用されている。

- 列の順序がデータ ファイルとテーブルとの間で異なる場合。
- 終了文字またはプレフィックス長がデータ ファイルの列によって異なる場合。

> [!NOTE]
> フォーマット ファイルが存在しない場合に、 **bcp** コマンドで data-format スイッチ ( **-n**、 **-c**、 **-w**、または **-N**) を指定するか、BULK INSERT 操作で DATAFILETYPE オプションを指定すると、指定したデータ形式がデータ ファイルのフィールドを解釈するための既定の方法として使用されます。

## <a name="related-tasks"></a><a name="RelatedTasks"></a> 関連タスク

- [フォーマット ファイルの作成 &#40;SQL Server&#41;](../../relational-databases/import-export/create-a-format-file-sql-server.md)
- [データの一括インポートでのフォーマット ファイルの使用 &#40;SQL Server&#41;](../../relational-databases/import-export/use-a-format-file-to-bulk-import-data-sql-server.md)
- [フォーマット ファイルを使用したテーブル列のスキップ &#40;SQL Server&#41;](../../relational-databases/import-export/use-a-format-file-to-skip-a-table-column-sql-server.md)
- [フォーマット ファイルを使用したデータ フィールドのスキップ &#40;SQL Server&#41;](../../relational-databases/import-export/use-a-format-file-to-skip-a-data-field-sql-server.md)
- [フォーマット ファイルを使用したテーブル列とデータ ファイル フィールドのマッピング &#40;SQL Server&#41;](../../relational-databases/import-export/use-a-format-file-to-map-table-columns-to-data-file-fields-sql-server.md)

## <a name="see-also"></a>参照

- [XML 以外のフォーマット ファイル &#40;SQL Server&#41;](../../relational-databases/import-export/non-xml-format-files-sql-server.md)
- [XML フォーマット ファイル &#40;SQL Server&#41;](../../relational-databases/import-export/xml-format-files-sql-server.md)
- [一括インポートまたは一括エクスポートのデータ形式 &#40;SQL Server&#41;](../../relational-databases/import-export/data-formats-for-bulk-import-or-bulk-export-sql-server.md)
