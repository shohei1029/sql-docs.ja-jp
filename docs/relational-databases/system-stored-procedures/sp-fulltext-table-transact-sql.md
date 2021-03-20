---
description: sp_fulltext_table (Transact-SQL)
title: sp_fulltext_table (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- sp_fulltext_table_TSQL
- sp_fulltext_table
dev_langs:
- TSQL
helpviewer_keywords:
- sp_fulltext_table
ms.assetid: a765f311-07fc-4af3-b74c-e9a027fbecce
author: markingmyname
ms.author: maghan
monikerRange: =azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 7184d4d9fae78821c0558ae193e7a830f6f28f28
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104749962"
---
# <a name="sp_fulltext_table-transact-sql"></a>sp_fulltext_table (Transact-SQL)
[!INCLUDE[tsql-appliesto-ss2008-xxxx-asdw-xxx-md](../../includes/tsql-appliesto-ss2008-xxxx-asdw-xxx-md.md)]

  フルテキストインデックスを作成するテーブルをマークまたは非表示にします。  
  
> [!IMPORTANT]  
>  [!INCLUDE[ssNoteDepFutureAvoid](../../includes/ssnotedepfutureavoid-md.md)] 代わりに、フル [テキストインデックスの作成](../../t-sql/statements/create-fulltext-index-transact-sql.md)、フルテキストインデックスの [変更](../../t-sql/statements/alter-fulltext-index-transact-sql.md)、および [フルテキスト](../../t-sql/statements/drop-fulltext-index-transact-sql.md) インデックスの削除を使用します。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```  
  
sp_fulltext_table   
   [ @tabname= ] 'qualified_table_name'           
   , [ @action= ] 'action'   
   [   
   , [ @ftcat= ] 'fulltext_catalog_name'           
   , [ @keyname= ] 'unique_index_name'   
   ]  
```  
  
## <a name="arguments"></a>引数  
`[ @tabname = ] 'qualified_table_name'` 1つまたは2つの要素で構成されるテーブル名を指定します。 テーブルは、現在のデータベース内に存在している必要があります。 *qualified_table_name* は **nvarchar (517)**,、既定値はありません。  
  
`[ @action = ] 'action'` 実行するアクションを指定します。 *アクション* は **nvarchar (50)**,、既定値はありませんが、これらの値のいずれかを指定することができます。  
  
|値|説明|  
|-----------|-----------------|  
|**作成**|*Qualified_table_name* によって参照されるテーブルのフルテキストインデックスのメタデータを作成し、このテーブルのフルテキストインデックスデータが *fulltext_catalog_name* に存在する必要があることを指定します。 この操作では、フルテキストキー列として *unique_index_name* を使用することも指定します。 この一意なインデックスは既に存在していて、テーブル内の列に定義しておく必要があります。<br /><br /> フルテキストカタログが設定されるまで、このテーブルに対してフルテキスト検索を実行することはできません。|  
|**」**|*Qualified_table_name* のフルテキストインデックスのメタデータを削除します。 フルテキストインデックスがアクティブな場合は、自動的に非アクティブ化されてから削除されます。 フルテキスト インデックスを削除する前に、列を削除する必要はありません。|  
|**アクティブ化**|非アクティブ化された後、 *qualified_table_name* に対してフルテキストインデックスデータを収集する機能をアクティブにします。 フルテキスト インデックスをアクティブにするには、フルテキスト インデックスの対象となる列が少なくとも 1 つ必要です。<br /><br /> フルテキスト インデックスは、インデックス作成用の最初の列が追加されるとすぐに自動的にアクティブになり、作成を開始できるようになります。 最後の列がインデックスから削除されると、インデックスは非アクティブになります。 変更の追跡がオンの場合、非アクティブなインデックスをアクティブにすると、新しく作成が開始されます。<br /><br /> この方法では、実際にフルテキストインデックスが設定されるわけではありませんが、次のフルテキストインデックスの作成時に *qualified_table_name* の行を取得できるように、ファイルシステムのフルテキストカタログにテーブルを登録するだけです。|  
|**非**|*Qualified_table_name* のフルテキストインデックスを非アクティブ化して、 *qualified_table_name* に対してフルテキストインデックスデータを収集できないようにします。 フルテキスト インデックス メタデータはそのまま残り、テーブルは再びアクティブにできます。<br /><br /> 変更の追跡がオンになっている場合、アクティブなインデックスを非アクティブにすると、インデックス作成は停止します。つまり、実行中の作成操作は停止し、インデックスは変更されなくなります。|  
|**start_change_tracking**|フルテキストインデックスの増分作成を開始します。 テーブルが timestamp 型でない場合は、フルテキスト インデックスの完全作成を開始します。 テーブルに対する変更の追跡を開始します。<br /><br /> フルテキスト変更の追跡では、 **image**、 **text**、または **ntext** 型のフルテキストインデックス列に対して実行される WRITETEXT または UPDATETEXT 操作は追跡されません。|  
|**stop_change_tracking**|テーブルに対する変更の追跡を停止します。|  
|**update_index**|現在の追跡された変更のセットをフルテキストインデックスに反映します。|  
|**Start_background_updateindex**|変更の追跡中に発生した変更を、発生した時点でフルテキスト インデックスに反映します。|  
|**Stop_background_updateindex**|追跡した変更が発生したときにフルテキストインデックスに反映されないようにします。|  
|**start_full**|テーブルのフルテキストインデックスの完全作成を開始します。|  
|**start_incremental**|テーブルのフルテキストインデックスの増分作成を開始します。|  
|**Stop**|完全作成または増分作成を停止します。|  
  
`[ @ftcat = ] 'fulltext_catalog_name'`**Create** アクションの有効な既存のフルテキストカタログ名を指定します。 それ以外のすべてのアクションについては、このパラメーターを NULL にする必要があります。 *fulltext_catalog_name* は **sysname**,、既定値は NULL です。  
  
`[ @keyname = ] 'unique_index_name'`**Create** アクションの *qualified_table_name* 上の有効な単一キー列の一意の null 値インデックスです。 それ以外のすべてのアクションについては、このパラメーターを NULL にする必要があります。 *unique_index_name* は **sysname**,、既定値は NULL です。  
  
## <a name="return-code-values"></a>リターン コードの値  
 0 (成功) または 1 (失敗)  
  
## <a name="result-sets"></a>結果セット  
 なし  
  
## <a name="remarks"></a>解説  
 特定のテーブルに対してフルテキストインデックスを非アクティブ化した後、既存のフルテキストインデックスは、次の完全作成まで保持されます。ただし、では、非アクティブ化された [!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] テーブルに対するクエリがブロックされるため、このインデックスは使用されません。  
  
 テーブルが再アクティブ化され、インデックスが再作成されなかった場合でも、新しいフルテキストが有効になっている以外のすべての列に対するクエリでは、古いインデックスが引き続き使用できます。 削除された列のデータは、フルテキスト列検索をすべて指定するクエリで照合されます。  
  
 テーブルがフルテキストインデックス作成用に定義された後、フルテキストの一意キー列をあるデータ型から別のデータ型に切り替えると、その列のデータ型を変更するか、またはフルテキストの一意キーをある列から別の列に変更することによって、次のクエリの実行中にエラーが発生し、エラーメッセージ "*フル**テキスト* 検索キーの値に対する変換に失敗しました data_type key_value これを回避するには、 **sp_fulltext_table** の **drop** アクションを使用してこのテーブルのフルテキスト定義を削除し、 **sp_fulltext_table** と **sp_fulltext_column** を使用して再定義します。  
  
 フルテキストキー列は900バイト以下で定義する必要があります。 パフォーマンス上の理由から、キー列のサイズはできる限り小さくすることをお勧めします。  
  
## <a name="permissions"></a>権限  
 **Sysadmin** 固定サーバーロール、 **db_owner** および **db_ddladmin** 固定データベースロールのメンバー、またはフルテキストカタログに対する参照権限を持つユーザーだけが **sp_fulltext_table** を実行できます。  
  
## <a name="examples"></a>例  
  
### <a name="a-enabling-a-table-for-full-text-indexing"></a>A. フルテキスト インデックスに対してテーブルを有効にする  
 次の例では、データベースのテーブルのフルテキストインデックスメタデータを作成し `Document` `AdventureWorks` ます。 `Cat_Desc` フルテキストカタログです。 `PK_Document_DocumentID` は `Document` の一意な単一列のインデックスです。  
  
```  
USE AdventureWorks2012;  
GO  
EXEC sp_fulltext_table 'Production.Document', 'create', 'Cat_Desc', 'PK_Document_DocumentID';  
--Add some columns  
EXEC sp_fulltext_column 'Production.Document','DocumentSummary','add';  
-- Activate the full-text index  
EXEC sp_fulltext_table 'Production.Document','activate';  
GO  
```  
  
### <a name="b-activating-and-propagating-track-changes"></a>B. 変更の追跡をアクティブにして変更を反映する  
 次の例では、変更の追跡をアクティブにし、発生した変更を直ちにフルテキスト インデックスに反映します。  
  
```  
USE AdventureWorks2012;  
GO  
EXEC sp_fulltext_table 'Production.Document', 'Start_change_tracking';  
EXEC sp_fulltext_table 'Production.Document', 'Start_background_updateindex';  
GO  
```  
  
### <a name="c-removing-a-full-text-index"></a>C. フルテキストインデックスの削除  
 次の例では、`Document` データベースの `AdventureWorks` テーブルに対する、フルテキスト インデックス メタデータを削除します。  
  
```  
USE AdventureWorks2012;  
GO  
EXEC sp_fulltext_table 'Production.Document', 'drop';  
GO  
```  
  
## <a name="see-also"></a>参照  
 [INDEXPROPERTY &#40;Transact-SQL&#41;](../../t-sql/functions/indexproperty-transact-sql.md)   
 [OBJECTPROPERTY &#40;Transact-SQL&#41;](../../t-sql/functions/objectproperty-transact-sql.md)   
 [sp_help_fulltext_tables &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-help-fulltext-tables-transact-sql.md)   
 [sp_help_fulltext_tables_cursor &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-help-fulltext-tables-cursor-transact-sql.md)   
 [sp_helpindex &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-helpindex-transact-sql.md)   
 [システム ストアド プロシージャ &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/system-stored-procedures-transact-sql.md)   
 [Transact-sql&#41;&#40;のフルテキスト検索およびセマンティック検索ストアドプロシージャ ](../../relational-databases/system-stored-procedures/full-text-search-and-semantic-search-stored-procedures-transact-sql.md)  
  
  
