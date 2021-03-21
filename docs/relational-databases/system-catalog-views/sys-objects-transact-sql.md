---
description: sys.objects (Transact-SQL)
title: sys.objects (Transact-SQL)
ms.custom: ''
ms.date: 01/22/2021
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- sys.objects_TSQL
- objects
- sys.objects
- objects_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.objects catalog view
- table-valued parameters, sys.objects catalog view
- user-defined table types [SQL Server]
- table types [SQL Server]
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 2341290417ff87a30c3a23786bfa598233426223
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104748672"
---
# <a name="sysobjects-transact-sql"></a>sys.objects (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  ネイティブコンパイルのスカラーユーザー定義関数を含む、データベース内に作成される、ユーザー定義のスキーマスコープオブジェクトごとに1行のデータを格納します。  
  
 詳しくは、「[インメモリ OLTP でのユーザー定義のスカラー関数](../../relational-databases/in-memory-oltp/scalar-user-defined-functions-for-in-memory-oltp.md)」をご覧ください。  
  
> [!NOTE]  
>  sys. オブジェクトはスキーマスコープではないため、DDL トリガーを表示しません。 DML と DDL の両方のトリガーが、 [sys. トリガー](../../relational-databases/system-catalog-views/sys-triggers-transact-sql.md)にあります。 システムトリガーでは、さまざまな種類のトリガーに対して名前スコープルールの組み合わせがサポートされています。  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|name|**sysname**|オブジェクト名|  
|object_id|**int**|オブジェクト ID 番号。 データベース内で一意です。|  
|principal_id|**int**|個々の所有者の ID (スキーマの所有者と異なる場合)。 既定では、スキーマに含まれるオブジェクトはスキーマの所有者によって所有されます。 ただし、ALTER AUTHORIZATION ステートメントを使用して所有権を変更することによって、代替所有者を指定できます。<br /><br /> 別の所有者が存在しない場合は NULL になります。<br /><br /> オブジェクトの型が次のいずれかの場合は NULL になります。<br /><br /> C = CHECK 制約<br /><br /> D = DEFAULT (制約またはスタンドアロン)<br /><br /> F = FOREIGN KEY 制約<br /><br /> PK = PRIMARY KEY 制約<br /><br /> R = Rule (旧形式、スタンドアロン)<br /><br /> TA = アセンブリ (CLR 統合) トリガー<br /><br /> TR = SQL トリガー<br /><br /> UQ = UNIQUE 制約<br /><br /> EC = エッジ制約 |  
|schema_id|**int**|オブジェクトが含まれているスキーマの ID。<br /><br /> スキーマ スコープ システム オブジェクトは、常に sys スキーマまたは INFORMATION_SCHEMA スキーマに含まれています。|  
|parent_object_id|**int**|このオブジェクトが属するオブジェクトの ID です。<br /><br /> 0 = 子オブジェクトではありません。|  
|type|**char(2)**|オブジェクトの種類:<br /><br /> AF = 集計関数 (CLR)<br /><br /> C = CHECK 制約<br /><br /> D = DEFAULT (制約またはスタンドアロン)<br /><br /> F = FOREIGN KEY 制約<br /><br /> FN = SQL スカラー関数<br /><br /> FS = アセンブリ (CLR) スカラー関数<br /><br /> FT = アセンブリ (CLR) テーブル値関数<br /><br /> IF = SQL インライン テーブル値関数<br /><br /> 内部テーブル<br /><br /> P = SQL ストアドプロシージャ<br /><br /> PC = アセンブリ (CLR) ストアドプロシージャ<br /><br /> PG = プランガイド<br /><br /> PK = PRIMARY KEY 制約<br /><br /> R = Rule (旧形式、スタンドアロン)<br /><br /> RF = レプリケーション-フィルター-プロシージャ<br /><br /> S = システム ベース テーブル<br /><br /> SN = シノニム<br /><br /> SO = Sequence オブジェクト<br /><br /> U = テーブル (ユーザー定義)<br /><br /> V = ビュー<br /><br /> EC = エッジ制約 <br /><br /> <br /><br /> **適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降。<br /><br /> SQ = サービスキュー<br /><br /> TA = アセンブリ (CLR) DML トリガー<br /><br /> TF = SQL テーブル値関数<br /><br /> TR = SQL DML トリガー<br /><br /> TT = テーブル型<br /><br /> UQ = UNIQUE 制約<br /><br /> X = 拡張ストアド プロシージャ<br /><br /> <br /><br /> **適用対象**: [!INCLUDE[sssql14](../../includes/sssql14-md.md)] 以降、、 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 、 [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] 。<br /><br /> ST = STATS_TREE<br /><br /> <br /><br /> **適用対象**: [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] 以降、、 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 、 [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] 。<br /><br /> ET = 外部テーブル|  
|type_desc|**nvarchar(60)**|オブジェクトの種類の説明。<br /><br /> AGGREGATE_FUNCTION<br /><br /> CHECK_CONSTRAINT<br /><br /> CLR_SCALAR_FUNCTION<br /><br /> CLR_STORED_PROCEDURE<br /><br /> CLR_TABLE_VALUED_FUNCTION<br /><br /> CLR_TRIGGER<br /><br /> DEFAULT_CONSTRAINT<br /><br /> EXTENDED_STORED_PROCEDURE<br /><br /> FOREIGN_KEY_CONSTRAINT<br /><br /> INTERNAL_TABLE<br /><br /> PLAN_GUIDE<br /><br /> PRIMARY_KEY_CONSTRAINT<br /><br /> REPLICATION_FILTER_PROCEDURE<br /><br /> RULE<br /><br /> SEQUENCE_OBJECT<br /><br /> <br /><br /> **適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降。<br /><br />  SERVICE_QUEUE<br /><br /> SQL_INLINE_TABLE_VALUED_FUNCTION<br /><br /> SQL_SCALAR_FUNCTION<br /><br /> SQL_STORED_PROCEDURE<br /><br /> SQL_TABLE_VALUED_FUNCTION<br /><br /> SQL_TRIGGER<br /><br /> SYNONYM<br /><br /> SYSTEM_TABLE<br /><br /> TABLE_TYPE<br /><br /> UNIQUE_CONSTRAINT<br /><br /> USER_TABLE<br /><br /> VIEW|  
|create_date|**datetime**|オブジェクトが作成された日付です。|  
|modify_date|**datetime**|オブジェクトが ALTER ステートメントを使用して最後に変更された日付です。 オブジェクトがテーブルまたはビューの場合は、テーブルまたはビューのインデックスが作成または変更されたときにも modify_date が変更されます。|  
|is_ms_shipped|**bit**|オブジェクトが内部 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] コンポーネントによって作成されることを示します。|  
|is_published|**bit**|オブジェクトがパブリッシュされます。|  
|is_schema_published|**bit**|オブジェクトのスキーマのみがパブリッシュされることを示します。|  
  
## <a name="remarks"></a>解説  
 [OBJECT_ID](../../t-sql/functions/object-id-transact-sql.md)、 [OBJECT_NAME](../../t-sql/functions/object-name-transact-sql.md)、および[objectproperty](../../t-sql/functions/objectproperty-transact-sql.md)() 組み込み関数を、sys. オブジェクトに表示されるオブジェクトに適用できます。  
  
 このビューには、システムオブジェクトを表示する [sys.system_objects](../../relational-databases/system-catalog-views/sys-system-objects-transact-sql.md)という同じスキーマを持つバージョンがあります。 システムオブジェクトとユーザーオブジェクトの両方を表示する [sys.all_objects](../../relational-databases/system-catalog-views/sys-all-objects-transact-sql.md) という別のビューがあります。 3つのすべてのカタログビューの構造は同じです。  
  
 このバージョンの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、XML インデックスや空間インデックスなどの拡張インデックスは、sys.objects で内部テーブルと見なされます (type = IT および type_desc = INTERNAL_TABLE)。 拡張インデックスの場合:  
  
-   名前インデックステーブルの内部名を指定します。  
  
-   parent_object_id はベース テーブルの object_id です。  
  
-   is_ms_shipped、is_published、is_schema_published の各列は 0 に設定されます。  

**関連する便利なシステムビュー**  
オブジェクトのサブセットを表示するには、次のように、特定の種類のオブジェクトのシステムビューを使用します。  
- [sys.tables](sys-tables-transact-sql.md)  
- [sys.views](sys-views-transact-sql.md)  
- [sys.procedures](sys-procedures-transact-sql.md)  
  
## <a name="permissions"></a>権限  
 [!INCLUDE[ssCatViewPerm](../../includes/sscatviewperm-md.md)] 詳細については、「 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)」を参照してください。  
  
## <a name="examples"></a>例  
  
### <a name="a-returning-all-the-objects-that-have-been-modified-in-the-last-n-days"></a>A. 過去 n 日間に変更されたすべてのオブジェクトを返す  
 次のクエリの `<database_name>` と `<n_days>` を有効な値に置き換えてから、クエリを実行します。  
  
```sql  
USE <database_name>;  
GO  
SELECT name AS object_name   
  ,SCHEMA_NAME(schema_id) AS schema_name  
  ,type_desc  
  ,create_date  
  ,modify_date  
FROM sys.objects  
WHERE modify_date > GETDATE() - <n_days>  
ORDER BY modify_date;  
GO  
```  
  
### <a name="b-returning-the-parameters-for-a-specified-stored-procedure-or-function"></a>B. 指定されたストアドプロシージャまたは関数のパラメーターを返す  
 次のクエリを実行する前に、 `<database_name>` とを `<schema_name.object_name>` 有効な名前に置き換えます。  
  
```sql  
USE <database_name>;  
GO  
SELECT SCHEMA_NAME(schema_id) AS schema_name  
    ,o.name AS object_name  
    ,o.type_desc  
    ,p.parameter_id  
    ,p.name AS parameter_name  
    ,TYPE_NAME(p.user_type_id) AS parameter_type  
    ,p.max_length  
    ,p.precision  
    ,p.scale  
    ,p.is_output  
FROM sys.objects AS o  
INNER JOIN sys.parameters AS p ON o.object_id = p.object_id  
WHERE o.object_id = OBJECT_ID('<schema_name.object_name>')  
ORDER BY schema_name, object_name, p.parameter_id;  
GO  
```  
  
### <a name="c-returning-all-the-user-defined-functions-in-a-database"></a>C. データベース内のすべてのユーザー定義関数を返す  
 次のクエリを実行する前に、を `<database_name>` 有効なデータベース名に置き換えます。  
  
```sql  
USE <database_name>;  
GO  
SELECT name AS function_name   
  ,SCHEMA_NAME(schema_id) AS schema_name  
  ,type_desc  
  ,create_date  
  ,modify_date  
FROM sys.objects  
WHERE type_desc LIKE '%FUNCTION%';  
GO  
```  
  
### <a name="d-returning-the-owner-of-each-object-in-a-schema"></a>D. スキーマ内の各オブジェクトの所有者を返す  
 次のクエリの `<database_name>` と `<schema_name>` をすべて有効な名前に置き換えてから、クエリを実行します。  
  
```sql  
USE <database_name>;  
GO  
SELECT 'OBJECT' AS entity_type  
    ,USER_NAME(OBJECTPROPERTY(object_id, 'OwnerId')) AS owner_name  
    ,name   
FROM sys.objects WHERE SCHEMA_NAME(schema_id) = '<schema_name>'  
UNION   
SELECT 'TYPE' AS entity_type  
    ,USER_NAME(TYPEPROPERTY(SCHEMA_NAME(schema_id) + '.' + name, 'OwnerId')) AS owner_name  
    ,name   
FROM sys.types WHERE SCHEMA_NAME(schema_id) = '<schema_name>'   
UNION  
SELECT 'XML SCHEMA COLLECTION' AS entity_type   
    ,COALESCE(USER_NAME(xsc.principal_id),USER_NAME(s.principal_id)) AS owner_name  
    ,xsc.name   
FROM sys.xml_schema_collections AS xsc JOIN sys.schemas AS s  
    ON s.schema_id = xsc.schema_id  
WHERE s.name = '<schema_name>';  
GO  
```  
  
## <a name="see-also"></a>参照  
 [カタログ ビュー &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/catalog-views-transact-sql.md)   
 [sys.all_objects &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-all-objects-transact-sql.md)   
 [sys.system_objects &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-system-objects-transact-sql.md)   
 [sys.triggers &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-triggers-transact-sql.md)   
 [オブジェクト カタログ ビュー &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/object-catalog-views-transact-sql.md)   
 [SQL Server システムカタログに対するクエリについてよく寄せられる質問](../../relational-databases/system-catalog-views/querying-the-sql-server-system-catalog-faq.yml)   
 [sys.internal_tables &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-internal-tables-transact-sql.md)  
  
  
