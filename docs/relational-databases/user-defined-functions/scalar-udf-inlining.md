---
title: Microsoft SQL Server でのスカラー UDF のインライン化 | Microsoft Docs
description: SQL Server (SQL Server 2019 以降) 内でスカラー UDF を呼び出すスカラー UDF インライン化機能を使用すると、クエリのパフォーマンスが向上します。
ms.custom: ''
ms.date: 03/30/2021
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.suite: sql
ms.technology: ''
ms.tgt_pltfrm: ''
ms.topic: conceptual
helpviewer_keywords: ''
ms.assetid: ''
author: s-r-k
ms.author: karam
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 57c9c28cafeb7e55183a3d95194480c62f279e11
ms.sourcegitcommit: 0b37eb7aef2f358f80867cd13830dd6683da8d85
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105981218"
---
# <a name="scalar-udf-inlining"></a>スカラー UDF のインライン化

 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

この記事では、[インテリジェントなクエリ処理](../../relational-databases/performance/intelligent-query-processing.md)機能スイートに含まれる機能であるスカラー UDF のインライン化について説明します。 この機能により、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[sssql19](../../includes/sssql19-md.md)] 以降) でスカラー UDF を呼び出すクエリのパフォーマンスが向上します。

## <a name="t-sql-scalar-user-defined-functions"></a>T-SQL スカラー ユーザー定義関数
[!INCLUDE[tsql](../../includes/tsql-md.md)] で実装されていて単一のデータ値を返すユーザー定義関数は、T-SQL スカラー ユーザー定義関数と呼ばれます。 T-SQL の UDF は、[!INCLUDE[tsql](../../includes/tsql-md.md)] クエリ間でコードの再利用とモジュール性を実現するための洗練された方法です。 一部の計算 (複雑なビジネス ルールなど) は、命令型の UDF 形式で表した方が簡単です。 UDF は、複雑な SQL クエリの作成に関する専門知識を必要とせずに、複雑なロジックを構築するのに役立ちます。 UDF の詳細については、「[ユーザー定義関数の作成 (データベース エンジン)](../../relational-databases/user-defined-functions/create-user-defined-functions-database-engine.md)」を参照してください。

## <a name="performance-of-scalar-udfs"></a>スカラー UDF のパフォーマンス
スカラー UDF を使用すると、通常、次の理由でパフォーマンスが低下します。

- **反復的な呼び出し:** UDF は、該当するタプルごとに 1 回ずつ、反復的な方法で呼び出されます。 このため、関数呼び出しによる反復的なコンテキスト切り替えの追加コストが発生します。 特に、定義内で [!INCLUDE[tsql](../../includes/tsql-md.md)] クエリを実行する UDF は大きな影響を受けます。

- **コスト計算の欠如:** 最適化では、関係演算子のみがコスト計算されて、スカラー演算子はされません。 スカラー UDF が導入される前は、他のスカラー演算子は一般的に低コストであり、コスト計算を必要としませんでした。 スカラー演算用に少し CPU コストを追加すれば十分でした。 実際のコストは大きいのにいまだにコストが低いと認識されているシナリオがあります。

- **解釈形式の実行:** UDF はステートメントのバッチとして評価されて、ステートメントごとに実行します。 各ステートメント自体はコンパイルされて、コンパイル済みのプランがキャッシュされます。 このキャッシュ対策は再コンパイルを回避できるので若干の時間節約になりますが、各ステートメントは別々に実行されます。 クロスステートメントの最適化は実行されません。

- **直列実行:** [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、UDF を呼び出すクエリでクエリ内の並列処理を行うことはできません。 

## <a name="automatic-inlining-of-scalar-udfs"></a>スカラー UDF の自動インライン化
スカラー UDF インライン化機能の目的は、UDF の実行が主なボトルネックになる、T-SQL スカラー UDF を呼び出すクエリのパフォーマンスを向上させることです。

この新しい機能では、スカラー UDF はスカラー式またはスカラー サブクエリに自動的に変換され、呼び出し元のクエリ内で UDF 演算子の代わりに置き換えられます。 その後、これらの式とサブクエリは最適化されます。 その結果、クエリ プランにはユーザー定義関数演算子が含まれなくなりますが、ビューやインライン TVF などのように、その効果はプランに反映されます。

### <a name="example-1---single-statement-scalar-udf"></a>例 1 - 単一ステートメントのスカラー UDF
次のようなクエリを検討します

```sql
SELECT L_SHIPDATE, O_SHIPPRIORITY, SUM (L_EXTENDEDPRICE *(1 - L_DISCOUNT)) 
FROM LINEITEM
INNER JOIN ORDERS
  ON O_ORDERKEY = L_ORDERKEY 
GROUP BY L_SHIPDATE, O_SHIPPRIORITY ORDER BY L_SHIPDATE;
```

このクエリでは、明細品目の割引価格の合計が計算されて、出荷日および出荷優先度でグループ化された結果が表示されます。 式 `L_EXTENDEDPRICE *(1 - L_DISCOUNT)` は、特定の品目の割引価格の式です。 このような式は、モジュール化と再利用のために関数として抽出できます。

```sql
CREATE FUNCTION dbo.discount_price(@price DECIMAL(12,2), @discount DECIMAL(12,2)) 
RETURNS DECIMAL (12,2) AS
BEGIN
  RETURN @price * (1 - @discount);
END
```

そして、この UDF を呼び出すようにクエリを変更できます。

```sql
SELECT L_SHIPDATE, O_SHIPPRIORITY, SUM (dbo.discount_price(L_EXTENDEDPRICE, L_DISCOUNT)) 
FROM LINEITEM
INNER JOIN ORDERS
  ON O_ORDERKEY = L_ORDERKEY 
GROUP BY L_SHIPDATE, O_SHIPPRIORITY ORDER BY L_SHIPDATE
```

先に説明した理由により、UDF を使用するクエリはパフォーマンスが低下します。 ここで、スカラー UDF のインライン化を使用すると、UDF の本体のスカラー式はクエリ内で直接置き換えられます。 このクエリの実行結果は次の表のようになります。

| クエリ: | UDF なしのクエリ | UDF ありのクエリ (インライン化なし) | スカラー UDF インライン化ありのクエリ | 
| --- | --- | --- | --- |
| 実行時間: | 1.6 秒 | 29 分 11 秒 | 1.6 秒 |

これらの値は、10 GB の CCI データベース (TPC-H スキーマを使用) を使用し、デュアル プロセッサ (12 コア)、96 GB の RAM、SSD を備えたコンピューターで実行した場合のものです。 値には、コールド プロシージャ キャッシュとバッファー プールを使用したコンパイルと実行の時間が含まれます。 既定の構成が使用され、他のインデックスは作成されませんでした。

### <a name="example-2---multi-statement-scalar-udf"></a>例 2 - 複数ステートメントのスカラー UDF
変数の代入や条件分岐など、複数の T-SQL ステートメントを使用して実装されるスカラー UDF もインライン展開できます。 カスタマー キーを指定されて、その顧客のサービス カテゴリを決定する、次のようなスカラー UDF について考えます。 カテゴリを取得するには、最初に、SQL クエリを使用して、顧客による全注文の総額を計算します。 次に、`IF (...) ELSE` ロジックを使用して、総額に基づいてカテゴリを決定します。

```sql
CREATE OR ALTER FUNCTION dbo.customer_category(@ckey INT) 
RETURNS CHAR(10) AS
BEGIN
  DECLARE @total_price DECIMAL(18,2);
  DECLARE @category CHAR(10);

  SELECT @total_price = SUM(O_TOTALPRICE) FROM ORDERS WHERE O_CUSTKEY = @ckey;

  IF @total_price < 500000
    SET @category = 'REGULAR';
  ELSE IF @total_price < 1000000
    SET @category = 'GOLD';
  ELSE 
    SET @category = 'PLATINUM';

  RETURN @category;
END
```

ここで、この UDF を呼び出すクエリを検討します。

```sql
SELECT C_NAME, dbo.customer_category(C_CUSTKEY) FROM CUSTOMER;
```

[!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] (互換性レベル 140 およびそれ以前) でのこのクエリの実行プランは次のようになります。

![インライン化のないクエリ プラン](./media/query-plan-without-udf-inlining.png)

プランで示されているように、ここでは [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] はシンプルな戦略を採用しています。`CUSTOMER` テーブル内のすべてのタプルについて、UDF を呼び出して結果を出力します。 この方法は単純で非効率的です。 インライン化を使用すると、このような UDF は同等のスカラー サブクエリに変換されて、呼び出し元のクエリで UDF の代わりに置き換えられます。

同じクエリに対し、UDF のインライン化を使用したプランは次のようになります。

![インライン化のあるクエリ プラン](./media/query-plan-with-udf-inlining.png)

前に説明したように、クエリ プランにはユーザー定義関数演算子が含まれなくなりますが、ビューやインライン TVF などのように、その効果はプランにおいて確認できます。 上のプランで確認できる重要な点をいくつか示します。

-  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] により、`CUSTOMER` と `ORDERS` の間に暗黙の結合が推論され、それが結合演算子によって明示化されています。
-  また、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] によって暗黙の `GROUP BY O_CUSTKEY on ORDERS` が推論され、IndexSpool と StreamAggregate を使用して実装されています。
-  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、すべての演算子で並列処理が使用されるようになっています。

UDF 内のロジックの複雑さによっては、結果として得られるクエリ プランがさらに大きくて複雑になる可能性があります。 このように、UDF の内部の演算が不透明なボックスではなくなっており、そのため、クエリ オプティマイザーでコストを計算でき、これらの演算を最適化できます。 また、UDF がプランに含まれなくなったため、反復的な UDF の呼び出しは、関数呼び出しのオーバーヘッドがまったくないプランに置き換えられています。

## <a name="inlineable-scalar-udfs-requirements"></a>インライン化可能なスカラー UDF の要件
<a name="requirements"></a> 以下のすべての条件に該当する場合、スカラー T-SQL UDF はインライン化できます。

- UDF が、次のコンストラクトを使用して書かれている。
    - `DECLARE`、`SET`:変数の宣言と代入。
    - `SELECT`:単一または複数の変数代入を含む SQL クエリ <sup>1</sup>。
    - `IF`/`ELSE`:任意の入れ子レベルでの分岐。
    - `RETURN`:1 つまたは複数の return ステートメント。
    - `UDF`:入れ子になった、または再帰関数呼び出し <sup>2</sup>。
    - その他:`EXISTS`、`ISNULL` などの関係演算。
- UDF で、時間に依存する組み込み関数 (`GETDATE()` など) または副作用のある組み込み関数 <sup>3</sup> (`NEWSEQUENTIALID()` など) が呼び出されていない。
- UDF で、`EXECUTE AS CALLER` 句が使用されている (`EXECUTE AS` 句が指定されていない場合の既定の動作)。
- UDF で、テーブル変数またはテーブル値パラメーターが参照されていない。
- スカラー UDF を呼び出すクエリの `GROUP BY` 句で、スカラー UDF 呼び出しが参照されていない。
- `DISTINCT` 句でその選択リストのスカラー UDF を呼び出すクエリには、`ORDER BY` 句は含まれません。
- UDF は `ORDER BY` 句では使用されません。
- UDF がネイティブでコンパイルされていない (相互運用機能はサポートされます)。
- UDF が、計算列または CHECK 制約定義で使用されていない。
- UDF で、ユーザー定義型が参照されていない。
- UDF にシグネチャが追加されていない。
- UDF がパーティション関数ではない。
- UDF に、共通テーブル式 (CTE) への参照が含まれていない。
- UDF に、インライン化されると結果が変わる可能性がある組み込み関数 (`@@ROWCOUNT` など) への参照が含まれていない <sup>4</sup>。
- UDF に、パラメーターとしてスカラー UDF に渡される集計関数が含まれていない <sup>4</sup>。
- UDF で、組み込みのビュー (`OBJECT_ID` など) が参照されていない <sup>4</sup>。
- UDF で、XML メソッドが参照されていない <sup>5</sup>。
- UDF に、`TOP 1` 句のない `ORDER BY` が含まれている SELECT が含まれていない <sup>5</sup>。
- UDF に、`ORDER BY` 句 (`SELECT @x = @x + 1 FROM table1 ORDER BY col1` など) と組み合わせて割り当てを実行する SELECT クエリが含まれていない <sup>5</sup>。
- UDF に、複数の RETURN ステートメントが含まれていない <sup>6</sup>。
- UDF が、RETURN ステートメントから呼び出されていない <sup>6</sup>。
- UDF で、`STRING_AGG` 関数が参照されていない <sup>6</sup>。 
- UDF で、リモート テーブルが参照されていない <sup>7</sup>。
- UDF 呼び出しクエリで、`GROUPING SETS`、`CUBE`、または `ROLLUP` が使用されない <sup>7</sup>。
- UDF 呼び出しクエリで、割り当て用の UDF パラメーターとして使用される変数が含まれない (たとえば、`SELECT @y = 2`、`@x = UDF(@y)`) <sup>7</sup>。

<sup>1</sup> 変数の累積/集計を含む `SELECT` (`SELECT @val += col1 FROM table1` など) は、インライン化ではサポートされていません。

<sup>2</sup> 再帰的な UDF は、特定の深さまでのみインライン化されます。

<sup>3</sup> 結果が現在のシステム時刻によって異なる組み込み関数は、時間に依存します。 内部のグローバル状態を更新する場合がある組み込み関数は、副作用のある関数の例です。 このような関数は、内部の状態に基づいて、呼び出されるたびに異なる結果を返します。

<sup>4</sup> [!INCLUDE[sql-server-2019](../../includes/sssql19-md.md)] CU2 に制限を追加

<sup>5</sup> [!INCLUDE[sql-server-2019](../../includes/sssql19-md.md)] CU4 に制限を追加

<sup>6</sup> [!INCLUDE[sql-server-2019](../../includes/sssql19-md.md)] CU5 に制限を追加

<sup>7</sup> [!INCLUDE[sql-server-2019](../../includes/sssql19-md.md)] CU6 に制限を追加

> [!NOTE]
> 最新の T-SQL スカラー UDF のインライン化の修正とインライン化の資格シナリオの変更については、サポート技術情報の記事を参照してください。[修正: SQL Server 2019 のスカラー UDF のインライン化の問題](https://support.microsoft.com/help/4538581)。

### <a name="checking-whether-or-not-a-udf-can-be-inlined"></a>UDF をインライン化できるかどうかの確認
すべての T-SQL スカラー UDF について、[sys.sql_modules](../system-catalog-views/sys-sql-modules-transact-sql.md) カタログ ビューに `is_inlineable` という名前のプロパティが含まれており、これは UDF がインライン化可能かどうかを示します。 

> [!NOTE]
> `is_inlineable` プロパティは、UDF 定義内にあるコンストラクトから派生します。 コンパイル時に UDF が実際にインライン化可能かどうかは確認されません。 詳細については、[インライン化の条件](#requirements)を参照してください。

値 1 はインライン化可能であることを示し、0 はそれ以外を示します。 すべてのインライン TVF についても、このプロパティの値は 1 になります。 他のすべてのモジュールでは、値は 0 になります。

スカラー UDF がインライン化可能であっても、常にインライン化されるという意味ではありません。 UDF をインライン化するかどうかは、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] によって (クエリごと、UDF ごとに) 決定されます。 UDF をインライン化できない場合のいくつかの例を次に示します。

-  UDF の定義が数千行のコードになる場合、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] はインライン化しないことを選択する可能性があります。 
-  `GROUP BY` 句での UDF 呼び出しはインライン化されません。 この決定は、スカラー UDF を参照しているクエリのコンパイル時に行われます。
-  UDF が証明書で署名されている場合。 UDF が作成された後に署名を追加および削除できるため、スカラー UDF を参照するクエリをコンパイルするときに、インライン化するかどうかの決定が行われます。 たとえば、システム関数は通常、証明書で署名されます。 [sys. crypt_properties](../../relational-databases/system-catalog-views/sys-crypt-properties-transact-sql.md) を使用して、署名されているオブジェクトを見つけることができます。 

   ```sql
   SELECT * 
   FROM sys.crypt_properties AS cp
   INNER JOIN sys.objects AS o ON cp.major_id = o.object_id;
   ```

### <a name="checking-whether-inlining-has-happened-or-not"></a>インライン化が行われたかどうかの確認
すべての前提条件が満たされていて、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] がインライン化の実行を決定した場合、UDF は関係式に変換されます。 インライン化が行われたかどうかは、クエリ プランを見ると簡単にわかります。

- インライン化が正常に行われた UDF のプランの xml には、`<UserDefinedFunction>` xml ノードが含まれません。 
- 特定の XEvent が生成されています。

## <a name="enabling-scalar-udf-inlining"></a>スカラー UDF のインライン化を有効にする
データベースに対して互換性レベル 150 を有効にすることで、自動的にワークロードをスカラー UDF インライン化の対象にすることができます。 これは [!INCLUDE[tsql](../../includes/tsql-md.md)] を使って設定できます。 次に例を示します。  

```sql
ALTER DATABASE [WideWorldImportersDW] SET COMPATIBILITY_LEVEL = 150;
```

この機能を利用するために、UDF またはクエリに対して、これ以外に行う必要のある他の変更はありません。

## <a name="disabling-scalar-udf-inlining-without-changing-the-compatibility-level"></a>互換性レベルを変更せずに、スカラー UDF のインライン化を無効にする
スカラー UDF のインライン化は、データベースの互換性レベルを 150 以上に維持しながら、データベース、ステートメント、または UDF の範囲で、無効にすることができます。 データベースの範囲でスカラー UDF のインライン化を無効にするには、該当するデータベースのコンテキスト内で、次のステートメントを実行します。 

```sql
ALTER DATABASE SCOPED CONFIGURATION SET TSQL_SCALAR_UDF_INLINING = OFF;
```

データベースに対してスカラー UDF のインライン化を再び有効にするには、該当するデータベースのコンテキスト内で、次のステートメントを実行します。

```sql
ALTER DATABASE SCOPED CONFIGURATION SET TSQL_SCALAR_UDF_INLINING = ON;
```

ON のとき、この設定は [`sys.database_scoped_configurations`](../system-catalog-views/sys-database-scoped-configurations-transact-sql.md) で有効として表示されます。 `USE HINT` クエリ ヒントとして `DISABLE_TSQL_SCALAR_UDF_INLINING` を指定することで、特定のクエリについてスカラー UDF のインライン化を無効にすることもできます。 次に例を示します。

```sql
SELECT L_SHIPDATE, O_SHIPPRIORITY, SUM (dbo.discount_price(L_EXTENDEDPRICE, L_DISCOUNT)) 
FROM LINEITEM
INNER JOIN ORDERS
  ON O_ORDERKEY = L_ORDERKEY 
GROUP BY L_SHIPDATE, O_SHIPPRIORITY ORDER BY L_SHIPDATE
OPTION (USE HINT('DISABLE_TSQL_SCALAR_UDF_INLINING'));
```

> [!TIP]
> `USE HINT` クエリ ヒントは、データベース スコープの構成または互換性レベルの設定より優先されます。

`CREATE FUNCTION` または `ALTER FUNCTION` ステートメントで INLINE 句を使用して、特定の UDF についてスカラー UDF のインライン化を無効にすることもできます。
次に例を示します。

```sql
CREATE OR ALTER FUNCTION dbo.discount_price(@price DECIMAL(12,2), @discount DECIMAL(12,2))
RETURNS DECIMAL (12,2)
WITH INLINE = OFF
AS
BEGIN
    RETURN @price * (1 - @discount);
END;
```

上のステートメントが実行されると、この UDF はそれを呼び出すクエリにインライン化されなくなります。 この UDF のインライン化を再度有効にするには、次のステートメントを実行します。

```sql
CREATE OR ALTER FUNCTION dbo.discount_price(@price DECIMAL(12,2), @discount DECIMAL(12,2))
RETURNS DECIMAL (12,2)
WITH INLINE = ON
AS
BEGIN
    RETURN @price * (1 - @discount);
END
```

> [!NOTE]
> `INLINE` 句は必須ではありません。 `INLINE` 句を指定しないと、UDF をインライン化できるかどうかに基づいて、自動的に `ON`/`OFF` が設定されます。 `INLINE = ON` が指定されていても、UDF がインライン化の条件を満たしていないと、エラーがスローされます。

## <a name="important-notes"></a>重要な注意点
この記事で説明したように、スカラー UDF のインライン化では、スカラー UDF を含むクエリが、同等のスカラー サブクエリを含むクエリに変換されます。 この変換のため、次のようなシナリオでは、示される動作が異なる場合があります。

1. クエリ テキストが同じでも、インライン化が行われるとクエリ ハッシュが異なります。
1. 以前は表示されなかった UDF 内のステートメントでの特定の警告 (0 による除算など) が、インライン化によって表示されるようになる場合があります。
1. インライン化によって新しい結合が導入される場合があるため、クエリ レベルの結合ヒントが有効ではなくなる可能性があります。 代わりに、ローカル結合ヒントを使用する必要があります。
1. インライン スカラー UDF を参照するビューに、インデックスを付けることはできません。 そのようなビューにインデックスを付ける必要がある場合は、参照されている UDF のインライン化を無効にします。
1. UDF をインライン化すると、[動的データ マスク](../security/dynamic-data-masking.md)の動作が変化する可能性があります。 特定の状況では (UDF のロジックに応じて)、出力列のマスキングに関してインライン化がより控え目になる場合があります。 UDF で参照されている列が出力列ではない場合、それらはマスクされません。 
1. UDF で `SCOPE_IDENTITY()`、`@@ROWCOUNT`、`@@ERROR` などの組み込み関数が参照されている場合、組み込み関数によって返される値はインライン化によって変化します。 このような動作の変化は、UDF 内のステートメントのスコープがインライン化によって変化するためです。 [!INCLUDE[sql-server-2019](../../includes/sssql19-md.md)] CU2 以降では、UDF で特定の組み込み関数 (`@@ROWCOUNT` など) が参照される場合、インライン化はブロックされます。

## <a name="see-also"></a>参照
[ユーザー定義関数の作成 (データベース エンジン)](../../relational-databases/user-defined-functions/create-user-defined-functions-database-engine.md)   
[SQL Server データベース エンジンと Azure SQL Database のパフォーマンス センター](../../relational-databases/performance/performance-center-for-sql-server-database-engine-and-azure-sql-database.md)     
[クエリ処理アーキテクチャ ガイド](../../relational-databases/query-processing-architecture-guide.md)     
[プラン表示の論理操作と物理操作のリファレンス](../../relational-databases/showplan-logical-and-physical-operators-reference.md)     
[結合](../../relational-databases/performance/joins.md)     
[インテリジェントなクエリ処理のデモ](https://aka.ms/IQPDemos)     
[修正: SQL Server 2019 のスカラー UDF のインライン化の問題](https://support.microsoft.com/help/4538581)     

