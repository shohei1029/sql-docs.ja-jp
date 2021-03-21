---
title: SQL Server テーブルを作成する (OLE DB ドライバー) | Microsoft Docs
description: OLE DB Driver for SQL Server の ITableDefinition::CreateTable 関数を利用するとコンシューマーが SQL Server テーブルを作成できるしくみについて説明します。
ms.custom: ''
ms.date: 06/14/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: connectivity
ms.topic: reference
helpviewer_keywords:
- tables [OLE DB]
- OLE DB Driver for SQL Server, tables
- DBCOLUMNDESC usage
- adding tables
- CreateTable function
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: cab38e1c68bc12cab536090cc074cab14d97a48e
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104754272"
---
# <a name="creating-sql-server-tables"></a>SQL Server テーブルの作成
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[Driver_OLEDB_Download](../../../includes/driver_oledb_download.md)]

  OLE DB Driver for SQL Server では、コンシューマーが [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] のテーブルを作成できる、**ITableDefinition::CreateTable** 関数が公開されます。 コンシューマーは **CreateTable** を使用して、コンシューマーが命名するパーマネント テーブル、および OLE DB Driver for SQL Server によって生成される一意の名前が付いたパーマネント テーブルや一時テーブルを作成します。  
  
 コンシューマーが **ITableDefinition::CreateTable** を呼び出すときに、DBPROP_TBL_TEMPTABLE プロパティの値が VARIANT_TRUE の場合、OLE DB Driver for SQL Server によって、コンシューマー向けに一時テーブル名が生成されます。 コンシューマーは、**CreateTable** メソッドの *pTableID* パラメーターに NULL を設定します。 OLE DB Driver for SQL Server によって生成される名前が付いた一時テーブルは、**TABLES** 行セットには表示されませんが、**IOpenRowset** インターフェイス経由でアクセスできます。  
  
 コンシューマーが *pTableID* パラメーター内にある *uName* 共用体の *pwszName* メンバーにテーブル名を指定すると、OLE DB Driver for SQL Server ではその名前で [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] テーブルが作成されます。 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] テーブルの名前付けに関する制約が適用されるので、そのテーブル名で、パーマネント テーブル、ローカル一時テーブルまたはグローバル一時テーブルのいずれであるかを示すことができます。 詳細については、「[CREATE TABLE](../../../t-sql/statements/create-table-transact-sql.md)」を参照してください。 *ppTableID* パラメーターには NULL を指定できます。  
  
 OLE DB Driver for SQL Server では、パーマネント テーブル名または一時テーブル名を生成できます。 コンシューマーが *pTableID* パラメーターを NULL に設定し、有効な DBID\* を指すように *ppTableID* を設定すると、OLE DB Driver for SQL Server は、*ppTableID* の値が指す DBID の *uName* 共用体の *pwszName* メンバーに、生成したテーブル名を返します。 OLE DB Driver for SQL Server によって生成される名前が付いた一時テーブルを作成するために、コンシューマーは、*rgPropertySets* パラメーターで参照されるテーブル プロパティ セットに OLE DB テーブル プロパティ DBPROP_TBL_TEMPTABLE を含めます。 OLE DB Driver for SQL Server によって名前が付けられた一時テーブルはローカルになります。  
  
 *pTableID* パラメーターの *eKind* メンバーに DBKIND_NAME を指定しないと、**CreateTable** では DB_E_BADTABLEID が返されます。  
  
## <a name="dbcolumndesc-usage"></a>DBCOLUMNDESC の使用方法  
 コンシューマーは、*pwszTypeName* メンバーまたは *wType* メンバーのいずれかを使用して、列のデータ型を指定できます。 *pwszTypeName* でデータ型を指定すると、OLE DB Driver for SQL Server では *wType* の値が無視されます。  
  
 *pwszTypeName* メンバーを使用する場合、コンシューマーでは、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] のデータ型名を使用してデータ型が指定されます。 有効なデータ型名は、PROVIDER_TYPES スキーマの行セットの TYPE_NAME 列に返されるデータ型名です。  
  
 OLE DB Driver for SQL Server では、*wType* メンバー内の DBTYPE OLE DB 列挙値のサブセットが認識されます。 詳しくは、「[ITableDefinition でのデータ型のマッピング](../../oledb/ole-db-data-types/data-type-mapping-in-itabledefinition.md)」をご覧ください。  
  
> [!NOTE]  
>  列データ型を指定するためにコンシューマーが *pTypeInfo* または *pclsid* メンバーのいずれかを設定すると、**CreateTable** では DB_E_BADTYPE が返されます。  
  
 コンシューマーは、DBCOLUMNDESC *dbcid* メンバーの *uName* 共用体の *pwszName* メンバーに列名を指定します。 列名は Unicode 文字列として指定されます。 *dbcid* の *eKind* メンバーには、DBKIND_NAME を指定する必要があります。 *eKind* が無効、または *pwszName* が NULL、あるいは *pwszName* の値が有効な [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] ID ではない場合、**CreateTable** では DB_E_BADCOLUMNID が返されます。  
  
 列のすべてのプロパティは、テーブルに定義されたすべての列で使用できます。 プロパティ値の設定が競合していると、**CreateTable** では DB_S_ERRORSOCCURRED または DB_E_ERRORSOCCURRED が返されます。 無効な列プロパティを設定したことで [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] テーブルの作成に失敗した場合、**CreateTable** ではエラーが返されます。  
  
 DBCOLUMNDESC の列プロパティは、次のように解釈されます。  
  
|プロパティ ID|説明|  
|-----------------|-----------------|  
|DBPROP_COL_AUTOINCREMENT|R/W:読み取り/書き込み<br /><br /> 既定値はVARIANT_FALSE 説明:作成された列で ID プロパティを設定します。 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] では、ID プロパティをテーブル内の 1 つの列に設定できます。 複数の列でこのプロパティを VARIANT_TRUE に設定すると、OLE DB Driver for SQL Server がサーバーでテーブルの作成を試みた時点でエラーが発生します。<br /><br /> [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] の ID プロパティは、**integer** 型、**numeric** 型、および小数点以下桁数が 0 の **decimal** 型の場合のみ有効です。 他のデータ型の列でこのプロパティを VARIANT_TRUE に設定すると、OLE DB Driver for SQL Server がサーバーでテーブルの作成を試みた時点でエラーが発生します。<br /><br /> DBPROP_COL_AUTOINCREMENT と DBPROP_COL_NULLABLE の両方が VARIANT_TRUE で、DBPROP_COL_NULLABLE の *dwOption* が DBPROPOPTIONS_REQUIRED ではない場合、OLE DB Driver for SQL Server は DB_S_ERRORSOCCURRED を返します。 DBPROP_COL_AUTOINCREMENT と DBPROP_COL_NULLABLE の両方が VARIANT_TRUE で、DBPROP_COL_NULLABLE の *dwOption* が DBPROPOPTIONS_REQUIRED の場合、DB_E_ERRORSOCCURRED が返されます。 列は [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] の ID プロパティで定義され、DBPROP_COL_NULLABLE の *dwStatus* メンバーが DBPROPSTATUS_CONFLICTING に設定されます。|  
|DBPROP_COL_DEFAULT|R/W:読み取り/書き込み<br /><br /> 既定値はなし<br /><br /> 説明:列に対して [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] の DEFAULT 制約を作成します。<br /><br /> DBPROP の *vValue* メンバーには、さまざまなデータ型のいずれかを指定できます。 *vValue.vt* メンバーでは、列のデータ型と互換性のある型を指定する必要があります。 たとえば、DBTYPE_WSTR で定義された列の既定値として BSTR N/A を定義した場合は互換性の要件が満たされます。 DBTYPE_R8 として定義された列に同じ既定値を定義すると、OLE DB Driver for SQL Server がサーバーでテーブルの作成を試みた時点でエラーが発生します。|  
|DBPROP_COL_DESCRIPTION|R/W:読み取り/書き込み<br /><br /> 既定値はなし<br /><br /> 説明:DBPROP_COL_DESCRIPTION 列プロパティは、OLE DB Driver for SQL Server では実装されません。<br /><br /> コンシューマーがこのプロパティ値の書き込みを試みた時点で、DBPROP 構造体の *dwStatus* メンバーは DBPROPSTATUS_NOTSUPPORTED を返します。<br /><br /> このプロパティを設定しても、OLE DB Driver for SQL Server で重大なエラーが発生することはありません。 他のすべてのパラメーター値が有効であれば、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] のテーブルが作成されます。|  
|DBPROP_COL_FIXEDLENGTH|R/W:読み取り/書き込み<br /><br /> 既定値はVARIANT_FALSE<br /><br /> 説明:コンシューマーが DBCOLUMNDESC の *wType* メンバーを使用して列のデータ型を定義すると、OLE DB Driver for SQL Server では DBPROP_COL_FIXEDLENGTH を使用してデータ型のマッピングが判断されます。 詳しくは、「[ITableDefinition でのデータ型のマッピング](../../oledb/ole-db-data-types/data-type-mapping-in-itabledefinition.md)」をご覧ください。|  
|DBPROP_COL_NULLABLE|R/W:読み取り/書き込み<br /><br /> 既定値はなし<br /><br /> 説明:テーブルを作成するときに、OLE DB Driver for SQL Server では、このプロパティの設定に応じて、列が NULL 値を許容するかどうかを指定します。 このプロパティが設定されていないときは、列が値として NULL を許容するかどうかは、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] の既定のデータベース オプション ANSI_NULLS によって決まります。<br /><br /> OLE DB Driver for SQL Server は ISO 準拠のプロバイダーです。 接続されたセッションでは、ISO 準拠の動作が行われます。 DBPROP_COL_NULLABLE を設定しないと、列は NULL 値を許容します。|  
|DBPROP_COL_PRIMARYKEY|R/W:読み取り/書き込み<br /><br /> 既定値はVARIANT_FALSE 説明:VARIANT_TRUE の場合、OLE DB Driver for SQL Server は PRIMARY KEY 制約を設定して列を作成します。<br /><br /> 列プロパティとして定義するときは、1 つの列だけが制約を判断できます。 複数の列でこのプロパティを VARIANT_TRUE に設定すると、OLE DB Driver for SQL Server が [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] のテーブルの作成を試みた時点でエラーが返されます。<br /><br /> 注:コンシューマーは **IIndexDefinition::CreateIndex** を使用して、2 つ以上の列に PRIMARY KEY 制約を作成できます。<br /><br /> DBPROP_COL_PRIMARYKEY と DBPROP_COL_UNIQUE の両方が VARIANT_TRUE で、DBPROP_COL_UNIQUE の *dwOption* が DBPROPOPTIONS_REQUIRED ではない場合、OLE DB Driver for SQL Server は DB_S_ERRORSOCCURRED を返します。<br /><br /> DBPROP_COL_PRIMARYKEY と DBPROP_COL_UNIQUE の両方が VARIANT_TRUE で、DBPROP_COL_UNIQUE の *dwOption* が DBPROPOPTIONS_REQUIRED の場合、DB_E_ERRORSOCCURRED が返されます。 列は [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] の ID プロパティで定義され、DBPROP_COL_PRIMARYKEY の *dwStatus* メンバーが DBPROPSTATUS_CONFLICTING に設定されます。<br /><br /> DBPROP_COL_PRIMARYKEY と DBPROP_COL_NULLABLE の両方が VARIANT_TRUE の場合、OLE DB Driver for SQL Server はエラーを返します。<br /><br /> コンシューマーが無効な [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] のデータ型の列に PRIMARY KEY 制約を作成しようとすると、OLE DB Driver for SQL Server は [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] からのエラーを返します。 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] の **bit**、**text**、**ntext**、および **image** データ型で作成した列には、PRIMARY KEY 制約を定義できません。|  
|DBPROP_COL_UNIQUE|R/W:読み取り/書き込み<br /><br /> 既定値はVARIANT_FALSE 説明:[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] UNIQUE 制約を列に適用します。<br /><br /> 列プロパティとして定義するときは、1 つの列だけに制約が適用されます。 コンシューマーは **IIndexDefinition::CreateIndex** を使用して、2 つ以上の列の組み合わせ値に UNIQUE 制約を適用できます。<br /><br /> DBPROP_COL_PRIMARYKEY と DBPROP_COL_UNIQUE の両方が VARIANT_TRUE で、*dwOption* が DBPROPOPTIONS_REQUIRED ではない場合、OLE DB Driver for SQL Server は DB_S_ERRORSOCCURRED を返します。<br /><br /> DBPROP_COL_PRIMARYKEY と DBPROP_COL_UNIQUE の両方が VARIANT_TRUE で、*dwOption* が DBPROPOPTIONS_REQUIRED の場合、DB_E_ERRORSOCCURRED が返されます。 列は [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] の ID プロパティで定義され、DBPROP_COL_PRIMARYKEY の *dwStatus* メンバーが DBPROPSTATUS_CONFLICTING に設定されます。<br /><br /> DBPROP_COL_NULLABLE と DBPROP_COL_UNIQUE の両方が VARIANT_TRUE で、*dwOption* が DBPROPOPTIONS_REQUIRED ではない場合、OLE DB Driver for SQL Server は DB_S_ERRORSOCCURRED を返します。<br /><br /> DBPROP_COL_NULLABLE と DBPROP_COL_UNIQUE の両方が VARIANT_TRUE で、*dwOption* が DBPROPOPTIONS_REQUIRED の場合、DB_E_ERRORSOCCURRED が返されます。 列は [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] の ID プロパティで定義され、DBPROP_COL_NULLABLE の *dwStatus* メンバーが DBPROPSTATUS_CONFLICTING に設定されます。<br /><br /> コンシューマーが無効な [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] のデータ型の列に UNIQUE 制約を作成しようとすると、OLE DB Driver for SQL Server は [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] からのエラーを返します。 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] の **bit** データ型で作成された列には、UNIQUE 制約を定義できません。|  
  
 コンシューマーが **ITableDefinition::CreateTable** を呼び出すときに、OLE DB Driver for SQL Server では、テーブル プロパティが次のように解釈されます。  
  
|プロパティ ID|説明|  
|-----------------|-----------------|  
|DBPROP_TBL_TEMPTABLE|R/W:読み取り/書き込み<br /><br /> 既定値はVARIANT_FALSE 説明:OLE DB Driver for SQL Server では既定で、コンシューマー別の名前が付くテーブルが作成されます。 VARIANT_TRUE を指定すると、OLE DB Driver for SQL Server によりコンシューマー向けに一時テーブル名が生成されます。 コンシューマーは **CreateTable** の *pTableID* パラメーターに NULL を設定します。 *ppTableID* パラメーターには、有効なポインターを含める必要があります。|  
  
 正常に作成されたテーブルでコンシューマーが行セットを開くように要求すると、OLE DB Driver for SQL Server は、カーソルがサポートされる行セットを開きます。 渡されるプロパティ セットで任意の行セット プロパティを指定できます。  
  
 次の例では、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] テーブルを作成します。  
  
```  
// This CREATE TABLE statement shows the details of the table created by   
// the following example code.  
//  
// CREATE TABLE OrderDetails  
// (  
//    OrderID      int      NOT NULL  
//    ProductID   int      NOT NULL  
//    CONSTRAINT PK_OrderDetails  
//         PRIMARY KEY CLUSTERED (OrderID, ProductID),  
//    UnitPrice   money      NOT NULL,  
//    Quantity   int      NOT NULL,  
//    Discount   decimal(2,2)   NOT NULL  
//        DEFAULT 0  
// )  
//  
// The PRIMARY KEY constraint is created in an additional example.  
HRESULT CreateTable  
    (  
    ITableDefinition* pITableDefinition  
    )  
    {  
    DBID            dbidTable;  
    const ULONG     nCols = 5;  
    ULONG           nCol;  
    ULONG           nProp;  
    DBCOLUMNDESC    dbcoldesc[nCols];  
  
    HRESULT         hr;  
  
    // Set up column descriptions. First, set default property values for  
    //  the columns.  
    for (nCol = 0; nCol < nCols; nCol++)  
        {  
        dbcoldesc[nCol].pwszTypeName = NULL;  
        dbcoldesc[nCol].pTypeInfo = NULL;  
        dbcoldesc[nCol].rgPropertySets = new DBPROPSET;  
        dbcoldesc[nCol].pclsid = NULL;  
        dbcoldesc[nCol].cPropertySets = 1;  
        dbcoldesc[nCol].ulColumnSize = 0;  
        dbcoldesc[nCol].dbcid.eKind = DBKIND_NAME;  
        dbcoldesc[nCol].wType = DBTYPE_I4;  
        dbcoldesc[nCol].bPrecision = 0;  
        dbcoldesc[nCol].bScale = 0;  
  
        dbcoldesc[nCol].rgPropertySets[0].rgProperties =   
            new DBPROP[NCOLPROPS_MAX];  
        dbcoldesc[nCol].rgPropertySets[0].cProperties = NCOLPROPS_MAX;  
        dbcoldesc[nCol].rgPropertySets[0].guidPropertySet =  
            DBPROPSET_COLUMN;  
  
        for (nProp = 0; nProp < NCOLPROPS_MAX; nProp++)  
            {  
            dbcoldesc[nCol].rgPropertySets[0].rgProperties[nProp].  
                dwOptions = DBPROPOPTIONS_REQUIRED;  
            dbcoldesc[nCol].rgPropertySets[0].rgProperties[nProp].colid  
                 = DB_NULLID;  
  
            VariantInit(  
                &(dbcoldesc[nCol].rgPropertySets[0].rgProperties[nProp].  
                    vValue));  
  
            dbcoldesc[nCol].rgPropertySets[0].rgProperties[nProp].  
                vValue.vt = VT_BOOL;  
            }  
        }  
  
    // Set the column-specific information.  
    dbcoldesc[0].dbcid.uName.pwszName = L"OrderID";  
    dbcoldesc[0].rgPropertySets[0].rgProperties[0].dwPropertyID =   
        DBPROP_COL_NULLABLE;  
    dbcoldesc[0].rgPropertySets[0].rgProperties[0].vValue.boolVal =   
        VARIANT_FALSE;  
    dbcoldesc[0].rgPropertySets[0].cProperties = 1;  
  
    dbcoldesc[1].dbcid.uName.pwszName = L"ProductID";  
    dbcoldesc[1].rgPropertySets[0].rgProperties[0].dwPropertyID =   
        DBPROP_COL_NULLABLE;  
    dbcoldesc[1].rgPropertySets[0].rgProperties[0].vValue.boolVal =   
        VARIANT_FALSE;  
    dbcoldesc[1].rgPropertySets[0].cProperties = 1;  
  
    dbcoldesc[2].dbcid.uName.pwszName = L"UnitPrice";  
    dbcoldesc[2].wType = DBTYPE_CY;  
    dbcoldesc[2].rgPropertySets[0].rgProperties[0].dwPropertyID =   
        DBPROP_COL_NULLABLE;  
    dbcoldesc[2].rgPropertySets[0].rgProperties[0].vValue.boolVal =   
        VARIANT_FALSE;  
    dbcoldesc[2].rgPropertySets[0].cProperties = 1;  
  
    dbcoldesc[3].dbcid.uName.pwszName = L"Quantity";  
    dbcoldesc[3].rgPropertySets[0].rgProperties[0].dwPropertyID =   
        DBPROP_COL_NULLABLE;  
    dbcoldesc[3].rgPropertySets[0].rgProperties[0].vValue.boolVal =   
        VARIANT_FALSE;  
    dbcoldesc[3].rgPropertySets[0].cProperties = 1;  
  
    dbcoldesc[4].dbcid.uName.pwszName = L"Discount";  
    dbcoldesc[4].wType = DBTYPE_NUMERIC;  
    dbcoldesc[4].bPrecision = 2;  
    dbcoldesc[4].bScale = 2;  
    dbcoldesc[4].rgPropertySets[0].rgProperties[0].dwPropertyID =   
        DBPROP_COL_NULLABLE;  
    dbcoldesc[4].rgPropertySets[0].rgProperties[0].vValue.boolVal =   
        VARIANT_FALSE;  
    dbcoldesc[4].rgPropertySets[0].rgProperties[1].dwPropertyID =   
        DBPROP_COL_DEFAULT;  
    dbcoldesc[4].rgPropertySets[0].rgProperties[1].vValue.vt = VT_BSTR;  
    dbcoldesc[4].rgPropertySets[0].rgProperties[1].vValue.bstrVal =  
        SysAllocString(L"0");  
    dbcoldesc[4].rgPropertySets[0].cProperties = 2;  
  
    // Set up the dbid for OrderDetails.  
    dbidTable.eKind = DBKIND_NAME;  
    dbidTable.uName.pwszName = L"OrderDetails";  
  
    if (FAILED(hr = pITableDefinition->CreateTable(NULL, &dbidTable,  
        nCols, dbcoldesc, NULL, 0, NULL, NULL, NULL)))  
        {  
        DumpError(pITableDefinition, IID_ITableDefinition);  
        goto SAFE_EXIT;  
        }  
  
SAFE_EXIT:  
    // Clean up dynamic allocation in the property sets.  
    for (nCol = 0; nCol < nCols; nCol++)  
        {  
        for (nProp = 0; nProp < NCOLPROPS_MAX; nProp++)  
            {  
            if (dbcoldesc[nCol].rgPropertySets[0].rgProperties[nProp].  
                vValue.vt == VT_BSTR)  
                {  
                SysFreeString(dbcoldesc[nCol].rgPropertySets[0].  
                    rgProperties[nProp].vValue.bstrVal);  
                }  
            }  
  
        delete [] dbcoldesc[nCol].rgPropertySets[0].rgProperties;  
        delete [] dbcoldesc[nCol].rgPropertySets;  
        }  
  
    return (hr);  
    }  
```  
  
## <a name="see-also"></a>参照  
 [テーブルとインデックス](../../oledb/ole-db-tables-indexes/tables-and-indexes.md)  
  
  
