---
description: SQL Server Native Client 内の大きな CLR User-Defined 型 (OLE DB)
title: 大きな CLR ユーザー定義型 (OLE DB) | Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- large CLR user-defined types [OLE DB]
ms.assetid: 4bf12058-0534-42ca-a5ba-b1c23b24d90f
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: d4d7c3b854f49a426dc5e788e1fc1ab44eb28b35
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104753232"
---
# <a name="large-clr-user-defined-types-in-sql-server-native-client-ole-db"></a>SQL Server Native Client 内の大きな CLR User-Defined 型 (OLE DB)
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  このトピックでは、大きな共通言語ランタイム (CLR) ユーザー定義型 (UDT) をサポートするための、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Native Client の OLE DB に対する変更について説明します。  
  
 Native Client での大きな CLR Udt のサポートの詳細につい [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] ては、「 [large Clr User-Defined Types](../../../relational-databases/native-client/features/large-clr-user-defined-types.md)」を参照してください。 サンプルについては、「[大きな CLR UDT の使用 &#40;OLE DB&#41;](../../../relational-databases/native-client-ole-db-how-to/use-large-clr-udts-ole-db.md)」を参照してください。  
  
## <a name="data-format"></a>データ形式  
 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Native Client では、大きなオブジェクト (LOB) の型についてサイズが無制限である値の長さを表す場合に、~0 が使用されます。 8,000 バイトを超える CLR UDT のサイズも ~0 で表されます。  
  
 次の表に、パラメーターおよび行セットでのデータ型のマッピングを示します。  
  
|SQL Server のデータ型|OLE DB データ型|メモリ レイアウト|値|  
|--------------------------|----------------------|-------------------|-----------|  
|CLR UDT|DBTYPE_UDT|BYTE[](バイト配列\)|132 (oledb.h)|  
  
 UDT 値はバイト配列として表されます。 16 進文字列との間の変換がサポートされています。 リテラル値は、"0x" で始まる 16 進文字列として表されます。 16 進文字列は、ベース 16 のバイナリ データをテキストで表現したものです。 一例として、サーバー型 **varbinary(10)** から DBTYPE_STR への変換が挙げられます。この変換では、1 対の文字が 1 バイトを表す 20 文字の 16 進表記が生成されます。  
  
## <a name="parameter-properties"></a>パラメーターのプロパティ  
 DBPROPSET_SQLSERVERPARAMETER プロパティ セットは、OLE DB を介して UDT をサポートします。 詳細については、「[ユーザー定義型の使用](~/relational-databases/native-client/features/using-user-defined-types.md)」を参照してください。  
  
## <a name="column-properties"></a>列のプロパティ  
 DBPROPSET_SQLSERVERCOLUMN プロパティ セットは、OLE DB を介してテーブルの作成をサポートします。 詳細については、「[ユーザー定義型の使用](~/relational-databases/native-client/features/using-user-defined-types.md)」を参照してください。  
  
## <a name="data-type-mapping-in-itabledefinitioncreatetable"></a>ITableDefinition::CreateTable でのデータ型マッピング  
 UDT 列が必要な場合は、ITableDefinition::CreateTable で使用される **DBCOLUMNDESC** 構造体で、以下の情報が使用されます。  
  
|OLE DB データ型 (*wType*)|*pwszTypeName*|SQL Server のデータ型|*rgPropertySets*|  
|----------------------------------|--------------------|--------------------------|----------------------|  
|DBTYPE_UDT|無視|UDT|DBPROPSET_SQLSERVERCOLUMN プロパティ セットを含める必要があります。|  
  
## <a name="icommandwithparametersgetparameterinfo"></a>ICommandWithParameters::GetParameterInfo  
 **prgParamInfo** を介して DBPARAMINFO 構造体に返される情報は、次のとおりです。  
  
|パラメーターのタイプ|*wType*|*ulParamSize*|*bPrecision*|*bScale*|*dwFlags* DBPARAMFLAGS_ISLONG|  
|--------------------|-------------|-------------------|------------------|--------------|------------------------------------|  
|DBTYPE_UDT<br /><br /> (8,000 バイト以下の長さ)|"DBTYPE_UDT"|*n*|undefined|undefined|オフ|  
|DBTYPE_UDT<br /><br /> (8,000 バイトを超える長さ)|"DBTYPE_UDT"|~0|undefined|undefined|set|  
  
## <a name="icommandwithparameterssetparameterinfo"></a>ICommandWithParameters::SetParameterInfo  
 DBPARAMBINDINFO 構造体で提供される情報は、次の表に準拠する必要があります。  
  
|パラメーターのタイプ|*pwszDataSourceType*|*ulParamSize*|*bPrecision*|*bScale*|*dwFlags* DBPARAMFLAGS_ISLONG|  
|--------------------|--------------------------|-------------------|------------------|--------------|------------------------------------|  
|DBTYPE_UDT<br /><br /> (8,000 バイト以下の長さ)|DBTYPE_UDT|*n*|無視|無視|パラメーターが DBTYPE_IUNKNOWN を使用して渡される場合に設定する必要があります。|  
|DBTYPE_UDT<br /><br /> (8,000 バイトを超える長さ)|DBTYPE_UDT|~0|無視|無視|無視|  
  
## <a name="isscommandwithparameters"></a>ISSCommandWithParameters  
 アプリケーションでは、**ISSCommandWithParameters** を使用して、「パラメーターのプロパティ」セクションで定義されているパラメーターのプロパティを取得または設定します。  
  
## <a name="icolumnsrowsetgetcolumnsrowset"></a>IColumnsRowset::GetColumnsRowset  
 返される列は次のとおりです。  
  
|列の型|DBCOLUMN_TYPE|DBCOLUMN_COLUMNSIZE|DBCOLUMN_PRECISION|DBCOLUMN_SCALE|DBCOLUMN_FLAGS_ISLONG|DBCOLUMNS_ISSEARCHABLE|DBCOLUMN_OCTETLENGTH|  
|-----------------|--------------------|--------------------------|-------------------------|---------------------|-----------------------------|-----------------------------|---------------------------|  
|DBTYPE_UDT<br /><br /> (8,000 バイト以下の長さ)|DBTYPE_UDT|*n*|NULL|NULL|Clear|DB_ALL_EXCEPT_LIKE|n|  
|DBTYPE_UDT<br /><br /> (8,000 バイトを超える長さ)|DBTYPE_UDT|~0|NULL|NULL|オン|DB_ALL_EXCEPT_LIKE|0|  
  
 UDT には次の列も定義されています。  
  
|列識別子|種類|説明|  
|-----------------------|----------|-----------------|  
|DBCOLUMN_UDT_CATALOGNAME|DBTYPE_WSTR|UDT 列の場合は、UDT が定義されているカタログの名前。|  
|DBCOLUMN_UDT_SCHEMANAME|DBTYPE_WSTR|UDT 列の場合は、UDT が定義されているスキーマの名前。|  
|DBCOLUMN_UDT_NAME|DBTYPE_WSTR|UDT 列の場合は、UDT の 1 部構成の名前。|  
|DBCOLUMN_ASSEMBLY_TYPENAME|DBTYPE_WSTR|UDT 列の場合は、UDT の完全な型名。 アセンブリ型の完全修飾名を使用することで、Type.GetType メソッドを使用してその型のオブジェクトのインスタンスを作成できます。|  
  
## <a name="icolumnsinfogetcolumninfo"></a>IColumnsInfo::GetColumnInfo  
 DBCOLUMNINFO 構造体で返される情報は次のとおりです。  
  
|パラメーターのタイプ|*wType*|*ulColumnSize*|*bPrecision*|*bScale*|*dwFlags*<br /><br /> DBCOLUMNFLAGS_ISLONG|  
|--------------------|-------------|--------------------|------------------|--------------|-----------------------------------------|  
|DBTYPE_UDT<br /><br /> (8,000 バイト以下の長さ)|DBTYPE_UDT|*n*|~0|~0|Clear|  
|DBTYPE_UDT<br /><br /> (8,000 バイトを超える長さ)|DBTYPE_UDT|~0|~0|~0|オン|  
  
## <a name="columns-rowset-schema-rowsets"></a>COLUMNS 行セット (スキーマ行セット)  
 UDT 型に対して返される列値を次に示します。  
  
|列の型|DATA_TYPE|COLUMN_FLAGS、DBCOLUMFLAGS_ISLONG|CHARACTER_OCTET_LENGTH|  
|-----------------|----------------|-----------------------------------------|------------------------------|  
|DBTYPE_UDT<br /><br /> (8,000 バイト以下の長さ)|DBTYPE_UDT|Clear|*n*|  
|DBTYPE_UDT<br /><br /> (8,000 バイトを超える長さ)|DBTYPE_UDT|オン|0|  
  
 UDT には、次の追加の列が定義されています。  
  
|列識別子|種類|説明|  
|-----------------------|----------|-----------------|  
|SS_UDT_CATALOGNAME|DBTYPE_WSTR|UDT 列の場合は、UDT が定義されているカタログの名前。|  
|SS_UDT_SCHEMANAME|DBTYPE_WSTR|UDT 列の場合は、UDT が定義されているスキーマの名前。|  
|SS_UDT_NAME|DBTYPE_WSTR|UDT 列の場合は、UDT の 1 部構成の名前。|  
|SS_ASSEMBLY_TYPENAME|DBTYPE_WSTR|UDT 列の場合は、UDT の完全な型名。 アセンブリ型の完全修飾名を使用することで、Type.GetType メソッドを使用してその型のオブジェクトのインスタンスを作成できます。|  
  
 PROCEDURE_PARAMETERS 行セットに関しては、DATA_TYPE に COLUMNS スキーマ行セットと同じ値が格納され、TYPE_NAME に UDT が格納されます。 同じ追加の列も定義されています。  
  
 PROVIDER_TYPES スキーマ行セットには、ユーザー定義型が表示されません。  
  
## <a name="bindings-and-conversions"></a>バインドと変換  
  
|Binding データ型|UDT からサーバー|UDT 以外からサーバー|サーバーから UDT|サーバーから UDT 以外|  
|----------------------|-------------------|------------------------|---------------------|--------------------------|  
|DBTYPE_UDT|サポート (5)|エラー (1)|サポート (5)|エラー (4)|  
|DBTYPE_BYTES|サポート (5)|該当なし|サポート (5)|該当なし|  
|DBTYPE_WSTR|サポート (2)、(5)|該当なし|サポート (3)、(5)、(6)|該当なし|  
|DBTYPE_BSTR|サポート (2)、(5)|該当なし|サポート (3)、(5)|該当なし|  
|DBTYPE_STR|サポート (2)、(5)|該当なし|サポート (3)、(5)|該当なし|  
|DBTYPE_IUNKNOWN|サポート (6)|該当なし|サポート (6)|該当なし|  
|DBTYPE_VARIANT (VT_UI1 &#124; VT_ARRAY)|サポート (5)|該当なし|サポート (3)、(5)|該当なし|  
|DBTYPE_VARIANT (VT_BSTR)|サポート (2)、(5)|該当なし|該当なし|該当なし|  
  
### <a name="key-to-symbols"></a>記号の説明  
  
|Symbol|意味|  
|------------|-------------|  
|1|**ICommandWithParameters::SetParameterInfo** で DBTYPE_UDT 以外のサーバーの型が指定され、アクセサーの型が DBTYPE_UDT の場合は、ステートメントの実行時にエラーが発生します。  発生するエラーは DB_E_ERRORSOCCURRED、パラメーターの状態は DBSTATUS_E_BADACCESSOR です。<br /><br /> UDT ではないサーバー パラメーターに UDT 型のパラメーターを指定すると、エラーになります。|  
|2|データが 16 進数文字列からバイナリ データに変換されます。|  
|3|データがバイナリ データから 16 進文字列に変換されます。|  
|4|**CreateAccessor** または **GetNextRows** を使用したときに、検証が行われる場合があります。 このエラーは DB_E_ERRORSOCCURRED です。 バインドの状態は、DBBINDSTATUS_UNSUPPORTEDCONVERSION に設定されます。|  
|5|BY_REF を使用できます。|  
|6|UDT パラメーターを、DBBINDING で DBTYPE_IUNKNOWN としてバインドできます。 DBTYPE_IUNKNOWN へのバインドは、アプリケーションで ISequentialStream インターフェイスを使用してデータをストリームとして処理する必要があることを示します。 コンシューマーが DBTYPE_IUNKNOWN 型としてバインドで *Wtype* を指定し、ストアドプロシージャの対応する列または出力パラメーターが UDT の場合、SQL Server Native Client は ISequentialStream を返します。 入力パラメーターの場合、SQL Server Native Client は ISequentialStream インターフェイスのを照会します。<br /><br /> UDT が大きい場合は、DBTYPE_IUNKNOWN バインドを使用しながら UDT データの長さをバインドしないようにすることができます。 ただし、小さな UDT の場合は長さをバインドする必要があります。 次の条件が 1 つ以上当てはまる場合は、DBTYPE_UDT パラメーターを大きな UDT として指定できます。<br />*ulParamParamSize* は ~0 です。<br />DBPARAMBINDINFO 構造体で DBPARAMFLAGS_ISLONG が設定されている。<br /><br /> 行データの場合は、DBTYPE_IUNKNOWN バインドを大きな UDT だけに使用できます。 列が大きな UDT 型であるかどうかを確認するには、行セットまたはコマンド オブジェクトの IColumnsInfo インターフェイス上で IColumnsInfo::GetColumnInfo メソッドを使用します。 次の条件が 1 つ以上当てはまる場合、DBTYPE_UDT 列は大きな UDT 列です。<br />DBCOLUMNINFO 構造体の *dwFlags* メンバーで、DBCOLUMNFLAGS_ISLONG フラグが設定されている。 <br />DBCOLUMNINFO の *ulColumnSize* メンバーは ~0 です。|  
  
 DBTYPE_NULL と DBTYPE_EMPTY は入力パラメーターにバインドできますが、出力パラメーターや結果にはバインドできません。 入力パラメーターにバインドした場合は、状態を DBSTATUS_S_ISNULL (DBTYPE_NULL の場合) または DBSTATUS_S_DEFAULT (DBTYPE_EMPTY の場合) に設定する必要があります。 DBTYPE_BYREF を、DBTYPE_NULL または DBTYPE_EMPTY と共に使用することはできません。  
  
 DBTYPE_UDT は、DBTYPE_EMPTY および DBTYPE_NULL に変換することもできます。 ただし、DBTYPE_NULL および DBTYPE_EMPTY を DBTYPE_UDT に変換することはできません。 この動作は、DBTYPE_BYTES 型と一貫性があります。 UDT をパラメーターとして処理する場合には、**ISSCommandWithParameters** が使用されます。  
  
 OLE DB Core Services で提供されるデータ変換 (**IDataConvert**) は、DBTYPE_UDT 型には適用できません。  
  
 また、その他のバインドもサポートされません。  
  
## <a name="comparability-for-irowsetfind"></a>IRowsetFind での比較  
 UDT 型については、次の比較のみがサポートされています。  
  
-   EQ  
  
-   NE  
  
-   IGNORE  
  
 その他の比較を試みると、DB_E_BADCOMPAREOP が返されます。  
  
## <a name="bcp-support-for-udts"></a>BCP による UDT のサポート  
 UDT 値は、文字値またはバイナリ値としてのみインポートおよびエクスポートできます。  
  
## <a name="down-level-client-behavior-for-udts"></a>UDT に対する下位クライアントの動作  
 UDT に対しては、下位クライアントで次のように型マッピングが行われます。  
  
|クライアントのバージョン|DBTYPE_UDT<br /><br /> (8,000 バイト以下の長さ)|DBTYPE_UDT<br /><br /> (8,000 バイトを超える長さ)|  
|--------------------|------------------------------------------------------------------|---------------------------------------------------------|  
|SQL Server 2005|UDT|varbinary(max)|  
|SQL Server 2008 以降|UDT|UDT|  
  
 **DataTypeCompatibility** (SSPROP_INIT_DATATYPECOMPATIBILITY) を "80" に設定すると、大きな UDT 型が、下位クライアントで表示されるときと同じようにクライアントで表示されます。  
  
## <a name="see-also"></a>参照  
 [大きな CLR ユーザー定義型](~/relational-databases/native-client/features/large-clr-user-defined-types.md)  
  
  

