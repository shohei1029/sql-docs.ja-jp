---
description: CREATE TYPE (Transact-SQL)
title: CREATE TYPE (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 12/05/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- sql13.swb.sysdatatype.properties.f1
- CREATE TYPE
- TYPE_TSQL
- TYPE
- CREATE_TYPE_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- UDTs [SQL Server], creating
- CLR user-defined types [SQL Server]
- user-defined table types [SQL Server]
- user-defined types [SQL Server], creating
- CREATE TYPE statement
- alias data types [SQL Server], creating
- data types [SQL Server], creating
ms.assetid: 2202236b-e09f-40a1-bbc7-b8cff7488905
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: be33b74a13fc1d579fd4ec8a352a39777f045134
ms.sourcegitcommit: 295b9dfc758471ef7d238a2b0f92f93e34acbb1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106054800"
---
# <a name="create-type-transact-sql"></a>CREATE TYPE (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] または [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] で別名データ型またはユーザー定義型を現在のデータベースで作成します。 別名データ型の実装は、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のネイティブ システム型に基づきます。 ユーザー定義型は、[!INCLUDE[msCoName](../../includes/msconame-md.md)][!INCLUDE[dnprdnshort](../../includes/dnprdnshort-md.md)] 共通言語ランタイム (CLR) のアセンブリのクラスを使用して実装します。 ユーザー定義型を実装にバインドするには、先に [CREATE ASSEMBLY](../../t-sql/statements/create-assembly-transact-sql.md) を使用して、その実装を含む CLR アセンブリを [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] で登録しておく必要があります。  
  
 既定では、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の CLR コード実行機能は無効になっています。 マネージド コード モジュールを参照するデータベース オブジェクトを作成、変更、および削除できますが、それらの参照を [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] で実行するには、[sp_configure](../../relational-databases/system-stored-procedures/sp-configure-transact-sql.md) を使用して [clr enabled オプション](../../database-engine/configure-windows/clr-enabled-server-configuration-option.md)を有効にする必要があります。  
 
> [!NOTE]  
>  このトピックでは、SQL Server への .NET Framework CLR の統合について説明します。 CLR 統合は、Azure [!INCLUDE[ssSDS](../../includes/sssds-md.md)] には適用されません。
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql
-- User-defined Data Type Syntax    
CREATE TYPE [ schema_name. ] type_name  
{   
    [
      FROM base_type   
      [ ( precision [ , scale ] ) ]  
      [ NULL | NOT NULL ]
    ]
    | EXTERNAL NAME assembly_name [ .class_name ]   
    | AS TABLE ( { <column_definition> | <computed_column_definition> [ ,... n ] }
      [ <table_constraint> ] [ ,... n ]    
      [ <table_index> ] [ ,... n ] } )
 
} [ ; ]  
  
<column_definition> ::=  
column_name <data_type>  
    [ COLLATE collation_name ]   
    [ NULL | NOT NULL ]  
    [   
        DEFAULT constant_expression ]   
      | [ IDENTITY [ ( seed ,increment ) ]   
    ]  
    [ ROWGUIDCOL ] [ <column_constraint> [ ...n ] ]   
  
<data type> ::=   
[ type_schema_name . ] type_name   
    [ ( precision [ , scale ] | max |   
                [ { CONTENT | DOCUMENT } ] xml_schema_collection ) ]   
  
<column_constraint> ::=   
{     { PRIMARY KEY | UNIQUE }   
        [ CLUSTERED | NONCLUSTERED ]   
        [   
            WITH ( <index_option> [ ,...n ] )   
        ]  
  | CHECK ( logical_expression )   
}   
  
<computed_column_definition> ::=  
  
column_name AS computed_column_expression   
[ PERSISTED [ NOT NULL ] ]  
[   
    { PRIMARY KEY | UNIQUE }  
        [ CLUSTERED | NONCLUSTERED ]  
        [   
            WITH ( <index_option> [ ,...n ] )  
        ]  
    | CHECK ( logical_expression )   
]   
  
<table_constraint> ::=  
{   
    { PRIMARY KEY | UNIQUE }   
        [ CLUSTERED | NONCLUSTERED ]   
    ( column [ ASC | DESC ] [ ,...n ] )   
        [   
    WITH ( <index_option> [ ,...n ] )   
        ]  
    | CHECK ( logical_expression )   
}   
  
<index_option> ::=  
{  
    IGNORE_DUP_KEY = { ON | OFF }  
}  

< table_index > ::=  
  INDEX constraint_name  
     [ CLUSTERED | NONCLUSTERED ]   (column [ ASC | DESC ] [ ,... n ] )} }  
```  
  
```syntaxsql
-- User-defined Memory Optimized Table Types Syntax  
CREATE TYPE [schema_name. ] type_name  
AS TABLE ( { <column_definition> [ ,... n ] }  
    | [ <table_constraint> ] [ ,... n ]    
    | [ <table_index> ] [ ,... n ] } )
    [ WITH ( <table_option> [ ,... n ] ) ]  
 [ ; ]  
  
<column_definition> ::=  
column_name <data_type>  
    [ COLLATE collation_name ]   [ NULL | NOT NULL ]    [  
      [ IDENTITY [ (1 , 1) ]  
    ]  
    [ <column_constraint> [ ... n ] ]    [ <column_index> ]  
  
<data type> ::=  
 [type_schema_name . ] type_name [ (precision [ , scale ]) ]  
  
<column_constraint> ::=  
{ PRIMARY KEY {   NONCLUSTERED HASH WITH (BUCKET_COUNT = bucket_count) 
                | NONCLUSTERED } }  
  
< table_constraint > ::=  
{ PRIMARY KEY { NONCLUSTERED HASH (column [ ,... n ] ) 
                   WITH (BUCKET_COUNT = bucket_count) 
               | NONCLUSTERED  (column [ ASC | DESC ] [ ,... n ] )  } }  
  
<column_index> ::=  
  INDEX index_name  
{ { [ NONCLUSTERED ] HASH WITH (BUCKET_COUNT = bucket_count) 
     | NONCLUSTERED } }  
  
< table_index > ::=  
  INDEX constraint_name  
{ { [ NONCLUSTERED ] HASH (column [ ,... n ] ) WITH (BUCKET_COUNT = bucket_count) 
 |  [NONCLUSTERED]  (column [ ASC | DESC ] [ ,... n ] )} }  
  
<table_option> ::=  
{  
    [MEMORY_OPTIMIZED = {ON | OFF}]  
}  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
*schema_name*  
別名データ型またはユーザー定義型が所属しているスキーマの名前です。  

*type_name*  
別名データ型またはユーザー定義型の名前です。 型の名前は、[識別子](../../relational-databases/databases/database-identifiers.md)の規則に従っている必要があります。  

*base_type*  
別名データ型の基になる [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 提供のデータ型です。 *base_type* のデータ型は **sysname** で、既定値はありません。有効値は次のとおりです。  

:::row:::
   :::column span="":::
      **bigint**<br>            **binary(** *n* **)**<br>            **bit**<br>            **char(** *n* **)**
   :::column-end:::
   :::column span="":::
      **date**<br>            **datetime**<br>            **datetime2**<br>            **datetimeoffset**
   :::column-end:::
   :::column span="":::
      **decimal**<br>            **float**<br>            **image**<br>            **int**
   :::column-end:::
   :::column span="":::
      **money**<br>            **nchar(** *n* **)**<br>            **ntext**<br>            **numeric**
   :::column-end:::
   :::column span="":::
      **nvarchar(** *n* &#124; **max)**<br>            **real**<br>            **smalldatetime**<br>            **smallint**
   :::column-end:::
   :::column span="":::
      **smallmoney**<br>            **sql_variant**<br>            **text**<br>            **time**
   :::column-end:::
   :::column span="":::
      **tinyint**<br>            **uniqueidentifier**<br>            **varbinary(** *n* &#124; **max)**<br>            **varchar(** *n* &#124; **max)**
   :::column-end:::
:::row-end:::
  
 また *base_type* は、これらのシステム データ型のいずれかにマップする任意のデータ型のシノニムにすることもできます。  
  
 *有効桁数 (precision)*  
 **decimal** 型または **numeric** 型の場合は、格納可能な 10 進数の最大桁数を示す、負ではない整数です。これは、小数点の左側と右側の桁数の合計です。 詳しくは、「[decimal 型と numeric 型 &#40;Transact-SQL&#41;](../../t-sql/data-types/decimal-and-numeric-transact-sql.md)」をご覧ください。  
  
 *scale*  
 **decimal** 型または **numeric** 型の場合は、小数点の右側にとることができる 10 進数の最大桁数を示す、負ではない整数です。有効桁数以下の数値である必要があります。 詳しくは、「[decimal 型と numeric 型 &#40;Transact-SQL&#41;](../../t-sql/data-types/decimal-and-numeric-transact-sql.md)」をご覧ください。  
  
 **NULL** | NOT NULL  
 型が NULL 値を保持できるかどうかを指定します。 指定しない場合は、NULL が既定値です。  
  
 *assembly_name*  
 **適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。  
  
 共通言語ランタイム内のユーザー定義型の実装を参照する [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のアセンブリを指定します。 *assembly_name* は、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の現在のデータベース内の既存のアセンブリに一致している必要があります。  
  
> [!NOTE]  
>  EXTERNAL_NAME は、包含データベースでは使用できません。  
  
 **[.** *class_name* **]**  
 **適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。  
  
 ユーザー定義型を実装するアセンブリ内のクラスを指定します。 *class_name* は有効な識別子であり、アセンブリ内にアセンブリで可視のクラスとして存在している必要があります。 *class_name* は、対応するアセンブリ内のクラス名に正確に一致している必要があります。大文字と小文字は、データベース照合順序に関係なく区別されます。 クラスの記述に使用されている C# などのプログラミング言語が、名前空間の概念を使用している場合、クラス名は、角かっこ ( **[ ]** ) で囲まれた名前空間で修飾された名前にすることができます。 *class_name* を指定しない場合は、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] によって *type_name* と同じであると見なされます。  
  
 \<column_definition>  
 ユーザー定義テーブル型の列を定義します。  
  
 \<data type>  
 ユーザー定義テーブル型の列でデータ型を定義します。 データ型について詳しくは、「[データ型 &#40;Transact-SQL&#41;](../../t-sql/data-types/data-types-transact-sql.md)」をご覧ください。 テーブルについて詳しくは、「[CREATE TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/create-table-transact-sql.md)」をご覧ください。  
  
 \<column_constraint>  
 ユーザー定義テーブル型の列制約を定義します。 PRIMARY KEY、UNIQUE、CHECK などの制約がサポートされています。 テーブルについて詳しくは、「[CREATE TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/create-table-transact-sql.md)」をご覧ください。  
  
 \<computed_column_definition>  
 計算列の式をユーザー定義テーブル型の列として定義します。 テーブルについて詳しくは、「[CREATE TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/create-table-transact-sql.md)」をご覧ください。  
  
 \<table_constraint>  
 ユーザー定義テーブル型のテーブル制約を定義します。 PRIMARY KEY、UNIQUE、CHECK などの制約がサポートされています。  
  
 \<index_option>  
 一意のクラスター化インデックスまたは一意の非クラスター化インデックスにおいて、複数行の挿入操作で重複したキー値が見つかった場合の、エラー応答を指定します。 インデックス オプションについて詳しくは、「[CREATE INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/create-index-transact-sql.md)」をご覧ください。  
 
  `INDEX *index_name* [ CLUSTERED | NONCLUSTERED ] (*column_name* [ ASC | DESC ] [ ,... *n* ] )`  
     
**適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降と [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。

テーブル上にインデックスを作成することを指定します。 これには、クラスター化インデックスまたは非クラスター化インデックスを指定できます。 インデックスには一覧表示される列が含まれ、昇順、降順のいずれかでデータが並べ替えられます。
  
 INDEX  
 CREATE TABLE ステートメントの一部として列インデックスとテーブル インデックスを指定する必要があります。 メモリ最適化テーブルでは、CREATE INDEX および DROP INDEX はサポートされません。  
  
 MEMORY_OPTIMIZED  
 **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降と [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。  
  
 テーブル型がメモリ最適化かどうかを示します。 既定では、このオプションはオフになっています。テーブル (型) は、メモリ最適化テーブル (型) ではありません。 メモリ最適化テーブル型は、他のユーザー テーブルと同様にスキーマがディスク上に保存されるメモリ最適化ユーザー テーブルです。  
  
 BUCKET_COUNT  
 **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降と [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。  
  
 ハッシュ インデックスに作成されるバケットの数を示します。 ハッシュ インデックスの BUCKET_COUNT の最大値は 1,073,741,824 です。 バケット数について詳しくは、「[メモリ最適化テーブルのインデックス](../../relational-databases/in-memory-oltp/indexes-for-memory-optimized-tables.md)」をご覧ください。 *bucket_count* は必須の引数です。  
  
 HASH  
 **適用対象**: [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降と [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。  
  
 ハッシュ インデックスを作成することを示します。 ハッシュ インデックスは、メモリ最適化テーブルでのみサポートされます。  
  
## <a name="remarks"></a>解説  
 *assembly_name* とそのメソッドで参照されているアセンブリのクラスは、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] でユーザー定義型を実装するためのすべての要件を満たしている必要があります。 これらの要件の詳細については、「[CLR ユーザー定義型](../../relational-databases/clr-integration-database-objects-user-defined-types/clr-user-defined-types.md)」を参照してください。  
  
 その他、次のような注意事項があります。  
  
-   クラスにはオーバーロードされたメソッドが存在する可能性があります。ただし、これらのメソッドはマネージド コード内からのみ呼び出すことができ、[!INCLUDE[tsql](../../includes/tsql-md.md)] から呼び出すことはできません。  
  
-   *assembly_name* が SAFE または EXTERNAL_ACCESS の場合、すべての静的メンバーは **const** または **readonly** として宣言する必要があります。  
  
 データベース内では、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] で CLR からアップロードされた任意の指定された型に対して、1 つのユーザー定義型のみを登録できます。 データベース内にユーザー定義型が既に存在する CLR 型に対してユーザー定義型を作成した場合、CREATE TYPE は失敗し、エラーが発生します。 この制約が必要なのは、CLR 型を複数のユーザー定義型にマップすることが可能な場合の、SQL 型の解決時のあいまいな状態を避けるためです。  
  
 型のミューテーター メソッドが *void* を返さない場合、CREATE TYPE ステートメントは実行されません。  
  
 ユーザー定義型を変更するには、DROP TYPE ステートメントを使用して型を削除してから、再作成する必要があります。  
  
 **sp_addtype** を使用して作成したユーザー定義型と異なり、CREATE TYPE を使用して作成した型に対しては、データベース ロール **public** に REFERENCES 権限が自動的に付与されるわけではありません。 この権限は個別に付与する必要があります。  
  
 ユーザー定義テーブル型の場合、*column_name* \<data type> で使用される構造化ユーザー定義型は、テーブル型が定義されているデータベース スキーマ スコープの一部になります。 データベース内の別のスコープに含まれている構造化ユーザー定義型にアクセスするには、2 つの部分から構成される名前を使用します。  
  
 ユーザー定義テーブル型では、計算列の主キーを PERSISTED および NOT NULL にする必要があります。  
  
## <a name="memory-optimized-table-types"></a>メモリ最適化テーブル型  
 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降では、ディスク上ではなく、プライマリ メモリ内でテーブル型のデータを処理できます。 詳細については、「[インメモリ OLTP &#40;インメモリ最適化&#41;](../../relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization.md)」を参照してください。 メモリ最適化テーブル型の作成方法を示すコード サンプルについては、「[メモリ最適化テーブルおよびネイティブ コンパイル ストアド プロシージャの作成](../../relational-databases/in-memory-oltp/creating-a-memory-optimized-table-and-a-natively-compiled-stored-procedure.md)」をご覧ください。  
  
## <a name="permissions"></a>アクセス許可  
 現在のデータベース内の CREATE TYPE 権限、および *schema_name* に対する ALTER 権限が必要です。 *schema_name* を指定しなかった場合は、現在のユーザーのスキーマを判断するための既定の名前解決ルールが適用されます。 *assembly_name* を指定した場合は、ユーザーがそのアセンブリの所有者であるか、そのアセンブリに対する REFERENCES 権限を持っている必要があります。  

 CREATE TABLE ステートメント内の列をユーザー定義型として定義する場合は、そのユーザー定義型に対する REFERENCES 権限が必要です。
 
   >[!NOTE]
  > ユーザー定義型を使用する列があるテーブルを作成するユーザーは、そのユーザー定義型に対して REFERENCES アクセス許可を持っている必要があります。
  > このテーブルを TempDB 内に作成する必要がある場合、テーブルを作成する **前** に毎回 REFERENCES アクセス許可を明示的に付与する必要があります。または、このデータ型と REFERENCES アクセス許可を model データベースに追加する必要があります。 この処理が完了すると、このデータ型とアクセス許可は TempDB で永続的に利用できるようになります。 この処理が完了していない場合、SQL Server の再起動時にユーザー定義のデータ型とアクセス許可は消去されます。 詳細については、「[CREATE TABLE](./create-table-transact-sql.md#permissions-1)」を参照してください。
  
## <a name="examples"></a>例  
  
### <a name="a-creating-an-alias-type-based-on-the-varchar-data-type"></a>A. varchar データ型に基づいた別名型を作成する  
 次の例では、システムから提供されている `varchar` データ型に基づいて、別名型を作成します。  
  
```sql  
CREATE TYPE SSN  
FROM varchar(11) NOT NULL ;  
```  
  
### <a name="b-creating-a-user-defined-type"></a>B. ユーザー定義型を作成する  
 次の例では、アセンブリ `utf8string` 内のクラス `utf8string` を参照する型 `Utf8String` を作成します。 型を作成する前に、アセンブリ `utf8string` がローカル データベースに登録されます。 CREATE ASSEMBLY ステートメントのバイナリ部分を有効な記述と置き換えます。  
  
**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。  
  
```sql  
CREATE ASSEMBLY utf8string  
AUTHORIZATION [dbi]   
FROM 0x4D... ;  
GO  
CREATE TYPE Utf8String   
EXTERNAL NAME utf8string.[Microsoft.Samples.SqlServer.utf8string] ;  
GO  
```  
  
### <a name="c-creating-a-user-defined-table-type"></a>C. ユーザー定義テーブル型を作成する  
 次の例では、2 つの列を持つユーザー定義テーブル型が作成されます。 テーブル値パラメーターの作成方法および使用方法の詳細については、「[テーブル値パラメーターの使用 &#40;データベース エンジン&#41;](../../relational-databases/tables/use-table-valued-parameters-database-engine.md)」をご覧ください。  
  
```sql  
CREATE TYPE LocationTableType AS TABLE   
    ( LocationName VARCHAR(50)  
    , CostRate INT );  
GO  
```  

### <a name="d-creating-a-user-defined-table-type-with-primary-key-and-index"></a>D. プライマリ キーとインデックスでユーザー定義テーブル型を作成する
次の例では、3 つの列を持つユーザー定義テーブル型を作成します。そのうちの 1 つ (`Name`) はプライマリ キーで、もう 1 つ (`Price`) には非クラスター化インデックスが作成されています。  テーブル値パラメーターの作成方法および使用方法の詳細については、「[テーブル値パラメーターの使用 &#40;データベース エンジン&#41;](../../relational-databases/tables/use-table-valued-parameters-database-engine.md)」をご覧ください。

```sql
CREATE TYPE InventoryItem AS TABLE
(
    [Name] NVARCHAR(50) NOT NULL,
    SupplierId BIGINT NOT NULL,
    Price DECIMAL (18, 4) NULL,
    PRIMARY KEY (
        Name
    ),
    INDEX IX_InventoryItem_Price (
        Price
    )
)
GO
```
  
## <a name="see-also"></a>参照  
 [CREATE ASSEMBLY &#40;Transact-SQL&#41;](../../t-sql/statements/create-assembly-transact-sql.md)   
 [DROP TYPE &#40;Transact-SQL&#41;](../../t-sql/statements/drop-type-transact-sql.md)   
 [EVENTDATA &#40;Transact-SQL&#41;](../../t-sql/functions/eventdata-transact-sql.md)    
 [CLR ユーザー定義型](../../relational-databases/clr-integration-database-objects-user-defined-types/clr-user-defined-types.md)     
 [SQL Server でのユーザー定義型の使用](../../relational-databases/clr-integration-database-objects-user-defined-types/working-with-user-defined-types-in-sql-server.md)     
