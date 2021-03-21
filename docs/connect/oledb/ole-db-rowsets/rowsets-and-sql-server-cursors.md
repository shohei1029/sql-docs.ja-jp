---
title: 行セットと SQL Server カーソル (OLE DB ドライバー)
description: SQL Server からは結果セットがコンシューマーに返され、既定の結果セットは OLE DB Driver for SQL Server のサーバー カーソルと異なります。そのしくみについて説明します。
ms.custom: ''
ms.date: 06/14/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: connectivity
ms.topic: reference
helpviewer_keywords:
- OLE DB rowsets, cursors
- OLE DB Driver for SQL Server, rowsets
- rowsets [OLE DB], cursors
- properties [OLE DB]
- cursors [OLE DB]
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: 6f7b5fa8d0dabcdb5d6355bc5b34764f4ce84edd
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104755322"
---
# <a name="rowsets-and-sql-server-cursors"></a>行セットと SQL Server カーソル
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[Driver_OLEDB_Download](../../../includes/driver_oledb_download.md)]

  [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] では、2 種類の方法でコンシューマーに結果セットが返されます。  
  
-   既定の結果セット。これには次の特徴があります。  
  
    -   オーバーヘッドを最小限に抑えます。  
  
    -   最高のパフォーマンスでデータをフェッチします。  
  
    -   既定の順方向専用かつ読み取り専用のカーソル機能だけをサポートします。  
  
    -   一度に 1 行をコンシューマーに返します。  
  
    -   1 つの接続でサポートされるアクティブ ステートメントは一度に 1 つだけです。  
  
         ステートメントの実行後は、コンシューマーがすべての結果を取得するまで、またはステートメントが取り消されるまで、その接続で別のステートメントを実行することはできません。  
  
    -   すべての [!INCLUDE[tsql](../../../includes/tsql-md.md)] ステートメントをサポートします。  
  
-   サーバー カーソル。これには、次の特徴があります。  
  
    -   すべてのカーソル機能をサポートします。  
  
    -   コンシューマーに行のブロックを返すことができます。  
  
    -   1 つの接続で複数のアクティブ ステートメントをサポートします。  
  
    -   カーソル機能とパフォーマンスのバランスが取れています。  
  
         カーソル機能をサポートすると、既定の結果セットに比べてパフォーマンスが低下する場合があります。 コンシューマーはカーソル機能を使用して比較的小さな行のセットを取得することで、この欠点を補うことができます。  
  
    -   複数の結果セットを返す [!INCLUDE[tsql](../../../includes/tsql-md.md)] ステートメントはサポートしません。  
  
 コンシューマーは、特定の行セット プロパティを設定することで、行セットでのカーソルの動作を変更できます。 コンシューマーでそれらの行セット プロパティがまったく設定されないか、またはすべてのプロパティが既定値に設定された場合、OLE DB Driver for SQL Server では既定の結果セットを使用して行セットが実装されます。 プロパティのいずれか 1 つでも既定値以外の値に設定されると、OLE DB Driver for SQL Server では、サーバー カーソルを使用して行セットが実装されます。  
  
 次の行セット プロパティによって、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] カーソルを使用することが OLE DB Driver for SQL Server に指示されます。 プロパティによっては、他のプロパティと組み合わせて使用できるものもあります。 たとえば、DBPROP_IRowsetScroll プロパティと DBPROP_IRowsetChange プロパティを備えた行セットは、即時更新動作を表すブックマーク行セットになります。 同時に指定できないプロパティもあります。 たとえば、DBPROP_OTHERINSERT を備えた行セットにはブックマークを含めることはできません。  
  
|プロパティ ID|値|行セットの動作|  
|-----------------|-----------|---------------------|  
|DBPROP_SERVERCURSOR|VARIANT_TRUE|行セットを使用して [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] データを更新できません。 行セットは順次処理され、順方向のスクロールとフェッチのみがサポートされます。 相対行位置指定がサポートされます。 コマンド テキストに ORDER BY 句を指定できます。|  
|DBPROP_CANSCROLLBACKWARDS または DBPROP_CANFETCHBACKWARDS|VARIANT_TRUE|行セットを使用して [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] データを更新できません。 行セットでは、両方向へのスクロールとフェッチがサポートされます。 相対行位置指定がサポートされます。 コマンド テキストに ORDER BY 句を指定できます。|  
|DBPROP_BOOKMARKS または DBPROP_LITERALBOOKMARKS|VARIANT_TRUE|行セットを使用して [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] データを更新できません。 行セットは順次処理され、順方向のスクロールとフェッチのみがサポートされます。 相対行位置指定がサポートされます。 コマンド テキストに ORDER BY 句を指定できます。|  
|DBPROP_OWNUPDATEDELETE、DBPROP_OWNINSERT、または DBPROP_OTHERUPDATEDELETE|VARIANT_TRUE|行セットを使用して [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] データを更新できません。 行セットでは、両方向へのスクロールとフェッチがサポートされます。 相対行位置指定がサポートされます。 コマンド テキストに ORDER BY 句を指定できます。|  
|DBPROP_OTHERINSERT|VARIANT_TRUE|行セットを使用して [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] データを更新できません。 行セットでは、両方向へのスクロールとフェッチがサポートされます。 相対行位置指定がサポートされます。 参照先の列にインデックスが存在する場合は、コマンド テキストに ORDER BY 句を指定できます。<br /><br /> 行セットにブックマークが含まれる場合は、DBPROP_OTHERINSERT に VARIANT_TRUE を指定できません。 この表示設定プロパティとブックマークを指定して行セットを作成しようとすると、エラーが発生します。|  
|DBPROP_IRowsetLocate または DBPROP_IRowsetScroll|VARIANT_TRUE|行セットを使用して [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] データを更新できません。 行セットでは、両方向へのスクロールとフェッチがサポートされます。 **IRowsetLocate** インターフェイスを使用したブックマークと絶対位置指定が、行セットでサポートされます。 コマンド テキストに ORDER BY 句を指定できます。<br /><br /> DBPROP_IRowsetLocate と DBPROP_IRowsetScroll は、行セット内にブックマークを要求します。 ブックマークを含み、DBPROP_OTHERINSERT に VARIANT_TRUE を設定した行セットを作成しようとすると、エラーが発生します。|  
|DBPROP_IRowsetChange または DBPROP_IRowsetUpdate|VARIANT_TRUE|行セットを使用して [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] データを更新できます。 行セットは順次処理され、順方向のスクロールとフェッチのみがサポートされます。 相対行位置指定がサポートされます。 更新可能なカーソルをサポートするすべてのコマンドでは、これらのインターフェイスがサポートされます。|  
|DBPROP_IRowsetLocate または DBPROP_IRowsetScroll、および DBPROP_IRowsetChange または DBPROP_IRowsetUpdate|VARIANT_TRUE|行セットを使用して [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] データを更新できます。 行セットでは、両方向へのスクロールとフェッチがサポートされます。 **IRowsetLocate** を使用したブックマークと絶対位置指定が、行セットでサポートされます。 コマンド テキストに ORDER BY 句を指定できます。|  
|DBPROP_IMMOBILEROWS|VARIANT_FALSE|行セットを使用して [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] データを更新できません。 行セットでは、順方向のスクロールしかサポートされません。 相対行位置指定がサポートされます。 参照先の列にインデックスが存在する場合は、コマンド テキストに ORDER BY 句を指定できます。<br /><br /> DBPROP_IMMOBILEROWS は、別のセッションのコマンドや別のユーザーから挿入された [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 行を表示できる行セットだけで使用できます。 DBPROP_OTHERINSERT に VARIANT_TRUE を設定できない行セットで、プロパティに VARIANT_FALSE が設定されている行セットを開こうとすると、エラーが発生します。|  
|DBPROP_REMOVEDELETED|VARIANT_TRUE|行セットを使用して [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] データを更新できません。 行セットでは、順方向のスクロールしかサポートされません。 相対行位置指定がサポートされます。 別のプロパティで制約されている場合を除いて、コマンド テキストに ORDER BY 句を指定できます。|  
  
 サーバー カーソルでサポートされる OLE DB Driver for SQL Server 行セットは、**IOpenRowset::OpenRowset** メソッドを使用して、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] ベース テーブルまたはビュー上に簡単に作成できます。 テーブルやビューを名前で指定し、必要な行セット プロパティのセットを *rgPropertySets* パラメーターで渡します。  
  
 コンシューマーが、サーバー カーソルでサポートされる行セットを要求すると、行セットを作成するコマンド テキストが制限されます。 具体的には、1 つの行セットを結果として返す 1 つの SELECT ステートメント、または 1 つの行セットを結果として返す 1 つの SELECT ステートメントを実装するストアド プロシージャに制限されます。  
  
 次の 2 つの表に、各種 OLE DB プロパティとカーソル モデルのマッピングを示します。 また、特定の種類のカーソル モデルを使用するために設定する必要がある行セット プロパティも示します。  
  
 表の各欄には、特定のカーソル モデル向けの行セット プロパティ値を示しています。 このトピックの前半で示した行セット プロパティのデータ型はすべて VT_BOOL で、既定値は VARIANT_FALSE です。 表では、次の記号を使用しています。  
  
 F = 既定値 (VARIANT_FALSE)  
  
 T = VARIANT_TRUE  
  
 \- = VARIANT_TRUE または VARIANT_FALSE  
  
 特定の種類のカーソル モデルを使用するには、カーソル モデルに対応する列を探し、列の値が "T" であるすべての行セット プロパティを見つけます。 これらの行セット プロパティに VARIANT_TRUE を設定すると、その特定のカーソル モデルを使用できます。 値が "-" の行セット プロパティには、VARIANT_TRUE と VARIANT_FALSE のどちらも設定できます。  
  
|行セット プロパティ/カーソル モデル|Default<br /><br /> 結果<br /><br /> set<br /><br /> (RO)|速い<br /><br /> 順方向<br /><br /> のみ<br /><br /> (RO)|静的<br /><br /> (RO)|Keyset<br /><br /> ドリブン<br /><br /> (RO)|  
|--------------------------------------|-------------------------------------------|--------------------------------------------|-----------------------|----------------------------------|  
|DBPROP_SERVERCURSOR|F|T|T|T|  
|DBPROP_DEFERRED|F|F|-|-|  
|DBPROP_IrowsetChange|F|F|F|F|  
|DBPROP_IrowsetLocate|F|F|-|-|  
|DBPROP_IrowsetScroll|F|F|-|-|  
|DBPROP_IrowsetUpdate|F|F|F|F|  
|DBPROP_BOOKMARKS|F|F|-|-|  
|DBPROP_CANFETCHBACKWARDS|F|F|-|-|  
|DBPROP_CANSRCOLLBACKWARDS|F|F|-|-|  
|DBPROP_CANHOLDROWS|F|F|-|-|  
|DBPROP_LITERALBOOKMARKS|F|F|-|-|  
|DBPROP_OTHERINSERT|F|T|F|F|  
|DBPROP_OTHERUPDATEDELETE|F|T|F|T|  
|DBPROP_OWNINSERT|F|T|F|T|  
|DBPROP_OWNUPDATEDELETE|F|T|F|T|  
|DBPROP_QUICKSTART|F|F|-|-|  
|DBPROP_REMOVEDELETED|F|F|F|-|  
|DBPROP_IrowsetResynch|F|F|F|-|  
|DBPROP_CHANGEINSERTEDROWS|F|F|F|F|  
|DBPROP_SERVERDATAONINSERT|F|F|F|-|  
|DBPROP_UNIQUEROWS|-|F|F|F|  
|DBPROP_IMMOBILEROWS|-|-|-|T|  
  
|行セット プロパティ/カーソル モデル|動的 (RO)|キー セット (R/W)|動的 (R/W)|  
|--------------------------------------|--------------------|---------------------|----------------------|  
|DBPROP_SERVERCURSOR|T|T|T|  
|DBPROP_DEFERRED|-|-|-|  
|DBPROP_IrowsetChange|F|-|-|  
|DBPROP_IrowsetLocate|F|-|F|  
|DBPROP_IrowsetScroll|F|-|F|  
|DBPROP_IrowsetUpdate|F|-|-|  
|DBPROP_BOOKMARKS|F|-|F|  
|DBPROP_CANFETCHBACKWARDS|-|-|-|  
|DBPROP_CANSRCOLLBACKWARDS|-|-|-|  
|DBPROP_CANHOLDROWS|F|-|F|  
|DBPROP_LITERALBOOKMARKS|F|-|F|  
|DBPROP_OTHERINSERT|T|F|T|  
|DBPROP_OTHERUPDATEDELETE|T|T|T|  
|DBPROP_OWNINSERT|T|T|T|  
|DBPROP_OWNUPDATEDELETE|T|T|T|  
|DBPROP_QUICKSTART|-|-|-|  
|DBPROP_REMOVEDELETED|T|-|T|  
|DBPROP_IrowsetResynch|-|-|-|  
|DBPROP_CHANGEINSERTEDROWS|F|-|F|  
|DBPROP_SERVERDATAONINSERT|F|-|F|  
|DBPROP_UNIQUEROWS|F|F|F|  
|DBPROP_IMMOBILEROWS|F|T|F|  
  
 行セット プロパティの特定のセットに対して選択されるカーソル モデルは、次のようにして決まります。  
  
 行セット プロパティの指定されたコレクションから、上の表に示したプロパティのサブセットを取得します。 これらのプロパティを、各行セット プロパティのフラグ値 (必須 (T、F) または省略可能 (-)) に応じて、2 つのサブグループに分けます。 カーソル モデルごとに、最初のテーブルから開始し、左から右へ移動します。 2 つのサブグループ内のプロパティの値を、その列の対応するプロパティの値と比較します。 必須プロパティに不一致がなく、かつオプション プロパティの不一致が最も少ないカーソル モデルが選択されます。 複数のカーソル モデルが該当する場合は、一番左側のモデルが選択されます。  
  
## <a name="sql-server-cursor-block-size"></a>SQL Server カーソル ブロックのサイズ  
 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] カーソルで OLE DB Driver for SQL Server 行セットがサポートされる場合、**IRowset::GetNextRows** メソッドまたは **IRowsetLocate::GetRowsAt** メソッドの行ハンドル配列パラメーター内の要素数によって、カーソル ブロックのサイズが決まります。 配列内のハンドルで指定される行が、カーソル ブロックのメンバーです。  
  
 ブックマークをサポートしている行セットの場合、**IRowsetLocate::GetRowsByBookmark** メソッドを使用して取得される行ハンドルにより、カーソル ブロックのメンバーが定義されます。  
  
 行セットの作成や [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] カーソル ブロックの形成にどのメソッドを使用したかにかかわらず、行セットの次の行をフェッチするメソッドが実行されるまで、カーソル ブロックはアクティブです。  
  
## <a name="see-also"></a>参照  
 [行セット](../../oledb/ole-db-rowsets/rowsets.md)  
  
  
