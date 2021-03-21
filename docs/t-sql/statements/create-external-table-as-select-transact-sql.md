---
description: CREATE EXTERNAL TABLE AS SELECT (Transact-SQL)
title: CREATE EXTERNAL TABLE AS SELECT (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 08/10/2017
ms.prod_service: synapse-analytics, pdw
ms.reviewer: ''
ms.service: sql-data-warehouse
ms.subservice: design
ms.topic: conceptual
f1_keywords:
- CREATE EXTERNAL TABLE AS SELECT
- CREATE_EXTERNAL_TABLE_AS_SELECT
- CREATE EXTERNAL TABLE AS SELECT_TSQL
helpviewer_keywords:
- External, table
- PolyBase, external table
- External, table create as select
- PolyBase, create table as select
ms.assetid: 32dfe254-6df7-4437-bfd6-ca7d37557b0a
author: ronortloff
ms.author: rortloff
monikerRange: '>= aps-pdw-2016 || = azure-sqldw-latest'
ms.openlocfilehash: 6983bda4d07c68fff408891a6156b4c59f944b9f
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104754352"
---
# <a name="create-external-table-as-select-transact-sql"></a>CREATE EXTERNAL TABLE AS SELECT (Transact-SQL)
[!INCLUDE[applies-to-version/asa-pdw](../../includes/applies-to-version/asa-pdw.md)]

  外部テーブルを作成してから、Hadoop または Azure Blob ストレージに [!INCLUDE[tsql](../../includes/tsql-md.md)] SELECT ステートメントの結果を並列でエクスポートします。

 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則 (Transact-SQL)](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)

## <a name="syntax"></a>構文

```syntaxsql 
CREATE EXTERNAL TABLE [ [database_name  . [ schema_name ] . ] | schema_name . ] table_name   
    WITH (   
        LOCATION = 'hdfs_folder',  
        DATA_SOURCE = external_data_source_name,  
        FILE_FORMAT = external_file_format_name  
        [ , <reject_options> [ ,...n ] ]  
    )  
    AS <select_statement>  
[;]  
  
<reject_options> ::=  
{  
    | REJECT_TYPE = value | percentage  
    | REJECT_VALUE = reject_value  
    | REJECT_SAMPLE_VALUE = reject_sample_value  
}  
  
<select_statement> ::=  
    [ WITH <common_table_expression> [ ,...n ] ]  
    SELECT <select_criteria>  
```

## <a name="arguments"></a>引数
 **[ [ *database_name* . [ *schema_name* ] . ] | *schema_name* . ] *table_name*** は、データベースに作成するテーブルの 1 から 3 部構成の名前です。 外部テーブルの場合、テーブル メタデータのみがリレーショナル データベースに格納されます。 

 **LOCATION =  '*hdfs_folder*'** には、外部データ ソースで SELECT ステートメントの結果を書き込む場所を指定します。 場所はフォルダー名であり、必要に応じて、Hadoop クラスターまたは BLOB ストレージのルート フォルダーへの相対パスを含めることができます。 PolyBase ではパスとフォルダー (まだ存在しない場合) が作成されます。

外部ファイルは *hdfs_folder* に書き込まれ、*QueryID_date_time_ID.format* という名前が付けられます (*ID* は増分識別子、*format* はエクスポートされるデータ形式)。 たとえば、QID776_20160130_182739_0.orc です。

 **DATA_SOURCE = *external_data_source_name*** には、外部データが格納されている、または格納される場所を含む、外部データ ソース オブジェクトの名前を指定します。 場所は、Hadoop クラスターまたは Azure Blob ストレージのいずれかです。 外部データ ソースを作成するには、[CREATE EXTERNAL DATA SOURCE &#40;Transact-SQL&#41;](../../t-sql/statements/create-external-data-source-transact-sql.md) を使用します。

 **FILE_FORMAT = *external_file_format_name*** には、外部データ ファイルの形式を含む、外部ファイル形式オブジェクトの名前を指定します。 外部ファイル形式を作成するには、[CREATE EXTERNAL FILE FORMAT &#40;Transact-SQL&#41;](../../t-sql/statements/create-external-file-format-transact-sql.md) を使用します。

 この CREATE EXTERNAL TABLE AS SELECT ステートメントの実行時に、**REJECT オプション** は適用されません。 代わりに、ここで指定し、後で外部テーブルからデータをインポートする際に、データベースで使用できるようにします。 後で CREATE TABLE AS SELECT ステートメントで外部テーブルからデータを選択するときに、データベースでは拒否オプションを使用して、インポートを停止するまでにインポートの失敗が許容される行の数または割合を決定します。

   - **REJECT_VALUE = *reject_value*** には、インポートに失敗することができる行の値または割合を指定します。これを超えると、データベースのインポートが停止されます。

   - **REJECT_TYPE = **value** | percentage** を使用して、REJECT_VALUE オプションがリテラル値として指定されているか、割合として指定されているかを明確にします。

      - **Value** は、REJECT_VALUE が割合ではなくリテラル値の場合に使用されます。 失敗した行の数が *reject_value* を超えた場合、データベースは外部データ ファイルからの行のインポートを停止します。

        たとえば、REJECT_VALUE = 5 で、REJECT_TYPE = value の場合、データベースは、5 行のインポートが失敗した後、行のインポートを停止します。

      - **Percentage** は、REJECT_VALUE がリテラル値ではなく割合の場合に使用されます。 失敗した行の *percentage* が *reject_value* を超えた場合、データベースは外部データ ファイルからの行のインポートを停止します。 失敗した行の割合は、間隔をおいて計算されます。

   - **REJECT_SAMPLE_VALUE = *reject_sample_value*** は、REJECT_TYPE= percentage の場合に必要です。これにより、データベースが失敗した行の割合を再計算する前に、インポートを試みる行の数が指定されます。

      たとえば、REJECT_SAMPLE_VALUE = 1000 の場合、データベースは外部データ ファイルから 1000 行のインポートを試みた後、失敗した行の割合を計算します。 失敗した行の割合が *reject_value* 未満の場合、データベースは別の 1000 行の読み込みを試みます。 データベースは引き続き、その 1000 行のそれぞれのインポートを試みた後、失敗した行の割合を再計算します。

     > [!NOTE]
     >  データベースは一定の間隔で失敗した行の割合を計算するため、失敗した行の実際の割合が *reject_value* を超える場合があります。

     **例:**

     この例は、REJECT の 3 つのオプションが相互にどのように作用するかを示しています。 たとえば、REJECT_TYPE = percentage で REJECT_VALUE = 30、かつ REJECT_SAMPLE_VALUE = 100 の場合、次のシナリオが発生する可能性があります。

     - データベースでは、最初の 100 行の読み込みが試行されます。この場合、失敗が 25、成功が 75 です。
     - 失敗した行の割合は 25% と計算されます。これは reject 値である 30% を下回っています。 そのため、読み込みを停止する必要はありません。
     - データベースでは、次の 100 行の読み込みが試行されます。 今回は成功が 25、失敗が 75 です。
     - 失敗した行の割合が 50% として再計算されます。 失敗した行の割合が、30% という reject 値を超えました。
     - 200 行の読み込みを試みた後、失敗した行の割合が 50% で読み込みに失敗しています。この割合は、指定された制限の 30% を超えています。

 **WITH *common_table_expression*** には、共通テーブル式 (CTE) と呼ばれる一時的な名前付き結果セットを指定します。 詳細については、「[WITH common_table_expression &#40;Transact-SQL&#41;](../../t-sql/queries/with-common-table-expression-transact-sql.md)」を参照してください。 

 **SELECT \<select_criteria>** を使用すると、SELECT ステートメントの結果が新しいテーブルに追加されます。 *select_criteria* は、新しいテーブルにコピーするデータを決定する SELECT ステートメントの本文です。 SELECT ステートメントについては、「[SELECT &#40;Transact-SQL&#41;](../../t-sql/queries/select-transact-sql.md)」を参照してください。

## <a name="permissions"></a>アクセス許可

 このコマンドを実行するには、*データベース ユーザー* は以下のすべての権限またはメンバーシップが必要です。

- **db_ddladmin** 固定データベース ロールの新しいテーブルまたはメンバーシップを含む、ローカル スキーマに対する **ALTER SCHEMA** 権限。
- **db_ddladmin** 固定データベース ロールの **CREATE TABLE** 権限またはメンバーシップ。
- *select_criteria* で参照されるすべてのオブジェクトに対する **SELECT** 権限。

 ログインには次のすべての権限が必要です。

- **ADMINISTER BULK OPERATIONS**
- **ALTER ANY EXTERNAL DATA SOURCE**
- **ALTER ANY EXTERNAL FILE FORMAT**
- Hadoop クラスターまたは BLOB ストレージで外部フォルダーを読み取りおよび書き込みを行うための **書き込み** 権限。

 > [!IMPORTANT]
 >  ALTER ANY EXTERNAL DATA SOURCE 権限は、あらゆる外部データ ソース オブジェクトを作成し、変更する能力をプリンシパルに与えます。そのため、データベース上のすべてのデータベース スコープ資格情報にアクセスする能力も与えます。 この権限は特権として考える必要があり、システム内の信頼できるプリンシパルにのみ与える必要があります。

## <a name="error-handling"></a>エラー処理
 CREATE EXTERNAL TABLE AS SELECT でデータをテキスト区切りファイルにエクスポートした場合、エクスポートに失敗した行に対する拒否ファイルはありません。

 外部テーブルの作成時に、データベースでは外部の Hadoop クラスターまたは BLOB ストレージへの接続が試行されます。 接続に失敗した場合、コマンドは失敗し、外部テーブルは作成されません。 データベースは接続を 3 回以上再試行するため、コマンドが失敗するまで 1 分以上かかる場合があります。

 CREATE EXTERNAL TABLE AS SELECT が取り消されたか、失敗した場合、データベースにより、外部データ ソースで既に作成されている新しいファイルとフォルダーの削除が 1 回だけ試みられます。

 データベースは、データのエクスポート時に外部データ ソースで発生したすべての Java エラーを報告します。

##  <a name="general-remarks"></a><a name="GeneralRemarks"></a> 全般的な解説
 CREATE EXTERNAL TABLE AS SELECT ステートメントが完了したら、外部テーブルに対して [!INCLUDE[tsql](../../includes/tsql-md.md)] クエリを実行できます。 CREATE TABLE AS SELECT ステートメントを使用してインポートする場合を除き、これらの操作ではクエリの実行中にデータをデータベースにインポートします。

 外部テーブルの名前と定義は、データベースのメタデータに格納されます。 データは外部データ ソースに格納されます。

 外部ファイルの名前は *QueryID_date_time_ID.format* です ( *ID* は増分識別子、 *format* はエクスポートされるデータ形式)。 たとえば、QID776_20160130_182739_0.orc です。

 CREATE EXTERNAL TABLE AS SELECT ステートメントを使用すると、ソース テーブルがパーティション分割されている場合でも、常にパーティション分割されていないテーブルが作成されます。

 EXPLAIN で作成されたクエリ プランの場合、データベースでは外部テーブルに対して次のクエリ プラン操作が使用されます。

- 外部シャッフル移動
- 外部ブロードキャスト移動
- 外部パーティション移動

 **適用対象:** Parallel Data Warehouse

外部テーブルを作成するための前提条件として、アプライアンス管理者は Hadoop 接続を構成する必要があります。 詳細については、Analytics Platform System のドキュメントの外部データへの接続の構成 (Analytics Platform System) に関する記事を参照してください。このドキュメントは、[Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=48241)からダウンロードできます。

## <a name="limitations-and-restrictions"></a>制限事項と制約事項
 外部テーブル データがデータベースの外部にあるため、バックアップおよび復元操作ではデータベースに格納されているデータに対してのみ作用します。 その結果、メタデータのみがバックアップされ、復元されます。

 データベースでは、外部テーブルを含むデータベース バックアップを復元する際に、外部データ ソースへの接続は確認されません。 元のソースにアクセスできない場合でも、外部テーブルのメタデータの復元は正常に行われますが、外部テーブルでの SELECT 操作は失敗します。

 データベースでは、データベースと外部データ間のデータの一貫性は保証されません。 外部データとデータベース間の一貫性を保つことだけは、お客様が行う必要があります。

 外部テーブルでのデータ操作言語 (DML) 操作はサポートされていません。 たとえば、[!INCLUDE[tsql](../../includes/tsql-md.md)] の UPDATE、INSERT、DELETE [!INCLUDE[tsql](../../includes/tsql-md.md)] ステートメントを使用して、外部データを変更することはできません。

 外部テーブルで許可されるデータ定義言語 (DDL) 操作は、CREATE TABLE、DROP TABLE、CREATE STATISTICS、DROP STATISTICS、CREATE VIEW、DROP VIEW のみです。

 サーバーレス SQL プールの外部テーブルを、現在データがある場所に作成することはできません。 データの格納に使用されている場所を再利用するには、その場所を ADLS で手動で削除する必要があります。

 PolyBase では、32 個の同時 PolyBase クエリを実行しているときに、フォルダーあたり最大 33,000 ファイルを使用できます。 この最大数には、各 HDFS フォルダー内のファイルとサブフォルダーの両方が含まれます。 コンカレンシーの度合いが 32 未満である場合、ユーザーは 33,000 より多いファイルが含まれている HDFS のフォルダーに対して PolyBase クエリを実行できます。 Hadoop および PolyBase のユーザーがファイル パスを短くし、HDFS フォルダーごとに 30,000 以下のファイルを使用することをお勧めします。 参照されているファイルが多すぎると、JVM のメモリ不足例外が発生します。

 この CREATE EXTERNAL TABLE AS SELECT では [SET ROWCOUNT &#40;Transact-SQL&#41;](../../t-sql/statements/set-rowcount-transact-sql.md) の効果はありません。 同様の動作を実現するには、[TOP &#40;Transact-SQL&#41;](../../t-sql/queries/top-transact-sql.md) を使用します。

 CREATE EXTERNAL TABLE AS SELECT で RCFile から選択する場合、RCFile の列値にパイプ "|" 文字を含めることはできません。

Parquet または ORC ファイルに対する CREATE EXTERNAL TABLE AS SELECT では、次の文字がデータ内に存在する場合、拒否されたレコードが含まれる可能性があるエラーが発生します。

- |
- " (引用符文字)
- /r/n
- /r
- /n

これらの文字が含まれる CREATE EXTERNAL TABLE AS SELECT を使用するには、最初にデータに CREATE EXTERNAL TABLE AS SELECT ステートメントを実行してデータを区切りテキストファイルにエクスポートする必要があり、その後は外部ツールを使用して Parquet または ORC に変換することができます。

## <a name="locking"></a>ロック
 SCHEMARESOLUTION オブジェクトの共有ロックを取得します。

##  <a name="examples"></a><a name="Examples"></a> 使用例

### <a name="a-create-a-hadoop-table-by-using-create-external-table-as-select"></a>A. CREATE EXTERNAL TABLE AS SELECT を使用して Hadoop テーブルを作成する

 次の例では、ソース テーブル `dimCustomer` の列の定義とデータを使用して、`hdfsCustomer` という名前の新しい外部テーブルを作成します。

 テーブル定義はデータベースに格納され、SELECT ステートメントの結果は、Hadoop 外部データソース *customer_ds* の '/pdwdata/customer.tbl' ファイルにエクスポートされます。 ファイルは、外部ファイル形式 *customer_ff* に従って書式設定されます。

 ファイル名はデータベースによって生成され、クエリ ID を含みます。これにより、ファイルを生成元のクエリに合わせやすくなります。

 Customer ディレクトリの前のパス `hdfs://xxx.xxx.xxx.xxx:5000/files/` は既に存在している必要があります。 Customer ディレクトリが存在しない場合は、データベースでそのディレクトリが作成されます。

> [!NOTE]
>  この例では 5000 に指定されています。 ポートが指定されていない場合、データベースは既定のポートとして 8020 を使用します。

 結果の Hadoop の場所とファイル名は `hdfs:// xxx.xxx.xxx.xxx:5000/files/Customer/ QueryID_YearMonthDay_HourMinutesSeconds_FileIndex.txt.` です。

```sql  
-- Example is based on AdventureWorks   
CREATE EXTERNAL TABLE hdfsCustomer  
WITH (  
        LOCATION='/pdwdata/customer.tbl',  
        DATA_SOURCE = customer_ds,  
        FILE_FORMAT = customer_ff  
) AS SELECT * FROM dimCustomer;  
```

### <a name="b-use-a-query-hint-with-create-external-table-as-select"></a>B. CREATE EXTERNAL TABLE AS SELECT でクエリ ヒントを使用する

 このクエリは、CREATE EXTERNAL TABLE AS SELECT ステートメントでクエリ結合ヒントを使用する場合の基本的な構文を示しています。 クエリが送信された後、データベースではハッシュ結合方法を使用して、クエリ プランを生成します。 結合ヒントと OPTION 句の使用方法の詳細については、「[OPTION 句 &#40;Transact-SQL&#41;](../../t-sql/queries/option-clause-transact-sql.md)」を参照してください。

> [!NOTE]
>  この例では 5000 に指定されています。 ポートが指定されていない場合、データベースは既定のポートとして 8020 を使用します。

```sql  
-- Example is based on AdventureWorks  
CREATE EXTERNAL TABLE dbo.FactInternetSalesNew  
WITH   
    (   
        LOCATION = '/files/Customer',  
        DATA_SOURCE = customer_ds,  
        FILE_FORMAT = customer_ff  
    )  
AS SELECT T1.* FROM dbo.FactInternetSales T1 JOIN dbo.DimCustomer T2  
ON ( T1.CustomerKey = T2.CustomerKey )  
OPTION ( HASH JOIN );  
```

## <a name="see-also"></a>こちらもご覧ください
 - [CREATE EXTERNAL DATA SOURCE &#40;Transact-SQL&#41;](../../t-sql/statements/create-external-data-source-transact-sql.md)
 - [CREATE EXTERNAL FILE FORMAT &#40;Transact-SQL&#41;](../../t-sql/statements/create-external-file-format-transact-sql.md)
 - [CREATE EXTERNAL TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/create-external-table-transact-sql.md)
 - [CREATE TABLE &#40;Azure Synapse Analytics、Parallel Data Warehouse&#41;](~/t-sql/statements/create-table-azure-sql-data-warehouse.md)
 - [CREATE TABLE AS SELECT &#40;Azure Synapse Analytics&#41;](../../t-sql/statements/create-table-as-select-azure-sql-data-warehouse.md)
 - [DROP TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/drop-table-transact-sql.md)
 - [ALTER TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-table-transact-sql.md)



