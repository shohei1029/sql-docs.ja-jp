---
title: フィールド ターミネータと行ターミネータの指定 (SQL Server) | Microsoft Docs
description: フィールド ターミネータと行ターミネータは、フィールドや行の終了位置と次のフィールドや行の開始位置を、データ ファイルを読み取るプログラムに示します。
ms.custom: ''
ms.date: 07/26/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: data-movement
ms.topic: conceptual
helpviewer_keywords:
- bcp utility [SQL Server], terminators
- field terminators [SQL Server]
- data formats [SQL Server], terminators
- row terminators [SQL Server]
- terminators [SQL Server]
ms.assetid: f68b6782-f386-4947-93c4-e89110800704
author: MashaMSFT
ms.author: mathoma
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 3208393d25df19d980da6b66c78dc3711f428ecd
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104752282"
---
# <a name="specify-field-and-row-terminators-sql-server"></a>フィールド ターミネータと行ターミネータの指定 (SQL Server)
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]
  文字列データ フィールドでは、省略可能なターミネータ文字を使用して、データ ファイルの各フィールドの末尾 ( *フィールド ターミネータ* を使用) と各行の末尾 ( *行ターミネータ* を使用) を示すことができます。 ターミネータ文字は、フィールドや行の終了位置と次のフィールドや行の開始位置を、データ ファイルを読み取るプログラムに示す方法の 1 つです。  
  
> [!IMPORTANT]
>  ネイティブ形式または Unicode ネイティブ形式を使用するときは、フィールド ターミネータではなくプレフィックス長を使用します。 ネイティブ形式のデータ ファイルは [!INCLUDE[msCoName](../../includes/msconame-md.md)][!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の内部バイナリ データ形式で格納されるので、ネイティブ形式のデータがターミネータと競合することがあります。  
  
## <a name="characters-supported-as-terminators"></a>ターミネータとしてサポートされる文字  
 **bcp** コマンド、BULK INSERT ステートメント、および OPENROWSET 一括行セット プロバイダーでは、フィールド ターミネータまたは行ターミネータとしてさまざまな文字がサポートされます。また、常に、各ターミネータの最初のインスタンスが検索されます。 ターミネータ用にサポートされる文字を次の表に示します。  
  
|ターミネータ文字|指定方法|  
|---------------------------|------------------|  
|タブ|\t<br /><br /> 既定のフィールド ターミネータです。|  
|改行文字|\n<br /><br /> 既定の行ターミネータです。|  
|キャリッジ リターン/ライン フィード|\r|  
|円記号*|\\\|  
|NULL ターミネータ (表示されないターミネータ)**|\0|  
|任意の印刷可能な文字 (NULL、タブ、改行、およびキャリッジ リターンを除き、制御文字は印刷可能ではありません)|(*、A、t、l など)|  
|上に列挙したターミネータ文字の一部または全部を含む 10 文字までの印刷可能な文字列|(**\t\*\*、end、!!!!!!!!!!、\t-\n など)|  
  
 *円記号のエスケープ文字と共に制御文字として機能するのは、t、n、r、0 および '\0' だけです。  
  
 **印刷時には NULL 制御文字 (\0) が表示されませんが、データ ファイルでは 1 つの文字です。 つまり、フィールド ターミネータまたは行ターミネータとして NULL 制御文字を使用することと、フィールド ターミネータまたは行ターミネータをまったく使用しないことは異なります。  
  
> [!IMPORTANT]  
>  データ内にターミネータ文字が出現すると、データではなく、ターミネータとして解釈されます。その文字に続くデータは、次のフィールドまたは次のレコードに属すると解釈されます。 したがって、ターミネータがデータに出現することがないように、注意深くターミネータを選択してください。 たとえば、データに下位サロゲートが含まれている場合、フィールド ターミネータには下位サロゲート フィールド ターミネータは適していません。  
  
## <a name="using-row-terminators"></a>行ターミネータの使用  
 行ターミネータと最後のフィールドのターミネータを兼用できます。 ただし、通常は、行ターミネータを別に指定する方が便利です。 たとえば、表形式の出力を生成するには、各行の最後のフィールドを改行文字 (\n) で終了し、他のすべてのフィールドをタブ文字 (\t) で終了します。 各データ レコードをデータ ファイル内の独自の行に配置するには、行ターミネータに \r\n の組み合わせを指定します。  
  
> [!NOTE]  
>  **bcp** を対話的に使用し、\n (改行) を行ターミネータとして指定すると、 **bcp** によって自動的に \r (キャリッジ リターン) 文字が前に付加され、結果的には行ターミネータが \r\n になります。  
  
## <a name="specifying-terminators-for-bulk-export"></a>一括エクスポートのターミネータの指定  
 **char** データまたは **nchar** データを一括エクスポートするときに、既定のターミネータ以外を使用する場合、 **bcp** コマンドに対してターミネータを指定する必要があります。 次のいずれかの方法で、ターミネータを指定できます。  
  
-   フォーマット ファイルで、フィールドごとにターミネータを指定します。  
  
    > [!NOTE]  
    >  フォーマット ファイルの使用方法の詳細は、「[データのインポートまたはエクスポート用のフォーマット ファイル &#40;SQL Server&#41;](../../relational-databases/import-export/format-files-for-importing-or-exporting-data-sql-server.md)」を参照してください。  
  
-   フォーマット ファイルを使用しない場合には、次の方法があります。  
  
    -   **-t** スイッチを使用して、行内の最後のフィールドを除くすべてのフィールドのフィールド ターミネータを指定します。さらに、 **-r** スイッチを使用して、行ターミネータを指定します。  
  
    -   **-t** スイッチを指定しないで、文字形式のスイッチ ( **-c** または **-w** ) を使用すると、フィールド ターミネータがタブ文字 \t に設定されます。 これは、 **-t**\t を指定することと同じです。  
  
        > [!NOTE]  
        >  **-n** (ネイティブ データ) スイッチまたは **-N** (Unicode ネイティブ) スイッチを指定すると、ターミネータは挿入されません。  
  
    -   対話的な **bcp** コマンドに、 **in** オプションまたは **out** オプションが含まれていて、フォーマット ファイル スイッチ ( **-f**) またはデータ形式スイッチ ( **-n**、 **-c**、 **-w**、または **-N**) のいずれも含まれていない場合に、プレフィックス長とフィールド長の指定をしないと、各フィールドのフィールド ターミネータが要求されます。既定ではターミネータは "なし" になっています。  
  
         `Enter field terminator [none]:`  
  
         通常は、既定値を選択することをお勧めします。 ただし、 **char** データ フィールドまたは **nchar** データ フィールドについては、次の「ターミネータ使用のガイドライン」を参照してください。 コンテキスト内でこのプロンプトが表示される例については、「[bcp を使用した互換性のためのデータ形式の指定 &#40;SQL Server&#41;](../../relational-databases/import-export/specify-data-formats-for-compatibility-when-using-bcp-sql-server.md)」を参照してください。  
  
        > [!NOTE]  
        >  **bcp** コマンドですべてのフィールドを対話形式で指定すると、各フィールドへの応答を XML 形式以外のファイルに保存するように要求するプロンプトが表示されます。 XML 以外のフォーマット ファイルの詳細については、「[XML 以外のフォーマット ファイル &#40;SQL Server&#41;](../../relational-databases/import-export/non-xml-format-files-sql-server.md)」を参照してください。  
  
### <a name="guidelines-for-using-terminators"></a>ターミネータ使用のガイドライン  
 状況によっては、 **char** データ フィールドまたは **nchar** データ フィールドには、ターミネータが役に立つ場合があります。 次に例を示します。  
  
-   プレフィックス長がわからないプログラムにインポートされるデータ ファイル内で NULL 値が含まれるデータ列。  
  
     NULL 値が含まれているすべてのデータ列は、可変長と見なされます。 プレフィックス長がない場合、ターミネータは、データが正しく解釈されるように NULL 値の末尾を識別する必要があります。  
  
-   多くの列によって領域が部分的にのみ使用されている、長い固定長の列。  
  
     この状況では、ターミネータを指定することで記憶領域が最小限になり、フィールドが可変長として処理されます。  

### <a name="specifying-n-as-a-row-terminator-for-bulk-export"></a>`\n` を一括エクスポートの行ターミネータとして指定する

`\n` を一括エクスポートの行ターミネータとして指定するか、既定の行ターミネータを暗黙的に使用する場合、bcp では復帰と改行の組み合わせ (CRLF) が行ターミネータとして出力されます。 行ターミネータとして改行文字のみ (LF) を出力する場合 (Unix および Linux コンピュータの標準)、16 進数表記を使用して LF 行ターミネータを指定します。 次に例を示します。

```cmd
bcp -r '0x0A'
```

### <a name="examples"></a>例  
 この例では、フィールド ターミネータにコンマ、行ターミネータに改行文字 (\n) を使用して、文字形式で `AdventureWorks.HumanResources.Department` テーブルから `Department-c-t.txt` データ ファイルにデータが一括エクスポートされます。  
  
 **bcp** コマンドには、次のスイッチがあります。  
  
|Switch|説明|  
|------------|-----------------|  
|**-c**|データ フィールドが文字データとして読み込まれることを指定します。|  
|**-t** `,`|コンマ (,) をフィールド ターミネータとして指定します。|  
|**-r** \n|改行文字を行ターミネータとして指定します。 改行文字は既定の行ターミネータなので省略できます。|  
|**-T**|**bcp** ユーティリティが統合セキュリティを使用した信頼関係接続を使用して [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] に接続することを指定します。 **-T** を指定しない場合、正常にログインするには **-U** と **-P** を指定する必要があります。|  
  
 詳細については、「 [bcp Utility](../../tools/bcp-utility.md)」を参照してください。  
  
 [!INCLUDE[msCoName](../../includes/msconame-md.md)] Windows コマンド プロンプトで、次のように入力します。  
  
```cmd
bcp AdventureWorks.HumanResources.Department out C:\myDepartment-c-t.txt -c -t, -r \n -T  
```  
  
 このコマンドにより、それぞれ 4 つのフィールドを持つ 16 個のレコードが含まれる `Department-c-t.txt`が作成されます。 各フィールドはコンマで区切られています。  
  
## <a name="specifying-terminators-for-bulk-import"></a>一括インポートのターミネータの指定  
 **char** データまたは **nchar** データを一括インポートするときに、一括インポート コマンドではデータ ファイルで使用されているターミネータが認識される必要があります。 次のように、ターミネータの指定方法は一括インポート コマンドによって異なります。  
  
-   **bcp**  
  
     インポート操作のターミネータの指定には、エクスポート操作と同じ構文を使用します。 詳細については、このトピックの「一括エクスポートのターミネータの指定」を参照してください。  
  
-   BULK INSERT  
  
     次の表に示す修飾子を使用して、フォーマット ファイル内の個別のフィールドまたはデータ ファイル全体にターミネータを指定できます。  
  
    |Qualifier|説明|  
    |---------------|-----------------|  
    |FIELDTERMINATOR **='** _field_terminator_*_'_*|文字データ ファイルや Unicode 文字データ ファイルに使用されるフィールド ターミネータを指定します。<br /><br /> 既定値は \t (タブ文字) です。|  
    |ROWTERMINATOR **='** _row_terminator_*_'_*|文字データ ファイルや Unicode 文字データ ファイルに使用される行ターミネータを指定します。<br /><br /> 既定値は \n (改行記号) です。|  
  
     詳細については、「[BULK INSERT &#40;Transact-SQL&#41;](../../t-sql/statements/bulk-insert-transact-sql.md)」を参照してください。  
  
-   INSERT ...SELECT * FROM OPENROWSET(BULK...)  
  
     OPENROWSET 一括行セット プロバイダーでは、ターミネータを指定できるのはフォーマット ファイルのみです (Large Object データ型を除き、これは必須です)。 文字データ ファイルで既定以外のターミネータが使用されている場合、フォーマット ファイルで定義する必要があります。 詳細については、「[フォーマット ファイルの作成 &#40;SQL Server&#41;](../../relational-databases/import-export/create-a-format-file-sql-server.md) および [データの一括インポートでのフォーマット ファイルの使用 &#40;SQL Server&#41;](../../relational-databases/import-export/use-a-format-file-to-bulk-import-data-sql-server.md)」を参照してください。  
  
     OPENROWSET BULK 句の詳細については、「 [OPENROWSET &#40;Transact-SQL&#41;](../../t-sql/functions/openrowset-transact-sql.md)を使用) を示すことができます。  

### <a name="specifying-n-as-a-row-terminator-for-bulk-import"></a>`\n` を一括インポートの行ターミネータとして指定する
`\n` を一括インポートの行ターミネータとして指定するか、既定の行ターミネータを暗黙的に使用する場合、bcp および BULK INSERT ステートメントでは、復帰と改行の組み合わせ (CRLF) が行ターミネータとして想定されます。 ソース ファイルで改行文字のみ (LF) が行ターミネータとして使用されている場合 (Unix および Linux コンピュータで生成されたファイルの標準)、16 進数表記を使用して LF 行ターミネータを指定します。 BULK INSERT ステートメントの例を次に示します。

```sql
    ROWTERMINATOR = '0x0A'
```
 
### <a name="examples"></a>例  
 この例では、前述の例で作成された `Department-c-t.txt` データ ファイルから、 `myDepartment` サンプル データベースの [!INCLUDE[ssSampleDBUserInputNonLocal](../../includes/sssampledbuserinputnonlocal-md.md)] テーブルに、文字データを一括インポートします。 このテーブルを作成しないと、例を実行できません。 **dbo** スキーマでこのテーブルを作成するには、 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] のクエリ エディターで次のコードを実行します。  
  
```sql  
USE AdventureWorks;  
GO  
DROP TABLE myDepartment;  
CREATE TABLE myDepartment   
(DepartmentID smallint,  
Name nvarchar(50),  
GroupName nvarchar(50) NULL,  
ModifiedDate datetime not NULL CONSTRAINT DF_AddressType_ModifiedDate DEFAULT (GETDATE())  
);  
GO 
```  
  
#### <a name="a-using-bcp-to-interactively-specify-terminators"></a>A. bcp を使用した対話的なターミネータの指定  
 次の例では、 `Department-c-t.txt` コマンドを使用して、 `bcp` データ ファイルを一括インポートします。 このコマンドでは、一括エクスポート コマンドと同じコマンド スイッチを使用します。 詳細については、このトピックの「一括エクスポートのターミネータの指定」を参照してください。  
  
 Windows コマンド プロンプトで、次のように入力します。  
  
```cmd
bcp AdventureWorks..myDepartment in C:\myDepartment-c-t.txt -c -t , -r \n -T  
```  
  
#### <a name="b-using-bulk-insert-to-interactively-specify-terminators"></a>B. BULK INSERT を使用した対話的なターミネータの指定  
 次の例では、次の表に示す修飾子を指定した `Department-c-t.txt` ステートメントを使用して、 `BULK INSERT` データ ファイルを一括インポートします。  
  
|オプション|属性|  
|------------|---------------|  
|DATAFILETYPE **='** char **'**|データ フィールドが文字データとして読み込まれることを指定します。|  
|FIELDTERMINATOR **='** `,` **'**|コンマ (`,`) をフィールド ターミネータとして指定します。|  
|ROWTERMINATOR **='** `\n` **'**|改行文字を行ターミネータとして指定します。|  
  
 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] のクエリ エディターで、次のコードを実行します。  
  
```sql  
USE AdventureWorks;  
GO  
BULK INSERT myDepartment FROM 'C:\myDepartment-c-t.txt'  
   WITH (  
      DATAFILETYPE = 'char',  
      FIELDTERMINATOR = ',',  
      ROWTERMINATOR = '\n'  
);  
GO  
```  
  
## <a name="see-also"></a>参照  
 [bcp ユーティリティ](../../tools/bcp-utility.md)   
 [BULK INSERT &#40;Transact-SQL&#41;](../../t-sql/statements/bulk-insert-transact-sql.md)   
 [OPENROWSET &#40;Transact-SQL&#41;](../../t-sql/functions/openrowset-transact-sql.md)   
 [bcp を使用したフィールド長の指定 &#40;SQL Server&#41;](../../relational-databases/import-export/specify-field-length-by-using-bcp-sql-server.md)   
 [bcp を使用したデータ ファイルのプレフィックス長の指定 &#40;SQL Server&#41;](../../relational-databases/import-export/specify-prefix-length-in-data-files-by-using-bcp-sql-server.md)   
 [bcp を使用したファイル ストレージ型の指定 &#40;SQL Server&#41;](../../relational-databases/import-export/specify-file-storage-type-by-using-bcp-sql-server.md)  
  
  
