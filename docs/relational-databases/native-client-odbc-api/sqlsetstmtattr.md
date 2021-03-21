---
description: SQLSetStmtAttr
title: SQLSetStmtAttr |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
apitype: DLLExport
helpviewer_keywords:
- SQLSetStmtAttr function
ms.assetid: 799c80fd-c561-4912-8562-9229076dfd19
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: a9005450f9dcb700a08b70820e75ddc8d3f83d90
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104754862"
---
# <a name="sqlsetstmtattr"></a>SQLSetStmtAttr
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client ODBC ドライバーは、混合 (キーセット/動的) カーソル モデルをサポートしません。 SQL_ATTR_KEYSET_SIZE を使用してキーセットのサイズを設定する場合、0 以外の値を設定すると失敗します。  
  
 このアプリケーションでは、 **sqlfetch** または [sqlfetchscroll](../../relational-databases/native-client-odbc-api/sqlfetchscroll.md) 関数呼び出しで返される行の数を宣言するために、すべてのステートメントに SQL_ATTR_ROW_ARRAY_SIZE を設定します。 ドライバーは、サーバー カーソルを指定するステートメントで SQL_ATTR_ROW_ARRAY_SIZE を使用して、カーソルからのフェッチ要求を満たすためにサーバーが生成する行ブロックのサイズを判断します。 トランザクションの分離レベルが、コミット済みのトランザクションの反復可能読み取りを保証できるレベルの場合、行のメンバーシップや順序が、動的カーソルのブロック サイズに収まる範囲内で固定されます。 カーソルは、この値で示されるブロック外では完全に動的になります。 サーバー カーソルのブロック サイズは完全に動的で、フェッチ処理のどの時点でも変更可能です。  
  
## <a name="sqlsetstmtattr-and-table-valued-parameters"></a>SQLSetStmtAttr とテーブル値パラメーター  
 SQLSetStmtAttr を使用すると、テーブル値パラメーターの列の記述子フィールドにアクセスする前に、アプリケーションパラメーター記述子 (APD) で SQL_SOPT_SS_PARAM_FOCUS を設定できます。  
  
 SQL_SOPT_SS_PARAM_FOCUS をテーブル値パラメーターではないパラメーターの序数に設定しようとした場合、SQLSetStmtAttr は SQL_ERROR を返し、"属性値が無効です" というメッセージで SQLSTATE = HY024 の診断レコードが作成されます。 SQL_SOPT_SS_PARAM_FOCUS は、SQL_ERROR が返されたときに変更されません。  
  
 SQL_SOPT_SS_PARAM_FOCUS に 0 を設定すると、パラメーターの記述子レコードへのアクセスが復元されます。  
  
 SQLSetStmtAttr を使用して SQL_SOPT_SS_NAME_SCOPE を設定することもできます。 詳細については、このトピックの後半の「SQL_SOPT_SS_NAME_SCOPE」のセクションを参照してください。  
  
 詳細については、「準備された [ステートメントのテーブル値パラメーターのメタデータ](../../relational-databases/native-client-odbc-table-valued-parameters/table-valued-parameter-metadata-for-prepared-statements.md)」を参照してください。  
  
 テーブル値パラメーターの詳細については、「 [テーブル値パラメーター &#40;ODBC&#41;](../../relational-databases/native-client-odbc-table-valued-parameters/table-valued-parameters-odbc.md)」を参照してください。  
  
## <a name="sqlsetstmtattr-support-for-sparse-columns"></a>SQLSetStmtAttr によるスパース列のサポート  
 SQLSetStmtAttr を使用して SQL_SOPT_SS_NAME_SCOPE を設定できます。 詳細については、このトピックの「SQL_SOPT_SS_NAME_SCOPE」を参照してください。スパース列の詳細については、「 [スパース列のサポート &#40;ODBC&#41;](../../relational-databases/native-client/odbc/sparse-columns-support-odbc.md)」を参照してください。  
  
## <a name="statement-attributes"></a>ステートメント属性  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client ODBC ドライバーは、次のドライバー固有のステートメント属性もサポートします。  
  
### <a name="sql_sopt_ss_cursor_options"></a>SQL_SOPT_SS_CURSOR_OPTIONS  
 カーソルでのドライバー固有のパフォーマンス オプションを使用するかどうかを指定します。 これらのオプションが設定されている場合、 [SQLGetData](../../relational-databases/native-client-odbc-api/sqlgetdata.md)は許可されません。 既定の設定は SQL_CO_OFF です。 *Valueptr* 値の型は SQLLEN です。  
  
|*Valueptr* 値|説明|  
|----------------------|-----------------|  
|SQL_CO_OFF|既定値。 高速順方向専用、読み取り専用のカーソル、および autofetch を無効にして、前方参照専用の読み取り専用カーソルに対して **SQLGetData** を有効にします。 SQL_SOPT_SS_CURSOR_OPTIONS を SQL_CO_OFF に設定すると、カーソルの種類は変更されません。 つまり、高速順方向専用カーソルは高速順方向専用カーソルのままです。 カーソルの種類を変更するには、 **SQLSetStmtAttr**/SQL_ATTR_CURSOR_TYPE を使用して、アプリケーションで別の種類のカーソルを設定する必要があります。|  
|SQL_CO_FFO|高速順方向専用の読み取り専用カーソルを有効にし、順方向専用、読み取り専用のカーソルでの **SQLGetData** を無効にします。|  
|SQL_CO_AF|すべてのカーソルの種類で autofetch オプションを有効にします。 このオプションがステートメントハンドルに対して設定されている場合、 **Sqlexecute** または **SQLExecDirect** は暗黙的な **sqlfetchscroll** (SQL_FIRST) を生成します。 カーソルが開かれ、最初の行バッチが 1 回のラウンドトリップでサーバーに返されます。|  
|SQL_CO_FFO_AF|autofetch オプションを設定して高速順方向専用カーソルを有効にします。 これは、SQL_CO_AF と SQL_CO_FFO の両方を指定した場合と同じです。|  
  
 これらのオプションを設定すると、サーバーは最後の行がフェッチされたことを検出した時点で、カーソルを自動的に閉じます。 アプリケーションは依然として [SQLFreeStmt](../../relational-databases/native-client-odbc-api/sqlfreestmt.md) (SQL_CLOSE) または [sqlcloを](../../relational-databases/native-client-odbc-api/sqlclosecursor.md)呼び出す必要がありますが、ドライバーは、サーバーに終了通知を送信する必要はありません。  
  
 Select リストに **text**、 **ntext**、または **image** 列が含まれている場合、高速順方向専用カーソルは動的カーソルに変換され、 **SQLGetData** は許可されます。  
  
### <a name="sql_sopt_ss_defer_prepare"></a>SQL_SOPT_SS_DEFER_PREPARE  
 SQL_SOPT_SS_DEFER_PREPARE 属性は、ステートメントを直ちに準備するか、 **Sqlexecute**、 [SQLDescribeCol](../../relational-databases/native-client-odbc-api/sqldescribecol.md) 、または [SQLDescribeParam](../../relational-databases/native-client-odbc-api/sqldescribeparam.md) が実行されるまで遅延させるかを決定します。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 7.0 以前のバージョンでは、このプロパティは無視されます (準備は遅延されません)。 *Valueptr* 値の型は SQLLEN です。  
  
|*Valueptr* 値|説明|  
|----------------------|-----------------|  
|SQL_DP_ON|既定値。 [SQLPrepare 関数](../../odbc/reference/syntax/sqlprepare-function.md)を呼び出した後、 **sqlexecute** が呼び出されるか、メタプロパティ操作 (**SQLDescribeCol** または **SQLDescribeParam**) が実行されるまで、ステートメントの準備は遅延されます。|  
|SQL_DP_OFF|**SQLPrepare** が実行されるとすぐに、ステートメントが準備されます。|  
  
### <a name="sql_sopt_ss_regionalize"></a>SQL_SOPT_SS_REGIONALIZE  
 ステートメント レベルのデータ変換を決定します。 この属性を設定すると、日付、時刻、通貨の値を文字列値に変換する際に、ドライバーはクライアントのロケール設定を使用します。 この場合の変換は、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ネイティブ データ型から文字列への変換のみです。  
  
 *Valueptr* 値の型は SQLLEN です。  
  
|*Valueptr* 値|説明|  
|----------------------|-----------------|  
|SQL_RE_OFF|既定値。 ドライバーは、日付、時刻、通貨の値を文字列データに変換する際に、クライアントのロケール設定を使用しません。|  
|SQL_RE_ON|ドライバーは、日付、時刻、通貨の値を文字列データに変換する際に、クライアントのロケール設定を使用します。|  
  
 地域別の変換の設定は、通貨、数値、日付、および時刻のデータ型に適用されます。 変換の設定は、通貨、数値、日付、または時刻の値を文字列に変換するときの出力変換にのみ適用されます。  
  
> [!NOTE]  
>  ステートメント オプション SQL_SOPT_SS_REGIONALIZE が有効な場合、ドライバーは現在のユーザーのロケール レジストリ設定を使用します。 アプリケーションが **Setthreadlocale** を呼び出すなどして、現在のスレッドのロケールを設定している場合、ドライバーはそのロケールを優先しません。  
  
 データ ソースの地域別の動作を変更すると、アプリケーション エラーが発生することがあります。 日付文字列を解析し、ODBC の定義に従った形式の日付文字列を受け付けるアプリケーションは、地域別の動作の値を変更することによって、悪影響を受ける可能性があります。  
  
### <a name="sql_sopt_ss_textptr_logging"></a>SQL_SOPT_SS_TEXTPTR_LOGGING  
 SQL_SOPT_SS_TEXTPTR_LOGGING 属性は、 **テキスト** または **イメージ** データを含む列に対する操作のログ記録を切り替えます。 *Valueptr* 値の型は SQLLEN です。  
  
|*Valueptr* 値|説明|  
|----------------------|-----------------|  
|SQL_TL_OFF|**テキスト** および **イメージ** データに対して実行される操作のログ記録を無効にします。|  
|SQL_TL_ON|既定値。 **テキスト** および **イメージ** データに対して実行される操作のログ記録を有効にします。|  
  
### <a name="sql_sopt_ss_hidden_columns"></a>SQL_SOPT_SS_HIDDEN_COLUMNS  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の SELECT FOR BROWSE ステートメントで非表示にされた列を結果セットに公開します。 既定では、ドライバーはこのような列を公開しません。 *Valueptr* 値の型は SQLLEN です。  
  
|*Valueptr* 値|説明|  
|----------------------|-----------------|  
|SQL_HC_OFF|既定値。 FOR BROWSE 列が結果セットで非表示になります。|  
|SQL_HC_ON|FOR BROWSE 列を公開します。|  
  
### <a name="sql_sopt_ss_querynotification_msgtext"></a>SQL_SOPT_SS_QUERYNOTIFICATION_MSGTEXT  
 クエリ通知要求のメッセージ テキストを返します。  
  
### <a name="sql_sopt_ss_querynotification_options"></a>SQL_SOPT_SS_QUERYNOTIFICATION_OPTIONS  
 クエリ通知要求で使用するオプションを指定します。 オプションは、次に示すように、`name=value` 構文を使用した文字列で指定します。 アプリケーションがサービスを作成して、キューから通知を読み取る必要があります。  
  
 クエリ通知オプションの構文を次に示します。  
  
 `service=<service-name>[;(local database=<database>|broker instance=<broker instance>)]`  
  
 次に例を示します。  
  
 `service=mySSBService;local database=mydb`  
  
### <a name="sql_sopt_ss_querynotification_timeout"></a>SQL_SOPT_SS_QUERYNOTIFICATION_TIMEOUT  
 クエリ通知をアクティブのままにしておく秒数を指定します。 既定値は432000秒 (5 日) です。 *Valueptr* 値の型は SQLLEN です。  
  
### <a name="sql_sopt_ss_param_focus"></a>SQL_SOPT_SS_PARAM_FOCUS  
 SQL_SOPT_SS_PARAM_FOCUS 属性は、後続の SQLBindParameter、SQLGetDescField、SQLSetDescField、SQLGetDescRec、および SQLSetDescRec の呼び出しのフォーカスを指定します。  
  
 SQL_SOPT_SS_PARAM_FOCUS の型は SQLULEN です。  
  
 既定値は 0 で、SQL ステートメントのパラメーター マーカーに対応するパラメーターが、上記の呼び出しで指定されます。 テーブル値パラメーターのパラメーター番号に設定すると、そのテーブル値パラメーターの列が上記の呼び出しで指定されます。 テーブル値パラメーターのパラメーター番号以外の値に設定すると、エラー IM020 ("パラメーターのフォーカスがテーブル値パラメーターを参照していません") が返されます。  
  
### <a name="sql_sopt_ss_name_scope"></a>SQL_SOPT_SS_NAME_SCOPE  
 SQL_SOPT_SS_NAME_SCOPE 属性は、後続のカタログ関数呼び出しの名前スコープを指定します。 SQLColumns によって返される結果セットは、SQL_SOPT_SS_NAME_SCOPE の設定によって異なります。  
  
 SQL_SOPT_SS_NAME_SCOPE の型は SQLULEN です。  
  
|*Valueptr* 値|説明|  
|----------------------|-----------------|  
|SQL_SS_NAME_SCOPE_TABLE|既定値。<br /><br /> テーブル値パラメーターを使用する場合は、実際のテーブルのメタデータが返される必要があることを示します。<br /><br /> スパース列機能を使用する場合、SQLColumns は、スパース **column_set** のメンバーではない列のみを返します。|  
|SQL_SS_NAME_SCOPE_TABLE_TYPE|アプリケーションが実際のテーブルではなくテーブル型のメタデータを必要としていること (テーブル型のメタデータが返される必要があること) を示します。 その後、アプリケーションはテーブル値パラメーターの TYPE_NAME を *TableName* パラメーターとして渡します。|  
|SQL_SS_NAME_SCOPE_EXTENDED|スパース列機能を使用する場合、 **column_set** のメンバーシップに関係なく、sqlcolumns はすべての列を返します。|  
|SQL_SS_NAME_SCOPE_SPARSE_COLUMN_SET|スパース列機能を使用する場合、SQLColumns は、スパース **column_set** のメンバーである列のみを返します。|  
|SQL_SS_NAME_SCOPE_DEFAULT|SQL_SS_NAME_SCOPE_TABLE と同等です。|  
  
 SS_TYPE_CATALOG_NAME と SS_TYPE_SCHEMA_NAME は、 *CatalogName* パラメーターと *SchemaName* パラメーターでそれぞれ使用され、テーブル値パラメーターのカタログとスキーマを識別します。 テーブル値パラメーターのメタデータの取得が完了すると、アプリケーションによって、SQL_SOPT_SS_NAME_SCOPE は既定値 SQL_SS_NAME_SCOPE_TABLE に設定し直す必要があります。  
  
 SQL_SOPT_SS_NAME_SCOPE が SQL_SS_NAME_SCOPE_TABLE に設定されると、リンク サーバーへのクエリは失敗します。 サーバーコンポーネントを含むカタログを使用した SQLColumns または Sqlprimarykey の呼び出しは失敗します。  
  
 SQL_SOPT_SS_NAME_SCOPE を無効な値に設定しようとすると、SQL_ERROR が返され、"属性の値が正しくありません" というメッセージを含む SQLSTATE HY024 の診断レコードが生成されます。  
  
 SQL_SOPT_SS_NAME_SCOPE に SQL_SS_NAME_SCOPE_TABLE 以外の値が指定されている場合に、SQLTables、Sqltables、SQLPrimaryKeys などのカタログ関数が呼び出されると、SQL_ERROR が返されます。 "関数のシーケンス エラーです (SQL_SOPT_SS_NAME_SCOPE が SQL_SS_NAME_SCOPE_TABLE に設定されていません)" というメッセージを含む SQLSTATE HY010 の診断レコードが生成されます。  
  
## <a name="see-also"></a>参照  
 [SQLGetStmtAttr 関数](../../odbc/reference/syntax/sqlgetstmtattr-function.md)   
 [ODBC API 実装の詳細](../../relational-databases/native-client-odbc-api/odbc-api-implementation-details.md)  
  
