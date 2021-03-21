---
description: SQLCloseCursor
title: 'Sqlclo: |Microsoft Docs'
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
apitype: DLLExport
helpviewer_keywords:
- SQLCloseCursor function
ms.assetid: e7134d65-5c1c-4ae2-b119-d9b4b9a42483
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 996df188ed1b209132977ccee7c5195eba94b829
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104755102"
---
# <a name="sqlclosecursor"></a>SQLCloseCursor
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  **SqlcloSQLFreeStmt** は、 [](../../relational-databases/native-client-odbc-api/sqlfreestmt.md) *オプション* の値を SQL_CLOSE に置き換えます。 Native Client ODBC ドライバーでは、 **Sqlcloの** 受信時に [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 保留中の結果セットの行が破棄されます。 ステートメントの列とパラメーターのバインド (存在する場合) は、 **Sqlcloに** よって変更されないことに注意してください。  
  
## <a name="see-also"></a>参照  
 [SQLCloseCursor]()   
 [ODBC API 実装の詳細](../../relational-databases/native-client-odbc-api/odbc-api-implementation-details.md)  
  
