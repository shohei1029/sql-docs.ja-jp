---
description: sp_set_session_context (Transact-sql)
title: sp_set_session_context (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 05/14/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- sp_set_session_context
- sp_set_session_context_TSQL
- sys.sp_set_session_context
- sys.sp_set_session_context_TSQL
helpviewer_keywords:
- sp_set_session_context
ms.assetid: 7a3a3b2a-1408-4767-a376-c690e3c1fc5b
author: VanMSFT
ms.author: vanto
monikerRange: =azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: ff9f50aa953b0a7388156a9793d3ab1cbe819dc7
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104754632"
---
# <a name="sp_set_session_context-transact-sql"></a>sp_set_session_context (Transact-sql)
[!INCLUDE [sqlserver2016-asdb-asdbmi-asa](../../includes/applies-to-version/sqlserver2016-asdb-asdbmi-asa.md)]

セッションコンテキストでキーと値のペアを設定します。  
  

 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```  
sp_set_session_context [ @key= ] N'key', [ @value= ] 'value'  
    [ , [ @read_only = ] { 0 | 1 } ]  
[ ; ]  
```  
  
## <a name="arguments"></a>引数  
 [ @key =] N'key '  
 **Sysname** 型の設定対象のキー。 キーの最大サイズは128バイトです。  
  
 [ @value =] ' 値 '  
 **Sql_variant** 型の指定されたキーの値。 値を NULL に設定すると、メモリが解放されます。 最大サイズは 8,000 バイトです。  
  
 [ @read_only =] {0 | 1}  
 **ビット** 型のフラグです。 1の場合、指定されたキーの値は、この論理接続で再び変更することはできません。 0 (既定値) の場合は、値を変更できます。  
  
## <a name="permissions"></a>権限  
 すべてのユーザーは、セッションのコンテキストを設定できます。  
  
## <a name="remarks"></a>解説  
 他のストアドプロシージャと同様に、パラメーターとして渡すことができるのは、リテラルと変数 (式または関数呼び出しではない) だけです。  
  
 セッションコンテキストの合計サイズは 1 MB に制限されています。 この制限を超える値を設定すると、ステートメントは失敗します。 [Transact-sql&#41;&#40;sys.dm_os_memory_objects](../../relational-databases/system-dynamic-management-views/sys-dm-os-memory-objects-transact-sql.md)で、全体的なメモリ使用量を監視できます。  
  
 次のように [sys.dm_os_memory_cache_counters &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-os-memory-cache-counters-transact-sql.md) に対してクエリを実行することで、全体的なメモリ使用量を監視できます。 `SELECT * FROM sys.dm_os_memory_cache_counters WHERE type = 'CACHESTORE_SESSION_CONTEXT';`  
  
## <a name="examples"></a>例  
A. 次の例は、という名前のセッションコンテキストキーを設定して、値を英語で返す方法を示しています。  
  
```  
EXEC sys.sp_set_session_context @key = N'language', @value = 'English';  
SELECT SESSION_CONTEXT(N'language');  
```  
  
 次の例は、オプションの読み取り専用フラグの使用方法を示しています。  
  
```  
EXEC sys.sp_set_session_context @key = N'user_id', @value = 4, @read_only = 1;  
```  

B. 次の例は、値が12323ad の client_correlation_id という名前のセッションコンテキストキーを設定および取得する方法を示しています。
```
-- set value
EXEC sp_set_session_context 'client_correlation_id', '12323ad'; 

--check value
SELECT SESSION_CONTEXT(N'client_correlation_id');

```

## <a name="see-also"></a>参照  
 [CURRENT_TRANSACTION_ID &#40;Transact-SQL&#41;](../../t-sql/functions/current-transaction-id-transact-sql.md)   
 [SESSION_CONTEXT &#40;Transact-sql&#41;](../../t-sql/functions/session-context-transact-sql.md)   
 [行レベルのセキュリティ](../../relational-databases/security/row-level-security.md)   
 [CONTEXT_INFO  &#40;Transact-SQL&#41;](../../t-sql/functions/context-info-transact-sql.md)   
 [SET CONTEXT_INFO &#40;Transact-SQL&#41;](../../t-sql/statements/set-context-info-transact-sql.md)  
  
  
