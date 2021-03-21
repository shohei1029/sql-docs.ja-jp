---
title: 複数のアクティブな結果セット (MARS) の使用 | Microsoft Docs
description: 複数のアクティブな結果セットのサポートによってインメモリ OLTP および OLE DB Driver に対して SQL Server でのアプリケーション デザインが簡素化される方法について説明します。
ms.custom: ''
ms.date: 06/12/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: connectivity
ms.topic: reference
helpviewer_keywords:
- OLE DB Driver for SQL Server, MARS
- MSOLEDBSQL, MARS
- data access [OLE DB Driver for SQL Server], MARS
- Multiple Active Result Sets
- OLE DB Driver for SQL Server, MARS
- MARS [SQL Server]
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: 73f0e06f497f4d95c6d3e82ade44596b55d29bf4
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104751132"
---
# <a name="using-multiple-active-result-sets-mars"></a>複数のアクティブな結果セット (MARS) の使用
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[Driver_OLEDB_Download](../../../includes/driver_oledb_download.md)]

  [!INCLUDE[ssVersion2005](../../../includes/ssversion2005-md.md)] では、[!INCLUDE[ssDE](../../../includes/ssde-md.md)] にアクセスするアプリケーションで複数のアクティブな結果セット (MARS) がサポートされるようになりました。 以前のバージョンの [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] では、データベース アプリケーションは 1 つの接続で複数のアクティブなステートメントを保持できませんでした。 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] の既定の結果セットを使用しているときは、アプリケーションはその接続で他のバッチを実行する前に、1 つのバッチのすべての結果セットを処理するか、取り消す必要がありました。 [!INCLUDE[ssVersion2005](../../../includes/ssversion2005-md.md)] では新しい接続属性が導入され、アプリケーションは接続ごとに複数の要求を保留中にできるだけでなく、接続ごとに複数のアクティブな既定の結果セットを保持できるようになりました。  
  
 MARS によって、次の新しい機能を使用したアプリケーションのデザインが簡素化されます。  
  
-   アプリケーションは、既定の結果セットを複数開くことができ、複数の既定の結果セットからの読み取りをインターリーブすることができます。  
  
-   アプリケーションは既定の結果セットを開いたまま、他のステートメント (INSERT、UPDATE、DELETE、ストアド プロシージャ呼び出しなど) を実行できます。  
  
 MARS を使用するアプリケーションについては、次のガイドラインを参照してください。  
  
-   1 つの SQL ステートメント (SELECT、OUTPUT を伴う DML、RECEIVE、READ TEXT など) で生成される結果セットが短期間しか有効でない場合や結果セットが小さい場合は、既定の結果セットを使用します。  
  
-   1 つの SQL ステートメントで生成される結果セットが長期間有効な場合や結果セットが大きくなる場合は、サーバー カーソルを使用します。  
  
-   結果を返すかどうかにかかわらず手続き型の要求の場合、および複数の結果を返すバッチの場合、常に、結果の最後まで読み取ります。  
  
-   接続プロパティの変更やトランザクションの管理には、できるだけ [!INCLUDE[tsql](../../../includes/tsql-md.md)] ステートメントではなく API 呼び出しを優先して使用します。  
  
-   MARS では、複数のバッチが同時に実行されている間、セッション スコープの権限の借用が禁止されます。  
  
> [!NOTE]  
>  既定では、MARS 機能は有効になっていません。 OLE DB Driver for SQL Server で [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] に接続するときに MARS を使用するには、接続文字列で MARS を明示的に有効にする必要があります。 詳細については、このトピックで後述する「OLE DB Driver for SQL Server」セクションを参照してください。  
  
 OLE DB Driver for SQL Server では、接続でアクティブ ステートメントの数は制限されません。  
  
 複数のステートメントで構成される単一のバッチやストアド プロシージャを複数同時実行する必要のない一般的なアプリケーションでは、MARS の実装方法を理解しなくても、MARS の利点を得ることができます。 ただし、より複雑な要件のアプリケーションでは、MARS の実装方法を考慮する必要があります。  
  
 MARS では、1 つの接続内で複数の要求の実行をインターリーブできます。 つまり、1 つのバッチを実行し、その実行内で他の要求を実行できます。 ただし、MARS では並列実行が行われるのではなく、複数の実行がインターリーブされることに注意してください。  
  
 MARS のインフラストラクチャでは、複数のバッチがインターリーブ方式で実行されますが、実行の切り替えは明確に定義された時点でしか行われません。 また、ほとんどのステートメントはバッチ内で自動的に実行される必要があります。 クライアントに行を返すステートメント ("*呼び出しポイント*" と呼ばれることもあります) では、行をクライアントへ送信しているとき、その送信を完了する前に実行をインターリーブすることができます。次に例を示します。  
  
-   SELECT  
  
-   FETCH  
  
-   RECEIVE  
  
 ストアド プロシージャまたはバッチの一部として実行されるその他のステートメントはいずれも、他の MARS 要求に切り替えられる前に実行を完了する必要があります。  
  
 バッチがどのようにインターリーブ実行されるかは、さまざまな要因の影響を受けます。そのため、呼び出しポイントを含む複数のバッチからのコマンドが実行されるときに、正確なシーケンスを予測することは困難です。 このような複雑なバッチをインターリーブ実行したときに、好ましくない影響が発生しないように注意してください。  
  
 このような問題を回避するために、接続状態 (SET、USE) やトランザクション (BEGIN TRAN、COMMIT、ROLLBACK) を管理する場合は、[!INCLUDE[tsql](../../../includes/tsql-md.md)] ステートメントではなく API 呼び出しを使用します。このとき、複数のステートメントで構成されるバッチに呼び出しポイントも含まれている場合、接続状態やトランザクションを管理するステートメントを含めないようにします。さらに、このようなバッチでは、すべての結果を処理するか、残りを取り消すことによって、実行を順番に行います。  
  
> [!NOTE]  
>  MARS が有効なときにトランザクションを手動または暗黙に開始するバッチやストアド プロシージャでは、バッチが終了する前にトランザクションを完了する必要があります。 トランザクションを完了しないと、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] はバッチの終了時にトランザクションによって行われたすべての変更をロールバックします。 このようなトランザクションは、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] によってバッチスコープのトランザクションとして管理されます。 これは [!INCLUDE[ssVersion2005](../../../includes/ssversion2005-md.md)] で導入された新しい形式のトランザクションで、MARS が有効なときに、既存の適切に動作するストアド プロシージャを使用できるようになります。 バッチスコープのトランザクションの詳細については、[トランザクション ステートメント &#40;Transact-SQL&#41;](../../../t-sql/statements/statements.md) に関する記事を参照してください。  
  
 ADO から MARS を使用する例については、「[OLE DB Driver for SQL Server での ADO の使用](../../oledb/applications/using-ado-with-oledb-driver-for-sql-server.md)」を参照してください。  
  
## <a name="in-memory-oltp"></a>インメモリ OLTP  
 インメモリ OLTP では、クエリとネイティブ コンパイル ストアド プロシージャを使用した MARS がサポートされています。 MARS を使用すると、新しい結果セットから行をフェッチする要求を送信する前に、各結果セットを完全に取得しなくても、複数のクエリからデータを要求することができます。 複数の開いている結果セットを正常に読み取るには、MARS が有効になっている接続を使用する必要があります。  
  
 MARS は既定で無効になっているため、接続文字列に `MultipleActiveResultSets=True` を追加して明示的に有効にする必要があります。 次の例は、SQL Server のインスタンスに接続し、MARS が有効になっていることを指定する方法を示しています。  
  
```  
Data Source=MSSQL; Initial Catalog=AdventureWorks; Integrated Security=SSPI; MultipleActiveResultSets=True  
```  
  
 インメモリ OLTP を使用した MARS は、SQL エンジンの残りの部分では基本的に MARS と同じです。 メモリ最適化テーブルとネイティブ コンパイル ストアド プロシージャで MARS を使用した場合の違いを次のリストに示します。  
  
 **MARS とメモリ最適化テーブル**  
  
 MARS が有効になっている接続を使用する場合の、ディスク ベース テーブルとメモリ最適化テーブルの違いは次のとおりです。  
  
-   2 つのステートメントは同じターゲット オブジェクト内のデータを変更できますが、両方が同じレコードを変更しようとすると、書き込み競合によって新しい操作が失敗します。 ただし、両方の操作で異なるレコードを変更する場合は、操作は正常に行われます。  
  
-   各ステートメントはスナップショット分離で実行されるため、新しい操作では既存のステートメントによって行われた変更が認識できません。 同時実行ステートメントが同じトランザクションで実行される場合でも、SQL エンジンによって相互に分離されたステートメントごとにバッチスコープのトランザクションが作成されます。 ただし、バッチスコープのトランザクションは引き続き結合されているため、1 つのバッチスコープのトランザクションのロールバックが、同じバッチ内の他のトランザクションに影響を及ぼします。  
  
-   DDL 操作はユーザー トランザクションでは許可されないため、すぐに失敗します。  
  
 **MARS およびネイティブ コンパイル ストアド プロシージャ**  
  
 ネイティブ コンパイル ストアド プロシージャは、MARS が有効になっている接続で実行でき、呼び出しポイントが検出された場合にのみ、別のステートメントに実行を譲ることができます。 呼び出しポイントには SELECT ステートメントが必要です。これは、ネイティブ コンパイル ストアド プロシージャ内で別のステートメントに実行を譲ることができる唯一のステートメントです。 SELECT ステートメントがプロシージャ内に存在しない場合は、他のステートメントに譲らず、それが始まる前に、完了するまで実行されます。  
  
 **MARS とインメモリ OLTP のトランザクション**  
  
 インターリーブされたステートメントおよび ATOMIC ブロックによって行われた変更は、相互に分離されます。 たとえば、1 つのステートメントまたは ATOMIC ブロックでいくつかの変更を行ってから、別のステートメントに実行を譲ると、新しいステートメントでは、最初のステートメントによって行われた変更が認識されません。 また、最初のステートメントでは、実行の再開時に、他のステートメントによって行われた変更が認識されません。 ステートメントでは、ステートメントが開始される前に完了してコミットされた変更のみが認識されます。  
  
 BEGIN TRANSACTION ステートメントを使用して、現在のユーザー トランザクション内で新しいユーザー トランザクションを開始できます。これは相互運用モードでのみサポートされているため、BEGIN TRANSACTION は T-SQL ステートメントからしか呼び出すことができません。ネイティブ コンパイル ストアド プロシージャからは呼び出せません。 SAVE TRANSACTION を使用して、トランザクションにセーブ ポイントを作成できます。または transaction.Save(save_point_name) への API 呼び出しを使用してセーブ ポイントにロールバックできます。 この機能も、T-SQL ステートメントからのみ有効にできます。ネイティブ コンパイル ストアド プロシージャ内からは有効にできません。  
  
 **MARS と列ストア インデックス**  
  
 SQL Server (2016 以降) では、列ストア インデックスを持つ MARS がサポートされています。 SQL Server 2014 では、列ストア インデックスに読み取り専用で接続するために MARS が使用されます。    ただし、SQL Server 2014 では、列ストア インデックスを含むテーブルで DML (データ操作言語) を同時操作するとき、MARS を利用できません。 その場合、SQL Server は接続を強制終了し、トランザクションを中止します。   SQL Server 2012 には読み取り専用の列ストア インデックスがあり、それらには MARS は適用されません。  
  
## <a name="ole-db-driver-for-sql-server"></a>OLE DB Driver for SQL Server  
 OLE DB Driver for SQL Server では、DBPROPSET_SQLSERVERDBINIT プロパティ セットに実装される SSPROP_INIT_MARSCONNECTION データ ソース初期化プロパティの追加によって、MARS がサポートされます。 また、新しい接続文字列のキーワードとして **MarsConn** が追加されました。 **true** または **false** を値として受け取ります。既定値は **false** です。  
  
 データ ソース プロパティ DBPROP_MULTIPLECONNECTIONS の既定値は VARIANT_TRUE です。 これは、複数の同時実行コマンドや行セット オブジェクトをサポートするために、プロバイダーが複数の接続を起動することを意味しています。 MARS が有効なとき、OLE DB Driver for SQL Server では 1 つの接続で複数のコマンドや行セット オブジェクトをサポートできるので、MULTIPLE_CONNECTIONS が既定で VARIANT_FALSE に設定されます。  
  
 DBPROPSET_SQLSERVERDBINIT プロパティ セットに行われた機能強化の詳細については、「[初期化プロパティと承認プロパティ](../../oledb/ole-db-data-source-objects/initialization-and-authorization-properties.md)」を参照してください。  
  
### <a name="ole-db-driver-for-sql-server-example"></a>OLE DB Driver for SQL Server の例  
 次の例では、セッション オブジェクトを作成する前に、OLE DB Driver for SQL Server を使用してデータ ソース オブジェクトを作成し、DBPROPSET_SQLSERVERDBINIT プロパティ セットを使用して MARS を有効にしています。  
  
```  
#include <msoledbsql.h>  
  
IDBInitialize *pIDBInitialize = NULL;  
IDBCreateSession *pIDBCreateSession = NULL;  
IDBProperties *pIDBProperties = NULL;  
  
// Create the data source object.  
hr = CoCreateInstance(CLSID_MSOLEDBSQL, NULL,  
   CLSCTX_INPROC_SERVER,  
   IID_IDBInitialize,   
    (void**)&pIDBInitialize);  
  
hr = pIDBInitialize->QueryInterface(IID_IDBProperties, (void**)&pIDBProperties);  
  
// Set the MARS property.  
DBPROP rgPropMARS;  
  
// The following is necessary since MARS is off by default.  
rgPropMARS.dwPropertyID = SSPROP_INIT_MARSCONNECTION;  
rgPropMARS.dwOptions = DBPROPOPTIONS_REQUIRED;  
rgPropMARS.dwStatus = DBPROPSTATUS_OK;  
rgPropMARS.colid = DB_NULLID;  
V_VT(&(rgPropMARS.vValue)) = VT_BOOL;  
V_BOOL(&(rgPropMARS.vValue)) = VARIANT_TRUE;  
  
// Create the structure containing the properties.  
DBPROPSET PropSet;  
PropSet.rgProperties = &rgPropMARS;  
PropSet.cProperties = 1;  
PropSet.guidPropertySet = DBPROPSET_SQLSERVERDBINIT;  
  
// Get an IDBProperties pointer and set the initialization properties.  
pIDBProperties->SetProperties(1, &PropSet);  
pIDBProperties->Release();  
  
// Initialize the data source object.  
hr = pIDBInitialize->Initialize();  
  
//Create a session object from a data source object.  
IOpenRowset * pIOpenRowset = NULL;  
hr = IDBInitialize->QueryInterface(IID_IDBCreateSession, (void**)&pIDBCreateSession));  
hr = pIDBCreateSession->CreateSession(  
   NULL,             // pUnkOuter  
   IID_IOpenRowset,  // riid  
  &pIOpenRowset ));  // ppSession  
  
// Create a rowset with a firehose mode cursor.  
IRowset *pIRowset = NULL;  
DBPROP rgRowsetProperties[2];  
  
// To get a firehose mode cursor request a   
// forward only read only rowset.  
rgRowsetProperties[0].dwPropertyID = DBPROP_IRowsetLocate;  
rgRowsetProperties[0].dwOptions = DBPROPOPTIONS_REQUIRED;  
rgRowsetProperties[0].dwStatus = DBPROPSTATUS_OK;  
rgRowsetProperties[0].colid = DB_NULLID;  
VariantInit(&(rgRowsetProperties[0].vValue));  
rgRowsetProperties[0].vValue.vt = VARIANT_BOOL;  
rgRowsetProperties[0].vValue.boolVal = VARIANT_FALSE;  
  
rgRowsetProperties[1].dwPropertyID = DBPROP_IRowsetChange;  
rgRowsetProperties[1].dwOptions = DBPROPOPTIONS_REQUIRED;  
rgRowsetProperties[1].dwStatus = DBPROPSTATUS_OK;  
rgRowsetProperties[1].colid = DB_NULLID;  
VariantInit(&(rgRowsetProperties[1].vValue));  
rgRowsetProperties[1].vValue.vt = VARIANT_BOOL;  
rgRowsetProperties[1].vValue.boolVal = VARIANT_FALSE;  
  
DBPROPSET rgRowsetPropSet[1];  
rgRowsetPropSet[0].rgProperties = rgRowsetProperties  
rgRowsetPropSet[0].cProperties = 2  
rgRowsetPropSet[0].guidPropertySet = DBPROPSET_ROWSET;  
  
hr = pIOpenRowset->OpenRowset (NULL,  
   &TableID,  
   NULL,  
   IID_IRowset,  
   1,  
   rgRowsetPropSet  
   (IUnknown**)&pIRowset);  
```  

  
## <a name="see-also"></a>参照  
 [OLE DB Driver for SQL Server の機能](../../oledb/features/oledb-driver-for-sql-server-features.md)   
 
  
  
