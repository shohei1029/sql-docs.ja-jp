---
description: bcp_colfmt
title: bcp_colfmt |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
apiname:
- bcp_colfmt
apilocation:
- sqlncli11.dll
apitype: DLLExport
helpviewer_keywords:
- bcp_colfmt function
ms.assetid: 5c3b6299-80c7-4e84-8e69-4ff33009548e
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 8e014d9298fd3d756e0d6a85349949ee52a813ec
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104749622"
---
# <a name="bcp_colfmt"></a>bcp_colfmt
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  ユーザー ファイルのデータのコピー元またはコピー先の形式を指定します。 ソース形式として使用する場合、 **bcp_colfmt** は、テーブルへの一括コピーのデータソースとして使用される既存のデータファイルの形式を指定し [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。 ターゲット形式として使用する場合は、 **bcp_colfmt** で指定された列形式を使用してデータファイルが作成されます。  
  
## <a name="syntax"></a>構文  
  
```  
  
RETCODE bcp_colfmt (  
        HDBC hdbc,  
        INT idxUserDataCol,  
        BYTE eUserDataType,  
        INT cbIndicator,  
        DBINT cbUserData,  
        LPCBYTE pUserDataTerm,  
        INT cbUserDataTerm,  
        INT idxServerCol);  
```  
  
## <a name="arguments"></a>引数  
 *hdbc*  
 一括コピーが有効な ODBC 接続ハンドルです。  
  
 *idxUserDataCol*  
 ユーザー データ ファイルの、形式が指定される列の序数です。 最初の列は 1 です。  
  
 *eUserDataType*  
 ユーザー ファイル内にある列のデータ型です。 データベーステーブル (*Idxservercolumn*) の対応する列のデータ型と異なる場合は、可能であれば一括コピーによってデータが変換されます。  
  
 [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] では、 *Euserdatatype* パラメーターでの SQLXML および sqludt データ型のトークンのサポートが導入されました。  
  
 *Euserdatatype* パラメーターは、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ODBC C データ型の列挙子ではなく、sqlncli のデータ型のトークンによって列挙されます。 たとえば、固有の型 SQLCHARACTER を使用して、文字列、ODBC 型 SQL_C_CHAR を指定でき [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。  
  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のデータ型に対して既定のデータ表現を指定するには、このパラメーターに 0 を設定します。  
  
 ファイルへの一括コピーを行う [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 場合、 *Euserdatatype* が SQLDECIMAL または sqldecimal の場合は、次のようになります。  
  
-   ソース列が **decimal** または **numeric** でない場合は、既定の有効桁数と小数点以下桁数が使用されます。  
  
-   ソース列が **decimal** または **numeric** の場合は、ソース列の有効桁数と小数点以下桁数が使用されます。  
  
 *cbIndicator*  
 列データ内にある長さのインジケーターや NULL インジケーターのバイト単位の長さです。 インジケーターの長さの有効値は、0 (インジケーターを使用しない場合)、1、2、4、または 8 です。  
  
 一括コピーのインジケーターの既定の使用方法を指定するには、このパラメーターに SQL_VARLEN_DATA を設定します。  
  
 インジケーターは、メモリ内ではすべてのデータの直前に、データ ファイル内ではインジケーターを適用するデータの直前に配置します。  
  
 複数の方法 (インジケーターと列の最大長、インジケーターとターミネータ シーケンスなど) を使用してデータ ファイルの列長を指定すると、一括コピーではコピーするデータの量が最も少なくなる方法が使用されます。  
  
 ユーザーの操作でデータの形式が調整されずに一括コピーで生成されたデータ ファイルには、列データの長さが異なる場合や、列の値に NULL が許容される場合にインジケーターが含まれます。  
  
 *cbUserData*  
 ユーザー ファイル内にある列データの最大長 (バイト単位)。長さのインジケーターやターミネータの長さは含まれません。  
  
 *Cbuserdata* を SQL_NULL_DATA に設定すると、データファイルの列のすべての値がであるか、NULL に設定する必要があることを示します。  
  
 *Cbuserdata* を SQL_VARLEN_DATA に設定すると、各列のデータの長さがシステムによって決定されることになります。 これは、列によっては、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] からコピーされるデータの前に長さのインジケーターや NULL インジケーターを生成したり、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] にコピーするデータにインジケーターが必要になる場合があることを意味します。  
  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]文字データ型とバイナリデータ型の場合、 *Cbuserdata* には SQL_VARLEN_DATA、SQL_NULL_DATA、0、または正の値を指定できます。 *Cbuserdata* が SQL_VARLEN_DATA 場合、システムは長さインジケーター (存在する場合) またはターミネータシーケンスを使用してデータの長さを決定します。 長さのインジケーターとターミネータ シーケンスの両方を指定した場合、一括コピーはコピーするデータ量が少なくなる方法を使用します。 *Cbuserdata* が SQL_VARLEN_DATA 場合、データ型は [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 文字型またはバイナリ型で、長さのインジケーターもターミネータシーケンスも指定されていないと、システムはエラーメッセージを返します。  
  
 *cbUserData* が 0 または正の値の場合、システムは最大データ長として *cbUserData* を使用します。 ただし、*cbUserData* に正の値を指定し、長さのインジケーターやターミネータ シーケンスを指定した場合、システムはコピーするデータ量が少なくなる方法を使用してデータ長を決定します。  
  
 *cbUserData* の値はデータのバイト数を表します。 文字データが Unicode ワイド文字で表されている場合、*cbUserData* パラメーターが正の値のときは、各文字のサイズ (バイト数) に文字数を掛けた数を表します。  
  
 *pUserDataTerm*  
 列に使用するターミネータ シーケンスです。 このパラメーターは主に文字データ型に対して有効です。これは、他のすべての型は固定長であったり、バイト数を正確に記録するために長さのインジケーターが必要になる (バイナリ データの場合) ためです。  
  
 抽出されるデータが途中で終了されないようにしたり、ユーザー ファイル内のデータが途中で終了していないことを示すには、このパラメーターに NULL を設定します。  
  
 複数の方法 (ターミネータと長さのインジケーター、ターミネータと列の最大長など) を使用してユーザー ファイルの列長を指定すると、一括コピーはコピーするデータの量が最も少なくなる方法を使用します。  
  
 一括コピー API では、必要に応じて Unicode から MBCS への文字変換が実行されます。 このため、ターミネータのバイト文字列とそのバイト文字列の長さの両方を正しく設定するように注意する必要があります。  
  
 *cbUserDataTerm*  
 列に使用するターミネータ シーケンスの長さ (バイト単位)。 データ内にターミネータが存在しないか不要な場合は、この値を 0 に設定します。  
  
 *idxServerCol*  
 データベーステーブル内の列の序数位置です。 最初の列の序数は 1 です。 列の序数位置は [Sqlcolumns](../../relational-databases/native-client-odbc-api/sqlcolumns.md)によって報告されます。  
  
 この値が 0 の場合、一括コピーではデータ ファイル内のこの列が無視されます。  
  
## <a name="returns"></a>戻り値  
 SUCCEED または FAIL。  
  
## <a name="remarks"></a>解説  
 **Bcp_colfmt** 関数を使用すると、一括コピーのユーザーファイル形式を指定できます。 次に、一括コピーに使用するフォーマットの内容を示します。  
  
-   ユーザー ファイルの列からデータベース列へのマッピング  
  
-   ユーザー ファイルの各列のデータ型  
  
-   各列の省略可能なインジケーターの長さ  
  
-   ユーザー ファイルの各列におけるデータの最大長  
  
-   各列の省略可能なターミネータ バイト シーケンス  
  
-   省略可能なターミネータ バイト シーケンスの長さ  
  
 **Bcp_colfmt** を呼び出すたびに、1つのユーザーファイル列の形式が指定されます。 たとえば、5つの列から構成されるユーザーデータファイルの3つの列の既定の設定を変更するには、まず [bcp_columns](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-columns.md)**(5)** を呼び出し、 **bcp_colfmt** 次に5回呼び出します。これらの呼び出しのうち3つを使用して、カスタム形式を設定します。 残りの2つの呼び出しでは、 *Euserdatatype* を0に設定し、 *cbindicator*、 *Cbuserdata*、および *cbuserdataterm* をそれぞれ0、SQL_VARLEN_DATA、および0に設定します。 このプロシージャでは、5 つの列すべてをコピーします。それらの列のうち 3 つはカスタマイズされた形式でコピーされ、2 つは既定の形式でコピーされます。  
  
 *Cbindicator* の場合、大きな値の型が有効であることを示す値8。 フィールドにプレフィックスを指定し、そのフィールドに対応する列が新しい max 型である場合、この値は 8 にしか設定できません。 詳細については、「 [bcp_bind](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-bind.md)」を参照してください。  
  
 **Bcp_colfmt** を呼び出す前に、 **bcp_columns** 関数を呼び出す必要があります。  
  
 ユーザーファイルの各列に対して **bcp_colfmt** を1回呼び出す必要があります。  
  
 ユーザーファイルの列に対して複数回 **bcp_colfmt** を呼び出すと、エラーが発生します。  
  
 ユーザー ファイル内のすべてのデータを [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] テーブルにコピーする必要はありません。 列をスキップするには、列のデータの形式を指定し、 *Idxservercol* パラメーターを0に設定します。 列をスキップする場合でも、その列の型を指定する必要があります。  
  
 [Bcp_writefmt](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-writefmt.md)関数は、書式指定を永続化するために使用できます。  
  
## <a name="bcp_colfmt-support-for-enhanced-date-and-time-features"></a>bcp_colfmt による機能強化された日付と時刻のサポート  
 日付型または時刻型の *Euserdatatype* パラメーターで使用される型の詳細については、「 [&#40;OLE DB および ODBC&#41;の拡張された日付/時刻型に対する一括コピーの変更](../../relational-databases/native-client-odbc-date-time/bulk-copy-changes-for-enhanced-date-and-time-types-ole-db-and-odbc.md)」を参照してください。  
  
 詳細については、「 [日付と時刻の機能強化 &#40;ODBC&#41;](../../relational-databases/native-client-odbc-date-time/date-and-time-improvements-odbc.md)」を参照してください。  
  
## <a name="see-also"></a>参照  
 [一括コピー関数](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/sql-server-driver-extensions-bulk-copy-functions.md)  
  
  
