---
description: CREATE PARTITION SCHEME (Transact-SQL)
title: CREATE PARTITION SCHEME (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 04/10/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- CREATE PARTITION SCHEME
- SCHEME
- PARTITION SCHEME
- CREATE_PARTITION_SCHEME_TSQL
- SCHEME_TSQL
- PARTITION_SCHEME_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- partitioned indexes [SQL Server], schemes
- partitioned tables [SQL Server], schemes
- CREATE PARTITION SCHEME statement
- partition schemes [SQL Server], creating
- filegroups [SQL Server], partitions
- partitioned indexes [SQL Server], filegroups
- partitioned tables [SQL Server], filegroups
- mapping partitions [SQL Server]
ms.assetid: 5b21c53a-b4f4-4988-89a2-801f512126e4
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: aa3279f16685cf3df8979a26de130bd7ff9994bf
ms.sourcegitcommit: 295b9dfc758471ef7d238a2b0f92f93e34acbb1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106054838"
---
# <a name="create-partition-scheme-transact-sql"></a>CREATE PARTITION SCHEME (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  パーティション テーブルまたはパーティション インデックスのパーティションをファイル グループにマップする構成を、現在のデータベース内に作成します。 パーティション テーブルまたはパーティション インデックスのパーティションの数とドメインは、パーティション関数で決まります。 パーティション構成を作成する前に、まず [CREATE PARTITION FUNCTION](../../t-sql/statements/create-partition-function-transact-sql.md) ステートメントでパーティション関数を作成しておく必要があります。  

>[!NOTE]
>Azure SQL Database では、プライマリ ファイル グループのみがサポートされます。  

 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql
CREATE PARTITION SCHEME partition_scheme_name  
AS PARTITION partition_function_name  
[ ALL ] TO ( { file_group_name | [ PRIMARY ] } [ ,...n ] )  
[ ; ]  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
 *partition_scheme_name*  
 パーティション構成の名前です。 パーティション構成の名前は、データベース内で一意であり、かつ[識別子](../../relational-databases/databases/database-identifiers.md)のルールに従っている必要があります。  
  
 *partition_function_name*  
 パーティション構成を使用するパーティション関数の名前です。 パーティション関数によって作成されたパーティションは、パーティション構成で指定されたファイル グループにマップされます。 *partition_function_name* はデータベースに既に存在する必要があります。 1 つのパーティションに FILESTREAM ファイル グループと非 FILESTREAM ファイル グループの両方を含めることはできません。  
  
 ALL  
 すべてのパーティションを *file_group_name* で提供されるファイル グループにマップすることを指定します。**[** PRIMARY **]** を指定した場合は、すべてのパーティションをプライマリ ファイル グループにマップすることを指定します。 ALL を指定した場合は、指定できる *file_group_name* は 1 つだけです。  
  
 *file_group_name* |  **[** PRIMARY **]** [ **,** _...n_]  
 *partition_function_name* によって指定されたパーティションを保持するファイル グループの名前を指定します。 *file_group_name* がデータベースに既に存在する必要があります。  
  
 **[** PRIMARY **]** を指定した場合、パーティションはプライマリ ファイル グループに格納されます。 ALL を指定した場合は、指定できる *file_group_name* は 1 つだけです。 パーティションは、パーティション 1 から始まり、[ **,** _...n_] で一覧表示されているファイル グループの順序で、ファイル グループに割り当てられます。 [ **,** _...n_] では、同じ *file_group_name* を複数回指定できます。 *n* が *partition_function_name* で指定されたパーティションの数を保持するのに十分ではない場合、CREATE PARTITION SCHEME は失敗し、エラーが発生します。  
  
 *partition_function_name* によって生成されるパーティションの数がファイル グループより少ない場合、割り当てられていない最初のファイル グループが NEXT USED とマークされ、情報メッセージに NEXT USED ファイル グループの名前が表示されます。 ALL を指定した場合、唯一の *file_group_name* に、この *partition_function_name* に対する NEXT USED プロパティが設定されます。 ALTER PARTITION FUNCTION ステートメントで追加のパーティションを作成した場合は、NEXT USED ファイル グループがそのパーティションを受け取ります。 追加の割り当てられていないファイル グループを作成して新しいパーティションを保持するには、ALTER PARTITION SCHEME を使用します。  
  
 *file_group_name* [ 1 **,** _...n_] でプライマリ ファイル グループを指定するときは、PRIMARY を **[** PRIMARY **]** のように区切る必要があります。これは、PRIMARY がキーワードであるためです。  
  
 [!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)] では PRIMARY のみサポートされます。 後半の例 E をご覧ください。 
  
## <a name="permissions"></a>アクセス許可  
 CREATE PARTITION SCHEME を実行する場合、下記の権限を使用することができます。  
  
-   ALTER ANY DATASPACE 権限。 この権限は、既定では **sysadmin** 固定サーバー ロール、 **db_owner** 固定データベース ロール、および **db_ddladmin** 固定データベース ロールのメンバーに与えられています。  
  
-   データベースの CONTROL 権限または ALTER 権限 (パーティション構成はこのデータベース内で作成)。  
  
-   データベースのサーバーの CONTROL SERVER 権限または ALTER ANY DATABASE 権限 (パーティション構成はこのデータベースで作成)。  
  
## <a name="examples"></a>例  
  
### <a name="a-creating-a-partition-scheme-that-maps-each-partition-to-a-different-filegroup"></a>A. 各パーティションを異なるファイル グループにマップするパーティション構成を作成する  
 次の例では、テーブルまたはインデックスを 4 つのパーティションに分割するパーティション関数を作成します。 その後、4 つのパーティションをそれぞれ保持するファイル グループを指定するパーティション構成を作成します。 この例では、ファイル グループが既にデータベースに存在していると仮定しています。  
  
```sql  
CREATE PARTITION FUNCTION myRangePF1 (INT)  
AS RANGE LEFT FOR VALUES (1, 100, 1000);  
GO  
CREATE PARTITION SCHEME myRangePS1  
AS PARTITION myRangePF1  
TO (test1fg, test2fg, test3fg, test4fg);  
```  
  
パーティション分割列 **col1** でパーティション関数 `myRangePF1` を使用するテーブルのパーティションは、次の表に示すように割り当てられます。  

|[ファイル グループ]|パーティション|値|
|-|-|-|
|`test1fg`|1|**col1** <= `1`|
|`test2fg`|2|**col1** > `1` AND **col1** <= `100`|
|`test3fg`|3|**col1** > `100` AND **col1** <= `1000`|
|`test4fg`|4|**col1** > `1000`|
  
### <a name="b-creating-a-partition-scheme-that-maps-multiple-partitions-to-the-same-filegroup"></a>B. 複数のパーティションを同じファイル グループにマップするパーティション構成を作成する  
 すべてのパーティションを同じファイル グループにマップする場合は、ALL キーワードを使用します。 ただし、複数の (すべてではない) パーティションを同じファイル グループにマップする場合は、次の例に示すように、ファイル グループ名を繰り返す必要があります。  
  
```sql  
CREATE PARTITION FUNCTION myRangePF2 (INT)  
AS RANGE LEFT FOR VALUES (1, 100, 1000);  
GO  
CREATE PARTITION SCHEME myRangePS2  
AS PARTITION myRangePF2  
TO ( test1fg, test1fg, test1fg, test2fg );  
```  
  
 パーティション分割列 **col1** でパーティション関数 `myRangePF2` を使用するテーブルのパーティションは、次の表に示すように割り当てられます。  
  
|[ファイル グループ]|パーティション|値|
|-|-|-|
|`test1fg`|1|**col1** <= `1`|
|`test1fg`|2|**col1** > `1` AND **col1** <= `100`|
|`test1fg`|3|**col1** > `100` AND **col1** <= `1000`|
|`test2fg`|4|**col1** > `1000`|

### <a name="c-creating-a-partition-scheme-that-maps-all-partitions-to-the-same-filegroup"></a>C. すべてのパーティションを同じファイル グループにマップするパーティション構成を作成する  
 次の例では、これまでの例と同じパーティション関数を作成し、すべてのパーティションを同じファイル グループにマップするパーティション構成を作成します。  
  
```sql  
CREATE PARTITION FUNCTION myRangePF3 (INT)  
AS RANGE LEFT FOR VALUES (1, 100, 1000);  
GO  
CREATE PARTITION SCHEME myRangePS3  
AS PARTITION myRangePF3  
ALL TO ( test1fg );  
```  
  
### <a name="d-creating-a-partition-scheme-that-specifies-a-next-used-filegroup"></a>D. 'NEXT USED' ファイル グループを指定するパーティション構成を作成する  
 次の例では、これまでの例と同じパーティション関数を作成し、関連するパーティション関数によって作成されるパーティションよりも多くのファイル グループを一覧するパーティション構成を作成します。  
  
```sql  
CREATE PARTITION FUNCTION myRangePF4 (INT)  
AS RANGE LEFT FOR VALUES (1, 100, 1000);  
GO  
CREATE PARTITION SCHEME myRangePS4  
AS PARTITION myRangePF4  
TO (test1fg, test2fg, test3fg, test4fg, test5fg)  
```  
  
 このステートメントを実行すると、次のメッセージが返されます。  
  
パーティション構成 'myRangePS4' が正しく作成されました。 'test5fg' は、パーティション構成 'myRangePS4' で次に使用されるファイル グループに設定されています。  
  
  
 パーティション関数 `myRangePF4` を変更してパーティションを追加すると、ファイル グループ `test5fg` は新たに作成されたパーティションを受け取ります。  

### <a name="e-creating-a-partition-scheme-only-on-primary"></a>E. PRIMARY にのみパーティション構成を作成する

 Azure [!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)] では、ファイルとファイル グループの追加はサポートされていませんが、PRIMARY ファイル グループのみをパーティション分割するテーブルのパーティション分割はサポートされています。 

次の例では、テーブルまたはインデックスを 4 つのパーティションに分割するパーティション関数を作成します。 すべてのパーティションが PRIMARY ファイル グループに作成されることを指定するパーティション構成が作成されます。  
  
```sql  
CREATE PARTITION FUNCTION myRangePF1 (INT)  
AS RANGE LEFT FOR VALUES (1, 100, 1000);  
GO  
CREATE PARTITION SCHEME myRangePS1  
AS PARTITION myRangePF1  
ALL TO ( [PRIMARY] );  
```
   
## <a name="see-also"></a>関連項目  
 [CREATE PARTITION FUNCTION &#40;Transact-SQL&#41;](../../t-sql/statements/create-partition-function-transact-sql.md)   
 [ALTER PARTITION SCHEME &#40;Transact-SQL&#41;](../../t-sql/statements/alter-partition-scheme-transact-sql.md)   
 [DROP PARTITION SCHEME &#40;Transact-SQL&#41;](../../t-sql/statements/drop-partition-scheme-transact-sql.md)   
 [EVENTDATA &#40;Transact-SQL&#41;](../../t-sql/functions/eventdata-transact-sql.md)   
 [パーティション テーブルとパーティション インデックスの作成](../../relational-databases/partitions/create-partitioned-tables-and-indexes.md)   
 [sys.partition_schemes &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-partition-schemes-transact-sql.md)   
 [sys.data_spaces &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-data-spaces-transact-sql.md)   
 [sys.destination_data_spaces &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-destination-data-spaces-transact-sql.md)   
 [sys.partitions &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-partitions-transact-sql.md)   
 [sys.tables &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-tables-transact-sql.md)   
 [sys.indexes &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-indexes-transact-sql.md)   
 [sys.index_columns &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-index-columns-transact-sql.md)  
  
  
