---
description: Unicode 関数の引数
title: Unicode 関数の引数 |Microsoft Docs
ms.custom: ''
ms.date: 01/19/2017
ms.prod: sql
ms.prod_service: connectivity
ms.reviewer: ''
ms.technology: connectivity
ms.topic: conceptual
helpviewer_keywords:
- Unicode [ODBC], functions
- functions [ODBC], Unicode functions
ms.assetid: eafe8c7e-f6d2-44d7-99ee-cf2148a30f4f
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: b91a4880cb39fac38f1eec918e861c7aef25787b
ms.sourcegitcommit: 0b37eb7aef2f358f80867cd13830dd6683da8d85
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105981118"
---
# <a name="unicode-function-arguments"></a>Unicode 関数の引数
ODBC 3.5 (またはそれ以降) のドライバーマネージャーでは、引数に文字列または SQLPOINTER へのポインターを受け取るすべての関数の ANSI バージョンと Unicode バージョンの両方がサポートされています。 Unicode 関数はマクロとしてではなく、関数として実装されます (拡張子は *W*)。 ANSI 関数 (のサフィックスを使用して、または指定しないで呼び出すことができ *ます) は*、現在の ODBC API 関数と同じです。  
  
## <a name="remarks"></a>解説  
 文字列または長さの引数を常に返す、または受け取る Unicode 関数の場合、引数は文字数として渡されます。 サーバーデータの長さの情報を返す関数の場合、表示サイズと有効桁数は文字数で示されます。 長さ (データの転送サイズ) が文字列または文字列以外のデータを参照する場合、長さはオクテット長で記述されます。 たとえば、 **Sqlgetinfow** の長さはバイト数として取得されますが、 **SQLExecDirectW** では文字数が使用されます。  
  
 文字数は、ANSI 関数のバイト数 (オクテット) と、UNICODE 関数の WCHAR の数 (16 ビットワード) を示します。 特に、2バイト文字シーケンス (DBCS) またはマルチバイト文字シーケンス (MBCS) は、複数のバイトで構成できます。 UTF-16 Unicode 文字シーケンスは、複数の WCHARs で構成できます。  
  
 Unicode (W) バージョンと ANSI (A) バージョンの両方をサポートする ODBC API 関数の一覧を次に示します。  
  
:::row:::
   :::column span="":::
      **SQLBrowseConnect**<br>      **SQLColAttribute**<br>      **SQLColAttributes**<br>      **SQLColumnPrivileges**<br>      **SQLColumns** <br>      **SQLConnect** <br>      **SQLDataSources**<br>      **SQLDescribeCol**  <br>      **SQLDriverConnect** <br>      **SQLDrivers** <br>      **SQLError**  <br>      **SQLExecDirect**<br>      **SQLForeignKeys**<br>      **SQLGetConnectAttr** <br>      **SQLGetConnectOption** <br>      **SQLGetCursorName**<br>      **SQLGetDescField** <br>      **SQLGetDescRec** <br>      **SQLGetDiagField**
   :::column-end:::
   :::column span="":::
      **SQLGetDiagRec**        <br>      **SQLGetInfo**        <br>      **SQLGetStmtAttr**<br>      **SQLGetTypeInfo**<br>      **SQLNativeSql**<br>      **SQLPrepare**<br>      **SQLPrimaryKeys**<br>      **SQLProcedureColumns**<br>      **SQLProcedures**<br>      **SQLSetConnectAttr**<br>      **SQLSetConnectOption**<br>      **SQLSetCursorName**<br>      **SQLSetDescField**<br>      **SQLSetStmtAttr**<br>      **SQLSpecialColumns**<br>      **SQLStatistics**<br>      **SQLTablePrivileges**<br>      **SQLTables**
   :::column-end:::
:::row-end:::
  
 次に示すのは、Unicode (W) バージョンと ANSI (A) バージョンの両方をサポートする ODBC インストーラーおよび ODBC 変換関数の一覧です。  
  
:::row:::
   :::column span="":::
      **SQLConfigDataSource**<br>      **SQLCreateDataSource**<br>      **SQLDataSourceToDriver**<br>      **SQLDriverToDataSource**<br>      **Sqlgetがあるドライバー**<br>      **Sqlgetdrivers ドライバー**<br>      **SQLGetTranslator**<br>      **SQLInstallDriver**
   :::column-end:::
   :::column span="":::
      **SQLInstallDriverManager**  <br>      **Sqlインストーラエラー**  <br>      **SQLInstallODBC**  <br>      **SQLReadFileDSN**  <br>      **SQLRemoveDSNFromINI**  <br>      **SQLValidDSN**  <br>      **SQLWriteDSNToINI**
   :::column-end:::
:::row-end:::
  
> [!NOTE]
>  ODBC *3. x* ドライバーマネージャーでは、unicode **#define** を使用した odbc *2.x アプリケーションの* 再コンパイルがサポートされているため、非推奨の関数では unicode から ANSI へのマッピングがサポートされています。  
  
 このセクションでは、次のトピックを扱います。  
  
-   [Unicode アプリケーション](../../../odbc/reference/develop-app/unicode-applications.md)  
  
-   [Unicode ドライバー](../../../odbc/reference/develop-app/unicode-drivers.md)  
  
-   [ドライバー マネージャーの関数マッピング](../../../odbc/reference/develop-app/function-mapping-in-the-driver-manager.md)
