---
description: CREATE TABLE (Azure Synapse Analytics)
title: CREATE TABLE (Azure Synapse Analytics) | Microsoft Docs
ms.custom: ''
ms.date: 03/30/2021
ms.service: sql-data-warehouse
ms.reviewer: ''
ms.topic: reference
dev_langs:
- TSQL
ms.assetid: ea21c73c-40e8-4c54-83d4-46ca36b2cf73
author: julieMSFT
ms.author: jrasnick
monikerRange: '>= aps-pdw-2016 || = azure-sqldw-latest'
ms.openlocfilehash: 0744d4859c8895283f26a4ab01e8b915ba72de3b
ms.sourcegitcommit: 295b9dfc758471ef7d238a2b0f92f93e34acbb1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106054773"
---
# <a name="create-table-azure-synapse-analytics"></a>CREATE TABLE (Azure Synapse Analytics)

[!INCLUDE[applies-to-version/asa-pdw](../../includes/applies-to-version/asa-pdw.md)]

  [!INCLUDE[ssSDW](../../includes/sssdw-md.md)] または [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] で新しいテーブルを作成します。  

テーブルについて、またテーブルの使用方法について理解するには、[[!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] のテーブル](https://azure.microsoft.com/documentation/articles/sql-data-warehouse-tables-overview/)に関するページを参照してください。

> [!NOTE]
>  この記事の [!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] に関する解説は、特に記載のない限り、[!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] と [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] の両方に適用されます。

[!INCLUDE[synapse-analytics-od-supported-tables](../../includes/synapse-analytics-od-supported-tables.md)]

 ![記事リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "記事リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  

<a name="Syntax"></a>

## <a name="syntax"></a>構文
  
```syntaxsql
-- Create a new table.
CREATE TABLE { database_name.schema_name.table_name | schema_name.table_name | table_name }
    ( 
      { column_name <data_type>  [ <column_options> ] } [ ,...n ]
    )  
    [ WITH ( <table_option> [ ,...n ] ) ]  
[;]  

<column_options> ::=
    [ COLLATE Windows_collation_name ]
    [ NULL | NOT NULL ] -- default is NULL
    [ IDENTITY [ ( seed, increment ) ]
    [ <column_constraint> ]

<column_constraint>::=
    {
        DEFAULT constant_expression
        | PRIMARY KEY NONCLUSTERED NOT ENFORCED -- Applies to Azure Synapse Analytics only
        | UNIQUE NOT ENFORCED -- Applies to Azure Synapse Analytics only
    }

<table_option> ::=
    {
       CLUSTERED COLUMNSTORE INDEX -- default for Azure Synapse Analytics 
      | CLUSTERED COLUMNSTORE INDEX ORDER (column [,...n])  
      | HEAP --default for Parallel Data Warehouse
      | CLUSTERED INDEX ( { index_column_name [ ASC | DESC ] } [ ,...n ] ) -- default is ASC
    }  
    {
        DISTRIBUTION = HASH ( distribution_column_name )
      | DISTRIBUTION = ROUND_ROBIN -- default for Azure Synapse Analytics
      | DISTRIBUTION = REPLICATE -- default for Parallel Data Warehouse
    }
    | PARTITION ( partition_column_name RANGE [ LEFT | RIGHT ] -- default is LEFT  
        FOR VALUES ( [ boundary_value [,...n] ] ) )

<data type> ::=
      datetimeoffset [ ( n ) ]  
    | datetime2 [ ( n ) ]  
    | datetime  
    | smalldatetime  
    | date  
    | time [ ( n ) ]  
    | float [ ( n ) ]  
    | real [ ( n ) ]  
    | decimal [ ( precision [ , scale ] ) ]   
    | numeric [ ( precision [ , scale ] ) ]   
    | money  
    | smallmoney  
    | bigint  
    | int   
    | smallint  
    | tinyint  
    | bit  
    | nvarchar [ ( n | max ) ]  -- max applies only to Azure Synapse Analytics 
    | nchar [ ( n ) ]  
    | varchar [ ( n | max )  ] -- max applies only to Azure Synapse Analytics  
    | char [ ( n ) ]  
    | varbinary [ ( n | max ) ] -- max applies only to Azure Synapse Analytics  
    | binary [ ( n ) ]  
    | uniqueidentifier  
```  

<a name="Arguments"></a>
## <a name="arguments"></a>引数

 *database_name*  
 新しいテーブルを格納するデータベースの名前です。 既定値は現在のデータベースです。  
  
 *schema_name*  
 テーブルのスキーマです。 "*スキーマ*" の指定は省略可能です。 空白の場合は、既定のスキーマが使用されます。  
  
 *table_name*  
 新しいテーブルの名前です。 ローカルの一時テーブルを作成するには、テーブル名の先頭に # を付けます。  一時テーブルの説明とガイダンスについては、[[!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] の一時テーブル](https://azure.microsoft.com/documentation/articles/sql-data-warehouse-tables-temporary/)に関するページを参照してください。 

 *column_name*  
 テーブルの列の名前です。

### <a name="column-options"></a><a name="ColumnOptions"></a> 列のオプション

 `COLLATE` *Windows_collation_name*  
 式の照合順序を指定します。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] でサポートされている Windows 照合順序のいずれかを指定する必要があります。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] でサポートされている Windows 照合順序の一覧については、「[Windows 照合順序名 (Transact-SQL)](windows-collation-name-transact-sql.md)」を参照してください。  
  
 `NULL` | `NOT NULL`  
 列で `NULL` 値を許可するかどうかを指定します。 既定では、 `NULL`です。  
  
 [ `CONSTRAINT` *constraint_name* ] `DEFAULT` *constant_expression*  
 列の既定値を指定します。  
  
 | 引数 | 説明 |
 | -------- | ----------- |
 | *constraint_name* | (省略可能) 制約の名前です。 制約名は、データベース内で一意です。 名前は、他のデータベース内で再利用できます。 |
 | *constant_expression* | 列の既定値です。 式は、リテラル値または定数である必要があります。 たとえば、`'CA'` や `4` などの定数式は許可されます。 `2+3` や `CURRENT_TIMESTAMP` などの定数式は許可されません。 |
  
### <a name="table-structure-options"></a><a name="TableOptions"></a> テーブル構造のオプション

テーブルの種類を選択する方法の詳細については、[[!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] でのテーブルのインデックス作成](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-index/)に関するページを参照してください。
  
 `CLUSTERED COLUMNSTORE INDEX` 
 
クラスター化列ストア インデックスとしてテーブルを格納します。 クラスター化列ストア インデックスは、テーブルのすべてのデータに適用されます。 この動作は [!INCLUDE[ssSDW](../../includes/sssdw-md.md)] の既定の動作です。
 
 `HEAP` テーブルをヒープとして格納します。 この動作は [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] の既定の動作です。  
  
 `CLUSTERED INDEX` ( *index_column_name* [ ,...*n* ] )  
 1 つまたは複数のキー列を含むクラスター化インデックスとしてテーブルを格納します。 この動作は、行によってデータを格納します。 *index_column_name* を使用して、インデックスに 1 つまたは複数のキー列の名前を指定します。  詳細については、「全般的な解説」の「行ストア テーブル」を参照してください。
 
 `LOCATION = USER_DB` このオプションは非推奨です。 この構文は容認されますが、現在は不要であり、動作にも影響しません。   
  
### <a name="table-distribution-options"></a><a name="TableDistributionOptions"></a> テーブル分散オプション

最適な分散メソッドを選択する方法および分散テーブルを使用する方法を理解するには、[[!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] でテーブルを分散する](https://azure.microsoft.com/documentation/articles/sql-data-warehouse-tables-distribute/)方法に関するページを参照してください。

`DISTRIBUTION = HASH` ( *distribution_column_name* ) *distribution_column_name* に格納された値をハッシュすることにより、各行を 1 つの分散に割り当てます。 これは決定的アルゴリズムであり、常に同じ値が同じ分散にハッシュされることを意味します。  NULL を持つ行はすべて同じディストリビューションに割り当てられるので、ディストリビューション列は NOT NULL として定義する必要があります。

`DISTRIBUTION = ROUND_ROBIN` 行をラウンド ロビン方式ですべてのディストリビューションに均等に分散させます。 この動作は [!INCLUDE[ssSDW](../../includes/sssdw-md.md)] の既定の動作です。

`DISTRIBUTION = REPLICATE` テーブルの 1 つのコピーを各コンピューティング ノードに格納します。 [!INCLUDE[ssSDW](../../includes/sssdw-md.md)] の場合、テーブルは各コンピューティング ノード上のディストリビューション データベースに格納されます。 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] の場合、テーブルはコンピューティング ノードにまたがる [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ファイルグループに格納されます。 この動作は [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] の既定の動作です。
  
### <a name="table-partition-options"></a><a name="TablePartitionOptions"></a>テーブル パーティションのオプション
テーブル パーティションの使用の詳細については、[[!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] でのテーブルのパーティション分割](https://azure.microsoft.com/documentation/articles/sql-data-warehouse-tables-partition/)に関するページを参照してください。

 `PARTITION` ( *partition_column_name* `RANGE` [ `LEFT` | `RIGHT` ] `FOR VALUES` ( [ *boundary_value* [,...*n*] ] ))   
1 つまたは複数のテーブルのパーティションを作成します。 これらのパーティションは横方向のテーブル スライスであり、テーブルがヒープ、クラスター化インデックス、またはクラスター化列ストア インデックスのいずれで格納されているかに関係なく、行のサブセットに対して操作を適用できます。 ディストリビューション列とは異なり、テーブルのパーティションは、各行が格納されるディストリビューションを決定しません。 代わりに、テーブルのパーティションでは、行をグループ化して、各ディストリビューション内に格納する方法が決定されます。  

| 引数 | 説明 |
| -------- | ----------- |
|*partition_column_name*| [!INCLUDE[ssSDW](../../includes/sssdw-md.md)] で行をパーティション分割するのに使用する列を指定します。 この列は、どのようなデータ型でもかまいません。 [!INCLUDE[ssSDW](../../includes/sssdw-md.md)] では、パーティション列の値が昇順に並べ替えられます。 小さい順に並べる場合は、`RANGE` 指定の `LEFT` から `RIGHT` に並べられます。 |  
| `RANGE LEFT` | 左側 (値が小さくなっていく) のパーティションに属する境界値を指定します。 既定値は LEFT です。 |
| `RANGE RIGHT` | 左側 (値が大きくなっていく) のパーティションに属する境界値を指定します。 | 
| `FOR VALUES` ( *boundary_value* [,...*n*] ) | パーティションの境界値を指定します。 *boundary_value* は定数式です。 NULL にすることはできません。 また、*partition_column_name* のデータ型に一致するか、またはそのデータ型に暗黙的に変換可能である必要があります。 さらに、暗黙的な変換時には切り詰めることができないため、値のサイズおよびスケールは *partition_column_name* のデータ型と一致しません。<br></br><br></br>`PARTITION` 句は指定したが、境界値を指定しない場合、[!INCLUDE[ssSDW](../../includes/sssdw-md.md)] ではパーティションを 1 つ含むパーティション テーブルが作成されます。 必要に応じて、後でテーブルを 2 つのパーティションに分割できます。<br></br><br></br>境界値を 1 つ指定した場合、生成されるテーブルには 2 つのパーティションが含まれます。境界値よりも小さい値に対するパーティションと、境界値よりも大きい値に対するパーティションです。 非パーティション テーブルにパーティションを移動した場合、非パーティション テーブルはデータを受け取りますが、そのメタデータ内にパーティション境界は設定されません。| 

 「例」セクションの「[パーティション テーブルの作成](#PartitionedTable)」を参照してください。

### <a name="ordered-clustered-columnstore-index-option"></a>クラスター化列ストア インデックスの順序付けオプション 

クラスター化列ストア インデックス (CCI) は、[!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] でテーブルを作成するための既定値です。  CCI 内のデータは、列ストアセグメントに圧縮される前に並べ替えられることはありません。  ORDER を指定して CCI を作成すると、データは並べ替えられてからインデックス セグメントに追加されるため、パフォーマンスが向上することがあります。 詳細については、「[順序指定クラスター化列ストア インデックスを使用したパフォーマンス チューニング](/azure/sql-data-warehouse/performance-tuning-ordered-cci?view=azure-sqldw-latest&preserve-view=true)」を参照してください。  

順序付けされた CCI は、文字列型の列を除く [!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] でサポートされている任意のデータ型の列に作成できます。  

ユーザーは、**sys.index_columns** の **column_store_order_ordinal** 列に対して、テーブルが順序付けられている列とその順序でクエリを実行できます。  

詳細については、「[順序指定クラスター化列ストア インデックスを使用したパフォーマンス チューニング](/azure/sql-data-warehouse/performance-tuning-ordered-cci)」を参照してください。   

### <a name="data-type"></a><a name="DataTypes"></a> データ型

[!INCLUDE[ssSDW](../../includes/sssdw-md.md)] では、最も一般的に使用されるデータ型をサポートしています。 以下に、サポートされるデータ型を一覧し、その詳細説明および格納バイトを示します。 データ型とその使用方法をよく理解するには、[[!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] のテーブルのデータ型](https://azure.microsoft.com/documentation/articles/sql-data-warehouse-tables-data-types)に関するページを参照してください。

データ型変換のテーブルについては、「[CAST および CONVERT (Transact-SQL)](../functions/cast-and-convert-transact-sql.md)」の「暗黙的な変換」セクションを参照してください。

>[!NOTE]
>詳細については、「[日付と時刻のデータ型および関数&#40;Transact-SQL&#41;](../functions/date-and-time-data-types-and-functions-transact-sql.md)」を参照してください。

`datetimeoffset` [ ( *n* ) ]  
 *n* の既定値は 7 です。  
  
 `datetime2` [ ( *n* ) ]  
`datetime` と同じ。ただし、秒の小数部の値を指定することができます。 *n* の既定値は `7` です。  
  
|*n* 値|Precision|スケール|  
|--:|--:|-:|  
|`0`|19|0|  
|`1`|21|1|  
|`2`|22|2|  
|`3`|23|3|  
|`4`|24|4|  
|`5`|25|5|  
|`6`|26|6|  
|`7`|27|7|  
  
 `datetime`  
 グレゴリオ暦カレンダーに従って、19 から 23 文字で日の日付と時刻を格納します。 日付には、年、月、日を含めることができます。 時刻には、時、分、秒が含まれます。オプションで、秒の小数部の 3 桁の数字を表示できます。 ストレージ サイズは 8 バイトです。  
  
 `smalldatetime`  
 日付と時刻を格納します。 ストレージ サイズは 4 バイトです。  
  
 `date`  
 グレゴリオ暦カレンダーに従い、最大 10 文字の年、月、日を使用して日付を格納します。 ストレージ サイズは 3 バイトです。 日付は、整数として保存されます。  
  
 `time` [ ( *n* ) ]  
 *n* の既定値は `7` です。  
  
 `float` [ ( *n* ) ]  
 浮動小数点数値データで使用するための概数値のデータ型です。 浮動小数点データは概数であるため、データ型の範囲に含まれるすべての値を正確に表せるわけではありません。 *n* では、`float` の仮数を指数表記で格納するために使用するビット数を指定します。 *n* によって、有効桁数と記憶域のサイズが決まります。 *n* を指定する場合、`1` から `53` までの値にする必要があります。 *n* の既定値は `53` です。  
  
| *n* 値 | Precision | ストレージ サイズ |  
| --------: | --------: | -----------: |  
| 1 から 24   | 7 桁  | 4 バイト      |  
| 25 から 53  | 15 桁 | 8 バイト      |  
  
 [!INCLUDE[ssSDW](../../includes/sssdw-md.md)] では、*n* は次の 2 つの値のいずれかの値として扱われます。 `1`<= *n* <= `24` の場合、*n* は `24` として処理されます。 `25` <= *n* <= `53` の場合、*n* は `53` として処理されます。  
  
 [!INCLUDE[ssSDW](../../includes/sssdw-md.md)] `float` データ型は、*n* が `1` から `53` の値をとるすべてのケースにおいて ISO 標準に準拠しています。 倍精度のシノニムは `float(53)` です。  
  
 `real` [ ( *n* ) ]  
 実数の定義は、浮動小数点数と同じです。 `real` の ISO シノニムは、`float(24)` です。  
  
 `decimal` [ ( *precision* [ *, scale* ] ) ] | `numeric` [ ( *precision* [ *, scale* ] ) ]  
 固定長の有効桁数と小数点以下保持桁数を持つ数値を格納します。  
  
 *有効桁数 (precision)*  
 小数点の右側と左側に保持できる桁数を合計した、10 進数の最大桁数。 有効桁数の値は、`1` ～ `38` (最大有効桁数) にする必要があります。 既定の有効桁数は `18` です。  
  
 *scale*  
 小数点の右側にとることのできる 10 進数の最大桁数。 *Scale* は、`0` ～ *precision* とする必要があります。 *scale* を指定できるのは、*precision* が指定されている場合のみです。 既定の scale は`0` です。したがって、`0` <= *scale* <= *precision* となります。 ストレージの最大サイズは有効桁数によって異なります。  
  
| Precision | ストレージのバイト数  |  
| ---------: |-------------: |  
|  1-9       |             5 |  
| 10 から 19      |             9 |  
| 20 から 28      |            13 |  
| 29 から 38      |            17 |  
  
 `money` | `smallmoney`  
 通貨値を表すデータ型。  
  
| データ型 | ストレージのバイト数 |  
| --------- | ------------: |  
| `money`|8|  
| `smallmoney` |4|  
  
 `bigint` \| `int` \| `smallint` \| `tinyint`  
 整数データを使用する実数データ型です。 次の表では記憶域を示します。  
  
| データ型 | ストレージのバイト数 |  
| --------- | ------------: |  
| `bigint`|8|  
| `int` |4|  
| `smallint` |2|  
| `tinyint` |1|  
  
 `bit`  
 `1`、`0`、または `NULL の値をとる整数型です。 [!INCLUDE[ssSDW](../../includes/sssdw-md.md)] はビット列の記憶域を最適化します。 テーブル内のビット列が 8 個以下の場合、列は 1 バイトとして格納されます。 ビット列が 9 から 16 個の場合、列は 2 バイトとして格納されます。以下同様です。  
  
 `nvarchar` [ ( *n* | `max` ) ]  -- `max` は [!INCLUDE[ssSDW](../../includes/sssdw-md.md)] にのみ適用されます。  
 可変長の Unicode 文字データ。 *n* 1 ～ 4000 の値を指定できます。 `max` は最大格納サイズが 2^31-1 バイト (2 GB) であることを示します。 記憶域のサイズは、入力文字数の 2 倍のバイト数に 2 バイトを足した値です。 入力データの長さは 0 文字でもかまいません。  
  
 `nchar` [ ( *n* ) ]  
 *n* 文字の長さを持つ、固定長の Unicode 文字データです。 *n* には `1` ～ `4000` の値を指定する必要があります。 ストレージのサイズは、*n* の 2 倍のバイト数です。  
  
 `varchar` [ ( *n*  | `max` ) ]  -- `max` は [!INCLUDE[ssSDW](../../includes/sssdw-md.md)] にのみ適用されます。   
 *n* バイトの長さを持つ、可変長の Unicode 以外の文字データです。 *n* には `1` ～ `8000` の値を指定する必要があります。 `max` は最大記憶域サイズが 2^31-1 バイト (2 GB) であることを示します。記憶領域のサイズは、入力されたデータの実際の長さに 2 バイトを加えたものとなります。  
  
 `char` [ ( *n* ) ]  
 *n* バイトの長さを持つ、固定長の Unicode 以外の文字データです。 *n* には `1` ～ `8000` の値を指定する必要があります。 ストレージのサイズは *n* バイトです。 *n* の既定値は `1` です。  
  
 `varbinary` [ ( *n*  | `max` ) ]  -- `max` は [!INCLUDE[ssSDW](../../includes/sssdw-md.md)] にのみ適用されます。  
 可変長 binary データ。 *n* には、`1` ～ `8000` の値を指定できます。 `max` は最大格納サイズが 2^31-1 バイト (2 GB) であることを示します。 記憶領域のサイズは、入力されたデータの実際の長さに 2 バイトを加えたものとなります。 *n* の既定値は 7 です。  
  
 `binary` [ ( *n* ) ]  
 *n* バイトの長さを持つ、固定長のバイナリ データです。 *n* には、`1` ～ `8000` の値を指定できます。 ストレージのサイズは *n* バイトです。 *n* の既定値は `7` です。  
  
 `uniqueidentifier`  
 16 バイトの GUID です。  
   
<a name="Permissions"></a>  
## <a name="permissions"></a>アクセス許可  
 テーブルを作成するには、`db_ddladmin` 固定データベース ロールでの権限、または次の権限が必要です。
 - データベースに対する `CREATE TABLE` 権限
 - テーブルを含めるスキーマに対する `ALTER SCHEMA` 権限 

パーティション テーブルを作成するには、`db_ddladmin` 固定データベース ロールでの権限、または次の権限が必要です。

- `ALTER ANY DATASPACE` 権限
  
 ローカル一時テーブルを作成するログインでは、テーブルに対する `CONTROL`、`INSERT`、`SELECT`、`UPDATE` の各権限が受け入れられます。  
 
<a name="GeneralRemarks"></a>  
## <a name="general-remarks"></a>全般的な解説  
 
最小値と最大値については、[[!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] の容量制限](https://azure.microsoft.com/documentation/articles/sql-data-warehouse-service-capacity-limits/)に関するページを参照してください。 
 
### <a name="determining-the-number-of-table-partitions"></a>テーブルのパーティションの数を決定する
各ユーザー定義テーブルは、複数の小さなテーブルに分割され、ディストリビューションと呼ばれる個々の場所に格納されるます。 [!INCLUDE[ssSDW](../../includes/sssdw-md.md)] では 60 のディストリビューションが使用されます。 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] において、ディストリビューションの数はコンピューティング ノードの数によって異なります。
 
各ディストリビューションには、すべてのテーブル パーティションが含まれます。 たとえば、60 のディストリビューションと 4 つのテーブル パーティションに加えて 1 つの空のパーティションがある場合は、300 のパーティションが存在することになります (5 x 60= 300)。 テーブルがクラスター化列ストア インデックスである場合、パーティションごとに列ストア インデックスが 1 つ存在することになります。つまり、列ストア インデックスの数は 300 になります。

列ストア インデックスの利点を活用する上で十分な行が各列ストア インデックスに含まれるようにするために、使用するテーブル パーティションの数を少なくすることをお勧めします。 詳細については、[[!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] でのテーブルのパーティション分割](https://azure.microsoft.com/documentation/articles/sql-data-warehouse-tables-partition/)に関するページと [[!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] でのテーブルのインデックス作成](https://azure.microsoft.com/documentation/articles/sql-data-warehouse-tables-index/)に関するページを参照してください。  

### <a name="rowstore-table-heap-or-clustered-index"></a>行ストア テーブル (ヒープまたはクラスター化インデックス)

行ストア テーブルは、行単位で格納されるテーブルです。 ヒープまたはクラスター化インデックスが該当します。 [!INCLUDE[ssSDW](../../includes/sssdw-md.md)] では、ページ圧縮によってすべての行ストア テーブルを作成します。この動作はユーザーが構成できる設定ではありません。

### <a name="columnstore-table-columnstore-index"></a>列ストア テーブル (列ストア インデックス)

列ストア テーブルは、列単位で格納されるテーブルです。 列ストア インデックスは、列ストア テーブルに格納されているデータを管理する技術です。  クラスター化列ストア インデックスはデータの配布方法には影響しません。むしろ、各ディストリビューションへのデータの格納方法に影響します。

行ストア テーブルを列ストア テーブルに変更するには、テーブル上の既存のインデックスをすべて削除してから、クラスター化列ストア インデックスを作成します。 例については、「[CREATE COLUMNSTORE INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/create-columnstore-index-transact-sql.md)」を参照してください。

詳細と例については、次の記事をご覧ください。
- [列ストア インデックスのバージョン管理機能の概要](../../relational-databases/indexes/columnstore-indexes-what-s-new.md)
- [[!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] でテーブルにインデックスを作成する](https://azure.microsoft.com/documentation/articles/sql-data-warehouse-tables-index/)
- [列ストア インデックスの説明](~/relational-databases/indexes/columnstore-indexes-overview.md) 

<a name="LimitationsRestrictions"></a>  
## <a name="limitations-and-restrictions"></a>制限事項と制約事項  
 ディストリビューション列の DEFAULT 制約を定義することはできません。  
  
### <a name="partitions"></a>メジャー グループ
パーティションを使用する場合、パーティション列には、Unicode のみの照合順序は設定できません。 たとえば、次のステートメントは失敗します。  
  
 ```sql
CREATE TABLE t1 ( c1 varchar(20) COLLATE Divehi_90_CI_AS_KS_WS) WITH (PARTITION (c1 RANGE FOR VALUES (N'')))
```  
 
 *boundary_value* が *partition_column_name* 内のデータ型に暗黙的に変換する必要があるリテラル値である場合は、矛盾が発生します。 リテラルは [!INCLUDE[ssSDW](../../includes/sssdw-md.md)] システム ビューを通して表示されますが、変換後の値は [!INCLUDE[tsql](../../includes/tsql-md.md)] の操作で使用されます。 

### <a name="temporary-tables"></a>一時テーブル

 ## で始まるグローバル一時テーブルはサポートされていません。  
  
 ローカル一時テーブルには、次のような制限事項と制約があります。  
  
-   現在のセッションにのみ表示されます。 [!INCLUDE[ssSDW](../../includes/sssdw-md.md)] では、セッションの終了時にローカル一時テーブルを自動的に削除します。 明示的に削除するには、DROP TABLE ステートメントを使用します。   
-   名前を変更することはできません。 
-   パーティションまたはビューを持つことはできません。  
-   それらのアクセス許可を変更することはできません。 ローカル一時テーブルでは、`GRANT`、`DENY`、および `REVOKE` ステートメントは使用できません。   
-   データベース コンソール コマンドは、一時テーブルに対してはブロックされます。   
-   バッチ内で複数のローカル一時テーブルを使用する場合、各テーブルの名前は一意でなければなりません。 複数のセッションが同じバッチを実行していて、同じローカル一時テーブルを作成する場合、[!INCLUDE[ssSDW](../../includes/sssdw-md.md)] の内部では、ローカル一時テーブルごとに一意の名前を維持するために、ローカル一時テーブル名に数値サフィックスを追加します。  
    
<a name="LockingBehavior"></a>   
## <a name="locking-behavior"></a>ロック動作  
 テーブルでは排他的ロックを取得します。 DATABASE、SCHEMA、SCHEMARESOLUTION オブジェクトでは、共有ロックを取得します。  
 

<a name="ExamplesColumn"></a>   
## <a name="examples-for-columns"></a>列の例

### <a name="a-specify-a-column-collation"></a><a name="ColumnCollation"></a> A. 列の照合順序を指定します。 
 次の例では、テーブル `MyTable` を 2 つの異なる列照合順序で作成します。 既定では、列 `mycolumn1` の既定の照合順序は Latin1_General_100_CI_AS_KS_WS となります。 列 `mycolumn2` の照合順序は Frisian_100_CS_AS となります。  
  
```sql
CREATE TABLE MyTable   
  (  
    mycolumnnn1 nvarchar,  
    mycolumn2 nvarchar COLLATE Frisian_100_CS_AS )  
WITH ( CLUSTERED COLUMNSTORE INDEX )  
;  
```  
  
### <a name="b-specify-a-default-constraint-for-a-column"></a><a name="DefaultConstraint"></a> B. 列に対して DEFAULT 制約を指定する

 次の例では、列に対して既定値を指定する構文を示します。 colA 列には constraint_colA という名前の DEFAULT 制約があり、既定値は 0 です。  
  
```sql
CREATE TABLE MyTable
  (  
    colA int CONSTRAINT constraint_colA DEFAULT 0,  
    colB nvarchar COLLATE Frisian_100_CS_AS
  )  
WITH ( CLUSTERED COLUMNSTORE INDEX )  
;  
```

<a name="ExamplesTemporaryTables"></a> 
## <a name="examples-for-temporary-tables"></a>一時テーブルの例

### <a name="c-create-a-local-temporary-table"></a><a name="TemporaryTable"></a> C. ローカル一時テーブルを作成します。  
 次の例は、#myTable という名前のローカル一時テーブルを作成します。 テーブルは、3 つの部分で構成される名前によって指定され、# で始まります。
  
```sql
CREATE TABLE AdventureWorks.dbo.#myTable
  (  
   id int NOT NULL,  
   lastName varchar(20),  
   zipCode varchar(6)  
  )  
WITH  
  (   
    DISTRIBUTION = HASH (id),  
    CLUSTERED COLUMNSTORE INDEX
  )  
;  
```

<a name="ExTableStructure"></a>  
## <a name="examples-for-table-structure"></a>テーブル構造の例

### <a name="d-create-a-table-with-a-clustered-columnstore-index"></a><a name="ClusteredColumnstoreIndex"></a> D. クラスター化列ストア インデックスを持つテーブルを作成する  
 次の例では、クラスター化列ストア インデックスを持つ分散テーブルを作成します。 各ディストリビューションは、列ストアとして格納されます。  
  
 クラスター化列ストア インデックスは、データの配布方法には影響しません。データは常に行によって配布されます。 クラスター化列ストア インデックスは、各ディストリビューション内でのデータの格納方法に影響します。  
  
```sql
  CREATE TABLE MyTable
  (  
    colA int CONSTRAINT constraint_colA DEFAULT 0,  
    colB nvarchar COLLATE Frisian_100_CS_AS
  )  
WITH   
  (   
    DISTRIBUTION = HASH ( colB ),  
    CLUSTERED COLUMNSTORE INDEX
  )  
;  
```  

### <a name="e-create-an-ordered-clustered-columnstore-index"></a><a name="OrderedClusteredColumnstoreIndex"></a> E. 順序付けされたクラスター化列ストア インデックスを作成する

以下の例は、順序付けされたクラスター化列ストア インデックスを作成する方法を示しています。 インデックスは SHIPDATE で順序付けされます。

```sql
CREATE TABLE Lineitem  
WITH (DISTRIBUTION = ROUND_ROBIN, CLUSTERED COLUMNSTORE INDEX ORDER(SHIPDATE))  
AS  
SELECT * FROM ext_Lineitem
```

<a name="ExTableDistribution"></a> 
## <a name="examples-for-table-distribution"></a>テーブルの分散例

### <a name="f-create-a-round_robin-table"></a><a name="RoundRobin"></a> F. ROUND_ROBIN テーブルを作成する  
 次の例では、3 つの列を含みパーティションのない ROUND_ROBIN テーブルを作成します。 データはすべてのディストリビューションに分散されます。 テーブルは CLUSTERED COLUMNSTORE INDEX で作成されており、ヒープまたは行ストア クラスター化インデックスより、パフォーマンスとデータ圧縮が優れています。  
  
```sql
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED COLUMNSTORE INDEX );  
```  
  
### <a name="g-create-a-hash-distributed-table"></a><a name="HashDistributed"></a> G. ハッシュ分散テーブルを作成する

 次の例では、前の例と同じテーブルを作成します。 ただし、このテーブルの場合、行は ROUND_ROBIN テーブルのようにランダムに分散されるのではなく、(`id` 列上に) 配布されます。 テーブルは CLUSTERED COLUMNSTORE INDEX で作成されており、ヒープまたは行ストア クラスター化インデックスより、パフォーマンスとデータ圧縮が優れています。  
  
```sql
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH  
  (   
    DISTRIBUTION = HASH (id),   
    CLUSTERED COLUMNSTORE INDEX  
  );  
```  
  
### <a name="h-create-a-replicated-table"></a><a name="Replicated"></a> H. レプリケート テーブルを作成します。  
 次の例では、前の例のようなレプリケート テーブルを作成します。 レプリケート テーブルは、各計算ノードに完全にコピーされます。 各計算ノードにこのコピーがあれば、クエリにおけるデータ移動を減らすことができます。 この例は CLUSTERED INDEX を使用して作成され、ヒープよりもデータの圧縮率が高くなっています。 ヒープでは、適切な CLUSTERED COLUMNSTORE INDEX 圧縮を実現するための十分な列が含まれていない場合があります。  
  
```sql
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH  
  (   
    DISTRIBUTION = REPLICATE,
    CLUSTERED INDEX (lastName)  
  );  
```  

<a name="ExTablePartitions"></a> 
## <a name="examples-for-table-partitions"></a>テーブル パーティションの例

###  <a name="i-create-a-partitioned-table"></a><a name="PartitionedTable"></a> I. パーティション テーブルを作成します。

 次の例では、例 A で示したのと同じテーブルを作成し、`id` 列に RANGE LEFT パーティションを追加します。 4 つのパーティション境界値が指定されており、5 つのパーティションが作成されます。  
  
```sql
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode int)  
WITH
  (
  
    PARTITION ( id RANGE LEFT FOR VALUES (10, 20, 30, 40 )),  
    CLUSTERED COLUMNSTORE INDEX
  )  
;  
```  
  
 この例では、データは次のパーティションに並べ替えられます。  
  
- パーティション 1: col <= 10
- パーティション 2:10 < col <= 20
- パーティション 3:20 < col <= 30
- パーティション 4:30 < col <= 40
- パーティション 5:40 < col  
  
 この同じテーブルを RANGE LEFT (既定値) ではなく RANGE RIGHT でパーティション分割したとすると、データは次のパーティションに並べ替えられます。  
  
- パーティション 1: col < 10  
- パーティション 2:10 <= col < 20
- パーティション 3:20 <= col < 30
- パーティション 4:30 <= col < 40
- パーティション 5:40 <= col  
  
### <a name="j-create-a-partitioned-table-with-one-partition"></a><a name="OnePartition"></a> J. パーティションが 1 つのパーティション テーブルを作成する

 次の例では、1 つのパーティションを持つパーティション テーブルを作成します。 境界値は指定されていないため、結果は 1 つのパーティションになります。  
  
```sql
CREATE TABLE myTable (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode int)  
WITH
    (
      PARTITION ( id RANGE LEFT FOR VALUES ( )),  
      CLUSTERED COLUMNSTORE INDEX  
    )  
;  
```  
  
### <a name="k-create-a-table-with-date-partitioning"></a><a name="DatePartition"></a> K. 日付のパーティション分割でテーブルを作成する

 次の例では、`myTable` という名前の新しいテーブルを作成し、`date` 列に基づいてパーティション分割を行います。 RANGE RIGHT を使用し、境界値として日付を使用することで、各パーティションにデータの月が格納されます。  
  
```sql
CREATE TABLE myTable (  
    l_orderkey      bigint,
    l_partkey       bigint,
    l_suppkey       bigint,
    l_linenumber    bigint,
    l_quantity      decimal(15,2),  
    l_extendedprice decimal(15,2),  
    l_discount      decimal(15,2),  
    l_tax           decimal(15,2),  
    l_returnflag    char(1),  
    l_linestatus    char(1),  
    l_shipdate      date,  
    l_commitdate    date,  
    l_receiptdate   date,  
    l_shipinstruct  char(25),  
    l_shipmode      char(10),  
    l_comment       varchar(44))  
WITH
  (
    DISTRIBUTION = HASH (l_orderkey),  
    CLUSTERED COLUMNSTORE INDEX,  
    PARTITION ( l_shipdate  RANGE RIGHT FOR VALUES
      (  
        '1992-01-01','1992-02-01','1992-03-01','1992-04-01','1992-05-01',
        '1992-06-01','1992-07-01','1992-08-01','1992-09-01','1992-10-01',
        '1992-11-01','1992-12-01','1993-01-01','1993-02-01','1993-03-01',
        '1993-04-01','1993-05-01','1993-06-01','1993-07-01','1993-08-01',
        '1993-09-01','1993-10-01','1993-11-01','1993-12-01','1994-01-01',
        '1994-02-01','1994-03-01','1994-04-01','1994-05-01','1994-06-01',
        '1994-07-01','1994-08-01','1994-09-01','1994-10-01','1994-11-01',
        '1994-12-01'  
      ))
  );  
```  
  
<a name="SeeAlso"></a>
## <a name="see-also"></a>関連項目
 
[CREATE TABLE AS SELECT &#40;Azure Synapse Analytics&#41;](../../t-sql/statements/create-table-as-select-azure-sql-data-warehouse.md)   
[DROP TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/drop-table-transact-sql.md)   
[ALTER TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-table-transact-sql.md)   
[sys.index_columns &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-index-columns-transact-sql.md) 
