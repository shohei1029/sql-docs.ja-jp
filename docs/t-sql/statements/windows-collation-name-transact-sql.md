---
description: Windows 照合順序名 (Transact-SQL)
title: Windows 照合順序名 (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/06/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
dev_langs:
- TSQL
helpviewer_keywords:
- Windows collations [SQL Server]
- names [SQL Server], collations
- collations [SQL Server], Windows collations
- Collation Designator
ms.assetid: acceef84-2c68-46e2-a021-be019b7ab14e
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: ebe10e5195a934ae7921619c5914d65dfbb50eaa
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104733430"
---
# <a name="windows-collation-name-transact-sql"></a>Windows 照合順序名 (Transact-SQL)

[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] で COLLATE 句に Windows 照合順序名を指定します。 Windows 照合順序名は、照合順序指定子と比較形式で構成されます。

![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)

## <a name="syntax"></a>構文

```syntaxsql
<Windows_collation_name> :: =
<CollationDesignator>_<ComparisonStyle>

<ComparisonStyle> :: =
{ <CaseSensitivity>_<AccentSensitivity> [ _<KanatypeSensitive> ] [ _<WidthSensitive> ] [ _<VariationSelectorSensitive> ] 
}
| { _UTF8 }
| { _BIN | _BIN2 }
```

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数

*CollationDesignator*   
Windows 照合順序で使用される基本照合順序規則を指定します。 基本照合順序規則には、次の要素が含まれます。

- 辞書順での並べ替えを指定した場合に適用される並べ替えおよび比較規則。 並べ替え規則は、アルファベットまたは言語に基づきます。
- **varchar** データを格納するために使用されるコード ページ。

いくつかの例を次に示します。

- Latin1\_General または French: 両方でコード ページ 1252 が使用されます。
- Turkish: コード ページ 1254 が使用されます。

*CaseSensitivity*  
**CI** を指定すると大文字小文字は区別されず、**CS** を指定すると大文字小文字が区別されます。

*AccentSensitivity*  
**AI** を指定するとアクセントは区別されず、**AS** を指定するとアクセントが区別されます。

*KanatypeSensitive*  
このオプションを省略すると、かなが区別されません。**KS** を指定すると、かなが区別されます。

*WidthSensitivity*  
このオプションを省略すると、文字幅が区別されません。**WS** を指定すると、文字幅が区別されます。

*VariationSelectorSensitivity*  
- **適用対象**:[!INCLUDE[sssql17-md](../../includes/sssql17-md.md)] 以降 

- このオプションを省略すると、異体字セレクターが区別されません。**VSS** を指定すると、異体字セレクターが区別されます。

**UTF8**  
- **適用対象**:[!INCLUDE[sql-server-2019](../../includes/sssql19-md.md)] 以降   

- 対象となるデータ型で UTF-8 のエンコードが使用されるように指定します。 詳細については、「 [Collation and Unicode Support](../../relational-databases/collations/collation-and-unicode-support.md)」を参照してください。

**BIN**  
旧バージョンとの互換性のあるバイナリ並べ替え順を使用します。

**BIN2**  
コード ポイントの比較セマンティクスを使用するバイナリ並べ替え順を指定します。

## <a name="remarks"></a>注釈
照合順序のバージョンによっては、一部のコード ポイントで、並べ替え加重や大文字/小文字マッピングが定義されない可能性があります。 たとえば、次のような `LOWER` 関数の出力を比較してみます。この場合、同じ文字が指定されていますが、同じ照合順序でもバージョンは異なります。

```sql
SELECT NCHAR(504) COLLATE Latin1_General_CI_AS AS [Uppercase],
       NCHAR(505) COLLATE Latin1_General_CI_AS AS [Lowercase];
-- Ǹ    ǹ


SELECT LOWER(NCHAR(504) COLLATE Latin1_General_CI_AS) AS [Version80Collation],
       LOWER(NCHAR(504) COLLATE Latin1_General_100_CI_AS) AS [Version100Collation];
-- Ǹ    ǹ
```

最初のステートメントには、古い照合順序のこの文字の大文字と小文字の両方の形式が示されています (Unicode データを操作する場合、照合順序は文字の可用性には影響しません)。 しかし、2 番目のステートメントでは、照合順序が Latin1\_General\_CI\_AS である場合、大文字が返されます。これは、このコード ポイントのその照合順序には、小文字のマッピングが定義されていないためです。

一部の言語では、古い照合順序を回避すると重大な結果となる可能性があります。 たとえば、Telegu がこれに該当します。

Windows 照合順序と [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 照合順序で、同じクエリに対して異なるクエリ プランが生成される場合があります。

## <a name="examples"></a>例

次に Windows 照合順序名の例をいくつか示します。

- **Latin1\_General\_100\_CI\_AS**

  照合順序に、Latin1 一般辞書の並べ替え規則が使用され、コード ページ 1252 と対応付けられます。 これはバージョン \_100 の照合順序であり、大文字と小文字は区別されず (CI)、アクセントは区別されます (AS)。

- **Estonian\_CS\_AS**

  照合順序ではエストニア語辞書の並べ替え規則が使用され、コード ページ 1257 にマップされます。 これはバージョン \_80 の照合順序であり (名前にバージョン番号がないことで暗黙的に示されている)、大文字と小文字が区別され (CS)、アクセントが区別されます (AS)。

- **Japanese\_Bushu\_Kakusu\_140\_BIN2**

  照合順序ではバイナリ コード ポイントの並べ替え規則が使用され、コード ページ 932 にマップされます。 これはバージョン \_140 の照合順序であり、日本語の部首画数辞書並べ替え規則は無視されます。

## <a name="windows-collations"></a>Windows 照合順序

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンスでサポートされている Windows 照合順序の一覧を表示するには、次のクエリを実行します。

```sql
SELECT * FROM sys.fn_helpcollations() WHERE [name] NOT LIKE N'SQL%';
```

次の表に、[!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] でサポートされるすべての Windows 照合順序を示します。

|Windows ロケール|照合順序バージョン 100|照合順序バージョン 90|
|--------------------|---------------------------|--------------------------|
|アルザス語 (フランス)|Latin1_General_100_|使用不可|
|アムハラ語 (エチオピア)|Latin1_General_100_|使用不可|
|アルメニア語 (アルメニア)|Cyrillic_General_100_|使用不可|
|アッサム語 (インド)|Assamese_100_ <sup>1</sup>|使用不可|
|ベンガル語 (バングラデシュ)|Bengali_100_<sup>1</sup>|使用不可|
|バシキール語 (ロシア)|Bashkir_100_|使用不可|
|バスク語 (バスク)|Latin1_General_100_|使用不可|
|ベンガル語 (インド)|Bengali_100_<sup>1</sup>|使用不可|
|ボスニア語 (ボスニア・ヘルツェゴビナ、キリル文字)|Bosnian_Cyrillic_100_|使用不可|
|ボスニア語 (ボスニア・ヘルツェゴビナ、ラテン文字)|Bosnian_Latin_100_|使用不可|
|ブルトン語 (フランス)|Breton_100_|使用不可|
|中国語 (中華人民共和国マカオ特別行政区)|Chinese_Traditional_Pinyin_100_|使用不可|
|中国語 (中華人民共和国マカオ特別行政区)|Chinese_Traditional_Stroke_Order_100_|使用不可|
|中国語 (シンガポール)|Chinese_Simplified_Stroke_Order_100_|使用不可|
|コルシカ語 (フランス)|Corsican_100_|使用不可|
|クロアチア語 (ボスニア・ヘルツェゴビナ、ラテン文字)|Croatian_100_|使用不可|
|ダリー語 (アフガニスタン)|Dari_100_|使用不可|
|英語 (インド)|Latin1_General_100_|使用不可|
|英語 (マレーシア)|Latin1_General_100_|使用不可|
|英語 (シンガポール)|Latin1_General_100_|使用不可|
|フィリピノ語 (フィリピン)|Latin1_General_100_|使用不可|
|フリジア語 (オランダ)|Frisian_100_|使用不可|
|グルジア語 (グルジア)|Cyrillic_General_100_|使用不可|
|グリーンランド語 (グリーンランド)|Danish_Greenlandic_100_|使用不可|
|グジャラート語 (インド)|Indic_General_100_<sup>1</sup>|Indic_General_90_|
|ハウサ語 (ナイジェリア、ラテン文字)|Latin1_General_100_|使用不可|
|ヒンディー語 (インド)|Indic_General_100_<sup>1</sup>|Indic_General_90_|
|イボ語 (ナイジェリア)|Latin1_General_100_|使用不可|
|イヌクティトット語 (カナダ、ラテン文字)|Latin1_General_100_|使用不可|
|イヌクティトット語 (音節文字) カナダ|Latin1_General_100_|使用不可|
|アイルランド語 (アイルランド)|Latin1_General_100_|使用不可|
|日本語 (日本 XJIS)|Japanese_XJIS_100_|Japanese_90_、Japanese_|
|日本語 (日本)|Japanese_Bushu_Kakusu_100_|使用不可|
|カンナダ語 (インド)|Indic_General_100_<sup>1</sup>|Indic_General_90_|
|クメール語 (カンボジア)|Khmer_100_<sup>1</sup>|使用不可|
|キチェ語 (グアテマラ)|Modern_Spanish_100_|使用不可|
|キニヤルワンダ語 (ルワンダ)|Latin1_General_100_|使用不可|
|コーンクニー語 (インド)|Indic_General_100_<sup>1</sup>|Indic_General_90_|
|ラオス語 (ラオス人民民主共和国)|Lao_100_<sup>1</sup>|使用不可|
|下ソルブ語 (ドイツ)|Latin1_General_100_|使用不可|
|ルクセンブルク語 (ルクセンブルク)|Latin1_General_100_|使用不可|
|マラヤーラム語 (インド)|Indic_General_100_<sup>1</sup>|使用不可|
|マルタ語 (マルタ)|Maltese_100_|使用不可|
|マオリ語 (ニュージーランド)|Maori_100_|使用不可|
|マプ語 (チリ)|Mapudungan_100_|使用不可|
|マラーティー語 (インド)|Indic_General_100_<sup>1</sup>|Indic_General_90_|
|モホーク語 (カナダ)|Mohawk_100_|使用不可|
|モンゴル語 (PRC)|Cyrillic_General_100_|使用不可|
|ネパール語 (ネパール)|Nepali_100_<sup>1</sup>|使用不可|
|ノルウェー語 (ブークモール、ノルウェー)|Norwegian_100_|使用不可|
|ノルウェー語 (ニーノシュク、ノルウェー)|Norwegian_100_|使用不可|
|オクシタン語 (フランス)|French_100_|使用不可|
|オディア語 (インド)|Indic_General_100_<sup>1</sup>|使用不可|
|パシュトゥー語 (アフガニスタン)|Pashto_100_<sup>1</sup>|使用不可|
|ペルシア語 (イラン)|Persian_100_|使用不可|
|パンジャーブ語 (インド)|Indic_General_100_<sup>1</sup>|Indic_General_90_|
|ケチュア語 (ボリビア)|Latin1_General_100_|使用不可|
|ケチュア語 (エクアドル)|Latin1_General_100_|使用不可|
|ケチュア語 (ペルー)|Latin1_General_100_|使用不可|
|ロマンシュ語 (スイス)|Romansh_100_|使用不可|
|サーミ語 (イナリ、フィンランド)|Sami_Sweden_Finland_100_|使用不可|
|サーミ語 (ルレ、ノルウェー)|Sami_Norway_100_|使用不可|
|サーミ語 (ルレ、スウェーデン)|Sami_Sweden_Finland_100_|使用不可|
|サーミ語 (北、フィンランド)|Sami_Sweden_Finland_100_|使用不可|
|サーミ語 (北、ノルウェー)|Sami_Norway_100_|使用不可|
|サーミ語 (北、スウェーデン)|Sami_Sweden_Finland_100_|使用不可|
|サーミ語 (スコルト、フィンランド)|Sami_Sweden_Finland_100_|使用不可|
|サーミ語 (南、ノルウェー)|Sami_Norway_100_|使用不可|
|サーミ語 (南、スウェーデン)|Sami_Sweden_Finland_100_|使用不可|
|サンスクリット語 (インド)|Indic_General_100_<sup>1</sup>|Indic_General_90_|
|セルビア語 (ボスニア・ヘルツェゴビナ、キリル文字)|Serbian_Cyrillic_100_|使用不可|
|セルビア語 (ボスニア・ヘルツェゴビナ、ラテン文字)|Serbian_Latin_100_|使用不可|
|セルビア語 (セルビア、キリル文字)|Serbian_Cyrillic_100_|使用不可|
|セルビア語 (セルビア、ラテン文字)|Serbian_Latin_100_|使用不可|
|セソト サ レボア語/北ソト語 (南アフリカ)|Latin1_General_100_|使用不可|
|セツワナ語/ツワナ語 (南アフリカ)|Latin1_General_100_|使用不可|
|シンハラ語 (スリランカ)|Indic_General_100_<sup>1</sup>|使用不可|
|スワヒリ語 (ケニア)|Latin1_General_100_|使用不可|
|シリア語 (シリア)|Syriac_100_<sup>1</sup>|Syriac_90_|
|タジク語 (タジキスタン)|Cyrillic_General_100_|使用不可|
|タマジット語 (アルジェリア、ラテン文字)|Tamazight_100_|使用不可|
|タミール語 (インド)|Indic_General_100_<sup>1</sup>|Indic_General_90_|
|テルグ語 (インド)|Indic_General_100_<sup>1</sup>|Indic_General_90_|
|チベット語 (PRC)|Tibetan_100_<sup>1</sup>|使用不可|
|トルクメン語 (トルクメニスタン)|Turkmen_100_|使用不可|
|ウイグル語 (PRC)|Uighur_100_|使用不可|
|上ソルブ語 (ドイツ)|Upper_Sorbian_100_|使用不可|
|ウルドゥー語 (パキスタン)|Urdu_100_|使用不可|
|ウェールズ語 (イギリス)|Welsh_100_|使用不可|
|ウォロフ語 (セネガル)|French_100_|使用不可|
|コサ語 (南アフリカ)|Latin1_General_100_|使用不可|
|サハ語 (ロシア)|Yakut_100_|使用不可|
|イ語 (PRC)|Latin1_General_100_|使用不可|
|ヨルバ語 (ナイジェリア)|Latin1_General_100_|使用不可|
|ズールー語 (南アフリカ)|Latin1_General_100_|使用不可|
|[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降では非推奨とされており、サーバー レベルでは利用できません|ヒンディー語|ヒンディー語|
|[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降では非推奨とされており、サーバー レベルでは利用できません|Korean_Wansung_Unicode|Korean_Wansung_Unicode|
|[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降では非推奨とされており、サーバー レベルでは利用できません|Lithuanian_Classic|Lithuanian_Classic|
|[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降では非推奨とされており、サーバー レベルでは利用できません|マケドニア語|マケドニア語|
||||

<sup>1</sup> Windows の Unicode のみの照合順序は、列レベルまたは式レベルのデータにのみ適用できます。 これらの照合順序は、サーバーまたはデータベースの照合順序としては使用できません。

<sup>2</sup> 中国語 (台湾) の照合順序と同様に、中国語 (マカオ) は簡体中国語の規則を使用し、中国語 (台湾) とは異なるコード ページ 950 を使用します。

## <a name="see-also"></a>参照

- [照合順序と Unicode のサポート](../../relational-databases/collations/collation-and-unicode-support.md)
- [ALTER TABLE](../../t-sql/statements/alter-table-transact-sql.md)
- [定数](../../t-sql/data-types/constants-transact-sql.md)
- [CREATE DATABASE](../../t-sql/statements/create-database-transact-sql.md)
- [CREATE TABLE](../../t-sql/statements/create-table-transact-sql.md)
- [DECLARE @local_variable](../../t-sql/language-elements/declare-local-variable-transact-sql.md)
- [テーブル](../../t-sql/data-types/table-transact-sql.md)
- [sys.fn_helpcollations](../../relational-databases/system-functions/sys-fn-helpcollations-transact-sql.md)
