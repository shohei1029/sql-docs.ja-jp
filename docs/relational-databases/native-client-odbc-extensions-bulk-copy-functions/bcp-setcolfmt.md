---
description: bcp_setcolfmt
title: bcp_setcolfmt |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
apiname:
- bcp_setcolfmt
apilocation:
- sqlncli11.dll
apitype: DLLExport
helpviewer_keywords:
- bcp_setcolfmt function
ms.assetid: afb47987-39e7-4079-ad66-e0abf4d4c72b
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 0e87855f1094d3ac274abdb3acd38612fd0b3dd5
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104749032"
---
# <a name="bcp_setcolfmt"></a>bcp_setcolfmt
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  **Bcp_setcolfmt** 関数は、 [bcp_colfmt](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-colfmt.md)よりも優先されます。 列の照合順序を指定する場合は、 **bcp_setcolfmt** 関数を使用する必要があります。 [bcp_setbulkmode](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-setbulkmode.md) を使用すると、複数の列の形式を指定できます。  
  
 この関数では、一括コピー操作で列の形式を柔軟に指定できます。 個別の列形式属性を設定する場合に、この関数を使用します。 **Bcp_setcolfmt** を呼び出すたびに、1つの列形式属性が設定されます。  
  
 **Bcp_setcolfmt** 関数は、ユーザーファイル内のデータのソースまたはターゲットの形式を指定します。 ソース形式として使用する場合、 **bcp_setcolfmt** は、のテーブルに一括コピーするデータソースとして使用される既存のデータファイルの形式を指定し [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。 ターゲット形式として使用する場合は、 **bcp_setcolfmt** で指定された列形式を使用してデータファイルが作成されます。  
  
## <a name="syntax"></a>構文  
  
```  
  
RETCODE bcp_setcolfmt (  
        HDBC hdbc,  
        INT field,  
        INT property,  
        void* pValue,  
        INT cbValue);  
```  
  
## <a name="arguments"></a>引数  
 *hdbc*  
 一括コピーが有効な ODBC 接続ハンドルです。  
  
 *分野*  
 プロパティを設定する列の序数です。  
  
 *property*  
 プロパティ定数のいずれかを指定します。 次の表に、プロパティ定数を示します。  
  
|プロパティ|値|説明|  
|--------------|-----------|-----------------|  
|BCP_FMT_TYPE|BYTE|ユーザー ファイル内にある列のデータ型です。 データベース テーブル内の対応する列のデータ型と異なる場合は、一括コピーでは可能な限りデータを変換します。<br /><br /> BCP_FMT_TYPE パラメーターは、ODBC C データ型の列挙子ではなく、sqlncli.h 内の SQL Server データ型トークンで列挙されます。 たとえば、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 固有の SQLCHARACTER 型を使用して、ODBC の SQL_C_CHAR 型の文字列を指定できます。<br /><br /> [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のデータ型に対して既定のデータ表現を指定するには、このパラメーターに 0 を設定します。<br /><br /> SQL Server からファイルに一括コピーを行う場合、BCP_FMT_TYPE が SQLDECIMAL または SQLDECIMAL の場合、基になる列が **decimal** または **numeric** でない場合は、既定の有効桁数と小数点以下桁数が使用されます。 それ以外の場合、ソース列が **decimal** または **numeric** の場合は、ソース列の有効桁数と小数点以下桁数が使用されます。|  
|BCP_FMT_INDICATOR_LEN|INT|インジケーター (プレフィックス) のバイト単位の長さです。<br /><br /> これは、列データ内にある長さのインジケーターや NULL インジケーターのバイト単位の長さです。 インジケーターの長さの有効値は、0 (インジケーターを使用しない)、1、2、または 4 です。<br /><br /> 一括コピーのインジケーターの既定の使用方法を指定するには、このパラメーターに SQL_VARLEN_DATA を設定します。<br /><br /> インジケーターは、メモリ内ではすべてのデータの直前に、データ ファイル内ではインジケーターを適用するデータの直前に配置します。<br /><br /> 複数の方法 (インジケーターと列の最大長、インジケーターとターミネータ シーケンスなど) を使用してデータ ファイルの列長を指定すると、一括コピーではコピーするデータの量が最も少なくなる方法が使用されます。<br /><br /> ユーザーの操作でデータの形式が調整されずに一括コピーで生成されたデータ ファイルには、列データの長さが異なる場合や、列の値に NULL が許容される場合にインジケーターが含まれます。|  
|BCP_FMT_DATA_LEN|DBINT|データ (列長) のバイト単位の長さです。<br /><br /> これは、ユーザー ファイル内にある列データのバイト単位の最大長です。長さのインジケーターやターミネータの長さは含まれません。<br /><br /> BCP_FMT_DATA_LEN を SQL_NULL_DATA に設定すると、データ ファイルの列に含まれるすべての値が NULL に設定されます。<br /><br /> また、BCP_FMT_DATA_LEN を SQL_VARLEN_DATA に設定すると、各列のデータの長さがシステムによって決定されます。 これは、列によっては、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] からコピーされるデータの前に長さのインジケーターや NULL インジケーターを生成したり、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] にコピーするデータにインジケーターが必要になる場合があることを意味します。<br /><br /> [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の文字データ型やバイナリ データ型の場合は、BCP_FMT_DATA_LEN に SQL_VARLEN_DATA、SQL_NULL_DATA、0、正の値のいずれかを指定できます。 BCP_FMT_DATA_LEN が SQL_VARLEN_DATA の場合、システムは、長さのインジケーター (存在する場合) またはターミネータ シーケンスを使用してデータの長さを決定します。 長さのインジケーターとターミネータ シーケンスの両方を指定した場合、一括コピーはコピーするデータ量が少なくなる方法を使用します。 BCP_FMT_DATA_LEN が SQL_VARLEN_DATA の場合、データ型は [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 文字型またはバイナリ型になります。長さのインジケーターとターミネータ シーケンスをどちらも指定しなかった場合は、システムからエラー メッセージが返されます。<br /><br /> BCP_FMT_DATA_LEN が 0 または正の値の場合、システムは最大データ長として BCP_FMT_DATA_LEN を使用します。 ただし、BCP_FMT_DATA_LEN に正の値を指定し、長さのインジケーターとターミネータ シーケンスを指定した場合、システムはコピーするデータ量が少なくなる方法を使用してデータ長を決定します。<br /><br /> BCP_FMT_DATA_LEN の値はデータのバイト数を表します。 文字データが Unicode ワイド文字で表されている場合、BCP_FMT_DATA_LEN パラメーター値が正の値のときは、各文字のサイズ (バイト数) に文字数を掛けた数を表します。|  
|BCP_FMT_TERMINATOR|LPCBYTE|この列で使用されるターミネータ シーケンス (ANSI または Unicode) を指すポインターです。 このパラメーターは主に文字データ型に対して有効です。これは、他のすべての型は固定長であったり、バイト数を正確に記録するために長さのインジケーターが必要になる (バイナリ データの場合) ためです。<br /><br /> 抽出されるデータが途中で終了されないようにしたり、ユーザー ファイル内のデータが途中で終了していないことを示すには、このパラメーターに NULL を設定します。<br /><br /> 複数の方法 (ターミネータと長さのインジケーター、ターミネータと列の最大長など) を使用してユーザー ファイルの列長を指定すると、一括コピーはコピーするデータの量が最も少なくなる方法を使用します。<br /><br /> 一括コピー API では、必要に応じて Unicode から MBCS への文字変換が実行されます。 このため、ターミネータのバイト文字列とそのバイト文字列の長さの両方を正しく設定するように注意する必要があります。|  
|BCP_FMT_SERVER_COL|INT|データベース内での列の序数位置。|  
|BCP_FMT_COLLATION|LPCSTR|照合順序名。|  
  
 *pValue*  
 *プロパティ* に関連付ける値へのポインターです。 このポインターにより、列形式のプロパティを個別に設定できます。  
  
 *cbvalue*  
 プロパティ バッファーのバイト単位の長さです。  
  
## <a name="returns"></a>戻り値  
 SUCCEED または FAIL。  
  
## <a name="remarks"></a>解説  
 この関数は、 **bcp_colfmt** 関数よりも優先されます。 **Bcp_setcolfmt** 関数には、 **bcp_colfmt** のすべての機能が用意されています。 さらに、列の照合順序もサポートされます。 列形式属性は、次に示した順番で設定することをお勧めします。  
  
 BCP_FMT_SERVER_COL  
  
 BCP_FMT_DATA_LEN  
  
 BCP_FMT_TYPE  
  
 **Bcp_setcolfmt** 関数を使用すると、一括コピーのユーザーファイル形式を指定できます。 次に、一括コピーに使用するフォーマットの内容を示します。  
  
-   ユーザー ファイルの列からデータベース列へのマッピング  
  
-   ユーザー ファイルの各列のデータ型  
  
-   各列の省略可能なインジケーターの長さ  
  
-   ユーザー ファイルの各列におけるデータの最大長  
  
-   各列の省略可能なターミネータ バイト シーケンス  
  
-   省略可能なターミネータ バイト シーケンスの長さ  
  
 **Bcp_setcolfmt** を呼び出すたびに、1つのユーザーファイル列の形式が指定されます。 たとえば、5つの列から構成されるユーザーデータファイルの3つの列の既定の設定を変更するには、まず [bcp_columns](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-columns.md)**(5)** を呼び出し、 **bcp_setcolfmt** 次に5回呼び出します。これらの呼び出しのうち3つを使用して、カスタム形式を設定します。 残りの2つの呼び出しについては、BCP_FMT_TYPE を0に設定し、BCP_FMT_INDICATOR_LENGTH、BCP_FMT_DATA_LEN、および *Cbvalue* をそれぞれ0、SQL_VARLEN_DATA、および0に設定します。 このプロシージャでは、5 つの列すべてをコピーします。それらの列のうち 3 つはカスタマイズされた形式でコピーされ、2 つは既定の形式でコピーされます。  
  
 **Bcp_setcolfmt** を呼び出す前に、 **bcp_columns** 関数を呼び出す必要があります。  
  
 ユーザーファイルの各列のプロパティごとに **bcp_setcolfmt** を1回呼び出す必要があります。  
  
 ユーザー ファイル内のすべてのデータを SQL Server テーブルにコピーする必要はありません。 列をスキップするには、列のデータの形式を指定する際に BCP_FMT_SERVER_COL パラメーターを 0 に設定します。 列をスキップする場合でも、その列の型を指定する必要があります。  
  
 [Bcp_writefmt](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-writefmt.md)関数は、書式指定を永続化するために使用できます。  
  
## <a name="bcp_setcolfmt-support-for-enhanced-date-and-time-features"></a>bcp_setcolfmt による機能強化された日付と時刻のサポート  
 日付型または時刻型の BCP_FMT_TYPE プロパティと共に使用される型は、 [&#40;OLE DB および ODBC&#41;の拡張された日付と時刻の型に対する一括コピーの変更 ](../../relational-databases/native-client-odbc-date-time/bulk-copy-changes-for-enhanced-date-and-time-types-ole-db-and-odbc.md)で指定されています。  
  
 詳細については、「 [日付と時刻の機能強化 &#40;ODBC&#41;](../../relational-databases/native-client-odbc-date-time/date-and-time-improvements-odbc.md)」を参照してください。  
  
## <a name="see-also"></a>参照  
 [一括コピー関数](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/sql-server-driver-extensions-bulk-copy-functions.md)  
  
  
