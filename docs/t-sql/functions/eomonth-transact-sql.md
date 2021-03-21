---
description: EOMONTH (Transact-SQL)
title: EOMONTH (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/04/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- EOMONTH
- EOMONTH_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- EOMONTH function
ms.assetid: 1d060d8e-3297-4244-afef-57df2f8f92e2
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 4df4eee0955e9b8f728e77ffd6e1fd0d67f6ba91
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104747682"
---
# <a name="eomonth-transact-sql"></a>EOMONTH (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

この関数は、オプションのオフセットを使用して、指定された日付を含んでいる月の最後の日付を返します。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql
EOMONTH ( start_date [, month_to_add ] )  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引数
*start_date*  
月の最終日を返す日付を指定する日付の式。  
  
*month_to_add*  
*start_date* に追加する月数を指定する省略可能な整数式。  
  
*month_to_add* 引数に値が指定されると、`EOMONTH` は指定された月数を *start_date* に追加し、結果として生成された月の最終日を返します。 この整数式を追加したことによって有効な日付範囲をオーバーフローすると、`EOMONTH` はエラーを生成します。  
  
## <a name="return-type"></a>戻り値の型  
 **date**  
  
## <a name="remarks"></a>解説  
`EOMONTH` 関数は、[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降のサーバーに対してリモート処理が可能です。 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] よりも前のバージョンのサーバーに対してリモート処理を実行することはできません。  
  
## <a name="examples"></a>例  
  
### <a name="a-eomonth-with-explicit-datetime-type"></a>A. 明示的な datetime 型を使用する EOMONTH  
  
```sql 
DECLARE @date DATETIME = '12/1/2011';  
SELECT EOMONTH ( @date ) AS Result;  
GO  
```

[!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
Result  
------------  
2011-12-31  
  
(1 row(s) affected)  
```  

### <a name="b-eomonth-with-string-parameter-and-implicit-conversion"></a>B. 文字列パラメーターと暗黙的な変換を使用する EOMONTH  
  
```sql
DECLARE @date VARCHAR(255) = '12/1/2011';  
SELECT EOMONTH ( @date ) AS Result;  
GO  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
Result  
------------  
2011-12-31  
  
(1 row(s) affected)  
```  
  
### <a name="c-eomonth-with-and-without-the-month_to_add-parameter"></a>C. month_to_add パラメーターを使用する EOMONTH と使用しない EOMONTH  
  
これらの結果セットに示されている値は、`12/01/2011` から `12/31/2011` の実行日を含む期間を反映します。

```sql  
DECLARE @date DATETIME = GETDATE();  
SELECT EOMONTH ( @date ) AS 'This Month';  
SELECT EOMONTH ( @date, 1 ) AS 'Next Month';  
SELECT EOMONTH ( @date, -1 ) AS 'Last Month';  
GO  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
This Month  
-----------------------  
2011-12-31  
  
(1 row(s) affected)  
  
Next Month  
-----------------------  
2012-01-31  
  
(1 row(s) affected)  
  
Last Month  
-----------------------  
2011-11-30  
  
(1 row(s) affected)  
```
