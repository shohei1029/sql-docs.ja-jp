---
title: bcp を使用したファイル ストレージ型の指定
description: bcp を使用して、データベース テーブルの型、その文字表現、または暗黙的な型変換がサポートされているデータ型として、データをファイルにエクスポートします。
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: data-movement
ms.topic: conceptual
helpviewer_keywords:
- bcp utility [SQL Server], file storage types
- importing data, file storage types
- native data format [SQL Server]
- file storage types [SQL Server]
- data formats [SQL Server], file storage types
ms.assetid: 85e12df8-1be7-4bdc-aea9-05aade085c06
author: MashaMSFT
ms.author: mathoma
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.custom: seo-lt-2019
ms.openlocfilehash: ffcde703125ecc798177461343b98da6077c534f
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104752272"
---
# <a name="specify-file-storage-type-using-bcp-sql-server"></a>bcp を使用したファイル ストレージ型の指定 (SQL Server)
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]
  *ファイル ストレージ型* は、データ ファイルへのデータの格納方法を記述します。 データ ファイルには、データベース テーブルの型 (ネイティブ形式)、文字表現 (文字形式)、または暗黙的な型変換がサポートされているデータ型のいずれかでデータをエクスポートできます。暗黙的な型変換では、たとえば、 **smallint** は **int** としてコピーされます。ユーザー定義のデータ型は、基本データ型としてエクスポートされます。  
  
## <a name="the-bcp-prompt-for-file-storage-type"></a>ファイル ストレージ型の bcp プロンプト  
 対話型の **bcp** コマンドで、フォーマット ファイル スイッチ ( **-f** ) またはデータ形式スイッチ ( **-n** 、 **-c**、 **-w**、または **-N**) のどちらも付けずに **in** または **out** オプションを指定すると、次のように各データ フィールドのファイル ストレージ型を要求するプロンプトが表示されます。  
  
 `Enter the file storage type of field <field_name> [<default>]:`  
  
 この要求への応答は、次のように、実行するタスクによって異なります。  
  
-   できるだけコンパクトなストレージ型 (ネイティブ データ形式) で [!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスからデータ ファイルにデータを一括エクスポートするには、**bcp** によって提供される既定のファイル ストレージ型をそのまま使用します。 ネイティブのファイル ストレージ型の一覧については、このトピックの「ネイティブのファイル ストレージ型」を参照してください。  
  
-   文字形式で [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスからデータ ファイルにデータを一括エクスポートするには、テーブルのすべての列にファイル ストレージ型として **char** を指定します。  
  
-   データ ファイルから [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスにデータを一括インポートするには、文字形式で格納されたデータの場合はファイル ストレージ型として **char** を指定し、ネイティブ データ型形式で格納されたデータの場合は、必要に応じて次のファイル ストレージ型のいずれかを指定します。  
  
    |ファイル ストレージ型|コマンド プロンプトで入力する文字|  
    |-----------------------|-----------------------------|  
    |**char**|**c** **[har]**|  
    |**varchar**|**c[har]**|  
    |**nchar**|**w**|  
    |**nvarchar**|**w**|  
    |**text**|**T** **[ext]**|  
    |**ntext2**|**W**|  
    |**[バイナリ]**|**x**|  
    |**varbinary**|**x**|  
    |**image**|**I** **[mage]**|  
    |**datetime**|**d[ate]**|  
    |**smalldatetime**|**D**|  
    |**time**|**te**|  
    |**date**|**de**|  
    |**datetime2**|**d2**|  
    |**datetimeoffset**|**do**|  
    |**decimal**|**n**|  
    |**numeric**|**n**|  
    |**float**|**f[loat]**|  
    |**real**|**r**|  
    |**Int**|**i[nt]**|  
    |**bigint**|**B[igint]**|  
    |**smallint**|**s[mallint]**|  
    |**tinyint**|**t[inyint]**|  
    |**money**|**m[oney]**|  
    |**smallmoney**|**M**|  
    |**bit**|**b[it]**|  
    |**uniqueidentifier**|**u**|  
    |**sql_variant**|**V[ariant]**|  
    |**timestamp**|**x**|  
    |**UDT** (ユーザー定義データ型)|**U**|  
    |**XML**|**X**|  
  
     \***char** ファイル ストレージ型でエクスポートされた非文字データのデータ ファイルの場合、このファイルに割り当てられる格納領域のサイズは、フィールド長、プレフィックス長、およびターミネータの相互関係で決まります。  
  
     \*\***ntext**、 **text**、および **image** データ型は、将来の [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]バージョンで削除される予定です。 新しい開発作業ではこれらのデータ型の使用を避け、現在このデータ型を使用しているアプリケーションは変更を検討してください。 代わりに、 **nvarchar(max)** 、 **varchar(max)** 、 **varbinary(max)** を使用してください。  
  
## <a name="native-file-storage-types"></a>ネイティブのファイル ストレージ型  
 各ネイティブのファイル ストレージ型は、対応するホスト ファイル データ型として、フォーマット ファイルに記録されます。  
  
|ファイル ストレージ型|ホスト ファイル データ型|  
|-----------------------|-------------------------|  
|**char**|SQLCHAR|  
|**varchar**|SQLCHAR|  
|**nchar**|SQLNCHAR|  
|**nvarchar**|SQLNCHAR|  
|**text**|SQLCHAR|  
|**ntext**|SQLNCHAR|  
|**[バイナリ]**|SQLBINARY|  
|**varbinary**|SQLBINARY|  
|**image**|SQLBINARY|  
|**datetime**|SQLDATETIME|  
|**smalldatetime**|SQLDATETIM4|  
|**decimal**|SQLDECIMAL|  
|**numeric**|SQLNUMERIC|  
|**float**|SQLFLT8|  
|**real**|SQLFLT4|  
|**int**|SQLINT|  
|**bigint**|SQLBIGINT|  
|**smallint**|SQLSMALLINT|  
|**tinyint**|SQLTINYINT|  
|**money**|SQLMONEY|  
|**smallmoney**|SQLMONEY4|  
|**bit**|SQLBIT|  
|**uniqueidentifier**|SQLUNIQUEID|  
|**sql_variant**|SQLVARIANT|  
|**timestamp**|SQLBINARY|  
|UDT (ユーザー定義データ型)|SQLUDT|  
  
 \*文字形式で格納されたデータ ファイルでは、ファイル ストレージ型として **char** が使用されます。 したがって、文字データ ファイルの場合、フォーマット ファイルに表示されるデータ型は SQLCHAR のみです。  
  
 \*\*DEFAULT 値が指定されている **text** 列、 **ntext** 列、および **image** 列にデータを一括インポートすることはできません。  
  
## <a name="additional-considerations-for-file-storage-types"></a>ファイル ストレージ型のその他の考慮事項  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスからデータ ファイルにデータを一括エクスポートするときは、次のことを考慮してください。  
  
-   **char** 型は、常にファイル ストレージ型として指定できます。  
  
-   無効な暗黙的な型変換を表すファイル ストレージ型を入力すると、 **bcp** は失敗します。たとえば、 **int** データに **smallint** を指定することはできますが、 **smallint** データに **int** を指定すると、結果としてオーバーフロー エラーが発生します。  
  
-   **float**、 **money**、 **datetime**、または **int** などの非文字データ型をそれぞれのデータベース型として格納すると、データが [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のネイティブ形式でデータ ファイルに書き込まれます。  
  
    > [!NOTE]  
    >  **bcp** コマンドですべてのフィールドを対話形式で指定すると、各フィールドへの応答を XML 形式以外のファイルに保存するように要求するプロンプトが表示されます。 XML 以外のフォーマット ファイルの詳細については、「[XML 以外のフォーマット ファイル &#40;SQL Server&#41;](../../relational-databases/import-export/non-xml-format-files-sql-server.md)」を参照してください。  
  
## <a name="see-also"></a>参照  
 [bcp ユーティリティ](../../tools/bcp-utility.md)   
 [データ型 &#40;Transact-SQL&#41;](../../t-sql/data-types/data-types-transact-sql.md)   
 [bcp を使用したフィールド長の指定 &#40;SQL Server&#41;](../../relational-databases/import-export/specify-field-length-by-using-bcp-sql-server.md)   
 [フィールド ターミネータと行ターミネータの指定 &#40;SQL Server&#41;](../../relational-databases/import-export/specify-field-and-row-terminators-sql-server.md)   
 [bcp を使用したデータ ファイルのプレフィックス長の指定 &#40;SQL Server&#41;](../../relational-databases/import-export/specify-prefix-length-in-data-files-by-using-bcp-sql-server.md)  
  
  
