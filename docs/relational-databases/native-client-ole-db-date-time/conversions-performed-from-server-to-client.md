---
description: サーバーからクライアントに対して実行される SQL Server Native Client 変換
title: サーバーからクライアントへの変換
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: ''
ms.topic: reference
helpviewer_keywords:
- conversions [OLE DB], server to client
ms.assetid: 676fdf24-fb72-4ea0-a8d2-2b197da3c83f
author: markingmyname
ms.author: maghan
ms.custom: seo-dt-2019
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: f7fbf944f45231a5dea9bb34de5e8095a9bc7a68
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104748402"
---
# <a name="sql-server-native-client-conversions-performed-from-server-to-client"></a>サーバーからクライアントに対して実行される SQL Server Native Client 変換
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  このトピックでは、[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] (以降) と、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client OLE DB で作成されたクライアント アプリケーションとの間で実行される日付または時刻の変換について説明します。  
  
## <a name="conversions"></a>コンバージョン  
 次の表では、クライアントに返される型とバインドの型との間の変換について説明しています。 出力パラメーターでは、ICommandWithParameters::SetParameterInfo が呼び出され、*pwszDataSourceType* で指定された型が実際のサーバーの型と一致しない場合、サーバーによって暗黙的な変換が実行され、クライアントに返された型は ICommandWithParameters::SetParameterInfo を介して指定された型と一致します。 これにより、サーバーの変換規則がこのトピックで説明したものと異なる場合に、予期しない変換結果が発生する可能性があります。 たとえば、既定の日付を指定する必要がある場合、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では 1899-12-30 ではなく 1900-1-1 が使用されます。  
  
|To -><br /><br /> ソース|DATE|DBDATE|DBTIME|DBTIME2|DBTIMESTAMP|DBTIMESTAMPOFFSET|FILETIME|BYTES|VARIANT|SSVARIANT|BSTR|STR|WSTR|  
|----------------------|----------|------------|------------|-------------|-----------------|-----------------------|--------------|-----------|-------------|---------------|----------|---------|----------|  
|Date|1、7|[OK]|-|-|1|1、3|1、7|-|[OK] \(VT_BSTR)|[OK]|[OK]|4|4|  
|Time|5、6、7|-|9|[OK]|6|3、6|5、6|-|[OK] \(VT_BSTR)|[OK]|[OK]|4|4|  
|Smalldatetime|7|8|9、10|10|[OK]|3|7|-|7 (VT_DATE)|[OK]|[OK]|4|4|  
|Datetime|5、7|8|9、10|10|[OK]|3|7|-|7 (VT_DATE)|[OK]|[OK]|4|4|  
|Datetime2|5、7|8|9、10|10|7|3|5、7|-|[OK] \(VT_BSTR)|[OK]|[OK]|4|4|  
|Datetimeoffset|5、7、11|8、11|9、10、11|10、11|7、11|OK|5、7、11|-|[OK] \(VT_BSTR)|[OK]|[OK]|4|4|  
|Char、Varchar、<br /><br /> Nchar、Nvarchar|7、13|12|12、9|12|12|12|7、13|該当なし|該当なし|該当なし|該当なし|該当なし|該当なし|  
|Sql_variant<br /><br /> (datetime)|7|8|9、10|10|[OK]|3|7|-|7 (VT_DATE)|[OK]|[OK]|4|4|  
|Sql_variant<br /><br /> (smalldatetime)|7|8|9、10|10|[OK]|3|7|-|7 (VT_DATE)|[OK]|[OK]|4|4|  
|Sql_variant<br /><br /> (date)|1、7|[OK]|2|2|1|1、3|1、7|-|OK (VT_BSTR)|[OK]|[OK]|4|4|  
|Sql_variant<br /><br /> (time)|5、6、7|2|6|[OK]|6|3、6|5、6|-|OK (VT_BSTR)|[OK]|[OK]|4|4|  
|Sql_variant<br /><br /> (datetime2)|5、7|8|9、10|10|[OK]|3|5、7|-|OK (VT_BSTR)|[OK]|[OK]|4|4|  
|Sql_variant<br /><br /> (datetimeoffset)|5、7、11|8、11|9、10、11|10、11|7、11|OK|5、7、11|-|OK (VT_BSTR)|[OK]|[OK]|4|4|  
  
## <a name="key-to-symbols"></a>記号の説明  
  
|Symbol|意味|  
|------------|-------------|  
|[OK]|変換は必要ありません。|  
|-|変換はサポートされていません。 IAccessor::CreateAccessor の呼び出し時にバインドが検証される場合、DBBINDSTATUS_UPSUPPORTEDCONVERSION が *rgStatus* で返されます。 アクセサー検証が遅延する場合は、DBSTATUS_E_BADACCESSOR が設定されます。|  
|1|時刻フィールドに 0 が設定されます。|  
|2|DBSTATUS_E_CANTCONVERTVALUE が設定されます。|  
|3|タイムゾーンは 0 に設定されます。|  
|4|クライアント バッファーのサイズが十分でない場合、DBSTATUS_S_TRUNCATED が設定されます。 サーバーの型に秒の小数部が含まれている場合、結果文字列の桁数はサーバーの型の小数点以下桁数と完全に一致します。|  
|5|秒または秒の小数部の切り捨ては無視されます。|  
|6|日付は現在の日付に設定されます。ただし、変換元が時間の文字列リテラルで、変換先が DBTYPE_DATE の場合は別です。 この場合、1899-12-30 が使用されます。|  
|7|値がオーバーフローする場合は、DBSTATUS_E_DATAOVERFLOW が設定されます。|  
|8|時刻フィールドは無視されます。|  
|9|秒の小数部のフィールドは無視されます。|  
|10|日付部分は無視されます。|  
|11|時刻はクライアントのタイム ゾーンに変換されます。 この変換中にエラーが発生した場合、DBSTATUS_E_DATAOVERFLOW が設定されます。|  
|12|文字列は ISO リテラルとして解析され、対象の型に変換されます。 これが失敗すると、文字列は OLE 日付リテラル (時刻要素も含む) として解析され、OLE Date (DBTYPE_DATE) から対象の型に変換されます。 文字列は、ISO 形式の解析を成功させるために許可された対象の型のリテラルの構文に準拠している必要があります。 OLE での解析を成功させるには、文字列は OLE で認識される構文に準拠している必要があります。 文字列を解析できない場合は、DBSTATUS_E_CANTCONVERTVALUE が設定されます。 任意の部分の値が範囲外の場合は、DBSTATUS_E_DATAOVERFLOW が設定されます。|  
|13|文字列は ISO リテラルとして解析され、対象の型に変換されます。 これが失敗すると、文字列は OLE 日付リテラル (時刻要素も含む) として解析され、OLE Date (DBTYPE_DATE) から対象の型に変換されます。 文字列は datetime リテラルの構文に準拠している必要があります。ただし、変換先が DBTYPE_DATE または DBTYPE_DBTIMESTAMP の場合は別です。 この場合、ISO 形式の解析を成功させるために、datetime リテラルまたは時刻リテラルが許容されています。 OLE での解析を成功させるには、文字列は OLE で認識される構文に準拠している必要があります。 文字列を解析できない場合は、DBSTATUS_E_CANTCONVERTVALUE が設定されます。 任意の部分の値が範囲外の場合は、DBSTATUS_E_DATAOVERFLOW が設定されます。|  
  
## <a name="see-also"></a>参照  
 [バインドと変換 &#40;OLE DB&#41;](../../relational-databases/native-client-ole-db-date-time/conversions-ole-db.md)  
  
  
