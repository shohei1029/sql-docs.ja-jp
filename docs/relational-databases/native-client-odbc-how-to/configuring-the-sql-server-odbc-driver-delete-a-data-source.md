---
title: データソースの削除 (ODBC) |Microsoft Docs
description: Odbc アプリケーションを SQL Server 2005 以降で使用する前に、ODBC 管理者、プログラム、またはファイルを使用してデータソースを削除する方法について説明します。
ms.custom: ''
ms.date: 08/01/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- data sources [ODBC]
ms.assetid: 910e3e16-7b91-49d8-80bb-b4243926afaa
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 1dff34d1e0ce2554cd13fb5e261ad35ced8f378c
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104752562"
---
# <a name="configuring-the-sql-server-odbc-driver---delete-a-data-source"></a>SQL Server ODBC ドライバーの構成 - データ ソースの削除
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] 以降で ODBC アプリケーションを使用する前に、以前のバージョンの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] でカタログ ストアド プロシージャのバージョンをアップグレードする方法や、データ ソースを追加、削除、およびテストする方法を理解しておく必要があります。  
  
  データソースを削除するには、ODBC 管理者を使用してプログラムで ( [Sqlconfigdatasource](../../relational-databases/native-client-odbc-api/sqlconfigdatasource.md)を使用)、またはファイルを削除します (ファイルデータソース名が指定されている場合)。  
  
### <a name="to-delete-a-data-source-by-using-odbc-administrator"></a>ODBC アドミニストレーターを使用してデータ ソースを削除するには  
  
1.  **コントロールパネル** で、[**管理ツール**] を開き、[ **odbc データソース (64 ビット)** ] または [ **odbc データソース (32 ビット)**] をダブルクリックします。 コマンド プロンプトから odbcad32.exe を実行することもできます。  
  
2.  [ **ユーザー dsn**]、[ **システム dsn**]、または [ **ファイル dsn** ] タブをクリックします。  
  
3.  削除するデータソースを選択します。  
  
4.  [ **削除**] をクリックし、削除を確定します。  

## <a name="example"></a>例  
 プログラムによってデータソースを削除するには、2番目のパラメーターとして ODBC_REMOVE_DSN または ODBC_REMOVE_SYS_DSN を使用して [Sqlconfigdatasource](../../relational-databases/native-client-odbc-api/sqlconfigdatasource.md) を呼び出します。  
  
 次のサンプルでは、データ ソースをプログラムで削除する方法を示しています。  
  
```  
// remove_odbc_data_source.cpp  
// compile with: ODBCCP32.lib user32.lib  
#include <iostream>  
#include \<windows.h>  
#include \<odbcinst.h>  
  
int main() {   
   LPCSTR provider = "SQL Server";   // Windows SQL Server Driver  
   LPCSTR provider = "SQL Server";   // Windows SQL Server driver  
   LPCSTR provider2 = "SQL Server Native Client 11.0";   // SQL Server 2012 Native Client driver  
   LPCSTR dsnname = "DSN=data2";  
   BOOL retval = SQLConfigDataSource(NULL, ODBC_REMOVE_DSN, provider, dsnname);  
   std::cout << retval;   // 1 if successful  
}  
```  
  
## <a name="see-also"></a>参照  
 [データソース &#40;ODBC&#41;に追加する ](../../relational-databases/native-client-odbc-how-to/configuring-the-sql-server-odbc-driver-add-a-data-source.md)  
  
  
