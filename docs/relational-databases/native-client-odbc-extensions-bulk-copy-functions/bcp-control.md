---
description: bcp_control
title: bcp_control |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
apiname:
- bcp_control
apilocation:
- sqlncli11.dll
apitype: DLLExport
helpviewer_keywords:
- bcp_control function
ms.assetid: 32187282-1385-4c52-9134-09f061eb44f5
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 335dd32c4d3ba62c9aae09cc38adb4383d1f71fd
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104749582"
---
# <a name="bcp_control"></a>bcp_control
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  ファイルと [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の間の一括コピーに使用するさまざまな制御パラメーターの既定の設定を変更します。  
  
## <a name="syntax"></a>構文  
  
```  
  
RETCODE bcp_control (  
        HDBC hdbc,  
        INT eOption,  
        void* iValue);  
```  
  
## <a name="arguments"></a>引数  
 *hdbc*  
 一括コピーが有効な ODBC 接続ハンドルです。  
  
 *eOption*  
 は次のいずれかです。  
  
 BCPABORT   
 既に実行中の一括コピー操作を停止します。 別のスレッドからの BCPABORT の *eOption* を使用して **bcp_control** を呼び出し、実行中の一括コピー操作を停止します。 *Ivalue* パラメーターは無視されます。  
  
 BCPBATCH   
 バッチごとの行数を指定します。 既定値は 0 です。既定値を指定すると、データを抽出するときはテーブル内のすべての行が抽出されることを示し、データを [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] にコピーするときはユーザー データ ファイル内のすべての行がコピーされることを示します。 1 より小さい値を指定すると、BCPBATCH は既定値にリセットされます。  
  
 BCPDELAYREADFMT  
 ブール値を true に設定すると、 [bcp_readfmt](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-readfmt.md) が実行時に読み込まれます。 False (既定値) の場合、bcp_readfmt はすぐにフォーマットファイルを読み取ります。 BCPDELAYREADFMT が true で bcp_columns または bcp_setcolfmt を呼び出すと、シーケンスエラーが発生します。  
  
 `bcp_control(hdbc,` `, (void *)FALSE)` Bcpdelayreadfmt および bcp_writefmt を呼び出した後に bcpdelayreadfmt を呼び出した場合も、シーケンスエラーが発生し `bcp_control(hdbc,` `, (void *)TRUE)` ます。  
  
 詳細については、「[メタデータの検出](../../relational-databases/native-client/features/metadata-discovery.md)」を参照してください。  
  
 BCPFILECP  
 *Ivalue* には、データファイルのコードページの番号が含まれています。 1252 や 850 などのコード ページ番号を指定するか、次のいずれかの値を指定できます。  
  
 BCPFILE_ACP を指定すると、ファイル内のデータには、クライアントの Microsoft Windows&#xAE; コード ページが使用されます。  
  
 BCPFILE_OEMCP を指定すると、ファイル内のデータには、クライアントの OEM コード ページ (既定) が使用されます。  
  
 BCPFILE_RAW を指定すると、ファイル内のデータには、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のコード ページが使用されます。  
  
 BCPFILEFMT  
 データ ファイル形式のバージョン番号を指定します。 80 ( [!INCLUDE[ssVersion2000](../../includes/ssversion2000-md.md)] )、90 ( [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] )、100 ( [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] または [!INCLUDE[ssKilimanjaro](../../includes/sskilimanjaro-md.md)] )、110 ( [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] )、または 120 () [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] を指定できます。 120 が既定値です。 このオプションは、以前のバージョンのサーバーでサポートされていた形式でデータをエクスポートおよびインポートする際に便利です。 たとえば、サーバーのテキスト列から取得したデータを、以降の [!INCLUDE[ssVersion2000](../../includes/ssversion2000-md.md)] サーバーの **varchar (max)** 列にインポートするには、 [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] 80 を指定する必要があります。 同様に、 **varchar (max)** 列からデータをエクスポートするときに80を指定した場合は、テキスト列が形式で保存され、 [!INCLUDE[ssVersion2000](../../includes/ssversion2000-md.md)] サーバーのテキスト列にインポートできるのと同じように保存され [!INCLUDE[ssVersion2000](../../includes/ssversion2000-md.md)] ます。  
  
 BCPFIRST   
 ファイルまたはテーブルにコピーする最初のデータ行を指定します。 既定値は 1 です。1 未満の値を指定すると、このオプションは既定値にリセットされます。  
  
 BCPFIRSTEX  
 BCP out 操作の場合は、データ ファイルにコピーするための、データベース テーブルの最初の行を指定します。  
  
 BCP in 操作の場合は、データベース テーブルにコピーするための、データ ファイルの最初の行を指定します。  
  
 *Ivalue* パラメーターは、値を含む符号付き64ビット整数のアドレスである必要があります。 BCPFIRSTEX に渡すことができる最大値は 2 ^ 63-1 です。  
  
 BCPFMTXML  
 XML 形式のフォーマット ファイルが生成されることを指定します。 既定では無効になっています。  
  
 XML フォーマットファイルには柔軟性がありますが、制約が追加されています。 たとえば、フィールドのプレフィックスとターミネータを同時に指定することはできません。これは以前のフォーマットファイルでは可能でした。  
  
> [!NOTE]  
>  XML フォーマット ファイルがサポートされるのは、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] を [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client と共にインストールした場合だけです。  
  
 BCPHINTS  
 *Ivalue* には、sqltchar 文字列ポインターが含まれています。 ポインターが指す文字列には、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 一括コピー処理ヒント、または結果セットを返す Transact-SQL ステートメントを指定します。 複数の結果セットを返す Transact-SQL ステートメントを指定すると、1 つ目以外の結果セットはすべて無視されます。 一括コピー処理のヒントの詳細については、「 [Bcp ユーティリティ](../../tools/bcp-utility.md)」を参照してください。  
  
 BCPKEEPIDENTITY  
 *Ivalue* が TRUE の場合、一括コピー関数は、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] id 制約で定義された列に対して指定されたデータ値を挿入するように指定します。 入力ファイルには ID 列の値を指定する必要があります。 このオプションを設定しないと、挿入される行に対して新しい ID 値が生成されます。 ファイル内に存在する ID 列用のデータはすべて無視されます。  
  
 BCPKEEPNULLS   
 ファイル内の空のデータ値を [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] テーブルで NULL 値に変換するかどうかを指定します。 *Ivalue* が TRUE の場合、空の値はテーブルで NULL に変換され [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。 既定では、空の値は [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] テーブル内の列の既定値 (存在する場合) に変換されます。  
  
 BCPLAST   
 コピーする最後の行を示します。 既定では、すべての行がコピーされます。1 より小さい値を指定すると、このオプションは既定値にリセットされます。  
  
 BCPLASTEX  
 BCP out 操作の場合は、データ ファイルにコピーするための、データベース テーブルの最後の行を指定します。  
  
 BCP in 操作の場合は、データベース テーブルにコピーするための、データ ファイルの最後の行を指定します。  
  
 *Ivalue* パラメーターは、値を含む符号付き64ビット整数のアドレスである必要があります。 BCPLASTEX に渡すことができる最大値は 2^63-1 です。  
  
 BCPMAXERRS   
 一括コピー操作が失敗するまでに発生してもかまわないエラーの数です。 既定値は10です。1未満の値を指定すると、このオプションが既定値にリセットされます。 一括コピーでは、最大 65,535 個のエラーが許容されます。 このオプションに 65,535 を超える値を設定しようとすると、65,535 が設定されます。  
  
 BCPODBC  
 TRUE の場合、文字形式で保存される **datetime** と **smalldatetime** の値が、ODBC タイムスタンプエスケープシーケンスのプレフィックスとサフィックスを使用するように指定します。 BCPODBC.BCP オプションは DB_OUT にのみ適用されます。  
  
 FALSE の場合、1997年1月1日を表す **datetime** 値は、1997-01-01 00:00: 00.000 という文字列に変換されます。 TRUE の場合、同じ **datetime** 値は {ts ' 1997-01-01 00:00: 00.000 '} と表されます。  
  
 BCPROWCOUNT  
 現在 (または最後) の BCP 操作で処理された行数を返します。  
  
 BCPTEXTFILE  
 TRUE の場合、データ ファイルがバイナリ ファイルではなくテキスト ファイルであることを示します。 ファイルがテキスト ファイルの場合、BCP では、データ ファイルの先頭 2 バイトに含まれる Unicode バイト マーカーをチェックして、そのファイルが Unicode 形式かどうかを判断します。  
  
 BCPUNICODEFILE  
 TRUE の場合、入力ファイルが Unicode ファイルであることを指定します。  
  
 *iValue*  
 指定した *eOption* の値を指定します。 *Ivalue* は、後で64ビット値に拡張できるように void ポインターにキャストされた整数 (longlong) 値です。  
  
## <a name="returns"></a>戻り値  
 SUCCEED または FAIL。  
  
## <a name="remarks"></a>解説  
 この関数では、一括コピー操作のさまざまな制御パラメーターを設定します。たとえば、一括コピーが取り消されるまでに発生してもかまわないエラーの数、データ ファイルから最初にコピーする行番号や最後にコピーする行番号、バッチ サイズなどを設定します。  
  
 また、この関数は、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] から SELECT ステートメントの結果セットを一括コピーするときに、その SELECT ステートメントを指定するためにも使用します。 *EOption* を BCPHINTS に設定し、 *ivalue* を SET ステートメントを含む sqltchar 文字列へのポインターを持つように設定します。  
  
 これらの制御パラメーターは、ユーザー ファイルと [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] テーブルの間でコピーを行う場合のみ意味があります。 コントロールパラメーターの設定は、bcp_sendrow でにコピーされる行には影響しません [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。 [](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-sendrow.md)  
  
## <a name="example"></a>例  
  
```  
// Variables like henv not specified.  
SQLHDBC      hdbc;  
DBINT      nRowsProcessed;  
  
// Application initiation, get an ODBC environment handle, allocate the  
// hdbc, and so on.  
...   
  
// Enable bulk copy prior to connecting on allocated hdbc.  
SQLSetConnectAttr(hdbc, SQL_COPT_SS_BCP, (SQLPOINTER) SQL_BCP_ON,  
   SQL_IS_INTEGER);  
  
// Connect to the data source, return on error.  
if (!SQL_SUCCEEDED(SQLConnect(hdbc, _T("myDSN"), SQL_NTS,  
   _T("myUser"), SQL_NTS, _T("myPwd"), SQL_NTS)))  
   {  
   // Raise error and return.  
   return;  
   }  
  
// Initialize bulk copy.   
if (bcp_init(hdbc, _T("address"), _T("address.add"), _T("addr.err"),  
   DB_IN) == FAIL)  
   {  
   // Raise error and return.  
   return;  
   }  
  
// Set the number of rows per batch.   
if (bcp_control(hdbc, BCPBATCH, (void*) 1000) == FAIL)  
   {  
   // Raise error and return.  
   return;  
   }  
  
// Set file column count.   
if (bcp_columns(hdbc, 1) == FAIL)  
   {  
   // Raise error and return.  
   return;  
   }  
  
// Set the file format.   
if (bcp_colfmt(hdbc, 1, 0, 0, SQL_VARLEN_DATA, '\n', 1, 1)  
   == FAIL)  
   {  
   // Raise error and return.  
   return;  
   }  
  
// Execute the bulk copy.   
if (bcp_exec(hdbc, &nRowsProcessed) == FAIL)  
   {  
   // Raise error and return.  
   return;  
   }  
  
printf_s("%ld rows processed by bulk copy.", nRowsProcessed);  
  
```  
  
## <a name="see-also"></a>参照  
 [一括コピー関数](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/sql-server-driver-extensions-bulk-copy-functions.md)  
  
  
