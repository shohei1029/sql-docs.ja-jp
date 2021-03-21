---
description: sp_prepare (Transact SQL)
title: sp_prepare (Transact SQL) |Microsoft Docs
ms.custom: ''
ms.date: 02/28/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, synapse-analytics, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- sp_cursor_prepare_TSQL
- sp_cursor_prepare
dev_langs:
- TSQL
helpviewer_keywords:
- sp_prepare
ms.assetid: f328c9eb-8211-4863-bafa-347e1bf7bb3f
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 017009d5dd3d22c8bba1eed67f5a8ebe01d87ed7
ms.sourcegitcommit: 0310fdb22916df013eef86fee44e660dbf39ad21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104754982"
---
# <a name="sp_prepare-transact-sql"></a>sp_prepare (Transact SQL)
[!INCLUDE [sql-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdbmi-asa-pdw.md)]

パラメーター化さ [!INCLUDE[tsql](../../includes/tsql-md.md)] れたステートメントを準備し、実行用のステートメント *ハンドル* を返します。  `sp_prepare` は、ID = 11 を指定した場合に表形式のデータ ストリーム (TDS) パケットで呼び出されます。  
  
 ![記事リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql  
sp_prepare handle OUTPUT, params, stmt, options  
```  
  
## <a name="arguments"></a>引数  
 *扱え*  
 は、SQL Server によって生成される *準備済みハンドル* 識別子です。 *handle* は、 **int** 戻り値を持つ必須パラメーターです。  
  
 *params*  
 パラメーター化されたステートメントを指定します。 変数の *params* 定義は、ステートメントのパラメーターマーカーに置き換えられます。 *params* は、 **ntext**、 **nchar**、または **nvarchar** の入力値を呼び出す必須のパラメーターです。 ステートメントがパラメーター化されていない場合は、NULL 値を入力します。  
  
 *stmt*  
 カーソル結果セットを定義します。 *Stmt* パラメーターは必須であり、 **ntext**、 **nchar**、または **nvarchar** の入力値に対してを呼び出します。  
  
 *options*  
 カーソル結果セット列の説明を返す省略可能なパラメーターです。 *オプション* には、次の int 入力値が必要です。  
  
|値|説明|  
|-----------|-----------------|  
|0x0001|RETURN_METADATA|  
  
## <a name="examples"></a>例  
A. 次の例では、単純なステートメントを準備して実行します。  
  
```sql  
DECLARE @P1 INT;  
EXEC sp_prepare @P1 OUTPUT,   
    N'@P1 NVARCHAR(128), @P2 NVARCHAR(100)',  
    N'SELECT database_id, name FROM sys.databases WHERE name=@P1 AND state_desc = @P2';  
EXEC sp_execute @P1, N'tempdb', N'ONLINE';  
EXEC sp_unprepare @P1;  
```

B. 次の例では、AdventureWorks2016 データベースでステートメントを準備し、後でハンドルを使用してステートメントを実行します。

```sql
-- Prepare query
DECLARE @P1 INT;  
EXEC sp_prepare @P1 OUTPUT,   
    N'@Param INT',  
    N'SELECT *
FROM Sales.SalesOrderDetail AS sod
INNER JOIN Production.Product AS p ON sod.ProductID = p.ProductID
WHERE SalesOrderID = @Param
ORDER BY Style DESC;';  

-- Return handle for calling application
SELECT @P1;
GO
```
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]

```
-----------
1

(1 row affected)
```

次に、ハンドル値1を使用してクエリを2回実行してから、準備されたプランを破棄します。

```sql
EXEC sp_execute 1, 49879;  
GO

EXEC sp_execute 1, 48766;
GO

EXEC sp_unprepare 1; 
GO
```
  
## <a name="see-also"></a>参照  
 [システム ストアド プロシージャ &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/system-stored-procedures-transact-sql.md)  
  

