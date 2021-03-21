---
description: ALTER TABLE column_definition (Transact-SQL)
title: column_definition (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 09/24/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- column_definition
- column_definition_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- column_definition
- ALTER TABLE statement
- column properties [SQL Server]
- column definitions [SQL Server]
ms.assetid: a1742649-ca29-4d9b-9975-661cdbf18f78
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 6ef0c115d5367f0973cb821c38b35adf0093e50e
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104750312"
---
# <a name="alter-table-column_definition-transact-sql"></a>ALTER TABLE column_definition (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi](../../includes/applies-to-version/sql-asdb-asdbmi.md)]

  [ALTER TABLE](../../t-sql/statements/alter-table-transact-sql.md) を使用してテーブルに追加される列のプロパティを指定します。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql
column_name <data_type>  
[ FILESTREAM ]  
[ COLLATE collation_name ]   
[ NULL | NOT NULL ]  
[   
    [ CONSTRAINT constraint_name ] DEFAULT constant_expression [ WITH VALUES ]   
    | IDENTITY [ ( seed , increment ) ] [ NOT FOR REPLICATION ]   
]  
[ ROWGUIDCOL ]   
[ SPARSE ]   
[ ENCRYPTED WITH  
  ( COLUMN_ENCRYPTION_KEY = key_name ,  
      ENCRYPTION_TYPE = { DETERMINISTIC | RANDOMIZED } ,   
      ALGORITHM =  'AEAD_AES_256_CBC_HMAC_SHA_256'   
  ) ]  
[ MASKED WITH ( FUNCTION = ' mask_function ') ]  
[ <column_constraint> [ ...n ] ]  
  
<data type> ::=   
[ type_schema_name . ] type_name   
    [ ( precision [ , scale ] | max |   
        [ { CONTENT | DOCUMENT } ] xml_schema_collection ) ]   
  
<column_constraint> ::=   
[ CONSTRAINT constraint_name ]   
{     { PRIMARY KEY | UNIQUE }   
        [ CLUSTERED | NONCLUSTERED ]   
        [   
            WITH FILLFACTOR = fillfactor    
          | WITH ( < index_option > [ , ...n ] )   
        ]   
        [ ON { partition_scheme_name ( partition_column_name )   
            | filegroup | "default" } ]  
  | [ FOREIGN KEY ]   
        REFERENCES [ schema_name . ] referenced_table_name [ ( ref_column ) ]   
        [ ON DELETE { NO ACTION | CASCADE | SET NULL | SET DEFAULT } ]   
        [ ON UPDATE { NO ACTION | CASCADE | SET NULL | SET DEFAULT } ]   
        [ NOT FOR REPLICATION ]   
  | CHECK [ NOT FOR REPLICATION ] ( logical_expression )   
}  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
 *column_name*  
 変更、追加、または削除する列の名前を指定します。 *column_name* は 1 ～ 128 文字で指定できます。 timestamp データ型で作成される新しい列の場合は、*column_name* を省略できます。 **timestamp** データ型の列に対して *column_name* を指定しない場合には、名前 **timestamp** が使われます。  
  
 [ _type_schema_name_ **.** ] *type_name*  
 追加する列のデータ型と、それが属するスキーマを指定します。  
  
 *type_name* は次のいずれかです。  
  
-   [!INCLUDE[msCoName](../../includes/msconame-md.md)][!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] システム データ型。  
  
-   [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] システム データ型に基づく別名データ型。 このデータ型をテーブル定義で使用するには、先に CREATE TYPE を使って作成しておく必要があります。  
  
-   [!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[dnprdnshort](../../includes/dnprdnshort-md.md)] ユーザー定義型とそれが属するスキーマ。 [!INCLUDE[dnprdnshort](../../includes/dnprdnshort-md.md)] ユーザー定義型をテーブル定義で使用するには、先に CREATE TYPE を使って作成しておく必要があります。  
  
 *type_schema_name* を指定しない場合、[!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssDE](../../includes/ssde-md.md)] では次の順序で *type_name* が参照されます。  
  
-   [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のシステム データ型  
  
-   現在のデータベースにおける現在のユーザーの既定のスキーマ。  
  
-   現在のデータベースの **dbo** スキーマ。  
  
*有効桁数 (precision)*  
 指定されるデータ型の有効桁数です。 有効桁数の詳細については、「[有効桁数、小数点以下桁数、および長さ &#40;Transact-SQL&#41;](../../t-sql/data-types/precision-scale-and-length-transact-sql.md)」を参照してください。  
  
*scale*  
 指定されるデータ型の小数点以下桁数です。 有効な小数点以下桁数の詳細については、「[有効桁数、小数点以下桁数、および長さ &#40;Transact-SQL&#41;](../../t-sql/data-types/precision-scale-and-length-transact-sql.md)」を参照してください。  
  
**max**  
 **varchar**、**nvarchar**、**varbinary** データ型のみに適用されます。 これらは 2^31 バイトの文字とバイナリ データ、および 2^30 バイトの Unicode データを格納するときに使用されます。  
  
**CONTENT**  
 *column_name* 内の **xml** データ型の各インスタンスが複数のトップレベル要素で構成できることを指定します。 CONTENT は、**xml** データ型のみに適用され、*xml_schema_collection* も指定されている場合にだけ指定できます。 指定しない場合は、CONTENT が既定の動作になります。  
  
DOCUMENT  
 *column_name* 内の **xml** データ型の各インスタンスは 1 つのトップレベル要素のみで構成できることを指定します。 DOCUMENT は、**xml** データ型のみに適用され、*xml_schema_collection* も指定されている場合にだけ指定できます。  
  
 *xml_schema_collection*  
 **適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。  
  
 **xml** データ型にのみ適用されます。XML スキーマ コレクションとこのデータ型を関連付けるためのものです。 スキーマで **xml** 列を使用するには、まず、[CREATE XML SCHEMA COLLECTION](../../t-sql/statements/create-xml-schema-collection-transact-sql.md) を使用してデータベース内にスキーマを作成する必要があります。  
  
FILESTREAM  
 必要に応じて、*type_name* が **varbinary(max)** の列に対して FILESTREAM ストレージ属性を指定します。  
  
 列に対して FILESTREAM を指定した場合、ROWGUIDCOL 属性を持つ **uniqueidentifier** データ型の列がテーブルに存在する必要があります。 この列では、null 値を許可してはならず、また UNIQUE、PRIMARY KEY のいずれかの単一列制約を持つ必要があります。 列の GUID 値は、データの挿入時にアプリケーションによって、または NEWID () 関数を使用する DEFAULT 制約によって、提供する必要があります。  
  
 テーブルに FILESTREAM 列が定義されている間は、ROWGUIDCOL 列を削除したり、関連する制約を変更したりすることはできません。 ROWGUIDCOL 列は、最後の FILESTREAM 列を削除した後にのみ削除できます。  
  
 列に対して FILESTREAM ストレージ属性を指定した場合、この列のすべての値がファイル システム上の FILESTREAM データ コンテナーに格納されます。  
  
 列定義の使用方法を示す例については、「[FILESTREAM &#40;SQL Server&#41;](../../relational-databases/blob/filestream-sql-server.md)」を参照してください。  
  
COLLATE *collation_name*  
 列の照合順序を指定します。 指定しない場合、データベースの既定の照合順序が列に割り当てられます。 照合順序名には、Windows 照合順序名または SQL 照合順序名を指定できます。 一覧と詳細については、「[Windows 照合順序名 &#40;Transact-SQL&#41;](../../t-sql/statements/windows-collation-name-transact-sql.md)」と「[SQL Server 照合順序名 &#40;Transact-SQL&#41;](../../t-sql/statements/sql-server-collation-name-transact-sql.md)」を参照してください。  
  
 COLLATE 句を使用して照合順序を指定できるのは、**char**、**varchar**、**nchar**、**nvarchar** データ型の列だけです。  
  
 COLLATE 句の詳細については、「[COLLATE &#40;Transact-SQL&#41;](~/t-sql/statements/collations.md)」を参照してください。  
  
 NULL | NOT NULL  
 列で NULL 値を許すかどうかを示します。 NULL は厳密には制約ではありませんが、NOT NULL と同じように指定することができます。  
  
[ CONSTRAINT *constraint_name* ]  
 DEFAULT 値の定義の開始を指定します。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の旧バージョンとの互換性を保つため、DEFAULT に制約名を割り当てることができます。 *constraint_name* は、[識別子](../../relational-databases/databases/database-identifiers.md)の規則に従う必要があります。ただし、名前の先頭に番号記号 (#) を指定することはできません。 *constraint_name* を指定しない場合、DEFAULT 定義にはシステムによって作成される名前が割り当てられます。  
  
DEFAULT  
 列の既定値を指定するキーワードです。 DEFAULT 定義を使用すると、既存のデータ行に新しい列の値を設定できます。 DEFAULT 定義は、**timestamp** 列または IDENTITY プロパティが指定されている列には適用できません。 ユーザー定義型の列に既定値を指定する場合は、その型で *constant_expression* 型からユーザー定義型への暗黙的な変換がサポートされている必要があります。  
  
*constant_expression*  
 リテラル値、NULL 値、または列の既定値として使用されるシステム関数です。 [!INCLUDE[dnprdnshort](../../includes/dnprdnshort-md.md)] ユーザー定義型として定義されている列と共に使用する場合は、その型を実装したときに、*constant_expression* からユーザー定義型への暗黙的な変換がサポートされる必要があります。  
  
WITH VALUES   
 DEFAULT 制約と共に列を追加する際に列が NULL を許容している場合、WITH VALUES を使用すると、既存の行の新しい列の値は DEFAULT の *constant_expression* で指定される値に設定されます。 追加する列が NULL を許容していない場合、既存の行のその列の値は常に DEFAULT の *constant expression* で指定される値に設定されます。 SQL Server 2012 以降、これはメタ データの操作 [adding-not-null-columns-as-an-online-operation](alter-table-transact-sql.md#adding-not-null-columns-as-an-online-operation) である場合があります。
関連する列が追加されない場合にこれを使用しても、影響はありません。
 
 DEFAULT *constant_expression* で指定される値が、既存の行に追加される新規列に格納されることを指定します。 追加する列に NULL 値が許容され、WITH VALUES を指定した場合、新しい列には既定値が格納され、既存の行に追加されます。 NULL 値が許容される列に対して WITH VALUES を指定しない場合は、既存の行の新しい列には NULL 値が格納されます。 新しい列で NULL 値が許容されない場合は、WITH VALUES の指定に関係なく、新しい行に既定値が格納されます。  
  
IDENTITY  
 新しい列が ID 列であることを指定します。 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]は、列に一意な増分値を設定します。 既存のテーブルに ID 列を追加すると、テーブルの既存の行にシード値と増分値を持つ識別番号が追加されます。 行の更新順序は保証されません。 識別番号は、新しく追加された行に対しても生成されます。  
  
 ID 列は通常、PRIMARY KEY 制約と組み合わせて使用し、テーブルの一意な行識別子 (ROWID) の役割を果たします。 IDENTITY プロパティは、**tinyint**、**smallint**、**int**、**bigint**、**decimal(p,0)** 、**numeric(p,0)** のいずれかの列に割り当てることができます。 ID 列は 1 つのテーブルにつき 1 つだけ作成できます。 DEFAULT キーワードとバインドされている既定値は、ID 列で使用できません。 seed と increment は両方指定するか、どちらも指定しないでください。 どちらも指定しない場合、既定値は (1,1) になります。  
  
> [!NOTE]  
>  既存のテーブル列を変更して IDENTITY プロパティを追加することはできません。  
  
 パブリッシュされたテーブルへの ID 列の追加はサポートされていません。これは、列をサブスクライバーにレプリケートするときに集約できなくなる可能性があるためです。 パブリッシャーの ID 列の値は、影響を受けるテーブルの行が物理的に格納されている順序に依存します。 サブスクライバー側では行は別の方法で格納される場合があるため、ID 列の値は同じ行で異なる可能性があります。  
  
 値の明示的な挿入を許可して列の IDENTITY プロパティを無効にするには、[SET IDENTITY_INSERT](../../t-sql/statements/set-identity-insert-transact-sql.md) を使用します。  
  
*seed*  
 テーブルに読み込まれる先頭行で使用される値を指定します。  
  
*increment*  
 読み込まれている前の行の ID 値に加算される増分値を指定します。  
  
NOT FOR REPLICATION  
 **適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。  
  
 IDENTITY プロパティに対して指定できます。 IDENTITY プロパティに対してこの句を指定した場合、レプリケーション エージェントが挿入操作を実行するとき ID 列の値は増加しません。  
  
ROWGUIDCOL  
 **適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。  
  
 列が、行グローバル一意識別子列であることを指定します。 ROWGUIDCOL は、**uniqueidentifier** 列にのみ割り当てることができ、1 つのテーブルに 1 つの **uniqueidentifier** 列だけを ROWGUIDCOL 列として指定できます。 ROWGUIDCOL は、ユーザー定義データ型の列には割り当てできません。  
  
 ROWGUIDCOL では、列に格納される値は一意である必要はありません。 また、ROWGUIDCOL はテーブルに新しい行を挿入しても値を自動的に生成しません。 各列に一意な値を生成するには、INSERT ステートメントで NEWID 関数を使用するか、列の既定値として NEWID 関数を指定します。 詳しくは、「[NEWID &#40;Transact-SQL&#41;](../../t-sql/functions/newid-transact-sql.md)」および「[INSERT &#40;Transact-SQL&#41;](../../t-sql/statements/insert-transact-sql.md)」をご覧ください。  
  
SPARSE  
 列がスパース列であることを示します。 スパース列のストレージは NULL 値用に最適化されます。 スパース列を NOT NULL として指定することはできません。 スパース列のその他の制限事項と詳細については、「[スパース列の使用](../../relational-databases/tables/use-sparse-columns.md)」を参照してください。  
  
\<column_constraint>  
 列制約の引数の定義については、「[column_constraint &#40;Transact-SQL&#41;](../../t-sql/statements/alter-table-column-constraint-transact-sql.md)」を参照してください。  
  
 ENCRYPTED WITH  
 [Always Encrypted](../../relational-databases/security/encryption/always-encrypted-database-engine.md) 機能を使って暗号化列を指定します。  
  
 COLUMN_ENCRYPTION_KEY = *key_name*  
 列の暗号化キーを指定します。 詳しくは、「[CREATE COLUMN ENCRYPTION KEY &#40;Transact-SQL&#41;](../../t-sql/statements/create-column-encryption-key-transact-sql.md)」をご覧ください。  
  
ENCRYPTION_TYPE = { DETERMINISTIC | RANDOMIZED }  
 **確定的な暗号化** は常に任意のプレーン テキストを指定した値の場合は、同じ暗号化された値を生成するメソッドを使用します。 決定論的な暗号化を使うことにより、等価比較を使った検索や、グループ化、暗号化された値に基づく等価結合を使ったテーブルの結合が可能になりますが、承認されていないユーザーが、暗号化された列のパターンを調べることで暗号化された値に関する情報を推測することも可能になります。 決定論的に暗号化された列で 2 つのテーブルを結合することができるのは、両方の列が同じ列暗号化キーを使って暗号化されている場合のみです。 明確な暗号化では、バイナリ 2 文字型の列の並べ替え順序を持つ列の照合順序を使用する必要があります。  
  
 **暗号化をランダム化** は低い予測可能な方法でデータを暗号化するためのメソッドを使用します。 ランダム化された暗号化は、より安全ですが、SQL Server インスタンスで[セキュア エンクレーブを使用する Always Encrypted](../../relational-databases/security/encryption/always-encrypted-enclaves.md) がサポートされる場合を除き、暗号化された列に対する計算とインデックス作成はすべて阻止されます。
  
 Always Encrypted (セキュア エンクレーブなし) を使用している場合、政府の ID 番号などのパラメーターまたはグループ化パラメーターで検索される列には、決定論的な暗号化を使用します。 ランダム化された暗号化は、他のレコードとグループ化されたり、テーブルの結合に使用されたりせず、関心のある暗号化された列を含む行の検索には他の列 (トランザクション番号など) が使用されるため検索されることのない、クレジット カード番号などのデータに使用します。  

 セキュア エンクレーブを使用する Always Encrypted を使用する場合は、ランダム化された暗号化が推奨される暗号化の種類です。  
  
 列は、該当するデータ型である必要があります。  
  
ALGORITHM  
**適用対象**: [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 以降、[!INCLUDE[ssSDS](../../includes/sssds-md.md)]。  
**'AEAD_AES_256_CBC_HMAC_SHA_256'** を指定する必要があります。  
  
 機能の制約などについて詳しくは、「[Always Encrypted &#40;データベース エンジン&#41;](../../relational-databases/security/encryption/always-encrypted-database-engine.md)」をご覧ください。  
  
   
ADD MASKED WITH ( FUNCTION = ' *mask_function* ')  
 **適用対象**: [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 以降、[!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)]。  
  
 動的なデータ マスクを指定します。 *mask_function* マスキング関数は、適切なパラメーターの名前を指定します。 以下の関数を使用できます。  
  
-   default()  
  
-   email()  
  
-   partial()  
  
-   random()  
  
 関数のパラメーターについては、「[動的なデータ マスキング](../../relational-databases/security/dynamic-data-masking.md)」を参照してください。  
  
## <a name="remarks"></a>解説  
 追加される列が **uniqueidentifier** データ型の場合は、NEWID() 関数を使用する既定値を定義することで、テーブル内にある既存の各行の新しい列に一意の識別子値を設定できます。  
  
 [!INCLUDE[ssDE](../../includes/ssde-md.md)]では、列定義において DEFAULT、IDENTITY、ROWGUIDCOL または列制約を指定する場合に順序は適用されません。  
  
 列の追加によりデータ行のサイズが 8,060 バイトを超える場合、ALTER TABLE ステートメントは失敗します。  
  
## <a name="examples"></a>例  
 例については、「[ALTER TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-table-transact-sql.md)」を参照してください。  
  
## <a name="see-also"></a>参照  
 [ALTER TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-table-transact-sql.md)  
  
