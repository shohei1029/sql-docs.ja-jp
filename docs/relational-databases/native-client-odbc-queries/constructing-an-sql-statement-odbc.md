---
title: SQL ステートメントの構築 (ODBC) |Microsoft Docs
description: SQL Server Client ODBC ドライバーが SQL ステートメントを処理し、いくつかの Transact-sql ステートメントを解析し、他のデータベースをそのままデータベースに渡す方法について説明します。
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- SQL Server Native Client ODBC driver, statements
- statements [ODBC], constructing
- ODBC applications, statements
ms.assetid: 0acc71e2-8004-4dd8-8592-05c022bdd692
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 4a729c1e9bc89cf1c7d6d911260742811b7eac8b
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104750992"
---
# <a name="constructing-an-sql-statement-odbc"></a>SQL ステートメントの構築 (ODBC)
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  ODBC アプリケーションでは、[!INCLUDE[tsql](../../includes/tsql-md.md)] ステートメントを実行することで、ほぼすべてのデータベース アクセスを行います。 これらのステートメントの形式は、アプリケーションの要件によって異なります。 SQL ステートメントは、次の方法で構築できます。  
  
-   ハードコーディング  
  
     アプリケーションで固定タスクとして実行される静的ステートメントです。  
  
-   実行時に構築  
  
     実行時に構築される SQL ステートメントで、これによりユーザーは SELECT、WHERE、ORDER BY などの一般的な句を使用してステートメントを調整できます。 これには、ユーザーが入力したアドホック クエリも含まれます。  
  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]クライアント odbc ドライバーは、によって直接サポートされていない odbc および ISO 構文に対してのみ SQL ステートメントを解析します。この構文は、ドライバーによって [!INCLUDE[ssDE](../../includes/ssde-md.md)] に変換され [!INCLUDE[tsql](../../includes/tsql-md.md)] ます。 その他すべての SQL 構文は、変更されずに[!INCLUDE[ssDE](../../includes/ssde-md.md)]に渡されます。ここでは、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] が有効な [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] かどうかが判断されます。 この方法には、次の 2 つの利点があります。  
  
-   オーバーヘッドの削減  
  
     ドライバーの処理オーバーヘッドが最小限に抑えられます。これは、スキャンする必要のある ODBC 句および ISO 句の数が少ないためです。  
  
-   柔軟性  
  
     プログラマは、アプリケーションの移植性を調整できます。 複数のデータベースに対する移植性を強化するには、主に ODBC 構文および ISO 構文を使用します。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 固有の強力な機能を使用するには、対応する [!INCLUDE[tsql](../../includes/tsql-md.md)] 構文を使用します。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]Native CLIENT odbc ドライバーは、 [!INCLUDE[tsql](../../includes/tsql-md.md)] odbc ベースのアプリケーションがのすべての機能を利用できるように、完全な構文をサポートしてい [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。  
  
 SELECT ステートメント内の列リストには、現在のタスクを実行するのに必要な列だけを含める必要があります。 これにより、ネットワーク経由で送信されるデータ量が少なくなるだけでなく、アプリケーションに対するデータベース変更の影響も少なくなります。 アプリケーションでテーブルの列を参照していなければ、アプリケーションは、その列に行われる変更の影響を受けません。  
  
## <a name="see-also"></a>参照  
 [ODBC&#41;&#40;クエリの実行 ](../../relational-databases/native-client-odbc-queries/executing-queries-odbc.md)  
  
  
