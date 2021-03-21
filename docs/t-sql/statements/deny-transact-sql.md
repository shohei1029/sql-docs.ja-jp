---
description: DENY (Transact-SQL)
title: DENY (Transact-SQL)
ms.custom: ''
ms.date: 05/15/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- DENY
- DENY_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- schema-level securables [SQL Server]
- security [SQL Server], denying access
- DENY statement
- permissions [SQL Server], denying
- server-level securables [SQL Server]
- inherited security settings [SQL Server]
- denying permissions [SQL Server], DENY statement
- principal security [SQL Server]
- database-level securables [SQL Server]
- denying permissions [SQL Server]
ms.assetid: c32d1e01-9ee9-4665-a516-fcfece58078e
author: VanMSFT
ms.author: vanto
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: ce34d8c21230b03161b0552a7e0a569c3b03a469
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104754492"
---
# <a name="deny-transact-sql"></a>DENY (Transact-SQL)

[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  プリンシパルに対する権限を拒否します。 プリンシパルが、そのグループまたはロールのメンバーシップから権限を継承しないようにします。 DENY は、オブジェクトの所有者または sysadmin 固定サーバー ロールのメンバーに適用されない点を除いて、すべての権限より優先されます。
  **セキュリティに関する注意** sysadmin 固定サーバー ロールのメンバーとオブジェクトの所有者の権限を拒否することはできません。
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql
-- Syntax for SQL Server and Azure SQL Database  
  
-- Simplified syntax for DENY  
DENY   { ALL [ PRIVILEGES ] } 
     | <permission>  [ ( column [ ,...n ] ) ] [ ,...n ]  
    [ ON [ <class> :: ] securable ] 
    TO principal [ ,...n ]   
    [ CASCADE] [ AS principal ]  
[;]

<permission> ::=  
{ see the tables below }  
  
<class> ::=  
{ see the tables below }  
```  
  
```syntaxsql
-- Syntax for Azure Synapse Analytics and Parallel Data Warehouse  
  
DENY   
    <permission> [ ,...n ]  
    [ ON [ <class_> :: ] securable ]   
    TO principal [ ,...n ]  
    [ CASCADE ]  
[;]  
  
<permission> ::=  
{ see the tables below }  
  
<class> ::=  
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
 このオプションでは、可能な権限がすべて拒否されるわけではありません。 ALL を指定すると、次の権限が拒否されます。  
  
-   セキュリティ保護可能なリソースがデータベースの場合、ALL は BACKUP DATABASE、BACKUP LOG、CREATE DATABASE、CREATE DEFAULT、CREATE FUNCTION、CREATE PROCEDURE、CREATE RULE、CREATE TABLE、および CREATE VIEW を意味します。  
  
-   セキュリティ保護可能なリソースがスカラー関数の場合、EXECUTE および REFERENCES。  
  
-   セキュリティ保護可能なリソースがテーブル値関数の場合、DELETE、INSERT、REFERENCES、SELECT、UPDATE。  
  
-   セキュリティ保護可能なリソースがストアド プロシージャの場合、EXECUTE。  
  
-   セキュリティ保護可能なリソースがテーブルの場合、ALL は DELETE、INSERT、REFERENCES、SELECT、および UPDATE を意味します。  
  
-   セキュリティ保護可能なリソースがビューの場合、ALL は DELETE、INSERT、REFERENCES、SELECT、および UPDATE を意味します。  
  
> [!NOTE]  
>  DENY ALL 構文は非推奨です。 [!INCLUDE[ssNoteDepFutureAvoid](../../includes/ssnotedepfutureavoid-md.md)]代わりに特定の権限を拒否してください。  
  
 PRIVILEGES  
 ISO 準拠のために用意されています。 ALL の動作は変更されません。  
  
 *permission*  
 権限の名前を指定します。 権限とセキュリティ保護可能なリソースの有効な組み合わせについては、後のトピックを参照してください。  
  
 *column*  
 権限を拒否するテーブルの列の名前を指定します。 かっこ () で囲む必要があります。  
  
 *class*  
 権限を拒否するセキュリティ保護可能なリソースのクラスを指定します。 スコープ修飾子 **::** が必要です。  
  
 *securable*  
 権限を拒否するセキュリティ保護可能なリソースを指定します。  
  
 TO *principal*  
 プリンシパルの名前です。 セキュリティ保護可能なリソースに対する権限を拒否できるプリンシパルは、そのリソースによって異なります。 有効な組み合わせについては、後のセキュリティ保護可能なリソースの各トピックを参照してください。  
  
 CASCADE  
 指定したプリンシパル、およびこのプリンシパルによって権限が許可されている他のすべてのプリンシパルに対しても、同じ権限を拒否することを示します。 GRANT OPTION を使用してプリンシパルに権限が与えられている場合に必要となります。  
  
 AS *principal*  
 このクエリを実行するプリンシパルが権限を拒否する権利を取得した、元のプリンシパルを指定します。
AS <principal> 句は、権限の拒否者として記録されるプリンシパルは、ステートメントを実行しているユーザー以外のプリンシパルでなければならないことを示すために使います。 たとえば、ユーザー Mary の principal_id は 12、ユーザー Raul の principal_id は 15 であるものとします。 Mary が `DENY SELECT ON OBJECT::X TO Steven WITH GRANT OPTION AS Raul;` を実行します。ステートメントは実際にはユーザー 13 (Mary) によって実行されましたが、sys.database_permissions テーブルでは、DENY ステートメントの grantor_prinicpal_id は 15 (Raul) であることが示されます。
  
このステートメントで AS を使っても、別のユーザーを偽装できることは意味しません。  
  
## <a name="remarks"></a>解説  
 DENY ステートメントの完全な構文は複雑です。 前の構文ダイアグラムは、構造をわかりやすくするために簡略化されています。 セキュリティ保護可能なリソースに対する権限を拒否するための完全な構文については、後のトピックを参照してください。  
  
 GRANT OPTION で権限が付与されたプリンシパルに対して、その権限を拒否するときに CASCADE を指定しないと、DENY は失敗します。  
  
 システム ストアド プロシージャ sp_helprotect では、データベース レベルのセキュリティ保護可能なリソースに対する権限をレポートします。  
  
> [!CAUTION]  
>  テーブル レベルの DENY は列レベルの GRANT ステートメントよりも優先されません。 この動作は権限の階層内で一貫していませんが、旧バージョンとの互換性のために保持されています。 将来のリリースでは削除される予定です。  
  
> [!CAUTION]  
>  データベースに対する CONTROL 権限を拒否すると、そのデータベースに対する CONNECT 権限が暗黙的に拒否されます。 データベースに対する CONTROL 権限を拒否されたプリンシパルは、そのデータベースに接続できません。  
  
> [!CAUTION]  
>  CONTROL SERVER 権限を拒否すると、そのサーバーに対する CONNECT SQL 権限が暗黙的に拒否されます。 サーバーに対する CONTROL SERVER 権限が拒否されたプリンシパルは、そのサーバーに接続できません。  
  
## <a name="permissions"></a>アクセス許可  
 呼び出し元 (または AS オプションで指定されたプリンシパル) は、セキュリティ保護可能なリソースに対する CONTROL 権限、またはセキュリティ保護可能なリソースに対する CONTROL 権限を暗黙的に与える上位の権限を保持している必要があります。 AS オプションを使用する場合、指定されたプリンシパルは、権限が拒否されるセキュリティ保護可能なリソースを所有している必要があります。  
  
 固定サーバー ロール sysadmin のメンバーなど、CONTROL SERVER 権限が許可されているユーザーは、サーバー内のセキュリティ保護可能なリソースに対する権限を拒否できます。 db_owner 固定データベース ロールのメンバーなど、データベースに対する CONTROL 権限が許可されているユーザーは、データベース内のセキュリティ保護可能なリソースに対する権限を拒否できます。 スキーマに対する CONTROL 権限が許可されているユーザーは、スキーマ内のオブジェクトに対する権限を拒否できます。 AS 句を使用する場合、指定されるプリンシパルは、権限の拒否対象となるセキュリティ保護可能なリソースを所有している必要があります。  
  
## <a name="examples"></a>例

 次の表は、セキュリティ保護可能なリソースと、セキュリティ保護可能なリソース固有の構文について説明しているトピックの一覧です。  
  
|[セキュリティ保護可能なリソース]|構文|
|----------|------|
|アプリケーション ロール|[DENY (データベース プリンシパルの権限の拒否) &#40;Transact-SQL&#41;](../../t-sql/statements/deny-database-principal-permissions-transact-sql.md)|  
|アセンブリ|[DENY (アセンブリの権限の拒否) &#40;Transact-SQL&#41;](../../t-sql/statements/deny-assembly-permissions-transact-sql.md)|  
|非対称キー|[DENY (非対称キーの権限の拒否) &#40;Transact-SQL&#41;](../../t-sql/statements/deny-asymmetric-key-permissions-transact-sql.md)|  
|可用性グループ|[DENY (可用性グループの権限の拒否) &#40;Transact-SQL&#41;](../../t-sql/statements/deny-availability-group-permissions-transact-sql.md)|  
|Certificate|[DENY (証明書の権限の拒否) &#40;Transact-SQL&#41;](../../t-sql/statements/deny-certificate-permissions-transact-sql.md)|  
|コントラクト|[DENY (Service Broker の権限の拒否) &#40;Transact-SQL&#41;](../../t-sql/statements/deny-service-broker-permissions-transact-sql.md)|  
|データベース|[DENY (データベースの権限の拒否) &#40;Transact-SQL&#41;](../../t-sql/statements/deny-database-permissions-transact-sql.md)|  
|データベース スコープ資格情報|[DENY (データベース スコープの資格情報の拒否) (Transact-SQL)](../../t-sql/statements/deny-database-scoped-credential-transact-sql.md)|  
|エンドポイント|[DENY (エンドポイントの権限の拒否) &#40;Transact-SQL&#41;](../../t-sql/statements/deny-endpoint-permissions-transact-sql.md)|  
|フルテキスト カタログ|[DENY (フルテキストの権限の拒否) &#40;Transact-SQL&#41;](../../t-sql/statements/deny-full-text-permissions-transact-sql.md)|  
|フルテキスト ストップリスト|[DENY (フルテキストの権限の拒否) &#40;Transact-SQL&#41;](../../t-sql/statements/deny-full-text-permissions-transact-sql.md)|  
|機能|[DENY (オブジェクトの権限の拒否) &#40;Transact-SQL&#41;](../../t-sql/statements/deny-object-permissions-transact-sql.md)|  
|ログイン|[DENY (サーバー プリンシパルの権限の拒否) &#40;Transact-SQL&#41;](../../t-sql/statements/deny-server-principal-permissions-transact-sql.md)|  
|メッセージ型|[DENY (Service Broker の権限の拒否) &#40;Transact-SQL&#41;](../../t-sql/statements/deny-service-broker-permissions-transact-sql.md)|  
|Object|[DENY (オブジェクトの権限の拒否) &#40;Transact-SQL&#41;](../../t-sql/statements/deny-object-permissions-transact-sql.md)|  
|キュー|[DENY (オブジェクトの権限の拒否) &#40;Transact-SQL&#41;](../../t-sql/statements/deny-object-permissions-transact-sql.md)|  
|リモート サービス バインド|[DENY (Service Broker の権限の拒否) &#40;Transact-SQL&#41;](../../t-sql/statements/deny-service-broker-permissions-transact-sql.md)|  
|Role|[DENY (データベース プリンシパルの権限の拒否) &#40;Transact-SQL&#41;](../../t-sql/statements/deny-database-principal-permissions-transact-sql.md)|  
|ルート|[DENY (Service Broker の権限の拒否) &#40;Transact-SQL&#41;](../../t-sql/statements/deny-service-broker-permissions-transact-sql.md)|  
|スキーマ|[DENY (スキーマ権限の拒否) &#40;Transact-SQL&#41;](../../t-sql/statements/deny-schema-permissions-transact-sql.md)|  
|検索プロパティ リスト|[DENY (検索プロパティ リスト権限の拒否) &#40;Transact-SQL&#41;](../../t-sql/statements/deny-search-property-list-permissions-transact-sql.md)|  
|サーバー|[DENY (サーバーの権限の拒否) &#40;Transact-SQL&#41;](../../t-sql/statements/deny-server-permissions-transact-sql.md)|  
|サービス|[DENY (Service Broker の権限の拒否) &#40;Transact-SQL&#41;](../../t-sql/statements/deny-service-broker-permissions-transact-sql.md)|  
|ストアド プロシージャ|[DENY (オブジェクトの権限の拒否) &#40;Transact-SQL&#41;](../../t-sql/statements/deny-object-permissions-transact-sql.md)|  
|対称キー|[DENY (対称キーの権限の拒否) &#40;Transact-SQL&#41;](../../t-sql/statements/deny-symmetric-key-permissions-transact-sql.md)|  
|シノニム|[DENY (オブジェクトの権限の拒否) &#40;Transact-SQL&#41;](../../t-sql/statements/deny-object-permissions-transact-sql.md)|  
|システム オブジェクト|[DENY (システム オブジェクトの権限の拒否) &#40;Transact-SQL&#41;](../../t-sql/statements/deny-system-object-permissions-transact-sql.md)|  
|テーブル|[DENY (オブジェクトの権限の拒否) &#40;Transact-SQL&#41;](../../t-sql/statements/deny-object-permissions-transact-sql.md)|  
|Type|[DENY (型の権限の拒否) &#40;Transact-SQL&#41;](../../t-sql/statements/deny-type-permissions-transact-sql.md)|  
|User|[DENY (データベース プリンシパルの権限の拒否) &#40;Transact-SQL&#41;](../../t-sql/statements/deny-database-principal-permissions-transact-sql.md)|  
|表示|[DENY (オブジェクトの権限の拒否) &#40;Transact-SQL&#41;](../../t-sql/statements/deny-object-permissions-transact-sql.md)|  
|XML スキーマ コレクション|[DENY (XML スキーマ コレクションの権限の拒否) &#40;Transact-SQL&#41;](../../t-sql/statements/deny-xml-schema-collection-permissions-transact-sql.md)|  
  
## <a name="see-also"></a>参照  
 [REVOKE &#40;Transact-SQL&#41;](../../t-sql/statements/revoke-transact-sql.md)   
 [sp_addlogin &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-addlogin-transact-sql.md)   
 [sp_adduser &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-adduser-transact-sql.md)   
 [sp_changedbowner &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-changedbowner-transact-sql.md)   
 [sp_dropuser &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-dropuser-transact-sql.md)   
 [sp_helprotect &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-helprotect-transact-sql.md)   
 [sp_helpuser &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-helpuser-transact-sql.md)  
  
  
