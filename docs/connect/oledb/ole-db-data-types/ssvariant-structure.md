---
title: SSVARIANT 構造体 (OLE DB ドライバー)
description: OLE DB Driver for SQL Server の SSVARIANT 構造
ms.custom: ''
ms.date: 06/15/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: connectivity
ms.topic: reference
f1_keywords:
- SSVARIANT
helpviewer_keywords:
- SSVARIANT struct
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: 7095942d695c57667e47d32bb6c60a97c51fbd59
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104752022"
---
# <a name="ssvariant-structure"></a>SSVARIANT 構造体
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[Driver_OLEDB_Download](../../../includes/driver_oledb_download.md)]

  msoledbsql.h で定義されている **SSVARIANT** 構造体は、OLE DB Driver for SQL Server の DBTYPE_SQLVARIANT 値に対応します。  
  
 **SSVARIANT** は、識別共用体です。 vt メンバーの値に応じて、コンシューマーは読み取るメンバーを決めることができます。 vt 値は、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] データ型に対応します。 したがって、**SSVARIANT** 構造体には、任意の SQL Server 型を格納できます。 標準の OLE DB 型のデータ構造体の詳細については、「[型インジケーター](/previous-versions/windows/desktop/ms711251(v=vs.85))」を参照してください。  
  
## <a name="remarks"></a>解説  
 DataTypeCompat==80 の場合、いくつかの **SSVARIANT** サブタイプが文字列になります。 たとえば、次の vt 値は **SSVARIANT** では VT_SS_WVARSTRING として表されます。  
  
-   VT_SS_DATETIMEOFFSET  
  
-   VT_SS_DATETIME2  
  
-   VT_SS_TIME2  
  
-   VT_SS_DATE  
  
 DateTypeCompat == 0 の場合、これらの型はネイティブ形式で表されます。  
  
 SSPROP_INIT_DATATYPECOMPATIBILITY の詳細については、「[OLE DB Driver for SQL Server での接続文字列キーワードの使用](../../oledb/applications/using-connection-string-keywords-with-oledb-driver-for-sql-server.md)」を参照してください。  
  
 msoledbsql.h ファイルには、**SSVARIANT** 構造体のメンバー型の逆参照を単純化するバリアント アクセス マクロが格納されています。 たとえば、V_SS_DATETIMEOFFSET を次のように使用できます。  
  
```  
memcpy(&V_SS_DATETIMEOFFSET(pssVar).tsoDateTimeOffsetVal, pDTO, cbNative);  
V_SS_DATETIMEOFFSET(pssVar).bScale = bScale;  
```  
  
 **SSVARIANT** 構造体の各メンバーのアクセス マクロの完全なセットについては、msoledbsql.h ファイルを参照してください。  
  
 次の表で、**SSVARIANT** 構造体のメンバーについて説明します。  
  
|メンバー|OLE DB 型インジケーター|OLE DB C データ型|vt の値|説明|  
|------------|---------------------------|------------------------|--------------|--------------|  
|vt|SSVARTYPE|||**SSVARIANT** 構造体に格納される値の型を指定します。|  
|bTinyIntVal|DBTYPE_UI1|**BYTE**|**VT_SS_UI1**|**tinyint**[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] データ型がサポートされています。|  
|sShortIntVal|DBTYPE_I2|**SHORT**|**VT_SS_I2**|**smallint**[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] データ型がサポートされています。|  
|lIntVal|DBTYPE_I4|**LONG**|**VT_SS_I4**|**int**[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] データ型がサポートされています。|  
|llBigIntVal|DBTYPE_I8|**LARGE_INTEGER**|**VT_SS_I8**|**bigint**[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] データ型がサポートされています。|  
|fltRealVal|DBTYPE_R4|**float**|**VT_SS_R4**|**real**[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] データ型がサポートされています。|  
|dblFloatVal|DBTYPE_R8|**double**|**VT_SS_R8**|**float**[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] データ型がサポートされています。|  
|cyMoneyVal|DBTYPE_CY|**LARGE_INTEGER**|**VT_SS_MONEY VT_SS_SMALLMONEY**|**money** と **smallmoney**[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] のデータ型がサポートされています。|  
|fBitVal|DBTYPE_BOOL|**VARIANT_BOOL**|**VT_SS_BIT**|**bit**[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] データ型がサポートされています。|  
|rgbGuidVal|DBTYPE_GUID|**GUID**|**VT_SS_GUID**|**uniqueidentifier**[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] データ型がサポートされています。|  
|numNumericVal|DBTYPE_NUMERIC|**DB_NUMERIC**|**VT_SS_NUMERIC**|**numeric**[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] データ型がサポートされています。|  
|dDateVal|DBTYPE_DATE|**DBDATE**|**VT_SS_DATE**|**date**[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] データ型をサポートします。|  
|tsDateTimeVal|DBTYPE_DBTIMESTAMP|**DBTIMESTAMP**|**VT_SS_SMALLDATETIME VT_SS_DATETIME VT_SS_DATETIME2**|**smalldatetime**、**datetime**、**datetime2**[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] のデータ型がサポートされています。|  
|Time2Val|DBTYPE_DBTIME2|**DBTIME2**|**VT_SS_TIME2**|**time**[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] データ型がサポートされています。<br /><br /> 次のメンバーを含みます。<br /><br /> *tTime2Val* (**DBTIME2**)<br /><br /> *bScale* (**BYTE**) *tTime2Val* 値の小数点以下桁数を指定します。|  
|DateTimeVal|DBTYPE_DBTIMESTAMP|**DBTIMESTAMP**|**VT_SS_DATETIME2**|**datetime2**[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] データ型がサポートされています。<br /><br /> 次のメンバーを含みます。<br /><br /> *tsDataTimeVal* (DBTIMESTAMP)<br /><br /> *bScale* (**BYTE**) *tsDataTimeVal* 値の小数点以下桁数を指定します。|  
|DateTimeOffsetVal|DBTYPE_DBTIMESTAMPOFSET|**DBTIMESTAMPOFFSET**|**VT_SS_DATETIMEOFFSET**|**datetimeoffset**[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] データ型がサポートされています。<br /><br /> 次のメンバーを含みます。<br /><br /> *tsoDateTimeOffsetVal* (**DBTIMESTAMPOFFSET**)<br /><br /> *bScale* (**BYTE**) *tsoDateTimeOffsetVal* 値の小数点以下桁数を指定します。|  
|NCharVal|対応する OLE DB 型インジケーターはありません。|**struct _NCharVal**|**VT_SS_WVARSTRING、**<br /><br /> **VT_SS_WSTRING**|**nchar** と **nvarchar**[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] のデータ型がサポートされています。<br /><br /> 次のメンバーを含みます。<br /><br /> *sActualLength* (**SHORT**) *pwchNCharVal* がポイントする文字列の実際の長さを指定します。 末尾の 0 は含まれません。<br /><br /> *sMaxLength* (**SHORT**) *pwchNCharVal* がポイントする文字列の最大長を指定します。<br /><br /> *pwchNCharVal* (**WCHAR** \*) 文字列へのポインター。<br /><br /> *rgbReserved* (**BYTE[5]** ) 照合順序情報を指定します。<br /><br /> 使用されていないメンバー: *dwReserved*、および *pwchReserved*。|  
|CharVal|対応する OLE DB 型インジケーターはありません。|**struct _CharVal**|**VT_SS_STRING、**<br /><br /> **VT_SS_VARSTRING**|**char** と **varchar**[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] のデータ型がサポートされています。<br /><br /> 次のメンバーを含みます。<br /><br /> *sActualLength* (**SHORT**) *pchCharVal* がポイントする文字列の実際の長さを指定します。 末尾の 0 は含まれません。<br /><br /> *sMaxLength* (**SHORT**) *pchCharVal* がポイントする文字列の最大長を指定します。<br /><br /> *pchCharVal* (**CHAR** \*) 文字列へのポインター。<br /><br /> *rgbReserved* (**BYTE[5]** ) 照合順序情報を指定します。<br /><br /> 使用されないメンバー :<br /><br /> *dwReserved*、および *pwchReserved*。|  
|BinaryVal|対応する OLE DB 型インジケーターはありません。|**struct _BinaryVal**|**VT_SS_VARBINARY、**<br /><br /> **VT_SS_BINARY**|**binary** と **varbinary**[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] のデータ型がサポートされています。<br /><br /> 次のメンバーを含みます。<br /><br /> *sActualLength* (**SHORT**) *prgbBinaryVal* がポイントするデータの実際の長さを指定します。<br /><br /> *sMaxLength* (**SHORT**) *prgbBinaryVal* がポイントするデータの最大長を指定します。<br /><br /> *prgbBinaryVal* (**BYTE** \*) バイナリ データへのポインター。<br /><br /> 使用されていないメンバー: *dwReserved*。|  
|UnknownType|未使用|未使用|未使用|未使用|  
|BLOBType|未使用|未使用|未使用|未使用|  
| &nbsp; | &nbsp; | &nbsp; | &nbsp; | &nbsp; |
  
## <a name="known-issues"></a>既知の問題
### <a name="possible-narrow-string-data-corruption"></a>ナロー文字列データの破損の可能性
バージョン 18.4 より前の OLE DB ドライバーでは、次のすべての条件に当てはまる場合、`sql_variant` 列に挿入すると、サーバーのデータが破損する可能性がありました。
- クライアント コンピューターのコード ページが、データベース照合順序のコード ページと一致しない。
- 挿入するクライアント バッファーに、クライアントのコード ページでエンコードされた非 ASCII ナロー文字列文字が含まれている。
- 次のいずれかの条件に該当する。
  - `sql_variant` 列に対応するパラメーターを記述する `DBPARAMBINDINFO` 構造の `pwszDataSourceType` フィールドが、`L"DBTYPE_SQLVARIANT"`、`L"DBTYPE_VARIANT"`、または `L"sql_variant"` に設定されている。 詳細については、次の情報を参照してください。「[ICommandWithParameters::SetParameterInfo](/previous-versions/windows/desktop/ms725393(v=vs.85))」。

    *or*
  - 挿入に使用されるパラメーター化された SQL クエリが準備されている。

具体的には、OLE DB ドライバーでは、データが挿入前にデータベース照合順序のコード ページに変換されませんでした。 ただし、データベース照合順序のコード ページでデータがエンコードされたことが、ドライバーによって誤ってサーバーに示されていました。 この動作が原因で、データと、`sql_variant` 列に格納されている対応するコード ページが一致しませんでした。

同様に、同じ値が取得されたとき、OLE DB ドライバーでは、文字列がクライアントのコード ページに変換されませんでした。 ただし、挿入されたデータは既にクライアントのコード ページにあるため (上の段落を参照)、クライアント アプリケーションではデータを正しく解釈できました。 それでも、他のドライバーを使用するアプリケーションでは、これらの値が破損した形式で取得されました。 破損が発生したのは、他のドライバーによってデータベース照合順序のコード ページで文字列が解釈され、それをクライアントのコード ページに変換することが試行されたためです。

バージョン 18.4 以降では、OLE DB ドライバーでは、挿入前にナロー文字列がデータベース照合順序のコード ページに変換されます。 同様に、ドライバーでは、取得時にデータがクライアントのコード ページに再変換されます。 その結果、上記のバグに依存するクライアント アプリケーションでは、以前のバージョンの OLE DB ドライバーを使用して挿入されたデータを取得するときに問題が発生する可能性があります。 次の[復旧手順](#recovery-procedure)は、これらの問題を解決するためのガイダンスを提供することを目的としています。

### <a name="recovery-procedure"></a>復旧手順
> [!IMPORTANT]  
> 次の復旧手順を実行する前に、既存のデータを必ずバックアップしてください。

バージョン 18.4 の OLE DB ドライバーに切り替えた後に、アプリケーションで `sql_variant` 列からのデータの取得時に問題が発生した場合は、データが格納されているデータベースと同じ照合順序になるように破損データを修正する必要があります。 次のスクリプトを使用すると、`sql_variant` 列の 1 つの値を復旧できます。 このスクリプトはテンプレートであり、シナリオに合わせて調整する必要があります。

> [!IMPORTANT]  
> データの元のコード ページは格納されていないため、データが最初にどのようにエンコードされたかをサーバーに通知する必要があります。 これを行うには、最初にデータを挿入したクライアントのコード ページと同じコード ページを持つデータベースのコンテキスト内で、スクリプトを実行します。 たとえば、コード ページ `932` で構成されたクライアントから破損データが挿入された場合は、日本語の照合順序 (例: `Japanese_XJIS_100_CS_AI`) を使用してデータベースのコンテキスト内で、次のスクリプトを実行する必要があります。

```sql
/*
    Description:
        Template that can be used to recover the corrupted value inserted into the sql_variant column.

    Scenario:
        The database is named [YourDatabase] and it contains a table named [YourTable], which contains the corrupted value.
        Schema is named [dbo].
        The corrupted value is stored in a column of type sql_variant named [YourColumn].
        The corrupted value is sql_variant of BaseType char. For details on sql_variant properties, see:
            https://docs.microsoft.com/sql/t-sql/functions/sql-variant-property-transact-sql
*/

-- Base type in sql_variant can hold a maximum of 8000 bytes
-- For details see: 
--  https://docs.microsoft.com/sql/t-sql/data-types/sql-variant-transact-sql#remarks
DECLARE @bin VARBINARY(8000)

-- In the following lines we convert the sql_variant base type to binary.
-- <FilterExpression>
--      Is a placeholder and must be replaced with an expression that filters a single corrupted value to be recovered.
--      Therefore, the expression must result in a single value being returned only.
SET @bin = (SELECT CAST([YourColumn] AS VARBINARY(8000)) FROM [YourDatabase].[dbo].[YourTable] WHERE <FilterExpression>)

-- In the following lines we store the binary value in char(59) (a fixed-size character data type).
-- IMPORTANT NOTE: 
--      This example assumes the corrupted sql_variant's base type is char(59).
--      You MUST adjust the type (that is, char/varchar) and size to match your scenario exactly.
DECLARE @char CHAR(59)
SET @char = CAST((@bin) AS CHAR(59))
DECLARE @sqlvariant sql_variant

-- The following lines recover the corrupted value by translating the value to the collation of the database.
-- <DBCollation>
--      Must be replaced with the collation (for example, Latin1_General_100_CI_AS_SC_UTF8) of the database holding the data.
SET @sqlvariant = @char collate <DBCollation>

-- Finally, we update the corrupted value with the recovered value.
-- "<FilterExpression>"
--      Is a placeholder and must be replaced with an expression that filters a single corrupted value to be recovered.
--      Therefore, the expression must result in a single value being returned only.
UPDATE [YourDatabase].[dbo].[YourTable] SET [YourColumn] = @sqlvariant WHERE <FilterExpression>
```

## <a name="see-also"></a>参照  
 [データ型 &#40;OLE DB&#41;](../../oledb/ole-db-data-types/data-types-ole-db.md)  
  
