---
description: bcp_moretext
title: bcp_moretext |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
apiname:
- bcp_moretext
apilocation:
- sqlncli11.dll
apitype: DLLExport
helpviewer_keywords:
- bcp_moretext function
ms.assetid: 23e98015-a8e4-4434-9b3f-9c7350cf965f
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 70a364306f2498e6a9f504bcbb2d1c119c746d9d
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104749532"
---
# <a name="bcp_moretext"></a>bcp_moretext
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  長い可変長データ型の値の一部を [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] に送信します。  
  
## <a name="syntax"></a>構文  
  
```  
  
RETCODE bcp_moretext (  
        HDBC hdbc,  
        DBINT cbData,  
        LPCBYTE pData);  
```  
  
## <a name="arguments"></a>引数  
 *hdbc*  
 一括コピーが有効な ODBC 接続ハンドルです。  
  
 *cbData*  
 *PData* によって参照されるデータから SQL Server にコピーされるデータのバイト数を指定します。 値 SQL_NULL_DATA は、NULL を示します。  
  
 *pData*  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] に送信する、サポートされている長い可変長データ チャンクへのポインターです。  
  
## <a name="returns"></a>戻り値  
 SUCCEED または FAIL。  
  
## <a name="remarks"></a>解説  
 この関数を [bcp_bind](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-bind.md) および [bcp_sendrow](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-sendrow.md) と組み合わせて使用すると、長い可変長のデータ値を多数の小さなチャンクで SQL Server にコピーできます。 **bcp_moretext** は、 **text**、 **ntext**、 **image**、 **varchar (max)**、 **nvarchar (max)**、 **VARBINARY (max)**、ユーザー定義型 (UDT)、および XML の SQL Server 各データ型を持つ列で使用できます。 **bcp_moretext** は、データ変換をサポートしていません。指定されたデータは、ターゲット列のデータ型と一致している必要があります。  
  
 **Bcp_moretext** でサポートされているデータ型に対して NULL 以外の *pData* パラメーターを使用して **bcp_bind** が呼び出された場合、 **bcp_sendrow** は長さに関係なくデータ値全体を送信します。 ただし、 **bcp_bind** がサポートされているデータ型に対して NULL の *pData* パラメーターを持っている場合、 **bcp_moretext** を使用して、データが存在するバインド列が処理されたことを示す **bcp_sendrow** から正常に返された直後にデータをコピーできます。  
  
 **Bcp_moretext** を使用して、サポートされているデータ型の列を1つの行で送信する場合は、その行でサポートされている他のすべてのデータ型の列を送信するためにも使用する必要があります。 列はスキップされません。 サポートされるデータ型は、SQLTEXT、SQLNTEXT、SQLIMAGE、SQLUDT、および SQLXML です。 列の型が varchar(max)、nvarchar(max)、または varbinary(max) の場合、SQLCHARACTER、SQLVARCHAR、SQNCHAR、SQLBINARY、SQLVARBINARY も、サポート対象となります。  
  
 **Bcp_bind** または [bcp_collen](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-collen.md)のいずれかを呼び出すと、SQL Server 列にコピーされるすべてのデータパーツの合計長が設定されます。 **Bcp_bind** または **bcp_collen** の呼び出しで指定されているよりも多くのバイトを SQL Server 送信しようとすると、エラーが発生します。 このエラーが発生するのは、たとえば、SQL Server **テキスト** 列の使用可能なデータの長さを4500に設定するために **bcp_collen** を使用 **bcp_moretext** し、データバッファーの長さが1000バイト長であることを各呼び出しに対して指定する場合などです。  
  
 コピーされた行に複数の長い可変長列が含まれている場合、 **bcp_moretext** は最初にそのデータを最も低い順序の列に送信し、次に、その後に最も低い2番目の列に番号が付けられます。 コピーするデータの合計長を正しく設定することが重要です。 長さの設定以外に、一括コピーによって列のすべてのデータを受け取ったことを示す方法はありません。  
  
 Bcp_sendrow と bcp_moretext を使用して **var (max)** 値がサーバーに送信されるときに、列の長さを設定するために bcp_collen を呼び出す必要はありません。 代わりに、これらの型に対してのみ、長さ0の bcp_sendrow を呼び出すことによって値が終了します。  
  
 通常、アプリケーションは、ループ内で **bcp_sendrow** および **bcp_moretext** を呼び出して、多数のデータ行を送信します。 2つの **テキスト** 列を含むテーブルに対してこれを行う方法の概要を次に示します。  
  
```  
while (there are still rows to send)  
{  
bcp_sendrow(...);  
  
for (all the data in the first varbinary(max) column)  
bcp_moretext(...);  
bcp_moretext(hdbc, 0, NULL);  
  
for (all the data in the second varbinary(max) column)  
bcp_moretext(...);  
bcp_moretext(hdbc, 0, NULL);  
  
}  
  
```  
  
## <a name="example"></a>例  
 この例では、 **bcp_bind** と **bcp_sendrow** で **bcp_moretext** を使用する方法を示します。  
  
```  
// Variables like henv not specified.  
HDBC      hdbc;  
DBINT      idRow = 5;  
char*      pPart1 = "This text value isn't very long,";  
char*      pPart2 = " but it's broken into three parts";  
char*      pPart3 = " anyhow.";  
DBINT      cbAllParts;  
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
if (bcp_init(hdbc, "comdb..articles", NULL, NULL, DB_IN) == FAIL)  
   {  
   // Raise error and return.  
   return;  
   }  
  
// Bind program variables to table columns.   
if (bcp_bind(hdbc, (LPCBYTE) &idRow, 0, SQL_VARLEN_DATA, NULL, 0,  
   SQLINT4, 1)    == FAIL)  
   {  
   // Raise error and return.  
   return;  
   }  
  
cbAllParts = (DBINT) (strnlen(pPart1, sizeof(pPart1) + 1) + strnlen(pPart2, sizeof(pPart2) + 1) + strnlen(pPart3, sizeof(pPart3) + 1));  
if (bcp_bind(hdbc, NULL, 0, cbAllParts, NULL, 0, SQLTEXT, 2) == FAIL)  
   {  
   // Raise error and return.  
   return;  
   }  
  
// Send this row, with the text value broken into three chunks.   
if (bcp_sendrow(hdbc) == FAIL)  
   {  
   // Raise error and return.  
   return;  
   }  
  
if (bcp_moretext(hdbc, (DBINT) strnlen(pPart1, sizeof(pPart1) + 1), pPart1) == FAIL)  
   {  
   // Raise error and return.  
   return;  
   }  
if (bcp_moretext(hdbc, (DBINT) strnlen(pPart2, sizeof(pPart2) + 1), pPart2) == FAIL)  
   {  
   // Raise error and return.  
   return;  
   }  
if (bcp_moretext(hdbc, (DBINT) strnlen(pPart3, sizeof(pPart3) + 1), pPart3) == FAIL)  
   {  
   // Raise error and return.  
   return;  
   }  
  
// All done. Get the number of rows processed (should be one).  
nRowsProcessed = bcp_done(hdbc);  
  
// Carry on.  
```  
  
## <a name="see-also"></a>参照  
 [一括コピー関数](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/sql-server-driver-extensions-bulk-copy-functions.md)  
  
  
