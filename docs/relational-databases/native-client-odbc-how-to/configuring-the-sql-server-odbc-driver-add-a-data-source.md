---
title: データソースの追加 (ODBC) |Microsoft Docs
description: ODBC ドライバー SQL Server、リモートストアドプロシージャ呼び出しメカニズムを使用して SQL Server のリモートストアドプロシージャとしてストアドプロシージャを呼び出す方法について説明します。
ms.custom: ''
ms.date: 08/01/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- data sources [ODBC]
ms.assetid: b4ac6f0e-8e6a-4b1a-9a7e-60e0a69b2180
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: af6b417f8742fa20f49954f8b5ae2bcb5ddfc6f3
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104752552"
---
# <a name="configuring-the-sql-server-odbc-driver---add-a-data-source"></a>SQL Server ODBC ドライバーの構成 - データ ソースの追加
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] 以降で ODBC アプリケーションを使用する前に、以前のバージョンの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] でカタログ ストアド プロシージャのバージョンをアップグレードする方法や、データ ソースを追加、削除、およびテストする方法を理解しておく必要があります。  
  
  データソースを追加するには、ODBC アドミニストレーターを使用するか、プログラムで ( [Sqlconfigdatasource](../../relational-databases/native-client-odbc-api/sqlconfigdatasource.md)を使用)、またはファイルを作成します。  
  
### <a name="to-add-a-data-source-by-using-odbc-administrator"></a>ODBC アドミニストレーターを使用してデータ ソースを追加するには  
  
1.  **コントロールパネル** で、[**管理ツール**] にアクセスしてから、 **odbc データソース (64 ビット)** または **odbc データソース (32 ビット)** のいずれかにアクセスします。 または、odbcad32.exe を呼び出すことができます。  
  
2.  [ **ユーザー dsn**]、 **[システム dsn**]、または [ **ファイル dsn** ] タブをクリックし、[ **追加**] をクリックします。  
  
3.  [ **SQL Server**] をクリックし、[ **完了**] をクリックします。  
  
4.  **SQL Server に新しいデータソースを作成** するウィザードの手順を完了します。  
  
### <a name="to-add-a-data-source-programmatically"></a>データ ソースをプログラムで追加するには  
  
1.  2番目のパラメーターを ODBC_ADD_DSN または ODBC_ADD_SYS_DSN に設定して [Sqlconfigdatasource](../../relational-databases/native-client-odbc-api/sqlconfigdatasource.md) を呼び出します。  
  
### <a name="to-add-a-file-data-source"></a>ファイル データ ソースを追加するには  
  
1.  接続文字列で SAVEFILE = file_name パラメーターを指定して [SQLDriverConnect](../../relational-databases/native-client-odbc-api/sqldriverconnect.md) を呼び出します。 接続が確立されると、ODBC ドライバーによって、SAVEFILE パラメーターが指す場所の接続パラメーターを使用してファイル データ ソースが作成されます。  
  
## <a name="see-also"></a>参照  
[データソース &#40;ODBC&#41;の削除 ](../../relational-databases/native-client-odbc-how-to/configuring-the-sql-server-odbc-driver-delete-a-data-source.md)    
  
  
