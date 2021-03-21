---
title: OLE DB テーブル値パラメーターの型のサポート (プロパティ) | Microsoft Docs
description: OLE DB Driver for SQL Server のテーブル値パラメーター行セット オブジェクトに関連付けられている OLE DB のプロパティとプロパティ セットについて説明します。
ms.custom: ''
ms.date: 06/14/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: connectivity
ms.topic: reference
helpviewer_keywords:
- table-valued parameters (OLE DB), API support (properties)
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: 7dc83e9b9cadf8d0569e1a26e852c7b4525e56c6
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104750832"
---
# <a name="ole-db-table-valued-parameter-type-support-properties"></a>OLE DB テーブル値パラメーターの型のサポート (プロパティ)
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[Driver_OLEDB_Download](../../../includes/driver_oledb_download.md)]

  このトピックでは、テーブル値パラメーターの行セット オブジェクトに関連付けられている OLE DB プロパティおよびプロパティ セットについて説明します。  
  
## <a name="properties"></a>Properties  
 次に、テーブル値パラメーターの行セット オブジェクトの IRowsetInfo::GetProperties メソッドを使用して公開されるプロパティの一覧を示します。 テーブル値パラメーターの行セット プロパティはすべて読み取り専用であることに注意してください。 したがって、IOpenRowset::OpenRowset メソッドまたは ITableDefinitionWithConstraints::CreateTableWithConstraints メソッドを使用してプロパティを既定値以外の値に設定しようとすると、エラーが発生し、オブジェクトは作成されません。  
  
 テーブル値パラメーターの行セット オブジェクトで実装されていないプロパティは、次の一覧には含まれていません。 すべてのプロパティの一覧は、Windows Data Access Components の OLE DB に関するドキュメントを参照してください。  
  
|プロパティ ID|値|  
|-----------------|-----------|  
|DBPROP_ABORTPRESERVE|VARIANT_TRUE|  
|DBPROP_ACCESSORDER|DBPROPVAL_AO_RANDOM|  
|DBPROP_BLOCKINGSTORAGEOBJECTS|VARIANT_TRUE|  
|DBPROP_BOOKMARKS<br /><br /> DBPROP_LITERALBOOKMARKS|R/W:読み取り専用<br /><br /> 既定値はVARIANT_FALSE<br /><br /> 説明:ブックマークはテーブル値パラメーターの行セット オブジェクトで利用できません。|  
|DBPROP_BOOKMARKSKIPPED|VARIANT_FALSE|  
|DBPROP_BOOKMARKTYPE|DBPROPVAL_BMK_NUMERIC|  
|DBPROP_CANHOLDROWS|VARIANT_FALSE|  
|DBPROP_CHANGEINSERTEDROWS|VARIANT_TRUE|  
|DBPROP_COLUMNRESTRICT|VARIANT_FALSE|  
|DBPROP_COMMANDTIMEOUT|0|  
|DBPROP_COMMITPRESERVE|VARIANT_TRUE|  
|DBPROP_DEFERRED|VARIANT_FALSE|  
|DBPROP_DELAYSTORAGEOBJECTS|VARIANT_FALSE|  
|DBPROP_IAccessor<br /><br /> DBPROP_IColumnsInfo<br /><br /> DBPROP_IConvertType<br /><br /> DBPROP_IRowset<br /><br /> DBPROP_IRowsetInfo<br /><br /> DBPROP_IColumnsRowset|VARIANT_TRUE|  
|DBPROP_IConnectionPointContainer<br /><br /> DBPROP_IMultipleResults<br /><br /> DBPROP_IRowsetUpdate<br /><br /> DBPROP_IRowsetIdentity<br /><br /> DBPROP_IRowsetLocate<br /><br /> DBPROP_IRowsetScroll<br /><br /> DBPROP_IRowsetResynch|VARIANT_FALSE|  
|DBPROP_IRowsetChange|VARIANT_TRUE<br /><br /> 注:テーブル値パラメーターの行セット オブジェクトでは、IRowsetChange インターフェイスがサポートされます。<br /><br /> DBPROP_IRowsetChange を VARIANT_TRUE に設定して作成された行セットの動作は、即時更新モードの動作になります。<br /><br /> ただし、BLOB 列が ISequentialStream オブジェクトとしてバインドされる場合、コンシューマーでは、テーブル値パラメーターの行セット オブジェクトの有効期間中その列を保持する必要があります。|  
|DBPROP_ISupportErrorInfo|VARIANT_TRUE|  
|DBPROP_ISequentialStream|VARIANT_TRUE|  
|DBPROP_IMMOBILEROWS|VARIANT_TRUE|  
|DBPROP_LITERALIDENTITY|VARIANT_TRUE|  
|DBPROP_LOCKMODE|DBPROPVAL_LM_NONE|  
|DBPROP_MAXOPENROWS|0|  
|DBPROP_MAXPENDINGROWS|0|  
|DBPROP_MAXROWS|0|  
|DBPROP_NOTIFICATIONPHASES|0|  
|DBPROP_NOTIFYCOLUMNSET DBPROP_NOTIFYROWDELETE DBPROP_NOTIFYROWFIRSTCHANGE DBPROP_NOTIFYROWINSERT DBPROP_NOTIFYROWRESYNCH DBPROP_NOTIFYROWSETRELEASE DBPROP_NOTIFYROWSETFETCH-POSITIONCHANGE DBPROP_NOTIFYROWUNDOCHANGE DBPROP_NOTIFYROWUNDODELETE DBPROP_NOTIFYROWUNDOINSERT DBPROP_NOTIFYROWUPDATE|0|  
|DBPROP_OTHERINSERT DBPROP_OTHERUPDATEDELETE|VARIANT_FALSE|  
|DBPROP_OWNINSERT<br /><br /> DBPROP_OWNUPDATEDELETE|VARIANT_TRUE|  
|DBPROP_QUICKRESTART|VARIANT_TRUE|  
|DBPROP_REENTRANTEVENTS|VARIANT_FALSE|  
|DBPROP_REMOVEDELETED|VARIANT_TRUE|  
|DBPROP_RETURNPENDINGINSERTS|VARIANT_TRUE|  
|DBPROP_ROWRESTRICT|VARIANT_FALSE|  
|DBPROP_ROWTHREADMODEL|DBPROPVAL_RT_FREETHREAD|  
|DBPROP_SERVERCURSOR|VARIANT_FALSE|  
|DBPROP_SERVERDATAONINSERT|VARIANT_FALSE|  
|DBPROP_STRONGIDENTITY|VARIANT_TRUE|  
|DBPROP_TRANSACTEDOBJECT|VARIANT_FALSE|  
|DBPROP_UNIQUEROWS|VARIANT_FALSE|  
|DBPROP_UPDATABILITY|DBPROPVAL_UP_CHANGE &#124; DBPROPVAL_UP_DELETE &#124; DBPROPVAL_UP_INSERT|  
  
## <a name="property-sets"></a>プロパティ セット  
 次のプロパティ セットでは、テーブル値パラメーターがサポートされます。  
  
### <a name="dbpropset_sqlservercolumn"></a>DBPROPSET_SQLSERVERCOLUMN  
 このプロパティは、テーブル値パラメーターの行セット オブジェクトを作成するプロセスでコンシューマーによって使用されます。このプロセスでは、必要に応じて、DBCOLUMNDESC 構造体から各列の ITableDefinitionWithConstraints::CreateTableWithConstraints を使用します。  
  
|プロパティ ID|プロパティ値|  
|-----------------|--------------------|  
|SSPROP_COL_COMPUTED|R/W:読み取り/書き込み<br /><br /> 既定値はVARIANT_FALSE<br /><br /> 型: VT_BOOL<br /><br /> 説明:VARIANT_TRUE に設定された場合、その列が計算列であることを示します。 VARIANT_FALSE に設定された場合は、その列が計算列ではないことを示します。|  
  
### <a name="dbpropset_sqlserverparameter"></a>DBPROPSET_SQLSERVERPARAMETER  
 これらのプロパティは、ISSCommandWithParameters::GetParameterProperties の呼び出しでテーブル値パラメーターの型情報の検出中にコンシューマーによって読み取られ、ISSCommandWithParameters::SetParameterProperties を使用してテーブル値パラメーターに関する特定のプロパティの設定中にコンシューマーによって設定されます。  
  
 次の表に、これらのプロパティの詳細を示します。  
  
|プロパティ ID|プロパティ値|  
|-----------------|--------------------|  
|SSPROP_PARAM_TYPE_TYPENAME|R/W:読み取り/書き込み<br /><br /> 既定値はVT_EMPTY<br /><br /> 型: VT_BSTR<br /><br /> 説明:コンシューマーは、このプロパティを使用して、テーブル値パラメーターの型名を取得または設定します。<br /><br /> このプロパティは、CLR ユーザー定義型と共に使用することもできます。<br /><br /> このプロパティは、テーブル値パラメーターのテーブルの型名を指定するように、必要に応じて指定できます (ODBC の呼び出し構文のコマンドの場合)。 このプロパティは、パラメーター化されたアドホック SQL クエリに必要です。|  
|SSPROP_PARAM_TYPE_SCHEMANAME|R/W:読み取り/書き込み<br /><br /> 既定値はVT_EMPTY<br /><br /> 型: VT_BSTR<br /><br /> 説明:コンシューマーは、このプロパティを使用して、テーブル値パラメーターの型のスキーマ名を取得または設定します。<br /><br /> このプロパティは、CLR ユーザー定義型と共に使用することもできます。|  
|SSPROP_PARAM_TYPE_CATALOGNAME|R/W:読み取り専用<br /><br /> 既定値はVT_EMPTY<br /><br /> 型: VT_BSTR<br /><br /> 説明:コンシューマーは、このプロパティを使用して、テーブル値パラメーターの型のカタログ名を取得します。<br /><br /> このプロパティは、CLR ユーザー定義型と共に使用することもできます。 このプロパティを設定するとエラーになります。ユーザー定義テーブル型は、それを使用するテーブル値パラメーターと同じデータベースに内にある必要があります。|  
|SSPROP_PARAM_TABLE_DEFAULT_COLUMNS|R/W:読み取り/書き込み<br /><br /> 既定値はVT_EMPTY<br /><br /> 型: VT_UI2 &#124; VT_ARRAY<br /><br /> 説明:コンシューマーは、このプロパティを使用して、既定値として扱う、行セット内の列セットを指定します。 これらの列の値は送信されません。 プロバイダーは、コンシューマーの行セット オブジェクトからデータをフェッチしている間、このような列のバインドを必要としません。<br /><br /> 配列の各要素は、行セット オブジェクト内の列の序数である必要があります。 序数が無効な場合、コマンドの実行時にエラーが発生します。|  
|SSPROP_PARAM_TABLE_COLUMN_ORDER|R/W:読み取り/書き込み<br /><br /> 既定値はVT_EMPTY<br /><br /> 型: VT_UI2 &#124; VT_ARRAY<br /><br /> 説明:このプロパティは、列データの並べ替え順序を示すヒントをサーバーに提供するためにコンシューマーによって使用されます。 プロバイダーでは検証は行われず、提供された仕様にコンシューマーが準拠していることが想定されます。 サーバーでは、このプロパティを使用して最適化を実行します。<br /><br /> 各列の列順序情報は、配列内の要素のペアで表されます。 ペアの最初の要素は列数で、 ペアの 2 番目の要素は、昇順の場合は 1、降順の場合は 2 になります。|  
  
## <a name="see-also"></a>参照  
 [OLE DB テーブル値パラメーターの型のサポート](../../oledb/ole-db-table-valued-parameters/ole-db-table-valued-parameter-type-support.md)   
 [テーブル値パラメーターの使用 &#40;OLE DB&#41;](../../oledb/ole-db-how-to/use-table-valued-parameters-ole-db.md)  
  
  
