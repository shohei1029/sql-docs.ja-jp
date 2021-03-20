---
description: SQLDescribeCol
title: SQLDescribeCol |Microsoft Docs
ms.custom: ''
ms.date: 03/17/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
apitype: DLLExport
helpviewer_keywords:
- SQLDescribeCol function
ms.assetid: ffbf34c6-8268-434f-829a-82009a6cda59
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 8b4962c3be438333c4791fc990cbaa5b3fd66569
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104751402"
---
# <a name="sqldescribecol"></a>SQLDescribeCol
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  実行されるステートメントの場合、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native CLIENT ODBC ドライバーでは、結果セットの列を説明するためにサーバーに対してクエリを実行する必要はありません。 この場合、 **SQLDescribeCol** ではサーバーのやり取りは行われません。 [Sqlcolattribute](../../relational-databases/native-client-odbc-api/sqlcolattribute.md)や [Sqlnumresultcols](../../relational-databases/native-client-odbc-api/sqlnumresultcols.md)と同様に、 **SQLDescribeCol** に対して、実行されていないステートメントを呼び出すと、サーバーのラウンドトリップが生成されます。  
  
 1 つの [!INCLUDE[tsql](../../includes/tsql-md.md)] ステートメントまたはステートメント バッチから複数の結果行セットが返される場合、別のテーブルの列を序数で参照したり、結果セット内の別の列を参照することができます。 各セットに対して **SQLDescribeCol** を呼び出す必要があります。 結果セットが変更されると、アプリケーションでは、行の結果をフェッチする前に、データ値を再バインドする必要があります。 複数の結果セットを返す処理の詳細については、「 [Sqlmoreresults](../../relational-databases/native-client-odbc-api/sqlmoreresults.md)」を参照してください。  
  
 準備された SQL ステートメントのバッチによって複数の結果セットが生成されるときは、最初の結果セットの列属性のみが報告されます。  
  
 大きな値のデータ型の場合、 *DataTypePtr* で返される値は SQL_VARCHAR、SQL_VARBINARY、または SQL_NVARCHAR です。 *Columnsizeptr* の SQL_SS_LENGTH_UNLIMITED の値は、サイズが "無制限" であることを示します。  
  
 で始まるデータベースエンジンの機能強化により [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 、SQLDescribeCol は、予期される結果についてより正確な説明を取得できます。 これらのより正確な結果は、以前のバージョンので SQLDescribeCol によって返された値とは異なる場合があり [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。 詳細については、「[メタデータの検出](../../relational-databases/native-client/features/metadata-discovery.md)」を参照してください。  
  
## <a name="sqldescribecol-support-for-enhanced-date-and-time-features"></a>SQLDescribeCol による機能強化された日付と時刻のサポート  
 日付型または時刻型に対して返される値を次に示します。  
  
| 属性 | *DataTypePtr* | *ColumnSizePtr* | *DecimalDigitsPtr* |  
| --------- | ------------- |---------------- | ------------------ |  
|DATETIME|SQL_TYPE_TIMESTAMP|23|3|  
|smalldatetime|SQL_TYPE_TIMESTAMP|16|0|  
|date|SQL_TYPE_DATE|10|0|  
|time|SQL_SS_TIME2|8、10..16|0..7|  
|datetime2|SQL_TYPE_TIMESTAMP|19、21..27|0..7|  
|datetimeoffset|SQL_SS_TIMESTAMPOFFSET|26、28..34|0..7|  
  
 詳細については、「 [日付と時刻の機能強化 &#40;ODBC&#41;](../../relational-databases/native-client-odbc-date-time/date-and-time-improvements-odbc.md)」を参照してください。  
  
## <a name="sqldescribecol-support-for-large-clr-udts"></a>SQLDescribeCol による大きな CLR UDT のサポート  
 **SQLDescribeCol** は、大きな CLR ユーザー定義型 (udt) をサポートしています。 詳細については、「 [LARGE CLR User-Defined Types &#40;ODBC&#41;](../../relational-databases/native-client/odbc/large-clr-user-defined-types-odbc.md)」を参照してください。  
  
## <a name="see-also"></a>参照  
 [SQLDescribeCol 関数](../../odbc/reference/syntax/sqldescribecol-function.md)   
 [ODBC API 実装の詳細](../../relational-databases/native-client-odbc-api/odbc-api-implementation-details.md)  
  
