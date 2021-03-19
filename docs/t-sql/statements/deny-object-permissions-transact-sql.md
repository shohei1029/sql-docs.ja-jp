---
description: DENY (オブジェクトの権限の拒否) (Transact-SQL)
title: DENY (オブジェクトの権限の拒否) (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
dev_langs:
- TSQL
helpviewer_keywords:
- DENY statement, objects
- table permissions [SQL Server]
ms.assetid: 0b8d3ddc-38c0-4241-b7bb-ee654a5081aa
author: VanMSFT
ms.author: vanto
ms.openlocfilehash: 021cea9426fd6fb1c6e14c450fa37e69ef0048a6
ms.sourcegitcommit: 2cc2e4e17ce88ef47cda32a60a02d929e617738e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/15/2021
ms.locfileid: "103473216"
---
# <a name="deny-object-permissions-transact-sql"></a>DENY (オブジェクトの権限の拒否) (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  セキュリティ保護可能なリソースの OBJECT クラスのメンバーに対する権限を拒否します。 OBJECT クラスのメンバーは、テーブル、ビュー、テーブル値関数、ストアド プロシージャ、拡張ストアド プロシージャ、スカラー関数、集計関数、サービス キュー、およびシノニムです。  

  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql
DENY <permission> [ ,...n ] ON   
    [ OBJECT :: ][ schema_name ]. object_name [ ( column [ ,...n ] ) ]  
        TO <database_principal> [ ,...n ]   
    [ CASCADE ]  
        [ AS <database_principal> ]  
  
<permission> ::=  
    ALL [ PRIVILEGES ] | permission [ ( column [ ,...n ] ) ]  
  
<database_principal> ::=   
        Database_user   
    | Database_role   
    | Application_role   
    | Database_user_mapped_to_Windows_User   
    | Database_user_mapped_to_Windows_Group   
    | Database_user_mapped_to_certificate   
    | Database_user_mapped_to_asymmetric_key   
    | Database_user_with_no_login  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
 *permission*  
 スキーマに含まれるオブジェクトで拒否できる権限を指定します。 権限の一覧については、後の「解説」を参照してください。  
  
 ALL  
 ALL を指定しても、可能な権限がすべて拒否されるわけではありません。 ALL を指定すると、指定したオブジェクトに適用されるすべての ANSI-92 権限を拒否することになります。 ALL の意味は、状況に応じて次のようになります。  
  
 - スカラー関数の権限: EXECUTE、REFERENCES。  
 - テーブル値関数の権限: DELETE、INSERT、REFERENCES、SELECT、UPDATE。  
 - ストアド プロシージャの権限: EXECUTE。  
 - テーブルの権限: DELETE、INSERT、REFERENCES、SELECT、UPDATE。  
 - ビューの権限: DELETE、INSERT、REFERENCES、SELECT、UPDATE。  
  
PRIVILEGES  
 ANSI-92 準拠のために用意されています。 ALL の動作は変更されません。  
  
*column*  
 権限を拒否するテーブル、ビュー、またはテーブル値関数内の列の名前を指定します。 かっこ **( )** で囲む必要があります。 列で拒否できるのは、SELECT、REFERENCES、および UPDATE の各権限だけです。 *column* は permission 句内、またはセキュリティ保護可能なリソースの名前の後に指定できます。  
  
> [!CAUTION]  
>  テーブル レベルの DENY は列レベルの GRANT ステートメントよりも優先されません。 この動作は権限の階層内で一貫していませんが、旧バージョンとの互換性のために保持されています。 SQL Server では、[Common Criteria Compliance Enabled サーバー構成](/../../database-engine/configure-windows/common-criteria-compliance-enabled-server-configuration-option.md)で実行するようにサーバーが構成されている場合、この動作は異なります。 ただし、一般的な方法としてではなく、通常は慎重に使用する必要があります。
  
 ON [ OBJECT **::** ] [ *schema_name* ] **.** *object_name*  
 権限を拒否するオブジェクトを指定します。 OBJECT 句は、*schema_name* を指定する場合は省略可能です。 OBJECT 句を使用する場合は、スコープ修飾子 ( **::** ) が必要です。 *schema_name* が指定されていない場合、既定のスキーマが使用されます。 *schema_name* が指定されている場合、スキーマのスコープ修飾子 ( **.** ) が必要です。  
  
 TO \<database_principal>  
 権限を拒否するプリンシパルを指定します。  
  
 CASCADE  
 このプリンシパルによって権限が許可されている他のプリンシパルに対しても、同じ権限を拒否することを示します。  
  
 AS \<database_principal>  
 このクエリを実行するプリンシパルが権限を拒否する権利を取得した、元のプリンシパルを指定します。  
  
 *Database_user*  
 データベース ユーザーを指定します。  
  
 *Database_role*  
 データベース ロールを指定します。  
  
 *Application_role*  
 アプリケーション ロールを指定します。  
  
 *Database_user_mapped_to_Windows_User*  
 Windows ユーザーにマップされているデータベース ユーザーを指定します。  
  
 *Database_user_mapped_to_Windows_Group*  
 Windows グループにマップされているデータベース ユーザーを指定します。  
  
 *Database_user_mapped_to_certificate*  
 証明書にマップされているデータベース ユーザーを指定します。  
  
 *Database_user_mapped_to_asymmetric_key*  
 非対称キーにマップされているデータベース ユーザーを指定します。  
  
 *Database_user_with_no_login*  
 対応するサーバー レベルのプリンシパルがないデータベース ユーザーを指定します。  
  
## <a name="remarks"></a>解説  
 オブジェクトに関する情報は、各種カタログ ビューに表示されます。 詳しくは、「[オブジェクト カタログ ビュー &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/object-catalog-views-transact-sql.md)」をご覧ください。  
  
 オブジェクトは、スキーマ レベルのセキュリティ保護可能なリソースで、権限の階層で親となっているスキーマに含まれています。 次の表に、オブジェクトで拒否できる権限のうち最も限定的なものを、それらを暗黙的に含む一般的な権限と共に示します。  
  
|オブジェクト権限|権限が含まれるオブジェクト権限|権限が含まれるスキーマ権限|  
|-----------------------|----------------------------------|----------------------------------|  
|ALTER|CONTROL|ALTER|  
|CONTROL|CONTROL|CONTROL|  
|DELETE|CONTROL|DELETE|  
|EXECUTE|CONTROL|EXECUTE|  
|INSERT|CONTROL|INSERT|  
|RECEIVE|CONTROL|CONTROL|  
|REFERENCES|CONTROL|REFERENCES|  
|SELECT|RECEIVE|SELECT|  
|TAKE OWNERSHIP|CONTROL|CONTROL|  
|UPDATE|CONTROL|UPDATE|  
|VIEW CHANGE TRACKING|CONTROL|VIEW CHANGE TRACKING|  
|VIEW DEFINITION|CONTROL|VIEW DEFINITION|  
  
## <a name="permissions"></a>アクセス許可  
 オブジェクトに対する CONTROL 権限が必要です。  
  
 AS 句を使用する場合、指定されるプリンシパルは、権限が拒否されるオブジェクトを所有している必要があります。  
  
## <a name="examples"></a>例  
以下の例では、AdventureWorks データベースを使います。
  
### <a name="a-denying-select-permission-on-a-table"></a>A. テーブルの SELECT 権限を拒否する  
 次の例では、テーブル `Person.Address` の `SELECT` 権限を、ユーザー `RosaQdM` に対して拒否します。  
  
```sql  
DENY SELECT ON OBJECT::Person.Address TO RosaQdM;  
GO  
```  
  
### <a name="b-denying-execute-permission-on-a-stored-procedure"></a>B. ストアド プロシージャの EXECUTE 権限を拒否する  
 次の例では、ストアド プロシージャ `EXECUTE` の `HumanResources.uspUpdateEmployeeHireInfo` 権限を、アプリケーション ロール `Recruiting11` に対して拒否します。  
  
```sql  
DENY EXECUTE ON OBJECT::HumanResources.uspUpdateEmployeeHireInfo  
    TO Recruiting11;  
GO   
```  
  
### <a name="c-denying-references-permission-on-a-view-with-cascade"></a>C. CASCADE を指定してビューの REFERENCES 権限を拒否する  
 次の例では、ビュー `HumanResources.vEmployee` 内の列 `BusinessEntityID` の `REFERENCES` 権限を、ユーザー `Wanida` に対して拒否します。ここでは `CASCADE` を使用します。  
  
```sql  
DENY REFERENCES (BusinessEntityID) ON OBJECT::HumanResources.vEmployee   
    TO Wanida CASCADE;  
GO  
```  
  
## <a name="see-also"></a>参照  
 [GRANT (オブジェクトの権限の許可) &#40;Transact-SQL&#41;](../../t-sql/statements/grant-object-permissions-transact-sql.md)   
 [REVOKE (オブジェクトの権限の取り消し) &#40;Transact-SQL&#41;](../../t-sql/statements/revoke-object-permissions-transact-sql.md)   
 [オブジェクト カタログ ビュー &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/object-catalog-views-transact-sql.md)   
 [アクセス許可 &#40;データベース エンジン&#41;](../../relational-databases/security/permissions-database-engine.md)   
 [プリンシパル &#40;データベース エンジン&#41;](../../relational-databases/security/authentication-access/principals-database-engine.md)   
 [Securables](../../relational-databases/security/securables.md)   
 [sys.fn_builtin_permissions &#40;Transact-SQL&#41;](../../relational-databases/system-functions/sys-fn-builtin-permissions-transact-sql.md)   
 [HAS_PERMS_BY_NAME &#40;Transact-SQL&#41;](../../t-sql/functions/has-perms-by-name-transact-sql.md)   
 [sys.fn_my_permissions &#40;Transact-SQL&#41;](../../relational-databases/system-functions/sys-fn-my-permissions-transact-sql.md)  
  
  
