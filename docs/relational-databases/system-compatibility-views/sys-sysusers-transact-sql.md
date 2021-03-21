---
description: sys.sysユーザー (Transact-sql)
title: sys.sysユーザー (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- sys.sysusers
- sys.sysusers_TSQL
- sysusers_TSQL
- sysusers
dev_langs:
- TSQL
helpviewer_keywords:
- sysusers system table
- sys.sysusers compatibility view
ms.assetid: 5f0e6a8d-c983-44f6-97e9-aab5bff67d18
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 7e9bea84a6fc6237af2d710a4abe580005e4b458
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104751972"
---
# <a name="syssysusers-transact-sql"></a>sys.sysユーザー (Transact-sql)
[!INCLUDE [sql-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdbmi-asa-pdw.md)]

  [!INCLUDE[msCoName](../../includes/msconame-md.md)]データベース内の windows ユーザー、windows グループ、 [!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ユーザー、またはロールごとに1行のデータを格納 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] します。  
  
> [!IMPORTANT]  
>  [!INCLUDE[ssnoteCompView](../../includes/ssnotecompview-md.md)]  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|**uid**|**smallint**|このデータベースで一意のユーザー ID。<br /><br /> 1 = データベース所有者<br /><br /> ユーザーおよびロールの数が32767を超えた場合、オーバーフローまたは NULL を返します。|  
|**status**|**smallint**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|**name**|**sysname**|このデータベース内で一意なユーザー名またはグループ名です。|  
|**sid**|**varbinary(85)**|このエントリのセキュリティ識別子です。|  
|**ロール**|**varbinary(2048)**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|**createdate**|**datetime**|アカウントが追加された日付。|  
|**updatedate**|**datetime**|アカウントが最後に変更された日付。|  
|**altuid**|**smallint**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]<br /><br /> ユーザーおよびロールの数が32767を超えた場合、オーバーフローまたは NULL を返します。|  
|**password**|**varbinary(256)**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|**gid**|**smallint**|このユーザーが所属するグループ ID です。 **Uid** が **gid** と同じ場合、このエントリはグループを定義します。 グループとユーザーの合計数が32767を超える場合は、オーバーフローまたは NULL を返します。|  
|**environ**|**varchar(255)**|予約済み。|  
|**hasdbaccess**|**int**|1 = アカウントはデータベースにアクセスできます。|  
|**islogin**|**int**|1 = アカウントは、Windows グループ、Windows ユーザー、または [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログインアカウントを持つユーザーです。|  
|**isntname**|**int**|1 = アカウントは、Windows グループまたは Windows ユーザーです。|  
|**isntgroup**|**int**|1 = アカウントは Windows グループです。|  
|**isntuser**|**int**|1 = アカウントは Windows ユーザーです。|  
|**issqluser**|**int**|1 = アカウントは [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ユーザーです。|  
|**isaliased**|**int**|1 = アカウントは別のユーザーの別名です。|  
|**issqlrole**|**int**|1 = アカウントは [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ロールです。|  
|**isapprole**|**int**|1 = アカウントはアプリケーションロールです。|  
  
## <a name="see-also"></a>参照  
 [システムビューへのシステムテーブルのマッピング &#40;Transact-sql&#41;](../../relational-databases/system-tables/mapping-system-tables-to-system-views-transact-sql.md)   
 [互換性ビュー &#40;Transact-SQL&#41;](~/relational-databases/system-compatibility-views/system-compatibility-views-transact-sql.md)  
  
  
