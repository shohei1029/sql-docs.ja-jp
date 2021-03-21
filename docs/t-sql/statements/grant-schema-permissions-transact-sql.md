---
description: GRANT (スキーマ権限の許可) (Transact-SQL)
title: GRANT (スキーマ権限の許可) (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 06/19/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
dev_langs:
- TSQL
helpviewer_keywords:
- schemas [SQL Server], permissions
- permissions [SQL Server], schemas
- GRANT statement, schemas
- granting permissions [SQL Server], schemas
ms.assetid: b2aa1fc8-e7af-45d2-9f80-737543c8aa95
author: VanMSFT
ms.author: vanto
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 4a2c11cca7a069a18fea4832d6a1221be3dcbecd
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104754332"
---
# <a name="grant-schema-permissions-transact-sql"></a>GRANT (スキーマ権限の許可) (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  スキーマの権限を許可します。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql
GRANT permission  [ ,...n ] ON SCHEMA :: schema_name  
    TO database_principal [ ,...n ]  
    [ WITH GRANT OPTION ]  
    [ AS granting_principal ]  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
 *permission*  
 スキーマで許可できる権限を指定します。 権限の一覧については、後の「解説」を参照してください。  
  
 ON SCHEMA **::** schema *_name*  
 権限を許可するスキーマを指定します。 スコープ修飾子 **::** が必要です。  
  
 *database_principal*  
 権限を許可するプリンシパルを指定します。 次のいずれかになります。  
  
-   データベース ユーザー  
-   データベース ロール (database role)  
-   アプリケーション ロール (application role)  
-   Windows ログインにマップされているデータベース ユーザー  
-   Windows グループにマップされているデータベース ユーザー  
-   証明書にマップされているデータベース ユーザー  
-   非対称キーにマップされているデータベース ユーザー  
-   サーバー プリンシパルにマップされていないデータベース ユーザー  
  
GRANT OPTION  
 権限が許可されたプリンシパルが、この権限を他のプリンシパルにも許可できることを示します。  
  
AS *granting_principal*  
 このクエリを実行するプリンシパルが権限を許可する権利を取得した、元のプリンシパルを指定します。 次のいずれかになります。  
  
-   データベース ユーザー  
-   データベース ロール (database role)  
-   アプリケーション ロール (application role)  
-   Windows ログインにマップされているデータベース ユーザー  
-   Windows グループにマップされているデータベース ユーザー  
-   証明書にマップされているデータベース ユーザー  
-   非対称キーにマップされているデータベース ユーザー  
-   サーバー プリンシパルにマップされていないデータベース ユーザー  
  
## <a name="remarks"></a>解説  
  
> [!IMPORTANT]  
>  権限許可対象ユーザーは、ALTER 権限と REFERENCE 権限を組み合わせて使用することで、データを表示したり、許可されていない関数を実行できる場合があります。 次に例を示します。テーブルの ALTER 権限と関数の REFERENCE 権限を持つユーザーは、関数を介した計算列を作成して実行できます。 この場合、ユーザーには計算列の SELECT 権限も必要です。  
  
 スキーマは、データベース レベルのセキュリティ保護可能なリソースで、権限の階層で親となっているデータベースに含まれています。 次に、スキーマで許可できる権限のうち最も限定的なものを、それらを暗黙的に含む一般的な権限と共に示します。  
  
|スキーマ権限|権限が含まれるスキーマ権限|権限が含まれるデータベース権限|  
|-----------------------|----------------------------------|------------------------------------|  
|ALTER|CONTROL|ALTER ANY SCHEMA|  
|CONTROL|CONTROL|CONTROL|  
|CREATE SEQUENCE|ALTER|ALTER ANY SCHEMA|  
|DELETE|CONTROL|DELETE|  
|EXECUTE|CONTROL|EXECUTE|  
|INSERT|CONTROL|INSERT|  
|REFERENCES|CONTROL|REFERENCES|  
|SELECT|CONTROL|SELECT|  
|TAKE OWNERSHIP|CONTROL|CONTROL|  
|UPDATE|CONTROL|UPDATE|  
|VIEW CHANGE TRACKING|CONTROL|CONTROL|  
|VIEW DEFINITION|CONTROL|VIEW DEFINITION|  
  
> [!CAUTION]  
>  スキーマに対して ALTER 権限を持つユーザーは、所有権の継承を使用することによって、他のスキーマ内のセキュリティ保護可能なリソース (そのユーザーが明示的にアクセスを拒否されているセキュリティ保護可能なリソースを含む) にアクセスできます。 こうしたアクセスが可能になるのは、プリンシパルがオブジェクトとその参照先オブジェクトを所有している場合に、所有権を継承すると、参照先オブジェクトに対する権限チェックが行われないためです。 スキーマに対して ALTER 権限を持つユーザーは、そのスキーマの所有者が所有するプロシージャ、シノニム、およびビューを作成できます。 所有権の継承により、これらのオブジェクトから、スキーマの所有者が所有する他のスキーマ内の情報にアクセスできるようになります。 したがって、スキーマの所有者が他のスキーマも所有している場合、可能であればそのスキーマに対する ALTER 権限は許可しないようにしてください。  
  
 たとえば、この問題が発生するシナリオを次に挙げます。 これらのシナリオでは、U1 というユーザーが、S1 スキーマに対して ALTER 権限を持っていると想定しています。 U1 ユーザーには、スキーマ S2 内の T1 というテーブル オブジェクトへのアクセスが拒否されています。 S1 スキーマと S2 スキーマは、同じ所有者が所有しています。  
  
 U1 ユーザーは、データベースに対して CREATE PROCEDURE 権限を持ち、S1 スキーマに対して EXECUTE 権限を持っているとします。 この場合、U1 ユーザーはストアド プロシージャを作成し、そのストアド プロシージャ内で、拒否されたオブジェクト T1 にアクセスすることができます。  
  
 U1 ユーザーは、データベースに対して CREATE SYNONYM 権限を持ち、S1 スキーマに対して SELECT 権限を持っているとします。 この場合、U1 ユーザーは拒否されたオブジェクト T1 のシノニムを S1 スキーマ内に作成し、そのシノニムを使用して、拒否されたオブジェクト T1 にアクセスできることになります。  
  
 U1 ユーザーは、データベースに対して CREATE VIEW 権限を持ち、S1 スキーマに対して SELECT 権限を持っているとします。 この場合、U1 ユーザーは拒否されたオブジェクト T1 にデータをクエリするビューを S1 スキーマ内に作成し、そのビューを使用して、拒否されたオブジェクト T1 にアクセスできることになります。
  
## <a name="permissions"></a>アクセス許可  
 権限の許可者 (または AS オプションで指定されたプリンシパル) は、GRANT OPTION によって与えられた権限を保持しているか、権限が暗黙的に与えられる上位の権限を保持している必要があります。  
  
 AS オプションを使用する場合は、次の追加要件があります。  
  
|AS *granting_principal*|必要な追加権限|  
|------------------------------|------------------------------------|  
|データベース ユーザー|ユーザーに対する IMPERSONATE 権限、db_securityadmin 固定データベース ロールのメンバーシップ、db_owner 固定データベース ロールのメンバーシップ、または sysadmin 固定サーバー ロールのメンバーシップ。|  
|Windows ログインにマップされているデータベース ユーザー|ユーザーに対する IMPERSONATE 権限、db_securityadmin 固定データベース ロールのメンバーシップ、db_owner 固定データベース ロールのメンバーシップ、または sysadmin 固定サーバー ロールのメンバーシップ。|  
|Windows グループにマップされているデータベース ユーザー|Windows グループのメンバーシップ、db_securityadmin 固定データベース ロールのメンバーシップ、db_owner 固定データベース ロールのメンバーシップ、または sysadmin 固定サーバー ロールのメンバーシップ。|  
|証明書にマップされているデータベース ユーザー|db_securityadmin 固定データベース ロールのメンバーシップ、db_owner 固定データベース ロールのメンバーシップ、または sysadmin 固定サーバー ロールのメンバーシップ。|  
|非対称キーにマップされているデータベース ユーザー|db_securityadmin 固定データベース ロールのメンバーシップ、db_owner 固定データベース ロールのメンバーシップ、または sysadmin 固定サーバー ロールのメンバーシップ。|  
|サーバー プリンシパルにマップされていないデータベース ユーザー|ユーザーに対する IMPERSONATE 権限、db_securityadmin 固定データベース ロールのメンバーシップ、db_owner 固定データベース ロールのメンバーシップ、または sysadmin 固定サーバー ロールのメンバーシップ。|  
|データベース ロール|ロールに対する ALTER 権限、db_securityadmin 固定データベース ロールのメンバーシップ、db_owner 固定データベース ロールのメンバーシップ、または sysadmin 固定サーバー ロールのメンバーシップ。|  
|アプリケーション ロール|ロールに対する ALTER 権限、db_securityadmin 固定データベース ロールのメンバーシップ、db_owner 固定データベース ロールのメンバーシップ、または sysadmin 固定サーバー ロールのメンバーシップ。|  
  
 オブジェクトの所有者は、所有するオブジェクトの権限を許可できます。 セキュリティ保護可能なリソースに対して CONTROL 権限があるプリンシパルは、そのリソースの権限を許可できます。  
  
 sysadmin 固定サーバー ロールのメンバーなど、CONTROL SERVER 権限が許可されているユーザーは、サーバー内のセキュリティ保護可能なリソースに対する権限を許可できます。 db_owner 固定データベース ロールのメンバーなど、データベースに対する CONTROL 権限が許可されているユーザーは、データベース内のセキュリティ保護可能なリソースに対する権限を許可できます。 スキーマに対する CONTROL 権限が許可されているユーザーは、スキーマ内のオブジェクトに対する権限を許可できます。  
  
## <a name="examples"></a>例  
  
### <a name="a-granting-insert-permission-on-schema-humanresources-to-guest"></a>A. スキーマ HumanResources に対する INSERT 権限を、guest に許可する  
  
```sql  
GRANT INSERT ON SCHEMA :: HumanResources TO guest;  
```  
  
### <a name="b-granting-select-permission-on-schema-person-to-database-user-wiljo"></a>B. スキーマ Person に対する SELECT 権限を、データベース ユーザー WilJo に許可する  
  
```sql  
GRANT SELECT ON SCHEMA :: Person TO WilJo WITH GRANT OPTION;  
```  
  
## <a name="see-also"></a>参照  
 [DENY (スキーマ権限の拒否) &#40;Transact-SQL&#41;](../../t-sql/statements/deny-schema-permissions-transact-sql.md)   
 [REVOKE (スキーマ権限の取り消し) &#40;Transact-SQL&#41;](../../t-sql/statements/revoke-schema-permissions-transact-sql.md)   
 [GRANT &#40;Transact-SQL&#41;](../../t-sql/statements/grant-transact-sql.md)   
 [アクセス許可 &#40;データベース エンジン&#41;](../../relational-databases/security/permissions-database-engine.md)   
 [プリンシパル &#40;データベース エンジン&#41;](../../relational-databases/security/authentication-access/principals-database-engine.md)   
 [CREATE CERTIFICATE &#40;Transact-SQL&#41;](../../t-sql/statements/create-certificate-transact-sql.md)   
 [CREATE ASYMMETRIC KEY &#40;Transact-SQL&#41;](../../t-sql/statements/create-asymmetric-key-transact-sql.md)   
 [CREATE APPLICATION ROLE &#40;Transact-SQL&#41;](../../t-sql/statements/create-application-role-transact-sql.md)   
 [暗号化階層](../../relational-databases/security/encryption/encryption-hierarchy.md)   
 [sys.fn_builtin_permissions &#40;Transact-SQL&#41;](../../relational-databases/system-functions/sys-fn-builtin-permissions-transact-sql.md)   
 [sys.fn_my_permissions &#40;Transact-SQL&#41;](../../relational-databases/system-functions/sys-fn-my-permissions-transact-sql.md)   
 [HAS_PERMS_BY_NAME &#40;Transact-SQL&#41;](../../t-sql/functions/has-perms-by-name-transact-sql.md)  
  
  
