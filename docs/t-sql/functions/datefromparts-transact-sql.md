---
description: DATEFROMPARTS (Transact-SQL)
title: DATEFROMPARTS (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 07/29/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- DATEFROMPARTS_TSQL
- DATEFROMPARTS
dev_langs:
- TSQL
helpviewer_keywords:
- DATEFROMPARTS function
ms.assetid: 5b885376-87aa-41f1-9e18-04987aead250
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 345cdd42525fb4227989320f16d28a6ca79e1255
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104735551"
---
# <a name="datefromparts-transact-sql"></a>DATEFROMPARTS (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

この関数は、指定の年月日の値にマッピングされる **date** 値を返します。
  
![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)
  
## <a name="syntax"></a>構文  
  
```syntaxsql
DATEFROMPARTS ( year, month, day )  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
*year*  
年を指定する整数式。
  
*month*  
1 - 12 で月を指定する整数式。
  
*day*  
日を指定する整数式。
  
## <a name="return-types"></a>戻り値の型
**date**
  
## <a name="remarks"></a>解説  
`DATEFROMPARTS` は、日付部分が指定の年月日に設定され、時間部分が既定値に設定されている **date** の値を返します。 引数が無効な場合、`DATEFROMPARTS` はエラーを生成します。 `DATEFROMPARTS` は、必須引数に 1 つでも NULL 値が含まれている場合、NULL を返します。
  
この関数は、[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] サーバー以降のリモート処理に対応しています。 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] より前のバージョンについては、リモート処理できません。
  
## <a name="examples"></a>例  
これは `DATEFROMPARTS` 関数の使用例です。
  
```sql
SELECT DATEFROMPARTS ( 2010, 12, 31 ) AS Result;  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
Result  
----------------------------------  
2010-12-31  
  
(1 row(s) affected)  
```  
  
## <a name="see-also"></a>関連項目
[date &#40;Transact-SQL&#41;](../../t-sql/data-types/date-transact-sql.md)
  
  

