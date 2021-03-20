---
title: IMultipleResults を使用した複数の結果セットの処理 | Microsoft Docs
description: コンシューマーで IMultipleResults を使用して、OLE DB Driver for SQL Server のコマンドを実行して返される結果を処理する方法について説明します。
ms.custom: ''
ms.date: 06/14/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: connectivity
ms.topic: reference
helpviewer_keywords:
- multiple rowsets
- rowsets [OLE DB], multiple
- IMultipleResults interface
- multiple-rowset results
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: e5c9c73e56b257251b59dbfb611ce15058d78602
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104735402"
---
# <a name="using-imultipleresults-to-process-multiple-result-sets"></a>IMultipleResults を使用した複数の結果セットの処理
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[Driver_OLEDB_Download](../../../includes/driver_oledb_download.md)]

  コンシューマーは **IMultipleResults** インターフェイスを使用して、OLE DB Driver for SQL Server のコマンドを実行して返される結果を処理できます。 OLE DB Driver for SQL Server から実行用のコマンドを送信すると、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] でステートメントが実行されて結果が返されます。  
  
 クライアントはコマンドの実行結果をすべて処理する必要があります。 OLE DB Driver for SQL Server では、コマンドの実行結果として、複数の行セットから構成されるオブジェクトが生成される場合があります。そのため、クライアントが開始したラウンド トリップをアプリケーション データの取得で完了させるには、**IMultipleResults** インターフェイスを使用してください。  
  
 次の [!INCLUDE[tsql](../../../includes/tsql-md.md)] ステートメントを実行すると複数の行セットが生成されます。行セットには、**OrderDetails** テーブルの行データを含むものや、COMPUTE BY 句の結果を含むものがあります。  
  
```  
SELECT OrderID, FullPrice = (UnitPrice * Quantity), Discount,  
    Discounted = UnitPrice * (1 - Discount) * Quantity  
FROM OrderDetails  
ORDER BY OrderID  
COMPUTE  
    SUM(UnitPrice * Quantity), SUM(UnitPrice * (1 - Discount) * Quantity)  
    BY OrderID  
```  
  
 このテキストを含んだコマンドを実行し、返される結果のインターフェイスとして行セットを要求した場合、最初の行セットのみが返されます。 コンシューマーでは、返された行セットのすべての行を処理できます。 ただし、DBPROP_MULTIPLECONNECTIONS データ ソース プロパティが VARIANT_FALSE に設定されていて、その接続で MARS が有効になっていない場合、コマンドを取り消さない限り同じセッション オブジェクトで他のコマンドを実行することはできません (OLE DB Driver for SQL Server によって他の接続が作成されることはありません)。 OLE DB Driver for SQL Server は接続で MARS が有効でないとき、DBPROP_MULTIPLECONNECTIONS が VARIANT_FALSE の場合は DB_E_OBJECTOPEN エラーを返し、アクティブなトランザクションがある場合は E_FAIL を返します。  
  
 また、ストリーム出力パラメーターを使用していて、アプリケーションが返された出力パラメーターの値を使い残した状態で **IMultipleResults::GetResults** を呼び出して次の結果セットを取得しようとすると、OLE DB Driver for SQL Server から DB_E_OBJECTOPEN が返されます。 MARS が無効になっており、行セットを生成しないコマンドやサーバー カーソルでない行セットを生成するコマンドを実行していて接続がビジー状態であり、また DBPROP_MULTIPLECONNECTIONS データ ソース プロパティが VARIANT_TRUE に設定されている場合、OLE DB Driver for SQL Server では、トランザクションがアクティブの場合を除き、同時実行されるコマンド オブジェクトをサポートする接続が追加作成されます (トランザクションがアクティブの場合はエラーが返されます)。 トランザクションとロックは、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] によって接続ごとに管理されます。 他の接続が作成されている場合でも、個別の接続のコマンドはロックを共有しません。 コマンドによって要求された行にロックを設定することで、そのコマンドが別のコマンドによってブロックされないようにする必要があります。 MARS を有効にしている場合、その接続では複数のコマンドをアクティブにできます。明示的なトランザクションを使用している場合、すべてのコマンドが共通のトランザクションを共有します。  
  
 コマンドを取り消すには、[ISSAbort::Abort](../../oledb/ole-db-interfaces/issabort-abort-ole-db.md) を使用するか、コマンド オブジェクトおよび派生行セットのすべての参照を解放します。  
  
 すべてのインスタンスで **IMultipleResults** を使用すると、コマンド実行の結果生成されたすべての行セットをコンシューマーが取得し、コマンド実行を取り消して他のコマンドのためにセッションを解放するタイミングを適切に判断できます。  
  
> [!NOTE]  
>  [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] カーソルを使用するときは、コマンドを実行するとカーソルが作成されます。 カーソルを作成するときに [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] は成功か失敗を返します。したがって、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] インスタンスへのラウンド トリップが完了するのはコマンドを実行した結果が返ってきたときです。 その後、**GetNextRows** 呼び出しはそれぞれがラウンド トリップになります。 このようなしくみによって、サーバー カーソルからのフェッチの結果である行セットを処理する、複数のアクティブなコマンド オブジェクトが存在できます。 詳細については、「[行セットと SQL Server カーソル](../../oledb/ole-db-rowsets/rowsets-and-sql-server-cursors.md)」を参照してください。  
  
## <a name="see-also"></a>参照  
 [コマンド](../../oledb/ole-db-commands/commands.md)  
  
  
