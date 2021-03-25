---
description: デタッチとアタッチを使用したデータベースのアップグレード (Transact-SQL)
title: デタッチとアタッチを使用したデータベースのアップグレード (Transact-SQL)
ms.date: 06/03/2020
ms.prod: sql
ms.reviewer: ''
ms.technology: ''
ms.topic: conceptual
helpviewer_keywords:
- database attaching [SQL Server]
- upgrading databases
- database upgrades [SQL Server]
- database detaching [SQL Server]
- detaching databases [SQL Server]
- attaching databases [SQL Server]
ms.assetid: 99f66ed9-3a75-4e38-ad7d-6c27cc3529a9
author: stevestein
ms.author: sstein
ms.custom: seo-dt-2019
ms.openlocfilehash: 8dcf9f0c1acf4231d532a25fade340d152d3b6af
ms.sourcegitcommit: c09ef164007879a904a376eb508004985ba06cf0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104890779"
---
# <a name="upgrade-a-database-using-detach-and-attach-transact-sql"></a>デタッチとアタッチを使用したデータベースのアップグレード (Transact-SQL)
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
このトピックでは、デタッチ操作とアタッチ操作を使用し、 [!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)]のデータベースをアップグレードする方法について説明します。 [!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)]にアタッチした後は、データベースが直ちに使用可能となり、自動的にアップグレードされます。 これにより、データベースが古いバージョンの [!INCLUDE[ssde_md](../../includes/ssde_md.md)] で使用されるのを防ぎます。 ただし、メタデータのアップグレードは、データベースの[データベースの互換性レベル](../../relational-databases/databases/view-or-change-the-compatibility-level-of-a-database.md)の設定には影響しません。 詳細については、このトピックで後述する「[アップグレード後のデータベース互換性レベル](#dbcompat)」を参照してください。  
  
 **このトピックの内容**  
  
-   **作業を開始する準備:**  
  
     [制限事項と制約事項](#Restrictions)  
  
     [Recommendations (推奨事項)](#Recommendations)  
  
-   **SQL Server データベースをアップグレードするには:**  
  
     [デタッチ操作とアタッチ操作の使用](#SSMSProcedure)  
  
-   **補足情報:**    

     [SQL Server データベースのアップグレード後](#FollowUp)  
  
##  <a name="before-you-begin"></a><a name="BeforeYouBegin"></a> はじめに  
  
###  <a name="limitations-and-restrictions"></a><a name="Restrictions"></a> 制限事項と制約事項  
  
-   システム データベースをアタッチすることはできません。  
  
-   アタッチとデタッチを行うと、 **cross db ownership chaining** オプションが 0 に設定され、複数データベースの組み合わせ所有権が無効になります。 チェーンを有効にする方法については、「 [cross db ownership chaining サーバー構成オプション](../../database-engine/configure-windows/cross-db-ownership-chaining-server-configuration-option.md)」を参照してください。  
  
-   レプリケートされたデータベースをアタッチする際に、そのデータベースがデタッチではなくコピーされたものである場合は、次の点を考慮してください。  
  
    -   同じサーバー インスタンスのアップグレードされたバージョンにデータベースをアタッチする場合は、アタッチ操作が完了した後、 **sp_vupgrade_replication** を実行してレプリケーションをアップグレードする必要があります。 詳細については、「[sp_vupgrade_replication &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-vupgrade-replication-transact-sql.md)」を参照してください。  
  
    -   バージョンに関係なく別のサーバー インスタンスにデータベースをアタッチする場合は、アタッチ操作が完了した後、 **sp_removedbreplication** を実行してレプリケーションを削除する必要があります。 詳細については、「[sp_removedbreplication &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-removedbreplication-transact-sql.md)」を参照してください。  
  
###  <a name="recommendations"></a><a name="Recommendations"></a> 推奨事項  
不明なソースや信頼されていないソースからデータベースをアタッチまたは復元しないことをお勧めします。 こうしたデータベースには、意図しない [!INCLUDE[tsql](../../includes/tsql-md.md)] コードを実行したり、スキーマまたは物理データベース構造を変更してエラーを発生させるような、悪意のあるコードが含まれている可能性があります。 不明または信頼できないソースのデータベースを使用する前に、運用サーバー以外のサーバーでそのデータベースに対し [DBCC CHECKDB](../../t-sql/database-console-commands/dbcc-checkdb-transact-sql.md) を実行し、さらに、そのデータベースのストアド プロシージャやその他のユーザー定義コードなどのコードを調べます。  
  
##  <a name="to-upgrade-a-database-by-using-detach-and-attach"></a><a name="SSMSProcedure"></a> デタッチとアタッチを使用してデータベースをアップグレードするには  
  
1.  データベースをデタッチします。 詳細については、「 [データベースのデタッチ](../../relational-databases/databases/detach-a-database.md)」を参照してください。  
  
2.  必要に応じて、デタッチされたデータベース ファイルとログ ファイルを移動します。  
  
     新しいログ ファイルを作成する場合であっても、ログ ファイルをデータ ファイルと一緒に移動する必要があります。 場合によっては、データベースの再アタッチに既存のログ ファイルが必要になります。 したがって、デタッチしたログ ファイルを使わずにデータベースを正常にアタッチできるまで、デタッチしたログ ファイルは必ずすべて保管しておいてください。  
  
    > [!NOTE]  
    >  ログ ファイルを指定せずにデータベースのインポートを試みると、アタッチ操作は元の場所でログ ファイルを検索します。 元の場所にログの元のコピーが依然として存在する場合は、そのコピーがアタッチされます。 元のログ ファイルが使用されないようにするには、新しいログ ファイルのパスを指定するか、ログ ファイルの元のコピーを (新しい場所にコピーした後で) 削除します。  
  
3.  コピーしたファイルを [!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)]のインスタンスにアタッチします。 詳細については、「 [Attach a Database](../../relational-databases/databases/attach-a-database.md)」を参照してください。  
  
## <a name="example"></a>例  
 次の例では、データベースのコピーを以前のバージョンの SQL Server からアップグレードします。 [!INCLUDE[tsql](../../includes/tsql-md.md)] ステートメントは、アタッチするサーバー インスタンスに接続されたクエリ エディター ウィンドウで実行しています。  
  
1.  次の [!INCLUDE[tsql](../../includes/tsql-md.md)] ステートメントを実行してデータベースをデタッチします。  
  
    ```sql  
    USE master;  
    GO  
    EXEC sp_detach_db @dbname = N'MyDatabase';  
    GO  
    ```  
  
2.  任意の方法で、データ ファイルとログ ファイルを新しい場所にコピーします。  
  
    > [!IMPORTANT]  
    > 実稼動データベースの場合は、データベースとトランザクション ログを別のディスクに配置することをお勧めします。 これらのドライブは I/O とファイルの増加要件が異なるため、別々の場所に保持するのがベスト プラクティスと見なされます。  
  
    ファイルをネットワーク経由でリモート コンピューターのディスクにコピーするには、そのリモート コンピューターの UNC (Universal Naming Convention) 名を使用します。 UNC 名の形式は、`\\Servername\Sharename\Path\Filename` です。 ローカル ハード ディスクにファイルを書き込む場合と同様、リモート ディスクでのファイルの読み取りや書き込みに必要な適切な権限が、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]のインスタンスで使用するユーザー アカウントに許可されている必要があります。  
  
3.  次の [!INCLUDE[tsql](../../includes/tsql-md.md)] ステートメントを使用して、移動したデータベースとログをアタッチします (ログのアタッチは省略できます)。  
  
    ```sql  
    USE master;  
    GO  
    CREATE DATABASE MyDatabase   
        ON (FILENAME = 'C:\MySQLServer\MyDatabase.mdf'),  
        (FILENAME = 'C:\MySQLServer\Database.ldf')  
        FOR ATTACH;  
    GO  
    ```  
  
    [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)]では、新しくアタッチされたデータベースはオブジェクト エクスプローラーにすぐに表示されません。 このデータベースを表示するには、オブジェクト エクスプローラーで、 **[表示]** をクリックし、 **[最新の情報に更新]** をクリックします。 オブジェクト エクスプローラーの **[データベース]** ノードを展開すると、データベースの一覧に新しくアタッチされたデータベースが表示されるようになります。  
  
##  <a name="follow-up-after-upgrading-a-sql-server-database"></a><a name="FollowUp"></a>補足情報: SQL Server データベースのアップグレード後  
データベースにフルテキスト インデックスがある場合、アップグレード プロセスでは、 **upgrade_option** サーバー プロパティの設定に応じて、インポート、リセット、または再構築が行われます。 アップグレード オプションがインポート (**upgrade_option** = 2) または再構築 (**upgrade_option** = 0) に設定されている場合、アップグレード中はフルテキスト インデックスを使用できなくなります。 インデックスを作成するデータ量によって、インポートには数時間、再構築には最大でその 10 倍の時間がかかることがあります。 また、アップグレード オプションがインポートに設定されており、フルテキスト カタログが使用できない場合は、関連付けられたフルテキスト インデックスが再構築されます。 **upgrade_option** サーバー プロパティの設定を変更するには、 [sp_fulltext_service](../../relational-databases/system-stored-procedures/sp-fulltext-service-transact-sql.md)を使用します。  
  
### <a name="database-compatibility-level-after-upgrade"></a><a name="dbcompat"></a> アップグレード後のデータベース互換性レベル  

アップグレード後は、以前の互換性レベルが新しいバージョンでサポートされていない場合を除き、データベースの互換性レベルはアップグレード前の互換性レベルのままです。 この場合、アップグレードされたデータベースの互換性レベルは、サポートされている最低の互換性レベルに設定されます。

たとえば、[!INCLUDE [sssql19-md](../../includes/sssql19-md.md)]のインスタンスにアタッチする前の互換性レベルが 90 であったデータベースをアタッチする場合、アップグレード後、互換性レベルは 100 に設定されます。これは、[!INCLUDE [sssql19-md](../../includes/sssql19-md.md)] でサポートされている最低の互換性レベルです。 詳細については、「[ALTER DATABASE 互換性レベル &#40;Transact-SQL&#41;](../../t-sql/statements/alter-database-transact-sql-compatibility-level.md)」を参照してください。

### <a name="managing-metadata-on-the-upgraded-server-instance"></a>アップグレードされたサーバー インスタンスでのメタデータの管理  
データベースを別のサーバー インスタンスにアタッチするときは、ユーザーおよびアプリケーションに一貫した使用環境を提供するために、アタッチ先のサーバー インスタンスで、ログイン、ジョブ、権限などのデータベースのメタデータの一部またはすべてを作成し直す必要が生じる場合があります。 詳細については、「 [データベースを別のサーバー インスタンスで使用できるようにするときのメタデータの管理 &#40;SQL Server&#41;](../../relational-databases/databases/manage-metadata-when-making-a-database-available-on-another-server.md)」を参照してください。  
  
### <a name="service-master-key-and-database-master-key-encryption-changes-from-3des-to-aes"></a>3DES から AES へのサービス マスター キーとデータベース マスター キーの暗号化の変更  
 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降のバージョンは、AES 暗号化アルゴリズムを使用してサービス マスター キー (SMK) とデータベース マスター キー (DMK) を保護します。 AES は、以前のバージョンで使用されていた 3DES よりも新しい暗号化アルゴリズムです。 データベースが最初に [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]の新しいインスタンスにアタッチまたは復元されるとき、データベース マスター キー (サービス マスター キーにより暗号化されたもの) のコピーはまだサーバーに格納されていません。 使用する必要があります、 `OPEN MASTER KEY` ステートメントをデータベース マスター_キー (DMK) の暗号化を解除します。 DMK の暗号化が解除されると、`ALTER MASTER KEY REGENERATE` ステートメントを使用して、サービス マスター キー (SMK) で暗号化された DMK のコピーをサーバーに提供することにより、将来、自動的に暗号化解除することも可能となります。 データベースを以前のバージョンからアップグレードした場合、新しい AES アルゴリズムを使用するように DMK を再作成する必要があります。 DMK を再作成する方法については、「[ALTER MASTER KEY &#40;Transact-SQL&#41;](../../t-sql/statements/alter-master-key-transact-sql.md)」を参照してください。 DMK キーを再作成して AES にアップグレードするのに必要な時間は、DMK によって保護されているオブジェクトの数によって異なります。 DMK キーを再作成して AES にアップグレードする作業は、1 回限りで済み、今後のキー ローテーション方法には影響を与えません。  
  
  
