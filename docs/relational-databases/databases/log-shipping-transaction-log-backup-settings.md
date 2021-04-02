---
description: '[トランザクション ログのバックアップの設定]'
title: '[トランザクション ログのバックアップの設定] | Microsoft Docs'
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: ''
ms.topic: conceptual
f1_keywords:
- sql13.swb.databaseproperties.logshipping.settings.tlogback.f1
ms.assetid: 9a6e6c16-7f71-412b-bba6-7bffac001277
author: stevestein
ms.author: sstein
ms.openlocfilehash: 00516158d50c2ccc551e038647e38510636a85ed
ms.sourcegitcommit: 0b37eb7aef2f358f80867cd13830dd6683da8d85
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105981098"
---
# <a name="log-shipping-transaction-log-backup-settings"></a>[トランザクション ログのバックアップの設定]
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
  このダイアログ ボックスを使用すると、ログ配布構成のトランザクション ログ バックアップ設定を構成および変更できます。  
  
 ログ配布の概念については、「 [ログ配布について &#40;SQL Server&#41;](../../database-engine/log-shipping/about-log-shipping-sql-server.md)」を参照してください。  
  
## <a name="options"></a>Options  
 **[バックアップ フォルダーのネットワーク パス (例: \\\\primaryserver\\backup)]**  
 このボックスに、バックアップ フォルダーへのネットワーク共有を入力します。 トランザクション ログ バックアップが保存されるローカル フォルダーを共有することにより、ログ配布コピー ジョブでこれらのファイルをセカンダリ サーバーにコピーできます。 セカンダリ サーバー インスタンスでコピー ジョブを実行できるように、このネットワーク共有での読み取り権限をプロキシ アカウントに与えてください。 既定では、このアカウントは、セカンダリ サーバー インスタンスの SQLServer エージェント サービス アカウントですが、管理者はジョブに対して別のプロキシ アカウントを選択できます。  
  
 **[バックアップ フォルダーがプライマリ サーバーに存在する場合は、バックアップ フォルダーのローカル パスを入力 (例: c:\\backup)]**  
 バックアップ フォルダーがプライマリ サーバーにある場合は、ローカル ドライブ名およびバックアップ フォルダーへのパスを入力します。 バックアップ フォルダーがプライマリ サーバーにない場合は、空白のままにしておきます。  
  
 ここにローカル パスを指定すると、BACKUP コマンドはこのパスを使用して、トランザクション ログ バックアップを作成します。ローカル パスを指定しなかった場合、BACKUP コマンドは **[バックアップ フォルダーのネットワーク パス (例: \\\\primaryserver\\backup)]** ボックスで指定されたネットワーク パスを使用します。  
  
> [!NOTE]  
>  実行中の [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] サービス アカウントがプライマリ サーバーのローカル システム アカウントの場合は、バックアップ フォルダーをプライマリ サーバーに作成し、そのフォルダーのローカル パスをここに入力する必要があります。 プライマリ サーバー インスタンスの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] サービス アカウントには、このフォルダーの読み取り権限と書き込み権限が必要です。  
  
 **[次の期間を経過したファイルを削除する]**  
 トランザクション ログ バックアップを削除する前に、バックアップ ディレクトリに保持する期間を指定します。  
  
 **[バックアップが次の期間内に行われない場合は警告する]**  
 トランザクション ログ バックアップが発生していないという警告を生成する前に、ログ配布が待機する期間を指定します。  
  
 **ジョブ名**  
 ログ配布用のトランザクション ログ バックアップを作成する際に使用される SQL Server エージェント ジョブの名前を表示します。 最初にジョブを作成するときに、ボックスに別の名前を入力して名前を変更できます。  
  
 **[スケジュール]**  
 プライマリ データベースのトランザクション ログのバックアップに関する現在のスケジュールを表示します。 バックアップ ジョブが作成される前に、 **[スケジュール]** をクリックしてこのスケジュールを変更できます。バックアップ ジョブが作成された後は、 **[ジョブの編集]** をクリックしてこのスケジュールを変更できます。  
  
### <a name="backup-job"></a>バックアップ ジョブ  
 **[スケジュール]**  
 SQL Server エージェント ジョブの作成時に作成されたスケジュールを変更します。  
  
 **[ジョブの編集]**  
 プライマリ データベースのトランザクション ログ バックアップを実行するジョブの SQL Server エージェント ジョブ パラメーターを変更します。  
  
 **[このジョブを無効にする]**  
 SQL Server エージェント ジョブのトランザクション ログ バックアップ作成を無効にします。  
  
### <a name="compression"></a>圧縮  
 [!INCLUDE[ssEnterpriseEd10](../../includes/ssenterpriseed10-md.md)] (またはそれ以降のバージョン) では、 [バックアップの圧縮](../../relational-databases/backup-restore/backup-compression-sql-server.md)がサポートされています。  
  
 **[バックアップの圧縮の設定]**  
 [!INCLUDE[ssEnterpriseEd10](../../includes/ssenterpriseed10-md.md)] (またはそれ以降のバージョン) で、このログ配布構成のログ バックアップについて、バックアップの圧縮の値を次の中から 1 つ選択します。  
  
|値|説明|  
|-|-|  
|**[既定のサーバー設定を使用する]**|オンにすると、サーバー レベルの既定値が使用されます。<br /><br /> この既定値は、 **backup compression default** サーバー構成オプションで設定されます。 このオプションの現在の設定を表示する方法については、「 [backup compression default サーバー構成オプションの表示または構成](../../database-engine/configure-windows/view-or-configure-the-backup-compression-default-server-configuration-option.md)」を参照してください。|  
|**[バックアップを圧縮する]**|オンにすると、サーバー レベルの既定値に関係なく、バックアップが圧縮されます。<br /><br /> **\*\* 重要 \*\*** 既定の設定では、圧縮によって CPU 使用率が著しく増加し、圧縮処理によって CPU がさらに消費されるために、同時に実行される操作が悪影響を受ける場合があります。 このため、 [リソース ガバナー](../../relational-databases/resource-governor/resource-governor.md)によって CPU 使用率が制限されるセッションで、優先度の低い圧縮バックアップを作成することができます。 詳細については、「 [リソース ガバナーを使用してバックアップの圧縮による CPU 使用率を制限する方法 &#40;Transact-SQL&#41;](../../relational-databases/backup-restore/use-resource-governor-to-limit-cpu-usage-by-backup-compression-transact-sql.md)」を参照してください。|  
|**[バックアップを圧縮しない]**|オンにすると、サーバー レベルの既定値に関係なく、圧縮されていないバックアップが作成されます。|  
  
## <a name="see-also"></a>参照  
 [SQL Server エージェント ジョブ ステップを作成および管理するユーザーの構成](../../ssms/agent/configure-a-user-to-create-and-manage-sql-server-agent-jobs.md)   
 [ログ配布について &#40;SQL Server&#41;](../../database-engine/log-shipping/about-log-shipping-sql-server.md)  
  
  
