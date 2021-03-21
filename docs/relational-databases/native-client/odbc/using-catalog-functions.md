---
description: カタログ関数の使用
title: カタログ関数の使用 |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- ODBC, functions
- SQLLinkedCatalogs function
- SQL Server Native Client ODBC driver, catalog functions
- SQLLinkedServers function
- catalog functions [ODBC]
- functions [ODBC]
ms.assetid: 7773fb2e-06b5-4c4b-88e9-0ad9132ad273
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: a17a0daef11d854352d339b4ac99182487a2ccd4
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104754402"
---
# <a name="using-catalog-functions"></a>カタログ関数の使用
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  どのようなデータベースであっても、その構造は、データベースに格納されたデータを保持するような構造になっています。 この構造の定義は、権限などその他の情報と共にカタログに保存されます。カタログは、システム テーブルのセットとして実装され、データ辞書と呼ばれることもあります。  
  
 Native Client ODBC ドライバーを使用すると、 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] アプリケーションは odbc カタログ関数を呼び出すことによってデータベース構造を決定できます。 カタログ関数は情報を結果セットとして返す関数で、カタログのシステム テーブルをクエリするカタログ ストアド プロシージャを使用して実装されます。 たとえば、アプリケーションが、システム上のすべてのテーブルに関する情報を含む結果セット、または特定のテーブルが持つすべての列に関する情報を含む結果セットを要求するとします。 この場合、標準の ODBC カタログ関数を使用して、アプリケーションが接続している [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] からカタログ情報を取得します。  
  
 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] は分散クエリをサポートします。分散クエリは、1 つのクエリで複数の異種 OLE DB データ ソースのデータにアクセスするクエリです。 リモートの OLE DB データ ソースへアクセスするための方法として、目的のデータ ソースをリンク サーバーとして定義する方法があります。 これを行うには、 [sp_addlinkedserver](../../../relational-databases/system-stored-procedures/sp-addlinkedserver-transact-sql.md)を使用します。 リンク サーバーを定義すると、このサーバーのオブジェクトを次のような 4 部構成の名前を使用して Transact-SQL ステートメントで参照できるようになります。  
  
 *linked_server_name. object_name*.  
  
 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Native Client ODBC ドライバーでは、リンク サーバーからのカタログ情報の取得に役立つ、次の 2 つのドライバー固有の関数をサポートします。  
  
-   **SQLLinkedServers**  
  
     ローカルサーバーに定義されているリンクサーバーの一覧を返します。  
  
-   **SQLLinkedCatalogs**  
  
     リンク サーバーに含まれるカタログの一覧を返します。  
  
 リンクサーバー名とカタログ名を指定すると、 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Native CLIENT ODBC ドライバーは、 _linked_server_name_ の2部構成の名前を使用してカタログから情報を取得することをサポートし **ます。** 次の ODBC カタログ関数の *CatalogName* の _カタログ_。  
  
-   **SQLColumnPrivileges**  
  
-   **SQLColumns**  
  
-   **SQLPrimaryKeys**  
  
-   **SQLStatistics**  
  
-   **SQLTablePrivileges**  
  
-   **SQLTables**  
  
 2部構成の _linked_server_name_**。**_カタログ_ は、 [SQLForeignKeys](../../../relational-databases/native-client-odbc-api/sqlforeignkeys.md)上の *FKCatalogName* および *PKCatalogName* でもサポートされています。  
  
 SQLLinkedServers と SQLLinkedCatalogs を使用する場合は、次のファイルが必要です。  
  
-   sqlncli.h  
  
     リンク サーバーのカタログ関数の関数プロトタイプと定数定義を含むファイルです。 sqlncli.h を ODBC アプリケーションにインクルードし、アプリケーションのコンパイル時にはこのファイルをインクルード パスに配置しておく必要があります。  
  
-   sqlncli11.lib  
  
     リンカーのライブラリ パスに存在し、リンクされるファイルとして指定する必要があります。 sqlncli11.lib は、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Native Client ODBC ドライバーに付属しています。  
  
-   sqlncli11.dll  
  
     実行時に存在する必要があります。 sqlncli11.dll は、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Native Client ODBC ドライバーに付属しています。  
  
## <a name="see-also"></a>参照  
 [SQL Server Native Client &#40;ODBC&#41;](../../../relational-databases/native-client/odbc/sql-server-native-client-odbc.md)   
 [SQLColumnPrivileges](../../../relational-databases/native-client-odbc-api/sqlcolumnprivileges.md)   
 [SQLColumns](../../../relational-databases/native-client-odbc-api/sqlcolumns.md)   
 [SQLPrimaryKeys](../../../relational-databases/native-client-odbc-api/sqlprimarykeys.md)   
 [SQLTablePrivileges](../../../relational-databases/native-client-odbc-api/sqltableprivileges.md)   
 [SQLTables](../../../relational-databases/native-client-odbc-api/sqltables.md)   
 [SQLStatistics](../../../relational-databases/native-client-odbc-api/sqlstatistics.md)  
  
  
