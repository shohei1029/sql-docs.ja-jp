---
title: テーブル値パラメーターの行セットの作成 (OLE DB ドライバー)
description: OLE DB Driver for SQL Server のコンシューマーが作成できるメモリ内のオブジェクトであるテーブル値パラメーターの行セットについて説明します。
ms.custom: ''
ms.date: 06/14/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: connectivity
ms.topic: reference
helpviewer_keywords:
- table-valued parameters, rowset creation
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: bbde1d2007e18cefba01a43522cbaf5f07a6cee1
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104750792"
---
# <a name="table-valued-parameter-rowset-creation"></a>テーブル値パラメーターの行セットの作成
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[Driver_OLEDB_Download](../../../includes/driver_oledb_download.md)]

  コンシューマーはテーブル値パラメーターに任意の行セット オブジェクトを使用できますが、通常の行セット オブジェクトはバックエンドのデータ ストアに対して実装されるため、パフォーマンスが制限されます。 このため、OLE DB Driver for SQL Server では、コンシューマーがメモリ内データの上位に特殊な行セット オブジェクトを作成できます。 この特殊なメモリ内の行セット オブジェクトは、テーブル値パラメーターの行セットと呼ばれる新しい COM オブジェクトです。 このオブジェクトには、パラメーター セットと同様の機能が用意されています。  
  
 テーブル値パラメーターの行セット オブジェクトは、コンシューマーが、複数のセッション レベルのインターフェイスを使用して、入力パラメーターに対して明示的に作成します。 テーブル値パラメーターの行セット オブジェクトは、テーブル値パラメーターごとに 1 つのインスタンスがあります。 コンシューマーは、既知のメタデータ情報を指定するか (静的なシナリオ)、プロバイダー インターフェイスを使用してメタデータ情報を検出する (動的なシナリオ) ことにより、テーブル値パラメーターの行セット オブジェクトを作成できます。 この後の各セクションで、これらの 2 つのシナリオについて説明します。  
  
## <a name="static-scenario"></a>静的なシナリオ  
 型情報がわかっている場合、コンシューマーは ITableDefinitionWithConstraints::CreateTableWithConstraints を使用し、テーブル値パラメーターに対応するテーブル値パラメーターの行セット オブジェクトのインスタンスを作成します。  
  
 *guid* フィールド (*pTableID* パラメーター) には、特殊な GUID (CLSID_ROWSET_TVP) を含めます。 *pwszName* メンバーには、コンシューマーがインスタンスを作成するテーブル値パラメーターの型の名前を含めます。 *eKind* フィールドは、DBKIND_GUID_NAME に設定されます。 この名前は、ステートメントがアドホック SQL の場合に必要です。プロシージャ呼び出しの場合、名前は省略可能です。  
  
 コンシューマーは集計の場合、制御している IUnknown と共に *pUnkOuter* パラメーターを渡します。  
  
 テーブル値パラメーターの行セット オブジェクトのプロパティは読み取り専用のため、コンシューマーでは *rgPropertySets* のどのプロパティも設定する必要はありません。  
  
 各 DBCOLUMNDESC 構造体の *rgPropertySets* メンバーの場合、コンシューマーは列ごとに追加のプロパティを指定できます。 これらのプロパティは、DBPROPSET_SQLSERVERCOLUMN プロパティ セットに属しています。 これらのプロパティによって、列ごとに計算の設定と既定の設定を指定できます。 また、NULL 値の許容や ID など、列の既存のプロパティもサポートされます。  
  
 コンシューマーでは、テーブル値パラメーターの行セット オブジェクトから対応する情報を取得するために、IRowsetInfo::GetProperties を使用します。  
  
 各列の状態 (null、unique、computed、update) に関する情報を取得する目的で、コンシューマーは IColumnsRowset::GetColumnsRowset または IColumnsInfo::GetColumnInfo を使用できます。 これらのメソッドでは、各テーブル値パラメーターの行セットの列に関する詳細情報が取得できます。  
  
 コンシューマーは、テーブル値パラメーターの列ごとに型を指定します。 この方法は、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] でテーブルを作成する際に列を指定する方法と似ています。 コンシューマーは、*ppRowset* 出力パラメーターを使用し、OLE DB Driver for SQL Server からテーブル値パラメーターの行セット オブジェクトを取得します。  
  
## <a name="dynamic-scenario"></a>動的なシナリオ  
 コンシューマーで型情報がわからない場合は、IOpenRowset::OpenRowset を使用してテーブル値パラメーターの行セット オブジェクトのインスタンスを作成する必要があります。 すべてのコンシューマーは、プロバイダーに型名を知らせる必要があります。  
  
 このシナリオでは、プロバイダーがコンシューマーに代わって、テーブル値パラメーターの行セット オブジェクトに関する型情報をサーバーから取得します。  
  
 *pTableID* パラメーターおよび *pUnkOuter* パラメーターは、静的なシナリオと同様に設定します。 その後、OLE DB Driver for SQL Server が型情報 (列情報と制約) をサーバーから取得し、*ppRowset* パラメーターを使用してテーブル値パラメーターの行セット オブジェクトを返します。 この操作にはサーバーとの通信が必要になるため、静的なシナリオよりもパフォーマンスが低くなります。 動的なシナリオは、パラメーター化されたプロシージャ呼び出しでのみ動作します。  
  
## <a name="see-also"></a>参照  
 [テーブル値パラメーター &#40;OLE DB&#41;](../../oledb/ole-db-table-valued-parameters/table-valued-parameters-ole-db.md)   
 [テーブル値パラメーターの使用 &#40;OLE DB&#41;](../../oledb/ole-db-how-to/use-table-valued-parameters-ole-db.md)  
  
  
