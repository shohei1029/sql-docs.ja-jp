---
title: サーバーのプロパティ ([詳細設定] ページ) | Microsoft Docs
description: SQL Server のサーバーの詳細設定について説明します。 FILESTREAM オプション、さまざまなしきい値、フルテキスト アップグレードの代替方法、その他の設定について説明します。
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: high-availability
ms.reviewer: ''
ms.technology: configuration
ms.topic: conceptual
f1_keywords:
- sql13.swb.serverproperties.advanced.f1
ms.assetid: cc5e65c2-448e-4f37-9ad4-2dfb1cc84ebe
author: markingmyname
ms.author: maghan
ms.openlocfilehash: 5df2313ce8dc5067828b8d21dc416694b2c17853
ms.sourcegitcommit: 00af0b6448ba58e3685530f40bc622453d3545ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104673530"
---
# <a name="server-properties---advanced-page"></a>サーバーのプロパティ - [詳細設定] ページ
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
  このページを使用すると、サーバーの詳細設定を表示または変更できます。  
  
 **[サーバーのプロパティ] ページを表示するには**  
  
-   [サーバー プロパティの表示または変更 &#40;SQL Server&#41;](../../database-engine/configure-windows/view-or-change-server-properties-sql-server.md)  
  
## <a name="containment"></a>Containment  
 包含データベースの有効化  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のこのインスタンスが包含データベースを許可するかどうかを示します。 **True** の場合は、包含データベースを作成、復元、およびアタッチできます。 **False** の場合は、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]のこのインスタンスで包含データベースを作成、復元、およびアタッチすることはできません。 包含プロパティを変更すると、データベースのセキュリティに影響する場合があります。 包含データベースを有効にすると、データベース所有者は、この [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]へのアクセスを許可できるようになります。 包含データベースを無効にすると、ユーザーが接続できないようにすることができます。 包含プロパティの影響について調べるには、「 [包含データベース](../../relational-databases/databases/contained-databases.md) 」および「 [包含データベースでのセキュリティのベスト プラクティス](../../relational-databases/databases/security-best-practices-with-contained-databases.md)」を参照してください。  
  
## <a name="filestream"></a>FILESTREAM  
 **[FILESTREAM アクセス レベル]**  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]のインスタンスでの FILESTREAM サポートの現在のレベルを表示します。 アクセス レベルを変更するには、次の値のいずれかを選択します。  
  
 **Disabled**  
 バイナリ ラージ オブジェクト (BLOB) データをファイル システムに格納できません。 これが既定値です。  
  
 **[有効な Transact-SQL アクセス]**  
 [!INCLUDE[tsql](../../includes/tsql-md.md)]を使用して FILESTREAM データにアクセスできます。ただし、ファイル システムを介してアクセスすることはできません。  
  
 **[有効なフル アクセス]**  
 [!INCLUDE[tsql](../../includes/tsql-md.md)] を使用して FILESTREAM データにアクセスできます。また、ファイル システムを介してアクセスすることもできます。  
  
 FILESTREAM を初めて有効にする場合は、ドライバーを構成するためにコンピューターの再起動が必要になることがあります。  
  
 **[FILESTREAM 共有名]**  
 セットアップ中に選択された FILESTREAM 共有の読み取り専用の名前を表示します。 詳細については、「[FILESTREAM &#40;SQL Server&#41;](../../relational-databases/blob/filestream-sql-server.md)」をご覧ください。  
  
## <a name="miscellaneous"></a>その他  
 **[トリガーから他のトリガーの起動を許可する]**  
 トリガーから他のトリガーを起動できるようにします。 トリガーは 32 レベルまで入れ子にできます。 詳細については、「[CREATE TRIGGER &#40;Transact-SQL&#41;](../../t-sql/statements/create-trigger-transact-sql.md)」の「入れ子にされたトリガー」をご覧ください。  
  
 **[ブロックされたプロセスのしきい値]**  
 ブロックされたプロセスのレポートを生成するためのしきい値 (秒単位) です。 しきい値は 0 ～ 86,400 の範囲で設定できます。 既定では、ブロックされているプロセスのレポートは生成されません。 詳細については、「 [blocked process threshold サーバー構成オプション](../../database-engine/configure-windows/blocked-process-threshold-server-configuration-option.md)」を参照してください。  
  
 **[カーソルのしきい値]**  
 カーソル キーセットが非同期に生成されるカーソル セット内の行数を指定します。 カーソルが結果セットのキーセットを生成するとき、その結果セットに返される行数をクエリ オプティマイザーが予測します。 返される行数がこのしきい値を超えていると予測された場合、カーソルは非同期に生成されます。これにより、ユーザーはカーソルの作成が続行されている間に行を取り出すことができます。 返される行数がこのしきい値以下と予測された場合、カーソルは同期をとって生成され、すべての行が返されるまでクエリが待機します。  
  
 -1 に設定すると、すべてのキーセットが同期をとって生成されます。これはカーソル セットが小さい場合に役立ちます。 0 に設定すると、すべてのカーソル キーセットが非同期に生成されます。 それ以外の値に設定した場合、クエリ オプティマイザーはカーソル セットの予測行数を比較し、設定された値を超えていると、キーセットを非同期に生成します。 詳細については、「 [cursor threshold サーバー構成オプションの構成](../../database-engine/configure-windows/configure-the-cursor-threshold-server-configuration-option.md)」を参照してください。  
  
 **[既定のフルテキスト言語]**  
 フルテキスト インデックス列に、既定の言語を指定します。 フルテキスト インデックス データの言語の分析は、データの言語に依存します。 このオプションの既定値は、サーバーの言語です。 表示される設定に対応する言語については、「[sys.fulltext_languages &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-fulltext-languages-transact-sql.md)」をご覧ください。  
  
 **[既定の言語]**  
 他に指定しない限り、新しいすべてのログインの既定の言語になります。  
  
 **フルテキスト アップグレード オプション**  
 データベースを [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)]からアップグレードする際のフルテキスト インデックスの移行方法を制御します。 このプロパティは、データベースのインポート、データベース バックアップの復元、ファイル バックアップの復元、またはデータベース コピー ウィザードを使用したデータベースのコピーによってアップグレードする場合に適用されます。  
  
 選択肢は次のとおりです。  
  
 **[インポート]**  
 フルテキスト カタログがインポートされます。 この操作は、 **[再構築]** の場合よりも大幅に時間が短縮されます。 ただし、インポートされたフルテキスト カタログでは、 [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)]で導入された新しい拡張機能であるワード ブレーカーが使用されません。 したがって、最終的に、フルテキスト カタログを再構築する可能性があります。  
  
 フルテキスト カタログが使用できない場合は、関連付けられたフルテキスト インデックスが再構築されます。 このオプションは [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] データベースでのみ使用できます。  
  
 **リビルド**  
 フルテキスト カタログは、導入された新しい拡張機能であるワード ブレーカーを使用して再構築されます。 インデックスの再構築には時間がかかり、アップグレード後にかなりの量の CPU とメモリが必要になる可能性があります。  
  
 **リセット**  
 フルテキスト カタログがリセットされます。 [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] のフルテキスト カタログ ファイルは削除されますが、フルテキスト カタログのメタデータおよびフルテキスト インデックスは保持されます。 アップグレード後、すべてのフルテキスト インデックスで変更の追跡は無効化されており、クロールは自動的には開始されません。 アップグレードの完了後、手動で完全作成を実行するまで、カタログは空のままになります。  
  
 フルテキスト アップグレード オプションを選択する方法については、「 [フルテキスト検索のアップグレード](../../relational-databases/search/upgrade-full-text-search.md)」を参照してください。  
  
> [!NOTE]  
>  フルテキスト アップグレード オプションは、 [sp_fulltext_service](../../relational-databases/system-stored-procedures/sp-fulltext-service-transact-sql.md)の upgrade_option 操作を使用して設定することもできます。  
  
 [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] のデータベースをアタッチ、復元、またはコピーした直後にデータベースは使用可能となり、自動的にアップグレードされます。 データベースにフルテキスト インデックスがある場合、アップグレード プロセスでは、 **"フルテキスト アップグレード オプション"** サーバー プロパティの設定に応じて、インポート、リセット、または再構築が行われます。 アップグレード オプションが **[インポート]** または **[再構築]** に設定されている場合、アップグレード中はフルテキスト インデックスを使用できなくなります。 インデックスを作成するデータ量によって、インポートには数時間、再構築には最大でその 10 倍の時間がかかることがあります。 なお、アップグレード オプションが **[インポート]** に設定されており、フルテキスト カタログが使用できない場合は、関連付けられたフルテキスト インデックスが再構築されます。 **フルテキスト アップグレード オプション** プロパティの設定の表示と変更については、「 [サーバー インスタンスでのフルテキスト検索の管理と監視](../../relational-databases/search/manage-and-monitor-full-text-search-for-a-server-instance.md)」を参照してください。  
  
 **[テキスト レプリケーションの最大サイズ]**  
 単一の INSERT、UPDATE、WRITETEXT、または UPDATETEXT の各ステートメント内のレプリケート対象列またはキャプチャ対象列に追加できる **text** 型、 **ntext** 型、 **varchar(max)** 型、 **nvarchar(max)** 型、 **xml** 型、 **image** 型のデータの最大サイズ (バイト単位) を指定します。 設定を変更すると即座に反映されます。 詳細については、「 [max text repl size サーバー構成オプションの構成](../../database-engine/configure-windows/configure-the-max-text-repl-size-server-configuration-option.md)」を参照してください。  
  
 **[スタートアップ プロシージャのスキャン]**  
 スタートアップ時のストアド プロシージャの自動実行が [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] でスキャンされるように指定します。 **[True]** に設定した場合、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] は、サーバーに定義された自動実行のストアド プロシージャをすべてスキャンして実行します。 **[False]** (既定) に設定された場合、スキャンは実行されません。 詳細については、「 [scan for startup procs サーバー構成オプションの構成](../../database-engine/configure-windows/configure-the-scan-for-startup-procs-server-configuration-option.md)」を参照してください。  
  
 **[2 桁表記の年の基準になる年]**  
 年を 2 桁で入力する場合の最大年が示されます。 表示された年からさかのぼって 99 年間を 2 桁で入力できます。 その他の年はすべて 4 桁で入力する必要があります。  
  
 たとえば、既定の設定が 2049 の場合、「3/14/49」と入力した日付は 2049 年 3 月 14 日として解釈され、「3/14/50」と入力した日付は 1950 年 5 月 14 日と解釈されます。 詳細については、「 [two digit year cutoff サーバー構成オプションの構成](../../database-engine/configure-windows/configure-the-two-digit-year-cutoff-server-configuration-option.md)」を参照してください。  
  
## <a name="network"></a>ネットワーク  
 **[ネットワーク パケット サイズ]**  
 ネットワーク全体で使用されるパケット サイズ (バイト単位) を設定します。 既定のパケット サイズは 4,096 バイトです。 アプリケーションで一括コピー操作を行ったり、大量の **text** 型または **image** 型のデータを送受信したりする場合は、パケット サイズを既定値より大きくすると、ネットワークの読み取りと書き込みが少なくなるので効率が向上します。 アプリケーションで送受信する情報量が少ない場合は、パケット サイズを 512 バイトに設定できます。これは、ほとんどのデータ転送に対して十分なサイズです。 詳細については、「 [network packet size サーバー構成オプションの構成](../../database-engine/configure-windows/configure-the-network-packet-size-server-configuration-option.md)」を参照してください。  
  
> [!NOTE]  
>  パフォーマンスの向上が明確でない限り、パケット サイズは変更しないでください。 多くのアプリケーションでは、既定のパケット サイズが最適です。  
  
 **[リモート ログイン タイムアウト]**  
 失敗したリモート ログインの試行から戻るまでの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の待ち秒数を指定します。 この設定は、異種クエリ用に作成された OLE DB プロバイダーへの接続に適用されます。 既定値は 20 秒です。 0 に設定すると、待ち時間は無制限になります。 詳細については、「 [remote login timeout サーバー構成オプションの構成](../../database-engine/configure-windows/configure-the-remote-login-timeout-server-configuration-option.md)」を参照してください。  
  
 設定を変更すると即座に反映されます。  
  
## <a name="parallelism"></a>並行処理:  
 **[並列処理のコストしきい値]**  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] がクエリの並列プランを作成して実行する場合のしきい値を指定します。 コストとは、特定のハードウェア構成で、直列プランを実行するための予想所要時間を秒単位で表したものです。 symmetric multiprocessors の場合のみ設定してください。 詳細については、「 [cost threshold for parallelism サーバー構成オプションの構成](../../database-engine/configure-windows/configure-the-cost-threshold-for-parallelism-server-configuration-option.md)」を参照してください。  
  
 **Locks**  
 使用できるロックの最大数を設定します。これにより、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] で使用されるメモリの量が制限されます。 既定値は 0 です。0 の場合、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] は、システム要件の変更に基づいてロックを動的に割り当てたり、割り当て解除したりできます。  
  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] が動的にロックを割り当てるように構成することをお勧めします。 詳細については、「 [locks サーバー構成オプションの構成](../../database-engine/configure-windows/configure-the-locks-server-configuration-option.md)」を参照してください。  
  
 **[並列処理の最大限度]**  
 並列プランの実行で使用されるプロセッサの数を制限します (最大 64 まで)。 既定値の 0 の場合は、使用できるプロセッサをすべて使用します。 値 1 の場合は、並列プランの生成を抑制します。 1 より大きい値の場合は、1 つのクエリ実行で使用されるプロセッサの最大数を制限します。 使用可能なプロセッサ数よりも多い値を指定すると、実際に使用可能なプロセッサ数が使用されます。 詳細については、「 [max degree of parallelism サーバー構成オプションの構成](../../database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option.md)」を参照してください。  
  
 **[クエリの待機]**  
 クエリがタイムアウトの前にリソースを待機する秒数 (0 ～ 2,147,483,647) を指定します。既定値の -1 を使用した場合、タイムアウトは、予測されるクエリ コストの 25 倍として計算されます。 詳細については、「 [query wait サーバー構成オプションの構成](../../database-engine/configure-windows/configure-the-query-wait-server-configuration-option.md)」を参照してください。  
  
## <a name="see-also"></a>参照  
 [サーバー構成オプション &#40;SQL Server&#41;](../../database-engine/configure-windows/server-configuration-options-sql-server.md)  
  
  
