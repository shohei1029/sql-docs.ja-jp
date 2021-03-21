---
description: GRANT (Transact-SQL)
title: GRANT (Transact-SQL)
ms.custom: ''
ms.date: 06/12/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- GRANT_TSQL
- GRANT
dev_langs:
- TSQL
helpviewer_keywords:
- granting permissions [SQL Server], GRANT statement
- schema-level securables [SQL Server]
- GRANT statement
- cross-database permissions
- GRANT statement, about GRANT statement
- server-level securables [SQL Server]
- database-level securables [SQL Server]
- permissions [SQL Server], granting
ms.assetid: a760c16a-4d2d-43f2-be81-ae9315f38185
author: VanMSFT
ms.author: vanto
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: efdce09afb69773f068735356c83b88d5f61508d
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104740082"
---
# <a name="grant-transact-sql"></a>GRANT (Transact-SQL)

[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  セキュリティ保護可能なリソースに対する権限をプリンシパルに許可します。  一般的な概念は、\<some object> に対する \<some permission> を \<some user, login, or group> に付与することです。 権限の概要については、「[権限 &#40;データベース エンジン&#41;](../../relational-databases/security/permissions-database-engine.md)」を参照してください。  
  
 ![記事リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "記事リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql
-- Syntax for SQL Server and Azure SQL Database  
  
-- Simplified syntax for GRANT  
GRANT { ALL [ PRIVILEGES ] }  
      | permission [ ( column [ ,...n ] ) ] [ ,...n ]  
      [ ON [ class :: ] securable ] TO principal [ ,...n ]   
      [ WITH GRANT OPTION ] [ AS principal ]  
```  
  
```syntaxsql
-- Syntax for Azure Synapse Analytics and Parallel Data Warehouse  
  
GRANT
    <permission> [ ,...n ]  
    [ ON [ <class_type> :: ] securable ]
    TO principal [ ,...n ]  
    [ WITH GRANT OPTION ]  
[;]  
  
<permission> ::=  
{ see the tables below }  
  
<class_type> ::=  
{  
      LOGIN  
    | DATABASE  
    | OBJECT  
    | ROLE  
    | SCHEMA  
    | USER  
}  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数

ALL  
このオプションは旧バージョンとの互換性のためだけに保持されており、非推奨とされます。 実際にはすべての権限が許可されるわけではありません。 ALL を指定すると、次のアクセス許可が許可されます。

  - セキュリティ保護可能なリソースがデータベースの場合、ALL は BACKUP DATABASE、BACKUP LOG、CREATE DATABASE、CREATE DEFAULT、CREATE FUNCTION、CREATE PROCEDURE、CREATE RULE、CREATE TABLE、および CREATE VIEW を意味します。  
  
  - セキュリティ保護可能なリソースがスカラー関数の場合、EXECUTE および REFERENCES。  
  
  - セキュリティ保護可能なリソースがテーブル値関数の場合、DELETE、INSERT、REFERENCES、SELECT、UPDATE。  
  
  - セキュリティ保護可能なリソースがストアド プロシージャの場合、EXECUTE。  
  
  - セキュリティ保護可能なリソースがテーブルの場合、ALL は DELETE、INSERT、REFERENCES、SELECT、および UPDATE を意味します。  
  
  - セキュリティ保護可能なリソースがビューの場合、ALL は DELETE、INSERT、REFERENCES、SELECT、および UPDATE を意味します。  

PRIVILEGES  
ISO 準拠のために用意されています。 ALL の動作は変更されません。  
  
*permission*  
権限の名前を指定します。 アクセス許可とセキュリティ保護可能なリソースの有効な組み合わせについては、後のトピックを参照してください。  
  
*column*  
権限を許可するテーブルの列の名前を指定します。 かっこ () で囲む必要があります。  
  
*class*  
権限を許可するセキュリティ保護可能なリソースのクラスを指定します。 スコープ修飾子 **::** が必要です。  
  
*securable*  
権限を許可するセキュリティ保護可能なリソースを指定します。  
  
TO *principal*  
プリンシパルの名前です。 セキュリティ保護可能なリソースに対する権限を許可できるプリンシパルは、そのリソースによって異なります。 有効な組み合わせについては、後のトピックを参照してください。  
  
GRANT OPTION  
権限が許可された被付与者が、この権限を他のプリンシパルにも許可できることを示します。  
  
AS *principal*  
AS <principal> 句は、権限の許可者として記録されるプリンシパルは、ステートメントを実行しているユーザー以外のプリンシパルでなければならないことを示すために使います。 たとえば、ユーザー Mary の principal_id は 12、ユーザー Raul の principal_id は 15 であるものとします。 Mary が `GRANT SELECT ON OBJECT::X TO Steven WITH GRANT OPTION AS Raul;` を実行します。ステートメントは実際にはユーザー 13 (Mary) によって実行されましたが、sys.database_permissions テーブルでは、grantor_prinicpal_id は 15 (Raul) であることが示されます。  

一般に、AS 句の使用は、権限チェーンを明示的に定義する必要がある場合を除き、推奨されません。 詳しくは、「[権限 (データベース エンジン)](../../relational-databases/security/permissions-database-engine.md)」の「**権限チェック アルゴリズムの概要**」セクションをご覧ください。

このステートメントで AS を使っても、別のユーザーを偽装できることは意味しません。

## <a name="remarks"></a>解説

GRANT ステートメントの完全な構文は複雑です。 前の構文ダイアグラムは、構造をわかりやすくするために簡略化されています。 セキュリティ保護可能なリソースに対するアクセス許可を許可するための完全な構文については、後の記事を参照してください。  

許可された権限を取り消す場合は REVOKE ステートメントを使用します。また、GRANT によってプリンシパルに特定の権限が許可されないようにするには DENY ステートメントを使用します。  

権限を許可すると、指定したセキュリティ保護可能なリソースに対する権限の DENY または REVOKE は削除されます。 対象のセキュリティ保護可能なリソースの上位スコープで同じ権限が拒否されている場合は、その DENY ステートメントが優先されますが、 上位スコープで許可されている権限を取り消そうとしても、その REVOKE ステートメントは優先されません。  

データベース レベルの権限は、指定されたデータベースのスコープ内で許可されます。 ユーザーが別のデータベースのオブジェクトに対する権限を必要とする場合、そのデータベースにユーザー アカウントを作成するか、または現在のデータベースへのアクセス権と同様に、そのデータベースへのアクセス権もユーザー アカウントに与えます。  

> [!CAUTION]  
> テーブル レベルの DENY は列レベルの GRANT ステートメントよりも優先されません。 この動作は権限の階層内で一貫していませんが、旧バージョンとの互換性のために保持されています。 将来のリリースでは削除される予定です。  

システム ストアド プロシージャ sp_helprotect では、データベース レベルのセキュリティ保護可能なリソースに対する権限をレポートします。  

## <a name="with-grant-option"></a>WITH GRANT OPTION

**GRANT** ...**WITH GRANT OPTION** は、権限を受け取るセキュリティ プリンシパルが、指定された権限を他のセキュリティ アカウントに付与する能力を与えられることを意味します。 権限を与えられるプリンシパルがロールまたは Windows グループである場合、グループまたはロールのメンバーではないユーザーにオブジェクト権限をさらに付与する必要があるときは、**AS** 句を使用する必要があります。 **GRANT** ステートメントを実行できるのはグループまたはロールではなくユーザーだけなので、グループまたはロールの特定のメンバーは、権限を付与するときに、**AS** 句を使用して、ロールまたはグループのメンバーシップを明示的に呼び出す必要があります。 次の例では、ロールまたは Windows グループに付与するときの **WITH GRANT OPTION** の使用方法を示します。  
  
```sql
-- Execute the following as a database owner  
GRANT EXECUTE ON TestProc TO TesterRole WITH GRANT OPTION;  
EXEC sp_addrolemember TesterRole, User1;  
-- Execute the following as User1  
-- The following fails because User1 does not have the permission as the User1  
GRANT EXECUTE ON TestMe TO User2;  
-- The following succeeds because User1 invokes the TesterRole membership  
GRANT EXECUTE ON TestMe TO User2 AS TesterRole;  
```

## <a name="chart-of-sql-server-permissions"></a>SQL Server 権限の一覧表

[!INCLUDE[ssDE](../../includes/ssde-md.md)] のすべてのアクセス許可を示した pdf 形式のポスター サイズの一覧表については、[https://aka.ms/sql-permissions-poster](https://aka.ms/sql-permissions-poster) を参照してください。  

## <a name="permissions"></a>アクセス許可

権限の許可者 (または AS オプションで指定されたプリンシパル) は、GRANT OPTION によって与えられた権限を保持しているか、権限が暗黙的に与えられる上位の権限を保持している必要があります。 AS オプションを使用する場合は、追加の要件を満たす必要があります。 詳細については、セキュリティ保護可能なリソース別の記事を参照してください。

オブジェクトの所有者は、所有するオブジェクトの権限を許可できます。 セキュリティ保護可能なリソースに対して CONTROL 権限があるプリンシパルは、そのリソースの権限を許可できます。

sysadmin 固定サーバー ロールのメンバーなど、CONTROL SERVER 権限が許可されているユーザーは、サーバー内のセキュリティ保護可能なリソースに対する権限を許可できます。 db_owner 固定データベース ロールのメンバーなど、データベースに対する CONTROL 権限が許可されているユーザーは、データベース内のセキュリティ保護可能なリソースに対する権限を許可できます。 スキーマに対する CONTROL 権限が許可されているユーザーは、スキーマ内のオブジェクトに対する権限を許可できます。

## <a name="examples"></a>例

次の表は、セキュリティ保護可能なリソースと、その構文について説明している記事の一覧です。  

| セキュリティ保護可能 | GRANT 構文|
| ---------| ------ |
|アプリケーション ロール|[GRANT (データベース プリンシパルの権限の許可) &#40;Transact-SQL&#41;](../../t-sql/statements/grant-database-principal-permissions-transact-sql.md)|  
|アセンブリ|[GRANT (アセンブリの権限の許可) &#40;Transact-SQL&#41;](../../t-sql/statements/grant-assembly-permissions-transact-sql.md)|  
|非対称キー|[GRANT (非対称キーの権限の許可) &#40;Transact-SQL&#41;](../../t-sql/statements/grant-asymmetric-key-permissions-transact-sql.md)|  
|可用性グループ|[GRANT (可用性グループの権限の許可) &#40;Transact-SQL&#41;](../../t-sql/statements/grant-availability-group-permissions-transact-sql.md)|  
|Certificate|[GRANT (証明書の権限の許可) &#40;Transact-SQL&#41;](../../t-sql/statements/grant-certificate-permissions-transact-sql.md)|  
|コントラクト|[GRANT (Service Broker の権限の許可) &#40;Transact-SQL&#41;](../../t-sql/statements/grant-service-broker-permissions-transact-sql.md)|  
|データベース|[GRANT (データベースの権限の許可) &#40;Transact-SQL&#41;](../../t-sql/statements/grant-database-permissions-transact-sql.md)|
|データベース スコープ資格情報|[GRANT (データベース スコープの資格情報の許可) (Transact-SQL)](../../t-sql/statements/grant-database-scoped-credential-transact-sql.md)|  
|エンドポイント|[GRANT (エンドポイントの権限の許可) &#40;Transact-SQL&#41;](../../t-sql/statements/grant-endpoint-permissions-transact-sql.md)|  
|フルテキスト カタログ|[GRANT (フルテキストの権限の許可) &#40;Transact-SQL&#41;](../../t-sql/statements/grant-full-text-permissions-transact-sql.md)|  
|フルテキスト ストップリスト|[GRANT (フルテキストの権限の許可) &#40;Transact-SQL&#41;](../../t-sql/statements/grant-full-text-permissions-transact-sql.md)|  
|機能|[GRANT (オブジェクトの権限の許可) &#40;Transact-SQL&#41;](../../t-sql/statements/grant-object-permissions-transact-sql.md)|  
|ログイン|[GRANT (サーバー プリンシパルの権限の許可) &#40;Transact-SQL&#41;](../../t-sql/statements/grant-server-principal-permissions-transact-sql.md)|  
|メッセージ型|[GRANT (Service Broker の権限の許可) &#40;Transact-SQL&#41;](../../t-sql/statements/grant-service-broker-permissions-transact-sql.md)|  
|Object|[GRANT (オブジェクトの権限の許可) &#40;Transact-SQL&#41;](../../t-sql/statements/grant-object-permissions-transact-sql.md)|  
|キュー|[GRANT (オブジェクトの権限の許可) &#40;Transact-SQL&#41;](../../t-sql/statements/grant-object-permissions-transact-sql.md)|  
|リモート サービス バインド|[GRANT (Service Broker の権限の許可) &#40;Transact-SQL&#41;](../../t-sql/statements/grant-service-broker-permissions-transact-sql.md)|  
|Role|[GRANT (データベース プリンシパルの権限の許可) &#40;Transact-SQL&#41;](../../t-sql/statements/grant-database-principal-permissions-transact-sql.md)|  
|ルート|[GRANT (Service Broker の権限の許可) &#40;Transact-SQL&#41;](../../t-sql/statements/grant-service-broker-permissions-transact-sql.md)|  
|スキーマ|[GRANT (スキーマ権限の許可) &#40;Transact-SQL&#41;](../../t-sql/statements/grant-schema-permissions-transact-sql.md)|  
|検索プロパティ リスト|[GRANT (検索プロパティ リスト権限の許可) &#40;Transact-SQL&#41;](../../t-sql/statements/grant-search-property-list-permissions-transact-sql.md)|  
|サーバー|[GRANT (サーバーの権限の許可) &#40;Transact-SQL&#41;](../../t-sql/statements/grant-server-permissions-transact-sql.md)|  
|サービス|[GRANT (Service Broker の権限の許可) &#40;Transact-SQL&#41;](../../t-sql/statements/grant-service-broker-permissions-transact-sql.md)|  
|ストアド プロシージャ|[GRANT (オブジェクトの権限の許可) &#40;Transact-SQL&#41;](../../t-sql/statements/grant-object-permissions-transact-sql.md)|  
|対称キー|[GRANT (対称キーの権限の許可) &#40;Transact-SQL&#41;](../../t-sql/statements/grant-symmetric-key-permissions-transact-sql.md)|  
|シノニム|[GRANT (オブジェクトの権限の許可) &#40;Transact-SQL&#41;](../../t-sql/statements/grant-object-permissions-transact-sql.md)|  
|システム オブジェクト|[GRANT (システム オブジェクトの権限の許可) &#40;Transact-SQL&#41;](../../t-sql/statements/grant-system-object-permissions-transact-sql.md)|  
|テーブル|[GRANT (オブジェクトの権限の許可) &#40;Transact-SQL&#41;](../../t-sql/statements/grant-object-permissions-transact-sql.md)|  
|Type|[GRANT (型の権限の許可) &#40;Transact-SQL&#41;](../../t-sql/statements/grant-type-permissions-transact-sql.md)|  
|User|[GRANT (データベース プリンシパルの権限の許可) &#40;Transact-SQL&#41;](../../t-sql/statements/grant-database-principal-permissions-transact-sql.md)|  
|表示|[GRANT (オブジェクトの権限の許可) &#40;Transact-SQL&#41;](../../t-sql/statements/grant-object-permissions-transact-sql.md)|  
|XML スキーマ コレクション|[GRANT (XML スキーマ コレクションの権限の許可) &#40;Transact-SQL&#41;](../../t-sql/statements/grant-xml-schema-collection-permissions-transact-sql.md)|  

## <a name="see-also"></a>参照

- [DENY &#40;Transact-SQL&#41;](../../t-sql/statements/deny-transact-sql.md)
- [REVOKE &#40;Transact-SQL&#41;](../../t-sql/statements/revoke-transact-sql.md)
- [sp_addlogin &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-addlogin-transact-sql.md)
- [sp_adduser &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-adduser-transact-sql.md)
- [sp_changedbowner &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-changedbowner-transact-sql.md)
- [sp_dropuser &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-dropuser-transact-sql.md)
- [sp_helprotect &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-helprotect-transact-sql.md)
- [sp_helpuser &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-helpuser-transact-sql.md)
