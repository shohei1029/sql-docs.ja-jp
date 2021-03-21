---
description: SQLColumns
title: SQLColumns |Microsoft Docs
ms.custom: ''
ms.date: 03/17/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
apitype: DLLExport
helpviewer_keywords:
- SQLColumns function
ms.assetid: 69d3af44-8196-43ab-8037-cdd06207b171
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 086904d3456ba5160813c731ca84db249692b441
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104755062"
---
# <a name="sqlcolumns"></a>SQLColumns
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  **Sqlcolumns** は、 *CatalogName*、 *TableName*、または *ColumnName* パラメーターの値が存在するかどうかを SQL_SUCCESS 返します。 これらのパラメーターで無効な値が使用されている場合、 **Sqlfetch** は SQL_NO_DATA を返します。  
  
> [!NOTE]  
>  大きな値型の場合、すべての長さパラメーターが SQL_SS_LENGTH_UNLIMITED という値で返されます。  
  
 **Sqlcolumns** は、静的サーバーカーソルで実行できます。 更新可能なカーソル (動的カーソルまたはキーセットカーソル) で **Sqlcolumns** を実行しようとすると、カーソルの種類が変更されたことを示す SQL_SUCCESS_WITH_INFO が返されます。  
  
 > Native Client ODBC ドライバーでは、 *CatalogName* パラメーターの2部構成の名前 ( *Linked_Server_Name*) を受け入れることによって、リンクサーバー上のテーブルに関する情報のレポートをサポートしています。  
  
 ODBC 2 の場合。*x* アプリケーションでは、 *tablename* でワイルドカードを使用していません。 **sqlcolumns** は、名前が *tablename* に一致し、現在のユーザーが所有しているテーブルに関する情報を返します。 現在のユーザーが *tablename* パラメーターと一致する名前を持つテーブルを所有していない場合、 **sqlcolumns** は、テーブル名が *tablename* パラメーターと一致する他のユーザーが所有しているテーブルに関する情報を返します。 ODBC 2 の場合。*x* アプリケーションワイルドカードを使用する **sqlcolumns** は、名前が *TableName* に一致するすべてのテーブルを返します。 ODBC 3 の場合。*x* Applications **sqlcolumns** は、所有者に関係なく名前が *TableName* に一致するすべてのテーブル、またはワイルドカードを使用するかどうかを返します。  
  
 次の表は、結果セットによって返される列の一覧です。  
  
|列名|説明|  
|-----------------|-----------------|  
|DATA_TYPE|**VARCHAR (max)** データ型の SQL_VARCHAR、SQL_VARBINARY、または SQL_WVARCHAR を返します。|  
|TYPE_NAME|**Varchar (max)**、 **varbinary (max)**、および **nvarchar (max)** データ型の "varchar"、"varbinary"、または "nvarchar" を返します。|  
|COLUMN_SIZE|**Varchar (max)** データ型の場合、列のサイズが無制限であることを示す SQL_SS_LENGTH_UNLIMITED を返します。|  
|BUFFER_LENGTH|**Varchar (max)** データ型の場合、バッファーのサイズが無制限であることを示す SQL_SS_LENGTH_UNLIMITED を返します。|  
|SQL_DATA_TYPE|**VARCHAR (max)** データ型の SQL_VARCHAR、SQL_VARBINARY、または SQL_WVARCHAR を返します。|  
|CHAR_OCTET_LENGTH|char 型または binary 型の列の最大長を返します。 サイズが無制限であることを示す場合は 0 を返します。|  
|SS_XML_SCHEMACOLLECTION_CATALOG_NAME|XML スキーマ コレクション名が定義されているカタログの名前を返します。 カタログ名が見つからない場合は、この変数に空文字列が含まれます。|  
|SS_XML_SCHEMACOLLECTION_SCHEMA_NAME|XML スキーマ コレクション名が定義されているスキーマの名前を返します。 スキーマ名が見つからない場合は、この変数に空文字列が含まれます。|  
|SS_XML_SCHEMACOLLECTION_NAME|XML スキーマ コレクションの名前を返します。 名前が見つからない場合は、この変数に空文字列が含まれます。|  
|SS_UDT_CATALOG_NAME|UDT (ユーザー定義型) を含むカタログの名前です。|  
|SS_UDT_SCHEMA_NAME|UDT を含むスキーマの名前。|  
|SS_UDT_ASSEMBLY_TYPE_NAME|UDT のアセンブリ修飾名です。|  
  
 Udt の場合は、UDT の名前を示すために、既存の TYPE_NAME 列が使用されます。そのため、 **Sqlcolumns** または [SQLProcedureColumns](../../relational-databases/native-client-odbc-api/sqlprocedurecolumns.md)の結果セットに列を追加することはできません。 UDT 列または UDT パラメーターの場合、DATA_TYPE は SQL_SS_UDT です。  
  
 サーバーからパラメーターの UDT に関する追加のメタデータ プロパティを返したり、サーバーでこのメタデータ プロパティの情報が必要となる場合は、上記に定義したドライバー固有の新しい記述子を使用して、UDT のメタデータ プロパティの取得や設定を行えます。  
  
 クライアントがに接続 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] して SQLColumns を呼び出す場合、カタログ入力パラメーターに NULL またはワイルドカード値を使用しても、他のカタログからの情報は返されません。 代わりに、現在のカタログに関する情報だけが返されます。 クライアントはまず SQLTables を呼び出して、目的のテーブルがあるカタログを特定できます。 その後、クライアントは、そのカタログ値を SQLColumns の呼び出しでカタログ入力パラメーターに使用して、そのテーブル内の列に関する情報を取得できます。  
  
## <a name="sqlcolumns-and-table-valued-parameters"></a>SQLColumns とテーブル値パラメーター  
 SQLColumns によって返される結果セットは、SQL_SOPT_SS_NAME_SCOPE の設定によって異なります。 詳細については、「 [SQLSetStmtAttr](../../relational-databases/native-client-odbc-api/sqlsetstmtattr.md)」を参照してください。 テーブル値パラメーター用に次の列が追加されました。  
  
|列名|データ型|内容|  
|-----------------|---------------|--------------|  
|SS_IS_COMPUTED|Smallint|TABLE_TYPE の列では、列が計算列の場合は SQL_TRUE、それ以外の場合は SQL_FALSE になります。|  
|SS_IS_IDENTITY|Smallint|列が ID 列の場合は SQL_TRUE、それ以外の場合は SQL_FALSE になります。|  
  
 テーブル値パラメーターの詳細については、「 [テーブル値パラメーター &#40;ODBC&#41;](../../relational-databases/native-client-odbc-table-valued-parameters/table-valued-parameters-odbc.md)」を参照してください。  
  
## <a name="sqlcolumns-support-for-enhanced-date-and-time-features"></a>SQLColumns による機能強化された日付と時刻のサポート  
 日付型または時刻型に対して返される値の詳細については、「 [カタログメタデータ](../../relational-databases/native-client-odbc-date-time/metadata-catalog.md)」を参照してください。  
  
 詳細については、「 [日付と時刻の機能強化 &#40;ODBC&#41;](../../relational-databases/native-client-odbc-date-time/date-and-time-improvements-odbc.md)」を参照してください。  
  
## <a name="sqlcolumns-support-for-large-clr-udts"></a>SQLColumns による大きな CLR UDT のサポート  
 **Sqlcolumns** は、大きな CLR ユーザー定義型 (udt) をサポートしています。 詳細については、「 [LARGE CLR User-Defined Types &#40;ODBC&#41;](../../relational-databases/native-client/odbc/large-clr-user-defined-types-odbc.md)」を参照してください。  
  
## <a name="sqlcolumns-support-for-sparse-columns"></a>SQLColumns によるスパース列のサポート  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]Sqlcolumns の結果セットには、次の2つの特定の列が追加されています。  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|SS_IS_SPARSE|**Smallint**|列がスパース列の場合は SQL_TRUE、それ以外の場合は SQL_FALSE になります。|  
|SS_IS_COLUMN_SET|**Smallint**|列が **column_set** 列の場合、これは SQL_TRUE です。それ以外の場合は、SQL_FALSE ます。|  
  
 ODBC 仕様に準拠して、SS_IS_SPARSE と SS_IS_COLUMN_SET は、より前のバージョンに追加されたドライバー固有のすべての列の前、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] および odbc 自体によって指定されたすべての列の前に表示されます。  
  
 SQLColumns によって返される結果セットは、SQL_SOPT_SS_NAME_SCOPE の設定によって異なります。 詳細については、「 [SQLSetStmtAttr](../../relational-databases/native-client-odbc-api/sqlsetstmtattr.md)」を参照してください。  
  
 ODBC でのスパース列の詳細については、「 [スパース列のサポート &#40;odbc&#41;](../../relational-databases/native-client/odbc/sparse-columns-support-odbc.md)」を参照してください。  
  
## <a name="see-also"></a>参照  
 [SQLColumns 関数](../../odbc/reference/syntax/sqlcolumns-function.md)   
 [ODBC API 実装の詳細](../../relational-databases/native-client-odbc-api/odbc-api-implementation-details.md)  
  
