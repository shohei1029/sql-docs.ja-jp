---
description: ODBC テーブル値パラメーターの使用
title: ODBC Table-Valued Parameters | の使用Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- table-valued parameters (ODBC), scenarios
- ODBC, table-valued parameters
ms.assetid: f1b73932-4570-4a8a-baa0-0f229d9c32ee
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: b0694bb36991fd0d9b8d7c9b652f6d7eaa93998a
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104755022"
---
# <a name="uses-of-odbc-table-valued-parameters"></a>ODBC テーブル値パラメーターの使用
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  このトピックでは、ODBC でテーブル値パラメーターを使用する主なユーザー シナリオについて説明します。  
  
-   複数行のバッファーに完全にバインドされるテーブル値パラメーター (すべての値をメモリ内に保持した TVP としてデータを送信する)  
  
-   行のストリーミングを使用するテーブル値パラメーター (実行時のデータを使用する TVP としてデータを送信する)  
  
-   システム カタログからテーブル値パラメーターのメタデータを取得  
  
-   準備されたステートメント用にテーブル値パラメーターのメタデータを取得  
  
## <a name="table-valued-parameter-with-fully-bound-multirow-buffers-send-data-as-a-tvp-with-all-values-in-memory"></a>複数行のバッファーに完全にバインドされるテーブル値パラメーター (すべての値をメモリ内に保持した TVP としてデータを送信する)  
 複数行のバッファーに完全にバインドされる形式で使用する場合、すべてのパラメーター値はメモリ内から使用できます。 このような形式は OLTP トランザクションなどでは一般的に行われ、テーブル値パラメーターを 1 つのストアド プロシージャにパッケージ化できます。 テーブル値パラメーターを使用しないと、複雑な複数のステートメントで構成されるバッチを動的に生成し、サーバーを複数回呼び出すことになります。  
  
 テーブル値パラメーター自体は、他のパラメーターと共に [SQLBindParameter](../../odbc/reference/syntax/sqlbindparameter-function.md) を使用してバインドされます。 すべてのパラメーターがバインドされると、アプリケーションは各テーブル値パラメーターにパラメーターフォーカス属性 SQL_SOPT_SS_PARAM_FOCUS を設定し、テーブル値パラメーターの列に対して SQLBindParameter を呼び出します。  
  
 テーブル値パラメーターのサーバー型は、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 固有の新しい型 SQL_SS_TABLE です。 SQL_SS_TABLE の C データ型へのバインドは、常に SQL_C_DEFAULT にする必要があります。 テーブル値パラメーターにバインドされたパラメーターについては、データが転送されません。このパラメーターは、テーブルのメタデータを渡し、テーブル値パラメーターを構成する列にデータを渡す方法を制御するために使用されます。  
  
 テーブル値パラメーターの長さには、サーバーに送信される行数が設定されます。 テーブル値パラメーターの SQLBindParameter の *Columnsize* パラメーターは、送信できる最大行数を指定します。これは、列バッファーの配列サイズです。 *Parametervalueptr* は、SQLBindParameter 内のテーブル値パラメーターのパラメーターバッファーです。 *Parametervalueptr* とそれに関連付けられている *bufferlength* は、必要に応じてテーブル値パラメーターの型名を渡すために使用されます。 型名は、ストアド プロシージャの呼び出しには必要ありませんが、SQL ステートメントには必要です。  
  
 SQLBindParameter の呼び出しでテーブル値パラメーターの型名を指定する場合は、ANSI アプリケーションとしてビルドされたアプリケーションであっても、常に Unicode 値として指定する必要があります。 SQLSetDescField を使用してテーブル値パラメーターの型名を指定する場合、アプリケーションのビルド方法に準拠したリテラルを使用できます。 ODBC ドライバー マネージャーで、必要な Unicode 変換を実行します。  
  
 テーブル値パラメーターおよびテーブル値パラメーターの列のメタデータは、SQLGetDescRec、SQLSetDescRec、SQLGetDescField、および SQLSetDescField を使用して、個別に、または明示的に操作できます。 ただし、SQLBindParameter のオーバーロードは通常、より便利であり、ほとんどの場合、明示的な記述子アクセスは必要ありません。 この方法は、他のデータ型の SQLBindParameter の定義と一致します。ただし、テーブル値パラメーターの場合は、影響を受ける記述子フィールドが若干異なります。  
  
 アプリケーションでは、場合によっては、動的な SQL を含むテーブル値パラメーターを使用して、テーブル値パラメーターの型名を指定する必要があります。 この場合、テーブル値パラメーターが接続の現在の既定のスキーマで定義されていない場合は、SQL_CA_SS_TYPE_CATALOG_NAME と SQL_CA_SS_TYPE_SCHEMA_NAME を SQLSetDescField を使用して設定する必要があります。 テーブル型の定義とテーブル値パラメーターは同じデータベースに存在する必要があるため、アプリケーションでテーブル値パラメーターを使用する場合は、SQL_CA_SS_TYPE_CATALOG_NAME を設定しないでください。 それ以外の場合、SQLSetDescField はエラーを報告します。  
  
 このシナリオのサンプルコードは、「 `demo_fixed_TVP_binding` [ODBC&#41;&#40;の Table-Valued パラメーターの使用 ](../../relational-databases/native-client-odbc-how-to/use-table-valued-parameters-odbc.md)」の手順に記載されています。  
  
## <a name="table-valued-parameter-with-row-streaming-send-data-as-a-tvp-using-data-at-execution"></a>行のストリーミングを使用するテーブル値パラメーター (実行時のデータを使用する TVP としてデータを送信する)  
 このシナリオでは、要求に応じてアプリケーションからドライバーに行が渡され、サーバーにストリーム送信されます。 これにより、すべての行をメモリ内にバッファリングする必要がなくなります。 これは、一括挿入や一括更新のシナリオの代表的な例です。 テーブル値パラメーターは、パフォーマンスの点ではパラメーター配列と一括コピーとの間に位置します。 つまり、テーブル値パラメーターは、パラメーター配列と同程度にプログラミングが容易ですが、サーバー側の柔軟性が増します。  
  
 テーブル値パラメーターとその列は、前の「複数行のバッファーに完全にバインドされるテーブル値パラメーター」で説明したとおりにバインドされますが、テーブル値パラメーター自体の長さのインジケーターは、SQL_DATA_AT_EXEC に設定されます。 ドライバーは、実行時データパラメーターの通常の方法で SQLExecute または SQLExecuteDirect に応答します。つまり、SQL_NEED_DATA を返します。 ドライバーがテーブル値パラメーターのデータを受け入れる準備が整うと、SQLParamData は SQLBindParameter の *Parametervalueptr* の値を返します。  
  
 アプリケーションでは、テーブル値パラメーターとして SQLPutData を使用して、テーブル値パラメーターの構成列のデータを使用できるかどうかを示します。 SQLPutData がテーブル値パラメーターに対して呼び出される場合、 *DataPtr* は常に null である必要があります。また、 *StrLen_or_Ind* は、テーブル値パラメーターのバッファーに指定された配列のサイズ (SQLBindParameter の *columnsize* パラメーター) 以下である必要があります。 0 はテーブル値パラメーターの行がなくなったことを示すため、ドライバーはプロシージャの次の実パラメーターの処理に進みます。 *StrLen_or_Ind* が0でない場合、ドライバーはテーブル値パラメーターではないパラメーターと同じ方法でテーブル値パラメーターを構成する列を処理します。各テーブル値パラメーターの列は、実際のデータ長、SQL_NULL_DATA を指定することも、長さ/インジケーターバッファーを使用して実行時にデータを指定することもできます。 文字またはバイナリ値を部分的に渡すときに、通常どおり SQLPutData を繰り返し呼び出すことで、テーブル値パラメーターの列の値を渡すことができます。  
  
 テーブル値パラメーターのすべての列が処理されたら、ドライバーはテーブル値パラメーターに戻り、テーブル値パラメーターのデータの次の行を処理します。 したがって、実行時データのテーブル値パラメーターの場合、バインドされたパラメーターを順番にスキャンする通常の方法には従いません。 バインドされたテーブル値パラメーターは、SQLPutData が0に等しい *StrLen_Or_IndPtr* 呼び出されるまでポーリングされます。このとき、ドライバーはテーブル値パラメーターの列をスキップし、次の実際のストアドプロシージャパラメーターに移動します。  SQLPutData が1以上のインジケーター値を渡すと、ドライバーは、バインドされたすべての行と列の値を持つまで、テーブル値パラメーターの列と行を順番に処理します。 その後、ドライバーはテーブル値パラメーターに戻ります。 テーブル値パラメーターのテーブル値パラメーターのトークンを受信してから、テーブル値パラメーターの Sqlparamdata (hstmt, NULL, n) を呼び出す場合、アプリケーションでは、サーバーに渡される次の行に対して、テーブル値パラメーターの構成列データとインジケーターバッファーの内容を設定する必要があります。  
  
 このシナリオのサンプルコードは、 `demo_variable_TVP_binding` [ODBC&#41;&#40;の Table-Valued パラメーターを使用する ](../../relational-databases/native-client-odbc-how-to/use-table-valued-parameters-odbc.md)ルーチンに含まれています。  
  
## <a name="retrieving-table-valued-parameter-metadata-from-the-system-catalog"></a>システム カタログからテーブル値パラメーターのメタデータを取得  
 アプリケーションが SQLProcedureColumns を呼び出して、テーブル値パラメーターのパラメーターを持つプロシージャを呼び出すと、DATA_TYPE が SQL_SS_TABLE として返され、TYPE_NAME はテーブル値パラメーターのテーブル型の名前になります。 SQLProcedureColumns によって返される結果セットには、次の2つの列が追加されます。 SS_TYPE_CATALOG_NAME は、テーブル値パラメーターのテーブル型が定義されているカタログの名前を返し、SS_TYPE_SCHEMA_NAME はテーブル値パラメーターのテーブル型が定義されているスキーマの名前を返します。 ODBC 仕様に準拠して、SS_TYPE_CATALOG_NAME と SS_TYPE_SCHEMA_NAME は、以前のバージョンので追加されたすべてのドライバー固有の列の前 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] と、odbc 自体によって指定されたすべての列の前に表示されます。  
  
 新しい列は、テーブル値パラメーター用だけでなく、CLR ユーザー定義型パラメーター用にも作成されます。 UDT パラメーターの既存のスキーマ列とカタログ列も依然として作成されますが、共通のスキーマ列とカタログ列を必要とするデータ型にもそれらの列を含めておくと、今後アプリケーション開発が簡単になります  (XML スキーマ コレクションは多少異なり、この変更には含まれていないことに注意してください)。  
  
 アプリケーションでは、永続的なテーブル、システムテーブル、およびビューと同じように、SQLTables を使用してテーブル型の名前を決定します。 アプリケーションでテーブル値パラメーターに関連付けられたテーブル型を識別できるように、新しいテーブル型として TABLE TYPE が導入されました。 テーブル型と通常のテーブルでは、異なる名前空間を使用します。 つまり、テーブル型と実際のテーブルに、同じ名前を使用できます。 これに対処するために、新しいステートメント属性として SQL_SOPT_SS_NAME_SCOPE が導入されました。 この属性は、テーブル名をパラメーターとして受け取る SQLTables およびその他のカタログ関数が、テーブル名を実際のテーブルの名前またはテーブル型の名前として解釈するかどうかを指定します。  
  
 アプリケーションでは、SQLColumns を使用して、永続テーブルの場合と同じ方法でテーブル型の列を決定しますが、最初に SQL_SOPT_SS_NAME_SCOPE を設定して、実際のテーブルではなくテーブル型を操作していることを示す必要があります。 SQLPrimaryKeys はテーブル型でも使用できますが、SQL_SOPT_SS_NAME_SCOPE を使用します。  
  
 このシナリオのサンプルコードは、 `demo_metadata_from_catalog_APIs` [ODBC&#41;&#40;の Table-Valued パラメーターを使用する ](../../relational-databases/native-client-odbc-how-to/use-table-valued-parameters-odbc.md)ルーチンに含まれています。  
  
## <a name="retrieving-table-valued-parameter-metadata-for-a-prepared-statement"></a>準備されたステートメント用にテーブル値パラメーターのメタデータを取得  
 このシナリオでは、アプリケーションは SQLNumParameters と SQLDescribeParam を使用して、テーブル値パラメーターのメタデータを取得します。  
  
 IPD フィールド SQL_CA_SS_TYPE_NAME は、テーブル値パラメーターの型名を取得するために使用されます。 IPD フィールド SQL_CA_SS_TYPE_SCHEMA_NAME と SQL_CA_SS_TYPE_CATALOG_NAME は、それぞれスキーマとカタログを取得するために使用されます。  
  
 テーブル型の定義とテーブル値パラメーターは同じデータベースに存在する必要があります。 SQLSetDescField は、テーブル値パラメーターの使用時にアプリケーションが SQL_CA_SS_TYPE_CATALOG_NAME を設定した場合にエラーを報告します。  
  
 SQL_CA_SS_TYPE_CATALOG_NAME および SQL_CA_SS_TYPE_SCHEMA_NAME を使用すると、CLR ユーザー定義型パラメーターに関連付けられたカタログとスキーマも取得できます。 SQL_CA_SS_TYPE_CATALOG_NAME および SQL_CA_SS_TYPE_SCHEMA_NAME は、CLR UDT 型の既存の型固有のカタログ スキーマの属性の代わりに使用します。  
  
 このシナリオでは、SQLDescribeParam がテーブル値パラメーター列の列のメタデータを返さないため、アプリケーションは SQLColumns を使用してテーブル値パラメーターの列メタデータを取得します。  
  
 このユースケースのサンプルコードは、 `demo_metadata_from_prepared_statement` [ODBC&#41;&#40;の Table-Valued パラメーターを使用する ](../../relational-databases/native-client-odbc-how-to/use-table-valued-parameters-odbc.md)ルーチンに含まれています。  
  
## <a name="see-also"></a>参照  
 [テーブル値パラメーター &#40;ODBC&#41;](../../relational-databases/native-client-odbc-table-valued-parameters/table-valued-parameters-odbc.md)  
  
