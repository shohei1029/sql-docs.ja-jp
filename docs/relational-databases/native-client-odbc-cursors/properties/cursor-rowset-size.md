---
description: カーソルの行セット サイズ
title: カーソル行セットサイズ |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- cursors [ODBC], rowset size
- ODBC cursors, rowset size
- rowsets [ODBC]
ms.assetid: 2febe2ae-fdc1-490e-a79f-c516bc8e7c3f
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 5d135dc225e33fa28680b0ff7e74cc81346f1642
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104756182"
---
# <a name="cursor-rowset-size"></a>カーソルの行セット サイズ
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  ODBC カーソルでは、一度にフェッチできる行数は制限されません。 **Sqlfetch** または [sqlfetchscroll](../../../relational-databases/native-client-odbc-api/sqlfetchscroll.md)を呼び出すたびに、複数の行を取得できます。 Microsoft [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] のようなクライアント/サーバー型のデータベースで作業しているときは、1 回に複数行を取得する方が効率的です。 フェッチで返される行の数は行セットサイズと呼ばれ、 [SQLSetStmtAttr](../../../relational-databases/native-client-odbc-api/sqlsetstmtattr.md)の SQL_ATTR_ROW_ARRAY_SIZE を使用して指定されます。  
  
```  
SQLUINTEGER uwRowsize;  
SQLSetStmtAttr(m_hstmt, SQL_ATTR_ROW_ARRAY_SIZE, (SQLPOINTER)uwRowsetSize, SQL_IS_UINTEGER);  
```  
  
 行セット サイズが 2 以上のカーソルをブロック カーソルと呼びます。  
  
 ブロック カーソルの結果セット列は、次の 2 つの方法でバインドできます。  
  
-   列方向のバインド  
  
     各列が変数の配列にバインドされます。 各配列には行セット サイズと同じ数の要素が含まれます。  
  
-   行方向のバインド  
  
     1 行のすべての列のデータとインジケーターが含まれる構造体を使用して配列が構築されます。 この配列には行セット サイズと同じ数の構造体が含まれます。  
  
 列方向または行方向のバインドのいずれかを使用すると、 **Sqlfetch** または **sqlfetchscroll** を呼び出すたびに、バインドされた配列に、取得した行セットのデータが格納されます。  
  
 [SQLGetData](../../../relational-databases/native-client-odbc-api/sqlgetdata.md) を使用して、ブロックカーソルから列データを取得することもできます。 **SQLGetData** は一度に1行ずつ動作するため、 **SQLGetData** を呼び出す前に **SQLSetPos** を呼び出して、行セット内の特定の行を現在の行として設定する必要があります。  
  
 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]Native CLIENT ODBC ドライバーでは、行セットを使用して結果セット全体をすばやく取得する最適化が提供されます。 この最適化を使用するには、 **SQLExecDirect** または **sqlexecute** が呼び出されたときに、カーソルの属性を既定値 (順方向専用、読み取り専用、行セットサイズ = 1) に設定します。 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]Native CLIENT ODBC ドライバーは、既定の結果セットを設定します。 スクロールを使用しないでクライアントに結果を送信する場合は、既定の結果セットの方がサーバー カーソルよりも効率的です。 ステートメントを実行後、行セット サイズを増やし、列方向のバインドか行方向のバインドを使用します。 これにより、 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 既定の結果セットを使用してクライアントに結果行を効率的に送信でき [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] ます。一方、NATIVE client ODBC ドライバーでは、クライアントのネットワークバッファーから行を継続的にプルします。  
  
## <a name="see-also"></a>参照  
 [カーソルのプロパティ](../../../relational-databases/native-client-odbc-cursors/properties/cursor-properties.md)  
  
  
