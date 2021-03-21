---
description: INSERT (Transact-SQL)
title: INSERT (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 04/21/2020
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- INSERT_TSQL
- INSERT
dev_langs:
- TSQL
helpviewer_keywords:
- inserting multiple rows
- user-defined types [SQL Server], inserting values
- DML [SQL Server], INSERT statement
- bulk load [SQL Server]
- row additions [SQL Server], INSERT statement
- inserting rows
- table value constructor [SQL Server]
- adding data
- INSERT statement [SQL Server], about INSERT statement
- INSERT statement [SQL Server]
- UDTs [SQL Server], inserting values
- adding rows
- INSERT INTO statement
- data manipulation language [SQL Server], INSERT statement
- inserting data
ms.assetid: 1054c76e-0fd5-4131-8c07-a6c5d024af50
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 42085faef5f7da55a65f32569e51298991e9bb62
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104750562"
---
# <a name="insert-transact-sql"></a>INSERT (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]


[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] で、1 つまたは複数の行をテーブルやビューに追加します。 例については、「[例](#InsertExamples)」を参照してください。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql
-- Syntax for SQL Server and Azure SQL Database  

[ WITH <common_table_expression> [ ,...n ] ]  
INSERT   
{  
        [ TOP ( expression ) [ PERCENT ] ]   
        [ INTO ]   
        { <object> | rowset_function_limited   
          [ WITH ( <Table_Hint_Limited> [ ...n ] ) ]  
        }  
    {  
        [ ( column_list ) ]   
        [ <OUTPUT Clause> ]  
        { VALUES ( { DEFAULT | NULL | expression } [ ,...n ] ) [ ,...n     ]   
        | derived_table   
        | execute_statement  
        | <dml_table_source>  
        | DEFAULT VALUES   
        }  
    }  
}  
[;]  
  
<object> ::=  
{   
    [ server_name . database_name . schema_name .   
      | database_name .[ schema_name ] .   
      | schema_name .   
    ]  
  table_or_view_name  
}  
  
<dml_table_source> ::=  
    SELECT <select_list>  
    FROM ( <dml_statement_with_output_clause> )   
      [AS] table_alias [ ( column_alias [ ,...n ] ) ]  
    [ WHERE <search_condition> ]  
        [ OPTION ( <query_hint> [ ,...n ] ) ]  
```  
  
```syntaxsql
-- External tool only syntax  

INSERT   
{  
    [BULK]  
    { database_name.schema_name.table_or_view_name | schema_name.table_or_view_name | table_or_view_name }  
    ( <column_definition> )  
    [ WITH (  
        [ [ , ] CHECK_CONSTRAINTS ]  
        [ [ , ] FIRE_TRIGGERS ]  
        [ [ , ] KEEP_NULLS ]  
        [ [ , ] KILOBYTES_PER_BATCH = kilobytes_per_batch ]  
        [ [ , ] ROWS_PER_BATCH = rows_per_batch ]  
        [ [ , ] ORDER ( { column [ ASC | DESC ] } [ ,...n ] ) ]  
        [ [ , ] TABLOCK ]  
    ) ]  
}  
  
[; ] <column_definition> ::=  
 column_name <data_type>  
    [ COLLATE collation_name ]  
    [ NULL | NOT NULL ]  
  
<data type> ::=   
[ type_schema_name . ] type_name   
    [ ( precision [ , scale ] | max ]  
```  
  
```syntaxsql
-- Syntax for Azure Synapse Analytics and Parallel Data Warehouse  

INSERT INTO { database_name.schema_name.table_name | schema_name.table_name | table_name }
    [ ( column_name [ ,...n ] ) ]  
    {   
      VALUES ( { NULL | expression } )  
      | SELECT <select_criteria>  
    }  
    [ OPTION ( <query_option> [ ,...n ] ) ]  
[;]  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
 WITH \<common_table_expression>  
 INSERT ステートメントのスコープ内で定義された、一時的な名前付き結果セット (共通テーブル式とも呼ばれる) を指定します。 結果セットは SELECT ステートメントから派生します。 詳細については、「[WITH common_table_expression &#40;Transact-SQL&#41;](../../t-sql/queries/with-common-table-expression-transact-sql.md)」を参照してください。  
  
 TOP (*expression*) [ PERCENT ]  
 挿入するランダムな行の数または比率 (%) を指定します。 *expression* は行数または行の比率 (%) にすることができます。 詳細については、「[TOP &#40;Transact-SQL&#41;](../../t-sql/queries/top-transact-sql.md)」を参照してください。  
  
 INTO  
 INSERT キーワードと対象のテーブルとの間で使用できるキーワードで、省略可能です。  
  
 *server_name*  
 **適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。  
  
 テーブルまたはビューが配置されているリンク サーバーの名前です。 *server_name* は、[リンク サーバー](../../relational-databases/system-stored-procedures/sp-addlinkedserver-transact-sql.md)名として指定することも、[OPENDATASOURCE](../../t-sql/functions/opendatasource-transact-sql.md) 関数を使用して指定することもできます。  
  
 *server_name* をリンク サーバーとして指定する場合は、*database_name* と *schema_name* が必要です。 *server_name* を OPENDATASOURCE で指定する場合は、*database_name* および *schema_name* がすべてのデータ ソースに適用されるとは限らず、リモート オブジェクトにアクセスする OLE DB プロバイダーの機能により制限されます。  
  
 *database_name*  
 **適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。  
  
 データベースの名前です。  
  
 *schema_name*  
 テーブルまたはビューが属するスキーマの名前を指定します。  
  
 *table_or view_name*  
 データを受け取るテーブルまたはビューの名前を指定します。  
  
 [table](../../t-sql/data-types/table-transact-sql.md) 変数は、そのスコープの中では、INSERT ステートメントでテーブル ソースとして使用できます。  
  
 *table_or_view_name* によって参照されるビューは更新可能であることが必要です。また、そのビューの FROM 句ではベース テーブルを 1 つだけ参照している必要があります。 たとえば、複数のテーブルを参照するビューに対して INSERT を実行するには、1 つのベース テーブルの列のみを参照する *column_list* を使用する必要があります。 更新可能なビューの詳細については、「[CREATE VIEW &#40;Transact-SQL&#41;](../../t-sql/statements/create-view-transact-sql.md)」を参照してください。  
  
 *rowset_function_limited*  
 **適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。  
  
 [OPENQUERY](../../t-sql/functions/openquery-transact-sql.md) 関数または [OPENROWSET](../../t-sql/functions/openrowset-transact-sql.md) 関数を指定します。 これらの関数の使用は、リモート オブジェクトにアクセスする OLE DB プロバイダーの機能により制限されます。  
  
 WITH ( \<table_hint_limited> [... *n* ] )  
 対象のテーブルに設定可能なテーブル ヒントを 1 つ以上指定します。 キーワード WITH とかっこが必要です。  
  
 READPAST、NOLOCK、および READUNCOMMITTED は指定できません。 テーブル ヒントの詳細については、「[テーブル ヒント &#40;Transact-SQL&#41;](../../t-sql/queries/hints-transact-sql-table.md)」を参照してください。  
  
> [!IMPORTANT]  
>  INSERT ステートメントの対象となるテーブルに対して、HOLDLOCK、SERIALIZABLE、READCOMMITTED、REPEATABLEREAD、および UPDLOCK のヒントを指定する機能は、将来のバージョンの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では削除される予定です。 これらのヒントは、INSERT ステートメントのパフォーマンスに影響を与えません。 新しい開発作業では、これらのオプションの使用は避け、現在これらを使用しているアプリケーションは修正するようにしてください。  
  
 INSERT ステートメントの対象であるテーブルに対して TABLOCK ヒントを指定すると、TABLOCKX ヒントを指定した場合と同じ効果を得られます。 テーブルに対して、排他ロックが取得されます。  
  
 (*column_list*)  
 データを挿入する、1 つ以上の列で構成されるリストを指定します。 *column_list* はかっこで囲み、コンマで区切る必要があります。  
  
 *column_list* に列がない場合、[!INCLUDE[ssDE](../../includes/ssde-md.md)]では、列の定義に基づいて値を設定できる必要があります。値を設定できない場合は行を読み込むことはできません。 [!INCLUDE[ssDE](../../includes/ssde-md.md)]は、列が次の条件を満たす場合、自動的に列に値を設定します。  
  
-   IDENTITY プロパティを持っている。 増分された次の ID 値が使用されます。  
  
-   既定値を持っている。 列の既定値が使用されます。  
  
-   データ型は **timestamp** です。 現在のタイムスタンプ値が使用されます。  
  
-   NULL 値が許可されます。 NULL 値が使用されます。  
  
-   計算列である。 計算値が使用されます。  
  
ID 列に値を明示的に挿入するときは *column_list* を使用する必要があります。また、テーブルの SET IDENTITY_INSERT オプションを ON にする必要があります。  
  
OUTPUT 句  
 挿入操作の一部として、挿入された行を返します。 処理中のアプリケーションに結果を返すことも、テーブルまたはテーブル変数に結果を挿入して処理を続行することもできます。  
  
 OUTPUT 句は、ローカル パーティション ビュー、分散パーティション ビュー、またはリモート テーブルのいずれかを参照する DML ステートメントではサポートされていません。また、*execute_statement* が含まれる INSERT ステートメントでもサポートされていません。 OUTPUT INTO 句は、\<dml_table_source> 句を含む INSERT ステートメントではサポートされません。 この句の引数と動作について詳しくは、「[OUTPUT 句 &#40;Transact-SQL&#41;](../../t-sql/queries/output-clause-transact-sql.md)」をご覧ください。
  
 VALUES  
 追加するデータ値のリストを 1 つ以上指定します。 *column_list* (指定されている場合) またはテーブル内の各列ごとに 1 つのデータ値が必要です。 値リストは、かっこで囲む必要があります。  
  
 値リスト内の値がテーブル内の列と同じ順序で並んでいない場合、またはテーブル内のすべての列に対応していない場合、*column_list* を使用して、各入力値を格納する列を明示的に指定する必要があります。  
  
 [!INCLUDE[tsql](../../includes/tsql-md.md)] 行コンストラクター (テーブル値コンストラクターとも呼ばれる) を使用すると、単一の INSERT ステートメント内に複数の行を指定できます。 行コンストラクターは、かっこで囲まれ、コンマで区切られた複数の値リストを含む単一の VALUES 句で構成されています。 詳細については、「[テーブル値コンストラクター &#40;Transact-SQL&#41;](../../t-sql/queries/table-value-constructor-transact-sql.md)」を参照してください。  

> [!NOTE]  
> Azure Synapse Analytics で挿入できる値は、定数リテラル値または変数参照のみです。 リテラル以外を挿入するには、変数を非定数値に設定して、変数を挿入します。

 DEFAULT  
 [!INCLUDE[ssDE](../../includes/ssde-md.md)]によって、列に対して定義されている既定値が読み込まれます。 既定値がなく、列に対して NULL が許可されている場合は、NULL が挿入されます。 **timestamp** 型が定義されている列には、次のタイムスタンプ値が挿入されます。 DEFAULT は ID 列には有効ではありません。  
  
 *式 (expression)*  
 定数、変数、または式を指定します。 式には EXECUTE ステートメントを含めることができません。  
  
 Unicode 文字データ型 **nchar**、**nvarchar**、**ntext** を参照している場合は、'*expression*' の前に大文字の 'N' を付ける必要があります。 'N' が指定されていない場合、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、文字列はデータベースまたは列の既定の照合順序に対応するコード ページに変換されます。 文字列がこのコード ページにない場合は、失われます。  
  
 *derived_table*  
 テーブルに読み込まれるデータの行を返す、有効な SELECT ステートメントを指定します。 SELECT ステートメントには、共通テーブル式 (CTE) を含めることはできません。  
  
 *execute_statement*  
 SELECT ステートメントまたは READTEXT ステートメントでデータを返す有効な EXECUTE ステートメントです。 詳細については、「 [EXECUTE &#40;Transact-SQL&#41;](../../t-sql/language-elements/execute-transact-sql.md)」を参照してください。  
  
 INSERT...EXEC ステートメントでは、EXECUTE ステートメントの RESULT SETS オプションを指定することができません。  
  
 *execute_statement* を INSERT で使用する場合、各結果セットに、テーブルの列または *column_list* の列との互換性が必要です。  
  
 *execute_statement* を使用して、同じサーバー上またはリモート サーバー上で、ストアド プロシージャを実行できます。 リモート サーバーのプロシージャが実行されると、結果セットがローカル サーバーに返され、ローカル サーバーのテーブルに読み込まれます。 分散トランザクションでは、接続で複数のアクティブな結果セット (MARS) が有効になっている場合、*execute_statement* をループバック リンク サーバーに対して実行できません。  
  
 *execute_statement* が READTEXT ステートメントでデータを返す場合、各 READTEXT ステートメントは最大で 1 MB (1024 KB) のデータを返すことができます。 また *execute_statement* は、拡張プロシージャで使用することもできます。 *execute_statement* は、拡張プロシージャのメイン スレッドによって返されたデータを挿入します。ただし、メイン スレッド以外のスレッドからの出力は挿入しません。  
  
 テーブル値パラメーターは、INSERT EXEC ステートメントの対象として指定できませんが、INSERT EXEC 文字列またはストアド プロシージャにソースとして指定できます。 詳細については、「[テーブル値パラメーターの使用 &#40;Database Engine&#41;](../../relational-databases/tables/use-table-valued-parameters-database-engine.md)」を参照してください。  
  
 \<dml_table_source>  
 INSERT、UPDATE、DELETE、または MERGE ステートメントの OUTPUT 句で返された行 (WHERE 句でフィルター処理される場合もあります) を対象のテーブルに挿入するように指定します。 \<dml_table_source> を指定する場合は、外部の INSERT ステートメントの対象が次の制限を満たしている必要があります。 
  
-   ビューではなくベース テーブルである必要があります。  
  
-   リモート テーブルは使用できません。  
  
-   トリガーが定義されているテーブルは使用できません。  
  
-   主キー/外部キーのリレーションシップに加えることはできません。  
  
-   マージ レプリケーションや、トランザクション レプリケーションの更新可能なサブスクリプションに加えることはできません。  
  
 データベース互換性レベルを 100 以上に設定する必要があります。 詳細については、「[OUTPUT 句 &#40;Transact-SQL&#41;](../../t-sql/queries/output-clause-transact-sql.md)」を参照してください。  
  
 \<select_list>  
 Output 句から返された列のどれを挿入するかを指定するコンマ区切りのリストです。 \<select_list> 内の列は、値の挿入先である列と互換である必要があります。 \<select_list> では、集計関数または TEXTPTR を参照できません。 
  
> [!NOTE]  
>  SELECT リストに含まれる変数は、\<dml_statement_with_output_clause> で加えられる変更に関係なく、その元の値を参照します。  
  
 \<dml_statement_with_output_clause>  
 影響を受ける行を OUTPUT 句で返す有効な INSERT、UPDATE、DELETE、または MERGE ステートメントです。 このステートメントには WITH 句を指定できず、リモート テーブルまたはパーティション ビューを対象にすることもできません。 UPDATE または DELETE を指定する場合、カーソルベースの UPDATE または DELETE は指定できません。 ソース行を、入れ子になった DML ステートメントとして参照することはできません。  
  
 WHERE \<search_condition>  
 \<dml_statement_with_output_clause> から返される行をフィルター処理する有効な \<search_condition> を含む WHERE 句です。 詳しくは、「[検索条件 &#40;Transact-SQL&#41;](../../t-sql/queries/search-condition-transact-sql.md)」をご覧ください。 このコンテキストで使用される \<search_condition> には、サブクエリ、データにアクセスするスカラー ユーザー定義関数、集計関数、TEXTPTR、またはフルテキスト検索述語を含めることができません。 
  
 DEFAULT VALUES  
 **適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。  
  
 新しい行が、各列に対して定義されている既定値で構成されることを指定します。  
  
 BULK  
**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。  
  
 外部ツールでバイナリ データ ストリームをアップロードする際に使用されます。 このオプションは、[!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)]、SQLCMD、OSQL などのツールや、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client などのデータ アクセス アプリケーション プログラミング インターフェイスで使用することは想定されていません。  
  
 FIRE_TRIGGERS  
 **適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。  
  
 バイナリ データ ストリームのアップロード処理中に、変換先テーブルで定義されている挿入トリガーを実行することを指定します。 詳細については、「[BULK INSERT &#40;Transact-SQL&#41;](../../t-sql/statements/bulk-insert-transact-sql.md)」を参照してください。  
  
 CHECK_CONSTRAINTS  
 **適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。  
  
 バイナリ データ ストリームのアップロード処理中に、対象テーブルまたはビューに対するすべての制約を検証します。 詳細については、「[BULK INSERT &#40;Transact-SQL&#41;](../../t-sql/statements/bulk-insert-transact-sql.md)」を参照してください。  
  
 KEEPNULLS  
 **適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。  
  
 バイナリ データ ストリームのアップロード処理中に、空の列の NULL 値を保持します。 詳細については、「[一括インポート中の NULL の保持または既定値の使用 &#40;SQL Server&#41;](../../relational-databases/import-export/keep-nulls-or-use-default-values-during-bulk-import-sql-server.md)」をご覧ください。  
  
 KILOBYTES_PER_BATCH = kilobytes_per_batch  
 バッチあたりのデータの概算キロバイト数 (KB) を *kilobytes_per_batch* として指定します。 詳細については、「[BULK INSERT &#40;Transact-SQL&#41;](../../t-sql/statements/bulk-insert-transact-sql.md)」を参照してください。  
  
 ROWS_PER_BATCH =*rows_per_batch*  
 **適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。  
  
 バイナリ データ ストリーム内のデータ行の概算数を指定します。 詳細については、「[BULK INSERT &#40;Transact-SQL&#41;](../../t-sql/statements/bulk-insert-transact-sql.md)」を参照してください。  
  
> [!NOTE]
>  列リストが指定されていない場合、構文エラーが発生します。  

## <a name="remarks"></a>解説  
SQL グラフ テーブルへのデータの挿入に固有の情報については、「[INSERT (SQL グラフ)](../../t-sql/statements/insert-sql-graph.md)」をご覧ください。 

## <a name="best-practices"></a>ベスト プラクティス  
 @@ROWCOUNT 関数を使用して、クライアント アプリケーションに挿入される行数を返します。 詳細については、「[@@ROWCOUNT &#40;Transact-SQL&#41;](../../t-sql/functions/rowcount-transact-sql.md)」を参照してください。  
  
### <a name="best-practices-for-bulk-importing-data"></a>データの一括インポートに関するベスト プラクティス  
  
#### <a name="using-insert-intoselect-to-bulk-import-data-with-minimal-logging-and-parallelism"></a>INSERT INTO...SELECT を使用したデータ一括インポート時の最小ログ記録と並列処理 
`INSERT INTO <target_table> SELECT <columns> FROM <source_table>` を使用すると、最小ログ記録を行って、1 つのテーブル (ステージング テーブルなど) から別のテーブルに多数の行を効率的に転送できます。 最小ログ記録を行うと、ステートメントのパフォーマンスが向上します。また、トランザクションの実行中に、使用可能なトランザクション ログ領域がこの操作でいっぱいになる可能性を低減します。  
  
このステートメントの最小ログ記録には、次の要件があります。  
-   データベース復旧モデルが単純復旧モデルまたは一括ログ復旧モデルに設定されている。  
-   対象テーブルが、空のヒープか、空でないヒープである。  
-   対象テーブルがレプリケーションで使用されない。  
-   対象テーブルに対して `TABLOCK` のヒントが指定されている。  
  
MERGE ステートメントでの挿入操作の結果としてヒープに挿入される行についても、最小ログ記録が行われる場合があります。  
  
制限の緩い一括更新 (BU) ロックを保持する `BULK INSERT` ステートメントとは異なり、`TABLOCK` ヒントが指定された `INSERT INTO … SELECT` を使用すると、テーブルに対する排他 (X) ロックが保持されます。 つまり、複数の挿入操作を同時に実行して行を挿入することはできません。 

ただし、[!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] およびデータベース互換性レベル 130 以降、ヒープまたはクラスター化列ストア インデックス (CCI) に挿入するときに、1 つの `INSERT INTO … SELECT` ステートメントを並列実行できるようになりました。 `TABLOCK` ヒントを使用すると、並列挿入を行うことができます。  

上記のステートメントの並列処理には、最小ログ記録の要件と同様の次の要件があります。  
-   対象テーブルが、空のヒープか、空でないヒープである。  
-   対象テーブルにクラスター化列ストア インデックス (CCI) はあるが非クラスター化インデックスはない。  
-   対象テーブルに IDENTITY_INSERT が OFF に設定された ID 列がない。  
-   対象テーブルに対して `TABLOCK` のヒントが指定されている。

最小ログ記録と並列挿入の要件が満たされているシナリオでは、両方の機能強化が連携し、データ ロード操作の最大スループットが確保されます。

> [!NOTE]
> ローカル一時テーブル (# プレフィックスで識別されます) とグローバル一時テーブル (## プレフィックスで識別されます) への挿入は、TABLOCK ヒントを使用した並列処理にも有効です。
  
#### <a name="using-openrowset-and-bulk-to-bulk-import-data"></a>OPENROWSET および BULK によるデータの一括インポート  
 OPENROWSET 関数では次のテーブル ヒントを使用できます。これらのテーブル ヒントにより、一括読み込みの最適化を INSERT ステートメントで利用できます。  
  
-   `TABLOCK` ヒントを使用すると、挿入操作のログ レコード数を最小化できます。 データベース復旧モデルが単純復旧モデルまたは一括ログ復旧モデルに設定されている必要があります。また、対象テーブルはレプリケーションで使用できません。 詳細については、「[一括インポートで最小ログ記録を行うための前提条件](../../relational-databases/import-export/prerequisites-for-minimal-logging-in-bulk-import.md)」次を参照してください。  
-   `TABLOCK` ヒントを使用すると、並列挿入操作を行うことができます。 対象テーブルは、非クラスター化インデックスのないヒープまたはクラスター化列ストア インデックス (CCI) であり、対象テーブルに ID 列を指定することはできません。  
-   `IGNORE_CONSTRAINTS` ヒントを使用すると、FOREIGN KEY および CHECK 制約チェックを一時的に無効にすることができます。  
-   `IGNORE_TRIGGERS` ヒントを使用すると、トリガーの実行を一時的に無効にすることができます。  
-   `KEEPDEFAULTS` ヒントを使用すると、データ レコードにテーブルの列値が含まれていない場合に、NULL の代わりにテーブル列の既定値を挿入できます。  
-   `KEEPIDENTITY` ヒントを使用すると、インポートしたデータ ファイルの ID 値を対象テーブルの ID 列に使用できます。  
  
これらの最適化は、`BULK INSERT` コマンドで使用できるものと似ています。 詳細については、「[テーブル ヒント &#40;Transact-SQL&#41;](../../t-sql/queries/hints-transact-sql-table.md)」を参照してください。  
  
## <a name="data-types"></a>データ型  
 行の挿入時には、次のデータ型の動作を考慮してください。  
  
-   **char**、**varchar**、**varbinary** 型の列に値が読み込まれる場合、後続の空白 (**char** と **varchar** の場合は空白、**varbinary** の場合は 0) の埋め込みや切り捨ては、テーブルが作成されたときに列に対して定義された SET ANSI_PADDING の設定値によって決まります。 詳細については、「[SET ANSI_PADDING &#40;Transact-SQL&#41;](../../t-sql/statements/set-ansi-padding-transact-sql.md)」を参照してください。  
  
     次の表は、SET ANSI_PADDING OFF の既定の操作を示します。  
  
    |データ型|既定の操作|  
    |---------------|-----------------------|  
    |**char**|定義された列幅になるように値に空白を埋めます。|  
    |**varchar**|空白以外の最終文字に達するまで、後続の空白を削除します。文字列が空白だけで構成されている場合は、1 つのスペース文字を残して、後続の空白を削除します。|  
    |**varbinary**|後続の 0 を削除します。|  
  
-   **varchar** 型または **text** 型の列に空文字列 (' ') が読み込まれると、既定の操作では、長さが 0 の文字列が読み込まれます。  
  
-   **text** 型列または **image** 型列に NULL 値を挿入した場合、有効なテキスト ポインターは作成されず、また、8 KB のテキスト ページもあらかじめ割り当てられません。  
  
-   **uniqueidentifier** 型で作成される列は、特別にフォーマットされた 16 バイトのバイナリ値を格納します。 ID 列の場合とは異なり、[!INCLUDE[ssDE](../../includes/ssde-md.md)]は、**uniqueidentifier** 型の列に対して自動的に値を生成しません。 挿入操作の際、**uniqueidentifier** 列には、**uniqueidentifier** 型の変数と、*xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx* (ハイフンを含む 36 文字で、*x* は 0 ～ 9 または a ～ f の範囲の 16 進数値) という形式の文字列定数を使用できます。 たとえば、**uniqueidentifier** 変数または列には、6F9619FF-8B86-D011-B42D-00C04FC964FF という値を指定できます。 GUID を取得するには、[NEWID()](../../t-sql/functions/newid-transact-sql.md) 関数を使用します。  
  
### <a name="inserting-values-into-user-defined-type-columns"></a>ユーザー定義型の列への値の挿入  
 ユーザー定義型の列に値を挿入するには、次のようにします。  
  
-   そのユーザー定義型の値を指定します。  
  
-   ユーザー定義型で [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] システム データ型からの暗黙的または明示的な変換がサポートされている場合は、そのシステム データ型の値を指定します。 次の例は、文字列からの明示的な変換によって、ユーザー定義型 `Point` の列に値を挿入する方法を示しています。  
  
    ```sql
    INSERT INTO Cities (Location)  
    VALUES ( CONVERT(Point, '12.3:46.2') );  
    ```  
  
     明示的な変換を実行することなく、バイナリ値を指定することもできます。これは、すべてのユーザー定義型が、バイナリからの暗黙的な変換が可能であるためです。  
  
-   そのユーザー定義型の値を返すユーザー定義関数を呼び出します。 次の例では、ユーザー定義関数 `CreateNewPoint()` を使用してユーザー定義型 `Point` の新しい値を作成し、この値を `Cities` テーブルに挿入します。  
  
    ```sql
    INSERT INTO Cities (Location)  
    VALUES ( dbo.CreateNewPoint(x, y) );  
    ```  
  
## <a name="error-handling"></a>エラー処理  
 TRY...CATCH 構造でステートメントを指定することで、INSERT ステートメントのエラー処理を実装できます。  
  
 INSERT ステートメントが制約やルールに違反していたり、その値が列のデータ型と互換性を持たない場合は、ステートメントが失敗し、エラー メッセージが返されます。  
  
 INSERT が SELECT または EXECUTE で複数の行を読み込んでいる場合、読み込まれている値でルールや制約の違反が発生すると、ステートメントが停止し、行は読み込まれません。  
  
 INSERT ステートメントの中で、式の評価中に算術エラー (オーバーフロー、0 による除算、またはドメイン エラー) が発生すると、[!INCLUDE[ssDE](../../includes/ssde-md.md)]では、SET ARITHABORT が ON に設定されている場合と同様に、これらのエラーが処理されます。 バッチは停止し、エラー メッセージが返されます。 SET ARITHABORT と SET ANSI_WARNINGS を OFF に設定して式を評価中に、INSERT、DELETE、または UPDATE ステートメントで算術演算エラー、オーバーフロー、0 除算、またはドメイン エラーが検出されると、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では NULL 値が挿入または更新されます。 出力先の列で NULL 値が許容されない場合は、挿入または更新処理は失敗し、エラーが返されます。  
  
## <a name="interoperability"></a>相互運用性  
 テーブルやビューを対象とする INSERT 操作で `INSTEAD OF` トリガーが定義されている場合は、INSERT ステートメントの代わりにトリガーが実行されます。 `INSTEAD OF` トリガーの詳細については、「[CREATE TRIGGER &#40;Transact-SQL&#41;](../../t-sql/statements/create-trigger-transact-sql.md)」を参照してください。  
  
## <a name="limitations-and-restrictions"></a>制限事項と制約事項  
 リモート テーブルに値を挿入するとき、すべての列のすべての値が指定されている場合を除いて、指定された値をどの列に挿入するかをユーザーが指定する必要があります。  
  
 TOP を INSERT と共に使用する場合、参照される行は任意の順序に並べられません。また、このステートメントで、ORDER BY 句を直接指定することはできません。 TOP を使用して意味のある順序で行を挿入する必要がある場合は、サブセレクト ステートメントで ORDER BY 句を指定して TOP を使用する必要があります。 例については、後の「例」のセクションを参照してください。
 
SELECT と ORDER BY を使って行を作成する INSERT クエリでは、ID 値の計算方法は保証されますが、行の挿入順序は保証されません。

Parallel Data Warehouse では、ORDER BY 句は、TOP も一緒に指定しない限り、VIEWS、CREATE TABLE AS SELECT、INSERT SELECT、インライン関数、派生テーブル、サブクエリ、共通テーブル式では無効です。
  
## <a name="logging-behavior"></a>ログ記録の動作  
 INSERT ステートメントは、常に完全にログに記録されます。ただし、BULK キーワードを指定して OPENROWSET 関数を使用している場合、または `INSERT INTO <target_table> SELECT <columns> FROM <source_table>` を使用している場合は除きます。 これらの操作のログへの記録は最小限にできます。 詳細については、前の「データの一括読み込みの推奨事項」を参照してください。  
  
## <a name="security"></a>セキュリティ  
 リンク サーバーに接続する場合、送信側サーバーは受信側サーバーに接続するためにログイン名とパスワードをリンク サーバーに代わって提供します。 この接続を機能させるには、[sp_addlinkedsrvlogin](../../relational-databases/system-stored-procedures/sp-addlinkedsrvlogin-transact-sql.md) を使用して、リンク サーバー間でログイン マッピングを作成する必要があります。  
  
 OPENROWSET(BULK...) を使用するにあたっては、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] で権限借用がどのように処理されるかを理解しておくことが重要です。 詳しくは、「[BULK INSERT または OPENROWSET&#40;BULK...&#41; を使用した一括データのインポート &#40;SQL Server&#41;](../../relational-databases/import-export/import-bulk-data-by-using-bulk-insert-or-openrowset-bulk-sql-server.md)」の「セキュリティに関する考慮事項」をご覧ください。  
  
### <a name="permissions"></a>アクセス許可  
 対象のテーブルに対する INSERT 権限が必要です。  
  
 INSERT 権限は、既定では `sysadmin` 固定サーバー ロール、`db_owner` および `db_datawriter` 固定データベース ロールのメンバー、およびテーブル所有者に与えられています。 `sysadmin`、`db_owner`、`db_securityadmin` ロールのメンバーと、テーブル所有者は、他のユーザーに権限を譲渡できます。  
  
 OPENROWSET 関数の BULK オプションで INSERT を実行するには、`sysadmin` 固定サーバー ロールまたは `bulkadmin` 固定サーバー ロールのメンバーであることが必要です。  
  
##  <a name="examples"></a><a name="InsertExamples"></a> 使用例  
  
|カテゴリ|主な構文要素|  
|--------------|------------------------------|  
|[基本構文](#BasicSyntax)|INSERT • テーブル値コンストラクター|  
|[列の値を処理する](#ColumnValues)|IDENTITY • NEWID • 既定値 • ユーザー定義型|  
|[他のテーブルのデータを挿入する](#OtherTables)|INSERT...SELECT • INSERT...EXECUTE • WITH 共通テーブル式 • TOP • OFFSET FETCH|  
|[標準的なテーブル以外の対象オブジェクトを指定する](#TargetObjects)|ビュー • テーブル変数|  
|[リモート テーブルに行を挿入する](#RemoteTables)|リンク サーバー、OPENQUERY 行セット関数、OPENDATASOURCE 行セット関数|  
|[テーブルまたはデータ ファイルのデータを一括読み込みする](#BulkLoad)|INSERT...SELECT • OPENROWSET 関数|  
|[ヒントを使用してクエリ オプティマイザーの既定の動作をオーバーライドする](#TableHints)|テーブル ヒント|  
|[INSERT ステートメントの結果をキャプチャする](#CaptureResults)|OUTPUT 句|  
  
###  <a name="basic-syntax"></a><a name="BasicSyntax"></a> 基本構文  
 このセクションの例では、最低限必要な構文を使用して INSERT ステートメントの基本機能を示します。  
  
#### <a name="a-inserting-a-single-row-of-data"></a>A. 1 行のデータを挿入する  
 次の例では、[!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] データベースの `Production.UnitMeasure` テーブルに 1 行を挿入します。 このテーブルの列は、`UnitMeasureCode`、`Name`、および `ModifiedDate` です。 すべての列の値が指定され、テーブルの列と同じ順序で並んでいるため、列名を列リストで指定する必要はありません *。*  
  
```sql
INSERT INTO Production.UnitMeasure  
VALUES (N'FT', N'Feet', '20080414');  
```  
  
#### <a name="b-inserting-multiple-rows-of-data"></a>B. 複数行のデータを挿入する  
 次の例では、単一の INSERT ステートメントで[テーブル値コンストラクター](../../t-sql/queries/table-value-constructor-transact-sql.md)を使用して、[!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] データベースの `Production.UnitMeasure` テーブルに 3 行を挿入します。 すべての列の値が指定され、テーブルの列と同じ順序で並んでいるため、列名を列リストで指定する必要はありません。  
  
```sql
INSERT INTO Production.UnitMeasure  
VALUES (N'FT2', N'Square Feet ', '20080923'), (N'Y', N'Yards', '20080923')
    , (N'Y3', N'Cubic Yards', '20080923');  
```  
  
#### <a name="c-inserting-data-that-is-not-in-the-same-order-as-the-table-columns"></a>C. テーブルの列と順序が異なるデータを挿入する  
 次の例では、列リストを使用して、各列に挿入する値を明示的に指定します。 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] データベースの `Production.UnitMeasure` テーブルの列の順序は、`UnitMeasureCode`、`Name`、`ModifiedDate` です。ただし、*column_list* では列がその順序で並んでいません。  
  
```sql
INSERT INTO Production.UnitMeasure (Name, UnitMeasureCode,  
    ModifiedDate)  
VALUES (N'Square Yards', N'Y2', GETDATE());  
```  
  
###  <a name="handling-column-values"></a><a name="ColumnValues"></a> 列の値を処理する  
 このセクションの例では、IDENTITY プロパティ、DEFAULT 値、または **uniqueidentifer** 列やユーザー定義型の列などのデータ型で定義された列に値を挿入する方法を示します。  
  
#### <a name="d-inserting-data-into-a-table-with-columns-that-have-default-values"></a>D. 列が既定値に設定されているテーブルにデータを挿入する  
 次の例では、値が自動生成される列や既定値が設定される列を使用して、テーブルに行を挿入します。 `Column_1` は、文字列と `column_2` に挿入される値を連結して値を自動的に生成する計算列です。 `Column_2` は、既定の制約で定義されています。 この列の値が指定されていない場合、既定値が使用されます。 `Column_3` は **rowversion** データ型で定義されており、一意の増分する 2 進数を自動的に生成します。 `Column_4` は、値の自動生成を行いません。 この列の値が指定されていない場合、NULL が挿入されます。 INSERT ステートメントでは、すべての列ではなく一部の列の値を含む行を挿入します。 最後の INSERT ステートメントでは、どの列も指定されていないため、DEFAULT VALUES 句を使用して既定値のみが挿入されます。  
  
```sql
CREATE TABLE dbo.T1   
(  
    column_1 AS 'Computed column ' + column_2,   
    column_2 varchar(30)   
        CONSTRAINT default_name DEFAULT ('my column default'),  
    column_3 rowversion,  
    column_4 varchar(40) NULL  
);  
GO  
INSERT INTO dbo.T1 (column_4)   
    VALUES ('Explicit value');  
INSERT INTO dbo.T1 (column_2, column_4)   
    VALUES ('Explicit value', 'Explicit value');  
INSERT INTO dbo.T1 (column_2)   
    VALUES ('Explicit value');  
INSERT INTO T1 DEFAULT VALUES;   
GO  
SELECT column_1, column_2, column_3, column_4  
FROM dbo.T1;  
GO  
```  
  
#### <a name="e-inserting-data-into-a-table-with-an-identity-column"></a>E. ID 列を持つテーブルにデータを挿入する  
 次の例では、ID 列にデータを挿入する方法をいくつか示します。 最初の 2 つの INSERT ステートメントで、新規の行に対して ID 値が生成されます。 3 番目の INSERT ステートメントは、SET IDENTITY_INSERT ステートメントで設定された列の IDENTITY プロパティをオーバーライドし、ID 列に値を明示的に挿入します。  
  
```sql
CREATE TABLE dbo.T1 ( column_1 int IDENTITY, column_2 VARCHAR(30));  
GO  
INSERT T1 VALUES ('Row #1');  
INSERT T1 (column_2) VALUES ('Row #2');  
GO  
SET IDENTITY_INSERT T1 ON;  
GO  
INSERT INTO T1 (column_1,column_2)   
    VALUES (-99, 'Explicit identity value');  
GO  
SELECT column_1, column_2  
FROM T1;  
GO  
```  
  
#### <a name="f-inserting-data-into-a-uniqueidentifier-column-by-using-newid"></a>F. NEWID() を使用して uniqueidentifier 列にデータを挿入する  
 次の例では、[NEWID](../../t-sql/functions/newid-transact-sql.md)() 関数を使用して、`column_2` の GUID を取得します。 ID 列とは異なり、[!INCLUDE[ssDE](../../includes/ssde-md.md)]では [uniqueidentifier](../../t-sql/data-types/uniqueidentifier-transact-sql.md) 型の列に対して自動的に値が生成されません。2 番目の `INSERT` ステートメントを参照してください。  
  
```sql
CREATE TABLE dbo.T1   
(  
    column_1 int IDENTITY,   
    column_2 uniqueidentifier,  
);  
GO  
INSERT INTO dbo.T1 (column_2)   
    VALUES (NEWID());  
INSERT INTO T1 DEFAULT VALUES;   
GO  
SELECT column_1, column_2  
FROM dbo.T1;  
```  
  
#### <a name="g-inserting-data-into-user-defined-type-columns"></a>G. ユーザー定義型の列にデータを挿入する  
 次の [!INCLUDE[tsql](../../includes/tsql-md.md)] ステートメントでは、`Points` テーブルの `PointValue` 列に 3 行を挿入します。 この列は、[CLR ユーザー定義型](../../relational-databases/clr-integration-database-objects-user-defined-types/clr-user-defined-types.md) (UDT) を使用しています。 `Point` データ型は、UDT のプロパティとして公開されている整数値 X と Y で構成されます。 コンマ区切りの X と Y の値を `Point` 型にキャストするには、CAST 関数または CONVERT 関数のいずれかを使用する必要があります。 最初の 2 つのステートメントでは、CONVERT 関数を使用し、3 つ目のステートメントでは CAST 関数を使用して、文字列値を `Point` 型に変換しています。 詳しくは、「[UDT データの操作](../../relational-databases/clr-integration-database-objects-user-defined-types/working-with-user-defined-types-manipulating-udt-data.md)」をご覧ください。  
  
```sql
INSERT INTO dbo.Points (PointValue) VALUES (CONVERT(Point, '3,4'));  
INSERT INTO dbo.Points (PointValue) VALUES (CONVERT(Point, '1,5'));  
INSERT INTO dbo.Points (PointValue) VALUES (CAST ('1,99' AS Point));  
```  
  
###  <a name="inserting-data-from-other-tables"></a><a name="OtherTables"></a> 他のテーブルのデータを挿入する  
 このセクションの例では、あるテーブルの行を別のテーブルに挿入する方法を示します。  
  
#### <a name="h-using-the-select-and-execute-options-to-insert-data-from-other-tables"></a>H. SELECT および EXECUTE オプションを使用して他のテーブルのデータを挿入する  
 次の例では、INSERT...SELECT または INSERT...EXECUTE を使用して、あるテーブルのデータを別のテーブルに挿入する方法を示します。 各方法は、列リストに式とリテラル値を含む複数のテーブルを参照する SELECT ステートメントに基づきます。  
  
 1 番目の INSERT ステートメントでは、SELECT ステートメントを使用して [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] データベースのソース テーブル (`Employee`、`SalesPerson`、および `Person`) からデータを取得し、その結果セットを `EmployeeSales` テーブルに格納します。 2 番目の INSERT ステートメントは、EXECUTE 句を使用して SELECT ステートメントを含むストアド プロシージャを呼び出します。3 番目の INSERT ステートメントは、EXECUTE 句を使用して SELECT ステートメントをリテラル文字列として参照します。  
  
```sql
CREATE TABLE dbo.EmployeeSales  
( DataSource   varchar(20) NOT NULL,  
  BusinessEntityID   varchar(11) NOT NULL,  
  LastName     varchar(40) NOT NULL,  
  SalesDollars money NOT NULL  
);  
GO  
CREATE PROCEDURE dbo.uspGetEmployeeSales   
AS   
    SET NOCOUNT ON;  
    SELECT 'PROCEDURE', sp.BusinessEntityID, c.LastName,   
        sp.SalesYTD   
    FROM Sales.SalesPerson AS sp    
    INNER JOIN Person.Person AS c  
        ON sp.BusinessEntityID = c.BusinessEntityID  
    WHERE sp.BusinessEntityID LIKE '2%'  
    ORDER BY sp.BusinessEntityID, c.LastName;  
GO  
--INSERT...SELECT example  
INSERT INTO dbo.EmployeeSales  
    SELECT 'SELECT', sp.BusinessEntityID, c.LastName, sp.SalesYTD   
    FROM Sales.SalesPerson AS sp  
    INNER JOIN Person.Person AS c  
        ON sp.BusinessEntityID = c.BusinessEntityID  
    WHERE sp.BusinessEntityID LIKE '2%'  
    ORDER BY sp.BusinessEntityID, c.LastName;  
GO  
--INSERT...EXECUTE procedure example  
INSERT INTO dbo.EmployeeSales   
EXECUTE dbo.uspGetEmployeeSales;  
GO  
--INSERT...EXECUTE('string') example  
INSERT INTO dbo.EmployeeSales   
EXECUTE   
('  
SELECT ''EXEC STRING'', sp.BusinessEntityID, c.LastName,   
    sp.SalesYTD   
    FROM Sales.SalesPerson AS sp   
    INNER JOIN Person.Person AS c  
        ON sp.BusinessEntityID = c.BusinessEntityID  
    WHERE sp.BusinessEntityID LIKE ''2%''  
    ORDER BY sp.BusinessEntityID, c.LastName  
');  
GO  
--Show results.  
SELECT DataSource,BusinessEntityID,LastName,SalesDollars  
FROM dbo.EmployeeSales;  
```  
  
#### <a name="i-using-with-common-table-expression-to-define-the-data-inserted"></a>I. WITH 共通テーブル式を使用して挿入するデータを定義する  
 次の例では、[!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] データベースに `NewEmployee` テーブルを作成します。 共通テーブル式 (`EmployeeTemp`) で、`NewEmployee` テーブルに挿入する 1 つ以上のテーブルの行を定義します。 INSERT ステートメントは、共通テーブル式の列を参照します。  
  
```sql
CREATE TABLE HumanResources.NewEmployee  
(  
    EmployeeID int NOT NULL,  
    LastName nvarchar(50) NOT NULL,  
    FirstName nvarchar(50) NOT NULL,  
    PhoneNumber Phone NULL,  
    AddressLine1 nvarchar(60) NOT NULL,  
    City nvarchar(30) NOT NULL,  
    State nchar(3) NOT NULL,   
    PostalCode nvarchar(15) NOT NULL,  
    CurrentFlag Flag  
);  
GO  
WITH EmployeeTemp (EmpID, LastName, FirstName, Phone,   
                   Address, City, StateProvince,   
                   PostalCode, CurrentFlag)  
AS (SELECT   
       e.BusinessEntityID, c.LastName, c.FirstName, pp.PhoneNumber,  
       a.AddressLine1, a.City, sp.StateProvinceCode,   
       a.PostalCode, e.CurrentFlag  
    FROM HumanResources.Employee e  
        INNER JOIN Person.BusinessEntityAddress AS bea  
        ON e.BusinessEntityID = bea.BusinessEntityID  
        INNER JOIN Person.Address AS a  
        ON bea.AddressID = a.AddressID  
        INNER JOIN Person.PersonPhone AS pp  
        ON e.BusinessEntityID = pp.BusinessEntityID  
        INNER JOIN Person.StateProvince AS sp  
        ON a.StateProvinceID = sp.StateProvinceID  
        INNER JOIN Person.Person as c  
        ON e.BusinessEntityID = c.BusinessEntityID  
    )  
INSERT INTO HumanResources.NewEmployee   
    SELECT EmpID, LastName, FirstName, Phone,   
           Address, City, StateProvince, PostalCode, CurrentFlag  
    FROM EmployeeTemp;  
GO  
```  
  
#### <a name="j-using-top-to-limit-the-data-inserted-from-the-source-table"></a>J. TOP を使用してソース テーブルから挿入されるデータを制限する  
 次の例では、`EmployeeSales` テーブルを作成し、ランダムに選ばれた上位 5 人の従業員の名前と年度累計売り上げデータを [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] データベースの `HumanResources.Employee` テーブルから挿入します。 INSERT ステートメントにより、`SELECT` ステートメントで返された任意の 5 行が選択されます。 OUTPUT 句は、`EmployeeSales` テーブルに挿入される行を表示します。 上位 5 人の従業員を特定するために SELECT ステートメントの ORDER BY 句を使用することはありません。  
  
```sql
CREATE TABLE dbo.EmployeeSales  
( EmployeeID   nvarchar(11) NOT NULL,  
  LastName     nvarchar(20) NOT NULL,  
  FirstName    nvarchar(20) NOT NULL,  
  YearlySales  money NOT NULL  
 );  
GO  
INSERT TOP(5)INTO dbo.EmployeeSales  
    OUTPUT inserted.EmployeeID, inserted.FirstName, 
        inserted.LastName, inserted.YearlySales  
    SELECT sp.BusinessEntityID, c.LastName, c.FirstName, sp.SalesYTD   
    FROM Sales.SalesPerson AS sp  
    INNER JOIN Person.Person AS c  
        ON sp.BusinessEntityID = c.BusinessEntityID  
    WHERE sp.SalesYTD > 250000.00  
    ORDER BY sp.SalesYTD DESC;  
```  
  
 TOP を使用して意味のある順序で行を挿入する必要がある場合は、次の例に示すように、サブセレクト ステートメントで ORDER BY を指定して TOP を使用する必要があります。 OUTPUT 句は、`EmployeeSales` テーブルに挿入される行を表示します。 ランダムな行の代わりに、ORDER BY 句の結果に基づいて、上位 5 人の従業員が挿入されます。  
  
```sql
INSERT INTO dbo.EmployeeSales  
    OUTPUT inserted.EmployeeID, inserted.FirstName, 
        inserted.LastName, inserted.YearlySales  
    SELECT TOP (5) sp.BusinessEntityID, c.LastName, c.FirstName, sp.SalesYTD   
    FROM Sales.SalesPerson AS sp  
    INNER JOIN Person.Person AS c  
        ON sp.BusinessEntityID = c.BusinessEntityID  
    WHERE sp.SalesYTD > 250000.00  
    ORDER BY sp.SalesYTD DESC;  
```  
  
###  <a name="specifying-target-objects-other-than-standard-tables"></a><a name="TargetObjects"></a> 標準的なテーブル以外の対象オブジェクトを指定する  
 このセクションの例では、ビューまたはテーブル変数を指定して行を挿入する方法を示します。  
  
#### <a name="k-inserting-data-by-specifying-a-view"></a>K. ビューを指定してデータを挿入する  
 次の例では、対象オブジェクトとしてビュー名を指定します。しかし、新しい行は基になるベース テーブルに挿入されます。 `INSERT` ステートメント内の値の順番は、ビューの列の順番に一致している必要があります。 詳細については、「[ビューを使用したデータ変更](../../relational-databases/views/modify-data-through-a-view.md)」を参照してください。  
  
```sql
CREATE TABLE T1 ( column_1 int, column_2 varchar(30));  
GO  
CREATE VIEW V1 AS   
SELECT column_2, column_1   
FROM T1;  
GO  
INSERT INTO V1   
    VALUES ('Row 1',1);  
GO  
SELECT column_1, column_2   
FROM T1;  
GO  
SELECT column_1, column_2  
FROM V1;  
GO  
```  
  
#### <a name="l-inserting-data-into-a-table-variable"></a>L. テーブル変数を指定してデータを挿入する  
 次の例では、[!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] データベースの対象オブジェクトとしてテーブル変数を指定します。  
  
```sql
-- Create the table variable.  
DECLARE @MyTableVar table(  
    LocationID int NOT NULL,  
    CostRate smallmoney NOT NULL,  
    NewCostRate AS CostRate * 1.5,  
    ModifiedDate datetime);  
  
-- Insert values into the table variable.  
INSERT INTO @MyTableVar (LocationID, CostRate, ModifiedDate)  
    SELECT LocationID, CostRate, GETDATE() 
    FROM Production.Location  
    WHERE CostRate > 0;  
  
-- View the table variable result set.  
SELECT * FROM @MyTableVar;  
GO  
```  
  
###  <a name="inserting-rows-into-a-remote-table"></a><a name="RemoteTables"></a> リモート テーブルに行を挿入する  
 このセクションの例では、[リンク サーバー](../../relational-databases/system-stored-procedures/sp-addlinkedserver-transact-sql.md)または[行セット関数](../functions/opendatasource-transact-sql.md)を使用してリモート テーブルを参照し、リモートの対象テーブルに行を挿入する方法を示します。  
  
#### <a name="m-inserting-data-into-a-remote-table-by-using-a-linked-server"></a>M. リンク サーバーを使用してリモート テーブルにデータを挿入する  
 次の例では、リモート テーブルに行を挿入します。 [sp_addlinkedserver](../../relational-databases/system-stored-procedures/sp-addlinkedserver-transact-sql.md) を使用してリモート データ ソースへのリンクを作成した後、 *server.catalog.schema.object* という形式の、4 つの要素で構成されたオブジェクト名の一部として、リンク サーバー名 `MyLinkServer` を指定します。  
  
**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。  
  
```sql
USE master;  
GO  
-- Create a link to the remote data source.   
-- Specify a valid server name for @datasrc as 'server_name' 
-- or 'server_nameinstance_name'.  
  
EXEC sp_addlinkedserver @server = N'MyLinkServer',  
    @srvproduct = N' ',  
    @provider = N'SQLNCLI',   
    @datasrc = N'server_name',  
    @catalog = N'AdventureWorks2012';  
GO  
```  
  
```sql
-- Specify the remote data source in the FROM clause using a four-part name   
-- in the form linked_server.catalog.schema.object.  
  
INSERT INTO MyLinkServer.AdventureWorks2012.HumanResources.Department (Name, GroupName)  
VALUES (N'Public Relations', N'Executive General and Administration');  
GO  
```  
  
#### <a name="n-inserting-data-into-a-remote-table-by-using-the-openquery-function"></a>北 OPENQUERY 関数を使用してリモート テーブルにデータを挿入する  
 次の例では、[OPENQUERY](../../t-sql/functions/openquery-transact-sql.md) 行セット関数を指定してリモート テーブルに行を挿入します。 この例では、前の例で作成したリンク サーバー名を使用します。  
  
**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。  
  
```sql
INSERT OPENQUERY (MyLinkServer, 
    'SELECT Name, GroupName 
     FROM AdventureWorks2012.HumanResources.Department')  
VALUES ('Environmental Impact', 'Engineering');  
GO  
```  
  
#### <a name="o-inserting-data-into-a-remote-table-by-using-the-opendatasource-function"></a>O. OPENDATASOURCE 関数を使用してリモート テーブルにデータを挿入する  
 次の例では、[OPENDATASOURCE](../../t-sql/functions/opendatasource-transact-sql.md) 行セット関数を指定してリモート テーブルに行を挿入します。 *server_name* または *server_name\instance_name* という形式を使用して、データ ソースの有効なサーバー名を指定します。  
  
**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。  
  
```sql
-- Use the OPENDATASOURCE function to specify the remote data source.  
-- Specify a valid server name for Data Source using the format 
-- server_name or server_nameinstance_name.  
  
INSERT INTO OPENDATASOURCE('SQLNCLI',  
    'Data Source= <server_name>; Integrated Security=SSPI')  
    .AdventureWorks2012.HumanResources.Department (Name, GroupName)  
    VALUES (N'Standards and Methods', 'Quality Assurance');  
GO  
```  
  
#### <a name="p-inserting-into-an-external-table-created-using-polybase"></a>P. PolyBase を使用して作成された外部のテーブルに挿入する  
 Hadoop または Azure ストレージに SQL Server からのデータをエクスポートします。 最初に、変換先ファイルまたはディレクトリを指す外部テーブルを作成します。 次に、ローカルの SQL Server テーブルからのデータを外部データ ソースをエクスポートするのに INSERT INTO 使用します。 INSERT INTO ステートメントでは、存在しないと、SELECT ステートメントの結果は、指定されたファイルの形式で指定した場所にエクスポートする場合、変換先ファイルまたはディレクトリを作成します。  詳細については、「 [PolyBase 入門](../../relational-databases/polybase/polybase-guide.md)」を参照してください。  
  
**適用対象**: [!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)]  
  
```sql
-- Create an external table.   
CREATE EXTERNAL TABLE [dbo].[FastCustomers2009] (  
        [FirstName] char(25) NOT NULL,   
        [LastName] char(25) NOT NULL,   
        [YearlyIncome] float NULL,   
        [MaritalStatus] char(1) NOT NULL  
)  
WITH (  
        LOCATION='/old_data/2009/customerdata.tbl',  
        DATA_SOURCE = HadoopHDP2,  
        FILE_FORMAT = TextFileFormat,  
        REJECT_TYPE = VALUE,  
        REJECT_VALUE = 0  
);  
  
-- Export data: Move old data to Hadoop while keeping 
-- it query-able via external table.  

INSERT INTO dbo.FastCustomer2009  
SELECT T.* FROM Insured_Customers T1 JOIN CarSensor_Data T2  
ON (T1.CustomerKey = T2.CustomerKey)  
WHERE T2.YearMeasured = 2009 and T2.Speed > 40;  
```  
  
###  <a name="bulk-loading-data-from-tables-or-data-files"></a><a name="BulkLoad"></a> テーブルまたはデータ ファイルのデータを一括読み込みする  
 このセクションの例では、INSERT ステートメントを使用してテーブルにデータを一括読み込みする 2 つの方法を示します。  
  
#### <a name="q-inserting-data-into-a-heap-with-minimal-logging"></a>Q. 最小ログ記録を行ってヒープにデータを挿入する  
 次の例では、新しいテーブル (ヒープ) を作成し、最小ログ記録を使用して、別のテーブルのデータをそのテーブルに挿入します。 この例では、`AdventureWorks2012` データベースの復旧モデルが FULL に設定されていると想定しています。 したがって、最小ログ記録が使用されるようにするために、行を挿入する前に `AdventureWorks2012` データベースの復旧モデルを BULK_LOGGED に設定し、INSERT INTO...SELECT ステートメントの後に FULL に戻しています。 また、対象テーブル `Sales.SalesHistory` に TABLOCK ヒントが指定されています。 これにより、ステートメントが使用するトランザクション ログの領域が最小化され、ステートメントが効率的に実行されるようになります。  
  
```sql
-- Create the target heap.  
CREATE TABLE Sales.SalesHistory(  
    SalesOrderID int NOT NULL,  
    SalesOrderDetailID int NOT NULL,  
    CarrierTrackingNumber nvarchar(25) NULL,  
    OrderQty smallint NOT NULL,  
    ProductID int NOT NULL,  
    SpecialOfferID int NOT NULL,  
    UnitPrice money NOT NULL,  
    UnitPriceDiscount money NOT NULL,  
    LineTotal money NOT NULL,  
    rowguid uniqueidentifier ROWGUIDCOL  NOT NULL,  
    ModifiedDate datetime NOT NULL );  
GO  
-- Temporarily set the recovery model to BULK_LOGGED.  
ALTER DATABASE AdventureWorks2012  
SET RECOVERY BULK_LOGGED;  
GO  
-- Transfer data from Sales.SalesOrderDetail to Sales.SalesHistory  
INSERT INTO Sales.SalesHistory WITH (TABLOCK)  
    (SalesOrderID,   
     SalesOrderDetailID,  
     CarrierTrackingNumber,   
     OrderQty,   
     ProductID,   
     SpecialOfferID,   
     UnitPrice,   
     UnitPriceDiscount,  
     LineTotal,   
     rowguid,   
     ModifiedDate)  
SELECT * FROM Sales.SalesOrderDetail;  
GO  
-- Reset the recovery model.  
ALTER DATABASE AdventureWorks2012  
SET RECOVERY FULL;  
GO  
```  
  
#### <a name="r-using-the-openrowset-function-with-bulk-to-bulk-load-data-into-a-table"></a>R. OPENROWSET 関数を BULK を指定して使用し、テーブルにデータを一括読み込みする  
 次の例は、OPENROWSET 関数を指定することによって、テーブルにデータ ファイルからの行を挿入します。 パフォーマンスを最適化するために、IGNORE_TRIGGERS テーブル ヒントを指定しています。 他の例については、「[BULK INSERT または OPENROWSET&#40;BULK...&#41; を使用した一括データのインポート &#40;SQL Server&#41;](../../relational-databases/import-export/import-bulk-data-by-using-bulk-insert-or-openrowset-bulk-sql-server.md)」をご覧ください。  
  
**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。  
  
```sql
INSERT INTO HumanResources.Department WITH (IGNORE_TRIGGERS) (Name, GroupName)  
SELECT b.Name, b.GroupName   
FROM OPENROWSET (  
    BULK 'C:SQLFilesDepartmentData.txt',  
    FORMATFILE = 'C:SQLFilesBulkloadFormatFile.xml',  
    ROWS_PER_BATCH = 15000)AS b ;  
```  
  
###  <a name="overriding-the-default-behavior-of-the-query-optimizer-by-using-hints"></a><a name="TableHints"></a> ヒントを使用してクエリ オプティマイザーの既定の動作をオーバーライドする  
 このセクションの例では、[テーブル ヒント](../../t-sql/queries/hints-transact-sql-table.md)を使用して、INSERT ステートメントを処理する際のクエリ オプティマイザーの既定の動作を一時的にオーバーライドする方法を示します。  
  
> [!CAUTION]  
>  通常、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] クエリ オプティマイザーでは、クエリにとって最適な実行プランが選択されるため、ヒントは、経験を積んだ開発者やデータベース管理者が最後の手段としてのみ使用することをお勧めします。  
  
#### <a name="s-using-the-tablock-hint-to-specify-a-locking-method"></a>S. TABLOCK ヒントを使用してロック手法を指定する  
 次の例では、Production.Location テーブルに対して排他 (X) ロックを使用することと、このロックを INSERT ステートメントの終了まで保持することを指定します。  
  
**適用対象**: [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)]、[!INCLUDE[ssSDS_md](../../includes/sssds-md.md)]。  
  
```sql
INSERT INTO Production.Location WITH (XLOCK)  
(Name, CostRate, Availability)  
VALUES ( N'Final Inventory', 15.00, 80.00);  
```  
  
###  <a name="capturing-the-results-of-the-insert-statement"></a><a name="CaptureResults"></a> INSERT ステートメントの結果をキャプチャする  
 このセクションの例では、[OUTPUT 句](../../t-sql/queries/output-clause-transact-sql.md)を使用して、INSERT ステートメントの影響を受ける各行の情報や、それらに基づく式を返す方法を示します。 これらの結果は処理アプリケーションに返され、確認メッセージの表示、アーカイブ化、その他のアプリケーション要件で使用することができます。  
  
#### <a name="t-using-output-with-an-insert-statement"></a>T. OUTPUT を INSERT ステートメントで使用する  
 次の例では、`ScrapReason` テーブルに 1 行を挿入し、`OUTPUT` 句を使用してステートメントの結果を `@MyTableVar` テーブル変数に返します。 `ScrapReasonID` 列が `IDENTITY` プロパティで定義されているため、`INSERT` ステートメントではこの列の値を指定していません。 ただし、[!INCLUDE[ssDE](../../includes/ssde-md.md)]によってこの列用に生成された値が、`OUTPUT` 句で `INSERTED.ScrapReasonID` 列に返されます。  
  
```sql
DECLARE @MyTableVar table( NewScrapReasonID smallint,  
                           Name varchar(50),  
                           ModifiedDate datetime);  
INSERT Production.ScrapReason  
    OUTPUT INSERTED.ScrapReasonID, INSERTED.Name, INSERTED.ModifiedDate  
        INTO @MyTableVar  
VALUES (N'Operator error', GETDATE());  
  
--Display the result set of the table variable.  
SELECT NewScrapReasonID, Name, ModifiedDate FROM @MyTableVar;  
--Display the result set of the table.  
SELECT ScrapReasonID, Name, ModifiedDate   
FROM Production.ScrapReason;  
```  
  
#### <a name="u-using-output-with-identity-and-computed-columns"></a>U. ID 列と計算列で OUTPUT を使用する  
 次の例では、`EmployeeSales` テーブルを作成し、INSERT ステートメントを使用してこのテーブルに複数行を挿入します。基になるテーブルからデータを取得するため、SELECT ステートメントも使用します。 `EmployeeSales` テーブルには、ID 列 (`EmployeeID`) および計算列 (`ProjectedSales`) があります。 これらの値は[!INCLUDE[ssDE](../../includes/ssde-md.md)]によって挿入操作中に生成されるため、いずれの列も `@MyTableVar` で定義できません。  
  
```sql
CREATE TABLE dbo.EmployeeSales  
( EmployeeID   int IDENTITY (1,5)NOT NULL,  
  LastName     nvarchar(20) NOT NULL,  
  FirstName    nvarchar(20) NOT NULL,  
  CurrentSales money NOT NULL,  
  ProjectedSales AS CurrentSales * 1.10   
);  
GO  
DECLARE @MyTableVar table(  
  LastName     nvarchar(20) NOT NULL,  
  FirstName    nvarchar(20) NOT NULL,  
  CurrentSales money NOT NULL  
  );  
  
INSERT INTO dbo.EmployeeSales (LastName, FirstName, CurrentSales)  
  OUTPUT INSERTED.LastName,   
         INSERTED.FirstName,   
         INSERTED.CurrentSales  
  INTO @MyTableVar  
    SELECT c.LastName, c.FirstName, sp.SalesYTD  
    FROM Sales.SalesPerson AS sp  
    INNER JOIN Person.Person AS c  
        ON sp.BusinessEntityID = c.BusinessEntityID  
    WHERE sp.BusinessEntityID LIKE '2%'  
    ORDER BY c.LastName, c.FirstName;  
  
SELECT LastName, FirstName, CurrentSales  
FROM @MyTableVar;  
GO  
SELECT EmployeeID, LastName, FirstName, CurrentSales, ProjectedSales  
FROM dbo.EmployeeSales;  
```  
  
#### <a name="v-inserting-data-returned-from-an-output-clause"></a>V. OUTPUT 句から返されたデータを挿入する  
 次の例では、MERGE ステートメントの OUTPUT 句から返されたデータをキャプチャし、そのデータを別のテーブルに挿入します。 MERGE ステートメントは、[!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] データベースの `SalesOrderDetail` テーブル内で処理される注文に基づいて、`ProductInventory` テーブルの `Quantity` 列を毎日更新します。 また、在庫が 0 になった製品の行を削除します。 この例では、削除された行をキャプチャし、在庫がない製品を追跡する別のテーブル `ZeroInventory` に挿入します。  
  
```sql
--Create ZeroInventory table.  
CREATE TABLE Production.ZeroInventory (DeletedProductID int, RemovedOnDate DateTime);  
GO  
  
INSERT INTO Production.ZeroInventory (DeletedProductID, RemovedOnDate)  
SELECT ProductID, GETDATE()  
FROM  
(   MERGE Production.ProductInventory AS pi  
    USING (SELECT ProductID, SUM(OrderQty) FROM Sales.SalesOrderDetail AS sod  
           JOIN Sales.SalesOrderHeader AS soh  
           ON sod.SalesOrderID = soh.SalesOrderID  
           AND soh.OrderDate = '20070401'  
           GROUP BY ProductID) AS src (ProductID, OrderQty)  
    ON (pi.ProductID = src.ProductID)  
    WHEN MATCHED AND pi.Quantity - src.OrderQty <= 0  
        THEN DELETE  
    WHEN MATCHED  
        THEN UPDATE SET pi.Quantity = pi.Quantity - src.OrderQty  
    OUTPUT $action, deleted.ProductID) AS Changes (Action, ProductID)  
WHERE Action = 'DELETE';  
IF @@ROWCOUNT = 0  
PRINT 'Warning: No rows were inserted';  
GO  
SELECT DeletedProductID, RemovedOnDate FROM Production.ZeroInventory;  
```  

#### <a name="w-inserting-data-using-the-select-option"></a>西 SELECT オプションを使用してデータを挿入する  
 次の例では、INSERT ステートメントと SELECT オプションを使って複数のデータ行を挿入する方法を示します。 1 番目の `INSERT` ステートメントでは、`SELECT` ステートメントを使用して、コピー元のテーブルからデータを直接取得し、結果セットを `EmployeeTitles` テーブルに格納します。  
  
```sql
CREATE TABLE EmployeeTitles  
( EmployeeKey   INT NOT NULL,  
  LastName     varchar(40) NOT NULL,  
  Title      varchar(50) NOT NULL  
);  
INSERT INTO EmployeeTitles  
    SELECT EmployeeKey, LastName, Title   
    FROM ssawPDW.dbo.DimEmployee  
    WHERE EndDate IS NULL;  
```  
  
#### <a name="x-specifying-a-label-with-the-insert-statement"></a>X. INSERT ステートメントでラベルを指定する  
 次の例では、INSERT ステートメントでのラベルの使用を示します。  
  
```sql
-- Uses AdventureWorks  
  
INSERT INTO DimCurrency   
VALUES (500, N'C1', N'Currency1')  
OPTION ( LABEL = N'label1' );  
```  
  
#### <a name="y-using-a-label-and-a-query-hint-with-the-insert-statement"></a>Y. INSERT ステートメントでラベルとクエリ ヒントを使用する  
 このクエリでは、INSERT ステートメントでラベルとクエリの結合ヒントを使用するための基本構文を示します。 クエリが、コントロール ノード に送信されたた後で、コンピューティング ノード上で実行されている [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] が、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] クエリ プランを生成するときにハッシュ結合の方法を適用します。 結合ヒントと OPTION 句の使用方法の詳細については、「[OPTION (SQL Server PDW)](../../t-sql/queries/option-clause-transact-sql.md)」を参照してください。  
  
```sql
-- Uses AdventureWorks  
  
INSERT INTO DimCustomer (CustomerKey, CustomerAlternateKey, 
    FirstName, MiddleName, LastName )   
SELECT ProspectiveBuyerKey, ProspectAlternateKey, 
    FirstName, MiddleName, LastName  
FROM ProspectiveBuyer p JOIN DimGeography g ON p.PostalCode = g.PostalCode  
WHERE g.CountryRegionCode = 'FR'  
OPTION ( LABEL = 'Add French Prospects', HASH JOIN);  
```  
  
## <a name="see-also"></a>参照  
 [BULK INSERT &#40;Transact-SQL&#41;](../../t-sql/statements/bulk-insert-transact-sql.md)   
 [DELETE &#40;Transact-SQL&#41;](../../t-sql/statements/delete-transact-sql.md)   
 [EXECUTE &#40;Transact-SQL&#41;](../../t-sql/language-elements/execute-transact-sql.md)   
 [FROM &#40;Transact-SQL&#41;](../../t-sql/queries/from-transact-sql.md)   
 [IDENTITY &#40;Property&#41; &#40;Transact-SQL&#41;](../../t-sql/statements/create-table-transact-sql-identity-property.md)   
 [NEWID &#40;Transact-SQL&#41;](../../t-sql/functions/newid-transact-sql.md)   
 [SELECT &#40;Transact-SQL&#41;](../../t-sql/queries/select-transact-sql.md)   
 [UPDATE &#40;Transact-SQL&#41;](../../t-sql/queries/update-transact-sql.md)   
 [MERGE &#40;Transact-SQL&#41;](../../t-sql/statements/merge-transact-sql.md)   
 [OUTPUT 句 &#40;Transact-SQL&#41;](../../t-sql/queries/output-clause-transact-sql.md)   
 [inserted テーブルと deleted テーブルの使用](../../relational-databases/triggers/use-the-inserted-and-deleted-tables.md)  
  
