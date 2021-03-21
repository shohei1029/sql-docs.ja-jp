---
title: プリンシパル (データベース エンジン) | Microsoft Docs
description: SQL Server リソースを要求できるエンティティであるデータベース エンジンのプリンシパルについて説明します。 SQL Server レベルとデータベースレベルのプリンシパルがあります。
ms.custom: ''
ms.date: 01/09/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: security
ms.topic: conceptual
f1_keywords:
- sql13.swb.roleproperties.selectroll.f1
- sql13.swb.databaseuser.permissions.user.f1--May use common.permissions
helpviewer_keywords:
- certificates [SQL Server], principals
- roles [SQL Server], principals
- permissions [SQL Server], principals
- '##MS_SQLAuthenticatorCertificate##'
- principals [SQL Server]
- '##MS_SQLResourceSigningCertificate##'
- groups [SQL Server], principals
- '##MS_AgentSigningCertificate##'
- authentication [SQL Server], principals
- schemas [SQL Server], principals
- principals [SQL Server], about principals
- security [SQL Server], principals
- users [SQL Server], principals
- '##MS_SQLReplicationSigningCertificate##'
ms.assetid: 3f7adbf7-6e40-4396-a8ca-71cbb843b5c2
author: VanMSFT
ms.author: vanto
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 9dc2478bee6ef6eafe5e0fe5d830325311bbdd6a
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104750412"
---
# <a name="principals-database-engine"></a>プリンシパル (データベース エンジン)
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  *プリンシパル* は、 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] リソースを要求できるエンティティです。 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] の承認モデルの他のコンポーネントと同様に、プリンシパルは階層内に配置できます。 プリンシパルの効力のスコープは、プリンシパルの定義のスコープ (Windows、サーバー、データベース) と、プリンシパルが分割不可能かコレクションであるかによって異なります。 分割できないプリンシパルの例には Windows ログインがあり、コレクションであるプリンシパルの例には Windows グループがあります。 各プリンシパルには、1 つのセキュリティ識別子 (SID) があります。 このトピックは、すべてのバージョンの SQL Server に適用されますが、SQL Database または Azure Synapse Analytics ではサーバー レベルのプリンシパルでいくつかの制約があります。 
  
## <a name="sql-server-level-principals"></a>SQL Server レベルのプリンシパル  
  
- [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 認証ログイン   
- Windows ユーザーの Windows 認証ログイン  
- Windows グループの Windows 認証ログイン   
- Active Directory ユーザーの Azure Active Directory 認証ログイン
- Active Directory グループの Azure Active Directory 認証ログイン
- サーバー ロール  
  
## <a name="database-level-principals"></a>データベースレベルのプリンシパル
  
- データベース ユーザー (12 種類のユーザーがあります。 詳細については、「[CREATE USER](../../../t-sql/statements/create-user-transact-sql.md)」 (ユーザーの作成) を参照してください。)
- データベース ロール
- アプリケーション ロール
  
## <a name="sa-login"></a>sa ログイン  
 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] `sa` ログインは、サーバーレベル プリンシパルです。 このログインは、インスタンスのインストール時に既定で作成されます。 [!INCLUDE[ssVersion2005](../../../includes/ssversion2005-md.md)]より、sa の既定のデータベースは master です。 これは、以前のバージョンの [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]の動作から変更されています。 `sa` ログインは `sysadmin` 固定サーバーレベル ロールに属します。 `sa` ログインにはサーバーのすべてのアクセス許可が与えられ、制限できません。 `sa` ログインは削除できませんが、無効にすれば誰も利用できません。

## <a name="dbo-user-and-dbo-schema"></a>dbo ユーザーと dbo スキーマ

`dbo` ユーザーは、各データベースの特別なユーザー プリンシパルです。 すべての SQL Server 管理者、`sysadmin` 固定サーバー ロールのメンバー、`sa` ログイン、データベースの所有者は `dbo` ユーザーとしてデータベースに入ります。 `dbo` ユーザーにはデータベースのすべてのアクセス許可が与えられ、制限したり、削除したりすることはできません。 `dbo` はデータベースの所有者 (database owner) という意味ですが、`dbo` ユーザー アカウントは `db_owner` 固定データベース ロールと同じではなく、`db_owner` 固定データベース ロールはデータベースの所有者として記録されているユーザー アカウントと同じではありません。     
`dbo` ユーザーは `dbo` スキーマを所有します。 `dbo` スキーマは、その他のスキーマが指定されていない限り、すべてのユーザーの既定のスキーマとなります。  `dbo` スキーマは削除できません。
  
## <a name="public-server-role-and-database-role"></a>public のサーバー ロールとデータベース ロール  
すべてのログインは `public` 固定サーバー ロールに属します。すべてのデータベース ユーザーは `public` データベース ロールに属します。 セキュリティ保護可能なリソースに対する特定の権限が与えられていないか権限が拒否されたログインまたはユーザーは、public がそのリソースに対して許可されている権限を継承します。 `public` 固定サーバー ロールと `public` 固定データベース ロールは削除できません。 ただし、`public` ロールからアクセス許可を取り消すことができます。 既定で `public` ロールにはさまざまなアクセス許可が割り当てられています。 そのようなアクセス許可のほとんどはデータベースの日常的操作、つまり、誰にでも許可されなければならない類いの操作に必要となります。 public ログインまたはユーザーからアクセス許可を取り消す際は注意してください。すべてのログインまたはユーザーに影響を与えます。 一般的に、public に対するアクセス許可は拒否しないでください。deny ステートメントは、個々に行う grant ステートメントをオーバーライドします。 
  
## <a name="information_schema-and-sys-users-and-schemas"></a>INFORMATION_SCHEMA と sys のユーザーとスキーマ 
 各データベースには、カタログ ビューにユーザーとして表示される 2 つのエンティティ `INFORMATION_SCHEMA` および `sys` が含まれています。 データベース エンジンによる内部利用でこれらのエンティティが必要になります。 変更したり、削除したりすることはできません。  
  
## <a name="certificate-based-sql-server-logins"></a>証明書ベースの SQL Server ログイン  
 名前が 2 つの番号記号 (##) で囲まれたサーバー プリンシパルは、内部システムでのみ使用されます。 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] のインストール時に証明書から作成される以下のプリンシパルは、削除しないでください。  
  
-   \##MS_SQLResourceSigningCertificate##    
-   \##MS_SQLReplicationSigningCertificate##    
-   \##MS_SQLAuthenticatorCertificate##    
-   \##MS_AgentSigningCertificate##   
-   \##MS_PolicyEventProcessingLogin##   
-   \##MS_PolicySigningCertificate##   
-   \##MS_PolicyTsqlExecutionLogin##   
 
 これらのプリンシパル アカウントのパスワードは、Microsoft に発行された証明書に基づくので、管理者は変更できません。
  
## <a name="the-guest-user"></a>guest ユーザー  
 各データベースには、 `guest`の動作から変更されています。 データベースにはアクセスできるが、データベース内のユーザー アカウントは持っていないユーザーは、 `guest` ユーザーに許可された権限を継承します。 `guest` ユーザーは消去することはできませんが、その CONNECT 権限を取り消すことで無効にすることはできます。 CONNECT 権限を取り消すには、`master` または `tempdb` 以外のデータベース内で `REVOKE CONNECT FROM GUEST;` を実行します。  
  
  
## <a name="related-tasks"></a>Related Tasks  
 権限システムの設計の詳細については、「 [データベース エンジンの権限の概要](../../../relational-databases/security/authentication-access/getting-started-with-database-engine-permissions.md)」を参照してください。  
  
 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] オンライン ブックのこのセクションの内容は次のとおりです。  
  
-   [ログイン、ユーザー、およびスキーマの管理方法に関するトピック](./create-a-login.md)  
  
-   [サーバーレベルのロール](../../../relational-databases/security/authentication-access/server-level-roles.md)  
  
-   [データベース レベルのロール](../../../relational-databases/security/authentication-access/database-level-roles.md)  
  
-   [アプリケーション ロール](../../../relational-databases/security/authentication-access/application-roles.md)  
  
## <a name="see-also"></a>参照  
 [SQL Server の保護](../../../relational-databases/security/securing-sql-server.md)   
 [sys.database_principals &#40;Transact-SQL&#41;](../../../relational-databases/system-catalog-views/sys-database-principals-transact-sql.md)   
 [sys.server_principals &#40;Transact-SQL&#41;](../../../relational-databases/system-catalog-views/sys-server-principals-transact-sql.md)   
 [sys.sql_logins &#40;Transact-SQL&#41;](../../../relational-databases/system-catalog-views/sys-sql-logins-transact-sql.md)   
 [sys.database_role_members &#40;Transact-SQL&#41;](../../../relational-databases/system-catalog-views/sys-database-role-members-transact-sql.md)   
 [サーバー レベルのロール](../../../relational-databases/security/authentication-access/server-level-roles.md)   
 [データベース レベルのロール](../../../relational-databases/security/authentication-access/database-level-roles.md)  
  
