---
title: 強力なパスワード | Microsoft Docs
description: SQL Server でのパスワードの概要と、デプロイのセキュリティを強化する強力なパスワードの構成方法について説明します。
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: security
ms.topic: conceptual
helpviewer_keywords:
- logins [SQL Server], passwords
- passwords [SQL Server], strong
- symbols [SQL Server]
- security [SQL Server], passwords
- passwords [SQL Server], symbols
- characters [SQL Server], password policies
- strong passwords [SQL Server]
ms.assetid: 338548f4-c4d8-47ca-b597-5c9c0f2fa205
author: VanMSFT
ms.author: vanto
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 0594f08c28653e6fd118fa70dd4d0a6dd6debeb0
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104739572"
---
# <a name="strong-passwords"></a>強力なパスワード
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]
  パスワードは、サーバーのセキュリティ上で最も弱点になる可能性があります。 パスワードを選択するときは、最大限の注意を払ってください。 強力なパスワードの特徴は次のとおりです。  
  
-   少なくとも 8 文字以上のもの  
  
-   文字、数字、および記号を組み合わせたパスワードである  
  
-   辞書に載っていない  
  
-   コマンド名ではない  
  
-   人名ではない  
  
-   ユーザー名ではない  
  
-   コンピューター名ではない  
  
-   定期的に変更される  
  
-   以前のパスワードと異なる  
  
 [!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のパスワードは、最大 128 文字までで、英字、記号、数字を使用できます。 ログイン、ユーザー名、ロール、およびパスワードは [!INCLUDE[tsql](../../includes/tsql-md.md)] ステートメントで使用されることが多く、特定の記号については二重引用符 (") または角かっこ ([ ]) で囲む必要が生じます。 これらの区切り記号を [!INCLUDE[tsql](../../includes/tsql-md.md)] ステートメントで使用するのは、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のログイン、ユーザー、ロール、またはパスワードに次のような特徴がある場合です。  
  
-   スペースが含まれているか、スペースが先頭にある  
  
-   $ または \@ で始まる  
  
 OLE DB または ODBC の接続文字列で使用する場合、ログインまたはパスワードに次の文字は含めないでください: [] () , ; ? * ! \@ =。 これらの文字は、接続の初期化や、接続の値を区切る場合に使用されています。  
  
## <a name="related-content"></a>関連コンテンツ  
 [パスワード ポリシー](../../relational-databases/security/password-policy.md)  
  
  
