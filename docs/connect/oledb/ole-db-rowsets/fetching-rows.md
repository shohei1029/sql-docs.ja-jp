---
title: 行のフェッチ (OLE DB ドライバー) | Microsoft Docs
description: OLE DB Driver for SQL Server のコンシューマーで IRowset インターフェイスを使用して行のフェッチ、行からのデータの取得、および行の管理を行う方法について説明します。
ms.custom: ''
ms.date: 06/14/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: connectivity
ms.topic: reference
helpviewer_keywords:
- fetching rows
- OLE DB rowsets, fetching
- rowsets [OLE DB], fetching
- IRowset interface
- OLE DB Driver for SQL Server, fetching
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: 43f50c291902c0fa488683992282470ee2ad9374
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104753032"
---
# <a name="fetching-rows"></a>行のフェッチ
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[Driver_OLEDB_Download](../../../includes/driver_oledb_download.md)]

  **IRowset** インターフェイスは、行セットの基本インターフェイスです。 **IRowset** インターフェイスには、行を順番にフェッチするメソッド、フェッチした行からデータを取得するメソッド、行を管理するメソッドが用意されています。 コンシューマーは、すべての基本的な行セット操作に **IRowset** のメソッドを使用します。 基本的な操作には、行のフェッチと解放、列値の取得などがあります。  
  
 コンシューマーは行セットのインターフェイス ポインターを取得してから、通常、まず **IRowsetInfo::GetProperties** メソッドを使用して行セットの機能を判断します。 このメソッドにより、行セットが公開しているインターフェイスに関する情報に加えて、個別のインターフェイスとしては提供されない行セットの機能に関する情報も返されます。このような行セットの情報には、アクティブ行の最大数や、保留中の更新を同時に含むことができる行数などが含まれています。  
  
 次に、コンシューマーは、行セット内の列の特性、つまりメタデータを調査します。 この調査では、**IColumnsInfo** メソッドを使用して簡易列情報を取得するか、または **IColumnsRowset** を使用して拡張列情報を取得します。 **GetColumnInfo** メソッドは、次の情報を返します。  
  
-   結果セット内の列数。  
  
-   DBCOLUMNINFO 構造体の配列。列ごとに 1 つの構造体があります。  
  
     構造体の順序は、行セット内で列が表示される順序です。 各 DBCOLUMNINFO 構造体には、列名、列の序数、列の値の最大長、列のデータ型、有効桁数、長さなど、列のメタデータが格納されます。  
  
-   1 つのアロケーション ブロック内にあるすべての文字列値に関するストレージへのポインター。  
  
 コンシューマーは必要な列を、メタデータから判断するか、行セットを生成したテキスト コマンドに基づいて判断します。 **IColumnsInfo** によって返される列情報の順番、または **IColumnsRowset** によって返される列メタデータ行セット内の序数から、必要な列の序数が決まります。  
  
 **IColumnsInfo** インターフェイスと **IColumnsRowset** インターフェイスを使用して、行セット内の列に関する情報を抽出します。 **IColumnsInfo** インターフェイスが限定された情報を返すのに対して、**IColumnsRowset** はすべてのメタデータを提供します。  
  
> [!NOTE]  
>  [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Version 7.0 以前では、**IColumnsInfo::GetColumnsInfo** によって返される省略可能なメタデータ列 DBCOLUMN_COMPUTEMODE は、列が計算列かどうかを示す値ではなく、DBSTATUS_S_ISNULL を返します。これは、基になる列が計算列かどうかを判断できないためです。  
  
 序数を使用して、列へのバインドを指定します。 バインドとは、コンシューマーの構造体の要素を列に関連付ける 1 つの構造体です。 バインドでは、列のデータ値、長さ、および状態値をバインドできます。  
  
 バインドのセットは、アクセサーでまとめて収集されます。 アクセサーは、**IAccessor::CreateAccessor** メソッドを使用して作成します。 アクセサーには複数のバインドを含めることができるので、複数の列のデータを 1 回の呼び出しで取得または設定できます。 コンシューマーは、アプリケーションのさまざまな部分によって異なる使用パターンに合わせて、複数のアクセサーを作成できます。 行セットが存在する間は、アクセサーを作成および解放できます。  
  
 コンシューマーはデータベースから行をフェッチするために、**IRowset::GetNextRows** や **IRowsetLocate::GetRowsAt** などのメソッドを呼び出します。 これらのフェッチ操作により、サーバーの行データがプロバイダーの行バッファーに格納されます。 コンシューマーは、プロバイダーの行バッファーに直接アクセスできません。 **IRowset::GetData** を使用して、プロバイダーのバッファーのデータをコンシューマーのバッファーにコピーし、**IRowsetChange::SetData** を使用して、コンシューマーのバッファー内で変更されたデータをプロバイダーのバッファーにコピーします。  
  
 コンシューマーは行へのハンドル、アクセサーへのハンドル、およびコンシューマーが割り当てたバッファーへのポインターを渡して、**GetData** メソッドを呼び出します。 **GetData** は、アクセサーの作成に使用したバインドに指定されているとおりに、データを変換し、列を返します。 コンシューマーはさまざまなアクセサーやバッファーを使用して、1 つの行に対して **GetData** を複数回呼び出すことができます。その結果、同じデータの複数のコピーを取得することができます。  
  
 可変長列のデータは、さまざまな方法で処理できます。 最初に、このような列をコンシューマーの構造体の有限部分にバインドできます。 この場合、データの長さがバッファー長を超えると切り捨てが発生します。 コンシューマーは状態 DBSTATUS_S_TRUNCATED を確認することで、切り捨てが発生したかどうかを判断できます。 返される長さは、常に、実際のバイト単位の長さなので、切り捨てられたデータ量も判断できます。  
  
 コンシューマーは行のフェッチまたは更新を終了するときに、**ReleaseRows** メソッドを使用して行を解放します。 これにより、行セット内の行のコピーからリソースが解放され、新しい行の空き領域が確保されます。 その後、行のフェッチや作成、行データへのアクセスといったサイクルを繰り返すことができます。  
  
 コンシューマーは行セットの操作を終了するときに、**IAccessor::ReleaseAccessor** メソッドを呼び出してアクセサーを解放します。 さらに、行セットが公開しているすべてのインターフェイスで **IUnknown::Release** メソッドを呼び出して、行セットを解放します。 行セットが解放されると、コンシューマーが保持している残りの行やアクセサーも強制的に解放されます。  
  
## <a name="in-this-section"></a>このセクションの内容  
  
-   [次のフェッチ位置](../../oledb/ole-db-rowsets/fetching-rows-next-fetch-position.md)  
  
## <a name="see-also"></a>参照  
 [行セット](../../oledb/ole-db-rowsets/rowsets.md)  
  
  
