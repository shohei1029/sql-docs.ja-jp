---
description: コマンド構文 (Native Client OLE DB プロバイダー)
title: コマンド構文 (Native Client OLE DB プロバイダー) |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- SQL Server Native Client OLE DB provider, commands
- commands [OLE DB]
- SQL Server Native Client OLE DB provider, stored procedures
- stored procedures [OLE DB], command syntax
ms.assetid: d463d3d7-e5cb-426d-8e92-aa29980356b6
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: bfb37f5c5b64bfc741cd7182bc6f3958134bab70
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104737462"
---
# <a name="sql-server-native-client-command-syntax"></a>SQL Server Native Client コマンドの構文
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]Native Client OLE DB プロバイダーは、DBGUID_SQL マクロで指定されたコマンド構文を認識します。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]Native Client OLE DB プロバイダーの場合、指定子は、ODBC SQL、ISO、およびの混在が有効な構文であることを示し [!INCLUDE[tsql](../../includes/tsql-md.md)] ます。 たとえば、次の SQL ステートメントでは、ODBC SQL のエスケープ シーケンスを使用して、LCASE 文字列関数を指定しています。  
  
```  
SELECT customerid={fn LCASE(CustomerID)} FROM Customers  
```  
  
 LCASE 関数は、大文字をすべて小文字に変換した文字列を返します。 ISO の文字列関数 LOWER も同じ操作を実行します。つまり、次の SQL ステートメントは、上記の ODBC ステートメントと同義の ISO ステートメントになります。  
  
```  
SELECT customerid=LOWER(CustomerID) FROM Customers  
```  
  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]Native Client OLE DB プロバイダーは、コマンドのテキストとして指定した場合に、ステートメントのいずれかの形式を正常に処理します。  
  
## <a name="stored-procedures"></a>ストアド プロシージャ  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]Native Client OLE DB provider コマンドを使用してストアドプロシージャを実行する場合は、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] コマンドテキストで ODBC CALL エスケープシーケンスを使用します。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]次に、Native Client OLE DB プロバイダーは、のリモートプロシージャコール機構を使用して、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] コマンド処理を最適化します。 たとえば次のような場合、[!INCLUDE[tsql](../../includes/tsql-md.md)] ステートメント形式ではなく、ODBC SQL ステートメントをコマンド テキストとして使用することをお勧めします。  
  
-   ODBC SQL  
  
    ```  
    {call SalesByCategory('Produce', '1995')}  
    ```  
  
-   Transact-SQL  
  
    ```  
    EXECUTE SalesByCategory 'Produce', '1995'  
    ```  
  
## <a name="see-also"></a>参照  
 [コマンド](../../relational-databases/native-client-ole-db-commands/commands.md)  
  
  
