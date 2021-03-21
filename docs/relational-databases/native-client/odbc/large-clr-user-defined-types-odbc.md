---
description: 大きな CLR ユーザー定義型 (ODBC)
title: 大きな CLR User-Defined 型 (ODBC) |Microsoft Docs
ms.custom: ''
ms.date: 03/17/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- ODBC, large user-defined types
- large user-defined types [ODBC]
ms.assetid: ddce337e-bb6e-4a30-b7cc-4969bb1520a9
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: f1e55ddcd51072688d4544a41b415f42bb79fd58
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104748354"
---
# <a name="large-clr-user-defined-types-odbc"></a>大きな CLR ユーザー定義型 (ODBC)
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  このトピックでは、大きな共通言語ランタイム (CLR) ユーザー定義型 (UDT) をサポートするための、SQL Server Native Client の ODBC に対する変更について説明します。  
  
 大きな CLR Udt の ODBC サポートを示すサンプルについては、「 [大きな udt のサポート](../../../relational-databases/native-client-odbc-how-to/support-for-large-udts.md)」を参照してください。  
  
 SQL Server Native Client での大きな CLR Udt のサポートの詳細については、「 [大きな clr User-Defined 型](../../../relational-databases/native-client/features/large-clr-user-defined-types.md)」を参照してください。  
  
## <a name="data-format"></a>データ形式  
 SQL Server Native Client では、大きなオブジェクト (LOB) の型について、列のサイズが 8,000 バイトを超えていることを示す場合に、SQL_SS_LENGTH_UNLIMITED が使用されます。 SQL Server 2008 以降では、サイズが 8,000 バイトを超えている CLR UDT にも同じ値が使用されるようになりました。  
  
 UDT 値はバイト配列として表されます。 16 進文字列との間の変換がサポートされています。 リテラル値は、"0x" で始まる 16 進文字列として表されます。  
  
 次の表に、パラメーターおよび結果セットでのデータ型のマッピングを示します。  
  
|SQL Server のデータ型|SQL データ型|値|  
|--------------------------|-------------------|-----------|  
|CLR UDT|SQL_SS_UDT|-151 (sqlncli.h)|  
  
 次の表では、対応する構造体と ODBC C 型について説明します。 基本的に、CLR UDT は、追加のメタデータを持つ **varbinary** 型です。  
  
|SQL データ型|メモリ レイアウト|C データ型|値 (sqlext.h)|  
|-------------------|-------------------|-----------------|------------------------|  
|SQL_SS_UDT|SQLCHAR * (unsigned char \* )|SQL_C_BINARY|SQL_BINARY (-2)|  
  
## <a name="descriptor-fields-for-parameters"></a>パラメーターの記述子フィールド  
 IPD フィールドに返される情報は次のとおりです。  
  
|記述子フィールド|SQL_SS_UDT<br /><br /> (8,000 バイト以下の長さ)|SQL_SS_UDT<br /><br /> (8,000 バイトを超える長さ)|  
|----------------------|-------------------------------------------------------------------|----------------------------------------------------------|  
|SQL_DESC_CASE_SENSITIVE|SQL_FALSE|SQL_FALSE|  
|SQL_DESC_CONCISE_TYPE|SQL_SS_UDT|SQL_SS_UDT|  
|SQL_DESC_DATETIME_INTERVAL_CODE|0|0|  
|SQL_DESC_DATETIME_INTERVAL_PRECISION|*n*|SQL_SS_LENGTH_UNLIMITED (0)|  
|SQL_DESC_FIXED_PREC_SCALE|SQL_FALSE|SQL_FALSE|  
|SQL_DESC_LENGTH|*n*|SQL_SS_LENGTH_UNLIMITED (0)|  
|SQL_DESC_LOCAL_TYPE_NAME|"udt"|"udt"|  
|SQL_DESC_OCTET_LENGTH|*n*|SQL_SS_LENGTH_UNLIMITED (0)|  
|SQL_DESC_PRECISION|*n*|SQL_SS_LENGTH_UNLIMITED (0)|  
|SQL_DESC_SCALE|0|0|  
|SQL_DESC_TYPE|SQL_SS_UDT|SQL_SS_UDT|  
|SQL_DESC_TYPE_NAME|"udt"|"udt"|  
|SQL_DESC_UNSIGNED|SQL_TRUE|SQL_TRUE|  
|SQL_CA_SS_UDT_CATALOG_NAME|UDT を含むカタログの名前|UDT を含むカタログの名前|  
|SQL_CA_SS_UDT_SCHEMA_NAME|UDT を含むスキーマの名前|に UDT を含むスキーマの名前を指定します。|  
|SQL_CA_SS_UDT_TYPE_NAME|UDT の名前|UDT の名前|  
|SQL_CA_SS_UDT_ASSEMBLY_TYPE_NAME|UDT の完全修飾名|UDT の完全修飾名|  
  
 UDT パラメーターの場合、SQL_CA_SS_UDT_TYPE_NAME は常に **SQLSetDescField** を使用して設定する必要があります。 SQL_CA_SS_UDT_CATALOG_NAME と SQL_CA_SS_UDT_SCHEMA_NAME は省略可能です。  
  
 UDT が、テーブルとは異なるスキーマで同じデータベースに定義されている場合は、SQL_CA_SS_UDT_SCHEMA_NAME を設定する必要があります。  
  
 UDT がテーブルとは別のデータベースに定義されている場合は、SQL_CA_SS_UDT_CATALOG_NAME と SQL_CA_SS_UDT_SCHEMA_NAME を設定する必要があります。  
  
 SQL_CA_SS_UDT_TYPE_NAME、SQL_CA_SS_UDT_CATALOG_NAME、または SQL_CA_SS_UDT_SCHEMA_NAME の設定にエラーや省略があった場合は、SQLSTATE HY000 およびサーバー固有のメッセージ テキストで、診断レコードが生成されます。  
  
## <a name="descriptor-fields-for-results"></a>結果の記述子フィールド  
 IRD フィールドに返される情報は次のとおりです。  
  
|記述子フィールド|SQL_SS_UDT<br /><br /> (8,000 バイト以下の長さ)|SQL_SS_UDT<br /><br /> (8,000 バイトを超える長さ)|  
|----------------------|-------------------------------------------------------------------|----------------------------------------------------------|  
|SQL_DESC_AUTO_UNIQUE_VALUE|SQL_FALSE|SQL_FALSE|  
|SQL_DESC_CASE_SENSITIVE|SQL_FALSE|SQL_FALSE|  
|SQL_DESC_CONCISE_TYPE|SQL_SS_UDT|SQL_SS_UDT|  
|SQL_DESC_DATETIME_INTERVAL_CODE|0|0|  
|SQL_DESC_DATETIME_INTERVAL_PRECISION|*n*|SQL_SS_LENGTH_UNLIMITED (0)|  
|SQL_DESC_DISPLAY_SIZE|2 *n*|SQL_SS_LENGTH_UNLIMITED (0)|  
|SQL_DESC_FIXED_PREC_SCALE|SQL_FALSE|SQL_FALSE|  
|SQL_DESC_LENGTH|*n*|SQL_SS_LENGTH_UNLIMITED (0)|  
|SQL_DESC_LITERAL_PREFIX|"0x"|"0x"|  
|SQL_DESC_LITERAL_SUFFIX|""|""|  
|SQL_DESC_LOCAL_TYPE_NAME|"udt"|"udt"|  
|SQL_DESC_OCTET_LENGTH|*n*|SQL_SS_LENGTH_UNLIMITED (0)|  
|SQL_DESC_PRECISION|*n*|SQL_SS_LENGTH_UNLIMITED (0)|  
|SQL_DESC_SCALE|0|0|  
|SQL_DESC_SEARCHABLE|SQL_PRED_NONE|SQL_PRED_NONE|  
|SQL_DESC_TYPE|SQL_SS_UDT|SQL_SS_UDT|  
|SQL_DESC_TYPE_NAME|"udt"|"udt"|  
|SQL_DESC_UNSIGNED|SQL_TRUE|SQL_TRUE|  
|SQL_CA_SS_UDT_CATALOG_NAME|UDT を含むカタログの名前|UDT を含むカタログの名前|  
|SQL_CA_SS_UDT_SCHEMA_NAME|UDT を含むスキーマの名前|UDT を含むスキーマの名前|  
|SQL_CA_SS_UDT_TYPE_NAME|UDT の名前|UDT の名前|  
|SQL_CA_SS_UDT_ASSEMBLY_TYPE_NAME|UDT の完全修飾名|UDT の完全修飾名|  
  
## <a name="column-metadata-returned-by-sqlcolumns-and-sqlprocedurecolumns-catalog-metadata"></a>SQLColumns および SQLProcedureColumns から返される列のメタデータ (カタログ メタデータ)  
 UDT に対して次の列値が返されます。  
  
|列名|SQL_SS_UDT<br /><br /> (8,000 バイト以下の長さ)|SQL_SS_UDT<br /><br /> (8,000 バイトを超える長さ)|  
|-----------------|-------------------------------------------------------------------|----------------------------------------------------------|  
|DATA_TYPE|SQL_SS_UDT|SQL_SS_UDT|  
|TYPE_NAME|UDT の名前|UDT の名前|  
|COLUMN_SIZE|*n*|SQL_SS_LENGTH_UNLIMITED (0)|  
|BUFFER_LENGTH|*n*|SQL_SS_LENGTH_UNLIMITED (0)|  
|DECIMAL_DIGITS|NULL|NULL|  
|SQL_DATA_TYPE|SQL_SS_UDT|SQL_SS_UDT|  
|SQL_DATETIME_SUB|NULL|NULL|  
|CHAR_OCTET_LENGTH|*n*|SQL_SS_LENGTH_UNLIMITED (0)|  
|SS_UDT_CATALOG_NAME|UDT を含むカタログの名前|UDT を含むカタログの名前|  
|SS_UDT_SCHEMA_NAME|UDT を含むスキーマの名前|UDT を含むスキーマの名前|  
|SS_UDT_ASSEMBLY_TYPE_NAME|UDT の完全修飾名|UDT の完全修飾名|  
  
 最後の 3 つの列はドライバー固有の列です。 これらは、ODBC 定義の列の後、SQLColumns または SQLProcedureColumns の結果セットの既存のドライバー固有の列の前に追加されます。  
  
 個々の Udt またはジェネリック型 "udt" に対して、SQLGetTypeInfo によって返される行はありません。  
  
## <a name="bindings-and-conversions"></a>バインドと変換  
 SQL から C データ型への変換としてサポートされているものは次のとおりです。  
  
|変換対象|SQL_SS_UDT|  
|-----------------------------|------------------|  
|SQL_C_WCHAR|さ|  
|SQL_C_BINARY|サポートされています|  
|SQL_C_CHAR|さ|  
  
 \* バイナリデータは16進数の文字列に変換されます。  
  
 C から SQL データ型への変換としてサポートされているものは次のとおりです。  
  
|変換対象|SQL_SS_UDT|  
|-----------------------------|------------------|  
|SQL_C_WCHAR|さ|  
|SQL_C_BINARY|サポートされています|  
|SQL_C_CHAR|さ|  
  
 \* 16進文字列からバイナリデータへの変換が行われます。  
  
## <a name="sql_variant-support-for-udts"></a>SQL_VARIANT による UDT のサポート  
 SQL_VARIANT 列では UDT がサポートされません。  
  
## <a name="bcp-support-for-udts"></a>BCP による UDT のサポート  
 UDT 値は、文字またはバイナリ値としてのみインポートおよびエクスポートできます。  
  
## <a name="downlevel-client-behavior-for-udts"></a>UDT に対する下位クライアントの動作  
 UDT に対しては、下位クライアントで次のように型マッピングが行われます。  
  
|サーバーのバージョン|SQL_SS_UDT<br /><br /> (8,000 バイト以下の長さ)|SQL_SS_UDT<br /><br /> (8,000 バイトを超える長さ)|  
|--------------------|-------------------------------------------------------------------|----------------------------------------------------------|  
|SQL Server 2005|**UDT**|**varbinary(max)**|  
|SQL Server 2008 以降|**UDT**|**UDT**|  
  
## <a name="odbc-functions-supporting-large-clr-udts"></a>大きな CLR UDT をサポートする ODBC 関数  
 ここでは、大きな CLR UDT をサポートするための、SQL Server Native Client の ODBC 関数に対する変更について説明します。  
  
### <a name="sqlbindcol"></a>SQLBindCol  
 UDT の結果列の値は、このトピックの「バインドと変換」の説明に従って、SQL から C データ型に変換されます。  
  
### <a name="sqlbindparameter"></a>SQLBindParameter  
 UDT に必要な値は次のとおりです。  
  
|SQL データ型|*Parametertype*|*ColumnSizePtr*|*DecimalDigitsPtr*|  
|-------------------|---------------------|---------------------|------------------------|  
|SQL_SS_UDT<br /><br /> (8,000 バイト以下の長さ)|SQL_SS_UDT|*n*|0|  
|SQL_SS_UDT<br /><br /> (8,000 バイトを超える長さ)|SQL_SS_UDT|SQL_SS_LENGTH_UNLIMITED (0)|0|  
  
### <a name="sqlcolattribute"></a>SQLColAttribute  
 UDT に対して返される値は、このトピックの「結果の記述子フィールド」で説明したとおりです。  
  
### <a name="sqlcolumns"></a>SQLColumns  
 Udt に対して返される値は、このトピックの「SQLColumns および SQLProcedureColumns によって返される列のメタデータ (カタログメタデータ)」で説明されています。  
  
### <a name="sqldescribecol"></a>SQLDescribeCol  
 UDT に対して返される値は次のとおりです。  
  
|SQL データ型|*DataTypePtr*|*ColumnSizePtr*|*DecimalDigitsPtr*|  
|-------------------|-------------------|---------------------|------------------------|  
|SQL_SS_UDT<br /><br /> (8,000 バイト以下の長さ)|SQL_SS_UDT|*n*|0|  
|SQL_SS_UDT<br /><br /> (8,000 バイトを超える長さ)|SQL_SS_UDT|SQL_SS_LENGTH_UNLIMITED (0)|0|  
  
### <a name="sqldescribeparam"></a>SQLDescribeParam  
 UDT に対して返される値は次のとおりです。  
  
|SQL データ型|*DataTypePtr*|*ColumnSizePtr*|*DecimalDigitsPtr*|  
|-------------------|-------------------|---------------------|------------------------|  
|SQL_SS_UDT<br /><br /> (8,000 バイト以下の長さ)|SQL_SS_UDT|*n*|0|  
|SQL_SS_UDT<br /><br /> (8,000 バイトを超える長さ)|SQL_SS_UDT|SQL_SS_LENGTH_UNLIMITED (0)|0|  
  
### <a name="sqlfetch"></a>SQLFetch  
 UDT の結果列の値は、このトピックの「バインドと変換」の説明に従って、SQL から C データ型に変換されます。  
  
### <a name="sqlfetchscroll"></a>SQLFetchScroll  
 UDT の結果列の値は、このトピックの「バインドと変換」の説明に従って、SQL から C データ型に変換されます。  
  
### <a name="sqlgetdata"></a>SQLGetData  
 UDT の結果列の値は、このトピックの「バインドと変換」の説明に従って、SQL から C データ型に変換されます。  
  
### <a name="sqlgetdescfield"></a>SQLGetDescField  
 新しい型で使用できる記述子フィールドは、このトピックの「パラメーターの記述子フィールド」および「結果の記述子フィールド」で説明したとおりです。  
  
### <a name="sqlgetdescrec"></a>SQLGetDescRec  
 UDT に対して返される値は次のとおりです。  
  
|SQL データ型|種類|サブタイプ|長さ|有効桁数|スケール|  
|-------------------|----------|-------------|------------|---------------|-----------|  
|SQL_SS_UDT<br /><br /> (8,000 バイト以下の長さ)|SQL_SS_UDT|0|*n*|n|0|  
|SQL_SS_UDT<br /><br /> (8,000 バイトを超える長さ)|SQL_SS_UDT|0|SQL_SS_LENGTH_UNLIMITED (0)|SQL_SS_LENGTH_UNLIMITED (0)|0|  
  
### <a name="sqlgettypeinfo"></a>SQLGetTypeInfo  
 UDT に対して返される値は、このトピックの「SQLColumns および SQLProcedureColumns から返される列のメタデータ (カタログ メタデータ)」で説明したとおりです。  
  
### <a name="sqlprocedurecolumns"></a>SQLProcedureColumns  
 UDT に対して返される値は、このトピックの「SQLColumns および SQLProcedureColumns から返される列のメタデータ (カタログ メタデータ)」で説明したとおりです。  
  
### <a name="sqlputdata"></a>SQLPutData  
 UDT パラメーター値は、このトピックの「バインドと変換」の説明に従って、C から SQL データ型に変換されます。  
  
### <a name="sqlsetdescfield"></a>SQLSetDescField  
 新しい型で使用できる記述子フィールドについては、このトピックで前述した「パラメーターの記述子フィールド」と「結果の記述子フィールド」を参照してください。  
  
### <a name="sqlsetdescrec"></a>SQLSetDescRec  
 UDT に対して許可される値は次のとおりです。  
  
|SQL データ型|種類|サブタイプ|長さ|有効桁数|スケール|  
|-------------------|----------|-------------|------------|---------------|-----------|  
|SQL_SS_UDT<br /><br /> (8,000 バイト以下の長さ)|SQL_SS_UDT|0|*n*|*n*|0|  
|SQL_SS_UDT<br /><br /> (8,000 バイトを超える長さ)|SQL_SS_UDT|0|SQL_SS_LENGTH_UNLIMITED (0)|SQL_SS_LENGTH_UNLIMITED (0)|0|  
  
### <a name="sqlspecialcolumns"></a>SQLSpecialColumns  
 DATA_TYPE、TYPE_NAME、COLUMN_SIZE、BUFFER_LENGTH、DECIMAL_DIGTS の各 UDT 列に対して返される値は、このトピックの「SQLColumns および SQLProcedureColumns から返される列のメタデータ (カタログ メタデータ)」で説明したとおりです。  
  
## <a name="see-also"></a>参照  
 [大きな CLR ユーザー定義型](../../../relational-databases/native-client/features/large-clr-user-defined-types.md)  
  
  
