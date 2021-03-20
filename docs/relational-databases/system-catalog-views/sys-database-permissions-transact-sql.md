---
description: sys.database_permissions (Transact-sql)
title: sys.database_permissions (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 08/11/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- database_permissions
- sys.database_permissions_TSQL
- database_permissions_TSQL
- sys.database_permissions
dev_langs:
- TSQL
helpviewer_keywords:
- sys.database_permissions catalog view
ms.assetid: c1e261f8-6cb0-4759-b5f1-5ec233602655
author: VanMSFT
ms.author: vanto
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 4896a671fd9dd0cf28829b57a1ec7a4f85ad2a1f
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104750062"
---
# <a name="sysdatabase_permissions-transact-sql"></a>sys.database_permissions (Transact-sql)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  データベースに含まれている、権限ごとまたは列例外の権限ごとに 1 行のデータを返します。 列権限が、対応するオブジェクトレベルの権限とは異なる場合、列権限ごとに行が存在します。 列権限が対応するオブジェクト権限と同じ場合、その行は存在せず、適用されている権限はオブジェクトの権限です。  
  
> [!IMPORTANT]  
>  列レベルの権限は、同じエンティティに対するオブジェクトレベルの権限をオーバーライドします。  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|**class**|**tinyint**|権限が存在するクラスを識別します。<br /><br /> 0 = データベース<br />1 = オブジェクトまたは列<br />3 = スキーマ<br />4 = データベースプリンシパル<br />5 = アセンブリ-以降 **に適用さ** [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] れます。<br />6 = 型<br />10 = XML スキーマコレクション- <br />                      **適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。<br />15 = メッセージの種類: 以降 **に適用さ** [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] れます。<br />16 = サービスコントラクト-以降 **に適用さ** [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] れます。<br />17 = サービス-以降 **に適用さ** [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] れます。<br />18 = リモートサービスバインド-以降 **に適用さ** [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] れます。<br />19 = ルート: 以降 **に適用さ** [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] れます。<br />23 = フルテキストカタログ: 以降 **に適用さ** [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] れます。<br />24 = 対称キー-以降 **に適用さ** [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] れます。<br />25 = 証明書-以降 **に適用さ** [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] れます。<br />26 = 非対称キー-以降 **に適用さ** [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] れます。|  
|**class_desc**|**nvarchar(60)**|権限が存在するクラスの説明です。<br /><br /> DATABASE<br /><br /> OBJECT_OR_COLUMN<br /><br /> SCHEMA<br /><br /> DATABASE_PRINCIPAL<br /><br /> ASSEMBLY<br /><br /> TYPE<br /><br /> XML_SCHEMA_COLLECTION<br /><br /> MESSAGE_TYPE<br /><br /> SERVICE_CONTRACT<br /><br /> SERVICE<br /><br /> REMOTE_SERVICE_BINDING<br /><br /> ROUTE<br /><br /> FULLTEXT_CATALOG<br /><br /> SYMMETRIC_KEYS<br /><br /> CERTIFICATE<br /><br /> ASYMMETRIC_KEY|  
|**major_id**|**int**|権限が存在する対象の ID。クラスに従って解釈されます。 通常、 **major_id** は、クラスが表すものに適用される id の種類にすぎません。 <br /><br /> 0 = データベース自体 <br /><br /> >0 = ユーザーオブジェクトの Object-IDs <br /><br /> \<0 = システムオブジェクトの Object-IDs |  
|**minor_id**|**int**|権限が存在することを示すセカンダリ ID。クラスに従って解釈されます。 多くの場合、オブジェクトのクラスに使用できるサブカテゴリがないため、 **minor_id** は0になります。 それ以外の場合は、テーブルの列 ID です。|  
|**grantee_principal_id**|**int**|権限が許可されるデータベースプリンシパル ID。|  
|**grantor_principal_id**|**int**|これらのアクセス許可の権限の許可のあるデータベースプリンシパル ID。|  
|**type**|**char (4)**|データベース権限の種類。 権限の種類の一覧については、次の表を参照してください。|  
|**permission_name**|**nvarchar(128)**|アクセス許可の名前。|  
|**状態**|**char(1)**|アクセス許可の状態:<br /><br /> D = 拒否<br /><br /> R = 取り消し<br /><br /> G = 許可<br /><br /> W = 許可の許可オプション|  
|**state_desc**|**nvarchar(60)**|権限の状態の説明。<br /><br /> DENY<br /><br /> REVOKE<br /><br /> GRANT<br /><br /> GRANT_WITH_GRANT_OPTION|  

## <a name="database-permissions"></a>データベース権限   
次の種類のアクセス許可が可能です。
  
|アクセス許可の種類|アクセス許可名|適用されるセキュリティ保護可能なリソース|  
|---------------------|---------------------|--------------------------|  
|AADS |ALTER ANY DATABASE EVENT SESSION |DATABASE |  
|AAMK |任意のマスクを変更します。 |DATABASE |  
|AEDS |すべての外部データ ソースを変更します。 |DATABASE |  
|AEFF |任意の外部のファイル形式を変更します。 |DATABASE |  
|AL|ALTER|アプリケーションロール、アセンブリ、非対称キー、証明書、コントラクト、データベース、フルテキストカタログ、メッセージの種類、オブジェクト、リモートサービスバインド、ロール、ルート、スキーマ、サービス、対称キー、ユーザー、XML スキーマコレクション|  
|ALAK|ALTER ANY ASYMMETRIC KEY|DATABASE|  
|ALAR|ALTER ANY APPLICATION ROLE|DATABASE|  
|ALAS|ALTER ANY ASSEMBLY|DATABASE|  
|ALCF|ALTER ANY CERTIFICATE|DATABASE|  
|ALDS|ALTER ANY DATASPACE|DATABASE|  
|ALED|ALTER ANY DATABASE EVENT NOTIFICATION|DATABASE|  
|ALFT|ALTER ANY FULLTEXT CATALOG|DATABASE|  
|ALMT|ALTER ANY MESSAGE TYPE|DATABASE|  
|ALRL|ALTER ANY ROLE|DATABASE|  
|ALRT|ALTER ANY ROUTE|DATABASE|  
|ALSB|ALTER ANY REMOTE SERVICE BINDING|DATABASE|  
|ALSC|ALTER ANY CONTRACT|DATABASE|  
|ALSK|ALTER ANY SYMMETRIC KEY|DATABASE|  
|ALSM|ALTER ANY SCHEMA|DATABASE|  
|ALSV|ALTER ANY SERVICE|DATABASE|  
|ALTG|ALTER ANY DATABASE DDL TRIGGER|DATABASE|  
|ALUS|ALTER ANY USER|DATABASE|  
|AUTH|AUTHENTICATE|DATABASE|  
|BADB|BACKUP DATABASE|DATABASE|  
|BALO|BACKUP LOG|DATABASE|  
|CL|CONTROL|APPLICATION ROLE、ASSEMBLY、ASYMMETRIC KEY、CERTIFICATE、CONTRACT、DATABASE、FULLTEXT CATALOG、MESSAGE TYPE、OBJECT、REMOTE SERVICE BINDING、ROLE、ROUTE、SCHEMA、SERVICE、SYMMETRIC KEY、TYPE、USER、XML SCHEMA COLLECTION|  
|CO|CONNECT|DATABASE|  
|CORP|CONNECT REPLICATION|DATABASE|  
|CP|CHECKPOINT|DATABASE|  
|CRAG|CREATE AGGREGATE|DATABASE|  
|CRAK|CREATE ASYMMETRIC KEY|DATABASE|  
|CRAS|CREATE ASSEMBLY|DATABASE|  
|CRCF|CREATE CERTIFICATE|DATABASE|  
|CRDB|CREATE DATABASE|DATABASE|  
|CRDF|CREATE DEFAULT|DATABASE|  
|CRED|CREATE DATABASE DDL EVENT NOTIFICATION|DATABASE|  
|CRFN|CREATE FUNCTION|DATABASE|  
|CRFT|CREATE FULLTEXT CATALOG|DATABASE|  
|CRMT|CREATE MESSAGE TYPE|DATABASE|  
|CRPR|CREATE PROCEDURE|DATABASE|  
|CRQU|CREATE QUEUE|DATABASE|  
|CRRL|CREATE ROLE|DATABASE|  
|CRRT|CREATE ROUTE|DATABASE|  
|CRRU|CREATE RULE|DATABASE|  
|CRSB|CREATE REMOTE SERVICE BINDING|DATABASE|  
|CRSC|CREATE CONTRACT|DATABASE|  
|CRSK|CREATE SYMMETRIC KEY|DATABASE|  
|CRSM|CREATE SCHEMA|DATABASE|  
|CRSN|CREATE SYNONYM|DATABASE|  
|CRSO|**適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降。<br /><br /> CREATE SEQUENCE|DATABASE|  
|CRSV|CREATE SERVICE|DATABASE|  
|CRTB|CREATE TABLE|DATABASE|  
|CRTY|CREATE TYPE|DATABASE|  
|CRVW|CREATE VIEW|DATABASE|  
|CRXS|**適用対象**: [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降。<br /><br /> CREATE XML SCHEMA COLLECTION|DATABASE|  
|DABO |ADMINISTER DATABASE BULK OPERATIONS | DATABASE |
|DL|DELETE|データベース、オブジェクト、スキーマ|  
|EAES |EXECUTE ANY EXTERNAL SCRIPT |DATABASE |
|EX|EXECUTE|ASSEMBLY、DATABASE、OBJECT、SCHEMA、TYPE、XML SCHEMA COLLECTION|  
|IM|IMPERSONATE|User|  
|IN|INSERT|データベース、オブジェクト、スキーマ|  
|RC|RECEIVE|OBJECT|  
|RF|REFERENCES|アセンブリ、非対称キー、証明書、コントラクト、データベース、フルテキストカタログ、メッセージ型、オブジェクト、スキーマ、対称キー、型、XML スキーマコレクション|  
|SL|SELECT|データベース、オブジェクト、スキーマ|  
|SN|SEND|SERVICE|  
|SPLN|SHOWPLAN|DATABASE|  
|SUQN|SUBSCRIBE QUERY NOTIFICATIONS|DATABASE|  
|TO|TAKE OWNERSHIP|ASSEMBLY、ASYMMETRIC KEY、CERTIFICATE、CONTRACT、DATABASE、FULLTEXT CATALOG、MESSAGE TYPE、OBJECT、REMOTE SERVICE BINDING、ROLE、ROUTE、SCHEMA、SERVICE、SYMMETRIC KEY、TYPE、XML SCHEMA COLLECTION|  
|UP|UPDATE|データベース、オブジェクト、スキーマ|  
|VW|VIEW DEFINITION|APPLICATION ROLE、ASSEMBLY、ASYMMETRIC KEY、CERTIFICATE、CONTRACT、DATABASE、FULLTEXT CATALOG、MESSAGE TYPE、OBJECT、REMOTE SERVICE BINDING、ROLE、ROUTE、SCHEMA、SERVICE、SYMMETRIC KEY、TYPE、USER、XML SCHEMA COLLECTION|  
|VWCK |列の暗号化キーの定義を表示します。|DATABASE |  
|VWCM |任意の列のマスター_キーの定義の表示|DATABASE |  
|VWCT|VIEW CHANGE TRACKING|テーブル、スキーマ|  
|VWDS|VIEW DATABASE STATE|DATABASE|  
  
## <a name="permissions"></a>権限  
 すべてのユーザーは自分の権限を参照できます。 他のユーザーの権限を表示するには、VIEW DEFINITION、ALTER ANY USER、またはユーザーに対する権限が必要です。 ユーザー定義ロールを表示するには、ALTER ANY ROLE、またはロールのメンバーシップ (public など) が必要です。  
  
 [!INCLUDE[ssCatViewPerm](../../includes/sscatviewperm-md.md)] 詳細については、「 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)」を参照してください。  
  
## <a name="examples"></a>例  
  
### <a name="a-listing-all-the-permissions-of-database-principals"></a>A: データベース プリンシパルのすべての権限を一覧表示する  
 次のクエリは、データベースプリンシパルに対して明示的に許可または拒否されている権限を一覧表示します。  
  
> [!IMPORTANT]  
>  固定データベースロールの権限は、sys.database_permissions には表示されません。 そのため、データベースプリンシパルには、ここに記載されていない追加のアクセス許可がある場合があります。  
  
```  
SELECT pr.principal_id, pr.name, pr.type_desc,   
    pr.authentication_type_desc, pe.state_desc, pe.permission_name  
FROM sys.database_principals AS pr  
JOIN sys.database_permissions AS pe  
    ON pe.grantee_principal_id = pr.principal_id;  
```  
  
### <a name="b-listing-permissions-on-schema-objects-within-a-database"></a>B: データベース内のスキーマオブジェクトに対する権限を一覧表示する  
 次のクエリでは、特定のスキーマオブジェクトに対して許可または拒否されるアクセス許可を一覧表示するために、sys.database_principals と sys.database_permissions を sys に結合しています。  
  
```  
SELECT pr.principal_id, pr.name, pr.type_desc,   
    pr.authentication_type_desc, pe.state_desc,   
    pe.permission_name, s.name + '.' + o.name AS ObjectName  
FROM sys.database_principals AS pr  
JOIN sys.database_permissions AS pe  
    ON pe.grantee_principal_id = pr.principal_id  
JOIN sys.objects AS o  
    ON pe.major_id = o.object_id  
JOIN sys.schemas AS s  
    ON o.schema_id = s.schema_id;  
```  
    
  
## <a name="see-also"></a>参照  
 [Securables](../../relational-databases/security/securables.md)   
 [権限の階層 &#40;データベース エンジン&#41;](../../relational-databases/security/permissions-hierarchy-database-engine.md)   
 [セキュリティ カタログ ビュー &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/security-catalog-views-transact-sql.md)   
 [カタログ ビュー &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/catalog-views-transact-sql.md)  
  
  


