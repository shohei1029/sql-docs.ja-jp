---
title: CAST および CONVERT (Transact-SQL) | Microsoft Docs
description: CAST および CONVERT Transact-SQL 関数のリファレンス。 これらの関数では、式がある型から別の型に変換されます。
ms.date: 08/23/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- CAST_TSQL
- CONVERT_TSQL
- CAST
- CONVERT
dev_langs:
- TSQL
helpviewer_keywords:
- CAST function
- automatic data type conversion
- varbinary data type
- CONVERT function
- data types [SQL Server], converting
- large value data types
- implicit data type conversions
- image data type, converting
- explicit data type conversions [SQL Server]
- binary [SQL Server], converting
- text data type, converting
- date and time [SQL Server], cast and convert
- truncating conversions
- converting data types [SQL Server], conversion functions
- time zones [SQL Server]
- roundtrip conversions
ms.assetid: a87d0850-c670-4720-9ad5-6f5a22343ea8
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: a0742f3c81e1b98d206bb04a8fa3a38850e77e98
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104751272"
---
# <a name="cast-and-convert-transact-sql"></a>CAST および CONVERT (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

これらの関数は、あるデータ型の式を別のデータ型に変換します。  

## <a name="syntax"></a>構文  
  
```sqlsyntax 
-- CAST Syntax:  
CAST ( expression AS data_type [ ( length ) ] )  
  
-- CONVERT Syntax:  
CONVERT ( data_type [ ( length ) ] , expression [ , style ] )  
```  

![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
*式 (expression)*  
任意の有効な[式](../../t-sql/language-elements/expressions-transact-sql.md)。
  
*data_type*  
対象のデータ型です。 **xml**、**bigint**、**sql_variant** が含まれます。 別名データ型は使用できません。
  
*length*  
データ型でユーザー指定の長さが許可されるとき、ターゲット データ型の長さを指定する任意の整数。 既定値は 30 です。
  
*style*  
CONVERT 関数で *expression* を変換する方法を指定する整数式です。 スタイル値が NULL の場合は、NULL が返されます。 *data_type* は範囲を決定します。 
  
## <a name="return-types"></a>戻り値の型
*data_type* に変換された *expression* を返します。
  
## <a name="date-and-time-styles"></a>日付および時刻のスタイル  
date または time データ型の *expression* の場合、*style* には次の表に示すいずれかの値を指定できます。 その他の値は 0 として処理されます。 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降では、date 型と time 型を **datetimeoffset** に変換するときにサポートされるスタイルは、0 または 1 のみです。 他のすべての変換スタイルでは 9809 が返されます。
  
> [!NOTE]
> [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] は、クウェート アルゴリズムを使用して、アラビア スタイルで、日付の書式をサポートします。
  
|2 桁の年 (yy) (<sup>1</sup>)|4 桁の年 (yyyy)|Standard|入力/出力 (<sup>3</sup>)|  
|---|---|--|---|
|-|**0** または **100** (<sup>1、</sup><sup>2</sup>)|datetime および smalldatetime の既定値|mon dd yyyy hh:miAM (または PM)|  
|**1**|**101**|米国|  1 = mm/dd/yy<br /> 101 = mm/dd/yyyy|  
|**2**|**102**|ANSI|  2 = yy.mm.dd<br /> 102 = yyyy.mm.dd|  
|**3**|**103**|イギリス/フランス|  3 = dd/mm/yy<br /> 103 = dd/mm/yyyy|  
|**4**|**104**|ドイツ語|  4 = dd.mm.yy<br /> 104 = dd.mm.yyyy|  
|**5**|**105**|イタリア語|  5 = dd-mm-yy<br /> 105 = dd-mm-yyyy|  
|**6**|**106** <sup>(1)</sup>|-|  6 = dd mon yy<br /> 106 = dd mon yyyy|  
|**7**|**107** <sup>(1)</sup>|-|  7 = Mon dd, yy<br /> 107 = Mon dd, yyyy|  
|**8** または **24**|**108**|-|hh:mi:ss|  
|-|**9** または **109** (<sup>1、</sup><sup>2</sup>)|既定値 + ミリ秒|mon dd yyyy hh:mi:ss:mmmAM (または PM)|  
|"**10**"|**110**|米国| 10 = mm-dd-yy<br /> 110 = mm-dd-yyyy|  
|**11**|**111**|日本| 11 = yy/mm/dd<br /> 111 = yyyy/mm/dd|  
|**12**|**112**|ISO| 12 = yymmdd<br /> 112 = yyyymmdd|  
|-|**13** または **113** (<sup>1、</sup><sup>2</sup>)|ヨーロッパの既定値 + ミリ秒|dd mon yyyy hh:mi:ss:mmm (24h)|  
|**14**|**114**|-|hh:mi:ss:mmm (24h)|  
|-|**20** または **120** (<sup>2</sup>)|ODBC 標準|yyyy-mm-dd hh:mi:ss (24h)|  
|-|**21** または **25** または **121** (<sup>2</sup>)|time、date、datetime2、および datetimeoffset の ODBC 標準 (ミリ秒を含む) の既定値|yyyy-mm-dd hh:mi:ss.mmm (24h)|  
|**22**|-|米国| mm/dd/yy hh:mi:ss AM (または PM)|
|-|**23**|ISO8601|yyyy-mm-dd|
|-|**126** (<sup>4</sup>)|ISO8601|yyyy-mm-ddThh:mi:ss.mmm (スペースなし)<br /><br /> **注:** ミリ秒 (mmm) の値が 0 の場合、ミリ秒を表す小数部の値は表示されません。 たとえば、値 "2012-11-07T18:26:20.000" は、"2012-11-07T18:26:20"' と表示されます。| 
|-|**127**(<sup>6、7</sup>)|ISO 8601 (タイム ゾーン Z)|yyyy-MM-ddThh:mm:ss.fffZ (スペースなし)<br /><br /> **注:** ミリ秒 (mmm) の値が 0 の場合、ミリ秒を示す小数部の値は表示されません。 たとえば、値 "2012-11-07T18:26:20.000" は、"2012-11-07T18:26:20" と表示されます。|  
|-|**130** (<sup>1、</sup><sup>2</sup>)|Hijri (<sup>5</sup>)|dd mon yyyy hh:mi:ss:mmmAM<br /><br /> このスタイルでは、**mon** は月の正式名に関する複数トークンの Hijri (イスラム暦) ユニコード表現を表します。 この値は、SSMS の既定の米国用インストールでは正しく表示されません。|  
|-|**131** (<sup>2</sup>)|Hijri (<sup>5</sup>)|dd/mm/yyyy hh:mi:ss:mmmAM|  
  
<sup>1</sup> これらのスタイル値で返される結果は非決定的です。 この場合は、すべての (yy) (2 桁の年) スタイルと、(yyyy) (4 桁の年) スタイルのサブセットが対象となります。
  
<sup>2</sup> 既定値 (**0** または **100**、**9** または **109**、**13** または **113**、**20** または **120**、**23**、および **21** または **25** または **121**) は常に 4 桁の年 (yyyy) を返します。

<sup>3</sup>**datetime** に変換する場合は入力になり、文字データに変換する場合は出力になります。

<sup>4</sup> XML で使用するよう設計されています。 **datetime** または **smalldatetime** から文字データに変換する場合の出力形式は、前の表に示したとおりです。

<sup>5</sup> Hijri とはいくつかのバリエーションがあるカレンダー システムです。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ではクウェート アルゴリズムが使用されます。

> [!IMPORTANT]
>  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の既定では、2 桁の年は、終了年 2049 年を基準に解釈されます。 つまり、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] は 2 桁の年 49 を 2049 年と解釈し、2 桁の年 50 を 1950 年と解釈します。 クライアント アプリケーションの中には、Automation オブジェクトに基づくものなど、2030 年を終了年とするものも多くあります。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] には、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] によって使用される基準年を変更するための 2 桁年基準構成オプションが用意されています。 これにより、日付の一貫性のある処理を行うことができます。 年は 4 桁で指定することをお勧めします。

<sup>6</sup> 文字データを **datetime** または **smalldatetime** 型にキャストする場合のみサポートされます。 日付部分または時刻部分のみを表す文字データを、**datetime** または **smalldatetime** データ型にキャストするとき、時刻部分が指定されていないと 00:00:00.000 に設定され、日付部分が指定されていないと 1900-01-01 に設定されます。
  
<sup>7</sup> タイム ゾーン インジケーター **Z** は省略可能です。これを使用すると、タイム ゾーン情報が含まれる XML の **datetime** 値から、タイム ゾーン情報が含まれない [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] **datetime** 値へのマップが容易になります。 Z は、タイム ゾーン UTC-0 を示します。 \+ または - 方向の HH:MM オフセットは、他のタイム ゾーンを示します。 (例: `2006-12-12T23:45:12-08:00`)。
  
**smalldatetime** を文字データに変換する場合、秒またはミリ秒を含むスタイルではそれらの位置に 0 が表示されます。 **datetime** または **smalldatetime** の値から変換するときは、適切な **char** または **varchar** データ型の長さを使用して、日付の不要な部分を切り捨てます。
  
時刻を含むスタイルを使って文字データから **datetimeoffset** に変換すると、タイム ゾーン オフセットが結果に追加されます。
  
## <a name="float-and-real-styles"></a>float 型スタイルと real 型スタイル
*expression* が **float** 型または **real** 型の場合、*style* は次の表に示すいずれかの値になります。 その他の値は 0 として処理されます。
  
|値|出力|  
|---|---|
|**0** (既定値)|最高 6 桁。 該当する場合は、科学的表記法で使用します。|  
|**1**|常に 8 桁。 常に科学的表記法で使用します。|  
|**2**|常に 16 桁。 常に科学的表記法で使用します。|  
|**3**|常に 17 桁。 ロスレス変換に使用します。 このスタイルでは、すべての浮動小数点数または実数が異なる文字列に変換されることが保証されます。<br /><br /> **適用対象:** [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 以降) と [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。|  
|**126、128、129**|レガシのために含まれていますが、将来のリリースではこれらの値は非推奨になる可能性があります。|  
  
## <a name="money-and-smallmoney-styles"></a>money 型スタイルと smallmoney 型スタイル
*expression* が **money** 型または **smallmoney** 型の場合、*style* は次の表に示すいずれかの値になります。 その他の値は 0 として処理されます。
  
|値|出力|  
|---|---|
|**0** (既定値)|小数点位置から左へ 3 桁ごとの位置にはコンマを挿入しません。また、小数点右側には 2 桁をとります。<br /><br />例:4235.98|  
|**1**|小数点位置から左へ 3 桁ごとの位置にはコンマを挿入します。また、小数点右側には 2 桁をとります。<br /><br />例:3,510.92|  
|**2**|小数点位置から左へ 3 桁ごとの位置にはコンマを挿入しません。また、小数点右側には 4 桁をとります。<br /><br />例:4235.9819|  
|**126**|char(n) または varchar(n) に変換する場合はスタイル 2 に相当します。|  
  
## <a name="xml-styles"></a>xml スタイル
*expression* が **xml** 型の場合、*style* は次の表に示すいずれかの値になります。 その他の値は 0 として処理されます。
  
|値|出力|  
|---|---|
|**0** (既定値)|既定の解析動作を使用します。つまり、余分な空白を破棄し、内部の DTD サブセットを許可しません。<br /><br />**注:** **xml** データ型への変換では、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] での余分な空白は XML 1.0 とは別の方法で処理されます。 詳細については、「[XML データのインスタンスの作成](../../relational-databases/xml/create-instances-of-xml-data.md)」を参照してください。|  
|**1**|余分な空白を保持します。 このスタイル設定は、**xml:space="preserve"** の動作と一致するように、既定の **xml:space** の処理を設定します。|  
|**2**|限定的な内部 DTD サブセット処理を有効にします。<br /><br /> 有効な場合、サーバーでは内部 DTD サブセットで提供される情報を次のように使用して、検証を行わない解析操作を実行できます。<br /><br />   - 属性の既定値が適用されます<br />   - 内部エンティティの参照が解決および展開されます<br />   - DTD コンテンツ モデルの構文の正確さが確認されます<br /><br /> パーサーでは外部 DTD サブセットが無視されます。 また、XML 宣言を評価して、**standalone** 属性に **yes** または **no** の値が設定されているかどうかも確認しません。 代わりに、XML インスタンスをスタンドアロン ドキュメントとして解析します。|  
|**3**|余分な空白を保持し、限定的な内部 DTD サブセット処理を有効にします。|  
  
## <a name="binary-styles"></a>バイナリ スタイル
*expression* が **binary(n)** 、**char(n)** 、**varbinary(n)** 、または **varchar(n)** の場合、*style* は次の表に示すいずれかの値になります。 表に記載されていないスタイル値の場合は、エラーが返されます。
  
|値|出力|  
|---|---|
|**0** (既定値)|ASCII 文字をバイナリ バイトに変換するか、バイナリ バイトを ASCII 文字に変換します。 各文字またはバイトは 1:1 で変換されます。<br /><br /> バイナリの *data_type* の場合は、文字 0x が結果の左側に追加されます。|  
|**1**、**2**|バイナリの *data_type* の場合は、式は文字式であることが必要です。 *expression* は **偶数** 桁の 16 進数 (0、1、2、3、4、5、6、7、8、9、A、B、C、D、E、F、a、b、c、d、e、f) である必要があります。 *style* が 1 に設定されている場合、式の最初の 2 文字は 0x である必要があります。 式に奇数桁の文字が含まれているか、いずれかの文字が使用できない場合は、エラーが発生します。<br /><br /> 変換された式の長さが *data_type* の長さを超える場合、結果の右側が切り捨てられます。<br /><br /> 固定長の *data_type* が変換された結果より長い場合は、結果の右側に 0 が追加されます。<br /><br /> *data_type* が文字型の場合は、バイナリ式である必要があります。 各バイナリ文字は、2 桁の 16 進数の英数文字に変換されます。 変換された式の長さが *data_type* の長さを超えているとします。 その場合は切り捨てられます。<br /><br /> *data_type* が固定サイズの文字型で、変換された結果の長さが *data_type* の長さよりも短い場合、変換された式の右側に空白が追加され、偶数桁の 16 進数が維持されます。<br /><br /> *style* が 2 の場合、変換された結果の左側に文字 0x は追加されません。|  
  
## <a name="implicit-conversions"></a>暗黙的な変換
暗黙的な変換では、CAST 関数または CONVERT 関数を指定する必要はありません。 明示的な変換では、CAST 関数または CONVERT 関数を指定する必要があります。 次の図は、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] システムで提供されるデータ型に対して許可されるすべての明示的および暗黙的なデータ型変換を示しています。 **bigint**、**sql_variant**、**xml** が含まれます。 代入時に **sql_variant** データ型からの暗黙的な変換は行われませんが、**sql_variant** への暗黙的な変換は行われます。
  
> [!TIP]  
> このグラフは、[Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=35834)で PNG ファイルとしてダウンロードできます。  
  
![データ型変換テーブル](../../t-sql/data-types/media/lrdatahd.png "データ型変換テーブル")
  
上のグラフは、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] で許可されているすべての明示的および暗黙的な変換を示していますが、結果的に生成される変換のデータ型は実行される操作に依存します。

-  明示的な変換の場合、ステートメント自体によって結果のデータ型が決定されます。    
-  暗黙的な変換の場合、変数の値の設定や列への値の挿入などの代入ステートメントは、変数宣言または列定義によって定義されたデータ型になります。    
-  比較演算子または他の式の場合、結果のデータ型は、[データ型の優先順位](../../t-sql/data-types/data-type-precedence-transact-sql.md)の規則によって異なります。

> [!TIP]
> [変換でデータ型の優先順位の影響](#precedence-example)については、このセクションの後半で実際の例を確認できます。

**datetimeoffset** と、**char**、**nchar**、**nvarchar**、および **varchar** の各文字型との間で変換を行う場合、変換後のタイム ゾーン オフセットの部分は、HH と MM の両方が常に 2 桁である必要があります。 たとえば、-08:00 などです。
  
> [!NOTE]   
> Unicode データでは常に偶数のバイト数が使用されるので、**binary** 型または **varbinary** 型と、Unicode がサポートするデータ型との間でデータを変換する場合は注意してください。 たとえば、次のような変換を行うと、16 進数の 41 は返されません。 16 進数の値 4100 が返されます。`SELECT CAST(CAST(0x41 AS nvarchar) AS varbinary)` 詳細については、「 [Collation and Unicode Support](../../relational-databases/collations/collation-and-unicode-support.md)」を参照してください。 
  
## <a name="large-value-data-types"></a>大きな値のデータ型
大きな値のデータ型では、それより小さな値のデータ型、つまり **nvarchar**、**varbinary**、および **varchar** 型と同様の明示的および暗黙的な変換動作が行われます。 ただし、次のガイドラインを考慮してください。
-   **image** 型から **varbinary(max)** 型への変換およびその逆の変換は暗黙的な変換であり、**text** 型と **varchar(max)** 型の間、および **ntext** 型と **nvarchar(max)** 型の間の変換も暗黙的な変換です。  
-   **varchar(max)** 型のような大きな値のデータ型から、**varchar** 型のような小さな値のデータ型への変換は暗黙的な変換ですが、大きな値のサイズが、小さなデータ型で指定された長さを超えると、切り捨てが発生します。  
-   **nvarchar**、**varbinary**、または **varchar** 型から対応する大きな値のデータ型への変換は、暗黙的に行われます。  
-   **sql_variant** 型から大きな値のデータ型への変換は明示的な変換です。  
-   大きな値のデータ型は、**sql_variant** 型へは変換できません。  
  
**xml** データ型からの変換について詳しくは、「[XML データのインスタンスの作成](../../relational-databases/xml/create-instances-of-xml-data.md)」をご覧ください。
  
## <a name="xml-data-type"></a>xml データ型。
明示的か暗黙的かにかかわらず、**xml** データ型を文字列またはバイナリのデータ型にキャストするとき、**xml** データ型の内容は、定義されている一連のルールに基づいてシリアル化されます。 これらの規則については、「[XML データのシリアル化の定義](../../relational-databases/xml/define-the-serialization-of-xml-data.md)」を参照してください。 他のデータ型から **xml** データ型への変換については、「[XML データのインスタンスの作成](../../relational-databases/xml/create-instances-of-xml-data.md)」をご覧ください。
  
## <a name="text-and-image-data-types"></a>text および image データ型
**text** および **image** データ型では、データ型の自動変換はサポートされていません。 **text** データは文字データに、**image** データは **binary** または **varbinary** 型に明示的に変換できますが、最大の長さは 8,000 バイトに制限されます。 文字を含む文字式を **int** 型に変換するなど、不適切な変換が試行された場合、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ではエラー メッセージが返されます。
  
## <a name="output-collation"></a>出力の照合順序  
CAST または CONVERT 関数が文字列を入出力する場合、出力では入力と同じ照合順序および照合順序ラベルが使用されます。 入力が文字列ではない場合、出力ではデータベースの既定の照合順序、および強制可能な既定照合順序の照合順序ラベルが使用されます。 詳細については、「[照合順序の優先順位 &#40;Transact-SQL&#41;](../../t-sql/statements/collation-precedence-transact-sql.md)」を参照してください。
  
出力に別の照合順序を割り当てるには、CAST 関数または CONVERT 関数の結果式に COLLATE 句を適用します。 次に例を示します。
  
`SELECT CAST('abc' AS varchar(5)) COLLATE French_CS_AS`
  
## <a name="truncating-and-rounding-results"></a>切り捨てと丸め処理の結果
文字式またはバイナリ式 (**binary**、**char**、**nchar**、**nvarchar**、**varbinary**、または **varchar**) を異なるデータ型の式に変換するとき、変換操作により、出力データが切り捨てられたり、出力データの一部だけ表示されたり、エラーが返されたりする場合があります。 このようなことは、結果が小さすぎて表示できない場合に発生します。 **binary**、**char**、**nchar**、**nvarchar**、**varbinary**、および **varchar** 型への変換では、次の表に示す変換を除き、切り捨てが行われます。
  
|変換前のデータ型|変換後のデータ型|結果|  
|---|---|---|
|**int**、**smallint**、または **tinyint**|**char**|*|  
||**varchar**|*|  
||**nchar**|E|  
||**nvarchar**|E|  
|**money**、**smallmoney**、**numeric**、**decimal**、**float**、または **real**|**char**|E|  
||**varchar**|E|  
||**nchar**|E|  
||**nvarchar**|E|  
  
\* = 結果が短すぎて表示できません<br /><br />E = 結果が短すぎて表示できない場合エラーが返される。
  
[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、元のデータから別のデータ型に変換し、再度元のデータ型に戻すラウンド トリップ変換の場合にだけ、異なるバージョンでも同じ結果が生成されることが保証されています。 次にラウンド トリップ変換の例を示します。
  
```sql
DECLARE @myval DECIMAL (5, 2);  
SET @myval = 193.57;  
SELECT CAST(CAST(@myval AS VARBINARY(20)) AS DECIMAL(10,5));  
-- Or, using CONVERT  
SELECT CONVERT(DECIMAL(10,5), CONVERT(VARBINARY(20), @myval));  
```  
  
> [!WARNING]  
> **binary** 型の値を作成し、これを数値データ型に変換することは避けてください。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、**decimal** または **numeric** データ型の **binary** 型への変換が [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のバージョン間で同じになることは保証されていません。  
  
次の例では、短すぎて表示できない結果式を示します。
  
```sql
USE AdventureWorks2012;  
GO  
SELECT p.FirstName, p.LastName, SUBSTRING(p.Title, 1, 25) AS Title,
    CAST(e.SickLeaveHours AS char(1)) AS [Sick Leave]  
FROM HumanResources.Employee e JOIN Person.Person p 
    ON e.BusinessEntityID = p.BusinessEntityID  
WHERE NOT e.BusinessEntityID > 5;  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```   
FirstName   LastName      Title   Sick Leave
---------   ------------- ------- --------`
Ken         Sanchez       NULL   *
Terri       Duffy         NULL   *
Roberto     Tamburello    NULL   *
Rob         Walters       NULL   *
Gail        Erickson      Ms.    *

(5 row(s) affected)  
```
  
小数点の位置が異なるデータ型を変換すると、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] により、切り捨てられた結果の値が返される場合と、丸められた値が返される場合があります。 次の表にその動作を示します。
  
|ソース|ターゲット|動作|  
|---|---|---|
|**numeric**|**numeric**|Round|  
|**numeric**|**int**|Truncate|  
|**numeric**|**money**|Round|  
|**money**|**int**|Round|  
|**money**|**numeric**|Round|  
|**float**|**int**|Truncate|  
|**float**|**numeric**|Round<br /><br /> 科学的表記法を使用した **float** 値から **decimal** または **numeric** への変換は、有効桁数 17 桁までの値に制限されます。 有効桁数が 17 より多い値はゼロに丸められます。|  
|**float**|**datetime**|Round|  
|**datetime**|**int**|Round|  
  
たとえば、値 10.6496 と -10.6496 を **int** または **numeric** 型に変換すると、切り捨てや丸め処理が行われる可能性があります。
  
```sql
SELECT  CAST(10.6496 AS INT) as trunc1,
        CAST(-10.6496 AS INT) as trunc2,
        CAST(10.6496 AS NUMERIC) as round1,
        CAST(-10.6496 AS NUMERIC) as round2;
 ```
クエリ結果を次の表に示します。
 
|trunc1|trunc2|round1|round2|
|---|---|---|---|
|10|-10|11|-11|
 
変換後のデータ型の小数点以下桁数が変換前のデータ型の小数点以下桁数より少ない場合は、値が丸められます。 たとえば、次の変換では `$10.3497` が返されます。
  
`SELECT CAST(10.3496847 AS money);`
  
数値ではない **char**、**nchar**、**nvarchar**、**varchar** データを、**decimal**、**float**、**int**、**numeric** に変換すると、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] はエラー メッセージを返します。 また、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、空の文字列 (" ") を **numeric** または **decimal** に変換してもエラーが返されます。
  
## <a name="certain-datetime-conversions-are-nondeterministic"></a>一部の datetime 変換が非決定的である

文字列から datetime への変換が非決定的であるスタイルは次のようになっています。
  
- 100 未満のすべてのスタイル<sup>1</sup>
- 106  
- 107
- 109
- 113
- 130  
  
<sup>1</sup> スタイル 20 と 21 を除く

詳細については、「[リテラル日付文字列を DATE 値に非決定論的に変換する](../data-types/nondeterministic-convert-date-literals.md)」を参照してください。

## <a name="supplementary-characters-surrogate-pairs"></a>補助文字 (サロゲート ペア)
[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降、補助文字 (SC) の照合順序を使用した場合は、**nchar** または **nvarchar** 型から **nchar** または **nvarchar** 型への CAST 操作を実行すると、サロゲート ペア内では切り捨てられません。 代わりに、補助文字の前で切り捨てが行われます。 たとえば次のコード フラグメントでは、`@x` で `'ab'` だけが保持されます。 補助文字を保持するための十分な領域がありません。
  
```sql
DECLARE @x NVARCHAR(10) = 'ab' + NCHAR(0x10000);  
SELECT CAST (@x AS NVARCHAR(3));  
```  
  
SC の照合順序を使用する場合、`CONVERT` の動作は `CAST` の動作と似たものとなります。 詳細については、「照合順序と Unicode のサポート」の「[補助文字](../../relational-databases/collations/collation-and-unicode-support.md#Supplementary_Characters)」セクションを参照してください。
  
## <a name="compatibility-support"></a>互換性サポート
以前のバージョンの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、**time** および **datetime2** データ型に対する CAST および CONVERT 操作の既定のスタイルは、いずれかの型が計算列の式で使用されている場合を除き、121 です。 計算列の場合、既定のスタイルは 0 です。 この動作は、計算列が作成されるとき、自動パラメーター化を含むクエリで使用されるとき、または制約の定義で使用されるときに、計算列に影響を与えます。
  
[互換性レベル](../../t-sql/statements/alter-database-transact-sql-compatibility-level.md#compatibility-levels-and-database-engine-upgrades) 110 以上では、**time** および **datetime2** データ型に対する CAST および CONVERT 操作は、既定のスタイルとして常に 121 になります。 クエリが古い動作に依存する場合は、110 より小さい互換性レベルを使用するか、または影響を受けるクエリで 0 スタイルを明示的に指定してください。

|[互換性レベル](../../t-sql/statements/alter-database-transact-sql-compatibility-level.md#compatibility-levels-and-database-engine-upgrades)値|CAST と CONVERT の既定のスタイル<sup>1</sup>|計算列の既定のスタイル|  
|------------|------------|------------|
|< **110**|121|0|  
|> = **110**|121|121|  

<sup>1</sup> 計算列を除く

データベースを互換性レベル 110 以上にアップグレードしても、ディスクに格納されているユーザー データは変更されません。 このようなデータは手動で適切に修正する必要があります。 たとえば、SELECT INTO を使用して、前に説明した計算列の式を含むソースからテーブルを作成した場合は、計算列の定義自体ではなく、(スタイル 0 を使用する) データが格納されます。 このようなデータは、手動で更新してスタイル 121 に一致させる必要があります。
  
## <a name="examples"></a><a name="BKMK_examples"></a> 使用例  
  
### <a name="a-using-both-cast-and-convert"></a>A. CAST と CONVERT の両方を使用する  
次の例では、表示価格の最初の桁が `3` である製品について製品名を取得し、その `ListPrice` を `int` 型に変換します。
  
```sql
-- Use CAST  
USE AdventureWorks2012;  
GO  
SELECT SUBSTRING(Name, 1, 30) AS ProductName, ListPrice  
FROM Production.Product  
WHERE CAST(ListPrice AS int) LIKE '33%';  
GO  
  
-- Use CONVERT.  
USE AdventureWorks2012;  
GO  
SELECT SUBSTRING(Name, 1, 30) AS ProductName, ListPrice  
FROM Production.Product  
WHERE CONVERT(int, ListPrice) LIKE '33%';  
GO  
```  

[!INCLUDE[ssResult](../../includes/ssresult-md.md)] このサンプル結果セットは、CAST と CONVERT の両方で同じです。 

```
ProductName                    ListPrice
------------------------------ ---------------------
LL Road Frame - Black, 58      337.22
LL Road Frame - Black, 60      337.22
LL Road Frame - Black, 62      337.22
LL Road Frame - Red, 44        337.22
LL Road Frame - Red, 48        337.22
LL Road Frame - Red, 52        337.22
LL Road Frame - Red, 58        337.22
LL Road Frame - Red, 60        337.22
LL Road Frame - Red, 62        337.22
LL Road Frame - Black, 44      337.22
LL Road Frame - Black, 48      337.22
LL Road Frame - Black, 52      337.22
Mountain-100 Black, 38         3374.99
Mountain-100 Black, 42         3374.99
Mountain-100 Black, 44         3374.99
Mountain-100 Black, 48         3374.99
HL Road Front Wheel            330.06
LL Touring Frame - Yellow, 62  333.42
LL Touring Frame - Blue, 50    333.42
LL Touring Frame - Blue, 54    333.42
LL Touring Frame - Blue, 58    333.42
LL Touring Frame - Blue, 62    333.42
LL Touring Frame - Yellow, 44  333.42
LL Touring Frame - Yellow, 50  333.42
LL Touring Frame - Yellow, 54  333.42
LL Touring Frame - Yellow, 58  333.42
LL Touring Frame - Blue, 44    333.42
HL Road Tire                   32.60

(28 rows affected)
```
  
### <a name="b-using-cast-with-arithmetic-operators"></a>B. CAST を算術演算子と共に使用する  
この例では、今年のこれまでの売り上げ合計 (`Computed`) を手数料のパーセンテージ (`SalesYTD`) で割り、単一列の計算 (`CommissionPCT`) を行います。 この値は、最も近い整数に丸められた後、`int` データ型に CAST されます。
  
```sql
USE AdventureWorks2012;  
GO  
SELECT CAST(ROUND(SalesYTD/CommissionPCT, 0) AS INT) AS Computed  
FROM Sales.SalesPerson   
WHERE CommissionPCT != 0;  
GO  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
```  
Computed
------
379753754
346698349
257144242
176493899
281101272
0  
301872549
212623750
298948202
250784119
239246890
101664220
124511336
97688107

(14 row(s) affected)  
```  
  
### <a name="c-using-cast-to-concatenate"></a>C. CAST を使用して連結する  
この例では、CAST を使用して文字型ではない式を連結します。 AdventureWorksDW データベースを使用します。
  
```sql
SELECT 'The list price is ' + CAST(ListPrice AS VARCHAR(12)) AS ListPrice  
FROM dbo.DimProduct  
WHERE ListPrice BETWEEN 350.00 AND 400.00;  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```  
ListPrice
------------------------
The list price is 357.06
The list price is 364.09
The list price is 364.09
The list price is 364.09
The list price is 364.09  
```  
  
### <a name="d-using-cast-to-produce-more-readable-text"></a>D. CAST を使用してテキストをさらに読みやすくする  
この例では、SELECT リストの中で CAST を使用し、`Name` 列を **char(10)** 型の列に変換します。 AdventureWorksDW データベースを使用します。
  
```sql
SELECT DISTINCT CAST(EnglishProductName AS CHAR(10)) AS Name, ListPrice  
FROM dbo.DimProduct  
WHERE EnglishProductName LIKE 'Long-Sleeve Logo Jersey, M';  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```  
Name        ListPrice
----------  ---------
Long-Sleev  31.2437
Long-Sleev  32.4935
Long-Sleev  49.99  
```  
  
### <a name="e-using-cast-with-the-like-clause"></a>E. CAST を LIKE 句と共に使用する  
この例では、`money` 列の `SalesYTD` の値をデータ型 `int` に変換した後、データ型 `char(20)` に変換して、`LIKE` 句で使用できるようにします。
  
```sql
USE AdventureWorks2012;  
GO  
SELECT p.FirstName, p.LastName, s.SalesYTD, s.BusinessEntityID  
FROM Person.Person AS p   
JOIN Sales.SalesPerson AS s   
    ON p.BusinessEntityID = s.BusinessEntityID  
WHERE CAST(CAST(s.SalesYTD AS INT) AS char(20)) LIKE '2%';  
GO  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```  
FirstName        LastName            SalesYTD         BusinessEntityID
---------------- ------------------- ---------------- -------------
Tsvi             Reiter              2811012.7151      279
Syed             Abbas               219088.8836       288
Rachel           Valdez              2241204.0424      289

(3 row(s) affected)  
```
  
### <a name="f-using-convert-or-cast-with-typed-xml"></a>F. CONVERT または CAST を、型指定された XML と共に使用する  
これらの例では、[XML データ型と列 &#40;SQL Server&#41;](../../relational-databases/xml/xml-data-type-and-columns-sql-server.md) で CONVERT を使用し、型指定された XML への変換を行います。
  
次の例では、空白、テキスト、およびマークアップのある文字列を、型指定された XML に変換し、すべての余分な空白 (ノード間の境界の空白) を削除します。
  
```sql
SELECT CONVERT(XML, '<root><child/></root>')  
```  
  
次の例では、空白、テキスト、およびマークアップのある同様の文字列を、型指定された XML に変換し、すべての余分な空白 (ノード間の境界の空白) を保持します。
  
```sql
SELECT CONVERT(XML, '<root>          <child/>         </root>', 1)  
```  
  
次の例では、空白、テキスト、マークアップのある文字列を、型指定された XML にキャストします。
  
```sql
SELECT CAST('<Name><FName>Carol</FName><LName>Elliot</LName></Name>'  AS XML)  
```  
  
他の例については、「[XML データのインスタンスの作成](../../relational-databases/xml/create-instances-of-xml-data.md)」をご覧ください。
  
### <a name="g-using-cast-and-convert-with-datetime-data"></a>G. CAST および CONVERT を datetime データと共に使用する  
`GETDATE()` の値以降、この例では、現在の日付と時刻を表示し、`CAST` を使用して現在の日付と時刻を文字列データ型に変更した後、`CONVERT` を使用して `ISO 8601` 形式で日付と時刻を表示します。
  
```sql
SELECT   
   GETDATE() AS UnconvertedDateTime,  
   CAST(GETDATE() AS NVARCHAR(30)) AS UsingCast,  
   CONVERT(nvarchar(30), GETDATE(), 126) AS UsingConvertTo_ISO8601  ;  
GO  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```  
UnconvertedDateTime     UsingCast              UsingConvertTo_ISO8601
----------------------- ---------------------- ------------------------------
2006-04-18 09:58:04.570 Apr 18 2006  9:58AM    2006-04-18T09:58:04.570

(1 row(s) affected)  
```
  
この例は、前の例とほぼ逆になります。 この例では、日付と時刻を文字型データとして表示し、`CAST` を使用して文字型データを `datetime` データ型に変更します。さらに、`CONVERT` を使用して、文字型データを `datetime` データ型に変換します。
  
```sql
SELECT   
   '2006-04-25T15:50:59.997' AS UnconvertedText,  
   CAST('2006-04-25T15:50:59.997' AS datetime) AS UsingCast,  
   CONVERT(datetime, '2006-04-25T15:50:59.997', 126) AS UsingConvertFrom_ISO8601 ;  
GO  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```  
UnconvertedText         UsingCast               UsingConvertFrom_ISO8601
----------------------- ----------------------- ------------------------
2006-04-25T15:50:59.997 2006-04-25 15:50:59.997 2006-04-25 15:50:59.997

(1 row(s) affected)  
```
  
### <a name="h-using-convert-with-binary-and-character-data"></a>H. CONVERT をバイナリ データおよび文字データと共に使用する  
これらの例では、さまざまなスタイルを使用してバイナリ データと文字データを変換した結果を示します。
  
```sql
--Convert the binary value 0x4E616d65 to a character value.  
SELECT CONVERT(CHAR(8), 0x4E616d65, 0) AS [Style 0, binary to character];  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```  
Style 0, binary to character
----------------------------
Name  

(1 row(s) affected)  
```
 
この例では、Style 1 が結果の切り捨てを強制できることを示します。 結果セット内の文字 0x は、切り捨てを強制します。  
```sql  
SELECT CONVERT(CHAR(8), 0x4E616d65, 1) AS [Style 1, binary to character];  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```  
Style 1, binary to character
------------------------------
0x4E616D

(1 row(s) affected)  
```  
 
この例は、結果に文字 0x が含まれていないため、Style 2 は結果を切り捨てないことを示しています。  
```sql  
SELECT CONVERT(CHAR(8), 0x4E616d65, 2) AS [Style 2, binary to character];  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```  
Style 2, binary to character
------------------------------
4E616D65

(1 row(s) affected)  
```
  
文字値 'Name' をバイナリ値に変換します。  
```sql
SELECT CONVERT(BINARY(8), 'Name', 0) AS [Style 0, character to binary];  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```  
Style 0, character to binary
----------------------------
0x4E616D6500000000

(1 row(s) affected)  
```
  
```sql
SELECT CONVERT(BINARY(4), '0x4E616D65', 1) AS [Style 1, character to binary];  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```  
Style 1, character to binary
---------------------------- 
0x4E616D65

(1 row(s) affected)  
```  

```sql
SELECT CONVERT(BINARY(4), '4E616D65', 2) AS [Style 2, character to binary];  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```  
Style 2, character to binary  
----------------------------------  
0x4E616D65

(1 row(s) affected)  
```  
  
### <a name="i-converting-date-and-time-data-types"></a>I. 日付と時刻のデータ型の変換  
この例では、date、time、および datetime データ型の変換の例を示します。
  
```sql
DECLARE @d1 DATE, @t1 TIME, @dt1 DATETIME;  
SET @d1 = GETDATE();  
SET @t1 = GETDATE();  
SET @dt1 = GETDATE();  
SET @d1 = GETDATE();  
-- When converting date to datetime the minutes portion becomes zero.  
SELECT @d1 AS [DATE], CAST (@d1 AS DATETIME) AS [date as datetime];  
-- When converting time to datetime the date portion becomes zero   
-- which converts to January 1, 1900.  
SELECT @t1 AS [TIME], CAST (@t1 AS DATETIME) AS [time as datetime];  
-- When converting datetime to date or time non-applicable portion is dropped.  
SELECT @dt1 AS [DATETIME], CAST (@dt1 AS DATE) AS [datetime as date], 
   CAST (@dt1 AS TIME) AS [datetime as time];  
```  

### <a name="j-using-convert-with-datetime-data-in-different-formats"></a>J. 異なる形式で CONVERT と datetime データを使用する  
`GETDATE()` 値から、この例では `CONVERT` を使用し、この記事の「[日付および時刻のスタイル](#date-and-time-styles)」セクションのあらゆる日付スタイルと時刻スタイルを表示します。

|形式番号|クエリの例|サンプルの結果|
|--------|------------------------------------|------------------|
|0|`SELECT CONVERT(NVARCHAR, GETDATE(), 0)`|Aug 23 2019  1:39PM|
|1|`SELECT CONVERT(NVARCHAR, GETDATE(), 1)`|08/23/19|
|2|`SELECT CONVERT(NVARCHAR, GETDATE(), 2)`|19.08.23|
|3|`SELECT CONVERT(NVARCHAR, GETDATE(), 3)`|23/08/19|
|4|`SELECT CONVERT(NVARCHAR, GETDATE(), 4)`|23.08.19|
|5|`SELECT CONVERT(NVARCHAR, GETDATE(), 5)`|23-08-19|
|6|`SELECT CONVERT(NVARCHAR, GETDATE(), 6)`|23 Aug 19|
|7|`SELECT CONVERT(NVARCHAR, GETDATE(), 7)`|Aug 23, 19|
|8 または 24 または 108|`SELECT CONVERT(NVARCHAR, GETDATE(), 8)`|13:39:17|
|9 または 109|`SELECT CONVERT(NVARCHAR, GETDATE(), 9)`|Aug 23 2019  1:39:17:090PM|
|10|`SELECT CONVERT(NVARCHAR, GETDATE(), 10)`|08-23-19|
|11|`SELECT CONVERT(NVARCHAR, GETDATE(), 11)`|19/08/23|
|12|`SELECT CONVERT(NVARCHAR, GETDATE(), 12)`|190823|
|13 または 113|`SELECT CONVERT(NVARCHAR, GETDATE(), 13)`|23 Aug 2019 13:39:17:090|
|14 または 114|`SELECT CONVERT(NVARCHAR, GETDATE(), 14)`|13:39:17:090|
|20 または 120|`SELECT CONVERT(NVARCHAR, GETDATE(), 20)`|2019-08-23 13:39:17|
|21 または 25 または 121|`SELECT CONVERT(NVARCHAR, GETDATE(), 21)`|2019-08-23 13:39:17.090|
|22|`SELECT CONVERT(NVARCHAR, GETDATE(), 22)`|08/23/19  1:39:17 PM|
|23|`SELECT CONVERT(NVARCHAR, GETDATE(), 23)`|2019-08-23|
|101|`SELECT CONVERT(NVARCHAR, GETDATE(), 101)`|08/23/2019|
|102|`SELECT CONVERT(NVARCHAR, GETDATE(), 102)`|2019.08.23|
|103|`SELECT CONVERT(NVARCHAR, GETDATE(), 103)`|23/08/2019|
|104|`SELECT CONVERT(NVARCHAR, GETDATE(), 104)`|23.08.2019|
|105|`SELECT CONVERT(NVARCHAR, GETDATE(), 105)`|23-08-2019|
|106|`SELECT CONVERT(NVARCHAR, GETDATE(), 106)`|23 Aug 2019|
|107|`SELECT CONVERT(NVARCHAR, GETDATE(), 107)`|2019 年 8 月 23 日|
|110|`SELECT CONVERT(NVARCHAR, GETDATE(), 110)`|08-23-2019|
|111|`SELECT CONVERT(NVARCHAR, GETDATE(), 111)`|2019/08/23|
|112|`SELECT CONVERT(NVARCHAR, GETDATE(), 112)`|20190823|
|113|`SELECT CONVERT(NVARCHAR, GETDATE(), 113)`|23 Aug 2019 13:39:17.090|
|120|`SELECT CONVERT(NVARCHAR, GETDATE(), 120)`|2019-08-23 13:39:17|
|121|`SELECT CONVERT(NVARCHAR, GETDATE(), 121)`|2019-08-23 13:39:17.090|
|126|`SELECT CONVERT(NVARCHAR, GETDATE(), 126)`|2019-08-23T13:39:17.090|
|127|`SELECT CONVERT(NVARCHAR, GETDATE(), 127)`|2019-08-23T13:39:17.090|
|130|`SELECT CONVERT(NVARCHAR, GETDATE(), 130)`|22 ذو الحجة 1440  1:39:17.090P|
|131|`SELECT CONVERT(NVARCHAR, GETDATE(), 131)`|22/12/1440  1:39:17.090PM|

### <a name="k-effects-of-data-type-precedence-in-allowed-conversions"></a><a name="precedence-example"></a> K. 許可される変換におけるデータ型の優先順位の影響  
次の例では型 VARCHAR の変数が定義され、整数値が変数に代入された後、文字列型の変数の連結が選択されます。

```sql
DECLARE @string VARCHAR(10);
SET @string = 1;
SELECT @string + ' is a string.' AS Result
```

[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```  
Result
-----------------------
1 is a string.
```  

int 値 1 は VARCHAR に変換されました。

次の例では、同様のクエリで代わりに int 変数を利用しています。

```sql
DECLARE @notastring INT;
SET @notastring = '1';
SELECT @notastring + ' is not a string.' AS Result
```

ここでは、SELECT ステートメントからは次のエラーがスローされます。

```
Msg 245, Level 16, State 1, Line 3
Conversion failed when converting the varchar value ' is not a string.' to data type int.
```

式 `@notastring + ' is not a string.'` を評価するために、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、式の結果を計算する前に、データ型の優先順位の規則に従って暗黙的な変換を完了する必要があります。 int は VARCHAR よりも優先順位が高いため、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では文字列の整数への変換が試行され、その文字列を整数に変換できないため失敗します。 

変換可能な文字列を指定する場合、ステートメントが成功します。次に例を示します。

```SQL
DECLARE @notastring INT;
SET @notastring = '1';
SELECT @notastring + '1'
```

この場合、文字列 `'1'` を整数値 1 に変換できるため、この SELECT ステートメントからは値 2 が返されます。 指定されたデータ型が整数である場合、+ 演算子は文字列連結ではなく加算演算子になります。

## <a name="examples-sssdwfull-and-sspdw"></a>例: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)]、[!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="l-using-cast-and-convert"></a>L. CAST と CONVERT を使用する  
次の各例では、表示価格の最初の桁が `3` である製品について製品名を取得し、これらの製品の `ListPrice` を **int** 型に変換します。`AdventureWorksDW2016` データベースを使用します。
  
```sql
SELECT EnglishProductName AS ProductName, ListPrice  
FROM dbo.DimProduct  
WHERE CAST(ListPrice AS int) LIKE '3%';  
```  
  
CAST ではなく CONVERT を使用した同じクエリの例を次に示します。 `AdventureWorksDW2016` データベースを使用します。
  
```sql
SELECT EnglishProductName AS ProductName, ListPrice  
FROM dbo.DimProduct  
WHERE CONVERT(INT, ListPrice) LIKE '3%';  
```  
  
### <a name="m-using-cast-with-arithmetic-operators"></a>M. CAST を算術演算子と共に使用する  
この例では、製品単価 (`UnitPrice`) を割引率 (`UnitPriceDiscountPct`) で割り、単一列の値を計算します。 この結果は、最も近い整数に丸められ、最終的に `int` データ型に変換されます。 この例では、`AdventureWorksDW2016` データベースを使用します。
  
```sql
SELECT ProductKey, UnitPrice,UnitPriceDiscountPct,  
       CAST(ROUND (UnitPrice*UnitPriceDiscountPct,0) AS int) AS DiscountPrice  
FROM dbo.FactResellerSales  
WHERE SalesOrderNumber = 'SO47355'   
      AND UnitPriceDiscountPct > .02;  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```  
ProductKey  UnitPrice  UnitPriceDiscountPct  DiscountPrice
----------  ---------  --------------------  -------------
323         430.6445   0.05                  22
213         18.5043    0.05                  1
456         37.4950    0.10                  4
456         37.4950    0.10                  4
216         18.5043    0.05                  1  
```  
  
### <a name="n-using-cast-with-the-like-clause"></a>北 CAST を LIKE 句と共に使用する  
この例では、**money** 列 `ListPrice` を **int** 型に変換して、次に LIKE 句と共に使用できるよう **char(20)** 列に変換します。 この例では、`AdventureWorksDW2016` データベースを使用します。  
  
```sql
SELECT EnglishProductName AS Name, ListPrice  
FROM dbo.DimProduct  
WHERE CAST(CAST(ListPrice AS INT) AS CHAR(20)) LIKE '2%';  
```  
  
### <a name="o-using-cast-and-convert-with-datetime-data"></a>O. CAST および CONVERT を datetime データと共に使用する  
この例では、現在の日付と時刻を表示し、CAST を使用して現在の日付と時刻を文字列データ型に変更した後、最終的に CONVERT を使用して ISO 8601 形式で日付と時刻を表示します。 この例では、`AdventureWorksDW2016` データベースを使用します。
  
```sql
SELECT TOP(1)  
   SYSDATETIME() AS UnconvertedDateTime,  
   CAST(SYSDATETIME() AS NVARCHAR(30)) AS UsingCast,  
   CONVERT(NVARCHAR(30), SYSDATETIME(), 126) AS UsingConvertTo_ISO8601  
FROM dbo.DimCustomer;  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```  
UnconvertedDateTime     UsingCast                     UsingConvertTo_ISO8601  
---------------------   ---------------------------   ---------------------------  
07/20/2010 1:44:31 PM   2010-07-20 13:44:31.5879025   2010-07-20T13:44:31.5879025  
```  
  
この例は、前の例とほぼ逆になります。 この例では、日付と時刻を文字型データとして表示し、CAST を使用して文字型データを **datetime** データ型に変更します。さらに、CONVERT を使用して、文字型データを **datetime** データ型に変換します。 この例では、`AdventureWorksDW2016` データベースを使用します。
  
```sql
SELECT TOP(1)   
   '2010-07-25T13:50:38.544' AS UnconvertedText,  
CAST('2010-07-25T13:50:38.544' AS DATETIME) AS UsingCast,  
   CONVERT(DATETIME, '2010-07-25T13:50:38.544', 126) AS UsingConvertFrom_ISO8601  
FROM dbo.DimCustomer;  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```  
UnconvertedText         UsingCast               UsingConvertFrom_ISO8601
----------------------- ----------------------- ------------------------
2010-07-25T13:50:38.544 07/25/2010 1:50:38 PM   07/25/2010 1:50:38 PM  
```  

## <a name="see-also"></a>関連項目
[データ型の優先順位 &#40;Transact-SQL&#41;](../../t-sql/data-types/data-type-precedence-transact-sql.md)       
[データ型の変換 &#40;データベース エンジン&#41;](../../t-sql/data-types/data-type-conversion-database-engine.md)     
[FORMAT &#40;Transact-SQL&#41;](../../t-sql/functions/format-transact-sql.md)      
[STR &#40;Transact-SQL&#41;](../../t-sql/functions/str-transact-sql.md)     
[SELECT &#40;Transact-SQL&#41;](../../t-sql/queries/select-transact-sql.md)      
[システム関数 &#40;Transact-SQL&#41;](../../relational-databases/system-functions/system-functions-category-transact-sql.md)      
[照合順序と Unicode のサポート](../../relational-databases/collations/collation-and-unicode-support.md)      
[国際化に対応した Transact-SQL ステートメントの記述](../../relational-databases/collations/write-international-transact-sql-statements.md)       
  
