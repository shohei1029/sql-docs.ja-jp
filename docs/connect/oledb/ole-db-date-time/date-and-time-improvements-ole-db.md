---
title: 日付と時刻の機能強化 (OLE DB) | Microsoft Docs
description: これらの記事では、OLE DB Driver for SQL Server で新しい日付と時刻のデータ型がどのようにサポートされるかについて説明します。 概要とサンプルを参照してください。
ms.custom: ''
ms.date: 06/14/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: connectivity
ms.topic: reference
helpviewer_keywords:
- date/time [OLE DB]
- OLE DB, date/time improvements
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: 490b8875547560a2fa5d2e8792fc80a1c9dabbff
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104742342"
---
# <a name="date-and-time-improvements-ole-db"></a>日付と時刻の強化機能 (OLE DB)
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[Driver_OLEDB_Download](../../../includes/driver_oledb_download.md)]

  [!INCLUDE[ssKatmai](../../../includes/sskatmai-md.md)] では、新しい日付と時刻のデータ型が導入されています。 ここでは、OLE DB Driver for SQL Server の拡張機能としてこれらの新しい型を公開する方法について説明します。 日付と時刻の新しいデータ型に対する OLE DB Driver for SQL Server サポートの概要については、「[日付と時刻の機能強化](../../oledb/features/date-and-time-improvements.md)」を参照してください。 サンプルについては、「[強化された日付/時刻機能の使用 &#40;OLE DB&#41;](../../oledb/ole-db-how-to/use-enhanced-date-and-time-features-ole-db.md)」を参照してください。  
  
 日付と時刻のデータ型に関する一般的な情報については、「[datetime &#40;Transact-SQL&#41;](../../../t-sql/data-types/datetime-transact-sql.md)」を参照してください。  
  
## <a name="in-this-section"></a>このセクションの内容  
 [OLE DB の日付/時刻の強化に対するデータ型のサポート](../../oledb/ole-db-date-time/data-type-support-for-ole-db-date-and-time-improvements.md)  
 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] の日付および時刻データ型をサポートする OLE DB (OLE DB Driver for SQL Server) の型について説明します。  
  
 [メタデータ &#40;OLE DB&#41;](../../oledb/ole-db-date-time/metadata-parameter-and-rowset.md)  
 DBBINDING 構造、**ICommandWithParameters::GetParameterInfo**、**ICommandWithParameters::SetParameterInfo**、**IColumnsRowset::GetColumnsRowset**、I **ColumnsInfo::GetColumnInfo** に関する詳細が含まれています。 また、OLE DB スキーマ行セットの更新についても説明します。  
  
 [バインドと変換 &#40;OLE DB&#41;](../../oledb/ole-db-date-time/conversions-ole-db.md)  
 既存の日付型と新しい日付型の両方を対象とした、サーバーとクライアント間における変換の規則について説明します。  
  
 [機能強化された日付型と時刻型向けの一括コピーの変更 (OLE DB)](../../oledb/ole-db-date-time/bulk-copy-changes-for-enhanced-date-and-time-types-ole-db.md)  
 一括コピー操作をサポートする日付または時刻の機能強化について説明します。  
  
 [OLE DB API による機能強化された日付と時刻のサポート](../../oledb/ole-db-date-time/ole-db-api-support-for-date-and-time-enhancements.md)  
 機能強化された日付や時刻をサポートする OLE DB API について説明します。  
  
 [IRowsetFind での比較](../../oledb/ole-db-date-time/comparability-for-irowsetfind.md)  
 日付型または時刻型、および **IRowsetFind** について説明します。  
 
  
## <a name="see-also"></a>参照  
 [OLE DB Driver for SQL Server のプログラミング](../../oledb/ole-db/oledb-driver-for-sql-server-programming.md)  
  
  
