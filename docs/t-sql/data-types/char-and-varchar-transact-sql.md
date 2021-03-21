---
description: char および varchar (Transact-SQL)
title: char および varchar (Transact-SQL)
ms.custom: ''
ms.date: 11/19/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- varchar
- varchar_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- fixed-length data types [SQL Server]
- character data type
- char data type
- varchar(max) data type
- variable-length data types [SQL Server]
- varchar data type
- utf8
ms.assetid: 282cd982-f4fb-4b22-b2df-9e8478f13f6a
author: MikeRayMSFT
ms.author: mikeray
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 20b23401d6f5d708dae34e20e3bfe1e202f7a7d9
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104735742"
---
# <a name="char-and-varchar-transact-sql"></a>char および varchar (Transact-SQL)

[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

固定サイズ (**char**)、または可変サイズ (**varchar**) の文字データ型です。 [!INCLUDE[sql-server-2019](../../includes/sssql19-md.md)] 以降、UTF-8 が有効になっている照合順序を使用する場合、これらのデータ型には [Unicode](../../relational-databases/collations/collation-and-unicode-support.md#Unicode_Defn) 文字データの全範囲が格納され、[UTF-8](https://www.wikipedia.org/wiki/UTF-8) 文字エンコードが使用されます。 UTF-8 が無効の照合順序を指定する場合、これらのデータ型には、対応するその照合順序のコード ページでサポートされている文字のサブセットのみが格納されます。

## <a name="arguments"></a>引数

**char** [ ( *n* ) ] 固定サイズの文字列データです。 *n* によってバイト単位での文字列のサイズが定義されます。1 から 8,000 までの値にする必要があります。 *Latin* などの 1 バイト エンコード文字セットの場合、ストレージのサイズは *n* バイトとなり、格納できる文字数もまた *n* となります。 マルチバイト エンコード文字セットの場合、ストレージのサイズは引き続き *n* バイトですが、格納できる文字数が *n* よりも少なくなる場合があります。 **char** の ISO シノニムは、**character** です。 文字セットについて詳しくは、「[1 バイト文字セットとマルチバイト文字セット](/cpp/c-runtime-library/single-byte-and-multibyte-character-sets)」をご覧ください。

**varchar** [ ( *n* | **max** ) ] 可変サイズの文字列データです。 *n* (1 から 8,000 の値を指定可能) を使用して文字列のサイズをバイト単位で定義するか、または **max** を使用して列の制約サイズが最大ストレージである 2^31-1 バイト (2 GB) までであることを指定します。 *Latin* などの 1 バイト エンコード文字セットの場合、ストレージのサイズは *n* バイト + 2 バイトとなり、格納できる文字数もまた *n* となります。 マルチバイト エンコード文字セットの場合、ストレージのサイズは引き続き *n* バイト + 2 バイトですが、格納できる文字数が *n* よりも少なくなる場合があります。 **varchar** の ISO シノニムは、**charvarying** または **charactervarying** です。 文字セットについて詳しくは、「[1 バイト文字セットとマルチバイト文字セット](/cpp/c-runtime-library/single-byte-and-multibyte-character-sets)」をご覧ください。

## <a name="remarks"></a>解説

一般的な誤解として、[CHAR(*n*) および VARCHAR(*n*)](../../t-sql/data-types/char-and-varchar-transact-sql.md) では *n* によって文字数が定義されると考えられています。 実際には、[CHAR(*n*) および VARCHAR(*n*)](../../t-sql/data-types/char-and-varchar-transact-sql.md) では、*n* によって文字長が **バイト** (0-8,000) で定義されます。 *n* は、格納できる文字数を定義しません。 これは、[NCHAR(*n*) および NVARCHAR(*n*)](../../t-sql/data-types/nchar-and-nvarchar-transact-sql.md) の定義と同様です。
この誤解が生じるのは、1 バイト エンコードを使用すると、CHAR と VARCHAR の格納サイズが *n* バイトとなり、文字の数も *n* となるためです。 しかしながら、[UTF-8](https://www.wikipedia.org/wiki/UTF-8) などのマルチバイト エンコードの場合、より高い Unicode 範囲 (128-1,114,111) では 1 文字に 2 バイト以上が使用されることになります。 たとえば、CHAR(10) として定義された列では、[!INCLUDE[ssde_md](../../includes/ssde_md.md)]で格納できる文字は、1 バイト エンコード (Unicode 範囲 0-127) を使用する文字は 10 個ですが、マルチバイト エンコード (Unicode 範囲 128-1,114,111) を使用する場合は 10 個未満です。 Unicode の格納と文字の範囲の詳細については、「[UTF-8 と UTF-16 でのストレージの相違点](../../relational-databases/collations/collation-and-unicode-support.md#storage_differences)」を参照してください。

データ定義または変数宣言ステートメントで *n* を指定しないと、既定の長さは 1 になります。 CAST 関数および CONVERT 関数で *n* を指定しないと、既定の長さは 30 になります。

COLLATE 句で特定の照合順序を指定しない限り、**char** 型または **varchar** 型を使用するオブジェクトにはデータベースの既定の照合順序が割り当てられます。 照合順序によって、文字型データの格納に使用されるコード ページが制御されます。

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のマルチバイト エンコードには以下が含まれます。

- 一部の東アジア言語に向けた 2 バイト文字セット (DBCS)。コード ページ 936 および 950 (中国語)、932 (日本語)、または 949 (韓国語) を使用します。
- コード ページ 65001 を使用する UTF 8。 **適用対象:** [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[sql-server-2019](../../includes/sssql19-md.md)] 以降)

複数言語をサポートするサイトがある場合:

- [!INCLUDE[sql-server-2019](../../includes/sssql19-md.md)] 以降、Unicode をサポートして文字変換の問題を最小限に抑えるために、UTF-8 が有効になっている照合順序の使用を検討してください。
- 下位バージョンの [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] を使用する場合、文字変換の問題を最小限に抑えるために、Unicode の **nchar** データ型または **nvarchar** データ型を使用することを検討してください。

**char** 型または **varchar** 型を使用する場合は、次のことをお勧めします。

- 列データ エントリのサイズが一定の場合は、**char** を使用します。
- 列データ エントリのサイズが大幅に変化する場合は、**varchar** を使用します。
- 列データ エントリのサイズが大幅に変化し、かつ文字列の長さが 8,000 バイトを超える可能性がある場合は、**varchar(max)** を使用します。

CREATE TABLE または ALTER TABLE 実行時に SET ANSI_PADDING が OFF に設定されている場合、NULL として定義されている **char** 型の列は **varchar** 型として扱われます。

> [!WARNING]
> Null 以外の varchar(max) または nvarchar(max) の各列には、24 バイトの追加の固定割り当てが必要で、これは並べ替え操作中の 8,060 バイトの行制限におけるカウント対象となります。 これにより、テーブル内に作成できる Null 以外の varchar(max) または nvarchar(max) の列数について、暗黙的な制限が生じます。
テーブルの作成時やデータ挿入時に、最大行サイズが許容最大値の 8,060 バイトを超えるという通常の警告以外の、特別なエラーは提供されません。 この大きな行サイズが原因で、クラスター化インデックス キーの更新や列セット全体の並べ替えなどの通常操作の一部でエラー (エラー 512 など) が発生する可能性があります。これは、操作の実行中にのみ発生します。

## <a name="converting-character-data"></a><a name="_character"></a> 文字データの変換

文字式を異なるサイズの文字型に変換する場合、値が新しいデータ型に長すぎる場合は、切り捨てられます。 **uniqueidentifier** 型は、文字式からの変換のための文字型と見なされるため、文字型に変換する場合は切り捨てのルールが適用されます。 例については、後の「例」のセクションを参照してください。

文字式がデータ型やサイズが異なる文字式に変換される場合 (**char(5)** から **varchar(5)** や、**char(20)** から **char(15)** など)、入力値の照合順序が変換後の値に割り当てられます。 文字式でない式が文字型に変換される場合、現在のデータベースの既定の照合順序が変換後の値に割り当てられます。 どちらの場合でも、[COLLATE](../../t-sql/statements/collations.md) 句を使用して特定の照合を割り当てることができます。

> [!NOTE]
> コード ページ変換は **char** および **varchar** データ型に対してはサポートされていますが、**text** データ型に対してはサポートされていません。 以前のバージョンの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] と同様に、コード ページの変換中のデータの損失は報告されません。

近似の **数値** データ型に変換される文字式には、オプションで指数表記を含めることができます。 この表記は、オプションでプラス (+) またはマイナス (-) 記号、次いで数字が続く、小文字の e または大文字の E です。

文字式を正確な **numeric** 型に変換する場合、その文字式は、数字と小数点から構成されている必要があります。必要に応じて、プラス記号 (+) またはマイナス記号 (-) を付けることができます。 先頭の空白は無視されます。 123,456.00 の千の区切り記号など、コンマ区切りの記号は、文字列内で使用できません。

文字式を **money** 型または **smallmoney** 型に変換する場合、その文字式も、小数点とドル記号 ($) を含むことができます。 $123,456.00 のようにコンマを区切り記号として使用できます。

空の文字列が **int** に変換されると、その値は ```0``` になります。 空の文字列を日付に変換されると、その値は[日付の既定値](date-transact-sql.md) (```1900-01-01```) になります。

## <a name="examples"></a>例

### <a name="a-showing-the-default-value-of-n-when-used-in-variable-declaration"></a>A. 変数宣言で使用された場合の n の既定値

次の例は、`char` データ型および `varchar` データ型が変数宣言で使用された場合に、*n* の既定値が 1 になることを示しています。

```sql
DECLARE @myVariable AS VARCHAR = 'abc';
DECLARE @myNextVariable AS CHAR = 'abc';
--The following returns 1
SELECT DATALENGTH(@myVariable), DATALENGTH(@myNextVariable);
GO
```

### <a name="b-showing-the-default-value-of-n-when-varchar-is-used-with-cast-and-convert"></a>B. CAST および CONVERT と共に varchar が使用された場合の n の既定値

次の例は、`char` データ型または `varchar` データ型が `CAST` 関数および `CONVERT` 関数と共に使用された場合に、*n* の既定値が 30 になることを示しています。

```sql
DECLARE @myVariable AS VARCHAR(40);
SET @myVariable = 'This string is longer than thirty characters';
SELECT CAST(@myVariable AS VARCHAR);
SELECT DATALENGTH(CAST(@myVariable AS VARCHAR)) AS 'VarcharDefaultLength';
SELECT CONVERT(CHAR, @myVariable);
SELECT DATALENGTH(CONVERT(CHAR, @myVariable)) AS 'VarcharDefaultLength';
```

### <a name="c-converting-data-for-display-purposes"></a>C. 表示用にデータを変換する

次の例では、2 つの列を文字型に変換し、表示されるデータに特定の形式を適用するスタイルを指定します。 **money** 型は文字データに変換され、スタイル 1 が適用されます。それによって、整数部分は 3 桁ごとにコンマで区切られ、小数点以下は 2 桁までの値が表示されます。 **datetime** 型は文字データに変換され、スタイル 3 が適用されます。それによって、dd/mm/yy の形式でデータが表示されます。 WHERE 句の **money** 型は文字タイプにキャストされ、文字列比較操作が実行されます。

```sql
USE AdventureWorks2012;
GO
SELECT BusinessEntityID,
   SalesYTD,
   CONVERT (VARCHAR(12),SalesYTD,1) AS MoneyDisplayStyle1,
   GETDATE() AS CurrentDate,
   CONVERT(VARCHAR(12), GETDATE(), 3) AS DateDisplayStyle3
FROM Sales.SalesPerson
WHERE CAST(SalesYTD AS VARCHAR(20) ) LIKE '1%';
```

[!INCLUDE[ssResult](../../includes/ssresult-md.md)]

```
BusinessEntityID SalesYTD              DisplayFormat CurrentDate             DisplayDateFormat
---------------- --------------------- ------------- ----------------------- -----------------
278              1453719.4653          1,453,719.47  2011-05-07 14:29:01.193 07/05/11
280              1352577.1325          1,352,577.13  2011-05-07 14:29:01.193 07/05/11
283              1573012.9383          1,573,012.94  2011-05-07 14:29:01.193 07/05/11
284              1576562.1966          1,576,562.20  2011-05-07 14:29:01.193 07/05/11
285              172524.4512           172,524.45    2011-05-07 14:29:01.193 07/05/11
286              1421810.9242          1,421,810.92  2011-05-07 14:29:01.193 07/05/11
288              1827066.7118          1,827,066.71  2011-05-07 14:29:01.193 07/05/11
```

### <a name="d-converting-uniqueidentifer-data"></a>D. Uniqueidentifier 型データを変換する

次の例では、`uniqueidentifier` 型の値を `char` 型の値に変換します。

```sql
DECLARE @myid uniqueidentifier = NEWID();
SELECT CONVERT(CHAR(255), @myid) AS 'char';
```

次の例は、変換後のデータ型に対して値が長すぎる場合のデータの切り捨てを示します。 **uniqueidentifier** 型は 36 文字に制限されているため、この長さを超える文字は切り捨てられます。

```sql
DECLARE @ID NVARCHAR(max) = N'0E984725-C51C-4BF4-9960-E1C80E27ABA0wrong';
SELECT @ID, CONVERT(uniqueidentifier, @ID) AS TruncatedValue;
```

[!INCLUDE[ssResult](../../includes/ssresult-md.md)]

```
String                                       TruncatedValue
-------------------------------------------- ------------------------------------
0E984725-C51C-4BF4-9960-E1C80E27ABA0wrong    0E984725-C51C-4BF4-9960-E1C80E27ABA0

(1 row(s) affected)
```

## <a name="see-also"></a>関連項目

[nchar および nvarchar &#40;Transact-SQL&#41;](../../t-sql/data-types/nchar-and-nvarchar-transact-sql.md)

[CAST および CONVERT &#40;Transact-SQL&#41;](../../t-sql/functions/cast-and-convert-transact-sql.md)

[COLLATE &#40;Transact-SQL&#41;](../../t-sql/statements/collations.md)

[データ型の変換&#40;Transact-SQL&#41;](../../t-sql/data-types/data-type-conversion-database-engine.md)

[データ型 &#40;Transact-SQL&#41;](../../t-sql/data-types/data-types-transact-sql.md)

[データベース サイズの見積もり](../../relational-databases/databases/estimate-the-size-of-a-database.md)

[照合順序と Unicode のサポート](../../relational-databases/collations/collation-and-unicode-support.md)

[1 バイト文字セットとマルチバイト文字セット](/cpp/c-runtime-library/single-byte-and-multibyte-character-sets)
