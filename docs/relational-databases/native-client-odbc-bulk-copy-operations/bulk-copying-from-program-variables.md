---
description: プログラム変数からの一括コピー
title: プログラム変数からの一括コピー |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- bulk copy [ODBC], program variables
- bcp_bind function
- mapping data types [ODBC]
- SQL Server Native Client ODBC driver, bulk copy
- data types [ODBC], bulk copying
- ODBC, bulk copy operations
- program variables [ODBC]
ms.assetid: e4284a1b-7534-4b34-8488-b8d05ed67b8c
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 92ba50ba85056dc9a55bc68f91a370015ec91f2a
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104754772"
---
# <a name="bulk-copying-from-program-variables"></a>プログラム変数からの一括コピー
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  プログラム変数から直接一括コピーできます。 行のデータを保持し、 [bcp_init](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-init.md) を呼び出して一括コピーを開始する変数を割り当てた後、各列に対して [bcp_bind](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-bind.md) を呼び出して、列に関連付けられるプログラム変数の場所と形式を指定します。 各変数にデータを入力し、 [bcp_sendrow](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-sendrow.md) を呼び出して、サーバーに1行のデータを送信します。 すべての行がサーバーに送信されるまで、変数の入力と **bcp_sendrow** の呼び出しを繰り返します。次に、 [bcp_done](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-done.md) を呼び出して、操作が完了したことを指定します。  
  
 **Bcp_bind**_pData_ パラメーターには、列にバインドされている変数のアドレスが含まれています。 各列のデータは、次の 2 つの方法のいずれかを使用して格納できます。  
  
-   データを保持する変数を 1 つ割り当てる  
  
-   データ変数の直前にインジケーター変数を割り当てる  
  
 インジケーター変数は、可変長列のデータの長さを示します。列で NULL 値を許容している場合は NULL 値も示します。 データ変数のみが使用されている場合は、この変数のアドレスが **bcp_bind**_pData_ パラメーターに格納されます。 インジケーター変数が使用されている場合は、インジケーター変数のアドレスが **bcp_bind**_pData_ パラメーターに格納されます。 一括コピー関数は、 **bcp_bind**_Cbindicator_ パラメーターと *pData* パラメーターを追加することで、データ変数の場所を計算します。  
  
 **bcp_bind** は、可変長データを処理する3つの方法をサポートしています。  
  
-   データ変数のみを含む *Cbdata* を使用します。 データの長さを *cbdata* に配置します。 一括コピーされるデータの長さが変わるたびに、 [bcp_collen](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-collen.md)を呼び出して *cbdata* をリセットします。 他の2つの方法のいずれかを使用している場合は、 *Cbdata* の SQL_VARLEN_DATA を指定します。 列に指定されているすべてのデータ値が NULL の場合は、 *Cbdata* の SQL_NULL_DATA を指定します。  
  
-   インジケーター変数を使用します。 新しい各データ値をデータ変数に移動するときに、その値の長さをインジケーター変数に格納します。 他の2つのメソッドのいずれかを使用している場合は、 *Cbindicator* に0を指定します。  
  
-   ターミネータ ポインターを使用します。 データを終了するビットパターンのアドレスを使用して **bcp_bind**_pterm_ パラメーターを読み込みます。 他の2つのメソッドのいずれかを使用している場合は、 *Pterm* に NULL を指定します。  
  
 これら3つのメソッドはすべて同じ **bcp_bind** 呼び出しで使用できます。その場合、コピーされるデータ量が最小になる仕様が使用されます。  
  
 **Bcp_bind**_type_ パラメーターは、ODBC データ型識別子ではなく、DB-Library データ型識別子を使用します。 DB-Library のデータ型識別子は、ODBC **bcp_bind** 関数で使用するために sqlncli で定義されています。  
  
 一括コピー関数では、すべての ODBC C データ型をサポートしているわけではありません。 たとえば、一括コピー関数では ODBC SQL_C_TYPE_TIMESTAMP 構造がサポートされていないため、 [SQLBindCol](../../relational-databases/native-client-odbc-api/sqlbindcol.md) または [SQLGetData](../../relational-databases/native-client-odbc-api/sqlgetdata.md) を使用して odbc SQL_TYPE_TIMESTAMP データを SQL_C_CHAR 変数に変換します。 その後 **bcp_bind** を sqlcharacter の *型* パラメーターと共に使用して、変数を [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] **datetime** 列にバインドすると、一括コピー関数は、文字変数内の timestamp エスケープ句を適切な datetime 形式に変換します。  
  
 次の表に、ODBC SQL データ型から [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] データ型へのマッピングを行う際に使用が推奨されるデータ型を示します。  
  
|ODBC SQL データ型|ODBC C データ型|bcp_bind *型* パラメーター|SQL Server のデータ型|  
|-----------------------|----------------------|--------------------------------|--------------------------|  
|SQL_CHAR|SQL_C_CHAR|SQLCHARACTER|**character**<br /><br /> **char**|  
|SQL_VARCHAR|SQL_C_CHAR|SQLCHARACTER|**varchar**<br /><br /> **文字の変化**<br /><br /> **char varying**<br /><br /> **sysname**|  
|SQL_LONGVARCHAR|SQL_C_CHAR|SQLCHARACTER|**text**|  
|SQL_WCHAR|SQL_C_WCHAR|SQLNCHAR|**nchar**|  
|SQL_WVARCHAR|SQL_C_WCHAR|SQLNVARCHAR|**nvarchar**|  
|SQL_WLONGVARCHAR|SQL_C_WCHAR|SQLNTEXT|**ntext**|  
|SQL_DECIMAL|SQL_C_CHAR|SQLCHARACTER|**decimal**<br /><br /> **alpha**<br /><br /> **money**<br /><br /> **smallmoney**|  
|SQL_NUMERIC|SQL_C_NUMERIC|SQLNUMERICN|**numeric**|  
|SQL_BIT|SQL_C_BIT|SQLBIT|**bit**|  
|SQL_TINYINT (符号付き)|SQL_C_SSHORT|SQLINT2|**smallint**|  
|SQL_TINYINT (符号なし)|SQL_C_UTINYINT|SQLINT1|**tinyint**|  
|SQL_SMALL_INT (符号付き)|SQL_C_SSHORT|SQLINT2|**smallint**|  
|SQL_SMALL_INT (符号なし)|SQL_C_SLONG|SQLINT4|**int**<br /><br /> **integer**|  
|SQL_INTEGER (符号付き)|SQL_C_SLONG|SQLINT4|**int**<br /><br /> **integer**|  
|SQL_INTEGER (符号なし)|SQL_C_CHAR|SQLCHARACTER|**decimal**<br /><br /> **alpha**|  
|SQL_BIGINT (符号付きと符号なし)|SQL_C_CHAR|SQLCHARACTER|**bigint**|  
|SQL_REAL|SQL_C_FLOAT|SQLFLT4|**real**|  
|SQL_FLOAT|SQL_C_DOUBLE|SQLFLT8|**float**|  
|SQL_DOUBLE|SQL_C_DOUBLE|SQLFLT8|**float**|  
|SQL_BINARY|SQL_C_BINARY|SQLBINARY|**[バイナリ]**<br /><br /> **timestamp**|  
|SQL_VARBINARY|SQL_C_BINARY|SQLBINARY|**varbinary**<br /><br /> **バイナリの変化**|  
|SQL_LONGVARBINARY|SQL_C_BINARY|SQLBINARY|**image**|  
|SQL_TYPE_DATE|SQL_C_CHAR|SQLCHARACTER|**datetime**<br /><br /> **smalldatetime**|  
|SQL_TYPE_TIME|SQL_C_CHAR|SQLCHARACTER|**datetime**<br /><br /> **smalldatetime**|  
|SQL_TYPE_TIMESTAMP|SQL_C_CHAR|SQLCHARACTER|**datetime**<br /><br /> **smalldatetime**|  
|SQL_GUID|SQL_C_GUID|SQLUNIQUEID|**uniqueidentifier**|  
|SQL_INTERVAL_|SQL_C_CHAR|SQLCHARACTER|**char**|  
  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] に、符号付きの **tinyint**、unsigned **smallint**、または unsigned **int** データ型がありません。 これらのデータ型を変換するときにデータ値が失われないようにするには、2 番目に大きい整数データ型を使用して [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] テーブルを作成します。 ユーザーが元のデータ型で許容されている範囲外の値を後から追加しないようにするには、次のようにルールを [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 列に適用し、ソースのデータ型でサポートされる範囲内に許容値を限定します。  
  
```  
CREATE TABLE Sample_Ints(STinyIntCol   SMALLINT,  
USmallIntCol INT)  
GO  
CREATE RULE STinyInt_Rule  
AS   
@range >= -128 AND @range <= 127  
GO  
CREATE RULE USmallInt_Rule  
AS   
@range >= 0 AND @range <= 65535  
GO  
sp_bindrule STinyInt_Rule, 'Sample_Ints.STinyIntCol'  
GO  
sp_bindrule USmallInt_Rule, 'Sample_Ints.USmallIntCol'  
GO  
```  
  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、interval データ型を直接サポートしません。 ただしアプリケーションでは、interval 型のエスケープ シーケンスを文字列として [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 文字型列に格納できます。 アプリケーションでは、後で使用するためにこれらのエスケープ シーケンスを読み取ることができますが、 [!INCLUDE[tsql](../../includes/tsql-md.md)] ステートメント内では使用できません。  
  
 一括コピー関数を使用すると、ODBC データ ソースから読み取ったデータを [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] にすばやく読み込むことができます。 [SQLBindCol](../../relational-databases/native-client-odbc-api/sqlbindcol.md)を使用して結果セットの列をプログラム変数にバインドした後、 **bcp_bind** を使用して、同じプログラム変数を一括コピー操作にバインドします。 [Sqlfetchscroll](../../relational-databases/native-client-odbc-api/sqlfetchscroll.md)または **sqlfetch** を呼び出すと、ODBC データソースからプログラム変数にデータの行がフェッチされ、 [bcp_sendrow](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-sendrow.md)を呼び出すと、プログラム変数からにデータが一括コピーされ [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。  
  
 アプリケーションでは、 **bcp_bind** _pData_ パラメーターに指定されたデータ変数のアドレスを変更する必要があるときに、いつでも [bcp_colptr](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-colptr.md)関数を使用できます。 アプリケーションでは、 **bcp_bind**_cbdata_ パラメーターでもともと指定されていたデータ長を変更する必要がある場合、いつでも [bcp_collen](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-collen.md)関数を使用できます。  
  
 "bcp_readrow" のような関数は用意されていないため、一括コピーを使用してデータを [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] からプログラム変数に読み取ることはできません。 実行できるのは、アプリケーションからサーバーへのデータの送信だけです。  
  
## <a name="see-also"></a>参照  
 [ODBC&#41;&#40;の一括コピー操作の実行 ](../../relational-databases/native-client-odbc-bulk-copy-operations/performing-bulk-copy-operations-odbc.md)  
  
  
