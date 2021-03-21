---
title: XML データ型の使用 | Microsoft Docs
description: OLE DB Driver for SQL Server での XML データ型について説明します。このデータ型を使用すると、XML ドキュメントや XML フラグメントを SQL Server データベースに格納できます。
ms.custom: ''
ms.date: 06/12/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: connectivity
ms.topic: reference
helpviewer_keywords:
- IRowsetChange interface
- IRowsetUpdate interface
- data access [OLE DB Driver for SQL Server], xml data type
- OLE DB Driver for SQL Server schema rowsets
- PROVIDER_TYPES rowset
- IColumnsInfo interface
- IRowsetFind interface
- IColumnsRowset interface
- PROCEDURE_PARAMETERS rowset
- MSOLEDBSQL, XML
- xml data type [SQL Server], OLE DB Driver for SQL Server
- OLE DB Driver for SQL Server, XML
- IRowset interface
- ISequentialStream interface
- ISSCommandWithParameters interface
- SS_XMLSCHEMA rowset
- OLE DB Driver for SQL Server OLE DB interfaces
- XML [SQL Server], OLE DB Driver for SQL Server
- COLUMNS rowset
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: 087429db4c42793799eb1abb7a08ab4267b1afa0
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104742432"
---
# <a name="using-xml-data-types"></a>XML データ型の使用
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[Driver_OLEDB_Download](../../../includes/driver_oledb_download.md)]

  [!INCLUDE[ssVersion2005](../../../includes/ssversion2005-md.md)] には **xml** データ型が導入されています。このデータ型を使用すると XML ドキュメントや XML フラグメントを [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] データベースに格納できます。 **xml** データ型は [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] での組み込みデータ型の 1 つであり、**int** や **varchar** などの他の組み込みデータ型といくつかの点で似ています。 **xml** データ型は、他の組み込みデータ型と同様に、テーブル作成時の列の型、変数の型、パラメーターの型、または関数の戻り値の型として使用したり、CAST 関数や CONVERT 関数でも使用できます。  
  
## <a name="programming-considerations"></a>プログラミングの考慮事項  
 XML は自己記述型で、必要に応じて、次のようにドキュメントのエンコードを指定する XML ヘッダーを含めることができます。  
  
 `<?xml version="1.0" encoding="windows-1252"?><doc/>`  
  
 XML 標準では、ドキュメントの先頭の数バイトを調べることで、ドキュメントで使用しているエンコードを XML プロセッサで検出する方法が説明されています。 アプリケーションで指定するエンコードとドキュメントで指定されているエンコードが競合する場合があります。 バインドされたパラメーターとして渡されるドキュメントの場合、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] では XML がバイナリ データとして扱われるので、データ変換は行われず、XML パーサーはドキュメントに指定されたエンコードを問題なく使用することができます。 ただし、WSTR としてバインドされた XML データの場合、そのドキュメントが Unicode でエンコードされていることをアプリケーションで確認する必要があります。 このシナリオでは、必然的にドキュメントが DOM に読み込まれることになり、エンコードが Unicode に変更され、ドキュメントがシリアル化される場合があります。 この手順が行われないと、データ変換が行われ、結果として無効なまたは破損した XML になります。  
  
 また、XML がリテラルで指定された場合も競合が発生する可能性があります。 たとえば、次に示すステートメントは無効です。  
  
 `INSERT INTO xmltable(xmlcol) VALUES('<?xml version="1.0" encoding="UTF-16"?><doc/>')`  
  
 `INSERT INTO xmltable(xmlcol) VALUES(N'<?xml version="1.0" encoding="UTF-8"?><doc/>')`  
  
## <a name="ole-db-driver-for-sql-server"></a>OLE DB Driver for SQL Server 
 DBTYPE_XML は、OLE DB Driver for SQL Server の XML 固有の新しいデータ型です。 また、既存の OLE DB 型である DBTYPE_BYTES、DBTYPE_WSTR、DBTYPE_BSTR、DBTYPE_XML、DBTYPE_STR、DBTYPE_VARIANT、および DBTYPE_IUNKNOWN を使用して、XML データにアクセスできます。 XML 型の列に格納されたデータ型は、OLE DB Driver for SQL Server の行セット内の列から、次の形式で取得できます。  
  
-   テキスト文字列  
  
-   **ISequentialStream**  
  
> [!NOTE]  
>  OLE DB Driver for SQL Server には SAX リーダーは含まれていませんが、MSXML を使用して **ISequentialStream** を簡単に SAX オブジェクトや DOM オブジェクトに渡せます。  
  
 **ISequentialStream** は、大きな XML ドキュメントを取得するために使用します。 他の大きな値の型で使用される方法と同じ方法を XML でも使用できます。 詳細については、「[大きな値をとるデータ型の使用](../../oledb/features/using-large-value-types.md)」を参照してください。  
  
 行セットの XML 型の列に格納されているデータは、アプリケーションで **IRow::GetColumns**、**IRowChange::SetColumns**、**ICommand::Execute** などの通常のインターフェイスを使用することにより、取得、挿入、または更新することもできます。 取得する場合と同様に、アプリケーション プログラムでテキスト文字列または **ISequentialStream** を OLE DB Driver for SQL Server に渡すことができます。  
  
> [!NOTE]  
>  **ISequentialStream** インターフェイスを使用して XML データを文字列形式で送信するには、DBTYPE_IUNKNOWN を指定して **ISequentialStream** を取得し、その *pObject* 引数をバインドの際に NULL に設定する必要があります。  
  
 コンシューマーのバッファーが小さすぎて取得した XML データが切り捨てられるときは、データ長が 0xffffffff で返されます。この値は、長さが不明であることを意味します。 この動作は、実際のデータの前に長さの情報を送信しないでクライアントにストリーム送信されるデータ型の実装と一貫性を持たせています。 **IRowset::GetData** のようにプロバイダーが値全体をバッファーに読み込み、データ変換を行うときは、実際の長さを返すことができます。  
  
 SQL Server に送信される XML データは、サーバーではバイナリ データとして扱われます。 この動作により、変換が行われなくなり、XML パーサーが XML エンコードを自動検出できるようになります。 これにより、広範な XML ドキュメント (UTF-8 でエンコードされた XML ドキュメントなど) を [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] への入力として受け取ることができます。  
  
 入力 XML が DBTYPE_WSTR にバインドされる場合、不要なデータ変換によってデータが破損しないようにするために、アプリケーションでは入力 XML が Unicode でエンコードされていることを確認する必要があります。  
  
### <a name="data-bindings-and-coercions"></a>データ バインドと強制型変換  
 次の表に、特定のデータ型を [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] の **xml** データ型と共に使用した場合に行われるバインドおよび強制型変換を示します。  
  
|データ型|SQL Server の<br /><br /> **XML**|SQL Server の<br /><br /> **XML 以外から**|サーバーからの<br /><br /> **XML**|サーバーからの<br /><br /> **XML 以外から**|  
|---------------|---------------------------|--------------------------------|-----------------------------|----------------------------------|  
|DBTYPE_XML|パス スルー<sup>6、7</sup>|エラー<sup>1</sup>|OK<sup>11、6</sup>|エラー<sup>8</sup>|  
|DBTYPE_BYTES|パス スルー<sup>6、7</sup>|N/A<sup>2</sup>|可 <sup>11、6</sup>|該当なし <sup>2</sup>|  
|DBTYPE_WSTR|パス スルー<sup>6、10</sup>|該当なし <sup>2</sup>|OK<sup>4、6、12</sup>|該当なし <sup>2</sup>|  
|DBTYPE_BSTR|パス スルー<sup>6、10</sup>|該当なし <sup>2</sup>|可 <sup>3</sup>|該当なし <sup>2</sup>|  
|DBTYPE_STR|OK<sup>6、9、10</sup>|該当なし <sup>2</sup>|可<sup>5、6、12</sup>|該当なし <sup>2</sup>|  
|DBTYPE_IUNKNOWN|**ISequentialStream** 経由のバイト ストリーム <sup>7</sup>|該当なし <sup>2</sup>|**ISequentialStream** 経由のバイト ストリーム <sup>11</sup>|該当なし <sup>2</sup>|  
|DBTYPE_VARIANT (VT_UI1 &#124; VT_ARRAY)|パス スルー<sup>6、7</sup>|該当なし <sup>2</sup>|該当なし|該当なし <sup>2</sup>|  
|DBTYPE_VARIANT (VT_BSTR)|パス スルー<sup>6、10</sup>|該当なし <sup>2</sup>|可<sup>3</sup>|該当なし <sup>2</sup>|  
  
 <sup>1</sup>DBTYPE_XML 以外のサーバー型が **ICommandWithParameters::SetParameterInfo** で指定され、アクセサー型が DBTYPE_XML の場合、ステートメントの実行時にエラーが発生します (DB_E_ERRORSOCCURRED、パラメーターの状態は DBSTATUS_E_BADACCESSOR)。それ以外の場合は、データがサーバーに送信されますが、XML 型からパラメーターのデータ型への暗黙の変換が行われないことを示すエラーがサーバーから返されます。  
  
 <sup>2</sup>この記事での説明の対象外です。  
  
 <sup>3</sup>形式は UTF-16 です。バイト順マーク (BOM)、エンコード仕様、および終端を示す NULL は付加されません。  
  
 <sup>4</sup>形式は UTF-16 です。BOM とエンコード仕様は付加されず、NULL で終端が示されます。  
  
 <sup>5</sup>クライアントのコード ページでエンコードされ、NULL で終端が示される、マルチバイト文字列形式です。 Unicode を使用できるサーバーから変換するとデータが破損する場合があるので、このバインドはお勧めしません。  
  
 <sup>6</sup>BY_REF を使用できます。  
  
 <sup>7</sup>UTF-16 データは BOM で始まる必要があります。 BOM で始まらない場合は、サーバーでエンコードが正しく認識されないことがあります。  
  
 <sup>8</sup>検証は、アクセサーの作成時、またはフェッチ時に行われます。 エラーは DB_E_ERRORSOCCURRED です。その場合、バインドの状態は DBBINDSTATUS_UNSUPPORTEDCONVERSION に設定されます。  
  
 <sup>9</sup>データは、サーバーに送信される前にクライアントのコード ページを使用して Unicode に変換されます。 ドキュメントのエンコードがクライアントのコード ページと一致しない場合、データが破損することがあるので、このバインドはお勧めしません。  
  
 <sup>10</sup>サーバーに送信されるデータには必ず BOM が追加されます。 既にデータが BOM で始まる場合、バッファーの先頭に 2 つの BOM が存在することになります。 サーバーでは、1 つ目の BOM からエンコードを UTF-16 と認識した後、その BOM を破棄します。 2 つ目の BOM は、幅が 0 の改行を行わない空白文字として解釈されます。  
  
 <sup>11</sup>形式は UTF-16 です。エンコード仕様は付加されません。サーバーから受け取ったデータに BOM が追加されます。 サーバーから空文字列が返されても、アプリケーションには BOM が返されます。 バッファー長が奇数バイトの場合、データは適切に切り捨てられます。 1 つの値が複数のチャンクで返される場合、それぞれを連結して正しい値を再構成できます。  
  
 <sup>12</sup>バッファー長が 2 文字より少ない場合、つまり、領域が不足して NULL で終端が示されない場合、オーバーフロー エラーが報告されます。  
  
> [!NOTE]  
>  NULL の XML 値の場合は、データは返されません。  
  
 XML 標準では、UTF-16 でエンコードされた XML をバイト順マーク (BOM) で始める必要があります。BOM は UTF-16 の文字コードでは 0xFEFF です。 WSTR バインドおよび BSTR バインドを使用する場合、そのバインドによってエンコードが暗黙的に決まるので、OLE DB Driver for SQL Server から BOM が要求されることも追加されることもありません。 BYTES バインド、XML バインド、または IUNKNOWN バインドを使用する場合、その目的は他の XML プロセッサや XML ストレージ システムでの処理を簡単にすることです。 この場合、BOM は UTF-16 でエンコードされた XML を示し、SQL Server を含め XML プロセッサの多くは値の先頭の数バイトを調べてエンコードを推定するので、アプリケーションで実際のエンコードを考慮する必要はありません。 BYTES バインド、XML バインド、または IUNKNOWN バインドを使用して OLE DB Driver for SQL Server から受け取る XML データは、常に、BOM が付加された UTF-16 にエンコードされ、エンコード宣言は埋め込まれません。  
  
 OLE DB Core Services で提供されるデータ変換 (**IDataConvert**) は、DBTYPE_XML 型には適用できません。  
  
 検証はデータがサーバーに送信されたときに行われます。 クライアント側の検証とエンコードの変更はアプリケーションで処理する必要があります。 直接処理するのではなく、DOM リーダーや SAX リーダーを使用して XML データを処理することを強くお勧めします。  
  
 DBTYPE_NULL と DBTYPE_EMPTY は入力パラメーターにバインドできますが、出力パラメーターや結果にはバインドできません。 入力パラメーターにバインドした場合、状態を DBSTATUS_S_ISNULL または DBSTATUS_S_DEFAULT に設定する必要があります。  
  
 DBTYPE_XML は DBTYPE_EMPTY および DBTYPE_NULL に変換でき、DBTYPE_EMPTY は DBTYPE_XML に変換できますが、DBTYPE_NULL を DBTYPE_XML に変換することはできません。 この動作は、DBTYPE_WSTR と一貫性があります。  
  
 DBTYPE_IUNKNOWN は (上記の表に示したように) サポート済みのバインドですが、DBTYPE_XML と DBTYPE_IUNKNOWN との間では変換は行われません。 DBTYPE_IUNKNOWN は、DBTYPE_BYREF と共に使用することはできません。  
  
### <a name="ole-db-rowset-additions-and-changes"></a>OLE DB 行セットに関する追加事項と変更事項  
 OLE DB Driver for SQL Server では、多くの主要な OLE DB スキーマ行セットに新しい値が追加され、変更が加えられました。  
  
#### <a name="the-columns-and-procedure_parameters-schema-rowsets"></a>COLUMNS スキーマ行セットと PARAMETERS スキーマ行セット  
 COLUMNS スキーマ行セットと PROCEDURE_PARAMETERS スキーマ行セットに次の列が追加されました。  
  
|列名|Type|説明|  
|-----------------|----------|-----------------|  
|SS_XML_SCHEMACOLLECTION_CATALOGNAME|DBTYPE_WSTR|XML スキーマ コレクションを定義しているカタログの名前。 XML 型以外の列または型指定されていない XML 列の場合は NULL です。|  
|SS_XML_SCHEMACOLLECTION_SCHEMANAME|DBTYPE_WSTR|XML スキーマ コレクションを定義しているスキーマの名前。 XML 型以外の列または型指定されていない XML 列の場合は NULL です。|  
|SS_XML_SCHEMACOLLECTIONNAME|DBTYPE_WSTR|XML スキーマ コレクションの名前。 XML 型以外の列または型指定されていない XML 列の場合は NULL です。|  
  
#### <a name="the-provider_types-schema-rowset"></a>PROVIDER_TYPES スキーマ行セット  
 PROVIDER_TYPES スキーマ行では、**xml** データ型の COLUMN_SIZE の値は 0 で、DATA_TYPE は DBTYPE_XML です。  
  
#### <a name="the-ss_xmlschema-schema-rowset"></a>SS_XMLSCHEMA スキーマ行セット  
 クライアントで XML スキーマ情報を取得できるように、新しいスキーマ行セット SS_XMLSCHEMA が導入されました。 SS_XMLSCHEMA 行セットには、次の列が含まれています。  
  
|列名|Type|説明|  
|-----------------|----------|-----------------|  
|SCHEMACOLLECTION_CATALOGNAME|DBTYPE_WSTR|XML コレクションが属するカタログ。|  
|SCHEMACOLLECTION_SCHEMANAME|DBTYPE_WSTR|XML コレクションが属するスキーマ。|  
|SCHEMACOLLECTIONNAME|DBTYPE_WSTR|型指定された XML 列の場合は、XML スキーマ コレクションの名前です。それ以外の場合は、NULL です。|  
|TARGETNAMESPACEURI|DBTYPE_WSTR|XML スキーマの対象名前空間。|  
|SCHEMACONTENT|DBTYPE_WSTR|XML スキーマのコンテンツ。|  
  
 各 XML スキーマのスコープは、カタログ名、スキーマ名、スキーマ コレクション名、および対象名前空間を識別する Uniform Resource Identifier (URI) 別に設定されます。 また、DBSCHEMA_XML_COLLECTIONS という名前の新しい GUID も定義されます。 SS_XMLSCHEMA スキーマ行セットの制限数と制限列は、次のように定義されます。  
  
|GUID|制限数|制限列|  
|----------|----------------------------|------------------------|  
|DBSCHEMA_XML_COLLECTIONS|4|SCHEMACOLLECTION_CATALOGNAME<br /><br /> SCHEMACOLLECTION_SCHEMANAME<br /><br /> SCHEMACOLLECTIONNAME<br /><br /> TARGETNAMESPACEURI|  
  
### <a name="ole-db-property-set-additions-and-changes"></a>OLE DB プロパティ セットに関する追加事項と変更事項  
 OLE DB Driver for SQL Server では、多くの主要な OLE DB プロパティ セットに新しい値が追加され、変更が加えられました。  
  
#### <a name="the-dbpropset_sqlserverparameter-property-set"></a>DBPROPSET_SQLSERVERPARAMETER プロパティ セット  
 OLE DB で **xml** データ型をサポートするため、OLE DB Driver for SQL Server では、次の値を含む新しい DBPROPSET_SQLSERVERPARAMETER プロパティ セットが実装されました。  
  
|Name|Type|説明|  
|----------|----------|-----------------|  
|SSPROP_PARAM_XML_SCHEMACOLLECTION_CATALOGNAME|DBTYPE_WSTR|XML スキーマ コレクションを定義しているカタログ (データベース) の名前。 SQL の 3 部構成による名前の識別子の一部になります。|  
|SSPROP_PARAM_XML_SCHEMACOLLECTION_SCHEMANAME|DBTYPE_WSTR|スキーマ コレクションに含まれている XML スキーマの名前。 SQL の 3 部構成による名前の識別子の一部になります。|  
|SSPROP_PARAM_XML_SCHEMACOLLECTIONNAME|DBTYPE_WSTR|カタログに含まれている XML スキーマ コレクションの名前。SQL の 3 部構成による名前の識別子の一部になります。|  
  
#### <a name="the-dbpropset_sqlservercolumn-property-set"></a>DBPROPSET_SQLSERVERCOLUMN プロパティ セット  
 **ITableDefinition** インターフェイスでのテーブルの作成をサポートするため、OLE DB Driver for SQL Server では、DBPROPSET_SQLSERVERCOLUMN プロパティ セットに次の 3 つの新しい列が追加されました。  
  
|Name|Type|説明|  
|----------|----------|-----------------|  
|SSPROP_COL_XML_SCHEMACOLLECTION_CATALOGNAME|VT_BSTR|型指定された XML 列の場合、このプロパティは XML スキーマが格納されているカタログ名を指定する文字列です。 他の列の型の場合、このプロパティでは空文字列が返されます。|  
|SSPROP_COL_XML_SCHEMACOLLECTION_SCHEMANAME|VT_BSTR|型指定された XML 列の場合、このプロパティはこの列を定義している XML スキーマ名を指定する文字列です。|  
|SSPROP_COL_XML_SCHEMACOLLECTIONNAME|VT_BSTR|型指定された XML 列の場合、このプロパティは値を定義している XML スキーマ コレクション名を指定する文字列です。|  
  
 SSPROP_PARAM 値と同様に、これらのプロパティはすべて省略可能なプロパティで、既定値は空です。 SSPROP_COL_XML_SCHEMACOLLECTION_CATALOGNAME と SSPROP_COL_XML_SCHEMACOLLECTION_SCHEMANAME は、SSPROP_COL_XML_SCHEMACOLLECTIONNAME が指定されている場合のみ指定できます。 XML をサーバーに渡すときにこれらの値が含まれていると、これらの値が妥当かどうかが現在のデータベースとの比較によりチェックされ、インスタンス データがスキーマとの比較によりチェックされます。 どの場合でも、これらはすべて空かすべて設定されている場合に妥当と判断されます。  
  
### <a name="ole-db-interface-additions-and-changes"></a>OLE DB インターフェイスに関する追加事項と変更事項  
 OLE DB Driver for SQL Server では、多くの主要な OLE DB インターフェイスに新しい値が追加され、変更が加えられました。  
  
#### <a name="the-isscommandwithparameters-interface"></a>ISSCommandWithParameters インターフェイス  
 OLE DB で **xml** データ型をサポートするため、OLE DB Driver for SQL Server では、[ISSCommandWithParameters](../../oledb/ole-db-interfaces/isscommandwithparameters-ole-db.md) インターフェイスの追加など、多くの変更が加えられました。 この新しいインターフェイスは、主要な OLE DB インターフェイス **ICommandWithParameters** から継承されます。 **ICommandWithParameters** から継承される 3 つのメソッド (**GetParameterInfo**、**MapParameterNames**、および **SetParameterInfo**) に加えて、**ISSCommandWithParameters** では、サーバー固有のデータ型を処理するために使用される [GetParameterProperties](../../oledb/ole-db-interfaces/isscommandwithparameters-getparameterproperties-ole-db.md) メソッドと [SetParameterProperties](../../oledb/ole-db-interfaces/isscommandwithparameters-setparameterproperties-ole-db.md) メソッドが提供されます。  
  
> [!NOTE]  
>  **ISSCommandWithParameters** インターフェイスでは、新しい SSPARAMPROPS 構造体も使用されます。  
  
#### <a name="the-icolumnsrowset-interface"></a>IColumnsRowset インターフェイス  
 OLE DB Driver for SQL Server では、次の [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 固有の列が **IColumnRowset::GetColumnsRowset** メソッドで返される行セットに追加されました。 これらの列には、XML スキーマ コレクションの 3 部構成の名前が含まれます。 XML 以外の列または型指定されていない XML 列の場合、これら 3 列の既定値はすべて NULL になります。  
  
|列名|Type|説明|  
|-----------------|----------|-----------------|  
|DBCOLUMN_SS_XML_SCHEMACOLLECTION_CATALOGNAME|DBTYPE_WSTR|XML スキーマ コレクションが属するカタログ。<br /><br /> それ以外の場合は、NULL です。|  
|DBCOLUMN_SS_XML_SCHEMACOLLECTION_SCHEMANAME|DBTYPE_WSTR|XML スキーマ コレクションが属するスキーマ。 それ以外の場合は、NULL です。|  
|DBCOLUMN_SS_XML_SCHEMACOLLECTIONNAME|DBTYPE_WSTR|型指定された XML 列の場合は、XML スキーマ コレクションの名前です。それ以外の場合は、NULL です。|  
  
#### <a name="the-irowset-interface"></a>IRowset インターフェイス  
 XML 列内の XML インスタンスは **IRowset::GetData** メソッドを使用して取得されます。 クライアントで指定されているバインドによって異なりますが、XML インスタンスは DBTYPE_BSTR、DBTYPE_WSTR、DBTYPE_VARIANT、DBTYPE_XML、DBTYPE_STR、DBTYPE_BYTES として、または DBTYPE_IUNKNOWN 経由のインターフェイスとして取得できます。 コンシューマーが DBTYPE_BSTR、DBTYPE_WSTR、または DBTYPE_VARIANT を指定すると、プロバイダーで XML インスタンスの型がユーザーが要求した型に変換され、対応するバインドで指定した場所に格納されます。  
  
 コンシューマーが DBTYPE_IUNKNOWN を指定し、*pObject* 引数に NULL を設定した場合、または *pObject* 引数に IID_ISequentialStream を設定した場合、プロバイダーから **ISequentialStream** インターフェイスが返されるので、列の XML データをストリーム出力できます。 その後、**ISequentialStream** から XML データが Unicode 文字のストリームとして返されます。  
  
 プロバイダーは DBTYPE_IUNKNOWN にバインドされた XML 値を返すときに、`sizeof (IUnknown *)` でサイズ値を報告します。 この動作は、列が DBTYPE_IUnknown または DBTYPE_IDISPATCH としてバインドされる場合に行われる動作、および正確な列のサイズを特定できない場合に DBTYPE_IUNKNOWN や ISequentialStream で行われる動作と同じです。  
  
#### <a name="the-irowsetchange-interface"></a>IRowsetChange インターフェイス  
 コンシューマーは、列の XML インスタンスを 2 とおりの方法で更新できます。 1 つ目の方法では、プロバイダーで作成されるストレージ オブジェクト **ISequentialStream** を使用します。 コンシューマーは **ISequentialStream::Write** メソッドを呼び出して、プロバイダーから返された XML インスタンスを直接更新できます。  
  
 2 つ目の方法では、**IRowsetChange::SetData** メソッドまたは **IRowsetChange::InsertRow** メソッドを使用します。 この方法では、コンシューマーのバッファーにある XML インスタンスを DBTYPE_BSTR、DBTYPE_WSTR、DBTYPE_VARIANT、DBTYPE_XML、または DBTYPE_IUNKNOWN 型のバインドで指定できます。  
  
 DBTYPE_BSTR、DBTYPE_WSTR、または DBTYPE_VARIANT が指定されている場合、コンシューマーのバッファーに存在する XML インスタンスがプロバイダーによって適切な列に格納されます。  
  
 DBTYPE_IUNKNOWN と ISequentialStream が指定されており、コンシューマーがどのストレージ オブジェクトも指定しない場合、**ISequentialStream** オブジェクトをあらかじめ作成して XML ドキュメントをこのオブジェクトにバインドし、このオブジェクトを **IRowsetChange::SetData** メソッドを使用してプロバイダーに渡す必要があります。 また、ストレージ オブジェクトを作成して pObject 引数に IID_ISequentialStream を設定し、**ISequentialStream** オブジェクトを作成してから、その **ISequentialStream** オブジェクトを **IRowsetChange::SetData** メソッドに渡すこともできます。 どちらの場合も、プロバイダーは **ISequentialStream** オブジェクトを使用して XML オブジェクトを取得し、それを適切な列に挿入できます。  
  
#### <a name="the-irowsetupdate-interface"></a>IRowsetUpdate インターフェイス  
 **IRowsetUpdate** インターフェイスには遅延更新のための機能が用意されています。 行セットで使用できるデータは、コンシューマーが **IRowsetUpdate::Update** メソッドを呼び出すまで他のトランザクションで使用できません。  
  
#### <a name="the-irowsetfind-interface"></a>IRowsetFind インターフェイス  
 **IRowsetFind::FindNextRow** メソッドでは、**xml** データ型を処理できません。 *hAccessor* 引数に DBTYPE_XML の列を指定して **IRowsetFind::FindNextRow** を呼び出すと、DB_E_BADBINDINFO が返されます。 この動作は、検索対象の列の型とは無関係に行われます。 その他のバインドの型では、検索対象の列が **xml** データ型の場合、**FindNextRow** が DB_E_BADCOMPAREOP を返して失敗します。  
 
  
## <a name="see-also"></a>参照  
 [OLE DB Driver for SQL Server の機能](../../oledb/features/oledb-driver-for-sql-server-features.md)    
 [ISSCommandWithParameters &#40;OLE DB&#41;](../../oledb/ole-db-interfaces/isscommandwithparameters-ole-db.md)  
  
  
