---
description: データ型の使用方法
title: データ型の使用 |Microsoft Docs
ms.custom: ''
ms.date: 03/16/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- ODBC data types
- ODBC data types, about ODBC data types
- SQL Server Native Client ODBC driver, result sets
- ODBC applications, result sets
- data types [ODBC]
- SQL Server Native Client ODBC driver, data types
- data types [ODBC], about data types
ms.assetid: 4f19b0d6-94ac-4a98-a121-57d38787864c
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: b2ceef68921c64d9e51e8f4ba68b315a88ec24fc
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104752752"
---
# <a name="data-type-usage"></a>データ型の使用方法
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]Native CLIENT ODBC ドライバーでは、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 次のデータ型の使用が強制されます。  
  
|データ型|制限事項|  
|---------------|----------------|  
|日付リテラル|日付リテラルは、SQL_TYPE_TIMESTAMP 列 ( [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] **datetime** または **smalldatetime** のデータ型) に格納されている場合、時刻の値は 12:00: 00.000 A.M. になります。|  
|**money** と **smallmoney**|**Money** データ型と **smallmoney** データ型の整数部分のみが重要です。 データ型の変換中に SQL **money** データの小数部が切り捨てられた場合、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client ODBC ドライバーはエラーではなく警告を返します。|  
|SQL_BINARY (NULL 値を許容)|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 6.0 以前のインスタンスに接続していて、SQL_BINARY 型の列で NULL 値が許容される場合、データ ソースに格納されているデータには 0 が埋め込まれません。 このような列からデータを取得すると、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native CLIENT ODBC ドライバーによって、右側にゼロが埋め込まれます。 ただし、連結など、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] により実行される操作で作成されるデータには、このような埋め込みは行われません。<br /><br /> また、データを [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 6.0 以前のインスタンスの列に格納するときに、そのデータが長すぎて列に収まりきらない場合、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] はデータを右側から切り捨てます。<br /><br /> 注: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native CLIENT ODBC ドライバーでは、6.5 以前のバージョンへの接続がサポートされて [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] います。|  
|SQL_CHAR (切り捨て)|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 6.0 以前のインスタンスに接続し、データを SQL_CHAR 型の列に格納するときに、そのデータが長すぎて列に収まりきらない場合、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では警告を通知せずにデータを右側から切り捨てます。<br /><br /> 注: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native CLIENT ODBC ドライバーでは、6.5 以前のバージョンへの接続がサポートされて [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] います。|  
|SQL_CHAR (NULL 値を許容)|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 6.0 以前のインスタンスに接続していて、SQL_CHAR 型の列で NULL 値が許容される場合、データ ソースに格納されているデータには空白が埋め込まれません。 このような列からデータを取得すると、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native CLIENT ODBC ドライバーによって、右側に空白が埋め込まれます。 ただし、連結など、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] により実行される操作で作成されるデータには、このような埋め込みは行われません。<br /><br /> 注: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native CLIENT ODBC ドライバーでは、6.5 以前のバージョンへの接続がサポートされて [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] います。|  
|SQL_LONGVARBINARY、SQL_LONGVARCHAR、SQL_WLONGVARCHAR|複数の行に影響を与える SQL_LONGVARBINARY、SQL_LONGVARCHAR、または SQL_WLONGVARCHAR データ型の列の更新は、6のインスタンスに接続すると完全にサポートされ [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。*x* 以降。 4.2 x のインスタンスに接続すると [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 、S1000 エラー "部分的な挿入/更新" が発生します。 text または image 列の挿入または更新に失敗しました。" が返されます。<br /><br /> 注: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native CLIENT ODBC ドライバーでは、6.5 以前のバージョンへの接続がサポートされて [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] います。|  
|文字列関数パラメーター|文字列関数の *string_exp* パラメーターは、データ型 SQL_CHAR または SQL_VARCHAR である必要があります。 SQL_LONG_VARCHAR データ型はサポートされません。 *Count* パラメーターは8000以下でなければなりません。 SQL_CHAR および SQL_VARCHAR のデータ型は最大8000文字までに制限されているためです。|  
|時刻リテラル|時刻リテラルは、SQL_TIMESTAMP 列 ( [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] **datetime** または **smalldatetime** のデータ型) に格納されている場合、1900年1月1日の日付値を持ちます。|  
|**timestamp**|**Timestamp** 列には、NULL 値のみを手動で挿入できます。 ただし、 **timestamp** 列はによって自動的に更新されるため、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] NULL 値は上書きされます。|  
|**tinyint**|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] **Tinyint** データ型は符号なしです。 **Tinyint** 列は、既定で SQL_C_UTINYINT データ型の変数にバインドされます。|  
|別名データ型|4.2 x のインスタンスに接続されている場合 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 、ODBC ドライバーは、列の NULL 値の許容属性を明示的に宣言しない列定義に NULL を追加します。 したがって、別名データ型の定義に格納されている NULL 値の許容属性は無視されます。<br /><br /> 4.2 x のインスタンスに接続されている場合 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 、別名データ型を持つ列では、基本データ型が **char** または **binary** で、null 値の許容が宣言されていない列は、 **varchar** または **varbinary** として作成されます。 [Sqlcolattribute](../../relational-databases/native-client-odbc-api/sqlcolattribute.md)、 [sqlcolumns](../../relational-databases/native-client-odbc-api/sqlcolumns.md)、および [SQLDescribeCol](../../relational-databases/native-client-odbc-api/sqldescribecol.md) は、これらの列のデータ型として SQL_VARCHAR または SQL_VARBINARY を返します。 これらの列から取得するデータには、埋め込みは行われません。<br /><br /> 注: [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native CLIENT ODBC ドライバーでは、6.5 以前のバージョンへの接続がサポートされて [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] います。|  
|LONG データ型|*実行時データ* パラメーターは、SQL_LONGVARBINARY と SQL_LONGVARCHAR の両方のデータ型に対して制限されます。|  
|大きな値型|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]Native CLIENT odbc ドライバーでは、 **varchar (max)** 型、 **varbinary (max)** 型、および **nvarchar (max)** 型が、SQL_VARCHAR、SQL_VARBINARY、および SQL_WVARCHAR (それぞれ、ODBC SQL データ型を受け入れるか返す api) で公開されます。|  
|ユーザー定義型 (UDT)|UDT 列は SQL_SS_UDT としてマップされます。 SQL ステートメントで、UDT の ToString() メソッドまたは ToXMLString() メソッドを使用するか、CAST/CONVERT 関数を使用して UDT 列を明示的に別の型にマップする場合、結果セット内の列の型には変換された列の実際の型が反映されます。<br /><br /> [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]Native CLIENT ODBC ドライバーは、UDT 列にバイナリとしてのみバインドできます。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] でサポートされるのは、SQL_SS_UDT データ型と SQL_C_BINARY データ型の間の変換のみです。|  
|XML|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、XML を Unicode テキストに自動的に変換します。 XML 型は SQL_SS_XML としてマップされます。|  
  
## <a name="see-also"></a>参照  
 [ODBC&#41;&#40;結果の処理 ](../../relational-databases/native-client-odbc-results/processing-results-odbc.md)  
  
  
