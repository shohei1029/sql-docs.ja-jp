---
description: Constants (Transact-SQL)
title: Constants (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 09/09/2020
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
dev_langs:
- TSQL
helpviewer_keywords:
- uniqueidentifier data type
- bit data type
- constants [SQL Server]
- scalar values
- money data type, constants
- binary [SQL Server], constants
- float data type
- Unicode [SQL Server], constants
- constants [Transact-SQL]
- integer constants
- decimal data type, constants
- character strings [SQL Server], constants
- positive values [SQL Server]
- formats [SQL Server], constants
- datetime data type [SQL Server]
- literals [SQL Server], constants
- real data type
- negative values
ms.assetid: 58ae3ff3-b1d5-41b2-9a2f-fc7ab8c83e0e
author: MikeRayMSFT
ms.author: mikeray
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 2b7094f1da16b03fd5409c926dd1b3f9fc173d22
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104747742"
---
# <a name="constants-transact-sql"></a>Constants (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

リテラル値またはスカラー値としても知られる定数は、特定のデータ値を表す記号です。 定数の形式は、その定数が表す値のデータ型に依存します。
  
## <a name="character-string-constants"></a>文字列定数
文字列定数では、英数字 (a ～ z、A ～ Z、および 0 ～ 9)、感嘆符 (!)、アット マーク (@)、および番号記号 (#) などの特殊文字を単一引用符で囲みます。 文字列定数には、現在のデータベースの既定の照合順序が指定されます。 COLLATE 句が使用されている場合、COLLATE 句によって指定された照合順序への変換の前に、データベースの既定のコード ページへの変換が引き続き行われます。 ユーザーが入力する文字列は、コンピューターのコード ページから評価され、必要に応じてデータベースの既定のコード ページに変換されます。

> [!NOTE]
> COLLATE 句を使用して [UTF8 対応照合順序](../../relational-databases/collations/collation-and-unicode-support.md#utf8)を指定した場合、COLLATE 句によって指定された照合順序への変換の前に、データベースの既定のコード ページへの変換が引き続き行われます。 指定された Unicode 対応の照合順序に直接変換することはできません。 詳細については、「[Unicode 文字列](#unicode-strings)」をご覧ください。
  
接続に対して QUOTED_IDENTIFIER オプションが OFF に設定されている場合は、文字列を二重引用符で囲むこともできます。ただし、Microsoft [OLE DB Driver for SQL Server](../../connect/oledb/oledb-driver-for-sql-server.md) および [ODBC Driver for SQL Server](../../connect/odbc/download-odbc-driver-for-sql-server.md) では、自動的に `SET QUOTED_IDENTIFIER ON` が使用されます。 単一引用符を使用することをお勧めします。
  
単一引用符で囲まれた文字列に単一引用符を埋め込む場合は、単一引用符を 2 つ続けて並べることで 1 つの単一引用符を表します。 これは、二重引用符で囲む文字列では必要ありません。
  
文字列の例は次のとおりです。
  
```
'Cincinnati'  
'O''Brien'  
'Process X is 50% complete.'  
'The level for job_id: %d should be between %d and %d.'  
"O'Brien"  
```  
  
空文字列は、2 つの単一引用符の間に何も挿入しないで表します。 6.x 互換性モードでは、空文字列は 1 つのスペースと見なされます。
  
文字列は、拡張[照合順序](../../relational-databases/collations/collation-and-unicode-support.md)をサポートします。
  
> [!NOTE]  
> 8,000 バイト以上の文字列定数は **varchar(max)** データ型に分類されます。  
  
## <a name="unicode-strings"></a>Unicode 文字列
Unicode 文字列の形式は文字列に似ていますが、先頭に N 識別子が付きます (N は、SQL-92 標準で National Language を表します)。 

> [!IMPORTANT]  
> プレフィックス N は大文字にする必要があります。 

たとえば、`'Michél'` は文字定数であり、`N'Michél'` は Unicode 定数です。 Unicode 定数は Unicode データとして解釈され、コード ページを使用して評価されることはありません。 Unicode 定数は照合順序を持ちます。 この照合順序では主に比較と大文字小文字の区別が制御されます。 Unicode 定数には、現在のデータベースの既定の照合順序が指定されます。 COLLATE 句が使用されている場合、COLLATE 句によって指定された照合順序への変換の前に、データベースの既定の照合順序への変換が引き続き行われます。 詳細については、「 [Collation and Unicode Support](../../relational-databases/collations/collation-and-unicode-support.md#storage_differences)」を参照してください。
  
Unicode 文字列定数は、拡張照合順序をサポートします。
  
> [!NOTE]  
> 8,000 バイト以上の Unicode 文字列定数は **nvarchar(max)** データ型に分類されます。  
  
## <a name="binary-constants"></a>バイナリ定数
binary 型定数は 16 進数の文字列であり、`0x` というプレフィックスが付きます。 引用符では囲みません。
  
バイナリ文字列の例は次のとおりです。
  
```
0xAE  
0x12Ef  
0x69048AEFDD010E  
0x  (empty binary string)  
```  
  
> [!NOTE]  
>  8,000 バイト以上の binary 型文字列は **varbinary(max)** データ型に分類されます。  
  
## <a name="bit-constants"></a>bit 型定数
**bit** 型定数は数値の 0 または 1 で表します。引用符では囲みません。 1 より大きい数値が使用される場合は、1 に変換されます。
  
## <a name="datetime-constants"></a>datetime 型定数
**datetime** 型定数は、特定の形式の日付文字値で表し、単一引用符で囲みます。
  
例を次に **datetime** 定数。
  
```
'December 5, 1985'  
'5 December, 1985'  
'851205'  
'12/5/98'  
```  
  
次に datetime 型定数の例を示します。
  
```
'14:30:24'  
'04:24 PM'  
```  
  
## <a name="integer-constants"></a>整数定数
**integer** 型定数は数値文字列で表し、引用符では囲みません。また、小数点は含みません。 **integer** 定数は整数である必要があります。 10 進数に含まれることはできません。
  
例を次に **integer** 定数。
  
```
1894  
2  
```  
  
## <a name="decimal-constants"></a>decimal 型定数
**decimal** 型定数は、小数点を含む数値文字列で表し、引用符では囲みません。
  
例を次に **decimal** 定数。
  
```
1894.1204  
2.0  
```  
  
## <a name="float-and-real-constants"></a>float 型と real 型定数
**float** および **real** 型定数は科学的表記法で表します。
  
次に **float** または **real** 値の例を示します。
  
```
101.5E5  
0.5E-2  
```  
  
## <a name="money-constants"></a>money 型定数
**money** 型定数は数値文字列で表し、オプションで小数点および通貨記号をプレフィックスとして付加することができます。 **money** 型定数は、引用符では囲みません。
  
[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、金額を表す文字列の 3 桁ごとにコンマ (,) を挿入するなどのグループ化ルールを設定することはできません。
  
> [!NOTE]  
>  コンマは、指定した任意の場所無視 **money** リテラルです。  
  
例を次に **money** 定数。
  
```
$12  
$542023.14  
```  
  
## <a name="uniqueidentifier-constants"></a>uniqueidentifier 型定数
**uniqueidentifier** 型定数は、GUID 値を表す文字列です。 文字型または binary 型文字列の形式で指定できます。
  
次の例は、両方とも同じ GUID を指定しています。
  
```
'6F9619FF-8B86-D011-B42D-00C04FC964FF'  
0xff19966f868b11d0b42d00c04fc964ff  
```  
  
## <a name="specifying-negative-and-positive-numbers"></a>負の数値と正の数値の指定  
数値が正であるか負であるかを示すには、数値型定数に **+** または **-** 単項演算子を付加します。 これにより、符号付き数値を表す数値式が作成されます。 **+** または **-** 単項演算子が付加されない場合、正の値になります。
  
署名 **integer** 式。  
  
```
+145345234
-2147483648
```
署名 **decimal** 式。  
  
```
+145345234.2234
-2147483648.10
```
  
署名 **float** 式。  
  
```
+123E-3
-12E5
```
  
署名 **money** 式。  
  
```
-$45.56
+$423456.99
```
  
## <a name="enhanced-collations"></a>拡張照合順序  
[!INCLUDE[ssde_md](../../includes/ssde_md.md)] は、拡張照合順序をサポートする文字および Unicode 文字列定数をサポートしています。 詳細については、[[COLLATE &#40;Transact-SQL&#41;](../../t-sql/statements/collations.md) 句をご確認ください。
  
## <a name="see-also"></a>関連項目
[データ型 &#40;Transact-SQL&#41;](../../t-sql/data-types/data-types-transact-sql.md)  
[式 &#40;Transact-SQL&#41;](../../t-sql/language-elements/expressions-transact-sql.md)  
[演算子 &#40;Transact-SQL&#41;](../../t-sql/language-elements/operators-transact-sql.md)
[照合順序と Unicode のサポート](../../relational-databases/collations/collation-and-unicode-support.md)  
[照合順序の優先順位](../../t-sql/statements/collation-precedence-transact-sql.md)    
  
