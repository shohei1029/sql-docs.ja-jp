---
description: インデックス付きビューの作成
title: インデックス付きビューの作成 | Microsoft Docs
ms.custom: ''
ms.date: 11/19/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: table-view-index
ms.topic: conceptual
helpviewer_keywords:
- indexed views [SQL Server], creating
- clustered indexes, views
- CREATE INDEX statement
- large_value_types_out_of_row option
- indexed views [SQL Server]
- views [SQL Server], indexed views
ms.assetid: f86dd29f-52dd-44a9-91ac-1eb305c1ca8d
author: stevestein
ms.author: sstein
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 875d634fd1fc867d59b58872db5ba5dd96dfd52a
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104753192"
---
# <a name="create-indexed-views"></a>インデックス付きビューの作成

[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

この記事では、ビューにインデックスを作成する方法について説明します。 ビューに作成する最初のインデックスは、一意なクラスター化インデックスにする必要があります。 一意のクラスター化インデックスを作成した後は、非クラスター化インデックスを追加で作成できます。 ビューに一意のクラスター化インデックスを作成すると、そのビューは、クラスター化インデックスが定義されているテーブルと同じ方法でデータベースに格納されるので、クエリのパフォーマンスが向上します。 クエリ オプティマイザーではインデックス付きビューを使って、クエリの実行速度を高めることができます。 オプティマイザーでビューを代用するかどうかを判別するために、ビューがクエリで参照されている必要はありません。

## <a name="before-you-begin"></a><a name="BeforeYouBegin"></a> はじめに

次の手順は、インデックス付きビューの作成に必要な手順であり、インデックス付きビューの正常な実装に不可欠です。

1. SET オプションが、ビューで参照されるすべての既存のテーブルに対して正しいことを確認します。
2. テーブルやビューを作成する前に、そのセッション用の SET オプションが正しく設定されていることを確認します。
3. ビュー定義が決定的であることを確認します。
4. ベース テーブルの所有者がビューと同じであることを確認します。
5. `WITH SCHEMABINDING` オプションを使用して、ビューを作成します。
6. ビューに一意のクラスター化インデックスを作成します。

> [!IMPORTANT]
> 多数のインデックス付きビュー、または少数ではあるものの非常に複雑なインデックス付きビューで参照されるテーブルに対して DML<sup>1</sup> を実行する場合、これらの参照されるインデックス付きビューを更新する必要もあります。 その結果、DML クエリのパフォーマンスが大幅に低下する場合があります。また、場合によっては、クエリ プランを生成できないこともあります。
> このようなシナリオでは、運用環境で使用する前に DML クエリをテストし、クエリ プランを分析してから DML ステートメントを調整/簡素化します。
>
> <sup>1</sup> 更新、削除、挿入操作など。

### <a name="required-set-options-for-indexed-views"></a><a name="Restrictions"></a> インデックス付きビューに必要な SET オプション

クエリの実行時、異なる SET オプションがアクティブになっている場合、 [!INCLUDE[ssDE](../../includes/ssde-md.md)] は同じ式を評価しても異なる結果を生成することがあります。 たとえば、SET のオプション `CONCAT_NULL_YIELDS_NULL` を ON に設定すると、式 `'abc' + NULL` は値 `NULL` を返すようになります。 一方、`CONCAT_NULL_YIELDS_NULL` を OFF に設定すると、同じ式を実行が `'abc'` を生成するようになります。

ビューが正しく維持され、一貫性のある結果が返されるようにするには、インデックス付きビューで、いくつかの SET オプションに固定値が必要となります。 固定値の設定が必要な SET オプションと、その値 ( **必要な値** の列を参照) を下の表に示します。この設定は次の条件に該当する場合に常に必要となります:

- ビューが作成され、そのビューのインデックスも作成されている。
- ビューの作成時にビューで参照されるベース テーブル。
- インデックス付きビューに関与するテーブルで実行される挿入、更新、または削除操作がある。 この要件には一括コピー、レプリケーション、分散クエリなどの操作も含まれます。
- クエリ オプティマイザーで、クエリ プランの生成にインデックス付きビューが使用される。

|SET オプション|必須値|既定のサーバー値|Default<br /><br /> OLE DB および ODBC 値|Default<br /><br /> DB-Library 値|
|-----------------|--------------------|--------------------------|---------------------------------------|-----------------------------------|
|ANSI_NULLS|ON|ON|ON|OFF|
|ANSI_PADDING|ON|ON|ON|OFF|
|ANSI_WARNINGS<sup>1</sup>|ON|ON|ON|OFF|
|ARITHABORT|ON|ON|OFF|OFF|
|CONCAT_NULL_YIELDS_NULL|ON|ON|ON|OFF|
|NUMERIC_ROUNDABORT|OFF|OFF|OFF|OFF|
|QUOTED_IDENTIFIER|ON|ON|ON|OFF|
|&nbsp;|&nbsp;|&nbsp;|&nbsp;|&nbsp;|

<sup>1</sup>`ANSI_WARNINGS` を ON に設定すると、暗黙的に `ARITHABORT` が ON に設定されます。

OLE DB または ODBC サーバー接続を使用している場合、変更する必要があるのは `ARITHABORT` 設定の値だけです。 すべての DB-Library 値は、サーバー レベルで **sp_configure** を使用するか、アプリケーションから SET コマンドを使用して、正しく設定する必要があります。

> [!IMPORTANT]
> `ARITHABORT` ユーザー オプションは、そのサーバーのデータベースで初めてインデックス付きビューまたは計算列のインデックスが作成されたときすぐに、サーバー全体で ON に設定することを強くお勧めします。

### <a name="deterministic-views"></a>決定的なビュー

インデックス付きビューの定義は決定的である必要があります。 選択リストのすべての式と、`WHERE` 句および `GROUP BY` 句が決定的である場合、ビューは決定的であるといえます。 決定的な式では、特定の入力値セットで評価するとき常に同じ結果が返されます。 決定的な式には、決定的な関数のみを含めることができます。 たとえば、`DATEADD` 関数は、3 つのパラメーターの任意の引数値セットに対して常に同じ結果を返すため、決定的であるといえます。 `GETDATE` は、常に同じ引数で起動されるにもかかわらず、返す値は実行のたびに変化するため、非決定的であるといえます。

ビュー列が決定的かどうかを判断するには、 **COLUMNPROPERTY** 関数の [IsDeterministic](../../t-sql/functions/columnproperty-transact-sql.md) プロパティを使用します。 スキーマ バインドを含むビューの決定的な列が正確であるかどうかを判断するには、`COLUMNPROPERTY` 関数の **IsPrecise** プロパティを使います。 `COLUMNPROPERTY` は、TRUE の場合は 1、FALSE の場合は 0、有効でない入力に対しては NULL を返します。 これは、列が決定的でないか、正確でないことを表します。

式が決定的でも、浮動小数点式が含まれる場合は、正確な結果はプロセッサのアーキテクチャまたはマイクロコードのバージョンによって異なる可能性があります。 データの整合性を確保するため、このような式は、インデックス付きビューの非キー列としてのみ含めることができます。 浮動小数点式を含まない決定的な式は、正確な式です。 インデックス ビューのキー列と `WHERE` または `GROUP BY` 句には、正確で決定的な式だけを含めることができます。

### <a name="additional-requirements"></a>その他の要件

SET オプションと決定的な関数の要件に加えて、次の要件を満たす必要があります。

- `CREATE INDEX` を実行するユーザーが、ビューの所有者である必要があります。
- インデックスを作成する場合は、`IGNORE_DUP_KEY` オプションを OFF に設定する必要があります (既定の設定)。
- ビュー定義では、 _schema_ **.** _tablename_ という 2 つの部分から構成される名前でテーブルが参照されていること。
- ビューで参照されるユーザー定義関数は、`WITH SCHEMABINDING` オプションを使用して作成する必要があります。
- ビューで参照されるユーザー定義関数は、2 つの部分で構成されている名前 ( _\<schema\>_ **.** _\<function\>_ ) で参照される必要があります。
- ユーザー定義関数のデータ アクセス プロパティが `NO SQL` で、外部アクセス プロパティが `NO` である必要があります。
- 共通言語ランタイム (CLR) 関数をビューの選択リストに使用することはできますが、クラスター化インデックス キーの定義に含めることはできません。 CLR 関数は、ビューの WHERE 句や、ビューの JOIN 操作の ON 句では使用できません。
- ビュー定義で使用する CLR ユーザー定義型の CLR 関数やメソッドは、次の表のようにプロパティが設定されている必要があります。

   |プロパティ|Note|
   |--------------|----------|
   |DETERMINISTIC = TRUE|Microsoft .NET Framework メソッドの属性として、明示的に宣言する必要があります。|
   |PRECISE = TRUE|.NET Framework メソッドの属性として、明示的に宣言する必要があります。|
   |DATA ACCESS = NO SQL|DataAccess 属性を DataAccessKind.None に、SystemDataAccess 属性を SystemDataAccessKind.None に設定することで決定されます。|
   |EXTERNAL ACCESS = NO|CLR ルーチンの場合は、このプロパティの既定値は NO です。|
   |&nbsp;|&nbsp;|

- ビューは、`WITH SCHEMABINDING` オプションを使用して作成する必要があります。
- ビューが、ビューと同じデータベース内のベース テーブルのみを参照していること。 ビューでは、他のビューを参照できません。

- `GROUP BY` が存在する場合、VIEW 定義には `COUNT_BIG(*)` を含める必要があります。`HAVING` を含めることはできません。 このような `GROUP BY` 制限は、インデックス付きビュー定義にのみ適用されます。 クエリがこの `GROUP BY` 制限を満たしていない場合でも、実行プランでインデックス付きビューを使用することはできます。
- ビュー定義に `GROUP BY` 句が含まれている場合、一意のクラスター化インデックスのキーでは、`GROUP BY` 句で指定した列のみを参照できます。

- ビュー定義の SELECT ステートメントには、次の Transact-SQL 要素を使用できません。

   | Transact-SQL の要素 | (続き) | (続き) |
   | --------------------- | ----------- | ----------- |
   |`COUNT`|行セット関数 (`OPENDATASOURCE`、`OPENQUERY`、`OPENROWSET`、`OPENXML`)|`OUTER` 結合 (`LEFT`、`RIGHT`、または `FULL`)|
   |派生テーブル (`FROM` 句で `SELECT` ステートメントを指定することで定義される)|自己結合|`SELECT *` または `SELECT <table_name>.*` を使用して列を指定|
   |`DISTINCT`|`STDEV`、`STDEVP`、`VAR`、`VARP`、または `AVG`|共通テーブル式 (CTE)|
   |**float**<sup>1</sup>、**text**、**ntext**、**image**、**XML**、または **filestream** の列|サブクエリ|順位付け関数または集計関数が含まれている `OVER` 句|
   |フルテキスト述語 (`CONTAINS`、`FREETEXT`)|NULL 値を許容する式を参照する `SUM` 関数|`ORDER BY`|
   |CLR ユーザー定義集計関数|`TOP`|`CUBE`、`ROLLUP`、または `GROUPING SETS` の演算子|
   |`MIN`, `MAX`|`UNION`、`EXCEPT`、または `INTERSECT` の演算子|`TABLESAMPLE`|
   |テーブル変数|`OUTER APPLY` または `CROSS APPLY`|`PIVOT`, `UNPIVOT`|
   |スパース列セット|インライン (TVF) または複数ステートメントのテーブル値関数 (MSTVF)|`OFFSET`|
   |`CHECKSUM_AGG`|`STRING_AGG`|||

   <sup>1</sup> インデックス付きビューには **float** 列を含めることができますが、このような列はクラスター化インデックス キーには含めることができません。

   > [!IMPORTANT]
   > テンポラル クエリ (`FOR SYSTEM_TIME` 句を使うクエリ) 上では、インデックス付きビューはサポートされていません。

### <a name="recommendations"></a><a name="Recommendations"></a> 推奨事項

インデックス付きビューで **datetime** 文字リテラルと **smalldatetime** 文字列リテラルを参照するときは、決定的な日付形式スタイルを使用して、そのリテラルを目的の日付型に明示的に変換することをお勧めします。 決定的な日付形式の一覧については、「[CAST および CONVERT &#40;Transact-SQL&#41;](../../t-sql/functions/cast-and-convert-transact-sql.md)」を参照してください。 決定的な式と非決定的な式の詳細については、このページの「[考慮事項](#nondeterministic)」セクションを参照してください。

多数のインデックス付きビュー、または少数ではあるものの非常に複雑なインデックス付きビューで参照されるテーブルに対して DML (`UPDATE`、`DELETE`、`INSERT` など) を実行する場合、DML 実行時にこれらのインデックス付きビューを更新する必要もあります。 その結果、DML クエリのパフォーマンスが大幅に低下する場合があります。また、場合によっては、クエリ プランを生成できないこともあります。 このようなシナリオでは、運用環境で使用する前に DML クエリをテストし、クエリ プランを分析してから DML ステートメントを調整/簡素化します。

### <a name="considerations"></a><a name="Considerations"></a> 考慮事項

インデックス付きビューの列の **large_value_types_out_of_row** オプションの設定は、ベース テーブルの対応する列の設定が継承されます。 この値は、 [sp_tableoption](../../relational-databases/system-stored-procedures/sp-tableoption-transact-sql.md)を使用して設定します。 式から形成される列に対する既定の設定は 0 です。 つまり、大きい値の型は行内に格納されます。

インデックス付きビューはパーティション分割されたテーブルに作成でき、インデックス付きビュー自体をパーティション分割できます。

[!INCLUDE[ssDE](../../includes/ssde-md.md)] でインデックス付きビューが使用されないようにするには、クエリに `OPTION (EXPAND VIEWS)` ヒントを含めます。 これによって、オプションの 1 つが正しく設定されていない場合、オプティマイザーもビューのインデックスを使用できません。 `OPTION (EXPAND VIEWS)` ヒントの詳細については、「[SELECT &#40;Transact-SQL&#41;」](../../t-sql/queries/select-transact-sql.md)を参照してください。

ビューが削除されると、ビューのすべてのインデックスも削除されます。 クラスター化インデックスが削除されると、ビューのすべての非クラスター化インデックスと自動的に作成された統計も削除されます。 ユーザーが作成したビューの統計は、保持されます。 非クラスター化インデックスは、個別に削除できます。 ビュー上のクラスター化インデックスを削除すると、格納された結果セットも削除され、オプティマイザーは、ビューの処理を標準的なビューと同様の処理に戻します。

テーブルとビューのインデックスは無効にされる可能性があります。 テーブルのクラスター化インデックスが無効になると、そのテーブルに関連するビューのインデックスも無効になります。

<a name="nondeterministic"></a>**datetime** 型または **smalldatetime** 型への文字列の暗黙的な変換が必要な式は非決定的であると見なされます。 詳細については、「[リテラル日付文字列を DATE 値に非決定論的に変換する](../../t-sql/data-types/nondeterministic-convert-date-literals.md)」を参照してください。

### <a name="security"></a><a name="Security"></a> セキュリティ

#### <a name="permissions"></a><a name="Permissions"></a> Permissions

ビューを作成するには、データベースの **CREATE VIEW** アクセス許可と、ビューが作成されているスキーマの **ALTER** アクセス許可が必要です。 ベース テーブルが別のスキーマ内に存在する場合、少なくともそのテーブルに対する **REFERENCES** アクセス許可が必要です。 インデックスを作成するユーザーとビューを作成したユーザーが異なる場合は、インデックスを作成するときに、ビューに対する **ALTER** アクセス許可が必要です (スキーマの ALTER によって処理されます)。

> [!NOTE]  
> インデックスは、所有者が参照先のテーブルと同じビューにのみ作成できます。 これは、ビューとテーブル間の完全な **所有権の継承** とも呼ばれます。 通常、テーブルとビューが同じスキーマ内に存在する場合、そのスキーマ内のすべてのオブジェクトに同じスキーマの所有者が適用されます。 つまり、そのビューの所有者にならずに、ビューを作成することが可能です。 これに対し、スキーマ内の個別のオブジェクトの所有者を明示的に別にすることも可能です。 所有者がスキーマの所有者と異なる場合、sys.tables の列 **principal_id** に値が格納されます。


## <a name="using-transact-sql"></a><a name="TsqlProcedure"></a> Transact-SQL の使用

### <a name="to-create-an-indexed-view"></a>インデックス付きビューを作成するには

次の例では、ビューとそのビューのインデックスを作成します。 AdventureWorks データベースでインデックス付きビューを使用する 2 つのクエリが含まれています。

```sql
--Set the options to support indexed views.
SET NUMERIC_ROUNDABORT OFF;
SET ANSI_PADDING, ANSI_WARNINGS, CONCAT_NULL_YIELDS_NULL, ARITHABORT,
   QUOTED_IDENTIFIER, ANSI_NULLS ON;
--Create view with schemabinding.
IF OBJECT_ID ('Sales.vOrders', 'view') IS NOT NULL
   DROP VIEW Sales.vOrders ;
GO
CREATE VIEW Sales.vOrders
   WITH SCHEMABINDING
   AS  
      SELECT SUM(UnitPrice*OrderQty*(1.00-UnitPriceDiscount)) AS Revenue,
         OrderDate, ProductID, COUNT_BIG(*) AS COUNT
      FROM Sales.SalesOrderDetail AS od, Sales.SalesOrderHeader AS o
      WHERE od.SalesOrderID = o.SalesOrderID
      GROUP BY OrderDate, ProductID;
GO
--Create an index on the view.
CREATE UNIQUE CLUSTERED INDEX IDX_V1
   ON Sales.vOrders (OrderDate, ProductID);
GO
--This query can use the indexed view even though the view is
--not specified in the FROM clause.
SELECT SUM(UnitPrice*OrderQty*(1.00-UnitPriceDiscount)) AS Rev,
   OrderDate, ProductID
FROM Sales.SalesOrderDetail AS od
JOIN Sales.SalesOrderHeader AS o
   ON od.SalesOrderID=o.SalesOrderID
      AND ProductID BETWEEN 700 and 800
      AND OrderDate >= CONVERT(datetime,'05/01/2002',101)
   GROUP BY OrderDate, ProductID
   ORDER BY Rev DESC;
GO
--This query can use the above indexed view.
SELECT OrderDate, SUM(UnitPrice*OrderQty*(1.00-UnitPriceDiscount)) AS Rev
FROM Sales.SalesOrderDetail AS od
JOIN Sales.SalesOrderHeader AS o
   ON od.SalesOrderID=o.SalesOrderID
      AND DATEPART(mm,OrderDate)= 3
      AND DATEPART(yy,OrderDate) = 2002
    GROUP BY OrderDate
    ORDER BY OrderDate ASC;
```

詳細については、「[CREATE VIEW &#40;Transact-SQL&#41;](../../t-sql/statements/create-view-transact-sql.md)」を参照してください。

## <a name="see-also"></a>参照

- [CREATE INDEX](../../t-sql/statements/create-index-transact-sql.md)
- [SET ANSI_NULLS](../../t-sql/statements/set-ansi-nulls-transact-sql.md)
- [SET ANSI_PADDING](../../t-sql/statements/set-ansi-padding-transact-sql.md)
- [SET ANSI_WARNINGS](../../t-sql/statements/set-ansi-warnings-transact-sql.md)
- [SET ARITHABORT](../../t-sql/statements/set-arithabort-transact-sql.md)
- [SET CONCAT_NULL_YIELDS_NULL](../../t-sql/statements/set-concat-null-yields-null-transact-sql.md)
- [SET NUMERIC_ROUNDABORT](../../t-sql/statements/set-numeric-roundabort-transact-sql.md)
- [SET QUOTED_IDENTIFIER](../../t-sql/statements/set-quoted-identifier-transact-sql.md)  
