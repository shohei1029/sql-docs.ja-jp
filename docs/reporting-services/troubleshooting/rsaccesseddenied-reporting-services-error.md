---
title: rsAccessedDenied - Reporting Services エラー | Microsoft Docs
description: "このエラー リファレンスでは、\"rsAccessedDenied\" について説明します: ユーザー 'mydomain\\myAccount' には、この操作を行うのに必要な権限が与えられていません。"
ms.date: 05/22/2019
ms.prod: reporting-services
ms.prod_service: reporting-services-native
ms.technology: troubleshooting
ms.topic: conceptual
helpviewer_keywords:
- rsAccessDenied error
ms.assetid: 2f76b1bf-96a2-4755-b76b-84e933220efc
author: maggiesMSFT
ms.author: maggies
ms.openlocfilehash: 20d213cac8f3d3413266dc410b0d66879e771619
ms.sourcegitcommit: 295b9dfc758471ef7d238a2b0f92f93e34acbb1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106054185"
---
# <a name="rsaccesseddenied---reporting-services-error"></a>rsAccessedDenied - Reporting Services エラー
  [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] エラー **rsAccessedDenied** は、操作を実行する権限がユーザーに与えられていない場合に発生します。 たとえば、レポートを開くためのロールが割り当てられていない場合や、必要な権限を使用してブラウザーを開かなかった場合などです。  
  
||  
|-|  
|**[!INCLUDE[applies](../../includes/applies-md.md)]** [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] ネイティブ モード &#124; SharePoint モード|  
  
- URL を指定してレポート サーバーへ直接アクセスしているときにこのエラーが発生した場合は、HTTP 401 エラーに例外がマップされます。  
  
- Web ポータルの使用中にエラーが発生した場合、その例外は通常、HTTP 401 エラーか、その他の定義済みの HTML エラー ページにマップされます。  
  
- スケジュール設定された操作、サブスクリプション、または配信中に発生した場合は、レポート サーバーのログ ファイルにのみ記録されます。  
  
## <a name="details"></a>詳細  
  
|詳細|値|  
|-|-|  
|**製品名**|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]|  
|**イベント ID**|rsAccessedDenied|  
|**イベント ソース**|Microsoft.ReportingServices.Diagnostics.Utilities.ErrorStrings|  
|**コンポーネント**|[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]|  
|**メッセージ テキスト**|ユーザー 'mydomain\myAccount' には、この操作を行うのに必要な権限が与えられていません。 (rsAccessDenied) (ReportingServicesLibrary)。|  
  
## <a name="user-action"></a>ユーザー アクション  
 レポート サーバーのコンテンツや操作にアクセスする権限は、ロールの割り当てによって与えられます。 新規インストールを実行した時点では、ローカル管理者のみがレポート サーバーにアクセスできます。 他のユーザーがレポート サーバーにアクセスできるようにするには、ローカル管理者が、ドメインのユーザー アカウントやグループ アカウントを指定するロールの割り当て、ユーザーが実行できるタスクを定義する 1 つ以上のロール、およびスコープ (通常は、レポート サーバー フォルダー階層のルート ノードである [ホーム] フォルダー) を作成する必要があります。 Web ポータルを使ってロールの割り当てを作成することができます。 詳細については、「[ロールの割り当て](../../reporting-services/security/role-assignments.md)」をご覧ください。  
  
 このエラーは、レポート サーバーのローカル管理が原因で発生することもあります。 詳細については、「 [ローカル管理用のネイティブ モードのレポート サーバー &#40;SSRS&#41; の構成](../../reporting-services/report-server/configure-a-native-mode-report-server-for-local-administration-ssrs.md)」を参照してください。  
  
## <a name="see-also"></a>関連項目  
 [ロールの割り当て](../../reporting-services/security/role-assignments.md)  
 [ネイティブ モードのレポート サーバーに対する権限の許可](../../reporting-services/security/granting-permissions-on-a-native-mode-report-server.md)  
 [ロールと権限 &#40;Reporting Services&#41;](../../reporting-services/security/roles-and-permissions-reporting-services.md)  
  