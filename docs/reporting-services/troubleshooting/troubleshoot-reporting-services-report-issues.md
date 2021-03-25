---
title: Reporting Services レポートの問題のトラブルシューティング
description: この記事では、ネイティブ モードまたは SharePoint モードのレポート サーバーでのレポートのデザイン、プレビュー、エクスポート、パブリッシュ、表示に関する問題のトラブルシューティングを行います。
ms.date: 02/27/2016
ms.prod: reporting-services
ms.prod_service: reporting-services-native
ms.technology: troubleshooting
ms.topic: conceptual
ms.assetid: a705d103-85b1-49b5-b27f-332b1040d029
author: maggiesMSFT
ms.author: maggies
ms.openlocfilehash: 8812cfc8ac70da47da0414534ff380dd07686037
ms.sourcegitcommit: bacd45c349d1b33abef66db47e5aa809218af4ea
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104792944"
---
# <a name="troubleshoot--reporting-services-report-issues"></a>Reporting Services レポートの問題のトラブルシューティング
このトピックは、 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion.md)] レポートのデザイン、レポートのプレビュー、ネイティブ モードまたは SharePoint モードのレポート サーバーへのレポートのパブリッシュ、レポート サーバーでのレポートの表示、別のファイル形式へのレポートのエクスポートに関する問題のトラブルシューティングを行ううえで参考になります。  
## <a name="monitor-report-servers"></a>レポート サーバーを監視する  
システム ツールとデータベース ツールを使用して、レポート サーバーの利用状況を監視することができます。 また、レポート サーバーのトレース ログ ファイルを表示したり、レポート サーバーの実行ログで特定のレポートに関する詳細情報を照会したりすることもできます。 パフォーマンス モニターを使用している場合は、レポート サーバー Web サービスと Windows サービスのパフォーマンス カウンターを追加して、要求時の処理やスケジュールされた処理でのボトルネックを特定できます。  
詳細については、「 [レポート サーバーのパフォーマンスの監視](../report-server/monitoring-report-server-performance.md)」を参照してください。  
  
  
## <a name="view-the-report-server-logs"></a>レポート サーバー ログを表示する  
[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion.md)] では、特定のレポート、デバッグ情報、HTTP 要求と HTTP 応答、およびレポート サーバーのイベントに関するデータを記録するログ ファイルに、多数の内部イベントと外部イベントが記録されます。 また、パフォーマンス ログを作成してから、収集するデータを指定するパフォーマンス カウンターを選択することもできます。 既定のインストールでは、ログ ファイルの既定のディレクトリは `<drive>\Program Files\Microsoft SQL Server\MSRS130.MSSQLSERVER\Reporting Services\LogFiles`です。   
  
詳細については、「 [Reporting Services のログ ファイルとソース](../report-server/reporting-services-log-files-and-sources.md)」を参照してください。  
  
レポートの待機が発生している原因がデータの取得なのか、レポートの処理なのか、またはレポートの表示なのかを具体的に特定するには、実行ログを使用します。 詳細については、「[レポート サーバー実行ログと ExecutionLog3 ビュー](../report-server/report-server-executionlog-and-the-executionlog3-view.md)」を参照してください。   
  
## <a name="view-the-call-stack-for-report-processing-error-messages-on-the-report-server"></a>レポート サーバーでレポート処理に関するエラー メッセージの呼び出し履歴を表示する  
パブリッシュされたレポートをレポート マネージャーで表示する際、一般的な処理や表示のエラーを表すエラー メッセージが表示されることがあります。 詳細を確認するには、呼び出し履歴を表示します。   
  
呼び出し履歴を表示するには、レポート サーバーにローカル管理者の資格情報を使ってログオンし、レポート マネージャーのページを右クリックして **[ソースの表示]** をクリックします。 呼び出し履歴には、エラー メッセージの詳細なコンテキストが示されます。  
  
## <a name="use-ssmanstudiofull-to-verify-queries-and-credentials"></a>[!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull.md)] を使用してクエリと資格情報を検証する  
[!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull.md)] を使用すると、複雑なクエリをレポートに含める前に検証できます。   
  
詳細については、「[データベース エンジン クエリ エディター](../../ssms/f1-help/database-engine-query-editor-sql-server-management-studio.md)」と「[オブジェクト エクスプローラーを使用したオブジェクトの管理](../../ssms/object/manage-objects-by-using-object-explorer.md)」を参照してください。  
  
## <a name="analyze-problem-reports-with-report-data-cached-on-the-client"></a>クライアント上にキャッシュされたレポート データを使用して問題のあるレポートを分析する  
レポート作成者が Business Intelligence Development Studio でレポートを作成する場合は、レポート作成クライアントで、レポートのプレビュー時に使用される .rdl.data ファイルとしてデータがキャッシュされます。 キャッシュは、クエリが変更されるたびに更新されます。 レポートに関する問題をデバッグするには、レポート データが更新されないようにしてデバッグ中にデータが変更されることを防ぐと役立つ場合があります。   
  
[!INCLUDE[ssBIDevStudioFull](../../includes/ssbidevstudiofull.md)] で使用できるのがキャッシュ データのみかどうかを制御するには、 [!INCLUDE[ssBIDevStudio](../../includes/ssbidevstudio.md)]内の devenv.exe.config に次のセクションを追加します。 既定のディレクトリの場所は、 `<drive>:Program Files\Microsoft Visual Studio 10.0\Common7\IDE`です。   
  
```  
<system.diagnostics>  
      <switches>  
         <add name="Microsoft.ReportDesigner.ReportPreviewStore.ForceCache" value="1" />  
      </switches>  
   </system.diagnostics>  
```  
値が 1 に設定されていれば、キャッシュされたレポート データのみが使用されます。 レポートのデバッグが完了したら、このセクションを削除してください。  
  
## <a name="see-also"></a>参照  
[エラーとイベント (Reporting Services)](errors-and-events-reference-reporting-services.md)  
  
  

[!INCLUDE[feedback_stackoverflow_msdn_connect](../../includes/feedback-stackoverflow-msdn-connect-md.md)]
