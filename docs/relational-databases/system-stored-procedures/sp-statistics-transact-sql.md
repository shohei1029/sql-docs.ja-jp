---
description: sp_statistics (Transact-SQL)
title: sp_statistics (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- sp_statistics_TSQL
- sp_statistics
dev_langs:
- TSQL
helpviewer_keywords:
- sp_statistics
ms.assetid: 0bb6495f-258a-47ec-9f74-fd16671d23b8
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: f9584cf09ef7602b6b821d51230bc72d556e0169
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104752472"
---
# <a name="sp_statistics-transact-sql"></a>sp_statistics (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  指定したテーブルまたはインデックス付きビュー上にあるすべてのインデックスおよび統計の一覧を返します。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql  
-- Syntax for SQL Server, Azure SQL Database, Azure Synapse Analytics, Parallel Data Warehouse  
  
sp_statistics [ @table_name = ] 'table_name'    
     [ , [ @table_owner = ] 'owner' ]   
     [ , [ @table_qualifier = ] 'qualifier' ]   
     [ , [ @index_name = ] 'index_name' ]   
     [ , [ @is_unique = ] 'is_unique' ]  
     [ , [ @accuracy = ] 'accuracy' ]  
```  

[!INCLUDE[synapse-analytics-od-unsupported-syntax](../../includes/synapse-analytics-od-unsupported-syntax.md)]

## <a name="arguments"></a>引数  
`[ @table_name = ] 'table_name'` カタログ情報を返すために使用するテーブルを指定します。 *table_name* は **sysname** であり、既定値はありません。 ワイルドカードのパターンマッチングはサポートされていません。  
  
`[ @table_owner = ] 'owner'` カタログ情報を返すために使用するテーブルのテーブル所有者の名前を指定します。 *table_owner* は **sysname**,、既定値は NULL です。 ワイルドカードのパターンマッチングはサポートされていません。 *Owner* が指定されていない場合、基になる DBMS の既定のテーブル可視性ルールが適用されます。  
  
 では [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 、指定された名前のテーブルが現在のユーザーによって所有されている場合、そのテーブルのインデックスが返されます。 *Owner* が指定されておらず、現在のユーザーが指定された *名前* のテーブルを所有していない場合、このプロシージャは、データベース所有者が所有する、指定された *名前* のテーブルを検索します。 存在する場合は、そのテーブルのインデックスが返されます。  
  
`[ @table_qualifier = ] 'qualifier'` テーブル修飾子の名前を指定します。 *修飾子* は **sysname**,、既定値は NULL です。 さまざまな DBMS 製品では、3つの要素で構成するテーブル (_修飾子_) がサポート **しています。**_所有者_**。**_名前_)。 では [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 、このパラメーターはデータベース名を表します。 一部の製品では、テーブルのデータベース環境のサーバー名を表します。  
  
`[ @index_name = ] 'index_name'` インデックス名を指定します。 *index_name* は **sysname**,、既定値は% です。 ワイルドカードパターンマッチングがサポートされています。  
  
`[ @is_unique = ] 'is_unique'` 一意のインデックス ( **Y** の場合) のみを返すかどうかを指定します。 *is_unique* は **char (1)**,、既定値は **N** です。  
  
`[ @accuracy = ] 'accuracy'` 統計のカーディナリティとページ精度のレベルです。 *精度* は **char (1)**,、既定値は **Q** です。カーディナリティとページが正確になるように統計が更新されるようにするには、 **E** を指定します。  
  
 値 **E** (SQL_ENSURE) は、無条件で統計を取得するようにドライバーに要求します。  
  
 値 **Q** (SQL_QUICK) は、サーバーからすぐに使用できる場合にのみ、ドライバーがカーディナリティとページを取得するように要求します。 この場合、ドライバーで取得される値が最新であるかどうかは保証されません。 Open Group standard に書き込まれたアプリケーションは、常に ODBC 3. x 互換のドライバーから SQL_QUICK 動作します。  
  
## <a name="result-sets"></a>結果セット  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|**TABLE_QUALIFIER**|**sysname**|テーブル修飾子の名前。 この列は NULL にすることができます。|  
|**TABLE_OWNER**|**sysname**|テーブル所有者の名前。 この列は常に値が返されます。|  
|**TABLE_NAME**|**sysname**|テーブル名。 この列は常に値が返されます。|  
|**NON_UNIQUE**|**smallint**|NOT NULL。<br /><br /> 0 = 一意<br /><br /> 1 = 一意ではない|  
|**INDEX_QUALIFIER**|**sysname**|インデックス所有者の名前。 一部の DBMS 製品では、テーブル所有者以外のユーザーがインデックスを作成できます。 で [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] は、この列は常に **TABLE_NAME** と同じです。|  
|**INDEX_NAME**|**sysname**|インデックスの名前です。 この列は常に値が返されます。|  
|**TYPE**|**smallint**|この列は常に値を返します。<br /><br /> 0 = テーブルの統計<br /><br /> 1 = クラスター化<br /><br /> 2 = ハッシュされる<br /><br /> 3 = 非クラスター化|  
|**SEQ_IN_INDEX**|**smallint**|インデックス内での列の位置。|  
|**COLUMN_NAME**|**sysname**|返される **TABLE_NAME** の各列の列名。 この列は常に値が返されます。|  
|**COLLATION**|**char(1)**|照合順序で使用されている並べ替え順。 次の値をとります。<br /><br /> A = 昇順<br /><br /> D = 降順<br /><br /> NULL = 適用なし|  
|**基数**|**int**|テーブルの行数またはインデックス内の一意の値。|  
|**トピック**|**int**|インデックスまたはテーブルを格納するページ数。|  
|**FILTER_CONDITION**|**varchar(128)**|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では値は返されません。|  
  
## <a name="return-code-values"></a>リターン コードの値  
 なし  
  
## <a name="remarks"></a>解説  
 結果セットのインデックスは、 **NON_UNIQUE**、 **型**、 **INDEX_NAME**、および **SEQ_IN_INDEX** 列の昇順で表示されます。  
  
 クラスター化インデックス型は、テーブルのデータがインデックスの順に格納されているインデックス型を指します。 これは、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] クラスター化インデックスに対応します。  
  
 ハッシュ化インデックス型は、完全一致検索または範囲検索を受け付けますが、パターン照合検索ではインデックスは使用されません。  
  
 **sp_statistics** は、ODBC の **sqlstatistics** に相当します。 返される結果は、 **NON_UNIQUE**、 **種類**、 **INDEX_QUALIFIER**、 **INDEX_NAME**、および **SEQ_IN_INDEX** 順に並べ替えられます。 詳細については、 [ODBC API リファレンス](../../odbc/reference/syntax/odbc-reference.md)を参照してください。  
  
## <a name="permissions"></a>権限  
 スキーマに対する SELECT 権限が必要です。  
  
## <a name="example-sssdwfull-and-sspdw"></a>例: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] および [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
 次の例では、テーブルに関する情報を返し `DimEmployee` ます。  
  
```sql  
-- Uses AdventureWorks  
  
EXEC sp_statistics DimEmployee;  
```  
  
## <a name="see-also"></a>参照  
 [Transact-sql&#41;&#40;のカタログストアドプロシージャ ](../../relational-databases/system-stored-procedures/catalog-stored-procedures-transact-sql.md)   
 [システム ストアド プロシージャ &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/system-stored-procedures-transact-sql.md)  
  
