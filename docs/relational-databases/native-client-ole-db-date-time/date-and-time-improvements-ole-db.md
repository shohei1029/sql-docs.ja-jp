---
description: SQL Server Native Client の日付と時刻の強化 (OLE DB)
title: 日付と時刻の強化機能 (OLE DB)
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: ''
ms.topic: reference
helpviewer_keywords:
- date/time [OLE DB]
- OLE DB, date/time improvements
ms.assetid: 71614aaf-0fa4-4fe0-b522-68e2e0b66f43
author: markingmyname
ms.author: maghan
ms.custom: seo-dt-2019
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: c907bc757040443d3dc5279974ed500163634534
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104748382"
---
# <a name="sql-server-native-client-date-and-time-improvements-ole-db"></a>SQL Server Native Client の日付と時刻の強化 (OLE DB)
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] では、新しい日付と時刻のデータ型が導入されています。 このセクションでは、これらの新しい型がネイティブクライアントで拡張として公開されるしくみについて説明し [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]新しい日付と時刻のデータ型の Native Client のサポートの概要については、「[日付と時刻の機能強化](../../relational-databases/native-client/features/date-and-time-improvements.md)」を参照してください。 サンプルについては、「[強化された日付/時刻機能の使用 &#40;OLE DB&#41;](../../relational-databases/native-client-ole-db-how-to/use-enhanced-date-and-time-features-ole-db.md)」を参照してください。  
  
 日付と時刻のデータ型に関する一般的な情報については、「[datetime &#40;Transact-SQL&#41;](../../t-sql/data-types/datetime-transact-sql.md)」を参照してください。  
  
## <a name="in-this-section"></a>このセクションの内容  
 [OLE DB の日付/時刻の強化に対するデータ型のサポート](../../relational-databases/native-client-ole-db-date-time/data-type-support-for-ole-db-date-and-time-improvements.md)  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]日付と時刻のデータ型をサポートする OLE DB (Native Client) 型に関する情報を提供 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] します。  
  
 [メタデータ &#40;OLE DB&#41;](./data-type-support-for-ole-db-date-and-time-improvements.md)  
 DBBINDING 構造、**ICommandWithParameters::GetParameterInfo**、**ICommandWithParameters::SetParameterInfo**、**IColumnsRowset::GetColumnsRowset**、I **ColumnsInfo::GetColumnInfo** に関する詳細が含まれています。 また、OLE DB スキーマ行セットの更新についても説明します。  
  
 [バインドと変換 &#40;OLE DB&#41;](../../relational-databases/native-client-ole-db-date-time/conversions-ole-db.md)  
 既存の日付型と新しい日付型の両方を対象とした、サーバーとクライアント間における変換の規則について説明します。  
  
 [OLE DB および ODBC の &#40;、強化された日付/時刻型に対する一括コピーの変更&#41;](../../relational-databases/native-client-odbc-date-time/bulk-copy-changes-for-enhanced-date-and-time-types-ole-db-and-odbc.md)  
 一括コピー操作をサポートする日付または時刻の機能強化について説明します。  
  
 [OLE DB API による機能強化された日付と時刻のサポート](../../relational-databases/native-client-ole-db-date-time/ole-db-api-support-for-date-and-time-enhancements.md)  
 機能強化された日付や時刻をサポートする OLE DB API について説明します。  
  
 [IRowsetFind での比較](../../relational-databases/native-client-ole-db-date-time/comparability-for-irowsetfind.md)  
 日付型または時刻型、および **IRowsetFind** について説明します。  
  
 [以前の SQL Server バージョン &#40;OLE DB の新しい日付と時刻の機能&#41;](../../relational-databases/native-client-ole-db-date-time/new-date-and-time-features-with-previous-sql-server-versions-ole-db.md)  
 機能強化された日付や時刻を使用するクライアント アプリケーションが以前のバージョンの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] と通信する場合、および以前のバージョンの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client でコンパイルされたクライアントから、機能強化された日付や時刻をサポートするサーバーにコマンドを送信する場合に想定される動作について説明します。  
  
## <a name="see-also"></a>参照  
 [SQL Server Native Client &#40;OLE DB&#41;](../../relational-databases/native-client/ole-db/sql-server-native-client-ole-db.md)  
  
