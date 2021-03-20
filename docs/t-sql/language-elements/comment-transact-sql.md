---
description: -- (コメント) (Transact-SQL)
title: -- (コメント) (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 07/25/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- --_TSQL
- Comment
- --
dev_langs:
- TSQL
helpviewer_keywords:
- nonexecuting text strings [SQL Server]
- remarks [SQL Server]
- -- (comment character)
- comments [SQL Server]
ms.assetid: 676ea8c2-52c1-4ef6-9354-320f1a091153
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 98fd3d266d2ae6b9fd2d7f8ae6d7cdb786cc4c04
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104752962"
---
# <a name="---comment-transact-sql"></a>-- (コメント) (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  ユーザーが入力したテキストを示します。 コメントは、単独行に指定したり、[!INCLUDE[tsql](../../includes/tsql-md.md)] コマンド ラインの終端で入れ子にしたり、または、[!INCLUDE[tsql](../../includes/tsql-md.md)] ステートメントの中に指定したりすることができます。 サーバーはコメントを評価しません。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql
-- text_of_comment  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
 *text_of_comment*  
 コメントのテキストを表す文字列です。  
  
## <a name="remarks"></a>注釈  
1 行のコメントまたは入れ子にしたコメントには、2 つのハイフン ( **--** ) を使用します。 **--** によって挿入された コメントは、復帰文字 (U+000A)、改行文字 (U+000D)、またはその 2 つの組み合わせで指定された改行で終了します。 コメントの長さには制限がありません。 次の表に、テキストのコメント化/コメント解除に使用できるキーボード ショートカットを示します。
  
|アクション|Standard|  
|------------|--------------|  
|選択したテキストをコメント化する|Ctrl + K、Ctrl + C|  
|選択したテキストのコメント化を解除する|Ctrl + K、Ctrl + U|  
  
 キーボード ショートカットの詳細については、「[SQL Server Management Studio のキーボード ショートカット](../../ssms/sql-server-management-studio-keyboard-shortcuts.md)」を参照してください。  
  
 複数行のコメントについては、「[スラッシュ スター &#40;ブロック コメント&#41; &#40;Transact-SQL&#41;](../../t-sql/language-elements/slash-star-comment-transact-sql.md)」を参照してください。  
  
## <a name="examples"></a>例  
 次の例では、-- コメント文字を使用します。  
  
```sql  
-- Choose the AdventureWorks2012 database.  
USE AdventureWorks2012;  
GO  
-- Choose all columns and all rows from the Address table.  
SELECT *  
FROM Person.Address  
ORDER BY PostalCode ASC; -- We do not have to specify ASC because   
-- that is the default.  
GO  
```  
  
## <a name="see-also"></a>関連項目  
 [フロー制御言語 &#40;Transact-SQL&#41;](~/t-sql/language-elements/control-of-flow.md)  
  
  
