---
description: SQLCancel
title: SQLCancel |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- SQLCancel function
ms.assetid: d4c965ae-c1ac-4e9d-b4b9-32b561401106
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 64a3a1849524aca952c0710500907f209a42e3d5
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104755132"
---
# <a name="sqlcancel"></a>SQLCancel
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  [SQLCancel](../../odbc/reference/syntax/sqlcancel-function.md)のトピックでは、ODBC 2.x では、ステートメントに対して処理が実行されていないときにアプリケーションが **SQLCancel** を呼び出した場合、 **SQLCancel** は **SQLFreeStmt** と同じ効果を **SQL_CLOSE** 持つということを示しています。この動作は、完全な場合にのみ定義され、アプリケーションは **SQLFreeStmt** または **sqlcloを** 呼び出してカーソルを閉じる必要があります。 ただし、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client アプリケーションが ODBC API バージョンを 3.5. x 以降に設定している場合でも、 **SQLCancel** 関数では odbc 2.x の動作が使用されます。  
  
## <a name="see-also"></a>参照  
 [SQLCancel](../../odbc/reference/syntax/sqlcancel-function.md)   
 [ODBC API 実装の詳細](../../relational-databases/native-client-odbc-api/odbc-api-implementation-details.md)  
  
