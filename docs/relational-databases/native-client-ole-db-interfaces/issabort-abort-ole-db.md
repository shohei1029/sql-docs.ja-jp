---
description: 'ISSAbort:: Abort (Native Client OLE DB provider)'
title: 'ISSAbort:: Abort (Native Client OLE DB provider) |Microsoft Docs'
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
apiname:
- ISSAbort::Abort (OLE DB)
apitype: COM
helpviewer_keywords:
- Abort method
ms.assetid: a5bca169-694b-4895-84ac-e8fba491e479
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: b0c140c0d772f7a6206f6d481464cdbbfcf1ff31
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104745502"
---
# <a name="issabortabort-native-client-ole-db-provider"></a>ISSAbort:: Abort (Native Client OLE DB Provider)
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  現在の行セットと、現在のコマンドに関連付けられているバッチ コマンドを取り消します。  
  
**Issabort** インターフェイスは、Native Client OLE DB プロバイダーで公開されています。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] **Issabort:: Abort** メソッドを使用して、現在の行セットを取り消すために使用されます。また、行セットを最初に生成したコマンドを使用してバッチ処理されたコマンドと、まだ実行が完了していないコマンドを取り消します。  
  
 **Issabort** は、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] **ICommand:: Execute** または **IOpenRowset:: OpenRowset** によって返される **IMultipleResults** オブジェクトに対して **QueryInterface** を使用して使用可能な Native Client プロバイダー固有のインターフェイスです。  
  
## <a name="syntax"></a>構文  
  
```  
  
HRESULT Abort(void);  
```  
  
## <a name="remarks"></a>解説  
 中断されているコマンドがストアドプロシージャ内にある場合、ストアドプロシージャ (およびそのプロシージャを呼び出したプロシージャ) の実行が終了され、ストアドプロシージャ呼び出しを含むコマンドバッチも終了します。 サーバーがクライアントに結果セットを転送中の場合、この処理も停止します。 クライアントが結果セットを使用しない場合、行セットを解放する前に **ISSAbort::Abort** を呼び出すと、行セットの解放が高速になります。ただし、開いているトランザクションがあり、XACT_ABORT が ON の場合、**ISSAbort::Abort** が呼び出されたときに、トランザクションがロールバックされます。  
  
 **ISSAbort::Abort** が S_OK を返すと、関連する **IMultipleResults** インターフェイスは使用できない状態になり、このインターフェイスが解放されるまで、すべてのメソッド呼び出しに DB_E_CANCELED が返されます (ただし **IUnknown** インターフェイスで定義されたメソッドは除きます)。 **Abort** を呼び出す前に、**IMultipleResults** から **IRowset** を取得している場合、これも使用できない状態になり、**ISSAbort::Abort** の正常な呼び出し後にこのインターフェイスが解放されるまでは、すべてのメソッド呼び出しで DB_E_CANCELED が返されます (ただし、**IUnknown** インターフェイスと **IRowset::ReleaseRows** で定義されたメソッドは除きます)。  
  
> [!NOTE]  
>  [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] 以降のバージョンでは、サーバーの XACT_ABORT 状態が ON の場合、**ISSAbort::Abort** を実行すると、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] に接続するときに、現在のすべての暗黙的または明示的なトランザクションが終了し、ロールバックされます。 以前のバージョンの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、現在のトランザクションは中止されません。  
  
## <a name="arguments"></a>引数  
 [なし] :  
  
## <a name="return-code-values"></a>リターン コードの値  
 S_OK  
 **ISSAbort::Abort** メソッドは、バッチが取り消された場合 S_OK を、それ以外の場合 DB_E_CANTCANCEL を返します。 バッチが既に取り消されている場合は、DB_E_CANCELED を返します。  
  
 DB_E_CANCELED  
 バッチは既に取り消されています。  
  
 DB_E_CANTCANCEL  
 バッチは取り消されませんでした。  
  
 E_FAIL  
 プロバイダー固有のエラーが発生しました。詳細を確認するには、[ISQLServerErrorInfo](isqlservererrorinfo-geterrorinfo-ole-db.md) インターフェイスを使用してください。  
  
 E_UNEXPECTED  
 メソッドの呼び出しが予期されませんでした。 たとえば、**ISSAbort::Abort** が既に呼び出されていたために、オブジェクトがゾンビ状態になっている場合などです。  
  
 E_OUTOFMEMORY  
 メモリ不足エラー。  
  
## <a name="see-also"></a>参照  
 [ISSAbort &#40;OLE DB&#41;]()  
  
