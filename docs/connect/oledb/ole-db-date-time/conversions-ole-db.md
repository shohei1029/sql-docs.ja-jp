---
title: バインドと変換 (OLE DB) | Microsoft Docs
description: OLE DB Driver for SQL Server で datetime 値と datetimeoffset 値の間で変換を行う方法について説明します。 一般的な変換規則がいくつかあります。
ms.custom: ''
ms.date: 06/14/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: connectivity
ms.topic: reference
helpviewer_keywords:
- conversions [OLE DB]
- bindings [OLE DB]
- OLE DB, bindings and conversions
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: 2e87bcae399abd2b563f9c347f6752f49568a523
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104755342"
---
# <a name="conversions-ole-db"></a>変換 (OLE DB)
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[Driver_OLEDB_Download](../../../includes/driver_oledb_download.md)]

  このセクションでは、**datetime** 値と **datetimeoffset** 値の間で変換を行う方法について説明します。 ここで説明する変換は、OLE DB によって既に提供されているか、OLE DB の一貫性がある拡張機能です。  
  
 OLE DB における日付と時刻のリテラルと文字列の形式は、通常 ISO に従うため、クライアントのロケールに依存しません。 これには、標準が OLE オートメーションである DBTYPE_DATE という例外があります。 ただし、OLE DB Driver for SQL Server では、クライアントとの間でデータがやりとりされる際に型の変換しか実行されないため、アプリケーションでは、OLE DB Driver for SQL Server に DBTYPE_DATE と文字列形式の変換を強制することができません。 それ以外の場合、文字列では次の形式を使用します (角かっこに囲まれたテキストは省略可能な要素を示します)。  
  
-   **datetime** および **datetimeoffset** 文字列の形式は次のとおりです。  
  
     *yyyy*-*mm*-*dd*[ *hh*:*mm*:*ss*[.*9999999*][ ± *hh*:*mm*]]  
  
-   **time** 型の文字列の形式は次のとおりです。  
  
     *hh*:*mm*:*ss*[.*9999999*]  
  
-   **date** 文字列の形式は次のとおりです。  
  
     *yyyy*-*mm*-*dd*  
  
> [!NOTE]  
>  以前のバージョンの [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Native Client および SQLOLEDB では、標準の変換が失敗した場合に備えて OLE 変換が実装されていました。 OLE DB Driver for SQL Server は動作が [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Native Client と同じです。 このため、OLE DB Driver for SQL Server によって実行された変換には、OLE DB 仕様と異なるものがあります。  
  
 文字列からの変換では、空白文字やフィールドの幅を柔軟に処理できます。 詳細については、「[OLE DB の日付/時刻の強化に対するデータ型のサポート](../../oledb/ole-db-date-time/data-type-support-for-ole-db-date-and-time-improvements.md)」の「データ フォーマット: 文字列とリテラル」セクションをご覧ください。  
  
 一般的な変換規則を次に示します。  
  
-   文字列を日付型または時刻型に変換すると、文字列はまず ISO リテラルとして解析されます。 これが失敗すると、文字列は OLE 日付リテラルとして解析されます。OLE 日付リテラルには時刻要素があります。  
  
-   時刻が存在しなくても受信側が時刻を格納できる場合、時刻は 0 に設定されます。 日付が存在しなくても受信側が日付を格納できる場合、ISO 変換を使用すると日付は現在の日付に設定され、OLE 変換を使用すると日付は 1899-12-30 に設定されます。  
  
-   クライアントが使用しているデータ型にタイム ゾーンが存在しなくても、サーバーがタイム ゾーンを格納できる場合、クライアントのデータはクライアントのタイム ゾーンで表されていると仮定されます。  
  
-   サーバーにタイム ゾーンが存在しなくても、クライアントにタイム ゾーン情報がある場合、UTC のタイム ゾーンが使用されます。 これはサーバーの動作とは異なります。  
  
-   時刻が存在しても受信側が時刻を格納できない場合、時刻要素は無視されます。  
  
-   日付が存在しても受信側が日付を格納できない場合、日付要素は無視されます。  
  
-   クライアントからサーバーへの変換時に秒または秒の小数部の切り捨てが発生すると、DB_E_ERRORSOCCURRED が返され、状態 DBSTATUS_E_DATAOVERFLOW が設定されます。  
  
-   サーバーからクライアントへの変換時に秒または秒の小数部の切り捨てが発生すると、DBSTATUS_S_TRUNCATED が設定されます。  
  
## <a name="in-this-section"></a>このセクションの内容  
 [クライアントからサーバーへの変換](../../oledb/ole-db-date-time/conversions-performed-from-client-to-server.md)  
 OLE DB Driver for SQL Server を使用して作成されたクライアント アプリケーションと [!INCLUDE[ssKatmai](../../../includes/sskatmai-md.md)] (以降) との間で実行される日付または時刻の変換について説明します。  
  
 [サーバーからクライアントへの変換](../../oledb/ole-db-date-time/conversions-performed-from-server-to-client.md)  
 [!INCLUDE[ssKatmai](../../../includes/sskatmai-md.md)] (以降) と OLE DB Driver for SQL Server を使用して作成されたクライアント アプリケーションとの間で実行される日付または時刻の変換について説明します。  
  
## <a name="see-also"></a>参照  
 [日付と時刻の強化機能 &#40;OLE DB&#41;](../../oledb/ole-db-date-time/date-and-time-improvements-ole-db.md)  
  
  
