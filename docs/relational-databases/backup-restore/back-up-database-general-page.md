---
title: '[データベースのバックアップ] ([全般] ページ) | Microsoft Docs'
description: SQL Server では、[データベースのバックアップ] ダイアログ ボックスの [全般] ページを使用して、データベースのバックアップ操作で使用するソースとバックアップ先の設定を表示または変更します。
ms.custom: ''
ms.date: 07/01/2016
ms.prod: sql
ms.prod_service: backup-restore
ms.reviewer: ''
ms.technology: backup-restore
ms.topic: conceptual
f1_keywords:
- sql13.swb.backupdatabase.general.f1
ms.assetid: 5c344dfd-1ad3-41cc-98cd-732973b4a162
author: cawrites
ms.author: chadam
ms.openlocfilehash: d6303aa2a0ee3165ca3a90f466fbb001f6f7dc83
ms.sourcegitcommit: 0b37eb7aef2f358f80867cd13830dd6683da8d85
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105981016"
---
# <a name="back-up-database-general-page"></a>[データベースのバックアップ] \([全般] ページ)
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
  **[データベースのバックアップ]** ダイアログ ボックスの **[全般]** ページでは、データベースのバックアップ操作の設定を表示または変更できます。  
  
 バックアップの基本的な概念については、「[バックアップの概要 &#40;SQL Server&#41;](../../relational-databases/backup-restore/backup-overview-sql-server.md)」を参照してください。  
  
> [!NOTE]  
>  [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] を使用してバックアップ タスクを指定する場合、 **[スクリプト]** ボタンをクリックしてスクリプトの保存先を選択することにより、対応する [!INCLUDE[tsql](../../includes/tsql-md.md)] の [BACKUP](../../t-sql/statements/backup-transact-sql.md) スクリプトを生成できます。  
  
 **SQL Server Management Studio を使用してバックアップを作成するには**  
  
-   [データベースの完全バックアップの作成 &#40;SQL Server&#41;](../../relational-databases/backup-restore/create-a-full-database-backup-sql-server.md)  
  
-   [データベースの差分バックアップの作成 &#40;SQL Server&#41;](../../relational-databases/backup-restore/create-a-differential-database-backup-sql-server.md)  
  
    > [!IMPORTANT]  
    >  データベース メンテナンス プランを定義して、データベース バックアップを作成できます。 詳細については、 [オンライン ブックの「](../maintenance-plans/maintenance-plans.md) メンテナンス プラン [!INCLUDE[ssKilimanjaro](../../includes/sskilimanjaro-md.md)] 」を参照してください。  
  
 **部分バックアップを作成するには**  
  
-   部分バックアップを作成するには、[!INCLUDE[tsql](../../includes/tsql-md.md)] の [BACKUP](../../t-sql/statements/backup-transact-sql.md) ステートメントで PARTIAL オプションを使用する必要があります。  
  
## <a name="options"></a>Options  
  
### <a name="source"></a>source  
 **[ソース]** パネルのオプションでは、データベースを特定し、バックアップ操作のバックアップの種類とコンポーネントを指定します。  
  
 **[データベース]**  
 バックアップするデータベースを選択します。  
  
 **復旧モデル**  
 選択したデータベースで表示される復旧モデル (SIMPLE、FULL、または BULK_LOGGED) を表示します。  
  
 **バックアップの種類**  
 指定したデータベースで実行するバックアップの種類を選択します。  
  
|バックアップの種類|適用対象|制限|  
|-----------------|-------------------|------------------|  
|[完全]|データベース、ファイル、ファイル グループ|**master** データベースでは、完全バックアップのみ可能です。<br /><br /> SIMPLE (単純) 復旧モデルの場合、ファイルおよびファイル グループのバックアップは読み取り専用ファイル グループについてのみ実行できます。|  
|差分|データベース、ファイル、ファイル グループ|SIMPLE (単純) 復旧モデルの場合、ファイルおよびファイル グループのバックアップは読み取り専用ファイル グループについてのみ実行できます。|  
|トランザクション ログ|トランザクション ログ|トランザクション ログ バックアップは、単純復旧モデルでは使用できません。|  
  
 **[バックアップのみコピーする]**  
 コピーのみのバックアップを作成する場合に選択します。 *コピーのみのバックアップ* は、従来の [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] バックアップのシーケンスから独立した [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] バックアップです。 詳細については、「[コピーのみのバックアップ &#40;SQL Server&#41;](../../relational-databases/backup-restore/copy-only-backups-sql-server.md)」を参照してください。  
  
> [!NOTE]  
>  **[差分]** オプションが選択されている場合、コピーのみのバックアップは作成できません。  
  
 **[バックアップ コンポーネント]**  
 バックアップするデータベース コンポーネントを選択します。 **[バックアップの種類]** ボックスの一覧で **[トランザクション ログ]** を選択した場合は、このオプションを設定できません。  
  
 次のいずれかのオプション ボタンをクリックします。  
  
|オプション|説明|  
|-|-|  
|**[データベース]**|データベース全体がバックアップされるように指定します。|  
|**[ファイルおよびファイル グループ]**|指定したファイルやファイル グループがバックアップされるように指定します。<br /><br /> このオプションをクリックすると、 **[ファイルおよびファイル グループの選択]** ダイアログ ボックスが表示されます。 バックアップするファイル グループまたはファイルを選択して **[OK]** をクリックすると、選択した項目が **[ファイルおよびファイル グループ]** ボックスに表示されます。|  
  
### <a name="destination"></a>宛先  
 **[バックアップ先]** パネルのオプションでは、バックアップ操作で使用するバックアップ デバイスの種類を指定して、既存の論理バックアップ デバイスまたは物理バックアップ デバイスを検索できます。  
  
> [!NOTE]  
>  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] バックアップ デバイスの詳細については、「[バックアップ デバイス &#40;SQL Server&#41;](../../relational-databases/backup-restore/backup-devices-sql-server.md)」を参照してください。  
  
 **[バックアップ先]**  
 バックアップ先のメディアの種類を、次の中から 1 つ選択します。 選択したバックアップ先が、 **[バックアップ先]** 一覧に表示されます。  
  
|メディアの種類|説明|  
|-|-|  
|**ディスク**|ディスクにバックアップします。 データベース用に作成されたシステム ファイルやディスク ベースの論理バックアップ デバイスを指定する場合もあります。 現在選択されているディスクが、 **[バックアップ先]** 一覧に表示されます。 バックアップ操作には最大 64 台のディスク デバイスを選択できます。|  
|**[テープ]**|テープにバックアップします。 データベース用に作成されたローカル テープ ドライブやテープ ベースの論理バックアップ デバイスを指定する場合もあります。 現在選択されているテープが、 **[バックアップ先]** リストに表示されます。 最大数は 64 です。 サーバーにテープ デバイスが接続されていない場合、このオプションは無効になります。 選択したテープが **[バックアップ先]** 一覧に表示されます。<br /><br /> 注:テープ バックアップ デバイスは、将来のバージョンの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]でサポートされなくなる予定です。 新規の開発作業ではこの機能を使用しないようにし、現在この機能を使用しているアプリケーションは修正することを検討してください。|  
|**URL**|Microsoft Azure Blob ストレージにバックアップします。|  
  
 次に示すオプションの表示は、選択したバックアップ先の種類によって異なります。 [ディスク] または [テープ] を選択すると、次のオプションが表示されます。  
  
 **追加**  
 ファイルまたはデバイスを **[バックアップ先]** 一覧に追加します。 ローカル ディスクまたはリモート ディスクの最大 64 個のデバイスで同時にバックアップできます。 リモート ディスクのファイルを指定するには、完全修飾の汎用名前付け規則 (UNC) 名を使用してください。 詳細については、「 [バックアップ デバイス &#40;SQL Server&#41;](../../relational-databases/backup-restore/backup-devices-sql-server.md)」を参照してください。  
 
 
  
 **Remove**  
 **[バックアップ先]** 一覧から、現在選択されているデバイスを削除します。  
  
 **Contents**  
選択したデバイスにメディア コンテンツがある場合、これを表示します。  **URL** が指定されている場合は、機能が実行されません。 
   
**[バックアップ先の選択]** ダイアログ ボックス **[バックアップ先の選択]** ダイアログ ボックスは、 **[追加]** を選択すると表示されます。   オプションの表示は、選択したバックアップ先の種類によって異なります。 

バックアップ先として **[ディスク]** または **[テープ]** を選択すると、次のオプションが表示されます。  

*
  **[ファイル名]**  
    バックアップ ファイルの名前を指定します。

バックアップ先として **[URL]** を選択すると、次のオプションが表示されます。
*
  **[Azure ストレージ コンテナー]**  
  バックアップ ファイルを格納する Windows Azure Storage コンテナーの名前。 
   
*
  **共有アクセス ポリシー:**  
  手動で入力されたコンテナーの Shared Access Signature を入力します。  このフィールドは、既存のコンテナーを選択した場合には使用できません。
  
*
  **バックアップ ファイル:**  
  バックアップ ファイルの名前。

*
  **新しいコンテナー:**  
Shared Access Signature がない既存のコンテナーを登録するために使用します。  「 [Connect to a Microsoft Azure Subscription](../../relational-databases/backup-restore/connect-to-a-microsoft-azure-subscription.md)」(Microsoft Azure Subscription への接続) を参照してください。
  
## <a name="see-also"></a>参照  
 [トランザクション ログのバックアップ &#40;SQL Server&#41;](../../relational-databases/backup-restore/back-up-a-transaction-log-sql-server.md)   
 [ファイルおよびファイル グループのバックアップ &#40;SQL Server&#41;](../../relational-databases/backup-restore/back-up-files-and-filegroups-sql-server.md)   
 [ディスク ファイルの論理バックアップ デバイスの定義 &#40;SQL Server&#41;](../../relational-databases/backup-restore/define-a-logical-backup-device-for-a-disk-file-sql-server.md)   
 [テープ ドライブの論理バックアップ デバイスの定義 &#40;SQL Server&#41;](../../relational-databases/backup-restore/define-a-logical-backup-device-for-a-tape-drive-sql-server.md)   
 [復旧モデル &#40;SQL Server&#41;](../../relational-databases/backup-restore/recovery-models-sql-server.md)  
  
  
