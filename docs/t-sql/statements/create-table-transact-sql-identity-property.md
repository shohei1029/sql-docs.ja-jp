---
description: CREATE TABLE (Transact-SQL) IDENTITY (プロパティ)
title: IDENTITY (プロパティ) (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: synapse-analytics, database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- IDENTITY_TSQL
- IDENTITY
dev_langs:
- TSQL
helpviewer_keywords:
- IDENTITY property
- columns [SQL Server], creating
- identity columns [SQL Server], IDENTITY property
- autonumbers, identity numbers
ms.assetid: 8429134f-c821-4033-a07c-f782a48d501c
author: VanMSFT
ms.author: vanto
monikerRange: =azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 848ba2bd20e77c8dff156482256639f4675562ad
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104752672"
---
# <a name="create-table-transact-sql-identity-property"></a>CREATE TABLE (Transact-SQL) IDENTITY (プロパティ)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa.md)]

  テーブルに ID 列を作成します。 このプロパティは、[!INCLUDE[tsql](../../includes/tsql-md.md)] の CREATE TABLE ステートメントおよび ALTER TABLE ステートメントで使用します。  
  
> [!NOTE]  
>  IDENTITY プロパティは、列の行 ID プロパティを示す SQL-DMO **Identity** プロパティとは異なります。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql  
IDENTITY [ (seed , increment) ]
```  
  
[!INCLUDE[synapse-analytics-od-unsupported-syntax](../../includes/synapse-analytics-od-unsupported-syntax.md)]  

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
 *seed*  
 テーブルに読み込まれる最初の行に使用される値です。  
  
 *increment*  
 読み込まれている前の行の ID 値に加算される増分の値です。

 > [!NOTE]
 > Azure Synapse Analytics では、データ ウェアハウスの分散アーキテクチャにより、ID の値は増分になりません。 詳細については、[Synapse SQL プールで IDENTITY を使用して代理キーを作成する方法](/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-identity#allocation-of-values)に関する記事を参照してください。
  
 seed と increment の両方を指定するか、またはどちらも指定しないでください。 どちらも指定しないときの既定値は (1,1) です。  
  
## <a name="remarks"></a>解説  
 ID 列はキー値の生成に使用できます。 列の ID プロパティでは、次の点が保証されます。  
  
-   新しい値はそれぞれ、現在のシードと増分に基づいて生成されます。  
  
-   特定のトランザクションの新しい各値は、テーブルの他の同時実行トランザクションとは異なります。  
  
 列の ID プロパティでは、次の点は保証されません。  
  
-   **値の一意性**: **PRIMARY KEY** 制約、**UNIQUE** 制約、または **UNIQUE** インデックスを使用して、一意性を強制する必要があります。 - 
 
> [!NOTE]
> Azure Synapse Analytics では、**PRIMARY KEY** 制約、**UNIQUE** 制約、または **UNIQUE** インデックスはサポートされません。 詳細については、[Synapse SQL プールで IDENTITY を使用して代理キーを作成する方法](/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-identity#what-is-a-surrogate-key)に関する記事を参照してください。

-   **トランザクション内の連続する値**: 複数行を挿入するトランザクションは、テーブルで同時に他の挿入が実行される可能性があるため、その複数行の連続する値を取得するとは限りません。 連続した値にする必要がある場合、トランザクションはテーブル上で排他ロックを使用するか、**SERIALIZABLE** 分離レベルを使用する必要があります。  
  
-   **サーバーの再起動または他のエラーが発生した後の連続した値** -[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、パフォーマンス上の理由から ID 値をキャッシュすることがあります。割り当てられた値の一部は、データベースの障害やサーバーの再起動が発生したときに失われることがあります。 その結果、挿入時に非連続的な ID 値が生成される場合があります。 非連続的な値が許可されない場合、アプリケーションは独自のメカニズムを使用してキー値を生成する必要があります。 シーケンス ジェネレーターを **NOCACHE** オプションを指定して使用すると、非連続的な値を絶対にコミットされないトランザクションに制限することができます。  
  
-   **値の再利用**: 特定のシードと増分値が指定された特定の ID プロパティでは、ID 値がエンジンによって再利用されることはありません。 特定の挿入ステートメントが失敗した場合または挿入ステートメントがロールバックされた場合、使用した ID 値は失われ、再度生成されることはありません。 その結果、それ以降の ID 値が生成されると、連続しない場合があります。  
  
 これらの制限事項が設計に含まれているのは、パフォーマンスを向上するため、および多くの一般的な状況で許容されるためです。 これらの制限事項が原因で ID 値を使用できない場合は、アプリケーションを使用して、現在の値を保持する別のテーブルを作成し、テーブルと番号の割り当てへのアクセスを管理します。  
  
 ID 列を持つテーブルがレプリケーション用に発行されている場合、使用されているレプリケーションの種類に適した方法で、ID 列を管理する必要があります。 詳細については、「[Replicate Identity Columns](../../relational-databases/replication/publish/replicate-identity-columns.md)」 (ID 列のレプリケート) を参照してください。  
  
 ID 列は 1 つのテーブルにつき 1 つだけ作成できます。  
  
 メモリ最適化テーブルで、シードと増分を 1,1 に設定する必要があります。 シードまたは増分を 1 以外に設定すると、次のエラーが発生します。1 以外のシードと増分の使用は、メモリ最適化テーブルではサポートされません。  
  
## <a name="examples"></a>例  
  
### <a name="a-using-the-identity-property-with-create-table"></a>A. CREATE TABLE で IDENTITY プロパティを使用する  
 次の例では、ID 番号を自動的に増分するテーブルを、`IDENTITY` プロパティを使用して新規作成します。  
  
```sql  
USE AdventureWorks2012;  
  
IF OBJECT_ID ('dbo.new_employees', 'U') IS NOT NULL  
   DROP TABLE new_employees;  
GO  
CREATE TABLE new_employees  
(  
 id_num int IDENTITY(1,1),  
 fname varchar (20),  
 minit char(1),  
 lname varchar(30)  
);  
  
INSERT new_employees  
   (fname, minit, lname)  
VALUES  
   ('Karin', 'F', 'Josephs');  
  
INSERT new_employees  
   (fname, minit, lname)  
VALUES  
   ('Pirkko', 'O', 'Koskitalo');  
```  
  
### <a name="b-using-generic-syntax-for-finding-gaps-in-identity-values"></a>B. ID 値のギャップを検索する汎用構文を使用する  
 次の例では、データが削除された場合に ID 値のギャップを検索する汎用構文を示します。  
  
> [!NOTE]  
>  次に示す [!INCLUDE[tsql](../../includes/tsql-md.md)] スクリプトの最初の部分は、あくまでも参考です。 実行できるのは、"`-- Create the img table`" というコメントで始まる [!INCLUDE[tsql](../../includes/tsql-md.md)] スクリプトです。  
  
```sql 
-- Here is the generic syntax for finding identity value gaps in data.  
-- The illustrative example starts here.  
SET IDENTITY_INSERT tablename ON;  
DECLARE @minidentval column_type;  
DECLARE @maxidentval column_type;  
DECLARE @nextidentval column_type;  
SELECT @minidentval = MIN($IDENTITY), @maxidentval = MAX($IDENTITY)  
    FROM tablename  
IF @minidentval = IDENT_SEED('tablename')  
   SELECT @nextidentval = MIN($IDENTITY) + IDENT_INCR('tablename')  
   FROM tablename t1  
   WHERE $IDENTITY BETWEEN IDENT_SEED('tablename') AND   
      @maxidentval AND  
      NOT EXISTS (SELECT * FROM tablename t2  
         WHERE t2.$IDENTITY = t1.$IDENTITY +   
            IDENT_INCR('tablename'))  
ELSE  
   SELECT @nextidentval = IDENT_SEED('tablename');  
SET IDENTITY_INSERT tablename OFF;  
-- Here is an example to find gaps in the actual data.  
-- The table is called img and has two columns: the first column   
-- called id_num, which is an increasing identification number, and the   
-- second column called company_name.  
-- This is the end of the illustration example.  
  
-- Create the img table.  
-- If the img table already exists, drop it.  
-- Create the img table.  
IF OBJECT_ID ('dbo.img', 'U') IS NOT NULL  
   DROP TABLE img;  
GO  
CREATE TABLE img (id_num INT IDENTITY(1,1), company_name sysname);  
INSERT img(company_name) VALUES ('New Moon Books');  
INSERT img(company_name) VALUES ('Lucerne Publishing');  
-- SET IDENTITY_INSERT ON and use in img table.  
SET IDENTITY_INSERT img ON;  
  
DECLARE @minidentval SMALLINT;  
DECLARE @nextidentval SMALLINT;  
SELECT @minidentval = MIN($IDENTITY) FROM img  
 IF @minidentval = IDENT_SEED('img')  
    SELECT @nextidentval = MIN($IDENTITY) + IDENT_INCR('img')  
    FROM img t1  
    WHERE $IDENTITY BETWEEN IDENT_SEED('img') AND 32766 AND  
      NOT    EXISTS (SELECT * FROM img t2  
          WHERE t2.$IDENTITY = t1.$IDENTITY + IDENT_INCR('img'))  
 ELSE  
    SELECT @nextidentval = IDENT_SEED('img');  
SET IDENTITY_INSERT img OFF;  
```  
  
## <a name="see-also"></a>参照  
 [ALTER TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-table-transact-sql.md)   
 [CREATE TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/create-table-transact-sql.md)   
 [DBCC CHECKIDENT &#40;Transact-SQL&#41;](../../t-sql/database-console-commands/dbcc-checkident-transact-sql.md)   
 [IDENT_INCR &#40;Transact-SQL&#41;](../../t-sql/functions/ident-incr-transact-sql.md)   
 [@@IDENTITY &#40;Transact-SQL&#41;](../../t-sql/functions/identity-transact-sql.md)   
 [IDENTITY &#40;関数&#41; &#40;Transact-SQL&#41;](../../t-sql/functions/identity-function-transact-sql.md)   
 [IDENT_SEED &#40;Transact-SQL&#41;](../../t-sql/functions/ident-seed-transact-sql.md)   
 [SELECT &#40;Transact-SQL&#41;](../../t-sql/queries/select-transact-sql.md)   
 [SET IDENTITY_INSERT &#40;Transact-SQL&#41;](../../t-sql/statements/set-identity-insert-transact-sql.md)   
 [ID 列のレプリケート](../../relational-databases/replication/publish/replicate-identity-columns.md)  
  
  
