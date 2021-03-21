---
description: bcp_colptr
title: bcp_colptr |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
apiname:
- bcp_colptr
apilocation:
- sqlncli11.dll
apitype: DLLExport
helpviewer_keywords:
- bcp_colptr function
ms.assetid: 02ece13e-1da3-4f9d-b860-3177e43d2471
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 644b2d0b51217ef8f1bf94d2746cdf42c74d0195
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104749592"
---
# <a name="bcp_colptr"></a>bcp_colptr
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] への現在のコピー操作に関するプログラム変数のデータ アドレスを設定します。  
  
## <a name="syntax"></a>構文  
  
```  
  
RETCODE bcp_colptr (  
        HDBC hdbc,  
        LPCBYTE pData,  
        INT idxServerCol);  
```  
  
## <a name="arguments"></a>引数  
 *hdbc*  
 一括コピーが有効な ODBC 接続ハンドルです。  
  
 *pData*  
 コピーするデータを指すポインターです。 バインドされたデータ型が大きな値の型 (SQLTEXT や SQLIMAGE など) の場合は、 *pData* を NULL にすることができます。 NULL *pData* は、長いデータ値が [bcp_moretext](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-moretext.md)を使用してチャンク内の SQL Server に送信されることを示します。  
  
 *PData* が NULL に設定されていて、バインドされたフィールドに対応する列が大きな値型ではない場合、 **bcp_colptr** は失敗します。  
  
 大きな値の型の詳細については、「 [bcp_bind](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-bind.md)」を参照してください **。**  
  
 *idxServerCol*  
 データベース テーブル内にある、データのコピー先となる列の序数位置です。 テーブル内の最初の列は列 1 です。 列の序数位置は [Sqlcolumns](../../relational-databases/native-client-odbc-api/sqlcolumns.md)によって報告されます。  
  
## <a name="returns"></a>戻り値  
 SUCCEED または FAIL。  
  
## <a name="remarks"></a>解説  
 **Bcp_colptr** 関数を使用すると [bcp_sendrow](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-sendrow.md)で SQL Server にデータをコピーするときに、特定の列のソースデータのアドレスを変更できます。  
  
 初期状態では、ユーザーデータへのポインターは **bcp_bind** を呼び出すことによって設定されます。 **Bcp_sendrow** の呼び出しの間でプログラム変数のデータアドレスが変更された場合は、 **bcp_colptr** を呼び出して、データへのポインターをリセットできます。 次に **bcp_sendrow** を呼び出すと、 **bcp_colptr** への呼び出しによってアドレス指定されたデータが送信されます。  
  
 データアドレスを変更するテーブルのすべての列に対して、個別の **bcp_colptr** 呼び出しが必要です。  
  
## <a name="see-also"></a>参照  
 [一括コピー関数](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/sql-server-driver-extensions-bulk-copy-functions.md)  
  
  
