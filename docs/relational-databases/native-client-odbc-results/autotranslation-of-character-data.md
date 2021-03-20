---
description: 文字データの自動変換
title: 文字データの自動変換 |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- result sets [ODBC], autotranslating character data
- data types [ODBC], autotranslating character data
- ACPs
- SQL Server Native Client ODBC driver, result sets
- ODBC applications, result sets
- AutoTranslate feature
- ANSI code pages
- character data autotranslation [ODBC]
- autotranslating character data
- SQL Server Native Client ODBC driver, data types
- ODBC data types, autotranslating character data
ms.assetid: 86a8adda-c5ad-477f-870f-cb370c39ee13
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: c8b570a25b2a822e54efba4551e615a92d121d93
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104752762"
---
# <a name="autotranslation-of-character-data"></a>文字データの自動変換
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  SQL_C_CHAR で宣言された ANSI 文字変数や、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] **CHAR**、 **varchar**、または **text** データ型を使用してに格納されているデータなどの文字データは、制限された文字数のみを表すことができます。 1 文字ごとに 1 バイトを使用して保存される文字データでは、256 文字しか表現できません。 SQL_C_CHAR 変数に格納される値は、クライアント コンピューターの ANSI コード ページ (ACP) を使用して解釈されます。 サーバーで **char**、 **varchar**、または **text** データ型を使用して格納されている値は、サーバーの ACP を使用して評価されます。  
  
 サーバーとクライアントの両方に同じ ACP がある場合、SQL_C_CHAR、 **CHAR**、 **varchar**、または **text** オブジェクトに格納されている値の解釈に問題はありません。 サーバーとクライアントの ACPs が異なる場合、 **CHAR**、 **varchar**、または **text** 型の列、変数、またはパラメーターで使用されている場合、クライアントからの SQL_C_CHAR データはサーバー上で別の文字として解釈される可能性があります。 たとえば、0xA5 という値の文字バイトは、コード ページ 437 を使用するコンピューターでは &#xD1; という文字に解釈されますが、コード ページ 1252 を使用するコンピューターでは円記号 (\) として解釈されます。  
  
 Unicode データは、1 文字あたり 2 バイトを使用して格納されます。 Unicode の仕様は、すべての拡張文字を網羅しています。したがって、すべての Unicode 文字は、すべてのコンピューターで同じ文字に解釈されます。  
  
 Native Client ODBC ドライバーの AutoTranslate 機能は、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] クライアントと異なるコードページを持つサーバー間での文字データの移動に関する問題を最小限に抑えようとしています。 AutoTranslate は、 [SQLDriverConnect](../../relational-databases/native-client-odbc-api/sqldriverconnect.md)の接続文字列、 [sqlconfigdatasource](../../relational-databases/native-client-odbc-api/sqlconfigdatasource.md)の構成文字列、または [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] odbc アドミニストレーターを使用して Native Client ODBC ドライバーのデータソースを構成するときに設定できます。  
  
 AutoTranslate が "no" に設定されている場合、クライアントの SQL_C_CHAR 変数と、データベースの **CHAR**、 **varchar**、または **text** 型の列、変数、またはパラメーターの間で移動されたデータの変換は行われません [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。 この場合、データに拡張文字が含まれていて、クライアント コンピューターとサーバー コンピューターのコード ページが異なっていると、ビット パターンはクライアントとサーバーでは異なって解釈される可能性があります。 クライアントとサーバーが同じコード ページを使用している場合は、データは同じ文字に解釈されます。  
  
 AutoTranslate が "yes" に設定されている場合、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native CLIENT ODBC ドライバーは Unicode を使用して、クライアント上の SQL_C_CHAR 変数と、データベース内の **CHAR**、 **varchar**、または **text** 型の列、変数、またはパラメーターの間で移動されたデータを変換し [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。  
  
-   データがクライアントの SQL_C_CHAR 変数から **CHAR**、 **varchar**、または **text** 型の列、変数、またはパラメーターにデータベースで送信されると [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 、ODBC ドライバーはまず、クライアントの Acp を使用して SQL_C_CHAR から unicode に変換し、次にサーバーの acp を使用して unicode から文字に変換します。  
  
-   データベース内の **char**、 **varchar**、または **text** 型の列、変数、またはパラメーターから、データがクライアントの SQL_C_CHAR 変数に送信されると [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native CLIENT ODBC ドライバーは、まずサーバーの acp を使用して文字を unicode に変換し、次に unicode からクライアントの acp を使用して SQL_C_CHAR します。  
  
 これらの変換はすべて、クライアントで実行されている Native Client ODBC ドライバーによって行われるため [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 、サーバーの ACP はクライアントコンピューターにインストールされているコードページの1つである必要があります。  
  
 Unicode を使用して文字を変換することで、両方のコード ページに存在しているすべての文字を適切に変換できます。 ただし、一方のコード ページにあっても、他方のコード ページにない文字の場合は、変換先のコード ページでは表現できません。 たとえば、コード ページ 1252 には登録商標記号 (&#xAE;) がありますが、コード ページ 437 にはこの記号がありません。  
  
 AutoTranslate の設定は、次の変換には影響しません。  
  
-   データベース内の文字 SQL_C_CHAR クライアント変数と Unicode **nchar**、 **nvarchar**、または **ntext** 型の列、変数、またはパラメーターの間でデータを移動する [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。  
  
-   Unicode SQL_C_WCHAR クライアント変数と、データベース内の文字 **char**、 **varchar**、または **text** 型の列、変数、またはパラメーターの間でデータを移動する [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。  
  
 文字から Unicode にデータが移動される場合、データは必ず変換されます。  
  
## <a name="see-also"></a>参照  
 [ODBC&#41;&#40;結果の処理 ](../../relational-databases/native-client-odbc-results/processing-results-odbc.md)   
 [照合順序と Unicode のサポート](../../relational-databases/collations/collation-and-unicode-support.md)  
  
  
