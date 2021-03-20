---
title: ISSAbort::Abort (OLE DB ドライバー) | Microsoft Docs
description: OLE DB Driver for SQL Server の現在のコマンドに関連付けられている現在の行セットとバッチ コマンドが ISSAbort::Abort メソッドによって取り消されるしくみについて説明します。
ms.custom: ''
ms.date: 06/14/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: connectivity
ms.topic: reference
apiname:
- ISSAbort::Abort (OLE DB)
apitype: COM
helpviewer_keywords:
- Abort method
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: e5739727f82779f2ef32d7dd8359c2c3d47f0cad
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104738752"
---
# <a name="issabortabort-ole-db"></a>ISSAbort::Abort (OLE DB)
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[Driver_OLEDB_Download](../../../includes/driver_oledb_download.md)]

  現在の行セットと、現在のコマンドに関連付けられているバッチ コマンドを取り消します。  
  
OLE DB Driver for SQL Server が公開する `ISSAbort` インターフェイスは、`ISSAbort::Abort` メソッドを提供します。このメソッドは、現在の行セットを取り消したうえで、この行セットを最初に生成したコマンドを含んでいるバッチ コマンドのうち、実行が完了していないのものを取り消す場合に使用します。  
  
 `ISSAbort` は、OLE DB Driver for SQL Server 固有のインターフェイスです。これを使用するには、`ICommand::Execute` または `IOpenRowset::OpenRowset` により返される `IMultipleResults` オブジェクトの `QueryInterface` を使用します。  
  
## <a name="syntax"></a>構文  
  
```  
  
HRESULT Abort(void);  
```  
  
## <a name="remarks"></a>解説  
 中止されるコマンドがストアド プロシージャの場合、ストアド プロシージャ (およびそのプロシージャを呼び出したすべてのプロシージャ) の実行と、そのストアド プロシージャの呼び出しが含まれるコマンド バッチの実行が終了します。 サーバーがクライアントに結果セットを転送中の場合、この転送も停止します。 クライアントで結果セットの使用が望まれない場合、`**`ISSAbort::Abort` before releasing the rowset will speed up the rowset release, but if there is an open transaction and XACT_ABORT is ON, the transaction will be rolled back when `ISSAbort::Abort` の呼び出しが取り消されます  
  
 `ISSAbort::Abort` が S_OK を返すと、関連する `IMultipleResults` インターフェイスは使用できない状態になり、このインターフェイスが解放されるまで、すべてのメソッド呼び出しに DB_E_CANCELED が返されます (ただし `IUnknown` インターフェイスで定義されたメソッドは除きます)。 `Abort` を呼び出す前に、`IMultipleResults` から `IRowset` を取得している場合、これも使用できない状態になり、`ISSAbort::Abort` の正常な呼び出し後にこのインターフェイスが解放されるまでは、すべてのメソッド呼び出しで DB_E_CANCELED が返されます (ただし、`IUnknown` インターフェイスと `IRowset::ReleaseRows` で定義されたメソッドは除きます)。  
  
> [!NOTE]  
>  [!INCLUDE[ssVersion2005](../../../includes/ssversion2005-md.md)] 以降のバージョンでは、サーバーの XACT_ABORT 状態が ON の場合、`ISSAbort::Abort` を実行すると、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] に接続するときに、現在のすべての暗黙的または明示的なトランザクションが終了し、ロールバックされます。 以前のバージョンの [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] では、現在のトランザクションは中止されません。  
  
## <a name="arguments"></a>引数  
 [なし] :  
  
## <a name="return-code-values"></a>リターン コードの値  
 S_OK  
 `ISSAbort::Abort` メソッドは、バッチが取り消された場合 S_OK を、それ以外の場合 DB_E_CANTCANCEL を返します。 バッチが既に取り消されている場合は、DB_E_CANCELED を返します。  
  
 DB_E_CANCELED  
 バッチは既に取り消されています。  
  
 DB_E_CANTCANCEL  
 バッチは取り消されませんでした。  
  
 E_FAIL  
 プロバイダー固有のエラーが発生しました。詳細を確認するには、[ISQLServerErrorInfo](./isqlservererrorinfo-geterrorinfo-ole-db.md) インターフェイスを使用してください。  
  
 E_UNEXPECTED  
 メソッドの呼び出しが予期されませんでした。 たとえば、`ISSAbort::Abort` が既に呼び出されていたために、オブジェクトがゾンビ状態になっている場合などです。  
  
 E_OUTOFMEMORY  
 メモリ不足エラー。  
  
