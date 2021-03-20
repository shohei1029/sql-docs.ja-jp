---
description: sys.database_scoped_credentials (Transact-sql)
title: sys.database_scoped_credentials (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/27/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics
ms.reviewer: ''
ms.technology: system-objects
ms.topic: conceptual
f1_keywords:
- sys.database_scoped_credentials
- sys.database_scoped_credentials_TSQL
- database_scoped_credentials
- database_scoped_credentials_TSQL
helpviewer_keywords:
- sys.database_scoped_credentials catalog view
ms.assetid: 68e8aa6b-bcdc-42aa-93d8-d498f724c188
author: VanMSFT
ms.author: vanto
monikerRange: =azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 710764e5c530e5bcfa8f4bf7a8d01780948103ad
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104747952"
---
# <a name="sysdatabase_scoped_credentials-transact-sql"></a>sys.database_scoped_credentials (Transact-sql)
[!INCLUDE [sqlserver2016-asdb-asdbmi-asa](../../includes/applies-to-version/sqlserver2016-asdb-asdbmi-asa.md)]

  データベース内のデータベーススコープ資格情報ごとに1行のデータを返します。  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|name|**sysname**|データベーススコープの資格情報の名前。 データベース内で一意です。|  
|credential_id|**int**|データベーススコープ資格情報の ID。 データベース内で一意です。|  
|principal_id|**int**|キーを所有するデータベースプリンシパルの ID。|  
|credential_identity|**nvarchar (4000)**|使用する識別情報の名前。 通常、これは Windows ユーザーです。 一意である必要はありません。|  
|create_date|**datetime**|データベーススコープの資格情報が作成された時刻。|  
|modify_date|**datetime**|データベーススコープの資格情報が最後に変更された時刻。|  
|target_type|**nvarchar (100)**|データベーススコープ資格情報の種類。 `NULL`データベーススコープの資格情報に対してを返します。|  
|target_id|**int**|データベーススコープの資格情報のマップ先のオブジェクトの ID。 データベーススコープの資格情報の場合は0を返します|  
  
## <a name="permissions"></a>アクセス許可  
 データベースに対する `CONTROL` 権限が必要です。  
  
## <a name="see-also"></a>参照  
 [資格情報 &#40;データベース エンジン&#41;](../../relational-databases/security/authentication-access/credentials-database-engine.md)   
 [CREATE DATABASE SCOPED CREDENTIAL &#40;Transact-SQL&#41;](../../t-sql/statements/create-database-scoped-credential-transact-sql.md)   
 [ALTER DATABASE SCOPED CREDENTIAL &#40;Transact-SQL&#41;](../../t-sql/statements/alter-database-scoped-credential-transact-sql.md)   
 [DROP DATABASE SCOPED CREDENTIAL &#40;Transact-SQL&#41;](../../t-sql/statements/drop-database-scoped-credential-transact-sql.md)   
 [CREATE CREDENTIAL &#40;Transact-SQL&#41;](../../t-sql/statements/create-credential-transact-sql.md)   
 [sys.credentials &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-credentials-transact-sql.md)  
  
  
