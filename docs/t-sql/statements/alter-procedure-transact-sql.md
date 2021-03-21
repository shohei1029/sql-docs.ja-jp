---
description: ALTER PROCEDURE (Transact-SQL)
title: ALTER PROCEDURE (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 05/01/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- ALTER_PROCEDURE_TSQL
- ALTER_PROC_TSQL
- ALTER PROC
- ALTER PROCEDURE
dev_langs:
- TSQL
helpviewer_keywords:
- ALTER PROCEDURE statement
- stored procedure modifications [SQL Server]
- modifying stored procedures
- stored procedures [SQL Server], modifying
ms.assetid: ed9b2f76-11ec-498d-a95e-75b490a75733
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 10491ba478d5aec85f6e213756ebe00e9904b59d
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104746422"
---
# <a name="alter-procedure-transact-sql"></a>ALTER PROCEDURE (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] で、CREATE PROCEDURE ステートメントを使用して作成した既存のプロシージャを変更します。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則 (Transact-SQL)](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql  
-- Syntax for SQL Server and Azure SQL Database
  
ALTER { PROC | PROCEDURE } [schema_name.] procedure_name [ ; number ]   
    [ { @parameter [ type_schema_name. ] data_type }   
        [ VARYING ] [ = default ] [ OUT | OUTPUT ] [READONLY]  
    ] [ ,...n ]   
[ WITH <procedure_option> [ ,...n ] ]  
[ FOR REPLICATION ]   
AS { [ BEGIN ] sql_statement [;] [ ...n ] [ END ] }  
[;]  
  
<procedure_option> ::=   
    [ ENCRYPTION ]  
    [ RECOMPILE ]  
    [ EXECUTE AS Clause ]  
```  
  
```syntaxsql  
-- Syntax for SQL Server CLR Stored Procedure  
  
ALTER { PROC | PROCEDURE } [schema_name.] procedure_name [ ; number ]   
    [ { @parameter [ type_schema_name. ] data_type }   
        [ = default ] [ OUT | OUTPUT ] [READONLY]  
    ] [ ,...n ]   
[ WITH EXECUTE AS Clause ]  
AS { EXTERNAL NAME assembly_name.class_name.method_name }  
[;]  
```  
  
```syntaxsql  
-- Syntax for Azure Synapse Analytics and Parallel Data Warehouse  
  
ALTER { PROC | PROCEDURE } [schema_name.] procedure_name  
    [ { @parameterdata_type } [= ] ] [ ,...n ]  
AS { [ BEGIN ] sql_statement [ ; ] [ ,...n ] [ END ] }  
[;]  
```  
  

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
 *schema_name*  
 プロシージャが属するスキーマの名前を指定します。  
  
 *procedure_name*  
 変更するプロシージャの名前です。 プロシージャ名は、 [識別子](../../relational-databases/databases/database-identifiers.md)のルールに従っている必要があります。  
  
 **;** *number*  
 同じ名前のプロシージャをグループ化するために使用する既存の整数を指定します (省略可能)。グループ化されたプロシージャは、DROP PROCEDURE ステートメントを使用して一度に削除できます。  
  
> [!NOTE]  
>  [!INCLUDE[ssNoteDepFutureAvoid](../../includes/ssnotedepfutureavoid-md.md)]  
  
 **@** *parameter*  
 プロシージャ内のパラメーターです。 パラメーターは 2,100 個まで指定できます。  
  
 [ _type\_schema\_name_ **.** ] _data\_type_  
 パラメーターのデータ型とそれが属するスキーマです。  
  
 データ型の制約については、「[CREATE PROCEDURE &#40;Transact-SQL&#41;](../../t-sql/statements/create-procedure-transact-sql.md)」をご覧ください。  
  
 VARYING  
 出力パラメーターとしてサポートされている結果セットを指定します。 このパラメーターはストアド プロシージャによって動的に作成され、その内容は変化します。 カーソル パラメーターにのみ適用されます。 このオプションは、CLR プロシージャでは無効です。  
  
 *default*  
 パラメーターの既定値です。  
  
 OUT | OUTPUT  
 パラメーターが戻りパラメーターであることを示します。  
  
 READONLY  
 パラメーターをプロシージャの本体内で更新または変更できないことを示します。 パラメーターの型がテーブル値型の場合は、READONLY を指定する必要があります。  
  
 RECOMPILE  
 [!INCLUDE[ssDE](../../includes/ssde-md.md)]では、このプロシージャ用のプランをキャッシュせず、実行時にプロシージャを再コンパイルします。  
  
 ENCRYPTION  
 **適用対象**:SQL Server ([!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降)、[!INCLUDE[ssSDSFull](../../includes/sssdsfull-md.md)]。  
  
 [!INCLUDE[ssDE](../../includes/ssde-md.md)]で、ALTER PROCEDURE ステートメントの元のテキストを、暗号化した形式に変換することを示します。 暗号化した形式の出力は、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 内のどのカタログ ビューでも直接見ることはできません。 システム テーブルまたはデータベース ファイルへのアクセス権を持たないユーザーは、暗号化した形式のテキストを取得できません。 ただし、[DAC ポート](../../database-engine/configure-windows/diagnostic-connection-for-database-administrators.md)経由でシステム テーブルにアクセスする権限、または直接データベース ファイルにアクセスする権限を持っているユーザーは、このテキストを使用できます。 また、サーバー プロセスにデバッガーをアタッチできるユーザーは、実行時、元のプロシージャをメモリから取得できます。 システム メタデータのアクセス方法について詳しくは、「[メタデータ表示の構成](../../relational-databases/security/metadata-visibility-configuration.md)」をご覧ください。  
  
 このオプションを使って作成したプロシージャを、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] レプリケーションの一部として発行することはできません。  
  
 このオプションは、共通言語ランタイム (CLR) のストアド プロシージャには指定できません。  
  
> [!NOTE]  
>  [!INCLUDE[ssDE](../../includes/ssde-md.md)]では、アップグレード中に、**sys.sql_modules** に格納されている暗号化コメントにより、プロシージャが再作成されます。  
  
 EXECUTE AS  
 アクセス後にストアド プロシージャを実行するセキュリティ コンテキストを指定します。  
  
 詳細については、「[EXECUTE AS 句 &#40;Transact-SQL&#41;](../../t-sql/statements/execute-as-clause-transact-sql.md)」を参照してください。  
  
 FOR REPLICATION  

  
 レプリケーション用に作成したストアド プロシージャは、サブスクライバーでは実行できないことを示します。 FOR REPLICATION オプションを指定して作成したストアド プロシージャは、ストアド プロシージャ フィルターとして使用され、レプリケーション時にのみ実行されます。 FOR REPLICATION を指定した場合、パラメーターは宣言できません。 このオプションは、CLR プロシージャでは無効です。 RECOMPILE オプションは、FOR REPLICATION を使って作成されたプロシージャでは無視されます。  
  
> [!NOTE]  
>  このオプションは、包含データベースでは使用できません。  
  
 { [ BEGIN ] *sql_statement* [;] [ ...*n* ] [ END ] }  
 プロシージャの本体を構成する 1 つ以上の [!INCLUDE[tsql](../../includes/tsql-md.md)] ステートメントを指定します。 省略可能な BEGIN キーワードと END キーワードを使用して、ステートメントを囲むことができます。 詳しくは、「[CREATE PROCEDURE &#40;Transact-SQL&#41;](../../t-sql/statements/create-procedure-transact-sql.md)」の「ベスト プラクティス」、「全般的な解説」、「制限事項と制約事項」をご覧ください。  
  
 EXTERNAL NAME _assembly\_name_ **.** _class\_name_ **.** _method\_name_  
 **適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。  
  
 CLR ストアド プロシージャで参照する [!INCLUDE[dnprdnshort](../../includes/dnprdnshort-md.md)] アセンブリのメソッドを指定します。 *class_name* は、有効な [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 識別子であること、およびアセンブリにクラスとして存在していることが必要です。 クラス名に名前空間とその区切り文字のピリオド (**.**) が含まれる場合は、クラス名をかっこ (**[ ]**) または引用符 (**""**) で区切る必要があります。 指定するメソッドは、クラスの静的メソッドであることが必要です。  
  
 既定では、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] は CLR コードを実行できません。 共通言語ランタイム モジュールを参照するデータベース オブジェクトを作成、変更、および削除することはできますが、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] でこれらの参照を実行するには、[clr enabled オプション](../../database-engine/configure-windows/clr-enabled-server-configuration-option.md)を有効にする必要があります。 このオプションを有効にするには、[sp_configure](../../relational-databases/system-stored-procedures/sp-configure-transact-sql.md) を使用します。  
  
> [!NOTE]  
>  CLR プロシージャは、包含データベースではサポートされていません。  
  
## <a name="general-remarks"></a>全般的な解説  
 [!INCLUDE[tsql](../../includes/tsql-md.md)] ストアド プロシージャを CLR ストアド プロシージャに変更したり、その逆に変更することはできません。  
  
 ALTER PROCEDURE では権限は変更されず、従属ストアド プロシージャまたはトリガーに影響することはありません。 しかし、QUOTED_IDENTIFIER と ANSI_NULLS の現在のセッション設定は、変更時にストアド プロシージャに含まれます。 ストアド プロシージャの最初の作成時に有効であった設定と変更後の設定が異なる場合、ストアド プロシージャの動作が変わる可能性があります。  
  
 以前のプロシージャ定義が WITH ENCRYPTION または WITH RECOMPILE を使用して作成されている場合、これらのオプションは、ALTER PROCEDURE に指定されるときだけ有効になります。  
  
 ストアド プロシージャについて詳しくは、「[CREATE PROCEDURE &#40;Transact-SQL&#41;](../../t-sql/statements/create-procedure-transact-sql.md)」をご覧ください。  
  
## <a name="security"></a>セキュリティ  
  
### <a name="permissions"></a>アクセス許可  
 プロシージャの **ALTER** 権限、または **db_ddladmin** 固定データベース ロールのメンバーシップが必要です。  
  
## <a name="examples"></a>例  
 次の例では、`uspVendorAllInfo` ストアド プロシージャを作成します。 このプロシージャは、[!INCLUDE[ssSampleDBCoFull](../../includes/sssampledbcofull-md.md)] を提供するすべてのベンダーの名前と、そのベンダーの提供製品、信用格付け、およびベンダーが現時点で製品を提供できるかどうかを返します。 このプロシージャを作成した後、別の結果セットを返すように変更されます。  
  
```sql
IF OBJECT_ID ( 'Purchasing.uspVendorAllInfo', 'P' ) IS NOT NULL   
    DROP PROCEDURE Purchasing.uspVendorAllInfo;  
GO  
CREATE PROCEDURE Purchasing.uspVendorAllInfo  
WITH EXECUTE AS CALLER  
AS  
    SET NOCOUNT ON;  
    SELECT v.Name AS Vendor, p.Name AS 'Product name',   
      v.CreditRating AS 'Rating',   
      v.ActiveFlag AS Availability  
    FROM Purchasing.Vendor v   
    INNER JOIN Purchasing.ProductVendor pv  
      ON v.BusinessEntityID = pv.BusinessEntityID   
    INNER JOIN Production.Product p  
      ON pv.ProductID = p.ProductID   
    ORDER BY v.Name ASC;  
GO    
```  
  
 次の例では、`uspVendorAllInfo` ストアド プロシージャを変更します。 EXECUTE AS CALLER 句を削除し、指定した製品を供給するベンダーだけを返すようにプロシージャの本体を変更します。 ここでは、 `LEFT` 関数および `CASE` 関数を使用して、結果セットの表示をカスタマイズします。  
  
```sql  
USE AdventureWorks2012;  
GO  
ALTER PROCEDURE Purchasing.uspVendorAllInfo  
    @Product VARCHAR(25)   
AS  
    SET NOCOUNT ON;  
    SELECT LEFT(v.Name, 25) AS Vendor, LEFT(p.Name, 25) AS 'Product name',   
    'Rating' = CASE v.CreditRating   
        WHEN 1 THEN 'Superior'  
        WHEN 2 THEN 'Excellent'  
        WHEN 3 THEN 'Above average'  
        WHEN 4 THEN 'Average'  
        WHEN 5 THEN 'Below average'  
        ELSE 'No rating'  
        END  
    , Availability = CASE v.ActiveFlag  
        WHEN 1 THEN 'Yes'  
        ELSE 'No'  
        END  
    FROM Purchasing.Vendor AS v   
    INNER JOIN Purchasing.ProductVendor AS pv  
      ON v.BusinessEntityID = pv.BusinessEntityID   
    INNER JOIN Production.Product AS p   
      ON pv.ProductID = p.ProductID   
    WHERE p.Name LIKE @Product  
    ORDER BY v.Name ASC;  
GO  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 ```   
 Vendor               Product name  Rating    Availability  
-------------------- ------------- -------   ------------  
Proseware, Inc.      LL Crankarm   Average   No  
Vision Cycles, Inc.  LL Crankarm   Superior  Yes  
(2 row(s) affected)`  
```  

## <a name="see-also"></a>参照  
 [CREATE PROCEDURE &#40;Transact-SQL&#41;](../../t-sql/statements/create-procedure-transact-sql.md)   
 [DROP PROCEDURE &#40;Transact-SQL&#41;](../../t-sql/statements/drop-procedure-transact-sql.md)   
 [EXECUTE &#40;Transact-SQL&#41;](../../t-sql/language-elements/execute-transact-sql.md)   
 [EXECUTE AS &#40;Transact-SQL&#41;](../../t-sql/statements/execute-as-transact-sql.md)   
 [EVENTDATA &#40;Transact-SQL&#41;](../../t-sql/functions/eventdata-transact-sql.md)   
 [ストアド プロシージャ &#40;データベース エンジン&#41;](../../relational-databases/stored-procedures/stored-procedures-database-engine.md)   
 [sys.procedures &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-procedures-transact-sql.md)  
  
  
