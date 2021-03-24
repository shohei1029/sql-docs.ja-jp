---
title: データベースのデタッチとアタッチ (SQL Server)
description: SQL Server データベースのデータとトランザクション ログ ファイルをデタッチしてから再アタッチすることで、データベースを別のインスタンスに変更したり、データベースを移動したりすることができます。
ms.custom: ''
ms.date: 06/30/2020
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: backup-restore
ms.topic: conceptual
helpviewer_keywords:
- upgrading databases
- databases [SQL Server], detaching
- detach database [SQL Server]
- databases [SQL Server], attaching
- removing databases
- transaction logs [SQL Server], detaching
- databases [SQL Server], removing
- restoring [SQL Server], attached databases
- transaction logs [SQL Server], attaching
- differential backups, after detaching
- moving databases
- attach database [SQL Server]
- detaching databases [SQL Server]
- differential base [SQL Server]
- attaching databases [SQL Server]
- databases [SQL Server], moving
ms.assetid: d0de0639-bc54-464e-98b1-6af22a27eb86
author: stevestein
ms.author: sstein
ms.openlocfilehash: ffa25f98d738b2cac2ec4d796bb946e9bac9e03b
ms.sourcegitcommit: c09ef164007879a904a376eb508004985ba06cf0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104890743"
---
# <a name="database-detach-and-attach-sql-server"></a>データベースのデタッチとアタッチ (SQL Server)
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
データベースのデータ ファイルおよびトランザクション ログ ファイルは、デタッチして、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]の同一または別のインスタンスに再度アタッチすることができます。 同一コンピューターの別の [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンスにデータベースを変更したり、データベースを移動したりする場合、データベースをデタッチしてアタッチする操作が便利です。  
  
  
##  <a name="security"></a><a name="Security"></a> セキュリティ  
ファイル アクセス許可は、データベースのデタッチやアタッチなど、さまざまなデータベース操作中に設定されます。  
  
> [!IMPORTANT]  
> 不明なソースや信頼されていないソースからデータベースをアタッチまたは復元しないことをお勧めします。 こうしたデータベースには、意図しない [!INCLUDE[tsql](../../includes/tsql-md.md)] コードを実行したり、スキーマまたは物理データベース構造を変更してエラーを発生させるような、悪意のあるコードが含まれている可能性があります。   
> 不明または信頼できないソースのデータベースを使用する前に、運用サーバー以外のサーバーでそのデータベースに対し [DBCC CHECKDB](../../t-sql/database-console-commands/dbcc-checkdb-transact-sql.md) を実行し、さらに、そのデータベースのストアド プロシージャやその他のユーザー定義コードなどのコードを調べます。  
  
##  <a name="detaching-a-database"></a><a name="DetachDb"></a> データベースのデタッチ  
データベースはデタッチすると、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンスからは削除されますが、データ ファイルおよびトランザクション ログ ファイル内ではそのまま残ります。 これらのデータ ファイルとトランザクション ログ ファイルを使用して、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]の任意のインスタンスにデータベースをアタッチできます。その際、そのデータベースをデタッチした元のサーバーにアタッチすることもできます。  
  
次の条件に 1 つでも該当する場合、データベースをデタッチできません。  
  
-   データベースがレプリケートおよびパブリッシュされている。 レプリケートされている場合、データベースをパブリッシュしてはいけません。 データベースをデタッチする前に、 [sp_replicationdboption](../../relational-databases/system-stored-procedures/sp-replicationdboption-transact-sql.md)を実行してパブリッシングを無効にする必要があります。  
  
    > [!NOTE]  
    > **sp_replicationdboption** を使用できない場合、 [sp_removedbreplication](../../relational-databases/system-stored-procedures/sp-removedbreplication-transact-sql.md)を実行してレプリケーションを削除できます。  
  
-   データベースに、データベース スナップショットが存在する。  
  
    データベースをデタッチするには、すべてのデータベース スナップショットを削除する必要があります。 詳細については、「 [データベース スナップショットの削除 &#40;Transact-SQL&#41;](../../relational-databases/databases/drop-a-database-snapshot-transact-sql.md)」を参照してください。  
  
    > [!NOTE]  
    > データベース スナップショットのデタッチおよびアタッチは行うことができません。  

-   データベースは、Always On 可用性グループの一部です。  
  
    可用性グループから削除されるまで、データベースをデタッチすることはできません。 詳細については、「[Always On 可用性グループからプライマリ データベースを削除する](../../database-engine/availability-groups/windows/remove-a-primary-database-from-an-availability-group-sql-server.md)」をご覧ください。
  


-   データベースがデータベース ミラーリング セッションでミラー化される。  
  
    セッションが終了するまでは、データベースはデタッチできません。 詳細については、「 [データベース ミラーリングの削除 &#40;SQL Server&#41;](../../database-engine/database-mirroring/removing-database-mirroring-sql-server.md)」を参照してください。  
  
-   データベースに問題がある。 問題のあるデータベースはデタッチできません。デタッチするには緊急モードにする必要があります。 データベースを緊急モードに設定する方法の詳細については、「 [ALTER DATABASE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-database-transact-sql.md)」を参照してください。  
  
-  データベースがシステム データベースである。  
  
### <a name="backup-and-restore-and-detach"></a>バックアップと復元およびデタッチ  
読み取り専用のデータベースをデタッチすると、差分バックアップの差分ベースに関する情報が失われます。 詳細については、「 [差分バックアップ &#40;SQL Server&#41;](../../relational-databases/backup-restore/differential-backups-sql-server.md)」を参照してください。  
  
### <a name="responding-to-detach-errors"></a>デタッチ エラーへの対応  
データベースのデタッチ中にエラーが発生すると、データベースがクリーンに閉じず、トランザクション ログが再構築されないことがあります。 エラー メッセージが表示される場合は、次の修正操作を実行してください。  
  
1.  プライマリ ファイルだけでなく、データベースに関連付けられているすべてのファイルを再アタッチします。  
  
2.  エラー メッセージの原因となった問題を解決します。  
  
3.  データベースをデタッチし直します。  
  
##  <a name="attaching-a-database"></a><a name="AttachDb"></a> データベースのインポート  
コピーまたはデタッチした [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] データベースはアタッチできます。 フルテキスト カタログ ファイルを含む [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] データベースを [!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] サーバー インスタンスにアタッチする場合、カタログ ファイルは [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)]と同様に他のデータベース ファイルと一緒に以前の場所からアタッチされます。 詳細については、「 [フルテキスト検索のアップグレード](../../relational-databases/search/upgrade-full-text-search.md)」を参照してください。  
  
データベースをアタッチするときは、すべてのデータ ファイル (MDF ファイルおよび NDF ファイル) を利用できる状態にする必要があります。 データベースを最初に作成したときか最後にアタッチしたときとデータ ファイルのパスが異なる場合、ファイルの現在のパスを指定する必要があります。  
  
> [!NOTE]  
> アタッチ中のプライマリ データ ファイルが読み取り専用の場合、 [!INCLUDE[ssDE](../../includes/ssde-md.md)] ではデータベースが読み取り専用であると想定されます。  
  
暗号化されたデータベースが [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスに最初にアタッチされている場合、データベース所有者は、`OPEN MASTER KEY DECRYPTION BY PASSWORD = 'password'` というステートメントを実行してデータベースのマスター キーを開く必要があります。 `ALTER MASTER KEY ADD ENCRYPTION BY SERVICE MASTER KEY` ステートメントを実行してマスター キーの自動暗号化解除を有効にすることをお勧めします。 詳細については、「[CREATE MASTER KEY &#40;Transact-SQL&#41;](../../t-sql/statements/create-master-key-transact-sql.md)」と「[ALTER MASTER KEY &#40;Transact-SQL&#41;](../../t-sql/statements/alter-master-key-transact-sql.md)」を参照してください。  
  
 次に示すように、ログ ファイルをアタッチするための要件の一部は、データベースが読み書き可能か読み取り専用かによって異なります。  
  
-   読み書き可能なデータベースは、通常、新しい場所にログ ファイルをアタッチできます。 ただし、場合によっては、データベースの再アタッチに既存のログ ファイルが必要になります。 したがって、デタッチされたログ ファイルなしでデータベースが正常にアタッチされるまで、デタッチされたすべてのログ ファイルを常に保持しておくことが重要です。  
  
    読み書き可能なデータベースのログ ファイルが 1 つで、そのファイルの新しい場所を指定しない場合、アタッチ操作ではファイルの古い場所が検索されます。 古いログ ファイルが見つかった場合、データベースがクリーンにシャットダウンされたかどうかにかかわらず、そのファイルが使用されます。 しかし、古いログ ファイルが見つからなかった場合、およびデータベースがクリーンにシャットダウンされたもののアクティブなログ チェーンがない場合、アタッチ操作によってそのデータベースの新しいログ ファイルが作成されます。  
  
-   アタッチ中のプライマリ データ ファイルが読み取り専用の場合、 [!INCLUDE[ssDE](../../includes/ssde-md.md)] ではデータベースが読み取り専用であると想定されます。 読み取り専用データベースは、プライマリ ファイルに指定されている場所でログ ファイルを使用できる必要があります。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ではプライマリ ファイルに格納されているログの場所を更新できないので、新しいログ ファイルは作成できません。  
 
###  <a name="metadata-changes-on-attaching-a-database"></a><a name="Metadata"></a> データベースのインポート時におけるメタデータの変更  
読み取り専用データベースをデタッチして再アタッチすると、現在の差分ベースに関するバックアップ情報が失われます。 *差分ベース* とは、データベース内のすべてのデータ、またはデータベースのファイルやファイル グループのサブセット内のすべてのデータを対象とした最新の完全バックアップのことです。 ベース バックアップ情報がない場合、 **master** データベースは読み取り専用データベースと同期されなくなります。そのため、それ以降に取得した差分バックアップで予期しない結果が発生することがあります。 したがって、読み取り専用データベースに対して差分バックアップを使用する場合は、データベースを再アタッチした後に、完全バックアップを行って新しい差分ベースを作成する必要があります。 差分バックアップについては、「[差分バックアップ &#40;SQL Server&#41;](../../relational-databases/backup-restore/differential-backups-sql-server.md)」を参照してください。  
  
アタッチ時に、データベースが起動します。 通常はデータベースをアタッチすると、そのデータベースはデタッチまたはコピーされたときと同じ状態になります。 ただし、アタッチおよびデタッチ操作により、複数データベースにまたがる組み合わせ所有権が無効になります。 チェーンを有効にする方法については、「 [cross db ownership chaining サーバー構成オプション](../../database-engine/configure-windows/cross-db-ownership-chaining-server-configuration-option.md)」を参照してください。 

 > [!IMPORTANT]
 > 既定で、かつ、セキュリティ上の理由から、データベースがアタッチされているとき、*is_broker_enabled*、*is_honor_broker_priority_on*、*is_trustworthy_on* のオプションは必ず OFF に設定されます。 これらのオプションを ON に設定する方法については「[ALTER DATABASE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-database-transact-sql.md)」を参照してください。  詳細については、「[データベースを別のサーバーで使用できるようにするときのメタデータの管理](../../relational-databases/databases/manage-metadata-when-making-a-database-available-on-another-server.md)」を参照してください。
  
### <a name="backup-and-restore-and-attach"></a>バックアップと復元およびアタッチ  
完全または部分的にオフラインのデータベースと同様に、復元中のファイルが含まれているデータベースはアタッチできません。 復元シーケンスを停止すると、データベースをアタッチできます。 データベースのインポート後、復元シーケンスを再開できます。  
  
###  <a name="attaching-a-database-to-another-server-instance"></a><a name="OtherServerInstance"></a> 別のサーバー インスタンスへのデータベースのインポート  
  
> [!IMPORTANT]  
> 新しいバージョンの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] で作成したデータベースは、それ以前のバージョンでアタッチすることはできません。 これにより、データベースが古いバージョンの [!INCLUDE[ssde_md](../../includes/ssde_md.md)] で物理的に使用できないようにします。 ただし、これはメタデータの状態に関係し、[データベースの互換性レベル](../../relational-databases/databases/view-or-change-the-compatibility-level-of-a-database.md)には影響しません。 詳細については、「[ALTER DATABASE 互換性レベル &#40;Transact-SQL&#41;](../../t-sql/statements/alter-database-transact-sql-compatibility-level.md)」を参照してください。   
  
データベースを別のサーバー インスタンスにアタッチするときは、ユーザーおよびアプリケーションに一貫した使用環境を提供するために、アタッチ先のサーバー インスタンスで、ログインやジョブなどのデータベースのメタデータの一部またはすべてを作成し直す必要が生じる場合があります。 詳細については、「[データベースを別のサーバー インスタンスで使用できるようにするときのメタデータの管理 &#40;SQL Server&#41;](../../relational-databases/databases/manage-metadata-when-making-a-database-available-on-another-server.md)」を参照してください。  
  
##  <a name="related-tasks"></a><a name="RelatedTasks"></a> 関連タスク  
**データベースをデタッチするには**  
  
-   [sp_detach_db &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-detach-db-transact-sql.md)  
  
-   [データベースのデタッチ](../../relational-databases/databases/detach-a-database.md)  
  
**データベースをアタッチするには**  
  
-   [CREATE DATABASE &#40;SQL Server Transact-SQL&#41;](../../t-sql/statements/create-database-transact-sql.md)  
  
-   [データベースのアタッチ](../../relational-databases/databases/attach-a-database.md)  
  
-   [sp_attach_db &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-attach-db-transact-sql.md)  
  
-   [sp_attach_single_file_db &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql.md)  
  
**デタッチとアタッチを使用してデータベースをアップグレードするには**  
  
-   [デタッチとアタッチを使用したデータベースのアップグレード &#40;Transact-SQL&#41;](../../relational-databases/databases/upgrade-a-database-using-detach-and-attach-transact-sql.md)  
  
**デタッチとアタッチを使用してデータベースを移動するには**  
  
-   [デタッチとアタッチを使用してデータベースを移動する方法 &#40;Transact-SQL&#41;](../../relational-databases/databases/move-a-database-using-detach-and-attach-transact-sql.md)  
  
**データベース スナップショットを削除するには**  
  
-   [データベース スナップショットの削除 &#40;Transact-SQL&#41;](../../relational-databases/databases/drop-a-database-snapshot-transact-sql.md)  
  
## <a name="see-also"></a>参照  
 [データベース ファイルとファイル グループ](../../relational-databases/databases/database-files-and-filegroups.md)  
  
