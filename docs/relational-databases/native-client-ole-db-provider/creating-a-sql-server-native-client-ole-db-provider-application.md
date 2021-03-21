---
description: SQL Server Native Client OLE DB プロバイダー アプリケーションの作成
title: OLE DB アプリを作成する
ms.custom: ''
ms.date: 03/17/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- SQL Server Native Client OLE DB provider, application creation
- applications [SQL Server Native Client]
- OLE DB, creating applications
ms.assetid: f3ae6815-f32d-4913-a1a2-2ba2f20cfd88
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 0a15d78c5e1a3f53a812eb13d94eded070f6b3e1
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104753692"
---
# <a name="creating-a-sql-server-native-client-ole-db-provider-application"></a>SQL Server Native Client OLE DB プロバイダー アプリケーションの作成
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  Native Client OLE DB プロバイダーアプリケーションを作成するには、次 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の手順を実行します。  
  
1.  データ ソースへの接続の確立。  
  
2.  コマンドの実行。  
  
3.  結果の処理。  

> [!NOTE]  
>  可能な場合は、Windows 認証を使用します。 Windows 認証が使用できない場合は、実行時に資格情報を入力するようユーザーに求めます。 資格情報をファイルに保存するのは避けてください。 資格情報を保存する必要がある場合は、[Win32 CryptoAPI](/windows/win32/seccng/cng-portal) を使用して暗号化してください。  
  
## <a name="in-this-section"></a>このセクションの内容  
  
-   [データ ソースへの接続の確立](../../relational-databases/native-client-ole-db-provider/establishing-a-connection-to-a-data-source.md)  
  
-   [コマンドの実行](../../relational-databases/native-client-ole-db-provider/executing-a-command.md)  
  
-   [結果の処理](../../relational-databases/native-client-ole-db-provider/processing-results.md)  
  
-   [OLE DB プロパティについて](../../relational-databases/native-client-ole-db-provider/about-ole-db-properties.md)  
  
-   [SQL Server Native Client の OLE DB での OUTPUT 句の使用](../../relational-databases/native-client-ole-db-provider/using-the-output-clause-with-ole-db-in-sql-server-native-client.md)  
  
## <a name="see-also"></a>参照  
 [SQL Server Native Client &#40;OLE DB&#41;](../../relational-databases/native-client/ole-db/sql-server-native-client-ole-db.md)  
  
