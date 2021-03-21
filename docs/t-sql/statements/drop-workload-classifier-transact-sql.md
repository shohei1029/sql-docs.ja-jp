---
description: DROP WORKLOAD CLASSIFIER (Transact-SQL)
title: DROP WORKLOAD CLASSIFIER (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 11/04/2019
ms.prod: sql
ms.prod_service: synapse-analytics
ms.reviewer: jrasnick
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- WORKLOAD CLASSIFIER
- WORKLOAD_CLASSIFIER_TSQL
- DROP_WORKLOAD_CLASSIFIER_TSQL
- DROP WORKLOAD GROUP
dev_langs:
- TSQL
helpviewer_keywords:
- DROP WORKLOAD CLASSIFIER statement
author: ronortloff
ms.author: rortloff
monikerRange: =azure-sqldw-latest
ms.openlocfilehash: 1b49c0635ec485032724425021531d85a1ebf16e
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104752842"
---
# <a name="drop-workload-classifier-transact-sql"></a>DROP WORKLOAD CLASSIFIER (Transact-SQL)

[!INCLUDE [asa](../../includes/applies-to-version/asa.md)]

既存のユーザー定義のワークロード管理分類子を削除します。  要求が実行されている場合、または要求キュー内で中断状態にある場合、要求の分類は維持されるので、分類子をすぐに削除することができます。 重要度が異なる分類子を削除および再作成することは、既に分類済みの要求に影響しません。
  
![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)。  
  
## <a name="syntax"></a>構文

```syntaxsql
DROP WORKLOAD CLASSIFIER classifier_name;
```

[!INCLUDE[synapse-analytics-od-unsupported-syntax](../../includes/synapse-analytics-od-unsupported-syntax.md)]

## <a name="arguments"></a>引数

*classifier_name*  
ワークロード分類子を識別する名前を指定します。
  
## <a name="permissions"></a>アクセス許可

CONTROL DATABASE アクセス許可が必須です。  
  
## <a name="examples"></a>例

次の例では、`wgcELTROLE` という名前のワークロード分類子が削除されます。  

```sql
DROP WORKLOAD CLASSIFIER wgcELTRole;
```

> [!NOTE]
> 送信された要求に一致する分類子が存在しない場合、それは既定のワークロード グループに分類されます。  既定のワークロード グループは、smallrc リソース クラスです。
  
## <a name="see-also"></a>参照

[CREATE WORKLOAD CLASSIFIER &#40;Transact-SQL&#41;](../../t-sql/statements/create-workload-classifier-transact-sql.md)</br>
[[!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] ワークロード分類](/azure/sql-data-warehouse/sql-data-warehouse-workload-classification)
