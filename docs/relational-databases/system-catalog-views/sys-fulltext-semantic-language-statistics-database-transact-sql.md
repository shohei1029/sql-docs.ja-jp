---
description: sys.fulltext_semantic_language_statistics_database (Transact-sql)
title: sys.fulltext_semantic_language_statistics_database (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.prod_service: database-engine
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- sys.fulltext_semantic_language_statistics_database_TSQL
- fulltext_semantic_language_statistics_database_TSQL
- fulltext_semantic_language_statistics_database
- sys.fulltext_semantic_language_statistics_database
dev_langs:
- TSQL
helpviewer_keywords:
- sys.fulltext_semantic_language_statistics_database catalog view
ms.assetid: 32e95614-ed88-4068-8c37-1e21544717bc
author: pmasl
ms.author: pelopes
ms.reviewer: mikeray
ms.openlocfilehash: 9a2cf710611007080c9b00932b59141553151131
ms.sourcegitcommit: 295b9dfc758471ef7d238a2b0f92f93e34acbb1b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106054673"
---
# <a name="sysfulltext_semantic_language_statistics_database-transact-sql"></a>sys.fulltext_semantic_language_statistics_database (Transact-sql)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  の現在のインスタンスにインストールされているセマンティック言語統計データベースに関する行を返し [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。  
  
 このビューに対してクエリを実行すると、セマンティック処理に必要なセマンティック言語統計コンポーネントについて調べることができます。  
   
  
|**列名**|**Type**|**説明**|  
|-|-|-|  
|**database_id**|**int**|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンス内で一意のデータベースの ID です。|  
|**register_date**|**datetime**|データベースがセマンティック処理用に登録された日付。|  
|**registered_by**|**int**|セマンティック処理のためにデータベースを登録したサーバープリンシパルの ID。|  
|**version**|**nvarchar(128)**|セマンティック言語統計データベースに固有の最新バージョン情報。|  
  
## <a name="general-remarks"></a>全般的な解説  
 詳細については、「 [セマンティック検索のインストールと構成](../../relational-databases/search/install-and-configure-semantic-search.md)」を参照してください。  
  
## <a name="metadata"></a>Metadata  
 セマンティックインデックス作成でサポートされている言語の詳細については、カタログビューに対してクエリを実行 [sys.fulltext_semantic_languages &#40;transact-sql&#41;](../../relational-databases/system-catalog-views/sys-fulltext-semantic-languages-transact-sql.md)を参照してください。  
  
## <a name="security"></a>Security  
  
### <a name="permissions"></a>アクセス許可  
 カタログ ビューでのメタデータの表示が、ユーザーが所有しているかそのユーザーが権限を許可されている、セキュリティ保護可能なメタデータに制限されます。  
  
## <a name="examples"></a>例  
 次の例では、 **sys.fulltext_semantic_language_statistics_database** に対してクエリを実行し、の現在のインスタンスに登録されているセマンティック言語統計データベースに関する情報を取得する方法を示し [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。  
  
```  
SELECT * FROM sys.fulltext_semantic_language_statistics_database;  
GO  
```  
  
## <a name="see-also"></a>参照  
 [セマンティック検索のインストールと構成](../../relational-databases/search/install-and-configure-semantic-search.md)  
  
  
