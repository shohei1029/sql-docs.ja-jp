---
description: sp_special_columns_100 (Azure Synapse Analytics)
title: sp_special_columns_100 (Azure Synapse Analytics)
ms.custom: ''
ms.date: 03/14/2017
ms.prod_service: synapse-analytics, pdw
ms.service: sql-data-warehouse
ms.subservice: design
ms.reviewer: ''
ms.topic: reference
dev_langs:
- TSQL
ms.assetid: 5774fadc-77cc-46f8-8f9f-a0f9efe95e21
author: ronortloff
ms.author: rortloff
monikerRange: '>= aps-pdw-2016 || = azure-sqldw-latest'
ms.openlocfilehash: 45385004023168f40ea47ce9dfe88d14ba4e9fb3
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104745002"
---
# <a name="sp_special_columns_100-azure-synapse-analytics"></a>sp_special_columns_100 (Azure Synapse Analytics)
[!INCLUDE[applies-to-version/asa-pdw](../../includes/applies-to-version/asa-pdw.md)]

  テーブル内の行を一意に識別する、最適な列のセットを返します。 では、トランザクションによって行の値が更新されると、自動的に更新される列も返されます。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則 (Transact-SQL)](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql  
-- Syntax for Azure Synapse Analytics and Parallel Data Warehouse  
  
sp_special_columns_100 [ @table_name = ] 'table_name'     
     [ , [ @table_owner = ] 'table_owner' ]   
     [ , [ @qualifier = ] 'qualifier' ]   
     [ , [ @col_type = ] 'col_type' ]   
     [ , [ @scope = ] 'scope' ]  
     [ , [ @nullable = ] 'nullable' ]   
     [ , [ @ODBCVer = ] 'ODBCVer' ]   
[ ; ]  
```

[!INCLUDE[synapse-analytics-od-unsupported-syntax](../../includes/synapse-analytics-od-unsupported-syntax.md)]
  
## <a name="arguments"></a>引数  
 [ @table_name =] '*table_name*'  
 カタログ情報を返すために使用するテーブルの名前です。 *名前* は **sysname**,、既定値はありません。 ワイルドカードのパターンマッチングはサポートされていません。  
  
 [ @table_owner =] '*table_owner*'  
 カタログ情報を返すために使用するテーブルのテーブル所有者を示します。 *owner* は **sysname**,、既定値は NULL です。 ワイルドカードのパターンマッチングはサポートされていません。 *Owner* が指定されていない場合、基になる DBMS の既定のテーブル可視性ルールが適用されます。  
  
 では [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 、指定された名前のテーブルが現在のユーザーによって所有されている場合、そのテーブルの列が返されます。 *Owner* が指定されておらず、現在のユーザーが指定された *名前* のテーブルを所有していない場合、このプロシージャは、データベース所有者が所有する、指定された *名前* のテーブルを検索します。 テーブルが存在する場合は、その列が返されます。  
  
 [ @qualifier =] '*修飾子*'  
 テーブル修飾子の名前を指定します。 *修飾子* は **sysname**,、既定値は NULL です。 さまざまな DBMS 製品では、3部構成のテーブル名 (*qualifier.owner.name*) がサポートしています。 では [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 、この列はデータベース名を表します。 一部の製品では、テーブルのデータベース環境のサーバー名を表します。  
  
 [ @col_type =] '*col_type*'  
 列の型です。 *col_type* は **char (** 1 **)**,、既定値は r です。型 r は、1つまたは複数の列から値を取得することによって、指定されたテーブル内の任意の行を一意に識別できる、最適な列または列のセットを返します。 列には、この目的のために特別に設計された擬似列か、テーブルの一意インデックスの1つ以上の列を指定できます。 種類 V は、指定したテーブルに自動更新される列が 1 つ以上存在する場合、該当する列または列のセットを返します。この列は、トランザクションによって行の値が更新されると、データ ソースによって自動的に更新される列です。  
  
 [ @scope =] '*scope*'  
 ROWID の最低限必要なスコープを指定します。 *スコープ* は **char (** 1 **)**,、既定値は T です。範囲 C は、ROWID がその行に位置している場合にのみ有効であることを指定します。 範囲 T は、ROWID がトランザクションに対して有効であることを指定します。  
  
 [ @nullable =] '*nullable*'  
 特別列に NULL 値を認めるかどうかを示します。 *nullable* は **char (** 1 **)**,、既定値は U です。 O は、null 値を許可しない特殊な列を指定します。 U は部分的に NULL を許容する列を指定します。  
  
 [ @ODBCVer =] '*Odbcver*'  
 使用する ODBC のバージョンを指定します。 *Odbcver* は **int (** 4 **)**,、既定値は2です。 既定値は ODBC Version 2.0 を示します。 Odbc バージョン2.0 と ODBC バージョン3.0 の相違点の詳細については、odbc version 3.0 の ODBC Sqlsee Columns の仕様を参照してください。  
  
## <a name="return-code-values"></a>リターン コードの値  
 なし  
  
## <a name="result-sets"></a>結果セット  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|SCOPE|**smallint**|行 ID の実際のスコープ。 0、1、または2を指定できます。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 常に0を返します。 このフィールドは常に値を返します。<br /><br /> 0 = SQL_SCOPE_CURROW。 行 ID は、その行に位置している場合にのみ有効であることが保証されます。 その行が別のトランザクションによって更新または削除された場合は、後でその行 ID を使って再度選択しても行は返されません。<br /><br /> 1 = SQL_SCOPE_TRANSACTION。 行 ID は、現在のトランザクションの間有効であることが保証されます。<br /><br /> 2 = SQL_SCOPE_SESSION。 行 ID は、セッションの間 (トランザクションの境界を越えて) 有効であることが保証されます。|  
|COLUMN_NAME|**sysname**|返された *テーブル* の各列の列名。 このフィールドは常に値を返します。|  
|DATA_TYPE|**smallint**|ODBC SQL データ型。|  
|TYPE_NAME|**sysname**|データソースに依存するデータ型の名前。たとえば、 **char**、 **varchar**、 **money**、 **text** などです。|  
|PRECISION|**Int**|データソースの列の有効桁数。 このフィールドは常に値を返します。|  
|LENGTH|**Int**|データソース内のバイナリ形式のデータ型に必要な長さ (バイト単位)。たとえば、 **char (** 10 **)** の場合は10、 **整数** の場合は4、 **smallint** の場合は2になります。|  
|SCALE|**smallint**|データソース上の列の小数点以下桁数。 小数点以下桁数が適用されないデータ型に対しては NULL が返されます。|  
|PSEUDO_COLUMN|**smallint**|その列が疑似列であるかどうかを示します。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 常に1を返します。<br /><br /> 0 = SQL_PC_UNKNOWN<br /><br /> 1 = SQL_PC_NOT_PSEUDO<br /><br /> 2 = SQL_PC_PSEUDO|  
  
## <a name="remarks"></a>解説  
 sp_special_columns は ODBC の SQLSpecialColumns に相当します。 返される結果は、スコープによって並べ替えられます。  
  
## <a name="permissions"></a>権限  
 スキーマに対する SELECT 権限が必要です。  
  
## <a name="examples-sssdwfull-and-sspdw"></a>例: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)]、[!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
 次の例では、テーブル内の行を一意に識別する列に関する情報を返し `FactFinance` ます。  
  
```sql  
-- Uses AdventureWorks  
  
EXEC sp_special_columns_100 @table_name = 'FactFinance';  
```  
  
## <a name="see-also"></a>参照  
 [Azure Synapse Analytics ストアドプロシージャ](../../relational-databases/system-stored-procedures/sql-data-warehouse-stored-procedures.md)  
  
  

