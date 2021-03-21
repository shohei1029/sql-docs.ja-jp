---
description: SMALLDATETIMEFROMPARTS (Transact-SQL)
title: SMALLDATETIMEFROMPARTS (Transact-SQL)
ms.custom: ''
ms.date: 01/29/2021
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- SMALLDATETIMEFROMPARTS
- SMALLDATETIMEFROMPARTS_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- SMALLDATETIMEFROMPARTS function
author: julieMSFT
ms.author: jrasnick
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 53bb5beffc8c16f7e2a2dee7238d358f4bc5b89d
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104754582"
---
# <a name="smalldatetimefromparts-transact-sql"></a>SMALLDATETIMEFROMPARTS (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  返します、 **smalldatetime** 指定した日付と時刻の値です。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql  
SMALLDATETIMEFROMPARTS ( year, month, day, hour, minute )  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
 *year*  
 年を指定する整数式。  
  
 *month*  
 月を指定する整数式。  
  
 *day*  
 日を指定する整数式。  
  
 *hour*  
 時間を指定する整数式。  
  
 *minute*  
 分を指定する整数式。  
  
## <a name="return-types"></a>戻り値の型  
 **smalldatetime**  
  
## <a name="remarks"></a>解説  
 この関数は、完全に初期化された **smalldatetime** 値のコンストラクターと同様に動作します。 引数が有効でない場合は、エラーがスローされます。 必須の引数が null の場合は、null が返されます。  
 
 この関数は、リモート処理は実行することのできる [!INCLUDE[sssql11-md](../../includes/sssql11-md.md)] サーバー上とします。 [!INCLUDE[sssql11-md](../../includes/sssql11-md.md)] 下のバージョンのサーバーには、リモート処理されません。  
  
## <a name="examples"></a>例  
  
```sql  
SELECT SMALLDATETIMEFROMPARTS ( 2010, 12, 31, 23, 59 ) AS Result  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
Result  
---------------------------  
2010-12-31 23:59:00  
  
(1 row(s) affected)  
```  
  

