---
description: REVOKE (Transact-SQL)
title: REVOKE (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 07/26/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- REVOKE_TSQL
- REVOKE
dev_langs:
- TSQL
helpviewer_keywords:
- schema-level securables [SQL Server]
- REVOKE statement, Transact-SQL syntax
- removing permissions
- server-level securables [SQL Server]
- deleting permissions
- revoking permissions [SQL Server]
- REVOKE statement
- denying permissions [SQL Server], removing
- database-level securables [SQL Server]
- granting permissions [SQL Server], removing
- permissions [SQL Server], revoking
- dropping permissions
ms.assetid: 9d31d3e7-0883-45cd-bf0e-f0361bbb0956
author: VanMSFT
ms.author: vanto
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 40b950c34fa6cc506c0362e0fca8a531e5ce7423
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104740202"
---
# <a name="revoke-transact-sql"></a>REVOKE (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  以前に許可または拒否した権限を取り消します。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql
-- Syntax for SQL Server and Azure SQL Database  
  
-- Simplified syntax for REVOKE  
REVOKE [ GRANT OPTION FOR ]  
      {   
        [ ALL [ PRIVILEGES ] ]  
        |  
                permission [ ( column [ ,...n ] ) ] [ ,...n ]  
      }  
      [ ON [ class :: ] securable ]   
      { TO | FROM } principal [ ,...n ]   
      [ CASCADE] [ AS principal ]  
```  
  
```syntaxsql
-- Syntax for Azure Synapse Analytics and Parallel Data Warehouse  
  
REVOKE   
    <permission> [ ,...n ]  
    [ ON [ <class_type> :: ] securable ]   
    [ FROM | TO ] principal [ ,...n ]  
    [ CASCADE ]  
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
 GRANT OPTION FOR  
 指定した権限を与える許可を取り消すことを示します。 CASCADE 引数を使用する場合、これは必須です。  
  
> [!IMPORTANT]  
>  指定した権限が GRANT オプションなしでプリンシパルに許可されている場合は、その権限自体が取り消されます。  
  
 ALL  
**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降
  
 このオプションでは、可能な権限がすべて取り消されるわけではありません。 ALL を指定すると、次の権限が取り消されます。  
  
-   セキュリティ保護可能なリソースがデータベースの場合、ALL は BACKUP DATABASE、BACKUP LOG、CREATE DATABASE、CREATE DEFAULT、CREATE FUNCTION、CREATE PROCEDURE、CREATE RULE、CREATE TABLE、および CREATE VIEW を意味します。  
  
-   セキュリティ保護可能なリソースがスカラー関数の場合、EXECUTE および REFERENCES。  
  
-   セキュリティ保護可能なリソースがテーブル値関数の場合、DELETE、INSERT、REFERENCES、SELECT、UPDATE。  
  
-   セキュリティ保護可能なリソースがストアド プロシージャの場合、EXECUTE。  
  
-   セキュリティ保護可能なリソースがテーブルの場合、ALL は DELETE、INSERT、REFERENCES、SELECT、および UPDATE を意味します。  
  
-   セキュリティ保護可能なリソースがビューの場合、ALL は DELETE、INSERT、REFERENCES、SELECT、および UPDATE を意味します。  
  
> [!NOTE]  
>  REVOKE ALL 構文は非推奨です。 [!INCLUDE[ssNoteDepFutureAvoid](../../includes/ssnotedepfutureavoid-md.md)]代わりに特定の権限を取り消してください。  
  
 PRIVILEGES  
 ISO 準拠のために用意されています。 ALL の動作は変更されません。  
  
 *permission*  
 権限の名前を指定します。 セキュリティ保護可能なリソースと権限の有効な組み合わせについては、後の「[セキュリティ保護可能なリソース別の構文](#securable)」の一覧を参照してください。  
  
 *column*  
 権限を取り消すテーブルの列の名前を指定します。 かっこが必要です。  
  
 *class*  
 権限を取り消すセキュリティ保護可能なリソースのクラスを指定します。 スコープ修飾子 **::** が必要です。  
  
 *securable*  
 権限を取り消すセキュリティ保護可能なリソースを指定します。  
  
 TO | FROM *principal*  
 プリンシパルの名前です。 セキュリティ保護可能なリソースに対する権限を取り消すことのできるプリンシパルは、そのリソースによって異なります。 有効な組み合わせの詳細については、後の「[セキュリティ保護可能なリソース別の構文](#securable)」の一覧を参照してください。  
  
 CASCADE  
 このプリンシパルによって権限が許可されている他のプリンシパルからも、同じ権限が取り消されることを示します。 CASCADE 引数を使用する場合は、GRANT OPTION FOR 引数も指定する必要があります。  
  
> [!CAUTION]  
>  WITH GRANT OPTION で許可されている権限を CASCADE で取り消すと、その権限の GRANT および DENY の両方が取り消されます。  
  
 AS *principal*  
 自分以外のプリンシパルによって付与された権限を取り消すことを示すには、AS <principal> 句を使用します。 たとえば、ユーザー Mary の principal_id が 12、ユーザー Raul の principal_id が 15 であるとします。 Mary と Raul はどちらも、Steven という名前のユーザーに同じ権限を付与します。 sys.database_permissions テーブルでは、同じ権限が 2 回表示されますが、grantor_prinicpal_id の値は異なります。 Mary は、`AS RAUL` 句を使って、Raul の権限付与を取り消すことができます。
 
このステートメントで AS を使っても、別のユーザーを偽装できることは意味しません。  
  
## <a name="remarks"></a>解説  
 REVOKE ステートメントの完全な構文は複雑です。 前の構文ダイアグラムは、構造をわかりやすくするために簡略化されています。 特定のセキュリティ保護可能なリソースに対する権限を取り消すための完全な構文については、後の「[セキュリティ保護可能なリソース別の構文](#securable)」の一覧を参照してください。  
  
 許可された権限を取り消す場合は REVOKE ステートメントを使用します。また、GRANT によってプリンシパルに特定の権限が許可されないようにするには DENY ステートメントを使用します。  
  
 権限を許可すると、指定したセキュリティ保護可能なリソースに対する権限の DENY または REVOKE は削除されます。 対象のセキュリティ保護可能なリソースの上位スコープで同じ権限が拒否されている場合は、その DENY ステートメントが優先されますが、 ただし、上位スコープで許可されている権限を取り消そうとしても、その REVOKE ステートメントは優先されません。  
  
> [!CAUTION]  
>  テーブル レベルの DENY は列レベルの GRANT ステートメントよりも優先されません。 この動作は権限の階層内で一貫していませんが、旧バージョンとの互換性のために保持されています。 将来のリリースでは削除される予定です。  
  
 システム ストアド プロシージャ sp_helprotect では、データベース レベルのセキュリティ保護可能なリソースに対する権限がレポートされます  
  
 GRANT OPTION で権限が許可されたプリンシパルから権限を取り消すときに CASCADE を指定しない場合、REVOKE ステートメントは失敗します。  
  
## <a name="permissions"></a>アクセス許可  
 セキュリティ保護可能なリソースに対して CONTROL 権限があるプリンシパルは、そのセキュリティ保護可能なリソースの権限を取り消すことができます。 オブジェクトの所有者は、所有するオブジェクトに対する権限を取り消すことができます。  
  
 sysadmin 固定サーバー ロールのメンバーなど、CONTROL SERVER 権限が許可されているユーザーは、サーバー内のセキュリティ保護可能なリソースに対する権限を取り消すことができます。 db_owner 固定データベース ロールのメンバーなど、データベースに対する CONTROL 権限が許可されているユーザーは、データベース内のセキュリティ保護可能なリソースに対する権限を取り消すことができます。 スキーマに対する CONTROL 権限が許可されているユーザーは、スキーマ内のオブジェクトに対する権限を取り消すことができます。  
  
##  <a name="securable-specific-syntax"></a><a name="securable"></a> セキュリティ保護可能なリソース別の構文  
 次の表は、セキュリティ保護可能なリソースと、セキュリティ保護可能なリソース固有の構文について説明しているトピックの一覧です。  
  
|セキュリティ保護可能|トピック|  
|---------------|-----------|  
|アプリケーション ロール|[REVOKE (データベース プリンシパルの権限の取り消し) &#40;Transact-SQL&#41;](../../t-sql/statements/revoke-database-principal-permissions-transact-sql.md)|  
|アセンブリ|[REVOKE (アセンブリの権限の取り消し) &#40;Transact-SQL&#41;](../../t-sql/statements/revoke-assembly-permissions-transact-sql.md)|  
|非対称キー|[REVOKE (非対称キーの権限の取り消し) &#40;Transact-SQL&#41;](../../t-sql/statements/revoke-asymmetric-key-permissions-transact-sql.md)|  
|可用性グループ|[REVOKE (可用性グループの権限の取り消し) &#40;Transact-SQL&#41;](../../t-sql/statements/revoke-availability-group-permissions-transact-sql.md)|  
|Certificate|[REVOKE (証明書の権限の取り消し) &#40;Transact-SQL&#41;](../../t-sql/statements/revoke-certificate-permissions-transact-sql.md)|  
|コントラクト|[REVOKE (Service Broker の権限の拒否) &#40;Transact-SQL&#41;](../../t-sql/statements/revoke-service-broker-permissions-transact-sql.md)|  
|データベース|[REVOKE (データベースの権限の取り消し) &#40;Transact-SQL&#41;](../../t-sql/statements/revoke-database-permissions-transact-sql.md)|  
|エンドポイント|[REVOKE (エンドポイントの権限の取り消し) &#40;Transact-SQL&#41;](../../t-sql/statements/revoke-endpoint-permissions-transact-sql.md)|  
|データベース スコープ資格情報|[REVOKE (データベース スコープの資格情報の取り消し) (Transact-SQL)](../../t-sql/statements/revoke-database-scoped-credential-transact-sql.md)|  
|フルテキスト カタログ|[REVOKE (フルテキストの権限の取り消し) &#40;Transact-SQL&#41;](../../t-sql/statements/revoke-full-text-permissions-transact-sql.md)|  
|フルテキスト ストップリスト|[REVOKE (フルテキストの権限の取り消し) &#40;Transact-SQL&#41;](../../t-sql/statements/revoke-full-text-permissions-transact-sql.md)|  
|関数|[REVOKE (オブジェクトの権限の取り消し) &#40;Transact-SQL&#41;](../../t-sql/statements/revoke-object-permissions-transact-sql.md)|  
|ログイン|[REVOKE (サーバー プリンシパルの権限の取り消し) &#40;Transact-SQL&#41;](../../t-sql/statements/revoke-server-principal-permissions-transact-sql.md)|  
|メッセージ型|[REVOKE (Service Broker の権限の拒否) &#40;Transact-SQL&#41;](../../t-sql/statements/revoke-service-broker-permissions-transact-sql.md)|  
|Object|[REVOKE (オブジェクトの権限の取り消し) &#40;Transact-SQL&#41;](../../t-sql/statements/revoke-object-permissions-transact-sql.md)|  
|キュー|[REVOKE (オブジェクトの権限の取り消し) &#40;Transact-SQL&#41;](../../t-sql/statements/revoke-object-permissions-transact-sql.md)|  
|リモート サービス バインド|[REVOKE (Service Broker の権限の拒否) &#40;Transact-SQL&#41;](../../t-sql/statements/revoke-service-broker-permissions-transact-sql.md)|  
|ロール|[REVOKE (データベース プリンシパルの権限の取り消し) &#40;Transact-SQL&#41;](../../t-sql/statements/revoke-database-principal-permissions-transact-sql.md)|  
|ルート|[REVOKE (Service Broker の権限の拒否) &#40;Transact-SQL&#41;](../../t-sql/statements/revoke-service-broker-permissions-transact-sql.md)|  
|スキーマ|[REVOKE (スキーマ権限の取り消し) &#40;Transact-SQL&#41;](../../t-sql/statements/revoke-schema-permissions-transact-sql.md)|  
|検索プロパティ リスト|[REVOKE (検索プロパティ リスト権限の取り消し) &#40;Transact-SQL&#41;](../../t-sql/statements/revoke-search-property-list-permissions-transact-sql.md)|  
|サーバー|[REVOKE (サーバーの権限の取り消し) &#40;Transact-SQL&#41;](../../t-sql/statements/revoke-server-permissions-transact-sql.md)|  
|サービス|[REVOKE (Service Broker の権限の拒否) &#40;Transact-SQL&#41;](../../t-sql/statements/revoke-service-broker-permissions-transact-sql.md)|  
|ストアド プロシージャ|[REVOKE (オブジェクトの権限の取り消し) &#40;Transact-SQL&#41;](../../t-sql/statements/revoke-object-permissions-transact-sql.md)|  
|対称キー|[REVOKE (対称キーの権限の取り消し) &#40;Transact-SQL&#41;](../../t-sql/statements/revoke-symmetric-key-permissions-transact-sql.md)|  
|シノニム|[REVOKE (オブジェクトの権限の取り消し) &#40;Transact-SQL&#41;](../../t-sql/statements/revoke-object-permissions-transact-sql.md)|  
|システム オブジェクト|[REVOKE (システム オブジェクトの権限の取り消し) &#40;Transact-SQL&#41;](../../t-sql/statements/revoke-system-object-permissions-transact-sql.md)|  
|Table|[REVOKE (オブジェクトの権限の取り消し) &#40;Transact-SQL&#41;](../../t-sql/statements/revoke-object-permissions-transact-sql.md)|  
|種類|[REVOKE (型の権限の取り消し) &#40;Transact-SQL&#41;](../../t-sql/statements/revoke-type-permissions-transact-sql.md)|  
|ユーザー|[REVOKE (データベース プリンシパルの権限の取り消し) &#40;Transact-SQL&#41;](../../t-sql/statements/revoke-database-principal-permissions-transact-sql.md)|  
|View|[REVOKE (オブジェクトの権限の取り消し) &#40;Transact-SQL&#41;](../../t-sql/statements/revoke-object-permissions-transact-sql.md)|  
|XML スキーマ コレクション|[REVOKE (XML スキーマ コレクションの権限の取り消し) &#40;Transact-SQL&#41;](../../t-sql/statements/revoke-xml-schema-collection-permissions-transact-sql.md)|  
  
## <a name="see-also"></a>参照  
 [権限の階層 &#40;データベース エンジン&#41;](../../relational-databases/security/permissions-hierarchy-database-engine.md)   
 [DENY &#40;Transact-SQL&#41;](../../t-sql/statements/deny-transact-sql.md)   
 [GRANT &#40;Transact-SQL&#41;](../../t-sql/statements/grant-transact-sql.md)   
 [sp_addlogin &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-addlogin-transact-sql.md)   
 [sp_adduser &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-adduser-transact-sql.md)   
 [sp_changedbowner &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-changedbowner-transact-sql.md)   
 [sp_dropuser &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-dropuser-transact-sql.md)   
 [sp_helprotect &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-helprotect-transact-sql.md)   
 [sp_helpuser &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-helpuser-transact-sql.md)  
  
  
