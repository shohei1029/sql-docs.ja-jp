---
description: sp_columns (Transact-SQL)
title: sp_columns (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 10/17/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- sp_columns_TSQL
- sp_columns
dev_langs:
- TSQL
helpviewer_keywords:
- sp_columns
ms.assetid: 2dec79cf-2baf-4c0f-8cbb-afb1a8654e1e
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 7ef2d20ec911fc361ebbb7162853ee2e2cfe748c
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104750242"
---
# <a name="sp_columns-transact-sql"></a>sp_columns (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  現在の環境でクエリを実行できる、指定されたオブジェクトの列情報を返します。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql  
-- Syntax for SQL Server, Azure SQL Database, Azure Synapse Analytics, Parallel Data Warehouse  
  
sp_columns [ @table_name = ] object  
     [ , [ @table_owner = ] owner ]   
     [ , [ @table_qualifier = ] qualifier ]   
     [ , [ @column_name = ] column ]   
     [ , [ @ODBCVer = ] ODBCVer ]  
```  
  
## <a name="arguments"></a>引数  
`[ \@table_name = ] object` カタログ情報を返すために使用するオブジェクトの名前を指定します。 *オブジェクト* には、テーブル、ビュー、またはテーブル値関数などの列を持つその他のオブジェクトを指定できます。 *オブジェクト* は **nvarchar (384)**,、既定値はありません。 ワイルドカードパターンマッチングがサポートされています。  
  
`[ \@table_owner = ] owner` カタログ情報を返すために使用されるオブジェクトの所有者を示します。 *owner* は **nvarchar (384)**,、既定値は NULL です。 ワイルドカードパターンマッチングがサポートされています。 *Owner* が指定されていない場合、基になる DBMS の既定のオブジェクト可視性ルールが適用されます。  
  
 指定した名前のオブジェクトを現在のユーザーが所有している場合は、そのオブジェクトの列が返されます。 *Owner* が指定されておらず、現在のユーザーが指定された *オブジェクト* を持つオブジェクトを所有していない場合、 **sp_columns** は、データベース所有者が所有する、指定された *オブジェクト* を持つオブジェクトを検索します。 存在する場合は、そのオブジェクトの列が返されます。  
  
`[ \@table_qualifier = ] qualifier` オブジェクト修飾子の名前を指定します。 *修飾子* は **sysname**,、既定値は NULL です。 さまざまな DBMS 製品では、3つの要素で構成するオブジェクト (_修飾子_) がサポート **しています。**_所有者_**。**_名前_)。 では [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 、この列はデータベース名を表します。 一部の製品では、オブジェクトのデータベース環境のサーバー名を表します。  
  
`[ \@column_name = ] column` は1つの列であり、カタログ情報の列が1つだけ必要な場合に使用します。 *列* は **nvarchar (384)**,、既定値は NULL です。 *列* が指定されていない場合は、すべての列が返されます。 では [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 、 *列* は **syscolumns** テーブルに示されている列名を表します。 ワイルドカードパターンマッチングがサポートされています。 相互運用性を最大にするために、ゲートウェイクライアントでは、SQL-92 標準のパターン照合 (% と _ ワイルドカード文字) のみを想定する必要があります。  
  
`[ \@ODBCVer = ] ODBCVer` 使用されている ODBC のバージョンを示します。 *Odbcver* は **int**,、既定値は2です。 これは、ODBC バージョン2を示します。 有効な値は2または3です。 バージョン2とバージョン3の動作の違いについては、ODBC **Sqlcolumns** の仕様を参照してください。  
  
## <a name="return-code-values"></a>リターン コードの値  
 なし  
  
## <a name="result-sets"></a>結果セット  
 **Sp_columns** カタログストアドプロシージャは、ODBC の **sqlcolumns** に相当します。 返される結果は、 **TABLE_QUALIFIER**、 **TABLE_OWNER**、および **TABLE_NAME** 順に並べ替えられます。  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|**TABLE_QUALIFIER**|**sysname**|オブジェクト修飾子の名前です。 このフィールドは NULL にすることができます。|  
|**TABLE_OWNER**|**sysname**|オブジェクト所有者の名前。 このフィールドは常に値を返します。|  
|**TABLE_NAME**|**sysname**|オブジェクト名 このフィールドは常に値を返します。|  
|**COLUMN_NAME**|**sysname**|返される **TABLE_NAME** の各列の列名。 このフィールドは常に値を返します。|  
|**DATA_TYPE**|**smallint**|ODBC データ型の整数コードです。 ODBC のデータ型にマップできないデータ型の場合は、NULL になります。 ネイティブデータ型の名前が **TYPE_NAME** 列に返されます。|  
|**TYPE_NAME**|**sysname**|データ型を表す文字列。 基になる DBMS は、このデータ型の名前を提示します。|  
|**PRECISION**|**int**|有効桁数。 **有効桁数** 列の戻り値は、10進数値です。|  
|**LENGTH**|**int**|データの転送サイズ。<sup>1</sup>|  
|**段階**|**smallint**|小数点の右側の桁数。|  
|**RADIX**|**smallint**|数値データ型のベース。|  
|**NULLABLE**|**smallint**|Null 値の許容属性を指定します。<br /><br /> 1 = NULL 値を許容します。<br /><br /> 0 = NULL 値を許容しません。|  
|**備考**|**varchar (254)**|このフィールドは常に NULL を返します。|  
|**COLUMN_DEF**|**nvarchar (4000)**|列の既定値です。|  
|**SQL_DATA_TYPE**|**smallint**|記述子の TYPE フィールドでの SQL データ型の値です。 この列は、 **datetime** および SQL-92 **interval** データ型を除き、 **DATA_TYPE** 列と同じです。 この列は常に値が返されます。|  
|**SQL_DATETIME_SUB**|**smallint**|**Datetime** および SQL-92 **interval** データ型のサブタイプコード。 他のデータ型の場合、この列は NULL を返します。|  
|**CHAR_OCTET_LENGTH**|**int**|文字型または整数型の列の最大長 (バイト単位)。 他のすべてのデータ型については、この列は NULL を返します。|  
|**ORDINAL_POSITION**|**int**|オブジェクト内の列の序数位置。 オブジェクトの最初の列は1です。 この列は常に値が返されます。|  
|**IS_NULLABLE**|**varchar (254)**|オブジェクト内の列の NULL 値の許容属性です。 ISO ルールの後に、null 値の許容属性が決定されます。 ISO SQL に準拠している DBMS では、空の文字列を返すことはできません。<br /><br /> YES = 列に NULL を含むことができます。<br /><br /> NO = 列に NULL を含めることはできません。<br /><br /> Null 値許容属性が不明の場合、この列は長さ0の文字列を返します。<br /><br /> この列に返される値は、 **Null 許容** 列に対して返される値とは異なります。|  
|**SS_DATA_TYPE**|**tinyint**|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 拡張ストアドプロシージャによって使用されるデータ型。 詳細については、「[データ型 &#40;Transact-SQL&#41;](../../t-sql/data-types/data-types-transact-sql.md)」を参照してください。|  
  
 <sup>1</sup> 詳細については、Microsoft ODBC のドキュメントを参照してください。  
  
## <a name="permissions"></a>権限  
 スキーマに対する SELECT および VIEW DEFINITION 権限が必要です。  
  
## <a name="remarks"></a>解説  
 **sp_columns** は、区切られた識別子の要件に従います。 詳細については、「[データベース識別子](../../relational-databases/databases/database-identifiers.md)」を参照してください。  
  
## <a name="examples"></a>例  
 次の例では、指定されたテーブルの列情報を返します。  
  
```sql  
USE AdventureWorks2012;  
GO  
EXEC sp_columns @table_name = N'Department',  
   @table_owner = N'HumanResources';  
```  
  
## <a name="examples-sssdwfull-and-sspdw"></a>例: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)]、[!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
 次の例では、指定されたテーブルの列情報を返します。  
  
```sql  
-- Uses AdventureWorks  
  
EXEC sp_columns @table_name = N'DimEmployee',  
   @table_owner = N'dbo';  
```  
  
## <a name="see-also"></a>参照  
 [sp_tables &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-tables-transact-sql.md)   
 [Transact-sql&#41;&#40;のカタログストアドプロシージャ ](../../relational-databases/system-stored-procedures/catalog-stored-procedures-transact-sql.md)   
 [システム ストアド プロシージャ &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/system-stored-procedures-transact-sql.md)  
  
  


