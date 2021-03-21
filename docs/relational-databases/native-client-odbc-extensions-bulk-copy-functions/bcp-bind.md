---
description: bcp_bind
title: bcp_bind |Microsoft Docs
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.technology: native-client
ms.topic: reference
apiname:
- bcp_bind
apilocation:
- sqlncli11.dll
apitype: DLLExport
helpviewer_keywords:
- bcp_bind function
ms.assetid: 6e335a5c-64b2-4bcf-a88f-35dc9393f329
author: markingmyname
ms.author: maghan
ms.custom: ''
ms.reviewer: ''
ms.date: 03/14/2017
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 3d73f055a77bb120d8c56425426273d6a06412b0
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104749642"
---
# <a name="bcp_bind"></a>bcp_bind

[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] に一括コピーするために、プログラム変数からテーブル列にデータをバインドします。  

## <a name="syntax"></a>構文

```  
  
RETCODE bcp_bind (  
        HDBC hdbc,
        LPCBYTE pData,  
        INT cbIndicator,  
        DBINT cbData,  
        LPCBYTE pTerm,  
        INT cbTerm,  
        INT eDataType,  
        INT idxServerCol);  
```  
  
## <a name="arguments"></a>引数

 *hdbc*  
 一括コピーが有効な ODBC 接続ハンドルです。  
  
 *pData*  
 コピーされたデータへのポインターです。 *Edatatype* が SQLTEXT、SQLNTEXT、SQLXML、sqlntext、sqlntext、sqlntext、sqlntext、SQLNTEXT、sqlntext、または SQLIMAGE の場合は、 *pData* を NULL にすることができます。 NULL *pData* は、長いデータ値が [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] [bcp_moretext](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-moretext.md)を使用してチャンクに送信されることを示します。 ユーザーは、ユーザーバインドフィールドに対応する列が BLOB 列の場合、 *pData* を NULL に設定する必要があります。それ以外の場合、 **bcp_bind** は失敗します。  
  
 データ内にインジケーターが存在する場合、それらのインジケーターはメモリ内ではデータの直前にあります。 この場合、 *pData* パラメーターはインジケーター変数を指します。インジケーターの幅 ( *cbindicator* パラメーター) は、一括コピーによってユーザーデータを正しくアドレス指定するために使用されます。  
  
 *cbIndicator*  
 列のデータに関する長さのインジケーターや NULL インジケーターのバイト単位の長さです。 インジケーターの長さの有効値は、0 (インジケーターを使用しない場合)、1、2、4、または 8 です。 インジケーターは、メモリ内ではすべてのデータの直前にあります。 たとえば、一括コピーを使用して [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] テーブルに整数値を挿入するには、次のような構造体の型定義を使用します。  
  
```
typedef struct tagBCPBOUNDINT  
    {  
    int iIndicator;  
    int Value;  
    } BCPBOUNDINT;  
```  
  
 この例では、 *pData* パラメーターは、構造体の宣言されたインスタンスのアドレス (BCPBOUNDINT *iindicator* 構造体メンバーのアドレス) に設定されます。 *Cbindicator* パラメーターは整数のサイズ (sizeof (int)) に設定され、 *cbindicator* パラメーターはもう一度整数のサイズ (sizeof (int)) に設定されます。 バインドされた列に NULL 値が含まれているサーバーに行を一括コピーするには、インスタンスの *Iindicator* メンバーの値を SQL_NULL_DATA に設定する必要があります。  
  
 *cbData*  
 プログラム変数内のデータのバイト数です。長さのインジケーター、NULL インジケーター、およびターミネータの長さは含まれません。  
  
 *Cbdata* を SQL_NULL_DATA に設定すると、サーバーにコピーされるすべての行に列の NULL 値が格納されます。  
  
 *Cbdata* を SQL_VARLEN_DATA に設定すると、システムは、文字列ターミネータ (またはその他の方法) を使用してコピーされるデータの長さを決定します。  
  
 整数などの固定長データ型の場合、データ型によってデータの長さがシステムに示されます。 したがって、固定長データ型の場合は、 *Cbdata* を安全に SQL_VARLEN_DATA することも、データの長さを指定することもできます。  
  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]文字データ型およびバイナリデータ型の場合、 *cbdata* には SQL_VARLEN_DATA、SQL_NULL_DATA、正の値、または0を指定できます。 *Cbdata* が SQL_VARLEN_DATA 場合、システムは長さ/null インジケーター (存在する場合) またはターミネータシーケンスを使用してデータの長さを決定します。 インジケーターとターミネータ シーケンスの両方を指定した場合、システムでは、コピーされるデータ量が少なくなる方法が使用されます。 *Cbdata* が SQL_VARLEN_DATA 場合、列のデータ型は [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 文字型またはバイナリ型で、長さのインジケーターもターミネータシーケンスも指定されていないと、システムはエラーメッセージを返します。  
  
 *Cbdata* が0または正の値の場合、システムではデータ長として *cbdata* が使用されます。 ただし、正の *Cbdata* 値に加えて、長さのインジケーターまたはターミネータシーケンスが指定されている場合、システムは、コピーされるデータ量が最小になるメソッドを使用してデータ長を決定します。  
  
 *Cbdata* パラメーター値は、データのバイト数を表します。 文字データが Unicode ワイド文字で表されている場合、 *cbdata* パラメーターの正の値は、各文字のサイズ (バイト単位) を乗算した文字数を表します。  
  
 *pTerm*  
 このプログラム変数の終了位置をマークするバイト パターンがある場合、そのバイト パターンを指すポインターです。 たとえば、通常、ANSI 文字列と MBCS C 文字列には 1 バイトのターミネータ (\0) があります。  
  
 変数にターミネータがない場合は、 *Pterm* を NULL に設定します。  
  
 C NULL ターミネータをプログラム変数のターミネータとして指定する場合、空文字列 ("") を使用できます。 Null で終わる空の文字列は1バイト (ターミネータバイト自体) を構成するので、 *Cbterm* を1に設定します。 たとえば、 *szName* の文字列が null で終了し、長さを示すためにターミネータを使用する必要があることを示すには、次のようにします。  
  
```
bcp_bind(hdbc, szName, 0,  
   SQL_VARLEN_DATA, "", 1,  
   SQLCHARACTER, 2)  
```

 この例では、終了しない形式の場合、 *szName* 変数からバインドされたテーブルの2番目の列に15文字をコピーすることを示している可能性があります。  

```
bcp_bind(hdbc, szName, 0, 15,
   NULL, 0, SQLCHARACTER, 2)  
```

 一括コピー API では、必要に応じて Unicode から MBCS への文字変換が実行されます。 ターミネータのバイト文字列とそのバイト文字列の長さの両方を正しく設定してください。 たとえば、 *szName* の文字列が unicode ワイド文字列で、unicode の null 終端記号の値によって終了することを示すには、次のように指定します。  
  
```  
bcp_bind(hdbc, szName, 0,
   SQL_VARLEN_DATA, L"",  
   sizeof(WCHAR), SQLNCHAR, 2)  
```  
  
 バインドされた [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 列がワイド文字の場合、 [bcp_sendrow](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-sendrow.md)に対して変換は実行されません。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 列が MBCS 文字型の場合、データが [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] に送信されるときに、ワイド文字がマルチバイト文字に変換されます。  
  
 *cbTerm*  
 プログラム変数にターミネータがある場合は、そのターミネータを構成するバイト数です。 変数にターミネータがない場合は、 *Cbterm* を0に設定します。  

*Edatatype* プログラム変数の C データ型です。 プログラム変数内のデータは、データベース列の型に変換されます。 このパラメーターが 0 の場合、変換は実行されません。  

*Edatatype* パラメーターは、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ODBC C データ型の列挙子ではなく、sqlncli のデータ型のトークンによって列挙されます。 たとえば、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 固有の SQLINT2 型を使用して、ODBC の SQL_C_SHORT 型の 2 バイトの整数を指定できます。  

[!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] では、 **_Edatatype_** パラメーターでの SQLXML および sqludt データ型のトークンのサポートが導入されました。  

次の表では、有効な列挙データ型と対応する ODBC C データ型の一覧を示します。

|eDataType|C 型|
|-----------------------|------------|  
|SQLTEXT|char *|  
|SQLNTEXT|wchar_t *|  
|SQLCHARACTER|char *|  
|SQLBIGCHAR|char *|  
|SQLVARCHAR|char *|  
|SQLBIGVARCHAR|char *|  
|SQLNCHAR|wchar_t *|  
|SQLNVARCHAR|wchar_t *|  
|SQLBINARY|unsigned char *|  
|SQLBIGBINARY|unsigned char *|  
|SQLVARBINARY|unsigned char *|  
|SQLBIGVARBINARY|unsigned char *|  
|SQLBIT|char|  
|SQLBITN|char|  
|SQLINT1|char|  
|SQLINT2|short int|  
|SQLINT4|INT|  
|SQLINT8|_int64|  
|SQLINTN|*cbIndicator*<br /> 1: SQLINT1<br /> 2: SQLINT2<br /> 4: SQLINT4<br /> 8: SQLINT8|  
|SQLFLT4|float|  
|SQLFLT8|float|  
|SQLFLTN|*cbIndicator*<br /> 4: SQLFLT4<br /> 8: SQLFLT8|  
|SQLDECIMALN|SQL_NUMERIC_STRUCT|  
|SQLNUMERICN|SQL_NUMERIC_STRUCT|  
|SQLMONEY|DBMONEY|  
|SQLMONEY4|DBMONEY4|  
|SQLMONEYN|*cbIndicator*<br /> 4: SQLMONEY4<br /> 8: SQLMONEY|  
|SQLTIMEN|SQL_SS_TIME2_STRUCT|  
|SQLDATEN|SQL_DATE_STRUCT|  
|SQLDATETIM4|DBDATETIM4|  
|SQLDATETIME|DBDATETIME|  
|SQLDATETIMN|*cbIndicator*<br /> 4: SQLDATETIM4<br /> 8: SQLDATETIME|  
|SQLDATETIME2N|SQL_TIMESTAMP_STRUCT|  
|SQLDATETIMEOFFSETN|SQL_SS_TIMESTAMPOFFSET_STRUCT|  
|SQLIMAGE|unsigned char *|  
|SQLUDT|unsigned char *|  
|SQLUNIQUEID|SQLGUID|  
|SQLVARIANT|*以下を除く任意のデータ型:*<br />-   text<br />-   ntext<br />-   image<br />-   varchar(max)<br />-   varbinary(max)<br />-   nvarchar(max)<br />-   xml<br />-   timestamp|  
|SQLXML|*サポートされる C のデータ型*<br />-   char*<br />-   wchar_t *<br />-   unsigned char *|  

*Idxservercol* データがコピーされるデータベーステーブル内の列の序数位置です。 テーブル内の最初の列は列 1 です。 列の序数位置は [Sqlcolumns](../../relational-databases/native-client-odbc-api/sqlcolumns.md)によって報告されます。  
  
## <a name="returns"></a>戻り値

 SUCCEED または FAIL。

## <a name="remarks"></a>解説

プログラム変数からのテーブルにデータをコピーする高速で効率的な方法には、 **bcp_bind** を使用し [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。  

このまたはその他の一括コピー関数を呼び出す前に [bcp_init](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-init.md) を呼び出してください。 **Bcp_init** を呼び出すと、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 一括コピーの対象テーブルが設定されます。 **Bcp_bind** と [bcp_sendrow](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-sendrow.md)で使用するために **bcp_init** を呼び出すと、データファイルを示す **BCP_INIT** の _szdatafile_ ファイルパラメーターが NULL に設定されます。**bcp_init**_edirection_ パラメーターが DB_IN に設定されています。  

コピー先のテーブル内のすべての列に対して、個別の **bcp_bind** 呼び出しを作成 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] します。 必要な **bcp_bind** の呼び出しが行われたら、 **bcp_sendrow** を呼び出して、プログラム変数からにデータ行を送信し [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。 列の再バインドはサポートされていません。

既に受信した行をコミットする場合は常に [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 、 [bcp_batch](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-batch.md)を呼び出します。 たとえば、1000行が挿入されるたびに、またはその他の間隔で1回 **bcp_batch** を呼び出します。  

挿入する行がなくなった場合は、 [bcp_done](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-done.md)を呼び出します。 この操作を行わないと、エラーが発生します。

[Bcp_control](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-control.md)で指定されたコントロールパラメーターの設定は、 **bcp_bind** の行転送には影響しません。  

列の値が [bcp_moretext](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-moretext.md)の呼び出しによって指定されるため、列の値が null に設定されて *いる場合は*、 *EDATATYPE* が SQLTEXT、SQLNTEXT、SQLXML、SQLNTEXT、SQLNTEXT、SQLNTEXT、SQLNTEXT、sqlntext、sqlntext、または SQLIMAGE に設定されている後続の列も **bcp_moretext** *pdata* に設定する必要があります。また  

**Varchar (max)**、 **varbinary (max)**、 **nvarchar (max)** などの新しい大きな値型の場合は、 *edatatype* パラメーターで型インジケーターとして SQLCHARACTER、sqlcharacter、SQLCHARACTER、sqlcharacter、および sqlcharacter を使用できます。  

*Cbterm* が0でない場合は、すべての値 (1、2、4、または 8) がプレフィックス (*cbterm*) に対して有効です。 この場合、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client はターミネータを検索し、ターミネータ (*i*) に対してデータの長さを計算し、 *cbdata* を i の小さい値と prefix の値に設定します。  

*Cbterm* が0で、 *cbterm* (プレフィックス) が0でない場合、 *cbterm* は8である必要があります。 8バイトのプレフィックスは、次の値を取ることができます。  

- 0xFFFFFFFFFFFFFFFF は、フィールドの NULL 値を示します。  

- 0xFFFFFFFFFFFFFFFE は、データをサーバーに効率的に送信するために使用される特別なプレフィックス値として扱われます。 この特別なプレフィックス値を含むデータは、次のような形式になります。  

- <SPECIAL_PREFIX> \<0 or more  DATA CHUNKS> <ZERO_CHUNK の場所:  

- SPECIAL_PREFIX には 0xFFFFFFFFFFFFFFFE を指定します。  

- DATA_CHUNK は、チャンク長を含む4バイトのプレフィックスであり、その後に4バイトのプレフィックスで長さが指定された実際のデータが続きます。  

- ZERO_CHUNK は、データの末尾を示す 0 (00000000) をすべて含む4バイトの値です。  

- その他の有効な8バイト長は、通常のデータ長として扱われます。  

 **Bcp_bind** を使用するときに [bcp_columns](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-columns.md)を呼び出すと、エラーが発生します。  
  
## <a name="bcp_bind-support-for-enhanced-date-and-time-features"></a>bcp_bind による機能強化された日付と時刻のサポート

日付型または時刻型の *Edatatype* パラメーターで使用される型の詳細については、「 [&#40;OLE DB および ODBC&#41;の拡張された日付/時刻型に対する一括コピーの変更](../../relational-databases/native-client-odbc-date-time/bulk-copy-changes-for-enhanced-date-and-time-types-ole-db-and-odbc.md)」を参照してください。  

詳細については、「 [日付と時刻の機能強化 &#40;ODBC&#41;](../../relational-databases/native-client-odbc-date-time/date-and-time-improvements-odbc.md)」を参照してください。  

## <a name="example"></a>例
  
```
#include sql.h  
#include sqlext.h  
#include odbcss.h  
// Variables like henv not specified.  
HDBC      hdbc;  
char         szCompanyName[MAXNAME];  
DBINT      idCompany;  
DBINT      nRowsProcessed;  
DBBOOL      bMoreData;  
char*      pTerm = "\t\t";  
  
// Application initiation, get an ODBC environment handle, allocate the  
// hdbc, and so on.  
...
  
// Enable bulk copy prior to connecting on allocated hdbc.  
SQLSetConnectAttr(hdbc, SQL_COPT_SS_BCP, (SQLPOINTER) SQL_BCP_ON,  
   SQL_IS_INTEGER);  
  
// Connect to the data source; return on error.  
if (!SQL_SUCCEEDED(SQLConnect(hdbc, _T("myDSN"), SQL_NTS,  
   _T("myUser"), SQL_NTS, _T("myPwd"), SQL_NTS)))  
   {  
   // Raise error and return.  
   return;  
   }  
  
// Initialize bcp.
if (bcp_init(hdbc, "comdb..accounts_info", NULL, NULL  
   DB_IN) == FAIL)  
   {  
   // Raise error and return.  
   return;  
   }  
  
// Bind program variables to table columns.
if (bcp_bind(hdbc, (LPCBYTE) &idCompany, 0, sizeof(DBINT), NULL, 0,  
   SQLINT4, 1)    == FAIL)  
   {  
   // Raise error and return.  
   return;  
   }  
if (bcp_bind(hdbc, (LPCBYTE) szCompanyName, 0, SQL_VARLEN_DATA,  
   (LPCBYTE) pTerm, strnlen(pTerm, sizeof(pTerm)), SQLCHARACTER, 2) == FAIL)  
   {  
   // Raise error and return.  
   return;  
   }  
  
while (TRUE)  
   {  
   // Retrieve and process program data.
   if ((bMoreData = getdata(&idCompany, szCompanyName)) == TRUE)  
      {  
      // Send the data.
      if (bcp_sendrow(hdbc) == FAIL)  
         {  
         // Raise error and return.  
         return;  
         }  
      }  
   else  
      {  
      // Break out of loop.  
      break;  
      }  
   }  
  
// Terminate the bulk copy operation.  
if ((nRowsProcessed = bcp_done(hdbc)) == -1)  
   {  
   printf_s("Bulk-copy unsuccessful.\n");  
   return;  
   }  
  
printf_s("%ld rows copied.\n", nRowsProcessed);  
```  
  
## <a name="see-also"></a>参照

 [一括コピー関数](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/sql-server-driver-extensions-bulk-copy-functions.md)
