---
title: 日付と時刻の機能強化 | Microsoft Docs
description: SQL Server 2008 に追加された日付と時刻のデータ型に対する OLE DB Driver for SQL Server のサポートについて説明します。
ms.custom: ''
ms.date: 06/12/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: connectivity
ms.topic: reference
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: ca548ab01e4379b6cea92b5764820383abaf926b
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104742862"
---
# <a name="date-and-time-improvements"></a>日付と時刻の強化機能
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[Driver_OLEDB_Download](../../../includes/driver_oledb_download.md)]

  このトピックでは、[!INCLUDE[ssKatmai](../../../includes/sskatmai-md.md)] に追加された日付と時刻のデータ型に対する OLE DB Driver for SQL Server のサポートについて説明します。  
  
 日付と時刻の機能強化の詳細については、「[日付と時刻の機能強化 &#40;OLE DB&#41;](../../oledb/ole-db-date-time/date-and-time-improvements-ole-db.md)」を参照してください。  
  
 この機能を説明するサンプル アプリケーションについては、「[SQL Server データ プログラミング サンプル](https://msftdpprodsamples.codeplex.com/)」を参照してください。  
  
## <a name="usage"></a>使用法  
 ここでは、新しい日付型と時刻型のさまざまな使用方法について説明します。  
  
### <a name="use-date-as-a-distinct-data-type"></a>個別のデータ型として日付を使用する  
 [!INCLUDE[ssKatmai](../../../includes/sskatmai-md.md)] 以降では、日付型と時刻型のサポートの強化により、DBTYPE_DBDATE OLE DB 型をより効果的に使用できるようになりました。  
  
### <a name="use-time-as-a-distinct-data-type"></a>個別のデータ型として時刻を使用する  
 OLE DB には既に、有効桁数が 1 秒のデータ型として DBTYPE_DBTIME があります。このデータ型には時刻のみが含まれます。
  
 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] の新しい時刻データ型では、秒の小数部の精度が 100 ナノ秒です。 これには、OLE DB Driver for SQL Server に新しい型が必要です。DBTYPE_DBTIME2。 秒の小数部を含まない時刻を使用するように記述された既存のアプリケーションでは、time(0) 列を使用できます。 アプリケーションがメタデータに返される型に依存しない場合は、既存の OLE DB DBTYPE_TIME 型とそれに対応する構造体が正常に動作します。  
  
### <a name="use-time-as-a-distinct-data-type-with-extended-fractional-seconds-precision"></a>秒の有効桁数が拡張された個別のデータ型として時刻を使用する  
 プロセス制御や製造アプリケーションなど、アプリケーションによっては、有効桁数が 100 ナノ秒までの時刻データを処理できる必要があります。 OLE DB でのこの目的のための新しい型が DBTYPE_DBTIME2 です。  
  
### <a name="use-datetime-with-extended-fractional-seconds-precision"></a>秒の有効桁数が拡張された Datetime を使用する  
 OLE DB では既に、有効桁数が 1 ナノ秒までの型が定義されています。 ただし、この型は既に [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] の既存のアプリケーションで使用されており、このようなアプリケーションでは、有効桁数を 1/300 秒までしか想定していません。 新しい **datetime2(3)** 型は、既存の datetime 型と直接的な互換性がありません。 これがアプリケーションの動作に影響するというリスクがある場合、アプリケーションは新しい DBCOLUMN フラグを使用して、実際のサーバーの種類を判断する必要があります。    
  
### <a name="use-datetime-with-extended-fractional-seconds-precision-and-timezone"></a>秒の有効桁数とタイム ゾーンが拡張された Datetime を使用する  
 アプリケーションによっては、タイム ゾーン情報を含む datetime 値が必要です。 これは、新しい DBTYPE_DBTIMESTAMPOFFSET 型でサポートされています。
  
### <a name="use-datetimedatetimedatetimeoffset-data-with-client-side-conversions-consistent-with-existing-conversions"></a>既存の変換と一貫性のあるクライアント側変換で Date/Time/Datetime/Datetimeoffset データを使用する  
 この変換は、[!INCLUDE[ssKatmai](../../../includes/sskatmai-md.md)] で導入されたすべての日付型と時刻型との間の変換を含めるように一貫して拡張されます。  
  
## <a name="see-also"></a>参照  
 [OLE DB Driver for SQL Server の機能](../../oledb/features/oledb-driver-for-sql-server-features.md)  
  
  
