---
title: 孤立したユーザーのトラブルシューティング
description: データベース ユーザー ログインがマスター データベースに存在しなくなったとき、孤立したユーザーが発生します。 このトピックでは、孤立したユーザーを特定して解決する方法について説明します。
ms.custom: seo-lt-2019
ms.date: 07/14/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: high-availability
ms.topic: troubleshooting
helpviewer_keywords:
- orphaned users [SQL Server]
- logins [SQL Server], orphaned users
- troubleshooting [SQL Server], user accounts
- user accounts [SQL Server], orphaned users
- failover [SQL Server], managing metadata
- database mirroring [SQL Server], metadata
- users [SQL Server], orphaned
ms.assetid: 11eefa97-a31f-4359-ba5b-e92328224133
author: cawrites
ms.author: chadam
monikerRange: '>= aps-pdw-2016 || = azuresqldb-current || = azure-sqldw-latest || >= sql-server-2016'
ms.openlocfilehash: 3067428c780c675b7601f66abc023a07b056eabe
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104753202"
---
# <a name="troubleshoot-orphaned-users-sql-server"></a>孤立したユーザーのトラブルシューティング (SQL Server)
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  データベース ユーザーが [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] マスター **データベースのログインに基づくが、ログインが** マスター **に存在しなくなったとき、** の孤立ユーザーが発生します。 これはログインが削除されたか、データベースが別のサーバーに移され、ログインがなくなったときに発生します。 このトピックでは、孤立ユーザーを見つけ、ログインに再マッピングする方法について説明します。  
  
> [!NOTE]  
>  移動される可能性のあるデータベースには包含データベース ユーザーを利用し、孤立ユーザーの可能性を減らします。 詳細については、「 [包含データベース ユーザー - データベースの可搬性を確保する](../../relational-databases/security/contained-database-users-making-your-database-portable.md)」を参照してください。  
  
## <a name="background"></a>バックグラウンド  
 ログインに基づくセキュリティ プリンシパルを利用して [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスでデータベースに接続するには、 **マスター** データベースでプリンシパルに有効なログインを与える必要があります。 このログインは、プリンシパル ID を確認し、プリンシパルが [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]のインスタンスに接続できるかどうかを決定する認証プロセスで使用されます。 サーバー インスタンスの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログインは、 **sys.server_principals** カタログ ビューと **sys.sql_logins** 互換性ビューで表示できます。  
  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログインは、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログインにマップされている "データベース ユーザー" として個々のデータベースにアクセスします。 このルールには次の 3 つの例外があります。  
  
-   包含データベース ユーザー  
  
     包含データベース ユーザーはユーザー/データベース レベルで認証し、ログインには関連付けられません。 データベースの移植性が高く、包含データベース ユーザーは孤立しないことから、包含データベース ユーザーが推奨されます。 ただし、データベースごと再作成する必要があります。 データベースの多い環境では現実的ではない可能性があります。  
  
-   **guest** アカウント  
  
     このアカウントがデータベースで有効になっていると、データベース ユーザーにマップされていない [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログインは **ゲスト** ユーザーとしてデータベースにアクセスできます。 既定では、 **guest** アカウントは無効になっています。  
  
-   Microsoft Windows グループのメンバー  
  
     Windows ユーザーから作成した [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログインは、Windows ユーザーが属しているグループがデータベースのユーザーである場合にデータベースにアクセスできます。  
  
 データベース ユーザーへの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログインのマップに関する情報は、データベース内に格納されます。 これには、データベース ユーザーの名前および対応する [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログインの SID が含まれます。 データベース内での承認には、このデータベース ユーザーの権限が適用されます。  
  
 データベース ユーザー (ログイン ベース) は、それに対応する [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログインが未定義のとき、またはサーバー インスタンスで適切に定義されていないとき、インスタンスにログインできません。 このようなユーザーは、そのサーバー インスタンスのデータベースの *孤立ユーザー* と呼ばれます。 孤立状態は、データベース ユーザーが `master` インスタンスに存在しないログイン SID にマップされると発生する場合があります。 データベースを復元した後や、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の別のインスタンスにアタッチしたが、そこではログインが作成されていないときも、孤立状態になることがあります。 データベース ユーザーは、対応する [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログインが削除された場合にも孤立状態になることがあります。 ログインが再作成される場合でも、異なる SID が与えられ、データベース ユーザーが孤立します。  
  
## <a name="detect-orphaned-users"></a>孤立したユーザーを検出する  

**[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] および PDW の場合**

見つからない [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 認証ログインに基づいて [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の孤立ユーザーを検出するには、ユーザー データベースで次のステートメントを実行します。  
  
```  
SELECT dp.type_desc, dp.SID, dp.name AS user_name  
FROM sys.database_principals AS dp  
LEFT JOIN sys.server_principals AS sp  
    ON dp.SID = sp.SID  
WHERE sp.SID IS NULL  
    AND authentication_type_desc = 'INSTANCE';  
```  
  
 現在のデータベース内で、どの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログインにもリンクされていない [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 認証ユーザーとそのセキュリティ識別子 (SID) が出力されます。  

**SQL Database と Azure Synapse Analytics の場合**

`sys.server_principals` テーブルは、SQL Database または Azure Synapse Analytics では使用できません。 これらの環境では、次の手順を使用して孤立ユーザーを識別します。

1. `master` データベースに接続し、次のクエリを使用してログインの SID を選択します。
    ```
    SELECT sid 
    FROM sys.sql_logins 
    WHERE type = 'S'; 
    ```

2. ユーザー データベースに接続し、次のクエリを使用して `sys.database_principals` テーブル内のユーザーの SID を確認します。

    ```
    SELECT name, sid, principal_id
    FROM sys.database_principals 
    WHERE type = 'S' 
      AND name NOT IN ('guest', 'INFORMATION_SCHEMA', 'sys')
      AND authentication_type_desc = 'INSTANCE';
    ```

3. 2 つのリストを比較して、ユーザー データベースの `sys.database_principals` テーブルのユーザー SID の中に、マスター データベースの `sql_logins` テーブルのログイン SID と一致しないものがあるかどうかを調べます。 
  
## <a name="resolve-an-orphaned-user"></a>孤立したユーザーを解決する  
マスター データベースで、SID オプションで [CREATE LOGIN](../../t-sql/statements/create-login-transact-sql.md) ステートメントを使用して、なくなったログインを再作成します。前のセクションで取得したデータベース ユーザーの `SID` を提供します。  
  
```  
CREATE LOGIN <login_name>   
WITH PASSWORD = '<use_a_strong_password_here>',  
SID = <SID>;  
```  
  
 **マスター** に既に存在するログインに孤立ユーザーをマッピングするには、ログイン名を指定し、ユーザー データベースで [ALTER USER](../../t-sql/statements/alter-user-transact-sql.md) ステートメントを実行します。  
  
```  
ALTER USER <user_name> WITH Login = <login_name>;  
```  
  
 なくなったログインを再作成するとき、提供されたパスワードを使用し、データベースにアクセスできます。 それから、ALTER LOGIN ステートメントを使用し、ログイン アカウントのパスワードを変更できます。  
  
```  
ALTER LOGIN <login_name> WITH PASSWORD = '<enterStrongPasswordHere>';  
```  
  
> [!IMPORTANT]  
>  いずれのログインでも、固有のパスワードを変更できます。 `ALTER ANY LOGIN` 権限を持つログインだけが、他のユーザーのログイン パスワードを変更できます。 ただし、 **sysadmin** ロール メンバーのパスワードを変更できるのは、 **sysadmin** ロールのメンバーだけです。  
  
## <a name="see-also"></a>参照  
 [CREATE LOGIN &#40;Transact-SQL&#41;](../../t-sql/statements/create-login-transact-sql.md)   
 [ALTER USER &#40;Transact-SQL&#41;](../../t-sql/statements/alter-user-transact-sql.md)   
 [CREATE USER &#40;Transact-SQL&#41;](../../t-sql/statements/create-user-transact-sql.md)   
 [sys.database_principals &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-database-principals-transact-sql.md)   
 [sys.server_principals &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-server-principals-transact-sql.md)   
 [sp_change_users_login &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-change-users-login-transact-sql.md)   
 [sp_addlogin &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-addlogin-transact-sql.md)   
 [sp_grantlogin &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-grantlogin-transact-sql.md)   
 [sp_password &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-password-transact-sql.md)   
 [sys.sysusers &#40;Transact-SQL&#41;](../../relational-databases/system-compatibility-views/sys-sysusers-transact-sql.md)   
 [sys.sql_logins](../../relational-databases/system-catalog-views/sys-sql-logins-transact-sql.md) [sys.syslogins &#40;Transact-SQL&#41;](../../relational-databases/system-compatibility-views/sys-syslogins-transact-sql.md)  
  
  
