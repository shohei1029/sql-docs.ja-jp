---
title: 一括コピー操作の実行 (ODBC) |Microsoft Docs
description: SQL Server Native Client ODBC ドライバーが SQL Server の一括コピー操作を実行する DB-Library 関数をサポートする方法について説明します。
ms.custom: ''
ms.date: 03/16/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- SQL Server Native Client ODBC driver, bulk copy
- bulk copy [ODBC]
- ODBC, bulk copy operations
- minimally logged operations [SQL Server Native Client]
- bulk copy [ODBC], about bulk copy
ms.assetid: 5c793405-487c-4f52-88b8-0091d529afb3
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 8b6481e980a257958bc5e0f74720683feeb924c7
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104754752"
---
# <a name="performing-bulk-copy-operations-odbc"></a>一括コピー操作の実行 (ODBC)
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  ODBC 標準では、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の一括コピー操作が直接サポートされません。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client ODBC ドライバーでは、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 7.0 以降のインスタンスに接続するときに、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の一括コピー操作を実行する DB-Library 関数がサポートされます。 このドライバー固有の拡張機能では、一括コピー関数を使用する既存の DB-Library アプリケーションを簡単にアップグレードできる手段が用意されています。 次のファイルには、一括コピー専用のサポートが含まれています。  
  
-   sqlncli.h  
  
     一括コピー関数の関数プロトタイプと定数定義が含まれています。 一括コピー操作を実行する ODBC アプリケーションでは、sqlncli.h をインクルードする必要があります。このファイルは、コンパイル時にアプリケーションのインクルード パスに存在している必要があります。  
  
-   sqlncli11.lib  
  
     リンカーのライブラリ パスに存在し、リンクされるファイルとして指定する必要があります。 sqlncli11.lib は、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client ODBC ドライバーに付属しています。  
  
-   sqlncli11.dll  
  
     実行時に存在する必要があります。 sqlncli11.dll は、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client ODBC ドライバーに付属しています。  
  
> [!NOTE]  
>  ODBC **Sqlbulkoperations** 関数には、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 一括コピー関数との関係はありません。 アプリケーションでは、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 固有の一括コピー関数を使用して、一括コピー操作を実行する必要があります。  
  
## <a name="minimally-logging-bulk-copies"></a>一括コピーの最小ログ記録  
 完全復旧モデルでは、一括読み込みで実行されたすべての行挿入操作が、トランザクション ログに完全に記録されます。 ただし大量のデータを読み込むと、トランザクション ログがすぐにいっぱいになる可能性があります。 特定の条件下では、最小ログ記録が可能になります。 最小ログ記録を行うことで、一括読み込み操作によってログ領域がいっぱいになる可能性が少なくなります。また、これは完全なログ記録よりも効率的です。  
  
 最小ログ記録の使用の詳細については、「 [一括インポートで最小ログ記録を行うための前提条件](../../relational-databases/import-export/prerequisites-for-minimal-logging-in-bulk-import.md)」を参照してください。  
  
## <a name="remarks"></a>解説  
 [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] 以降で bcp.exe を使用すると、[!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] より前のバージョンでは発生しなかった状況でエラーが発生することがあります。 これは、新しいバージョンでは bcp.exe でデータ型が暗黙的に変換されなくなったためです。 [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] より前のバージョンでは、対象テーブルのデータ型が money データ型の場合、bcp.exe で数値データが money データ型に変換されました。 ただし、その際には、余分なフィールドは単純に切り捨てられていました。 以降では [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] 、データ型がファイルと対象テーブルの間で一致しない場合、対象テーブルに合わせて切り捨てが必要なデータがあると、bcp.exe によってエラーが発生します。 このエラーを解決するには、対象のデータ型に合わせてデータを修正します。 [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] より前のリリースの bcp.exe を使用することもできます。  
  
## <a name="in-this-section"></a>このセクションの内容  
  
-   [データ ファイルとフォーマット ファイルの使用](../../relational-databases/native-client-odbc-bulk-copy-operations/using-data-files-and-format-files.md)  
  
-   [プログラム変数からの一括コピー](../../relational-databases/native-client-odbc-bulk-copy-operations/bulk-copying-from-program-variables.md)  
  
-   [一括コピー バッチ サイズの管理](../../relational-databases/native-client-odbc-bulk-copy-operations/managing-bulk-copy-batch-sizes.md)  
  
-   [テキスト データと画像データの一括コピー](../../relational-databases/native-client-odbc-bulk-copy-operations/bulk-copying-text-and-image-data.md)  
  
-   [DB-Library から ODBC への一括コピーの変換](../../relational-databases/native-client-odbc-bulk-copy-operations/converting-from-db-library-to-odbc-bulk-copy.md)  
  
## <a name="see-also"></a>参照  
 [SQL Server Native Client &#40;ODBC&#41;](../../relational-databases/native-client/odbc/sql-server-native-client-odbc.md)   
 [データの一括インポートと一括エクスポート &#40;SQL Server&#41;](../../relational-databases/import-export/bulk-import-and-export-of-data-sql-server.md)  
  
  
