---
title: 分散トランザクションのサポート (OLE DB ドライバー)
description: OLE DB Driver for SQL Server のコンシューマーで ITransactionJoin::JoinTransaction メソッドを使用して分散トランザクションに参加する方法について説明します。
ms.custom: ''
ms.date: 06/14/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: connectivity
ms.topic: reference
helpviewer_keywords:
- OLE DB, transactions
- distributed transactions [OLE DB]
- MS DTC
- transactions [OLE DB]
- OLE DB Driver for SQL Server, transactions
- ITransactionJoin interface
- MS DTC, about distributed transaction support
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: 88c3b8414c854667ed5722a0fd044e8e28cfd4b9
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104754232"
---
# <a name="supporting-distributed-transactions"></a>分散トランザクションのサポート
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[Driver_OLEDB_Download](../../../includes/driver_oledb_download.md)]

  OLE DB Driver for SQL Server のコンシューマーは、**ITransactionJoin::JoinTransaction** メソッドを使用して、Microsoft 分散トランザクション コーディネーター (MS DTC) によりコーディネートされる分散トランザクションに参加できます。  
  
 MS DTC が公開する COM オブジェクトを使用すると、クライアントは、さまざまなデータ ストアに対する複数の接続にまたがってコーディネートされるトランザクションを起動したり、このトランザクションに参加することができます。 OLE DB Driver for SQL Server のコンシューマーは、MS DTC **ITransactionDispenser** インターフェイスを使用してトランザクションを起動します。 **ITransactionDispenser** の **BeginTransaction** メンバーは、分散トランザクション オブジェクト上の参照を返します。 この参照は、**JoinTransaction** を使用して、OLE DB Driver for SQL Server に渡されます。  
  
 MS DTC は、分散トランザクションでの非同期のコミットとアボートをサポートします。 非同期トランザクションの状態を通知する場合、コンシューマーは、**ITransactionOutcomeEvents** インターフェイスを実装し、そのインターフェイスを MS DTC トランザクション オブジェクトに接続します。  
  
 分散トランザクションの場合、OLE DB Driver for SQL Server では、**ITransactionJoin::JoinTransaction** のパラメーターを次のように実装します。  
  
|パラメーター|説明|  
|---------------|-----------------|  
|*punkTransactionCoord*|MS DTC トランザクション オブジェクトへのポインター。|  
|*IsoLevel*|OLE DB Driver for SQL Server では無視されます。 MS DTC によりコーディネートされるトランザクションの分離レベルは、コンシューマーが MS DTC からトランザクション オブジェクトを取得するときに決まります。|  
|*IsoFlags*|0 を指定する必要があります。 コンシューマーが他の値を指定すると、OLE DB Driver for SQL Server から XACT_E_NOISORETAIN が返されます。|  
|*POtherOptions*|NULL 以外の場合、OLE DB Driver for SQL Server では、インターフェイスからのオプション オブジェクトが要求されます。 このオプション オブジェクトの *ulTimeout* メンバーが 0 以外の場合、OLE DB Driver for SQL Server からは XACT_E_NOTIMEOUT が返されます。 OLE DB Driver for SQL Server では、*szDescription* メンバーの値は無視されます。|  
  
 次の例では、MS DTC を使用してトランザクションをコーディネートします。  
  
```  
// Interfaces used in the example.  
IDBCreateSession*       pIDBCreateSession   = NULL;  
ITransactionJoin*       pITransactionJoin   = NULL;  
IDBCreateCommand*       pIDBCreateCommand   = NULL;  
IRowset*                pIRowset            = NULL;  
  
// Transaction dispenser and transaction from MS DTC.  
ITransactionDispenser*  pITransactionDispenser = NULL;  
ITransaction*           pITransaction       = NULL;  
  
    HRESULT             hr;  
  
// Get the command creation interface for the session.  
if (FAILED(hr = pIDBCreateSession->CreateSession(NULL,  
     IID_IDBCreateCommand, (IUnknown**) &pIDBCreateCommand)))  
    {  
    // Process error from session creation. Release any references and  
    // return.  
    }  
  
// Get a transaction dispenser object from MS DTC and  
// start a transaction.  
if (FAILED(hr = DtcGetTransactionManager(NULL, NULL,  
    IID_ITransactionDispenser, 0, 0, NULL,  
    (void**) &pITransactionDispenser)))  
    {  
    // Process error message from MS DTC, release any references,  
    // and then return.  
    }  
if (FAILED(hr = pITransactionDispenser->BeginTransaction(  
    NULL, ISOLATIONLEVEL_READCOMMITTED, ISOFLAG_RETAIN_DONTCARE,  
    NULL, &pITransaction)))  
    {  
    // Process error message from MS DTC, release any references,  
    // and then return.  
    }  
  
// Join the transaction.  
if (FAILED(pIDBCreateCommand->QueryInterface(IID_ITransactionJoin,  
    (void**) &pITransactionJoin)))  
    {  
    // Process failure to get an interface, release any references, and  
    // then return.  
    }  
if (FAILED(pITransactionJoin->JoinTransaction(  
    (IUnknown*) pITransaction, 0, 0, NULL)))  
    {  
    // Process join failure, release any references, and then return.  
    }  
  
// Get data into a rowset, then update the data. Functions are not  
// illustrated in this example.  
if (FAILED(hr = ExecuteCommand(pIDBCreateCommand, &pIRowset)))  
    {  
    // Release any references and return.  
    }  
  
// If rowset data update fails, then terminate the transaction, else  
// commit. The example doesn't retain the rowset.  
if (FAILED(hr = UpdateDataInRowset(pIRowset, bDelayedUpdate)))  
    {  
    // Get error from update, then abort.  
    pITransaction->Abort(NULL, FALSE, FALSE);  
    }  
else  
    {  
    if (FAILED(hr = pITransaction->Commit(FALSE, 0, 0)))  
        {  
        // Get error from failed commit.  
        //  
        // If a distributed commit fails, application logic could  
        // analyze failure and retry. In this example, terminate. The   
        // consumer must resolve this somehow.  
        pITransaction->Abort(NULL, FALSE, FALSE);  
        }  
    }  
  
if (FAILED(hr))  
    {  
    // Update of data or commit failed. Release any references and  
    // return.  
    }  
  
// Un-enlist from the distributed transaction by setting   
// the transaction object pointer to NULL.  
if (FAILED(pITransactionJoin->JoinTransaction(  
    (IUnknown*) NULL, 0, 0, NULL)))  
    {  
    // Process failure, and then return.  
    }  
  
// Release any references and continue.  
```  
  
## <a name="see-also"></a>参照  
 [トランザクション](../../oledb/ole-db-transactions/transactions.md)  
  
  
