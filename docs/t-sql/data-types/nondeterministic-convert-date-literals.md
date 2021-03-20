---
description: リテラル日付文字列を DATE 値に非決定論的に変換する
title: 日付リテラルの非決定論的変換 | Microsoft Docs
ms.custom: ''
ms.date: 11/19/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.technology: t-sql
ms.topic: reference
dev_langs:
- TSQL
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: genemi
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 12d0a52315d5865fcda260af787a71cc274a851e
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104736692"
---
# <a name="nondeterministic-conversion-of-literal-date-strings-into-date-values"></a>リテラル日付文字列を DATE 値に非決定論的に変換する

CHARACTER 文字列を DATE データ型に変換することを許可するときは注意が必要です。 このような変換はしばしば _非決定論的_ であるためです。

このような非決定論的な変換は [SET LANGUAGE](../statements/set-language-transact-sql.md) と [SET DATEFORMAT](../statements/set-dateformat-transact-sql.md) の設定を考慮することで制御します。



## <a name="set-language-example-month-name-in-polish"></a>SET LANGUAGE 例: ポーランド語の月名

- `SET LANGUAGE Polish;`

文字の文字列には月の名前を指定できます。 しかしながら、名前は英語でしょうか。ポーランド語でしょうか。クロアチア語でしょうか。あるいは別の言語でしょうか。 また、ユーザーのセッションは該当する LANGUAGE に設定されるでしょうか。

たとえば、_listopad_ という言葉があります。これは月の名前です。 しかしながら、どの月に当たるかは、使用されている SQL システムによって認識される言語に依存します。
- ポーランド語であれば、_listopad_ は 11 月に変換されます (英語の _November_ です)。
- クロアチア語であれば、_listopad_ は 10 月に変換されます (英語の _October_ です)。

#### <a name="code-example-of-set-language"></a>SET LANGUAGE のコード例

```sql
--SELECT alias FROM sys.syslanguages ORDER BY alias;

DECLARE @yourInputDate  NVARCHAR(32) = '28 listopad 2018';

SET LANGUAGE Polish;
SELECT CONVERT(DATE, @yourInputDate) AS [SL_Polish];

SET LANGUAGE Croatian;
SELECT CONVERT(DATE, @yourInputDate) AS [SL_Croatian];

SET LANGUAGE English;


/***  Actual output:  For the two months, note the 11 versus the 10.
SL_Polish
2018-11-28

SL_Croatian
2018-10-28
***/
```



## <a name="set-dateformat-example"></a>SET DATEFORMAT の例

- `SET DATEFORMAT dmy;`

上記の **dmy** 形式によると、'01-03-2018' というサンプル日付文字列は _2018 年 3 月の最初の日_ として解釈されます。

代わりに **mdy** が指定された場合、同じ文字列 '01-03-2018' は _2018 年 1 月の 3 番目の日_ として解釈されます。

また、**ymd** が指定された場合、何が出力されるかはわかりません。 "2018" という数値は日としては大きすぎる数字です。
<!--
The preceding claim of "no guarantee" might be incorrect, in the minds of the SQL query engine Developer team?
-->

#### <a name="specific-countries"></a>特定の国

日本と中国では、DATEFORMAT に **ymd** が使用されます。 この形式の各部分は、最も大きいユニットから最も小さいユニットへという理にかなった順序になっています。 結果的に、このフォーマットは効果的に並べ替えられます。 この形式は _国際式_ と見なされています。 国際式と見なされるのは、4 桁は年度であることがはっきりしており、**ydm** という古式を使用している国はないからです。

ドイツやフランスなど、その他の国では、DATEFORMAT は **dmy** です。これは **'dd-mm-yyyy'** を意味します。 **dmy** 書式は並べ替えが効果的ではありませんが、最も小さいユニットから最も大きいユニットへとなっており、順序としては理にかなっています。

**mdy** は米国とミクロネシア連邦だけで使用されています。これは並べ替えられません。 この書式の混在した順序は、日付を読み上げるときの発話パターンに一致するものです。

#### <a name="code-example-of-set-dateformat-mdy-versus-dmy"></a>SET DATEFORMAT のコード例: *mdy* と *dmy* の違い

次の Transact-SQL コード例では同じ日付文字の文字列が使用されていますが、3 つの異なる DATEFORMAT が設定されています。 このコードを実行すると、コメントにある内容が出力されます。

```sql
DECLARE @yourDateString NVARCHAR(10) = '12-09-2018';
PRINT @yourDateString + '  = the input.';

SET DATEFORMAT dmy;
SELECT CONVERT(DATE, @yourDateString) AS [DMY-Interpretation-of-input-format];

SET DATEFORMAT mdy;
SELECT CONVERT(DATE, @yourDateString) AS [MDY-Interpretation-of-input-format];

SET DATEFORMAT ymd;
SELECT CONVERT(DATE, @yourDateString) AS [YMD-Interpretation--?--NotGuaranteed];


/***  Actual output:
12-09-2018  = the input.

DMY-Interpretation-of-input-format
2018-09-12

MDY-Interpretation-of-input-format
2018-12-09

YMD-Interpretation--?--NotGuaranteed
2018-12-09
***/
```

上のコード例の最後の例では、**ymd** 形式と入力文字列が一致していません。 入力文字列の 3 番目のノードは数値ですが、日としては大きすぎます。 Microsoft は、このような不一致から値が出力されることは保証しません。

#### <a name="convert-offers-explicit-codes-for-_deterministic_-control-of-date-formats"></a>CONVERT からは、日付書式を _決定的に_ 制御するための明示的コードが与えられます。

CAST と CONVERT に関する Microsoft のドキュメント記事には、CONVERT 関数と併用し、日付変換を _決定的に_ 制御できる明示的コードの一覧があります。 この記事は毎月、飛び抜けて高い閲覧数を記録します。

- [CAST と CONVERT (Transact-SQL): 日付と時刻のスタイル](../functions/cast-and-convert-transact-sql.md#date-and-time-styles)
- [CAST と CONVERT (Transact-SQL): 一部の datetime 変換が非決定論的である](../functions/cast-and-convert-transact-sql.md#certain-datetime-conversions-are-nondeterministic)



## <a name="compatibility-level-90-and-above"></a>互換性レベル 90 以上

SQL Server 2000 では、互換性レベルは 80 でした。 80 以下のレベル設定の場合、暗黙的日付変換は決定的でした。

互換性レベルが 90 の SQL Server 2005 からは、暗黙的日付変換が非決定論的になりました。 レベル 90 より、日付変換は SET LANGUAGE と SET DATEFORMAT に依存するようになりました。

#### <a name="unicode"></a>Unicode

<!-- The next live sentence needs an explanatory example!  N'somethingHere?'.
-->
照合順序間で行われる Unicode 以外の文字データの変換も非決定論的であると見なされます。



## <a name="see-also"></a>関連項目

- [セッション言語の設定](../../relational-databases/collations/set-a-session-language.md)
- [日付と時刻のデータ型および関数 (Transact-SQL)](../functions/date-and-time-data-types-and-functions-transact-sql.md)
- [FORMAT (Transact-SQL)](../functions/format-transact-sql.md)
- [ISDATE (Transact-SQL)](../functions/isdate-transact-sql.md)



<!--
This new article is linked-to by the following articles (at least initially on 2018/11/19).....
...
* docs/relational-databases/views/create-indexed-views.md
* docs/relational-databases/indexes/indexes-on-computed-columns.md
* docs/t-sql/functions/cast-and-convert-transact-sql.md
...
As a reaction to public PR 1279, this approach of creating a new article to link to is a better alternative than a docs/includes/ approach.
GeneMi (MightyPen), 2018/11/19
-->

