---
description: sys.database_principals (Transact-sql)
title: sys.database_principals (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 10/27/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- database_principals
- database_principals_TSQL
- sys.database_principals
- sys.database_principals_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.database_principals catalog view
ms.assetid: 8cb239e9-eb8c-4109-9cec-0d35de95fa0e
author: VanMSFT
ms.author: vanto
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: ed859905d902edc2d6244425c473a0f6755a888c
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104752242"
---
# <a name="sysdatabase_principals-transact-sql"></a>sys.database_principals (Transact-sql)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  データベース内のセキュリティプリンシパルごとに1行のデータを返し [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|**name**|**sysname**|プリンシパルの名前。データベース内で一意です。|  
|**principal_id**|**int**|プリンシパルの ID。データベース内で一意です。|  
|**type**|**char(1)**|プリンシパルの種類:<br /><br /> A = アプリケーション ロール<br /><br /> C = 証明書にマップされたユーザー<br /><br /> E = Azure Active Directory の外部ユーザー<br /><br /> G = Windows グループ<br /><br /> K = 非対称キーにマップされたユーザー<br /><br /> R = データベース ロール<br /><br /> S = SQL ユーザー<br /><br /> U = Windows ユーザー<br /><br /> X = Azure Active Directory グループまたはアプリケーションからの外部グループ|  
|**type_desc**|**nvarchar(60)**|プリンシパルの種類の説明。<br /><br /> APPLICATION_ROLE<br /><br /> CERTIFICATE_MAPPED_USER<br /><br /> EXTERNAL_USER<br /><br /> WINDOWS_GROUP<br /><br /> ASYMMETRIC_KEY_MAPPED_USER<br /><br /> DATABASE_ROLE<br /><br /> SQL_USER<br /><br /> WINDOWS_USER<br /><br /> EXTERNAL_GROUPS|  
|**default_schema_name**|**sysname**|SQL 名でスキーマが指定されていない場合に使用される名前です。 型が S、U、またはでないプリンシパルの場合は Null です。|  
|**create_date**|**datetime**|プリンシパルが作成された日時。|  
|**modify_date**|**datetime**|プリンシパルが最後に変更された時刻。|  
|**owning_principal_id**|**int**|このプリンシパルを所有するプリンシパルの ID。 既定では、すべての固定データベースロールが **dbo** によって所有されています。|  
|**sid**|**varbinary(85)**|プリンシパルの SID (セキュリティ識別子)。  SYS および INFORMATION スキーマの場合は NULL です。|  
|**is_fixed_role**|**bit**|1の場合、この行は固定データベースロールの1つのエントリ (db_owner、db_accessadmin、db_datareader、db_datawriter、db_ddladmin、db_securityadmin、db_backupoperator、db_denydatareader、db_denydatawriter) を表します。|  
|**authentication_type**|**int**|**適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降。<br /><br /> 認証の種類を示します。 使用可能な値とその説明を次に示します。<br /><br /> 0: 認証なし<br />1: インスタンス認証<br />2: データベース認証<br />3: Windows 認証<br />4: 認証を Azure Active Directory|  
|**authentication_type_desc**|**nvarchar(60)**|**適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降。<br /><br /> 認証の種類の説明。 使用可能な値とその説明を次に示します。<br /><br /> なし: 認証なし<br />インスタンス: インスタンス認証<br />データベース: データベース認証<br />WINDOWS: Windows 認証<br />外部: Azure Active Directory 認証|  
|**default_language_name**|**sysname**|**適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降。<br /><br /> このプリンシパルの既定の言語を示します。|  
|**default_language_lcid**|**int**|**適用対象**: [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降。<br /><br /> このプリンシパルの既定の LCID を示します。|  
|**allow_encrypted_value_modifications**|**bit**|**適用対象**: [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 以降、[!INCLUDE[ssSDS_md](../../includes/sssds-md.md)]。<br /><br /> 一括コピー操作でのサーバーの暗号化メタデータ チェックを抑制します。 これにより、データの暗号化を解除することなく、テーブルまたはデータベース間で Always Encrypted を使用して暗号化されたデータを一括コピーできます。 既定値は OFF です。 |      
  
## <a name="remarks"></a>解説  
 *PasswordLastSetTime* プロパティは、SQL Server のサポートされているすべての構成で使用できますが、その他のプロパティは SQL Server が Windows Server 2003 以降で実行されており、CHECK_POLICY と CHECK_EXPIRATION の両方が有効になっている場合にのみ使用できます。 詳細については、「 [パスワードポリシー](../../relational-databases/security/password-policy.md) 」を参照してください。
Principal_id の値は、プリンシパルが削除された場合に再利用される可能性があるため、常に増加するとは限りません。
  
## <a name="permissions"></a>権限  
 すべてのユーザーは自分のユーザー名、システム ユーザー、および固定データベース ロールを参照できます。 他のユーザーを参照するには、ALTER ANY USER、またはユーザーに対する権限が必要です。 ユーザー定義ロールを参照するには、ALTER ANY ROLE、またはロールのメンバーシップが必要です。  
  
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
  
## <a name="examples-sssdwfull-and-sspdw"></a>例: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)]、[!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="c-listing-all-the-permissions-of-database-principals"></a>C: データベースプリンシパルのすべてのアクセス許可を一覧表示する  
 次のクエリは、データベースプリンシパルに対して明示的に許可または拒否されている権限を一覧表示します。  
  
> [!IMPORTANT]  
>  固定データベースロールの権限は、には表示されません `sys.database_permissions` 。 そのため、データベースプリンシパルには、ここに記載されていない追加のアクセス許可がある場合があります。  
  
```  
SELECT pr.principal_id, pr.name, pr.type_desc,   
    pr.authentication_type_desc, pe.state_desc, pe.permission_name  
FROM sys.database_principals AS pr  
JOIN sys.database_permissions AS pe  
    ON pe.grantee_principal_id = pr.principal_id;  
```  
  
### <a name="d-listing-permissions-on-schema-objects-within-a-database"></a>D: データベース内のスキーマオブジェクトに対する権限を一覧表示する  
 次のクエリでは、 `sys.database_principals` `sys.database_permissions` 特定の `sys.objects` `sys.schemas` スキーマオブジェクトに対して許可または拒否されている権限を、およびと結合して一覧表示します。  
  
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
 [プリンシパル &#40;データベース エンジン&#41;](../../relational-databases/security/authentication-access/principals-database-engine.md)   
 [sys.server_principals &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-server-principals-transact-sql.md)   
 [セキュリティ カタログ ビュー &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/security-catalog-views-transact-sql.md)   
 [包含データベースユーザー-データベースの移植性を高める](../../relational-databases/security/contained-database-users-making-your-database-portable.md)   
 [Azure Active Directory の認証を使用して、SQL データベースに接続します。](/azure/azure-sql/database/authentication-aad-overview)  
  
