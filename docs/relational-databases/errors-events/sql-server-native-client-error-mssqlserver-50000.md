---
title: MSSQLSERVER_50000 | Microsoft Docs
description: SQL Server Native Client をインストールまたは更新しようとしましたが、エラーが発生しました。 エラーの説明と、考えられる解決策をご確認ください。
ms.custom: ''
ms.date: 04/04/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: supportability
ms.topic: reference
helpviewer_keywords:
- 50000 [SQL Server Native Client setup error]
ms.assetid: 5426d87a-d5d9-4984-b211-b07d69e834a2
author: MashaMSFT
ms.author: mathoma
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 32015c0727ec44bd802ddb5f7cf5eb4c35ab2ea8
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104735031"
---
# <a name="error-mssqlserver_50000-in-sql-server-native-client"></a>SQL Server Native Client でのエラー MSSQLSERVER_50000 
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

    
## <a name="details"></a>詳細  
  
| 属性 | 値 |  
| :-------- | :---- |  
|製品名|SQL Server|  
|製品バージョン|11.0|  
|イベント ID|50000|  
|イベント ソース|SETUP|  
|コンポーネント|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client|  
|シンボル名||  
|メッセージ テキスト|ファイル '%.*ls' の読み取り中にネットワーク エラーが発生しました。|  
  
## <a name="explanation"></a>説明  
 名前が sqlncli.msi から変更された MSI ファイルを使用して [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client がインストールされているコンピューターで、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client をインストール (または更新) しようとしました。  
  
## <a name="user-action"></a>ユーザーの操作  
 このエラーを解決するには、既存のバージョンの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client をアンインストールします。 このエラーを防ぐには、sqlncli.msi 以外の名前の MSI ファイルから [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client をインストールしないようにします。  
  
## <a name="internal-only"></a>内部使用のみ  
  
