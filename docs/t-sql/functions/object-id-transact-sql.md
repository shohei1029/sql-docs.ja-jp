---
description: OBJECT_ID (Transact-SQL)
title: OBJECT_ID (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- OBJECT_ID
- OBJECT_ID_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- objects [SQL Server], IDs
- identification numbers [SQL Server], database objects
- checking object exists
- IDs [SQL Server], database objects
- OBJECT_ID function
- database objects [SQL Server], IDs
- displaying object IDs
- viewing object IDs
- verifying object exists
ms.assetid: f89286db-440f-4218-a828-30881ce3077a
author: VanMSFT
ms.author: vanto
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 6039bcbf036093af3fcb3c22e7a3845afb485574
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104735102"
---
# <a name="object_id-transact-sql"></a>OBJECT_ID (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  スキーマ スコープ オブジェクトのデータベース オブジェクト ID 番号を返します。  
  
> [!IMPORTANT]  
>  DDL トリガーなどのスキーマ スコープでないオブジェクトは、OBJECT_ID を使用してクエリできません。 [sys.objects](../../relational-databases/system-catalog-views/sys-objects-transact-sql.md) カタログ ビューにないオブジェクトについては、適切なカタログ ビューを照会してオブジェクト ID 番号を取得してください。 たとえば、DDL トリガーのオブジェクト ID 番号を取得するには、`SELECT OBJECT_ID FROM sys.triggers WHERE name = 'DatabaseTriggerLog``'` を使用します。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql
OBJECT_ID ( '[ database_name . [ schema_name ] . | schema_name . ]   
  object_name' [ ,'object_type' ] )  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
 **'** *object_name* **'**  
 使用するオブジェクトです。 *object_name* は **varchar** または **nvarchar** です。 場合 *object_name* は **varchar**, に暗黙的に変換されます **nvarchar** です。 データベース名とスキーマ名の指定は省略可能です。  
  
 **'** *object_type* **'**  
 スキーマ スコープのオブジェクトの種類です。 *object_type* は **varchar** または **nvarchar** です。 場合 *object_type* は **varchar**, に暗黙的に変換されます **nvarchar** です。 オブジェクトの種類の一覧については、「[sys.objects &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-objects-transact-sql.md)」 の **type** 列を参照してください。  
  
## <a name="return-types"></a>戻り値の型  
 **int**  
  
## <a name="exceptions"></a>例外  
 空間インデックスに対して、OBJECT_ID は NULL を返します。  
  
 エラーが発生した場合は NULL を返します。  
  
 ユーザーが所有しているか、または権限を与えられている、セキュリティ保護可能なリソースのメタデータのみを表示できます。 つまり、オブジェクトに対する権限がユーザーに与えられていない場合、メタデータを生成する組み込み関数 (OBJECT_ID など) が NULL を返す可能性があります。 詳細については、「 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)」を参照してください。  
  
## <a name="remarks"></a>解説  
 システム関数へのパラメーターが省略可能の場合は、現在のデータベース、ホスト コンピューター、サーバー ユーザー、またはデータベース ユーザーが推測されます。 組み込み関数の後には常にかっこが必要です。  
  
 一時テーブル名を指定する場合は、現在のデータベースが **tempdb** でない限り、一時テーブル名の前にデータベース名を指定する必要があります。 (例: `SELECT OBJECT_ID('tempdb..#mytemptable')`)。  
  
 システム関数は、選択リストの中、WHERE 句の中、また、式を使える所ならどこにでも使用できます。 詳しくは、「[式 &#40;Transact-SQL&#41;](../../t-sql/language-elements/expressions-transact-sql.md)」および「[WHERE &#40;Transact-SQL&#41;](../../t-sql/queries/where-transact-sql.md)」をご覧ください。  
  
## <a name="examples"></a>例  
  
### <a name="a-returning-the-object-id-for-a-specified-object"></a>A. 指定したオブジェクトのオブジェクト ID を返す  
 次の例では、[!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] データベース内の `Production.WorkOrder` テーブルのオブジェクト ID を返します。  
  
```sql  
USE master;  
GO  
SELECT OBJECT_ID(N'AdventureWorks2012.Production.WorkOrder') AS 'Object ID';  
GO  
```  
  
### <a name="b-verifying-that-an-object-exists"></a>B. オブジェクトが存在することを確認する  
 次の例では、指定したテーブルにオブジェクト ID があることを確認して、テーブルが存在するかどうかをチェックします。 テーブルが存在する場合は、削除されます。 テーブルが存在しない場合、`DROP TABLE` ステートメントは実行されません。  
  
```sql  
USE AdventureWorks2012;  
GO  
IF OBJECT_ID (N'dbo.AWBuildVersion', N'U') IS NOT NULL  
DROP TABLE dbo.AWBuildVersion;  
GO  
```  
  
### <a name="c-using-object_id-to-specify-the-value-of-a-system-function-parameter"></a>C. OBJECT_ID を使用して、システム関数パラメーターの値を指定する  
 次の例では、[!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] データベース内にある `Person.Address` テーブルのすべてのインデックスとパーティションについて、[sys.dm_db_index_operational_stats](../../relational-databases/system-dynamic-management-views/sys-dm-db-index-operational-stats-transact-sql.md) 関数を使用して情報を返しています。  
 
[!INCLUDE[synapse-analytics-od-unsupported-syntax](../../includes/synapse-analytics-od-unsupported-syntax.md)]
  
> [!IMPORTANT]  
>  [!INCLUDE[tsql](../../includes/tsql-md.md)] 関数 DB_ID および OBJECT_ID を使ってパラメーター値を返すときには、有効な ID が返されることを常に確認してください。 存在しない場合やスペルが正しくない場合など、データベースまたはオブジェクト名が見つからない場合、両方の関数が NULL を返します。 **sys.dm_db_index_operational_stats** 関数では、NULL 値はすべてのデータベースまたはすべてのオブジェクトを指定するワイルドカード値として解釈されます。 これは、意図しない操作である可能性があるため、このセクションの例では、データベースおよびオブジェクト ID を確認する安全な方法を示します。
  
```sql  
DECLARE @db_id INT;  
DECLARE @object_id INT;  
SET @db_id = DB_ID(N'AdventureWorks2012');  
SET @object_id = OBJECT_ID(N'AdventureWorks2012.Person.Address');  
IF @db_id IS NULL   
  BEGIN;  
    PRINT N'Invalid database';  
  END;  
ELSE IF @object_id IS NULL  
  BEGIN;  
    PRINT N'Invalid object';  
  END;  
ELSE  
  BEGIN;  
    SELECT * FROM sys.dm_db_index_operational_stats(@db_id, @object_id, NULL, NULL);  
  END;  
GO  
```  
  
## <a name="examples-sssdwfull-and-sspdw"></a>例: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)]、[!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="d-returning-the-object-id-for-a-specified-object"></a>D. 指定したオブジェクトのオブジェクト ID を返す  
 次の例では、[!INCLUDE[ssawPDW](../../includes/ssawpdw-md.md)] データベース内の `FactFinance` テーブルのオブジェクト ID を返します。  
  
```sql  
SELECT OBJECT_ID('AdventureWorksPDW2012.dbo.FactFinance') AS 'Object ID';  
```  
  
## <a name="see-also"></a>参照  
 [メタデータ関数 &#40;Transact-SQL&#41;](../../t-sql/functions/metadata-functions-transact-sql.md)   
 [sys.objects &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-objects-transact-sql.md)   
 [sys.dm_db_index_operational_stats &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-index-operational-stats-transact-sql.md)   
 [OBJECT_DEFINITION &#40;Transact-SQL&#41;](../../t-sql/functions/object-definition-transact-sql.md)   
 [OBJECT_NAME &#40;Transact-SQL&#41;](../../t-sql/functions/object-name-transact-sql.md)  
  
  

