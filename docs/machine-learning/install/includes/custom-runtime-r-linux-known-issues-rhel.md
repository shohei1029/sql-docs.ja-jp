---
ms.prod: sql
ms.technology: machine-learning-services
ms.date: 03/18/2021
ms.topic: include
author: dphansen
ms.author: davidph
ms.openlocfilehash: 60759c204b1070561fa43337d6feccb36ae95b5f
ms.sourcegitcommit: efce0ed7d1c0ab36a4a9b88585111636134c0fbb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104833770"
---
## <a name="known-issues"></a>既知の問題

**libstdc++.so.6** が正しいバージョンではない場合、次のエラーが表示されます。

*Exthost: Load extension failed /lib64/libstdc++.so.6: version `GLIBCXX_3.4.20' not found (required by /home/mssql_satellite/externallanguagessandboxpath/libRExtension.so.1.1)* (Exthost: 拡張機能の読み込みに失敗しました /lib64/libstdc++.so.6: (/home/mssql_satellite/externallanguagessandboxpath/libRExtension.so.1.1 で必要な) バージョン 'GLIBCXX_3.4.20' が見つかりませんでした)
