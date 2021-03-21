---
description: sql_variant による日付型と時刻型のサポート
title: 日付型と時刻型のサポートの sql_variantMicrosoft Docs
ms.custom: ''
ms.date: 03/03/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- sql_variant data type
ms.assetid: 12ff1ea6-e2cc-40e6-910c-3126974a90b3
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 886f8ba633cd820cb142582e99604e28fa729eca
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104748143"
---
# <a name="sql_variant-support-for-date-and-time-types"></a>sql_variant による日付型と時刻型のサポート
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  このトピックでは、 **sql_variant** データ型が、強化された日付と時刻の機能をサポートする方法について説明します。  
  
 SQL_CA_SS_VARIANT_TYPE 列属性は、バリアント型結果列の C 型を返すために使用されます。 [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] では、SQL_CA_SS_VARIANT_SQL_TYPE 属性が追加されています。この属性は、実装行記述子 (IRD) 内のバリアント型結果列の SQL 型を設定します。 SQL_CA_SS_VARIANT_SQL_TYPE は、実装パラメーター記述子 (IPD) 内で、SQL_SS_VARIANT 型で SQL_C_BINARY C 型がバインドされた SQL_SS_TIME2 パラメーターまたは SQL_SS_TIMESTAMPOFFSET パラメーターの SQL 型を指定するためにも使用できます。  
  
 新しい型 SQL_SS_TIME2 と SQL_SS_TIMESTAMPOFFSET は、SQLColAttribute によって設定できます。 SQL_CA_SS_VARIANT_SQL_TYPE は、SQLGetDescField で返すことができます。  
  
 結果列については、ドライバーによってバリアント型から日付型または時刻型に変換されます。 詳細については、「 [SQL から C への変換](../../relational-databases/native-client-odbc-date-time/datetime-data-type-conversions-from-sql-to-c.md)」を参照してください。SQL_C_BINARY にバインドする場合、バッファーの長さは、SQL 型に対応する構造体を受け取るのに十分な大きさである必要があります。  
  
 次の表で説明するように、ドライバーは、SQL_SS_TIME2 パラメーターと SQL_SS_TIMESTAMPOFFSET パラメーターに対して C 値を **sql_variant** 値に変換します。 パラメーターが SQL_C_BINARY としてバインドされ、かつ、サーバー型が SQL_SS_VARIANT である場合、アプリケーションで SQL_CA_SS_VARIANT_SQL_TYPE が別の SQL 型に設定されていない限り、バイナリ値として扱われます。 この場合は、SQL_CA_SS_VARIANT_SQL_TYPE が優先されます。つまり、SQL_CA_SS_VARIANT_SQL_TYPE が設定されている場合、C 型からバリアントの SQL 型を推定するという既定の動作がオーバーライドされます。  
  
|C 型|サーバーの種類|説明|  
|------------|-----------------|--------------|  
|SQL_C_CHAR|varchar|SQL_CA_SS_VARIANT_SQL_TYPE は無視されます。|  
|SQL_C_WCHAR|nvarcar|SQL_CA_SS_VARIANT_SQL_TYPE は無視されます。|  
|SQL_C_TINYINT|smallint|SQL_CA_SS_VARIANT_SQL_TYPE は無視されます。|  
|SQL_C_STINYINT|smallint|SQL_CA_SS_VARIANT_SQL_TYPE は無視されます。|  
|SQL_C_SHORT|smallint|SQL_CA_SS_VARIANT_SQL_TYPE は無視されます。|  
|SQL_C_SSHORT|smallint|SQL_CA_SS_VARIANT_SQL_TYPE は無視されます。|  
|SQL_C_USHORT|INT|SQL_CA_SS_VARIANT_SQL_TYPE は無視されます。|  
|SQL_C_LONG|INT|SQL_CA_SS_VARIANT_SQL_TYPE は無視されます。|  
|SQL_C_SLONG|INT|SQL_CA_SS_VARIANT_SQL_TYPE は無視されます。|  
|SQL_C_ULONG|bigint|SQL_CA_SS_VARIANT_SQL_TYPE は無視されます。|  
|SQL_C_SBIGINT|bigint|SQL_CA_SS_VARIANT_SQL_TYPE は無視されます。|  
|SQL_C_FLOAT|real|SQL_CA_SS_VARIANT_SQL_TYPE は無視されます。|  
|SQL_C_DOUBLE|float|SQL_CA_SS_VARIANT_SQL_TYPE は無視されます。|  
|SQL_C_BIT|bit|SQL_CA_SS_VARIANT_SQL_TYPE は無視されます。|  
|SQL_C_UTINYINT|tinyint|SQL_CA_SS_VARIANT_SQL_TYPE は無視されます。|  
|SQL_C_BINARY|varbinary|SQL_CA_SS_VARIANT_SQL_TYPE は設定されません。|  
|SQL_C_BINARY|time|SQL_CA_SS_VARIANT_SQL_TYPE = SQL_SS_TIME2<br /><br /> Scale は SQL_DESC_PRECISION ( **SQLBindParameter** の *DecimalDigits* パラメーター) に設定されます。|  
|SQL_C_BINARY|datetimeoffset|SQL_CA_SS_VARIANT_SQL_TYPE = SQL_SS_TIMESTAMPOFFSET<br /><br /> Scale は SQL_DESC_PRECISION ( **SQLBindParameter** の *DecimalDigits* パラメーター) に設定されます。|  
|SQL_C_TYPE_DATE|date|SQL_CA_SS_VARIANT_SQL_TYPE は無視されます。|  
|SQL_C_TYPE_TIME|time(0)|SQL_CA_SS_VARIANT_SQL_TYPE は無視されます。|  
|SQL_C_TYPE_TIMESTAMP|datetime2|Scale は SQL_DESC_PRECISION ( **SQLBindParameter** の *DecimalDigits* パラメーター) に設定されます。|  
|SQL_C_NUMERIC|decimal|有効桁数が SQL_DESC_PRECISION ( **SQLBindParameter** の *columnsize* パラメーター) に設定されています。<br /><br /> スケールセットを SQL_DESC_SCALE (SQLBindParameter の *DecimalDigits* パラメーター) に設定します。|  
|SQL_C_SS_TIME2|time|SQL_CA_SS_VARIANT_SQL_TYPE は無視されます。|  
|SQL_C_SS_TIMESTAMPOFFSET|datetimeoffset|SQL_CA_SS_VARIANT_SQL_TYPE は無視されます。|  
  
## <a name="see-also"></a>参照  
 [ODBC&#41;&#40;の日付と時刻の改善 ](../../relational-databases/native-client-odbc-date-time/date-and-time-improvements-odbc.md)  
  
  
