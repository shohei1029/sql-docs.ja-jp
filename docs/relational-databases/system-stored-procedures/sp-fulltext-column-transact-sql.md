---
description: sp_fulltext_column (Transact-sql)
title: sp_fulltext_column (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- sp_fulltext_column_TSQL
- sp_fulltext_column
dev_langs:
- TSQL
helpviewer_keywords:
- sp_fulltext_column
ms.assetid: a84cc45d-1b50-44af-85df-2ea033b8a6a9
author: markingmyname
ms.author: maghan
monikerRange: =azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: a3f8b83ec0ad778269e677144912ef7e82480aad
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104749982"
---
# <a name="sp_fulltext_column-transact-sql"></a>sp_fulltext_column (Transact-sql)
[!INCLUDE[tsql-appliesto-ss2008-xxxx-asdw-xxx-md](../../includes/tsql-appliesto-ss2008-xxxx-asdw-xxx-md.md)]

  テーブルの特定の列がフルテキストインデックスに参加するかどうかを指定します。  
  
> [!IMPORTANT]  
>  [!INCLUDE[ssNoteDepFutureAvoid](../../includes/ssnotedepfutureavoid-md.md)] 代わりに、 [ALTER フルテキストインデックス](../../t-sql/statements/alter-fulltext-index-transact-sql.md) を使用してください。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```  
  
sp_fulltext_column [ @tabname= ] 'qualified_table_name' ,   
     [ @colname= ] 'column_name' ,   
     [ @action= ] 'action'   
     [ , [ @language= ] 'language_term' ]   
     [ , [ @type_colname= ] 'type_column_name' ]  
```  
  
## <a name="arguments"></a>引数  
`[ @tabname = ] 'qualified_table_name'` 1つまたは2つの要素で構成されるテーブル名を指定します。 テーブルは、現在のデータベース内に存在している必要があります。 テーブルにフルテキスト インデックスがある。 *qualified_table_name* は **nvarchar (517)** で、既定値はありません。  
  
`[ @colname = ] 'column_name'`*Qualified_table_name* 内の列の名前を指定します。 列には、文字、 **varbinary (max)** 、または **image** 型の列を指定する必要があり、計算列にすることはできません。 *column_name* は **sysname** であり、既定値はありません。  
  
> [!NOTE]  
>  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、 **varbinary (max)** または **image** データ型の列に格納されているテキストデータのフルテキストインデックスを作成できます。 画像と画像にはインデックスが付けられません。  
  
`[ @action = ] 'action'` 実行するアクションを指定します。 *アクション* は **varchar (20)**,、既定値はありません、次の値のいずれかを指定することができます。  
  
|値|説明|  
|-----------|-----------------|  
|**add**|テーブルの非アクティブなフルテキストインデックスに *qualified_table_name* の *column_name* を追加します。 この操作により、列でフルテキストインデックスを作成できるようになります。|  
|**」**|テーブルの非アクティブなフルテキストインデックスから *qualified_table_name* の *column_name* を削除します。|  
  
`[ @language = ] 'language_term'` 列に格納されているデータの言語を示します。 に含まれる言語の一覧につい [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ては、「 [Sys.fulltext_languages &#40;transact-sql&#41;](../../relational-databases/system-catalog-views/sys-fulltext-languages-transact-sql.md)」を参照してください。  
  
> [!NOTE]  
>  列に複数の言語またはサポートされていない言語のデータが含まれている場合は、' ニュートラル ' を使用してください。 既定値は、構成オプション ' default full text language ' によって指定されます。  
  
`[ @type_colname = ] 'type_column_name'`*Column_name* のドキュメント型を保持する *qualified_table_name* 内の列の名前を指定します。 この列は **char**、 **nchar**、 **varchar**、または **nvarchar** である必要があります。 *Column_name* のデータ型が **varbinary (max)** または **image** 型の場合にのみ使用されます。 *type_column_name* は **sysname** であり、既定値はありません。  
  
## <a name="return-code-values"></a>リターン コードの値  
 0 (成功) または 1 (失敗)  
  
## <a name="result-sets"></a>結果セット  
 なし  
  
## <a name="remarks"></a>解説  
 フルテキストインデックスがアクティブな場合、実行中の作成は停止されます。 また、アクティブなフルテキスト インデックスを持つテーブルで変更の追跡が有効になっている場合、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では現在のインデックスが維持されます。 たとえば [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、あるテーブルに対して現在行われているインデックス作成がすべて停止され、既存のインデックスの削除、および新しいインデックスの作成が開始されます。  
  
 変更の追跡がオンになっていて、インデックスを保持しながら列をフルテキストインデックスから追加または削除する必要がある場合は、テーブルを非アクティブ化し、必要な列を追加または削除する必要があります。 この操作では、インデックスは変化しません。 作成を開始することが実用的な場合は、テーブルを後でアクティブにすることができます。  
  
## <a name="permissions"></a>権限  
 ユーザーは、 **db_ddladmin** 固定データベースロールのメンバーであるか、 **db_owner** 固定データベースロールのメンバーであるか、テーブルの所有者である必要があります。  
  
## <a name="examples"></a>例  
 次の例では、`DocumentSummary` テーブルの `Document` 列を、テーブルのフルテキスト インデックスに追加します。  
  
```  
USE AdventureWorks2012;  
GO  
EXEC sp_fulltext_column 'Production.Document', DocumentSummary, 'add';  
GO  
```  
  
 次の例では、`spanishTbl` というテーブルにフルテキスト インデックスが作成されていることを前提としています。 `spanishCol`列をフルテキストインデックスに追加するには、次のストアドプロシージャを実行します。  
  
```  
EXEC sp_fulltext_column 'spanishTbl', 'spanishCol', 'add', 0xC0A;  
GO  
```  
  
 このクエリを実行すると、次のようになります。  
  
```  
SELECT *   
FROM spanishTbl   
WHERE CONTAINS(spanishCol, 'formsof(inflectional, trabajar)')  
```  
  
 結果セットには、、、など、さまざまな形式の行が含まれ `trabajar` `trabajo` `trabajamos` `trabajan` ます。  
  
> [!NOTE]  
>  1つのフルテキストクエリ関数句に示されているすべての列は、同じ言語を使用する必要があります。  
  
## <a name="see-also"></a>参照  
 [OBJECTPROPERTY &#40;Transact-SQL&#41;](../../t-sql/functions/objectproperty-transact-sql.md)   
 [sp_help_fulltext_columns &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-help-fulltext-columns-transact-sql.md)   
 [sp_help_fulltext_columns_cursor &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-help-fulltext-columns-cursor-transact-sql.md)   
 [sp_help_fulltext_tables &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-help-fulltext-tables-transact-sql.md)   
 [sp_help_fulltext_tables_cursor &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-help-fulltext-tables-cursor-transact-sql.md)   
 [システム ストアド プロシージャ &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/system-stored-procedures-transact-sql.md)   
 [Transact-sql&#41;&#40;のフルテキスト検索およびセマンティック検索ストアドプロシージャ ](../../relational-databases/system-stored-procedures/full-text-search-and-semantic-search-stored-procedures-transact-sql.md)  
  
  
