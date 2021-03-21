---
description: 'IBCPSession:: BCPColFmt (Native Client OLE DB provider)'
title: 'IBCPSession:: BCPColFmt (Native Client OLE DB provider) |Microsoft Docs'
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
apiname:
- IBCPSession::BCPColFmt (OLE DB)
apitype: COM
helpviewer_keywords:
- BCPColFmt method
ms.assetid: 2852f4ba-f1c6-4c4c-86b2-b77e4abe70de
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 62b18f80e3479d713438720c08c5f203642a1efc
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104749412"
---
# <a name="ibcpsessionbcpcolfmt-native-client-ole-db-provider"></a>IBCPSession:: BCPColFmt (Native Client OLE DB Provider)
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  プログラム変数と [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 列のバインドを作成します。  
  
## <a name="syntax"></a>構文  
  
```  
  
HRESULT BCPColFmt(   
      DBORDINAL idxUserDataCol,  
      int eUserDataType,  
      int cbIndicator,  
      int cbUserData,  
      BYTE *pbUserDataTerm,  
      int cbUserDataTerm,  
      DBORDINAL idxServerCol);  
```  
  
## <a name="remarks"></a>解説  
 **BCPColFmt** メソッドは、BCP データ ファイルのフィールドと [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 列のバインドを作成するために使用します。 このメソッドは、列の長さ、型、ターミネータ、およびプレフィックス長をパラメーターとして受け取り、個々のフィールドの対応するプロパティに設定します。  
  
 ユーザーが対話モードを選択すると、このメソッドが 2 回呼び出されます。1 回は既定値 (サーバーの列の型によって異なります) に応じて列形式を設定するために呼び出され、もう 1 回はクライアントが対話モードで選択した列の型に応じて各列の形式を設定するために呼び出されます。  
  
 対話モード以外の場合、このメソッドは列ごとに 1 回だけ呼び出され、各列の型を文字型またはネイティブ型に設定し、列ターミネータと行ターミネータを設定します。  
  
 **BCPColFmt** メソッドにより、一括コピーのユーザー ファイル形式を指定できます。 次に、一括コピーに使用するフォーマットの内容を示します。  
  
-   ユーザー ファイルのフィールドからデータベース列へのマッピング  
  
-   ユーザー ファイルの各フィールドのデータ型  
  
-   各フィールドの省略可能なインジケーターの長さ  
  
-   ユーザー ファイルの各フィールドにおけるデータの最大長  
  
-   各フィールドの省略可能なターミネータ バイト シーケンス  
  
-   省略可能なターミネータ バイト シーケンスの長さ  
  
 **BCPColFmt** を呼び出すたびに、ユーザー ファイルの 1 つのフィールドの形式が指定されます。 たとえば、5 つのフィールドから構成されるユーザー データ ファイルの 3 つのフィールドの既定の設定を変更するには、まず、`BCPColumns(5)` を呼び出し、**BCPColFmt** を 5 回呼び出します。この 5 回の呼び出しのうち 3 回は独自の形式を設定して呼び出します。 残りの 2 回の呼び出しでは、*eUserDataType* を BCP_TYPE_DEFAULT に設定し、*cbIndicator*、*cbUserData*、*cbUserDataTerm* をそれぞれ 0、BCP_VARIABLE_LENGTH、0 に設定します。 このプロシージャでは、5 つの列すべてをコピーします。それらの列のうち 3 つはカスタマイズされた形式でコピーされ、2 つは既定の形式でコピーされます。  
  
> [!NOTE]  
>  **BCPColFmt** を呼び出す前に、[IBCPSession::BCPColumns](../../relational-databases/native-client-ole-db-interfaces/ibcpsession-bcpcolumns-ole-db.md) メソッドを呼び出す必要があります。 **BCPColFmt** は、ユーザー ファイル内の列ごとに 1 回呼び出す必要があります。 **BCPColFmt** をユーザー ファイルの任意の列に対して複数回呼び出すと、エラーが発生します。  
  
 ユーザー ファイル内の全データを [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] テーブルにコピーする必要はありません。 列をスキップするには、列のデータの形式を指定する際に idxServerCol パラメーターを 0 に設定します。 一方、フィールドをスキップする場合は、メソッドを正しく機能させるためにすべての情報が必要になります。  
  
 **注**[IBCPSession::BCPWriteFmt](../../relational-databases/native-client-ole-db-interfaces/ibcpsession-bcpwritefmt-ole-db.md) 関数を使用すると、**BCPColFmt** で指定された形式指定を保存できます。  
  
## <a name="arguments"></a>引数  
 *idxUserDataCol*[in]  
 ユーザー データ ファイル内のフィールドのインデックス。  
  
 *eUserDataType*[in]  
 ユーザー データ ファイル内のフィールドのデータ型。 使用できるデータ型は、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client ヘッダーファイル (sqlncli) に、BCP_TYPE_SQLINT4 などの BCP_TYPE_XXX 形式で一覧表示されます。 BCP_TYPE_DEFAULT 値を指定すると、プロバイダーはテーブルやビューの列と同じ型を使用します。 **eUserDataType** 引数に BCP_TYPE_SQLDECIMAL または BCP_TYPE_SQLNUMERIC を指定し、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] からファイルへの一括コピー操作を行う場合の動作を次に示します。  
  
-   コピー元の列が decimal 型または numeric 型以外の場合は、既定の有効桁数と小数点以下桁数が使用されます。  
  
-   コピー元の列が decimal 型または numeric 型の場合は、コピー元の列の有効桁数と小数点以下桁数が使用されます。  
  
 *cbIndicator*[in]  
 フィールドのプレフィックス長。 既定値は BCP_PREFIX_DEFAULT です。 有効なプレフィックス長は、0、1、2、4、および 8 です。 フィールドがチャンク分割される場合に頻繁に使用されるプレフィックスのサイズは 8 です。 チャンク分割は、大きい値になっている列を効率的に一括コピーするために使用します。  
  
 *cbUserData*[in]  
 ユーザー ファイル内にあるフィールド データの最大長 (バイト単位)。長さのインジケーターやターミネータの長さは含まれません。  
  
 **cbUserData** を BCP_LENGTH_NULL に設定すると、データ ファイルのフィールドに含まれているすべての値が NULL に設定されます。 **cbUserData** を BCP_LENGTH_VARIABLE に設定すると、各フィールドのデータの長さがシステムによって決定されます。 これは、フィールドによっては、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] からコピーされるデータの前に長さのインジケーターや NULL インジケーターを生成したり、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] にコピーするデータにインジケーターが必要になる場合があることを意味します。  
  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の文字データ型やバイナリ データ型の場合は、**cbUserData** を BCP_LENGTH_VARIABLE、BCP_LENGTH_NULL、0、正の値のいずれかにできます。 **cbUserData** が BCP_LENGTH_VARIABLE の場合、システムは長さのインジケーター (存在する場合) またはターミネータ シーケンスを使用してデータの長さを決定します。 長さのインジケーターとターミネータ シーケンスの両方を指定した場合、一括コピーはコピーするデータ量が少なくなる方法を使用します。 **cbUserData** が BCP_LENGTH_VARIABLE の場合、データ型は [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 文字型またはバイナリ型になります。長さのインジケーターとターミネータ シーケンスをどちらも指定しなかった場合は、システムからエラー メッセージが返されます。  
  
 **cbUserData** が 0 または正の値の場合、システムは最大データ長として **cbUserData** を使用します。 ただし、**cbUserData** に正の値を指定し、長さのインジケーターやターミネータ シーケンスを指定した場合、システムはコピーするデータ量が少なくなる方法を使用してデータ長を決定します。  
  
 **cbUserData** の値はデータのバイト数を表します。 文字データが Unicode ワイド文字で表されている場合、**cbUserData** パラメーターが正の値のときは、各文字のサイズ (バイト数) に文字数を掛けた数を表します。  
  
 *pbUserDataTerm*[size_is][in]  
 フィールドに使用するターミネータ シーケンス。 このパラメーターは主に文字データ型に対して有効です。これは、他のすべての型は固定長であったり、バイト数を正確に記録するために長さのインジケーターが必要になる (バイナリ データの場合) ためです。  
  
 抽出されるデータが途中で終了されないようにしたり、ユーザー ファイル内のデータが途中で終了していないことを示すには、このパラメーターに NULL を設定します。  
  
 複数の方法 (ターミネータと長さのインジケーター、ターミネータと列の最大長など) を使用してユーザー ファイルの列長を指定すると、一括コピーはコピーするデータの量が最も少なくなる方法を使用します。  
  
 一括コピー API では、必要に応じて Unicode から MBCS への文字変換が実行されます。 このため、ターミネータのバイト文字列とそのバイト文字列の長さの両方を正しく設定するように注意する必要があります。  
  
 *cbUserDataTerm*[in]  
 列に使用するターミネータ シーケンスの長さ (バイト単位)。 データ内にターミネータが存在しないか不要な場合は、この値を 0 に設定します。  
  
 *idxServerCol*[in]  
 データベース テーブル内での列の序数位置。 最初の列の序数は 1 です。 列の序数位置は **IColumnsInfo::GetColumnInfo** などのメソッドから報告されます。 この値が 0 の場合、一括コピーではデータ ファイル内のこのフィールドは無視されます。  
  
## <a name="return-code-values"></a>リターン コードの値  
 S_OK  
 メソッドが成功しました。  
  
 E_FAIL  
 プロバイダー固有のエラーが発生しました。詳細を確認するには、[ISQLServerErrorInfo](isqlservererrorinfo-geterrorinfo-ole-db.md) インターフェイスを使用してください。  
  
 E_UNEXPECTED  
 メソッドの呼び出しが予期されませんでした。 たとえば、このメソッドが呼び出される前に、[IBCPSession::BCPInit](../../relational-databases/native-client-ole-db-interfaces/ibcpsession-bcpinit-ole-db.md) メソッドが呼び出されなかった場合などです。  
  
 E_INVALIDARG  
 引数が無効です。  
  
 E_OUTOFMEMORY  
 メモリ不足エラー。  
  
## <a name="see-also"></a>参照  
 [IBCPSession &#40;OLE DB&#41;](../../relational-databases/native-client-ole-db-interfaces/ibcpsession-ole-db.md)   
 [一括コピー操作の実行](../../relational-databases/native-client/features/performing-bulk-copy-operations.md)  
  
