---
description: sp_pdw_remove_network_credentials (Azure Synapse Analytics)
title: sp_pdw_remove_network_credentials
titleSuffix: Azure Synapse Analytics
ms.date: 03/14/2017
ms.prod_service: synapse-analytics, pdw
ms.reviewer: ''
ms.service: sql-data-warehouse
ms.subservice: design
ms.topic: reference
dev_langs:
- TSQL
ms.assetid: c12696a2-5939-402b-9866-8a837ca4c0a3
author: ronortloff
ms.author: rortloff
monikerRange: '>= aps-pdw-2016 || = azure-sqldw-latest'
ms.custom: seo-dt-2019
ms.openlocfilehash: 1007555e8c4b16ded12a4d6ebb17b85c8f794711
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104739022"
---
# <a name="sp_pdw_remove_network_credentials-azure-synapse-analytics"></a>sp_pdw_remove_network_credentials (Azure Synapse Analytics)
[!INCLUDE[applies-to-version/asa-pdw](../../includes/applies-to-version/asa-pdw.md)]

  ネットワークファイル共有にアクセスするために、に格納されているネットワーク資格情報 [!INCLUDE[ssSDW](../../includes/sssdw-md.md)] を削除します。 たとえば、このストアドプロシージャを使用して、 [!INCLUDE[ssSDW](../../includes/sssdw-md.md)] 独自のネットワーク内に存在するサーバーでバックアップ操作と復元操作を実行するための権限を削除します。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則 (Transact-SQL)](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql  
-- Syntax for Azure Synapse Analytics and Parallel Data Warehouse  
  
sp_pdw_remove_network_credentials 'target_server_name'  
```

[!INCLUDE[synapse-analytics-od-unsupported-syntax](../../includes/synapse-analytics-od-unsupported-syntax.md)]

## <a name="arguments"></a>引数  
 '*target_server_name*'  
 対象サーバーのホスト名または IP アドレスを指定します。 このサーバーにアクセスするための資格情報はから削除され [!INCLUDE[ssSDW](../../includes/sssdw-md.md)] ます。 これによって、自分のチームによって管理される実際の対象サーバーのアクセス許可が変更または削除されることはありません。  
  
 *target_server_name* は nvarchar (]) として定義されています。  
  
## <a name="return-code-values"></a>リターン コードの値  
 0 (成功) または 1 (失敗)  
  
## <a name="permissions"></a>権限  
 **ALTER SERVER STATE** 権限が必要です。  
  
## <a name="error-handling"></a>エラー処理  
 [制御] ノードおよびすべての計算ノードで資格情報の削除が成功しなかった場合に、エラーが発生します。  
  
## <a name="general-remarks"></a>全般的な解説  
 このストアドプロシージャは、の NetworkService アカウントからネットワーク資格情報を削除 [!INCLUDE[ssSDW](../../includes/sssdw-md.md)] します。 NetworkService アカウントは、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 制御ノードとコンピューティングノードで SMP の各インスタンスを実行します。 たとえば、バックアップ操作を実行すると、制御ノードと各コンピューティングノードは、NetworkService アカウントの資格情報を使用して対象サーバーにアクセスします。  
  
## <a name="metadata"></a>Metadata  
 すべての資格情報を一覧表示し、資格情報が削除されたことを確認するには、 [sys.dm_pdw_network_credentials &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-pdw-network-credentials-transact-sql.md)を使用します。  
  
 資格情報を追加するには、 [Azure Synapse Analytics&#41;&#40;sp_pdw_add_network_credentials ](../../relational-databases/system-stored-procedures/sp-pdw-add-network-credentials-sql-data-warehouse.md)を使用します。  
  
## <a name="examples-sssdwfull-and-sspdw"></a>例: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)]、[!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="a-remove-credentials-for-performing-a-database-backup"></a>A. データベースバックアップを実行するための資格情報を削除する  
 次の例では、10.192.147.63 の IP アドレスを持つ対象サーバーにアクセスするためのユーザー名とパスワードの資格情報を削除します。  
  
```sql  
EXEC sp_pdw_remove_network_credentials '10.192.147.63';  
```  
  
  

