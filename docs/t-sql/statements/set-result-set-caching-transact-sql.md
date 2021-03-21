---
description: SET RESULT_SET_CACHING (Transact-SQL)
title: RESULT_SET_CACHING の設定 (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 04/16/2020
ms.prod: sql
ms.prod_service: synapse-analytics
ms.reviewer: jrasnick
ms.technology: t-sql
ms.topic: reference
f1_keywords: ''
dev_langs:
- TSQL
helpviewer_keywords: ''
author: XiaoyuMSFT
ms.author: xiaoyul
monikerRange: =azure-sqldw-latest
ms.openlocfilehash: 035030880a8c4b609bf430c2598cb2b778dedbda
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104737832"
---
# <a name="set-result-set-caching-transact-sql"></a>結果セット キャッシュの設定 (Transact-SQL) 

[!INCLUDE [asa](../../includes/applies-to-version/asa.md)]

現在のクライアント セッションの結果セットのキャッシュ動作を制御します。  

[!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] への適用  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文

```syntaxsql
SET RESULT_SET_CACHING { ON | OFF };
```  

[!INCLUDE[synapse-analytics-od-unsupported-syntax](../../includes/synapse-analytics-od-unsupported-syntax.md)]

## <a name="remarks"></a>解説  

result_set_caching の設定を構成するユーザー データベースに接続した状態で、このコマンドを実行します。

**ON**   
現在のクライアント セッションの結果セットのキャッシュを有効にします。  結果セットのキャッシュがデータベース レベルで OFF にされていた場合、これをセッションで ON にすることはできません。

**OFF**   
現在のクライアント セッションの結果セットのキャッシュを無効にします。

## <a name="examples"></a>例

クエリの request_id を指定して [sys.dm_pdw_exec_requests](../../relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql.md) 内の result_cache_hit 列をクエリし、このクエリの実行時に、結果のキャッシュ ヒットおよびキャッシュ ミスのいずれが使用されたかを確認します。

```sql
SELECT result_cache_hit
FROM sys.dm_pdw_exec_requests
WHERE request_id = 'QID58286'
```

## <a name="permissions"></a>アクセス許可

public ロールのメンバーシップが必要です

## <a name="see-also"></a>参照

- [結果セットのキャッシュを使用したパフォーマンスのチューニング](/azure/sql-data-warehouse/performance-tuning-result-set-caching)
- [ALTER DATABASE SET のオプション &#40;Transact-SQL&#41;](./alter-database-transact-sql-set-options.md?preserve-view=true&view=azure-sqldw-latest&preserve-view=true)
- [ALTER DATABASE &#40;Transact-SQL&#41;](./alter-database-transact-sql.md?preserve-view=true&view=azure-sqldw-latest&preserve-view=true)
- [DBCC SHOWRESULTCACHESPACEUSED (Transact-SQL)](../database-console-commands/dbcc-showresultcachespaceused-transact-sql.md)
- [DBCC DROPRESULTSETCACHE (Transact-SQL)](../database-console-commands/dbcc-dropresultsetcache-transact-sql.md)