---
description: SQL Server Native Client メッセージの結果
title: SQL Server メッセージの結果 (Native Client OLE DB プロバイダー)
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- SQL Server Native Client OLE DB provider, errors
- errors [OLE DB], SQL Server message results
- OLE DB error handling, SQL Server message results
ms.assetid: 6663c6f9-def1-4d9e-845b-2085e5efc401
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: c9bfcc73520ddcaf7243099c04af414c8f5aff54
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104738542"
---
# <a name="sql-server-native-client-message-results"></a>SQL Server Native Client メッセージの結果
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  次の [!INCLUDE[tsql](../../includes/tsql-md.md)] ステートメントは、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client OLE DB プロバイダーの行セット、または実行時に影響を受ける行の数を生成しません。  
  
-   PRINT  
  
-   重大度が 10 以下の RAISERROR  
  
-   DBCC  
  
-   SET SHOWPLAN  
  
-   SET STATISTICS  
  
 上記のステートメントでは、行セットや行数の結果が返されるのではなく、ステートメントから 1 つ以上の情報メッセージが返されるか、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] から情報メッセージが返されます。 正常に実行される [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] と、Native client OLE DB プロバイダーは S_OK を返します。メッセージは、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] native client OLE DB プロバイダーコンシューマーが使用できます。  
  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]Native client OLE DB プロバイダーは S_OK を返し、多くのステートメントの実行後、 [!INCLUDE[tsql](../../includes/tsql-md.md)] または [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] native client OLE DB プロバイダーメンバー関数のコンシューマー実行に従って、1つまたは複数の情報メッセージを使用できるようにします。  
  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]Native Client OLE DB プロバイダーコンシューマーは、クエリテキストを動的に指定できるようにするため、リターンコードの値、返される **IRowset** または **IMultipleResults** インターフェイス参照の有無、または影響を受ける行の数に関係なく、すべてのメンバー関数の実行後にエラーインターフェイスをチェックする必要があります。  
  
## <a name="see-also"></a>参照  
 [エラー](../../relational-databases/native-client-ole-db-errors/errors.md)  
  
  
