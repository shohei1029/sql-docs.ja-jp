---
title: 可用性レプリカが可用性グループに参加していない
description: Always On 可用性グループにレプリカが参加していないことに対して考えられる理由を特定する方法を学習します。
ms.custom: seodec18
ms.date: 05/17/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: troubleshooting
f1_keywords:
- sql13.swb.agdashboard.arp4joined.issues.f1
helpviewer_keywords:
- Availability Groups [SQL Server], policies
ms.assetid: 9c0d10b1-9e12-430c-83b9-ca2bd0a3afc4
author: cawrites
ms.author: chadam
ms.openlocfilehash: 2165e226e8b599aed5e1ecbf95c68eda3d86278a
ms.sourcegitcommit: 524a0f0cc9533188f4b14d2e78ba1cfe816b3b9a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2021
ms.locfileid: "105633082"
---
# <a name="availability-replica-is-not-joined-to-an-always-on-availability-group"></a>可用性レプリカが Always On 可用性グループに参加していない
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]
    
## <a name="introduction"></a>はじめに  
  
- **ポリシー名**: 可用性レプリカの結合状態
- **問題**: 可用性レプリカが参加していません。
- **カテゴリ**: **警告**
- **ファセット**: 可用性レプリカ  
  
## <a name="description"></a>説明  
 このポリシーは、可用性レプリカの参加状態をチェックします。 可用性レプリカが可用性グループに追加されていても、適切に参加していない場合、ポリシーは異常な状態です。 それ以外の場合、ポリシーは正常な状態です。  
  
## <a name="possible-causes"></a>考えられる原因  
 セカンダリ レプリカが可用性グループに参加していません。 可用性レプリカを可用性グループに正しく参加させるには、参加状態が Joined Standalone Instance (1) または Joined Failover Cluster (2) である必要があります。  
  
## <a name="possible-solution"></a>考えられる解決方法  
 Transact-SQL、PowerShell、または SQL Server Management Studio を使用して、セカンダリ レプリカを可用性グループに参加させます。 セカンダリ レプリカを可用性グループに参加させる方法の詳細については、「 [可用性グループへのセカンダリ レプリカの参加 (SQL Server)](https://msdn.microsoft.com/library/ff878473\(SQL.110\).aspx)」をご覧ください。  
  
## <a name="see-also"></a>参照  
 [AlwaysOn 可用性グループの概要 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server.md)   
 [AlwaysOn ダッシュボードの使用 &#40;SQL Server Management Studio&#41;](../../../database-engine/availability-groups/windows/use-the-always-on-dashboard-sql-server-management-studio.md)  
  
  
