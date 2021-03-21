---
title: 行セットのプロパティと動作 (OLE DB ドライバー)
description: これらは OLE DB Driver for SQL Server 行セット プロパティです。プロパティ名と説明が含まれます。
ms.custom: ''
ms.date: 09/30/2020
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: connectivity
ms.topic: reference
helpviewer_keywords:
- rowsets [OLE DB], properties
- OLE DB Driver for SQL Server, rowsets
- properties [OLE DB]
- OLE DB rowsets, properties
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: f25839eba4a430c97dbcb0267f26ac99d2d3e215
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104753022"
---
# <a name="rowset-properties-and-behaviors"></a>行セットのプロパティと動作
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[Driver_OLEDB_Download](../../../includes/driver_oledb_download.md)]

  OLE DB Driver for SQL Server の行セットのプロパティを次に示します。
  
|プロパティ ID|説明|  
|-----------------|-----------------|  
|DBPROP_ABORTPRESERVE|R/W:読み取り/書き込み<br /><br /> 既定値はVARIANT_FALSE<br /><br /> 説明:このプロパティによって、中止操作後の行セットの動作が決まります。<br /><br /> VARIANT_FALSE:OLE DB Driver for SQL Server により、中止操作後に行セットが無効になります。 行セット オブジェクトの機能は、ほぼ失われます。 サポートされるのは、**IUnknown** 操作、および未処理の行とアクセサー ハンドルの解放のみです。<br /><br /> VARIANT_TRUE:OLE DB Driver for SQL Server により、有効な行セットが保持されます。|  
|DBPROP_ACCESSORDER|R/W:読み取り/書き込み<br /><br /> 既定値はDBPROPVAL_AO_RANDOM<br /><br /> 説明:アクセス順序。 行セット内の列にアクセスする順序を指定します。<br /><br /> DBPROPVAL_AO_RANDOM:任意の順序で列にアクセスできます。<br /><br /> DBPROPVAL_AO_SEQUENTIALSTORAGEOBJECTS:ストレージ オブジェクトとしてバインドされている列は、列序数で決まるシーケンシャルな順序でのみアクセスできます。<br /><br /> DBPROPVAL_AO_SEQUENTIAL:すべての列が、列序数で決まるシーケンシャルな順序でアクセスされる必要があります。|  
|DBPROP_APPENDONLY|この行セット プロパティは、OLE DB Driver for SQL Server では実装されていません。 このプロパティ値の読み取りまたは書き込みを行うと、エラーが発生します。|  
|DBPROP_BLOCKINGSTORAGEOBJECTS|R/W:読み取り専用<br /><br /> 既定値はVARIANT_TRUE<br /><br /> 説明:OLE DB Driver for SQL Server ストレージ オブジェクトは、他の行セット メソッドを使用してブロックします。|  
|DBPROP_BOOKMARKS DBPROP_LITERALBOOKMARKS|R/W:読み取り/書き込み<br /><br /> 既定値はVARIANT_FALSE<br /><br /> 説明:OLE DB Driver for SQL Server では、DBPROP_BOOKMARKS または DBPROP_LITERALBOOKMARKS が VARIANT_TRUE のとき、行セットの行 ID のブックマークがサポートされます。<br /><br /> いずれかのプロパティを VARIANT_TRUE に設定しても、ブックマークによる行セットの位置指定が有効になるわけではありません。 ブックマークによる行セットの位置指定をサポートする行セットを作成するには、DBPROP_IRowsetLocate または DBPROP_IRowsetScroll を VARIANT_TRUE に設定します。<br /><br /> OLE DB Driver for SQL Server では、[!INCLUDE[msCoName](../../../includes/msconame-md.md)] [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] カーソルを使用して、ブックマークを含む行セットがサポートされます。 詳細については、「[行セットと SQL Server カーソル](../../oledb/ole-db-rowsets/rowsets-and-sql-server-cursors.md)」を参照してください。<br /><br /> 注:これらのプロパティ設定と、OLE DB Driver for SQL Server のその他のカーソル定義プロパティ設定の間に一貫性がないと、エラーが発生します。 たとえば、DBPROP_OTHERINSERT が VARIANT_TRUE のときに、DBPROP_BOOKMARKS を VARIANT_TRUE に設定すると、コンシューマーが行セットを開く時点でエラーが発生します。|  
|DBPROP_BOOKMARKSKIPPED|R/W:読み取り専用<br /><br /> 既定値はVARIANT_FALSE<br /><br /> 説明:ブックマークが設定された行セットの位置指定または検索を実行する際に、コンシューマーが無効なブックマークを報告した場合、OLE DB Driver for SQL Server から DB_E_BADBOOKMARK が返されます。|  
|DBPROP_BOOKMARKTYPE|R/W:読み取り専用<br /><br /> 既定値はDBPROPVAL_BMK_NUMERIC<br /><br /> 説明:OLE DB Driver for SQL Server では、数値のブックマークのみ実装されています。 OLE DB Driver for SQL Server のブックマークは、32 ビットの符号なし整数で、DBTYPE_UI4 型です。|  
|DBPROP_CACHEDEFERRED|この行セット プロパティは、OLE DB Driver for SQL Server では実装されていません。 このプロパティ値の読み取りまたは書き込みを行うと、エラーが発生します。|  
|DBPROP_CANFETCHBACKWARDS DBPROP_CANSCROLLBACKWARDS|R/W:読み取り/書き込み<br /><br /> 既定値はVARIANT_FALSE<br /><br /> 説明:OLE DB Driver for SQL Server では、シーケンシャルではない行セットで逆方向のフェッチと逆方向のスクロールがサポートされます。 OLE DB Driver for SQL Server は、DBPROP_CANFETCHBACKWARDS と DBPROP_CANSCROLLBACKWARDS のいずれかが VARIANT_TRUE のとき、カーソル対応の行セットを作成します。 詳細については、「[行セットと SQL Server カーソル](../../oledb/ole-db-rowsets/rowsets-and-sql-server-cursors.md)」を参照してください。|  
|DBPROP_CANHOLDROWS|R/W:読み取り/書き込み<br /><br /> 既定値はVARIANT_FALSE<br /><br /> 説明: 行セット内に現在ある行に保留中の変更が存在する場合、コンシューマーがその行セットの行をさらに取得しようとすると、既定では、OLE DB Driver for SQL Server により DB_E_ROWSNOTRELEASED が返されます。 この動作は変更できます。<br /><br /> DBPROP_CANHOLDROWS と DBPROP_IRowsetChange の両方が VARIANT_TRUE の場合は、ブックマークが設定された行セットになります。 この 2 つのプロパティが VARIANT_TRUE の場合、行セットでは **IRowsetLocate** インターフェイスを使用でき、DBPROP_BOOKMARKS と DBPROP_LITERALBOOKMARKS の両方が VARIANT_TRUE になります。<br /><br /> ブックマークを含む OLE DB Driver for SQL Server の行セットは、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] カーソルによりサポートされます。|  
|DBPROP_CHANGEINSERTEDROWS|R/W:読み取り/書き込み<br /><br /> 既定値はVARIANT_FALSE<br /><br /> 説明:行セットがキーセット ドリブン カーソルを使用している場合、このプロパティには VARIANT_TRUE のみ設定できます。|  
|DBPROP_COLUMNRESTRICT|R/W:読み取り専用<br /><br /> 既定値はVARIANT_FALSE<br /><br /> 説明:コンシューマーが行セット内の列を変更できないとき、OLE DB Driver for SQL Server により、このプロパティは VARIANT_TRUE に設定されます。 行セットの他の列は、更新可能にできます。また、行自体を削除することもできます。<br /><br /> このプロパティが VARIANT_TRUE の場合、コンシューマーは DBCOLUMNINFO 構造体の *dwFlags* メンバーを確認し、各列に値を書き込めるかどうかを判断します。 列が変更可能な場合は、*dwFlags* は DBCOLUMNFLAGS_WRITE です。|  
|DBPROP_COMMANDTIMEOUT|R/W:読み取り/書き込み<br /><br /> 既定値は0<br /><br /> 説明:既定では、OLE DB Driver for SQL Server は **ICommand::Execute** メソッドでタイムアウトしません。|  
|DBPROP_COMMITPRESERVE|R/W:読み取り/書き込み<br /><br /> 既定値はVARIANT_FALSE<br /><br /> 説明:このプロパティによって、コミット操作後の行セットの動作が決まります。<br /><br /> VARIANT_TRUE:OLE DB Driver for SQL Server により、有効な行セットが保持されます。<br /><br /> VARIANT_FALSE:OLE DB Driver for SQL Server により、コミット操作後に行セットが無効になります。 行セット オブジェクトの機能は、ほぼ失われます。 サポートされるのは、**IUnknown** 操作、および未処理の行とアクセサー ハンドルの解放のみです。|  
|DBPROP_DEFERRED|R/W:読み取り/書き込み<br /><br /> 既定値はVARIANT_FALSE<br /><br /> 説明:VARIANT_TRUE に設定すると、OLE DB Driver for SQL Server では、行セットに対してサーバー カーソルの使用を試みます。 **Text**、**ntext**、**image** 列は、アプリケーションがアクセスしないとサーバーから返されません。|  
|DBPROP_DELAYSTORAGEOBJECTS|R/W:読み取り専用<br /><br /> 既定値はVARIANT_FALSE<br /><br /> 説明:OLE DB Driver for SQL Server では、ストレージ オブジェクトでの即時更新モードがサポートされます。<br /><br /> シーケンシャル ストリーム オブジェクトのデータに加えた変更は、直ちに [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] に送信されます。 変更は、行セットのトランザクション モードに基づいてコミットされます。|  
|DBPROP_HIDDENCOLUMNS|R/W:読み取り専用<br /><br /> 既定値はVARIANT_FALSE<br /><br /> **説明:** 非表示の列数<br /><br /> DBPROP_UNIQUEROWS が VARIANT_TRUE の場合、DBPROP_HIDDENCOLUMNS プロパティは、行セット内の行を一意に識別するためにプロバイダーによって追加された追加の "非表示" 列の数を返します。 これらの列は、**IColumnsInfo::GetColumnInfo** と **IColumnsRowset::GetColumnsRowset** によって返されます。 ただし、**IColumnsInfo::GetColumnInfo** によって *pcColumns* 引数で返される行数には含まれません。<br /><br /> **IColumnsInfo::GetColumnInfo** によって返される *prgInfo* 構造体で表される列の総数 (非表示列を含めた数) を調べるには、**IColumnsInfo::GetColumnInfo** から *pcColumns* で返される列の数に DBPROP_HIDDENCOLUMNS の値を加算します。 DBPROP_UNIQUEROWS が VARIANT_FALSE の場合、DBPROP_HIDDENCOLUMNS は 0 です。|  
|DBPROP_IAccessor DBPROP_IColumnsInfo DBPROP_IConvertType DBPROP_IRowset DBPROP_IRowsetInfo|R/W:読み取り専用<br /><br /> 既定値はVARIANT_TRUE<br /><br /> 説明:OLE DB Driver for SQL Server では、すべての行セットでこれらのインターフェイスがサポートされます。|  
|DBPROP_IColumnsRowset|R/W:読み取り/書き込み<br /><br /> 既定値はVARIANT_TRUE<br /><br /> 説明:OLE DB Driver for SQL Server では、**IColumnsRowset** インターフェイスがサポートされます。|  
|DBPROP_IConnectionPointContainer|R/W:読み取り/書き込み<br /><br /> 既定値はVARIANT_FALSE<br /><br /> 説明:IConnectionPointContainer。 VARIANT_TRUE の場合は、指定したインターフェイスを行セットがサポートします。 VARIANT_FALSE の場合は、指定したインターフェイスを行セットがサポートしません。 インターフェイスをサポートするプロバイダーは、VARIANT_TRUE を指定してインターフェイスに関連付けられたプロパティをサポートする必要があります。 これらのプロパティは、主に、CommandProperties::SetProperties を介してインターフェイスを要求するために使用されます。|  
|DBPROP_IMultipleResults|R/W:読み取り/書き込み<br /><br /> 既定値はVARIANT_FALSE<br /><br /> 説明:OLE DB Driver for SQL Server では、**IMultipleResults** インターフェイスがサポートされます。|  
|DBPROP_IRowsetChange DBPROP_IRowsetUpdate|R/W:読み取り/書き込み<br /><br /> 既定値はVARIANT_FALSE<br /><br /> 説明:OLE DB Driver for SQL Server では、**IRowsetChange** インターフェイスと **IRowsetUpdate** インターフェイスがサポートされます。<br /><br /> DBPROP_IRowsetChange を VARIANT_TRUE に設定して作成された行セットの動作は、即時更新モードの動作になります。<br /><br /> DBPROP_IRowsetUpdate が VARIANT_TRUE の場合、DBPROP_IRowsetChange も VARIANT_TRUE になります。 この場合は、行セットの動作は、遅延更新モードの動作になります。<br /><br /> OLE DB Driver for SQL Server では、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] カーソルを使用して、**IRowsetChange** または **IRowsetUpdate** のいずれかを公開する行セットがサポートされます。 詳細については、「[行セットと SQL Server カーソル](../../oledb/ole-db-rowsets/rowsets-and-sql-server-cursors.md)」を参照してください。|  
|DBPROP_IRowsetIdentity|R/W:読み取り/書き込み<br /><br /> 既定値はVARIANT_TRUE<br /><br /> 説明:OLE DB Driver for SQL Server では、**IRowsetIdentity** インターフェイスがサポートされます。 行セットがこのインターフェイスをサポートする場合、同じ行を基準とする 2 つの行ハンドルは、常に同じデータと状態を示します。 コンシューマーは、**IRowsetIdentity::IsSameRow** メソッドを呼び出して、2 つの行ハンドルを比較して、同じ行インスタンスを参照しているかどうかを確認できます。|  
|DBPROP_IRowsetLocate DBPROP_IRowsetScroll|R/W:読み取り/書き込み<br /><br /> 既定値はVARIANT_FALSE<br /><br /> 説明:OLE DB Driver for SQL Server では、**IRowsetLocate** インターフェイスと **IRowsetScroll** インターフェイスを公開できます。<br /><br /> DBPROP_IRowsetLocate が VARIANT_TRUE の場合、DBPROP_CANFETCHBACKWARDS および DBPROP_CANSCROLLBACKWARDS も VARIANT_TRUE になります。<br /><br /> DBPROP_IRowsetScroll が VARIANT_TRUE の場合、DBPROP_IRowsetLocate も VARIANT_TRUE になり、行セットでこの 2 つのインターフェイスが使用可能になります。<br /><br /> ブックマークは、いずれのインターフェイスにも必要です。 OLE DB Driver for SQL Server は、コンシューマーがいずれかのインターフェイスを要求したときに、DBPROP_BOOKMARKS と DBPROP_LITERALBOOKMARKS を VARIANT_TRUE に設定します。<br /><br /> OLE DB Driver for SQL Server では、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] カーソルを使用して **IRowsetLocate** と **IRowsetScroll** がサポートされます。 詳細については、「[行セットと SQL Server カーソル](../../oledb/ole-db-rowsets/rowsets-and-sql-server-cursors.md)」を参照してください。<br /><br /> これらのプロパティ設定と、OLE DB Driver for SQL Server のその他のカーソル定義プロパティ設定の間に一貫性がないと、エラーが発生します。 たとえば、DBPROP_OTHERINSERT が VARIANT_TRUE のときに、DBPROP_IRowsetScroll を VARIANT_TRUE に設定すると、コンシューマーが行セットを開く時点でエラーが発生します。|  
|DBPROP_IRowsetResynch|R/W:読み取り/書き込み<br /><br /> 既定値はVARIANT_FALSE<br /><br /> 説明:OLE DB Driver for SQL Server では、オンデマンドで **IRowsetResynch** インターフェイスが公開されます。 OLE DB Driver for SQL Server では、任意の行セットでインターフェイスを公開できます。|  
|DBPROP_ISupportErrorInfo|R/W:読み取り/書き込み<br /><br /> 既定値はVARIANT_TRUE<br /><br /> 説明:OLE DB Driver for SQL Server では、行セットで **ISupportErrorInfo** インターフェイスが公開されます。|  
|DBPROP_ILockBytes|このインターフェイスは、OLE DB Driver for SQL Server では実装されていません。 このプロパティの読み取りまたは書き込みを行うと、エラーが発生します。|  
|DBPROP_ISequentialStream|R/W:読み取り/書き込み<br /><br /> 既定値はVARIANT_FALSE<br /><br /> 説明:OLE DB Driver for SQL Server では、**ISequentialStream** インターフェイスを公開して、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] に保存された長い可変長データをサポートします。|  
|DBPROP_IStorage|このインターフェイスは、OLE DB Driver for SQL Server では実装されていません。 このプロパティの読み取りまたは書き込みを行うと、エラーが発生します。|  
|DBPROP_IStream|このインターフェイスは、OLE DB Driver for SQL Server では実装されていません。 このプロパティの読み取りまたは書き込みを行うと、エラーが発生します。|  
|DBPROP_IMMOBILEROWS|R/W:読み取り/書き込み<br /><br /> 既定値はVARIANT_TRUE<br /><br /> 説明:このプロパティは、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] のキーセット カーソルの場合のみ VARIANT_TRUE で、それ以外のカーソルの場合は VARIANT_FALSE です。<br /><br /> VARIANT_TRUE:行セットでは、挿入または更新された行は並べ替えられません。 **IRowsetChange::InsertRow** の場合、行は行セットの最後に挿入されます。 **IRowsetChange::SetData** の場合、行セットが順序付けられていなければ、更新された行の位置は変更されません。 行セットが順序付けられていて、**IRowsetChange::SetData** でその行セットの順序付けに使われている列が変更された場合、行は移動されません。 行セットが一連のキー列を基に構築されている場合 (一般的には DBPROP_OTHERUPDATEDELETE が VARIANT_TRUE になっており、DBPROP_OTHERINSERT が VARIANT_FALSE になっている行セットの場合)、キー列の値を変更すると、通常は、現在の行が削除され、新しい行が挿入された場合と同じ結果になります。 したがって、DBPROP_OWNINSERT が VARIANT_FALSE の場合は、DBPROP_IMMOBILEROWS プロパティが VARIANT_TRUE でも、行が移動されたり、行セットから削除されたように見えることがあります。<br /><br /> VARIANT_FALSE:行セットが順序付けられている場合、挿入された行は、行セットの正しい順序で表示されます。 行セットが順序付けられていない場合は、挿入された行は、行セットの最後に追加されます。 **IRowsetChange::SetData** が行セットの順序付けに使われている列を変更した場合、行は移動されます。 行セットが順序付けされていない場合は、行の位置は変更されません。|  
|DBPROP_LITERALIDENTITY|R/W:読み取り専用<br /><br /> 既定値はVARIANT_TRUE<br /><br /> 説明:このプロパティは、常に VARIANT_TRUE です。|  
|DBPROP_LOCKMODE|R/W:読み取り/書き込み<br /><br /> 既定値はDBPROPVAL_LM_NONE<br /><br /> 説明:行セットによって実行されるロックのレベル (DBPROPVAL_LM_NONE、DBPROPVAL_LM_SINGLEROW) です。<br /><br /> 注:トランザクションでスナップショットの分離を使用する場合、行セットがキーセット カーソルまたは動的サーバー カーソルを使用して開かれ、ロックのモードが DBPROPVAL_LM_SINGLEROW に設定されていると、トランザクションの開始後に別のユーザーがその行を更新している場合に行のフェッチ時にエラーが発生します。 他の種類のカーソルや他の種類のロック モードでは、トランザクションの開始後に別のユーザーが行を更新していても、その行の更新を試みない限り、エラーは発生しません。 どちらの場合も、エラーはサーバーで発生します。|  
|DBPROP_MAXOPENROWS|R/W:読み取り専用<br /><br /> 既定値は0<br /><br /> 説明:OLE DB Driver for SQL Server では、行セットでアクティブにできる行数は制限されません。|  
|DBPROP_MAXPENDINGROWS|R/W:読み取り専用<br /><br /> 既定値は0<br /><br /> 説明:OLE DB Driver for SQL Server では、行セットで変更を保留にしておける行数は制限されません。|  
|DBPROP_MAXROWS|R/W:読み取り/書き込み<br /><br /> 既定値は0<br /><br /> 説明:既定では、OLE DB Driver for SQL Server では、行セット内の行数は制限されません。 コンシューマーが DBPROP_MAXROWS 設定すると、OLE DB Driver for SQL Server は、SET ROWCOUNT ステートメントを使用して、行セット内の行数を制限します。<br /><br /> SET ROWCOUNT を使用すると、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] ステートメントの実行が予期しない結果になる可能性があります。 詳細については、「[SET ROWCOUNT](../../../t-sql/statements/set-rowcount-transact-sql.md)」を参照してください。|  
|DBPROP_MAYWRITECOLUMN|この行セット プロパティは、OLE DB Driver for SQL Server では実装されていません。 このプロパティ値の読み取りまたは書き込みを行うと、エラーが発生します。|  
|DBPROP_MEMORYUSAGE|この行セット プロパティは、OLE DB Driver for SQL Server では実装されていません。 このプロパティ値の読み取りまたは書き込みを行うと、エラーが発生します。|  
|DBPROP_NOTIFICATIONGRANULARITY|この行セット プロパティは、OLE DB Driver for SQL Server では実装されていません。 このプロパティ値の読み取りまたは書き込みを行うと、エラーが発生します。|  
|DBPROP_NOTIFICATIONPHASES|R/W:読み取り専用<br /><br /> 既定値はDBPROPVAL_NP_OKTODO &#124; DBPROPVAL_NP_ABOUTTODO &#124;  DBPROPVAL_NP_SYNCHAFTER &#124; DBPROPVAL_NP_FAILEDTODO &#124;  DBPROPVAL_NP_DIDEVENT<br /><br /> 説明:OLE DB Driver for SQL Server では、すべての通知フェーズがサポートされます。|  
|DBPROP_NOTIFYCOLUMNSET DBPROP_NOTIFYROWDELETE DBPROP_NOTIFYROWFIRSTCHANGE DBPROP_NOTIFYROWINSERT DBPROP_NOTIFYROWRESYNCH DBPROP_NOTIFYROWSETRELEASE DBPROP_NOTIFYROWSETFETCH-POSITIONCHANGE DBPROP_NOTIFYROWUNDOCHANGE DBPROP_NOTIFYROWUNDODELETE DBPROP_NOTIFYROWUNDOINSERT DBPROP_NOTIFYROWUPDATE|R/W:読み取り専用<br /><br /> 既定値はDBPROPVAL_NP_OKTODO &#124;  DBPROPVAL_NP_ABOUTTODO<br /><br /> 説明:OLE DB Driver for SQL Server の通知フェーズは、指定された行セットの変更を実行する前はキャンセル可能です。 OLE DB Driver for SQL Server では、実行完了後のフェーズのキャンセルはサポートされません。|  
|DBPROP_ORDEREDBOOKMARKS|この行セット プロパティは、OLE DB Driver for SQL Server では実装されていません。 このプロパティ値の読み取りまたは書き込みを行うと、エラーが発生します。|  
|DBPROP_OTHERINSERT DBPROP_OTHERUPDATEDELETE DBPROP_OWNINSERT DBPROP_OWNUPDATEDELETE|R/W:読み取り/書き込み<br /><br /> 既定値はVARIANT_FALSE<br /><br /> 説明:変更の表示プロパティを設定すると、OLE DB Driver for SQL Server では、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] カーソルを使用して行セットをサポートします。 詳細については、「[行セットと SQL Server カーソル](../../oledb/ole-db-rowsets/rowsets-and-sql-server-cursors.md)」を参照してください。|  
|DBPROP_QUICKRESTART|R/W:読み取り/書き込み<br /><br /> 既定値はVARIANT_FALSE<br /><br /> 説明:VARIANT_TRUE に設定すると、OLE DB Driver for SQL Server では、行セットに対してサーバー カーソルの使用を試みます。|  
|DBPROP_REENTRANTEVENTS|R/W:読み取り専用<br /><br /> 既定値はVARIANT_TRUE<br /><br /> 説明:OLE DB Driver for SQL Server の行セットは再入可能であり、コンシューマーが通知コールバックから再入可能でない行セット メソッドにアクセスしようとした場合に DB_E_NOTREENTRANT を返すことができます。|  
|DBPROP_REMOVEDELETED|R/W:読み取り/書き込み<br /><br /> 既定値はVARIANT_FALSE<br /><br /> 説明:OLE DB Driver for SQL Server により、行セットにより公開される [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] データにへの変更の表示に基づいて、このプロパティの値が変更されます。<br /><br /> VARIANT_TRUE:コンシューマーまたは他の [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] ユーザーが削除した行は、行セットを最新状態に更新したとき、行セットから削除されます。 DBPROP_OTHERINSERT は VARIANT_TRUE です。<br /><br /> VARIANT_FALSE:コンシューマーまたは他の [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] ユーザーが削除した行は、行セットを最新状態に更新したとき、行セットから削除されません。 削除された [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 行の行セット内の状態値は DBROWSTATUS_E_DELETED です。 DBPROP_OTHERINSERT は VARIANT_TRUE です。<br /><br /> このプロパティに含まれる値は、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] カーソルがサポートする行セットに関する値のみです。 詳細については、「[行セットと SQL Server カーソル](../../oledb/ole-db-rowsets/rowsets-and-sql-server-cursors.md)」を参照してください。<br /><br /> DBPROP_REMOVEDELETED プロパティがキーセット カーソルの行セットに実装されている場合、削除された行はフェッチ時に行セットから削除されます。また、**GetNextRows** や **GetRowsAt** などの行フェッチ メソッドから、S_OK および要求された行数よりも少ない行を返すことができます。 この動作は、DB_S_ENDOFROWSET の状態を示すものではないこと、また、返される行数は、残りの行がある場合は決して 0 にならないことに注意してください。|  
|DBPROP_REPORTMULTIPLECHANGES|この行セット プロパティは、OLE DB Driver for SQL Server では実装されていません。 このプロパティ値の読み取りまたは書き込みを行うと、エラーが発生します。|  
|DBPROP_RETURNPENDINGINSERTS|R/W:読み取り専用<br /><br /> 既定値はVARIANT_FALSE<br /><br /> 説明:行をフェッチするメソッドが呼び出されたとき、OLE DB Driver for SQL Server では、挿入が保留中になっている行は返されません。|  
|DBPROP_ROWRESTRICT|R/W:読み取り専用<br /><br /> 既定値はVARIANT_TRUE<br /><br /> 説明:OLE DB Driver for SQL Server の行セットは、行に基づくアクセス権をサポートしていません。 行セットで **IRowsetChange** インターフェイスが公開されている場合、**SetData** メソッドをコンシューマーから呼び出すことができます。|  
|DBPROP_ROWSET_ASYNCH|R/W:読み取り/書き込み<br /><br /> 既定値は0<br /><br /> 説明:行セットの非同期処理を提供します。 このプロパティは、Rowset プロパティ グループおよび DBPROPSET_ROWSET プロパティ セットに含まれています。 型は VT_14 です。<br /><br /> OLE DB Driver for SQL Server でサポートされているビットマスクの唯一の値は、**DBPROPVAL_ASYNCH_INITIALIZE** です。|  
|DBPROP_ROWTHREADMODEL|R/W:読み取り専用<br /><br /> 既定値はDBPROPVAL_RT_FREETHREAD<br /><br /> 説明:OLE DB Driver for SQL Server では、1 つのコンシューマーの複数の実行スレッドから、そのオブジェクトへのアクセスがサポートされます。|  
|DBPROP_SERVERCURSOR|R/W:読み取り/書き込み<br /><br /> 既定値はVARIANT_FALSE<br /><br /> 説明:設定すると、行セットのサポートに [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] カーソルが使われます。 詳細については、「[行セットと SQL Server カーソル](../../oledb/ole-db-rowsets/rowsets-and-sql-server-cursors.md)」を参照してください。|  
|DBPROP_SERVERDATAONINSERT|R/W:読み取り/書き込み<br /><br /> 既定値はVARIANT_FALSE<br /><br /> 説明:挿入時のサーバー データ。<br /><br /> VARIANT_TRUE:挿入がサーバーに送信される時点で、プロバイダーでは、サーバーからデータを取得して、ローカルの行キャッシュを更新します。<br /><br /> VARIANT_FALSE:プロバイダーでは、新しく行を挿入するためにサーバーの値を取得しません。|  
|DBPROP_STRONGIDENTITY|R/W:読み取り専用<br /><br /> 既定値はVARIANT_TRUE<br /><br /> 説明:厳密な行 ID。 行セットに対して挿入が許可されていて (**IRowsetChange** または **IRowsetUpdate** が TRUE)、DBPROP_UPDATABILITY が InsertRows をサポートするように設定されている場合、DBPROP_STRONGIDENTITY の値は DBPROP_CHANGEINSERTEDROWS プロパティの値により決まります (DBPROP_CHANGEINSERTEDROWS プロパティの値が VARIANT_FALSE の場合は VARIANT_FALSE になります)。|  
|DBPROP_TRANSACTEDOBJECT|R/W:読み取り専用<br /><br /> 既定値はVARIANT_FALSE<br /><br /> 説明:OLE DB Driver for SQL Server では、トランザクションされたオブジェクトのみがサポートされます。 詳細については、「[トランザクション](../../oledb/ole-db-transactions/transactions.md)」を参照してください。|  
|DBPROP_UNIQUEROWS|R/W:読み取り/書き込み<br /><br /> 既定値はVARIANT_FALSE<br /><br /> 説明:一意の行。<br /><br /> VARIANT_TRUE:各行は、その列の値で一意に識別されます。 行を一意に識別する列のセットは、**GetColumnInfo** メソッドから返される DBCOLUMNINFO 構造体の DBCOLUMNFLAGS_KEYCOLUMN に設定されます。<br /><br /> VARIANT_FALSE:行は、その列の値で一意に識別される場合とされない場合があります。 またキー列は、DBCOLUMNFLAGS_KEYCOLUMN によって示されない場合があります。|  
|DBPROP_UPDATABILITY|R/W:読み取り/書き込み<br /><br /> 既定値は0<br /><br /> 説明:OLE DB Driver for SQL Server では、すべての DBPROP_UPDATABILITY 値がサポートされます。 DBPROP_UPDATABILITY を設定しても、更新可能な行セットが作成されるわけではありません。 行セットを更新可能にするには、DBPROP_IRowsetChange または DBPROP_IRowsetUpdate を設定してください。|  
  
 次の表に、OLE DB Driver for SQL Server のプロバイダー固有のプロパティ セット DBPROPSET_SQLSERVERROWSET の定義を示します。  
  
|プロパティ ID|説明|  
|-----------------|-----------------|  
|SSPROP_COLUMN_ID|列:ColumnID<br /><br /> R/W:読み取り専用<br /><br /> 型: VT_U12 &#124; VT_ARRAY<br /><br /> 既定値はVT_EMPTY<br /><br /> 説明:現在の [!INCLUDE[tsql](../../../includes/tsql-md.md)] SELECT ステートメント内にある COMPUTE 句の結果列の序数位置 (1 から始まります) を表す整数値の配列。 これは、ODBC SQL_CA_SS_COLUMN_ID 属性に相当する OLE DB Driver for SQL Server です。|  
|SSPROP_DEFERPREPARE|列:いいえ<br /><br /> R/W:読み取り/書き込み<br /><br /> 型: VT_BOOL<br /><br /> 既定値はVARIANT_TRUE<br /><br /> 説明:VARIANT_TRUE:準備実行では、**ICommand::Execute** が呼び出されるか、メタプロパティ操作が実行されるまで、コマンドの準備が遅延されます。 プロパティが次の値に設定されている場合は、ステートメントの準備が行われます。<br /><br /> VARIANT_FALSE:**ICommandPrepare::Prepare** が実行されたとき、ステートメントが準備されます。|  
|SSPROP_IRowsetFastLoad|列:いいえ<br /><br /> R/W:読み取り/書き込み<br /><br /> 型: VT_BOOL<br /><br /> 既定値はVARIANT_FALSE<br /><br /> 説明:このプロパティを VARIANT_TRUE に設定すると、**IOpenRowset::OpenRowset** から行セットを高速に読み込んで開くことができます。 **ICommandProperties::SetProperties** でこのプロパティを設定することはできません。|  
|SSPROP_ISSAsynchStatus|列:いいえ。<br /><br /> R/W:読み取り/書き込み<br /><br /> 型: VT_BOOL<br /><br /> 既定値はVARIANT_FALSE<br /><br /> 説明:このプロパティを VARIANT_TRUE に設定すると、[ISSAsynchStatus](../../oledb/ole-db-interfaces/issasynchstatus-ole-db.md) インターフェイスを使用した非同期操作を実行できます。|  
|SSPROP_ISSDataClassification|R/W:読み取り/書き込み<br /><br />  型: VT_BOOL<br /><br /> 既定値はVARIANT_TRUE<br /><br /> 説明: OLE DB Driver for SQL Server では、[ISSDataClassification](../ole-db-interfaces/issdataclassification-ole-db.md) インターフェイスを使用した秘密度分類情報の取得がサポートされています。|  
|SSPROP_MAXBLOBLENGTH|列:いいえ<br /><br /> R/W:読み取り/書き込み<br /><br /> 型: VT_I4<br /><br /> 既定値はこのプロバイダーでは、サーバーから返されるテキストのサイズを制限しません。プロパティ値は最大値に設定されます。 たとえば、2147483647 に設定されます。<br /><br /> 説明:OLE DB Driver for SQL Server では、SET TEXTSIZE ステートメントを実行して、SELECT ステートメントで返されるバイナリ ラージ オブジェクト (BLOB) データのサイズを制限します。|  
|SSPROP_NOCOUNT_STATUS|列:NoCount<br /><br /> R/W:読み取り専用<br /><br /> 型: VT_BOOL<br /><br /> 既定値はVARIANT_FALSE<br /><br /> 説明:[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] の SET NOCOUNT ON/OFF の状態を表すブール値です。<br /><br /> VARIANT_TRUE: SET NOCOUNT ON の場合<br /><br /> VARIANT_FALSE: SET NOCOUNT OFF の場合|  
|SSPROP_QP_NOTIFICATION_MSGTEXT|列:いいえ<br /><br /> R/W:読み取り/書き込み<br /><br /> 型: VT_BSTR (許可される文字数: 1 から 2,000 文字)<br /><br /> 既定値は空の文字列<br /><br /> 説明:クエリ通知のメッセージ テキストです。 これはユーザーが定義するので、定義済みの書式はありません。|  
|SSPROP_QP_NOTIFICATION_OPTIONS|列:いいえ<br /><br /> R/W:読み取り/書き込み<br /><br /> 型: VT_BSTR<br /><br /> 既定値は空の文字列<br /><br /> 説明:クエリ通知オプション。 これらは `name=value` 形式の文字列で指定されます。 ユーザーがサービスを作成して、キューから通知を読み取る必要があります。 クエリ通知オプションの構文を次に示します。<br /><br /> `service=<service-name>[;(local database=<database>&#124;broker instance=<broker instance>)]`<br /><br /> 次に例を示します。<br /><br /> `service=mySSBService;local database=mydb`|  
|SSPROP_QP_NOTIFICATION_TIMEOUT|列:いいえ<br /><br /> R/W:読み取り/書き込み<br /><br /> 型: VT_UI4<br /><br /> 既定値は432,000 秒 (5 日)<br /><br /> 最小:1 秒<br /><br /> 最大値:2^31-1 秒<br /><br /> 説明:クエリ通知をアクティブのままにしておく秒数。|  
  
## <a name="see-also"></a>参照  
 [行セット](../../oledb/ole-db-rowsets/rowsets.md)  
  
  
