---
description: 日付と時刻の強化機能 (ODBC)
title: 日付と時刻の強化 (ODBC) |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- date/time [ODBC]
- ODBC, date/time improvements
ms.assetid: e31d5ca5-2103-498f-954c-1ee93e217186
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 397f922b275009fd94308d4ff1235113bd865432
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104756132"
---
# <a name="date-and-time-improvements-odbc"></a>日付と時刻の強化機能 (ODBC)
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] では、新しい日付と時刻のデータ型が導入されました。 このセクションでは、これらの新しい型がネイティブクライアントで拡張として公開されるしくみについて説明し [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]Native Client による新しい日付と時刻のデータ型のサポートの概要については、「[日付と時刻の機能強化](../../relational-databases/native-client/features/date-and-time-improvements.md)」を参照してください。 ODBC の日付と時刻のサポートを示すサンプルについては、「 [日付と時刻の型の使用](../../relational-databases/native-client-odbc-how-to/use-date-and-time-types.md)」を参照してください。  
  
 日付と時刻のデータ型に関する一般的な情報については、「[datetime &#40;Transact-SQL&#41;](../../t-sql/data-types/datetime-transact-sql.md)」を参照してください。  
  
## <a name="in-this-section"></a>このセクションの内容  
 [ODBC の日付/時刻の強化に対するデータ型のサポート](../../relational-databases/native-client-odbc-date-time/data-type-support-for-odbc-date-and-time-improvements.md)  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の日付と時刻のデータ型をサポートする ODBC 型について説明します。  
  
 [ODBC&#41;&#40;メタデータ ]()  
 実装パラメーター記述子 (IPD) および実装行記述子 (IRD) フィールドに返される情報、および **Sqlcolumns** と **SQLProcedureColumns** によって返される列のメタデータについて説明します。 また、 **SQLGetTypeInfo** によって返されるデータ型のメタデータについても説明します。  
  
 [ODBC&#41;&#40;の datetime データ型の変換 ](../../relational-databases/native-client-odbc-date-time/datetime-data-type-conversions-odbc.md)  
 datetime 値と datetimeoffset 値の間で変換を行う方法について説明します。  
  
 [sql_variant による日付型と時刻型のサポート](../../relational-databases/native-client-odbc-date-time/sql-variant-support-for-date-and-time-types.md)  
 SQL_VARIANT 関数による機能強化された日付と時刻のサポートについて説明します。  
  
 [OLE DB および ODBC の &#40;、強化された日付/時刻型に対する一括コピーの変更&#41;](../../relational-databases/native-client-odbc-date-time/bulk-copy-changes-for-enhanced-date-and-time-types-ole-db-and-odbc.md)  
 一括コピー操作をサポートする日付または時刻の機能強化について説明します。  
  
 [以前の SQL Server バージョン &#40;ODBC&#41;の日付と時刻の型の動作の強化 ](../../relational-databases/native-client-odbc-date-time/enhanced-date-and-time-type-behavior-with-previous-sql-server-versions-odbc.md)  
 機能強化された日付と時刻を使用するクライアントアプリケーションが古いバージョンのと通信する場合、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] および以前のバージョンの Native client でコンパイルされたクライアントが、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 機能強化された日付と時刻の機能をサポートするサーバーにコマンドを送信する場合に想定される動作について説明します。  
  
 [ODBC API による機能強化された日付と時刻のサポート](../../relational-databases/native-client-odbc-date-time/odbc-api-support-for-enhanced-date-and-time-features.md)  
 機能強化された日付と時刻をサポートする ODBC 関数の一覧を示します。  
  
## <a name="see-also"></a>参照  
 [SQL Server Native Client &#40;ODBC&#41;](../../relational-databases/native-client/odbc/sql-server-native-client-odbc.md)  
  
