---
title: bcp を使用したフィールド長の指定 (SQL Server) | Microsoft Docs
description: SQL Server では、必要に応じて、bcp から、フィールド長、既定のフィールド長、char データを含むファイルのデータ ストレージに対するフィールド長の影響を確認するように促されます。
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: data-movement
ms.topic: conceptual
helpviewer_keywords:
- native data format [SQL Server]
- default field lengths
- field length [SQL Server]
- data formats [SQL Server], field length
- bcp utility [SQL Server], field length
ms.assetid: 240f33ca-ef4a-413a-a4de-831885cb505b
author: MashaMSFT
ms.author: mathoma
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 0a7b5f7e99bf8b50522af3c0429e664077629ddb
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104752332"
---
# <a name="specify-field-length-by-using-bcp-sql-server"></a>bcp を使用したフィールド長の指定 (SQL Server)
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]
  フィールド長は、文字形式でデータを表現するために必要な文字の最大数を示します。 データがネイティブ形式で格納されている場合、フィールド長は既にわかっています。たとえば、 **int** データ型では 4 バイトになります。 プレフィックス長に 0 を指定した場合、 **bcp** コマンドを実行すると、フィールド長、既定のフィールド長、 **char** データを含むデータ ファイル内のデータ ストレージに対するフィールド長の影響を確認するプロンプトが表示されます。  
  
## <a name="the-bcp-prompt-for-field-length"></a>フィールド長を要求する bcp プロンプト  
 対話型の **bcp** コマンドで、フォーマット ファイル スイッチ ( **-f**) またはデータ形式スイッチ ( **-n**、 **-c**、 **-w** または **-N**) のどちらも付けずに **in** または **out** オプションを指定すると、次のように各データ フィールドの長さを要求するプロンプトが表示されます。  
  
 `Enter length of field <field_name> [<default>]:`  
  
 コンテキスト内でこのプロンプトが表示される例については、「[bcp を使用した互換性のためのデータ形式の指定 &#40;SQL Server&#41;](../../relational-databases/import-export/specify-data-formats-for-compatibility-when-using-bcp-sql-server.md)」を参照してください。  
  
> [!NOTE]  
>  **bcp** コマンドですべてのフィールドを対話形式で指定すると、各フィールドへの応答を XML 形式以外のファイルに保存するように要求するプロンプトが表示されます。 XML 以外のフォーマット ファイルの詳細については、「[XML 以外のフォーマット ファイル &#40;SQL Server&#41;](../../relational-databases/import-export/non-xml-format-files-sql-server.md)」を参照してください。  
  
 **bcp** コマンドでフィールド長を要求するプロンプトが表示されるかどうかは、次のさまざまな要素によって決まります。  
  
-   固定長でないデータ型のコピー時にプレフィックス長を 0 に指定した場合、 **bcp** によってフィールド長を要求するプロンプトが表示されます。  
  
-   非文字データを文字データに変換するとき、 **bcp** によってデータの保存に十分な長さの既定フィールド長が提示されます。  
  
-   ファイル保存形式が非文字である場合、 **bcp** コマンドによってフィールド長を要求するプロンプトは表示されません。 データは、[!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のネイティブ データ表現 (ネイティブ形式) で格納されます。  
  
## <a name="using-default-field-lengths"></a>既定のフィールド長の使用  
 通常、 [!INCLUDE[msCoName](../../includes/msconame-md.md)] では、 **bcp** によって提示された既定値をフィールド長に使用することをお勧めします。 キャラクター モードのデータ ファイルが作成された場合、既定のフィールド長を使用することによって、データの切り捨てや数値オーバーフロー エラーの発生を防止できます。  
  
 不適切なフィールド長を指定すると、問題が発生する場合があります。 たとえば、数値データをコピーするときに、そのデータに対して短すぎるフィールド長を指定すると、 **bcp** ユーティリティによってオーバーフロー エラー メッセージが出力され、データはコピーされません。 また、 **datetime** データをエクスポートするときに、その文字列に対して 26 バイトよりも短いフィールド長を指定すると、 **bcp** ユーティリティによってデータが切り捨てられ、エラー メッセージも表示されません。  
  
> [!IMPORTANT]  
>  既定のサイズ オプションを使用すると、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] は文字列全体を読み取ります。 場合によっては、既定のフィールド長を使用すると、"予期しないファイルの終了" エラーが発生することもあります。 通常、このエラーは、予期されるフィールドの一部のみがデータ ファイル内にある場合に **money** データ型および **datetime** データ型で発生します。たとえば、 **mm** dd *yy*/*形式の*/*datetime* 値が時刻要素なしで指定された場合、 **char** 形式の **datetime** 値の長さは、予期される 24 文字よりも短くなります。 この種類のエラーを防止するには、フィールド ターミネータまたは固定長データ フィールドを使用するか、既定のフィールド長を別の値に変更します。  
  
### <a name="default-field-lengths-for-character-file-storage"></a>文字ファイル保存の既定のフィールド長  
 次の表は、文字ファイル保存形式として保存されるデータの既定のフィールド長を示しています。 NULL 値を使用できるデータの長さは、NULL 値を使用できないデータの長さと同じです。  
  
|データ型|既定の長さ (文字数)|  
|---------------|-----------------------------------|  
|**char**|列に対して定義された長さ|  
|**varchar**|列に対して定義された長さ|  
|**nchar**|列に対して定義された長さの 2 倍|  
|**nvarchar**|列に対して定義された長さの 2 倍|  
|**[テキスト]**|0|  
|**ntext**|0|  
|**bit**|1|  
|**[バイナリ]**|列に対して定義された長さの 2 倍 + 1|  
|**varbinary**|列に対して定義された長さの 2 倍 + 1|  
|**image**|0|  
|**datetime**|24|  
|**smalldatetime**|24|  
|**float**|30|  
|**real**|30|  
|**int**|12|  
|**bigint**|19|  
|**smallint**|7|  
|**tinyint**|5|  
|**money**|30|  
|**smallmoney**|30|  
|**decimal**|41*|  
|**numeric**|41*|  
|**uniqueidentifier**|37|  
|**timestamp**|17|  
|**varchar(max)**|0|  
|**varbinary(max)**|0|  
|**nvarchar(max)**|0|  
|UDT|ユーザー定義型 (UDT) 列の長さ|  
|XML|0|  
  
 \***decimal** データ型と **numeric** データ型について詳しくは、「[decimal 型と numeric 型 &#40;Transact-SQL&#41;](../../t-sql/data-types/decimal-and-numeric-transact-sql.md)」をご覧ください。  
  
> [!NOTE]  
>  **tinyint** 型の列には、0 ～ 255 の値を入力できます。この範囲の任意の数を表現するために必要な最大文字数は 3 です。これは、100 ～ 255 の値に相当します。  
  
### <a name="default-field-lengths-for-native-file-storage"></a>ネイティブ ファイル保存の既定のフィールド長  
 次の表は、ネイティブ ファイル保存形式として保存されるデータの既定のフィールド長を示しています。 NULL 値を使用できるデータの長さは、NULL 値を使用できないデータの長さと同じです。また、文字データは常に文字形式で保存されます。  
  
|データ型|既定の長さ (文字数)|  
|---------------|-----------------------------------|  
|**bit**|1|  
|**[バイナリ]**|列に対して定義された長さ|  
|**varbinary**|列に対して定義された長さ|  
|**image**|0|  
|**datetime**|8|  
|**smalldatetime**|4|  
|**float**|8|  
|**real**|4|  
|**int**|4|  
|**bigint**|8|  
|**smallint**|2|  
|**tinyint**|1|  
|**money**|8|  
|**smallmoney**|4|  
|**decimal**|*|  
|**numeric**|*|  
|**uniqueidentifier**|16|  
|**timestamp**|8|  
  
 \***decimal** データ型と **numeric** データ型について詳しくは、「[decimal 型と numeric 型 &#40;Transact-SQL&#41;](../../t-sql/data-types/decimal-and-numeric-transact-sql.md)」をご覧ください。  
  
 前述のすべての場合に、後で [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] に再読み込みするためにデータ ファイルを作成して、保存領域を最小限に抑えるには、既定のファイル保存形式と既定のフィールド長と共に、フィールド長プレフィックスを使用します。  
  
## <a name="see-also"></a>参照  
 [bcp ユーティリティ](../../tools/bcp-utility.md)   
 [データ型 &#40;Transact-SQL&#41;](../../t-sql/data-types/data-types-transact-sql.md)   
 [フィールド ターミネータと行ターミネータの指定 &#40;SQL Server&#41;](../../relational-databases/import-export/specify-field-and-row-terminators-sql-server.md)   
 [bcp を使用したデータ ファイルのプレフィックス長の指定 &#40;SQL Server&#41;](../../relational-databases/import-export/specify-prefix-length-in-data-files-by-using-bcp-sql-server.md)   
 [bcp を使用したファイル ストレージ型の指定 &#40;SQL Server&#41;](../../relational-databases/import-export/specify-file-storage-type-by-using-bcp-sql-server.md)   
 [一括インポート中の NULL の保持または既定値の使用 &#40;SQL Server&#41;](../../relational-databases/import-export/keep-nulls-or-use-default-values-during-bulk-import-sql-server.md)  
  
  
