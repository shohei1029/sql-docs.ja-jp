---
description: IMultipleResults を使用して SQL Server Native Client で複数の結果セットを処理する
title: IMultipleResults、複数の結果セット
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- multiple rowsets
- rowsets [OLE DB], multiple
- IMultipleResults interface
- multiple-rowset results
ms.assetid: 754d3f30-7d94-4b67-8dac-baf2699ce9c6
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: c3d3d02a04d0fbe6f83d67c1da22022b3c070d32
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104749002"
---
# <a name="using-imultipleresults-to-process-multiple-result-sets-in-sql-server-native-client"></a>IMultipleResults を使用して SQL Server Native Client で複数の結果セットを処理する
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  コンシューマーは、 **IMultipleResults** インターフェイスを使用して、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client OLE DB プロバイダーのコマンド実行によって返される結果を処理します。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]Native Client OLE DB プロバイダーが実行するコマンドを送信すると、は [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ステートメントを実行し、結果を返します。  
  
 クライアントはコマンドの実行結果をすべて処理する必要があります。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]Native Client OLE DB プロバイダーコマンドを実行すると、複数行セットオブジェクトが結果として生成される可能性があるため、 **IMultipleResults** インターフェイスを使用して、アプリケーションデータの取得がクライアントによって開始されるラウンドトリップを完了するようにします。  
  
 次の [!INCLUDE[tsql](../../includes/tsql-md.md)] ステートメントを実行すると複数の行セットが生成されます。行セットには、**OrderDetails** テーブルの行データを含むものや、COMPUTE BY 句の結果を含むものがあります。  
  
```sql
SELECT OrderID, FullPrice = (UnitPrice * Quantity), Discount,  
    Discounted = UnitPrice * (1 - Discount) * Quantity  
FROM OrderDetails  
ORDER BY OrderID  
COMPUTE  
    SUM(UnitPrice * Quantity), SUM(UnitPrice * (1 - Discount) * Quantity)  
    BY OrderID  
```  
  
 このテキストを含んだコマンドを実行し、返される結果のインターフェイスとして行セットを要求した場合、最初の行セットのみが返されます。 コンシューマーでは、返された行セットのすべての行を処理できます。 ただし、DBPROP_MULTIPLECONNECTIONS データソースプロパティが VARIANT_FALSE に設定されていて、その接続で MARS が有効になっていない場合は、コマンドが取り消されるまで、他のコマンドをセッションオブジェクトに対して実行することはできません ( [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client OLE DB プロバイダーは別の接続を作成しません)。 MARS が接続で有効になっていない場合、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client OLE DB プロバイダーは DBPROP_MULTIPLECONNECTIONS が VARIANT_FALSE 場合に DB_E_OBJECTOPEN エラーを返し、アクティブなトランザクションがある場合は E_FAIL を返します。  
  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]また、 **IMultipleResults:: GetResults** を呼び出して次の結果セットを取得する前に、ストリーム出力パラメーターを使用していて、アプリケーションが返された出力パラメーター値をすべて使用していない場合は、Native Client OLE DB プロバイダーからも DB_E_OBJECTOPEN が返されます。 MARS が有効になっておらず、サーバーカーソルではない行セットを生成するコマンドを実行しているときに、接続がビジー状態になっていて、DBPROP_MULTIPLECONNECTIONS データソースプロパティが VARIANT_TRUE に設定されている場合、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client OLE DB プロバイダーは、同時実行コマンドオブジェクトをサポートするために追加の接続を作成します。 トランザクションとロックは、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] によって接続ごとに管理されます。 他の接続が作成されている場合でも、個別の接続のコマンドはロックを共有しません。 コマンドによって要求された行にロックを設定することで、そのコマンドが別のコマンドによってブロックされないようにする必要があります。 MARS を有効にしている場合、その接続では複数のコマンドをアクティブにできます。明示的なトランザクションを使用している場合、すべてのコマンドが共通のトランザクションを共有します。  
  
 コマンドを取り消すには、[ISSAbort::Abort](../../relational-databases/native-client-ole-db-interfaces/issabort-abort-ole-db.md) を使用するか、コマンド オブジェクトおよび派生行セットのすべての参照を解放します。  
  
 すべてのインスタンスで **IMultipleResults** を使用すると、コマンド実行の結果生成されたすべての行セットをコンシューマーが取得し、コマンド実行を取り消して他のコマンドのためにセッションを解放するタイミングを適切に判断できます。  
  
> [!NOTE]  
>  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] カーソルを使用するときは、コマンドを実行するとカーソルが作成されます。 カーソルを作成するときに [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] は成功か失敗を返します。したがって、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンスへのラウンド トリップが完了するのはコマンドを実行した結果が返ってきたときです。 その後、**GetNextRows** 呼び出しはそれぞれがラウンド トリップになります。 このようなしくみによって、サーバー カーソルからのフェッチの結果である行セットを処理する、複数のアクティブなコマンド オブジェクトが存在できます。 詳細については、「[行セットと SQL Server カーソル](../../relational-databases/native-client-ole-db-rowsets/rowsets-and-sql-server-cursors.md)」を参照してください。  
  
## <a name="see-also"></a>参照  
 [コマンド](../../relational-databases/native-client-ole-db-commands/commands.md)  
  
  
