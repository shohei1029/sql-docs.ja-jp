---
title: テーブル値パラメーターへのデータの挿入 (OLE DB ドライバー) | Microsoft Docs
description: OLE DB Driver for SQL Server では、コンシューマーがテーブル値パラメーター行にデータを指定するためのプッシュ モデルとプル モデルがサポートされています。
ms.custom: ''
ms.date: 06/14/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: connectivity
ms.topic: reference
helpviewer_keywords:
- table-valued parameters, inserting data into
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: c815febe24b321efb1ec1eb1dc99080baf3c5d43
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104750842"
---
# <a name="inserting-data-into-table-valued-parameters"></a>テーブル値パラメーターへのデータの挿入
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[Driver_OLEDB_Download](../../../includes/driver_oledb_download.md)]

  OLE DB Driver for SQL Server では、コンシューマーがテーブル値パラメーターの行にデータを指定するときのモデルとして、プッシュ モデルとプル モデルという 2 つのモデルがサポートされます。 プル モデルの使用方法を示すサンプルを利用できます。「[SQL Server データ プログラミング サンプル](https://msftdpprodsamples.codeplex.com/)」を参照してください。  
  
> [!NOTE]  
>  テーブル値パラメーターの列では、すべての行に既定値以外の値が格納されているか、すべての行に既定値が格納されているかのどちらかでなければなりません。 一部の行にだけ既定値を格納することはできません。 したがって、テーブル値パラメーターのバインドでは、テーブル値パラメーターの行セット列データに許容される状態値は DBSTATUS_S_ISNULL と DBSTATUS_S_OK だけです。 DBSTATUS_S_DEFAULT ではエラーが発生し、バインド状態値が DBSTATUS_E_BADSTATUS に設定されます。  
  
## <a name="push-model-loads-all-table-valued-parameter-data-in-memory"></a>プッシュ モデル (メモリ内のすべてのテーブル値パラメーターのデータを読み込む)  
 プッシュ モデルは、パラメーター セットの使用方法 (ICommand::Execute の DBPARAMS パラメーター) に似ています。 プッシュ モデルが使用されるのは、IRowset インターフェイスをカスタマイズして実装せずに、テーブル値パラメーターの行セット オブジェクトを使用する場合だけです。 テーブル値パラメーターの行セット内の行数が少なく、アプリケーションに高いメモリ負荷がかからないことが見込まれる場合は、プッシュ モデルをお勧めします。 プッシュ モデルは、プル モデルよりも簡単な方法です。このモデルでは、コンシューマー アプリケーションに対し、標準的な OLE DB アプリケーションで現在一般的である以上の機能は要求されません。  
  
 コンシューマーは、コマンドを実行する前に、すべてのテーブル値パラメーターのデータをプロバイダーに提供します。 コンシューマーは、データを提供するために、各テーブル値パラメーター用にテーブル値パラメーターの行セット オブジェクトを作成します。 テーブル値パラメーターの行セット オブジェクトでは、行セットの挿入、設定、および削除の各操作が公開されます。このような操作は、コンシューマーがテーブル値パラメーターのデータを操作する際に使用します。 プロバイダーでは、実行時にこのテーブル値パラメーターの行セット オブジェクトからデータをフェッチします。  
  
 テーブル値パラメーターの行セット オブジェクトがコンシューマーに提供されると、コンシューマーはそのオブジェクトを行セット オブジェクトとして処理できます。 コンシューマーは IColumnsInfo::GetColumnInfo インターフェイス メソッドまたは IColumnsRowset::GetColumnsRowset インターフェイス メソッドを使用し、各列の型情報 (型、最大長、有効桁数、小数点以下桁数) を取得できます。 その後、コンシューマーはアクセサーを作成してデータのバインドを指定します。 次に、データ行をテーブル値パラメーターの行セットに挿入します。 これは IRowsetChange::InsertRow の使用で行えます。 データを操作する必要がある場合、IRowsetChange::SetData または IRowsetChange::DeleteRows もテーブル値パラメーターの行セット オブジェクトで使用できます。 テーブル値パラメーターの行セット オブジェクトは、ストリーム オブジェクトと同様に参照数がカウントされます。  
  
 IColumnsRowset::GetColumnsRowset が使用される場合、後で、結果的に生成される列の行セット オブジェクトで IRowset::GetNextRows、IRowset::GetData、IRowset::ReleaseRows メソッドが呼び出されます。  
  
 OLE DB Driver for SQL Server でコマンドの実行が開始されると、テーブル値パラメーターの値は、このテーブル値パラメーターの行セット オブジェクトからフェッチされ、サーバーに送信されます。  
  
 プッシュ モデルでは、コンシューマーによる作業は必要最小限になりますが、使用するメモリはプル モデルよりも多くなります。これは、すべてのテーブル値パラメーターのデータが実行時にメモリ内に存在する必要があるためです。  
  
## <a name="pull-model-obtaining-table-valued-parameter-data-on-demand-from-the-consumer"></a>プル モデル (要求時にコンシューマーからテーブル値パラメーターのデータを取得する)  
 プル モデルは、次の 2 つのシナリオで役立ちます。  
  
-   行をストリームする場合。  
  
-   別のプロバイダーの行セットがテーブル値パラメーターの値として使用されている場合。  
  
 プル モデルでは、コンシューマーが要求時にプロバイダーにデータを提供します。 この方法は、アプリケーションでデータの挿入が何度も行われ、メモリ内のテーブル値パラメーターの行セットのデータが過度にメモリにアクセスする場合に使用します。 複数の OLE DB プロバイダーが使用される場合、プル モデルでは、コンシューマーが任意の行セット オブジェクトをテーブル値パラメーターの値として提供できます。  
  
 プル モデルを使用するには、コンシューマーが行セット オブジェクトを独自に実装する必要があります。 テーブル値パラメーターの行セット (CLSID_ROWSET_TVP) でプル モデルを使用する場合、プロバイダーが ITableDefinitionWithConstraints::CreateTableWithConstraints メソッドまたは IOpenRowset::OpenRowset メソッドを使用して公開する、テーブル値パラメーターの行セット オブジェクトを集計するためにコンシューマーが必要になります。 コンシューマー オブジェクトに期待されるのは、IRowset インターフェイスの実装をオーバーライドすることだけです。 次の関数をオーバーライドする必要があります。  
  
-   IRowset::GetNextRows  
  
-   IRowset::AddRefRows  
  
-   IRowset::GetData  
  
-   IRowset::ReleaseRows  
  
-   IRowset::RestartPosition  
  
 OLE DB Driver for SQL Server では、コンシューマーの行セット オブジェクトから一度に 1 つ以上の行を読み取り、テーブル値パラメーターのストリーミング動作をサポートします。 たとえば、ユーザーは、(メモリではなく) ディスクにテーブル値パラメーターの行セットのデータを保持し、OLE DB Driver for SQL Server から要求されたときにディスクからデータを読み取る機能を実装する場合があります。  
  
 コンシューマーは、テーブル値パラメーターの行セット オブジェクトで IAccessor::CreateAccessor を使用して、コンシューマーのデータ形式を OLE DB Driver for SQL Server に通知します。 プロバイダーは、データをコンシューマー バッファーから読み取る際に、既定以外の書き込み可能な列すべてを少なくとも 1 つのアクセサー ハンドルで使用できることを確認し、対応するハンドルを使用して列のデータを読み取ります。 あいまいさを排除するため、テーブル値パラメーターの行セットの列とバインドが一対一で対応するようにする必要があります。 同じ列に対するバインドが重複すると、エラーが発生します。 また、各アクセサーでは、DBBindings の *iOrdinal* メンバーを順番に使用します。 IRowset::GetData が各行のアクセサーの数と同じ回数だけ呼び出されます。呼び出しの順序は、*iOrdinal* 値の順序 (昇順) に基づきます。  
  
 プロバイダーは、テーブル値パラメーターの行セット オブジェクトで公開されるインターフェイスのほとんどを実装することが期待されています。 コンシューマーは、最小限のインターフェイス (IRowset) を使用して行セット オブジェクトを実装します。 無計画な集計があるため、残りの必須の行セット オブジェクトのインターフェイスは、テーブル値パラメーターの行セット オブジェクトによって実装されます。  
  
 任意の OLE DB プロバイダー用に取得した行セット オブジェクトなどの他の行セット オブジェクトの場合、コンシューマーが提供した行セットは、OLE DB の仕様で指定されているとおり、必須の行セット オブジェクトのインターフェイスすべてを実装する必要があります。  
  
 実行時に、OLE DB Driver for SQL Server は行セット オブジェクトにコールバックして、行をフェッチして列のデータを読み取ります。  
  
## <a name="see-also"></a>参照  
 [テーブル値パラメーター &#40;OLE DB&#41;](../../oledb/ole-db-table-valued-parameters/table-valued-parameters-ole-db.md)   
 [テーブル値パラメーターの使用 &#40;OLE DB&#41;](../../oledb/ole-db-how-to/use-table-valued-parameters-ole-db.md)  
  
  
