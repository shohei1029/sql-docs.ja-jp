---
title: 包含データベースへの包含ユーザー アクセス
description: 包含データベースの包含ユーザー アクセスを構成する方法と、従来のログイン/ユーザー モデル間の違いについて説明します。
ms.custom: seo-lt-2019
ms.date: 01/28/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: security
ms.topic: conceptual
helpviewer_keywords:
- contained database, users
- user [SQL Server], about contained database users
ms.assetid: e57519bb-e7f4-459b-ba2f-fd42865ca91d
author: VanMSFT
ms.author: vanto
monikerRange: =azuresqldb-current||>=sql-server-2016||=azure-sqldw-latest||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: bfd77ab7a8f03431c103326793686eaa49a698b7
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104754692"
---
# <a name="contained-database-users---making-your-database-portable"></a>包含データベース ユーザー - データベースの可搬性を確保する

[!INCLUDE [SQL Server ASDB, ASDBMI, ASDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa.md)]

  包含データベース ユーザーを使用して [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] と [!INCLUDE[ssSDS](../../includes/sssds-md.md)] のデータベース レベルでの接続が認証されます。 包含データベースは、他のデータベース、およびデータベースをホストする [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]/ [!INCLUDE[ssSDS](../../includes/sssds-md.md)] (および master データベース) のインスタンスから分離されたデータベースです。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、Windows 認証と [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 認証の両方で包含データベース  ユーザーがサポートされます。 [!INCLUDE[ssSDS](../../includes/sssds-md.md)]を使用して、包含データベース ユーザーとデータベース レベルのファイアウォール規則を結合します。 このトピックでは、従来のログイン/ユーザー モデルおよび Windows またはサーバー レベルのファイアウォール規則と比較して、包含データベース モデルの相違点とこれを使用する利点について説明します。 特定のシナリオ、管理の容易性、アプリケーションのビジネス ロジックでは、従来のログイン / ユーザー モデルとサーバー レベルのファイアウォール規則を引き続き使用する必要があります。  
  
> [!NOTE]  
> [!INCLUDE[msCoName](../../includes/msconame-md.md)] の [!INCLUDE[ssSDS](../../includes/sssds-md.md)] サービスが向上し、SLA の保証が高度になると、包含データベース ユーザー モデルとデータベース用ファイアウォール規則に切り替え、特定のデータベースに対する SLA の可用性と最大ログイン レートをさらに高める必要があります。 [!INCLUDE[msCoName](../../includes/msconame-md.md)] は、今すぐこのような変化に対応することをお勧めします。  
  
## <a name="traditional-login-and-user-model"></a>従来のログイン / ユーザー モデル

 従来の接続モデルでは、Windows ユーザーまたは Windows グループのメンバーが [!INCLUDE[ssDE](../../includes/ssde-md.md)] に接続する際、Windows によって認証されているユーザーまたはグループの資格情報を指定します。 または、名前とパスワードの両方を指定でき、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 認証を使用して接続します。 どちらの場合も、接続ユーザーの資格情報に対応するログインが master データベースに格納されている必要があります。 通常、 [!INCLUDE[ssDE](../../includes/ssde-md.md)] で Windows 認証の資格情報が確認されるか、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 認証の資格情報で本人性が確認されると、ユーザー データベースへの接続が試行されます。 ユーザー データベースに接続するには、そのデータベース内のユーザーに対してログインをマップ (関連付けることが) できなければなりません。 また、特定のデータベースへの接続を接続文字列で指定する方法もあります。この方法は、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では任意ですが、 [!INCLUDE[ssSDS](../../includes/sssds-md.md)]では必須です。  
  
 重要なのは、(master データベース内の) ログインと (ユーザー データベース内の) ユーザーの両方が存在し、かつ相互に関連付けられていなければならない、ということです。 これは、ユーザー データベースへの接続が、master データベース内のログインに依存していることを意味します。そのことが、データベースのホストを別の [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] や [!INCLUDE[ssSDSFull](../../includes/sssdsfull-md.md)] サーバーに切り替えることを困難にしています。 また、なんらかの理由で、master データベースへの接続が利用できないと (フェールオーバーが進行中であるなど)、全体的な接続時間が増えたり、接続がタイムアウトしたりする可能性もあります。そのため、接続のスケーラビリティが低下します。  
  
## <a name="contained-database-user-model"></a>包含データベース ユーザー モデル

 包含データベース ユーザー モデルでは、ログインが master データベースには存在しません。 認証プロセスはユーザー データベースで実行されます。master データベースには、ユーザー データベース内のデータベース ユーザーに関連付けられたログインは存在しません。 包含データベース ユーザー モデルは Windows 認証と [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 認証の両方をサポートしており、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] と [!INCLUDE[ssSDS](../../includes/sssds-md.md)]の両方で使用できます。 包含データベース ユーザーとして接続するには必ず、ユーザー データベースのパラメーターが接続文字列に含まれている必要があります。 [!INCLUDE[ssDE](../../includes/ssde-md.md)] はそれを基に、認証プロセスがどちらのデータベースで管理されるかを判別します。 包含データベース ユーザーのアクティビティは、そのユーザーを認証するデータベースに限定されます。そのため包含データベース ユーザーとして接続しているときは、そのユーザーが必要とする個々のデータベースに、ユーザー アカウントを別々に作成する必要があります。 データベースを切り替えるには、 [!INCLUDE[ssSDS](../../includes/sssds-md.md)] ユーザー側で新しい接続を作成する必要があります。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 内の包含データベース ユーザーは、別のデータベースに同一ユーザーが存在する場合、データベースを切り替えることができます。  
  
**Azure:** [!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)] と [!INCLUDE[ssSDW_md](../../includes/sssdw-md.md)] は、包含データベース ユーザーとしての Azure Active Directory ID をサポートします。 [!INCLUDE[ssSDS_md](../../includes/sssds-md.md)] では、 [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] 認証で包含データベース ユーザーがサポートされますが、 [!INCLUDE[ssSDW_md](../../includes/sssdw-md.md)] ではサポートされません。 詳細については、[Azure Active Directory 認証を使用した SQL Database への接続](/azure/azure-sql/database/authentication-aad-overview)に関するページを参照してください。 Azure Active Directory 認証を使用するとき、Active Directory ユニバーサル認証を使用し、SSMS から接続できます。  管理者は多要素認証を要求するようにユニバーサル認証を設定できます。多要素認証では、電話、テキスト メッセージ、PIN のあるスマート カード、モバイル アプリ通知を利用して ID を確認します。 詳細については、[SQL Database と Azure Synapse Analytics での Azure AD MFA の SSMS サポート](/azure/azure-sql/database/authentication-mfa-ssms-overview)に関する記事を参照してください。  
  
 [!INCLUDE[ssSDS](../../includes/sssds-md.md)] と [!INCLUDE[ssSDW_md](../../includes/sssdw-md.md)]に関しては、接続文字列にはデータベース名が常に必要となるため、従来のモデルから包含データベース ユーザー モデルに切り替える際、接続文字列に対する変更は不要です。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 接続の場合は、データベースの名前を接続文字列に追加する必要があります (既に存在する場合は不要)。  
  
> [!IMPORTANT]  
> 従来型のモデルを使用した場合、サーバー レベルのロールとサーバー レベルの権限ですべてのデータベースに対するアクセスを制限できます。 包含データベース モデルを使用した場合、データベース所有者と ALTER ANY USER 権限を持ったデータベース ユーザーとがデータベースに対するアクセス権を付与できます。 これにより高い権限を与えられたサーバー ログインのアクセス制御範囲は狭められ、高い権限を与えられたデータベース ユーザーのアクセス制御範囲は拡大します。  
  
## <a name="firewalls"></a>ファイアウォール  
  
### [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]

 Windows ファイアウォール ルールはすべての接続に適用され、ログイン (従来のモデルの接続) と包含データベース ユーザーに同じ影響を及ぼします。 Windows ファイアウォールの詳細については、「 [データベース エンジン アクセスを有効にするための Windows ファイアウォールを構成する](../../database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access.md)」を参照してください。  
  
### <a name="sssds-firewalls"></a>[!INCLUDE[ssSDS](../../includes/sssds-md.md)] ファイアウォール

 [!INCLUDE[ssSDS](../../includes/sssds-md.md)] では、サーバー レベルの接続 (ログイン) 用とデータベース レベルの接続 (包含データベース ユーザー) 用にファイアウォール規則を切り離すことができます。 ユーザー データベースに接続すると、最初にデータベースのファイアウォール規則がチェックされます。 データベースへのアクセスを許可する規則が存在しない場合は、サーバー レベルのファイアウォール規則がチェックされます。これには、SQL Database サーバーのマスター データベースへのアクセスが必要です。 データベース レベルのファイアウォール規則と包含データベース ユーザーを組み合わせることで、接続中にサーバーのマスター データベースにアクセスする必要がなくなり、接続のスケーラビリティが向上します。  
  
 [!INCLUDE[ssSDS](../../includes/sssds-md.md)] のファイアウォール規則の詳細については、次のトピックを参照してください。  
  
- [Azure SQL Database ファイアウォール](/previous-versions/azure/ee621782(v=azure.100))  
- [方法: ファイアウォール設定を構成する (Azure SQL Database)](/previous-versions/azure/jj553530(v=azure.100))  
- [sp_set_firewall_rule &#40;Azure SQL データベース&#41;](../../relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database.md)  
- [sp_set_database_firewall_rule &#40;Azure SQL データベース&#41;](../../relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database.md)  
  
## <a name="syntax-differences"></a>構文上の違い  
  
|従来のモデル|包含データベース ユーザー モデル|  
|-----------------------|-----------------------------------|  
|master データベースに接続する場合:<br /><br /> `CREATE LOGIN login_name  WITH PASSWORD = 'strong_password';`<br /><br /> 次にユーザー データベースに接続する場合:<br /><br /> `CREATE USER 'user_name' FOR LOGIN 'login_name';`|ユーザー データベースにする場合:<br /><br /> `CREATE USER user_name  WITH PASSWORD = 'strong_password';`|  
  
|従来のモデル|包含データベース ユーザー モデル|  
|-----------------------|-----------------------------------|  
|master データベースのコンテキストでパスワードを変更するには:<br /><br /> `ALTER LOGIN login_name  WITH PASSWORD = 'strong_password';`|ユーザー データベースのコンテキストでパスワードを変更するには:<br /><br /> `ALTER USER user_name  WITH PASSWORD = 'strong_password';`|  

### <a name="managed-instance"></a>マネージド インスタンス

Azure SQL Managed Instance は、包含データベースのコンテキストでオンプレミス SQL Server のように動作します。 自分の包含ユーザーを作成するときは、ご利用のデータベースのコンテキストを master データベースからユーザーデータベースに変更してください。 また、containment オプションを設定するときは、ユーザー データベースへのアクティブな接続が存在しないようにする必要があります。 


次に例を示します。

> [!WARNING]
> 次のスクリプトを実行する前に、Managed Instance データベースで他の接続がアクティブになっていないことを確認してください。 このスクリプトにより、データベースで実行されている他のプロセスが中断される可能性があります。

```sql
Use MASTER;
GO 

ALTER DATABASE Test
SET RESTRICTED_USER
WITH ROLLBACK IMMEDIATE;

ALTER DATABASE Test
SET containment=partial;

ALTER DATABASE Test
SET MULTI_USER;

USE Test;  
GO 

CREATE USER Carlo  
WITH PASSWORD='Enterpwdhere*'  

SELECT containment_desc FROM sys.databases
WHERE name='Test'
```

  
## <a name="remarks"></a>解説  
  
- [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]では、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]のインスタンスに対して包含データベース ユーザーを有効にする必要があります。 詳細については、[包含データベース認証サーバー構成オプション](../../database-engine/configure-windows/contained-database-authentication-server-configuration-option.md)を参照してください。  
- 包含データベース ユーザーとログインの名前が重複しない場合は、アプリケーションで共存させることができます。  
- **name1** という名前のログインが master データベースに存在し、なおかつ **name1** という名前の包含データベース ユーザーを作成した場合、接続文字列でデータベース名を指定すると、そのデータベースに接続するときのログイン コンテキストよりも、データベース ユーザーのコンテキストが優先されます。 つまり、包含データベース ユーザーは、同じ名前のログインに優先します。  
- [!INCLUDE[ssSDS](../../includes/sssds-md.md)] では、包含データベース ユーザーの名前を、サーバーの管理者アカウントと同じ名前にすることはできません。  
- [!INCLUDE[ssSDS](../../includes/sssds-md.md)] サーバーの管理者アカウントは、包含データベース ユーザーにできません。 サーバー管理者は、包含データベース ユーザーを作成し、管理するための十分な権限を持っています。 サーバー管理者は、ユーザー データベースの包含データベース ユーザーに権限を付与できます。  
- 包含データベース ユーザーはデータベース レベル プリンシパルであるため、使用するすべてのデータベースで包含データベース ユーザーを作成する必要があります。 ID はデータベースに限定され、同じサーバー内の別のデータベースで同じ名前とパスワードを持つユーザーとは完全に独立しています。  
- 通常のログインで使用するパスワードと同じ強度のパスワードを使用します。  
  
## <a name="see-also"></a>参照

 [包含データベース](../../relational-databases/databases/contained-databases.md)   
 [包含データベースでのセキュリティのベスト プラクティス](../../relational-databases/databases/security-best-practices-with-contained-databases.md)   
 [CREATE USER &#40;Transact-SQL&#41;](../../t-sql/statements/create-user-transact-sql.md)   
 [Azure Active Directory の認証を使用して、SQL データベースに接続します。](/azure/azure-sql/database/authentication-aad-overview)  
