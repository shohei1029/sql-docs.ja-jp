---
description: nchar および nvarchar (Transact-SQL)
title: nchar と nvarchar (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 07/19/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
dev_langs:
- TSQL
helpviewer_keywords:
- nvarchar data type
- nchar data type
ms.assetid: 81ee5637-ee31-4c4d-96d0-56c26a742354
author: MikeRayMSFT
ms.author: mikeray
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 11205430c39bf127d7875e820158ea9bfe7cfbe2
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104747762"
---
# <a name="nchar-and-nvarchar-transact-sql"></a>nchar および nvarchar (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

固定サイズ (**nchar**)、または可変サイズ (**nvarchar**) の文字データ型です。 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降、[補助文字 (SC)](../../relational-databases/collations/collation-and-unicode-support.md#Supplementary_Characters) が有効になっている照合順序を使用する場合、これらのデータ型には [Unicode](../../relational-databases/collations/collation-and-unicode-support.md#Unicode_Defn) 文字データの全範囲が格納され、[UTF-16](https://www.wikipedia.org/wiki/UTF-16) 文字エンコードが使用されます。 SC が無効の照合順序を指定する場合、これらのデータ型には [UCS-2](https://www.wikipedia.org/wiki/Universal_Coded_Character_Set#Encoding_forms) 文字エンコードでサポートされている文字データのサブセットのみが格納されます。

## <a name="arguments"></a>引数
**nchar** [ ( n ) ]  
固定サイズの文字列データです。 *n* によってバイト ペアでの文字列のサイズが定義されます。1 から 4,000 までの値にする必要があります。 ストレージのサイズは、*n* の 2 倍のバイト数です。 [UCS-2](https://www.wikipedia.org/wiki/UTF-16#U+0000_to_U+D7FF_and_U+E000_to_U+FFFF) エンコードの場合、ストレージのサイズは *n* の 2 倍のバイト数となり、格納できる文字数もまた *n* となります。 UTF-16 エンコードの場合、ストレージのサイズは引き続き *n* の 2 倍のバイト数ですが、補助文字によって 2 つのバイト ペア (または [サロゲート ペア](https://www.wikipedia.org/wiki/UTF-16#U+010000_to_U+10FFFF)) が使用されるため、格納できる文字数は *n* よりも少なくなる場合があります。 **nchar** の ISO シノニムは、**national char** および **national character** です。
  
**nvarchar** [ ( n | **max** ) ]  
可変サイズの文字列データです。 *n* によってバイト ペアでの文字列のサイズが定義されます。1 から 4,000 までの値を指定できます。 **max** は、ストレージの最大サイズが 2^30-1 文字 (2 GB) であることを示します。 ストレージのサイズは、*n* の 2 倍のバイト数 + 2 バイトです。 [UCS-2](https://www.wikipedia.org/wiki/UTF-16#U+0000_to_U+D7FF_and_U+E000_to_U+FFFF) エンコードの場合、ストレージのサイズは *n* の 2 倍のバイト数 + 2 バイトとなり、格納できる文字数もまた *n* となります。 UTF-16 エンコードの場合、ストレージのサイズは引き続き *n* の 2 倍のバイト数 + 2 バイトですが、補助文字によって 2 つのバイト ペア (または [サロゲート ペア](https://www.wikipedia.org/wiki/UTF-16#U+010000_to_U+10FFFF)) が使用されるため、格納できる文字数は *n* よりも少なくなる場合があります。 ISO シノニム **nvarchar** は national char のさまざまな **と** 各国語文字がさまざまな **です**。
  
## <a name="remarks"></a>注釈  
一般的な誤解として、[NCHAR(*n*) および NVARCHAR(*n*)](../../t-sql/data-types/nchar-and-nvarchar-transact-sql.md) では *n* によって文字数が定義されると考えられています。 実際には、[NCHAR(*n*) および NVARCHAR(*n*)](../../t-sql/data-types/nchar-and-nvarchar-transact-sql.md) では、*n* によって文字長が **バイトペア** (0-4,000) で定義されます。 *n* は、格納できる文字数を定義しません。 これは、[CHAR(*n*) および VARCHAR(*n*)](../../t-sql/data-types/char-and-varchar-transact-sql.md) の定義と同様です。   
この誤解が生じるのは、Unicode の範囲 0-65,535 で定義された文字を使用すると、各バイトペアにつき 1 つの文字を格納できるためです。 しかしながら、より高い Unicode の範囲 (65,536-1,114,111) の場合、1 つの文字で 2 つのバイトペアが使用されることがあります。 たとえば、NCHAR(10) として定義された列では、[!INCLUDE[ssde_md](../../includes/ssde_md.md)]で格納できる文字は、1 つのバイトペア (Unicode 範囲 0-65535) を使用する文字は 10 個ですが、2 つのバイトペア (Unicode 範囲 65,536-1,114,111) を使用する場合は 10 個未満です。 Unicode の格納と文字の範囲の詳細については、「[UTF-8 と UTF-16 でのストレージの相違点](../../relational-databases/collations/collation-and-unicode-support.md#storage_differences)」を参照してください。     

データ定義または変数宣言ステートメントで *n* を指定しないと、既定の長さは 1 になります。 CAST 関数で *n* を指定しないと、既定の長さは 30 になります。

**nchar** または **nvarchar** を使用する場合は、次のことをお勧めします。
- 列データ エントリのサイズが一定の場合は、**nchar** を使用します。  
- 列データ エントリのサイズが大幅に変化する場合は、**nvarchar** を使用します。  
- 列データ エントリのサイズが大幅に変化し、かつ文字列の長さが 4,000 バイト ペアを超える可能性がある場合は、**nvarchar(max)** を使用します。  
  
**sysname** と機能的に等価であるシステム提供のユーザー定義データ型は、 **nvarchar (128)**, が許容されない点が異なります。 **sysname** データベース オブジェクト名を参照するために使用します。
  
使用するオブジェクト **nchar** または **nvarchar** COLLATE 句を使用して、特定の照合順序が割り当てられていない限り、データベースの既定の照合順序は割り当てられます。
  
SET ANSI_PADDING が ON にでは常に **nchar** と **nvarchar** です。 SET ANSI_PADDING OFF には適用されませんが、 **nchar** または **nvarchar** データ型。
  
Unicode 文字の文字列定数には、プレフィックスとして文字 N を付けて UCS-2 または UTF-16 の入力を通知します。これは SC 照合順序が使用されているか使用されていないかによって異なります。 プレフィックス N がない場合、文字列はデータベースの既定のコード ページに変換され、特定の文字が認識されない場合があります。 [!INCLUDE[sql-server-2019](../../includes/sssql19-md.md)] 以降、UTF-8 が有効になっている照合順序を使用する場合、既定のコード ページでは UNICODE UTF-8 文字セットを格納させることができます。 
 
> [!NOTE]  
> 文字列定数にプレフィックスとして文字 N を付けたときに、変換する定数の長さが nvarchar 文字列データ型の最大長 (4,000) を超えない場合、暗黙的な変換によって UCS-2 または UTF-16 の文字列が生成されます。 それ以外の場合、暗黙的な変換では大きな値の nvarchar(max) が生成されます。
  
> [!WARNING]  
> Null 以外の **varchar(max)** または **nvarchar(max)** の各列には、24 バイトの追加の固定割り当てが必要です。これは並べ替え操作中の 8,060 バイトの行制限におけるカウント対象となります。 これらの追加バイトにより、テーブル内の Null 以外の **varchar(max)** または **nvarchar(max)** の列数について、暗黙的な制限が生じます。 テーブルの作成時やデータ挿入時に、最大行サイズが許容最大値の 8,060 バイトを超えるという通常の警告以外の、特別なエラーは提供されません。 この大きな行サイズにより、一部の通常操作の間に、ユーザーが予期しないエラー (エラー 512 など) が発生することがあります。  このような操作の 2 つの例は、クラスター化インデックス キーの更新と、列セット全体の並べ替えです。
  
## <a name="converting-character-data"></a>文字データの変換  
文字データを変換する方法の詳細については、 [char と varchar &#40;Transact-SQL&#41;](../../t-sql/data-types/char-and-varchar-transact-sql.md) を参照してください。
  
## <a name="see-also"></a>関連項目
[ALTER TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-table-transact-sql.md)  
[CAST および CONVERT &#40;Transact-SQL&#41;](../../t-sql/functions/cast-and-convert-transact-sql.md)  
[COLLATE &#40;Transact-SQL&#41;](../statements/collations.md)  
[CREATE TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/create-table-transact-sql.md)  
[データ型 &#40;Transact-SQL&#41;](../../t-sql/data-types/data-types-transact-sql.md)  
[DECLARE @local_variable &#40;Transact-SQL&#41;](../../t-sql/language-elements/declare-local-variable-transact-sql.md)  
[LIKE &#40;Transact-SQL&#41;](../../t-sql/language-elements/like-transact-sql.md)  
[SET ANSI_PADDING &#40;Transact-SQL&#41;](../../t-sql/statements/set-ansi-padding-transact-sql.md)  
[SET @local_variable &#40;Transact-SQL&#41;](../../t-sql/language-elements/set-local-variable-transact-sql.md)    
[照合順序と Unicode のサポート](../../relational-databases/collations/collation-and-unicode-support.md)     
[1 バイト文字セットとマルチバイト文字セット](/cpp/c-runtime-library/single-byte-and-multibyte-character-sets)  
