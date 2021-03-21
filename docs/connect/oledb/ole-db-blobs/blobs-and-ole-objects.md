---
title: BLOB と OLE オブジェクト (OLE DB ドライバー) | Microsoft Docs
description: OLE DB Driver for SQL Server での、バイナリ ラージ オブジェクトとしての SQL Server データ型へのコンシューマー アクセスに対する ISequentialStream インターフェイスによるサポート方法について説明します。
ms.custom: ''
ms.date: 05/25/2020
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: connectivity
ms.topic: reference
helpviewer_keywords:
- BLOBs, OLE objects
- BLOBs
- storage object [OLE DB]
- OLE DB Driver for SQL Server, BLOBs
- large data, OLE objects
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: 80c1c1eff66c19b009dcfdd9164129734f6c4362
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104749182"
---
# <a name="blobs-and-ole-objects"></a>BLOB と OLE オブジェクト
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[Driver_OLEDB_Download](../../../includes/driver_oledb_download.md)]

  OLE DB Driver for SQL Server で公開されている **ISequentialStream** インターフェイスにより、コンシューマーはバイナリ ラージ オブジェクト (BLOB) として [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] の **ntext**、**text**<a href="#text_note"><sup>**1**</sup></a>、**image**、**varchar(max)** 、**nvarchar(max)** 、**varbinary(max)** 、および xml の各データ型にアクセスできます。 **ISequentialStream** の **Read** メソッドを使用すると、扱いやすい単位で大量のデータを取得できます。

 <b id="text_note">[1]:</b>ISequentialStream インターフェイスを使用して UTF-8 でエンコードされたデータをレガシ テキスト列に挿入することは、UTF-8 をサポートするサーバーのみに制限されます。 UTF-8 をサポートしていないサーバーをターゲットにしてこのシナリオを実行しようとすると、ドライバーによって次のエラー メッセージが送信されます。"*選択された列の型上でストリームはサポートされていません*"。

 この機能を示すサンプルについては、「[大きなデータの設定 (OLE DB)](../../oledb/ole-db-how-to/set-large-data-ole-db.md)」を参照してください。  
  
 コンシューマーからデータ変更用にバインドされたアクセサーにインターフェイス ポインターを渡すとき、OLE DB Driver for SQL Server は、コンシューマーに実装された **IStorage** インターフェイスを使用できます。  
  
 大きな値データ型の場合、OLE DB Driver for SQL Server では、**IRowset** インターフェイスや DDL インターフェイスで型の想定サイズの確認が行われます。 **varchar** 型、**nvarchar** 型、および **varbinary** データ型の列の最大サイズが無制限に設定されている場合、列のデータ型を返すスキーマ行セットおよびインターフェイスによって列は ISLONG と表されます。  
  
 OLE DB Driver for SQL Server では、**varchar(max)** 型、**varbinary(max)** 型、および **nvarchar(max)** 型が、それぞれ DBTYPE_STR、DBTYPE_BYTES、および DBTYPE_WSTR として公開されます。  
  
 このようなデータ型を使用して作業するために、アプリケーションでは次のような操作を行えます。  
  
-   データ型 DBTYPE_STR、DBTYPE_BYTES、または DBTYPE_WSTR としてバインドします。 バッファーのサイズが十分でない場合、これらのデータ型は以前のリリースでの動作と同様に (以前よりも大きな値を格納できるようになりましたが)、切り捨てが行われます。  
  
-   データ型としてバインドし、DBTYPE_BYREF も指定します。  
  
-   DBTYPE_IUNKNOWN としてバインドし、ストリーミングを使用します。  
  
 DBTYPE_IUNKNOWN にバインドすると、ISequentialStream ストリーム機能が使用されます。 OLE DB Driver for SQL Server では、大きな値データ型の DBTYPE_IUNKNOWN としての出力パラメーターのバインドがサポートされます。 これは、DBTYPE_IUNKNOWN としてクライアントに返されるこれらのデータ型を、ストアド プロシージャが戻り値として返すシナリオをサポートするものです。  
  
## <a name="storage-object-limitations"></a>ストレージ オブジェクトの制限事項  
  
-   OLE DB Driver for SQL Server でサポートされる、開いているストレージ オブジェクトは 1 つのみです。 複数の **ISequentialStream** インターフェイス ポインターへの参照を取得するために、複数のストレージ オブジェクトを開こうとすると、DBSTATUS_E_CANTCREATE が返されます。  
  
-   OLE DB Driver for SQL Server の読み取り専用プロパティ DBPROP_BLOCKINGSTORAGEOBJECTS の既定値は VARIANT_TRUE です。 そのため、ストレージ オブジェクトがアクティブの場合は、(ストレージ オブジェクト以外の) 一部のメソッドが失敗して E_UNEXPECTED が返されます。  
  
-   コンシューマーに実装されたストレージ オブジェクトを参照する行アクセサーを作成するときは、そのオブジェクトのデータ長を OLE DB Driver for SQL Server 側で認識しておく必要があります。 コンシューマー側では、アクセサーの作成に使用する DBBINDING 構造体に長さのインジケーターをバインドする必要があります。  
  
-   行に 1 つの大きなデータ値とそれ以外のデータが格納されていて、DBPROP_ACCESSORDER が DBPROPVAL_AO_RANDOM ではない場合は、OLE DB Driver for SQL Server のカーソルに対応した行セットを使用して行のデータを取得するか、すべての大きなデータ値を処理してから行の他の値を取得する必要があります。 DBPROP_ACCESSORDER が DBPROPVAL_AO_RANDOM の場合、OLE DB Driver for SQL Server によりすべての XML データ型がバイナリ ラージ オブジェクト (BLOB) としてキャッシュされ、それらに任意の順序でアクセスできます。  
  
## <a name="in-this-section"></a>このセクションの内容  
  
-   [大きなデータの取得](../../oledb/ole-db-blobs/getting-large-data.md)  
  
-   [大きなデータの設定](../../oledb/ole-db-blobs/setting-large-data.md)  
  
-   [BLOB 出力パラメーターのストリーミング サポート](../../oledb/ole-db-blobs/streaming-support-for-blob-output-parameters.md)  
  
## <a name="see-also"></a>参照  
 [OLE DB Driver for SQL Server のプログラミング](../../oledb/ole-db/oledb-driver-for-sql-server-programming.md)        
 [大きな値の型の使用](../../oledb/features/using-large-value-types.md)  
  
  
