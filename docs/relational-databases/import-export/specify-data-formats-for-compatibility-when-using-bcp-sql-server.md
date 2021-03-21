---
title: bcp で互換性データ形式を指定する
description: bcp を使用した SQL Server での一括エクスポートでは、データ形式が想定されるレイアウトと互換性がない可能性があります。 XML 以外のフォーマット ファイルでは、互換性データ形式を指定します。
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: data-movement
ms.topic: conceptual
helpviewer_keywords:
- bulk exporting [SQL Server], compatibility
- bulk importing [SQL Server], compatibility
- compatibility [SQL Server], data formats
- data formats [SQL Server], compatibility
- bcp utility [SQL Server], compatibility
ms.assetid: cd5fc8c8-eab1-4165-9468-384f31e53f0a
author: MashaMSFT
ms.author: mathoma
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.custom: seo-lt-2019
ms.openlocfilehash: 41f983239a2cf946bb46e04315cff69ba3b2abc2
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104751022"
---
# <a name="specify-compatibility-data-formats-when-using-bcp-sql-server"></a>bcp を使用して互換性データ形式を指定する (SQL Server)
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]
  このトピックでは、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]**bcp** コマンドのデータ形式属性、フィールド固有のプロンプト、および XML 以外のフォーマット ファイルでのフィールドごとのデータの格納について説明します。 このトピックの内容は、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] データを一括エクポートして別のプログラム (別のデータベース プログラムなど) に一括インポートする場合に有用です。 ソース テーブルの既定のデータ形式 (ネイティブ、文字、または Unicode) が、他のプログラムで想定されているデータ レイアウトと互換性がない場合があります。互換性がない場合はデータをエクスポートするときに、データ レイアウトを記述する必要があります。  
  
> [!NOTE]  
>  データをインポートまたはエクスポートするためのデータ形式に精通していない場合は、「 [一括インポートまたは一括エクスポートのデータ形式 &#40;SQL Server&#41;](../../relational-databases/import-export/data-formats-for-bulk-import-or-bulk-export-sql-server.md)」を参照してください。  
  
  
##  <a name="bcp-data-format-attributes"></a><a name="bcpDataFormatAttr"></a> bcp データ形式の属性  
 **bcp** コマンドにより、次のデータ形式属性に関して、データ ファイル内の各フィールドの構造を指定できます。  
  
-   ファイル ストレージ型  
  
     *ファイル ストレージ型* は、データ ファイルへのデータの格納方法を記述します。 データ ファイルには、データベース テーブルの型 (ネイティブ形式)、文字表現 (文字形式)、または暗黙的な型変換がサポートされているデータ型のいずれかでデータをエクスポートできます。暗黙的な型変換では、たとえば、 **smallint** は **int** としてコピーされます。ユーザー定義のデータ型は、基本データ型としてエクスポートされます。 詳細については、「 [bcp を使用したファイル ストレージ型の指定 &#40;SQL Server&#41;](../../relational-databases/import-export/specify-file-storage-type-by-using-bcp-sql-server.md)」を参照してください。  
  
-   プレフィックス長  
  
     **bcp** コマンドでは、ネイティブ形式のデータをデータ ファイルに一括エクスポートするためのファイル ストレージが最も少なくなるように、各フィールドの前にそのフィールドの長さを 1 文字以上の文字列で指定します。 このような文字列を、 *プレフィックス長文字列* と呼びます。 詳細については、「 [bcp を使用したデータ ファイルのプレフィックス長の指定 &#40;SQL Server&#41;](../../relational-databases/import-export/specify-prefix-length-in-data-files-by-using-bcp-sql-server.md)」を参照してください。  
  
-   フィールド長  
  
     フィールド長は、文字形式でデータを表現するために必要な文字の最大数を示します。 データがネイティブ形式で格納される場合、フィールド長は既にわかっています。 詳細については、「 [bcp を使用したフィールド長の指定 &#40;SQL Server&#41;](../../relational-databases/import-export/specify-field-length-by-using-bcp-sql-server.md)」を参照してください。  
  
-   フィールド ターミネータ  
  
     文字列データ フィールドでは、省略可能なターミネータ文字を使用して、データ ファイルの各フィールドの末尾 ( *フィールド ターミネータ* を使用) と各行の末尾 ( *行ターミネータ* を使用) を示すことができます。 ターミネータ文字は、フィールドや行の終了位置と次のフィールドや行の開始位置を、データ ファイルを読み取るプログラムに示す方法の 1 つです。 詳細については、「 [フィールド ターミネータと行ターミネータの指定 &#40;SQL Server&#41;](../../relational-databases/import-export/specify-field-and-row-terminators-sql-server.md)」を参照してください。  
  
  
##  <a name="overview-of-the-field-specific-prompts"></a><a name="FieldSpecificPrompts"></a> フィールド固有のプロンプトの概要  
 対話的な **bcp** コマンドに **in** オプションまたは **out** オプションが含まれていて、フォーマット ファイル スイッチ ( **-f**) またはデータ形式スイッチ ( **-n**、 **-c**、 **-w**、または **-N**) のいずれも含まれていない場合は、ソース テーブルとターゲット テーブルの各列で、上記の属性について順番に問い合わせが行われます。 問い合わせが行われる際は、 **bcp** コマンドにより、テーブル列の [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] データ型に基づいてそれぞれ既定値が表示されます。 すべての問い合わせに対して既定値を受け入れることは、コマンド ラインでネイティブ形式 ( **-n**) を指定するのと同じ結果になります。 各プロンプトには、[*default*] のように既定値が角かっこ付きで表示されます。 表示される既定値を受け入れるには、Enter キーを押します。 既定値以外を指定するには、プロンプトで新しい値を入力します。  
  
### <a name="example"></a>例  
 次の例では、 **bcp** コマンドを使用して、 `HumanResources.myTeam` テーブルから `myTeam.txt` ファイルに、データを対話的に一括エクスポートします。 このテーブルを作成しないと、例を実行できません。 テーブルの詳細とテーブルを作成する方法については、「[HumanResources.myTeam サンプル テーブル &#40;SQL Server&#41;](../../relational-databases/import-export/humanresources-myteam-sample-table-sql-server.md)」を参照してください。  
  
 コマンドでフォーマット ファイルもデータ型も指定しないと、 **bcp** からデータ形式情報が要求されます。 [!INCLUDE[msCoName](../../includes/msconame-md.md)] Windows コマンド プロンプトで、次のように入力します。  
  
```cmd
bcp AdventureWorks.HumanResources.myTeam out myTeam.txt -T  
```  
  
 列ごとに、bcp からフィールド固有の値が要求されます。 次の例は、テーブルの `EmployeeID` 列と `Name` 列のフィールド固有のプロンプトを示しています。また、各列の既定のファイル保存形式 (ネイティブ形式) も示しています。 `EmployeeID` 列と `Name` 列のプレフィックス長は、それぞれ 0 と 2 です。 ここでは、各フィールドのターミネータとして、ユーザーがコンマ (`,`) を指定します。  
  
 `Enter the file storage type of field EmployeeID [smallint]:`  
  
 `Enter prefix-length of field EmployeeID [0]:`  
  
 `Enter field terminator [none]:,`  
  
 `Enter the file storage type of field Name [nvarchar]:`  
  
 `Enter prefix length of field Name [2]:`  
  
 `Enter field terminator [none]:,`  
  
 `.`  
  
 `.`  
  
 `.`  
  
 テーブルの列ごとに、列の順番に従って、同等のプロンプトが (必要に応じて) 表示されます。  
  
  
##  <a name="storing-field-by-field-data-in-a-non-xml-format-file"></a><a name="FieldByFieldNonXmlFF"></a> XML 以外のフォーマット ファイルでのフィールドごとのデータの格納  
 すべてのテーブル列の情報を指定すると、 **bcp** コマンドから XML 以外のフォーマット ファイルを生成することを求められます。この生成は省略可能です。このフォーマット ファイルには、プロンプトに応じて指定したフィールドごとの情報が格納されます (上記の例を参照)。 フォーマット ファイルを生成することを選択すると、いつでも、そのテーブルからデータをエクスポートしたり、同じような構造のデータを [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]にインポートできます。  
  
> [!NOTE]  
>  フォーマット ファイルを使用して、データ ファイルから [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスにデータを一括インポートできます。また、形式を再度指定しないで、そのテーブルからデータを一括エクスポートできます。 詳細については、「 [データのインポートまたはエクスポート用のフォーマット ファイル &#40;SQL Server&#41;](../../relational-databases/import-export/format-files-for-importing-or-exporting-data-sql-server.md)」を参照してください。  
  
 次の例では、 `myFormatFile.fmt`という名前の XML 以外のフォーマット ファイルを作成します。  
  
 `Do you want to save this format information in a file? [Y/n] y`  
  
 `Host filename: [bcp.fmt]myFormatFile.fmt`  
  
 フォーマット ファイルの既定の名前は bcp.fmt ですが、必要に応じて別のファイル名を指定できます。  
  
> [!NOTE]  
>  文字形式やネイティブ形式など、ファイル保存形式に 1 つのデータ形式を使用するデータ ファイルの場合は、 **format** オプションを使用することで、データをエクスポートまたはインポートしなくても、フォーマット ファイルをすばやく作成できます。 この方法は簡単で、XML フォーマット ファイルと XML 以外のフォーマット ファイルのどちらも作成できるという利点があります。 詳細については、「[フォーマット ファイルの作成 &#40;SQL Server&#41;](../../relational-databases/import-export/create-a-format-file-sql-server.md)」をご覧ください。  
  
  
## <a name="related-tasks"></a>Related Tasks  
  
-   [bcp を使用したファイル ストレージ型の指定 &#40;SQL Server&#41;](../../relational-databases/import-export/specify-file-storage-type-by-using-bcp-sql-server.md)  
  
-   [bcp を使用したデータ ファイルのプレフィックス長の指定 &#40;SQL Server&#41;](../../relational-databases/import-export/specify-prefix-length-in-data-files-by-using-bcp-sql-server.md)  
  
-   [bcp を使用したフィールド長の指定 &#40;SQL Server&#41;](../../relational-databases/import-export/specify-field-length-by-using-bcp-sql-server.md)  
  
-   [フィールド ターミネータと行ターミネータの指定 &#40;SQL Server&#41;](../../relational-databases/import-export/specify-field-and-row-terminators-sql-server.md)  
  
## <a name="related-content"></a>関連コンテンツ  
 [なし] :  
  
## <a name="see-also"></a>参照  
 [データの一括インポートと一括エクスポート &#40;SQL Server&#41;](../../relational-databases/import-export/bulk-import-and-export-of-data-sql-server.md)   
 [一括インポートまたは一括エクスポートのデータ形式 &#40;SQL Server&#41;](../../relational-databases/import-export/data-formats-for-bulk-import-or-bulk-export-sql-server.md)   
 [bcp ユーティリティ](../../tools/bcp-utility.md)   
 [データ型 &#40;Transact-SQL&#41;](../../t-sql/data-types/data-types-transact-sql.md)  
  
  
