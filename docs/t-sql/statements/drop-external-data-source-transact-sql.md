---
description: DROP EXTERNAL DATA SOURCE (Transact-SQL)
title: DROP EXTERNAL DATA SOURCE (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/03/2017
ms.prod: sql
ms.prod_service: synapse-analytics, pdw, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
dev_langs:
- TSQL
ms.assetid: 3f65a2f5-a6c6-4be5-8ca4-6057078fe10e
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: e1f5f4c99b6a5b5356115cc23a661de0e03c1eb9
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104753682"
---
# <a name="drop-external-data-source-transact-sql"></a>DROP EXTERNAL DATA SOURCE (Transact-SQL)
[!INCLUDE [sqlserver2016-asdbmi-asa-pdw](../../includes/applies-to-version/sqlserver2016-asdbmi-asa-pdw.md)]

  PolyBase の外部データ ソースを削除します。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql
-- Drop an external data source  
DROP EXTERNAL DATA SOURCE external_data_source_name  
[;]  
```  
  
## <a name="arguments"></a>引数  
 *external_data_source_name*  
 削除する外部データ ソースの名前です。  
  
## <a name="metadata"></a>Metadata  
 外部データ ソースの一覧を表示するのには、sys.external_data_sources のシステム ビューを使用します。  
  
```sql  
SELECT * FROM sys.external_data_sources;  
```  
  
## <a name="permissions"></a>権限  
 ALTER ANY EXTERNAL DATA SOURCE が必要です。  
  
## <a name="locking"></a>ロック  
 外部データ ソースのオブジェクトに対して共有ロックを取得します。  
  
## <a name="general-remarks"></a>全般的な解説  
 外部データ ソースを削除しても、外部データは削除されません。  
  
## <a name="examples"></a>例  
  
### <a name="a-using-basic-syntax"></a>A. 基本的な構文を使用します  
  
```sql  
DROP EXTERNAL DATA SOURCE mydatasource;  
```  
  
## <a name="see-also"></a>参照  
 [CREATE EXTERNAL DATA SOURCE &#40;Transact-SQL&#41;](../../t-sql/statements/create-external-data-source-transact-sql.md)  
  
  

