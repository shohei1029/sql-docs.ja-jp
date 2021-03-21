---
title: フォーマット ファイルの作成 (SQL Server) | Microsoft Docs
description: SQL Server テーブルを一括インポートまたは一括エクスポートする場合、フォーマット ファイルを使用すると、ほとんど他のプログラムからデータ ファイルを編集したり読み取ったりすることなく、データ ファイルを出力できます。
ms.custom: ''
ms.date: 02/23/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: data-movement
ms.topic: conceptual
helpviewer_keywords:
- format files [SQL Server], creating
ms.assetid: f680b4a0-630f-4052-9c79-d348c1076f7b
author: MashaMSFT
ms.author: mathoma
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: a097686535a2e8fae1e5a26d8bcea69b91d31d0c
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104749692"
---
# <a name="create-a-format-file-sql-server"></a>フォーマット ファイルの作成 (SQL Server)
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]
  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] テーブルにデータを一括インポートする場合、またはテーブルからデータを一括エクスポートする場合、フォーマット ファイルを使用して、他のデータ形式に準拠するため、または他のソフトウェアからデータ ファイルを読み取るための編集をほとんど (あるいはまったく) 必要としないデータ ファイルを柔軟なシステムに出力できます。  
  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 非 XML 形式と XML 形式の 2 種類のフォーマット ファイルがサポートされます。 XML 以外のフォーマットとは、以前のバージョンの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]でサポートされる従来のフォーマットです。  
  
 通常は、XML フォーマット ファイルと XML 以外のフォーマット ファイルの間には互換性があります。 ただし、XML フォーマット ファイルの方が XML 以外のフォーマット ファイルよりも優れた点がいくつかあるので、新しいフォーマット ファイルには XML 構文を使用することをお勧めします。  
  
> [!NOTE]  
>  フォーマット ファイルの読み取りに使用される **bcp** ユーティリティ (Bcp.exe) のバージョンは、フォーマット ファイルの作成に使用されたバージョン、またはそれ以降のバージョンである必要があります。 たとえば、[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] の **bcp** では、[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] の **bcp** によって生成されるバージョン 10.0 のフォーマット ファイルを読み取ることができますが、[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] の **bcp** では、[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] の **bcp** によって生成されるバージョン 11.0 のフォーマット ファイルを読み取ることができません。  
  
 このトピックでは、 [bcp ユーティリティ](../../tools/bcp-utility.md) を使用して、特定のテーブルのフォーマット ファイルを作成する方法について説明します。 フォーマット ファイルは、指定されたデータ型のオプション ( **-n**、 **-c**、 **-w**、または **-N**)、およびテーブルやビューの区切り記号から構成されます。  
  
## <a name="creating-a-non-xml-format-file"></a>XML 以外のフォーマット ファイルの作成  
 **bcp** コマンドを使用してフォーマット ファイルを作成するには、 **format** 引数を指定し、データ ファイルのパスの代わりに **nul** を使用します。 **format** オプションには、次に示す **-f** オプションが必要です。  
  
 **bcp** _table_or_view_ **format** nul **-f** _format_file_name_  
  
> [!NOTE]  
> XML 以外のフォーマット ファイルであることを区別するには、MyTable.fmt のように、ファイル名拡張子として .fmt を使用することをお勧めします。  
  
 XML 以外のフォーマット ファイルの構造およびフィールドについては、「 [XML 以外のフォーマット ファイル &#40;SQL Server&#41;](../../relational-databases/import-export/non-xml-format-files-sql-server.md)でサポートされる従来のフォーマットです。  
  
### <a name="examples"></a>例  
 ここでは、 **bcp** コマンドを使用して XML 以外のフォーマット ファイルを作成する方法を示す次の例について説明します。  
  
-   A. ネイティブ データ用の XML 以外のフォーマット ファイルの作成  
  
-   B. 文字データ用の XML 以外のフォーマット ファイルの作成  
  
-   C. Unicode ネイティブ データ用の XML 以外のフォーマット ファイルの作成  
  
-   D. Unicode 文字データ用の XML 以外のフォーマット ファイルの作成  
  
-   F. コード ページ オプションを使用したフォーマット ファイルの使用  
  
 この例では、 `HumanResources.Department` サンプル データベースの [!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)] テーブルを使用しています。 `HumanResources.Department` テーブルには、 `DepartmentID`、 `Name`、 `GroupName`、および `ModifiedDate`の 4 つの列があります。  
  
#### <a name="a-creating-a-non-xml-format-file-for-native-data"></a>A. ネイティブ データ用の XML 以外のフォーマット ファイルの作成  
 次の例では、 `Department-n.xml`テーブルに対して [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)]`HumanResources.Department` という名前の XML フォーマット ファイルを作成します。 このフォーマット ファイルでは、ネイティブ データ型が使用されます。 生成されたフォーマット ファイルの内容をコマンドの後に示します。  
  
 **bcp** コマンドには、次の修飾子が含まれます。  
  
|修飾子|説明|  
|----------------|-----------------|  
|**formatnul-f** _format_file_|XML 以外のフォーマット ファイルを指定します。|  
|**-n**|ネイティブ データ型を指定します。|  
|**-T**|**bcp** ユーティリティが統合セキュリティを使用した信頼関係接続を使用して [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] に接続することを指定します。 **-T** を指定しない場合、正常にログインするには **-U** や **-P** を指定する必要があります。|  
  
 Windows コマンド プロンプトで、次の `bcp` コマンドを入力します。  
  
```cmd
bcp AdventureWorks2012.HumanResources.Department format nul -T -n -f Department-n.fmt  
```  
  
 生成されるフォーマット ファイル `Department-n.fmt`には、次の情報が含まれます。  
  
```  
12.0  
4  
1  SQLSMALLINT   0       2       ""   1     DepartmentID         ""  
2  SQLNCHAR      2       100     ""   2     Name                 SQL_Latin1_General_CP1_CI_AS  
3  SQLNCHAR      2       100     ""   3     GroupName            SQL_Latin1_General_CP1_CI_AS  
4  SQLDATETIME   0       8       ""   4     ModifiedDate         ""  
```  
  
 詳細については、「 [XML 以外のフォーマット ファイル &#40;SQL Server&#41;](../../relational-databases/import-export/non-xml-format-files-sql-server.md)でサポートされる従来のフォーマットです。  
  
#### <a name="b-creating-a-non-xml-format-file-for-character-data"></a>B. 文字データ用の XML 以外のフォーマット ファイルの作成  
 次の例では、 `Department.fmt`テーブルに対して [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)]`HumanResources.Department` という名前の XML フォーマット ファイルを作成します。 このフォーマット ファイルでは、文字データ形式と、既定と異なるフィールド ターミネータ (`,`) が使用されます。 生成されたフォーマット ファイルの内容をコマンドの後に示します。  
  
 **bcp** コマンドには、次の修飾子が含まれます。  
  
|修飾子|説明|  
|----------------|-----------------|  
|**formatnul-f** _format_file_|XML 以外のフォーマット ファイルを指定します。|  
|**-c**|文字データを指定します。|  
|**-T**|**bcp** ユーティリティが統合セキュリティを使用した信頼関係接続を使用して [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] に接続することを指定します。 **-T** を指定しない場合、正常にログインするには **-U** や **-P** を指定する必要があります。|  
  
 Windows コマンド プロンプトで、次の `bcp` コマンドを入力します。  
  
```cmd
bcp AdventureWorks2012.HumanResources.Department format nul -c -f Department-c.fmt -T  
```  
  
 生成されるフォーマット ファイル `Department-c.fmt`には、次の情報が含まれます。  
  
```  
12.0  
4  
1  SQLCHAR       0       7       "\t"     1     DepartmentID            ""  
2  SQLCHAR       0       100     "\t"     2     Name                    SQL_Latin1_General_CP1_CI_AS  
3  SQLCHAR       0       100     "\t"     3     GroupName               SQL_Latin1_General_CP1_CI_AS  
4  SQLCHAR       0       24      "\r\n"   4     ModifiedDate            ""  
```  
  
 詳細については、「 [XML 以外のフォーマット ファイル &#40;SQL Server&#41;](../../relational-databases/import-export/non-xml-format-files-sql-server.md)でサポートされる従来のフォーマットです。  
  
#### <a name="c-creating-a-non-xml-format-file-for-unicode-native-data"></a>C. Unicode ネイティブ データ用の XML 以外のフォーマット ファイルの作成  
 `HumanResources.Department` テーブルの、Unicode ネイティブ データ用の XML 以外のフォーマット ファイルを作成するには、次のコマンドを使用します。  
  
```cmd
bcp AdventureWorks2012.HumanResources.Department format nul -T -N -f Department-n.fmt  
```  
  
 Unicode ネイティブデータの使用方法の詳細については、「[Unicode ネイティブ形式を使用したデータのインポートまたはエクスポート &#40;SQL Server&#41;](../../relational-databases/import-export/use-unicode-native-format-to-import-or-export-data-sql-server.md)」を参照してください。  
  
#### <a name="d-creating-a-non-xml-format-file-for-unicode-character-data"></a>D. Unicode 文字データ用の XML 以外のフォーマット ファイルの作成  
 既定のターミネータを使用する `HumanResources.Department` テーブルの、Unicode 文字データ用の XML 以外のフォーマット ファイルを作成するには、次のコマンドを使用します。  
  
```cmd
bcp AdventureWorks2012.HumanResources.Department format nul -T -w -f Department-w.fmt  
```  
  
 Unicode 文字データの使用方法の詳細については、「[Unicode 文字形式を使用したデータのインポートまたはエクスポート &#40;SQL Server&#41;](../../relational-databases/import-export/use-unicode-character-format-to-import-or-export-data-sql-server.md)」を参照してください。  
  
#### <a name="f-using-a-format-file-with-the-code-page-option"></a>F. コード ページ オプションを使用したフォーマット ファイルの使用  
bcp コマンドを使用して (つまり `bcp format` を使用して) フォーマット ファイルを作成する場合、照合順序/コード ページに関する情報がフォーマット ファイルに記述されます。   
次に、照合順序が含まれる、5 つの列があるテーブル用のフォーマット ファイルの例を示します。  
  
```  
13.0  
5  
1  SQLCHAR         0       0       "**\t**"         1     c_0          Cyrillic_General_CS_AS  
2  SQLCHAR         0       0       "**\t**"         2     c_1          Cyrillic_General_CS_AS  
3  SQLCHAR         0       3000    "**\t**"         3     c_2          Cyrillic_General_CS_AS  
4  SQLCHAR         0       5       "**\t**"         4     c_3          ""  
5  SQLCHAR         0       41      "!!!\r\r\n"      5     c_4          ""  
  
```  
  
 "`bcp in -c -C65001 -f format_file` ..." または "`BULK INSERT`/`OPENROWSET` ... `FORMATFILE='format_file' CODEPAGE=65001` ..." を使用して [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] にデータをインポートすると、65001 を超える照合順序またはコード ページ情報のオプションが存在するようになります。  
そのため、フォーマット ファイルを生成する場合、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]にデータをインポートし直す前に、生成されたフォーマット ファイルから照合順序の情報を削除する必要があります。  
次に照合順序情報を含まないフォーマット ファイルの例を示します。  
  
```  
13.0  
5  
1  SQLCHAR         0       0       "**\t**"         1     c_0              ""  
2  SQLCHAR         0       0       "**\t**"         2     c_1              ""  
3  SQLCHAR         0       3000    "**\t**"         3     c_2              ""  
4  SQLCHAR         0       5       "**\t**"         4     c_3              ""  
5  SQLCHAR         0       41      "!!!\r\r\n"      5     c_4              ""  
```  
  
## <a name="creating-an-xml-format-file"></a>XML フォーマット ファイルの作成  
 **bcp** コマンドを使用してフォーマット ファイルを作成するには、 **format** 引数を指定し、データ ファイルのパスの代わりに **nul** を使用します。 **format** オプションには常に **-f** オプションが必要です。XML フォーマット ファイルを作成するには、次に示すように **-x** オプションも指定する必要があります。  
  
 **bcp** _table_or_view_ **format nul-f** _format_file_name_ **-x**  
  
> [!NOTE]  
> XML フォーマット ファイルであることを区別するには、MyTable.xml のように、ファイル名拡張子として .xml を使用することをお勧めします。  
  
 XML フォーマット ファイルの構造およびフィールドについては、「 [XML フォーマット ファイル &#40;SQL Server&#41;](../../relational-databases/import-export/xml-format-files-sql-server.md)でサポートされる従来のフォーマットです。  
  
### <a name="examples"></a>例  
 ここでは、 **bcp** コマンドを使用して XML フォーマット ファイルを作成する方法を示す次の例について説明します。  
  
-   A. 文字データ用の XML フォーマット ファイルの作成  
-   B. ネイティブ データ用の XML フォーマット ファイルの作成  
  
 この例では、 `HumanResources.Department` サンプル データベースの [!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)] テーブルを使用しています。 `HumanResources.Department` テーブルには、 `DepartmentID`、 `Name`、 `GroupName`、および `ModifiedDate`の 4 つの列があります。  
  
> [!NOTE]  
>  [!INCLUDE[ssSampleDBdesc](../../includes/sssampledbdesc-md.md)]  
  
#### <a name="a-creating-an-xml-format-file-for-character-data"></a>A. 文字データ用の XML フォーマット ファイルの作成  
 次の例では、 `Department.xml`テーブルに対して [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)]`HumanResources.Department` という名前の XML フォーマット ファイルを作成します。 このフォーマット ファイルでは、文字データ形式と、既定と異なるフィールド ターミネータ (`,`) が使用されます。 生成されたフォーマット ファイルの内容をコマンドの後に示します。  
  
 **bcp** コマンドには、次の修飾子が含まれます。  
  
|修飾子|説明|  
|----------------|-----------------|  
|**formatnul-f** _format_file_ **-x**|XML フォーマット ファイルを指定します。|  
|**-c**|文字データを指定します。|  
|**-t** `,`|コンマ ( **,** ) をフィールド ターミネータとして指定します。<br /><br /> 注: データ ファイルで既定のフィールド ターミネータ (`\t`) が使用されている場合、 **-t** スイッチは不要です。|  
|**-T**|**bcp** ユーティリティが統合セキュリティを使用した信頼関係接続を使用して [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] に接続することを指定します。 **-T** を指定しない場合、正常にログインするには **-U** や **-P** を指定する必要があります。|  
  
 Windows コマンド プロンプトで、次の `bcp` コマンドを入力します。  
  
```cmd
bcp AdventureWorks2012.HumanResources.Department format nul -c -x -f Department-c.xml -t, -T  
```  
  
 生成されるフォーマット ファイル `Department-c.xml`には、次の XML 要素が含まれます。  
  
```xml
<?xml version="1.0"?>  
<BCPFORMAT xmlns="https://schemas.microsoft.com/sqlserver/2004/bulkload/format" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">  
 <RECORD>  
  <FIELD ID="1" xsi:type="CharTerm" TERMINATOR="," MAX_LENGTH="7"/>  
  <FIELD ID="2" xsi:type="CharTerm" TERMINATOR="," MAX_LENGTH="100" COLLATION="SQL_Latin1_General_CP1_CI_AS"/>  
  <FIELD ID="3" xsi:type="CharTerm" TERMINATOR="," MAX_LENGTH="100" COLLATION="SQL_Latin1_General_CP1_CI_AS"/>  
  <FIELD ID="4" xsi:type="CharTerm" TERMINATOR="\r\n" MAX_LENGTH="24"/>  
 </RECORD>  
 <ROW>  
  <COLUMN SOURCE="1" NAME="DepartmentID" xsi:type="SQLSMALLINT"/>  
  <COLUMN SOURCE="2" NAME="Name" xsi:type="SQLNVARCHAR"/>  
  <COLUMN SOURCE="3" NAME="GroupName" xsi:type="SQLNVARCHAR"/>  
  <COLUMN SOURCE="4" NAME="ModifiedDate" xsi:type="SQLDATETIME"/>  
 </ROW>  
</BCPFORMAT>  
```  
  
 このフォーマット ファイルの構文については、「[XML フォーマット ファイル &#40;SQL Server&#41;](../../relational-databases/import-export/xml-format-files-sql-server.md)」を参照してください。 文字データについては、「[文字形式を使用したデータのインポートまたはエクスポート &#40;SQL Server&#41;](../../relational-databases/import-export/use-character-format-to-import-or-export-data-sql-server.md)」を参照してください。  
  
#### <a name="b-creating-an-xml-format-file-for-native-data"></a>B. ネイティブ データ用の XML フォーマット ファイルの作成  
 次の例では、 `Department-n.xml`テーブルに対して `HumanResources.Department` という名前の XML フォーマット ファイルを作成します。 このフォーマット ファイルでは、ネイティブ データ型が使用されます。 生成されたフォーマット ファイルの内容をコマンドの後に示します。  
  
 **bcp** コマンドには、次の修飾子が含まれます。  
  
|修飾子|説明|  
|----------------|-----------------|  
|**formatnul-f** _format_file_ **-x**|XML フォーマット ファイルを指定します。|  
|**-n**|ネイティブ データ型を指定します。|  
|**-T**|**bcp** ユーティリティが統合セキュリティを使用した信頼関係接続を使用して [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] に接続することを指定します。 **-T** を指定しない場合、正常にログインするには **-U** や **-P** を指定する必要があります。|  
  
 Windows コマンド プロンプトで、次の `bcp` コマンドを入力します。  
  
```cmd
bcp AdventureWorks2012.HumanResources.Department format nul -x -f Department-n.xml -n -T  
```  
  
 生成されるフォーマット ファイル `Department-n.xml`には、次の XML 要素が含まれます。  
  
```xml
<?xml version="1.0"?>  
<BCPFORMAT xmlns="https://schemas.microsoft.com/sqlserver/2004/bulkload/format" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">  
 <RECORD>  
  <FIELD ID="1" xsi:type="NativeFixed" LENGTH="2"/>  
  <FIELD ID="2" xsi:type="NCharPrefix" PREFIX_LENGTH="2" MAX_LENGTH="100" COLLATION="SQL_Latin1_General_CP1_CI_AS"/>  
  <FIELD ID="3" xsi:type="NCharPrefix" PREFIX_LENGTH="2" MAX_LENGTH="100" COLLATION="SQL_Latin1_General_CP1_CI_AS"/>  
  <FIELD ID="4" xsi:type="NativeFixed" LENGTH="8"/>  
 </RECORD>  
 <ROW>  
  <COLUMN SOURCE="1" NAME="DepartmentID" xsi:type="SQLSMALLINT"/>  
  <COLUMN SOURCE="2" NAME="Name" xsi:type="SQLNVARCHAR"/>  
  <COLUMN SOURCE="3" NAME="GroupName" xsi:type="SQLNVARCHAR"/>  
  <COLUMN SOURCE="4" NAME="ModifiedDate" xsi:type="SQLDATETIME"/>  
 </ROW>  
</BCPFORMAT>  
```  
  
 このフォーマット ファイルの構文については、「[XML フォーマット ファイル &#40;SQL Server&#41;](../../relational-databases/import-export/xml-format-files-sql-server.md)」を参照してください。 ネイティブ データの使用方法については、「[ネイティブ形式を使用したデータのインポートまたはエクスポート &#40;SQL Server&#41;](../../relational-databases/import-export/use-native-format-to-import-or-export-data-sql-server.md)」を参照してください。  
  
## <a name="mapping-data-fields-to-table-columns"></a>データ フィールドからテーブル列へのマッピング  
 **bcp** によって作成されたフォーマット ファイルには、すべてのテーブル列が順番に記述されます。 テーブル行を再配置または削除する場合は、フォーマット ファイルを変更できます。 その結果、フィールドとテーブル列とが直接マップされないデータ ファイル用に、フォーマット ファイルをカスタマイズできます。 詳細については、次のトピックを参照してください。  
  
-   [フォーマット ファイルを使用したテーブル列のスキップ &#40;SQL Server&#41;](../../relational-databases/import-export/use-a-format-file-to-skip-a-table-column-sql-server.md)  
  
-   [フォーマット ファイルを使用したデータ フィールドのスキップ &#40;SQL Server&#41;](../../relational-databases/import-export/use-a-format-file-to-skip-a-data-field-sql-server.md)  
  
-   [フォーマット ファイルを使用したテーブル列とデータ ファイル フィールドのマッピング &#40;SQL Server&#41;](../../relational-databases/import-export/use-a-format-file-to-map-table-columns-to-data-file-fields-sql-server.md)  
  
## <a name="see-also"></a>参照  
 [bcp ユーティリティ](../../tools/bcp-utility.md)   
 [フォーマット ファイルを使用したテーブル列とデータ ファイル フィールドのマッピング &#40;SQL Server&#41;](../../relational-databases/import-export/use-a-format-file-to-map-table-columns-to-data-file-fields-sql-server.md)   
 [フォーマット ファイルを使用したテーブル列のスキップ &#40;SQL Server&#41;](../../relational-databases/import-export/use-a-format-file-to-skip-a-table-column-sql-server.md)   
 [フォーマット ファイルを使用したデータ フィールドのスキップ &#40;SQL Server&#41;](../../relational-databases/import-export/use-a-format-file-to-skip-a-data-field-sql-server.md)   
 [XML 以外のフォーマット ファイル &#40;SQL Server&#41;](../../relational-databases/import-export/non-xml-format-files-sql-server.md)   
 [XML フォーマット ファイル &#40;SQL Server&#41;](../../relational-databases/import-export/xml-format-files-sql-server.md)  
  
  
