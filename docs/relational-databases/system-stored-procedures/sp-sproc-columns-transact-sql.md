---
description: sp_sproc_columns (Transact-sql)
title: sp_sproc_columns (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- sp_sproc_columns
- sp_sproc_columns_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sp_sproc_columns
ms.assetid: 62c18c21-35c5-4772-be0d-ffdcc19c97ab
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 66af1e613e5b02eee96357da383a0b535424ebf5
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104745032"
---
# <a name="sp_sproc_columns-transact-sql"></a>sp_sproc_columns (Transact-sql)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  現在の環境の単一のストアドプロシージャまたはユーザー定義関数の列情報を返します。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```  
-- Syntax for SQL Server, Azure SQL Database, Azure Synapse Analytics, Parallel Data Warehouse  
  
sp_sproc_columns [[@procedure_name = ] 'name']   
    [ , [@procedure_owner = ] 'owner']   
    [ , [@procedure_qualifier = ] 'qualifier']   
    [ , [@column_name = ] 'column_name']  
    [ , [@ODBCVer = ] 'ODBCVer']  
    [ , [@fUsePattern = ] 'fUsePattern']  
```  
  
## <a name="arguments"></a>引数  
`[ @procedure_name = ] 'name'` カタログ情報を返すために使用するプロシージャの名前を指定します。 *名前* は **nvarchar (** 390 **)**,、既定値は%,、現在のデータベース内のすべてのテーブルを意味します。 ワイルドカードパターンマッチングがサポートされています。  
  
`[ @procedure_owner = ] 'owner'` プロシージャの所有者の名前を指定します。 *owner* は **nvarchar (** 384 **)**,、既定値は NULL です。 ワイルドカードパターンマッチングがサポートされています。 *Owner* が指定されていない場合、基になる DBMS の既定のプロシージャ可視性ルールが適用されます。  
  
 指定した名前のプロシージャが現在のユーザーによって所有されている場合は、そのプロシージャに関する情報が返されます。 *Owner* が指定されておらず、現在のユーザーが指定された名前のプロシージャを所有していない場合、 **sp_sproc_columns** は、データベース所有者が所有している、指定された名前のプロシージャを検索します。 プロシージャが存在する場合は、その列に関する情報が返されます。  
  
`[ @procedure_qualifier = ] 'qualifier'` プロシージャ修飾子の名前を指定します。 *修飾子* は **sysname**,、既定値は NULL です。 さまざまな DBMS 製品では、3部構成のテーブル名 (*qualifier.owner.name*) がサポートしています。 では [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 、このパラメーターはデータベース名を表します。 一部の製品では、テーブルのデータベース環境のサーバー名を表します。  
  
`[ @column_name = ] 'column_name'` 1つの列を指定します。カタログ情報の列が1つだけ必要な場合に使用します。 *column_name* は **nvarchar (** 384 **)**,、既定値は NULL です。 *Column_name* を省略すると、すべての列が返されます。 ワイルドカードパターンマッチングがサポートされています。 相互運用性を最大にするために、ゲートウェイクライアントは ISO 標準のパターン照合 (% と _ ワイルドカード文字) のみを想定する必要があります。  
  
`[ @ODBCVer = ] 'ODBCVer'` 使用する ODBC のバージョンを示します。 *Odbcver* は **int**,、既定値は 2,、ODBC バージョン2.0 を示します。 Odbc バージョン2.0 と ODBC バージョン3.0 の相違点の詳細については、odbc version 3.0 の ODBC **SQLProcedureColumns** 仕様を参照してください。  
  
`[ @fUsePattern = ] 'fUsePattern'` アンダースコア (_)、パーセント (%)、および角かっこ ([]) の各文字をワイルドカード文字として解釈するかどうかを決定します。 有効な値は 0 (パターン一致がオフ) および 1 (パターン一致がオン) です。 *Fusepattern* は **ビット**,、既定値は1です。  
  
## <a name="return-code-values"></a>リターン コードの値  
 なし  
  
## <a name="result-sets"></a>結果セット  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|**PROCEDURE_QUALIFIER**|**sysname**|プロシージャ修飾子の名前。 この列は NULL にすることができます。|  
|**PROCEDURE_OWNER**|**sysname**|プロシージャ所有者の名前。 この列は常に値が返されます。|  
|**PROCEDURE_NAME**|**nvarchar (** 134 **)**|プロシージャ名。 この列は常に値が返されます。|  
|**COLUMN_NAME**|**sysname**|返される **TABLE_NAME** の各列の列名。 この列は常に値が返されます。|  
|**COLUMN_TYPE**|**smallint**|このフィールドは常に値を返します。<br /><br /> 0 = SQL_PARAM_TYPE_UNKNOWN<br /><br /> 1 = SQL_PARAM_TYPE_INPUT<br /><br /> 2 = SQL_PARAM_TYPE_OUTPUT<br /><br /> 3 = SQL_RESULT_COL<br /><br /> 4 = SQL_PARAM_OUTPUT<br /><br /> 5 = SQL_RETURN_VALUE|  
|**DATA_TYPE**|**smallint**|ODBC データ型用の整数コードです。 このデータ型を ISO 型にマップできない場合、値は NULL になります。 ネイティブデータ型の名前が **TYPE_NAME** 列に返されます。|  
|**TYPE_NAME**|**sysname**|データ型の文字列表現。 これは、基になる DBMS によって示されるデータ型の名前です。|  
|**PRECISION**|**int**|有効桁数。 **有効桁数** 列の戻り値は、10進数値です。|  
|**LENGTH**|**int**|データの転送サイズです。|  
|**段階**|**smallint**|小数点の右側の桁数。|  
|**RADIX**|**smallint**|数値型の基数です。|  
|**NULLABLE**|**smallint**|Null 値の許容属性を指定します。<br /><br /> 1 = null 値を許容するデータ型を作成できます。<br /><br /> 0 = Null 値は使用できません。|  
|**備考**|**varchar (** 254 **)**|プロシージャの列の説明です。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] はこの列の値を返しません。|  
|**COLUMN_DEF**|**nvarchar (** 4000 **)**|列の既定値です。|  
|**SQL_DATA_TYPE**|**smallint**|記述子の **type** フィールドに表示される SQL データ型の値。 この列は、**datetime** データ型と ISO **interval** データ型以外は、**DATA_TYPE** 列と同じです。 この列は常に値が返されます。|  
|**SQL_DATETIME_SUB**|**smallint**|**SQL_DATA_TYPE** の値が **SQL_DATETIME** または **SQL_INTERVAL** の場合は、**datetime** ISO **interval** サブコードになります。 **Datetime** および ISO **interval** 以外のデータ型の場合、このフィールドは NULL になります。|  
|**CHAR_OCTET_LENGTH**|**int**|**文字** または **バイナリ** データ型の列の最大長 (バイト単位)。 他のすべてのデータ型については、この列は NULL を返します。|  
|**ORDINAL_POSITION**|**int**|テーブル内の列の序数位置。 テーブルの最初の列は1です。 この列は常に値が返されます。|  
|**IS_NULLABLE**|**varchar (254)**|テーブル内の列の null 値の許容属性。 ISO ルールの後に、null 値の許容属性が決定されます。 ISO に準拠している DBMS は、空の文字列を返すことはできません。<br /><br /> 列が NULL を含むことができる場合は YES、含むことができない場合は NO を表示します。<br /><br /> Null 値許容属性が不明の場合、この列は長さ0の文字列を返します。<br /><br /> この列に返される値は、NULLABLE 列に返される値とは異なります。|  
|**SS_DATA_TYPE**|**tinyint**|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 拡張ストアドプロシージャによって使用されるデータ型。 詳細については、「[データ型 &#40;Transact-SQL&#41;](../../t-sql/data-types/data-types-transact-sql.md)」を参照してください。|  
  
## <a name="remarks"></a>解説  
 **sp_sproc_columns** は、ODBC の **SQLProcedureColumns** に相当します。 返される結果は、 **PROCEDURE_QUALIFIER**、 **PROCEDURE_OWNER**、 **PROCEDURE_NAME** の順に並べ替えられ、プロシージャの定義にパラメーターが表示される順序に従って並べ替えられます。  
  
## <a name="permissions"></a>権限  
 スキーマに対する SELECT 権限が必要です。  
  
## <a name="see-also"></a>参照  
 [Transact-sql&#41;&#40;のカタログストアドプロシージャ ](../../relational-databases/system-stored-procedures/catalog-stored-procedures-transact-sql.md)   
 [システム ストアド プロシージャ &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/system-stored-procedures-transact-sql.md)  
  
  
